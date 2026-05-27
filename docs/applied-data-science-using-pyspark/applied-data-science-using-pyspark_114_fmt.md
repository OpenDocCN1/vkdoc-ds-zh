# 计算货币价值
```
monetary_df = df.groupBy("CustomerID").agg(sum("Amount").alias("Monetary"))
```