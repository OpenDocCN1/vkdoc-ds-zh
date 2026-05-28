# 配方 6-2：从 DataFrame 创建临时视图

## 问题

创建 DataFrame 是开始使用 Spark SQL 的第一步，而第二步则是将创建的 DataFrame 注册为 Spark 的 `SparkSession` 中的一个临时表。对于那些好奇 `SparkSession` 是什么的人，它是处理结构化数据（如行和列）的入口点。Spark 提供了一个名为 `createOrReplaceTempView` 的 API，它允许你将 DataFrame 注册为可查询的实体。

## 解决方案

此临时视图将作为一个内存中的实体，可用于对同一数据进行分析和其他重复性操作。

## 工作原理

我们来创建这个临时视图。

```python
>>> studentDf.createOrReplaceTempView("Students")
```

执行这条语句将创建一个名为 `Students` 的临时视图。我们继续验证一下。

```python
>>> spark.tableNames()
```

这是输出结果：

```text
['students']
```

这里你可以验证 `students` 视图已经注册到了 `SparkSession` 中，现在已经可以随时在 SQL 查询中使用了。你可能想知道这个视图的有效期是多久。例如，如果我们退出 Spark Session 并重新登录，这个视图是否仍然可用？答案是否定的，因为这些视图仅在创建它们的会话范围内有效。当我们退出 Spark 会话并重新登录时，我们创建的 `students` 视图将不可用。

我们在这里验证一下：

```python
>>> quit()
```

我们退出当前会话并重新登录。

```python
>>> from pyspark import SQLContext
>>> sqlContext = SQLContext(sc)
```

通过这两条语句，我们导入了 `SQLContext`，然后使用默认可用的 `SparkContext`（通过变量名 `sc`）实例化了 `SQLContext`。

```python
>>> sqlContext.tableNames()
```

以下是输出结果，显示当前会话中没有已存在的视图。

```text
[]
```