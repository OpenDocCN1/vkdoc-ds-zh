# 5. 连续变量的汇总统计量

分析数据的一个良好起点是计算一些简单的汇总统计量。本章将解释如何计算连续变量的汇总统计量。

汇总统计量主要分为两类：单变量汇总统计量和关联度量。单变量统计量是从单个变量计算得出的，包括均值和中位数等位置度量，以及方差、标准差和极差等离散程度度量。关联度量则用于概括两个变量之间的关系，包括协方差、皮尔逊相关系数和斯皮尔曼相关系数。

本章还涵盖了一些有助于对样本来源总体进行推断的方法，包括夏皮罗-威尔克检验、科尔莫戈罗夫-斯米尔诺夫检验，以及置信区间和预测区间。

你将学习如何：

*   计算单变量统计量
*   计算不同观察组别的统计量
*   计算两个变量之间的协方差、皮尔逊相关系数和斯皮尔曼相关系数
*   进行假设检验，检查相关性是否具有统计显著性
*   执行夏皮罗-威尔克检验和科尔莫戈罗夫-斯米尔诺夫检验
*   计算置信区间和预测区间

本章将使用 R 语言自带的`trees`、`iris`、`warpbreaks`和`PlantGrowth`数据集，以及本书下载资源中提供的`bottles`数据集。在开始本章之前，熟悉这些数据集会有所帮助。对于 R 自带的数据集，你可以通过输入`help(数据集名称)`来查看其更多信息。关于`bottles`数据集的更多信息，请参阅附录 C。

## 单变量统计量

要生成数据集中所有变量的汇总信息，可以使用`summary`函数。该函数会根据每个变量的类别以适当方式进行汇总。对于数值型变量，它会给出均值、中位数、极差和四分位距。对于因子型变量，它会给出每个类别的数量。如果某个变量存在任何缺失值，它还会告诉你缺失值的数量。

```
> summary(iris)
Sepal.Length     Sepal.Width    Petal.Length     Petal.Width          Species
Min.   :4.300   Min.   :2.000   Min.   :1.000   Min.   :0.100   setosa    :50
1st Qu.:5.100   1st Qu.:2.800   1st Qu.:1.600   1st Qu.:0.300   versicolor:50
Median :5.800   Median :3.000   Median :4.350   Median :1.300   virginica :50
Mean   :5.843   Mean   :3.057   Mean   :3.758   Mean   :1.199
3rd Qu.:6.400   3rd Qu.:3.300   3rd Qu.:5.100   3rd Qu.:1.800
Max.   :7.900   Max.   :4.400   Max.   :6.900   Max.   :2.500
```

要计算单个变量的某个特定统计量，可以使用表 5-1 中对应的函数。

表 5-1. 用于汇总连续变量的函数；标有星号(*)的函数输出单个数值

| 统计量 | 函数 |
| --- | --- |
| 均值* | `mean` |
| 中位数* | `median` |
| 标准差* | `sd` |
| 中位绝对偏差* | `mad` |
| 方差* | `var` |
| 最大值* | `max` |
| 最小值* | `min` |
| 四分位距* | `IQR` |
| 极差 | `range` |
| 分位数 | `quantile` |
| Tukey 五数总括 | `fivenum` |
| 总和* | `sum` |
| 乘积* | `prod` |
| 观察值数量* | `length` |

例如，要计算树木的平均高度，使用以下命令：

```
> mean(trees$Height)
[1] 76
```

如果变量中存在缺失数据值，请按下文所示将`na.rm`参数设置为`T`。这告诉 R 在计算统计量时忽略任何缺失值。否则，根据函数的不同，结果可能是另一个缺失值或错误消息：

```
> mean(dataset$variable, na.rm=T)
```

要同时计算数据集中每个变量的某个特定统计量，可以将`sapply`函数与表 5-1 中的任意统计量配合使用：

```
> sapply(trees, mean)
   Girth   Height   Volume
13.24839 76.00000 30.17097
```

同样，如果数据集中有缺失值，则将`na.rm`参数设置为`T`：

```
> sapply(dataset, mean, na.rm=T)
```

如果你的数据集中有任何变量是非数值型的，`sapply`函数的行为会不一致。例如，此命令尝试计算`iris`数据集中每个变量的最大值。R 会返回一条错误消息，因为数据集中的第五个变量是一个因子变量：

