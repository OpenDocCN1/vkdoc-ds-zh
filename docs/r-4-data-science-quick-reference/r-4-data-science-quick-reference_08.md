# 数据操作：筛选、排序与修改

## 数据表展示示例

一个包含22行5列的数据表如下所示：

```r
## # A tibble: 22 × 5
##    Sepal.Length Sepal.Width Petal.Length
##   <dbl>      <dbl>       <dbl>
## 1          4.9          3           1.4
## 2          4.7          3.2         1.3
## 3          4.6          3.1         1.5
## # . . . with 19 more rows, and 2 more variables:
## # Petal.Width <dbl>, Species <fct>
```

你可以使用函数或 lambda 表达式作为谓词。

考虑一个表格中某些数值包含缺失数据的情况。如果我们想筛选出所有值都大于三的行，我们可以使用`filter_all()`：

```r
df |> filter_all(all_vars(. > 3))

## # A tibble: 1 × 3
##       A     B     C
##   <dbl> <dbl> <dbl>
## 1    11    12    13
```

它删除了前两行和最后一行，因为它们都包含小于或等于三的值。它还删除了第三行，因为`NA`不被视为大于三。我们可以将测试限制在不包含缺失值的列上：

```r
df |> filter_if(~ all(!is.na(.)), all_vars(. > 3))

## # A tibble: 3 × 3
##       A     B     C
##   <dbl> <dbl> <dbl>
## 1     4     5    NA
## 2    11    12    13
## 3    22    22     1
```

这会将列`C`排除在测试之外。这意味着即使`C`的值小于三，我们也保留了最后一行。

如果你想保留中间两行，但不要第一行或最后一行，可以编写一个更复杂的谓词，并使用`filter_all()`。

```r
df |> filter_all(all_vars(is.na(.) | . > 3))

## # A tibble: 2 × 3
##       A     B     C
##   <dbl> <dbl> <dbl>
## 1     4     5    NA
## 2    11    12    13
```

你需要使用向量“或”运算符`|`。表达式必须是向量表达式，因此不能使用`||`。

许多`dplyr`函数都有`_all`、`_at`和`_if`变体，但此处我仅描述常用的函数。其他函数请参考软件包文档。

### 排序

如果要按选定列的值对行进行排序，所需的函数是`arrange()`。你可以按一列或多列排序：

```r
iris_df |>
  arrange(Petal.Length) |>
  print(n = 5)

## # A tibble: 150 × 5
##    Sepal.Length Sepal.Width Petal.Length
##   <dbl>      <dbl>       <dbl>
## 1          4.6         3.6          1
## 2          4.3         3            1.1
## 3          5.8         4            1.2
## 4          5           3.2          1.2
## 5          4.7         3.2          1.3
## # . . . with 145 more rows, and 2 more variables:
## # Petal.Width <dbl>, Species <fct>
```

```r
iris_df |>
  arrange(Sepal.Length, Petal.Length) |>
  print(n = 5)

## # A tibble: 150 × 5
##    Sepal.Length Sepal.Width Petal.Length
##   <dbl>      <dbl>       <dbl>
## 1          4.3         3            1.1
## 2          4.4         3            1.3
## 3          4.4         3.2          1.3
## 4          4.4         2.9          1.4
## 5          4.5         2.3          1.3
## # . . . with 145 more rows, and 2 more variables:
## # Petal.Width <dbl>, Species <fct>
```

如果要按降序排序，可以使用函数`desc()`。

```r
iris_df |>
  arrange(desc(Petal.Length)) |>
  print(n = 5)

## # A tibble: 150 × 5
##    Sepal.Length Sepal.Width Petal.Length
##   <dbl>      <dbl>       <dbl>
## 1          7.7         2.6          6.9
## 2          7.7         3.8          6.7
## 3          7.7         2.8          6.7
## 4          7.6         3            6.6
## 5          7.9         3.8          6.4
## # . . . with 145 more rows, and 2 more variables:
## # Petal.Width <dbl>, Species <fct>
```

### 修改数据框

在处理数据框时，我们通常希望根据表中的变量（列）计算值。仅靠筛选行和列是不够的。

`mutate()`函数允许我们根据涉及任何现有列的表达式，向数据框添加新列。考虑一个包含宽度和高度的表格。

```r
df <- tribble(
  ~height, ~width,
  10,     12,
  42,     24,
  14,     12
)
```

我们可以使用`mutate()`和以下表达式添加一个面积列：

```r
df |> mutate(area = height * width)

## # A tibble: 3 × 3
##   height width  area
##   <dbl> <dbl> <dbl>
## 1     10    12   120
## 2     42    24  1008
## 3     14    12   168
```

