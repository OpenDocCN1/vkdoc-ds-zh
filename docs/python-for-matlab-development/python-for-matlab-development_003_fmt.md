# 1.1 通过 MATLAB 等价对应学习 Python

作为 MATLAB 开发者，你在学习 Python 时已具备先发优势，因为这两种语言之间存在许多共通之处。本书采用类似“罗塞塔石碑”的方法，将 MATLAB 和 Python 代码并列呈现。MATLAB 代码显示在左侧，而 Python 代码（通常以 `ipython` 交互会话形式呈现）则位于右侧：

| **MATLAB:** | **Python:**
| --- | --- |
| `>> format long e>> x = [2.3 3.3];>> y = [-4 5.0];>> x*y'7.300000000000001e+00>> whosName Size Bytes Classx    1x2  16    doubley    1x2  16    double` | `In :import numpy as npIn : x = np.array([2.3, 3.3])In : y = np.array([-4, 5.0])In : x.dot(y)Out: 7.300000000000001In : whosVar Type     Data/Infox   ndarray  2: float64, 16 bytesy   ndarray  2: float64, 16 bytes` |

为保持示例简洁，我对它们进行了精简编辑；实际上，两个 `whos` 命令返回的结果都包含比展示部分更多的列。此外，MATLAB 中的 `ans` = 行也在全书各处被省略了。

同样有帮助的是，Python 的数值与科学计算模块与 MATLAB 的对应模块之间共享了许多函数名称。表 1-1 展示了其中的一小部分。Python 函数的前缀 `np`、`npr`、`plt` 和 `sci` 分别通过以下 Python 语句定义：`import numpy as np`、`import numpy.random as npr`、`import matplotlib.pyplot as plt` 和 `import scipy.interpolate as sci`。

**表 1-1** MATLAB 和 Python 共有的部分函数名称

| 类别 | MATLAB | Python | 说明 |
| --- | --- | --- | --- |
| 创建 | `meshgrid()` | `np.meshgrid()` | 创建一对坐标数组 |
|  | `logspace()` | `np.logspace()` | 指数分布序列 |
|  | `linspace()` | `np.linspace()` | 均匀分布序列 |
|  | `ones()` | `np.ones()` | 创建全 1 数组 |
|  | `zeros()` | `np.zeros()` | 创建全 0 数组 |
|  | `rand()` | `npr.rand()` | 创建随机数组 |
| 运算 | `all()` | `np.all()` | 若所有项均为真则返回真 |
|  | `angle()` | `np.angle()` | 复数的幅角 |
|  | `any()` | `np.any()` | 若任一项为真则返回真 |
|  | `ceil()` | `np.ceil()` | 向上取整函数 |
|  | `cross()` | `np.cross()` | 叉积 |
|  | `cumprod()` | `np.cumprod()` | 累积乘积 |
|  | `cumsum()` | `np.cumsum()` | 累积求和 |
|  | `diag()` | `np.diag()` | 创建对角矩阵或提取对角元素 |
|  | `tril()` | `np.tril()` | 提取下三角元素 |
|  | `triu()` | `np.triu()` | 提取上三角元素 |
|  | `floor()` | `np.floor()` | 向下取整函数 |
|  | `histogram()` | `np.histogram()` | 从坐标向量创建直方图矩阵 |
| 插值 | `polyfit()` | `np.polyfit()` | 对数据进行多项式拟合 |
|  | `griddata()` | `sci.griddata()` | 二维插值 |
| 绘图 | `plot()` | `plt.plot()` | 创建折线图 |
|  | `imshow()` | `plt.imshow()` | 以图形方式显示数组 |
|  | `contourf()` | `plt.contourf()` | 显示填充等值线 |
|  | `spy()` | `plt.spy()` | 显示稀疏模式 |

尽管这些相似之处令人鼓舞，但这两种语言在根本上仍存在差异，这些差异在第 4 章中会表现得最为明显。

## 1.2 Python 真的免费吗？

Python 及其最重要模块的源代码在开源许可下可免费获取。然而，将数百万行免费源代码转变为一套稳健的、能在您所在组织的 Windows、Linux 和 macOS 计算机上实现安全包管理的 Python 安装，其间存在着天壤之别。

至少有两家公司——Anaconda 和 ActiveState——提供获得许可的 Python 发行版，用于解决复杂的安装、捆绑、部署、包管理和安全审查问题。两家公司均允许个人免费使用其发行版，但针对商业部署则需收费。

大型组织可以通过构建自己的发行版来绕过 Anaconda 或 ActiveState 的许可发行版，但这需要相当的专业知识，并会增加相应的人力成本。除了要在多种架构上编译 Python 以及流行模块所需的数百个库依赖项之外，还需要一个经验丰富的团队来维护组织的私有模块仓库，并持续更新和扫描模块以保障安全。

从这个意义上说，任何超出小型组织规模的 Python 部署都绝对*不是*免费的。这种情况并非 Python 所独有。任何在组织内广泛部署的流行开源软件包，都应通过安装跟踪、安全审查、版本管理等方式进行管理。这同样适用于 Node 及其包管理器 `npm`、Ruby 及其 Gems、Rust 和 crates、Perl 和 CPAN、R 和 CRAN 等。

如果 Python 成本高昂，为什么不坚持使用 MATLAB 呢？主要有四个原因：（1）Python 如此流行，以至于大型组织可能已经拥有 Anaconda 或 ActiveState 的许可证，这与 MATLAB 及其用户的需求无关；（2）Anaconda 和 ActiveState 提供的 Python 许可费用远低于 MATLAB 许可证；（3）与 MATLAB 的工具箱不同，额外的 Python 模块不会增加成本；（4）Python 实例不受许可证服务器或许可证文件的约束——一旦公司从 Anaconda 或 ActiveState 购买了许可证，覆盖范围内的开发者后续使用 Python 便不受限制。最后一点极大地减少了在独立网络上部署、以及在家或旅途中使用的后勤麻烦。

## 1.3 工具箱呢？

MATLAB 的全部功能是通过大量按领域划分且单独销售的工具箱来实现的。然而，本书中的示例仅使用核心 MATLAB 产品。

尽管如此，许多原本只能通过众多工具箱（即地图、曲线拟合、数据库、并行计算、符号数学、统计、优化、控制、信号处理）获得的解决方案，都可以通过调用 Python 函数在 MATLAB 中实现。举一个简单的例子，加权最小二乘函数存在于 MATLAB 的曲线拟合工具箱中。但如果您没有曲线拟合工具箱，您可以在 MATLAB 中通过调用 `statsmodels` 模块中的 Python 函数 `WLS()` 来计算加权最小二乘（详见第 11.​7.​1 节）。