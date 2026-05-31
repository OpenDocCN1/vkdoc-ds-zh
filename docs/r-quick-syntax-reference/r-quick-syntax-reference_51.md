# 矩阵、数组和数据框

`base` 包中有许多关于矩阵、数组和数据框的函数我们尚未涉及。其中部分函数包括：

- `aperm()`，对数组进行维度置换
- `rowsum()`，根据 `group` 变量设置的分组，对矩阵或数据框的行进行求和
- `colMeans()`，返回数据框或矩阵的列均值，或返回数组从第一维度到指定维度的均值
- `colSums()`，返回数据框或矩阵的列和，或返回数组从第一维度到指定维度的和
- `rowMeans()`，返回数据框或矩阵的行均值，或返回数组从指定维度加一到最后一个维度的和
- `rowSums()`，返回数据框或矩阵的行和，或返回数组从指定维度加一到最后一个维度的和
- `col()`，返回一个与参数维度相同的矩阵，其各列包含列索引，或返回一个每列为一个因子的因子矩阵
- `row()`，返回一个与参数维度相同的矩阵，其各行包含行索引，或返回一个每行为一个因子的因子矩阵
- `det()`，返回矩阵的行列式
- `determinant()`，返回行列式的模（或模的对数）以及模的符号
- `eigen()`，返回矩阵的特征值和特征向量
- `kappa()`，计算方阵的条件数
- `kronecker()`，返回两个对象的克罗内克 `product`（乘积）矩阵或数组，其中 `product` 是一个指定函数。这两个对象可以是向量、矩阵和/或数组。结果的维度是两个对象维度的乘积。
- `norm()`，返回通过 `one`（1-范数）、`infinity`（无穷范数）、`Frobenius`（Frobenius 范数）、`maximum modulus`（最大模）或 `spectral`（谱范数，或 `2`-范数）方法计算的矩阵范数

模型拟合中使用的一些函数如下：

- `backsolve()`，求解左侧矩阵为上三角矩阵的矩阵方程
- `forwardsolve()`，求解左侧矩阵为下三角矩阵的矩阵方程
- `chol()`，正定方阵的 Cholesky 分解
- `chol2inv()`，利用矩阵的 Cholesky 分解求正定矩阵的逆矩阵
- `qr()`，矩阵的 QR 分解
- `svd()`，矩阵的奇异值分解。

函数及其参数的列表见表 16-6。

**表 16-6** 部分矩阵、数组及数据框函数

