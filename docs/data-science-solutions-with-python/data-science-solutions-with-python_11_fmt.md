# 10. 使用 H2O 自动化机器学习流程

本章简短而富有洞察力，通过解释如何借助一个广泛使用的机器学习框架（即 H2O）来自动化机器学习流程，为本书画上句号。

## 探索自动化机器学习

在一个充斥着无数机器学习框架的世界里，你可能会认为组织会轻易地采用机器学习。不幸的是，事实并非如此。由于机器学习算法相当复杂，导致技能人才严重短缺。

为了应对这些挑战，H2O 引入了 `AutoML`，它试图通过同时训练和精心挑选性能卓越的方法来自动化机器学习过程。

尽管 H2O AutoML 功能强大，但它并不包含聚类分析、生存分析和时间序列分析等方法。

本章使用了第 3 章的数据。同时，它还将领先的 AutoML 方法的性能与第 3 章中执行的方法进行了对比。

## 预处理特征

本章直接操作第 3 章中获取的数据，因此不再详细描述预处理任务。清单 10-1 执行了所有预处理任务。

```python
import pandas as pd
df = pd.read_csv(r"filepath\WA_Fn-UseC_-Marketing_Customer_Value_Analysis.csv")
drop_column_names = df.columns[[0, 6]]
initial_data = df.drop(drop_column_names, axis="columns")
initial_data.iloc[::, 0] = pd.get_dummies(initial_data.iloc[::, 0])
initial_data.iloc[::, 2] = pd.get_dummies(initial_data.iloc[::, 2])
initial_data.iloc[::, 3] = pd.get_dummies(initial_data.iloc[::, 3])
initial_data.iloc[::, 4] = pd.get_dummies(initial_data.iloc[::, 4])
initial_data.iloc[::, 5] = pd.get_dummies(initial_data.iloc[::, 5])
initial_data.iloc[::, 6] = pd.get_dummies(initial_data.iloc[::, 6])
initial_data.iloc[::, 7] = pd.get_dummies(initial_data.iloc[::, 7])
initial_data.iloc[::, 8] = pd.get_dummies(initial_data.iloc[::, 8])
initial_data.iloc[::, 9] = pd.get_dummies(initial_data.iloc[::, 9])
initial_data.iloc[::, 15] = pd.get_dummies(initial_data.iloc[::, 15])
initial_data.iloc[::, 16] = pd.get_dummies(initial_data.iloc[::, 16])
initial_data.iloc[::, 17] = pd.get_dummies(initial_data.iloc[::, 17])
initial_data.iloc[::, 18] = pd.get_dummies(initial_data.iloc[::, 18])
initial_data.iloc[::, 20] = pd.get_dummies(initial_data.iloc[::, 20])
initial_data.iloc[::, 21] = pd.get_dummies(initial_data.iloc[::, 21])
```

清单 10-1 预处理特征

### H2O AutoML 实战

本节使用 `H2OAutoML()` 方法来训练多种方法并对其性能进行排名。本节将执行并评估 `H2OAutoML`。

清单 10-2 准备了 H2O 框架。

```python
import h2o as initialize_h2o
initialize_h2o.init()
```

清单 10-2 准备 H2O 框架

清单 10-3 将 `pandas` 数据框转换为 H2O 数据框。

```python
h2o_data = initialize_h2o.H2OFrame(initial_data)
```

清单 10-3 将 Pandas 数据框转换为 H2O 数据框

清单 10-4 概述了自变量和因变量特征。

```python
int_x = initial_data.iloc[::,0:19]
fin_x = initial_data.iloc[::,19:21]
x_combined = pd.concat([int_x, fin_x], axis=1)
x_list = list(x_combined.columns)
y_list = initial_data.columns[19]
y = y_list
x = h2o_data.col_names
x.remove(y_list)
```

清单 10-4 概述特征

清单 10-5 随机划分数据。

