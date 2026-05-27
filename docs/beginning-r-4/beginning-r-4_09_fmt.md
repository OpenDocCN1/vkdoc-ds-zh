# 4. 数据操作

本书旨在让你深入了解现实世界的统计方法。与许多“课堂就绪”的示例不同，现实世界的数据在进行任何统计之前通常需要预先处理。在本章中，你将学习如何使用 `data.table` [9] 和 `extraoperators` [20] 包来创建应用统计方法分析数据并做出数据驱动决策所需的数据集。

这是一项艰巨的任务，所以请记住，一开始觉得 `data.table` 有些令人困惑是很正常的。尽管如此，请继续探索，继续尝试，务必完成本章末尾的理解力检查和练习题，并且随时可以寻求帮助！

学完本章后，你应该能够：

-   将 `R` 数据转换为 `data.table` 格式。
-   理解 `data.table` 结构的三个部分。
-   应用逻辑运算符，通过*子集选取*从较大的数据集中提取目标样本。
-   基于当前数据创建新列。
-   对数据集应用 `by` 分组计算。

## 4.1 准备工作

为了练习操作 `data.table` 格式的信息，我们需要做一些准备工作。为了继续练习创建和使用项目，我们将为本章启动另一个新项目。在该项目中，我们还将创建一些文件夹，并从本书的 `GitHub` 网站下载一些数据文件。

如有必要，请回顾第 1 章中的步骤来创建一个新项目。启动 RStudio 后，在左上角的菜单栏中，选择 *File* 并点击 *New Project*。选择 *New Directory* ➤ *New Project*，*Directory name* 填写 `04WorkingWithData`，然后选择 *Create Project*。要创建一个 `R` 脚本文件，请在最顶部的功能区上，*File* 字样下方，点击带有加号图标的空白纸张小图标，然后选择 *R Script* 菜单选项。点击软盘形状的 *save* 图标，将此文件命名为 `WorkwData.R`，然后点击 *Save*。

在右下角窗格中，你应该会看到你的项目的两个文件，在 *Files* 选项卡正下方，点击名为 *New Folder* 的按钮。在 *New Folder* 弹出框中，输入 `data` 并选择 *OK*。在右下角窗格中，点击你新建的 *data* 文件夹。重复文件夹创建过程，创建一个名为 `ch04` 的新文件夹。

接下来，你需要从本书的网站下载一些文件。这些文件应存放在 *GitHub* 上的 `http://github.com/apress/beginning-r-4`。但是，永久链接也可以通过在网站 `http://mattwiley.org/publications.html` 上点击 *Beginning R* 的 *Code* 来找到。导航到本章的文件夹，将德克萨斯州县数据 [3] 和美国社区调查 [4] 的数据下载到你刚刚创建的 `ch04` 文件夹中。总的来说，你的项目子文件夹中应该有六个文件。

请记住，如果你已经在第 3 章中下载了这些文件，你可以简单地将这些文件从第 3 章的项目中复制过来。但是，请务必重命名文件夹，以便你的数据存放在此路径下：`YourProjectFolder/data/ch04`。如果你为本章创建了一个独立项目，那么你已经位于 `04WorkingWithData` 文件夹中，并且该文件夹下会有 `data/ch04` 两个子文件夹。

本章我们将使用四个包。请记住，所有这些包已经在第 2 章中安装到你的本地计算机上了。无需重新安装。但是，由于这是一个新项目，并且我们是首次运行这组代码。因此，你需要运行以下四个 `library()` 调用：

```
library(data.table)
## data.table 1.13.0 using 6 threads (see ?getDTthreads). Latest news: r-datatable.com
library(extraoperators)
library(JWileymisc)
library(palmerpenguins)
```

应用我们在第 3 章学到的知识，我们使用 `data.table` 包中的 `fread()` 函数读取 `Counties_in_Texas` 数据集。由于此函数已经来自 `data.table`，与其他数据不同，我们**不**需要将其转换为 `data.table` 格式；我们的数据已经具备正确的结构：

```
texasData <- fread(file = "data/ch04/Counties_in_Texas.csv",
header = TRUE,
skip = 0)
```

本章我们将使用另外两个数据集。通过拥有三种不同类型的数据，我们可以确保早期的探索能接触到足够多样的现实世界数据“风格”。用不同的数据对比和比较特定技术，有助于你进行实践，并学会如何将这些技术应用到你在“野外”遇到的*任何*数据集上。

与 `texasData` 不同，这两个数据集已经存在。我们只需确保它们是 `data.table` 格式即可。暂时不用过多思考 `data()` 函数，它所做的只是从 `JWileymisc` 包中加载信息到环境窗格中。接着，`as.data.table()` 函数接收一个输入（即我们的新数据），并使用赋值运算符 `<-` 将其存储为一个名为 `acesData` 的对象。该变量包含超过 `6,500` 条观测值（即数据行），分布在 `19` 个变量（即列）中。为了避免混淆对象，我们使用 `rm()` 函数来*移除* `aces_daily`：

```
data(aces_daily)
acesData <- as.data.table(aces_daily)
rm(aces_daily)
```

对于最后一个数据集，我们使用 `palmerpenguins` [11] 数据集。不过，我们需要再次使用 `as.data.table()` 函数将其转换为 `data.table` 格式：

```
penguinsData <- as.data.table(penguins)
```

设置好这三个数据对象后，你就可以开始练习*处理数据*了。

## 4.2 我们的数据长什么样？

在动手使用数据之前，先了解我们所看到的内容会很有帮助。在实际数据应用中，研究者通常对自己常用的数据集非常熟悉。我们的目标是让这三个数据集成为本书后续内容的常用数据集。现在花时间探索这些数据，将来会大有裨益。

如第 3 章所述，你可以通过 RStudio 右上角的“环境”窗格查看数据。每个数据集的左侧都有一个带箭头的圆点，右侧则是一个方形表格网格。点击左侧的带箭头圆点，可以查看每个数据集中包含的数据类型。除了显示*列名*之外，它还会显示数据类型（例如，**整**数、**日期**或**数**值）。另一个选项是点击最右侧的方形表格网格，这将在新标签页中打开数据集以供查看。关闭该标签页即可返回代码区域。

然而，在 `R` 中，传统上我们使用*函数*来完成任务。`colnames()` 函数接收数据作为输入，并输出列名。列名的顺序与它们在数据集中自然出现的顺序一致：

```
colnames(acesData)
##  [1] "UserID"             "SurveyDay"     "SurveyInteger"
##  [4] "SurveyStartTimec11" "Female"        "Age"
##  [7] "BornAUS"            "SES_1"         "EDU"
## [10] "SOLs"               "WASONs"        "STRESS"
## [13] "SUPPORT"            "PosAff"        "NegAff"
## [16] "COPEPrb"            "COPEPrc"       "COPEExp"
## [19] "COPEDis"
```

知道了列名之后，我们就可以使用它们了。注意名为 `EDU` 的列，其中没有关于*具体学位类型*的信息。为了了解 `EDU` 列中存储的内容，我们使用 `unique()` 函数。回想一下我们的第一个脚本以及用于访问特定数据列的 `$` 运算符。`unique()` 函数允许你查看每个数据副本，且*每个值只出现一次*：

