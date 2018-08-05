---
title: Hexo博客链接唯一化方案，解决中文链接问题
abbrlink: 18664
date: 2018-08-03 11:09:24
tags: Hexo
---

### 文章链接需要唯一化
Hexo博客默认是将文章标题用于文章URL的，这会存在一个严重的问题，当文章标题使用中文时，URL中也会使用中文。导致以下问题：

1.中文URL复制起来被转码后很长，就像这种又臭又长：``/2018/07/24/%E6%88%91%EF%BC%8C%E4%B8%80%E4%B8%AA%E6%99%AE%E9%80%9AIT%E4%BA%BA%E7%9A%84%E7%94%9F%E6%B6%AF%E5%9B%9E%E9%A1%BE/#%E4%BA%BA%E7%94%9F%E7%AC%AC%E4%B8%80%E4%B8%AA%E6%9C%BA%E6%A2%B0%E9%94%AE%E7%9B%98``

2.修改文章标题或发布时间，文章URL都会改变，不利于搜索引擎收录和分享

### 安装hexo-abbrlink
[hexo-abbrlink](https://github.com/rozbo/hexo-abbrlink) 是一个实现链接唯一化的插件

```Shell
$ npm install hexo-abbrlink --save
```

### 配置hexo-abbrlink
修改Hexo的 ``config.yml`` 内的 ``permalink``：

```Bash
permalink: posts/:abbrlink/    # posts可自行更换
```

建议permalink后面跟着的内容最好不要超过两个斜杠，影响爬虫爬取，因为一般搜索引擎爬虫不爬取深度超过三层的内容。

向 ``config.yml`` 添加以下配置：

```Bash
# abbrlink config
abbrlink:
  alg: crc32  # 算法 crc16(默认) 或 crc32
  rep: hex    # 进制：dec(默认) 或 hex
```

不同的算法和禁止组合会得到不同的链接，下面提供了参考，如何选择就看需要了，需要注意的是使用 ``crc16`` 算法的文章数上限是 ``65535``。

```
crc16 & hex
https://blog.vinlic.com/posts/66c8.html

crc16 & dec
https://blog.vinlic.com/posts/65535.html

crc32 & hex
https://blog.vinlic.com/posts/8ddf18fb.html

crc32 & dec
https://blog.vinlic.com/posts/1690090958.html
```