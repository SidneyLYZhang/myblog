---
title: "Python中用 Dask 处理数据"
subtitle: ""
date: 2020-01-13T11:29:21+08:00
lastmod: 2020-01-13T11:29:21+08:00
draft: false
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
description: "Python Dask的初步使用者指南。"
resources:
- name: "featured-image"
  src: "featured-image.jpg"

tags: ["python", "Dask", "Data Science"]
categories: ["notes"]

lightgallery: true
---

<!--more-->

### 启

一直用Pandas做数据处理，因为我一直没有遇到数据量极其巨大的情况。所以也就没有在意Pandas对于内存的侵占。直到最近……

最近遇到的事情都很无语，选取的数据集数据量越来越大，随随便便一个数据文件就要1.5G……公司这台垃圾电脑只有2G内存，都不够加载数据的……
所以，我不得不开始大幅度重构我的分析底层结构。

第一个需要重构的就是数据处理的基础内容——读取和清洗部分。
也就是把pandas相关的处理都换成Dask的，不过我觉得，还是要同时保留部分pandas的代码，以备不时之需，毕竟数据量激增只是最近的事情。
对后续变化的估计还比较模糊，多留一些也是个好事。

另外，我在后面翻译了Dask官方提供的最佳实践，只翻译了Dask通用部分和Dask DataFrame部分，两篇。有需要的可以直接往后看。前面只是我个人的一些笔记、想法等等内容。

当然，我在知道Dask之前，自己尝试了写了一个处理巨量数据的方法，效果极差啊，会贻笑大方之家的……所以就不提这个了。总之，这里写到的内容如有不妥，还要恳请大家多读提出意见，以便改进。

### 承 ： Dask入门

