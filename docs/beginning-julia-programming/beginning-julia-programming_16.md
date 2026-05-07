# 14. 元编程

## 14.1 引言

元编程是 Julia 编程语言最强大的特性之一。粗略地说，元编程是关于 Julia 代码控制源文件的其他部分，其程度可以达到修改它们并控制其执行。要理解这个过程，你必须理解 Julia 代码的执行方式。广义上讲，Julia 代码执行有两个阶段：

- 生成抽象语法树  
  Julia 代码被解析成适合求值的形式。
- 求值  
  编译器执行解析后的代码。

元编程是关于在代码被解析之后但在执行之前对其进行修改。这一特性被证明非常有用，因为你可以在代码被解析后但在执行前进行修改。你可以编写简短的代码段，这些代码段可以执行根据给定规则编写更大代码段的繁琐工作。



## 14.2 `:` 运算符

每个 Julia 任务都被视为一个表达式。REPL 会计算这些表达式。要执行元编程，你必须能够阻止 Julia 计算表达式。这可以通过 `:` 运算符实现。让我们在以下 Julia 代码中查看其用法：

```
julia> a = "Hello"
"Hello"
julia> :a
:a
julia> b = "1.0"
"1.0"
julia> :b
:b
julia> :(3.5²)
:(3.5 ^ 2)
```

输出显示为 `:a`、`:b` 和 `:(3.5 ^ 2)`。这些被称为符号。这些符号是未经计算的代码片段，可供修改。

符号也可以通过将表达式包裹在 `quote` 和 `end` 关键字中来形成。以下示例演示了这一概念：

```
julia> a = quote
       3.5²
       end
quote # REPL[437], line 2:
    3.5 ^ 2
end
julia> typeof(a)
Expr
julia> eval(a) 12.25
julia> 3.5² 12.25
julia> :(3.5²)
:(3.5 ^ 2)
```

变量 `a` 存储了一个 `Expr`（表达式）对象。这可以通过 `eval()` 函数进行计算。两种定义符号的写法 `:(3.5 ^ 2)` 都可以使用。`quote` 和 `end` 关键字用于多行用法，而 `:` 运算符通常用于单行用法。

## 14.3 表达式

让我们通过 Julia 探查一个表达式。为此，我们先构造一个简单的表达式，用于计算直角三角形的斜边，假设其垂直边为 2 个单位，底边为 3 个单位。

```
julia> A = quote
       p=3
       b=4
       h=sqrt(p²+b²)
       end
quote  # REPL[442], line 2:
    p = 3 # REPL[442], line 3:
    b = 4 # REPL[442], line 4:
    h = sqrt(p ^ 2 + b ^ 2)
end
julia> eval(A) # 计算 A
5.0
```

这个表达式将在后续用于探查其属性和构造。

### 14.3.1 `fieldnames()` 和 `dump()`

`fieldnames()` 函数可用于查看存储在 `A` 中的这个表达式的结构。完整的结构，即抽象语法树，可以通过 `dump()` 函数获得。

```
julia> fieldnames(A)
3-element Array{Symbol,1}:
 :head
 :args
 :typ
julia> dump(A)
Expr
  head: Symbol block
  args: Array{Any}((6,))
    1: Expr
      head: Symbol line
      args: Array{Any}((2,))
        1: Int64 2
        2: Symbol REPL[442]
      typ: Any
    2: Expr
      head: Symbol =
      args: Array{Any}((2,))
        1: Symbol p
        2: Int64 3
      typ: Any
    3: Expr
      head: Symbol line
      args: Array{Any}((2,))
        1: Int64 3
        2: Symbol REPL[442]
      typ: Any
    4: Expr
      head: Symbol =
      args: Array{Any}((2,))
        1: Symbol b
        2: Int64 4
      typ: Any
    5: Expr
      head: Symbol line
      args: Array{Any}((2,))
        1: Int64 4
        2: Symbol REPL[442]
      typ: Any
    6: Expr
      head: Symbol =
      args: Array{Any}((2,))
        1: Symbol h
        2: Expr
          head: Symbol call
          args: Array{Any}((2,))
            1: Symbol sqrt
            2: Expr
              head: Symbol call
              args: Array{Any}((3,))
                1: Symbol +
                2: Expr
                3: Expr
              typ: Any
          typ: Any
      typ: Any
  typ: Any
```

输出是一个表达式数组，这些表达式是存储在变量名 `A` 中的原始表达式的子表达式。每个子表达式都有一个 `head`、`Expr` 和 `typ`，它们定义了一个代码块、其子表达式以及包含的类型。这就是抽象语法树（AST）。让我们尝试探查 `Symbol block` 的参数：

```
julia> A.args[1]
:( # REPL[442], line 2:)
julia> A.args[2]
:(p = 3)
julia> A.args[3]
:( # REPL[442], line 3:)
julia> A.args[4]
:(b = 4)
julia> A.args[5]
:( # REPL[442], line 4:)
julia> A.args[6]
:(h = sqrt(p ^ 2 + b ^ 2))
```

或者，可以使用 `for` 循环来打印 `args` 列表：

