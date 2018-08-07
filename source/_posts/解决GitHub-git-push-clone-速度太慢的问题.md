---
title: 解决GitHub git push/clone 速度太慢的问题
abbrlink: 7262beec
date: 2018-08-05 22:53:02
tags:
 - GitHub
 - Git
---

### 存在的问题
平时使用 ``git push`` 或 ``git clone`` GitHub上的库时总会遇上速度慢的问题，特别是存在大文件的时候，只有16kb/s的速度。

### 查询域名IP
面对这种问题一般可以采用科学上网解决，但是假设因为特殊条件不能科学上网时可以通过修改本机 ``hosts`` 解决。

首先需要了解的是，在进行push或clone操作时慢是因为 ``github.global.ssl.fastly.net`` 域名被墙，解决方案就是用 [IPAddress.com](https://www.ipaddress.com/) 查询出这个域名的IP地址。

访问 [http://github.global.ssl.fastly.net.ipaddress.com/#ipinfo](http://github.global.ssl.fastly.net.ipaddress.com/#ipinfo) 即可查询。

{% asset_img 1.jpg 查询IP %}

查询到的IP为： ``151.101.13.194`` 。

同时如果你希望顺便提高访问github网站的速度，也可以查询下 ``github.com`` 的IP，一起写入 ``hosts``。

访问 [http://github.com.ipaddress.com/#ipinfo](http://github.global.ssl.fastly.net.ipaddress.com/#ipinfo) 即可查询。

{% asset_img 2.jpg 查询IP %}

查询到的IP为：``192.30.253.112``

### 修改Hosts

接下来就需要修改hosts文件了，该文件所在路径是：``C:\Windows\System32\drivers\etc\hosts``

但是由于权限限制可能没法直接编辑，直接点的方法如下：

打开 ``hosts`` 文件并复制里面的内容

在桌面新建一个同名 ``hosts`` 文件，使用记事本直接打开，然后将复制的内容粘贴进去

将刚刚查询到的IP和域名写进去，注意IP和域名之间有空格。

```
151.101.185.194 github.global.ssl.fastly.net 
192.30.253.112 github.com
```

{% asset_img 3.jpg 修改hosts %}

编辑完后保存 ``hosts`` 文件，将这个桌面的 ``hosts`` 文件复制到 ``C:\Windows\System32\drivers\etc\hosts`` 覆盖掉原来的 ``hosts`` 文件，弹出权限询问直接点继续即可。

### 验证效果
完成后，可以尝试 ``git push`` 或 ``git clone`` 操作，会发现速度从原来的的10+kb/s提速到600kb+以上了，根据网络情况还可以更快，博主提速到了1MB+/s。

也可以尝试访问 [github.com](github.com) 看看访问速度是否有所提升。