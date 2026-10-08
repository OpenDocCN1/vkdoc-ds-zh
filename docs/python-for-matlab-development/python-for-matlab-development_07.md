# 5. 日期和时间

时间数据在计算工作中频繁出现。来自传感器的测量记录、经济结果、股票价格、时间步进数值模拟等，如果没有伴随的时间值，都是无用的。在本章中，我们将介绍 Python 的`datetime`模块，它提供了与 MATLAB 同名数据类型类似的功能。

## 5.1 时间

Python 的`time`模块具有返回当前时间（本地时区或 UTC）、测量 CPU 时间或经过的时间，以及休眠所需时间的功能。

### 5.1.1 当前时间

Python 的`time.time()`函数返回与 MATLAB 中`posixtime(datetime())`相同的值，即当前时间的双精度表示，以 Unix 纪元秒为单位，自 1970 年 1 月 1 日午夜起持续递增的秒数（忽略闰秒），精度为微秒。除此之外，它在 Python 中还用于计算代码段的运行时间。

`time.strftime()`函数根据所需格式返回当前时间：

| MATLAB: | Python: |
| --- | --- |
| `>> format long e` `>> posixtime(datetime())` `1.572210959845713e+09` `>> t = datetime;` `>> datestr(t,'yyyy-mm-dd HH:MM:ss')` `'2019-10-27 14:25:26'` | `In : import time` `In : time.time()` `Out: 1572210959.845713` `In : time.strftime('%Y-%m-%d %H:%M:%S')` `Out: '2019-10-27 14:25:26'` |

### 5.1.2 时间字符串格式

`strftime()` 的格式参数包含 1989 年 C 标准库中同名函数所接受的所有参数，以及一些额外参数。在某些情况下，输出结果取决于计算机的区域设置。这可以通过在 Linux 和 macOS 的终端中执行命令 `locale`，或在 Windows 的命令提示符中执行命令 `systeminfo`（查看输出中的“系统区域设置”）来揭示。表 5-1 中的示例使用了来自 Python 项目文档的 `en_US` 和 `de_DE` 区域设置。

如果支持该格式，则给出 `datestr()` 函数对应的 MATLAB 等效格式字符串。

**表 5-1** `strftime()` 和 `strptime()` 格式代码

| Python 指令 | 含义 | 示例 | `datestr()` 中的 MATLAB 等效项 |
| --- | --- | --- | --- |
| `%a` | 缩写的工作日名称 | Sun, Mon (en_US) So, Mo (de_DE) | `ddd` |
| `%A` | 完整的工作日名称 | Sunday (en_US) Sonntag (de_DE) | `dddd` |
| `%w` | 表示星期几的整数 | Python: 0 (周日) … 6 MATLAB: 1 (周日) … 7 | `e` |
| `%d` | 两位数的月份中的日期 | 01, …, 31 | `dd` |
| `%b` | 缩写的月份名称 | Jan, Dec (en_US) Jan, Dez (de_DE) | `MMM` |
| `%B` | 完整的月份名称 | January (en_US) Januar (de_DE) | `MMMM` |
| `%m` | 两位数的月份 | 01, …, 12 | `MM` |
| `%y` | 两位数的年份 | 99, 00, …, 19 | `yy` |
| `%Y` | 四位数的年份 | 1999, 2000, …, 2019 | `yyyy` 或 `u` |
| `%H` | 两位数的小时，0–23 | 00, …, 23 | `HH` |
| `%I` | 两位数的小时，0–11 | 00, …, 11 | `HH PM` (输出包含 `PM`) |
| `%p` | AM 或 PM | AM, PM (en_US) am, pm (de_DE) | `AM` 或 `PM` |
| `%M` | 两位数的分钟 | 00, …, 59 | `mm` |
| `%S` | 两位数的秒 | 00, …, 59 | `ss` |
| `%f` | 六位数的微秒 | 000000, …, 999999 | `S .. SSSSSSSSS` (十分之一秒到纳秒) |
| `%z` | UTC 偏移 ±HHMM[SS[.ffffff]] | +0000, –0900015 | `Z` |
| `%j` | 三位数的一年中的第几天 | 001, …, 366 | `DDD` |
| `%U` | 两位数的年份中的第几周（星期日为一周开始） | 00, …, 53 | 不可用 |
| `%W` | 两位数的年份中的第几周（星期一为一周开始） | 00, …, 53 | 不可用 |
| `%c` | 区域设置特定的日期和时间 | Sun Oct 27 16:57:47 2019 (en US) | `datetime()` 默认 |
| `%x` | 区域设置特定的日期 | 10/27/19 (en_US) 27.10.2019 (de_DE) | 不可用 |
| `%X` | 区域设置特定的时间 | 17:00:53 (en_US) 17:00:53 (de_DE) | 不可用 |
| `%%` | 字面量 % | % | % |

