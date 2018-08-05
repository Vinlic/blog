---
title: 给Hexo博客加只看板猫
abbrlink: 56088
date: 2018-08-03 10:29:14
tags: 
 - Hexo
 - Live2D
---

### 效果
{% asset_img 1.gif [看板猫] %}

### 准备
在安装之前先把模型包克隆到本地，进入找到白猫的文件夹，白猫的模型名字是 ``live2d-widget-model-tororo`` ，你也可以选择其他模型。

```Shell
$ git clone https://github.com/xiazeyu/live2d-widget-models 
$ cd live2d-widget-models/packages
```

在Hexo项目目录建立一个文件夹： ``live2d_models``

将在模型文件夹里的 ``live2d-widget-model-tororo`` 文件夹复制到 ``live2d_models`` 中，并改名为 ``tororo`` 。

模型准备完毕。

### 安装hexo-helper-live2d

[hexo-helper-live2d](https://github.com/EYHN/hexo-helper-live2d/blob/master/README.zh-CN.md) 是一个借助Live2D技术在网页展示伪3D形象的Hexo插件

```Shell
$ npm install hexo-helper-live2d --save
```

### 配置hexo-helper-live2d

向Hexo的 ``config.yml`` 添加以下配置

```Bash
live2d:
  enable: true    # 是否开启插件
  scriptFrom: local    # 默认local，也可以使用jsdelivr CDN：jsdelivr
  pluginRootPath: live2dw/    # 插件在站点上的根目录（默认）
  pluginJsPath: lib/    # 脚本文件相对与插件根目录路径（默认）
  pluginModelPath: assets/    # 模型文件相对与插件根目录路径（默认）
  tagMode: false    # 标签模式, false是全站都显示，true是只有使用标签才生效，具体请查看文档
  debug: false    # 调试, 是否在控制台输出日志
  model:
    use: tororo    # 模型名称，刚刚在live2d_models内的文件夹名称
  display:
    position: left    # 显示位置，left为左边，right为右边
    width: 150    # 显示宽度
    height: 300    # 显示高度
  mobile:
    show: false    # 手机端是否显示
```

```
标签模式：在你想插入的页面的 </body> 前插入 {{ live2d() }} 或 <%- live2d() %>。 将配置中的 tagMode 设置为 true，然后插件将只会在拥有live2d标签的页面出现。
```

配置完毕后就可以开始使用啦:stuck_out_tongue_closed_eyes:。