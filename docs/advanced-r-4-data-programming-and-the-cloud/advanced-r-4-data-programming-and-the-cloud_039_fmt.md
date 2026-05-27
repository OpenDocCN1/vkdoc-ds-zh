# 第 6 章 编写包

`check()` 函数包含多个步骤。它首先确保文档是最新的，然后构建一个源代码压缩包，最后在其上运行 `Rcmd check`。我们可以看到，检查捕捉到了不少问题。所有这些问题基本上都归结为没有精确地告诉 R 导入或需要哪些函数或包。大多数问题的出现是因为包和交互式 R 工作方式之间的差异。在交互式会话期间，`base`、`methods`、`graphics`、`utils` 和 `stats` 包默认会被加载，即使没有调用 `library(stats)` 之类。这意味着在交互式会话中，默认情况下搜索路径上有许多可用的函数。在包中，R 在最近的版本中变得更加严格，并且要求明确导入 `base` 包之外的所需函数。这些检查甚至建议了我们可以在命名空间中添加什么。我们使用 `roxygen2` 包，因此我们不是将这些添加到命名空间，而是将它们添加到 R 代码文件中。具体来说，在 `AdvancedRPkg/R/plot_functions.R` 中，紧跟在 `@export` roxygen2 语句之后，我们为 `meanPlot()` 添加以下内容：

```r
##' @importFrom graphics plot points
##' @importFrom stats formula
```

并且在同一个文件中为 `ggplot.lm()` 方法添加以下内容：

```r
##' @importFrom stats predict qnorm
```

在 `AdvancedRPkg/R/textplot.R` 中，紧跟在 `@param object` roxygen2 语句之后，我们为 `show()` 方法添加以下内容：

```r
##' @importFrom utils head
```

此外，还有一个类似的问题是关于某些全局变量的绑定不可见。这源于 `ggplot.lm()` 方法；因为它使用了 `within()`，R 无法判断这些变量是否已被定义，即使它们是在包含这些变量的数据框环境中进行查找。虽然这只是一个有点虚假的提示，但最好还是消除所有提示。一个解决方案是在我们的 R 文件中的某处添加对 `globalVariables()` 的调用。在 `AdvancedRPkg/R/plot_functions.R` 文件的顶部，我们添加以下内容：

```r
globalVariables(c("fit", "se.fit"))
```

注意到的最后一个问题是，在其中一个示例中找不到 `ggplot()`。因为我们的包导入了 `ggplot2` 包，所以 `ggplot()` 函数对于包内的代码是可用的。但是，如果用户只加载了我们的包，`ggplot2` 函数并不会加载到用户的搜索路径中。函数的示例是作为用户来运行的，因此该函数不可用，R 会抛出一个错误。这里的最佳路径比较复杂。

我们可以省略这个示例，但那样文档的帮助性就会降低。我们可以将 `ggplot2` 从 `DESCRIPTION` 文件的 `Imports` 字段移动到 `Depends` 字段。被依赖的包会在加载包之前加载。这种方法的缺点是迫使用户在加载我们的包时必须加载 `ggplot2`。这会增加用户函数被屏蔽的几率，因为它强制加载了许多包。我们也可以确保代码不被运行。最后，我们可以通过添加 `ggplot2::function()` 来显式地指向 `ggplot2` 包。这样做很繁琐，因为我们在那个示例中使用了几个 `ggplot2` 函数，并且需要在各处添加。然而，这确保了示例能够正常工作，并且避免将 `ggplot2` 加载到用户的搜索路径上。当然，如果用户愿意，他们可以通过自己显式调用 `library(ggplot2)` 来加载 `ggplot2`。在 `AdvancedRPkg/R/plot_functions.R` 文件中，我们编辑 `ggplot.lm()` 示例的 roxygen2 代码，如下所示：