```
> sapply(iris, max)
Error in Summary.factor(c(1L, 1L, 1L, 1L, 1L, 1L, 1L, 1L, 1L, 1L, 1L,  :
max not meaningful for factors
```

要避免此问题，可以使用方括号表示法或`subset`函数（如第 1 章“数据框”和第 3 章“选择数据子集”中所述）从数据集中排除任何非数值型变量：

```
> sapply(iris[-5], max)
Sepal.Length  Sepal.Width Petal.Length  Petal.Width
         7.9          4.4          6.9          2.5
```

## 按组统计

你可能希望根据某个因子的水平对数值变量的值进行分组，并计算每组的一个统计量。有两个函数可以实现此功能，分别是`tapply`和`aggregate`。

你可以将`tapply`函数与表 5-1 中的任何统计量配合使用。例如，对于`iris`数据集，计算每个品种的`花萼宽度`的平均值：

```
> tapply(iris$Sepal.Width, iris$Species, mean)
setosa versicolor  virginica
3.428      2.770      2.974
```

如果数值变量有任何缺失数据，请将`na.rm`参数设置为`T`：

```
> tapply(dataset$variable, dataset$factor1, mean, na.rm=T)
```

你也可以通过将因子变量嵌套在`list`函数内部，按多个因子对数据进行分组。R 会为因子水平的每种组合分别计算统计量。例如，对于`warpbreaks`数据集，显示`张力`和`羊毛类型`每种组合下断裂次数的中位数：

```
> tapply(warpbreaks$breaks, list(warpbreaks$wool, warpbreaks$tension), median)
   L  M  H
A 51 21 24
B 29 28 17
```

当使用多个分组变量时，你只能使用输出单个值的统计函数（即表 5-1 中带有星号的函数）。

或者，你也可以使用`aggregate`函数按组汇总变量。使用`aggregate`函数的优点在于你可以同时汇总多个连续变量。它也可以与输出多个值的统计函数（例如`range`和`quantile`）一起使用。然而，结果的显示方式略有不同，因此是使用`tapply`还是`aggregate`取决于个人偏好。

要计算每个品种的`花萼宽度`平均值，请按如下方式使用`aggregate`函数：

```
> aggregate(Sepal.Width~Species, iris, mean)
     Species Sepal.Width
1     setosa       3.428
2 versicolor       2.770
3  virginica       2.974
```

同样，你也可以使用多个分组变量。例如，对于`warpbreaks`数据集，计算`羊毛`和`张力`每种组合下断裂次数的中位数：

```
> aggregate(breaks~wool+tension, warpbreaks, median)
  wool tension breaks
1    A       L     51
2    B       L     29
3    A       M     21
4    B       M     28
5    A       H     24
6    B       H     17
```

要同时汇总两个或更多连续变量，请按照如下方式将它们嵌套在`cbind`函数中：

# 关联度量

两个变量之间的关联是指它们之间的关系，这种关系意味着如果你知道一个变量的值，就能了解另一个变量的一些情况。正关联意味着随着一个变量值的增加，另一个变量的值也趋于增加。负关联意味着随着一个变量值的增加，另一个变量的值趋于减少。最常用的关联度量包括：

*   `协方差`：衡量两个连续变量之间线性关联的度量。协方差是尺度依赖的，意味着其值取决于变量所使用的测量单位。因此，直接解释协方差的值比较困难。两个变量之间的绝对协方差越大，关联性越强。正值表示正关联，负值表示负关联。

*   `皮尔逊相关系数`（记为 `r`）：一种尺度独立的关联度量，意味着其值不受测量单位的影响。相关系数的取值范围在 -1 到 1 之间，其中 -1 表示完全负相关，0 表示不相关，1 表示完全正相关。相关系数仅度量线性关系，因此通过散点图检查非线性关系非常重要（参见第 8 章中的“散点图”部分）。

*   `斯皮尔曼秩相关系数`：皮尔逊相关系数的一种非参数替代方法，它既能度量非线性关系也能度量线性关系。其取值范围也在 -1（完全负相关）到 1（完全正相关）之间，值为 0 表示不相关。斯皮尔曼相关系数既可用于秩次数据，也可用于连续数据。

