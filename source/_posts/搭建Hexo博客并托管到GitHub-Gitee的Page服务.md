---
title: 搭建Hexo博客并托管到GitHub/Gitee的Page服务
abbrlink: 31846
date: 2018-08-02 23:23:59
tags: 
 - Hexo
 - Git
---

### 介绍
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页，可以先访问他们官网了解一下：[hexo.io](https://hexo.io/)。

在尝试过诸如WordPress这些博客系统后，觉得这些系统都太笨重了，直到看见了Hexo，刚好满足我的需求，配上Nginx的话甚好，现在是直接部署在github上:yum:。

### 准备

#### 创建GitPage项目
请确保你已经有一个 **[GitHub](https://github.com/)** 或 **[Gitee](https://gitee.com/)** 的账号。这两个托管平台都支持静态页面服务，将Hexo博客部署到上面可以让你省下服务器的经费和维护精力，在部署之前你必须在他们的平台创建Page项目，如果你不希望将Hexo博客部署上去可略过本步骤。

请参考教程自行创建：

[GitHub创建Page项目](https://pages.github.com/)

[Gitee码云创建Page项目](http://git.mydoc.io/?t=154714)

创建完成后，请确保获得的Page地址（如xxx.github.io 或 xxx.gitee.io）可访问。另外，可以先复制Page项目的仓库地址（如https://github.com/xxx/xxx.github.io.git或https://gitee.com/xxx/xxx.git），配置部署仓库信息时需要用到。

#### 安装Git
如果已经安装请略过这一步
- Windows：下载并安装 [Git](https://github.com/waylau/git-for-win)
- Mac：使用 Homebrew, MacPorts : ``brew install git``;或下载 [安装程序](http://sourceforge.net/projects/git-osx-installer/) 安装
- Linux (Fedora, Red Hat, CentOS) : ``sudo yum install git-core``
- Linux (Ubuntu, Debian) : ``sudo apt-get install git-core``

> 对于Windows用户来说，安装Git For Windows时，请勾选Add to PATH选项。
另外，也可以使用Git Bash，这是git for windows自带的一组程序，提供了Linux风格的shell。打开它的方法很简单，在任意位置单击右键，选择“Git Bash Here”即可。由于Hexo的很多操作都涉及到命令行，可以考虑始终使用Git Bash来进行操作。

#### 安装Node.js
如果已经安装请略过这一步
- Windows : 下载并安装 [Node.js](https://nodejs.org/)
- Mac : 下载并安装 [Node.js](https://nodejs.org/)
- Linux : 初学者建议直接安装 [Node.js](https://nodejs.org/en/download/)，老司机建议使用[NVM](https://github.com/creationix/nvm/blob/master/README.md)管理Node版本

安装后可以通过命令行检查npm以及git命令是否可用。

### 安装Hexo
在命令行使用NPM安装Hexo

```Shell
$ npm install -g hexo-cli
```

npm安装速度慢？<a href="/p/50211/" target="_blank">试试配置npm镜像源为淘宝</a>

### 初始化Hexo
在安装后需要建立Hexo项目，先选择一个存放Hexo项目的路径位置，在这个路径下运行``hexo init``命令初始化Hexo项目，这里假设项目名称为Test，接下来都将继续使用这个名称。

```Shell
$ hexo init Test
```

等待初始化完成后出现 ``INFO  Start blogging with Hexo!`` 即成功。

接着进入Test目录运行 ``npm install``。

```Shell
$ cd Test
$ npm install
```

如果执行不出差错，在Hexo项目文件夹内容会是下面这样。

{% asset_img 1.jpg [Hexo项目目录] %}

### 配置Hexo
Hexo项目的配置文件是 ``_config.yml`` ，使用任意代码编辑器打开这个配置文件，里面默认的Site配置有以下内容，当前只需要修改这部分配置即可。

```Bash
# Site
title: Vinlic的个人博客    # 博客标题
subtitle: Vinlic Blog    # 博客副标题
description: 本博客是...    # 博客简介
keywords: 关键字1,关键字2    # 博客关键字（用几个关键字描述你的博客，英文逗号隔开）
author: 蒂花之秀    # 博客作者
language:    # 博客使用语言，根据主题支持的语言来配置，暂时空着
timezone: Asia/Shanghai    # 博客时区设置
```

配置Git仓库信息，配置后在执行部署（deploy）时hexo会自动把编译成静态的页面自动上传到Page项目中。
```Bash
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git    # 设置为git
  repo: https://github.com/xxx/xxx.github.io.git    # Git仓库地址
  branch: master    # Page项目的分支，直接master
```

### 测试Hexo
Hexo项目在建立时默认自带一个 ``landscape`` 主题，如果希望更换其他主题请看下一步，这里在本地先启动Hexo博客试一下效果。
在Test项目目录中运行：

```Shell
$ hexo s
```

此处是简写命令，也可以使用 ``hexo server`` 完成同样的操作，如果启动服务器发生错误，可能是默认监听的 ``4000`` 端口发生冲突，请解决端口冲突或使用 ``-p`` 参数指定端口：``hexo s -p 5000``。

启动成功后，在浏览器访问 **[http://localhost:4000/](http://localhost:4000/)**
不出意外，应该能够看到博客页面了。

### 下载主题
Hexo官网为我们提供了200+个主题进行挑选：**[Hexo官网主题下载](https://hexo.io/themes/)**
选择一个自己喜欢的主题，点击主题图片可预览效果，点击主题名称将跳转到该主题的GitHub仓库。
接下来，以本博客的主题 [AirCloud](https://github.com/aircloud/hexo-theme-aircloud) 作为示例展示安装配置流程。
在Test项目目录中运行以下Git命令克隆主题到博客
主题的克隆方式都是一样的： ``git clone [主题GitHub仓库链接] themes/[主题名称]`` ，主题名称将用于接下来的配置中。

```Shell
$ git clone https://github.com/aircloud/hexo-theme-aircloud themes/aircloud
```

这里主题名称是 ``aircloud``。

### 配置主题
主题的GitHub仓库里面一般会有安装文档指导如何正确配置他们的主题，每个主题的配置项可能都有所不同，一般主题应该会提供配置文件Demo，参照Demo配置即可，但是要注意配置项的名称后的分号和值之间一定要有空格，例： ``language: zh`` 。

使用任意代码编辑器打开Test项目目录中的 ``_config.yml``  ，根据该主题作者提供的 [配置文件Demo](https://github.com/aircloud/hexo-aircloud-blog/blob/master/_config.yml) 修改 ``_config.yml`` 。

#### 站点
在之前配置的基础上进行以下添加和修改
```Bash
language: zh    # 此主题只支持zh和en两种语言，二选一
email: vinlic@vinlic.com    # 个人邮箱
github_username: Vinlic    # GitHub用户名
anchorjs: true    # 默认开启
sidebar-avatar: img/avatar.jpg    # 博客头像，文件放到source/img内
```

#### URL
修改站点URL为在GitHub/Gitee申请的Page地址，如 xxx.github.io 或 xxx.gitee.io ，做如下配置
```Bash
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://xxx.github.io/    # 使用Page地址
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:
```

#### 修改主题
更换主题为之前克隆的主题名称，修改 ``theme`` 为 ``aircloud`` 。
```Bash
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: aircloud    # 将原主题名替换为aircloud
```

#### 友情链接
如果你需要给你的博客添加友情链接请往配置文件添加以下内容
```Bash
# Friends
friends: [
    {
        title: "XXX网站",    # 友情链接网站名
        href: "http://www.xxx.com"    # 友情链接网站链接
    },
    {
        title: "XXX网站2",
        href: "http://www.xxx2.com"
    }
]
```

### 命令
至此，距离完成搭建已经不远了，有必要认识一下几个主要命令
```Shell
$ hexo init [项目名]    生成Hexo博客项目
$ hexo clean    清除db.json和静态目录public
$ hexo new [文章名]    生成一篇默认布局的空文章
$ hexo generate 或 hexo g    生成静态文件
$ hexo server 或 hexo s    启动本地服务器调试
$ hexo deploy 或 hexo d    部署到GitPage仓库
```

更多命令可参考：[Hexo命令文档](https://hexo.io/zh-cn/docs/commands.html)

### 部署
在部署之前先在本地再测试一下，先清除一下之前的缓存，然后重新生成静态文件，再启动服务器。
```Shell
$ hexo clean
$ hexo g
$ hexo s
```

启动后在浏览器访问，如果一切正常，应该能够看见默认的一篇HelloWorld。

接下来就可以进行部署了，运行以下命令上传静态文件部署到GitPage上。

```Shell
$ hexo d
```

如果是部署到GitHub上可能会稍微慢点，耐心等一等就好了。

部署完成后就可以通过Page地址访问了，如 xxx.github.io 或 xxx.gitee.io :smiling_imp:。

每个主题都有不同的特性，可以细读主题文档，发掘更多主题的功能，Hexo拥有许多的插件可选，插件也有对应的文档指导如何安装和配置插件，可以尝试安装插件，下载插件请访问[Hexo官网插件下载](https://hexo.io/plugins/)。

如果一切工作都完成后，建议将Hexo文章链接唯一化，请参考：[
Hexo博客链接唯一化方案，解决中文链接问题](/p/18664/)