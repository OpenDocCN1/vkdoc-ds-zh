# 6. pandas 深入：数据操作

在上一章中，你了解了如何从数据库和文件等数据源获取数据。一旦数据以数据框格式存在，就可以对其进行操作。准备好数据以便更容易地进行分析和操作是很重要的。特别是为下一阶段做准备时，数据必须准备好用于可视化。

本章深入探讨 pandas 库为此数据分析阶段提供的功能。数据操作的三个阶段将分别处理，通过一系列示例说明各种操作，并解释如何最好地使用该库的函数来执行这些操作。数据操作的三个阶段是：

- 数据准备
- 数据转换
- 数据聚合

## 数据准备

在开始操作数据之前，有必要准备数据并将其组装成数据结构的形式，以便稍后使用 pandas 库提供的工具进行操作。数据准备的不同步骤列举如下。

- 加载
- 组装
    - 合并
    - 连接
    - 组合
- 重塑（数据透视）
- 移除

上一章已经涵盖了加载。在加载阶段，还包括将许多不同格式转换为数据框等数据结构的部分准备。但即使你已经获得了数据，可能来自不同的来源和格式，并将其统一到一个数据框中，你仍然需要执行进一步的数据准备操作。在本章中，特别是在本节中，你将会看到如何执行所有必要的操作，以便将数据整合到一个统一的数据结构中。

pandas 对象中包含的数据可以通过不同方式组装：

- *合并* — `pandas.merge()` 函数基于一个或多个键连接数据框中的行。这种模式对于熟悉 SQL 语言的人来说非常熟悉，因为它也实现了连接操作。
- *连接* — `pandas.concat()` 函数沿一个轴连接对象。
- *组合* — `pandas.DataFrame.combine_first()` 函数允许你连接重叠的数据，以便通过从另一个结构中获取数据来填充一个数据结构中的缺失值。

此外，准备过程的一部分还包括数据透视，即行和列之间的交换。



### 合并

合并操作对应于熟悉 SQL 的人的`JOIN`操作，它通过使用一个或多个键连接行来组合数据。

实际上，任何使用关系数据库的人通常都会使用 SQL 的`JOIN`查询，根据表之间共享的某些参考值（键）从不同表中获取数据。基于这些键，可以将其他表组合的结果以表格形式获得新数据。使用`pandas`库进行的这种操作称为*合并*，而`merge()`函数执行此类操作。

首先，您必须导入`pandas`库并定义两个将作为本节示例的数据框。

```
>>> import numpy as np
>>> import pandas as pd
>>> frame1 = pd.DataFrame( {'id':['ball','pencil','pen','mug','ashtray'],
...                      'price': [12.33,11.44,33.21,13.23,33.62]})
>>> frame1
id  price
0     ball  12.33
1   pencil  11.44
2      pen  33.21
3      mug  13.23
4  ashtray  33.62
>>> frame2 = pd.DataFrame( {'id':['pencil','pencil','ball','pen'],
...                      'color': ['white','red','red','black']})
>>> frame2
color      id
0  white  pencil
1    red  pencil
2    red    ball
3  black     pen
```

通过对两个数据框对象应用`merge()`函数来执行合并。

```
>>> pd.merge(frame1,frame2)
id  price  color
0    ball  12.33    red
1  pencil  11.44  white
2  pencil  11.44    red
3     pen  33.21  black
```

正如您从结果中看到的，返回的数据框由所有具有共同`id`的行组成。除了公共列之外，还添加了第一个和第二个数据框中的列。

在这种情况下，您使用了`merge()`函数而没有显式指定任何列。实际上，在大多数情况下，您需要决定基于哪一列进行合并。

为此，添加带有列名的`on`选项作为合并的键。

```
>>> frame1 = pd.DataFrame( {'id':['ball','pencil','pen','mug','ashtray'],
...                      'color': ['white','red','red','black','green'],
...                      'brand': ['OMG','ABC','ABC','POD','POD']})
>>> frame1
id  color brand
0     ball  white   OMG
1   pencil    red   ABC
2      pen    red   ABC
3      mug  black   POD
4  ashtray  green   POD
>>> frame2 = pd.DataFrame( {'id':['pencil','pencil','ball','pen'],
...                      'brand': ['OMG','POD','ABC','POD']})
>>> frame2
id brand
0  pencil   OMG
1  pencil   POD
2    ball   ABC
3     pen   POD
```

现在，在这种情况下，您有两个具有相同列名的数据框。因此，如果您启动合并，您将不会得到任何结果。

```
>>> pd.merge(frame1,frame2)
Empty DataFrame
Columns: [id, color, brand]
Index: []
```

有必要显式定义`pandas`必须遵循的合并标准，在`on`选项中指定键列的名称。

```
>>> pd.merge(frame1,frame2,on='id')
id  color brand_x brand_y
0    ball  white     OMG     ABC
1  pencil    red     ABC     OMG
2  pencil    red     ABC     POD
3     pen    red     ABC     POD
>>> pd.merge(frame1,frame2,on='brand')
id_x  color brand    id_y
0     ball  white   OMG  pencil
1   pencil    red   ABC    ball
2      pen    red   ABC    ball
3      mug  black   POD  pencil
4      mug  black   POD     pen
5  ashtray  green   POD  pencil
6  ashtray  green   POD     pen
```

正如预期的那样，根据合并的标准，结果差异很大。

然而，经常会出现相反的问题，即您有两个数据框，其中的键列名称不同。要解决这种情况，您必须使用`left_on`和`right_on`选项，它们分别指定第一个和第二个数据框的键列。这是一个例子。

```
>>> frame2.columns = ['sid','brand']
>>> frame2
sid brand
0  pencil   OMG
1  pencil   POD
2    ball   ABC
3     pen   POD
>>> pd.merge(frame1, frame2, left_on='id', right_on='sid')
id  color brand_x     sid brand_y
0    ball  white     OMG    ball     ABC
1  pencil    red     ABC  pencil     OMG
2  pencil    red     ABC  pencil     POD
3     pen    red     ABC     pen     POD
```

默认情况下，`merge()`函数执行*内连接*；结果中的键是交集的结果。

其他可能的选项是*左连接*、*右连接*和*外连接*。外连接产生所有键的并集，结合了左连接和右连接的效果。要选择连接类型，您必须使用`how`选项。

```
>>> frame2.columns = ['id','brand']
>>> pd.merge(frame1,frame2,on='id')
id  color brand_x brand_y
0    ball  white     OMG     ABC
1  pencil    red     ABC     OMG
2  pencil    red     ABC     POD
3     pen    red     ABC     POD
>>> pd.merge(frame1,frame2,on='id',how='outer')
id  color brand_x brand_y
0     ball  white     OMG     ABC
1   pencil    red     ABC     OMG
2   pencil    red     ABC     POD
3      pen    red     ABC     POD
4      mug  black     POD     NaN
5  ashtray  green     POD     NaN
>>> pd.merge(frame1,frame2,on='id',how='left')
id  color brand_x brand_y
0     ball  white     OMG     ABC
1   pencil    red     ABC     OMG
2   pencil    red     ABC     POD
3      pen    red     ABC     POD
4      mug  black     POD     NaN
5  ashtray  green     POD     NaN
>>> pd.merge(frame1,frame2,on='id',how='right')
id  color brand_x brand_y
0  pencil    red     ABC     OMG
1  pencil    red     ABC     POD
2    ball  white     OMG     ABC
3     pen    red     ABC     POD
```

