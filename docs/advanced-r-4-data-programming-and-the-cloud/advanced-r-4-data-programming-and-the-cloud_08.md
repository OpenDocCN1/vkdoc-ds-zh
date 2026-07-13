
# 第 9 章 其他数据管理包

## 9.1 排序

理解整洁方法的一个有益方式是，它使用动词来描述对数据的操作。在 `tidyverse` 中，数据通过 `arrange()` 函数进行排序，该函数将数据集作为其第一个参数：

```r
diris <- arrange(diris, Sepal.Length)
diris
```

```
## # A tibble: 150 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>  
## 1          4.3         3            1.1         0.1 setosa 
## 2          4.4         2.9          1.4         0.2 setosa 
## 3          4.4         3            1.3         0.2 setosa 
## 4          4.4         3.2          1.3         0.2 setosa 
## 5          4.5         2.3          1.3         0.3 setosa 
## # ... with 145 more rows
```

`tidyverse` 通过函数工作，这些函数通常遵循使用动词的惯例，意在表示正在对数据执行的操作。为了遵循英语常见的“名词-动词”句子结构，这些函数还使用了*管道操作符*。

管道操作符编码为 `%>%`，它消除了在每个函数内部引用数据集的需要。具体而言，它将左侧的数据对象（名词）传递到右侧函数（动词）的*第一个*形式参数中。

比较以下代码与之前的代码，看看第一个形式参数是如何现在位于左侧的：

```r
diris %>% arrange(Sepal.Length)
```

```
## # A tibble: 150 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>  
## 1          4.3         3            1.1         0.1 setosa 
## 2          4.4         2.9          1.4         0.2 setosa 
## 3          4.4         3            1.3         0.2 setosa 
## 4          4.4         3.2          1.3         0.2 setosa 
## 5          4.5         2.3          1.3         0.3 setosa 
## # ... with 145 more rows
```

## 9.2 选择与子集化

值得注意的是，如果某个函数未将*数据*保留为第一个形参，那么在右侧函数参数的该位置使用一个 `.`（句点或句号）将能使管道按预期工作。

管道操作符本身不会修改我们的数据集 `diris`。与往常一样，需要使用赋值操作符才能进行永久性更改。`arrange()` 函数在第一个参数之后接受额外的参数，以提供按这些其他列进行排序的功能。默认是升序排列，可以通过再次调用 `desc()` 函数来可选地修改为*降序*排列：

```r
diris <- diris %>% arrange(Sepal.Length, desc(Sepal.Width))
diris
```

```
## # A tibble: 150 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.3         3            1.1         0.1 setosa
## 2          4.4         3.2          1.3         0.2 setosa
## 3          4.4         3            1.3         0.2 setosa
## 4          4.4         2.9          1.4         0.2 setosa
## 5          4.5         2.3          1.3         0.3 setosa
## # ... with 145 more rows
```

通过 `anyDuplicated()` 可以查看整个数据集或特定列首次出现重复时的行索引。当对整个数据集调用此函数时，它会查找所有列之间的匹配，在此数据集中发生在第 78 行和第 79 行。`Sepal.Length` 中的重复变量出现在第三行，如下代码所示：

```r
diris %>% anyDuplicated()
## [1] 79

anyDuplicated(diris$Sepal.Length)
## [1] 3
```

`anyDuplicated()` 返回首次重复的行索引，而 `duplicated()` 则简单地告诉我们表中有多少次这样的重复。查看代码，我们看到在所有列完全匹配的情况下，第 79 行是唯一出现的情况。另一方面，萼片长度的重复可能首次发生在第 3 行，但还有更多重复：

```r
diris %>% duplicated() %>% table()
## .
## FALSE  TRUE
##   149     1

table(duplicated(diris$Sepal.Length))
##
## FALSE  TRUE
##    35   115
```

如果我们希望看到仅包含不同值的表格结果，`distinct()` 的用法与 `arrange()` 非常相似。第一个形参是数据，其余的是指定列名的变量。如果未指定任何列名，`distinct()` 将对所有列进行操作。回想 `duplicated()` 的调用结果，共有 149 个唯一的行：