`H2OAutoML()` 方法允许我们指定训练每种方法所需的大量时间。通过指定 `max_runtime_secs` 来实现。

```python
h2o_training_data, h2o_validation_data, h2o_test_data = h2o_data.split_frame(ratios=[.8,.1])
```

清单 10-5 随机划分数据框

清单 10-6 执行 H2O 的自动机器学习方法。

```python
from h2o.automl import H2OAutoML
h2o_automatic_ml = H2OAutoML(max_runtime_secs = 240)
h2o_automatic_ml.train(x= x, y= y, training_frame = h2o_training_data, validation_frame = h2o_validation_data)
```

清单 10-6 执行 H2O 的自动机器学习

清单 10-7 按升序对 H2O 的方法进行排名（见表 10-1）。

表 10-1 H2O 方法排名

| Model_ID | Mean_residual_deviance | Rmse | Mse | Mae | Rmsle |
| --- | --- | --- | --- | --- | --- |
| `StackedEnsemble_AllModels_AutoML_20210824_115721` | 18371.3 | 135.541 | 18371.3 | 86.6257 | 0.482043 |
| `StackedEnsemble_BestOfFamily_AutoML_20210824_115721` | 18566.8 | 136.26 | 18566.8 | 87.7959 | 0.477828 |
| `GBM_1_AutoML_20210824_115721` | 19392.8 | 139.258 | 19392.8 | 90.0652 | 0.490441 |
| `GBM_3_AutoML_20210824_115721` | 19397.8 | 139.276 | 19397.8 | 90.3467 | 0.490855 |
| `GBM_2_AutoML_20210824_115721` | 19467.3 | 139.525 | 19467.3 | 89.9908 | 0.491887 |
| `GBM_grid__1_AutoML_20210824_115721_model_4` | 19607.9 | 140.028 | 19607.9 | 91.3311 | 0.495784 |
| `GBM_4_AutoML_20210824_115721` | 19633.4 | 140.119 | 19633.4 | 89.9553 | 0.499693 |
| `XRT_1_AutoML_20210824_115721` | 19717.6 | 140.42 | 19717.6 | 89.7474 | 0.485144 |
| `GBM_grid__1_AutoML_20210824_115721_model_2` | 19805.8 | 140.733 | 19805.8 | 91.7092 | 0.494005 |
| `GBM_grid__1_AutoML_20210824_115721_model_9` | 19858.6 | 140.921 | 19858.6 | 90.0839 | 0.521174 |

```python
h2o_method_ranking = h2o_automatic_ml.leaderboard
print(h2o_method_ranking)
```

清单 10-7 对 H2O 的方法进行排名

表 10-1 表明，`StackedEnsemble_AllModels_AutoML_20210824_115721` 是排名最高的方法，其平均残差偏差最小（为 18371.3）。均方根误差为 135.541。

相比之下，排名最低的方法是 `GBM_grid__1_AutoML_20210824_115721_model_9`，其平均残差偏差为 19858.6，均方根误差为 140.921。清单 10-8 评估了排名最高的方法（`StackedEnsemble_AllModels_AutoML_20210824_115721`）。

```
highest_ranking_method = h2o_automatic_ml.leader
print(highest_ranking_method)
ModelMetricsRegressionGLM: stackedensemble
** Reported on train data. **
MSE: 4900.131929292816
RMSE: 70.00094234574857
MAE: 45.791336342731775
RMSLE: 0.3107457400431741
R²: 0.9421217261180531
Mean Residual Deviance: 4900.131929292816
Null degrees of freedom: 7289
Residual degrees of freedom: 7277
Null deviance: 617106545.1167994
Residual deviance: 35717061.632615335
AIC: 82648.0458245655
ModelMetricsRegressionGLM: stackedensemble
** Reported on validation data. **
MSE: 17967.946605287365
RMSE: 134.04456947331872
MAE: 83.66742154264892
RMSLE: 0.43883929226519075
R²: 0.7718029372846945
Mean Residual Deviance: 17967.946605287365
Null degrees of freedom: 931
Residual degrees of freedom: 919
Null deviance: 73564698.35450743
Residual deviance: 16728158.289522538
AIC: 11790.460469478105
ModelMetricsRegressionGLM: stackedensemble
** Reported on cross-validation data. **
MSE: 18371.339394332357
RMSE: 135.5409140973026
MAE: 86.62574587341507
RMSLE: 0.4820432203973515
R²: 0.7830055540572105
Mean Residual Deviance: 18371.339394332357
Null degrees of freedom: 7289
Residual degrees of freedom: 7276
Null deviance: 617269595.4214188
Residual deviance: 133908692.84528854
AIC: 92282.67565857261
```

