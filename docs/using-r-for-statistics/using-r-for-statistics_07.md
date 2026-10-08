# 6. 表格数据

本章将解释如何对分类变量进行总结和统计计算。分类数据通常用频数表（针对一个变量）或列联表（针对两个或多个变量）来呈现，这种表格显示了每个类别或每个类别组合中的数值个数。以此形式总结的数据被称为表格数据。

除了将分类数据总结到表格中，您可能还需要使用卡方拟合优度检验将其与假设的分布进行比较。您可能还想确定两个或多个分类变量之间是否存在关联。卡方关联检验和费希尔精确检验是两种可以帮助您实现这一目标的方法。

您将学习如何：
- 创建频数表和列联表来总结分类数据，以及如何将结果存储为表格对象
- 以百分比或边际和的形式显示您的表格
- 执行卡方拟合优度检验
- 执行卡方关联检验，以确定两个或多个分类变量之间的关系
- 对二乘二列联表执行费希尔精确关联检验
- 执行比例检验

本章使用 R 语言自带的 `warpbreaks` 和 `esoph` 数据集以及 `Titanic` 表格对象。您可以通过输入 `help(datasetname)` 来查看关于它们的更多信息。本章还使用了 `people2` 数据集（这是第 3 章介绍的 `people` 数据集的修改版）和 `apartments` 数据集。这两个数据集都随本书的下载资源提供，并在附录 C 中进行了描述。

## 频数表

频数表通过显示每个类别中的观测数量来总结一个分类变量。针对两个或多个分类变量的频数表（称为列联表或交叉表）通过显示落入每个类别组合中的观测数量来总结两个或多个分类变量之间的关系。

在 R 语言中，表格也是一种保存制表数据的对象类型。R 语言自带了一些示例表格对象，例如 `HairEyeColor` 和 `Titanic`。

### 创建表格

要创建显示因子变量每个水平观测数量的一维频数表，请使用 `table` 函数：

```
> table(people2$Eye.Color)

Blue Brown Green
  7     6     3
```

尽管 R 语言允许你从任何类型的变量创建表格，但它们仅对取值数量相对较少的因子变量才有实际意义。如果你想包含一个连续变量，请先使用 `cut` 函数将其划分为类别，如第 3 章“将连续变量划分为类别”中所述。要向表格添加一个显示缺失值数量（如果有的话）的额外列，请设置 `useNA` 参数：

```
> table(dataset$factor1, useNA="ifany")
```

要创建一个二维列联表，请将两个变量作为输入：

```
> table(people2$Eye.Color, people2$Sex)

        Male Female
  Blue     4      3
  Brown    5      1
  Green    1      2
```

类似地，你可以为三个或更多变量创建列联表：

```
> table(people2$Eye.Color, people2$Height.Cat, people2$Sex)

, ,  = Male

        Medium Short Tall
  Blue       2     0    2
  Brown      4     0    1
  Green      0     0    1

, ,  = Female

        Medium Short Tall
  Blue       3     0    0
  Brown      0     0    0
  Green      0     2    0
```

要将表格保存为表格对象，请将 `table` 函数的输出赋值给一个新的对象名称：

```
> sexeyetable <- table(people2$Eye.Color, people2$Sex)
```

创建表格对象后，你可以将进一步的函数应用于该对象，以生成与表格数据相关的输出。例如，你可以使用 `pie` 函数创建饼图来显示每种眼睛颜色的人数比例，或者使用 `summary` 函数来检验眼睛颜色与性别之间的关联。你将在本章后面部分了解其中一些函数。请保存 `sexeyetable` 对象，因为它将在后面派上用场。

### 显示表格

有一些函数可以让你以不同方式展示表格对象。其中包括 `ftable`、`prop.table` 和 `addmargins`。

`ftable` 函数以更紧凑的方式显示表格，这对于具有三个或更多维度的表格非常有用：

