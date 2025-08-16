---
title: "手搓密码管理器"
subtitle: "一个Rust写的密码管理器 RPaWoMaster"
date: 2025-07-12T16:27:58+08:00
lastmod: 2025-08-16T22:00:00+08:00
draft: false
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
description: ""
license: ""
images: []

tags: ["密码管理器","Rust","CLI"]
categories: ["个人项目","经验分享","工具开发"]

featuredImage: "chris-abney-ugQb6PFLPWI-unsplash.jpg"
featuredImagePreview: "chris-abney-ugQb6PFLPWI-unsplash.jpg"

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: true
rssFullText: false

toc:
  enable: true
  auto: true
  keepStatic: false
code:
  copy: true
  maxShownLines: 50
math:
  enable: false
  # ...
mapbox:
  enable: true
  # ...
share:
  enable: true
  # ...
comment:
  enable: true
  # ...
---

【AI Summary】在央企保密要求下，用 Rust 手搓了一个 CLI 密码管理器 RPaWoMaster，兼顾相对安全与便捷，并记录了从 Python 到可执行文件的全过程。

<!--more-->


##  原点

在央企工作，有保密要求，主要是上级单位有保密要求，所以延续到我们这，就不得不严格处理各类安全问题。
密码是第一个重要的事情。各类网络下载的密码管理器，但凡带有联网功能的，都被一刀切的不让用于各类公司密码的管理。
给的途径就是让研发自己搞一个，供大家集体使用。可不凑巧，公司研发都不爱管这事，毕竟是额外的工作。

而我又十分依赖密码管理器做日常密码管理，所以，不得不自己用python写了一个简单的[密码管理器](https://github.com/SidneyLYZhang/pysswordSz) 。
用来做个人的公司密码管理工具。之后看到单位同事，竟然还在使用加密excel来管理密码的时候，我真的震惊了很久，不安全更甚于以前。
这还是研发中的中流砥柱吗？

所以，想着不行推广一下我这个Python密码管理器。但是我的这个小软件是python写的，可以用pip安装。
但很多普通同事根本不会安装python。这也就是我开始考虑，把这个小软件写成可执行文件，以便适用更广泛的环境。
直接用Python的打包软件，试过很多种，每一个都有问题，要不挂在数据有问题，要不体积过于巨大……

最后才迫使自己还是写个可执行程序吧……

为什么选择Rust？因为Haskell我好久不用了，已经不熟悉了。所以才在Rust、Racket之间选择了一下，感觉Rust写起来更顺一点。
然后就有了这个小程序。

## 密码器安全吗？

对于这个的讨论，其实已经很多了。从我的理解来看，现今的密码管理器都只能保证相对安全。
绝对安全，就意味着绝对难以使用，就像量子密码，要先获得量子纠缠态的一对粒子才可以。

在这个相对的安全中，绝对的隔离才是真实的安全。这也带来了另一个问题，一个密码对应一个安全码，还不能被保存，
这不就是用一个密码对应另一个密码的套娃了吗？所以，绝对的隔离也不现实。使用便利性也是我觉得作为一个密码管理器比较重要的部分。
所以，我才在每一个自己的密码管理器上也都把内部加密和解密的程序外放，作为功能之一，以便可以通过人为方式，
添加更加深入的自定义安全方案，实现自我选择的安全区隔。

另外，严格区分主密码和二步验证密码（一次性密码）的加密区隔，并都严格按照90天进行分别更新，以实现安全上的隔离。
让风险尽可能分化开。至少在一刻全部密码失效的概率尽可能降低。

这样就安全吗？其实也不尽然，等哪一天RSA密码可以轻松破解时，这套方案也就不安全了，这也是密码的悲哀，安全总是暂时的。
不过，就目前来说，你忘了核心密码和OTP的验证密码，在我这个密码管理器里的密码就真的将不再能被你看到了。
所以不要把密码写到任何人可见的地方，也不要忘记你的密码。这很重要。

## RPaWoMaster

我的这个密码管理器叫 `RPaWoMaster` ，就是 Rust PassWord Master的简写。

功能上，这是一个完全的CLI（命令行）程序，使用上需要你有一定使用命令行的能力。当然，这个使用其实并不复杂。有一点和别的cli密码管理器不太一样，
就是 `search` 命令，在 `RPaWoMaster` 里，search既是查照也是展示密码，尽可能减少你反复使用命令行的时间。
另一点不同，就是文件（夹）加密的部分，这部分需要你使用另一个独立密码，一个加密文件一个密码，这个加密是独立于整体密码管理器体系的。
通常我们使用 GPG key 来进行安全加密，如果我脱离这个GPG Key，就无法解密，需要我把Key带入新的需要解密的地方。
但我找了个讨巧的方法，让这个再次被加密后的RSA-Keypair可以随加密文件一起保存，以便脱离核心密码库继续使用。借助这个加密我们可以把密码库进一步
加密隔离，实现更多加密嵌套，也有更多安全性。另一方面，加密文件，只需要安装 `RPaWoMaster` ，就能实现解密。便捷性满分啊。

不过，也有一点，是目前 `RPaWoMaster` [V0.2.0](https://github.com/SidneyLYZhang/rpawomaster/releases/tag/v0.2.0) 版本还未实装的功能，
就是密码粘贴板功能。如果你需要这个功能，可能暂时还需要等待几周，我目前还没时间完成这个功能的优化和构造。

更详细的使用方法，你可以安装之后使用 `RPaWoMaster --help` 来查询，或者看一下 [`RPaWoMaster`](https://github.com/SidneyLYZhang/rpawomaster)
的README内容，当然也可以使用 Zread.ai/DeepWiki.com 的LLM生成式说明文档（ [zread]() | [deepwiki](https://deepwiki.com/SidneyLYZhang/rpawomaster) ）。
Zread.ai的文档较老，是V0.1.8版本的，我还不会更新Zread的文档。所以，你需要更新的文档还是看一下DeepWiki的吧。

最后说一下怎么安装好了，现阶段我推荐安装rust之后使用 `cargo` 直接从源码安装。也可以直接从GitHub下载源文件，你可以从Release里面下载最新版本。
我还在努力把程序推送到scoop和homebrew的库，以便你可以使用这两个包管理器进行安装。但暂时/一两个月内我可能没时间来完成这部分工作。
所以，希望大家稍等一段时间了。

不过，我这个程序更多还是给我的同事使用，所以，我直接拷贝给他们的都是打包好的文件和powershell下的安装工具文件。
只是想使用我这个小软件的各位可能现阶段要稍复杂一些了。

## 最后的一些感慨

这个小工具零零散散写了很久，应该也有一个多月了，从7月到8月15日，目前也只是核心功能完成了，已经是可以使用的状态了。
在这一个多月中，我最希望的，就是自己尽快用上这个新密码管理器。
现在这个夙愿达成，再把粘贴板的能力实装好，就可以做些别的了。

好像最近这几周，我一直在搞各种小工具，除了这个密码管理器，我还在弄一个git插件，用来自动生成message，无论是commit、tag还是changelog，
用这个插件快速解决message的问题，用git就这点让我总是很抓头，现在用 opencommit 这个软件，但npm在windows上也不是很好用，
这就是后续的个人工具计划了。

以上。

希望各位朋友，今天也会是开心的一天。