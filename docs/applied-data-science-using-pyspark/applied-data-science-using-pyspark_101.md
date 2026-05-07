# 设置混洗分区数
sparkSession.conf.set("spark.sql.shuffle.partitions",50)
```

一些用于混洗操作的其他设置值得探索：

```
