# 2. 基础数据访问与合并

使用 `pandas` 访问和合并 `DataFrames` 有多种方法。本章将介绍从 `DataFrame` 中获取数据、创建子 `DataFrame` 以及合并 `DataFrames` 的基本方法。

## DataFrame 的创建与访问

`pandas` 采用类似字典的语法，这对于熟悉 Python 但不熟悉 `pandas` 的用户来说非常直观。每个列名被视为一个键，行值则作为对应的值返回。`DataFrame` 对象的构造函数也支持以字典形式创建 `DataFrame`。需要注意的是，当你从 `DataFrame` 获取某一列时，该列指向原始的 `DataFrame`，这正是我们能够对原始数据进行修改的原因。即使语法上看起来像是在将数据存储到原始数据的子集中（如代码清单 2-1 底部所示），实际上也是如此。这种机制有利于内存性能，因为我们不必频繁地创建数据的副本。

```
>> import pandas as pd
>> account_info = pd.DataFrame({
"name": ["Bob", "Mary", "Mita"],
"account": [123846, 123972, 347209],
"balance": [123, 3972, 7209],
})
>> account_info["name"]
0    Bob
1    Mary
2    Mita
Name: name, dtype: object
>> account_info["name"] = ["Smith", "Jane", "Patel"]
>> account_info
name  account     balance
0    Smith 123846       123
1    Jane  123972       3972
2    Patel 347209       7209
代码清单 2-1
字典语法示例
```

类似地，也可以通过传入一个列名列表来创建子`DataFrame`，如代码清单 2-2 所示。

```
>> import pandas as pd
>> account_info = pd.DataFrame({
"name": ["Bob", "Mary", "Mita"],
"account": [123846, 123972, 347209],
"balance": [123, 3972, 7209],
})
>> account_info[["name", "balance"]]
name  balance
0     Bob   123
1     Mary  3972
2     Mita  7209
代码清单 2-2
创建子 DataFrame 示例
```

如果从原始 `DataFrame` 创建子`DataFrame`，并期望修改子`DataFrame` 时原始数据不受影响，那么字典语法可能在不经意间导致混淆。`pandas` 无法保证在代码清单 2-1 和 2-2 所展示的简单情况之外，字典语法返回的对象究竟是视图还是副本。这也正是对于具有多重索引或多级列标的 `DataFrame`，我们更推荐使用 `loc` 方法而非字典语法的原因。我们将在下一节讨论的 `loc` 方法，能够确保你在原始 `DataFrame` 上操作，而非操作副本。同理，如果你确实需要一份 `DataFrame` 的副本，应该显式地创建它。

### `iloc` 方法

`DataFrame` 的行可以通过 `iloc` 方法访问，该方法采用类似列表的语法。代码清单 2-3 演示了这一用法。

```
>> import pandas as pd
>> account_info = pd.DataFrame({
"name": ["Bob", "Mary", "Mita"],
"account": [123846, 123972, 347209],
"balance": [123, 3972, 7209],
})
>> account_info.iloc[1]
name        Mary
account     123972
balance     3972
>> account_info.iloc[0:2]
name  account  balance
0    Bob   123846   123
1    Mary  123972   3972
>> account_info.iloc[:]
name  account  balance
0    Bob   123846   123
1    Mary  123972   3972
2    Mita  347209   7209
代码清单 2-3
使用 iloc 访问 DataFrame 行示例
```

`iloc` 用于通过基于整数位置的索引来索引 `DataFrame`。`iloc` 函数中的第一个位置指定行索引，第二个位置指定列索引。这意味着我们可以像代码清单 2-4 那样同时选择行和列。

```
>> import pandas as pd
>> account_info = pd.DataFrame({
"name": ["Bob", "Mary", "Mita"],
"account": [123846, 123972, 347209],
"balance": [123, 3972, 7209],
})
>> account_info.iloc[1, 2]

>> account_info.iloc[1, 2] = 3975
>> account_info.iloc[1, 2]

>> account_info.iloc[:, [0, 2]]
name  balance
0    Bob   123
1    Mary  3975
2    Mita  7209
代码清单 2-4
使用 iloc 访问 DataFrame 行和列示例
```

`iloc` 也接受布尔数组。在代码清单 2-5 中，我们通过对每个行索引取模并转换为布尔值，来获取所有奇数行。