```r
##' @examples
##' ggplot2::ggplot(
##' lm(mpg ˜ hp * qsec, data = mtcars),
##' ggplot2::aes(hp, mpg, linetype = factor(qsec)),
##' vars = list(
##' hp = min(mtcars$hp):max(mtcars$hp),
##' qsec = round(mean(mtcars$qsec) + c(-1, 1) * sd(mtcars$qsec)), 1)) +
##' ggplot2::geom_ribbon(ggplot2::aes(ymin = LL, ymax = UL), alpha = .2) +
##' ggplot2::geom_line() +
##' ggplot2::theme_bw()
```

接下来，我们重新运行 `check()` 函数：

```r
check(cran = TRUE)
```

正在更新 AdvancedRPkg 文档

正在写入 NAMESPACE

正在加载 AdvancedRPkg

正在写入 NAMESPACE

-- 正在构建 ------------------------------------------ AdvancedRPkg --

正在设置环境变量：

* CFLAGS : -Wall -pedantic

* CXXFLAGS : -Wall -pedantic

* CXX11FLAGS: -Wall -pedantic

----------------------------------------------------------------------

v 正在查找文件 'C:\...\RCode\AdvancedRPkg/DESCRIPTION'

- 正在准备 'AdvancedRPkg':

v 正在检查 DESCRIPTION 元信息

- 正在检查源文件和 make 文件以及 shell 脚本中是否有 LF 换行符

- 正在检查是否存在空目录或不需要的目录

- 正在检查是否应添加 'data/datalist' 文件

- 正在构建 'AdvancedRPkg_0.0.0.9000.tar.gz'

--

-- 正在检查 ------------------------------------------ AdvancedRPkg --

正在设置环境变量：

* _R_CHECK_CRAN_INCOMING_USE_ASPELL_: TRUE

* _R_CHECK_CRAN_INCOMING_REMOTE_ : FALSE

* _R_CHECK_CRAN_INCOMING_ : FALSE

* _R_CHECK_FORCE_SUGGESTS_ : FALSE

* NOT_CRAN : true

-- R CMD check -------------------------------------------------------

在 `dir.create(pkgoutdir, mode = "0755")` 中出现警告：

'C:\...\Temp\...\AdvancedRPkg.Rcheck' 已存在

* 正在使用日志目录 'C:/.../Temp/.../AdvancedRPkg.Rcheck'

* 正在使用 R 版本 3.6.3 (2020-02-29)

* 正在使用平台: x86_64-w64-mingw32 (64-bit)

## 第 6 章 编写 paCkages

我们收到了一些关于临时文件已存在的警告，因为我们之前运行过代码。我们在尝试运行测试时也遇到了一个错误，因为虽然我们已经创建了它，但尚未安装 `AdvancedRPkg`。

我们可以通过将包安装到本地库来解决这个问题。确保你选择了所构建的 tar 包的位置：

```r
build()
install.packages("../AdvancedRPkg_0.0.0.9000.tar.gz")
```

要解决有关临时文件的问题（如果遇到），你可以关闭并重新启动 R。注意，你需要重新加载这些包。然后重新检查。如果一切顺利，我们应得到状态为“通过”，如下所示。你也应该得到零错误和零警告，并且测试应该运行正常：

```r
check(cran = TRUE)
```

### 初始检查输出

```txt
## * 正在使用会话字符集: ISO8859-1

```txt
## `dir.create(dir, mode = "0755")` 中的警告：
## 'C:\...\Temp\...\AdvancedRPkg.Rcheck\00_pkg_src' 已存在
## * 使用选项 '--no-manual --as-cran'
## * 正在检查文件 'AdvancedRPkg/DESCRIPTION' ... 通过
## * 这是包 'AdvancedRPkg' 版本 '0.0.0.9000'
## * 包编码：UTF-8
## * 正在检查包的命名空间信息 ... 通过
## * 正在检查包的依赖关系 ... 通过
## * 正在检查是否为源代码包 ... 通过
```

### 详细检查过程（部分警告与失败）

```txt
# 第 6 章 编写 paCkages

