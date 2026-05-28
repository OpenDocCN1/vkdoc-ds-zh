# 页面排名

一个页面的重要性，由所有指向该页面的出站链接所在网页的重要性共同决定。例如，假设网页 `X` 的相对重要性非常高。网页 `X` 有一个指向网页 `Y` 的出站链接；因此，网页 `Y` 也会具有较高的重要性。

## 页面排名算法总结

我们来总结一下页面排名算法：

1.  将每个页面的排名初始化为 1 或某个任意数值。

2.  对于 `someSequence` 中的 `i`，执行以下操作：
    1.  计算每个入站页面贡献的分数。
    2.  计算所有贡献分数之和。
    3.  按如下公式计算更新后的页面排名：

```
更新后的页面排名 = 1 – s + s × 贡献分数之和
```

## 示例

让我给你展示一个例子。假设 `PR(a)`、`PR(b)`、`PR(c)` 和 `PR(d)` 分别是页面 `a`、`b`、`c` 和 `d` 的页面排名。页面 `d` 有两个入站链接：第一个来自页面 `a`，第二个来自页面 `b`。

现在我们需要知道一个网页对页面排名的贡献是如何计算的。其对页面排名的贡献由以下公式给出：

```
对某页面的贡献 = 贡献页面的页面排名 / 贡献页面的总出站链接数
```

在我们的例子中，网页 `a` 有三个出站链接：第一个指向页面 `b`，第二个指向页面 `c`，最后一个指向页面 `d`。因此，页面 `a` 对页面 `d` 页面排名的贡献是 `PR(a) / 3`。现在我们需要计算页面 `b` 对页面 `d` 的贡献。页面 `b` 有两个出站链接：第一个指向页面 `c`，第二个指向页面 `d`。因此，页面 `b` 的贡献是 `PR(b) / 2`。

所以，页面 `d` 的页面排名将按如下方式更新，其中 `s` 称为阻尼系数：

```
PR(d) = 1 – s + s × (PR(a)/3 + PR(b)/2)
```

**注意：** 如需了解更多关于页面排名的信息，请访问维基百科 [`https://en.wikipedia.org/wiki/PageRank`](https://en.wikipedia.org/wiki/PageRank)。

### 工作原理

请按照本节中的步骤来使用页面排名。

### 第 5-4-1 步：创建包含出站链接的网页嵌套列表并初始化排名

我们已经创建了一个网页及其出站链接的嵌套列表。现在我们需要初始化所有页面的页面排名。我们将它们初始化为 1：

```python
>>> pageLinks =  [['a', ['b','c','d']],
['c', ['b']],
...                             ['b', ['d','c']],
...                             ['d', ['a','c']]]
```

```python
>>> pageRanks =  [['a',1],
...               ['c',1],
...               ['b',1],
...               ['d',1]]
```

为嵌套列表创建排名后，我们需要定义运行页面排名的迭代次数。

### 第 5-4-2 步：编写一个计算贡献的函数

我们将编写一个函数，它接收两个参数。函数的第一个参数是网页 URI 列表，这些 URI 提供了指向其他网页的出站链接。第二个参数是通过第一个参数中的出站链接访问到的网页的排名。该函数将返回对第一个参数中所有网页的贡献：

```python
>>> def rankContribution(uris, rank):
...     numberOfUris = len(uris)
...     rankContribution = float(rank) / numberOfUris
...     newrank =[]
...     for uri in uris:
...       newrank.append((uri, rankContribution))
...     return newrank
```

这段代码非常清晰。我们的函数 `rankContribution()` 将返回 URI 列表（第一个变量）对页面排名的贡献。该函数首先计算列表 `URIs` 中元素的数量；然后计算对给定 URIs 的排名贡献。最后，对于每个 URI，返回其贡献的排名。

### 第 5-4-3 步：创建配对 RDD

首先，让我们创建链接数据的配对 RDD：

```python
>>> pageLinksRDD  = sc.parallelize(pageLinks, 2)
>>> pageLinksRDD.collect()
```

输出如下：

```python
[['a', ['b', 'c', 'd']],
['c', ['b']],
['b', ['d', 'c']],
['d', ['a', 'c']]]
```

然后，我们创建排名数据的配对 RDD：

```python
>>> pageRanksRDD  = sc.parallelize(pageRanks, 2)
>>> pageRanksRDD.collect()
```

输出如下：

```python
[['a', 1],
['c', 1],
['b', 1],
['d', 1]]
```

### 第 5-4-4 步：创建更新页面排名的循环

现在是时候编写最终循环来更新每个页面的页面排名了：

```python
>>>numIter = 20
>>>s = 0.85
```

我们定义了迭代次数和阻尼系数 `s`。

```python
>>> for i in range(numIter):
...     linksRank = pageLinksRDD.join(pageRanksRDD)
...     contributedRDD = linksRank.flatMap(lambda x : rankContribution(x[1][0],x[1][1]))
...     sumRanks = contributedRDD.reduceByKey(lambda v1,v2 : v1+v2)
...     pageRanksRDD = sumRanks.map(lambda x : (x[0],(1-s)+s*x[1]))
...
```

让我们分析一下我们的 `for` 循环。首先，我们通过内连接将 `pageLinksRDD` 与 `pageRanksRDD` 连接起来。然后，`for` 块的第二行使用我们之前定义的 `rankContribution()` 函数计算页面排名的贡献。

在下一行中，我们汇总所有获得的贡献。在最后一行中，我们使用 `map()` 函数更新每个网页的排名。现在是时候查看结果了：

```python
>>> pageRanksRDD.collect()
```

输出如下：

```python
[('b', 1.3572437951279825),
('c', 1.2463781024360086),
('d', 0.8746512999550939),
('a', 0.521726802480915)]
```

最终，我们得到了每个页面的估计页面排名。