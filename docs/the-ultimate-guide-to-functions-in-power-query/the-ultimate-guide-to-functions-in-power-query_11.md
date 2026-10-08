## 第五章 日期和时间函数

在数据分析和处理的日新月异领域中，时间是一个不可动摇的维度。从标有精确交易时间的销售记录，到被细致追踪的项目时间线，理解和驾驭复杂多变的日期与时间世界至关重要。本章深入探讨 Power Query 的日期和时间函数，为你指明一条以精准和巧妙攻克时间数据复杂性的道路。

日期和时间不仅仅是屏幕上的数字；它们代表着业务运作的脉搏、客户行为的节奏以及事件的演进。然而，这些时间戳在数据源中常常是零散、不一致或存在差异的。Power Query 凭借其丰富的专用函数库，为协调和挖掘隐藏在时间数据中的洞察提供了关键。

探索之旅从基础概念的解析开始，以消除对日期和时间格式的神秘感。理解国际标准、时区和日期运算的细微差别，对于有效处理时间数据至关重要。

随着深入钻研，你将体验到日期和时间函数在将原始时间戳转化为清晰且有意义信息方面的强大力量。从提取星期模式到计算关键里程碑之间的持续时间，这些函数构成了时间分析的基石。

第一个函数 `DateTime.LocalNow` 无需任何参数，可按如下方式用于确定今天的日期和当前时间：`DateTime.LocalNow()` 的返回值为 `datetime` 类型。

### 日期信息

要检查某个日期是否在特定的日期范围内（例如上周或下个月），可以使用多个函数。以下是你可用于检查某个日期是否在下一个、上一个或当前时间周期（仅单个周期）内的函数列表：

`Date.IsInCurrentDay`
`Date.IsInPreviousDay`
`Date.IsInNextDay`
`Date.IsInCurrentWeek`
`Date.IsInPreviousWeek`
`Date.IsInNextWeek`
`Date.IsInCurrentMonth`
`Date.IsInPreviousMonth`
`Date.IsInNextMonth`
`Date.IsInCurrentQuarter`
`Date.IsInPreviousQuarter`
`Date.IsInNextQuarter`
`Date.IsInCurrentYear`
`Date.IsInPreviousYear`
`Date.IsInNextYear`

上述所有函数都接受一个日期格式的参数。基于输入的值，并与 `DateTime.LocalNow()` 的值进行比较，函数结果将为 `true` 或 `false`。例如，`Date.IsInCurrentMonth` 用于指示给定的日期时间值是否发生在当前月份（由系统当前日期和时间决定）。

上述函数用于评估某个日期是否在下一个或上一个时间周期内（它们可用于检查某个日期是否在下个月，但不能用于检查是否在未来两个月内）。但要检查某个日期是否在超过一个的下一个或上一个时间周期内（即 N 个周期），则可以使用以下函数之一：

`Date.IsInPreviousNDays`
`Date.IsInNextNDays`
`Date.IsInPreviousNWeeks`
`Date.IsInNextNWeeks`
`Date.IsInPreviousNMonths`
`Date.IsInNextNMonths`
`Date.IsInPreviousNQuarters`
`Date.IsInNextNQuarters`
`Date.IsInPreviousNYears`
`Date.IsInNextNYears`

上述所有函数都接受两个参数：

1.  日期值作为第一个参数输入。
2.  `N` 作为下一个或上一个周期的数量，在第二个参数中指定。

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

# 为日期添加一个值

在 Power Query 中，可以使用多个函数来对日期添加或减去一定数量的天、周、月、季度或年。这些函数（如下所述）接收两个输入：第一个输入是参考日期，第二个输入是数字格式的值（正数或负数），用于确定要从参考日期中添加（如果输入值为正）或减去（如果输入值为负）的天、周、月、季度或年的数量。此组包含以下函数：

- `Date.AddDays(dateTime as any, numberOfDays as number) as any`
- `Date.AddMonths(dateTime as any, numberOfMonths as number) as any`
- `Date.AddQuarters(dateTime as any, numberOfQuarters as number) as any`
- `Date.AddWeeks(dateTime as any, numberOfWeeks as number) as any`
- `Date.AddYears(dateTime as any, numberOfYears as number) as any`

根据函数说明，这些函数对不同示例的结果如表 5-3 所示。所有公式的第一个参数均为日期类型，已在表 5-3 的第一列中输入，并在公式中以 `x` 表示。

**表 5-3.** 日期的加减操作

