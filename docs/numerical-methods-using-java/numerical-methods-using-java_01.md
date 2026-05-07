# 1. Java 数值方法导论

早在 2009 年，我在伦敦公寓的阁楼里编写了矩阵乘法算法的第一行代码，即经典的 *O*(*n*³) 版本，那时这仅仅是一种爱好。我用 Java 编写交易策略，但找不到一个好用且专业的 Java 数学库（即，具有专业维护、文档详实且有示例、扎实的面向对象设计、开放的数据结构且功能丰富的库）。大部分策略代码是用 `R`/`MATLAB` 开发的，而交易/订单执行系统则用的是 Java。然而，让 Java 与 `R`/`MATLAB` 通信，反之亦然，简直是一场噩梦，并由此引发了许多其他令 IT 头疼的问题（例如，多线程安全、多实例、调试、`R`/`MATLAB` 速度非常慢，以及莫名其妙的“不知道为什么不工作”问题）。

我认为，如果有一个像当时无处不在的“C 语言数值算法”那样的 Java 数学库，世界将从中受益。一个简单的方法是（自动）将 C 代码翻译成 Java。然而，这只会给我带来一堆函数（过程），它们只能完成函数本身的功能。没有数据结构和模块（或子算法）可以让我复用来构建新的数据结构或新算法。向量不应该只是一堆数字或一个数组。向量是一个数学概念，具有明确定义的属性和运算，任何处理向量的算法都应该理解这一点，也就是说，它不应该重新编码这些属性和运算。

虽然当时有一些 Java 数值库，最著名的是 Apache Commons Math，但没有一个被设计成高性能的。自 FORTRAN/C 时代以来，现代计算已经取得了长足的进步。我们现在有了并发编程、更高效的算法（用于矩阵乘法等）、像 GPU 这样的新硬件等等。简单地翻译 FORTRAN/C 代码或教科书式的算法实现无法利用这些进步。性能必然非常缓慢。我们需要多线程、高度优化过的数据结构和最高效的算法。例如，我们没有使用简单的 *O*(*n*³) 算法进行矩阵乘法，而是实际实现了 Strassen 算法的并行化版本。经过团队过去 10 年大量的反复调整、实验和测试，最终的结果是我们现在可能拥有了 Java 中最快的线性代数实现。

我对现有的 Java 数学库存在的另一个问题是它们范围有限。一个量化交易策略可能简单如移动平均线交叉，但也可能很复杂，涉及求解随机微分方程。它肯定包含大量的统计分析。最后但同样重要的是，它需要一套求解器来优化各种东西，比如策略参数。几乎所有统计程序都涉及某种优化，例如回归和最大似然估计。目前没有既好用、又免费或廉价、并且是用 Java 编写的优化器。像 Gurobi 和 Mosek 这样的专业优化器价格昂贵。

一定是有什么东西砸到了我的脑袋，让我失去了理智，因为我从投资银行的交易员岗位上辞职了。我踏上了一场伟大的冒险，着手创建一个满足以下所有条件的 Java 数学库：

*   基于数据结构与软件工程进行良好设计
*   对人类（而非机器）易于阅读
*   充分利用现代计算技术
*   具备高性能
*   功能广泛，涵盖线性代数、微积分、统计学、时间序列分析、优化、随机微分方程等领域



当我辞职时，并未意识到这件事做起来有多困难。我不知道有多少堪比赫拉克勒斯式的艰巨任务需要克服。我当时相当天真，只觉得创造一些（理想中）能与世界分享的东西会很有趣。少年时期，我曾编写自己的数学书来整理数学笔记。我认为用代码来组织算法会更有用，这算是童年爱好的延续。

结果发现，构建一个数学库，尤其是一个优秀的数学库，极具挑战性。这远不止是将数学公式翻译成代码那么简单。首先，我们需要理解算法中的每一个细节。对于其中许多算法，我们阅读了原始文献。一些古老的文献仿佛是用晦涩难懂的语言写成的，理解起来十分困难。许多论文遗漏了细节，还有许多存在错误和笔误。对于我们实现的每一个算法，都必须重新推导数学上的每一步，以确保我们理解了所有细节，并保证算法的正确性。这与学习通常编写良好、配有大量示例的教学用教科书完全不同。

有许多概念我们习以为常，以至于不会去思考其背后的细节。例如，我们经常使用矩阵的特征值和特征向量，但很少有人关心矩阵分解究竟是如何工作的。要编写一个线性代数包，我们需要理解矩阵的多种不同分解和因式分解方法，以及它们的优缺点、精度、数值稳定性、调试与测试、API 设计、用户友好性，以及其他所有细微之处。这个过程绝非易事。

其次，我们必须验证代码的正确性。如果论文附带示例，我们会复现它们以确保结果匹配。这些示例通常为数不多且比较简单。我们需要涵盖广泛的测试用例、边界情况、压力测试和性能测试。我们的基准是 `R`。我们会始终将结果与 `R` 的结果进行比对。当出现差异时，我们必须解释每一项差异。我们对代码的准确性非常认真，并在每个版本中持续修复错误。在某些情况下，我们的代码甚至比 `R` 还要精确。例如，我们先前不知道 `R` 的 Johansen 检验只有少数几个关键点，比如 10%、5%、1%，而对于其他值，它是在这些点之间进行线性插值（通过 `R` 的源代码得知）。而另一方面，我们则使用精确分布函数计算了 Johansen 检验的渐近分布。仅此一项就花费了大量时间进行调试和阅读 `R` 的源代码。

