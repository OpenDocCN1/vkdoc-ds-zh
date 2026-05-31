# A tibble: 12 x 5
Sepal.Length Sepal.Width Petal.Length Petal.Width Species

1          4.3         3.0          1.1         0.1  setosa
2          4.4         3.2          1.3         0.2  setosa
3          4.4         3.0          1.3         0.2  setosa
4          4.4         2.9          1.4         0.2  setosa
```

或者，如果我们希望排除具有特定特征的任何行，可以通过取反来实现。这对于移除异常值或错误数据点很有用：

```
filter(diris, !Sepal.Length %in% interest)