```r
diris %>% distinct()
```

```
## # A tibble: 149 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.3         3            1.1         0.1 setosa
## 2          4.4         3.2          1.3         0.2 setosa
## 3          4.4         3            1.3         0.2 setosa
## 4          4.4         2.9          1.4         0.2 setosa
## 5          4.5         2.3          1.3         0.3 setosa
## # ... with 144 more rows
```

类似地，回想一下，如果只考虑萼片长度，则只有 35 个唯一的行。因此，使用 `distinct()` 的第二个形参来保存 `Sepal.Length` 列，在我们的 tibble 中只会产生 35 行。这里显示这些行的前五行：

```r
diris %>% distinct(Sepal.Length)
```

```
## # A tibble: 35 x 1
##   Sepal.Length
##          <dbl>
## 1          4.3
## 2          4.4
## 3          4.5
## 4          4.6
## 5          4.7
## # ... with 30 more rows
```

本节最后，再次调用 `distinct()`，并在第二和第三个形参中指定多个列。在这里，只要同时满足 `Sepal.Length` 和 `Sepal.Width` 不重复，该行就被视为是唯一的。请特别注意第 2–4 行，它们具有相同的 `Sepal.Length`，但这些行并未被视为重复，因为它们的 `Sepal.Width` 不同：

```r
diris %>% distinct(Sepal.Length, Sepal.Width)
```

```
## # A tibble: 117 x 2
##   Sepal.Length Sepal.Width
##          <dbl>       <dbl>
## 1          4.3         3
## 2          4.4         3.2
## 3          4.4         3
## 4          4.4         2.9
## 5          4.5         2.3
## # ... with 112 more rows
```

### 9.2 选择与子集化

在`tidyverse`中选择存储数据的部分与`data.table`的方法不同。请记住，`tidyverse`通过函数调用来工作。这些函数被设计为独立于底层数据结构。尽管如此，我们确实希望得到相同的结果。按位置选择行通过`slice()`函数实现，它接收两个形参。第一个是数据对象，在我们的例子中是`diris`，第二个允许我们选择特定的行。请记住我们正在使用`管道`操作符`%>%`。因此，我们的第一个形参*在*函数之外！下面两行代码只选择前五行，一行是传统的函数表示法，另一行使用了管道操作符。目前使用管道可能看起来稍微更简洁但有些多余。当我们开始执行更复杂的操作时，管道的真正优势会变得更加明显：

```r
slice(diris, 1:5)
```

```
## # A tibble: 5 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.3         3            1.1         0.1 setosa
## 2          4.4         3.2          1.3         0.2 setosa
## 3          4.4         3            1.3         0.2 setosa
## 4          4.4         2.9          1.4         0.2 setosa
## 5          4.5         2.3          1.3         0.3 setosa
```

```r
diris %>% slice(1:5)
```

```
## # A tibble: 5 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.3         3            1.1         0.1 setosa
## 2          4.4         3.2          1.3         0.2 setosa
## 3          4.4         3            1.3         0.2 setosa
## 4          4.4         2.9          1.4         0.2 setosa
## 5          4.5         2.3          1.3         0.3 setosa
```

如前所述，`-`可用于否定。因此，我们也可以通过丢弃第 1–145 行来仅保留最后五行：

```r
diris %>% slice(-(1:145))
```

```
## # A tibble: 5 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          7.7         3.8          6.7         2.2 virginica
## 2          7.7         3            6.1         2.3 virginica
## 3          7.7         2.8          6.7         2   virginica
## 4          7.7         2.6          6.9         2.3 virginica
## 5          7.9         3.8          6.4         2   virginica
```

除了直接选择行，我们还可以对它们设置逻辑条件。同样，这类参数属于函数调用的第二个形参。但在这种情况下，我们使用`filter()`而不是`slice()`。`filter()`函数使用逻辑索引。通过编写一个逻辑测试，很容易选择`Species`是或不是`setosa`的所有行：

