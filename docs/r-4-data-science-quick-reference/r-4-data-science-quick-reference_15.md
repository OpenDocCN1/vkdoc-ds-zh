# 11. 处理日期：lubridate

`lubridate` 包是处理日期所必需的，并且与 Tidyverse 配合良好。但是，在导入 `tidyverse` 包时并不会自动加载它，因此你需要显式地加载它。

```r
library(lubridate)
```

## 时间点

你可以使用 `ymd()` 函数的各种变体来创建日期以及包含一天中时间的日期时间。字母 `y`、`m` 和 `d` 分别代表年、月和日。使用 `ymd()` 时，你应该将数据写成年份在前、月份其次、日期在最后的格式。该函数在解析日期方面非常灵活。

```r
ymd("1975 Feb 15")
# [1] "1975-02-15"

ymd("19750215")
# [1] "1975-02-15"

ymd("1975/2/15")
# [1] "1975-02-15"

ymd("1975-02-15")
# [1] "1975-02-15"
```

如果年、月、日的顺序不同，你可以对字母 `y`、`m` 和 `d` 进行排列组合。每种排列都对应一个解析器，它会按照指定的顺序解释输入。

```r
dmy("150275")
# [1] "1975-02-15"

mdy("February 15th 1975")
# [1] "1975-02-15"
```

如果你想在日期中添加一天中的时间，可以通过使用 `ymd()` 函数的 `_h()`、`_hm()` 和 `_hms()` 变体来分别添加小时、小时和分钟，或者小时、分钟和秒。

```r
dmy_h("15/2/1975 2pm")
# [1] "1975-02-15 14:00:00 UTC"

dmy_hm("15/2/1975 14:30")
# [1] "1975-02-15 14:30:00 UTC"

dmy_hms("15/2/1975 14:30:10")
# [1] "1975-02-15 14:30:10 UTC"
```

如果你有一个时间对象：

```r
x <- dmy_hms("15/2/1975 14:30:10")
```

那么你可以通过专门的函数提取其组成部分：

```r
c(day(x), month(x), year(x))
# [1]   15    2 1975

c(hour(x), minute(x), second(x))
# [1] 14 30 10

c(week(x), # 一年中的第几周
  wday(x), # 一周中的第几天
  yday(x)) # 一年中的第几天
# [1] 7 7 46
```

这些函数有对应的赋值函数，你可以使用它们来修改时间点的组成部分。

```r
minute(x) <- 15
wday(x) <- 42
x
# [1] "1975-03-22 14:15:10 UTC"
```

## 时区

当你添加一天中的时间时，时区也是必需的。毕竟，在知道我们所在的时区之前，我们无法知道一个给定的小时对应的是什么时间。如果我告诉你我打算两点钟给你打电话，你不能想当然地认为那是你所在时区的两点钟。^(¹⁹) 除非你另外指定，否则这些函数会默认采用 UTC 时区。你可以通过 `tz` 参数指定其他时区。

```r
dmy_hm(
  "15/2/1975 14:00",
  tz = "Europe/Copenhagen"
)
# [1] "1975-02-15 14:00:00 CET"
```

你可以通过两种不同的方式，将一个时区的时间点转换到另一个时区。一种方式是设置时区，并假定时日和时间原本就在这个时区。也就是说，你只改变对象的时区属性，而不改动时间信息本身。你可以使用 `force_tz()` 函数来完成此操作。

```r
force_tz(
```

# 这是一个 CET 时区的日期/时间

```
dmy_hm("15/2/1975 14:00", tz = "Europe/Copenhagen")
```

# 它将被转换到 GMT 时区

```r
tz = "Europe/London"

)

## [1] "1975-02-15 14:00:00 GMT"
```

尽管 CET 和 GMT 是不同的时区，`force_tz()` 函数仍保持小时数为 14:00，并仅更新时区信息。

更常见的情况是，你想知道一个时区的时间点在另一个时区是什么时间。例如，如果我答应在丹麦时间两点钟给你打电话，而你在英国，你可以使用 `with_tz()` 将时间从我的时区转换到你的时区。

```r
with_tz(
```


# 这是一个 CET 时区的日期/时间

`dmy_hm("15/2/1975 14:00", tz = "Europe/Copenhagen")`,


# 这将其转换为相同时间点但表示为 GMT

```r
tz = "Europe/London"

```

## [1] "1975-02-15 13:00:00 GMT"

哥本哈根比伦敦早一个小时，因此当我们将 14:00 从哥本哈根转换到 GMT 时，得到的小时数是 13:00。

## 时间间隔

如果你有两个时间点，那么你就有了一个时间间隔：这两个时间点之间的时间。你可以使用 `interval()` 函数从两个时间点创建一个间隔对象。

