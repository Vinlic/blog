---
title: 阿里云Linux服务器数据盘扩容跳坑小结
tags:
  - Linux
  - 阿里云
  - CentOS
  - 存储
abbrlink: 2fcd543b
date: 2019-04-22 12:54:11
---

### 简介

一直以来都是在做开发，对服务器运维碰得比较少，所以这也是第一次尝试给硬盘扩容操作，因为不了解LVM（逻辑卷管理）直接根据阿里云官方扩容文档进行扩容直接掉进了坑，后面发现数据盘是LVM后进行LVM扩容才解决了问题，在此小结一波。

### 扩容的原因

公司在生产环境的服务突然宕掉，看了一下日志是磁盘空间满了写不进数据，服务器是阿里云的，系统是CentOS 7.4。

执行 ``df -h`` 命令发现公司的服务器50G大小的数据盘被占满，接着开始排查占用大量空间的罪魁祸首。结果发现原来是由于公司的老业务系统并没有定期清理日志的功能导致海量日志积压严重占满了数据盘。

虽然定期清理日志是能够解决问题的，但是下个月生产环境会再上线一个新的服务，存储空间确实有些小，于是着手扩容数据盘。

### 开始扩容

既然是阿里云的服务器那就得先从阿里云的控制台入手进行。

做任何大事前备份永远在第一位，首先进控制台对系统盘和数据盘各做一波磁盘快照。

然后翻出了官方的<a href="https://help.aliyun.com/document_detail/25452.html?spm=5176.2020520101.0.d25452.73564df5NnEs9L">《Linux系统的数据盘扩容文档》</a>。

根据官方文档指引，在云服务器ECS中找到公司服务器的实例 > 本实例磁盘，然后点击要扩容磁盘尾部的“更多”，点击“磁盘扩容”，调整需要扩容的容量付款完成后需要返回控制台重启服务器使扩容生效。

{% asset_img 0.jpg [控制台扩容] %}

下一步就要登录服务器进行文件系统的扩容，因为控制台只负责完成磁盘扩容，而磁盘分区的文件系统并未扩容。

### 文件系统扩容（反面例子）

登录服务器后，通过 `fdisk -l` 命令看到 `/dev/vdb` 这个数据盘确实已经扩容了，接下来进行文件系统扩容。

根据官方文档首先要卸载数据盘主分区，但不论怎么卸载都是失败的（当时不知道 `/dev/mapper/vgxxx` 分区是逻辑卷，以为和 `/dev/vdb1` 一样）。

```Shell
[root@host ~]# umount /dev/mapper/vgxxx
umount: /dev/mapper/vgxxx: device is busy.
(In some cases useful info about processes that use
the device is found by lsof(8) or fuser(1))
```

在进行了数次尝试后（包括用-f参数强制卸载和lsof、fuser查看分区是否被进程占用），依然无法卸载分区。

只好采用比较极端的手段卸载分区（有重要业务的服务器不要随意尝试）：修改 `/etc/fstab` 文件将数据盘的挂载项注释掉并重启服务器。

这下执行 `df -h` 看到分区已经没有挂载了，于是进行下一步。

接下来根据文档需要使用 `fdisk` 命令进行分区重建，依次输入:
```Shell
`d` 删除分区
`n` 创建分区
`p` 指定主分区
`1` 选择分区号1
`回车` 默认与原来分区保持一致的第一个可用扇区编号
`回车` 默认，最后一个分区编号
`wq` 开始分区并保存
```
```Shell
[root@host ~]# fdisk /dev/mapper/vgxxx
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Command (m for help): d
Selected partition 1
Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1,
First sector (2048-167755775, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-167755775, default 167755775):
Using default value 167755775
Command (m for help): wq
The partition table has been altered!
 
Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 22: Invalid argument.
The kernel still uses the old table.
The new table will be used at the next reboot.
```

这一波操作下来发现得到了一个WARNING，并没有成功分区，这就很尴尬了。接下来就是不断的各种尝试，用 `partprobe` 重读分区表等等操作，到后面差一点点搞出问题要使用快照回滚硬盘，十分惊险，分分钟上演删库跑路操作。

然后仔细去了解了 `/dev/mapper` 和常见的 `/dev/vdb1` 这类文件系统的区别，才知道数据盘是使用LVM的，难怪这一大波操作一点用都没有。

### LVM扩容（正确方法）

在知道数据盘是使用LVM后，就开始LVM扩容操作，关于LVM的介绍请点<a href="http://www.cnblogs.com/gaojun/archive/2012/08/22/2650229.html?spm=a2c4e.11153940.blogcont439331.7.3f80105fXY1Vpg#_Toc333352427">这里</a>了解。

#### 安装LVM

首先判断系统是否安装了LVM工具
```Shell
[root@host ~]# rpm -qa | grep lvm
lvm2-libs-2.02.177-4.el7.x86_64
lvm2-2.02.177-4.el7.x86_64
```

