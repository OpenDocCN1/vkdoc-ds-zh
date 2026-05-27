# 添加一个包含当前日期的列

```
df = df.withColumn("CurrentDate", lit(current_date).cast(DateType()))
```