以下小节将解释如何在 R 中计算这些关联度量中的每一种。

## 协方差

要计算两个变量之间的协方差，请使用 `cov` 函数：

```
> cov(trees$Height, trees$Volume)
[1] 62.66
```

你也可以为整个数据集创建一个协方差矩阵，该矩阵显示每对变量之间的协方差：

```
> cov(trees)
           Girth   Height    Volume
Girth   9.847914 10.38333  49.88812
Height 10.383333 40.60000  62.66000
Volume 49.888118 62.66000 270.20280
```

从输出中可以看出，树木周长和树木高度之间的协方差是 10.38。矩阵对角线上的值给出了变量的方差。例如，树木体积的方差是 270.2。

如果你的数据集有任何非数值变量，R 将显示一条错误消息：

```
> cov(iris)
Error: is.numeric(x) || is.logical(x) is not TRUE
```

要避免此问题，请使用括号索引或 `subset` 函数排除非数值变量：

```
> cov(iris[-5])
             Sepal.Length Sepal.Width Petal.Length Petal.Width
Sepal.Length    0.6856935  -0.0424340    1.2743154   0.5162707
Sepal.Width    -0.0424340   0.1899794   -0.3296564  -0.1216394
Petal.Length    1.2743154  -0.3296564    3.1162779   1.2956094
Petal.Width     0.5162707  -0.1216394    1.2956094   0.5810063
```

如果任何变量有缺失值，请将 `use` 参数设置为 `"pairwise"`，如下所示。否则，协方差矩阵也将包含缺失值：

```
> cov(dataset, use="pairwise")
```

`use` 参数的另一个有用选项是 `"complete"`。此选项完全排除在任何变量上有缺失值的观测值，而 `"pairwise"` 仅排除在给定一对变量中任何一个有缺失值的观测值。输入 `help(cov)` 可查看关于这些选项的更多详细信息。

## 皮尔逊相关系数

要计算两个变量之间的皮尔逊相关系数，请使用 `cor` 函数：

```
> cor(trees$Girth, trees$Volume)
[1] 0.9671194
```

该值非常接近 1，这表明树的周长和体积之间存在非常强的正相关关系。这意味着周长较大的树往往体积也更大。

你也可以为整个数据集创建一个相关矩阵：

```
> cor(trees)
           Girth    Height    Volume
Girth  1.0000000 0.5192801 0.9671194
Height 0.5192801 1.0000000 0.5982497
Volume 0.9671194 0.5982497 1.0000000
```

请注意，矩阵对角线上的值都等于 1，因为一个变量总是与自身完全相关。

记住要使用方括号表示法或 `subset` 函数来排除所有非数值变量，如下所示以 `iris` 数据集为例：

```
> cor(iris[-5])
```

如果任何变量存在缺失值，请将 `use` 参数设置为 `"pairwise"`：

```
> cor(dataset, use="pairwise")
```

## 斯皮尔曼秩相关系数

`cor` 函数也可以计算两个变量之间的斯皮尔曼秩相关系数。将 `method` 参数设置为 `"spearman"`：

```
> cor(trees$Girth, trees$Volume, method="spearman")
[1] 0.9547151
```

你也可以为整个数据集创建一个相关矩阵。记住要使用方括号表示法排除所有非数值变量：

```
> cor(trees, method="spearman")
           Girth    Height    Volume
Girth  1.0000000 0.4408387 0.9547151
Height 0.4408387 1.0000000 0.5787101
Volume 0.9547151 0.5787101 1.0000000
```

如果任何变量存在缺失值，请将 `use` 参数设置为 `"pairwise"`：

```
> cor(dataset$var1, dataset$var2, method="spearman", use="pairwise")
```

# 相关性的假设检验

相关性的假设检验用于确定相关性是否具有统计显著性。该检验的零假设是总体相关性等于零，这意味着变量之间没有相关性。备择假设是总体相关性不等于零，这意味着变量之间存在某种相关性。你也可以执行单侧检验，其中备择假设是总体相关性大于零（变量正相关）或总体相关性小于零（变量负相关）。

注意：有关假设检验的更多详细信息，请参见第 10 章。

你可以使用 `cor.test` 函数对两个变量之间的相关性进行检验：