```
unique(acesData$EDU)
## [1]  0 NA  1
```

从前面的代码输出中，我们发现 `EDU` 列是一个*独热*编码（有时称为*虚拟*编码）的例子。如果条目是 `1`，则该行数据由至少拥有高等教育学位的参与者填写。另一方面，`0` 表示参与者（还）没有获得高等教育学位。还有一种情况是参与者未作任何回答，这就是空值或 `NA`（不可用）选项。

当然，我们在数据集中还看到了其他列。探索 `SurveyDay`，我们看到了 `Date` 类型数据的一个例子。在 `R` 中，此类 `Date` 数据的常见格式是表示年、月、日的 **YYYY-MM-DD**。这种日期格式几乎是全球通用的，但在美国，另一种不同的日期格式更为常见。不过，处理日期对象时一定要小心。理解数据最初是在哪里编码的，以及如果附加了时间数据，则要考虑时区，通常至关重要：

```
unique(acesData$SurveyDay)
##  [1] "2017-02-24" "2017-02-25" "2017-02-26" "2017-02-27" "2017-02-28"
##  [6] "2017-03-01" "2017-03-02" "2017-03-03" "2017-03-04" "2017-03-05"
## [11] "2017-03-06" "2017-03-07" "2017-02-22" "2017-02-23" "2017-03-08"
## [16] "2017-03-09" "2017-03-10" "2017-03-11" "2017-03-12" "2017-03-13"
## [21] "2017-03-14"
```

虽然前两个函数让我们能轻松查看所有列名并探索特定列的数据，但查看数据集的前几行也很有帮助。`head()` 函数正是为此而生，默认显示前六行。`acesData` 有 19 列，因此在控制台输出时会自动换行。可以看到其中可能包含相当多的 `NA` 数据。课堂上的例子通常不会展示如何处理这类挑战。因此，你现在就已经在探索现实生活中的数据可能有多杂乱：

```
head(acesData)
##    UserID  SurveyDay SurveyInteger SurveyStartTimec11 Female Age
## 1:      1 2017-02-24             2          1.927e-01      0  21
## 2:      1 2017-02-24             3          4.859e-01      0  21
## 3:      1 2017-02-25             1          1.157e-05      0  21
## 4:      1 2017-02-25             2          1.931e-01      0  21
## 5:      1 2017-02-25             3          4.062e-01      0  21
## 6:      1 2017-02-26             1          1.638e-02      0  21
##    BornAUS SES_1 EDU  SOLs WASONs STRESS SUPPORT PosAff NegAff
## 1:       0     5   0    NA     NA      5      NA  1.519  1.669
## 2:       0     5   0 0.000      0      1   7.022  1.505  1.000
## 3:       0     5   0    NA     NA      1      NA  1.564     NA
## 4:       0     5   0    NA     NA      2      NA  1.563  1.357
## 5:       0     5   0 6.925      0      0   6.154  1.127  1.000
## 6:       0     5   0    NA     NA      0      NA  1.338  1.661
##    COPEPrb COPEPrc COPEExp COPEDis
## 1:      NA      NA      NA      NA
## 2:   2.257   2.378   2.414   2.181
## 3:      NA      NA      NA      NA
## 4:      NA      NA      NA      NA
## 5:      NA      NA   2.034      NA
## 6:      NA      NA      NA      NA
```

请试着对 `penguinsData` 和 `texasData` 数据集也应用这三个函数。你对正在操作的数据越熟悉，后续章节的例子就越容易理解。

现在，我们介绍最后一个与 `head()` 相对应的函数。`tail()` 函数显示数据的*最后*六行：

```
tail(penguinsData)
##      species island bill_length_mm bill_depth_mm flipper_length_mm
## 1: Chinstrap  Dream           45.7          17.0               195
## 2: Chinstrap  Dream           55.8          19.8               207
## 3: Chinstrap  Dream           43.5          18.1               202
## 4: Chinstrap  Dream           49.6          18.2               193
## 5: Chinstrap  Dream           50.8          19.0               210
## 6: Chinstrap  Dream           50.2          18.7               198
##    body_mass_g    sex year
## 1:        3650 female 2009
## 2:        4000   male 2009
## 3:        3400 female 2009
## 4:        3775   male 2009
## 5:        4100   male 2009
## 6:        3775 female 2009
```

## 4.3 `data.table` 是如何工作的？

`data.table` 结构包含三个不同的区域。第一个区域作用于*行*，第二个作用于*列*，最后一个则用于*分组*。类似于数学矩阵，前两个区域（行和列）通常被称为 `i` 和 `j`。最后一个区域，因为是**按**分组进行操作，常被称为 `by`。因此，通用的 **d**`ata.` **t**`able` 通常表示为 `DT[i, j, by]`。注意，与通常的函数圆括号不同，这里使用的是方括号 `[]`。为了理解如何以这种方式使用数据，我们将一起探索数据表格的每个区域。在深入学习数据的过程中，请记住，理解原始数据的样貌有助于理解这些新技术所展示的内容。

### 行操作如何工作？

`DT[i, j, by]` 中的第一个区域是针对行元素的操作。这包括对行进行排序（可以理解为字母顺序或数值顺序）以及根据特定特征选择行（可以理解为子集选择）。

## `order()`

`order()` 函数可用于按顺序组织数据。该函数会重新排列每一`行`，使各行按顺序排列。然而，它必须知道要根据哪一列来进行排序。因此，尽管它是一个`行`操作，`order()` 函数却接受一个`列`参数。回想一下，在 `penguinsData` 中，`flipper_length_mm` 列的最后六行显示了一些不同的值。根据你对 `penguinsData` 的目视检查，你注意到 `flipper_length_mm` 的范围是 172 到 231，但它们并不是按顺序排列的。我们使用第一个`行`操作，也就是 `DT[i, j, by]` 结构的第 `i` 个位置，来对 `flipper_length_mm` 进行 `order()` 排序，从小到大。我们称之为`升序`排序：

```
penguinsData[order(flipper_length_mm)]
##      species    island bill_length_mm bill_depth_mm flipper_length_mm
##   1:  Adelie    Biscoe           37.9          18.6               172
##   2:  Adelie    Biscoe           37.8          18.3               174
##   3:  Adelie Torgersen           40.2          17.0               176
##   4:  Adelie     Dream           39.5          16.7               178
##   5:  Adelie     Dream           37.2          18.1               178
## ---
## 340:  Gentoo    Biscoe           51.5          16.3               230
## 341:  Gentoo    Biscoe           55.1          16.0               230
## 342:  Gentoo    Biscoe           54.3          15.7               231
## 343:  Adelie Torgersen             NA            NA                NA
## 344:  Gentoo    Biscoe             NA            NA                NA
##      body_mass_g    sex year
##   1:        3150 female 2007
##   2:        3400 female 2007
##   3:        3450 female 2009
##   4:        3250 female 2007
##   5:        3900   male 2007
## ---
## 340:        5500   male 2009
## 341:        5850   male 2009
## 342:        5650   male 2008
## 343:          NA    2007
## 344:          NA    2009
```

