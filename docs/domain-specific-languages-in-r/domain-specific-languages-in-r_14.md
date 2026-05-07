# 12. 动态规划

作为另一个例子，我们将创建一个用于定义动态规划算法的领域特定语言。动态规划是一种用于加速递归计算的技术。当我们需要通过递归计算一个量，而该递归会拆分为其他递归，并且这些递归存在重叠时（即计算过程需要多次求值相同的递归调用），我们可以通过记忆递归调用的结果来加速计算。如果我们能预先知道需要计算的值，我们可以从基本情况开始，逐步构建这些值的表格，直到得到最终值，而不是从顶层递归向下计算（后者需要更多的簿记工作来记忆结果）。

以一个经典的例子，如斐波那契数列为例。斐波那契数 `F(n) = 1`，如果 `n` 为 1 或 2；否则，`F(n) = F(n – 1) + F(n – 2)`。要计算 `F(n)`，我们需要递归计算 `F(n – 1)` 和 `F(n – 2)`。要计算 `F(n – 1)`，我们需要计算 `F(n – 2)` 和 `F(n – 3)`，这显然与计算 `F(n – 2)` 所需的递归重叠，而 `F(n – 2)` 又由 `F(n – 3)` 和 `F(n – 4)` 计算得出。

我们可以递归地计算第 n 个斐波那契数，但这会需要指数级的递归调用。相反，我们可以记忆递归过程中每个 `F(m)` 的结果，以避免重复计算。一种更直接的方法是按顺序计算 `m = 1,…,n` 的 `F(m)`，并将结果存储在表格中。

```
n <- 10
F <- vector("numeric", length = n)
F[1] <- F[2] <- 1
for (m in 3:n) {
  F[m] <- F[m-1] + F[m-2]
}
F[n]
## [1] 55
```

另一个同样经典的例子是计算两个字符串 `x` 和 `y`（长度分别为 `n` 和 `m`）之间的编辑距离。这是将 `x` 转换为 `y` 所需的最少变换次数（字符替换、删除或插入），可以针对 `i=1,…, n+1` 和 `j=1,…, m+1` 递归定义如下：

```
E[i,j] = 
  if (j == 1) i - 1
  else if (i == 1) j - 1
  else min(
    E[i-1, j] + 1,          // 插入
    E[i, j-1] + 1,          // 删除
    E[i-1, j-1] + (x[i-1] != y[j-1] ? 1 : 0)  // 替换
  )
```

这里有两个基本情况，分别描述了 `x` 的前缀与空字符串，以及空字符串与 `y` 的前缀之间的编辑距离。递归情况有三种：插入，表示从 `x[1,…, i-2]` 与 `y[1,…, j-1]` 的匹配状态，转移到 `x[1,…, i-1]` 与 `y[1,…, j-1]`；删除，表示从 `y[1,…, j-2]` 与 `x[1,…, i-1]` 的匹配状态，转移到 `y[1,…, j-1]` 与 `x[1,…, i-1]`；最后是替换，表示从 `x[1,…, i-2]` 与 `y[1,…, j-2]` 的匹配状态，转移到 `x[1,…, i-1]` 与 `y[1,…, j-1]`。如果 `x[i-1] == y[j-1]`，此操作的代价为 0；如果 `x[i-1] != y[j-1]`，则代价为 1，这由指示变量 `1_{x[i-1] != y[j-1]}` 来体现。


  
这种递归也可以很容易地转化为动态规划算法。出于测试目的，我们可以构造以下两个序列：  

```
x <- c("a", "b", "c")
y <- c("a", "b", "b", "c")
```

其中，通过插入一个 `b` 可以从 `x` 变换到 `y`，因此编辑距离为 1。  

使用动态规划计算递归的代码可能如下所示：  

```
n <- length(x)
m <- length(y)
E <- vector("numeric", length = (n + 1) * (m + 1))
dim(E) <- c(n + 1, m + 1)
for (i in 1:(n + 1))
E[i, 1] <- i - 1
for (j in 1:(m + 1))
E[1, j] <- j - 1
for (i in 2:(n + 1)) {
for (j in 2:(m + 1)) {
E[i, j] <- min(
E[i - 1, j] + 1,
E[i, j - 1] + 1,
E[i - 1, j - 1] + (x[i - 1] != y[j - 1])
)
}
}
E
##      [,1] [,2] [,3] [,4] [,5]
## [1,]    0    1    2    3    4
## [2,]    1    0    1    2    3
## [3,]    2    1    0    1    2
## [4,]    3    2    1    1    1
```