```
> cor.test(dataset$var1, dataset$var2)
```

默认情况下，R 执行皮尔逊相关性的检验。如果你更倾向于检验斯皮尔曼相关性，请将 `method` 参数设置为 `"spearman"`：

```
> cor.test(dataset$var1, dataset$var2, method="spearman")
```

默认情况下，R 执行双侧检验，但你可以通过根据需要将 `alternative` 参数设置为 `"less"` 或 `"greater"` 来调整：

```
> cor.test(dataset$var1, dataset$var2, alternative="greater")
```

输出结果包含相关性估计值的 95% 置信区间。要调整此区间的大小，请使用 `conf.level` 参数：

```
> cor.test(dataset$var1, dataset$var2, conf.level=0.99)
```

## 示例 5-1. 使用树木数据集进行相关性的假设检验

假设你想要执行一个假设检验来帮助确定树的周长和体积之间的相关性是否具有统计显著性。

要在 5% 的显著性水平下对树的周长和体积之间的皮尔逊积矩相关性进行双侧检验，请使用以下命令：

```
> cor.test(trees$Girth, trees$Volume)
```

输出结果如下所示：

```
> aggregate(cbind(Sepal.Width, Sepal.Length)~Species, iris, mean)
     Species Sepal.Width Sepal.Length
1     setosa       3.428        5.006
2 versicolor       2.770        5.936
3  virginica       2.974        6.588
```

你可以将输出保存到一个新的数据框中，如下所示。这允许你将结果用于进一步分析：

```
> sepalmeans<-aggregate(cbind(Sepal.Width, Sepal.Length)~Species, iris, mean)
```

如果任何连续变量有缺失值，请记得将 `na.rm` 参数设置为 `T`。

## Pearson 相关性检验

```
Pearson's product-moment correlation

data:  trees$Girth and trees$Volume
t = 20.4783, df = 29, p-value < 2.2e-16
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 0.9322519 0.9841887
sample estimates:
cor
 0.9671194
```

相关性估计值为 0.967，95% 置信区间为 0.932 到 0.984。这意味着随着树的周长增加，树的体积也趋于增加。

由于 p 值 `2.2e-16` 远小于显著性水平 0.05，我们可以拒绝零假设（即周长和体积之间没有相关性），转而支持备择假设，即两者是相关的。

## 科学计数法

科学计数法是一种更紧凑地表示非常大或非常小数字的方法。

例如，数字 720000 等于 7.2 × 100000 或 7.2 × 10⁵。R 和许多其他编程语言使用字母 `e` 来表示“乘以 10 的幂”，因此 7.2 × 10⁵ 显示为 `7.2e5`。

科学计数法对于非常小的数字同样适用，例如，数字 0.000072 等于 7.2 × 0.00001 或 7.2 × 10^(-5)。使用 R 表示法，这将显示为 `7.2e-5`。

## 比较样本与指定分布

有时你可能希望确定你的样本是否与从特定类型的分布（例如正态分布）中抽取的样本一致。这很有用，因为许多统计技术（例如方差分析）仅适用于正态分布的数据。

两种允许你进行此操作的方法是夏皮罗-威尔克检验和科尔莫戈罗夫-斯米尔诺夫检验。要直观地评估你的数据与正态分布的拟合程度，请使用直方图或正态概率图（详见第 8 章）。

### 夏皮罗-威尔克检验

夏皮罗-威尔克检验是一种假设检验，可以帮助确定样本是否来自正态分布。该检验的零假设是样本来自正态分布，备择假设是样本并非来自正态分布。

你可以使用 `shapiro.test` 函数执行夏皮罗-威尔克检验。

```
> shapiro.test(dataset$variable)
```

### 示例 5-2. 使用树木数据集进行夏皮罗-威尔克检验

假设你想要执行一个夏皮罗-威尔克检验来帮助确定树的高度是否遵循正态分布。你将使用 5% 的显著性水平。要执行此检验，请使用以下命令：

```
> shapiro.test(trees$Height)
```

输出结果：

```
Shapiro-Wilk normality test

data:  trees$Height
W = 0.9655, p-value = 0.4034
```

从输出结果可以看出，检验的 p 值为 0.4034。由于这不小于我们的显著性水平 0.05，我们不能拒绝零假设。这意味着没有证据表明树的高度不遵循正态分布。