注意，我们尚未对数据使用赋值运算符 `<-`。上述代码并`没有` `永久`改变 `penguinsData` 的顺序。请注意，如下所示的 `flipper_length_mm` 并`不是`按顺序排列的：

```
head(penguinsData)
##    species    island bill_length_mm bill_depth_mm flipper_length_mm
## 1:  Adelie Torgersen           39.1          18.7               181
## 2:  Adelie Torgersen           39.5          17.4               186
## 3:  Adelie Torgersen           40.3          18.0               195
## 4:  Adelie Torgersen             NA            NA                NA
## 5:  Adelie Torgersen           36.7          19.3               193
## 6:  Adelie Torgersen           39.3          20.6               190
##    body_mass_g    sex year
## 1:        3750   male 2007
## 2:        3800 female 2007
## 3:        3250 female 2007
## 4:         NA     2007
## 5:        3450 female 2007
## 6:        3650   male 2007
```

如果我们的工作需要`永久`改变数据，那么我们必须将那临时改变永久地赋值回去。在前面的代码（`penguinsData[order(flipper_length_mm)]`）中，我们在控制台上看到了输出，然而并没有发生永久性的改变。相比之下，在下面的代码中，控制台上没有输出。尽管如此，我们的数据现在已根据鳍状肢长度按升序排列：

```
penguinsData <- penguinsData[order(flipper_length_mm)]
```

如果需要，我们可以通过直接调用 `penguinsData` 来确认这个改变。如你所见，这个改变现在确实是永久性的：

```
penguinsData
##      species    island bill_length_mm bill_depth_mm flipper_length_mm
##   1:  Adelie    Biscoe           37.9          18.6               172
##   2:  Adelie    Biscoe           37.8          18.3               174
##   3:  Adelie Torgersen           40.2          17.0               176
##   4:  Adelie     Dream           39.5          16.7               178
##   5:  Adelie     Dream           37.2          18.1               178
## ---
## 340:  Gentoo    Biscoe           51.5          16.3               230
## 341:  Gentoo    Biscoe           55.1          16.0               230
## 342:  Gentoo    Biscoe           54.3          15.7               231
## 343:  Adelie  Torgersen            NA            NA                NA
## 344:  Gentoo    Biscoe             NA            NA                NA
##      body_mass_g    sex year
##   1:        3150 female 2007
##   2:        3400 female 2007
##   3:        3450 female 2009
##   4:        3250 female 2007
##   5:        3900   male 2007
## ---
## 340:        5500   male 2009
## 341:        5850   male 2009
## 342:        5650   male 2008
## 343:          NA    2007
## 344:          NA    2009
```

这种控制是否保留改变的能力非常有用。它允许我们在不改变任何内容的情况下探索数据。虽然 `penguinsData` 非常短，只有 344 行数据，但我们的 `acesData` 有超过 `6,500` 行。数据量太大，无法逐一查看，但 `R` 可以愉快地对任意多行的数据进行分析。

`order()` 的常见用途包括按数字顺序或字母顺序进行组织。如果在列名前面加上一个负号（`–`），那么就会得到`降序`排列。在这个例子中，我们使用 `order()` 按字母降序对 `penguinsData` 进行排序。注意，我们没有将数据赋值给一个变量，所以这是 `order` 函数临时使用的一个例子。另请注意，`flipper_length_mm` 现在`不按顺序`了。这是因为尽管我们正在按 `species` 进行字母排序，但 `R` 知道我们希望对每个观测值保持数据完整：

```
penguinsData[order(-species)]
##      species    island bill_length_mm bill_depth_mm flipper_length_mm
##   1:  Gentoo    Biscoe           48.4          14.4               203
##   2:  Gentoo    Biscoe           45.1          14.5               207
##   3:  Gentoo    Biscoe           44.0          13.6               208
##   4:  Gentoo    Biscoe           48.7          15.7               208
##   5:  Gentoo    Biscoe           42.7          13.7               208
## ---
## 340:  Adelie    Biscoe           41.0          20.0               203
## 341:  Adelie     Dream           41.1          18.1               205
## 342:  Adelie     Dream           40.8          18.9               208
## 343:  Adelie Torgersen           44.1          18.0               210
## 344:  Adelie Torgersen             NA            NA                NA
##      body_mass_g    sex year
##   1:        4625 female 2009
##   2:        5050 female 2007
##   3:        4350 female 2008
##   4:        5350   male 2008
##   5:        3950 female 2008
## ---
## 340:        4725   male 2009
## 341:        4300   male 2008
## 342:        4300   male 2008
## 343:        4000   male 2009
## 344:          NA    2007
```

一次可以将多个列放入 `order()` 函数中。在这种情况下，第一列先被排序，然后在`尽可能不破坏第一列顺序的情况下`对第二列进行排序。虽然这里我们只展示了两列，但在实践中，这可以扩展到任意所需数量的列。

```
penguinsData[order(-species, flipper_length_mm)]
##      species    island bill_length_mm bill_depth_mm flipper_length_mm
##   1:  Gentoo    Biscoe           48.4          14.4               203
##   2:  Gentoo    Biscoe           45.1          14.5               207
##   3:  Gentoo    Biscoe           44.0          13.6               208
##   4:  Gentoo    Biscoe           48.7          15.7               208
##   5:  Gentoo    Biscoe           42.7          13.7               208
## ---
## 340:  Adelie    Biscoe           41.0          20.0               203
## 341:  Adelie     Dream           41.1          18.1               205
## 342:  Adelie     Dream           40.8          18.9               208
## 343:  Adelie Torgersen           44.1          18.0               210
## 344:  Adelie Torgersen             NA            NA                NA
##      body_mass_g    sex year
##   1:        4625 female 2009
##   2:        5050 female 2007
##   3:        4350 female 2008
##   4:        5350   male 2008
##   5:        3950 female 2008
## ---
## 340:        4725   male 2009
## 341:        4300   male 2008
## 342:        4300   male 2008
## 343:        4000   male 2009
## 344:          NA    2007
```

这种多列排序适用的例子，可能包括对姓氏、名字和中间名进行排序。在我们其中一位作者使用的一些数据中，通常按学期、课程科目、课程编号，然后按学生标识符进行排序。

### 子集选择

从数据集中选择或`子集化`某些行也是一项很有用的技能，这属于行操作。因此，我们使用 `DT[i, j, by]` 的第一个`行`操作或第 `i` 个位置来进行子集选择。子集是通过使用`逻辑`运算符（有时称为布尔运算符）来实现的。首先写出 `TRUE` 的条件，然后，计算结果为 `TRUE` 的行将被选中。为了设定这些条件，我们使用基础 `R` 和我们的 `extraoperators` 包中的运算符。作为参考，在我们给出一些带代码的实际示例之前，我们先在表 4-1 中展示常用的运算符和函数。

#### 注意：区间表示法

