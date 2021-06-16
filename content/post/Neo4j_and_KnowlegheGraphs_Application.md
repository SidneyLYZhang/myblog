---
title: "Neo4j与知识图谱 —— Part 4"
date: 2021-06-15T15:36:21+08:00
lastmod: 2021-06-15T16:30:21+08:00
draft: false
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

### 笔记列表

1. [Neo4j与知识图谱 —— Part 1 ：废话、安装与设计思路](https://lyzhang.me/post/neo4j_and_knowleghegraphs/)
2. [Neo4j与知识图谱 —— Part 2 ：Cypher基础](https://lyzhang.me/post/neo4j_and_knowleghegraphs_Cypher/)
3. [Neo4j与知识图谱 —— Part 3 ：建立数据基础](https://lyzhang.me/post/neo4j_and_knowleghegraphs_Data/)
4. Neo4j与知识图谱 —— Part 4 ：初步应用模式

因为时间已过很久，好久没有更新。主要是因为又有其他事情要做了。

但善始善终，还是尽快结束比较好？后续这两部分就显得略简单了。大意如是，倒是无所谓简陋了。

### (1) 应用形式

主要用于搜索、相关推荐、专题制作与用户画像生成方面。

对于搜索的应用，就是简单的根据用户搜索关键词结合图谱信息对用户可能的意图进行匹配与排序。这属于直接的应用。复杂一些就是相关推荐，内容对用户吸引力可以借由用户行为在内容本身标签的基础上的表征，再构建知识图谱的真实关系，对标签的价值进行加权，形成更具可能性的排序，算间接应用的模式。

### （2）创建应用模式

所有应用目标，都是在知识图谱提取方面展开的，对实体本身的搜索使用python直接在neo4j上查询，对结果子图基于场景再做分化。

搜索场景，需要构建节目的排序，所以结果子图被用来