```
>> import pandas as pd
>> account_info = pd.DataFrame({
"name": ["Bob", "Mary", "Mita"],
"account": [123846, 123972, 347209],
"balance": [123, 3972, 7209],
})
>> account_info.iloc[account_info.index % 2 == 1]
name  account    balance
1    Mary  123972     3972
代码清单 2-5
使用 iloc 访问 DataFrame 行和列示例
```

`iloc` 也可以接受一个函数；不过，该函数只会被调用一次，传入整个 `DataFrame`。将其直接传入与在调用 `iloc` 之前简单地调用该函数相比，差别不大，因此本文不再赘述。

在处理多重索引和多级列标的 `DataFrame` 时，`iloc` 非常实用，因为级别本身就是整数值。我们来回顾一个示例并进行解析。这里我们通过 `":"` 指定要获取所有行，并使用一个布尔数组来指定列。我们获取多级列标 `"data"` 下的值，即 `["score", "date", "score", "date"]`，然后通过指定值必须等于 `"score"` 来创建一个布尔数组。代码清单 2-6 将此过程分阶段拆解，以便于理解。

```
>> restaurant_inspections
inspection             0                1
data                 score date       score date
restaurant location
Diner      (4, 2)    90    02/18      100   05/18
Pandas     (5, 4)    55    04/18      76    01/18
>> score_columns = (
restaurant_inspections.columns.get_level_values("data")
== "score")
>> score_columns
[True, False, True, False]
>> restaurant_inspections.iloc[:, score_columns]
inspection                0          1
data                      score      score
restaurant location
Diner      (4, 2)         90         100
Pandas     (5, 4)         55         76
代码清单 2-6
使用 iloc 从多重索引多级列标 DataFrame 中提取子 DataFrame
```

### `loc` 方法

`loc` 与 `iloc` 类似，但它允许你通过列名或标签来索引`DataFrame`。列表 2-7 展示了与列表 2-4 功能等效的`loc`用法。

```
>> import pandas as pd
>> account_info = pd.DataFrame({
"name": ["鲍勃", "玛丽", "米塔"],
"account": [123846, 123972, 347209],
"balance": [123, 3972, 7209],
})
>> account_info.loc[1, "balance"]

>> account_info.loc[:, ["name", "balance"]]
name  balance
0    鲍勃   123
1    玛丽  3972
2    米塔  7209
```
列表 2-7
使用 `loc` 访问 `DataFrame` 中行和列的示例

`loc` 也可以用于多级索引、多层列的 `DataFrames`，并且与 `iloc` 一样支持布尔型数组。列表 2-8 演示了这一点。

```
>> import pandas as pd
>> account_info
account         0                    1
account_info    number   balance     number   balance
name  username
鲍勃   smithb    123846   123         123847   450
玛丽  mj100     123972   3972        123973   222
米塔  patelm    347209   7209
>> account_info.loc[
("玛丽", "mj100"), pd.IndexSlice[:, "balance"]
]
0    balance    3972
1    balance    222
```
列表 2-8
使用 `loc` 从多级索引、多层列的 `DataFrame` 中提取子 `DataFrame` 的示例

在字典语法部分的末尾提到，对于复杂的 `DataFrames`，`loc` 方法优于字典语法。让我们看看使用每种语法时底层发生了什么，以解释原因。列表 2-9 展示了在操作更复杂的 `DataFrame` 时，每种访问方法在底层转换成了什么。请注意，在列表 2-9 的后半部分使用字典语法时，底层代码使用了 `__getitem__` 方法，然后在其上调用 `__setitem__`。这与直接调用 `__setitem__` 的 `loc` 方法相反。这里不可靠的是 `__getitem__`，它无法保证返回的是副本还是指向原始 `DataFrame` 的所谓视图。在列不涉及多级的简单情况下，这两种情况的底层代码看起来相同，但在此处这种更复杂的情况中，字典语法会导致链式索引，并调用不可预测的 `__getitem__`。

```
"""
下面的代码等效于：
account_info.__setitem__(
(slice(None), (0, 'balance')),
NEW_BALANCE,
)
"""
account_info.loc[:, (0, "balance")] = NEW_BALANCE
"""
下面的代码等效于：
account_info.__getitem__(0).__setitem__('balance', NEW_BALANCE )
"""
account_info[0]["balance"] = NEW_BALANCE
```
列表 2-9
使用 `loc` 与字典语法提取子 `DataFrame` 的对比