### 5.1.3 tic, toc；%timeit

MATLAB 的 `tic` 和 `toc` 计算嵌套它们之间的命令所消耗的 CPU 时间。Python 中的 `time.time()` 可以做同样的事情，但需要更多输入：

| MATLAB: | Python: |
| --- | --- |
| `>> N = 1000;` `>> tic` `>> a = eig(rand(N))` `>> toc` `Elapsed time: 1.57437 seconds.` | `In :` `import time; import numpy as np` `In : N = 1000` `In : tic = time.time()` `In : a = np.linalg.eig(np.random.rand(N,N))` `In : time.time() - tic` `Out: 1.2564113140106201` |

在 Python 程序中，使用 `time.time()` 的差值很有用，但在 ipython 中进行交互式操作时，使用其“魔法”命令 `%timeit` 来测量命令执行所需时间会更方便：

Python:

```
In : %timeit np.linalg.eig(np.random.rand(1000,1000))
```

1.2 s ± 18.5 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)

# 5.2 日期

`%timeit` 会多次运行该命令（上述示例中为七次）并返回所有运行的平均时间。

Python 有三个用于处理日期的标准模块，各自具有不同的功能：

- `datetime` 是适用范围最广的模块，也是本节讨论的重点。它有许多函数可以简化日期计算和执行转换。
- `date` 可以说是 `datetime` 的一个子集，在仅处理日期而不涉及一天内的时间时非常有用。
- `calendar` 具有确定年份是否为闰年、给定日期是星期几以及一个月中天数的函数。

## 5.2.1 `datetime` 对象与字符串的相互转换

`datetime` 模块与 `time` 模块一样，使用 `strftime()` 函数以所需格式打印日期：

| MATLAB: | Python: |
| --- | --- |
| `>> now = datetime()` `28-Oct-2019 21:16:36` `>> datetime(now,'Format',...` `'u-MM-dd hh:mm')` `2019-10-28 21:16` | `In :` `from datetime import` `datetime` `In : datetime.now()` `Out: datetime(2019, 10, 28, 21,` `16, 36, 225635)` `In : datetime.now().strftime(` `'%Y-%m-%d %H:%M')` `Out: '2019-10-28 21:16'` |

`datetime` 和 `time` 都有一个同名的函数 `strptime()`，它执行相反的操作：接收一个包含日期的字符串和另一个描述格式的字符串，然后返回一个 Python 变量，该变量随后可用于执行日期计算。

方便的是，`strptime()` 使用与 `strftime()` 相同的格式指令（表 5-1）从字符串中解析日期，而 `strftime()` 则使用这些指令以所需格式打印日期。从字符串“Aug 12 04:05:51 2006”创建 `datetime` 对象的方法如下：

