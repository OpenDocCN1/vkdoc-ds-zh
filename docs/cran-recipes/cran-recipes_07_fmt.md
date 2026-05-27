# 3. Lubridate：日期与时间处理

`Lubridate` 从回答一个简单的问题开始——基于日期和小时，它是上午还是下午？从那里开始，它变得更加复杂，但在处理日期、时间以及组合的日期时间时保持了一致的方法。

## 3.1 硬编码咖啡时间

```r
if (!require("lubridate")) install.packages("lubridate")
bk.coffee.time <- as.POSIXct("080418 10:11",
format = "%y%m%d %H:%M")
am(bk.coffee.time)
## [1] TRUE
bk.time.span <- interval(ymd("2019-01-01"), ymd("2019-06-30"))
bk.time.span.value <- as.duration(bk.time.span)
bk.time.span.value
## [1] "15552000s (~25.71 weeks)"
```

## 3.2 持续时间计算

注意以下输出以秒为单位，并且是字符格式：

```r
bk.how.long.in.hours.and.minutes <- duration(hours = 4,
minutes = 10)
bk.how.long.in.hours.and.minutes
## [1] "15000s (~4.17 hours)"
bk.hours.numeric <-
as.numeric(bk.how.long.in.hours.and.minutes,"hours")
bk.hours.numeric
## [1] 4.166667
bk.minutes.numeric <-
as.numeric(bk.how.long.in.hours.and.minutes,"minutes")
bk.minutes.numeric
## [1] 250
```

显示五天的格式：

```r
bk.five.whole.days <- as.period(5, unit = "day") #five days
bk.five.whole.days
## [1] "5d 0H 0M 0S"
```

## 3.3 使用 Interval 跨越两个日期

如何跨越两个日期——2009 年与 2010 年？此语法展示了每种时间度量之一——年、月、日、小时、分钟和秒：

```r
bk.span.of.two.dates <- interval(ymd_hms("2009-01-01 00:00:00"),
ymd_hms("2010-02-02 01:01:01"))
as.period(bk.span.of.two.dates)
## [1] "1y 1m 1d 1H 1M 1S"
```

将跨度更改为同一天的两个年份之间：

```r
bk.span.of.two.dates <- interval(ymd_hms("2009-01-01 00:00:00"),
ymd_hms("2010-01-01 00:00:00"))
as.period(bk.span.of.two.dates, unit = "day")
## [1] "365d 0H 0M 0S"
```

显示闰年与非闰年之间的跨度：

```r
bk.leap.year.span <- interval(ymd_hms("2016-01-01 00:00:00"),
ymd_hms("2017-01-01 01:00:00"))
as.period(bk.leap.year.span, unit = "day")
## [1] "366d 1H 0M 0S"
```

### 3.3.1 处理时区

```r
bk.chicago.time.interval <- interval(ymd("2016/11/06",
tz = "America/Chicago"),ymd("2016/11/09"))
#you can also enter date as 2016-11-06
bk.hours <- as.period(bk.chicago.time.interval, unit = "hours")
bk.hours
## [1] "67H 0M 0S"
```

现在使用爱尔兰的贝尔法斯特作为时区：

```r
bk.europe.belfast.time.interval <- interval(ymd("2016/11/06",
tz = "Europe/Belfast"),ymd("2016/11/09"))
bk.hours <- as.period(bk.europe.belfast.time.interval,
unit = "hours")
bk.hours
## [1] "72H 0M 0S"
```

持续时间：两个时间点之间的时间量是多少？在此语法中，`%--%` 运算符通过从结束日期减去开始日期来创建日期区间。请注意，计算出的变量 `bk.interval` 包含了两个日期/时间：开始和结束。

```r
bk.start.date.time <- mdy_hm("8-11-2017 5:21",
tz = "US/Eastern")
bk.end.date.time  <- mdy_hm("8-12-2018 6:21",
tz = "US/Eastern")
bk.interval <- bk.start.date.time %--% bk.end.date.time
bk.interval
## [1] 2017-08-11 05:21:00 EDT--2018-08-12 06:21:00 EDT
```

请注意，`bk.interval` 是一个特殊的类：

```r
class(bk.interval)
[1] "Interval"
attr(,"package")
[1] "lubridate"
```

## 3.4 计算两个日期/时间之间的持续时间（秒）

根据正确格式化的区间计算持续时间：

```r
bk.duration <- as.duration(bk.interval)
bk.duration
## [1] "31626000s (~1 years)"
```

这是历史上的另一个例子。1701 年 1 月 18 日，腓特烈三世将普鲁士从公国升级为王国。二战后的 1947 年 2 月 25 日，盟国管制委员会宣布普鲁士国“自此不复存在”。普鲁士作为一个被承认的国家，存在了多少年？

```r
bk.prussia.started <- ymd("1701-01-18")
bk.prussia.ended <- ymd("1947-02-25")
bk.interval <- bk.prussia.started %--% bk.prussia.ended
bk.prussia.in.existence <- as.duration(bk.interval)
bk.prussia.in.existence
## [1] "7766236800s (~246.1 years)"
```

普鲁士存在了略多于 246 年。

使用 `as.duration` 函数时，输出结果以秒为单位。

```r
bk.seventeen.seconds <- as.duration(17)
bk.seventeen.seconds
## [1] "17s"
```

要使用不同的时间度量单位显示结果，可以使用 `hours` 或 `minutes` 作为参数：

```r
as.numeric(bk.seventeen.seconds, "hours")
## [1] 0.004722222
as.numeric(bk.seventeen.seconds, "minutes")
## [1] 0.2833333
```

请注意，`duration` 和 `as.duration` 是不同的函数：

```r
bk.hours <- 25
bk.minutes <- 8
bk.twenty.five.hours <- duration(hours = bk.hours)
bk.twenty.five.hours
## [1] "90000s (~1.04 days)"
```

将小时和分钟指定为持续时间参数：

```r
bk.twenty.five.hours.and.eight.minutes <-
duration(hours = bk.hours, minutes = bk.minutes)
bk.twenty.five.hours.and.eight.minutes
## [1] "90480s (~1.05 days)"
```

将区间显示为持续时间：

```r
bk.time.span <- interval(ymd("2019-01-01"), ymd("2019-06-30")) 
bk.time.span.value <- as.duration(bk.time.span)
bk.time.span.value
##[1] "15552000s (~25.71 weeks)"
```

以小时显示持续时间：

```r
dur <- duration(hours = 10, minutes = 6)
as.numeric(dur, "hours")
## [1] 10.1
```

以分钟显示持续时间：

```r
as.numeric(dur, "minutes")
## [1] 606
```

## 3.5 更多区间计算

`interval` 函数先显示结束日期：

```r
interval(ymd(20090201), ymd(20090101))
## [1] 2009-02-01 UTC--2009-01-01 UTC
bk.date1 <- ymd_hms("2009-03-08 01:59:59")
bk.date2 <- ymd_hms("2000-02-29 12:00:00")
bk.interval <- interval(bk.date2, bk.date1)
bk.interval
## [1] 2000-02-29 12:00:00 UTC--2009-03-08 01:59:59 UTC
```

这是计算区间的另一种方法：

```r
bk.interval.alternate.method <-
lubridate::`%--%`(lubridate::ymd("2009-03-08"),
lubridate::ymd("2000-02-29"))
bk.interval.alternate.method
## [1] 2009-03-08 UTC--2000-02-29 UTC
```

交换开始和结束日期：

```r
bk.interval.negative <-interval(bk.date1, bk.date2)
bk.interval.negative
## [1] 2009-03-08 01:59:59 UTC--2000-02-29 12:00:00 UTC
```

显示单个月份的跨度：

```r
bk.span <- interval(ymd(20090101), ymd(20090201))
bk.span
## [1] 2009-01-01 UTC--2009-02-01 UTC
```

以秒为单位显示区间：

```r
bk.interval.length.in.seconds <- int_length(bk.span)
bk.interval.length.in.seconds
## [1] 2678400
```

作为合理性检查，将之前计算的以秒为单位的区间与直接计算 31 天 * 24 小时 * 60 分钟 * 60 秒的结果进行比较：

```r
bk.comparison <- 31*24*60*60
bk.comparison
## [1] 2678400
```

区间开始时间：

