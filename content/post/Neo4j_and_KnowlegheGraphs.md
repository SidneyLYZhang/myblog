---
title: "Neo4j与知识图谱 —— Part 1"
date: 2019-12-23T15:36:21+08:00
lastmod: 2019-12-23T16:20:21+08:00
draft: true
keywords: ["Neo4j", "Cypher", "CQL", "GraphQL", "Learning", "python"]
description: "总得积累点新东西。譬如Neoj4。譬如知识图谱。这是一份我的学习笔记。如有谬误还望海涵。"
tags: ["交流", "学习", "笔记"]
categories: ["笔记","学习", "Neo4j"]
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

### 废话要从头开始说

最近一年，一直在忙这忙那，没有一点总结自己的时间。事情总感觉很赶喽，不知道是不是我的错觉，但时间真的总感觉不够用了、越来越不够用。
是不是自己年纪大了呢？还是压力释然呢？

现在公司的业务其实是用不上知识图谱的，但是，上级想要，想把知识图谱应用到真实业务里面，不是简单的研究一下。这倒是让我惊讶了不少。
一般直接考虑知识图谱的应用，多数是在搜索、文献、医学、智能推荐等行业里有不少应用。但是在我现在的行业（媒体行业），能直接挂钩的似乎也就是搜索，
但这还不是领导想要的那种应用。

绞尽脑汁写了一个方案，还必须落地执行。就我一个人哦……

好吧，我就把我在这里落地执行的整个过程都记录一下，整理成一种笔记好了。于是有了这篇Blog。
不知道后面还会不会写，但我还是会尽可能都记录下来。以留记录和自己的知识整理。

从Neo4j的角度和知识图谱本身的角度，一共两个方面来写我的笔记吧。这里不会涉及最终的落地执行方案，会写一些技术实现和Tips。

所以应该会有一些错漏、不足，大家多多包涵，也请多多指教。

### 安装与配置

国内下载Neo4j真心很困难，加历靠着300M的网络勉强能下载，但在公司全体员工分享200M的带宽，下载

ftp://neo4j.55555.io