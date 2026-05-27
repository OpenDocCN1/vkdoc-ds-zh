# 2. 安装包和使用库

你已经安装了 `R` 和 RStudio 并熟悉了它们。现在是时候发现 `R` 的另一个有助于你学习统计学的功能了。

在第 1 章中，你的第一个 `R` 脚本使用了函数 `plot(x = mtcars$wt, y = mtcars$mpg)`。然后，像变魔术一样，一个图形出现在你右下角的绘图面板中——但这并不是魔术。在幕后，你其实悄悄使用了 `graphics` 包。

什么是包？当一个程序员编写了打算多次使用的代码时，他们通常会为这些代码取一个名字，我们称这些名字为 `函数`。一个 `R` `包` 通常是一个或多个为解决常见或相似任务而收集在一起的函数。通过将这些函数收集在一个包中，该包可以与许多人共享。请记住，`R` 是免费的，并且被世界上许多顶尖研究人员使用。每个人都有自己的技能来解决特定类型的问题。通过在包中分享他们的函数，我们都能共同受益。

这正是你使用 `plot()` 函数时发生的事情。一些程序员（在这个例子中是 `R` 核心开发团队）聚在一起，在一个脚本中编写了一些用于创建图形的函数（包括 `plot` 函数）。他们将所有这些代码放在 `graphics` 包中。

使用 `plot()` 不需要你付出任何额外工作，因为有些包非常流行，默认就包含在 `R` 中。然而，大多数包并非默认包含。并非所有人都需要所有包；如果全部包含，下载 `R` 的时间会变得太长。相反，包保存在 CRAN 上，需要时即可下载。包的内容比我们这里介绍的更丰富。如果你想了解更多关于包的信息（或许还想编写自己的包），请参阅《Advanced R: Data Programming and the Cloud》以获取详细信息。

到本章结束时，你应该能够：

*   理解包是为 `R` 添加额外功能的一种方式。
*   使用 `install.packages()` 函数为 `R` 安装附加包。
*   使用 `library()` 函数为你的 `R` 脚本激活包。
*   理解何时从 CRAN 安装包，以及何时使用本地库。

如果你已经了解包，并渴望开始学习统计学，请继续安装表 2-1 中的这些包，然后进入下一章。如果你希望跟随我们一起了解你将在本书中使用的这些包，请继续阅读本章。

**表 2-1 包列表**

| 包 | 用途 |
| --- | --- |
| `haven` | 输入和输出来自 SPSS、Stata 和 SAS 的常见统计文件类型。 |
| `readxl` | 将 Microsoft .xlsx 文件输入到 `R`。 |
| `writexl` | 从 `R` 输出 Microsoft .xlsx 文件。 |
| `data.table` | 高效、便捷地管理数据/行/列。 |
| `extraoperators` | 一个包含运算符的 R 包，用于加速日常任务。 |
| `JWileymisc` | 一个包含 `aces_daily` 模拟数据的 R 包。 |
| `ggplot` | 用于探索性数据分析（EDA）的可定制可视化、图形和图表。 |
| `visreg` | 回归模型的可视化。 |
| `emmeans` | 汇总统计模型。 |
| `ez` | 因子实验的简便分析和可视化（我们将用于方差分析）。 |
| `palmerpenguins` | 一个关于企鹅的新鲜有趣的数据集（目前在 GitHub 上）。 |

## 2.1 安装包

一个包 `每台电脑` 只需安装一次。换句话说，每次开始新项目时都不应重新安装。在学习这么多新知识时，很难记住什么时候该做什么。因此，如果你和我们一样使用同一台电脑学习 `R` 语言，那么每个包只需要安装一次。无论是在项目中安装包，还是直接在控制台窗格中安装，都没有关系。无论哪种方式，包都会被安装到你的电脑上，并且这些功能/函数将可用于你在这台电脑上曾经拥有、正在拥有或将要拥有的任何项目。话虽如此，如果你换了一台新电脑并希望在那台新机器上运行脚本，你当然需要在那台新机器上重新安装你使用的包。