第三，我们设计的软件必须用户友好，并符合良好的软件工程原则。大多数流行的数学库，例如 NETLIB，都是几十年前用 FORTRAN/C 编写的。它们既不用户友好，也不是良好的编程实践（因为那时计算机科学还不够发达）。让我感到困扰的一点是，一个函数通常有几十个参数，并且参数名难以辨认和记忆（因为当时“对象”和数据结构的概念还不存在，变量名长度限制在 8 个字符以内）。我常常记不住变量的含义，漏掉一些参数，或者搞乱它们的顺序。按照现代标准来看，它们的使用体验实在是太不友好了。

代码通常难以阅读。好的代码读起来应该像英语一样流畅，并且易于其他专业程序员维护，无需作者坐在旁边指导。在编写数学库时，我们尽力让代码行与论文对应，以便其他人可以通过对比来理解我们的逻辑。这样做的目的是为了让其他人能够修复我们的错误、拓展我们的工作、将其翻译成另一种语言（例如 `C#`），并移植到其他硬件（例如 GPU）上。要做到这些，他们需要理解我们的代码。我认为，这对于几十年前的 FORTRAN/C 代码来说几乎是不可行的。我敢打赌，一旦作者离开，除了运行它并相信它没有错误之外，没有人能对这些代码做任何事情。我曾通过阅读原始论文，用 `Java` 编写了 `ALGORITHM AS 288`。我尝试理解 `AS 288` 的 FORTRAN 代码（见图 1-1）以作对比，但至今仍然没有成功。那段 FORTRAN 代码似乎只是毫无明显理由地在到处移动变量，然后神奇地得到了正确结果。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig1_HTML.png](img/500382_1_En_1_Fig1_HTML.png)

图 1-1

很难记住 `A`、`B`、`C`、`K`、`N`、`X`、`Y` 这些变量（AS 108）的含义和顺序

我们构建这样一个库的首次尝试是 `SuanShu`，它以中国已知最早的数学著作之一《算数书》命名，该书大约写于公元前 200 年。自 2009 年起，这已经是十年之功。它最初只是一个基础的线性代数包，后来发展成为一个涵盖微积分、优化算法、统计分析、微分方程等多个领域的庞大库。它始于我个人的爱好，随后世界各地的数十位专家、教授、博士、学生和从业者为这个库做出了贡献。它曾被一些知名商业组织和学术机构采用。2018 年，我们决定停止销售和支持 `SuanShu`。我们将截至 2012 年 6 月的代码库开源。

基于我们十年的数值编程经验，`NM Dev` 是一个全新的、从头开始构建的数值库，它是 `SuanShu` 的继任者。在过去 10 年中，我们收集了大量用户反馈和错误报告。我们希望通过一个新的软件基础和架构来体现所有这些良好建议。

## 1.1 库的设计

`SuanShu` 以及后续的 `NM Dev` 的目标是让工程应用的编程变得简单易行。程序员能够以一种源代码完全面向对象且可独立测试的方式来编写数学程序。`NM Dev` 的源代码遵循最严格的编码标准，以保证其可读性、可维护性，并且易于修改和扩展。

`NM Dev` 彻底改变了传统数值计算的方式，即使用 `Netlib` 或 `GSL` 的方式。这些最流行、最“标准”的库的仓库，是用 FORTRAN 和 C 等老语言编写的临时性源代码的集合。这些代码最大的问题之一是不可读（对大多数现代程序员而言），因此也就无法维护。例如，理解 `AS 288` 本身就是一个挑战，更不用说改进了。其他问题包括但不限于：缺乏数据结构、代码重复、完全过程化、变量命名糟糕、滥用 `GOTO`、缺乏测试用例、文档不足、缺乏 IDE 支持、与 `Java` 等现代语言链接不便、对并行计算不友好等。

为了解决这些问题，`NM Dev` 是一个可复用的数学组件（而非过程）框架，使得程序员可以像搭乐高积木一样将组件组合起来，构建更复杂的算法。`NM Dev` 是完全重新编写的，使其符合变量命名、代码结构、可重用性、可读性、可维护性以及软件工程流程等现代编程范式。为了确保高质量的代码和极少的错误，`NM Dev` 拥有数千个每日运行的单元测试用例。



### 1.1.1 类的精简原则

我们决定尽可能精简类库，以避免方法泛滥。这一理念源于`jMatrices`白皮书，其挑战在于将方法组织成最小且正确的包。我们将以 NM Dev 矩阵包为例进行说明。

`Matrix`类仅有 26 个方法，其中 9 个是构造函数及其相关方法；3 个是`AbstractMatrix`接口的重写方法；8 个是`MatrixSpace`接口的重写方法。只有 6 个方法是该类特有的，旨在方便用户调用。其他数十种矩阵操作（例如不同的分解方法、秩等属性以及求逆等变换）则被分组到多个类和包中。大多数情况下，每个操作本身就是一个独立的类。例如，求逆操作本身就是一个继承自`Matrix`的类。其构造函数将待求逆的矩阵作为输入。例如，要计算以下矩阵的逆：

![$$ A=\left[\begin{array}{ccc}1&amp; 2&amp; 3\\ {}6&amp; 5&amp; 4\\ {}8&amp; 7&amp; 9\end{array}\right] $$](images/500382_1_En_1_Chapter/500382_1_En_1_Chapter_TeX_Equa.png)

NM Dev 代码如下：

```
Matrix A = new DenseMatrix(
new double[][]{
{1, 2, 3},
{6, 5, 4},
{8, 7, 9}
});
Matrix Ainv = new Inverse(A);
System.out.println(Ainv);
```

输出结果如下：

```
3x3
[,1] [,2] [,3]
[1,] -0.809524, -0.142857, 0.333333,
[2,] 1.047619, 0.714286, -0.666667,
[3,] -0.095238, -0.428571, 0.333333,
```

