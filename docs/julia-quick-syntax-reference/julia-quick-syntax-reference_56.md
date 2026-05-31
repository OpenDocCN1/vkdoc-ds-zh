# 需求(j) .. sum(i, x(i,j)) =g= b(j);

```julia
@constraints trmodel begin
    supply[p in plants], # 满足工厂 p 的供应限制
        sum(x[p,m] for m in markets) <= a[p]
    demand[m in markets], # 满足市场 m 的需求
        sum(x[p,m] for p in plants) >= b[m]
end
```

### 声明模型目标函数

与约束和变量不同，目标函数是唯一的、单一的函数。请注意，正是在这一点上，您指定了优化的方向。

