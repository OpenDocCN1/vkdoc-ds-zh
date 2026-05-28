```python
# file: code/f2py/MB_main.py

import numpy as np
import time
from MB_fortran import mb

def main():
    imax = 255
    for N in [500, 1000, 2000, 5000]:
        T_s = time.time()
        nR, nC = N, N
        Re = np.linspace(-0.7440, -0.7433, nC)
        Im = np.linspace(0.1315, 0.1322, nR)
        img = np.zeros((nR*nC,), dtype=np.int16)
        img = mb(Re, Im, imax).reshape(nR, nC)
        print(N, time.time() - T_s)

if __name__ == '__main__':
    main()
```

表 14-9 显示了 `f2py` 编译的模块与其他单 CPU 结果的对比情况。

**表 14-9**  
Mandelbrot 程序的执行时间（秒）

| N    | MATLAB 1 核 | Python 基线 1 核 | Python+ Cython 1 核 | Python+ Pythran 1 核 | Python+ Numba 1 核 | Python+ f2py 1 核 |
|------|-------------|------------------|---------------------|----------------------|--------------------|--------------------|
| 500  | 1.3         | 4.2              | 0.25                | 0.10                 | 0.09               | 0.10               |
| 1000 | 4.4         | 17.1             | 1.01                | 0.40                 | 0.36               | 0.40               |
| 2000 | 17.7        | 67.4             | 4.03                | 1.59                 | 1.44               | 1.60               |
| 5000 | 112.5       | 420.0            | 25.18               | 9.93                 | 8.95               | 10.05              |

## 14.12 配方 14-1：在单台计算机上使用 Python 加速 MATLAB

在 Mandelbrot 问题上，单 CPU 的 MATLAB 性能相比普通的单 CPU Python 实现表现不俗（表 14-8）。但与性能增强后的 Python 版本相比，则黯然失色。与 Python 类似，MATLAB 的性能也可以通过多种方式提升：

- 使用并行 for 循环（需要并行计算工具箱或 MATLAB Coder）。
- 用 C、C++ 或 Fortran 编写 `MB()` 和 `nIter()` 函数，添加头文件并调用 `mex` API 函数，然后使用 `mex` 命令创建编译好的 MATLAB 扩展。
- 调用性能增强的 Python 模块。

此处，我们将探讨最后一种方案，即从 MATLAB 调用基于 Python 的 `Cython`、`Pythran`、`Numba` 和 `f2py` 加速方案。在所有情况下，该方案都包含将 MATLAB 输入转换为 Python 变量，以及将 Python 返回值转换为 MATLAB 变量的开销。表 14-10 所示的计时结果，不包含 MATLAB 程序启动时在 MATLAB 内部加载 Python 模块的时间。

### 14.12.1 在 MATLAB 友好的虚拟环境中编译 Python 模块

接下来的四节中的三节，即第 14.12.2、14.12.3 和 14.12.5 节，会使用你计算机上的 C、C++ 或 Fortran 编译器来构建二进制 Python 模块。在 macOS 上，这意味着必须安装 Xcode 开发库。在所有操作系统上，务必在共享库与 MATLAB 一致的 conda 环境（如 `matpy`，第 2.5.1 节）中工作。否则，编译好的模块将无法在 MATLAB 中加载。

### 14.12.2 MATLAB + Cython

该驱动程序加载在第 14.11 节编译的 `MB_cython` 模块。

MATLAB 2020b：

```
% file: cython/mb/MB_main.m

np = py.importlib.import_module('numpy');
MB_cython = py.importlib.import_module('MB_cython');
imax = int64(255);

for N = [500 1000 2000 5000]
    tic
    nR = N; nC = N;
    Re = np.array(linspace(-0.7440, -0.7433, nC));
    Im = np.array(linspace( 0.1315,  0.1322, nR));
    img = py2mat(MB_cython.MB(Re, Im, imax));
    fprintf('%5d %.3f\n',N,toc);
end
```

### 14.12.3 MATLAB + Pythran

该驱动程序加载在第 14.9 节编译的 `MB_pythran` 模块。

MATLAB 2020b：

```
% file: pythran/mb/MB_main.m

np = py.importlib.import_module('numpy');
MB_pythran = py.importlib.import_module('MB_pythran');
imax = int64(255);

for N = [500 1000 2000 5000]
    tic
    nR = N; nC = N;
    Re = np.array(linspace(-0.7440, -0.7433, nC));
    Im = np.array(linspace( 0.1315,  0.1322, nR));
    img = py2mat(MB_pythran.MB(Re, Im, imax));
    fprintf('%5d %.3f\n',N,toc);
end
```

### 14.12.4 MATLAB + Numba

该驱动程序加载第 14.10 节的并行 for 循环模块 `MB_numba_prange`。

MATLAB 2020b：

