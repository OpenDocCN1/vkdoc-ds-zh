# 11. HDInsight

第 1 章将 Hadoop 描述为一个开源的分布式文件系统，能够处理海量数据。微软在其 Azure 云平台上对 Hadoop 的实现称为`HDInsight`，旨在处理云中的大量数据。`HDInsight`使用 Azure Blob 存储来存放数据，使 Hadoop 在云中作为一种服务提供。Azure 有助于以经济高效的方式在云中存储大型数据集。微软仅按实际使用的资源收费。

Apache Hive 支持对存储在 Hadoop 中的大型数据集进行分析。Hive 是构建在 Hadoop 之上的数据仓库基础架构，用于提供数据汇总、查询和分析。它最初由 Facebook 开发，现已成为一个 Apache 开源项目。

本章探讨将数据从 Azure `HDInsight`导出到 Excel 进行分析的最简单方法。

## 获取免费 Azure 账户

前面的章节介绍了如何使用 Azure Data Marketplace，此服务对于某些数据库基本上是免费的。本章的示例需要一个 Azure 账户。可以通过访问[`https://azure.microsoft.com/zh-cn/pricing/free-trial/`](https://azure.microsoft.com/zh-cn/pricing/free-trial/)获取 30 天试用账户。

随后会出现图 11-1 所示的界面。

![A978-1-4842-0529-7_11_Fig1_HTML.jpg](img/A978-1-4842-0529-7_11_Fig1_HTML.jpg)

图 11-1 截至 2015 年 7 月的 Azure 免费试用界面

点击`立即试用`，系统将提示您输入相关信息以设置一个月的免费试用。

## 将 Hadoop 文件导入 Power Query

本示例演示如何使用 Windows Azure Blob Storage 上的`HDInsight`对大型数据集运行 Hive 查询。然后，演示如何使用 Power Query 将数据导入 Excel，以便使用数据透视表和 Power Map 进行分析。

### 创建 Azure 存储账户

要完成本教程，您需要刚创建的 Windows Azure 账户。

登录 Azure 门户[`https://manage.windowsazure.com`](https://manage.windowsazure.com/)。会出现图 11-2 所示的界面。使用您的 Azure 凭据登录。

![A978-1-4842-0529-7_11_Fig2_HTML.jpg](img/A978-1-4842-0529-7_11_Fig2_HTML.jpg)

图 11-2 Microsoft Azure 门户

登录后，点击 Azure 门户屏幕左下角的`+新建`，如图 11-3 所示。突出显示新账户，然后点击屏幕底部的`管理访问密钥`。将出现`管理访问密钥`弹出窗口，如图 11-6 所示。请注意主访问密钥和辅助访问密钥。点击密钥右侧的图标可将其复制到剪贴板。点击屏幕右下角的复选标记关闭窗口。

![A978-1-4842-0529-7_11_Fig6_HTML.jpg](img/A978-1-4842-0529-7_11_Fig6_HTML.jpg)

图 11-6 管理访问密钥

点击右下角的`创建存储账户`并等待账户创建完成。随后会出现如图 11-5 所示的界面。

![A978-1-4842-0529-7_11_Fig5_HTML.jpg](img/A978-1-4842-0529-7_11_Fig5_HTML.jpg)

图 11-5 新建的存储账户

依次选择`数据服务` ➤ `存储`和`快速创建`，如图 11-4 所示。为 URL 输入一个唯一的名称。本示例将使用`testhive`。选择离您最近的位置。

![A978-1-4842-0529-7_11_Fig4_HTML.jpg](img/A978-1-4842-0529-7_11_Fig4_HTML.jpg)

图 11-4 在 Azure 中设置存储账户

![A978-1-4842-0529-7_11_Fig3_HTML.jpg](img/A978-1-4842-0529-7_11_Fig3_HTML.jpg)

图 11-3 Azure 门户界面



### 配置 Hadoop 集群

要配置 Hadoop 集群，请遵循以下步骤：

点击屏幕左下角的 `+NEW`。选择 `Data Services ➤ HDInsight ➤ Hadoop`，然后在屏幕右侧填写数值，如图 11-7 所示。选择一个集群名称。对于本例，两个节点应该就足够了。Azure 将默认用户名设置为 `Admin`。你需要创建一个至少包含十个字符的密码，其中至少包含一个大写字母、一个数字和一个特殊字符，并按照提示输入两次。请务必记住输入的密码。

![A978-1-4842-0529-7_11_Fig7_HTML.jpg](img/A978-1-4842-0529-7_11_Fig7_HTML.jpg)

图 11-7.

配置 HDInsight 集群

点击屏幕右下角的 `Create HDInsight Cluster`。然后，在集群创建完成后（这可能需要 20 分钟或更长时间），将会出现如图 11-8 所示的屏幕。

![A978-1-4842-0529-7_11_Fig8_HTML.jpg](img/A978-1-4842-0529-7_11_Fig8_HTML.jpg)

图 11-8.

显示新集群

从左侧窗格点击 `HDInsight` 以查看集群列表，如图 11-9 所示。应该会显示刚刚创建的集群。

![A978-1-4842-0529-7_11_Fig9_HTML.jpg](img/A978-1-4842-0529-7_11_Fig9_HTML.jpg)

图 11-9.

HDInsight 集群列表

选择刚刚创建的集群，然后点击屏幕底部的 `Query Console`。如果系统提示，请输入你的管理员用户名和密码。将出现如图 11-10 所示的屏幕。

![A978-1-4842-0529-7_11_Fig10_HTML.jpg](img/A978-1-4842-0529-7_11_Fig10_HTML.jpg)

图 11-10.

HDInsight 查询控制台

向下滚动以查看“带有示例数据的解决方案”。点击屏幕顶部的 `Hive Editor` 选项卡。在 Hive 编辑器中，输入查询名称，并接受默认 SQL 作为 `Select * from hivesampletable` 以选择所有记录，如图 11-11 所示。

![A978-1-4842-0529-7_11_Fig11_HTML.jpg](img/A978-1-4842-0529-7_11_Fig11_HTML.jpg)

图 11-11.

用于选择所有记录的 SQL 查询

点击 `Submit`。要查看查询结果，请点击屏幕左下角的查询名称，如图 11-12 所示。

![A978-1-4842-0529-7_11_Fig12_HTML.jpg](img/A978-1-4842-0529-7_11_Fig12_HTML.jpg)

图 11-12.

查询结果

在 HD Query Console 屏幕上，点击屏幕顶部的 `File Browser`。结果如图 11-13 所示。

![A978-1-4842-0529-7_11_Fig13_HTML.jpg](img/A978-1-4842-0529-7_11_Fig13_HTML.jpg)

图 11-13.

集群列表

点击左侧“名称”列中的第一项，然后点击你的集群名称，以获取如图 11-14 所示的结果。

![A978-1-4842-0529-7_11_Fig14_HTML.jpg](img/A978-1-4842-0529-7_11_Fig14_HTML.jpg)

图 11-14.

集群中的文件列表

### 导入 Excel

有多种方法可以将此数据导入 Excel。本节将演示如何将文件下载到具有定长字段的文本文件中。

请遵循以下步骤：

| 旧名称 | 新名称 |
| --- | --- |
| `Column2` | `Datetime` |
| `Column4` | `OS` |
| `Column5` | `Manufacturer` |
| `Coluimn6` | `Model` |
| `Column7` | `State` |
| `Column 8` | `Country` |
| `Column10` | `Accesses` |

通过点击屏幕顶部的 `Job History`，然后点击查询名称，返回到如图 11-12 所示的屏幕。然后点击 `Download File`。下载的作业日志如图 11-15 所示，报告下载了 59793 行。你看到的数字可能会有所不同。

![A978-1-4842-0529-7_11_Fig15_HTML.jpg](img/A978-1-4842-0529-7_11_Fig15_HTML.jpg)

图 11-15.

下载的作业日志

点击屏幕底部标有 `Download File` 的蓝色框。当提示选择用于打开文件的程序时，选择“记事本”。结果将出现在记事本中，如图 11-16 所示。这看起来是一个网站访问的日志文件。

![A978-1-4842-0529-7_11_Fig16_HTML.jpg](img/A978-1-4842-0529-7_11_Fig16_HTML.jpg)

图 11-16.

网站访问的日志文件

执行 `Save As` 将文件保存到桌面，以便于访问。打开 Excel，点击 `Power Query` 选项卡。选择 `from File`，然后选择 `From Text` 和 `Browse` 以找到下载的文件。该文件将加载到查询编辑器中，如图 11-17 所示。请注意，Power Query 解析了定长字段，将所有数据整齐地放入了单独的列中，但没有描述性的列标题。

![A978-1-4842-0529-7_11_Fig17_HTML.jpg](img/A978-1-4842-0529-7_11_Fig17_HTML.jpg)

图 11-17.

文件已加载到 Power Query 中

要重命名关键列，请右键单击列标题，选择 `Rename`，然后输入新的描述性名称，如下所示。

结果如图 11-18 所示。

要将数据加载到电子表格中，请点击功能区左端的 `Close & Load`，并选择 `Close & Load`。数据被加载到电子表格中，如图 11-19 所示。

![A978-1-4842-0529-7_11_Fig19_HTML.jpg](img/A978-1-4842-0529-7_11_Fig19_HTML.jpg)

图 11-19.

数据加载到电子表格中

![A978-1-4842-0529-7_11_Fig18_HTML.jpg](img/A978-1-4842-0529-7_11_Fig18_HTML.jpg)

图 11-18.

重命名列后的 Power Query 编辑器

### 创建数据透视表

要创建数据透视表以按州、制造商和型号分析访问量，请遵循以下步骤：

点击 `Insert` 和 `Pivot Table`。接受默认设置，将其放在新工作表中。将 `state` 拖到 `Columns` 框中，将 `manufacturer` 和 `model` 拖到 `Rows` 框中，将 `accesses` 拖到 `Values` 框中。结果显示按制造商和型号划分的访问量，如图 11-20 所示。

![A978-1-4842-0529-7_11_Fig20_HTML.jpg](img/A978-1-4842-0529-7_11_Fig20_HTML.jpg)

图 11-20.

显示按制造商和型号划分的访问量的数据透视表

### 在 Power Map 中创建地图

此示例演示如何使用 Power Map 映射数据。请遵循以下步骤：

将数据加载到电子表格中后，点击 `Insert` 选项卡，然后点击 `Map` 和 `Launch Power Map`。接受默认的国家和州作为默认地理字段，然后点击 `Next`，如图 11-21 所示。

![A978-1-4842-0529-7_11_Fig21_HTML.jpg](img/A978-1-4842-0529-7_11_Fig21_HTML.jpg)

图 11-21.

选择地理字段

点击 `Accesses` 作为高度，右键点击 `OS` 并选择 `Set as Category`，如图 11-22 所示，该图显示地图平移到了美国。

![A978-1-4842-0529-7_11_Fig22_HTML.jpg](img/A978-1-4842-0529-7_11_Fig22_HTML.jpg)

图 11-22.

显示美国地区按操作系统划分的访问量的地图

欧洲的类似显示如图 11-23 所示。

![A978-1-4842-0529-7_11_Fig23_HTML.jpg](img/A978-1-4842-0529-7_11_Fig23_HTML.jpg)

图 11-23.

显示欧洲地区按操作系统划分的访问量的地图

如果你已经用完了刚创建的 HDInsight 集群，请将其删除，这样你就不会为此付费。

## 摘要

本章仅初步探讨了在 Azure 中创建和访问 Hadoop 集群，以及将数据移至 Excel 和 Power Query，以便使用数据透视表和 Power Map 进行分析，从而提供按操作系统分类的数据图形化显示。