区间表示法是一种紧凑的方式，可以非常精确地定义你想要的数字区间。例如，假设你有一组数字：1, 2, 3, 4, 5。用文字描述，我们可以写成“大于 3 且小于 5 的数字”，那么在我们这个小集合中，就会得到数字 4。我们也可以说“至少为 3 且小于 5 的数字”，那么在我们的数字集合中，就会得到数字 3 和 4。用文字描述这些的方式相对较长，因此人们经常使用区间表示法。区间表示法使用圆括号“（”和“）”，以及方括号“[”和“]”。圆括号表示大于“（”或小于“）”。方括号表示大于或等于“[”或小于或等于“]”。例如，描述“大于 3 且小于 5 的数字”的区间表示法是 `(3, 5)`。描述“大于或等于 3 且小于 5 的数字”的区间表示法是 `[3, 5)`。表 4-1 中的许多逻辑运算符都用于选择特定区间。

## 表 4-1：逻辑函数

| 函数 | 作用 |
| :--- | :--- |
| `==` | 值/向量是否相等？ |
| `!=` | 值/向量是否不相等？ |
| `%like%` | 值是否类似于引号内的字符？ |
| `<` 或 `%l%` | 小于。 |
| `<=` 或 `%le%` | 小于或等于。 |
| `>` 或 `%g%` | 大于。 |
| `>=` 或 `%ge%` | 大于或等于。 |
| `%gl%` | 大于且小于，`(x, y)`。 |
| `%gel%` | 大于或等于且小于，`[x, y)`。 |
| `%gle%` | 大于且小于或等于，`(x, y]`。 |
| `%gele%` | 大于或等于且小于或等于，`[x, y]`。 |
| `%in%` | 属于。 |
| `%nin%` | 不属于。 |
| `%c%` | 对右侧的操作进行链式调用。 |
| `%e%` | 集合运算符，用于使用集合表示法。 |
| `is.na()` | 选择缺失数据的函数。 |

相等是一个相当常见的逻辑运算，这里我们尝试在 `texasData` 上应用它。与 `order()` 非常相似，即使这是一个*行*操作，我们也必须在特定的*列*内部执行它。因此，使用 `CountyName` 列，我们*只*选择那些与 `Victoria` `完全匹配`的行。`Victoria` 恰好是德克萨斯州的一个县，是我们其中一人的工作院校所在地。再次请注意，由于我们没有使用 `<-` 运算符将此结果赋值给任何变量，我们目前只是在探索数据。数据集中的观测数量没有变化（可以在右上角的“环境”窗格中确认）：

```
texasData[CountyName == "Victoria"]
##    CountyName FIPSNumber CountyNumber PublicHealthRegion
## 1:   Victoria        469          235                  8
##    HealthServiceRegion MetropolitanStatisticalArea_MSA
## 1:                   8                        Victoria
##    MetropolitanDivisions MetroArea NCHSUrbanRuralClassification_2006
## 1:                    --     Metro                       Small Metro
##    NCHSUrbanRuralClassification_2013
## 1:                       Small Metro
```

有时候，我们想要的不是精确*匹配*，而是**不**匹配。这称为“不等于”；在 `R` 中，**不**是用感叹号“!”编码的。在这种情况下，回到我们的 `penguinsData`，我们只选择**不是** `Adelie` 物种的数据：

```
penguinsData[species != "Adelie"]
##        species island bill_length_mm bill_depth_mm flipper_length_mm
##   1: Chinstrap  Dream           46.1          18.2               178
##   2: Chinstrap  Dream           58.0          17.8               181
##   3: Chinstrap  Dream           42.4          17.3               181
##   4: Chinstrap  Dream           47.0          17.3               185
##   5: Chinstrap  Dream           43.2          16.6               187
## ---
## 188:    Gentoo Biscoe           52.1          17.0               230
## 189:    Gentoo Biscoe           51.5          16.3               230
## 190:    Gentoo Biscoe           55.1          16.0               230
## 191:    Gentoo Biscoe           54.3          15.7               231
## 192:    Gentoo Biscoe             NA            NA                NA
##      body_mass_g    sex year
##   1:        3250 female 2007
##   2:        3700 female 2007
##   3:        3600 female 2007
##   4:        3700 female 2007
##   5:        2900 female 2007
## ---
## 188:        5550   male 2009
## 189:        5500   male 2009
## 190:        5850   male 2009
## 191:        5650   male 2008
## 192:          NA    2009
```

在讨论其他行选择运算符之前，有两种方法可以*组合*逻辑值。一种是*且*，用 `&` 编码；另一种是*或*，用 `|` 编码。以下代码展示了两个不同的例子，其中相同的独立逻辑值（例如，`species == "Gentoo"` 加上 `flipper_length_mm == 230`）首先通过*且*连接，然后通过*或*连接。结果的差异很具有启发性。同时请注意，字符值使用引号 `""`，而数值则不需要：

```
penguinsData[species == "Gentoo" &
flipper_length_mm == 230]
##    species island bill_length_mm bill_depth_mm flipper_length_mm
## 1:  Gentoo Biscoe           50.0          16.3               230
## 2:  Gentoo Biscoe           59.6          17.0               230
## 3:  Gentoo Biscoe           49.8          16.8               230
## 4:  Gentoo Biscoe           48.6          16.0               230
## 5:  Gentoo Biscoe           52.1          17.0               230
## 6:  Gentoo Biscoe           51.5          16.3               230
## 7:  Gentoo Biscoe           55.1          16.0               230
##    body_mass_g  sex year
## 1:        5700 male 2007
## 2:        6050 male 2007
## 3:        5700 male 2008
## 4:        5800 male 2008
## 5:        5550 male 2009
## 6:        5500 male 2009
## 7:        5850 male 2009
penguinsData[species == "Gentoo" |
flipper_length_mm == 230]
##      species island bill_length_mm bill_depth_mm flipper_length_mm
##   1:  Gentoo Biscoe           48.4          14.4               203
##   2:  Gentoo Biscoe           45.1          14.5               207
##   3:  Gentoo Biscoe           44.0          13.6               208
##   4:  Gentoo Biscoe           48.7          15.7               208
##   5:  Gentoo Biscoe           42.7          13.7               208
## ---
## 120:  Gentoo Biscoe           52.1          17.0               230
## 121:  Gentoo Biscoe           51.5          16.3               230
## 122:  Gentoo Biscoe           55.1          16.0               230
## 123:  Gentoo Biscoe           54.3          15.7               231
## 124:  Gentoo Biscoe             NA            NA                NA
##      body_mass_g    sex year
##   1:        4625 female 2009
##   2:        5050 female 2007
##   3:        4350 female 2008
##   4:        5350   male 2008
##   5:        3950 female 2008
## ---
## 120:        5550   male 2009
## 121:        5500   male 2009
## 122:        5850   male 2009
## 123:        5650   male 2008
## 124:          NA    2009
```

掌握了这些数据，我们再来探索表 4-1 中的更多运算符。能够将多个逻辑运算符链式调用是非常有用的。在本例中，`%like%` 运算符选择 `UserID` 在任何位置包含 `19` 的所有行。这是一个所谓的*模糊*匹配的例子。它与其他运算符更精确的性质形成对比。接下来，我们要求 `STRESS` *大于或等于 4 且严格小于 6*。消极情绪 `NegAff` 必须*严格小于 2*。从表 4-1 中注意，这同样可以写成 `NegAff < 2`。通常，在 `R` 中，就像在生活中一样，解决问题的方法不止一种。最后，要求 `SurveyInteger` 精确等于 `2`：

