# 1.4 为什么 Python 不会取代 MATLAB

偶尔会有人问我，Python 是否会取代 MATLAB。对于某些任务，答案是肯定的，它确实可以。然而，当问题变得更为专业化时，你会发现 MATLAB 为解决这些问题而设计的工具箱没有可信的竞争对手。控制系统工程师会理所当然地坚持使用一款或多款控制工具箱；从事嵌入式硬件工作的工程师需要 `Coder` 和 `Fixed-Point Designer`；飞机设计师则可以通过 `Aerospace Blockset` 探索大型的设计权衡空间。

即使在存在 Python 替代方案的情况下，调用工具箱函数也比维护一个混合的 Python/MATLAB 代码库、保持 Python 安装与 MATLAB 更新同步、以及编写带有别扭函数调用和类型转换的更难阅读的代码要简单和清晰得多。从长远来看，通过避免将 Python 添加到 MATLAB 工作流程中的复杂性，使用工具箱甚至可能成本更低。利弊权衡会因用例、个人、项目和组织的不同而有所差异。

本书中将不再提及 Simulink，因为它自成一派；目前没有任何 Python 项目具备与其相近的功能^(²)。

当你需要方便、彻底且快速地解决那些最特定和最专业的工程问题时，MathWorks 的产品是无价的。Python 可以在 MATLAB 和 Simulink 旁边扮演辅助角色，但不会取代它们。

话虽如此，一旦你精通了 Python，你会发现对 MATLAB 的需求会减少。这与其说是对 MATLAB 的批评，不如说是对 Python 的认可。Python 作为一种通用语言，在许多 MATLAB 不太擅长的领域表现卓越。让 Python 来处理那些“唾手可得”的任务，比如从一组 `NetCDF4` 文件中聚合数据、扫描目录查找包含名为“2017 inventory”选项卡的 Excel 文件、以及将纯文本数据重新格式化为一组 JSON 记录。

奇怪的是，对于最庞大、计算量最大的任务，Python 也可能会是更好的选择。与 MATLAB 不同，Python 的并行处理模块（如 `Dask`）不受许可证限制，因此你可以在尽可能多的处理器上运行。

同时掌握 Python 和 MATLAB 可以扩展你解决问题的选择范围。无论这意味着仅用 Python 还是用 MATLAB/Python 混合方案来解决新问题，你所能调用的能力都将远超仅使用 MATLAB。

## 1.5 内容概览

本书旨在实现多个目标：一本 Python 教程；一本 MATLAB/Python 等效表达式、函数和程序的集合；一本用 Python 解决计算问题的参考手册；以及一本 MATLAB 调用 Python 的示例手册。你应该从哪里开始呢？

所有读者，即使是 Python 专家，也应该从第 2 章“安装”开始。它描述了如何创建运行本书示例所需的、对 MATLAB 友好的虚拟环境。

不熟悉 Python 的 MATLAB 开发者可以通过阅读并实践第 3 章和第 4 章“语言基础”和“数据容器”中的示例来快速上手。一旦你对 Python 的基础知识感到熟悉，就可以跳到你感兴趣的部分。

第 5 章“日期和时间”涵盖了测量、读取、写入和偏移日期与时间对象的细节。

第 6 章“从 MATLAB 调用 Python 函数”是本书的核心开始部分。它涵盖了 MATLAB 的 `py` 模块的基础知识，展示了如何在 MATLAB 内部扩展 Python 搜索路径，描述了 MATLAB 中 Python 原生变量的细微差别以及如何将它们转换为 MATLAB 原生变量，并展示了两种语言交互的其他机制。

第 7 章“输入与输出”涵盖了数值计算中常见的文本和二进制文件的读写：纯文本、JSON、YAML、XML、CSV、Excel、原始二进制、HDF5、netCDF。关于网络 I/O 的部分展示了如何在 Python 和 MATLAB 中编写 TCP/IP 客户端和服务器。本章中首次出现了简单的 MATLAB 调用 Python 的示例（让 MATLAB 读取 YAML 和 `.ini` 文件）。

除了读写文件，程序有时还需要读取目录内容、搜索文件、获取文件元数据（如大小和修改时间）以及删除目录。这些操作将在第 8 章“与文件系统交互”中介绍。

Python 和 MATLAB 偶尔需要运行外部可执行程序、检查环境变量以及监控计算机负载。第 9 章“与操作系统和外部可执行程序交互”展示了如何在两种语言中完成这些操作。

第 10 章“面向对象编程”展示了如何在 MATLAB 和 Python 中定义和实例化类。

第 11 章“NumPy 和 SciPy”占据了本书近四分之一的篇幅。它涵盖了 NumPy 的 `ndarray`（最接近 MATLAB 矩阵的 Python 对象），以及许多执行数组操作、线性代数和插值的函数。`SciPy` 是一个基于 NumPy 构建的科学计算包，提供了额外的功能，如稀疏矩阵、优化、曲线拟合和微分方程求解器。

数据可视化包含在第 12 章“绘图”中。除了点图、线图和等高线图的基础知识外，还描述了如何在地图上叠加数据。

Python 的 `Pandas` 模块在 MathWorks 将表格添加到 MATLAB 之前的五年就已出现。第 13 章“表格和数据框架”展示了 Pandas 数据框架的基础知识以及如何使用 MATLAB 表格执行等效操作。

第 14 章“高性能计算”探讨了让 Python 运行得更快的方法——如果你愿意用 Python 实现缓慢的 MATLAB 函数，更快的 Python 可能意味着更快的 MATLAB 程序。本章给出了使用 Cython、Pythran、f2py 和 Numba 在单台计算机上加速 Python 运行的例子，以及使用 `dask` 在集群上运行 Python 的例子。你会看到，无论 Python 程序在哪里被加速，只要让 MATLAB 调用更快的 Python 函数，类似的 MATLAB 程序也能得到同样程度的加速。

MATLAB 和 Python 都有一些尖锐的边角，可能给新开发者带来困扰。第 15 章“语言陷阱”涵盖了这两种语言需要注意的方面。

附录 A 列出了本书中所有 MATLAB 调用 Python 的示例，而附录 B-E 则包含了因篇幅过长而无法放入正文的源代码列表。本书的所有源代码都可以在本书的 Github 仓库中找到：[`https://github.com/Apress/python-for-matlab-development`](https://github.com/Apress/python-for-matlab-development)。

## 1.6 我已经会 Python 了。如何在 MATLAB 中调用 Python 函数？

首先，按照第 2.​5.​1 节所述，创建一个对 MATLAB 友好的虚拟环境 `matpy`。然后直接跳到第 6 章“从 MATLAB 调用 Python 函数”。有关 MATLAB 调用 Python 的示例列表，请参考附录 A；这些示例可以帮助指导你自己的代码开发。


