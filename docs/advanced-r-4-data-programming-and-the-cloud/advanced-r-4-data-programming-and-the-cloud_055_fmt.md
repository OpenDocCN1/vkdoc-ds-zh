# 第 9 章 其他数据管理包

```
## # A tibble: 1 x 2
##       M    SD
##   <dbl> <dbl>
## 1  3.06 0.436
```

假设我们想要获取数据，仅使用 `virginica` 物种，并计算萼片宽度的平均值。结果应该是一个仅包含一个变量的 `tibble`，即我们所需的均值。这就是管道运算符开始展现其优势的地方，因为这段代码非常易读。实际上，即使是非编程人员也可能理解这段代码的含义：

```r
diris %>% filter(Species == "virginica") %>% summarise(M = mean(Sepal.Width))
```

```
## # A tibble: 1 x 1
##       M
##   <dbl>
## 1  3.06
```

## 1 2.97

这种一次一步的思路经过调整，可以管理各种数据处理操作。回顾我们对 `data.table` 的研究，其第三个形式参数 `by =` 允许我们按列元素进行分组。对于 `tidyverse`，我们使用 `group_by()` 对列名进行操作，即可完成同样的任务。将我们的均值计算示例扩展到所有三个物种，现在我们得到一个两列的 `tibble`，每个物种占一行。我们还可以计算原始数据中多个列的均值：

```r
diris %>%
  group_by(Species) %>%
  summarise(
    mSW = mean(Sepal.Width),
    mPW = mean(Petal.Width))
```

```
## # A tibble: 3 x 3
##   Species     mSW   mPW
##   <chr>     <dbl> <dbl>
## 1 setosa     3.43 0.246
## 2 versicolor 2.77 1.33
## 3 virginica  2.97 2.03
```

这种链式结构，就像 `data.table` 的链式结构一样，可以无限（或近乎无限）地进行下去。为了找到前面数据中的相关性，我们只需在链的末尾添加一个使用 `cor()` 函数的步骤：

```r
diris %>%
  group_by(Species) %>%
  summarise(
    mSW = mean(Sepal.Width),
    mPW = mean(Petal.Width)) %>%
  summarise(r = cor(mSW, mPW))
```

```
## # A tibble: 1 x 1
##       r
##   <dbl>
## 1 -0.759
```

变量也可以创建其他变量。我们根据花瓣宽度是否大于或小于该物种的花瓣宽度中位数，创建一个新的布尔变量：

```r
diris <- diris %>%
  group_by(Species) %>%
  mutate(MedPW = Petal.Width > median(Petal.Width))
diris
```

```
## # A tibble: 150 x 8
## # Groups:   Species [3]
##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species     V
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>   <dbl>
##  1          5.1         3.5          1.4         0.2 setosa  0.280
##  2          4.9         3            1.4         0.2 setosa  0.280
##  3          4.7         3.2          1.3         0.2 setosa  0.26
##  4          4.6         3.1          1.5         0.2 setosa  0.3
##  5          5           3.6          1.4         0.2 setosa  0.280
## # ... with 145 more rows, and 2 more variables: V2 <dbl>, MedPW <lgl>
```

按多个变量分组也很容易。同时按物种和中位数花瓣宽度布尔值分组，我们重新计算花萼宽度和花瓣宽度的均值：

```r
diris %>%
  group_by(Species, MedPW) %>%
  summarise(
    mSW = mean(Sepal.Width),
    mPW = mean(Petal.Width))
```

```
## # A tibble: 6 x 4
## # Groups:   Species [3]
##   Species    MedPW   mSW   mPW
##   <chr>      <lgl> <dbl> <dbl>
## 1 setosa     FALSE  3.38 0.185
## 2 setosa     TRUE   3.54 0.375
## 3 versicolor FALSE  2.62 1.19
## 4 versicolor TRUE   2.96 1.50
## 5 virginica  FALSE  2.84 1.81
## 6 virginica  TRUE   3.13 2.27
```

