# 将值转换为日期和时间格式

有一些函数用于将不同格式的值转换为日期，见表 2-8。

**表 2-8.** 将值转换为日期格式

| 函数 | 描述 |
| --- | --- |
| `Date.FromText(text as text) as date` | 此函数用于将文本值转换为日期。 |
| `Time.FromText(text as text) as time` | 此函数用于将文本值转换为时间。 |
| `DateTime.FromText(text as text) as datetime` | 此函数用于将文本值转换为日期和时间。 |
| `DateTimeZone.FromText(text as text) as datetimezone` | 此函数用于将文本值转换为日期、时间和时区。 |
| `Date.From(value as any) as date` | 此函数用于将任意格式的值转换为日期。 |
| `Time.From(value as any) as time` | 此函数用于将任意格式的值转换为时间。 |
| `DateTime.From(value as any) as datetime` | 此函数用于将任意格式的值转换为日期和时间。 |
| `DateTimeZone.From(value as any) as datetimezone` | 此函数用于将任意格式的值转换为日期、时间和时区。 |

