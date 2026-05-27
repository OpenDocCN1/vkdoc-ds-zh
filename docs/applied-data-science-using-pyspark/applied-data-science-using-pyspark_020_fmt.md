# 识别并分配变量列表

```python
float_vars=list(set(df.columns) - set(['CUST_ID']))
for column in float_vars:
df=df.withColumn(column,df[column].cast(FloatType()))
from pyspark.ml.feature import Imputer
```