# `topeka = 0.12599999999999997`

## 第 10 章 数学库

### 10.1.2 选择披萨还是三明治：一个非线性问题

非线性问题的唯一区别在于，你需要为变量提供良好的初始点（试试在初始点为 0 时运行以下示例！），你需要使用特定的 `@NLconstraints` 宏来定义（非线性）约束，并使用 `@NLobjective` 宏来定义（非线性）目标。

请注意，你无需提供任何额外信息（特别是无需提供导数）。

#### 问题描述

一名学生需要决定如何分配每周 80 美元的食品预算。她只能在披萨（`p`）和三明治（`s`）之间做出选择。披萨每份 10 美元，而三明治每份只需 4 美元。

她的“效用函数”（即她对给定披萨和三明治组合的满意程度）为 `100 * p - 2 * p² + 70 * s - 2 * s² - 3 * p * s`。她喜欢这两种食物，但更偏爱披萨。

你在此使用一个简单的两变量非线性函数最大化的单约束问题。为简化示例，这里不使用集合（即变量和约束均为标量）。

`max 100 * p - 2 * p² + 70 * s - 2 * s² - 3 * p * s`

`p, s`

满足约束：

`10 * p + 4 * s <= 80`

其中第一个方程是效用函数，第二个方程是经济学家所称的“预算约束”，即我们不能花费超出预算的钱 :-)。

#### 导入库并声明模型

由于处理的是非线性模型，你需要选择能够处理此类问题的求解引擎。`IPOPT` 可胜任此任务：

```
using JuMP, Ipopt
m = Model(with_optimizer(Ipopt.Optimizer, print_level=0))
```

#### 声明模型变量、约束和目标函数

由于这些是物理量，除了显式约束外，还需要为变量添加一个下界 0（上下界可以在一个表达式中指定，如 `lB <= var <= uB`）。

```
m = Model(with_optimizer(Ipopt.Optimizer))
@variable(m, 0 <= p, start=1, base_name="披萨数量")
@variable(m, 0 <= s, start=1, base_name="三明治数量")
@constraint(m, budget, 10p + 4s <= 80)
@NLobjective(m, Max, 100*p - 2*p² + 70*s - 2*s² - 3*p*s)
```

你可以将问题理解为最优的“周平均值”，因此最优结果为半份披萨也是可以接受的。否则，你应在变量声明中添加 `integer=true` 选项将其限制为整数。但这样问题的类别将变为混合整数非线性规划（`MINLP`），并且你也需要更换求解引擎（`bonmin` 适用于求解 `MINLP` 问题）。

#### 求解模型并可视化结果

由于问题是非线性的，求解结果报告为“局部最优解”。你需要自行判断问题的性质是否保证局部最优解也是全局最优解（本例中即是如此）。

```
optimize!(m)
status = termination_status(m)
if (status == MOI.OPTIMAL || status == MOI.LOCALLY_SOLVED ||
    status == MOI.TIME_LIMIT) && has_values(m)
    if (status == MOI.OPTIMAL)
        println("** 问题正确求解 **")
    else
        println("** 问题返回了一个（可能非最优的）解 **")
    end
    println("- 目标值 : ", objective_value(m))
    println("- 最优解:")
    println("披萨: $(value.(p))")
    println("三明治: $(value.(s))")
    println("- 对偶变量（预算）: $(dual.(budget))")
else
    println("模型未正确求解。")
    println(status)
end

# 预期输出：
# ** 问题返回了一个（可能非最优的）解 **
# - 目标值 : 750.8928616403513
# - 最优解:
# 披萨: 4.642857242997538
# - 对偶变量（预算）: -5.624999975248088
```

### 10.2 SymPy：计算机代数系统

另一个有用的数学包是 `SymPy.jl`（参见 [`github.com/JuliaPy/SymPy.jl`](https://github.com/JuliaPy/SymPy.jl)），它是 Python `SymPy` 库（参见 [`www.sympy.org/en/index.html`](https://www.sympy.org/en/index.html)）的封装，用于符号计算，即导数、积分、方程（或方程组）等的*解析*求解。

`SymPy` 是一个非常庞大的库，是一个`计算机代数系统`，其功能从基本的符号算术一直涵盖到量子物理学。你将在此看到该库的一般结构，并用来求解上一节中的相同问题，但这次是解析求解。完整的 API 请参考 `SymPy` 文档（[`docs.sympy.org/latest/index.html`](https://docs.sympy.org/latest/index.html)）（其中的大部分功能在 `SymPy.jl` 中都有直接对应）。

#### 10.2.1 加载库并声明符号

变量可以使用 `@var x y z` 语法声明。你还可以声明变量的定义域，例如正数、实数、整数、奇数等。（完整列表参见 [`docs.sympy.org/dev/modules/core.html#module-sympy.core.assumptions`](https://docs.sympy.org/dev/modules/core.html#module-sympy.core.assumptions)）。你可以求解同样的披萨（`qp`）和三明治（`qs`）选择示例，但此处将披萨价格（`pp`）和三明治价格（`ps`）保留为符号形式，以展示所得到的解是解析形式的。稍后才会获得具体的数值结果。我通过强制消耗全部预算来简化示例。这样你就可以使用拉格朗日乘数法来找到解析解（如果你不熟悉此法，不用担心。只需跟随 `SymPy` 的操作，看看如何操作符号方程，并在解析解中用实际数值替换符号后找到数值解即可）。

```
using SymPy
@vars qp qs pp ps positive=true
@vars λ
```

#### 10.2.2 创建并操作表达式

一旦声明了符号，你就可以用它们来创建代数表达式（函数）。请注意，`qp`、`qs` 等现在既是 `Julia` 变量又是 `SymPy.jl` 的“符号”，是对底层 `SymPy` 结构的引用。你所创建的表达式也是如此（不要与 `Julia` 语言的表达式混淆）：

```
julia> typeof(:(qp+qs)) # Julia 语言表达式
Expr

julia> typeof(:(qp))    # Julia 符号
Symbol

julia> typeof(qp+qs)    # SymPy 表达式，即 SymPy 对象
Sym
```

你可以通过将每个约束乘以其相应的“拉格朗日乘数”并添加到目标函数中，来创建所谓的“拉格朗日函数”。然后，你可以使用 `diff(function, variable)` 求出该拉格朗日函数的偏导数：

```
utility = 100*qp - 2*qp² + 70*qs - 2*qs² - 3*qp*qs
budget = pp*qp + ps*qs
lagr = utility + λ*(80 - budget)
dlqp = diff(lagr, qp)
dlqs = diff(lagr, qs)
dlλ  = diff(lagr, λ)
```

如前所述，每个导数都以符号形式表达：

```
julia> dlqp
-pp·λ - 4·qp - 3·qs + 100
```

#### 10.2.3 求解方程组

一阶条件告诉我们，关于 `qp`、`qs` 和 `λ` 的解可以通过将拉格朗日函数的相应导数设为零得到。也就是说，你要求解一个包含三个未知数的三个方程组成的方程组。`solve([equations], [variables])` 正是用于此目的：

```
julia> sol = solve([Eq(dlqp,0), Eq(dlqs,0), Eq(dlλ,0)],
                   [qp, qs, λ]) # 如果其他包中已定义了名为 "solve" 的函数，
                               # 此处即为 SymPy.solve
Dict{Any,Any} with 3 entries:
  λ  => 5*(19*pp - 2*ps - 56)/(2*pp² - 3*pp*ps + 2*ps²)
```