```r
diris %>% filter(Species == "setosa")
```

```
## # A tibble: 50 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.3         3            1.1         0.1 setosa
## 2          4.4         3.2          1.3         0.2 setosa
## 3          4.4         3            1.3         0.2 setosa
## 4          4.4         2.9          1.4         0.2 setosa
## 5          4.5         2.3          1.3         0.3 setosa
## # ... with 45 more rows
```

```r
diris %>% filter(Species != "setosa")
```

```
## # A tibble: 100 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.9         2.5          4.5         1.7 virginica
## 2          4.9         2.4          3.3         1   versicolor
## 3          5           2.3          3.3         1   versicolor
## 4          5           2            3.5         1   versicolor
## 5          5.1         2.5          3           1.1 versicolor
## # ... with 95 more rows
```

也可以基于数值与不等号、等号或不等号的比较，以及逻辑索引来选择行。数值和字符测试可以混合匹配使用，以精确提取所需的行：

```r
iris %>% filter(Sepal.Length < 5 & Petal.Width > .2)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width    Species
## 1          4.6         3.4          1.4         0.3     setosa
## 2          4.5         2.3          1.3         0.3     setosa
## 3          4.8         3.0          1.4         0.3     setosa
## 4          4.9         2.4          3.3         1.0 versicolor
## 5          4.9         2.5          4.5         1.7  virginica
```

```r
diris %>% filter(Sepal.Length == 4.3 | Species != "setosa")
```

```
## # A tibble: 101 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.3         3            1.1         0.1 setosa
## 2          4.9         2.5          4.5         1.7 virginica
## 3          4.9         2.4          3.3         1   versicolor
## 4          5           2.3          3.3         1   versicolor
## 5          5           2            3.5         1   versicolor
```

## # ... 还有 **96** 行

若我们想在一列中查找多个匹配项，可使用`%in%`运算符，并配合一个感兴趣的取值列表。这使我们能够将编码与选择项分离，然后根据兴趣进行筛选：

```r
interest <- c(4.3, 4.4)
diris %>% filter(Sepal.Length %in% interest)
```

```
## # A tibble: 4 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.3         3            1.1         0.1 setosa
## 2          4.4         3.2          1.3         0.2 setosa
## 3          4.4         3            1.3         0.2 setosa
## 4          4.4         2.9          1.4         0.2 setosa
```

# 第九章 其他数据管理包

另外，如果我们希望排除具有某些特征的行，可以通过“不在”或 `%nin%` 运算符实现。这个运算符同样可用于移除异常值或错误数据点：

```r
diris %>% filter(Sepal.Length %nin% interest)
```

## # 一个 tibble: 146 x 5
## Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## <dbl> <dbl> <dbl> <dbl> <chr>
## 1 4.5 2.3 1.3 0.3 setosa
## 2 4.6 3.6 1 0.2 setosa
## 3 4.6 3.4 1.4 0.3 setosa
## 4 4.6 3.2 1.4 0.2 setosa
## 5 4.6 3.1 1.5 0.2 setosa
## # ... 还有 141 行

我们展示的最后一个筛选示例使用了多个参数。使用多个参数并非必需，但可以作为一种将代码分解为人类易读块的有用方式。

多个参数用逗号分隔，在逻辑上等同于 `&`：

```r
diris %>% filter(Sepal.Length == 4.3 | Species != "setosa", Petal.Width < 1.5)
```

## # 一个 tibble: 37 x 5
## Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## <dbl> <dbl> <dbl> <dbl> <chr>
## 1 4.3 3 1.1 0.1 setosa
## 2 4.9 2.4 3.3 1 versicolor
## 3 5 2.3 3.3 1 versicolor
## 4 5 2 3.5 1 versicolor
## 5 5.1 2.5 3 1.1 versicolor
## # ... 还有 32 行