如果没有安装可以使用YUM安装LVM2
```Shell
[root@host ~]# yum install lvm2
```

#### 扩容分区

首先在 `/dev/vdb` 创建一个新分区，因为已有vdb1，这里新建的分区号为2。

执行 `fdisk /dev/vdb` 并依次输入：
```Shell
`n` 创建分区
`p` 指定主分区，如果主分区已经占满可以替换成 `e` 扩展分区
`2` 选择分区号2
`回车` 默认与原来分区保持一致的第一个可用扇区编号
`回车` 默认，最后一个分区编号
`t` 改变分区类型
`2` 指定要改变刚刚新建的2号分区
`8e` 分区类型8e表示LVM分区
`wq` 开始分区并保存
```

```Shell
[root@host ~]# fdisk /dev/vdb
Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (2-4, default 2): 2,
First sector (104857600-167772159, default 104857600):
Using default value 104857600
Last sector, +sectors or +size{K,M,G} (104857600-167772159, default 167772159):
Using default value 167772159
Partition 2 of type Linux and of size 30 GiB is set

Command (m for help): t
Partition number (1,2, default 2):
Hex code (type L to list all codes): 8e
Changed type of partition 'Linux' to 'Linux LVM'

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
```

这里也报了一个WANING，这里使用 `partprobe` 重读分区表即可（partprobe可以修改kernel中分区表，使kernel重新读取分区表。 因此，使用该命令就可以创建分区并且在不重新启动机器的情况下系统能够识别这些分区）。

```Shell
[root@host ~]# partprobe
```

格式化新建的分区 `/dev/vdb2`

```Shell
[root@host ~]# mkfs -t ext3 /dev/vdb2
```

查看数据盘的分区表及使用情况

```Shell
[root@host ~]# partx /dev/vdb
NR     START       END   SECTORS SIZE NAME UUID
 1      2048 104857599 104855552  50G
 2 104857600 167772159  62914560  30G
```

看到这个结果心里是欣喜的，因为那30G就是扩容的部分，现在在vdb2中只需要将这个分区加入LVM即可扩容。

使用 `pvcreate` 命令创建PV，如果碰到关于检测到签名的WARNING直接输入 `y` 回车确认即可。

```Shell
[root@host ~]# pvcreate /dev/vdb2
WARNING: ext3 signature detected on /dev/vdb2 at offset 1080. Wipe it? [y/n]: y
  Wiping ext3 signature on /dev/vdb2.
  Physical volume "/dev/vdb2" successfully created.
```

使用 `vgextend` 命令扩容VG `vgxxx`

```Shell
[root@host ~]# vgextend vgxxx /dev/vdb2
  Volume group "vgxxx" successfully extended
```

使用 `lvdisplay` 命令查看已存在的LV信息，从这里面可以看到LV Name为 `lvxxx`

```Shell
[root@host ~]# lvdisplay
  --- Logical volume ---
  LV Path                /dev/vgxxx/lvxxx
  LV Name                lvxxx
  VG Name                vgxxx
  LV UUID                o2lYBF-7Txu-SVBv-SXyg-wZ5F-ivcf-RdqIf5
  LV Write Access        read/write
  LV Creation host, time izwz9h0tj0z3gn0gfuxxb1z, 2018-09-19 18:07:49 +0800
  LV Status              available
  # open                 1
  LV Size                <50.00 GiB
  Current LE             12799
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           252:0
```

使用 `lvextend` 扩容LV `lvxxx`，这里将100%的空闲空间全部用于扩容

```Shell
[root@host ~]# lvextend -l +100%FREE /dev/mapper/vgxxx-lvxxx
  Size of logical volume vgxxx/lvxxx changed from <50.00 GiB (12799 extents) to 79.99 GiB (20478 extents).
  Logical volume vgxxx/lvxxx successfully resized.
```

这里已经成功将VG和LV扩容，接下来只需要使用 `resize2fs` 命令重设文件系统大小即可完成LVM扩容！

```Shell
[root@host ~]# resize2fs /dev/mapper/vgxxx-lvxxx
resize2fs 1.42.9 (28-Dec-2013)
Filesystem at /dev/mapper/vgxxx-lvxxx is mounted on /home; on-line resizing required
old_desc_blocks = 7, new_desc_blocks = 10
The filesystem on /dev/mapper/vgxxx-lvxxx is now 20969472 blocks long.
```

使用 `df -h` 命令查看 `/dev/mapper/vgxxx-lvxxx` 分区是否已经扩容成功

```Shell
[root@host ~]# df -h /dev/mapper/vgxxx-lvxxx
Filesystem                     Size  Used Avail Use% Mounted on
/dev/mapper/vgxxx-lvxxx   79G   11G   65G  15% /home
```

到此为止，此次服务器数据盘扩容成功了。官方文档坑还是多，对于运维新手不够友好特别是做开发的兼职运维的这种。总而言之还是自身知识水平不足导致，有空也得多去补补运维知识了哈哈哈哈。