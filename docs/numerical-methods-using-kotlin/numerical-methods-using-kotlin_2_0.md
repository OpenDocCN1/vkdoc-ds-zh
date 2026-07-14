# 2. 线性代数

线性代数算法是数值计算中最重要的算法，也是所有数值算法的基础。许多数值算法，无论多么复杂，都涉及一些线性代数计算。例如，非线性问题通常难以（甚至不可能）直接求解。一种常用技术是使用多个较小的线性子问题来逼近非线性问题（如同在微积分中的做法）。而线性问题通常涉及一些向量和矩阵，因为它包含多个变量。向量和矩阵的运算在数值计算中无处不在。不同库的理论算法可能相同，但其实现方式却可能大相径庭，从而导致性能和精度上的巨大差异。因此，线性代数算法（如矩阵分解）的实现，对于许多数值算法的精度和性能，乃至整个数值计算领域，都至关重要。

要创建一个高质量、专业的线性代数计算库，至少面临两个挑战：精度和高性能（快速计算）。计算机只能表示有限数量的数字（一台 64 位计算机大约能表示 1.8e19 个数字）；换句话说，有无数个数字计算机是无法表示的。计算的（浮点）值通常只是真实理论结果的近似值。当计算涉及一个非常大的数和一个非常小的数时，情况会更糟。结果往往偏向大数，忽略了小数的精度。在迭代计算（例如优化计算）中，每一轮微小的误差都会累积。一个理论上可解的问题有时会变得在实践中不可行。

因此，仅仅正确地实现理论算法是不够的。一个专业的数值库还需要处理实际的数值问题，例如，处理一个元素数量级相差极大的矩阵。涉及一系列连续行和/或列操作的基本矩阵运算，若混合了非常小和非常大的元素，将导致相当大的舍入误差。处理此问题的一种方法是在操作前尽可能地对矩阵进行重排，使元素按单调顺序重新排列。可以应用置换矩阵将较小的元素推到左上角，然后从右下角开始进行操作。有时，选择更好的算法也会有所帮助。例如，在计算特征值时，`QL`算法通常比`QR`算法具有更小的舍入误差。

在`NM Dev`中，我们严肃对待数值稳定性问题。我们通过大量基准测试问题实例来测试我们的库，并收集病态和退化情况。然后，我们应用各种数值技术逐一处理这些情况。我们仔细地将我们的结果与其他开源和商业软件进行比较，以确保我们的结果良好。这个过程使得我们的库更加健壮，能够处理各种问题结构。

加速向量和矩阵的计算，通常也会相应地加速任何数值计算。利用现代计算技术加速的方法（及其考量因素）有很多：并行计算（多线程、多核、多机器、`CUDA`）、更快的算法、更高效的数据结构、原地操作与对象副本复制、内联操作与虚函数调用等等。其中一些技术可以加速代码执行（例如内联操作），但可能会使代码对其他人（有时甚至是原作者）难以理解，从而导致不可维护。`NM Dev`致力于在（人类）可读性和速度之间取得平衡。我们将`NM Dev`的性能与所有其他公开可用的主要`Java`数学库进行了比较。比较是在一台配备双`Intel Xeon X5650 2.66GHz`（共 12 核）处理器、24GB 内存、运行`Windows 7 Ultimate 64`位系统的机器上进行的。使用的`JVM`是`Oracle Java HotSpot 64-Bit Server 1.8.0_25`。根据我们的基准测试，`NM Dev`是迄今为止（可能）最快的（`Java`）数学库。以下是几个基准比较。完整测试套件的源代码和结果可从此处下载：

```
https://nm.dev/html/benchmark/benchmark_chart.xhtml?category=linearalgebra
```

`NM Dev`或`SuanShu`的矩阵乘法比`Apache Commons Math`快 182 倍，比`R`快 14 倍。见图 2-1。

![](img/528380_1_En_2_Fig1_HTML.jpg)

关于矩阵乘法相对耗时比较的柱状图。`Apache commons 3.5`和`colt 1.2.0`的性能最高，而`suanshu 3.3.0`的性能最低。

图 2-1

`NM Dev`的矩阵乘法比`Apache`快 182 倍

这是因为`NM Dev`的矩阵实现使用了自适应`Strassen`算法的并行版本进行快速矩阵乘法，其复杂度为`O(n^(2.8074))`。`Apache Commons Math`和我们所知的大多数其他`Java`库实现的是标准的`O(n³)`算法。对于较大的矩阵，尤其是处理大数据时，`NM Dev`的性能优势非常显著。

`NM Dev`的矩阵行列式计算比`Apache Commons Math`快 31 倍。见图 2-2。

![](img/528380_1_En_2_Fig2_HTML.png)

关于矩阵行列式相对耗时比较的柱状图。`Apache commons 3.5`和`m t j 1.0.4`的性能最高，而`suanshu 3.3.0`的性能最低。

图 2-2

`NM Dev`的矩阵行列式计算比`Apache`快 31 倍

`NM Dev`的`LU`分解比`Apache Commons Math`快 32 倍。见图 2-3。

![](img/528380_1_En_2_Fig3_HTML.png)

关于`LU`分解相对耗时比较的柱状图。`Apache commons 3.5`的范围最高。

图 2-3

`NM Dev`的`LU`分解比`Apache`快 32 倍

`NM Dev`的`QR`分解比`R`快两倍。见图 2-4。

![](img/528380_1_En_2_Fig4_HTML.png)

关于`QR`分解相对耗时比较的柱状图。`Colt 1.2.0`、`jama 1.0.3`、`ujmp 0.3.0`的性能最高，而`suanshu 3.3.0`的性能最低。

图 2-4

`NM Dev`的`QR`分解比`R`快两倍

`NM Dev`的`SVD`分解比`R`快两倍。见图 2-5。

![](img/528380_1_En_2_Fig5_HTML.png)

关于奇异值分解相对耗时比较的柱状图。`Apache commons 3.5`、`colt 1.2.0`、`jama 1.0.3`、`ujmp 0.3.0`的性能最高，而`suanshu 3.3.0`的性能最低。

图 2-5

`NM Dev`的`SVD`分解比`R`快两倍

在求解此线性方程组时，`NM Dev`比`R`快六倍。见图 2-6。

![](img/528380_1_En_2_Fig6_HTML.png)

相对耗时与奇异值分解的柱状图。`Apache Commons 3.5`性能最高，而`Suanshu 3.3.0`和`ojAlgo 38.2`性能最低。

图 2-6

在求解线性方程组时，`NM Dev`比`R`语言快六倍

本章的源代码可在`S2`平台上运行：

