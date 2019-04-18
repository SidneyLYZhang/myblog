---
title: "对python的一些吐槽"
date: 2019-04-18T09:56:21+08:00
lastmod: 2019-04-18T09:56:21+08:00
draft: false
keywords: ["Python", "吐槽", "click", "pampy"]
description: "这是真吐槽de吐槽现场……"
tags: ["吐槽", "Python", "编程"]
categories: ["click","python","入门","教程"]
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

## 说在前面

工作就是要帮助别人，也帮助自己。

所以，做一些常用到的工具，帮助大家能够快速完成工作，也是必须的啊……但是我比较懒，很多事都是开始了，但拖拖拉拉进展缓慢……现在也只弄好了一小部分……实在被自己搞得很无奈啊……

就纠结纠结着很久，做了这么一个初始化的python命令行工具，其实也没有做完……

## 数据准确性的判断

这个工具是为了进行数据一致性检验的，一般情况下，数据一致性的需要是在于医学领域上的使用，为了确认不同病例、案例、医学结果的一致性而存在。但，在我看来，除了这个用处外，还有一个用处，就是对任意数据的有效性进行验证。

多数时候，数据的有效性、是要给予其他手段来进行辅助实现验证的，例如使用验证码、区块链等等。但是在缺乏这些手段的时候，还需要一定的对数据的确认，就可以使用一致性检验。

之所以这样使用，主要是进行了一个初始假设：“不同的局部数据都可以在一定程度上反映总体的特征”。

面对数据的一般情况，都是不同情况的抽样，有的抽样比较大，有的比较小，但都是真实的局部。所以，任意两个抽样只要是相似的，就可以认为是同一个真实样本的反映了。基于这样一个不算缜密的逻辑，对实际数据的准确性进行验证，最后的结果其实更多是寻求心理安慰的。

那为什么还要这么做呢？

只是因为有需求啊。大多数情况下，一致性检验只是最粗糙的数据准确的判断，稍微严密一点的做法就是检验是否同分布。但这样其实也并不是完全合乎逻辑的，就算用上贝叶斯方法对多种先验结果进行验证也一样缺乏完善的可确定性，毕竟统计就是在不确定中寻找不确定的确定模式而已，模式相同也不一定说明真实性是相同的。

所以，面对这个需求，最佳的方案还是区块链或者附带验证码。可没有这些的时候，只好退而求其次，粗糙的试试看吧。就是这么回事了。

## 一致性检验

一致性检验，方法还是很丰富的，Kappa检验、Kendall's W检验等等，这两个我在命令行工具里已经实现了。常见可用于这一检验的方法有两类，参数类和非参数类。

参数类，就是分布比较。常见的有：

1. KL散度：就是相对熵。deep learning中应用广泛。多用于对分布近似处理后，判断损失了多少信息。其实相似的，用KL散度来度量两个样本之间的差异是否同分布，也是可以的。但是这样考虑会放大一些统计偏差（误差）。
2. f-散度：KL散度算是f散度的一种情况。可以处理更多情况，当然，基于不同定义的f散度，各比较的差异其实就是度量定义的不同了。例如Hellinger distance，也是另一种的f散度。
   f-散度定义于：
   $$
   D_{f}(P\parallel Q)\equiv \int _{{\Omega }}f\left({\frac  {dP}{dQ}}\right)\,dQ
   $$
3. Bregman 散度：或者叫Bregman失真函数。比较的重点在于噪声的程度或者说是相异的程度。这个也更适合已经有一组真实数据的情况下进行比较，当没有真实数据时，就很难判断这个噪声是否正常了。
4. Wasserstein distance：这个在机器学习里用的很多了。就和KL散度一样。但是不同点在于Wasserstein距离直接比较的是分布情况不是基于分布的数据情况。使用程度要好于f散度。
   Wasserstein距离定义于：
   $$
   W_{{p}}(\mu ,\nu ):=\left(\inf _{{\gamma \in \Gamma (\mu ,\nu )}}\int _{{M\times M}}d(x,y)^{{p}}\,{\mathrm  {d}}\gamma (x,y)\right)^{{1/p}}
   $$