```r
bk.start <- int_start(bk.span)
bk.start
## [1] "2009-01-01 UTC"
```

区间停止时间：

```r
bk.stop <- int_end(bk.span)
bk.stop
## [1] "2009-02-01 UTC"
```

当开始和结束时间颠倒时，`bk.span` 会是什么样子？它会显示镜像：

```r
bk.span.flipped <- int_flip(bk.span)
bk.span.flipped
## [1] 2009-02-01 UTC--2009-01-01 UTC
```

## 3.6 区间重叠

创建一个新的区间。
结束日期：

```r
bk.date1.second.range <- ymd_hms("2010-06-08 01:59:59")
```

开始日期：

```r
bk.date2.second.range <- ymd_hms("2010-04-29 12:00:00")
bk.another.span <- interval(bk.date1.second.range,
bk.date2.second.range)
bk.another.span
## [1] 2010-06-08 01:59:59 UTC--2010-04-29 12:00:00 UTC
```

这第二个跨度是否与第一个跨度重叠？不重叠，因为第二个区间设定在 2010 年：

```r
bk.overlap.or.not <- int_overlaps(bk.another.span,bk.span)
bk.overlap.or.not
## [1] FALSE
```

现在更改范围，使它们重叠。
结束日期：

```r
bk.date1.second.range <- ymd_hms("2009-06-08 01:59:59")
```

开始日期：

```r
bk.date2.second.range <- ymd_hms("2008-04-29 12:00:00")
bk.another.span <- interval(bk.date1.second.range,
bk.date2.second.range)
bk.another.span
## [1] 2009-06-08 01:59:59 UTC--2008-04-29 12:00:00 UTC
```

这第二个跨度是否与第一个跨度重叠？

```r
bk.overlap.or.not <- int_overlaps(bk.another.span,bk.span)
bk.overlap.or.not
## [1] TRUE
```

## 3.7 区间偏移

计算区间偏移。当整个时间表向前或向后移动一定时间量时使用此功能^(¹⁹)：

```r
int <- interval(ymd("2001-01-01"), ymd("2002-01-01"))
int_shift(int, duration(days = 11))
## [1] 2001-01-12 UTC--2002-01-12 UTC
int_shift(int, duration(hours = -1))
## [1] 2000-12-31 23:00:00 UTC--2001-12-31 23:00:00 UTC
```

颠倒日期顺序：

```r
bk.interval <- interval(ymd("2001-01-01"), ymd("2002-11-11"))
bk.interval
## [1] 2001-01-01 UTC--2002-11-11 UTC
bk.interval.flip <- int_flip(bk.interval)
bk.interval.flip
## [1] 2002-11-11 UTC--2001-01-01 UTC
```

## 3.8 对齐（Alignment）

日期和时间测量始于古代历史，最早可追溯到苏美尔人的六十进制系统。此后，不断需要进行修改。即使在今天，偶尔仍需进行细微调整，以修正地球自转的不完美。近年测量技术的进步，使得因苏门答腊等地地震引起的地球自转速率变化得以被察觉。^(²⁰)

起始日期和结束日期是否匹配？lubridate 会提供一个逻辑值（真/假）作为答案。

```r
bk.date1 <- ymd_hms("2009-06-08 01:59:59")  #结束日期
bk.date2 <- ymd_hms("2008-04-29 12:00:00")  #起始日期
bk.span1 <- interval(bk.date1,bk.date2)
bk.date3 <- ymd_hms("2011-06-08 01:59:59")  #结束日期
bk.date4 <- ymd_hms("2008-04-29 12:00:00")  #起始日期
bk.span2 <- interval(bk.date1,bk.date2)
bk.aligns.true.or.false <- int_aligns(bk.span1, bk.span2)
bk.aligns.true.or.false
## [1] TRUE
```

答案是真，因为两个时间区间共享同一个起始日期。

通过 `int_diff()` 计算出的时间区间（日期）：

```r
bk.multiple.dates <- bk.date4 + days(1:5)
bk.multiple.dates
## [1] "2008-04-30 12:00:00 UTC" "2008-05-01 12:00:00 UTC"
## [3] "2008-05-02 12:00:00 UTC" "2008-05-03 12:00:00 UTC"
## [5] "2008-05-04 12:00:00 UTC"
```

创建一个包含五个日期的向量——4 月 30 日、5 月 1 日、5 月 2 日、5 月 3 日和 5 月 4 日：

```r
bk.int.diff <- int_diff(bk.multiple.dates)
bk.int.diff
## [1] 2008-04-30 12:00:00 UTC--2008-05-01 12:00:00 UTC
## [2] 2008-05-01 12:00:00 UTC--2008-05-02 12:00:00 UTC
## [3] 2008-05-02 12:00:00 UTC--2008-05-03 12:00:00 UTC
## [4] 2008-05-03 12:00:00 UTC--2008-05-04 12:00:00 UTC
```

## 3.9 周期（Periods）

周期记录的是挂钟时间（wall-clock time）的变化，例如，今晚电视剧《生活大爆炸》从开始到结束之间的时间。一个时间单位（比如“年”）的长度会发生变化，取决于它是平年还是闰年。也可能有其他原因。秒是恒定不变的，但任何更高级别的时间单位（例如“分钟”）都会根据其在日历时间中的位置而变化。如果你从事的项目对时间精度要求很高，那么理解 R 语言日期时间计算的细微差别就变得至关重要。

创建一些可以添加到日期时间中的周期，使得结果是一个有效的开始/停止日期时间：

```
bk.ninety.seconds.and.five.minutes <- period(c(90, 5),
c("second", "minute"))
bk.ninety.seconds.and.five.minutes
## [1] "5M 90S"
class(bk.ninety.seconds.and.five.minutes)
## [1] "Period"
## attr(,"package")
## [1] "lubridate"
bk.ninety.seconds.and.five.minutes
## [1] "5M 90S"
```

一些基本计算：

```
period(-1, "days")
## [1] "-1d 0H 0M 0S"
period(c(3, 1, 2, 13, 1),
c("second", "minute", "hour", "day", "week"))
## [1] "20d 2H 1M 3S"
period(c(1, -60), c("hour", "minute"))
## [1] "1H -60M 0S"
period(0, "second")
## [1] "0S"
```

## 3.10 序列生成（Sequencing）

有时你需要计算接下来的 N 个周期。这包括分钟、小时、天、月、年等。

```
bk.sequence.start <- ymd(190102)   #2019 年 1 月 2 日
bk.next.12.months <- bk.sequence.start + months(1:12)
bk.next.12.months
##  [1] "2019-02-02" "2019-03-02" "2019-04-02" "2019-05-02" "2019-06-02"
##  [6] "2019-07-02" "2019-08-02" "2019-09-02" "2019-10-02" "2019-11-02"
## [11] "2019-12-02" "2020-01-02"
length(bk.next.12.months)
## [1] 12
```

在特定日期之间生成日期序列：

```
start1 <- mdy("06/01/20")
end1 <- mdy("06/10/20")
end2 <- mdy("09/30/25")
x <- seq(start1, end1,"days")
x
##  [1] "2020-06-01" "2020-06-02" "2020-06-03" "2020-06-04" "2020-06-05"
##  [6] "2020-06-06" "2020-06-07" "2020-06-08" "2020-06-09" "2020-06-10"
```

按月生成一个包含 24 个月的序列：

```
y <- seq(start1, by = "month", length.out = 24)
y
##  [1] "2020-06-01" "2020-07-01" "2020-08-01" "2020-09-01" "2020-10-01"
##  [6] "2020-11-01" "2020-12-01" "2021-01-01" "2021-02-01" "2021-03-01"
## [11] "2021-04-01" "2021-05-01" "2021-06-01" "2021-07-01" "2021-08-01"
## [16] "2021-09-01" "2021-10-01" "2021-11-01" "2021-12-01" "2022-01-01"
## [21] "2022-02-01" "2022-03-01" "2022-04-01" "2022-05-01"
```

使用季度生成一个序列：

