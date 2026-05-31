# 9.1 暴力搜索

最简单且可能最容易理解的求解器是暴力搜索。即，你只需遍历函数定义域中所有可能的取值 *x*，枚举 *f*(*x*) 的所有可能值。然后选择对应最小 *f*(*x*) 的那个 *x*。可能存在多个解。然而，这种方法很少使用，因为它并不实用。一个要求是定义域必须是可枚举的。大多数实际优化问题都是在连续的定义域（如实数集）上求解的。你无法枚举无穷多个数值。规避这个问题的一种方法是离散化。你从无穷的定义域中选择有限个点。例如，你可以将单位区间 [0, 1] 离散化为 11 个离散值：0、0.1、0.2、……、1.0。离散化最明显的缺点是，当所选的点不包含最优解时，你很可能会得到一个次优解。事实上，如果定义域是无界的 (−∞, +∞)，则如何选择点来包含一个解并不明显。此外，除了极小的极简问题外，其性能都非常慢。假设你要对十个变量进行求解；那么问题的维度是 *n*¹⁰，其中 *n* 是每个维度选择的离散化点的数量。问题的复杂度呈指数级增长。尽管如此，暴力搜索很容易理解。因此我们首先从它开始介绍。

以下 NM Dev 代码大致演示了如何使用该库解决一个优化问题。假设你想要最小化函数 *f*(*x*) = *x*² – 4。

```
// 使用目标函数定义优化问题
val problem: OptimProblem = object : OptimProblem {
    override fun dimension(): Int {
        return 1
    }
    override fun f(): RealScalarFunction {
        return object : RealScalarFunction {
            // 目标函数
            override fun evaluate(v: Vector): Double {
                val x: Double = v.get(1)
                val polynomial: Polynomial = Polynomial(1.0, 0.0, -4.0) // f(x) = x² – 4
                val fx: Double = polynomial.evaluate(x)
                return fx
            }
            override fun dimensionOfDomain(): Int {
                return 1
            }
            override fun dimensionOfRange(): Int {
                return 1
            }
        }
    }
}
// 设置要使用的求解器及解决方案
val solver: DoubleBruteForceMinimizer = DoubleBruteForceMinimizer(false)
val soln = solver.solve(problem)
// 对于暴力搜索，我们需要显式枚举定义域中的值
val domain = mutableListOf(
    DenseVector(-2.0),
    DenseVector(-1.0),
    DenseVector(0.0), // 最小化点
    DenseVector(1.0),
    DenseVector(2.0)
)
soln.setDomain(domain)
println(String.format("f(%s) = %f", soln.minimizer(), soln.min()))
```

输出结果如下：

```
f([0.000000] ) = -4.000000
```

在 NM Dev 中使用求解器解决优化问题需要遵循以下步骤：

1. 通过实现 `OptimProblem` 接口来定义一个优化问题。你需要在实现中给出目标函数。

2. 构建要使用的求解器，例如 `DoubleBruteForceMinimizer`。

3. 调用 `solve` 函数，利用优化问题从求解器构造一个 `Solution` 对象。换句话说，求解器对象是一个工厂，它为优化问题（称为 `OptimProblem`）创建一个称为 `Solution` 的解。求解器可以复用于其他问题，但一个解仅对应一个问题。

4. 向 `Solution` 对象提供任何与问题相关的信息。在此例中，对于暴力搜索，你需要列出待搜索的可能值，即定义域。

5. 调用 `minimizer` 函数来运行算法以计算最小化点 *x*^∗，或调用 `min` 函数返回函数 *f*(*x*^∗) 的最小值。

上述流程适用于 NM Dev 库中的所有求解器。所有 NM 求解器都是用于最小化的。要进行最大化，你只需将目标函数乘以 -1 取反即可。通常，`OptimProblem`、求解器和 `Solution` 的签名如下所示：

```
public interface OptimProblem {
    /**
     * 获取变量数量。
     *
     * @return 变量数量。
     */
    public int dimension();
    /**
     * 获取目标函数。
     *
     * @return 目标函数
     */
    public RealScalarFunction f();
}
public interface MultivariateMinimizer> extends Minimizer {
    /**
     * 求解优化问题，例如 {@link OptimProblem}。
     *
     * @param problem 一个优化问题
     * @return 优化问题的解
     * @throws Exception 当求解问题出现错误时
     */
    public S solve(P problem) throws Exception;
}
public interface MinimizationSolution {
    /**
     * 获取（近似）找到的最小值。
     *
     * @return （近似）找到的最小值
     */
    public double minimum();
    /**
     * 获取最小化问题的最小化点（解）。
     *
     * @return 最小化点
     */
    public T minimizer();
}
```

`DoubleBruteForceMinimizer` 具有如下签名：

```
public DoubleBruteForceMinimizer(boolean isParallel);
@Override
public BruteForceMinimizer.Solution solve(OptimProblem problem) throws Exception
```

当 `isParallel` 标志设置为 `true` 时，代码会使用多线程并行运行搜索，在所有可用的线程上搜索所有提供的定义域值。

暴力搜索可用于解决单变量优化问题，如前面示例所示，也可用于解决多变量优化问题。以下是一个求解示例：

*f*(*x*, *y*) = *x*² + *y*²

```
val problem = object : OptimProblem {
    override fun dimension(): Int {
        return 2
    }
    override fun f(): RealScalarFunction {
        return object : RealScalarFunction {
            override fun evaluate(v: Vector): Double {
                val x: Double = v.get(1)
                val y: Double = v.get(2)
                val fx: Double = x * x + y * y
                return fx
            }
            override fun dimensionOfDomain(): Int {
                return 2
            }
            override fun dimensionOfRange(): Int {
                return 1
            }
        }
    }
}
val bf = DoubleBruteForceMinimizer(true)
val soln = bf.solve(problem)
val domain = mutableListOf(
    DenseVector(-2.0, -2.0),
    DenseVector(-1.0, -1.0),
    DenseVector(0.0, 0.0), // 最小化点
    DenseVector(1.0, 1.0),
    DenseVector(2.0, 2.0)
)
soln.setDomain(domain)
println(String.format("f(%s) = %f", soln.minimizer(), soln.min()))
```

输出结果如下：

```
f([0.000000, 0.000000] ) = 0.000000
```

正如这个示例所示，除非你神奇地知道解（在此例中是 (0, 0)）并将其放入定义域列表中，否则暴力搜索将无法找到该解。在这种情况下，如果你已经知道了答案，那就不需要优化器了。



