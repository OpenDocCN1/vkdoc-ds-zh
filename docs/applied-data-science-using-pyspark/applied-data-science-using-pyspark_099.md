# Stratified Sampling
from pyspark.sql.types import IntegerType
df = df.withColumn("Exited", df["Exited"].cast(IntegerType()))
df.select('Exited').describe().show()
