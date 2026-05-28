# -*- coding: utf-8 -*-

```python
import sys
import os
import pandas as pd
import sqlite3 as sq
import re
```

如果 `sys.platform` 是 `'linux'` 或 `'darwin'`，则 `Base` 设置为 `os.path.expanduser('~') + 'VKHCG'`，否则 `Base` 设置为 `'C:/VKHCG'`。

```python
if sys.platform == 'linux' or sys.platform == 'darwin':
    Base = os.path.expanduser('~') + 'VKHCG'
else:
    Base = 'C:/VKHCG'
```

```python
print('################################')
print('Working Base :', Base, ' using ', sys.platform)
print('################################')
```

以下是输入输出文件路径和公司标识：

```python
sInputFileName = '03-Process/01-EDS/02-Python/Process_ExchangeRates.csv'
sOutputFileName = '05-Organise/01-EDS/02-Python/Organise-Forex.csv'
Company = '04-Clark'
```

连接到 SQLite 数据库 `clark.db`。

```python
sDatabaseName = Base + '/' + Company + '/05-Organise/SQLite/clark.db'
conn = sq.connect(sDatabaseName)
#conn = sq.connect(':memory:')
```

## 将外汇数据导入生态系统。

```python
#### Import Forex Data
sFileName = Base + '/' + Company + '/' + sInputFileName
print('################################')
print('Loading :', sFileName)
print('################################')
ForexDataRaw = pd.read_csv(sFileName, header=0, low_memory=False, encoding="latin-1")
print('################################')
```

```python
ForexDataRaw.index.names = ['RowID']
sTable = 'Forex_All'
print('Storing :', sDatabaseName, ' Table:', sTable)
ForexDataRaw.to_sql(sTable, conn, if_exists="replace")
```

## 现在以 1,000,000.00 英镑作为初始资金。

```python
sSQL = "SELECT 1 as Bag\
, CAST(min(Date) AS VARCHAR(10)) as Date \
,CAST(1000000.0000000 as NUMERIC(12,4)) as Money \
,'USD' as Currency \
FROM Forex_All \
;"
sSQL = re.sub("\s\s+", " ", sSQL)
nMoney = pd.read_sql_query(sSQL, conn)
```

```python
nMoney.index.names = ['RowID']
sTable = 'MoneyData'
print('Storing :', sDatabaseName, ' Table:', sTable)
nMoney.to_sql(sTable, conn, if_exists="replace")
```

```python
sTable = 'TransactionData'
print('Storing :', sDatabaseName, ' Table:', sTable)
nMoney.to_sql(sTable, conn, if_exists="replace")
```

## 现在模拟克拉克小先生建议的交易模型。

> **注意**
> 克拉克小先生的假设——即基于有限证据作为您进一步研究起点的解释——是，如果您简单地持续兑换同一“袋”资金，将会产生利润。

```python
ForexDay=pd.read_sql_query("SELECT Date FROM Forex_All GROUP BY Date;", conn)

t=0
for i in range(ForexDay.shape[0]):
    sDay=ForexDay['Date'][i]
    sSQL='\
SELECT M.Bag as Bag, \
F.Date as Date, \
round(M.Money * F.Rate,6) AS Money, \
F.CodeIn AS PCurrency, \
F.CodeOut AS Currency \
FROM MoneyData AS M \
JOIN \
( \
SELECT \
CodeIn, CodeOut, Date, Rate \
FROM \
Forex_All \
WHERE\
CodeIn = "USD" AND CodeOut = "GBP" \
UNION \
SELECT \
CodeOut AS CodeIn, CodeIn AS CodeOut,  Date, (1/Rate) AS Rate \
FROM \
Forex_All \
WHERE\
CodeIn = "USD" AND CodeOut = "GBP" \
) AS F \
ON \
M.Currency=F.CodeIn \
AND \
F.Date ="' + sDay + '";'
    sSQL=re.sub("\s\s+", " ", sSQL)
    ForexDayRate=pd.read_sql_query(sSQL, conn)
    for j in range(ForexDayRate.shape[0]):
        sBag=str(ForexDayRate['Bag'][j])
        nMoney=str(round(ForexDayRate['Money'][j],2))
        sCodeIn=ForexDayRate['PCurrency'][j]
        sCodeOut=ForexDayRate['Currency'][j]
        sSQL='UPDATE MoneyData SET Date= "' + sDay + '", '
```

