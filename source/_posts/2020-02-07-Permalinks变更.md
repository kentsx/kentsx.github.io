---
title: Permalinks变更及调整Github merge branch
tags: [建站, link, github]
categories:
  - 金融狗在假装码农
photos: []
author: Mr.K
top: 0
date: 2020-02-07 12:15:07
---
今天对网站的内容做了一些修正，让网站能更好地展示。

<!-- more --> 
# Permalinks调整
之前使用的Permalinks为`url/yyyy/title`的形式。但由于我的文章大部分都是中文标题，也就导致了title是有中文的。而链接由于RFC的强制要求，也就会对url中非数字和英文字母进行parse，成为一串%XX的这种形式，非常难看。
另外，我的网页下都有一个分享网址，那就会变的很长，太丑了！

参考了网上的建议，都改成英文和数字的组合形式。由于我的网址是自带了文章年份的，所以，我只要加上文章的月、日、时、分、秒（生成瞬间），那就绝对不可能有重复了。哦，对了如果纯数字，permalink也会有bug，原因不详。所以就加了一个头`pt=`

一开始，用了一个python跑了一圈，加了一个id叫`timestamp: pt=DDHHmmss`这种形式，但这有个弊端，就是每次都需要手动输入。亦或者每次用python跑一圈，但更新时间就一直在变，这也不好。

最后在hexo的官方文档中，找到了方法。我舍弃掉了“秒”这个单位，可以直接调整站点的config来实现。
```
permalink: :year/pt=:month:day:hour:minute/
#最后的/一定要加，不然点击链接就会变成保存文件
```

# Github Branch变更
自从新安装了整个hexo框架以后，每次`hexo d`都是去一个叫`gh-pages`的branch，然后再手动去merge pull request。
而我明明在站点config中设置了git到master branch。

问题可能是出在最新版的hexo-git-deployer上。在module的README中写着，如果repo是github的，则默认是gh-pages。所以一定是我设置的方法不对导致。
找到了设置方法如下：
```
deploy:
  type: git
  repo: 
        github: https://github.com/XXXXX/XXXXXX.git,master
            #此处在repo网址之后，直接",branch_name"，
```
