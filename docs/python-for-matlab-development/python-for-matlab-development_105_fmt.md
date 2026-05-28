# `code/dask/my_fn.py`

```python
from sympy import primefactors
import time

def my_fn(a, b, incr):
    Ts = time.time()
    s = 0
    for x in range(a,b,incr):
        s += sum(primefactors(x))
    return s, time.time() - Ts
```

MATLAB:

```matlab
% code/dask/prime_dask.m
Im = @py.importlib.import_module;
ddist = Im('dask.distributed');
my_fn = Im('my_fn');
br_dask = Im('bridge_dask');
A = int64(2);
B = int64(1000000);
n_jobs = int64(11*18);
[S, elapsed] = do_parallel(A, B, n_jobs);
tic;
%client = ddist.Client('127.0.0.1:8786');
client = ddist.Client('143.198.155.245:8786');
client.upload_file('my_fn.py');
% 将任务提交到集群
output = py.list({});
incr = n_jobs;
for i = 0:n_jobs-1
    task = br_dask.delayed(my_fn.my_fn, A+i, B, incr);
    output.append(task);
end
output = br_dask.compute(output);
client.close()
% 后处理：聚合部分解
S = 0;
mat_output = py2mat(output);
for i = 1:length(mat_output)
    partial_sum = mat_output{i}{1};
    S = S + partial_sum;
end
dT = toc;
fprintf('A=%d B=%d, %.3f sec\n', A, B, dT);
fprintf('S=%ld\n', S);
```

### 14.14.2 使用 MATLAB 的并行千兆像素曼德博集合

这里有一个 MATLAB 程序，它将第 14.13.6 节中展示的 `MB_numba_dask2.py` 文件中 Numba 增强的 `MB()` 函数发送到 `dask` 集群：

MATLAB:

```matlab
% code/dask/MB_numba_dask.m
Im = @py.importlib.import_module;
ddist = Im('dask.distributed');
np    = Im('numpy');
br_dask = Im('bridge_dask');
MB_py = Im('MB_numba_dask2');
N = int64( 35000 );
Tc = tic;
nR = N; nC = N;
Re_limits = np.array({-0.7440, -0.7433});
Im_limits = np.array({ 0.1315,  0.1322});
n_groups = 16;
n_jobs_per_group = 18;
n_jobs = n_jobs_per_group*n_groups;
%client = ddist.Client('127.0.0.1:8786');
client = ddist.Client('143.198.155.245:8786');
results = py.list({});
for c = 1:n_groups
    tasks  = py.list({});
    Tcs = tic;
    for i = 1:n_jobs_per_group
        job = br_dask.delayed(MB_py.MB, nC, Re_limits, ...
            nR, Im_limits, 255, (c-1)*n_jobs_per_group+i-1, n_jobs);
        tasks.append(job);
    end
    results.extend( br_dask.compute(tasks) )
    Tce = toc(Tcs);
    fprintf('group %2d took %.3f sec\n', c-1, Tce)
end
client.close();
% 重新组装图像
img = zeros(N,N, 'uint8');
Tps = tic;
for i = 1:length(results)
    my_rows  = py2mat(results{i}{1}) + 1; % 0 索引转 1 索引
    img_rows = py2mat(results{i}{2});
    img(my_rows,:) = img_rows;
end
Tpe = toc(Tps);
fprintf('py2mat conversion took %.3f\n', Tpe);
Te = toc(Tc);
fprintf('%.3f  %5d\n', Te, N);
imshow(img)
```

性能比纯 Python 解决方案（耗时 84 秒）更差，因为 MATLAB 需要将 1.1 GB 的 `img` 数组从 Python 原生变量转换为 MATLAB 原生变量。如果不考虑这个转换时间，性能将是相同的：

```
>> MB_numba_dask
group  0 took 5.658 sec
group  1 took 6.278 sec
group  2 took 4.812 sec
group  3 took 5.495 sec
group  4 took 4.746 sec
group  5 took 5.028 sec
group  6 took 4.982 sec
group  7 took 5.098 sec
group  8 took 4.911 sec
group  9 took 5.036 sec
group 10 took 4.923 sec
group 11 took 4.790 sec
group 12 took 4.946 sec
group 13 took 4.530 sec
group 14 took 4.528 sec
group 15 took 5.192 sec
py2mat conversion took 30.253
111.463  35000
```

回想一下表 14-1 中 MATLAB 处理 5k x 5k 性能的 112 秒时间。在这里，借助 Python 和一个 18 节点的集群，MATLAB 可以在相同时间内解决一个规模大 49 倍的问题。性能提升大部分来自 Numba 极大地加快了 `MB()` 函数的运行速度，其余则源于计算机数量的增加。对于这个问题，MATLAB 相比 Python 的一个优势是 MATLAB 能够渲染千兆像素图像。Python 中的 `matplotlib` 会因一个不明确的错误而失败。

![](img/516169_1_En_14_Figg_HTML.jpg)