```
q <- seq(start1, end2,by = "quarter")
q
##  [1] "2020-06-01" "2020-09-01" "2020-12-01" "2021-03-01" "2021-06-01"
##  [6] "2021-09-01" "2021-12-01" "2022-03-01" "2022-06-01" "2022-09-01"
## [11] "2022-12-01" "2023-03-01" "2023-06-01" "2023-09-01" "2023-12-01"
## [16] "2024-03-01" "2024-06-01" "2024-09-01" "2024-12-01" "2025-03-01"
## [21] "2025-06-01" "2025-09-01"
```

`Lubridate` 风格的解析：

```
period("2M 1sec")
## [1] "2M 1S"
period("2hours 2minutes 1second")
## [1] "2H 2M 1S"
period("2d 2H 2M 2S")
## [1] "2d 2H 2M 2S"
period("2days 2hours 2mins 2secs")
## [1] "2d 2H 2M 2S"
```

数字缺失时默认为 1。重复的单位会被累加：

```
duration("day day")
## [1] "172800s (~2 days)"
```

## 3.11 `Period` 与 `Duration` 的区别

根据弗吉尼亚大学的一个网站^(²¹)：

*   **时间区间（`Interval`）** 是两个特定日期之间以秒为单位的流逝时间。（如果未提供具体时间，则每个日期的时间默认为 00:00:00，即午夜。）
*   **持续时长（`Duration`）** 是独立的、以秒为单位的流逝时间，与起始日期无关。
*   **周期（`Period`）** 是以“日历”或“时钟”时间（如 4 周、2 个月等）度量的流逝时间，与起始日期无关。

在查看以下示例时，请牢记这些区别。"tz" 代表时区：

```
bk.boundary <- ymd_hms("2009-03-08 01:59:59",
tz="America/Chicago")
bk.boundary
## [1] "2009-03-08 01:59:59 CST"
bk.boundary2 <- bk.boundary + days(1) # 周期
bk.boundary2
## [1] "2009-03-09 01:59:59 CDT"
bk.boundary3 <- bk.boundary + ddays(1) # 持续时长
bk.boundary3
## [1] "2009-03-09 02:59:59 CDT"
bk.true.false <- is.period(as.Date("2009-08-03"))
bk.true.false
## [1] FALSE
```

将秒转换为周期，以及将周期转换为秒。周期会略有变化，因此要转换为精确的秒数，需要做一些假设。假设一个日历年为 365.25 天，一个月为 365.25/12 = 30.4375 天。

两天的秒数 = 48 小时 x 60 分钟 x 60 秒 = 172,800。`seconds_to_period()` 函数将一定量的秒数转换回更易读的天数、小时数等。

```
bk.period.from.seconds <- seconds_to_period(172800)
bk.period.from.seconds
## [1] "2d 0H 0M 0S"
```

将变量设置为 1 年零 1 个月 = 13 个月：

```
bk.period.to.seconds <- period_to_seconds(years(1) + months(1))
bk.period.to.seconds
## [1] 34187400
bk.period.to.seconds.in.months <- bk.period.to.seconds /
(60*60*24*30.4375)
bk.period.to.seconds.in.months
## [1] 13
```

## 3.12 时间跨度

`Timespan` 使用三种度量方式：

*   `Duration`（持续时间）
*   `Interval`（时间区间）
*   `Period`（时段）

`Lubridate` 手册^(²²)对这三种度量方式定义如下：

*   `Duration` 精确记录时间跨度内的总秒数。它们衡量时间的精确流逝，但并不总是与人造时间单位（如小时、月、年）对齐。
*   `Period` 记录两个日期时间之间时钟时间的变化。它以人造单位计量：年、月、日、小时、分钟和秒。
*   `Interval` 是由两个真实日期时间界定的时间跨度。`Interval` 可以精确地转换为时段和持续时间。

```
bk.duration <-duration(3690, "seconds")
bk.duration
## [1] "3690s (~1.02 hours)"
bk.period <- period(3690, "seconds")
bk.period
## [1] "3690S"
bk.define.second.minute.hour <- period(second = 30,
minute = 1, hour = 1)
bk.define.second.minute.hour
## [1] "1H 1M 30S"
```

开始/结束的实际日历日期和挂钟时间：

```
bk.interval <- interval(ymd_hms("2009-08-09 13:01:30"),
ymd_hms("2009-08-09 12:00:00"))
bk.interval
## [1] 2009-08-09 13:01:30 UTC--2009-08-09 12:00:00 UTC
```

此示例来自优秀的网站 `rdocumentation.org`^(²³)：

```
date1 <- ymd_hms("2009-03-08 01:59:59")
date2 <- ymd_hms("2000-02-29 12:00:00")
interval(date2, date1)
## [1] 2000-02-29 12:00:00 UTC--2009-03-08 01:59:59 UTC
interval(date1, date2)
## [1] 2009-03-08 01:59:59 UTC--2000-02-29 12:00:00 UTC
```

夏令时边界。添加 `days(1)` 会将日期/时间精确地推迟一天：

```
bk.date <- ymd_hms("2009-03-08 01:59:59")
bk.one.more.day <- bk.date + days(1)
bk.date
## [1] "2009-03-08 01:59:59 UTC"
bk.one.more.day
## [1] "2009-03-09 01:59:59 UTC"
bk.date + ddays(1)
```

让我们看看能否通过从 2 月 29 日开始请求恰好一个日历年，来“欺骗”`Lubridate`：

```
bk.date2 <- ymd_hms("2000-02-29 12:00:00")
bk.one.year.later <- bk.date2 + years(1)
bk.one.year.later
## [1] NA
```

不，`Lubridate` 知道闰年，不会允许你这样做。从 2000 年 2 月 29 日向前推恰好一年将得到 2001 年 2 月 29 日，这是一个无效日期。因此，结果为 `NA`。在处理日期方面，`Lubridate` 确实是最聪明的软件包。

使用 `dyears` 代替 `years`，我们就能得到一个关于闰年后一年是什么日期的合乎逻辑的答案。

```
bk.date2 <- ymd_hms("2000-02-29 12:00:00")
bk.one.year.later <- bk.date2 + dyears(1)
bk.one.year.later
## [1] "2001-02-28 18:00:00 UTC"
```

使用此逻辑来显示向前推进一个月后的所有月份。对于少于 31 天的月份，会显示 `NA`。

```
bk.date3 <- ymd_hms("2009-01-31 01:00:00")
bk.date3 + c(0:11) * months(1)
##  [1] "2009-01-31 01:00:00 UTC" NA
##  [3] "2009-03-31 01:00:00 UTC" NA
##  [5] "2009-05-31 01:00:00 UTC" NA
##  [7] "2009-07-31 01:00:00 UTC" "2009-08-31 01:00:00 UTC"
##  [9] NA                        "2009-10-31 01:00:00 UTC"
## [11] NA                        "2009-12-31 01:00:00 UTC"
```

创建一个时间区间：

```
bk.span <- bk.date2 %--% bk.date
bk.span
## [1] 2000-02-29 12:00:00 UTC--2009-03-08 01:59:59 UTC
```

向前推进一年：

```
bk.date4 <- ymd_hms("2009-01-01 00:00:00")
bk.date5  <- bk.date4 + years(1)
bk.date5
## [1] "2010-01-01 UTC"
```

往回推三天，然后向前推六小时：

```
bk.date7  <- ymd_hms("2009-03-08 01:59:59") # DST 边界
bk.date8 <- bk.date7 - days(3) + hours(6)
bk.date8
## [1] "2009-03-05 07:59:59 UTC"
```

穿越夏令时边界。注意 `Lubridate` 在这里是如何将时间往回拨的。

```
bk.date <- ymd_hms("2009-03-08 01:59:59")
bk.date9 <- bk.date + 3 * seconds(10)
bk.date  # 开始
## [1] "2009-03-08 01:59:59 UTC"
bk.date9 # 结束
## [1] "2009-03-08 02:00:29 UTC"
```

按指定的月份和天数向前推进。你可以将 `bk.date10` 视为一个时间块：

```
bk.date10 <- months(6) + days(1)
bk.date10
## [1] "6m 1d 0H 0M 0S"
```

现在，将 `bk.date10` 加到我正在写作的日期，2021-1-22 上：

```
date.in.future.6mo.1day <- ymd("2021-01-22") + bk.date10
date.in.future.6mo.1day
## [1] "2021-07-23"
```

