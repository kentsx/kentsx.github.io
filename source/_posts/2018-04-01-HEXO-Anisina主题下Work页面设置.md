---
title: HEXO Anisina主题下Work页面设置
timestamp: pt=0401152819
date: 2018-04-01 15:28:19
tags:
- HEXO
- Anisina
- 建站
categories: 金融狗在假装码农
---
> 因本人不是计算机出身，所以对语言多为一知半解，经过多番研究，终于找到了如何设置Anisina主题下Work模式下内容。
<!--more-->
### 适用范围
* Hexo框架，且需要为3.0及以上版本
* Anisina主题（本主题个人认为比较简洁，适合大多数人使用）
* 若你是用的不是Anisina，但是也涉及到数据文件的设置，那么仅供参考，具体配置请自行调整

### 在主页上设置Work页面
#### 在Hexo根目录下打开git
```
hexo new page "work" 
#此处引号中内容可自定义,则下文文件夹名称会改变，注意大小写，Github是区分大小写的
```
#### 在`source\work`件夹内找到`index.md`文件
打开后，在两个`---`之间加入以下内容：
```
layout: works 
#此处只能用works，因为主题作者后续的布局使用的是这个名字
```
如图：
![workpage1](https://file.kmore.ml/blog_img/20200210223828.png)

保存后关闭。

#### 出现Work的目录标签
此时，在git中`hexo clean && hexo s -g`之后，你就可以在本地预览到你的主目录上多了`WORK`这个标签。

但是点开之后，是空的，只有基本的头像和名字。这是因为我们还没有配置内容文件。

### 配置Work页中内容
> 参考Hexo官方关于[数据文件](https://hexo.io/zh-cn/docs/data-files.html)的介绍，并结合Anisina作者在`works.ejs`文件中的配置。
#### 新建文件及文件夹

* 在`source`文件夹中创建文件夹`_data`，注意此处有下划线;
* 在`source\_data`文件夹中，新建文件txt文件，并将此文件名与格式改为`project.yml`。

#### 设置`project.yml`文件内容

> 如下图设置文件中内容，为方便大家修改，此处提供代码块，内容请自行修改（文章末尾提供压缩包）：

```
project1: 
    title: 以下内容仅用于模板占位
    date:  未知
    img_link: https://XXXXXXXX.jpg
    use:
        lan1: java
        lan2: C++
        
    subTitle: 测试单个
    direction: 本项目为个人博客项目，主要目的是用于自己练手几挑战下自己。
    link: http://kentspace.xyz
    
project2: 
    title: 春游
    date:  2018-3-10
    img_link: https://XXXXXXXX.jpg
    subTitle: 测试multiple
    direction: 用于展示部分个人摄影照片
    link: http://kentspace.xyz
```
![workpage2](https://file.kmore.ml/blog_img/20200210223852.png)


**特别注意我在图片中写的内容及冒号要用英文下的冒号，且需要和后面内容空一格。内容写完后保存**

### 再次生成及部署
在git中`hexo clean && hexo s -g`后即可预览，当然要真正使用还需要`deploy`。

> 完成后效果图可以参考[我的Work页面](https://kentspace.xyz/work)，欢迎提问和留言！
> 曾经的网页界面，目前已经更新为Ocean

> 附件: 下载解压后，将整个文件夹放至source文件夹内。下载[_data文件](https://kentsx-github-1256168505.cos.ap-shanghai.myqcloud.com/attachmet/_data.zip)