需要注意的是，对象`Ainv`属于`Matrix`类，通过关键字`new`创建，而非通过方法调用。总之，我们选择拥有数百个类，而不是一个包含数百个方法的类。每个类都被刻意保持短小精悍。这种类的精简原则是指导整个库开发的关键设计决策。

### 1.1.2 Java 与 C++ 性能对比

FORTRAN、C 和 C++ 因其接近硬件的性能，历来是数学领域的常用语言。现代 Java 的性能已可与 C++ 媲美。过去对 Java 速度慢的误解，很大程度上是因为 1995 年的 Java 1 确实比 C++ 慢。自那时起，Java 已有了长足的进步。在我撰写本文时，Java 已发展到 16 版本，且 17 版本即将发布。与 C/C++ 相比，Java 现在是一种具有竞争力的技术。事实上，要为 C/C++ 进行实际优化，你需要找到“合适的”程序员来编写代码。这位程序员需要了解 C/C++ 的所有性能问题、性能分析和代码优化（如循环展开），甚至可能需要编写汇编代码片段。一个普通的 C/C++ 程序员编写的代码可能并不比 Java 快。（我通常反对代码优化技术，因为它们使代码难以阅读，进而难以维护，就像 Netlib 和 Statlib 中的许多 FORTRAN/C/C++ 代码一样。）

更重要的是，大多数现代软件运行在多核上。代码优化技术在并行计算技术面前相形见绌。在 Java 中编写并发编程代码比在 C++ 中要容易得多、高效得多（也愉快得多）。因此，编写高性能软件时，我个人更倾向于在 Java 中为多核、多 CPU 和云进行编码，而不是在 C/C++ 中进行代码优化。

总的来说，在通用编程语言中，只要可能，我更喜欢使用 Java 而不是 C/C++、FORTRAN、汇编等，因为 Java 是一种易于学习和使用的编程语言，同时又不会牺牲性能。它一直是构建系统和应用程序最流行的语言。

（至于 FORTRAN，我不确定为什么 FORTRAN 在 2022 年仍然存在。你该如何阅读成千上万行全部大写/小写、充斥着各种 Cs 和 GOTOs 的代码呢？）

## 1.2 环境搭建

本节将引导您完成开发环境的安装步骤，并使用 NM Dev 编写您的第一个数学程序。

### 1.2.1 安装 Java 开发工具包

Java 开发工具包（JDK）是构建和运行任何 Java 应用程序的必备组件。它是一个开发工具包，包含用于编译、调试、构建和运行 Java 程序的工具。Java 程序员必须在其机器上正确安装 JDK。还有 JRE，即 Java 运行时环境。JRE 是运行任何 Java 程序的先决条件。也就是说，JDK 供开发、编码、测试、调试和构建 Java 程序的程序员使用；JRE 供用户运行 Java 应用程序。您编写的 Java 代码，一旦完成，可以在支持 JRE 及其变体的超过 130 亿台设备上运行。JRE 是 JDK 下载包的一部分。

JDK 可从 Sun Microsystems（现为 Oracle 的一部分）免费获取。自 1995 年以来，已发布了多个版本。在撰写本文时，最新版本是 Java SE 16（也称为 JDK 16）。每次发布都会带来新功能，使 Java 更强大、更易于使用。例如，2014 年 3 月发布的 JDK 8 为 Java 带来了 lambda 表达式，使代码更加简洁。要使用 NM Dev，您需要安装 JDK 8 或更高版本。以下是在 Windows 上安装 JDK 16 的步骤。

**第 1 步：下载 JDK**

前往 Oracle 的 JDK 下载站点，如图 1-2 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig2_HTML.png](img/500382_1_En_1_Fig2_HTML.png)

图 1-2: JDK 下载

```
https://www.oracle.com/au/java/technologies/javase-downloads.html
```

单击 JDK 下载。

在下一页，单击下载 Windows x64 安装程序，如图 1-3 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig3_HTML.png](img/500382_1_En_1_Fig3_HTML.png)

图 1-3: JDK Windows 安装程序

在对话框中，选择“我已审阅并接受 Oracle Java SE 的 Oracle 技术网络许可协议”，然后单击下载按钮，如图 1-4 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig4_HTML.png](img/500382_1_En_1_Fig4_HTML.png)

图 1-4: Oracle Java 协议

**第 2 步：安装 JDK**

运行安装程序 `jdk-16.0.2_windows-x64_bin.exe`（或根据您将来下载时的类似名称）来安装 JDK 16。安装向导将引导您完成整个过程，如图 1-5 所示。如果您接受默认设置，只需一路单击“下一步”。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig5_HTML.png](img/500382_1_En_1_Fig5_HTML.png)

图 1-5: JDK 安装向导

JDK 16 将安装在目录 `C:\Program Files\Java\jdk-16.0.2` 中，如图 1-6 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig6_HTML.png](img/500382_1_En_1_Fig6_HTML.png)

图 1-6: JDK 默认安装目录

完成后，单击“关闭”，如图 1-7 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig7_HTML.png](img/500382_1_En_1_Fig7_HTML.png)

图 1-7: JDK 安装完成

**第 3 步：验证 JDK 安装**

要检查 JDK 16 是否已正确安装在机器上，我们检查 Java 版本。首先，打开 Windows 命令提示符。这可以通过在 Windows 菜单的搜索按钮中键入 `cmd` 并选择“命令提示符”来完成，如图 1-8 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig8_HTML.png](img/500382_1_En_1_Fig8_HTML.png)

图 1-8: Windows 命令提示符



接下来运行命令 `java -version` 查看你的 JDK 版本。如果安装成功，你应该能看到版本号。本例中，版本号是 16.0.2，如图 1-9 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig9_HTML.png](img/500382_1_En_1_Fig9_HTML.png)