要合并多个键，您只需向`on`选项添加一个列表。

```
>>> pd.merge(frame1,frame2,on=['id','brand'],how='outer')
id  color brand
0     ball  white   OMG
1   pencil    red   ABC
2      pen    red   ABC
3      mug  black   POD
4  ashtray  green   POD
5   pencil    NaN   OMG
6   pencil    NaN   POD
7     ball    NaN   ABC
8      pen    NaN   POD
```

#### 基于索引合并

在某些情况下，可以不考虑数据框的列作为键，而将索引用作合并的键。然后，为了决定考虑哪些索引，您将`left_index`或`right_index`选项设置为`True`来激活它们，并且可以同时激活它们。

```
>>> pd.merge(frame1,frame2,right_index=True, left_index=True)
id_x  color brand_x    id_y brand_y
0    ball  white     OMG  pencil     OMG
1  pencil    red     ABC  pencil     POD
2     pen    red     ABC    ball     ABC
3     mug  black   POD     pen     POD
```

但是数据框对象有一个`join()`函数，当您想按索引进行合并时，它要方便得多。它还可以用于合并许多具有相同索引但列不重叠的数据框对象。

实际上，如果您执行以下操作：

```
>>> frame1.join(frame2)
```

您将收到一个错误代码，因为`frame1`中的某些列与`frame2`中的列同名。在调用`join()`函数之前，您需要重命名`frame2`中的列。

```
>>> frame2.columns = ['id2','brand2']
>>> frame1.join(frame2)
id  color brand     id2 brand2
0     ball  white   OMG  pencil    OMG
1   pencil    red   ABC  pencil    POD
2      pen    red   ABC    ball    ABC
3      mug  black   POD     pen    POD
4  ashtray  green   POD     NaN    NaN
```

在这里，您执行了一次合并，但这次是基于索引的值而不是列。这次还有一个仅在`frame1`中存在的索引 4，但`frame2`中对应列的值报告为`NaN`。



## 连接

另一种类型的数据组合被称为*连接*。NumPy 提供了一个 `concatenate()` 函数来对数组执行此类操作。

```
>>> array1 = np.arange(9).reshape((3,3))
>>> array1
array([[0, 1, 2],
[3, 4, 5],
[6, 7, 8]])
>>> array2 = np.arange(9).reshape((3,3))+6
>>> array2
array([[ 6,  7,  8],
[ 9, 10, 11],
[12, 13, 14]])
>>> np.concatenate([array1,array2],axis=1)
array([[ 0,  1,  2,  6,  7,  8],
[ 3,  4,  5,  9, 10, 11],
[ 6,  7,  8, 12, 13, 14]])
>>> np.concatenate([array1,array2],axis=0)
array([[ 0,  1,  2],
[ 3,  4,  5],
[ 6,  7,  8],
[ 6,  7,  8],
[ 9, 10, 11],
[12, 13, 14]])
```

使用 pandas 库及其数据结构（如 Series 和 DataFrame），带标签的轴允许你进一步泛化数组的连接。pandas 为此类操作提供了 `concat()` 函数。

```
>>> ser1 = pd.Series(np.random.rand(4), index=[1,2,3,4])
>>> ser1
1    0.636584
2    0.345030
3    0.157537
4    0.070351
dtype: float64
>>> ser2 = pd.Series(np.random.rand(4), index=[5,6,7,8])
>>> ser2
5    0.411319
6    0.359946
7    0.987651
8    0.329173
dtype: float64
>>> pd.concat([ser1,ser2])
1    0.636584
2    0.345030
3    0.157537
4    0.070351
5    0.411319
6    0.359946
7    0.987651
8    0.329173
dtype: float64
```

默认情况下，`concat()` 函数在 `axis = 0` 上操作，返回的对象是一个 Series。如果你设置 `axis = 1`，结果将是一个 DataFrame。

```
>>> pd.concat([ser1,ser2],axis=1)
0         1
1  0.636584       NaN
2  0.345030       NaN
3  0.157537       NaN
4  0.070351       NaN
5       NaN  0.411319
6       NaN  0.359946
7       NaN  0.987651
8       NaN  0.329173
```

这类操作的问题在于，结果中无法识别被连接的部分。例如，你想在连接轴上创建一个层次化索引。要做到这一点，你必须使用 `keys` 选项。

```
>>> pd.concat([ser1,ser2], keys=[1,2])
1  1    0.636584
2    0.345030
3    0.157537
4    0.070351
2  5    0.411319
6    0.359946
7    0.987651
8    0.329173
dtype: float64
```

对于沿 `axis = 1` 进行 Series 组合的情况，keys 会成为 DataFrame 的列标题。

```
>>> pd.concat([ser1,ser2], axis=1, keys=[1,2])
1         2
1  0.636584       NaN
2  0.345030       NaN
3  0.157537       NaN
4  0.070351       NaN
5       NaN  0.411319
6       NaN  0.359946
7       NaN  0.987651
8       NaN  0.329173
```

到目前为止，你已经看到了应用于 Series 的连接，但相同的逻辑也可以应用于 DataFrame。

```
>>> frame1 = pd.DataFrame(np.random.rand(9).reshape(3,3),
...                      index=[1,2,3],
...                      columns=['A','B','C'])
>>> frame2 = pd.DataFrame(np.random.rand(9).reshape(3,3),
...                      index=[4,5,6],
...                      columns=['A','B','C'])
>>> pd.concat([frame1, frame2])
A         B         C
1  0.400663  0.937932  0.938035
2  0.202442  0.001500  0.231215
3  0.940898  0.045196  0.723390
4  0.568636  0.477043  0.913326
5  0.598378  0.315435  0.311443
6  0.619859  0.198060  0.647902
>>> pd.concat([frame1, frame2], axis=1)
A         B         C         A         B         C
1  0.400663  0.937932  0.938035       NaN       NaN       NaN
2  0.202442  0.001500  0.231215       NaN       NaN       NaN
3  0.940898  0.045196  0.723390       NaN       NaN       NaN
4       NaN       NaN       NaN  0.568636  0.477043  0.913326
5       NaN       NaN       NaN  0.598378  0.315435  0.311443
6       NaN       NaN       NaN  0.619859  0.198060  0.647902
```

### 组合

还存在另一种数据组合的情况，它无法通过合并或连接实现。例如，你希望两个数据集的索引能够完全重合或至少部分重合。

一个适用于 Series 的函数是 `combine_first()`，它可以执行此类操作并进行数据对齐。

```
>>> ser1 = pd.Series(np.random.rand(5),index=[1,2,3,4,5])
>>> ser1
1    0.942631
2    0.033523
3    0.886323
4    0.809757
5    0.800295
dtype: float64
>>> ser2 = pd.Series(np.random.rand(4),index=[2,4,5,6])
>>> ser2
2    0.739982
4    0.225647
5    0.709576
6    0.214882
dtype: float64
>>> ser1.combine_first(ser2)
1    0.942631
2    0.033523
3    0.886323
4    0.809757
5    0.800295
6    0.214882
dtype: float64
>>> ser2.combine_first(ser1)
1    0.942631
2    0.739982
3    0.886323
4    0.225647
5    0.709576
6    0.214882
dtype: float64
```

相反，如果你想要部分重叠，可以指定要重叠的 Series 部分。

```
>>> ser1[:3].combine_first(ser2[:3])
1    0.942631
2    0.033523
3    0.886323
4    0.225647
5    0.709576
dtype: float64
```

### 数据透视

