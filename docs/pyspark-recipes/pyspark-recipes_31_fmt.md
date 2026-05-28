# 技巧 8-1. 创建一个 `DataFrame`

## 问题

你希望创建一个 `DataFrame`。

## 解决方案

如你所知，`DataFrame` 是一个命名列的集合。你可能还记得第 5 章中的灯丝数据。你希望对灯丝数据执行以下操作：

- 创建一个 `DataFrame`
- 了解 `DataFrame` 的模式
- 打印 `DataFrame` 的内容
- 筛选出 100W 灯泡的数据
- 从 `DataFrame` 中选择寿命大于某个值的 100W 灯泡数据

## 工作原理

### 步骤 8-1-1. 创建灯丝数据的嵌套列表

首先，我们必须创建一个灯丝数据的嵌套列表。你在第 5 章中已经熟悉了这个数据。

```python
>>> filamentData = [['filamentA','100W',605],
...  ['filamentB','100W',683],
...  ['filamentB','100W',691],
...  ['filamentB','200W',561],
...  ['filamentA','200W',530],
...  ['filamentA','100W',619],
...  ['filamentB','100W',686],
...  ['filamentB','200W',600],
...  ['filamentB','100W',696],
...  ['filamentA','200W',579],
...  ['filamentA','200W',520],
...  ['filamentA','100W',622],
...  ['filamentA','100W',668],
...  ['filamentB','200W',569],
...  ['filamentB','200W',555],
...  ['filamentA','200W',541]]
```

创建这个 `filamentData` 嵌套列表后，我们将为其创建一个 `RDD`。为了创建 `RDD`，我们将使用 `parallelize()` 函数：

```python
>>> filamentDataRDD = sc.parallelize(filamentData, 4)
>>> filamentDataRDD.take(4)
```

输出如下：

```python
[['filamentA', '100W', 605],
['filamentB', '100W', 683],
['filamentB', '100W', 691],
['filamentB', '200W', 561]]
```

`filamentDataRDD` 这个 `RDD` 有四个分区。我们已成功创建了 `RDD`。下一步是为我们的 `DataFrame` 创建模式。

### 步骤 8-1-2. 创建 `DataFrame` 的模式

在我们的 `DataFrame` 中，有三个列。首先，我们要定义这些列。我们通过使用 `StructField()` 函数来定义列。`PySparkSQL` 有自己的数据类型，所有这些类型都定义在子模块 `pyspark.sql.types` 中。我们必须从 `pyspark.sql.types` 导入所有内容：

```python
>>> from pyspark.sql.types import *
```

导入所需的子模块后，我们定义 `DataFrame` 的第一个列：

```python
>>> FilamentTypeColumn = StructField("FilamentType",StringType(),True)
```

让我们看一下 `StructField()` 的参数。第一个参数是列名。在这个例子中，列名是 `FilamentType`。第二个参数是列中元素的数据类型。在这个例子中，第一个列的数据类型是 `StringType()`。我们知道，某个列的某些元素可能为空。因此，最后一个参数（其值为 `True`）表示此列可能包含空值或缺失数据。

```python
>>> BulbPowerColumn = StructField("BulbPower",StringType(),True)
>>> LifeInHoursColumn = StructField("LifeInHours",StringType(),True)
```

我们为每个列创建了一个 `StructField`。现在，我们必须使用 `StructType` 对象来创建完整 `DataFrame` 的模式，如下所示：

```python
>>> FilamentDataFrameSchema = StructType([FilamentTypeColumn, BulbPowerColumn, LifeInHoursColumn])
```

`FilamentDataFrameSchema` 就是我们 `DataFrame` 的完整模式。

```python
>>> FilamentDataFrameSchema
```

输出如下：

```
StructType(
List(StructField(FilamentType,StringType,true),
StructField(BulbPower,StringType,true),
StructField(LifeInHours,StringType,true))
)
```

从上面的代码可以看出，我们可以通过 `FilamentDataFrameSchema` 变量观察到 `DataFrame` 三个列的模式。

我们知道，`DataFrame` 是 `Row` 对象的 `RDD`。因此，我们必须将 `filamentDataRDD` 这个 `RDD` 转换为 `Row` 对象的 `RDD`。在 `Row` 对象的 `RDD` 中，每一行都是一个 `Row` 对象。在下一个配方步骤中，我们将把 `RDD` 转换为 `Row` 对象的 `RDD`。

