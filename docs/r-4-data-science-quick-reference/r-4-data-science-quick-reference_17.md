# 13. 绘图：`ggplot2`

`ggplot2` 包包含大量用于创建各种图形的函数。要全面覆盖需要一整本书——事实上已有好几本这样的书——因此我无法在此详述。在本章中，我只会尝试让你了解该包的工作原理。

当你加载 `tidyverse` 时，`ggplot2` 包也会被加载，但你也可以单独使用它：

```r
library(ggplot2)
```

## `ggplot2` 的基本绘图组件

与 R 的基础图形不同，使用 `ggplot2` 时，你不需要通过绘制线条、点或任何所需元素到图形画布上来创建独立的图形组件。相反，你需指定如何将数据映射到抽象的图形美学属性，例如 `x` 和 `y` 坐标、颜色、形状等。然后指定这些美学属性应如何在图形中呈现，例如 `x` 和 `y` 坐标应该绘制为散点图还是线条。在此基础上，你还可以添加图形信息，例如散点图中的点应该是什么形状、颜色美学属性映射到什么颜色等。你通过分步添加属性来构建图形，这使得修改图形变得容易。如果你想在图中添加一条线性回归线，只需一个命令即可；由于 `ggplot2` 已经知道你的哪些数据变量被映射到了 `x` 和 `y` 坐标，它会自动计算线性回归并将其添加到图中。如果你想以对数比例绘制数据，只需告诉 `ggplot2` 轴应进行对数变换即可。

起初，`ggplot2` 可能看起来比 R 的基础图形更复杂，但你很快就会习惯。

`ggplot2` 的主要组件如下：

*   **数据**——显然，你有要绘制的数据。
*   **美学属性**——美学属性将数据映射到抽象的图形概念，例如 `x` 和 `y` 坐标、颜色和填充。
*   **几何对象**——几何对象决定了你要制作的图形类型，例如是直方图、散点图还是箱线图。
*   **统计变换**——统计变换指定了在绘图前应如何汇总数据。你的数据并非总是需要汇总，也就是说，统计变换可以是恒等映射。散点图不会对 `x` 和 `y` 坐标进行汇总计算，但回归线或直方图会。
*   **标度**——标度指定了通过美学属性映射到图形概念的数据应如何在图上实际放置。你的 `x` 和 `y` 坐标数据可能以米为单位，但这些米应映射到图上的点。标度负责处理这件事。
*   **坐标系**——坐标系允许你转换将数据缩放为图形组件的结果。例如，如果你想让图形以对数比例显示 `y` 轴，那么坐标变换可以完成此操作。
*   **分面**——分面根据数据中的变量将图形拆分为子图。

你可以使用 `ggplot()` 函数创建 `ggplot2` 图形。然后可以添加上述一个或多个组件。在打印该图形对象之前，不会发生任何事情；打印它才会生成图形。一个典型的模式是立即打印它，并在创建图形对象的同一语句中添加组件。

```r
ggplot(. . . ) + . . . 组件 . . .
```

要向图形添加组件，你使用加法。`ggplot2` 包不使用管道操作符。不过，你经常会看到数据通过管道传递给 `ggplot()` 调用，但在那之后，你必须记得使用加法而不是管道。

一个图层创建了你可以看到的（部分）图形。它至少必须包含数据、美学属性、一个统计变换（可以是恒等映射）和一个几何对象（该对象可能指定了统计变换）。在图形显示你的数据之前，你的图形必须至少有一个图层。

## 向绘图对象添加组件

我们能创建的最简单的图形是空图形。你可以通过调用不带任何参数的 `ggplot()` 来创建它。

```r
p <- ggplot()
```

你可以通过调用 `summary()` 函数来查看它的组成部分，但这里的大部分信息并不相关。我会在后续讲解中，高亮显示与本小节所见组件相关的行。

```
summary(p)
## data: [x]
## faceting: 
##     compute_layout: function
##     draw_back: function
##     draw_front: function
##     draw_labels: function
##     draw_panels: function
##     finish_data: function
##     init_scales: function
##     map_data: function
##     params: list
##     setup_data: function
##     setup_params: function
##     shrink: TRUE
##     train_scales: function
## vars: function
## super:  
```

要创建图形，你需要打印该图形对象。你可以显式地执行此操作：

```r
print(p)
```

