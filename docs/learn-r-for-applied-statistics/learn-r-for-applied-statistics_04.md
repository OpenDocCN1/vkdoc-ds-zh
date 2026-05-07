# 2. 入门指南

R 语言是一种具有面向对象特性的编程语言，非常适合统计计算和数据可视化。R 语言可以执行描述性统计、推断性统计以及回归分析。R 语言是一个 GNU 软件包，也是一款命令行应用程序。`RStudio` 是 R 语言的集成开发环境（IDE）。IDE 通过提供代码编辑器、编译器和调试器等功能，帮助你更轻松、更高效地编写代码。代码编辑器通常具备语法高亮和智能代码补全功能。

在本章中，你将探索 R 语言命令行应用程序和 `RStudio` IDE，并在你的计算机上安装 R 和 `RStudio`。你将了解什么是 IDE，并探索 `RStudio` 的界面。`RStudio` 和 R 可以轻松读取 `.csv` 文件，执行一些描述性统计，并绘制简单的图表。

## 什么是 R？

R 语言用于统计计算，并得到 R 统计计算基金会的支持。许多学者和研究人员使用 R 语言进行数据和统计分析，并且 R 语言的受欢迎程度随着时间的推移而不断上升。

R 是一个 GNU 软件包，遵循 GNU 通用公共许可证发布，可以认为它在某种程度上是免费且开源的。R 以命令行应用程序的形式提供，如图 2-1 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig1_HTML.jpg](img/471585_1_En_2_Fig1_HTML.jpg)

图 2-1

RGui 界面

R 语言是 S 编程语言的一种实现，其库包含统计和数据可视化技术，并且可以执行描述性统计、推断性统计和回归分析。你将探索 R 语言命令行应用程序与 `RStudio` IDE 之间的区别，以及描述性统计特性和数据可视化特性的基础知识。

## 集成开发环境

IDE 是一种帮助程序员更轻松、更高效地开发软件的软件应用程序。IDE 由代码编辑器、编译器和调试器工具组成。代码编辑器通常提供语法高亮和智能代码补全功能。

有些 IDE（如 `NetBeans`）还包含解释器，而其他 IDE（如 `SharDevelop`）则没有。有些 IDE 包含版本控制系统和图形用户界面（GUI）构建器等工具，许多 IDE 还包含类和对象浏览器。

IDE 的开发目的是通过整合代码编辑器、编译器、调试器和解释器等功能来提高开发者的生产力。这与 `VI` 和 `NotePad++` 等编程代码文本编辑器不同，后者虽然提供语法高亮，但通常不与调试器和编译器进行通信。

IDE 的起源可以追溯到早期系统中将穿孔卡片提交给编译器的时代。Dartmouth BASIC 是第一个随 IDE 一起创建的编程语言。后来，慕尼黑的 Softlab 公司创建了 Maestro I，它可以被认为是 20 世纪 70 年代到 80 年代之间第一个完整的 IDE。Maestro I 可以在弗吉尼亚州阿灵顿的信息技术博物馆中找到。后来创建的 Softbench IDE 支持插件。如今，Visual Studio、`NetBeans` 和 Eclipse 是最著名的 IDE。R 语言的 IDE 是 `RStudio`，图 2-2 展示了它的智能代码补全功能。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig2_HTML.jpg](img/471585_1_En_2_Fig2_HTML.jpg)

图 2-2

RStudio IDE 的智能代码补全

## RStudio：R 语言的 IDE

在 R 语言中，`RStudio` 是最流行的 IDE。`RStudio` 拥有一个包含语法高亮和智能代码补全功能的代码编辑器。`RStudio` 还有一个工作区，显示所有变量和历史记录。你可以双击变量，通过表格和其他选项来查看它们。

R 控制台位于 `RStudio` 中，因此你可以在运行脚本后查看 R 脚本的结果；你也可以在 R 控制台中输入 R 代码来进行一些简单的计算。`RStudio` 中提供了“绘图和其他”区域，让你可以查看由 R 脚本绘制的图表和图形。“绘图和其他”区域允许你轻松保存图形和图表。图 2-3 展示了 `RStudio` IDE 的界面。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig3_HTML.jpg](img/471585_1_En_2_Fig3_HTML.jpg)