### 步骤 8-1-3. 创建 `Row` 对象的 `RDD`

`RDD` 的 `map()` 函数最适合将任何 `RDD` 从一种结构转换为另一种结构，以便将我们的 `filamentRDD2` 这个 `RDD` 转换为 `Row` 对象的 `RDD`。`DataFrame` 本质上就是 `Row` 对象的 `RDD`。让我们创建一个行的 `RDD`。但是，为了使用 `Row`，我们必须首先导入它。`Row` 位于 `pyspark.sql` 中。我们可以像这样导入 `Row`：

```python
>>> from pyspark.sql import Row
>>> filamentRDDofROWs = filamentDataRDD.map(lambda x :Row(str(x[0]), str(x[1]), str(x[2])))
>>> filamentRDDofROWs.take(4)
```

输出如下：

```
[Row('filamentA', '100W', '605'),
 Row('filamentB', '100W', '683'),
 Row('filamentB', '100W', '691'),
 Row('filamentB', '200W', '561')]
```

可以看到，我们已经创建了一个行的 `RDD`：`filamentRDDofROWs`。我们对 `RDD` 应用了 `take()` 函数，并打印出了其中的四行元素。

### 步骤 8-1-4. 创建 `DataFrame`

我们已经创建了模式和行的 `RDD`。因此，我们可以创建 `DataFrame` 了。为了创建 `DataFrame`，我们需要 `SQLContext` 对象。让我们在下面一行代码中创建 `SQLContext` 对象：

```python
>>> from pyspark.sql import SQLContext
>>> sqlContext = SQLContext(sc)
```

我们创建了 `sqlContext` 对象。就像 `SparkContext`（在我们的案例中，即在 `PySpark` 控制台中，`sc` 是 `PySpark` 的入口点，我曾提到 `sc` 是 `SparkContext` 的一个对象）一样，`SQLContext` 是 `PySparkSQL` 的入口点。

使用定义在 `SQLContext` 上的 `createDataFrame()` 函数，我们将创建我们的 `DataFrame`：`filamentDataFrameRaw`。我们向 `createDataFrame()` 函数提供两个参数：第一个是 `Row` 对象的 `RDD` `filamentRDDofROWs`；第二个参数是我们 `DataFrame` 的模式 `FilamentDataFrameSchema`。

```python
>>> filamentDataFrameRaw = sqlContext.createDataFrame(filamentRDDofROWs, FilamentDataFrameSchema)
```

我们已经根据灯丝数据创建了 DataFrame。我们给这个 DataFrame 赋了引用名`filamentDataFrameRaw`。让我们在控制台上打印 DataFrame 的记录。之前，我们使用`take()`函数从 DataFrame 获取数据。但现在我们要改变获取数据的方式；我们将使用`show()`函数。`show()`函数能以一种美观的方式打印数据。我们可以向`show()`函数提供要显示的行数。在下面一行中，将获取四条记录：

```python
>>> filamentDataFrameRaw.show(4)
```

输出如下，仅显示前四行：

```
+------------+---------+-----------+
|FilamentType|BulbPower|LifeInHours|
+------------+---------+-----------+
|   filamentA|     100W|        605|
|   filamentB|     100W|        683|
|   filamentB|     100W|        691|
|   filamentB|     200W|        561|
+------------+---------+-----------+
```

## 步骤 8-1-5. 打印 DataFrame 的模式

我们已经创建了 DataFrame。让我们检查一下它的模式。我们可以通过使用 DataFrame 上定义的`printSchema()`函数来获取 DataFrame 的模式：

```python
>>> filamentDataFrameRaw.printSchema()
```

输出如下：

```
root
|-- FilamentType: string (nullable = true)
|-- BulbPower: string (nullable = true)
|-- LifeInHours: string (nullable = true)
```

`printSchema()`函数的输出显示该 DataFrame 有三个列。这些列指示了列名、列的数据类型以及列是否可空（`nullable`）。如果你注意到`LifeInHours`列的数据类型是`string`，那你真是个细心的读者。以小时表示的时间最好用整数数据类型或浮点类型来表示。因此，我们必须更改第三列的数据类型。