或者直接将其输入到你的 R 终端中：

```r
p
```

但在本例中，图形是空的——我们在创建 `p` 时并未向其中添加任何内容——因此我没有展示结果。

由于在 R 脚本中将对象放在最外层会打印该对象，因此 `ggplot` 对象并不总是先赋值给变量再绘制。你完全可以只写 `ggplot()` 对象。

```r
ggplot()
```

为什么会有人创建空图形对象呢？你无法打印空对象。嗯，你可以打印，但你会得到一个空白的画布，所以这没什么意义。不过，你可以通过向其中添加组件来逐步构建图形。你可以从空图形开始，然后在单独的命令中添加所需的一切。

### 添加数据

你可以将数据作为参数添加到 `ggplot()` 中。如果你想向空图形添加数据，则需要使用几何对象；请参阅下面的示例。如果你在 `ggplot()` 函数中添加了数据，那么之后添加到图形中的所有组件都能访问这些数据。

使用一些随机测试数据，我们可以创建一个包含关联数据的图形对象。

```r
dat <- tibble(
  foo = runif(100),
  bar = 20 * foo + 5 + rnorm(100, sd = 10),
  baz = rep(1:2, each = 50)
)
p <- ggplot(data = dat)
```

如果你调用 `summary(p)`，你会看到以下行：

```
## data: foo, bar, baz [100x3]
```

出现在顶部。它显示了数据中的变量。这些变量尚未映射到任何图形对象；那是美学映射的职责。

### 添加美学映射

我们在图形中看到的是点、线、颜色等。要创建这些图形，`ggplot2` 需要知道数据中的哪些变量应被解释为坐标，哪些决定线条粗细，哪些决定颜色等。美学映射正是为此服务的。

思考一下这个图形对象：

```r
p <- ggplot(data = dat, aes(x = foo, y = bar, color = baz))
```

在此，我们指定了 `foo` 决定 x 坐标，`bar` 决定 y 坐标，`baz` 决定颜色。如果你查看图形的摘要，你会在 `data` 行下面看到从数据变量到图形对象的映射：

```
## data: foo, bar, baz [100x3]
## mapping:  x = ~foo, y = ~bar, colour = ~baz
```

打印（或显示）此图形将得到一个空图形，其 x 轴和 y 轴与数据中 x 和 y（`foo` 和 `bar`）值的范围相匹配。除此之外图形是空的，因为它没有指定几何对象。

### 添加几何对象

几何对象指定了图形的类型。它们使用从数据到图形属性的美学映射，并基于此创建图形。

最直接的图形之一是散点图。我们可以将 `geom_point()` 几何对象添加到之前创建的图形中，以获得散点图。

```r
p <- ggplot(data = dat, aes(x = foo, y = bar, color = baz)) +
  geom_point()
```

如果你调用 `summary(p)`，你会在输出底部看到以下行：

```
## geom_point: na.rm = FALSE
## stat_identity: na.rm = FALSE
## position_identity
```

它们告诉你，你有一个点几何对象，并且将数据映射到摘要的统计量是恒等统计量。当我们将其绘制成点时，我们不对数据进行任何摘要统计。你可以通过打印图形对象来创建图形；你可以在图 13-1 中看到结果。

![使用 ggplot2 创建了一个散点图。带有轻微颜色深浅变化的点散布在整个图上。](img/476587_2_En_13_Fig1_HTML.jpg)

图 13-1 点几何图形

```r
print(p)
```

你可以直接将数据和美学映射添加到几何对象中。这在你想将替代数据叠加到图形上时尤其有用，但作为示例，考虑将相同的数据和美学映射移到 `geom_point()` 调用中。

```r
p <- ggplot() +
  geom_point(data = dat, aes(x = foo, y = bar, color = baz))
```

如果你查看摘要，你会看到美学映射现在已与几何对象分组在一起：

```
## mapping: x = ~foo, y = ~bar, colour = ~baz
## geom_point: na.rm = FALSE
## stat_identity: na.rm = FALSE
## position_identity
```

但除此之外，变化不大。这可用于向图形添加额外数据。这也表明，有时从一个空图形对象开始，然后在不同的几何对象中添加不同的数据集是有意义的。

由于 `baz` 是数值型的，它被解释为一个连续变量。你可以通过将其转换为因子来获得离散的颜色映射。

