## 打印结果
```
print("t 统计量:", t_statistic)
print("p 值:", p_value)
```
输出：
```
t 统计量: -1.31
p 值: 0.19
```

注意，t 检验结果不显著。这意味着你无法拒绝零假设。因此，你可以安全地得出结论：新版本的网站布局与旧版本相比，并未显著推动更多购买。尽管这个实验没有产生统计学上显著的结果，但它带来了希望，即未来可以测试更多迭代，这可能会改善购买行为。

另外，我们在 GitHub 上的一个单独笔记本中提供了一个假样本用例。当你运行那个实验时，你会注意到它显示了 6% 的提升。在这种情况下，t 检验也显示结果不显著。不幸的是，当你无法拒绝零假设时，你没有足够的证据表明新版本优于旧版本。因此，你需要进一步调查、完善分析并迭代实验。

### 使用 PySpark 进行 t 检验

现在，让我们看看如何使用 PySpark 的可扩展能力运行 t 检验。请记住，当你在第 5 章学习线性回归时，你查看了变量系数和变量的 p 值显著性。这些显著的 p 值基于 t 检验。那么，让我们利用这一点，看看是否可以通过线性回归得到相同的结果。要运行线性回归，你需要将输入特征转换为向量。

```python
from pyspark.ml.regression import LinearRegression
from pyspark.ml.feature import VectorAssembler

vectorAssembler = VectorAssembler(inputCols=["group_flag"], outputCol="v_group_flag")
v_data = vectorAssembler.transform(data)
```