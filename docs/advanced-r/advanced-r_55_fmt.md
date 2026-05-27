# 一个 tibble：2 行 x 2 列

| `MedPW` | `r`    |
|---------|--------|
| FALSE   | -0.78  |
| TRUE    | -0.76  |

## 数据合并与重塑

我们不会重复在 `data.table` 章节中关于合并与重塑数据的内容，只提一点：`merge()` 函数调用在某种程度上仍然有效。这个函数与 `data.table` 中的函数是同一个，因为我们使用 `dplyr` 对构建在数据框基础上的 `tibble` 进行操作，所以它能工作。然而，一旦这样做了，它们就会失去 `tibble` 的身份。因此，我们还引入了 `*join()` 函数，这些函数更接近于一些传统的数据库语言。

同样，我们先运行一段代码来创建四个独立的数据对象。请再次注意，在实际生活中这通常是不需要的，因为我们的数据通常来自不同的数据库：

```r
diris %>%  group_by(Species) %>%  select(Species, Sepal.Width) %>%  slice(1:3)
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

`merge()` 函数操作两个数据集。对于像 `diris2` 和 `dalt2` 这样的一对一数据，合并本身只保留两个数据集中都匹配的行。我们可以看到，对于这两个对象，`diris2` 中的 `virginica` 行被丢弃了：

```r
merge(diris2, dalt2)
Species Sepal.Width Type MedPW
1     setosa         3.5 wide  TRUE
2 versicolor         3.2 wide  TRUE
```

如果我们想保留 `diris2` 的全部三行，那么合并后会有一些缺失数据。在这种情况下，因为我们任意选择 `diris2` 作为我们的第一个形式，`all.x = TRUE` 会保留 `diris2` 的所有行：

```r
merge(diris2, dalt2, all.x = TRUE)
Species Sepal.Width Type MedPW
1     setosa         3.5 wide  TRUE
2 versicolor         3.2 wide  TRUE
3  virginica         3.3     NA
```

也可以设置第二个值，即 `all.y = TRUE`，尽管在这种情况下没有明显区别。根据两个特定表格匹配程度的不同，缺失变量的模式可能不同。类似地，也可以指定我们希望从所有表中获取所有行。这可能会产生最多的缺失数据位置；在我们的例子中，这样的合并将与前面示例中显示的三行完全相同。

如前所述，这些不是 `tibble`。如果我们希望合并后再强制转换回 `tibble`，可以用 `as_tibble()` 包装它们。然而，正如你将在我们的数据库章节中看到的，`dplyr` 也操作数据库风格的对象。特别是，可以通过 `dplyr` 访问结构化查询语言 (SQL)。SQL 有连接操作，我们现在来看四种类型的连接。笼统地说，`merge()` 侧重于某种方式的匹配。连接则侧重于对象 1 和对象 2 的行与列之间的关系。

`left_join()` 会返回第一个对象的所有行，以及第一个和第二个对象的全部列。因为这是一个 `dplyr` 函数调用，我们得到的是一个 `tibble` 作为输出。如你所见，这与 `all.x=TRUE` 类似（尽管并非在所有情况下都完全匹配）：

```r
left_join(diris2,dalt2)
Joining, by = "Species"
Source: local data frame [3 x 4]
Groups: Species [?]
Species Sepal.Width  Type MedPW

1     setosa         3.5  wide  TRUE
2 versicolor         3.2  wide  TRUE
3  virginica         3.3      NA
```

`right_join()` 与左连接非常相似，区别在于它返回第二个对象的所有行。同样，两个对象的所有列都会被返回。在这种情况下，这种连接与 `merge()` 函数没有太大区别。然而，这种情况并不代表所有情况。网上很多图表声称揭示了连接的工作原理。根据我们的经验，这些解释的准确性可能参差不齐。我们的建议是找几个小的数据表，或者像我们这样自己创建一些。然后开始尝试连接。在反复实践之后，你会逐渐掌握哪种技术最接近你想要的最终数据。

```r
right_join(diris2,dalt2)
Joining, by = "Species"
Source: local data frame [2 x 4]
Groups: Species [?]
Species Sepal.Width  Type MedPW

1     setosa         3.5  wide  TRUE
2 versicolor         3.2  wide  TRUE
```

与基本 `merge()` 函数最匹配的是 `inner_join()` 函数，在本例中，它会生成与之前的右连接相同的结果。相比之下，从合并的角度来看，最奇特的是 `anti_join()`。`anti_join()` 返回第一个数据库中在第二个数据对象中没有匹配值的所有行。至于列，它只返回第一个对象的列。

```r
anti_join(diris2,dalt2)
Joining, by = "Species"
Source: local data frame [1 x 2]
Groups: Species [3]
Species Sepal.Width

1 virginica         3.3
```

在连接和合并之间，有很多选项可以将数据组合成一个对象。记住，你的最终目标是获取分析所需的数据。有时，先将一个或多个数据集合合并成一个表格，然后使用本章前面介绍的技术将其缩减为你想要的数据集，是合理的做法。当然，如果你更熟悉合并，它们通常也能很好地工作。如果你确实需要使用 `dplyr`，`as_tibble()` 总是可以作为包装函数，将你的对象强制转换回应有的格式。毕竟，`data.table` 和 `dplyr` 的 `tibble` 都是数据框的扩展。

我们的最后一个目标是重塑数据，如果每行都有一个唯一的 ID，效果会更好。我们先再次刷新一下数据集。然后引入 `n()`，这是一种引用行数的特殊方式，就像数据表中的 `.N` 一样：

```r
diris                           
1          5.1         3.5          1.4         0.2  setosa     1
2          4.9         3.0          1.4         0.2  setosa     2
3          4.7         3.2          1.3         0.2  setosa     3
4          4.6         3.1          1.5         0.2  setosa     4
5          5.0         3.6          1.4         0.2  setosa     5

# ... 还有 145 行
```

要使用 `reshape()` 变长，我们使用基础 R 函数。然后，我们用 `varying` 标签来标明重复测量的变量或我们希望在长数据集中堆叠的变量。`timevar` 标签接收指示一行属于哪个堆栈的变量名。之所以这样命名，是因为这些变量通常按时间排序。用 `v.names` 指定新堆叠变量的名称，并用 `idvar` 指定 ID 变量。任何未在 `varying` 中列出的变量将被视为不变变量，并会简单重复。

```r
diris.long     
1  setosa     1     1   5.1   1.4
2  setosa     2     1   4.9   1.4
3  setosa     3     1   4.7   1.3
4  setosa     4     1   4.6   1.5
5  setosa     5     1   5.0   1.4

# ... 还有 295 行
```

此时，`Type` 的含义可能还不清楚。`Type` 告诉我们当前涉及的是花萼还是花瓣的长度还是宽度。当我们的数据从宽变长时，其长度翻倍。现在，`Sepal` 和 `Petal` 列必须承担双重任务，而 `Type` 就是用来区分它们的。来看看我们数据的一个 `slice()`：

```r
slice(diris.long, c(1, 151))
```