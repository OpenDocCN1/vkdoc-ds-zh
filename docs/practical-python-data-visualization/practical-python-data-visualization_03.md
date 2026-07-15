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

# 第二章：探索 Jupyter Notebook

它会在操作系统默认浏览器中打开一个网页。如果浏览器窗口已打开，它会在同一浏览器窗口的新标签页中打开该页面。另一种打开页面的方式（如果您不小心关闭了此浏览器窗口）是在浏览器中访问 `http://localhost:8888/`，将显示一个类似于图 2-2 所示的页面。

**图 2-2. 使用令牌登录**

令牌可以在服务器日志中找到。以下是包含令牌的服务器日志示例。

要访问笔记本，请在浏览器中打开此文件：
`file:///C:/Users/Ashwin/AppData/Roaming/jupyter/runtime/nbserver-8420-open.html`

或者复制并粘贴以下任一 URL：
`http://localhost:8888/?token=e4a4fab0d8c22cd01b6530d5daced19d32d7e0c3a56f925c`
或者
`http://127.0.0.1:8888/?token=e4a4fab0d8c22cd01b6530d5daced19d32d7e0c3a56f925c`

在这个日志中，我们可以看到几个 URL。它们指向同一个页面（`localhost` 和 `127.0.0.1` 是同一主机）。我们可以直接复制并粘贴这些 URL 中的任意一个到浏览器标签页的地址栏中，打开 Jupyter Notebook 首页，或者像之前提到的那样访问 `http://localhost:8888/`，然后粘贴服务器日志中的令牌（本例中为 `e4a4fab0d8c22cd01b6530d5daced19d32d7e0c3a56f925c`）并登录，这将带我们进入同一个首页。

请注意，每个 Jupyter Notebook 服务器实例都有其自己的令牌，此处的令牌无法用于您的 Jupyter Notebook。该令牌仅对该服务器进程有效。

按照上述任一方法操作后，您将在浏览器窗口中看到一个首页标签页，如图 2-3 所示。

**图 2-3. Jupyter Notebook 的新首页标签页**

可以看到，网页本身有三个标签页：文件、运行中和集群。"文件"标签页显示了我们启动笔记本服务器时所在目录的目录和文件。在此示例中，我从 Raspberry Pi 的 `lxterminal` 执行了命令 `jupyter notebook`。当前工作目录是 pi 用户的主目录（`/home/pi`）。这就是为什么我们在图 2-3 的截图中能够看到我的 RPi 计算机主目录中的所有文件和目录。

在右上角，有退出和注销按钮。如果单击注销按钮，您将从当前会话中注销。要再次登录，您将再次需要令牌或嵌入令牌的 URL（来自笔记本服务器日志），如前所述。如果单击退出，将停止在命令提示符下运行的笔记本服务器进程，并显示如图 2-4 所示的模式消息框。

**图 2-4. 单击退出后显示的消息**

要再次使用它，我们需要在命令提示符下再次执行命令 `jupyter notebook`。

在右上角，退出和注销按钮的下方，有一个小的刷新按钮。单击该按钮可刷新首页。旁边是新建按钮。单击后，会显示一个下拉菜单，如图 2-5 所示。

**图 2-5. 新建笔记本的选项**

如您所见，下拉菜单分为两个部分，笔记本和其他。在此示例中，您可以选择创建 Octave 和 Python 3 笔记本。如果您的计算机安装了更多 Jupyter Notebook 支持的其他编程语言，这些附加语言也会在此处显示。我们还可以创建文本文件和文件夹。我们可以通过单击终端在 Web 浏览器中打开命令提示符。图 2-6 是在单独的 Web 浏览器标签页中运行 `lxterminal` 的截图。

**图 2-6. 浏览器内的新 lxterminal 窗口**

从下拉菜单中选择 Python 3 将创建一个新的 Python 3 笔记本，如图 2-7 所示。

**图 2-7. 一个新的 Python 3 笔记本**

如果单击浏览器中的首页标签页返回首页，然后点击"运行中"标签页，您可以看到对应于终端和 Python 3 笔记本的条目，如图 2-8 所示。

**图 2-8. 当前 Jupyter Notebook 子进程的摘要**

## 在 Jupyter Notebook 中运行代码

