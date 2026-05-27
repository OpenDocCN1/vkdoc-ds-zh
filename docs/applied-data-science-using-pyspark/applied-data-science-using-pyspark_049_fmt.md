# Import Sparksession
```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("CF").getOrCreate()
```