再次强调，我们总是可以链式添加更多操作：

```r
diris %>%
  group_by(Species, MedPW) %>%
  summarise(
    mSW = mean(Sepal.Width),
    mPW = mean(Petal.Width)) %>%
  group_by(MedPW) %>%
  summarise(r = cor(mSW, mPW))
```

```
## # A tibble: 2 x 2
##   MedPW      r
##   <lgl>  <dbl>
## 1 FALSE -0.778
## 2 TRUE  -0.760
```

### 9.5 数据合并与重塑

不再重复我们在第 7 章中关于合并和重塑数据的讨论，我们注意到 `merge()` 函数在一定程度上仍然有效。由于 `tibble` 是建立在数据框之上的，所以它能够工作。然而，当合并发生时，它们会失去 `tibble` 的状态。因此，我们还要介绍 `*join()` 函数，它们更接近一些传统的数据库语言。

我们首先运行一小段代码来创建四个独立的数据对象。请注意，在实际应用中这很可能不是必需的，因为我们的数据通常来自不同的数据库：

```r
# 数据集一
diris <- diris %>%
  group_by(Species) %>%
  select(Species, Sepal.Width) %>%
  slice(1:3)
diris
```

```
## # A tibble: 9 x 2
## # Groups:   Species [3]
##   Species    Sepal.Width
##   <chr>            <dbl>
## 1 setosa             3.5
## 2 setosa             3
## 3 setosa             3.2
## 4 versicolor         3.2
## 5 versicolor         3.2
## 6 versicolor         3.1
## 7 virginica          3.3
## 8 virginica          2.7
## 9 virginica          3
```

```r
# 数据集二
diris2 <- diris %>% slice(c(1, 4, 7))
diris2
```

```
## # A tibble: 3 x 2
## # Groups:   Species [3]
##   Species    Sepal.Width
##   <chr>            <dbl>
## 1 setosa             3.5
## 2 versicolor         3.2
## 3 virginica          3.3
```

```r
# 数据集三
dalt1 <- tibble(
  Species = c("setosa", "setosa", "versicolor", "versicolor",
              "virginica", "virginica", "other", "other"),
  Type = c("wide", "wide", "wide", "wide",
           "narrow", "narrow", "moderate", "moderate"),
  MedPW = c(TRUE, FALSE, TRUE, FALSE, TRUE, FALSE, TRUE, FALSE))
dalt1
```

```
## # A tibble: 8 x 3
##   Species    Type     MedPW
##   <chr>      <chr>    <lgl>
## 1 setosa     wide     TRUE
## 2 setosa     wide     FALSE
## 3 versicolor wide     TRUE
## 4 versicolor wide     FALSE
## 5 virginica  narrow   TRUE
## 6 virginica  narrow   FALSE
## 7 other      moderate TRUE
## 8 other      moderate FALSE
```

```r
# 数据集四
dalt2 <- dalt1 %>% slice(c(1, 3))
dalt2
```

```
## # A tibble: 2 x 3
##   Species    Type  MedPW
##   <chr>      <chr> <lgl>
## 1 setosa     wide  TRUE
## 2 versicolor wide  TRUE
```

`merge()` 函数对两个数据集进行操作。对于像 `diris2` 和 `dalt2` 这样的一对一数据，默认的合并只保留两个数据集中都匹配的行。我们可以看到，对于这两个对象，`diris2` 中的 `virginica` 行被丢弃了：

```r
diris2 %>% merge(dalt2)
```

```
##   Species Sepal.Width Type MedPW
## 1 setosa          3.5 wide  TRUE
## 2 versicolor      3.2 wide  TRUE
```

如果我们希望保留 `diris2` 的所有三行，那么合并后就会有一些缺失数据。在这种情况下，由于我们任意选择 `diris2` 作为第一个形式参数，`all.x = TRUE` 会保留 `diris2` 的所有行：

