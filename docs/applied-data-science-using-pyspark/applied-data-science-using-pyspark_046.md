# 将 RDD 转换为 DataFrame
df_txts = spark.createDataFrame(tokens, ['list_of_words','index'])