到目前为止，我们从所有列中选择了行。如果我们只想选择特定的列，`select()` 函数将数据库作为其第一个形式参数，并在其余形式参数中提供列信息。对于表格结构中的命名列，选择一个或多个列只需列出列名即可。也可以按位置选择。

对比这些按名称和按位置的列参数，并注意两者都可以按任何需要的顺序进行列选择：

```r
diris %>% select(Sepal.Length, Sepal.Width)
```

## # 一个 tibble: 150 x 2
## Sepal.Length Sepal.Width
## <dbl> <dbl>
## 1 4.3 3
## 2 4.4 3.2
## 3 4.4 3
## 4 4.4 2.9
## 5 4.5 2.3
## # ... 还有 145 行

```r
diris %>% select(1, 5, 2)
```

## # 一个 tibble: 150 x 3
## Sepal.Length Species Sepal.Width
## <dbl> <chr> <dbl>
## 1 4.3 setosa 3
## 2 4.4 setosa 3.2
## 3 4.4 setosa 3
## 4 4.4 setosa 2.9
## 5 4.5 setosa 2.3
## # ... 还有 145 行

到目前为止，都是直接指定特定的列。如果需要根据程序或用户需求动态变化列，该怎么办？这种情况下，使用字符串引用是理想的，因为字符串很容易转换为变量。多个作为字符串的变量名可以作为单独的参数传递：

```r
diris %>% select("Sepal.Length", "Sepal.Width")
```

## # 一个 tibble: 150 x 2
## Sepal.Length Sepal.Width
## <dbl> <dbl>
## 1 4.3 3
## 2 4.4 3.2
## 3 4.4 3
## 4 4.4 2.9
## 5 4.5 2.3
## # ... 还有 145 行

这些可以放入一个变量中，例如 `colVar1`，该变量也可以包含多个列名：

```r
colVar1 <- c("Sepal.Length", "Sepal.Width")
diris %>% select(all_of(colVar1))
```

## # 一个 tibble: 150 x 2
## Sepal.Length Sepal.Width
## <dbl> <dbl>
## 1 4.3 3
## 2 4.4 3.2
## 3 4.4 3
## 4 4.4 2.9
## 5 4.5 2.3
## # ... 还有 145 行

此外，`select()` 有一些辅助函数，允许进行更复杂的选择。其中一个辅助函数的例子是 `any_of()`，它尝试在 `colVar2` 集合和 `diris` 集合的列名之间进行尽可能多的匹配。这些辅助函数的名称最近变化不小，因此即使你熟悉辅助函数，简要回顾一下也会有所帮助。注意 `colVar2` 的最后一个条目并非真实列名。尽管如此，由于使用了该辅助函数，仍然得到了正确的结果：

```r
colVar2 <- c("Sepal.Length", "Sepal.Width", "错误名称!")
diris %>% select(any_of(colVar2))
```

## # 一个 tibble: 150 x 2
## Sepal.Length Sepal.Width
## <dbl> <dbl>
## 1 4.3 3
## 2 4.4 3.2
## 3 4.4 3
## 4 4.4 2.9
## 5 4.5 2.3
## # ... 还有 145 行

# 第九章 其他数据管理包

到目前为止，我们的所有工作都返回另一个 tibble 作为输出。将原始数据提取为向量有几种技术。如果我们知道包含所需数据的列名，可以直接输入名称。类似地，如果我们的列存储在变量中，则可以调用该变量（确保一次只请求一列！）。无论哪种方法，控制台都会打印相同的数据。注意这些数据是向量，而非 tibble 结构。同样注意，我们将函数包裹在`head()`函数中以节省空间：

```R
head(diris[["Sepal.Length"]]) # 如果你有 R 变量，很简单
```

```
## [1] 4.3 4.4 4.4 4.4 4.5 4.6
```

```R
head(diris[[colVar1[1]]]) # 如果你知道位置，很简单
```

```
## [1] 4.3 4.4 4.4 4.4 4.5 4.6
```

```R
head(diris$Sepal.Length) # 输入简单
```

