... 还有 145 行数据

按多个变量进行分组也很容易。按品种和花瓣宽度中位数布尔值进行分组，我们重新计算萼片和花瓣宽度的平均值：

```
diris  %>%
  group_by(Species, MedPW) %>%
  summarise( M1 = mean(Sepal.Width),  M2 = mean(Petal.Width))
Source: local data frame [6 x 4]
Groups: Species [?]

  Species  MedPW    M1    M2
1  setosa  FALSE   3.4  0.19
2  setosa   TRUE   3.5  0.38
3  versicolor FALSE   2.6  1.19
4  versicolor  TRUE   3.0  1.50
5  virginica FALSE   2.8  1.81
6  virginica  TRUE   3.1  2.27
```

同样，总是可以在链式操作中添加更多操作：

```
diris %>%
  group_by(Species, MedPW) %>%
  summarise(
    M1 = mean(Sepal.Width),
    M2 = mean(Petal.Width)) %>%
  group_by(MedPW) %>%
  summarise(r = cor(M1, M2))
```