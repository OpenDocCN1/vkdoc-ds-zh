# file: code/hpc/MB_vectorized.py
import numpy as np
import time
def nIter(c, imax):
nR,nC = c.shape
z = np.zeros_like(c)
img = np.zeros(z.shape,
               dtype=np.uint8)
Idx = np.ones(z.shape,
              dtype=np.bool )
  for i in
range(imax):
z[Idx] = z[Idx]*z[Idx] + \
         c[Idx]
New = (z.real**2 +
       z.imag**2 <= 4) \
      .reshape(nR,nC)
    if not np.any(New):
      break
img[New] = i
Idx *= New
  return img
def main():
imax = 255
  for N in
[500,1000,2000,5000]:
T_s = time.time()
nR, nC = N, N
Re = np.linspace(-0.7440,
                 -0.7433, nC)
Im = np.linspace( 0.1315,
                   0.1322, nR)
zR,zI = np.meshgrid(Re,Im)
z_init = zR + zI*(1j)
img = nIter(z_init, imax)
print(N, time.time() - T_s)
if __name__ == '__main__': main()
```

性能结果（表 14-4）很有意思；向量化对 MATLAB 没有影响，但在 Python 中带来了 3 倍的加速。令人失望的是，Linux 上的 MATLAB 2020b 在计算 *N* = 5000 的解时崩溃了。

**表 14-4：** 标量与向量曼德勃罗实现执行时间（秒）

| N | MATLAB 标量 | MATLAB 向量 | Python 标量 | Python 向量 |
|---|---|---|---|---|
| 500 | 1.6 | 1.7 | 4.2 | 1.3 |
| 1000 | 5.8 | 5.7 | 17.1 | 4.7 |
| 2000 | 23.2 | 24.6 | 67.4 | 19.3 |
| 5000 | 145.1 | *崩溃* | 420.0 | 184.4 |

## 14.8 Cython

Cython⁷³ 是一个 Python 到 C 的编译器。虽然它没有实现完整的 Python 语言，但未实现的功能会按常规方式解释执行，只是不进行编译。

Cython 拥有两种强大的能力，可以使 Python 代码运行得更快。它能够：

*   将部分 Python 代码转换为 C，然后将 C 编译成优化的共享对象，该对象可作为 Python 模块加载。



*   提供一个接口层，使得调用原生 C 和 C++ 库变得简单直接。

### 14.8.1 使用 Cython 编译的 Python

使用 Cython 生成编译后的 Python 模块需要四个步骤：

1.  将你希望编译的现有 Python 源文件中的函数复制到以 `.pyx`（而非 `.py`）结尾的新文件中。包含 `main()` 的文件不会被编译，且必须保留在 `.py` 文件中。
2.  在 `.pyx` 文件中，对希望提升速度的 Python 变量添加 C 语言类型注解。如果你的代码使用了 NumPy，还需添加一行与 NumPy `import` 对应的 `cimport` 语句。
3.  创建一个设置文件（一个小的 Python 程序），告诉 Cython 需要编译哪些 `.pyx` 文件，并可选择指定要使用的编译器以及编译和链接标志。
4.  运行设置文件来编译你的代码。

以曼德勃罗集程序为例，前两个步骤通过左侧原始代码（不包括 `main()`）与右侧灰色显示的 Cython 增强代码的并排对比来展示。

| Python，基线版本： | Python，含 Cython 注解： |
| --- | --- |
| `1` `#!/usr/bin/env python3`<br>`2` `# cython/mb/MB_orig.py`<br>`3`<br>`4` `import numpy as np`<br>`5` `import time`<br>`6` `def nIter(c, imax):`<br>`7`<br>`8`<br>`9`<br>`10` `z = complex(0, 0)`<br>`11` `for i in range(imax):`<br>`12` `z = z*z + c`<br>`13` `if abs(z) > 2:`<br>`14` `break`<br>`15` `return i`<br>`16`<br>`17` `def MB(Re, Im, imax):`<br>`18`<br>`19`<br>`20`<br>`21` `nR = len(Im)`<br>`22` `nC = len(Re)`<br>`23` `img = np.zeros((nR, nC))`<br>`24` `for i,I in enumerate(Im):`<br>`25` `for j,R in enumerate(Re):`<br>`26` `c = complex(R, I)`<br>`27` `img[i, j] = nIter(c, imax)`<br>`28` `return img` | `1` `#  cython/mb/MB_cython.pyx`<br>`2` `cimport numpy as np`<br>`3` `import numpy as np`<br>`4` `import time`<br>`5` `def nIter(double complex c,`<br>`6` `long imax):`<br>`7` `cdef int i`<br>`8` `cdef double complex z`<br>`9` `z = complex(0, 0)`<br>`10` `for i in range(imax):`<br>`11` `z = z*z + c`<br>`12` `if z.real*z.real + z.imag*z.imag > 4:`<br>`13` `break`<br>`14` `return i`<br>`15`<br>`16` `def MB(Re, Im, imax):`<br>`17` `cdef int i,j`<br>`18` `cdef double I, R`<br>`19` `cdef double complex c`<br>`20` `nR = len(Im)`<br>`21` `nC = len(Re)`<br>`22` `img = np.zeros((nR, nC), dtype=np.uint8)`<br>`23` `for i,I in enumerate(Im):`<br>`24` `for j,R in enumerate(Re):`<br>`25` `c = complex(R, I)`<br>`26` `img[i, j] = nIter(c, imax)`<br>`27` `return img` |

适用于第三步的设置文件如下所示：

```python
