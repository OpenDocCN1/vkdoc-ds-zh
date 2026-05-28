# file: numba/mb/MB_numba.py

import numpy as np

from numba import jit, prange, uint8, int64, float64, complex128

import time

@jit(uint8(complex128,int64),nopython=True, fastmath=True)

def nIter(c, imax):

    z = complex(0, 0)

    for i in range(imax):

        z = z*z + c

        if z.real*z.real + z.imag*z.imag > 4:

            break

    return i

@jit(uint8:,:,

     nopython=True, fastmath=True, parallel=True)

def MB(Re, Im, imax):

    nR = len(Im)

    nC = len(Re)

    img = np.zeros((nR, nC), dtype=np.uint8)

    for i in prange(len(Im)):

        I = Im[i]

        for j,R in enumerate(Re):

            c = complex(R, I)

            img[i, j] = nIter(c, imax)

    return img

def main():

    imax = 255

    for N in [500, 1000, 2000, 5000]:

        T_s = time.time()

        nR, nC = N, N

        Re = np.linspace(-0.7440, -0.7433, nC)

        Im = np.linspace( 0.1315, 0.1322, nR)

        img = MB(Re, Im, imax)

        T_e = time.time()

        print(f'{T_e - T_s:.3f}  {N:5d}')

if __name__ == '__main__': main()

```

`nIter()` 和 `MB()` 的 `@jit()` 装饰器可能看起来令人生畏，但其组成部分很直接。`nIter()` 返回一个标量 `uint8`。输入是一个双精度复数标量——`complex128`——和一个常规的 Python 整数标量 `int64`。因此，`nIter()` 的签名是 `uint8(complex128, int64)`。关键字参数 `nopython` 和 `fastmath` 将在第 14.10.2 节中解释。`MB()` 函数返回一个 `uint8` 的二维数组，在 Numba 中表示为 `uint8[:,:]`。输入是一对一维双精度浮点数组和一个整数标量。因此，`MB()` 的签名是 `uint8:,:`。`MB()` 的装饰器还有一个额外的关键字参数 `parallel=True`，表示使用了并行 range 函数 `prange()`。

### 14.10.1 使用 Numba 的并行 for 循环

Numba 的 `prange()` 函数类似于 Cython 的。编写并行 for 循环相当于将现有的 `for i in ...` 结构替换为 `for i in prange(N)`，其中 `N` 是一个整数标量。要求循环迭代一个整数范围可能意味着，如果现有循环迭代其他对象，则需要对该循环进行小幅修改。

在我们的例子中，我们必须将左侧的结构（它同时填充了索引 `i` 和第 `i` 个虚数标量值）替换为右侧的结构（它仅遍历索引）。标量值必须在后续行中提取：

| **Python，基线：** | **Python，Numba 并行 for：** |
| --- | --- |
| `for i,I in enumerate(Im):` | `for i in prange(len(Im)):` `I = Im[i]` |

Numba 负责将循环体中的工作分发到不同核心，以及聚合同时计算之间共享变量的结果。

### 14.10.2 Numba 关键字参数 `nopython`, `fastmath`

`@jit()` 的关键字参数 `nopython`（通常设置为 `True`）指示 JIT 编译器完全跳过 Python 解释器，仅使用编译后的代码。这是推荐设置，甚至有其专用的装饰器：`@njit()` 等同于 `@jit(...,nopython=True)`。仅当编译失败时才应将该关键字设置为 `False`。

关键字参数 `fastmath` 启用不安全的操作^((77))，例如假设输入永远不会是 NaN 或 Inf，并允许编译器重新排序浮点指令。当然，请比较 `fastmath=True` 开启与关闭时代码的输出，以确保结果不受影响。

### 14.10.3 Numba 性能

如表 14-8 所示，使用 Numba 增强的 Mandelbrot 程序性能令人惊叹。使用 Numba，纯 Python 代码的速度超过了 Cython 和 Pythran 编译的模块。

### 14.10.4 Numba 的限制

虽然 Numba 的性能提升令人印象深刻，但它们也附带了一些限制。需要由 Numba 加速的函数应相对底层且计算密集。

**表 14-8** Mandelbrot 程序执行时间（秒）

| *N* | MATLAB 单核 | Python 基线 单核 | Python + Cython 单核 | Python + Pythran 单核 | Python + Pythran 四核 | Python + Numba 单核 | Python + Numba 四核 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 500 | 1.3 | 4.2 | 0.25 | 0.10 | 0.05 | 0.09 | 0.04 |
| 1000 | 4.4 | 17.1 | 1.01 | 0.40 | 0.15 | 0.36 | 0.15 |
| 2000 | 17.7 | 67.4 | 4.03 | 1.59 | 0.62 | 1.44 | 0.62 |
| 5000 | 112.5 | 420.0 | 25.18 | 9.93 | 3.88 | 8.95 | 3.71 |

除非使用 Numba 的包装器地址协议（Wrapper Address Protocol），^((78)) 否则通过 `@jit` 装饰的函数只能调用 NumPy 和标准库函数的一个子集。

在使用 `Numba` 进行优化尝试之前，先编写一个测试程序，其中包含执行所需加速计算所需的语言结构和函数调用。用 `@jit` 修饰这些试验函数，然后运行程序以检查是否正常。如果看到类似如下的错误：

```

