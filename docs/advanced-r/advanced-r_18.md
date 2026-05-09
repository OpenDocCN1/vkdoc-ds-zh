# 7: Jane Mary, Jack Dun-Dee 0 jane jack
```

我们可以看到 `This` `Test` 出现在两行中，因此我们进行汇总并输出最终结果。`This Test` 有两个精确匹配。`Test Thas` 没有精确匹配，但 `Uncertain` 列下有一个近似匹配。最后，`jack dee` 是 `Jack Dun-Dee` 的近似但不确信的匹配，而 `jane jack` 是 `Jane Mary` 或 `Jack Dun-Dee` 的不确信匹配。

```r
finaloutput <- output[, .(
  N = sum(N),
  Uncertain = paste(na.omit(Written), collapse = ", ")),
  by = Match]
finaloutput
