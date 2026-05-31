# 6. 数值微分与数值积分

数值微分与数值积分是一种技术，适用于以下情况：你无法先验地知道函数，将函数视为黑箱，或者无法对该函数进行解析（或符号化、闭式）的微分与积分。该技术通过使用许多线性函数来近似函数，每个线性函数对应函数定义域上的一个微小区间。事实上，函数的逐段线性近似正是微积分的基础。当区间足够小，或者当你将函数充分放大时，函数看起来就像一条直线（这就是微分）。当你将这些“直线”拼接起来，并累加其下方的块面积（通常是矩形或梯形）时，就得到了积分。当区间长度趋近于无穷小时，这个概念就达到了极限。然而，在计算机上，你无法让区间长度任意小。你必须满足于足够好的近似或足够小的区间，因此就有了数值微分与数值积分。

在图 6-1 中，箭头是每个区间内函数的线性近似。它们看起来足够接近，可以表示函数在各区间内的斜率或导数。每个箭头下方的块面积也可以轻松计算（它们基本上是梯形）。你可以将它们相加得到函数的（定）积分。你拥有的子区间越多，数值导数和数值积分的精度就越高。

![](img/528380_1_En_6_Fig1_HTML.jpg)

一张 `f` 关于 `x` 的曲线图。曲线先急剧下降，然后急剧上升，形成一条下垂曲线。从曲线上有 12 条线段投影到 `X` 轴上。

图 6-1  
数值微分与数值积分示例

本章的源代码可以在 S2 平台上运行，访问地址如下：

