# ... with 144 more rows

```
# ... with 144 more rows
```

如果我们寻求多个匹配项，可以使用`%in%`运算符以及我们感兴趣的列表。这允许我们将指示我们选择兴趣的编码分离出来，然后基于该兴趣进行筛选：

```r
interest <- c(4.3, 4.4, 4.5)
filter(diris, Sepal.Length %in% interest)
```