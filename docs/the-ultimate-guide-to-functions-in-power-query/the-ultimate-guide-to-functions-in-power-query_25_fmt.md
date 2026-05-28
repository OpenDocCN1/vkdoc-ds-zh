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