```
## [1] 4.3 4.4 4.4 4.4 4.5 4.6
```

排除一列或多列使用取反操作。同样，要排除的列可以通过位置或直接命名来引用：

```R
diris %>% select(-1)
```

```
## # A tibble: 150 × 4
##   Sepal.Width Petal.Length Petal.Width Species
##         <dbl>        <dbl>       <dbl> <chr>
## 1         3            1.1         0.1 setosa
## 2         3.2          1.3         0.2 setosa
## 3         3            1.3         0.2 setosa
## 4         2.9          1.4         0.2 setosa
## 5         2.3          1.3         0.3 setosa
## # … with 145 more `行`
```

```R
diris %>% select(-Sepal.Length, -Petal.Width)
```

```
## # A tibble: 150 × 3
##   Sepal.Width Petal.Length Species
##         <dbl>        <dbl> <chr>
## 1         3            1.1 setosa
## 2         3.2          1.3 setosa
## 3         3            1.3 setosa
## 4         2.9          1.4 setosa
## 5         2.3          1.3 setosa
## # … with 145 more `行`
```

当需要排除变量时，我们设置一个变量来保存该向量，然后直接对该变量取反即可。或者，我们可以结合辅助函数使用取反：

```R
diris %>% select(-colVar1)
```

```
## Warning: Using an external vector in selections was deprecated in tidyselect 1.1.0.
## ℹ Please use `all_of(colVar1)` instead of `colVar1` to silence this message.
## ℹ See <https://tidyselect.r-lib.org/reference/faq-external-vector.html>.
## This message is displayed once per session.
## # A tibble: 150 × 3
##   Petal.Length Petal.Width Species
##          <dbl>       <dbl> <chr>
## 1          1.1         0.1 setosa
## 2          1.3         0.2 setosa
## 3          1.3         0.2 setosa
## 4          1.4         0.2 setosa
## 5          1.3         0.3 setosa
## # … with 145 more `行`
```

```R
diris %>% select(-any_of(colVar2))
```

```
## # A tibble: 150 × 3
##   Petal.Length Petal.Width Species
##          <dbl>       <dbl> <chr>
## 1          1.1         0.1 setosa
## 2          1.3         0.2 setosa
## 3          1.3         0.2 setosa
## 4          1.4         0.2 setosa
## 5          1.3         0.3 setosa
## # … with 145 more `行`
```

# 第九章 其他数据管理包

现在已经使用了几次`any_of()`辅助函数，我们简要地再看三个辅助函数。它们是`starts_with()`、`ends_with()`和`contains()`，允许我们在不输入完整列名的情况下选择变量。

它们默认忽略字母大小写，尽管这是一个可以设置为`FALSE`的选项。

tidyverse方法的一个优点是其专注于创建以动词命名的、完成单一特定任务的函数，这样可以很容易地从函数名看出其功能：

```
diris %>% select(starts_with("sepal"))
## # 一个 tibble: 150 x 2
## Sepal.Length Sepal.Width
## <dbl> <dbl>
## 1 4.3 3
## 2 4.4 3.2
## 3 4.4 3
## 4 4.4 2.9
## 5 4.5 2.3
## # ... 还有 145 **行**
```

```
diris %>% select(starts_with("sepal", ignore.case = FALSE))
```

# A tibble: 150 x 0

我们要介绍的最后一个辅助函数是 `matches()`。该函数使用正则表达式（如下所示）来匹配特定的列模式。本书不会对正则表达式进行完整讨论。然而，在下面的表达式中，模式匹配的是以至少一个字母字符开头、后跟一个标点符号、再跟至少一个字母字符的字符串：

```
diris %>% select(matches("ˆ[[:alpha:]]+[[:punct:]]{1}[[:alpha:]]+"))
```

# A `tibble`: 150 x 4

```
## Sepal.Length Sepal.Width Petal.Length Petal.Width
## <dbl> <dbl> <dbl> <dbl>
## 1 4.3 3 1.1 0.1
## 2 4.4 3.2 1.3 0.2
## 3 4.4 3 1.3 0.2
```

