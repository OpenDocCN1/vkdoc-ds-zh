# 数据合并与重塑

无需重复我们在 `data.table` 章节中关于数据合并与重塑的讨论，我们注意到 `merge()` 函数调用在一定程度上仍然有效。该函数与 `data.table` 中的函数相同，并且由于我们使用 `dplyr` 对 `tibble` 进行操作（而 `tibble` 是基于数据框构建的），因此它能够工作。然而，在进行此操作后，它们会失去其 `tibble` 状态。因此，我们也引入了 `*join()` 系列函数，这些函数更接近一些更传统的数据库语言。

再次说明，我们首先运行一小段代码来创建四个独立的数据对象。请注意，在实际应用中这通常是不必要的，因为我们的数据通常来自不同的数据库：

```
diris %  group_by(Species) %>%  select(Species, Sepal.Width) %>%  slice(1:3)
diris
Source: local data frame [9 x 2]
Groups: Species [3]
Species Sepal.Width

1     setosa         3.5
2     setosa         3.0
3     setosa         3.2
4 versicolor         3.2
5 versicolor         3.2
6 versicolor         3.1
7  virginica         3.3
8  virginica         2.7
9  virginica         3.0
diris2        
1     setosa         3.5
2 versicolor         3.2
3  virginica         3.3
dalt1      
1     setosa     wide  TRUE
2     setosa     wide FALSE
3 versicolor     wide  TRUE
4 versicolor     wide FALSE
5  virginica   narrow  TRUE
6  virginica   narrow FALSE
7      other moderate  TRUE
8      other moderate FALSE
dalt2   
1     setosa  wide  TRUE
2 versicolor  wide  TRUE
```

`merge()` 函数对两个数据集进行操作。对于像 `diris2` 和 `dalt2` 这样的一对一数据，`merge` 本身只保留同时匹配两个数据集的行。我们可以看到，对于这两个对象，`diris2` 中的 `virginica` 行被丢弃了：

```
merge(diris2, dalt2)
Species Sepal.Width Type MedPW
1     setosa         3.5 wide  TRUE
2 versicolor         3.2 wide  TRUE
```

如果我们希望保留 `diris2` 的所有三行，那么在合并后就会有一些缺失数据。在这种情况下，由于我们任意选择 `diris2` 作为第一个形式参数，设置 `all.x = TRUE` 会保留 `diris2` 的所有行：

```
merge(diris2, dalt2, all.x = TRUE)
Species Sepal.Width Type MedPW
1     setosa         3.5 wide  TRUE
2 versicolor         3.2 wide  TRUE
3  virginica         3.3     NA
```

也可以设置第二个参数，即 `all.y = TRUE`，尽管在此例中这没有明显的区别。根据两个特定表的匹配程度，可能会产生不同的缺失变量模式。同样，也可以声明我们希望保留所有表中的所有行。这可能导致出现最多的缺失数据位置；在我们的示例中，这样的合并将与前面示例中显示的三行结果相同。

如前所述，这些结果不再是 `tibble`。如果我们希望合并后仍返回 `tibble`，可以将其用 `as_tibble()` 包装。然而，正如你将在我们的数据库章节中看到的，`dplyr` 也操作于数据库风格的对象上。特别是，可以通过 `dplyr` 访问结构化查询语言（SQL）。SQL 中有连接（join）操作，我们现在来看四种类型的连接。大体上概括，`merge()` 侧重于某种方式的匹配，而连接则侧重于对象 1 和对象 2 的行与列。

在 `left_join()` 中，第一个对象的所有行以及第一个和第二个对象的所有列都会被返回。由于这是一个 `dplyr` 函数调用，我们得到的输出是一个 `tibble`。如你所见，这与 `all.x=TRUE` 类似（尽管并非在所有情况下都完全等同）：

```
left_join(diris2,dalt2)
Joining, by = "Species"
Source: local data frame [3 x 4]
Groups: Species [?]
Species Sepal.Width  Type MedPW

1     setosa         3.5  wide  TRUE
2 versicolor         3.2  wide  TRUE
3  virginica         3.3      NA
```

`right_join()` 与左连接非常相似，只是它返回第二个对象的所有行。同时，仍然返回两个对象的所有列。在这种情况下，这种连接类型与 `merge()` 函数没有太大区别。然而，这种情况并非所有情况。互联网上有许多图表声称解释了连接的工作原理。根据我们的经验，这些解释的准确性时好时坏。我们的建议是，使用几个小数据表，或者像我们在这里一样自己构建几个。然后开始进行连接操作。在这个过程中，经过多次实践，你将逐渐掌握哪种技术能让你最接近想要的数据形态。

```
right_join(diris2,dalt2)
Joining, by = "Species"
Source: local data frame [2 x 4]
Groups: Species [?]
Species Sepal.Width  Type MedPW

1     setosa         3.5  wide  TRUE
2 versicolor         3.2  wide  TRUE
```

与基本 `merge()` 函数最接近的匹配是 `inner_join()` 函数，在此例中将产生与前面右连接相同的结果。相反，从合并的角度来看，最奇特的连接是 `anti-join()`。`anti_join()` 返回第一个数据库中所有在第二个数据对象中没有匹配值的行。至于列，它只返回第一个对象的列。

```
anti_join(diris2,dalt2)
Joining, by = "Species"
Source: local data frame [1 x 2]
Groups: Species [3]
Species Sepal.Width

1 virginica         3.3
```

在连接与合并之间，有许多选项可以将数据组合成单个对象。记住，你的目标最终是得到分析所需的精确数据。有时，首先将一个或多个数据集合并到一个表中是有意义的，然后使用本章前面介绍的技术将其精简到所需的数据集。当然，如果你更熟悉合并操作，它们通常也能很好地工作。如果你确实需要 `dplyr`，`as_tibble()` 始终可以作为包装函数使用，将你的对象强制转换回应有的形态。毕竟，`data.table` 和 `dplyr` 的 `tibble` 都是数据框的扩展。

我们的最后一个目标是数据重塑，这在每行有唯一标识符（ID）时效果更好。我们首先再次刷新我们的数据集。然后介绍 `n()`，这是一种引用行数的特殊方式，很像数据表中的 `.N`：

```
diris                           
1          5.1         3.5          1.4         0.2  setosa     1
2          4.9         3.0          1.4         0.2  setosa     2
3          4.7         3.2          1.3         0.2  setosa     3
4          4.6         3.1          1.5         0.2  setosa     4
5          5.0         3.6          1.4         0.2  setosa     5
```