**图 1-9** Java 版本号

#### 步骤 4：设置 PATH 环境变量

某些旧版本的 JDK 安装程序需要你手动设置 `PATH` 环境变量。（JDK 16 似乎不需要这样做。我在安装 JDK 16 时并没有设置。）如果你遇到如下错误，请尝试以下操作：

```
is not recognized as an internal or external command, operable program or batch file.
```

我们可以手动在 Windows 中设置 `PATH` 环境变量，以便任何 Java 应用程序（例如 IDE）都能找到 Java 运行时并启动它。

转到控制面板，然后依次点击“系统和安全”与“系统”，如图 1-10 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig10_HTML.png](img/500382_1_En_1_Fig10_HTML.png)

**图 1-10** 控制面板 ➤ 系统和安全

然后点击“高级系统设置”，如图 1-11 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig11_HTML.png](img/500382_1_En_1_Fig11_HTML.png)

**图 1-11** 高级系统设置

接着点击“环境变量”，如图 1-12 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig12_HTML.png](img/500382_1_En_1_Fig12_HTML.png)

**图 1-12** 系统属性对话框

在“系统变量”的 `PATH` 变量中添加 JDK 安装目录下的 `bin` 文件夹路径。本例中为 `C:\Program Files\Java\jdk-16.0.2\bin`，如图 1-13 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig13_HTML.png](img/500382_1_En_1_Fig13_HTML.png)

**图 1-13** Windows 环境变量

#### 在 macOS 上安装 JDK 的步骤类似

##### 步骤 1：下载 JDK

前往 Oracle 的 JDK 下载网站。

```
https://www.oracle.com/au/java/technologies/javase-downloads.html
```

点击 JDK 下载。

在下一页，点击下载 macOS 安装程序。

在对话框中，勾选“我已审阅并接受 Oracle Java SE 的 Oracle 技术网络许可协议”，然后点击下载按钮。

##### 步骤 2：安装 JDK

打开安装文件（`.dmg` 文件）。按照安装向导进行 JDK 安装。安装完成后，弹出 `.dmg` 文件。

##### 步骤 3：验证 JDK 安装

我们通过 Launchpad 打开“终端”；然后点击“其他”，再选择“终端”。在终端中，运行 `java -version` 以检查是否已安装 JDK 16。

```
> java -version
java version "16.0.2" 2021-07-20
```

### 1.2.2 NetBeans 与 Maven

集成开发环境（IDE）是一种为计算机程序员提供软件开发综合设施的软件应用程序。它是我们编写代码的地方。IDE 通常至少包含源代码编辑器、构建自动化工具和调试器。它使浏览、导航、错误检查、编码、调试、运行和测试我们的程序变得更加容易。它是程序员不可或缺的工具。流行的 Java IDE 有 NetBeans、IntelliJ IDEA 和 Eclipse。所有这些工具都是免费的。IntelliJ IDEA 为专业程序员提供付费版本。

我们可以从其网站下载 NetBeans。

```
https://netbeans.apache.org/download/
```

截至撰写本文时，最新的可用版本是 12.4，如图 1-14 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig14_HTML.png](img/500382_1_En_1_Fig14_HTML.png)

**图 1-14** NetBeans 下载

下载并运行安装程序，如图 1-15 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig15_HTML.png](img/500382_1_En_1_Fig15_HTML.png)

**图 1-15** NetBeans 安装程序

连续点击“下一步”以同意许可协议和默认设置，如图 1-16 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig16_HTML.png](img/500382_1_En_1_Fig16_HTML.png)

**图 1-16** NetBeans 许可协议

默认安装文件夹是 `C:\Program Files\NetBeans-12.4`。它会自动找到我们刚刚安装的 JDK 的位置，如图 1-17 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig17_HTML.png](img/500382_1_En_1_Fig17_HTML.png)

**图 1-17** NetBeans 文件夹

点击“安装”，如图 1-18 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig18_HTML.png](img/500382_1_En_1_Fig18_HTML.png)

**图 1-18** NetBeans 安装设置

等待安装过程完成，如图 1-19 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig19_HTML.png](img/500382_1_En_1_Fig19_HTML.png)

**图 1-19** NetBeans 正在安装

等待设置完成，如图 1-20 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig20_HTML.png](img/500382_1_En_1_Fig20_HTML.png)

**图 1-20** NetBeans 设置完成

要启动 NetBeans IDE，我们前往安装目录下的 `bin` 文件夹。要运行的可执行文件是 `netbeans64.exe`，如图 1-21 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig21_HTML.png](img/500382_1_En_1_Fig21_HTML.png)

**图 1-21** NetBeans 可执行文件

双击 `netbeans64.exe` 打开 IDE。我们将看到欢迎启动页面，如图 1-22 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig22_HTML.png](img/500382_1_En_1_Fig22_HTML.png)

**图 1-22** NetBeans 欢迎页面

首次使用 NetBeans 时，它可能会要求您下载并激活 nb-javac Impl，如图 1-23 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig23_HTML.png](img/500382_1_En_1_Fig23_HTML.png)

**图 1-23** nb-javac Impl

这是一个 Java 编辑支持库，如图 1-24 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig24_HTML.png](img/500382_1_En_1_Fig24_HTML.png)

**图 1-24** nb-javac Java 编辑支持库

接受协议，如图 1-25 所示。



![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig25_HTML.png](img/500382_1_En_1_Fig25_HTML.png)

图 1-25

`nb-javac` API 许可协议

点击“安装”，如图 1-26 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig26_HTML.png](img/500382_1_En_1_Fig26_HTML.png)

