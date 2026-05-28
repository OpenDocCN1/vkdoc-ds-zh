# 第 2 章：探索 Jupyter Notebook

在下一章中，我们将学习如何安装 `Jupyter Notebook`，并简要了解 `Jupyter Notebook` 的使用方法。

在第 1 章中，我们熟悉了 Python，并学会了如何用 Python 编写一个非常简单的程序。我们还了解了如何在交互模式和脚本模式下使用 Python。本章将探索 `Jupyter Notebook`。在第 1 章中，我们看到交互模式能为我们提供 Python 语句的即时反馈。本书中几乎所有演示都将继续使用 Python 的交互模式。不过，我们将不再使用 Python 内置的基于解释器的交互模式，而是使用另一种更强大的工具，即 `Jupyter` 工具。本章的全部内容都将围绕这一主题展开。

你将学习本章的以下内容：

- `Jupyter Notebook` 概述。
- 安装 `Jupyter Notebook`。
- 在 `Jupyter Notebook` 中运行代码。

完成本章学习后，你应该能够熟练使用 `Jupyter Notebook` 进行 Python 编程。

© Ashwin Pajankar 2021
A. Pajankar，*实用 Python 数据可视化*，
[`doi.org/10.1007/978-1-4842-6455-3_2`](https://doi.org/10.1007/978-1-4842-6455-3_2#DOI)

## `Jupyter Notebook` 概述

在第 1 章中，你学习了运行 Python 语句的多种方法。我们在脚本中以及解释器的交互模式下运行了 Python 语句。使用交互模式的主要优势在于能获得即时反馈。这种模式的主要缺点是，如果在输入语句时犯错并执行了错误语句，我们必须重新输入整条语句才能再次执行。此外，将其保存为程序也比较困难。在解释器的“文件”菜单选项中可以找到保存已执行语句的选项。但是，所有语句及其输出将以纯文本格式保存为`.py`扩展名的文件。

如果存在图形输出，它会单独显示，无法与语句一并存储。

由于解释器交互模式存在这些局限性，我们将使用一种更好的工具来在网页浏览器中交互式地运行 Python 语句：`Jupyter Notebook`。`Jupyter` 是一个服务器程序，可以在网页浏览器中创建交互式笔记本。

`Jupyter`笔记本是一种基于 Web 的笔记本，用于使用包括 Python、Octave、Julia 和 R 在内的多种语言进行交互式编程。它在科研领域非常受欢迎。`Jupyter`笔记本可以将代码、可视化结果、输出以及富文本整合到单个文件中。`Jupyter Notebook` 相较于 Python 自带的交互式提示符的优势在于，用户可以编辑代码并立即看到新的输出，这在 Python 交互模式下是无法实现的。另一个优势是，我们将代码、富文本元素以及代码的输出（可能是图形或富文本格式）保存在磁盘上的同一个文件中。这使得分发变得非常容易。我们可以通过互联网或便携式存储设备保存并分享这些笔记本。此外，还有许多在线服务可以帮助我们在云服务器上存储和执行 `Jupyter Notebook` 脚本。

## 安装 `Jupyter Notebook`

我们可以轻松地在任何计算机上安装 `Jupyter Notebook` 服务器程序，只需在命令提示符下运行以下命令：

```
pip3 install jupyter
```

让我们探索如何使用 `Jupyter Notebook` 来编写和执行 Python 语句。在操作系统命令提示符下运行以下命令，以启动 `Jupyter Notebook` 服务器进程：

```
jupyter notebook
```

一旦 `Jupyter Notebook` 服务器进程启动，命令提示符窗口将显示服务器日志，如图 2-1 所示。

**图 2-1.** 启动新的 Jupyter Notebook 进程