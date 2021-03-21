---
title: 在同一台电脑上配置多个github的git方法
timestamp: pt=0310042507
date: 2018-03-10 04:25:07
tags:
- github
categories: 金融狗在假装码农
---
由于近期测试在github page上托管个人主页，前一个是用html做的，但没有套用AXEO及其他，纯HTML，尽管界面套用模板挺漂亮，但发布文章实在是非常麻烦，所以便又重新注册此github账号用以单独作为page使用。<!--more-->

所以这里就碰到了一个问题，需要在同一台电脑上配置两个github的ssh，方便git。

在强大互联网上搜索之后，总结出自己的这套。借鉴前人的经验，略有不完善处，我会继续改进。文后会给出参考文章网址。


---
### 假设环境

>账号一：github第一个账号：github；
>账号二：github第二个账号：gitsecond。

### 配置账号一
#### 生成ssh密钥
```
1、安装Git软件
2、在桌面打开Git，进行设置
git config --global user.name "userName"//github的帐号名(也可以自定义)
git config --global user.email "emailAdress"//github的邮箱地址(也可以自定义)
3、生成ssh密钥
ssh-keygen -t rsa -C "githubEmailAdress"//github邮箱地址
```

#### github网站配置
在github上将`id_rsa.pub`文件内容添加上（简略方法如下，还不清楚请百度）

具体方法为:
>在github页面右上角，点击`头像`，然后`Settings`；
>左边栏点击`SSH and GPG keys`,右侧出现`New SSH key`按键，将内容复制进去即可。

### 配置账号二
#### 生产ssh密钥
```
ssh-keygen -t rsa -C "githubEmailAdress"//github邮箱地址
# 设置名称为id_rsa_gitsecond
Enter file in which to save the key (/c/Users/Administrator/.ssh/id_rsa): id_rsa_gitsecond
```
注意此时.ssh文件夹中会产生两个名字为`id_rsa_gitsecond`的文件。
#### 新密钥添加到SSH agent中
```
ssh-agent bash
ssh-add ~/.ssh/id_rsa_gitsecond
```
#### 用记事本创建一个config文件（后缀请在编辑完内容后删除）
```
# 该文件用于配置私钥对应的服务器
# Default github user
Host github
HostName github.com
User git
IdentityFile C:/Users/Administrator/.ssh/id_rsa

# gitsecond配置
Host gitsecond
HostName github.com
User git
IdentityFile C:/Users/Administrator/.ssh/id_rsa_gitsecond
```

#### 测试链接情况
```
$ ssh -T github
Hi xxxxxxx! You've successfully authenticated, but GitHub does not provide shel
l access.

$ ssh -T gitsecond #使用config中的host值
Hi yyyyyyy! You've successfully authenticated, but GitHub does not provide shell
 access.
```

#### clone文件至本地
>若使用gitsecond账号：
```
git clone gitsecond:github_name/your_repository_name.git #第一个为你的github名字，第二个为repo的名字
```
>

#### 日常使用
**注意：日常使用中，若是使用第一个账号，因为是default的，push前无需额外操作。但使用gitsecond账号，push前要将ssh再加载到agent上一次。命令如下：**
```
ssh-agent bash
ssh-add ~/.ssh/id_rsa_gitsecond
```

---
参考链接：
[百度经验贴](https://jingyan.baidu.com/article/219f4bf7f6f8e1de442d3829.html)
[CSDN帖子](http://blog.csdn.net/u012150370/article/details/52694629)