| 参考日期 (x) | `AddDays(x, 2)` | `AddDays(x, -5)` | `AddMonths(x, -3)` | `AddYears(x, -2)` |
| :--- | :--- | :--- | :--- | :--- |
| 31/03/2023 | 2/04/2023 | 26/03/2023 | 31/12/2022 | 31/03/2021 |
| 5/04/2023 | 7/04/2023 | 31/03/2023 | 5/01/2023 | 5/04/2021 |
| 8/04/2023 | 10/04/2023 | 3/04/2023 | 8/01/2023 | 8/04/2021 |

*(续表)*

**表 5-3.** *(续表)*

| 参考日期 (x) | `AddDays(x, 2)` | `AddDays(x, -5)` | `AddMonths(x, -3)` | `AddYears(x, -2)` |
| :--- | :--- | :--- | :--- | :--- |
| 12/04/2023 | 14/04/2023 | 7/04/2023 | 12/01/2023 | 12/04/2021 |
| 13/04/2023 | 15/04/2023 | 8/04/2023 | 13/01/2023 | 13/04/2021 |
| 14/04/2023 | 16/04/2023 | 9/04/2023 | 14/01/2023 | 14/04/2021 |
| 16/04/2023 | 18/04/2023 | 11/04/2023 | 16/01/2023 | 16/04/2021 |
| 22/04/2023 | 24/04/2023 | 17/04/2023 | 22/01/2023 | 22/04/2021 |
| 24/04/2023 | 26/04/2023 | 19/04/2023 | 24/01/2023 | 24/04/2021 |
| 28/04/2023 | 30/04/2023 | 23/04/2023 | 28/01/2023 | 28/04/2021 |
| 3/05/2023 | 5/05/2023 | 28/04/2023 | 3/02/2023 | 3/05/2021 |
| 4/05/2023 | 6/05/2023 | 29/04/2023 | 4/02/2023 | 4/05/2021 |
| 9/05/2023 | 11/05/2023 | 4/05/2023 | 9/02/2023 | 9/05/2021 |
| 17/05/2023 | 19/05/2023 | 12/05/2023 | 17/02/2023 | 17/05/2021 |
| 19/05/2023 | 21/05/2023 | 14/05/2023 | 19/02/2023 | 19/05/2021 |

## 日期偏移（Shifting Dates）

要确定某个特定日期的周、月、季度或年的起始或结束日期，可以使用以下函数：

- `Date.EndOfDay`
- `Date.StartOfDay`
- `Date.EndOfWeek`
- `Date.StartOfWeek`
- `Date.EndOfMonth`
- `Date.StartOfMonth`
- `Date.EndOfQuarter`
- `Date.StartOfQuarter`
- `Date.EndOfYear`
- `Date.StartOfYear`

因此，通过在 `Date.EndOfYear` 的第一个参数中输入一个日期，将显示该日期所在年份的最后一天，而 `Date.StartOfYear` 的结果是该年份的第一天。参见表 5-4。

**表 5-4.** 日期偏移函数

| 参考日期 | `Date.EndOfDay` | `Date.EndOfWeek` | `Date.EndOfMonth` | `Date.EndOfYear` |
| :--- | :--- | :--- | :--- | :--- |
| 27/07/2020 | 2/08/2020 | 31/07/2020 | 30/09/2020 | 31/12/2020 |
| 30/07/2020 | 2/08/2020 | 31/07/2020 | 30/09/2020 | 31/12/2020 |
| 8/11/2020 | 8/11/2020 | 30/11/2020 | 31/12/2020 | 31/12/2020 |
| 5/01/2021 | 10/01/2021 | 31/01/2021 | 31/03/2021 | 31/12/2021 |
| 8/04/2021 | 11/04/2021 | 30/04/2021 | 30/06/2021 | 31/12/2021 |
| 6/09/2021 | 12/09/2021 | 30/09/2021 | 30/09/2021 | 31/12/2021 |
| 2/10/2021 | 3/10/2021 | 31/10/2021 | 31/12/2021 | 31/12/2021 |
| 19/11/2021 | 21/11/2021 | 30/11/2021 | 31/12/2021 | 31/12/2021 |
| 27/11/2021 | 28/11/2021 | 30/11/2021 | 31/12/2021 | 31/12/2021 |
| 26/12/2021 | 26/12/2021 | 31/12/2021 | 31/12/2021 | 31/12/2021 |
| 14/02/2022 | 20/02/2022 | 28/02/2022 | 31/03/2022 | 31/12/2022 |
| 30/03/2022 | 3/04/2022 | 31/03/2022 | 31/03/2022 | 31/12/2022 |
| 28/04/2022 | 1/05/2022 | 30/04/2022 | 30/06/2022 | 31/12/2022 |
| 12/06/2022 | 12/06/2022 | 30/06/2022 | 30/06/2022 | 31/12/2022 |
| 22/07/2022 | 24/07/2022 | 31/07/2022 | 30/09/2022 | 31/12/2022 |
| 7/11/2022 | 13/11/2022 | 30/11/2022 | 31/12/2022 | 31/12/2022 |
| 24/01/2023 | 29/01/2023 | 31/01/2023 | 31/03/2023 | 31/12/2023 |
| 26/03/2023 | 26/03/2023 | 31/03/2023 | 31/03/2023 | 31/12/2023 |

