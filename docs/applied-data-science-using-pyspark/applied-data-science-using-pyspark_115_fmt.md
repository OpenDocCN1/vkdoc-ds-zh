# 合并最近一次消费、频率和货币价值

```
rfm_df = recency_df.join(frequency_df, "CustomerID").join(monetary_df, "CustomerID")
```