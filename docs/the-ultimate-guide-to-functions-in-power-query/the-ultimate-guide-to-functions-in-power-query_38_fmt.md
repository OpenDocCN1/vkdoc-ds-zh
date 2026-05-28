# 第 6 章 列表函数

**表 6-32.** `List.Transform` 示例

| 公式 | 结果 |
| --- | --- |
| `= List.Transform({1..5},each _+ 2)` | `{3,4,5,6,7}` |
| `= List.Transform({1..5},each Number.Factorial(_))` | `{1,2,6,24,120}` |
| `= List.Transform({1..5},each Number.Round(Number.Exp(_)))` | `{3,7,20,55,148}` |
| `= List.Transform({"abc","DEF","mNg"}, each Text.Proper(_))` | `{"abc","Def","Mng"}` |

---

## `List.TransformMany`

`List.TransformMany` 可用于通过特定逻辑更改或转换列表中的值，具体如下：

```
List.TransformMany(
    list as list,
    collectionTransform as function,
    resultTransform as function
) as list
```

基于此语法，此函数接受三个输入：

1.  第一个输入定义了参考列表。
2.  在第二个输入中，定义了转换函数。
3.  在第三个输入中，定义了重新转换函数的方法。换句话说，将第一个输入视为 `x`，第二个输入中定义的函数形式为 `(x) =>`，其结果被视为 `y`。在此函数的第三个输入中，定义了一个新函数，形式为 `(x, y) =>`。

---