```
> ftable(Titanic)
                   Survived  No Yes
Class Sex    Age
1st   Male   Child            0   5
             Adult          118  57
      Female Child            0   1
             Adult            4 140
2nd   Male   Child            0  11
             Adult          154  14
      Female Child            0  13
             Adult           13  80
3rd   Male   Child           35  13
             Adult          387  75
      Female Child           17  14
             Adult           89  76
Crew  Male   Child            0   0
             Adult          670 192
      Female Child            0   0
             Adult            3  20
```

`prop.table` 函数将表格中的每个单元格计数显示为总数的比例：

```
> prop.table(sexeyetable)
          Male Female
  Blue  0.2500 0.1875
  Brown 0.3125 0.0625
  Green 0.0625 0.1250
```

要显示单元格计数占行或列总和的比例（而非占总计数的比例），可以将 `margin` 参数设置为：1 表示行，2 表示列，3 及以上表示更高维度：

```
> prop.table(sexeyetable, margin=2)
             Male    Female
  Blue  0.4000000 0.5000000
  Brown 0.5000000 0.1666667
  Green 0.1000000 0.3333333
```

要显示百分比，请将整个表格乘以 100。你也可以使用 `round` 函数来四舍五入表格中的所有数字：

```
> round(prop.table(sexeyetable)*100)
        Male Female
  Blue    25     19
  Brown   31      6
  Green    6     12
```

`addmargins` 函数显示包含行和列总计的表格：

```
> addmargins(sexeyetable)
        Male Female Sum
  Blue     4      3   7
  Brown    5      1   6
  Green    1      2   3
  Sum     10      6  16
```

要仅向表格的一个维度添加边际和，可以将 `margin` 参数设置为：1 表示行，2 表示列，3 及以上表示更高维度：

```
> addmargins(sexeyetable, margin=1)
        Male Female
  Blue     4      3
  Brown    5      1
  Green    1      2
  Sum     10      6
```

### 从计数数据创建表格

到目前为止，你是通过将数据集中的每一行计为一个观测值来创建表格的。然而，有时你可能会遇到计数数据已经聚合的数据集，例如 `warpbreaks` 数据集（包含在 R 中）。

要从包含计数数据的数据框创建表格，请使用 `xtabs` 函数：

```
> xtabs(breaks~wool+tension, warpbreaks)
    tension
wool   L   M   H
   A 401 216 221
   B 254 259 169
```

如果数据框有多列计数数据，请将它们放在 `list` 函数中：

```
> xtabs(list(counts1, counts2) ~ factor1, dataset)
```

你可以通过将 `xtabs` 函数的输出分配给一个新对象名称来创建一个表格对象：

```
> warpbreakstable <- xtabs(breaks~wool+tension, warpbreaks)
```

要从表格对象创建计数数据的数据框，请使用 `as.data.frame` 函数：

```
> as.data.frame(Titanic)
   Class    Sex   Age Survived Freq
1    1st   Male Child       No    0
2    2nd   Male Child       No    0
3    3rd   Male Child       No   35
4   Crew   Male Child       No    0
5    1st Female Child       No    0
6    2nd Female Child       No    0
7    3rd Female Child       No   17
8   Crew Female Child       No    0
9    1st   Male Adult       No  118
10   2nd   Male Adult       No  154
11   3rd   Male Adult       No  387
12  Crew   Male Adult       No  670
13   1st Female Adult       No    4
14   2nd Female Adult       No   13
15   3rd Female Adult       No   89
16  Crew Female Adult       No    3
17   1st   Male Child      Yes    5
18   2nd   Male Child      Yes   11
19   3rd   Male Child      Yes   13
20  Crew   Male Child      Yes    0
21   1st Female Child      Yes    1
22   2nd Female Child      Yes   13
23   3rd Female Child      Yes   14
24  Crew Female Child      Yes    0
25   1st   Male Adult      Yes   57
26   2nd   Male Adult      Yes   14
27   3rd   Male Adult      Yes   75
28  Crew   Male Adult      Yes  192
29   1st Female Adult      Yes  140
30   2nd Female Adult      Yes   80
31   3rd Female Adult      Yes   76
32  Crew Female Adult      Yes   20
```

### 直接创建表格

有时你可能根本没有数据集，只有一个计数表。为了能够执行诸如卡方检验（本章后面会介绍）之类的分析，你需要将数据作为表格对象输入到 R 中。