| R 中的函数 | 限制条件 |
| --- | --- |
| `aperm(a, perm=NULL, resize=TRUE, ...)` | `a`，矩阵或数组<br>`perm`，NULL、整数或字符向量；通过索引或字符串指定维度的顺序；若非 NULL，其长度必须等于`a`的维度数，且是`a`维度的一个排列；若为 NULL 则返回维度反转的结果<br>`resize`，逻辑值；必须为 TRUE 或 FALSE<br>`...`，传递给底层函数的任何参数 |
| `rowsum(x, group, reorder=TRUE, na.rm=FALSE, ...)` | `x`，任意数值矩阵<br>`group`，长度等于`x`行数的向量或因子——用于分组<br>`reorder`，逻辑值；必须为 TRUE 或 FALSE<br>`na.rm`，逻辑值；必须为 TRUE 或 FALSE<br>`...`，传递给底层函数或从底层函数接收的任何参数 |
| `colMeans(x, na.rm=FALSE, dims=1)` | `x`，逻辑型、数值型或复数型矩阵、数据框或数组<br>`na.rm`，逻辑值；必须为 TRUE 或 FALSE<br>`dims`，数值；1 ≤ `dims` ≤ n-1，其中 n 是维度数 |
| `colSums(x, na.rm=FALSE, dims=1)` | 参见 `colMeans()` |
| `rowMeans(x, na.rm=FALSE, dims=1)` | 参见 `colMeans()` |
| `rowSums(x, na.rm=FALSE, dims=1)` | 参见 `colMeans()` |
| `col(x, as.factor=FALSE)` | `x`，任意矩阵<br>`as.factor`，逻辑值；必须为 TRUE 或 FALSE |
| `row(x, as.factor=FALSE)` | 参见 `col()` |
| `det(x, ...)` | `x`，逻辑型或数值型方阵；逻辑值会被强制转换为数值<br>`...`，忽略 |
| `determinant(x, logarithm=TRUE, ...)` | `x`，逻辑型或数值型方阵；逻辑值会被强制转换为数值<br>`logarithm`，逻辑值；必须为 TRUE 或 FALSE<br>`...`，忽略 |
| `eigen(x, symmetric, only.values=FALSE, EISPACK=FALSE)` | `x`，逻辑型、数值型或复数型方阵；逻辑值会被强制转换为数值<br>`symmetric`，逻辑值；若为 TRUE 则假定矩阵对称，FALSE 则不对称<br>`only.values`，逻辑值；若为 TRUE 仅返回特征值，FALSE 则同时返回特征值和特征向量<br>`EISPACK`，逻辑值；已废弃并忽略 |
| `kappa(z, exact=FALSE, norm=NULL, method=c("qr", "direct"), ...)` | `z`，逻辑型或数值型方阵；逻辑值会被强制转换为数值<br>`exact`，逻辑值；必须为 TRUE 或 FALSE<br>`norm`，字符；必须为 NULL、"O"或"I"——分别对应 1-范数和无穷范数<br>`method`，字符；必须为"qr"或"direct"；默认值为"qr"<br>`...`，传递给底层函数的任何参数 |
| `kronecker(X, Y, FUN="*", make.names=FALSE, ...)` | `X`，`Y`，向量、矩阵和数组；不必具有相同模式；必须是函数`FUN`的合法输入<br>`FUN`，一个函数；可以是字符串<br>`make.names`，逻辑值；必须为 TRUE 或 FALSE；不适用于所有函数<br>`...`，函数`FUN`的任何参数 |
| `norm(x, type=c("O","I","F","M","2"))` | `x`，逻辑型、数值型或复数型矩阵；逻辑型和复数型会被强制转换为数值<br>`type`，字符；默认值为"O" |
| `backsolve(r, x, k=ncol(r), upper.tri=TRUE, transpose=FALSE)` | `r`，逻辑型、数值型或复数型上三角矩阵——逻辑型和复数型会被强制转换为数值<br>`x`，逻辑型、数值型或复数型向量或矩阵——逻辑型和复数型会被强制转换为数值<br>`k`，数值——向下取整为整数；1 ≤ `k` ≤ ncol(`r`)；要使用的'r'的列数<br>`upper.tri`，逻辑值；为 TRUE 时使用上三角，FALSE 时使用下三角<br>`transpose`，逻辑值；为 TRUE 时公式中的`r`会被转置 |
| `forwardsolve(l, x, k=ncol(l), upper.tri=FALSE, transpose=FALSE)` | `l`，逻辑型、数值型或复数型下三角矩阵——逻辑型和复数型会被强制转换为数值<br>`x`，逻辑型、数值型或复数型向量或矩阵——逻辑型和复数型会被强制转换为数值<br>`k`，数值——向下取整为整数；1 ≤ `k` ≤ ncol(`l`)；要使用的'l'的列数<br>`upper.tri`，逻辑值；为 TRUE 时使用上三角，FALSE 时使用下三角<br>`transpose`，逻辑值；为 TRUE 时公式中的`l`会被转置 |
| `chol(x, pivot=FALSE, LINPACK=FALSE, tol=-1, ...)` | `x`，原始型、逻辑型或数值型矩阵——原始型和逻辑型矩阵会被强制转换为数值；必须是方阵且正定<br>`pivot`，逻辑值；为 TRUE 时使用主元，FALSE 不使用主元<br>`LINPACK`，（已弃用）逻辑值；为 TRUE 时使用 LINPACK，FALSE 不使用 LINPACK<br>`tol`，数值；当`pivot=TRUE`且`LINPACK=FALSE`时的容差<br>`...`，传递给底层函数的任何参数 |
| `chol2inv(x, size=NCOL(x), LINPACK=FALSE)` | `x`，其前`size`列为 Cholesky 分解结果的矩阵<br>`size`，数值、逻辑型或复数型——逻辑型和复数型会被强制转换为数值；1 ≤ `size` ≤ ncol(`x`)<br>`LINPACK`，逻辑值；已废弃——不再使用 |
| `qr(x, tol=1e-7, LAPACK=FALSE, ...)` | `x`，逻辑型、数值型或复数型矩阵；逻辑型矩阵会被强制转换为数值<br>`tol`，数值；奇异性容差<br>`LAPACK`，逻辑值；若为 FALSE，`qr()`使用 LINPACK<br>`...`，传递给底层函数的任何参数 |
| `svd(x, nu=min(n,p), nv=min(n,p), LINPACK=FALSE)` | `x`，逻辑型、数值型或复数型矩阵；逻辑型矩阵会被强制转换为数值<br>`nu`，整数；0 ≤ `nu` ≤ n；n = nrow(`x`)<br>`nv`，整数；0 ≤ `nv` ≤ p；p = ncol(`x`)<br>`LINPACK`，逻辑值；已废弃并忽略 |



