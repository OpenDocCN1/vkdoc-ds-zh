# 8. 偏微分方程

偏微分方程（PDE）是在一个方程中将多元函数的值与其各个偏导数关联起来的等式。上一章讨论的常微分方程是偏微分方程的一个子类，因为常微分方程处理的是单变量函数。偏微分方程比常微分方程难解得多，因为一个简单的 PDE 就可能允许存在一大类解。例如，对于这个简单的 PDE：

```
$$ \frac{\partial²v}{\partial x\partial y}=0 $$
```

对于任意单变量函数 `f` 和 `g`，任何形如 `v(x, y) = f(x) + g(y)` 的函数都满足该方程。这远远超出了 ODE 解公式中通常允许自由选择一些数值或系数的范围。在 PDE 的研究中，通常可以自由选择函数。

一般来说，除了少数几类问题外，偏微分方程很少能解析求解。大多数 PDE 都没有通用的公式。因此，现代数学和科学研究中有大量关于用数值方法近似求解特定偏微分方程的研究。偏微分方程在数学、科学和工程学中无处不在。例如，它们是现代科学理解声音、热量、扩散、静电学、电动力学、流体动力学、空气动力学、弹性力学、广义相对论和量子力学的基础。许多纯数学领域的问题，如微分几何和变分法，也会引出偏微分方程。千禧年问题^(⁷)之一就是证明著名的偏微分方程——纳维-斯托克斯方程的存在性和唯一性。偏微分方程是一个非常庞大的主题。在本章中我们甚至无法浅尝辄止。我们将简要讨论偏微分方程的一个小子类（即二阶线性 PDE），并解释如何使用一种特定的数值方法（即有限差分法）来求解它们。

## 8.1 二阶线性 PDE

二阶 PDE 具有如下形式：

```
$$ a\left(\ast \right)\frac{\partial²u}{\partial {x}²}+b\left(\ast \right)\frac{\partial²u}{\partial x\partial y}+c\left(\ast \right)\frac{\partial²u}{\partial {y}²}+d\left(\ast \right)\frac{\partial u}{\partial x}+e\left(\ast \right)\frac{\partial u}{\partial y}+f\left(\ast \right)u+g\left(\ast \right)=0 $$
```

根据系数 `a(*)`、`b(*)`、`c(*)`、`d(*)`、`e(*)`、`f(*)` 和 `g(*)` 的函数形式，可以将其分为三类不同的类别。

如果系数仅依赖于变量而非导数，则该 PDE 是线性偏微分方程。即：

```
$$ \ast =x,y $$
```

如果系数依赖于变量和低阶导数，则该 PDE 是拟线性偏微分方程。即：

```
$$ \ast =x,y,\frac{\partial {u}^{n-1}}{\partial x},\frac{\partial {u}^{n-1}}{\partial y},\dots $$
```

如果系数依赖于最高阶导数，则该 PDE 是非线性偏微分方程。即：

```
$$ \ast =x,y,\frac{\partial {u}^n}{\partial x},\frac{\partial {u}^n}{\partial y},\dots $$
```

### 8.1.1 抛物型方程

如果满足以下条件，则二阶 PDE 是抛物型形式：

```
$$ {b}²\left(\ast \right)-a\left(\ast \right)c\left(\ast \right)=0 $$
```

抛物型 PDE 最简单且最著名的例子之一是一维热方程，如下所示：

```
$$ {u}_t=\frac{\partial u}{\partial t}=\alpha \frac{\partial²u}{\partial {x}²}=\alpha {u}_{xx} $$
```

`u(t, x)` 是时刻 `t` 在细杆上位置 `x` 处的温度。`α` 是热扩散率，是一个正常数。热方程大致阐述了热力学第二定律：热量会从较热的物体流向相邻较冷的物体，其流量与温差以及两者之间材料的热导率成正比。当热量流入（流出）一种材料时，其温度会按比例升高（降低），比例系数为热量除以材料质量，这个比例因子称为材料的比热扩散率。简单来说，给定时刻和位置处的温度升高或降低的速率与该点温度与其附近平均温度之差成正比。这个概念可以使用三维热方程推广，如下所示：

```
$$ {u}_t=\frac{\partial u}{\partial t}=\alpha \left(\frac{\partial²u}{\partial {x}²}+\frac{\partial²u}{\partial {y}²}+\frac{\partial²u}{\partial {z}²}\right)=\alpha\;\Delta u $$
```

`∆` 是拉普拉斯算子，它是 n 维欧几里得空间中的一个二阶微分算子，定义为二次可微实值函数的梯度的散度。它给出了某点邻域内函数平均值与该点函数值的差异。在当前示例中，它衡量了温度偏离调和函数均值性质的程度。

一个 PDE 可以代表一大类方程或现象。热方程具有非常广泛的适用性。金融数学中的布莱克-斯科尔斯方程（它起源于价值数万亿美元的期权和衍生品市场）就是热方程的一个变体。量子力学中的薛定谔方程可以被视为虚时间中的热方程。在图像分析中，热方程用于解决像素化问题和识别边缘。热方程的解在流体动力冲击的数学公式中非常有用。

与求解常微分方程一样，求解偏微分方程需要一定的初始条件、边界条件或约束条件来缩小范围。有三种典型的边界条件对应于三类边值问题。

第一类边值问题的边界条件是在函数定义域的边界上指定函数值本身。这称为狄利克雷边界条件。例如，如果一根铁棒的一端保持恒温，那么问题区域中该空间点的值（计算该区域的热量或温度）就是已知的。见图 8-1。

![img/528380_1_En_8_Fig1_HTML.png](img/528380_1_En_8_Fig1_HTML.png)

*一个长度为 l 的三维圆柱形状。横截面轴线已标出，纵向形状位于 X 轴上。所有内容都显示了初始温度分布。*

**图 8-1** 热方程的初始边值问题

从数学上，可以这样表示：

```
$$\left\{\begin{array}{l}\frac{\partial u}{\partial t}-a\frac{\partial²u}{\partial {x}²}=0,0<t<T,0<x<l\\ {}u\left(x,0\right)=\phi (x),0\le x\le l\\ {}u\left(0,t\right)={g}_1(t),u\left(l,t\right)={g}_2(t),0\le t\le T\end{array}\right.$$
```

第一个（微分）方程描述了热系统的动力学特性。第二个方程是初始条件，描述了杆内任意位置的初始温度。第三个方程是边界条件，指定了杆两端在任意时刻的温度。边界条件 `u(0, t) = g_1(t), u(l, t) = g_2(t)` 被称为第一类边界条件。`φ(x)`、`g_1(t)` 和 `g_2(t)` 是已知函数。它们也满足连接条件。

```
$$\left\{\begin{array}{c}\phi (0)={g}_1(0)\\ {}\phi (l)={g}_2(0)\end{array}\right.$$
```

第二类边界条件称为诺伊曼边界条件。它指定了函数在边界上的法向导数值。法向导数是沿空间某个表面法线（即正交）方向的方向导数。如果法线方向，即边界 `Γ` 的外法线方向，用 `n` 表示，则函数 `f` 的方向导数有时表示如下：

```
$$\frac{\partial f}{\partial \boldsymbol{n}}=\nabla f(x)\cdot \boldsymbol{n}=\frac{\partial f}{\partial \boldsymbol{x}}\cdot \boldsymbol{n}$$
```

一个例子是，如果铁杆的一端有一个加热器，那么能量将以恒定速率增加，但实际温度是未知的。

第三类边界条件也称为柯西边界条件。它同时指定了域边界上的函数值和法向导数值。这相当于施加了狄利克雷和诺伊曼边界条件。

形式上，第二类和第三类边界条件可以表示如下：

