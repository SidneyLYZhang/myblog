---
title: "知识图谱 - 论文翻译 #EINS"
date: 2020-03-08T15:18:21+08:00
lastmod: 2020-03-08T16:30:21+08:00
draft: true
keywords: ["知识图谱","paper","translation","Knowledge Graphs"]
description: "一篇关于知识图谱的综述论文，我感觉这篇论文已经写得很全面了。确实很好的总结了现有的知识图谱内容，以及研究前沿在什么地方。"
tags: ["交流", "学习", "笔记", "论文翻译"]
categories: ["笔记","学习"]
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

### 翻译前言

论文来自于 [ARXIV.org](https://arxiv.org/) ，130，而且很全面的介绍了知识图谱的研究现状，并对研究趋势做了说明。而且论文本身就叫做 [Knowledge Graphs](https://arxiv.org/abs/2003.02320) ，如果不是论文的话，多扩充一些，写成教材感觉都快够了……

最近，一直在忙公司知识图谱的事情，自己也颇有想法，然后正好看到这篇论文，突然感觉有种困了就被递了枕头的感觉，甚至有一瞬间觉得，可以就此做点什么自己的研究。

另一方面，想起来国内很多关于知识图谱的介绍都较为简单，很多事情说明并不清晰，也是促使我把这篇论文翻译了的决心。我自己获取这些知识也都是四处寻找，论文看了一篇又一篇，也仅仅是了解了一个大概，有些地方确实还是了解不够清晰。这篇确实也能在这方面减少很多自己梳理的难度。当然，作为单纯的入门，这篇论文会有一点艰深，但好在脉络清晰，遇到不好理解的地方善用百度、google也是可以比较好的学习到新知识的。

另外，我也把我的一些学习笔记放在了翻译中间，使用引言的方式记录：

> 笔记样式。应该还算比较好识别吧。

*论文原文 ： [arxiv-2003.02320](https://arxiv.org/pdf/2003.02320.pdf)*

**Knowledge Graphs** 的论文作者：

AIDAN HOGAN, IMFD, DCC, Universidad de Chile, Chile

EVA BLOMQVIST, Linköping University, Sweden

MICHAEL COCHEZ, Vrije Universiteit, The Netherlands

CLAUDIA D’AMATO, University of Bari, Italy

GERARD DE MELO, Rutgers University, USA

CLAUDIO GUTIERREZ, IMFD, DCC, Universidad de Chile, Chile

JOSÉ EMILIO LABRA GAYO, Universidad de Oviedo, Spain

SABRINA KIRRANE, SEBASTIAN NEUMAIER, and AXEL POLLERES, WU Vienna, Austria

ROBERTO NAVIGLI, Sapienza University of Rome, Italy

AXEL-CYRILLE NGONGA NGOMO, DICE, Universität Paderborn, Germany

SABBIR M. RASHID, Tetherless World Constellation, Rensselaer 

Polytechnic Institute, USA

ANISA RULA, University of Milano-Bicocca, Italy and University of Bonn, Germany

LUKAS SCHMELZEISEN, WeST, Universität Koblenz–Landau, Germany

JUAN SEQUEDA, data.world, USA

STEFFEN STAAB, Universität Stuttgart, Germany and University of Southampton, UK

ANTOINE ZIMMERMANN, École des mines de Saint-Étienne, France

以下即为全文翻译，但是全文较长，我分几个部分逐步发布。另外，因为我的翻译不是一次性完成，便于大家方便查找和阅读，请自行对照原文的参考文献，这个也没有翻译的必要，同时，我也保持了文献记录编号未作改变，所以我这就不另作参考文献这一部分了。

目录部分是我分步翻译的情况，也便于直接查找想看的那段文章在哪。

******

### 目录

- **知识图谱 - 论文翻译 #EINS**
  - 论文摘要 Abstract
  - 1 介绍 Introduction
  - 2 数据图 Data Graphs
- [知识图谱 - 论文翻译 #ZWEI](https://lyzhang.me/post/Review_paper_of_KnowledgeGraphs_SIC)
  - 3 Schema,Identity,Context
- [知识图谱 - 论文翻译 #DREI](https://lyzhang.me/post/Review_paper_of_KnowledgeGraphs_DK)
  - 4 Deductive Knowledge
- [知识图谱 - 论文翻译 #VIER](https://lyzhang.me/post/Review_paper_of_KnowledgeGraphs_IK)
  - 5 Inductive Knowledge
- [知识图谱 - 论文翻译 #FÜNF](https://lyzhang.me/post/Review_paper_of_KnowledgeGraphs_CEQA)
  - 6 Creation and Enrichment
  - 7 Quality Assessment
- [知识图谱 - 论文翻译 #SECHS](https://lyzhang.me/post/Review_paper_of_KnowledgeGraphs_RP)
  - 8 Refinement
  - 9 Publication
- [知识图谱 - 论文翻译 #SIEBEN](https://lyzhang.me/post/Review_paper_of_KnowledgeGraphs_KGPSC)
  - 10 Knowledge Graphs in Practice
  - 11 Summary and Conclusion
- [知识图谱 - 论文翻译 #ACHT](https://lyzhang.me/post/Review_paper_of_KnowledgeGraphs_AB)
  - Appendix A : Background
- [知识图谱 - 论文翻译 #NEUN](https://lyzhang.me/post/Review_paper_of_KnowledgeGraphs_AFD)
  - Appendix B : Formal Definitions

******

### 论文摘要 ABSTRACT

在这篇论文中，我们对知识图谱进行了全面介绍，尤其最近知识图谱最近在需要多样化、动态、大规模数据收集的场景下又引起了业界和学术界的极大关注。在简要介绍之后，我们介绍并对比了知识图谱的各种基于图的数据模型和查询语言。我们也讨论了Schema、identity和context在知识图谱中所起到的作用。我们也解释了如何通过演绎和归纳的组合来表示和提取知识。我们总结了知识图谱的创建、丰富、评估、改进和发布的方法。然后我们概述了著名的开放知识图谱和企业知识图谱，以及他们的应用与他们如何使用上述技术。最后我们总结了知识图谱的未来研究方向。

### 1 介绍 INTRODUCTION

“知识图谱”这个短语自从最早1972年的一篇文献[440]中被使用以来，这个短语的现代形式源自2012年发布的Google知识图谱[459]，随后Airbnb[83]、亚马逊[280]、eBay[]、Facebook[]、IBM[]、领英[]、、微软[]、Uber[]等等更多企业也随之宣布了对知识图谱进一步的开发研究。事实证明，工业界越来越普及的应用使学术界难以忽视：越来越多的关于知识图谱的科学文献正在被发表，这其中还包括书籍（例如[400]），以及概述定义的论文（例如[136]），新技术（例如[298,399,521]），知识图谱特定方面的分析（例如[375,519]）。

所有这些发展的基础使使用图形表示数据的核心思想，通常使用一些方法来明确的刻画出知识[365]。这个结果通常用于整合、管理以及压缩大规模数据集的应用场景中[365]。例如与关系模型或者NoSQL的替代方案相比，基于图的知识表示有更多优势。图为很多领域提供了一个简洁、直观的抽象表达，其中的边代表社交数据中固有的实体关系、生物学中的相互作用、数目的引文与合著者、运输网络之间的（具有潜在周期性的）关系[15]。图允许维护者推迟一个Schema的定义，并且允许数据及它的范围，已可能比关系设置中更灵活的方式进行演化，

### 2 数据图 DATA GRAPHS



#### 2.1 模型 Models



#### 2.2 查询 Querying