如果向数据框添加列，可以引用已经添加的变量。例如，如果高度和宽度数据以英寸为单位，而你想转换为厘米，并且还想要平方厘米的面积，可以这样做：

```r
cm_per_inch <- 2.54
df |> mutate(
  height_cm = cm_per_inch * height,
  width_cm = cm_per_inch * width,
  area_cm = height_cm * width_cm
)

## # A tibble: 3 × 5
##   height width height_cm width_cm area_cm
##   <dbl> <dbl>     <dbl>    <dbl>   <dbl>
## 1     10    12      25.4     30.5   774.
## 2     42    24     107.      61.0  6503.
## 3     14    12      35.6     30.5  1084.
```

然而，以下表达式将无法工作，因为你无法在`mutate()`调用中引用稍后添加的变量：

```r
df %>% mutate(
  area_cm = height_cm * width_cm,
  height_cm = cm_per_inch * height,
  width_cm = cm_per_inch * width
)

## Error in `mutate()`:
## ! 计算 `area_cm = height_cm * width_cm` 时出现问题。
## 由以下错误引起：
## ! 找不到对象 'height_cm'
```

如果不为新变量命名，即调用`mutate()`时不使用命名参数，那么表达式将成为变量名：

```r
df |> mutate(cm_per_inch * height)

## # A tibble: 3 × 3
##   height width `cm_per_inch * height`
##   <dbl> <dbl>                   <dbl>
## 1     10    12                   25.4
## 2     42    24                  107.
```

在此示例中，单位（英寸和厘米）未与数据一起编码，在未来的计算中可能会混淆不同的单位。如果使用 `units` 包（不属于 Tidyverse），可以显式指定单位，在合理的情况下自动进行单位转换，并且还能获得单位类型安全性作为额外好处。

```r
library(units)

df |> mutate(
  height_in = units::as_units(height, "in"),
  width_in = units::as_units(width, "in"),
  area_in = height_in * width_in,
  height_cm = units::set_units(height_in, "cm"),
  width_cm = units::set_units(width_in, "cm"),
  area_cm = units::set_units(area_in, "cm^2")
)
```

```
# A tibble: 3 × 8
  height width height_in width_in area_in        height_cm width_cm area_cm
   <dbl> <dbl>     <dbl>    <dbl>   <dbl>          <dbl>    <dbl>   <dbl>
1     10    12        10        12     120          25.4    30.5      168.
2     42    24        42        24    1008          107.     61.0     6451.
3     14    12        14        12     168          35.6    30.5      336.
# ℹ 3 more variables: height_cm [cm], width_cm [cm], area_cm [cm²]
```

这是可行的，因为 `tibble` 可以包含各种类型的类，`units` 包正是利用了这一点。`Tibble` 使用一个接口，允许你定义自己的类应如何显示，但这超出了本书的范围。

之前，我们为原始数据表创建了携带单位的值，但原始的高度和宽度列仍然存在。我们可以使用 `select()` 来移除它们，但函数 `transmute()` 已经为我们完成了这项工作。

```r
df %>% transmute(
  height_in = units::as_units(height, "in"),
  width_in = units::as_units(width, "in"),
  area_in = height_in * width_in,
  height_cm = units::set_units(height_in, "cm"),
  width_cm = units::set_units(width_in, "cm"),
  area_cm = units::set_units(area_in, "cm²")
)
```

```
# A tibble: 3 × 6
  height_in width_in area_in height_cm width_cm
       <dbl>    <dbl>   <dbl>     <dbl>    <dbl>
1        10       12     120      25.4     30.5
2        42       24    1008     107.      61.0
3        14       12     168      35.6     30.5
# ℹ 1 more variable: area_cm [cm²]
```

当你使用 `transmute()` 而非 `mutate()` 时，未赋值的变量将被移除。

与过滤操作类似，在转换数据框时使用的表达式必须是向量化表达式。请看以下示例：

```r
df %>% mutate(~ if (.x < 0) -.x else .x)
```

```
Error in `mutate()`:
! Problem while computing `..1 = ~if (.x < 0) -.x else .x`.
x `..1` must be a vector, not a `formula` object.
```

该操作失败，因为函数 `my_abs()`（我自定义的 `abs()` 函数版本）不是向量化表达式。由于 `if` 表达式中的 `x` 使然，它并非向量化表达式。

如果我们使用内置的 `abs()` 函数，那么该表达式就没有问题；它支持向量运算。

```r
df %>% mutate(abs(x))
```