将一个由两个日历日期所表示的时间区间转换为周数。为了验证结果，将 52 周乘以 34.826 年得到 1811.286 周。再次注意，一周并不是一个精确的时间刻度数，因为在该时间跨度内存在许多闰年。^(²⁴)

```
bk.int <- interval(ymd("1980-01-01"), ymd("2014-09-18"))
bk.time.length.in.weeks <- time_length(bk.int, "week")
bk.time.length.in.weeks
## [1] 1811.286
```

相同的计算，使用年作为度量单位：

```
bk.year.age.at.last.whole.year <- time_length(bk.int, "year")
bk.year.age.at.last.whole.year
## [1] 34.71233
```

使用 `trunc`（截断）函数来获取完整的年数：

```
bk.truncated.time.length <- trunc(time_length(bk.int, "year"))
bk.truncated.time.length
## [1] 34
```

### 3.12.1 时间区间与持续时间的对比

```
bk.int <- interval(ymd("1900-01-01"), ymd("1999-12-31"))
bk.int
## [1] 1900-01-01 UTC--1999-12-31 UTC
```

使用 `time_length` 显示一年：

```
bk.time.interval.length <-  time_length(bk.int, "year")
bk.time.interval.length
## [1] 99.99726
bk.time.interval.as.year <- time_length(as.duration(bk.int),
"year")
bk.time.interval.as.year
## [1] 99.99452
```

R 的默认起始日期是 1970 年 1 月 1 日。

将日期显示为自 1970 年 1 月 1 日以来的第 44 天：^(²⁵)

```
bk.date <- as_date(44)
bk.date
## [1] "1970-02-14"
```

`Lubridate` 手册使用“源点”（origin）一词来表示起始日期/时间。我不太确定为什么使用这个词。“源点”一词源自法语 `origine`，源自拉丁语 `origo`，意为“升起”。在这种情况下，词源学并没有多大帮助。冒着被视为扫兴的守旧者的风险，我更喜欢简单的盎格鲁-撒克逊术语“开始日期和时间”。

在 2019 年 2 月 1 日的起始日期上增加 21 天：

```
bk.date <- as_date(21, origin = lubridate::ymd("2019-02-01"))
bk.date
## [1] "2019-02-22"
```

简单的字符到 R 日期的转换：

```
bk.date <- as_datetime("2019-01-02")
bk.date
## [1] "2019-01-02 UTC"
class(bk.date)
## [1] "POSIXct" "POSIXt"
```

此示例展示了分隔符的重要性。在输入年、月、日时不使用分隔符，会导致 `Lubridate` 将整个“日期”当作秒数处理。这些秒数被加到了 R 的默认起始日期（1970 年 1 月 1 日）上，从而得出错误的结果：

```
bk.date.only.number.input <- as_datetime(20190102)
bk.date.only.number.input
## [1] "1970-08-22 16:21:42 UTC"
```

### 3.12.2 协调世界时（UTC）

根据 wikipedia.org 的定义，“协调世界时（UTC）是世界调节时钟和时间的主要时间标准。它大约在 0° 经度处平均太阳时的 1 秒以内，并且不因夏令时而调整。它实际上是格林威治标准时间（GMT）的继承者。”^(²⁶)

一个良好的实践是，将任何从外部来源读取的日期立即转换为有效的 R 日期。使用 R 的 `POSIXct` 和 `POSIXlt` 类可以处理带有时区控制的日期和时间。如果你将变量保留为字符格式，那么你所做的所有事情都需要手动编码，并且存在得到错误结果的额外风险。请将 R 作为 R 来使用，而不是当作打字机。

除非另有说明，`Lubridate` 默认使用 UTC：

```
bk.date_utc <- ymd_hms("2010-08-03 00:50:50")
## [1] "2010-08-03 00:50:50 UTC"
```

现在，使用欧洲/伦敦时区计算一个日期：

```
bk.date_europe <- ymd_hms("2010-08-03 00:50:50",
tz="Europe/London")
bk.date_europe
## [1] "2010-08-03 00:50:50 BST"
```

### 3.12.3 `as_date` 与 `as.Date` 的对比

为了说明二者差异，首先使用这两个函数展示同一个日期是什么样子。在不指定时区的情况下，没有区别。

```
bk.as_date_versus_as.Date <- c(as_date(bk.date_utc),
as.Date(bk.date_utc))
bk.as_date_versus_as.Date
## [1] "2010-08-03" "2010-08-03"
```

现在，使用同一个日期，展示根据前面列出的时区 "Europe/London" 指定后，值会发生变化：

```
bk.as_date_versus_as.Date <- c(as_date(bk.date_europe), as.Date(bk.date_europe))
bk.as_date_versus_as.Date
## [1] "2010-08-03" "2010-08-02"
```

### 3.12.4 通过硬编码创建日期/时间对象

```
bk.date.and.time <- ymd_hms("2010-12-03 00:56:50")
bk.date.and.time
## [1] "2010-12-03 00:56:50 UTC"
class(bk.date.and.time)
## [1] "POSIXct" "POSIXt"
```

仅提取日期，不包含时间：

```
bk.date.only <- date(bk.date.and.time)
bk.date.only
## [1] "2010-12-03"
```

根据特定时区获取日期。`bk.data.and.time` 原本（不指定 `tz` 时）的日期是 2010 年 12 月 3 日，但指定时区 `America/New_York` 后，其值变为 2010 年 12 月 2 日：

```
bk.date.only.with.time.zone <- as.Date(bk.date.and.time,
tz = "America/New_York")
bk.date.only.with.time.zone
## [1] "2010-12-02"
```

### 3.12.5 通过单独修改月、日、年来修订日期

```
bk.date <- ymd("2018-12-31")
bk.date.revised <- update(bk.date, month = 01,
mday = 01, year = 2019)
bk.date.revised
## [1] "2019-01-01"
```

### 3.12.6 小数年份

使用 6 月 30 日作为半年的估算值。计算结果为 2017 年加上接近 0.5 的小数部分：

```
bk.date <- ymd("2017-06-30")
bk.decimal <- decimal_date (bk.date)
bk.decimal
## [1] 2017.493
bk.show.difference <- bk.decimal - 2017
bk.show.difference
## [1] 0.4931507
```

如果你从小数年份中减去 2017，会再次得到大约半年，对应于日历日期 2017 年 6 月 30 日。该日期是标准的 POSIX 格式：

```
bk.date.again <- date_decimal(bk.decimal)
bk.date.again
## [1] "2017-06-30 00:00:00 UTC"
class(bk.date.again)
## [1] "POSIXct" "POSIXt"
```

### 3.12.7 工作日

在本例中，第三天是星期二，因为一周从星期天开始：

```
bk.date <- as.Date("2019-01-15")
bk.working.day <- wday(bk.date)
bk.working.day
## [1] 3
```

### 3.12.8 当前是否处于夏令时？

该日期在时区 "America/New_York" 是否处于夏令时？答案将是逻辑值，真或假。

```
bk.date.time <- ymd("2019-03-10",tz = "America/New_York")
bk.daylight.savings.time <- dst(bk.date.time)
bk.daylight.savings.time
## [1] FALSE
```

### 3.12.9 猜测格式

针对多种日期格式的猜测格式例程——当日期时间列中各个数据元素的格式不一致时，该如何处理。猜测格式并非直观函数。其思路是将各种格式的字符型日期匹配起来，从 "Thu, 1 July 2004 22:30:00" 到 "12/31/99" 都涵盖。然后这些日期会与可能的格式进行匹配，并输出详细结果。我不想抱怨，但乍一看，它用起来似乎非常困难。看到来自 stack overflow 用户 agstudy^(²⁷) 简化的代码后，我真松了一口气：

```
bk.dates.in.different.formats <- sampleDates <-
c("4/6/2004","4/6/2004","4/6/2004","4/7/2004",
"4/6/2004","4/7/2004","2014-06-28","2014-06-30","2014-07-12",
"2014-07-29","2014-07-29","2014-08-12")
```

引入 `parse_date_time` 函数：

```
bk.simple.parsed.dates <-
parse_date_time(bk.dates.in.different.formats,
c("Ymd", "mdY"))
bk.simple.parsed.dates
##  [1] "2004-04-06 UTC" "2004-04-06 UTC" "2004-04-06 UTC" "2004-04-07 UTC"
##  [5] "2004-04-06 UTC" "2004-04-07 UTC" "2014-06-28 UTC" "2014-06-30 UTC"
##  [9] "2014-07-12 UTC" "2014-07-29 UTC" "2014-07-29 UTC" "2014-08-12 UTC"
```

