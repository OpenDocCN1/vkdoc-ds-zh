# 列表函数

## `List.ContainsAll`

```
= List.ContainsAll({1..10},{1,3,7})
true

= List.ContainsAll({1..10},{1,3,11})
False
```

## `List.ContainsAny`

`List.ContainsAny` 与 `List.ContainsAll` 类似，区别在于，如果参照列表包含第二个输入中的至少一个值，则 `List.ContainsAny` 的结果为 `true`。

`List.ContainsAny` 的定义如下：

```
List.ContainsAny(
   list as list,
   values as list,
   optional equationCriteria as any
) as logical
```

该函数接收三个输入：

1.  第一个输入是参照列表。
2.  第二个输入是要检查是否存在于参照列表中的值列表。
3.  第三个输入定义了比较值的逻辑，允许你指定自定义的比较条件。

该函数的一些简单示例见表 6-26。

**表 6-26.** `List.ContainsAny` 的示例

| 公式 | 结果 |
|---------|--------|
| `= List.ContainsAny({1..10},{1,3,7})` | `true` |
| `= List.ContainsAny({1..10},{1,3,11})` | `true` |
| `= List.ContainsAny({1..10},{12,13,11})` | `False` |

## `List.AllTrue`

此函数接收一个列表作为输入，如果参照列表中的所有值都等于 `True`，则此函数的结果为 `True`；否则，此函数的结果为 `False`。其定义如下：

```
List.AllTrue(list as list) as logical
```

需要注意的是，输入到此函数中的列表必须包含 `True` 或 `False` 值，如果包含数字或文本，此函数将返回错误。

## `List.AnyTrue`

此函数定义如下：

```
List.AnyTrue(list as list) as logical
```

此函数接收一个列表作为输入，如果该列表中至少有一个值等于 `True`，则此函数的结果为 `True`；否则，此函数的结果为 `False`。

### 列表中的位置

本节解释与提取列表中值位置相关的函数。

### `List.Positions`

`List.Positions` 函数在 Power Query 中的定义非常简单，如下所示：

```
List.Positions(list as list) as list
```

此函数接收一个列表作为输入，其输出是一个说明值位置的列表。此函数的输出仅取决于参照列表中的值数量，而不取决于它们的值或类型。换句话说，对于包含两个值的列表，此函数的结果始终等于 `{0,1}`；对于包含三个值的列表，其结果始终等于 `{0,1,2}`。

因此，以下公式的结果等于 `{0,1,2}`：

```
= List.Positions({1,10,-1})
```

### `List.PositionOf`

要提取某个值的位置，可以按如下方式使用 `List.PositionOf`：

```
List.PositionOf(
   list as list,
   value as any,
   optional occurrence as nullable number,
   optional equationCriteria as any
) as any
```

此函数接收四个输入：

1.  第一个输入是参照列表。
2.  第二个输入是你正在搜索位置的值。
3.  第三个输入是可选的，当搜索值在参照列表中重复出现多次时使用。此输入指定哪个重复项出现在输出中。输入 `Occurrence.First` 表示输出中显示第一个重复项的位置。使用 `Occurrence.Last` 表示显示最后一个重复项的位置。最后，使用 `Occurrence.All` 时，所有重复项的位置会以列表形式显示在输出中。
4.  此函数的第四个输入也是可选的，允许你指定自定义的比较条件。

此函数的各种示例见表 6-27。

**表 6-27.** `List.PositionOf` 的示例

| 公式 | 结果 |
|---------|--------|
| `= List.PositionOf({1..10},5)` | |
| `= List.PositionOf({4,5,3,5,2},5)` | |
| `= List.PositionOf({4,5,3,5,2},5,Occurrence.First )` | |
| `= List.PositionOf({4,5,3,5,2},5,Occurrence.Last )` | |
| `= List.PositionOf({4,5,3,5,2},5,Occurrence.All )` | `{1,3}` |
| `= List.PositionOf({"a","b","A","A"},"A")` | `2` |

### `List.PositionOfAny`

`List.PositionOfAny` 与 `List.Position` 相似，但不同之处在于，该函数可以在参考列表中同时搜索两个或更多值，并提取它们的位置。该函数的定义如下：

