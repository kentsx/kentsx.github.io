---
title: Travis部署Hexo记录
tags: [Travis, Hexo]
categories:
  - 金融狗在假装码农
photos: []
author: Mr.K
top: 0
date: 2021-03-21 18:00:41
---
今天终于心血来潮，来弄一下这个事情，以前觉得用Hexo写博客的时候，确实有一个纠结的地方，就是每次换电脑或者出差的时候，就不方便。

<!-- more --> 

现在一部分解决了这个问题，就是使用Travis来做这个中间的步骤。网上也有很多案例，但无奈我试了授权的方法，依然没能成功，最后是在综合了几篇的方法之后，最终成功了。

## 目标及准备工作
### 目标2个
1、将博客源码放在blog repo的blog-source分支；
2、使每次push都可以直接启动Travis并且生成新的Github page。

### 新建分支并push
0、删除本地hexo生成的文件夹，我只留下了`scanfolds`,`source`,`themes`文件夹和根目录下已经有的文件，这样这个hexo项目应该基本是清空完了，只留下了核心的部分。
1、**新建git**：在hexo文件的根目录，`git init`
2、**连接remote库**：`git remote add origin git@github.com:xxxx/xxxx.git`，这里的xxxx根据自己的来定，可以直接在github网页复制得到；
3、**新建branch**：`git checkout -b blog-source`，生成新的branch`blog-source`
4、**推送到remote库**：`git add .`，`git commit -m 'blog-source branch'`，`git push -u origin blog-source`

这时候在github网页上就能看到一个新的分支，并且已经把刚才文件夹里的东西存在里面了

## 配置Travis
### 注册并且导入repo
注册直接用github sign-in，这样你的repo应该就会都同步过去了。配置我是自己摸索的，可以参考这篇文章[《手把手教你使用Travis CI自动部署你的Hexo博客到Github上》](https://www.jianshu.com/p/e22c13d85659)
### Travis网页设置
在`github---> settings--->developers settings---->Personal access tokens`里面，生成一个token，选择的权限可以把除了delete repo以外的都勾上，然后把一串token复制出来，备用
回到Travis网页，在repo的setting里，有个Environmental Variables，新建一个`GH_TOKEN`（千万不要用别的名字），把刚刚获取的一串token复制到value里并apply。
现在新版本的travis已经没有`build only if .travis.yml is present`的开关了，但似乎不影响。

我这里用的是access token的方法，也有用ssh密钥的，我觉得比较麻烦，而且我也成功。关键是这个比较简单！！！
### 本地库travis配置
前面一步已经建立好了github库和travis的联系，但需要告诉travis要怎么去build对吧，这步就是做这个。
本地项目库根目录中，新建一个`.travis.yml`的文件，把以下内容放进去：
```
#选择语言和版本
language: node_js
node_js: stable

# 需要的环境和安装
# S: Build Lifecycle
install:
  - npm install

# 如果你用了gulp压缩就开，我没用所以我就注释掉了
#before_script:
 # - npm install -g gulp

#执行的命令，就是之前常用的hexo g
script:
  - hexo g

#hexo g生成的网页都在public文件夹里，以前的hexo d就是把这些内容放到github上
# 现在就手动把这些内容放进去
after_script:
  - cd ./public
  - git init
  # xxxx换成github名字
  - git config user.name "XXXXX"
  # 换成github的登录邮箱
  - git config user.email "XXX@XXXX.com"
  - git add .
  # 这步就是git的备注，随意改引号里的东西
  - git commit -m "Update docs"
  # 告诉Travis环境变量，push到的分支是master
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master
# E: Build LifeCycle

branches:
  only:
  # travis运行的触发条件，我的是blog-source分支有变动就触发
    - blog-source
env:
 global:
 # repo的地址，可以在github中获取
   - GH_REF: github.com/xxxx/xxxxx

```
再用一次git的添加推送方式，把这个`.travis.yml`文件推送到`blog-source`分支。正常的话，travis已经就会开始build第一次了。
## 参考文章
- [《使用 Travis CI 实现 Hexo 博客自动部署》](https://xirikm.net/2019/826-2)
- [《用 Travis CI 自动部署 hexo》](https://segmentfault.com/a/1190000004667156)
- [《手把手教你使用Travis CI自动部署你的Hexo博客到Github上》](https://www.jianshu.com/p/e22c13d85659)
