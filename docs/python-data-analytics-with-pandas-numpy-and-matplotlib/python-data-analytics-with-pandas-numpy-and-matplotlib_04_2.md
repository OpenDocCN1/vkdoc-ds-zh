

# 5. pandas：读写数据

在上一章中，你熟悉了 `pandas` 库及其为数据分析提供的基本功能。你了解到 `DataFrame` 和 `Series` 是该库的核心。它们是执行所有数据操作、计算和分析的基础。

在本章中，你将看到 `pandas` 提供的所有用于读取存储在许多类型介质（如文件和数据库）中的数据的工具。同时，你还会看到如何直接将数据结构写入这些格式，而无需过多担心所使用的技术。

本章重点介绍 `pandas` 提供的一系列 `I/O API` 函数，用于直接将数据作为 `DataFrame` 对象进行读写。首先从文本文件开始，然后逐渐过渡到更复杂的二进制格式。

在本章末尾，你还将学习如何与所有常见的数据库（包括 SQL 和 NoSQL）进行交互，其中包括展示如何将数据存储到 `DataFrame` 的示例。同时，你将学习如何读取数据库中包含的数据，并将其作为 `DataFrame` 检索出来。

## I/O API 工具

`pandas` 是一个专为数据分析而设计的库，因此你可以预计它主要专注于计算和数据处理。从外部文件写入和读取数据的过程可以被视为数据处理的一部分。事实上，你将看到即使在这个阶段，你也可以执行一些操作来准备传入的数据，以便后续处理。

因此，这一步对数据分析非常重要，因此 `pandas` 库中必须存在一个用于此目的特定工具——一组称为 `I/O API` 的函数。这些函数分为两大类别：*读取器* 和 *写入器*。

| 读取器 (Readers) | 写入器 (Writers) |
| --- | --- |
| `read_csv` | `to_csv` |
| `read_excel` | `to_excel` |
| `read_hdf` | `to_hdf` |
| `read_sql` | `to_sql` |
| `read_json` | `to_json` |
| `read_html` | `to_html` |
| `read_stata` | `to_stata` |
| `read_clipboard` | `to_clipboard` |
| `read_pickle` | `to_pickle` |
| `read_msgpack` | `to_msgpack` (实验阶段) |
| `read_gbq` | `to_gbq` (实验阶段) |

## CSV 和文本文件

多年来，每个人都习惯了以文本形式写入和读取文件。特别是，数据通常以表格形式报告。如果一行中的值用逗号分隔，就得到了 `CSV`（逗号分隔值）格式，这或许是最著名和最流行的格式。

其他形式的表格数据可以用空格或制表符分隔，通常包含在各种类型的文本文件（通常带有 `.txt` 扩展名）中。

这类文件是最常见的数据来源，并且更容易转录和解释。对此，`pandas` 提供了一组专门用于此类文件的函数。

* `read_csv`
* `read_table`
* `to_csv`

### 读取 CSV 或文本文件中的数据

根据经验，接触数据分析的人最常进行的操作就是读取 CSV 文件（或至少是文本文件）中包含的数据。

但在开始处理文件之前，您需要先导入以下库。

```python
>>> import numpy as np
>>> import pandas as pd
```

为了了解 pandas 如何处理这类数据，首先在工作目录中创建一个小的 CSV 文件，如代码清单 5-1 所示，并将其保存为 `ch05_01.csv`。

```
white,red,blue,green,animal
1,5,2,3,cat
2,7,8,5,dog
3,3,6,7,horse
2,2,8,3,duck
4,4,2,1,mouse
```
代码清单 5-1
`ch05_01.csv`

由于该文件使用逗号分隔，您可以使用 `read_csv()` 函数读取其内容，并将其转换为一个 dataframe 对象。

```python
>>> csvframe = pd.read_csv('ch05_01.csv')
>>> csvframe
white  red  blue  green animal
0      1    5     2      3    cat
1      2    7     8      5    dog
2      3    3     6      7  horse
3      2    2     8      3   duck
4      4    4     2      1  mouse
```

本章继续使用 Python 交互式命令行，但您同样可以选择使用 Jupyter Notebook，将代码插入单元格中，并以交互方式逐一执行。在使用 Jupyter Notebook 的情况下，您将获得数据框结果的图形化显示，如图 5-1 所示。

![](img/336498_3_En_5_Fig1_HTML.jpg)

**图 5-1** 在 Jupyter Notebook 上执行的书籍代码

如您所见，读取 CSV 文件中的数据相当简单。CSV 文件是表格数据，其中同一列的值由逗号分隔。由于 CSV 文件被视为文本文件，您也可以使用 `read_table()` 函数，但需要指定分隔符。

```python
>>> pd.read_table('ch05_01.csv',sep=',')
white  red  blue  green animal
0      1    5     2      3    cat
1      2    7     8      5    dog
2      3    3     6      7  horse
3      2    2     8      3   duck
4      4    4     2      1  mouse
```

在这个例子中，您可以看到 CSV 文件的第一行包含了标识所有列的标题。但这并非普遍情况；表格数据往往直接以第一行开始（见代码清单 5-2）。

```
1,5,2,3,cat
2,7,8,5,dog
3,3,6,7,horse
2,2,8,3,duck
4,4,2,1,mouse
```
```python
>>> pd.read_csv('ch05_02.csv')
1  5  2  3    cat
0  2  7  8  5    dog
1  3  3  6  7  horse
2  2  2  8  3   duck
3  4  4  2  1  mouse
```
代码清单 5-2
`ch05_02.csv`

在这种情况下，您可以通过将 `header` 选项设置为 `None`，来确保 pandas 为列分配默认名称。

```python
>>> pd.read_csv('ch05_02.csv', header=None)
0  1  2  3      4
0  1  5  2  3    cat
1  2  7  8  5    dog
2  3  3  6  7  horse
3  2  2  8  3   duck
4  4  4  2  1  mouse
```

此外，您还可以通过向 `names` 选项分配一个标签列表来直接指定列名。

```python
>>> pd.read_csv('ch05_02.csv', names=['white','red','blue','green','animal'])
white  red  blue  green animal
0      1    5     2      3    cat
1      2    7     8      5    dog
2      3    3     6      7  horse
3      2    2     8      3   duck
4      4    4     2      1  mouse
```

在更复杂的情况下，当您希望通过读取 CSV 文件来创建具有层级结构的 dataframe 时，可以扩展 `read_csv()` 函数的功能，通过添加 `index_col` 选项，将需要转换为索引的所有列都指定出来。

为了更好地理解这种可能性，创建一个新的 CSV 文件，其中包含两列用作层级结构的索引。然后，将该文件保存到工作目录中，命名为 `ch05_03.csv`（见代码清单 5-3）。

```
color,status,item1,item2,item3
black,up,3,4,6
black,down,2,6,7
white,up,5,5,5
white,down,3,3,2
white,left,1,2,1
red,up,2,2,2
red,down,1,1,4
```
代码清单 5-3
`ch05_03.csv`

现在，您可以通过编写以下代码将 CSV 文件读取为 dataframe。

```
>>> pd.read_csv('ch05_03.csv', index_col=['color','status'])
item1  item2  item3
color status
black up          3      4      6
down        2      6      7
white up          5      5      5
down        3      3      2
left        1      2      1
red   up          2      2      2
down        1      1      4
```