```
acesData[UserID %like% "19" &
STRESS  %gel%  c(4, 6) &
NegAff %l% 2 &
SurveyInteger == 2]
##     UserID  SurveyDay SurveyInteger SurveyStartTimec11 Female Age
##  1:     19 2017-03-07             2             0.2080      1  24
##  2:    119 2017-02-25             2             0.2367      1  25
##  3:    190 2017-03-01             2             0.2042      0  23
##  4:    190 2017-03-05             2             0.2152      0  23
##  5:    190 2017-03-07             2             0.2224      0  23
##  6:    190 2017-03-09             2             0.2577      0  23
##  7:    190 2017-03-12             2             0.1990      0  23
##  8:    191 2017-02-25             2             0.1937      1  21
##  9:    191 2017-03-01             2             0.1875      1  21
## 10:    191 2017-03-02             2             0.1875      1  21
##     BornAUS SES_1 EDU SOLs WASONs STRESS SUPPORT PosAff NegAff
##  1:       1     7   1   NA     NA      5      NA  3.972  1.267
##  2:       1     8   1   NA     NA      4      NA  2.021  1.270
##  3:       0     5   1   NA     NA      5      NA  1.332  1.042
##  4:       0     5   1   NA     NA      5      NA  2.492  1.579
##  5:       0     5   1   NA     NA      4      NA  1.822  1.000
##  6:       0     5   1   NA     NA      5      NA  1.882  1.528
##  7:       0     5   1   NA     NA      5      NA  1.047  1.812
##  8:       0     6   0   NA     NA      5      NA  3.403  1.000
##  9:       0     6   0   NA     NA      4      NA  1.032  1.682
## 10:       0     6   0   NA     NA      4      NA  1.047  1.619
##     COPEPrb COPEPrc COPEExp COPEDis
##  1:      NA      NA      NA      NA
##  2:      NA      NA      NA      NA
##  3:      NA      NA      NA      NA
##  4:      NA      NA      NA      NA
##  5:      NA      NA      NA      NA
##  6:      NA      NA      NA      NA
##  7:      NA      NA      NA      NA
##  8:      NA      NA      NA      NA
##  9:      NA      NA      NA      NA
## 10:      NA      NA      NA      NA
```

请注意，在最近的这个例子中，有几个列包含 `NA`。这些 `NA` 代表*缺失数据*。由于 `NA` 有时具有复杂性，我们必须谨慎对待如何查找缺失数据。基于我们目前所学的一切，假设我们想检查是否有参与者**没有**报告他们的 `EDU` 状态。换句话说，在 `acesData` 中，是否有调查参与者选择不回应教育水平？根据我们目前学到的知识，下面的两行代码之一可能看起来是可行的。毕竟，我们之前对 `CountyName == "Victoria"` 和 `SurveyInteger == 2` 使用了类似的技术：

```
acesData[EDU == "NA"]
## Empty data.table (0 rows and 19 cols): UserID,SurveyDay,SurveyInteger,SurveyStartTimec11,Female,Age...
acesData[EDU == NA]
## Empty data.table (0 rows and 19 cols): UserID,SurveyDay,SurveyInteger,SurveyStartTimec11,Female,Age...
```

不要被迷惑！尽管前面的代码行似乎表明没有结果，但实际上有很多观测值缺少教育状态！缺失数据很复杂。现在，我们不会在本文中深入探讨其原因——这是另一个话题。你需要记住的是，缺失数据很棘手，并且 `R` 有一个特殊函数来处理它们。要使用的函数是 `is.na()` 函数。由于它在特定列的每一行中检查缺失数据，所以它位于我们预期的位置，即 `data.frame` 的行部分。如你所见，远非没有缺失数据的行，我们有超过 200 行缺少教育数据！

```
acesData[is.na(EDU)]
##      UserID  SurveyDay SurveyInteger SurveyStartTimec11 Female Age
##   1:      2 2017-02-22             3                 NA     NA  NA
##   2:      2 2017-02-25             3                 NA     NA  NA
##   3:      3 2017-02-23             3                 NA     NA  NA
##   4:      3 2017-03-05             3                 NA     NA  NA
##   5:      4 2017-03-01             3                 NA     NA  NA
#  ---
## 216:    183 2017-02-23             3                 NA     NA  NA
## 217:    184 2017-03-07             3                 NA     NA  NA
## 218:    189 2017-02-22             3                 NA     NA  NA
## 219:    190 2017-02-28             3                 NA     NA  NA
## 220:    190 2017-03-04             3                 NA     NA  NA
##      BornAUS SES_1 EDU   SOLs WASONs STRESS SUPPORT PosAff NegAff
##   1:      NA    NA  NA 23.476      2     NA      NA     NA     NA
##   2:      NA    NA  NA 34.755      0     NA      NA     NA     NA
##   3:      NA    NA  NA  0.000      0     NA      NA     NA     NA
##   4:      NA    NA  NA  0.000      0     NA      NA     NA     NA
##   5:      NA    NA  NA  6.190      1     NA      NA     NA     NA
## ---
## 216:      NA    NA  NA 19.600      1     NA      NA     NA     NA
## 217:      NA    NA  NA 96.670      3     NA      NA     NA     NA
## 218:      NA    NA  NA  9.266      1     NA      NA     NA     NA
## 219:      NA    NA  NA  0.000      1     NA      NA     NA     NA
## 220:      NA    NA  NA  6.870      1     NA      NA     NA     NA
##      COPEPrb COPEPrc COPEExp COPEDis
##   1:      NA      NA      NA      NA
##   2:      NA      NA      NA      NA
##   3:      NA      NA      NA      NA
##   4:      NA      NA      NA      NA
##   5:      NA      NA      NA      NA
##  ---
## 216:      NA      NA      NA      NA
## 217:      NA      NA      NA      NA
## 218:      NA      NA      NA      NA
## 219:      NA      NA      NA      NA
## 220:      NA      NA      NA      NA
```

虽然我们为了清晰起见将其单独讨论，但就像之前的所有逻辑运算一样，我们可以通过使用 `&` 和 `|` 将 `is.na()` 链式调用来创建子集。

花点时间回顾一下你刚学到的内容，确保你理解了用于创建复杂逻辑运算以选择所需数据的构建块。在练习中，你将有机会扩展和修改前面的每个示例，以更深入地了解它们的工作原理。练习结束后，我们将继续学习 `data.table` 的下一部分。在下一节中，我们将在学习更多关于 `data.table` 知识的同时，继续了解更多的逻辑运算。

### 列操作如何工作？

在 `DT[i, j, by]` 中，第二个区域用于对列进行操作。这包括仅选择某些列以及创建新列。

#### 选择列

