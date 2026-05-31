# 导入必要的库
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, count, sum, max, lit, datediff, when, concat, percentile_approx
from pyspark.sql.types import DateType
import datetime
import random