关于包，还有一点需要了解：它们通常 `依赖` 于其他包。因此，在安装每个包时，你可能会发现，尽管计划只安装一个包，但在控制台窗格中看到 `R` 实际上正在安装好几个包。这都是包安装过程中的自然现象，无需担心。`R` 足够智能，能够知道所有需要的依赖项，并且只会安装缺失的包（因此，包的安装顺序可能会有所不同）。

要从 CRAN 安装一个新包，我们使用 `install.packages(pkgs = "")` 函数。`R` 是一种区分大小写的语言，并且 CRAN 强制执行一条规则：任何包都不能与其他包拥有完全相同的名称。但是，必须注意精确地拼写包名；否则，安装函数的搜索功能将无法工作。此外，由于 `R` 区分大小写，还必须完美匹配大小写字母。因此，以下每个小节都不会遵循标准的大写规则。相反，我们选择仅使用完全正确的包名形式。

在逐一安装每个包的过程中，我们还会简要提及其用法以及它为 `R` 基础功能增加了哪些额外特性。

请注意，安装包时，控制台可能会输出大量信息。这看起来可能令人不知所措，但你无需担心。通常，如果出现问题，你会看到关于“error”（错误）或有时“non-zero exit status”（非零退出状态）的信息，这是技术领域中表示至少存在一个（或多个）错误或问题的常见方式。虽然这看起来令人困惑，但其理念源于“零退出状态”表示退出安装过程时没有问题。从这个角度看，“非零退出状态”意味着存在一些错误或问题。如果没有出现错误或“非零退出状态”，那么包安装时显示的所有信息和文本很可能都无需担心。

最后，尽管我们会使用多个不同的附加包，但你不需要了解这些包的所有内容。也就是说，你不需要学习每一个包。我们有时只使用一个包中的几个函数，以便尽可能简单高效地完成任务。为了使用一个函数而安装一个包，却不学习该包中其他（可能很多）函数，这是完全可以接受的。

### haven

在统计计算领域，`R` 是一个越来越受欢迎的新秀。由于 `R` 是新秀，有时需要从诸如 SPSS、Stata 或 SAS 等经典程序导入数据。`haven` 包 [19] 提供了从这些来源读取数据到 `R` 的函数。

取决于你的电脑，如果系统之前从未安装过 `R` 或任何包，你可能会发现安装的不仅仅是 `haven` 包。这是因为 `haven` 本身依赖于其他一些包。同样，如果在运行 `install.packages(pkgs = "haven")` 后，你看到输出的最后部分与我们的相符，那么你的第一个外部包安装就成功了。我们将在关于数据输入和输出的第 3 章中介绍 `haven` 函数的实际应用。

```
install.packages(pkgs = "haven")
## 正在将包安装到 'C:/Users/.../R/win-library/4.0'
## （因为未指定 'lib'）
## 包 'haven' 成功解压并检查了 MD5 校验和
##
## 下载的二进制包位于
##      C:\...\downloaded_packages
```

### readxl

由于 Microsoft Office 软件套件的普及，Excel 格式文件也是常见的数据来源。鉴于其普及性，从 `.xlsx` 文件输入数据可能非常有用。`readxl` 包 [18] 实现了这一点。和之前一样，`R` 可能会自动为你安装一些额外的包作为 `readxl` 的依赖项。还要注意，`R` 函数对其输入可能有点“智能”。在这种情况下，我们利用了 `install.packages()` 函数期望包名作为 **第一个** 输入这一事实。因此，我们不需要显式地写出 `pkgs = "readxl"`。今后，我们会在首次介绍一个函数时使用其正式输入名。首次使用之后，我们仅在必要时才使用正式输入名。

```
install.packages("readxl")
## 正在将包安装到 'C:/Users/.../R/win-library/4.0'
## （因为未指定 'lib'）
## 包 'readxl' 成功解压并检查了 MD5 校验和
##
## 下载的二进制包位于
##      C:\...\downloaded_packages
```

### writexl

除了读取 Excel 文件，在向他人传达结果时，写入 Excel 文件也很有帮助。`writexl` 包 [14] 使我们能够写入 `.xlsx` 文件。