![$$\left\{\begin{array}{c}{\left[\frac{\partial u}{\partial x}-{\lambda}_1(t)u\right]}_{x=0}={g}_1(t)\\ {}{\left[\frac{\partial u}{\partial x}-{\lambda}_2(t)u\right]}_{x=l}={g}_1(t)\end{array}\right.$$](images/528380_1_En_8_Chapter/528380_1_En_8_Chapter_TeX_Equl.png)

当`λ_1(t) = λ_2(t) = 0`时，为第二类边界条件。当`λ_1(t) ≥ 0`且`λ_2(t) ≥ 0`时，为第三类边界条件。

## 8.1.2 双曲型方程

如果满足以下条件，则二阶偏微分方程为双曲型：

![$${b}²\left(\ast \right)-a\left(\ast \right)c\left(\ast \right)>0$$](img/528380_1_En_8_Chapter_TeX_Equm.png)

双曲型偏微分方程最简单也是最常见的例子是一维波动方程。

![$${u}_{tt}=\frac{\partial²u}{\partial {t}²}={c}²\frac{\partial²u}{\partial {x}²}={c}²{\nabla}²u$$](img/528380_1_En_8_Chapter_TeX_Equn.png)

双曲型方程的解是“波状”的。如果在双曲型微分方程的初始数据中产生一个扰动，那么并非空间中的每一点都会同时感受到该扰动。相对于固定的时间坐标，扰动具有有限的传播速度。它们沿着方程的特征线传播。这一特性从本质上将双曲型方程与抛物型偏微分方程和椭圆型偏微分方程区分开来。椭圆型或抛物型方程的初始（或边界）数据发生扰动时，域内几乎所有点都会立即感受到。参见图 8-2。

![](img/528380_1_En_8_Fig2_HTML.jpg)

一条曲线开始时平坦，急剧上升到峰值，然后急剧下降，最后变直。

**图 8-2** 传播脉冲可以用波动方程建模

仅波动方程本身并不能确定一个具体的物理解；通常需要通过设置带有额外条件的问题来获得唯一解，例如规定波振幅和相位的初始条件。另一类重要问题出现在由边界条件限定的封闭空间中，其解代表驻波或谐波，类似于乐器的谐波。

双曲型方程的初值问题如下：

![$$\left\{\begin{array}{c}\frac{\partial²u}{\partial {t}²}={c}²\frac{\partial²u}{\partial {x}²},t>0\\ {}u\left(x,0\right)=\phi (x)\\ {}{\left.\frac{\partial u}{\partial t}\right|}_{t=0}=\psi (x)\end{array}\right.$$](img/528380_1_En_8_Chapter_TeX_Equo.png)

可以证明，该方程具有如下性质：如果`u`及其一阶时间导数在直线`t = 0`上由初始数据任意指定（且具有足够的光滑性），则对于所有时间`t`都存在一个解。

边值问题如下：

![$$\left\{\begin{array}{c}\frac{\partial²u}{\partial {t}²}={c}²\frac{\partial²u}{\partial {x}²},t>0\\ {}{\left.u\left(x,0\right)=\phi (x),\frac{\partial u}{\partial t}\right|}_{t=0}=\psi (x)\kern0.5em \\ {}u\left(0,t\right)={g}_1(t),u\left(l,t\right)={g}_2(t)\end{array}\right.$$](img/528380_1_En_8_Chapter_TeX_Equp.png)

最后一组边界条件是第一类边界条件。

## 8.1.3 椭圆型方程

如果满足以下条件，则二阶偏微分方程为椭圆型：

![$${b}²\left(\ast \right)-a\left(\ast \right)c\left(\ast \right)<0$$](img/528380_1_En_8_Chapter_TeX_Equq.png)

最简单的形式是泊松方程：

![$$\Delta u={\nabla}²u=\frac{\partial²u}{\partial {x}²}+\frac{\partial²u}{\partial {y}²}=f\left(x,y\right)$$](img/528380_1_En_8_Chapter_TeX_Equr.png)

特别地，当`f(x, y) = 0`时，为拉普拉斯方程，也称为调和方程：

![$$\Delta u=\frac{\partial²u}{\partial {x}²}+\frac{\partial²u}{\partial {y}²}=0$$](img/528380_1_En_8_Chapter_TeX_Equs.png)

例如，联系标量势`φ`和吸引质量`ρ`的引力泊松方程如下：

![$${\nabla}²\phi =4\pi G\rho$$](img/528380_1_En_8_Chapter_TeX_Equt.png)

泊松方程的第一类边值问题如下：

![$$\left\{\begin{array}{c}\frac{\partial²u}{\partial {x}²}+\frac{\partial²u}{\partial {y}²}=f\left(x,y\right),\left(x,y\right)\in \Omega \\ {}{\left.u\left(x,y\right)\right|}_{\left(x,y\right)\in \Gamma}=\phi \left(x,y\right),\Gamma =\partial \varOmega \end{array}\right.$$](img/528380_1_En_8_Chapter_TeX_Equu.png)

`Ω`是求解域或区域；`Γ`是`Ω`的边界（或边缘）。`f(x, y)`和`φ(x, y)`是已知的、在`Ω ∪ Γ`上的连续函数。`u(x, y)`是您要求解的函数。

第二类和第三类边界条件可以表示如下：

![$${\left.\left(\frac{\partial u}{\partial n}+\alpha u\right)\right|}_{\left(x,y\right)\in \Gamma}=\phi \left(x,y\right)$$](img/528380_1_En_8_Chapter_TeX_Equv.png)

当`α = 0`时，为第二类边界条件。当`α ≠ 0`时，为第三类边界条件。

# 8.2 有限差分法

计算偏微分方程数值解主要有三种数值方法：有限元法、有限体积法和有限差分法。截至本书撰写时，NM Dev 仅支持有限差分法，也称为网格法。

我们在第 6 章和第 7 章中介绍了有限差分法的基本概念。其核心思想相同，只是需要将其扩展到更高维度，以处理包含多个变量的偏微分方程。以下是步骤总结：

1. 在解域上施加一个网格或栅格。
2. 用一组有限的、离散的节点（网格节点）替换自变量的连续变量域。
3. 在网格节点上定义函数值。
4. 用差商替换网格节点上的导数值。
5. 偏微分方程现在简化为一个包含有限数量未知数的代数方程组，即差分方程。
6. 分析收敛性并估计误差。
7. 如果差分方程组有解，并且当网格无限小时，该解收敛于原始微分方程的解，那么差分方程的解就是原始问题的近似解（或数值解）。

## 8.2.1 双曲型方程的数值解

考虑以下一维波动方程：

![$$ \frac{\partial²u}{\partial {t}²}={c}²\frac{\partial²u}{\partial {x}²} $$](img/528380_1_En_8_Chapter_TeX_Equw.png)

两端边界固定为 0。即：

![$$ u\left(t,0\right)=u\left(t,a\right)=0 $$](img/528380_1_En_8_Chapter_TeX_Equx.png)

两个初始条件，即 `u` 及其在 `t` = 0 时的一阶时间导数 `u[t]`，如下所示：

![$$ \left\{\begin{array}{c}u\left(0,x\right)=f(x),0<x<a\\ {}{u}_t\left(0,x\right)=g(x),0<x<a\end{array}\right. $$](img/528380_1_En_8_Chapter_TeX_Equy.png)

在 NM Dev 中，`WaveEquation1D` 类可用于构建此类一维波动方程。其签名如下：

```
/**
* 构造一个一维波动方程。
*
* @param c2 方程中的波动系数 c²
* @param T  关注的时间段 (0, T)
* @param a  关注的区域 (0, a)
* @param f  u 的初始条件，即 u(0, x)
* @param g  u 的时间导数的初始条件，即 ut(0,x)
*/
public WaveEquation1D(
double c2,
double T,
double a,
UnivariateRealFunction f,
UnivariateRealFunction g)
```

由于时间导数和空间导数都是二阶的，我们使用中心差分来逼近它们。设时间步长为 `∆t`，空间步长为 `∆x`。记 `u_j^k = u(t_k, x_j)` 为网格点 (`k`, `j`) 处的近似值。差分公式如下：

![$$ \frac{u_j^{k+1}-2{u}_j^k+{u}_j^{k-1}}{{\left(\Delta t\right)}²}={c}²\frac{u_{j+1}^k-2{u}_j^k+{u}_{j-1}^k}{{\left(\Delta x\right)}²} $$](img/528380_1_En_8_Chapter_TeX_Equz.png)

令

![$$ s={c}²\frac{{\left(\Delta t\right)}²}{{\left(\Delta x\right)}²} $$](img/528380_1_En_8_Chapter_TeX_Equaa.png)

解出 `u_j^{k+1}` 可得：

![$$ {u}_j^{k+1}=s\left({u}_{j+1}^k+{u}_{j-1}^k\right)+2\left(1-s\right){u}_j^k-{u}_j^{k-1} $$](img/528380_1_En_8_Chapter_TeX_Equab.png)

请注意，要计算每个 `u_j^{k+1}`，需要使用前两个时间步的四个节点。因此，这是一种求解 `u_j^k = u(t_k, x_j)` 数值解的显式格式。

作为一种显式格式，它不是无条件稳定的。可以证明，只有当满足以下条件时，该格式才是稳定的：

![$$ s={c}²\frac{{\left(\Delta t\right)}²}{{\left(\Delta x\right)}²}\le 1 $$](img/528380_1_En_8_Chapter_TeX_Equac.png)

如果将 `∆t/∆x` 定义为此格式的速度，那么稳定性条件意味着该格式的速度必须至少与波速 `c`（精确解的速度）一样大：

![$$ \Delta x\ge c\Delta t $$](img/528380_1_En_8_Chapter_TeX_Equad.png)

此方法称为中心差分法，是一种二阶方法，其截断误差阶数为 `O((∆t)² + (∆x)²)`。参见图 8-3：

![A graph of x versus t displays 16 different nodes with different points at the same interval.](img/528380_1_En_8_Fig3_HTML.jpg)

**图 8-3** 求解一维波动方程的显式格式

以下 NM Dev 代码在区域 `[0, 1] × [0, 2]` 内求解波动方程，其中 `c²` = 4，并使用以下初始条件：

![$$ \left\{\begin{array}{c}u\left(0,x\right)=0.1\sin \left(\pi x\right)\\ {}{u}_t\left(0,x\right)=0.2\pi \sin \left(\pi x\right)\end{array}\right. $$](img/528380_1_En_8_Chapter_TeX_Equae.png)

# 示例代码与输出

## 代码实现

下面是一个 Kotlin 示例，演示如何使用 NM Dev 库求解一维波动方程并计算特定点上的解。

```kotlin
val f1: UnivariateRealFunction = object : AbstractUnivariateRealFunction() {
override fun evaluate(x: Double): Double {
return 0.1 * sin(PI * x) // 0.1 * sin(π x)
}
}
val f2: UnivariateRealFunction = object : AbstractUnivariateRealFunction() {
override fun evaluate(x: Double): Double {
return 0.2 * PI * sin(PI * x) // 0.2π * sin(π x)
}
}
val c2: Double = 4.0 // c²
val T: Double = 1.0 // 时间上界
val a: Double = 2.0 // x 方向上界
val pde: WaveEquation1D = WaveEquation1D(c2, T, a, f1, f2)
val m: Int = 80 // dt = T/m
val n: Int = 39 // dx = a/n
val soln: PDESolutionTimeSpaceGrid1D = ExplicitCentralDifference1D().solve(pde, m, n)
var t: Int = 0 // 时间索引
var x: Int = 1 // x 索引
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 0
x = 16
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 0
x = 31
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 20
x = 1
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 20
x = 16
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 20
x = 31
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 40
x = 1
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 40
x = 16
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 40
x = 31
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 60
x = 1
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 60
x = 16
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 60
x = 31
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
```

## 输出结果

```
u(0,1) = 0.015643
u(0,16) = 0.058779
u(0,31) = -0.098769
u(20,1) = 0.015691
u(20,16) = 0.058956
u(20,31) = -0.099066
u(40,1) = -0.015605
u(40,16) = -0.058636
u(40,31) = 0.098529
u(60,1) = -0.015728
u(60,16) = -0.059098
u(60,31) = 0.099305
```

## `WaveEquation1D` 类

`WaveEquation1D` 类用于构造一维波动方程偏微分方程。你需要指定解域和初始边界条件。其签名如下：

```java
/**
* 构造一个一维波动方程。
*
* @param beta 方程中的波系数 β
* @param T    感兴趣的时间区间 (0, T)
* @param a    感兴趣的空间区间 (0, a)
* @param f    关于 u 的初始条件，即 u(0, x)
* @param g    关于 u 的时间导数的初始条件，
*             即 ut(0,x)
*/
public WaveEquation1D(
double beta,
double T,
double a,
UnivariateRealFunction f,
UnivariateRealFunction g)
```

## `ExplicitCentralDifference1D` 求解器

`ExplicitCentralDifference1D` 是一个 PDE 求解器，使用中心差分法求解一维波动方程。其签名如下：

```java
/**
* 用给定的解网格分辨率参数求解一维波动方程。
*
* @param pde 波动方程问题
* @param m   时间轴上的网格点数
* @param n   空间轴上的网格点数
* @return 解网格
*/
public PDESolutionTimeSpaceGrid1D solve(
WaveEquation1D pde,
final int m,
final int n
)
```

## `PDESolutionTimeSpaceGrid1D` 接口

解由 `PDESolutionTimeSpaceGrid1D` 对象表示。它适用于那些将解表示为时间和空间网格的方法。其签名如下：

```java
public interface PDESolutionTimeSpaceGrid1D {
/**
* 获取网格点 (tm, xn) 处的值。
*
* @param m 时间轴上的索引
* @param n 空间轴上的索引
* @return 该点的值
*/
double u(int m, int n);
/**
* 获取索引为 {@code k} 的时间轴上的值。
*
* @param m 网格点的索引
* @return tm
*/
double t(int m);
/**
* 获取索引为 {@code j} 的空间轴上的值。
*
* @param n 网格点的索引
* @return xn
*/
double x(int n);
/**
* 获取解中内部时间轴网格点的数量。
*
* @return 时间轴上的解网格点数量
*/
int M();
/**
* 获取解中内部空间轴网格点的数量。
*
* @return 空间轴上的解网格点数量
*/
int N();
}
```

## 扩展到二维波动方程

NM Dev 支持求解高达二维的波动方程。二维波动方程问题定义如下：

![$$ \frac{\partial²u}{\partial {t}²}={c}²\left(\frac{\partial²u}{\partial {x}²}+\frac{\partial²u}{\partial {y}²}\right) $$](img/528380_1_En_8_Chapter_TeX_Equaf.png)

两个边界端点均固定为 0，即：

![$$ u\left(t,0,0\right)=u\left(t,a,0\right)=u\left(t,0,b\right)=u\left(t,a,b\right)=0 $$](img/528380_1_En_8_Chapter_TeX_Equag.png)

两个初始条件，即 *u* 及其在 *t* = 0 时的一阶时间导数 *u*[*t*]，如下所示：

![$$ \left\{\begin{array}{c}u\left(0,x,y\right)=f\left(x,y\right),0<x<a,0<y<b\\ {}{u}_t\left(0,x,y\right)=g\left(x,y\right),0<x<a,0<y<b\end{array}\right. $$](img/528380_1_En_8_Chapter_TeX_Equah.png)

在 NM Dev 中，`WaveEquation2D` 类可用于构造这样的二维波动方程。其签名如下：

# 二维波动方程

## 构造函数

```java
/**
* 构造一个二维波动方程。
*
* @param c2 方程中的 c²
* @param T  感兴趣的时间区间 (0, T)
* @param a  沿 x 轴的区域大小，x ∈ (0, a)
* @param b  沿 y 轴的区域大小，y ∈ (0, b)
* @param f  关于 u 的初始条件，即 u(0, x, y)
* @param g  关于 u 的时间导数的初始条件，
*           即 ut(0,x,y)
*/
public WaveEquation2D(
double c2,
double T,
double a,
double b,
BivariateRealFunction f,
BivariateRealFunction g
)
```

## 中心差分格式

二维中心差分格式如下：

![$$ \frac{u_{ij}^{k+1}-2{u}_{ij}^k+{u}_{ij}^{k-1}}{{\left(\Delta t\right)}²}={c}²\left(\frac{u_{i+1j}^k-2{u}_{ij}^k+{u}_{i-1j}^k}{{\left(\Delta x\right)}²}+\frac{u_{ij+1}^k-2{u}_{ij}^k+{u}_{ij-1}^k}{{\left(\Delta y\right)}²}\right) $$](img/528380_1_En_8_Chapter_TeX_Equai.png)

### 稳定条件

该格式在满足以下条件时是稳定的：

![$$ \frac{{\left(\Delta x\right)}²+{\left(\Delta y\right)}²}{{\left(\Delta t\right)}²}\ge 4{c}² $$](img/528380_1_En_8_Chapter_TeX_Equaj.png)

### 截断误差

截断误差的阶数为 `O((Δt)² + (Δx)² + (Δy)²)`。

## 代码示例

例如，以下 NM Dev 代码在区域 `[0, 2] × [0, 2] × [0, 2]` 中求解了二维波动方程，其中 `c²=1/4`，初始条件如下：

```kotlin
println("求解二维波动方程")
val f1: BivariateRealFunction = object : AbstractBivariateRealFunction() {
override fun evaluate(x: Double,y: Double): Double {
return 0.1 * sin(PI * x) * sin(PI * y / 2.0)
}
}
val f2: BivariateRealFunction = object : AbstractBivariateRealFunction() {
override fun evaluate(x: Double, y: Double): Double {
return 0.0
}
}
val c2: Double = 1.0 / 4 // 波速平方
val T: Double = 2.0
val a: Double = 2.0
val b: Double = 2.0 // 解域边界
val pde: WaveEquation2D = WaveEquation2D(
c2, T, a, b, f1, f2)
val m: Int = 40 // dt = T/m
val n: Int = 39 // dx = a/n
val p: Int = 39 // dy = b/p
val soln: PDESolutionTimeSpaceGrid2D = ExplicitCentralDifference2D().solve(pde, m, n, p)
var t: Int = 40 // 时间索引
var x: Int = 1 // x 索引
var y: Int = 1 // y 索引
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 40 // 时间索引
x = 1 // x 索引
y = 16 // y 索引
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 40 // 时间索引
x = 1 // x 索引
y = 31 // y 索引
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 40 // 时间索引
x = 16 // x 索引
y = 1 // y 索引
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 40 // 时间索引
x = 16 // x 索引
y = 16 // y 索引
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 40 // 时间索引
x = 16 // x 索引
y = 31 // y 索引
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 40 // 时间索引
x = 31 // x 索引
y = 1 // y 索引
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 40 // 时间索引
x = 31 // x 索引
y = 16 // y 索引
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 40 // 时间索引
x = 31 // x 索引
y = 31 // y 索引
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
```

输出如下：

```
u(40,1,1) = -0.001145
u(40,1,16) = -0.013877
u(40,1,31) = -0.009476
u(40,16,1) = -0.004302
u(40,16,16) = -0.052141
u(40,16,31) = -0.035606
u(40,31,1) = 0.007228
u(40,31,16) = 0.087616
u(40,31,31) = 0.059830
```

## 求解器

`ExplicitCentralDifference2D` 是一个偏微分方程求解器，使用中心差分法求解二维波动方程。其签名如下：

```java
/**
* 求解二维波动方程，并指定求解网格的分辨率参数。
*
* @param pde 波动方程问题
* @param m   沿时间轴的网格点数（不包括初始条件）
* @param n   沿 x 轴的网格点数（不包括两个边界）
* @param p   沿 y 轴的网格点数（不包括两个边界）
* @return 解网格
*/
public PDESolutionTimeSpaceGrid2D solve(
WaveEquation2D pde,
final int m,
final int n,
final int p
)
```

### 解的表示

解由 `PDESolutionTimeSpaceGrid2D` 对象表示。它适用于将解生成为时间与空间网格的方法。其签名如下：

```java
public interface PDESolutionTimeSpaceGrid2D {
/**
* 获取网格点 (tk, xi, yj) 处的值。
*
* @param k 沿时间轴的索引
* @param i 沿 x 轴的索引
* @param j 沿 y 轴的索引
* @return 网格点的值
*/
double u(int k, int i, int j);
/**
* 获取索引为 @{code k} 的时间轴上的值。
*
* @param k 网格点的索引
* @return tk
*/
double t(int k);
/**
* 获取索引为 @{code i} 的 x 轴上的值。
*
* @param i 网格点的索引
* @return xi
*/
double x(int i);
/**
* 获取索引为 @{code j} 的 y 轴上的值。
*
* @param j 网格点的索引
* @return yj
*/
double y(int j);
/**
* 获取解中内部时间轴网格点的数量。
*
* @return 沿时间轴的解网格点数量
*/
int m();
/**
* 获取解中内部 x 轴网格点的数量。
*
* @return 沿 x 轴的解网格点数量
*/
int n();
/**
* 获取解中内部 y 轴网格点的数量。
*
* @return 沿 y 轴的解网格点数量
*/
int p();
}
```

## 8.2.2 椭圆型方程的数值解

考虑泊松方程的第一类边值问题（见图 8-4），如下所示：

![$$ \left\{\begin{array}{c}\frac{\partial²u}{\partial {x}²}+\frac{\partial²u}{\partial {y}²}=f\left(x,y\right),\left(x,y\right)\in \Omega \\ {}{\left.u\left(x,y\right)\right|}_{\left(x,y\right)\in \Gamma}=\phi \left(x,y\right),\Gamma =\partial\ \Omega \end{array}\right. $$](img/528380_1_En_8_Chapter_TeX_Equak.png)

![](img/528380_1_En_8_Fig4_HTML.jpg)

*图 8-4 二维区域上的网格*

取 `h` 和 `τ` 分别为 `x` 和 `y` 方向上的步长。网格内的节点记为 `{(x[k], y[j]) | x[k] = kh, y[j] = jτ, k, j ∈ Z}`，`(k, j = 0, ±1, ±2, ⋯)`。解域内部的节点称为内部节点。它们属于 `Ω ∪ Γ`。对于每个节点，有四个相邻节点，分别沿 `x` 和 `y` 方向相距一个步长，`(k, j = ±1, ±2)`。如果一个节点的所有四个相邻节点都是内部节点，则该节点称为正则内部节点。正则内部节点集记为 `Ω^((1))`。如果某个相邻节点不是内部节点，则该节点是非正则内部节点。非正则内部节点集记为 `Ω^((2))`。边界 `Γ` 与网格线的交点称为边界节点。偏微分方程的数值解需要确定函数 `u` 在所有内部节点上的值。

为简化记法，记 `f_j^k = f(x_k, y_j)`。对于正则内部节点 `(k, j) ∈ Ω^((1))`，二阶中心差商公式如下：

![$$ {\left.\frac{\partial²u}{\partial {x}²}\right|}_{\left(k,j\right)}=\frac{\frac{u_j^{k+1}-{u}_j^k}{h}-\frac{u_j^k-{u}_j^{k-1}}{h}}{h}-\frac{h²}{12}{u}_{x⁴}^{(4)}\left({x}_k+{\theta}_1h,{y}_j\right)=\frac{u_j^{k+1}-2{u}_j^k+{u}_j^{k-1}}{h²}-\frac{h²}{12}{u}_{x⁴}^{(4)}\left({x}_k+{\theta}_1h,{y}_j\right) $$](img/528380_1_En_8_Chapter_TeX_Equal.png)

![$$ {\left.\frac{\partial²u}{\partial {y}²}\right|}_{\left(k,j\right)}=\frac{u_{j+1}^k-2{u}_j^k+{u}_{j-1}^k}{\tau²}-\frac{\tau²}{12}{u}_{y⁴}^{(4)}\left({x}_k,{y}_j+{\theta}_2\tau \right) $$](img/528380_1_En_8_Chapter_TeX_Equam.png)

其中 `0 < θ[1], θ[2] < 1`。

泊松方程 `∂²u/∂x² + ∂²u/∂y² = f(x, y)` 在节点 `(k, j)` 处可表示为：

![$$ \frac{u_j^{k+1}-2{u}_j^k+{u}_j^{k-1}}{h²}+\frac{u_{j+1}^k-2{u}_j^k+{u}_{j-1}^k}{\tau²}={f}_j^k+R\left(k,j\right) $$](img/528380_1_En_8_Chapter_TeX_Equan.png)

截断误差为：

![$$ R\left(k,j\right)=\frac{h²}{12}{u}_{x⁴}^{(4)}\left({x}_k+{\theta}_1h,{y}_j\right)+\frac{\tau²}{12}{u}_{x⁴}^{(4)}\left({x}_k,{y}_j+{\theta}_2\tau \right)=O\left({h}²+{\tau}²\right) $$](img/528380_1_En_8_Chapter_TeX_Equao.png)

忽略误差项 `R(k, j)`，泊松方程的差分逼近式为：

![$$ \frac{u_j^{k+1}-2{u}_j^k+{u}_j^{k-1}}{h²}+\frac{u_{j+1}^k-2{u}_j^k+{u}_{j-1}^k}{\tau²}={f}_j^k $$](img/528380_1_En_8_Chapter_TeX_Equap.png)

差分方程的数量等于正则内部节点 `u[k, j]` 的数量。每个差分方程不仅包含正则内部节点处解的近似值 `u[k, j]`，还包含非正则内部节点的近似值。因此，方程的数量小于待求解未知数的数量。非正则内部节点处的近似值由边界条件给出。处理边界条件有多种方案。接下来我们描述两种较简单的方案。

### 直接传递

将最接近非正则内部节点的边界节点集合中的 `u` 值，用作该节点处 `u[k, j]` 值的近似值。我们称此为边界条件的直接传递。

例如，假设节点 `P(k, j)` 是一个非正则内部节点，且最接近的边界节点是 `Q` 节点，那么有如下公式：

![$$ {u}_j^k=u(Q)=\phi (Q),\left(k,j\right)\in {\Omega}^{(2)} $$](img/528380_1_En_8_Chapter_TeX_Equaq.png)

该公式可视为节点的零阶插值。它很简单，截断误差为 `O(h + τ)`。使用上述替换后，方程数量等于未知数数量。现在可以求解该线性方程组。

### 线性插值

该方案通过线性插值边界节点 `Q` 和与节点 `P` 位于同一网格线上相邻的内部节点 `R`，来近似非正则内部节点 `P(k, j)` 处的 `u[k, j]` 值。即：

![$$ {u}_j^k=\frac{h}{h+d}\phi (Q)+\frac{d}{h+d}\phi (R) $$](img/528380_1_En_8_Chapter_TeX_Equar.png)

其中 `d = |QR|`，截断误差为 `O(h²)`。使用上述替换后，方程数量等于未知数数量。现在可以求解该线性方程组。

注意，每个差分方程涉及五个节点：`{u_j^(k+1), 2u_j^k, u_j^(k-1), u_(j+1)^k, u_(j-1)^k}`。这被称为网格中节点的五点模板或五点菱形格式。

![$$ \frac{u_j^{k+1}-2{u}_j^k+{u}_j^{k-1}}{h²}+\frac{u_{j+1}^k-2{u}_j^k+{u}_{j-1}^k}{\tau²}={f}_j^k $$](img/528380_1_En_8_Chapter_TeX_Equas.png)

在实践中，通常设置 `h = τ`。此时五点菱形格式简化为：

![$$ \frac{1}{h²}\left({u}_j^{k+1}+{u}_j^{k-1}+{u}_{j+1}^k+{u}_{j-1}^k-4{u}_j^k\right)={f}_j^k $$](img/528380_1_En_8_Chapter_TeX_Equat.png)

它缩写为：`1/h² ◇ u_j^k = f_j^k`，其中 `◇ u_j^k = u_j^(k+1) + u_j^(k-1) + u_(j+1)^k + u_(j-1)^k - 4u_j^k`。参见图 8-5。

![图 8-5 网格中节点的五点模板](img/528380_1_En_8_Fig5_HTML.jpg)

*图 8-5 网格中节点的五点模板*

例如，考虑使用五点菱形格式求解如下拉普拉斯方程的第一类边值问题：

![$$ \left\{\begin{array}{l}\frac{\partial²u}{\partial {x}²}+\frac{\partial²u}{\partial {y}²}=0\\ {}{\left.u\left(x,y\right)\right|}_{\Gamma}=\log \left[{\left(1+x\right)}²+{y}²\right]\end{array}\right. $$](images/528380_1_En_8_Chapter/528380_1_En_8_Chapter_TeX_Equav.png)

![$$ \varOmega =\left\{\left(x,y\right)\left|0\le x,y\le 1\right.\right\} $$](img/528380_1_En_8_Chapter_TeX_Equaw.png)

取 `h = τ = 1/3`。网格中有四个内部节点（红色），均为正则内部节点。根据五点菱形格式，线性方程组如下：

![$$ \left\{\begin{array}{c}\frac{1}{h²}\left({u}_1²+{u}_1⁰+{u}_2¹+{u}_0¹-4{u}_1¹\right)=0\\ {}\frac{1}{h²}\left({u}_1³+{u}_1¹+{u}_2²+{u}_0²-4{u}_1²\right)=0\\ {}\frac{1}{h²}\left({u}_2²+{u}_2⁰+{u}_3¹+{u}_1¹-4{u}_2¹\right)=0\\ {}\frac{1}{h²}\left({u}_2³+{u}_2¹+{u}_3²+{u}_1²-4{u}_2²\right)=0\end{array}\right. $$](img/528380_1_En_8_Chapter_TeX_Equax.png)

边界值（蓝色）可从`u(x, y)|Γ`直接计算得出，如下所示：

![$$ \left\{\begin{array}{c}{u}_0¹=\log \frac{16}{9}, {u}_0²=\log \frac{25}{9}\\ {}{u}_1⁰=\log \frac{10}{9}, {u}_2⁰=\log \frac{13}{9}\\ {}{u}_3¹=\log \frac{25}{9}, {u}_3²=\log \frac{34}{9}\\ {}{u}_1³=\log \frac{37}{9}, {u}_2³=\log \frac{40}{9}\end{array}\right. $$](img/528380_1_En_8_Chapter_TeX_Equay.png)

该线性方程组的解如下所示（参见图 8-6）：

![$$ \left\{\begin{array}{l}{u}_1¹=0.634804\\ {}{u}_1²=1.059992\\ {}{u}_2¹=0.798500\\ {}{u}_2²=1.169821\end{array}\right. $$](img/528380_1_En_8_Chapter_TeX_Equaz.png)

![](img/528380_1_En_8_Fig6_HTML.jpg)

一张 `y` 相对于 `x` 的图表显示了 16 个不同节点，这些节点在相同间隔处位于不同点上。

### 图 8-6 网格与拉普拉斯方程的解

以下 NM Dev 代码求解了该示例：

```kotlin
val ZERO: BivariateRealFunction // 常量零函数，f = 0
= object : AbstractBivariateRealFunction() {
override fun evaluate(x: Double, y: Double): Double {
return 0.0
}
override fun evaluate(x: Vector): Double {
return 0.0
}
}
// 边界条件
val g: BivariateRealFunction = object : AbstractBivariateRealFunction() {
override fun evaluate(x: Double, y: Double): Double {
return ln((1.0 + x) * (1.0 + x) + y * y)
}
}
val a: Double = 1.0 // x 维度宽度
val b: Double = 1.0 // y 维度高度
val pde: PoissonEquation2D = PoissonEquation2D(a, b, ZERO, g)
val solver: IterativeCentralDifference = IterativeCentralDifference(
1e-8, // 精度
40) // 最大迭代次数
val soln: PDESolutionGrid2D = solver.solve(pde, 2, 2)
var k: Int = 1
var j: Int = 1 // 节点索引
val u_11: Double = soln.u(k, j) // x = 0.3, y = 0.3
println(String.format("u_%d,%d = u(%f,%f): %f", k, j, soln.x(k), soln.y(j), u_11))
k = 1
j = 2
val u_12: Double = soln.u(k, j) // x = 0.3, y = 0.6
println(String.format("u_%d,%d = u(%f,%f): %f", k, j, soln.x(k), soln.y(j), u_12))
k = 2
j = 1
val u_21: Double = soln.u(k, j) // x = 0.6, y = 0.3
println(String.format("u_%d,%d = u(%f,%f): %f", k, j, soln.x(k), soln.y(j), u_21))
k = 2
j = 2
val u_22: Double = soln.u(k, j) // x = 0.6, y = 0.6
println(String.format("u_%d,%d = u(%f,%f): %f", k, j, soln.x(k), soln.y(j), u_22))
```

输出结果如下：

```
u_1,1 = u(0.333333,0.333333): 0.634804
u_1,2 = u(0.333333,0.666667): 0.798500
u_2,1 = u(0.666667,0.333333): 1.059992
u_2,2 = u(0.666667,0.666667): 1.169821
```

要建立一个泊松偏微分方程问题，首先需要创建一个 `PoissonEquation2D` 对象。其签名如下：

```java
/**
 * 构建一个泊松方程问题。
 *
 * @param a 感兴趣的区域 [0, a]
 * @param b 感兴趣的区域 [0, b]
 * @param f 方程中的强迫项 f(x, y)
 * @param g 狄利克雷边界条件 g(x, y)
 */
public PoissonEquation2D(
    double a,
    double b,
    BivariateRealFunction f,
    BivariateRealFunction g)
```

`f` 是泊松方程右侧的项。当 `f` = 0 时，方程变为拉普拉斯方程。`g` 指定了偏微分方程问题的第一类边界条件。`PoissonEquation2D` 类的求解域为 [0, `a`] × [0, `b`]。若需求解其他区域范围，用户需在编码前进行变换。

`IterativeCentralDifference` 是一个偏微分方程求解器，用于通过中心差分公式求解泊松方程问题。其签名如下：

```java
/**
 * 使用给定的精度作为收敛判据和允许的最大迭代次数，构造此方法的一个实例。
 * 当连续两次迭代之间的差值在给定精度范围内时，迭代停止。
 *
 * @param precision     收敛阈值
 * @param maxIterations 允许的最大迭代次数
 */
public IterativeCentralDifference(
    double precision,
    int maxIterations
) {
/**
 * 求解泊松方程问题，并指定网格分辨率参数。
 *
 * 当迭代在允许的最大迭代次数内无法收敛至指定的误差限时，
 * 将抛出 {@link UnsatisfiableErrorCriterionException}。
 *
 * @param pde 泊松方程
 * @param m   网格沿 x 轴的内点数
 * @param n   网格沿 y 轴的内点数
 * @return 求解结果网格
 */
public PDESolutionGrid2D solve(
    final PoissonEquation2D pde,
    final int m,
    final int n
)
```

在 NM Dev 中，使用有限差分法求解二维 PDE 问题会生成一个 `PDESolutionGrid2D` 对象。它包含关于网格、节点以及每个节点上近似值的所有信息。其签名如下：

# 一个双变量 PDE 的解

一个双变量 PDE 的解，适用于通过二维网格形式生成解的方法。索引的范围分别为 0 到 (m+1) 和 0 到 (n+1)。

```java
/**
* 一个双变量 PDE 的解，适用于通过二维网格形式生成解的方法。
* 索引的范围分别为 0 到 (m+1) 和 0 到 (n+1)。
*
*/
public interface PDESolutionGrid2D {
    /**
    * 获取位于 (xk, yj) 处网格点的值。
    *
    * @param k 沿 x 轴的索引
    * @param j 沿 y 轴的索引
    * @return 该点的值
    */
    double u(int k, int j);
    /**
    * 获取索引 {@code k} 处 x 轴上的值。
    *
    * @param k 网格点的索引
    * @return xk
    */
    double x(int k);
    /**
    * 获取索引 {@code j} 处 y 轴上的值。
    *
    * @param j 网格点的索引
    * @return yj
    */
    double y(int j);
    /**
    * 获取解中 x 轴上的内部网格点数量。
    *
    * @return 沿 x 轴解网格点的数量
    */
    int m();
    /**
    * 获取解中 y 轴上的内部网格点数量。
    *
    * @return 沿 y 轴解网格点的数量
    */
    int n();
}
```

请注意，`double u(int k, int j)` 函数接收的是节点索引，而不是域值作为输入。`PDESolutionGrid2D` 仅包含网格节点处的信息。如果你需要函数在非网格节点处的近似值，则需要进行一些插值。

作为另一个示例，以下 NM Dev 代码求解了在 0 ≤ `x` ≤ 0.5，0 ≤ `y` ≤ 0.5 范围内具有初始条件和边界条件的拉普拉斯方程：

![$$ \left\{\begin{array}{c}u\left(0,y\right)=u\left(x,0\right)=0\\ {}u\left(x,0.5\right)=200x\\ {}u\left(0.5,y\right)=200y\end{array}\right. $$](img/528380_1_En_8_Chapter_TeX_Equba.png)

```kotlin
val ZERO: BivariateRealFunction // 一个恒零函数，f = 0
= object : AbstractBivariateRealFunction() {
override fun evaluate(x: Double, y: Double): Double {
return 0.0
}
override fun evaluate(x: Vector): Double {
return 0.0
}
}
// 边界条件
val EPSION: Double = 1e-8
val g: BivariateRealFunction = object : AbstractBivariateRealFunction() {
override fun evaluate(x: Double, y: Double): Double {
if (DoubleUtils.isZero(x, EPSION) || DoubleUtils.isZero(y, EPSION)) {
return 0.0
} else if (DoubleUtils.equal(x, 0.5, EPSION)) {
return 200.0 * y
} else if (DoubleUtils.equal(y, 0.5, EPSION)) {
return 200.0 * x
}
// 不可达，无关紧要
return Double.NaN
}
}
val a: Double = 0.5 // x 轴方向的宽度
val b: Double = 0.5 // y 轴方向的高度
val pde: PoissonEquation2D = PoissonEquation2D(a, b, ZERO, g)
val solver: IterativeCentralDifference = IterativeCentralDifference(
EPSION, // 精度
40) // 最大迭代次数
val soln: PDESolutionGrid2D = solver.solve(pde, 4, 4)
var k: Int = 1
var j: Int = 1 // 节点索引
val u_11: Double = soln.u(k, j)
println(String.format("u_%d,%d = u(%f,%f): %f", k, j, soln.x(k), soln.y(j), u_11))
k = 1
j = 2
val u_12: Double = soln.u(k, j)
println(String.format("u_%d,%d = u(%f,%f): %f", k, j, soln.x(k), soln.y(j), u_12))
k = 2
j = 1
val u_21: Double = soln.u(k, j)
println(String.format("u_%d,%d = u(%f,%f): %f", k, j, soln.x(k), soln.y(j), u_21))
k = 2
j = 2
val u_22: Double = soln.u(k, j)
println(String.format("u_%d,%d = u(%f,%f): %f", k, j, soln.x(k), soln.y(j), u_22))
k = 3
j = 3
val u_33: Double = soln.u(k, j)
println(String.format("u_%d,%d = u(%f,%f): %f", k, j, soln.x(k), soln.y(j), u_33))
k = 4
j = 4
val u_44: Double = soln.u(k, j)
println(String.format("u_%d,%d = u(%f,%f): %f", k, j, soln.x(k), soln.y(j), u_44))
k = 5
j = 5
val u_55: Double = soln.u(k, j)
println(String.format("u_%d,%d = u(%f,%f): %f", k, j, soln.x(k), soln.y(j), u_55))
```

输出如下（见图 8-7）：

![](img/528380_1_En_8_Fig7_HTML.jpg)

一个三维图形显示了维度为 1 到 1 的 5×5 网格图案。

**图 8-7**：5×5 解网格

```
u_1,1 = u(0.100000,0.100000): 4.000000
u_1,2 = u(0.100000,0.200000): 8.000000
u_2,1 = u(0.200000,0.100000): 8.000000
u_2,2 = u(0.200000,0.200000): 16.000000
u_3,3 = u(0.300000,0.300000): 36.000000
u_4,4 = u(0.400000,0.400000): 64.000000
u_5,5 = u(0.500000,0.500000): 100.000000
```

### 8.2.3 抛物型方程的数值解法

考虑如下一维热传导方程：

![$$ \frac{\partial u}{\partial t}=a\frac{\partial²u}{\partial {x}²},a&gt;0 $$](img/528380_1_En_8_Chapter_TeX_Equbb.png)

这个偏微分方程最为著名的、甚至改变了历史进程的应用，莫过于用于确定欧式期权价格的布莱克-舒尔斯方程。布莱克-舒尔斯方程催生了华尔街价值万亿美元的衍生品业务。经过变量变换后，布莱克-舒尔斯方程可以写成如下形式：

![$$ \frac{\partial u}{\partial \tau }=\frac{\partial²u}{\partial {x}²} $$](img/528380_1_En_8_Chapter_TeX_Equbc.png)

其中，`τ` 是期权的到期时间，期权在 `τ = 0` 时到期。`x` 是期权所参考的标的股票的价格。初始条件（`E` 为期权的行权价）其实就是收益函数，如下所示：

![$$ u\left(0,x\right)=\max \left(x-E,0\right) $$](img/528380_1_En_8_Chapter_TeX_Equbd.png)

边界条件表明，当股票变得一文不值时，期权也同样一文不值。

![$$ u\left(\tau, 0\right)=0 $$](img/528380_1_En_8_Chapter_TeX_Eqube.png)

当股票价格远高于行权价时，期权的表现就如同股票本身。

![$$ \underset{x\to \infty }{\lim }u\left(\tau, x\right)=x $$](img/528380_1_En_8_Chapter_TeX_Equbf.png)

近似求解该偏微分方程的一种直接方法是，用前向差分替代时间导数，并用中心差分替代二阶导数。即：

# 热传导方程的数值解法

## 显式格式

![$$ \frac{u_j^{k+1}-{u}_j^k}{\Delta \tau }=\frac{u_{j+1}^k-2{u}_j^k+{u}_{j-1}^k}{{\left(\Delta x\right)}²} $$](img/528380_1_En_8_Chapter_TeX_Equbg.png)

求解 `u_j^{k+1}`，你会得到如下表达式（参见图 8-8）：

![$$ {u}_j^{k+1}=\alpha {u}_{j+1}^k+\left(1-2\alpha \right){u}_j^k+\alpha {u}_{j-1}^k $$](img/528380_1_En_8_Chapter_TeX_Equbh.png)

![$$ \alpha =\frac{\Delta \tau }{{\left(\Delta x\right)}²} $$](img/528380_1_En_8_Chapter_TeX_Equbi.png)

![](img/528380_1_En_8_Fig8_HTML.jpg)

一张 x 相对于 tau 的图展示了 16 个不同的节点，这些节点在相同间隔下具有不同的点。

**图 8-8** 热传导方程的显式格式

这是一个显式公式，因为在时间步 `k`，你知道所有 `j` 对应的 `u_j^k`。你可以利用这个公式，使用三个节点（红色）显式地计算出 `u_j^{k+1}`（蓝色）。这种显式格式并非条件稳定。可以证明，仅当满足以下条件时，它才是稳定的：

![$$ 0&lt;\alpha \le \frac{1}{2} $$](img/528380_1_En_8_Chapter_TeX_Equbj.png)

或者，等效于：

![$$ 2\Delta \tau \le {\left(\Delta x\right)}² $$](img/528380_1_En_8_Chapter_TeX_Equbk.png)

因此，如果你想将 `x` 方向上的节点数加倍，就需要将时间步长减为四分之一。每个时间步的计算耗时翻倍（因为 `x` 方向节点数加倍），同时时间步数增加为原来的四倍。这意味着，将 `x` 方向节点数加倍会导致求解耗时变为原来的八倍。

## 隐式格式

或者，你可以使用后向差分来近似时间导数。其差分方程近似如下：

![$$ \frac{u_j^k-{u}_j^{k-1}}{\Delta \tau }=\frac{u_{j+1}^k-2{u}_j^k+{u}_{j-1}^k}{{\left(\Delta x\right)}²} $$](img/528380_1_En_8_Chapter_TeX_Equbl.png)

将时间 `k` 的项整理到等式左侧，你会得到如下表达式（参见图 8-9）：

![$$ -\alpha {u}_{j-1}^k+\left(1+2\alpha \right){u}_j^k-\alpha {u}_{j+1}^k={u}_j^{k-1} $$](img/528380_1_En_8_Chapter_TeX_Equbm.png)

![](img/528380_1_En_8_Fig9_HTML.jpg)

一张`x`与`τ`的关系图显示了 16 个不同的节点，这些节点在相同间隔内具有不同的坐标点。

**图 8-9**：热方程的隐式格式

下一时间步`k`的三个值无法通过`k − 1`时刻的旧值立即计算出来。你可以将时间步`k`和`k + 1`的值收集起来，并将其写成一个线性方程组。

![$$ \left[\begin{array}{ccccc}1+2\alpha &amp; -\alpha &amp; 0&amp; \cdots &amp; 0\\ {}-\alpha &amp; 1+2\alpha &amp; -\alpha &amp; &amp; 0\\ {}0&amp; -\alpha &amp; \ddots &amp; \ddots &amp; \\ {}\vdots &amp; &amp; \ddots &amp; \ddots &amp; -\alpha \\ {}0&amp; 0&amp; &amp; -\alpha &amp; 1+2\alpha \end{array}\right] \left[\begin{array}{c}{u}_{N^{-}+1}^k\\ {}\vdots \\ {}\begin{array}{c}{u}_0^k\\ {}\vdots \\ {}{u}_{N^{+}-1}^k\end{array}\end{array}\right] $$](images/528380_1_En_8_Chapter/528380_1_En_8_Chapter_TeX_Equbn.png)

![$$ = \left[\begin{array}{c}{u}_{N^{-}+1}^{k-1}\\ {}\vdots \\ {}{u}_0^{k-1}\\ {}\vdots \\ {}{u}_{N^{+}-1}^{k-1}\end{array}\right]+\alpha \left[\begin{array}{c}{u}_{N^{-}}^k\\ {}0\\ {}\vdots \\ {}0\\ {}{u}_{N^{+}}^k\end{array}\right]=\left[\begin{array}{c}{b}_{N^{-}+1}^k\\ {}\vdots \\ {}{b}_0^k\\ {}\vdots \\ {}{b}_{N^{+}-1}^k\end{array}\right] $$](images/528380_1_En_8_Chapter/528380_1_En_8_Chapter_TeX_Equbo.png)

`{u}_{N^{-}+1}^k`、`{u}_{N^{+}-1}^k`、`{u}_{N^{-}+1}^{k-1}`、`{u}_{N^{+}-1}^{k-1}`、`{u}_{N^{-}}^k`和`{u}_{N^{+}}^k`来自边界条件。以矩阵形式，你可以这样写：

![$$ {\boldsymbol{Mu}}^{\boldsymbol{k}}={\boldsymbol{b}}^{\boldsymbol{m}} $$](img/528380_1_En_8_Chapter_TeX_Equbp.png)

或者这样写：

![$$ {\boldsymbol{u}}^{\boldsymbol{k}}={\boldsymbol{M}}^{-\textbf{1}}{\boldsymbol{b}}^{\boldsymbol{m}} $$](img/528380_1_En_8_Chapter_TeX_Equbq.png)

在实践中，你几乎从不会计算逆矩阵，因为求逆的计算开销非常大，并且容易受到舍入误差的影响。在计算量和存储方面，会使用更高效的方法，例如`LU`分解、逐次超松弛法（SOR）以及高斯-赛德尔方法。`NM Dev`库已经实现了所有这些方法。隐式有限差分算法是无条件稳定的。

## 克兰克-尼科尔森格式

克兰克-尼科尔森有限差分法进一步改进了显式方法和隐式方法，使得它既无条件稳定，又具有`O`((∆`τ`)²)阶的截断误差（而其他两种方法为`O`(∆`τ`)）。克兰克-尼科尔森有限差分法取前向差分近似和后向差分近似的平均值。这两个方程的平均值如下：

![$$ \frac{u_j^{k+1}-{u}_j^k}{\Delta \tau }=\frac{1}{2}\left(\frac{u_{j+1}^k-2{u}_j^k+{u}_{j-1}^k}{{\left(\Delta x\right)}²}+\frac{u_{j+1}^{k+1}-2{u}_j^{k+1}+{u}_{j-1}^{k+1}}{{\left(\Delta x\right)}²}\right) $$](img/528380_1_En_8_Chapter_TeX_Equbr.png)

重新整理，将时间步`k + 1`的项放在左侧，时间步`k`的项放在右侧，得到：

![$$ {u}_j^{k+1}-\frac{1}{2}\alpha \left({u}_{j+1}^{k+1}-2{u}_j^{k+1}+{u}_{j-1}^{k+1}\right)={u}_j^k+\frac{1}{2}\alpha \left({u}_{j+1}^k-2{u}_j^k+{u}_{j-1}^k\right) $$](img/528380_1_En_8_Chapter_TeX_Equbs.png)

同样，你可以将这个线性方程组写成矩阵形式：

![$$ \left[\begin{array}{ccccc}1+\alpha &amp; -\frac{1}{2}\alpha &amp; 0&amp; \cdots &amp; 0\\ {}-\frac{1}{2}\alpha &amp; 1+\alpha &amp; -\frac{1}{2}\alpha &amp; &amp; 0\\ {}0&amp; -\frac{1}{2}\alpha &amp; \ddots &amp; \ddots &amp; \\ {}\vdots &amp; &amp; \ddots &amp; \ddots &amp; -\frac{1}{2}\alpha \\ {}0&amp; 0&amp; &amp; -\frac{1}{2}\alpha &amp; 1+\alpha \end{array}\right]\left[\begin{array}{c}{u}_{N^{-}+1}^{k+1}\\ {}\vdots \\ {}{u}_0^{k+1}\\ {}\vdots \\ {}{u}_{N^{+}-1}^{k+1}\end{array}\right] $$](images/528380_1_En_8_Chapter/528380_1_En_8_Chapter_TeX_Equbt.png)

![$$ =\left[\begin{array}{c}{Z}_{N^{-}+1}^k\\ {}\vdots \\ {}{Z}_0^k\\ {}\vdots \\ {}{Z}_{N^{+}-1}^k\end{array}\right]+\frac{1}{2}\alpha \left[\begin{array}{c}{u}_{N^{-}}^{k+1}\\ {}0\\ {}\vdots \\ {}0\\ {}{u}_{N^{+}}^{k+1}\end{array}\right] $$](images/528380_1_En_8_Chapter/528380_1_En_8_Chapter_TeX_Equbu.png)

![$$ {Z}_j^k=\left(1-\alpha \right){u}_j^k+\frac{1}{2}\alpha \left({u}_{j-1}^k+{u}_{j+1}^k\right) $$](img/528380_1_En_8_Chapter_TeX_Equbv.png)

然后，该方程组可通过 SOR（`NM Dev`中的默认方法）求解。

例如，以下`NM Dev`代码使用克兰克-尼科尔森方法在区间 [0, 6000] × [0, 1] 上求解该热方程：

![$$ \left\{\begin{array}{c}\frac{\partial u}{\partial t}={10}^{-5}\frac{\partial²u}{\partial {x}²}\\ {}u\left(0,x\right)=2x+\sin \left(2\pi x\right)\\ {}\begin{array}{c}u\left(t,0\right)=0\\ {}u\left(t,1\right)=2\end{array}\end{array}\right. $$](img/528380_1_En_8_Chapter_TeX_Equbw.png)

```
val f1: UnivariateRealFunction = object : AbstractUnivariateRealFunction() {
override fun evaluate(x: Double): Double {
return 2.0 * x + sin(2.0 * PI * x) // 初始条件
}
}
val f2: UnivariateRealFunction = object : AbstractUnivariateRealFunction() {
override fun evaluate(t: Double): Double {
return 0.0 // 在 x = 0 处的边界条件
}
}
val f3: UnivariateRealFunction = object :  AbstractUnivariateRealFunction() {
override fun evaluate(t: Double): Double {
return 2.0 // 在 x = 6000 处的边界条件
}
}
val pde: HeatEquation1D = HeatEquation1D(
1e-5, // 热方程系数
1.0, 6000.0, // 解域边界
f1,
0.0,
f2,
0.0,
f3
)
// 对于狄利克雷边界条件，c_k 为 0
val m: Int = 50
val n: Int = 39
val soln: PDESolutionTimeSpaceGrid1D
= CrankNicolsonHeatEquation1D().solve(pde, m, n)
var t: Int = 0
var x: Int = 1
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 0
x = 16
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 0
x = 31
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 15
x = 1
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 15
x = 16
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 15
x = 31
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 30
x = 1
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 30
x = 16
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 30
x = 31
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 45
x = 1
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 45
x = 16
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
t = 45
x = 31
println(String.format("u(%d,%d) = %f", t, x, soln.u(t, x)))
```

输出如下：

```
u(0,1) = 0.206434
u(0,16) = 1.387785
u(0,31) = 0.562312
u(15,1) = 0.126965
u(15,16) = 1.089188
u(15,31) = 1.064061
u(30,1) = 0.087867
u(30,16) = 0.942280
u(30,31) = 1.310920
u(45,1) = 0.068630
u(45,16) = 0.870001
u(45,31) = 1.432373
```

`HeatEquation1D`类用于构建这样一个热方程问题。其签名如下：

```java
/**
* 构造一个热方程问题。
*
* @param beta 方程中的 beta 参数
* @param a    感兴趣的区域 (0, a)
* @param T    感兴趣的时间段 (0, T)
* @param f    u 的初始条件，即 u(0,x)
* @param c1   在 x = 0 处混合边界条件的系数
* @param g1   在 x = 0 处的混合边界条件
* @param c2   在 x = a 处混合边界条件的系数
* @param g2   在 x = a 处的混合边界条件
*/
public HeatEquation1D(
double beta,
double a,
double T,
UnivariateRealFunction f,
double c1,
UnivariateRealFunction g1,
double c2,
UnivariateRealFunction g2)
```

`CrankNicolsonHeatEquation1D`类使用 Crank-Nicolson 有限差分算法求解一维热方程。其签名如下：

# 二维热方程求解

## 一维热方程求解方法

```java
/**
* 求解给定的一维热方程。
*
* @param pde 偏微分方程问题
* @param M   时间轴上的网格点数（不包括初始条件）
* @param N   空间轴上的网格点数（不包括两个边界）
* @return 网格点上的解 \(u(t,x)\)
*/
public PDESolutionTimeSpaceGrid1D solve(
final HeatEquation1D pde,
final int M,
final int N
)
```

## 二维热方程求解

### 问题定义

此外，`NM Dev` 库支持求解二维热方程。其形式如下：

![$$ \frac{\partial u}{\partial t}=\beta \left(\frac{\partial²u}{\partial {x}²}+\frac{\partial²u}{\partial {y}²}\right) $$](img/528380_1_En_8_Chapter_TeX_Equbx.png)

初始条件如下：

![$$ u\left(0,x,y\right)=f\left(x,y\right) $$](img/528380_1_En_8_Chapter_TeX_Equby.png)

边界条件如下：

![$$ u\left(t,x,y\right)=g\left(t,x,y\right) $$](img/528380_1_En_8_Chapter_TeX_Equbz.png)

其中(`x`, `y`) ∈ `δR`，`δR` 是曲面 `R` 的边界。

### 方法与构造

`NM Dev` 类 `HeatEquation2D` 构造这样一个对象。其签名如下：

```java
/**
* 构造一个二维热方程问题。
*
* @param beta 方程中的 beta 参数
* @param T    感兴趣的时间段 (0, T)
* @param a    沿 x 轴的区域大小，x ∈ (0, a)
* @param b    沿 y 轴的区域大小，y ∈ (0, b)
* @param f    u 的初始条件，即 u(0,x,y)
* @param g    在 x = 0, a 和 y = 0, b 处的边界条件
*/
public HeatEquation2D(
double beta,
double T,
double a,
double b,
BivariateRealFunction f,
TrivariateRealFunction g
)
```

`NM Dev` 类 `AlternatingDirectionImplicitMethod` 使用交替方向隐式（ADI）方法求解二维热方程。ADI 方法的思想是将有限差分方程拆分为两个，一个对 `x` 方向导数隐式处理，另一个对 `y` 方向导数隐式处理。这使得每一步都可以通过 LU 分解求解对称的三对角线性方程组。解是在三维网格（时间和 `x`、`y` 坐标）上计算的。ADI 是隐式方法，无条件稳定；它是二阶方法，截断误差为 *O*(∆`t`² + ∆`x`² + ∆`y`²)。

### 示例代码与输出

以下代码求解这个示例 PDE 问题：

![$$ \left\{\begin{array}{c}\frac{\partial u}{\partial t}={10}^{-4}\left(\frac{\partial²u}{\partial {x}²}+\frac{\partial²u}{\partial {y}²}\right)\\ {}u\left(0,x,y\right)=0\\ {}u\left(t,x,y\right)={e}^y\cos x-{e}^x\cos y\end{array}\right. $$](img/528380_1_En_8_Chapter_TeX_Equca.png)

```kotlin
println("求解一个二维热方程")
// 求解域
val a: Double = 4.0
val b: Double = 4.0
// 时间域
val T: Double = 5000.0
// 热方程系数
val beta: Double = 1e-4
// 初始条件
val f: BivariateRealFunction = object : AbstractBivariateRealFunction() {
override fun evaluate(x1: Double, x2: Double): Double {
return 0.0
}
}
// 边界条件
val g: TrivariateRealFunction = object : AbstractTrivariateRealFunction() {
override fun evaluate(t: Double, x: Double, y: Double): Double {
return exp(y) * cos(x) - exp(x) * cos(y)
}
}
val PDE: HeatEquation2D = HeatEquation2D(beta, T, a, b, f, g)
val adi: AlternatingDirectionImplicitMethod
= AlternatingDirectionImplicitMethod(1e-5)
val soln: PDESolutionTimeSpaceGrid2D = adi.solve(PDE, 50, 39, 39)
var t: Int = 50
var x: Int = 1
var y: Int = 1
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 50
x = 1
y = 16
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 50
x = 1
y = 31
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 50
x = 16
y = 1
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 50
x = 16
y = 16
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 50
x = 16
y = 31
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 50
x = 31
y = 1
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 50
x = 31
y = 16
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
t = 50
x = 31
y = 31
println(String.format("u(%d,%d,%d) = %f", t, x, y, soln.u(t, x, y)))
```

输出如下：

```
u(50,1,1) = -0.000000
u(50,1,16) = 4.771024
u(50,1,31) = 22.814937
u(50,16,1) = -4.771024
u(50,16,16) = -0.000000
u(50,16,31) = 2.415165
u(50,31,1) = -22.814937
u(50,31,16) = -2.415165
u(50,31,31) = -0.000000
```

### `AlternatingDirectionImplicitMethod` 类签名

`AlternatingDirectionImplicitMethod` 类的签名如下：

```java
/**
* 使用给定的精度参数构造一个 ADI 方法。默认情况下，
* 使用多核并行计算以获得更高性能。
*
* @param epsilon 精度参数
*/
public AlternatingDirectionImplicitMethod(double epsilon)
/**
* 求解给定的二维热方程问题，使用网格中沿三个轴（时间、x 和 y）的给定点数。
*
* @param pde 热方程问题
* @param m   时间轴上的网格点数（不包括初始条件）
* @param n   x 轴上的网格点数（不包括边界）
* @param p   y 轴上的网格点数（不包括边界）
* @return 解网格
*/
public PDESolutionTimeSpaceGrid2D solve(
HeatEquation2D pde,
final int m,
final int n,
final int p
)
```

