# 基于 Spark 的大数据分析

面向大规模数据处理、机器学习、图分析及高速数据流处理的 Spark 实践指南

![A978-1-4842-0964-6_BookFrontmatter_Figa_HTML.png](img/A978-1-4842-0964-6_BookFrontmatter_Figa_HTML.png)

作者在本书中提及的任何源代码或其他补充材料，读者均可从 [`www.apress.com`](http://www.apress.com/) 获取。如需详细了解如何查找本书的源代码，请访问 [`www.apress.com/source-code/`](http://www.apress.com/source-code/)。

ISBN 978-1-4842-0965-3  
e-ISBN 978-1-4842-0964-6  
DOI 10.1007/978-1-4842-0964-6

© Apress 2015

基于 Spark 的大数据分析

管理总监：Welmoed Spahr  
首席编辑：Celestin John Suresh  
开发编辑：Chris Nelson  
技术审校：Sundar Rajan Raman 和 Heping Liu  
编辑委员会：Steve Anglin, Louise Corrigan, Jim DeWolf, Jonathan Gennick, Robert Hutchinson, Michelle Lowman, James Markham, Susan McDermott, Matthew Moodie, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Gwenan Spearing  
协调编辑：Jill Balzano  
文字编辑：Kim Burton-Weisman  
排版：SPi Global  
索引编制：SPi Global  
插图制作：SPi Global

如需了解翻译相关事宜，请发送邮件至 `rights@apress.com`，或访问 [`www.apress.com`](http://www.apress.com/)。

Apress 及 friends of ED 图书可通过批量购买用于学术、企业或推广用途。大多数图书也提供电子版及许可证。欲了解更多信息，请参考我们的批量销售与电子书许可网页：[`www.apress.com/bulk-sales`](http://www.apress.com/bulk-sales)。

本作品受版权保护。出版商保留所有权利，包括整体或部分内容的翻译、重印、插图重用、朗诵、广播、微缩胶片或其他物理形式的复制、传输或信息存储与检索、电子改编、计算机软件，以及目前已知或未来开发的任何类似或不同方法的权利。但以下情况除外：与书评或学术分析相关的简短摘录，或专门为计算机系统输入与执行而提供的材料，仅供购买者个人使用。未经出版商所在地区现行版权法许可，不得复制本出版物或其部分内容，且使用许可必须始终从施普林格（Springer）获取。许可可通过版权清算中心（Copyright Clearance Center）的 RightsLink 获得。违反相关版权法将依法追究责任。

本书中可能出现商标名称、标识及图像。未对每个商标名称、标识或图像加注商标符号，而仅以编辑方式使用这些名称、标识及图像，旨在维护商标所有者权益，无意侵犯商标权。本书中使用的商号、商标、服务标记及类似术语，即使未明确标识，也不应被视为对其是否受所有权保护的表达。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑及出版商对可能存在的任何错误或遗漏不承担法律责任。出版商对本书内容不作任何明示或暗示的保证。

本书通过施普林格科学与商业媒体纽约公司（Springer Science+Business Media New York）发行全球图书贸易，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，邮箱：`orders-ny@springer-sbm.com`，或访问 `www.springer.com`。

Apress Media, LLC 是一家加州有限责任公司，其唯一成员（所有者）为施普林格科学与商业媒体金融公司（SSBM Finance Inc）。SSBM Finance Inc 是一家特拉华州公司。

谨以此书献给我的母亲，她不仅带我来到这个世界，更以无条件的爱抚养我成长。

