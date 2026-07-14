### 第 1 章：入门指南

#### 1.1 为什么选择 Julia？

有这么多的编程语言可用，为什么还要创造另一种呢？为什么要花时间学习 Julia？值得吗？

使用 Julia 的主要论点之一是其有助于改善编程中长期存在的一个权衡——快速编码与快速执行。

一方面，Julia 允许开发者使用类似于 Python、R 或 MATLAB 的动态、高级语言进行编码，与代码交互并具有强大的表达能力（例如，参见第六章）。

另一方面，只需付出最少的努力，开发者就可以使用 Julia 编写出运行速度（几乎）与用 C 或 FORTRAN 编写的程序一样快的程序。

但是，优化现有的、具有大量库和成熟生态系统的语言，而不是从头开始创建一种新语言，不是更好吗？

嗯，是也不是。改善动态语言运行时执行的尝试有很多。PyPy ([`pypy.org`](https://pypy.org))、Cython ([`cython.org`](https://cython.org)) 和 Numba ([`numba.pydata.org`](https://numba.pydata.org)) 是 Python 编程语言的三个显著例子。它们都与一个事实相冲突：Python（以及一般所有当前的动态语言）是在近期即时编译器发展之前设计的，因此它提供的特性不易优化。这些优化工具要么失败，要么需要复杂的工作才能运行。

相反，Julia 从一开始就被设计为与即时编译器一起工作，并且其语言特性——及其内部实现——都经过仔细考虑，以便在尊重编译器约束的同时，为程序员提供现代语言所期望的生产力。结果是，符合 Julia 规范的代码保证能与底层的即时编译器一起工作，最终生成高度优化的编译代码。

**使用新语言的隐性成本**

使用新语言的主要“成本”在于学习语言本身，以及不得不放弃你习惯使用的有用库和功能丰富的开发编辑器。诚然，这是事实，但在 Julia 的情况下，这些成本被几个因素所缓解：

- 该语言在语法上被设计为类似于主流语言（你将在本书中看到！）。如果你已经掌握了一门编程语言，你很可能会对 Julia 的语法感到得心应手。

- Julia 允许你轻松地将代码与所有主流编程语言进行接口对接（参见第七章“Julia 与其他语言的接口”），从而重用它们庞大的库集（当这些库尚未移植到 Julia 时）。

## 1.2 安装 Julia

除了在运行时性能上超越传统高级动态语言之外，Julia 从头创建的事实意味着它使用了最佳、最现代的技术，无需担心与现有代码或内部架构的兼容性问题。

你可能喜欢的一些 Julia 特性包括：内置的基于 Git 的包管理器、完整的代码内省、多重分派、核心内的高层并行计算方法，以及在变量名中使用 Unicode 字符（例如希腊字母）。

凭借其计算优势，Julia 在科学、高性能编程领域有着天然的根基，但作为通用编程语言，它也变得越来越成熟。这就是为什么本书并非专门关注数学领域，而是开发了一套广泛的、简单的、基础性的示例，即使是初级程序员也能理解。

### 1.2.1 获取 Julia

可以通过安装适用于你系统的 Julia 二进制文件来运行 Julia 代码，这些二进制文件可在 Julia 项目网站 (`julialang.org`) 的下载区 (`julialang.org/downloads/`) 获取。

这些二进制文件附带了一个 Julia 解释器控制台（即“REPL”——读取、求值、打印、循环），你可以在其中以命令行方式运行 Julia 代码。

为了获得更好的体验，请查看集成开发环境，例如基于 Atom (`atom.io`) 文本编辑器的 IDE Juno (`junolab.org/`)，或 Julia Jupyter (`jupyter.org/`) 后端 IJulia (`github.com/JuliaLang/IJulia.jl`)。

详细的设置说明可以在它们各自的网站上找到，但简而言之，步骤相当直接。

**对于 Juno:**

1. 首先安装主要的 Julia 二进制文件。
2. 下载、安装并打开 Atom 文本编辑器 (`atom.io`)。
3. 在 Atom 中，转到“设置” ➤ “安装”面板。
4. 在搜索框中输入 `uber-juno` 并按回车键。点击同名包上的“安装”按钮。

**对于 IJulia:**

1. 首先安装主要的 Julia 二进制文件。
2. 使用你操作系统喜欢的工具（例如 Linux 中的包管理器、Python 的 `pip` 包管理器或 Anaconda 发行版）安装基于 Python 的 Jupyter Notebook 服务器。
3. 在 Julia 控制台中，键入 `using Pkg; Pkg.update(); Pkg.add("IJulia"); Pkg.build("IJulia")`。
4. 现在 IJulia 内核已安装。只需启动 notebook 服务器并使用浏览器访问它。

你也可以选择（至少开始时）完全不安装 Julia，而是尝试一些支持 Julia 的在线计算环境，比如 JuliaBox (`juliabox.com/`)、CoCalc (`cocalc.com/doc/software-julia.html`)、Nextjournal (`nextjournal.com`) 和 Binder (`mybinder.org`)。

### 1.2.2 Juno 和 IJulia 的一些技巧

**Juno 可以：**

*   使用 `Ctrl+Shift+Down` 启用块选择模式。
*   通过选择代码并选择 `运行 Block` 或键入 `Ctrl+Enter`（Windows 和 Linux）或 `Cmd+Enter`（Mac）来运行选中的代码。
*   使用 `Ctrl+/`（Windows 和 Linux）或 `Cmd+/`（Mac）注释/取消注释代码块。

**IJulia:**

*   查看 `帮助 ➤ 键盘快捷键` 中提供的许多键盘快捷键。
*   需要为团队或班级在计算环境中运行 Julia？使用 JupyterHub (`github.com/jupyterhub/jupyterhub`)，这是基于 Jupyter 的多用户解决方案。

## 1.3 运行 Julia

根据你的需求，有多种方式可以运行 Julia 代码：

1.  Julia 可以在控制台中交互式运行。启动 `julia` 以获得 REPL 控制台，然后在那里输入命令（完成后键入 `exit()` 或使用 `CTRL+D`）。
2.  创建一个脚本，即一个以 `.jl` 结尾的文本文件，然后让 Julia 使用 `julia myscript.jl [arg1, arg2,..]` 解析并运行它。脚本文件也可以在 Julia 控制台中运行。只需键入 `include("myscript.jl")`。
3.  在 Linux 或 macOS 上，你可以改为在脚本顶部添加系统上 Julia 解释器的位置，前面加上 `#!` 并在其后跟一个空行，例如 `#!/usr/bin/julia`（你可以通过在控制台中键入 `which julia` 找到 Julia 解释器的完整路径）。确保文件是可执行的（例如，`chmod +x myscript.jl`）。然后你可以使用 `./myscript.jl` 运行脚本。
4.  使用集成开发环境（例如前面提到的那些），打开一个 Julia 脚本，并使用编辑器特定的运行命令。

你可以定义一个全局的（针对计算机的所有用户）和本地的（针对单个用户）Julia 文件，这些文件将在每次启动时执行，你可以在其中定义例如应该始终可用的函数或变量。这两个文件的位置如下：

*   全局 Julia 启动文件：`[JULIA_INSTALL_FOLDER]\etc\julia\startup.jl`（其中 `JULIA_INSTALL_FOLDER` 是 Julia 的安装位置）
*   本地 Julia 启动文件：`[USER_HOME_FOLDER]\.julia\config\startup.jl`（其中 `USER_HOME_FOLDER` 是本地用户的主文件夹，例如 Windows 中的 `%HOMEPATH%` 和 Linux 中的 `~`）

请记住在 Linux 中使用正斜杠 (`/`)。请注意，本地 config 文件夹可能不存在。在这种情况下，只需创建 `config` 文件夹作为 `.julia` 的子文件夹，并在那里启动新的 `startup.jl` 文件。

Julia 将所有在同一工作会话中创建的对象保存在内存中。你可能有时想要释放内存或通过删除不再需要的对象来“清理”会话。如果你想这样做，只需重启 Julia 会话（你可能想使用第三章末尾提到的技巧）或使用 `Revise.jl` (`github.com/timholy/Revise.jl`) 包进行更精细的控制。

你可以使用 `versioninfo()` 选项（在 Julia 会话中）确定你正在使用的 Julia 版本。

## 1.4 杂项语法元素

Julia 支持单行 (`#`) 和多行 (`#= [...] =#`) 注释。多行注释可以嵌套并出现在行的任何位置：

```julia
println("Some code..")

#=
Multiline comment
#= nested multiline comment =#
Still a comment
=#

println(#= A comment in the middle of the line =# "This is a code") # Normal single-line comment
```

你不需要使用分号来表示语句的结束。如果使用了分号，它将抑制命令的输出（在脚本模式下会自动完成）。如果在 REPL 中单独使用分号，它允许你切换到操作系统命令 shell 提示符，以启动系统范围的命令。

块不需要用括号括起来，但它们在结束处确实需要关键字 `end`。

---
**可用的开发环境**——例如 Juno (`junolab.org`)、IJulia 朱庇特内核 (`github.com/JuliaLang/IJulia.jl`) 和 VSCode Julia 插件 (`github.com/JuliaEditorSupport/julia-vscode`) ——坦白说相当酷，已经实现了许多常见功能。它们使你在第一次使用 Julia 编码时就能高效工作。

虽然缩进在语言中没有任何功能意义，但空白有时很重要。例如，函数调用必须将输入严格附加在函数名后面的括号中，例如：

```text

```

# 将 `a[2]` 重新绑定到另一个对象。

# 同时改变对象 `a`：

```
julia> a[2] = 40 # 40
julia> b # [[[1, 2], 3], 40]
julia> c # [[[1, 2], 3], 4]
julia> d # [[[1, 2], 3], 4]
```


# 重新绑定 `a[1][2]`，同时


# 改变 `a` 和 `a[1]`：

```
julia> `a[1][2]` = 30 # 30
julia> `b` # [[[1, 2], 30], 40]
julia> `c` # [[[1, 2], 30], 4]
julia> `d` # [[[1, 2], 3], 4]
```

# 重新绑定 `a[1][1][2]`，同时

# 改变 `a`、`a[1]` 和 `a[1][1]`：

```julia
julia> a[1][1][2] = 20 # 20
julia> b # [[[1, 20], 30], 40]
julia> c # [[[1, 20], 30], 4]
julia> d # [[[1, 2], 3], 4]
```

# 重新绑定 a：

`julia` `a = 5` # `5`

`julia` `b` # `[[[1, 20], 30], 40]`

`julia` `c` # `[[[1, 20], 30], 4]`

`julia` `d` # `[[[1, 2], 3], 4]`