```
% file: numba/mb/MB_main.m

np = py.importlib.import_module('numpy');
MB_numba = py.importlib.import_module('MB_numba_prange');
imax = int64(255);

for N = [500 1000 2000 5000]
    tic
    nR = N; nC = N;
    Re = np.array(linspace(-0.7440, -0.7433, nC));
    Im = np.array(linspace( 0.1315,  0.1322, nR));
    img = py2mat(MB_numba.MB(Re, Im, imax));
    fprintf('%5d %.3f\n',N,toc);
end
```

### 14.12.5 MATLAB + f2py

该驱动程序加载在第 14.11 节编译的 `MB_fortran` 模块。

MATLAB 2020b：

```
% file: f2py/mb/MB_main.m

np = py.importlib.import_module('numpy');
MB_fortran = py.importlib.import_module('MB_fortran');
imax = int64(255);

for N = [500 1000 2000 5000]
    tic
    nR = N; nC = N;
    Re = np.array(linspace(-0.7440, -0.7433, nC));
    Im = np.array(linspace( 0.1315,  0.1322, nR));
    img = py2mat(MB_fortran.mb(Re, Im, imax));
    fprintf('%5d %.3f\n',N,toc);
end
```

```matlab
% file: code/f2py/MB_main.m
```

np = py.importlib.import_module('numpy');

MB_fortran = py.importlib.import_module('MB_fortran');

imax = int64(255);

for N = [500 1000 2000 5000]

tic

nR = N; nC = N;

Re = np.array(linspace(-0.7440, -0.7433, nC));

Im = np.array(linspace( 0.1315,  0.1322, nR));

img = py2mat(MB_fortran.mb(Re, Im, imax));

fprintf('%5d %.3f\n',N,toc);

end