显然，`parse_date_time` 函数也是智能函数之一。我们从一堆混乱的日期格式开始，最终得到了一致的 POSIX 格式向量。

日期向量中有多少个元素？

```
bk.simple.parsed.dates
length(bk.simple.parsed.dates)
## [1] 12
```

使用 `class()` 来表明该向量包含真实的日期：

```
class(bk.simple.parsed.dates)
## [1] "POSIXct" "POSIXt"
```

在这个例子中，只使用了 "Ymd" 和 "mdY" 两种格式。不过，如果你需要解析的格式种类繁多，还有很多其他格式可以尝试。

### 3.12.10 小时函数

`hour` 函数从日期时间中提取小时，并允许有针对性地修改日期时间变量中的小时：

```
bk.x <- ymd_hms(c("2009-08-07 01:01:01"))
bk.x
## [1] "2009-08-07 01:01:01 UTC"
bk.hour <- hour(bk.x)
bk.hour
## [1] 1
```

将现有日期时间变量中的小时从 1 改为 5：

```
hour(bk.x) <- 5
bk.x
## [1] "2009-08-07 05:01:01 UTC"
```

将小时改为 25，Lubridate 会做出符合逻辑的处理——将日期时间跳到第二天：

```
hour(bk.x) <- 25
bk.x
## [1] "2009-08-08 01:01:01 UTC"
```

### 3.12.11 从日期中提取名称

使用 `label` 参数获取月份的缩写名称：

```
bk.date <- ymd("2019-01-15")
month(bk.date, label = TRUE)
## [1] Jan
## 12 Levels: Jan < Feb < Mar < Apr < May < Jun < Jul < Aug < Sep < ... < Dec
```

下面是获取月份名称（而非月份数字）的另一种方法。月份名称缩写内置于 R 中，即 `month.abb`：

```
the.months = 1:12
names(the.months) = month.abb
the.months[month(bk.date)]
## Jan
##   1
```

使用 `label` 和 `abbr` 参数获取完整的月份名称：

```
month(bk.date, label = TRUE, abbr = FALSE)
## [1] January
## 12 Levels: January < February < March < April < May < June < ... < December
```

当通过索引号来处理月份时，请注意索引是从 1 开始，而不是 0：

```
month(bk.date + months(0:5), label = F)
## [1] 1 2 3 4 5 6
```

使用 `label = T` 参数时，会用到月份缩写：

```
month(bk.date + months(0:5), label = T)
## [1] Jan Feb Mar Apr May Jun
## 12 Levels: Jan < Feb < Mar < Apr < May < Jun < Jul < Aug < Sep < ... < Dec
```

### 3.12.12 解析包含小时、分钟和秒成分的周期

根据 `rdocumentation.org` 的描述，Lubridate 中的 `ms` 函数

> *将字符或数值向量转换为指定了小时数、分钟数和秒数的周期对象。`hms()` 将除“-”之外的所有非数字字符都识别为分隔符（“-”用于负数时长）。在解析了小时、分钟和秒之后，剩余输入将被忽略。*^(²⁸)

`ms` 与本章稍后展示的 `parse_date_time` 函数不同。

```
ms(c("09:10", "09:02", "1:10"))
## [1] "9M 10S" "9M 2S"  "1M 10S"
bk.ms.example <-  ms(c("09:10", "09:02", "1:10"))
bk.ms.example
## [1] "9M 10S" "9M 2S"  "1M 10S"
```

对 `bk.ms.example` 应用 `length` 函数，结果有三个元素，而非一个。

```
length(bk.ms.example)
## [1] 3
bk.ms.example
## [1] "9M 10S" "9M 2S"  "1M 10S"
```

`hm` 像 `ms` 一样，提取小时、分钟和秒的信息，但假定第一个元素是小时，第二个元素（冒号后面）是分钟：

```
bk.hm.example <-  hm(c("09:10", "09:02", "11:01"))
bk.hm.example
## [1] "9H 10M 0S" "9H 2M 0S"  "11H 1M 0S"
```

`hms` 则假定顺序为小时、分钟、秒：

```
hms("7 6 5")
## [1] "7H 6M 5S"
```

如果某个元素的值超过了常规时间类别的允许上限（例如，超过 59 分钟），那么设置 `roll = TRUE` 将强制它进行正确处理——进位到下一级别。`hms` 函数将 7 小时 65 分钟 5 秒转换为 8 小时 5 分钟 5 秒。

```
hms("7 65 5", roll = TRUE)
## [1] "8H 5M 5S"
```

## 3.13 解析日期时间：Lubridate 的主力函数

`parse_date_time()` 是 Lubridate 中的一个主力函数，它能够将多种日期字符表示形式转换为有效的 POSIXct 日期（合法的 R 语言日期格式）。

以下是一些体现 `parse_date_time()` 强大功能与灵活性的示例。

输入两个日期，第一个格式为两位数的日、两位数的月和两位数的年，第二个格式为两位数的日、全拼的月和四位数的年。“orders”参数告诉 Lubridate 哪种格式对应哪个日期。有关 Lubridate 支持的所有格式列表，请参见附录 G。

```
bk.two.dates <- parse_date_time(c("20-01-19", "20 January 2019"), orders = c("d m y", "d B Y"))
bk.two.dates
## [1] "2019-01-20 UTC" "2019-01-20 UTC"
class(bk.two.dates)
## [1] "POSIXct" "POSIXt"
```

输出结果中有多少个元素？

```
length(bk.two.dates)
## [1] 2
bk.two.dates
## [1] "2019-01-20 UTC" "2019-01-20 UTC"
```

基于日-月-年（两位数）加小时-分钟-秒进行解析：

```
bk.date <- parse_date_time("21-02-19 20-13-06",
orders = "dmy HMS")
bk.date
## [1] "2019-02-21 20:13:06 UTC"
```

请注意，日期前不需要补零：

```
bk.date <- mdy("09/5/2016")
bk.date
## [1] "2016-09-05"
```

你可以按年/月/日/小时/分钟/秒的格式输入日期，然后使用 `ymd_hms()` 将其转换为标准的 R 语言日期形式。这种格式的日期符合 ISO 标准：

```
bk.date2 <- "2015-11-7 10:11:05"
bk.date3 <- ymd_hms(bk.date2)
bk.date3
## [1] "2015-11-07 10:11:05 UTC"
```

## 3.14 日期验证

一个对象是否是有效日期？

```
bk.is.this.a.date <- is.Date(as.Date("2009-08-03"))
bk.is.this.a.date
## [1] TRUE
bk.is.this.a.date <-  is.Date(9999999)
bk.is.this.a.date
## [1] FALSE
```

### 3.14.1 计算时间差

`difftime()` 用于计算两个日期之间的天数差。作为一种度量单位，它以天数为计量时长：

```
bk.date1 <- ymd_hms("2009-06-08 01:59:59") #结束日期
bk.date2 <- ymd_hms("2008-04-29 12:00:00") #开始日期
bk.diff.in.dates <- difftime(bk.date1,bk.date2)
bk.diff.in.dates
##时间差为 404.5833 天
```

`difftime()` 实际上不是日期，而是一个天数数量：

```
bk.is.difftime <- is.Date(bk.diff.in.dates)
bk.is.difftime
## [1] FALSE
```

你可以询问 Lubridate 某个变量是否是 `difftime` 对象：

```
bk.is.a.difftime <- is.difftime(bk.diff.in.dates)
bk.is.a.difftime
## [1] TRUE
```

判断一个对象是否为 `POSIX` 格式：

```
bk.posix.query <- is.POSIXt(now())
bk.posix.query
## [1] TRUE
```

判断一个对象是否为时间跨度：

```
is.timespan(as.Date("2009-08-03"))
## [1] FALSE
is.timespan(duration(second = 1))
## [1] TRUE
```

判断一个日期是否在闰年中：

```
bk.not.leap.year <- as.Date("2019-01-01")
bk.query.leap.year <- leap_year(bk.not.leap.year)
bk.query.leap.year
## [1] FALSE
```

