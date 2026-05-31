# 第 3 章：纯函数式编程

```
x <- 1:1000

microbenchmark::microbenchmark(
  r_lin_search(-1, x),
  l_lin_search(-1, x)
)
```

## Unit: microseconds
##   expr                    min        lq       mean
##   r_lin_search(-1, x )  834.297   927.9130  1121.50154
##   l_lin_search(-1, x )   20.117    20.6345    47.32387
##   median      uq        max     neval  cld
##   986.011   1094.573   5361.536   100    b