```r
start <- dmy("02 11 1949")
end <- dmy("15 02 1975")
interval(start, end)

```

## [1] 1949-11-02 UTC--1975-02-15 UTC

中缀运算符 `%--%` 也能实现同样的功能。

```r
start %--% end

```

## [1] 1949-11-02 UTC--1975-02-15 UTC

你可以使用 `int_start()` 和 `int_end()` 获取间隔的起点和终点。

```r
int <- interval(start, end)
int

```

## [1] 1949-11-02 UTC--1975-02-15 UTC

```r
int_start(int)

```

## [1] "1949-11-02 UTC"

```r
int_end(int)

```

## [1] "1975-02-15 UTC"

起点不必在终点之前。你可以定义一个起点在终点之后的间隔。

```r
end %--% start

```

## [1] 1975-02-15 UTC--1949-11-02 UTC

```r
int_start(start %--% end)

```

## [1] "1949-11-02 UTC"

```r
int_start(end %--% start)

```

## [1] "1975-02-15 UTC"

你可以使用 `int_flip()` 翻转一个间隔。

```r
int_flip(end %--% start)

```

## [1] 1949-11-02 UTC--1975-02-15 UTC

函数 `int_standardize()` 会将起点在终点之后的间隔翻转过来，否则保持原样。

```r
int_standardize(start %--% end)

```

## [1] 1949-11-02 UTC--1975-02-15 UTC

```r
int_standardize(end %--% start)

```

## [1] 1949-11-02 UTC--1975-02-15 UTC

`int_length()` 会以秒为单位给出间隔的长度。

```r
x <- now()
int <- interval(x, x + minutes(1)) # 从现在起向前一分钟
int_length(int) # 长度为一分钟，即 60 秒

```

## [1] 60

```r
int <- interval(x, x + minutes(20)) # 从现在起向前 20 分钟
int_length(int) / 60 # 除以 60 得到以分钟为单位的长度

```

## [1] 20

你可以使用 `%within%` 运算符检查某个时间点是否在一个间隔内。

```r
ymd("1867 05 02") %within% int

```

## [1] FALSE

```r
ymd("1959 04 23") %within% int

```

## [1] FALSE

```r
x %within% int # 起点在间隔内部

```

## [1] TRUE

```r
(x + minutes(20)) %within% int # 终点在间隔内部

```

## [1] TRUE

你可以通过给 `int_start()` 或 `int_end()` 赋值来更新间隔的起点和终点：

```r
int_start(int) <- dmy("19 Aug 1950")
int

```

## [1] 1950-08-19 01:00:00 CET--2022-08-23 11:50:29 CEST

```r
int_end(int) <- dmy("19 Sep 1950")
int

```

## [1] 1950-08-19 01:00:00 CET--1950-09-19 01:00:00 CET

你可以将整个间隔移动一个固定的量。例如，你可以使用 `int_shift()` 将间隔向前移动一个月：

```r
int_shift(int, months(1))

```

## [1] 1950-09-19 01:00:00 CET--1950-10-19 01:00:00 CET

给定两个间隔，`int_overlaps()` 函数检查它们是否重叠。

```r
int1 <- interval(dmy("19 Aug 1950"), dmy("19 Sep 1950"))
int2 <- interval(dmy("19 oct 1950"), dmy("25 nov 1951"))
int3 <- interval(dmy("19 oct 1948"), dmy("25 aug 1951"))
int4 <- interval(dmy("19 oct 1981"), dmy("25 aug 2051"))
```

# `int1` 在 `int2` 之前结束

`int_overlaps(int1, int2)`

## [1] FALSE

# int3 在 int1 之前开始，但两者有重叠

`int_overlaps(int1, int3)`

## [1] TRUE

# 没有重叠，int4 相比 int1 位于遥远的未来

`int_overlaps(int1, int4)`

## [1] FALSE

函数 `int_aligns()` 检查四个起点/终点中是否有任何两个相等。也就是说，第一个间隔的起点或终点必须等于第二个间隔中的至少一个点。我们之前创建的四个间隔没有共享的间隔端点。

```r
c(
  int_aligns(int1, int2),
  int_aligns(int1, int3),
  int_aligns(int1, int4)
)
```

## [1] FALSE FALSE FALSE

我们可以创建共享端点的间隔并测试 `int_aligns()`：

```r
int5 <- interval(int_start(int1), int_end(int1) + years(3))
int6 <- int_shift(int5, -years(3))
int7 <- int_shift(int6, -years(3))
c(
  int_aligns(int1, int5), # 共享起点
  int_aligns(int1, int6), # 共享终点
  int_aligns(int1, int7)  # 重叠但不共享端点
)
```

## [1] TRUE TRUE FALSE