| MATLAB: | Python: |
| --- | --- |
| `>> S ='Aug 12 04:05:51 2006';` `>> dt=datetime(S,'InputFormat',...` `'MMM dd HH:mm:SS yyyy')` `dt =` `datetime` `12-Aug-2006 04:05:00` | `In :` `from datetime import` `datetime` `In : S = 'Aug 12 04:05:51 2006'` `In : dt = datetime.strptime(S,` `...:       '%b %d %H:%M:%S %Y')` `In : dt` `Out: datetime.datetime(` `2006, 8, 12, 4, 5, 51)` |

## 5.2.2 时间增量

`datetime` 模块函数 `timedelta()` 创建的时间偏移量，其工作方式类似于 MATLAB 中的 `duration` 对象。此示例打印一周的火星太阳日（持续时间为 24 小时 39 分 35.244 秒），从 1986 年 6 月 28 日中午开始：

| MATLAB: | Python: |
| --- | --- |
| `Mars_day = duration(...` `24,39,35.244)` `Start = datetime(...` `1986,6,28,12,0,0)` `for` `i = 0:6` `Start + i*Mars_day` `end` `28-Jun-1986 12:00:00` `29-Jun-1986 12:39:35` `30-Jun-1986 13:19:10` `01-Jul-1986 13:58:45` `02-Jul-1986 14:38:20` `03-Jul-1986 15:17:56` `04-Jul-1986 15:57:31` | `from datetime import` `datetime, timedelta` `Mars_day = timedelta(hours=24,` `minutes=39, seconds=35.244)` `Start = datetime(1986,6,28,12)` `for` `i` `in` `range(7):` `print(Start + i*Mars_day)` `1986-06-28 12:00:00` `1986-06-29 12:39:35.244000` `1986-06-30 13:19:10.488000` `1986-07-01 13:58:45.732000` `1986-07-02 14:38:20.976000` `1986-07-03 15:17:56.220000` `1986-07-04 15:57:31.464000` |

`timedelta` 对象有一个方法 `.total_seconds()`，它返回该增量所表示的秒数。两个 `datetime` 对象的差是一个 `timedelta`，因此我们可以轻松确定两个日期之间经过了多少时间：

| MATLAB: | Python: |
| --- | --- |
| `Jul4 = '1976-07-04 12:00';` `Aug1 = '1976-08-01 18:00';` `J=datetime(Jul4,'InputFormat',...` `'yyyy-MM-dd HH:mm')` `A=datetime(Aug1,'InputFormat',...` `'yyyy-MM-dd HH:mm')` `duration(A-J,'Format','s')` `2.4408e+06 sec` | `from datetime import` `datetime as DT` `Jul4 = '1976-07-04 12:00'` `Aug1 = '1976-08-01 18:00'` `J = DT.strptime(Jul4, '%Y-%m-%d %H:%M')` `A = DT.strptime(Aug1, '%Y-%m-%d %H:%M')` `(A - J).total_seconds()` `Out: 2440800.0` |

MATLAB 和 Python 还允许计算时间增量的比率。此示例打印一个地球年中的火星天数：

| MATLAB: | Python: |
| --- | --- |
| `>> Mars_day = duration(...` `24,39,35.244);` `>> duration(years(1))/Mars_day` `355.47` | `In : Mars_day = timedelta(hours=24,` `...:     minutes=39, seconds=35.244)` `In : timedelta(days=365.24)/Mars_day` `Out: 355.4677472922519` |

# 5.3 时区

默认情况下，Python 和 MATLAB 中的 `Datetime` 对象都不包含时区信息；它们仅反映主机的日期和时间。

通过在调用 `datetime()`、`datetime.now()` 或 `datetime.fromtimestamp()` 函数时填充可选的 `tzinfo` 或 `tz` 关键字，可以使 Python `datetime` 对象包含时区信息。可以创建 `tzinfo` 对象的两个 Python 模块是 `pytz` 和 `dateutil.tz`。（Python 3.9 引入了 `zoneinfo` 模块，在第 5.5 节中讨论。本书使用的 MATLAB 2020b 版本发布于 Python 3.9 之前，因此不支持该模块。）尽管 `pytz` 功能更多，但来自 `dateutil.tz` 的时区在处理边缘情况（例如计算跨越夏令时/标准时间转换的时间差）时能产生更准确的结果 [1]。

