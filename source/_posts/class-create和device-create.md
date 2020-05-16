---
title: class_create和device_create
categories: linux
date: 2020-05-16 06:59:56
tags:
---

这两个接口的作用。
<!--more-->

## class_create和device_create的结合使用
在字符设备驱动中我们会同时使用这两个接口。
示例：
```c
static int __init miscdrv_entry(void)
{
    int ret;

    major = register_chrdev(0, DRVNAME, &miscdrv);              //#define DRVNAME "miscdev"
    pr_info("[%s] major: %d\n", __func__, major);

    miscdrv_class = class_create(THIS_MODULE, "miscdrv_class");
        ret = PTR_ERR(miscdrv_class);
        if (IS_ERR(miscdrv_class)) {
        unregister_chrdev(major, DRVNAME);
        return ret;
    }   

    device_create(miscdrv_class, NULL, MKDEV(major, 0), NULL, "miscdev%d", 0); 
    device_create(miscdrv_class, NULL, MKDEV(major, 10), NULL, "miscdev%d", 2); 

    return 0;
}

static void __exit miscdrv_exit(void)
{
    device_destroy(miscdrv_class, MKDEV(major, 0));
    device_destroy(miscdrv_class, MKDEV(major, 10));

    class_destroy(miscdrv_class);
    unregister_chrdev(major, DRVNAME);
}
```
通常我们会先创建一个类（`class`），在类下创建多个设备（这里估计任意写的次设备号）。
这样，驱动加载后会自动生成 `/dev/miscdev0` 和 `/dev/miscdev2`。
```shell
[root@imx6ull:/mnt]# ls /dev/misc* -l
crw-------    1 root     root      245,   0 Jan  1 00:43 /dev/miscdev0
crw-------    1 root     root      245,  10 Jan  1 00:43 /dev/miscdev2
```
同时，`sysfs` 虚拟文件系统会创建2个目录：
* `/sys/class/miscdev_class`:
```shell
[root@imx6ull:/mnt]# ls /sys/class/miscdrv_class/ -l
total 0
lrwxrwxrwx    1 root     root             0 Jan  1 00:44 miscdev0 -> ../../devices/virtual/miscdrv_class/miscdev0
lrwxrwxrwx    1 root     root             0 Jan  1 00:44 miscdev2 -> ../../devices/virtual/miscdrv_class/miscdev2
```
* `/sys/devices/virtual/miscdrv_class/` (注意是miscdrv_class而不是字符设备明miscdev):
```shell
[root@imx6ull:/mnt]# ls /sys/devices/virtual/miscdrv_class/ -l
total 0
drwxr-xr-x    3 root     root             0 Jan  1 00:43 miscdev0
drwxr-xr-x    3 root     root             0 Jan  1 00:43 miscdev2
```
<br>

## 只使用class_create
显然两个字符设备节点是通过 `device_create` 创建出来的。
> 如果代码中不创建设备（不调用device_create）会怎么样呢？

> 通过mknod创建的设备能直接使用吗？

### 不调用device_create
和预期一样。但有一点意外：
```shell
[root@imx6ull:/mnt]# ls /sys/class/miscdrv_class/ -l
total 0
[root@imx6ull:/mnt]# ls /sys/devices/virtual/miscdev_class -l               //如果类下没有设备，sysfs甚至不创建类目录
ls: /sys/devices/virtual/miscdev_class: No such file or directory
```

### 使用mknod手动添加设备
```shell
[root@imx6ull:/mnt]# mknod /dev/miscdev0 c 245 0
[root@imx6ull:/mnt]# ls /dev/misc* -l
crw-r--r--    1 root     root      245,   0 Jan  1 00:59 /dev/miscdev0
```
> 可以直接访问吗？当然是可以的：

```shell
[root@imx6ull:/mnt]# cat /dev/miscdev0 
[ 1624.245360] [miscdrv_open] entry
[ 1624.248948] [miscdrv_read] entry
[ 1624.252240] [miscdrv_close] entry
```
内核会根据主设备号找到驱动，调用驱动对应接口。
至于你操作的是哪个具体设备，驱动需要根据次设备号判断。