你可以使用 `as.table` 函数创建一个一维表格对象。如下所示，输入每个类别的计数：

```
> table1D <- as.table(c(5, 21, 17, 3, 1))
```

当你查看表格时，可以看到 R 已为类别提供了默认名称 A、B 等：

```
> table1D
A  B  C  D  E
5 21 17  3  1
```

要使用正确的类别名称覆盖默认名称，请使用 `row.names` 函数：

```
> row.names(table1D) <- c("类别 1", "类别 2", "类别 3", "类别 4", "类别 5")
```

你也可以用类似的方式将二维表格输入到 R 中。假设你想要输入表 6-1 中显示的数据。

表 6-1. 接受活性治疗或对照治疗后，患病与未患病受试者的数量

| | | 疾病状态 |
| --- | --- | --- |
| | | 已痊愈 | 未痊愈 |
| 组别 | 活性治疗组 | 15 | 11 |
| | 对照组 | 10 | 15 |

要创建一个二维表格，你首先需要创建一个矩阵对象，然后将其转换为表格对象。矩阵是一种保存矩形数据网格的对象类型，其中所有数据都是同一类型（字符型或数字型）。它有点像向量，但具有两个维度。要创建矩阵，请使用 `matrix` 函数：

```
> matrix1 <- matrix(c(15, 10, 11, 15), nrow=2)
```

这将创建如下所示的矩阵：

```
> matrix1
     [,1] [,2]
[1,]   15   11
[2,]   10   15
```

如你所见，R 已将值列表排列成一个二乘二矩阵。R 从矩阵的第一列开始，从上到下填充，然后移动到第二列，依此类推。`nrow` 参数告诉 R 矩阵应该有多少行，所需的列数是自动计算的。或者，你可以使用 `ncol` 参数来指定列数。

要为表格的维度和水平提供适当的标签，请使用 `dimnames` 参数：

```
> matrix1 <- matrix(c(15, 9, 11, 17), nrow=2, dimnames=list(组别=c("活性", "对照"), 疾病=c("已痊愈", "未痊愈")))
```

一旦你创建了矩阵，就可以使用 `as.table` 函数将其转换为表格对象：

```
> treattable <- as.table(matrix1)
```

或者，你可以通过将 `matrix` 函数嵌套在 `as.table` 函数中，一步创建表格：

```
> treattable <- as.table(matrix(c(15, 9, 11, 16), nrow=2, dimnames=list(组别=c("活性", "对照"), 疾病=c("已痊愈", "未痊愈"))))
```

#### 卡方拟合优度检验

卡方拟合优度检验（也称为皮尔逊卡方检验或χ²检验）允许你将分类数据与理论分布进行比较。其原假设是数据遵循指定的分布，备择假设是数据不遵循该分布。该检验仅在数据量充足时适用，通常定义为每个类别（在原假设下）的期望频数至少为 5。

该检验不应与卡方关联性检验（见本章下一节）混淆，后者用于判断两个或多个分类变量是否相关。卡方拟合优度检验和卡方关联性检验都是皮尔逊卡方检验的形式，但它们针对数据回答了不同的问题。为了说明区别，假设你记录了一系列六面骰子的投掷结果，并希望通过这些数据判断骰子是否公平，此时应使用卡方拟合优度检验。该检验的原假设是六个面被掷出的概率相等，均为 1/6。然而，如果你同时记录了掷骰子的人名和骰子结果，并想判断掷骰结果与掷骰人之间是否存在关系，则应使用卡方关联性检验。

**注意**

有关假设检验的更多详细信息，请参阅第 10 章。

你可以使用 `chisq.test` 函数执行卡方拟合优度检验。如果你有一个一维表对象，可以针对均匀分布（即原假设所有类别出现的概率相等）进行检验，如下所示：

```
> chisq.test(tableobject)
```

如果你使用原始数据，请将 `table` 函数嵌套在 `chisq.test` 函数中：

```
> chisq.test(table(dataset$factor1))
```