图 1-26

`nb-javac` 安装完成

为了创建我们的第一个数值编程项目，请进入“文件”菜单，然后选择“新建项目”，如图 1-27 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig27_HTML.png](img/500382_1_En_1_Fig27_HTML.png)

图 1-27

选择“新建项目”

在“类别”中选择“Java with Maven”，在“项目”中选择“Java 应用程序”，如图 1-28 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig28_HTML.png](img/500382_1_En_1_Fig28_HTML.png)

图 1-28

Java with Maven

Maven 是一个主要用于 Java 项目的构建自动化工具。Maven 解决软件构建的两个方面：如何构建软件及其依赖关系。它使用一个 XML 文件来描述正在构建的软件项目、其对外部模块和组件的依赖关系、构建顺序、目录以及所需的插件。在我们的场景中，它指定了要下载哪些 Java 库（如 NM Dev 库）以及它们的下载位置。

让我们为第一个项目命名，例如 `nmdevproject1`。我将该项目放在一个名为 `C:\Temp` 的临时文件夹中，如图 1-29 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig29_HTML.png](img/500382_1_En_1_Fig29_HTML.png)

图 1-29

我们的第一个 NM Dev 项目

我们需要打开并编辑名为 `pom.xml` 的 Maven 文件，以告知 IDE 在哪里下载 NM Dev 库。该文件可以在“项目”窗口的“项目文件”下找到。我们需要在 POM 文件中添加两个部分。

NM Dev 的分发包部署在 NM Maven 仓库中。为了让 Maven 知道这个仓库，请在 `pom.xml` 文件的 `<repositories>` 部分中添加以下仓库定义：

```
nm-repo
NM Maven Repository
http://repo.nm.dev/maven/
default
```

为了在项目中使用 NM Dev 库，请在 `<dependencies>` 部分中添加以下内容：

```
dev.nm
nmdev
1.0.0
```

这两部分共同指示 Maven 前往 NM Maven 仓库下载 `nmdev` JAR 文件，版本为 1.0.0，如图 1-30 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig30_HTML.png](img/500382_1_En_1_Fig30_HTML.png)

图 1-30

NM Maven 仓库

为了创建我们的第一个类（是的，所有 Java 文件都称为*类*），我们右键单击包，然后选择“新建”，接着选择“Java 类”，如图 1-31 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig31_HTML.png](img/500382_1_En_1_Fig31_HTML.png)

图 1-31

新建 ➤ Java 类

将这个类命名为 `HelloNM`，如图 1-32 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig32_HTML.png](img/500382_1_En_1_Fig32_HTML.png)

图 1-32

`HelloNM`

现在，我们准备编写第一个用于数值编程的类了！在 IDE 已经为您创建的类主体 `{ ... }` 中，输入以下代码：

```
public static void main(String[] args) {
System.out.println("Hello NM!");
Vector v = new DenseVector(1., 2., 3.);
System.out.println(v);
}
```

`main` 是任何 Java 程序的入口点。

```
public static void main(String[] args) {
```

`System.out.println` 会打印出引号内的任何内容。

```
System.out.println("Hello NM!");
```

以下代码片段是我们的第一个数值编程代码。它定义了一个向量。

![$$ \boldsymbol{v}=\left(1,2,3\right) $$](img/500382_1_En_1_Chapter_TeX_Equb.png)

```
Vector v = new DenseVector(1., 2., 3.);
```

现在这个代码本身是无法编译的。IDE 应该会显示一些错误消息，提示找不到某些类。我们需要导入在 Java 程序中使用的那些类，例如 `Vector`、`DenseVector`。大多数现代 IDE 可以为您自动完成此操作。要在 NetBeans 中执行此操作，请按 Ctrl+Shift+I（同时按下 Ctrl、Shift 和 I 这三个键）。这将在类的顶部自动添加两行导入语句。

```
import dev.nm.algebra.linear.vector.doubles.Vector;
import dev.nm.algebra.linear.vector.doubles.dense.DenseVector;
```

要运行代码，请右键单击编辑器，然后点击“运行文件”，如图 1-33 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig33_HTML.png](img/500382_1_En_1_Fig33_HTML.png)

图 1-33

运行文件

哎呀……它失败了！见图 1-34。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig34_HTML.png](img/500382_1_En_1_Fig34_HTML.png)

图 1-34

缺少 NM Dev 许可证文件

它显示以下内容：

```
Exception in thread "main" dev.nm.misc.license.LicenseError: license file not found - put the license file in the classpath, working directory, or next to nmdev.jar (loaded license from: license not yet loaded)
```

我们需要将名为 `nm.lic` 的 NM 许可证文件放入项目文件夹中，如图 1-35 所示。该许可证文件可以从官网获取。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig35_HTML.png](img/500382_1_En_1_Fig35_HTML.png)

图 1-35

NM Dev 许可证文件

```
https://nm.dev
```

让我们再试一次。哇，成功了！以下是我们使用 NM Dev 进行的第一个数值编程！见图 1-36。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig36_HTML.png](img/500382_1_En_1_Fig36_HTML.png)

图 1-36

你好，NM！

输出结果如下：

```
--- exec-maven-plugin:3.0.0:exec (default-cli) @ nmdevproject1 ---
Hello NM!
[1.000000, 2.000000, 3.000000]

BUILD SUCCESS

Total time:  5.296 s
Finished at: 2021-08-11T02:37:14+10:00
```

