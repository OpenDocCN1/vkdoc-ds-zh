# 维韦克·米什拉《Apache Cassandra 开发入门》

![A978-1-4842-0142-8_BookFrontmatter_Figa_HTML.png](img/A978-1-4842-0142-8_BookFrontmatter_Figa_HTML.png)

**ISBN** 978-1-4842-0143-5  
**e-ISBN** 978-1-4842-0142-8  
**DOI** 10.1007/978-1-4842-0142-8  

© Apress 2014

《Apache Cassandra 开发入门》  
版权所有 © 2014，维韦克·米什拉

本作品受版权保护。出版商保留所有权利，无论涉及材料的全部或部分，特别是翻译、重印、重用插图、朗诵、广播、在缩微胶片或任何其他物理方式上的复制，以及传输或信息存储与检索、电子改编、计算机软件，或现在已知或此后开发的类似或不同方法的权利。法律保留的例外情况是：与评论或学术分析相关的简短摘录，或专门为输入计算机系统并执行而提供的材料，仅供作品购买者独家使用。复制本出版物或其部分内容，仅允许在出版商所在地现行版权法的规定下进行，并且必须始终从 Springer 获得使用许可。使用许可可通过版权清算中心的 RightsLink 获得。违规行为将根据相应的版权法予以起诉。

ISBN-13 (平装)：978-1-4842-0143-5  
ISBN-13 (电子版)：978-1-4842-0142-8

本书中可能出现商标名称、标识和图像。我们并非在每次出现商标名称、标识或图像时都使用商标符号，而仅在编辑风格下使用这些名称、标识和图像，以维护商标所有者的利益，无意侵犯商标。本出版物中使用的商品名称、商标、服务标记和类似术语，即使未被标识为此类，也不应被视为对它们是否受所有权保护的看法。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑和出版商均不对可能出现的任何错误或遗漏承担法律责任。出版商对书中所含内容不作任何明示或暗示的担保。

常务董事：Welmoed Spahr  
首席编辑：Jeff Olson  
技术审校：Brian O’Neill  
编辑委员会：Steve Anglin, Mark Beckner, Gary Cornell, Louise Corrigan, Jim DeWolf, Jonathan Gennick, Robert Hutchinson, Michelle Lowman, James Markham, Matthew Moodie, Jeff Olson, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Gwenan Spearing, Matt Wade, Steve Weiss  
协调编辑：Melissa Maldonado  
文字编辑：Lori Cavanaugh  
排版：SPi Global  
索引编制：SPi Global  
美术：SPi Global  
封面设计：Anna Ishchenko

全球图书贸易由 Springer Science+Business Media New York 发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 `www.springeronline.com`。Apress Media, LLC 是一家加利福尼亚有限责任公司，其唯一成员（所有者）是 Springer Science + Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是一家特拉华州公司。

如需翻译信息，请发送电子邮件至 `rights@apress.com`，或访问 `www.apress.com`。

Apress 及 friends of ED 的书籍可批量购买用于学术、企业或促销用途。大多数书籍也提供电子书版本和许可。更多信息，请参考我们的特别批量销售–电子书许可网页：`www.apress.com/bulk-sales`。

作者在本文中引用的任何源代码或其他补充材料，读者可在 `www.apress.com` 获取。有关如何定位书籍源代码的详细信息，请访问 `www.apress.com/source-code/`。

---

谨以此书献给我的妻子拉什米（Rashmi）和我的天使乌迪提（Uditi）。没有你们，这一切都不可能实现。

---

## 关于作者

![A978-1-4842-0142-8_BookFrontmatter_Fig1_HTML.jpg](img/A978-1-4842-0142-8_BookFrontmatter_Fig1_HTML.jpg)

维韦克·米什拉（Vivek Mishra）是一位技术爱好者、作者、演讲者和大数据架构师。他是开源项目 Kundera 的主要提交者，并为 Apache Cassandra 做出了贡献。