[`s21.nm.dev/hub/user-redirect/lab/tree/s2-public/Numerical%20Methods%20Using%20Kotlin/chapter6Differentiation.ipynb`](https://s21.nm.dev/hub/user-redirect/lab/tree/s2-public/Numerical%2520Methods%2520Using%2520Kotlin/chapter6Differentiation.ipynb)

[`s21.nm.dev/hub/user-redirect/lab/tree/s2-public/Numerical%20Methods%20Using%20Kotlin/chapter6Integration.ipynb`](https://s21.nm.dev/hub/user-redirect/lab/tree/s2-public/Numerical%2520Methods%2520Using%2520Kotlin/chapter6Integration.ipynb)

源代码也可以在 GitHub 上找到：

[`github.com/apress/numerical-methods-kotlin`](https://github.com/apress/numerical-methods-kotlin)

## 6.1 数值微分

数值微分以数值方式估算函数在某一点的导数。通常，使用差商来近似导数。构造这种差商的方法有很多种。泰勒展开可用于量化每种近似的误差项。此外，可以使用外推法来提高精度，将差商外推到分母无穷小的情形。本章还涵盖了一些在数学其他分支（如优化理论）中重要的导数。它们包括梯度向量、雅可比矩阵和海森矩阵。`NM Dev` 实现了一些特殊函数的一阶导数。由于精度问题，这些函数的导数无法使用通用方法进行数值计算。它们通常被写成无穷级数或连分数的形式。由于这些特殊函数的重要性和基础性，需要为它们提供专门的实现。

## 6.2 有限差分

有限差分是（可能）最流行的数值导数计算方法。它是数值求解许多常微分方程和偏微分方程的基础。该方法将连续的函数定义域离散化为有限数量的节点，从而形成一个网格。对于一维问题，即计算单变量函数的导数，它会沿着 `x` 轴选取一组点。对于二维问题，即计算双变量函数的偏导数，它会在平面上构建一个节点网格或网格。参见图 6-2。

![](img/528380_1_En_6_Fig2_HTML.png)

一张 `y` 关于 `x` 的曲线图，显示了一个由 11 行 7 列等间距节点组成的网格图案。

图 6-2  
为二维函数域选择的等距节点网格

在已知网格点上所有函数值的情况下，泰勒展开给出了你想要计算的导数的表达式，该表达式呈现为一个（如显式方法）或一套（如隐式方法，如克兰克-尼科尔森方法）代数方程的形式，并附带一个误差项。有限差分是一种近似的数值解法，它将微分问题转化为代数问题。这个数学概念很直观，表达式也很简单。

使用泰勒展开构造有限差分的方法有很多。基本类型包括一阶前向差分、一阶后向差分、一阶中心差分、二阶中心差分等。还有显式方法和隐式方法：显式方法只需代入数值即可得到结果；隐式方法则需要求解一个代数方程组才能得到结果。不同有限差分的误差可以是基于网格尺寸的一阶、二阶或更高阶。

单变量函数的一阶导数是最简单且最容易解释的。它表示函数在某一点的斜率。两点之间的斜率是这两个点之间函数值的变化量 `Δf` 除以这两个点之间的差值 `Δx` 所得的商 `Δf/Δx`。连接这两点的线被称为函数在这两点之间的*割线*（图 6-3 中的红线）。当其中一个点向另一个点移动时，割线也随之移动。当一个点完全与另一个点重合时（即两点变成同一个点），割线就变成了函数在该点的切线（图 6-3 中的黑线）。该切线的斜率被定义为函数在该接触点处的斜率，因此也就是函数在该点的一阶导数。换句话说，一阶导数就是当步长 `Δx` 趋近于无穷小时的那个差商。当 `Δx` 足够小时，这个商就是对一阶导数 `df/dx` 的一个良好近似。根据你如何选取这两个点，你会得到不同的有限差分。

![](img/528380_1_En_6_Fig3_HTML.jpg)

一张曲线图，展示了一条具有水平切线的上拱曲线，以及中心投影线之后的一条倾斜直线，用以说明前向差分。

图 6-3  
前向差分



### 6.2.1 前向差分

假设你要计算函数 `f` 在点 `x[0]` 处的导数。考虑点 (`x[0]`, `f(x[0])`) 和 (`x[0] + h`, `f(x[0] + h)`) 之间的线性插值。这两点之间割线的斜率通过前向（两点）差分来近似导数。

![$$ {f}^{\prime}\left({x}_0\right)\approx \frac{f\left({x}_0+h\right)-f\left({x}_0\right)}{h} $$](img/528380_1_En_6_Chapter_TeX_Equa.png)

你可以通过泰勒展开来理解这个想法，如下所示：

![$$ f\left({x}_0+h\right)=f\left({x}_0\right)+{hf}^{\prime}\left({x}_0\right)+\frac{h²}{2!}{f}^{\prime \prime}\left(\xi \right),{x}_0\le \xi \le {x}_0+h $$](img/528380_1_En_6_Chapter_TeX_Equb.png)

![$$ {f}^{\prime}\left({x}_0\right)=\frac{f\left({x}_0+h\right)-f\left({x}_0\right)}{h}-\frac{h}{2!}{f}^{\prime \prime}\left(\xi \right),{x}_0\le \xi \le {x}_0+h $$](img/528380_1_En_6_Chapter_TeX_Equc.png)

![$$ \approx \frac{f\left({x}_0+h\right)-f\left({x}_0\right)}{h} $$](img/528380_1_En_6_Chapter_TeX_Equd.png)

误差项如下：

![$$ R(x)=\left\Vert {f}^{\prime}\left({x}_0\right)-\frac{f\left({x}_0+h\right)-f\left({x}_0\right)}{h}\right\Vert =\frac{h}{2!}{f}^{\prime \prime}\left(\xi \right)=\textrm{O}(h) $$](img/528380_1_En_6_Chapter_TeX_Eque.png)

这里的大 O 符号表示误差项总是小于（有界于）一个常数乘以 `h`，这个常数称为 "`h` 的大 O"。换句话说，误差趋于零的速度快于一个常数乘以 `h`。`h` 越小，误差就越小。它们都以线性速率趋于零。误差项是一阶的。在使用单步预测-校正法求解常微分方程时，前向差分非常有用。

### 6.2.2 后向差分

考虑点 (`x[0] − h`, `f(x[0] − h)` ) 和 (`x[0]`, `f(x[0])` ) 之间的另一个线性插值。这两点之间割线的斜率近似导数，由后向（两点）差分给出。参见图 6-4。

![$$ {f}^{\prime}\left({x}_0\right)\approx \frac{f\left({x}_0\right)-f\left({x}_0-h\right)}{h} $$](img/528380_1_En_6_Chapter_TeX_Equf.png)

![](img/528380_1_En_6_Fig4_HTML.jpg)

一张图展示了一条具有水平切线的上凸曲线和一条在中心投影线之前的倾斜线，用以说明后向差分。

**图 6-4**

**后向差分**

根据泰勒展开，你可以得到：

![$$ f\left({x}_0-h\right)=f\left({x}_0\right)-{hf}^{\prime}\left({x}_0\right)+\frac{h²}{2!}{f}^{\prime \prime}\left(\xi \right),{x}_0-h\le \xi \le {x}_0 $$](img/528380_1_En_6_Chapter_TeX_Equg.png)

![$$ {f}^{\prime}\left({x}_0\right)=\frac{f\left({x}_0\right)-f\left({x}_0-h\right)}{h}+\frac{h}{2!}{f}^{\prime \prime}\left(\xi \right),{x}_0-h\le \xi \le {x}_0 $$](img/528380_1_En_6_Chapter_TeX_Equh.png)

其误差项与前向差分相同。

![$$ R(x)=\left\Vert {f}^{\prime}\left({x}_0\right)-\frac{f\left({x}_0\right)-f\left({x}_0-h\right)}{h}\right\Vert =\frac{h}{2!}{f}^{\prime \prime}\left(\xi \right)=\textrm{O}(h) $$](img/528380_1_En_6_Chapter_TeX_Equi.png)

当未来的数据尚不可用时，后向差分非常有用。在某些控制问题中，未来的数据可能依赖于根据历史数据估计的导数。

### 6.2.3 中心差分

考虑点 (`x[0] − h`, `f(x[0] − h)`) 和 (`x[0] + h`, `f(x[0] + h)`) 之间的另一个线性插值。这两点之间割线的斜率通过中心（三点）差分来近似导数。

![$$ {f}^{\prime}\left({x}_0\right)\approx \frac{f\left({x}_0+h\right)-f\left({x}_0-h\right)}{2h} $$](img/528380_1_En_6_Chapter_TeX_Equj.png)

参见图 6-5。

![](img/528380_1_En_6_Fig5_HTML.png)

一张图展示了一条具有水平切线的上凸曲线和一条穿过中心投影线的倾斜线，用以说明中心差分。

**图 6-5**

**中心差分**

根据泰勒展开，你可以得到：

![$$ f\left({x}_0+h\right)=f\left({x}_0\right)+{hf}^{\prime}\left({x}_0\right)+\frac{h²}{2!}{f}^{\prime \prime}\left(\xi \right)+\frac{h³}{3!}{f}^{\prime \prime \prime}\left({\xi}_1\right),{x}_0\le {\xi}_1\le {x}_0+h $$](img/528380_1_En_6_Chapter_TeX_Equk.png)

![$$ f\left({x}_0-h\right)=f\left({x}_0\right)-{hf}^{\prime}\left({x}_0\right)+\frac{h²}{2!}{f}^{\prime \prime}\left(\xi \right)-\frac{h³}{3!}{f}^{\prime \prime \prime}\left({\xi}_2\right),{x}_0-h\le {\xi}_2\le {x}_0 $$](img/528380_1_En_6_Chapter_TeX_Equl.png)

将这两个方程相减，你得到：

![$$ {f}^{\prime}\left({x}_0\right)=\frac{f\left({x}_0+h\right)-f\left({x}_0-h\right)}{2h}-\frac{h²}{3!}{f}^{\prime \prime \prime}\left({\xi}_1\right),{x}_0\le {\xi}_1\le {x}_0+h $$](img/528380_1_En_6_Chapter_TeX_Equm.png)

误差项如下：

![$$ R(x)=\left\Vert {f}^{\prime}\left({x}_0\right)-\frac{f\left({x}_0+h\right)-f\left({x}_0-h\right)}{2h}\right\Vert $$](img/528380_1_En_6_Chapter_TeX_Equn.png)

![$$ =\frac{h²}{3!}{f}^{\prime \prime \prime}\left(\xi \right)=\textrm{O}\left({h}²\right) $$](img/528380_1_En_6_Chapter_TeX_Equo.png)

中心差分的误差是 `h`²的 大 O。这意味着误差以二次速率（快于线性速率）趋于零。这个误差项比前向和后向差分都要小。中心差分比前向和后向差分更精确，因此，如果过去和未来的数据值都可获得，则更倾向于使用中心差分。

对于之前的例子，你想计算以下函数在 `x = 2` 处的一阶导数：

![$$ f(x)=-\left({x}²-4x+6\right) $$](img/528380_1_En_6_Chapter_TeX_Equp.png)

前向差分给出负结果，因为割线是向下倾斜的；后向差分给出正结果，因为割线是向上倾斜的；而中心差分给出 `df/dx = 0`，这是正确答案。

如果数据值是等间距的，那么中心差分就是前向差分和后向差分的平均值。中心差分常用于求解偏微分方程。



### 6.2.4 高阶导数

如果函数 `f(x)` 的一阶导函数 `f'(x)` 也是可微的，那么 `f'(x)` 的导数就称为函数 `f(x)` 的二阶导数。即：

![$$ {f}^{\prime \prime }(x)={\left({f}^{\prime }(x)\kern0.5em \right)}^{\prime } $$](img/528380_1_En_6_Chapter_TeX_Equq.png)

通过归纳，如果 `f(x)` 的 `(n-1)` 阶导数存在且可微，则定义其 `n` 阶导数如下：

![$$ {f}^{(n)}(x)={\left({f}^{\left(n-1\right)}(x)\kern0.5em \right)}^{\prime } $$](img/528380_1_En_6_Chapter_TeX_Equr.png)

如果函数 `f` 是连续的，则称 `f ∈ C⁰`。如果 `f'` 存在且连续，则称 `f` 是光滑函数，并且 `f ∈ C¹`。如果 `f` 可以无限次微分（因此必然连续），则称 `f ∈ C^∞`。`f(x) = e^(x)` 属于 `C^∞`。

下面列出了一些高阶导数的有限差分公式。

二阶导数前向差分：

![$$ {f}^{\prime \prime }(x)=\frac{f\left(x+2h\right)-2f\left(x+h\right)+f(x)}{h²}+O\left({h}²\right) $$](img/528380_1_En_6_Chapter_TeX_Equs.png)

二阶导数后向差分：

![$$ {f}^{\prime \prime }(x)=\frac{f(x)-2f\left(x-h\right)+f\left(x-2h\right)}{h²}+O\left({h}²\right) $$](img/528380_1_En_6_Chapter_TeX_Equu.png)

二阶导数中心差分：

![$$ {f}^{\prime \prime }(x)=\frac{f\left(x+h\right)-2f(x)+f\left(x-h\right)}{h²}+O\left({h}²\right) $$](img/528380_1_En_6_Chapter_TeX_Equv.png)

三阶导数前向差分：

![$$ {f}^{\prime \prime \prime }(x)=\frac{f\left(x+3h\right)-3f\left(x+2h\right)+3f\left(x+h\right)-f(x)}{h³}+O\left({h}³\right) $$](img/528380_1_En_6_Chapter_TeX_Equw.png)

三阶导数后向差分：

![$$ {f}^{\prime \prime \prime }(x)=\frac{f(x)-3f\left(x-h\right)+3f\left(x-2h\right)-f\left(x-3h\right)}{h³}+O\left({h}³\right) $$](img/528380_1_En_6_Chapter_TeX_Equx.png)

三阶导数中心差分：

![$$ {f}^{\prime \prime \prime }(x)=\frac{f\left(x+2h\right)-2f\left(x+h\right)+2f\left(x-h\right)-f\left(x-2h\right)}{2{h}³}+O\left({h}³\right) $$](img/528380_1_En_6_Chapter_TeX_Equy.png)

一般地，可以得到如下公式。

`n` 阶导数前向差分：

![$$ {\Delta}_h^n\leftf\right=\sum \limits_{i=0}^n{\left(-1\right)}^{n-i}\left(\begin{array}{c}n\\ {}i\end{array}\right)f\left(x+ ih\right) $$](img/528380_1_En_6_Chapter_TeX_Equz.png)

`n` 阶导数后向差分：

![$$ {\nabla}_h^n\leftf\right=\sum \limits_{i=0}^n{\left(-1\right)}^{n-i}\left(\begin{array}{c}n\\ {}i\end{array}\right)f\left(x- ih\right) $$](img/528380_1_En_6_Chapter_TeX_Equaa.png)

`n` 阶导数中心差分：

![$$ {\delta}_h^n\leftf\right=\sum \limits_{i=0}^n{\left(-1\right)}^{n-i}\left(\begin{array}{c}n\\ {}i\end{array}\right)f\left(x+\left(\frac{n}{2}-i\right)h\right) $$](img/528380_1_En_6_Chapter_TeX_Equab.png)

`(n choose i)` 是二项式系数。杨辉三角的每一行提供了每个 `i` 值对应的系数。

到目前为止提供的公式使用两个点来计算二阶导数，使用三个点来计算三阶导数。还存在使用三个点计算二阶导数、四个点计算三阶导数的公式。一般而言，利用线性代数，你可以构建有限差分近似，该近似对于任意阶导数，都可以在评估点的左侧使用任意数量的点，并在右侧使用（可能不同）数量的点。这涉及求解一个线性系统，使得这些点在评估点附近的泰勒展开最能逼近所需导数的泰勒展开。这在网格上对函数进行微分时非常有用，因为当你接近网格边缘时，你必须在某一侧采样越来越少的点。

在 NM Dev 中，`FiniteDifference` 类用于计算给定函数的指定阶导数函数。以下使用 NM Dev 的代码片段计算了前面例子的一阶导数：

```kotlin
val f: UnivariateRealFunction = object : AbstractUnivariateRealFunction() {
override fun evaluate(x: Double): Double {
return -(x * x - 4 * x + 6) // -(x² - 4x + 6)
}
}
val x: Double = 2.0
val df1_forward: UnivariateRealFunction
= FiniteDifference(f, 1, FiniteDifference.Type.FORWARD)
var dfdx: Double = df1_forward.evaluate(x) // evaluate at x
println(String.format("df/dx(x=%f) = %.16f using forward difference", x, dfdx))
val df1_backward: UnivariateRealFunction
= FiniteDifference(f, 1, FiniteDifference.Type.BACKWARD)
dfdx = df1_backward.evaluate(x) // evaluate at x
println(String.format("df/dx(x=%f) = %.16f using backward difference", x, dfdx))
val df1_central: UnivariateRealFunction
= FiniteDifference(f, 1, FiniteDifference.Type.CENTRAL)
dfdx = df1_central.evaluate(x) // evaluate at x
println(String.format("df/dx(x=%f) = %.16f using central difference", x, dfdx))
```

解析地，一阶导函数如下：

![$$ \frac{dy}{dx}=-2x+4 $$](img/528380_1_En_6_Chapter_TeX_Equac.png)

输出结果如下：

```text
df/dx(x=2.000000) = -0.0000000298023224 using forward difference
df/dx(x=2.000000) = 0.0000000298023224 using backward difference
df/dx(x=2.000000) = 0.0000000000000000 using central difference
```

请注意，前向差分结果略微为负，因为割线是向下倾斜的；后向差分结果略微为正，因为割线也是向下倾斜的。中心差分给出了正确答案，即 0。

解析地，二阶导函数如下：

![$$ \frac{d²y}{dx²}=-2 $$](img/528380_1_En_6_Chapter_TeX_Equad.png)

以下代码片段计算了二阶导数：

```kotlin
val f: UnivariateRealFunction = object : AbstractUnivariateRealFunction() {
override fun evaluate(x: Double): Double {
return -(x * x - 4 * x + 6) // -(x² - 4x + 6)
}
}
val x: Double = 2.0
println("differentiate univariate functions")
val df1_forward: UnivariateRealFunction
= FiniteDifference(f, 2, FiniteDifference.Type.FORWARD)
var dfdx: Double = df1_forward.evaluate(x) // evaluate at x
println(String.format("d2f/dx2(x=%f) = %.16f using forward difference", x, dfdx))
val df1_backward: UnivariateRealFunction
= FiniteDifference(f, 2, FiniteDifference.Type.BACKWARD)
dfdx = df1_backward.evaluate(x) // evaluate at x
println(String.format("d2f/dx2(x=%f) = %.16f using backward difference", x, dfdx))
val df1_central: UnivariateRealFunction
= FiniteDifference(f, 2, FiniteDifference.Type.CENTRAL)
dfdx = df1_central.evaluate(x) // evaluate at x
println(String.format("d2f/d2x(x=%f) = %.16f using central difference", x, dfdx))
```

输出结果如下：

```text
d2f/dx2(x=2.000000) = -2.0000015520433827 using forward difference
d2f/dx2(x=2.000000) = -1.9999985243161564 using backward difference
d2f/d2x(x=2.000000) = -1.9999985243161564 using central difference
```



`FiniteDifference` 数值计算给定函数指定阶数的导函数。它本身是一个函数，可用于计算任意点的导数值。`FiniteDifference` 的签名如下：

```
/**
* Constructs an approximate derivative function for f using finite
* difference.
*
* @param f     a univariate function
* @param order the order of the derivative
* @param type  the type of finite difference to use, c.f., {@link Type}
*/
public FiniteDifference(UnivariateRealFunction f, int order, Type type)
@Override
public double evaluate(double x)
/**
* Evaluates numerically the derivative of f at point x,
* f'(x), with step size h.
* It could be challenging to automatically determine the step size
* h, esp. when |x| is near 0.
* It may, for example, require an analysis that involves f' and
* f''.
* The user may want to experiment with different hs by calling this
* function.
*
* @param x the point to evaluate the derivative of f at
* @param h step size
* @return f'(x), the numerical derivative of f at point
* x with step size h
*/
public double evaluate(double x, double h)
```

支持的类型如下：

*   `FiniteDifference.FORWARD`
*   `FiniteDifference.BACKWARD`
*   `FiniteDifference.CENTRAL`

在计算机上使用浮点运算计算导函数时，一个重要的实践考量是步长 *h* 的选择。若 *h* 过小，减法运算会产生较大的舍入误差。计算机中只有有限数量的实数可表示。两个非常接近但不同的实数会因有限的二进制表示而表示为相同的浮点数。因此，所有有限差分公式都是病态的，当 *h* 足够小时，由于抵消效应，结果将为 0。另一方面，若 *h* 过大，割线与切线将不匹配，导致近似值偏差较大。对于基本的中心差分法，最优步长是机器精度 *ε* 的立方根。对于在 *x* 和 *x* + *h* 处求值的数值导数公式，一个既小又不会产生较大舍入误差的 *h* 选择是 ![$$ \sqrt{\upepsilon}x,x\ne 0 $$](img/528380_1_En_6_Chapter_TeX_IEq5.png)。对于双精度浮点数，机器精度 *ε* 通常约为 1e-16。如果你调用第一个 `evaluate` 函数，NM Dev 会为你自动选择 *h*。或者，如果你调用第二个 `evaluate` 函数，你可以自己实现选择 *h* 的逻辑。

一般来说，对于有限差分法，计算导数时使用的点数越多，结果越精确。同时，导数的阶数越高，精度越低。例如，从之前的代码示例可以看出，近似二阶导数比近似一阶导数的精度低。

## 多元有限差分

多元函数的偏导数是指函数关于其中一个变量的导数，同时保持其他变量为常数。例如，二元函数关于 *x* 的偏导数表示为：

![$$ {D}_xf={f}_x\left(x,y\right)=\frac{\partial f\left(x,y\right)}{\partial x} $$](img/528380_1_En_6_Chapter_TeX_Equad.png)

与第 6.2 节中一元函数的求法类似，这些偏导数可以使用有限差分法进行数值计算，区别在于你需要在平面或更高维度的域上（而非数轴上）施加一个网格。

关于 *x* 的二阶偏导数表示为：

![$$ {D}_{xx}f={f}_{xx}\left(x,y\right)=\frac{\partial²f\left(x,y\right)}{\partial {x}²} $$](img/528380_1_En_6_Chapter_TeX_Equae.png)

二阶混合偏导数是指先对一个变量求导，再对另一个变量求导。例如，先对 *x* 求导再对 *y* 求导的二阶混合偏导数表示为：

![$$ {D}_{xy}f={f}_{xy}\left(x,y\right)=\frac{\partial }{\partial y}\frac{\partial f\left(x,y\right)}{\partial x} $$](img/528380_1_En_6_Chapter_TeX_Equaf.png)

高阶偏导数和混合偏导数的形式如下：

![$$ \frac{\partial^{i+j+k}f}{\partial {x}^i\partial {y}^j\partial {z}^k}={\partial}_x^i{\partial}_y^j{\partial}_z^kf $$](img/528380_1_En_6_Chapter_TeX_Equag.png)

NM Dev 通过在函数上递归应用一阶中心有限差分法来数值计算这些导数。该类为 `MultivariateFiniteDifference`，其签名如下：

```
/**
* Constructs the partial derivative of a multi-variable function.
* For example,
* varidx = new int[]{1, 2} means
* \[
* f_{x_1,x_2} = {\partial² \over \partial x_2 \partial x_1} = {\partial
* \over \partial x_2}({\partial \over \partial x_1})
* \]
* @param f      the real multivariate function to take derivative of
* @param varidx the variable indices of the derivative, counting from 1 up
*               to the domain dimension of f
*/
public MultivariateFiniteDifference(RealScalarFunction f, int[] varidx)
```

`varidx` 参数接受变量的索引，该类将按照该顺序对这些变量依次求导。

以下是一个示例：

![$$ z=f\left(x,y\right)={x}²+ xy+{y}² $$](img/528380_1_En_6_Chapter_TeX_Equah.png)

该函数的图形在欧几里得空间中定义了一个曲面。在该曲面的每一个点上，都存在无数条切线。偏微分就是选择其中一条线并求出其斜率。通常，最受关注的线是那些与 *xz* 平面平行（保持 *y* 为常数）以及与 *yz* 平面平行（保持 *x* 为常数）的线。参见图 6-6。

![](img/528380_1_En_6_Fig6_HTML.jpg)

**图 6-6**  
*z = x² + xy + y²* 的图形。对于在点 (1, 1) 处保持 *y* 为常数的偏导数，对应的切线平行于 *xz* 平面。

为了找到函数在点 (1, 1) 处平行于 *xz* 平面的切线斜率，你将 *y* 视为常数。通过在假设 *y* 为常数的条件下求方程的导数，你会发现函数 *f* 在点 (*x*, *y*) 处的斜率为：

![$$ \frac{\partial z}{\partial x}=2x+y $$](img/528380_1_En_6_Chapter_TeX_Equai.png)

参见图 6-7。

![](img/528380_1_En_6_Fig7_HTML.jpg)

**图 6-7**



上图是函数在 y = 1 的 xz 平面上的切片，注意两轴采用了不同比例尺。切线的斜率为 3

在点 (1, 1) 处计算结果为 3。以下代码片段解决了这个问题：

```
// f = x² + xy + y²
val f: RealScalarFunction = object : AbstractBivariateRealFunction() {
override fun evaluate(x: Double, y: Double): Double {
return x * x + x * y + y * y
}
}
// df/dx = 2x + y
val dx: MultivariateFiniteDifference
= MultivariateFiniteDifference(f, intArrayOf(1))
println(String.format("Dxy(1.0,1.0) %f", dx.evaluate(DenseVector(1.0, 1.0))))
```

输出结果如下：

```
Dxy(1.,1.) 3.000000
```

关于多元函数微分的另一个示例，请参考以下内容：

![$$ f\left(x,y,z\right)= xy+2 xyz $$](img/528380_1_En_6_Chapter_TeX_Equaj.png)

以下代码片段计算了这两个混合偏导数：

![$$ {D}_{xy}f=1+2z $$](img/528380_1_En_6_Chapter_TeX_Equak.png)

![$$ {D}_{yx}f=1+2z $$](img/528380_1_En_6_Chapter_TeX_Equal.png)

```
// f = xy + 2xyz
val f: RealScalarFunction = object : RealScalarFunction {
override fun evaluate(v: Vector): Double {
return v.get(1) * v.get(2) + 2 * v.get(1) * v.get(2) * v.get(3)
}
override fun dimensionOfDomain(): Int {
return 3
}
override fun dimensionOfRange(): Int {
return 1
}
}
val dxy: MultivariateFiniteDifference // 1 + 2z
// differentiate the first variable and then the second one
= MultivariateFiniteDifference(f, intArrayOf(1, 2))
println(String.format("Dxy(1.0,1.0,1.0) %f", dxy.evaluate(DenseVector(1.0, 1.0, 1.0))))
println(String.format("Dxy(-100.0,0.0,-1.5) %f", dxy.evaluate(DenseVector(-100.0, 0.0, -1.5))))
//continuous function allows switching the order of differentiation by Clairaut's theorem
val dyx: MultivariateFiniteDifference // 1 + 2z
// differentiate the second variable and then the first one
= MultivariateFiniteDifference(f, intArrayOf(2, 1))
println(String.format("Dyx(1.0,1.0,1.0) %f", dyx.evaluate(DenseVector(1.0, 1.0, 1.0))))
println(String.format("Dyx(-100.0,0.0,-1.5) %f", dyx.evaluate(DenseVector(-100.0, 0.0, -1.5))))
```

输出结果如下：

```
Dxy(1.,1.,1.) 3.000001
Dxy(-100.,0.,-1.5) -2.000000
Dyx(1.,1.,1.) 3.000001
Dyx(-100.,0.,-1.5) -2.000000
```

### 6.3.1 梯度

存在若干重要且广泛使用的混合偏导向量和矩阵，即梯度向量、雅可比矩阵和海森矩阵。

多元实值函数 *f* 的梯度函数 ∇*f* 是一个向量值函数（或向量场），从 ℝ^(*n*) 映射到 ℝ^(*n*)。它接收 ℝ^(*n*) 中的一个点 ***x*** = (*x*[1], ⋯, *x*[*n*])，并输出一个 ℝ^(*n*) 中的向量。任一点处的梯度 ∇*f*(***x***) 是一个向量，其分量是 *f* 在 ***x*** 处的偏导数，即：

![$$ \nabla f\left(\boldsymbol{x}\right)=\left[\begin{array}{c}\frac{\partial f}{\partial {x}_1}\left(\boldsymbol{x}\right)\\ {}\vdots \\ {}\frac{\partial f}{\partial {x}_n}\left(\boldsymbol{x}\right)\end{array}\right] $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equam.png)

每个分量 ![$$ \frac{\partial f}{\partial {x}_i}\left(\boldsymbol{x}\right) $$](img/528380_1_En_6_Chapter_TeX_IEq6.png) 是函数沿某一坐标轴的偏导数，表示该方向上的变化率。因此，梯度向量类似于一阶导数或斜率（对于单变量函数而言则是切线）。它可被解释为函数增长最快的方向及其变化率。如果函数在点 ***x*** 处的梯度非零，则梯度方向即为函数从 ***x*** 出发增长最快的方向，而梯度幅值就是该方向上的增长率，即最大的绝对方向导数。此外，当且仅当某点是驻点（导数消失的点）时，该点的梯度为零向量。因此梯度在优化理论中扮演着基础性角色。你将在第[9](http://dx.doi.org/10.1007/978-1-4842-6797-4_9)章看到，许多优化算法（如梯度下降法）都使用了梯度。

例如，考虑一个房间，其温度由标量场 *T* 表示，即该多元函数或场接收点 (*x*, *y*, *z*) 并输出温度值 *T*(*x*, *y*, *z*)。在房间的每一点处，*T* 的梯度指示了从该点出发温度上升最快的方向，而梯度幅值则决定了该方向上升的速度。更一般地，若多元函数 *f* 可微，则 ∇*f* 与单位向量 ***v*** 的点积就是函数沿 ***v*** 方向的斜率或变化率，称为 *f* 沿 ***v*** 的方向导数。泰勒展开的多元形式表明，函数的最佳线性近似可用梯度表达：

![$$ f\left(\boldsymbol{x}\right)\approx f\left({\boldsymbol{x}}_{\textbf{0}}\right)+\nabla f\left({\boldsymbol{x}}_{\textbf{0}}\right)\bullet \left(\boldsymbol{x}-{\boldsymbol{x}}_{\textbf{0}}\right) $$](img/528380_1_En_6_Chapter_TeX_Equan.png)

***x*** 与 ***x***[**0**] 是 ℝ^(*n*) 空间中的点。*f* 将 ℝ^(*n*) 映射到 ℝ，∇*f*: ℝ^(*n*) → ℝ^(*n*)。最后一项的点积结果为实数。

NM Dev 支持计算多元函数的梯度以及梯度函数，相关类为 `Gradient` 和 `GradientFunction`，其签名如下：

```
/**
* 构造多元函数 f 在点 x 处的梯度向量。
*
* @param f 一个多元函数
* @param x 计算 f 梯度的点
*/
public Gradient(RealScalarFunction f, Vector x)
/**
* 构造实标量函数 f 的梯度函数。
*
* @param f 一个实标量函数
*/
public GradientFunction(RealScalarFunction f)
```

例如，考虑以下函数：

![$$ f\left(x,y\right)=x\exp \left(-\left({x}²+{y}²\right)\right) $$](img/528380_1_En_6_Chapter_TeX_Equao.png)

参见图 6-8。



![](img/528380_1_En_6_Fig8_HTML.jpg)

一个图表展示了两个圆形图案，分别带有密集的输入和输出箭头。两个轴上的数值范围均从 -2 到 2。

**图 6-8** 二维函数 `f(x, y) = xe^(−(x² + y²))` 的梯度以蓝色箭头绘制在函数的伪彩色图上。

以下代码计算该函数的梯度及其梯度函数：

```
// f = x * exp(-(x² + y²))
val f: RealScalarFunction = object : AbstractBivariateRealFunction() {
override fun evaluate(x: Double, y: Double): Double {
return x * exp(-(x * x + y * y))
}
}
val x1: Vector = DenseVector(0.0, 0.0)
val g1_0: Vector = Gradient(f, x1)
println(String.format("\ngradient at %s = %s", x1, g1_0))
val df: GradientFunction = GradientFunction(f)
val g1_1: Vector = df.evaluate(x1)
println(String.format("gradient at %s = %s", x1, g1_1))
val x2: Vector = DenseVector(-1.0, 0.0)
val g2: Vector = df.evaluate(x2)
println(String.format("gradient at %s = %s", x2, g2))
val x3: Vector = DenseVector(1.0, 0.0)
val g3: Vector = df.evaluate(x3)
println(String.format("gradient at %s = %s", x3, g3))
```

输出结果如下：

```
gradient at [0.000000, 0.000000]  = [1.000000, 0.000000]
gradient at [0.000000, 0.000000]  = [1.000000, 0.000000]
gradient at [-1.000000, 0.000000]  = [-0.367879, 0.000000]
gradient at [1.000000, 0.000000]  = [-0.367879, 0.000000]
```

再考虑另一个函数，如下所示：

![$$ f\left(x,y\right)=-{\left({\cos}²x+{\cos}²y\right)}² $$](img/528380_1_En_6_Chapter_TeX_Equap.png)

参见图 6-9。

![](img/528380_1_En_6_Fig9_HTML.jpg)

一个三维图形展示了具有四个极小值的网格图案。中央凹陷触碰到 XY 平面的中心，并呈现不同的输出箭头。

**图 6-9** 函数 `f(x,y) = −(cos²x + cos²y)²` 的梯度在底部平面上以投影向量场的形式描绘。

以下代码计算该函数的梯度及其梯度函数：

```
// f = -((cos(x))² + (cos(y))²)²
val f: RealScalarFunction = object : AbstractBivariateRealFunction() {
override fun evaluate(x: Double, y: Double): Double {
var z: Double = cos(x) * cos(x)
z += cos(y) * cos(y)
z = -z * z
return z
}
}
val x1: Vector = DenseVector(0.0, 0.0)
val g1_0: Vector = Gradient(f, x1)
println(String.format("\ngradient at %s = %s", x1, g1_0))
val df: GradientFunction = GradientFunction(f)
val g1_1: Vector = df.evaluate(x1)
println(String.format("gradient at %s = %s", x1, g1_1))
val x2: Vector = DenseVector(-1.0, 0.0)
val g2: Vector = df.evaluate(x2)
println(String.format("gradient at %s = %s", x2, g2))
val x3: Vector = DenseVector(1.0, 0.0)
val g3: Vector = df.evaluate(x3)
println(String.format("gradient at %s = %s", x3, g3))
```

输出结果如下：

```
gradient at [0.000000, 0.000000]  = [0.000000, 0.000000]
gradient at [0.000000, 0.000000]  = [0.000000, 0.000000]
gradient at [-1.000000, 0.000000]  = [-2.349491, 0.000000]
gradient at [1.000000, 0.000000]  = [2.349491, 0.000000]
```

### 6.3.2 雅可比矩阵

正如将单变量标量值函数的导数推广到多变量标量值函数的梯度，你还可以将这一概念进一步推广到多变量向量值函数。假设有一个向量值函数 **f**: ℝ^*n* → ℝ^*m*，它输入 ℝ^*n* 空间中的点 **x** = (*x*[1], ⋯, *x*[*n*])，并输出 ℝ^*m* 空间中的向量 **f**(**x**)。**f** 的雅可比矩阵定义为一个 *m* × *n* 矩阵，记作 **J**，其第 (*i*, *j*) 个元素如下：

![$$ {\boldsymbol{J}}_{ij}=\frac{\partial {f}_i}{\partial {x}_j} $$](img/528380_1_En_6_Chapter_TeX_Equaq.png)

或者显式地表示为：

![$$ \boldsymbol{J}=\left[\frac{\partial \boldsymbol{f}}{\partial {x}_1}\cdots \frac{\partial \boldsymbol{f}}{\partial {x}_n}\right] $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equar.png)

![$$ =\left[\begin{array}{c}{\nabla}^T{f}_1\\ {}\vdots \\ {}{\nabla}^T{f}_m\end{array}\right] $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equas.png)

![$$ =\left[\begin{array}{ccc}\frac{\partial {f}_1}{\partial {x}_1}&amp; \cdots &amp; \frac{\partial {f}_1}{\partial {x}_n}\\ {}\vdots &amp; \ddots &amp; \vdots \\ {}\frac{\partial {f}_m}{\partial {x}_1}&amp; \cdots &amp; \frac{\partial {f}_m}{\partial {x}_n}\end{array}\right] $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equat.png)

其中 ∇^*T**f*[*i*] 是第 *i* 个分量梯度的转置（行向量）。

雅可比矩阵是由所有一阶偏导数构成的矩阵。它表示了 **f** 在其可微点处的微分。它是 **f** 在 **x** 邻域内变化的最佳线性近似。也就是说，对于所有靠近 **x** 的点 **y**，它是 **f**(**y**) 的最佳线性近似。这引出了以下形式的泰勒展开：

![$$ \boldsymbol{f}\left(\boldsymbol{y}\right)=\boldsymbol{f}\left(\boldsymbol{x}\right)+{\boldsymbol{J}}_{\boldsymbol{f}}\left(\boldsymbol{y}-\boldsymbol{x}\right)+O\left(\left\Vert \boldsymbol{y}-\boldsymbol{x}\right\Vert \right) $$](img/528380_1_En_6_Chapter_TeX_Equau.png)

当 **y** 趋近于 **x** 时，误差项趋近于零的速度远快于 **y** 与 **x** 之间距离的趋近速度。从这个意义上说，雅可比矩阵可以被视为多变量向量值函数的一种“一阶导数”。

如果 *m* = *n*，那么 **f** 是从 ℝ^*n* 到自身的一个函数，雅可比矩阵就是一个方阵。此时可以计算其行列式，称为雅可比行列式。雅可比行列式有时简称为“雅可比”。给定点处的雅可比行列式提供了关于 **f** 在该点附近局部行为的重要信息。例如，反函数定理指出，如果连续可微函数 **f** 在点 **x** ∈ ℝ^**n** 处的雅可比行列式非零，则 **f** 在 **x** 附近是可逆的。此外，如果点 **x** 处的雅可比行列式为正，则 **f** 在 **x** 附近保持定向；如果为负，则 **f** 反转定向。点 **x** 处雅可比行列式的绝对值给出了函数 **f** 在 **x** 附近膨胀或收缩体积的倍数。

NM Dev 支持计算多元向量值函数的雅可比矩阵和雅可比函数。相关的类是 `Jacobian` 和 `JacobianFunction`。它们的签名如下：


好的，作为高级文档工程师和翻译员，我已根据您的要求和示例格式，完成了以下中文翻译。


```
/**
* 为多元函数 f 在某点 x 处构建雅可比矩阵。
*
* @param f 一个多元函数
* @param x 计算雅可比矩阵的点
*/
public Jacobian(RealVectorFunction f, Vector x)
/**
* 为多元函数 f 在某点 x 处构建雅可比矩阵。
*
* @param f 以单变量函数数组形式表示的多元函数
* @param x 计算雅可比矩阵的点
*/
public Jacobian(final RealScalarFunction[] f, Vector x)
/**
* 为多元函数 f 在某点 x 处构建雅可比矩阵。
*
* @param f 以单变量函数列表形式表示的多元函数
* @param x 计算雅可比矩阵的点
*/
public Jacobian(final List f, Vector x)
/**
* 构建一个实标量函数 f 的雅可比函数。
*
* @param f 一个实标量函数
*/
public JacobianFunction(RealVectorFunction f
```

在 NM Dev 中，一个多元向量值函数可以通过 `RealVectorFunction`、`RealScalarFunction` 数组或 `RealScalarFunction` 列表来表示。

考虑这个函数 **F**: ℝ² → ℝ²:

![$$ \boldsymbol{F}\left(\left[\begin{array}{c}x\\ {}y\end{array}\right]\right)=\left[\begin{array}{c}{f}_1\left(x,y\right)\\ {}{f}_2\left(x,y\right)\end{array}\right]=\left[\begin{array}{c}{x}²y\\ {}5x+\sin y\end{array}\right] $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equav.png)

其雅可比矩阵如下：

![$$ {\boldsymbol{J}}_{\boldsymbol{F}}\left(x,y\right)=\left[\begin{array}{cc}\frac{\partial {f}_1}{\partial x}&amp; \frac{\partial {f}_1}{\partial y}\\ {}\frac{\partial {f}_2}{\partial x}&amp; \frac{\partial {f}_2}{\partial y}\end{array}\right]=\left[\begin{array}{cc}2 xy&amp; {x}²\\ {}5&amp; \cos y\end{array}\right] $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equaw.png)

雅可比行列式如下：

![$$ \left\Vert {\boldsymbol{J}}_{\boldsymbol{F}}\left(x,y\right)\right\Vert =2 xy\cos y-5{x}² $$](img/528380_1_En_6_Chapter_TeX_Equax.png)

以下代码解决了这个示例：

```
val F: RealVectorFunction = object : RealVectorFunction {
override fun evaluate(v: Vector): Vector {
val x: Double= v.get(1)
val y: Double= v.get(2)
val f1: Double = x * x * y
val f2: Double = 5.0 * x + sin(y)
return DenseVector(f1, f2)
}
override fun dimensionOfDomain(): Int {
return 2
}
override fun dimensionOfRange(): Int {
return 2
}
}
val x0: Vector = DenseVector(0.0, 0.0)
val J00: Matrix = Jacobian(F, x0)
println(String.format(
"\n 在 %s 处的雅可比矩阵 = %s，行列式 = %f",
x0,
J00,
MatrixMeasure.det(J00)))
val J: RntoMatrix = JacobianFunction(F) // [2xy, x²], [5, cosy]
val J01: Matrix = J.evaluate(x0)
println(String.format(
"\n 在 %s 处的雅可比矩阵 = %s，行列式 = %f",
x0,
J01,
MatrixMeasure.det(J01)))
val x1: Vector = DenseVector(1.0, PI)
val J1: Matrix = J.evaluate(x1)
println(String.format(
"在 %s 处的雅可比矩阵 = %s，行列式 = %f",
x1,
J1,
MatrixMeasure.det(J1)))
```

输出如下：

```
在 [0.000000, 0.000000] 处的雅可比矩阵  = 2x2
[,1] [,2]
[1,] 0.000000, 0.000000,
[2,] 5.000000, 1.000000, ，行列式 = 0.000000
在 [0.000000, 0.000000] 处的雅可比矩阵  = 2x2
[,1] [,2]
[1,] 0.000000, 0.000000,
[2,] 5.000000, 1.000000, ，行列式 = 0.000000
在 [1.000000, 3.141593] 处的雅可比矩阵  = 2x2
[,1] [,2]
[1,] 6.283185, 1.000000,
[2,] 5.000000, -1.000000, ，行列式 = -11.283185
```

考虑这个函数 **F**: ℝ³ → ℝ³:

![$$ \boldsymbol{F}\left(\left[\begin{array}{c}{x}_1\\ {}{x}_2\\ {}{x}_3\end{array}\right]\right)=\left[\begin{array}{c}5{x}_2\\ {}4{x_1}²-2\sin \left({x}_2{x}_3\right)\\ {}{x}_2{x}_3\end{array}\right] $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equay.png)

其雅可比矩阵如下：

![$$ {\textbf{J}}_{\textbf{F}}\left(x,y\right)=\left[\begin{array}{ccc}0&amp; 5&amp; 0\\ {}8{x}_1&amp; -2{x}_3\cos \left({x}_2{x}_3\right)&amp; -2{x}_2\cos \left({x}_2{x}_3\right)\\ {}0&amp; {x}_3&amp; {x}_2\end{array}\right] $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equaz.png)

雅可比行列式如下：

![$$ \left\Vert {\boldsymbol{J}}_{\boldsymbol{F}}\left({x}_1,{x}_2,{x}_3\right)\right\Vert =-40{x}_1{x}_2 $$](img/528380_1_En_6_Chapter_TeX_Equba.png)

以下代码解决了这个示例：

```
val F: RealVectorFunction = object : RealVectorFunction {
override fun evaluate(v: Vector): Vector {
val x1: Double = v.get(1)
val x2: Double = v.get(2)
val x3: Double = v.get(3)
val f1: Double = 5.0 * x2
val f2: Double = 4.0 * x1 * x1 - 2.0 * sin(x2 * x3)
val f3: Double = x2 * x3
return DenseVector(f1, f2, f3)
}
override fun dimensionOfDomain(): Int {
return 3
}
override fun dimensionOfRange(): Int {
return 3
}
}
val x0: Vector = DenseVector(0.0, 0.0, 1.0)
val J: RntoMatrix = JacobianFunction(F)
val J0: Matrix = J.evaluate(x0)
println(String.format(
"\n 在 %s 处的雅可比矩阵 = %s，行列式 = %f",
x0,
J0,
MatrixMeasure.det(J0)))
val x1: Vector = DenseVector(1.0, 2.0, 3.0)
val J1: Matrix = J.evaluate(x1)
println(String.format(
"在 %s 处的雅可比矩阵 = %s，行列式 = %f",
x1,
J1,
MatrixMeasure.det(J1)))
```

输出如下：

```
在 [0.000000, 0.000000, 1.000000] 处的雅可比矩阵  = 3x3
[,1] [,2] [,3]
[1,] 0.000000, 5.000000, 0.000000,
[2,] 0.000000, -2.000000, 0.000000,
[3,] 0.000000, 1.000000, 0.000000, ，行列式 = 0.000000
在 [1.000000, 2.000000, 3.000000] 处的雅可比矩阵  = 3x3
[,1] [,2] [,3]
[1,] 0.000000, 5.000000, 0.000000,
[2,] 8.000000, -5.761022, -3.840681,
[3,] 0.000000, 3.000000, 2.000000, ，行列式 = -80.000000
```

由此可见，**F** 在 `x[1]` 和 `x[2]` 符号相同的那些点附近会反转定向。除了在 `x[1] = 0` 或 `x[2] = 0` 的点附近外，该函数在其余各处都是局部可逆的。直观地说，如果你以点 `(1, 2, 3)` 周围的一个微小物体开始，并对其应用 **F**，你将得到一个体积大约是原始物体体积的 40 × 1 × 2 = 80 倍的新物体，并且定向被反转。



### 6.3.3 海森矩阵

你可以将单变量函数二阶导数的概念推广到多变量实值函数。首先计算标量函数的梯度（从某种意义上说，即求一阶导数）。然后计算梯度函数的雅可比矩阵（从某种意义上说，即求二阶导数）。这个雅可比矩阵被称为海森矩阵。海森矩阵是一个标量值函数或标量场的二阶偏导数构成的方阵。它描述了多变量函数的局部曲率。即：

![$$ \boldsymbol{H}\left(f\left(\boldsymbol{x}\right)\right)=\boldsymbol{J}\left(\nabla f\left(\boldsymbol{x}\right)\right) $$](img/528380_1_En_6_Chapter_TeX_Equbb.png)

具体来说，假设 *f*: ℝ^(*n*) → ℝ。如果 *f* 的所有二阶偏导数存在且在其定义域上连续，那么 *f* 的海森矩阵 ***H*** 是一个 *n* × *n* 方阵，通常按如下方式定义和排列：

![$$ {\boldsymbol{H}}_f=\left[\begin{array}{cccc}\frac{\partial²f}{\partial {x}_1²}&amp; \frac{\partial²f}{\partial {x}_1\partial {x}_2}&amp; \cdots &amp; \frac{\partial²f}{\partial {x}_1\partial {x}_n}\\ {}\frac{\partial²f}{\partial {x}_2\partial {x}_1}&amp; \frac{\partial²f}{\partial {x}_2²}&amp; \cdots &amp; \frac{\partial²f}{\partial {x}_2\partial {x}_n}\\ {}\vdots &amp; \vdots &amp; \ddots &amp; \vdots \\ {}\frac{\partial²f}{\partial {x}_n\partial {x}_1}&amp; \frac{\partial²f}{\partial {x}_n\partial {x}_2}&amp; \cdots &amp; \frac{\partial²f}{\partial {x}_n²}\end{array}\right] $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equbc.png)

简写形式如下：

![$$ {\left({\boldsymbol{H}}_f\right)}_{i,j}=\frac{\partial²f}{\partial {x}_i\partial {x}_j} $$](img/528380_1_En_6_Chapter_TeX_Equbd.png)

海森矩阵是一个对称矩阵。二阶导数连续的假设意味着求导顺序无关紧要（根据克莱罗定理）。海森矩阵的行列式被称为海森行列式。海森矩阵用于解决牛顿类方法中的优化问题，因为它们是函数局部泰勒展开中二次项的系数。即：

![$$ y=f\left(\boldsymbol{x}+\Delta \boldsymbol{x}\right)\approx f\left(\boldsymbol{x}\right)+\nabla f\left(\boldsymbol{x}\right)\Delta \boldsymbol{x}+\frac{1}{2}\Delta {\boldsymbol{x}}^{\boldsymbol{T}}H\left(\boldsymbol{x}\right)\Delta \boldsymbol{x} $$](img/528380_1_En_6_Chapter_TeX_Eqube.png)

我们将在第[11](http://dx.doi.org/10.1007/978-1-4842-6797-4_11)章更详细地讨论如何在优化中使用海森矩阵。

NM Dev 支持计算多变量实值函数的海森矩阵和海森函数。相关的类是 `Hessian` 和 `HessianFunction`。它们的签名如下：

```
/**
* 为多变量函数 f 在点 x 处构造海森矩阵。
*
* @param f 一个多变量函数
* @param x 要计算 f 的海森矩阵的点
*/
public Hessian(RealScalarFunction f, Vector x)
/**
* 构造实标量函数 f 的海森函数。
*
* @param f 一个实标量函数
*/
public HessianFunction(RealScalarFunction f)
```

以下示例代码计算了 *f*(*x*, *y*) = *xy* 的海森矩阵和海森函数：

```
val f: RealScalarFunction = object : AbstractBivariateRealFunction() {
override fun evaluate(x: Double, y: Double): Double {
return x * y // f = xy
}
}
val x1: Vector = DenseVector(1.0, 1.0)
val H1: Hessian = Hessian(f, x1)
println(String.format(
"\n 在 %s 处的海森矩阵 = %s, 行列式 = %f",
x1,
H1,
MatrixMeasure.det(H1)))
val H: RntoMatrix = HessianFunction(f)
val Hx1: Matrix = H.evaluate(x1)
println(String.format(
"\n 在 %s 处的海森矩阵 = %s, 行列式 = %f",
x1,
Hx1,
MatrixMeasure.det(Hx1)))
val x2: Vector = DenseVector(0.0, 0.0)
val H2: Hessian = Hessian(f, x2)
println(String.format(
"\n 在 %s 处的海森矩阵 = %s, 行列式 = %f",
x2,
H2,
MatrixMeasure.det(H2)))
val Hx2: Matrix = H.evaluate(x2)
println(String.format(
"\n 在 %s 处的海森矩阵 = %s, 行列式 = %f",
x2,
Hx2,
MatrixMeasure.det(Hx2)))
```

输出如下：

```
在 [1.000000, 1.000000] 处的海森矩阵 = 2x2
[,1] [,2]
[1,] 0.000000, 1.000000,
[2,] 1.000000, 0.000000, , 行列式 = -0.999999
在 [1.000000, 1.000000] 处的海森矩阵 = 2x2
[,1] [,2]
[1,] 0.000000, 1.000000,
[2,] 1.000000, 0.000000, , 行列式 = -0.999999
在 [0.000000, 0.000000] 处的海森矩阵 = 2x2
[,1] [,2]
[1,] 0.000000, 1.000000,
[2,] 1.000000, 0.000000, , 行列式 = -1.000000
在 [0.000000, 0.000000] 处的海森矩阵 = 2x2
[,1] [,2]
[1,] 0.000000, 1.000000,
[2,] 1.000000, 0.000000, , 行列式 = -1.000000
```

## 6.4 里德方法

里德方法通过对一系列近似值进行外推，改进了有限差分法的估算。要计算数值导数，首先必须使用一系列递减的步长来计算一系列近似值。然后，里德方法使用内维尔算法将步长外推至零。通常，它能比简单的（一次性）有限差分法提供更高的精度。

初始步长 *h*[0] 的选择至关重要。如果 *h*[0] 太大，计算出的值可能不精确。如果 *h*[0] 太小，由于舍入误差，对于不同的步长，你可能反复计算得到“相同”的值。

以下代码片段比较了使用里德方法与简单有限差分法计算的、在 *x* = 0.5 处对数函数的一至九阶导数值。

```
val f: UnivariateRealFunction = object : AbstractUnivariateRealFunction() {
override fun evaluate(x: Double): Double {
return ln(x)
}
}
val x: Double = 0.5
for (order in 1..9) {
val fd: FiniteDifference = FiniteDifference(f, order, FiniteDifference.Type.CENTRAL)
val ridder: Ridders = Ridders(f, order)
println(String.format(
"Ridder 法计算的 %d 阶导数 @ %f = %.16f", order, x, ridder.evaluate(x)))
println(String.format(
"有限差分法计算的 %d 阶导数 @ %f = %.16f", order, x, fd.evaluate(x)))
}
```

输出如下：

```
Ridder 法计算的 1 阶导数 @ 0.500000 = 2.0000000000000000
有限差分法计算的 1 阶导数 @ 0.500000 = 2.0000000000000000
Ridder 法计算的 2 阶导数 @ 0.500000 = -4.0000004374066640
有限差分法计算的 2 阶导数 @ 0.500000 = -4.0000031040867650
Ridder 法计算的 3 阶导数 @ 0.500000 = 16.0000016378464240
有限差分法计算的 3 阶导数 @ 0.500000 = 16.0002441406250000
Ridder 法计算的 4 阶导数 @ 0.500000 = -95.9999555891298100
有限差分法计算的 4 阶导数 @ 0.500000 = -95.9874881885177200
Ridder 法计算的 5 阶导数 @ 0.500000 = 767.9383982440593000
有限差分法计算的 5 阶导数 @ 0.500000 = 767.5675271157362000
Ridder 法计算的 6 阶导数 @ 0.500000 = -7681.9174458835320000
有限差分法计算的 6 阶导数 @ 0.500000 = -7686.1197112745450000
Ridder 法计算的 7 阶导数 @ 0.500000 = 92116.9184885280300000
有限差分法计算的 7 阶导数 @ 0.500000 = 92428.4022426225400000
Ridder 法计算的 8 阶导数 @ 0.500000 = -1290356.2663459945000000
有限差分法计算的 8 阶导数 @ 0.500000 = -1300812.8634378603000000
Ridder 法计算的 9 阶导数 @ 0.500000 = 20941653.5495638000000000
有限差分法计算的 9 阶导数 @ 0.500000 = 21347177.7802486680000000
```

前四阶导数的解析结果精确为 2、-4、16 和 -96。请注意，里德方法提供了更好的精度，尤其是对于通常对有限差分法具有挑战性的高阶导数。

里德方法也适用于多变量函数。以下示例比较了里德方法与第 6.3 节示例中有限差分法的结果。


```kotlin
// f = xy + 2xyz
val f: RealScalarFunction = object : RealScalarFunction {
override fun evaluate(v: Vector): Double {
return v.get(1) * v.get(2) + 2 * v.get(1) * v.get(2) * v.get(3)
}
override fun dimensionOfDomain(): Int {
return 3
}
override fun dimensionOfRange(): Int {
return 1
}
}
val dxy_ridder: Ridders = Ridders(f, intArrayOf(1, 2))
val dxy: MultivariateFiniteDifference // 1 + 2z
// 先对第一个变量求导，再对第二个变量求导
= MultivariateFiniteDifference(f, intArrayOf(1, 2))
val x0: Vector = DenseVector(1.0, 1.0, 1.0)
println(String.format("Dxy(%s) by Ridder = %.16f", x0, dxy_ridder.evaluate(x0)))
println(String.format("Dxy(%s) by FD =  %.16f", x0, dxy.evaluate(x0)))
val x1: Vector = DenseVector(-100.0, 0.0, -1.5)
println(String.format("Dxy(%s) by FD = %.16f", x1, dxy_ridder.evaluate(x1)))
println(String.format("Dxy(%s) by FD = %.16f", x1, dxy.evaluate(x1)))
// 由克莱罗定理，连续函数允许交换求导顺序
val dyx_ridder: Ridders = Ridders(f, intArrayOf(2, 1))
val dyx: MultivariateFiniteDifference // 1 + 2z
// 先对第二个变量求导，再对第一个变量求导
= MultivariateFiniteDifference(f, intArrayOf(2, 1))
println(String.format("Dyx(%s) by Ridder = %.16f", x0, dyx_ridder.evaluate(x0)))
println(String.format("Dyx(%s) by FD = %.16f", x0, dyx.evaluate(x0)))
println(String.format("Dyx(%s) by Ridder = %.16f", x1, dyx_ridder.evaluate(x1)))
println(String.format("Dyx(%s) by FD = %.16f", x1, dyx.evaluate(x1)))
```

输出结果如下：

```
Dxy([1.000000, 1.000000, 1.000000] ) by Ridder = 2.9999999941935465
Dxy([1.000000, 1.000000, 1.000000] ) by FD =  3.0000013187575885
Dxy([-100.000000, 0.000000, -1.500000] ) by FD = -2.0000000000032907
Dxy([-100.000000, 0.000000, -1.500000] ) by FD = -1.9999999999865270
Dyx([1.000000, 1.000000, 1.000000] ) by Ridder = 2.9999999941935465
Dyx([1.000000, 1.000000, 1.000000] ) by FD = 3.0000013187575885
Dyx([-100.000000, 0.000000, -1.500000] ) by Ridder = -2.0000000000044610
Dyx([-100.000000, 0.000000, -1.500000] ) by FD = -1.9999999999748960
```

解析结果分别为 3 和 -2。注意，Ridders 方法比有限差分法具有更好的精度。

## 6.5 特殊函数的导数函数

`NM Dev` 支持许多特殊函数及其一阶导数函数。特殊函数通常指无法通过有限个初等函数（如有限个多项式、有理函数、三角函数、指数函数及其反函数的和、积或复合）计算出的函数。其导数函数也是如此。由于这些函数在许多应用中的重要性（例如统计学中的高斯函数），您必须能够精确计算这些函数，而无法承受使用有限差分法带来的不精确。针对特殊函数，有专门的计算技术，例如连分数。`NM Dev` 支持的特殊函数及其一阶导数函数包括：高斯函数、误差函数、贝塔函数、正则化不完全贝塔函数和伽马函数。

### 6.5.1 高斯导数函数

高斯函数定义如下：

![$$ f(x)=a\exp \left(-\frac{{\left(x-b\right)}²}{2{c}²}\right) $$](img/528380_1_En_6_Chapter_TeX_Equbf.png)

计算高斯函数的类是 `Gaussian`，其签名如下：

```java
/**
* 构造一个高斯函数的实例。
*
* @param a a
* @param b b
* @param c c
*/
public Gaussian(double a, double b, double c)
```

计算高斯函数导数的类是 `DGaussian`，其签名如下：

```java
/**
* 构造高斯函数的导数函数。
*
* @param phi 一个 {@link Gaussian} 函数
*/
public DGaussian(Gaussian phi)
```

示例如下：

```kotlin
val G: Gaussian = Gaussian(1.0, 0.0, 1.0) // 标准高斯函数
val dG: DGaussian = DGaussian(G)
var x: Double = -0.5
println(String.format("dG/dx(%f) = %f", x, dG.evaluate(x)))
x = 0.0
println(String.format("dG/dx(%f) = %f", x, dG.evaluate(x)))
x = 0.5
println(String.format("dG/dx(%f) = %f", x, dG.evaluate(x)))
```

输出结果如下：

```
dG/dx(-0.500000) = 0.176033
dG/dx(0.000000) = -0.000000
dG/dx(0.500000) = -0.176033
```

### 6.5.2 误差导数函数

误差函数定义如下：

![$$ erzz=\frac{2}{\pi }{\int}_0^z{e}^{-{t}²} dt $$](img/528380_1_En_6_Chapter_TeX_Equbg.png)

计算误差函数的类是 `Erf`。计算误差函数导数的类是 `DErf`。这两个类都从 `AbstractUnivariateRealFunction` 继承了 `evaluate(double x)` 方法。它们的构造函数不带任何参数。

示例如下：

```kotlin
val z: Double = 0.5
val E: Erf = Erf()
val dE: DErf = DErf()
println(String.format("erf(%f) = %f", z, E.evaluate(z)))
println(String.format("dErf/dz(%f) = %f", z, dE.evaluate(z)))
```

输出结果如下：

```
erf(0.500000) = 0.520500
dErf/dz(0.500000) = 0.878783
```

### 6.5.3 贝塔导数函数

贝塔函数也称为第一类欧拉积分。对于任意实数 `x`，`y` > 0，其定义如下：

![$$ B\left(x,y\right)={\int}_0¹{t}^{x-1}{\left(1-t\right)}^{y-1} dt $$](img/528380_1_En_6_Chapter_TeX_Equbh.png)

计算贝塔函数的类是 `Beta`。计算贝塔函数导数的类是 `DBeta`。这两个类都从 `AbstractBivariateRealFunction` 继承了 `evaluate(double x, double y)` 方法。它们的构造函数不带任何参数。

示例如下：

```kotlin
val x: Double = 1.5
val y: Double = 2.5
val B: Beta = Beta()
val dB: DBeta = DBeta()
println(String.format("Beta(%f) = %f", x, B.evaluate(x, y)))
println(String.format("dBeta/dz(%f) = %f", x, dB.evaluate(x, y)))
```

输出结果如下：

```
Beta(1.500000) = 0.196350
dBeta/dz(1.500000) = -0.239473
```

### 6.5.4 正则化不完全贝塔导数函数

不完全贝塔函数是贝塔函数的一种推广，定义如下：

![$$ B\left(x;p,q\right)={\int}_0^x{t}^{p-1}{\left(1-t\right)}^{q-1} dt $$](img/528380_1_En_6_Chapter_TeX_Equbi.png)

当 `x` = 1 时，不完全贝塔函数与完全贝塔函数重合。

正则化不完全贝塔函数（简称正则化贝塔函数）由不完全贝塔函数和完全贝塔函数定义：

![$$ {I}_x\left(p,q\right)=\frac{B\left(x;p,q\right)}{B\left(p,q\right)} $$](img/528380_1_En_6_Chapter_TeX_Equbj.png)

正则化不完全贝塔函数是贝塔分布的累积分布函数。

计算正则化不完全贝塔函数的类是 `BetaRegularized`，其签名如下：

```java
/**
* 构造一个具有参数 p 和 q 的 Ix(p,q) 实例。
*
* @param p p > 0，形状参数
* @param q q > 0，形状参数
*/
public BetaRegularized(final double p, final double q)
```

计算正则化不完全贝塔函数导数的类是 `DBetaRegularized`，其签名如下：

```java
/**
* 构造正则化不完全贝塔函数 {@link BetaRegularized} 的导数函数。
*
* @param p 形状参数
* @param q 形状参数
*/
public DBetaRegularized(double p, double q)
```

示例如下：

```kotlin
val p: Double = 0.5
val q: Double = 2.5
val I: BetaRegularized = BetaRegularized(p, q)
val dI: DBetaRegularized = DBetaRegularized(p, q)
val x: Double = 1.0
println(String.format("BetaRegularized(%f) = %f", x, I.evaluate(x)))
println(String.format("dBetaRegularized/dz(%f) = %f", x, dI.evaluate(x)))
```

输出结果如下：

```
BetaRegularized(1.000000) = 1.000000
dBetaRegularized/dz(1.000000) = 0.000000
```


### 6.5.5 Gamma 导数函数

`Gamma` 函数是阶乘函数向实数和复数领域的推广，其自变量下移了 1。对于实数，其定义如下：

![$$ \Gamma (z)={\int}_0^{\infty }{x}^{z-1}{e}^{-x} dx $$](img/528380_1_En_6_Chapter_TeX_Equbk.png)

用于计算 Gamma 函数的类是 `Gamma`。用于计算 Gamma 函数导数函数的类是 `DGamma`。两个类都继承自 `AbstractUnivariateRealFunction` 的 `evaluate(double x)` 方法。它们的构造方法不接受任何参数。NM Dev 提供了多种 Gamma 函数的实现。它们在精度和性能方面具有不同的特性。默认选择是 Lanczos 算法，所有计算均使用 `double` 完成。

示例如下：

```kotlin
val z: Double = 0.5
// 维基百科：Lanczos 近似
val G: Gamma = GammaLanczosQuick()
val dG: DGamma = DGamma()
println(String.format("Gamma(%f) = %f", z, G.evaluate(z)))
println(String.format("dGamma/dz(%f) = %f", z, dG.evaluate(z)))
```

输出结果如下：

```
Gamma(0.500000) = 1.772454
dGamma/dz(0.500000) = -3.480231
```

### 6.5.6 多项式导数函数

多项式并非严格意义上的特殊函数。我们在此将其收录，是因为 NM Dev 不使用有限差分法来计算多项式的导数函数。对多项式求导存在闭式解。

![$$ p(x)={a}_n{x}^n+\cdots {a}_1x+{a}_0 $$](img/528380_1_En_6_Chapter_TeX_Equbl.png)

![$$ \frac{dp}{dx}={na}_n{x}^{n-1}+\cdots {a}_2x+{a}_1 $$](img/528380_1_En_6_Chapter_TeX_Equbm.png)

用于计算多项式的类是 `Polynomial`，其签名如下：

```java
/**
 * 根据系数数组构造一个多项式。
 * 第一个/索引为 0 的项对应 x^n 项。
 * 最后一个/索引为 n 的项对应常数项。
 * 多项式的次数为 n，即数组长度减 1。
 *
 * 例如，
 *
 * new Polynomial(1, -2, 3, 2)
 *
 * 将创建一个表示 x³ - 2x² + 3x + 2 的 {@code Polynomial} 实例。
 *
 * @param coefficients 多项式系数
 */
public Polynomial(double... coefficients)
```

用于计算多项式导数函数的类是 `DPolynomial`，其签名如下。多项式的导数函数仍然是一个多项式。

```java
/**
 * 构造一个 {@link Polynomial} 的导数函数，该导数函数本身也是一个多项式。
 *
 * @param polynomial 一个多项式
 */
public DPolynomial(Polynomial polynomial)
```

示例如下：

```kotlin
val p: Polynomial = Polynomial(1.0, 2.0, 1.0) // x² + 2x + 1
val dp: Polynomial = DPolynomial(p) // 2x + 2
val x: Double = 1.0
println(String.format("dp/dx(%f) = %f", x, dp.evaluate(x)))
```

输出结果如下：

```
dp/dx(1.000000) = 4.000000
```

## 6.6 数值积分

定积分是曲线或函数 *f*(*x*)（称为 *被积函数*）在实数轴上两点 [*a*, *b*] 之间所形成的（有符号）面积。其定义如下：

![$$ I={\int}_a^bf(x) dx $$](img/528380_1_En_6_Chapter_TeX_Equbn.png)

符号 `dx`，称为变量 `x` 的微分，表示积分变量是 `x`。它表示 `x` 的一个微小变化，如同微分中的概念。积分就是计算定积分值的过程。微积分第一基本定理表明，积分的导数就是被积函数。因此，积分有时也被称为函数的反导数。见图 6-10。

![$$ f(x)=\frac{d}{dx}{\int}_0^xf(t) dt $$](img/528380_1_En_6_Chapter_TeX_Equbo.png)

![](img/528380_1_En_6_Fig10_HTML.png)

在曲线下方，三个区域（正面积和负面积）在 X Y 轴上从 a 到 b 的距离处被阴影标出，对应 f(x)。

**图 6-10.** 积分作为曲线下的有符号面积

黎曼给出了积分的第一个严格定义。黎曼积分的基本思想是将面积划分成许多矩形来近似面积，然后将它们求和（因此得名积分）。通过采用越来越好的近似，比如说无穷多个无穷小的矩形，你可以说“在极限情况下”，你就得到了曲线下的精确面积。图 6-11 展示了一个区间上规则划分的一系列黎曼和。顶部的数字是矩形的总面积，它收敛于函数的积分。

![](img/528380_1_En_6_Fig11_HTML.jpg)

六幅具有不同 A 值的图通过六个步骤减小 A 值，展示了柱状图随着下降和上升曲线的组合而依次增加的过程。

**图 6-11.** 一系列黎曼和

从数学上讲，区间 [*a*, *b*] 的一个划分是一组有限点序列：

![$$ a={x}_0&lt;{x}_1&lt;\cdots {x}_n=b $$](img/528380_1_En_6_Chapter_TeX_Equbp.png)

在每个子区间内，有一个选定点 `t[i]` ∈ [`x[i]`, `x[i+1]`] 用于确定矩形 `f(t[i])` 的高度。因此，矩形面积为 `f(t[i])` (`x[i+1] - x[i]`)。函数 `f` 相对于该划分的黎曼和定义为所有矩形面积之和。

![$$ {S}_n=\sum \limits_{i=0}^{n-1}f\left({t}_i\right)\left({x}_{i+1}-{x}_i\right) $$](img/528380_1_En_6_Chapter_TeX_Equbq.png)

当取 `n` 趋于无穷大的极限时，黎曼和就变成了积分值：

![$$ I=\underset{n\to \infty }{\lim }{S}_n $$](img/528380_1_En_6_Chapter_TeX_Equbr.png)

数值积分是一种使用类似划分概念来近似定积分的技术：使用被积函数有限样本值的加权平均近似来替换函数值。数值积分之所以重要，是因为（通常）无法解析地找到（闭式）反导数。即使反导数存在，由于它们可能无法由初等函数构成，计算起来也可能并不容易。计算数值近似值比计算用特殊函数或无穷级数表示的反导数要容易得多。此外，被积函数可能仅在特定点（例如通过采样获得）已知。出于这个原因，一些嵌入式系统和其他计算机应用程序可能需要数值积分。

传统上，计算面积大小的数学方法被称为*求积*。现在它已成为积分的同义词，但使用较少。数值积分公式仍然被称为数值求积公式。进行数值积分的方法有很多种。它们的区别在于将区间划分成多少份、划分是否等距、如何选择选定点、是开式（不使用端点）还是闭式（使用端点），以及是否（以及如何）进行外推。本章只能浅尝辄止，介绍一些最基本的变体。

## 6.7 牛顿-柯特斯家族

牛顿-柯特斯公式，也称为牛顿-柯特斯求积法则或简称为牛顿-柯特斯法则，是一组基于在等距点上计算被积函数值的数值积分公式。它们以艾萨克·牛顿和罗杰·柯特斯的名字命名。如果被积函数在等距点上的值可用，那么牛顿-柯特斯公式会很有用。如果可能使用一组不同的（可能不等距的）点，那么其他方法（如高斯求积）可能更精确。牛顿-柯特斯公式有多种推导方式。其中最著名的两个是梯形求积公式和辛普森求积公式。



### 6.7.1 梯形求积公式

梯形公式通过用一条直线代替函数，本质上是将函数下方的区域近似为梯形并计算其面积。参见图 6-12。

![](img/528380_1_En_6_Fig12_HTML.jpg)

梯形公式的图示展示了一条方程 `y = x² + 4x + 6` 的向上凸曲线。两条垂直线投影到 X 轴上，形成了一个带阴影的梯形。

**图 6-12** 梯形公式

通过 `a` 点和 `b` 点的直线方程如下：

![$$ {L}_1(x)=\frac{f(b)-f(a)}{b-a}\left(x-a\right)+f(a) $$](img/528380_1_En_6_Chapter_TeX_Equbs.png)

直线下方的面积就是梯形的面积，计算方法为：底边（平行边）之和乘以高（底边之间的垂直距离），然后除以 2。你也可以通过正规的积分运算得到相同结果。

用 `L1(x)` 替换 `f(x)`，得到：

![$$ {\int}_a^bf(x) dx\approx {\int}_a^b{L}_1(x) dx $$](img/528380_1_En_6_Chapter_TeX_Equbt.png)

![$$ ={\int}_a^b\left[\frac{f(b)-f(a)}{b-a}\left(x-a\right)+f(a)\right] dx $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equbu.png)

![$$ =\frac{f(a)+f(b)}{2}\left(b-a\right) $$](img/528380_1_En_6_Chapter_TeX_Equbv.png)

对于函数 `f(x)` 和区间 `[a, b]`，可以将区间划分为 `N` 个子区间。然后对每个子区间应用梯形法则并将结果求和。

令

![$$ \Delta x=\frac{b-a}{N} $$](img/528380_1_En_6_Chapter_TeX_Equbw.png)

![$$ {\int}_a^bf(x) dx\approx \frac{\Delta x}{2}\sum \limits_{k=1}^N\left(f\left({x}_{k-1}\right)+f\left({x}_k\right)\right) $$](img/528380_1_En_6_Chapter_TeX_Equbx.png)

可以证明，假设 `f''(x)` 在 `[a, b]` 上存在且连续，则对于某个 `ξ` ∈ `[a, b]`，使用梯形法则的误差如下：

![$$ \varepsilon =-\frac{{\left(b-a\right)}³}{12N}{f}^{\prime \prime}\left(\xi \right) $$](img/528380_1_En_6_Chapter_TeX_Equby.png)

在 NM Dev 中，执行梯形积分的类是 `Trapezoidal`。下面的示例解决了图 6-12 中的问题：

```kotlin
val f: UnivariateRealFunction = object : AbstractUnivariateRealFunction() {
    override fun evaluate(x: Double): Double {
        return -(x * x - 4 * x - 6) // -(x² - 4x - 6)
    }
}
// 积分上下限
val a: Double = 1.5
val b: Double = 3.5
// 使用梯形法则的积分器
val integrator: Integrator = Trapezoidal(1e-8, 20) // 精度，最大迭代次数
// 执行积分
val I: Double = integrator.integrate(f, a, b)
println(String.format("S_[%.1f,%.1f] f(x) dx = %f", a, b, I))
```

输出如下：

```
S_[1.5,3.5] f(x) dx = 18.833333
```

`Trapezoidal` 类的签名如下：

```java
/**
 * 构造一个实现梯形法则的积分器。
 *
 * @param precision     收敛阈值
 * @param maxIterations 最大迭代次数
 */
public Trapezoidal(double precision, int maxIterations)
```

在 NM Dev 中，所有积分器（算法）的实现都继承自 `Integrator` 类。它们都调用 `integrate` 来执行任务。

```java
/**
 * 对函数 f 从 a 到 b 进行积分，
 * \[
 * \int_a^b\! f(x)\, dx
 * \]
 *
 * @param f 一个一元函数
 * @param a 积分下限
 * @param b 积分上限
 * @return \(\int_a^b\! f(x)\, dx\)
 */
public double integrate(UnivariateRealFunction f, double a, double b);
```

### 6.7.2 辛普森求积公式

辛普森公式不是用一条直线，而是用一个二次函数或抛物线来替换 `f(x)`。区间被分成两个部分，产生三个点：`a`、`b` 和中点 `m = (a + b) / 2`。该二次函数如下：

![$$ {L}_2(x)= $$](img/528380_1_En_6_Chapter_TeX_Equbz.png)

![$$ \frac{\left(x-m\right)\left(x-b\right)}{\left(a-m\right)\left(a-b\right)}f(a)+\frac{\left(x-a\right)\left(x-b\right)}{\left(m-a\right)\left(m-b\right)}f(m)+\frac{\left(x-m\right)\left(x-a\right)}{\left(b-m\right)\left(b-a\right)}f(b) $$](img/528380_1_En_6_Chapter_TeX_Equca.png)

从几何角度来看，辛普森公式计算抛物线所围成的曲边梯形的面积，用以近似 `f(x)` 所围成的面积。参见图 6-13。

![](img/528380_1_En_6_Fig13_HTML.jpg)

图示借助两条曲线和一个阴影区域说明了辛普森求积公式。一条曲线对应方程 `y = 0.5x³ + 3x² - 4x + 3`，另一条表示误差。

**图 6-13** 辛普森公式

用 `L2(x)` 替换 `f(x)`，得到：

![$$ {\int}_a^bf(x) dx\approx {\int}_a^b{L}_2(x) dx $$](img/528380_1_En_6_Chapter_TeX_Equcb.png)

![$$ =\frac{b-a}{6}\left[f(a)+4f\left(\frac{a+b}{2}\right)+f(b)\right] $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equcc.png)

![$$ =\frac{h}{3}\left[f(a)+4f\left(\frac{a+b}{2}\right)+f(b)\right] $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equcd.png)

`h = (b - a) / 2` 是每个子区间的步长。由于公式中存在 `1/3`，该辛普森法则通常被称为辛普森的 1/3 法则。

对于函数 `f(x)` 和区间 `[a, b]`，可以将区间划分为 `N` 个子区间。然后对每个子区间应用辛普森法则并将结果求和。得到以下公式：

![$$ {\int}_a^bf(x) dx\approx \frac{h}{3}\sum \limits_{j=1}^{N/2}\left[f\left({x}_{2j-2}\right)+4f\left({x}_{2j-1}\right)+f\left({x}_{2j}\right)\right] $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equce.png)

对于二阶连续可微的函数，辛普森法则有望比梯形法则更优。但对于较粗糙的函数，梯形法则可能更可取。可以证明，假设四阶导数 `f⁴(x)` 在 `[a, b]` 上存在且连续，则对于某个 `ξ` ∈ `[a, b]`，使用辛普森 1/3 法则的误差如下：

![$$ \varepsilon =-\frac{h⁴}{180}\left(b-a\right){f}^{(4)}\left(\xi \right) $$](img/528380_1_En_6_Chapter_TeX_Equcf.png)

在 NM Dev 中，执行辛普森 1/3 积分法的类是 `Simpson`。下面的示例求解了图 6-12 中的问题：

```kotlin
val f: UnivariateRealFunction = object : AbstractUnivariateRealFunction() {
    override fun evaluate(x: Double): Double {
        return -(x * x - 4 * x + 6) // -(x² - 4x + 6)
    }
}
// 积分上下限
val a: Double = 1.5
val b: Double = 3.5
// 使用辛普森法则的积分器
val integrator: Integrator = Simpson(1e-8, 20) // 精度，最大迭代次数
// 执行积分
val I: Double = integrator.integrate(f, a, b)
println(String.format("S_[%.0f,%.0f] f(x) dx = %f", a, b, I))
```

输出如下：

```
S_[1.5,3.5] f(x) dx = 18.833333
```

`Simpson` 类的签名如下：

```java
/**
 * 构造一个实现辛普森法则的积分器。
 *
 * @param precision     收敛阈值
 * @param maxIterations 最大迭代次数
 */
public Simpson(double precision, int maxIterations)
```



### 6.7.3 牛顿-柯特斯求积公式

可以想象，你可以将区间 `[a, b]` 划分为多于一个子区间（如梯形法则的情况）或多于两个子区间（如辛普森法则的情况）。你可以将其划分为 `n` 个区间。你也可以使用包含端点 `a` 和 `b` 的闭型公式，或者由于 `f(a)` 和 `f(b)` 可能不存在而不使用端点的开型公式。牛顿-柯特斯公式就是这样的一个推广。它将区间 `[a, b]` 划分为 `n` 个相等的子区间。对于闭型公式，等距节点如下所示：

`x[i] = a + ih`, `h = (b - a) / n`

对于开型公式，等距节点如下所示：

`x[i] = a + (i + 1)h`, `h = (b - a) / (n + 2)`

对于闭型公式，可以构造一个 `n` 次差分多项式，如下所示：

```
L_n(x) = ∑_{k=0}^n (w(x) / ((x - x_k) * w'(x_k))) * f(x_k)
```

其中 `w(x) = (x - x[0])(x - x[1])⋯(x - x[n])`。

用 `L_n(x)` 替换 `f(x)`，得到以下结果：

```
∫_a^b f(x) dx ≈ ∫_a^b L_n(x) dx = ∫_a^b (∑_{k=0}^n (w(x) / ((x - x_k) * w'(x_k))) * f(x_k)) dx
= ∑_{i=0}^n (∫_a^b (w(x) / ((x - x_k) * w'(x_k))) dx) * f(x_k)
= ∑_{i=0}^n A_k * f(x_k)
```

其中系数 `A_k = ∫_a^b (w(x) / ((x - x_k) * w'(x_k))) dx`。

该公式称为牛顿-柯特斯公式。这些系数称为牛顿-柯特斯系数。要使用该公式，你需要计算系数 `A[k]`。代入 `x = a + th`，得到：

```
w(x) = w(a + th) = h^(n+1) * t * (t - 1) * ... * (t - n)
w'(x) = h^n * (-1)^(n-1) * (k!) * (n - k)!
A_k = ∫_a^b (w(x) / ((x - x_k) * w'(x_k))) dx
= ∫_0^n (h^(n+1) * t * (t - 1) * ... * (t - n)) / (h^n * (-1)^(n-1) * (k!) * (n - k)! * h * (t - k)) * h dt
```

设

```
C_k^(n) = ((-1)^(n-k)) / (n * (k!) * (n - k)!) * ∫_0^n (t * (t - 1) * ... * (t - n)) / (t - k) dt
```

因此

```
A_k = (b - a) * C_k^(n)
```

注意，`A[k]` 是一个常数，仅取决于区间 `[a, b]` 和划分的子区间数 `n`，而与函数无关。因此，牛顿-柯特斯系数可以预先计算。

梯形公式和辛普森公式是牛顿-柯特斯公式的特例。当 `n = 1` 时，牛顿-柯特斯公式如下：

```
L_1(x) = (b - a) / 2 * [f(a) + f(b)]
```

这就是梯形公式。

当 `n = 2` 时，牛顿-柯特斯公式如下：

```
L_2(x) = (b - a) / 6 * [f(a) + 4f((a + b) / 2) + f(b)]
```

这就是辛普森公式。

当 `n = 4` 时，牛顿-柯特斯公式如下：

```
C = (b - a) / 90 * [7f(x_0) + 32f(x_1) + 12f(x_2) + 32f(x_3) + 7f(x_4)]
```

并且 `x[i] = a + kh`（`k = 0, 1, 2, 3, 4`），`h = (b - a) / 4`。

参见图 6-14。

图表用两条曲线展示了 n 等于 2 和 4 时的牛顿-柯特斯公式。标记了五个节点，两条曲线在此重合。

**图 6-14** n=2 和 n=4 时的牛顿-柯特斯公式

代数精度是衡量数值积分公式逼近效果的一种方法。注意，牛顿-柯特斯系数 `A[k]` 取决于 `n`，但不依赖于被积函数本身。对于 `n = 1`，梯形公式是一个 1 次多项式。如果牛顿-柯特斯公式中的 `f(x)` 是次数不超过 `n` 的多项式，则等号成立。

```
∫_a^b f(x) dx ≈ ∑_{k=0}^n A_k * f(x_k)
```

如果 `f(x)` 是一个 `n + 1` 次多项式，则右侧只是一个近似。称该求积公式具有 `n` 次代数精度。

对于 `n = 1`，梯形求积规则的误差如下，并称其具有一次代数精度。

```
ε = -((b - a)³) / (12N) * f''(ξ), a ≤ ξ ≤ b
```

对于 `n = 2`，辛普森求积规则的误差如下，并称其具有三次代数精度。

```
ε = -(h⁴) / 180 * (b - a) * f⁴(ξ), a ≤ ξ ≤ b
```

对于一般的 `n`，误差项如下：

```
ε = (f^(n+1)(ξ)) / ((n + 1)!) * w(x), a ≤ ξ ≤ b
```

如果 `f(x)` 是 `n` 次多项式，那么 `f^(n+1)(x) = 0`。因此，误差 `ε = 0`。牛顿-柯特斯求积公式的代数精度至少为 `n`。有一个定理指出：当 `n` 为奇数时，代数精度为 `n`。当 `n` 为偶数时，代数精度为 `n + 1`。例如，当 `n = 2` 时，辛普森公式的代数精度为 3。当 `n = 4` 时，代数精度为 5。

假设 `f(x[i])`（`x[i]` 为格点）的舍入误差为 `ε_i, ε_max = max(0 ≤ i ≤ n) |ε_i|`，则牛顿-柯特斯公式的误差如下：

```
ε = |b - a| * |∑_{i=0}^n C_i^(n) * ε_i| ≤ |(b - a)| * ε_max * |∑_{i=0}^n C_i^(n)|
```

最后一项 `∑_{i=0}^n |C_i^(n)|` 随着 `n` 的增大而增大。这将导致近似误差增大，因此牛顿-柯特斯公式对于较大的 `n` 值是不稳定的。因此，在实践中，牛顿-柯特斯公式很少用于 `n ≥ 8` 的情况。

考虑这个积分示例：

```
I = ∫_0¹ y dx = ∫_0¹ (4 / (1 + x²)) dx = π
```

参见图 6-15。



![](img/528380_1_En_6_Fig15_HTML.jpg)

一张图描绘了一条下凹曲线，其中部分区域被着色，以展示π的定积分。两条线投影到 X 轴上，形成了一个着色区域。

**图 6-15** π的定积分

使用梯形公式，得到如下结果：

![$$ {\int}_0¹ ydx\approx \frac{1-0}{2}\left[\frac{4}{1+1}+\frac{4}{1+0}\right]=3 $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equcz.png)

参见图 6-16。

![](img/528380_1_En_6_Fig16_HTML.jpg)

一张图描绘了一条下凹曲线，其中部分区域被着色，以展示π的梯形求积公式。两条线投影到 X 轴上，形成了一个着色区域。

**图 6-16** π的梯形公式

使用辛普森公式，得到如下结果：

![$$ {\int}_0¹ ydx\approx \frac{1-0}{6}\left[\frac{4}{1+1}+4\frac{4}{1+\frac{1}{4}}+\frac{4}{1+0}\right]\approx 3.1333 $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equda.png)

参见图 6-17 和图 6-18。

![](img/528380_1_En_6_Fig18_HTML.jpg)

一张图描绘了两条递减曲线和一个着色区域，以展示π的辛普森求积公式。三个节点被标记并投影到 X 轴上。

**图 6-18** π的辛普森公式

![](img/528380_1_En_6_Fig17_HTML.jpg)

一张图描绘了两条曲线，以展示π的辛普森求积公式。三个节点被标记在两条曲线重合的位置。

**图 6-17** π的辛普森公式

使用 `n = 4` 的牛顿-柯特斯公式，得到如下结果：

![$$ {x}_0=0,{x}_1=0.2500,{x}_2=0.5000,{x}_3=0.7500,{x}_4=1.0000 $$](img/528380_1_En_6_Chapter_TeX_Equdb.png)

![$$ {\int}_0¹ ydx\approx \frac{b-a}{90}\left[7f\left({x}_0\right)+32f\left({x}_1\right)+12f\left({x}_2\right)+32f\left({x}_3\right)+7f\left({x}_4\right)\right]\approx 3.1421 $$](images/528380_1_En_6_Chapter/528380_1_En_6_Chapter_TeX_Equdc.png)

参见图 6-19 和图 6-20。

![](img/528380_1_En_6_Fig20_HTML.jpg)

一张图展示了两条曲线同时先上升后下降一段时间的趋势。五个节点被标记在曲线重叠部分，并投影到 X 轴上，形成了一个着色区域。

**图 6-20** π的 `n=4` 牛顿-柯特斯公式

![](img/528380_1_En_6_Fig19_HTML.jpg)

一张图展示了两条曲线同时先上升后下降一段时间的趋势。五个节点被标记在曲线重叠部分。

**图 6-19** π的 `n=4` 牛顿-柯特斯公式

以下 NM Dev 代码解决了这个示例：

```
val f: UnivariateRealFunction = object : AbstractUnivariateRealFunction() {
override fun evaluate(x: Double): Double {
return 4.0 / (1.0 + x * x) // 4/(1+x²)
}
}
// 积分限
val a: Double = 0.0
val b: Double = 1.0
val integrator1: Integrator = Trapezoidal(1e-8, 20) // 使用梯形法则
val integrator2: Integrator = Simpson(1e-8, 20) // 使用辛普森法则
val integrator3: Integrator = NewtonCotes(3, NewtonCotes.Type.CLOSED, 1e-8, 20) // 使用牛顿-柯特斯封闭法则
val integrator4: Integrator = NewtonCotes(3, NewtonCotes.Type.OPEN, 1e-8, 20) // 使用牛顿-柯特斯开放法则
// 积分计算
val I1: Double = integrator1.integrate(f, a, b)
println(String.format("S_[%.0f,%.0f] f(x) dx = %.16f, 使用梯形法则", a, b, I1))
val I2: Double = integrator2.integrate(f, a, b)
println(String.format("S_[%.0f,%.0f] f(x) dx = %.16f, 使用辛普森法则", a, b, I2))
val I3: Double = integrator3.integrate(f, a, b)
println(String.format("S_[%.0f,%.0f] f(x) dx = %.16f, 使用牛顿-柯特斯封闭法则", a, b, I3))
val I4: Double = integrator4.integrate(f, a, b)
println(String.format("S_[%.0f,%.0f] f(x) dx = %.16f, 使用牛顿-柯特斯开放法则", a, b, I4))
```

输出如下：

```
S_[0,1] f(x) dx = 3.1415926436556850, 使用梯形法则
S_[0,1] f(x) dx = 3.1415926535528365, 使用辛普森法则
S_[0,1] f(x) dx = 3.1415926497180000, 使用牛顿-柯特斯封闭法则
S_[0,1] f(x) dx = 3.1415926674370604, 使用牛顿-柯特斯开放法则
```

`NewtonCotes` 类的签名如下：

```
/**
* 构造一个牛顿-柯特斯求积的实例。
*
* @param rate          进一步细分积分区间的比率。
*                      例如，当 {@code rate = 2} 时，将 [xi, xi+1]
*                      细分为两个等长区间。这等价于梯形法则。
* @param type          指定使用 CLOSED（封闭）还是 OPEN（开放）公式
* @param precision     所需精度，例如 {@code 1e-8}
* @param maxIterations 最大迭代次数
*/
public NewtonCotes(int rate, Type type, double precision, int maxIterations)
```

积分的准确性和性能不仅取决于使用哪种求积公式，还取决于被积函数本身。一般来说，梯形公式的准确性不如辛普森公式。低阶的牛顿-柯特斯公式计算简单、使用方便，且具有较高的精度。另一方面，高阶的牛顿-柯特斯公式不仅计算复杂，而且稳定性差。因此，它们很少被使用。在实践中，我们通常更喜欢使用像辛普森法则这样的低阶公式。