NM Dev 是一个包含大量数学和数值算法的大型 Java 库。它涵盖的主题包括线性代数、方程求解、曲线拟合、数值微分与积分、常微分方程和偏微分方程、线性规划、二次规划、二阶锥规划、半定规划、序列二次规划、整数规划、遗传算法、基础统计与概率、随机数生成、线性回归以及时间序列分析。它是许多其他 Java 库（如 AlgoQuant，一个大型金融分析库）的构建基础。更多信息可参阅 NM Dev Javadoc。

```
https://nm.dev/html/javadoc/nmdev/
```



### 1.2.3 `nmdev.jar`

另外，你也可以从网站下载 `nmdev.jar`。

```
https://nm.dev
```

要在 NetBeans 中创建 NM Dev 项目，请打开 NetBeans IDE，单击“文件”，然后单击“新建项目”（`Ctrl+Shift+N`）。在“类别”中，选择“Java with Ant”；在“项目”中，选择“Java 应用程序”，如图 1-37 所示。单击“下一步”。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig37_HTML.png](img/500382_1_En_1_Fig37_HTML.png)

图 1-37  
NetBeans 中的 Java with Ant

在“项目名称”中，输入你喜欢的名称，例如 `NMSetUpGuide`。在“项目位置”中，输入你要保存项目的路径。确保勾选“使用专用文件夹存储库”复选框，如图 1-38 所示。单击“完成”。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig38_HTML.png](img/500382_1_En_1_Fig38_HTML.png)

图 1-38  
NetBeans 中的新 Java 应用程序

我们需要告诉这个 Java 项目我们将要调用 NM Dev 库。为此，右键单击“项目”选项卡上的“库”文件夹，然后单击“添加 JAR/文件夹”，如图 1-39 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig39_HTML.png](img/500382_1_En_1_Fig39_HTML.png)

图 1-39  
在 NetBeans 中添加 JAR/文件夹

浏览到文件 `nmdev.jar` 所在的位置。选中 `nmdev.jar`，然后点击“打开”，如图 1-40 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig40_HTML.png](img/500382_1_En_1_Fig40_HTML.png)

图 1-40  
NetBeans 中的 `nmdev.jar`

要在 NetBeans 中安装 NM Dev Javadoc，我们需要将 `nmdev.jar` 与其 Javadoc 关联起来。右键单击刚刚添加的 `nmdev.jar` 文件，然后选择“编辑”，如图 1-41 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig41_HTML.png](img/500382_1_En_1_Fig41_HTML.png)

图 1-41  
在 NetBeans 中添加 Javadoc

在弹出的窗口中，在“Javadoc”部分，单击“浏览”并选择 `nmdev-javadoc.jar` 文件。然后点击“打开”，如图 1-42 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig42_HTML.png](img/500382_1_En_1_Fig42_HTML.png)

图 1-42  
NetBeans 中的 `nmdev-javadoc.jar`

现在，我们已经创建了一个空的 NM Dev 项目。我们可以将上一节中的 NM Dev 代码复制、粘贴并运行到这个空项目中。

### 1.2.4 IntelliJ IDEA

IntelliJ IDEA 是另一个用于 Java 开发（及更多）的流行 IDE。你可以从 JetBrain 的网站免费下载社区版，如图 1-43 所示。

```
https://www.jetbrains.com/idea/download/
```

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig43_HTML.png](img/500382_1_En_1_Fig43_HTML.png)

图 1-43  
IntelliJ IDEA

要在 IntelliJ IDEA 中创建 Maven 项目，请转到菜单，选择“文件”，然后选择“新建”，再选择“项目”。在左侧面板中，选择“Maven”，如图 1-44 所示。然后点击“下一步”。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig44_HTML.png](img/500382_1_En_1_Fig44_HTML.png)

图 1-44  
在 IntelliJ IDEA 中创建新 Maven 项目

在下一页，我们将此项目命名为 `demo`。此处我将项目放在文件夹 `~/projects/demo` 中，如图 1-45 所示。点击“完成”。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig45_HTML.png](img/500382_1_En_1_Fig45_HTML.png)

图 1-45  
在 IntelliJ IDEA 中创建新项目

现在，我们在左侧看到一个“项目”面板，其中显示了项目中的文件和外部库。右侧是显示已打开文件的窗口，如图 1-46 所示。此处 IntelliJ IDEA 在创建项目后自动打开了 `pom.xml` 文件。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig46_HTML.png](img/500382_1_En_1_Fig46_HTML.png)

图 1-46  
IntelliJ IDEA 中的 `pom.xml`

我们将需要像在 NetBeans 中那样填写 Maven 信息。对于 IntelliJ IDEA，我们需要手动添加更多信息，例如源代码编码和 JDK 版本。我们还可以指定放置 NM 许可文件的文件夹。在 `pom.xml` 中，在标签 `<project></project>` 下的第一层级添加以下内容：

```
UTF-8

1.1.0-SNAPSHOT
license

nm-repo
NM 的 Maven 仓库
http://repo.nm.dev/maven/
默认

dev.nm
nmdev
${nmdev.version}

```

我们需要重新加载项目，以便 IntelliJ IDEA 识别新配置。这将同时启用一些功能，例如外部依赖库的自动补全。要重新加载，请右键单击项目名称，转到“Maven”，然后点击“重新加载项目”，如图 1-47 所示。等待窗口刷新，即表示重新加载完成。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig47_HTML.png](img/500382_1_En_1_Fig47_HTML.png)

图 1-47  
在 IntelliJ IDEA 中重新加载项目

接下来，让我们创建一个新类并添加一些代码。在左侧项目中，依次转到 `src`、`main`、`java`，最后右键单击。选择“新建”，然后点击“Java 类”，如图 1-48 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig48_HTML.png](img/500382_1_En_1_Fig48_HTML.png)

图 1-48  
在 IntelliJ IDEA 中新建 Java 类