```r
diris2 %>% merge(dalt2, all.x = TRUE)
```

```
##   Species Sepal.Width Type MedPW
## 1 setosa          3.5 wide  TRUE
## 2 versicolor      3.2 wide  TRUE
## 3 virginica       3.3 <NA>    NA
```

也可以设置第二个参数，即 `all.y = TRUE`，不过在本例中这样做没有明显区别。根据两个特定表的匹配程度，可能会产生不同的缺失变量模式。类似地，我们也可以设置保留所有表的所有行。这可能会产生最多的缺失数据位置；在我们的示例中，这样的合并与前面示例中显示的三行完全相同：

```r
diris2 %>% merge(dalt2, all.y = TRUE)
```

```
##   Species Sepal.Width Type MedPW
## 1 setosa          3.5 wide  TRUE
## 2 versicolor      3.2 wide  TRUE
```

```r
diris2 %>% merge(dalt2, all = TRUE)
```

```
##   Species Sepal.Width Type MedPW
## 1 setosa          3.5 wide  TRUE
## 2 versicolor      3.2 wide  TRUE
## 3 virginica       3.3 <NA>    NA
```

如前所述，这些结果不是 `tibble`。如果我们希望合并后再强制转换回 `tibble`，可以用 `as_tibble()` 包装它们。然而，`tidyverse` 也可以操作数据库风格的对象，正如你在第 10 章中将看到的。特别是，*结构化查询语言*（SQL）可以通过 `dplyr` 访问。SQL 有连接操作，我们现在考虑四种连接类型。

`left_join()` 会返回第一个对象的所有行，以及第一个和第二个对象的所有列。由于这是一个 `dplyr` 函数调用，我们得到一个 `tibble` 类作为返回输出：

```r
diris2 %>% left_join(dalt2)
```

```
## Joining, by = "Species"
## # A tibble: 3 x 4
## # Groups:   Species [3]
##   Species    Sepal.Width Type  MedPW
##   <chr>            <dbl> <chr> <lgl>
## 1 setosa             3.5 wide  TRUE
## 2 versicolor         3.2 wide  TRUE
## 3 virginica          3.3 NA    NA
```

`right_join()` 返回一个包含两个对象所有列以及*第二个*对象所有行的 `tibble`。互联网上的许多图表声称它们揭示了连接操作如何工作的真相。根据我们的经验，这些解释的准确性可能时好时坏。至少，大多数人会觉得许多此类图表令人困惑。我们的建议是，拿几个小的数据表，或者像我们这里一样自己创建一些数据。然后开始尝试连接操作。这就是“学骑自行车”的方法。与其讨论双轮运动的物理学，不如多练习，不要害怕在这个过程中擦伤膝盖：

```r
diris2 %>% right_join(dalt2)
```

```
## Joining, by = "Species"
## # A tibble: 2 x 4
## # Groups:   Species [2]
##   Species    Sepal.Width Type  MedPW
##   <chr>            <dbl> <chr> <lgl>
## 1 setosa             3.5 wide  TRUE
## 2 versicolor         3.2 wide  TRUE
```

在这种情况下，`inner_join()` 函数产生的结果与之前的右连接相同。但是，它只返回两个对象中都匹配的行。也许最奇怪的连接是 `anti_join()`，它返回第一个数据库中与第二个数据对象中的值不匹配的所有行。至于列，它只返回第一个对象的列：

```r
diris2 %>% inner_join(dalt2)
```

```
## Joining, by = "Species"
## # A tibble: 2 x 4
## # Groups:   Species [2]
##   Species    Sepal.Width Type  MedPW
##   <chr>            <dbl> <chr> <lgl>
## 1 setosa             3.5 wide  TRUE
## 2 versicolor         3.2 wide  TRUE
```

```r
diris2 %>% anti_join(dalt2)
```

