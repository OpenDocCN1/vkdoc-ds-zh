# 构建、安装和分发 R 包

与 Git 类似，在构建 R 包时，忽略目录中的某些文件可能会很有帮助。我们在主目录 `AdvancedRPkg/` 下创建一个名为 `.Rbuildignore` 的文件，包含以下代码。该文件应与 `DESCRIPTION` 文件位于同一层级：

```
desktop.ini
.Rhistory
.RData
```

再次运行 `document("AdvancedRPkg")` 确保文档是最新的之后，我们就差不多准备好了。此时，我们已经做了一些更改，因此最好将更改添加到 Git 仓库并进行提交（如果你一开始选择了使用 Git）。虽然你可以通过运行 `git add` 一次性添加所有文件，但根据相似主题分块提交更改，能提供更多信息，并且在需要时也更容易回滚。例如，我们为以下每一项使用了单独的提交，这些都在后续代码中以分块形式展示：

*   更新后的代码文件（`AdvancedRPkg/R/` 目录下的每个文件）、`DESCRIPTION` 和 `NAMESPACE`，提交信息为 "添加 roxygen 样式文档"
*   示例数据，提交信息为 "添加示例数据"
*   更新后的文档文件（`AdvancedRPkg/man/` 目录下的每个文件），提交信息为 "重新运行 roxygen 生成包文档"
*   `.Rbuildignore` 文件，提交信息为 "添加文件以在包构建时忽略某些文件"

现在，我们可以在 `chapter06.R` 文件中使用 `devtools` 包中的 `build()` 函数将包构建成一个压缩的源代码 tar 包。输出显示了构建过程，并在最后生成了一个 `.tar.gz` 文件：

```
build("AdvancedRPkg")
"c:/usr/MRO/MRO-3.3.1/bin/x64/R" --no-site-file --no-environ  \
--no-save --no-restore --quiet CMD build "∼\Apress_AdvancedR\RFiles\AdvancedRPkg"  \
--no-resave-data --no-manual
* checking for file '∼\Apress_AdvancedR\RFiles\AdvancedRPkg/DESCRIPTION' ... OK
* preparing 'AdvancedRPkg':
* checking DESCRIPTION meta-information ... OK
* checking for LF line-endings in source and make files
* checking for empty or unneeded directories
* looking to see if a 'data/datalist' file should be added
* building 'AdvancedRPkg_0.0.0.9000.tar.gz'
[1] "∼/Apress_AdvancedR/RFiles/AdvancedRPkg_0.0.0.9000.tar.gz"
```

我们可以安装并使用它。但是，再运行一组检查可能会很有帮助。`devtools` 包中的 `check()` 函数会对包运行 `Rcmd check`。参数 `cran = TRUE` 会使用针对 CRAN 的测试。以下代码应在我们的 `chapter06.R` 文件中运行。输出内容非常广泛，因此我们省略了许多按预期工作的部分，并突出显示了一些可能表明我们需要处理的问题的检查。省略的输出用 `[. . .]` 表示。

```
check("AdvancedRPkg", cran = TRUE)
Updating AdvancedRPkg documentation
Loading AdvancedRPkg
[. . .]
* using R version 3.3.1 (2016-06-21)
[. . .]
* checking R code for possible problems ... NOTE
ggplot.lm: no visible global function definition for 'formula'
ggplot.lm: no visible global function definition for 'predict'
ggplot.lm: no visible binding for global variable 'fit'
ggplot.lm: no visible global function definition for 'qnorm'
ggplot.lm: no visible binding for global variable 'se.fit'
meanPlot: no visible global function definition for 'plot'
meanPlot: no visible global function definition for 'points'
show,textplot: no visible global function definition for 'head'
Undefined global functions or variables:
fit formula head plot points predict qnorm se.fit
Consider adding
importFrom("graphics", "plot", "points")
importFrom("stats", "formula", "predict", "qnorm")
importFrom("utils", "head")
to your NAMESPACE file.
[. . .]
* checking examples ... ERROR
Running examples in 'AdvancedRPkg-Ex.R' failed
The error most likely occurred in:
> base::assign(".ptime", proc.time(), pos = "CheckExEnv")
> ### Name: ggplot.lm
> ### Title: Method for plotting linear models
> ### Aliases: ggplot.lm
>
> ### ** Examples
>
> ggplot(
+   lm(mpg ∼ hp * qsec, data = mtcars),
+   aes(hp, mpg, linetype = factor(qsec)),
+   vars = list(
+     hp = min(mtcars$hp):max(mtcars$hp),
+     qsec = round(mean(mtcars$qsec) + c(-1, 1) * sd(mtcars$qsec)), 1)) +
+   geom_ribbon(aes(ymin = LL, ymax = UL), alpha = .2) +
+   geom_line() +
+   theme_bw()
Error: could not find function "ggplot"
Execution halted
* checking for unstated dependencies in 'tests' ... OK
* checking tests ...
Running 'testthat.R'
OK
* DONE
Status: 1 ERROR, 1 NOTE
See
'∼ /Temp/RtmpEnhRD4/AdvancedRPkg.Rcheck/00check.log'
for details.
R CMD check results
1 error  | 0 warnings | 1 note
```

