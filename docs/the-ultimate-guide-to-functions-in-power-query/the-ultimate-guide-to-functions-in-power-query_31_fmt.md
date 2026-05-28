# 第 6 章 列表函数

这两个函数都接受以下参数：

1.  第一个参数中输入引用列表。
2.  这些函数的第二个参数是可选的，如果引用列表为空，则该参数会显示在输出中。

根据函数说明，以下公式的结果等于 1：

`= List.First({1..10})`

而以下公式的结果等于 -1：

`= List.First({ },-1)`

## List.FirstN 和 List.LastN

这两个函数彼此相似，区别在于 `List.FirstN` 用于从列表开头提取特定数量的值，而 `List.LastN` 用于从列表末尾提取特定数量的值。两者的定义如下：

```
List.FirstN(
  list as list,
  countOrCondition as any
) as any
```

```
List.LastN(
  list as list,
  optional countOrCondition as any
) as any
```

这两个函数接受两个输入，其中第一个输入是列表格式，第二个输入是数值格式。

1.  第一个输入中输入引用列表。
2.  在此输入中，输入要从列表开头（或末尾）提取值的数量。在此输入中，也可以定义筛选条件来代替具体数字。在这种情况下，将从列表开头（或末尾）显示满足相关条件的最大一组值作为输出。换句话说，将从列表开头（或末尾）提取值，直到遇到第一个不符合条件的实例。

根据函数说明，这些函数在不同示例中的输出如表 6-10 所示。

**表 6-10.** `List.FirstN` 的示例

| 公式 | 结果 |
| :--- | :--- |
| `= List.FirstN({1..10},3)` | `{1,2,3}` |
| `= List.FirstN({1..10},5)` | `{1,2,3,4,5}` |
| `= List.FirstN({1..10},each _<5)` | `{1,2,3,4}` |
| `= List.FirstN({5, 4,1, 2, 6, 1}, each _>3)` | `{5.4}` |
| `= List.LastN({1..10},3)` | `{8,9,10}` |
| `= List.LastN({1..10},5)` | `{6,7,8,9,10}` |
| `= List.LastN({1..10},each _<5)` | `{1,2,3,4}` |
| `= List.LastN({5, 4,1, 2, 6, 1}, each _<3)` | `{1}` |

在示例 `List.LastN({5, 4,1, 2, 6, 1}, each _<3)` 中，说明列表末尾小于 3 的值应显示在输出中。如您所见，引用列表中也包含数字 2（它小于 3 并满足条件），但输出仅包含数字 1。原因是，只有在满足条件时，列表末尾的值才会显示在输出中，一旦遇到不满足条件的值，搜索操作就会停止，并且不再检查后续的值。因此，在此示例中，由于数字 6（倒数第二个数据）大于 3，因此不会检查数字 6 之前的数据。

## List.Range

此函数用于从列表的不同部分提取值，定义如下：

```
List.Range(
  list as list,
  offset as number,
  optional count as nullable number
) as list
```

此函数接受以下三个输入：

1.  在此函数的第一个输入中，输入引用列表。
2.  在此输入中，通过输入数字来确定提取值的起始点。
3.  此函数的第三个输入指示要从列表中提取的值的数量。

此函数的不同示例如表 6-11 所示。

**表 6-11.** `List.Range` 的示例

| 公式 | 结果 |
| :--- | :--- |
| `= List.Range({1..10},5,3)` | `{6,7,8}` |
| `= List.Range({1..10},1,3)` | `{2,3,4}` |
| `= List.Range({1..10},3,1)` | `{4}` |

## List.Max 和 List.Min

`List.Max` 和 `List.Min` 用于提取列表中的最大值和最小值，定义如下：

```
List.Max(
  list as list,
  optional default as any,
  optional comparisonCriteria as any,
  optional includeNulls as nullable logical
) as any
```

```
List.Min(
  list as list,
  optional default as any,
  optional comparisonCriteria as any,
  optional includeNulls as nullable logical
) as any
```