# 基于上述赋值的符号评估更多表达式
m.evaluate('runs(y) & jumps_over(y, x) & sleeps(x)', g)
True
m.evaluate('exists y. (fox(y) & runs(y))', g)
True
```

此代码片段基于不同符号的估值，根据规则和域描绘了表达式的评估。我们创建基于 FOL 的表达式，并根据预定义的假设查看它们的结果。例如，第一个表达式返回假，因为 Rover 从不`runs()`，第二个和第三个表达式为真，因为它们满足了所有条件，比如 Felix 和 Alex 可以跳过 Rover 或 Garfield，Rover 是一只狗且不跑，而 Garfield 是一只猫。

第二组表达式是基于将 Felix 和 Rover 赋值给域（`dom`）中的特定符号，并在评估表达式时传递该变量（`g`）来进行评估的。我们甚至可以使用`satisfiers()`函数来满足开放公式或表达式，如下所示：

```