除了组装数据以统一来自不同来源的值之外，另一个相当常见的操作是*数据透视*。事实上，按行或按列排列值并不总是适合你的目标。有时你可能希望根据列值或行值重新排列数据。

#### 使用层次化索引进行数据透视

你已经看到 DataFrame 可以支持层次化索引。可以利用此特性重新排列 DataFrame 中的数据。在数据透视的上下文中，有两个基本操作：

*   *堆叠*——旋转或透视数据结构，将列转换为行

*   *去堆叠*——将行转换为列

```
>>> frame1 = pd.DataFrame(np.arange(9).reshape(3,3),
...                    index=['white','black','red'],
...                    columns=['ball','pen','pencil'])
>>> frame1
ball  pen  pencil
white     0    1       2
black     3    4       5
red       6    7       8
```

在 DataFrame 上使用 `stack()` 函数，会将列透视成行，从而生成一个 Series：

```
>>> ser5 = frame1.stack()
>>> ser5
white  ball      0
pen       1
pencil    2
black  ball      3
pen       4
pencil    5
red    ball      6
pen       7
pencil    8
dtype: int32
```

从这个具有层次化索引的 Series 中，你可以通过使用 `unstack()` 函数将其重新组装成一个数据透视表（DataFrame）。

```
>>> ser5.unstack()
ball  pen  pencil
white     0    1       2
black     3    4       5
red       6    7       8
```

你也可以在不同层级上进行去堆叠，在函数参数中指定层级数或其名称即可。

```
>>> ser5.unstack(0)
white  black  red
ball        0      3    6
pen         1      4    7
pencil      2      5    8
```



#### 从“长”格式转换为“宽”格式

最常见的数据集存储方式是通过逐条记录数据产生的，这些数据将构成文本文件（例如 CSV）中的一行，或数据库表中的一行。当您拥有仪器读数、随时间迭代的计算结果或简单手动输入的一系列数值时，这种情况尤其常见。与此类似的案例包括日志文件，它通过不断累积数据，逐行填充内容。

这种数据集类型的独特之处在于，其条目分布在多个列中，并且常常在后续行中重复出现。如果始终使用表格形式，这种数据被称为*长*格式或*堆叠*格式。

为了更清晰地理解这一点，请考虑以下数据框。

```
>>> longframe = pd.DataFrame({ 'color':['white','white','white',
...                                  'red','red','red',
...                                  'black','black','black'],
...                         'item':['ball','pen','mug',
...                                 'ball','pen','mug',
...                                 'ball','pen','mug'],
...                         'value': np.random.rand(9)})
>>> longframe
  color  item     value
0  white  ball  0.091438
1  white   pen  0.495049
2  white   mug  0.956225
3    red  ball  0.394441
4    red   pen  0.501164
5    red   mug  0.561832
6  black  ball  0.879022
7  black   pen  0.610975
8  black   mug  0.093324
```

这种数据记录方式存在一些缺点。例如，某些字段会重复出现。如果将各列视为键，那么这种格式的数据将难以阅读，尤其是难以充分理解键值与其余列之间的关系。

除了长格式，还有另一种在表中排列数据的方式，称为*宽*格式。这种模式更易于阅读，便于与其他表建立关联，并且占用的空间更少。因此，通常来说这是一种更高效的数据存储方式，尽管在数据填充过程中不太实用。

作为准则，选择一列或一组列作为主键；然后，这些列中包含的值必须是唯一的。

在这方面，pandas 提供了一个函数，允许您将数据框从长格式转换为宽格式。这个函数是 `pivot()`，它接受将扮演键角色的列作为参数。

从前面的例子出发，您可以选择将 `color` 列作为键，将 `item` 列作为第二个键，后者的值将构成数据框的新列，从而创建一个宽格式的数据框。

```
>>> wideframe = longframe.pivot(index='color',columns='item')
>>> wideframe
value
item       ball       mug       pen
color
black  0.879022  0.093324  0.610975
red    0.394441  0.561832  0.501164
white  0.091438  0.956225  0.495049
```

正如您现在所看到的，在这种格式下，数据框更加紧凑，其中包含的数据也更具可读性。

### 删除

数据准备的最后一个阶段是删除列和行。您已经在第 4 章中了解了这部分内容。但为了完整性，此处再次进行说明。通过示例定义一个数据框。

```
>>> frame1 = pd.DataFrame(np.arange(9).reshape(3,3),
...                    index=['white','black','red'],
...                    columns=['ball','pen','pencil'])
>>> frame1
       ball  pen  pencil
white     0    1       2
black     3    4       5
red       6    7       8
```

要删除某一列，只需对数据框应用 `del` 命令，并指定列名即可。

```
>>> del frame1['ball']
>>> frame1
       pen  pencil
white    1       2
black    4       5
red      7       8
```

相反，要删除不需要的行，则必须使用 `drop()` 函数，并将相应索引的标签作为参数。

```
>>> frame1.drop('white')
       pen  pencil
black    4       5
red      7       8
```

## 数据转换

到目前为止，您已经了解了如何准备数据进行分析。实际上，这个过程代表了数据框中数据的重组，可能包括通过其他数据框添加数据以及移除不需要的部分。

现在，您将开始数据操作的第二个阶段：*数据转换*。在您安排好数据的形式及其在数据结构中的位置之后，转换它们的值就变得非常重要。事实上，在本节中，您将看到一些常见问题以及使用 pandas 库函数解决这些问题所需的步骤。

其中一些操作涉及重复值或无效值的处理，可能需要进行删除或替换。其他操作则与修改索引相关。还有的步骤包括处理和加工数据的数值与字符串。

### 删除重复项

由于各种原因，数据框中可能存在重复的行。在规模巨大的数据框中，检测这些行可能非常困难。在这种情况下，pandas 提供了一系列工具来分析大型数据结构中存在的重复数据。

首先，创建一个包含一些重复行的简单数据框。

```
>>> dframe = pd.DataFrame({ 'color': ['white','white','red','red','white'],
...                      'value': [2,1,3,3,2]})
>>> dframe
   color  value
0  white      2
1  white      1
2    red      3
3    red      3
4  white      2
```

应用于数据框的 `duplicated()` 函数可以检测出似乎重复的行。它会返回一个布尔值序列，其中每个元素对应一行，如果该行重复（即，除第一次出现之外的后续出现），则返回 `True`；如果该行在之前的元素中没有重复项，则返回 `False`。

```
>>> dframe.duplicated()
0    False
1    False
2    False
3     True
4     True
dtype: bool
```

返回一个布尔序列在许多情况下非常有用，尤其是在进行过滤时。实际上，如果您想知道哪些行是重复的，只需输入以下代码：

```
>>> dframe[dframe.duplicated()]
   color  value
3    red      3
4  white      2
```

通常，所有重复的行都需要从数据框中删除；为此，pandas 提供了 `drop_duplicates()` 函数，该函数返回不含重复行的数据框。

```
>>> dframe[dframe.duplicated()]
   color  value
3    red      3
4  white      2
```

### 映射

pandas 库提供了一组函数，正如您将在本节中看到的，它们利用映射来执行某些操作。映射无非是创建两个不同值之间的匹配列表，能够将一个值绑定到特定的标签或字符串。

要定义映射，没有比 `dict` 对象更好的选择了。

```
map = {
'label1' : 'value1',
'label2' : 'value2',
...
}
```

您在本节中看到的函数执行特定的操作，但它们都接受一个 `dict` 对象作为参数。

*   `replace()` — 替换值
*   `map()` — 创建新列
*   `rename()` — 替换索引值



