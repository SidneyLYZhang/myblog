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
对我来说，最希望实现的就是快速登陆公司电脑，这就已经足够棒了。（毕竟公司密码要求三个月一换，还要达到一定的密码安全性等级，那可是很难记忆的东西呀……
但结果是，不能用……公司运维严厉拒绝了我这个简单的请求。甚是无奈啊……）

另外，在公司也能更好的使用隐私窗口来登陆个人网站，而不使用个人的密码管理器，也能进一步让自己的信息相对公司环境完成隔离，
个人隐私安全性也增加很多，这也是我很在意的地方。
而且这也符合我随时跑路风的办公风格很相近。😂

现在可以选择的硬件密钥，其实不多，开源选择也不多，好用的更是少了。主要是一下这些：
- 国产的[CanoKey](https://docs.canokeys.org/)，
- Google的[OpenSK](https://github.com/google/OpenSK)，
- 我选择的[Pico Key](https://www.picokeys.com/)，
- 原版[Yubico Yubikey](https://www.yubico.com/)。

CanoKey给我的感觉是现在转向直接制作实体Key出售，对于开源部分的维护很粗糙，尤其是开源部分的安全性还是有很多问题的，一个CanoKey大概要179，主要是想尽可能少花些钱，就Pass了这个方案。OpenSK给我的感觉是更新较慢，2021年之后全靠社区维护，而且再没有固定版本提供，需要自己用cargo build最新代码，我虽然也使用rust工作，但build终归也是一种很麻烦的事情……Yubico呢，就是贵，少则300多，多则上千，暂时不想这么花钱，便宜是现在第一要务。而我选择PicoKey，就是因为它很简单、便宜、好维护，功能也不差，满足了我几乎所有要求。

### 制作流程

整体制作，我就是按照[官方的说明](https://www.picokeys.com/pico-fido/)，以及Muse大佬的教程（[L站帖子](https://linux.do/t/topic/485658)及[MUSE Blog](https://blog.pepaper.org/2025/03/%e8%87%aa%e5%88%b6%e5%85%bc%e5%ae%b9-yubikey-%e7%9a%84-fido2-%e7%a1%ac%e4%bb%b6%e5%ae%89%e5%85%a8%e5%af%86%e9%92%a5/)）对照进行的。

你如果需要快速的入门指引，Muse大佬的教程应该更为简洁，清晰。我这里的制作流程，会补充一点我个人的安装记录，以及后续如何使用之类的信息。更多还是帮我自己做一些记忆和梳理。

#### Step 1 - 基础准备

我买的就是微雪的RP2350-ONE开发板（Waveshare RP2350-One 开发板），某宝价格28~30人民币，很良心的价格了！

另外一些增加使用感的配套产品：3D打印壳或者热缩管。

{{< admonition type=info title="RP2350开发板" open=true >}}
这里需要注意不要选择老的RP2040的开发板，PicoKey[官方推荐使用RP2350](https://github.com/polhenarejos/pico-fido?tab=readme-ov-file#security-considerations)也是因为RP2350可以实现更好的安全性，极大降低密钥使用风险，并保证安全可靠。
{{< /admonition >}}

{{< admonition type=info title="热缩管" open=false >}}
微雪RP2350-ONE的尺寸：

![RP2350-One尺寸](RP2350-One-details-size.jpg)

所以理论上，你购买直径18mm的热缩管就可以，2米大概5~7元人民币。如果你没有热风枪，也需要买一把，价格一般50~100元的就可以了吧。
{{< /admonition >}}

{{< admonition type=info title="3D打印外壳" open=false >}}
可选的外壳也有很多种，我现在找到开源外壳文件的主要是以下三种：
- [@PatvdLeer](https://www.printables.com/@PatvdLeer) 做的 [3D打印外壳](https://www.printables.com/model/1129764-waveshare-rp2040-one-and-rp2350-one-case)
- [@taoengine](https://makerworld.com.cn/zh/@taoengine) 做的 [外壳](https://makerworld.com.cn/zh/models/1514456-rp2350-rp2040-one-usbbao-hu-ke-yong-yu-pico-keys)
- [@wtser](https://makerworld.com.cn/zh/@wtser) 做了优化的 [外壳](https://makerworld.com.cn/zh/models/1576841-waveshare-rp2350-one-usb-wai-ke#profileId-1722358)

选择自己喜欢的，然后找一家3D打印店，帮忙打印出来基于可以了。我实在某宝找的店。
{{< /admonition >}}

#### Step 2 - 下载固件

官网提供了三种固件：
1. Pico HSM
2. Pico Fido
3. Pico OpenPGP

分别提供加密解密和密钥的功能，Pico OpenPGP提供了符合OpenGPG标准的加解密密钥。
对于我只需要硬件密钥功能的情况，单独下载Pico Fido就可以了。如果还需要用作数字认证、加密解密
这一类密码学应用（比如git commit的签名等），就可以增加HSM和OpenPGP的固件。
基本的安装方式，是一致的。

可以根据官方的 [页面下载指引](https://www.picokeys.com/getting-started/) ，完成固件下载，
也可以根据自己的需求，直接从官方的Github仓库（[HSM](https://github.com/polhenarejos/pico-hsm/releases)、[Fido](https://github.com/polhenarejos/pico-fido/releases)、[OpenPGP](https://github.com/polhenarejos/pico-openpgp/releases)）直接下载所需固件。

总之，从官方指引页面下载其实更好找对应硬件一些。

如果你买的和我一样是微雪的产品，那么就从Vendor中找到“Waveshare”，从Model中找到“RP2350-One”，点击下载就可以了。

但是，PicoKey的一个问题就是三种固件不能同时使用……所以你先选择自己的使用需求，然后下载对应固件，
如果你已经安装过一个固件了，在切换不同功能时，需要使用[数据清理固件](https://github.com/polhenarejos/pico-nuke/releases)，
清理完已有数据，再刷入新固件；全新固件，直接刷你的目标固件就可以了。

简单总结一下：

1. 选择硬件密钥的使用目标（HSM、Fido、OpenPGP）
2. 下载指定固件
3. 如果硬件需要切换使用能力，则需要先安装Pico Nuke固件
4. 硬件初始化之后，或对于全新硬件，刷入目标固件

#### Step 3 - 安装