图 2-3

RStudio IDE 界面



## 安装 R 和 RStudio

为了编写 R 脚本，你必须安装 R 编程命令行应用程序。你可以从 [`www.r-project.org/`](http://www.r-project.org/) 下载 R 编程命令行应用程序，如图 2-4 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig4_HTML.jpg](img/471585_1_En_2_Fig4_HTML.jpg)

图 2-4

R 项目网站

在本书中，你将下载适用于 Windows 的 R。你也可以下载适用于 Linux 和 Mac OS 的版本，如图 2-5 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig5_HTML.jpg](img/471585_1_En_2_Fig5_HTML.jpg)

图 2-5

为不同操作系统选项下载 R 基础版本

要安装该软件，请双击下载的安装文件，并按照安装程序的指示安装 R 编程命令行应用程序，如图 2-6 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig6_HTML.jpg](img/471585_1_En_2_Fig6_HTML.jpg)

图 2-6

安装 R

安装完 R 编程命令行应用程序后，你可以启动它，如图 2-7 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig7_HTML.jpg](img/471585_1_En_2_Fig7_HTML.jpg)

图 2-7

RGui 界面

你可以使用 `print()` 函数创建自己的 Hello World 应用程序。Hello World 应用程序是学习编程语言时开发的标准入门程序。在 RGui 中输入以下代码：

```
print("Hello World");
```

`print()` 函数用于在控制台屏幕上打印一些文本。你可以打印除图 2-8 中显示的 "Hello World" 之外的任何文本。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig8_HTML.jpg](img/471585_1_En_2_Fig8_HTML.jpg)

图 2-8

R 的 "Hello World" 应用程序

RStudio 是 R 编程语言最流行的集成开发环境（IDE）。RStudio 帮助你更轻松、更高效地编写 R 编程代码。要下载并安装 RStudio，请访问 [`www.rstudio.com/`](http://www.rstudio.com/)，如图 2-9 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig9_HTML.jpg](img/471585_1_En_2_Fig9_HTML.jpg)

图 2-9

RStudio IDE 网站

下载最新版本。对于本书，你将下载 64 位 Windows 版本。下载完 RStudio 安装程序或安装文件后，双击该文件以安装 RStudio IDE，如图 2-10 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig10_HTML.jpg](img/471585_1_En_2_Fig10_HTML.jpg)

图 2-10

安装 RStudio IDE

安装完 RStudio IDE 后，你可以运行 RStudio IDE 软件，如图 2-11 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig11_HTML.jpg](img/471585_1_En_2_Fig11_HTML.jpg)

图 2-11

RStudio IDE 界面

在运行脚本之前，你需要选择要使用的 R 编程命令行应用程序版本。点击“工具” ➤ “全局选项”，如图 2-12 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig12_HTML.jpg](img/471585_1_En_2_Fig12_HTML.jpg)

图 2-12

RStudio IDE 的“工具”菜单

点击“更改”按钮以选择 R 版本，如图 2-13 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig13_HTML.jpg](img/471585_1_En_2_Fig13_HTML.jpg)

图 2-13

RStudio IDE 选项

对于初学者，请选择图 2-14 中所示的 R 版本。如果你将来想更改 R 版本，可以使用此方法进行更改。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig14_HTML.jpg](img/471585_1_En_2_Fig14_HTML.jpg)

图 2-14

“选择 R 安装”对话框

点击“确定”并选择 R 版本后，你必须重启 RStudio IDE，如图 2-15 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig15_HTML.jpg](img/471585_1_En_2_Fig15_HTML.jpg)

图 2-15

RStudio IDE 的 R 版本更改

重启 RStudio 后，“控制台”选项卡应显示所选的 R 版本，如图 2-16 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig16_HTML.jpg](img/471585_1_En_2_Fig16_HTML.jpg)

图 2-16

已更改的 R 版本



## 在 R 和 RStudio 中编写脚本

你可以使用 R 编程语言中的 `read.csv()` 函数读取逗号分隔值（CSV）文件，如图 2-17 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig17_HTML.jpg](img/471585_1_En_2_Fig17_HTML.jpg)

图 2-17：RGui：读取 CSV 文件