很多时候，你可能需要将来自多个来源的数据合并到一个 `DataFrame` 中。现在你已经了解了一些基本的数据访问方法，接下来我们将研究将不同 `DataFrames` 的数据合并在一起的几种不同方法。

## 使用 `merge` 方法合并 DataFrames

`Merge` 的工作方式与关系数据库连接相同，甚至具有熟悉的选项：外连接、内连接、左连接和右连接。右连接本质上与左连接相同，只是 `DataFrames` 的传入顺序相反，因此本章不会为右连接提供显式示例。

当你想要查找两个 pandas `DataFrames` 的交集时，会使用内连接（图 2-1）。例如，在列表 2-10 中，我们试图查找两个数据集中都存在的建筑，即那些建于 1844 年且至今仍屹立不倒的建筑。

![../images/487367_1_En_2_Chapter/487367_1_En_2_Fig1_HTML.png](img/487367_1_En_2_Fig1_HTML.png)

图 2-1
内连接的维恩图

```
>> import pandas as pd
>> building_records_1844
established
building
大饭店             1830
琼斯农场           1842
公共图书馆         1836
玛丽埃塔之家       1823
>> building_records_2020
established
building
萨姆面包店         1962
大饭店             1830
公共图书馆         1836
梅伯里工厂         1924
>> cols = building_records_2020.columns.difference(
building_records_1844.columns
)
>> pd.merge(
building_records_1844,
building_records_2020[cols],
how='inner',
on=["building"],
)
established
building
大饭店             1830
公共图书馆         1836
```
列表 2-10
使用内连接查找至今仍矗立的 1844 年建筑

在列表 2-11 中，我们将两个基因样本数据集合并在一起，这意味着我们希望将两个数据集的所有数据都包含在同一个数据集中，且不重复。我们可以通过执行外连接来实现这一点（图 2-2）。

![../images/487367_1_En_2_Chapter/487367_1_En_2_Fig2_HTML.png](img/487367_1_En_2_Fig2_HTML.png)

图 2-2
外连接的维恩图

```
>> import pandas as pd
>> gene_group1
FC1           P1
id
Myc          2             0.05
BRCA1        3             0.01
BRCA2        8             0.02
>> gene_group2
FC2           P2
id
Myc          2             0.05
BRCA1        3             0.01
Notch1       2             0.03
BRCA2        8             0.02
>> pd.merge(
gene_group1,
gene_group2,
how='outer',
on=["id"],
)
FC1          P2            FC2           P2
id
Myc          2            0.05          2             0.05
BRCA1        3            0.01          3             0.01
BRCA2        8            0.02          8             0.02
Notch1       NaN          NaN           2             0.03
```
列表 2-11
使用外连接合并两个基因样本集，避免公共数据重复

在清单 2-12 中，我们正在使用更准确的历史数据更新现代建筑记录。历史记录包含确切的建成日期，我们希望用其更新仅包含估算日期的现代记录。首先，我们使用左合并将更准确的新建成日期作为一个新列添加到数据集中（图 2-3）。在这种情况下，左合并非常有用，因为我们只关心更新现存建筑的现代记录中的建成日期。注意，我们还利用了 `suffixes` 参数为列名提供名称。这样做的好处是，在完成操作后，我们不必将列重命名为与原始列相同的名称。合并完成后，我们需要将两个 `established` 列的数据合并在一起。这是通过用现代记录中的值替换旧 `established` 列中的所有缺失值（即 `NaN`）来完成的。因此，如果历史记录有建成日期，我们就使用它；否则，我们回退到现代记录的建成日期。最后，删除现代记录中原始的 `established` 列，转而使用包含来自现代记录和历史记录合并值的新列。

![../images/487367_1_En_2_Chapter/487367_1_En_2_Fig3_HTML.png](img/487367_1_En_2_Fig3_HTML.png)