在本节中，我们使用 `pytz` 的唯一目的是列出所有已知的时区字符串——类似于 MATLAB 的 `timezones` 命令——这是 `dateutil.tz` 所不能做到的：

| MATLAB: | Python: |
| --- | --- |
| `>> timezones`![](img/516169_1_En_5_Figa_HTML.jpg) | `In :` `import pytz` `In : pytz.all_timezones` `Out:` `['Africa/Abidjan',` `'Africa/Accra',` `'Africa/Addis_Ababa',` `'Africa/Algiers',` `...` `'US/Pacific',` `'US/Samoa',` `'UTC',` `'Universal',` `'W-SU',` `'WET',` `'Zulu']` |

MATLAB 表中的时区名称与 `pytz.all_timezones` 返回的名称相匹配。

有了前文列表中获取的有效时区名称，我们就可以创建一个包含时区信息的 `datetime` 对象。请注意，`datetime` 的输出会标识操作系统从中获取时区信息的文件（此示例中为 `'/usr/share/zoneinfo/America/Los_Angeles'`）：

| MATLAB: | Python: |
| --- | --- |
| `>> T = datetime(2020,2,14,...` `13,30,25,'TimeZone',...` `'America/Los_Angeles')` `T =` `14-Feb-2020 13:30:25` `>> T.TimeZone` `'America/Los_Angeles'` `>> datetime(T,'Format',...` `'yyyy-MM-dd''T'...` `'HH:mm:ss''.''SSSSSS')` `2020-02-14T13:30:25.000000` `>> datetime(T,'Format','Z')` `-0800` | `In :` `from datetime import` `datetime` `In :` `from dateutil import` `tz` `In : LA_tz = tz.gettz('America/Los_Angeles')` `In : T = datetime(2020,2,14,13,30,25,` `...:     tzinfo=LA_tz)` `In : T` `Out: datetime.datetime(2020,2,14,13,30,25,` `tzinfo=tzfile(` `'/usr/share/zoneinfo/America/Los_Angeles'))` `In : T.isoformat()` `Out: '2020-02-14T13:30:25-08:00'` `In : T.utcoffset()` `Out: datetime.timedelta(days=-1,seconds=57600)` |

将包含时区信息的 `datetime` 对象显示为任何其他所需时区很容易：

| MATLAB: | Python: |
| --- | --- |
| `>> datetime(T,'TimeZone',...` `'Asia/Tokyo')` `15-Feb-2020 06:30:25` | `In : Tokyo_tz = tz.gettz('Asia/Tokyo')` `In : T.astimezone(Tokyo_tz)` `Out: datetime.datetime(2020,2,15,6,30,25,` `tzinfo=tzfile(` `'/usr/share/zoneinfo/Asia/Tokyo'))` |

## 5.3.1 协调世界时与本地时间

协调世界时（UTC），也称为祖鲁时间或格林威治标准时间，是记录覆盖广阔地理区域的事件的首选时区。MATLAB 和 Python 有几个显式处理协调世界时而非单个计算机本地时间的函数。

矛盾的是，返回协调世界时的 `datetime` 函数，即 `datetime.utcnow()` 和 `datetime.utcfromtimestamp()`，并没有设置 `tzinfo`，因此它们返回的是不含时区信息的对象。换句话说，日、时、分等的数值是正确的，但底层没有元数据表明这是协调世界时时区。如果后续日期计算的计算精度很重要，那么正确方式是在函数名中不包含 `utc`，并显式地传入 `tz=dateutil.tz.UTC` [2]：

Python:

```

```
import time
from datetime import datetime
from dateutil import tz

