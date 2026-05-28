# 5. pandas 中的基本数据转换

pandas 库拥有一个庞大的 API，提供了多种数据转换方法。在本章中，我们将介绍一些在 pandas 中转换数据最强大、最流行的方法。

## 数据透视与数据透视表

数据透视（`pivot`）和数据透视表（`pivot_table`）非常流行，尤其对初学者具有吸引力，因为它们功能强大。然而，它们的强大功能是以性能为代价的。虽然 `pivot` 是作为数据规范化步骤的一部分来初步转换 DataFrame 的优秀工具，但在数据分析阶段不应频繁使用。列表 5-1 展示了如何使用 `pivot_table` 将原始检查数据转换为餐厅及其平均检查分数的聚合格式。请注意，在列表 5-1 中，聚合函数被显式指定为 `np.mean`，尽管这是不必要的，因为 `np.mean` 是默认值。`pivot` 本质上是在执行 `groupby`，根据需要应用聚合函数，并将结果重新组织成新的表格格式。

```python
>> df
restaurant  location    date       score
Diner       (4, 2)      02/18      90
Pandas      (5, 4)      04/18      55
Diner       (4, 2)      05/18      100
Pandas      (5, 4)      01/18      76
>> df = df.pivot_table(
values=['score'],
index=["restaurant","location"],
aggfunc=np.mean
)
>> df
score
restaurant  location
Diner       (4, 2)         95
Pandas      (5, 4)         66
```

*列表 5-1 使用 `pivot_table` 计算每家餐厅的平均检查分数*

列表 5-1 中存在一些性能问题。`pivot_table` 没有限制内存复制的选项，因此每次使用时都会创建一个全新的 DataFrame。如果你的 DataFrame 非常大，这可能会对程序性能造成重大影响。在内部，`pivot_table` 会按唯一的餐厅和位置组合对数据进行分组，这需要时间，特别是在组合数量很大的情况下。如果这是作为数据规范化步骤的一部分使用的，那么它比在程序的数据分析过程中多次使用要好得多。这是因为唯一分组和复制所有内存的性能损失只会发生一次，而不是在程序中发生多次。最好一次性规范化和定位 DataFrame，使其能够优化你计划对其执行的所有分析，而不是将其保留在某种未优化的原始形式中，并在每个分析步骤中重新定位它。请注意，如果 DataFrame 已经按唯一值分组，我们可以像列表 5-2 那样运行 `groupby` 来计算平均分数，这将会快两倍。我们将在章节 7 中深入讨论 `groupby` 的性能。其他分析很可能需要按唯一餐厅分组的数据，因此此示例中的分组至少应该是规范化步骤的一部分，这样就没有必要使用 `pivot_table` 了。

```python
>> df
date        score
restaurant  location
Diner       (4, 2)     02/18       90
04/18       55
Diner       (4, 2)     05/18       100
01/18       76
>> df = df[["score"]].groupby(["restaurant","location"]).mean()
>> df
restaurant  location     score
Diner       (4, 2)       95
Pandas      (5, 4)       66
```

*列表 5-2 使用 `groupby` 计算每家餐厅的平均检查分数*

### Pivot 与 Pivot Table 的对比与重塑操作

`Pivot`的功能与`pivot table`相同，但不允许聚合数据。当使用`pivot table`时，任何导致多个值的列和索引组合必须被聚合在一起。而`pivot`在遇到这种情况时，会直接抛出`ValueError`。请注意，在清单 5-3 中，没有药物和日期的组合会产生多个值；然而在清单 5-4 中，同一药物和日期存在或将会出现多行数据，因此清单 5-4 会抛出`ValueError`。因此，`pivot`和`pivot table`的一个遗憾的局限是，当某个索引和列组合存在多个值时，它们都无法输出数据。`Pivot table`强制你对多个值进行聚合或选择一个，而`pivot`则直接抛出`ValueError`。

```
>> df
date          tumor_size    drug      dose
02/18         90            01384     10
02/25         80            01384     10
03/07         65            01384     10
03/21         60            01384     10
02/18         30            01389     7
02/25         20            01389     7
03/07         25            01389     7
03/21         25            01389     7
>> df.pivot(
index="drug",
columns="date",
values="tumor_size",
)
ValueError: Index contains duplicate entries, cannot reshape
Listing 5-4
当同一列索引组合存在多个值时，Pivot 会抛出 ValueError
```

