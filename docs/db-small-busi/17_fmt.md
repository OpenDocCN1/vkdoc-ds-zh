# 十七、存档您的数据库

第十六章讲述了如何满足外部的信息请求，这通常需要存储大量的历史数据，随着企业的扩张，这些数据的数量会越来越大。此外，诸如 2002 年美国萨班斯-奥克斯利法案等法规要求上市公司、会计师、律师和其他人在审计后将业务记录存储五年，将财务数据存储七年。这进一步增加了存储历史数据的负担。

无论增加多少硬件或对应用程序进行多少微调，在计算机系统上存储越来越多的数据都会导致性能下降。因此，企业需要一种有效的方法来管理这种增长。

数据归档是指将非活动数据与正在使用的数据分开，并将非活动数据存储一段时间的过程。数据库归档侧重于归档存储在数据库中的数据。数据库存档的主要目标是保持对非活动数据的访问，以防以后需要满足特定的业务政策或政府法规，同时优化活动数据库的性能。如果不再需要存档的数据，可以将其销毁。正如在第八章中所讨论的，数据保护法通常规定数据的保存时间不应超过需要的时间。

数据归档将在您的数据库操作已经成为常规操作并且您的数据已经被清理之后进行。该程序可能会定期执行。当您开始使用数据库时，您的归档过程可能非常简单—也许您正在当前数据库中的活动表和非活动表之间移动数据，或者从当前数据库向专门为归档目的而设置的数据库移动数据。然而，随着你的业务增长，你可能需要寻找更复杂的方法。

## 数据库存档和数据库备份的区别

数据库备份旨在提供在出现重大问题时完全恢复当前数据库的全部或主要部分的能力。相比之下，数据库归档侧重于提供对少量数据（如表中的单行数据）的访问，以响应特定的数据请求。

备份是不断变化的活动数据库的辅助拷贝，因此往往是短期的，并定期被覆盖。这可能使它们不适合提供数据来响应业务政策和政府法规。此外，从备份中检索特定数据既耗时又昂贵，尤其是在存储介质（如磁带）不在现场的情况下。与典型的备份不同，归档必须能够经受时间的考验，鉴于技术不断变化的本质，这可能是一项挑战。

## 确定哪些数据应该归档

并非所有数据都同样重要。例如，如上所述，一些企业需要将其财务记录存档至少七年，以符合政府法规，但其他数据，如不再使用的供应商联系人，就不那么重要了。根据这些要求，企业可以创建数据保留策略，指定保留和归档各种类型数据的标准。这些归档策略必须考虑相关业务的数据访问模式。

来自第二章–4 中案例研究的以下示例说明了归档策略是如何演变的。

### 智能手推车公司

假设智能手推车公司对其客户的购买习惯和自己的材料购买模式进行了大量的数据分析。该分析用于制定如下数据保留策略：

*   人们发现，一年多没有买东西的顾客很少再从这家商店买东西。有鉴于此，制定了一项政策决定，将最后一张发票已过期一年以上的客户的所有联系信息和发票存档，并保存五年。

*   在为组件找到新的供应商后，企业很少重用该供应商。但是，如果任何组件有问题，可能需要联系以前的供应商。这意味着他们的联系方式和他们所提供产品的数据必须存档并无限期保存。

### 詹宁斯-哈佛法律事务所有限公司

`Jennings-Havard` 律师事务所案件的数据保留政策侧重于是否已立遗嘱，如下所示：

*   没有遗嘱的案件在结案后六个月归档，保存五年。

*   有关遗嘱的案件在结案后六个月归档，并保存至遗嘱主体死亡，如果死亡发生在五年内，则保存五年。

### 连接南侧

数据保留政策基于查询者和连接南方的工作人员的行为模式。

*   分析显示，一年后没有返回连接南区的询问者不太可能再次这样做。有鉴于此，所有已结案一年或更长时间的案件将被归档，直至赠款到期。