### 3.14.2 时区

R 使用一组标准的时区代码。通过运行 `OlsonNames()` 可以获取所有时区代码。以下脚本列出了其中的 25 个示例，当时共计 594 个时区：

```
my.time.zones <- OlsonNames()
length(my.time.zones)
## [1] 594
my.sample.of.time.zones <- sample_n(as.data.frame(my.time.zones), 25)
my.sample.of.time.zones #注意：由于是随机抽样，你的输出结果可能与我不同
##             my.time.zones
## 1                 Iceland
## 2      Australia/Canberra
## 3             Asia/Muscat
## 4     Atlantic/Cape_Verde
## 5         Europe/Brussels
## 6        Indian/Mauritius
## 7         America/Rosario
## 8          Asia/Chungking
## 9    Australia/Queensland
## 10         Pacific/Kosrae
## 11          Pacific/Chuuk
## 12          Africa/Asmera
## 13         Pacific/Midway
## 14       America/Anguilla
## 15          Europe/Moscow
## 16            Africa/Lome
## 17         Asia/Kamchatka
## 18       Antarctica/Davis
## 19     Australia/Tasmania
## 20     America/Martinique
## 21            Pacific/Yap
## 22           Asia/Jakarta
## 23 America/Bahia_Banderas
## 24        America/Toronto
## 25     Atlantic/Reykjavik
```

### 3.14.3 指定日期/时间的快捷方法

只需输入年、月、日以及可选的时间：

```
bk.easy.time1 <- make_datetime(year = 1999, month = 12,
day = 22, sec = 10)
bk.easy.time1
## [1] "1999-12-22 00:00:10 UTC"
```

这里创建两个时间差为一秒的日期时间：

```
bk.easy.time2 <- make_datetime(year = 1999, month = 12,
day = 22, sec = c(10, 11))
bk.easy.time2
## [1] "1999-12-22 00:00:10 UTC" "1999-12-22 00:00:11 UTC"
```

### 3.14.4 处理周数

计算一年中的第几周：

```
bk.x <- ymd("2012-03-26")
week(bk.x)
## [1] 13
```

将一个变量设为第 1 周，然后设为第 54 周：

```
bk.week  3
bk.week.logical.question
## [1] FALSE
```

### 3.14.5 检验区间或日期：是否在另一个区间内？

定义两个新年之间的一个区间：

```
bk.int <- interval(ymd("2001-01-01"), ymd("2002-01-01"))
bk.int
## [1] 2001-01-01 UTC--2002-01-01 UTC
```

设置一个六个月的区间：

```
bk.int2 <- interval(ymd("2001-06-01"), ymd("2002-01-01"))
bk.int2
## [1] 2001-06-01 UTC--2002-01-01 UTC
```

问：2001 年 5 月 3 日是否在 2001 年 1 月 1 日至 2002 年 1 月 1 日这个范围内？

```
bk.within.or.without <- ymd("2001-05-03") %within% bk.int
bk.within.or.without
## [1] TRUE
```

提问：一个区间是否在另一个区间内？即 2001 年 6 月 1 日至 2002 年 1 月 1 日是否在 2001 年 1 月 1 日至 2002 年 1 月 1 日之间？

```
bk.within.or.without.2 <- bk.int2 %within% bk.int
bk.within.or.without.2
## [1] TRUE
bk.within.or.without.3 <- ymd("1999-01-01") %within% bk.int  # 1999 年 1 月 1 日是否在 2001 年 1 月 1 日至 2002 年 1 月 1 日之间
bk.within.or.without.3
## [1] FALSE
bk.dates <- ymd(c("2014-12-20", "2014-12-30", "2015-01-01",
"2015-01-03"))
bk.blackouts<- c(interval(ymd("2014-12-30"), ymd("2014-12-31")),
interval(ymd("2014-12-30"), ymd("2015-01-03")))
bk.blackouts
## [1] 2014-12-30 UTC--2014-12-31 UTC 2014-12-30 UTC--2015-01-03 UTC
```

使用四个日期，询问每个日期是否在禁用日期范围内：

```
bk.are.dates.within.blackouts <-  bk.dates %within% bk.blackouts
bk.are.dates.within.blackouts
## [1] FALSE  TRUE FALSE  TRUE
```

依次测试每个日期是否在*任意*一个禁用日期范围内：

```
bk.dates <- ymd(c("2014-12-20", "2014-12-30",
"2015-01-01", "2015-01-03"))
bk.blackouts<- list(interval(ymd("2014-12-30"),
ymd("2014-12-31")), interval(ymd("2014-12-30"),
ymd("2015-01-03")))
bk.are.dates.within.blackouts <- bk.dates %within% bk.blackouts
bk.are.dates.within.blackouts
## [1] FALSE  TRUE  TRUE  TRUE
```

### 3.14.6 其他函数：创建指定的时间差

`make_difftime()` 函数提供了丰富的选项来创建两个日期时间之间的时间差。^((29))

```
make_difftime(1)
## 时间差为 1 秒
时间差为 1 秒：
bk.difftime1sec <- make_difftime(1)
bk.difftime1sec
## 时间差为 1 秒：
make_difftime(60)
## 时间差为 1 分钟：
make_difftime(3600)
## 时间差为 1 小时
make_difftime(3600, units = "minute")
## 时间差为 60 分钟
时间差为 60 分钟：
make_difftime(second = 90)
## 时间差为 1.5 分钟
```

创建一个 1.5 分钟的时间差：

```
make_difftime(minute = 1.5)
## 时间差为 1.5 分钟
```

通过指定各个计量单位来创建一个时间差：

```
make_difftime(second = 3, minute = 1.5, hour = 2, day = 6,
week = 1)
## 时间差为 13.08441 天
make_difftime(hour = 1, minute = -60)
## 时间差为 0 秒
make_difftime(day = -1)
## 时间差为 -1 天
make_difftime(120, day = -1, units = "minute")
## 时间差以分钟为单位
## [1]     2 -1440
```

如果没有指定分钟数，Lubridate 会假定为 0 分钟：

```
bk.date  3   #逻辑测试
## [1] TRUE
```

将月份显示为数字：

```
bk.date  #显示日期
## [1] "2019-01-12 00:05:00 UTC"
month(bk.date)
## [1] 1
```

### 3.14.7 强制将日期/时间设为不同的时区

