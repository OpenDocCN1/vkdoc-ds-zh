# 领域未来

数据以及旨在利用数据的算法，正成为消费者导向和商业导向公司的一项基础性、具有显著竞争优势的资产。

文件共享和协作解决方案`Box`和`Dropbox`很可能被视为大数据分析应用，考虑到它们存储的海量文件。越来越多垂直聚焦的大数据分析应用正在涌现。正如后续章节中提到的，像`Opower`这样的公司，从电表获取数据，帮助消费者和企业了解其电力消耗，并更有效地利用能源。

最近被`Google`以 32 亿美元收购的`Nest`，是一款学习型恒温器，它能理解消费者行为，并将算法应用于其收集的数据，从而更好地调节家庭冷暖。这些大数据分析公司中，有些将被收购，而另一些则将效仿`QlikTech`、`Tableau`和`Splunk`，在寻求建立领先、独立的大数据分析公司的过程中上市。显而易见的是，市场对大数据的胃口才刚刚开始。

![Image](img/sq.jpg) **注意** 市场对大数据的胃口才刚刚开始。

随着越来越多的大数据分析应用进入市场，基础设施提供商的定位如何？在基于云的基础设施方面，`Amazon`很可能在几乎每个领域都提供极具竞争力的产品。对于那些它不涉及的领域，更大的开源基础设施供应商可能会介入，提供基于云的产品。如果历史具有预测性，大型企业玩家，从`Cisco`到`EMC`、`IBM`和`Oracle`，很可能会继续成为这些供应商极其活跃的收购方。`Intel`对`Cloudera`的 9 亿美元投资表明，它对大数据极为重视。

展望未来，预计会有更多的大数据分析应用涌现，以提升我们的工作和个人生活。其中一些应用程序将帮助我们更好地理解信息。但许多应用程序不会止步于此。大数据分析应用将提高我们触达正确客户的能力，以及在触达后有效服务客户的能力。但它们不会就此止步。处于大数据和移动交叉点的大数据分析应用，将改善我们就医时获得的护理质量。有朝一日，大数据分析应用甚至可能使自动驾驶汽车接管我们的日常通勤。无论应用场景如何，大数据领域在未来几年内必将成为深刻创新的源泉。

---

## 章节

### 你的大数据路线图

思考在数据收集和分析完成后，你希望如何根据结果采取行动，这将帮助你顺利导航至成功的大数据成果。在某些情况下，你的项目成果可能是你向管理层、合作伙伴或客户展示的交互式可视化或仪表板。在其他情况下，你可能会实施使用大数据采取算法行动（做出更好的财务决策、自动更改定价或投放更有针对性的广告）的自动化系统。

你的路线图还应包括一个计划，用于组建合适的团队并获取你想要分析的必要数据资产。有了愿景和关键问题，你就可以整合必要的人员、技术和数据源，来交付你所需的答案。

## 再见 SQL，你好 NoSQL

首先，让我们看看大数据中一个备受关注的新兴领域——SQL 和 NoSQL。从许多方面来看，`NoSQL` 数据存储像是回到了未来。传统关系数据库管理系统（RDBMS）依赖基于表的数据存储和结构化查询语言（`SQL`）来访问数据。相比之下，`NoSQL` 系统既不使用基于表的方法，也不使用 `SQL`。相反，它们依赖键值存储方法进行数据存储和查找。实际上，`NoSQL` 系统与关系数据库系统引入之前普遍使用的信息管理系统（IMS）非常相似！

在关系数据库系统出现之前，数据存储在相对简单的数据存储中，这与当今 `NoSQL` 产品中的键值系统并无二致。发生变化的是，在经过很长一段时间（大约 40 年）使用计算机系统存储结构化数据之后，非结构化数据现在的增长速度远快于其结构化对应物。IMS 重新受到青睐，而它如今被称为 `NoSQL`。另一个变化是，正如 `MySQL` 因其开源分发方式而流行起来一样，`NoSQL` 也受益于开源软件的流行，主要体现在 `MongoDB` 和 `Cassandra` 上。

与同为开源但可通过同名公司获得商业支持的 `MySQL` 数据库类似，`MongoDB` 也采取了类似的方法。`MongoDB` 作为免费的开源软件提供，其商业版本和支持可通过同名公司获得。`Cassandra` 是另一个 `NoSQL` 数据库，由 Apache 软件基金会维护。

`MongoDB` 是最流行的 `NoSQL` 数据库，MongoDB（公司）已获得约 2.31 亿美元融资。DataStax 为 `Cassandra` 提供商业支持，迄今已获得约 8300 万美元融资。`Aerospike` 是该领域的另一个参与者，它专注于通过优化其产品以充分利用闪存存储的特性来提高 `NoSQL` 性能。`Aerospike` 最近决定将其数据库服务器代码开源。

