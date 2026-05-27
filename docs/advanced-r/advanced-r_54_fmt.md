# 变量重命名与排序

使用我们原始的 `select()` 函数实现相同结果的另一种方法是使用 `select` 辅助函数之一。共有七个这样的辅助函数，我们目前想要的是 `one_of()`，它用于管理字符向量：

```r
select(diris, one_of(v))

# A tibble: 150 x 1
Sepal.Length

1          4.3
2          4.4
3          4.4
4          4.4
5          4.5

# ... 还有 145 行
```

在使用 `dplyr` 的数据结构 `tibble` 时，像在数据表中使用的方法也能奏效。但这些方法并不能保证在 `dplyr` 的整个使用过程中都能顺利转换。最好还是用 `dplyr` 的“母语”来使用它。不过，为了完整性，下面给出两个函数调用示例：

```r
diris[, v]

# A tibble: 150 x 1
Sepal.Length

1          4.3
2          4.4
3          4.4
4          4.4
5          4.5

# ... 还有 145 行
diris[, 1]

# A tibble: 150 x 1
Sepal.Length

1          4.3
2          4.4
3          4.4
4          4.4
5          4.5

# ... 还有 145 行
```

到目前为止，我们所有的操作都会返回另一个 `tibble` 作为输出。要将原始数据提取为向量，需要使用几种不同的技术。如果我们知道包含所需数据的列名，直接键入名称很容易。如果我们的列名可能是可变的，则可以通过第三种方法重用我们的变量 `v`。无论使用哪种方法，控制台都会打印相同的数据。请注意，这些数据是向量，而不是数据表结构。我们用粗体标记了将列数据作为向量访问的代码，只是为了强调。

```r
head(diris[["Sepal.Length"]])
[1] 4.3 4.4 4.4 4.4 4.5 4.6
head(diris$Sepal.Length)       # easy to type
[1] 4.3 4.4 4.4 4.4 4.5 4.6
head(diris[[v]])               # easy if you have a R variable
[1] 4.3 4.4 4.4 4.4 4.5 4.6
```

排除某列或多列可能比选择特定列更有意义。排除操作使用多种格式的否定。特别是，要排除的列可以通过名称或位置来指定：

```r
select(diris, -1)

# A tibble: 150 x 4
Sepal.Width Petal.Length Petal.Width Species

1         3.0          1.1         0.1  setosa
2         3.2          1.3         0.2  setosa
3         3.0          1.3         0.2  setosa
4         2.9          1.4         0.2  setosa
5         2.3          1.3         0.3  setosa

# ... 还有 145 行
select(diris, -Sepal.Length, -Petal.Width)

# A tibble: 150 x 3
Sepal.Width Petal.Length Species

1         3.0          1.1  setosa
2         3.2          1.3  setosa
3         3.0          1.3  setosa
4         2.9          1.4  setosa
5         2.3          1.3  setosa

# ... 还有 145 行
```

同样，当需要进行变量排除时，我们设置一个变量来保存向量，并使用 `paste0()` 将否定符号粘贴到该变量的前面，然后使用 `select_()` 调用来处理字符串。或者，我们可以在常用的 `select()` 函数中使用 `select` 辅助函数 `one_of()` 进行否定操作。无论哪种方式，我们都能得到相同的结果：

```r
ex                   
1         3.0          1.1         0.1  setosa
2         3.2          1.3         0.2  setosa
3         3.0          1.3         0.2  setosa
4         2.9          1.4         0.2  setosa
5         2.3          1.3         0.3  setosa

# ... 还有 145 行
select(diris, -one_of(v))

# A tibble: 150 x 4
Sepal.Width Petal.Length Petal.Width Species

1         3.0          1.1         0.1  setosa
2         3.2          1.3         0.2  setosa
3         3.0          1.3         0.2  setosa
4         2.9          1.4         0.2  setosa
5         2.3          1.3         0.3  setosa

# ... 还有 145 行
```

现在我们已经多次使用这个 `select` 便捷函数，我们简要地看看另外四个这样的函数。这四个函数的目标是帮助我们选择变量，而无需键入完整的列名。按照不特定的顺序，`starts_with()`、`ends_with()`、`contains()` 和 `matches()` 都使用正则表达式，默认在当前变量列表中查找，并且默认忽略大小写，不过这个特性可以关闭。与其全部演示一遍，不如用下面的代码展示两个使用 `starts_with()` 的例子。`dplyr` 方法的一个优点是创建命名简洁、专注于单一任务的函数（通常以动词命名），这使得从函数名称就能很容易地知道它的功能。其余辅助函数的操作方式与 `starts_with()` 类似，并且其功能完全如其名称所示：

