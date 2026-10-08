# R语言数据连接详解

## `full_join()` 函数

相比之下，`full_join()` 函数会返回一个包含所有键的表格。如果某个键仅在一个表中找到，那么来自另一个表的变量将被设置为 `NA`。

```r
full_join(
  grades_geography, grades_physics,
  by = "name", suffix = c(".geography", ".physics")
)
```

```
## # A tibble: 5 × 3
##    name            grade.geography grade.physics
##    <chr>           <chr>           <chr>
##  1 Marko Polo      A               <NA>
##  2 Charles Darwin  A               C
##  3 Immanuel Kant   A+              <NA>
##  4 Isaac Newton    <NA>            A+
##  5 Albert Einstein <NA>            A+
```

如果你希望保留左表或右表中的所有键（但并非左右表全部保留），当另一个表中缺少该键时可能填充 `NA`，那么你需要使用 `left_join()` 或 `right_join()`。

## `left_join()` 和 `right_join()`

```r
left_join(
  grades_geography, grades_physics,
  by = "name", suffix = c(".geography", ".physics")
)
```

```
## # A tibble: 3 × 3
##    name            grade.geography grade.physics
##    <chr>           <chr>           <chr>
##  1 Marko Polo      A               <NA>
##  2 Charles Darwin  A               C
##  3 Immanuel Kant   A+              <NA>
```

```r
right_join(
  grades_maths, grades_physics,
  by = "name", suffix = c(".maths", ".physics")
)
```

```
## # A tibble: 3 × 3
##    name            grade.maths grade.physics
##    <chr>           <chr>       <chr>
##  1 Isaac Newton    A+          A+
##  2 Charles Darwin  B           C
##  3 Albert Einstein <NA>        A+
```

## `semi_join()` 函数

`semi_join()` 会返回第一个表中所有能在第二个表中找到键的行。

```r
semi_join(
  grades_maths2, grades_biology2,
  by = "name", suffix = c(".geography", ".physics")
)
```

```
## # A tibble: 4 × 2
##    name            grade
##    <chr>           <chr>
##  1 Marko Polo      D
##  2 Isaac Newton    A+
##  3 Isaac Newton    A+
##  4 Charles Darwin  B
```

当键匹配时，你只会从第一个表中获得每一行的单个副本，无论该键在第二个表中出现多少次。

这与 `inner_join()` 不同，后者会返回两个表中所有行的组合。因此，`inner_join()` 并不等同于 `semi_join()` 与 `select()` 的组合。

```r
inner_join(
  grades_maths2, grades_biology2,
  by = "name", suffix = c(".geography", ".physics")
) |> select(1:2)
```

```
## # A tibble: 5 × 2
##    name            grade.geography
##    <chr>           <chr>
##  1 Marko Polo      D
##  2 Isaac Newton    A+
##  3 Isaac Newton    A+
##  4 Charles Darwin  B
##  5 Charles Darwin  B
```

你仍然可能从 `semi_join()` 中得到多个相同的行。如果第一个表本身有重复行，那么这些行也会重复出现在结果中。如果你不希望这样，可以将连接与 `distinct()` 结合使用。

## `anti_join()` 函数

`anti_join()` 会返回第一个表中所有在第二个表中找不到其键（一个或多个键）的行。可以将其视为 `semi_join()` 的补集。

```r
anti_join(
  grades_maths2, grades_physics,
  by = "name", suffix = c(".geography", ".physics")
)
```

```
## # A tibble: 1 × 2
##    name            grade
##    <chr>           <chr>
##  1 Marko Polo      D
```

## 使用 `reduce()` 合并多个表

连接函数只接受两个表作为输入，因此你可能想知道如何合并多个表。一种解决方案是使用 `purrr` 包中的 `reduce()` 函数。

```r
grades |>
  reduce(full_join, by = "name") |>
  rename_at(2:5, ~ c("maths", "biology", "geography", "physics"))
```

