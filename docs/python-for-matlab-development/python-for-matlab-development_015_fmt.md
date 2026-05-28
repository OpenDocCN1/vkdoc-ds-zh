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