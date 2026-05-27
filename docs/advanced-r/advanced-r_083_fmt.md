# A tibble: 1 x 1
`r`

1 -0.76

变量也可以创建其他变量。我们根据花瓣宽度是否大于该品种花瓣宽度的中位数来创建一个新变量。这可能让它的外观有些变化，但这仍然是一个 `tibble`：

```
diris %>%
  group_by(Species) %>%
  mutate(MedPW = Petal.Width > median(Petal.Width))
diris
Source: local data frame [150 x 8]
Groups: Species [3]

  `Sepal.Length` `Sepal.Width` `Petal.Length` `Petal.Width` `Species`     V    V2 `MedPW`
1          5.1         3.5          1.4         0.2  setosa  0.28  0.28  FALSE
2          4.9         3.0          1.4         0.2  setosa  0.28  0.28  FALSE
3          4.7         3.2          1.3         0.2  setosa  0.26  0.26  FALSE
4          4.6         3.1          1.5         0.2  setosa  0.30  0.30  FALSE
5          5.0         3.6          1.4         0.2  setosa  0.28  0.28  FALSE
```