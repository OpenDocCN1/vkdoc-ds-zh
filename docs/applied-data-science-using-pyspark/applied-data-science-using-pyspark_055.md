# Casting variables
int_vars=['userId','movieId']
for column in int_vars:
df=df.withColumn(column,df[column].cast(IntegerType()))
float_vars=['rating']
for column in float_vars:
df=df.withColumn(column,df[column].cast(FloatType()))
(training, test) = df.randomSplit([0.8, 0.2])
```

在这段代码中，我们使用 `ALS` 执行矩阵分解。PySpark 中 `ALS` 模型的参数如下：

*   `NumBlocks` 是用户和项目被划分成的块数，以便并行计算。
*   `rank` 是模型中潜在特征的数量。
*   `maxIter` 是运行的最大迭代次数。
*   `regParam` 指定 `ALS` 中的正则化参数，通过惩罚较大的系数来防止过拟合。
*   `implicitPrefs` 指定是使用显式反馈 `ALS` 变体还是适用于隐式反馈数据的变体（默认为 `false`，表示使用显式反馈）。
*   `alpha` 是一个适用于 `ALS` 隐式反馈变体的参数，它控制偏好观测中的基线置信度（默认为 `1.0`）。

我们将 `coldStartStrategy` 设置为 `drop`，以消除空的评估指标。

```
