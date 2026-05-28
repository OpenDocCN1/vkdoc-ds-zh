# Pro Salesforce Analytics Cloud：Wave 平台、生成器与探索器指南

William Smith 与 Helen Sun

---

本书作者在文中引用的任何源代码或其他补充材料，读者可在 [`www.apress.com`](http://www.apress.com/) 获取。关于如何查找本书源代码的详细信息，请访问 [`www.apress.com/source-code/`](http://www.apress.com/source-code/)。

ISBN 978-1-4842-1204-2  
电子版 ISBN 978-1-4842-1203-5  
DOI 10.1007/978-1-4842-1203-5  
© Apress 2016

**Pro Salesforce Analytics Cloud：Wave 平台、生成器与探索器指南**  
常务董事：Welmoed Spahr  
选题编辑：Susan McDermott  
技术审校：Mike Martin, Carl Brundage  
编辑委员会：Steve Anglin, Pramila Balen, Louise Corrigan, James DeWolf, Jonathan Gennick, Robert Hutchinson, Celestin Suresh John, Michelle Lowman, James Markham, Susan McDermott, Matthew Moodie, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Gwenan Spearing  
统筹编辑：Rita Fernando  
文字编辑：Carole Berglie  
排版：SPi Global  
索引编制：SPi Global  
封面设计：Friedhelm Steinen-Broo  
如需了解翻译事宜，请发送电子邮件至 `rights@apress.com`，或访问 [`www.apress.com`](http://www.apress.com/)。Apress 及 friends of ED 图书可批量采购用于学术、企业或促销用途。大多数图书也提供电子版及许可。更多信息请参阅我们的特殊批量销售及电子书许可网页：[`www.apress.com/bulk-sales`](http://www.apress.com/bulk-sales)。

本作品受版权保护。出版商保留所有权利，涉及整体或部分材料的翻译、重印、插图复用、摘录、广播、微缩胶片或其他物理形式的复制、传输或信息存储与检索、电子改编、计算机软件，以及目前已知或未来开发的类似或不同方法的专有权。与评论或学术分析相关的简短摘录，或专为在计算机系统上输入和执行、仅供购买者专用而提供的材料，不在本法律保留范围内。未经出版商所在地现行版权法规定及 Springer 许可，不得复制本出版物或其任何部分。使用许可必须通过版权清算中心的 RightsLink 获取。违反者将根据相应版权法被起诉。

书中可能出现的商标名称、标识和图片。我们不使用商标符号标注每个商标名称、标识或图片，仅以编辑方式使用并维护商标所有者利益，无意侵犯商标权。本书中使用的商品名称、商标、服务标志及类似术语，即使未经特别标识，也不应被视为对其是否受专有权利保护的立场表达。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑及出版商均不对可能存在的任何错误或遗漏承担法律责任。出版商对本书所含内容不作任何明示或暗示的保证。

本书通过全球图书贸易由 Springer Science+Business Media New York 发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 `www.springer.com`。Apress Media, LLC 是加利福尼亚州有限责任公司，其唯一成员（所有者）为 Springer Science + Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是特拉华州公司。

献给 Nathaniel G. Smith

## 引言

数据发现产品是一类分析和商业智能软件，随着组织努力用数据驱动决策，它主导了新投资并日益流行。其流行原因不难理解。通过数据发现软件，普通用户可以创建前所未有的强大应用。用户不再依赖 IT 部门满足报告和数据分析需求。数据发现用户成为“公民数据科学家”，执行基础数据分析并发现相关性，这以前只有通过高端统计分析包才能实现。

Salesforce Analytics Cloud 是 2014 年在 Salesforce 大会 Dreamforce 上推出的数据发现产品，其特性使其成为考虑投资这一激动人心技术领域的组织的理想选择。它 100%基于云，遵循 Salesforce“移动优先”战略，提供功能完善的移动平台用于创建和使用数据发现应用。Salesforce Analytics Cloud 的定价模式允许组织无需资本投入即可采用。Analytics Cloud 采用固定费用订阅制，不涉及数据传输成本。尽管 Salesforce Analytics Cloud 面向普通用户，但它也具备高级功能，使程序员能够开发复杂的应用。部分订阅包中包含全天候支持服务。

Salesforce Analytics Cloud 内置了使用 Salesforce 数据构建应用的能力。它还能利用源自 Salesforce 外部的数据创建应用。这种使用外部数据创建应用的能力正是本书的重点。

本书第一部分指导如何使用 Salesforce Analytics Cloud，先介绍基本概念，再探讨更高级的用法。

第二部分聚焦两个用例。第一个用例涉及一家制造业公司的数据，其数据来源广泛，从工厂车间到高管层。第二个用例章节展示了如何将 Salesforce Analytics Cloud 用于销售绩效数据，并完全在 Salesforce Analytics Cloud 移动界面上开发。最后一章涵盖高级数据采集与处理，介绍如何使用网络爬虫和抓取工具收集数据以供 Salesforce Analytics Cloud 使用。接着概述数据处理，并讨论 Python 编程语言及其在数据处理中的应用。

## 致谢

我们衷心感谢朋友兼同事 Paul Carlstoem 介绍我们认识 Apress 的专业人士。我们向 Apress 的 Susan McDermott 和 Rita Fernando 致以诚挚谢意，感谢他们为本书付出的辛勤努力。与 Apress 团队合作令人愉快，没有他们的帮助，本书将无法问世。我们感谢技术编辑 Mike Martin 和 Carl Brundage。我们还要感谢 Ciolek 律师事务所（执业律师）在处理我们作为作者所涉法律事务中提供的协助。最后但同样重要的是，我们感谢家人和朋友对我们写作工作的支持，特别是我们长久的朋友和导师 Mesbeh Ahmed 博士。