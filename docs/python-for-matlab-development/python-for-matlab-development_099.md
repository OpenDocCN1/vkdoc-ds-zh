# pythran/mb/MB_pythran.py
import numpy as np
#pythran export nIter(complex, int)
def nIter(c, imax):
    z = complex(0, 0)
    for i in range(imax):
        z = z*z + c
        if abs(z) > 2:
            break
    return np.uint8(i)

#pythran export MB(float [], float [], int)
def MB(Re, Im, imax):
    nR = Im.shape[0]
    nC = Re.shape[0]
    img = np.zeros((nR, nC), dtype=np.uint8)
    #omp parallel for
    for i, I in enumerate(Im):
        for j, R in enumerate(Re):
            c = complex(R, I)
            img[i, j] = nIter(c, imax)
    return img
```

前两个注释定义了后面几行函数的签名（不包括返回类型，Pythran 会自动推断），第三个注释指定 `for i, I` 循环为并行循环。

使用以下命令编译函数：

```
> pythran -Ofast -fopenmp MB_pythran.py
```

Pythran 需要 C 头文件才能使用基本线性代数子程序（BLAS）。如果你的编译命令显示：

```
fatal error: cblas.h: No such file or directory
```

并且没有创建共享对象文件，你需要下载并安装适合你操作系统的 BLAS 开发头文件。例如，在 Ubuntu 上，可以通过 `apt install libopenblas-dev` 解决。即使安装了头文件，你仍然可能看到“fatal error”，但共享对象文件已成功创建。

要禁止 `for` 循环的 OpenMP 并行化，可以从源代码中删除 `#omp parallel for` 注释，或者从编译命令中删除 `-fopenmp` 开关。

`pythran` 命令会产生一个以输入源文件命名的编译后共享对象文件（在我的电脑上是 `MB_pythran.cpython-38-x86_64-linux-gnu.so`）。用于运行 Pythran 编译模块的驱动程序与用于 Cython 编译模块的驱动程序相同。唯一的区别是加载的模块名称——`import MB_pythran` 而不是 `import MB_cython`：

```
#!/usr/bin/env python3