您可以通过访问各个函数的帮助页面（`?函数名`，其中`函数名`是函数的名称）或使用 RStudio 中的“帮助”选项卡来获取更多信息。

### 其他一些函数及说明

-   以下是一些常用的其他函数：`R.home()`、`R.Version()`、`all.equal()`、`identical()`、`dir()`、`getwd()`、`setwd()`、`unique()`、`hexmode()`、`jitter()`、`append()`、`duplicated()`（以及`anyDuplicated()`）、`attr()`（以及`attributes()`）、`pretty()`、`margin.table()`、`prop.table()`、`cut()`、`rev()`、`readline()`、`system()`、`try()`、`warnings()`和`stop()`。对于这些函数，我们将简要说明它们的功能。您可以在 R 提示符下输入`?'函数名'`（其中`函数名`是函数的名称）或使用 RStudio 中的“帮助”选项卡来获取有关这些函数的更多信息。

以下是函数说明：

-   `R.home()` 返回包含 R 程序的目录的完整路径。
-   `R.Version()` 返回 R 版本及其他相关信息。
-   `all.equal()` 测试两个对象是否近似相等。
-   `identical()` 测试两个对象是否完全相同。
-   `dir()` 返回硬盘上某个目录中的内容。
-   `getwd()` 返回硬盘上的当前工作目录。
-   `setwd()` 设置硬盘上的工作目录。
-   `unique()` 返回一个向量，其中移除了原始向量中的所有重复元素。该函数仅适用于向量，包括列表模式的向量。
-   `hexmode()` 返回一个数的十六进制值。
-   `jitter()` 为数值型对象的元素添加少量抖动（噪声）。`jitter()` 的参数控制添加的抖动幅度。
-   `append()` 用于追加向量。`append()` 的一个参数指定了在向量的哪个位置进行追加。
-   `duplicated()` 和 `anyDuplicated()` 用于查找重复项。对于向量（包括列表），`duplicated()` 返回一个与原向量等长的向量，其中不重复的元素以及重复元素的第一个实例对应 `FALSE`，其余重复元素对应 `TRUE`。对于矩阵和数据框，则比较各行。函数 `anyDuplicated()` 统计有多少个不同的元素（或矩阵、数据框中有多少行）存在重复。
-   `attr()` 和 `attributes()` 返回对象的某个属性或属性列表。使用 `attr()` 函数可以访问并获取某个属性的值。要查看对象的所有属性列表，请使用 `attributes()`。
-   `pretty()` 接受任何可强制转换为数值型的对象，并返回一个等间距值构成的向量，该向量的长度接近给定值，且其取值范围与原始对象相似。
-   `margin.table()` 接受一个逻辑型、数值型或复数型对象，并返回表格中指定边距的边际和。
-   `prop.table()` 接受一个逻辑型、数值型或复数型对象，并返回该对象除以所有元素之和的结果。逻辑型对象会被强制转换为数值型，复数对象的实部和虚部会分别处理。
-   `cut()` 将数值型向量切割成因子，并返回一个字符型向量，其中原始元素的位置被因子名称替代。被切割的对象可以是任何能强制转换为向量的对象，但必须是数值型。可以指定断点和因子名称，但默认情况下 `cut()` 会根据断点自动创建断点和因子名称。
-   `rev()` 反转对象中元素的顺序，并返回一个向量。该对象可以是原子向量，也可以是任何适合反转顺序的模式，例如 `list`、`expression` 和 `call` 模式。
-   `readline()` 从控制台读取一行——用于 R 函数的交互式使用。
-   `system()` 从 R 内部运行系统命令——命令需要用引号括起来。
-   `try()` 尝试执行一个表达式或函数——返回错误信息或执行结果。错误不会导致程序停止。
-   `warnings()` 如果程序在运行过程中产生了警告，则返回这些警告信息。
-   `stop()` 指示 R 停止执行某个函数。如果 `stop()` 的参数是字符串，则执行 `stop()` 时会打印该字符串。该函数在调试函数以及检查输入函数的对象是否满足条件时非常有用。
-   `gc()` 垃圾回收——清理会话环境。

