# 秘诀 5-2. 对多个键进行数据聚合

## 问题

你想要对一个 DataFrame 进行数据聚合，并按多个键进行分组。

## 解决方案

我们将使用前一个秘诀中的录取数据。为了根据多列条件进行数据聚合，我们必须按多列进行分组。我们可以使用 `groupby()` 函数来按多列条件对数据进行分组。我们通过以下步骤来实现：

-   按 `admit` 和 `gender` 列对数据进行分组，并找出申请入学的平均值。
-   按 `admit` 和 `department` 列对数据进行分组，并找出申请入学的平均值。

## 工作原理

让我们打印 UCB 的录取数据。

```
In [1]: ucbDataFrame.show()
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

### 步骤 5-2-1. 按性别分组并查找申请的平均值

我们将使用 `groupby()` 函数对 `admit` 和 `gender` 列进行分组。随后，我们将应用 `mean()` 函数。

```
In [2]: groupedOnAdmitGender = ucbDataFrame.groupby(["admit", "gender"]).mean()
In [3]: groupedOnAdmitGender.show()
```

以下是输出：

```
+--------+------+------------------+
|   admit|gender|    avg(frequency)|
+--------+------+------------------+
|Rejected|Female|             213.0|
|Rejected|  Male|248.83333333333334|
|Admitted|Female| 92.83333333333333|
|Admitted|  Male|199.66666666666666|
+--------+------+------------------+
```

### 步骤 5-2-2. 按院系分组并查找申请的平均值

我们将使用 `groupby()` 函数对 `admit` 和 `department` 列进行分组。随后，我们将应用 `mean()` 函数。

```
In [4]: groupedOnAdmitDepartment = ucbDataFrame.groupby(["admit", "department"]).mean()
In [5]: groupedOnAdmitDepartment.show()
```

以下是输出：

```
+--------+----------+--------------+
|   admit|department|avg(frequency)|
+--------+----------+--------------+
|Admitted|         C|         161.0|
|Admitted|         E|          73.5|
|Rejected|         A|         166.0|
|Admitted|         B|         185.0|
|Admitted|         F|          23.0|
|Admitted|         A|         300.5|
|Rejected|         C|         298.0|
|Rejected|         D|         261.5|
|Admitted|         D|         134.5|
|Rejected|         F|         334.0|
|Rejected|         B|         107.5|
|Rejected|         E|         218.5|
+--------+----------+--------------+
```