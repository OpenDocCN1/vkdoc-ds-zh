# 如果选项未设置为 1，则加载住房数据集

```python
else:
    #filename = "melb_data.csv"
    target_variable_name = 'type'
    file_location = '/FileStore/tables/melb_data.csv'
    file_type = 'csv'
    # CSV 选项
    infer_schema = 'true'
    first_row_is_header = 'true'
    delimiter = ','
    # 以下选项适用于 CSV 文件。对于其他文件类型，这些选项将被忽略。
    df = spark.read.format(file_type) \
        .option('inferSchema', infer_schema) \
        .option('header', first_row_is_header) \
        .option('sep', delimiter) \
        .load(file_location)
    df = df.withColumn(target_variable_name, F.when(df[target_variable_name] == 'h', 0).otherwise(1))
df.show()
```