`base` 包中还有许多其他函数，其中许多与 R 的运行有关。`as.` 和 `is.` 类函数很常见。在帮助页面列表中，有 110 个 `as.` 类函数的链接和 46 个 `is.` 类函数的链接。如果您对这些列表中的内容感兴趣，可以点击链接页面查看。贝塞尔函数和按位逻辑函数也属于 `base` 包的一部分。

## stats 包

`stats` 包包含基本描述性统计、概率分布、检验、模型拟合函数、聚类函数、一些绘图函数以及其他用于输出结果的函数。`stats` 包帮助页面的链接列表长达 18 页（`help(package=stats)`）。在本章中，我们将简要介绍基本描述性统计、检验、聚类及其他用于多元数据的函数，以及建模函数。概率分布可在第 9 章中找到。

### 基本描述性统计

`stats` 包中的一些基本统计函数包括：

- `weighted.mean()`，用于计算对象的加权平均值
- `sd()`，用于计算对象的标准差
- `var()`，用于计算向量的方差，或矩阵/数据框的协方差矩阵
- `cov()`，用于计算矩阵或数据框的协方差矩阵——比 `var()` 更灵活
- `cov.wt()`，用于计算矩阵或数据框的加权协方差矩阵或相关系数矩阵
- `cor()`，用于计算向量之间或矩阵/数据框内部的相关系数
- `median()`，用于计算对象元素的中位数
- `mad()`，用于计算对象元素的中位数绝对偏差
- `IQR()`，用于计算对象元素的四分位距
- `quantile()`，用于计算对象元素中特定的分位数
- `fivenum()`，用于计算对象元素的 Tukey 五数汇总
- `ave()`，根据因子值对对象的行进行分组，并使用指定函数对每组进行操作
- `cancor()`，用于计算两个矩阵之间的典型相关
- `dist()`，根据距离类型和计算平均值所用的幂次，计算矩阵行之间的某种平均差异
- `mahalanobis()`，用于计算矩阵各行之间的马氏距离
- `ecdf()`，用于计算对象元素的经验累积分布函数——该函数支持分位数方法
- `r2dtable()`，基于边际值使用 Patefield 算法创建随机二维列联表
- `simulate()`，根据已拟合的模型模拟观测值
- `TukeyHSD()`，为模型系数计算考虑了多重假设检验的置信区间——用于方差分析模型
- `xtabs()`，根据公式创建列联表
- `smooth()`，使用 Tukey 的运行中位数平滑法对含噪声的数据集进行平滑处理——通常用于时间序列数据

函数及其参数详见表 16-7。

表 16-7

`stats` 包中的基本统计函数



