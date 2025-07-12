---
title: "搞个邮件服务器吧"
subtitle: "部署poste.io邮件服务器"
date: 2025-07-05T14:14:45+08:00
lastmod: 2025-07-05T14:14:45+08:00
draft: false
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
description: "这是一篇关于折腾自建邮箱服务器的碎碎念式记录。"
license: ""
images: ["largepic.jpg"]

tags: ["mailserver","howto","beginner","DNS 配置","域名邮箱"]
categories: ["notes"]

featuredImage: "largepic.jpg"
featuredImagePreview: "largepic.jpg"

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


【TL;DR】放弃 Yandex，改用 poste.io 自建邮箱，踩坑 DNS、rDNS、反代，虽不完美却乐在其中。

<!--more-->

我有好几个域名，常用的两个。怎么也得用起来，之前使用[Yandex](https://mail.yandex.ru/)的域名邮箱，
但是想好用还要付费什么的，而且使用有点累人，毕竟动不动就蹦俄语。这么折腾了好几年，可算下定决心，
还是自己部署一个邮件服务器吧。

本来想单点部署Stalwart邮箱服务器，但是我搞了好几天，都没能搞定……不是无法验证域名，就是证书有问题，
使用nginx反代也总是出现返回错误……最终让我难绷到不要折腾stalwart了……

然后就选择了poste.io来部署服务器。

我主要参考了[香菇肥牛](https://qing.su/)的这篇[教程](https://qing.su/article/poste-io-with-docker-on-linux.html)。
不过，我确实想说，香菇肥牛真厉害，按照他的教程部署，确实减少了很多问题。
如果你需要的是一个十分详细的教程，那么，我更推荐香菇肥牛的教程。我这个只是一个个人的查缺补漏的记录。

## 部署要点

我这里只记录了关键信息。不包含具体流程。具体安装部署，可以参考香菇肥牛的教程（话说这个教程很棒！详细完整！）。

### 1. 服务器问题

最先需要的，是一个可以进行[Reverse DNS（rDNS）](https://learn.microsoft.com/zh-cn/azure/dns/dns-reverse-dns-overview)配置的服务器。
我最后选择购买了[racknerd](https://my.racknerd.com/aff.php?aff=15010)的服务器，发工单设置rDNS。

其实也有别的服务器可以选择，只是racknerd我看还挺便宜啊，适合我，所以选择了这个。

### 2. 域名 DNS 的设置

这部分我还真的是有点无语，或者说是对Godaddy感到无语，很多地方的设置并不像以前那么好操作，尤其是mail服务器要设置大量的DNS信息，
写起来真的让我感觉很累，而且不好检查其中的问题。
看多了很容易看串行……

要不是域名一直在godaddy买，真的想把DNS换到别家进行管理……

只要按照香菇肥牛的说明来设置，其实还是不难的。剩下的就是稍作等待，等DNS设置生效。

这里后续增加新域名的DNS设置，用来后续便于自己查询（当然任何人都可以来替换使用，应该都差不多）：

| 记录类型 | 前缀 | 解析地址 | 其他 |
| ------- | ---- | ------- | ---- |
|MX|@|mx.qing.su|优先级 10；请注意，这个MX记录地址应该是和你的主域名设定是一样的。|
|TXT|@|v=spf1 a mx -all| |
|TXT|_dmarc|v=DMARC1; p=reject; rua=mailto:postmaster@example.com; ruf=mailto:postmaster@example.com|可以根据需要指定邮箱地址|
|TXT|s2025xxxxxxx._domainkey|k=rsa; p=Your_domain_key_xxxxxx|根据系统生成的值来设定|

autoconfig、mta-sts、autodiscover这三个我也都有设置，主要也是为了便于客户端使用。另外就是，我也设置了[DNS SRV](https://www.cloudflare-cn.com/learning/dns/dns-records/dns-srv-record/)……这部分实在是太难了，我搞了很久很久才明白……总之一句话说，就是让smtp之类的信息可以更好更准确的到达服务器。

### 3. 创建邮箱账户

这个……怎么说呢，我上来只创建了个人邮箱地址，没有做postmaster，这个确实是有问题的。毕竟DNS填写中的很多地方我都使用了postmaster的邮箱……

不过确实也能感受出，poste.io是很老的产品了，不支持很多安全措施，这也就意味着，建立的账号密码，一定要定期更新，否则风险确实有些高，还有满足密码的位数、字符类型多样性这方面，也十分重要，尽量减少被暴力破解的风险，就算是自己的小服务器，安全也是很重要的。

## 我的补充

或者应该叫我的遗憾？没能部署好Stalwart，是我最遗憾的事情。毕竟这个安全上、功能完整性上都要比poste.io好不少。
但我确实搞不定Docker下Stalwart的Nginx的反代理，搞得我焦头烂额……只好默默放弃了……
希望有好心人，能写一个基于Docker的Stalwart如何设置反代的教程……

这些天用这自己部署的邮箱，发送接收邮件啥的，还真的是成就感满分。虽然还不完美，但前景可期。尤其是，有了专门针对某些订阅信息的专门邮箱，
不再把主邮箱搞得一堆垃圾邮件，心烦意乱了。这就已经让我快乐了不少。

但是，还是有很多问题，比如Claude注册，还是很容易被毙掉，因为之前没经验，用自己的域名邮箱注册被B掉之后，就一直很难注册了。
或许是认识我了吧？无语……

下一步还是希望能把webmail搞得更安全一些，至少双因素验证要有吧……这个慢慢考虑了……今年除非有突飞猛进的经验增长，要不暂时就先这样了吧……

以上。

最后就是希望大家和我都一切顺利~