```
## Joining, by = "Species"
## # A tibble: 1 x 2
## # Groups:   Species [1]
##   Species    Sepal.Width
##   <chr>            <dbl>
```

## 1 virginica 3.3

在连接和合并之间，有多种将数据组合到单个对象中的选项。请记住，您的目标是最终获得分析所需的数据。

有时，先将一个或多个数据集合合并到一个表中，然后使用本章前面介绍的技术将其缩减为您想要的数据集是有意义的。当然，如果您更熟悉合并操作，它们通常也能很好地工作。

如果您确实需要使用 tidyverse，`as_tibble()` 始终可以作为包装函数，将您的对象强制转换回它应有的样子。毕竟，`data.table` 和 tidyverse 的 tibbles 都是数据框的扩展。

我们的最后一个目标是数据重塑，如果每行有一个唯一的 ID，效果会更好。我们首先再次刷新我们的数据集。然后，我们引入 `n()`，这是一种引用行数的特殊方式，很像数据表中的 `.N`：

```r
diris <- as_tibble(iris)
diris <- diris %>% mutate(ID = 1:n())
diris
```

```
## # A tibble: 150 x 6
##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species    ID
##           <dbl>       <dbl>        <dbl>       <dbl> <chr>  <int>
##  1          5.1         3.5          1.4         0.2 setosa     1
##  2          4.9         3            1.4         0.2 setosa     2
##  3          4.7         3.2          1.3         0.2 setosa     3
##  4          4.6         3.1          1.5         0.2 setosa     4
##  5          5           3.6          1.4         0.2 setosa     5
## # ... with 145 more rows
```

为了将数据变长，我们使用 tidyr 函数 `pivot_longer()`。此函数接受我们数据中要修改为新格式的列列表。在这种情况下，它指的是我们的花萼和花瓣变量。`names_to` 参数使用一个特殊命令 `.value` 来指示我们希望新列包含旧列的值。相比之下，下一个元素 `Type` 将包含指示数据来自哪一列的信息。因为我们在 `names_to` 中有两个条目，所以我们的 `names_pattern` 期望有两个部分。使用正则表达式，我们用 `|` 表示逻辑“或”来指示变量部分：

```r
diris.long <-
  diris %>% pivot_longer(
    cols = c(Sepal.Length, Sepal.Width, Petal.Length, Petal.Width),
    names_to = c(".value", "Type"),
    names_pattern = "(Sepal|Petal).(Width|Length)"
  )
diris.long
```

```
## # A tibble: 300 x 5
##    Species    ID Type  Sepal Petal
##    <chr>   <int> <chr> <dbl> <dbl>
##  1 setosa      1 Length   5.1   1.4
##  2 setosa      1 Width    3.5   0.2
##  3 setosa      2 Length   4.9   1.4
##  4 setosa      2 Width    3     0.2
##  5 setosa      3 Length   4.7   1.3
## # ... with 295 more rows
```

我们将宽数据变长了。现在我们通过使用 `pivot_wider()` 来逆转这个过程。`names_from` 指示更宽的列名将从哪里来。分隔符默认为 `_`，因此我们将其改为句点。最后，`values_from` 包含我们数据值的列：

```r
diris.wide <- diris.long %>% pivot_wider(
  names_from = Type,
  names_sep = ".",
  values_from = c(Sepal, Petal)
)
diris.wide
```

```
## # A tibble: 150 x 6
##    Species    ID Sepal.Length Sepal.Width Petal.Length Petal.Width
##    <chr>   <int>        <dbl>       <dbl>        <dbl>       <dbl>
##  1 setosa      1          5.1         3.5          1.4         0.2
##  2 setosa      2          4.9         3            1.4         0.2
##  3 setosa      3          4.7         3.2          1.3         0.2
##  4 setosa      4          4.6         3.1          1.5         0.2
##  5 setosa      5          5           3.6          1.4         0.2
```