编辑距离可以通过该表中右下角的单元格获得：  

```
E[n + 1, m + 1]
## [1] 1
```

对于上述两个例子，将递归转化为动态规划算法是直接的，但问题本身的声明——即递归表达的内容——在实现中丢失了，而实现正是这些填充表格的循环。  

我们希望构建一个领域特定语言，能够让我们从递归出发指定动态规划算法，并由该语言自动构建循环和计算。例如，我们应该能够像下面这样指定一个递归：  

```
fib <- {
F[n] <- 1 ? n <= 2
F[n] <- F[n - 1] + F[n - 2]
} %where% {
n <- 1:10
}
```

然后通过这个表达式构建斐波那契数列表：  

```
fib
##  [1]  1  1  2  3  5  8 13 21 34 55
```

在递归中，我们使用 `?` 运算符来指定在何种条件下应使用给定规则。你可能熟悉用 `?` 获取函数文档的用法，但 R 解析器也将其视为中缀运算符，并且它的优先级是最低的——甚至低于 `<-` 赋值。事实证明，这对该 DSL 十分方便。我们将递归情况指定为赋值，而通过使用优先级比赋值更低的运算符，如果规则有关联的条件，则调用表达式的最顶层将始终是一个 `?` 调用。  

与斐波那契递归类似，我们应该能够像下面这样指定编辑距离的计算：  

```
x <- c("a", "b", "c")
y <- c("a", "b", "b", "c")
edit <- {
E[1, j] <- j - 1
E[i, 1] <- i - 1
E[i, j] <- min(
E[i - 1, j] + 1,
E[i, j - 1] + 1,
E[i - 1, j - 1] + (x[i - 1] != y[j - 1])
) ? i > 1 && j > 1
} %where% {
i <- 1:(length(x) + 1)
j <- 1:(length(y) + 1)
}
```

并得到计算后的表格：  

```
edit
##      [,1] [,2] [,3] [,4] [,5]
## [1,]    0    1    2    3    4
## [2,]    1    0    1    2    3
## [3,]    2    1    0    1    2
## [4,]    3    2    1    1    1
```

这里，前两种情况仅在 `i` 或 `j` 为 1 时有效。我们可以用 `?` 来指定这一点，但我采用了另一种方法。我在赋值左侧使用索引模式来指定索引变量应匹配一个常量。这为我们提供了类似于前一章模式匹配的语义。  

我们也可以对斐波那契递归使用这种语法，如下所示：  

```
{
F[1] <- 1
F[2] <- 1
F[n] <- F[n - 1] + F[n - 2]
} %where% {
n <- 1:10
}
##  [1]  1  1  2  3  5  8 13 21 34 55
```

对于递归的求值语义，我将使用第一条同时满足索引模式和 `?` 条件的规则来计算值。由于我们总是选择第一个这样的表达式，因此我们不需要在编辑距离规范中显式指定一般情况的条件，例如：  

```
{
E[1,j] <- j - 1
E[i,1] <- i - 1
E[i,j] <- min(
E[i - 1,j] + 1,
E[i,j - 1] + 1,
E[i - 1,j - 1] + (x[i - 1] != y[j - 1])
)
} %where% {
i <- 1:(length(x) + 1)
j <- 1:(length(y) + 1)
}
##      [,1] [,2] [,3] [,4] [,5]
## [1,]    0    1    2    3    4
## [2,]    1    0    1    2    3
## [3,]    2    1    0    1    2
## [4,]    3    2    1    1    1
```

## 解析表达式  

这些示例让我们对语言的语法有了概念。我们希望它看起来像这样：  

```
DYNPROG_EXPR ::= RECURSIONS '%where%' RANGES
RECURSIONS ::= '{' PATTERN_ASSIGNMENTS '}'
RANGES ::= '{' RANGES_ASSIGNMENTS '}'
```

