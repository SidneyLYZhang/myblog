---
title: "学数学与写程序"
subtitle: "函数式编程的随感"
date: 2025-10-29T17:14:43+08:00
lastmod: 2025-10-29T17:20:43+08:00
draft: false
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
description: ""
license: ""
images: []

tags: ["Lean4", "函数式编程", "随感"]
categories: ["随感"]

featuredImage: "Wan_aicreate.png"
featuredImagePreview: "Wan_aicreate.png"

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: true
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

{{< admonition type=quote title="[_@来源_ Linux.do 帖子](https://linux.do/t/756775/20)" open=true >}}
研究haskell,lean4,用于数学抽象。
{{< /admonition >}}

在一个学什么编程语言的帖子中，看到有人提到了Lean4和Haskell，其实我个人感觉，除非是数学类专业，否则就不用先考虑Lean4了。
Lean4的确可以作为一般的编程语言来看，但整体代码管理、使用都有点不那么让人满意。
但作为数学证明的形式化验证（辅助证明器），这些就足够了。相比而言，为了了解函数式编程，
或者数学证明在程序中的实现，Haskell可能更适合，虽然其实验性也很强。
另外还有Racket（更现代的Lisp），在函数式编程的探索上，也很有实践和学习的意义。

说到Lean4，就绕不开Curry-Howard同构（简称CH同构）了。在Lean4中，CH同构是其基础，但对于大多数函数式编程语言来说，
λ-演算（lambda calculus）可能更基础一些。我个人感觉，多数函数式编程对于λ-演算的支撑更完善，最多还对柯里化有不错的支持，
但到CH同构只能说多数语言是作为类型系统的底层而非直接的语言API来使用。
另外，CH同构直接提供的更多是“数学证明的形式化验证”的便利。虽然在实际编程中CH同构也可以有很强大的能力，
但遭不住的是，它所需的数学思维也更复杂。

相对而言，λ演算，就要简单很多很多了。理解上，只要有中学的数理基础，就足够了。
基于λ演算推广来的柯里化，也并不复杂。正是因为这部分更简单，所以，多数函数式编程语言，在这方面的的支撑也跟全面。
无论Rust、Haskell、Python，只要有函数式编程能力，几乎都能完成。

我还上学那会儿（20年前了），在数学→程序这条路上（重点是数学），
对于当时大学生（比如我），可能 FORTRAN 会有更多大教授在使用，年轻些的老师更偏爱Matlab或者R，
但学校教的更多是C和Java。函数式语言多数是课堂上的介绍内容，Lisp、OCaml之类的。
这也是一种学校教育的惯性，FORTRAN优秀的大型机计算能力，是那时候复杂计算必要的基础，数值计算只是进门前的小坡；
依赖于个人电脑性能的提升，Matlab、R也能在自己的电脑上处理一些复杂的计算和验证工作。
尤其R，不单单是计算能力，更多的还是发论文的图片，从那时就已经是这样了。
但对于新手，学校课程设置上还是偏爱延续多年的C和Java。

转移到现在这个时空，一个数学人，是否需要学会编程呢？现在越来越复杂的证明，
越来越细分的专业领域，数学本身不同细分领域的跨度已经快隔阂彼此的共通性，即使基础相同，但构造出的远景依然差异万分。
所以，为了更好的往前走，可能Lean4是一个必要的选择。不是现在就学会，以后可能也要学会。
如果是应用领域的话，即数学应用，C或者FORTRAN，这两个古董，可能依然是个坎，就我了解的，
现在依然有大量的基础程序是使用这两个语言编写的。
工业领域的话，Python现在很时髦，Rust在声称要重写一切。也是值得学习的选择。

一定要一个第一选择呢？

让我推荐的话，可能还是Haskell，这是衔接数学和程序的最好的阶梯；
如果你要做数学竞赛的话，就优先学Python，毕竟python的生态更丰富，适合寻找问题的解决方案；
如果你在大学前，已经熟练某些编程语言，那么，不如试试老树新枝的Racket，
或者直接开始Lean4的学习吧，固有认知需要打破，不要犹犹豫豫。

当然，这都只是一个已老的数学“青年”的随感。

那为什么不学Coq，而要学Lean4呢？Lean4 Mathlib的数学领域更多更统一，对于数学符号（类型）的支持更全面，这是其一；
其二，还是Coq在证明策略上要差Lean4很多，程序写起来更累，可读性也略有不足。
当然，并非不是不能学，比如你老师就是用Coq，为了毕业你也得学上，不是？

哦，对了，想起一个有趣的。Haskell Curry 与 William Alvin Howard 先后接续的完善了这个逻辑同构，
所以这个结构就叫做Curry-Howard同构。
Haskell这个编程语言也是为了纪念一下Haskell Curry的共享，而取的这个名字。
Currying（柯里化）也是为了纪念Haskell Curry，他把链式单参函数的数学逻辑推广到λ演算中。
Curry简直就是最早的函数式程序语言奠基者了吧？或者，函数式编程的大魔王？