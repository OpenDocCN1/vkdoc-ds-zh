# 第 3 章：使用 Leather 进行数据可视化

在第 2 章中，我们使用`Jupyter Notebook`熟悉了`Python`编程。现在，你应该能熟练地使用`Jupyter Notebook`编写交互式`Python`程序了。

在本章中，我们将使用`Jupyter Notebook`和`Python`，并结合数据可视化库`leather`来生成基本的可视化图表。以下是本章将涵盖的主题列表：

- `leather`简介

- 更多可视化类型

- 比例

- 样式设置

## 在 Jupyter Notebook 中运行操作系统命令

你已经了解了如何使用`Jupyter Notebook`进行`Python`编程。现在我们将探讨如何在笔记本中执行操作系统命令。这很有用，因为我们需要使用`pip`来安装许多实用程序，我们可以在笔记本中直接运行它。

© Ashwin Pajankar 2021  

A. Pajankar, *实用 Python 数据可视化*,  

[`doi.org/10.1007/978-1-4842-6455-3_3`](https://doi.org/10.1007/978-1-4842-6455-3_3#DOI)

---

第 3 章：使用 Leather 进行数据可视化

为本章所有代码创建一个新的`Jupyter`笔记本。我们将在所有章节中遵循此流程，因此到本书结束时，我们将按章节将所有程序都保存在笔记本中。

要在笔记本单元格中执行操作系统命令，我们必须使用`!`符号作为前缀，如下所示：

```
!dir
```

该命令的输出如图 3-1 所示。

**图 3-1.** `Jupyter Notebook`中操作系统命令的输出

你可以通过运行以下语句来升级`pip`工具：

```
!pip3 install --upgrade pip
```

使用以下语句安装用于数据可视化演示的`leather`库：

```
!pip3 install leather
```

这将在计算机上安装`leather`数据可视化库。在下一节中，我们将开始使用笔记本进行`Python`和`leather`的可视化操作。

---

第 3 章：使用 Leather 进行数据可视化

## Leather 简介

`leather`是一个易于使用、广受欢迎的`Python`数据可视化库。你可以在`Python`包索引 (PyPI) 上找到它，网址为 [`pypi.org/project/leather/`](https://pypi.org/project/leather/)。