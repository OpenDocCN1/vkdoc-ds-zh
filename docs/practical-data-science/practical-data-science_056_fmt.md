# 定义要使用的"分类器"

```python
classifiers = {
    "经验协方差": `EllipticEnvelope(support_fraction=1., contamination=0.261)`,
    "稳健协方差（最小协方差行列式）": `EllipticEnvelope(contamination=0.261)`,
    "OCSVM": `OneClassSVM(nu=0.261, gamma=0.05)`
}
```

这些分类器都假设你在测试正常数据条目。我们来看一下 `EllipticEnvelope(support_fraction=1., contamination=0.261)`。其中的 `support_fraction` 是你希望用来确定正常值与异常值边界的数据占总体的比例。在这个例子中，我们用了 1，也就是 100%。`contamination` 则指示了数据集中可能存在的异常值比例，即数据集的污染程度，也就是异常值在数据集中的占比。在这里，它被设置为 0.261（最大可能为 0.5），通常可以描述为 26.1% 的污染度。

而 `EllipticEnvelope(contamination=0.261)` 仅仅是改变了包含的数据集比例，它使用了所有设置的默认值，唯一例外是将污染度（`contamination`）设置为 26.1%。

第三种是另一种类型的检测方法，称为 `sklearn.svm.OneClassSVM`，本章稍后会进行讨论。

```python
colors = ['m', 'g', 'b']
legend1 = {}
legend2 = {}
```