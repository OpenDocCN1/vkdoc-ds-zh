# 对所有特征进行归一化，使其缩放到 0 到 1 之间

```
normalizer = Normalizer(inputCol="features", outputCol="normFeatures", p=1.0)
```