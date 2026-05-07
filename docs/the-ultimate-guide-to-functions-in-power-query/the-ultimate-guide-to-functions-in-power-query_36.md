# 第 6 章 列表函数

## `List.ReplaceRange`

若要基于位置（例如，从第 5 个到第 12 个值）用新值替换列表的一部分，可按如下方式使用 `List.ReplaceRange`：

`List.ReplaceRange(`
`list` as list,
`index` as number,
`count` as number,
`replaceWith` as list
`) as list`

此函数接受四个输入。

1. 第一个输入表示参考列表。
2. 在此函数的第二个输入中，指定了从列表中删除值的起始位置。（编号从 0 开始）。
3. 应在第三个输入中确定需要删除值的数量。
4. 在第四个输入中，输入应替换已删除值的值列表。

基于此函数的输入，表 6-31 中提供了该函数的多个示例。

**表 6-31.** `List.ReplaceRange` 示例

| 公式 | 结果 |
| --- | --- |
| `= List.ReplaceRange({1..10},3,6,{5})` | `{1,2,3,5,10}` |
| `= List.ReplaceRange({1..10},0,6,{5})` | `{5,7,8,9,10}` |
| `= List.ReplaceRange({1..10},1,8,{5,6})` | `{1,5,6,10}` |

---

