---
title: gcc
date: 2020-04-12 08:14:49
tags:
categories: 编译
---

gcc的一些基础知识。
编译到底做了一些什么，编译时的注意事项等等。
<!--more-->

## 头文件的位置
像 `stdio.h` 在编译器中的 `include` 目录。
自己需要的头文件，编译时用 `-I` 指定，比如 `-I ./`
<br>

## 库的位置
像 `printf` 函数，都在事先编好的库中。库在编译器中的 `lib` 目录。
编译时，
* 用 `-lxxx` 指定需要的库，编译器会去 `lib` 、 `usr/lib` 目录下找叫 `libxxx.so`
或者 `libxxx.so.数字` 的库文件。
* 用 `-L xxx` 指定库的路径。
<br>
<br>

## GCC的编译过程
韦老师团队写的下面这篇文档非常好。
[01.嵌入式Linux应用开发基础知识.docx](https://github.com/100askTeam/01_all_series_quickstart/blob/master/04_%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8_%E6%AD%A3%E5%BC%8F%E5%BC%80%E5%A7%8B/01_%E5%B5%8C%E5%85%A5%E5%BC%8FLinux%E5%BA%94%E7%94%A8%E5%BC%80%E5%8F%91%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/doc_pic/01.%E5%B5%8C%E5%85%A5%E5%BC%8FLinux%E5%BA%94%E7%94%A8%E5%BC%80%E5%8F%91%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.docx)

最初的程序是程序员对着巨大的机器插拔线缆。
之后有了纸带打孔编程，指代上的洞代表01，一条纸带就是要给程序。
再之后，就用一段特定的代码来表示一段特定的01串。这就是汇编语言。
再往后，就出现了c等高级语言。

对老师的文档做下摘要和总结。

一个c/cpp文件的编译分为4步：
* 1）预处理，preprocessing。
* 2）编译，compilation。
* 3）汇编，assembly。
* 4）链接，linking。

<br>

### 预处理
在c/cpp中，以 `#` 开头的都叫做 `预处理命令` ，包括 `#include` `#define` `#if` 等。
预处理要做的就是将它们展开或选择需要编译的代码。

例：
`gcc -E -o main.i main.c -I ./`
预处理不加 `-o` 选项时，会以标准输出的形式打出来。
预处理，需要 `-I` 。一但 `.i` 文件生成了，后续的编译就不需要再指定 `-I` 了。
<br>

### 编译
代码决定后，正式将这些c/cpp代码转换成汇编代码。
用到的工具为 `cc1` 。

例：
`gcc -S main.i` 或者 `gcc -S -o main.s main.i`
指不指定输出都可以。不指定，默认就是*.s。
<br>

### 汇编
汇编就是将汇编代码转换成机器码。
用到的工具为 `as` 。

例：
`gcc -c main.s` 或者 `gcc -c -o main.o main.s`
指不指定输出都可以。不指定，默认就是*.o。
<br>

### 链接
链接就是将多个 `.o文件` `.so文件` 链接起来，生成app。
用到的工具为 `ld` 或者 `collect2` 。

例：
`gcc -o main main.o sub.o`
这里就必须指定了，否则默认生成的是 `a.out` 。
<br>


### gcc -v
可以通过 `-v` 看到上述过程。

```shell
chuck@chuck11:~/bsp$ gcc -o main main.c sub.c -I ./ -v
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-linux-gnu/7/lto-wrapper
OFFLOAD_TARGET_NAMES=nvptx-none
OFFLOAD_TARGET_DEFAULT=1
Target: x86_64-linux-gnu
Configured with: ../src/configure -v --with-pkgversion='Ubuntu 7.5.0-3ubuntu1~18.04' --with-bugurl=file:///usr/share/doc/gcc-7/README.Bugs --enable-languages=c,ada,c++,go,brig,d,fortran,objc,obj-c++ --prefix=/usr --with-gcc-major-version-only --program-suffix=-7 --program-prefix=x86_64-linux-gnu- --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --enable-bootstrap --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-gnu-unique-object --disable-vtable-verify --enable-libmpx --enable-plugin --enable-default-pie --with-system-zlib --with-target-system-zlib --enable-objc-gc=auto --enable-multiarch --disable-werror --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-offload-targets=nvptx-none --without-cuda-driver --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu
Thread model: posix
gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04) 
COLLECT_GCC_OPTIONS='-o' 'main' '-I' './' '-v' '-mtune=generic' '-march=x86-64'
 /usr/lib/gcc/x86_64-linux-gnu/7/cc1 -quiet -v -I ./ -imultiarch x86_64-linux-gnu main.c -quiet -dumpbase main.c -mtune=generic -march=x86-64 -auxbase main -version -fstack-protector-strong -Wformat -Wformat-security -o /tmp/ccZkhzNQ.s
GNU C11 (Ubuntu 7.5.0-3ubuntu1~18.04) version 7.5.0 (x86_64-linux-gnu)
    compiled by GNU C version 7.5.0, GMP version 6.1.2, MPFR version 4.0.1, MPC version 1.1.0, isl version isl-0.19-GMP

GGC heuristics: --param ggc-min-expand=100 --param ggc-min-heapsize=131072
ignoring nonexistent directory "/usr/local/include/x86_64-linux-gnu"
ignoring nonexistent directory "/usr/lib/gcc/x86_64-linux-gnu/7/../../../../x86_64-linux-gnu/include"
#include "..." search starts here:
#include <...> search starts here:
 ./
 /usr/lib/gcc/x86_64-linux-gnu/7/include
 /usr/local/include
 /usr/lib/gcc/x86_64-linux-gnu/7/include-fixed
 /usr/include/x86_64-linux-gnu
 /usr/include
End of search list.
GNU C11 (Ubuntu 7.5.0-3ubuntu1~18.04) version 7.5.0 (x86_64-linux-gnu)
    compiled by GNU C version 7.5.0, GMP version 6.1.2, MPFR version 4.0.1, MPC version 1.1.0, isl version isl-0.19-GMP

GGC heuristics: --param ggc-min-expand=100 --param ggc-min-heapsize=131072
Compiler executable checksum: b62ed4a2880cd4159476ea8293b72fa8
COLLECT_GCC_OPTIONS='-o' 'main' '-I' './' '-v' '-mtune=generic' '-march=x86-64'
 as -v -I ./ --64 -o /tmp/cc3Tp9Or.o /tmp/ccZkhzNQ.s
GNU assembler version 2.30 (x86_64-linux-gnu) using BFD version (GNU Binutils for Ubuntu) 2.30
COLLECT_GCC_OPTIONS='-o' 'main' '-I' './' '-v' '-mtune=generic' '-march=x86-64'
 /usr/lib/gcc/x86_64-linux-gnu/7/cc1 -quiet -v -I ./ -imultiarch x86_64-linux-gnu sub.c -quiet -dumpbase sub.c -mtune=generic -march=x86-64 -auxbase sub -version -fstack-protector-strong -Wformat -Wformat-security -o /tmp/ccZkhzNQ.s
GNU C11 (Ubuntu 7.5.0-3ubuntu1~18.04) version 7.5.0 (x86_64-linux-gnu)
    compiled by GNU C version 7.5.0, GMP version 6.1.2, MPFR version 4.0.1, MPC version 1.1.0, isl version isl-0.19-GMP

GGC heuristics: --param ggc-min-expand=100 --param ggc-min-heapsize=131072
ignoring nonexistent directory "/usr/local/include/x86_64-linux-gnu"
ignoring nonexistent directory "/usr/lib/gcc/x86_64-linux-gnu/7/../../../../x86_64-linux-gnu/include"
#include "..." search starts here:
#include <...> search starts here:
 ./
 /usr/lib/gcc/x86_64-linux-gnu/7/include
 /usr/local/include
 /usr/lib/gcc/x86_64-linux-gnu/7/include-fixed
 /usr/include/x86_64-linux-gnu
 /usr/include
End of search list.
GNU C11 (Ubuntu 7.5.0-3ubuntu1~18.04) version 7.5.0 (x86_64-linux-gnu)
    compiled by GNU C version 7.5.0, GMP version 6.1.2, MPFR version 4.0.1, MPC version 1.1.0, isl version isl-0.19-GMP

GGC heuristics: --param ggc-min-expand=100 --param ggc-min-heapsize=131072
Compiler executable checksum: b62ed4a2880cd4159476ea8293b72fa8
sub.c: In function ‘sub_fun’:
sub.c:3:8: warning: implicit declaration of function ‘printf’ [-Wimplicit-function-declaration]
        printf("Sub fun!\n");
        ^~~~~~
sub.c:3:8: warning: incompatible implicit declaration of built-in function ‘printf’
sub.c:3:8: note: include ‘<stdio.h>’ or provide a declaration of ‘printf’
COLLECT_GCC_OPTIONS='-o' 'main' '-I' './' '-v' '-mtune=generic' '-march=x86-64'
 as -v -I ./ --64 -o /tmp/ccA3F0W2.o /tmp/ccZkhzNQ.s
GNU assembler version 2.30 (x86_64-linux-gnu) using BFD version (GNU Binutils for Ubuntu) 2.30
COMPILER_PATH=/usr/lib/gcc/x86_64-linux-gnu/7/:/usr/lib/gcc/x86_64-linux-gnu/7/:/usr/lib/gcc/x86_64-linux-gnu/:/usr/lib/gcc/x86_64-linux-gnu/7/:/usr/lib/gcc/x86_64-linux-gnu/
LIBRARY_PATH=/usr/lib/gcc/x86_64-linux-gnu/7/:/usr/lib/gcc/x86_64-linux-gnu/7/../../../x86_64-linux-gnu/:/usr/lib/gcc/x86_64-linux-gnu/7/../../../../lib/:/lib/x86_64-linux-gnu/:/lib/../lib/:/usr/lib/x86_64-linux-gnu/:/usr/lib/../lib/:/usr/lib/gcc/x86_64-linux-gnu/7/../../../:/lib/:/usr/lib/
COLLECT_GCC_OPTIONS='-o' 'main' '-I' './' '-v' '-mtune=generic' '-march=x86-64'
 /usr/lib/gcc/x86_64-linux-gnu/7/collect2 -plugin /usr/lib/gcc/x86_64-linux-gnu/7/liblto_plugin.so -plugin-opt=/usr/lib/gcc/x86_64-linux-gnu/7/lto-wrapper -plugin-opt=-fresolution=/tmp/ccskUa6D.res -plugin-opt=-pass-through=-lgcc -plugin-opt=-pass-through=-lgcc_s -plugin-opt=-pass-through=-lc -plugin-opt=-pass-through=-lgcc -plugin-opt=-pass-through=-lgcc_s --build-id --eh-frame-hdr -m elf_x86_64 --hash-style=gnu --as-needed -dynamic-linker /lib64/ld-linux-x86-64.so.2 -pie -z now -z relro -o main /usr/lib/gcc/x86_64-linux-gnu/7/../../../x86_64-linux-gnu/Scrt1.o /usr/lib/gcc/x86_64-linux-gnu/7/../../../x86_64-linux-gnu/crti.o /usr/lib/gcc/x86_64-linux-gnu/7/crtbeginS.o -L/usr/lib/gcc/x86_64-linux-gnu/7 -L/usr/lib/gcc/x86_64-linux-gnu/7/../../../x86_64-linux-gnu -L/usr/lib/gcc/x86_64-linux-gnu/7/../../../../lib -L/lib/x86_64-linux-gnu -L/lib/../lib -L/usr/lib/x86_64-linux-gnu -L/usr/lib/../lib -L/usr/lib/gcc/x86_64-linux-gnu/7/../../.. /tmp/cc3Tp9Or.o /tmp/ccA3F0W2.o -lgcc --push-state --as-needed -lgcc_s --pop-state -lc -lgcc --push-state --as-needed -lgcc_s --pop-state /usr/lib/gcc/x86_64-linux-gnu/7/crtendS.o /usr/lib/gcc/x86_64-linux-gnu/7/../../../x86_64-linux-gnu/crtn.o
COLLECT_GCC_OPTIONS='-o' 'main' '-I' './' '-v' '-mtune=generic' '-march=x86-64'
```

可以清晰的看出编译的过程。
其中：
`/usr/lib/gcc/x86_64-linux-gnu/7/cc1` 生成的 `.s` ， `as` 生成的 `.o` 都位于 `/tmp/` 目录下。
最终链接使用的是 `/usr/lib/gcc/x86_64-linux-gnu/7/collect2` 。
<br>

### gcc -Wall
打开告警。
<br>

### gcc -g
以操作系统的本地格式（stabs，COFF，XCOFF，或DWARF）产生调试信息，gdb能使用这些信息。
我们反汇编，也必须加上 `-g` ，否则没有函数信息，看不懂汇编的就会一脸懵逼。

加上 `-g` 后，文件明显的就变大了。
例：
```shell
chuck@chuck11:~/bsp$ gcc -c main.c -I ./
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ ll
total 28
drwxrwxr-x  2 chuck chuck 4096 Apr 18 08:42 ./
drwxr-xr-x 17 chuck chuck 4096 Apr 18 08:16 ../
-rw-rw-r--  1 chuck chuck  153 Apr 12 12:52 main.c
-rw-rw-r--  1 chuck chuck 1608 Apr 18 08:42 main.o
-rw-rw-r--  1 chuck chuck  139 Apr 12 12:51 Makefile
-rw-rw-r--  1 chuck chuck   52 Apr 12 12:51 sub.c
-rw-rw-r--  1 chuck chuck   19 Apr 12 12:51 sub.h
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ gcc -c main.c -I ./ -g -o a.o
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ ll
total 36
drwxrwxr-x  2 chuck chuck 4096 Apr 18 08:43 ./
drwxr-xr-x 17 chuck chuck 4096 Apr 18 08:16 ../
-rw-rw-r--  1 chuck chuck 5992 Apr 18 08:43 a.o
-rw-rw-r--  1 chuck chuck  153 Apr 12 12:52 main.c
-rw-rw-r--  1 chuck chuck 1608 Apr 18 08:42 main.o
-rw-rw-r--  1 chuck chuck  139 Apr 12 12:51 Makefile
-rw-rw-r--  1 chuck chuck   52 Apr 12 12:51 sub.c
-rw-rw-r--  1 chuck chuck   19 Apr 12 12:51 sub.h
```
<br>


### gcc -O
优化选项。没有搞懂的欲望。

|选项|说明|
|-|-|
|-O|不优化|
|-O2|**TODO**|
|-O3|**TODO**|

例：
```shell
chuck@chuck11:~/bsp$ gcc -c main.c -I ./ -O2 -o main_o2.o
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ gcc -c main.c -I ./
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ gcc -c main.c -I ./ -O3 -o main_o3.o
chuck@chuck11:~/bsp$ gcc -c main.c -I ./ -O1 -o main_o1.o
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ ll
total 40
drwxrwxr-x  2 chuck chuck 4096 Apr 18 08:50 ./
drwxr-xr-x 17 chuck chuck 4096 Apr 18 08:16 ../
-rw-rw-r--  1 chuck chuck  153 Apr 12 12:52 main.c
-rw-rw-r--  1 chuck chuck 1608 Apr 18 08:50 main.o
-rw-rw-r--  1 chuck chuck 1624 Apr 18 08:50 main_o1.o
-rw-rw-r--  1 chuck chuck 1728 Apr 18 08:50 main_o2.o
-rw-rw-r--  1 chuck chuck 1728 Apr 18 08:50 main_o3.o
-rw-rw-r--  1 chuck chuck  139 Apr 12 12:51 Makefile
-rw-rw-r--  1 chuck chuck   52 Apr 12 12:51 sub.c
-rw-rw-r--  1 chuck chuck   19 Apr 12 12:51 sub.h
```
随着优化等级变高，代码变多了。
<br>

### 链接选项
选项比较多，可以参考：
[01.嵌入式Linux应用开发基础知识.docx](https://github.com/100askTeam/01_all_series_quickstart/blob/master/04_%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8_%E6%AD%A3%E5%BC%8F%E5%BC%80%E5%A7%8B/01_%E5%B5%8C%E5%85%A5%E5%BC%8FLinux%E5%BA%94%E7%94%A8%E5%BC%80%E5%8F%91%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/doc_pic/01.%E5%B5%8C%E5%85%A5%E5%BC%8FLinux%E5%BA%94%E7%94%A8%E5%BC%80%E5%8F%91%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.docx)

#### 动态库和静态库
我们先来讲讲库。
库分：
* 静态库， `.a` 文件
* 动态库， `.so` 文件

**动态库**
通过 `-shared` 选项生成 。
```shell
chuck@chuck11:~/bsp$ gcc -shared sub.c -o libsub.so
chuck@chuck11:~/bsp$ gcc -o main main.c -I ./ -lsub -L ./
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ ./main 
./main: error while loading shared libraries: libsub.so: cannot open shared object file: No such file or directory
chuck@chuck11:~/bsp$ ll main
-rwxrwxr-x 1 chuck chuck 8328 Apr 18 17:50 main*
```
注意我故意列出了main的大小 `8328` 字节。

**静态库**
> 那么 `.a` 和 `.so` 是否仅仅是名字上的差别呢？

我们来做个实验。

```shell
chuck@chuck11:~/bsp$ gcc -shared sub.c -o libsub.a
chuck@chuck11:~/bsp$ ll
total 40
drwxrwxr-x  2 chuck chuck 4096 Apr 18 17:56 ./
drwxr-xr-x 17 chuck chuck 4096 Apr 18 17:32 ../
-rwxrwxr-x  1 chuck chuck 7896 Apr 18 17:56 libsub.a*
-rwxrwxr-x  1 chuck chuck 7896 Apr 18 17:50 libsub.so*
-rw-rw-r--  1 chuck chuck  153 Apr 12 12:52 main.c
-rw-rw-r--  1 chuck chuck  139 Apr 12 12:51 Makefile
-rw-rw-r--  1 chuck chuck   52 Apr 12 12:51 sub.c
-rw-rw-r--  1 chuck chuck   19 Apr 12 12:51 sub.h
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ gcc -o main main.c -I ./ -lsub -L ./
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ ./main 
./main: error while loading shared libraries: libsub.so: cannot open shared object file: No such file or directory
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ rm libsub.so 
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ gcc -o main main.c -I ./ -lsub -L ./
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ ./main 
Main fun!
Sub fun!
chuck@chuck11:~/bsp$ ll main
-rwxrwxr-x 1 chuck chuck 8328 Apr 18 17:56 main*
chuck@chuck11:~/bsp$ diff libsub.a libsub.so
chuck@chuck11:~/bsp$
```
会发现：
* 1）使用 `-shared` 生成 `.a` 和 `.so` 内容是一样的
* 2）链接，优先使用 `.so`
* 3）链接后，使用 `.a` 链接的main是可以直接运行的！！！
* 4）`main` 的大小并没有差异，说明仍然是在运行时动态链接的

我们继续实验：
```shell
chuck@chuck11:~/bsp$ cd ../
chuck@chuck11:~$ 
chuck@chuck11:~$ cp bsp/main .
chuck@chuck11:~$ 
chuck@chuck11:~$ ./main 
./main: error while loading shared libraries: .//libsub.a: cannot open shared object file: No such file or directory
chuck@chuck11:~$ 
chuck@chuck11:~$ cp bsp/libsub.a .
chuck@chuck11:~$ ./main 
Main fun!
Sub fun!
chuck@chuck11:~$
```
也就是说，链接 `.a` 的app运行时要求库在当前目录，而链接 `.so` 的app运行时要求库在 `lib` 目录。

我们做下印证：
```shell
chuck@chuck11:~$ su
Password: 
root@chuck11:/home/chuck# 
root@chuck11:/home/chuck# cp bsp/libsub.a /usr/lib/
root@chuck11:/home/chuck# ./main 
./main: error while loading shared libraries: .//libsub.a: cannot open shared object file: No such file or directory
root@chuck11:/home/chuck# cp bsp/libsub.so /usr/lib/
root@chuck11:/home/chuck# ./main 
./main: error while loading shared libraries: .//libsub.a: cannot open shared object file: No such file or directory
root@chuck11:/home/chuck#
root@chuck11:/home/chuck# cd bsp/
root@chuck11:/home/chuck/bsp# gcc -o main main.c -I ./ -L ./ -lsub
root@chuck11:/home/chuck/bsp# 
root@chuck11:/home/chuck/bsp# ./main 
Main fun!
Sub fun!
```
和预期一致。

> 那么静态库正确的生成方法是？

参考：
[gcc生成静态链接库](http://c.biancheng.net/view/7168.html)

我们使用 `ar` 命令。
`ar rcs + 静态库文件的名字 + 目标文件列表`
事实上，ar是linux的一个备份压缩命令，它将多个文件打包成一个备份文件（归档文件），
反过来也可以提取。

参数说明：
* `r` 用来替换库中已有的目标文件，或者加入新的目标文件
* `c` 表示创建一个库。不管库否存在，都将创建
* `s` 用来创建目标文件索引，这在创建较大的库时能提高速度

我们不需要记住每个参数的含义，记住使用 `ar rcs` 即可。
例：
```shell
chuck@chuck11:~/bsp$ gcc -c -o sub.o sub.c
chuck@chuck11:~/bsp$ ar rcs libsub.a sub.o 
chuck@chuck11:~/bsp$ gcc -o main main.c -I ./ -L ./ -lsub
chuck@chuck11:~/bsp$ ll
total 44
drwxrwxr-x  2 chuck chuck 4096 Apr 18 18:38 ./
drwxr-xr-x 17 chuck chuck 4096 Apr 18 18:34 ../
-rw-rw-r--  1 chuck chuck 1680 Apr 18 18:38 libsub.a
-rwxrwxr-x  1 chuck chuck 8360 Apr 18 18:38 main*
-rw-rw-r--  1 chuck chuck  153 Apr 12 12:52 main.c
-rw-rw-r--  1 chuck chuck  139 Apr 12 12:51 Makefile
-rw-rw-r--  1 chuck chuck   52 Apr 12 12:51 sub.c
-rw-rw-r--  1 chuck chuck   19 Apr 12 12:51 sub.h
-rw-rw-r--  1 chuck chuck 1536 Apr 18 18:38 sub.o
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ ./main 
Main fun!
Sub fun!
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ cd ../
chuck@chuck11:~$ cp bsp/main .
chuck@chuck11:~$ ./main 
Main fun!
Sub fun!
chuck@chuck11:~$
```
不论哪个目录，`main` 都可以运行。而且大小相较动态库，变大了 `8360` 。
这才是静态库。

> *注意：`ar` 打包的是归档文件，即 `.o` 文件，不能直接 `ar .c`*

错误示范：
```shell
chuck@chuck11:~/bsp$ ar rcs -o libsub.a sub.c 
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ ll
total 28
drwxrwxr-x  2 chuck chuck 4096 Apr 18 18:43 ./
drwxr-xr-x 17 chuck chuck 4096 Apr 18 18:42 ../
-rw-rw-r--  1 chuck chuck  120 Apr 18 18:43 libsub.a
-rw-rw-r--  1 chuck chuck  153 Apr 12 12:52 main.c
-rw-rw-r--  1 chuck chuck  139 Apr 12 12:51 Makefile
-rw-rw-r--  1 chuck chuck   52 Apr 12 12:51 sub.c
-rw-rw-r--  1 chuck chuck   19 Apr 12 12:51 sub.h
chuck@chuck11:~/bsp$ 
chuck@chuck11:~/bsp$ gcc -o main main.c -I ./ -L ./ -lsub
.//libsub.a: error adding symbols: Archive has no index; run ranlib to add one
collect2: error: ld returned 1 exit status
```
可以发现，libsub.a很小。显然sub.c并没有被编译，记住ar只是一个打包命令，它不编译。
<br>



#### -static
<br>


















































