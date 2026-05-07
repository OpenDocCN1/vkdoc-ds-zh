
![](img/A978-1-4842-0865-6_CoverFigure_HTML.jpg)

![A978-1-4842-0865-6_CoverFigure_HTML.jpg](img/A978-1-4842-0865-6_CoverFigure_HTML.jpg) 法比奥·内利 著《使用 D3 创建 Web 图表》

![A978-1-4842-0865-6_BookFrontmatter_Figa_HTML.png](img/A978-1-4842-0865-6_BookFrontmatter_Figa_HTML.png)

ISBN 978-1-4842-0866-3  
电子书 ISBN 978-1-4842-0865-6  
DOI 10.1007/978-1-4842-0865-6

© 2014 年 Apress 出版社

**《使用 D3 创建 Web 图表》**

常务董事：韦尔莫德·斯帕尔  
首席编辑：本·雷诺-克拉克  
开发编辑：詹姆斯·马卡姆 和 克里斯·尼尔森  
技术审校：马修·坎宁  
编辑委员会：史蒂夫·安格林、马克·贝克纳、尤安·白金汉、加里·康奈尔、路易丝·科里根、吉姆·德沃尔夫、乔纳森·格尼克、罗伯特·哈钦森、米歇尔·洛曼、詹姆斯·马卡姆、马修·穆迪、杰夫·奥尔森、杰弗里·佩珀、道格拉斯·庞迪克、本·雷诺-克拉克、多米尼克·谢克沙夫特、格温南·斯皮林、马特·韦德、史蒂夫·韦斯  
协调编辑：吉尔·巴尔扎诺 和 克里斯汀·里基茨  
文字编辑：丽莎·韦基奥内、凯齐亚·恩兹利 和 布伦丹·弗罗斯特  
排版：SPi Global  
索引编制：SPi Global  
插图绘制：SPi Global  
封面设计：安娜·伊什琴科

本书通过 Springer Science+Business Media New York（地址：233 Spring Street, 6th Floor, New York, NY 10013）在全球图书贸易中发行。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 [`www.springeronline.com`](http://www.springeronline.com/)。Apress Media, LLC 是一家加利福尼亚有限责任公司，其唯一成员（所有者）是 Springer Science + Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是一家特拉华州公司。

有关翻译信息，请发送电子邮件至 `rights@apress.com`，或访问 [`www.apress.com`](http://www.apress.com/)。Apress 及 friends of ED 的书籍可批量购买用于学术、企业或促销用途。大多数图书也提供电子书版本和许可证。如需更多信息，请参考我们的特殊批量销售–电子书许可网页：[`www.apress.com/bulk-sales`](http://www.apress.com/bulk-sales)。

作者在本文中引用的任何源代码或其他补充材料，读者可在 [`www.apress.com`](http://www.apress.com/) 获取。有关如何找到本书源代码的详细信息，请访问 [`www.apress.com/source-code/`](http://www.apress.com/source-code/)。

本作品受版权保护。出版商保留所有权利，无论是整体还是部分材料，特别是翻译、重印、重用插图、朗诵、广播、以缩微胶卷或任何其他物理方式复制，以及电子改编、计算机软件或通过目前已知或未来开发的类似或不同方法进行传输或信息存储与检索的权利。与评论或学术分析相关的简短摘录，或专门为输入计算机系统并执行而提供的材料（仅供购买者独家使用）不受此法律限制。未经出版商所在地现行版权法许可，不得复制本出版物或其部分内容，且必须始终从 Springer 获取使用许可。可通过 Copyright Clearance Center 的 RightsLink 获取使用许可。违反者将根据相应版权法受到起诉。

本书中可能出现商标名称、标识和图像。我们不以每次出现商标名称、标识或图像时都使用商标符号，而是仅以编辑方式使用这些名称、标识和图像，以维护商标所有者的利益，无意侵犯商标权。

本出版物中使用的商品名称、商标、服务标志及类似术语，即使未明确标识，也不应被视为对其是否受专有权利保护的表达意见。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑和出版商均不对可能存在的任何错误或遗漏承担法律责任。出版商对本书所含内容不作任何明示或暗示的保证。

本出版物中使用通用描述性名称、注册名称、商标、服务标志等，即使没有具体声明，也不意味着这些名称不受相关保护性法律和法规的约束，因此可自由使用。

出版商、作者和编辑可以假定本书中的建议和信息在出版时是真实准确的。出版商、作者和编辑均不对本书所含内容或可能存在的任何错误或遗漏提供明示或暗示的保证。

本书采用无酸纸印刷。

Springer 是 Springer Science+Business Media (www.springer.com) 的一部分

谨以此书献给我的祖父波罗和祖母弗兰卡，感谢他们在我生命中给予的所有支持

**关于作者**



![A978-1-4842-0865-6_BookFrontmatter_Fig1_HTML.jpg](img/A978-1-4842-0865-6_BookFrontmatter_Fig1_HTML.jpg) 法比奥·内利（Fabio Nelli）是位于意大利波梅齐亚的私人研究中心 IRBM 科学园的信息技术科学应用专家。他曾长期在 IBM、EDS、默克雪兰诺以及多家银行和保险公司担任计算机顾问，还曾在贝克曼库尔特公司担任信息技术与自动化系统专家。

他拥有罗马大学有机化学硕士学位，近期又获得了诺韦德拉泰 eCampus 大学自动化与计算机工程学士学位。

内利目前正在开发用于连接`Oracle`数据库的`Java`应用程序、利用科学仪器生成数据的程序，以及为研究人员提供实时分析的网络服务器应用程序。

网站：[`www.meccanismocomplesso.org`](http://www.meccanismocomplesso.org/)

