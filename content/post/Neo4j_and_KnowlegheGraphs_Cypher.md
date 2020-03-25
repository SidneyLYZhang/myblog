---
title: "Neo4j与知识图谱 —— Part 2"
date: 2020-03-20T15:36:21+08:00
lastmod: 2020-03-24T16:30:21+08:00
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

### 笔记列表

1. [Neo4j与知识图谱 —— Part 1 ：废话、安装与设计思路](https://lyzhang.me/post/neo4j_and_knowleghegraphs/)
2. Neo4j与知识图谱 —— Part 2 ：Cypher基础
3. Neo4j与知识图谱 —— Part 3 ：建立数据基础
4. Neo4j与知识图谱 —— Part 4 ：初步应用模式

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

同时建立节点和关系，也是可行的，需要先指明建立的节点变量，再建立关系：

```cypher
CREATE (me:Person {name:"Sidney Zhang"})
CREATE (tom:Person {name:"Tom Z."})
CREATE (me)-[rel:FRIEND]->(tom)
```

不过还是要注意重复建立节点的问题，所以我个人感觉查询再新建关系是更保险的模式。

**1.4 查询/模式匹配**

我觉得使用Cypher进行查询，就是一种模式匹配，这完全不同于SQL的逻辑，但至少从图的角度来看，模式匹配可以更好的遍历图数据，精确的关系模型虽然也可以实现，效率在这里并不是我目前关心的重点。总之，在Neo4j中，进行查询的核心，其实就是模式匹配，并在这个基础上实现各种数据库计算。上面通过查询再建立数据关系就是一个例子。

所谓模式匹配，在Neo4j中就是匹配到所给出的描述结构的对应数据，并把结果赋给给出的变量上。

```cypher
MATCH (a:Person {name:"x-man"})-[r]-(x) RETURN a,r,x
```

`RETURN` 就是返回结果，不是单纯的匹配好，记录对应变量，总的来说，与SQL的列名称相似。我在使用中，感觉Cypher的语法更偏于直观含义的表达。

在复杂一些，在模式匹配基础上增加一些限定，这与SQL一样，都是使用 `WHERE` 关键词，我们把上一个 `MATCH...RETURN` 的结构可以改为：

```
MATCH (a:Person)-[r]-(x)
WHERE a.name = "x-man"
RETURN a,r,x
```

在WHERE的条件下，可以建立更复杂的匹配模式，只在MATCH中实现的模式匹配，我认为应该叫精确匹配，当需要范围特性、模糊特性等要求时，使用WHERE关键词的匹配就更灵活，也更便于使用，因为WHERE的使用在这里也更接近SQL的语法。不过，在cypher中也有一些独有的厉害的关键词，比如：`STARTS WITH` 由某些字母开始、`ENDS WITH` 由某些字母结束、`CONTAINS` 包含某些字母、`NOT` 不是、`exists()` 存在[属性、关系等]、`IN` 在某个序列中，以及很多类似的很有意义的关键词。下面是另一种开头文字匹配的方式：

```
MATCH (p:Person)-[r]-(x:Person)
WHERE p.name =~ 'Sidn.*'
RETURN p,r,x
```

**1.4 更新**

也是基于MATCH所匹配到的节点或者关系，对应更新相关数据。一般更新我们想到的都是使用 UPDATE 这样的关键词，但在Cypher中，匹配到所需要的数据，然后使用 `SET` 进行更新，在直观感受上，似乎更便于理解。

```
MATCH (p:Person {name:"Sidney Zhang"})
SET p.Gender = "male"
```

是否还需要 `RETURN` 其实仅仅是是否要检查结果的选择而已了。

**1.5 更多内容**

对于如何使用Cypher，可以更多参考官方的 [Cypher指引](https://neo4j.com/developer/cypher-style-guide/) ，以及 [官方推荐的代码规范](https://github.com/opencypher/openCypher/blob/master/docs/style-guide.adoc) 。

当然也可以学习一下 [官方Cypher手册](https://neo4j.com/docs/cypher-manual/4.0/) 。

### （2）Python Package Neo4j 入门

python包的使用其实并不复杂，与一般包的区别不大。 `Neo4j` 这个包本身是对Cypher Shell的一个Python表达，所以在使用上，只要了解Cypher，使用这个也并不难。

**2.1 安装**

我觉得直接使用 `pip` 安装就可以了： `pip install neo4j` 。Neo4j官方出过很多版本的python驱动，除了官方的、Community的，我个人更偏向于官方最新的这个python驱动，然后基于这个驱动再开发适用自己习惯的API。

我觉得并不需要什么特别的设置，就可以，选择从国内镜像下载可能速度更好一些。其他实在没有什么更需要再安装部分特别记录的。

检查安装是否成功，只需要打开python，输入：

```python
> from neo4j import GraphDatabase
```

安装正常的话，一般就不会有什么错误出现。

**2.2 登录到图数据库**

登录到图数据库，首先需要你确定自己的用户、密码和网址。

一般Community版本Neo4j的用户只有：`neo4j`。密码的话，第一次登录网页管理器的时候，会被要求修改。网址的话，因为python基于bolt协议进行连接，所以需要仔细检查数据库的bolt的地址和端口。所以，一般而言，登陆模式如下：

```python
uri = "bolt://localhost:7687"
driver = GraphDatabase.driver(uri, auth=("neo4j", "#password#"))
```

**2.3 使用Cypher**

官方驱动的基本模式，是把查询转换为网络交互，也就是每次查询都是一个和服务器的对话过程。所以，运行的前题是构建一个基本的交互渠道——SESSION，当已经连接好服务器的时候，处理起来是很简单的：

```python
dialog = driver.session()
```

因为官方驱动所用于处理的依然是基于Cypher的，在本地把查询交由服务器处理，再获得结果：

```python
result = dialog.run("MATCH (a)-[r]-(b) RETURN a,r,b")
```

但是，通过python获得的返回结果，按照官方说法是 `StatementResult` 。我在官网查了半天，感觉这个StatementResult应该就是再Consuming Result里说的： `BoltStatementResult` ：

> Every time Cypher is executed, a BoltStatementResult is returned.
> 
> 每次Cypher被执行，都会返回BoltStatemmentResult这个结果。

不过这个结果，一般可以当作python list 和python dict的混合。可以仔细查询 [官方说明](https://neo4j.com/docs/api/python-driver/current/results.html) 进行处理。

### （3）构建目前所需的处理工具

我需要的工具，在前面其实已经简单说明了，最重要需要的是建立、更新、查询、删除这四个功能。

新建一个节点和新建一个关系，这是相似的，不同之处在于新建关系需要查询出对节点，那么就有两种建立函数，分别针对节点和关系：

```python
# CREATE NODE
def newNode(name, type, values = None):
    dialog = """
    CREATE (a:$type {name:$name})
    RETURN a
    """
    anode = doCypher(dialogg, name, type)
    updateNode(anode, values)

# CREATE RELATIONSHIP
def newRelation(nType,rType,nValues,rValues):
    xNodes = matchnode(nType, nValues)
    createRelationship(xNodes, rType, rValues)
```

更新操作也是类似关系的建立模式，先MATCH再SET： `def update(mode,type, values)`。删除的道理类似。不一样的是查询，因为查询需要定义的内容比较复杂。

通用查询需要什么内容呢？首先，查询什么，节点还是关系；其次，在什么条件下查询；最次，需要取得什么结果。所以至少查询需要两种查询模式，分别针对节点和关系。但我并不是要完全把Cypher的结构都分散到函数的构建里。所以总体的设计上，是要改变新建节点或者关系的这个模式，首先分别建立节点抽象和关系抽象，再设计总体的查询就会简单很多。这样也可以冲更抽象的结构上形成统一。

```python
class node_kg(object):
    def __init__(self, session, type):
        pass
    def new(self, name):
        pass
    def getNodes(self, byValue = None, condition = None):
        pass
    def contentNodes(self, byValue = None, condition = None, selection = None):
        pass
    @staticmethod
    def update(cls, type, byValues, newValues):
        pass
class relationship_kg(object):
    def __init__(self, session, type, node_a = None, node_b = None):
        pass
    def new(self,name):
        pass
    def getRelationship(self, byValue = None, condition = None):
        pass
    def contentRelationships(self, byValue = None, condition = None, selection = None):
        pass
    @staticmethod
    def update(cls, type, nodes, byValues, newValues):
        pass
class KGDatabase(object):
    def __init__(self, username, password):
        pass
    def search(self, type, byValue = None, condition = None, selection = None):
        if type in self.nodeTypes :
            pass
        elif type in self.relaTypes :
            pass
    # def .. .. ..
```