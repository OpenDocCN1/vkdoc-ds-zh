# 从 MJD、JD、GPS 转换为 datetime：
In : Time( 58790.52018518518, format='mjd').datetime
Out: datetime.datetime(2019, 11, 3, 12, 29, 4)
In : Time( 2458791.020185185 , format='jd').datetime
Out: datetime.datetime(2019, 11, 3, 12, 29, 3, 999995)
In : Time( 1256819362.0, format='gps').datetime
Out: datetime.datetime(2019, 11, 3, 12, 29, 41)
```

### 5.5 Python >= 3.9 中的 `zoneinfo`

Python 3.9（MATLAB 2020b 不支持该版本，参见第 2.​7 节）引入了 `zoneinfo` 模块以改进时区支持。`zoneinfo` 的主要优势在于它对跨越夏令时转换的时间计算进行了严格处理。此外，与 `pytz` 类似，`zoneinfo` 包含一个列出所有已知时区名称的机制，因此无需仅仅为了访问 `pytz.all_timezones` 这个时区列表而导入 `pytz`。

#### 5.5.1 列出可用的时区

Python 3.9：

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

#### 5.5.2 跨越夏令时转换的日期递增

此示例演示了使用时区数据库中洛杉矶条目，正确处理标准时间到夏令时的转换：

Python 3.9：

```
import zoneinfo
from datetime import datetime, timedelta
LA_tz = zoneinfo.ZoneInfo("America/Los_Angeles")
Start = datetime(2022, 3, 9, 10, tzinfo=LA_tz)
for i in range(7):
day = Start + timedelta(days=i)
print(day, day.tzname())