`check()` 函数包含几个步骤。它首先确保文档是最新的，然后构建一个源代码包的 tar 包，接着对其运行 `Rcmd check`。在这里我们可以看到，检查捕获了不少问题。所有问题基本上都可以归结为没有明确告诉 R 需要导入或依赖哪些函数或包。大多数问题源于包和交互式 R 工作方式的差异。在交互式会话中，默认会加载 `base`、`methods`、`graphics`、`utils` 和 `stats` 等包，即使没有显式调用 `library(stats)`。这意味着在交互式会话中，许多函数默认就在搜索路径上。在包中，R 在最近的版本中变得更加严格，要求 `base` 包之外的所需函数必须被显式导入。检查甚至建议我们可以在命名空间中添加什么。我们使用的是 `roxygen2` 包，所以与其将这些添加到命名空间，不如将它们添加到 R 代码文件中。具体来说，在 `AdvancedRPkg/R/plot_functions.R` 中，紧接在 `@export roxygen2` 语句之后，我们为 `meanPlot()` 添加如下内容：

```
##' @importFrom graphics plot points
##' @importFrom stats formula
```

在同一文件中，为 `ggplot.lm()` 方法添加：

```
##' @importFrom stats predict qnorm
```

在 `AdvancedRPkg/R/textplot.R` 中，紧接在 `@param object roxygen2` 语句之后，为 `show()` 方法添加：

```
##' @importFrom utils head
```

此外，对于某些全局变量，也出现了类似的“未找到可见绑定”问题。这来自 `ggplot.lm()` 方法；因为它使用了 `within()`，所以 R 无法判断这些变量已被定义，即使它们是在包含它们的数据框环境中查找的。虽然这是一个有点虚假的提示，但清除所有提示是很好的做法。一种解决方案是在我们的 R 文件中的某个地方添加一个 `globalVariables()` 调用。在 `AdvancedRPkg/R/plot_functions.R` 的顶部，我们添加以下内容：

```
globalVariables(c("fit", "se.fit"))
```

最后指出的问题是，在一个示例中找不到 `ggplot()` 函数。因为我们的包导入了 `ggplot2` 包，所以 `ggplot()` 函数对我们包内部的代码是可用的。然而，如果用户只加载我们的包，`ggplot2` 函数并不会加载到用户的搜索路径上。函数的示例是作为用户运行的，因此该函数不可用，R 会抛出错误。这里的最佳前进路径比较复杂。我们可以省略这个示例，但那样文档就缺少了帮助性。我们可以将 `ggplot2` 从 `DESCRIPTION` 文件的 `Imports` 字段移到 `Depends` 字段。依赖的包会在加载一个包之前被加载。这种方法的缺点是强制用户在使用我们的包时必须加载 `ggplot2`。这增加了用户遇到函数屏蔽的机会，因为它强制加载了许多包。我们也可以确保代码不被运行。最后，我们可以通过添加 `ggplot2::function()` 来显式指向 `ggplot2` 包。这很繁琐，因为我们在那个示例中使用了多个 `ggplot2` 函数，并且需要在整个示例中添加。但是，这确保了示例能够工作，并且避免了将 `ggplot2` 加载到用户的搜索路径上。当然，用户如果愿意，也可以通过自己显式调用 `library(ggplot2)` 来自由加载 `ggplot2`。在 `AdvancedRPkg/R/plot_functions.R` 文件中，我们编辑 `ggplot.lm()` 示例的 `roxygen2` 代码，如下所示：

```
##' @examples
##' ggplot2::ggplot(
##'   lm(mpg ∼ hp * qsec, data = mtcars),
##'   ggplot2::aes(hp, mpg, linetype = factor(qsec)),
##'   vars = list(
##'     hp = min(mtcars$hp):max(mtcars$hp),
##'     qsec = round(mean(mtcars$qsec) + c(-1, 1) * sd(mtcars$qsec)), 1)) +
##'   ggplot2::geom_ribbon(ggplot2::aes(ymin = LL, ymax = UL), alpha = .2) +
##'   ggplot2::geom_line() +
##'   ggplot2::theme_bw()
```

接下来我们重新运行 `check()` 函数。如果一切顺利，我们应该得到一个 OK 的状态，如下所示：

```
check("AdvancedRPkg", cran = TRUE)
Updating AdvancedRPkg documentation
[. . .]
* DONE
Status: OK
R CMD check results
0 errors | 0 warnings | 0 notes
```

此时，我们可以将所有更改提交到 Git 仓库，并与 GitHub 同步。我们也可以安装这个包。首先，打开一个终端，导航到包含我们包的文件夹（不是包目录本身，而是包含包目录的目录）。在支持 R 的终端（例如 Windows 命令提示符，如果你确保安装 R 时已将其添加到系统路径，则可以是任何终端）中，我们可以运行 `R CMD INSTALL`：