```
install.packages("writexl")
## 正在将包安装到 'C:/Users/.../R/win-library/4.0'
## （因为未指定 'lib'）
## 包 'writexl' 成功解压并检查了 MD5 校验和
##
## 下载的二进制包位于
##      C:\...\downloaded_packages
```

### data.table

将数据输入 `R` 后，在能够执行统计分析之前，我们通常需要先对数据进行一些处理。这可以从计算汇总表到仅提取可用数据的一个子集。为了让我们能够轻松处理大量数据（并非所有数据集都像 `mtcars` 那样短小），我们使用 `data.table` 包 [9] 来操作或“清洗”数据。

```
install.packages("data.table")
## 正在将包安装到 'C:/Users/.../R/win-library/4.0'
## （因为未指定 'lib'）
## 包 'data.table' 成功解压并检查了 MD5 校验和
##
## 下载的二进制包位于
##      C:\...\downloaded_packages
```

### extraoperators

在处理较大数据集时，研究较小的信息子集可能会很有用。虽然基础 `R` 有能力轻松地将多个条件串联起来进行这种子集划分，但阅读这样的代码并不总是清晰的。此外，这样的代码可能很长。`extraoperators` 包 [20] 能节省按键次数，并使各种逻辑比较不那么复杂。

```
install.packages("extraoperators")
## 正在将包安装到 'C:/Users/.../R/win-library/4.0'
## （因为未指定 'lib'）
## 包 'extraoperators' 成功解压并检查了 MD5 校验和
##
## 下载的二进制包位于
##      C:\...\downloaded_packages
```

### JWileymisc

`JWileymisc` 包 [21] 包含一个基于 12 天研究的模拟数据集（`aces_daily`），其中记录了参与者自我报告的压力和情绪等指标。参与者每天报告三次，但如同真实研究（或其模拟）中常见的情况，可能存在缺失数据。该数据集帮助我们理解超越常见“课堂完美”示例的统计方法。

```
install.packages("JWileymisc")
## Installing package into 'C:/Users/.../R/win-library/4.0'
## (as 'lib' is unspecified)
## package 'JWileymisc' successfully unpacked and MD5  sums checked
##
## The downloaded binary packages are in
##      C:\...\downloaded_packages
```

### ggplot2

你的第一个程序使用了基础图形包绘制图表。我们常常需要更定制化或更高级的图形。正如 `R` 是一种统计编程语言，`ggplot2` 包 [17] 使用了所谓的“图形语法”。

```
install.packages("ggplot2")
## Installing package into 'C:/Users/.../R/win-library/4.0'
## (as 'lib' is unspecified)
## package 'ggplot2' successfully unpacked and MD5 sums checked
##
## The downloaded binary packages are in
##      C:\...\downloaded_packages
```

### visreg

一种常见的统计方法是将数据进行分析，并找出模型输入或预测变量与输出或目标变量之间的数学关系。这使研究者能够理解可控或可调节的自变量与因变量之间的关系。我们之前通过一个简单的示例看到了这一点：用汽车重量预测每加仑英里数这一因变量。`visreg` 包 [7] 使可视化模型的这些部分变得非常容易。借助恰当的可视化图表，可以更容易地理解统计概念，该包将帮助你理解模型工作原理，并有效传达分析结果。

```
install.packages("visreg")
## Installing package into 'C:/Users/.../R/win-library/4.0'
## (as 'lib' is unspecified)
## package 'visreg' successfully unpacked and MD5 sums checked
##
## The downloaded binary packages are in
##      C:\...\downloaded_packages
```

### emmeans

`emmeans` 包 [13] 帮助我们汇总统计模型并进行后续检验，从而以易于解释的方式呈现结果。我们现在就来安装这个包。

```
install.packages("emmeans")
## Installing package into 'C:/Users/.../R/win-library/4.0'
## (as 'lib' is unspecified)
## package 'emmeans' successfully unpacked and MD5 sums checked
##
## The downloaded binary packages are in
##      C:\...\downloaded_packages
```

