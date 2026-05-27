# 导入库

```python
import pyspark
from pyspark.sql import SparkSession
import mlflow
import mlflow.spark
import sys
import time
from pyspark.ml.classification import RandomForestClassifier
from pyspark.ml.evaluation import BinaryClassificationEvaluator
```

请注意，我们导入了与 MLflow 相关的库，这些库允许用户使用 PySpark。MLflow 支持多种语言和包，包括 Spark 和 Python，并对`scikitlearn`和`tensorflow`等包提供了扩展支持。

```python
spark = SparkSession.builder.appName("mlflow_example").getOrCreate()
```