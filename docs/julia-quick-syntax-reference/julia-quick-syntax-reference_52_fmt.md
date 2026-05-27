# 正变量 `x`

```julia
@variables trmodel begin
    x[p in plants, m in markets] >= 0 # 运输量, 单位：箱
end
```

## 定义约束

### 声明模型约束

与 GAMS 一样，每个约束实际上可以是一个“约束族”，因为它可以在定义的集合上进行索引。