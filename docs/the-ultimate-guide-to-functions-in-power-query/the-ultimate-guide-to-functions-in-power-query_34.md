# 列表函数

`{1,4,3}`是`{1,2,1,4,3}`。但为了研究更高级的列表合并场景，应使用以下函数。

## `List.Combine`

该函数用于合并两个或多个列表，语法如下：`List.Combine(lists as list) as list`

该函数接收一个输入，格式为`list`，因此所有要合并的列表必须放置在一个引用列表内作为该函数的输入。然后，在该函数的输出中，引用列表内提供的所有列表会被合并，并显示在一个新列表中。

因此，以下公式的结果等于`{1,2,1,4,3}`：

```
= List.Combine({{1,2},{1,4,3}})
```

而以下公式的结果等于`{1,2,1,3,1,3}`：

```
= List.Combine({ {1,2},{1,3},{1,3}})
```

## `List.InsertRange`

要在特定位置向列表添加新值（值列表），可以使用`List.InsertRange()`，语法如下：

```
List.InsertRange(
    list as list,
    index as number,
    values as list
) as list
```

该函数接收三个输入：

1. 第一个输入表示引用列表。
2. 该函数的第二个输入通过输入一个数字来确定添加新值的位置。
3. 要添加到引用列表的值以列表形式插入到该输入中。

基于函数说明，该函数在不同公式中的结果如表 6-18 所示。

**表 6-18.** `List.InsertRange`示例

| 公式 | 结果 |
|------|------|
| `= List.InsertRange({"abc","bcf","deg","z"},2,{"b","a"})` | `{"abc","bcf","b","a","deg","z"}` |
| `= List.InsertRange({1..5},2,{1..3})` | `{1,2,1,2,3,3,4,5}` |
| `= List.InsertRange({1..5},1,{1..3})` | `{1,1,2,3,1,3,4,5}` |

## `List.Zip`

要合并多个列表中对应的值，可以使用`List.Zip()`，语法如下：

`List.Zip(lists as list) as list`

该函数接收一个列表形式的输入。输入的列表包含多个列表，并且在输出中它们对应的值会各自聚合到单独的列表中。

例如，在第一个输入中输入`{{1..3},{4..5}}`的结果是`{{1,4},{2,5},{3,null}}`。

类似地，公式`= List.Zip({{1..4},{3..5},{2..7}})`的结果等于以下列表：

```
{ {1,3,2},{2,4,3},{3,5,4},{4,null,5},{null,null,6},{null,null,7} }
```

## `List.Union`

`List.Union`的语法如下，用于合并两个或多个列表，功能与`List.Combine`类似。区别在于该函数会忽略重复值（在所有列表中重复出现的值）。

```
List.Union(
    lists as list,
    optional equationCriteria as any
) as list
```

该函数接收以下两个输入：

1. 该函数的第一个输入是引用列表，包含要合并的列表。
2. 该函数的第二个输入允许你指定用于比较值的自定义条件。

因此，对于不同示例，该函数的结果如表 6-19 所示。

**表 6-19.** `List.Union`示例

| 公式 | 结果 |
|------|------|
| `= List.Union({{1,2},{1,4,3}})` | `{1,2,4,3}` |
| `= List.Union({{1,2,1},{1,4,3}})` | `{1,2,1,4,3}` |
| `= List.Union({{1,2},{1,3},{1,3}})` | `{1,2,3}` |
| `= List.Union({{1,2,3,2},{1,3,2,4}})` | `{1,2,3,2,4}` |
| `= List.Union({{1,2},{1,3},{1,4,1}})` | `{1,2,1,3,4}` |

## `List.Intersect`

`List.Intersect`的语法如下，用于提取两个或多个列表中的公共值：

```
List.Intersect(
    lists as list,
    optional equationCriteria as any
) as list
```

输出包含在所有输入列表中重复出现的值。该函数接收两个参数。

