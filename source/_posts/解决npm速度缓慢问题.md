---
title: 解决npm速度缓慢问题
abbrlink: 50211
date: 2018-08-03 10:10:00
tags: Node.js
---

### 问题
众所周知，国内使用官方的npm有时候速度很慢，为了解决这些问题，出现了[淘宝npm镜像站](http://npm.taobao.org/)以及[cnpm](https://github.com/cnpm/cnpm)，接下来就讲解不同情境下提高npm速度的方法。

### 临时使用
临时使用淘宝npm镜像安装

```Shell
$ npm install [package] --registry https://registry.npm.taobao.org
```

### 一劳永逸
配置npm镜像源为淘宝npm镜像

```Shell
$ npm config set registry https://registry.npm.taobao.org
```

配置成功后可以用以下命令验证是否配置成功：

```Shell
$ npm config get registry
```

### 安装CNPM
先运行以下命令通过npm从淘宝npm精选安装cnpm

```Shell
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```

接下来就可以使用cnpm安装NPM包了

```Shell
$ cnpm install [package]
```