# 这将其转换为相同时间点但表示为 GMT
tz = "Europe/London"
)
## [1] "1975-02-15 13:00:00 GMT"
```

哥本哈根比伦敦早一个小时，因此当我们将 14:00 从哥本哈根转换到 GMT 时，得到的小时数是 13:00。



## 时间间隔

如果你有两个时间点，那么你就有了一个时间间隔：这两个时间点之间的时间。你可以使用 `interval()` 函数从两个时间点创建一个间隔对象。

```
start <- dmy("02 11 1949")
end <- dmy("15 02 1975")
interval(start, end)
## [1] 1949-11-02 UTC--1975-02-15 UTC
```

中缀运算符 `%--%` 也能实现同样的功能。

```
start %--% end
## [1] 1949-11-02 UTC--1975-02-15 UTC
```

你可以使用 `int_start()` 和 `int_end()` 获取间隔的起点和终点。

```
int <- interval(start, end)
int
## [1] 1949-11-02 UTC--1975-02-15 UTC
int_start(int)
## [1] "1949-11-02 UTC"
int_end(int)
## [1] "1975-02-15 UTC"
```

起点不必在终点之前。你可以定义一个起点在终点之后的间隔。

```
end %--% start
## [1] 1975-02-15 UTC--1949-11-02 UTC
int_start(start %--% end)
## [1] "1949-11-02 UTC"
int_start(end %--% start)
## [1] "1975-02-15 UTC"
```

你可以使用 `int_flip()` 翻转一个间隔。

```
int_flip(end %--% start)
## [1] 1949-11-02 UTC--1975-02-15 UTC
```

函数 `int_standardize()` 会将起点在终点之后的间隔翻转过来，否则保持原样。

```
int_standardize(start %--% end)
## [1] 1949-11-02 UTC--1975-02-15 UTC
int_standardize(end %--% start)
## [1] 1949-11-02 UTC--1975-02-15 UTC
```

`int_length()` 会以秒为单位给出间隔的长度。

```
x <- now()
int <- interval(x, x + minutes(1)) # 从现在起向前一分钟
int_length(int) # 长度为一分钟，即 60 秒
## [1] 60
int <- interval(x, x + minutes(20)) # 从现在起向前 20 分钟
int_length(int) / 60 # 除以 60 得到以分钟为单位的长度
## [1] 20
```

你可以使用 `%within%` 运算符检查某个时间点是否在一个间隔内。

```
ymd("1867 05 02") %within% int
## [1] FALSE
ymd("1959 04 23") %within% int
## [1] FALSE
x %within% int # 起点在间隔内部
## [1] TRUE
(x + minutes(20)) %within% int # 终点在间隔内部
## [1] TRUE
```

你可以通过给 `int_start()` 或 `int_end()` 赋值来更新间隔的起点和终点：

```
int_start(int) <- dmy("19 Aug 1950")
int
## [1] 1950-08-19 01:00:00 CET--2022-08-23 11:50:29 CEST
int_end(int) <- dmy("19 Sep 1950")
int
## [1] 1950-08-19 01:00:00 CET--1950-09-19 01:00:00 CET
```

你可以将整个间隔移动一个固定的量。例如，你可以使用 `int_shift()` 将间隔向前移动一个月：

```
int_shift(int, months(1))
## [1] 1950-09-19 01:00:00 CET--1950-10-19 01:00:00 CET
```

给定两个间隔，`int_overlaps()` 函数检查它们是否重叠。

```
int1 <- interval(dmy("19 Aug 1950"), dmy("19 Sep 1950"))
int2 <- interval(dmy("19 oct 1950"), dmy("25 nov 1951"))
int3 <- interval(dmy("19 oct 1948"), dmy("25 aug 1951"))
int4 <- interval(dmy("19 oct 1981"), dmy("25 aug 2051"))
