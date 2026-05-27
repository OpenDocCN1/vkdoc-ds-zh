# `..rather than..`（意为“...而不是...”）

```
struct MyType

end
```

## 按列优先再按行循环遍历矩阵元素

矩阵数据在内存中是以各列数组首尾相连的方式存储的（`列优先`顺序），即先存储第一列，接着是第二列，以此类推。

因此，先按列再按行循环遍历矩阵（以便连续读取值）的速度更快：

```
M = rand(2000,2000)
```

```
function sumRowCol(M) # 较慢
    s = 0.0
    for r in 1:size(M)[1]
        for c in 1:size(M)[2]
            s += M[r,c]
        end
    end
    return s
end

function sumColRow(M) # 较快
    s = 0.0
    for c in 1:size(M)[2]
        for r in 1:size(M)[1]
            s += M[r,c]
        end
    end
    return s
end
```

```
@benchmark sumRowCol(M) # 中位时间: 21.684 ms
@benchmark sumColRow(M) # 中位时间: 4.787 ms
```

## 8.2 代码并行化

Julia 提供了核心功能来促进代码并行化，既可以使用多线程，也可以使用多进程。线程共享同一内存（因此无需在多个线程之间复制数据），但受限于在同一 CPU 上运行。此外，很难“保证”安全的多线程操作。Julia 中的线程支持仍处于实验阶段，尽管其开发进展迅速 2。

进程各自拥有独立的内存（因此数据需要在进程之间复制，这代表了需要与并行执行带来的优势相平衡的计算成本），并且可以分布在多个 CPU 甚至多台机器上（例如，通过 SSH 连接）。由于 Julia 中的多进程并行化已经成熟且稳定，在本节的剩余部分，我将讨论它。你看到的所有函数都是 `Distributed` 标准库包的一部分。

### 8.2.1 添加和移除进程

进程可以随时添加和移除：

```
wksIDs = addprocs(3) # 2,3,4
```

```
println("工作进程 pid: ")
for pid in workers()
    println(pid) # 2,3,4
end

rmprocs(wksIDs[2]) # 或 rmprocs(workers()[2]) 移除进程 pid 3

println("工作进程 pid: ")
for pid in workers()
    println(pid) # 2,4 剩余
end

@everywhere println(myid()) # 2,4
```

第一行添加了三个进程。在此例中，它们被添加到运行主 Julia 进程的同一台机器上，但可以在 `addprocs()` 中直接指定 SSH 连接详情，以便将进程添加到其他机器（这些机器也必须安装 Julia）。

通常，让 `n` 等于机器上可用的核心数是合理的。

也可以使用命令行标志 `-p`（例如，`./julia -p 2`）直接启动带有多个进程的 Julia。`-p` 参数会隐式加载 `Distributed` 模块。

在此示例中，新进程的“内部 ID”（不同于操作系统分配的 pid）被保存在数组 `wksIDs` 中，尽管你并不一定需要它，因为调用 `workers()` 也会返回该数组。

主 Julia 进程（例如，提供交互式提示符的进程）的 pid 始终为 1，并且除非它是唯一进程，否则它不被视为工作进程。

然后，你可以检查活动的工作进程，并通过 `rmprocs(pid)` 移除不再需要的进程。

最后，你可以使用函数 `myid()` 返回进程的 PID，并使用 `@everywhere` 宏将该函数传播到所有工作进程并打印。

### 8.2.2 在项目列表上运行繁重计算

本节考虑两种常见的并行化模式。

第一种模式涉及对一个项目列表执行计算开销很大的操作，并且你对每个项目的结果都感兴趣。关键在于，每个项目的计算是独立于其他项目的。