非参数类：

1. Kappa Test：一般指的也就是Cohen's Kappa检验，多用于比较2个被检情况的相似性。但是单纯的这个方法是有问题的，因为只能比较两组数据之间的情况。
2. Fleiss' kappa：就是Cohen版本的增强版，终于可以比较多组了。但复杂程度也增加啦。
3. Kendall’s Coefficient of Concordance (W)：就是Kendall's W 检验。
   KW检验定义于：
   $$
   W={\frac  {12 \sum _{{i=1}}^{n}(R_{i}-{\frac  {1}{n}}\sum _{{i=1}}^{n}R_{i})^{2}}{m^{2}(n^{3}-n)}}
   $$

非参数类这类一致性检验的方法，社会科学、医学的数据检验里。用到我这个数据检验里，所检验比较的是不同抽样是否为同一事物的不同角度的真实内容。因为真实性是真实抽样本身固有的，所以，在仅四点的抽样上应存在一致性。

其实写这么多数学一样的东西，真的不觉得有什么意义，只是让人觉得这还算专业，不是侃侃胡邹。

当然也有胡邹的成分，譬如：用一致性检验数据的真实性，这本身就是有点胡邹了，虽然可以说有点理，但这个理不好说经不经得起推敲，毕竟，没有验证证明，只是简单的假设一下推断一下。各位看官看看就好，乐呵乐呵。也得等有时间了，再好好考虑如何确保结果严密。

## 对click的吐槽

下面就是开始吐槽了。

先说，click确实比python自带的argparse好用了超多超多，但是，问题也并没有减少。还是很多很多。

最让我无语的是，错误文本简直没有道理……在A处的错误，拽出错误的地方在W……这么无厘头的问题，搞得我晕头转向了好久才能修改好程序。造成这样错误出此案方式的原因，我也只好自我安慰说可能也是因为click构建参数、变量的模式吧。

那么，我遇到的最多的错误来原有：

* 参数 `argument` 不能有 help ！！！！就是说 `@click.argument('name')` 中不能添加 `help = 'help texts'` 。你知道这是什么感受吗？我写的命令，别人可能看不明白，因为help没有这个参数说明。当然不是不能解决，就是用group添加子命令来解决，但是结果就是命令行工具会变复杂。
* 命令行工具的入口只能有一个。如果你有很多子命令，就把命令都绑定到group的`def`下，然后以这个函数为入口。所以在生成互斥命令方面click还是有很多便利的。
* click对于文件路径的识别，还是需要使用`click.path` 方法，直接传入当作str处理时会出错的。

除了这些问题，click还是超级好用的。

## 对Pampy的吐槽

实话说，我一开认为Pampy是能和Haskell模式匹配相去不远的，但是没想到，差的不是一点半点啊……没有`NUM`关键词可以匹配所有数字类型，也不能针对特定人一的单一元素进行匹配，实在是纠结啊……

所以，当需要对某一个特定形式进行匹配的时候，就需要写尽所有情况。例如：要对只有两个元素的列表进行匹配，我就需要写出所有匹配：`[int, str],[int, int],[str, str],[str, int]`。不过，就像我在前面说的，如果我现在的元素类型是：`numpy.int64`，那么就无法匹配到对应的数据中了。

所以要使用pampy，需要比较全面的考虑需要遇到的匹配类型，然后精心设计匹配模式。或者把容易出现错误的内容转化为pampy可以接受的内容。

当然，pampy也支持`MyClass`这个匹配类型，但是，使用起来其实并不方便的，因为还是要转化类型，工作并没有减少。

当然，好的地方是，Pampy真的是帮助我这样超熟悉函数式编程的用户有了更多实现习惯的方式。而且代码的可读性也增加了。这些都是Pampy多带来的好处。很值得肯定的。只是这些不足还有待处理啊。

