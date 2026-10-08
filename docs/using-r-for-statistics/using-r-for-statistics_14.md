## 数据集

本附录提供了本书中使用的每个示例数据集的详细信息和相关来源。这些数据集可作为 R 工作区文件或单独的 csv 文件随本书下载获取（`www.apress.com/9781484201404`）。

### `apartments`

| 描述 | 2012 年 10 月在英国赫特福德郡毕晓普斯托福德五英里半径范围内广告出租的 32 套一居室公寓的详细信息。 |
| --- | --- |
| 变量 | `Town` | 公寓地点 |
|  | `Furnished` | 提供家具（是或否） |
|  | `Price.Cat` | 租金价格类别（每历月） |
| 来源 | `www.rightmove.co.uk` |
| 使用章节 | 第六章 |

### `bigcats`

| 描述 | 四种大型猫科动物的平均体重 |
| --- | --- |
| 变量 | `Name` | 物种名称。注意，第一个 Leopard 实例指的是 Pantera Pardus，第二个实例指的是 Unica unica（雪豹） |
|  | `Weight` | 该物种雄性的平均体重（千克） |
| 来源 | 网址：`dialspace.dial.pipex.com/agarman/facts1.htm` |
| 使用章节 | 第四章 |

### `bottles`

| 描述 | 从生产线上随机选择的 30 瓶软饮料中液体体积的数据集。此为虚构数据 |
| --- | --- |
| 变量 | `Volume` | 体积（毫升） |
| 使用章节 | 第五章、第十章 |

### `brains`

# 数据集描述

## `CIAdata1`，`CIAdata2`

| 描述 | 七个欧洲国家的人口统计数据 |
| --- | --- |
| 变量 | `country` 国家名称 |
| | `lifeExp` 预期寿命（年） |
| | `urban` 居住在城市地区的人口比例（%） |
| | `pcGDP` 人均国内生产总值（美元） |
| 来源 | 收集自 2012 年 8 月 5 日的美国中情局世界概况 网址：`https://www.cia.gov/library/publications/the-world-factbook/` |
| 使用章节 | 第四章、第九章 |

## `coffeeshop`

| 描述 | 某咖啡店五天内的总销售额。此为虚构数据 |
| --- | --- |
| 变量 | `Date` 日期，格式为 dd/mmm/yyyy |
| | `Sales` 当日销售额（英镑） |
| 使用章节 | 第三章 |

## `concrete`

| 描述 | 确定最佳混凝土配比的实验结果 |
| --- | --- |
| 变量 | `Cement` 水泥类型（I 或 II） |
| | `Additive` 添加剂（A 或 B） |
| | `Additive.Dose` 添加剂剂量（0.3%、0.4%或 0.5%） |
| | `Density` 密度（克/立方厘米） |
| 来源 | 该实验于 2007 年在智利圣地亚哥进行 |
| 使用章节 | 第十一章 |

## `CPIdata`

| 描述 | 六个国家的消费者价格指数数据（2012 年） |
| --- | --- |
| 变量 | `country` 国家名称 |
| | `CPI` 消费者价格指数（以纽约为 100） |
| 来源 | 网址：`www.numbeo.com/cost-of-living/rankings_by_country.jsp` |
| 使用章节 | 第四章 |

## `customers`

| 描述 | 居住在英国伯克郡雷丁地区的五名客户的姓名和地址。此为虚构数据 |
| --- | --- |
| 变量 | `Name` 表示客户全名的字符串 |
| | `Address` 表示客户完整地址的字符串 |
| 使用章节 | 第三章 |

## `endangered`

| 描述 | 四种大型猫科动物的保护状况 |
| --- | --- |
| 变量 | `Name` 物种名称 |
| | `Status` 保护状况 |
| 来源 | 网址：`www.bigcats.com/redlist.php` |
| 使用章节 | 第四章 |

## `fiveyearreport`