```
        sSQL= sSQL + ' Money = ' + nMoney + ', Currency="' + sCodeOut + '"'

        sSQL= sSQL + ' WHERE Bag=' + sBag + ' AND Currency="' + sCodeIn + '";'

        sSQL=re.sub("\s\s+", " ", sSQL)

        cur = conn.cursor()

        cur.execute(sSQL)

        conn.commit()

        t+=1

        print('Trade :', t, sDay, sCodeOut, nMoney)

        sSQL=' \

INSERT INTO TransactionData ( \

RowID, \

Bag, \

Date, \

Money, \

Currency \

)  \

SELECT ' + str(t) + ' AS RowID, \

Bag, \

Date, \

Money, \

Currency \

FROM MoneyData \

;'

        sSQL=re.sub("\s\s+", " ", sSQL)

        cur = conn.cursor()

        cur.execute(sSQL)

        conn.commit()

################################################################
```

接下来，加载交易日志，以深入了解实现这一额外收入流程的内在洞察。

```python
sSQL="SELECT RowID, Bag, Date, Money, Currency FROM TransactionData ORDER BY RowID;"

sSQL=re.sub("\s\s+", " ", sSQL)

TransactionData=pd.read_sql_query(sSQL, conn)

OutputFile=Base + '/' + Company + '/' + sOutputFileName

TransactionData.to_csv(OutputFile, index = False)

################################################################
```

这个挑战是，以1999年1月4日的1,000,000美元或603,189.41英镑为起点，通过将全部金额兑换为英镑或美元来获利。最终结果：到2017年5月5日，你赚取了119,828.98美元，即投资回报率为19.87%。干得漂亮。

**警告**
这是一个简单的例子。外汇交易属于高风险行为。在为客户尝试此操作前，请务必寻求专业建议。

## 报告超级步骤

`报告`超级步骤是生态系统中的一个环节，它通过讲故事和数据可视化的艺术来提升数据科学成果的价值。你可以进行最优秀的数据科学分析，但如果你无法通过执行一个可靠且值得信赖的`报告`步骤，将数据科学转化为可操作的商业洞察，那么你就没有为你的业务带来任何优势。让我通过一个例子来指导你，展示你到目前为止从本书中学到的内容。

### 结果摘要

任何分析中最重要的步骤就是结果摘要。你的数据科学技术和算法可以产生最严谨、最先进的数学或最具体的统计结果来满足需求，但如果你不能将这些结果总结成一个好的故事，你就没有达到你的要求。

#### 理解背景

区分优秀数据科学家与顶尖数据科学家的，不是算法或数据工程，而是数据科学家将其发现应用于客户背景的能力。

**示例：**
酒吧在23:45提供了最后一轮酒水，还剩一位客人。到23:50时，这位饮酒者杯中剩下的啤酒不到5%。

从饮酒者的背景来看，如果他必须赶午夜巴士，他要么喝得更慢以撑到午夜，要么立刻喝完剩下的酒。从酒吧员工的背景来看，他们想在23:45关门，并且必须让这最后一位顾客离开才能锁门，因为他们也想赶午夜巴士。

显然，如果你能确定双方是否都想赶午夜巴士，你就能理解饮酒者杯中啤酒量的背景。单纯的数据科学指标——杯中还剩5%——是毫无价值的。

我曾有一位初级数据科学家试图利用啤酒厂闭路电视系统进行图像处理，来判断人们杯中啤酒的液位，以便让他的数据科学系统在夜间通知酒吧员工顾客的饮酒速度。其目的是在晚班时段多赚取3%的利润。

但问题更为复杂，因为每晚都有几位当地的常客会在午夜后留下来，去赶对面公交枢纽在12:30之后新推出的有补贴的“安全驾驶”夜班车回家，这样他们就不用支付常规的公交车费。所以，他们喝酒的花费比回家的车费还少，而且还能免费乘车，前提是他们身上有酒味。

结果，啤酒厂不得不为三家酒吧的六名员工支付午夜后的双倍加班费，原因在于午夜过后，出于安全考虑，员工不能单独与顾客待在酒吧里。

员工们总是很晚才回家，因为他们不符合慈善夜班车的资格，只能打车回家。直到啤酒厂向数据科学家提供了外部闭路电视录像，我们才发现了真正的问题所在。解决方案是：如果顾客在23:30之前乘坐常规公交车，并在啤酒厂购买了啤酒，就为他们提供一张常规公交车票的优惠券。晚班总利润因此增加了7.3%。不需要复杂的数据科学，只需要一些背景理解。

我见过太多数据科学家花费大量时间，用最复杂的算法得出优秀的结果，却无法以业务能理解的方式向业务方阐述他们的发现。甚至更糟的是，他们的结果无法投入生产，因为在更大规模上，这些结果根本不像数据科学家设计的那样有效——这使得整个工作从一开始就毫无意义。

**警告**
经验告诉我，这是数据科学家成败的关键领域。始终要确定你结果的完整背景环境。理解结果背后的故事。

