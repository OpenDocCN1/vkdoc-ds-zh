# 9.2 C²优问题

`OptimProblem`类有些难以设置，并且在大多数情况下使用起来不方便。C⁰函数是连续函数。C¹函数是光滑函数。C²函数是连续、光滑且具有二阶导数的函数。它也被称为二次可微函数。如果你要优化这样的函数，`C2OptimProblemImpl`提供了一个便捷的包装器来创建`OptimProblem`对象。其签名如下：

```java
/**
* Construct an optimization problem with an objective function.
* This uses a numerical gradient and a numerical Hessian, if needed.
*
* @param f the objective function to be minimized
*/
public C2OptimProblemImpl(RealScalarFunction f)
```

你可以使用简化的代码重写之前的示例。

对于单变量函数 `f(x) = x² − 4`，代码如下：

```kotlin
// set up the solver to use and the solution
val solver = DoubleBruteForceMinimizer(false)
val soln = solver.solve(C2OptimProblemImpl(Polynomial(1.0, 0.0, -4.0))) // f(x) = x² - 4
// for brute force search, we need to explicitly enumerate the values in the domain
val domain = mutableListOf(
    DenseVector(-2.0),
    DenseVector(-1.0),
    DenseVector(0.0), // the minimizer
    DenseVector(1.0),
    DenseVector(2.0)
)
soln.setDomain(domain)
println(String.format("f(%s) = %f", soln.minimizer(), soln.min()))
```

输出如下：

```
f([0.000000] ) = -4.000000
```

对于多变量函数 `f(x, y) = x² + y²`，代码如下：

```kotlin
val bf = DoubleBruteForceMinimizer(true)
val soln = bf.solve(
    C2OptimProblemImpl(
        object : AbstractBivariateRealFunction() {
            override fun evaluate(x: Double, y: Double): Double {
                val fx: Double  = x * x + y * y
                return fx
            }
        }))
val domain = mutableListOf(
    DenseVector(-2.0, -2.0),
    DenseVector(-1.0, -1.0),
    DenseVector(0.0, 0.0), // the minimizer
    DenseVector(1.0, 1.0),
    DenseVector(2.0, 2.0)
)
soln.setDomain(domain)
println(String.format("f(%s) = %f", soln.minimizer(), soln.min()))
```

输出如下：

```
f([0.000000, 0.000000] ) = 0.000000
```