```
myData <- read.csv(file="D:/data.csv", header=TRUE, sep=",");
myData;
```

在 R 编程语言中，你可以使用 `summary()` 函数获取所有变量的基本描述性统计信息。我将在本书后续章节中讨论图 2-18 所示的描述性统计。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig18_HTML.jpg](img/471585_1_En_2_Fig18_HTML.jpg)

图 2-18：RGui：`summary()` 函数

```
summary(myData);
```

在 R 编程语言中，你可以使用 `plot()` 函数绘制散点图，如图 2-19 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig19_HTML.jpg](img/471585_1_En_2_Fig19_HTML.jpg)

图 2-19：RGui：绘制图表

```
plot(myData$x, myData$x2);
```

RStudio 是一个集成开发环境（IDE），为 R 编程命令行应用程序提供图形用户界面。RStudio 为 R 编程语言提供单词建议和语法高亮功能。R 编程语言的 RStudio IDE 如图 2-20 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig20_HTML.jpg](img/471585_1_En_2_Fig20_HTML.jpg)

图 2-20：RStudio IDE 界面

使用 RStudio，你可以将所有代码写入代码编辑器并运行脚本，如图 2-22、2-23 和 2-24 所示。

```
myData <- read.csv(file="D:/data.csv", header=TRUE, sep=",");
myData;
summary(myData);
plot(myData$x, myData$x2);
```

当你输入代码时，RStudio 会显示智能代码补全，如图 2-21 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig21_HTML.jpg](img/471585_1_En_2_Fig21_HTML.jpg)

图 2-21：RStudio IDE 智能代码补全

你必须选中代码编辑器中的所有 R 代码，然后点击“运行”或按 `Ctrl + Enter` 来运行脚本（图 2-22）。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig22_HTML.jpg](img/471585_1_En_2_Fig22_HTML.jpg)

图 2-22：RStudio IDE：运行脚本

或者，你可以点击“代码” ➤ “运行区域” ➤ “全部运行”（图 2-23）。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig23_HTML.jpg](img/471585_1_En_2_Fig23_HTML.jpg)

图 2-23：RStudio IDE：运行脚本

结果如图 2-24 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig24_HTML.jpg](img/471585_1_En_2_Fig24_HTML.jpg)

图 2-24：RStudio IDE：运行 R 脚本后的结果

RStudio IDE 在代码编辑器中提供语法高亮功能。当你运行 R 脚本时，可以在“控制台”选项卡中查看结果，并在“绘图”选项卡中查看散点图。通过双击“全局环境”选项卡中的 `myData`，你可以查看从 `.csv` 文件加载的数据，如图 2-25 所示。

![../images/471585_1_En_2_Chapter/471585_1_En_2_Fig25_HTML.jpg](img/471585_1_En_2_Fig25_HTML.jpg)

图 2-25：RStudio IDE：查看已加载的数据

## 结论

在本章中，你了解了 R 编程。现在你明白了什么是 R 编程：它是一种用于统计计算的编程语言，由 R 统计计算基金会支持。研究人员使用 R 编程进行统计分析，并且 R 的受欢迎程度逐年上升。

你还探索了 RStudio。RStudio 是 R 编程语言的 IDE，它具有语法高亮和智能代码补全功能，可帮助你更轻松、更高效地编写 R 脚本。你还了解了 R 如何读取 `.csv` 文件、执行描述性统计和数据可视化，并探讨了它们之间的区别。

你还安装了 R 和 RStudio IDE，并了解了如何让 RStudio IDE 与 R 编程命令行应用程序集成。

你还了解到，IDE 是一种帮助你更轻松、更高效地编写代码的软件。IDE 通常提供语法高亮和智能代码补全功能，并包含代码编辑器、编译器和调试器。

对于 R 编程，RStudio 是最流行的 IDE。RStudio 有一个包含语法高亮和智能代码补全功能的代码编辑器。RStudio 还有一个工作区，显示所有变量和历史记录。你可以双击变量以使用表格等方式查看它们。R 控制台位于 RStudio 中，因此你可以查看 R 脚本的结果。RStudio 中提供“绘图和其他”部分，让你查看由 R 代码绘制的图表和图形。