清单 10-8 执行 H2O 自动机器学习

结果表明，`StackedEnsemble_AllModels_AutoML_20210824_115721` 方法解释了测试数据中 77% 的相关变化，以及验证数据中 78% 的相关变化。

## 结论

本章中执行的普通最小二乘法，其表现远超排名最高的 H2O AutoML 方法（`StackedEnsemble_AllModels_AutoML_20210824_115721`）。H2O 方法揭示了测试数据中 77% 的相关变化。相比之下，第 3 章中执行的所有方法（使用 `Scikit-Learn` 和 `PySpark` 框架）均解释了测试数据中 100% 的相关变化。遗憾的是，`StackedEnsemble_AllModels_AutoML_20210824_115721` 方法没有汇总函数。本书至此结束。

## 索引

- A
    - 加速失效时间方法
    - Apache Spark
    - AutoML 方法
        - H2O
    - 预处理任务
- B
    - 大数据
    - 业务
    - 客户关系
    - 决策
    - ETL
    - 特征
    - 产品开发
    - 仓储
- C
    - 类别
    - 聚类中心
    - 聚类方法
    - Cox 比例风险方法
- D
    - 数据驱动型组织
    - 决策树
    - 深度信念网络
    - 深度学习 (DL)
    - 降维器
    - DL 框架，Keras
- E
    - 肘部曲线
    - 集成方法
- F
    - `findspark` 框架
- G
    - 高斯分布
    - 梯度提升方法
    - H2O 框架
    - `PySpark`
    - XGBoost
    - GraphX
- H, I, J
    - H2OAutoML
    - H2O 框架
    - H2O 逻辑回归方法
    - Hadoop 分布式文件系统 (HDF)
    - Hadoop 文件系统 (HDFS)
- K
    - Keras
    - `k-means` 方法
        - H2O 框架
        - PySpark 框架
- L
    - Lifeline
    - Cox 比例风险方法
    - 线性回归方法
    - 逻辑回归方法
- M
    - 机器学习 (ML)
        - H2O
        - Scikit-Learn 框架
        - XGBoost
    - Mantel-Haenszel 方法
    - Microsoft CSV 文件
    - 多类分类方法
    - 多层感知器神经网络
- N
    - 神经网络
- O
    - 普通最小二乘法
- P, Q
    - 预处理特征
    - 预处理任务
    - 主成分方法
        - H2O 框架
        - PySpark 框架
    - 尺度
    - `Scikit-Learn`
    - PySpark 框架
    - PySpark 逻辑回归方法
    - PySpark 主成分方法
- R
    - 残差
    - 弹性分布式数据集 (RDDs)
- S
    - Scikit-Learn 决策树方法
    - Scikit-Learn 框架
    - Scikit-Learn 逻辑回归方法
    - Scikit-Learn 多层感知器神经网络
    - 深度信念网络
        - H2O
        - Keras
    - Spark 配置
    - Spark 框架
    - Spark MLlib
    - Spark 流处理
    - 监督学习
    - 生存分析方法
- T
    - TensorFlow
- U
    - 无监督学习
- V, W
    - `VectorAssembler()` 方法
- X, Y, Z
    - XGBoost