| R 语言中的函数 | 描述 |
| --- | --- |
| `weighted.mean(x, w, ..., na.rm=FALSE)` | 计算 x 的加权平均值，其中 x 会被强制转换为向量。 |
| `sd(x, na.rm=FALSE)` | 计算 x 的标准差，其中 x 会被强制转换为向量；除以 (n-1) 的平方根。 |
| `var(x, y=NULL, na.rm=FALSE, use)` | 如果 x 是向量，则计算 x 的方差；如果提供了 y，则计算 x 和 y 的协方差；如果 x 是矩阵或数据框，则计算 x 的协方差矩阵；除以 (n-1)。 |
| `cov(x, y=NULL, use="everything", method=c("pearson", "kendall", "spearman"))` | 如果提供了 y，则计算 x 和 y 的协方差；如果 x 是矩阵或数据框，则计算 x 的协方差矩阵；比 `var()` 有更多选项。 |
| `cov.wt(x, wt=rep(1/nrow(x), nrow(x)), cor=FALSE, center=TRUE, method=c("unbiased", "ML"))` | 计算 x 的加权协方差矩阵或加权相关矩阵，其中 x 是矩阵或数据框。 |
| `cor(x, y=NULL, use="everything", method=c("pearson", "kendall", "spearman"))` | 如果提供了 y，则计算 x 和 y 的相关性；如果仅提供 x，则计算 x 内部的相关性，其中 x 是向量、矩阵或数据框。 |
| `median(x, na.rm=FALSE)` | 计算 x 各元素的中位数。 |
| `mad(x, center=median(x), constant=1.4826, na.rm=FALSE, low=FALSE, high=FALSE)` | 计算 x 的中位数绝对偏差。 |
| `IQR(x, na.rm=FALSE, type=7)` | 计算 x 的四分位距。 |
| `quantile(x, probs=seq(0,1,.25), na.rm=FALSE, names=TRUE, type=7, ...)` | 计算 x 在 `probs` 指定概率处的分位数。 |
| `fivenum(x, na.rm=FALSE)` | 计算 x 的 Tukey 五数汇总。 |
| `ave(x, ..., FUN=mean)` | `FUN` 中的函数对 x 的元素分组进行操作，分组变量由参数 `...` 指定。 |
| `cancor(x, y, xcenter=TRUE, ycenter=TRUE)` | 计算矩阵 x 和 y 之间的典型相关。 |
| `dist(x, method="euclidean", diag=FALSE, upper=FALSE, p=2)` | 计算矩阵行之间的距离，距离类型由 `method` 指定。 |
| `mahalanobis(x, center, cov, inverted=FALSE)` | 计算矩阵各行之间的马氏距离。 |
| `ecdf(x)` | 计算 x 的经验累积分布函数。 |
| `r2dtable(n, r, c)` | 基于行和列的边际总数创建一个随机表格。 |
| `simulate(x, nsim=1, seed=NULL, ...)` | 从给定的模型 x 中模拟观测值；x 是一个模型。 |
| `TukeyHSD(x, which, order=FALSE, conf.level=0.95, ...)` | 方差分析模型的 Tukey 诚实显著差异检验。 |
| `xtabs(formula=~., data=parent.frame(), subset, sparse=FALSE, na.action, exclude=c(NA,NaN), drop.unused.levels=FALSE)` | 基于公式创建列联表，其中公式右侧的变量用于对左侧的对象进行分组。 |
| `smooth(x, kind=c("3RS3R", "3RSS", "3RSR", "3R", "3S", "3", "S"), twiceit=FALSE, endrule="Tukey", do.ends=FALSE)` | 使用 Tukey 的运行中位数平滑器对向量或时间序列进行平滑。 |

你可以在 R 提示符下输入 `?函数名`（其中 `函数名` 是函数的名称），或使用 R Studio 中的“帮助”（Help）选项卡，查找有关这些函数的更多信息。

### 执行检验的若干函数

`stats` 包中有多个执行假设检验的函数。其中一些函数包括：

*   `ansari.test()` 用于 Ansari-Bradley 检验，检验两个样本的尺度参数是否存在差异。
*   `bartlett.test()` 用于方差齐性检验。
*   `binomial.test()` 用于使用二项分布的精确检验。
*   `Box.test()` 用于 Box-Pierce 和 Ljung-Box 检验——在时间序列中用于检验独立性。
*   `chisq.test()` 用于使用皮尔逊检验对计数数据进行检验。
*   `cor.test()` 用于配对样本的相关性检验。
*   `fisher.test()` 用于使用 Fisher 精确检验的列联表检验。
*   `fligner.test()` 用于 Fligner-Killeen 方差齐性检验。
*   `friedman.test()` 用于 Friedman 秩和检验。
*   `kruskal.test()` 用于 Kruskal-Wallis 秩和检验。
*   `ks.test()` 用于单样本或双样本的 Kolmogorov-Smirnov 检验。
*   `mantelhaen.test()` 用于计数数据的 Cochran-Mantel-Haenszel 卡方检验。
*   `mauchly.test()` 用于 Mauchly 提出的球形度检验。
*   `mcnemar.test()` 用于 McNemar 提出的计数数据卡方检验。
*   `mood.test()` 用于 Mood 提出的双样本尺度检验。
*   `oneway.test()` 用于单因素布局下检验均值是否相等。
*   `pairwise.prop.test()` 用于成对比较比例。
*   `pairwise.t.test()` 用于成对比较 t 检验。
*   `pairwise.wilcox.test()` 用于成对比较 Wilcox 秩和检验。
*   `poisson.test()` 用于使用泊松分布的精确检验。
*   `power.anova.test()` 用于计算平衡单因素方差分析的检验功效。
*   `power.prop.test()` 用于计算比较两个比例的检验功效。
*   `power.t.test()` 用于单样本和双样本 t 检验的检验功效。
*   `PP.test()` 用于 Phillips-Perron 检验，检验时间序列数据中的单位根。
*   `prop.test()` 用于比例检验。
*   `prop.trend.test()` 用于比例的趋势检验。
*   `quade.test()` 用于 Quade 检验。
*   `shapiro.test()` 用于 Shapiro-Wilk 正态性检验。
*   `t.test()` 用于执行 t 检验。
*   `var.test()` 用于比较两个方差的 F 检验。
*   `wilcox.test()` 用于 Wilcoxon 秩和检验和符号检验。

