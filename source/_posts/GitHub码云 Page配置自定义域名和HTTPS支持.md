---
title: GitHub/码云 Page配置自定义域名和HTTPS支持
abbrlink: 50505
date: 2018-08-02 23:24:43
tags: 
 - Hexo
 - SSL
---

### 
在此之前，你必须有一个可用的域名，如： ``www.example.com``

### 添加CNAME解析
到你的域名注册商那里添加一条CNAME解析记录，这里是以阿里云域名解析为例。

记录值为你的Page地址，例如：``xxx.github.io`` 或 ``xxx.gitee.io``，主机记录是你的域名前缀，假设域名是 ``www.example.com`` 那么主机记录就是 ``www`` 。

{% asset_img 6.jpg CNAME解析记录 %}

### GitHub配置自定义域名

添加完解析后就需要配置自定义域名了，GitHub有两种配置方式：

#### 方式1
**打开Page项目 -> Settings -> GitHub Pages -> 填写Custom domain -> Save**

{% asset_img 4.jpg GitHub Page %}

#### 方式2
向Page项目根目录上传一个CNAME文件，文件内容是你自定义的域名

```
www.example.com
```

更多内容请参考：[为GitHub Pages站点添加或删除自定义域名](https://help.github.com/articles/adding-or-removing-a-custom-domain-for-your-github-pages-site/)

如果是使用Hexo搭建的博客，可以把CNAME文件放入 ``source`` 文件夹中，这样每次生成都会自动把CNAME文件放到public文件夹。

### 码云配置自定义域名

码云对配置自定义域名是收费的，但提供了一个月的Pro试用资格，可以试用一下，或者付费（我觉得没有必要），试用步骤如下：

试用方式：**打开Page项目 -> 服务 -> Gitee Pages -> 右侧点击试用一个月Pro版本**

升级后接下来只需填写自己的并启动域名即可：

{% asset_img 7.jpg 码云 Page %}

更多内容请参考：[码云Pages使用帮助](http://git.mydoc.io/?t=154714)

### GitHub开启HTTPS

GitHub Page项目默认的地址已经是自带HTTPS无需配置，如： ``https://xxx.github.io`` ，以下是自定义域名开启HTTPS方法。

开启方式：**打开Page项目 -> Settings -> GitHub Pages -> 勾选Enforce HTTPS**

{% asset_img 3.jpg GitHub Page %}

如果是灰色不可勾选，请等待几天，可能证书还没发放。

开启后会显示有https协议的URL：

{% asset_img 5.jpg GitHub Page %}

生效后，你的自定义域名就可以使用HTTPS访问了，由于浏览器有缓存可能不会及时生效，可以尝试清除浏览器缓存或者使用其他浏览器访问测试。:grinning:

了解更多：[GitHub官方开启HTTPS文档](https://help.github.com/articles/securing-your-github-pages-site-with-https/)

### 码云开启HTTPS

码云用户默认的Page地址已经是自带HTTPS无需配置，如： ``https://xxx.gitee.io`` ，以下是自定义域名开启HTTPS方法。

请确保你已经在使用码云Pages Pro版本，因为只有这个版本才支持自定义域名配置HTTPS，如果还未升级请参考[码云配置自定义域名](./#码云配置自定义域名)。

首先需要申请一个SSL证书才能配置，可以从 [FreeSSL.org](https://freessl.org/?from=gitee) 免费申请SSL证书，申请方法请参照该网站的说明，申请得到证书后就可以进行下一步的配置证书了。

配置方式：**打开Page项目 -> 服务 -> Gitee Pages -> 配置域名证书 -> 填写证书、私钥文件内容 -> 提交**

生效后，你的自定义域名就可以使用HTTPS访问了，由于浏览器有缓存可能不会及时生效，可以尝试清除浏览器缓存或者使用其他浏览器访问测试。:grinning:

### 使用Cloudflare实现HTTPS
如果以上平台无法提供HTTPS服务时，你也可以采用以下解决方案。

在这之前请确保已经完成了 [添加CNAME解析](./#添加CNAME解析)

让 [Cloudflare](https://www.cloudflare.com/) 代理SSL实现HTTPS。

> 需要特别说明的是，这个操作其实是把你域名的DNS解析服务器换成Cloudflare的DNS解析服务器，域名解析工作改由Cloudflare来完成，达到代理SSL的目的，实际上不会啥影响，如果你不希望更换，可以放弃使用Cloudflare。

#### 注册账号

进入 [Cloudflare账号注册](https://dash.cloudflare.com/sign-up)

{% asset_img 1.jpg 账号注册 %}

注册完成后需要填写域名，比如我的自定义域名是 ``blog.vinlic.com`` ，就填这个

{% asset_img 2.jpg 填写域名 %}

接下来Cloudflare会开始查询此域名的DNS记录，也就是把你之前这个域名设置的解析记录全部导入到这边，进入下一步后它们会让你选择一个方案，选择免费（Free）确认就好了。

确认之后，会显示当前已查询到的DNS记录，直接下一步。

#### 修改DNS服务器

在上一步之后Cloudflare会提供两个新的DNS服务器地址，将它们复制起来。

{% asset_img 8.jpg 修改DNS %}

接着到域名注册商那修改DNS服务器，这里截图的是阿里云DNS修改。

{% asset_img 9.jpg 修改DNS %}

{% asset_img 10.jpg 修改DNS %}

修改完DNS后，回到Cloudflare点击继续进入主界面。

主界面应该显示 ``Status: Website not active``，这时需要等待DNS生效，一般1个小时左右就能生效了，如果超过一天还未生效建议查看是否正确修改DNS，生效后再进入Cloudflare主界面，点击 ``Recheck Nameservers`` 重新检查DNS服务器，如果已生效，Status就会显示 ``Active`` 并显示为绿色，这样就修改成功了。

{% asset_img 11.jpg 修改DNS %}

#### 开启SSL加密

点击Crypto功能，并设置SSL为 ``Full`` 。

{% asset_img 12.jpg 开启SSL %}

生效后，你的自定义域名就可以使用HTTPS访问了:grinning: