# 例如：`/usr/lib/R/`

```julia
using Pkg
Pkg.build("RCall")
```

## 第 7 章 将 Julia 与其他语言交互

##### 7.4.1 交互式 R 命令提示符

你可以通过美元符号 `$` 从 Julia 中访问嵌入的 R 命令提示符（然后按 Backspace 键返回 Julia REPL）。你还可以在这个嵌入的 R 命令提示符中再次使用美元符号或 `@rput` 宏来访问 Julia 符号或表达式。反之，你也可以使用 `@rget` 宏将 R 变量取回 Julia：

```julia
julia> a = [1,2]; b = 3
```

```julia
julia> @rput a # 将 `a` 变量及其绑定的对象显式传输到 R
```

```
R> c = a + $b # 在 R 中访问 a，并通过美元符号访问 b
```

```julia
julia> @rget c # 将 `c` 变量及其绑定的对象显式传输到 Julia
julia> c # [4,5]
```

你无需转换数据，因为 RCall 支持与基本 Julia 类型（包括数组和字典）以及流行的表格数据格式（如 `DataFrames`）之间的自动转换。

##### 7.4.2 在 Julia 程序中嵌入 R 代码

在 Julia 程序中嵌入 R 代码与你之前看到的 Python 类似。你可以使用 `R"..."` 字符串宏来定义和调用 R 函数，并且在函数调用中可以直接使用你的 Julia 数据：

```julia
using RCall
```

```julia
R"""
sumMyArgs <- function(i,j) i+j
getNElement <- function(n) {
    a <- c(0,1,2,3,4,5,6,7,8,9)
    return(a[n])
}
"""
```

```julia
i = [3,4]
a = rcopy(R"sumMyArgs"(3,4)) # 7
b = rcopy(R"sumMyArgs"(i,[5,6])) # [8,10]
b = rcopy(R"sumMyArgs($i,c(5,6))") # [8.0,10.0]（另一种写法）
c = rcopy(R"sumMyArgs"([3,4],5)) # [8,9]
d = rcopy(R"getNElement"(1)) # 0
```

与 `PyCall` 的一个显著区别是，通过 `R"..."` 定义的 R 函数调用结果尚未转换为可用的 Julia 对象，而是保持为 `RObjects` 类型。为了将 `RObjects` 转换为标准的 Julia 对象，你可以使用 `rcopy` 函数；如果你想强制进行特定类型的转换，还可以依赖 `convert`：

```julia
convert(Array{Float64}, R"sumMyArgs"(i,[5,6])) # [8.0,9.0]
```

```julia
convert(Array{Int64}, R"sumMyArgs($i,c(5,6))") # [8,9]
```

最后，在使用 R 时，你无需担心索引约定，因为 R 和 Julia 都采用基于 1 的数组索引。

##### 7.4.3 使用 R 库

虽然 RCall 提供了两个专门的宏（`@rlibrary` 和 `@rimport`）来处理 R 库，但它们的效率不高，因为数据会在 R 和 Julia 之间被多次复制。

请直接使用 `R"..."` 字符串宏：

```julia
mydf = DataFrame(deposit = ["London","Paris","New-York","Hong-Kong"]; q = [3,2,5,3]) # 在 Julia 中创建一个 DataFrame（一种具有命名列的表结构）
```

```julia
R"""
install.packages('ggplot2', repos='http://cran.us.r-project.org')
library(ggplot2)
ggplot($mydf, aes(x = q)) +
    geom_histogram(binwidth=1)
"""
```

**注意：** 在 MacOS 系统中，你可能需要 *Xquartz 窗口服务器*（请参阅 [`www.xquartz.org`](https://www.xquartz.org)）来显示图表。

##### 7.4.4 JuliaCall：在 R 中使用 Julia

反过来，若要在 R 工作流中嵌入 Julia 代码，你可以使用名为 `JuliaCall` 的 R 包（请参阅 [`github.com/Non-Contradiction/JuliaCall`](https://github.com/Non-Contradiction/JuliaCall)）。

**安装**

```r
> install.packages("JuliaCall")
```

就是这样。

**用法**

```r
> library(JuliaCall)
> julia_setup()
```

请注意，与 `PyJulia` 不同，“设置”函数需要在每次启动新的 R 会话时调用，而不仅仅是在安装 `JuliaCall` 包时调用。如果你的系统路径中没有包含 `julia`，或者你有多个版本并希望指定要使用的版本，可以将 `JULIA_HOME = "/path/to/julia/binary/executable/directory"` 参数（例如，`JULIA_HOME = "/home/myUser/lib/julia-1.1.0/bin"`）传递给 `julia_setup` 调用。

`JuliaCall` 依赖于一些内容（比如对象之间的转换）。