#### 通过映射替换值

在您组装的数据结构中，经常会有不满足需求的值。例如，文本可能是外语，或者是另一个值的同义词，或者没有以所需的形状表达。在这种情况下，对各种值进行替换操作通常是一个必要的过程。

作为示例，定义一个包含各种对象和颜色的数据帧，其中包括两种非英语的颜色。组装操作可能会继续维护包含不良形式值的数据。

```python
>>> frame = pd.DataFrame({ 'item':['ball','mug','pen','pencil','ashtray'],
...                     'color':['white','rosso','verde','black','yellow'],
'price':[5.56,4.20,1.30,0.56,2.75]})
>>> frame
item   color  price
0     ball   white   5.56
1      mug   rosso   4.20
2      pen   verde   1.30
3   pencil   black   0.56
4  ashtray  yellow   2.75
```

为了用新值替换不正确的值，需要定义一个对应关系映射，其中包含新值作为键。

```python
>>> newcolors = {
...    'rosso': 'red',
...    'verde': 'green'
... }
```

现在唯一要做的就是使用`replace()`函数并将映射作为参数传递。

```python
>>> frame.replace(newcolors)
item   color  price
0     ball   white   5.56
1      mug     red   4.20
2      pen   green   1.30
3   pencil   black   0.56
4  ashtray  yellow   2.75
```

从结果可以看出，数据帧中的两种颜色已被替换为正确的值。一个常见的例子是将`NaN`值替换为另一个值，例如`0`。您可以使用`replace()`，它非常擅长完成这项工作。

```python
>>> ser = pd.Series([1,3,np.nan,4,6,np.nan,3])
>>> ser
0   1.0
1   3.0
2   NaN
3   4.0
4   6.0
5   NaN
6   3.0
dtype: float64
>>> ser.replace(np.nan,0)
0    1.0
1    3.0
2    0.0
3    4.0
4    6.0
5    0.0
6    3.0
dtype: float64
```

#### 通过映射添加值

在前面的示例中，您了解了如何通过映射对应关系来替换值。在本例中，您将继续通过另一个示例来利用值的映射。在这里，您利用映射来根据另一列包含的值向某一列添加值。映射将始终单独定义。

```python
>>> frame = pd.DataFrame({ 'item':['ball','mug','pen','pencil','ashtray'],
...                     'color':['white','red','green','black','yellow']})
>>> frame
item   color
0     ball   white
1      mug     red
2      pen   green
3   pencil   black
4  ashtray  yellow
```

假设您想添加一列来指示数据帧中显示的商品的价格。在这样做之前，假设您在某个地方有一个可用的价格表，其中描述了每种商品的价格。然后定义一个包含每种商品价格列表的`dict`对象。

```python
>>> prices = {
...    'ball' : 5.56,
...    'mug' : 4.20,
...    'bottle' : 1.30,
...    'scissors' : 3.41,
...    'pen' : 1.30,
...    'pencil' : 0.56,
...    'ashtray' : 2.75
... }
```

当应用于序列或数据帧的列时，`map()`函数接受一个函数或一个包含映射的`dict`对象。因此在这种情况下，您可以将价格映射应用于`item`列，确保向价格数据帧添加一列。

```python
>>> frame['price'] = frame['item'].map(prices)
>>> frame
item   color  price
0     ball   white   5.56
1      mug     red   4.20
2      pen   green   1.30
3   pencil   black   0.56
4  ashtray  yellow   2.75
```

#### 重命名轴的索引

以与您对序列和数据帧中包含的值所看到的非常相似的方式，轴标签也可以使用映射以非常相似的方式进行转换。因此，为了替换标签索引，pandas 提供了`rename()`函数，该函数接受映射作为参数，即一个`dict`对象。

```python
>>> frame
item   color  price
0     ball   white   5.56
1      mug     red   4.20
2      pen   green   1.30
3   pencil   black   0.56
4  ashtray  yellow   2.75
>>> reindex = {
...   0: 'first',
...   1: 'second',
...   2: 'third',
...   3: 'fourth',
...   4: 'fifth'}
>>> frame.rename(reindex)
item   color  price
first      ball   white   5.56
second      mug     red   4.20
third       pen   green   1.30
fourth   pencil   black   0.56
fifth   ashtray  yellow   2.75
```

如您所见，默认情况下，索引被重命名。如果您想重命名列，必须使用`columns`选项。这次您将各种映射显式分配给`index`和`columns`两个选项。

```python
>>> recolumn = {
...    'item':'object',
...    'price': 'value'}
>>> frame.rename(index=reindex, columns=recolumn)
object   color  value
first      ball   white   5.56
second      mug     red   4.20
third       pen   green   1.30
fourth   pencil   black   0.56
fifth   ashtray  yellow   2.75
```

同样，对于最简单的需要替换单个值的情况，您可以避免编写和分配许多变量。

```python
>>> frame.rename(index={1:'first'}, columns={'item':'object'})
object   color  price
0         ball   white   5.56
first      mug     red   4.20
2          pen   green   1.30
3       pencil   black   0.56
4      ashtray  yellow   2.75
```

到目前为止，您已经看到`rename()`函数返回一个带有更改的数据帧，而原始数据帧保持不变。如果您希望更改对调用该函数的对象生效，请将`inplace`选项设置为`True`。

```python
>>> frame.rename(columns={'item':'object'}, inplace=True)
>>> frame
object   color  price
0     ball   white   5.56
1      mug     red   4.20
2      pen   green   1.30
3   pencil   black   0.56
4  ashtray  yellow   2.75
```



### 离散化与分箱

本节将介绍一种更复杂的转换过程——*离散化*。它有时可用于处理按顺序生成的大量数据，尤其在某些实验场景中。然而，为了对数据进行分析，需要将这些数据转换为离散类别，例如，将这类读数的数值范围划分为更小的区间，并统计其中的出现次数或计算相关统计量。另一种情况是，当您对某个总体进行精确读数而获得大量样本时。即使在此情况下，为了便于数据分析，也需要将数值范围划分为类别，然后分析每个类别相关的出现次数和统计量。

例如，您可能获取了一个实验指标的读数，其数值在 0 到 100 之间。这些数据被收集在一个列表中。

```
>>> results = [12,34,67,55,28,90,99,12,3,56,74,44,87,23,49,89,87]
```

您已知实验值的范围是 0 到 100；因此可以均匀地将这个区间划分为四个相等的部分，即*分箱*。第一个分箱包含 0 到 25 之间的值，第二个包含 26 到 50 之间的值，第三个包含 51 到 75 之间的值，最后一个包含 76 到 100 之间的值。

要使用 pandas 进行这种分箱操作，首先需要定义一个包含分箱边界值的数组：

```
>>> bins = [0,25,50,75,100]
```

然后，使用一个名为 `cut()` 的特殊函数，将其应用于结果数组，同时传入分箱边界。

```
>>> cat = pd.cut(results, bins)
>>> cat
[(0, 25], (25, 50], (50, 75], (50, 75], (25, 50], ..., (75, 100], (0, 25], (25, 50], (75, 100], (75, 100]]
Length: 17
Categories (4, interval[int64, right]): [(0, 25] < (25, 50] < (50, 75] < (75, 100]]
```

`cut()` 函数返回的对象是一个特殊的 *Categorical*（类别）类型对象。您可以将其视为一个包含分箱名称的字符串数组。其内部包含一个 `categories` 数组，用于指示不同内部类别的名称，以及一个 `codes` 数组，其中包含一个与 `results`（即被分箱的数组）元素数量相同的数字列表。这些数字对应于 `results` 中相应元素被分配到的分箱。

