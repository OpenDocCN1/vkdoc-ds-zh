# 第 6 章 列表函数

## `List.Reverse`

`List.Reverse` 在 Power Query 中是一个非常简单的函数，用于反转列表中值的顺序。该函数接受一个 `list` 格式的参数，并在输出中显示反转后的列表，其形式如下：`List.Reverse(list as list) as list`

为阐明此函数的用法，表 6-8 中提供了一些相关示例。

**表 6-8** `List.Reverse` 示例

| 公式 | 结果 |
|---------|--------|
| `= List.Reverse({1..4})` | `{4,3,2,1}` |
| `= List.Reverse({"a",1,"b",10})` | `{10,"b",1,"a"}` |
| `= List.Reverse({3,8,1})` | `{1,8,3}` |

## `List.Sort`

通过使用 `List.Sort`，可以对列表中的值进行升序和降序、按字母顺序、按数字顺序或任何其他逻辑进行排序。此函数的定义如下：

```
List.Sort(
   list as list,
   optional comparisonCriteria as any
) as list
```

因此，该函数接受以下两个参数：

1.  第一个参数中定义了参考列表。
2.  在此函数的第二个参数中，指定了排序逻辑。此输入是可选的；如果不输入，列表中的值将按升序排序。但是，可以在该输入中输入 `Order.Descending` 以降序对值进行排序。在高级情况下，可以通过函数定义自定义排序逻辑。

该函数的一些基本示例在表 6-9 中给出。

**表 6-9** `List.Sort` 示例

| 公式 | 结果 |
|---------|--------|
| `=List.Sort({1,5,-2,-3})` | `{-3,-2,1,5}` |
| `= List.Sort({1,5,2,3},Order.Descending)` | `{5,3,2,1}` |
| `= List.Sort({"a","x","b","c"})` | `{"a","b","c","x"}` |
| `= List.Sort({"a","x","b","c"},Order.Descending)` | `{"x","c","b","a"}` |

如前几个示例所示，以下公式的结果等于 `{-3, -2, 1, 5}`：

```
= List.Sort({1, 5, -2, -3})
```

这是因为 `-3` 小于 `1`，所以被列在开头。但是，如果不考虑数字的符号进行比较，可以使用以下公式：

```
= List.Sort({1, 5, -2, -3}, (x) => Number.Abs(x))
```

在此公式中，您通过考虑值的绝对值（而非原始值）对列表中的值进行排序。因此，上述公式的结果等于 `{1, -2, -3, 5}`。

也可以使用以下公式对列表进行排序，而不考虑值的符号，并从大到小排列：

```
= List.Sort({1, 5, -2, -3}, {(x) => Number.Abs(x),Order.Descending})
= List.Sort({1, 5, -2, -3}, {(x) => Number.Abs(x),1})
= List.Sort({1, 5, -2, -3}, {each Number.Abs(_),1})
```

在另一个示例中，通过使用以下公式，偶数在输出中优先列出，其次是奇数：

```
= List.Sort({1, 2, 5, 4, 3}, (x) => Number.IsOdd(x))
```

上述公式的输出等于 `{4, 2, 3, 1, 5}`，其中所有偶数排在前面，后面跟着奇数。要将奇数和偶数在其组内从小到大排序，可以使用以下公式：

```
= List.Sort({1, 2, 5, 4, 3}, {{each Number.IsOdd(_)},{each _}})
```

上述公式中定义了两步排序。首先，根据 `{each Number.IsOdd(_)}` 对值进行排序。然后，如果此公式的结果对于多个值相同，则根据 `{each _}` 条件（从小到大）对其进行排序。因此，结果为 `{2,4,1,3,5}`。

在另一个示例中，以下公式不使用第一个字符（左侧字符）对列表中的文本进行排序，而是使用最后一个字符（右侧字符）作为排序依据：

```
= List.Sort({"AXY", "XYA", "WND", "ACC"}, each Text.End(_, 1))
```

上述公式的结果等于 `{"XYA", "ACC", "WND", "AXY"}`。

### 从列表中提取

在 Power Query 中，有许多用于从列表中提取值的函数，本节将对这些函数进行探讨。

### `List.First` 和 `List.Last`

`List.First` 用于提取列表中的第一个值，`List.Last` 用于提取列表中的最后一个值，如下所示：

```
List.First(
   list as list,
   optional defaultValue as any
) as any

List.Last(
   list as list,
   optional defaultValue as any
) as any
```