你可能已经注意到我们的 `acesData` 和 `texasData` 包含多个列。如果我们只对其中几列感兴趣该怎么办？我们可以通过在 `j` 位置使用列操作来选择我们想要的列。只选择一列非常简单。只需在 `j` 位置输入列名即可。为此，我们需要让 `R` 知道我们要跳过第一个或 `i` 位置。要跳过，只需使用一个逗号。在这里，我们只查看 `EDU` 列中的数据。我们看到 `0` 表示未获得高等教育学位，`1` 表示已获得高等教育学位，而 `NA` 表示缺失数据。请注意我们代码中的逗号！

```
acesData[, EDU]
##    [1]  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
##   [22]  0  0  0  0  0  0  0  0  0 NA  0  0  0  0  0  0 NA  0  0  0  0
...
##  [988]  1  1  1  1  1  1  1  1  1  1  1  1  1  1  1  1  1  1  1  1  1  1  1
##  [ 达到 getOption("max.print") -- 省略了 5599 个条目 ]
```

注意，当选择单个列时，无论是使用方括号还是美元符号，结果都是相同的。为了避免显示过多文本，我们将只使用 `unique()` 显示唯一值：

```
unique(acesData$EDU)
## [1]  0 NA  1
unique(acesData[, EDU])
## [1]  0 NA  1
```

当然，我们通常需要不止一列。要选择多列，请使用 `.()` 列出所需列。再次记住，列操作属于第二个位置，因此需要前导逗号来表示“空”的第一个位置：

```
acesData[, .(UserID, STRESS, NegAff, SurveyInteger, EDU, SES_1)]
##       UserID STRESS NegAff SurveyInteger EDU SES_1
##    1:      1      5  1.669             2   0     5
...
## 6599:    191      0  1.171             1   0     6
```

我们一直将第一个位置留空，但这并不意味着它必须始终为空。回顾我们关于子集的工作，回想一下我们相当复杂的逻辑操作集合。首先对 `UserID`、`STRESS`、`NegAff` 和 `SurveyInteger` 进行逻辑运算。然后，通过使用逗号将这些逻辑子集与我们的列选择按行组合。注意，我们在以下代码中使用 `#` 添加了一个注释。代码注释告诉 `R` 忽略 `#` 之后的文本。同时，注释帮助我们阅读代码，并确切地看到我们在哪里将行子集与列选择结合起来：

```
acesData[UserID %like% "19" &
  STRESS %gel% c(4, 6) &
  NegAff %l% 2 &
  SurveyInteger == 2, #注意这里的逗号！
  .(UserID, STRESS, NegAff, SurveyInteger, EDU, SES_1)]
##       UserID STRESS NegAff SurveyInteger EDU SES_1
##  1:       19      5  1.267             2   1     7
...
## 10:      191      4  1.619             2   0     6
```

花点时间研究一下前面的代码。你已经在学习使用相当复杂的技术了！通过行子集和列选择，使用数据表的这两个 `i` 和 `j` 位置：`DT[i, j, by]` 可以实现很多事情。

### 创建列

请注意，在上一个示例中有十个观测值。这十个观测值似乎只有四名不同的参与者（基于`UserID`）。有时，进行计数会有所帮助。当然，这已经是一个足够受限的子集，我们可以通过检查来完成。然而，这并不总是可行的。创建一个统计观测值或行数的操作就是创建新列。请记住，在右上角的“环境”窗格中，行是观测值，列是变量。因此，统计有多少行就是创建一个新变量。因此，这是一个列操作。要获取计数，我们使用`.N`。在统计学中，*N*通常代表总体数量。用它来统计整个数据集是合理的。由于这是一个列操作，我们必须使用列或第*j*个位置。记得使用逗号来跳过行操作空间！

```r
acesData[, .N]
## [1] 6599
```

自然地，这会给出`acesData`中每一行的计数。如果我们想自动统计子集中的十行，我们需要用逻辑操作填充行操作空间。我们使用熟悉的限制条件，将选择某些列替换为计数：

```r
acesData[UserID %like% "19" &
STRESS %gel% c(4, 6) &
NegAff %l% 2 &
SurveyInteger == 2, #注意逗号！
.N]
## [1] 10
```

如果我们不想统计总行数，而是想统计符合这些条件的唯一参与者有多少个，该怎么办？在这种情况下，我们需要使用`uniqueN()`函数。因为我们是在统计唯一参与者，并且每个参与者都有一个唯一的`UserID`，我们需要让我们的列计数函数知道要在哪一列进行检查。同样，将以下示例与前一个示例进行比较和对比。我们将`.N`替换为`uniqueN(UserID)`，这回答了另一个问题：

```r
acesData[UserID %like% "19" &
STRESS %gel% c(4, 6) &
NegAff %l% 2 &
SurveyInteger == 2, #注意逗号！
uniqueN(UserID)]
## [1] 4
```

这有道理吗？如果有，那太好了！如果没有，那也没关系。学习R语言很像学习其他任何语言。学会如何读写需要时间。如果你感觉有点卡住了，请再回顾一遍这些例子。尝试在这里或那里做一点修改，看看会发生什么。例如，在最新的这个例子中，当我们把`.N`换成`uniqueN(UserID)`时会发生什么？原始的子集看起来是怎样的？回去看看上一节，当我们查看所有十行数据时发生了什么。永远不要羞于寻求帮助。如果你在我们的课堂上，我们很乐意回答学生的问题。如果你在别人的课堂上，我们保证大多数讲师和教授都喜欢回答疑问（如果你觉得害羞，可以给他们看这句话）。

休息十秒钟给自己鼓鼓劲后，我们继续探讨更多列的创建。到目前为止，我们实际上还没有创建任何一个新列。我们做了一些计数，但并没有真正创建一个新列。是时候通过讨论重新编码来改变这一点了。有时，我们收集的原始数据并不完全是我们想要的。例如，在`acesData`中，`STRESS`量表的范围是 0 到 10。如果我们想将参与者分成低、中、高压力三组呢？这就是一个重新编码的例子。要做到这一点，需要创建一个新列，然后根据我们的`STRESS`量表为该列赋值。要构建新列，我们使用列赋值操作符`:=`。我们称新列为`STRESS_3`。使用我们的逻辑行操作，将它们分成三个范围：[0, 3]表示低，(3, 6]表示中，(6, 10]表示高。

在下面的代码中，请花点时间注意几个关键特征。首先，行选择操作`%gele%`代表“大于等于”和“小于等于”。它捕获了低压力组的[0, 3]闭区间。如有必要，请回顾表 4-1。接下来，注意逗号，特别是`STRESS_3`前面的逗号。它们将行操作区域与列操作区域分开。继续运行这段代码：

```r
acesData[STRESS %gele% c(0, 3),
STRESS_3 := "low"]
```

你运行了上面的代码，但似乎什么也没发生。像通常的赋值操作一样，控制台不会打印任何内容。然而，在幕后，事情确实发生了变化。首先，在“环境”窗格中，你应该看到右上角有一个循环箭头。点击那个箭头。点击后，`acesData`的 19 列将刷新，显示现在有 20 列。我们可以使用`View()`函数查看修改后的数据集，这将在 RStudio 中打开数据查看器：

```r
View(acesData) ## 无输出，打开 RStudio 数据查看器
```

