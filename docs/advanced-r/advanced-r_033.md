# ... with 145 more rows
```

可以通过 `anyDuplicated()` 查看出现重复时的第一行索引，无论是针对整个数据集还是仅针对特定列。在整个数据集上调用此函数时，它会查找所有列之间的匹配，这在此数据集中的第 `78` 行和第 `79` 行发生。在以下代码中显示，萼片长度中的重复变量出现在第三行：

```
anyDuplicated(diris)
[1] 79
anyDuplicated(diris$Sepal.Length)
[1] 3
```

而 `anyDuplicated()` 返回第一个重复项的行索引，`duplicated` 只是告诉我们在我们的表中有多少次这样的重复发生。查看我们的代码，我们看到对于所有列的完全匹配，第 `79` 行是唯一一次出现这种情况。另一方面，萼片长度的重复可能第一次出现在第 `3` 行，但还有很多其他的重复。

```
table(duplicated(diris))
FALSE  TRUE
149     1
table(duplicated(diris$Sepal.Length))
FALSE  TRUE
35   115
```

如果我们想查看仅包含不同值的表结果，`distinct()` 的工作方式很像 `arrange()`。第一个形式参数是针对数据的，其余的参数指定列名变量。如果没有指定列名，`distinct()` 对所有列进行操作。从 `duplicated()` 调用中回忆，有 `149` 个唯一的行。

```
distinct(diris)

