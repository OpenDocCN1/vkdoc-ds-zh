# A tibble: 3 x 3
Species    M1    M2

1     setosa   3.4  0.25
2 versicolor   2.8  1.33
3  virginica   3.0  2.03
```

这种链式结构，就像数据表的链式结构一样，可以无限（或接近无限）地进行下去。要在上述数据中找到一个相关性，我们只需在链的末尾再添加一个步骤：

```
diris %>%
group_by(Species) %>%
summarise( M1 = mean(Sepal.Width),  M2 = mean(Petal.Width)) %>%
summarise(r = cor(M1, M2))

