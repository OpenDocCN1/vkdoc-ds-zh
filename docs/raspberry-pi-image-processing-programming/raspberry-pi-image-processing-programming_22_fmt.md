# Python 科学计算生态系统简介

在上一章中，我们学习了如何使用`Pillow`进行高级图像处理。`Pillow`是图像处理操作的一个很好的起点。然而，它有其局限性。当涉及到实现复杂的图像处理操作（例如分割、形态学操作、高级滤波器和测量）时，`Pillow`就显得力不从心了。我们确实需要使用更好的库来进行高级图像处理。`SciPy`生态系统为Python的所有科学用途奠定了基础。

`SciPy`代表科学计算Python。它广泛使用`NumPy`（数值计算Python）和`matplotlib`分别进行数值运算和数据可视化。本章涵盖`NumPy`、`SciPy`和`matplotlib`的基础知识，并通过`NumPy`、`scipy.misc`和`matplotlib`探索入门级的编程示例。我们将在本章中探讨以下主题：

- Python 科学计算生态系统
- `matplotlib`
- PIL 图像对象与`NumPy`的`ndarray`之间的转换

在本章结束时，我们将掌握`SciPy`生态系统的基础知识。

## Python 科学计算生态系统

Python科学计算生态系统是一个用于Python的开源免费库集合，专用于科学领域。它包含用于数值运算（`NumPy`）、科学运算（`SciPy`）和数据可视化（`matplotlib`）的模块。它被世界各地的科学、数学、工程和分析社区广泛使用。

该生态系统包含以下核心组件：

- `NumPy`
- `SciPy`
- `matplotlib`
- `IPython`
- `Pandas`
- `Sympy`
- `Nose`

许多其他库使用生态系统中的核心模块来提供额外的算法和数据结构。例如`OpenCV`和`Scikit`。

图6-1恰当地总结了Python科学计算生态系统在科学计算领域的作用。

**图6-1.** Python科学计算生态系统的核心组件

在树莓派上安装Python科学计算生态系统组件的最佳方法是使用`pip`工具。

首先，使用以下命令升级你的树莓派：

```
sudo python3 -m pip install --upgrade pip
```

然后，使用以下命令安装Python科学计算生态系统的组件：

```
pip3 install numpy scipy matplotlib ipython jupyter pandas sympy nose
```

这将安装所有必需的库。

## 简单示例

让我们学习几个简单的示例。我们将学习如何从`scipy.misc`读取内置图像，将其存储在一个变量中，并查看该变量的数据类型，如清单6-1所示。

**清单6-1.** `prog01.py`

```python
from scipy import misc

img = misc.face()
print(type(img))
```

输出如下：

```
<class 'numpy.ndarray'>
```