```
## # A tibble: 5 × 5
##    name            maths biology geography physics
##    <chr>           <chr> <chr>   <chr>     <chr>
##  1 Marko Polo      D     F       A         <NA>
##  2 Isaac Newton    A+    D       <NA>      A+
##  3 Charles Darwin  B     A+      A         C
##  4 Immanuel Kant   <NA>  A+      <NA>      <NA>
##  5 Albert Einstein <NA>  <NA>    <NA>      A+
```

`rename_at()` 函数的工作方式与 `select_at()` 类似，这里我用它来重命名最后四列。

## 虚构国家的收入数据

在第 5 章中，我们曾以一个关于虚构国家收入的例子进行过探讨。当时，我们尚未掌握处理缺失数据的合适工具。我们曾想用缺失数据所在行的国家平均收入来替换 `NA`，但 `tidyr` 中的函数无法满足这一需求。有了 `dplyr`，我们就拥有了相应的工具。

回顾一下数据：

```r
mean_income <- tribble(
  ~country, ~`2002`, ~`2003`, ~`2004`, ~`2005`,
  "Numenor",  123456,  132654,     NA,  324156,
  "Westeros", 314256,  NA,         NA,  465321,
  "Narnia",   432156,  NA,         NA,      NA,
  "Gondor",   531426,  321465, 235461,  463521,
  "Laputa",    14235,   34125,  45123,   51234,
)
```

以下管道操作实现了我们的目标。我会逐步解释，但你也可以尝试自行推演各个步骤：

```r
mean_income |>
  pivot_longer(
    names_to = "year",
    values_to = "mean_income",
    !country
  ) |>
  group_by(
    country
  ) |>
  mutate(
    mean_per_country = mean(mean_income, na.rm = TRUE),
    mean_income = ifelse(
      is.na(mean_income),
      mean_per_country,
      mean_income
    )
  )
```

管道数据处理示例

以下是使用 `pivot_wider` 进行数据透视的示例：

```r
pivot_wider(
  names_from = "year",
  values_from = "mean_income"
)
```

```
# A tibble: 5 × 6
# Groups:   country [5]
  country mean_per_country `2002`  `2003`  `2004`
  <chr>              <dbl>  <dbl>   <dbl>   <dbl>
1 Numenor           193422 123456  132654  193422
2 Westeros          389788. 314256 389788. 389788.
3 Narnia            432156 432156  432156  432156
4 Gondor            387968. 531426 321465  235461
5 Laputa             36179.  14235  34125   45123
# ℹ 1 more variable: `2005` <dbl>
```

管道操作的第一步是重新格式化数据。我们在第5章中已经了解过其工作原理。

接下来，使用 `pivot_longer` 将宽格式数据转换为长格式：

```r
mean_income |>
  pivot_longer(
    names_to = "year",
    values_to = "mean_income",
    !country
  )
```

```
# A tibble: 20 × 3
   country   year   mean_income
   <chr>     <chr>        <dbl>
 1 Numenor   2002        123456
 2 Numenor   2003        132654
 3 Numenor   2004           NA
 4 Numenor   2005        324156
 5 Westeros  2002        314256
 6 Westeros  2003           NA
 7 Westeros  2004           NA
 8 Westeros  2005        465321
 9 Narnia    2002        432156
10 Narnia    2003           NA
11 Narnia    2004           NA
12 Narnia    2005           NA
13 Gondor    2002        531426
14 Gondor    2003        321465
15 Gondor    2004        235461
16 Gondor    2005        463521
17 Laputa    2002         14235
18 Laputa    2003         34125
19 Laputa    2004         45123
20 Laputa    2005         51234
```

我们使用 `pivot_longer()` 来将来自不同列的所有收入数据整合到一个单独的 `mean_income` 列中，同时 `year` 列将记录该数据来源的原始列信息。

在接下来的两个步骤中，我们按国家进行分组，这样我们计算的平均值就是每个国家的均值，而非整个数据集的均值。然后，我们计算这些均值并用它们替换 `NA` 单元格。为了解 `mutate()` 调用的具体效果，你可以将其替换为 `summarise()` 来查看每个国家的均值。

```r
mean_income |>
  pivot_longer(
    names_to = "year",
    values_to = "mean_income",
    !country
  ) |>
  group_by(
    country
  ) |>
  summarise(
    per_country_mean = mean(mean_income, na.rm = TRUE)
  )
```