我们可以通过使用`cast()`函数将列值从一种数据类型转换为另一种数据类型。

## 步骤 8-1-6. 更改列的数据类型

`withColumn()`函数通过向 DataFrame 添加一个新列来返回一个新的 DataFrame。但如果该列已存在于 DataFrame 中，`withColumn()`函数将替换现有列：

```python
>>> filamentDataFrame = filamentDataFrameRaw.withColumn('LifeInHours',filamentDataFrameRaw.LifeInHours.cast(FloatType()))
```

现在，检查模式将返回`float`作为`LifeInHours`列的数据类型：

```python
>>> filamentDataFrame.printSchema()
```

输出如下：

```
root
|-- FilamentType: string (nullable = true)
|-- BulbPower: string (nullable = true)
|-- LifeInHours: float (nullable = true)
```

`LifeInHours`列的数据类型已从`string`类型转换为`float`类型。让我们通过获取一些行来检查 DataFrame。我们可以使用以下函数显示行：

```python
>>> filamentDataFrame.show(5)
```

输出如下，仅显示前五行：

```
+--------------+----------+------------+
|FilamentType  |BulbPower |LifeInHours |
+--------------+----------+------------+
|   filamentA  |   100W   |   605.0    |
|   filamentB  |   100W   |   683.0    |
|   filamentB  |   100W   |   691.0    |
|   filamentB  |   200W   |   561.0    |
|   filamentA  |   200W   |   530.0    |
+--------------+----------+------------+
```

我们可以观察到，`LifeInHours`列的数据类型已更改为浮点型。

可以使用 DataFrame 对象的`columns`属性获取列名：

```python
>>> filamentDataFrame.columns
```

输出结果如下：

```
['FilamentType', 'BulbPower', 'LifeInHours']
```

## 步骤 8-1-7. 筛选出 BulbPower 为 100W 的数据

根据特定逻辑筛选行可以通过使用`filter()`函数完成。该函数接受一个逻辑表达式，并返回一个包含筛选后数据的 DataFrame：

```python
>>> filamentDataFrame100Watt = filamentDataFrame.filter(filamentDataFrame.BulbPower == '100W')
```

我们需要所有`BulbPower`等于`100W`的行。因此，我们将`filamentDataFrame.BulbPower == '100W'`作为参数传递给`filter()`函数。让我们看看`filamentDataFrame100Watt` DataFrame 中的内容：

```python
>>> filamentDataFrame100Watt.show()
```

输出结果如下：

```
+--------------+------------+---------------+
|FilamentType  |BulbPower   |LifeInHours    |
+----------------+-------------+---------------+
|   filamentA  |   100W     |   605.0    |
|   filamentB  |   100W     |   683.0    |
|   filamentB  |   100W     |   691.0    |
|   filamentA  |   100W     |   619.0    |
|   filamentB  |   100W     |   686.0    |
|   filamentB  |   100W     |   696.0    |
|   filamentA  |   100W     |   622.0    |
|   filamentA  |   100W     |   668.0    |
+----------------+-------------+---------------+
```

`filter()`函数准确地完成了任务。

## 步骤 8-1-8. 从 DataFrame 中选择数据

复合逻辑表达式也可以在`filter()`函数中使用。在此步骤中，我们将使用带有`&`运算符的复合逻辑表达式：

```python
>>> filamentData100WGreater650 = filamentDataFrame.filter((filamentDataFrame.BulbPower == '100W') & (filamentDataFrame.LifeInHours > 650.0))
>>> filamentData100WGreater650.show()
```

输出结果如下：

``` 
+--------------+-----------+------------+ 
|FilamentType  |BulbPower  |LifeInHours | 
+--------------+-----------+------------+ 
|   filamentB  |   100W    |   683.0    | 
|   filamentB  |   100W    |   691.0    | 
|   filamentB  |   100W    |   686.0    | 
|   filamentB  |   100W    |   696.0    | 
|   filamentA  |   100W    |   668.0    | 
+--------------+-----------+------------+ 
``` 

最终，我们满足了要求。在下一个方法中，我们将对`DataFrame`进行探索性分析。