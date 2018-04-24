---
title: "使用Hugo建个网站"
date: 2018-04-24T22:44:21+08:00
lastmod: 2018-04-24T22:44:21+08:00
draft: false
keywords: ["教程", "Hugo", "Git", "Netlify"]
description: "简明Hugo教程"
tags: ["教程", "入门"]
categories: ["教程","随谈"]
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
mathjax: false
mathjaxEnableSingleDollar: false
---

### 引言

本来我是信誓旦旦的腰使用[Jekyll](https://jekyllrb.com/)做个人网站的，但是无奈我折腾了好久没成果。真的是縗的一塌糊涂啊……或者说是年纪大了？不太适应Jekyll的模式了吗？总之，就是搞了很久没没有成功。尝试来尝试去，最后，选定了 [Hugo](https://gohugo.io/) ，建立了自己的静态网站……不但因为Hugo简单易用，还是因为Hugo在windows上只需要一个执行文件，安装很便捷，很利于在公司摸鱼写Blog。

选择Hugo的过程，也让我一度暗自感叹，技术路线的选择是多么重要！不适合我这个“中老年人”真的不行啊……而那些不知道什么是技术路线的企业也是很悲哀的，因为这在试错上就要经历很久很久的发展啊……叹气……

<!--more-->

### 建站的主要依赖的工具

因为是静态网站，我最先想到的就是Jekyll + Github的方案，最终选定Hugo之后，就打算基于以下几个开源软件（服务）实现：

- [Netlify](https://www.netlify.com/) ：用于提供站点解析，以及https链接
- [Hugo](https://gohugo.io/) ：静态网站的技术底层
- [Github](https://github.com/) ：托管静态网站代码与Blog文件的地方

为什么选择这样的方式？完全因为这三者都是免费的，可以很简单的实现一个稳定的静态网站。

### 基础环境安装

在Fedora上安装简直太简单了，就是：

```bash
$ sudo dnf install hugo
```

而Windows上安装，也比较简单。直接从 [Hugo Releases](https://github.com/gohugoio/hugo/releases) 下载对应版本的Windows程序的zip压缩文件，然后解压道某一个目录文件夹中，然后，添加这个文件夹目录到系统变量的 `PATH` 变量下。

这样就基本安装好了Hugo，相比Hexo、Jekyll的安装，真的要简单很多很多……那是不是安装好了，测试方法也很简单，就是使用Hugo的版本查询：

```bash
$ hugo version
Hugo Static Site Generator V0.39.1 .....
```

### 构建网站

按照官方的最简洁的 [快速入门](https://gohugo.io/getting-started/quick-start/) 的介绍，是使用命令 `hugo new site quckstart` 快速完成建立一个在quickstart文件夹下的新网站。这里使用的命令是：

```bash
hugo new site [path] [flags]
```

对于 `[flags]` 这个变量，一般很少能用到，如果感兴趣可以使用 `hugo new site --help` 进行查询。

说成普通话， `hugo new site [place]` 就是在当前目录里面建立一个文件夹“place”，并且生成网站需要的必要文件。如果place是一个路径，那么就是在这个指定的路径里建立网站。这就更好理解了。

例如，我在当前文件夹下，建立了一个叫做“myblog”的网站文件夹，可以使用 `tree` 命令查看到我们新建的网站结构：

```bash
$ tree myblog
myblog
├── archetypes
│   └── default.md
├── config.toml
├── content
├── data
├── layouts
├── static
└── themes

6 directories, 2 files
```

这就是一个空的网站了。每一个文件、文件夹的作用我就不一一介绍了，可以在官网 [文件结构介绍](https://gohugo.io/getting-started/directory-structure/) 中全部了解到，这里我只说我认为重要的内容：

- `themes` 文件夹就是存放主题的地方。每个主题一个独立的文件夹，可以从官网的指引下载，基本上都需要使用使用 `git clone` 从github上下载。
- `content` 文件夹就是存放blog文章的地方。使用hugo提供的 `hugo new` 工具可以很快速的创建新的blog。
- `config.toml` 是全站的配置文件。

当然其他文件夹文件并非不重要，在刚建立网站初期可以稍微不那么关心其他的内容，找到一个心仪的主题，按照主题的安装提示完成安装就可以，以致对于其他文件夹的效果，在初期我认为并不必要完全了解。

### 使用主题

说到主题，从官网的 [Themes](https://themes.gohugo.io/) 网页上，可以找到支持Hugo的几乎所有主题，然后跟着主题安装指导，一步步很简单就能安装好主题了。

这里我比较推荐 [Even](https://themes.gohugo.io/hugo-theme-even/) 和 [Jane](https://themes.gohugo.io/hugo-theme-jane/) ，我个人也更喜欢Jane主题的样式，感觉很简洁漂亮。

一般的主题都可以使用类似如下的Git方式安装：

```bash
$ git clone https://github.com/olOwOlo/hugo-theme-even.git themes/even
```

然后，Copy某一个 `config.toml` 到网站文件夹的根目录下，然后按照自己的需求修改这个config就基本上完成了安装。Jane主题的 [安装指引](https://github.com/xianmin/hugo-theme-jane/blob/master/README-zh.md) 做的比较全面，不太熟悉的人可以看看这个再转换到不同主题上。

另外，按照Hugo官方的手册，更推荐使用以下命令安装主题：

```bash
$ git submodule add https://github.com/olOwOlo/hugo-theme-even.git themes/even
```

或者：

```bash
$ git clone --depth 1 --recursive https://github.com/olOwOlo/hugo-theme-even.git themes
```

我觉得使用 `git submodule add` 比较好，上传到Github之后就能直接链接到对应的仓库中。

### 开始写文章

前面简单说到了，使用 `hugo new` 命令就可以新建Blog文章了。举例来说：

```bash
$ hugo new posts/first-post.md
```

就是在 `content` 文件夹下，建立一个新的文章，文章保存在文件 `first-post.md` 中。

直接生成的文章中都会带有一个 **Front Matter** ，主题说明中会带有一部分介绍，大部分还是需要仔细读一读官方的 [Front Matter Formats](https://gohugo.io/content-management/front-matter/) 的介绍。

如果你不熟悉Yaml或者Toml，那么我建议你可以先学习一下这些教程：

- [Yaml语法教程](http://ansible-tran.readthedocs.io/en/latest/docs/YAMLSyntax.html) 
- [Toml官方说明](https://github.com/toml-lang/toml) 

虽然我个人很喜欢toml的形式，但是，yaml还是用的人更多，这点不知道在未来会有什么改变。

最后，你写完了文章，要怎么预览呢？或者改变了网站的主题后，要怎么测试呢？这对于Hugo还真的一点也不难：

```bash
$ hugo server -D
```

然后，就能在浏览器中使用 `http://localhost:1313/` 或者 `http://127.0.0.1:1313/` 来浏览效果了。具体的 `hugo server` 用法可见官方网址： [hugo server](https://gohugo.io/commands/hugo_server/) 。

所有的Hugo命令都能在 [Commands | Hugo](https://gohugo.io/commands/) 中找到，在Bash中使用 `--help` 的选项，就能查询所有命令的使用方法了，一般的命令模式：

```bash
$ hugo [command] --help
```

想在Bash中找到所有Commands，直接使用 `hugo --help` 就可以了。

###上传网站(极简Git教程)

测试了网站没问题了，就可以上传Github了。如果你已经是Git的老用户，这就不需要看了。如果你还是新手，可以跟我一起来试试我这个快速Git入门，不过是无理论入门。这也是一个最简单的Github入门使用教程了。有机会再整理一个详细的Git教程好了。

上传网站内容到Github一共分6步：

**第1步** ：安装Git

如果你不确定自己的电脑是否安装了Git或者安装之后不知道安装是否完成，都可以使用git的版本查询完成确认：

```bash
$ git --version
git version 2.16.2.windows.1
```

有如上的类似结果，就说明你的git安装好了。如果没有，则需要重新安装。可以在 [Git官网](https://git-scm.com/) 下载安装，如果不确定怎么安装可以看看 [Git安装指引](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 。

安装了Git，为了保证Git和Github能无密码链接，也就是为后续工作减轻输入密码的工作量，可以使用SSH的方式建立连接，如果你觉得每次输入用户名密码不碍事，也可以忽略这个操作：

```bash
$ ssh-keygen -t rsa -C "email address"
Generating public/private rsa key pair.
......
```

使用这个命令 `ssh-keygen` 一般会要求确认位置和输入密码，这写操作都使用回车就能解决。

然后就是把建立的这个public rsa key放到Github上，先用命令提取key：

```bash
$ cat ~/.ssh/id_rsa.pub
```

复制提取的全部信息，粘贴到 [Github SSH Keys管理页面](https://github.com/settings/keys) 上。起一个好记忆的名字一保存就完成了SSH链接的工作。

**第2步** ：建立仓库

在目标文件夹内，使用Git仓库生成命令就能在本机建立仓库。但这并不完全，还需要在Github上同样建立一个仓库。我们详细来说，如果在本机需要对文件夹 `Zoll` 建立Git仓库的花，需要在这个文件的根目录下，操作：

```bash
$ git init
```

在Github上，选择“New Repository”链接，起一个名字，点击”Create repository“按钮就完成了相关的建仓库操作，Github要求的后续操作，暂时都可以不必关心，例如建立README文件之类，因为在推送文件之后，这些就会自然完成。

**第3步** ：添加文件

在之前 `Git init` 的“Zoll”文件夹下，运行一下命令：

```bash
$ git add .
```

就可以把所有文件都添加到我们新建的本地仓库中了。这个操作很简单，也就完成了添加文件的操作。

**第4步** ：完成更新提交

添加完文件并不是完成，还需要为刚刚添加的文件建立一个说明，也就是完成对更新的提交，因为每次我们改变文件都是对文件的一次更新，这一步就是确认这个更新。

```bash
$ git commit -m "text"
```

其中的“text”就是这次更新的提示文字，可以根据需要自己填写。

**第5步** ：连接远程与本机

我们分别在本机和Github（远程）都建了仓库，现在我们把他们关联起来：

```bash
$ git remote add origin https://github.com/[user]/[zoll].git
```

这样就可以把本机文件推送到Github上了。其中的链接，就是在Github新建的仓库的Github的链接，替换为你的，回车命令之后本地与远程的链接就完成了。

**第6步** ：推送文件

最后的推送也比较简单：

```bash
$ git push -u origin master
```

完成推送，Github上就能看到我们上传的文件了。

在第一次完成上述步骤之后，以后的网站更新就只需要完成第3、4、6步所涉及的步骤。

### 使用Netlify发布网站

要把网站发布到Netlify也并不复杂，为什么不使用Gihub本身提供的 [Pages](https://pages.github.com/) 功能呢？一方面无法完美解决HTTPS的连接问题，另一方面，Github本身链接速度也不够快，最大的问题是仓库名称限制很大，让人觉得很麻烦。

把Github的网站仓库连接到Netlify，并使用Netlify加载网站，就要简单容易很多，这也是我选这个的主要原因之一。

在Netlify上发布Hugo网站一般可以分4步：

**第1步** ：添加Netlify配置文件

需要在网站根目录下，创建一个 `netlify.toml` 的文件，文件内容根据官方说明，需要有以下内容：

```toml
[build]
publish = "public"
command = "hugo"

[context.production.environment]
HUGO_VERSION = "0.38.1"
HUGO_ENV = "production"
HUGO_ENABLEGITINFO = "true"

[context.split1]
command = "hugo --enableGitInfo"

[context.split1.environment]
HUGO_VERSION = "0.38.1"
HUGO_ENV = "production"

[context.deploy-preview]
command = "hugo -b $DEPLOY_PRIME_URL"

[context.deploy-preview.environment]
HUGO_VERSION = "0.38.1"

[context.branch-deploy]
command = "hugo -b $DEPLOY_PRIME_URL"

[context.branch-deploy.environment]
HUGO_VERSION = "0.38.1"

[context.next.environment]
HUGO_ENABLEGITINFO = "true"
```

保存文件之后，就可以上传到Github，初步的配置就算完成了。

**第2步** ：连接Github与Netlify

注册Netlify之后，就可以把保存网站的Github仓库连接到Netlify了。这一步出奇的简单，如果网站经过本地测试，那么，在netlify上就可以顺利完成初步发布了，完成了发布会给一个随机网址，直接打开这个网址就能看到你的网站了。

**第3步** ：使用自己的域名

大部分情况还是使用自己的域名，会更便于推广自己的Blog，也便于给同学查看。点击 `Add Custom Domain` 按钮，输入你的域名，然后，把Netlify的随即地址在你的DNS管理器上添加A记录，就可以了。

**第4步** ：启用 `https` 链接

设置好私人域名之后，就可以使用SSL/TLS certificate中的设置开启https了，或者你也可以上传自己的SSL证书。这都几乎是傻瓜式的操作。最后，不要忘记打开Force HTTPS的选项，保证网站持续在https的环境中。

到这里，你就已经拥有了自己的静态Blog网站了。似乎很复杂，但一步步尝试，还是很简单的。更详细的Hugo在Netlify的操作，可以看Hugo官方的指引说明： [Host on Netlify](https://gohugo.io/hosting-and-deployment/hosting-on-netlify/) 。

### 后话

每次更换网站，都很纠结，有一个好看的、漂亮的个人网站真的又容易又不容易。希望这是未来很长一段时间都不需要我再费精力的一个Blog，也希望我能写的更多，做的更多，变得更有趣一些吧。

