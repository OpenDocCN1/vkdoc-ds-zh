# 计算动态分位数

```
quantiles = [0.25, 0.50, 0.75]
recency_quantiles = rfm_df.approxQuantile("Recency", quantiles, 0.0)
frequency_quantiles = rfm_df.approxQuantile("Frequency", quantiles, 0.0)
monetary_quantiles = rfm_df.approxQuantile("Monetary", quantiles, 0.0)
```