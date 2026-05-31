# 柯尔莫戈洛夫-斯米诺夫检验

单样本 `Kolmogorov-Smirnov test`（柯尔莫戈洛夫-斯米诺夫检验）有助于判断一个样本是否来自某个特定的理论分布。它的原假设是样本来自该分布，备择假设是样本并非来自该分布。

双样本 `Kolmogorov-Smirnov test`（柯尔莫戈洛夫-斯米诺夫检验）有助于判断两个样本是否来自同一分布。它的原假设是两个样本来自同一分布，备择假设是它们并非来自同一分布。

请注意，单样本和双样本的 `Kolmogorov-Smirnov` 检验都要求数据是连续的。如果你的数据包含结（即某些值完全相等），则无法执行该检验。如果你的数据没有记录到足够的小数位数，这可能会成为一个问题。

你可以使用 `ks.test` 函数执行 `Kolmogorov-Smirnov` 检验。要执行原假设为样本来自均值为 100、标准差为 10 的正态分布的单样本检验，请使用以下命令：

`> ks.test(dataset$variable, "pnorm", 100, 10)`

要测试样本是否来自另一个理论分布，请将 `"pnorm"` 替换为相应的累积分布函数。标准概率分布的函数列表见第 7 章的表 7.1。你还必须将平均值和标准差替换为该分布相关的任何参数。使用 `help` 函数检查你所关注分布的参数。

要执行双样本检验以判断两个样本是否来自同一分布，请使用以下命令：

`> ks.test(dataset$sample1, dataset$sample2)`

如果你的数据是堆叠形式（两个样本的值都在一个变量中），则必须首先像第 4 章（在“解堆叠数据”部分）所解释的那样，对数据集进行解堆叠。

## 示例 5-3. 使用瓶装数据进行的单样本柯尔莫戈洛夫-斯米诺夫检验

考虑 `bottles` 数据集，该数据集可从本书的下载资源中获得。该数据集提供了从灌装线上抽取的 20 瓶软饮料样本的数据。该数据集包含一个名为 `Volume` 的变量，该变量给出了每瓶饮料的液体体积（毫升）。

瓶装灌装量被认为服从均值为 500 毫升、标准差为 25 毫升的正态分布。假设您希望使用单样本 `Kolmogorov-Smirnov` 检验来确定数据是否与该理论相符。该检验的原假设是瓶装体积来自所述分布，备择假设是它们并非来自该分布。检验将使用 0.05 的显著性水平。

要执行此检验，请使用以下命令：

`> ks.test(bottles$Volume, "pnorm", 500, 25)`

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

`> PlantGrowth2 <- unstack(PlantGrowth)`

数据处于解堆叠形式后，按如下所示执行检验：

`> ks.test(PlantGrowth2$trt1, PlantGrowth2$trt2)`

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

`> t.test(trees$Height)`

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

`> t.test(trees$Height, conf.level = 0.99)`

您也可以计算单侧置信区间。对于上侧置信区间，将 `alternative` 参数设置为 `"less"`。对于下侧置信区间，将其设置为 `"greater"`。

`> t.test(dataset$variable, alternative = "greater")`

要计算树木高度的预测区间，请使用以下命令：

`> predict(lm(trees$Height ~ 1), interval = "prediction")[1,]`

```
    fit      lwr      upr
76.0000 62.7788 89.2212
Warning message:
In predict.lm(lm(trees$Height ~ 1), interval = "prediction") :
  predictions on current data refer to _future_ responses
```

从输出中您可以看到树木高度的预测区间为 62.8 到 89.2 英尺。

同样，您可以使用 `level` 参数调整置信水平，如下所示：

`> predict(lm(dataset$variable ~ 1), interval = "prediction", level = 0.99)[1,]`

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

