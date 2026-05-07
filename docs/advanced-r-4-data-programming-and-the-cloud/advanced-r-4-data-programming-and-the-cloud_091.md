# 动态报告

在前面的章节中，你已经了解了 Shiny 如何基于动态数据提供交互式环境。动态报告与实时仪表盘有许多相似之处。另一方面，本章由于最终报告是 PDF 格式，因此交互性较弱。

在许多领域，报告是日常工作中不可或缺的一部分，利益相关者通常需要时间点的快照，而非完全交互式的环境。通过 `knitr` 和 `rmarkdown` 包，我们可以基于数据输入创建文档（例如 PDF、HTML 或 Microsoft Word）。对于基于持续变化的数据构建、但总体结构相同的定期报告，这能极大地节省时间。

`rmarkdown` 包 [42] 增加了将 R 进程嵌入此类文档的能力，实现一键式分析，并使用多种样式进行精美排版。LaTeX 接口几乎可以实现任何所需的格式，当然除了 PDF 之外还有其他选项。无论是从学生信息系统数据库实时提取数据进行董事会的注册人数报告，还是根据关键绩效指标生成战略计划行动表，当可变输入需要被塑造成标准化输出时，动态报告效果最佳。`knitr` [38] 和 `formatR` [39] 包也为此提供了便利。

此外，在本章中，我们还使用了 `evaluate` 包 [34] 和 `tufte` 包 [41]。这些包使我们能够更优雅地组织数据的布局和格式。我们还使用了 `ltm` 包 [19] 进行心理测量测试。

本章的目标是：增强我们在本地和云端机器上的软件环境，使动态文档能够编译；探索独立 `rmarkdown` 的结构；开发一个允许用户上传数据并下载 PDF 报告的 Shiny 页面；最后将所有内容推送至我们的云实例。我们从所需的软件开始：

```
options(width = 70, digits = 2)
```

© Matt Wiley and Joshua F. Wiley 2020

M. Wiley and J. F. Wiley, *高级 R 4 数据编程与云端应用*，[`doi.org/10.1007/978-1-4842-5973-3_15`](https://doi.org/10.1007/978-1-4842-5973-3_15#DOI)

第 15 章 动态报告

### 15.1 所需软件

我们已经拥有了大部分所需的软件，即 R、Shiny 包 [6]、PuTTY 以及基于 Ubuntu 的云实例。我们还需要安装一些新的包——无论是在本地机器上还是在云服务器上。



#### 本地机器

首先，在你的 R 控制台中，独立于任何 R 文件运行以下代码很重要：

```R
install.packages("rmarkdown")
install.packages("tufte")
install.packages("knitr")
install.packages("evaluate")
install.packages("ltm")
install.packages("formatR")
library(knitr)
```

### LaTeX 产品

使用 R 生成 PDF 时，最好只让计算机上安装一个 LaTeX 引擎来完成工作。正如前一章所述，LaTeX 有很多选择，包括 `tinytex`（来自 CRAN）和 MiKTeX [21]。如果你已经安装了某个 LaTeX 产品，它应该能与 RStudio 配合使用（至少在 Windows 上如此，你可能需要确保你的 LaTeX 解决方案在全局或用户 `environment PATH` 中）。

如果你还没有这样的系统，MiKTeX 是一个很好的选择，其默认设置和安装选项在大多数配置上通常“可以直接使用”。如果你确实遇到了无法生成 PDF 的情况，根据我们的经验，搜索输出的错误信息通常能找到一些可行的建议。

要安装 MiKTeX 来创建 PDF 文件，请访问 MiKTeX 网站（[`miktex.org/`](http://miktex.org/download)）获取推荐的基础安装程序（32 位）或其他基础安装程序（64 位）。我们在自己的系统上使用 64 位版本，尽管在本章中，不同版本之间可能不会有重大差异。下载后，在安装过程中接受所有默认设置。安装完成后，MiKTeX 软件包可能会有更新。要执行这些更新，请进入你的开始菜单。

在名为 MiKTeX 的文件夹中，你会找到*MiKTeX 控制台*的选项。其中有一个名为*检查更新*的按钮，你应该点击并运行它。虽然可能不是必须的，但我们建议在此安装后重新启动电脑。

#### 云实例

使用 PuTTY，访问你的云实例控制台。我们需要在控制台中逐行运行以下代码。回想一下前一章的内容，这些代码是为该服务器的所有用户安装软件包，而不仅仅是你的用户。这是允许 Shiny 服务器访问正确软件包的关键：

```bash
$ sudo su - -c "R -e \"install.packages('knitr')\""
$ sudo su - -c "R -e \"install.packages('rmarkdown')\""
$ sudo su - -c "R -e \"install.packages('tufte')\""
$ sudo su - -c "R -e \"install.packages('ltm')\""
$ sudo su - -c "R -e \"install.packages('formatR')\""
$ sudo su - -c "R -e \"install.packages('data.table')\""
$ sudo su - -c "R -e \"install.packages('evaluate')\""
```

上述代码不会花很长时间。但是，安装 `texlive`（Ubuntu 上用来替代 MiKTeX 的 LaTeX 解决方案）可能需要一些时间。它也需要相当大的磁盘空间，所以请确保你有足够的空间。在我们的试验中，需要安装完整版本。在云服务器上，就像在你的本地机器上一样，最好不要安装超过一个版本的 LaTeX。因此，如果你使用的云实例还用于其他用途，并且你已经安装了 `tinytex` 软件包，你可能需要试验一下是否可以不使用 `texlive`：

```bash
$ sudo apt-get install texlive-full
$ sudo reboot
```

在此之后，我们建议执行 `sudo reboot` 来重启你的云实例。这是一个执行重启的好时机，然后暂时关闭 PuTTY。在你的服务器重启期间，你可以使用本地机器来探索动态文档。

### 15.2 动态文档

动态文档不一定非要与互联网连接。实际上，大多数报告可能是内部使用的。在 RStudio 中，选择*文件*、*新建文件*、*R Markdown* 会打开一个向导。注意，有用于文档（例如，HTML、PDF 或 Word）以及演示文稿（例如，HTML 或 PDF）的选项。通常需要填写标题和作者。当然，RStudio 并不是必需的；在 R 中创建一个后缀名为 `.Rmd` 的新文件就足够了。

我们打算采用一种混合方式来介绍动态文档。也就是说，我们首先给出一个直接且信息丰富的示例，展示这类代码的样子，然后对代码的每个部分进行更深入的分析。我们从一个名为 `ch15_html.Rmd` 的新文件开始，并用以下代码填充它：

```yaml
title: "ch15_html"
author: "Matt & Joshua Wiley"
date: "29 April 2020"
output: html_document
```

```r
#'{r echo=FALSE}
## 注意 'echo=FALSE'
## 这意味着下面的代码永远不会出现在我们编织生成的 html 中
library(data.table)
diris <- as.data.table(iris)
## 然而，代码仍然会被运行，因此我们仍然可以访问它。
```