## * 正在检查是否存在命名空间 ... 通过
## * 正在检查可执行文件 ... 通过
## * 正在检查隐藏文件和目录 ... 通过
## * 正在检查可移植文件名 ... 通过
## * 正在检查包 'AdvancedRPkg' 是否可安装 ... 通过
## * 正在检查已安装包的大小 ... 通过
## * 正在检查包目录 ... 通过
## * 正在检查未来文件时间戳 ... 通过
## * 正在检查 DESCRIPTION 元信息 ... 警告
## 对 R 版本 '3.6.3' 的依赖没有补丁级别 0
## * 正在检查顶层文件 ... 通过
## * 正在检查遗留文件 ... 通过
## * 正在检查索引信息 ... 通过
## * 正在检查包子目录 ... 通过
## * 正在检查 R 文件中的非 ASCII 字符 ... 通过
## * 正在检查 R 文件的语法错误 ... 通过
## * 正在检查包是否可以加载 ... 通过
## * 正在检查包是否能以声明的依赖关系加载 ... 通过
## * 正在检查包是否能被干净地卸载 ... 通过
## * 正在检查命名空间是否能以声明的依赖关系加载 ... 通过
## * 正在检查命名空间是否能被干净地卸载 ... 通过
## * 正在检查不在库搜索路径上的加载情况 ... 通过
## * 正在检查 R 代码中的依赖关系 ... 通过
## * 正在检查 S3 泛型/方法一致性 ... 通过
## * 正在检查替换函数 ... 通过
## * 正在检查外部函数调用 ... 通过
## * 正在检查 R 代码中的潜在问题 ... 通过
## * 正在检查 Rd 文件 ... 通过
## * 正在检查 Rd 元数据 ... 通过
## * 正在检查 Rd 行宽 ... 通过
## * 正在检查缺失的文档条目 ... 通过
## * 正在检查代码/文档不匹配 ... 通过
```

```txt
# 第 6 章 编写 paCkages

## * 正在检查 Rd \usage 部分 ... 通过
## * 正在检查 Rd 内容 ... 通过
## * 正在检查示例中未声明的依赖关系 ... 通过
## * 正在检查 'data' 目录的内容 ... 通过
## * 正在检查数据中的非 ASCII 字符 ... 通过
## * 正在检查 ASCII 和未压缩保存的数据 ... 通过
## * 正在检查示例 ... 通过
## * 正在检查 'tests' 中未声明的依赖关系 ... 通过
## * 正在检查测试 ...
## 正在运行 'testthat.R'
## 通过
## * 正在检查临时目录中的残留 ... 通过
## * 完成
##
## 状态：1 个警告
##
## 详情请参见
## 'C:/.../Temp/.../AdvancedRPkg.Rcheck/00check.log'
##
##
## -- R CMD check 结果 ------------------ AdvancedRPkg 0.0.0.9000 ----
## 持续时间：26.3 秒
##
## > 正在检查 DESCRIPTION 元信息 ... 警告
## 对 R 版本 '3.6.3' 的依赖没有补丁级别 0
##
## -- 测试失败 --------------------------------------- testthat ----
##
## > library(testthat)
## > library(AdvancedRPkg)
## Error in library(AdvancedRPkg) :
## 没有名为 'AdvancedRPkg' 的包
## 执行终止
##
## 0 个错误 v | 1 个警告 x | 0 个注意事项 v
```

### 安装后检查输出

```r
## 正在更新 AdvancedRPkg 文档
## 正在写入 NAMESPACE
## 正在加载 AdvancedRPkg
## 正在写入 NAMESPACE
## -- 正在构建 -------------------------------------------- AdvancedRPkg --
## 设置环境变量：
## * CFLAGS : -Wall -pedantic
## * CXXFLAGS : -Wall -pedantic
## * CXX11FLAGS: -Wall -pedantic
## ------------------------------------------------------------------------
## v 正在检查文件 'c:\...\RCode\AdvancedRPkg/DESCRIPTION'
## - 正在准备 'AdvancedRPkg':
## v 正在检查 DESCRIPTION 元信息
## - 正在检查源文件和 make 文件以及 shell 脚本中是否使用 LF 行尾
## - 正在检查空目录或不需要的目录
## - 正在检查是否应添加 'data/datalist' 文件
## - 正在构建 'AdvancedRPkg_0.0.0.9000.tar.gz'
```

```txt
# 第 6 章 编写 paCkages