这些检验及其参数列于表 16-8 中。

**表 16-8** `stats` 包中的部分检验



| **检验** |
| `ansari.test(x, y, alternative=c(“two-sided”, “less”, “greater”), exact=NULL, conf.int=FALSE, conf.level=0.95, ...)` |
| `bartlett.test(x, g, ...)` |
| `biniom.test(x, n, p=0.5, alternative=c("two-sided", "less", "greater"), conf.level=0.95)` |
| `Box.test(x, lag=1, type=c("Box-Pierce", "Ljung-Box"), fitdf=0)` |
| `chisq.test(x, y=NULL, correct=TRUE, p=rep(1/length(x), length(x)), rescale.p=FALSE, B=2000)` |
| `cor.test(x, y, alternative=c("two.sided", "less", "greater"), method=c("pearson", "kendall", "spearman"), exact=NULL, conf.level=0.95, continuity=FALSE, ...)` |
| `fisher.test(x, y=NULL, workspace=200000, hybrid=FALSE, control=list(), or=1, alternative="two.sided", conf.int=TRUE, conf.level=0.95, simulate.p.value=FALSE, B=2000)` |
| `fligner.test(x, g, ...)` |
| `friedman.test(y, groups, blocks, ...)` |
| `kruskal(x, g, ...)` |
| `ks.test(x, y, ..., alternative=c("two-sided", "less", "greater"), exact=NULL)` |
| `mantelhaen.test(x, y=NULL, z=NULL, alternative=c("two.sided", "less", "greater"), correct=T, exact=F, conf.level=0.95)` |
| `mauchly.test(object, ...)` |
| `mcnemar.test(x, y=NULL, correct=TRUE)` |
| `mood.test(x, y, alternative=c("two.sided", "less", "greater"), ...)` |
| `oneway.test(formula, data, subset, na.action, var.equal=FALSE)` |
| `pairwise.prop.test(x, n, p.adjust.method=p.adjust.methods, ...)` |
| `pairwise.t.test(x, g, p.adjust.method=p.adjust.methods, pool.sd=!paired, paired=FALSE, alternative=c("two.sided", "less", "greater"), ...)` |
| `pairwise.wilcox.test(x, g, p.adjust.method=p.adjust.methods, paired=FALSE, ...)` |
| `poisson.test(x, T=1, r=1, alternative=c("two-sided", "less", "greater"), conf.level=0.95)` |
| `power.anova.test(groups=NULL, n=NULL, between.var=NULL, within.var=NULL, sig.level=0.05, power=NULL)` |
| `power.prop.test(n=NULL, p1=NULL, p2=NULL, sig.level=0.05, power=NULL, alternative=c("two-sided", "one.sided"), strict=FALSE)` |
| `power.t.test(n=NULL, delta=NULL, sd=1, sig.level=0.05, type=c("two.sample", "one.sample", "paired"), alternative=c("two.sided", "one.sided"), strict=FALSE)` |
| `PP.test(x, lshort=TRUE)` |
| `prop.test(x, n, p=NULL, alternative=c("two-sided", "less", "greater"), conf.level=0.95, correct=TRUE)` |
| `prop.tend.test(x, n, score=seq_along(x))` |
| `quade.test(y, ...)` |
| `shapiro.test(x)` |
| `t.test(x, y=NULL, alternative=c("two-sided", "less", "greater"), mu=0, paired=FALSE, var.equal=FALSE, conf.level=0.95, ...)` |
| `var.test(x, y, ratio=1, alternative=c("two-sided", "less", "greater"), conf.level=0.95, ...)` |
| `wilcox.test(x, y=NULL, alternative=c("two-sided", "less", "greater"), mu=0, paired=FALSE, exact=NULL, correct=TRUE, conf.int=FALSE, conf.level=0.95, ...)` |

如需了解更多关于任何检验的信息，请在 R 提示符后输入`?` `function.name`，其中 `function.name` 是函数名称，或使用 R Studio 中的“帮助”选项卡。

