---
title: "Neo4j与知识图谱 —— Part 2"
date: 2020-02-24T15:36:21+08:00
lastmod: 2020-03-05T16:30:21+08:00
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

### （0）介绍

搞Neo4j，不得不把Cypher搞起来。没有这个，很多基本操作也都无法开展。顺着这个角度，这一部分主要记录如下内容：

1. Cypher怎么用
2. 怎么用python处理Neo4j
3. 用基础工具构造我需要的能力

本来想直接翻译Cypher官方文档Basic部分，但是觉得最近可能来不及，以后有时间再说，如果我想起来的话。所以入门Cypher部分就是我的心得体会了。以及neo4j这个python包的一些吐槽与使用介绍，这个的官方介绍很粗糙，所以使用neo4j这个包的方法，我会写的稍微详细，也是为了我不要忘记。

因为官方包其实不太好用，毕竟neo4j包还是以处理cypher为基础的，虽然有py2neo这个社区包，不过很多特新未实现，导致用起来有的时候有点累，而且我用得到的部分也比较少，自己做一把轮子应该比现成的更好用一些。

### （1）Cypher入门

作为图数据库，Neo4j的基本元素总共就有2种：节点和关系。所以Cypher的入门要简单很多。相对的，常用的一些关键语法再掌握了，基本问题就不大了。

**1.1 节点**

在Neo4j种使用 `( )` 来标注这是一个节点（一对括号）。如何指明是哪一个节点？又要如何指明是哪种节点呢？可以这样简单的罗列如下：

```Cypher
() //匿名节点
(n) //任意节点n
(:Person) //匿名且为Person类型的节点
(p:Person) //任意为Person类型的节点p
```

即，任意节点在Cypher语法中都有4个默认的组成：标志、变量、指示和标签。`()` 就是标志，表明这是一个节点；`:`是一个指示，表明其后是标签，也就是这个节点是个什么类型；标签和变量就是由`:`分割，没有`:`的时候就只是变量。另外，在上面这短Cypher代码中，`//` 是一个注释标志，其后都是注释内容。

节点能够指明了，那么，每一个节点的区分就是由属性来区分了。节点属性，由 `{}` 标称。例如： `(n:Person {name:"Sidney Zhang", age:35, gender:"man"})` ，就表示一个Person类型节点变量n，有属性三个，分别为name、age、gender，且三个属性都分别有对应值。

**1.2 关系**

在图数据库中，节点之间的连接，也是图的基本元素，就是关系。在Neo4j中，这个关系被叫做表示关系（Representing Relationships），换句话说，就是可以画出来的直接关系，而且一般要有有向性。基本的表达使用两个方向箭头进行表征： `-->` 、 `<--`。

在细节表示上，与节点表示基本是一样：

```cypher
(n:Person {name:"Sidney Zhang"})-[r:LOVE]->(m:Person {name:"Jossitix Z"})
(n:Person {name:"Sidney Zhang"})-[:LOVE]->(m:Person)
(n:Person)-[rel]->(m:Person)
(n:Person)-[rel]-(m:Person)
```

对于关系来说，匿名关系没有会用到的场景。毕竟，对于关系来说，指明是什么关系更能帮助查询和分析。

类似的关系也是可以带有属性的，比如： `()-[:WATCH {times:4, duration:1203, duration_unit:"s"}]->()` ，就标名了一个收看（WATCH）的关系，其中，看了4次，看了时长1203秒。

综合来说，节点与关系，其实就是图数据库最基本的元素定义，他们都包含变量表示、本体表示、标签标识和分割指示这四个部分。在不同的元素类型中，以不同的模式表达。

**1.3 创建**

创建一个节点还是一个关系，在Neo4j中都是使用 `CREATE` 这个关键词进行的。

```cypher
CREATE (p:Person {name:"Sidney Zhang"})
CREATE (p:Person {name:"Jossitix Z"})

MATCH (sidney:Person {name:"Sidney Zhang"})
MATCH (jossitix:Person {name:"Jossitix Z"})
CREATE (sidney)-[r:LOVE {married:date(YEAR=2013)}]->(jossitix)
```

基本模式都差不多，不同之处在于：建立关系的时候，需要先查询对应节点再建立关系，否则容易重复建立所需的节点。

**1.4 查询/模式匹配**



### （2）Python Package Neo4j 入门



### （3）构建目前所需的处理工具