在弹出的对话框中，输入类名 `Demo`，如图 1-49 所示。按回车键。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig49_HTML.png](img/500382_1_En_1_Fig49_HTML.png)

图 1-49  
在 IntelliJ IDEA 中新建 Java 类名称

一个名为 `Demo.java` 的文件已创建，其中包含一些最简的类骨架代码，如图 1-50 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig50_HTML.png](img/500382_1_En_1_Fig50_HTML.png)

图 1-50  
`Demo.java`

以下代码计算两个向量的内积：

```
public void innerProductDemo() {
Vector v1 = new DenseVector(new double[] {1., 2.});
Vector v2 = new DenseVector(new double[] {3., 4.});
double product = v1.innerProduct(v2);
System.out.println(product);
}
public static void main(String[] args) {
Demo demo = new Demo();
demo.innerProductDemo();
}
```

你可能注意到，当你输入时，会自动弹出一个自动补全项的菜单，提示可能的代码，如图 1-51 所示。当你选择所需的项并按回车键确认后，必要的导入语句会自动添加到文件中。这些功能节省了大量的编码工作量和时间。请注意，我们使用的是来自 NM Dev 库的 `Vector` 类。确保不要选择来自 `java.util` 的那个。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig51_HTML.png](img/500382_1_En_1_Fig51_HTML.png)

图 1-51  
IntelliJ IDEA 中的自动补全

要运行代码，我们右键单击文件选项卡，然后选择运行 `Demo.main()` 来执行此类的 main 方法，如图 1-52 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig52_HTML.png](img/500382_1_En_1_Fig52_HTML.png)

图 1-52  
运行 `main()` 方法

输出显示在一个面板中，如图 1-53 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig53_HTML.png](img/500382_1_En_1_Fig53_HTML.png)

图 1-53  
IntelliJ IDEA 中的输出面板



### 1.2.5 SuanShu

SuanShu 是 NM Dev 的一个免费替代品。其代码库日期为 2012 年 6 月 6 日，目前已不再支持或维护。你可以从这个 GitHub 仓库下载 SuanShu：

```
https://github.com/haksunli/SuanShu
```

点击 **Download ZIP** 按钮，如图 1-54 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig54_HTML.png](img/500382_1_En_1_Fig54_HTML.png)

图 1-54

下载 SuanShu

解压下载文件夹中的 `SuanShu-master.zip` 文件，将源代码提取到 `SuanShu-master` 文件夹中。源代码结构如图 1-55 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig55_HTML.png](img/500382_1_En_1_Fig55_HTML.png)

图 1-55

SuanShu 源代码

我们可以将 SuanShu 库和源代码加载到 IDE 中。打开 IntelliJ IDEA，点击 **Open**，如图 1-56 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig56_HTML.png](img/500382_1_En_1_Fig56_HTML.png)

图 1-56

在 IntelliJ IDEA 中打开

导航到 `SuanShu-master` 文件夹，找到 `pom.xml` 文件。点击 **Open**，然后选择“Open as project”。IntelliJ IDEA 窗口应如图 1-57 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig57_HTML.png](img/500382_1_En_1_Fig57_HTML.png)

图 1-57

IntelliJ IDEA 中的 SuanShu

点击 **File**，然后点击 **Project Structure**，如图 1-58 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig58_HTML.png](img/500382_1_En_1_Fig58_HTML.png)

图 1-58

IntelliJ IDEA 中的项目结构

确保项目 SDK 设置为你所需的 JDK 版本。我们可能想使用不同版本的 JDK 尝试相同的代码，以检查性能和数值精度。不同版本和实现的 JDK 之间可能存在（细微）差异，如图 1-59 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig59_HTML.png](img/500382_1_En_1_Fig59_HTML.png)

图 1-59

在 IntelliJ IDEA 中设置项目 JDK

右键单击 `src` 文件夹，选择“Mark Directory as”，然后选择 **Sources Root**，以告知 IDE 源代码的根文件夹位置，如图 1-60 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig60_HTML.png](img/500382_1_En_1_Fig60_HTML.png)

图 1-60

IntelliJ IDEA 中的根文件夹

为了演示如何使用 SuanShu Java 数学库，我们添加一个新的演示类。依次右键单击 `src`、`main`、`java`，然后右键单击包 `com.numericalmethod.suanshu` 文件夹。点击 **New**，然后点击 **Java Class**。将这个新类命名为 `Main.java`，如图 1-61 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig61_HTML.png](img/500382_1_En_1_Fig61_HTML.png)

图 1-61

IntelliJ IDEA 中的 `Main.java`

创建标准的 Java 入口点 `main` 方法，然后你就可以使用 SuanShu Java 数值库中的任何类了！见图 1-62。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig62_HTML.png](img/500382_1_En_1_Fig62_HTML.png)

图 1-62

`public static void main`

下面的 SuanShu 代码片段演示了如何缩放向量，如图 1-63 所示：

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig63_HTML.png](img/500382_1_En_1_Fig63_HTML.png)

图 1-63

SuanShu，缩放向量

```
Vector v1 = new DenseVector(new double[]{1, 2, 3, 4, 5});
Vector v2 = v1.scaled(2.0);
System.out.print(v2);
```

将鼠标悬停在红色文本上，按下 Option+Return 查看可用的导入选项，选择你想要导入的类，然后按 Return 键，如图 1-64 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig64_HTML.png](img/500382_1_En_1_Fig64_HTML.png)

图 1-64

在 IntelliJ IDEA 中导入类

导入语句将自动添加到源代码中。请注意，SuanShu 类的包前缀如下：

```
com.numericalmethod.suanshu
```

