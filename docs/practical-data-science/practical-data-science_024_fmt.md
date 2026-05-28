# 平均值

如果列是数值类型，确定特定列中的平均值。

让我们检查纬度。在 R 中，使用：
```R
sapply(hist_latitude_with_id[,'Latitude'], mean, na.rm=TRUE)
```
结果是 `38.23803`。

让我们检查经度。在 R 中，使用：
```R
sapply(hist_longitude_with_id[,'Longitude'], mean, na.rm=TRUE)
```
结果是 `-13.87398`。