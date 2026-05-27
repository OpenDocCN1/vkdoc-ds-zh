# 如果选项设置为 1，则加载银行数据集
if `dataset` == 1:
    `target_variable_name` = 'y'
    # 文件位置和类型
    `file_location` = '/FileStore/tables/bank_full.csv'
    `file_type` = 'csv'
    # CSV 选项
    `infer_schema` = 'true'
    `first_row_is_header` = 'true'
    `delimiter` = ';'
    # 以下选项适用于 CSV 文件。对于其他文件类型，这些选项将被忽略。
    `df` = `spark.read.format(file_type)` \
        .option('inferSchema', `infer_schema`) \
        .option('header', `first_row_is_header`) \
        .option('sep', `delimiter`) \
        .load(`file_location`)
    `df` = `df.withColumn(target_variable_name, F.when(df[target_variable_name] == 'no', 0).otherwise(1))`

```
# 如果选项设置为 1，则加载银行数据集
if dataset == 1:
    target_variable_name = 'y'
    # 文件位置和类型
    file_location = '/FileStore/tables/bank_full.csv'
    file_type = 'csv'
    # CSV 选项
    infer_schema = 'true'
    first_row_is_header = 'true'
    delimiter = ';'
    # 以下选项适用于 CSV 文件。对于其他文件类型，这些选项将被忽略。
    df = spark.read.format(file_type) \
        .option('inferSchema', infer_schema) \
        .option('header', first_row_is_header) \
        .option('sep', delimiter) \
        .load(file_location)
    df = df.withColumn(target_variable_name, F.when(df[target_variable_name] == 'no', 0).otherwise(1))
```