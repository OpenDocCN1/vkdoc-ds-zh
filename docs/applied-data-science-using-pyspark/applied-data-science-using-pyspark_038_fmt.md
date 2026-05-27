# 导入 Sparksession

```
from pyspark.sql import SparkSession
spark=SparkSession.builder.appName("LDA").getOrCreate()
```