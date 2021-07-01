---
weight: 1
title: "Neo4j与知识图谱 —— Part 1"
date: 2020-03-05T16:30:21+08:00
lastmod: 2020-03-05T16:30:21+08:00
draft: false
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
description: "总得积累点新东西。譬如Neoj4。譬如知识图谱。这是一份我的学习笔记。如有谬误还望海涵。"
resources:
- name: "featured-image"
  src: "featured-image.png"

tags: ["Neo4j", "Cypher", "CQL", "GraphQL", "Learning", "python"]
categories: ["notes"]

lightgallery: true

toc:
  auto: true
---

<!--more-->

### 废话要从头开始说

最近一年，一直在忙这忙那，没有一点总结自己的时间。事情总感觉很赶喽，不知道是不是我的错觉，但时间真的总感觉不够用了、越来越不够用。
是不是自己年纪大了呢？要不就是亚历山大了呢？

现在公司的业务其实是用不上知识图谱的，但是，上级想要，想把知识图谱应用到真实业务里面，而且不是简单的研究一下那种。这倒是让我惊讶了不少。
一般直接考虑知识图谱的应用，多数是在搜索、文献、医学、智能推荐等行业里，也是这些领域有不少成熟应用方案。但是在我现在的行业（媒体行业），能直接挂钩的似乎也就只有搜索，
但这还不是领导想要的那种应用。

绞尽脑汁写了一个方案，还必须落地执行。就我一个人哦……

好吧，我就把我在这里落地执行的整个过程都记录一下，整理成一种笔记好了。于是有了这篇Blog。
不知道后面还会不会写，但我还是会尽可能都记录下来。以留记录和自己的知识整理。

从Neo4j的角度和知识图谱本身的角度，一共两个方面来写我的笔记吧。这里不会涉及最终的落地执行方案，会写一些技术实现和Tips。

所以应该会有一些错漏、不足，大家多多包涵，也请多多指教。

### 笔记列表