```
julia> for (n, expr) in enumerate(A.args)
           println(n,  ":  ",  expr)
       end
1:  # REPL[442], line 2:
2: p = 3
3:  # REPL[442], line 3:
4: b = 4
5:  # REPL[442], line 4:
6: h = sqrt(p ^ 2 + b ^ 2)
```

现在，让我们编辑 `A.args`（它存储了表达式 `:(h=sqrt(p²+b²))`），将其更改为另一个表达式 `:(multiply = p * b)`：

```
julia> eval(A) # 编辑前
5.0
julia> A.args[end]= :(multiply=p*b)
:(multiply = p * b)
julia> eval(A) # 编辑后

```

这是一个简单的元编程演示，其中 Julia 代码的子表达式被编辑，以计算完全不同的内容，而无需为此编写代码。

## 14.4 表达式插值

为了以更简单的方式编写表达式，你可以在表达式中使用 `$` 插值运算符，就像我们在字符串中学到的那样（参见第 9 章第 9.9 节）。例如，如果我们设置 `x=-2` 和 `y=2`，那么表达式 `expr=:(x^y)` 返回 `:(x^y)`，而 `expr1=:($x^y)` 将返回 `:(-2 ^ y)`，`expr2=:(x^$y)` 将返回 `:(x ^ 2)`。所有这些计算结果都相同，为 4。这在以下 Julia 代码中显示：

```
julia> x=-2

julia> y=2

julia> expr=:(x^y)
:(x ^ y)
julia> expr1=:($x^y)
:(-2 ^ y)
julia> expr2=:(x^$y)
:(x ^ 2)
julia> eval(expr)

julia> eval(expr1)

julia> eval(expr2)

```

这种插值的一个重要特性是，表达式求值在解析时进行，而其他插值仅在解析后调用 `eval()` 函数时才求值。

## 14.5 宏

使用 `宏`，你可以从未经计算的输入表达式生成一个新的输出表达式。这些表达式在解析时进行求值，并返回一个未经计算的表达式。它们类似于函数，但不同之处在于它们将输入表达式映射到输出表达式。

宏的语法如下：

```
macro name_of_macro
    # 宏的主体
end
```

宏的调用方式是在名称前放置一个 `@` 且不带空格，然后传递一个表达式，例如：

```
julia> @name_of_macro expr1, expr2
### 另一种调用方式
julia> @name_of_macro(expr1, expr2)
```

让我们通过以下 Julia 代码来理解这种定义和用法形式。这里定义了一个名为 `expFeatures` 的宏，它打印表达式的 `args`、`head` 和 `typ`。它还会计算表达式并返回计算后的值。

```
julia> macro expFeatures(expression)
           if typeof(expression)==Expr
               println(expression.args)
               println(expression.head)
               println(expression.typ)
           end
           answer=eval(expression)
           return answer
       end
@expFeatures (macro with 1 method)
julia> @expFeatures 3+4-5
Any[:-, :(3 + 4), 5]
Call
Any

julia> @expFeatures (3+4)-(5²)
Any[:-, :(3 + 4), :(5 ^ 2)]
Call
Any

julia> @expFeatures sin(90), sind(90)
Any[:(sin(90)), :(sind(90))]
Tuple
Any
(0.8939966636005579, 1.0)
```

当向宏 `@expFeatures` 输入表达式 `3+4-5` 时，发现 `args` 是 `Any[:-, :(3 + 4), 5]`，`head` 是 `call`，`typ` 是 `Any`。同时该表达式被计算为 2。类似地，可以向该宏输入其他表达式来研究其特征。在最后一次尝试中，输入了两个表达式（`sin(90)` 和 `sind(90)`），用逗号分隔（这就是为什么 `head` 变成 `tuple` 的原因），并且相应地完成了计算。

## 14.6 内置宏

Julia 编译器预定义了许多宏。这里将讨论其中一些。最常用的宏之一是用于计算代码执行时间的宏。对表达式使用 `@time`，你可以获得以下信息：

*   一个用于执行表达式的宏
*   执行所花费的时间
*   内存分配次数
*   其执行导致分配的总字节数
*   返回表达式的值

```
julia> x=1:10e4;
julia> @time x.³
  1.024123 seconds (63.48 k allocations: 4.167 MiB)
100000-element Array{Float64,1}:
 1.0
 8.0
 27.0
 64.0
 125.0
 216.0
 343.0
 512.0
 729.0
 1000.0
   .
   .
   .
 9.9976e14
 9.9979e14
 9.9982e14
 9.9985e14
 9.9988e14
 9.9991e14
 9.9994e14
 9.9997e14
 1.0e15
```

这显示执行耗时 1.024123 秒，进行了 63.48K 次分配，占用了 4.167 MiB 内存；然后显示了每个数组元素立方值的部分结果。用户还可以尝试使用 `@timev`、`@timed`、`@elapsed` 和 `@allocated`，并通过 `help?` 模式检查它们的用法。



## 14.7 总结

在本章中，我们介绍了符号、表达式、表达式插值和宏的概念，从而引入了 Julia 中的元编程概念。在 Julia 中拥有方便的元编程方式是其对开发者最具吸引力的特性之一。许多宏也以类似的方式被开发并发布。学会编写实用的宏并明智地使用它们，被认为是 Julia 程序员的关键技能。