#### 使用正则表达式解析 TXT 文件

在某些情况下，待解析数据文件中的分隔符并非定义良好的逗号或分号。此时，正则表达式便能派上用场。事实上，你可以在 `read_table()` 函数中通过 `sep` 选项指定一个正则表达式。

为了更好地理解正则表达式及其如何作为值分隔的标准，让我们从一个简单案例开始。例如，假设你的 TXT 文件中，值以空格或制表符分隔，且顺序不可预测。这种情况下，你必须使用正则表达式，因为这是唯一能同时处理这两种分隔符类型的方法。你可以使用通配符 `\s*` 来实现。`\s` 代表空格或制表符（如果你想指定制表符，可使用 `\t`），而星号表示可能存在多个字符（其他常用通配符参见表 5-1）。也就是说，值之间可能由多个空格或多个制表符分隔。

**表 5-1** 元字符

| `.` | 单个字符，换行符除外 |
| --- | --- |
| `\d` | 数字 |
| `\D` | 非数字字符 |
| `\s` | 空白字符 |
| `\S` | 非空白字符 |
| `\n` | 换行符 |
| `\t` | 制表符 |
| `\uxxxx` | 由十六进制数 `xxxx` 指定的 Unicode 字符 |

以一个极端情况为例：值由制表符或空格以随机顺序分隔（参见代码清单 5-4）。

```
white red blue green
1   5    2     3
2   7    8     5
3   3    6     7
代码清单 5-4
ch05_04.txt
```

现在，你可以编写以下代码，将 TXT 文件作为数据框读取：

```
>>> pd.read_table('ch05_04.txt',sep='\s+', engine='python')
white  red  blue  green
0      1    5     2      3
1      2    7     8      5
2      3    3     6      7
```

如你所见，结果是一个完美的数据框，其中的值排列得井井有条。

接下来你将看到一个例子，它可能看起来奇怪或不同寻常，但实际并非如此罕见。这个例子非常有助于理解正则表达式的强大潜力。实际上，你可能通常认为分隔符是逗号、空格、制表符等特殊字符，但事实上，你也可以将字母数字字符视为分隔符，例如整数 0。

在这个例子中，你需要从一个 TXT 文件中提取数字部分，该文件包含一系列字符，其中数值与文字字符完全融合在一起。

请记住：当 TXT 文件中不存在列标题时，将 `header` 选项设置为 `None`（参见代码清单 5-5）。

```
000END123AAA122
001END124BBB321
002END125CCC333
代码清单 5-5
ch05_05.txt
```

现在，你可以编写以下代码，将 TXT 文件作为数据框读取：

```
>>> pd.read_table('ch05_05.txt', sep='\D+', header=None, engine='python')
0    1    2
0  0  123  122
1  1  124  321
2  2  125  333
```

另一个相当常见的操作是排除某些行不进行解析。实际上，你并不总想包含文件中的标头或不必要的注释（参见代码清单 5-6）。使用 `skiprows` 选项，你可以排除所有不需要的行，只需分配一个包含待解析时忽略的行号的数组。

使用此选项时需注意：如果要排除前五行，应写 `skiprows = 5`；但如果要排除第五行，则应写 `skiprows = [5]`。

```
########### LOG FILE ############
This file has been generated by automatic system
white,red,blue,green,animal
12-Feb-2015: Counting of animals inside the house
1,5,2,3,cat
2,7,8,5,dog
13-Feb-2015: Counting of animals outside the house
3,3,6,7,horse
2,2,8,3,duck
4,4,2,1,mouse
代码清单 5-6
ch05_06.txt
```

现在，你可以编写以下代码，将此 TXT 文件作为数据框读取：

```
>>> pd.read_table('ch05_06.txt',sep=',',skiprows=[0,1,3,6])
white  red  blue  green animal
0      1    5     2      3    cat
1      2    7     8      5    dog
2      3    3     6      7  horse
3      2    2     8      3   duck
4      4    4     2      1  mouse
```

#### 分块读取 TXT 文件

当处理大型文件或只关注文件中的部分内容时，你通常需要分段（分块）读取文件。这既是为了进行任何迭代操作，也是因为你并不想解析整个文件。

例如，如果你只想读取文件的一部分，可以明确指定要解析的行数。借助 `nrows` 和 `skiprows` 选项，你可以选择起始行 `n` (`n = SkipRows`) 以及其后要读取的行数 (`nrows = i`)。

```
>>> pd.read_csv('ch05_02.csv',skiprows=[2],nrows=3,header=None)
0  1  2  3     4
0  1  5  2  3   cat
1  2  7  8  5   dog
2  2  2  8  3  duck
```

另一个有趣且相当常见的操作是将要解析的文本部分拆分成多个小块。然后，可以对每个小块执行特定操作，从而逐块迭代。

例如，你想每三行对某一列的值求和，然后将这些和插入到一个序列中。这个例子虽然简单且不切实际，但非常容易理解。一旦掌握了底层机制，你就能将其应用于更复杂的场景中。

```
>>> out = pd.Series(dtype='float64')
>>> i = 0
>>> pieces = pd.read_csv('ch05_01.csv',chunksize=3)
>>> for piece in pieces:
...    out.at[i] = piece['white'].sum()...    i = i + 1
...
>>> out
0    6
1    6
dtype: int64
```

### 写入 CSV 格式数据

除了读取文件中的数据外，将计算结果生成的数据文件，或者更一般地说，将数据结构中包含的数据写入文件，也是常见的操作。

例如，你可能希望将数据框中的内容写入一个 CSV 文件。要执行此写入过程，你需要使用 `to_csv()` 函数。该函数接受你生成的文件名作为参数（参见清单 5-7）。

```
>>> frame = pd.DataFrame(np.arange(16).reshape((4,4)),
index = ['red', 'blue', 'yellow', 'white'],
columns = ['ball', 'pen', 'pencil', 'paper'])
>>> frame.to_csv('ch05_07.csv')
```

如果你打开由 pandas 库生成的新文件 `ch05_07.csv`，你将会看到如下清单 5-7 所示的数据。

```
,ball,pen,pencil,paper
0,1,2,3
4,5,6,7
8,9,10,11
12,13,14,15
清单 5-7
ch05_07.csv
```

从前面的例子可以看出，当你将数据框写入文件时，默认情况下，索引和列名会被标记在文件中。可以通过将 `index` 和 `header` 两个选项设置为 `False` 来改变这一默认行为（参见清单 5-8）。

```
1,2,3
5,6,7
9,10,11
13,14,15
清单 5-8
ch05_07b.csv
```

```
>>> frame.to_csv('ch05_07b.csv', index=False, header=False)
```

写入文件时需要记住的一点是，数据结构中存在的 `NaN` 值在文件中会显示为空字段（参见清单 5-9）。


## 读取和写入 HTML 文件

pandas 为 HTML 格式提供了相应成对的 I/O API 函数。

* `read_html()`
* `to_html()`

这两个函数非常有用。你将体会到能够直接将数据框这类复杂数据结构转换为 HTML 表格的好处，而无需手动编写冗长的 HTML 代码，尤其是在处理 Web 相关任务时。