`NoSQL` 数据库特别适合作为文档存储。传统的结构化数据库需要在存储数据之前预先定义表和列。相比之下，`NoSQL` 数据库使用可以动态更改的动态模式。

由于现在产生的非结构化数据量巨大，`NoSQL` 数据库正变得越来越流行。现在说“告别” `SQL` 数据库可能还为时过早，但对于需要存储海量非结构化数据的应用程序来说，`NoSQL` 具有很大的意义。

## 编译和查询大数据

确定要处理的数据源是制定大数据路线图的第一步重要工作。有些数据可能是公开的，例如市场、人口或天气数据。其他数据源可能存在于内部的各种不同位置。如果你希望分析并减少网络安全问题，你的数据可能以日志文件的形式分布在不同网络设备上。

如果你的目标是通过更好的销售和营销来增加收入，你的数据可能以网站日志、应用程序仪表板以及各种分析产品的形式出现，这些产品可能位于内部或云端。或者，你可能正在从事一个大数据法律合规项目。在这种情况下，你的数据以文档和电子邮件的形式分散在电子邮件系统、文件服务器和系统备份中。金融交易数据可能存储在不同的数据仓库中。

根据你选择使用的工具，你可能不需要将所有数据汇集到同一个存储库中。有些产品可以处理其原有位置的数据，而不是将数据拉取到一个公共存储中。在其他情况下，你可能希望将所有数据编译到一个地方，以便更快地进行分析。

一旦你获得了数据访问权限，下一步就是运行查询以获取你需要的特定数据。尽管非结构化数据的规模不断增长，但绝大多数大数据仍然使用 `SQL` 和类似 `SQL` 的方法进行查询。但首先，让我们谈谈如何将数据转换为可以处理的状态。

---

¹ `http://gigaom.com/2013/01/08/idc-says-big-data-will-be-24b-market-in-2016-i-say-its-bigger/`  
² `http://www.theguardian.com/technology/2014/feb/04/facebook-10-years-mark-zuckerberg`  
³ `http://www.digitaltrends.com/social-media/according-to-facebook-there-are-350-million-photos-uploaded-on-the-social-network-daily-and-thats-just-crazy/#!Cht6e`  
⁴ `http://us.gizmodo.com/5937143/what-facebook-deals-with-everyday-27-billion-likes-300-million-photos-uploaded-and-500-terabytes-of-data`  
⁵ `http://twopcharts.com/twitteractivitymonitor`  
⁶ `http://www.securelist.com/en/analysis/204792243/Spam_in_July_2012`  
⁷ `http://knowwpcarey.com/article.cfm?cid=25&aid=1171`  
⁸ `http://en.wikipedia.org/wiki/Extract,_transform,_load`  
⁹ `http://phx.corporate-ir.net/phoenix.zhtml?c=176060&p=irol-corporateTimeline`  
¹⁰ `http://en.wikipedia.org/wiki/Amazon_Web_Services#cite_note-1`  
¹¹ `http://www.informationweek.com/software/information-management/amazon-redshift-leaves-on-premises-openi/240143912`  
¹² `http://www.informationweek.com/cloud/infrastructure-as-a-service/amazon-web-services-revenue-new-details/d/d-id/1112068?`  
¹³ `http://aws.typepad.com/aws/2012/06/amazon-s3-the-first-trillion-objects.html`  
¹⁴ 作者是 Ignition Partners 的投资人。  
¹⁵ `http://www.xconomy.com/san-francisco/2010/11/04/is-cloudera-the-next-oracle-ceo-mike-olson-hopes-so/2/`  
¹⁶ `http://static.googleusercontent.com/external_content/untrusted_dlcp/research.google.com/en/us/archive/mapreduce-osdi04.pdf`  
¹⁷ `http://blog.cloudera.com/blog/2012/10/cloudera-impala-real-time-queries-in-apache-hadoop-for-real/`  
¹⁸ `http://www.time.com/time/magazine/article/0,9171,1992403,00.html`  
¹⁹ `http://consumerist.com/2012/07/09/how-does-netflix-categorize-movies/`  
²⁰ `http://www.turn.com/whyturn`  
²¹ `http://www.slideshare.net/HusetMarkedsforing/rtb-update-4-dominic-trigg-rocket-fuel`  
²² `http://www.b2bmarketinginsider.com/strategy/real-time-marketing-trading-room-floor`  
²³ `http://en.wikipedia.org/wiki/Business_intelligence`