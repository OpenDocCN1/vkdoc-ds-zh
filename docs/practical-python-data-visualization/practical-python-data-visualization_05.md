# 第 4 章 科学 Python 生态系统与 NumPy

在第 3 章中，你学习了如何使用 Python 3 和 leather 数据可视化库创建简单的可视化图表。你还了解到，使用 leather 数据可视化库只能准备基本的可视化图表。对于更复杂精细的可视化，我们需要使用具有高级数据处理和可视化功能的库。

本章将探讨科学 Python 生态系统及其组成部分。它还将通过几个代码演示简要介绍 NumPy 库。本章探讨以下主题：

- 科学 Python 生态系统
- NumPy 与 `ndarray`
- Ndarray 属性
- NumPy 常量

在本书的剩余章节中，我们将逐一探讨科学 Python 生态系统的许多组成部分。在整本书中，我们将使用属于这个科学 Python 生态系统的不同库。本章获得的有价值知识将为后续章节奠定基础。由于本章是为一个广泛的生态系统编写的入门章节，我将其保持得简短而实用。

© Ashwin Pajankar 2021
A. Pajankar 著，《实用 Python 数据可视化》，
<https://doi.org/10.1007/978-1-4842-6455-3_4>

## 科学 Python 生态系统

科学 Python 生态系统（`SciPy`）是一个用于数学、科学和工程的 Python 库集合。SciPy 拥有以下核心组件：

1. *Python 编程语言：* 我们在第 1 章中探讨了 Python 3 编程语言的安装和一些基础知识。
2. *NumPy：* 这是数值 Python 库，是 Python 中进行数值计算的基础包。它定义了一种 *N* 维数据类型，可用于对多维数据进行数值计算。
3. *SciPy 库：* 它包含许多用于数学和科学计算的例程，可用于科学应用。
4. *Matplotlib：* 这是一个受 MATLAB 启发的 Python 3 数据可视化库。

该生态系统中还有几个额外的重要成员库：

1. *Pandas：* 代表 Python 数据分析。它提供了灵活的数据结构，如序列和数据帧。
2. *SymPy：* 代表符号 Python。它用于符号数学和代数。
3. *scikit-image：* 该库包含用于图像处理的例程。
4. *scikit-learn：* 该库包含用于机器学习的例程。

通常与 SciPy 一起使用的交互环境是 IPython 或 Jupyter Notebook。我们在第 2 章中详细了解了 Jupyter Notebook，并在第 3 章中也使用了它。在本书的其余部分我们将继续使用它。

下一节将更详细地介绍 NumPy 库。

## NumPy 与 Ndarrays

如前所述，NumPy 是 Python 中进行数值计算的基础包。NumPy 库最有用的特性是称为 `ndarray` 的多维容器数据结构。

`ndarray` 是一个多维数组（也称为容器），其中包含具有相同数据类型和大小的元素。我们可以在创建 `ndarray` 时定义元素的大小和数据类型。就像列表等其他数据结构一样，我们可以通过索引访问 `ndarray` 的内容。`ndarray` 中的索引范围从 0 开始（类似于 C 语言中的数组或 Python 中的列表）。我们可以将 `ndarray` 用于各种计算。

SciPy 中的所有其他库以及其他库也能识别并使用 NumPy `ndarray` 及相关例程来表示其自身的数据结构并对其进行操作。

让我们从一个动手示例开始。为此章创建一个新的 Jupyter 笔记本，然后运行以下命令安装 NumPy 库：

`!pip3 install numpy`

通过运行以下命令将其导入当前笔记本：

```python
import numpy as np
```

你可以创建一个列表，然后像这样用它来创建一个简单的 `ndarray`：

# NumPy 基础

```python
l1 = [1, 2, 3]
x = np.array(l1, dtype=np.int16)
```

这里我们从一个列表创建一个 `ndarray`。成员的数据类型是 16 位整数。你可以在 `https://numpy.org/devdocs/user/basics.types.html` 找到所支持数据类型的详细列表。

我们可以将前面的代码写成单行，如下所示：

```python
x = np.array(l1, dtype=np.int16)
```