这些函数的结果如表 5-5 所示。

**表 5-5.** 日期偏移函数

| 参考日期 | `StartOfDay` | `StartOfWeek` | `StartOfMonth` | `StartOfQuarter` | `StartOfYear` |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 27/07/2020 | 27/07/2020 | 27/07/2020 | 1/07/2020 | 1/07/2020 | 1/01/2020 |

| 30/07/2020 | 30/07/2020 | 27/07/2020 | 1/07/2020 | 1/07/2020 | 1/01/2020 |
| 8/11/2020 | 8/11/2020 | 2/11/2020 | 1/11/2020 | 1/10/2020 | 1/01/2020 |
| 5/01/2021 | 5/01/2021 | 4/01/2021 | 1/01/2021 | 1/01/2021 | 1/01/2021 |
| 8/04/2021 | 8/04/2021 | 5/04/2021 | 1/04/2021 | 1/04/2021 | 1/01/2021 |
| 6/09/2021 | 6/09/2021 | 6/09/2021 | 1/09/2021 | 1/07/2021 | 1/01/2021 |
| 2/10/2021 | 2/10/2021 | 27/09/2021 | 1/10/2021 | 1/10/2021 | 1/01/2021 |
| 19/11/2021 | 19/11/2021 | 15/11/2021 | 1/11/2021 | 1/10/2021 | 1/01/2021 |
| 27/11/2021 | 27/11/2021 | 22/11/2021 | 1/11/2021 | 1/10/2021 | 1/01/2021 |
| 26/12/2021 | 26/12/2021 | 20/12/2021 | 1/12/2021 | 1/10/2021 | 1/01/2021 |
| 14/02/2022 | 14/02/2022 | 14/02/2022 | 1/02/2022 | 1/01/2022 | 1/01/2022 |
| 30/03/2022 | 30/03/2022 | 28/03/2022 | 1/03/2022 | 1/01/2022 | 1/01/2022 |
| 28/04/2022 | 28/04/2022 | 25/04/2022 | 1/04/2022 | 1/04/2022 | 1/01/2022 |
| 12/06/2022 | 12/06/2022 | 6/06/2022 | 1/06/2022 | 1/04/2022 | 1/01/2022 |
| 22/07/2022 | 22/07/2022 | 18/07/2022 | 1/07/2022 | 1/07/2022 | 1/01/2022 |
| 7/11/2022 | 7/11/2022 | 7/11/2022 | 1/11/2022 | 1/10/2022 | 1/01/2022 |
| 24/01/2023 | 24/01/2023 | 23/01/2023 | 1/01/2023 | 1/01/2023 | 1/01/2023 |
| 26/03/2023 | 26/03/2023 | 20/03/2023 | 1/03/2023 | 1/01/2023 | 1/01/2023 |

## 转换为记录

有几个函数可用于将日期值转换为记录。首先，`Time.ToRecord` 函数用于将时间值转换为记录，如下所示：

`Time.ToRecord(时间 as time) as record`

根据函数语法，它接收一个时间类型的输入，并在输出中以记录形式显示时间值。因此，公式 `Time.ToRecord(#time(2,10,30))` 的结果等于以下值：

```
[Hour=2, Minute=10, Second=30]
```

类似地，`Date.ToRecord` 函数用于将日期值转换为记录，如下所示：

`Date.ToRecord(日期 as date) as record`

根据函数语法，它接收一个日期格式的输入，并在输出中以记录形式显示日期值。因此，公式 `Date.ToRecord(#date(2022,5,3))` 的结果等于以下值：

```
[Year=2022, Month=5, Day=3]
```

此组中的另一个函数是 `DateTime.ToRecord`，它用于将日期和时间值转换为记录。此函数接收一个 datetime 类型的输入，并将其值作为记录输出。

`DateTime.ToRecord(日期时间 as datetime) as record`

如您所见，此函数接收一个输入，并在输出中以记录形式显示其值。因此，公式 `DateTime.ToRecord(#datetime(2022,5,3,2,10,30))` 的结果等于以下值：

```
[Year=2022, Month=5, Day=3, Hour=2, Minute=10, Second=30]
```

