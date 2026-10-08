# 连接表

## 连接表

你的数据分布在多个表中是很常见的情况。这可能是由于表格是根据不同的计算（例如不同类型的汇总）创建的，也可能是因为你从不同文件中获取了数据。

如果你只需要按行或按列合并表格，那么可以使用`bind_rows()`和`bind_cols()`函数，它们的功能正如你所预期的那样。

```r
df1
## # A tibble: 4 × 2
##   A     B
##   <chr> <chr>
## 1 a1    b1
## 2 a2    b2
## 3 a3    b3
## 4 a4    b4

bind_cols(df1, df3)
## # A tibble: 2 × 4
##   A     B     C     D
##   <chr> <chr> <chr> <chr>
## 1 a1    b1    c1    d1
## 2 a2    b2    c2    d2
```

当你按行合并时，表格必须有相同的列；当你按列合并时，表格必须有相同的行数。

如果你有代表变量之间不同关系的表格（这是关系数据库旨在避免数据重复的基本原则），那么你可以使用连接函数来合并它们。

假设你有一个表格，记录了你所教的每个班级中学生与成绩的对应关系。你可以使用`inner_join()`来连接两个不同班级的表格。你需要用一个键来指定连接依据，该键由参数`by`设定。

```r
grades_maths
## # A tibble: 3 × 3
##   name           grade.x grade.y
##   <chr>          <chr>   <chr>
## 1 Marko Polo     D       F
## 2 Isaac Newton   A+      D
## 3 Charles Darwin B       A+
```

这告诉`inner_join()`，你希望将第一个表中所有行与第二个表中所有行进行合并，合并条件是两行具有相同的姓名。如果你给`by`参数传递一个变量名称的向量，你可以在一次连接中使用多个键。

在前面，每个姓名在每个表中仅出现一次。如果一个键出现多次，那么内连接的结果会生成一个包含该姓名下所有行组合的列表。

```r
grades_maths2
## # A tibble: 5 × 3
##   name           grade.x grade.y
##   <chr>          <chr>   <chr>
## 1 Marko Polo      D       F
## 2 Isaac Newton    A+      D
## 3 Isaac Newton    A+      D
## 4 Charles Darwin  B       A+
## 5 Charles Darwin  B       A+

inner_join(grades_maths2, grades_biology2, by = "grade")
## # A tibble: 5 × 3
##   name.x          grade name.y
##   <chr>           <chr> <chr>
## 1 Marko Polo      D     Isaac Newton
## 2 Isaac Newton    A+    Charles Darwin
## 3 Isaac Newton    A+    Charles Darwin
## 4 Isaac Newton    A+    Charles Darwin
## 5 Isaac Newton    A+    Charles Darwin
```

在最后这次连接中，你会看到内连接可能会多次返回相同的行。如果你希望避免这种情况，可以将连接与`distinct()`结合使用。

```r
inner_join(grades_maths2, grades_biology2, by = "grade") |> distinct()
## # A tibble: 2 × 3
##   name.x          grade name.y
##   <chr>           <chr> <chr>
## 1 Marko Polo      D     Isaac Newton
## 2 Isaac Newton    A+    Charles Darwin
```

这些表格可以拥有不同的变量（如果它们代表不同的关系），也可以共享部分或全部变量。在上述成绩的例子中，每个表格都包含相同的姓名/成绩关系，只是针对不同的班级。因此，它们也共享变量。

当存在共享变量时，可以通过后缀来区分来自两个表格的数据。默认的后缀，如上所示，是`.x`和`.y`。你可以使用`suffix`参数来更改它们。

```r
inner_join(
    `grades_maths`, `grades_biology`,
    by = "name", suffix = c(".maths", ".biology")
)
## # A tibble: 3 × 3
##   name            grade.maths grade.biology
##   <chr>           <chr>       <chr>
## 1 Marko Polo      D           F
## 2 Isaac Newton    A+          D
## 3 Charles Darwin  B           A+
```

学生可能选择不同的课程，因此你有多种选择来合并包含不同键的表格。`inner_join()`只返回键同时存在于两个表中的那些行。