若要针对不同的理论分布检验数据，请使用 `p` 参数给出原假设下期望的相对频率列表。你必须提供与表中水平数量相同的相对频率，且它们的总和必须为 1。

例如，要检验以下假设：10%的总体属于第一类，40%属于第二类，40%属于第三类，10%属于第四类，请使用以下命令：

```
> chisq.test(tableobject, p=c(0.1, 0.4, 0.4, 0.1))
```

**示例 6-1. 使用公寓数据进行卡方拟合优度检验**

考虑 `apartments` 数据集（可从本书的下载资源中获得，并在附录 C 中描述），该数据集提供了 2012 年 10 月英国某个特定区域中 39 套一居室公寓的出租详情。其中 `Price.Cat` 变量表示公寓的租金价格类别。

要创建一个显示各价格类别中公寓数量的表格，请使用以下命令：

```
> table(apartments$Price.Cat)
```

```
£500-550 £551-600 £601-650    £651+
5       13        8        6
```

据信，该区域 20%的一居室公寓租金低于 550 英镑，30%的公寓租金在 551 至 600 英镑之间，30%的公寓租金在 601 至 650 英镑之间，20%的公寓租金高于 650 英镑。

假设你想使用卡方拟合优度检验来判断数据是否与假设的价格分布一致。该检验的原假设是所述的价格分布是正确的，备择假设是它不正确。显著性水平设为 0.05。

要执行该检验，请使用以下命令：
```

```
> chisq.test(table(apartments$Price.Cat), p=c(0.2, 0.3, 0.3, 0.2))
```

这将输出以下结果：

```
Chi-squared test for given probabilities