还有一个问题就是，Pampy事实上使用的人并不多。毕竟，python本身支持的函数式编程范例已经很多了。而模式匹配多数只是被当做“switch case”模式或者“if else”的一种替代。

但从我在Haskell中的经验来说，模式匹配不单单是一种替代，在可读性上也比这两类模式有长足的进展。更重要的是，模式匹配为相似模式的递归既觉提供了足够的解决前提。

说到递归，我又得吐槽一下pampy的递归长度问题，100个元素的二叉树递归就是pampy极限了。可见python本质上也并不是一个完全支持函数式编程的语言。而pampy距离好用的模式匹配也就因此还有一个巨大的距离需要跨越了。

## 一致性检验的完整程序

把完整程序贴出来其实没啥意义，就是想槽都吐了，还有些啥呢？要不凑凑字数吧。

```python
# AUTHOR    : Liangyi ZHang <ly.zhang.1985@gmail.com>
# LICENSE   : MIT License

# PACKAGES

import pandas as pd
import numpy as np
import click
from sklearn.metrics import cohen_kappa_score
from scipy.stats.distributions import chi2
from pampy import match, HEAD, TAIL

# COMMANDTOOL

@click.command()
@click.argument("filename", type = click.Path(exists = True))
@click.option('--idx/--no-idx', default = False, 
                help = 'Whether the CSV file contains a column header. The default is not included. CSV文件是否包含列标题，默认不包含。')
@click.option('--kendallsw', 'functype', flag_value = 'kendallsw', default = True,
                help = 'Consistency test using the Kendalls W method (default) . 使用Kendalls W方法进行一致性检验（默认选择）。')
@click.option('--kappa', 'functype', flag_value = 'kappa',
                help = 'Consistency test using the Kappa method. 使用Kappa方法进行一致性检验。')
def computingResult(filename, functype, idx):
    '''
        一致性检验工具，目前主要实现Cohen's Kappa检验与Kendall's W检验。\n
        由于Cohen's Kappa检验是双元检验，多于两个被检情况时会自动检验每两种情况的一致性。\n
        程序默认使用Kendall's W检验进行一致性分析。\n
        数据使用csv文件输入，每一列为一个被检情况。\n
        \t\n
        FILENAME  \t  Data file(in csv). 数据文件（csv格式）。
    '''
    mkdata = getDatas(okFP(filename), idx)
    res = match(
        functype,
        'kendallsw', kendallsW(mkdata) ,
        'kappa', kappa(mkdata)
    )
    format_echo(res)

# FUNCTIONS

def okFP(pfile):
    return(pfile.replace('\\', '/'))

def getDatas(dafile, idx):
    if idx :
        hns = 0
    else :
        hns = None
    return(pd.read_csv(dafile, header = hns, sep = ','))

def kendallsW(datas): #computing kendall's w test
    dim = datas.shape
    S = np.var(list(datas.apply(sum, axis = 0))) * dim[1]
    d = (dim[0]**2)*(dim[1]**3 - dim[1])
    w = S / d
    xx = dim[1] * (dim[0] - 1) * w
    df = dim[0] - 1
    pv = chi2.sf(xx, df)
    return({'type' : 'Kendall\'s W Test',
            'value' :  w ,
            'p-value' : pv })

def namePair(lnames):
    return(
        match(
            lnames,
            [int, int], lambda a,b : [(a, b)] ,
            [str, str], lambda a,b : [(a, b)] ,
            [str, int], lambda a,b : [(a, b)] ,
            [int, str], lambda a,b : [(a, b)] ,
            [HEAD, TAIL], lambda a,t : list(map(lambda x : (a, x), t)) + namePair(t)
        )
    )

def amend(samp, floornum):
    if samp[1] < floornum :
        return((samp[0] , 1 + samp[1]))
    else :
        return(samp)

def okType(x):
    if 'int' in str(type(x)):
        return(int(x))
    else :
        return(str(x))

def kappa(datas): #computing kappa test
    dns = list(map(lambda x : okType(x), list(datas.columns.values)))
    if datas.shape[1] > 2 :
        pns = namePair(dns)
        res = dict(zip(
            list(map(lambda x : ' - '.join([str(y) for y in x]), pns)),
            list(map(lambda x : cohen_kappa_score(datas[x[0]],datas[x[1]]), pns))
        ))
    else :
        res = cohen_kappa_score(datas[dns[0]],datas[dns[1]])
    if isinstance(res, dict) :
        res = dict(list(
            map(lambda x : amend(x, 0) , zip(res.keys(),res.values()))
        ))
    else :
        if res < 0 :
            res = 1 + res
    return({'type' : 'Kappa Test',
            'value' :  res })

def format_echo(datas):
    print('\n' + '='*100 + '\n')
    print('使用 {} 方法对数据的一致性进行检验：'.format(datas['type']))
    print('\n')
    vdl = datas['value']
    if isinstance(vdl, dict):
        for c in vdl :
            print("被检情况{0:>10s}之间的一致性为:{1:1.5f}".format(c, vdl[c]))
    else :
        print("被检情况之间的一致性为:{0:1.5f}".format(vdl))
    if 'p-value' in datas.keys() :
        print("一致性的p值为:{0:2.5f}%".format(datas['p-value']*100))
    print('\n' + '='*100 + '\n')
    print(
    '''
        程度说明：
        0%  ~ 25%： 较底的一致性水平。
        25% ~ 75%： 一般程度的一致性。
        75% ~ 100%：极高一致性。
    ''')

# MAINSPROGRAM

if __name__ == "__main__":
    computingResult()
```

