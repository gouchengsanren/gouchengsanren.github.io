---
title: platform总线
categories: linux
date: 2020-05-17 07:16:22
tags:
---

platform虚拟总线的使用注意事项。
<!--more-->


## platform_device的资源
描述device资源的方式有两种：
* 1）`resource` :
```c
//arch/arm/mach-cns3xxx/devices.c
static struct resource cns3xxx_sdhci_resources[] = {
    [0] = {
        .start = CNS3XXX_SDIO_BASE,
        .end   = CNS3XXX_SDIO_BASE + SZ_4K - 1,
        .flags = IORESOURCE_MEM,
    },
    [1] = { 
        .start = IRQ_CNS3XXX_SDIO,
        .end   = IRQ_CNS3XXX_SDIO,
        .flags = IORESOURCE_IRQ,
    }, 
};  

static struct platform_device cns3xxx_sdhci_pdev = {
    .name       = "sdhci-cns3xxx",
    .id     = 0,
    .num_resources  = ARRAY_SIZE(cns3xxx_sdhci_resources),
    .resource   = cns3xxx_sdhci_resources,
};
```
* 2）dev的 `platform_data` ：
```c
//arch/arm/mach-davinci/board-da850-evm.c
static struct gpio_led da850_evm_bb_leds[] = {
    [0 ... DA850_N_BB_USER_LED - 1] = {
        .active_low = 1,
        .gpio = -1, /* assigned at runtime */
        .name = NULL, /* assigned at runtime */
    }, 
};      

static struct gpio_led_platform_data da850_evm_bb_leds_pdata = {
    .leds = da850_evm_bb_leds,
    .num_leds = ARRAY_SIZE(da850_evm_bb_leds),
};
    
static struct platform_device da850_evm_bb_leds_device = {
    .name       = "leds-gpio",
    .id     = -1, 
    .dev = {
        .platform_data = &da850_evm_bb_leds_pdata
    }
};
```
第二种更加自由，第一种偏向于描述硬件资源。
<br>

## 关于platform_device的id
> id可以不初始化吗？

如果只有一个device，可以不初始化，默认是0.
如果有多个device，必须初始化，且id不能重复。
否则会报以下错误：
```shell
[root@imx6ull:/mnt]# insmod led_imx6ull.ko 
[ 5711.683557] [bspled_probe] pdev->id:0
[root@imx6ull:/mnt]# 
[root@imx6ull:/mnt]# 
[root@imx6ull:/mnt]# insmod led_imx6ull2.ko 
[ 5715.843565] ------------[ cut here ]------------
[ 5715.861461] WARNING: CPU: 0 PID: 379 at fs/sysfs/dir.c:31 sysfs_warn_dup+0x64/0x74
[ 5715.870601] sysfs: cannot create duplicate filename '/devices/platform/bspled.0'
[ 5715.880155] ---[ end trace fff8368841ec45e8 ]---
[ 5715.889641] ------------[ cut here ]------------
[ 5715.894917] WARNING: CPU: 0 PID: 379 at lib/kobject.c:240 kobject_add_internal+0x2cc/0x340
[ 5715.903667] ---[ end trace fff8368841ec45e9 ]---
[ 5715.912608] [imx6ull_bspled_init] platform_device_register fail
insmod: ERROR: could not insert module led_imx6ull2.ko: File exists
```
<br>


