# 4: 2 2 d D

## 数据重塑

除了数据合并外，数据还可以进行重塑。以我们的`iris`数据集为例：每一行代表一个独特的花卉测量值，但每一朵被测量的花都使用了多个列。尽管没有唯一的键，但每朵花（行）都有多种测量值。较宽的数据可以重塑为长数据，其中每个键出现多次，并将变量存储在更少的列中。首先，我们将数据重置为`iris`数据集，并根据行位置创建一个唯一的`ID`作为键。回想一下，数据表会隐藏行号，因此我们通过使用数据表的另一个有用简写`.N`（表示行数）来将这些行号添加到它们的`ID`列中：

```
diris <- as.data.table(iris)

diris[, ID := 1:.N]

setkey(diris, ID)
```

**注意：** 重塑用于将数据从宽格式转换为长格式，或从长格式转换为宽格式。在宽格式中，重复测量的变量具有单独的列，列名表示评估指标。在长格式中，重复变量只有一列，而另一列则表示评估指标或“时间”。将宽格式转换为长格式可通过使用`melt()`函数完成，该函数需要多个参数。

要将数据重塑为长格式，我们使用`melt()`将数据表的测量变量堆叠成一种堆叠格式。此函数需要多个参数，以下将逐一讨论。要查看所有参数，请查阅帮助文档`?melt.data.table`。指定确切的方法`melt.data.table`非常重要，因为通用函数的参数是不同的。

第一个形式参数`data`是要熔化的数据表名称，接着是`id.vars`，它包含 ID 变量以及任何不应被重塑为长格式的其他变量。默认情况下，`id.vars`是未在下一个形式参数中明确列出的任何列名。分配给`id.vars`的任何列名都不会被重塑为长格式；它们会被重复，但保持原样。第三个形式参数用于测量变量，`measure.vars`使用这些列名来堆叠数据。通常，这是一个包含单独字符向量的列表，用于指示哪些变量应该在长数据中堆叠在一起。

第四个形式参数是`variable.name`，它是新创建的变量名称，用于指示新长数据集中特定行属于`measure.vars`的哪个级别。在下面的例子中，第一个级别对应长度测量值，而第二个级别对应`iris`花朵的宽度测量值。最后，`value.name`是熔化后（或长格式）列的名称，即长数据中包含原始宽变量值的列的名称。

描述重塑及其所需参数相当复杂。对比现在熟悉的`iris`数据集与重塑后的长数据集之间的输出可能更容易理解，如下所示：

```
diris.long <- melt(diris,
                   measure.vars = list(
                     c("Sepal.Length", "Sepal.Width"),
                     c("Petal.Length", "Petal.Width")),
                   variable.name = "Type",
                   value.name = c("Sepal", "Petal"),
                   id.vars = c("ID", "Species"))
diris.long
```

```
## ID Species Type Sepal Petal
## 1: 1 setosa 1 5.1 1.4
## 2: 2 setosa 1 4.9 1.4
## 3: 3 setosa 1 4.7 1.3
## ---
## 298: 148 virginica 2 3.0 2.0
## 299: 149 virginica 2 3.4 2.3
## 300: 150 virginica 2 3.0 1.8
```

请注意，我们的数据现在因为熔化而变成了 300 行。将`Type` 1 和 2 等同于萼片和花瓣的长度与宽度测量值可能会让人困惑。数据表的`melt()`方法默认只创建一个整数值，以指示它是第一个、第二个还是第三个（等等）级别。为了使标签更具信息性，我们使用`factor()`。因为`factor`会对列进行操作并更改该列的行元素，所以它属于数据表的第二个形式参数`j`：

```
diris.long[, Type := factor(Type, levels = 1:2, labels = c("Length", "Width"))]
diris.long
```

```
## ID Species Type Sepal Petal
## 1: 1 setosa Length 5.1 1.4
## 2: 2 setosa Length 4.9 1.4
## 3: 3 setosa Length 4.7 1.3
## ---
## 298: 148 virginica Width 3.0 2.0
## 299: 149 virginica Width 3.4 2.3
## 300: 150 virginica Width 3.0 1.8
```

数据集`diris.long`是一个很好的长数据示例。然而，它并非来自`diris`的可能最长数据集——这并不是说最长数据本身是目标。但是，如果仅对`diris`调用`melt()`并指定`id.vars`，那么得到的数据表可能是`iris`数据的最长形式。正如`id.vars`默认为不在`measure.vars`中的任何值一样，`measure.vars`也默认为不在`id.vars`中的任何值。同时，你还可以看到，`variable.name`和`value.name`分别默认为`variable`和`value`。请注意，`diris.long2`有 600 行，而原先的四列萼片/花瓣长度和宽度现在变成了两列。还请注意，当这样做时，`melt()`会自动将`variable`的值设置为数据集中最初的变量名称：

```
diris.long2 <- melt(diris, id.vars = c("ID", "Species"))
diris.long2
```

```
## ID Species variable value
## 1: 1 setosa Sepal.Length 5.1
## 2: 2 setosa Sepal.Length 4.9
## 3: 3 setosa Sepal.Length 4.7
## ---
## 598: 148 virginica Petal.Width 2.0
## 599: 149 virginica Petal.Width 2.3
## 600: 150 virginica Petal.Width 1.8
```

**注意：** 要将数据从长格式重塑为宽格式，可使用`dcast()`函数。其两个必要参数是`data`和`formula`，用于指示数据应如何从长格式重塑为宽格式。该公式必须指明单元格中的唯一值，否则需要在单元格内对数据进行聚合。

虽然`melt()`用于将宽数据变长，但`dcast()`用于将长数据变宽。如果没有数据的唯一标识符，则必须聚合多个值以适合一个单元格。因此，不加考虑地调用我们的转换函数可能会产生不想要的结果（尽管有时聚合多个值正是我们想要的）。

请注意，在第二个形式参数中，`Species`被指定为标识键，而`variable`被指定为查找新列名的位置：

```
dcast(diris.long2, Species ~ variable)
```

```
## Aggregate function missing, defaulting to 'length'
## Species Sepal.Length Sepal.Width Petal.Length Petal.Width
## 1: setosa 50 50 50 50
## 2: versicolor 50 50 50 50
```