# 第 9 章 其他数据管理包

```
## 4 4.4 2.9 1.4 0.2
## 5 4.5 2.3 1.3 0.3
## # ... with 145 more rows
```

通过这最后一段代码，我们对 `tidyverse` 子集操作的学习就告一段落了。接下来，我们将注意力转向变量重命名和排序。

## 9.3 变量重命名与排序

在数据清理过程中，列名需要重命名或重新排序是一个令人遗憾但常见的环节。在一个数据库中合理（或“合法”）的名称，在另一个数据库中可能无法使用。许多专有数据管理系统都有固定的、不含糊的名称。查看与 `tidyverse` 对象关联的名称，需要对我们数据集 `diris` 调用常规的 `names()` 或 `colnames()` 函数。无论哪种方式，在当前列顺序下，我们都能得到相同的结果：

```
diris %>% names()
```

```
## [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"
## [5] "Species"
```

```
diris %>% colnames()
```

```
## [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"
## [5] "Species"
```

要更改名称，请对数据对象调用 `rename()` 函数，并使用以下格式在第二个形式参数中设置新名称：`新名称 = 旧名称`。

可以通过将多个列作为附加参数（例如，在第三个或第四个形式参数中）一次性重命名。通过将 `name()` 的结果与旧结果进行比较，来确认更改是否生效。在以下代码中，我们将数据中 `Sepal` 和 `Length` 之间的句点删除：

```
diris <- diris %>% rename(SepalLength = Sepal.Length)
```

```
diris %>% names()
```

```
## [1] "SepalLength"  "Sepal.Width"  "Petal.Length" "Petal.Width"
## [5] "Species"
```

# 第 9 章 其他数据管理包

要将列重新排序，使长度和宽度分别配对，只需使用`select()`函数并为你数据对象重新赋值。请务必记住，我们已经将`SepalLength`重命名，中间不再有句点！

```
diris <- diris %>% select(
  SepalLength,
  Petal.Length,
  Sepal.Width,
  Petal.Width,
  Species)
```

```
diris
```

```
## # A tibble: 150 x 5
##    SepalLength Petal.Length Sepal.Width Petal.Width Species
##         <dbl>        <dbl>       <dbl>       <dbl> <chr>
## 1         4.3          1.1         3           0.1 setosa
## 2         4.4          1.3         3.2         0.2 setosa
## 3         4.4          1.3         3           0.2 setosa
## 4         4.4          1.4         2.9         0.2 setosa
## 5         4.5          1.3         2.3         0.3 setosa
## # ... with 145 more rows
```

早些时候，你看到numeric选择调用可以重新排序列，因此我们用最后一个重新排序的示例来结束本节。在这里，我们使用一个变量来保存数字位置，尽管我们也可以使用字符名称代替数字。然后我们使用`select()`函数将我们的列重新组织回其原始顺序：`colVar3 <- c(1, 3, 2, 4, 5)`

```
diris %>% select(all_of(colVar3))
```

```
## # A tibble: 150 x 5
##    SepalLength Sepal.Width Petal.Length Petal.Width Species
##         <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1         4.3         3            1.1         0.1 setosa
## 2         4.4         3.2          1.3         0.2 setosa
## 3         4.4         3            1.3         0.2 setosa
## 4         4.4         2.9          1.4         0.2 setosa
## 5         4.5         2.3          1.3         0.3 setosa
## # ... with 145 more rows
```

# 第 9 章 其他数据管理包

## 9.4 数据计算与创建变量

既然你已经对如何在 `tidyverse` 中管理原始数据有了很好的了解，我们将重点关注如何从旧数据创建新数据。为简单起见，我们从原始 `iris` 数据集重新创建我们的 `diris` 数据集。然后我们使用 `mutate()` 函数创建或替换列。该函数将常规的第一个参数，而后面的形式参数则表达如何创建新列。无论是创建一个还是多个，只需创建表达式让 `tidyverse` 知道要追加到 `tibble` 中什么内容。请注意，在这里我们没有将这种变异重新赋值回 `diris`。因此，这个变异不会被保存下来。在探索性分析中，从控制台运行此类变异以便理解数据特征是很方便的：

