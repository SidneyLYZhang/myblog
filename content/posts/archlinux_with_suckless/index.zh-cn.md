---
title: "Arch Linux与Suckless结合"
date: 2020-09-24T10:35:21+08:00
lastmod: 2020-09-24T17:35:21+08:00
draft: false
keywords: ["arch", "linux", "Learning", "install", "dwm", "st", "dmenu"]
description: "让arch linux用上suckless的软件..."
tags: ["学习", "笔记"]
categories: ["笔记","学习","archlinux"]
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

### 动机

[Suckless.org](http://Suckless.org) 提供了一个以前我不是很敢尝试的产品。但是，在各种压力下，这真的是不得不用一下（比如2G内存台式机，1.8G的双核CPU……而且我滚动更新到系统崩溃……）。

之前就说，人生苦短，要用ArchLinux……所以，就算重装也还是Arch吧……这次一定不要滚动更新到挂机就好……当然进一步降低系统需求也很必要，所以就用资源损耗最小的dwm吧。

我在用户的home文件夹中，增加了一个apps文件夹(`mkdir ~/apps`)，用来集中管理这些非yay和非pacman来源的软件。

这样就可以开始下载安装Suckless的软件了。

对于Suckless的软件基本步骤都是一样的，而且我觉得并不是很建议下载打包代码进行如下操作，而是使用git克隆代码内容，也可以在需要的时候更快的拉取新版本的代码进行后续处理：

1. 克隆git仓库（`git clone repository`）
2. 打补丁（`patch *.diff`）
3. 处理补丁冲突（`vim -O * *.rej`）
4. 编译安装（`sudo make clean install`）

### DWM

对于[DWM](https://dwm.suckless.org/)，克隆的仓库就是这样：

```bash
$ git clone https://git.suckless.org/dwm
```

然后就要选择需要的补丁了。DWM的补丁真的不少：[patches](https://dwm.suckless.org/patches/)。我对于

******

#### 附录1：我的 Arch Linux 软件列表

**基本软件**

- dwm ：窗口管理器。很轻量很轻量，真的很不错！
- dmenu ：命令式菜单，轻量好用。
- sddm ：登录管理器，适应性很广，而且轻量级好用的很啊~
- st ：很棒的终端模拟器。可以很好的根据自己的需要调整，并最终实现适合自己的那个样子。
- yay ：AUR软件的安装管理器，很好用。
- python ：必要的工作工具。
- zsh & oh-my-zsh ：终端及其外观。我喜欢zsh，远大于其他。
- speedcruch : 超强的计算器。

**编辑软件**

- wps-office ：还算好用，比其他的开源office要漂亮很多，对Microsoft的文件支持好很多啊。
- vim ：文本与代码的编辑器。加上一些我自己认为不错的配置之后，还是很好用的。记得安装gvim，以适应更多功能。

**文档工具**

- texlive ：latex编译器。
- pandoc ：转换器，转换各种文档格式，很重要的说。

**输入法**

- fcitx5 ：比旧版本的fcitx好用太多了！

**截图软件**

- scrot ：截图软件，在dwm里面还算好用。
- maim ：功能更多样的命令行截图软件，https://github.com/naelstrof/maim 。

**监控软件**

- bashtop
- conky

**浏览器**

- firefox ：
- surf ：

**文件管理**

- ranger ：
- git :
- lazygit :

**小玩意**

- neofetch
- lolcat
- figlet
- translate-shell
- tldr
- tree
- p7zip
- feh

#### 附录2：可能需要的参考文件