```
>>> cat.categories
IntervalIndex([(0, 25], (25, 50], (50, 75], (75, 100]], dtype='interval[int64, right]')
>>> cat.codes
array([0, 1, 2, 2, 1, 3, 3, 0, 0, 2, 2, 1, 3, 0, 1, 3, 3], dtype=int8)
```

最后，要知道每个分箱中的出现次数，即每个类别中有多少个结果，需要使用 `value_counts()` 函数。

```
>>> pd.value_counts(cat)
(75, 100]    5
(0, 25]      4
(25, 50]     4
(50, 75]     4
dtype: int64
```

如您所见，每个类别的下限用方括号表示，上限用圆括号表示。这种表示法与数学中用于表示区间的符号一致。如果括号是方形的，表示该数字属于该范围（闭区间）；如果是圆形的，则数字不属于该区间（开区间）。

您可以先在一个字符串数组中定义各个分箱的名称，然后将该数组赋值给 `cut()` 函数中的 `labels` 选项（您已用该函数创建了 Categorical 对象），从而为各个分箱命名。

```
>>> bin_names = ['不太可能','可能性较低','可能','极有可能']
>>> pd.cut(results, bins, labels=bin_names)
['不太可能', '可能性较低', '可能', '可能', '可能性较低', ..., '极有可能', '不太可能', '可能性较低', '极有可能', '极有可能']
Length: 17
Categories (4, object): ['不太可能' < '可能性较低' < '可能' < '极有可能']
```

如果在调用 `cut()` 函数时，传入一个整数参数而非显式指定分箱边界，它将把数组的数值范围划分为您指定数量的区间。区间的上下限将由样本数据（即被分箱的数组）的最小值和最大值确定。

```
>>> pd.cut(results, 5)
[(2.904, 22.2], (22.2, 41.4], (60.6, 79.8], (41.4, 60.6], (22.2, 41.4], ..., (79.8, 99.0], (22.2, 41.4], (41.4, 60.6], (79.8, 99.0], (79.8, 99.0]]
Length: 17
Categories (5, interval[float64, right]): [(2.904, 22.2] < (22.2, 41.4] < (41.4, 60.6] < (60.6, 79.8] < (79.8, 99.0]]
)
```

除了 `cut()` 之外，pandas 还提供了另一种分箱方法：`qcut()`。此函数直接将样本划分为五分位数。事实上，根据数据样本的分布，使用 `cut()` 时，每个分箱中的出现次数会不同。而 `qcut()` 则确保每个分箱中的出现次数相等，但每个分箱的边界会随之变化。

```
>>> quintiles = pd.qcut(results, 5)
>>> quintiles
[(2.999, 24.0], (24.0, 46.0], (62.6, 87.0], (46.0, 62.6], (24.0, 46.0], ..., (62.6, 87.0], (2.999, 24.0], (46.0, 62.6], (87.0, 99.0], (62.6, 87.0]]
Length: 17
Categories (5, interval[float64, right]): [(2.999, 24.0] < (24.0, 46.0] < (46.0, 62.6] < (62.6, 87.0] < (87.0, 99.0]]
```

```
>>> pd.value_counts(quintiles)
(2.999, 24.0]    4
(62.6, 87.0]     4
(24.0, 46.0]     3
(46.0, 62.6]     3
(87.0, 99.0]     3
dtype: int64
```

如您所见，在五分位数的情况下，界定分箱的区间与 `cut()` 函数生成的区间不同。此外，如果观察每个分箱中的出现次数，您会发现 `qcut()` 试图使每个分箱的出现次数标准化，但在本例的五分位数情况下，前两个分箱多了一次出现，因为结果数量不能被五整除。

## 检测和过滤异常值

在数据分析过程中，经常需要检测数据结构中是否存在异常值。举例来说，创建一个包含三列、每列 1000 个完全随机值的数据框：

```
>>> randframe = pd.DataFrame(np.random.randn(1000,3))
```

使用 `describe()` 函数可以查看每列的统计信息。

```
>>> randframe.describe()
0            1            2
count  1000.000000  1000.000000  1000.000000
mean      0.021609    -0.022926    -0.019577
std       1.045777     0.998493     1.056961
min      -2.981600    -2.828229    -3.735046
25%      -0.675005    -0.729834    -0.737677
50%       0.003857    -0.016940    -0.031886
75%       0.738968     0.619175     0.718702
max       3.104202     2.942778     3.458472
```

例如，您可以将那些值大于三倍标准差的数据视为异常值。要仅获取数据框每列的标准差，请使用 `std()` 函数。

```
>>> randframe.std()
0    1.045777
1    0.998493
2    1.056961
dtype: float64
```

现在，对数据框中的所有值应用过滤操作，并对每列应用相应的标准差。借助 `any()` 函数，您可以对每列应用此过滤条件。

```
>>> randframe[(np.abs(randframe) > (3*randframe.std())).any(axis=1)]
0         1         2
69  -0.442411 -1.099404  3.206832
576 -0.154413 -1.108671  3.458472
907  2.296649  1.129156 -3.735046
```



## 排列

对序列或 DataFrame 的行进行排列（随机重排序）操作，使用 `numpy.random.permutation()` 函数即可轻松实现。

以下示例中，创建一个包含按升序排列的整数的 DataFrame。

```
>>> nframe = pd.DataFrame(np.arange(25).reshape(5,5))
>>> nframe
0   1   2   3   4
0   0   1   2   3   4
1   5   6   7   8   9
2  10  11  12  13  14
3  15  16  17  18  19
4  20  21  22  23  24
```

现在，创建一个包含 0 到 4 五个整数的数组，并使用 `permutation()` 函数将其随机排列。这将作为 DataFrame 行数据的新顺序。

```
>>> new_order = np.random.permutation(5)
>>> new_order
array([2, 3, 0, 1, 4])
```

接下来，使用 `take()` 函数将此顺序应用到 DataFrame 的所有行。

```
>>> nframe.take(new_order)
0   1   2   3   4
2  10  11  12  13  14
3  15  16  17  18  19
0   0   1   2   3   4
1   5   6   7   8   9
4  20  21  22  23  24
```

如你所见，行的顺序已经改变；现在索引的顺序与 `new_order` 数组中指示的顺序一致。

你还可以对 DataFrame 的局部进行排列。生成一个序列限定在特定范围内的数组，例如，下例中范围是从 2 到 4。

```
>>> new_order = [3,4,2]
>>> nframe.take(new_order)
0   1   2   3   4
3  15  16  17  18  19
4  20  21  22  23  24
2  10  11  12  13  14
```

### 随机采样

你刚刚看到了如何通过对 DataFrame 进行排列来提取其局部数据。有时，当你有一个巨大的 DataFrame 时，可能需要对其进行随机采样，最快捷的方法是使用 `np.random.randint()` 函数。

```
>>> sample = np.random.randint(0, len(nframe), size=3)
>>> sample
array([1, 4, 4])
>>> nframe.take(sample)
0   1   2   3   4
1   5   6   7   8   9
4  20  21  22  23  24
4  20  21  22  23  24
```

从这个随机采样示例中可以看出，同一个样本可能会出现多次。

## 字符串操作

Python 是一种流行的语言，因为它易于处理字符串和文本。大多数操作都可以通过使用 Python 提供的内置函数轻松完成。对于更复杂的匹配和操作情况，则需要使用正则表达式。

