---
title: "我的Haskell教程（第零章）"
date: 2019-01-03T11:05:21+08:00
lastmod: 2018-01-03T11:06:21+08:00
draft: false
keywords: ["Haskell", "教程", "learn", "第零讲"]
description: "数据科学的工作流程，主要来源自己的经验"
tags: ["教程", "Haskell", "编程"]
categories: ["数学","Haskell","随笔","教程"]
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


## 说在前面内容

### 1、踏出那一步

想起很久以前，写过一个Haskell的学习笔记，感觉当时自己真的是很认真，整理了自己的思路写下来。当时是看着 ***Learn Haskell the Hard Way*** 开始学习Haskell的，所以这个笔记也难以摆脱其影响，有很多不那么直观的地方，需要多次实践之后，才能感觉到其中的妙处。

而我也不想像传统编程教学那样，囫囵吞枣地教一些，编程语言这个讲法从一开始就预示着，这是一个人类与机器进行单向沟通的方法，所以就像真实交流中一样，需要一些技巧，而比较有意思的是，Haskell这个纯函数式编程语言与别的语言最大不同就是，在这个编程范式下，自带了很多固有的技巧，这也是后面会讲到的内容。虽然不会把每一个技巧都讲完，但肯定会把我认为重要基础的内容都说清楚。

我要踏出其实就是这一步了，开始讲，而不是等待一个完美的教材，再推荐给大家。

那我希望，和我学Haskell的同学也可以踏出那一步，不要害怕这个听上去很诡异的语言，不同往常思维的程序语言的讲解，最重要的一点就是不要害怕学不会。

### 2、教程的结构

教程主要由导引、正文、练习、参考文献、补充阅读五个部分组成。

我会再每一讲结束，给出下一讲的课程导引，也就是希望大家能去提前了解的东西，这些导引我会分出两个层次，基础的导引知识和前置导读。如果你是最最最初级的编程语言学习者，只需要看导引知识的内容就够了，如果你已经会了一些编程语言，或者你已经在读或准备读取研究生了，或者你打算了解的更深入和全面，那提前看一看前置导读中的推荐是很必须的。这些导引一般作为下一次课可能涉及的内容，而且是我不会单独再说明讲解的内容。

每一讲正是内容，我也会分两个部分，基础部分和延伸。如果你刚刚接触，我建议可以先看完基础部分之后，做了一些练习再看延伸部分的教程部分可能更有助于理解。当然这是我的建议，和自己的设想，你也可以完全凭借自己的需要和程度来看，也完全可以。

教程部分之后就是练习，我会按照我认为的容易程度来安排顺序。当然我很欢迎你给我发来你的答案或者你的疑问，不过我可能不会每一个都回复。对于疑问我会挑一些有代表性的，专门做疑难解答，很简单的问题就恕我不回复了，好好看看教程、导引等一定可以解决。

参考文献，是这一讲所涉及的关键概念和学术延伸的来源。也是帮助大家进行扩展阅读的内容，学有余力了，看看这些，绝对有意想不到的好处。最后的补充阅读是一些我认为值得一读的有关联的内容，可以帮助大家了解的更多，有些也可以用来帮助大家学习别人的技巧和方法，所以这一部分多为推荐。课后多多看书学习吧。

### 3、Haskell的简单介绍

Haskell，是什么呢？

首先，Haskell是一门编程语言。作为编程语言，Haskell有很多自己的特点，比如：纯函数式编程啦、惰性运算啦、使用静态类型啦，等等特点。如果只是这么描述Haskell这门编程语言其实并没有什么意义，而真实有效的了解这门语言，需要的就是一个学习用的教程，所以我期望你在学完这个课程之后，能够明白Haskell是什么，它的每一个特点都代表了什么。

当然，这么不负责任的甩锅给以后的教程，也不太负责。但是也不想直接把各种介绍直接放到大家面前，在我看来这意义不大，还会带给不了解、不熟悉编程语言的学习者以过多压力。

那Haskell的历史呢？

历史倒是可以简单说一下，感性的认识一下Haskell。1987年，FPCA的会议上形成了统一想法，就是建立一个新的函数式编程语言，也是从这时开始Haskell开始算是孕育了。从1990年Haskell的第一个版本诞生，经过期间数次变化，直到1999年，Haskell语言标准Haskell98发布，我感觉才逐渐有更多人加入到Haskell的演变中与扩展中。而现在所使用的Haskell语言标准Haskell2010也在这期间经过反复修订，到2010年才发布，形成我们现在看到的Haskell形态。

换句话说，Haskell已经32岁了。

### 4、闲聊

这是第0讲的内容，其实是没有所谓的练习的。我就多闲话两句，作为一个数学系毕业的人，我个人认为Haskell是非常适合数学系讲授给学生的一门编程语言，有了在Haskell上的学习再学其他编程语言我感觉都简单了一些。不是说Haskell更难，而实Haskell更接近数学论证的逻辑，而命令式编程语言更接近数学运算的逻辑，运算的逻辑大家都用的比较多，而使用论证的逻辑的机会太少了，Haskell倒是个有趣的练习途径。

当然我的这个教程不是为了数学系学生写的，不会让大家看不懂的。倒是如果你想学的快一些，根据我的经验，再多了解一些数学倒是不错的方法，当然这是对于Haskell的学习来说的。

### 5、第1讲导引

第1讲会讲基础的Haskell安装相关的内容，所以需要你先了解以下内容：

1. 命令行（或称，终端）：在你的电脑上找到它，了解它的基本功能；
2. 环境变量：从设置到效果，都有一个清晰认识。

建议的扩展阅读内容：

1. CLI (on Wikipedia) : https://en.wikipedia.org/wiki/Command-line_interface
2. 环境变量 (on Wikipedia) : https://en.wikipedia.org/wiki/Environment_variable
3. Peter Van Roy : Programming Paradigms for Dummies: What Every Programmer Should Know 

******

### 参考文献

1. [Haskell (programming language) in wikipedia](https://en.wikipedia.org/wiki/Haskell_(programming_language))
2. [A History of Haskell: being lazy with class](https://www.microsoft.com/en-us/research/publication/a-history-of-haskell-being-lazy-with-class/?from=http%3A%2F%2Fresearch.microsoft.com%2F~simonpj%2Fpapers%2Fhistory-of-haskell%2Findex.htm)
3. PETER VAN ROY,SEIF HARIDI: Concepts, Techniques, and Models of Computer Programming (The MIT Press; 2003.6.5)