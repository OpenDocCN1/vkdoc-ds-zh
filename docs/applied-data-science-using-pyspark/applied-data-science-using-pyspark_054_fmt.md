```python
# Import appropriate libraries
from `pyspark.sql.types` import *
import `pyspark.sql.functions` as `sql_fun`
from `pyspark.ml.recommendation` import `ALS`, `ALSModel`
from `pyspark.mllib.evaluation` import `RegressionMetrics`, `RankingMetrics`
from `pyspark.ml.evaluation` import `RegressionEvaluator`
import `re`
```