在顶层，语言通过用户定义的中缀运算符 `%where%` 实现。在运算符的左侧，我们需要指定递归；在右侧，我们需要指定算法应循环的范围。我们可以这样实现该运算符：  

```
`%where%` <- function(recursion, ranges) {
parsed <- list(
recursions = parse_recursion(rlang::enquo(recursion)),
ranges = parse_ranges(rlang::enquo(ranges))
)
eval_dynprog(parsed)
}
```

我们简单地解析左侧和右侧，然后调用 `eval_dynprog` 函数来运行动态规划算法并返回结果。  

该语言最简单的方面是范围的规范。在这里，我们定义变量，并将它们与应迭代的序列关联起来。  

```
RANGES_ASSIGNMENTS ::= RANGES_ASSIGNMENT
|  RANGES_ASSIGNMENT ';' RANGES_ASSIGNMENTS
```

我们需要一个或多个赋值。这里，我指定了用 `;` 分隔它们，但我们也会接受换行符；我们只需使用 R 用来分隔语句的方式。  

赋值采用以下形式：  

```
RANGES_ASSIGNMENT ::= RANGE_INDEX '<-' RANGE_EXPRESSION
```

我不再进一步分解，只需指定 `RANGE_INDEX` 应是一个 R 变量，而 `RANGE_EXPRESSION` 应是一个求值为序列的 R 表达式。  

在 `%where%` 运算符中，我们将范围规范转换为一个 quosure，以便知道在哪个作用域中求范围的值，然后像这样处理结果：  

```
parse_ranges <- function(ranges) {
ranges_expr <- rlang::get_expr(ranges)
ranges_env <- rlang::get_env(ranges)
stopifnot(ranges_expr[[1]] == "{")
ranges_definitions <- ranges_expr[-1]
n <- length(ranges_definitions)
result <- vector("list", length = n)
indices <- vector("character", length = n)
for (i in seq_along(ranges_definitions)) {
assignment <- ranges_definitions[[i]]
stopifnot(assignment[[1]] == "<-")
range_var <- as.character(assignment[[2]])
range_value <- eval(assignment[[3]], ranges_env)
indices[[i]] <- range_var
result[[i]] <- range_value
}
names(result) <- indices
result
}
```

首先，我们提取 quosure 的表达式和环境。我们将处理前者，并使用后者来求表达式的值。我们期望范围位于花括号内，因此我们对此进行测试并提取实际规范。我们遍历它们，期望每个都是赋值，其中我们可以获得左侧的索引变量和右侧表示实际范围的表达式。我们在 quosure 的作用域内求表达式的值，并构建一个列表作为解析结果。该列表将为每个范围表达式包含一个项目。项目的名称将是迭代变量的值，而值将是相应表达式求值的结果。  

```
parse_ranges(rlang::quo({
n <- 1:10
}))
## $n
##  [1]  1  2  3  4  5  6  7  8  9 10
parse_ranges(rlang::quo({
i <- 1:(length(x) + 1)
j <- 1:(length(y) + 1)
}))
## $i
## [1] 1 2 3 4
##
## $j
## [1] 1 2 3 4 5
```

递归的规范稍微复杂一些。  

```
PATTERN_ASSIGNMENTS ::= PATTERN_ASSIGNMENT
|  PATTERN_ASSIGNMENT ';' PATTERN_ASSIGNMENTS
```

其中  

```
PATTERN_ASSIGNMENT ::= PATTERN '<-' RECURSION
|  PATTERN '<-' RECURSION '?' CONDITION
PATTERN ::= TABLE '[' INDICES ']'
```

我们不再进一步分解语法。这里，`TABLE` 是一个变量，`INDICES` 是一个逗号分隔的变量或表达式序列，而 `RECURSION` 和 `CONDITION` 都是 R 表达式。  



使用这种语法，我们需要提取三部分不同的信息：索引模式（用于匹配）、`?`条件（用于检查），以及实际的递归调用。不过，解析器不必比范围解析复杂太多，只需将每个递归情况的这三个属性收集到列表中即可。