目前，他在 Impetus Infotech Pvt Ltd.担任首席软件工程师。他专注于利用机器学习算法实现实时数据质量和虚拟化，并将其应用于解决业务问题。

## 关于技术审校

![A978-1-4842-0142-8_BookFrontmatter_Fig2_HTML.jpg](img/A978-1-4842-0142-8_BookFrontmatter_Fig2_HTML.jpg)

布莱恩·奥尼尔（Brian O'Neill）是一位技术领袖，也是大数据领域的公认权威。他领导并贡献于涉及分布式存储、实时计算和分析的开源项目。他于 2013 年获得了 InfoWorld 的技术领导力奖，并在 2012 年至 2014 年连续被选为 Datastax Cassandra MVP。他撰写了关于 Cassandra 的 Dzone 参考卡片，并最近出版了一本关于分布式处理的书籍，书名为《Storm 蓝图：分布式实时计算模式》。他拥有人工智能和数据管理方面的专利，并毕业于布朗大学。

目前，布莱恩担任 Health Market Science (HMS)的首席技术官，负责领导一个专注于医疗领域分析和数据管理的大数据平台的开发。该平台由 Storm 和 Cassandra 驱动，能够针对数千个不同的数据源（包括医疗索赔和社交媒体）提供实时数据管理和分析。

## 引言

近年来，大数据一直是热门话题。随着解决非结构化和半结构化数据问题的可能性出现，越来越多的组织正逐渐转向由大数据驱动的解决方案。这从根本上为组织提供了一种“超越关系型数据库”的思考方式。本书将引导您在这一旅程中通过许多此类用例。

在过去 4-5 年中，已经开发了许多 NoSQL 数据库。最近的研究表明，现在有超过 150 种不同的 NoSQL 数据库。这引出了一个问题：为什么要采用特定的数据库？例如，它是否具有可扩展性？是否在积极开发中？最重要的是，是否被社区和组织所接受？正是基于这些问题，Apache Cassandra 脱颖而出，并表明了它为何是目前最流行的 NoSQL 数据库之一。

Apache Cassandra 是一个列式分布式数据库，它将数据库应用开发从遇到传统关系型数据库在性能和可扩展性方面局限性的点向前推进。限制传统关系型数据库的一些因素包括：它们需要预定义模式、扩展到数百个数据节点的能力，以及数据管理和监控方面的大量工作。我们将讨论这些限制以及如何使用 Apache Cassandra 来解决这些问题。

《Apache Cassandra 开发入门》将向您介绍 Apache Cassandra，包括上述问题的答案，并对其功能集提供详细概述和解释。

从 Cassandra 基础开始，本书将引导您了解以下主题及更多内容：

- 数据建模
- 集群部署、日志记录和监控
- 性能调优
- 通过 MapReduce 进行批处理
- Hive 和 Pig 集成
- 处理基于图形的解决方案
- 用于 Cassandra 的开源工具及相关实用程序

本书面向数据库管理员、大数据开发人员、学生、大数据解决方案架构师以及计划使用或正在使用 Apache Cassandra 的技术决策者。


这本书中涉及的许多特性和概念都通过动手实践的方式进行了讲解，展示了具体如何操作。除了这些分步指南外，书中示例的源代码也可以从本书在 Apress 的产品页面（[`www.apress.com/9781484201435`](http://www.apress.com/9781484201435)）下载。

## 致谢

首先，我要感谢我的妻子 *Rashmi* 和女儿 *Uditi*，感谢她们在我整个职业生涯中给予的支持，以及鼓励我撰写本书。特别感谢我的父母，感谢他们鼓励我选择了正确的职业道路，并教会我对工作全力以赴。我还要感谢所有的同事、前辈和导师们，感谢他们的支持和富有见地的讨论。

非常感谢 *Chris Nelson*、*Brian O'Neill*、*Melissa Maldonado* 以及 *Apress* 团队，感谢他们细致的技术审阅，以及作为作者所获得的精彩体验。没有这个团队，这一切将无法实现。