### 统计模块中的一些建模函数

`stats` 中有许多用于建模的函数，包括以下内容：

*   `acf()` 用于估计时间序列的自相关和自协方差
*   `acf2AR()` 用于精确拟合自回归模型到自相关函数
*   `add1()` 用于查找可以从模型中添加或删除的单个项，拟合模型，并制表拟合结果
*   `AIC()` 和 `BIC()` 用于为合适的模型找到赤池信息准则或施瓦茨贝叶斯准则
*   `aov()` 用于拟合方差分析模型
*   `approx()` 和 `approxfun()` 用于执行线性插值
*   `ar()` 用于拟合时间序列自回归模型
*   `arima()` 用于拟合时间序列数据的自回归积分滑动平均模型
*   `arima.sim()` 用于从 ARIMA 模型进行模拟
*   `ccf()` 用于估计两个时间序列的互相关和互协方差
*   `complete.cases()` 用于查找向量、矩阵或数据框序列的完整案例
*   `contrasts()` 用于设置或获取因子对象的对比度
*   `cpgram()` 用于绘制时间序列数据的累积周期图
*   `decompose()` 用于使用移动平均分解季节性模式
*   `density()` 用于核密度估计
*   `ecdf()` 用于经验累积分布函数
*   `fft()` 用于时间序列数据的快速离散傅里叶变换
*   `filter()` 用于时间序列的线性过滤
*   `glm()` 用于拟合广义线性模型
*   `isoreg()` 用于等距或单调回归
*   `KalmanForcast()`, `KalmanLike()`, `KalmanRun()`, `KalmanSmooth()`, 和 `makeARIMA()` 用于卡尔曼滤波
*   `ksmooth()` 用于使用核平滑器进行平滑
*   `line()` 用于稳健地拟合一条直线——基于 Tukey 的探索性数据分析
*   `lm()` 用于拟合线性模型
*   `loess()` 用于拟合局部多项式模型
*   `loglin()` 用于拟合对数线性模型
*   `lsfit()` 用于拟合具有一个解释变量的最小二乘线性模型
*   `manova()` 用于拟合多元方差分析模型
*   `medpolish()` 用于矩阵的中位数抛光
*   `mvfft()` 用于矩阵的快速离散傅里叶变换
*   `nlm()` 用于寻找非线性模型的最小值
*   `nls()` 用于拟合非线性最小二乘模型
*   `optim()`, `optimHess()`, `optimise()`, 和 `optimize()` 用于优化函数
*   `pacf()` 用于估计时间序列的偏自协方差和偏自相关
*   `poly()` 和 `polym()` 用于创建所需次数的正交多项式
*   `ppr()` 用于拟合投影寻踪回归模型
*   `profile()` 用于模型剖析——泛型函数
*   `smooth.spline()` 用于拟合平滑样条模型
*   `spec()` 用于寻找时间序列数据的谱密度
*   `step()` 用于使用 AIC 通过逐步算法选择模型
*   `stl()` 用于使用 loess 方法对时间序列进行季节性分解
*   `StrucTS()` 用于拟合结构性时间序列模型
*   `supsmu()` 用于 Friedman 的超平滑
*   `update()` 用于更新模型

`stats` 中还有许多支持建模的函数，我们在此未涉及。您可以在各个函数的帮助页面找到更多信息：在 R 提示符后输入`?function.name`，其中 `function.name` 是函数名称，或使用 R Studio 中的“帮助”选项卡。



### 聚类算法及其他多变量技术

多变量分析中用于聚类和处理多变量数据的一些函数如下：

- `cmdscale()` 用于经典多维标度
- `cophenetic()` 用于层次聚类中的同表型距离
- `cut.dendrogram()` 用于通用的树结构
- `cutree()` 用于将树切割成组
- `dendrapply()` 用于对树状图的所有节点应用某个函数
- `as.dendrogram()` 用于为对象赋予树状图类
- `factanal()` 用于因子分析
- `hclust()` 用于层次聚类
- `identify.hclust()` 用于识别聚类
- `kmeans()` 用于 K 均值聚类
- `labels.dendrogram()` 用于给出树状图中叶子的顺序或标签
- `loadings()` 用于打印因子分析的载荷
- `merge.dendrogram()` 用于合并两个树状图
- `order.dendrogram()` 用于给出树状图叶子的顺序或标签
- `prcomp()` 用于主成分分析
- `princomp()` 也用于主成分分析
- `promax()` 用于因子分析中的轴旋转
- `reorder.dendrogram()` 用于在保持初始约束的情况下重新排序树状图
- `rev.dendrogram()` 用于反转树状图中节点的顺序
- `str.dendrogram()` 用于显示树状图的内部结构
- `varimax()` 用于因子分析中的轴旋转