```r
select(diris, starts_with("s"))

# A tibble: 150 x 3
Sepal.Length Sepal.Width Species

1          4.3         3.0  setosa
2          4.4         3.2  setosa
3          4.4         3.0  setosa
4          4.4         2.9  setosa
5          4.5         2.3  setosa

# ... 还有 145 行
select(diris, starts_with("s", ignore.case = FALSE))

# A tibble: 150 x 0
```

通过这最后一段代码，我们对 `dplyr` 子集操作的探索就结束了。接下来，我们将注意力转向变量重命名和排序。

## 变量重命名与排序

列名需要重命名或重新排序是数据清理中常见且令人遗憾的一部分。在一个数据库中合理（或“合法”）的列名，在另一个数据库中可能行不通。许多专有数据管理系统都拥有固定但笨重的名称。

查看与 `dplyr` 对象关联的名称，需要使用常用的 `names()` 或 `colnames()` 函数，并将我们的数据集 `diris` 作为参数传入。无论使用哪种方式，都能在当前列顺序下得到相同的结果：

```r
names(diris)
[1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"  "Species"
colnames(diris)
[1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"  "Species"
```

要更改名称，请在数据对象上调用 `rename()` 函数，并使用以下格式设置第二个形参的新名称：`新名称 = 旧名称`。可以通过将更多列作为额外的参数（例如，在第三、第四个形参中）传递给函数，来同时重命名多个列。通过比较 `names()` 的结果与之前的旧结果，可以确认更改是否生效。在下面的代码中，我们删除了数据中 `Sepal` 和 `Length` 之间的句点：

```r
diris <- rename(diris, SepalLength = Sepal.Length)
names(diris)
[1] "SepalLength"  "Sepal.Width"  "Petal.Length" "Petal.Width"  "Species"
```

## 数据计算与创建变量

为了重新排序列，将长度和宽度配对在一起，只需使用`select()`函数并为你的数据对象重新赋值即可。请务必记住，我们已经将`SepalLength`重命名，中间不再有句点！

```
diris                          
1         4.3          1.1         3.0         0.1  setosa
2         4.4          1.3         3.2         0.2  setosa
3         4.4          1.3         3.0         0.2  setosa
4         4.4          1.4         2.9         0.2  setosa
5         4.5          1.3         2.3         0.3  setosa

# ... 还有 145 行
```

之前，你已经看到数字形式的`select`调用可以重新排序列，所以我们将用一个最后的重新排序示例来结束本节。在这里，我们使用一个变量来保存数字位置，尽管我们也可以使用字符名称来代替数字。然后，我们使用`select_()`函数将我们的列重新组织回它们原始的顺序。

```
v                          
1         4.3         3.0          1.1         0.1  setosa
2         4.4         3.2          1.3         0.2  setosa
3         4.4         3.0          1.3         0.2  setosa
4         4.4         2.9          1.4         0.2  setosa
5         4.5         2.3          1.3         0.3  setosa
```

```
# ... 还有 145 行
```

既然你已经对如何在`dplyr`中管理原始数据有了很好的了解，我们将开始关注如何从旧数据中创建新数据。为简化起见，我们从原始`iris`数据集重新创建`diris`数据集。然后，我们将准备好使用新函数`mutate()`来创建或替换列。该函数接受通常的第一个参数，后续参数则表达了创建新列所需的操作。无论是一个还是多个列，只需创建表达式，让`dplyr`知道要向`tibble`添加什么内容：

```
diris                             
1          5.1         3.5          1.4         0.2  setosa     0     1     2
2          4.9         3.0          1.4         0.2  setosa     0     1     2
3          4.7         3.2          1.3         0.2  setosa     0     1     2
4          4.6         3.1          1.5         0.2  setosa     0     1     2
5          5.0         3.6          1.4         0.2  setosa     0     1     2

# ... 还有 145 行
```

当然，新数据并不一定是创建全新的列。相反，我们可能会基于现有信息创建新的计算列。也许我们想要一个名为`V`的新列，它是`Petal.Length`和`Petal.Width`的乘积。为清晰起见，我们首先移除刚刚添加的三个列：

```
diris                           
1          5.1         3.5          1.4         0.2  setosa  0.28
2          4.9         3.0          1.4         0.2  setosa  0.28
3          4.7         3.2          1.3         0.2  setosa  0.26
4          4.6         3.1          1.5         0.2  setosa  0.30
5          5.0         3.6          1.4         0.2  setosa  0.28

# ... 还有 145 行
```

更复杂的计算是在特定条件下才执行。在这种情况下，`if_else()`接受三个参数。第一个是用于测试 if/else 语句的逻辑条件。第二个和第三个参数指示在`if`或`else`情况下该如何处理。此外，`mutate()`不仅允许我们创建包含数据的列，还可以更改现有列的数据。因此，如果我们在第一次尝试中创建的列`V2`不足以体现`setosa`物种花瓣宽度与长度的乘积关系，我们可以将其扩展为对`virginica`物种进行平方根运算。为了确保我们看到每个物种的样本，我们使用`slice()`函数仅查看三个特定行：

