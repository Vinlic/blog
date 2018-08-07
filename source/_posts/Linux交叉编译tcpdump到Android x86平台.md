---
title: Linux交叉编译tcpdump到Android x86平台
abbrlink: 9cbe3a39
date: 2018-08-06 00:27:35
tags:
 - ARM
 - x86
 - Android
---

### 问题来源
之前在Genymotion上运行下载的 ``tcpdump`` （运行在Android arm平台的抓包程序） 的时候发现并不支持：``not executable: magic 7F45``。

很明显，运行在原来Android arm平台上``tcpdump`` 无法在Android x86平台上运行。

Android x86的Linux内核是通用的32位Linux内核，这个时候我们可以借助Linux编译 ``tcpdump`` 移植到Android x86平台。

### 准备工作
博主是拿一台闲置的CentOS7系统服务器拿来作为编译机。

首先先使用 ``yum groupinstall`` 安装 ``Development Tools`` 开发工具包和 ``glibc-static`` 静态库。

```Shell
$ yum groupinstall -y "Development Tools"
$ yum install -y glib-static
```

下载 ``tcpdump`` 和 ``libpcap`` 源码包并解压。

```Shell
$ wget http://www.tcpdump.org/release/tcpdump-4.9.2.tar.gz
$ wget http://www.tcpdump.org/release/libpcap-1.9.0.tar.gz
$ tar -zxvf tcpdump-4.9.2.tar.gz
$ tar -zxvf libpcap-1.9.0.tar.gz
```

### 开始编译
进入 ``libpcap-1.9.0`` 文件夹，编译 ``libpcap`` 。

```Shell
$ cd libpcap-1.9.0
$ export CC=gcc
$ ./configure --host=i686-pc-linux-gnu --with-pcap=linux
$ make
$ cd ..
```

进入 ``tcpdump-4.9.2`` 文件夹，编译 ``tcpdump`` 。

```Shell
$ cd tcpdump-4.9.2
$ export ac_cv_linux_vers=2
$ export CFLAGS=-static
$ export CPPFLAGS=-static
$ export LDFLAGS=-static
$ ./configure --host=i686-pc-linux-gnu --disable-ipv6
$ make
```

编译完成后，会在 ``tcpdump-4.9.2`` 文件夹里生成一个 ``tcpdump`` 文件，可以通过 ``file`` 命令查看。

```
$ file tcpdump
```

{% asset_img 1.jpg 验证 %}

### 运行程序
使用 ``adb push`` 将tcpdump文件复制到在Genymotion运行的Android模拟器中。

```Shell
$ adb push tcpdump /data/local
```

在 ``adb shell`` 中运行 ``tcpdump`` 试试。

```Shell
$ adb shell
# chmod 777 /data/local/tcpdump
# /data/local/tcpdump -h
```

运行成功，任务完成~

{% asset_img 2.jpg 运行tcpdump %}