### 14.14.3 使用 MATLAB 的并行直接频率响应

前两个 MATLAB 与 `dask` 结合使用的示例直接将 Python 的 `dask` 模块导入 MATLAB，并从中向 `dask` 集群提交任务和收集结果。对于更为复杂的直接频率响应问题，这种方法行不通。由于未知原因，用于此问题的基于 Python 的工作节点会失去与 MATLAB 中 `dask` 集群变量的连接，导致任务失败。

我能够让 MATLAB 运行这个大型问题的唯一方法是将所有 `dask` 对象和活动（集群创建、数据分发、任务提交）都保留在 Python 中。然后 MATLAB 必须通过一个 Python 封装器间接访问基于 `dask` 的求解器。这实际上是将第 14.13.7.6 节中展示的 `pysolve.py` 的直接频率响应求解器代码组织成三个函数的主要原因。`pysolve.py` 中的 `remote_solve()` 函数就是 MATLAB 需要的封装器。

下面是使用基于 Python 的 `dask` 求解器解决直接频率响应问题的 MATLAB 代码。我们不会从头重建有限元模型，而是从由 Python 程序 `code/dask/save_KCMb.py` 创建的 `.mat` 文件中加载矩阵。

MATLAB:

```matlab
% code/dask/run_fr_dask.m
clear all
Im = @py.importlib.import_module;
```

np      = Im('numpy');

pysolve = Im('pysolve');

T_main_s = tic;

%%

load('KCMb_7.mat') % K, C, M, b, keep_dof

fprintf('Keeping response at %d DOF\n', length(keep_dof))

load_history = load('load_hist_smoothed.txt');

n_jobs = int64(100);

n_FFT = 4096;

solve_with = "coiled";

% solve_with = "localhost";

% solve_with = "digitalocean";

%%

n_freq = int64(floor(n_FFT/2));

dT    = 5.0e-4;   % load_history 中每个点之间的时间间隔

Time  = 0:dT:dT*n_FFT;

Fs    = 1/dT;     % 采样频率 Hz

Hertz = linspace(0, Fs/2, n_freq); % 仅到奈奎斯特频率

dHz   = Hertz(2);

P = fft(load_history);

P_Nyq = P(1:n_freq);

n_dof = size(M,1);

fprintf('Time: %d times, 0.0 to %.6f sec steps of %.6f sec\n', n_FFT, Time(end), dT)

fprintf('Freq: %d freq, 0.0 to %.1f Hz steps of %.1f Hz\n', n_freq, Hertz(end), dHz)

% 将 MATLAB 原生数组转换为 Python 原生

b = rand(n_dof,1);

K = mat2py(K);

C = K * 0.004; % 比例阻尼

M = mat2py(M);

b = np.array(b);

P_Nyq = mat2py(P_Nyq);

Hertz = np.array(Hertz);

keep_dof = np.array(keep_dof).astype(np.int64);

x = pysolve.remote_solve(solve_with, n_jobs, K, C, M, b, P_Nyq, Hertz, keep_dof);

x = py2mat(x);

fprintf('%d frequencies took %.3f s\n', n_freq, toc(T_main_s))