1. Neo4j与知识图谱 —— Part 1 ：废话、安装与设计思路
2. [Neo4j与知识图谱 —— Part 2 ：Cypher基础](https://lyzhang.me/posts/neo4j_and_knowleghegraphs_Cypher/)
3. [Neo4j与知识图谱 —— Part 3 ：建立数据基础](https://lyzhang.me/posts/neo4j_and_knowleghegraphs_Data/)
4. [Neo4j与知识图谱 —— Part 4 ：初步应用模式](https://lyzhang.me/posts/neo4j_and_knowleghegraphs_Application/)

### 安装与配置

国内下载Neo4j真心很困难，加历靠着300M的网络勉强能下载，但在公司全体员工分享200M的带宽，下载就成了一个大问题。所幸找到了这个：

**[neo4j 镜像的索引](http://doc.we-yun.com:1008/doc/)**

或者： [neo4j](https://we-yun.com/index.php/blog/releases-56.html)

有几乎所有neo4j版本的下载，重点是速度很好，不知道是谁提供的，总之真的很好用的一个下载镜像。

对于正式安装来说，需要分为两个部分，一个是Java，另一个才是Neo4j。有趣的是，按照我在Ubuntu上安装时的感受，Neo4j4.0.0支持的是Java JDK 11，对于更新的JKD 13并不支持。所以请暂时安装Java JDK 11吧：

[Java SE Downloads](https://www.oracle.com/java/technologies/javase-downloads.html)

从这里下载对应需要的版本，然后安装。对于 Linux 系统，通过官网安装，可以下载 “Linux Compressed Archive” 这个安装包到你的安装目录（例如 `\usr\local\JVM`），解压后，在终端配置中增加环境变量，假定默认终端为ZSH：

```zsh
$ vim ~/.zshrc

export  JAVA_HOME=/usr/local/jvm/jdk11.0.6
export  CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
export  PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
export  JRE_HOME=$JAVA_HOME/jre

$ source ~/.zshrc
```

当然对于Linux来说，直接使用包管理器安装可能更容易，现在大部分分支都已包含OpenJDK11，例如Ubuntu安装就是 `sudo apt-get install openjdk-11-jdk` 。按照Oracle的说法，Oracle Java JDK与OpenJDK是一样的，所以，不用担心兼容问题。

然后安装Neo4j，同样并不复杂。

对于Neo4j至少有两个选择可以用于使用，一般大家都推荐使用Community，免费、安装简单；另一个方式就是安装官方提供的Desktop版程序，对于windows或者macOS来说，这个方法也不错，但是Desktop版本并非免费，包含试用企业版本程序，试用一年，如果只有一年的使用预期且不使用Linux，那么也是很不错的选择。

Desktop版本Neo4j的图数据库引擎，是可以按照自己需要安装的，这在初期我觉得还挺有趣的，但是实际使用的时候，就有点选择困难了，因为实际体验上我并没有感受到各版本之间实质的巨大变化。

按照我的一般处理原则，就直接选择当前最新版本进行安装，也就是下载安装Neo4J 4.0.0。

对于Community版本的Neo4j来说，下载解压缩之后，就可以使用了，只要Java配置合适，基本就没有什么问题。更进一步，需要系统自动启动图数据库的服务，在Arch Linux中相对简单，先添加必要的环境变量：

```zsh
$ vim ~/.zshrc

export NEO4J_HOME=/usr/local/neo4j/
export PATH=$PATH:$NEO4J_HOME/bin/

$ source ~/.zshrc
```

另一方面，也需要解决Linux的文件数限制，保证查询时不会出现错误，当然，这也是Neo4J官方要求，首先要先确定你需不需要修改：

```zsh
$ ulimit -n
1024
```

如果结果小于60000，就需要修改文件限制了，永久性修改方法如下：

```zsh
$ sudo vim /etc/security/limits.conf
password: 

*       hard        nofile      65535
*       soft        nofile      65535
```

保存后重启机器，即可生效。然后，这是一个选择，如果你需要更多的图算法支持的话，那么还需要安装 [apoc](https://neo4j.com/developer/neo4j-apoc/) （apoc = Awesome Procedures on Cypher），通俗来说就是Neo4j的存储过程库，是作为Neo4j的插件存在的，所以不是包含在基本的安装中。

安装方法也很简单，从对应位置下载到 `$NEO4J_HOME/plugins` 文件夹就可以。我使用 `wget` 下载，也可以使用别的方式。然后，我们需要修订一些Neo4j的一些设置选项：

```zsh
$ vim $NEO4J_HOME/conf/neo4j.conf

# 修改以下内容：
dbms.directories.import=import
dbms.memory.heap.initial_size=512m
dbms.memory.heap.max_size=1g
dbms.memory.pagecache.size=512m
dbms.connectors.default_listen_address=0.0.0.0
dbms.read_only=false
dbms.security.procedures.unrestricted=apoc.*
```

保存之后，基本的配置就算完成了。后续需要的，就是把neo4j加入systemd的启动行列，以保证neo4j的服务可以顺利每天启动。

创建服务的第一步，就是在 `/etc/systemd/system` 新建 `neo4j.service` 初始化文件，最优解则是使用 `sudo systemctl edit neo4j.service --full --all`：

```vim
[Unit]
Description=neo4j
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
ExecStart=/home/neo4j/bin/neo4j start
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/home/neo4j/bin/neo4j stop
PrivateTmp=true
User=root

[Install]
WantedBy=multi-user.target
```

不要忘记修改 `neo4j.service` 的权限：

```zsh
$ sudo chmod 754 neo4j.service
```

刷新systemctl的配置情况，就可以创建相应的自启动服务了：

```zsh
$ sudo systemctl daemon-reload
$ sudo systemctl start neo4j
$ sudo systemctl enable neo4j
```

对于 `systemctl` 的使用，可以参考 [阮一峰的入门教程](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-part-two.html) 。另外，apoc的介绍，可以看看 [Neo4j_APAC](https://me.csdn.net/GraphWay) 的介绍（[1](https://blog.csdn.net/graphway/article/details/78957415)、[2](https://blog.csdn.net/GraphWay/article/details/78986957)、[3](https://blog.csdn.net/GraphWay/article/details/79083135)），虽然是2017年的介绍了，但还是有很多借鉴意义的。

### 设计想法与思路

这里简单介绍我对于知识图谱的一些想法和感受。当然，最重要的就是公司这个知识图谱是如何设计的。

一般说到知识图谱，其实都是已经建设好的了，比如： [CN-DBpedia](http://kw.fudan.edu.cn/apis/cndbpedia/) 。但我这次不一样，我需要自己建立，这里就涉及知识图谱的抽象构造问题。幸亏公司数据大多数，是基于关系数据库进行存储构建的，也就从一定程度上减轻了我自己建立的烦恼。但是对数据的清洗就不是我目前可以简单处理的了。不过，也有一个另类解决方案，就是直接使用别人的结果。可惜多数知识图谱的对于商业应用还是需要授权的，这也就造成对于容易爬取的结构数据，就结合多方来源来构造和校准，单一来源的就以公司数据为准。但这个人工校准过程，我觉得在未来是必不可少的，人力对这些实际的杂乱数据的处理，还是远优于机器的。

由于这样的便利，我主要需要处理的数据从语义就转化为了已有关系的关系数据。到这里，我突然想到了腾讯知识图谱的构建工具，所需要的就是关系数据，然后人工添加关系，再基于关系数据库建立知识图谱。这与我现在面临的问题类似，事实上就是在既有的知识保存状态下找到一个简单的处理模式。很取巧，也没什么几乎含量。

回到我的问题上，我们需要建立的是音视频包含用户行为的知识图谱。这比大多数即有开源知识图谱都要更为针对性一些，主要针对性体现在两个方面，对象强调与面向强调。对象只针对音视频和与之有关的来源我司的用户。面向的也是我司自身的知识图谱需求。

在现实社会，音视频包含的范围很广泛，粗略来说，每天都有数以万计的新内容产生，我们这里所面对的，就只限于公司有版权的这些音视频，范围立刻就缩小了很多，为了扩展数据的应用范围，需要做两件事，添加近期热门的音视频内容的数据，以及热门演员的数据。对于用户行为来说，依然是对既有音视频内容的扩展。（所以，公司这个音视频知识图谱，应该叫做垂直领域的知识图谱了吧？）

所以，依照这个逻辑，人（Person）是一类节点，即可用于记录演员也可用于记录用户，他们在本质上都是Person/人。类似的，无论音乐还是电视节目，都是一类音视频产物，只是细节略有不同，他们也都可以属于各自的艺术范畴，例如：音乐、电影就可以是两个不同的范畴。也是基于这样的抽象，我们可以这样定义如下基本实体概念：

1. Person ： 人，这是基本实物之一；
2. Group ： 域/群，用来区分基本实物的抽象概念；
3. Appearance ： 音视频内容，基本实物之一，也是主体知识图谱的核心概念；
4. Genus : 类别，区别于群属的抽象，通俗来说就是标签，但不完全等同于通常在业务中的标签，类别强调属别之间的派生关系，末端类别才会关联实物，即存在自关联性；
5. Company ： 公司，某一个抽象群体的代表，例如版权所属，一般并非来源个人，而是基于公司这个群体实现授权，基本实体之一。

基于这样的基本实体，可以很容易的构造出基础关系模型：

1. derive ： 派生，主要存在于Genus之间，用于表征Genus的从属关系；
2. originate ： 属于，或叫“来自于”，主要用在基本实体与Group之间的划分；
3. natural-relationship : 自然存在的关系
   - copyright ： 版权授权，对音视频内容的授权的来源进行标记；
   - play : 饰演/出演/演奏，最基本的Person-Appearance关系之一；
   - sing ： 演唱，最基本的Person-Appearance关系之一；
   - watch ： 收看，最基本的Person-Appearance关系之一；
   - director : 导演；
   - scenarist ： 编剧；
   - compose : 作曲；
   - music ： 影视作品中的音乐；
   - producer ： 监制；
   - staff ： 其他工作人员；
   - issue ： 发行。

超出这些实体与关系的范围，就可定义为属性。对于音视频内容，还有一个有趣的问题就是时间，应该怎么处理。在这里我把与时间有关的内容统一算作是属性的内容。那顺便可与i总结 有如下属性：

* name ：名称/姓名，实体的基本属性；
* birthday ： 生日，Person的基本属性；
* gender ： 性别，Person的基本属性；
* role ： 角色；
* function ： 职能，除了工作职能（例如财务、选角导演等等）外，也表明音乐属性（间奏/插曲Interlude、主题曲themesong、结尾曲endingsong等），以及其他相关能力；
* issuedate ： 发行日期；
* premieredate : 首映日期；
* firsttime ： 首次观看时间；
* lasttime ： 最后观看时间；
* totaltimes ： 总观看次数；
* totallangs ： 总观看时长；
* from ： 来源，用户来源、原著来源等；
* duration ： 时长；
* totalepisodes ： 总集数；
* subtitle ： 副标题；
* …………

其中发行日期和首映日期只在特殊位置存在，所有他们都可以写作timedate，根据所处位置转换为首映还是发行日期。另外，上述内容中唯一不好理解的是 “Group-域/群” 这个概念。最后这里再做稍微的说明，除了可以用来区分用户和创作者之外，还可区分电影、电视剧这类内容区别。换句话说，Group是一个抽象概念，它以关系连接的内容表现自身。