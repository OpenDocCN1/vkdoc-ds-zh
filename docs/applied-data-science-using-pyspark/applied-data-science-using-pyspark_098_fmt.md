# 抽样方法

## 有放回简单随机抽样
`df_srs_without_rep = df.sample(True, 0.5, 23)`
`df_srs_without_rep.show(10, False)`

## 分层采样

在分层采样中，总体的每个成员都被分组到称为*层*的同质子组中，并从每个组（层）中选择一个代表。

```python
```