```

## 性能与成本分析

性能和成本与表 14-11 中的值相似。Coiled 云上的负载会导致时间和成本的波动，这超出了我们的控制范围。

如第 14.13.7.8 节所述，在 MATLAB 中单台计算机上运行 2048 个频率的时间大约是 8.5 小时。借助 Python 和 Coiled 云上的 100 个工作者，时间缩短到大约 15 分钟，速度提升了 33 倍。

**表 14-12** 在 Coiled 云上基于 MATLAB 的直接频率响应的耗时和成本

| 工作者数量 | 频率数量 | 时间 [秒] | 成本 [美元] |
|:---:|:---:|:---:|:---:|
| 3 | 16 | 402.1 | 0.02 |
| 64 | 64 | 472.1 | 0.50 |
| 100 | 100 | 628.0 | 0.88 |
| 100 | 1024 | 743.0 | 0.95 |
| 100 | 2048 | 940.2 | 1.60 |

## 参考文献

1. Emery D. Berger. *Scalene: Scripting-Language Aware Profiling for Python*. 2020. arXiv: `2006.03879 [cs.PL]`. URL: [`https://github.com/plasma-umass/scalene`](https://github.com/plasma-umass/scalene)
2. James F. Doyle. *结构中的波传播*. Springer-Verlag, 1989.
3. Serge Guelton 等. “Pythran：实现科学 Python 程序的静态优化.” 载于：*计算科学与发现* 8.1 (2015), 第 014001 页.
4. Jonathan Richard Shewchuk. “Triangle：设计一个二维高质量网格生成器与 Delaunay 三角剖分器.” 载于：*应用计算几何：面向几何工程*. 由 Ming C. Lin 和 Dinesh Manocha 编辑. 第 1148 卷. 计算机科学讲义. 选自第一届 ACM 应用计算几何研讨会. Springer-Verlag, 1996 年 5 月, 第 203–222 页. URL: [`www.cs.cmu.edu/˜quake/triangle.html`](https://www.cs.cmu.edu/%257Equake/triangle.html)

# 脚注

# 15. 语言陷阱

## 15.1 棘手的语言特性

MATLAB 和 Python 都不是理想的语言。两者都有令人烦恼甚至危险的地方——至少对于不熟悉语言设计中微妙细节的新手程序员而言。对其中一种语言的批评者热衷于指出对方的缺陷，同时对自己偏爱的语言所遭受的批评却视而不见。

## 15.2 MATLAB

*   **危险：** 混合类型的数学表达式会*向下*而非向上进行类型转换。

```

>> a = uint8(1) - 0.6

a =

uint8

```

结果得到 0 而不是 0.4，这令人震惊（或者更直白地说，完全错误）。MATLAB 在这一设计选择上独树一帜。C、C++、Fortran、Go、Java、JavaScript、Julia、Mathematica、OCaml、Python+NumPy、R、Rust 和 Scala 都向上进行类型转换，并对上述计算得出预期的浮点值 0.4。不言而喻，MATLAB 的向下转换可能会导致毫无戒心的开发者得到错误的结果。

*   **危险：** MATLAB“命名空间中存在你能想到的一切函数”这一特性，会导致在装有相同版本 MATLAB 但不同工具箱的计算机上运行同一个 `.m` 文件时产生意外行为。

*   **令人烦恼：** 虽然一个 `.m` 文件可以包含多个函数，但除非将这些函数所在文件转换为类，否则其他 `.m` 文件无法调用其中任何一个。在不采用面向对象编程的大型项目中，代码最终会分散在数百个 `.m` 文件中。这给代码编辑、调试和项目文件管理增加了后勤上的麻烦。

*   **令人烦恼：** 矩阵切片继承的是原始矩阵的维度，而不是切片自身的维度。例如，如果我们创建一个 4D 矩阵，然后沿每个维度提取一个 1D 切片，结果是具有两、三或四个维度的数组：

```

>> a = rand(3,4,5,6);

>> size(a)

3     4     5      6

>> size(a(:,1,1,1))

3     1

>> size(a(1,:,1,1))

1     4

>> size(a(1,1,:,1))

1     1     5

>> size(a(1,1,1,:))

1     1     1       6

```

这会为诸如 `plot()` 这样无法处理超过两个维度的函数带来问题：

```

>> plot(a(:,1,1,1))   % 绘图成功

>> plot(a(1,:,1,1))   % 绘图成功

>> plot(a(1,1,:,1))

使用 plot 时出错

数据不能超过两个维度。

```

为了获得期望的行为——在此示例中即一个仅包含一个维度的切片——必须显式地对切片调用 `squeeze()`。

*   **令人烦恼：** 默认的 Emacs 键绑定（*control*-w 和 *control*-y 用于剪切和粘贴；*control*-x *control*-s 用于保存等）会激怒新的 MATLAB 用户。世界其他地区已采用适用于 GUI 应用程序的 Windows 键绑定（*control*-c 和 *control*-v 用于剪切和粘贴；*control*-s 用于保存等）。虽然 MATLAB IDE 允许用户将键绑定更改为 Windows 风格（主页/环境/偏好设置/键盘/快捷键/活动设置/Windows 默认设置），但似乎只有少数用户知道这一点，并继续使用默认设置。

*   **令人困扰：** 无法将函数定义直接复制粘贴到 MATLAB 提示符中——`"Error: Function definition not supported in this context. Create functions in code file."` 这阻碍了对简单函数进行快速的沙盒测试。

### MATLAB 的不足

- **令人困扰：** 无法控制传递给 MATLAB 函数的参数是按值传递还是按引用传递。^((87)) 从技术上讲，所有参数都是按引用传递的，但 MATLAB 的即时编译器会采用写时复制逻辑，该逻辑可能遵循也可能不遵循代码中对输入和输出参数的重复使用。如果即时编译器决定复制变量而非就地使用，则可能导致严重的性能损失。

- **令人困扰：** 除非工具箱已经安装，否则无法确定一个 MATLAB 程序需要哪些工具箱。换句话说，如果从外部来源收到一个 MATLAB 程序，`matlab.codetools.requiredFilesAndProducts()` 函数只会报告在运行该函数的计算机上已找到的程序所依赖的工具箱。

- **令人困扰：** 关键字 `end` 用于关闭索引范围、类、函数、`if` 块、`for` 循环、`while` 循环、`switch/case` 语句、`try/catch` 块以及其他可能的语言结构。虽然 MATLAB 集成开发环境能轻松看出一个 `end` 关闭的是哪个结构，但在功能较弱的编辑器、纸质文档、网页、PDF 或 Word 文档中查看 m 文件时，这种配对会变得相当困难。

- **令人困扰：** 这是函数还是数据结构？MATLAB 使用圆括号向函数传递参数*同时*也用于索引矩阵和元胞数组，这使得无法通过查看代码（例如 `grF(x)`）来判断符号 `grF` 是函数还是数据容器。在 Python 中，你可以立即识别出 `grF(x)` 是函数调用，而 `grF[x]` 是索引过的数据容器。

- **令人困扰：** 由 MathWorks 托管的 MATLAB File Exchange 是一个由用户贡献的 m 文件资源库，非常有价值。与 GitHub 或 SourceForge 等其他代码分享网站不同，从 File Exchange 下载需要经过身份验证的登录。这一不便之处阻碍了 MATLAB 代码的分享。

## 15.3 Python

- **危险：** 列表和 NumPy 数组在使用标量索引进行索引时会进行边界检查，但使用索引范围时不会。这可能导致错误在程序中无声地传播（第 4.​3.​6 节）。

- **危险：** 当等号右侧是一个数据结构时，左侧的变量会成为右侧数据的*引用*，而不是其副本（第 4.​8 节）。因此，如果 `A` 是一个列表、字典、NumPy 数组等，那么赋值语句 `B = A` 会得到两个指向同一数据的变量。对 `A` 的更改会变成对 `B` 的更改，反之亦然。要使 `B` 包含 `A` 数据的副本，必须导入 `copy` 模块并使用其 `copy()` 或 `deepcopy()` 函数。不了解 Python 引用复制行为的开发者很可能会看到数据损坏以及代码计算结果错误。

- **令人困扰：** 对于期望 `N` 出现的 MATLAB 程序员来说，索引切片 `M:N` 以及对 `range(N)` 和 `np.arange(N)` 的调用令人抓狂。这些结构“不使用终值”的行为需要很长时间才能适应。

- **令人困扰：** NumPy 和 SciPy 都有一个名为 `linalg` 的线性代数子模块，它们包含常用函数但功能不同。例如，`scipy.linalg.eig()` 可以解决标准 (*Ax* = *λx*) 和广义 (*Ax* = *λBx*) 特征值问题，而 `numpy.linalg.eig()` 只解决标准问题。至少，这两个函数的内置文档中都应该有一个“另请参阅”条目，指向另一个模块中的对应函数。

- **烦人之处：** 与 MATLAB 全面实现的稀疏矩阵相比，Python 的稀疏矩阵感觉像是二等公民。

- **烦人之处：** 类型转换 NumPy 数组需要与转换标量不同的表示法。以下示例展示了将标量和数组从整数转换为双精度浮点数的语法：

```
In : I_scalar = 4  # 64 位整数

In : I_array  = np.array(([5, 6]), dtype=np.int64)

In : D_scalar = np.double(I_scalar)        # <- 类型转换标量

In : D_array = I_array.astype(np.double)   # <- 类型转换数组
```

`I.astype(np.double)`这种表示法并不直观。如果能像编写`np.float(I)`这样来将 NumPy 数组以及标量都转换为浮点数，会更清晰。

- **烦人之处**：虚拟环境是一个累赘。它们迫使你将模块集合分散到多个环境中，然后还得记住哪个环境能做什么，从而增加了不必要的复杂性。
- **烦人之处**：与 MATLAB 内置文档一贯的高质量相比，人们进行科学与工程工作所需的众多 Python 模块的文档在质量、风格和位置上都参差不齐。虽然良好的网络连接和浏览器通常能快速解答复杂的 Python 问题，但处于隔离网络中的开发者在这方面会遇到困难。

# 脚注

# 索引

## A

- `Altair`
- `Anaconda`
- 动画
    - 弹跳球
    - 关键元素
    - `matplotlib`
- 应用程序编程接口（API）
- `arange()`函数
- 面积图
    - `imagesc()`
    - `imshow()`
        - 双线性插值
        - 前缀
- `astimezone()`方法
- `Austin`

## B

- 基本线性代数子程序（BLAS）
- 块压缩行（BSR）
- `Bokeh`
- 桥接模块
- 内置作用域

## C

- `Cartopy`
    - 制图转换
    - 在地图上绘制线条
    - MATLAB
    - 在地球仪上叠加等值线
    - 投影函数
    - 按值对地图区域进行着色
- 单元格数组
- 类
    - `circle`
    - 构造函数方法
    - 自定义异常
    - 自定义打印方法
    - `__init__()`函数
    - 实例
    - 私有变量`与`公有变量
    - 变量
- 云服务
    - `Coiled`云
    - 性能
    - 运行
- 命令行输入
    - `argparse`
    - 函数参数
    - `sys`模块
- 逗号分隔值（CSV）
- 注释
    - 块注释
    - 定义
    - 文档字符串
- 通信密集型并行程序
- 压缩稀疏列（CSC）
- 压缩稀疏行（CSR）
- 计算机代数系统（CAS）
- `conda`包管理器
- `Conda`环境
- `conda-meta`目录
- `conda update`
- 转换器
- `create_dataset()`函数
- 创建、读取、更新、删除（CRUD）
- 曲线拟合
    - `differential_evolution()`
        - 桥接模块
        - `compute_ABCD()`函数
        - 代价函数
        - `matlab.engine`
        - `py`模块
        - 解决方案
    - 拟合模型
        - `curve_fit()`
        - `differential_evolution()`
        - `r2_score()`函数
        - `scipy.optimize`模块
        - `scipy.optimize.curve_fit()`
        - 测试数据
    - 线性回归
    - 多项式
- `Cython`
    - MATLAB
    - 并行`for`循环
    - 性能
    - Python

## D

- `Dask`
    - 执行范式
    - 曼德勃罗集
    - 多台计算机
    - 并行 MATLAB
    - 质因数
    - 调度器
    - 单台计算机
- 数据
    - 清洗
        - 列
        - 更改
            - 数据类型
            - 重命名
            - 时间戳
- 数据类
    - 便利机制
    - 动态修改
    - 实体关系
    - 字段值
        - 调用`mag()`
        - MATLAB `与` Python
        - `point`
        - 遍历
        - 类型强制
        - 类型验证，`Pydantic`
- 数据容器
    - MATLAB
    - 非标量
    - Python
- 数据帧。见“表格”
- 日期
    - `datetime`
    - 字符串
    - 时间差
    - `datestr()`函数
    - `datetime`函数
    - `datetime`模块
    - `datetime`对象
    - 定义
    - ISO 8601 时间字符串
    - 儒略日
    - Unix 纪元秒数
- 调试
    - Python 代码
        - 日记模式
        - `do_work()`
        - 函数
        - 日志记录
        - 步骤
        - `trace`模块
- `deepcopy()`函数
- 字典
    - 添加键
    - 创建
    - 数据集
    - 遍历键
        - `for key in dict`
        - 按键排序
        - 按值排序
    - 键是否存在
        - `.get()`方法
        - `KeyError`
        - `.setdefault()`方法
        - `test_key`
    - 局限性
    - `与`列表
    - Python
        - 国家到首都的关系
        - 下标
        - `struct`
        - 元组
    - 值
- 字典键（DOK）
- 微分方程
- 直接频率
- 目录
    - 内容
    - 创建
    - 删除
    - 表达式
    - 树
        - `.csv`文件
        - `dir()`
        - `find()`函数
        - 生成器
        - `os.walk()`
        - 输出
        - 打印
        - 遍历
        - `.xlsx`文件
- 杜芬振子方程

## E

- `eig()`特征值函数
- 封闭作用域
- `enumerate()`函数
- 枚举
- `env`命令
- Excel `.xls`、`.xlsx`
    - 定义
    - 提取列
    - 提取矩形块
    - 提取行
    - 获取工作表名称
    - 读取文件
    - 写入
    - 写入文件
- 异常
    - `executemany()` 函数
    - 外部可执行文件
    - 类字节对象
    - `CalledProcessError`
    - `ffmpeg`
    - `os.environ` 对象
    - 结果
    - `Result.returncode`
    - 状态
    - `subprocess` 模块
    - `subprocess.CalledProcessError`
    - `subprocess.run()`
    - `subsystem.run()`
    - `system()` 函数

## F

- `f2py` 命令
- 文件系统
    - 删除
    - 查找
    - MATLAB
    - Python
- 有限元
- `FlameGraphs`
- `For` 循环
    - 定义
    - 示例
    - 退出
    - 嵌套循环
    - 变量
- Fortran 编译模块
- 函数
    - 比较运算符
    - 装饰器
    - 关键字参数
    - 左侧参数计数
    - 列表/字典/数组
    - 按值传递/按引用传递
    - 类型注解/参数验证
    - 可变参数

## G

- 生成器
    - 定义
    - `range()`
    - `yield`, `next()`
- `geo*` 函数
- `GeoPandas`
    - 创建绘图
    - 数据框
    - 数据类型
    - 地理数据
    - 连接
    - 地图
    - 桥接函数
    - 桥接模块
    - 问题
    - `iterrows()`
    - 绘图
    - `read_file()` 函数
    - shapefile
    - Zillow 研究
    - 邮政编码
- `global` 关键字
- 全局作用域

## H

- `h5dump` 命令行工具
- HDF5
    - 数据集
    - `netcdf4`
    - 读取文件
    - 读取/写入数据集属性
    - 写入文件
- 分层数据文件
- 高性能计算
    - 基准性能
    - 有限元
    - 曼德勃罗集
    - 多处理
    - 性能分析工具
        - `austin`
        - 缺点
        - `flameGraphs`
        - `scalene`
    - 参考硬件/操作系统
    - 参考问题
        - 有限元
        - 曼德勃罗集

## I

- `If` 语句
    - 布尔表达式/运算符
    - 范围测试
    - 变量
- `imshow()` 函数
- 缩进
- 括号/圆括号
- 索引
    - 链
    - 方法
    - 步长
    - MATLAB 和 Python
    - 起始/结束/负索引
    - 制表符
    - 零基索引/索引范围
- 索引掩码
    - 创建
    - 数学运算
    - 用途
- `input()` 函数
- 安装，MATLAB
    - 创建/运行 Python 程序
    - 下载
    - 包
    - 安装后配置/检查
    - 工具箱
- 插值
    - 一维
        - 外推
        - 线性
        - 样条
    - 二维
        - 三次/线性方法
        - 评估
        - 函数
        - 网格
        - `griddata()`
        - `meshgrid()`
        - `np.meshgrid()`
        - 测试
        - 值
- 整数
- `ipython`, IDE
    - 应用程序导航器
    - 自动加载模块
    - 版本
- 可迭代对象
- `iterrows()`

## J

- JavaScript 对象表示法
    - 复杂数据结构
    - MATLAB 到 Python
    - Python 到 MATLAB
- 即时编译

## K

- 关键字参数

## L

- `Lambda` 函数
- 线性代数
    - 特征值问题
    - 线性方程
    - MATLAB 与 Python 对比
    - 奇异值分解
- 线性索引
- 线性规划问题
    - 整数值
    - `linprog()`
        - 桥接模块
        - 系数
        - MATLAB
        - Python 解法
        - 关系
        - sol 对象
        - 求解状态
        - 目标函数
    - `PuLP` 模块
    - 服务器属性
- `Linux`
- `list()` 函数
- 列表的列表
- 列表
- 局部作用域
- 局部变量，然后闭包、全局和内建
- 长表达式

## M

- `macOS`
- 曼德勃罗集
- MATLAB `.mat` 文件
    - 数据容器
    - 定义
    - 读取文件
    - v7.3
    - 写入文件
- MATLAB
    - Anaconda 的 Python 发行版
    - 调用函数
    - 现有会话
    - 新会话
    - 元胞数组
    - 配置
        - Linux/macOS
        - `matlab` 命令
        - 路径
        - `pyenv` 命令
        - Windows
    - `container.Map`
        - 定义
        - 特征求解器
    - 环境变量
    - 错误
    - 函数
    - `horzcat()` 函数
    - 混合环境
        - 不兼容的库函数
        - 安装引擎
        - 安装模块
        - 库
            - 底层库
        - 映射
    - `matpy`
    - `py` 模块
    - Python
        - `pyversion()`
        - 软件
        - 软件工程
        - 工具箱
    - MATLAB 原生变量
    - MATLAB 性能
    - MATLAB/Python 交互
        - 文件 I/O
        - 系统调用
        - TCP/IP 交换
- `matplotlib` 模块
- 矩阵切片
- `maxsplit` 关键字
- `MB()` 函数
- 混合类型数学表达式
- 模块/包
    - 定义
    - `def main()`
    - 依赖冲突/虚拟环境
    - 安装
    - 命名空间
    - 搜索路径
- `MongoDB`
    - 桥接模块
    - 删除
    - 读取
    - 更新
- `mset` 函数
- `multiprocessing` 模块

## N

- Natural Earth 项目
- `ncinfo` 命令行工具
- 网络通信协议
    - HTTP, HTTPS
    - MATLAB 客户端
    - Python 作为 Web 服务器
    - TCP/IP
    - TCP 服务器
- `Numba`
    - 关键字参数
    - 局限性
    - 曼德勃罗程序
    - MATLAB
    - 并行 for 循环
    - 性能
    - Python
- 数值数组
    - 常量
    - 转换
        - MATLAB
        - 命令
        - 函数
        - 单精度浮点数
        - 类型
- `NumPy`
    - 定义
    - 特征求解器
- `NumPy` 和 `SciPy`
- `NumPy` 数组
    - 十进制转二进制
    - 十六进制转十进制
    - MATLAB
    - 标量
    - 运算
        - 加/减
        - 逐元素
        - 厄米特
        - 转置
    - 感兴趣项
        - `find()` 函数
        - 最小项
    - 排序数据
    - 文本文件，读取
    - 类型转换
- `NumPy` 数组
    - 二进制文件
        - 字节序转换
        - 元数据
        - 读取
        - 二进制转十进制
    - 广播
        - 定义
        - 距离矩阵
        - 内存
        - 归一化向量
        - 规则
    - 复数标量
    - 创建
        - 对角矩阵
        - 单位矩阵
        - 膨胀矩阵
        - 等间距向量
        - 对数间距向量
        - 网格矩阵
        - `nan()` 函数
        - `np.array()` 函数
        - 全一矩阵
        - 随机矩阵
        - `range()` 函数
        - 堆叠
        - 测试矩阵
        - 三角矩阵
        - `zeros()` 函数
    - 数据类型
        - 十进制转十六进制
        - 定义
        - 维度
        - `dtype` 参数
        - `dtype` 参数
        - 格式化
        - 导入
        - MATLAB
        - `vs.` MATLAB 矩阵
        - 列主序
        - 维度
        - 数值字面量
        - `.resize()` 方法
        - 标量
        - `np.array()` 函数
    - `NumPy` 模块
    - 操作
        - 按位操作
        - 逐元素操作
        - 厄米操作
        - 乘法
        - 转置
    - 子矩阵
        - 提取
        - 更新
    - 关注项
        - 最近项
        - `find()` 函数
        - 最长项
        - 排序数据
    - 数值
    - 文本文件
        - 读取
        - 写入
    - 类型转换
- `whos` 命令

## O

- 面向对象编程 (OOP)
    - 类方法
    - 冲突
    - 计算性能
    - 性能影响
    - 模拟
    - 向量化解法
- `open()` 函数
- 操作系统
    - 计算机硬件
    - CPU 周期
    - 环境变量
    - 独立方法
    - MATLAB
    - 内存
    - 数值分析
    - 程序迭代
    - Python
    - 随机向量
    - `setenv()` 函数
    - 模拟
    - `TypeError` 错误

## P

- `pack()` 函数
- `Pandas`
- 并行程序
- `pathlib.Path()` 对象
- Pickle 文件
- 固定
- 数据透视表
    - 多级聚合
    - 单级聚合
- `Plotly`
- 图形
    - 批处理模式
    - 编辑
    - 线图
    - 点图
    - 堆叠图
- 绘图
    - 区域图
    - `Cartopy`
    - 探索
    - 线图
    - 点图
    - 点/线图
        - 日期和时间
        - 直方图
        - MATLAB
        - `matplotlib` 命令
        - 公制 `vs.` 美制
        - Python
        - 保存
        - 命令
        - 格式
        - MATLAB
        - `matplotlib` 函数
        - Python
        - `subplots()` 函数
        - 杨氏模量
- `PostgreSQL`
- `prange()` 函数
- 质因数
- `print()` 函数
- `purchase_orders`
- `py2mat()` 函数
- Python
    - 部署
    - 发行版
    - 列表
        - `.append()` 方法
        - 列表推导式
        - `.count()` 方法
        - 创建
        - 空列表
        - `.extend()` 方法
        - 提取范围
        - `find()` 函数
        - `IndexError`
        - 索引
        - `.insert()` 方法
        - 长度
        - 列表推导式
        - 成员测试
        - 合并
        - 方法
        - 嵌套容器
        - `.pop()` 方法
        - `.remove()` 方法
        - `.reverse()` 方法
        - 排序
        - 子集提取
        - 拆分
        - `ValueError` 异常
        - 压缩
    - 模块导入
    - 多台计算机
    - 高昂成本
    - `sys.path`
    - 别名
    - `append()`
    - `insert()`
    - 元组
        - 创建
        - 可哈希
        - 括号
        - `vs.` Python 列表
        - 包装
    - 变量
- Python 包索引 (PyPI)
- Python 特有方面
- `Pythran`
    - 注释
    - MATLAB
    - 并行 for 循环
    - 性能

## Q

- `Qt` 库

## R

- `range()` 函数
- `.ravel()` 方法
- 读取-求值-打印-循环 (REPL)
- `readmatrix()` 函数
- `read_text()` 方法
- `Redis`
- 回归
    - 置信区间
    - 最小二乘法
    - 普通回归
    - 加权回归
    - 预测区间
- 正则表达式
- 远程计算机
- `re.split()`
- 根
    - 多变量
    - 非线性方程组
    - 单变量
- `rstrip()` 方法

## S

- 卫星
- `save()` 命令
- 标量
- `Scalene`
- `SciPy`
    - 坐标格式 (COO)
    - 定义
    - 特征值求解器
    - 函数
    - 优化模块
    - `scipy.interpolate` 模块
    - `scipy.optimize` 模块
    - 稀疏模块
    - 稀疏矩阵
- `scipy.io.loadmat()`
- `Seaborn`
- 海面温度 (SST)
    - `CARTOPY_USER_BACKGROUNDS`
    - 数据
    - `earth_veg_index.jpeg`
    - 正射投影
- 二阶微分方程
- `.send()` 和 `.recv()` 函数
- 集合操作
    - MATLAB，`unique()` 函数
    - Python，`.issuperset()` 方法
    - 迭代/类型转换
    - `set()` 函数
- 模拟退火
    - 派生自 Annealer 的类
    - 自动估计器
    - `compute_distances()` 函数
    - 概念
    - `.distance_matrix` 属性
    - `energy()` 方法
    - 参数
    - 要求
    - `.state` 属性
    - 旅行商问题 (TSP)
- 奇异值分解 (SVD)
- `sleeper()` 和 `add_numbers()`
- 求解器代码
- 求解器函数
- 南美小麦产量
    - `add_geometries()` 函数
    - 边界
    - `Cartopy`
    - 案例
    - 国家名称
    - 数据库
    - 低分辨率文件
    - 多边形区域
- 稀疏矩阵
    - 块稀疏行 (BSR)
    - 创建
    - 坐标格式 (COO)
    - `csr_matrix()`
    - 稠密矩阵
    - 稠密表示
    - 坐标格式 (COO)
        - 维度
        - 索引值
        - 索引/值
        - 行/列
        - `sparse()` 函数
        - `.sum_duplicates()` 方法
    - 压缩稀疏列 (CSC)
    - 压缩稀疏行 (CSR)
    - 定义
    - 对角格式 (DIA)
    - 键字典格式 (DOK)
    - 格式/功能
    - 输入和输出 (I/O)
        - 组成部分
        - `.mat` 文件
        - `pickle` 文件
    - 链表格式 (LIL)
    - 线性代数
        - 特征值问题
        - 基于有限元的对称矩阵
        - 线性方程组
        - 矩阵摘要
        - 矩阵-向量乘积
        - `scipy.sparse.linalg` 模块
    - `Sturm` 序列号
    - MATLAB
    - `SciPy`
- `.split()` 方法
- `SQLite`
- 堆叠图
- `start_new_order()` 函数
- 启动目录
- `startup.m` 文件
- `strftime()` 函数
- 字符串
    - 字节数组
    - 字符数组
    - 格式化
    - 操作
        - 追加
        - 命令
        - 转换
        - 长度
        - `<TAB>` 键
        - 方法链式调用
        - 重复
        - 替换字符
        - 正则表达式
        - 用途
        - 替换
        - 分隔
        - 子字符串
        - 拆分
        - 模板
        - 测试
            - 相等性
            - 起始字符
            - 子串
            - 结尾字符
- `struct()` 函数
- `struct` 的 `pack()` 函数
- 结构化数据
    - 类
    - `hasattr()` 函数
    - `isfield()` 函数
    - `namedtuple` 方法
    - `SimpleNamespace` 方法
- 结构化查询语言 (SQL)
- 符号数学
    - `char` 属性
    - 冲突
    - 导数
    - 积分
    - `laplace_transform()` 函数
    - 线性代数
    - `s.doit()`
    - 级数
    - 求解方程
    - `SymPy` 代码生成器
    - 变量
- `sys.exc_info()` 函数

## T

- 表格
    - 列
        - 添加
        - 删除
        - 名称
        - 类型
    - 创建
    - `.describe()` 方法
    - `groupsummary()` 方法
    - `head()` 函数
    - 索引
    - `.info()` 方法
    - 连接
        - 风格
        - 食品成本
        - 食品类型
        - `how=` 关键字
        - `wxp` 列
    - 加载
        - MATLAB 的表格
        - `pets.csv` 文件
        - `read_csv()`
        - 字符串/浮点类型
    - 行
        - 迭代
        - 排序
    - 大小
    - 子集
        - 所有列，选中行
        - 所有行，选中列
        - 筛选行，条件操作
        - 选中行，选中列
    - `summary()` 函数
    - `tail()` 函数
    - `.value_counts()` 方法
- 测试矩阵
    - 箭头矩阵
    - 随机稀疏矩阵
    - 数组
    - `spy()` 函数
- 文本文件
    - 赋值
    - 二进制文件
    - CSV
    - 定义
    - `NumPy` 的 `genfromtxt()` 函数
    - `pandas`
    - `python` 模块
    - `ini` 文件
    - I/O 异常
    - JSON
    - MATLAB/Python
    - 解析文本
    - 操作
        - 正则表达式
        - `re.split()`
        - 分割字符串，子串
        - 字符串搜索
        - 去除空白符
    - `pathlib`，读取损坏文件
    - 读取/写入数值数组
    - 读取 `ini` 文件
    - 标准输出 (STDOUT)
    - 写入 `ini` 文件
    - 写入 YAML 文件
    - XML
        - `lxml` 模块
        - 带命名空间
        - 漂亮打印
        - 不带命名空间
        - 写入 XML
    - YAML
- 时间
    - 当前时间
    - 定义
    - 模块
    - 字符串格式
    - `tic/toc/%timeit`
    - `time.strftime()` 函数
    - `time.time()` 函数
- 时区
- `.tobytes()` 方法
- `to_datetime()` 函数
- `.to_excel()` 方法
- `.tofile()` 方法
- 旅行商问题 (TSP)
- 遍历生成器
- 元组，`np.ones()` 函数
- 二维数组

## U

- 单位制
    - 定义单位
    - MATLAB
    - 公制值
    - `NumPy` 数组
    - `pint/`符号工具箱
    - Python
- Unix 纪元秒
- `urlread()` 函数

## V

- 变量赋值
    - `=`
    - 就地更新运算符
    - 语言
    - 打印表达式
    - 海象运算符
- 向量化
- 虚拟环境
    - `Anaconda`
    - 命令
    - 定义
    - `matpy`
    - 开源语言

## W

- `which` 命令
- `While` 循环
- `whosmat()` 函数
- `write()` 方法

## X

- `$max` 聚合

## Y

- `yield` 关键字
- 杨氏模量

## Z

- `zoneinfo`
- `zoneinfo` 模块

# 脚注