# 极差

对于数值类型，通过将最大值减去最小值来确定值的范围。

在 R 中，对纬度列使用以下命令：

```R
sapply(hist_latitude_with_id[,'Latitude'], range, na.rm=TRUE)
```

纬度的范围是 -54.2767 到 78.2167。

在 R 中，对经度列使用以下命令：

```R
sapply(hist_longitude_with_id[,'Longitude'], range, na.rm=TRUE)
```

经度的范围是 -176.5000 到 179.2167。

在 R 中，对`country`列使用以下命令：

```R
sapply(hist_country_with_id[,'Country'], range, na.rm=TRUE)
Country Range is: "AD" to "ZW"
```

