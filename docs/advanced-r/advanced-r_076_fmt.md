# A tibble: 150 x 0
```

通过最后这段代码，我们在`dplyr`中进行数据子集选取的旅程就结束了。现在我们将注意力转向变量重命名和排序。

## 变量重命名与排序

列名需要重命名或重新排序，这在数据维护中是一个令人遗憾但又常规的操作。在一个数据库中有意义（或“合法”）的名称，在另一个数据库中可能行不通。许多专有数据管理系统有固定的名称，这些名称很繁琐。

查看与`dplyr`对象关联的名称需要使用通常在数据集`diris`上调用的`names()`或`colnames()`函数。无论使用哪种方法，对于当前的列顺序，我们都会得到相同的结果：

```
names(diris)
[1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"  "Species"
colnames(diris)
[1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"  "Species"
```

要更改名称，在数据对象上调用`rename()`函数，并按以下格式在第二个形参中设置新名称：`新名称 = 旧名称`。可以通过传递额外的参数（例如，在第三个、第四个形参中）一次性重命名多个列。通过比较`names()`的新结果与旧结果来确认更改是否生效。在以下代码中，我们移除了数据中`Sepal`和`Length`之间的句点：

```
diris <- rename(diris, SepalLength = Sepal.Length)
names(diris)
[1] "SepalLength"  "Sepal.Width"  "Petal.Length" "Petal.Width"  "Species"
```

要将列重新排序，使长度和宽度成对排列，只需使用`select()`函数并为你的数据对象重新赋值。请务必记住我们已经将`SepalLength`重命名，中间不再有句点！

```
diris
1         4.3          1.1         3.0         0.1  setosa
2         4.4          1.3         3.2         0.2  setosa
3         4.4          1.3         3.0         0.2  setosa
4         4.4          1.4         2.9         0.2  setosa
5         4.5          1.3         2.3         0.3  setosa
```