```
diris                               
1          5.1         3.5          1.4         0.2     setosa  0.28  0.28
2          7.0         3.2          4.7         1.4 versicolor  6.58    NA
3          6.3         3.3          6.0         2.5  virginica 15.00  3.87
```

不一定要创建新列；也可以使用`summarise()`函数直接在表格上计算信息。与往常一样，第一个参数告诉函数要使用哪些数据，其余参数则用于容纳各个计算。请注意，可以编写一个或多个计算。这里，我们演示了两种计算：算术平均值和标准差：

```
summarise(diris, M = mean(Sepal.Width), SD = sd(Sepal.Width))

# A tibble: 1 x 2
M    SD

1   3.1  0.44
```

与`data.table`不同，`dplyr`通过函数工作。此外，虽然你的数学教授可能花了一些时间讲解函数组合（并且将函数嵌套在彼此内部的技术有其用武之地），但在`dplyr`中有更清晰的写法。使用`%>%`操作符时，将多个函数链接在一起，我们便能得到期望的结果。假设我们想提取数据，仅使用`virginica`物种，并计算萼片宽度的平均值。结果应该是一个只包含一个变量（即我们寻找的平均值）的`tibble`。请注意，当使用`%>%`操作符时，数据集会被传递给第一个参数。因此，即使在下面的代码中，我们看起来指定了`filter()`的第一个形式参数，但由于使用了`%>%`操作符，函数被修改了：数据集作为第一个参数传递，而我们写的`Species == "virginica"`则作为第二个参数传递：

```
diris  %>%  filter(Species == "virginica") %>%  summarise(M = mean(Sepal.Width))

# A tibble: 1 x 1
M

1     3
```

这种一步接一步的思路可以进行调整以应对各种数据处理操作。回想一下我们对`data.table`的研究，其结构的第三个形式参数`by =`允许我们按列的元素进行分组。对于`dplyr`，我们使用`group_by()`并指定列名来完成相同的任务。将我们的平均值示例扩展到所有三个物种，现在我们得到一个两列的`tibble`，每个物种对应一行。我们还可以计算原始列中多个列的平均值。请注意，无论我们如何计算，`virginica`的萼片宽度平均值都是`3`。

```
diris %>%  group_by(Species) %>%  summarise(M1 = mean(Sepal.Width), M2 = mean(Petal.Width))

# A tibble: 3 x 3
Species    M1    M2

1     setosa   3.4  0.25
2 versicolor   2.8  1.33
3  virginica   3.0  2.03
```

这种链式结构，就像数据表的链式结构一样，可以无限进行下去（或接近无限）。要找出上述数据中的相关性，只需在链的末尾再添加一步：

```
diris %>%
group_by(Species) %>%
summarise( M1 = mean(Sepal.Width),  M2 = mean(Petal.Width)) %>%
summarise(r = cor(M1, M2))

# A tibble: 1 x 1
r

1 -0.76
```

变量也可以创建其它变量。我们根据花瓣宽度是否大于该物种花瓣宽度的中位数来创建一个新变量。这可能稍微改变了它的外观，但它仍然是一个`tibble`：

```
diris %
group_by(Species) %>%
mutate(MedPW = Petal.Width > median(Petal.Width))
diris
Source: local data frame [150 x 8]
Groups: Species [3]
Sepal.Length Sepal.Width Petal.Length Petal.Width Species     V    V2 MedPW

1          5.1         3.5          1.4         0.2  setosa  0.28  0.28 FALSE
2          4.9         3.0          1.4         0.2  setosa  0.28  0.28 FALSE
3          4.7         3.2          1.3         0.2  setosa  0.26  0.26 FALSE
4          4.6         3.1          1.5         0.2  setosa  0.30  0.30 FALSE
5          5.0         3.6          1.4         0.2  setosa  0.28  0.28 FALSE

# ... 还有 145 行
```

按多个变量分组也很容易。按物种和中位花瓣宽度布尔值分组，我们重新计算萼片宽度和花瓣宽度的平均值：

```
diris  %>%
group_by(Species, MedPW) %>%
summarise( M1 = mean(Sepal.Width),  M2 = mean(Petal.Width))
Source: local data frame [6 x 4]
Groups: Species [?]
Species MedPW    M1    M2

1     setosa FALSE   3.4  0.19
2     setosa  TRUE   3.5  0.38
3 versicolor FALSE   2.6  1.19
4 versicolor  TRUE   3.0  1.50
5  virginica FALSE   2.8  1.81
6  virginica  TRUE   3.1  2.27
```

同样，始终可以在链上继续添加更多操作：

```
diris %>%
group_by(Species, MedPW) %>%
summarise(
M1 = mean(Sepal.Width),
M2 = mean(Petal.Width)) %>%
group_by(MedPW) %>%
summarise(r = cor(M1, M2))
```