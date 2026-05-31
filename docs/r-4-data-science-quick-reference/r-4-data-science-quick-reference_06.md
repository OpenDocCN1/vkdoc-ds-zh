# Groups:   x_category [3]
```

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



当你进行汇总时，会移除你创建的最后一个分组——因为该汇总在每个组中本来就只剩下一行数据，所以它不再有用——但其他分组会保留。这在早期版本的`dplyr`中是默认行为，至今仍是，但这也是一个常见的错误来源。一些数据科学家期望这种分组保留行为，另一些则期望所有分组都被移除，如果你的预期未被满足，后续分析很可能出错；如果你认为自己正在对整个数据集进行操作，但所有操作都按一个或多个变量分组，那么你很可能会得到不期望的结果。

因此，`summarise()`现在引入了参数`.groups`，你可以在其中指定所需的行为。目前该参数还不是必须的，因为那会破坏大量现有代码的向后兼容性，但你会在后面看到我们之前提到的关于使用它的提示信息。

```
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

如果你的数据已经分组，或者像之前一样从一个汇总中得到了分组数据，你可以使用`ungroup()`再次移除分组。完成此操作后，就可以重新计算全局汇总了。

```
df |> group_by(x_category, y_category) |>
ungroup() |> # 移除分组
group_vars()
## character(0)
```

分组不仅在收集数据汇总时有用。你还可以使用`mutate()`基于分组计算，向数据框中添加列：

```
df |> group_by(x_category) |>
mutate(mean_x = mean(x), mean_y = mean(y)) |>
ungroup() |>
print(n = 5)
## # A tibble: 100 × 6
##        x         y x_category y_category mean_x
##                       
## 1 -0.416   -0.374  medium     medium      0.123
## 2 -0.845   -1.21   medium     medium      0.123
## 3 -0.0176   0.103  medium     medium      0.123
## 4 -1.18    -0.0132 medium     medium      0.123
## 5  0.508    0.504  medium     medium      0.123
## # . . . with 95 more rows, and 1 more variable:
## #   mean_y 
```

当你先计算汇总再进行`mutate`时，会创建一些列，其中汇总参数基于分组取值，但这些值会与现有数据组合，而不是像`summarise()`那样提取为一个新的数据框。

没有`group_by()`的`mutate()`将为整个数据集提供汇总值。

```
df |> mutate(mean_x = mean(x), mean_y = mean(y)) |> # 计算整个数据的 x 和 y 均值
distinct(mean_x, mean_y)                      # 获取唯一值以便打印
## # A tibble: 1 × 2
##   mean_x mean_y
##      
## 1 0.0895 0.0520
```

我使用了`distinct()`来获取`mean_x`和`mean_y`组合的唯一值，这能很好地说明结果。这里只有一行值，这是因为我们计算的是整个数据集的均值，而不是针对数据的不同分组。

相反，如果在向数据添加变量之前进行分组，那么汇总就是按组计算的。

```
df |> group_by(x_category) |>                       # 仅按 x_category 分组
mutate(mean_x = mean(x), mean_y = mean(y)) |> # 计算每个 x 组的均值和
distinct(mean_x, mean_y)                      # 获取唯一值以便打印
## # A tibble: 3 × 3
## # Groups:   x_category [3]
##   x_category mean_x  mean_y
##             
## 1 medium      0.123  0.0631
## 2 large       2.08   1.33
## 3 small      -2.55  -1.12
```

这里，针对不同的`x_category`因子有不同的值，表明我们计算了数据中每个不同`x_category`的均值。

如果你在分组之前计算全局汇总，可以将整个数据范围的汇总与分组汇总结合起来。

```
df |> mutate(mean_y = mean(y)) |> # 获取所有数据点的 y 均值
group_by(x_category)     |> # 然后分组，以便后续操作按组进行
mutate(mean_x = mean(x)) |> # 获取每个组的 x 均值（不是全局的）
distinct(mean_x, mean_y)    # 获取唯一值以便打印结果
## # A tibble: 3 × 3
## # Groups:    x_category [3]
##   x_category mean_y  mean_x
##             
## 1 medium     0.0520   0.123
## 2 large      0.0520   2.08
## 3 small      0.0520  -2.55
```

注意，输出行中的`mean_y`值是相同的；这是因为我们计算的是全局均值，而不是每个组的均值。

如果你需要根据不同分组计算汇总，例如，分别计算每个`x_category`的`x`均值以及每个`y_category`的`y`均值，那么你可以先调用`group_by(x_category)`来汇总`x`，接着调用`group_by(y_category)`来更改分组以便汇总`y`。

```
df |> group_by(x_category) |>
mutate(mean_x = mean(x)) |>
group_by(y_category) |>
mutate(mean_y = mean(y)) |>
distinct(
x_category, mean_x,
y_category, mean_y
)
## # A tibble: 5 × 4
## # Groups: y_category [3]
##   x_category y_category mean_x mean_y
##                  
## 1 medium   medium    0.123      0.127
## 2 medium   small     0.123     -2.38
## 3 large    medium    2.08       0.127
## 4 medium   large     0.123      2.69
## 5 small    medium   -2.55       0.127
```



### 连接表

你的数据分布在多个表中是很常见的情况。这可能是由于表格是根据不同的计算（例如不同类型的汇总）创建的，也可能是因为你从不同文件中获取了数据。

如果你只需要按行或按列合并表格，那么可以使用 `bind_rows()` 和 `bind_cols()` 函数，它们的功能正如你所预期的那样。

```
df1  
## 1 a1    b1
## 2 a2    b2
## 3 a3    b3
## 4 a4    b4
bind_cols(df1, df3)
## # A tibble: 2 × 4
##   A     B     C     D
##      
## 1 a1    b1    c1    d1
## 2 a2    b2    c2    d2
```

当你按行合并时，表格必须有相同的列；当你按列合并时，表格必须有相同的行数。

如果你有代表变量之间不同关系的表格（这是关系数据库旨在避免数据重复的基本原则），那么你可以使用连接函数来合并它们。

假设你有一个表格，记录了你所教的每个班级中学生与成绩的对应关系。你可以使用 `inner_join()` 来连接两个不同班级的表格。你需要用一个键来指定连接依据，该键由参数 `by` 设定。

```
grades_maths              
## 1 Marko Polo     D       F
## 2 Isaac Newton   A+      D
## 3 Charles Darwin B       A+
```

这告诉 `inner_join()`，你希望将第一个表中所有行与第二个表中所有行进行合并，合并条件是两行具有相同的姓名。如果你给 `by` 参数传递一个变量名称的向量，你可以在一次连接中使用多个键。

在前面，每个姓名在每个表中仅出现一次。如果一个键出现多次，那么内连接的结果会生成一个包含该姓名下所有行组合的列表。

```
grades_maths2               
## 1 Marko Polo      D       F
## 2 Isaac Newton    A+      D
## 3 Isaac Newton    A+      D
## 4 Charles Darwin  B       A+
## 5 Charles Darwin  B       A+
inner_join(grades_maths2, grades_biology2, by = "grade")
## # A tibble: 5 × 3
##   name.x          grade name.y
##               
## 1 Marko Polo      D     Isaac Newton
## 2 Isaac Newton    A+    Charles Darwin
## 3 Isaac Newton    A+    Charles Darwin
## 4 Isaac Newton    A+    Charles Darwin
## 5 Isaac Newton    A+    Charles Darwin
```

