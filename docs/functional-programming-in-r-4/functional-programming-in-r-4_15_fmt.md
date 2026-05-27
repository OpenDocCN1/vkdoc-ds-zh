# 第 3 章：纯函数式编程

```r
x <- 1:1000

microbenchmark::microbenchmark(
  r_lin_search(-1, x),
  l_lin_search(-1, x)
)
```

## Unit: microseconds

### expr
- `r_lin_search(-1, x )`
- `l_lin_search(-1, x )`

### 输出数据

| 指标 | `r_lin_search(-1, x )` | `l_lin_search(-1, x )` |
| :--- | :--- | :--- |
| min | 834.297 | 20.117 |
| lq | 927.9130 | 20.6345 |
| mean | 1121.50154 | 47.32387 |
| median | 986.011 | - |
| uq | 1094.573 | - |
| max | 5361.536 | - |
| neval | 100 | - |
| cld | b | - |