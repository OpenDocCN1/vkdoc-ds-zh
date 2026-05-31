# NumPy 入门指南

+   [NumPy 入门指南](README.md)

+   [引言](numerical-python_01.md)

+   [本书的组织方式](numerical-python_02.md)

+   [1. Python 计算入门](numerical-python_03.md)

+   [文档](numerical-python_04.md)

+   [2. 向量、矩阵和多维数组](numerical-python_05.md)

+   [3. 符号计算](numerical-python_06.md)

+   [然而，矩阵求逆比执行 LU 分解更困难，因此如果目标是求解方程 `Mx = b`（如本例所示），使用 LU 分解效率更高。这一点在方程组规模较大时尤为明显。使用本文讨论的两种方法，我们都能获得一个解的符号表达式，对于任意参数值都很容易求值，而无需重新计算。这正是符号计算的优势所在，也是它有时能超越直接数值计算的一个实例。当然，本例中的问题也可以轻松手动求解，但随着方程数量和未指定参数的增加，手动解析处理很快就变得过于冗长且繁琐。借助 SymPy 这样的计算机代数系统，我们可以拓展能用解析方法处理的问题的边界。](numerical-python_07.md)

+   [4. 绘图与可视化](numerical-python_08.md)

+   [5. 方程求解](numerical-python_09.md)

+   [非线性方程组](numerical-python_10.md)

+   [6. 优化](numerical-python_11.md)

+   [排版后](numerical-python_12.md)

+   [7. 插值](numerical-python_13.md)

+   [8. 积分](numerical-python_14.md)

+   [9. 常微分方程](numerical-python_15.md)

+   [通过 SymPy 进行拉普拉斯变换求解常微分方程](numerical-python_16.md)

+   [10. 稀疏矩阵与图](numerical-python_17.md)

+   [11. 偏微分方程](numerical-python_18.md)

+   [12. 数据处理与分析](numerical-python_19.md)

+   [13. 统计学](numerical-python_20.md)

+   [14. 统计建模](numerical-python_21.md)

+   [Markdown 排版结果](numerical-python_22.md)

+   [15. 机器学习](numerical-python_23.md)

+   [16. 贝叶斯统计](numerical-python_24.md)

+   [17. 信号处理](numerical-python_25.md)

+   [请注意，`DFT` 以离散样本作为输入，并输出离散频谱。为了将 `DFT` 应用于原本连续的信号，我们必须首先通过采样将信号缩减为离散值。根据采样定理，带宽为 `B` 的连续信号（即信号不包含高于 `B` 的频率），可以从采样频率为 `f[s] >= 2B` 的离散样本中完全重构。这是信号处理中一个非常重要的结论，因为它告诉我们，在何种情况下可以使用离散信号代替连续信号。它允许我们在测量连续过程时确定合适的采样率，因为通常可以通过物理因素等来了解或大致估计一个过程的带宽。虽然采样率决定了我们可以用离散傅里叶变换描述的最大频率，但频率空间中的样本间隔由总采样时间 `T` 决定，或者等效地，在采样频率确定后，由样本点数决定，即 `T = N/f[s]`。](numerical-python_26.md)

+   [18. 数据输入与输出](numerical-python_27.md)

+   [19. 代码优化](numerical-python_28.md)