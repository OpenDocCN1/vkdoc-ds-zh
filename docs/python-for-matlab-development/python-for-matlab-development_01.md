## 前言

2018 年，我与一位刚获得工程硕士学位的年轻工程师闲聊。我们谈到了 MATLAB，她抱怨在学校项目截止时经常遭遇许可证短缺的困扰。我在 25 年前也有同样的挫败感——难道情况一点都没变吗？

当然，很多事情已经改变了。

那时，我们已经各自找到了通往 Python 的路径，来处理过去用 MATLAB 完成的工作。为什么如此少的 MATLAB 用户意识到 Python 能带来的强大功能和自由呢？

我开始整理笔记，对比 Python 解决方案及其 MATLAB 等效方案。不久后，我了解到 MATLAB 的`py`模块。一个面向 Python 的二进制 API？！好得令人难以置信。从某种意义上说，它*确实*好得令人难以置信；早期版本无法使用`NumPy`等关键模块。

不过，The MathWorks 在每个 MATLAB 版本中都改进了`py`。如今，MATLAB 可以运行来自`NumPy`、`SciPy`、`Pandas`、`matplotlib`、`statsmodels`、`dask`，甚至是用`Cython`、`Numba`、`Pythran`和`f2py`编译的模块的代码。MATLAB + Python 的组合为两种语言都带来了惊人的可能性，然而很少有 MATLAB 开发者了解这一能力或如何利用它。我想，这正好是一本新书的沃土。




