# 排版后的 Markdown 文档

为了完善 Power Query 的函数体系，下一章将介绍与日期和时间操作相关的函数。这些函数用于从时间和日期格式的值中提取所需信息。因此，你将学习如何对日期进行加减特定值的操作，或如何比较不同的日期值。

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



