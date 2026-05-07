# 1.7 配方失效！MATLAB 崩溃了！（以及应对方法）

MATLAB 和 Python（特别是 Anaconda 发行版）会在你的计算机上占据很大空间。除了其他文件外，每个软件都附带超过一千个共享库。其中一些库是两者共有的（例如 ARPACK、BLAS、HDF5、英特尔数学核心函数库、JPEG、LAPACK、Qt、Xerces-C），但这些共有库往往版本不同且不兼容。

这是一个问题。如果你在 MATLAB 中通过其 `py` 模块调用一个 Python 函数，而该函数加载了一个 Python 和 MATLAB 共有的共享库，那么加载的是 MATLAB 的版本还是 Python 的版本？答案取决于包括环境变量在内的几个因素。如果你的环境设置不当，函数调用可能会调用不兼容的库例程，导致函数调用失败，或者 MATLAB 崩溃。

我能够设计出的对 MATLAB 最友好的环境是第 2.​5.​1 节中描述的 `matpy` 虚拟环境。`matpy` 无法阻止所有 MATLAB 崩溃。一个例子是将 Python 的 `matplotlib` 模块加载到 MATLAB 中，然后使用 `matplotlib` 的默认 Qt 后端绘制图形。一旦调用绘图命令，MATLAB 就会崩溃：

MATLAB 2020b：
```
>> plt = py.importlib.import_module('matplotlib.pyplot');
>> plt.plot()
```

![](img/516169_1_En_1_Figa_HTML.jpg)

解决方法是配置一个不同的 `matplotlib` 后端：Linux 和 macOS 上使用 `TkAgg`，Windows 上使用 `WXAgg`；详细内容见第 12.​4.​5 节。

计算机上的软件是不断变化的。操作系统更新、安全补丁、MATLAB 版本变更和 Python 模块更新都可能影响混合 MATLAB/Python 程序在 `matpy` 环境中的运行方式。

如果本书中的某个示例无法运行或导致 MATLAB 崩溃，请在本书的 GitHub 页面 [`https://github.com/Apress/python-for-matlab-development`](https://github.com/Apress/python-for-matlab-development) 上提交一个问题，并描述你的环境配置。

理想情况下，我将能够发布一个可行的解决方案。但这并非总是可能，因为我可能无法复现你的问题或匹配你的操作系统和 MATLAB 版本组合。但至少，你遇到的问题会得到更广泛的曝光——并可能获得众包解决方案。

脚注 1