另一方面，NM Dev 类的包前缀如下：

```
dev.nm
```

然而，这对用户来说应该没有区别，因为在现代 IDE 中导入是自动的。我们不需要手动输入导入语句。见图 1-65。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig65_HTML.png](img/500382_1_En_1_Fig65_HTML.png)

图 1-65

SuanShu 导入

现在，我们可以通过点击绿色播放按钮（图 1-66 中的第 7 行）来运行代码。输出将显示在 IDE 的底部窗口中。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig66_HTML.png](img/500382_1_En_1_Fig66_HTML.png)

图 1-66

SuanShu 输出

恭喜你完成了第一个 SuanShu 数值编程代码！你可以在 GitHub 上找到更多 SuanShu 示例，如图 1-67 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig67_HTML.png](img/500382_1_En_1_Fig67_HTML.png)

图 1-67

GitHub 中的 SuanShu 示例

```
https://github.com/nmltd/suanshu-examples
```

SuanShu 拥有大量的数学算法集合，包括线性代数、方程求解、曲线拟合、数值微分与积分、常微分与偏微分方程、线性规划、二次规划、二阶锥规划、半定规划、序列二次规划、整数规划、遗传算法、基础统计与概率、随机数生成、线性回归以及时间序列分析。你可以在 SuanShu Javadoc 中找到更多信息。

```
http://numericalmethod.com/javadoc/suanshu/
```



## 1.3 关于本书

本书涵盖了大学基础应用数学的广泛内容。这并非一本典型的数学教材。大多数人觉得学习数学既困难又枯燥。我认为，主要原因在于多数人不擅长符号、抽象推理、技术定义和严谨证明。例如，极限的技术定义如下：

定义域为 `D`（`D` ⊆ `ℝ`）的函数 `f`，当 `x` 趋近于 `c`（`c` ∈ 闭包(`D`)）时收敛于极限 `L`：若对于任意 `ε` > 0，存在 `δ` > 0，使得当 `x` ∈ `D` 且 `|x − c|` < `δ` 时，有 `|f(x) − L|` < `ε`。

这个极限定义之所以精确且技术化，是因为它*是*现代数学的基础。数学家可以驾驭它，但大多数人做不到。仅仅是那些 `ε` 和 `δ` 符号就足以让大多数人望而却步。数学家经过训练，能够将句子可视化地转化为符号，并在脑海中构建图像。对大多数人来说，如果我们仅仅这样解释：函数的极限，就是当你尝试让 `x` 不断接近 `c` 时，函数所趋近的那个值，再结合图 1-68 来理解，就会容易得多。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig68_HTML.png](img/500382_1_En_1_Fig68_HTML.png)

图 1-68

![$$\underset{\boldsymbol{x}\to \boldsymbol{c}}{\boldsymbol{\lim}}\boldsymbol{f}\left(\boldsymbol{x}\right)$$](img/500382_1_En_1_Chapter_TeX_IEq1.png)

同样地，证明 `lim_{x→c} f(x) = f(c)` 当且仅当 `f` 连续，并不困难，但相当技术化，因为我们需要另一个连续性的定义，并且要处理极限的 `ε-δ` 定义。如果简单地说，观察图 1-68，这显而易见，就会容易得多。当然，数学家们不会这样工作。对大多数人显而易见的事情，对数学家来说不能“仅仅是显而易见”；而对数学家显而易见的事情，对外行人来说通常并不明显。

对于大多数数学应用者而言，技术性和严谨性既不重要，也非必要。本书是为应用数学的工程师而非创造数学的数学家所写。书中不使用技术定义，而是通过图片和图表来辅助解释数学概念；不用严谨证明，而是尝试给出直观理解，让你轻松看出其合理性。更重要的是，本书中的每个概念都配有示例代码。如果我们能实际动手操作，学习一个新的数学概念会变得容易得多。例如，与其证明线性回归在 `α`、`β`、`ε`、`y` 和 `X` 方面是数据集的最佳拟合直线，不如直接用代码在你自己的数据集中绘制这条直线。与其仅仅列出正态分布的公式，不如通过该分布生成一些随机数并亲自观察，这样会更有趣。动手操作和变换参数，也就是实验，是学习和练习数学的最佳方式。它能让原本过于抽象而难以掌握的想法变得具体。正如儒家学者荀子所指出的：“不闻不若闻之，闻之不若见之，见之不若知之，知之不若行之。”^(¹)

### 1.3.1 示例代码

本书中的示例代码采用如下格式：

```
Vector v = new DenseVector(1., 2., 3.);
System.out.println(v);
```

通常后面会跟随对应的输出，如下所示：

```
[1.000000, 2.000000, 3.000000]
```

为简洁起见，我省略了所有样板代码和导入语句。要复现示例代码，你需要在其外面包裹一个函数。示例如下：

```
public void a_vector() {
Vector v = new DenseVector(1., 2., 3.);
System.out.println(v);
}
```

你还需要使用现代 IDE 的自动导入功能来导入相关类。在 NetBeans 中，自动导入的快捷键是 `Ctrl+Shift+I`。

大部分示例代码你可以直接复制粘贴，应该就能运行。其中一些代码会引用（较大的）数据集，这些数据未在书中重现。为了方便你操作，我们已将源代码和数据集上传到 GitHub。地址如下：

```
https://github.com/nmltd/numerical-methods-java
https://github.com/apress/numerical-methods-java
```

源代码应该可以直接编译、构建并运行，如图 1-69 所示。

![../images/500382_1_En_1_Chapter/500382_1_En_1_Fig69_HTML.png](img/500382_1_En_1_Fig69_HTML.png)

图 1-69

GitHub 上的示例代码

祝编码愉快！

脚注 1



