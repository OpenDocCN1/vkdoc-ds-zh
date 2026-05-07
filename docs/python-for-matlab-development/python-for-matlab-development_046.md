# code/IO/read_mat_73.py
import h5py
F = 'by_matlab_7.3.mat'
h5 = h5py.File(F, 'r')
a1 = h5['/a1'][:]
a2 = h5['/a2'][:]
c  = {}
c['x'] = h5['/c/x'][:]
c['y'] = h5['/c/y'][:]
h5.close()
```

除了必须逐个加载 `c` 的字段外，这没什么大不了的。显然，读取一个复杂的数据结构所需的精力将比调用 `scipy.io.loadmat()` 要多得多。

## 7.15 命令行输入

MATLAB 的用户体验主要是交互式的，即在 IDE 中开发和调用单个命令和/或用户开发的程序。当然，Python 也可以通过 PyCharm、Spyder、VS Code 和 Eclipse 等 IDE，通过 Jupyter notebook 或 Jupyterlab，或通过 `ipython` 或 `bpython` 等控制台 REPL 进行交互式使用。

然而，Python 也非常适合批处理使用，即将 Python 程序保存到文本文件中，然后从控制台或另一个程序将其作为命令调用。虽然在 MATLAB 中也可以做到这一点，但相比之下用得少得多。

批处理程序或命令行程序的输入可以来自命令行参数、环境变量和配置文件。在接下来的章节中，我们将看到两种语言如何捕获和处理命令行参数。

### 7.15.1 Python: `sys.argv`

从 Python 程序中获取命令行参数的最简单方法是使用 `sys` 模块，该模块在列表 `sys.argv` 中存储了程序在命令行中看到的所有内容：

```python
#!/usr/bin/env python3
