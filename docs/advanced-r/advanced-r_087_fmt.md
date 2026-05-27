# 一个包含 2 行 5 列的 tibble

此时，可能不清楚 `Type` 是什么。`Type` 告诉我们当前数据是属于萼片还是花瓣的长度或宽度。当我们的数据从宽格式堆叠为长格式时，其长度会翻倍。现在，`Sepal` 和 `Petal` 列必须承担双重职责，而 `Type` 的存在就是为了让我们知道具体是哪一种。看看我们数据的这个 `slice()` 切片：

```
slice(diris.long, c(1, 151))
```

```
物种      ID  类型 萼片 花瓣
1  setosa     1     1   5.1   1.4
2  setosa     1     2   3.5   0.2
```

`Type` 根据其表示的是长度还是宽度，会在 `1` 和 `2` 之间变化。然而，该信息并未编码在我们的数据中，因此我们使用 `mutate()` 函数来处理 `tibble`，以确保它理解 `Type` 是一个具有两个水平的因子，一个是 `Length`，另一个是 `Width`。请注意，经过这次清理后，我们的切片现在看起来合理多了。

```
diris.long     
1  setosa     1 Length   5.1   1.4
2  setosa     2 Length   4.9   1.4
3  setosa     3 Length   4.7   1.3
4  setosa     4 Length   4.6   1.5
5  setosa     5 Length   5.0   1.4
```