datetime.now()  # 本地时间（洛杉矶）
```
```
datetime.datetime(2019, 11, 9, 18, 20, 57, 118448)
```

# 从当前时间创建协调世界时——但不关联时区！

```
In : datetime.utcnow()
Out: datetime.datetime(2019, 11, 10, 2, 20, 57, 118448)
```

# 从当前时间创建协调世界时的更好方法

在交互式环境中查看示例：

```python
In : datetime.now(tz=tz.UTC)

Out: datetime.datetime(2019, 11, 10, 2, 20, 57, 118448, tzinfo=tzutc())
```

```python
In : epoch_sec = time.time()

In : epoch_sec

Out: 1573352547.984918
```

```python
In : datetime.fromtimestamp(epoch_sec)

Out: datetime.datetime(2019, 11, 9, 18, 22, 27, 984918)
```

# 从纪元秒创建协调世界时的错误方法

输入：`datetime.utcfromtimestamp(epoch_sec)`

输出：`datetime.datetime(2019, 11, 10, 2, 22, 27, 984918)`

# 从纪元秒创建协调世界时的正确方法

`In : datetime.fromtimestamp(epoch_sec, tz=tz.UTC)`

`Out: datetime.datetime(2019, 11, 10, 2, 22, 27, 984918, tzinfo=tzutc())`

MATLAB 中的类似转换如下所示：

MATLAB:
```
>> a = datetime('now')  % 计算机处于洛杉矶时区

21-Nov-2023 11:28:52

>> a = datetime('now','TimeZone','America/Los_Angeles')

21-Nov-2023 11:28:52

>> a = datetime('now','TimeZone','UTC')

21-Nov-2023 18:28:52

>> epoch_sec = convertTo(a,'posixtime')

1.700591332042366e+09

>> datetime(epoch_sec,'convertfrom','posixtime')

21-Nov-2023 18:28:52
```

## 5.4 与 `datetime` 对象之间的时间转换

第 5.2.1 节展示了如何将 `datetime` 对象转换为字符串，以及如何将字符串解析为 `datetime` 对象。在本节中，我们将使用本地时间和协调世界时探讨与 `datetime` 对象之间额外的格式转换。示例使用了以下导入语句：

Python:
```
In : from datetime import datetime

In : import pytz

In : import time
```

### 5.4.1 Unix 纪元秒

Unix 纪元秒与时区无关，因此从包含时区的 `datetime` 对象进行转换可能会造成误导：

Python:
```
In : epoch_sec = time.time()

In : epoch_sec

Out: 1572630202.1339452

In : dtZ = datetime.datetime.utcfromtimestamp( epoch_sec )

In : dtL = datetime.datetime.fromtimestamp(    epoch_sec )

In : dtZ

Out: datetime.datetime(2019, 11, 1, 17, 43, 22, 133945)

In : dtL

Out: datetime.datetime(2019, 11, 1, 10, 43, 22, 133945)

In : recovered_epoch_secZ = dtZ.timestamp()

In : recovered_epoch_secL = dtL.timestamp()

In : recovered_epoch_secZ

Out: 1572655402.133945

In : recovered_epoch_secL

Out: 1572630202.133945
```

为了保持转换的一致性，Unix 纪元值必须转换回最初测量时的时区。

### 5.4.2 ISO 8601 时间字符串

符合 ISO 8601 标准的时间字符串可以有以下形式之一：

*   `2019-11-01T15:54:04`

*   `2019-11-01T15:54:04Z`

*   `2019-11-01T15:54:04+00:00`

尽管 `datetime` 模块包含一个 `.fromisoformat()` 函数，但奇怪的是，该函数不接受末尾的 "Z"（代表 "Zulu"，也称为协调世界时）；必须省略时区指示符，或者将其指定为数字偏移量。

此外，由于带有时区偏移量的 ISO 格式函数会显式设置时区，因此需要使用 `.astimezone()` 方法从包含时区信息的 `datetime` 对象显式计算本地时间变量：

Python:
```
In : dtZ = datetime.fromisoformat('2019-11-01T15:54:04+00:00')

In : TZ_LA = pytz.timezone("America/Los_Angeles")