图 2-3
左合并的维恩图
```

# 使用`join`方法合并`DataFrame`

## 清单 2-12

使用左合并以更准确的历史数据更新现代记录

```python
>> import pandas as pd
>> building_records_1844
building          established
Grande Hotel      1832
Jone's Farm       1842
Public Library    1836
Marietta House    1823
>> building_records_2020
building             established
Sam's Bakery         1962
Grande Hotel         1830
Public Library       1836
Mayberry's Factory   1924
>> merged_records = pd.merge(
building_records_2020,
building_records_1844,
how='left',
right_on="building",
left_on="building",
suffixes=("_2000", ""),
)
>> merged_records
building             established_2000     established
Sam's Bakery         1962                 NaN
Grande Hotel         1830                 1832
Public Library       1835                 1836
Mayberry's Factory   1924                 NaN
>> merged_records["established"].fillna(
merged_records["established_2000"],
inplace=True,
)
>> del merged_records["established_2000"]
>> merged_records
building             established
Sam's Bakery         1962
Grande Hotel         1832
Public Library       1836
Mayberry's Factory   1924
```

## 图 2-4

反连接的维恩图

![../images/487367_1_En_2_Chapter/487367_1_En_2_Fig4_HTML.png](img/487367_1_En_2_Fig4_HTML.png)

## 清单 2-13

使用反连接合并方法消除同时参与两项试验的患者

在清单 2-13 中，我们计划进行第三次医疗试验，并且希望生成一份符合条件的参与者名单。只有参与过之前的试验 a 或 b（但不能同时参与两者）的参与者才有资格参加第三次试验。为了生成符合条件的患者名单，在合并试验 a 和试验 b 的患者数据时，我们需要使用反连接方法（图 2-4）。`pandas`的`merge`方法提供了一个名为`indicator`的参数，它会在结果`DataFrame`中添加一个名为`_merge`的附加列，用于报告键是仅存在于左侧（`left_only`）、仅存在于右侧（`right_only`），还是两个`DataFrame`中都有（`both`）。在我们希望执行某种非常规合并的特定情况下，这非常有用。通过使用`query`方法，我们可以选择`_merge`值不是`both`的行，然后删除`_merge`列。这可以像清单 2-13 末尾所示在一行内完成，但为了让你了解其工作原理，我们先将其分解为两个步骤。

```python
>> import pandas as pd
>> trial_a_records
name
patient
230858       John
237340       May
240932       Catherine
124093       Ahmed
>> trial_b_records
name
patient
210858       Abi
237340       May
240932       Catherine
154093       Julia
>> both_trials = pd.merge(
trial_a_records,
trial_b_records,
how='outer',
indicator=True,
right_index=True,
left_index=True,
on="name",
)
name            _merge
patient
230858       John            left_only
237340       May             both
240932       Catherine       both
124093       Ahmed           left_only
210858       Abi             right_only
154093       Julia           right_only
>> both_trials.query('_merge != "both"').drop('_merge', 1)
name
patient
230858       John
124093       Ahmed
210858       Abi
154093       Julia
>> both_trials = pd.merge(
trial_a_records,
trial_b_records,
how='outer',
indicator=True,
right_index=True,
left_index=True,
on="name",
).query('_merge != "both"').drop('_merge', 1)
```

`pandas`的`join`方法只是`merge`的一个包装器，它提供相同的基本合并方法：左连接、右连接、外连接和内连接。它允许你自动对多索引`DataFrame`执行合并操作，而无需指定要合并的索引。执行左连接时，它会自动使用左侧`DataFrame`的索引进行连接；对于右侧`DataFrame`也是如此。由于`join`是在`DataFrame`上执行的，而`merge`则需要显式传入两个`DataFrame`，因此`join`的默认行为是合并到“右侧”`DataFrame`的索引上。在这种情况下，“右侧”是传入的`DataFrame`。这与`merge`的默认行为（内连接）相反。

使用`merge`时，可以不指定显式的键进行合并。在合并两个具有相同数据的`DataFrame`，并且不希望左右`DataFrame`出现重复列，而只是简单地将两个数据集合并在一起的情况下，`merge`比`join`更可取。因为`join`在底层调用了`merge`，它显式地指定了要合并的键，从而消除了对于共享相同列名的`DataFrame`不输出重复列的可能性。这里要遵循的基本规则是：如果不在索引上进行连接，则使用`merge`。

清单 2-14 沿用了之前清单 2-10 中的一个内连接合并示例，但与之前公共建筑记录完全匹配的示例不同，这次存在差异。在这种情况下，使用`join`有几个好处。首先，数据已经根据唯一的建筑名称建立了索引，`join`会自动识别这些索引并用于连接这两个数据集。其次，数据中存在差异，因此我们希望输出`DataFrame`能够并排显示两个`DataFrame`的列，以便进行比较。

# 使用 `concat` 方法合并 DataFrame

`Concatenate` 是一种简单的合并两个 DataFrame 的方式。清单 2-15 演示了如何将来自多个数据源的相同数据进行简单拼接。`Concatenate` 有许多选项，包括 `join` 参数（指定使用外合并还是内合并）和 `axis` 参数（指定是按列 `axis=1` 合并，还是按行 `axis=0` 合并）。默认情况下，`concatenate` 会按行进行外合并。请注意，在清单 2-15 中，位置 `(6,4)` 同时出现在 `county_a` 和 `county_b` 数据中，并且在合并后的结果中，该位置的索引被重复了。

```
>> import pandas as pd
>> temp_county_a
temp
location
(4,5)          35.6
(1,2)          37.4
(6,4)          36.3
(1,7)          40.2
>> temp_county_b
temp
location
(6,4)          34.2
(0,4)          33.7
(3,8)          38.1
(1,5)          37.0
>> pd.concat([temp_county_a, temp_county_b])
temp
location
(4,5)          35.6
(1,2)          37.4
(6,4)          36.3
(1,7)          40.2
(6,4)          34.2
(0,4)          33.7
(3,8)          38.1
(1,5)          37.0
清单 2-15
使用 concat 合并两个 DataFrame
```

`Concatenate` 还可以用于更复杂的场景，例如创建多级列或多级索引。清单 2-16 演示了一个拼接操作，其中被拼接的每个 `DataFrame` 都是多级列中的一个值。请注意，在清单 2-16 中，`device_a` 和 `device_b` 的数据是相同位置上的温度测量值。这里我们指定 `axis=1`，使两个 DataFrame 按列进行外合并。清单 2-16 中的 `key` 参数指示 `concatenate` 将这两个温度列视为不同的列（即使它们名称相同），并由此创建一个多级列。按列进行外合并，并将每个设备的温度放入不同的列中，意味着合并后的结果中不会出现重复的位置索引值。这与清单 2-15 形成对比——在清单 2-15 中，结果中的索引值被重复，两个 DataFrame 只是简单堆叠在一起。

```
>> import pandas as pd
>> temp_device_a
temp
location
(4,5)          35.6
(1,2)          37.4
(6,4)          36.3
(1,7)          40.2
>> temp_device_b
temp
location
(4,5)          34.2
(1,2)          36.7
(6,4)          37.1
(1,7)          39.0
>> pd.concat(
[temp_device_a, temp_device_b],
keys=["device_a", "device_b"],
axis=1,
)
device_a       device_b
temp           temp
location
(4,5)         35.6           34.2
(1,2)         37.4           36.7
(6,4)         36.3           37.1
(1,7)         40.2           39.0
清单 2-16
使用多级列 concat 合并两个 DataFrame
```

在 `pandas` 中，合并 DataFrame 和提取子 DataFrame 的方法有很多种。选择哪种方法很大程度上取决于你的具体使用场景。这里给出的示例具有一定的代表性，但你应该查阅每个方法的文档，因为本章并未明确涵盖某些参数（例如排序）^(³)。

脚注 1

---
**清单 2-14**

使用内连接突出显示 1844 年建成并于 2020 年仍存在的建筑在建成日期上的差异

```
>> import pandas as pd
>> building_records_1844
established
building         location
Grande Hotel     (4,5)        1831
Jone's Farm      (1,2)        1842
Public Library   (6,4)        1836
Marietta House   (1,7)        1823
>> building_records_2020
established
building           location
Sam's Bakery        (5,1)        1962
Grande Hotel        (4,5)        1830
Public Library      (6,4)        1835
Mayberry's Factory  (3,2)        1924
>> building_records_1844.join(
building_records_2020,
how='inner',
rsuffix="_2000",
)
established   established_2000
building          location
Grande Hotel      (4,5)     1831          1830
Public Library    (6,4)     1836          1835
```