data:  table(apartments$Price.Cat)
X-squared = 1.8021, df = 3, p-value = 0.6145
```

p 值为 0.6145，不小于 0.05 的显著性水平，因此我们无法拒绝原假设。这意味着数据与假设的价格分布一致。

## 分类变量之间的关联性检验

正如我们可以使用协方差和相关性等统计量来考察连续变量之间的关联性一样，也有方法可以帮助你判断分类变量之间是否存在关联。其中两种方法是卡方关联性检验和费希尔精确检验。这些检验回答相同的问题，但适用于不同的情况。

卡方关联性检验用于检验两个或多个变量之间的关联性，并且无论每个变量有多少个水平都可以使用。然而，该检验仅在数据量充足时适用。

相比之下，费希尔精确检验只能用于考察两个各有两个水平的分类变量之间的关联性。然而，即使在数据量非常少的情况下，它也是适用的。

以下部分将解释如何在 R 中执行这些检验。

### 关联性卡方检验

关联性卡方检验（有时也称为独立性卡方检验）有助于确定两个或多个分类变量之间是否存在关联。该检验的原假设是变量之间相互独立，备择假设是变量之间并不独立（即至少有两个变量存在关联）。该检验仅在数据量充足时适用，通常定义为：所有表格单元格在（原假设下）的期望计数至少为 5。

当输入一个表格对象时，`summary`函数会执行关联性卡方检验。如果你已经创建了一个表格对象，可以直接使用`summary`函数：

```
> summary(tableobject)
```

如果你拥有原始数据，可以将`table`函数嵌套在`summary`函数内使用：

```
> summary(table(dataset$var1, dataset$var2, dataset$var3))
```

### 示例 6-2. 使用 PEOPLE2 数据进行关联性卡方检验

假设你想使用关联性卡方检验，利用`people2`数据集来确定性别与眼睛颜色之间是否存在关联。

如果你在本章前面“频数表”部分创建的`sexeyetable`对象仍然存在，请使用以下命令：

```
> summary(sexeyetable)
```

或者，你也可以使用原始数据进行检验：

```
> summary(table(people2$Sex, people2$Eye.Colour))
表中案例数：16
因子数：2
所有因子独立性检验：
卡方值 = 2.2857，自由度 = 2，p 值 = 0.3189
卡方近似可能不准确
```

由于 p 值 0.3189 不小于显著性水平 0.05，我们不能拒绝性别与眼睛颜色相互独立这一原假设。这意味着没有证据表明两者之间存在关联。

“卡方近似可能不准确”这一警告告诉我们，某些单元格的期望计数小于 5。这意味着结果可能不可靠，解释时应谨慎。关于期望计数较小时的卡方检验讨论，可参见 B. S. Everitt 所著的《列联表分析》（第二版，Chapman & Hall/CRC 出版社，1992 年）第 39 页。

### 示例 6-3. 使用 ESOPH 数据进行关联性卡方检验

考虑`esoph`数据集，该数据集包含在 R 中。该数据集提供了一项食管癌病例对照研究的结果。你可以通过输入`help(esoph)`查看更多细节。变量`agegp`、`alcgp`和`tobgp`分别列出了受试者的年龄、饮酒量和吸烟习惯的类别。变量`ncases`和`ncontrols`给出了每个类别中患有和未患有食管癌的受试者人数。

假设你想使用关联性卡方检验来确定吸烟习惯与食管癌之间是否存在任何关联。该检验的原假设是吸烟习惯与食管癌相互独立，备择假设是它们之间存在关联。将使用 0.05 的显著性水平。

由于该数据集包含已表示为计数形式的数据，你可以使用`xtabs`函数创建一个表格，其中给出每个吸烟习惯类别中患有和未患有食管癌的受试者人数：

```
> tobacco <- xtabs(cbind(ncases, ncontrols) ~ tobgp, esoph)
> tobacco
tobgp      ncases ncontrols
0-9g/天      78       525
10-19        58       236
20-29        33       132
30+          31        82
```

要执行检验，请使用以下命令：

```
> summary(tobacco)
调用：xtabs(formula = cbind(ncases, ncontrols) ~ tobgp, data = esoph)
表中案例数：1175
因子数：2
所有因子独立性检验：
卡方值 = 18.363，自由度 = 3，p 值 = 0.0003702
```

由于 p 值 0.0003702 小于显著性水平 0.05，我们可以拒绝吸烟类别与食管癌相互独立这一原假设，转而支持两者存在关联的备择假设。这意味着有证据表明吸烟习惯与食管癌之间存在关系。

#### 费希尔精确检验

费希尔精确检验用于检验各有两个水平的两个分类变量之间的关联性。与关联性卡方检验不同，即使数据量非常少，也可以使用该检验。该检验的原假设是两个变量相互独立，备择假设是它们并不独立。

你可以使用`fisher.test`函数执行费希尔精确检验。你可以将该函数用于一个 2×2 的表格对象：

```
> fisher.test(tableobject)
```

你也可以使用原始数据：

```
> fisher.test(dataset$var1, dataset$var2)
```

检验结果会附带一个比值比的 95%置信区间。你可以使用`conf.level`参数更改区间大小：

```
> fisher.test(dataset$var1, dataset$var2, conf.level = 0.99)
```

### 示例 6-4. 使用 PEOPLE2 数据进行费希尔精确检验

使用`table`函数，我们可以看到在`people2`数据集中，左撇子女性多于左撇子男性：

```
> table(people2$Sex, people2$Handedness)
         左撇子 右撇子
  男性        0     9
  女性        2     2
```

假设你想执行费希尔精确检验，以确定性别与用手习惯之间是否存在任何统计上显著的关系。该检验的原假设是性别与用手习惯相互独立，备择假设是它们之间存在关联。使用 0.05 的显著性水平。

要执行检验，请使用以下命令：

```
> fisher.test(people2$Sex, people2$Handedness)
```

这将给出输出结果：

```
        计数数据的费希尔精确检验

数据：people2$Sex 和 people2$Handedness
p 值 = 0.07692
备择假设：真实比值比不等于 1
95%置信区间：
 0.000000 2.098087
样本估计值：
比值比
         0
