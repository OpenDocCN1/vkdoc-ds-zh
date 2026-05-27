## 将各个列组合成一个列 - 'features'

```python
def assemble_vectors(df, features_list, target_variable_name):
    stages = []
    # 组合向量
    assembler = VectorAssembler(inputCols=features_list, outputCol='features')
    stages = [assembler]
    # 选择所有列 + 目标列 + 新创建的 'features' 列
    selectedCols = [target_variable_name, 'features']
    # 使用管道按顺序处理
    pipeline = Pipeline(stages=stages)
    # 组合器模型
    assembleModel = pipeline.fit(df)
    # 对数据应用组合器模型
    df = assembleModel.transform(df).select(selectedCols)
    return df
```