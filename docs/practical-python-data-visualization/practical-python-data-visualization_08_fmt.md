# Python 版本与本书约定

Python 2.x 版本已退役，不再受支持。此外，`Python 3` 与 `Python 2` 不向后兼容。`Python 3` 是语言的最新且当前受支持的版本。因此，我们在本书中全程使用 `Python 3` 来演示数据可视化的程序。

除非特别说明，本书中提到的 `Python` 均指代 `Python 3`。

## 第 1 章 Python 入门

### Python 增强提案

为了引导 `Python` 的开发、维护和支持，`Python` 领导团队提出了 `Python` 增强提案（`PEP`）的概念。这是为 `Python` 项目建议新特性和修复问题的主要机制。您可以在以下 `URL` 中阅读更多关于 `PEP` 的信息：

- [`www.python.org/dev/peps/`](https://www.python.org/dev/peps/)

- [`www.python.org/dev/peps/pep-0001/`](https://www.python.org/dev/peps/pep-0001/)

### Python 的哲学

`Python` 的哲学在 `PEP20` 中进行了详细阐述，被称为“Python 之禅”，可在 [`www.python.org/dev/peps/pep-0020/`](https://www.python.org/dev/peps/pep-0020/) 获取。以下是该 `PEP` 中的一些要点：

1.  优美胜于丑陋。
2.  显式胜于隐式。
3.  简单胜于复杂。
4.  复杂胜于凌乱。
5.  扁平胜于嵌套。
6.  稀疏胜于密集。
7.  可读性至关重要。
8.  特殊情况不应特殊到足以违反规则。
9.  尽管实用性胜过纯粹性。
10. 错误不应默默无声地传递。
11. 除非明确需要静默。
12. 面对歧义，拒绝猜测的诱惑。
13. 应该有一种——并且最好只有一种——显而易见的方式来做这件事。
14. 尽管那种方式一开始可能并不明显，除非你是荷兰人。
15. 现在做总比永远不做好。
16. 虽然“永远不做”通常比“*现在*就做”要好。
17. 如果实现难以解释，那可能是个坏主意。
18. 如果实现易于解释，那可能是个好主意。
19. 命名空间是个绝妙的主意——让我们更多地使用它们！

这些是影响 `Python` 编程语言发展并持续发挥作用的一般性哲学指南。

### Python 的应用

由于 `Python` 是一种通用编程语言，它在以下领域有众多应用：

1.  网页开发。
2.  图形用户界面（`GUI`）开发。
3.  科学与数值计算。
4.  软件开发。
5.  系统管理。

有关 `Python` 在各种项目中的案例研究，可在 [`www.python.org/success-stories/`](https://www.python.org/success-stories/) 获取。

![](index-17_1.jpg)

### 在各种平台上安装 Python

`Python` 实现是一个支持执行用 `Python` 编写的程序的程序或环境。由 Van Rossum 创建的原始实现被称为 `CPython`，并作为参考实现。本书中我们全程使用 `CPython`。它可以在 `Python` 官方网站上获得，我们将在本节学习如何安装它。

我倾向于在 `Windows` 电脑或运行 `Raspberry Pi OS` 的 `Raspberry Pi` 电脑上编写 `Python` 程序。让我们学习如何在这两个平台上安装 `Python`。

#### 在 Windows 电脑上安装

访问位于 [`www.python.org/downloads/`](https://www.python.org/downloads/) 的 `Python 3` 下载页面，下载适用于您电脑的 `Python 3` 安装文件。它会自动检测您电脑上的操作系统并显示合适的可下载文件，如图 [1-1] (#index_split_000.html#p17) 所示。

**图 1-1.** 带有下载选项的 Python 项目主页

![](index-18_1.jpg)

运行安装文件以安装 `Python 3`。在安装过程中，选中与将 `Python 3` 添加到 `PATH` 变量（图 1-2）相关的复选框。

**图 1-2.** Python 安装向导

点击“自定义安装”，该选项可提供如图 1-3 所示的定制选项。

![](img/index-19_1.jpg)

**图 1-3.** Python 安装选项

选中所有复选框，然后点击“下一步”继续安装。

完成安装。Python 二进制可执行程序在 Windows 操作系统上的名称是 `python`。安装完成后，在 Windows 命令提示符 `cmd` 中运行以下命令：

```
python -V
```

该命令将返回 Python 3 的版本，如下所示：

```
Python 3.8.1
```

我们也可以按如下方式检查 `pip3` 的版本：

```
pip3 -V
```

`pip` 代表 Pip installs Python 或 Pip installs Packages；其名称是一个递归缩写。它是 Python 编程语言的包管理器。我们可以使用 `pip` 工具为我们的演示安装其他所需的 Python 库。

要确定 Python 的确切位置，可以按如下方式运行 `where` 命令：

```
where python
```

它将返回以下结果：

```
C:\Users\Ashwin\AppData\Local\Programs\Python\Python38-32\
python.exe
```

类似地，我们可以通过运行以下命令来了解 `pip3` 工具的位置：

```
where pip3
```

#### 在 Ubuntu 和 Debian 衍生版上安装

Debian 是一个流行的发行版。Ubuntu Linux 和 Raspberry Pi OS 是其他基于 Debian 的流行发行版。Python 3 和 `pip3` 已预装在所有的 Debian 发行版及其衍生版（如 Ubuntu 或 Raspberry Pi OS）中，因此我们无需单独安装它们。我在一台 4 GB RAM 的 Raspberry Pi 4B 上使用 Raspberry Pi OS。Python 的两个主要版本，Python 2 和 Python 3，都已预装在 Debian 衍生版中。它们的可执行文件分别命名为 `python`（对应 Python 2）和 `python3`（对应 Python 3）。在我们的演示中必须使用 `python3`。

要确定所需二进制可执行文件的版本和位置，请逐一运行以下命令：

```
python3 -V
pip3 -V
which python3
which pip3
```

几乎所有其他流行的 Linux 发行版也预装了 Python。

![](img/index-21_1.jpg)

### Python 模式

Python 有多种模式，我们将逐一讨论。但首先，我们需要了解集成开发与学习环境（IDLE）。这是 Python 软件基金会为 Python 编程开发的一个集成开发环境（IDE）。当我们在 Windows 上安装 Python 3 的 `CPython` 实现时，`IDLE` 也会被安装。

我们可以通过多种方式在 Windows 操作系统上启动它。第一种方式是在 Windows 搜索栏中键入 `IDLE` 进行搜索，如图 1-4 所示。

**图 1-4.** Windows 上的 Python IDLE

![](img/index-22_1.png)

![](img/index-22_2.jpg)

另一种方式是通过在命令提示符（`cmd`）中运行以下命令来启动它：

```
idle
```

这将启动如图 1-5 所示的窗口。

**图 1-5.** Python IDLE

在我们继续之前，需要对其进行配置以便于阅读。我们可以通过选择 `选项` > `配置 IDLE` 来更改字体，如图 1-6 所示。

**图 1-6.** 配置 IDLE

这时会打开如图 1-7 所示的窗口。你可以在其中更改 `IDLE` 中字符的字体和大小。

![](img/index-23_1.png)

**图 1-7.** IDLE 配置

![](img/index-24_1.jpg)

根据你的个人偏好调整字体设置。

并非所有 Linux 发行版都预装了 `IDLE`。我们可以通过依次运行以下命令在 Debian 及其衍生版（Ubuntu 和 Raspberry Pi OS）上安装它。

```
sudo apt-get update
sudo apt-get install idle3
```

安装完成后，我们可以在菜单中找到 IDLE（此处为 Raspberry Pi OS 菜单），如图 1-8 所示。

![图 1-8. Raspberry Pi OS 菜单中的 IDLE](img/index-25_1.png)