```
parse_recursion <- function(recursion) {
  recursion_expr <- rlang::get_expr(recursion)
  recursion_env <- rlang::get_env(recursion)
  stopifnot(recursion_expr[[1]] == "{")
  recursion_cases <- recursion_expr[-1]
  n <- length(recursion_cases)
  patterns <- vector("list", length = n)
  conditions <- vector("list", length = n)
  recursions <- vector("list", length = n)
  for (i in seq_along(recursion_cases)) {
    case <- recursion_cases[[i]]
    condition <- TRUE
    stopifnot(rlang::is_call(case))
    if (case[[1]] == "?") {
      # NB: 此处顺序很重要！
      condition <- case[[3]]
      case <- case[[2]]
    }
    stopifnot(case[[1]] == "<-")
    pattern <- case[[2]]
    recursion <- case[[3]]
    patterns[[i]] <- pattern
    recursions[[i]] <- recursion
    conditions[[i]] <- condition
  }
  list(
    recursion_env = recursion_env,
    patterns = patterns,
    conditions = conditions,
    recursions = recursions
  )
}
```

该函数与范围解析器的函数并无本质区别。我们提取 quosure 环境以及表达式，确认它是一个对花括号的调用，然后遍历所有递归情况。

如果某个递归情况是对 `?` 的调用，那么它必然带有条件。由于 `?` 的优先级是所有运算符中最低的，因此如果条件存在，它必然是顶层调用——除非用户使用括号进行特殊处理，但那也是他们自找麻烦。我们将默认条件设为 `TRUE`。这样一来，在未指定 `?` 条件时，我们无需处理特殊情况；但如果有条件，我们就用表达式替换 `TRUE`。否则，我们只需收集每个递归情况的所有信息即可。

我们在解析器中不对递归进行求值，因此解析器中不会使用 quosure 环境。相反，我们会将 quosure 环境与解析后的信息一同返回。后续在求值递归表达式时，我们会用到它。

我们可以用斐波那契递归来测试解析器的行为。

```
parse_recursion(rlang::quo({
  F[n] <- 1
  F[n] <- if (n > 1) n * F[n - 1] else 1
}))
## $recursion_env
## <environment: R_GlobalEnv>
##
## $patterns
## $patterns[[1]]
## F[n]
##
## $patterns[[2]]
## F[n]
##
##
## $conditions
## $conditions[[1]]
## [1] TRUE
##
## $conditions[[2]]
## n > 1
##
##
## $recursions
## $recursions[[1]]
## [1] 1
##
## $recursions[[2]]
## n * F[n - 1]
```

同样，我们也可以用它来解析编辑距离的递归。

```
parse_recursion(rlang::quo({
  E[1,j] <- j - 1
  E[i,1] <- i - 1
  E[i,j] <- min(E[i - 1, j] + 1, E[i, j - 1] + 1, E[i - 1, j - 1] + (x[i - 1] != y[j - 1]))
}))
## $recursion_env
## <environment: R_GlobalEnv>
##
## $patterns
## $patterns[[1]]
## E[1, j]
##
## $patterns[[2]]
## E[i, 1]
##
## $patterns[[3]]
## E[i, j]
##
##
## $conditions
## $conditions[[1]]
## [1] TRUE
##
## $conditions[[2]]
## [1] TRUE
##
## $conditions[[3]]
## i > 1 && j > 1
##
##
## $recursions
## $recursions[[1]]
## j - 1
##
## $recursions[[2]]
## i - 1
##
## $recursions[[3]]
## min(E[i - 1, j] + 1, E[i, j - 1] + 1, E[i - 1, j - 1] + (x[i - 1] != y[j - 1]))
```

## 求值表达式

为了对动态规划表达式求值，我们需要遍历所有范围，为满足条件和模式的索引组合计算第一个值，并将结果存入动态规划表。我们可以通过构造嵌套循环来实现这一步，但还有一种更简单的方法：使用表达式 `do.call(expand.grid, ranges)` 来构造所有范围索引的组合。以斐波那契数列为例，我们会得到如下结果：

```
ranges <- parse_ranges(rlang::quo({
  n <- 1:10
}))
head(do.call(expand.grid, ranges))
##   n
## 1 1
## 2 2
## 3 3
## 4 4
## 5 5
## 6 6
```

除了每个范围关联了一个名称之外，这本质上就是输入本身。对于编辑距离的例子，该表达式的完整用法如下：