如果我们查看变量`STRESS_3`，只有某些行有值，其余行缺失。为什么？我们创建了新的`STRESS_3`变量，但仅针对某些压力水平为其赋值了“low”。其他压力水平没有分配值，所以缺失。为了将剩余的压力分数编码为类别，我们可以运行以下代码：

```r
acesData[STRESS %gle% c(3, 6),
STRESS_3 := "medium"]
acesData[STRESS %gle% c(6, 10),
STRESS_3 := "high"]
```

请注意，我们选择了使用`%gle%`，而不是像之前在`STRESS_3`中创建“low”赋值时使用的`%gele%`。这很重要，因为我们需要小心边界值的编码方式。例如，如果我们把恰好等于 3 的值称为“low”，那么我们就不希望它们也被包含在“medium”下，否则“low”的值会被覆盖。总的来说，这会创建以下区间：[0, 3], (3, 6], (6, 10]。这正是我们想要的，它能覆盖 0 到 10 之间的所有值（即压力分数的可能范围），并且每种压力分数只对应一个标签：“low”、“medium”或“high”。我们可以使用`View()`函数查看修改后的数据集，这将在 RStudio 中打开数据查看器。现在我们应该看到每一行都有`STRESS_3`的值：

```r
View(acesData) ## 无输出，打开 RStudio 数据查看器
```

接下来，请看下面的代码。我们几乎完美地复用了之前运行过的一些代码。你能发现区别吗？仔细看，你会发现我们添加了新的列名`STRESS_3`。当你运行这段代码时，确认我们的参与者所有人都处于中等压力。这很合理，因为他们的数值压力分数是 4 和 5。到目前为止，一切顺利！

```r
acesData[UserID %like% "19" &
STRESS %gel% c(4, 6) &
NegAff %l% 2 &
SurveyInteger == 2, #注意逗号！
.(UserID, STRESS, STRESS_3, NegAff, SurveyInteger, EDU, SES_1)]
##     UserID STRESS STRESS_3 NegAff SurveyInteger EDU SES_1
##  1:     19      5   medium  1.267             2   1     7
##  2:    119      4   medium  1.270             2   1     8
##  3:    190      5   medium  1.042             2   1     5
##  4:    190      5   medium  1.579             2   1     5
##  5:    190      4   medium  1.000             2   1     5
##  6:    190      5   medium  1.528             2   1     5
##  7:    190      5   medium  1.812             2   1     5
##  8:    191      5   medium  1.000             2   0     6
##  9:    191      4   medium  1.682             2   0     6
## 10:    191      4   medium  1.619             2   0     6
```

拥有了选择特定行、选择特定列以及创建或计数列的能力，我们准备探索数据的最后一部分。

### `by` 操作是如何工作的？

`DT[i, j, by]` 中的第三个区域用于分组。我们对这个区域的介绍有些简略——它实际上能做的事情要多得多。不过，你的主要目标是学习统计方法，而不是 `R` 或 `data.table`。因此，暂时请你原谅我们未能面面俱到。

请记住，逗号用于分隔每个部分。假设我们想要统计每个压力等级有多少个数据？回想一下，我们使用 `.N` 来统计项目数量。我们使用的新代码位于最后、第三个位置，即 `by = STRESS_3`。在这个案例中，我们将按三个组进行计数——也就是为低、中、高压力分别创建组计数：

```r
acesData[, .N, by = STRESS_3]
##    STRESS_3    N
## 1:   medium 1240
## 2:      low 4625
## 3:     high  537
## 4:       NA  197
```

输出结果符合你的预期吗？你对 `<NA>` 感到意外吗？它是从哪里来的？记住 `STRESS_3` 是我们自己构建的一个重新编码或计算出的列。它基于 `STRESS` 列。请记住，我们想要理解为什么有些 `STRESS_3` 数据是缺失的。为了大规模地回答这个问题，我们必须用到全部三项技能。首先，我们只选取 `STRESS_3` 数据缺失的行。这是一个行操作，使用了 `is.na()`。其次，我们需要统计正在发生的情况。这是一个列操作，我们继续使用 `.N`。第三，我们再次使用 `by =`，只不过这次是在原始的 `STRESS` 列上：

```r
acesData[is.na(STRESS_3),
.N,
by = STRESS]
##    STRESS   N
## 1:     NA 197
```

正如你所见，结果是我们的原始 `STRESS` 数据存在一些缺失值。作为对比，看看当我们设置 `STRESS_3 == "low"` 时会发生什么。现在我们看到了预期的结果。也就是说，低压力测量值来自原始 `STRESS` 分数，其值大于或等于 0 且小于或等于 3：

```r
acesData[STRESS_3 == "low",
.N,
by = STRESS]
##    STRESS    N
## 1:      1 1075
## 2:      2  716
## 3:      0 2146
## 4:      3  688
```

现在你已经了解了操作和操作表格数据的三种方式。这些基础构件是你使用真实世界数据进行统计学习所需的全部工具。在你开始本章末尾的练习之前，我们展示了一些使用这些表格操作的示例。

## 4.4 示例

常言道，教与学之间是有区别的。此刻，从技术上讲，我们已经教给你所有必要的内容。但这并不意味着你已经完全理解了！我们甚至不确定它现在是否能被理解。看到不同于理解，而两者都需要实践和经验才能掌握。然而，请相信我们有信心你能做到。这只需要付出一些努力，而且这没关系。在我们让你去做练习来获得一些实践之前，我们一起探讨三个示例。我们不从技术入手，而是每个示例都以一个问题开始。从问题入手很有帮助，因为数据科学在现实世界中通常就是这样开始的。

### 示例 1：大都会区计数

考虑到 `texasData` 中的 254 个观测值，每种类型的大都会区各有多少个县？为了回答这个问题，我们需要思考可能会用到 `i/j/by` 三个区域中的哪一个。

对于 `i` 或行操作，没有必要按行子集化。`texasData` 中的每一行数据都恰好代表德克萨斯州的一个县。对数据施加 `order()` 可能*有*价值。特别是，如果我们要讨论 `MetroAreas` 的类型，我们可能希望按该值排序，而不是按 `CountyName` 的默认字母顺序。

对于 `j` 或列操作，我们必须对县进行计数。这可以通过 `.N` 或可能 `uniqueN(CountyNumber)` 来实现。如果使用前者，我们假设数据中没有重复的行。在这种情况下，这是一个合理的假设。然而，在 `acesData` 中，注意每个参与者的 `UserID` 出现了多次。通常，如果存在某种标识列，使用 `uniqueN()` 会更安全。

对于 `by` 或分组操作，我们必须按 `MetroArea` 进行计数。

将这三部分结合起来，就得到了问题的解决方案。在这个案例中，我们没有为新创建的列命名。因此，它被自动命名为 `V1`。花点时间尝试这个解决方案，比如移除 `order(MetroArea)` 这一部分。会发生什么？

```r
texasData[order(MetroArea),
uniqueN(CountyNumber),
by = MetroArea]
##     MetroArea  V1
## 1:      Metro  82
## 2:  Non-Metro 172
```

如果我们在 `order()` 函数中作为行操作添加更多列，会发生什么？如果我们在 `by = .()` 分组操作中添加另一列，又会如何？请注意列表函数 `.()` 在第三个区域中与在第二个区域中一样有效，用于选择多列：