```
# A tibble: 3 × 2
      x `abs(x)`
   <dbl>    <dbl>
1  11.5    11.5
2  20.9    20.9
3  17.2    17.2
```

如果你使用 `ifelse()` 这种 `if` 表达式的向量化版本，就能得到一个向量化表达式。

```r
df %>% mutate(ifelse(x < 0, -x, x))
```

```
# A tibble: 3 × 2
      x `ifelse(x < 0, -x, x)`
   <dbl>                 <dbl>
1  11.5                  11.5
2  20.9                  20.9
3  17.2                  17.2
```

或者，你也可以使用 `Vectorize()` 函数，将一个不处理向量的函数转化为向量化表达式。

```r
my_abs <- Vectorize(function(x) if (x < 0) -x else x)

df %>% mutate(my_abs(x))
```

```
# A tibble: 3 × 2
      x `my_abs(x)`
   <dbl>      <dbl>
1  11.5       11.5
2  20.9       20.9
3  17.2       17.2
```

如果你需要将输入映射到多个不同的输出值，你可以无限嵌套 `ifelse()` 表达式，但这会导致代码难以阅读。`case_when()` 函数能极大地缓解这个问题。你可以为其提供一组谓词及对应的表达式，它将返回第一个匹配谓词所对应的表达式。

请看以下示例，我们使用 `case_when()` 根据变量 `x` 的值对其进行分类：

```r
df %>%
  mutate(
    x_category = case_when(
      x - mean(x) > 2 * sd(x) ~ "large",
      x - mean(x) < -2 * sd(x) ~ "small",
      TRUE                     ~ "medium"
    )
  ) |>
  print(n = 3)
```

```
# A tibble: 100 × 2
       x x_category
    <dbl> <chr>
1  -0.736 medium
2  -0.478 medium
3   0.796 medium
# ℹ 97 more rows
```

其中的 `TRUE` 行对应于 `if-else` 语句中的 `else` 部分。

### 分组与汇总

在许多分析中，我们需要数据的汇总统计。如果你能将一个或多个列映射为单个汇总值，可以使用 `summarise()` 函数。

```r
df %>% summarise(mean_x = mean(x), mean_y = mean(y))
```

```
# A tibble: 1 × 2
  mean_x mean_y
   <dbl>  <dbl>
1 -0.154 0.0224
```

在此示例中，我们将数据汇总为变量 `x` 和 `y` 的均值。

如果你将数据拆分为不同类别，并希望按组处理数据，可以使用 `group_by()` 函数。

```r
classify <- function(x) {
  case_when(
    x - mean(x) > 2 * sd(x) ~ "large",
    x - mean(x) < -2 * sd(x) ~ "small",
    TRUE                     ~ "medium"
  )
}

df %>% mutate(x_category = classify(x)) |>
  group_by(x_category) |>
  print(n = 3)
```

```
# A tibble: 100 × 3
# Groups:    x_category [3]
       x       y x_category
```

##    <dbl>   <dbl> <chr>

## 1  -2.08 -1.52   medium

## 2  0.580 -0.990  medium

## 3 -0.995 -0.443  medium

## # . . . with 97 more rows

结果是一个数据框，在打印时看起来与分组前没有区别。然而，在标题中你会注意到这一行：

# Groups: x_category [3]

这表明数据分组后*确实*发生了变化。当你将分组与汇总结合使用时，这一点会变得很明显。

```r
df %>% mutate(x_category = classify(x)) |>
  group_by(x_category) |>
  summarise(mean_x = mean(x), no_x = n())
## # A tibble: 3 × 3
##   x_category mean_x  no_x
##   <chr>       <dbl> <int>
## 1 large       2.04      2
## 2 medium     -0.155    96
## 3 small      -2.30      2
```

当你对数据进行分组然后汇总时，会得到每个组的汇总统计。此处我们计算了每个类别的均值和观测数量（函数 `n()` 返回观测数量）。

你可以使用 `group_vars()` 函数获取数据的分组变量。

```r
df %>% mutate(x_category = classify(x)) |>
  group_by(x_category) |>
  group_vars()
## [1] "x_category"
```

你可以依据多个变量对数据进行分组。

```r
df <- df %>% 
  mutate(
    x_category = classify(x),
    y_category = classify(y)
  )
df %>% group_by(x_category, y_category) |>
  group_vars()
## [1] "x_category" "y_category"
```

如果这样做，你将获得每个分组变量组合的汇总结果。

