# 第 9 章 其他数据管理包

```
## 4 4.4 2.9 1.4 0.2
## 5 4.5 2.3 1.3 0.3
## # ... with 145 more rows
```

通过这最后一段代码，我们对 `tidyverse` 子集操作的学习就告一段落了。接下来，我们将注意力转向变量重命名和排序。

## 9.3 变量重命名与排序

在数据清理过程中，列名需要重命名或重新排序是一个令人遗憾但常见的环节。在一个数据库中合理（或“合法”）的名称，在另一个数据库中可能无法使用。许多专有数据管理系统都有固定的、不含糊的名称。查看与 `tidyverse` 对象关联的名称，需要对我们数据集 `diris` 调用常规的 `names()` 或 `colnames()` 函数。无论哪种方式，在当前列顺序下，我们都能得到相同的结果：

```
diris %>% names()
```

```
## [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"
## [5] "Species"
```

```
diris %>% colnames()
```

```
## [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"
## [5] "Species"
```

要更改名称，请对数据对象调用 `rename()` 函数，并使用以下格式在第二个形式参数中设置新名称：`新名称 = 旧名称`。

可以通过将多个列作为附加参数（例如，在第三个或第四个形式参数中）一次性重命名。通过将 `name()` 的结果与旧结果进行比较，来确认更改是否生效。在以下代码中，我们将数据中 `Sepal` 和 `Length` 之间的句点删除：

```
diris <- diris %>% rename(SepalLength = Sepal.Length)
```

```
diris %>% names()
```

```
## [1] "SepalLength"  "Sepal.Width"  "Petal.Length" "Petal.Width"
## [5] "Species"
```