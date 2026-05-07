# 计算频率
frequency_df = df.groupBy("CustomerID").agg(count("TransactionDate").alias("Frequency"))

