# 使用 `scipy.stats.ttest_ind`。

计算`t`和`p`值：

```python
t, p = ttest_ind(a, b, equal_var=False)
print("t-Test_ind:            t = %g  p = %g" % (t, p))
```

其次，获取描述性统计量。