# 柯尔莫戈洛夫-斯米诺夫检验

单样本 `Kolmogorov-Smirnov test`（柯尔莫戈洛夫-斯米诺夫检验）有助于判断一个样本是否来自某个特定的理论分布。它的原假设是样本来自该分布，备择假设是样本并非来自该分布。

双样本 `Kolmogorov-Smirnov test`（柯尔莫戈洛夫-斯米诺夫检验）有助于判断两个样本是否来自同一分布。它的原假设是两个样本来自同一分布，备择假设是它们并非来自同一分布。

请注意，单样本和双样本的 `Kolmogorov-Smirnov` 检验都要求数据是连续的。如果你的数据包含结（即某些值完全相等），则无法执行该检验。如果你的数据没有记录到足够的小数位数，这可能会成为一个问题。

你可以使用 `ks.test` 函数执行 `Kolmogorov-Smirnov` 检验。要执行原假设为样本来自均值为 100、标准差为 10 的正态分布的单样本检验，请使用以下命令：

```
> ks.test(dataset$variable, "pnorm", 100, 10)
```

要测试样本是否来自另一个理论分布，请将 `"pnorm"` 替换为相应的累积分布函数。标准概率分布的函数列表见第 7 章的表 7.1。你还必须将平均值和标准差替换为该分布相关的任何参数。使用 `help` 函数检查你所关注分布的参数。

要执行双样本检验以判断两个样本是否来自同一分布，请使用以下命令：

```
> ks.test(dataset$sample1, dataset$sample2)
```

如果你的数据是堆叠形式（两个样本的值都在一个变量中），则必须首先像第 4 章（在“解堆叠数据”部分）所解释的那样，对数据集进行解堆叠。

## 示例 5-3. 使用瓶装数据进行的单样本柯尔莫戈洛夫-斯米诺夫检验

考虑 `bottles` 数据集，该数据集可从本书的下载资源中获得。该数据集提供了从灌装线上抽取的 20 瓶软饮料样本的数据。该数据集包含一个名为 `Volume` 的变量，该变量给出了每瓶饮料的液体体积（毫升）。

瓶装灌装量被认为服从均值为 500 毫升、标准差为 25 毫升的正态分布。假设您希望使用单样本 `Kolmogorov-Smirnov` 检验来确定数据是否与该理论相符。该检验的原假设是瓶装体积来自所述分布，备择假设是它们并非来自该分布。检验将使用 0.05 的显著性水平。

要执行此检验，请使用以下命令：

```
> ks.test(bottles$Volume, "pnorm", 500, 25)
```

这将给出以下输出：

```
One-sample Kolmogorov-Smirnov test

data:  bottles$Volume
D = 0.2288, p-value = 0.2108
alternative hypothesis: two-sided
```

从输出中我们可以看到，该检验的 p 值为 0.2108。由于这不小于 0.05 的显著性水平，我们不能拒绝原假设。这意味着没有证据表明瓶装体积不是来自所述的正态分布。

## 示例 5-4. 使用 PlantGrowth 数据进行的双样本柯尔莫戈洛夫-斯米诺夫检验

考虑 `PlantGrowth` 数据集（包含在 R 中），该数据集提供了三十批植物的干重，每批植物接受了三种不同处理中的一种。`weight` 变量给出了批次的重量，`groups` 变量给出了接受的处理方式（`ctrl`、`trt1` 或 `trt2`）。

假设您想使用双样本 `Kolmogorov-Smirnov` 检验来确定批次重量在处理组 `trt1` 和 `trt2` 中是否具有相同的分布。

首先，您需要使用 `unstack` 函数对数据进行解堆叠。

```
> PlantGrowth2 <- unstack(PlantGrowth)
```

数据处于解堆叠形式后，按如下所示执行检验：

```
> ks.test(PlantGrowth2$trt1, PlantGrowth2$trt2)
```

这将给出以下输出：

```
Two-sample Kolmogorov-Smirnov test

data:  PlantGrowth2$trt1 and PlantGrowth2$trt2
D = 0.8, p-value = 0.002057
alternative hypothesis: two-sided
```

