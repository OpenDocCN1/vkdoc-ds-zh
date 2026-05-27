# A tibble: 1 x 1
M

1     3
```

这种一步一步进行的思路可以修改以处理各种数据操作。回顾我们对 `data.table` 的研究，该结构的第三个形参 `by =` 允许我们按列的元素进行组织。对于 `dplyr`，我们在列名上使用 `group_by()` 来完成相同的任务。将我们的均值示例扩展到所有三个品种，我们现在有一个两列的 `tibble`，每个品种一行。我们还可以获取多个原始列的均值。注意 `virginica` 的萼片宽度均值是 `3`，无论我们如何计算。

```
diris %>%  group_by(Species) %>%  summarise(M1 = mean(Sepal.Width), M2 = mean(Petal.Width))
```