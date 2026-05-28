# 实用 Hadoop 迁移指南

## 如何将关系数据库与 Hadoop 生态系统集成，并将关系型应用重构至 NoSQL

—

Bhushan Lakhe

前言作者：Milind Bhandarkar

**实用 Hadoop 迁移指南**

**如何将关系数据库与 Hadoop 生态系统集成，并将关系型应用重构至 NoSQL**

Bhushan Lakhe

*实用 Hadoop 迁移指南：如何将关系数据库与 Hadoop 生态系统集成，并将关系型应用重构至 NoSQL*

Bhushan Lakhe

美国伊利诺伊州达里恩

平装本 ISBN-13：978-1-4842-1288-2
电子版 ISBN-13：978-1-4842-1287-5
DOI 10.1007/978-1-4842-1287-5

美国国会图书馆控制号：2016948866

版权 © 2016 归 Bhushan Lakhe 所有

本作品受版权保护。出版商保留对本作品全部或部分内容的所有权利，特别是翻译权、重印权、插图复用权、引用权、广播权、微缩胶片或其他物理形式的复制权，以及以目前已知或未来开发的任何相似或不同方法进行的信息传输、存储检索、电子适配、计算机软件等权利。法律保留条款中豁免的是：与评论或学术分析相关的简短摘录，或专门为输入和执行于计算机系统而提供、仅供购买者独家使用的材料。仅允许在出版商所在地现行版权法的规定下复制本出版物或其部分内容，且使用许可必须始终从 Springer 处获取。使用许可可通过版权清算中心的 RightsLink 获取。违反行为将根据相应版权法被起诉。

本书中可能出现商标名称、标识和图像。我们未在每个商标名称、标识或图像出现处都加注商标符号，仅在编辑性语境中使用这些名称、标识和图像，以维护商标所有者的权益，且无意侵犯商标权。

本出版物中使用的商号、商标、服务标志及类似术语，即使未明确标识，也不应被视为关于其是否受所有权保护的立场表达。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑及出版商均不对可能出现的任何错误或疏漏承担法律责任。出版商对书中所含材料不作任何明示或暗示的保证。

执行董事：Welmoed Spahr
采编编辑：Robert Hutchinson
开发编辑：Matthew Moodie
技术审校：Robert L. Geiger
编辑委员会：Steve Anglin, Aaron Black, Pramila Balan, Laura Berendson, Louise Corrigan, Jonathan Gennick, Robert Hutchinson, Celestin Suresh John, Nikhil Karkal, James Markham, Susan McDermott, Matthew Moodie, Natalie Pao, Gwenan Spearing
协调编辑：Rita Fernando
文字编辑：Corbin Collins
排版：SPi Global
索引编制：SPi Global
封面设计：FreePik 设计

本书通过 Springer Science+Business Media New York 向全球图书贸易发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话 1-800-SPRINGER，传真 (201) 348-4505，电子邮件 orders-ny@springer-sbm.com，或访问 [www.springer.com](http://www.springer.com)。Apress Media, LLC 是加利福尼亚州有限责任公司，其唯一成员（所有者）是 Springer Science + Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是一家特拉华州公司。

有关翻译事宜，请发送电子邮件至 rights@apress.com，或访问 [www.apress.com](http://www.apress.com)。

Apress 与 friends of ED 的书籍可批量购买，用于学术、企业或促销用途。大多数图书也提供电子书版本和许可证。更多信息，请参考我们的特别批量销售—电子书许可网页：[www.apress.com/bulk-sales](http://www.apress.com/bulk-sales)。

作者在本文中引用的任何源代码或其他补充材料，读者可访问 [www.apress.com](http://www.apress.com) 获取。如需了解如何查找本书源代码的详细信息，请访问 [www.apress.com/source-code/](http://www.apress.com/source-code/)。

印刷于无酸纸

*谨以此书献给我的母亲……*