```
# A tibble: 5 × 2
  country  per_country_mean
  <chr>                <dbl>
1 Gondor            387968.
2 Laputa             36179.
3 Narnia             432156
4 Numenor            193422
5 Westeros          389788.
```

下一步，我们可以在 `mutate()` 调用中将缺失数据替换为其国家均值，因为当我们通过 `mutate()` 创建新列时，可以引用前面已创建的列。

```r
mean_income |>
  pivot_longer(
    names_to = "year",
    values_to = "mean_income",
    !country
  ) |>
  group_by(
    country
  ) |>
  mutate(
    mean_per_country = mean(mean_income, na.rm = TRUE),
    mean_income = ifelse(
      is.na(mean_income),
      mean_per_country,
      mean_income
    )
  )
```

```
# A tibble: 20 × 4
# Groups:   country [5]
   country  year mean_income mean_per_country
   <chr>    <chr>        <dbl>            <dbl>
 1 Numenor  2002        123456           193422
 2 Numenor  2003        132654           193422
 3 Numenor  2004        193422           193422
 4 Numenor  2005        324156           193422
 5 Westeros 2002        314256           389788.
 6 Westeros 2003        389788.          389788.
 7 Westeros 2004        389788.          389788.
 8 Westeros 2005        465321           389788.
 9 Narnia   2002        432156           432156
10 Narnia   2003        432156           432156
11 Narnia   2004        432156           432156
12 Narnia   2005        432156           432156
13 Gondor   2002        531426           387968.
14 Gondor   2003        321465           387968.
15 Gondor   2004        235461           387968.
16 Gondor   2005        463521           387968.
17 Laputa   2002         14235            36179.
18 Laputa   2003         34125            36179.
19 Laputa   2004         45123            36179.
20 Laputa   2005         51234            36179.
```

如果你有一个管道，在 `group_by()` 调用和设置 `mean_income` 的 `mutate()` 调用之间还有其他步骤，那么你务必小心。不要在中间插入 `ungroup()`。该管道之所以有效，是因为我们在调用 `mutate()` 时，`mean_income` 是按国家分组的。如果我们取消分组，得到的将是所有国家的均值。

```r
mean_income |>
```

假设我们在这里执行了某些操作，这会移除分组...

接下来的 mutate 因为没有分组，所以计算的是全局均值...

```
pivot_longer(
    names_to = "year",
    values_to = "mean_income",
    !country
) |>
    group_by(country) |>
    ungroup() |>
    mutate(
        mean_per_country = mean(mean_income, na.rm = TRUE),
        mean_income = ifelse(
            is.na(mean_income),
            mean_per_country,
            mean_income
        )
    )
```

```
## # A tibble: 20 × 4
##    country   year   mean_income   mean_per_country
##    <chr>     <chr>         <dbl>              <dbl>
##  1 Numenor   2002       123456             249185.
##  2 Numenor   2003       132654             249185.
##  3 Numenor   2004       249185\.            249185.
##  4 Numenor   2005       324156             249185.
##  5 Westeros  2002       314256             249185.
##  6 Westeros  2003       249185\.            249185.
##  7 Westeros  2004       249185\.            249185.
##  8 Westeros  2005       465321             249185.
##  9 Narnia    2002       432156             249185.
## 10 Narnia    2003       249185\.            249185.
## 11 Narnia    2004       249185\.            249185.
## 12 Narnia    2005       249185\.            249185.
## 13 Gondor    2002       531426             249185.
## 14 Gondor    2003       321465             249185.
## 15 Gondor    2004       235461             249185.
## 16 Gondor    2005       463521             249185.
## 17 Laputa    2002        14235             249185.
## 18 Laputa    2003        34125             249185.
## 19 Laputa    2004        45123             249185.
## 20 Laputa    2005        51234             249185.
```

最后一步，我们只需使用`pivot_wider()`将长格式表格转换回原始格式；这里没有什么你在第 5 章中未见过的特别之处。

这就是我们如何将所有缺失数据替换为相应国家均值的方法。

脚注 1
