---
title: "搞个邮件服务器吧"
subtitle: "部署poste.io邮件服务器"
date: 2025-07-05T14:14:45+08:00
lastmod: 2025-07-05T14:14:45+08:00
draft: true
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
description: ""
license: ""
images: []

tags: ["mailserver","howto","beginner"]
categories: ["notes"]

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

我有好几个域名，常用的两个。怎么也得用起来，之前使用[Yandex](https://mail.yandex.ru/)的域名邮箱，
但是想好用还要付费什么的，而且使用有点累人，毕竟动不动就蹦俄语。这么折腾了好几年，可算下定决心，
还是自己部署一个邮件服务器吧。

本来想单点部署Stalwart邮箱服务器，但是我搞了好几天，都没能搞定……不是无法验证域名，就是证书有问题，
使用nginx反代也总是出现返回错误……最终让我难绷到不要折腾stalwart了……

然后就选择了poste.io来部署服务器。

我主要参考了[香菇肥牛](https://qing.su/)的这篇[教程](https://qing.su/article/poste-io-with-docker-on-linux.html)。
不过，我确实想说，香菇肥牛真厉害，按照他的教程部署，确实减少了很多问题。
如果你需要的是一个十分详细的教程，那么，我更推荐香菇肥牛的教程。我这个只是一个个人的查缺补漏的记录。

## 部署流程

### 0. 服务器问题

最先需要的，是一个可以进行[Reverse DNS（rDNS）](https://learn.microsoft.com/zh-cn/azure/dns/dns-reverse-dns-overview)配置的服务器。
我最后选择购买了[racknerd](https://my.racknerd.com/aff.php?aff=15010)的服务器，发工单设置rDNS。

其实也有别的服务器可以选择，只是racknerd我看还挺便宜啊，适合我，所以选择了这个。

### 1. 域名 DNS 的设置