*   分析还表明，一年或更长时间没有被连接南侧使用的组织不太可能被再次使用。因此，符合这些标准的所有组织的详细信息将被存档，直到授权到期。当授权到期时，如果新的授权取代了它，则归档的组织被审计：具有不同焦点的授权可能意味着一些归档的组织变得相关。

## 实际问题

首先，您可能会以小规模的方式存档您的数据，例如，在闪存或硬盘驱动器上。但是，随着久而久之和您需要归档的数据量的增长，您将需要更多地考虑您所使用的存储介质。归档文件可能会存储很长时间，因此请选择一种介质类型，只要您的保留策略允许，它就可以保存很长时间。

同样，您的归档策略以及用于归档数据的存储机制无疑会随着时间的推移而改变。因此，明智的做法是每年至少查看一次您的档案，看看是否有任何内容需要迁移到不同的存储介质。

对于拥有大量数据的企业来说，磁带通常被认为是归档数据的好方法，因为它寿命长、成本低，尤其是与硬盘驱动器相比。对于其他企业，云归档是一个可行的解决方案。云在第十八章中有所涉及。

## 使用 MS Access 为 Everyone 公司的卡片存档表的例子

以下示例提供了存档数据的基本工具，这些工具在数据库的早期阶段特别有用。您可以创建表来存档现有数据库或单独数据库中的数据。该示例使用使用 `MS Access 2013` 的当前数据库中的表。

假设经理 Pat 想要将所有 2014 年 12 月之前的 Everyone Inc. 的信用卡发票存档到一个名为 `Archived Invoices` 的特殊存档表中。以下过程会将所需数据从 `Invoices` 移至已存档的发票：

```
Back up your database.
Design a query that will select the required rows and columns from the Invoices table, as shown in Figure 17-1. Here you are backing up all the columns of the Invoices table and all the rows with InvoiceDate earlier than December 2014. Notice the criteria of the InvoiceDate column, which indicates that only invoices before December 2014 are selected.
```

![A978-1-4842-0277-7_17_Fig1_HTML.jpg](img/A978-1-4842-0277-7_17_Fig1_HTML.jpg)

图 17-1。

`Query` to select all rows from the `Invoices` table of the `Cards for Everyone Inc.` database with an `InvoiceDate` earlier than December 2014

**Note** 图 17-1 中的查询有很大的灵活性。例如，您可以选择日期在 2014 年 11 月的所有行、2014 年的所有行等等。

如果您愿意，可以对 `InvoiceDate` 列使用过滤器。

简单的查询在本书的附录中有详细介绍。

```
Run the query (using !) and check that it returns the rows that you are expecting. Figure 17-2 shows the output after running the query in Figure 17-1.
```

![A978-1-4842-0277-7_17_Fig2_HTML.jpg](img/A978-1-4842-0277-7_17_Fig2_HTML.jpg)

图 17-2。

Output from running the query in Figure 17-1

If the output from the query is correct, you need to choose a destination table in which to archive the data. Here we are choosing a table called `Archived Invoices`, which has the same columns and data types as the `Invoices` table and resides in the same `Cards for Everyone Inc.` database.

Select the `Append (Add) Query` from the toolbar of the `Design tab` above the `Query Design View`. This will give you the options shown in Figure 17-3. You can see that you are given the choice of selecting destination tables either within your existing database or from another database.

![A978-1-4842-0277-7_17_Fig3_HTML.jpg](img/A978-1-4842-0277-7_17_Fig3_HTML.jpg)

图 17-3。

为Cards for Everyone Inc.公司的Invoices表设计追加查询。只需像运行其他任何查询一样运行此追加查询（使用!）。通过打开Archived Invoices表检查追加查询的结果。您应该发现图17-2中的结果已被追加到`Archived Invoices`表中。

如果所有预期的行都已追加到`Archived Invoices`表，您可以通过运行删除查询将它们从`Invoices`表中删除。此类查询的效果是选择指定表中的行并永久移除它们。删除查询的设计方式与普通查询相同，只是选择了删除查询选项，如图17-4所示。