In : dtL = dtZ.astimezone(TZ_LA)

In : dtZ

Out: datetime.datetime(2019, 11, 1, 15, 54, 4,
      tzinfo=datetime.timezone.utc)

In : dtL

Out: datetime.datetime(2019, 11, 1, 8, 54, 4,
      tzinfo=)

In : recovered_isoZ = dtZ.isoformat()

In : recovered_isoZ

Out: '2019-11-01T15:54:04+00:00'

In : recovered_isoL = dtL.isoformat()

In : recovered_isoL

Out: '2019-11-01T08:54:04-07:00'
```

### 5.4.3 儒略日；简化儒略日；GPS 时间

Anaconda 附带了一个名为 `astropy` 的模块，该模块拥有强大的与天文学相关的时间转换功能。这些功能包括儒略日、简化儒略日、FITS、UT1、TAI 和 GPS 参照系。

以下示例使用了美国/太平洋时区：

Python:
```
In : from astropy.time import Time

In : dt = datetime.fromisoformat('2019-11-03T05:29:04-07:00')

In : dt

Out: datetime.datetime(2019, 11, 3, 5, 29, 4,
      tzinfo=datetime.timezone(
        datetime.timedelta(days=-1, seconds=61200)))
```

# 从 datetime 转换为 MJD、JD、GPS：

```python
In : astropy_t = Time( dt )

In : astropy_t

Out: 

In : astropy_t.mjd

Out: 58790.52018518518

In : astropy_t.jd

Out: 2458791.020185185

In : astropy_t.gps

Out: 1256819362.0
```

# 从 MJD、JD、GPS 转换为 datetime

```
In : Time( 58790.52018518518, format='mjd').datetime

Out: datetime.datetime(2019, 11, 3, 12, 29, 4)

In : Time( 2458791.020185185 , format='jd').datetime

Out: datetime.datetime(2019, 11, 3, 12, 29, 3, 999995)

In : Time( 1256819362.0, format='gps').datetime

Out: datetime.datetime(2019, 11, 3, 12, 29, 41)
```

## 5.5 Python >= 3.9 中的 `zoneinfo`

`Python` 3.9（`MATLAB` 2020b 不支持该版本，参见第 2.7 节）引入了 `zoneinfo` 模块以改进时区支持。`zoneinfo` 的主要优势在于它对跨越夏令时转换的时间计算进行了严格处理。此外，与 `pytz` 类似，`zoneinfo` 包含一个列出所有已知时区名称的机制，因此无需仅仅为了访问 `pytz.all_timezones` 这个时区列表而导入 `pytz`。

### 5.5.1 列出可用的时区

`Python` 3.9：

```
In : import zoneinfo

In : zoneinfo.available_timezones()

Out:
{'Africa/Abidjan',
'Africa/Accra',
'Africa/Addis_Ababa',
'Africa/Algiers',
'Africa/Asmara',
'Africa/Asmera',
[...]
'UTC',
'Universal',
'W-SU',
'WET',
'Zulu',
'build/etc/localtime'}
```

### 5.5.2 跨越夏令时转换的日期递增

此示例演示了使用时区数据库中洛杉矶条目，正确处理标准时间到夏令时的转换：

`Python` 3.9：

```
import zoneinfo
from datetime import datetime, timedelta

LA_tz = zoneinfo.ZoneInfo("America/Los_Angeles")
Start = datetime(2022, 3, 9, 10, tzinfo=LA_tz)

for i in range(7):
    day = Start + timedelta(days=i)
    print(day, day.tzname())

2022-03-09 10:00:00-08:00 PST

2022-03-10 10:00:00-08:00 PST

2022-03-11 10:00:00-08:00 PST

2022-03-12 10:00:00-08:00 PST

2022-03-13 10:00:00-07:00 PDT

2022-03-14 10:00:00-07:00 PDT

2022-03-15 10:00:00-07:00 PDT
```



