# 列表函数

这些函数接收以下四个输入，其中只有第一个输入是必需的，其余为可选：

1.  在第一个输入中输入参考列表。

2.  第二个输入是可选的，当结果等于 `null`（参考列表为空）时，指定函数的返回值。

3.  该函数的第三个输入也是可选的，用于指定比较值的标准。

4.  该函数的第四个输入可以是 `True`（考虑 `Null` 值）或 `False`（忽略 `Null` 值）。

根据函数说明，这些函数针对不同示例的结果如表 6-12 所示。

**表 6-12.** `List.Max` 示例

| 公式 | 结果 |
|---|---|
| `=List.Max({1..10})` | 10 |
| `=List.Max({},-1)` | -1 |
| `=List.Max({"a".."n"},-1)` | “n” |
| `=List.Max({-20..10},null,each Number.Abs(_))` | 20 |

## `List.MaxN` 和 `List.MinN`

`List.MaxN` 和 `List.MinN` 用于提取列表中最大的 n 个或最小的 n 个值，其语法如下：

```
List.MinN(
    list as list,
    countOrCondition as any,
    optional comparisonCriteria as any,
    optional includeNulls as nullable logical
) as list
```

```
List.MaxN(
    list as list,
    countOrCondition as any,
    optional comparisonCriteria as any,
    optional includeNulls as nullable logical
) as list
```

根据上述语法，这些函数接收以下四个输入：

1.  在第一个输入中输入参考列表。

2.  在这些函数的第二个输入中，确定数量 n（要提取的最大或最小值的数量）或提取条件。

3.  该函数的第三个输入是可选的，表达数据比较的逻辑。

4.  第四个输入是可选的，可以是 `True`（考虑 `Null` 值）或 `False`（忽略 `Null` 值）之一。

根据函数说明，这些函数在不同状态下的结果如表 6-13 所示。

**表 6-13.** `List.MaxN` 示例

| 公式 | 结果 |
|---|---|
| `=List.MaxN({-20..10},3)` | `{10,9,8}` |
| `=List.MaxN({"a",1,-1,"N"},3)` | `{"a","n",1}` |

## `List.Select`

`List.Select` 是 Power Query 中最重要且最强大的函数之一，用于根据自定义条件从列表中提取值，其语法如下：

```
List.Select(
    list as list,
    selection as function
) as list
```

该函数接收两个输入，其输出始终是一个列表。

1.  第一个输入是参考列表。

2.  第二个输入以一个函数的形式定义从参考列表中选择值的条件。该输入可以定义多种场景，下面将进行讨论。

让我们从一个示例开始。在公式 `List.Select({1..10}, each _>5)` 中，参考列表是一个从 1 到 10 的数字列表，条件定义为 `each _>5`，这意味着应提取参考列表中大于 5 的数字并在输出中显示。

因此，此公式的结果等于列表 `{6,7,8,9,10}`。

该函数的不同示例如表 6-14 所示。

**表 6-14.** `List.Select` 示例

| 函数 | 结果 |
|---|---|
| `=List.Select({1..10},each _>5)` | `{6,7,8,9,10}` |
| `=List.Select({1..10},each _>5 and _<8)` | `{6,7}` |
| `=List.Select({1..10},each Number.IsOdd(_))` | `{1,3,5,7,9}` |
| `=List.Select({1..10},each Number.Mod(_,3)=0)` | `{3,6,9}` |
| `=List.Select({1,"a",3,"x","n"},each Value.Is(_,type number))` | `{1,3}` |

在前面的示例中，条件 `each Number.IsOdd(_)` 表示只应从参考列表中提取奇数，条件 `each Number.Mod(_,3)=0` 表示只应从列表中提取 3 的倍数，最后，条件 `each Value.Is(_,type number)` 表示只应从参考列表中提取数字类型的值。

## `List.FindText`

要从列表中提取包含特定字符或文本的文本值，可以使用以下语法的 `List.FindText`：

```
List.FindText(
```