![A978-1-4842-0277-7_17_Fig4_HTML.jpg](img/A978-1-4842-0277-7_17_Fig4_HTML.jpg)

图 17-4。

用于从`Invoices`表中删除`InvoiceDate`在2014年12月之前的发票的删除查询。

此示例未考虑表之间的任何关系，当您试图避免留下没有任何“父”行的记录时，这一点尤为重要。

例如，图17-5显示了`Invoices`表与另外两个表`Customers`和`Products2Invoices`之间的链接关系。

*   如果客户没有相应的发票，这并不构成问题，因为客户将来可能会回来订购更多产品。合理的做法是将他们的详细信息保存在`Customers`表中，以防发生这种情况。
*   `Invoices2Products`表中的条目显示了为每张发票购买了哪些产品：图17-6展示了一个填充后的`Invoices2Products`表示例。如果`Invoices2Products`表中的一条记录没有链接到任何发票，它就成了“孤立”记录。它不再起作用，也不会被再次使用，因为它引用了一个已删除的发票。如果删除了`Invoices`表中的行，`Invoices2Products`表中具有匹配`InvoiceNo`的行将会出现断链。

    ![A978-1-4842-0277-7_17_Fig6_HTML.jpg](img/A978-1-4842-0277-7_17_Fig6_HTML.jpg)

    图 17-6。

    Cards for Everyone Inc.数据库中填充后的`Invoices2Products`表。

![A978-1-4842-0277-7_17_Fig5_HTML.jpg](img/A978-1-4842-0277-7_17_Fig5_HTML.jpg)

图 17-5。

Cards for Everyone Inc.数据库中`Invoices`表与相邻表之间的关系。

解决“孤立”行问题的最简单方法是在从发票表中删除行时使用级联删除。这不仅会删除发票表中的行，还会删除其他表中与之链接且具有相同`InvoiceNo`的所有行。

要使用此工具，您需要编辑表之间的关系，以便应用级联删除功能。

**注意**

编辑关系的方法包含在本书的附录中。

图17-7展示了在创建或编辑`Invoices`和`Invoices2Products`之间的关系时使用的级联删除功能。

![A978-1-4842-0277-7_17_Fig7_HTML.jpg](img/A978-1-4842-0277-7_17_Fig7_HTML.jpg)

图 17-7。

作为两个表之间关系一部分的级联删除功能。

或者，您可能希望归档`Invoices2Products`表中与`Invoices`表中已归档行相对应的行。这可以通过使用一个连接查询（在第十章中介绍）来实现。

如果您选择了与已归档发票行相对应的`Invoices2Products`行（即，与2014年12月之前开具的发票相对应的行），则可以将这些行添加到归档的`Invoices2Products`表中。图17-8显示了`Invoices`和`Invoices2Products`之间的连接查询，该查询从`Invoices`表中选择`InvoiceNo`，并从`Invoices2Products`表中选择相应的`ProductID`和`QuantityPurchased`。

![A978-1-4842-0277-7_17_Fig8_HTML.jpg](img/A978-1-4842-0277-7_17_Fig8_HTML.jpg)

图 17-8。

Cards for Everyone Inc.数据库中`Invoices`和`Invoices2Products`表之间的连接查询。

图17-9显示了运行图17-8中查询的结果。然后可以使用前面描述的方法将这些数据添加到`Invoices2Products`的归档表中。

![A978-1-4842-0277-7_17_Fig9_HTML.jpg](img/A978-1-4842-0277-7_17_Fig9_HTML.jpg)

图 17-9。

运行图17-8中查询的输出结果。

## 摘要

数据和数据库归档是业务运营的重要组成部分。它建立在满足外部请求的讨论之上，并涉及存储非活动数据，以便在需要时可以有效地访问这些数据。大量未使用的数据可能会降低当前系统的速度，因此将它们分开很重要。