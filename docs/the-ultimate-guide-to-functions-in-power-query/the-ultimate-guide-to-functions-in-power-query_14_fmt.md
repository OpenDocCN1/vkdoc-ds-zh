# 将值转换为数字格式

与将不同格式的值转换为数字格式相关的函数见表 2-6。所有这些函数都接收特定类型的输入，并输出一个数字格式的值。

`表 2-6.` 将值转换为数字格式

| 函数 | 描述 |
| --- | --- |
| `Number.FromText(text as text) as number` | 使用此函数，可以将文本格式存储的数值转换为数字。例如，如果数字 `123` 以文本形式存储，可以使用此函数将其转换为数字。 |
| `Number.From(value as any) as number` | 此函数用于将其他格式存储的数值转换为数字格式。 |
| `Int32.From(value as any) as number` | 此函数用于将值转换为 32 位整数表示形式。 |
| `Int64.From(value as any) as number` | 此函数用于将值转换为 64 位整数表示形式。 |
| `Single.From(value as any) as number` | 此函数根据给定的值返回一个单精度数字。 |
| `Double.From(value as any) as number` | 此函数根据给定的值返回一个双精度数字。 |
| `Decimal.From(value as any) as number` | 此函数根据给定的值返回一个十进制数字。 |
| `Currency.From(value as any) as number` | 此函数根据给定的值返回一个货币值。 |