| 描述 | Tesco、Sainsburys 和 Morrisons 超市连锁店 2007 年至 2011 年英国销售额（含增值税） |
| --- | --- |
| 变量 | `Year` 年份（2007–2011） |
| | `Tesco` Tesco 的英国销售额（含增值税，单位：百万英镑） |
| | `Sainsburys` Sainsburys 的英国销售额（含增值税，单位：百万英镑） |
| | `Morrisons` Morrisons 的英国销售额（含增值税，单位：百万英镑） |
| 来源 | 数据收集自各公司 2007–2011 年的年度报告 网址：`www.tescoplc.com/files/pdf/reports/tesco_annual_report_2011.pdf`，`www.tescoplc.com/files/pdf/reports/annual_report_2010.pdf`（第 16 页），`www.tescoplc.com/files/pdf/reports/annual_report_2009.pdf`（第 34 页），`www.tescoplc.com/files/pdf/reports/annual_report_2008.pdf`（第 5 页），`www.tescoplc.com/files/pdf/reports/annual_report_2007.pdf`（第 3 页），`www.j-sainsbury.co.uk/investor-centre/financial-performance/5-year-summary/`，`www.morrisons.co.uk/corporate/2011/annualreport/investor-information/five-year-summary-results/` |
| 使用章节 | 第九章 |

## `flights`

| 描述 | 2012 年 1 月 12 日至 13 日期间从南安普敦机场出发的七个航班的航班数据 |
| --- | --- |
| 变量 | `Date` 日期，格式为 dd/mm/yyyy |
| | `Time` 时间，格式为 hh:mm |
| | `Flight.Number` 包含字母数字的航班号 |
| | `Destination` 目的地城市名称 |
| 来源 | 网址：`www.southamptonairport.com` |
| 使用章节 | 第三章 |

## `fruit`

| 描述 | 2012 年 8 月英国水果价格数据集 |
| --- | --- |
| 变量 | `Product` 产品名称 |
| | `Price` 售价（英镑） |
| | `Unit` 销售单位 |
| 来源 | 网址：`www.sainsburys.co.uk` |
| 使用章节 | 第三章 |

## `grades1`

| 描述 | 关于 15 名分别属于 A、B、C 三个班级的学生成绩的虚构数据集。 |
| --- | --- |
| 变量 | `ClassA` A 班学生的成绩（百分比） |
| | `ClassB` B 班学生的成绩（百分比） |
| | `ClassC` C 班学生的成绩（百分比） |
| 使用章节 | 第四章、第十章 |

## `IQBrainSize`

| 描述 | 10 对同卵双胞胎的脑容量，使用磁共振成像和基于计算机的图像分析技术测量 |
| --- | --- |
| 变量 | `Pair` 配对标识符 |
| | `Twin1` 双胞胎中先出生者的总脑容量（立方厘米） |
| | `Twin2` 双胞胎中后出生者的总脑容量（立方厘米） |
| 来源 | 此数据取自文章“Brain Size, Head Size, and IQ in Monozygotic Twins”，作者为 Tramo, M. J. 等，发表于《Neurology》1998; 50:1246-1252。经许可转载。网址：`lib.stat.cmu.edu/datasets/IQ_Brain_Size` |
| 使用章节 | 第十章 |

### 人员

| 描述 | 样本为 16 人的身体特征数据。采用非随机方法选取样本。数据为自我报告。 |
| --- | --- |
| 变量 | `Subject` | 受访者编号 |
| | `Eye.Color` | 眼睛颜色（蓝色、绿色或棕色） |
| | `Height` | 身高（厘米） |
| | `Hand.Span` | 左手手展（毫米） |
| | `Sex` | 性别（1=男性, 2=女性） |
| | `Handedness` | 用手习惯（L=左撇子, R=右撇子） |
| 用于 | 第 3 章 |

### 人员 2

| 描述 | 该数据集是`people`数据集的清理版本。 |
| --- | --- |
| 变量 | `Subject` | 受访者编号 |
| | `Eye.Color` | 眼睛颜色（蓝色、绿色或棕色） |
| | `Height` | 身高（厘米） |
| | `Hand.Span` | 左手手展（毫米） |
| | `Sex` | 性别（男性或女性） |
| | `Handedness` | 用手习惯（左手或右手） |
| | `Height.Cat` | 身高类别（高、中等或矮） |
| 用于 | 第 6、8、11 章 |

