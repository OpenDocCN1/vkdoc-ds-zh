# file: `cython/mb/cython_setup.py`

```python
import os

from setuptools import setup

from Cython.Build import cythonize

import numpy

os.environ['CFLAGS'] = '-Ofast'

setup(
    ext_modules=cythonize("MB_cython.pyx"),
    include_dirs=[numpy.get_include()]
)
```

最后，运行此设置文件来编译代码：
```
python cython_setup.py build_ext --inplace
```

一次典型的调用会产生警告、新的 C 源文件，并希望最终生成一个以 `.pyx` 文件命名的共享对象。在我的 Linux 机器上，共享对象是 `MB_cython.cpython-38-x86_64-linux-gnu.so`。尽管名称很长，但在 Python 中通过 `import MB_cython` 即可导入。我们这番努力获得了什么？一个小型驱动程序加载编译后的模块并运行案例：

```python
#!/usr/bin/env python3
```