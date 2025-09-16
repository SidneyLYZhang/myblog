---
title: "做一把 Pico Key"
subtitle: "个人开源硬件密钥 Personal Secure Key"
date: 2025-09-07T15:52:43+08:00
lastmod: 2025-09-07T15:52:43+08:00
draft: true
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
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
- 原版[Yubico Yubikey](https://www.yubico.com/)，
- 其他的，[Onlykey](https://onlykey.io/)、[Solokey](https://solokeys.com/)、[Nitrokey](https://www.nitrokey.com/)、[thetis](https://thetis.io/)。

在选择上，我首先参考了[Privacy Guides](https://www.privacyguides.org/)的[密钥标准](https://www.privacyguides.org/en/security-keys/#criteria)。
并期望保证自己以最少的钱实现满足目标的标准，当然也考虑了大多数人的推荐。

CanoKey给我的感觉是现在转向直接制作实体Key出售，对于开源部分的维护很粗糙，尤其是开源部分的安全性还是有很多问题的，一个CanoKey大概要179，主要是想尽可能少花些钱，就Pass了这个方案。OpenSK给我的感觉是更新较慢，2021年之后全靠社区维护，而且再没有固定版本提供，需要自己用cargo build最新代码，我虽然也使用rust工作，但build终归也是一种很麻烦的事情……Yubico呢，就是贵，少则300多，多则上千，暂时不想这么花钱，便宜是现在第一要务。Onlykey那些，也都是贵的一批……最后，我选择PicoKeys，就是因为它很简单、便宜、好维护，功能也不差，满足了我几乎所有要求，但缺点也明显，PicoKey不能一个Key同时解决硬件登录和OpenPGP密钥。

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

{{< admonition type=note title="固件下载总结" open=true >}}
1. 选择硬件密钥的使用目标（HSM、Fido、OpenPGP）
2. 下载指定固件
3. 如果硬件需要切换使用能力（比如从Fido切换到OpenPGP），则需要先安装Pico Nuke固件
4. 硬件初始化之后，或对于全新硬件，刷入目标固件
5. 安装固件，只需要把对应固件，拖入开发版即可
{{< /admonition >}}

#### Step 3 - 安装

安装这部分主要记录的就是怎么刷RP2350-One的固件，以及如何完成初始化设定。
前面说刷固件就是把固件文件拖入开发版，这个操作本质上是针对RP2350-One进行的，并非所有开发版都这样，
不过大概率RP2350芯片的开发版都差不多。

详细步骤如下（即，通过恢复模式进行固件刷新的方式）：

1. 断开设备连接
2. 按住BOOTSEL按钮（BOOT按键）的同时，将设备插入USB端口（插入后就可以松开BOOT键了）
3. 文件管理器中将出现名为RPI-RP2（适用于RP2040板）或RP2350（适用于RP2350板）的挂载闪存单元
4. 将之前下载的 `.uf2` 文件复制到该挂载单元中
5. 设备将自动卸载闪存单元并重新挂载为Pico Key。LED指示灯会周期性闪烁

对于微雪的RP2350-One开发板，BOOT键稍微仔细看一下，就能看到，左侧是BOOT，右侧是RESET。
也可以对照一下前面的尺寸图，里面也能看到这个按钮。

大多数教程中，都推荐把固件拖入闪存后，等待1分钟，不过，我经过测试以及实验，
发现固件拖入闪存后，设备会自动弹出，并开始闪烁红灯，只需要等到红灯闪烁转为蓝灯周期性闪烁，
固件大概率就已经刷新好了。如果你还是不太理解，可以看看微雪提供的这个刷固件示例：

{{< admonition type=note title="刷固件示例" open=false >}}
![MicroPython固件刷新](Pico_MicroPython.gif)

详细可见微雪官网的 [RP2350-One#固件刷新](https://www.waveshare.net/wiki/RP2350-One#.E5.9B.BA.E4.BB.B6.E4.B8.8B.E8.BD.BD) 。
{{< /admonition >}}

刷好固件，现在就需要对Pico Key进行一些设置，需要打开[Pico Keys的配置网站](https://www.picokeys.com/pico-commissioner/)。
虽然这个配置是个网页，但所有操作都是在本地完成，不会有任何数据上传到Pico Keys的服务器。

首先就是Muse大佬的推荐配置，如下：

{{< admonition type=note title="MUSE大佬的推荐" open=true >}}
- **Select a known vendor** ： `Yubikey 4/5`
- **Options** ： `LED dimmable` + `Secure Boot` + `Secure Lock` + `Power Cycle on Reset`
- **Product Name** ： `Yubico YubiKey`
- **LED brightness** ： `1`
{{< /admonition >}}

这个配置主要是针对硬件密钥来配置，如果是OpenPGP的话，`Secure Boot` 和 `Secure Lock` 就可以不进行使用。

{{< admonition type=info title="其他选项的注释说明" open=false >}}
- `Secure Boot` 与 `Secure Lock` ：主要用于网络登录使用时的安全性
- `Power Cycle on Reset` ：仅对Pico Fido这个硬件Key有效
- **custom VID:PID** ：自定义VID和PID，是16进制的，形式为“1234:abcd”，可以自主填写
- **LED driver** ：有两个选择`Single`或者`WS2812`，根据实际情况选择即可，微雪这个开发版就是`Single`
{{< /admonition >}}

填写完配置，按下 `Commission via WebUSB` 按钮就可以开始初始化了。
在Windows下我使用Edge浏览器，就能完美实现Key的设定，很多人推荐在安卓手机上使用Chrome浏览器进行，这个我觉得倒是非必须。
在windows下的Edge浏览器写入设定时（也就是按下`Commission via WebUSB`按钮时），浏览器左上方会弹出一个确认框，选择Picokeys的选项就可以了。

稍等片刻，密钥闪烁蓝光并且亮度下降之后，就算初始化结束了。这时候需要重新插拔密钥，基本上，这个密钥就安装好了。
在密钥配置页面，选择`Commission via WebAuthn`，进行密钥验证，可以正常添加密钥并模拟登录，就算这个登陆密钥没问题了。

当然除了PicoKeys提供的[测试网站](https://webauthn.io/)，你也可以使用其他的，比如[Yubico的测试网站](https://demo.yubico.com/webauthn-technical/registration)。

#### Step 4 - 使用

一句话就是：你可以把它当成功能不全的Yubico Key来使用。

在网站上，通过前面的测试，其实你也应该感受到了，正常使用密钥，首先需要在网站上注册这个密钥为登录Key，
然后登陆的时候，再使用密钥进行给验证。不过要注意的是，使用密钥登录，一定要妥善保存网站登录的恢复代码或者恢复密钥。
免得自己把自己关在自己的账号之外……

日常管理密钥，可以使用 [YubiKey Manager](https://www.yubico.com/support/download/yubikey-manager/) 
或 [YubiKey Personalization Tools](https://www.yubico.com/support/download/yubikey-personalization-tools/) 进行普通管理和编辑；
要管理 TOTP，需要使用 [Yubico Authenticator](https://www.yubico.com/products/yubico-authenticator/)。
如果你需要看到更多信息或者和Muse大佬那样去检验安装的话，就是安装Yubico Authenticator。

{{< admonition type=note title="使用密钥登录Windows" open=false >}}
1. 打开 “设置-账户-登陆选项” ，选择“安全密钥”，打开管理。
2. 根据Windows的提示，就能完成基本的密钥添加。
3. 需要注意的时，使用密钥需要按一下硬件密钥的BOOT键，以启动认证。

说起来这还是很简单的。更详细的Windows使用说明可以参考[官方指引](https://support.microsoft.com/zh-cn/topic/%E4%BD%BF%E7%94%A8%E5%AE%89%E5%85%A8%E5%AF%86%E9%92%A5%E7%99%BB%E5%BD%95%E5%88%B0%E5%B8%90%E6%88%B7-b23a2a45-6ab8-4c86-9f22-bcadf60235aa)。
{{< /admonition >}}

使用密钥在网站登录时，需要想启动网站的登录验证，例如点击“use passkey”之类的按钮。然后点击密钥BOOT按钮。

#### Step 5 - OpenPGP

使用Pico-OpenPGP固件安装、设置之后，其实就可以把密钥添加到这个硬件卡（OpenPGP智能卡）了。
不过请注意一点，你需要先完成gpg密钥（含子密钥）的创建，同时需要熟悉gpg的使用。

{{< admonition type=note title="设置OpenPGP 智能卡" open=true >}}
1. 确认智能卡信息
2. 备份要迁移的密钥，含私钥、子密钥和撤销密钥
3. 通过gpg命令选择子密钥，然后逐一迁移到智能卡（简单示例：`key1` -> `keytocard`）
4. 删除迁移过的私钥
5. 确认一下本地是否只留下了公钥

参考：1. [GPG使用指南](https://blog.moe233.net/posts/18974f8b/) 2. [GPG物理密钥从安装到使用](https://blog.lamgc.moe/2021/02/26/gpg-smart-card-from-installation-to-use-tutorial/) 3. [智能卡使用](https://tccmu.com/2024/08/12/openpgp/)
{{< /admonition >}}

至此，就设定好智能卡了。剩下的就是使用这个设置好的智能卡了。
使用，就要超级简单了：`gpg --edit-card`后，输入 `fetch` 自动认证一下，就可以如常时用了。
