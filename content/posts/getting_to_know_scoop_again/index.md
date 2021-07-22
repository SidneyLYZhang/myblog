---
title: "重新认识SCOOP"
date: 2021-07-15T16:59:21+08:00
lastmod: 2021-07-15T16:59:21+08:00
draft: false
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
description: "随着时间的进展，scoop越来越有用了，gh、git之类传统安装方式会很难更新，使用scoop就容易了很多。"
resources:
- name: "featured-image"
  src: "featured-image.png"

tags: ["scoop", "windows", "installer", "CLI"]
categories: ["笔记"]

lightgallery: true

toc:
  auto: true
---

<!--more-->

### 再次认识一下scoop

最近重新整理电脑，发现很多东西的版本已经低了……看着一个个都在提醒更新新版软件，我也是十分的难受……不更新就难受……很难受那种……

幸好，Rust可以自己更新自己，还算是良心。

重新考虑怎么管理Windows的这些必要软件呢？使用choco安装管理，怎么样？但是现在choco安装自己就常常报错……我有点不太想用……那么剩下的，的就只有下载更新缓慢的scoop了……

已开始使用Scoop觉得很不爽，因为下载缓慢，现在虽然也是这样，但用上番羽土啬之后，这也就不算什么太大的问题，当然也有其他解决方案，对于一个懒人，梯子神么的，总也是个轻松的方法。

windows的安装现在很多时候，都是软件自带更新，或者基于windows store进行更新。可对于那些没有这些更新途径的软件来说，更新软件就有些难了。尤其是，github cli不更新，每次使用都会提醒有新版本……看着我那个难受啊……

尤其是那些更新频繁的软件，在windows上管理起来，非常愁人。

### 再说安装与使用

按照官方的说法，需要确定一点当前powershell的主版本要大于5。或者说，需要PowerShell版本不能太低了。我记得windows10自带的是Powershell5.x版本的，所以理论上，不追求PowerShell高版本的特性，不需要再安装其他版本。

可以通过这个命令简单看一下，你的powershell主版本号：

```powershell
> $psversiontable.psversion.major
7
```

然后很重要的一点，就是让powershell可以运行脚本，有更多权限，我建议最好在管理员权限下的Powershell里运行以下代码，增强必要权限：

```powershell
> set-executionpolicy remotesigned -scope currentuser
```

安装其实也并不复杂，同样在管理员权限下：

```powershell
> Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh')
```

对于我来说，其实是更新scoop。这个就更简单了。只需要 `scoop update` 一下就好了。只是在更新源信息的时候会慢很多，即使开启梯子，也并没有达到一个令我满意的更新速度。

### 用scoop安装的那些软件

目前呢，我使用scoop安装管理的主要是这几个常用的软件：

1. 7zip: `scoop install 7zip` : 安心的压缩解压缩软件，比winrar好太多。
2. github cli: `scoop install gh` : github的命令行软件，真的很不错，好用啊……
3. git: `scoop install git` : git的本体，也是版本管理必须的东东。
4. julia: `scoop install julia` : Julia语言的安装包……
5. gnupg: `scoop install gnupg` : 密码管理的必要产品嘛……为了git验证，签名自己的共享文件什么，很有用的。
6. aria2: `scoop install aria2` : 多线程下载神器啊……

遗憾的是，本来还想用scoop安装haskell的，但是只有8.6.5版本的Haskell，有点版本落后了。现在都8.10.x了……所以scoop还是不能满足我全部的安装管理需求，但解决了90%+的需求，就这个程度来说，我还是很满意的。

安装完成，可以使用 `scoop list` 看看安装了些什么。更新就是 `scoop update packagename` 。

### 参考内容

1. [scoop wiki](https://github.com/lukesampson/scoop/wiki)
2. [windows .Net](https://dotnet.microsoft.com/download)
3. [scoop官网](https://scoop.sh/)
4. [scoop repo](https://github.com/lukesampson/scoop)