在最后这次连接中，你会看到内连接可能会多次返回相同的行。如果你希望避免这种情况，可以将连接与 `distinct()` 结合使用。

```
inner_join(grades_maths2, grades_biology2, by = "grade") |> distinct()
## # A tibble: 2 × 3
##   name.x          grade name.y
##               
## 1 Marko Polo      D     Isaac Newton
## 2 Isaac Newton    A+    Charles Darwin
```

这些表格可以拥有不同的变量（如果它们代表不同的关系），也可以共享部分或全部变量。在上述成绩的例子中，每个表格都包含相同的姓名/成绩关系，只是针对不同的班级。因此，它们也共享变量。

当存在共享变量时，可以通过后缀来区分来自两个表格的数据。默认的后缀，如上所示，是 `.x` 和 `.y`。你可以使用 `suffix` 参数来更改它们。

```
inner_join(
grades_maths, grades_biology,
by = "name", suffix = c(".maths", ".biology")
)
## # A tibble: 3 × 3
##   name            grade.maths grade.biology
##                     
## 1 Marko Polo      D           F
## 2 Isaac Newton    A+          D
## 3 Charles Darwin  B           A+
```

学生可能选择不同的课程，因此你有多种选择来合并包含不同键的表格。

`inner_join()` 只返回键同时存在于两个表中的那些行。

```
grades_geography                      
## 1 Charles Darwin A               C
```

相比之下，`full_join()` 函数会返回一个包含所有键的表格。如果某个键仅在一个表中找到，那么来自另一个表的变量将被设置为 `NA`。

```
full_join(
grades_geography, grades_physics,
by = "name", suffix = c(".geography", ".physics")
)
## # A tibble: 5 × 3
##   name            grade.geography grade.physics
##                         
## 1 Marko Polo      A               
## 2 Charles Darwin  A               C
## 3 Immanuel Kant   A+              
## 4 Isaac Newton                A+
## 5 Albert Einstein             A+
```

如果你希望保留左表或右表中的所有键（但并非左右表全部保留），当另一个表中缺少该键时可能填充 `NA`，那么你需要使用 `left_join()` 或 `right_join()`。

```
left_join(
grades_geography, grades_physics,
by = "name", suffix = c(".geography", ".physics")
)
## # A tibble: 3 × 3
##   name            grade.geography grade.physics
##                         
## 1 Marko Polo      A               
## 2 Charles Darwin  A               C
## 3 Immanuel Kant   A+              
right_join(
grades_maths, grades_physics,
by = "name", suffix = c(".maths", ".physics")
)
## # A tibble: 3 × 3
##   name            grade.maths grade.physics
##                     
## 1 Isaac Newton    A+          A+
## 2 Charles Darwin  B           C
## 3 Albert Einstein         A+
```

`semi_join()` 会返回第一个表中所有能在第二个表中找到键的行。

```
semi_join(
grades_maths2, grades_biology2,
by = "name", suffix = c(".geography", ".physics")
)
## # A tibble: 4 × 2
##   name            grade
##              
## 1 Marko Polo      D
## 2 Isaac Newton    A+
## 3 Isaac Newton    A+
## 4 Charles Darwin  B
```

当键匹配时，你只会从第一个表中获得每一行的单个副本，无论该键在第二个表中出现多少次。

这与 `inner_join()` 不同，后者会返回两个表中所有行的组合。因此，`inner_join()` 并不等同于 `inner_join()` 与 `select()` 的组合。

```
inner_join(
grades_maths2, grades_biology2,
by = "name", suffix = c(".geography", ".physics")
) |> select(1:2)
## # A tibble: 5 × 2
##   name            grade.geography
##              
## 1 Marko Polo      D
## 2 Isaac Newton    A+
## 3 Isaac Newton    A+
## 4 Charles Darwin  B
## 5 Charles Darwin  B
```

你仍然可能从 `semi_join()` 中得到多个相同的行。如果第一个表本身有重复行，那么这些行也会重复出现在结果中。如果你不希望这样，可以将连接与 `distinct()` 结合使用。

`anti_join()` 会返回第一个表中所有在第二个表中找不到其键（一个或多个键）的行。可以将其视为 `semi_join()` 的补集。

```
anti_join(
grades_maths2, grades_physics,
by = "name", suffix = c(".geography", ".physics")
)
## # A tibble: 1 × 2
##   name            grade
##              
## 1 Marko Polo      D
```

连接函数只接受两个表作为输入，因此你可能想知道如何合并多个表。一种解决方案是使用 `purrr` 包中的 `reduce()` 函数。

```
grades 
reduce(full_join, by = "name") |>
rename_at(2:5, ~ c("maths", "biology", "geography", "physics"))
## # A tibble: 5 × 5
##   name            maths biology geography physics
##                       
## 1 Marko Polo      D     F       A         
## 2 Isaac Newton    A+    D             A+
## 3 Charles Darwin  B     A+      A         C
## 4 Immanuel Kant         A+        
## 5 Albert Einstein             A+
```

`rename_at()` 函数的工作方式与 `select_at()` 类似，这里我用它来重命名最后四列。



### 虚构国家的收入数据

在第 5 章中，我们曾以一个关于虚构国家收入的例子进行过探讨。当时，我们尚未掌握处理缺失数据的合适工具。我们曾想用缺失数据所在行的国家平均收入来替换`NA`，但`tidyr`中的函数无法满足这一需求。有了`dplyr`，我们就拥有了相应的工具。

回顾一下数据：

