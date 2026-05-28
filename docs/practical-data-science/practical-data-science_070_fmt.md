# 计算 `a` 和 `b` 的描述性统计量。

```
abar = a.mean()

avar = a.var(ddof=1)

na = a.size

adof = na - 1

bbar = b.mean()

bvar = b.var(ddof=1)

nb = b.size

bdof = nb - 1
```