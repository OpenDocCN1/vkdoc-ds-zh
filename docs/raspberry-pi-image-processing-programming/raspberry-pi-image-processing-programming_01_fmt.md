# Raspberry Pi 图像处理编程

## 结合 NumPy、SciPy、Matplotlib 与 OpenCV

### 第二版

Ashwin Pajankar

***

Raspberry Pi 图像处理编程

Ashwin Pajankar  

印度马哈拉施特拉邦，纳西克

ISBN-13（平装）：978-1-4842-8269-4  

ISBN-13（电子版）：978-1-4842-8270-0  

<https://doi.org/10.1007/978-1-4842-8270-0>

版权所有 © 2022 Ashwin Pajankar

本作品受版权保护。出版商保留所有权利，无论是涉及材料全部或部分的内容，特别是翻译、重印、重用插图、朗诵、广播、以微缩胶片或任何其他物理形式复制、信息存储与检索的传输、电子改编、计算机软件，或目前已知或以后开发的任何类似或不同方法的权利。

本书中可能出现商标名称、标识和图像。我们并非在每次出现商标名称、标识或图像时都使用商标符号，而是仅在编辑性使用、并维护商标所有者利益的前提下使用这些名称、标识和图像，无意侵犯商标权。

本书中使用的商品名称、商标、服务标记及类似术语，即使未明确标注为商标，也不应被视为对它们是否受专有权利保护的任何意见表达。

尽管本书中的建议和信息在出版时被认为是真实和准确的，但作者、编辑及出版商均不对可能出现的任何错误或遗漏承担法律责任。出版商对本书所含内容不做任何明示或暗示的担保。

**Apress Media LLC 董事总经理：** Welmoed Spahr  
**采编编辑：** Celestin Suresh John  
**开发编辑：** James Markham  
**协调编辑：** Aditee Mirashi  
**文字编辑：** April Rondeau  
**封面设计：** eStudioCalamar  
**封面图片设计：** Freepik (www.freepik.com)

本书通过 Springer Science+Business Media New York 向全球图书贸易发行，地址：1 New York Plaza, Suite 4600, New York, NY 10004-1562, USA。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 www.springeronline.com。Apress Media, LLC 是加利福尼亚州的一家有限责任公司，其唯一成员（所有者）是 Springer Science+Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是一家特拉华州公司。

如需翻译授权，请发送电子邮件至：`booktranslations@springernature.com`；如需重印、平装版或音频版权，请发送电子邮件至：`bookpermissions@springernature.com`。

Apress 图书可批量购买，用于学术、公司或促销用途。大多数图书也提供电子版和许可证。更多信息请参阅我们的印刷版和电子版批量销售网页：<http://www.apress.com/bulk-sales>。

作者在本书中引用的任何源代码或其他补充材料，读者可在 GitHub（`github.com/apress`）上获取。如需更详细信息，请访问 <http://www.apress.com/source-code>。

印刷于无酸纸上

---

*谨以此书纪念*  
*Govindarajulu Regeti 教授*  
*（1945 年 7 月 9 日至 2021 年 3 月 18 日）*

*大家亲切地称其为 RGR。Govindarajulu 教授在 JNTU 卡基纳达分校获得了电气与电子工程学士学位，并在 IIT 坎普尔分校获得了硕士和博士学位。他是 IIIT 海得拉巴分校的早期教员之一，并为使其成为如今的一流学府发挥了重要作用。他是该学院最受欢迎和喝彩的教员。他充满教学热情，并兼具老派魅力。毫无疑问，他关心每一位学生个体，悉心了解并指导他们。*