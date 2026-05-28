# 使用 Apache Giraph 进行实用图分析

作者：Claudio Martella, Roman Shaposhnik 与 Dionysios Logothetis

本文作者引用的任何源代码或其他补充材料，读者均可访问 [`www.apress.com`](http://www.apress.com/) 获取。有关如何查找本书源代码的详细信息，请访问 [`www.apress.com/source-code/`](http://www.apress.com/source-code/)。

ISBN 978-1-4842-1252-3 e-ISBN 978-1-4842-1251-6 DOI 10.1007/978-1-4842-1251-6

© Apress 2015

使用 Apache Giraph 进行实用图分析

常务董事：Welmoed Spahr

首席编辑：Jonathan Gennick

开发编辑：Douglas Pundick

技术审校：Steven Harenberg

编辑委员会：Steve Anglin, Mark Beckner, Gary Cornell, Louise Corrigan, Jim DeWolf, Jonathan Gennick, Robert Hutchinson, Michelle Lowman, James Markham, Susan McDermott, Matthew Moodie, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Gwenan Spearing, Matt Wade, Steve Weiss

协调编辑：Jill Balzano

文字编辑：Tiffany Taylor

排版：SPi Global

索引编制：SPi Global

插图制作：SPi Global

封面设计：Anna Ishchenko

有关翻译信息，请发送电子邮件至 `rights@apress.com`，或访问 [`www.apress.com`](http://www.apress.com)。Apress 及 friends of ED 书籍可批量购买用于学术、企业或促销用途。大多数图书也提供电子版及许可证。更多信息请参考我们的批量销售与电子书许可网页：[`www.apress.com/bulk-sales`](http://www.apress.com/bulk-sales)。

本作品受版权保护。出版商保留所有权利，无论涉及全部还是部分内容，具体包括翻译权、转载权、插图复用权、朗诵权、广播权、微缩胶片或其他物理形式复制权，以及信息存储与检索、电子改编、计算机软件传输，或使用目前已知或未来开发的类似或不同方法等权利。在法律保留的豁免范围内，允许摘录少量内容用于评论、学术分析，或专门为输入并运行于计算机系统而提供的材料，仅供购买者独家使用。未经出版商所在地现行版权法许可，禁止复制本出版物或其任何部分，且使用许可必须始终从 Springer 获取。许可可通过 Copyright Clearance Center 的 RightsLink 获得。违反行为将根据相关版权法予以起诉。

本书中可能出现商标名称、标识及图像。我们不会在每次出现商标名称、标识或图像时都使用商标符号，而是仅以编辑方式使用这些名称、标识和图像，以维护商标所有者的权益，无意侵犯商标。本出版物中使用的商品名、商标、服务标志及类似术语，即使未被明确标识，也不应被视为对是否受专有权利保护的表达意见。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑及出版商对可能存在的任何错误或遗漏不承担任何法律责任。出版商对本书所载内容不作任何明示或暗示的担保。

本书通过 Springer Science+Business Media New York 在全球图书贸易中发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 `www.springeronline.com`。Apress Media, LLC 是一家加利福尼亚有限责任公司，其唯一成员（所有者）为 Springer Science + Business Media Finance Inc（SSBM Finance Inc）。SSBM Finance Inc 是一家特拉华州公司。

## 引言

我们生活在一个所谓的大数据时代。我们听到数据科学家这样的术语，并且有很多关于分析、挖掘海量企业数据以获取具有商业价值信息碎片的讨论。甚至还有一些关于尿布和啤酒在同一商店过道一起出售的传说故事。共同的主题是：拥有大量数据，并设法将这些数据转化为可操作的信息。

接下来介绍图论。它是数学的一个分支，研究对象之间的成对关系。图论可以抽象地讲授，而且很可能经常如此。但它也非常实用。想象一下，绘制一个网站中所有链接关系的地图。某个页面可能与其他所有页面相比拥有更多的关系，或许该页面就是一个重要的页面。同样，我们可以研究一个群体中人与人之间的关系，也许拥有最多联系的人也可以被视为具有最广泛的影响力。当然，如果你的目标是快速传播一则新闻或八卦，你肯定需要那个联系广泛的人。

你手中的这本书是关于 Apache Giraph 及其在创建用于分析大型数据集的图结构中的应用。Apache Giraph 是一个图处理引擎，旨在实现可扩展性，并基于人与物之间的连接快速回答商业和科学问题。例如，它在 Facebook 中被用作挖掘和销售从你日常大量随机帖子中衍生出的信息的基础。

如果你有大量需要分析的数据，尤其是当从数据点之间的关系中能够衍生出信息时，那么这本书《使用 Apache Giraph 进行实用图分析》可以释放巨大的价值。今天就购买这本书。阅读它。并收获隐藏在你数据中的商业和科学价值。

## 注释约定

本书中的许多代码示例使用了诸如 `#1`、`#2`、`#3` 等形式的标记进行注释。每当你在代码清单中看到此类标记时，请知悉它并非代码本身的组成部分。它是对代码清单下方注释的引用。

例如，以下是第 5 章中代码清单 5-4 的几行代码：

```
public class GiraphHelloWorld extends                                    #2
BasicComputation<IntWritable, IntWritable,
NullWritable, NullWritable> {
@Override
public void compute(Vertex<IntWritable,                                #3
IntWritable, NullWritable> vertex,
Iterable<NullWritable> messages) {
```

查看该清单，你会看到引用 `#2` 和 `#3` 在正文的代码清单之后被解析。如下所示：

```
...
System.exit(ToolRunner.run(new GiraphRunner(), args));
}
}
```

`#1` 必需的导入语句。在后续所有代码清单中，我们将省略这些语句。

`#2` 扩展最基础的抽象超类以定义我们的图计算。

`#3` 定义 `compute` 方法，该方法将对每个顶点被调用。

你会在整本书中看到上述注释约定。代码中形如 `#1`、`#2`、`#3` 的注释，对应代码清单之后给出的说明。