```
diris <- as_tibble(iris)
diris %>% mutate(V0 = 0, X1 = 1L, X2 = 2L)
```

```
## # A tibble: 150 x 8
##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species    V0
##           <dbl>       <dbl>        <dbl>       <dbl> <chr>   <dbl>
## 1          5.1         3.5          1.4         0.2 setosa      0
## 2          4.9         3            1.4         0.2 setosa      0
## 3          4.7         3.2          1.3         0.2 setosa      0
## 4          4.6         3.1          1.5         0.2 setosa      0
## 5          5           3.6          1.4         0.2 setosa      0
## # ... with 145 more rows, and 2 more variables: X1 <int>, X2 <int>
```

当然，新数据可能并不全是创建全新的列。相反，我们也可以基于现有信息创建新的计算列。也许我们想要一个名为 `V` 的新列，它是 `Petal.Length` 和 `Petal.Width` 的乘积：

```
diris <- mutate(diris, V = Petal.Length * Petal.Width)
diris
```

```
## # A tibble: 150 x 6
##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species     V
##           <dbl>       <dbl>        <dbl>       <dbl> <chr>   <dbl>
## 1          5.1         3.5          1.4         0.2 setosa  0.280
## 2          4.9         3            1.4         0.2 setosa  0.280
## 3          4.7         3.2          1.3         0.2 setosa  0.26
```

# 第 9 章 其他数据管理包

```
## 4          4.6         3.1          1.5         0.2 setosa  0.3
## 5          5           3.6          1.4         0.2 setosa  0.280
## # ... with 145 more rows
```

更复杂的计算仅在满足某些条件时执行。在这种情况下，`if_else()` 需要三个参数。第一个是要测试 if/else 语句的逻辑条件。第二个和第三个形式参数指示在 `if` 或 `else` 情况下要执行的操作。此外，`mutate()` 不仅允许我们创建包含数据的列，还可以更改现有列的数据。因此，如果我们第一次创建列 `V2` 时，仅对 setosa 物种计算花瓣宽度和长度之间的乘法关系还不够，我们可以将其扩展到对 virginica 物种进行平方根运算。为了确保我们看到每个物种的一个样本，我们使用 `slice()` 函数查看三个特定的行：

```
diris <- diris %>% mutate(V2 = if_else(Species == "setosa", Petal.Length * Petal.Width, NA_real_))
diris <- diris %>% mutate(V2 = if_else(Species == "virginica", sqrt(Petal.Length * Petal.Width), V2))
diris %>% slice(c(1, 51, 101))
```

```
## # A tibble: 3 x 7
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species     V    V2
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>   <dbl> <dbl>
## 1          5.1         3.5          1.4         0.2 setosa  0.280 0.280
## 2          7           3.2          4.7         1.4 versic˜ 6.58  2.56
## 3          6.3         3.3          6           2.5 virgin˜15     3.87
```

并非必须创建新列；也可以直接使用 `summarise()` 函数计算表格上的信息。第一个形式参数一如既往地告诉函数使用哪个数据（尽管由于管道符的使用而隐藏了），其余参数用于保存各个计算结果。请注意，可以编码一个或多个计算。在这里，我们演示了算术均值 `mean()` 和标准差 `sd()` 的两种计算：

```
## summarise 中的计算允许进行多项操作
diris %>% summarise(M = mean(Sepal.Width), SD = sd(Sepal.Width))
```

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

# ……还有 145 **多**行

### 9.6 小结

`dplyr`、`tibble` 和 `tidyr` 包能让数据处理更具可读性。通过将对数据的操作与数据本身分离，我们可以学习相对有限的操作，并将其应用于任何数据。在幕后，tidyverse 会翻译这些函数调用来修改数据。在下一章中，你将看到这如何使我们能够快速访问大数据。表 9-1 描述了本章使用的关键函数。

