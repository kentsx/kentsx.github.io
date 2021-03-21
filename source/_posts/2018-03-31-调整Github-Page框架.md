---
title: 调整Github Page框架
timestamp: pt=0331160346
date: 2018-03-31 16:03:46
tags:
- github
- Hexo
categories: 金融狗在假装码农
---

## 替换框架始末
昨晚终于下定决定对原先的Github Page进行调整，使用Hexo对原blog进行替换。
<!--more-->
原本是抱着使用简单的目的，开始使用杨的[Tiny Blog](https://github.com/YangHanqing/tinyblog)项目的，非常感谢杨的这个非常好的创意，利用github issue和静态的页面，组成这样一个对markdown支持度如此之高的blog page（现在换成Hexo的页面，但似乎对于Markdown的表格语言并不支持，估计后期还得再做插件的调整）

可能我有一些外貌控，所以tiny blog的界面极简，同时排版靠右，让我觉得不是特别适应。

另外使用之后发现存在一些问题和不便。主要有以下几点：
* 博客内容无法设立tag
* 对内容不能进行归档，例如按照日期等
* 以上两个问题在博文数量上升之后会显得格外突出

## 选择Hexo原因
选择Hexo的原因其实比较简单，在几个为数不多的选择中，我认为Hexo是最为简便的。

首先，Wordpress过于庞大，不太适合在Github这样的体系中搭建，我之前是用过，认为效果不是很好，同时自己对博客appearance的设定有点复杂。

第二，在其余几个选项中，我认为Hexo最为适合Github，整个的搭建过程简单，同时又有非常多的开源的themes可以选择。

## Hexo体验
整体而言，Hexo的体验还是不错的，再配合使用了Git和Node.js之后，编辑的速度和deploy的反馈都不错。

困扰的点主要是以下几个：
* 本人不是码农出身，很多的语法还不熟悉，还有待进一步尝试
* [Anisina](https://github.com/Haojen/hexo-theme-Anisina)（V3.1）这个主题很不错，很符合我的appetite，不过原作者的文档写得一般般啦，导致有不少设置需要多次尝试才知道。如果之后有空，我会在作者基础上，再写得更详细一些。
* 七牛云这个储存网站我刚开始使用，还没有很多心得，待时间久一些我再来反馈。

## 后记
最后，还是感谢TinyBlog的作者Yang以及Hexo主题Anisina作者Ma，感谢你们的无私奉献。

此外，在Ma的原文下看到不少评论，其中ZSQ的关于配置调整的文章（[点此](https://www.willingcloud.com/2018/03/06/hexo-github-%E5%88%9B%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/)）有不少借鉴意义。还有就是进一步加入评论功能以及SEO方面的改进。

