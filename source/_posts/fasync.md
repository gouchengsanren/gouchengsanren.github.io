---
title: fasync
categories: linux
date: 2020-05-30 14:09:23
tags:
---


kernel向app异步通知，除了我们知道的netlink以外，还有一种日常工作中没有见过的方式：fasync。
它可以用来向app发送一个signal。app收到signal后进行一些处理。
<!--more-->

## fasync
具体使用示例在git上，可以参考：
[fasync示例（一个按键驱动）](https://github.com/gouchengsanren/files/tree/master/button_drv_and_app)
*注：没有写中断处理，简单写了个框架。可能后面git会更新。不影响本文*

使用方式比较简单：
* 1）声明一个 `fasync_struct` 结构体指针。
```
static struct fasync_struct *b_async = NULL;
```
* 2）`file_operations` 中实现 `fasync` 。
```
static int button_fasync(int fd, struct file *file, int on)
{
    pr_info("FASYNC\n");
    return fasync_helper(fd, file, on, &b_async);
}
```
* 3）中断中调用 `kill_fasync` 。
```
kill_fasync(&b_async, SIGIO, POLL_IN);      //SIGIO:29
```
* 4）app中设置信号处理函数、打开设备节点、绑定fd和pid、flag增加FASYNC。
由于app程序短，就直接全部贴上来了。
```
#include <stdio.h>
#include <unistd.h>
#include <signal.h>
#include <fcntl.h>

int fd;

void my_sig_func(int signo)
{
    printf("get a signal : %d\n", signo);
}

int main(int argc, char **argv)
{
    int Oflags;

    signal(SIGIO, my_sig_func);
    fd = open("/dev/bsp_button", O_RDWR);
    fcntl(fd, F_SETOWN, getpid());         //告诉内核，操作fd的是哪个pid
    Oflags = fcntl(fd, F_GETFL);
    fcntl(fd, F_SETFL, Oflags | FASYNC);

    while (1) 
    {
        sleep(2);
    }

    return 0;
}
```

这样就可以了。

> 那么它和 `netlink` 的区别是什么呢？

也是非常明显。
`fasync` 只能传递信号（SIGNAL），且app在收到信号后，还得再向内核查询。
SIGNAL以下：
```
#define SIGHUP       1
#define SIGINT       2
#define SIGQUIT      3
#define SIGILL       4
#define SIGTRAP      5
#define SIGABRT      6
#define SIGIOT       6
#define SIGBUS       7
#define SIGFPE       8
#define SIGKILL      9
#define SIGUSR1     10
#define SIGSEGV     11
#define SIGUSR2     12
#define SIGPIPE     13
#define SIGALRM     14
#define SIGTERM     15
#define SIGSTKFLT   16
#define SIGCHLD     17
#define SIGCONT     18
#define SIGSTOP     19
#define SIGTSTP     20
#define SIGTTIN     21
#define SIGTTOU     22
#define SIGURG      23
#define SIGXCPU     24
#define SIGXFSZ     25
#define SIGVTALRM   26
#define SIGPROF     27
#define SIGWINCH    28
#define SIGIO       29
#define SIGPOLL     SIGIO
```
而 `netlink` 可以传递任何信息。
<br>


