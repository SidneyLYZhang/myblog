---
title: "用上MongoDB随便做点啥"
date: 2020-02-26T15:36:21+08:00
lastmod: 2020-02-26T17:32:21+08:00
draft: false
keywords: ["MongoDB", "Python", "python"]
description: "用上MongoDB，随便做点啥，不要浪费免费的服务器。"
tags: ["学习", "笔记"]
categories: ["笔记","学习", "MongoDB"]
author: "Sidney Zhang"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: true
autoCollapseToc: false
postMetaInFooter: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: true
reward: false
mathjax: true
mathjaxEnableSingleDollar: true
---

### 0. Forward

这一部分俗称废话。以下：

我之前搞了一个保存密码Python小玩意，因为我自己一直并不满意，所以也就没有好好使用，只是简单保存一些必要的密码内容。

这两天偶然看到， @JiChen 大哥在微信群里问MongoDB的一些问题，把我的兴趣勾引起来了，因为隐约记得MongoDB的数据很接近Json，而我写的那个小玩意，也是用JSON保存的。
或许可以增加一些功能，用MongoDB来保存。还可以建立新的小工具，比如记录自己写的Alias的小工具，便于以后查找和使用。

总之，这就是我觉得可以用上MongoDB的一些事情了吧？无论如何先用上，别浪费MongoDB的免费资源。

*后面一切从简，我只记录我怎么干的，以及最终的一些代码说明，其他入门内容均不包含，最后看心情会不会增补参考文献吧。*

### 1. 安装MongoDB

我在WIndows WSL中安装的MongoDB，所以直接使用MongoDB的 [官方安装指引](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/) ，一路顺滑完成，基本安装命令如下：

```bash
$ sudo apt-get install gnupg
$ wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add -
$ echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.2.list
$ sudo apt-get update
$ sudo apt-get install -y mongodb-org
```

安装 `gnupg` 完全是为了减少后续安装出错的概率，如果可以确认已经安装了这个包，那么，从第二条命令开始即可。

这主要是为了本地获取MongoDB Shell，以便初期测试、后期检查数据使用。当然，既然要使用MongoDB官方免费服务器，那么注册一个账号也是必须的。这也是一种安装——远程服务安装。

建立账户，比较简单，邮箱、密码、用户名还有各种验证码。进入账号后，就可以开始 “Create New Cluster” 了，即开始建立你的免费MongoDB服务了。使用M0的免费服务就好，最后给Cluster起个名字就好了。

等待几分钟让服务器建立好，你就可以开始测试你的服务了。

### 2. 建立MongoDB的连接

使用 “CONNECT” 选项，建立连接的配置。第一步就是设置网络访问的安全属性，只能使用IP白名单方式访问你的数据库。如果使用 `0.0.0.0/0` 的网址，那么就相当于关闭了白名单功能。

然后，还需要选择你的登陆方式，我先选择MongoDB Shell做测试。云MongoDB的网址一般不会改变，记录下来，以后使用Python待见工具是还是继续需要的。

还需要创建用于登录、管理服务器的用户名和密码，这样，完成之后，建立连接的准备也就完成了。

在WSL中，打入MongoDB提供的Shell连接：

```zsh
$ mongo "mongodb+srv://sidplace-ru5jo.azure.mongodb.net/test"  --username sidney_zhang
MongoDB shell version v4.2.3
Enter password:
```

输入密码后，就进入了服务了。连接的验证也就至此完成。

### 3. 预期使用的想法与小工具的设计

我现在比较需要可以记录我的工作密码的小工具，以及可以记录查询Alias的工具。这应该是我可能常用的小工具吧？

这个小工具就先起名叫做： **HANPI** 吧。

需要至少包含以下主要命令：

- init：工具的初始化命令
- config：初始化后再配置工具的命令
- alias：记录或查找Alias的命令
- passwords：记录或者查找密码的命令

基本的文件结构，也按照能在pypi发布的方式构建吧，我也想让更多人用上hanpi嘛~哈哈哈~~要有趣，就尽可能有趣一些吧。

```powershell
> tree /F .
/HANPI
│  LICENSE
│  README.md
│  setup.cfg
│  setup.py
│
└─hanpi
        hanpi.py
        __init__.py
```

先这样，后面会改成什么样再说呗，总不要太苛刻了，发展才是一切嘛。古云：“易，穷则变，变则通，通则久。”^[1]

### 4. 使用PyMongo创建基础数据

用到MongoDB的地方主要有四个：创建数据库、插入新数据、删除某条数据，以及查找。

### 5. 

******

### REFERENCE

1. []() , 

### NOTE

^[1] : 出自 《易经·系辞 下》 ，可见 http://www.guoxue.com/book/zhouyi/0004.htm ；