关于任何函数的更多信息，请在 R 提示符下输入 `?函数名`，其中 `函数名` 是函数的名称，或使用 R Studio 中的“帮助”选项卡。

`stats` 包还包含几个概率分布（参见第 9 章）；八个 `as.` 函数；六个 `is.` 函数；许多绘图函数——如 `heatmap()` 和 19 个 `plot.` 函数——这些函数专用于许多与模型函数相关的类；核估计中使用的函数；模型的辅助函数——如七个 `model.` 函数；七个 `na.` 函数——用于处理缺失数据；13 个用于模型输出的 `predict.` 函数；27 个用于打印输出的 `print.` 函数；以及九个用于汇总输出的 `summary.` 函数。

### graphics 包

graphics 包包含函数 `plot()`——许多 `plot.` 方法都是为此编写的。`plot()` 的辅助函数位于 `graphics` 中。还有几个用于特定类型图表（如直方图和条形图）的绘图函数。graphics 帮助页面的链接列表长达三页（`help(package=graphics)`）。在本节中，我们将介绍特定类型的图表以及与绘图相关的其他几个函数。

以下是 graphics 中执行特定类型绘图的函数：

- `assocplot()` 用于 Cohen-Friendly 关联图；用于列联表；可与任何逻辑型或数值型矩阵配合使用
- `barplot()` 用于条形图；接受向量或矩阵对象（必须是逻辑型或数值型模式）作为条形的高度
- `boxplot()` 用于箱线图；逻辑型或数值型向量、矩阵、数组、数据框以及某些列表可作为该函数的输入
- `bxp()` 用于摘要的箱线图
- `cdplot()` 用于条件密度图
- `coplot()` 用于使用条件变量的散点图
- `dotchart()` 用于 Cleveland 点图；可使用数值型向量和矩阵绘图
- `fourfoldplot()` 用于 2 x 2 x k 列联表的四重图
- `hist()` 用于直方图；可为数值型向量、矩阵和数组生成直方图
- `mosaicplot()` 用于马赛克图；接受向量、矩阵、数据框或数组形式的数值型或逻辑型参数；主要用于列联表
- `pairs()` 用于配对变量的散点图；接受数值型向量、矩阵和数据框作为输入；创建一个绘图矩阵
- `persp()` 用于透视图；进行三维绘图
- `pie()` 用于饼图；使用数值型向量、矩阵和数组作为输入
- `smoothScatter()` 用于散点图的平滑版本——带颜色；版权归 M. P. Wand 所有
- `spineplot()` 用于脊柱图；使用逻辑型、数值型或复数型矩阵作为绘图输入；逻辑型和复数型矩阵会被强制转换为数值型；专为二维列联表开发
- `stars()` 用于星形图或段图；使用数值型矩阵或数据框作为绘图输入
- `stem()` 用于茎叶图；使用数值型向量、矩阵或数组作为绘图输入
- `stripchart()` 用于一维散点图
- `sunflowerplot()` 用于向日葵图，这是一种散点图，其中重复的点会用向日葵叶子表示重复的点；使用逻辑型、数值型或复数型向量、矩阵或数据框作为绘图输入

graphics 中还有一些控制绘图屏幕的函数。函数 `splitscreen()` 及其辅助函数 `close.screen()`、`erase.screen()` 和 `screen()` 用于将绘图屏幕分割成多个区域并向这些区域绘图。函数 `frame()` 和 `plot.new()` 用于打开一个新的绘图框架。

函数 `par()` 类似于 `options()`——但专门用于绘图——并包含绘图的默认选项。这些选项可以随时更改。调用 `par()` 会打开一个新的绘图框架。要查看选项列表，请在没有参数的情况下调用 `par()`。

函数 `plot()` 是基本的绘图函数，拥有大量辅助函数，并为多种方法定义。本书不涵盖 `plot()`。

您可以通过在 R 提示符下输入 `?函数名`（其中 `函数名` 是函数的名称）或使用 R Studio 中的“帮助”选项卡来获取关于 `graphics` 中函数的更多信息。