```
ranges <- parse_ranges(rlang::quo({
  i <- 1:(length(x) + 1)
  j <- 1:(length(y) + 1)
}))
head(do.call(expand.grid, ranges))
##   i j
## 1 1 1
## 2 2 1
## 3 3 1
## 4 4 1
## 5 1 2
## 6 2 2
```

这里我们从范围中得到了所有 `i` 和 `j` 的组合。与其构造嵌套循环的表达式，不如直接遍历这个调用所生成数据表的每一行。提取一行后，我们会得到一个带有命名的范围索引的向量。

```
edit_ranges <- do.call(expand.grid, ranges)
edit_ranges[1,]
##   i j
## 1 1 1
edit_ranges[2,]
##   i j
## 2 2 1
```

利用 `with` 函数，我们可以创建一个环境，将范围索引绑定到具体值上，而正是要在这样的环境中对递归表达式求值。

```
with(edit_ranges[3,],
  cat(i, j, "\n")
)
## 3 1
```

遍历通过这种方式创建的数据表的各行，将成为我们算法的核心。不过，在实现这一点之前，我们需要先构造用于插入循环体内的表达式。

我们先从创建用于测试模式与范围索引变量的代码开始。这里，我们将语法设计得比上一章的模式匹配更简单。我们假设递归左侧的索引由索引变量或常量组成，并构建一个测试，简单地将我们从范围解析器中获取的索引变量名称与递归规范中的值进行比较。我们可以像下面这样简单地构造这个函数：

```
make_pattern_match <- function(pattern, range_vars) {
  matches <- vector("list", length = length(range_vars))
  stopifnot(pattern[[1]] == "[")
  for (i in seq_along(matches)) {
    matches[[i]] <- call(
      "==",
      pattern[[i + 2]],
      range_vars[[i]]
    )
  }
  rlang::expr(all(!!! matches))
}
```

我们构造了一个由 `==` 比较组成的列表，用于比较规范中的索引模式与范围索引可以遍历的具体值。对于后者，我们首先需要将字符串转换为符号，以确保我们比较的是模式变量的值，而不是检查常量是否与变量名匹配。我们不在该函数内部进行这种转换；而是期望调用方已经完成了这一步。我们使用索引 `i + 2` 来获取模式的原因是，模式本身是对 `[` 运算符的调用，因此索引 1 是 `[` 符号，索引 2 是表名。索引则紧随这两个参数元素之后。

下面几个例子可以说明模式匹配谓词是如何构造的。

```
make_pattern_match(rlang::expr(F[1]), list(as.symbol("n")))
## all(1 == n)
make_pattern_match(rlang::expr(F[n]), list(as.symbol("n")))
## all(n == n)
make_pattern_match(rlang::expr(E[1,j]),
  list(as.symbol("i"), as.symbol("j")))
## all(1 == i, j == j)
```

我们测试所有范围变量，即使它们完全自由、可以取任何值。当它们自由时，我们只需检查它们是否等于自身——除非它们是 `NA`，否则这一条件总是成立；而如果它们是 `NA`，我们也束手无策。以这种方式创建测试，使得测试模式的代码更简洁，同时测试变量是否等于自身并不会改变最终条件的结果。



我们将这个表达式构造映射到所有模式上，从而为所有递归情况生成测试代码。

```r
make_pattern_tests <- function(patterns, range_vars) {
tests <- vector("list", length = length(patterns))
for (i in seq_along(tests)) {
tests[[i]] <- make_pattern_match(
patterns[[i]],
range_vars
)
}
tests
}
```

给定这些斐波那契解析器的输出结果：

```r
fib_ranges <- parse_ranges(rlang::quo({
n <- 1:10
}))
fib_recursions <- parse_recursion(rlang::quo({
F[n] <- 1                   ? n <= 2
F[n] <- F[n - 1] + F[n - 2] ? n > 2
}))
```

我们可以像这样为所有情况构建模式测试：

```r
make_pattern_tests(
fib_recursions$patterns,
Map(as.symbol, names(fib_ranges))
)
## [[1]]
## all(n == n)
##
## [[2]]
## all(n == n)
```

在求值表达式之前，我们需要知道模式和`?`条件是否同时匹配，因此我们将这两个列表合并起来。我们只需构造一个新列表，在其中使用`&&`将模式匹配与`?`条件结合起来。

