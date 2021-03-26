---
title: 语雀与Hexo互通且备份至Github
urlname: uq5wyk
date: '2021-03-25 22:44:05 +0800'
tags:
  - Hexo
  - Travis
categories:
  - 金融狗在假装码农
---

![3.png](https://cdn.nlark.com/yuque/0/2021/png/682563/1616750856496-d2431950-20cc-4176-8670-3fb60debec75.png#align=left&display=inline&height=735&margin=%5Bobject%20Object%5D&name=3.png&originHeight=735&originWidth=1184&size=42996&status=done&style=none&width=1184)
接着上一篇[《Travis 部署 Hexo 记录》](https://blog.kmore.ml/2021/pt=03211800/)继续写。。。

<!-- more --> 

之前写到，异地写作只要写完之后 git push 一下，就会有 Travis 自动部署到 github pages 中。现在再来进一步进阶一下，在语雀写，并且自动部署，实现语雀与 Hexo 的互通。

### 前置条件

准备好语雀账号和腾讯云账号（阿里云也可以，但我用的是腾讯云，腾讯云提供的免费数量足够正常使用了，不用担心付费问题）

### 腾讯云--云函数

如果在启用`云函数`中提示你开通什么的话，都选开通就可以。

#### 新建云函数

点开后选择自定义创建。地域我选的`广州`，据说只有广州和北京的能用，这个可以自己试试吧。运行环境选`php7`，函数名随意啦。
![新建云函数](https://z3.ax1x.com/2021/03/25/6XfT2j.png)

#### 建立函数

把下面这段代码复制到代码框里。

```
<?php
function main_handler($event, $context) {
    // 解析语雀post的数据
    $update_title = '';
    if($event->body){
        $yuque_data= json_decode($event->body);
        $update_title .= $yuque_data->data->title;
    }
    // 获取travis-ci的仓库信息
    // curl -X GET https://api.travis-ci.com/owner/ShimingLuo/repos -H 'Authorization: token XXXXXXXXXX' -H 'Travis-API-Version: 3'
    // 使用 repository.id、repository.slug
    // default params
    $repos = 'xxxxxx';  // 你的Travis仓库id 或 slug
    $token = 'xxxxxx'; // 你的登录Travis token
    $message = date("Y/m/d").':yuque update:'.$update_title;
    $branch = 'blog-source'; //需要读取的branch
    // post params
    $queryString = $event->queryString;
    $q_token = $queryString->token ? $queryString->token : $token;
    $q_repos = $queryString->repos ? $queryString->repos : $repos;
    $q_message = $queryString->message ? $queryString->message : $message;
    $q_branch = $queryString->branch ? $queryString->branch : 'blog-source'; //需要读取的branch
    //request travis ci
    $res_info = triggerTravisCI($q_repos, $q_token, $q_message, $q_branch);

    $res_code = 0;
    $res_message = '未知';
    if($res_info['http_code']){
        $res_code = $res_info['http_code'];
        switch($res_info['http_code']){
            case 200:
            case 202:
                $res_message = 'success';
            break;
            default:
                $res_message = 'faild';
            break;
        }
    }
    $res = array(
        'status'=>$res_code,
        'message'=>$res_message
    );
    return $res;
}

/*
* @description  travis api , trigger a build
* @param $repos string 仓库ID、slug
* @param $token string 登录验证token
* @param $message string 触发信息
* @param $branch string 分支
* @return $info array 回包信息
*/
function triggerTravisCI ($repos, $token, $message='yuque update', $branch='blog-source') {
    //初始化
    $curl = curl_init();
    //设置抓取的url
    curl_setopt($curl, CURLOPT_URL, 'https://api.travis-ci.com/repo/'.$repos.'/requests');
    //设置获取的信息以文件流的形式返回，而不是直接输出。
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
    //设置post方式提交
    curl_setopt($curl, CURLOPT_CUSTOMREQUEST, "POST");
    //设置post数据
    $post_data = json_encode(array(
        "request"=> array(
            "message"=>$message,
            "branch"=>$branch
        )
    ));
    $header = array(
      'Content-Type: application/json',
      'Travis-API-Version: 3',
      'Authorization:token '.$token,
      'Content-Length:' . strlen($post_data)
    );
    curl_setopt($curl, CURLOPT_HTTPHEADER, $header);
    curl_setopt($curl, CURLOPT_POSTFIELDS, $post_data);
    //执行命令
    $data = curl_exec($curl);
    $info = curl_getinfo($curl);
    //关闭URL请求
    curl_close($curl);
    return $info;
}
?>
```

##### 特别注意：

- 在 Travis 中获取 token 以后，Linux 中运行

```
curl -X GET https://api.travis-ci.com/owner/ShimingLuo/repos -H 'Authorization: token XXXXXXXXXX' -H 'Travis-API-Version: 3'
```

会返回一个 json 格式的内容，找到你要运行的 repo。理论上，repo id 和 slug 都行，但我只有 id 成功了，我建议就用 id 吧。

- 在上面命令的**第 16、22、55 行**的 branch 要改成需要操作的 branch。例如我的 gh page 在 master branch，但我的源码是在`blog-source`里，这里我都改成了`blog-source`。

#### 触发管理

```
API服务名：SCF_API_SERVICE
serviceId：service-5i0acgmi
apiId：api-kysretyg
请求方法：ANY
发布环境：发布
鉴权方式：免鉴权
启用集成响应：已启用
启用Base64编码：未启用
支持CORS：否
后端超时：15s
访问路径：https://service-5i0acgmi-1256168505.gz.apigw.tencentcs.com/release/xxxxxxx
```

![触发管理](https://z3.ax1x.com/2021/03/25/6Xf7xs.png)
复制最后一行的访问路径，备用。
至此，腾讯云这边就解决了。

### 语雀端

#### 新建知识库

新建一个知识库，类型就选博客吧，这样比较简单，可见范围随意，互联网可见也没啥问题，反正都是写了给大家看的。

#### 查看/变更知识库访问路径

这步比较重要。在新的知识库里，选择设置，能够看到路径的框，开头应该是`yuque.com`，后面跟着第一个，是你的 login（这个可以在帐户中设置），第二个是可以编辑状态的，是你的 repo 名，记录一下，后面会用到

#### 获取 token

进入到语雀的账号设置中，有一个 token 的标签，名字随意，至少勾选`读取你的知识库`和`读取你的文档`，然后把一长串 token 保存备份，也要用到。
![获取token](https://z3.ax1x.com/2021/03/25/6XhpRJ.png)

#### 配置 webhook

进到刚才新建的博客 repo 中，点设置---开发者。
我们来添加一个 Web Hook：

- 把前面在腾讯云里复制出来的访问路径地址，复制到 URL 栏里
- 在下面勾选中，我是勾选了**发布文档、更新文档、删除文档。**

![新建WebHook](https://z3.ax1x.com/2021/03/25/6Xh9z9.png)
添加保存即可！

### Hexo 配置

#### 增加 dependency

要使用语雀到 hexo，我们需要加 2 个 package。
分别是`[yuque-hexo] `和`[cross-env](https://github.com/kentcdodds/cross-env)`
前者是为了读取语雀的文档，后者是为了在 Travis 中可以读取放在环境变量里的语雀 TOKEN
方法是在 hexo 根目录的`package.json`中的 dependencies 最后加入两行
可以参考我的`[package.json](https://raw.githubusercontent.com/kentsx/blog/blog-source/package.json)`

```
"cross-env": "^7.0.3",
"yuque-hexo": "^1.7.0"
```

#### 配置 yuque-hexo

还是在`package.json`中，在最前面加上如下内容：

```

{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "clean": "hexo clean",
    "clean:yuque": "yuque-hexo clean",
    "deploy": "npm run sync && hexo clean && hexo g"
  },
  "hexo": {
    "version": "5.3.0"
  },
  "yuqueConfig": {
    "postPath": "source/_posts/yuque",
    "cachePath": "yuque.json",
    "mdNameFormat": "title",
    "adapter": "hexo",
    "concurrency": 5,
    "baseUrl": "https://www.yuque.com/api/v2",
    "login": "xxxx",
    "repo": "xxxx",
    "onlyPublished": false
  },
```

第 21 行和 22 行的内容，替换为前面在语雀里获取的。

#### 配置.travis.yml 文件

上一篇，我们已经建立了这个文件，但只能完成本地到 travis 的自动部署，现在我们对这个来进行适当调整。以下就是我纠结了时间最长的部分了，经历了无法获取语雀文档内容、无法备份到 github 等等。
废话不说，先上代码：

```
language: node_js
node_js: stable



branches:
  only:
  - blog-source

# S: Build Lifecycle
before_instal:
- export TZ='Asia/Shanghai' # 更改时区

install:
  - npm install

# 缓存文件
cache:
  directories:
    - node_modules

script:
  # - npm run deploy
  - yuque-hexo clean
  - cross-env YUQUE_TOKEN=$YUQUE_TOKEN yuque-hexo sync
  #备份到blog-source branch
  - git init
  - git config user.name "XXXXX"
  - git config user.email "xxxx@xxxx"
  - git checkout blog-source
  - git add .
  - git commit -m " -#$TRAVIS_BUILD_NUMBER-TravisCI - `date +"%Y-%m-%d %H:%M"`"
  # - git status
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" blog-source:blog-source
  - hexo g

after_script:
#生成新的page到master
  - cd ./public
  - git init
  - git config user.name "XXXXX"
  - git config user.email "xxxx@xxxx"
  - git add .
  - git commit -m " -#$TRAVIS_BUILD_NUMBER-TravisCI - `date +"%Y-%m-%d %H:%M"`"
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master
# E: Build LifeCycle

# branches:
#   only:
#     - blog-source
env:
 global:
   - GH_REF: github.com/kentsx/blog.git
```

主要改动就是在`script`一节里，我加入了语雀的部分和备份到 github 的部分。变动如下：

```
  - yuque-hexo clean # 每次拉取语雀文档前，先把原来的清空
  - cross-env YUQUE_TOKEN=$YUQUE_TOKEN yuque-hexo sync
  # 后面需要在Travis中配置YUQUE_TOKEN变量，这里调用了cross-env

  #备份到blog-source branch
  - git init
  - git config user.name "XXXXX" #github登录名
  - git config user.email "xxxx@xxxx" # github邮箱
  - git checkout blog-source  ##这步很关键，不然会出现detached HEAD情况，branch选择自己hexo源码branch
  - git add .
  - git commit -m " -#$TRAVIS_BUILD_NUMBER-TravisCI - `date +"%Y-%m-%d %H:%M"`"
  # 这样写，就能知道是那次travis部署push过来的了。
  # - git status
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" blog-source:blog-source
```

### Travis 端

在之前的基础上，添加一个环境变量，名称为`YUQUE_TOKEN`，value 就是刚才语雀端里那串 token

### 参考文档

- [《Hexo 博客终极玩法：云端写作，自动部署》](https://segmentfault.com/a/1190000017797561)
