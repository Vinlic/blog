---
title: 使用超快的Android模拟器——Genymotion
abbrlink: b5e44cfa
date: 2018-08-04 23:56:19
tags:
---

### 简介

Genymotion是一款强悍的跨平台Android模拟器，启动速度堪比真机调试，它基于Oracle VM VirtualBox，支持GPS、重力感应、光、温度等诸多传感器，并支持OpenGL 3D加速、电池电量模拟，跨平台方面能够运行在Windows、Linux、Mac系统。

之所以这么快我认为是因为Genymotion使用x86架构并进行了诸多优化，使得性能大幅提升，开机和打开应用最快仅需几秒，是时候摆脱开机要好几分钟的AVD了。

### 特殊要求
需要特别注意的是：Genymotion要求你电脑的CPU支持VT-X或AMD-V技术，它们分别是Intel或AMD的CPU所支持的虚拟化技术名称，也就是说需要到主板BIOS开启CPU虚拟化技术才能使用Genymotion，不然启动模拟器时会报错。如何开启请自行百度自己电脑主板的BIOS如何开启CPU的虚拟化技术，可以先下载一个 ``securable.exe`` ，它可以帮助你识别你的CPU是否支持虚拟化技术，如果不支持则无法开启使用。

{% asset_img 5.jpg 虚拟化技术支持 %}

### 安装Genymotion

首先进入 [Genymotion用户注册页面](https://www.genymotion.com/account/create/) 填写和选择相关信息后点击 CREATE ACCOUNT。

{% asset_img 1.jpg 注册Genymotion %}

点击后创建后，它们会向你填写的邮箱发送一封激活账户邮件，去你的邮箱进入激活邮件点击Click here链接完成激活

{% asset_img 2.jpg 注册Genymotion %}

{% asset_img 3.jpg 激活Genymotion %}

接下来我们点击下面那个不起眼的 ``Download Genymotion for personal use`` 链接，意思是下载仅个人使用的Genymotion（个人免费），如果你打算使用它用于商业产品开发则需要点击购买按钮，我们只是作为个人开发测试使用就使用免费的吧，点击后会进入下载页面。

{% asset_img 4.jpg 下载Genymotion %}

由于博主是在Windows系统安装，所以这里是下载Windows版本的。

如果你还没有安装VirtualBox，可以选择第一个Download，这个是附带了VirtualBox的Genymotion安装包

如果已安装直接点击第二个Download即可，但是如果在使用Genymotion时出现识别不到VirtualBox或者启动模拟器时报错，请尝试更换到 [VirtualBox 5.2.6](https://download.virtualbox.org/virtualbox/5.2.6/VirtualBox-5.2.6-120293-Win.exe)，因为某些版本的VirtualBox可能和Genymotion会有兼容性问题。

安装完毕后，桌面上应该会出现一个Genymotion和Genymotion Shell，我们暂时只使用Genymotion。

进入之后可能会要求登录，直接使用刚刚创建的账号登录即可，无需注册许可。

{% asset_img 6.jpg 安装Genymotion %}

### 添加模拟器

点击导航栏的 ``Add`` 按钮，弹出选择设备窗口。

{% asset_img 7.jpg 添加模拟器 %}

选择你希望运行的模拟器的Android版本并点击 ``Next``。

{% asset_img 8.jpg 添加模拟器 %}

这里可以浏览此模拟器默认的系统信息，也可以修改模拟器名字或者直接默认并点击 ``Next``。

{% asset_img 9.jpg 添加模拟器 %}

接下来只需要等待模拟器所需相关资源下载完成后点击 ``Finish``。

这个时候就会发现模拟器列表多出一个模拟器，双击模拟器就可以启动了。

{% asset_img 10.jpg 添加模拟器 %}

{% asset_img 11.jpg 启动模拟器 %}

启动速度可谓非常快了，从弹出模拟器窗口到系统启动仅用了6秒。

{% asset_img 12.gif 启动模拟器 %}

### 添加ARM支持
简介部分已经介绍过了，Genymotion是使用x86架构的模拟器，虽然变快了，但是并不是一劳永逸的，Genymotion从2.0版本已经移除了ARM支持库，因此无法安装在ARM环境下运行的APP，因此我们需要把ARM支持库给安装回来。

模拟器Android版本5.0以上：

下载 [ARM_Translation_lollipop.zip](http://www.pc6.com/softview/SoftView_509493.html)

模拟器Android版本5.0以下：

下载 [Genymotion-ARM-Translation.zip]()

> 下载后注意不要解压！