```r
make_condition_checks <- function(
ranges,
patterns,
conditions,
recursions
) {
test_conditions <- make_pattern_tests(
patterns,
Map(as.symbol, names(ranges))
)
for (i in seq_along(conditions)) {
test_conditions[[i]] <- rlang::call2(
"&&", test_conditions[[i]], conditions[[i]]
)
}
test_conditions
}
```

为了验证这一点是否按预期工作，我们可以再次使用斐波那契递归。

```r
make_condition_checks(
fib_ranges,
fib_recursions$patterns,
fib_recursions$conditions,
fib_recursions$recursions
)
## [[1]]
## all(n == n) && n > 2
##
## [[2]]
## all(n == n) && n <= 2
```

因此，现在的状态是我们拥有了所有的测试条件。我们可以将这些测试条件与用于求值递归的表达式结合起来，并构造一个`if`-`else`表达式序列。如果我们用两个参数构造一个`if`调用，这两个参数将被解释为测试条件以及在测试为真时要计算的值。如果我们用三个参数构造一个调用，则第三个参数被认为是语句的`else`部分。因此，构造一系列`if`-`else`语句的最简单方法是从列表的末尾开始。我们可以将最后一个案例翻译成一个没有`else`部分的`if`语句——或者，如果我们愿意，也可以制作一个会抛出错误的`else`部分。然后我们可以遍历这些案例，在每个案例中构造一个新的`if`表达式，并将前一个案例作为其`else`部分。这个想法可以这样实现：

```r
make_recursion_case <- function(
test_expr,
value_expr,
continue
) {
if (rlang::is_null(continue)) {
rlang::call2("if", test_expr, value_expr)
} else {
rlang::call2("if", test_expr, value_expr, continue)
}
}
make_update_expr <- function(
ranges,
patterns,
conditions,
recursions
) {
conditions <- make_condition_checks(
ranges,
patterns,
conditions,
recursions
)
continue <- NULL
for (i in rev(seq_along(conditions))) {
continue <- make_recursion_case(
conditions[[i]], recursions[[i]], continue
)
}
continue
}
```

对于斐波那契递归，结果就是这个表达式：

```r
make_update_expr(
fib_ranges,
fib_recursions$patterns,
fib_recursions$conditions,
fib_recursions$recursions
)
## if (all(n == n) && n > 2) F[n - 1] + F[n - 2] else if (all(n ==
##     n) && n <= 2) 1
```

剩下要做的就是为每个范围索引的组合求值这个表达式。为此，我们使用之前见过的`do.call`表达式创建表格，遍历所有表格行，并将更新表达式插入到循环体内部。用于创建整个循环函数的函数如下所示¹：

```r
make_loop_expr <- function(tbl_name, update_expr) {
rlang::expr({
combs <- do.call(expand.grid, ranges)
rlang::UQ(tbl_name) <- vector(
"numeric",
length = nrow(combs)
)
dim(rlang::UQ(tbl_name)) <- Map(length, ranges)
for (row in seq_along(rlang::UQ(tbl_name))) {
rlang::UQ(tbl_name)[row] <- with(
combs[row, , drop = FALSE], {
rlang::UQ(update_expr)
}
)
}
rlang::UQ(tbl_name)
})
}
```

除了`update_expr`之外，代码还需要知道表名。这是因为`update_expr`将在递归案例中引用该表。不过，从模式中很容易获取表名。

```r
get_table_name <- function(patterns) {
p <- patterns[[1]]
stopifnot(p[[1]] == "[")
p[[2]]
}
get_table_name(fib_recursions$patterns)
## F
```

展开斐波那契递归的表达式后，循环看起来是这样的：

```r
make_loop_expr(get_table_name(fib_recursions$patterns),
make_update_expr(
fib_ranges,
fib_recursions$patterns,
fib_recursions$conditions,
fib_recursions$recursions
))
## {
##     combs <- do.call(expand.grid, ranges)
##     F <- vector("numeric", length = nrow(combs))
##     dim(F) <- Map(length, ranges)
##     for (row in seq_along(F)) {
##         F[row] <- with(combs[row, , drop = FALSE], {
##             if (all(n == n) && n > 2)
##                 F[n - 1] + F[n - 2]
##             else if (all(n == n) && n <= 2)
##                 1
##         })
##     }
##     F
## }
```