#### 恰当的可视化

一图胜千言，这句话没错。但在数据科学中，你只希望你的可视化传达一个故事：即你准备的数据科学发现。确保你的受众能清晰无误地接收到你最重要的信息，这一点至关重要。

**警告**
当心使用过多颜色和选择不清晰的图表格式。你会失去想要传达的信息！保持简洁，切中要点。

勤加练习你的可视化工具，达到高水平的熟练度。我见过无数数据科学家因为没能进行恰当的可视化呈现，而失去了优秀数据科学成果的价值。

#### 消除冗余

你是否参加过这样的演示：演讲者费尽心思准备了50张幻灯片来反馈他的数据科学成果？最痛苦的景象就是听众们在忍受这种超过两小时的演示时脸上的表情。

**提示**
平均而言，讲解一张幻灯片需要五分钟。你绝不应该展示超过十张幻灯片。

数据科学家最大的任务就是消除数据集中的冗余。虽然有各种算法，例如主成分分析（`PCA`），利用方差膨胀因子处理多重共线性来消除维度、填补或消除缺失值，用决策树进行细分，以及后向特征消除等，但消除冗余的最大贡献者是扎实可靠的特征工程。

**提示**
如果不需要，就舍弃它！应用恰当的特征工程每次都能胜出。

#### 在需要聚焦的地方引起注意

请记住：作为一名数据科学家，你的目标是为客户传递洞见，以便他们能够实施解决方案，来解决一个他们甚至可能尚未意识到的问题。你必须将注意力放在洞见本身，而非过程上。然而，你务必确保你的流程经过验证，并能支撑起一种经公认的算法或技术。

> **警告**
> 不要陷入这样一种境地：你的发现之所以受到质疑，仅仅是因为你的方法和步骤不够清晰和精确。

只讲述当前最重要的故事。以后你随时可以回来讲述下一个故事。

#### 讲述故事（弗赖塔格金字塔）

根据弗赖塔格金字塔，一个故事的剧情由五部分组成：铺垫、上升动作、高潮、下降动作和结局。这是书籍和剧本作者用来构建任何故事的基本框架。同理，你也必须引导你的业务经历数据科学流程。

- **铺垫**是故事中向观众介绍重要背景信息的部分。在数据科学中，你要讲述你所开展调查的背景。
- **上升动作**指的是一系列事件，它们共同推向最引人入胜的关键点。在数据科学中，你要指出重要的发现或结果。保持简洁，切中要害。
- **高潮**是决定故事人物命运好坏的转折点。在数据科学中，你要展示你的解决方案或发现将如何改变你所完成工作的结果。
- **在下降动作阶段**，高潮前后发生的事件之间的冲突得以展开。在数据科学中，你要证明，在试点中实施你的建议之后，同样的技术可以用来找出问题，并证实这些问题最终可以被解决。
- **结局**是故事的结果。在数据科学中，你要提出解决方案，并让改进得以永久化。

决定使用哪个可重复的“金句”来帮助你的核心信息在听众心中留下印象。人们通过你的重复来记住核心内容。你必须确保一件事：客户能记住你的核心信息。

确保你清晰无误地传达了这一信息。通过在会议中反复使用“金句”来强化核心信息。

一位优秀数据科学家的艺术，在于能够讲述关于数据的故事，并让人们记住其基线。

> **提示**
> 与朋友和家人一起练习你的故事讲述技巧。让他们给你反馈。确保你在众人面前讲话时感到自如。

既然你已经能轻松地讲述故事了，接下来我将为你提供更多示例，展示如何为数据科学创建报告步骤。

### 工程化实用报告超步骤

