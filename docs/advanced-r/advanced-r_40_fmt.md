如果我们寻找多个匹配项，可以使用 `%in%` 运算符以及我们感兴趣的列表。这允许我们将指示我们选择的编码分开，然后基于该兴趣进行过滤：

```r
interest <- c(4.3, 4.4, 4.5, 5.1, 5.2)
filter(diris, Sepal.Length %in% interest)
```