对于编辑距离递归，我们得到如下结果：

```r
edit_ranges <- parse_ranges(rlang::quo({
i <- 1:(length(x) + 1)
j <- 1:(length(y) + 1)
}))
edit_recursions <- parse_recursion(rlang::quo({
E[1,j] <- j - 1
E[i,1] <- i - 1
E[i,j] <- min(
E[i - 1,j] + 1,
E[i,j - 1] + 1,
E[i - 1,j - 1] + (x[i - 1] != y[j - 1])
)
}))
make_loop_expr(get_table_name(edit_recursions$patterns),
make_update_expr(
edit_ranges,
edit_recursions$patterns,
edit_recursions$conditions,
edit_recursions$recursions
))
## {
##     combs <- do.call(expand.grid, ranges)
##     E <- vector("numeric", length = nrow(combs))
##     dim(E) <- Map(length, ranges)
##     for (row in seq_along(E)) {
##         E[row] <- with(combs[row, , drop = FALSE], {
##             if (all(1 == i, j == j) && TRUE)
##                 j - 1
##             else if (all(i == i, 1 == j) && TRUE)
##                 i - 1
##             else if (all(i == i, j == j) && TRUE)
##                 min(E[i - 1, j] + 1, E[i, j - 1] + 1, E[i - 1,
##                   j - 1] + (x[i - 1] != y[j - 1]))
##         })
##     }
##     E
## }
```

现在剩下要做的就是求值这个循环表达式，而唯一的技巧是确保在正确的环境中求值它。循环内部的`with`语句确保我们使用相关的索引变量对表达式进行作用域覆盖，因此我们不必担心范围和范围索引。然而，我们在更新表达式中求值的表达式，应该在我们定义表达式的那个作用域中求值，这个作用域在我们解析表达式时被捕获在了 quosure 中。理想情况下，我们只需在这个 quosure 环境中求值循环，但循环表达式需要知道范围，所以我们也需要将这些范围放入作用域中。一种方法是在 quosure 作用域和循环之间放置一个局部函数调用环境，并通过这种方式求值表达式：

```r
eval_recursion <- function(ranges, recursions) {
loop <- make_loop_expr(
get_table_name(recursions$patterns),
make_update_expr(
ranges,
recursions$patterns,
recursions$conditions,
recursions$recursions
))
eval_env <- rlang::env_clone(
environment(), # this function environment
recursions$recursion_env # quosure environment
)
eval(loop, envir = eval_env)
}
```

对于斐波那契表达式，我们得到以下结果：

```r
eval_recursion(fib_ranges, fib_recursions)
##  [1]  1  1  2  3  5  8 13 21 34 55
```

正如我们本章前面所看到的，`%where%` 运算符只是解析了范围和递归，然后在名为 `eval_dynprog` 的函数中求值结果。这个函数除了调用我们刚刚编写的 `eval_recursion` 函数之外，没有做其他任何事。

```r
eval_dynprog <- function(dynprog) {
eval_recursion(dynprog$ranges, dynprog$recursions)
}
```

直接将解析后的片段提供给 `eval_dynprog` 函数，而不是通过 `%where%`，我们可以看到这确实是正在发生的事情。

```r
eval_dynprog(list(
ranges = fib_ranges,
recursions = fib_recursions
))
##  [1]  1  1  2  3  5  8 13 21 34 55
eval_dynprog(list(
ranges = edit_ranges,
recursions = edit_recursions
))
##      [,1] [,2] [,3] [,4] [,5]
## [1,]    0    1    2    3    4
## [2,]    1    0    1    2    3
## [3,]    2    1    0    1    2
## [4,]    3    2    1    1    1
```



## 修复评估环境

目前的解决方案虽然可行，但仍存在一个略显不足的方面。当评估循环时，我们在 quosure 环境和待评估的表达式之间放置了一个环境，而这个环境包含了一些局部变量，它们可能与 quosure 作用域中的变量产生冲突。

