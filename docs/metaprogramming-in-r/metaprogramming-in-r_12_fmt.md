# 后记

至此，你已经了解了从 `R` 程序内部操作 `R` 语言实际构造的各种技术。操作实际语言，进行元编程，为你提供了以多种方式扩展 `R` 的工具。你可以编写用于修改其他函数的函数——就像你计算函数导数时所做的那样——或者你可以编写小型嵌入式领域特定语言来操作或查询数据框，就像在 `dplyr` 和 `ggplot2` 中所做的那样。你知道了如何进行非标准求值，改变表达式的求值方式，以便在不同于通常求值的作用域中对它们进行求值，这通常可以简化函数在管道中的使用方式；但在编写程序时，应谨慎使用此类编程技术，因为很难推理在函数间传递的表达式。

你不应过度使用元编程。阅读你代码的人，大概知道如何阅读 `R` 代码，因此你不应打破他们对代码求值方式的预期。如果你开发了一个领域特定语言并为此使用元编程，这通常没问题；能够阅读你实现的语言表达式的人会知道它们的行为方式，但要谨慎使用元编程。它是一个强大的工具，但如果使用不当，也非常容易让你搬起石头砸自己的脚。

## 索引

- A

  - 访问承诺, `pryr` 包

  - 实际函数参数

    - `call` 函数

    - `deparse` 函数

    - 全局变量

    - `match.call` 函数

    - `plot` 函数

    - `substitute`

  - `alist(...)` 表达式

  - 自动加载环境

  - 自动微分

- B

  - `bquote` 函数

- C

  - 调用函数

    - 访问实际函数参数 (参见 实际函数参数)

    - 访问调用环境中的元素

    - `bind` 函数

    - `bindings$bindings`

    - `delayedAssign`

    - 求值环境

    - `expression` 函数

    - `expression` 函数, 嵌套

    - `parent.frame`

  - 组件, 当前函数

  - 构造函数

- D

  - `delayedAssign` 函数

  - `deparse` 函数

  - `diff_expr` 函数

- E

  - 空环境

  - 环境图

  - 环境

    - 自动加载

    - `base` 包

    - `baseenv()` 环境

    - 调用栈, 父框架环境

    - 链接链

    - `environmentName`

    - 显式创建与表达式求值

    - 函数调用图, 嵌套函数图, 包

  - Depends: graphics

  - Imports: MASS

  - `NAMESPACE`

  - `sd` 函数

  - `stats`

  - `stats::sd` 和 `base::ls`

  - Suggests: 导入的函数

  - 操作

  - `new.env` 函数

  - `parent.frame` 函数

  - `search` 函数

- 求值环境

- 表达式

  - 访问与操作

  - 控制结构

  - 函数调用中的自动微分

  - 简化

  - 参数

  - `bquote`

  - 字符串

  - 分割与导出

  - `expr[[1]]`

  - 函数调用

  - 函数符号

  - 处理调用

  - 乘法

  - 命名函数

  - `quote(-simplified)`

  - `simplify_call` 函数

- F, G, H, I, J, K, L

  - 形参

- 函数参数

  - 函数

    - 实际参数 (参见 实际函数参数)

    - 调用组件, 当前函数

    - 环境

    - 函数形参

    - `parent.env`

    - `sys.function`

  - 构造

  - `delayedAssign`

  - 操作

  - 环境

  - `eval` 函数

  - 形参

  - 函数体

    - 修改

  - M

  - `make_macro`

  - `match.call` 函数

  - 修改函数

- N, O

  - 非标准求值 (`NSE`)

    - 显式求值表达式

    - 函数内部使用 `substitute`

    - 修改环境

    - `my_with`

    - 与函数一起使用 `substitute`

  - 编写宏

- P

  - `parse` 函数

  - `pryr` 包

- Q

  - `Quote` 函数

  - 引号表达式

- R

  - 递归函数

- S, T, U

  - `search` 函数

  - 替换

    - `deparse(substitute(x))`

    - 表达式, 变量

    - 函数参数隐式/显式

    - `substitute` 函数

    - 变量

- V, W, X, Y, Z

  - 逐字表达式