```

### 14.12.6 MATLAB + Python 性能结果

表 14-10 显示，对于我们的基准问题，Python 中观察到的性能提升同样使 MATLAB 受益。

**表 14-10** 基于 MATLAB 且经 Python 增强的 Mandelbrot 程序的执行时间（秒）

| `N` | MATLAB 单核 | MATLAB + Cython 单核 | MATLAB + Pythran 4 核 | MATLAB + Numba 4 核 | MATLAB + f2py 单核 |
| --- | --- | --- | --- | --- | --- |
| 500 | 1.3 | 0.29 | 0.05 | 0.05 | 0.11 |
| 1000 | 4.4 | 1.02 | 0.21 | 0.19 | 0.41 |
| 2000 | 17.7 | 4.21 | 0.69 | 0.58 | 1.59 |
| 5000 | 112.5 | 26.02 | 4.10 | 3.93 | 10.13 |

尽管单核的 `f2py` 性能与多核 `Numba` 相比略显逊色，但 `f2py` 提供了一个其他加速工具所不具备的次要优势：通过 `f2py`，你可以比使用 MATLAB 自带的 `mex` 工具更轻松地为 MATLAB 创建 Fortran 接口。

## 14.13 使用 Dask 进行分布式内存并行处理

前面的章节展示了将工作负载分布到计算机的多个核心上可以显著提升性能。我们还可以做得更好——好得多——通过将工作负载分布到多台计算机上。有几个 Python 模块，包括 Ray、^(⁷⁹) dispy^(⁸⁰) 和 dask^(⁸¹)，都可以轻松地运行一个将任务分发给多台计算机的 Python 程序。

`Dask` 相对于其他模块有其优势，因为它还提供了一个外核数据容器，即 `dask` 数组，它支持对计算机内存无法容纳的大型数组进行计算，类似于 MATLAB 的“tall 数组”。此外，`dask` 得到了 Coiled^(⁸²) 的支持，后者为 `dask` 作业提供商业支持和云计算服务。Coiled 每月为 `dask` 作业提供最多 100 个核心、总计 1000 小时的免费 CPU 时间，这对于无法访问高端计算集群的个人来说颇具吸引力。

后续章节将使用 `Dask` 来演示分布式内存并行处理。主要的 `dask` 概念在第 14.13.2 节中介绍，但如果没有详细的示例，这些概念可能意义不大。第 14.13.3 节将给出这样一个示例，我们将在该节中解决一个“易并行”且计算密集型的问题——对许多数字的质因数求和。首先，我们只使用单台计算机的多个核心。

真正的乐趣要从我们使用多台计算机才开始。在我们能在多台计算机上运行 `dask` 之前，必须在这些计算机上启动后台调度程序和工作者作业；相关细节将在第 14.13.4 节中解释。一旦后台机制就绪，在第 14.13.5 节中，我们将在一组计算机上重新运行质因数求和问题，并看到一些不错的加速效果。

内存密集型作业会带来额外的复杂性。在第 14.13.6 节中，我们将使用一组只有 1 GB 内存的计算机制作我们在第 14.2 节中计算过的 Mandelbrot 集合的十亿像素图像。不用说，我们必须采取预防措施以避免崩溃。

最后，在第 14.13.7 节中，我们将结合使用 `dask` 积累的经验来解决一个与真实世界应用非常相似的问题。我们将使用一个包含 50 万个自由度的模型，通过跨越数千个频率的频域解决方案，求解一个瞬态动态有限元问题。此类问题在单台计算机上可能需要数天时间，但我们将使用 `Coiled` 的云服务，在不到 15 分钟内（无论通过 Python 还是 MATLAB）获得解决方案。

### 14.13.1 并行 MATLAB

与 Python 一样，MATLAB 也可以在分布式内存集群和云集群上运行。最直接的方法是使用并行计算工具箱。其他选项包括使用 `MATLAB Coder` 或 `MATLAB Compiler` 创建可以发送到远程计算机的独立可执行文件。

`MathWorks` 还提供 `MATLAB Online`，并通过它提供 `MATLAB in the Cloud`，但其功能仅限于您的 MATLAB 许可证所启用的功能。换句话说，如果您没有并行计算工具箱或云使用的许可证，您将无法在云中的多台计算机上运行您的 `m-files`。

如前所述（第 1.​3 节），本书的范围仅限于核心 MATLAB 产品，这排除了将纯 MATLAB 云解决方案与其对应的 Python 解决方案进行比较的可能性。尽管如此，我们将在第 14.14 节中看到，混合 MATLAB/Python 代码在私有集群以及 `Coiled` 云上都运行得非常高效。

### 14.13.2 Dask 执行范式与性能预期

# Dask 执行范式

Dask 的执行范式涵盖四个主要概念：

- **集群**是任务所有者可以访问的计算机集合。集群中的计算机必须具有一致的 Python 安装环境，并包含 `dask` 和 `distributed` 模块。集群实际上可以小到只有你当前正在使用的单台计算机（此时 `dask` 任务会分布到该计算机的多个核心上），也可以是大型 `HPC` 计算集群（可选配 `LSF`、`Slurm` 或 `PBS` 等独立任务调度器）。在后一种情况下，用户必须在所有计算机上拥有相同的账户，能够无密码通过 `ssh` 在计算机之间通信，或者能够通过单独的任务调度器向计算机提交任务。

- **`dask-scheduler`** 和 **`dask-worker`** 是在集群计算机上运行的后台任务。调度器的实例运行在其中一台计算机上，而每个计算机上则运行一个工作节点实例。这些进程需要通过 `dask-ssh` 命令手动启动，或者由任务调度器特定的配置文件来启动和管理。

- **客户端**是通过连接到 `dask` 调度器进程创建的 Python 变量。

- **延迟**任务是一个 Python 函数及其调用参数，它不会立即执行，而是被添加到一个待执行的任务列表中。

一个支持 `dask` 的 Python 程序会先用延迟任务填充一个列表，然后将此列表传递给 `dask.compute()`。这个函数将任务交给 `dask` 调度器，调度器再将每个任务发送给下一个空闲的工作节点（可能运行在不同的计算机上）。工作节点接收到任务后，会使用用户提供的参数调用任务对应的用户定义函数，然后将返回值发送回调度器。`dask.compute()` 从调度器获取任务的返回值，并将其追加到结果列表中。我们的程序负责设置好要并行执行的函数调用，而 `dask` 则处理其余的一切——将这些调用分配给处理器，序列化并通过网络发送输入参数，在远程计算机上执行调用，最后将返回值序列化并发送回启动任务的计算机。

`dask` 带来的便利性伴随着一定的性能代价，其大小取决于你可能只能有限控制的几个因素：

- 负载均衡策略
- 传递给远程执行函数或从其接收的数据量
- 网络速度
- 计算机资源的均匀性

最重要的是，`dask` 决定了如何以及何时传输数据。代码开发者通常很乐意卸下这个负担，即使计算/传输步骤的交错方式并不理想，但后续的并行效率可能会令人失望。“你的情况可能有所不同”这句谚语在并行处理中始终成立。

这一切听起来很复杂（在底层确实如此！）。我们究竟如何建立并运行一个分布式内存并行程序？如何能让代码运行得更快？唯一的方法就是通过示例来理解。

### 14.13.3 示例 1：单台计算机上的质因数之和

我们的第一个示例是一个计算给定范围内所有数的不同质因数之和的程序。计算质因数本身这个困难的部分，由 `SymPy` 的 `primefactors()` 函数完成，该函数类似于 MATLAB 的 `factor()`。^(⁸³) 串行版本如下所示：

MATLAB：

```

% code/dask/prime_seq.m

A = 2;

B = 10000000;

incr = 1;

[S, dT] = my_fn(A, B, incr);

fprintf('A=%d B=%d, %.3f sec\n',...

         A, B, dT);

fprintf('S=%ld\n', S);

function [S, dT]=my_fn(A, B, incr)

tic;

S = 0;

  for i = A:incr:B-1

   S = S + sum(unique(factor(i)));

end

dT = toc;
```

## Python

```python
#!/usr/bin/env python3
```