```
# ... with 95 more rows
```

除了逻辑索引外，也可以基于数值使用不等式、等式或非等式来选择行。数值和字符测试可以混合和匹配，以精确提取所需的行：

```
filter(iris, Sepal.Length  .2)
Sepal.Length Sepal.Width Petal.Length Petal.Width    Species
1          4.6         3.4          1.4         0.3     setosa
2          4.5         2.3          1.3         0.3     setosa
3          4.8         3.0          1.4         0.3     setosa
4          4.9         2.4          3.3         1.0 versicolor
5          4.9         2.5         4.5         1.7  virginica
```

```
filter(diris, Sepal.Length == 4.3 | Sepal.Width != 4.4)
```