让我们打印 `ndarray` 的值及其类型（我们知道，类型是 `ndarray`）。

```python
print(x)
print(type(x))
```

输出如下：

```
[1 2 3]
<class 'numpy.ndarray'>
```

正如我们在输出中观察到的，它属于类 `numpy.ndarray`。正如我们之前所学，索引从 0 开始。让我们通过如下方式访问 `ndarray` 的元素来演示这一点：

```python
print(x[0]); print(x[1]); print(x[2])
```

输出如下：

```
1
2
3
```

我们甚至可以使用负索引：-1 返回最后一个元素，-2 返回倒数第二个元素，依此类推。示例如下：

```python
print(x[-1])
```

如果我们提供任何无效的索引值，它会抛出一个错误。

```python
print(x[3])
```

在这个语句中，我们试图访问 `ndarray` 的第四个元素，但它并不存在。因此返回以下错误：`IndexError Traceback (most recent call last)`

```
<ipython-input-4-d3c02b9c2b5d> in <module>
----> 1 print(x[3])
IndexError: index 3 is out of bounds for axis 0 with size 3
```

## 多维

数组也可以拥有多个维度，如下所示：`x1 = np.array([[1, 2, 3], [4, 5, 6]], np.int16)`

这表示一个两行三列的二维矩阵。我们可以按如下方式访问单个元素：

`print(x1[0, 0]); print(x1[0, 1]); print(x1[0, 2]);`

我们甚至可以访问整行：

`print(x1[0, :])`

`print(x1[1, :])`

输出如下：

```
[1 2 3]
[4 5 6]
```

我们可以使用以下语法访问整列：

`print(x[:, 0])`

输出如下：

```
[1 4]
```

我们甚至可以拥有超过两个维度的 `ndarray`。以下是创建三维 (3D) 数组的语法：

`x2 = np.array([[[1, 2, 3], [4, 5, 6]],[[0, -1, -2], [-3, -4, -5]]], np.int16)`

科学和商业应用经常使用多维数据。`Ndarray` 对于存储数值数据非常有用。尝试运行以下命令并检索前面 3D 矩阵中的元素。

`print(x2 [0, 0, 0])`

`print(x2 [1, 1, 2])`

`print(x2[:, 1, 1])`

## Ndarray 属性

我们可以通过 `ndarray` 的属性来了解更多信息。

首先，我们通过演示来看看所有属性。此示例使用我们之前用过的同一个 3D 矩阵。

`x2 = np.array([[[1, 2, 3], [4, 5, 6]],[[0, -1, -2], [-3, -4, -5]]], np.int16)`

我们可以通过以下语句获取维度数量：`print(x2.ndim)`

输出返回维度数量。然后，我们可以按如下方式了解 `ndarray` 的形状：

`print(x2.shape)`

形状表示各个维度的大小，如下所示：`(2, 2, 3)`

我们可以按如下方式确定成员的数据类型：

`print(x2.dtype)`

输出如下：

```
int16
```

我们还可以了解大小（元素数量）以及存储所需的内存字节数，如下所示：

`print(x2.size)`

`print(x2.nbytes)`

输出如下：

我们可以使用以下代码计算转置：

`print(x2.T)`

## NumPy 常量

`NumPy` 库包含许多有用的数学和科学常量，您可以在程序中使用它们。以下代码片段打印了所有重要的常量：

`print(np.inf)`

`print(np.NAN)`

`print(np.NINF)`

`print(np.NZERO)`

`print(np.PZERO)`

`print(np.e)`

`print(np.euler_gamma)`

`print(np.pi)`

输出如下：

```
inf
nan
-inf
-0.0
0.0
2.718281828459045
0.5772156649015329
3.141592653589793
```

## 总结

本章介绍了 `NumPy` 和 `ndarray` 的基础知识。`NumPy` 库非常庞大，包含许多例程。甚至有专门介绍 `NumPy` 的书籍。就我们的目的而言，我们将在后续章节中根据可视化演示的需要，探索 `NumPy` 库中更多的例程。

下一章将介绍一些 `ndarray` 创建例程以及使用 `Matplotlib` 进行数据可视化的基础知识。