### ez

我们的倒数第二个包是 `ez` 包 [12]，它帮助我们执行一种称为方差分析（ANOVA）的统计方法。我们在本书末尾附近讨论 ANOVA。尽管如此，我们现在就来安装这个包。

```
install.packages("ez")
## Installing package into 'C:/Users/.../R/win-library/4.0'
## (as 'lib' is unspecified)
## package 'ez' successfully unpacked and MD5 sums checked
##
## The downloaded binary packages are in
##      C:\...\downloaded_packages
```

### palmerpenguins

我们最后一个包提供数据，而非统计计算。`palmerpenguins` 包 [11] 包含一个广受欢迎的企鹅数据集！

```
install.packages("palmerpenguins")
## Installing package into 'C:/Users/.../R/win-library/4.0'
## (as 'lib' is unspecified)
## package 'palmerpenguins' successfully unpacked and MD5  sums checked
##
## The downloaded binary packages are in
##      C:\...\downloaded_packages
```

## 2.2 使用包

既然所有这些包都已安装完毕，现在是时候检查安装是否成功了。你可能还记得，每个电脑上只需安装一次包。然而，每次你想使用某个包中的函数时，都必须让 `R` 知道要搜索哪些包。这是因为当你使用一个函数（记住，函数是执行特定任务的特定代码的名称）时，`R` 需要将你输入的函数名称与执行该工作的代码匹配起来。为了避免浪费时间搜索你可能已安装的所有包（我们的电脑上有几百个），我们通过调用 `library()` 来告诉 `R` 在特定脚本中应搜索哪些包。每次在脚本窗格中启动脚本时都需要运行此操作。因此，惯例是将此调用放在代码的顶部附近。这可以提醒你脚本的其他用户：如果他们未安装这些包中的某一个，则必须在运行你的代码之前安装它。

在每个会话中运行一次 `library()` 调用就足够了。每次启动 RStudio 并打开一个项目时，都需要运行所有必要的 `library()` 调用。运行这些调用非常简单，从本章开始，我们将在每章的开头列出该章代码所需的所有 `library()` 调用。为了展示一次成功的调用是什么样子，我们运行 `readxl` 包：

```
library(readxl)
```

正如你所见，这并没有什么特别的。如果包已经安装，一次正常的 `library()` 调用通常不会显示任何文本。不过，有时一次成功的调用可能会提供少量信息，比如 `data.table` 包就是这样：

```
library(data.table)
## data.table 1.13.0 using 6 threads (see ?getDTthreads). Latest news: r-datatable.com
```

相反，尝试使用一个未安装的包会导致错误：

```
library(fake)
## Error in library(fake): there is no package called 'fake'
```

现在，你已经完成了本书后续内容所需的所有后台软件和包的安装。恭喜！从现在开始，我们将首先学习一些代码，然后进入全面的统计学内容。

## 2.3 总结

在本书中，每章都会以一个类似表 2-2 的总结结束，表中按介绍顺序详细列出了一些可能对快速参考有用的事实。

**表 2-2** 章节总结

| 概念 | 含义 |
| --- | --- |
| `install.packages(pkgs = "")` | 安装指定的包；每台电脑只需运行一次。 |
| `library()` | 将包加载到内存中供脚本使用；每个会话只需运行一次。 |

## 2.4 掌握练习

通过完成一些练习来检查你的进度并在练习中成长。理解力检查提出了一些批判性思考问题，最好以书面或口头形式回答。统计学的艺术之一在于向利益相关者或受众成功传达结果。有时受众是高度技术性的，而有时则非常非技术性。练习题则是对本章所探讨概念更直接的应用。

### 理解力检查

1. 假设你将自己编写的`R`代码分享给一位朋友。你的朋友是否需要在你代码所使用的电脑上安装这些包？

### 练习题

1. 访问 [https://rdatatable.gitlab.io/data.table/](https://rdatatable.gitlab.io/data.table/)，并从包作者处下载`data.table`速查表。
2. 使用你选择的搜索引擎，搜索短语“`R ggplot2`”，看看能否找到一份速查表。