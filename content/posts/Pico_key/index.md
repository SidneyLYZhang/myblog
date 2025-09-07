---
title: "做一把 Pico Key"
subtitle: "个人开源硬件密钥 Personal Secure Key"
date: 2025-09-07T15:52:43+08:00
lastmod: 2025-09-07T15:52:43+08:00
draft: true
author: ""
authorLink: ""
description: ""
license: ""
images: []

tags: []
categories: []

featuredImage: ""
featuredImagePreview: ""

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

<!--more-->

开源硬件个人密钥，还是很不错的，差不多60块人民币，就能解决很多问题啦~
对我来说，快速登陆公司电脑，就是一个很棒的事情了。（毕竟公司密码要求三个月一换，还要达到一定的密码安全性等级，那可是很难记忆的东西呀……）

另外，在公司也能更好的使用隐私窗口来登陆个人网站，而不使用个人的密码管理器，也能进一步让自己的信息相对公司环境完成隔离，
个人隐私安全性也增加很多，这也是我很在意的地方。
而且这也符合我随时跑路风的办公风格很相近。😂

现在可以选择的硬件密钥，其实不多，开源选择也不多，好用的更是少了。主要是一下这些：
- 国产的[CanoKey](https://docs.canokeys.org/)，
- Google的[OpenSK](https://github.com/google/OpenSK)，
- 我选择的[Pico Key](https://www.picokeys.com/)，
- 原版[Yubico Yubikey](https://www.yubico.com/)。

CanoKey给我的感觉是现在转向直接制作实体Key出售，对于开源部分的维护很粗糙，尤其是开源部分的安全性还是有很多问题的，一个CanoKey大概要179，想再少花些钱，就Pass了这个方案。OpenSK我感觉更新较慢，2021年之后全靠社区维护，而且再没有固定版本提供，需要自己用cargo build最新代码，我虽然也使用rust工作，但这也是一种很麻烦的事情……Yubico呢，就是贵，少则300多，多则上千，暂时不想这么花钱，便宜很重要的。而我选择PicoKey，就是很简单、便宜、好维护，功能也不差，我已经很满足了。

### 制作流程

整体制作，我就是按照[官方的说明](https://www.picokeys.com/pico-fido/)，以及Muse大佬的教程（[L站帖子](https://linux.do/t/topic/485658)及[MUSE Blog](https://blog.pepaper.org/2025/03/%e8%87%aa%e5%88%b6%e5%85%bc%e5%ae%b9-yubikey-%e7%9a%84-fido2-%e7%a1%ac%e4%bb%b6%e5%ae%89%e5%85%a8%e5%af%86%e9%92%a5/)）对照进行的。

你如果需要入门指引，Muse大佬的教程应该更为简洁，清晰。我这里的制作流程，会补充一点我个人的感受，以及后续如何使用之类的信息。更多还是帮我自己做一些记忆和信息整理。

#### Step 1 - 基础准备

{{< admonition type=info title="RP2350开发板" open=true >}}
这里需要注意不要选择老的RP2040的开发板，PicoKey[官方推荐RP2350](https://github.com/polhenarejos/pico-fido?tab=readme-ov-file#security-considerations)也是因为RP2350可以实现更好的安全性，极大降低密钥使用风险，并保证安全可靠。
{{< /admonition >}}

我买的就是微雪的RP2350-ONE开发板（Waveshare RP2350-One 开发板），某宝价格28~30人民币，真的很良心！

另外一些增加使用感的配套产品：3D打印壳或者热缩管。