例如，你将重用之前见过的 `fib` 函数。首先需要确保所有进程（假设你已使用 `addprocs(3)` 添加了三个工作进程）都知道你希望它们使用的函数。你可以在函数定义前使用 `@everywhere` 宏，以便将该函数传输给所有工作进程（如果你在后续阶段添加更多进程，它们将无法访问该函数）。如果你需要在工作线程中调用多个函数（或者需要调用多个子函数），一个方便的选择是将它们及其所需的全局变量定义在一个以 `@everywhere` 宏为前缀的 `begin` 块中（即 `@everywhere begin [共享函数定义] end`），或者将它们放入一个文件并运行 `@everywhere include("computationalcode.jl")`。

然后，你可以创建输入数据数组，并使用 `pmap(op, inputData)` 对每个元素运行繁重操作：

```
a = rand(1:35,100)
```

```
@everywhere function fib(n)
    if n == 0 return 0 end
    if n == 1 return 1 end
    return fib(n-1) + fib(n-2)
end

@benchmark results = map(fib, a)      # 串行: 中位时间: 490.473 ms
@benchmark results = pmap(fib, a)     # 并行: 中位时间: 249.295 ms
```

`pmap` 函数会自动动态地选取“空闲”进程，为其分配任务，并将结果合并到返回的数组中。只有函数的计算部分在工作进程中执行。当函数的计算成本很高时（例如本例），`pmap` 非常方便。但请注意，即使你使用了三个工作线程，计算时间的缩减系数也远低于三 3。

### 8.2.3 聚合结果

第二种情况是，你想要对每个项目执行一个小型操作，但同时希望最后执行一个“聚合函数”来获取一个标量值（如果输入是矩阵，则获取一个数组）。

在这些情况下，你可以使用 `@distributed (aggregationFunction) for` 结构。例如，你并行执行除以 2 的操作，然后使用 `sum` 作为聚合函数（假设有三个可用的工作进程）：

```
function f(n)
    s = 0.0
    for i = 1:n
        s += i/2
    end
    return s
end
```

```
function pf(n)
    s = @distributed (+) for i = 1:n    # 使用变量 s 上的 sum 进行聚合
        i/2                             # for 循环的最后一个元素被聚合器使用
    end
    return s
end

@benchmark f(10000000)      # 中位时间: 11.478 ms
@benchmark pf(10000000)     # 中位时间: 4.458 ms
```