## 日期规范

在 Power Query 中有不同的函数（如下所述）用于从日期值中提取信息（天数、月数或年数）。

### Date.Day

此函数在 Power Query 中定义如下，它接收一个日期类型的输入，并在输出中显示参考日期的日期值。

`Date.Day(日期时间 as any) as nullable number`

换句话说，此函数在输出中显示输入日期的“日”部分。因此，以下公式的结果将等于数字 3：

```
= Date.Day(#date(2022,5,3))
```

### Date.DayOfWeek

使用此函数，可以确定某个日期的星期数（在一周中的第几天）。此函数在 Power Query 中定义如下，并接收两个输入。第一个输入是日期类型，第二个输入通过传入 `Day.Sunday` 或 `Day.Monday` 等值，来指定每周的第一天是哪一天。

```
Date.DayOfWeek(
    日期时间 as any,
    optional 一周的第一天 as nullable number
) as nullable number
```

此函数始终返回一个数字，表示该日期是星期几。以下公式的结果等于 1：

```
= Date.DayOfWeek(#date(2022,5,3))
```

### Date.DayOfWeekName

# 第五章 日期和时间函数

此函数与`Date.DayOfWeek`函数类似，但输出结果将显示星期名称而非星期数字。该函数在 Power Query 中的定义如下：

```
Date.DayOfWeekName(
    date as any,
    optional culture as nullable text
)
```

该函数的第一个输入代表参考日期，第二个输入为可选参数，可通过输入如`"en-US"`的表达式来确定星期命名的规则。以下公式的结果等于 Tuesday（星期二）：

```
= Date.MonthName(#date(2022,5,3))
```

## `Date.WeekOfMonth`

要确定某个月份中的周序号，可以使用以下函数：

```
Date.WeekOfMonth(
    dateTime as any,
    optional firstDayOfWeek as nullable number
) as nullable number
```

根据函数语法，该函数接收两个输入。第一个输入为日期类型，在第二个输入中，输入诸如`Day.Sunday`或`Day.Monday`的值，可指定一周的第一天是哪一天。根据函数说明，以下公式的结果等于 19，表示该日期属于该年的第 19 周：

```
= Date.WeekOfYear(#date(2022,5,3))
```

## `Date.WeekOfYear`

要确定某个日期的周序号（从年初开始），可按如下方式使用此函数：

```
Date.WeekOfYear(
    dateTime as any,
    optional firstDayOfWeek as nullable number
) as nullable number
```

基于函数语法，它接受两个输入。第一个输入为日期类型，在第二个输入中，输入诸如`Day.Sunday`或`Day.Monday`的值，可指定一周的第一天是哪一天。根据函数说明，以下公式的结果等于 19，表示该日期属于该年的第 19 周：

```
= Date.WeekOfYear(#date(2022,5,3))
```

## `Date.Month`

此函数在 Power Query 中的定义如下，它接收一个日期类型的输入，并指定所输入日期的月份数字：

```
Date.Month(dateTime as any) as nullable number
```

换句话说，该函数在输出中显示输入日期的月份部分。因此，以下公式的结果等于 5：

```
= Date.Month(#date(2022,5,3))
```

## `Date.MonthName`

此函数与`Date.Month`函数类似，但输出结果将显示月份名称而非月份数字。该函数在 Power Query 中的定义如下：

```
Date.MonthName(
    date as any,
    optional culture as nullable text
) as nullable text
```

该函数的第一个输入代表参考日期，在第二个可选输入中，你可以通过输入如`"en-US"`的表达式来定义月份命名的规则。以下公式的结果等于 May（五月）：

```
= Date.MonthName(#date(2022,5,3))
```

## `Date.DayOfYear`

此函数在 Power Query 中的定义如下，它接收一个日期类型的输入，并在输出中指定所输入日期自年初以来的天数：

```
Date.DayOfYear(dateTime as any) as nullable number
```

因此，以下公式的结果是 123，表示该日期属于该年的第 123 天：

```
= Date.DayOfYear(#date(2022,5,3))
```

## `Date.Year`

此函数在 Power Query 中的定义如下。它接收一个日期类型的输入，并在输出中确定输入日期的年份值。

```
Date.Year(dateTime as any) as nullable number
```

换句话说，该函数在输出中显示输入日期的年份部分。因此，以下公式的结果等于 2022：

```
= Date.Year(#date(2022,5,3))
```

### 总结

在本章中，你开启了一段掌握 Power Query 中日期和时间函数复杂性的旅程。在数据分析领域，时间是一个关键维度，需要精确和技巧才能有效驾驭。