再次转到 Python 3 的 Untitled1 标签页，在文本区域（也称为单元格）中输入以下语句：

```
printf("Hello, World!\n");
```

单击运行。Jupyter 将把它作为 Python 3 语句执行，并立即在单元格下方显示结果，如图 2-9 所示。

**图 2-9. Jupyter Notebook 中的代码输出**

如您所见，执行后，它会自动在结果下方创建一个新单元格，并将光标放置在该处。我们来讨论一下菜单栏和编程单元格上方的图标。我们可以通过单击软盘图标来保存文件。我们可以通过单击 `+` 图标在当前单元格之后添加一个新的空单元格。接下来的三个图标分别是剪切、复制和粘贴。向上和向下箭头可以分别上下移动当前单元格的位置。下一个选项是运行单元格，我们已见过。接下来的三个图标用于中断内核、重启内核以及重启内核并重新运行笔记本中的所有单元格。在它旁边，有一个下拉菜单，允许我们选择单元格应为何种类型。图 2-10 是单击下拉菜单时的截图。

**图 2-10. Jupyter Notebook 中的单元格类型**

当您选择代码选项时，该单元格被视为 Python 3 代码单元格。当您选择 Markdown 选项时，它被视为 Markdown 单元格。Markdown 是一种可以创建富文本输出的标记语言。

例如，任何以 `#` 开头的内容都会创建一个标题，以 `##` 开头的内容会创建副标题，以此类推。只需在 Markdown 单元格中输入以下行并执行它：

```
# 标题 1
## 标题 2
```

在我们的 Python 3 演示中，我们主要使用 Markdown 单元格作为标题。但是，您可以自行进一步探索 Markdown。您可以在 [`jupyter-notebook.readthedocs.io/en/stable/examples/Notebook/Working%20With%20Markdown%20Cells.html`](https://jupyter-notebook.readthedocs.io/en/stable/examples/Notebook/Working%20With%20Markdown%20Cells.html) 找到更多相关信息。前述演示的输出如图 2-11 所示。

**图 2-11. Markdown 中的标题**

您甚至可以通过单击笔记本顶部的名称来更改笔记本文件的名称。单击后，会显示一个用于重命名的模式框，如图 2-12 所示。

**图 2-12. 在 Jupyter 中重命名笔记本**

如果您愿意，可以重命名它。如果我们浏览在命令提示符下启动 Jupyter Notebook 时的磁盘位置，我们会找到扩展名为 `ipynb` 的文件，它代表 IPython Notebook。

同样，我们可以将 Jupyter Notebook 用于支持 Jupyter 的其他编程语言进行交互式编程。

# 第 2 章：探索 Jupyter Notebook

我们将主要使用这种笔记本格式来存储交互式会话的代码片段，因为所有内容都保存在单个文件中，如前所述，这便于分享。

## 清除输出

我们可以清除某个单元格或整个笔记本的输出。在菜单栏中，选择 `Cell`。在下拉菜单中，`Current Outputs` 和 `All Output` 都提供了 `Clear` 选项用于清除单元格的输出。图 2-13 是可用选项的截图。

**图 2-13.** 在 Jupyter 中清除输出

Jupyter Notebook 最显著的优势之一是，如果存在语法错误，或者你只是想修改代码，都可以编辑已执行的单元格。Jupyter Notebook 就像一个在网页浏览器中运行的 IDE，并在同一窗口中生成输出。这种交互性以及在同一文件中保存代码、富文本和输出的能力，使 Jupyter Notebook 在全球广受欢迎。用于运行 Python 程序的内核来自 `IPython` 项目。如前所述，我们也可以将其用于其他编程语言。我甚至用它运行过 GNU Octave 程序。

**注意：** 你可以在以下网址找到关于 Jupyter Notebook 和 IPython 的更多信息：

- [jupyter.org/](https://jupyter.org/)
- [ipython.org/](https://ipython.org/)

## 本章小结

在本章中，我们开始在各平台上安装 Jupyter Notebook。接着探讨了如何在 Jupyter Notebook 中运行简单的 Python 语句。我们了解到，可以将代码及其输出存储在单个文件中，便于在互联网上分享。

在下一章中，我们将使用 Jupyter Notebook，结合 Python 和一款流行的数据可视化库 `leather`，进行简单的可视化操作。

