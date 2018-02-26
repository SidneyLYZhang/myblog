---
title: "My First Post"
date: 2018-02-25T20:50:34+08:00
keywords: ["testing"]
description: "First ~ Hello World"
tags: ["First Time", "Testing"]
categories: ["无"]
author: "Sidney Zhang"
comment: false
toc: true
autoCollapseToc: false
postMetaInFooter: false
contentCopyright: false
reward: false
mathjax: true
mathjaxEnableSingleDollar: true
---

# Hello World

这是测试，也是Hugo的Hello World～

我应该会在这里测试很多东西。比如标注、数学公式、音乐、视频、代码块、图片。其他的我用的不多，这些如果ok了，其他也就ok了，没有别的问题，就只在这里测试这些了。

<!--more-->

### 数学公式

测试Latex公式，标准Markdown是没有支持的，但很多Markdown方言有支持。希望这里ok～

$$x_{n-1}^2 + y_{n-1} = f(x_n)$$

### 测试插入音乐

{{% music "518896134" %}}

以模式：`{{% music "28196554" %}}`生成。

### 测试插入视频

只能插入Yuotube？？

{{% youtube "Lln8jwTAJpg" %}}

类似插入音乐的模式：`{{% youtube "Lln8jwTAJpg" %}}`生成。

### 测试代码块

```Haskell
xdo :: [Integer] -> Integer
xdo [] = 0
xdo (1:xt) = xdo xt
xdo (x:xt) | isPrime x = 1 + xdo xt
	   | otherwise = xdo xt
	   where isPrime :: Integer -> Bool
isPrime x = head (filter (\y -> mod x y == 0) [2..x]) == x
```

上面是一个Haskell的代码。

### 测试插入图片