> 这下有设备了，sysfs会创建 `/sys/devices/virtual/miscdev_class` 目录吗？

仍然不会:
```shell
[root@imx6ull:/mnt]# ls /sys/devices/virtual/miscdrv_class/ -l
ls: /sys/devices/virtual/miscdrv_class/: No such file or directory
```
而且对应的class目录下也没有增加新设备：
```shell
[root@imx6ull:/mnt]# ls /sys/class/miscdrv_class/ -l
total 0
```
**总结**：
也就是说只有通过device_create接口创建的设备，sysfs才会将该设备纳入管理。
<br>


## device未销毁（未device_destroy）的后果
通过 `mknod` 创建的设备节点，我们直接 `rm` 删除就可以了。
> 如果是通过 `device_create` 创建的设备不销毁会有什么后果？

```c
device_create(miscdrv_class, NULL, MKDEV(major, 5), NULL, "miscdev%d", 5);
//device_destroy(miscdrv_class, MKDEV(major, 5));   //故意不销毁
```

```shell
[root@imx6ull:/mnt]# insmod miscdrv.ko 
[ 2761.639832] [miscdrv_entry] major: 245
[root@imx6ull:/mnt]# 
[root@imx6ull:/mnt]# rmmod miscdrv 
[root@imx6ull:/mnt]# 
[root@imx6ull:/mnt]# insmod miscdrv.ko 
[ 2774.192532] [miscdrv_entry] major: 245
[ 2774.206907] ------------[ cut here ]------------
[ 2774.211642] WARNING: CPU: 0 PID: 539 at fs/sysfs/dir.c:31 sysfs_warn_dup+0x64/0x74
[ 2774.221564] sysfs: cannot create duplicate filename '/devices/virtual/miscdrv_class'     //内核WARN告警：无法创建重复目录
[ 2774.232010] ---[ end trace 1f6c23c15d2b3615 ]---
[ 2774.243888] ------------[ cut here ]------------
[ 2774.250508] WARNING: CPU: 0 PID: 539 at lib/kobject.c:240 kobject_add_internal+0x2cc/0x340
[ 2774.259632] ---[ end trace 1f6c23c15d2b3616 ]---
[ 2774.267846] ------------[ cut here ]------------
[ 2774.272500] WARNING: CPU: 0 PID: 539 at fs/sysfs/dir.c:31 sysfs_warn_dup+0x64/0x74
[ 2774.281248] sysfs: cannot create duplicate filename '/dev/char/245:5'
[ 2774.288517] ---[ end trace 1f6c23c15d2b3617 ]---
```
而且这个错误是无法恢复的。
我们尝试删除告警的目录：
```shell
[root@imx6ull:/mnt]# rmmod miscdrv 
[root@imx6ull:/mnt]# 
[root@imx6ull:/mnt]# rm -rf /sys/devices/virtual/miscdrv_class/
rm: can't remove '/sys/devices/virtual/miscdrv_class/miscdev5/subsystem': Operation not permitted
rm: can't remove '/sys/devices/virtual/miscdrv_class/miscdev5/power/runtime_suspended_time': Operation not permitted
rm: can't remove '/sys/devices/virtual/miscdrv_class/miscdev5/power/autosuspend_delay_ms': Operation not permitted
rm: can't remove '/sys/devices/virtual/miscdrv_class/miscdev5/power/runtime_active_time': Operation not permitted
rm: can't remove '/sys/devices/virtual/miscdrv_class/miscdev5/power/control': Operation not permitted
rm: can't remove '/sys/devices/virtual/miscdrv_class/miscdev5/power/runtime_status': Operation not permitted
rm: can't remove '/sys/devices/virtual/miscdrv_class/miscdev5/power': Operation not permitted
rm: can't remove '/sys/devices/virtual/miscdrv_class/miscdev5/dev': Operation not permitted
rm: can't remove '/sys/devices/virtual/miscdrv_class/miscdev5/uevent': Operation not permitted
rm: can't remove '/sys/devices/virtual/miscdrv_class/miscdev5': Operation not permitted
rm: can't remove '/sys/devices/virtual/miscdrv_class': Operation not permitted
```
<br>