### 字符串操作的内置方法

在许多情况下，你会遇到复合字符串，需要将其各个部分分离出来，然后分配给正确的变量。`split()` 函数允许你以分隔符（例如逗号）为参考点来分离文本的各个部分。

```
>>> text = '16 Bolton Avenue , Boston'
>>> text.split(',')
['16 Bolton Avenue ', 'Boston']
```

如第一个元素所示，字符串末尾带有一个空格字符。为了解决这个常见问题，你需要将 `split()` 函数与 `strip()` 函数一起使用，后者会去除空白字符（包括换行符）。

```
>>> tokens = [s.strip() for s in text.split(',')]
>>> tokens
['16 Bolton Avenue', 'Boston']
```

结果是一个字符串数组。如果元素数量很少且固定不变，一种非常有趣的赋值方式如下：

```
>>> address, city = [s.strip() for s in text.split(',')]
>>> address
'16 Bolton Avenue'
>>> city
'Boston'
```

到目前为止，你已经看到了如何将文本拆分为多个部分，但通常你还需要相反的操作，即连接多个字符串以形成更长的文本。

最直观和简单的方法是使用 `+` 运算符连接文本的各个部分。

```
>>> address + ',' + city
'16 Bolton Avenue, Boston'
```

当只有两三个字符串需要连接时，这非常有用。如果有许多部分需要连接，更实用的方法是使用赋值给分隔符的 `join()` 函数，该分隔符用于连接各个字符串。

```
>>> strings = ['A+','A','A-','B','BB','BBB','C+']
>>> ';'.join(strings)
'A+;A;A-;B;BB;BBB;C+'
```

另一类可以对字符串执行的操作是在其中搜索文本片段，即子字符串。Python 提供了关键字，这是检测子字符串的最佳方式。

```
>>> 'Boston' in text
True
```

然而，还有两个函数可以用于此目的：`index()` 和 `find()`。

```
>>> text.index('Boston')

>>> text.find('Boston')
```

在这两种情况下，函数都会返回子字符串在文本中对应字符的位置编号。然而，当子字符串未找到时，这两个函数的行为差异就会显现出来：

```
>>> text.index('New York')
Traceback (most recent call last):
File "", line 1, in 
ValueError: substring not found
>>> text.find('New York')
```

实际上，`index()` 函数会返回一条错误消息，而 `find()` 在子字符串未找到时会返回 `-1`。同样，你还可以知道某个字符或字符组合（子字符串）在文本中出现了多少次。`count()` 函数可以提供这个数字。

```
>>> text.count('e')

>>> text.count('Avenue')
```

另一个可以对字符串执行的操作是替换或删除子字符串（或单个字符）。在这两种情况下，都使用 `replace()` 函数，如果提示将子字符串替换为空白字符，则该操作等同于从文本中删除该子字符串。

```
>>> text.replace('Avenue','Street')
'16 Bolton Street , Boston'
>>> text.replace('1', '')'16 Bolton Avenue, Boston'
```



### 正则表达式

正则表达式提供了一种非常灵活的方式来搜索和匹配文本中的字符串模式。一个单一的表达式，通常被称为*正则表达式*，是根据正则表达式语言形成的字符串。Python 内置了一个名为 `re` 的模块，它负责正则表达式的操作。

首先，当你想要使用正则表达式时，需要导入该模块：

```
>>> import re
```

`re` 模块提供了一组函数，可以分为三类：

- 模式匹配
- 替换
- 分割

让我们从几个例子开始。例如，用于表示一个或多个空白字符序列的正则表达式是 `\s+`。在上一节中，为了通过分隔符将文本分割成多个部分，你使用了 `split()`。`re` 模块甚至也有一个 `split()` 函数，它执行相同的操作，但它可以接受一个正则表达式模式作为分割标准，这使得它更加灵活。

```
>>> text = "This is      an\t odd  \n text!"
>>> re.split('\s+', text)
['This', 'is', 'an', 'odd', 'text!']
```

让我们更深入地分析 `re` 模块的机制。当你调用 `re.split()` 函数时，正则表达式首先被编译，然后它随后在文本参数上调用 `split()` 函数。你可以使用 `re.compile()` 函数编译正则表达式，从而获得一个可复用的正则表达式对象，并因此节省 CPU 周期。这在集合或字符串数组中迭代搜索子字符串的操作中尤其如此。

```
>>> regex = re.compile('\s+')
```

如果你使用 `compile()` 函数创建了一个 `regex` 对象，你可以通过以下方式直接对它应用 `split()`。

```
>>> regex.split(text)
['This', 'is', 'an', 'odd', 'text!']
```

为了匹配一个正则表达式模式到文本中的任何其他业务子字符串，你可以使用 `findall()` 函数。它返回文本中所有符合正则表达式的子字符串的列表。

例如，如果你想在一个字符串中查找所有以大写 "`A`" 开头的单词，或者例如，查找所有以 "`a`" 开头且不区分大小写的单词，你需要输入以下内容：

```
>>> text = 'This is my address: 16 Bolton Avenue, Boston'
>>> re.findall('A\w+',text)
['Avenue']
>>> re.findall('[A,a]\w+',text)
['address', 'Avenue']
```

还有两个与 `findall()` 函数相关的函数——`match()` 和 `search()`。`findall()` 返回列表中的所有匹配项，而 `search()` 函数只返回第一个匹配项。此外，该函数返回的对象是一个特殊的对象：

```
>>> re.search('[A,a]\w+',text)
<_sre.SRE_Match object; span=(? , ?), match='address'>
```

这个对象不包含与正则表达式模式匹配的子字符串的值，但它返回该子字符串在字符串中的起始和结束位置。

```
>>> search = re.search('[A,a]\w+',text)
>>> search.start()
8
>>> search.end()
15
>>> text[search.start():search.end()]
'address'
```

`match()` 函数仅在字符串的开头执行匹配；如果第一个字符没有匹配上，它就不会在字符串中继续搜索。如果没有找到匹配项，它将不会返回任何对象。

```
>>> re.match('[A,a]\w+',text)
```

如果 `match()` 有响应，它会返回一个与你在 `search()` 函数中看到的相同的对象。

```
>>> re.match('T\w+',text)
<_sre.SRE_Match object; span=(0, 4), match='This'>
>>> match = re.match('T\w+',text)
>>> text[match.start():match.end()]
'This'
```

## 数据聚合

数据处理的最后一个阶段是数据聚合。数据聚合涉及一种转换，该转换从一个数组生成一个单一的整数。实际上，你已经进行过许多数据聚合操作，例如，当你计算 `sum()`、`mean()` 和 `count()` 时。事实上，这些函数对一组数据进行操作，并执行计算，产生一个由单个值组成的一致结果。然而，一种更正式且对数据聚合有更多控制的方式是包含对一组数据进行分类的方式。

为分组而对一组数据进行分类通常是数据分析过程中的一个关键阶段。这是一个转换过程，因为在将数据分成不同的组之后，你应用一个函数，该函数以某种方式转换或变换数据，具体取决于它们所属的组。通常情况下，分组和应用函数这两个阶段是在一步中完成的。

对于数据分析的这一部分，pandas 也提供了一个非常灵活且高性能的工具：`GroupBy`。

同样，就像连接的情况一样，熟悉关系型数据库和 SQL 语言的人可以发现相似之处。尽管如此，像 SQL 这样的语言在应用于分组操作时是相当有限的。事实上，鉴于像 Python 这样的编程语言的灵活性，以及所有可用的库，特别是 pandas，你可以对分组执行非常复杂的操作。