我在本书中引用的任何源代码或其他补充材料，读者均可通过本书的产品页面在GitHub上获取，地址为[`www.apress.com/9781484230534`](http://www.apress.com/9781484230534)。请注意，该源代码假设你已经完成了第2章中概述的源代码设置。

现在来检查一下你的图形能力。

```python
import matplotlib.pyplot as plt
import matplotlib.patheffects as path_effects

fig = plt.figure(figsize=(21, 3))
t = fig.text(0.2, 0.5, 'Practical Data Science', fontsize=75, weight=1000, va="center")
t.set_path_effects([path_effects.PathPatchEffect(offset=(4, -4), hatch="xxxx", facecolor='gray'), path_effects.PathPatchEffect(edgecolor='white', linewidth=1.1, facecolor='black')])
plt.plot()
plt.show()
```

既然我已经解释了报告超步骤的各个方面，我将展示如何帮助VKHC集团公司处理他们的流程。尝试以下代码，了解你在数据科学公司规模上的进展：

```python
import matplotlib.pyplot as plt
from numpy.random import randn

z = randn(8)
plt.figure(figsize=(15, 5))
red_dot, = plt.plot(z, "ro-", markersize=18)
white_cross, = plt.plot(z[:5], "w+", markeredgewidth=3, markersize=15)
black_cross, = plt.plot(z[:2], "y+", markeredgewidth=3, markersize=15)
plt.legend([red_dot, (red_dot, white_cross),(red_dot,black_cross)], ["Krennwallner", "Clark","Hillman"])
```

# Vermeulen PLC 公司

Vermeulen 公司需要一份所有客户数据链接的地图。你能提供一份报告来满足这个需求吗？我将通过一个示例来指导你如何实现这一需求。

## 创建网络路由图

让我们加载客户数据并创建一个图数据库。你可以使用该图数据库来生成图像，从而支撑你关于客户的故事叙述。

打开你的 `Python` 编辑器，并创建标准环境。

```python
import sys
import os
import pandas as pd
import networkx as nx
import matplotlib.pyplot as plt

pd.options.mode.chained_assignment = None

if sys.platform == 'linux' or sys.platform == 'darwin':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'

print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')

sInputFileName='02-Assess/01-EDS/02-Python/Assess-Network-Routing-Customer.csv'

sOutputFileName1='06-Report/01-EDS/02-Python/Report-Network-Routing-Customer.gml'
sOutputFileName2='06-Report/01-EDS/02-Python/Report-Network-Routing-Customer.png'
Company='01-Vermeulen'
```

从接入步骤中导入网络客户数据。

```python
#### 导入国家数据

sFileName=Base + '/' + Company + '/' + sInputFileName
print('################################')
print('加载中 :',sFileName)
print('################################')
CustomerDataRaw=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
CustomerData=CustomerDataRaw.head(100)
print('已加载国家数据:',CustomerData.columns.values)
print('################################')

print(CustomerData.head())
print(CustomerData.shape)

G=nx.Graph()
for i in range(CustomerData.shape[0]):
    for j in range(CustomerData.shape[0]):
        Node0=CustomerData['Customer_Country_Name'][i]
        Node1=CustomerData['Customer_Country_Name'][j]
        if Node0 != Node1:
            G.add_edge(Node0,Node1)

for i in range(CustomerData.shape[0]):
    Node0=CustomerData['Customer_Country_Name'][i]
    Node1=CustomerData['Customer_Place_Name'][i] + '('+ CustomerData['Customer_Country_Name'][i] + ')'
    Node2='('+ "{:.9f}".format(CustomerData['Customer_Latitude'][i]) + ')' \
          '('+ "{:.9f}".format(CustomerData['Customer_Longitude'][i]) + ')'
    if Node0 != Node1:
        G.add_edge(Node0,Node1)
    if Node1 != Node2:
        G.add_edge(Node1,Node2)
```

以下是你对网络客户数据的洞察结果：

```python
print('节点数:', G.number_of_nodes())
print('边数:', G.number_of_edges())
```

导出网络客户数据。

```python
sFileName=Base + '/' + Company + '/' + sOutputFileName1
print('################################')
print('存储中 :',sFileName)
print('################################')
nx.write_gml(G, sFileName)

sFileName=Base + '/' + Company + '/' + sOutputFileName2
print('################################')
print('存储图图像:',sFileName)
print('################################')
```

现在，你可以为网络客户准备一张图像，用于解释你所获得的洞察。

```python
plt.figure(figsize=(25, 25))
pos=nx.spectral_layout(G,dim=2)
nx.draw_networkx_nodes(G,pos, node_color="k", node_size=10, alpha=0.8)
nx.draw_networkx_edges(G, pos,edge_color='r', arrows=False, style="dashed")
nx.draw_networkx_labels(G,pos,font_size=12,font_family='sans-serif',font_color='b')
plt.axis('off')
plt.savefig(sFileName,dpi=600)
plt.show()
print('################################')
print('### 完成!! #####################')
print('################################')
```

干得好。你已经为 Vermeulen 的管理层生成了一份关于其客户网络需求的报告。

## Krennwallner AG

Krennwallner 市场部门希望将广告牌的位置部署到公司 Web 服务器上。你能为位置信息准备三个版本的网页吗？

-   缩小视图时，位置以气泡形式聚集显示
-   位置以图钉形式显示
-   位置以热力图形式显示

要实现这个目标，我必须向你介绍一个新的库：`folium`。这个库能帮助你利用地理位置信息创建网页。使用 `conda install -c conda-forge folium` 命令安装它。

### 为广告牌挑选内容

打开你的 Python 编辑器，搭建以下环境：

```