```
R CMD INSTALL AdvancedRPkg
```

现在，如果你愿意，`library(AdvancedRPkg)` 在 R 中就可以工作了：

```
library(AdvancedRPkg)
```

你也可以分享由 `devtools` 包的 `build()` 创建的压缩 tar 包。如果你将包放在 GitHub 上，也可以通过运行以下代码轻松安装，将 `ElkhartGroup` 替换为你的用户名，并在 R 控制台（不是操作系统终端！）中运行此代码：

```
library(devtools)
install_github("ElkhartGroup/AdvancedRPkg")
```

如果需要，你可以编辑包的 `README` 文件。这不是必需的，但可以作为一个有用的参考。它是使用 Markdown 标记语言编写的。如果编辑了，它也会显示在 GitHub 上。我们编辑 `README.md` 文件，添加以下文本和标记。简而言之，Markdown 使用不同数量的井号 (`#`) 来表示标题级别：例如，`#` 表示一级标题，`##` 表示二级标题。星号 `*` 之间的文本被强调（斜体）。方括号 `[ ]` 和圆括号 `( )` 用于添加 URL 链接，三重反引号 (\` \` \`) 用于显示代码块的开始和结束。

## AdvancedRPkg

这是一个示例 R 包，伴随*高级 R：数据编程与云端*的第六章发布。
了解更多信息，请查看该[书籍](http://www.apress.com/9781484220764)。

## 安装

您可以通过运行以下命令来安装和测试此包：

```r
library(devtools)
install_github("ElkhartGroup/AdvancedRPkg")
```

最后，如果您愿意，可以将您的包提交到 CRAN。第一步是仔细阅读其当前所有政策，这些政策位于 CRAN 仓库政策网站（[`https://cran.r-project.org/web/packages/policies.html`](https://cran.r-project.org/web/packages/policies.html)）。一旦您确认您的包符合规定并已修正任何不符合之处，即可在[`https://cran.r-project.org/submit.html`](https://cran.r-project.org/submit.html)提交给 CRAN。CRAN 规模庞大，拥有数千个包，每天有海量的更新。它是由志愿者为社区运行的免费服务。因此，即使某些要求可能繁琐，如果您希望将您的包发布在 CRAN 上，遵守其规则并尽一切可能方便他们是唯一公平的做法。否则，GitHub 是一个相对容易托管和分发包源代码的地方。

## 总结

本章涵盖了开发、测试、文档化和发布 R 包的流程。虽然这个过程不一定涉及复杂的 R 代码，但处理好诸多方面并确保所有部分正常协同工作可能具有挑战性。这项工作的回报是用户安装和使用的便利性，以及高质量的文档和对代码按预期运行的保证。如果您计划继续开发 R 包，有用的进一步阅读资源是官方手册《编写 R 扩展》（[`https://cran.r-project.org/doc/manuals/R-exts.html`](https://cran.r-project.org/doc/manuals/R-exts.html)）以及使用`roxygen2`的文档（[`https://cran.r-project.org/web/packages/roxygen2/vignettes/roxygen2.html`](https://cran.r-project.org/web/packages/roxygen2/vignettes/roxygen2.html)）。`roxygen2`的小册子对于本章未涵盖的主题尤其有用，例如格式化文档和排序顺序（当包中的某些类或函数需要在其他类或函数之前加载时，这是必需的）。表 6-3 列出了本章使用的函数摘要。

表 6-3.
本章描述的关键函数

| 函数 | 功能说明 |
| --- | --- |
| `setup()` | 创建一个新包（在我们的示例中，是`AdvancedRPkg`）。 |
| `list.files()` | 如其名所示——相当于 Unix 或 Windows 的`ls`命令。 |
| `load_all()` | 在包构建或安装之前，`library()`无法工作；此函数可临时模拟该过程。 |
| `context()` | 我们用于`test_textplot.R`的`testthat`包的一部分。 |
| `test_that()` | 我们用于`test_textplot.R`的`testthat`包的一部分。 |
| `expect_*()` | 一个用于测试代码的函数家族。这些函数检查输出是否符合特定预期，例如输出的类别、代码是否返回错误或警告等等。有关完整列表，请在加载`testthat`包后，在控制台运行`apropos("expect_")`。 |
| `apropos()` | 用于搜索部分记住的函数名很有用。 |
| `test()` | 运行`testthat`测试；在我们的`chapter06.R`文件中运行。 |
| `package_coverage()` | 计算包的测试覆盖率；通常目标是 100%。 |
| `document()` | 用于为 R 包构建`roxygen2`文档。 |
| `build()` | 用于构建 R 包。 |
| `check()` | 用于检查 R 包；我们在示例中使用了 CRAN 选项。 |

本章也是本书中最后一个专门关注 R 编程及其软件开发工具的章节。本书的剩余部分将重点介绍高级层面使用 R 进行数据管理和应用分析。虽然我们会运用您在本部分学到的许多 R 编程知识并编写许多函数，但在后续章节中我们不会开发任何新的包。