反向操作也同样有用，因为目前数据的主要来源正是互联网世界。事实上，互联网上的很多数据并不总是以“即用”的形式打包在 TXT 或 CSV 文件中。然而，数据经常作为网页文本的一部分呈现。因此，拥有一个用于读取的函数证明是极为有用的。

这种操作非常普遍，目前被识别为网页抓取。这个过程正逐渐成为一系列流程中的基础部分，并将整合到数据分析的前端环节：数据挖掘与数据准备。

**注意**
现在许多网站都已采用 HTML5 格式，以避免缺少模块和出现错误信息的问题。我强烈建议你安装 `html5lib` 模块。Anaconda 指定的安装命令为：`conda install html5lib`

### 写入 HTML 数据

现在你将学习如何将数据框转换为 HTML 表格。数据框的内部结构会自动转换为嵌套的 `<TH>`、`<TR>` 和 `<TD>` 标签，并保留任何内部层级关系。你无需了解 HTML 即可使用这类函数。

由于数据框的数据结构可能相当复杂和庞大，在需要开发网页时，拥有这样一个函数会非常方便。

为了更好地理解这一潜力，下面是一个示例。你可以从定义一个简单的数据框开始。

借助 `to_html()` 函数，你可以直接将数据框转换为 HTML 表格。

```python
>>> frame = pd.DataFrame(np.arange(4).reshape(2,2))
```

由于 I/O API 函数是在 pandas 数据结构中定义的，因此你可以直接在数据框实例上调用 `to_html()` 函数。

```python
>>> print(frame.to_html())
```

正如你所见，为了尊重数据框的内部结构，创建 HTML 表格所需的整个由 HTML 标签构成的结构已被正确生成。

在接下来的例子中，你将看到表格在 HTML 文件中自动生成的效果。为此，我们使用一个比上一个例子稍复杂的数据框，其中包含索引和列名的标签。

```python
>>> frame = pd.DataFrame( np.random.random((4,4)),
...                    index = ['white','black','red','blue'],
...                    columns = ['up','down','right','left'])
>>> frame
up      down     right      left
white  0.292434  0.457176  0.905139  0.737622
black  0.794233  0.949371  0.540191  0.367835
red    0.204529  0.981573  0.118329  0.761552
blue   0.628790  0.585922  0.039153  0.461598
```

现在你将专注于通过生成字符串来编写一个 HTML 页面。这是一个简单而基础的示例，但对于理解并直接在 Web 浏览器中测试 pandas 的功能非常有用。

首先，创建一个包含 HTML 页面代码的字符串。

```python
>>> s = ['<html><head></head><body>']
>>> s.append('<h1>My DataFrame</h1>')
>>> s.append(frame.to_html())
>>> s.append('</body></html>')
>>> html = "".join(s)
```

现在，HTML 页面的所有代码都包含在 `html` 变量中，你可以将其直接写入名为 `myFrame.html` 的文件：

```python
>>> html_file = open('myFrame.html','w')
>>> html_file.write(html)
>>> html_file.close()
```

在你的工作目录中将会出现一个名为 `myFrame.html` 的新 HTML 文件。双击它即可从浏览器直接打开。一个 HTML 表格将会出现在页面的左上角，如图 5-2 所示。

![图 5-2](img/336498_3_En_5_Fig2_HTML.jpg)

一个表格包含 4 列，分别标记为 `up`、`down`、`right` 和 `left`，以及 4 行，分别标记为 `white`、`black`、`red` 和 `blue`。行和列中显示有数值。

图 5-2：数据框在网页中显示为 HTML 表格

### 从 HTML 文件读取数据

正如你刚刚所见，pandas 可以轻松地从 dataframe 生成 HTML 表格。相反的过程也是可行的；`read_html()` 函数会解析一个 HTML 页面，查找 HTML 表格。如果找到，它会将该表格转换为一个可用于数据分析的 dataframe 对象。

更准确地说，即使只有一个表格，`read_html()` 函数也会返回一个 dataframe 列表。被解析的来源可以是不同类型。例如，你可能需要读取某个目录中的 HTML 文件。你可以解析你在前一个示例中创建的 HTML 文件：

```python
>>> web_frames = pd.read_html('myFrame.html')
>>> web_frames[0]
Unnamed: 0        up      down     right      left
0      white  0.292434  0.457176  0.905139  0.737622
1      black  0.794233  0.949371  0.540191  0.367835
2        red  0.204529  0.981573  0.118329  0.761552
3       blue  0.628790  0.585922  0.039153  0.461598
```

如你所见，所有与 HTML 表格无关的标签都不会被考虑。此外，`web_frames` 是一个 dataframe 列表，尽管在你的例子中，你提取的 dataframe 只有一个。但是，你可以通过经典的方式调用它，来选择列表中你想要使用的项。在这种情况下，该项是唯一的，因此索引将为 0。

然而，关于 `read_html()` 函数最常用的模式是直接解析网络上的一个 URL。这样，网络中的网页被直接解析，并提取其中的表格。

例如，你可以调用一个包含 HTML 表格的网页，该表格显示了一个带有一些名称和分数的排名列表。

```python
>>> ranking = pd.read_html('https://www.meccanismocomplesso.org/en/meccanismo-complesso-sito-2/classifica-punteggio/')
>>> ranking[0]
Unnamed: 0        Member  Points  Levels
0             1   BrunoOrsini    2075     NaN
1             2     Berserker     700     NaN
2             3  albertosallu     275     NaN
3             4           Jon     180     NaN
4             5          Mr.Y     180     NaN
..          ...           ...     ...     ...
110         111  Gigi Bertana       5     NaN
111         112       p.barut       5     NaN
112         113  Indri4Africa       5     NaN
113         114     ghirograf       5     NaN
114         115  Marco Corbet       5     NaN
[115 rows x 4 columns]
```

同样的操作可以在任何包含一个或多个表格的网页上运行。

### 从 XML 读取数据

在 I/O API 函数列表中，没有专门针对 XML（可扩展标记语言）格式的工具。事实上，尽管它没有被列出，但这种格式非常重要，因为许多结构化数据都是以 XML 格式提供的。这不成问题，因为除了 pandas 之外，Python 还有许多其他库可以管理 XML 格式数据的读写。

