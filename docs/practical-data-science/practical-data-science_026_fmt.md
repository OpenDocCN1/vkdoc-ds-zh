# 众数
确定特定列中出现频率最高的值。
在R中，对`country`列使用以下命令：
```R
IP_DATA_COUNTRY_FREQ=data.table(with(IP_DATA_ALL_with_ID, table(Country)))
setorder(IP_DATA_COUNTRY_FREQ,-N)
IP_DATA_COUNTRY_FREQ[1,'Country']
```
结果是`US`。
在R中，对纬度列使用以下命令：
```R
IP_DATA_Latitude_FREQ=data.table(with(IP_DATA_ALL_with_ID, table(Latitude)))
setorder(IP_DATA_Latitude_FREQ,-N)
IP_DATA_Latitude_FREQ[1,'Latitude']
```
结果是`35.6427`。
在R中，对经度列使用以下命令：
```R
IP_DATA_Longitude_FREQ=data.table(with(IP_DATA_ALL_with_ID, table(Longitude)))
setorder(IP_DATA_Longitude_FREQ,-N)
IP_DATA_Longitude_FREQ[1,'Longitude']
```
结果是`139.7677`。