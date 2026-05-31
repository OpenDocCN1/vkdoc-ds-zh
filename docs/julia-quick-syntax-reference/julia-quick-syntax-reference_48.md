# 模型声明（运输模型）

JULIA

```julia
trmodel = Model(with_optimizer(GLPK.Optimizer, msg_lev=GLPK.MSG_ON)) # 我们选择 GLPK 并输出详细信息
```

### 声明模型变量

变量在 `@variable` 宏中声明和定义，并且可以具有多个维度——也就是说，它们可以通过多个索引进行索引。

边界在声明时同时给出。与 GAMS 不同，您无需定义位于目标函数左侧的变量。

## 定义变量

JULIA

