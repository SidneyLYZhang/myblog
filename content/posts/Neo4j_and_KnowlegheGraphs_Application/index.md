---
title: "Neo4j与知识图谱 —— Part 4"
date: 2021-06-15T16:30:21+08:00
lastmod: 2021-06-15T16:30:21+08:00
draft: false
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
description: "对于应用只想随便说说了，没什么干货，不看也罢，就是想补个全，多有遗漏，还望海涵啦……"
resources:
- name: "featured-image"
  src: "featured-image.jpg"

tags: ["Neo4j", "Cypher", "CQL", "GraphQL", "Learning", "python"]
categories: ["notes"]

lightgallery: true

toc:
  auto: true
---

<!--more-->

### 笔记列表

1. [Neo4j与知识图谱 —— Part 1 ：废话、安装与设计思路](https://lyzhang.me/neo4j_and_knowleghegraphs/)
2. [Neo4j与知识图谱 —— Part 2 ：Cypher基础](https://lyzhang.me/neo4j_and_knowleghegraphs_Cypher/)
3. [Neo4j与知识图谱 —— Part 3 ：建立数据基础](https://lyzhang.me/neo4j_and_knowleghegraphs_Data/)
4. Neo4j与知识图谱 —— Part 4 ：初步应用模式

因为时间已过很久，好久没有更新。主要是因为又有其他事情要做了。

但善始善终，还是尽快结束比较好？后续这两部分就显得略简单了。大意如是，倒是无所谓简陋了。

### (1) 应用形式

主要用于搜索、相关推荐、专题制作与用户画像生成方面。

对于搜索的应用，就是简单的根据用户搜索关键词结合图谱信息对用户可能的意图进行匹配与排序。这属于直接的应用。复杂一些就是相关推荐，内容对用户吸引力可以借由用户行为在内容本身标签的基础上的表征，再构建知识图谱的真实关系，对标签的价值进行加权，形成更具可能性的排序，算间接应用的模式。

推荐是现在的本职之一，借由知识图谱进行纠偏，是我现在的使用方向，曝光偏差很难消除，传统使用逆倾向分数或者构建强化学习之类的方案，媒体用户对于这类方法的反馈并不显著，至少点击率变化并不显著，所以，才尝试使用知识图谱在矩阵分解阶段结合倾向分数多做了一步纠偏，复合了知识图谱所包含的信息，也是为了扩展内容间关联。

专题制作方面，是智能营销的主要方面，为用户针对性生成专有专题，应该是基于知识图谱与智能推荐共同完成的分步智能化工作。

用户画像生成，就简单很多了。单纯的对用户行为在知识图谱上的漫步进行再现，形成用户独有的漫步图谱，再结合纠偏，就是用户的个人原始画像了。没啥技术含量，只能算是对用户行为与知识图谱的应用而已。

### (2) 创建应用模式

所有应用目标，都是在知识图谱提取方面展开的，对实体本身的搜索使用python直接在neo4j上查询，对结果子图基于场景再做分化。

所以，并没什么特别的模式可以写些厉害的东西了。

……

### 最后的虎头蛇尾

前面很杠杠，后面很拉跨……

因为事件太少，没时间好好总结了。凑合看看……大家不要打我哈……