### 发电站

| 描述 | 燃气轮机的 30 组压力、温度和输出测量值 |
| --- | --- |
| 变量 | `Pressure` | 涡轮机内部压力（毫巴） |
| | `Temp` | 涡轮机内部温度（摄氏度） |
| | `Output` | 涡轮机输出功率（兆瓦） |
| 来源 | 2010 年从英国某发电站的燃气轮机收集 |
| 用于 | 第 11 章 |

### 脉搏率

| 描述 | 四人的脉搏数据。采用非随机方法选取样本。数据为自我报告。 |
| --- | --- |
| 变量 | `Patient` | 患者标识符 |
| | `Pulse1` | 第一次脉搏读数（次/分钟） |
| | `Pulse2` | 第二次脉搏读数（次/分钟） |
| | `Pulse3` | 第三次脉搏读数（次/分钟） |
| 用于 | 第 3 章 |

### 抗压强度

| 描述 | 提供一个简单实验的结果，用于比较四种混凝土配方在凝固后三天、七天和十四天的立方体抗压强度 |
| --- | --- |
| 变量 | `Formula` | A（韦丘拉巴骨料 + 添加剂 A） B（韦丘拉巴骨料 + 添加剂 B） C（毛罗骨料 + 添加剂 A） D（毛罗骨料 + 添加剂 B） |
| | `Day3` | 立方体抗压强度（千克/平方米），凝固后三天测量 |
| | `Day7` | 立方体抗压强度（千克/平方米），凝固后七天测量 |
| | `Day14` | 立方体抗压强度（千克/平方米），凝固后十四天测量 |
| 来源 | 2007 年在智利圣地亚哥进行的实验 |
| 用于 | 第 4 章 |

### 超市

| 描述 | 英国四大连锁超市的数据 |
| --- | --- |
| 变量 | `Chain` | 连锁品牌名称 |
| | `Stores` | 英国境内门店数量 |
| | `Sales.Area` | 销售面积（千平方英尺） |
| | `Market.Share` | 市场份额（%） |
| 来源 | 门店数量和总销售面积数据来自 2011 年各公司年报： [`www.tescoplc.com/media/417/tesco_annual_report_2011_final.pdf`](http://www.tescoplc.com/media/417/tesco_annual_report_2011_final.pdf) [`www.j-sainsbury.co.uk/investor-centre/reports/2011/annual-report-and-financial-statements-2011/`](http://www.j-sainsbury.co.uk/investor-centre/reports/2011/annual-report-and-financial-statements-2011/) [`www.morrisons.co.uk/Documents/Morrisons-Annual-Report-2011.pdf`](http://www.morrisons.co.uk/Documents/Morrisons-Annual-Report-2011.pdf) 市场份额数据来自凯度消费者指数： [`www.kamcity.com/namnews/mktshare/2011/kantar-march11.htm`](http://www.kamcity.com/namnews/mktshare/2011/kantar-march11.htm) |
| 用于 | 第 2 章 |

### 生命体征

| 描述 | 四名患者的收缩压、舒张压和脉搏率测量值。此为虚构数据。 |
| --- | --- |
| 变量 | `subject` | 患者标识符 |
| | `test` | 参数名称（SysBP, DiaBP, Pulse） |
| | `result` | 收缩压（mmHg）、舒张压（mmHg）或脉搏（次/分钟） |
| 用于 | 第 4 章 |

### 世界卫生组织数据

| 描述 | 五个国家的酒精消费量及死亡率数据 |
| --- | --- |
| 变量 | `alcohol` | 15 岁以上成年人酒精消费量（每人每年纯酒精升数） |
| | `mortality` | 成人死亡率（15 岁至 60 岁之间死亡的概率，每 1000 人） |
| 来源 | 从世界卫生组织网站收集：`apps.who.int/ghodata` |
| 用于 | 第 4 章 |