在以下示例中，每个实例的字面日期/时间数值相同，但最后两个的时区发生了变化。顺便提一句，我发现通过代码更换时区相当棘手，很容易出错。对于任何涉及时区的新代码，你可能想进行抽查。你可以使用众多时区计算器中的一个：[`www.timeanddate.com/worldclock/converted.html`](http://www.timeanddate.com/worldclock/converted.html)。

从时区代码为 `"Japan"` 开始：

```
bk.date <- ymd_hms("2019-01-07 00:00:01", tz = "Japan")
bk.date
## [1] "2019-01-07 00:00:01 JST"
```

现在强制将时区设为 `"America/Chicago"`：

```
bk.date2 <- force_tz(bk.date, "America/Chicago")
bk.date2
## [1] "2019-01-07 00:00:01 CST"
```

请注意，这样的更改不会改变你看到的数字。它只是改变了与日期时间关联的隐形时区标签。

夏令时的有效性：

```
bk.date <- ymd_hms("2010-03-14 02:05:05 UTC")
bk.date
## [1] "2010-03-14 02:05:05 UTC"
```

现在，尝试将该时间回滚到在夏令时下不存在的时间：

```
bk.date.with.no.roll.to.valid.time <- force_tz(bk.date, "America/New_York", roll=FALSE)
bk.date.with.no.roll.to.valid.time
## [1] NA
```

接下来，指示 Lubridate 执行正确操作，在夏令时框架内回滚到一个合适的时间（整数）：

```
bk.date.with.roll.to.valid.time <- force_tz(bk.date,
"America/New_York", roll=TRUE)
bk.date.with.roll.to.valid.time
## [1] "2010-03-14 03:00:00 EDT"
```

### 3.14.8 在同一计算中处理不同时区

设置两个日期时间，相差约一小时：

```
two.dates <- c("2009-08-07 00:00:01", "2009-08-07 01:02:03")
bk.date <- ymd_hms(two.dates)
bk.date
## [1] "2009-08-07 00:00:01 UTC" "2009-08-07 01:02:03 UTC"
```

`UTC` 是协调世界时，是已弃用的格林威治标准时间的继任者。它是一个时间基准点，你可以说：“我把标杆插在这儿，地球上所有其他时间都相对于此计量。” 现代文明的整个运作都依赖于一个全球公认的每日起始点。

现在为组成 `bk.date` 的两个日期时间分配时区：

```
bk.assign.time.zones <- force_tzs(bk.date, tzones =
c("America/New_York", "Europe/Amsterdam"))
bk.assign.time.zones
## [1] "2009-08-07 04:00:01 UTC" "2009-08-06 23:02:03 UTC"
```

由于这些日期时间被强制指定为特定时区，`UTC` 值随之改变。

另一个例子，使用四个日期时间，每个都施加了强制时区，并指定一个输出时区，将所有日期时间以 `"America/New_York"` 时区表示。`libya` 时区涵盖利比亚的黎波里。

```
bk.date.values <- ymd_hms(c("2009-08-07 00:00:01",
"2009-08-07 01:02:03","2009-03-03 11:02:03",
"2009-04-01 05:02:03" ))
bk.assign.time.zones.with.tzone_out <-force_tzs(bk.date.values,
tzones = c("America/New_York", "Europe/Amsterdam", "Japan",
"libya"), tzone_out = "America/New_York")
bk.assign.time.zones.with.tzone_out
## [1] "2009-08-07 00:00:01 EDT" "2009-08-06 19:02:03 EDT"
## [3] "2009-03-02 21:02:03 EST" "2009-03-31 23:02:03 EDT"
```

#### 3.14.9 美国东部夏令时及其他时间示例

处理两个时区，并考虑美国东部夏令时的复杂性：

```r
bk.x <- ymd_hms(c("2009-08-07 00:00:01", "2009-08-07 01:02:03"))
bk.y <- force_tzs(bk.x, tzones = c("America/New_York",
"Europe/Amsterdam"))
bk.y
## [1] "2009-08-07 04:00:01 UTC" "2009-08-06 23:02:03 UTC"
```

在 `bk.x` 中，两个时间日期是硬编码的，默认情况下在内部表示为 UTC 日期。在 `bk.y` 中，最初为 UTC 的两个时间点 `point1` 和 `point2`，被转换为它们在纽约和阿姆斯特丹所对应的时间，前提是这些时间也以 UTC 术语表示。

注意 `point1` 如何从纽约时间午夜后 1 分钟变为大约凌晨 4 点。这是因为美国东部夏令时 = UTC – 4。

设置时区：

```r
bk.x <- ymd("2012-03-26")
tz(bk.x)
## [1] "UTC"
tz(bk.x) <- "GMT"
bk.x
## [1] "2012-03-26 GMT"
tz(bk.x) <- "America/New_York"
bk.x
## [1] "2012-03-26 EDT"
```

在此例中，纽约的午夜 12 点被转换为格林威治标准时间凌晨 4 点：

```r
bk.x <- ymd_hms("2009-08-07 00:00:01", tz = "America/New_York")
bk.with.time.zone <- with_tz(bk.x, "GMT")
bk.with.time.zone
## [1] "2009-08-07 04:00:01 GMT"
```

另一个时区更改，导致日期变化：

```r
bk.x.singapore <- ymd_hms("2009-08-07 00:00:01",
tz = "Singapore")
bk.x.singapore
## [1] "2009-08-07 00:00:01 +08"
bk.with.time.zone <- with_tz(bk.x.singapore, "GMT")
bk.with.time.zone
## [1] "2009-08-06 16:00:01 GMT"
```

年份修改：

```r
bk.date <- ymd("1995-01-01")
bk.year.1995 <- year(bk.date) == 1995
bk.year.1995
## [1] TRUE
```

#### 3.14.10 国际化

根据 Stack Overflow 上的一篇文章^(³⁰)

> _ 在计算领域，区域设置（locale）是一组定义用户语言、国家及任何特殊变体偏好的参数，用户希望在其用户界面中看到这些偏好。通常，区域设置标识符至少包含一个语言标识符和一个区域标识符。_

要查看系统认为你位于何处，请输入以下内容：

```r
Sys.getlocale("LC_TIME") #你的输出可能略有不同
## [1] "English_United States.1252"
```

区域设置按 ISO 标准指定。请注意，区域设置并非完美无缺。我曾尝试使用区域设置 `"ro_RO.utf8"`（罗马尼亚），结果收到消息：`"Error in Sys.setlocale("LC_TIME", locale):(converted from warning) OS reports request to set locale to "ro_RO.utf8" cannot be honored."`

因此，像任何有诸多事务在身的作者一样，我继续前行，在德国选择了一个新的区域设置：

```r
bk.date1 <-  "Ma 2012 august 14 11:28:30 "
bk.german.date <- ymd_hms(bk.date1, locale = "German")
bk.german.date
## [1] "2012-08-14 11:28:30 UTC"
```

**注意**
在 Mac 上，你的结果可能与 Windows 版本的 R 不同。

接下来，我使用自己的区域设置值来显示一个美国德克萨斯州的日期：

```r
bk.date.new <-  "Ma 2012 august 14 11:28:30 "
bk.texas.date <- ymd_hms(bk.date.new,
locale = "English_United States.1252")
bk.texas.date
## [1] "2012-08-14 11:28:30 UTC"
```

##### 3.14.10.1 有没有什么好的区域设置信息？

即使在 stackoverflow.com 上与专家讨论过这个问题后，仍然不总能清楚在哪里可以找到好的区域设置名称，至少对于 Windows 操作系统而言。Richie Cotton 建议了以下网站获取区域设置名称：[`https://docs.microsoft.com/en-us/windows/desktop/Intl/language-identifier-constants-and-strings`](https://docs.microsoft.com/en-us/windows/desktop/Intl/language-identifier-constants-and-strings)。 我使用这个微软网站获取白俄罗斯的区域设置。该网站显示 `"LANG_LANG_BELARUSIAN"` 作为区域设置名称。这没有生效。然而，我尝试了没有 `"LANG_"` 前缀的 `"BELARUSIAN"`，代码运行正常。

```r
bk.date1 <-  "Ma 2012 august 14 11:28:30 "
ymd_hms(bk.date1, locale = "LANG_LANG_BELARUSIAN")
##Error in Sys.setlocale("LC_TIME", locale) :
##  (converted from warning) OS reports request to set locale to ##"LANG_LANG_BELARUSIAN" cannot be honored
##In addition: Warning message:
##In Sys.setlocale("LC_TIME", locale) :
##  OS reports request to set locale to "LANG_LANG_BELARUSIAN" ##cannot be honored
ymd_hms(bk.date1,
locale = "BELARUSIAN") # 通过猜测修改
[1] "2012-08-14 11:28:30 UTC"
```

#### 3.14.11 `now()`、回滚与舍入

##### 3.14.11.1 当前时间

基于系统设置的当前日期和时间：

```r
bk.current.date.time <- now()
bk.current.date.time
## [1] "2021-01-12 17:11:12 CST"
```

带时区的 `now()`：

```r
bk.current.date.time <- now(tzone = "Pacific/Midway")
bk.current.date.time
## [1] "2021-01-12 12:11:12 SST"
```

我不确定你会多久用一次，但如果你想查看 R 启动其内部时间所使用的日期，只需键入 `origin`：

```r
origin
## [1] "1970-01-01 UTC"
```

如果只需要当前日期，省略小时和分钟：

```r
bk.today <- today()
bk.today
## [1] "2021-01-12"
bk.today.greenwich.time <- today("GMT")
bk.today.greenwich.time
## [1] "2021-01-12"
bk.today.have.not.had.dinner.on.mars.yet <-
as.Date("2035-01-01")
bk.true.or.false <- today() <
bk.today.have.not.had.dinner.on.mars.yet
bk.true.or.false
## [1] TRUE
```

##### 3.14.11.2 回滚

`rollback()` 将日期更改为上个月的最后一天或该月的第一天：

```r
bk.date <- ymd("2019-05-20")
bk.rolled.back <- rollback(bk.date)
bk.rolled.back
## [1] "2019-04-30"
bk.dates <- bk.date + months(0:2)
bk.dates
## [1] "2019-05-20" "2019-06-20" "2019-07-20"
bk.multiple.dates <- rollback(bk.dates)
bk.multiple.dates
## [1] "2019-04-30" "2019-05-31" "2019-06-30"
bk.date <- ymd_hms("2010-03-03 12:44:22")
bk.date.rolled.back <- rollback(bk.date)
bk.date.rolled.back
## [1] "2010-02-28 12:44:22 UTC"
```

在此语法中，Lubridate 将日期回滚到该月的第一天：

```r
bk.roll.to.first <- rollback(bk.date, roll_to_first = TRUE)
bk.roll.to.first
## [1] "2010-03-01 12:44:22 UTC"
```

忽略小时、分钟和秒：

```r
bk.roll.back.drop.hours.minutes.seconds <- rollback(bk.date, preserve_hms = FALSE)
bk.roll.back.drop.hours.minutes.seconds
## [1] "2010-02-28 UTC"
bk.roll.back.no.keep.hours.minutes.seconds <- rollback(bk.date,
roll_to_first = TRUE, preserve_hms = FALSE)
bk.roll.back.no.keep.hours.minutes.seconds
## [1] "2010-03-01 UTC"
```

### 3.14.11.3 舍入

舍入函数很直观。它包括向下舍入、向上舍入、传统四舍五入、周起始、时间单位和同步到下一个边界。以下示例来自 `Lubridate` 手册，并附有一些注释^((31))：

几乎但不完全是凌晨 2 点：

```
date <- as.POSIXct("2009-03-08 01:59:59") # 夏令时边界
```

打印小数秒：

```
options(digits.secs=6)
x <- ymd_hms("2009-08-03 12:01:59.23")
round_date(x, ".5s")
## [1] "2009-08-03 12:01:59 UTC"
round_date(x, "sec")
## [1] "2009-08-03 12:01:59 UTC"
round_date(x, "second")
## [1] "2009-08-03 12:01:59 UTC"
round_date(x, "minute")
## [1] "2009-08-03 12:02:00 UTC"
round_date(x, "5 mins")
## [1] "2009-08-03 12:00:00 UTC"
round_date(x, "hour")
## [1] "2009-08-03 12:00:00 UTC"
round_date(x, "2 hours")
## [1] "2009-08-03 12:00:00 UTC"
round_date(x, "day")
## [1] "2009-08-04 UTC"
round_date(x, "week")
## [1] "2009-08-02 UTC"
round_date(x, "month")
## [1] "2009-08-01 UTC"
round_date(x, "bimonth")
## [1] "2009-09-01 UTC"
round_date(x, "quarter") == round_date(x, "3 months")
## [1] TRUE
round_date(x, "halfyear")
## [1] "2009-07-01 UTC"
round_date(x, "year")
## [1] "2010-01-01 UTC"
x <- ymd_hms("2009-08-03 12:01:59.23")
floor_date(x, ".1s")
## [1] "2009-08-03 12:01:59.2 UTC"
floor_date(x, "second")
## [1] "2009-08-03 12:01:59 UTC"
floor_date(x, "minute")
## [1] "2009-08-03 12:01:00 UTC"
floor_date(x, "hour")
## [1] "2009-08-03 12:00:00 UTC"
floor_date(x, "day")
## [1] "2009-08-03 UTC"
floor_date(x, "week")
## [1] "2009-08-02 UTC"
floor_date(x, "month")
## [1] "2009-08-01 UTC"
floor_date(x, "bimonth")
## [1] "2009-07-01 UTC"
floor_date(x, "quarter")
## [1] "2009-07-01 UTC"
floor_date(x, "season")
## [1] "2009-06-01 UTC"
floor_date(x, "halfyear")
## [1] "2009-07-01 UTC"
floor_date(x, "year")
## [1] "2009-01-01 UTC"
x <- ymd_hms("2009-08-03 12:01:59.23")
ceiling_date(x, ".1 sec")
## [1] "2009-08-03 12:01:59.2 UTC"
ceiling_date(x, "second")
## [1] "2009-08-03 12:02:00 UTC"
ceiling_date(x, "minute")
## [1] "2009-08-03 12:02:00 UTC"
ceiling_date(x, "5 mins")
## [1] "2009-08-03 12:05:00 UTC"
ceiling_date(x, "hour")
## [1] "2009-08-03 13:00:00 UTC"
ceiling_date(x, "day")
## [1] "2009-08-04 UTC"
ceiling_date(x, "week")
## [1] "2009-08-09 UTC"
ceiling_date(x, "month")
## [1] "2009-09-01 UTC"
ceiling_date(x, "bimonth") == ceiling_date(x, "2 months")
## [1] TRUE
ceiling_date(x, "quarter")
## [1] "2009-10-01 UTC"
ceiling_date(x, "season")
## [1] "2009-09-01 UTC"
ceiling_date(x, "halfyear")
## [1] "2010-01-01 UTC"
ceiling_date(x, "year")
## [1] "2010-01-01 UTC"
x <- ymd("2000-01-01")
ceiling_date(x, "month")
## [1] "2000-02-01"
ceiling_date(x, "month", change_on_boundary = TRUE)
## [1] "2000-02-01"
```

秒的处理方式与其他时间度量类似。请注意，秒与分钟或小时等更高级别的时间度量不同，是不变的。换句话说，闰年中的一秒与非闰年中的一秒持续时间相同。

```
bk.date <- ymd("2012-03-26")
second(bk.date)
## [1] 0
```

将秒数设置为 1：

```
second(bk.date) <- 1
bk.date
## [1] "2012-03-26 00:00:01 UTC"
```

如果将日期时间设置为 61 秒，则其值将变为 1 秒：

```
second(bk.date)  2
## [1] FALSE
## ** 截断的日期时间 **
bk.x <- c("2011-12-31 12:59:59", "2010-01-01 12:11",
"2010-01-01 12", "2010-01-01")
bk.x.truncated <- ymd_hms(bk.x, truncated = 3)
bk.x.truncated
## [1] "2011-12-31 12:59:59 UTC" "2010-01-01 12:11:00 UTC"
## [3] "2010-01-01 12:00:00 UTC" "2010-01-01 00:00:00 UTC"
bk.x <- c("2011-12-31 12:59", "2010-01-01 12",
"2010-01-01")
```

在此语法中，请注意输出中缺少秒：

```
bk.x.truncated.2 <- ymd_hm(bk.x, truncated = 2)
bk.x.truncated.2
## [1] "2011-12-31 12:59:00 UTC" "2010-01-01 12:00:00 UTC"
## [3] "2010-01-01 00:00:00 UTC"
```

### 3.14.11.4 自动回滚运算

由于每个月的天数不同，`Lubridate` 无法使用简单的数学计算来处理，例如，计算从现在起一个月后的日期。例如，如果有人说“加一个月到 1 月 31 日”，通常他想到的是 2 月 28 日，而不是 3 月 3 日。`Lubridate` 进行加或减运算时，会确保最终结果不超过新计算月份的最后一天。

以下代码不会给你预期的结果：

```
bk.jan <- ymd_hms("2010-01-31 03:04:05")
bk.three.months.out <- bk.jan + months(1:3)
bk.three.months.out
## [1] NA                        "2010-03-31 03:04:05 UTC"
## [3] NA
```

现在使用我们的闪亮新玩具 `%m+%`。没有回滚。`Lubridate` 在向前推进三个月时，会创建三个与真实值和预期值相对应的月时间值。

> *问：为什么小比利-鲍勃不能在家尝试正则表达式？*
> *答：他妈妈不让他玩火柴。*

```
bk.jan.do.the.right.thing <- bk.jan %m+% months(1:3)
bk.jan.do.the.right.thing
## [1] "2010-02-28 03:04:05 UTC" "2010-03-31 03:04:05 UTC"
## [3] "2010-04-30 03:04:05 UTC"
leap <- ymd("2012-02-29")
leap %m+% years(1)  #跳到二月的最后一天
## [1] "2013-02-28"
leap %m+% years(-1) #同一日期，前一年
## [1] "2011-02-28"
leap %m-% years(1)  #减号表示回退一年
## [1] "2011-02-28"
```

脚注 1 2 3 4 5 6 7 8 9 10 11 12 13