p 值 0.002057 小于显著性水平 0.05。这意味着有证据支持拒绝原假设（即两个处理组的批次重量分布相同），而支持备择假设（即两个处理组的批次重量分布不同）。

## 置信区间与预测区间

总体均值的置信区间表明了样本均值估计总体均值的准确程度。95% 置信区间被定义为这样一种方式计算的区间：如果从总体中抽取大量样本，并为每个样本计算该区间，那么 95% 的区间将包含真实的总体均值。

预测区间表明了样本均值预测从总体中抽取的另一个观测值的准确程度。

获取样本均值置信区间的最简单方法是使用 `t.test` 函数，该函数直接在输出中提供置信区间。第 10 章的“学生 t 检验”部分会更详细地讨论该函数。如果您只对获取置信区间感兴趣，请使用以下命令：

```
> t.test(trees$Height)
```

```
One Sample t-test

data:  trees$Height
t = 66.4097, df = 30, p-value < 2.2e-16
alternative hypothesis: true mean is not equal to 0
95 percent confidence interval:
  73.6628 78.3372
sample estimates:
mean of x
76
```

从结果中，您可以看到树木的平均高度为 76 英尺，95% 置信区间为 73.7 到 78.3 英尺。

默认情况下，R 计算 95% 的区间。对于不同大小的置信区间，例如 99%，请按如下所示调整 `conf.level` 参数：

```
> t.test(trees$Height, conf.level = 0.99)
```

您也可以计算单侧置信区间。对于上侧置信区间，将 `alternative` 参数设置为 `"less"`。对于下侧置信区间，将其设置为 `"greater"`。

```
> t.test(dataset$variable, alternative = "greater")
```

要计算树木高度的预测区间，请使用以下命令：

```
> predict(lm(trees$Height ~ 1), interval = "prediction")[1,]
```

```
    fit      lwr      upr
76.0000 62.7788 89.2212
Warning message:
In predict.lm(lm(trees$Height ~ 1), interval = "prediction") :
  predictions on current data refer to _future_ responses
```

从输出中您可以看到树木高度的预测区间为 62.8 到 89.2 英尺。

同样，您可以使用 `level` 参数调整置信水平，如下所示：

```
> predict(lm(dataset$variable ~ 1), interval = "prediction", level = 0.99)[1,]
```

创建预测区间的命令使用了 `lm` 和 `predict` 函数。您将在第 11 章中了解更多关于这些函数的内容，这将帮助您理解这些复杂命令的作用。目前，您只需替换相应的数据集和变量名即可直接使用它们。

### 总结

你现在应该能够使用 R 语言来总结数据集中的连续变量，并检验它们之间的关系。你还应该能够根据样本对总体进行一些推断，例如样本是否服从正态分布。

下表总结了本章涵盖的最重要的命令。

| 任务 | 命令 |
| --- | --- |
| 每个变量的统计量 | `sapply(dataset, statistic)` |
| 按分组的统计量 | `tapply(dataset$var1, dataset$factor1, statistic)` |
| 按分组的统计量 | `aggregate(variable ~ factor, dataset, statistic)` |
| 协方差 | `cov(dataset$var1, dataset$var2)` |
| 协方差矩阵 | `cov(dataset)` |
| 皮尔逊相关系数 | `cor(dataset$var1, dataset$var2)` |
| 相关矩阵 | `cor(dataset)` |
| 斯皮尔曼秩相关系数 | `cor(dataset$var1, dataset$var2, method="spearman")` |
| 斯皮尔曼相关矩阵 | `cor(dataset, method="spearman")` |
| 相关性的假设检验 | `cor.test(dataset$var1, dataset$var2)` |
| 夏皮罗-威尔克检验 | `shapiro.test(dataset$variable)` |

| 单样本柯尔莫哥洛夫-斯米诺夫检验 | `ks.test(dataset$sample1, "pnorm", mean, sd)` |
| 双样本柯尔莫哥洛夫-斯米诺夫检验 | `ks.test(dataset$sample1, dataset$sample2)` |
| 置信区间 | `t.test(dataset$variable)` |
| 预测区间 | `predict(lm(dataset$variable ~ 1), interval="prediction")[1,]` |

既然你已经学会了如何总结连续变量，我们可以进入下一章，该章将介绍分类变量。
