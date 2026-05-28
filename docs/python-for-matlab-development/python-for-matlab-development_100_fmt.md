# pythran/mb/MB_pythran_main.py

```python
import numpy as np

from MB_pythran import MB

import time

def main():

    imax = 255

    for N in [500, 1000, 2000, 5000]:

        T_s = time.time()

        nR, nC = N, N

        Re = np.linspace(-0.7440, -0.7433, nC)

        Im = np.linspace( 0.1315,  0.1322, nR)

        img = MB(Re, Im, imax)

        print(N, time.time() - T_s)

if __name__ == '__main__': main()
```

## 14.9.3 Pythran 性能

单核 Pythran 性能比单核 Cython 提升超过 2.5 倍，四核版本比单核快 2.5 倍（表 14-7）。值得注意的是，相较于基线 Python 版本，性能提升了一个数量级，而实现这一提升仅需添加三行注释并在函数文件上调用 Pythran 编译器。

**表 14-7** Mandelbrot 程序执行时间（秒）

| `N` | MATLAB 单核 | Python 基线 单核 | Python + Cython 单核 | Python + Pythran 单核 | Python + Pythran 四核 |
| --- | --- | --- | --- | --- | --- |
| 500 | 1.3 | 4.2 | 0.25 | 0.10 | 0.05 |
| 1000 | 4.4 | 17.1 | 1.01 | 0.40 | 0.15 |
| 2000 | 17.7 | 67.4 | 4.03 | 1.59 | 0.62 |
| 5000 | 112.5 | 420.0 | 25.18 | 9.93 | 3.88 |

## 14.10 Numba

Numba^((76)) 是一个专注于加速数值计算的 Python 即时（JIT）编译器。与 Cython 和 Pythran 类似，Numba 也允许编写并行 for 循环。同样，使用 Numba 所需的代码修改主要涉及类型定义。这些修改是纯 Python 的。此外，无需处理外部生成的源文件、语言或编译器。另一个好处是，需要加速的函数可以与未修改的函数（包括主程序）共存，无需将它们隔离到单独的文件中。基于这些原因，使用 Numba 增强的代码比使用 Cython 或 Pythran 增强的代码更方便使用和部署。

通过以下步骤为 Python 添加 Numba 增强功能：

1.  导入 `numba` 模块，引入 `jit` 装饰器、要使用的数值数据类型，以及可选地引入用于编写并行 for 循环的 `prange` 函数。
2.  对每个需要提速的函数应用 `@jit()` 装饰器。该装饰器的参数是函数的签名，即其返回类型和每个输入参数的类型。该装饰器还接受其他关键字参数，例如 `nopython` 和 `fastmath`。
3.  可选地，使用 Numba 的并行 range 函数 `prange()` 重写 for 循环。

使用 Numba 增强的 Mandelbrot 程序如下所示：

**Python:**

```python
#!/usr/bin/env python3
```