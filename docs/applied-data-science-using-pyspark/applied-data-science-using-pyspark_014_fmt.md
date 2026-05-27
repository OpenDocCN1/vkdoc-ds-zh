# 导入 SparkSession

```python
from pyspark.sql import SparkSession
spark=SparkSession.builder.appName("Clustering").getOrCreate()
```