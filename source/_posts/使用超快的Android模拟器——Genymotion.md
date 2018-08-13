---
title: 使用超快的Android模拟器——Genymotion
abbrlink: b5e44cfa
date: 2018-08-04 23:56:19
tags:
  - Android
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

### 配置ADB
Genymotion默认的工具包是没有提供ADB支持的，想要使用ADB，就得配置为本机的Android SDK。

还没下载Android SDK？[点击下载Android SDK 24.4.1](https://dl.google.com/android/installer_r24.4.1-windows.exe?utm_source=androiddevtools&utm_medium=website) （强烈建议不要用迅雷下载，为了避免再出现类似 [XcodeGhost](https://www.zhihu.com/question/35721299) 事件）

下载完成安装后，进入Android SDK安装目录，打开 ``SDK Manager``，确保这三个工具都已安装，如果未安装请选中它们并点击右下角的 ``Install packages`` 同意协议并安装，如果安装工具缓慢或失败请百度解决。

{% asset_img 15.jpg 配置ADB %}

打开Genymotion导航栏的 ``Setting`` 并切换到 ``ADB`` 选项卡，改成 ``Use custom Android SDK tools`` 。

点击 ``Browse`` 找到Android SDK安装目录（如 ``android-sdk-windows`` 文件夹），选择此文件夹，如果下面显示 ``Android SDK tools found successfully`` 就成功了，如果显示错误请尝试搜索错误内容自行解决问题。

{% asset_img 16.jpg 配置ADB %}

使用ADB需要配置Android SDK环境变量，你可能已经配置过，如果还未配置此环境变量请查看此教程：[如何配置Android SDK环境变量](https://jingyan.baidu.com/article/15622f2434bc5cfdfcbea51c.html)

配置完毕后请打开 ``cmd`` 输入 ``adb`` 命令回车运行，查看是否可用，可用即为配置成功。

{% asset_img 17.jpg 配置ADB %}

然后重启一下模拟器。

### 添加ARM支持
简介部分已经介绍过了，Genymotion是使用x86架构的模拟器，虽然变快了，但是并不是一劳永逸的，Genymotion从2.0版本已经移除了ARM支持库，因此无法安装在ARM环境下运行的APP，因此我们需要把ARM支持库给安装回来。

下载 [Genymotion-ARM-Translation_v2.0.zip](https://github.com/Vinlic/blog/raw/master/files/Genymotion-ARM-Translation_v2.0.zip)

> 下载后注意不要解压！

将压缩包拖入开启的Android模拟器界面。

{% asset_img 13.jpg 安装ARM支持库 %}

拖拽后会有两种情况：

第一种：弹出安装询问弹框，这种情况直接点击 ``OK`` 就会进行安装，接着会询问是否重启模拟器点击 ``OK``。

第二种：只出现 ``File successfully copied to:/sdcard/Download`` 弹框，这时候就需要手动进行安装了，博主遇到的就是这种情况。

{% asset_img 14.jpg 安装ARM支持库 %}

先手动上传压缩包，启动 ``cmd`` 进入刚刚下载的压缩包的文件夹，并输入 ``adb push Genymotion-ARM-Translation_v2.0.zip /sdcard/Download`` 命令运行，以下演示的命令是在博主电脑上的，压缩包放在桌面。

```Bash
C:\Users\Vinlic-PC> cd Desktop 
C:\Users\Vinlic-PC\Desktop> adb push Genymotion-ARM-Translation_v2.0.zip /sdcard/Download
```

等待上传完成后运行 ``adb shell flash-archive.sh /sdcard/Download/Genymotion-ARM-Translation_v2.0.zip`` 命令，安装ARM支持库。

```Bash
C:\Users\Vinlic-PC\Desktop> adb shell flash-archive.sh /sdcard/Download/Genymotion-ARM-Translation_v2.0.zip
```

安装成功后就可以重启模拟器了。

{% asset_img 18.jpg 安装ARM支持库 %}

重启完成后就可以自由安装了。

### ARM程序移植
APP安装虽然是解决了，但是对于一些在命令行运行的基于arm架构的binary文件时会出现 ``cannot execute binary file`` 等类似问题。

针对这类问题，一般会采用交叉编译移植的方法。

具体操作流程请看：[通过交叉编译移植基于arm平台程序到x86平台](/p/9cbe3a39/)