```
>> df
date           tumor_size      drug         dose
02/18          90              01384        10
02/25          80              01384        10
03/07          65              01384        10
03/21          60              01384        10
02/18          30              01389        7
02/25          20              01389        7
03/07          25              01384        10
03/21          25              01389        7
>> df.pivot(
index="drug",
columns="date",
values="tumor_size"
)
date    02/18    02/25    03/07     03/21
drug
01384   90        80       65         60
01389   30        20       25         25
Listing 5-3
使用 pivot 重新定向 DataFrame
```

`Pivot`比`pivot table`性能更高，因为它不允许指定和生成多级列和多级索引。因此，它没有生成和处理这种更复杂`DataFrame`格式的开销。无论生成的`DataFrame`是否是多级索引或多级列`DataFrame`，`pivot table`仍然会像处理多级结构一样运行各种计算，这会增加相当多的开销，在某些情况下甚至比`pivot`多出六倍。虽然`pivot`允许你指定多个值并为其创建多级列，但它不允许你提供显式的列列表来生成多级列，或提供索引列表来生成多级索引。而`pivot table`支持这种多级`DataFrame`。它还有一些其他便利选项，例如添加所有行和列的小计，以及删除包含`NaN`的列。总之，如果你能使用`pivot`，就应该使用它，因为它比`pivot table`性能更高。

### Stack 和 Unstack

`Stack`和`unstack`将`DataFrame`的列层级重塑为最内层索引，反之亦然。清单 5-5 中展示了一个示例，其中每一列是一次餐厅卫生检查，值是卫生检查分数，索引代表被检查的餐厅。`Stack`用于重塑数据，使得每家餐厅的卫生检查分数出现在每一行中，而不是每一列中。请注意，`stack`会将顶部的列名转换为列值，这些值最终会从`DataFrame`中被删除。

# 数据重塑：`stack`、`unstack`、`melt` 与 `Transpose`

## 使用 `stack` 重塑

```
>> df
score
inspection                       0    1
restaurant  location
Diner       (4, 2)               90   100
Pandas      (5, 4)               55   76
>> df = df.stack().reset_index()
>> df
restaurant  location   inspection  score
Diner       (4, 2)      0          90
1          100
Pandas      (5, 4)      0          55
1          76
>> df.drop(column=["inspection"], inplace=True)
>> df.set_index(["restaurant", "location"], inplace=True)
>> df
score
restaurant  location
Diner       (4, 2)     90

Pandas      (5, 4)     55
```

**Listing 5-5**
使用 `stack` 重塑 `DataFrame`，使每一行代表一次检查

你可能认出了清单 5-5 中原始 `DataFrame` 的形状与清单 3-22 中的形状一致。在清单 5-5 中重塑之前的 `DataFrame` 形状，正是第 3 章末尾“选择正确的 `DataFrame`”部分中被认为最优的朝向。清单 5-5 展示了如何从该最优形状转换为原始的非最优形状。现在，我们来看如何将原始的非最优形状转换为最优形状。清单 5-6 向 `DataFrame` 添加了一个名为 `inspection` 的新列，其值将成为新 `DataFrame` 的列名。我们还使用了一个方便的 `groupby` 聚合函数 `cumcount`，它为每个组中的每一行创建一个行号。

## 使用 `unstack` 重塑

```
>> df
score
restaurant  location
Diner       (4, 2)      90

Pandas      (5, 4)      55

>> df["inspection"] = df.groupby(
["restaurant", "location"]).cumcount()
>> df
inspection  score
restaurant  location
Diner       (4, 2)    0          90
1          100
Pandas      (5, 4)    0          55
1          76
>> df.set_index("inspection", append=True, inplace=True)
>> df
score
restaurant  location   inspection
Diner        (4, 2)    0           90
1           100
Pandas        (5, 4)   0           55
1           76
>> df = df.unstack()
>> df
score
inspection                         0    1
restaurant  location
Diner       (4, 2)                 90   100
Pandas      (5, 4)                 55   76
```

**Listing 5-6**
使用 `unstack` 重塑 `DataFrame`，使每一列代表一次检查

