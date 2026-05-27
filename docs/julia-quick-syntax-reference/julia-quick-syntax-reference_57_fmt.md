# 目标函数

```julia
@objective trmodel Min begin
    sum(c[p,m] * x[p,m] for p in plants, m in markets)
end
```

### 模型的可读可视化（可选）

如果需要，您可以以人类可读的方式打印优化模型，以便检查其是否正确。

```julia
print(trmodel) # 以数学形式打印模型
```

### 模型求解

正是在这一点上，调用求解器并将模型传递给求解器引擎以求解。请注意感叹号，它表示`trmodel`对象会因调用而被修改。

![Image 20](img/index-195_1.png)

您可以使用`termination_status(modelObject)`检查求解器的返回状态（期望得到`MOI.OPTIMAL`），并分别使用`objective_value(modelObject)`和`value(variableName)`检索目标函数和变量的值。对偶值可以通过调用`dual(constraintName)`获得。

然后，您可以按自己喜欢的方式打印/可视化它们，例如：

```julia
status = termination_status(trmodel)
```

```julia
if (status == MOI.OPTIMAL || status == MOI.LOCALLY_SOLVED || 
    status == MOI.TIME_LIMIT) && has_values(trmodel)
    if (status == MOI.OPTIMAL)
        println("** 问题求解正确 **")
    else
        println("** 问题返回了（可能次优的）解 **")
    end
    println("- 目标函数值（总成本）: ", objective_value(trmodel))
    println("- 最优路线:")
    optRoutes = value.(x)
    
    [println("$p --> $m: $(optRoutes[p,m])") for m in markets, p in plants]
    
    println("- 供应的影子价格:")
    [println("$p = $(dual(supply[p]))") for p in plants]
    
    println("- 需求的影子价格:")
    [println("$m = $(dual(demand[m]))") for m in markets]
else
    println("模型求解不正确。")
    println(status)
end
```

输出结果应如下所示：