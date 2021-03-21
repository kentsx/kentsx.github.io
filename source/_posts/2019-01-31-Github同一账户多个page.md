---
title: Github同一账户多个page
tags: [建站, git, github]
categories:
  - 金融狗在假装码农
abstract: 【】
message: 感谢浏览，本文内容已加密，请输入密码：
top: 0
timestamp: pt=0131130440
date: 2019-01-31 13:04:40
photos:
author: Mr.K
password:
---
> 本文系Mr.K亲自试验后的结果。之前在网上找到了不少github搭page的文章，但都是以在域名后加`.com/xxxxx`的形式。

申请域名这些我们就直接跳过了，因为这个网上随处可以找到。接下来的DNS解析，才是本文的核心部分。

<!--more-->
**环境声明**
域名商：porkbun【Github不支持SSL证书上传，且Github的enforce HTTPS依然会报错】
DNS解析：Cloudflare，而且免费提供了CDN【登陆速度还不错】
Github托管页面包括：首页`www.kenny4ever.com`，小型文件`file.kenny4ever.com`，博客`blog.kenny4ever.com`。此三个页面为三个不同的repo，且在同一账号内。

> 之所以建三个不同repo的原因是为了维护和更换方便，相互直接不要干扰；另一方面，像file这个repo可以独用1GB的空间；再者我的blog页面是由hexo做的，所以一开始就独占了一个repo不想再混同了。

**Repo设置**
在首页和小型文件repo的根目录中，分别建立一个`CNAME`文件（无任何后缀），在文件里就分别加入一行二级域名，我的是`www.kenny4ever.com`、`file.kenny4ever.com`；不需要加`https`或者`http`。在hexo的source文件夹中也加入`CNAME`文件，`blog.kenny4ever.com`【域名和具体hexo的config文件如何配置，请根据自己的情况来】。

**DNS解析设置**
在termial中ping一下自己的xxxx.github.io。把后面的ip地址记录下来。剩下的请直接看图说话！
- A记录：name填写`@`，ipv4填写`刚才ping来的ip`
![github01](https://file.kmore.ml/pic/2019-01-31-Github同一账户多个page/github01.png "A记录")
- 三条CNAME记录：name填写`每个二级域名的`，domain name填写`xxxx.github.io`(xxx为你的github登录名)
![github02](https://file.kmore.ml/pic/2019-01-31-Github同一账户多个page/github02.png "3条CNAME")
