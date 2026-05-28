# 将值转换为文本格式

用于将不同格式的值转换为文本格式的函数列表见表 2-7。所有这些函数都接收特定类型的输入，并输出文本格式的结果。

**表 2-7.** 将值转换为文本格式

| 函数 | 描述 |
| --- | --- |
| `Text.From(value as any) as text` | 此函数用于将任何格式的值转换为文本。 |
| `Number.ToText(number as number) as text` | 此函数用于将数字格式的值转换为文本格式的值。 |
| `Logical.ToText(logical as logical) as text` | 使用此函数，可以将 `True`/`False` 值转换为文本。 |
| `Date.ToText(date as date) as text` | 使用此函数，可以将日期格式的值转换为文本格式的值。 |
| `Time.ToText(time as time) as text` | 使用此函数，可以将时间格式的值转换为文本格式的值。 |
| `DateTime.ToText(datetime as datetime) as text` | 使用此函数，可以将日期和时间格式的值转换为文本格式的值。 |
| `DateTimeZone.ToText(datetimezone as datetimezone) as text` | 使用此函数，可以将日期、时间和时区格式的值转换为文本格式的值。 |