[`s21.nm.dev/hub/user-redirect/lab/tree/s2-public/Numerical%20Methods%20Using%20Kotlin/chapter2.ipynb`](https://s21.nm.dev/hub/user-redirect/lab/tree/s2-public/Numerical%2520Methods%2520Using%2520Kotlin/chapter2.ipynb)

源代码可在`GitHub`上找到：

[`github.com/nmltd/s2-public/blob/main/Numerical%20Methods%20Using%20Kotlin/chapter2.ipynb`](https://github.com/nmltd/s2-public/blob/main/Numerical%2520Methods%2520Using%2520Kotlin/chapter2.ipynb)

## 2.1 向量

在数学中，欧几里得向量是一种具有大小（或长度）和方向的几何对象。一个`n`维向量可以表示为由`n`个标量元素组成的数组，其中每个元素是该向量在对应维度上投影的长度。例如，在二维欧几里得空间（记作`ℝ²`）中，从点`A = (0, 0)`指向点`B = (1, 2)`的向量`a`可以表示为：

![$$ a=\overrightarrow{AB}=\left(1,2\right) $$](img/528380_1_En_2_Chapter_TeX_Equa.png)

在`NM Dev`中，向量通过`Vector`接口表示。构造此类对象有多种方式。最常见的方式是使用`double`数组初始化一个`DenseVector`，指定每个元素。见图 2-7。

```kotlin
val v1: Vector = DenseVector(arrayOf(1.0, 2.0)) // 定义一个向量
println(String.format("v1 = %s", v1)) // 打印 v1
```

输出如下：

![](img/528380_1_En_2_Fig7_HTML.png)

一幅图，显示从原点 A 到交点 (1,2) 处 B 点绘制的箭头。

图 2-7

向量![$$ \overrightarrow{\boldsymbol{AB}}=\left(\textbf{1},\textbf{2}\right) $$](img/528380_1_En_2_Chapter_TeX_IEq1.png)

```
v1 = [1.000000, 2.000000]
```

### 2.1.1 逐元素运算

### 2.1.1 访问和修改向量

以下函数用于访问和修改 `Vector`：

```
/**
* 获取此向量的长度。
*
* @return 向量长度
*/
public int size();
/**
* 获取位置 i 处的值。
*
* @param i 向量条目的位置
* @return v[i]
*/
public double get(int i);
/**
* 更改此向量中某个条目的值。
* 这是唯一可能更改向量条目的方法。
*
* @param i     要更改条目的索引。索引从 1 开始计数，而非 0。
* @param value 要更改的值
*/
public void set(int i, double value);
```

计算机科学家从 0 开始计数，而数学家从 1 开始计数。NM Dev 遵循数学家的惯例，向量和矩阵的索引从 1 而不是 0 开始计数。

接续前面的示例，以下代码片段将向量改为 (1, −2)：

```
val size: Int = v1.size() // 2
println(String.format("v1 size = %d", size))
```

输出如下：

```
v1 size = 2
val v1_1: Double = v1.get(1) // 1.0
println(String.format("v1_1 = %f", v1_1))
```

输出如下：

```
v1_1 = 1.000000
v1.set(2, -2.0) // v1 变为 (1.0, -2.0)
println(String.format("v1 = %s", v1)) // 打印 v1
```

输出如下：

```
v1 = [1.000000, -2.000000]
```

#### 向量算术运算

在 NM Dev 中，向量上的算术运算——加法、减法、乘法、除法、缩放和幂运算——均为逐元素运算。

```
/**
* \(this + that\)
*
* @param that 一个向量
* @return \(this + that\)
*/
@Override
public Vector add(Vector that);
/**
* \(this - that\)
*
* @param that 一个向量
* @return \(this - that\)
*/
@Override
public Vector minus(Vector that);
/**
* 将 {@code this} 与 {@code that} 逐元素相乘。
*
* @param that 一个向量
* @return \(this \cdot that\)
*/
public Vector multiply(Vector that);
/**
* 将 {@code this} 除以 {@code that}，逐元素相除。
*
* @param that 一个向量
* @return \(this / that\)
*/
public Vector divide(Vector that);
/**
* 向此向量的所有条目添加一个常数。
*
* @param c 一个常数
* @return \(v + c\)
*/
public Vector add(double c);
/**
* 从此向量的所有条目中减去一个常数。
*
* @param c 一个常数
* @return \(v - c\)
*/
public Vector minus(double c);
/**
* 对此向量的所有条目进行幂运算，逐元素进行。
*
* @param c 一个常数
* @return \(v ^ c\)
*/
public Vector pow(double c);
/**
* 用一个常数缩放此向量，逐元素进行。获取向量的单位版本的方法如下：
* 
* vector.scaled(1\. / vector.norm())
* 
*
* @param c 一个常数
* @return \(c \times this\)
*/
public Vector scaled(double c);
```

以下代码片段演示了向量的算术运算：

```
val v2: Vector = DenseVector(arrayOf(-2.0, 1.0)) // 定义另一个向量
// 加法
val a1: Vector = v1.add(0.1) // 给所有条目加 0.1
println(String.format("a1 = %s", a1)) // 打印 a1
val a2: Vector = v1.add(v2) // a2 = v1 + v2，逐元素
println(String.format("a2 = %s", a2)) // 打印 a2
```

输出如下：

```
a1 = [1.100000, -1.900000]
a2 = [-1.000000, -1.000000]
// 减法
val m1: Vector = v1.minus(0.1) // 从所有条目中减 0.1
println(String.format("m1 = %s", m1)) // 打印 m1
val m2: Vector = v1.minus(v2) // v1 – v2，逐元素
println(String.format("m2 = %s", m2)) // 打印 m2
```

输出如下：

```
m1 = [0.900000, -2.100000]
m2 = [3.000000, -3.000000]
val s1: Vector = v1.scaled(0.5) // 将所有条目乘以 0.5
println(String.format("s1 = %s", s1)) // 打印 s1
```

输出如下：

```
s1 = [0.500000, -1.000000]
val t1: Vector = v1.multiply(v2) // 将 v1 与 v2 逐元素相乘
println(String.format("t1 = %s", t1)) // 打印 t1
```

输出如下：

```
t1 = [-2.000000, -2.000000]
// 除法
val d1: Vector = v1.divide(v2) // 将 v1 除以 v2，逐元素
println(String.format("d1 = %s", d1)) // 打印 d1
```

输出如下：

```
d1 = [-0.500000, -2.000000]
// 幂运算
val p1: Vector = v1.pow(2.0) // 取平方，逐元素
println(String.format("p1 = %s", p1)) // 打印 p1
```

输出如下：

```
p1 = [1.000000, 4.000000]
```

### 2.1.2 范数

向量 `x` = (*x*[1], *x*[1], …, *x*[*n*]) 的 *ℓ*[*p*]-范数定义如下：

![$$ {\left\Vert x\right\Vert}_p={\left(\sum \limits_{i=i}^n{\left|{\boldsymbol{x}}_i\right|}^p\right)}^p $$](img/528380_1_En_2_Chapter_TeX_Equb.png)

*范数*是一种函数，它将一个向量映射到一个非负实数，其性质类似于向量到原点的距离。它满足缩放齐次性，服从某种三角不等式，并且仅在原点处为零。在 NM Dev 中，向量范数通过 `norm(double p)` 成员方法计算。特别地，当不传入参数时，`norm()` 计算的是 *ℓ*[2]-范数。*ℓ*[2]-范数，也称为欧几里得范数，通常记为 ‖`x`‖[2] 或简写为 ‖`x`‖，表示向量到原点的欧几里得距离。它也是向量与其自身内积的平方根。以下代码片段计算了直角边长为 3 和 4 的直角三角形的 *ℓ*[1]-范数和 *ℓ*[2]-范数。请参见图 2-8。

![](img/528380_1_En_2_Fig8_HTML.png)

一幅从原点 A 到 B 的箭头图，箭头末端交于坐标为 (3,4) 的点。

图 2-8

一个 3-4-5 直角三角形

```
// 范数
val v3: Vector = DenseVector(arrayOf(3.0, 4.0))
val norm1: Double = v3.norm(1.0) // l1 范数 = (3.0 + 4.0) = 7.0
println(String.format("L1 范数 = %f", norm1))
val norm2: Double = v3.norm() // l2 范数 = sqrt(3² + 4²) = 5，勾股定理
println(String.format("L2 范数 = %f", norm2))
```

输出结果如下：

```
L1 范数 = 7.000000
L2 范数 = 5.000000
```

### 2.1.3 内积与夹角

在实数 ℝ^(*n*) 中，向量 x = (*x*[1], *x*[2], …, *x*[*n*]) 与 y = (*y*[1], *y*[2], …, *y*[*n*]) 的内积定义如下：

![$$ \left\langle \boldsymbol{x},\boldsymbol{y}\right\rangle =\boldsymbol{x}\cdot \boldsymbol{y}=\sum \limits_{i=1}^n{x}_i{y}_i $$](img/528380_1_En_2_Chapter_TeX_Equc.png)

x 与 y 之间的夹角 θ 定义如下：

![$$ \theta ={\cos}^{-1}\frac{\left\langle \boldsymbol{x},\boldsymbol{y}\right\rangle }{\left\Vert \boldsymbol{x}\right\Vert \left\Vert \boldsymbol{y}\right\Vert } $$](img/528380_1_En_2_Chapter_TeX_Equd.png)

请参见图 2-9。

![](img/528380_1_En_2_Fig9_HTML.png)

一张用公式展示点积与夹角的示意图。图中夹角的 θ 值小于 90 度。

图 2-9

点积与夹角

特别地，如果向量 x 和 y 正交或垂直（它们的夹角为 90°），则 x · y = 0。在另一个极端，如果它们指向同一方向（夹角为 0°），则 x · y = ‖x‖‖y‖。这意味着向量 x 与自身的内积为 x · x = ‖x‖²。在 NM Dev 中，内积和夹角方法的签名如下：

```
/**
 * 欧几里得空间中的内积即为点积。
 *
 * @param that 一个向量
 * @return \(this \cdot that\)
 * @see Wikipedia: Dot
 * product
 */
@Override
public double innerProduct(Vector that);
/**
 * 计算 {@code this} 与 {@code that} 之间的夹角 \(\theta\)。
 * 公式如下：
 * \[
 * this \cdot that = \|this\| \times \|that\| \times \cos \theta
 * \]
 *
 * @param that 一个向量
 * @return {@code this} 与 {@code that} 之间的夹角 \(\theta\)
 */
@Override
public double angle(Vector that);
```

示例如下：

```
// 内积与夹角
val v4: Vector = DenseVector(arrayOf(1.0, 2.0))
val v5: Vector = DenseVector(arrayOf(3.0, 4.0))
val dot: Double = v4.innerProduct(v5) // (1.*3\. + 2.*4.) = 11.
println(String.format(" = %f", v4, v5, dot))
val angle: Double = v4.angle(v5)
println(String.format("%s 与 %s 的夹角 = %f", v4, v5, angle))
```

输出结果如下：

```
 = 11.000000
[1.000000, 2.000000] 与 [3.000000, 4.000000] 的夹角 = 0.179853
```

## 2.2 矩阵

矩阵是按行和列排列在矩形阵列或表格中的一组元素或条目。一个 m × n 矩阵是一个具有 m 行和 n 列的矩形阵列。矩阵的元素可以是数字、符号或数学表达式。以下是一个由九个元素组成的三行三列矩阵：

![$$ \left[\begin{array}{ccc}1&amp; 2&amp; 3\\ {}4&amp; 5&amp; 6\\ {}7&amp; 8&amp; 9\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Eque.png)

在一个 m × n 矩阵 A 中的各个条目通常用 a[ij] 表示，其中 i（行索引）和 j（列索引）通常分别从 1 变化到 m 和 n。为了方便地表示矩阵运算结果中的某个元素，该元素的索引通常附加在带括号或方括号的矩阵表达式上（例如，(AB)[ij] 指的是矩阵乘积的一个元素）。如果 m > n，则矩阵是高矩阵；如果 m < n，则是扁矩阵；如果 m = n，则是方矩阵。见图 2-10。

![](img/528380_1_En_2_Fig10_HTML.png)

一个矩阵图示。方括号中给出的水平值从下标 1 1 到下标 1 n。它以从下标 m 1 到下标 m n 结束。

图 2-10

`a[ij]` 指的是第 i 行和第 j 列的条目

在 NM Dev 中，矩阵由 `Matrix` 接口表示。有多种构建 `Matrix` 对象的方法。最常见的方法是使用 `DenseMatrix` 实现，并在一个双精度数组中指定所有条目（非零和零）。以下代码片段构建了前面展示的 3×3 矩阵：

```
// matrix construction
val m1: Matrix = DenseMatrix(
arrayOf(
doubleArrayOf(1.0, 2.0, 3.0),
doubleArrayOf(4.0, 5.0, 6.0),
doubleArrayOf(7.0, 8.0, 9.0)
)
)
val m2: Matrix = DenseMatrix(m1) // copy the matrix m1
println(String.format("\nm1 = %s", m1)) // print out the matrix m1
println(String.format("\nm2 = %s", m2)) // print out the matrix m2
```

输出如下：

```
m1 = 3x3
[,1] [,2] [,3]
[1,] 1.000000, 2.000000, 3.000000,
[2,] 4.000000, 5.000000, 6.000000,
[3,] 7.000000, 8.000000, 9.000000,
m2 = 3x3
[,1] [,2] [,3]
[1,] 1.000000, 2.000000, 3.000000,
[2,] 4.000000, 5.000000, 6.000000,
[3,] 7.000000, 8.000000, 9.000000,
```

有许多种构造 `DenseMatrix` 的方法。其签名如下：

## DenseMatrix 构造函数

```java
/**
* 构造一个维度为 nRows * nCols 的零矩阵。
*
* @param nRows 行数
* @param nCols 列数
*/
public DenseMatrix(int nRows, int nCols)
/**
* 从一个二维的 {@code double[][]} 数组构造矩阵。
*
* @param data 一个二维数组输入
* @throws IllegalArgumentException 当 {@code data} 是锯齿数组时
*/
public DenseMatrix(double[][] data)
/**
* 从一个一维的 {@code double[]} 构造矩阵。该数组是矩阵行的串联。
* 一个示例用法是将向量转换为矩阵。例如，要构造一个列向量，我们这样做
* {@code
* DenseMatrix V = new DenseMatrix(v.toArray(), v.length, 1);
* } 要构造一个行向量，我们这样做
* {@code
* DenseMatrix V = new DenseMatrix(v.toArray(), 1, v.length);
* }
*
* @param data  一维数组输入
* @param nRows 行数
* @param nCols 列数
* @throws IllegalArgumentException 当 {@code data} 的长度不等于 nRows * nCols 时
*/
public DenseMatrix(double[] data, int nRows, int nCols)
/**
* 从向量构造一个列矩阵。
*
* @param v 一个向量
*/
public DenseMatrix(Vector v)
/**
* 将任意矩阵转换为标准矩阵表示形式。如果 {@code A} 是 {@link Densifiable} 类型，
* 则此方法与 {@link #toDense} 相同。
*
* @param A 一个矩阵
*/
public DenseMatrix(Matrix A)
/**
* 执行深拷贝的拷贝构造函数。
*
* @param A 一个 {@code DenseMatrix}
*/
public DenseMatrix(DenseMatrix A)
```

例如，以下代码片段从一个向量创建了一个列矩阵：

```kotlin
// 创建一个向量
val v: Vector = DenseVector(arrayOf(1.0, 2.0, 3.0))
// 从一个向量创建一个列矩阵
val m3: Matrix = DenseMatrix(v)
println(String.format("\nm3 = %s", m3)) // 打印矩阵 m3
```

输出如下：

```
m3 = 3x1
[,1]
[1,] 1.000000,
[2,] 2.000000,
[3,] 3.000000,
```

### 矩阵运算

在设计 NM Dev 库时，我们使类库尽可能简洁，以避免方法污染。这是受 jMatrices 白皮书的启发。挑战在于通过最小且正确的包来组织方法。`Matrix` 类只有 26 个方法，其中 9 个是构造及相关方法，3 个是重写 `AbstractMatrix` 接口的，8 个是重写 `MatrixSpace` 接口的。只有 6 个是类特有的，这使得调用这些方法对用户来说很方便。其他几十种矩阵运算，例如各种分解、秩等属性以及逆变换等，都被分组到多个类和包中。在大多数情况下，这些运算中的每一个都是一个独立的类。例如，求逆运算本身就是一个继承自 `Matrix` 的类。其构造函数接受一个待求逆的 `Matrix` 作为输入。例如，要求解以下矩阵的逆：

![$$ \textbf{A}=\left[\begin{array}{ccc}1&amp; 2&amp; 3\\ {}6&amp; 5&amp; 4\\ {}8&amp; 7&amp; 9\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equf.png)

你可以编写如下代码：

```kotlin
// 计算可逆矩阵的逆
val A: Matrix = DenseMatrix(
    arrayOf(
        doubleArrayOf(1.0, 2.0, 3.0),
        doubleArrayOf(6.0, 5.0, 4.0),
        doubleArrayOf(8.0, 7.0, 9.0)
    )
)
val Ainv: Matrix = Inverse(A)
val I: Matrix = A.multiply(Ainv)
println(String.format("%s * \n%s = \n%s", A, Ainv, I))
```

输出如下：

```
3x3
[,1] [,2] [,3]
[1,] 1.000000, 2.000000, 3.000000,
[2,] 6.000000, 5.000000, 4.000000,
[3,] 8.000000, 7.000000, 9.000000,  *
3x3
[,1] [,2] [,3]
[1,] -0.809524, -0.142857, 0.333333,
[2,] 1.047619, 0.714286, -0.666667,
[3,] -0.095238, -0.428571, 0.333333,  =
3x3
[,1] [,2] [,3]
[1,] 1.000000, 0.000000, 0.000000,
[2,] -0.000000, 1.000000, 0.000000,
[3,] -0.000000, -0.000000, 1.000000,
```

NM Dev 计算出的矩阵 A 的逆如下：

![$$ {A}^{-1}=\left[\begin{array}{ccc}-0.809524&amp; -0.142857&amp; 0.333333\\ {}1.047619&amp; 0.714286&amp; -0.666667\\ {}-0.095238&amp; -0.428571&amp; 0.333333\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equg.png)

需要注意的重要一点是，A 的逆 `Ainv` 也是一个 `Matrix`，是通过关键字 `new` 创建的，而不是通过方法调用。总之，我们选择拥有许多类，而不是拥有一个包含许多方法的类。每个类都刻意保持简短。这种类简洁原则是指导整个库开发的关键设计决策。

### 逐元素运算

这些函数用于访问和修改一个 `Matrix`：

```java
/**
* 将矩阵在 [i,j] 处的元素设置为指定值。
* 这是唯一可能修改矩阵的方法。
*
* @param i     行索引
* @param j     列索引
* @param value 要设置给 A[i,j] 的值
* @throws MatrixAccessException 如果 i 或 j 超出范围
*/
public void set(int i, int j, double value) throws MatrixAccessException;
/**
* 获取矩阵在 [i,j] 处的元素。
*
* @param i 行索引
* @param j 列索引
* @return A[i,j]
* @throws MatrixAccessException 如果 i 或 j 超出范围
*/
public double get(int i, int j) throws MatrixAccessException;
```

NM Dev 从 1 而不是 0 开始计数（向量和矩阵的）索引。左上角的元素是第 (1,1) 个元素。

以下代码片段将对角线元素设置为 0：

```kotlin
// getters and setters
val m1_11: Double = m1.get(1, 1)
val m1_22: Double = m1.get(2, 2)
val m1_33: Double = m1.get(3, 3)
println(String.format("\nThe diagonal entries are: %f, %f, %f", m1_11, m1_22, m1_33))
// changing the diagonal to 0
println("\nChanging the diagonal to 0")
m1.set(1, 1, 0.0)
m1.set(2, 2, 0.0)
m1.set(3, 3, 0.0)
println(String.format("m1 = %s", m1)) // print out the matrix m1
```

输出如下：

在 NM Dev 中，矩阵上的算术运算——加法、减法和缩放——都是逐元素运算。其方法签名如下：

```
/**
 * this + that
 *
 * @param that 一个矩阵
 * @return {@code this} 与 {@code that} 的和
 */
@Override
public Matrix add(Matrix that);
/**
 * this - that
 *
 * @param that 一个矩阵
 * @return {@code this} 与 {@code that} 的差
 */
@Override
public Matrix minus(Matrix that);
/**
 * 用常数缩放该矩阵 A。
 *
 * @param c 一个 double 值
 * @return cA
 */
public Matrix scaled(double c);
```

下面是一个示例：

```
val m1: Matrix = DenseMatrix(
    arrayOf(
        doubleArrayOf(1.0, 2.0, 3.0),
        doubleArrayOf(4.0, 5.0, 6.0),
        doubleArrayOf(7.0, 8.0, 9.0)
    )
)
// m2 = -m1
val m2: Matrix = m1.scaled(-1.0)
// m3 = m1 + m2 = m1 - m1 = 0
val m3: Matrix = m1.add(m2)
// not a recommended usage
val isEqual1: Boolean = m3.equals(m3.ZERO())
println(isEqual1)
// recommended usage
val isEqual2: Boolean = MatrixPropertyUtils.areEqual(m3, m3.ZERO(), 1e-16)
println(isEqual2)
```

输出如下：

```
true
true
```

`ZERO()` 方法生成一个与调用实例维度相同的零矩阵。`equals()` 函数比较两个矩阵并检查它们的值是否相等。然而，不推荐使用此函数，因为理论上相同的两个矩阵在计算机中可能具有不同的表示。元素的值可能因微小差异而不同，`equals()` 函数会错误地将它们视为不同。检查两个矩阵值是否相等的正确方法是逐元素检查它们在某个阈值内是否相同。工具函数 `MatrixPropertyUtils.areEqual` 正是为此目的而设计。该工具函数相较于 `equals()` 方法的优势在于，你可以为浮点数比较输入一个容差阈值。其方法签名如下：

```
/**
 * 在指定精度下检查两个矩阵是否相等。
 * 两个矩阵相等当且仅当：
 *
 * 维度相同；
 * 所有元素都相等。
 *
 * @param A1      一个矩阵
 * @param A2      一个矩阵
 * @param epsilon 精度参数：当数值 |x| &le; &epsilon; 时，视为 0
 * @return {@code true} 如果所有元素逐项相等
 */
public static boolean areEqual(Matrix A1, Matrix A2, double epsilon)
```

### 2.2.3 转置

矩阵 `A` 的转置 `A^T = A'` 定义如下：

![$$ {\left({\boldsymbol{A}}^T\right)}_{ij}={\left(\boldsymbol{A}\right)}_{ij} $$](img/528380_1_En_2_Chapter_TeX_Equh.png)

也就是说，它交换了行和列。列变成行，行变成列。例如，对于以下矩阵：

![$$ \boldsymbol{A}=\left[\begin{array}{ccc}1&amp; 2&amp; 3\\ {}4&amp; 5&amp; 6\\ {}7&amp; 8&amp; 9\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equi.png)

其转置如下：

![$$ {\boldsymbol{A}}^T=\left[\begin{array}{ccc}1&amp; 4&amp; 7\\ {}2&amp; 5&amp; 8\\ {}3&amp; 6&amp; 9\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equj.png)

以下代码实现了这个例子：

```
val m1: Matrix = DenseMatrix(
    arrayOf(
        doubleArrayOf(1.0, 2.0, 3.0),
        doubleArrayOf(4.0, 5.0, 6.0),
        doubleArrayOf(7.0, 8.0, 9.0)
    )
)
println(String.format("m1 = %s", m1)) // print out the matrix m1
val m1t: Matrix = m1.t() // doing a transpose
println(String.format("m1t = %s", m1t)) // print out the transpose matrix
```

输出如下：

```
m1 = 3x3
[,1] [,2] [,3]
[1,] 1.000000, 2.000000, 3.000000,
[2,] 4.000000, 5.000000, 6.000000,
[3,] 7.000000, 8.000000, 9.000000,
m1t = 3x3
[,1] [,2] [,3]
[1,] 1.000000, 4.000000, 7.000000,
[2,] 2.000000, 5.000000, 8.000000,
[3,] 3.000000, 6.000000, 9.000000,
```

### 2.2.4 矩阵乘法

矩阵乘法是一种二元运算，用于从两个矩阵得到第三个矩阵。这第三个矩阵是前两个矩阵的乘积，称为*矩阵乘积*。如果 `A` 是一个 `m` × `n` 矩阵，`B` 是一个 `n` × `p` 矩阵，那么它们的矩阵乘积 `C` = `AB` 是一个 `m` × `p` 矩阵。矩阵 `A` 中每一行的 `m` 个元素与矩阵 `B` 中对应列的 `m` 个元素相乘，这些乘积之和即为矩阵 `C` = `AB` 的一个元素。参见图 2-11。数学表示如下：

![$$ {c}_{ij}={a}_{i1}{b}_{1j}+{a}_{i2}{b}_{2j}+\cdots +{a}_{in}{b}_{nj}=\sum \limits_{k=1}^n{a}_{ik}{b}_{kj} $$](img/528380_1_En_2_Chapter_TeX_Equk.png)

![](img/528380_1_En_2_Fig11_HTML.jpg)

数学乘法的图示。该方程展示了将 A 乘以 B 得到 C 的函数。

**图 2-11** 矩阵乘法

例如，对于如下矩阵：

![$$ \boldsymbol{A}=\left[\begin{array}{ccc}1&amp; 2&amp; 3\\ {}4&amp; 5&amp; 6\\ {}7&amp; 8&amp; 9\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equl.png)

可以得到：

![$$ {\boldsymbol{A}}^T\boldsymbol{A}=\left[\begin{array}{ccc}1&amp; 4&amp; 7\\ {}2&amp; 5&amp; 8\\ {}3&amp; 6&amp; 9\end{array}\right]\left[\begin{array}{ccc}1&amp; 2&amp; 3\\ {}4&amp; 5&amp; 6\\ {}7&amp; 8&amp; 9\end{array}\right]=\left[\begin{array}{ccc}66&amp; 78&amp; 90\\ {}78&amp; 93&amp; 108\\ {}90&amp; 108&amp; 126\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equm.png)

请注意，矩阵乘法不满足交换律，这意味着`AB`不一定等于`BA`。

## 代码示例

以下代码实现了这个例子：

```
val m1: Matrix = DenseMatrix(
arrayOf(
doubleArrayOf(1.0, 2.0, 3.0),
doubleArrayOf(4.0, 5.0, 6.0),
doubleArrayOf(7.0, 8.0, 9.0)
)
)
val m1t: Matrix = m1.t()
val m1tm1: Matrix = m1t.multiply(m1)
println(String.format("%s * \n%s = \n%s", m1t, m1, m1tm1))
```

输出如下：

```
3x3
[,1] [,2] [,3]
[1,] 1.000000, 4.000000, 7.000000,
[2,] 2.000000, 5.000000, 8.000000,
[3,] 3.000000, 6.000000, 9.000000,  *
3x3
[,1] [,2] [,3]
[1,] 1.000000, 2.000000, 3.000000,
[2,] 4.000000, 5.000000, 6.000000,
[3,] 7.000000, 8.000000, 9.000000,  =
3x3
[,1] [,2] [,3]
[1,] 66.000000, 78.000000, 90.000000,
[2,] 78.000000, 93.000000, 108.000000,
[3,] 90.000000, 108.000000, 126.000000,
```

## 矩阵乘法的重要性

矩阵乘法在科学计算中占据核心地位，应用范围极广。线性代数中的许多数值计算过程（例如，求解线性系统、矩阵求逆、因式分解和行列式）本质上都可以简化为矩阵乘法。因此，研究快速矩阵乘法算法以加速矩阵乘法（以及随之而来的其他数值计算过程）具有重大意义。根据我们的基准测试，NM Dev 在矩阵乘法（以及因此在线性代数方面）已经是速度最快的。参见图 2-12。

![](img/528380_1_En_2_Fig12_HTML.png)

相对时间与矩阵乘法的柱状图。Apache commons 3.5 和 colt 1.2.0 的操作性能最高，而 suanshu 3.3.0 的性能最低。

**图 2-12** NM Dev 快速矩阵乘法

NM Dev 实现了一种用于快速矩阵乘法的先进算法。我们简要描述这个矩阵乘法算法的实现，与经典 IJK 算法相比，它显著加速了稠密矩阵-矩阵乘法。

## 经典 IJK 算法

我们首先描述作为该快速算法比较基准的方法，即 IJK 算法。经典的 IJK 算法使用三层循环进行矩阵乘法，因此时间复杂度为 `O(n³)`。假设 `A` 是 `m × n`，`B` 是 `n × p`，`C` 是 `m × p`，则 IJK 算法如下：

```
for (i = 1; i <= m; i ++){
for (j = 1; j <= p; j ++){
for (k = 1; k <= n; k ++){
C[i,k] += A[i,j] * B[j,k];
}
}
}
```

在 NM Dev 中，这是并行实现的；最外层的循环传递给一个 `ParallelExecutor`。由于行数通常多于可用线程数，这种并行化的 IJK 算法的时间复杂度仍然与经典版本大致相同，为 `O(mnp)` 或当 `m` = `n` = `p` 时的立方时间 `O(n³)`。这是大多数其他 Java 库中的实现方式，而我们希望做得比这好得多。

## 快速算法：Strassen 算法

我们快速乘法算法的核心是 Strassen 算法，它将时间复杂度降低到 `O(n^log₂7)`。Strassen 算法（Strassen, 1969）基于以下分块矩阵乘法：

![$$ \left(\begin{array}{cc}{\boldsymbol{A}}_{\textbf{11}}& {\boldsymbol{A}}_{\textbf{12}}\\ {}{\boldsymbol{A}}_{\textbf{21}}& {\boldsymbol{A}}_{\textbf{22}}\end{array}\right)\left(\begin{array}{cc}{\boldsymbol{B}}_{\textbf{11}}& {\boldsymbol{B}}_{\textbf{12}}\\ {}{\boldsymbol{B}}_{\textbf{21}}& {\boldsymbol{B}}_{\textbf{22}}\end{array}\right)=\left(\begin{array}{cc}{\boldsymbol{A}}_{\textbf{11}}{\boldsymbol{B}}_{\textbf{11}}+{\boldsymbol{A}}_{\textbf{12}}{\boldsymbol{B}}_{\textbf{21}}& {\boldsymbol{A}}_{\textbf{11}}{\boldsymbol{B}}_{\textbf{12}}+{\boldsymbol{A}}_{\textbf{12}}{\boldsymbol{B}}_{\textbf{22}}\\ {}{\boldsymbol{A}}_{\textbf{21}}{\boldsymbol{B}}_{\textbf{11}}+{\boldsymbol{A}}_{\textbf{22}}{\boldsymbol{B}}_{\textbf{21}}& {\boldsymbol{A}}_{\textbf{21}}{\boldsymbol{B}}_{\textbf{12}}+{\boldsymbol{A}}_{\textbf{22}}{\boldsymbol{B}}_{\textbf{22}}\end{array}\right) $$](img/528380_1_En_2_Chapter_TeX_Equn.png)

计算此式的朴素方法涉及八次子矩阵乘法和四次加法。我们使用的 Strassen 算法的 Winograd 变体放弃了一次子矩阵乘法，转而进行 11 次额外的加法/减法，当子矩阵足够大时，这种方法更快（Winograd, 1971）。

算法运行如下（参见图 2-13）：

![](img/528380_1_En_2_Fig13_HTML.jpg)

Strassen 算法的图示。网络拓扑中的虚线表示子矩阵划分，而粗箭头表示乘法。

**图 2-13** Strassen 算法的可视化（Winograd 变体）

`M[1] = A[11]` `N[1] = B[11]`
`M[2] = A[12]` `N[2] = B[21]`
`M[3] = A[21] + A[22]` `N[3] = B[12] − B[11]`
`M[4] = M[3] − A[11]` `N[4] = B[22] − N[3]`
`M[5] = A[11] − A[21]` `N[5] = B[22] − B[12]`
`M[6] = A[12] − M[4]` `N[6] = B[22]`
`M[7] = A[22]` `N[7] = B[21] − N[4]`

1.  将 `A` 分割成四个大小相等的象限 `A[11]`, `A[12]`, `A[21]`, `A[22]`。对 `B` 做同样的处理。
2.  得到以下因子矩阵：

`C[11] = P[1] + P[2]` `C[22] = T[2] + P[3]`
`T[1] = P[1] + P[4]` `T[3] = T[1] + P[3]`
`T[2] = T[1] + P[5]` `C[12] = T[3] + P[6]`
`C[21] = T[2] + P[7]` ``
3.  对于 `i` ∈ {1, 2, ⋯, 7}，计算 `P[i] = M[i] × N[i]`。根据维度的不同，我们或者使用并行 IJK，或者递归调用 Strassen 算法。
4.  然后可以如下得到最终乘积 `C` = `A` × `B`。`T[i]` 是临时矩阵。


## 秩

矩阵 `A` 的列秩是 `A` 中线性无关列的最大数量。类似地，行秩是 `A` 中线性无关行的最大数量。矩阵的列秩和行秩总是相等的，因此可以简单地称为矩阵的秩 `rank(A)`。如果方阵的秩等于其列数（或行数），则称其为**满秩矩阵**。

示例如下：

```
A = [[1, 0, 1],
     [-2, -3, 1],
     [3, 3, 0]]
```

`A` 的秩为 2，因为前两列是线性无关的。然而，第三列是前两列的线性组合。具体来说，第三列等于第一列减去第二列。这是一个非满秩矩阵。

另一个例子：

```
A = [[1, 1, 0, 2],
     [-1, -1, 0, -2]]
```

`A` 的秩为 1，因为每一列都是第一列的标量倍数。

在 NM Dev 中，实用函数 `MatrixMeasure.rank` 用于计算矩阵的秩。以下代码计算了上述两个矩阵的秩：

```kotlin
val PRECISION: Double = 1e-15
val M1: Matrix = DenseMatrix(
arrayOf(
doubleArrayOf(1.0, 0.0, 1.0),
doubleArrayOf(-2.0, -3.0, 1.0),
doubleArrayOf(3.0, 3.0, 0.0)
)
)
// 计算 M1 的秩，将绝对值小于 PRECISION 的数字视为 0
val rank1: Int = MatrixMeasure.rank(M1, PRECISION)
println("秩 1: ${rank1}")
val M2: Matrix = DenseMatrix(
arrayOf(
doubleArrayOf(1.0, 1.0, 0.0, 2.0),
doubleArrayOf(-1.0, -1.0, 0.0, -2.0)
)
)
// 计算 M2 的秩，将绝对值小于 PRECISION 的数字视为 0
val rank2: Int = MatrixMeasure.rank(M2, PRECISION)
println("秩 2: ${rank2}")
```

输出结果如下：

```text

```

### 2.2.6 行列式

`Determinant` 是一个将 *n* × *n* 方阵 *A* 映射到标量的函数，通常记作 `|A|`。将矩阵 `A` 的元素记为 `a[ij]`，`N[ij]` 表示删除矩阵 `A` 的第 *i* 行和第 *j* 列后所得子矩阵的行列式。则矩阵 `A` 的行列式可按如下递推定义：

![$$ \left|\boldsymbol{A}\right|=\sum \limits_{j=1}^n{\left(-1\right)}^{i+j}{m}_{ij}{N}_{ij} $$](img/528380_1_En_2_Chapter_TeX_Equq.png)

例如，当 *n* = 2 时，可得：

![$$ \left|\begin{array}{cc}a&amp; b\\ {}c&amp; d\end{array}\right|= ad- bc $$](img/528380_1_En_2_Chapter_TeX_Equr.png)

类似地，当 *n* = 3 时，可得：

![$$ \left|\begin{array}{ccc}a&amp; b&amp; c\\ {}d&amp; e&amp; f\\ {}g&amp; h&amp; i\end{array}\right|=a\left|\begin{array}{cc}e&amp; f\\ {}h&amp; i\end{array}\right|-b\left|\begin{array}{cc}d&amp; f\\ {}g&amp; i\end{array}\right|+c\left|\begin{array}{cc}d&amp; e\\ {}g&amp; h\end{array}\right| $$](img/528380_1_En_2_Chapter_TeX_Equs.png)

到目前为止，我们忽略了 `A` 和/或 `B` 的行/列数为奇数的情况。处理这种情况有几种方法。例如，你可以静态填充矩阵，使得在递归传递到 IJK 之前维度始终为偶数（静态填充）；或者，你可以在维度为奇数时再进行填充（动态填充）。另一种选择是，先忽略多余的行/列，待算法完成后再对其进行处理。（换句话说，如果 `A` 有多余的一行或 `B` 有多余的一列，则使用相应的矩阵-向量运算来计算 `C` 剩余的行/列。如果 `A` 有多余的一列且 `B` 有多余的一行，则它们的乘积可以在之后累加到 `C` 上。）NM Dev 在这个实现中使用了这种方法，称为**动态剥离**。

就其本身而言，这种基础版的 Strassen 算法在待乘矩阵大致为方阵时表现良好。在实践中，你可能会遇到某个矩阵高度矩形化（例如，过瘦或过长）的情况。你可以通过将矩阵分割成近似方形的块，然后在子矩阵上使用 Strassen 算法来解决这个问题。分块方案的目的是避免产生长条或高条的块。参见图 2-14。

![](img/528380_1_En_2_Fig14_HTML.jpg)

分块与瓦片化示意图。第一和第三个水平块被标识为 `A11`、`A12` 和 `A13`，而第二和第四个水平块被标识为 `B11`、`B21` 和 `B31`。

**图 2-14** 分块与瓦片化。左图：严格的分块方案（产生了长条！）。右图：高效的分块方案（我们的实现）

图 2-15 至 2-18 中的图表展示了我们混合的 Block-Strassen 算法与并行 IJK 算法在一台配备 Intel® Core i5-3337U CPU @ 1.80 GHz、6GB RAM、运行 Java 1.8.0 update 40 的机器上的性能对比。

![](img/528380_1_En_2_Fig15_HTML.jpg)

时间与复杂度的折线图。最高峰值由并行 IJK 方法表示。

**图 2-15** 乘法时间（按方法分类）与复杂度 `m × n × p` 的关系

测试模式参照 D'Alberto & Nicolau (2007)。我们对随机矩阵 `A`（`m` × `n`）和 `B`（`n` × `p`）执行了 `C = A × B` 运算。针对 `S`³ 中的每个三元组 (`m`, `n`, `p`)，其中 `S` = {100, 500, 1000, 2500, 5000, 7500, 10000}。该乘法运算使用并行 IJK 执行三次，然后使用混合 Block-Strassen（HBS）算法再执行三次。比较并记录了每种方法的平均时间。见图 2-15。

图 2-15 展示了乘法时间与维度乘积 `m × n × p` 的关系。IJK 算法的乘法时间是 `O(mnp)`，我们的实验结果表表明，并行 IJK 和 HBS 算法的乘法时间都与复杂度呈现强线性关系。然而，HBS 算法的梯度显著更小。

最佳拟合线的梯度表明，当复杂度趋近于无穷大时（忽略内存限制），HBS 算法所需的时间将比 IJK 算法少 63.5%。不过，有几个数据点（例如 `m`、`n`、`p` ≥ 5000）甚至显示出更大的加速比。参见图 2-16 和 2-17。

![](img/528380_1_En_2_Fig17_HTML.jpg)

使用 HBS 算法节省的时间百分比矩阵。表格中不同值的阴影取决于颜色主题。

**图 2-17** 使用 HBS 与并行 IJK 相比的时间节省（占 P.IJK 时间的百分比）

![](img/528380_1_En_2_Fig16_HTML.jpg)

HBS 算法时间节省的矩阵表示。图中每个表格都给出了多个 `n` 值。

**图 2-16** 使用 HBS 与并行 IJK 相比的时间节省（单位：秒）

图 2-16 以秒为单位展示了 HBS 相较于并行 IJK 的时间节省，而图 2-17 则将其表示为并行 IJK 时间的百分比。每个表格对应特定的 `n` 值（`A` 的列数），并覆盖了 `m`（`A` 的行数）和 `p`（`B` 的列数）的取值范围。见图 2-18。

![](img/528380_1_En_2_Fig18_HTML.jpg)

矩阵中最大逐元素相对误差的表示。对于 `n` 值 00100 和 00500，相对误差显示为无。

**图 2-18** HBS 相比于 PIJK 的最大逐元素相对误差（单位：1e-15）

最后，还进行了一项精度测试，以解决人们对 Strassen 算法数值稳定性的担忧。图 2-18 展示了结果乘积矩阵的最大逐元素相对误差 `max|(C_IJK - C_HBS) / C_IJK|`（对于所有 `i`, `j`）。可以看到没有任何误差超过 `2 × 10^(−14)`。请注意，我们并未将 Strassen 算法完全递归到标量级别；当矩阵 `M[i]` 和 `N[i]` 过小时，会改用 IJK 算法。这减少了误差。这表明 HBS 算法非常精确，因此当速度是首要考虑因素时，它是通用场景下的一个强力候选。HBS 是 NM Dev 中矩阵乘法的默认实现。


## 2.2 逆矩阵与伪逆矩阵

![$$ = aei+ bfg+ cdh- ceg- bdi- afh $$](img/528380_1_En_2_Chapter_TeX_Equt.png)

示例如下：

![$$ \left|\begin{array}{ccc}2&amp; 1&amp; 2\\ {}3&amp; 2&amp; 2\\ {}1&amp; 2&amp; 3\end{array}\right|=5 $$](img/528380_1_En_2_Chapter_TeX_Equu.png)

在 NM Dev 中，实用函数 `MatrixMeasure.det` 可计算矩阵的行列式。以下代码计算上述矩阵的行列式：

```
val M1: Matrix = DenseMatrix(
arrayOf(
doubleArrayOf(2.0, 1.0, 2.0),
doubleArrayOf(3.0, 2.0, 2.0),
doubleArrayOf(1.0, 2.0, 3.0)
)
)
// 计算矩阵 M1 的行列式
val det: Double = MatrixMeasure.det(M1)
println("行列式: ${det}")
```

输出结果如下：

```
行列式: 5.0
```

### 2.2.1 逆矩阵

对于 *n* 阶方阵 `A`，如果存在一个 *n* 阶方阵 `B`，使得：

![$$ \boldsymbol{AB}=\boldsymbol{BA}={\boldsymbol{I}}_n $$](img/528380_1_En_2_Chapter_TeX_Equv.png)

其中 `I[n]` 是 *n* 阶单位矩阵，则称矩阵 `A` 是**可逆**的，矩阵 `B` 称为矩阵 `A` 的逆矩阵。通常将 `A` 的逆矩阵记作 `A^(−1)`，即 `AA^(−1) = A^(−1)A = I[n]`。只有方阵才可能有逆矩阵。一个矩阵可逆当且仅当其满秩。并非所有方阵都可逆。不可逆的矩阵称为**奇异矩阵**或**退化矩阵**。矩阵奇异当且仅当其行列式为 0。

例如，考虑以下矩阵：

![$$ \boldsymbol{A}=\left[\begin{array}{cc}-1&amp; \frac{3}{2}\\ {}1&amp; -1\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equw.png)

`A` 是可逆的。可以通过计算行列式 `|A| = − 0.5` 来验证。具体地，可得：

![$$ \left[\begin{array}{cc}-1&amp; \frac{3}{2}\\ {}1&amp; -1\end{array}\right]\left[\begin{array}{cc}2&amp; 3\\ {}2&amp; 2\end{array}\right]=\left[\begin{array}{cc}1&amp; 0\\ {}0&amp; 1\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equx.png)

在 NM Dev 中，`Inverse` 类构造一个新矩阵，该矩阵是可逆矩阵的逆矩阵。以下代码计算上述矩阵的逆矩阵：

```
// 计算可逆矩阵的逆矩阵
val A1: Matrix = DenseMatrix(
arrayOf(
doubleArrayOf(-1.0, 3.0 / 2),
doubleArrayOf(1.0, -1.0)
)
)
val det1: Double = MatrixMeasure.det(A1)
println("A1 的行列式 = " + det1)
val Ainv1: Matrix = Inverse(A1)
val I1: Matrix = A1.multiply(Ainv1)
println(String.format("%s * \n%s = \n%s", A1, Ainv1, I1))
```

输出结果如下：

```
A1 的行列式 = -0.5
2x2
[,1] [,2]
[1,] -1.000000, 1.500000,
[2,] 1.000000, -1.000000,  *
2x2
[,1] [,2]
[1,] 2.000000, 3.000000,
[2,] 2.000000, 2.000000,  =
2x2
[,1] [,2]
[1,] 1.000000, 0.000000,
[2,] 0.000000, 1.000000,
```

再考虑另一个矩阵：

![$$ \boldsymbol{A}=\left[\begin{array}{cc}2&amp; 4\\ {}5&amp; 10\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equy.png)

很容易检验该矩阵是奇异的，因为 `|A| = 0`，所以其逆矩阵不存在。

### 2.2.2 伪逆矩阵

摩尔和彭罗斯定义了一种广义逆的概念，称为**摩尔-彭罗斯逆**，简称**伪逆**，记作 `A^+`。伪逆具有以下四个定义性质：

1.  `AA^+A = A`
2.  `A^+AA^+ = A^+`
3.  `(AA^+)^H = AA^+`
4.  `(A^+A)^H = A^+A`

运算符 `A^H` 表示矩阵的埃尔米特转置（也称为**共轭转置**）。首先对矩阵进行转置，然后将矩阵元素替换为其复共轭，即将 `a + bi` 替换为 `a − bi`。具体地，一个 *m* × *n* 矩阵 `A` 的埃尔米特转置定义如下：

![$$ {\left({\boldsymbol{A}}^H\right)}_{ij}=\overline{{\boldsymbol{A}}_{ij}} $$](img/528380_1_En_2_Chapter_TeX_Equz.png)

上划线表示标量的复共轭。如果 `A` 中所有元素均为实数，则埃尔米特转置与普通转置相同。第三条和第四条性质意味着 `AA^+` 和 `A^+A` 是对称矩阵。矩阵的伪逆是唯一确定的。它始终存在，即便对于非方阵也是如此。如果矩阵可逆，那么伪逆与逆矩阵相同。

在 NM Dev 中，`PseudoInverse` 类构造一个新矩阵，该矩阵是某个矩阵（奇异矩阵或可逆矩阵）的伪逆。以下代码计算上述矩阵的伪逆：

```kotlin
// compute the pseudo-inverse of a non-invertible matrix
val A2: Matrix = DenseMatrix(
arrayOf(
doubleArrayOf(2.0, 4.0),
doubleArrayOf(5.0, 10.0)
)
)
val det2: Double = MatrixMeasure.det(A2)
println("det of A2 = " + det2)
val A2p: PseudoInverse = PseudoInverse(A2)
println("The pseudo inverse is")
println(A2p)
// the first property of pseudo-inverse
println("Should be the same as the matrix")
val A2_copy: Matrix = A2.multiply(A2p).multiply(A2)
println(A2_copy)
// the second property of pseudo-inverse
println("Should be the same as the pseudo inverse")
val A2p_copy: Matrix = A2p.multiply(A2).multiply(A2p)
println(A2p_copy)
```

输出结果如下：

```
det of A2 = 0.0
the pseudo inverse is
2x2
[,1] [,2]
[1,] 0.013793, 0.034483,
[2,] 0.027586, 0.068966,
should be the same as the matrix
2x2
[,1] [,2]
[1,] 2.000000, 4.000000,
[2,] 5.000000, 10.000000,
should be the same as the pseudo inverse
2x2
[,1] [,2]
[1,] 0.013793, 0.034483,
[2,] 0.027586, 0.068966,
```

再考虑一个非方阵的示例，如下所示：

![$$ \boldsymbol{A}=\left[\begin{array}{cc}1&amp; 0\\ {}0&amp; 1\\ {}0&amp; 1\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equaa.png)

其伪逆如下所示：

![$$ {\boldsymbol{A}}^{+}=\left[\begin{array}{ccc}1&amp; 0&amp; 0\\ {}0&amp; 0.5&amp; 0.5\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equab.png)

以下代码计算上述矩阵的伪逆：

```kotlin
// compute the pseudo-inverse of a non-square matrix
val A3: Matrix = DenseMatrix(
arrayOf(
doubleArrayOf(1.0, 0.0),
doubleArrayOf(0.0, 1.0),
doubleArrayOf(0.0, 1.0)
)
)
val A3p: PseudoInverse = PseudoInverse(A3)
println("The pseudo inverse is")
println(A3p)
// the first property of pseudo-inverse
println("Should be the same as the matrix")
val A3_copy: Matrix = A3.multiply(A3p).multiply(A3)
println(A3_copy)
// the second property of pseudo-inverse
println("Should be the same as the pseudo inverse")
val A3p_copy: Matrix = A3p.multiply(A3).multiply(A3p)
println(A3p_copy)
```

输出结果如下：

```
the pseudo inverse is
2x3
[,1] [,2] [,3]
[1,] 1.000000, 0.000000, 0.000000,
[2,] 0.000000, 0.500000, 0.500000,
should be the same as the matrix
3x2
[,1] [,2]
[1,] 1.000000, 0.000000,
[2,] 0.000000, 1.000000,
[3,] 0.000000, 1.000000,
should be the same as the pseudo inverse
2x3
[,1] [,2] [,3]
[1,] 1.000000, 0.000000, 0.000000,
[2,] 0.000000, 0.500000, 0.500000,
```

## 2.2.8 克罗内克积

克罗内克积（又称外积或矩阵直积），用 ⨂ 表示，是对两个任意大小矩阵进行运算，结果得到一个分块矩阵。具体来说，如果 `A` 是一个 *m* × *n* 矩阵，`B` 是一个 *p* × *q* 矩阵，那么克罗内克积 `A` ⨂ `B` 是一个 *pm* × *qn* 的分块矩阵，如下所示：

![$$ \boldsymbol{A}\otimes \boldsymbol{B}=\left[\begin{array}{ccc}{a}_{11}\boldsymbol{B}&amp; \cdots &amp; {a}_{1n}\boldsymbol{B}\\ {}\vdots &amp; \ddots &amp; \vdots \\ {}{a}_{m1}\boldsymbol{B}&amp; \cdots &amp; {a}_{mn}\boldsymbol{B}\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equac.png)

或者更明确地表示如下：

![](`img/528380_1_En_2_Figa_HTML.png`)

矩阵的表示。该矩阵给出了 a 乘以 b 这个问题的答案。

以下是一个例子：

![$$ \left[\begin{array}{cc}1&amp; 2\\ {}3&amp; 4\end{array}\right]\otimes \left[\begin{array}{cc}0&amp; 5\\ {}6&amp; 7\end{array}\right]=\left[\begin{array}{cc}1\left[\begin{array}{cc}0&amp; 5\\ {}6&amp; 7\end{array}\right]&amp; 2\left[\begin{array}{cc}0&amp; 5\\ {}6&amp; 7\end{array}\right]\\ {}3\left[\begin{array}{cc}0&amp; 5\\ {}6&amp; 7\end{array}\right]&amp; 4\left[\begin{array}{cc}0&amp; 5\\ {}6&amp; 7\end{array}\right]\end{array}\right]=\left[\begin{array}{cccc}1\times 0&amp; 1\times 5&amp; 2\times 0&amp; 2\times 5\\ {}1\times 6&amp; 1\times 7&amp; 2\times 6&amp; 2\times 7\\ {}3\times 0&amp; 3\times 5&amp; 4\times 0&amp; 4\times 5\\ {}3\times 6&amp; 3\times 7&amp; 4\times 6&amp; 4\times 7\end{array}\right]=\left[\begin{array}{cccc}0&amp; 5&amp; 0&amp; 10\\ {}6&amp; 7&amp; 12&amp; 14\\ {}0&amp; 15&amp; 0&amp; 20\\ {}18&amp; 21&amp; 24&amp; 28\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equad.png)

这是另一个例子：

在 NM Dev 中，`KroneckerProduct` 类构造一个新矩阵，该矩阵是两个矩阵的克罗内克积。以下代码计算前面示例的克罗内克积：

```kotlin
val A1 = DenseMatrix(
arrayOf(
doubleArrayOf(1.0, 2.0),
doubleArrayOf(3.0, 4.0)
)
)
val B1 = DenseMatrix(
arrayOf(
doubleArrayOf(0.0, 5.0),
doubleArrayOf(6.0, 7.0)
)
)
val C1 = KroneckerProduct(A1, B1)
println(String.format("%s ⊗ \n%s = \n%s", A1, B1, C1))
```

输出结果如下：

```
2x2
[,1] [,2]
[1,] 1.000000, 2.000000,
[2,] 3.000000, 4.000000,  ⊗
2x2
[,1] [,2]
[1,] 0.000000, 5.000000,
[2,] 6.000000, 7.000000,  =
4x4
[,1] [,2] [,3] [,4]
[1,] 0.000000, 5.000000, 0.000000, 10.000000,
[2,] 6.000000, 7.000000, 12.000000, 14.000000,
[3,] 0.000000, 15.000000, 0.000000, 20.000000,
[4,] 18.000000, 21.000000, 24.000000, 28.000000,
```

以下是另一个克罗内克积的例子：

```kotlin
val A2 = DenseMatrix(
    arrayOf(
        doubleArrayOf(1.0, -4.0, 7.0),
        doubleArrayOf(-2.0, 3.0, 3.0)
    )
)
val B2 = DenseMatrix(
    arrayOf(
        doubleArrayOf(8.0, -9.0, -6.0, 5.0),
        doubleArrayOf(1.0, -3.0, -4.0, 7.0),
        doubleArrayOf(2.0, 8.0, -8.0, -3.0),
        doubleArrayOf(1.0, 2.0, -5.0, -1.0)
    )
)
val C2 = KroneckerProduct(A2, B2)
println(String.format("%s ⊗ \n%s = \n%s", A2, B2, C2))
```

输出结果如下：

```
2x3
[,1] [,2] [,3]
[1,] 1.000000, -4.000000, 7.000000,
[2,] -2.000000, 3.000000, 3.000000,  ⊗
4x4
[,1] [,2] [,3] [,4]
[1,] 8.000000, -9.000000, -6.000000, 5.000000,
[2,] 1.000000, -3.000000, -4.000000, 7.000000,
[3,] 2.000000, 8.000000, -8.000000, -3.000000,
[4,] 1.000000, 2.000000, -5.000000, -1.000000,  =
8x12
[,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10] [,11] [,12]
[1,] 8.000000, -9.000000, -6.000000, 5.000000, -32.000000, 36.000000, 24.000000, -20.000000, 56.000000, -63.000000, -42.000000, 35.000000,
[2,] 1.000000, -3.000000, -4.000000, 7.000000, -4.000000, 12.000000, 16.000000, -28.000000, 7.000000, -21.000000, -28.000000, 49.000000,
[3,] 2.000000, 8.000000, -8.000000, -3.000000, -8.000000, -32.000000, 32.000000, 12.000000, 14.000000, 56.000000, -56.000000, -21.000000,
[4,] 1.000000, 2.000000, -5.000000, -1.000000, -4.000000, -8.000000, 20.000000, 4.000000, 7.000000, 14.000000, -35.000000, -7.000000,
[5,] -16.000000, 18.000000, 12.000000, -10.000000, 24.000000, -27.000000, -18.000000, 15.000000, 24.000000, -27.000000, -18.000000, 15.000000,
[6,] -2.000000, 6.000000, 8.000000, -14.000000, 3.000000, -9.000000, -12.000000, 21.000000, 3.000000, -9.000000, -12.000000, 21.000000,
[7,] -4.000000, -16.000000, 16.000000, 6.000000, 6.000000, 24.000000, -24.000000, -9.000000, 6.000000, 24.000000, -24.000000, -9.000000,
[8,] -2.000000, -4.000000, 10.000000, 2.000000, 3.000000, 6.000000, -15.000000, -3.000000, 3.000000, 6.000000, -15.000000, -3.000000,
```

## 2.3 矩阵分解

矩阵分解是将一个矩阵因式分解为多个（更简单的）矩阵的乘积。针对不同应用场景（如求解线性方程组和计算特征值），存在多种不同的矩阵分解方法。它们在精度和稳定性等方面具有不同的数值特性。

### 2.3.1 LU 分解

LU 分解（即 LU 因式分解）将一个矩阵分解为一个下三角矩阵和一个上三角矩阵的乘积。计算机通常使用 LU 分解来求解方形线性方程组（当变量数量与方程数量相同时）。它也是求逆矩阵或计算矩阵行列式时的关键步骤。具体而言，对于一个方阵 `A`，LU 分解会找到一个下三角矩阵 `L`（对角线上方所有元素均为零）和一个上三角矩阵 `U`（对角线下方所有元素均为零），使得：

![$$ \boldsymbol{A}=\boldsymbol{LU} $$](img/528380_1_En_2_Chapter_TeX_Equaf.png)

例如，对于一个 3 × 3 的矩阵 `A`，其 LU 分解如下所示：

![$$ \left[\begin{array}{ccc}{a}_{11}&amp; {a}_{12}&amp; {a}_{13}\\ {}{a}_{21}&amp; {a}_{22}&amp; {a}_{23}\\ {}{a}_{31}&amp; {a}_{32}&amp; {a}_{33}\end{array}\right]=\left[\begin{array}{ccc}{l}_{11}&amp; 0&amp; 0\\ {}{l}_{21}&amp; {l}_{22}&amp; 0\\ {}{l}_{31}&amp; {l}_{32}&amp; {l}_{33}\end{array}\right]\left[\begin{array}{ccc}{u}_{11}&amp; {u}_{12}&amp; {u}_{13}\\ {}0&amp; {u}_{22}&amp; {u}_{23}\\ {}0&amp; 0&amp; {u}_{33}\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equag.png)

这种形式有时可能无法实现。例如，如果 `a[11] = 0`，那么要么 `l[11] = 0`，要么 `u[11] = 0`。只需对 `A` 的行重新排序，使得置换后矩阵的第一个元素非零，即可解决此问题。后续分解步骤中的相同问题也可用同样的方法解决。因此，乘积有时会包含一个置换矩阵 `P`。具体来说：

![$$ \boldsymbol{PA}=\boldsymbol{LU} $$](img/528380_1_En_2_Chapter_TeX_Equah.png)

`P` 是一个行置换矩阵，当左乘 `A` 时，会对 `A` 的行进行重排。事实证明，所有方阵都可以用这种形式进行分解，并且该分解在实践中具有数值稳定性。这使得 LUP 分解成为一种实用的技术。

例如，假设现有以下分解：

![$$ \left[\begin{array}{ccc}0&amp; 0&amp; 1\\ {}1&amp; 0&amp; 0\\ {}0&amp; 0&amp; 0\end{array}\right]\left[\begin{array}{ccc}1&amp; 2&amp; 3\\ {}4&amp; 5&amp; 6\\ {}7&amp; 8&amp; 9\end{array}\right]=\left[\begin{array}{ccc}1&amp; 0&amp; 0\\ {}0.142857&amp; 1&amp; 0\\ {}0.571429&amp; 0.5&amp; 1\end{array}\right]\left[\begin{array}{ccc}7&amp; 8&amp; 9\\ {}0&amp; 0.857143&amp; 1.714286\\ {}0&amp; 0&amp; 0\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equai.png)

在 NM Dev 中，`LU` 类用于对方阵执行 LU 分解。它会计算矩阵 `L`、`U` 和 `P`。以下代码实现了前面的示例：

```kotlin
val A: Matrix = DenseMatrix(
    arrayOf(
        doubleArrayOf(1.0, 2.0, 3.0),
        doubleArrayOf(4.0, 5.0, 6.0),
        doubleArrayOf(7.0, 8.0, 9.0)
    )
)
// 执行 LU 分解
val lu: LU = LU(A)
val P: PermutationMatrix = lu.P()
println(String.format("P = %s", P))
```

输出结果如下：

```
P = 3x3
[,1] [,2] [,3]
[1,] 0.000000, 0.000000, 1.000000,
[2,] 1.000000, 0.000000, 0.000000,
[3,] 0.000000, 1.000000, 0.000000,
```

```kotlin
val L: LowerTriangularMatrix = lu.L()
println(String.format("L = %s", L))
```

输出结果如下：

```
L = 3x3
[,1] [,2] [,3]
[1,] 1.000000, 0.000000, 0.000000,
[2,] 0.142857, 1.000000, 0.000000,
[3,] 0.571429, 0.500000, 1.000000,
```

```kotlin
val U: UpperTriangularMatrix = lu.U()
println(String.format("U = %s", U))
```

输出结果如下：

```
U = 3x3
[,1] [,2] [,3]
[1,] 7.000000, 8.000000, 9.000000,
[2,] 0.000000, 0.857143, 1.714286,
[3,] 0.000000, 0.000000, 0.000000,
```

```kotlin
val PA: Matrix = P.multiply(A)
val LU: Matrix = L.multiply(U)
// 验证 PA = LU
println(String.format("%s = \n%s is %b",
    PA,
    LU,
    MatrixPropertyUtils.areEqual(PA, LU, 1e-14)))
```

输出结果如下：

```
3x3
[,1] [,2] [,3]
[1,] 7.000000, 8.000000, 9.000000,
[2,] 1.000000, 2.000000, 3.000000,
[3,] 4.000000, 5.000000, 6.000000,  =
3x3
[,1] [,2] [,3]
[1,] 7.000000, 8.000000, 9.000000,
[2,] 1.000000, 2.000000, 3.000000,
[3,] 4.000000, 5.000000, 6.000000,  is true
```

### 2.3.2 乔列斯基分解

如果 `A` 是一个对称（若 `A` 为复数，则为埃尔米特）正定矩阵，那么你可以使 `U` 成为 `L` 的共轭转置。也就是说，你可以将 `A` 写成以下形式：

![$$ \boldsymbol{A}={\boldsymbol{LL}}^H $$](img/528380_1_En_2_Chapter_TeX_Equaj.png)

这种分解被称为乔列斯基分解。乔列斯基分解是将一个埃尔米特正定矩阵 `A` 分解为一个下三角矩阵 `L` 与其共轭转置 `L`^`H`（如果矩阵是实数，则简单表示为转置 `L`^`T`）的乘积。`L`^`H` 是一个上三角矩阵。对于正定矩阵，乔列斯基分解总是存在且唯一的。此外，计算乔列斯基分解比计算 LU 分解更高效，数值上也更稳定。在适用的情况下，对于求解线性方程组，乔列斯基分解的效率大约是 LU 分解的两倍。

经典乔列斯基分解的一种变体是 LDL 分解。即：

![$$ \boldsymbol{A}={\boldsymbol{LDL}}^H $$](img/528380_1_En_2_Chapter_TeX_Equak.png)

其中 `L` 是单位下三角矩阵（对角线元素为 1），`D` 是一个对角矩阵（对角线外元素全为零）。也就是说，为了在分解中引入一个额外的对角矩阵 `D`，要求 `L` 的对角元素为 1（单位三角）。其主要优势在于，LDL 分解可以使用本质上相同的算法进行计算和应用，但避免了平方根运算。LDL 分解与经典乔列斯基分解的关系如下：

# 2.3.3 海森堡分解与三对角化

![$$ \boldsymbol{A}={\boldsymbol{L}\boldsymbol{D}\boldsymbol{L}}^H={\boldsymbol{L}\boldsymbol{D}}^{\frac{1}{2}}{\left({\boldsymbol{D}}^{\frac{1}{2}}\right)}^H{\boldsymbol{L}}^H={\boldsymbol{L}\boldsymbol{D}}^{\frac{1}{2}}{\left({\boldsymbol{L}\boldsymbol{D}}^{\frac{1}{2}}\right)}^H $$](img/528380_1_En_2_Chapter_TeX_Equal.png)

例如，假设你有以下矩阵：

![$$ \left[\begin{array}{ccc}4&amp; 12&amp; -16\\ {}12&amp; 37&amp; -43\\ {}-16&amp; -43&amp; 98\end{array}\right]=\left[\begin{array}{ccc}2&amp; 0&amp; 0\\ {}6&amp; 1&amp; 0\\ {}-8&amp; 5&amp; 3\end{array}\right]\left[\begin{array}{ccc}2&amp; 6&amp; -8\\ {}0&amp; 1&amp; 5\\ {}0&amp; 0&amp; 3\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equam.png)

实现乔列斯基分解的方法有很多种。在 NM Dev 中，`Cholesky` 接口代表了库中的多种算法。`Chol` 类封装了一些实现，可根据输入矩阵选择使用。默认选择是 `Cholesky-Banachiewicz` 类。`Cholesky-Banachiewicz` 算法从矩阵 `L` 的左上角开始，逐行计算矩阵。NM Dev 的 `Chol` 实现针对性能进行了高度优化，因为乔列斯基分解是如此基础的操作，并出现在许多线性代数计算中。对于大型矩阵，`Chol` 会并行运行 `Cholesky-Banachiewicz` 算法。对于稀疏矩阵（元素大部分为零的矩阵），它会运行一个稀疏版本。`Cholesky` 计算矩阵 `L`。以下代码实现了前面的示例：

```kotlin
val A: Matrix = DenseMatrix(
    arrayOf(
        doubleArrayOf(4.0, 12.0, -16.0),
        doubleArrayOf(12.0, 37.0, -43.0),
        doubleArrayOf(-16.0, -43.0, 98.0)
    )
)
val chol: Cholesky = Chol(A)
val L: LowerTriangularMatrix = chol.L()
val Lt: UpperTriangularMatrix = chol.L().t()
println(String.format("\nL = %s", L))
```

输出如下：

```
L = 3x3
[,1] [,2] [,3]
[1,] 2.000000, 0.000000, 0.000000,
[2,] 6.000000, 1.000000, 0.000000,
[3,] -8.000000, 5.000000, 3.000000,
```

```kotlin
println(String.format("Lt = %s", Lt))
```

输出如下：

```
Lt = 3x3
[,1] [,2] [,3]
[1,] 2.000000, 6.000000, -8.000000,
[2,] 0.000000, 1.000000, 5.000000,
[3,] 0.000000, 0.000000, 3.000000,
```

```kotlin
val LLt: Matrix = L.multiply(Lt)
// 验证 A = LLt
println(String.format("%s = \n%s is %b",
    A,
    LLt,
    MatrixPropertyUtils.areEqual(A, LLt, 1e-14)))
```

输出如下：

```
3x3
[,1] [,2] [,3]
[1,] 4.000000, 12.000000, -16.000000,
[2,] 12.000000, 37.000000, -43.000000,
[3,] -16.000000, -43.000000, 98.000000,  =
3x3
[,1] [,2] [,3]
[1,] 4.000000, 12.000000, -16.000000,
[2,] 12.000000, 37.000000, -43.000000,
[3,] -16.000000, -43.000000, 98.000000,  is true
```

在二维空间 ℝ² 中，通常使用 x 轴和 y 轴作为坐标或基。然而，这两个轴除了相互垂直（正交）之外，并无特殊之处。如果更方便，你同样可以选择另一组正交轴来标记坐标。参见图 2-19。

基变换的示意图。角度箭头由符号 `v1`、`u1`、`v2` 和 `u2` 标识。

**图 2-19** – 坐标变换或基变换

对于图 2-19 中的示例，假设 `{u1 = (1, 0), u2 = (0, 1)}` 是通常的 x-y 轴。`{v1, v2}` 是新坐标系或基，它是 `{u1, u2}` 旋转 θ 角度得到的。你可以用 `{u1, u2}` 来表示 `{v1, v2}`，如下所示：

`v1 = u1 * cos θ + u2 * sin θ`

`v2 = u1 * (-sin θ) + u2 * cos θ`

或者等效地以矩阵形式表示：

`[v1, v2]^T = [[cos θ, sin θ], [-sin θ, cos θ]] * [u1, u2]^T`

通过左乘基变换矩阵 `P = [[cos θ, sin θ], [-sin θ, cos θ]]`，你可以用原始坐标来表示新坐标。相同的基变换概念也适用于更高维度。

两个 *n* × *n* 方阵 `A` 和 `B` 是相似的，如果存在一个可逆矩阵 `P`，使得：

`B = P^(-1) * A * P`

`A` 和 `B` 表示相同的线性变换，但处于两个具有不同基的坐标系中。矩阵 `P` 是基变换矩阵。在基变换后的新坐标系中，`x` 的坐标如下所示：

`x' = P * x`

在新坐标系中，该变换可以写成：

`y' = A * x'`

在具有原始基的坐标系中，该变换为：

`y = B * x = P^(-1) * A * P * x`

从几何角度来看，相似变换分三步进行：变换到新基（`Px`），执行简单变换（`APx`），再变换回旧基（`P^(-1)APx`）。基变换（例如，将旋转轴与 z 轴正方向对齐）可以得出相同变换或矩阵的更简形式，例如 `A` 中有更多的零。相似矩阵具有相同的特征值、重数和行列式。

（上）海森堡矩阵是在第一次对角线以下元素均为零的矩阵。第一次对角线是指恰好位于对角线之下的元素。换句话说，海森堡矩阵仅在第一次对角线、对角线以及矩阵的上三角部分有非零元素。即，当 `i > j + 1` 时，`a[i][j] = 0`。例如，以下矩阵是一个海森堡矩阵：

`[[1, 4, 2, 3], [3, 4, 1, 7], [0, 2, 3, 4], [0, 0, 1, 3]]`

海森堡分解对于方阵 `A` 找到一个矩阵 `Q`，使得：

`Q' * A * Q = H`

`H` 是一个海森堡矩阵。`Q` 是一个正交矩阵，这意味着任意两行（或两列）是正交的，即它们的点积为 0。等价地，`Q^(-1) = Q'`。因此，`A` 和 `H` 是相似的。

在 NM Dev 中，`HessenbergDecomposition` 类对方阵执行海森堡分解。它计算 `Q` 和 `H` 矩阵。以下代码实现了这个示例：

`Q * A * Q^T = H`

其中：

`Q = [[1, 0, 0, 0], [0, -0.348743, 0.089751, -0.932911], [0, -0.464991, -0.880822, 0.089084], [0, -0.813733, 0.464862, 0.348914]]`

`A = [[1, 5, 7, 9], [3, 0, 6, 3], [4, 3, 1, 0], [7, 13, 2, 10]]`

`H = [[1, -12.322250, -1.533240, -0.900742], [-8.602325, 13.594595, -1.657558, 7.593136], [0, -5.460964, 2.073954, -1.404349], [0, 0, 4.989271, -4.668549]]`

# 矩阵分解示例：Hessenberg分解与三对角化

## Hessenberg 分解示例

```kotlin
println("Hessenberg decomposition")
val A: Matrix = DenseMatrix(
    arrayOf(
        doubleArrayOf(1.0, 5.0, 7.0, 9.0),
        doubleArrayOf(3.0, 0.0, 6.0, 3.0),
        doubleArrayOf(4.0, 3.0, 1.0, 0.0),
        doubleArrayOf(7.0, 13.0, 2.0, 10.0)
    )
)
val hessenberg: HessenbergDecomposition = HessenbergDecomposition(A)
val H: Matrix = hessenberg.H()
println(String.format(
    "H = \n%s is Hessenberg, %b",
    H,
    Hessenberg.isHessenberg(H, 0.0)))
```

输出如下：

```
H =
4x4
[,1] [,2] [,3] [,4]
[1,] 1.000000, -12.322250, -1.533240, -0.900742,
[2,] -8.602325, 13.594595, -1.657558, 7.593136,
[3,] 0.000000, -5.460964, 2.073954, -1.404349,
[4,] 0.000000, 0.000000, 4.989271, -4.668549,  is Hessenberg, true
```

```kotlin
val Q: Matrix = hessenberg.Q()
println(String.format(
    "Q = \n%s is orthogonal, %b",
    Q,
    MatrixPropertyUtils.isOrthogonal(Q, 1e-14)))
```

输出如下：

```
Q =
4x4
[,1] [,2] [,3] [,4]
[1,] 1.000000, 0.000000, 0.000000, 0.000000,
[2,] 0.000000, -0.348743, 0.089751, -0.932911,
[3,] 0.000000, -0.464991, -0.880822, 0.089084,
[4,] 0.000000, -0.813733, 0.464862, 0.348914,  is orthogonal, true
```

```kotlin
// 验证 Qt * A * Q = H
val QtAQ: Matrix = Q.t().multiply(A).multiply(Q)
println(String.format("%s = \n%s is %b",
    H,
    QtAQ,
    MatrixPropertyUtils.isOrthogonal(Q, 1e-14)))
```

输出如下：

```
4x4
[,1] [,2] [,3] [,4]
[1,] 1.000000, -12.322250, -1.533240, -0.900742,
[2,] -8.602325, 13.594595, -1.657558, 7.593136,
[3,] 0.000000, -5.460964, 2.073954, -1.404349,
[4,] 0.000000, 0.000000, 4.989271, -4.668549,  =
4x4
[,1] [,2] [,3] [,4]
[1,] 1.000000, -12.322250, -1.533240, -0.900742,
[2,] -8.602325, 13.594595, -1.657558, 7.593136,
[3,] 0.000000, -5.460964, 2.073954, -1.404349,
[4,] -0.000000, 0.000000, 4.989271, -4.668549,  is true
```

如果 `A` 是埃尔米特矩阵，该变换会将其化简为三对角形式。即：

![$$ {\boldsymbol{Q}}^{\prime}\boldsymbol{AQ}=\boldsymbol{T} $$](img/528380_1_En_2_Chapter_TeX_Equaw.png)

`A` 和 `T` 是相似的。`T` 是一个三对角矩阵。三对角矩阵的非零元素仅位于主对角线、主对角线下方第一条次对角线和主对角线上方第一条超对角线上。也就是说，当 `i > j + 1` 或 `i < j - 1` 时，`a[ij] = 0`。其形式如下：

![$$ T=\left[\begin{array}{ccccc}{a}_1& {b}_1& & & \\ {}{c}_1& {a}_2& {b}_2& & \\ {}& {c}_2& \ddots & \ddots & \\ {}& & \ddots & \ddots & {b}_{n-1}\\ {}& & & {c}_{n-1}& {a}_n\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equax.png)

## 三对角化示例

在 NM Dev 中，`TriDiagonalization` 类用于对对称方阵进行三对角化。它会计算出 `Q` 和 `T` 矩阵。以下代码实现了此示例：

![$$ {\displaystyle \begin{array}{c}\left\lceil \begin{array}{cccc}1& 0& 0& 0\\ {}0& -0.40161& 0.601902& -0.690235\\ {}0& -0.562254& -0.756975& -0.332956\\ {}0& -0.722897& 0.254368& 0.64243\end{array}\right\rceil \times \left\lceil \begin{array}{cccc}1& 5& 7& 9\\ {}5& 0& 3& 13\\ {}7& 3& 1& 2\\ {}9& 13& 2& 10\end{array}\right\rceil \\ {}\times \left\lceil \begin{array}{cccc}1& 0& 0& 0\\ {}0& -0.40161& -0.562254& -0.722897\\ {}0& 0.601902& -0.756975& 0.254368\\ {}0& -0.690235& -0.332956& 0.64243\end{array}\right\rceil \\ {}=\left\lceil \begin{array}{cccc}1& -12.4499& 0& 0\\ {}-12.4499& 16.070968& -7.692568& 0\\ {}0& -7.692568& 1.696818& 4.45481\\ {}0& 0& 4.45481& -6.767786\end{array}\right\rceil \end{array}} $$](img/528380_1_En_2_Chapter_TeX_Equay.png)

```kotlin
println("三对角化")
// 定义一个对称矩阵
val S: Matrix = SymmetricMatrix(
    arrayOf(
        doubleArrayOf(1.0),
        doubleArrayOf(5.0, 0.0),
        doubleArrayOf(7.0, 3.0, 1.0),
        doubleArrayOf(9.0, 13.0, 2.0, 10.0)
    )
)
val triDiagonalization: TriDiagonalization = TriDiagonalization(S)
val T: Matrix = triDiagonalization.T()
println(String.format(
    "T = \n%s 是三对角矩阵, %b",
    T,
    MatrixPropertyUtils.isTridiagonal(T, 1e-14)))
```

输出如下：

# 2.3.4 QR 分解

QR 分解（或称 QR 因式分解）是将矩阵 `A` 分解为以下乘积形式的一种分解方法：

![$$ \boldsymbol{A}=\boldsymbol{QR} $$](img/528380_1_En_2_Chapter_TeX_Equaz.png)

其中，`Q` 是正交矩阵（其列向量为单位向量，且满足 `Q^(-1) = Q'`），`R` 是上三角矩阵。如果 `A` 可逆，并且要求 `R` 的对角线元素为正（即 `R` 中的对角线元素均为正数），则该分解是唯一的。如果 `A` 具有 *n* 个线性无关的列，则 `Q` 的前 *n* 列构成 `A` 列空间的一组标准正交基。更一般地，对于任意 1 ≤ *k* ≤ *n*，`Q` 的前 *k* 列构成 `A` 的前 *k* 列张成空间的一组标准正交基。`A` 的任意第 *k* 列仅依赖于 `Q` 的前 *k* 列这一事实，决定了 `R` 的三角形式。

有多种方法可实现 QR 分解，例如通过 Gram-Schmidt 过程（`GramSchmidt` 类）、Householder 变换（`Householder` 类）或 Givens 旋转（`GivensMatrix` 类）。在 NM Dev 中，`QRDecomposition` 接口代表了不同 QR 算法的实现。其签名如下：

```java
public interface QRDecomposition {
    /**
     * 获取 P，即 QR 分解中的置换矩阵。
     *
     * @return P
     */
    public PermutationMatrix P();

    /**
     * 获取 QR 分解中的正交矩阵 Q，即 A =
     * QR。Q 的维度为 m x n，与待正交化的矩阵 A 相同。
     *
     * @return Q
     */
    public Matrix Q();

    /**
     * 获取 QR 分解中的上三角矩阵 R，即 A =
     * QR。R 的维度为 n x n，是一个方阵。
     *
     * @return R
     */
    public UpperTriangularMatrix R();

    /**
     * 获取通过 QR 分解计算出的 A 的数值秩。
     * 数值秩的判定需要一个准则来判断某个值何时应被视为零，因此需要一个精度参数。
     * 这是一个实践性的选择，既取决于矩阵本身，也取决于应用场景。
     * 例如，对于一个具有较大第一特征向量的矩阵，我们应相应降低精度来计算秩。
     *
     * @return A 的秩
     */
    public int rank();

    /**
     * 获取方阵 Q。这是 QR 分解中 Q 矩阵的任意正交补全。
     * 其维度为 m x m（方阵）。我们有 A = sqQ * tallR。
     *
     * @return 方阵 Q
     */
    public Matrix squareQ();

    /**
     * 获取高矩阵 R。这是通过在 QR 分解中上三角方阵 R 的下方
     * 绑定零行来补全得到的。其维度为 m x n。它可能不是方阵。
     * 我们有 A = sqQ * tallR。
     *
     * @return 高矩阵 R
     */
    public Matrix tallR();
}
```

到目前为止讨论的分解方法仅适用于方阵。QR 分解既适用于方阵，也适用于高矩阵，不过对后者略有不同。具体来说，对于一个 *m* × *n* 且 *m* ≥ *n* 的矩阵 `A`，可以执行以下操作：

![$$ \boldsymbol{A}=\boldsymbol{Q}\boldsymbol{R}=\boldsymbol{Q}\left[\begin{array}{c}{\boldsymbol{R}}_{\textbf{1}}\\ {}\textbf{0}\end{array}\right]=\left[{\boldsymbol{Q}}_{\textbf{1}}\kern0.5em {\boldsymbol{Q}}_{\textbf{2}}\right]\left[\begin{array}{c}{\boldsymbol{R}}_{\textbf{1}}\\ {}\textbf{0}\end{array}\right]={\boldsymbol{Q}}_{\textbf{1}}{\boldsymbol{R}}_{\textbf{1}} $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equba.png)

如果线性代数问题的约束条件不允许将一般矩阵方便地化简为三角矩阵，那么将其化简为海森伯格形式通常是次优选择。事实上，任何矩阵都可以在有限步数内化简为海森伯格形式。随后，可以通过迭代过程（如移位 QR 分解）将海森伯格矩阵进一步化简为三角矩阵。在特征值算法中，海森伯格矩阵可以通过结合缩减步骤的移位 QR 分解进一步化简为三角矩阵。将一般矩阵先化简为海森伯格矩阵，再进一步化简为三角矩阵，而不是直接将一般矩阵化简为三角矩阵，通常可以节省 QR 算法在特征值问题中涉及的算术运算量。

`SymmetricMatrix` 类根据矩阵下三角部分的数据构造一个对称矩阵。其签名如下：

```java
/**
* 根据一个二维 {@code double[][]} 数组构造一个对称矩阵。
* 该数组仅指定整个矩阵的下三角部分（含主对角线）。
* 例如，
*
*         new double[][]{
*                  {1},
*                  {2, 3},
*                  {4, 5, 6},
*                  {7, 8, 9, 10},
*                  {11, 12, 13, 14, 15}});
*
* 会得到
* \[
* \begin{bmatrix}
* 1 & 2 & 4 & 7 & 11\\
* 2 & 3 & 5 & 8 & 12\\
* 4 & 5 & 6 & 9 & 13\\
* 7 & 8 & 9 & 10 & 14\\
* 11 & 12 & 13 & 14 & 15
* \end{bmatrix}
* \] * 为便于观察，此构造函数使用下三角而非上三角表示。
*
* @param data 下三角规范
*/
public SymmetricMatrix(double[][] data)
```

以下是演示 QR 分解的一些代码示例及其输出：

```
T = 4x4
[,1] [,2] [,3] [,4]
[1,] 1.000000, -12.449900, 0.000000, 0.000000,
[2,] -12.449900, 16.070968, -7.692568, 0.000000,
[3,] 0.000000, -7.692568, 1.696818, 4.454810,
[4,] 0.000000, 0.000000, 4.454810, -6.767786,  是三对角矩阵, true
val Q: Matrix = triDiagonalization.Q()
println(String.format(
"Q = \n%s 是正交矩阵, %b",
Q,
MatrixPropertyUtils.isOrthogonal(Q, 1e-14)))
```

输出如下：

```
Q = 4x4
[,1] [,2] [,3] [,4]
[1,] 1.000000, 0.000000, 0.000000, 0.000000,
[2,] 0.000000, -0.401610, 0.601902, -0.690235,
[3,] 0.000000, -0.562254, -0.756975, -0.332956,
[4,] 0.000000, -0.722897, 0.254368, 0.642430,  是正交矩阵, true
// 验证 Qt * A * Q = T
val QtSQ: Matrix = Q.t().multiply(S).multiply(Q)
println(String.format("%s = \n%s 是 %b",
T,
QtSQ,
MatrixPropertyUtils.areEqual(QtSQ, T, 1e-13)))
```

输出如下：

```
4x4
[,1] [,2] [,3] [,4]
[1,] 1.000000, -12.449900, 0.000000, 0.000000,
[2,] -12.449900, 16.070968, -7.692568, 0.000000,
[3,] 0.000000, -7.692568, 1.696818, 4.454810,
[4,] 0.000000, 0.000000, 4.454810, -6.767786,  =
4x4
[,1] [,2] [,3] [,4]
[1,] 1.000000, -12.449900, -0.000000, 0.000000,
[2,] -12.449900, 16.070968, -7.692568, 0.000000,
[3,] -0.000000, -7.692568, 1.696818, 4.454810,
[4,] 0.000000, 0.000000, 4.454810, -6.767786,  是 true
```

`Q` 是一个 `m` × `m` 的矩阵。`R` 是一个 `m` × `n` 的上三角矩阵。`R` 的底部 (`m` - `n`) 行全为零。`Q[1]` 是一个 `m` × `n` 的矩阵，而 `Q[2]` 是一个 `m` × (`m` - `n`) 的矩阵。`Q[1]` 和 `Q[2]` 的列向量都是正交的。`R[1]` 是一个 `n` × `n` 的矩阵。`Q()` 函数计算 `m` × `n` 的 `Q[1]` 矩阵。`R()` 函数计算 `n` × `n` 的 `R[1]` 矩阵。`squareQ()` 函数计算 `m` × `m` 的 `Q` 矩阵。`tallR()` 函数计算 `m` × `n` 的高矩阵 `R`。

NM Dev 类 `QR` 是一个封装类，用于执行 QR 分解。默认情况下，它使用 Householder QR 算法。与 Gram-Schmidt 过程相比，Householder 反射具有更好的数值稳定性。以下代码实现了此示例：

![$$ \left[\begin{array}{cc}3& 2\\ {}1& 2\end{array}\right]=\left[\begin{array}{cc}0.948683& -0.316228\\ {}0.316228& 0.948683\end{array}\right]\left[\begin{array}{cc}3.162278& 2.529822\\ {}0& 1.264911\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equbb.png)

```kotlin
val A: Matrix = DenseMatrix(
    arrayOf(
        doubleArrayOf(3.0, 2.0),
        doubleArrayOf(1.0, 2.0)
    )
)
// 使用 Householder QR 算法
val qr1: QRDecomposition = HouseholderQR(A, 0.0)
println("\nRank = " + qr1.rank())
val Q1: Matrix = qr1.Q()
println(String.format(
    "\nQ = \n%s is orthogonal, %b",
    Q1,
    MatrixPropertyUtils.isOrthogonal(Q1, 1e-15)))
val R1: UpperTriangularMatrix = qr1.R()
println("\nR = \n" + R1)
```

输出如下：

```
rank = 2
Q =
2x2
[,1] [,2]
[1,] -0.948683, -0.316228,
[2,] -0.316228, 0.948683,  is orthogonal, true
R =
2x2
[,1] [,2]
[1,] -3.162278, -2.529822,
[2,] 0.000000, 1.264911,
// 验证 Q1R1 = A
val Q1R1: Matrix = Q1.multiply(R1)
println(String.format("\n%s = \n%s is %b",
    A,
    Q1R1,
    MatrixPropertyUtils.areEqual(Q1R1, A, 1e-13)))
```

输出如下：

```
2x2
[,1] [,2]
[1,] 3.000000, 2.000000,
[2,] 1.000000, 2.000000,  =
2x2
[,1] [,2]
[1,] 3.000000, 2.000000,
[2,] 1.000000, 2.000000,  is true
// 使用 Gram-Schmidt QR 算法
val qr2: QRDecomposition = GramSchmidt(A)
println("\nRank = " + qr2.rank())
```

输出如下：

```
rank = 2
val Q2: Matrix = qr2.Q()
println(String.format(
    "\nQ = \n%s is orthogonal, %b",
    Q2,
    MatrixPropertyUtils.isOrthogonal(Q2, 1e-15)))
val R2: UpperTriangularMatrix = qr2.R()
println("\nR = \n" + R2)
```

输出如下：

```
Q =
2x2
[,1] [,2]
[1,] 0.948683, -0.316228,
[2,] 0.316228, 0.948683,  is orthogonal, true
R =
2x2
[,1] [,2]
[1,] 3.162278, 2.529822,
[2,] 0.000000, 1.264911,
// 验证 Q2R2 = A
val Q2R2: Matrix = Q2.multiply(R2)
println(String.format("%s = \n%s is %b",
    A,
    Q2R2,
    MatrixPropertyUtils.areEqual(Q2R2, A, 1e-13)))
```

输出如下：

```
2x2
[,1] [,2]
[1,] 3.000000, 2.000000,
[2,] 1.000000, 2.000000,  =
2x2
[,1] [,2]
[1,] 3.000000, 2.000000,
[2,] 1.000000, 2.000000,  is true
```

下面是一个高矩阵的示例：

![$$ \left[\begin{array}{ccc}1& 2& 3\\ {}6& 7& 8\\ {}11& 12& 13\\ {}16& 17& 18\\ {}21& 22& 23\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equbc.png)

![$$ =\left[\begin{array}{ccc}-0.034199& -0.773841& -0.359539\\ {}-0.205196& -0.507833& -0.031675\\ {}-0.376192& -0.241825& 0.868055\\ {}-0.547188& 0.024183& -0.202929\\ {}-0.718185& 0.290191& -0.273913\end{array}\right]\left[\begin{array}{ccc}-29.240383& -31.121343& -33.002304\\ {}0& -1.209127& -2.418254\\ {}0& 0& 0\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equbd.png)

![$$ =\left[\begin{array}{ccccc}-0.034199& -0.773841& -0.359539& -0.365120& -0.370701\\ {}-0.205196& -0.507833& -0.031675& 0.361174& 0.754023\\ {}-0.376192& -0.241825& 0.868055& -0.145512& -0.159079\\ {}-0.547188& 0.024183& -0.202929& 0.667982& -0.461107\\ {}-0.718185& 0.290191& -0.273913& -0.518524& 0.236864\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Eqube.png)

![$$ \times \left[\begin{array}{ccc}-29.240383& -31.121343& -33.002304\\ {}0& -1.209127& -2.418254\\ {}0& 0& 0\\ {}0& 0& 0\\ {}0& 0& 0\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equbf.png)

解决该问题的 NM Dev 代码如下：

### 2.3.5 特征值分解

给定一个线性变换或其矩阵 `A`，`A` 的一个特征向量 `v` 是满足以下条件的向量：

![$$ \boldsymbol{Av}=\lambda \boldsymbol{v} $$](img/528380_1_En_2_Chapter_TeX_Equbg.png)

`λ` 被称为 `A` 的一个特征值，对应于特征向量 `v`。

从几何角度看，这意味着当对向量 `v` 施加线性变换 `A` 时，该变换只会拉伸（`λ` > 1）或压缩（`λ` < 1）该向量，而不会改变其方向（当 `λ` > 0 时），除非将其翻转至相反方向（`λ` < 0）。`Av` 与 `v` 平行。参见图 2-20。

![](img/528380_1_En_2_Fig20_HTML.jpg)

特征向量示意图。一个直角处的对角箭头表示方程 `AX` 等于 `λX`。

图 2-20

特征向量与特征值

为了理解其重要性，考虑一个启发性的例子。考虑 `ℝ²` 中的这个对角矩阵：

![$$ \boldsymbol{A}=\left[\begin{array}{cc}{a}_{11}&amp; 0\\ {}0&amp; {a}_{22}\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equbh.png)

如果将 `A` 乘以 x 轴 `u₁ = [1, 0]^T`，得到：

![$$ {\boldsymbol{Au}}_{\textbf{1}}=\left[\begin{array}{cc}{a}_{11}&amp; 0\\ {}0&amp; {a}_{22}\end{array}\right]\left[\begin{array}{c}1\\ {}0\end{array}\right]=\left[\begin{array}{c}{a}_{11}\\ {}0\end{array}\right]={a}_{11}\left[\begin{array}{c}1\\ {}0\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equbi.png)

`Au₁` 与 `u₁` 平行，因此 x 轴是一个特征向量。对应的特征值是 `a₁₁`。

同样地，如果将 `A` 乘以 y 轴 `u₂ = [0, 1]^T`，得到：

![$$ {\boldsymbol{Au}}_{\textbf{2}}=\left[\begin{array}{cc}{a}_{11}&amp; 0\\ {}0&amp; {a}_{22}\end{array}\right]\left[\begin{array}{c}0\\ {}1\end{array}\right]=\left[\begin{array}{c}0\\ {}{a}_{22}\end{array}\right]={a}_{22}\left[\begin{array}{c}0\\ {}1\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equbj.png)

# QR 分解示例

```kotlin
val A: Matrix = DenseMatrix(
arrayOf(
doubleArrayOf(1.0, 2.0, 3.0),
doubleArrayOf(6.0, 7.0, 8.0),
doubleArrayOf(11.0, 12.0, 13.0),
doubleArrayOf(16.0, 17.0, 18.0),
doubleArrayOf(21.0, 22.0, 23.0)
)
)
val qr: QRDecomposition = QR(A, 1e-14)
println("\nRank = " + qr.rank())
val Q1: Matrix = qr.Q()
println("\nQ1 = \n" + Q1)
val R1: UpperTriangularMatrix = qr.R()
println("\nR1 = \n" + R1)
```

输出结果如下：

```
rank = 2
Q1 =
5x3
[,1] [,2] [,3]
[1,] -0.034199, -0.773841, -0.359539,
[2,] -0.205196, -0.507833, -0.031675,
[3,] -0.376192, -0.241825, 0.868055,
[4,] -0.547188, 0.024183, -0.202929,
[5,] -0.718185, 0.290191, -0.273913,
R1 =
3x3
[,1] [,2] [,3]
[1,] -29.240383, -31.121343, -33.002304,
[2,] 0.000000, -1.209127, -2.418254,
[3,] 0.000000, 0.000000, 0.000000,
```

验证 `Q1R1 = A`：

```kotlin
val Q1R1: Matrix = Q1.multiply(R1)
println(String.format("%s = \n%s is %b",
A,
Q1R1,
MatrixPropertyUtils.areEqual(Q1R1, A, 1e-13)))
val Q: Matrix = qr.squareQ()
println("\nQ = \n" + Q)
val R: Matrix = qr.tallR()
println("\nR = \n" + R)
```

输出结果如下：

```
5x3
[,1] [,2] [,3]
[1,] 1.000000, 2.000000, 3.000000,
[2,] 6.000000, 7.000000, 8.000000,
[3,] 11.000000, 12.000000, 13.000000,
[4,] 16.000000, 17.000000, 18.000000,
[5,] 21.000000, 22.000000, 23.000000,  =
5x3
[,1] [,2] [,3]
[1,] 1.000000, 2.000000, 3.000000,
[2,] 6.000000, 7.000000, 8.000000,
[3,] 11.000000, 12.000000, 13.000000,
[4,] 16.000000, 17.000000, 18.000000,
[5,] 21.000000, 22.000000, 23.000000,  is true
Q =
5x5
[,1] [,2] [,3] [,4] [,5]
[1,] -0.034199, -0.773841, -0.359539, -0.365120, -0.370701,
[2,] -0.205196, -0.507833, -0.031675, 0.361174, 0.754023,
[3,] -0.376192, -0.241825, 0.868055, -0.145512, -0.159079,
[4,] -0.547188, 0.024183, -0.202929, 0.667982, -0.461107,
[5,] -0.718185, 0.290191, -0.273913, -0.518524, 0.236864,
R =
5x3
[,1] [,2] [,3]
[1,] -29.240383, -31.121343, -33.002304,
[2,] 0.000000, -1.209127, -2.418254,
[3,] 0.000000, 0.000000, 0.000000,
[4,] 0.000000, 0.000000, 0.000000,
[5,] 0.000000, 0.000000, 0.000000,
```

验证 `QR = A`：

```kotlin
val QR: Matrix = Q.multiply(R)
println(String.format("%s = \n%s is %b",
A,
QR,
MatrixPropertyUtils.areEqual(QR, A, 1e-13)))
```

输出结果如下：

```
5x3
[,1] [,2] [,3]
[1,] 1.000000, 2.000000, 3.000000,
[2,] 6.000000, 7.000000, 8.000000,
[3,] 11.000000, 12.000000, 13.000000,
[4,] 16.000000, 17.000000, 18.000000,
[5,] 21.000000, 22.000000, 23.000000,  =
5x3
[,1] [,2] [,3]
[1,] 1.000000, 2.000000, 3.000000,
[2,] 6.000000, 7.000000, 8.000000,
[3,] 11.000000, 12.000000, 13.000000,
[4,] 16.000000, 17.000000, 18.000000,
[5,] 21.000000, 22.000000, 23.000000,  is true
```

# 特征分解与对角化

`A u₂` 与 `u₂` 平行，因此 y 轴是一个特征向量。对应的特征值是 `a₂₂`。实际上，在所有（无限多个）其他可能的向量中，只有这两个向量成立。也就是说，对于对角矩阵，其特征向量就是其基向量。反之亦然。对于任何可对角化的矩阵，如果你在以其特征向量构成的新基下表示该矩阵，那么在新坐标系中的矩阵就是一个对角矩阵。

## 数学定义

数学上，一个 `n × n` 的方阵 `A` 是可对角化的，如果存在一个 `n × n` 的方阵 `Q` 使得：

![A=QDQ^{-1}](img/528380_1_En_2_Chapter_TeX_Equbk.png)

或者等价地：

![D=Q^{-1}AQ](img/528380_1_En_2_Chapter_TeX_Equbl.png)

`Q` 是正交的，因此构成一组基。其列向量是 `A` 的特征向量，并且它们都是线性无关的。`D` 是一个对角矩阵，其对角线元素是对应的特征值。这称为矩阵 `A` 的特征值分解。这种分解可以从特征向量的基本性质推导出来：

![Av = λv](img/528380_1_En_2_Chapter_TeX_Equbm.png)

![AQ = QD](img/528380_1_En_2_Chapter_TeX_Equbn.png)

![A = QDQ^{-1}](img/528380_1_En_2_Chapter_TeX_Equbo.png)

## 应用：主成分分析

寻找特征值和特征向量非常重要，因为它们有着广泛的应用，例如在稳定性分析、振动分析、原子轨道和人脸识别中。

在机器学习或一般数据分析中，一个重要应用是降维或主成分分析（PCA）。PCA 是计算主成分并利用它们对数据进行基变换的过程，有时只使用前几个主成分而忽略其余部分。直观上，PCA 可以被认为是对数据拟合一个 *p* 维椭球体，通过旋转坐标轴，使得数据的大部分方差只落在少数几个坐标轴上。如果椭球体的一个（旋转后的）坐标轴很小，那么沿着该坐标轴的方差也很小。你可以移除这个维度，从而有效地将数据从高维空间投影到低维空间。

要找到椭球体的新坐标轴，你需要计算数据的协方差矩阵，然后计算该协方差矩阵的特征值和相应的特征向量。接着，你必须对每个正交的特征向量进行归一化，将它们转化为单位向量。完成这些步骤后，每个相互正交的单位特征向量都可以被解释为拟合数据椭球体的一个坐标轴。这种基的选择会将协方差矩阵变换为对角化形式，其中对角线元素代表每个坐标轴的方差。每个特征向量所代表的方差比例，可以通过将该特征向量对应的特征值除以所有特征值之和来计算。参见图 2-21。

![](img/528380_1_En_2_Fig21_HTML.png)

PCA 和降维的图。图中的两个箭头证实了保留新坐标轴的理由。

**图 2-21** PCA 和降维

## 特征分解算法与实现

有几种进行特征分解的方法，例如通过特征多项式（针对小矩阵）、`DQDS`算法（针对三对角矩阵）、`MR3`算法（针对对称矩阵）和`QR`算法（针对一般矩阵）。所有这些算法以及变换的实现都非常复杂。NM Dev 类 `EigenDecomposition` 封装了所有这些算法，并根据输入的矩阵自动应用合适的算法。其签名如下：

```java
/**
* 对方阵进行特征分解。
*
* @param A       一个可对角化的方阵
* @param epsilon 精度参数：当数值 |x| ≤ ε 时，视其为 0
*/
public EigenDecomposition(Matrix A, double epsilon)
/**
* 对方阵进行特征分解。
*
* @param A 一个可对角化的方阵
*/
public EigenDecomposition(Matrix A)
/**
* 获取对角矩阵 D，满足 Q * D * Q' = A。
*
* 注意，目前仅支持实数特征值。
*
* @return D
*/
public DiagonalMatrix D()
/**
* 获取矩阵 Q，满足 Q * D * Q' = A。
*
* 注意，目前仅支持实数特征值。
*
* @return Q
*/
public Matrix Q()
/**
* 获取矩阵 Q'，满足 Q * D * Q' = A。
*
* 注意，目前仅支持实数特征值。
*
* @return {@code Q.t()}
*/
public Matrix Qt()
```

以下代码实现了这个例子：

![矩阵等式](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equbp.png)

```kotlin
val A: Matrix = DenseMatrix(
arrayOf(
doubleArrayOf(5.0, 2.0),
doubleArrayOf(2.0, 5.0)
)
)
// 进行特征分解
val eigen: EigenDecomposition = EigenDecomposition(A)
val D: Matrix = eigen.D()
println("\nD = \n" + D)
val Q: Matrix = eigen.Q()
println("\nQ = \n" + Q)
val Qt: Matrix = eigen.Qt()
println("\nQt = \n" + Qt)
```

输出如下：

```
D =
2x2
[,1] [,2]
[1,] 7.000000, 0.000000,
[2,] 0.000000, 3.000000,
Q =
2x2
[,1] [,2]
[1,] 0.707107, -0.707107,
[2,] 0.707107, 0.707107,
Qt =
2x2
[,1] [,2]
[1,] 0.707107, 0.707107,
[2,] -0.707107, 0.707107,
// 验证 QDQ' = A
val QDQt: Matrix = Q.multiply(D).multiply(Qt)
println(String.format("%s = \n%s is %b",
A,
QDQt,
MatrixPropertyUtils.areEqual(A, QDQt, 1e-14)))
```

输出如下：

```
2x2
[,1] [,2]
[1,] 5.000000, 2.000000,
[2,] 2.000000, 5.000000,  =
2x2
[,1] [,2]
[1,] 5.000000, 2.000000,
[2,] 2.000000, 5.000000,  is true
```

虽然 `EigenDecomposition` 类提供了分解，但提取特征向量和对应的特征值并不方便。要操作特征向量和特征值，你需要使用 `Eigen` 和 `EigenProperty` 类。

`Eigen` 的签名如下：

# Eigen类

## 构造方法

```java
/**
* 计算方阵的特征值和特征向量。
* 对于每个特征值，存在无限多个与之相关的特征向量，它们构成一个向量空间。
* 此实现计算一组线性无关的基，这些基向量的任意线性组合都可作为特征向量。
*
* <p>
* TODO: 目前，我们同时计算实数和复数特征值，
* 但暂不支持复数特征值对应的特征向量计算。
* </p>
*
* @param A       一个方阵
* @param method  特征分解算法，参见 {@link Method}
* @param epsilon 精度参数：当数值 |x| ≤ ε 时，视其为 0
* @throws IllegalArgumentException 如果 A 不是方阵
*/
public Eigen(Matrix A, Method method, final double epsilon)
```

```java
/**
* 默认使用 {@link Method#QR} 方法。
*
* @param A       一个方阵
* @param epsilon 精度参数：当数值 |x| ≤ ε 时，视其为 0
* @see #Eigen(dev.nm.algebra.linear.matrix.doubles.Matrix, double)
*/
public Eigen(Matrix A, double epsilon)
```

```java
/**
* 计算方阵的特征值和特征向量。
*
* @param A      一个方阵
* @param method 特征分解算法，参见 {@link Method}
*/
public Eigen(Matrix A, Method method)
```

```java
/**
* 计算方阵的特征值和特征向量。
*
* @param A 一个方阵
* @see Wikipedia: QR
* algorithm
*/
public Eigen(Matrix A)
```

## 获取特征值

```java
/**
* 获取所有特征值，包括实数和复数。
*
* @return 特征值列表
*/
List getEigenvalues();
```

```java
/**
* 获取所有实数特征值。
* 特征值按降序排列。
*
* @return 所有实数特征值
*/
public double[] getRealEigenvalues()
```

## 获取特征属性

```java
/**
* 根据特征值获取对应的 {@link EigenProperty}。
* 注意，传入的数值必须与特征值的二进制表示完全相同。
* 传入近似值（精度范围内）可能导致匹配失败，即返回 {@code null}。
*
* @param eigenvalue 一个特征值
* @return 该特征值的 {@code EigenProperty}
*/
public EigenProperty getProperty(Number eigenvalue)
```

# EigenProperty类

`EigenProperty`的签名如下：

```java
/**
* `EigenProperty` 是一个只读结构，包含关于特定特征值的信息，
* 例如其重数和特征向量。
*
* @author Haksun Li
*/
public class EigenProperty {
/**
* 获取特征值。
*
* @return 特征值
*/
public Number eigenvalue()
/**
* 获取特征多项式根（特征值）的重数。
*
* @return 代数重数
*/
public int algebraicMultiplicity()
/**
* 获取由特征向量张成的向量空间的维数。
*
* @return 几何重数
*/
public int geometricMultiplicity()
/**
* 获取特征向量。
*
* @return 特征向量
*/
public List eigenbasis()
/**
* 获取一个特征向量。
* 注意，特征向量不是唯一的。
* 此实现始终返回基中的第一个向量。
* 要获取特征向量空间的完整基集，请使用
* `eigenbasis()`。
*
* @return 一个特征向量
*/
public Vector eigenVector()
```

# 概念说明

注意，一个矩阵可能存在重复的特征值。一个特征值的重复次数称为该特征值的`代数重数`。一个特征值的`几何重数`是指与其关联的线性无关特征向量的数量。也就是说，它是由与该特征值关联的特征向量构成的基所张成的零空间的维数。几何重数始终小于或等于该特征值的代数重数。

# 代码示例

以下NM Dev代码针对一个矩阵计算其特征值、对应的特征向量，以及由每组特征向量张成的线性空间。如果一个向量可以写成基向量的线性组合，则该向量属于这个线性空间。

以下矩阵：
$$ \boldsymbol{A}=\left[\begin{array}{ccc}1& -3& 3\\ {}3& -5& 3\\ {}6& -6& 4\end{array}\right] $$

有两个特征值：`4` 和 `-2`。

对于特征值 `4`，有一个特征向量 $$ \left[\begin{array}{c}0.5\\ {}0.5\\ {}1\end{array}\right] $$。它张成的零空间是一维线性空间（一条直线）。

对于特征值 `-2`，有两个特征向量：$$ \left[\begin{array}{c}1\\ {}1\\ {}0\end{array}\right] $$ 和 $$ \left[\begin{array}{c}-1\\ {}0\\ {}1\end{array}\right] $$。它们张成的零空间是二维线性空间（一个平面）。

```kotlin
val A: Matrix = DenseMatrix(
arrayOf(
doubleArrayOf(1.0, -3.0, 3.0),
doubleArrayOf(3.0, -5.0, 3.0),
doubleArrayOf(6.0, -6.0, 4.0),
)
)
// 执行特征分解
val eigen: Eigen = Eigen(A)
eigen.getEigenvalues().forEach {
println("特征值 = " + it)
}
// 第一个特征值
val eigenvalue0: Number = eigen.getEigenvalue(0) // 从 0 开始计数
println("特征值 0 = " + eigenvalue0)
// 获取与该特征值相关的属性
val prop0: EigenProperty = eigen.getProperty(eigenvalue0)
println("代数重数 = " + prop0.algebraicMultiplicity())
println("几何重数 = " + prop0.geometricMultiplicity())
val basis0: List = prop0.eigenbasis()
basis0.forEach {
println("基向量 = " + it)
}
val vs0: RealVectorSpace = RealVectorSpace(basis0, 1e-15)
// 检查该向量是否属于该向量空间，即是否为基向量的线性组合
val in0: Boolean = vs0.isSpanned(
DenseVector(arrayOf(-0.4, -0.4, -0.8)))
println("是否在向量空间中 = " + in0)
```

输出如下：

### 2.3.6 奇异值分解

到目前为止讨论的所有分解都适用于方阵。奇异值分解（SVD）可以看作是特征分解的一种扩展，但 SVD 适用于任何矩阵，无论是方阵、高矩阵还是宽矩阵。也就是说，SVD 适用于任何线性变换。具体来说，一个 `m` × `n` 矩阵 `A` 的 SVD 是三个矩阵的乘积：

![$$ \boldsymbol{A}={\boldsymbol{UDV}}^T $$](img/528380_1_En_2_Chapter_TeX_Equbr.png)

`U` 是一个 `m` × `m` 的正交矩阵。`D` 是一个 `m` × `n` 的对角矩阵。`V` 是一个 `n` × `n` 的正交矩阵。对角线元素 `σ[i] = D[ii]` 被称为 `A` 的奇异值。非零奇异值的数量等于 `A` 的秩。`U` 的列和 `V` 的列分别被称为 `A` 的左奇异向量和右奇异向量。通常，你会选择一个分解，使得奇异值 `D[ii]` 按降序排列。在这种情况下，`D`（但不总是 `U` 和 `V`）由 `A` 唯一确定。SVD 的数学应用包括计算伪逆、矩阵近似、以及确定矩阵的秩、值域和零空间。SVD 在科学、工程和统计学的所有领域也广泛有用，例如信号处理、数据的最小二乘拟合以及过程控制。

从几何角度看，SVD 说明任何线性变换都可以分解为三个步骤：即先旋转（`V`^(T)），然后拉伸/缩放（`D`）（不必在所有的方向上使用相同的量；你可以将 x 轴拉伸的比例是 y 轴的两倍），然后再旋转（`U`）。

例如，要将一个正方形变换成一个平行四边形，你可以先顺时针旋转 `θ` 角（只要选取一个合理的数值，这个值不是特别重要，因为旋转矩阵不唯一），然后沿不同坐标轴按不同比例缩放，最后再逆时针旋转 `θ` 角。在降维方面，你先通过旋转“选择合适的坐标轴”。然后通过忽略（或用 0 代替）最小的奇异值来投影掉次要的维度。请参见图 2-22。

![展示 SVD 几何解释的图示。图中的四个箭头代表各种角度，它们组合形成另一个角度。](img/528380_1_En_2_Fig22_HTML.png)

**图 2-22** SVD 几何解释

有几种进行 SVD 的方法，例如针对高矩阵的 Golub-Kahan 算法，针对对称矩阵的 QR 算法，以及针对双对角矩阵的多重相对稳健表示（MRRR）。所有这些算法和变换的实现都非常复杂。一个名为 `SVDDecomposition` 的 NM Dev 接口代表了不同的实现。其签名如下：

```java
public interface SVDDecomposition {
    /**
     * 获取归一化（因此为正）的奇异值。如果此计算关闭了归一化，它们可能与 D 中的值不同。
     *
     * @return 奇异值
     */
    public double[] getSingularValues();
    /**
     * 获取 SVD 分解中的 D 矩阵。
     *
     * @return D
     */
    public DiagonalMatrix D();
    /**
     * 获取 SVD 分解中的 U 矩阵。
     *
     * @return U
     */
    public Matrix U();
    /**
     * 获取 U 的转置，即 {@code U().t()}。
     *
     * @return {@code U().t()}
     */
    public Matrix Ut();
    /**
     * 获取 SVD 分解中的 V 矩阵。
     *
     * @return V
     */
    public Matrix V();
}
```

`SVD` 类封装了所有这些算法，并根据输入矩阵应用适当的一种。由于 `SVD` 继承自 `SVDDecomposition`，它具有相同的签名。以下 NM Dev 代码计算了此示例：

![$$ \left[\begin{array}{ccccc}1&amp; 0&amp; 0&amp; 0&amp; 2\\ {}0&amp; 0&amp; 3&amp; 0&amp; 0\\ {}0&amp; 0&amp; 0&amp; 0&amp; 0\\ {}0&amp; 2&amp; 0&amp; 0&amp; 0\end{array}\right]=\left[\begin{array}{cccc}0&amp; -1&amp; 0&amp; 0\\ {}-1&amp; 0&amp; 0&amp; 0\\ {}0&amp; 0&amp; 0&amp; -1\\ {}0&amp; 0&amp; -1&amp; 0\end{array}\right]\left[\begin{array}{ccccc}3&amp; 0&amp; 0&amp; 0&amp; 0\\ {}0&amp; \sqrt{5}&amp; 0&amp; 0&amp; 0\\ {}0&amp; 0&amp; 2&amp; 0&amp; 0\\ {}0&amp; 0&amp; 0&amp; 0&amp; 0\end{array}\right]\left[\begin{array}{ccccc}0&amp; 0&amp; 1&amp; 0&amp; 0\\ {}\sqrt{0.2}&amp; 0&amp; 0&amp; 0&amp; \sqrt{0.8}\\ {}0&amp; 1&amp; 0&amp; 0&amp; 0\\ {}0&amp; 0&amp; 0&amp; 1&amp; 0\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equbs.png)

```kotlin
val A: Matrix = DenseMatrix(
    arrayOf(
        doubleArrayOf(1.0, 0.0, 0.0, 0.0, 2.0),
        doubleArrayOf(0.0, 0.0, 3.0, 0.0, 0.0),
        doubleArrayOf(0.0, 0.0, 0.0, 0.0, 0.0),
        doubleArrayOf(0.0, 2.0, 0.0, 0.0, 0.0)
    )
)
// 执行 SVD
val svd: SVD = SVD(A, true, 1e-15)
val U: Matrix = svd.U()
println("\nU = \n" + U)
val D: DiagonalMatrix = svd.D()
println("\nD = \n" + D)
val V: Matrix = svd.V()
println("\nVt = \n" + V.t())
val Ut: Matrix = svd.Ut()
println("\nUt = \n" + Ut)
```

输出如下：
```
特征值 = 4.0
特征值 = -2.0
特征值 0 = 4.0
代数重数 = 1
几何重数 = 1
基向量 = [0.500000, 0.500000, 1.000000]
是否在向量空间中 = true
// 第二个特征值
val eigenvalue1: Number = eigen.getEigenvalue(1) // 从 1 开始计数
println("特征值 1 = " + eigenvalue1)
val prop1: EigenProperty = eigen.getProperty(eigenvalue1)
println("代数重数 = " + prop1.algebraicMultiplicity())
println("几何重数 = " + prop1.geometricMultiplicity())
val basis1: List = prop1.eigenbasis()
basis1.forEach {
    println("基向量 = " + it)
}
val vs1: RealVectorSpace = RealVectorSpace(basis1, 1e-15)
val in1: Boolean = vs1.isSpanned(DenseVector(arrayOf(-0.4, 0.4, 0.8)))
println("是否在向量空间中 = " + in1)
val in2: Boolean = vs1.isSpanned(DenseVector(arrayOf(-0.5, 0.5, 1.0)))
println("是否在向量空间中 = " + in2)
```

输出如下：
```
特征值 1 = -2.0
代数重数 = 2
几何重数 = 2
基向量 = [1.000000, 1.000000, 0.000000]
基向量 = [-1.000000, 0.000000, 1.000000]
是否在向量空间中 = true
是否在向量空间中 = true
```

## U =
```r
4x4
      [,1] [,2] [,3] [,4]
[1,] 0.000000, 1.000000, 0.000000, 0.000000,
[2,] 1.000000, 0.000000, 0.000000, 0.000000,
[3,] 0.000000, 0.000000, 0.000000, 1.000000,
[4,] 0.000000, 0.000000, 1.000000, 0.000000,
```

## D =
```r
4x4
      [,1]     [,2] [,3] [,4]
[1,] 3.000000, 0.000000, 0.000000, 0.000000,
[2,] 0.000000, 2.236068, 0.000000, 0.000000,
[3,] 0.000000, 0.000000, 2.000000, 0.000000,
[4,] 0.000000, 0.000000, 0.000000, 0.000000,
```

## Vt =
```r
4x5
      [,1]     [,2] [,3] [,4]     [,5]
[1,] 0.000000, 0.000000, 1.000000, 0.000000, 0.000000,
[2,] 0.447214, 0.000000, 0.000000, 0.000000, 0.894427,
[3,] 0.000000, 1.000000, 0.000000, 0.000000, 0.000000,
[4,] 0.000000, 0.000000, 0.000000, 1.000000, 0.000000,
```

## Ut =
```r
4x4
      [,1] [,2] [,3] [,4]
[1,] 0.000000, 1.000000, 0.000000, 0.000000,
[2,] 1.000000, 0.000000, 0.000000, 0.000000,
[3,] 0.000000, 0.000000, 0.000000, 1.000000,
[4,] 0.000000, 0.000000, 1.000000, 0.000000,
```

// 验证 `UDVt` = `A`

```scala
val UDVt: Matrix = U.multiply(D).multiply(V.t())
println(String.format("%s = \n%s is %b",
    A,
    UDVt,
    MatrixPropertyUtils.areEqual(UDVt, A, 1e-14)))
```

输出如下：

```
4x5
      [,1]     [,2]     [,3]     [,4]     [,5]
[1,] 1.000000, 0.000000, 0.000000, 0.000000, 2.000000,
[2,] 0.000000, 0.000000, 3.000000, 0.000000, 0.000000,
[3,] 0.000000, 0.000000, 0.000000, 0.000000, 0.000000,
[4,] 0.000000, 2.000000, 0.000000, 0.000000, 0.000000,  =
4x5
      [,1]     [,2]     [,3]     [,4]     [,5]
[1,] 1.000000, 0.000000, 0.000000, 0.000000, 2.000000,
[2,] 0.000000, 0.000000, 3.000000, 0.000000, 0.000000,
[3,] 0.000000, 0.000000, 0.000000, 0.000000, 0.000000,
[4,] 0.000000, 2.000000, 0.000000, 0.000000, 0.000000,  为 true
```

// 验证 `UtAV` = `D`

```scala
val UtAV: Matrix = Ut.multiply(A).multiply(V)
println(String.format("%s = \n%s is %b",
    A,
    UtAV,
    MatrixPropertyUtils.areEqual(UtAV, D, 1e-14)))
```

输出如下：

```
4x5
      [,1]     [,2]     [,3]     [,4]     [,5]
[1,] 1.000000, 0.000000, 0.000000, 0.000000, 2.000000,
[2,] 0.000000, 0.000000, 3.000000, 0.000000, 0.000000,
[3,] 0.000000, 0.000000, 0.000000, 0.000000, 0.000000,
[4,] 0.000000, 2.000000, 0.000000, 0.000000, 0.000000,  =
4x4
      [,1]     [,2] [,3] [,4]
[1,] 3.000000, 0.000000, 0.000000, 0.000000,
[2,] 0.000000, 2.236068, 0.000000, 0.000000,
[3,] 0.000000, 0.000000, 2.000000, 0.000000,
[4,] 0.000000, 0.000000, 0.000000, 0.000000,  为 true
```

# 2.4 线性方程组

线性方程组（或线性系统）是涉及同一组变量的一个或多个线性方程的集合。以下是一个示例：

![$$ \left\{\begin{array}{c}x+3y-2z=5\\ {}3x+5y+6z=7\\ {}2x+4y+3z=8\end{array}\right. $$](img/528380_1_En_2_Chapter_TeX_Equbt.png)

这是一个包含三个变量 `x`、`y` 和 `z` 的三个方程的方程组。线性方程组的解是对变量的赋值，使得所有方程同时成立。上述方程组的一个解如下所示：

![$$ \left\{\begin{array}{c}x=-15\\ {}y=8\\ {}z=2\end{array}\right. $$](img/528380_1_En_2_Chapter_TeX_Equbu.png)

因为它使所有三个方程都成立。`系统`一词表示这些方程被视为一个整体，而非单独个体。

线性系统理论是线性代数的基础和基本组成部分，而线性代数在现代数学的大多数领域都有应用。非线性方程组通常可以近似为线性系统，这是在建立相对复杂系统的数学模型或计算机模拟时的一种有用技术。求解的计算算法是数值线性代数的重要组成部分，并在工程、物理、化学、计算机科学和经济学中发挥着显著作用。

一个包含 `m` 个方程和 `n` 个未知数的一般线性系统可以写成如下形式：

![$$ \left\{\begin{array}{c}{a}_{11}{x}_1+{a}_{12}{x}_2+\cdots {a}_{1n}{x}_n={b}_1\\ {}{a}_{21}{x}_1+{a}_{22}{x}_2+\cdots +{a}_{21}{x}_n={b}_2\\ {}\vdots \\ {}{a}_{m1}{x}_1+{a}_{m2}{x}_2+\cdots +{a}_{mn}{x}_n={b}_m\end{array}\right. $$](img/528380_1_En_2_Chapter_TeX_Equbv.png)

`x[1]`、`x[2]`、…、`x[n]` 是 `n` 个未知数；`a[11]`、`a[12]`、…、`a[mn]` 是系统的 `m` × `n` 个系数；`b[1]`、`b[2]`、…、`b[m]` 是 `m` 个常数项。

将每个未知数视为线性组合中一个列向量的权重是很有帮助的。这使你可以从向量空间或线性空间的角度来考虑问题。例如，左侧向量所有可能线性组合的集合被称为它们的`张成空间`，当右侧向量位于该张成空间内或存在于该张成的向量空间中时，方程组有解。如果该张成空间中的每个向量都能唯一地表示为给定左侧向量的线性组合，那么任何解都是唯一的。

等价地，你可以将向量方程写成矩阵形式：

![$$ \boldsymbol{Ax}=\boldsymbol{b} $$](img/528380_1_En_2_Chapter_TeX_Equbw.png)

其中

![$$ \boldsymbol{A}=\left[\begin{array}{cccc}{a}_{11}&amp; {a}_{12}&amp; \dots &amp; {a}_{1n}\\ {}{a}_{21}&amp; {a}_{22}&amp; \dots &amp; {a}_{2n}\\ {}\vdots &amp; \vdots &amp; \ddots &amp; \vdots \\ {}{a}_{m1}&amp; {a}_{m2}&amp; \dots &amp; {a}_{mn}\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_IEq11.png), ![$$ \boldsymbol{x}=\left[\begin{array}{c}{x}_1\\ {}{x}_2\\ {}\vdots \\ {}{x}_n\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_IEq12.png), ![$$ \boldsymbol{b}=\left[\begin{array}{c}{b}_1\\ {}{b}_2\\ {}\vdots \\ {}{b}_m\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_IEq13.png)

张成空间的基中向量的数量现在表示为矩阵的`秩`，该秩小于 `m` 和 `n`。

线性方程组的解是对变量 `x[1]`、`x[2]`、…、`x[n]` 的赋值，使得每个方程都成立。所有可能解的集合称为`解集`。一个线性系统可能表现为以下三种情况之一：

- 系统有无限多个解。

- 系统有且仅有一个唯一解。

- 系统无解。

从几何角度看，对于包含两个变量（`x` 和 `y`）的方程组，每个线性方程在 `xy` 平面上确定一条直线。因为线性方程组的解必须满足所有方程，所以解集是这些直线的交点，因此可能是一条线、一个点或空集。见图 2-23。

![](img/528380_1_En_2_Fig23_HTML.png)

方程组解的图示。该图将两个方程描述为两条直线，它们在点 (2,3) 处相交。

图 2-23

两个线性方程组的解

对于三个变量，每个线性方程在三维空间中确定一个平面，解集是这些平面的交集。因此，解集可能是一个平面、一条线、一个点或空集。例如，三个平行平面没有公共点，则其方程组的解集为空。如果三个平面相交于一点，解集为一个点。如果三个平面通过两个点，则它们的方程至少有两个公共解；实际上，解集是无限的，由通过这两点的直线上的所有点组成。对于 `n` 个变量，每个线性方程在 `n` 维空间中确定一个`超平面`。解集是这些超平面的交集，这是一个维度低于 `n` 的仿射（或欧几里得）子空间。

一般来说，线性系统的行为由方程数 `m` 和未知数 `n` 之间的关系决定。具体来说：

1. 如果 `m` = `n`，系统有且仅有一个唯一解。
2. 如果 `m` > `n`，系统无解。这种系统也称为`超定`系统。
3. 如果 `m` < `n`，系统有无限多个解，但也可能无解。这种系统称为`欠定`系统。解集的维数等于 `n` − `m`。

图 2-24 以两个变量为例说明了这种三分法。第一个系统有无限多个解，即蓝色线上的所有点。第二个系统有唯一的解，即两条线的交点。第三个系统无解，因为三条线没有公共点。见图 2-24。

![](img/528380_1_En_2_Fig24_HTML.jpg)

三张图片的图示。一个方程有一条线，两个方程有两条相交的线，三个方程有三个点。

图 2-24

两个线性方程组的可能解

重要的是要注意，图 2-24 中的图像仅显示最常见的情况（一般情况）。如果方程线性相关（平行线/平面）或系统不一致（无解），线性方程组的行为会不同于一般情况。例如，两个方程两个未知数的系统可能无解（如果两条线平行），或者三个方程两个未知数的系统可能有解（如果三条线相交于一点）。见图 2-25。

![](img/528380_1_En_2_Fig25_HTML.png)

两条平行线的系统图示。两条平行线代表两个方程。

图 2-25

两条平行线的方程组是不一致的，因此无解

`NM Dev` 提供了一套工具来解决各种类型的线性方程组。它可以求解方阵系统、`欠定`系统和`超定`系统。针对每种类型的系统，有多种算法（以及同一算法的多种实现）可供求解。

### 2.4.1 行阶梯形矩阵与简化行阶梯形矩阵

让我们考虑一个解之前方程组的实际例子：

![$$ \left\{\begin{array}{c}x+3y-2z=5\\ {}3x+5y+6z=7\\ {}2x+4y+3z=8\end{array}\right. $$](img/528380_1_En_2_Chapter_TeX_Equbx.png)

你可以用另外两个变量表示`x`，然后将其代入第二和第三个方程。即：

```
x = 5 - 3y + 2z
```

然后得到：

![$$ \left\{\begin{array}{c}-4y+12z=-8\\ {}-2y+7z=-2\end{array}\right. $$](img/528380_1_En_2_Chapter_TeX_Equbz.png)

你重复这个步骤，解出`y`，然后将其代入第二个方程。即：

```
y = 2 + 3z
```

然后得到：

```
z = 2
```

已知`z`后，你将其回代到`y`的方程中求得`y`。已知`y`后，再将其回代到`x`的方程中求得`x`。最终得到一个解：

## 行阶梯形矩阵与简化行阶梯形矩阵

你可以将这个步骤一般化，并构造一个通用的算法来求解方程组。首先，你需要了解几个概念。

如果一个矩阵满足以下两个条件，则称其为行阶梯形矩阵：

- 所有元素全为零的行位于矩阵的底部。
- 非零行的首非零元（也称为*主元*）总是严格位于其上一行首非零元的右边。

这两个条件意味着，在首非零元所在的列下方，所有元素均为零。下面是一个 3 × 5 矩阵处于行阶梯形（但不是简化行阶梯形）的例子：

$$ \left[\begin{array}{ccccc}1&amp; {a}_0&amp; {a}_1&amp; {a}_2&amp; {a}_3\\ {}0&amp; 0&amp; 2&amp; {a}_4&amp; {a}_5\\ {}0&amp; 0&amp; 0&amp; 1&amp; {a}_6\end{array}\right] $$

如果一个矩阵满足以下条件，则称其为简化行阶梯形矩阵（也称为*行规范形*）：

- 它是行阶梯形矩阵。
- 每个非零行的首元为 1（称为首 1）。
- 包含首 1 的列中，所有其他元素均为零。

这是一个处于简化行阶梯形的矩阵示例：

$$ \left[\begin{array}{cccc}1&amp; 0&amp; {a}_1&amp; {b}_1\\ {}0&amp; 1&amp; {a}_2&amp; {b}_2\\ {}0&amp; 0&amp; 1&amp; {b}_3\end{array}\right] $$

一旦你将方程组化为简化行阶梯形，就容易计算或直接读出解了。上述矩阵实际上表示如下方程组：

$$ \left\{\begin{array}{c}{x}_1+{a}_1{x}_3={b}_1\\ {}{x}_2+{a}_2{x}_3={b}_2\\ {}{x}_3={b}_3\end{array}\right. $$

你可以从第三个方程读出 `x[3]` 的解。一旦知道了 `x[3]`，就可以将其简单地回代到另外两个方程中，得到 `x[1]` 和 `x[2]`。

### 2.4.2 回代法

回代法通过一个迭代过程求解形如 `Ux = b` 的矩阵方程，其中 `U` 是一个上三角矩阵。这个过程之所以得名，是因为对于一个上三角矩阵，你首先计算出 `x[n]`，然后将其向后代入下一个方程以求解 `x[n−1]`，最后重复此过程直到得到 `x[1]`。请注意，如果方程组是相容的，`U` 中的某些对角线元素可以为 0。下面是一个例子：

$$ \left[\begin{array}{ccc}1&amp; 2&amp; 3\\ {}0&amp; 0&amp; 5\\ {}0&amp; 0&amp; 0\end{array}\right]\times \left[\begin{array}{c}10\\ {}0\\ {}0\end{array}\right]=\left[\begin{array}{c}10\\ {}0\\ {}0\end{array}\right] $$

NM Dev 库中名为 `BackwardSubstitution` 的类实现了一个回代算法。以下代码求解了这个例子：

```kotlin
val U: UpperTriangularMatrix = UpperTriangularMatrix(
    arrayOf(
        doubleArrayOf(1.0, 2.0, 3.0),
        doubleArrayOf(0.0, 5.0),
        doubleArrayOf(0.0)
    )
)
val b: Vector = DenseVector(arrayOf(10.0, 0.0, 0.0))
val solver: BackwardSubstitution = BackwardSubstitution()
val x: Vector = solver.solve(U, b)
println("x = " + x)
// 验证 Ux = b
val Ux: Vector = U.multiply(x)
println(String.format("%s = \n%s is %b",
    Ux,
    b,
    MatrixPropertyUtils.areEqual(Ux, b, 1e-14))) // MatrixPropertyUtils.areEqual 也适用于向量
```

输出结果如下：

```
x = [10.000000, 0.000000, 0.000000]
[10.000000, 0.000000, 0.000000]  =
[10.000000, 0.000000, 0.000000]  is true
```

### 2.4.3 前向代入

类似地，你可以使用前向代入法，通过迭代过程求解下三角矩阵 `L` 的矩阵方程 `Lx = b`。该方法得名于此：对于下三角矩阵，你先计算 `x[1]`，然后将该值前向代入下一个方程以求解 `x[2]`，并重复此过程直至求出 `x[n]`。请注意，只要方程组一致，`L` 中的某些对角线元素可以为 0。下面是一个示例：

$$ \left[\begin{array}{ccc}0 &amp; 0 &amp; 0\\ {}2 &amp; 0 &amp; 0\\ {}4 &amp; 5 &amp; 6\end{array}\right]\times \left[\begin{array}{c}0\\ {}0\\ {}30\end{array}\right]=\left[\begin{array}{c}0\\ {}0\\ {}30\end{array}\right] $$

NM Dev 库中名为 `ForwardSubstitution` 的类实现了前向代入算法。以下代码求解了此示例：

```kotlin
val L: LowerTriangularMatrix = LowerTriangularMatrix(
    arrayOf(
        doubleArrayOf(1.0),
        doubleArrayOf(2.0, 3.0),
        doubleArrayOf(4.0, 5.0, 6.0)
    )
)
val b: Vector = DenseVector(arrayOf(10.0, 20.0, 30.0))
val solver: ForwardSubstitution = ForwardSubstitution()
val x: Vector = solver.solve(L, b)
println("x = " + x)
// 验证 Lx = b
val Lx: Vector = L.multiply(x)
println(String.format("%s = \n%s is %b",
    Lx,
    b,
    MatrixPropertyUtils.areEqual(Lx, b, 1e-14))) // MatrixPropertyUtils.areEqual 同样适用于向量
```

输出结果如下：

```
x = [10.000000, 0.000000, -1.666667]
[10.000000, 20.000000, 30.000000]  =
[10.000000, 20.000000, 30.000000]  is true
```

通常，NM Dev 库的 `LUSolver` 类可处理上三角矩阵和下三角矩阵。以下代码复现了前面两个问题。

# 一个 LSProblem

一个 `LSProblem` 代码示例：

```kotlin
// 一个 LSProblem
val L: LowerTriangularMatrix = LowerTriangularMatrix(
    arrayOf(
        doubleArrayOf(1.0),
        doubleArrayOf(2.0, 3.0),
        doubleArrayOf(4.0, 5.0, 6.0)
    )
)
val b1: Vector = DenseVector(arrayOf(10.0, 20.0, 30.0))
val solver1: LUSolver = LUSolver()
val x1: Vector = solver1.solve(
    // 构建线性系统问题: Lx = b1
    LSProblem(L, b1)
)
println("x1 = " + x1)
// 验证 Ux = b
val Lx: Vector = L.multiply(x1)
println(String.format("%s = \n%s is %b",
    Lx,
    b1,
    MatrixPropertyUtils.areEqual(Lx, b1, 1e-14))) // MatrixPropertyUtils.areEqual 同样适用于向量
```

输出结果如下：

```
x1 = [10.000000, 0.000000, -1.666667]
[10.000000, 20.000000, 30.000000]  =
[10.000000, 20.000000, 30.000000]  is true
```

另一个 `LSProblem` 示例：

```kotlin
// 另一个 LSProblem
val U: UpperTriangularMatrix = UpperTriangularMatrix(
    arrayOf(
        doubleArrayOf(1.0, 2.0, 3.0),
        doubleArrayOf(0.0, 5.0),
        doubleArrayOf(0.0),
    )
)
val b2: Vector = DenseVector(arrayOf(10.0, 0.0, 0.0))
val solver2: LUSolver = LUSolver()
val x2: Vector = solver2.solve(
    // 构建线性系统问题: Ux = b2
    LSProblem(U, b2)
)
println("x2 = " + x2)
// 验证 Ux = b
val Ux: Vector = U.multiply(x2)
println(String.format("%s = \n%s is %b",
    Ux,
    b2,
    MatrixPropertyUtils.areEqual(Ux, b2, 1e-14))) // MatrixPropertyUtils.areEqual 同样适用于向量
```

输出结果如下：

```
x2 = [10.000000, 0.000000, -0.000000]
[10.000000, 0.000000, 0.000000]  =
[10.000000, 0.000000, 0.000000]  is true
```

### 2.4.4 初等运算

通过一系列初等行运算，你可以将任何矩阵转换为（简化）行阶梯形矩阵。初等运算共有三种类型：

*   `行交换`：交换第 `i` 行与第 `j` 行。
*   `行乘法或缩放`：将第 `i` 行乘以一个非零常数。
*   `行加法`：用第 `i` 行加上第 `j` 行的倍数，替换第 `i` 行。

#### 行交换变换

对单位矩阵执行该运算，即可得到对应于该变换的初等矩阵。初等矩阵是与单位矩阵仅差一个初等行运算的矩阵。

例如，对应的初等矩阵交换了 3 × 3 单位矩阵的第二行和第三行。即：

![$$ \boldsymbol{P}=\left[\begin{array}{ccc}1 &amp; 0 &amp; 0\\ {}0 &amp; 0 &amp; 1\\ {}0 &amp; 1 &amp; 0\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equci.png)

如果你将任何 3 × 3 矩阵左乘（右乘）`P`，其第二行和第三行（列）将被交换。下面是一个示例：

![$$ \left[\begin{array}{ccc}1 &amp; 0 &amp; 0\\ {}0 &amp; 0 &amp; 1\\ {}0 &amp; 1 &amp; 0\end{array}\right]\times \left[\begin{array}{ccc}{a}_{11} &amp; {a}_{12} &amp; {a}_{13}\\ {}{a}_{21} &amp; {a}_{22} &amp; {a}_{23}\\ {}{a}_{31} &amp; {a}_{32} &amp; {a}_{33}\end{array}\right]=\left[\begin{array}{ccc}{a}_{11} &amp; {a}_{12} &amp; {a}_{13}\\ {}{a}_{31} &amp; {a}_{32} &amp; {a}_{33}\\ {}{a}_{21} &amp; {a}_{22} &amp; {a}_{23}\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equcj.png)

#### 行乘法变换

类似地，要将第 `i` 行缩放为非零常数 `m`，相应的初等矩阵是一个对角矩阵，其对角线上除第 `i` 个位置为 `m` 外，其余均为 1。例如，对应的初等矩阵将 3 × 3 单位矩阵的第二行缩放 2 倍。即：

![$$ \boldsymbol{D}=\left[\begin{array}{ccc}1 &amp; 0 &amp; 0\\ {}0 &amp; 2 &amp; 0\\ {}0 &amp; 0 &amp; 1\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equck.png)

下面是一个示例：

![$$ \left[\begin{array}{ccc}1 &amp; 0 &amp; 0\\ {}0 &amp; 2 &amp; 0\\ {}0 &amp; 0 &amp; 1\end{array}\right]\times \left[\begin{array}{ccc}{a}_{11} &amp; {a}_{12} &amp; {a}_{13}\\ {}{a}_{21} &amp; {a}_{22} &amp; {a}_{23}\\ {}{a}_{31} &amp; {a}_{32} &amp; {a}_{33}\end{array}\right]=\left[\begin{array}{ccc}{a}_{11} &amp; {a}_{12} &amp; {a}_{13}\\ {}2{a}_{21} &amp; 2{a}_{22} &amp; 2{a}_{23}\\ {}{a}_{31} &amp; {a}_{32} &amp; {a}_{33}\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equcl.png)

#### 行加法变换

要将第 `j` 行乘以标量 `m` 后加到第 `i` 行，对应的初等矩阵是单位矩阵，但 `a[ij] = m`。例如，要将 3 × 3 矩阵的第二行乘以 2 后加到第一行，对应的初等矩阵如下：

![$$ \boldsymbol{L}=\left[\begin{array}{ccc}1 &amp; 2 &amp; 0\\ {}0 &amp; 1 &amp; 0\\ {}0 &amp; 0 &amp; 1\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equcm.png)

下面是一个示例：

![$$ \left[\begin{array}{ccc}1 &amp; 2 &amp; 0\\ {}0 &amp; 1 &amp; 0\\ {}0 &amp; 0 &amp; 1\end{array}\right]\times \left[\begin{array}{ccc}{a}_{11} &amp; {a}_{12} &amp; {a}_{13}\\ {}{a}_{21} &amp; {a}_{22} &amp; {a}_{23}\\ {}{a}_{31} &amp; {a}_{32} &amp; {a}_{33}\end{array}\right]=\left[\begin{array}{ccc}{a}_{11}+2{a}_{21} &amp; {a}_{12}+2{a}_{22} &amp; {a}_{13}+2{a}_{23}\\ {}{a}_{21} &amp; {a}_{22} &amp; {a}_{23}\\ {}{a}_{31} &amp; {a}_{32} &amp; {a}_{33}\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equcn.png)

### 高斯消元法与高斯-若尔当消元法

高斯-若尔当消元法是一种用于求解线性方程组的算法。它还可用于计算矩阵的秩、方阵的行列式以及可逆矩阵的逆矩阵。要对矩阵执行行化简，你需要使用一系列初等行变换来修改矩阵，直到矩阵的左下角尽可能被零填满。

利用这些变换，矩阵总能被转换为上三角矩阵，实际上就是行阶梯形矩阵。当所有主元系数（每一行最左边的非零元）均为 1，并且所有包含主元的列在其他位置均为零时，该矩阵被称为简化行阶梯形矩阵。这种最终形式是唯一的；换句话说，它与所采用的行变换顺序无关。

*高斯消元法*指的是该过程的第一步，直到矩阵变为上三角矩阵或（非简化）行阶梯形矩阵。在这种形式下，你可以判断一个线性方程组是无解、有唯一解还是有无穷多解。高斯-若尔当消元法（即第二步）则继续使用行变换，直到求出解。换句话说，它将矩阵化为简化行阶梯形矩阵。

等价地，你可以看到行化简过程产生了原矩阵的一种矩阵分解。初等行变换可被视为在原矩阵左侧乘以初等矩阵。另一种观点是，将单行化简的一系列初等变换视为乘以一个弗罗贝尼乌斯矩阵。那么，算法计算的第一部分得到了一个 LU 分解，而第二部分则将原矩阵写为一个唯一确定的可逆矩阵与一个唯一确定的简化行阶梯矩阵的乘积。

名为 `GaussianElimination` 的 NM Dev 类执行初等行变换，将矩阵化简为行阶梯形。这等价于在左侧将原矩阵乘以可逆矩阵。对于方阵，该算法本质上是计算一个 LU 分解。你会得到如下结果：

![$$ \boldsymbol{TA}=\boldsymbol{U} $$](img/528380_1_En_2_Chapter_TeX_Equco.png)

其中 `T` 是变换矩阵，它是一系列初等矩阵/变换的乘积。`U` 是行阶梯形矩阵。

或者，你也可以得到如下结果：

![$$ \boldsymbol{PA}=\boldsymbol{LU} $$](img/528380_1_En_2_Chapter_TeX_Equcp.png)

其中 `P` 是置换矩阵，`L` 是下三角矩阵，`U` 是行阶梯形矩阵。

其签名如下：

```java
/**
 * 运行高斯消元算法。
 *
 * @param A           一个矩阵
 * @param usePivoting 若为 {@code true}，则使用部分主元法，例如用于数值稳定性。
 *                    通常情况下，不使用主元意味着不进行行交换。
 *                    只有当高斯消元从未遇到对角线上的零元素时，才能进行无主元操作。
 *                    由于除以零是致命错误，我们通常避免无主元操作。
 * @param epsilon     一个精度参数：当数字 |x| ≤ ε 时，被视为 0
 */
public GaussianElimination(Matrix A, boolean usePivoting, double epsilon)
/**
 * 使用部分主元法运行高斯消元算法。
 *
 * @param A 一个矩阵
 */
public GaussianElimination(Matrix A)
/**
 * 获取变换矩阵 T，满足 T * A = U。
 *
 * @return T
 */
public Matrix T()
/**
 * 获取上三角矩阵 U，满足 T * A = U 和 P * A = L * U。
 *
 * @return U
 */
public Matrix U()
/**
 * 获取下三角矩阵 L，满足 P * A = L * U。
 *
 * @return L
 */
public Matrix L()
/**
 * 获取置换矩阵 P，满足 P * A = L * U。
 *
 * @return P
 */
public PermutationMatrix P()
```

例如，以下 NM Dev 代码计算了这个示例：

![$$ \left[\begin{array}{ccc}0&amp; 0&amp; 1\\ {}0&amp; 1&amp; -0.5\\ {}1&amp; -0.6&amp; -0.2\end{array}\right]\times \left[\begin{array}{ccc}2&amp; 1&amp; 1\\ {}2&amp; 2&amp; -1\\ {}4&amp; -1&amp; 6\end{array}\right]=\left[\begin{array}{ccc}4&amp; -1&amp; 6\\ {}0&amp; 2.5&amp; -4\\ {}0&amp; 0&amp; 0.4\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equcq.png)

![$$ \left[\begin{array}{ccc}0&amp; 0&amp; 1\\ {}0&amp; 1&amp; 0\\ {}1&amp; 0&amp; 0\end{array}\right]\times \left[\begin{array}{ccc}2&amp; 1&amp; 1\\ {}2&amp; 2&amp; -1\\ {}4&amp; -1&amp; 6\end{array}\right]=\left[\begin{array}{ccc}1&amp; 0&amp; 0\\ {}0.5&amp; 1&amp; 0\\ {}0.5&amp; 0.6&amp; 1\end{array}\right]\left[\begin{array}{ccc}4&amp; -1&amp; 6\\ {}0&amp; 2.5&amp; -4\\ {}0&amp; 0&amp; 0.4\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equcr.png)

# 高斯消元与高斯-约当消元

下面的代码示例展示了如何使用 `GaussianElimination` 类。

```kotlin
val A: Matrix = DenseMatrix(
    arrayOf(
        doubleArrayOf(2.0, 1.0, 1.0),
        doubleArrayOf(2.0, 2.0, -1.0),
        doubleArrayOf(4.0, -1.0, 6.0),
    )
)
val ops: GaussianElimination = GaussianElimination(A, true, 0.0)
val T: Matrix = ops.T()
println("T = \n" + T)
val P: PermutationMatrix = ops.P()
println("P = \n" + P)
val U: Matrix = ops.U()
println(
    String.format("U = %s is upper triangular, %b",
        U,
        MatrixPropertyUtils.isUpperTriangular(U, 0.0)))
val L: Matrix = ops.L()
println(
    String.format("L = %s is lower triangular, %b",
        L,
        MatrixPropertyUtils.isLowerTriangular(L, 0.0)))
// verify that TA = U
val TA: Matrix = T.multiply(A)
println(String.format("%s = \n%s is %b",
    TA,
    U,
    MatrixPropertyUtils.areEqual(TA, U, 1e-15)))
// verify that PA = LU
val PA: Matrix = P.multiply(A)
val LU: Matrix = L.multiply(U)
println(String.format("%s = \n%s is %b",
    PA,
    LU,
    MatrixPropertyUtils.areEqual(PA, LU, 0.0)))
```

输出如下：

```
T =
3x3
[,1] [,2] [,3]
[1,] 0.000000, 0.000000, 1.000000,
[2,] 0.000000, 1.000000, -0.500000,
[3,] 1.000000, -0.600000, -0.200000,
P =
3x3
[,1] [,2] [,3]
[1,] 0.000000, 0.000000, 1.000000,
[2,] 0.000000, 1.000000, 0.000000,
[3,] 1.000000, 0.000000, 0.000000,
U = 3x3
[,1] [,2] [,3]
[1,] 4.000000, -1.000000, 6.000000,
[2,] 0.000000, 2.500000, -4.000000,
[3,] 0.000000, 0.000000, 0.400000,  is upper triangular, true
L = 3x3
[,1] [,2] [,3]
[1,] 1.000000, 0.000000, 0.000000,
[2,] 0.500000, 1.000000, 0.000000,
[3,] 0.500000, 0.600000, 1.000000,  is lower triangular, true
3x3
[,1] [,2] [,3]
[1,] 4.000000, -1.000000, 6.000000,
[2,] 0.000000, 2.500000, -4.000000,
[3,] 0.000000, 0.000000, 0.400000,  =
3x3
[,1] [,2] [,3]
[1,] 4.000000, -1.000000, 6.000000,
[2,] 0.000000, 2.500000, -4.000000,
[3,] 0.000000, 0.000000, 0.400000,  is true
3x3
[,1] [,2] [,3]
[1,] 4.000000, -1.000000, 6.000000,
[2,] 2.000000, 2.000000, -1.000000,
[3,] 2.000000, 1.000000, 1.000000,  =
3x3
[,1] [,2] [,3]
[1,] 4.000000, -1.000000, 6.000000,
[2,] 2.000000, 2.000000, -1.000000,
[3,] 2.000000, 1.000000, 1.000000,  is true
```

名为 `GaussianElimination` 的 NM Dev 类执行初等行变换，将矩阵化为行阶梯形。即：

![$$ \boldsymbol{TA}=\boldsymbol{U} $$](img/528380_1_En_2_Chapter_TeX_Equcs.png)

其中 `T` 是变换矩阵，是一系列初等矩阵/运算的乘积。`U` 是行阶梯形矩阵。此实现确保主元 1 在数值上精确为 1，以便于比较。假设一个主元 `u[i][j]` 等于 1，那么 `U.get(i, j) == 1` 总是返回 `true`。

例如，以下 NM Dev 代码计算了这个示例：

![$$ \left[\begin{array}{ccc}-2.75&amp; 1.75&amp; 0.75\\ {}4&amp; -2&amp; -1\\ {}2.5&amp; -1.5&amp; -0.5\end{array}\right]\times \left[\begin{array}{ccc}2&amp; 1&amp; 1\\ {}2&amp; 2&amp; -1\\ {}4&amp; -1&amp; 6\end{array}\right]=\left[\begin{array}{ccc}1&amp; 0&amp; 0\\ {}0&amp; 1&amp; 0\\ {}0&amp; 0&amp; 1\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equct.png)

```kotlin
val A: Matrix = DenseMatrix(
    arrayOf(
        doubleArrayOf(2.0, 1.0, 1.0),
        doubleArrayOf(2.0, 2.0, -1.0),
        doubleArrayOf(4.0, -1.0, 6.0),
    )
)
val ops: GaussJordanElimination = GaussJordanElimination(A, true, 0.0)
val U: Matrix = ops.U()
println(
    String.format("U = %s is in reduced row echelon form, %b",
        U,
        MatrixPropertyUtils.isReducedRowEchelonForm(U, 0.0)))
val T: Matrix = ops.T()
println("T = \n" + T)
// verify that TA = U
val TA: Matrix = T.multiply(A)
println(String.format("%s = \n%s is %b",
    TA,
    U,
    MatrixPropertyUtils.areEqual(TA, U, 1e-15)))
```

输出如下：

```
U = 3x3
[,1] [,2] [,3]
[1,] 1.000000, 0.000000, 0.000000,
[2,] 0.000000, 1.000000, 0.000000,
[3,] 0.000000, 0.000000, 1.000000,  is in reduced row echelon form, true
T =
3x3
[,1] [,2] [,3]
[1,] -2.750000, 1.750000, 0.750000,
[2,] 4.000000, -2.000000, -1.000000,
[3,] 2.500000, -1.500000, -0.500000,
3x3
[,1] [,2] [,3]
[1,] 1.000000, 0.000000, 0.000000,
[2,] 0.000000, 1.000000, 0.000000,
[3,] 0.000000, -0.000000, 1.000000,  =
3x3
[,1] [,2] [,3]
[1,] 1.000000, 0.000000, 0.000000,
[2,] 0.000000, 1.000000, 0.000000,
[3,] 0.000000, 0.000000, 1.000000,  is true
```

## 齐次与非齐次线性系统

通常，一个相容的线性方程组有方程数（`m`）小于或等于变量数（`n`），即 `m ≤ n`。否则，它就是一个超定系统，通常无解。为了理解线性方程组，如下所示：

# 线性映射的核与像

![$$ \boldsymbol{Ax}=\boldsymbol{b} $$](img/528380_1_En_2_Chapter_TeX_Equcu.png)

你可以将 `A` 视为一个线性变换或一个函数，它将一个线性空间 `V` 映射到另一个线性空间 `W`。线性映射的核，或称为*零空间*，即 `ker(A)`，是定义域的一个线性子空间，使得该函数将其所有元素映射到 0。

![$$ \ker \left(\boldsymbol{A}\right)=\left\{\boldsymbol{v}\in \boldsymbol{V}\kern0.5em |\kern0.5em \boldsymbol{Av}=0\right\} $$](img/528380_1_En_2_Chapter_TeX_Equcv.png)

秩-零化度定理指出，核的维度与像的维度之和等于定义域的维度。见图 2-26。

![](img/528380_1_En_2_Fig26_HTML.png)

*一个线性映射及其核的示意图。核中有两个椭圆形状，一个小一个大，它们与像中的两个椭圆相连。*

**图 2-26** 线性映射的核与像

## 齐次线性方程组

对于任意齐次线性方程组，如下所示：

![$$ \boldsymbol{Ax}=\boldsymbol{0} $$](img/528380_1_En_2_Chapter_TeX_Equcw.png)

至少存在一个解，称为零解（或平凡解），通过将每个变量赋值为零得到。即 `A0 = 0`。如果方程组具有非奇异矩阵（`det(A) ≠ 0`），则这也是唯一解。如果方程组具有奇异矩阵（`A` 不是满秩的），则 `A` 核的维度或秩不为 0，即 `ker(A) > 0`。此时存在一个包含无限多个解的解集。核中的每个向量都是一个非平凡解。该解集具有以下附加性质：

- 如果 `u` 和 `v` 是表示齐次方程组解的两个向量，那么向量和 `u + v` 也是该方程组的一个解。
- 如果 `u` 是表示齐次方程组解的一个向量，且 `c` 是任意标量，那么 `c*u` 也是该方程组的一个解。

## 非齐次线性方程组

对于非齐次线性方程组，有如下形式：

![$$ \boldsymbol{Ax}=\boldsymbol{b} $$](img/528380_1_En_2_Chapter_TeX_Equcx.png)

假设 `p` 是该线性方程组的一个特解，即 `Ap = b`；那么整个解集可以描述如下：

![$$ \left\{\boldsymbol{p}+\boldsymbol{v}\kern0.5em |\kern0.5em \boldsymbol{v}\in \ker \left(\boldsymbol{A}\right)\right\} $$](img/528380_1_En_2_Chapter_TeX_Equcy.png)

从几何角度看，这意味着非齐次方程组 `Ax = b` 的解集是齐次方程组 `Ax = 0` 解集的一个平移。具体来说，第一个方程组的平坦子空间或欧几里得子空间可以通过将齐次方程组的线性子空间沿向量 `p` 平移得到。这个推理仅当方程组至少有一个解时才成立。当且仅当向量 `b` 位于线性变换 `A` 的像中时，才会出现这种情况。

## 线性方程组的求解

NM Dev 类 `LinearSystemSolver` 用于求解形如 `Ax = b` 的线性方程组。假设在行化简之后，`A` 的行数不超过列数。也就是说，该系统不能是超定的。

注意，以下系统并非超定系统。其中有一行是线性相关的：

![$$ \left[\begin{array}{ccc}1&amp; -1&amp; 0\\ {}0&amp; -2&amp; 0\\ {}0&amp; 0&amp; -1\\ {}0&amp; 0&amp; -2\end{array}\right]\textbf{x}=\left[\begin{array}{c}-0.8\\ {}-1.6\\ {}0.8\\ {}1.6\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equcz.png)

线性方程组的求解分两步进行：

1. 首先，求解齐次方程组 `Ax = 0`，寻找非平凡解。
2. 然后，求解 `Ax = b`，得到一个特解。

如果 `A` 是满秩的，该实现将通过 LU 分解求解方程组。否则，通过 `p = Tb` 找到一个特解，其中 `T` 是将 `A` 化为简化行阶梯形的变换矩阵。最终解如下：

![$$ \boldsymbol{p}+\ker \left(\boldsymbol{A}\right) $$](img/528380_1_En_2_Chapter_TeX_Equda.png)

因此，这个解可以看作是 `A` 的零空间沿向量 `p` 的平移。

### 示例

例如，对于以下线性方程组：

![$$ \left[\begin{array}{cccc}0&amp; 1&amp; 2&amp; -1\\ {}1&amp; 0&amp; 1&amp; 1\\ {}-1&amp; 1&amp; 0&amp; -1\\ {}0&amp; 2&amp; 3&amp; -1\end{array}\right]\boldsymbol{x}=\left[\begin{array}{c}1\\ {}4\\ {}2\\ {}7\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equdb.png)

核的基满足：

![$$ \left[\begin{array}{cccc}0&amp; 1&amp; 2&amp; -1\\ {}1&amp; 0&amp; 1&amp; 1\\ {}-1&amp; 1&amp; 0&amp; -1\\ {}0&amp; 2&amp; 3&amp; -1\end{array}\right]\left[\begin{array}{c}-2\\ {}-1\\ {}1\\ {}1\end{array}\right]=\left[\begin{array}{c}0\\ {}0\\ {}0\\ {}0\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equdc.png)

一个特解满足：

![$$ \left[\begin{array}{cccc}0&amp; 1&amp; 2&amp; -1\\ {}1&amp; 0&amp; 1&amp; 1\\ {}-1&amp; 1&amp; 0&amp; -1\\ {}0&amp; 2&amp; 3&amp; -1\end{array}\right]\left[\begin{array}{c}9\\ {}11\\ {}-5\\ {}0\end{array}\right]=\left[\begin{array}{c}1\\ {}4\\ {}2\\ {}7\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equdd.png)

以下 NM Dev 代码求解此示例：

```kotlin
val A: Matrix = DenseMatrix(
arrayOf(
doubleArrayOf(0.0, 1.0, 2.0, -1.0),
doubleArrayOf(1.0, 0.0, 1.0, 1.0),
doubleArrayOf(-1.0, 1.0, 0.0, -1.0),
doubleArrayOf(0.0, 2.0, 3.0, -1.0)
)
)
val b: Vector = DenseVector(arrayOf(1.0, 4.0, 2.0, 7.0))
// 构造一个线性系统求解器
val solver: LinearSystemSolver = LinearSystemSolver(1e-15) // 精度
// 求解齐次线性系统
val soln: LinearSystemSolver.Solution = solver.solve(A)
// 得到一个特解
val p: Vector = soln.getParticularSolution(b)
println("p = \n" + p)
// 验证 Ap = b
val Ap: Vector = A.multiply(p)
println(String.format("%s = \n%s is %b",
Ap,
b,
MatrixPropertyUtils.areEqual(Ap, b, 1e-15)))
// 获取零空间的基
val kernel: List = soln.getHomogeneousSoln()
// println("kernel size = " + kernel.size())
// 验证 A * kernel = 0
val k: Vector = kernel.get(0)
println("kernel basis = " + k)
val Ak: Vector = A.multiply(k)
println("Ak = 0, " + Ak)
```

输出如下：

```
p =
[9.000000, 11.000000, -5.000000, 0.000000]
[1.000000, 4.000000, 2.000000, 7.000000]  =
[1.000000, 4.000000, 2.000000, 7.000000]  is true
kernel size = 1
kernel basis = [-2.000000, -1.000000, 1.000000, 1.000000]
Ak = 0, [0.000000, 0.000000, 0.000000, 0.000000]
```

`LinearSystemSolver` 的签名如下：

```java
/**
 * 构造一个用于求解线性方程组的求解器。
 *
 * @param epsilon 精度参数：当数值 |x| ≤ ε 时，视其为 0
 */
public LinearSystemSolver(double epsilon)
/**
 * 获取线性系统 Ax = b 的一个解。
 *
 * @param A0 表示线性方程组（齐次部分）的矩阵
 * @return 线性系统的一个解
 */
public Solution solve(final Matrix A0)
```

请注意，`solve`函数返回一个`LinearSystemSolver::Solution`类的对象。该对象包含由输入矩阵定义的线性系统的齐次解和非齐次解。其签名如下：

```
/**
 * This is the solution to a linear system of equations.
 */
public static interface Solution {
    /**
     * Get a particular solution for the linear system.
     *
     * @param b a vector
     * @return a particular solution
     */
    Vector getParticularSolution(Vector b);
    /**
     * Get the basis of the homogeneous solution for the linear system,
     * Ax = b.
     * That is, the solutions for Ax = 0.
     *
     * @return the homogeneous solution
     */
    List getHomogeneousSoln();
}
```

## 2.4.7 超定线性系统

如果方程的数量多于未知数的数量，则该方程组是超定的；即，*m* > *n*，其中*m*是方程的数量，*n*是未知数的数量。*m* × *n* 的矩阵***A***是一个高矩阵：

![$$ \boldsymbol{Ax}=\boldsymbol{b} $$](img/528380_1_En_2_Chapter_TeX_Equde.png)

超定系统通常是不一致的（无解）。唯一的例外是某些方程在系统中多次出现，或者等价地，某些方程是其他方程的线性组合。一般来说，每个未知数提供一个自由度。引入系统的每个方程限制一个自由度。因此，当方程的数量和自由变量的数量相等时，会出现临界情况。对于每个提供自由度的变量，都存在一个相应的约束。如果一致，这个方形方程组可以有一个唯一解。当系统被过度约束时，即方程数量超过未知数数量时，就会出现超定情况。

例如，图 2-27 显示了三个线性无关的方程，使得系统不一致，因此无解。

![](img/528380_1_En_2_Fig27_HTML.jpg)

一个超定系统的图示。三条线代表三个线性方程。

**图 2-27** 一个超定系统

在图 2-28 中，一个方程线性依赖于（实际上等同于）另一个方程。该系统有两个变量和两个（线性无关的）约束，因此系统是一致的，并有一个解。

![](img/528380_1_En_2_Fig28_HTML.jpg)

一个图。它包含两条线，其中一条代表两个相关方程的组合。

**图 2-28** 一个方程线性依赖于另一个方程；一个解

对于一个不存在解的超定系统，你应该尝试寻找“最佳近似”。定义“最佳近似”的方法有很多。普通最小二乘法（OLS）找到最小化最小平方的***x***解。即：

![$$ \underset{\textbf{x}}{\min }{\left\Vert \boldsymbol{Ax}-\boldsymbol{b}\right\Vert}_2 $$](img/528380_1_En_2_Chapter_TeX_Equdf.png)

从几何角度讲，OLS 在数据点{(***x***, ***)}***之间画一条（高维）线（或超平面），使得平方距离之和最小。见图 2-29。

![](img/528380_1_En_2_Fig29_HTML.png)

不同值的散点图显示在图表上，沿着一条从最高值到最低值的对角线。

**图 2-29** 超定系统的 OLS 解

解由以下公式给出：

![$$ \boldsymbol{x}={\left({\boldsymbol{A}}^T\boldsymbol{A}\right)}^{-1}{\boldsymbol{A}}^T\boldsymbol{b} $$](img/528380_1_En_2_Chapter_TeX_Equdg.png)

使用此公式，当不存在精确解时会找到一个近似解，而当存在精确解时它会给出一个精确解，前提是(***A***^(*T*)***A***)^(−1)存在（或等价地，***A***具有满列秩）。然而，在实践中，由于计算(***A***^(*T*)***A***)^(−1)时存在数值稳定性问题，我们不使用这个公式。相反，我们使用正交分解方法。

NM Dev 提供了两种正交分解方法的实现，一种基于 QR 分解，另一种基于 SVD 分解。

使用 QR 分解，你可以将***A***写成如下形式：

# QR 分解与 SVD 分解在超定线性系统中的应用

![QR分解公式](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equdh.png)

超定线性系统`Ax = b`可以重写为`Q₁R₁x = b`。由于`Q₁`是正交矩阵，可得：

![正交矩阵推导](img/528380_1_En_2_Chapter_TeX_Equdi.png)

由于`R₁`是上三角矩阵，可以使用回代法求解该系统。

以下 NM Dev 代码使用 QR 方法求解该超定系统：

![超定系统示例](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equdj.png)

```
// define an overdetermined system of linear equations
val A: Matrix = DenseMatrix(
    arrayOf(
        doubleArrayOf(1.0, 1.0),
        doubleArrayOf(1.0, 2.0),
        doubleArrayOf(1.0, 3.0),
        doubleArrayOf(1.0, 4.0)
    )
)
val b: Vector = DenseVector(arrayOf(6.0, 5.0, 7.0, 10.0))
val problem: LSProblem = LSProblem(A, b)
// solve the system using QR method
val solver1: OLSSolverByQR = OLSSolverByQR(0.0) // precision
// compute the OLS solution
val x1: Vector = solver1.solve(problem)
println("the OLS solution = " + x1)
```

输出如下：

```
the OLS solution = [3.500000, 1.400000]
```

使用 SVD 分解，`A`可以写成：

![SVD分解公式](img/528380_1_En_2_Chapter_TeX_Equdk.png)

伪逆为`A⁺ = VD⁺Uᵀ`，其中`D⁺`通过对角线上每个非零元素取倒数得到，零元素保持不变。在数值计算中，只有大于某个小容差的元素才被认为是非零的，其余的被替换为零。超定线性系统`Ax = b`可以按如下方式求解：

![SVD求解公式](img/528380_1_En_2_Chapter_TeX_Equdl.png)

以下 NM Dev 代码求解与之前相同的示例：

```
// solve the system using SVD method
val solver2: OLSSolverBySVD = OLSSolverBySVD(0.0) // precision
// compute the OLS solution
val x2: Vector = solver2.solve(problem)
// verify that Ax2 = b
// val Ax2: Vector = A.multiply(x2)
println("The OLS solution = " + x2)
```

输出如下：

```
the OLS solution = [3.500000, 1.400000]
```

输出结果与使用 QR 方法时完全相同。

## `LSProblem`类

NM Dev 中的`LSProblem`类构造了一个线性系统问题，可以通过多种求解器求解。该类使用构建器设计模式来构造一个`LSProblem`，该问题可自定义求解器的行为，例如迭代次数、精度、初始猜测以及左、右预处理器。预处理器是一种变换的应用，它将问题转化为更适合数值求解方法的形式。这个过程称为*预处理*。预处理通常用于减少数值问题的条件数。预处理后的问题通常通过迭代方法求解。`LSProblem`的签名如下：

## `public LSProblem(Matrix A, Vector b)`

构造一个线性方程组 `Ax = b`。

- `@param A` 线性方程组的齐次部分，即系数矩阵
- `@param b` 线性方程组的非齐次部分，即右侧向量

## `public ImmutableMatrix A()`

获取线性方程组的齐次部分，即系数矩阵。

- `@return` 系数矩阵

## `public ImmutableVector b()`

获取线性方程组的非齐次部分，即右侧向量。

- `@return` 向量

## `public int size()`

获取线性方程组中变量的个数。

- `@return` 变量个数

## `public LSProblem withMaxIteration(int maxIteration)`

覆盖最大迭代次数。

- `@param maxIteration` 最大迭代次数
- `@return` 覆盖了最大迭代次数的新问题

## `public int getMaxIteration()`

获取指定的最大迭代次数。

- `@return` 最大迭代次数

## `public LSProblem withTolerance(Tolerance tolerance)`

覆盖容差实例。

- `@param tolerance` 判定解收敛并停止迭代的标准
- `@return` 覆盖了容差的新问题

## `public Tolerance getTolerance()`

获取指定的 `{@link Tolerance}` 实例。

- `@return` `{@link Tolerance}` 实例

## `public LSProblem withInitialGuess(Vector initialGuess)`

覆盖解的初始猜测。

- `@param initialGuess` 解的初始猜测
- `@return` 覆盖了初始猜测的新问题

## `public Vector getInitialGuess()`

获取该问题的解的初始猜测。

- `@return` 初始猜测

## `public LSProblem withLeftPreconditioner(Preconditioner preconditioner)`

覆盖左预处理器。如果使用右预处理，请将其保留为默认值 - `{@link IdentityPreconditioner}`。

- `@param preconditioner` 预处理器
- `@return` 覆盖了左预处理器的新问题

## `public Preconditioner getLeftPreconditioner()`

获取左预处理器。

- `@return` 左预处理器

## `public LSProblem withRightPreconditioner(Preconditioner preconditioner)`

覆盖右预处理器。如果使用左预处理，请将其保留为默认值 - `{@link IdentityPreconditioner}`。

- `@param preconditioner` 预处理器
- `@return` 覆盖了右预处理器的新问题

## `public Preconditioner getRightPreconditioner()`

获取右预处理器。

- `@return` 右预处理器

# 2.5 稀疏矩阵

在数值计算中，稀疏矩阵（向量）是指大部分元素为零的矩阵（向量）。对于矩阵中需要有多少个零元素才能被视为稀疏，并没有严格的定义，但一个常见的标准是，非零元素的数量大致与行数或列数相当。相比之下，如果大部分元素都是非零的，则该矩阵被视为稠密矩阵。零值元素数量除以总元素数量，有时被称为矩阵的*稀疏度*。例如，下面的稀疏矩阵只包含 9 个非零元素，而有 26 个零元素。其稀疏度为 74%，稠密度为 26%。

![$$ \left[\begin{array}{ccccccc}11&amp; 22&amp; &amp; &amp; &amp; &amp; \\ {}&amp; 33&amp; 44&amp; &amp; &amp; &amp; \\ {}&amp; &amp; 55&amp; 66&amp; 77&amp; &amp; \\ {}&amp; &amp; &amp; &amp; &amp; 88&amp; \\ {}&amp; &amp; &amp; &amp; &amp; &amp; 99\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equdm.png)

从概念上讲，稀疏性对应于具有少量成对交互的系统。例如，考虑一排用弹簧依次连接的球：这是一个稀疏系统，因为只有相邻的球是耦合的。相比之下，如果同一排球中每个球都与其他所有球有弹簧连接，那么这个系统就对应一个稠密矩阵。稀疏性这一概念在组合数学以及网络理论和数值分析等应用领域中非常有用，这些领域通常具有较低密度的重要数据或连接。在求解偏微分方程和优化问题时，大型稀疏矩阵经常出现在科学或工程应用中。

在计算机上存储和操作稀疏矩阵时，采用能够利用矩阵稀疏结构的专用算法和数据结构是有益且通常必要的。使用标准的稠密矩阵结构和算法来处理大型稀疏矩阵是缓慢且低效的，因为处理能力和内存在零元素上被浪费了。稀疏数据本质上更容易压缩，因此所需存储空间显著减少。一些非常大的稀疏矩阵实际上无法使用标准的稠密矩阵算法进行操作。

通常，对于一个 `m` × `n` 的矩阵，以稠密格式存储该矩阵所需的内存量与 `m` × `n` 成正比（忽略矩阵维度本身也需要存储这一事实）。对于稀疏矩阵，可以通过仅存储非零条目来实现内存需求的显著降低。根据非零条目的数量和分布，可以使用不同的数据结构，与基本方法相比，可以大幅节省内存。其代价是访问单个元素变得更加复杂，并且需要额外的结构来明确地恢复原始矩阵。

NM Dev 库支持两种格式组。

* 支持高效修改的格式，例如键字典和列表的列表。这些通常用于构建矩阵。
* 支持高效访问和矩阵运算的格式，例如压缩稀疏行格式。这些通常用于支持矩阵运算。

## 2.5.1 键字典

键字典（DOK）由一个将（行，列）对映射到元素值的字典组成。也就是说，非零值被哈希化，以便可以在常数时间内完成添加、删除和检索操作。字典中缺失的元素被视为零。这种格式适用于以随机顺序增量式地构建稀疏矩阵，但不适合按字典序迭代非零值。您通常使用这种格式构建矩阵，然后将其转换为另一种更高效的格式进行处理。

以下 NM Dev 代码使用 DOK 以两种方式为此矩阵构建稀疏矩阵。您可以使用数组或列表来指定非零条目。

![$$ \boldsymbol{A}=\left[\begin{array}{cccc}1&amp; 2&amp; 0&amp; 0\\ {}0&amp; 3&amp; 9&amp; 0\\ {}0&amp; 1&amp; 4&amp; 0\end{array}\right] $$](images/528380_1_En_2_Chapter/528380_1_En_2_Chapter_TeX_Equdn.png)

```kotlin
// the target matrix in dense representation
val A: Matrix = DenseMatrix(arrayOf(
    doubleArrayOf(1.0, 2.0, 0.0, 0.0),
    doubleArrayOf(0.0, 3.0, 9.0, 0.0),
    doubleArrayOf(0.0, 1.0, 4.0, 0.0)
))
// DOK
val B1: SparseMatrix = DOKSparseMatrix(3, 4, // 3x4 dimension
    listOf( // specify only the non-zero entries
        SparseMatrix.Entry(MatrixCoordinate(3, 3), 4.0),
        SparseMatrix.Entry(MatrixCoordinate(2, 2), 3.0),
        SparseMatrix.Entry(MatrixCoordinate(1, 1), 1.0),
        SparseMatrix.Entry(MatrixCoordinate(3, 2), 1.0),
        SparseMatrix.Entry(MatrixCoordinate(2, 3), 9.0),
        SparseMatrix.Entry(MatrixCoordinate(1, 2), 2.0))
)
//verify that B1 = A
println(String.format(
    "B1 = A, %b",
    MatrixPropertyUtils.areEqual(B1, A, 1e-15)))
val B2: SparseMatrix = DOKSparseMatrix(3, 4, // 3x4 dimension
    listOf( // specify only the non-zero entries
        SparseMatrix.Entry(MatrixCoordinate(3, 3), 4.0),
        SparseMatrix.Entry(MatrixCoordinate(2, 2), 3.0),
        SparseMatrix.Entry(MatrixCoordinate(1, 1), 1.0),
        SparseMatrix.Entry(MatrixCoordinate(3, 2), 1.0),
        SparseMatrix.Entry(MatrixCoordinate(2, 3), 9.0),
        SparseMatrix.Entry(MatrixCoordinate(1, 2), 2.0))
)
//verify that B2 = A
println(String.format(
    "B2 = A, %b",
    MatrixPropertyUtils.areEqual(B2, A, 1e-15)))
```

输出如下：

```
B1 = A, true
B2 = A, true
```

## 2.5.2 列表的列表

列表的列表（LIL）为每行存储一个列表，每个条目包含列索引和值。通常，这些条目按列索引排序以加快查找速度。这是另一种适合增量式矩阵构建的格式。

以下 NM Dev 代码使用 LIL 以两种方式为前面所示的 `A` 构建稀疏矩阵。您可以使用数组或列表来指定非零条目。

### 2.5.3 压缩稀疏行
压缩稀疏行（CSR）或压缩行存储（CRS）或耶鲁格式通过三个（一维）数组来表示一个矩阵，这些数组分别包含非零值、行的范围以及列索引。这种格式支持快速的行访问和矩阵-向量乘法`Av`。CSR 格式使用三个（一维）数组（`V`、`COL_INDEX`、`ROW_INDEX`）以行形式存储一个稀疏的`m` × `n`矩阵`A`。设`NNZ`为`A`中非零条目的数量。数组`V`和`COL_INDEX`的长度为`NNZ`，分别包含非零值以及这些值对应的列索引。

以下 NM Dev 代码通过两种方式使用 CSR 构造之前提到的`A`的稀疏矩阵。你可以使用数组或列表来指定非零条目。
```kotlin
// CSR
val D1: SparseMatrix = CSRSparseMatrix(3, 4, // 3x4 dimension
listOf( // specify only the non-zero entries
SparseMatrix.Entry(MatrixCoordinate(3, 3), 4.0),
SparseMatrix.Entry(MatrixCoordinate(2, 2), 3.0),
SparseMatrix.Entry(MatrixCoordinate(1, 1), 1.0),
SparseMatrix.Entry(MatrixCoordinate(3, 2), 1.0),
SparseMatrix.Entry(MatrixCoordinate(2, 3), 9.0),
SparseMatrix.Entry(MatrixCoordinate(1, 2), 2.0))
)
//verify that D1 = A
println(String.format(
"D1 = A, %b",
MatrixPropertyUtils.areEqual(D1, A, 1e-15)))
val D2: SparseMatrix = CSRSparseMatrix(3, 4, // 3x4 dimension
listOf( // specify only the non-zero entries
SparseMatrix.Entry(MatrixCoordinate(3, 3), 4.0),
SparseMatrix.Entry(MatrixCoordinate(2, 2), 3.0),
SparseMatrix.Entry(MatrixCoordinate(1, 1), 1.0),
SparseMatrix.Entry(MatrixCoordinate(3, 2), 1.0),
SparseMatrix.Entry(MatrixCoordinate(2, 3), 9.0),
SparseMatrix.Entry(MatrixCoordinate(1, 2), 2.0)))
//verify that D2 = A
println(String.format(
"D2 = A, %b",
MatrixPropertyUtils.areEqual(D2, A, 1e-15)))
```

输出如下：
```
D1 = A, true
D2 = A, true
```

### 2.5.4 稀疏矩阵/向量运算
在 NM Dev 中，你可以使用`SparseVector`类创建稀疏向量。示例如下：
```kotlin
// sparse vector construction
val v1: SparseVector = SparseVector(
99, // vector size
listOf(
SparseVector.Entry(1, 11.0),
SparseVector.Entry(3, 22.0),
SparseVector.Entry(53, 33.0),
SparseVector.Entry(79, 44.0),
SparseVector.Entry(99, 55.0)
)
)
println("v = " + v1)
```

输出如下：
```
v1 = size: 99
[1] 11.0000
[3] 22.0000
[53] 33.0000
[79] 44.0000
[99] 55.0000
```

所有这些稀疏矩阵和向量数据结构与所有其他矩阵及向量数据结构完全互操作。稀疏数据结构完全支持所有矩阵和向量运算，例如加法和乘法。示例如下：
```kotlin
// addition
val M1: Matrix = B1.add(A)
println("M1 = " + M1)
```

输出如下：
```
M1 = 3x4
[,1] [,2] [,3] [,4]
[1,] 2.000000, 4.000000, 0.000000, 0.000000,
[2,] 0.000000, 6.000000, 18.000000, 0.000000,
[3,] 0.000000, 2.000000, 8.000000, 0.000000,
```

```kotlin
val M2: DOKSparseMatrix = B1.add(B2) as DOKSparseMatrix
println("M2 = " + M2)
```

输出如下：
```
M2 = 3x4 nnz = 6
(1, 1): 2.0
(1, 2): 4.0
(2, 2): 6.0
(2, 3): 18.0
(3, 2): 2.0
(3, 3): 8.0
```

```kotlin
val M3: LILSparseMatrix = C1.minus(C2) as LILSparseMatrix
println("M3 = " + M3)
```

输出如下：
```
M3 = 3x4 nnz = 0
```

```kotlin
val M4: CSRSparseMatrix = D1.multiply(D2.t()) as CSRSparseMatrix
println("M4 = " + M4)
```

输出如下：
```
M4 = 3x3 nnz = 9
(1, 1): 5.0
(1, 2): 6.0
(1, 3): 2.0
(2, 1): 6.0
(2, 2): 90.0
(2, 3): 39.0
(3, 1): 2.0
(3, 2): 39.0
(3, 3): 17.0
```

```kotlin
val v2: SparseVector = SparseVector(
4, // vector size
listOf(
SparseVector.Entry(1, 11.0)
)
)
println("v2 = " + v2)
```

输出如下：
```
v2 = size: 4
[1] 11.0000
```

```kotlin
val v3: Vector = B1.multiply(v2)
println("ve = " + v3)
```

输出如下：

```
ve = size: 3
[1] 11.0000
```

NM Dev 还支持多种其他针对特殊结构的矩阵，如下所示：

*   `DiagonalMatrix`

*   `BidiagonalMatrix`

*   `TridiagonalMatrix`

*   `LowerTriangularMatrix`

*   `UpperTriangularMatrix`

*   `SymmetricMatrix`

*   `PermutationMatrix`

这些数据结构具有其专属的运算，但它们也与所有其他矩阵和向量数据结构完全互操作。它们也完全支持所有矩阵和向量运算，例如加法和乘法。

### 2.5.5 求解稀疏矩阵方程

求解线性方程组是数值计算的基础。你可以利用问题的稀疏性来处理原本无法应对的更大规模问题。例如，前面章节讨论的求解器——如`LU`、`QR`和`SVD`分解——都适用于稠密矩阵。它们会用零填充整个矩阵（想象一下百万乘以百万规模的矩阵），并且需要反复填充大量的零元素。这些稠密矩阵可能无法装入计算机内存。对这些满矩阵的操作效率低下，且浪费在零元素上。求解线性方程组的算法通常是迭代法，这类方法利用稀疏矩阵的优势，不会改变稀疏模式，因为它们只涉及矩阵-向量乘积，不会产生填充。

求解 *n* × *n*（或非方阵）线性系统 `Ax` = `b` 的迭代方法，涉及一系列矩阵-向量乘法。从解的初始猜测开始，每次迭代都会返回一个新的解估计值。希望经过 *k* 次迭代后，估计值能收敛到令人满意的解（在容差范围内）。对于稠密矩阵 `A`，每次迭代需要 *O*(*n*²) 次运算。一个迭代方法需要 *O*(*kn*²) 次运算才能收敛。对于稀疏系统，矩阵-向量乘法只需要 *O*(#nonZeros) 次运算，其中 #nonZeros 是稀疏矩阵中非零元素的个数。因此，迭代法可能比传统的直接法（如求逆）求解线性方程组快得多。使用稀疏矩阵的迭代法比使用稠密矩阵的迭代法快得多。

`NM Dev` 支持多种稀疏矩阵线性系统求解器。它们如下：

- 双共轭梯度求解器（`BiCG`）

- 双共轭梯度稳定求解器（`BiCGSTAB`）

- 共轭梯度求解器（`CG`）

- 共轭梯度法向误差求解器（`CGNE`）

- 共轭梯度法向残差求解器（`CGNR`）

- 共轭梯度平方求解器（`CGS`）

- 高斯-赛德尔求解器

- 广义共轭残差求解器（`GRES`）

- 广义最小残差求解器（`GMRES`）

- 雅可比求解器

- 最小残差求解器（`MINRES`）

- 拟最小残差求解器（`QMR`）

- 最速下降求解器

- 逐次超松弛求解器（`SOR`）

- 对称逐次超松弛求解器（`SSOR`）

以下是选择稀疏系统迭代求解器的一些指南。对于埃尔米特问题，如果系统是正定的，使用`CG`或`MINRES`；否则，使用`MINRES`。为了避免在`CG`或`MINRES`中计算内积，你可以选择定常方法，如`Jacobi`、`Gauss-Seidel`、`SOR`或`SSOR`。这些方法在每次迭代中节省了计算成本，但迭代次数可能会增加，除非有良好的预处理。

对于非埃尔米特问题，选择就没那么容易了。如果矩阵-向量乘法非常昂贵，`GMRES`可能是最佳选择，因为它执行的乘法次数最少。次优的选择是`QMR`或`BiCG`。`QMR`在数值上比`BiCG`更稳定。当矩阵转置不可用时，有免转置的方法，如`CGS`或`BiCGSTAB`。对于非方阵系统，有用于求解超定系统的`CG`方法，如`CGNR`，以及用于求解欠定系统的方法，如`CGNE`。

使用预处理可以显著提高迭代方法的收敛速度。预处理将一个线性系统转化为另一个等价的系统（即具有相同解）。转化后的系统具有更有利的谱性质，从而影响收敛速度。特别地，预处理矩阵 `M` 近似于系数矩阵 `A`，且转化后的系统更容易求解。以下是一个示例：

![$$ {\boldsymbol{M}}^{-1}\boldsymbol{Ax}={\boldsymbol{M}}^{-1}\boldsymbol{b} $$](img/528380_1_En_2_Chapter_TeX_Equdo.png)

该方程组与原系统具有相同的解。其系数矩阵 `M`^(-1)`A` 的谱性质可能更有利。另一种预处理系统的方式如下：

![$$ {\boldsymbol{M}}_1^{-1}{\boldsymbol{AM}}_2^{-1}\left({\boldsymbol{M}}_{\textbf{2}}\boldsymbol{x}\right)={\boldsymbol{M}}_1^{-1}\boldsymbol{b} $$](img/528380_1_En_2_Chapter_TeX_Equdp.png)

矩阵 `M`1 和 `M`2 分别称为左预处理和右预处理。有三种预处理方式：左预处理、右预处理或分裂预处理。左预处理将 `M`2 设置为 `IdentityPreconditioner`。（当不应用任何预处理时，使用此单位预处理。）同样，右预处理将 `M`1 设置为 `IdentityPreconditioner`。

在 `NM Dev` 中，所有稀疏矩阵线性系统求解器都继承自 `IterativeLinearSystemSolver` 接口。其签名如下：

```java
public interface IterativeLinearSystemSolver {
    /**
     * 这是一个使用迭代求解器求解线性方程组的解。
     */
    public static interface Solution extends IterativeMethod {
        @Override
        public IterationMonitor step() throws ConvergenceFailure;//覆盖返回和异常类型
        @Override
        public Vector search(Vector... initials) throws ConvergenceFailure;//覆盖异常类型
    }
    /**
     * 迭代求解
     *
     * Ax = b
     *
     * 直到解收敛，即残差 (b - Ax) 的范数小于等于阈值。
     *
     * @param problem 一个线性方程组
     * @param monitor 一个迭代监视器
     * @return 线性问题的一个（近似）解
     * @throws ConvergenceFailure 如果算法未能收敛
     */
    public Solution solve(LSProblem problem, IterationMonitor monitor) throws ConvergenceFailure;
}
```

以下 `NM Dev` 代码使用多个非定常稀疏线性系统求解器求解一个稀疏线性系统。一般步骤是：首先使用 `LSProblem` 定义一个线性系统问题。然后为该问题构建一个求解器。这里有很多选择。求解问题会得到 `IterativeLinearSystemSolver.Solution`。利用该解对象，你可以从初始猜测（在这些示例中为 0）开始迭代搜索。搜索算法返回一个向量 `x` 作为解。通过使用 *ℓ*[2]-范数比较 `Ax` 与 `b` 之间的差异来验证结果的有效性。示例如下：
```

# 对称矩阵求解示例

## 矩阵定义与系统构建

定义一个8x8的对称稀疏矩阵 `A`：

```
// 对称矩阵：
// 8x8
// [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8]
// [1,] 7.000000, 0.000000, 1.000000, 0.000000, 0.000000, 2.000000, 7.000000, 0.000000,
// [2,] 0.000000, -4.000000, 8.000000, 0.000000, 2.000000, 0.000000, 0.000000, 0.000000,
// [3,] 1.000000, 8.000000, 1.000000, 0.000000, 0.000000, 0.000000, 0.000000, 5.000000,
// [4,] 0.000000, 0.000000, 0.000000, 7.000000, 0.000000, 0.000000, 9.000000, 0.000000,
// [5,] 0.000000, 2.000000, 0.000000, 0.000000, 5.000000, 1.000000, 5.000000, 0.000000,
// [6,] 2.000000, 0.000000, 0.000000, 0.000000, 1.000000, -1.000000, 0.000000, 5.000000,
// [7,] 7.000000, 0.000000, 0.000000, 9.000000, 5.000000, 0.000000, 11.000000, 0.000000,
// [8,] 0.000000, 0.000000, 5.000000, 0.000000, 0.000000, 5.000000, 0.000000, 5.000000,
```

```kotlin
val A: Matrix = CSRSparseMatrix(8, 8, // 矩阵维度
    listOf(
        SparseMatrix.Entry(MatrixCoordinate(1, 1), 7.0),
        SparseMatrix.Entry(MatrixCoordinate(1, 3), 1.0),
        SparseMatrix.Entry(MatrixCoordinate(1, 6), 2.0),
        SparseMatrix.Entry(MatrixCoordinate(1, 7), 7.0),
        SparseMatrix.Entry(MatrixCoordinate(2, 2), -4.0),
        SparseMatrix.Entry(MatrixCoordinate(2, 3), 8.0),
        SparseMatrix.Entry(MatrixCoordinate(2, 5), 2.0),
        SparseMatrix.Entry(MatrixCoordinate(3, 1), 1.0),
        SparseMatrix.Entry(MatrixCoordinate(3, 2), 8.0),
        SparseMatrix.Entry(MatrixCoordinate(3, 3), 1.0),
        SparseMatrix.Entry(MatrixCoordinate(3, 8), 5.0),
        SparseMatrix.Entry(MatrixCoordinate(4, 4), 7.0),
        SparseMatrix.Entry(MatrixCoordinate(4, 7), 9.0),
        SparseMatrix.Entry(MatrixCoordinate(5, 2), 2.0),
        SparseMatrix.Entry(MatrixCoordinate(5, 5), 5.0),
        SparseMatrix.Entry(MatrixCoordinate(5, 6), 1.0),
        SparseMatrix.Entry(MatrixCoordinate(5, 7), 5.0),
        SparseMatrix.Entry(MatrixCoordinate(6, 1), 2.0),
        SparseMatrix.Entry(MatrixCoordinate(6, 5), 1.0),
        SparseMatrix.Entry(MatrixCoordinate(6, 6), -1.0),
        SparseMatrix.Entry(MatrixCoordinate(6, 8), 5.0),
        SparseMatrix.Entry(MatrixCoordinate(7, 1), 7.0),
        SparseMatrix.Entry(MatrixCoordinate(7, 4), 9.0),
        SparseMatrix.Entry(MatrixCoordinate(7, 5), 5.0),
        SparseMatrix.Entry(MatrixCoordinate(7, 7), 11.0),
        SparseMatrix.Entry(MatrixCoordinate(8, 3), 5.0),
        SparseMatrix.Entry(MatrixCoordinate(8, 6), 5.0),
        SparseMatrix.Entry(MatrixCoordinate(8, 8), 5.0)
    )
)
```

构造一个全为1的稠密向量 `b`：

```kotlin
val b: Vector = DenseVector( // 注意我们仍然可以使用稠密数据结构
    arrayOf(1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0)
)
```

然后构造一个待求解的线性系统问题 `problem`：

```kotlin
val problem: LSProblem = LSProblem(A, b)
```

## 使用BiCG方法求解

构造一个双共轭梯度法（BiCG）求解器 `BiCG`：

```kotlin
val BiCG: BiconjugateGradientSolver
    = BiconjugateGradientSolver(
    10, // 最大迭代次数
    AbsoluteTolerance(1e-8) // 精度
)
```

求解问题，获得解 `soln1`，并使用0作为初始猜测搜索解向量 `x1`：

```kotlin
val soln1: IterativeLinearSystemSolver.Solution = BiCG.solve(problem)
val x1: Vector = soln1.search(SparseVector(A.nCols())) // 使用 0 作为初始猜测
```

输出解 `x1` 并验证解的精度：

```kotlin
println("x = " + x1)
val Ax1_b: Vector = A.multiply(x1).minus(b) // 验证 Ax = b
println("||Ax - b|| = " + Ax1_b.norm()) // 应（接近）为 0
```

输出如下：

```
x = [-0.041860, -0.003413, 0.117250, -0.112640, 0.024172, -0.107633, 0.198720, 0.190383]
||Ax - b|| = 3.4321107842718707E-13
```

## 使用BiCGSTAB方法求解

构造一个稳定双共轭梯度法（BiCGSTAB）求解器 `BiCGSTAB`：

```kotlin
val BiCGSTAB: BiconjugateGradientStabilizedSolver
    = BiconjugateGradientStabilizedSolver(
    10, // 最大迭代次数
    AbsoluteTolerance(1e-7) // 较低精度
)
```

求解问题，获得解 `soln2`，并使用0作为初始猜测搜索解向量 `x2`：

```kotlin
val soln2: IterativeLinearSystemSolver.Solution = BiCGSTAB.solve(problem)
val x2: Vector = soln2.search(SparseVector(A.nCols())) // 使用 0 作为初始猜测
```

输出解 `x2` 并验证解的精度：

```kotlin
println("x = " + x2)
val Ax2_b: Vector = A.multiply(x2).minus(b) // 验证 Ax = b
println("||Ax - b|| = " + Ax2_b.norm()) // 应（接近）为 0
```

输出如下：

```
x = [-0.041860, -0.003413, 0.117250, -0.112640, 0.024172, -0.107633, 0.198720, 0.190383]
||Ax - b|| = 6.001732649867594E-8
```

使用各种迭代线性系统求解器求解问题的代码示例如下：

```kotlin
val CGNE: `ConjugateGradientNormalErrorSolver`
    = `ConjugateGradientNormalErrorSolver`(
    10, // 最大迭代次数
    `AbsoluteTolerance`(1e-8) // 精度
)
val soln3: `IterativeLinearSystemSolver.Solution` = `CGNE.solve`(`problem`)
val x3: `Vector` = `soln3.search`(`SparseVector`(`A.nCols()`)) // 使用 0 作为初始猜测
`println`("x = " + x3)
val Ax3_b: `Vector` = `A.multiply`(x3).`minus`(b) // 验证 Ax = b
`println`("||Ax - b|| = " + `Ax3_b.norm`()) // 应（接近）为 0
```

输出如下：

```
x = [-0.041860, -0.003413, 0.117250, -0.112640, 0.024172, -0.107633, 0.198720, 0.190383]
||Ax - b|| = 1.6870268466872934E-9
```

```kotlin
val CGNR: `ConjugateGradientNormalResidualSolver`
    = `ConjugateGradientNormalResidualSolver`(
    10, // 最大迭代次数
    `AbsoluteTolerance`(1e-8) // 精度
)
val soln4: `IterativeLinearSystemSolver.Solution` = `CGNR.solve`(`problem`)
val x4: `Vector` = `soln4.search`(`SparseVector`(`A.nCols()`)) // 使用 0 作为初始猜测
`println`("x = " + x4)
val Ax4_b: `Vector` = `A.multiply`(x4).`minus`(b) // 验证 Ax = b
`println`("||Ax - b|| = " + `Ax4_b.norm`()) // 应（接近）为 0
```

输出如下：

```
x = [-0.041860, -0.003413, 0.117250, -0.112640, 0.024172, -0.107633, 0.198720, 0.190383]
||Ax - b|| = 6.817480703009663E-10
```

```kotlin
val CG: `ConjugateGradientSolver`
    = `ConjugateGradientSolver`(
    10, // 最大迭代次数
    `AbsoluteTolerance`(1e-8) // 精度
)
val soln5: `IterativeLinearSystemSolver.Solution` = `CG.solve`(`problem`)
val x5: `Vector` = `soln5.search`(`SparseVector`(`A.nCols()`)) // 使用 0 作为初始猜测
`println`("x = " + x5)
val Ax5_b: `Vector` = `A.multiply`(x5).`minus`(b) // 验证 Ax = b
`println`("||Ax - b|| = " + `Ax5_b.norm`()) // 应（接近）为 0
```

输出如下：

```
x = [-0.041860, -0.003413, 0.117250, -0.112640, 0.024172, -0.107633, 0.198720, 0.190383]
||Ax - b|| = 3.4321107842718707E-13
```

```kotlin
val CGS: `ConjugateGradientNormalResidualSolver`
    = `ConjugateGradientNormalResidualSolver`(
    10, // 最大迭代次数
    `AbsoluteTolerance`(1e-8) // 精度
)
val soln6: `IterativeLinearSystemSolver.Solution` = `CGS.solve`(`problem`)
val x6: `Vector` = `soln6.search`(`SparseVector`(`A.nCols()`)) // 使用 0 作为初始猜测
`println`("x = " + x6)
val Ax6_b: `Vector` = `A.multiply`(x6).`minus`(b) // 验证 Ax = b
`println`("||Ax - b|| = " + `Ax6_b.norm`()) // 应（接近）为 0
```

输出如下：

```
x = [-0.041860, -0.003413, 0.117250, -0.112640, 0.024172, -0.107633, 0.198720, 0.190383]
||Ax - b|| = 6.817480703009663E-10
```

```kotlin
val GRES: `GeneralizedConjugateResidualSolver`
    = `GeneralizedConjugateResidualSolver`(
    10, // 最大迭代次数
    `AbsoluteTolerance`(1e-8) // 精度
)
val soln7: `IterativeLinearSystemSolver.Solution` = `GRES.solve`(`problem`)
val x7: `Vector` = `soln7.search`(`SparseVector`(`A.nCols()`)) // 使用 0 作为初始猜测
`println`("x = " + x7)
val Ax7_b: `Vector` = `A.multiply`(x7).`minus`(b) // 验证 Ax = b
`println`("||Ax - b|| = " + `Ax7_b.norm`()) // 应（接近）为 0
```

输出如下：

```
x = [-0.041860, -0.003413, 0.117250, -0.112640, 0.024172, -0.107633, 0.198720, 0.190383]
||Ax - b|| = 1.0175362097255202E-15
```

```kotlin
val GMRES: `GeneralizedMinimalResidualSolver`
    = `GeneralizedMinimalResidualSolver`(
    10, // 最大迭代次数
    `AbsoluteTolerance`(1e-8) // 精度
)
val soln8: `IterativeLinearSystemSolver.Solution` = `GMRES.solve`(`problem`)
val x8: `Vector` = `soln8.search`(`SparseVector`(`A.nCols()`)) // 使用 0 作为初始猜测
`println`("x = " + x8)
val Ax8_b: `Vector` = `A.multiply`(x8).`minus`(b) // 验证 Ax = b
`println`("||Ax - b|| = " + `Ax8_b.norm`()) // 应（接近）为 0
```

输出如下：

```
x = [-0.041860, -0.003413, 0.117250, -0.112640, 0.024172, -0.107633, 0.198720, 0.190383]
||Ax - b|| = 1.0175362097255202E-15
```

```kotlin
x = [-0.041860, -0.003413, 0.117250, -0.112640, 0.024172, -0.107633, 0.198720, 0.190383]
||Ax - b|| = 1.0053497077208614E-15

val MINRES: `MinimalResidualSolver` = `MinimalResidualSolver`(
    10, // 最大迭代次数
    `AbsoluteTolerance`(1e-8) // 精度
)
val monitor: `CountMonitor` = `CountMonitor`()
val soln9: `IterativeLinearSystemSolver.Solution` = `MINRES.solve`(`problem`, monitor)
val x9: `Vector` = `soln9.search`(`SparseVector`(`A.nCols()`)) // 使用 0 作为初始猜测
`println`("x = " + x9)
val Ax9_b: `Vector` = `A.multiply`(x9).`minus`(b) // 验证 Ax = b
`println`("||Ax - b|| = " + `Ax9_b.norm`()) // 应（接近）为 0
```

输出如下：

# NM Dev 稀疏线性系统求解示例

```kotlin
println("使用稳态迭代求解器求解稀疏线性系统")
var A: Matrix = SymmetricMatrix(arrayOf(
    doubleArrayOf(4.0),
    doubleArrayOf(1.0, 3.0)))
val b = DenseVector(arrayOf(1.0, 2.0))
// 构造待求解的线性系统问题
val problem: LSProblem = LSProblem(A, b)
// 构造一个稀疏矩阵线性系统求解器
val gauss_seidel: GaussSeidelSolver = GaussSeidelSolver(10, AbsoluteTolerance(1e-4))
val soln1: IterativeLinearSystemSolver.Solution = gauss_seidel.solve(problem)
val x1 = soln1.search(SparseVector(A.nCols())) // 使用 0 作为初始猜测
println("x = " + x1)
val Ax1_b = A.multiply(x1).minus(b) // 验证 Ax = b
println("||Ax - b|| = " + Ax1_b.norm()) // 应（接近）为 0
```

输出如下：

```
x = [0.090917, 0.636361]
||Ax - b|| = 2.8131430040989613E-5
```

```kotlin
val QMR: QuasiMinimalResidualSolver
    = QuasiMinimalResidualSolver(
        10, // 最大迭代次数
        AbsoluteTolerance(1e-8) // 精度
    )
val soln10: IterativeLinearSystemSolver.Solution = QMR.solve(problem)
val x10: Vector = soln10.search(SparseVector(A.nCols())) // 使用 0 作为初始猜测
println("x = " + x10)
val Ax10_b: Vector = A.multiply(x10).minus(b) // 验证 Ax = b
println("||Ax - b|| = " + Ax10_b.norm()) // 应（接近）为 0
```

输出如下：

```
x = [-0.041860, -0.003413, 0.117250, -0.112640, 0.024172, -0.107633, 0.198720, 0.190383]
||Ax - b|| = 5.933770145831627E-13
```

```kotlin
// 构造一个稀疏矩阵线性系统求解器
val jacobi: JacobiSolver = JacobiSolver(10, AbsoluteTolerance(1e-4))
val soln2: IterativeLinearSystemSolver.Solution = jacobi.solve(problem)
val x2 = soln2.search(SparseVector(A.nCols()))
println("x = " + x2)
val Ax2_b = A.multiply(x1).minus(b) // 验证 Ax = b
println("||Ax - b|| = " + Ax2_b.norm()) // 应（接近）为 0
```

输出如下：

```
x = [0.090917, 0.636365]
||Ax - b|| = 2.8131430040989613E-5
```

```kotlin
val SOR: SuccessiveOverrelaxationSolver = SuccessiveOverrelaxationSolver(
    1.5,
    20, // 需要更多迭代
    AbsoluteTolerance(1e-4))
val soln3: IterativeLinearSystemSolver.Solution = SOR.solve(problem)
val x3 = soln3.search(SparseVector(A.nCols())) // 使用 0 作为初始猜测
println("x = " + x3)
val Ax3_b = A.multiply(x3).minus(b) // 验证 Ax = b
println("||Ax - b|| = " + Ax3_b.norm()) // 应（接近）为 0
```

输出如下：

```
x = [0.090909, 0.636344]
||Ax - b|| = 6.462981890138403E-5
```

```kotlin
val SSOR: SymmetricSuccessiveOverrelaxationSolver
    = SymmetricSuccessiveOverrelaxationSolver(
        1.5,
        20, // 需要更多迭代
        AbsoluteTolerance(1e-4))
val soln4: IterativeLinearSystemSolver.Solution = SSOR.solve(problem)
val x4 = soln4.search(SparseVector(A.nCols())) // 使用 0 作为初始猜测
println("x = " + x4)
val Ax4_b = A.multiply(x4).minus(b) // 验证 Ax = b
println("||Ax - b|| = " + Ax4_b.norm()) // 应（接近）为 0
```

输出如下：

```
x = [0.090904, 0.636348]
||Ax - b|| = 6.213495416129946E-5
```