[Dask](https://dask.org/)是我在DogeDoge上搜索“large csv python”的时候从 [Dask – A better way to work with large CSV files in Python](https://pythondata.com/dask-large-csv-python/) 这篇文章中了解到的。Dask官方自己对自己的描述也很清晰地说明了Dask的效能。

> _Dask是一个十分灵活的用于Python的并行计算库。_
> 
> _Dask is a flexible library for parallel computing in Python._

Dask的文档： [Dask](https://docs.dask.org/en/latest/)

#### （1）安装

我选择“pip”进行安装，由于电脑上我安装了四个版本的python（2.7，3.6，3.7，3.8），所以安装的时候略微麻烦一点：

```powershell
PS >  py -3.6 -m pip install -i https://pypi.tuna.tsinghua.edu.cn/simple "dask[complete]"
```

由于Dask包含很多内容，单纯安装（`pip install Dask`）只会安装核心包，对我的应用需求而言，需要更多一些。所以选择全部安装。

#### （2）简单使用

我主要需要使用Dask DataFrame的部分功能，所以，需要好好练习使用的，主要集中在这部分，同时涉及一些Dask Array的内容。

```python
# 加载包
import dask.dataframe as dd
# 设定必要数据
cnames = ["userid","program","name","type","x","y","lastdate"] #数据列名称
cfuns = {
    "name" : 'category',
    "type" : 'category'
} # 列数据类型
#加载数据
y = dd.read_csv("test_1800w.csv", sep = ';', names = cnames, dtype = cfuns, parse_dates = ['lastdate'])
```

使用 `y.head()` 就可以查看数据得前5行：

```python
           userid  program name type        x   y             lastdate
0  19961001121987  3500675  NaN  NaN  1286.05  64  2019-11-16 17:38:23
1  19951002102741  3511631  NaN  NaN     0.08   1  2019-11-09 15:15:27
2  19961001121987  3497986  NaN  NaN    13.00   1  2019-11-12 22:19:14
3  19961001121987  3496198  NaN  NaN     5.93   1  2019-11-16 18:15:27
4  19961001121987  3497603  NaN  NaN    13.00   1  2019-11-20 21:56:18
```

在数据读取并初始化方面，Dask DataFrame要比Pandas DataFrame效率高很多。当然，这只是针对大型数据集而言，例如我这个“test_1800w.csv”文件就有1800万行数据，
即使在32G内存、i7处理器的台式机（我家里的机器）上，这个数据集也需要很久才能加载完成。

增加列数据的方法，与Pandas是一样的：

```python
>>> y['hour'] = y.lastdate.dt.hour
```

Pandas中我用的很多的两个方法是，dataframe.groupby和dataframe.pivot_table。对应于dask DataFrame一样有这两个内置函数。（舒心了呢，不用着急自己写相应算法了。）

对于groupby方法，一般的使用要简单很多，例如针对某列元素分组对剩余中某列加和：

```python
>>> y.groupby('type').x.sum()
Dask Series Structure:
npartitions=1
    int64
      ...
Name: x, dtype: int64
Dask Name: series-groupby-sum-agg, 105 tasks
```

到这里就可以看到Dask的一个很厉害的特性：惰性计算。我使用Haskell，就是因为喜欢haskell的函数式编程和惰性计算。这对于我这样的人来说，可以更好的按照自己的习惯来写程序，
并把把程序适应到更多的场景下。python一般是不支持惰性计算的，函数式编程的支持更小众一些。尤其是在超大数据集上计算的时候，Dask提供的惰性计算，真的是帮我节省了很多功夫。
也就是当我们需要整理数据集的时候，添加列、修改列元素类型，总之不管中间怎么修改，只在调用`compute()`的时候才会最终计算结果，这对大数据量的帮助要强很多很多。

准备实际取得计算结果，只需要再添加上`.compute()`这个计算方法就可以： `y.groupby('type').x.sum().compute()` 。

由于Dask是并行数据处理的预建程序集合，所以在Dask Dataframe中使用pandas DataFrame的`agg`函数还是有些区别的。

对于常用的`sum`，`max`，`min`，`count`，`mean`这几个常见函数，我们可以用在pandas中一样的操作：

```python
>>> y.groupby('type').agg({'x':sum,'y':max})
```

如果需要想pandas中那样使用自定义函数的话，就需要使用Dask提供的Aggregate功能了，其实也并不复杂，只是把运算过程分解，并提前定义。
核心方法是用于自定义函数的`dd.Aggregation()`方法。

```python
>>> unique_count = dd.Aggregation(
    name = "unique_count",
    chunk = lambda x : x.unique(),
    agg = lambda s : len(s)
)
>>> y.groupby('type').agg({'userid': unique_count})
```

在这个例子里建立了一个“unique_count”的定制方法，计算每个类型中有多少不同的用户ID。对于`dd.Aggregation`的使用，我觉得还是需要稍微记录一下：

> name : str
>
>   聚合的名称，它必须是唯一的，中间结果将使用这个名称进行标识。
> 
> chunk : callable
> 
>   每一个Groupby分成的数据块都会调用的函数。它可以返回一个Series数据或者元组数据。并且所对应的索引必须与分块数据一致。
> 
> agg : callable
> 
>   对每一个chunk运算后的结果进行聚合计算的函数。相同的情况就是分组依然不会变。chunk计算步骤返回的是元组的话，这步聚合函数的运算得变量需要对应元组的每一个元素。
> 
> finalize : callable
> 
>   一个可选得最终计算步骤，依然是对聚合结果进行计算。

官方给了一个四个参数都包含的例子，计算平均值的方法：

```python
>>> custom_mean = dd.Aggregation(
        name = 'custom_mean',
        chunk = lambda s: (s.count(), s.sum()),
        agg = lambda count, sum: (count.sum(), sum.sum()),
        finalize = lambda count, sum: sum / count
    )
>>> df.groupby('g').agg(custom_mean)
```

Dask Dataframe在索引数据的时候，和Pandas DataFrame有一点不一样，一般对于一个Pandas DataFrame的数据`test`，想索引其中“name”列，可以简单使用`test.name`进行索引。
但是Dask中这会有一些问题：

```python
>>> y.type
Dask Series Structure:
npartitions=25
    category[unknown]
                  ...
          ...
                  ...
                  ...
Name: type, dtype: category
Dask Name: getitem, 100 tasks
```

也就是Dask在单独索引的时候也依然是惰性计算的，所以需要实际 __索引到__ 数据的时候，还是需要`compute()`：

```python
>>> y.type.compute()
0         电视剧
1         电视剧
2         电视剧
3         电视剧
4         电视剧
         ...
387473     购物
387474     购物
387475     购物
387476     购物
387477     购物
Name: progtype, Length: 18590294, dtype: category
Categories (30, object): [电视剧, 新闻, 曲艺, 电影, ..., 时尚, 法治, 科教, 购物]
```

另外，Dask Dataframe也支持Pandas其他索引方式，例如：`y.loc`、`y.iloc`、`y[['name','x']]`等。

#### （3）需要特别注意的地方

`dask.dataframe.pivot_table`方法与Pandas Dataframe在使用上并没有什么特别，虽然在函数的变量上没Pandas那么多，不过总体上使用感受要更简洁一些。

```python

```

但是，如果你真的这么运行，就会出现错误。类似的还有`dd.get_dummies`这个函数。

```python
>>> dd.get_dummies(y)

------------------------------------------------------------------------------------------------------------------------

<stdin> 1 <module>


reshape.py 157 get_dummies
raise NotImplementedError(unknown_cat_msg)

NotImplementedError:
`get_dummies` with unknown categories is not supported. Please use `column.cat.as_known()` or `df.categorize()` beforeha
nd to ensure known categories

>>> dd.pivot_table(y, index='type', columns='name', values='x', aggfunc='mean')

------------------------------------------------------------------------------------------------------------------------

<stdin> 1 <module>


reshape.py 227 pivot_table
"'columns' must have known categories. Please use "

ValueError:
'columns' must have known categories. Please use `df[columns].cat.as_known()` beforehand to ensure known categories

```

直接运行所获得的错误，其实也是Dask的一个小坑，如果你不注意的话……就是，对于重塑DataFrame（[Reshape DataFrame](https://docs.dask.org/en/latest/dataframe-api.html#reshape-dataframes)）
的方法，都需要Categories类型，而且是 __Known__ 模式的categories类型。

重塑数据的方法有三个：

1. dd.get_dummies()
2. dd.pivot_table()
3. dd.melt()

对于Dask来说，它所识别的Categories有两种，一个是unknown，另一个就是known。区别主要在于内部 `_meta` 数据上，“known categories”可以满足静态计算需要，但是unknown的就不可以了。

确定列Categories数据是否已知，可以使用`数据.列名.cat.known`来确定，比如：

```python
>>> y.name.cat.known
False
```

现在来说，我们的“y”数据里的“name”列并不是一个已知的Categories类型。

要消除未知，Dask提供了两个方式，一个是错误信息里就提到的方法：`df[columns].cat.as_known()`，这个方法一次只能对一列数据进行操作。另一个要厉害很多，一次对所有Categories类型列进行Known处理，即`df.categorize()`，不过这个方法会生成一个新的Dask Dadaframe数据，不过由于Dask是惰性运算的，这在实际内存消耗中并不明显，当然也可以把生成的新数据复制到既有变量上。

```python
>>> y = y.categorize()
>>> y
Dask DataFrame Structure:
               userid program            name             type        x      y        lastdate   hour
npartitions=25
                int64  int64  category[known]  category[known]  float64  int64  datetime64[ns]  int64
                  ...    ...              ...              ...      ...    ...             ...    ...
...               ...    ...              ...              ...      ...    ...             ...    ...
                  ...    ...              ...              ...      ...    ...             ...    ...
                  ...    ...              ...              ...      ...    ...             ...    ...
Dask Name: categorize_block, 176 tasks
```

### 转 ： 底层重构思路

我在自己的日常使用中，多数时候，是从超大体积文件中读取数据的，还有一定使用次数是从数据库中直接提取的。

所以对于一定体量的数据我希望使用Dask处理，对于其他体量很大的数据Pandas DadaFrame也已经足够了。

那么我就需要一个更方便的Class，这个Class可以快速帮助我获取需要的数据，保存计算结果，并且可以更快速更简单的使用Dask的一些特性，并兼顾小数据量时pandas的快速效果。

__最最重要的就是，便于我自己使用。__

那么，我的一般流程就是：get data -> fill na/null -> add auxiliary data -> select data -> group by / pivot -> Machine Learning -> save results 。

这个流程中，除了machine learning部分不一定能用dask解决之外，也是因为有的时候需要使用tensoflow或者pytorch进行分析，借助`tf.contrib.learn.extract_dask_data`或者[dask的这个官方解答](https://github.com/dask/distributed/blob/177dfb891089cc872bab34fb8369d75cae13bc0a/distributed/protocol/torch.py)，就可以延续Dask再temsorflow和pytorch上的使用。

当然，Dask也预建了一些流行的机器学习方法，详细的内容可以阅读官方文档[Dask-ML](https://ml.dask.org/index.html) 来学习和使用。

总之，除了数据量较小的情况，我都是用Dask进行数据处理部分，使用还是多数导入到其他函数中进行。也是就是，我的重构内容，主要集中在读取阶段和实际输出阶段。

因为在很多地方pandas dataframe和dask dataframe是一样的，重构的过程要简单很多。

初始化方法，就是记录数据库信息或者文件地址。获取数据的方法，就是调用dask或者pandas的读取函数，其他过程类似。不一样的地方是，为了增加使用的便利性，还需要内建一些计算方法，便于groupby和pivot_table聚合使用。

### 合

下面翻译了Dask的最佳实践，以弥补我自己使用上、叙述上的不妥当和不完善的地方，以及一些说明和补充吧。

#### （1）最佳实践

[官方这篇最佳实践](https://docs.dask.org/en/latest/best-practices.html)是针对Dask使用的，已经[有人翻译](https://blog.csdn.net/m0_37712274/article/details/93379488)了。这里是我的翻译，并增加了针对Dask DataFrame的[最佳实践](https://docs.dask.org/en/latest/dataframe-best-practices.html)，另外两篇最佳实践，有时间有需求再说吧。

##### Dask的最佳实践

开始使用Dask API是很简单的，但是想使用好它还是需要一些经验的。所以这里给出一些建议的做法，还有常见问题的解决方案。

这里集中关注的是Dask API中通用的最佳实践做法，读者也可以先看针对特定API方法的最佳实践：

* [Arrays](https://docs.dask.org/en/latest/array-best-practices.html)
* [DataFrame](https://docs.dask.org/en/latest/dataframe-best-practices.html)
* [Delayed](https://docs.dask.org/en/latest/delayed-best-practices.html)

__从小处着手__

并行性带来了额外的复杂性和运算开销。有时它对于大型问题是必需的，但通常来说并非如此。在工作流程中添加并行系统（例如Dask）之前，你可能要先尝试一些其他解决方案：

- __使用更好的算法或者数据结构__：你可以先尝试Numpy、Pandas、Scikit-Learn所提供的更快速的函数。在这过程中可能需要你和相应的专家进行讨论，或者更仔细地阅读他们的文档，以找到更好的预构建算法（pre-built algorithm）。
- __更好的文件格式__：使用支持随机访问的高效二进制格式，一般就可以帮助你更简单有效的处理大于内存的数据集。可以参考[_有效存储数据_](https://docs.dask.org/en/latest/best-practices.html#store-data-efficiently)一节的内容。
- __重构代码__：使用Numba或者Cython重构代码之后，可能就并行性就不再是必需的了。或者你也可以使用这些库中所提供的多核运算支持。
- __抽样__：如果你有非常多的数据，那就有可能使用所有数据就不一定是最有优势的方法。通过智能化抽样你可以通过一个较小的子集就得到相同的结果。
- __回顾__：如果你想尝试提高慢速代码的运行速度，那么搞清为什么这段代码运行很慢就很重要了。使用一定时间来回顾你的代码，可以帮助你找到运行慢的原因。这些原因也可以用来帮助你更好的了解到，使用并行运算后对运行速度是否有改善，还是有更好的处理方法。

__使用仪表盘（Dashboard）__

Dask的仪表盘可以帮助你了解你工作程序的状态。这些信息可以帮助指引你找到更有效的解决方法。在并行和分布式的计算中存在一些新的运算消耗需要引起注意，这也会导致你的直觉不再准确。使用仪表盘就可以帮助你重新了解什么是快和慢，以及如何处理它。

阅读[Dask仪表盘文档](https://docs.dask.org/en/latest/diagnostics-distributed.html)可以了解到更多信息。

__避免使用过大的分区__

你的分块数据应该足够小，以保证很快把它加载到当前可使用的内存里。使用选择Dask DataFrame的分区大小和Dask Array的数据块大小，来控制加载速度。

Dask会尽可能多的加载数据块在一台机器上就像这台机器上有多个内核。所以，如果你有1GB的数据块和十个内核，那Dask就至少要使用10GB的内存。另外，Dask有两三倍的数据块需要进行计算处理，因为总还是有一些其他工作需要处理。

如果你有一台十核心、100G的电脑，你可能会选择1GB大小的分块，每核心十个分块可以帮助Dask有更好的空间利用率，同时避免每个任务过小。

请记住，你也需要避免分块过小。关于这个，下一节会说到更多细节。

__避免使用过大的图__

Dask的工作流程是基于 _任务_ （tasks）的。一个任务是一个Python的函数，就像`np.sum`应用到一个python对象上，就像一个Pandas dataframe或者numpy array。如果你正在使用有很多分区的Dask数据，你使用的每一个运算，比如`x+1`，都会产生很多任务，至少产生的任务数是和分区是相等。

每一个任务都会有一些计算开销。这大概介于200微秒（us）和1毫秒(ms)之间。如果你需要计算数千个任务，也还好，这也就是消耗几秒钟，也不会给你造成什么麻烦。

但是，如果你有一个超级大的图，包含数以百万的任务，那么就是一个大问题了，不仅仅是因为要计算十几分钟到数个小时，如此巨大的图还会造成任务调度上出现很多负担。

对于这个问题，你可以这样来解决：

* 创建更小的图。你可以这样做：
  * __增加数据块的大小__ ：如果你有一个1000GB的数据，并且使用10MB的数据块，你就会有10万个数据分区。任何运算都会增加至少10万个任务。但是，如果你增大数据分块的大小到1GB或者更多的GB，然后你就把计算开销降低了几个数量级。这就需要你的机器有大于1GB的内存，不过这对于超大运算负载来说也是比较典型的情况。
  * __融合运算操作__ ：Dask自己只会做很少一点这类工作，但是你可以帮助它。如果你有一个包含很多子运算的复杂计算，你可以使用类似`da.map_blocks`和`dd.map_partitions`的函数打包这些运算到一个单独Python函数中。一般，最好是转移一些管理类运算到自己的函数里。这样Dask的进程管理就不用再考虑如何更好的安排这些运算了。
  * __分解你的运算过程__ ：对于非常大型的计算需求，你可以尝试每次发送更小的数据块给Dask进行计算。例如，你正在处理一个PB级别的数据，但是你发现Dask之恶能很好的处理100TB，可能你就需要分解你的计算到十个部分，然后一次又一次的分别计算他们。

_译者注：图 Graphs ，在这里——Dask官方文档中所指代的是Dask分布计算下所有分支所组成的运算结构，这个结构可以使用树形图进行描述，同时这里图的含义也可以理解为分解超大数据的所有并行计算需求。_

__学习定制化的运算__

高级Dask子集（array、dataframe、bag）已经包含了符合Numpy和Pandas标准的API接口的通用操作。然而，很多Python运算是复杂的并且有很多高级API未包含的运算。

幸运的是，可以通过定制化的开发来解决：

* 所有子集都有`map_partitions`和`map_blocks`这两个函数，这两个函数可以允许用户定制化的函数来代替Pandas Dataframe和Numpy Array中的一些运算。因为Dask子集是基于标准Python对象进行构建的，通常无需更改预建函数，就可以很容易的在数据集的各分区之间映射自定义的函数。
  ```python
  df.map_partitions(my_custom_func)
  ```
* 更多复杂的映射函数（`map_*`）。有时自定义运算并不能完美并行（embarrassingly parallel _译者注:并行计算数据，指“同时解决很多相似的独立的任务；任务之间基本不需要调度”。详细参见[维基百科](https://en.wikipedia.org/wiki/Embarrassingly_parallel)。_ ），但是还需要很多高级的命令。例如：你可能需要获取从一个数据分区到下一个数据分区的很少字节的信息，或者你想构建一个定制化的聚合函数。Dask的各子集已经提供了很好的方法来支持这些需求。
* 对于那些更复杂的计算流程，你可以转化你的运算集合到一些独立的分块上，并且可以根据计算需要使用Dask Delayed来安排这些分块。所有dask的子集都包含`to_delayed`方法。

|                方法                       |               说明                   |
| :---------------------------------------- | :---------------------------------- |
| `map_partitions(func, \*args[, meta, …])` | 应用python函数到每一个Dataframe分区上 |
| `rolling.map_overlap(func, df, before, after, …)` | 应用一个函数到每一个数据分区，共享相邻分区的行数据。 |
| `groupby.Aggregation(name, chunk, agg[, finalize])` | 用户定义聚合方式 |
| `blockwise(func, out_ind, \*args[, name, …])` | 张量运算：标准内积与外积 |
| `map_blocks(func, \*args[, name, token, …])` | 在所有计算分块和dask array之间映射一个函数 |
| `map_overlap(x, func, depth[, boundary, trim])` | 把一个函数映射到具有重叠部分的dask array上 |
| `reduction(x, chunk, aggregate[, axis, …])` | reduction的一般方法 |

__不需要的时候就停下使用Dask__

在很多计算流程中，都需要Dask去读取一个超大容量的数据，缩减所需的数据，然后每次只迭代少量数据用于计算。对于后面数据量较小的情况，就可以不再使用Dask，使用普通python方法可能效率更高。

```python
df = dd.read_parquet("lots-of-data-*.parquet")
df = df.groupby('name').mean()  # 大量减少数据
df = df.compute()               # 后续可以使用Pandas/NumPy进行计算
```

__如果可以就持久化（persist）__

从RAM访问数据通常要比从磁盘访问要快得多。一旦你有了一个清洁状态（clean state）的数据，你就可以很好的在RAM里继续处理你的数据。清洁状态的数据一般符合以下两点特征：

1. 适合存储在内存
2. 很简洁以致你无论怎么尝试不同分析，它依然保持既有的状态

```python
df = dd.read_parquet("lots-of-data-*.parquet")
df = df.fillna(...)  # 惰性清洗数据
df = df[df.name == 'Alice']  # 缩小数据到合理的尺寸

df = df.persist()  # 触发计算，并保留在分布式的RAM中
```

请注意，这旨在分布式系统上有意义（否则，如上所述，你可能需要继续使用Dask。）

__有效存储数据__

随着计算能力的增高，你就会发现数据检索和输入输出（I/O）逐渐会占据总运算时间的大部分。另外，并行计算通常将会给你的数据存储方式增加新的限制，特别是当你准备在数据上实现默写运算需要随机读取数据块的时候。

例如：

* 对压缩文件而言，你可能会发现，不再使用gzip和bz2，而是使用一些较新的系统格式，比如lz4、snappy和Z-Standard，就可以提供更好的性能和随机读取能力。
* 对于存储格式，你可能会发现你需要那些自描述格式（self-describing formats）的文件，也就是针对随机读取、元数据存储和二进制编码进行了优化的格式，比如：parquet、ORC、Zarr、HDF5、GeoTIFF等等。
* 当在云端进行计算时，你可能会发现很多比较老旧的格式（例如HDF5）并不能很好的完成所需要的工作。
* 你可能希望使用与习惯的查询方式相吻合的方式对数据进行分区或分块。在Dask DataFrame中，这可能意味着通过选择一列数据来快速定位和链接数据。对于Dask DataFrame就可能需要选择与检索方式和算法相匹配的块大小。

__进程与线程__

如果你需要使用Numpy、Pandas、scikit-learn、numba以及其他GIL发布的库进行数学运算，那么你可能就会需要使用很多的线程。如果你处理文本数据，或者列表和字典等这类Python数据集，那么你就可能会用到很多进程。

如果你使用的是有很多线程的大型计算机（至少10各线程），你就可以把计算工作至少分成几个进程。Python可以最高处理每进程有10线程的计算任务，但是50各线程的进程就不行了。

有关线程、进程以及以及怎样在Dask中配置的更全面信息，请参阅[调度程序文档](https://docs.dask.org/en/latest/scheduling.html)。

__使用Dask加载数据__

如果你需要使用较大型的Python对象，就请使用Dask创建它们吧。我们发现的一个常见的反常模式十，人们经常在Dask之外创建大型Python对象，然后把这些对象交由Dask进行管理。这样虽然可行，但意味着Dask需要通过这些超大数据对象的元数据来操作这些超大的Python对象，而不是使用常规的Dask操作来控制结果。

这里给出一些常见的模式，以供大家使用更好的方法进行处理：

_DataFrames_

```python
# 不要这样

ddf = ... a dask dataframe ...
for fn in filenames:
    df = pandas.read_csv(fn)  # 使用Pandas本地读取数据
    ddf = ddf.append(df)            # 添加到Dask
```

```python
# 可以这样

ddf = dd.read_csv(filenames)
```

_Arrays_

```python
# 不要这样

f = h5py.File(...)
x = np.asarray(f["x"])  # 在本地使用Numpy array获取数据

x = da.from_array(x)  # 交由dask处理
```

```python
# 可以这样

f = h5py.File(...)
x = da.from_array(f["x"])  # 直接使用dask读取数据
```

_Delayed_

```python
# 不要这样

@dask.delayed
def process(a, b):
    ...

df = pandas.read_csv("some-large-file.csv")  # 本地建立超大数据集
results = []
for item in L:
    result = process(item, df)  # 每个循环中均包含这个巨大数据集df
    results.append(result)
```

```python
# 可以这样

@dask.delayed
def process(a, b):
    ...

df = dask.delayed(pandas.read_csv)("some-large-file.csv")  # 使用Dask构建数据对象
results = []
for item in L:
    result = process(item, df)  # 每个循环仅包含指针
    results.append(result)
```

__避免重复调用计算__

在单独调用`Dask.compute()`时，使用共享计算方式来计算相关结果：

```python
# 不要重复调用compute计算

df = dd.read_csv("...")
xmin = df.x.min().compute()
xmax = df.x.max().compute()
```

```python
# 同时计算多个结果

df = dd.read_csv("...")

xmin, xmax = dask.compute(df.x.min(), df.x.max())
```

这样一来，Dask只需要计算一次共享部分的运算（例如：上面调用的dd.read_csv），而不是每次计算时都重复一次。

##### Dask DataFrame的最佳实践

开始使用Dask DataFrame是很简单的，但是想使用好它还是需要一些经验的。所以这里给出一些建议的做法，还有常见问题的解决方案。

_译者注：这句话好费……与Dask API的第一句话几乎一样。_

__使用Pandas__

对于适合RAM的数据，Pandas通常比Dask DataFrame要更快速，也更易于使用。尽管“大数据工具”让后人听起来很NB，但是他们几乎总是比普通数据工具要差一点，即使这些工具也很适用于这样的应用场景。

__缩减（Reduce）数据，然后使用Pandas__

与上述类似，即使数据集异常巨大，也可能在某个时刻数据会减少到一个易于管理的程度。这时就需要切换到Pandas了。

```python
df = dd.read_parquet('my-giant-file.parquet')
df = df[df.name == 'Alice']              # 选择一个子集
result = df.groupby('id').value.mean()   # 缩减数据量
result = result.compute()                # 转化为Pandas Dataframe
result...                                # 使用Pandas继续后续运算
```

__应用于Dask DataFrame时的Pandas性能提示__

常规的Pandas性能提示（例如：避免Apply、使用矢量运算（vectorized operations）、使用分类类型等等），全部也都适用于Dask DataFrame。关于这个主题更完整全面的介绍请参阅 [Tom Augspurger](https://github.com/TomAugspurger) 所写的 [Modern Pandas](https://tomaugspurger.github.io/modern-1-intro) 。

__使用索引__

Dask DataFrame可以使用单个列数据进行排序。针对这列的运算可以是很快速的。例如，如果你的数据集是针对时间来进行排序的，那么就可以很快速的针对特定日期来选择数据，或是执行时间序列的连接操作等等。你可以通过属性 `df.known_divisions` 来查看数据是否已经进行过排序。你可以使用 `.set_index(column_name)` 方法来设定索引列。这个操作会有较大开销，所以要谨慎使用（如下所示）：

```python
df = df.set_index('timestamp')  # 设定索引以加速一些运算

df.loc['2001-01-05':'2001-01-12']  # 如果你有索引那么它的运行就会很快
df.merge(df2, left_index=True, right_index=True)  # 这个运算也会很快速
```

获取更多信息，请参阅关于 [dataframe分区](https://docs.dask.org/en/latest/dataframe-design.html#dataframe-design-partitions) 的文档。

__避免全数据清洗__

设定索引是个很重要但开销很大的运算（如上所述）。你应该不会经常使用它，但是使用后就需要把它持续化（见下文）。

很多运算方式，比如： `set_index` 和 `merge/join` ，在单机内存中使用相比在并行或者分布式系统中要难得多。特别的，重新排列数据的清洗操作会变得耗费更多的内存资源。比如，如果你的数据已通过用户ID进行排列，现在你要改为按照时间顺序排列，所有的数据分区就需要彼此交换相应的数据碎片以满足排序需求。这可能是一个高并发的运算过程，特别是对于一个集群来说。

所以，一定要设定索引，但不要经常这样做。设定索引之后，如果你是在一个集群上使用的话，你可能会想把数据持久化 `persist`：

```python
df = df.set_index('column_name')  # 不要经常这么做
```

另外， `set_index` 有一些参数，在某些条件下可以加速运算。例如，如果你知道你的数据集已经排好序了，或者已知数据集是基于哪个值进行划分的，你就可以基于这个已知条件加速 `set_index` 的运算。更多信息请参阅 [Sset_index 文档](https://docs.dask.org/en/latest/dataframe-api.html#dask.dataframe.DataFrame.set_index)

```python
df2 = df.set_index(d.timestamp, sorted=True)
```

__智能的持久化（Persist Intelligently）__

> ! 说明 NOTE
> 
> 这几节的内容仅与使用分布式系统的用户有关。

通常DataFrame的工作流程看起来是这样的：

1. 从文件加载数据
2. 将数据过滤到特定子集上
3. 清洗数据并设定索引
4. 在这个索引上进行复杂的查询

一般的流程就是，加载，过滤、清洗数据后把结果保存在内存中。之后，每一个复杂查询都可以基于内存中的数据，而不用在经过一次完整的“加载-过滤-清洗”的流程。要做到这一点，需要使用 [`client.persist`](https://distributed.dask.org/en/latest/api.html#distributed.client.Client.persist) 方法：

```python
df = dd.read_csv('s3://bucket/path/to/*.csv')
df = df[df.balance < 0]
df = client.persist(df)

df = df.set_index('timestamp')
df = client.persist(df)

>>> df.customer_id.nunique().compute()
18452844

>>> df.groupby(df.city).size().compute()
...
```

持久化是很重要的，因为Dask DataFrame通常是惰性运算的。这也是告诉集群应该开始计算到现在定义的全部计算的一种方法，然后把结果尝试放在内存中。你会得到一个新的DataFrame，这个等价于持久化前的DataFrame，但是这时指向的是一个可以马上使用的数据。旧式DataFrame依然指向惰性计算的对象：

```python
# 不要这样
client.persist(df)  # 持久化操作不会改变原始数据对象

# 推荐这样做
df = client.persist(df)  # 覆盖旧有数据对象
```

__对数据分区以减小运算开销__

Dask DataFramne可以分割成很多Pandas DataFrame。有些时候称这些为"分区(partitions)"，通常需要自己确定分区的数量。例如，可以按照需要读取的CSV文件数来来确定。然而，随着时间推移，使用过滤（filtering）和链接（joining）操作修改了数据大小时，重新考虑所需的分区数量就是明智的了。分区过多或者过少，都会造成运算上的代价。

分区应该可以很恰当的被容纳在内存中（小于一个GB）不过也不能太大。每个分区上的每个运算都需要中央调控程序处理几百微妙。如果有上千个几乎注意不到的任务，最好还是尽可能减少一些运算任务的数量。

常见的情形是，先加载了大量数据到合理大小的分区（Dask的默认值也是一个不错的选择），随后将数据集从原始状态筛选为一个较小的集合。这时，将与多小分区重组为一个大分区是明智的。可以使用 `repartition` 方法来做到这一点：

```python
df = dd.read_csv('s3://bucket/path/to/*.csv')
df = df[df.name == 'Alice']  # 仅有原数据的1/100
df = df.repartition(npartitions=df.npartitions // 100)

df = df.persist()  # 如果是在一个分布系统中的话
```

这有助于减少计算开销，并提高Pandas矢量化运算的效率。尤其是当每个数据分区约有100MB的时候更需要如此。

另外，在重新处理数据之前减少分区也很有帮助，因为重新处理数据会依赖分区数量来产生 `n log(n)` 个任务。少于100个分区的DataFrame要比有上万个分区的DataFrame更容易处理。

__链接数据（Joins）__

根据不同的情况，链接两个DataFrame可能需要消耗很大，也可能消耗很小。以下情况的运算小号很小：

1. 链接Dask DataFrame和Pandas DataFrame
2. 链接Dask DataFrame和另一个单一分区的Dask DataFrame
3. 通过索引链接Dask DataFrame

另外，以下情况也是消耗很大的：

1. 通过列而非索引来链接Dask DataFrame

包含数据混洗（shuffle）情况会消耗很大。这也还好，Dask DataFrame可以很好地完成这项工作，但这比典型的线性运算要消耗更大：

```python
dd.merge(a, pandas_df)  # 快速
dd.merge(a, b, left_index=True, right_index=True)  # 快速
dd.merge(a, b, left_index=True, right_on='id')  # 半快半慢
dd.merge(a, b, left_on='id', right_on='id')  # 慢
```

更详细的信息请参阅 [Joins](https://docs.dask.org/en/latest/dataframe-joins.html) 。

__使用Apache Parquet格式存储数据__

HDF5是需求高性能的Pandas用户的常规选择。我们鼓励Dask DataFrame用户改为使用Parquet格式[存储和加载数据](https://docs.dask.org/en/latest/dataframe-create.html)。[Apache Parquet}(https://parquet.apache.org/) 是一种柱状二进制格式，它易于拆分成多个文件（更易于加载），并且与HDF5相比处理起来也更容易有一些（从库的角度来看）。它也是其他大数据系统（例如[Apache Spark](https://spark.apache.org/)和[Apache Impala](https://impala.apache.org/)）使用的一种通用格式，所以与其他系统交互也会容易得多：

```python
df.to_parquet('path/to/my-results/')
df = dd.read_parquet('path/to/my-results/')
```

Dask支持使用Python上不同Apache Parquet格式的引擎来读取Parquet文件：

```python
df1 = dd.read_parquet('path/to/my-results/', engine='fastparquet')
df2 = dd.read_parquet('path/to/my-results/', engine='pyarrow')
```

这些库可以使用如下方式安装：

```bash
conda install fastparquet pyarrow -c conda-forge
```

[fastqarquet](https://github.com/dask/fastparquet/) 是基于Python并使用[Numba](https://numba.pydata.org/) Python-to-LLVM编译器实现的。PyArrow是[Apache Arrow项目](https://arrow.apache.org/)的一部分，它[使用C++实现的Apache Parquet](https://github.com/apache/parquet-cpp)。

#### （2）一些其他内容与说明

__Dask + Numba ：__

Dask解决的最多的还是数据读取和一部分计算问题，如果需要更复杂（也更快速的）的计算支持，例如：Nvidia CUDA和AMD ROC之类的辅助时，就可以再加上[Numba](https://numba.pydata.org/)，整体运算时间在某些情况下就会快更多，也会更加完美一些。

当然，什么时候需要使用这个组合我自己还没有一个比较好的判别标准，在运算速度不太满意时，一般才会尝试，另外，我使用Dask的时间也还比较短，以后使用时间长了或许就会更好的理清自己需要在什么时候使用这个组合。

__Python Launcher for Windows：__

由于我电脑上装好了好多版本的Python，不得不用这个程序来管理使用的版本。好像是3.3版本python之后出现的这个管理程序，如果windows种安装了很多版本，这个管理程序确实九十分必要了。

下面是`py`这个管理程序的用法。主要分为两个部分，调用起对应python版本和在调用的版本上继续运行python的两个部分。所以，我指定python版本安装Package的时候，可以统一基于`py`的方式进行安装，而不再需要关心我调用的pip是否适合当前python版本。除了代码要长一点（`py -3.6 -m pip install dask`），所以基本上，在使用中使用py调起python要更容易处理一些，它也支持所有python本身的那些命令行参数。

详细信息请参阅 [Python Launcher for Windows](http://docs.python.org/3/using/windows.html#launcher) ，下面是python launcher的一个帮助文档。

```bash
PS > py -h
Python Launcher for Windows Version 3.8.150.1013

usage:
C:\Windows\py.exe [launcher-args] [python-args] script [script-args]

Launcher arguments:

-2     : Launch the latest Python 2.x version
-3     : Launch the latest Python 3.x version
-X.Y   : Launch the specified Python version
     The above all default to 64 bit if a matching 64 bit python is present.
-X.Y-32: Launch the specified 32bit Python version
-X-32  : Launch the latest 32bit Python X version
-X.Y-64: Launch the specified 64bit Python version
-X-64  : Launch the latest 64bit Python X version
-0  --list       : List the available pythons
-0p --list-paths : List with paths

The following help text is from Python:

usage: C:\Users\1\AppData\Local\Programs\Python\Python38\python.exe [option] ... [-c cmd | -m mod | file | -] [arg] ...
Options and arguments (and corresponding environment variables):
-b     : issue warnings about str(bytes_instance), str(bytearray_instance)
         and comparing bytes/bytearray with str. (-bb: issue errors)
-B     : don't write .pyc files on import; also PYTHONDONTWRITEBYTECODE=x
-c cmd : program passed in as string (terminates option list)
-d     : debug output from parser; also PYTHONDEBUG=x
-E     : ignore PYTHON* environment variables (such as PYTHONPATH)
-h     : print this help message and exit (also --help)
-i     : inspect interactively after running script; forces a prompt even
         if stdin does not appear to be a terminal; also PYTHONINSPECT=x
-I     : isolate Python from the user's environment (implies -E and -s)
-m mod : run library module as a script (terminates option list)
-O     : remove assert and __debug__-dependent statements; add .opt-1 before
         .pyc extension; also PYTHONOPTIMIZE=x
-OO    : do -O changes and also discard docstrings; add .opt-2 before
         .pyc extension
-q     : don't print version and copyright messages on interactive startup
-s     : don't add user site directory to sys.path; also PYTHONNOUSERSITE
-S     : don't imply 'import site' on initialization
-u     : force the stdout and stderr streams to be unbuffered;
         this option has no effect on stdin; also PYTHONUNBUFFERED=x
-v     : verbose (trace import statements); also PYTHONVERBOSE=x
         can be supplied multiple times to increase verbosity
-V     : print the Python version number and exit (also --version)
         when given twice, print more information about the build
-W arg : warning control; arg is action:message:category:module:lineno
         also PYTHONWARNINGS=arg
-x     : skip first line of source, allowing use of non-Unix forms of #!cmd
-X opt : set implementation-specific option
--check-hash-based-pycs always|default|never:
    control how Python invalidates hash-based .pyc files
file   : program read from script file
-      : program read from stdin (default; interactive mode if a tty)
arg ...: arguments passed to program in sys.argv[1:]

Other environment variables:
PYTHONSTARTUP: file executed on interactive startup (no default)
PYTHONPATH   : ';'-separated list of directories prefixed to the
               default module search path.  The result is sys.path.
PYTHONHOME   : alternate <prefix> directory (or <prefix>;<exec_prefix>).
               The default module search path uses <prefix>\python{major}{minor}.
PYTHONCASEOK : ignore case in 'import' statements (Windows).
PYTHONIOENCODING: Encoding[:errors] used for stdin/stdout/stderr.
PYTHONFAULTHANDLER: dump the Python traceback on fatal errors.
PYTHONHASHSEED: if this variable is set to 'random', a random value is used
   to seed the hashes of str and bytes objects.  It can also be set to an
   integer in the range [0,4294967295] to get hash values with a
   predictable seed.
PYTHONMALLOC: set the Python memory allocators and/or install debug hooks
   on Python memory allocators. Use PYTHONMALLOC=debug to install debug
   hooks.
PYTHONCOERCECLOCALE: if this variable is set to 0, it disables the locale
   coercion behavior. Use PYTHONCOERCECLOCALE=warn to request display of
   locale coercion and locale compatibility warnings on stderr.
PYTHONBREAKPOINT: if this variable is set to 0, it disables the default
   debugger. It can be set to the callable of your debugger of choice.
PYTHONDEVMODE: enable the development mode.
PYTHONPYCACHEPREFIX: root directory for bytecode cache (pyc) files.
```

__错误信息__

我常使用“pretty_errors”，来让错误信息更好看一些。只需要安装后加载就可以（[安装指引](https://github.com/onelivesleft/PrettyErrors)）：`import pretty_errors`。当在发生错误时，就能看到这些更好看的错误提示了。