# 定义插补函数

```
imputer = Imputer(
inputCols=input_cols,
outputCols=["{}_imputed".format(c) for c in input_cols])
```