上述完整程序，可以在我的Github中[下载](https://github.com/SidneyLYZhang/NewTVProgram/blob/master/pythonCLT/consistency.py)。

## 后续

准备找时间进一步在这个命令行工具中添加其他非参数类的一致性检验方法。同时把各方法的p-value的计算都添加好。

应该也会增加计算f散度或者KL散度的内容和Wasserstein距离，因为实际上我还挺常需要用到这两个计算的。虽然这两个计算多数用在deep learning中。但是，考虑两个数据的差异也可以用到这两个方法，而且有时候在一些问题上也很有效果。

*****

## 版权声明：

对于代码：

MIT License

Copyright (c) 2019 Liangyi Zhang (SidneyZhang)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

对于文章部分：

本作品（特指所撰写的文章）采用[知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议](http://creativecommons.org/licenses/by-nc-sa/4.0/) 进行许可。
如需商业使用，请通过 Email <zly@lyzhang.me> 联系我。

*****

## 参考文献：

1. https://scikit-learn.org/stable/modules/model_evaluation.html#cohen-kappa
2. http://www.real-statistics.com/reliability/kendalls-w/
3. https://stackoverflow.com/questions/11725115/p-value-from-chi-sq-test-statistic-in-python
4. https://github.com/santinic/pampy
5. Anna-Lena Popkes (February 2, 2019): "[Kullback-Leibler Divergence](http://alpopkes.com/files/kl_divergence.pdf)"
6. https://en.wikipedia.org/wiki/F-divergence
7. Olkin, I. and Pukelsheim, F. (1982). "The distance between two random vectors with given dispersion matrices". Linear Algebra Appl. 48: 257–263.
8. https://ncss-wpengine.netdna-ssl.com/wp-content/themes/ncss/pdf/Procedures/PASS/Kappa_Test_for_Agreement_Between_Two_Raters.pdf
9. http://www.real-statistics.com/reliability/fleiss-kappa/
10. Corder, G.W., Foreman, D.I. (2009).Nonparametric Statistics for Non-Statisticians: A Step-by-Step Approach Wiley, ISBN 978-0-470-45461-9