```r
df %>% group_by(x_category, y_category) |>
  summarise(mean_x = mean(x), mean_y = mean(y))
## `summarise()` has grouped output by 'x_category'. You can override using the
## `.groups` argument.
## # A tibble: 5 × 4
## # Groups: x_category [3]
##   x_category y_category mean_x mean_y
##   <chr>      <chr>       <dbl>  <dbl>
## 1 large    medium    2.08     1.33
## 2 medium   large     0.808    2.69
## 3 medium   medium    0.0755  0.141
## 4 medium   small     1.05    -2.38
## 5 small    medium   -2.55    -1.12
```

你收到的通知显示输出已“按 'x_category' 分组输出”，这意味着：通过调用 `summarise()` 得到的表格仍然按 `x_category` 分组，这也可以在打印结果的标题行中看到（`Groups: x_category [3]` 这一行）。

当你按多个变量分组时，效果相当于逐个添加分组，你也可以通过多次调用 `group_by()` 来实现这一点。不过，这时需要使用参数 `.add = TRUE` 来添加分组，而不是替换分组——如果不使用此参数，`group_by()` 会执行替换操作。

```r
### 同时添加两个类别
df %>% group_by(x_category, y_category) |> group_vars()
## [1] "x_category" "y_category"

### 分两步添加类别
df %>% group_by(x_category) |> group_by(y_category, .add = TRUE) |> group_vars()
## [1] "x_category" "y_category"

### 用一个分组替换另一个分组
df %>% group_by(x_category) |> group_by(y_category) |> group_vars()
## [1] "y_category"
```

当你进行汇总时，会移除你创建的最后一个分组——因为该汇总在每个组中本来就只剩下一行数据，所以它不再有用——但其他分组会保留。这在早期版本的 `dplyr` 中是默认行为，至今仍是，但这也是一个常见的错误来源。一些数据科学家期望这种分组保留行为，另一些则期望所有分组都被移除，如果你的预期未被满足，后续分析很可能出错；如果你认为自己正在对整个数据集进行操作，但所有操作都按一个或多个变量分组，那么你很可能会得到不期望的结果。

因此，`summarise()` 现在引入了参数 `.groups`，你可以在其中指定所需的行为。目前该参数还不是必须的，因为那会破坏大量现有代码的向后兼容性，但你会在后面看到我们之前提到的关于使用它的提示信息。

```r
### 删除我们创建的最后一个分组
df |> group_by(x_category, y_category) |>
  summarise(mean_x = mean(x), mean_y = mean(y), .groups = "drop_last") |>
  group_vars()
## [1] "x_category"

### 删除所有分组
df |> group_by(x_category, y_category) |>
  summarise(mean_x = mean(x), mean_y = mean(y), .groups = "drop") |>
  group_vars()
## character(0)

### 保留所有分组
df |> group_by(x_category, y_category) |>
  summarise(mean_x = mean(x), mean_y = mean(y), .groups = "keep") |>
  group_vars()
## [1] "x_category" "y_category"
```

如果你的数据已经分组，或者像之前一样从一个汇总中得到了分组数据，你可以使用 `ungroup()` 再次移除分组。完成此操作后，就可以重新计算全局汇总了。

```r
df |> group_by(x_category, y_category) |>
  ungroup() |> # 移除分组
  group_vars()
## character(0)
```

分组不仅在收集数据汇总时有用。你还可以使用 `mutate()` 基于分组计算，向数据框中添加列：

```r
df |> group_by(x_category) |>
  mutate(mean_x = mean(x), mean_y = mean(y)) |>
  ungroup() |>
  print(n = 5)
## # A tibble: 100 × 6
##        x         y x_category y_category mean_x mean_y
##    <dbl>     <dbl> <chr>      <chr>       <dbl>  <dbl>
## 1 -0.416   -0.374  medium     medium      0.123 0.0520
## 2 -0.845   -1.21   medium     medium      0.123 0.0520
## 3 -0.0176   0.103  medium     medium      0.123 0.0520
## 4 -1.18    -0.0132 medium     medium      0.123 0.0520
## 5  0.508    0.504  medium     medium      0.123 0.0520
## # . . . with 95 more rows
```

当你先计算汇总再进行 `mutate` 时，会创建一些列，其中汇总参数基于分组取值，但这些值会与现有数据组合，而不是像 `summarise()` 那样提取为一个新的数据框。

没有 `group_by()` 的 `mutate()` 将为整个数据集提供汇总值。

```r
df |> mutate(mean_x = mean(x), mean_y = mean(y)) |> # 计算整个数据的 x 和 y 均值
  distinct(mean_x, mean_y)                      # 获取唯一值以便打印
## # A tibble: 1 × 2
##   mean_x mean_y
##    <dbl>  <dbl>
## 1 0.0895 0.0520
```