我们可以避免这种情况，确保只将范围索引变量作为额外的作用域引入，不再引入其他变量。为此，我们需要采取一种稍有不同的方法。我们不再将循环构建为一个表达式，然后在函数加 quosure 作用域中对其进行评估，而是直接对范围索引组合进行循环。在循环体中，我们构建并评估更新表达式。

替代方案如下所示。与之前的方案类似，我们首先获取动态规划表的名称，并构建更新表达式。不过，这一次我们还需要以字符串形式获取表名。我们需要这个字符串来访问评估环境中的变量名。我们将评估环境构建为一个以 quosure 环境为父环境的空环境。这样一来，我们可以在不影响 quosure 环境的情况下修改评估环境，同时仍然能够访问 quosure 环境中的所有变量。

构建表时，我们不需要任何元编程。我们可以根据范围获取表的大小和维度。不过，我们需要将表放入评估环境，以便更新表达式能够访问它。我们通过环境上的 `[[` 运算符来实现这一点。构建好表并将其放入评估环境后，我们像之前一样遍历所有范围索引组合。

在计算递归值时，我们希望用索引变量覆盖更新表达式的作用域，因此需要在包含 `combs[row,]` 的环境中评估表达式。然而，如果我们直接将这一行作为 `eval` 的第二个参数，则无法评估实际的赋值操作。R 将 `eval` 的第二个参数视为表达式评估的环境，而在一个实际上是列表的环境中进行赋值是行不通的。你不会收到错误信息，但也不会将值赋给任何变量。因此，我们需要将递归值的评估和表的赋值分开处理。我们在一个 `combs[row,]` 覆盖了评估环境的环境中评估递归表达式，而在没有任何额外作用域覆盖的评估环境中评估赋值操作。

你可能会认为，一旦有了要插入表中的值，直接更新表即可，无需为此构建赋值表达式。但事情并没有那么简单。R 极力保持数据的不可变性，如果从两个不同位置引用一个表并对其元素进行赋值，R 会复制该表，仅更新其中一个副本，并将修改后的副本放入我们执行赋值操作的环境中，而原始表则保留在另一个作用域中。为了更新表，使得递归表达式能够使用它，我们需要在评估环境中进行更新（或者另一种方法是，更新表，然后将副本赋值回评估环境）。

因此，替代的评估函数如下所示：

```
eval_recursion <- function(ranges, recursions) {
  tbl_name <- get_table_name(recursions$patterns)
  tbl_name_string <- as.character(tbl_name)
  update_expr <- make_update_expr(
    ranges,
    recursions$patterns,
    recursions$conditions,
    recursions$recursions
  )
  eval_env <- rlang::child_env(recursionsxs$recursion_env)
  combs <- do.call(expand.grid, ranges)
  tbl <- vector("numeric", length = nrow(combs))
  dim(tbl) <- Map(length, ranges)
  eval_env[[tbl_name_string]] <- tbl
  for (row in seq_along(tbl)) {
    val <- eval(
      rlang::expr(rlang::UQ(update_expr)),
      combs[row, , drop = FALSE],
      eval_env
    )
    eval(rlang::expr(
      rlang::UQ(tbl_name)[rlang::UQ(row)]
        <- rlang::UQ(val)
    ), eval_env)
  }
  eval_env[[tbl_name_string]]
}
```

不过，它的行为与之前的函数完全相同。

```
eval_dynprog(list(
  ranges = fib_ranges,
  recursions = fib_recursions
))
##  [1]  1  1  2  3  5  8 13 21 34 55
eval_dynprog(list(
  ranges = edit_ranges,
  recursions = edit_recursions
))
##      [,1] [,2] [,3] [,4] [,5]
## [1,]    0    1    2    3    4
## [2,]    1    0    1    2    3
## [3,]    2    1    0    1    2
## [4,]    3    2    1    1    1
```

至少，除非你的递归引用了会被之前版本中的局部变量覆盖的变量。

脚注

在行下标中使用 `drop=FALSE` 是为了确保即使只有一行时，我们也能获取到包含命名变量的行。

取决于个人喜好，你可能更倾向于这个方案而不是之前的那个，仅仅因为我们最大限度地减少了生成代码的评估次数。不过，这个新方案涉及生成和评估更多的表达式，并且需要一些技巧来在我们评估递归的作用域中更新表，因此第一个方案相比之下也有其优点。



