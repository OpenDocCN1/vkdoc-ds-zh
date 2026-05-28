# 第 6 章 列表函数

`List.FindText` 函数的语法如下：

```
List.FindText(list as list, text as text) as list
```

此函数包含两个输入：

1.  第一个输入中应输入参考列表。
2.  此输入中应输入您要搜索的文本。例如，在此输入中输入 `"a"`，表示输出中将显示包含字母 `"a"` 的参考列表中的值；输入 `"ab"`，表示输出中将显示包含字母 `"ab"` 的参考列表中的值。

基于函数说明，表 6-15 提供了此函数应用的一些示例。

**表 6-15.** `List.FindText` 的示例

| **公式** | **结果** |
| --- | --- |
| `= List.FindText({"a".."z"},"m")` | `{"m"}` |
| `= List.FindText({"a".."z"},"M")` | `{ }` |
| `= List.FindText({"abc","bcf","deg","z"},"b")` | `{"abc","bcf"}` |

## 移除列表中的值

在 Power Query 中，有许多用于从列表中移除值的函数，本节将对这些函数进行介绍。

### `List.RemoveNulls`

此函数用于从列表中移除空值，其语法如下：

```
List.RemoveNulls(list as list) as list
```

此函数仅接受一个列表形式的输入，在移除该列表中的空值后，结果将显示在输出中。

根据以上说明，以下公式的结果等于 `{1,2,"",3,"null"}`：

```
= List.RemoveNulls({1,null,2,null,"",3,"null"})
```

如您所见，此公式的输出也包含了值 `"null"`，因为单词 `null` 被输入在双引号内，视为文本值，此函数不会将其视为空值。

### `List.Distinct`

`List.Distinct` 用于移除列表中的重复值，其结果始终是一个包含唯一值的列表。

```
List.Distinct(
    list as list,
    optional equationCriteria as any
) as list
```

此函数接受两个参数，如下所示：

1.  第一个参数中应输入参考列表。
2.  此函数的第二个参数是可选的，允许您指定比较值的自定义条件。

基于以上说明，此函数在不同示例下的结果如表 6-16 所示。

**表 6-16.** `List.Distinct` 的示例

| **公式** | **结果** |
| --- | --- |
| `= List.Distinct({1,3,2,1,2,5,1})` | `{1,3,2,5}` |
| `= List.Distinct({1,1,1,1,1,1,1})` | `{1}` |
| `= List.Distinct({"a","A","b","a"})` | `{"a","a","b"}` |
| `= List.Distinct({"a","A","b","a"},Comparer.OrdinalIgnoreCase)` | `{"a","b"}` |
| `= List.Distinct({1..10},each Number.IsOdd(_))` | `{1,2}` |

### `List.RemoveFirstN` 和 `List.RemoveLastN`

`List.RemoveFirstN` 和 `List.RemoveLastN` 用于从列表的开头或结尾移除指定数量的值，其语法如下：

```
List.RemoveFirstN(
    list as list,
    optional countOrCondition as any
) as list
```

```
List.RemoveLastN(
    list as list,
    optional countOrCondition as any
) as list
```

这些函数接受两个参数，如下所示：

1.  第一个参数中应输入一个参考列表。
2.  第二个输入决定了应从参考列表的开头或结尾移除多少个值。

### `List.RemoveRange`

`List.RemoveRange` 用于移除列表中的值，其语法如下：

```
List.RemoveRange(
    list as list,
    index as number,
    optional count as nullable number
) as list
```

此函数接受三个输入，如下所示：

1.  第一个输入中应输入一个参考列表。
2.  在此输入中，确定移除值的起始位置。
3.  第三个输入决定了应从参考列表中的起始位置（由第二个输入确定）开始移除多少个值。

表 6-17 中提供了使用 `List.RemoveRange` 的多个示例。

**表 6-17.** `List.RemoveRange` 的示例

| **公式** | **结果** |
| --- | --- |
| `= List.RemoveRange({1..10},3,5)` | `{1,2,3,9}` |
| `= List.RemoveRange({1..10},4,4)` | `{1,2,3,4,9,10}` |

### `List.RemoveItems`

使用具有以下语法的 `List.RemoveItems`，可以从列表中移除特定的值：

```
List.RemoveItems(
    list1 as list,
    list2 as list
) as list
```

该函数以列表形式接收以下两个输入：

1.  第一个输入是参考列表。
2.  该函数的第二个输入是一个值列表，表示要从参考列表中移除的值。

根据上述说明，在下面的公式中，参考列表包含从 1 到 10 的所有数字。第二个输入指定应从参考列表中移除数字 3 到 8。因此，该公式的结果等于 `{1,2,9,10}`：

```
=List.RemoveItems({1..10},{3..8})
```

此外，`List.RemoveMatchingItems` 函数用于从参考列表中移除值；区别在于该函数接收三个输入，匹配条件可以在第三个输入中指定。

```
List.RemoveMatchingItems(
    list1 as list,
    list2 as list,
    optional equationCriteria as any
) as list
```

## `List.Skip`

`List.Skip` 类似于 `List.RemoveFirstN`，用于从列表开头移除一定数量的值或满足特定条件的值，语法如下：

```
List.Skip(
    list as list,
    optional countOrCondition as any
) as list
```

该函数接收以下两个输入：

1.  第一个输入中传入参考列表。
2.  第二个输入中传入要从列表开头移除的值的数量或移除条件。

根据函数说明，以下公式的结果等于 `{5,6,7}`：

```
=List.Skip({1..7},4)
```

同样，以下公式的结果也等于 `{5,6,7}`：

```
= List.Skip({1..7},each _<5)
```

## `List.Alternate`

`List.Alternate` 使用以下语法，用于按照特定模式（例如，每隔一个值移除一次）从列表中移除或保留一系列值：

```
List.Alternate(
    list as list,
    count as number,
    optional repeatInterval as nullable number,
    optional offset as nullable number
) as list
```

该函数接收以下四个输入：

1.  第一个输入中传入参考列表。
2.  第二个输入确定每次迭代中跳过的值的数量。
3.  第三个输入确定在跳过的值之间选择多少个值。
4.  此输入为可选项，确定开始跳过值的起始点。在此输入中输入数字 2 表示应保留列表的前两个值（显示在输出中），跳过和保留值的模式从列表的第三个值开始。

根据上述说明，以下公式指定仅移除参考列表中的第一个值，其余值均报告在结果中。因此，该公式的结果等于 `{2..10}`。

```
= List.Alternate({1..10}, 1)
```

在以下公式中，指定应每隔一个值在列表中跳过。因此，公式的结果等于列表中的所有偶数，即 `{2,4,6,8,10}`。

```
= List.Alternate({1..10}, 1,1)
```

在以下公式中，指定每次迭代中应跳过一个值，并保留接下来的两个值，此过程持续到列表末尾。因此，公式的结果为 `{2,3,5,6,8,9}`。

```
= List.Alternate({1..10}, 1,2)
```

以下示例与上一个示例相反，每次迭代中跳过两个值，并保留下一个值。因此，以下公式的结果等于 `{3,6,9}`：

```
= List.Alternate({1..10}, 2,1)
```

以下公式与上述公式类似，区别在于模式是从列表中的第二个值之后开始执行。`{1,2,5,8}`

```
= List.Alternate({1..10}, 2,1,2)
```

### 列表合并

Power Query 中有多个函数用于将两个或多个列表合并在一起，本节将对此进行说明。除了这些现有函数外，还可以使用 `&` 运算符轻松合并列表。因此 `{1,2} &`