NameError: global name 'XYZ' is not defined

```

并且在被 `@jit` 修饰的函数中存在对 `XYZ()` 的调用，这意味着 `Numba` 无法编译该函数。此时，你的选择包括：简化函数直至能被编译、回退到 `multiprocessing` 模块（第 14.6 节），或使用将在第 14.13 节介绍的 `dask`。

## 14.11 f2py

`f2py` 是 `NumPy` 自带的一个命令行工具。它能让你比使用任何其他二进制接口工具都更轻松地，从经过特殊注释的 Fortran 代码创建已编译的 Python 模块。`f2py` 带来两大重要优势：它能创建用于调用遗留 Fortran 代码的 Python 接口模块，并且能为用 Fortran 重写的 Python 函数提供显著的计算加速。

以下 Fortran 代码实现了 Mandelbrot 实现中较早出现的 `nIter()` 和 `MB()` 函数。定义输入和输出参数的五行 `cf2py` 代码，是 `f2py` 创建包含文档的 Python 接口所需的全部内容。

Fortran 代码：

```

1   c file: code/f2py/mb.f

2         integer function nIter(c, imax)

3   cf2py intent(in)   :: c, imax

4   cf2py intent(out)  :: nIter

5         implicit none

6         complex*16 z, c

7         integer i, imax

8         nIter = imax

9         z = 0

10         do i = 0,imax - 1

11           z = z*z + c

12           if (zabs(z) .gt. 2.0) then

```



```fortran
13             nIter = i

14             return

15           endif

16         enddo

17         nIter = imax

18         end

20         subroutine MB(nR, nC, Re, Im, img, imax)

21   cf2py intent(in)   :: Re, Im, imax

22   cf2py intent(out)  :: img

23   cf2py intent(hide) :: nR, nC

24         implicit none

25         integer nR, nC, nIter, imax, i, j, ij

26         integer(kind=2) img(nR*nC)

27         real*8 Re(nR), Im(nC)

28         complex*16 c

29         do i = 1,nR

30           do j = 1,nC

31             c = cmplx(Re(i), Im(j))

32             ij = (i-1)*nC+j

33             img(ij) = nIter(c,imax)

34           enddo

35         enddo

36         end
```

使用命令 `f2py -c --opt="-Ofast" -m MB_fortran mb.f` 会生成 Python/Fortran 接口代码，使用系统的 Fortran 编译器编译该代码和前述 `mb.f`，然后将它们链接成一个编译好的 Python 模块 `MB_fortran`（在我的电脑上，该模块的全名是 `MB_fortran.cpython-38-x86_64-linux-gnu.so`）。

除了编写接口代码，`f2py` 还会为每个函数生成文档：

```
In : import MB_fortran

In : MB_fortran.mb?
调用签名: MB_fortran.mb(*args, **kwargs)

类型:           fortran

字符串形式:    

文档字符串:

img = mb(re,im,imax)

针对 ``mb`` 的包装器。

参数

re : 输入 rank-1 array('d')，边界为 (nr)

im : 输入 rank-1 array('d')，边界为 (nc)

imax : 输入 int

返回

img : rank-1 array('h')，边界为 (nr*nc)
```

这个驱动程序加载 Fortran 编译的模块，然后运行我们的基准测试案例：

```
#!/usr/bin/env python3
```

