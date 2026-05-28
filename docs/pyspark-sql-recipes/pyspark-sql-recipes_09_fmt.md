# 5. 使用 PySparkSQL 进行数据合并与数据聚合

数据合并与数据聚合是 PySparkSQL 用户日常工作中的重要组成部分。本章将讨论并介绍以下方法。

-   方法 5-1：基于单个键聚合数据
-   方法 5-2：基于多个键聚合数据
-   方法 5-3：创建列联表
-   方法 5-4：对两个 DataFrame 执行连接操作
-   方法 5-5：纵向堆叠两个 DataFrame
-   方法 5-6：横向堆叠两个 DataFrame
-   方法 5-7：执行缺失值插补

## 方法 5-1：基于单个键聚合数据

### 问题

你想对一个 DataFrame 进行数据聚合，并按照单个键进行分组。

### 解决方案

我们通过执行数据聚合来观察数据的摘要信息。

数据取自 R 语言的 `datasets` 包，名为 `UCBAdmissions`。该数据集最初以 R 表格形式存在。我将其转换为表格形式并保存到了 MySQL 数据库的一个表中。我们将使用 PySparkSQL 读取该表，并执行以下操作：

-   被录取和被拒绝学生数量的平均值
-   按性别统计申请入学的学生数量的平均值
-   按性别统计申请频率的平均值

为了使用聚合 API，如果聚合操作需要基于某个列进行，我们必须先对数据进行分组。要基于某个数据条件对列进行分组，需使用 `groupby()` 函数。对数据分组后，可以对其应用以下聚合函数：

-   `mean()`
-   `count()`
-   `sum()`
-   `min()`
-   `max()`
-   等。

### 工作原理

#### 步骤 5-1-1. 从 MySQL 读取数据

录取数据存在于 MySQL 的 `pysparksqlbook` 数据库的 `ucbdata` 表中。我们可以通过以下代码行进行读取。

```
In [1]: dbURL = "jdbc:mysql://localhost/pysparksqlbook"
In [2]: ucbDataFrame = spark.read.format("jdbc").options(url = dbURL, database ='pysparksqlbook', dbtable ='ucbdata', user="root", password="").load();
In [3]: ucbDataFrame.show()
```

以下是输出：

```
+--------+------+----------+---------+
|   admit|gender|department|frequency|
+--------+------+----------+---------+
|Admitted|  Male|         A|      512|
|Rejected|  Male|         A|      313|
|Admitted|Female|         A|       89|
|Rejected|Female|         A|       19|
|Admitted|  Male|         B|      353|
|Rejected|  Male|         B|      207|
|Admitted|Female|         B|       17|
|Rejected|Female|         B|        8|
|Admitted|  Male|         C|      120|
|Rejected|  Male|         C|      205|
|Admitted|Female|         C|      202|
|Rejected|Female|         C|      391|
|Admitted|  Male|         D|      138|
|Rejected|  Male|         D|      279|
|Admitted|Female|         D|      131|
|Rejected|Female|         D|      244|
|Admitted|  Male|         E|       53|
|Rejected|  Male|         E|      138|
|Admitted|Female|         E|       94|
|Rejected|Female|         E|      299|
+--------+------+----------+---------+
only showing top 20 rows
```

#### 步骤 5-1-2. 计算所需的平均值

我们需要计算被录取和被拒绝学生人数的平均值。我们将使用 `groupby()` 函数对 `admit` 列进行分组。随后，我们将应用 `mean()` 函数。

```
In [4]: groupedOnAdmit = ucbDataFrame.groupby(["admit"]).mean()
In [5]: groupedOnAdmit.show()
```

以下是输出：

```
+--------+------------------+
|   admit|    avg(frequency)|
+--------+------------------+
|Rejected|230.91666666666666|
|Admitted|            146.25|
+--------+------------------+
```

#### 步骤 5-1-3. 按性别分组

现在我们想要得到申请入学的学生按性别分组的平均值。我们将使用 `groupby()` 函数对 `gender` 列进行分组。随后，我们将应用 `mean()` 函数。

```
In [6]: groupedOnGender = ucbDataFrame.groupby(["gender"]).mean()
In [7]: groupedOnGender.show()
```

以下是输出：

```
+------+------------------+
|gender|    avg(frequency)|
+------+------------------+
|Female|152.91666666666666|
|  Male|            224.25|
+------+------------------+
```

#### 步骤 5-1-4. 按性别查找申请的平均频率

我们将使用 `groupby()` 函数对 `department` 列进行分组。随后，我们将应用 `mean()` 函数。

```
In [8]: groupedOnDepartment = ucbDataFrame.groupby(["department"]).mean()
In [9]: groupedOnDepartment.show()
```

以下是输出：

```
+----------+--------------+
|department|avg(frequency)|
+----------+--------------+
|         F|         178.5|
|         E|         146.0|
|         B|        146.25|
|         D|         198.0|
|         C|         229.5|
|         A|        233.25|
+----------+--------------+
```