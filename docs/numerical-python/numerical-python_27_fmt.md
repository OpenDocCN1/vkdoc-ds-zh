# 18. 数据输入与输出

在几乎所有科学计算和数据分析应用中，数据输入和输出都是必不可少的。这包括加载数据集，以及将结果持久化存储到磁盘文件或数据库中。因此，数据的导入与导出是计算工作流中的关键步骤。存储结构化与非结构化数据存在许多标准化格式。使用标准化格式的好处显而易见：你可以利用现有的库进行数据读写，从而节省时间和精力。在从事科学和技术计算的过程中，你很可能需要在与同事、同行合作时，或从设备、数据库等来源获取数据时，面对各种各样的数据格式。作为计算实践者，能够高效无缝地处理数据（无论其格式如何）至关重要。这正是本章专门探讨数据输入与输出主题的原因。

Python 对多种文件格式提供了良好的支持。事实上，针对最常见的格式，往往存在多种处理方案。本章将调查计算应用中常用的数据存储格式，并讨论每种格式适用的典型场景。我们还将介绍用于处理计算中常见数据格式的 Python 库和工具。

数据可以分为多个类别和类型。重要的类别包括结构化数据和非结构化数据；而数值则可以（例如）是类别型（有限值集）、序数型（具有有意义顺序的值）或数值型（连续或离散）。数值还具有类型，如字符串、整数、浮点数等。用于存储或传输数据的数据格式，理想情况下应能体现这些概念，以避免数据或元数据丢失，并且我们通常需要对数据的表示方式进行精细控制。

在计算应用中，我们大部分时间处理的是结构化数据，例如数组和表格数据。非结构化数据集的例子包括自由格式文本或包含非同类类型的嵌套列表。本章将重点介绍用于结构化数据的 CSV 系列格式和 HDF5 格式，并在本章末尾讨论 JSON 格式，它是一种轻量且灵活的格式，可用于存储简单和复杂的数据集，尤其适用于存储列表和字典。这种格式非常适合存储非结构化数据。我们还会简要探讨使用 `msgpack` 格式和 Python 内置的 `pickle` 格式将对象序列化为可存储数据的方法。

