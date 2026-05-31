# 一个包含 2 行 5 列的 tibble
物种      ID    类型 萼片 花瓣

1  setosa     1 Length   5.1   1.4
2  setosa     1  Width   3.5   0.2
```

我们处理了宽数据并将其变长。现在我们通过再次使用 `reshape()` 函数来逆转这个过程，这次将 `direction` 形参设置为 `wide`。由于我们要转换为宽格式，我们不需要 `varying` 参数，因为我们即将通过这个过程获得它们。我们需要 `ids = diris.long$ID`，以便将行 `1` 和 `151` 合并到同一个宽行中。

```
diris.wide2                                
1  setosa     1          5.1          1.4         3.5         0.2
2  setosa     2          4.9          1.4         3.0         0.2
3  setosa     3          4.7          1.3         3.2         0.2
4  setosa     4          4.6          1.5         3.1         0.2
5  setosa     5          5.0          1.4         3.6         0.2

