# 计算最近一次消费
recency_df = df.groupBy("CustomerID").agg(datediff(max("CurrentDate"), max("TransactionDate")).alias("Recency"))