这些库之一是 `lxml` 库，它在解析非常大的文件时以其卓越的性能而著称。在本节中，你将学习如何使用此模块解析 XML 文件，以及如何将其与 pandas 集成，以获取包含所需数据的 dataframe。有关该库的更多信息，我强烈建议访问 `lxml` 的官方网站 [`http://lxml.de/index.html`](http://lxml.de/index.html)。

以清单 5-11 中所示的 XML 文件为例。将其写入并保存为 `books.xml`，直接放在你的工作目录中。

```xml
<Book>
    <Author>Ross, Mark</Author>
    <Title>XML Cookbook</Title>
    <Genre>Computer</Genre>
    <Price>23.56</Price>
    <PublishDate>2014-22-01</PublishDate>
</Book>
<Book>
    <Author>Bracket, Barbara</Author>
    <Title>XML for Dummies</Title>
    <Genre>Computer</Genre>
    <Price>35.95</Price>
    <PublishDate>2014-12-16</PublishDate>
</Book>
```

清单 5-11
`books.xml`

在此示例中，你将获取 XML 文件中描述的数据结构，并将其直接转换为一个 dataframe。首先要做的是使用 `lxml` 库的子模块 `objectify`，按如下方式导入它。

```python
>>> from lxml import objectify
```

现在，你可以仅使用 `parse()` 函数来解析 XML 文件。

```python
>>> xml = objectify.parse('books.xml')
>>> xml
```

你将得到一个对象树，这是 `lxml` 模块的一种内部数据结构。

更详细地看一下这种类型的对象。为了在这种树状结构中导航，以便逐个元素选择，你必须首先定义根节点。你可以使用 `getroot()` 函数来完成此操作。

```python
>>> root = xml.getroot()
```

现在已定义了结构的根节点，你可以访问树的各个节点，每个节点对应于原始 XML 文件中包含的标签。这些项的名称将与相应标签的名称相同。要选择它们，只需用点分隔各个标签来编写，这在一定程度上反映了树中节点的层次结构。

```python
>>> root.Book.Author
'Ross, Mark'
>>> root.Book.PublishDate
'2014-22-01'
```

通过这种方式，你可以逐个访问节点，但也可以同时使用 `getchildren()` 访问各个元素。使用此函数，你将获得引用元素的所有子节点。

```python
>>> root.getchildren()
[<Element Book at 0x...>, <Element Book at 0x...>]
```

使用 `tag` 属性，你可以获取与子节点对应的标签名称。

```python
>>> [child.tag for child in root.Book.getchildren()]
['Author', 'Title', 'Genre', 'Price', 'PublishDate']
```

而使用 `text` 属性，你可以获取对应标签之间的值。

```python
>>> [child.text for child in root.Book.getchildren()]
['Ross, Mark', 'XML Cookbook', 'Computer', '23.56', '2014-22-01']
```

然而，无论你在 `lxml.etree` 树状结构中移动的能力如何，你需要的是将其转换为 dataframe。定义以下函数，其任务是分析 `etree` 的内容，以逐行填充一个 dataframe。

```python
>>> def etree2df(root):
...    column_names = []
...    for i in range(0,len(root.getchildren()[0].getchildren())):
...       column_names.append(root.getchildren()[0].getchildren()[i].tag)
...    xml_frame = pd.DataFrame(columns=column_names)
...    for j in range(0, len(root.getchildren())):
...       obj = root.getchildren()[j].getchildren()
...       texts = []
...       for k in range(0, len(column_names)):
...          texts.append(obj[k].text)
...       row = dict(zip(column_names, texts))
...       row_s = pd.Series(row)
...       row_s.name = j
...       xml_frame = pd.concat([xml_frame, row_s.to_frame().T], ignore_index=True)
...    return xml_frame
...
>>> etree2df(root)
Author            Title     Genre  Price PublishDate
0        Ross, Mark     XML Cookbook  Computer  23.56  2014-22-01
1  Bracket, Barbara  XML for Dummies  Computer  35.95  2014-12-16
```

## 在 Microsoft Excel 文件中读写数据

在上一节中，你了解了如何轻松地从 CSV 文件中读取数据。然而，数据以表格形式收集在 Excel 电子表格中的情况也并不罕见。

`pandas` 为此类格式提供了专门的函数：

- `to_excel()`

- `read_excel()`

`read_excel()` 函数能够读取 Excel 2003（`.xls`）文件和 Excel 2007（`.xlsx`）文件。这得益于内部模块 `xlrd` 的集成。

不过，为了从 Excel 文件执行这些读取和格式转换操作，Python 需要使用一个名为 `openpyxl` 的特殊库。要继续后续示例，你首先需要在虚拟环境中安装它。如果你使用的是 Anaconda 平台，可以打开 CMD.exe 提示符控制台，并使用以下命令：

```
conda install openpyxl
```

如果你愿意，也可以通过图形方式安装：在 Anaconda Navigator 中选择此包并请求安装。见图 5-3。

![](img/336498_3_En_5_Fig3_HTML.jpg)

一张 Anaconda Navigator 的截图，显示了在 edition 3 环境下 `openpyxl` 的描述信息。

**图 5-3** Anaconda Navigator 提供了安装 `openpyxl` 等包的图形化方式

如果你没有使用 Anaconda 平台，安装 `openpyxl` 最简单的方法是通过 PyPI。

```
pip install openpyxl
```

将该包安装到合适的虚拟环境后，打开一个 Excel 文件并输入如图 5-4 所示的数据。将数据复制到 sheet1 和 sheet2 中，然后将文件另存为 `ch05_data.xlsx`。

![](img/336498_3_En_5_Fig4_HTML.png)

两个 Excel 工作表表格。上方表格展示了从 A 到 E 共 5 列，其中 A 列被高亮显示。共 6 行，第 2 至 4 行标记为 a 到 c，第 5 行中 A 列被高亮显示。下方表格展示了从 A 到 E 共 5 列，其中 E 列被高亮显示。共 6 行，第 2 至 4 行标记为 a 到 c，第 5 行中 E 列被高亮显示。

**图 5-4** Excel 文件中 sheet1 和 sheet2 的两个数据集

要读取 XLS 文件中的数据并将其转换为 DataFrame，你只需使用 `read_excel()` 函数即可。

```
>>> pd.read_excel('ch05_data.xlsx')
   white  red  green  black
a     12   23     17     18
b     22   16     19     18
c     14   23     22     21
```

如你所见，默认情况下，返回的 DataFrame 由第一个工作表中的数据组成。但是，如果你需要加载第二个工作表中的数据，则必须将工作表名称或工作表编号（索引）作为第二个参数指定。

```
>>> pd.read_excel('ch05_data.xlsx','Sheet2')
   yellow  purple  blue  orange
A      11      16    44      22
B      20      22    23      44
C      30      31    37      32
>>> pd.read_excel('ch05_data.xlsx',1)
   yellow  purple  blue  orange
A      11      16    44      22
B      20      22    23      44
C      30      31    37      32
```

写入操作同理。要将 DataFrame 转换为 Excel 电子表格，你需要编写以下代码：

```
>>> frame = pd.DataFrame(np.random.random((4,4)),
...                      index = ['exp1','exp2','exp3','exp4'],
...                      columns = ['Jan2015','Fab2015','Mar2015','Apr2005'])
>>> frame
         Jan2015   Fab2015   Mar2015   Apr2005
exp1  0.030083  0.065339  0.960494  0.510847
exp2  0.531885  0.706945  0.964943  0.085642
exp3  0.981325  0.868894  0.947871  0.387600
exp4  0.832527  0.357885  0.538138  0.357990
>>> frame.to_excel('ch05_data2.xlsx')
```

在工作目录中，你会找到一个包含该数据的新 Excel 文件，如图 5-5 所示。

![](img/336498_3_En_5_Fig5_HTML.png)

一张显示数值数据的表格，包含 5 列（分别为 Jan 2015、Feb 2015、March 2015、April 2005）和 4 行（exp1 至 exp4）。

**图 5-5** Excel 文件中的 DataFrame

## JSON 数据

JSON（JavaScript 对象表示法）已成为最常见的标准格式之一，尤其适用于网络数据传输。因此，如果你想在网络上使用数据，处理这种数据格式是很常见的。

这种格式的特别之处在于其极大的灵活性，尽管它的结构远非你所熟悉的表格结构。

在本节的第一部分，你将学习如何使用 `read_json()` 和 `to_json()` 函数，以保持在本章讨论的 I/O API 函数框架内。在本节的第二部分，你将看到另一个示例，其中你需要处理 JSON 格式的结构化数据，这与实际案例更相关。

在我看来，一个有用的在线 JSON 格式校验应用程序是 JSON Viewer，可通过 [`http://jsonviewer.stack.hu/`](http://jsonviewer.stack.hu/) 访问。这个网络应用程序，一旦你输入或粘贴 JSON 格式的数据，就能让你检查输入的格式是否有效。此外，它还会显示树状结构，以便你更好地理解其结构（见图 5-6）。

让我们从更实用的案例开始，即当你有一个数据框（dataframe），需要将其转换为 JSON 文件时。因此，先定义一个数据框，然后对其调用 `to_json()` 函数，并将你想创建的文件名作为参数传入。

```
>>> frame = pd.DataFrame(np.arange(16).reshape(4,4),
...                      index=['white','black','red','blue'],
...                      columns=['up','down','right','left'])
>>> frame.to_json('frame.json')
```

在工作目录中，你会找到一个包含数据框数据并已转换为 JSON 格式的新 JSON 文件（见清单 5-12）。

```
{"up":{"white":0,"black":4,"red":8,"blue":12},"down":{"white":1,"black":5,"red":9,"blue":13},"right":{"white":2,"black":6,"red":10,"blue":14},"left":{"white":3,"black":7,"red":11,"blue":15}}
```

**清单 5-12** `frame.json`

反向操作也是可行的，使用 `read_json()` 并将文件名作为参数传入。

```
>>> pd.read_json('frame.json')
       up  down  right  left
white   0     1      2     3
black   4     5      6     7
red     8     9     10    11
blue   12    13     14    15
```

你看到的示例是一个相当简单的情况，其中 JSON 数据是表格形式的（因为 `frame.json` 文件来自一个数据框）。然而，通常情况下，JSON 文件并不具有表格结构。因此，你需要以某种方式将这种 `dict`（字典）结构文件转换为表格形式。这个过程称为*标准化*。

pandas 库提供了一个名为 `json_normalize()` 的函数，可以将 `dict` 或列表转换为表格。首先，你需要导入该函数：

```
>>> from pandas.io.json import json_normalize
```

然后，使用任何文本编辑器编写一个 JSON 文件，如清单 5-13 所示。将其保存到工作目录中，文件名为 `books.json`。

# HDF5 与 Pickle 格式：二进制数据存储

## JSON 数据处理示例
如你所见，文件结构不再是表格形式，而是更复杂了。因此，使用 `read_json()` 函数的方法不再适用。从这个示例中你将学到，你仍然可以从这种结构中获取表格形式的数据。首先，你需要加载 JSON 文件的内容并将其转换为字符串。

```python
>>> import pandas.io.json as json
>>> file = open('books.json','r')
>>> text = file.read()
>>> text = json.loads(text)
```

现在你可以应用 `json_normalize()` 函数了。快速浏览一下 JSON 文件中数据的内容，例如，你可能想要提取一个包含所有书籍的表格。那么，就将 `books` 这个键作为第二个参数传入。

```python
>>> pd.json_normalize(text,'books')
               title  price
0         XML Cookbook  23.56
1  Python Fundamentals  50.70
2    The NumPy library  12.30
3      Java Enterprise  28.60
4                HTML5  31.35
5   Python for Dummies  28.00
```

该函数会读取所有以 `books` 为键的元素的内容。所有属性都将转换为嵌套的列名，而相应的值将填充数据框。对于索引，该函数会分配一个递增的数字序列。

然而，你得到的是一个仅包含部分内部信息的数据框。如果能添加同一层级上其他键的值会更有用。在这种情况下，你可以通过向函数的第三个参数传入一个键列表来添加其他列。

```python
>>> pd.json_normalize(text,'books',['writer','nationality'])
               title  price           writer nationality
0         XML Cookbook  23.56        Mark Ross         USA
1  Python Fundamentals  50.70        Mark Ross         USA
2    The NumPy library  12.30        Mark Ross         USA
3      Java Enterprise  28.60  Barbara Bracket          UK
4                HTML5  31.35  Barbara Bracket          UK
5   Python for Dummies  28.00  Barbara Bracket          UK
```

现在，你从起始的树状结构得到了一个数据框。在图 5-7 中，你可以看到该数据框在 Jupyter Notebook 中的显示效果。

> **图 5-7** 在 Jupyter Notebook 中从起始树状结构获取的数据框

## HDF5 格式
到目前为止，你已经了解了如何以文本格式写入和读取数据。当你的数据分析涉及大量数据时，最好使用二进制格式。Python 中有几种处理二进制数据的工具。在这一领域取得一定成功的库是 `HDF5` 库。

HDF 这个术语代表 *层级数据格式*，事实上，这个库主要用于读写 `HDF5` 文件，这些文件包含具有节点的结构，并且能够存储多个数据集。

这个库完全用 C 语言开发，但也提供了与其他类型语言（如 Python、MATLAB 和 Java）的接口。它非常高效，尤其是在使用此格式保存海量数据时。与其他更简单的二进制格式相比，`HDF5` 支持实时压缩，从而利用数据结构中的重复模式来压缩文件大小。

目前，Python 中的可选方案是 `PyTables` 和 `h5py`。这两种形式在多个方面有所不同，因此选择哪种很大程度上取决于使用者的需求。

`h5py` 提供了与高级 API `HDF5` 的直接接口，而 `PyTables` 则抽象了 `HDF5` 的许多细节，以提供更灵活的数据容器、索引表、查询能力以及计算方面的其他媒介。

在这里，你将看到 `PyTables` 库的实际应用，它必须安装在你的虚拟环境中。如果你使用 Anaconda，可以通过 Anaconda Navigator 或从控制台使用以下命令轻松安装它：

```bash
conda install pytables
```

否则，你可以通过 `PyPI` 安装它。

```bash
pip install pytables
```

`pandas` 有一个类似 `dict` 的类叫 `HDFStore`，它使用 `PyTables` 来存储 pandas 对象。因此，在使用 `HDF5` 格式之前，你必须导入 `HDFStore` 类：

```python
>>> from pandas.io.pytables import HDFStore
```

现在，你已准备好将数据框（dataframe）的数据存储到一个 `.h5` 文件中。首先，创建一个数据框。

```python
>>> frame = pd.DataFrame(np.arange(16).reshape(4,4),
...                      index=['white','black','red','blue'],
...                      columns=['up','down','right','left'])
```

现在创建一个 `HDF5` 文件，并将其命名为 `mydata.h5`，然后将数据框的数据存入其中。

```python
>>> store = HDFStore('mydata.h5')
>>> store['obj1'] = frame
```

从这里，你可以猜到如何将多个数据结构存储到同一个 `HDF5` 文件中，为每个数据结构指定一个标签。

```python
>>> frame
up  down  right  left
white   0     1      2     3
black   4     5      6     7
red     8     9     10    11
blue   12    13     14    15
>>> store['obj2'] = frame
```

因此，使用这种格式，你可以将多个数据结构存储在一个由 `store` 变量表示的文件中。

```python
>>> store

File path: mydata.h5
```

反向过程也非常简单。假设有一个包含多种数据结构的 `HDF5` 文件，可以通过以下方式调用其中的对象：

```python
>>> store['obj2']
up  down  right  left
white   0     1      2     3
black   4     5      6     7
red     8     9     10    11
blue   12    13     14    15
```

## Pickle——Python 对象序列化
`pickle` 模块实现了一个强大的算法，用于 Python 数据结构的序列化和反序列化。*Pickling* 是将对象层次结构转换为字节流的过程。

这允许对象被传输和存储，然后由接收者自身重建，并保留所有原始特征。

在 Python 中，pickling 操作由 `pickle` 模块执行，但目前有一个名为 `_pickle` 的模块，它是大量优化 `pickle` 模块（用 C 语言编写）工作的成果。这个模块实际上可以比 `pickle` 模块快 **1000 倍**。然而，无论你使用哪个模块，这两个模块的接口几乎相同。

在具体介绍 pandas 中操作此格式的 I/O 函数之前，让我们更详细地看一下 `_pickle` 模块，并了解如何使用它。

### 使用 cPickle 序列化 Python 对象

# Python Pickling 与数据库交互

`pickle`（或 `cPickle`）模块使用的数据格式是 Python 专用的。默认情况下，使用 ASCII 表示法来表示它，以便于人眼阅读。然后，通过使用文本编辑器打开文件，你或许能够理解其内容。要使用此模块，你首先必须导入它：

```
>>> import _pickle as pickle
```

然后创建一个足够复杂、具有内部数据结构的对象，例如一个 `dict` 对象。

```
>>> data = { 'color': ['white','red'], 'value': [5, 7]}
```

现在，通过 `cPickle` 模块的 `dumps()` 函数对 `data` 对象执行序列化。

```
>>> pickled_data = pickle.dumps(data)
```

现在，要查看它是如何序列化 `dict` 对象的，你需要查看 `pickled_data` 变量的内容。

```
>>> print(pickled_data)
b'\x80\x04\x95/\x00\x00\x00\x00\x00\x00\x00}\x94(\x8c\x05color\x94]\x94(\x8c\x05white\x94\x8c\x03red\x94e\x8c\x05value\x94]\x94(K\x05K\x07eu.'
```

一旦数据被序列化，就可以轻松地将其写入文件或通过网络套接字、管道等发送。

数据传输后，可以使用 `_pickle` 模块的 `loads()` 函数重建序列化的对象（反序列化）。

```
>>> nframe = pickle.loads(pickled_data)
>>> nframe
{'color': ['white', 'red'], 'value': [5, 7]}
```

## 使用 pandas 进行 Pickling

当使用 pandas 库进行 pickle（和 unpickle）时，一切都简单得多。无需在 Python 会话中导入 `cPickle` 模块；整个操作是隐式执行的。

此外，pandas 使用的序列化格式不完全是 ASCII 格式。

```
>>> frame = pd.DataFrame(np.arange(16).reshape(4,4), index = ['up','down','left','right'])
>>> frame.to_pickle('frame.pkl')
```

你的工作目录中会有一个名为 `frame.pkl` 的新文件，其中包含关于 `frame` 数据框的所有信息。

要打开 PKL 文件并读取内容，只需使用以下命令：

```
>>> pd.read_pickle('frame.pkl')
0   1   2   3
up      0   1   2   3
down    4   5   6   7
left    8   9  10  11
right  12  13  14  15
```

如你所见，所有关于 pickle 和 unpickle 操作的细节都对 pandas 用户完全隐藏，这使得那些专门从事数据分析的人的工作尽可能简单易懂。

**注意**：当你使用此格式时，请确保你打开的文件是安全的。实际上，pickle 格式的设计初衷并非为了抵御错误和恶意构造的数据。

## 与数据库交互

在许多应用中，数据很少来自文本文件，因为这显然不是最高效的数据存储方式。

数据通常存储在基于 SQL 的关系型数据库中，也存储在近年来变得非常流行的各种 NoSQL 数据库中。

将 SQL 中的数据加载到数据帧中相当简单，并且 pandas 提供了一些函数来简化此过程。

`pandas.io.sql` 模块提供了一个独立于数据库的统一接口，称为 `sqlalchemy`。这个接口简化了连接模式，因为无论使用哪种数据库，命令始终相同。要建立连接，你需要使用 `create_engine()` 函数。利用此功能，你可以配置使用驱动程序所需的所有属性，例如用户、密码、端口和数据库实例。

要在 Anaconda 上安装 `sqlalchemy`，你可以通过 Anaconda Navigator 轻松安装，或者从控制台使用以下命令安装：

```
conda install sqlalchemy
```

或者，你也可以通过 PyPI 安装：

```
pip install sqlalchemy
```

以下是各种类型数据库的示例列表：

```python
>>> from sqlalchemy import create_engine
```

对于 PostgreSQL：

```python
>>> engine = create_engine('postgresql://scott:tiger@localhost:5432/mydatabase')
```

对于 MySQL：

```python
>>> engine = create_engine('mysql+mysqldb://scott:tiger@localhost/foo')
```

对于 Oracle：

```python
>>> engine = create_engine('oracle://scott:tiger@127.0.0.1:1521/sidname')
```

对于 MSSQL：

```python
>>> engine = create_engine('mssql+pyodbc://mydsn')
```

对于 SQLite：

```python
>>> engine = create_engine('sqlite:///foo.db')
```

## 使用 SQLite3 加载和写入数据

作为第一个示例，你将使用 SQLite 数据库，并采用其自带的 Python 驱动程序 `sqlite3`。SQLite3 是一种以非常简单和轻量级方式实现 DBMS SQL 的工具，因此它可以被集成到任何使用 Python 语言实现的应用程序中。实际上，这个实用的软件允许你创建一个嵌入在单个文件中的数据库。

这使其成为任何希望拥有数据库功能但无需安装真实数据库的人的理想工具。对于想要在过渡到真实数据库之前进行练习的人，或者需要在单个程序内使用数据库功能收集数据而无需与数据库交互的人来说，SQLite3 可能是一个正确的选择。

创建一个数据帧，你将用它来在 SQLite3 数据库中创建一个新表。

```python
>>> frame = pd.DataFrame( np.arange(20).reshape(4,5),
...                       columns=['white','red','blue','black','green'])
>>> frame
white  red  blue  black  green
0      0    1     2      3      4
1      5    6     7      8      9
2     10   11    12     13     14
3     15   16    17     18     19
```

现在开始实现与 SQLite3 数据库的连接。

```python
>>> engine = create_engine('sqlite:///foo.db')
```

将数据帧转换为数据库中的表。

```python
>>> frame.to_sql('colors',engine)
```

相反，要读取数据库，你必须将表名和 `engine` 一起传递给 `read_sql()` 函数。

```python
>>> pd.read_sql('colors',engine)
index  white  red  blue  black  green
0      0      0    1     2      3      4
1      1      5    6     7      8      9
2      2     10   11    12     13     14
3      3     15   16    17     18     19
```

如你所见，即使在这种情况下，得益于 pandas 库中提供的 I/O API，对数据库的写入操作也变得非常简单。

现在你将看到相同的操作，但不是使用 I/O API。这有助于了解 pandas 如何被证明是读写数据库数据的有效工具。

首先，你必须建立到数据库的连接，并通过定义正确的数据类型来创建一个表，以便容纳要加载的数据。

```python
>>> import sqlite3
>>> query = """
... CREATE TABLE test
... (a VARCHAR(20), b VARCHAR(20),
...  c REAL,        d INTEGER
... );"""
>>> con = sqlite3.connect(':memory:')
>>> con.execute(query)

>>> con.commit()
```

现在，你可以使用 SQL `INSERT` 语句输入数据。

```markdown
### 在 Docker 容器中使用 PostgreSQL 加载和写入数据

要运行此示例，你必须在系统上安装 PostgreSQL 数据库。就我而言，我使用 Docker 系统及其容器创建了一个数据库实例。

Docker 是由 Solomon Hykes 于 2013 年开发的一款应用程序，它允许你通过容器机制快速、干净地拥有、生成和卸载服务器应用程序。像 PostgreSQL 数据库这样的应用程序通常安装在物理服务器上，并且无论如何，即使是在本地安装，也需要大量时间和充足资源。为了执行简单的测试或小规模使用而安装此类应用程序非常不切实际。此外，完全卸载它们也同样复杂，而能够持续安装和卸载这类应用程序可能会令人抓狂。Docker 允许你将诸如 PostgreSQL（但也包括所有其他数据库、Web 服务器或任何其他应用）之类的复杂应用程序“关”在容器中。这些软件包可以通过简单的点击进行安装、在特定端口上激活、停用和删除，如图 5-8 所示。

![图 5-8：包含一些数据库容器的 Docker 应用程序](img/336498_3_En_5_Fig8_HTML.jpg)

一张 Docker 桌面的截图展示了多个容器，其中 2 个已退出，1 个正在运行（已启动 2 小时）。

Docker 的安装非常简单。访问官方网站（[www.docker.com/](http://www.docker.com/)），页面上会显示适用于你系统的特定版本的下载按钮，如图 5-9 所示。

![图 5-9：用于下载应用程序的 Docker 主页](img/336498_3_En_5_Fig9_HTML.jpg)

Docker 桌面的主页顶部显示“开发更快。随处运行。”，中间显示“Stack Overflow 2022 开发者调查中最受喜爱的工具”，以及一个写着“下载 Docker Desktop for Windows”的框，后面跟着 Apple 芯片、Linux 和 Intel 芯片的图标。

按照安装说明操作。安装完成后，你可以继续安装包含最新版本 PostgreSQL 数据库的容器。打开命令控制台并输入以下命令：

```
docker pull postgres
```

通过此命令，你将包含一个 PostgreSQL 数据库的容器下载到你的系统中（你可以在 [https://hub.docker.com/_/postgres](https://hub.docker.com/_/postgres) 找到关于它的所有信息）。然而，该容器尚未激活。要激活它，请运行以下命令：

```
docker run --name postgresql -e POSTGRES_USER=myusername -e POSTGRES_PASSWORD=mypassword -p 5432:5432 -v /data:/var/lib/postgresql/data -d postgres
```

通过这种方式，你已在系统中激活了一个 PostgreSQL 数据库，该数据库监听 5432 端口，登录凭据定义为`myusername/mypassword`。现在你可以安装另一个包含数据库管理 Web 应用程序的容器，名为`pgAdmin`。为此，首先使用以下命令下载对应其最新版本的容器。

```
docker pull dpage/pgadmin4:latest
```

如你对数据库所做的那样，也使用以下命令在 Docker 应用程序中激活`pgAdmin`：

```
docker run --name "pgadmin4" -p 8082:80 -e "PGADMIN_DEFAULT_EMAIL=user@domain.com" -e "PGADMIN_DEFAULT_PASSWORD=SuperSecret" -d dpage/pgadmin4
```

该应用程序已在 8080 端口上激活，并定义了访问凭据。因此，如果你查看 Docker，将会看到如图 5-10 所示的情况。

![图 5-10：包含两个与 PostgreSQL 数据库及其管理应用程序相关活动容器的 Docker](img/336498_3_En_5_Fig10_HTML.jpg)

一张 Docker 桌面的截图展示了 2 个正在运行的容器，一个已启动 4 小时，另一个已启动 3 小时。

尽管服务已启动并运行，但工作尚未完成。打开浏览器，输入 `https://localhost:8082` 地址，加载 `pgAdmin` 日志页面。将出现如图 5-11 所示的界面。

![图 5-11：pgAdmin 的登录网页](img/336498_3_En_5_Fig11_HTML.jpg)

右侧为 pgAdmin 登录页面的截图，左侧是一幅世界地图的插画，地图上叠加着连接至一堆圆盘的人形图标。

输入用于激活 `pgAdmin` 容器的凭证，即可访问 PostgreSQL 数据库管理页面，如图 5-12 所示。

![图 5-12：pgAdmin 管理员页面](img/336498_3_En_5_Fig12_HTML.jpg)

pgAdmin 页面的截图展示了 PostgreSQL 的管理工具。

点击**添加新服务器**按钮，将打开一个包含服务器设置的对话框。在**通用**面板中，将数据库名称输入为 `postgres`。在**连接**面板中，将服务器 IP 地址输入为 `172.17.0.2`，用户名为 `myusername`，密码为 `mypassword`（参见图 5-13）。点击**保存**按钮以激活服务器。

![图 5-13：作为服务器的 PostgreSQL 数据库的设置](img/336498_3_En_5_Fig13_HTML.jpg)

两张截图。左侧面板展示了 PostgreSQL 的通用设置。右侧面板展示了 PostgreSQL 的连接设置。

如果 IP 地址不匹配，可通过在控制台输入以下命令，直接从数据库容器中检查：

```
docker inspect postgresql
```

输出结果将显示所有容器的设置列表。您可以通过选择**网络设置** ➤ **IP 地址**来找到 IP 地址。

现在，您已拥有一个监听 5432 端口的数据库服务，接下来要做的第一件事是安装 `psycopg2` 库，该库专门用于管理和处理与数据库的连接。

如果使用 Anaconda：

```
conda install psycopg2
```

或者，如果您使用的是 PyPI：

```
pip install psycopg2
```

现在，您可以建立与数据库的连接：

```python
>>> import psycopg2
>>> from sqlalchemy import create_engine
>>> engine = create_engine('postgresql://myusername:mypassword@localhost:5432/postgres')
```
```

# 使用 SQL 数据库 PostgreSQL 读写数据

## 创建一个 DataFrame 对象

```python
>>> import pandas as pd
>>> import numpy as np
>>> frame = pd.DataFrame(np.random.random((4,4)),
index=['exp1','exp2','exp3','exp4'],
columns=['feb','mar','apr','may']);
```

现在您看到将这些数据传输到一张表中是多么简单。使用 `to_sql()` 方法，您将把数据记录到名为 `dataframe` 的表中。

```python
>>> frame.to_sql('dataframe',engine)
```

如果您熟悉 SQL 语言，查看新创建的表及其内容的更经典方式是使用 `psql` 会话。

```
psql -U postgres
```

在本例中，由于您在 Docker 容器内的数据库上操作，您需要运行以下命令：

```
>docker exec -it postgresql bash
root@ecaad3f3da6f:/#
```

一个命令控制台会直接在容器内打开，您可以在其中执行手动命令，例如使用 `psql` 激活 PostgreSQL shell。

```
root@ecaad3f3da6f:/# psql -h localhost -U myusername
psql (15.3 (Debian 15.3-1.pgdg110+1))
Type "help" for help.
myusername=#
```

切换到您感兴趣的数据库：

```
myusername=# \c postgres
```

连接到数据库后，对新创建的表执行 SQL 查询。

```
postgres=# SELECT * FROM DATAFRAME;
index|       feb       |       mar       |       apr       |       may
-----+-----------------+-----------------+-----------------+-----------------
exp1 |0.757871296789076|0.422582915331819|0.979085739226726|0.332288515791064
exp2 |0.124353978978927|0.273461421503087|0.049433776453223|0.0271413946693556
exp3 |0.538089036334938|0.097041417119426|0.905979807772598|0.123448718583967
exp4 |0.736585422687497|0.982331931474687|0.958014824504186|0.448063967996436
(4 行)
```

回到 Python shell，将表转换为 DataFrame 也是一项简单的操作。同样，这里有一个 `read_sql_table()` 函数，它直接读取数据库并返回一个 DataFrame。

```python
>>> pd.read_sql_table('dataframe',engine)
index       feb       mar       apr       may
0  exp1  0.757871  0.422583  0.979086  0.332289
1  exp2  0.124354  0.273461  0.049434  0.027141
2  exp3  0.538089  0.097041  0.905980  0.123449
3  exp4  0.736585  0.982332  0.958015  0.448064
```

当您想要读取数据库中的数据时，将整个单张表转换为 DataFrame 并非最常用的操作。事实上，使用关系数据库的人更倾向于使用 SQL 语言，通过插入 SQL 查询来选择哪些数据以及以何种形式导出数据。

SQL 查询文本可以集成到 `read_sql_query()` 函数中。

```python
>>> pd.read_sql_query('SELECT index,apr,may FROM DATAFRAME WHERE apr > 0.5',engine)
index       apr       may
0  exp1  0.979086  0.332289
1  exp3  0.905980  0.123449
2  exp4  0.958015  0.448064
```

# 使用 NoSQL 数据库 MongoDB 读写数据

在所有 NoSQL 数据库（BerkeleyDB、Tokyo Cabinet 和 MongoDB）中，MongoDB 正变得越来越普及。鉴于其在众多系统中的广泛应用，考虑在数据分析过程中读写 pandas 库生成的数据的可能性似乎是合适的。

正如你在 PostgreSQL 数据库中所见，你同样可以利用 Docker 容器来运行 MongoDB 数据库。采用这种解决方案，而不必在系统上安装数据库。在完成本章示例后，你可以随时删除该数据库，只需点击一个简单的垃圾箱按钮即可从 Docker 中移除容器，让你的电脑保持干净和原样。

从网络上下载 Docker 容器，然后输入以下命令激活它：

```
docker pull mongo
docker run --name mymongo -p 27017:27017 -d mongo
```

现在服务正在监听端口 `27017`，你可以使用 MongoDB 的官方驱动程序 `pymongo` 连接到这个数据库。这个包也需要安装到你的虚拟环境中。如果你使用 Anaconda，可以使用 Anaconda Navigator 或输入控制台命令：

```
conda install pymongo
```

否则，最简单的方法是从 PyPI 安装它。

```
pip install pymongo
```

安装好包之后，你可以直接开始编写代码，首先导入 `MongoClient` 方法。这个方法对于创建与数据库的连接至关重要。

```python
>>> from pymongo import MongoClient
>>> client = MongoClient('localhost',27017)
```

MongoDB 的单个实例可以同时支持多个数据库。你需要指向一个特定的数据库。

```python
>>> db = client.mydatabase
>>> db
Database(MongoClient(host=['localhost:27017'], document_class=dict, tz_aware=False, connect=True), 'mydatabase')
```

为了引用这个对象，你也可以使用以下方式：

```python
>>> client['mydatabase']
Database(MongoClient(host=['localhost:27017'], document_class=dict, tz_aware=False, connect=True), 'mydatabase')
```

现在你已经定义了数据库，接下来需要定义集合。集合是存储在 MongoDB 中的一组文档，可以视为 SQL 数据库中的表。

```python
>>> collection = db.mycollection
>>> db['mycollection']
Collection(Database(MongoClient(host=['localhost:27017'], document_class=dict, tz_aware=False, connect=True), 'mydatabase'), 'mycollection')
>>> collection
Collection(Database(MongoClient(host=['localhost:27017'], document_class=dict, tz_aware=False, connect=True), 'mydatabase'), 'mycollection')
```

现在是将数据加载到集合中的时候了。创建一个数据框。

```python
>>> import pandas as pd
>>> import numpy as np
>>> frame = pd.DataFrame( np.arange(20).reshape(4,5),
...                       columns=['white','red','blue','black','green'])
>>> frame
white  red  blue  black  green
0      0    1     2      3      4
1      5    6     7      8      9
2     10   11    12     13     14
3     15   16    17     18     19
```

在添加到集合之前，这个数据框必须转换为 JSON 格式。转换过程并不像你想象的那样直接；这是因为你需要设置要记录到数据库中的数据，以便能够尽可能公平和简单地将其重新提取为数据框。

```
>>> import json
>>> record = json.loads(frame.T.to_json()).values()
>>> record
dict_values([{'white': 0, 'red': 1, 'blue': 2, 'black': 3, 'green': 4}, {'white': 5, 'red': 6, 'blue': 7, 'black': 8, 'green': 9}, {'white': 10, 'red': 11, 'blue': 12, 'black': 13, 'green': 14}, {'white': 15, 'red': 16, 'blue': 17, 'black': 18, 'green': 19}])
```

现在你终于准备好将一个文档插入到集合中了，你可以使用 `insert()` 函数来完成。

```
>>> collection.mydocument.insert_many(record)

```

如你所见，记录的每一行都有一个对象。现在数据已加载到 MongoDB 数据库的文档中，你可以执行逆向过程——读取文档中的数据，然后将其转换为数据框。

```
>>> result = collection['mydocument'].find()
>>> df = pd.DataFrame(list(result))
>>> del df['_id']
>>> df
white  red  blue  black  green
0      0    1     2      3      4
1      5    6     7      8      9
2     10   11    12     13     14
3     15   16    17     18     19
```

你已经移除了包含 MongoDB 内部引用 ID 编号的列。

## 结论

在本章中，你了解了如何使用 `pandas` 库的 I/O API 功能来向文件和数据库读写数据，同时保持数据框的结构。具体来说，本章阐述了根据格式类型进行多种数据写入和读取的模式。

在本章的最后部分，你了解了如何与最流行的数据库模型进行交互，以便将数据直接作为数据框记录到数据库中或从中读取数据，从而可以使用 `pandas` 工具进行处理。

在下一章中，你将了解 `pandas` 库更高级的功能。诸如 `groupby` 等复杂工具以及其他数据处理形式将得到详细讨论。



