# 使用 scipy.stats.ttest_ind_from_stats。
t2, p2 = ttest_ind_from_stats(abar, np.sqrt(avar), na,
                              bbar, np.sqrt(bvar), nb,
                              equal_var=False)
print("t-Test_ind_from_stats: t = %g  p = %g" % (t2, p2))
```

查阅韦尔奇 T 检验的公式。

第三，你可以使用该公式来计算检验统计量。

```python