```r
ggplot(data = dat, aes(x = foo, y = bar, color = factor(baz))) +
  geom_point()
```

你可以在图 13-2 中看到结果。

![在 ggplot2 中添加了颜色变化美学参数的散点图。两个深浅不同的点，分别标记为因子 1 和因子 2，散布在图中。](img/476587_2_En_13_Fig2_HTML.jpg)

图 13-2 离散颜色美学

你可以更改因子中的水平来重新排序图例。有关操作因子的更多信息，请参阅第 10 章。

另一个简单的几何对象是线图。

```r
ggplot(data = arrange(dat, foo),
       aes(x = foo, y = bar, color = factor(baz))) +
  geom_line()
```

结果见图 13-3。在绘图之前，我使用 `arrange()` 根据 x 轴对数据进行了排序。否则，线条将不会从左到右，而是来回跳跃（如果你想理解我的意思，可以试一下）。

![ggplot2 中的线图几何对象。深浅不同的线条，标记为因子 1 和因子 2，起伏波动，形成紧密排列的尖锐峰值。](img/476587_2_En_13_Fig3_HTML.jpg)

图 13-3 线图

你可以有多个几何对象；请看下面的例子。

```r
p <- ggplot(data = dat,
            aes(x = foo, y = bar, color = baz)) +
  geom_point() +
  geom_smooth(formula = y ~ x, method = "loess")
```

如果你调用 `summary(p)`，你会在输出底部看到两个图层。

```
## geom_point: na.rm = FALSE
## stat_identity: na.rm = FALSE
## position_identity
## 
## geom_smooth: na.rm = FALSE, orientation = NA, se = TRUE
```

## stat_smooth 和 position_identity 的关系

注意，平滑几何对象没有使用恒等统计量。它显示了数据的摘要，并且从数据到该摘要的映射由 `stat_smooth` 统计量处理。

我们可以打印该图形：

```r
print(p)
```

查看图 13-4 中的结果。

![](img/476587_2_En_13_Fig4_HTML.jpg)

**图 13-4：包含两个几何对象的图**

一个`ggplot2`图，添加了两个几何对象。`bar`相对于`foo`的散点图中包含不同深浅的点，以及一条逐渐倾斜的线。

如果我们使用离散颜色，它也会对数据进行分组。因此，如果我们将`baz`再次设为因子，我们将得到不同颜色的数据点，并得到两条平滑线。

```r
ggplot(data = dat, aes(x = foo, y = bar, color = factor(baz))) +
  geom_point() +
  geom_smooth(formula = y ~ x, method = "loess")
```

查看图 13-5 中的结果。

![](img/476587_2_En_13_Fig5_HTML.jpg)

**图 13-5：包含两个几何对象和离散颜色的图**

一个`ggplot2`图，包含两个几何对象和一个颜色变化的美学参数。它具有带有阴影的散点和两条对应的线。

你可以使用`geom_histogram()`构建直方图（参见图 13-6）。

![](img/476587_2_En_13_Fig6_HTML.jpg)

**图 13-6：直方图**

使用`ggplot2`绘制的直方图。该直方图绘制了`count`相对于`foo`的分布，其中最高的柱形在 Y 轴上超过了 9。

```r
ggplot(data = dat, aes(x = foo)) +
  geom_histogram(binwidth = 0.05)
```

注意，此几何对象只需要一个 x 轴。通过`summary(p)`，你将看到其统计量是`stat_bin`。

```
## geom_bar: na.rm = FALSE
## stat_bin: binwidth = NULL, # ...此处有更多内容...
```

如果你想要密度图，可以使用`geom_density()`。

```r
ggplot(data = dat, aes(x = bar)) +
  geom_density()
```

你会看到其统计量是`stat_density`。

```
## geom_density: na.rm = FALSE
## stat_density: na.rm = FALSE
```

我想你已经看明白其中的模式了。

## 分面

添加网格分面的效果是，我们会得到子图，数据根据提供给`facet_grid()`的公式被分割成不同的组；参见图 13-7。

![](img/476587_2_En_13_Fig7_HTML.jpg)

**图 13-7：对图进行分面**

一个带有网格分面的散点图，包含 2 个子图。图 1 和图 2 包含散布在绘图区域中的单色点。

```r
ggplot(data = dat, aes(x = foo, y = bar)) +
  geom_point() + facet_grid(~ factor(baz))
```