所展示的两种案例是代码并行化中最常见的几种情况。Julia 提供了相当完整的高层和底层并行化函数列表，一些外部包，如 `DistributedArrays`（参见 [`github.com/JuliaParallel/DistributedArrays.jl`](https://github.com/JuliaParallel/DistributedArrays.jl)），有助于以相对简单的方式在许多情况下实现高效的并行化代码。

值得注意的是，Julia 中的并行计算可以扩展到在 GPU（图形处理单元）而非 CPU 上运行。这个主题超出了本书的范围，但你可以查阅官方文档（参见 [`docs.julialang.org/en/v1/manual/parallel-computing/index.html`](https://docs.julialang.org/en/v1/manual/parallel-computing/index.html)），其中提供了几个提供底层和高级接口的包。

## 8.3 调试

`调试`，通常是指“消除计算机程序中错误或故障的过程”（来自 Merriam-Webster 词典）。

在编译型语言中调试要困难得多，这促使了许多复杂、专用工具的开发以帮助调试程序。

Julia 的交互式和内省特性使得调试相对简单，因为程序员可以轻松地分析并在任何步骤更改代码。尽管如此，专门的调试工具已经被开发出来，用于帮助调试函数内部的代码，否则这些代码的各个组成部分将难以分析。

### 8.3.1 内省工具

`内省`是程序检查自身的能力，特别是`类型内省`是程序在运行时检查对象类型或属性的能力。

广义上讲，第一个“内省”工具是工作区（Workspace）面板，它在多个 Julia 开发环境中都有提供（图 8-2 显示了 Juno 中可用的面板）。它显示了当前作用域内所有标识符（变量、函数等）以及绑定到它们的对象。

*图 8-2. Juno IDE 中的工作区面板*

此外，Julia 提供了以下内省函数：

- `methods(myfunction)`：检索所有函数签名，即函数在被调用时接受的所有不同类型的参数。

- `@which myfunction(myargs)`：发现特定调用中会使用哪个方法（在可用的多个方法中，因为 Julia 支持多重分派）。

- `typeof(a)`：发现给定对象的类型。

- `eltype(a)`：发现给定对象内部元素的类型，例如 `typeof(ones(Int64,10))` 返回 `Array{Int64,1}`，而 `eltype(ones(Int64,10))` 返回 `Int64`。

- `fieldnames(aType)`：发现给定类型包含哪些字段。

- `dump(myobj)`：获取关于给定对象的详细信息。

## 8.3.2 调试工具

Julia 提供了一个功能完备的调试器栈。基础功能由 `JuliaInterpreter.jl` 包（参见 [`github.com/JuliaDebug/JuliaInterpreter.jl`](https://github.com/JuliaDebug/JuliaInterpreter.jl)）提供，而用户界面则由命令行包 `Debugger.jl`（[`github.com/JuliaDebug/Debugger.jl`](https://github.com/JuliaDebug/Debugger.jl)）和 `Rebugger.jl`（[`github.com/timholy/Rebugger.jl`](https://github.com/timholy/Rebugger.jl)）或 Juno IDE（[`junolab.org/`](https://junolab.org/)）提供。

由于这些功能类似，并且使用图形用户界面可能更方便，我们只讨论 Juno 中调试器的集成。Juno 集成的调试器允许开发者在任意点检查源代码并设置断点，断点是执行暂停的点，以便程序员检查状态、更改代码或继续执行。

让我们重用 `fib` 函数并为其打开一个调试会话（见图 8-3）。

*图 8-3. Juno IDE 中的调试会话*

你可以通过在 Juno REPL 中输入 `Juno.@enter function(args)` 来启动调试会话。在这种情况下，你可以使用 `Juno.@enter fib(10)`。如果尚未可见，启动调试会话将打开特定的调试器面板（如图 8-3 右侧所示）。REPL 提示符也会变为 `debug>`。

从图 8-3 中，你可以看到你使用了调试提示符来打印变量 `n` 的值（但你从左侧的工作区面板已经知道），并且最有趣的是，动态地更改了它的值。你用它来运行一个奇怪的斐波那契数列，跳过了从 8 到 6 的递归。

点击主编辑器面板中行号的左侧将切换该行的断点（一个红点）。断点列在调试器面板的底部。它们可以被禁用（这样就不再活动），或者可以被编辑，使其仅在满足特定条件时适用（此处是当 `n` 小于或等于 4 时）。红点将变为蓝色，表示这是一个条件断点。

在调试器面板的顶部是调试会话的图形控制。从左到右，这些控制是：

- *继续（Continue）：* 继续执行，直到调试的函数终止或遇到有效的断点。`Juno.@run function(args)` 是 `Juno.@enter function(args)` 后接按下 Continue 的快捷方式。

- *完成函数（Finish Function）：* 继续执行，直到当前函数退出或遇到有效的断点。

- *下一行（Next Line）：* 评估当前行，并在评估下一行之前停止。

- *执行到选中行（Step to Selected Line）：* 继续执行，直到选中的行或遇到有效的断点。

- *下一个表达式（Next Expression）：* 评估下一个表达式。仅当同一行定义了多个表达式时，才与 Next Line 有所不同。

- *步入函数（Step Into Function）：* 执行下一个函数调用，并在子函数的开头停止（例如，在图 8-3 中，它将调用 `fib(2)` 并在其起始处暂停）。即将被调用的函数显示在控件下方。

- *停止调试（Stop Debugging）：* 终止调试会话并将控制权交给正常的 REPL。

最后，请注意调试器面板中间的调用堆栈（Callstack）会话：它提供了导致当前状态的调用链的可点击历史记录。

## 8.4 管理运行时错误（异常）

与其他许多语言一样，运行时错误可以通过 `try/catch` 块来处理：

```
try

end
```