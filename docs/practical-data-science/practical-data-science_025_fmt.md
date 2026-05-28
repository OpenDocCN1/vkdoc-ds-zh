# 中位数

确定将特定列的数据集分成两半的值。

在 R 中，对纬度列使用以下命令：

```R
sapply(hist_latitude_with_id[,'Latitude'], median, na.rm=TRUE)
```

结果是`43.2126`。

在 R 中，对经度列使用以下命令：

```R
sapply(hist_longitude_with_id[,'Longitude'], median, na.rm=TRUE)
```

结果是`43.2126`。