```
List.PositionOfAny(
list as list,
values as list,
optional occurrence as nullable number,
optional equationCriteria as any
) as any
```

`List.PositionOfAny` 接收四个输入。

1.  第一个输入中，输入参考列表。
2.  第二个输入中，输入要搜索其位置的值列表。
3.  该函数的第三个输入是可选的，当搜索的值在参考列表中多次重复出现时使用。在此输入中，输入 `Occurrence.First` 表示函数返回第一次出现的位置，输入 `Occurrence.Last` 表示返回最后一次出现的位置。若要确定所有出现的位置，可以在该函数中使用 `Occurrence.All`。
4.  该函数的第四个输入是可选的，允许你为值比较指定自定义条件。

表 6-28 中提供了该函数的各种示例。

**表 6-28.** `List.PositionOfAny` 示例

| 公式 | 结果 |
| --- | --- |
| `= List.PositionOfAny({1..10},5)` | `4` |
| `= List.PositionOfAny({4,5,3,5,2},5)` | `1` |
| `= List.PositionOfAny({4,5,3,5,2},{5,3},Occurrence.First)` | `1` |
| `= List.PositionOfAny({4,5,3,5,2},{3},Occurrence.Last)` | `3` |
| `= List.PositionOfAny({4,5,3,5,2},{5,3},Occurrence.All)` | `{1,2,3}` |
| `= List.PositionOfAny({"a","b","A","A"},{"a","A"})` | `0` |

### 替换和转换值

Power Query 中有些函数允许你用新值替换列表中的值。

### `List.ReplaceValue`

使用 `List.ReplaceValue` 意味着可以将列表中的值替换为新值，如下所示：

```
List.ReplaceValue(
list as list,
oldValue as any,
newValue as any,
replacer as function
) as list
```

该函数接收四个输入。

1.  第一个输入中输入参考列表。
2.  第二个输入代表你想要用新值替换的旧值。
3.  第三个输入中输入新值。
4.  该函数的第四个输入代表替换类型，其定义为一个函数。在此输入中，可以使用两种模式：`Replacer.ReplaceValue` 和 `Replacer.ReplaceText`，分别用于替换数字和文本。

根据函数说明，该函数在不同模式下的结果如表 6-29 所示。

**表 6-29.** `List.ReplaceValue` 示例

| 公式 | 结果 |
| --- | --- |
| `= List.ReplaceValue({1..4},4,3,Replacer.ReplaceValue)` | `{1,2,3,3}` |
| `= List.ReplaceValue({1..4},4,3,Replacer.ReplaceText)` | `{1,2,3,4}` |

| `= List.ReplaceValue({"a","b","B"},"b","B",Replacer.ReplaceText)` | `{"a","B","B"}` |

### `List.ReplaceMatchingItems`

`List.ReplaceMatchingItems` 类似于 `List.ReplaceItems`，但 `List.ReplaceItems` 只能将列表中的一个值替换为新值，而 `List.ReplaceMatchingItems` 可用于将列表中的多个值替换为新值。该函数的定义如下：

```
List.ReplaceMatchingItems(
list as list,
replacements as list,
optional equationCriteria as any
) as list
```

**第 6 章 列表函数**

该函数接收三个输入。

1.  第一个输入定义了参考列表。

2.  第二个输入定义为一个列表的列表，其中每个内部列表包含两个值。第一个值表示旧值，第二个值表示新值。因此，要将列表中的 `1` 替换为 `-1`，并将 `4` 替换为 `-4`，此输入应定义为 `{{1,-1},{4,-4}}`。

3.  该函数的第三个输入是可选的，允许你为值比较指定自定义条件。

根据函数说明，该函数在不同示例中的结果如表 6-30 所示。

**表 6-30.** `List.ReplaceMatchingItems` 示例

| 公式 | 结果 |
| --- | --- |
| `= List.ReplaceMatchingItems({1..5},{{1,5}})` | `{5,2,3,4,5}` |
| `= List.ReplaceMatchingItems({1..5},{{1,5},{2,6}})` | `{5,6,3,4,5}` |