```

由于 p 值 0.07692 不小于显著性水平 0.05，我们不能拒绝性别与用手习惯相互独立这一原假设。这意味着没有证据表明性别与用手习惯之间存在关系。

#### 比例检验

比例检验允许你比较两组或更多组观测数据中，具有特定结果或属性（即成功）的观测比例，以判断这些比例是否存在显著差异。该检验的原假设是所有组的成功概率相同，备择假设是所有组的成功概率不全相同。

该检验可应用于一个 n 行两列的列联表，其中两列分别表示成功数与失败数，n 行表示每个分组中的观测数量。

你可以使用 `prop.test` 函数执行比例检验。如果数据已保存为表格对象，可以使用以下命令：

> `prop.test(tableobject)`

为使命令生效，你的表格必须恰好有两列，且表格的行应显示你要比较的不同分组。如果你的表格方向错误，即列显示分组而行显示成功与失败，可以使用 `t` 函数来转置表格：

> `prop.test(t(tableobject))`

如果你没有表格对象，可以通过将 `table` 函数嵌套在 `prop.test` 函数内，使用原始数据执行检验：

> `prop.test(table(dataset$groups, dataset$outcome))`

或者，你也可以使用原始计数数据执行检验：

> `prop.test(c(success1, success2), c(n1, n2))`

其中，`success1` 和 `success2` 分别是组 1 和组 2 的成功数，`n1` 和 `n2` 分别是组 1 和组 2 的总观测数。

### 示例 6-5：比例检验

考虑表 6-1（见“直接创建表格”部分）中的数据，该表显示了接受活跃治疗或对照治疗后，疾病已解决和未解决的患者数量。如果你仍有 `treattable` 表格对象，可以查看其内容：

> `treattable`
```

## 总结

你应该能够创建频数表和列联表来汇总分类数据，并能根据需要以边际和或百分比形式呈现它们。你应该能够使用卡方拟合优度检验将分类数据与假设分布进行比较。你还应该能够使用卡方关联检验或 Fisher 精确检验来寻找两个分类变量之间的关联。最后，你应该能够使用检验来比较两个或多个比例。

下表总结了所涵盖的最重要的命令。

| 任务 | 命令 |
| --- | --- |
| 列联表 | `table(dataset$factor1, dataset$factor2)` |
| 紧凑表 | `ftable(tableobject)` |
| 比例表 | `prop.table(tableobject)` |
| 含边际的表 | `addmargins(tableobject)` |
| 卡方拟合优度检验 | `chisq.test(tableobject, p=c(p1, p2, pN))` `chisq.test(table(dataset$factor1), p=c(p1, p2, pN))` |
| 卡方关联检验 | `summary(tableobject)` `summary(table(dataset$factor1, dataset$factor2))` |
| Fisher 精确检验 | `fisher.test(tableobject)` `fisher.test(dataset$factor1, dataset$factor2)` |
| 比例检验 | `prop.test(tableobject)` `prop.test(table(dataset$groups, dataset$outcome))` |

现在你已经学习了如何汇总连续变量和分类变量，我们可以进入下一章，你将学习概率分布。

---

```
         Disease
Group     Resolved Unresolved
  Active        15         11
  Control        8         17
```

要查看每个治疗组中疾病已解决和未解决的比例，请使用命令：

> `prop.table(treattable, margin=1)`

```
         Disease
Group      Resolved Unresolved
  Active  0.5769231  0.4230769
  Control 0.3200000  0.6800000
```

从输出中可以看出，治疗组中疾病已解决的受试者比例为 58%，而对照组为 32%。假设你想使用比例检验来判断这一差异是否显著，或者仅仅是随机变异的结果。将使用显著性水平 0.05。

要执行检验，请输入命令：

> `prop.test(treattable)`

或者，你也可以使用原始数据执行检验：

> `prop.test(c(15,8), c(26,25))`

这将产生以下输出：

```
2-sample test for equality of proportions with continuity correction

data:  treattable
X-squared = 1.6153, df = 1, p-value = 0.2038
alternative hypothesis: two.sided
95 percent confidence interval:
 -0.08963846  0.52348461
sample estimates:
   prop 1    prop 2
0.5769231 0.3600000
```

从输出中可以看出，p 值为 0.2038，不小于显著性水平 0.05，因此我们不能拒绝原假设，即两个治疗组中疾病已解决的患者比例相同。这意味着没有证据表明活跃治疗与对照治疗在疾病解决概率上存在差异。