在前面的图中，当你查看它们的摘要时，你会在`faceting`下看到类为`FacetNull`。

```
## faceting: 
```

当我们添加网格分面后，现在会看到：

```
## faceting: <ggproto object: Class FacetGrid, Facet, ...>
```

你可以通过在公式两侧都设置变量来绘制二维网格图：

```r
dat2 <- tibble(
  foo = rep(1:5, each = 20),
  bar = rep(1:2, each = 50),
  x = foo * bar + rnorm(100),
  y = -foo
)
ggplot(data = dat2, aes(x = x, y = y)) +
  geom_point() + facet_grid(factor(foo) ~ factor(bar))
```

结果如图 13-8 所示。

![](img/476587_2_En_13_Fig8_HTML.jpg)

**图 13-8：两个变量的分面网格**

一个带有网格分面的散点图，包含两侧的变量。十个图排列成 2 列，其中的点呈直线分布。

你可以使用两个以上的变量，但（自然）只能有两个维度。如果使用超过两个变量，不同的类别将作为标签显示在分面两侧。你将看到公式中出现所有因子组合。例如，考虑以下情况：

```r
dat3 <- tibble(
  foo = factor(rep(1:5, each = 20)),
  bar = factor(rep(1:2, each = 50)),
  baz = factor(rep(1:5, times = 20)),
  qux = factor(rep(1:2, times = 50)),
  x = rnorm(100),
  y = rnorm(100)
)
ggplot(data = dat3, aes(x = x, y = y)) +
  geom_point() + facet_grid(foo + bar ~ baz + qux)
```

结果如图 13-9 所示。

![](img/476587_2_En_13_Fig9_HTML.jpg)

**图 13-9：包含四个变量的分面图**

一个`ggplot2`图，其网格分面中包含 4 个变量。各图排列成 10 列 6 行，变量位于两侧。

## 添加坐标系

前面所有图都是在笛卡尔坐标系（默认坐标系）中绘制的。你可以更改图的坐标系。例如，可以翻转轴，比如图的 x 轴：

```r
ggplot(data = dat, aes(x = foo, y = bar)) +
  geom_point() + coord_flip()
```

或者，你也可以使用极坐标系代替笛卡尔坐标系。

```r
ggplot(data = dat, aes(x = foo, y = bar)) +
  geom_point() + coord_polar()
```

参见图 13-10 和图 13-11。

![](img/476587_2_En_13_Fig11_HTML.jpg)

**图 13-11：极坐标图**

使用`ggplot2`在极坐标中绘制点的雷达图。左侧、底部和右侧的径向距离分别测量为 0.75、0.50 和 0.25。

![](img/476587_2_En_13_Fig10_HTML.jpg)

**图 13-10：x 坐标翻转的图**

`foo`相对于`bar`的散点图，展示了在`ggplot2`中翻转 X 轴的功能。

## 修改刻度

`ggplot2`将数据映射到坐标或颜色的方式非常灵活。美学映射将数据映射到`x`或`y`坐标，或颜色和填充（用于填充区域的颜色），但之后你可以修改相应的图属性。用于执行此操作的函数以`scale_`开头，后面跟上要更改的属性，再然后是你要执行的操作。你修改的操作取决于你要更改的内容，例如坐标或颜色等。

例如，如果你想将`y`轴设置为对数刻度，可以使用`scale_y_log10()`；如果你想将连续变量映射到离散颜色，可以使用`scale_color_binned()`：

```r
ggplot(data = dat, aes(x = bar, y = foo, color = baz)) +
  geom_point() +
  scale_y_log10() +
  scale_color_binned()
```

参见图 13-12。

![](img/476587_2_En_13_Fig12_HTML.jpg)

**图 13-12：重新调整 y 坐标和颜色刻度的图**

`foo`相对于`bar`的散点图，展示了在`ggplot2`中修改轴范围和`baz`刻度颜色变化的功能。

所有这些转换函数的列表过于冗长，无法在此章节中列出，因此我建议你参阅包文档以获取更多信息。

受限于篇幅，我只能让你初步了解使用`ggplot2`实现的图形语法能做些什么，但我希望我已经传达出，通过这个包，你能够使用一种强大的语言来构建图形。其功能远不止我展示的这些，我强烈建议你自行更详细地探索这个包。