```
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

```
mean_income                                                     |>
pivot_longer(
names_to = "year",
values_to = "mean_income",
!country
)                                                           |>
group_by(
country
)                                                           |>
mutate(
mean_per_country = mean(mean_income, na.rm = TRUE),
mean_income = ifelse(
is.na(mean_income),
mean_per_country,
mean_income
)
)                                                           |>
pivot_wider(
names_from = "year",
values_from = "mean_income"
)
## # A tibble: 5 × 6
## # Groups: country [5]
##   country mean_per_country `2002`  `2003`  `2004`
##                         
## 1 Numenor          193422  123456 132654  193422
## 2 Westeros         389788\. 314256 389788\. 389788.
## 3 Narnia           432156  432156 432156  432156
## 4 Gondor           387968\. 531426 321465  235461
## 5 Laputa            36179\.  14235  34125   45123
## # . . . with 1 more variable: `2005` 
```

管道操作的第一步是重新格式化数据。我们在第 5 章中已经了解过其工作原理。

```
mean_income |>
pivot_longer(
names_to = "year",
values_to = "mean_income",
!country
)
## # A tibble: 20 × 3
##   country   year   mean_income
##                
## 1 Numenor   2002        123456
## 2 Numenor   2003        132654
## 3 Numenor   2004            NA
## 4 Numenor   2005        324156
## 5 Westeros  2002        314256
## 6 Westeros  2003            NA
## 7 Westeros  2004            NA
## 8 Westeros  2005        465321
## 9 Narnia    2002        432156
## 10 Narnia   2003            NA
## 11 Narnia   2004            NA
## 12 Narnia   2005            NA
## 13 Gondor   2002        531426
## 14 Gondor   2003        321465
## 15 Gondor   2004        235461
## 16 Gondor   2005        463521
## 17 Laputa   2002         14235
## 18 Laputa   2003         34125
## 19 Laputa   2004         45123
## 20 Laputa   2005         51234
```

我们使用`pivot_longer()`来将来自不同列的所有收入数据整合到一个单独的`mean_income`列中，同时`year`列将记录该数据来源的原始列信息。

在接下来的两个步骤中，我们按国家进行分组，这样我们计算的平均值就是每个国家的均值，而非整个数据集的均值。然后，我们计算这些均值并用它们替换`NA`单元格。为了解`mutate()`调用的具体效果，你可以将其替换为`summarise()`来查看每个国家的均值。

```
mean_income |>
pivot_longer(
names_to = "year",
values_to = "mean_income",
!country
)                                                         |>
group_by(
country
)                                                         |>
summarise(
per_country_mean = mean(mean_income, na.rm = TRUE)
)
## # A tibble: 5 × 2
##   country  per_country_mean
##                  
## 1 Gondor            387968.
## 2 Laputa             36179.
## 3 Narnia             432156
## 4 Numenor            193422
## 5 Westeros          389788.
```

下一步，我们可以在`mutate()`调用中将缺失数据替换为其国家均值，因为当我们通过`mutate()`创建新列时，可以引用前面已创建的列。

```
mean_income                                                   |>
pivot_longer(
names_to = "year",
values_to = "mean_income",
!country
)                                                          |>
group_by(
country
)                                                          |>
mutate(
mean_per_country = mean(mean_income, na.rm = TRUE),
mean_income = ifelse(
is.na(mean_income),
mean_per_country,
mean_income
)
)
## # A tibble: 20 × 4
## # Groups:  country [5]
##   country  year mean_income mean_per_country
##                         
## 1 Numenor  2002     123456           193422
## 2 Numenor  2003     132654           193422
## 3 Numenor  2004     193422           193422
## 4 Numenor  2005     324156           193422
## 5 Westeros 2002     314256           389788.
## 6 Westeros 2003     389788\.          389788.
## 7 Westeros 2004     389788\.          389788.
## 8 Westeros 2005     465321           389788.
## 9 Narnia   2002     432156           432156
## 10 Narnia  2003     432156           432156
## 11 Narnia  2004     432156           432156
## 12 Narnia  2005     432156           432156
## 13 Gondor  2002     531426           387968.
## 14 Gondor  2003     321465           387968.
## 15 Gondor  2004     235461           387968.
## 16 Gondor  2005     463521           387968.
## 17 Laputa  2002      14235            36179.
## 18 Laputa  2003      34125            36179.
## 19 Laputa  2004      45123            36179.
## 20 Laputa  2005      51234            36179.
```

如果你有一个管道，在`group_by()`调用和设置`mean_income`的`mutate()`调用之间还有其他步骤，那么你务必小心。*不要*在中间插入`ungroup()`。该管道之所以有效，是因为我们在调用`mutate()`时，`mean_income`是按国家分组的。如果我们取消分组，得到的将是*所有*国家的均值。

```
mean_income |>
pivot_longer(
names_to = "year",
values_to = "mean_income",
!country
)                                                            |>
group_by(
country
)                                                            |>
### 假设我们在这里执行了某些操作，这会移除分组...
ungroup()                                                    |>
### 接下来的 mutate 因为没有分组，所以计算的是全局均值...
mutate(
mean_per_country = mean(mean_income, na.rm = TRUE),
mean_income = ifelse(
is.na(mean_income),
mean_per_country,
mean_income
)
)
## # A tibble: 20 × 4
##   country   year   mean_income   mean_per_country
##                              
## 1 Numenor   2002       123456             249185.
## 2 Numenor   2003       132654             249185.
## 3 Numenor   2004       249185\.            249185.
## 4 Numenor   2005       324156             249185.
## 5 Westeros  2002       314256             249185.
## 6 Westeros  2003       249185\.            249185.
## 7 Westeros  2004       249185\.            249185.
## 8 Westeros  2005       465321             249185.
## 9 Narnia    2002       432156             249185.
## 10 Narnia   2003       249185\.            249185.
## 11 Narnia   2004       249185\.            249185.
## 12 Narnia   2005       249185\.            249185.
## 13 Gondor   2002       531426             249185.
## 14 Gondor   2003       321465             249185.
## 15 Gondor   2004       235461             249185.
## 16 Gondor   2005       463521             249185.
## 17 Laputa   2002        14235             249185.
## 18 Laputa   2003        34125             249185.
## 19 Laputa   2004        45123             249185.
## 20 Laputa   2005        51234             249185.
```

最后一步，我们只需使用`pivot_wider()`将长格式表格转换回原始格式；这里没有什么你在第 5 章中未见过的特别之处。

这就是我们如何将所有缺失数据替换为相应国家均值的方法。

脚注 1



# 9. 字符串处理：`stringr`

`stringr` 包提供了字符串操作函数。当你加载 `tidyverse` 包时，该包也会被加载：

```
library(tidyverse)
```

你也可以单独加载该包：

```
library(stringr)
```

## 统计字符串模式

`str_count()` 函数用于统计字符串中包含的标记（token）数量，其中标记可以是字符或单词等。默认情况下，`str_count()` 会统计字符串中的字符数。

```
strings <- c(
"给我一个冰淇淋",
"给你自己买个冰淇淋",
"我们的冰淇淋都卖完了",
"我尖叫，你尖叫，人人都爱冰淇淋。",
"一个冰淇淋,
两个冰淇淋,
三个冰淇淋",
"我想要一个冰淇淋。你想要一个冰淇淋吗？"
)
str_count(strings)
## [1]  10  11  12  16  14  18
```

对于 `strings` 中的每个字符串，我们得到了字符数。结果与我们使用 `nchar()` 得到的结果相同（但不同于 `length()`，后者会返回包含这些字符串的列表长度，在此例中为 6）。

你可以通过为 `str_count()` 指定 `boundary()` 选项来明确要求其统计字符数。该选项决定了标记之间的边界（如字符、单词、行等），也就是计数的单位。

```
str_count(strings,boundary("character"))
## [1]  10  11  12  16  14  18
```

如果你想统计单词数量，可以使用 `boundary("word")`：

```
str_count(strings, boundary("word"))
## [1]  5  5  7  8  9  11
```

你还可以在 `boundary()` 中使用另外两个选项：`"line_break"` 和 `"sentence"`。它们会根据语言环境（`locale()`）使用启发式规则来判断文本包含多少换行符和句子。

```
str_count(strings, boundary("line_break"))
## [1]  5  5  7  8  11  11
str_count(strings, boundary("sentence"))
## [1]  1  1  1  1  3  2
```

请注意，这里的换行符并非文本中的换行符。这些换行符是指在你所使用的 `locale()` 中期望放置换行符的位置。

最后，`str_count()` 可以让你统计子字符串在字符串中出现的次数：

```
str_count(strings, "冰淇淋")
## [1]  1  1  1  1  3 2
str_count(strings, "冰") # 也统计到了“冰”字
## [1]  2  1  4  3  6  4
```

你要求 `str_count()` 统计的模式不仅仅是一个字符串。它实际上是一个正则表达式。某些字符在正则表达式中具有特殊含义。^(¹⁷) 例如，点号（`.`）代表任意单个字符，而不是句号。

```
str_count(strings, ".")
## [1]  10  11  12  16  14  18
```

如果你希望将模式作为字面字符串处理，而不是正则表达式，可以将其包裹在 `fixed()` 中：

```
str_count(strings, fixed("。"))
## [1]  0  0  0  1  0  1
```

由于该模式是正则表达式，我们可以使用它来统计标点符号：

```
str_count(strings, "[[:punct:]]")
## [1]  0  0  0  1  1  2
```

或者统计字符串末尾出现“冰淇淋”的次数：

```
str_count(strings, "冰淇淋$")
## [1]  1  1  1  0  1  0
```

这里的 `$` 表示字符串的末尾。更准确地说，除了末尾可能后跟一个标点符号外，它表示在字符串的末尾。

```
str_count(strings, "冰淇淋[[:punct:]]?$")
## [1]  1  1  1  1  1  1
```

## 拆分字符串

有时，你需要根据某个分隔符来拆分字符串——这与在逗号分隔值（CSV）文件中根据逗号进行拆分类似。`stringr` 包中用于此功能的函数是 `str_split()`。

例如，我们可以按空格拆分：

```
strings <- c(
"一",
"二",
"一 二",
"一  二",
"一。 二。"
)
str_split(strings, " ")
## [[1]]
## [1] "一"
##
## [[2]]
## [1] "二"
##
## [[3]]
## [1] "一" "二"
##
## [[4]]
## [1] "一" ""  ""  "二"
##
## [[5]]
## [1] "一。" "二。"
```

由于我们是按单个空格进行拆分，对于包含三个空格的 `"一  二"`，我们会得到空字符串。如果你希望任意长度的空格序列都作为分隔符，可以这样操作：

```
str_split(strings, "[[:space:]]+")
## [[1]]
## [1] "一"
##
## [[2]]
## [1] "二"
##
## [[3]]
## [1] "一" "二"
##
## [[4]]
## [1] "一" "二"
##
## [[5]]
## [1] "一。" "二。"
```

你也可以使用 `boundary()` 函数进行拆分。例如，你可以使用 `boundary("word")` 将字符串拆分成单词：

```
str_split(strings, boundary("word"))
## [[1]]
## [1] "一"
##
## [[2]]
## [1] "二"
##
## [[3]]
## [1] "一" "二"
##
## [[4]]
## [1] "一" "二"
##
## [[5]]
## [1] "一" "二"
```

这样操作时，我们排除了第一次尝试中出现的空字符串，同时去掉了最后一个字符串中的句号。

## 字符串大小写转换

你可以使用 `str_to_lower()` 将字符串转换为全小写。

```
macdonald <- "Old MACDONALD had a farm."
str_to_lower(macdonald)
## [1] "old macdonald had a farm."
```

同样，你可以使用 `str_to_upper()` 将其转换为全大写。

```
str_to_upper(macdonald)
## [1] "OLD MACDONALD HAD A FARM."
```

如果使用 `str_to_sentence()`，则首字母大写，其余字母小写。

```
str_to_sentence(macdonald)
## [1] "Old macdonald had a farm."
```

`str_to_title()` 函数会将字符串中的所有单词首字母大写。

```
str_to_title(macdonald)
## [1] "Old Macdonald Had A Farm."
```



## 换行、填充与修剪

如果你想要对字符串进行换行（即添加换行符），使其适配指定宽度，可以使用 `str_wrap()`。

```
strings <- c(
"给我一个冰淇淋",
"给自己拿一个冰淇淋",
"我们的冰淇淋都没了",
"我尖叫，你尖叫，大家都爱冰淇淋。",
"一个冰淇淋，
两个冰淇淋，
三个冰淇淋",
"我想要一个冰淇淋。你也想要一个冰淇淋吗？"
)
str_wrap(strings)
## [1] "给我一个冰淇淋"
## [2] "给自己拿一个冰淇淋"
## [3] "我们的冰淇淋都没了"
## [4] "我尖叫，你尖叫，大家都爱冰淇淋。"
## [5] "一个冰淇淋，两个冰淇淋，三个冰淇淋"
## [6] "我想要一个冰淇淋。你也想要一个冰淇淋吗？"
```

默认宽度是 80 个字符，但你可以通过 `width` 参数来更改。

```
str_wrap(strings, width = 10)
## [1] "给我一个\n 冰淇淋"
## [2] "给自\n 己拿一个\n 冰淇淋"
## [3] "我们的\n 冰淇淋\n 都没了"
## [4] "我尖叫，\n 你尖叫，\n 大家都\n 爱冰淇淋。"
## [5] "一个冰\n 淇淋，\n 两个冰\n 淇淋，\n 三个冰\n 淇淋"
## [6] "我想要\n 一个冰\n 淇淋。\n 你也想\n 要一个\n 冰淇淋\n 吗？"
```

你可以在换行时使用 `indent` 参数来缩进字符串的第一行。

```
str_wrap(strings, width = 10, indent = 2)
## [1] " 给我\n 一个冰淇淋"
## [2] " 给自\n 己拿一个\n 冰淇淋"
## [3] " 我们\n 的冰淇淋\n 都没了"
## [4] " 我尖\n 叫，你尖\n 叫，大家\n 都爱冰\n 淇淋。"
## [5] " 一个\n 冰淇淋，\n 两个冰\n 淇淋，\n 三个冰\n 淇淋"
## [6] " 我想\n 要一个\n 冰淇淋。\n 你也想\n 要一个\n 冰淇淋\n 吗？"
```

如果你希望字符串左对齐、右对齐或居中对齐，可以使用 `str_pad()`。

默认情况下是右对齐字符串。

```
str_pad(strings, width = 50)
## [1] "                               给我一个冰淇淋"
## [2] "                          给自己拿一个冰淇淋"
## [3] "                       我们的冰淇淋都没了"
## [4] " 我尖叫，你尖叫，大家都爱冰淇淋。"
## [5] "一个冰淇淋，\n  两个冰淇淋，\n  三个冰淇淋"
## [6] "   我想要一个冰淇淋。你也想要一个冰淇淋吗？"
```

如果你想改为左对齐，可以将 `"right"` 传递给 `side` 参数。

```
str_pad(strings, width = 50, side = "right")
## [1] "给我一个冰淇淋                                "
## [2] "给自己拿一个冰淇淋                           "
## [3] "我们的冰淇淋都没了                        "
## [4] "我尖叫，你尖叫，大家都爱冰淇淋。  "
## [5] "一个冰淇淋，\n  两个冰淇淋，\n  三个冰淇淋"
## [6] "我想要一个冰淇淋。你也想要一个冰淇淋吗？    "
```

你需要使用 `"right"` 来实现左对齐，因为 `side` 参数决定的是在哪一侧进行填充；对于左对齐的文本，填充是在右侧。

如果你想让文本居中，应该使用 `"both"`；你会在左侧和右侧都进行填充。

```
str_pad(strings, width = 50, side = "both")
## [1] "           给我一个冰淇淋                       "
## [2] "           给自己拿一个冰淇淋                    "
## [3] "           我们的冰淇淋都没了                    "
## [4] " 我尖叫，你尖叫，大家都爱冰淇淋。  "
## [5] "一个冰淇淋，\n  两个冰淇淋，\n  三个冰淇淋"
## [6] " 我想要一个冰淇淋。你也想要一个冰淇淋吗？   "
```

在这些填充示例中，我们没有让字符串长度保持在填充宽度以下。如果字符串长度超过填充宽度，它将保持不变。你可以使用 `str_trunc()` 函数来将宽度裁剪到某个特定值。例如，我们可以在填充之前先将所有字符串截断为 25 个字符：

```
strings |> str_trunc(25) |> str_pad(width = 25, side = "left")
## [1] " 给我一个冰淇淋"
## [2] "给自己拿一个冰淇淋"
## [3] "我们的冰淇淋都没了..."
## [4] "我尖叫，你尖叫，大家都..."
## [5] " 一个冰淇淋，\n  两个..."
## [6] "我想要一个冰淇淋。你..."
```

`str_trim()` 函数用于移除字符串左侧和右侧的空白字符：

```
str_trim(c(
" 一小杯可乐",
"两大杯可乐 ",
" 三杯中杯可乐 "
))
## [1] "一小杯可乐"   "两大杯可乐"
## [3] "三杯中杯可乐"
```

它会保留字符串内部的空白字符。

由于 `str_trim()` 不会处理不在左或右边缘的空白字符，因此我们不能用它来去除字符串内部多余的空格。例如，如果两个单词之间有两个空格，如下所示，`str_trim()` 会保留它们原样：

```
str_trim(c(
" 一小杯可乐",
"两大  杯可乐 ",
" 三杯中杯可乐 "
))
## [1] "一小杯可乐"    "两大  杯可乐"
## [3] "三杯中杯可乐"
```

如果我们希望将两个空格缩短为一个空格，可以改用 `str_squish()`。

```
str_squish(c(
" 一小杯可乐",
"两大  杯可乐 ",
" 三杯中杯可乐 "
))
## [1] "一小杯可乐"  "两大杯可乐"
## [3] "三杯中杯可乐"
```

## 检测子串

要检查一个子串是否出现在另一个字符串中，可以使用 `str_detect()`。

```
str_detect(strings,"我")
## [1]  TRUE FALSE FALSE FALSE FALSE FALSE
str_detect(strings,"你")
## [1] FALSE FALSE FALSE  TRUE FALSE  TRUE
str_detect(strings,"冰淇淋")
## [1] TRUE TRUE TRUE TRUE TRUE TRUE
```

该模式是一个正则表达式，所以要测试“冰淇淋”后面紧跟句号的情况，你不能直接搜索“冰淇淋。”。

```
str_detect(strings, "冰淇淋.")
## [1] FALSE FALSE  TRUE  TRUE  TRUE  TRUE
```

你可以再次使用 `fixed()` 字符串。

```
str_detect(strings, fixed("冰淇淋."))
## [1] FALSE FALSE FALSE  TRUE FALSE  TRUE
```

或者，你可以对句点进行转义。

```
str_detect(strings,"冰淇淋\\.")
## [1] FALSE FALSE FALSE  TRUE FALSE  TRUE
```

你可以通过将 `negate` 参数设置为 `TRUE` 来测试子串是否*未*在字符串中找到。

```
str_detect(strings, fixed("冰淇淋."), negate = TRUE)
## [1]  TRUE  TRUE  TRUE FALSE  TRUE FALSE
```

有两个特殊函数用于测试字符串的开头或结尾：

```
str_starts(strings, "你")
## [1] FALSE FALSE FALSE  TRUE FALSE  TRUE
str_ends(strings, fixed("。"))
## [1] FALSE FALSE FALSE  TRUE FALSE FALSE
```

如果你想知道子串所在的位置，可以使用 `str_locate()`。它会返回匹配项的起始和结束索引。

```
str_locate(strings, "冰淇淋")
##      start end
## [1,]    -1  -1
## [2,]    -1  -1
## [3,]    -1  -1
## [4,]    -1  -1
## [5,]    -1  -1
## [6,]    -1  -1
```

这里，你得到每个字符串的起始和结束索引，但第六个字符串中这种模式出现了不止一次。

```
strings[6]
## [1] "我想要一个冰淇淋。你也想要一个冰淇淋吗？"
```

当你使用 `str_locate()` 时，只会得到第一个匹配项的索引。

```
str_locate(strings[6], "冰淇淋")
##      start end
## [1,]    -1  -1
```

`str_locate_all()` 函数会给出所有匹配项。

```
str_locate_all(strings[6],"冰淇淋")
## [[1]]
##      start end
## [1,]    -1  -1
## [2,]    -1  -1
```

如果你想要得到*匹配项之间*字符串的起始和结束点，可以使用 `invert_match()`。

```
ice_cream_locations <- str_locate_all(strings[6], "冰淇淋")
ice_cream_locations
## [[1]]
##      start end
## [1,]    -1  -1
## [2,]    -1  -1
invert_match(ice_cream_locations[[1]])
##      start end
## [1,]     0  -1
## [2,]     0  -1
## [3,]     0  -1
```

## 提取子串

要提取匹配模式的子串，可以使用 `str_extract()`。它会返回第一个匹配正则表达式的子串。

```
str_extract(strings, "(尖叫|冰淇淋)\\w*")
## [1] "冰淇淋" "冰淇淋" "冰淇淋"
## [4] "尖叫"   "冰淇淋" "冰淇淋"
```

它只返回第一个匹配项，但如果你想要所有匹配的子串，可以使用 `str_extract_all()`。

```
strings[4]
## [1] "我尖叫，你尖叫，大家都爱冰淇淋。"
str_extract(strings[4], "(尖叫|冰淇淋)\\w*")
## [1] "尖叫"
str_extract_all(strings[4], "(尖叫|冰淇淋)\\w*")
## [[1]]
## [1] "尖叫"   "尖叫"   "冰淇淋"
```



## 字符串转换

我们可以将匹配模式的子字符串替换为其他字符串。

```
lego_str <- str_replace(strings, "ice cream[s]?", "LEGO")
lego_str
## [1] "Give me an LEGO"
## [2] "Get yourself an LEGO"
## [3] "We are all out of LEGO"
## [4] "I scream, you scream, everybody loves LEGO."
## [5] "one LEGO,\n  two ice creams,\n  three ice creams"
## [6] "I want an LEGO. Do you want an ice cream?"
lego_str <- str_replace(lego_str, "an LEGO", "a LEGO")
lego_str
## [1] "Give me a LEGO"
## [2] "Get yourself a LEGO"
## [3] "We are all out of LEGO"
## [4] "I scream, you scream, everybody loves LEGO."
## [5] "one LEGO,\n  two ice creams,\n  three ice creams"
## [6] "I want a LEGO. Do you want an ice cream?"
```

这两个替换操作可以写成管道形式，使代码更具 Tidyverse 风格：

```
strings %>%
str_replace("ice cream[s]?", "LEGO") %>%
str_replace("an LEGO", "a LEGO")
## [1] "Give me a LEGO"
## [2] "Get yourself a LEGO"
## [3] "We are all out of LEGO"
## [4] "I scream, you scream, everybody loves LEGO."
## [5] "one LEGO,\n  two ice creams,\n  three ice creams"
## [6] "I want a LEGO. Do you want an ice cream?"
```

与之前的大多数函数一样，该函数仅影响第一个匹配项。要替换所有匹配项，需要使用 `str_replace_all()`。

```
strings %>%
str_replace_all("ice cream[s]?", "LEGO") %>%
str_replace_all("an LEGO", "a LEGO")
## [1] "Give me a LEGO"
## [2] "Get yourself a LEGO"
## [3] "We are all out of LEGO"
## [4] "I scream, you scream, everybody loves LEGO."
## [5] "one LEGO,\n  two LEGO,\n  three LEGO"
## [6] "I want a LEGO. Do you want a LEGO?"
```

你可以在替换字符串中引用匹配的分组，这是正则表达式中常见的操作。

```
us_dates <- c(
valentines = "2/14",
my_birthday = "2/15",
## 没人知道，但我们就这么定吧
jesus_birthday = "12/24"
)
## 将美国日期格式转换为更合理的格式
str_replace(us_dates, "(.*)/(.*)", "\\2/\\1")
## [1] "14/2" "15/2" "24/12"
```

`str_dup()` 函数用于复制字符串，即重复字符串多次。

```
str_c(
"NA",
str_dup("-NA", times = 7),
" BATMAN!"
)
## [1] "NA-NA-NA-NA-NA-NA-NA-NA BATMAN!"
```

这里我们还使用了 `str_c()` 来连接字符串。此函数与 `c()` 的工作方式不同；后者会创建一个包含多个字符串的向量，而前者会生成一个字符串。

```
## -- 连接 -------------------------------------------
c("foo", "bar", "baz")
## [1] "foo" "bar" "baz"
str_c("foo", "bar", "baz")
## [1] "foobarbaz"
```

提取和修改子字符串的更直接方法是使用 `str_sub()`。它允许你提取由开始和结束索引指定的子字符串，如果对其进行赋值，则会替换该子字符串。`str_sub()` 函数不如其他函数强大，因为它不适用于正则表达式，但也正因如此，它更容易理解。

如果你不知道子字符串的位置，则必须先找到它。为此，你可以使用 `str_locate()`。

```
my_string <- "this is my string"
my_location <- str_locate(my_string, "my")
my_location
##     start end
## [1,]    9  10
s <- my_location[,"start"]
e <- my_location[,"end"]
str_sub(my_string, s, e)
## [1] "my"
my_string_location <- str_locate(my_string, "string")
s <- my_string_location[,"start"]
e <- my_string_location[,"end"]
str_sub(my_string, s, e)
## [1] "string"
your_string <- my_string
s <- my_location[,"start"]
e <- my_location[,"end"]
str_sub(your_string, s, e) <- "your"
your_string
## [1] "this is your string"
your_banana <- your_string
your_string_location <- str_locate(your_string, "string")
s <- your_string_location[,"start"]
e <- your_string_location[,"end"]
str_sub(your_banana, s, e) <- "banana"
your_banana
## [1] "this is your banana"
```

当你对 `str_sub()` 的调用进行赋值时，看起来像是在修改字符串。这只是一种假象。赋值函数会创建新数据并更改变量引用的数据。因此，如果有多个引用指向同一个字符串，请务必小心。只有一个变量会指向新值；其余的变量仍指向旧字符串。这不仅适用于 `str_sub()`，而是 R 的普遍情况，它可能是一个潜在的错误来源。

```
my_string
## [1] "this is my string"
your_string
## [1] "this is your string"
your_banana
## [1] "this is your banana"
```

如果你经常编写代码生成标准报告，每次文本基本相同，只是修改了几个选定值，那么你会爱上 `str_glue()`。它完全符合你的需求。你向 `str_glue()` 提供一个模板字符串，一个包含可变部分（用花括号括起来）的字符串。调用 `str_glue()` 的结果是模板文本，但花括号中的值会被替换为它们所包含的 R 表达式的结果。

最简单的用法是模板引用变量。

```
macdonald <- "Old MacDonald"
eieio <- "E-I-E-I-O"
str_glue("{macdonald} had a farm. {eieio}")
## Old MacDonald had a farm. E-I-E-I-O
```

这些变量不必是全局变量。它们也可以作为命名参数传递给 `str_glue()`。

```
str_glue(
"{macdonald} had a farm. {eieio}",
macdonald = "Thomas",
eieio = "He certainly did not!"
)
## Thomas had a farm. He certainly did not!
```

通常，你可以在花括号中放置 R 表达式，表达式的求值结果将插入到模板字符串中。

```
str_glue(
"{str_dup(\"NA-\", times = 7)}NA BATMAN!"
)
## NA-NA-NA-NA-NA-NA-NA-NA BATMAN!
x <- seq(1:10)
str_glue(
"Holy {mean(x)} BATMAN!"
)
## Holy 5.5 BATMAN!
```

# 处理因子：`forcats`

如果你处理分类数据，通常可以用字符串来表示类别。然而，字符串也有一些缺点。例如，拼写错误很容易不被发现。如果你希望类别有序，那么字符串的字典顺序并不总是你需要的顺序。作为将类别编码为字符串的替代方案，我们有因子。在本章中，我们将介绍 `forcats` 包提供的用于创建和操作因子的功能。

当你导入 `tidyverse` 时，该包会被加载：

```
library(tidyverse)
```

但你也可以显式加载它：

```
library(forcats)
```



## 创建因子

内置的 `factor()` 函数仍然是从头构建因子的绝佳选择，尤其是当你想在创建时指明因子的水平（levels）。

如果你在创建因子时没有明确指定水平，那么每个输入中的唯一元素都会成为一个水平。

```
factor(c("A","C","B"))
## [1] A C B
## Levels: A B C
```

否则，你会得到传递给 `levels` 参数的那些水平。

```
factor(c("A","C","B"),levels =c("A","B","C","D"))
## [1] A C B
## Levels: A B C D
factor(c("A","C","B"),levels =c("D","C","B","A"))
## [1] A C B
## Levels: D C B A
```

如果有一个值在水平中不存在，它会被设置为 `NA`。

```
factor(c("A","C","B"),levels =c("A","B"))
## [1] A     B
## Levels: A B
```

通常，`factor()` 函数是创建因子的正确选择。然而，该函数并非泛型函数，因此如果你希望有一种方法来转换自己实现的数据结构（并且这种转换需要特定的方法），那么你可能需要特化 `forcats` 包中的 `as_factor()` 函数。

如果你特化了 `as_factor()` 函数，你可以做任何你想做的事情。但对于普通向量，`as_factor()` 的工作方式类似于基础 R 中的 `as.factor()`。不过，这两个函数在设置水平的方式上有所不同。如果你使用 `as.factor()`，水平会被排序。

```
f1<-as.factor(c("C","B","B","A","D"))
f1# levels ordered alphabetically
## [1] C B B A D
## Levels: A B C D
```

使用 `as_factor()` 时，水平按照它们在输入中出现的顺序排列。

```
f2<-as_factor(c("C","B","B","A","D"))
f2# levels in the order they appear in the input
## [1] C B B A D
## Levels: C B A D
```

## 拼接（Concatenation）

如果你有两个因子，你可以使用 `fct_c()` 来拼接它们。结果因子的水平是输入因子水平的并集。水平的顺序取决于输入因子的顺序。第一个因子的水平排在前面，然后是第二个因子的水平。如果因子共享某些水平，则顺序由第一个向量决定。

例如，重新考虑之前的因子 `f1` 和 `f2`。

```
f1
## [1] C B B A D
## Levels: A B C D
f2
## [1] C B B A D
## Levels: C B A D
```

它们有相同的水平，但顺序不同。如果我们拼接 `f1` 和 `f2`，我们会得到按照 `f1` 顺序排列的水平。

```
fct_c(f1, f2)
##    [1] C B B A D C B B A D
## Levels: A B C D
```

如果我们拼接 `f2` 和 `f1`，那么水平按照 `f2` 中的顺序排列。

```
fct_c(f2, f1)
##   [1] C B B A D C B B A D
## Levels: C B A D
```

现在考虑一个具有 `f1` 中不存在水平的因子，例如：

```
f3<-as_factor(c("X","Y","A"))
```

它与 `f1` 共享一个水平 `A`，但 `X` 和 `Y` 是 `f3` 独有的。如果我们以 `f1` 作为第一个参数进行拼接，新的水平会被包含进来，但排在 `f1` 中的水平之后（以及共享水平 `A` 之后）。

```
fct_c(f1, f3)
## [1] C B B A D X Y A
## Levels: A B C D X Y
```

如果我们以 `f3` 作为第一个参数进行拼接，那么 `f3` 的水平会排在前面。

```
fct_c(f3, f1)
## [1] X Y A C B B A D
## Levels: X Y A B C D
```

你可以使用 `fct_c()` 拼接多个因子。其规则与拼接两个因子相同。

```
fct_c(f1, f2, f3)
##   [1] C B B A D C B B A D X Y A
## Levels: A B C D X Y
fct_c(f2, f3, f1)
##   [1] C B B A D X Y A C B B A D
## Levels: C B A D X Y
fct_c(f3, f1, f2)
##   [1] X Y A C B B A D C B B A D
## Levels: X Y A B C D
```

如果你有多个水平不相同的因子，但你希望它们具有相同的水平（在不拼接的情况下），你可以使用 `fct_unify()`。它接收一个因子列表，并返回一个因子列表，其中所有因子都具有输入中所有水平的并集。

```
fct_unify(fs =list(f1, f2, f3))
## [[1]]
## [1] C B B A D
## Levels: A B C D X Y
##
## [[2]]
## [1] C B B A D
## Levels: A B C D X Y
##
## [[3]]
## [1] X Y A
## Levels: A B C D X Y
```

## 投影（Projection）

有时你的分类数据粒度过细，希望将类别归并到更大的类中。如果是这样，你需要的函数是 `fct_collapse()`。它允许你将现有的水平映射到新的水平。它的第一个参数是一个因子，之后你需要提供命名参数。命名参数的值必须是一个水平名称的列表。每个名称都会成为一个新的水平，并且该水平将包含你作为参数给出的列表中的元素。

```
fct_collapse(
fct_c(f3, f1),
a =c("A","X"),
b =c("B","Y"),
c =c("C","D")
)
## [1] a b a c b b a c
## Levels: a b c
```

你不需要重新映射所有水平。那些你没有映射的水平将保持不变。

```
fct_collapse(
fct_c(f3, f1),
a =c("A","X"),
b =c("B","Y")
)
## [1] a b a C b b a D
## Levels: a b C D
```

如果你只想重命名水平而不是归并它们，可以使用 `fct_recode()`。

```
f1
## [1] C B B A D
## Levels: A B C D
fct_recode(f1, a = "A", b = "B")
## [1] C b b a D
## Levels: a b C D
fct_recode(f1, X = "A", X = "B", Y = "C", Y = "D")
## [1] Y X X X Y
## Levels: X Y
```

如果你想根据每个水平所含元素的数量来减少水平数量，而不是以显式指定的方式合并水平，那么你可以使用 `fct_lump()`。你可以合并水平，只保留元素最多的 *n* 个水平，例如，*n* `= 5` 和 *n* `= 2`：

```
f  fct_lump(n = 5, other_level = "X") |> table()
##
## 3  5  6  7  9  10  X
## 2  4  2  2  4   5  1
f |> fct_lump(n = 2, other_level = "X") |> table()
##
## 5  9  10  X
## 4  4   5  7
```

这里你看到的结果中有多于五个和两个的水平，尽管我们调用了 *n* `= 5` 和 *n* `= 2` 的 `fct_lump()`。如果有几个水平具有相同数量的元素，该函数不会随机选择一些进行保留。你会得到所有出现次数最多的水平。

如果你使用负数，你会得到最不常见的类别。

```
f |> fct_lump(n = -2, other_level = "X") |> table()
##
## 1  4  8   X
## 0  0  0  20
```

除了选择你想要保留的类别数量，你还可以要求它们包含至少或至多一定比例的数据。为此，你需要使用 `prop` 参数。要得到包含至少 10% 数据的水平，你可以使用 `prop = 0.1`：

```
f |> fct_lump(prop = 0.1, other_level = "X") |> table()
##
## 5  9  10  X
## 4  4   5  7
```

如果你想要包含少于 10% 数据的类别，也可以使用 `prop`，但要用负值。要得到包含至多 10% 数据的类别，你可以使用：

```
f |> fct_lump(prop = -0.1, other_level = "X") |> table()
##
## 1  2  3  4  6  7  8  X
## 0  1  2  0  2   2 0 13
```

一种更直接的映射类别的方法是将固定数量的类别映射到一个新类别。`fct_other()` 允许你选择一个要保留或丢弃的类别列表。如果你选择保留某些类别，那么所有其他类别都会被归入同一个“其他”类别。如果你选择丢弃某些类别，那么被丢弃的类别会被归入“其他”类别。你可以使用 `other_level` 参数来命名这个“其他”类别。

```
f |> fct_other(keep = 1:5, other_level = "X")
## [1] 2 X X X 5 X X 3 3 X X 5 X 5 X X X 5 X X
## Levels: 1 2 3 4 5 X
f |> fct_other(drop = 1:5, other_level = "X")
## [1] X 9 9 9 X 7 7 X X 6 10 X 10 X 9
## [16] 6 10 X 10 10
## Levels: 6 7 8 9 10 X
```

有时，你想要的操作甚至更简单。你的数据中可能会有一些实际不存在的水平。你可以使用 `fct_drop()` 来删除这些空的水平。

```
f1
## [1] C B B A D
## Levels: A B C D
levels(f1) <- LETTERS[1:10]
f1
## [1] C B B A D
## Levels: A B C D E F G H I J
fct_drop(f1)
## [1] C B B A D
## Levels: A B C D
```



## 添加水平

`fct_drop()` 的反向操作是添加额外的水平。正如我们之前所做的那样，对 `levels()` 进行赋值会添加那些因子中不一定存在的水平，但同时也可能会重命名现有的水平。水平被重命名了，而不仅仅是添加。这一点在之前的 `f1` 上就发生了。当我们添加新水平时，它原有的水平 1、2、3 和 4 被替换为了 A、B、C 和 D。

使用 `fct_expand()` 函数，你可以添加因子中不存在的水平，而任何数据中已有的水平都会被忽略。

```
f1
## [1] C B B A D
## Levels: A B C D E F G H I J
f3
## [1] X Y A
## Levels: X Y A
fct_expand(f1, levels(f3))
## [1] C B B A D
## Levels: A B C D E F G H I J X Y
```

在 R 中，缺失数据并不被视为缺失数据，我们通常可以直接忽略它。然而，有时缺失数据中也包含我们需要考虑的信息。由于 R 在许多统计检验中无法很好地处理缺失数据（我补充这一点是有充分理由的，因为并没有一种普遍正确的方法来处理它），最好的办法是将其转换为 R **能够**处理的类别。你可以使用 `fct_explicit_na()` 将所有缺失数据映射到一个新的类别中：

```
f2
## [1] C B B A D
## Levels: C B A D
fna   A D
## Levels: C B A D
fna <- fct_explicit_na(fna, na_level = "Missing")
fna
## [1] C    Missing Missing A   D
## Levels: C B A D Missing
```

## 重排序水平

如果你使用 `factor()` 构造函数，可以通过 `levels` 参数来控制水平的顺序。如果你使用 `as.factor()`，水平将按名称排序；如果你使用 `as_factor()`，水平将按它们在数据中出现的顺序排序。不过，如果有需要，你也可以在这些顺序之间进行转换。

如果你使用 `factor()` 创建因子时没有指定 `levels`，那么水平将是输入中出现的所有元素，并按它们的自然顺序排序。

```
f <- factor(sample(LETTERS[1:5], 10, replace = TRUE))
f
## [1] C A B D E C B A B B
## Levels: A B C D E
```

`fct_inorder()` 函数会将你的水平顺序调整为与数据中类别出现的顺序一致——即使用 `as_factor()` 时得到的顺序。

```
fct_inorder(f)
## [1] C A B D E C B A B B
## Levels: C A B D E
```

用于重排序水平的函数也可以使因子成为有序因子。^(¹⁸)

```
fct_inorder(f, ordered = TRUE)
## [1] C A B D E C B A B B
## Levels: C < A < B < D < E
```

如果你想对水平进行排序，并不需要 `forcats` 包中的函数。使用传统的 `factor()` 就可以很好地完成。

```
factor(f, levels = sort(levels(f)))
## [1] C A B D E C B A B B
## Levels: A B C D E
factor(f, levels = sort(levels(f)), ordered = TRUE)
## [1] C A B D E C B A B B
## Levels: A < B < C < D < E
```

你可以使用 `fct_infreq()` 根据水平在因子中出现的频率对其进行排序：

```
table(f)
## f
## A B C D E
## 2 4 2 1 1
fct_infreq(f)
## [1] C A B D E C B A B B
## Levels: B A C D E
f |> fct_infreq() |> table()
##
## B A C D E
## 4 2 2 1 1
f |> fct_infreq(ordered = TRUE)
## [1] C A B D E C B A B B
## Levels: B < A < C < D < E
f |> fct_infreq() |> fct_rev() |> table()
##
## E D C A B
## 1 1 2 2 4
```

在最后一个示例中，我反转了水平顺序，因此顺序是从最小到最大，而不是从最大到最小。`fct_rev()` 函数用于反转水平顺序；它的一个常见用途是当你绘制图表，并且某个坐标轴由因子决定时，你可能希望反转其顺序。

脚注 1

# 11. 处理日期：lubridate

`lubridate` 包是处理日期所必需的，并且与 Tidyverse 配合良好。但是，在导入 `tidyverse` 包时并不会自动加载它，因此你需要显式地加载它。

```
library(lubridate)
```

## 时间点

你可以使用 `ymd()` 函数的各种变体来创建日期以及包含一天中时间的日期时间。字母 `y`、`m` 和 `d` 分别代表年、月和日。使用 `ymd()` 时，你应该将数据写成年份在前、月份其次、日期在最后的格式。该函数在解析日期方面非常灵活。

```
ymd("1975 Feb 15")
## [1] "1975-02-15"
ymd("19750215")
## [1] "1975-02-15"
ymd("1975/2/15")
## [1] "1975-02-15"
ymd("1975-02-15")
## [1] "1975-02-15"
```

如果年、月、日的顺序不同，你可以对字母 `y`、`m` 和 `d` 进行排列组合。每种排列都对应一个解析器，它会按照指定的顺序解释输入。

```
dmy("150275")
## [1] "1975-02-15"
mdy("February 15th 1975")
## [1] "1975-02-15"
```

如果你想在日期中添加一天中的时间，可以通过使用 `ymd()` 函数的 `_h()`、`_hm()` 和 `_hms()` 变体来分别添加小时、小时和分钟，或者小时、分钟和秒。

```
dmy_h("15/2/1975 2pm")
## [1] "1975-02-15 14:00:00 UTC"
dmy_hm("15/2/1975 14:30")
## [1] "1975-02-15 14:30:00 UTC"
dmy_hms("15/2/1975 14:30:10")
## [1] "1975-02-15 14:30:10 UTC"
```

如果你有一个时间对象

```
x <- dmy_hms("15/2/1975 14:30:10")
```

那么你可以通过专门的函数提取其组成部分：

```
c(day(x), month(x), year(x))
## [1]   15    2 1975
c(hour(x), minute(x), second(x))
## [1] 14 30 10
c(week(x), # 一年中的第几周
wday(x), # 一周中的第几天
yday(x)) # 一年中的第几天
## [1] 7 7 46
```

这些函数有对应的赋值函数，你可以使用它们来修改时间点的组成部分。

```
minute(x) <- 15
wday(x) <- 42
x
## [1] "1975-03-22 14:15:10 UTC"
```

## 时区

当你添加一天中的时间时，时区也是必需的。毕竟，在知道我们所在的时区之前，我们无法知道一个给定的小时对应的是什么时间。如果我告诉你我打算两点钟给你打电话，你不能想当然地认为那是你所在时区的两点钟。^(¹⁹) 除非你另外指定，否则这些函数会默认采用 UTC 时区。你可以通过 `tz` 参数指定其他时区。

```
dmy_hm(
"15/2/1975 14:00",
tz = "Europe/Copenhagen"
)
## [1] "1975-02-15 14:00:00 CET"
```

你可以通过两种不同的方式，将一个时区的时间点转换到另一个时区。一种方式是设置时区，并假定时日和时间原本就在这个时区。也就是说，你只改变对象的时区属性，而不改动时间信息本身。你可以使用 `force_tz()` 函数来完成此操作。

```
force_tz(
