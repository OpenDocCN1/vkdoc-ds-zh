# 使用分位数定义细分条件

## 按照Recency分配R_Segment
```python
rfm_df = rfm_df.withColumn("R_Segment",
    when(col("Recency") <= recency_quantiles[2], "R1")
    .when(col("Recency") <= recency_quantiles[1], "R2")
    .when(col("Recency") <= recency_quantiles[0], "R3")
    .otherwise("R4"))
```

## 按照Frequency分配F_Segment
```python
rfm_df = rfm_df.withColumn("F_Segment",
    when(col("Frequency") > frequency_quantiles[2], "F1")
    .when(col("Frequency") > frequency_quantiles[1], "F2")
    .when(col("Frequency") > frequency_quantiles[0], "F3")
    .otherwise("F4"))
```

## 按照Monetary分配M_Segment
```python
rfm_df = rfm_df.withColumn("M_Segment",
    when(col("Monetary") > monetary_quantiles[2], "M1")
    .when(col("Monetary") > monetary_quantiles[1], "M2")
    .when(col("Monetary") > monetary_quantiles[0], "M3")
    .otherwise("M4"))
```

## 拼接RFM_Segment
```python
rfm_df = rfm_df.withColumn("RFM_Segment", concat(col("R_Segment"), col("F_Segment"), col("M_Segment")))
```