由于数据输入与输出在许多以数据为中心的计算应用中至关重要，因此涌现出了多个 Python 库，旨在简化并协助处理不同格式的数据以及数据的移动和转换。例如，Blaze 库（[`http://blaze.pydata.org/en/latest`](http://blaze.pydata.org/en/latest)）提供了一个高级接口，用于访问不同格式和不同来源的数据。本章我们主要关注用于读取特定类型文件格式的底层库，这些格式对于存储数值数据和非结构化数据集非常有用。不过，也鼓励感兴趣的读者探索诸如 Blaze 这样的高级库。

## 导入模块

在本章中，我们使用多个不同的库来处理不同类型的数据。具体来说，我们需要用到 NumPy 和 Pandas，按照惯例，分别将它们导入为 `np` 和 `pd`：

```
In [1]: import numpy as np
In [2]: import pandas as pd
```

我们还使用了 Python 标准库中的 `csv` 和 `json` 模块：

```
In [3]: import csv
In [4]: import json
```

为了处理用于数值数据的 HDF5 格式，我们使用了 `h5py` 和 `pytables` 库：

```
In [5]: import h5py
In [6]: import tables
```

最后，在将对象序列化为可存储数据的上下文中，我们将探讨 `pickle` 和 `msgpack` 库：

```
In [7]: import pickle
In [8]: import msgpack
```

## 逗号分隔值

逗号分隔值（CSV）是一种直观且定义松散^(⁷¹)的纯文本文件格式，它简单而高效，在存储表格数据方面非常普遍。在此格式中，每条记录存储为一行，记录的每个字段都用分隔符（例如逗号）隔开。可选地，每个字段可以用引号括起来，以允许包含分隔符的字符串值字段。此外，第一行有时用于存储列名，注释行也很常见。CSV 文件的示例如清单 18-1 所示。

```
# 2013-2014 / 常规赛 / 所有滑冰者 / 摘要 / 得分
排名,球员,球队,位置,出场数,进球,助攻,得分,正负值,受罚时间,强打进球,强打得分,短手进球,短手得分,制胜球,加时制胜球,射正数,射正率,场均上场时间,场均换人次数,争球成功率
1,西德尼·克罗斯比,匹兹堡企鹅,中锋,80,36,68,104,+18,46,11,38,0,0,5,1,259,13.9,21:58,24.0,52.5
2,瑞安·盖茨拉夫,安纳海姆小鸭,中锋,77,31,56,87,+28,31,5,23,0,0,7,1,204,15.2,21:17,25.2,49.0
3,克劳德·吉鲁,费城飞人,中锋,82,28,58,86,+7,46,7,37,0,0,7,1,223,12.6,20:26,25.1,52.9
4,泰勒·塞古因,达拉斯星,中锋,80,37,47,84,+16,18,11,25,0,0,8,0,294,12.6,19:20,23.4,41.5
5,科里·佩里,安纳海姆小鸭,右翼,81,43,39,82,+32,65,8,18,0,0,9,1,280,15.4,19:28,23.2,36.0
清单 18-1
带注释行、标题行以及混合数值和字符串值数据字段的 CSV 文件示例（数据来源：www.nhl.com）
```

CSV 有时也被认为是字符分隔值的缩写，这反映了 CSV 格式通常指代一系列使用不同分隔符分隔字段的格式。例如，Tab 字符常被用作逗号的替代，此时该格式有时被称为 TSV 而非 CSV。术语分隔符分隔值（DSV）有时也用来指代这类格式。

在 Python 中，有几种读写 CSV 格式数据的方法，每种方法都有不同的用例和优势。首先，Python 标准库包含一个名为 `csv` 的模块，用于读取 CSV 数据。要使用此模块，我们可以将文件句柄作为参数调用 `csv.reader` 函数。它会返回一个类实例，该实例可用作迭代器，将给定 CSV 文件中的行解析为 Python 字符串列表。例如，要将文件 `playerstats-2013-2014.csv`（如清单 18-1 所示）读入嵌套的字符串列表，我们可以使用：

```
In [9]: with open("playerstats-2013-2014.csv") as f:
...:     csvreader = csv.reader(f)
...:     rows = [fields for fields in csvreader]
In [10]: rows[1][1:6]
Out[10]: ['Player', 'Team', 'Pos', 'GP', 'G']
In [11]: rows[2][1:6]
Out[11]: ['Sidney Crosby', 'PIT', 'C', '80', '36']
```

请注意，默认情况下，解析后行中的每个字段都是字符串类型的，即使该字段表示数值，例如上例中的 80（比赛场次）或 36（进球数）。虽然 `csv` 模块提供了一种灵活的方式来定义自定义 CSV 读取器类，但此模块对于读取带字符串值字段的 CSV 文件最为方便。

在计算工作中，通常需要存储和加载包含数值的数组，例如向量和矩阵。NumPy 库为此提供了 `np.loadtxt` 和 `np.savetxt` 函数。这些函数接受多个参数来微调要读取或写入的 CSV 格式类型：例如，通过 `delimiter` 参数，我们可以选择用于分隔字段的字符；通过 `header` 和 `comments` 参数，可以分别指定标题行和附加在标题行之前的注释行。

例如，考虑使用 `np.savetxt` 将一个形状为 (100, 3) 的随机数数组保存到文件 `data.csv` 中。为了给数据提供一些上下文，我们还向文件添加了标题和注释行，并明确使用 `delimiter=","` 参数请求使用逗号字符作为字段分隔符（默认分隔符是空格字符）：

```
In [12]: data = np.random.randn(100, 3)
In [13]: np.savetxt("data.csv", data, delimiter=",", header="x,y,z",
...:            comments="# 随机 x, y, z 坐标\n")
In [14]: !head -n 5 data.csv
# 随机 x, y, z 坐标
x,y,z
1.652276634254504772e-01,9.522165919962696234e-01,4.659850998659530452e-01
8.699729536125471174e-01,1.187589118344758443e+00,1.788104702180680405e+00
-8.106725710122602013e-01,2.765616277935758482e-01,4.456864674903074919e-01
```

要将此格式的数据读回 NumPy 数组，我们可以使用 `np.loadtxt` 函数。它接受的参数与 `np.savetxt` 类似：具体而言，我们再次将 `delimiter` 参数设置为 `","`，以指示由逗号字符分隔的字段。我们还需要使用 `skiprows` 参数跳过文件中的前两行（注释行和标题行），因为它们不包含数值数据：

```
In [15]: data_load = np.loadtxt("data.csv", skiprows=2, delimiter=",")
```

结果是一个新的 NumPy 数组，与使用 `np.savetxt` 写入 `data.csv` 文件的原始数组相同：

```
In [16]: (data == data_load).all()
Out[16]: True
```

请注意，与 Python 标准库中 `csv` 模块的 CSV 读取器相比，NumPy 中的 `loadtxt` 函数默认将所有字段转换为数值，结果是一个具有数值 `dtype`（`float64`）的 NumPy 数组：

```
In [17]: data_load[1,:]
Out[17]: array([ 0.86997295,  1.18758912,  1.7881047 ])
In [18]: data_load.dtype
Out[18]: dtype('float64')
```

要使用 `np.loadtxt` 读取包含非数值数据的 CSV 文件——例如我们之前使用 Python 标准库读取的 `playerstats-2013-2014.csv` 文件——我们必须使用 `dtype` 参数显式设置结果数组的数据类型。如果我们尝试读取包含非数值值的 CSV 文件而不设置 `dtype`，则会出错：

```
In [19]: np.loadtxt("playerstats-2013-2014.csv", skiprows=2, delimiter=",")

ValueError: could not convert string to float: b'Sidney Crosby'
```

使用 `dtype=bytes`（或 `str` 或 `object`），我们得到一个包含未解析值的 NumPy 数组：

```
In [20]: data = np.loadtxt("playerstats-2013-2014.csv", skiprows=2, delimiter=",", dtype=bytes)
In [21]: data[0][1:6]
Out[21]: array([b'Sidney Crosby', b'PIT', b'C', b'80', b'36'], dtype='|S13')
```

或者，如果我们只想读取数值类型的列，我们可以使用 `usecols` 参数选择读取列的子集：

```
In [22]: np.loadtxt("playerstats-2013-2014.csv", skiprows=2, delimiter=",", usecols=[6,7,8])
Out[22]: array([[  68.,  104.,   18.],
               [  56.,   87.,   28.],
               [  58.,   86.,    7.],
               [  47.,   84.,   16.],
               [  39.,   82.,   32.]])
```

虽然 NumPy 的 `savetxt` 和 `loadtxt` 函数是可配置且灵活的 CSV 写入器和读取器，但它们最适用于纯数值数据。另一方面，Python 标准库模块 `csv` 最适用于包含字符串值数据的 CSV 文件。在 Python 中读取 CSV 文件的第三种方法是使用 Pandas 的 `read_csv` 函数。我们在第 12 章中已经看到过此函数的示例，当时我们使用它从 TSV 格式的数据文件创建 Pandas 数据帧。当读取同时包含数值和字符串值字段的 CSV 文件时，Pandas 中的 `read_csv` 函数非常方便，在大多数情况下，它会自动确定字段的类型并进行相应转换。例如，当使用 `read_csv` 读取 `playerstats-2013-2014.csv` 文件时，我们得到一个 Pandas 数据帧，其中所有字段都被解析为具有合适类型的列：

| `排名` | `球员` | `出场数` | `进球` | `助攻` | `得分` |
| --- | --- | --- | --- | --- | --- |
| `1` | `西德尼·克罗斯比` | `80` | `36` | `68` | `104` |
| `2` | `瑞安·盖茨拉夫` | `77` | `31` | `56` | `87` |
| `3` | `克劳德·吉鲁` | `82` | `28` | `58` | `86` |
| `4` | `泰勒·塞古因` | `80` | `37` | `47` | `84` |
| `5` | `科里·佩里` | `81` | `43` | `39` | `82` |

```python
In [23]: df = pd.read_csv("playerstats-2013-2014.csv", skiprows=1)
In [24]: df = df.set_index("Rank")
In [25]: df[["Player", "GP", "G", "A", "P"]]
Out[25]:
```

利用 `DataFrame` 实例 `df` 的 `info` 方法，我们可以清晰地看到每一列被转换成了哪种类型（为简洁起见，此处输出已截断）：

```python
In [26]: df.info()

Int64Index: 5 entries, 1 to 5
Data columns (total 20 columns):
Player      5 non-null object
Team        5 non-null object
Pos         5 non-null object
GP          5 non-null int64
G           5 non-null int64
...
S           5 non-null int64
S%          5 non-null float64
TOI/GP      5 non-null object
Shift/GP    5 non-null float64
FO%         5 non-null float64
dtypes: float64(3), int64(13), object(4)
memory usage: 840.0+ bytes
```

数据框也可以通过 `DataFrame` 对象的 `to_csv` 方法写入 CSV 文件：

```python
In [27]: df[["Player", "GP", "G", "A", "P"]].to_csv("playerstats-2013-2014-subset.csv")
In [28]: !head -n 5 playerstats-2013-2014-subset.csv
Rank,Player,GP,G,A,P
1,Sidney Crosby,80,36,68,104
2,Ryan Getzlaf,77,31,56,87
3,Claude Giroux,82,28,58,86
4,Tyler Seguin,80,37,47,84
```

Python 标准库、NumPy 和 Pandas 的结合，为读写各种类型的 CSV 文件提供了强大的工具箱。然而，尽管 CSV 文件在处理表格数据时方便有效，但其格式也存在明显的缺陷。首先，它只能用于存储一维或二维数组，并且不包含有助于解释数据的元数据。其次，无论是存储还是读写，其效率都不高；此外，它无法在每个文件中存储多个数组，即使多个数组密切相关，也需要多个文件。因此，CSV 应仅用于简单的数据集。在下一节中，我们将介绍 HDF5 文件格式，该格式旨在高效存储数值数据，并克服 CSV 及相关格式等简单数据格式的所有缺点。

## HDF5

分层数据格式 5（HDF5）是一种用于存储数值数据的格式。它由非营利组织 HDF 集团开发，并在 BSD 开源许可下提供。HDF5 格式于 1998 年发布，其设计和实现旨在高效处理大型数据集，包括支持高性能并行 I/O。因此，HDF5 格式适用于分布式高性能超级计算机和集群，可用于存储和操作 TB 级甚至更大的数据集。然而，HDF5 的美妙之处在于它同样适用于小型数据集。因此，它是一种真正通用的格式，是计算从业者的宝贵工具。

# HDF5 文件格式与 Python 库

该格式的分层特性允许使用类似于文件系统的层次结构在文件内组织数据集。HDF5 文件中用于实体的术语是`组`和`数据集`，对应于文件系统中的目录和文件。HDF5 文件中的组可以嵌套以形成树形结构，因此格式名称中带有`分层`一词。HDF5 文件中的数据集是一个同质数组，具有一定的维度和特定类型的元素。HDF5 类型系统支持所有标准的基本数据类型，并允许定义自定义的复合数据类型。HDF5 文件中的组和数据集还可以拥有`属性`，这些属性可用于存储关于组和数据集的元数据。属性本身可以有不同的类型，例如数字或字符串值。

除了文件格式本身，HDF 集团还提供了该格式的库和参考实现。主库用 C 语言编写，其 C API 的包装器可用于多种编程语言。用于从 HDF5 文件访问数据的 HDF5 库对部分读写操作有复杂支持，可用于访问整个数据集的一小部分。这是一个强大的功能，使得可以对大于计算机内存的数据集进行计算^(⁷³)。HDF5 格式是一种成熟的文件格式，在不同平台和计算环境中得到广泛支持。这也使得 HDF5 成为长期存储数据的合适选择。作为一个数据存储平台，HDF5 为许多问题提供了解决方案：跨平台存储、高效的 I/O、可扩展至非常大的数据文件的存储，以及可用于注释和描述文件中组和数据集以使数据自描述的元数据系统（属性）。总的来说，这些特性使 HDF5 成为计算工作的绝佳工具。

## Python 库

Python 有两个用于使用 HDF5 文件的库：`h5py` 和 `PyTables`。这两个库使用 HDF5 的方法不同，熟悉这两个库非常值得。`h5py` 库提供了一个相对接近基本 HDF5 概念的 API，重点关注组和数据集。它为访问数据集提供了受 NumPy 启发的 API，这使得熟悉 NumPy 的人使用起来非常直观。

### h5py

`h5py` 库为 HDF5 文件格式提供了 Python 风格的接口，并为其数据集提供了类似 NumPy 的接口。有关该项目的更多信息，包括其官方文档，请访问其网站 [`www.h5py.org`](http://www.h5py.org)。在撰写本文时，该库的最新版本是 2.7.1。

`PyTables` 库提供了基于 HDF5 格式的更高级数据抽象，提供了类似数据库的功能，例如具有易于自定义数据类型的表。它还允许像数据库一样查询数据集，并使用高级索引功能。

### PyTables

`PyTables` 库在 HDF5 之上提供了类似数据库的数据模型。有关该项目及其文档的更多信息，请访问网站 [`http://pytables.github.io`](http://pytables.github.io)。在撰写本文时，`PyTables` 的最新版本是 3.4.3。

在接下来的两节中，我们将更详细地探讨如何使用 `h5py` 和 `PyTables` 库通过 HDF5 文件读写数值数据。

## h5py 详解

我们首先来探索 `h5py` 库。`h5py` 的 API 出奇地简单且使用起来令人愉快，同时又功能齐全。这是通过巧妙使用 Python 惯用语法（如字典和 NumPy 数组语义）实现的。表 18-1 总结了 `h5py` 库中的基本对象和方法。接下来，我们将通过一系列示例探索如何使用这些方法。

**表 18-1 h5py API 中主要对象和方法的总结**

| 对象 | 方法/属性 | 描述 |
| --- | --- | --- |
| `h5py.File` | `__init__(name, mode, ...)` | 打开一个现有的 HDF5 文件，或创建一个新的文件，文件名为 `name`。根据 `mode` 参数的值，文件可以以只读或读写模式打开（参见正文）。 |
| | `flush()` | 将缓冲区写入文件。 |
| | `close()` | 关闭一个打开的 HDF5 文件。 |
| `h5py.File, h5py.Group` | `create_group(name)` | 在当前组内创建一个名为 `name`（可以是路径）的新组。 |
| | `create_dataset(name, data=..., shape=..., dtype=..., ...)` | 创建一个新的数据集。 |
| | `[]` 字典语法 | 访问组内的项目（组和数据集）。 |
| `h5py.Dataset` | `dtype` | 数据类型。 |
| | `shape` | 数据集的形状（维度）。 |
| | `value` | 数据集底层数据的完整数组。 |
| | `[]` 数组语法 | 访问数据集中数据的元素或子集。 |
| `h5py.File, h5py.Group, h5py.Dataset` | `name` | 对象在 HDF5 文件层次结构中的名称（路径）。 |
| | `attrs` | 类似字典的属性访问。 |

### 文件操作

我们首先了解如何使用 `h5py.File` 对象打开现有的 HDF5 文件以及创建新的 HDF5 文件。该对象的初始化器仅将文件名作为必需参数，但我们通常还需要指定 `mode` 参数，通过它我们可以选择以只读或读写模式打开文件，以及打开文件时是否应截断文件。`mode` 参数接受与 Python 内置函数 `open` 类似的字符串值：`"r"` 用于只读（文件必须存在），`"r+"` 用于读写（文件必须存在），`"w"` 用于创建新文件（如果文件存在则截断），`"w-"` 用于创建新文件（如果文件存在则报错），`"a"` 用于读写（如果文件存在则打开，否则创建）。例如，要以读写模式创建一个新文件，我们可以使用

```
In [29]: f = h5py.File("data.h5", mode="w")
```

结果是一个文件句柄，此处赋值给变量 `f`，我们可以用它来访问文件并向其中添加内容。给定一个文件句柄，我们可以使用 `mode` 属性查看它是以何种模式打开的：

```
In [30]: f.mode
Out[30]: 'r+'
```

请注意，即使我们以 `"w"` 模式打开了文件，一旦文件被打开，它要么是只读模式（`"r"`），要么是读写模式（`"r+"`）。其他可以使用 HDF5 文件对象执行的文件级操作包括：使用 `flush` 方法刷新包含尚未写入文件的数据的缓冲区，以及使用 `close` 方法关闭文件：

```
In [31]: f.flush()
In [32]: f.close()
```

### 组操作

`File` 对象在表示 HDF5 文件句柄的同时，也代表称为`根组`的 HDF5 组对象。组的名称可以通过组对象的 `name` 属性访问。该名称采用路径形式，类似于文件系统中的路径，指定了该组在文件层级结构中的存储位置。根组的名称是 `"/"`：

```
In [33]: f = h5py.File("data.h5", "w")
In [34]: f.name
Out[34]: '/'
```

组对象具有 `create_group` 方法，用于在现有组中创建新组。使用此方法创建的新组将成为调用 `create_group` 方法的组实例的子组：

```
In [35]: grp1 = f.create_group("experiment1")
In [36]: grp1.name
Out[36]: '/experiment1'
```

这里，组 `experiment1` 是根组的一个子组，因此它在层级结构中的名称和路径是 `/experiment1`。创建新组时，其直接父组不一定必须事先存在。例如，要创建一个新组 `/experiment2/measurement`，我们可以直接使用根组的 `create_group` 方法，*而不必*先显式创建 `experiment2` 组。中间组会自动创建。

```
In [37]: grp2_meas = f.create_group("experiment2/measurement")
In [38]: grp2_meas.name
Out[38]: '/experiment2/measurement'
In [39]: grp2_sim = f.create_group("experiment2/simulation")
In [40]: grp2_sim.name
Out[40]: '/experiment2/simulation'
```

可以使用字典风格的接口来探索 HDF5 文件的组层次结构。要检索具有给定路径名称的组，我们可以从其祖先组（通常是根节点）之一执行类似字典的查找：

```
In [41]: f["/experiment1"]
Out[41]: 
In [42]: f["/experiment2/simulation"]
Out[42]: 
```

同样的字典查找方式也适用于子组（不仅仅限于根节点）：

```
In [43]: grp_experiment2 = f["/experiment2"]
In [44]: grp_experiment2['simulation']
Out[44]: 
```

# HDF5 文件的组遍历与数据集操作

`keys` 方法返回一个迭代器，用于遍历组内的子组和数据集的名称；`items` 方法返回一个迭代器，用于遍历组内每个实体的 `(name, value)` 元组。这些方法可用于以编程方式遍历组的层次结构。

```
In [45]: list(f.keys())
Out[45]: ['experiment1', 'experiment2']
In [46]: list(f.items())
Out[46]: [('experiment1', ),
('experiment2', )]
```

## 使用 visit 和 visititems 方法

要遍历 HDF5 文件中的组层次结构，我们还可以使用 `visit` 方法，该方法接受一个函数作为参数，并对文件层次结构中的每个实体调用该函数，同时传入该实体的名称：

```
In [47]: f.visit(lambda x: print(x))
experiment1
experiment2
experiment2/measurement
experiment2/simulation
```

或者使用 `visititems` 方法，该方法执行相同的操作，不同之处在于它在调用函数时同时传入项目名称和项目本身作为参数：

```
In [48]: f.visititems(lambda name, item: print(name, item))
experiment1 
experiment2 
experiment2/experiment 
experiment2/simulation 
```

## 字典风格的操作与外部工具

与 Python 字典的语义一致，我们还可以使用 Python 关键字 `in` 对 `Group` 对象进行集合成员测试操作：

```
In [49]: "experiment1" in f
Out[49]: True
In [50]: "simulation" in f["experiment2"]
Out[50]: True
In [51]: "experiment3" in f
Out[51]: False
```

通过使用 `visit` 和 `visititems` 方法，以及字典风格的方法 `keys` 和 `items`，我们可以轻松探索 HDF5 文件的结构和内容，即使我们事先不知道它包含什么以及其中的数据如何组织。能够方便地探索 HDF5 是该格式可用性的一个重要方面。此外，还有一些用于探索 HDF5 文件内容的非 Python 外部工具，这些工具在处理此类文件时通常很有用。特别是，`h5ls` 命令行工具对于快速检查 HDF5 文件的内容非常方便：

```
In [52]: f.flush()
In [53]: !h5ls -r data.h5
/                        Group
/experiment1                 Group
/experiment2                 Group
/experiment2/measurement     Group
/experiment2/simulation      Group
```

这里我们使用了 `h5ls` 程序的 `-r` 标志，以递归方式显示文件中的所有项目。`h5ls` 程序是一个名为 `hdf5-tools` 的软件包提供的系列 HDF5 实用工具之一（另请参阅 `h5stat`、`h5copy`、`h5diff` 等）。尽管这些不是 Python 工具，但它们在处理 HDF5 文件（包括在 Python 环境中）时非常有用。

# HDF5 数据集

既然我们已经探讨了如何在 HDF5 文件中创建和访问组，现在是时候看看如何存储数据集了。毕竟，存储数值数据是 HDF5 格式的主要目的。使用 `h5py` 在 HDF5 文件中创建数据集主要有两种方法。创建数据集最简单的方法是使用字典索引语法，直接将一个 NumPy 数组分配给 HDF5 组中的一个条目。第二种方法是使用 `create_dataset` 方法创建一个空数据集，我们将在本节后面看到相关示例。

## 创建与验证数据集

例如，要将两个 NumPy 数组 `array1` 和 `meas1` 分别存储到根组和 `experiment2/measurement` 组中，我们可以使用：

```
In [54]: array1 = np.arange(10)
In [55]: meas1 = np.random.randn(100, 100)
In [56]: f["array1"] = array1
In [57]: f["/experiment2/measurement/meas1"] = meas1
```

为了验证分配给 NumPy 数组的数据集已添加到文件中，我们使用 `visititems` 方法遍历文件层次结构：

```
In [58]: f.visititems(lambda name, value: print(name, value))
array1 
experiment1 
experiment2 
experiment2/measurement 
experiment2/measurement/meas1 
experiment2/simulation 
```

我们看到，`array1` 和 `meas1` 数据集确实已添加到文件中。请注意，在分配中用作字典键的路径决定了数据集在文件中的位置。

## 访问与检索数据集

要检索数据集，我们可以使用与检索组相同的类字典语法。例如，要检索存储在根组中的 `array1` 数据集，我们可以使用 `f["array1"]`：

```
In [59]: ds = f["array1"]
In [60]: ds
Out[60]: 
```

结果是一个 `Dataset` 对象，而不是我们分配给 `array1` 条目的 NumPy 数组。`Dataset` 对象是 HDF5 中底层数据的代理。与 NumPy 数组一样，`Dataset` 对象具有多个描述数据集的属性，包括 `name`、`dtype` 和 `shape`。它还具有返回数据集长度的 `len` 方法：

```
In [61]: ds.name
Out[61]: '/array1'
In [62]: ds.dtype
Out[62]: dtype('int64')
In [63]: ds.shape
Out[63]: (10,)
In [64]: ds.len()
Out[64]: 10
```

数据集的实际数据可以通过例如 `value` 属性访问。这将整个数据集作为 NumPy 数组返回，这里等同于我们分配给 `array1` 数据集的数组。

```
In [65]: ds.value
Out[65]: array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

要访问组层次结构中更深层的数据集，我们可以使用类似文件系统的路径名。例如，要检索 `experiment2/measurement` 组中的 `meas1` 数据集，我们可以使用：

```
In [66]: ds = f["experiment2/measurement/meas1"]
In [67]: ds
Out[67]: 
```

我们再次得到一个 `Dataset` 对象，可以使用我们之前介绍的对象属性来检查其基本属性：

```
In [68]: ds.dtype
Out[68]: dtype('float64')
In [69]: ds.shape
Out[69]: (100, 100)
```

请注意，此数据集的数据类型是 `float64`，而对于数据集 `array1`，数据类型是 `int64`。此类型信息源自分配给这两个数据集的 NumPy 数组。这里我们同样可以使用 `value` 属性将数组作为 NumPy 数组检索。相同操作的另一种语法是使用带有省略号表示法的方括号索引：`ds[...]`。

```
In [70]: data_full = ds[...]
In [71]: type(data_full)
Out[71]: numpy.ndarray
In [72]: data_full.shape
Out[72]: (100, 100)
```

## 数组索引与切片

这是类似 NumPy 数组索引的一个示例。`Dataset` 对象支持 NumPy 中使用的大多数索引和切片类型，这提供了一种强大且灵活的方法来从文件中部分读取数据。例如，要仅检索 `meas1` 数据集的第一列，我们可以使用：

```
In [73]: data_col = ds[:, 0]
In [74]: data_col.shape
Out[74]: (100,)
```

结果是一个包含 100 个元素的数组，对应于数据集中的第一列。请注意，此切片是在 HDF5 库中执行的，而不是在 NumPy 中，因此在此示例中，仅从文件中读取了 100 个元素并存储在生成的 NumPy 数组中，而无需将整个数据集加载到内存中。在处理无法放入内存的大型数据集时，这是一个重要特性。

例如，`Dataset` 对象还支持跨步索引：

## 索引和切片

```
In [75]: ds[10:20:3, 10:20:3] # 步长为 3
Out[75]: array([[-0.22321057, -0.61989199,  0.78215645,  0.73774187],
[-1.03331515,  2.54190817, -0.24812478, -2.49677693],
[ 0.17010011,  1.88589248,  1.91401249, -0.63430569],
[ 0.4600099 , -1.3242449 ,  0.41821078,  1.47514922]])
```

以及“花式索引”，其中为数组的某个维度给出索引列表（不适用于多个索引）：

```
In [76]: ds[[1,2,3], :].shape
Out[76]: (3, 100)
```

我们还可以使用布尔索引，其中使用一个布尔值的 NumPy 数组来索引一个 `Dataset`。例如，要筛选出第一列 (`ds[:, 0]`) 值大于 2 的每一行的前五列（在第二个轴上索引 `:5`），我们可以使用布尔掩码 `ds[:, 0] > 2` 来索引数据集：

```python
In [77]: mask = ds[:, 0] > 2
In [78]: mask.shape, mask.dtype
Out[78]: ((100,), dtype('bool'))
In [79]: ds[mask, :5]
Out[79]: array([[ 2.1224865 ,  0.70447132, -1.71659513,  1.43759445, -0.61080907],
[ 2.11780508, -0.2100993 ,  1.06262836, -0.46637199,  0.02769476],
[ 2.41192679, -0.30818179, -0.31518842, -1.78274309, -0.80931757],
[ 2.10030227,  0.14629889,  0.78511191, -0.19338282,  0.28372485]])
```

由于 `Dataset` 对象使用 NumPy 的索引和切片语法来选择底层数据的子集，因此对于熟悉 NumPy 的人来说，使用 `h5py` 在 Python 中处理大型 HDF5 数据集会非常自然。还要记住，对于大文件，在 `Dataset` 对象上进行索引切片与在可通过 `value` 属性访问的 NumPy 数组上进行索引切片有很大区别，因为前者避免将整个数据集加载到内存中。

## 创建数据集

到目前为止，我们已经了解了如何通过将数据显式分配给组对象中的条目来在 HDF5 文件中创建数据集。我们还可以使用 `create_dataset` 方法显式创建数据集。它将要创建的新数据集的名称作为第一个参数，我们可以使用 `data` 参数设置新数据集的数据，或者通过设置 `shape` 参数创建一个空数组。例如，除了赋值 `f["array2"] = np.random.randint(10, size=10)`，我们还可以使用 `create_dataset` 方法：

```python
In [80]: ds = f.create_dataset("array2", data=np.random.randint(10, size=10))
In [81]: ds
Out[81]:
In [82]: ds.value
Out[82]: array([2, 2, 3, 3, 6, 6, 4, 8, 0, 0])
```

当显式调用 `create_dataset` 方法时，我们可以更精细地控制结果数据集的属性。例如，我们可以使用 `dtype` 参数显式设置数据集的数据类型，使用 `compress` 参数选择压缩方法，使用 `chunks` 参数设置块大小，以及使用 `maxsize` 参数为可调整大小的数据集设置允许的最大数组大小。还有与 `Dataset` 对象相关的许多其他高级功能。有关详细信息，请参阅 `create_dataset` 的文档字符串。

当通过指定 `shape` 参数而不是提供一个数组来初始化数据集来创建空数组时，我们还可以使用 `fillvalue` 参数来设置数据集的默认值。例如，要创建一个形状为 `(5, 5)` 且默认值为 `-1` 的空数据集，我们可以使用：

```python
In [83]: ds = f.create_dataset("/experiment2/simulation/data1", shape=(5, 5), fillvalue=-1)
In [84]: ds
Out[84]:
In [85]: ds.value
Out[85]: array([[-1., -1., -1., -1., -1.],
       [-1., -1., -1., -1., -1.],
       [-1., -1., -1., -1., -1.],
       [-1., -1., -1., -1., -1.],
       [-1., -1., -1., -1., -1.]], dtype=float32)
```

## 高级操作与管理

HDF5 对于空数据集的磁盘使用非常智能，不会存储超出必要的数据，特别是当我们使用 `compression` 参数选择压缩方法时。有多种可用的压缩方法，例如 `'gzip'`。使用数据集压缩，我们可以创建一个非常大的数据集，并逐步用数据填充它们，例如，当测量结果或计算结果可用时，无需一开始就浪费大量存储空间。例如，让我们在组 `experiment1/simulation` 中创建一个形状为 `(5000, 5000, 5000)` 的 `data1` 大数据集：

```python
In [86]: ds = f.create_dataset("/experiment1/simulation/data1", shape=(5000, 5000, 5000), fillvalue=0, compression="gzip")
In [87]: ds
Out[87]:
```

最初，此数据集既不占用内存也不占用磁盘空间，直到我们开始用数据填充它。要为数据集赋值，我们可以再次使用类似 NumPy 的索引语法，并为数据集中的特定元素或通过切片语法选择的子集赋值：

```python
In [88]: ds[:, 0, 0] = np.random.rand(5000)
In [89]: ds[1, :, 0] += np.random.rand(5000)
In [90]: ds[:2, :5, 0]
Out[90]: array([[ 0.67240328, 0.        , 0.        , 0.        , 0.        ],
       [ 0.99613971, 0.48227152, 0.48904559, 0.78807044, 0.62100351]], dtype=float32)
```

请注意，未赋值的元素会设置为创建数组时指定的 `fillvalue` 值。如果我们不知道数据集的填充值是什么，可以通过查看 `Dataset` 对象的 `fillvalue` 属性来找出：

```python
In [91]: ds.fillvalue
Out[91]: 0.0
```

要查看新创建的数据集确实存储在我们打算分配的组中，我们可以再次使用 `visititems` 方法来列出 `experiment1` 组的内容：

```python
In [92]: f["experiment1"].visititems(lambda name, value: print(name, value))
simulation
simulation/data1
```

虽然数据集 `experiment1/simulation/data1` 非常大（4 × 5000³ 字节 ~ 465 Gb），但由于我们还没有用大量数据填充它，HDF5 文件仍然不会占用太多磁盘空间（仅约 357 Kb）：

```python
In [93]: f.flush()
In [94]: f.filename
Out[94]: 'data.h5'
In [95]: !ls -lh data.h5
-rw-r--r--@ 1 rob  staff   357K Apr  5 18:48 data.h5
```

到目前为止，我们已经了解了如何在 HDF5 文件中创建组和数据集。当然，有时也需要从文件中删除项目。使用 `h5py`，我们可以使用 Python 的 `del` 关键字从组中删除项目，这同样遵循 Python 字典的语义：

```python
In [96]: del f["/experiment1/simulation/data1"]
In [97]: f["experiment1"].visititems(lambda name, value: print(name, value))
simulation
```

### 属性

# HDF5 属性与 PyTables 库

属性是 HDF5 格式的一个组成部分，使其成为通过元数据注释数据和提供自描述数据的极佳格式。例如，在存储实验数据时，通常需要将外部参数和条件与观测数据一起记录。同样，在计算机模拟中，通常需要将额外的模型或模拟参数与生成的模拟结果一起存储。在所有这些情况下，最好的解决方案是确保所需的额外参数作为元数据与主要数据集一起存储。

HDF5 格式通过使用属性来支持这种类型的元数据。任意数量的属性可以附加到 HDF5 文件中的每个组和数据集。使用 `h5py` 库，可以通过类似字典的接口访问属性，就像访问组一样。`Group` 和 `Dataset` 对象的 Python 属性 `attrs` 用于访问 HDF5 属性：

```python
In [98]: f.attrs
Out[98]: 
```

要创建属性，我们只需为目标对象的 `attrs` 字典赋值即可。例如，为根组创建一个 `description` 属性，我们可以使用：

```python
In [99]: f.attrs["description"] = "实验与模拟的结果集"
```

类似地，为 `experiment1` 和 `experiment2` 组添加 `date` 属性：

```python
In [100]: f["experiment1"].attrs["date"] = "2015-1-1"
In [101]: f["experiment2"].attrs["date"] = "2015-1-2"
```

我们还可以直接向数据集（不仅仅是组）添加属性：

```python
In [102]: f["experiment2/simulation/data1"].attrs["k"] = 1.5
In [103]: f["experiment2/simulation/data1"].attrs["T"] = 1000
```

与组类似，我们可以使用 `Attribute` 对象的 `keys` 和 `items` 方法来获取对其包含的属性的迭代器：

```python
In [104]: list(f["experiment1"].attrs.keys())
Out[104]: ['date']
In [105]: list(f["experiment2/simulation/data1"].attrs.items())
Out[105]: [('k', 1.5), ('T', 1000)]
```

属性是否存在可以使用 Python 的 `in` 运算符来测试，这符合 Python 字典的语义：

```python
In [106]: "T" in f["experiment2/simulation/data1"].attrs
Out[106]: True
```

要删除现有属性，我们可以使用 `del` 关键字：

```python
In [107]: del f["experiment2/simulation/data1"].attrs["T"]
In [108]: "T" in f["experiment2/simulation"].attrs
Out[108]: False
```

HDF5 组和数据集的属性适合与实际数据集一起存储元数据。大量使用属性有助于为数据提供上下文，而数据通常需要具备上下文才能发挥作用。

## PyTables

`PyTables` 库为 Python 提供了另一种基于 HDF5 的接口。该库的核心是通过 HDF5 格式实现的高层级表格数据模型，当然它也可以像 `h5py` 库一样创建和读取通用的 HDF5 组与数据集。这里我们重点介绍表格数据模型，以补充上一节讨论的 `h5py` 库。我们将使用本章前面提到的 NHL 球员统计数据数据集来演示 `PyTables` 表格对象的使用，并基于该数据集从 Pandas 数据框构建一个 `PyTables` 表格。因此，我们首先使用 `read_csv` 函数将数据集读入 `DataFrame` 对象：

```python
In [109]: df = pd.read_csv("playerstats-2013-2014.csv", skiprows=1)
...: df = df.set_index("Rank")
```

接下来，我们使用 `tables.open_file` 函数^(⁷⁴)创建一个新的 PyTables HDF5 文件句柄。该函数的第一个参数是文件名，第二个可选参数是文件模式。返回结果是一个 PyTables HDF5 文件句柄（此处赋值给变量 `f`）：

```python
In [110]: f = tables.open_file("playerstats-2013-2014.h5", mode="w")
```

与 `h5py` 库类似，我们可以通过文件句柄对象的 `create_group` 方法创建 HDF5 组。该方法第一个参数是父组的路径，第二个参数是组名，还可选地接受 `title` 参数，用于为组设置描述性的 HDF5 属性。

```python
In [111]: grp = f.create_group("/", "season_2013_2014",
...:                      title="NHL player statistics for the 2013/2014 season")
In [112]: grp
Out[112]: /season_2013_2014 (Group) 'NHL player statistics for the 2013/2014 season'
children := []
```

与 `h5py` 库不同的是，`PyTables` 中的文件句柄对象并不代表 HDF5 文件中的根组。要访问根节点，我们必须使用文件句柄对象的 `root` 属性：

```python
In [113]: f.root
Out[113]: / (RootGroup) "
children := ['season_2013_2014' (Group)]
```

`PyTables` 库的一个出色特性是它能利用 HDF5 的类结构复合数据类型，轻松创建混合列类型的表格。在 `PyTables` 中定义此类表格数据结构的最简单方法是创建一个继承自 `tables.IsDescription` 类的子类，其中应包含由 `tables` 库中数据类型表示构成的字段。例如，要为球员统计数据数据集创建表格结构的规范，我们可以使用：

```python
In [114]: class PlayerStat(tables.IsDescription):
...:     player = tables.StringCol(20, dflt="")
...:     position = tables.StringCol(1, dflt="C")
...:     games_played = tables.UInt8Col(dflt=0)
...:     points = tables.UInt16Col(dflt=0)
...:     goals = tables.UInt16Col(dflt=0)
...:     assists = tables.UInt16Col(dflt=0)
...:     shooting_percentage = tables.Float64Col(dflt=0.0)
...:     shifts_per_game_played = tables.Float64Col(dflt=0.0)
```

这里，`PlayerStat` 类表示一个包含八个列的表格结构：前两列是固定长度字符串（`tables.StringCol`），接下来的四列是无符号整数（`tables.UInt8Col` 和 `tables.UInt16Col`，分别为 8 位和 16 位），最后两列是浮点类型（`tables.Float64Col`）。数据类型对象中的可选参数 `dflt` 用于指定字段的默认值。一旦通过此类形式定义了表格结构，我们就可以使用 `create_table` 方法在 HDF5 文件中创建实际的表格。该方法的第一个参数是组对象或父节点的路径，第二个参数是表格名称，第三个参数是表格规范类，第四个可选参数是表格标题（存储为对应数据集的 HDF5 属性）：

```python
In [115]: top30_table = f.create_table(grp, 'top30', PlayerStat, "Top 30 point leaders")
```

要向表格中插入数据，我们可以使用表格对象的 `row` 属性获取一个 `Row` 访问器类，该类可像字典一样用于填充行的值。当行对象完全初始化后，可以使用 `append` 方法将行实际插入表格：

## PyTables 表格操作与查询

以下代码展示了如何使用 PyTables 的表格接口填充数据。

```
In [116]: playerstat = top30_table.row
In [117]: for index, row_series in df.iterrows():
...:     playerstat["player"] = row_series["Player"]
...:     playerstat["position"] = row_series["Pos"]
...:     playerstat["games_played"] = row_series["GP"]
...:     playerstat["points"] = row_series["P"]
...:     playerstat["goals"] = row_series["G"]
...:     playerstat["assists"] = row_series["A"]
...:     playerstat["shooting_percentage"] = row_series["S%"]
...:     playerstat["shifts_per_game_played"] = row_series["Shift/GP"]
...:     playerstat.append()
```

`flush`方法强制将表格数据写入文件：

```
In [118]: top30_table.flush()
```

要访问表格中的数据，我们可以使用`cols`属性将列作为 `NumPy` 数组获取：

```
In [119]: top30_table.cols.player[:5]
Out[119]: array([b'Sidney Crosby', b'Ryan Getzlaf', b'Claude Giroux',
b'Tyler Seguin', b'Corey Perry'], dtype='|S20')
In [120]: top30_table.cols.points[:5]
Out[120]: array([104,  87,  86,  84,  82], dtype=uint16)
```

要按行方式访问数据，我们可以使用`iterrows`方法创建遍历表格所有行的迭代器。这里我们使用此方法循环所有行并打印到标准输出（为简洁起见，输出已截断）：

```
In [121]: def print_playerstat(row):
...:     print("%20s\t%s\t%s\t%s" %
...:           (row["player"].decode('UTF-8'), row["points"],
...:            row["goals"], row["assists"]))
In [122]: for row in top30_table.iterrows():
...:     print_playerstat(row)
Sidney Crosby    104    36    68
Ryan Getzlaf        87    31    56
Claude Giroux       86    28    58
Tyler Seguin        84    37    47
...
Jaromir Jagr        67    24    43
John Tavares        66    24    42
Jason Spezza        66    23    43
Jordan Eberle       65    28    37
```

`PyTables` 表格接口最强大的功能之一是通过查询从底层 HDF5 中有选择地提取行。例如，`where`方法允许我们传递一个以表格列为条件的表达式字符串，由 `PyTables` 用于过滤行：

```
In [123]: for row in top30_table.where("(points > 75) & (points <= 80)"):
...:     print_playerstat(row)
Phil Kessel        80    37    43
Taylor Hall        80    27    53
Alex Ovechkin      79    51    28
Joe Pavelski       79    41    38
Jamie Benn         79    34    45
Nicklas Backstrom  79    18    61
Patrick Sharp      78    34    44
Joe Thornton       76    11    65
```

使用`where`方法，我们还可以定义涉及多个列的条件：

```
In [124]: for row in top30_table.where("(goals > 40) & (points < 80)"):
...:     print_playerstat(row)
Alex Ovechkin     79    51    28
Joe Pavelski      79    41    38
```

此功能允许我们以类似数据库的方式查询表格。虽然对于像当前这样的小数据集，我们完全可以直接在内存中使用 `Pandas` 数据框执行此类操作，但请记住，HDF5 文件存储在磁盘上，而`PyTables`库对 I/O 的高效利用使我们能够处理无法装入内存的极大数据集，这对于整个数据集来说，是 `NumPy` 或 `Pandas` 无法实现的。

在结束本节之前，让我们检查一下包含我们刚刚创建的 `PyTables` 表格的 HDF5 文件的最终结构。

```
In [125]: f
Out[125]: File(filename=playerstats-2013-2014.h5, title=", mode="w", root_uep='/', filters=Filters(complevel=0, shuffle=False, fletcher32=False, least_significant_digit=None))
/ (RootGroup) " /season_2013_2014 (Group) '2013/2014 赛季 NHL 球员统计数据'
/season_2013_2014/top30 (Table(30,)) '得分前 30 的领跑者'
description := {
"assists": UInt16Col(shape=(), dflt=0, pos=0),
"games_played": UInt8Col(shape=(), dflt=0, pos=1),
"goals": UInt16Col(shape=(), dflt=0, pos=2),
"player": StringCol(itemsize=20, shape=(), dflt=b", pos=3),
"points": UInt16Col(shape=(), dflt=0, pos=4),
"position": StringCol(itemsize=1, shape=(), dflt=b'C', pos=5),
"shifts_per_game_played": Float64Col(shape=(), dflt=0.0, pos=6),
"shooting_percentage": Float64Col(shape=(), dflt=0.0, pos=7)}
byteorder := 'little'
chunkshape := (1489,)
```

从 `PyTables` 文件句柄及其包含的 HDF5 文件层次结构的字符串表示中，我们可以看到 `PyTables` 库创建了一个数据集`/season_2013_2014/top30`，它使用了一种复杂的复合数据类型，该类型是根据我们之前创建的`PlayerStat`对象中的规范生成的。最后，当我们在文件中完成对数据集的修改后，可以使用`flush`方法刷新其缓冲区并强制写入文件；当我们完成对文件的操作后，可以使用`close`方法关闭文件：

```
In [126]: f.flush()
In [127]: f.close()
```

虽然这里我们没有涵盖其他类型的数据集（例如常规的同构数组），但值得一提的是 `PyTables` 库也支持这些类型的数据结构。例如，我们可以使用`create_array`、`create_carray`和`create_earray`分别构建固定大小数组、分块数组和可扩展数组。有关如何使用这些数据结构的更多信息，请参阅相应的文档字符串。

## Pandas HDFStore

使用 Python 将数据存储在 HDF5 文件中的第三种方法是使用 `Pandas` 中的`HDFStore`对象。它可用于将数据框和其他 `Pandas` 对象持久化存储在 HDF5 文件中。要在 `Pandas` 中使用此功能，必须安装 `PyTables` 库。我们可以通过将文件名传递给其初始化器来创建一个`HDFStore`对象。结果是一个`HDFStore`对象，它可以作为一个字典使用，我们可以将 `Pandas` 的`DataFrame`实例分配给它，从而将其存储到 HDF5 文件中：

# HDFStore

`HDFStore`对象的行为类似于常规的Python字典，例如，我们可以通过调用`keys`方法来查看它包含哪些对象：

```
In [133]: store.keys()
Out[133]: ['/df1', '/df2']
```

并且我们可以使用Python的`in`关键字测试是否存在具有给定键的对象：

```
In [134]: 'df2' in store
Out[134]: True
```

要从存储中检索对象，我们再次使用类似字典的语义，通过对应的键来索引该对象：

```
In [135]: df = store["df1"]
```

通过`HDFStore`对象，我们还可以使用`root`属性访问底层的HDF5句柄。这实际上就是一个PyTables文件句柄：

```
In [136]: store.root
Out[136]: / (RootGroup) "   children := ['df1' (Group), 'df2' (Group)]
```

使用完`HDFStore`对象后，我们应该使用`close`方法关闭它，以确保与其关联的所有数据都已写入文件。

```
In [137]: store.close()
```

由于HDF5是一种标准文件格式，因此我们当然可以使用任何其他兼容HDF5的软件（例如`h5py`库）打开使用Pandas `HDFStore`或PyTables创建的HDF5文件。如果我们使用`h5py`打开由`HDFStore`生成的文件，可以轻松检查其内容，并了解`HDFStore`对象是如何组织我们分配给它的`DataFrame`对象的数据的：

```
In [138]: f = h5py.File("store.h5")
In [139]: f.visititems(lambda x, y: print(x, "\t" * int(3 - len(str(x))//8), y))
df1                 
df1/axis0           
df1/axis1           
df1/block0_items    
df1/block0_values   
df2                 
df2/axis0           
df2/axis1           
df2/block0_items    
df2/block0_values   
df2/block1_items    
df2/block1_values   
df2/block2_items    
df2/block2_values   
```

我们可以看到`HDFStore`对象将每个`DataFrame`对象存储在其自身的组中，并将每个数据框分割为几个异构的HDF5数据集（块），其中列按数据类型分组。此外，列名和值存储在单独的HDF5数据集中。

```
In [140]: f["/df2/block0_items"].value
Out[140]: array([b'S%', b'Shift/GP', b'FO%'], dtype='|S8')
In [141]: f["/df2/block0_values"][:3]
Out[141]: array([[ 13.9,  24\. ,  52.5],
[ 15.2,  25.2,  49\. ],
[ 12.6,  25.1,  52.9]])
In [142]: f["/df2/block1_values"][:3, :5]
Out[142]: array([[  1,  80,  36,  68, 104],
[  2,  77,  31,  56,  87],
[  3,  82,  28,  58,  86]])
```

# JSON

JSON（JavaScript Object Notation）是一种人类可读的轻量级纯文本格式，适合存储由列表和字典组成的数据集。此类列表和字典的值本身可以是列表或字典，或者必须是以下基本数据类型：字符串、整数、浮点数和布尔值，或`null`值（类似于Python中的`None`值）。这种数据模型允许存储复杂且多样的数据集，而无需像CSV等格式那样受表格形式的限制。例如，JSON文档可用作键值存储，其中不同键的值可以具有不同的结构和数据类型。

JSON格式最初设计用于在Web服务和JavaScript应用程序之间传递信息的数据交换格式。实际上，JSON是JavaScript语言的一个子集，因此也是有效的JavaScript代码。然而，JSON本身是一种与语言无关的数据格式，可以在包括Python在内的几乎所有语言和环境中轻松解析和生成。JSON语法也几乎是有效的Python代码，使得在Python中处理它也显得熟悉且直观。

我们在第10章中已经见过一个JSON数据集的示例，当时我们研究了东京地铁网络的图。在重新审视该数据集之前，我们先简要概述JSON基础以及如何在Python中读写JSON。Python标准库提供了`json`模块用于处理JSON格式的数据。具体来说，该模块包含从Python数据结构（列表或字典）生成JSON数据的函数：`json.dump`和`json.dumps`，以及将JSON数据解析为Python数据结构的函数：`json.load`和`json.loads`。函数`loads`和`dumps`以Python字符串作为输入和输出，而`load`和`dump`则操作文件句柄，将数据读写到文件中。

例如，我们可以通过调用`json.dumps`函数生成Python列表的JSON字符串。返回值是指定Python列表的JSON字符串表示形式，与用于创建该列表的Python代码非常相似。然而，一个显著的例外是Python值`None`，它在JSON中表示为`null`：

```
In [143]: data = ["string", 1.0, 2, None]
In [144]: data_json = json.dumps(data)
In [145]: data_json
Out[145]: '["string", 1.0, 2, null]'
```

要将JSON字符串转换回Python对象，我们可以使用`json.loads`：

```
In [146]: data = json.loads(data_json)
In [147]: data
Out[147]: ['string', 1.0, 2, None]
In [148]: data[0]
Out[148]: 'string'
```

我们可以使用完全相同的方法将Python字典存储为JSON字符串。同样，生成的JSON字符串与定义该字典的Python代码基本相同：

```
In [149]: data = {"one": 1, "two": 2.0, "three": "three"}
In [150]: data_json = json.dumps(data)
In [151]: data_json
Out[151]: '{"two": 2.0, "three": "three", "one": 1}'
```

要解析JSON字符串并将其转换回Python对象，我们再次使用`json.loads`：

```
In [152]: data = json.loads(data_json)
In [153]: data["two"]
Out[153]: 2.0
In [154]: data["three"]
Out[154]: 'three'
```

列表和字典的组合构成了一种多功能的数据结构。例如，我们可以存储包含可变数量元素的列表或字典的列表。这类数据很难直接存储为表格数组，而嵌套列表和字典的层级越多，处理起来就越不实用。使用`json.dump`和`json.dumps`函数生成JSON数据时，我们可以选择性地传入参数`indent=True`，以获得缩进后的JSON代码，使其更易于阅读：

```
In [155]: data = {"one": [1],
...:         "two": [1, 2],
...:         "three": [1, 2, 3]}
In [156]: data_json = json.dumps(data, indent=True)
In [157]: data_json
Out[157]: {
"two": [
1,

],
"three": [
1,
2,

],
"one": [

]
}
```

作为一个更复杂数据结构的例子，考虑一个包含列表、字典、元组列表和文本字符串的字典。我们可以使用与前面相同的方法，通过 `json.dumps` 生成该数据结构的 JSON 表示，但这里我们改用 `json.dump` 函数将内容写入文件。与 `json.dumps` 相比，它多接受一个文件句柄作为第二个参数，我们需要事先创建该句柄：

```
In [158]: data = {"one": [1],
...:         "two": {"one": 1, "two": 2},
...:         "three": [(1,), (1, 2), (1, 2, 3)],
...:         "four": "a text string"}
In [159]: with open("data.json", "w") as f:
...:     json.dump(data, f)
```

结果是 Python 数据结构的 JSON 表示被写入文件 `data.json`：

```
In [160]: !cat data.json
{"four": "a text string", "two": {"two": 2, "one": 1}, "three": [[1], [1, 2], [1, 2, 3]],
"one": [1]}
```

要读取 JSON 格式的文件并将其解析为 Python 数据结构，我们可以使用 `json.load`，需要向其传递一个打开文件的句柄：

```
In [161]: with open("data.json", "r") as f:
...:     data_from_file = json.load(f)
In [162]: data_from_file["two"]
Out[162]: [1, 2]
In [163]: data_from_file["three"]
Out[163]: [[1], [1, 2], [1, 2, 3]]
```

`json.load` 返回的数据结构并不总是与 `json.dump` 存储的完全相同。特别是，JSON 以 Unicode 格式存储，因此 `json.load` 返回的数据结构中的字符串始终是 Unicode 字符串。另外，从前面的例子可以看到，JSON 不区分元组和列表，`json.load` 总是生成列表而非元组，并且字典键的显示顺序也不保证，除非在 `dumps` 和 `dump` 函数中使用 `sorted_keys=True` 参数。

现在我们已经了解了如何使用 `json` 模块在 Python 列表/字典与 JSON 表示之间进行转换，值得重新审视第 10 章中的东京地铁数据集。这是一个更真实的数据集，也是混合了字典、可变长度列表和字符串值的数据结构示例。以下是该 JSON 文件的前 20 行：

```
In [164]: !head -n 20 tokyo-metro.json
{
"C": {
"color": "#149848",
"transfers": [
[
"C3",
"F15"
],
[
"C4",
"Z2"
],
[
"C4",
"G2"
],
[
"C7",
"M14"
],
```

要将 JSON 数据加载到 Python 数据结构中，我们像以前一样使用 `json.load`：

```
In [165]: with open("tokyo-metro.json", "r") as f:
...:     data = json.load(f)
```

结果是一个字典，每条地铁线路对应一个键：

```
In [166]: data.keys()
Out[166]: ['N', 'M', 'Z', 'T', 'H', 'C', 'G', 'F', 'Y']
```

每条地铁线路的字典值又是一个字典，包含线路颜色、换乘点列表以及线路车站之间的旅行时间：

```
In [167]: data["C"].keys()
Out[167]: ['color', 'transfers', 'travel_times']
In [168]: data["C"]["color"]
Out[168]: '#149848'
In [169]: data["C"]["transfers"]
Out[169]: [['C3', 'F15'],  ['C4', 'Z2'],  ['C4', 'G2'],  ['C7', 'M14'],  ['C7', 'N6'],
['C7', 'G6'],  ['C8', 'M15'],  ['C8', 'H6'],  ['C9', 'H7'],  ['C9', 'Y18'],
['C11', 'T9'],  ['C11', 'M18'],  ['C11', 'Z8'],  ['C12', 'M19'],  ['C18', 'H21']]
```

将数据集加载为嵌套的 Python 字典和列表结构后，我们可以轻松地遍历和过滤数据结构中的项，例如使用 Python 的列表推导式语法。以下示例演示了如何选择 C 线上旅行时间为 1 分钟的连通节点集。

```python
In [170]: [(s, e, tt) for s, e, tt in data["C"]["travel_times"] if tt == 1]
Out[170]: [('C3', 'C4', 1), ('C7', 'C8', 1), ('C9', 'C10', 1)]
```

字典的层级结构，连同其中存储的可变长度列表，使得这个数据集成为一个松散结构数据集的良好示例，因此也适用于 JSON 等灵活格式的存储。

字典层级和存储在其中的列表的可变长度特性，使得这个数据集成为一个松散结构数据集的良好示例，因此也适用于 JSON 等灵活格式的存储。

## 序列化

在上一节中，我们使用 JSON 格式生成了内存中 Python 对象（如列表和字典）的表示形式。这个过程称为序列化，在本例中，它生成了对象的 JSON 纯文本表示。JSON 格式的一个优点是它与语言无关，并且可以被其他软件轻松读取。其缺点是 JSON 文件空间效率不高，并且只能用于序列化有限类型的对象（列表、字典、基本类型，如上一节所述）。有许多替代的序列化技术可以解决这些问题。此处我们简要介绍两种替代方案，它们分别解决空间效率问题和可序列化的对象类型问题：`msgpack` 库和 Python 的 `pickle` 模块。

首先从 `msgpack` 开始，这是一种用于高效存储类 JSON 数据的二进制协议。`msgpack` 软件可用于多种语言和环境。有关该库及其 Python 绑定的更多信息，请参阅项目网站 [`http://msgpack.org`](http://msgpack.org)。与 `json` 模块类似，`msgpack` 库提供了两组函数，分别操作字节列表（`msgpack.packb` 和 `msgpack.unpackb`）和文件句柄（`msgpack.pack` 和 `msgpack.unpack`）。`pack` 和 `packb` 函数将 Python 数据结构转换为二进制表示形式，而 `unpack` 和 `unpackb` 函数则执行相反的操作。例如，东京地铁数据集的 JSON 文件相对较大，在磁盘上占用约 27 KB：

```
In [171]: !ls -lh tokyo-metro.json
-rw-r--r--@ 1 rob  staff    27K Apr  7 23:18 tokyo-metro.json
```

使用 `msgpack` 而非 JSON 打包数据结构会得到一个相当小的文件，大小约为 3 KB：

```
In [172]: data_pack = msgpack.packb(data)
In [173]: type(data_pack)
Out[173]: bytes
In [174]: len(data_pack)
Out[174]: 3021
In [175]: with open("tokyo-metro.msgpack", "wb") as f:
...:     f.write(data_pack)
In [176]: !ls -lh tokyo-metro.msgpack
-rw-r--r--@ 1 rob  staff   3.0K Apr  8 00:40 tokyo-metro.msgpack
```

更准确地说，数据集的字节列表表示仅使用了 3021 字节。在存储空间或带宽至关重要的应用中，这可能是一个显著的改进。然而，我们为这种更高的存储效率付出的代价是，我们必须使用 `msgpack` 库来解包数据，并且它使用二进制格式，因此不可读。这种权衡是否可以接受将取决于具体的应用。要解包二进制 `msgpack` 字节列表，我们可以使用 `msgpack.unpackb` 函数，它会恢复原始的数据结构：

```
In [177]: del data
In [178]: with open("tokyo-metro.msgpack", "rb") as f:
...:     data_msgpack = f.read()
...:     data = msgpack.unpackb(data_msgpack)
In [179]: list(data.keys())
Out[179]: ['T', 'M', 'Z', 'H', 'F', 'C', 'G', 'N', 'Y']
```

JSON 序列化的另一个问题是，只有特定类型的 Python 对象可以存储为 JSON。Python 的 `pickle` 模块可以创建几乎所有类型的 Python 对象的二进制表示，包括类实例和函数。使用 `pickle` 模块的方式与 `json` 模块完全相同：我们有用于将对象序列化为字节数组和文件句柄的 `dump` 和 `dumps` 函数，以及用于反序列化 pickle 对象的 `load` 和 `loads` 函数。

```
In [180]: with open("tokyo-metro.pickle", "wb") as f:
...:     pickle.dump(data, f)
In [181]: del data
In [182]: !ls -lh tokyo-metro.pickle
-rw-r--r--@ 1 rob  staff   8.5K Apr  8 00:40 tokyo-metro.pickle
```

Pickle 对象的大小远小于 JSON 序列化结果，但大于 `msgpack` 产生的序列化结果。我们可以使用 `pickle.load` 函数恢复 pickle 对象，该函数接受文件句柄作为参数：

```
In [183]: with open("tokyo-metro.pickle", "rb") as f:
...:     data = pickle.load(f)
In [184]: data.keys()
Out[184]: dict_keys(['T', 'M', 'Z', 'H', 'F', 'C', 'G', 'N', 'Y'])
```

Pickle 的主要优点是几乎任何类型的 Python 对象都可以被序列化。然而，Python pickle 文件无法被非 Python 编写的软件读取，也不推荐用于长期存储，因为无法始终保证 Python 版本之间以及定义被 pickle 对象的库的不同版本之间的兼容性。如果可能，使用 JSON 序列化基于列表和字典的数据结构通常是更好的方法，如果文件大小成为问题，`msgpack` 库提供了一个流行且易于访问的 JSON 替代方案。

## 总结

在本章中，我们回顾了用于将数值数据读写到磁盘文件的常见数据格式，并介绍了一系列可用于处理这些格式的 Python 库。我们首先研究了无处不在的 CSV 文件格式，这是一种简单透明的格式，适用于小型、简单的数据集。该格式的主要优势在于它是人类可读的纯文本，因此直观易懂。然而，它在处理数值数据时缺少许多理想特性，例如描述数据的元数据以及对多个数据集的支持。当数据的规模和复杂度超出 CSV 格式所能轻松处理的范围时，HDF5 格式自然成为数值数据的首选格式。HDF5 是一种二进制文件格式，因此不像 CSV 那样是人类可读的格式，但有许多好的工具可以探索 HDF5 文件中的内容，无论是通过编程方式还是使用命令行和基于 GUI 的用户界面。事实上，由于可以在属性中存储元数据，HDF5 是一种非常适合自描述数据的格式。它也是一种非常高效的数值数据文件格式，无论是在 I/O 还是存储方面，甚至可以作为计算无法装入计算机内存的超大数据集的数据模型。总而言之，HDF5 是数值计算的一个极佳工具，任何从事计算工作的人都应该熟悉它并从中获益匪浅。在本章末尾，我们还简要回顾了 JSON、`msgpack` 和 Python pickle，用于将数据序列化为文本和二进制格式。

## 拓展阅读

CSV 文件的非正式规范见 RFC 4180，[`http://tools.ietf.org/html/rfc4180`](http://tools.ietf.org/html/rfc4180)。该规范概述了 CSV 格式的许多常用特性，尽管并非所有 CSV 读写器都完全遵循此文档的每个方面。关于 HDF5 格式和 `h5py` 库，`h5py` 的创始人 Collette (2013) 提供了一篇易懂且信息丰富的介绍。此外，也值得了解 NetCDF（网络通用数据格式），[`www.unidata.ucar.edu/software/netcdf`](http://www.unidata.ucar.edu/software/netcdf)，这是另一种广泛使用的数值数据格式。`Pandas` 库还提供了我们在此讨论范围之外的 I/O 函数，例如读取 Excel 文件（`pandas.io.excel.read_excel`）和定宽格式（`read_fwf`）的功能。关于 JSON 格式，其简要但完整的规范可在网站 [`http://json.org`](http://json.org) 上找到。随着数据在计算中的作用日益重要，近年来格式和数据存储技术迅速多样化。作为计算从业者，从数据库（如 SQL 和 NoSQL 数据库）读取数据如今也是一项重要任务。Python 提供了一个通用数据库 API，用于标准化 Python 应用程序的数据库访问，如 PEP 249（[`www.python.org/dev/peps/pep-0249`](http://www.python.org/dev/peps/pep-0249)）所述。另一个值得关注的从 Python 读取数据库的项目是 SQLAlchemy（[`www.sqlalchemy.org`](http://www.sqlalchemy.org)）。

## 参考文献

Collette, A. (2013). *Python and HDF5.* Sebastopol: O’Reilly.

脚注 1 2 3 4 5 6