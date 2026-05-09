# ... 还有 145 行
```

可以通过 `anyDuplicated()` 查看整个数据集或仅特定列出现重复时的首行索引。当对整个数据集调用此函数时，它会查找所有列之间的匹配项，在本数据集中，第 `78` 行和第 `79` 行出现了这种情况。如下代码所示，萼片长度中重复的变量出现在第三行：

```
anyDuplicated(diris)
[1] 79
anyDuplicated(diris$Sepal.Length)
[1] 3
```

`anyDuplicated()` 返回第一个重复行的索引，而 `duplicated` 则简单地告诉我们表中发生了多少次重复。查看我们的代码，我们发现对于所有列的完全匹配，仅在第 `79` 行发生了一次。另一方面，重复的萼片长度可能在第三行首次出现，但还有更多重复。

```
table(duplicated(diris))
FALSE  TRUE
149     1
table(duplicated(diris$Sepal.Length))
FALSE  TRUE
35   115
```

如果我们希望仅查看具有不同值的表格结果，`distinct()` 的工作方式与 `arrange()` 非常相似。第一个形式参数用于数据，其余参数指定列名变量。如果未指定列名，`distinct()` 将对所有列进行操作。回想一下 `duplicated()` 调用，有 `149` 个唯一行。

```
distinct(diris)