## 第 9 章 其他数据管理包

**表 9-1.** 本章所述关键函数及功能摘要

| **函数** | **功能** |
|---|---|
| `as_tibble()` | 将数据对象（例如数据框）转换为 tibble。 |
| `arrange()` | 按指定列升序排列，若在列名上使用`desc()`则降序排列。 |
| `distinct()` | 这是 dplyr 对 `unique()` 的回应。它只返回不重复的行。 |
| `slice()` | 从 tibble 中切出指定的行。 |
| `filter()` | 根据匹配的特征过滤行。 |
| `select()` | 根据名称选择特定列。可通过辅助函数扩展功能：`one_of()`、`starts_with()`、`ends_with()`、`contains()`、`matches()`、`num_range()` 和 `everything()`。 |
| `rename()` | 重命名列。 |
| `mutate()` | 允许对现有 tibble 进行更改或变动。添加/删除/修改列数据是常见的用途。 |
| `summarise()` | 基于原始 tibble 的计算结果创建新的 tibble。 |
| `%>%` | 管道操作符避免函数嵌套，允许更简洁的代码来串联多个函数。 |
| `group_by()` | 允许按列分组。 |
| `left_join()` | 连接是处理数据的 SQL 方式。左连接保留第一个对象的所有行。 |
| `right_join()` | 保留第二个对象的所有行。 |
| `inner_join()` | 返回两个变量的所有列，以及第一个对象中与第二个数据对象匹配的所有行。 |
| `anti_join()` | 仅返回第一个数据对象中唯一、无法关联的部分。如果第二个对象旨在扩展第一个对象的信息，这将非常有用，因为它提供了一种快速查看剩余内容的方法。 |
| `pivot_longer()` | 将宽数据熔化变长。 |
| `pivot_wider()` | 将长数据转换为宽格式。 |

## 第 10 章

## 读取大数据

既然你已经具备操作数据的能力，现在是时候花点时间考虑数据存储在哪里了。R 在内存（随机存取存储器，RAM）中工作，而不是硬盘。快速检查你的系统设置应该能显示你拥有的内存大小。我们（作者）在真实系统中使用 16 到 128 GB 的内存，更大的数字是一种昂贵但方便的习惯。R 不能分析超过可用 RAM 的数据。当考虑到现代研究中遇到的越来越大的文件大小时，这一点变得尤为重要。RAM 中数据过多会导致 R 和你的计算机系统停止工作。因此，必须确保恰好合适的数据进入 RAM。

数据如何进入 RAM？较小的数据通常以逗号分隔值（CSV）文件或易于转换为 CSV 的文件形式存在。然而，较大的数据通常位于称为数据库的其他地方。虽然大多数数据库都有将文件导出为 CSV 的能力，但这并不总是方便。能够直接从数据库访问数据，并且（潜在地）能够将数据从 R 写入数据库，是成功分析更大数据的关键技能。

为简化复杂性，本章我们在本地机器上安装并托管一个数据库服务器。也就是说，大多数云计算解决方案都提供了在 Web 上托管数据库的选项。除了安装/设置过程之外，无论数据库的具体位置如何，连接和对接 R 与数据库的机制都是相同的。

我们将使用的数据库是 PostgreSQL，它既开源又流行。这个高级数据库内部理解 30 多种数据类型。我们推荐 Neil Matthew 和 Richard Stones 所著的《Beginning Databases with PostgreSQL》（Apress, 2005）。我们将引导你在本地机器上安装一个实践版本。我们的目标是让你尽快能够在此数据库中对数据使用 R。

更高级的数据库操作很可能不应在 R 中完成。

© Matt Wiley 和 Joshua F. Wiley 2020  
M. Wiley 和 J. F. Wiley，《Advanced R 4 Data Programming and the Cloud》，  
[`doi.org/10.1007/978-1-4842-5973-3_10`](https://doi.org/10.1007/978-1-4842-5973-3_10#DOI)