```r
texasData[order(MetroArea, NCHSUrbanRuralClassification_2013),
uniqueN(CountyNumber),
by = .(MetroArea, NCHSUrbanRuralClassification_2013)]
##    MetroArea NCHSUrbanRuralClassification_2013  V1
## 1:     Metro               Large Central Metro   6
## 2:     Metro                Large Fringe Metro  29
## 3:     Metro                      Medium Metro  25
## 4:     Metro                       Small Metro  22
## 5: Non-Metro                      Micropolitan  46
## 6: Non-Metro                          Non-core 126
```

### 示例 2：大都会统计区

思考示例 1 的结果后，我们可能开始对数据产生更多疑问。每个“小都会”地区通常包含多少个县？

首先，可能需要一些背景知识。大都会统计区通常由美国人口普查局设定。其目的是将主要受特定城市或城镇影响的区域组合在一起。对于“小都会”地区而言，这些是规模较小的城市，但仍然具有足够的影响力来成为当地社区的枢纽或交汇点。

对于 `i` 或行操作，现在需要按行子集化。在这种情况下，我们有一些选择。我们可以探索数据集并确认需要使用 `NCHSUrbanRuralClassification_2013 == "Small Metro"`，或者我们可以使用 `%like%` 运算符来只获取那些分类中包含单词 `"Small"` 的行。在这个案例中，两种方法都可以。通常，正确答案不止一个。

对于 `j` 或列操作，我们必须对县进行计数。这可以通过 `.N` 或可能 `uniqueN(CountyNumber)` 来实现。如果使用前者，我们假设数据中没有重复的行。在这种情况下，这是一个合理的假设。然而，在 `acesData` 中，注意每个参与者的 `UserID` 出现了多次。通常，如果存在某种标识列，使用 `uniqueN()` 会更安全。

对于 `by` 或分组操作，我们必须按 `MetropolitanStatisticalArea_MSA` 进行计数。

在我们的解决方案中，有 11 个 MSA，每个包含 1 到 3 个县。请记住这些都是“小”都会区：

```r
texasData[NCHSUrbanRuralClassification_2013 %like% "Small",
uniqueN(CountyNumber),
by = .(MetropolitanStatisticalArea_MSA)]
##    MetropolitanStatisticalArea_MSA V1
##  1:                  Wichita Falls  3
##  2:                      Texarkana  1
##  3:          College Station-Bryan  3
##  4:                        Abilene  3
##  5:                         Odessa  1
##  6:                       Victoria  2
##  7:                Sherman-Denison  1
##  8:                       Longview  3
##  9:                     San Angelo  2
## 10:                        Midland  2
## 11:                          Tyler  1
```

### 示例 3

尚未上大学和大学学历的参与者报告了多少次较高或非低压力（*压力 > 3*）？

考虑到 `i` 或行操作，我们需要按行取子集。我们必须要求 `STRESS > 3` 作为行操作。

考虑到 `j` 或列操作，我们必须对每一行进行计数，因为每一行都是一份独立的报告。这需要使用 `.N`。

考虑到 `by` 或分组操作，我们必须按 `EDU` 级别进行计数。

请注意，我们的解决方案并非按唯一参与者进行计数。相反，我们的解决方案是针对非低压力的具体实例：

```r
acesData[STRESS > 3,
.N,
by = .(EDU)]
##    EDU    N
## 1:   0 1150
## 2:   1  620
## 3:  NA    7
```

## 4.5 本章小结

本章探讨了 `DT[i, j, by]` 的三个区域。理解并使用这些区域对原始数据进行操作，是将统计方法成功应用于真实数据的关键。表 4-2 可作为本章所学关键概念的实用参考。

**表 4-2** 本章小结

| 概念 | 含义 |
| --- | --- |
| `<-` | 新数据/变量/项目赋值运算符。 |
| `colnames()` | 打印表格的所有列名。 |
| `unique()` | 仅打印唯一项目（例如，每个项目仅出现一次）。 |
| `head()` | 打印表格的前六行。 |
| `tail()` | 打印表格的后六行。 |
| `DT[i, j, by]` | `data.table` 的第一个/第 i 个/行操作区域。 |
| `==` | 值/向量是否相等？ |
| `!=` | 值/向量是否不相等？ |
| `%like%` | 值是否类似于引号内的字符？ |
| `<` 或 `%l%` | 小于。 |
| `<=` 或 `%le%` | 小于或等于。 |
| `>` 或 `%g%` | 大于。 |
| `>=` 或 `%ge%` | 大于或等于。 |
| `%gl%` | 大于且小于。 |
| `%gel%` | 大于或等于且小于。 |
| `%gele%` | 大于且小于或等于。 |
| `%gle%` | 大于或等于且小于或等于。 |
| `%in%` | 属于。 |
| `%nin%` | 不属于。 |
| `%c%` | 在右侧进行链式操作。 |
| `%e%` | 集合运算符，用于使用集合表示法。 |
| `is.na()` | 一种行操作函数，用于选择指定列中数据缺失的行。 |
| `&` | 逻辑与运算符，用于组合两个或多个上述行操作。 |
| `&#124;` | 逻辑或运算符，用于组合两个或多个上述行操作。 |
| `order()` | 一种行操作函数，用于对指定列的行进行排序。 |
| `DT[i, j, by]` | `data.table` 的第二个/第 j 个/列操作区域。 |
| `.()` | 用于选择多个列。 |
| `.N` | 行计数函数 – 统计所有行数。 |
| `uniqueN()` | 行计数函数 – 统计唯一行数。 |
| `:=` | 新列赋值运算符。 |
| `DT[i, j, by =]` | `data.table` 的第三个/按/分组操作区域。 |

## 4.6 精通练习

通过完成一些练习来检查进度并在实践中成长。理解性测试提出了一些批判性思维问题，最好通过书面或口头回答。统计学的艺术之一在于成功地将结果传达给利益相关者或受众。有时受众是高度技术性的，而其他时候则非常非技术性。练习则是对本章所探讨概念的更直接应用。

### 理解性测试

1.  为什么像 `order()` 这样的函数在排序时保持每一行的完整性很重要？你是否曾在使用电子表格软件时不小心只排序了单列？

2.  `data.table` 操作如何以及何时*永久*更改表格数据？比较和对比以下代码片段：`<-`、`order()`、`:=` 和 `uniqueN()`。这些操作中是否有*总是*更改表格？是否有*有时*更改表格？

### 练习

1.  哪段代码能给出正确结果？

   A. `acesData[STRESS == NA]`

   B. `acesData[is.na(STRESS)]`

2.  在*示例 2* 中，我们在解决方案中使用了 `NCHSUrbanRuralClassification_2013`。如果将其改为 `NCHSUrbanRuralClassification_2006` 会发生什么？

3.  在 `texasData` 中，是否存在 2006 年至 2013 年期间城市与农村分类不同的情况？换句话说，是否存在 `NCHSUrbanRuralClassification_2006` 与 `NCHSUrbanRuralClassification_2013` 不同的情况？提示：使用 `!=`。