### GroupBy

本节详细分析 `GroupBy` 的过程及其工作原理。通常，它的内部机制被称为*拆分-应用-合并*过程。在其操作模式中，你可以将此过程视为由三个操作表达的三个阶段：

- *拆分*——将数据集分成多个组
- *应用*——在每个组上应用一个函数
- *合并*——合并由不同组获得的所有结果

分析这三个不同的阶段 (见图 6-1)。在第一阶段，即拆分阶段，包含在数据结构（如 Series 或 DataFrame）中的数据根据给定的标准被分成几个组，这通常与索引或列中的特定值相关。用 SQL 术语来说，该列中包含的值被称为键。此外，如果你正在处理像 DataFrame 这样的二维对象，分组标准可以应用于行（`axis=0`）或列（`axis=1`）。

拆分-应用-合并的流程图，一种数据处理技术，流程从键=颜色到值。

图 6-1 拆分-应用-合并机制

第二阶段，即应用阶段，包括应用一个函数，或者说是由一个函数精确表达的计算，该函数为该组生成一个新的单一值。

最后一个阶段，即合并阶段，收集从每组获得的所有结果，并将它们组合起来形成一个新对象。



#### 一个实际示例

刚才你已经看到，pandas 中的数据聚合过程分为几个不同的阶段，称为“拆分-应用-合并”（split-apply-combine）。在这些阶段中，pandas 并非像你预期那样通过显式的函数来表达，而是通过一个 `groupby()` 函数来生成一个 `GroupBy` 对象，该对象是整个流程的核心。

为了更好地理解这一机制，我们来看一个实际示例。首先定义一个包含数值和字符串值的数据框（dataframe）。

```
>>> frame = pd.DataFrame({ 'color': ['white','red','green','red','green'],
...                     'object': ['pen','pencil','pencil','ashtray','pen'],
...                     'price1' : [5.56,4.20,1.30,0.56,2.75],
...                     'price2' : [4.75,4.12,1.60,0.75,3.15]})
>>> frame
color   object  price1  price2
0  white      pen    5.56    4.75
1    red   pencil    4.20    4.12
2  green   pencil    1.30    1.60
3    red  ashtray    0.56    0.75
4  green      pen    2.75    3.15
```

假设你想根据 `color` 列中列出的组标签，计算 `price1` 列的平均值。有几种方法可以实现。例如，你可以访问 `price1` 列，并与 `color` 列一起调用 `groupby()` 函数。

```
>>> group = frame['price1'].groupby(frame['color'])
>>> group
```

你得到的对象是一个 `GroupBy` 对象。在你刚刚执行的操作中，并没有进行任何实际的计算；只是收集了计算平均值所需的所有信息。你所做的是将所有具有相同 `color` 值的行分组为一个单独的项目。

为了详细分析数据框是如何被分成行组的，你需要调用 `GroupBy` 对象的 `groups` 属性。

```
>>> group.groups
{'green': [2, 4], 'red': [1, 3], 'white': [0]}
```

正如你所看到的，每个组都被列出，并明确指定了分配给它们的数据框中的行。现在，只需对组应用操作，就能得到每个组的结果。

```
>>> group.mean()
color
green    2.025
red      2.380
white    5.560
Name: price1, dtype: float64
>>> group.sum()
color
green    4.05
red      4.76
white    5.56
Name: price1, dtype: float64
```

## 分层分组

你已经了解了如何根据某一列的值作为键来选择分组数据。同样的方法可以扩展到多个列，即进行多键的分层分组。

```
>>> ggroup = frame['price1'].groupby([frame['color'],frame['object']])
>>> ggroup.groups
{('green', 'pen'): [4], ('green', 'pencil'): [2], ('red', 'ashtray'): [3], ('red', 'pencil'): [1], ('white', 'pen'): [0]}
>>> ggroup.sum()
color  object
green  pen        2.75
pencil     1.30
red    ashtray    0.56
pencil     4.20
white  pen        5.56
Name: price1, dtype: float64
```

到目前为止，你已经对单个数据列应用了分组，但实际上它可以扩展到多个列或整个数据框。如果你不需要多次重复使用 `GroupBy` 对象，那么将所有需要执行的分组和计算步骤合并到一次操作中，而不定义任何中间变量，会更方便。

```
>>> frame[['price1','price2']].groupby(frame['color']).mean()
price1  price2
color
green   2.025   2.375
red     2.380   2.435
white   5.560   4.750
>>> frame.groupby(frame['color']).mean(numeric_only=True)       price1  price2
color
green   2.025   2.375
red     2.380   2.435
white   5.560   4.750
```

## 分组迭代

`GroupBy` 对象支持迭代，用于生成一个由二元组组成的序列，每个元组包含组名及其对应的数据部分。

```
>>> for name, group in frame.groupby('color'):
...     print(name)
...     print(group)
...
green
color  object  price1  price2
2  green  pencil    1.30    1.60
4  green     pen    2.75    3.15
red
color   object  price1  price2
1   red   pencil    4.20    4.12
3   red  ashtray    0.56    0.75
white
color object  price1  price2
0  white    pen    5.56    4.75
```

这个示例仅用于演示 `print` 变量的操作。实际上，你可以将打印变量的操作替换为你想要应用的函数。

### 转换链

从这些示例中可以看到，对于每次分组，当它被施加某种函数计算或其他一般性操作时，无论分组是如何获得的，也不论选择标准是什么，其结果都是一个序列（如果你选择了单列数据）或一个数据框，并保留其索引系统和列名。

```
>>> result1 = frame['price1'].groupby(frame['color']).mean()
>>> type(result1)
pandas.core.series.Series
>>> result2 = frame.groupby(frame['color']).mean(numeric_only=True)
>>> type(result2)
pandas.core.frame.DataFrame
```

因此，可以在此过程的不同阶段中的任意点选择单个列。以下是三种情况，分别展示了在流程的三个不同阶段选择单个列的方式。这个示例说明了 pandas 提供的该分组系统的巨大灵活性。

```
>>> frame['price1'].groupby(frame['color']).mean()
color
green    2.025
red      2.380
white    5.560
Name: price1, dtype: float64
>>> frame.groupby(frame['color'])['price1'].mean()
color
green    2.025
red      2.380
white    5.560
Name: price1, dtype: float64
>>> (frame.groupby(frame['color']).mean(numeric_only=True))['price1']
color
green    2.025
red      2.380
white    5.560
Name: price1, dtype: float64
```

此外，在进行聚合运算之后，某些列的名称可能意义不大。实际上，为列名添加一个描述聚合类型的前缀往往很有用。添加前缀而不是完全替换名称，对于跟踪生成聚合值的源数据非常有帮助。如果你应用了一个转换链过程（由一个序列或数据框生成另一个），这一点尤其重要，因为需要与源数据保持某种参照关系。

```
>>> means = frame.groupby('color').mean(numeric_only=True).add_prefix('mean_')
>>> means
mean_price1  mean_price2
color
green        2.025        2.375
red          2.380        2.435
white        5.560        4.750
```

### 组上的函数

虽然许多方法并非专门为与 `GroupBy` 一起使用而实现，但它们实际上可以正确地与序列等数据结构配合使用。在上一节中，你看到了通过指定列名然后应用计算方法，可以轻松地从 `GroupBy` 对象获取序列。例如，你可以使用 `quantile()` 函数来计算分位数。