## * _R_CHECK_CRAN_INCOMING_USE_ASPELL_: TRUE
## * _R_CHECK_CRAN_INCOMING_REMOTE_ : FALSE
## * _R_CHECK_CRAN_INCOMING_ : FALSE
## * _R_CHECK_FORCE_SUGGESTS_ : FALSE
## * NOT_CRAN : true
## -- R CMD check ---------------------------------------------------------
## * 使用日志目录 'C:/.../Temp/.../AdvancedRPkg.Rcheck'
## * 使用 R 版本 3.6.3 (2020-02-29)
## * 使用平台: x86_64-w64-mingw32 (64-bit)
## * 使用会话字符集: ISO8859-1
## * 使用选项 '--no-manual --as-cran'
## * 正在检查文件 'AdvancedRPkg/DESCRIPTION' ... 通过
## * 这是包 'AdvancedRPkg' 版本 '0.0.0.9000'
## * 包编码：UTF-8
## * 正在检查包的命名空间信息 ... 通过
## * 正在检查包的依赖关系 ... 通过
## * 正在检查是否为源代码包 ... 通过
## * 正在检查是否存在命名空间 ... 通过
## * 正在检查可执行文件 ... 通过
## * 正在检查隐藏文件和目录 ... 通过
## * 正在检查可移植文件名 ... 通过
## * 正在检查包 'AdvancedRPkg' 是否可安装 ... 通过
## * 正在检查已安装包的大小 ... 通过
## * 正在检查包目录 ... 通过
## * 正在检查未来文件时间戳 ... 通过
## * 正在检查 DESCRIPTION 元信息 ... 通过
## * 正在检查顶层文件 ... 通过
## * 正在检查遗留文件 ... 通过
## * 正在检查索引信息 ... 通过
## * 正在检查包子目录 ... 通过
## * 正在检查 R 文件中的非 ASCII 字符 ... 通过
## * 正在检查 R 文件的语法错误 ... 通过
## * 正在检查包是否可以加载 ... 通过
## * 正在检查包是否能以声明的依赖关系加载 ... 通过
```

```txt
# 第 6 章 编写 paCkages

## * 正在检查包是否能被干净地卸载 ... 通过
## * 正在检查命名空间是否能以声明的依赖关系加载 ... 通过
## * 正在检查命名空间是否能被干净地卸载 ... 通过
## * 正在检查 R 代码中的依赖关系 ... 通过
## * 正在检查 S3 泛型/方法一致性 ... 通过
## * 正在检查替换函数 ... 通过
## * 正在检查外部函数调用 ... 通过
## * 正在检查 R 代码中的潜在问题 ... 通过
## * 正在检查 Rd 文件 ... 通过
## * 正在检查 Rd 元数据 ... 通过
## * 正在检查 Rd 行宽 ... 通过
## * 正在检查缺失的文档条目 ... 通过
## * 正在检查代码/文档不匹配 ... 通过
## * 正在检查 Rd \usage 部分 ... 通过
## * 正在检查 Rd 内容 ... 通过
## * 正在检查示例中未声明的依赖关系 ... 通过
## * 正在检查 'data' 目录的内容 ... 通过
## * 正在检查数据中的非 ASCII 字符 ... 通过
## * 正在检查 ASCII 和未压缩保存的数据 ... 通过
## * 正在检查示例 ... 通过
## * 正在检查 'tests' 中未声明的依赖关系 ... 通过
## * 正在检查测试 ...
## 正在运行 'testthat.R'
## 通过
```

### 最终检查状态

```txt
## 检查临时目录中的`detritus`... 通过

## `完成`

## 状态：正常

## -- R CMD 检查结果 -------------------- AdvancedRPkg 0.0.0.9000 ----
## 耗时：26.8 秒

## 0 个错误 v | 0 个警告 v | 0 个提示 v
```