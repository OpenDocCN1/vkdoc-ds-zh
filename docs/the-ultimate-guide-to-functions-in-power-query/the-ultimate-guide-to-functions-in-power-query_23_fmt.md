# 第 5 章 日期和时间函数

因此，如果 `X` 是过去两年时间段内的一个日期，那么 `Date.IsInPreviousNYears(X,2)` 的结果就是 `true`。

假设今天的日期是 2023 年 4 月 14 日，那么本组中不同公式的结果如表 5-1 所示。（为节省空间，所有函数名称开头的 `Date.IsIn` 表达式已被省略；`True` 用 `T` 表示；`False` 用 `F` 表示；`x` 代表列标题中输入的日期。）

表 5-1. 参考日期 (x)

| 日期 | `CurrentMonth(x)` | `NextMonth(x)` | `PreviousMonth(x)` | `NextYear(x)` | `CurrentYear(x)` | `NextNQuarters(x,5)` | `PreviousQuarter(x)` | `NextNMonths(x,7)` | `CurrentWeek(x)` |
|------|-------------------|----------------|--------------------|---------------|-----------------|----------------------|----------------------|---------------------|------------------|
| 18/11/2022 | F | F | F | F | F | F | F | F | F |
| 4/11/2023 | F | F | F | F | F | F | F | F | F |
| 11/10/2022 | F | F | F | F | F | F | F | F | F |
| 24/05/2022 | F | F | F | F | F | F | F | F | F |
| 22/04/2022 | F | F | F | F | F | F | F | F | F |
| 24/07/2022 | F | F | F | T | F | T | F | F | F |
| 9/03/2024 | F | F | F | F | F | F | F | F | F |
| 10/09/2022 | F | F | F | F | F | F | F | F | F |
| 11/02/2022 | F | F | F | F | T | T | F | T | F |
| 4/09/2023 | F | F | F | F | T | F | T | F | F |
| 1/02/2023 | F | F | F | F | T | F | F | T | F |
| 15/06/2023 | F | F | F | F | T | F | T | F | F |
| 19/02/2023 | F | F | F | F | T | T | F | T | F |
| 18/08/2023 | T | F | F | F | T | F | F | F | F |
| 18/04/2023 | F | F | F | F | T | T | F | T | F |
| 12/10/2023 | F | F | F | F | F | F | F | F | F |
| 18/12/2022 | F | F | F | F | F | F | F | F | F |
| 5/03/2023 | F | F | T | F | T | F | T | F | F |

## 日期时间信息

与上一节中的函数类似，这组信息函数也包含两组。第一组函数（包括以下函数）接收一个输入，用于检查输入的时间是否在当前时间之前或之后的某个时间段（小时、分钟或秒）内。其中包括以下几种情况：

- `DateTime.IsInCurrentHour`
- `DateTime.IsInCurrentMinute`
- `DateTime.IsInCurrentSecond`
- `DateTime.IsInNextHour`
- `DateTime.IsInNextMinute`
- `DateTime.IsInNextSecond`
- `DateTime.IsInNextNHours`
- `DateTime.IsInNextNMinutes`
- `DateTime.IsInNextNSeconds`

第二组函数接收两个输入（待检查的日期和时间，以及 `N` 作为时间段的数量），用于检查某个时间是否在当前时间之前或之后的 `N` 个时间段（小时、分钟或秒）内。该组中的函数如下：

- `DateTime.IsInPreviousHour`
- `DateTime.IsInPreviousMinute`
- `DateTime.IsInPreviousNHours`
- `DateTime.IsInPreviousNMinutes`

假设当前时间是 2023 年 4 月 14 日 4:30，那么各种公式的结果如表 5-2 所示。（为缩短表格，所有函数开头的 `DateTime.IsIn` 短语已被省略，并用术语 `X` 代替日期和时间值。）

表 5-2. 日期时间信息函数

| 参考日期 (x) | `CurrentHour(X)` | `NextHour(X)` | `PreviousHour(X)` | `NextNMinutes(X,120)` | `PreviousNHours(X,12)` |
|--------------------|------------------|---------------|-------------------|-----------------------|-------------------------|
| 13/04/2023 19:36 | False | False | False | False | False |
| 12/04/2023 16:47 | False | False | False | False | False |
| 13/04/2023 13:47 | False | False | False | False | False |
| 14/04/2023 16:41 | True | False | False | True | False |
| 12/04/2023 17:32 | False | False | False | False | False |
| 12/04/2023 17:49 | False | False | False | False | False |
| 14/04/2023 4:32 | False | False | False | False | True |
| 13/04/2023 8:49 | False | False | False | False | False |
| 13/04/2023 22:48 | False | False | False | False | False |
| 13/04/2023 2:45 | False | False | False | False | False |
| 12/04/2023 22:29 | False | False | False | False | False |
| 12/04/2023 4:23 | False | False | False | False | False |
| 13/04/2023 7:47 | False | False | False | False | False |
| 14/04/2023 3:23 | False | False | False | False | False |
| 12/04/2023 2:10 | False | False | False | False | False |
| 14/04/2023 8:07 | False | False | False | False | True |
| 13/04/2023 0:08 | False | False | False | False | False |
| 13/04/2023 17:01 | False | False | False | False | False |