那么 `stack` 和 `unstack` 的性能如何？它们都会复制内存，因为它们不是原地操作，这可能会很昂贵，因此只应在数据规范化时使用。然而，它们在数据转换方式上非常独特，因此很难找到比 `melt` 性能更高的替代方法，我们将在下一节中探讨 `melt`。

## Melt

清单 5-7 中展示了使用 `melt` 的示例。请注意，这与 `stack` 的示例非常相似。在这个示例中，我们基本上是用一行代码完成了之前用 `stack` 大约四行代码完成的工作。虽然 `melt` 的功能与 `stack` 相同，甚至更多，但它的执行方式性能稍高。这主要是因为它在高层调用各种数据转换时开销略小，也就是说，`melt` 不是调用底层的 `stack`，而是直接在底层执行 `stack` 之下的数据操作，从而避免了中间代码层。如果将原始的 `stack` 与 `melt` 进行对比，`stack` 大约快四倍。使用 `stack` 的缺点在于，它通常需要其他操作，例如设置索引、重命名列、将其转换回 `DataFrame` 等。这意味着在某些情况下，直接使用 `melt` 性能更高。

```
>> df
restaurant  location    0    1
Diner       (4, 2)      90   100
Pandas      (5, 4)      55   76
>> df = df.melt(
id_vars=["restaurant","location"],
value_vars=[0,1],
value_name="score").drop(columns="variable")
>> df
restaurant  location      score
Diner       (4, 2)        90

Pandas      (5, 4)        55
```

**Listing 5-7**
使用 `melt` 重塑 `DataFrame`，使每一行代表一次检查

## 转置

`Transpose` 是一个有用的技巧。它简单地将列转换为行，将行转换为列。在清单 5-8 中，有一个需要针对某种疾病进行治疗的患者列表，以及一个根据血型提供用于治疗该疾病的药物列表。我们需要根据患者的血型，将可用于治疗该患者的药物列表添加到患者表中。第一步是按血型对患者列表和药物表进行索引，然后我们可以执行简单的连接操作，将药物数据添加到患者列表中。由于药物表的方向是血型横跨列而非行，因此我们首先执行转置操作。注意，当我们执行此操作时，创建 `DataFrame` 时默认提供的索引会变成列，而列直接变成索引。这意味着在清单 5-8 中，我们无需显式设置索引，因为转置操作已经将索引设置为血型。

```
>> patient_list
id      history
blood_type
0+            02394   hbp
B+            02312   NaN
0-            23409   lbp
>> drug_table
index         0+   0-   A+   A-   B+   B-
0             ADF  ADF  ACB  DCB  ACE  BAB
1             GCB  RAB  DF   EFR       HEF
2             RAB
>> drug_table = drug_table.transpose(copy=False)
>> drug_table
blood_type   0     1      2
0+           ADF   GCB    RAB
0-           ADF   RAB
A+           ACB   DF
A-           DCB   EFR
B+           ACE
B-           BAB   HEF
>> patient_list.join(drug_table)
id       history    0      1      2
blood_type
0+               02394   hbp        ADF    GCB    RAB
B+               02312   NaN               ACE
0-               23409   lbp               ADF    RAB
```

**Listing 5-8**
使用转置来重塑 `DataFrame`

`Transpose` 是少数几个提供不重复数据选项（名为 `copy`）的 `DataFrame` 重塑函数之一。话虽如此，`copy=False` 并不一定意味着数据不会被重复，我们将在第 9 章中更详细地解释。数据是否被重复取决于多种因素，这些因素最终归结为数据的新形状是否可以按原样重用底层的 `NumPy` 数组，还是必须创建新的 `NumPy` 数组。回想一下，`NumPy` 数组必须都是相同类型。这意味着如果你要转置的 `DataFrame` 的行和列具有相同的类型，那么它很可能能够重用现有的 `NumPy` 数组。如果不是，它将不得不复制内存并重新构建它们。这意味着 `Transpose` 应该仅在绝对必要时使用，理想情况下作为数据标准化步骤的一部分。

从所有这些数据转换函数中得出的结论是，数据转换的成本非常高，在理想的程序中应该只在标准化阶段进行。在数据分析过程中应谨慎使用。如果你发现在每个数据分析步骤中都需要进行大量转换，那么你应该重新思考标准化后的 `DataFrame` 的方向。在下一章中，我们将探讨 `apply` 方法，并研究何时应该使用以及何时不应该使用它，同时还会介绍性能更高的替代方案。