1. 第一个参数表示一个引用列表，包含一组要计算交集的列表。
2. 第二个参数用于指定比较列表中元素的自定义条件。

根据函数说明，该函数在不同情况下的结果如表 6-20 所示。

**表 6-20.** `List.Intersect`示例

| 公式 | 结果 |
|------|------|



`= List.Intersect({{1..4},{2..5}})`

`{2,3,4}`

`= List.Intersect({{1..4},{2..5},{3..6}})`

`{3,4}`

`= List.Intersect({{1,2},{1,3},{1,4,1}})`

`{1}`

## `List.Difference`

`List.Difference` 用于计算两个列表之间的差异，其结果始终是一个包含第一个列表中存在但第二个列表中不存在的值的列表。

`List.Difference(`

`list1` as list,

`list2` as list,

optional `equationCriteria` as any

) as list

## 第 6 章 列表函数

该函数接收三个输入，如下所示：
1.  第一个列表输入到第一个参数中。
2.  第二个列表输入到第二个参数中。
3.  这个可选输入允许用户指定用于比较值的自定义条件。

基于此说明，该函数在不同输入情况下的结果如表 6-21 所示。

***表 6-21.** `List.Difference` 示例*

| **公式** | **结果** |
| --- | --- |
| `= List.Difference({1..10},{3..12})` | `{1,2}` |
| `= List.Difference({3..12},{1..10})` | `{11,12}` |
| `= List.Difference({3..12},{1..30})` | `{ }` |

### 对列表数据进行数学计算

Power Query 中有多个函数以列表作为输入，并对列表中的值执行数学运算后，输出一个值（数字）。其中最著名的函数之一是 `List.Count`，可用于获取列表的长度。因此，以下公式的结果等于列表中数字的个数，即 5：

`=List.Count({1..5})`

还有其他函数，可分为以下几组。

#### 第一组函数

第一组函数列于表 6-22 中，它们都接收一个列表作为输入。

***表 6-22.** 数学函数在列表上的应用*

| **函数** | **结果** |
| --- | --- |
| `List.Count` | 引用列表中值的数量 |
| `List.NonNullCount` | 引用列表中非空值的数量 |
| `List.StandardDeviation` | 引用列表中值的标准差 |

#### 第二组

第二组包括表 6-23 中列出的函数。

***表 6-23.** 数学函数在列表上的应用*

| **函数** | **结果** |
| --- | --- |
| `List.Sum` | 计算列表中所有数字的总和 |
| `List.Product` | 计算引用列表中所有数字的乘积 |
| `List.Average` | 计算列表中所有数字的平均值 |

这些函数接收两个输入：
1.  引用列表输入到第一个参数中。
2.  计算结果精度（可选），可以是 `Precision.Double` 或 `Precision.Decimal`，输入到第二个参数中。

#### 下一组

下一组包括用于计算列表中数字众数的 `List.Mode` 和中位数的 `List.Median`。它们的定义如下：

`List.Median(`

`list` as list,

optional `comparisonCriteria` as any

) as any

`List.Mode(`

`list` as list,

optional `equationCriteria` as any

) as any

这些函数接收两个输入，如下所示：
1.  引用列表输入到第一个参数中。
2.  此输入允许你指定用于比较值的自定义条件。

`List.Percentile` 是本组中的另一个函数，用于提取第 n 个百分位数，具体如下：

`List.Percentile(`

`list` as list,

`percentiles` as any,

optional `options` as nullable record

) as any

此函数按顺序接收三个输入：引用列表、所需的百分位数（以小数形式输入，如 0.25）以及精度。

基于上述说明，表 6-24 提供了该函数结果的多个示例。

***表 6-24.** 列表数学函数示例*