```
>>> group = frame.groupby('color')
>>> group['price1'].quantile(0.6)
color
green    2.170
red      2.744
white    5.560
Name: price1, dtype: float64
```

你也可以定义自己的聚合函数。先单独定义函数，然后将其作为参数传递给 `agg()` 函数。例如，你可以计算每个组值的极差（范围）。

```
>>> def range(series):
...      return series.max() - series.min()
...
>>> group['price1'].agg(range)
color
green    1.45
red      3.64
white    0.00
Name: price1, dtype: float64
```

你还可以同时使用多个聚合函数，通过向 `agg()` 函数传递一个包含待执行操作列表的数组，这些操作将成为新的列。

```
>>> group['price1'].agg(['mean','std',range])
mean       std  range
color
green  2.025  1.025305   1.45
red    2.380  2.573869   3.64
white  5.560       NaN   0.00
```



## 高级数据聚合

本节介绍 `transform()` 和 `apply()` 函数，它们允许你执行多种分组操作，其中一些操作非常复杂。

现在，假设你想将原始数据框（包含数据的数据框）与通过分组聚合计算得到的数据框（例如，求和）合并到同一个数据框中。

```
>>> frame = pd.DataFrame({ 'color':['white','red','green','red','green'],
...                     'price1':[5.56,4.20,1.30,0.56,2.75],
...                     'price2':[4.75,4.12,1.60,0.75,3.15]})
>>> frame
color  price1  price2
0  white    5.56    4.75
1    red    4.20    4.12
2  green    1.30    1.60
3    red    0.56    0.75
4  green    2.75    3.15
>>> sums = frame.groupby('color').sum().add_prefix('tot_')
>>> sums
tot_price1  tot_price2
color
green        4.05        4.75
red          4.76        4.87
white        5.56        4.75
>>> pd.merge(frame,sums,left_on='color',right_index=True)
color  price1  price2  tot_price1  tot_price2
0  white    5.56    4.75        5.56        4.75
1    red    4.20    4.12        4.76        4.87
3    red    0.56    0.75        4.76        4.87
2  green    1.30    1.60        4.05        4.75
4  green    2.75    3.15        4.05        4.75
```

得益于 `merge()`，你可以将聚合结果添加到原始数据框的每一行中。但还有另一种执行此类操作的方法，即使用 `transform()`。此函数执行你之前见过的聚合操作，但同时，它会将基于键值计算出的值显示在原始数据框的每一行上。

```
>>> frame.groupby('color').transform(np.sum).add_prefix('tot_')
tot_price1  tot_price2
0        5.56        4.75
1        4.76        4.87
2        4.05        4.75
3        4.76        4.87
4        4.05        4.75
```

如你所见，`transform()` 方法是一个更专业的函数，具有非常具体的要求：作为参数传递的函数必须生成一个单一的标量值（聚合）以供广播。

适用于更通用 `GroupBy` 的方法是 `apply()`。此方法完整地应用了“拆分-应用-合并”方案。实际上，此函数将对象分割成多个部分以便操作，在每个部分上调用传入的函数，然后尝试将各个部分链接在一起。

```
>>> frame = pd.DataFrame( { 'color':['white','black','white','white','black','black'],
...                      'status':['up','up','down','down','down','up'],
...                      'value1':[12.33,14.55,22.34,27.84,23.40,18.33],
...                      'value2':[11.23,31.80,29.99,31.18,18.25,22.44]})
>>> frame
color status  value1  value2
0  white     up   12.33   11.23
1  black     up   14.55   31.80
2  white   down   22.34   29.99
3  white   down   27.84   31.18
4  black   down   23.40   18.25
>>> frame.groupby(['color','status']).apply( lambda x: x.max())
color status  value1  value2
color status
black down    black   down   23.40   18.25
up      black     up   18.33   31.80
white down    white   down   27.84   31.18
up      white     up   12.33   11.23
5  black     up   18.33   22.44
>>> frame.rename(index=reindex, columns=recolumn)
color   object  value
first    white     ball   5.56
second     red      mug   4.20
third    green      pen   1.30
fourth   black   pencil   0.56
fifth   yellow  ashtray   2.75
>>> temp = pd.date_range('1/1/2015', periods=10, freq= 'H')
>>> temp
DatetimeIndex(['2015-01-01 00:00:00', '2015-01-01 01:00:00',
'2015-01-01 02:00:00', '2015-01-01 03:00:00',
'2015-01-01 04:00:00', '2015-01-01 05:00:00',
'2015-01-01 06:00:00', '2015-01-01 07:00:00',
'2015-01-01 08:00:00', '2015-01-01 09:00:00'],
dtype='datetime64[ns]', freq='H')
Length: 10, Freq: H, Timezone: None
>>> timeseries = pd.Series(np.random.rand(10), index=temp)
>>> timeseries
2015-01-01 00:00:00    0.368960
2015-01-01 01:00:00    0.486875
2015-01-01 02:00:00    0.074269
2015-01-01 03:00:00    0.694613
2015-01-01 04:00:00    0.936190
2015-01-01 05:00:00    0.903345
2015-01-01 06:00:00    0.790933
2015-01-01 07:00:00    0.128697
2015-01-01 08:00:00    0.515943
2015-01-01 09:00:00    0.227647
Freq: H, dtype: float64
>>> timetable = pd.DataFrame( {'date': temp, 'value1' : np.random.rand(10),
...                                       'value2' : np.random.rand(10)})
>>> timetable
date    value1    value2
0 2015-01-01 00:00:00  0.545737  0.772712
1 2015-01-01 01:00:00  0.236035  0.082847
2 2015-01-01 02:00:00  0.248293  0.938431
3 2015-01-01 03:00:00  0.888109  0.605302
4 2015-01-01 04:00:00  0.632222  0.080418
5 2015-01-01 05:00:00  0.249867  0.235366
6 2015-01-01 06:00:00  0.993940  0.125965
7 2015-01-01 07:00:00  0.154491  0.641867
8 2015-01-01 08:00:00  0.856238  0.521911
9 2015-01-01 09:00:00  0.307773  0.332822
```

然后，你向数据框中添加一个列，该列表示一组文本值，你将把它们用作键值。

```
>>> timetable['cat'] = ['up','down','left','left','up','up','down','right','right','up']
>>> timetable
date    value1    value2    cat
0 2015-01-01 00:00:00  0.545737  0.772712     up
1 2015-01-01 01:00:00  0.236035  0.082847   down
2 2015-01-01 02:00:00  0.248293  0.938431   left
3 2015-01-01 03:00:00  0.888109  0.605302   left
4 2015-01-01 04:00:00  0.632222  0.080418     up
5 2015-01-01 05:00:00  0.249867  0.235366     up
6 2015-01-01 06:00:00  0.993940  0.125965   down
7 2015-01-01 07:00:00  0.154491  0.641867  right
8 2015-01-01 08:00:00  0.856238  0.521911  right
9 2015-01-01 09:00:00  0.307773  0.332822     up
```

然而，这里展示的示例具有重复的键值。

## 总结

在本章中，你了解了划分数据处理阶段的三个基本部分：准备、处理和聚合。通过一系列示例，你学习了一组库函数，这些函数使 pandas 能够执行这些操作。

你了解了如何在简单数据结构上应用这些函数，以便熟悉它们的工作原理，并理解它们对更复杂情况的适用性。你现在具备了为数据分析的下一个阶段准备数据集所需的知识：数据可视化。

下一章将介绍 Python 库 `matplotlib`，它可以将数据结构转换为任何图表。