| **公式** | **结果** |
| --- | --- |
| `List.Count({1..10,4})` |  |
| `List.NonNullCount({1..10,4})` |  |
| `List.StandardDeviation({1..10,4})` | 2.788867 |
| `List.Sum({1..10,4})` |  |
| `List.Product({1..10,4})` |  |
| `List.Average({1..10,4})` |  |
| `List.Mode({1..10,4})` |  |
| `List.Median({1..10,4})` | 4.5 |
| `List.Percentile({1..10,4},0.25)` | 3.25 |

### 列表信息函数

为了检查两个列表是否相等，可以在两个列表之间使用 `=` 运算符，如下列公式所示：



`={1,2,3}={1,3,2}`

在检查两个列表是否相等时，只有当两个列表中的值（在值和格式方面）相同且顺序也一致时，结果才会是`True`。由于在上述公式中，第二个列表中的数字 3 和 2 相对于第一个列表发生了调换，因此上述公式的结果将为`False`，而以下公式的结果将为`True`：

`={1,2,3}={1,2,3}`

除了检查两个列表的相等性外，在 Power Query 中还可以使用多个函数来检查列表中的值是否存在，这些函数的结果将根据条件返回`True`或`False`。

## 第 6 章 列表函数

以下公式将讨论如何检查指定列表中是否存在某个值。

### `List.IsEmpty`

要检查一个列表是否为空或至少包含一个值，可以按如下方式使用`List.IsEmpty`：

`List.IsEmpty(**list** as list) as logical`

此函数接收一个列表作为输入。如果输入列表内至少注册了一个值，则此公式的结果为`False`；如果列表不包含任何值，则此函数的结果为`True`。

换句话说，在以下情况中，此函数的结果为`True`，而在其他情况下，此函数的结果为`False`：

`=List.Empty({ })`

### `List.IsDistinct`

要检查列表内是否存在重复值，可以按如下方式使用`List.IsDistinct`函数：

```
List.IsDistinct(
    **list** as list,
    optional **equationCriteria** as any
) as logical
```

根据函数语法，此函数接收以下两个输入：

1.  第一个输入中输入引用列表。
2.  此函数的第二个输入（可选）允许您指定用于比较列表中值的自定义条件。如果未输入，则在文本比较中会区分大小写。

如果引用列表基于第二个输入中定义的逻辑包含重复值，则此函数的结果为`False`；否则，此函数的结果为`True`。

根据函数说明，以下公式的结果为`True`：

`= List.IsDistinct({1..10})`

而以下公式的结果为`False`：

`= List.IsDistinct({1..10,1})`

### `List.Contains`

使用`List.Contains`可以检查引用列表中是否存在某个特定值（仅一个值）。此函数的定义如下：

```
List.Contains(
    **list** as list,
    **value** as any,
    optional **equationCriteria** as any
) as logical
```

根据上述语法，此函数接收三个输入：

1.  第一个输入中输入引用列表。
2.  在此函数的第二个输入中，输入您想要检查是否存在于引用列表中的值。
3.  此函数的第三个输入为可选，允许您指定用于比较值的自定义条件。

如果引用列表包含在第二个输入中注册的值，则此函数的输出为`True`，否则为`False`。因此，以下公式的结果为`True`：

`= List.Contains({1..10},3)`

而以下公式的结果为`False`：

`= List.Contains({1..10},11)`

### `List.ContainsAll`

要检查列表中是否存在两个或更多值，可以按如下方式使用`List.ContainsAll`：

```
List.ContainsAll(
    **list** as list,
    **values** as list,
    optional **equationCriteria** as any
) as logical
```

此函数接收以下三个输入：

1.  第一个输入中输入引用列表。
2.  第二个输入中输入要检查是否存在于引用列表中的值列表。
3.  第三个输入允许您指定用于比较值的自定义条件。

如果引用列表包含第二个输入中的所有值，则此函数的结果为`True`；在其他情况下，此函数的结果为`False`。

有关此函数的一些简单示例，请参见表 6-25。

**表 6-25.** `List.ContainsAll`的示例

| 公式 | 结果 |
|---------|--------|
| | |



