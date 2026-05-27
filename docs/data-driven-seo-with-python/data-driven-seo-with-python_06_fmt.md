# 3. 技术

技术 SEO 主要关注搜索引擎与网站之间的交互，具体包括：

- 使网站内容能够被搜索引擎发现。
- 通过内容与首页的距离，向搜索引擎暗示其优先级。
- 节省搜索引擎资源，以便访问（即爬取）那些旨在纳入搜索结果的内容。
- 再次从这些 URL 中提取内容含义，以便纳入搜索结果（即索引）。

在本章中，我们将探讨如何通过数据驱动的方法来改进技术 SEO，具体方式如下：

- `建模页面权威度`：这有助于帮助 SEO 从业者及非 SEO 人员理解技术 SEO 变更的影响。
- `内部链接优化`：改进内部链接的使用，使内容更易于被发现，并帮助向搜索引擎传达内容的优先级。
- `核心网页指标（CWV）`：虽然用户体验方面的益处常被称道，但改进 CWV 还能带来排名提升的好处，这是因为搜索引擎用于从网页提取内容的资源得到了节省。

我们绝不声称这是从技术角度对数据驱动 SEO 的最终定论。我们将要做的是，利用一些数据科学方法（如分布分析），揭示解决技术 SEO 问题的数据驱动途径。

## 数据科学的用武之地

SEO 的一个明显挑战是决定哪些页面应该对搜索引擎和用户开放，哪些不应该。虽然许多爬取工具会提供按站点深度等维度划分的页面分布可视化图表，但使用数据科学也绝无坏处。我们将深入探讨更详细、更复杂的细节，这将帮助您：

- 优化内部链接
- 根据页面内容将关键词分配到各页面
- 为孤立 URL 分配父节点

最终，上述列表将帮助您构建更有力的论据，以推动技术建议的实施。

## 建模页面权威度

技术优化通常涉及建议进行一些变更，这些变更往往会使 URL 变得不可索引或被规范化（原因有多种，例如内容重复）。提出这些变更的目的是将页面权威度集中到那些仍有资格被索引的 URL 上。

以下部分旨在帮助数据驱动的 SEO 量化有益的额外页面权威度。具体方法如下：

1. 筛选出网页
2. 检查优化前的权威度分布
3. 计算新的分布（以便在决定哪些 URL 将不再可索引后，量化其释放出的权威度可重新分配所带来的增量页面权威度）

首先，我们需要加载必要的包：

```python
import re
import time
import random
import pandas as pd
import numpy as np
import datetime
import requests
import json
from datetime import timedelta
from glob import glob
import os
from client import RestClient # 如果使用 Data For SEO API
from textdistance import sorensen_dice
from plotnine import *
import matplotlib.pyplot as plt
from pandas.api.types import is_string_dtype
from pandas.api.types import is_numeric_dtype
import uritools
from urllib.parse import urlparse
import tldextract
pd.set_option('display.max_colwidth', None)
%matplotlib inline
```

设置变量：

```python
root_domain = 'boundlesshq.com'
hostdomain = 'www.boundlesshq.com'
hostname = 'boundlesshq'
full_domain = 'https://www.boundlesshq.com'
client_name = 'Boundless'
audit_monthyear = 'jul_2022'
```

从 Sitebulb 桌面爬虫导入爬取数据。也可以使用 Screaming Frog 或任何其他网站爬取软件；不过，列名可能会有所不同：

```python
crawl_csv = pd.read_csv('data/boundlesshq_com_all_urls__excluding_uncrawled__filtered.csv')
```

使用列表推导式清理列名：

```python
crawl_csv.columns = [col.lower().replace('.','').replace('(','').replace(')','').replace(' ','_')
for col in crawl_csv.columns]
crawl_csv
```

以下是 `crawl_csv` 的结果：

![](img/537787_1_En_3_Figa_HTML.jpg)

数据框的截图显示了 URL 和爬取深度数据。该数据框或表格包含 5422 行和 25 列。

该数据框已加载到 Pandas 数据框中。最重要的字段如下：

- `url`：用于检测不索引和规范化的模式
- `ur`：URL 评分，Sitebulb 用于衡量内部页面权威度的内部指标
- `content_type`：用于筛选
- `passes_pagerank`：用于了解哪些页面具有权威度
- `indexable`：是否有资格被搜索引擎索引

### 筛选网页

下一步是筛选出属于该网站且能够传递权威度的实际网页：

```python
crawl_html = crawl_csv.copy()
crawl_html = crawl_html.loc[crawl_html['content_type'] == 'HTML']
crawl_html = crawl_html.loc[crawl_html['host'] == root_domain]
crawl_html = crawl_html.loc[crawl_html['passes_pagerank'] == 'Yes']
crawl_html
```

![](img/537787_1_En_3_Figb_HTML.jpg)

数据框的截图显示了 URL、爬取深度、爬取状态、主机、是否为子域名、方案和爬取来源数据。该数据框或表格包含 309 行和 25 列。

数据框已缩减至 309 行。为便于数据处理，我们将选择部分列：

```python
crawl_select = crawl_html[['url', 'ur', 'crawl_depth', 'crawl_source', 'http_status_code', 'indexable',
'indexable_status', 'passes_pagerank', 'total_impressions', 'first_parent_url', 'meta_robots_response']].copy()
```

### 优化前审视权限分布

这对于分组聚合和计数非常有用：

```python
crawl_select['project'] = client_name
crawl_select['count'] = 1
```

让我们快速获取一些统计数据：

```python
print(crawl_select['ur'].sum(), crawl_select['ur'].sum()/crawl_select.shape[0])
10993 35.57605177993528
```

该网站上的 URL 具有平均页面权限级别（以 `UR` 衡量）。让我们查看一些进一步的统计数据，包括可索引和不可索引的页面。我们将按 (I) 可索引和 (II) 传递 PageRank 进行维度划分，以汇总 URL 数量和 `UR`（URL 评级）：

```python
overall_pagerank_agg = crawl_select.groupby(['indexable',
'passes_pagerank']).agg({'count': 'sum',
'ur': 'sum'}).reset_index()
```

然后，我们通过将总 `UR` 除以 URL 总数来推导出每个 URL 的页面权限：

```python
overall_pagerank_agg['PA'] = overall_pagerank_agg['ur'] / overall_pagerank_agg['count']
overall_pagerank_agg
```

结果如下：

![](img/537787_1_En_3_Figc_HTML.jpg)

数据框的屏幕截图包含 2 行数据。列标题为 `indexable`、`passes_pagerank`、`count`、`ur` 和 `PA`。

我们看到有 32 个不可索引的 URL，总权限为 929，可以合并到可索引的 URL 中。

还有一些统计数据，这次纯粹出于好奇按站点级别进行分析：

```python
site_pagerank_agg = crawl_select.groupby(['indexable',
'crawl_depth']).agg({'count': 'sum',
'ur': 'sum'}).reset_index()
site_pagerank_agg['PA'] = site_pagerank_agg['ur'] / site_pagerank_agg['count']
site_pagerank_agg
```

结果如下：

![](img/537787_1_En_3_Figd_HTML.jpg)

数据框的屏幕截图显示了 `indexable`、`crawl_depth`、`count`、`ur` 和 `PA` 的 0 到 10 行值。

大多数具有可重新分配权限的 URL 距离主页有四次点击的距离。

让我们使用 `geom_histogram` 函数可视化优化前的权限分布：

```python
pageauth_dist_plt = (
ggplot(crawl_select, aes(x = 'ur')) +
geom_histogram(alpha = 0.7, fill = 'blue', bins = 20) +
labs(x = 'Page Authority', y = 'URL Count') +
theme(legend_position = 'none', axis_text_x=element_text(rotation=0, hjust=1, size = 12))
)
pageauth_dist_plt.save(filename = 'images/1_pageauth_dist_plt.png',
height=5, width=8, units = 'in', dpi=1000)
pageauth_dist_plt
```

正如我们从之前计算的统计数据中所预期的那样，大多数页面的 `UR` 在 25 到 50 之间，其余页面分散分布（图 3-1）。

![](img/537787_1_En_3_Fig1_HTML.png)

直方图绘制了 URL 数量与页面权限的关系。URL 数量和页面权限的范围从 0 到 100，间隔为 25。图表趋势在页面权限 25 到 50 之间较高。

**图 3-1.** 显示 URL 页面权限分数的 URL 数量的直方图

### 计算新的分布

在检查了当前分布之后，我们现在将量化优化后的新页面权限分布。

我们将首先获取一个按第一个父 URL 和 URL 的 `UR` 值划分的 URL 表，这将作为我们映射有多少额外权限可用的依据：

```
parent_pa_map = crawl_select[['first_parent_url', 'ur']].copy()
parent_pa_map = parent_pa_map.rename(columns = {'first_parent_url': 'url' , 'ur': 'extra_ur'})
parent_pa_map
```

结果如下：

![](img/537787_1_En_3_Fige_HTML.jpg)

父 URL 及其映射表的屏幕截图。列标题为 `url` 和 `extra_ur`。提到该表有 309 行和 2 列。

该表显示了所有父 URL 及其映射。

下一步是标记将被设为 noindex 的页面，以便我们可以重新分配它们的权限：

```
crawl_optimised = crawl_select.copy()
```

为 noindex 创建一个 URL 模式列表：

```
reallocate_conds = [
crawl_optimised['url'].str.contains('/page/[0-9]/'),
crawl_optimised['url'].str.contains('/country/')
]
```

如果满足 URL 模式条件，则对应的值：

```
reallocate_vals = [1, 1]
```

`reallocate` 列使用 `np.select` 函数来标记要设为 noindex 的 URL。任何不设为 noindex 的 URL 都使用 `default` 参数标记为 `0`：

```
crawl_optimised['reallocate'] = np.select(reallocate_conds, reallocate_vals, default = 0)
crawl_optimised
```

结果如下：

![](img/537787_1_En_3_Figf_HTML.jpg)

一个表格的屏幕截图，包含 `first_parent_url`、`meta_robots_response`、`project`、`count` 和 `reallocate` 的数据。

添加了 `reallocate` 列，以便我们可以开始看到重新分配的效果，即技术优化的潜在优势。

像往常一样，按 `reallocate` 进行分组操作，并计算平均 `PA`：

```
reallocate_agg = crawl_optimised.groupby('reallocate').agg({'count': sum, 'ur': sum}).reset_index()
reallocate_agg['PA'] = reallocate_agg['ur'] / reallocate_agg['count']
reallocate_agg
```

结果如下：

![](img/537787_1_En_3_Figg_HTML.jpg)

一个表格的屏幕截图，列出了 `reallocate`、`count`、`ur` 和 `PA` 相对于行值 0 和 1 的数据。`reallocate` 的值为 0 和 1，`count` 为 285、24，`ur` 为 10312、681，`PA` 为 36.182456、28.375000。

因此，我们实际上将把 681 个 `UR` 从 noindex URL 重新分配给 285 个可索引 URL。这些 noindex URL 的平均 `UR` 为 28。

我们只过滤将要设为 noindex 的 URL，以帮助我们确定额外的页面权限：

```
no_indexed = crawl_optimised.loc[crawl_optimised['reallocate'] == 1]
```

我们按第一个父 URL（父节点）聚合其中的总 URL 及其 URL，因为 `UR` 可能会重新分配给共享同一父节点的剩余可索引 URL：

```
no_indexed_map = no_indexed.groupby('first_parent_url').agg({'count': 'sum', 'ur': sum}).reset_index()
```

`add_ur` 是一个新创建的列，表示优化带来的额外权限。这是总 `UR` 除以 URL 数量：

```
no_indexed_map['add_ur'] = (no_indexed_map['ur'] / no_indexed_map['count']).round(0)
```

删除稍后连接不需要的列：

```
no_indexed_map.drop(['ur', 'count'], inplace = True,  axis = 1)
no_indexed_map
```

结果如下：

![](img/537787_1_En_3_Figh_HTML.jpg)

一个表格的屏幕截图，行标题编号从 0 到 7。列标题为 `first_parent_url` 和 `add_ur`。列出了 URL 链接和值。

上表将按第一个父 URL 合并到可索引 URL 中。

仅过滤可索引的 URL，并由于 noindex 重新分配 URL 而增加更多权限：

```
crawl_new = crawl_optimised.copy()
crawl_new = crawl_new.loc[crawl_new['reallocate'] == 0]
```

连接 `no_indexed_map` 以获取待添加的权威值：

```
crawl_new = crawl_new.merge(no_indexed_map, on = 'first_parent_url', how = 'left')
```

通常，在连接数据时，未出现在映射中的第一父级 URL 会出现空值。使用 `np.where()` 将这些空值替换为零，以便后续进行进一步的数据操作。

```
crawl_new['add_ur'] = np.where(crawl_new['add_ur'].isnull(), 0, crawl_new['add_ur'])
```

`new_ur` 是通过将 `ur` 与 `add_ur` 相加计算得出的新权威分数：

```
crawl_new['new_ur'] = crawl_new['ur'] + crawl_new['add_ur']
crawl_new
```

结果如下：

![](img/537787_1_En_3_Figi_HTML.jpg)

一张表格截图，包含以下数据：第一父级 URL、元数据 robots 响应、项目、计数、重新分配、add_ur 和 new_ur。

可索引的 URL 现在已获得优化后的权威分数，我们将通过以下方式进行可视化：

```
pageauth_newdist_plt = (
ggplot(crawl_new, aes(x = 'new_ur')) +
geom_histogram(alpha = 0.7, fill = 'lightgreen', bins = 20) +
labs(x = '页面权威度', y = 'URL 数量') +
theme(legend_position = 'none', axis_text_x=element_text(rotation=0, hjust=1, size = 12))
)
pageauth_newdist_plt.save(filename = 'images/2_pageauth_newdist_plt.png',
height=5, width=8, units = 'in', dpi=1000)
pageauth_newdist_plt
```

图 3-2 中的 `pageauth_newdist_plt` 展示了页面级权威度（页面权威度）的分布情况。

![](img/537787_1_En_3_Fig2_HTML.png)

一张直方图，横轴为页面权威度（0 到 100），纵轴为 URL 数量（0 到 80）。与其他区间相比，页面权威度在 60 到 80 的区间内，URL 数量较高。数值为近似值。

图 3-2 页面级权威度（页面权威度）分布直方图

影响是显著的，我们看到优化后大多数页面的 UR 都超过了 60，如果实施推进的话。

以下是一些快速统计数据来确认：

```
new_pagerank_agg = crawl_new.groupby(['reallocate']).agg({'count': 'sum',
'ur': 'sum',
'new_ur': 'sum'}).reset_index()
new_pagerank_agg['PA'] = new_pagerank_agg['new_ur'] / new_pagerank_agg['count']
print(new_pagerank_agg)
```
```
reallocate  count     ur   new_ur    PA
0           0    285  10312  16209.0  57.0
```

平均页面权威度现在为 57，而之前是 36，这是一个显著的提升。虽然这种方法并非精确科学，但它可以帮助你构建论据，推动技术 SEO 修复的变更请求得以实施。

### 内部链接优化

搜索引擎高度依赖链接来帮助确定网站内页面的相对重要性。这是因为搜索引擎基于随机冲浪者概念，通过分配内容被用户随机发现的概率来运作。也就是说，如果某个内容有更多链接指向它，那么它被发现的概率就更高。

如果某个内容有更多的入站链接，搜索引擎也会认为该内容具有更高的价值，因为它获得了更多的链接。

搜索引擎还依赖锚文本（链接文字）来指示超链接 URL 的内容主题，从而判断其与关键词的相关性。

因此，对于 SEO 而言，内部链接在网站优化中扮演着关键角色，帮助搜索引擎决定网站上哪些页面是重要的，以及它们关联的关键词是什么。

在这里，我们将提供一些利用数据科学优化内部链接的方法，内容涵盖：
1.  按网站层级分配权威度
2.  按外部网站累积的外部页面权威度分配权威度
3.  锚文本

```
import pandas as pd
import numpy as np
from textdistance import sorensen_dice
from plotnine import *
import matplotlib.pyplot as plt
from mizani.formatters import comma_format
target_name = 'ON24'
target_filename = 'on24'
website = 'www.on24.com'
```

链接数据来源于 Sitebulb 审计软件，导入的同时简化了列名以便操作：

```
link_raw = pd.read_csv('data/'+ client_filename + '_links.csv')
link_data = link_raw.copy()
link_data.drop('Unnamed: 13', axis = 1, inplace = True)
link_data.columns = [col.lower().replace('.','').replace('(','').replace(')','').replace(' ','_')
for col in link_data.columns]
link_data
```

![](img/537787_1_En_3_Figj_HTML.jpg)

一张表格截图，包含以下数据：来源 URL、来源 URL 排名 UR、目标 URL、目标 URL 排名 UR、锚文本、位置、爬取状态等。该数据框或表格有 406027 行和 13 列。

链接数据框向我们展示了链接列表，涉及以下方面：
- **来源 URL**：链接所在的位置
- **目标 URL**：链接指向的位置
- **来源 URL 排名 UR**：来源页面的页面权威度
- **目标 URL 排名 UR**：目标页面的页面权威度
- **锚文本**：超链接中使用的文字
- **位置**：链接被发现的位置

让我们导入同样来自 Sitebulb 的爬取数据：

```
crawl_data = pd.read_csv('data/'+ client_filename + '_crawl.csv')
crawl_data.drop('Unnamed: 103', axis = 1, inplace = True)
crawl_data.columns = [col.lower().replace('.','').replace('(','').replace(')','').replace(' ','_')
for col in crawl_data.columns]
crawl_data
```

结果如下：

![](img/537787_1_En_3_Figk_HTML.jpg)

一张表格截图，包含以下数据：URL、基础 URL、爬取深度、爬取状态、主机、HTTP 协议、是否为子域名、无查询字符串键等。该数据框或表格有 8611 行和 104 列。

因此，我们得到了常见的 URL 列表及其被发现的方式（爬取来源），以及其他超过 100 列的特征。

正如你所料，链接数据的行数远远超过爬取数据框，因为链接的数量远多于页面数量！

导入外部入站链接数据：

```
ahrefs_raw = pd.read_csv('data/'+ client_filename + '_ahrefs.csv')
ahrefs_raw.columns = [col.lower().replace('.','').replace('(','').replace(')','').replace(' ','_')
for col in ahrefs_raw.columns]
ahrefs_raw
```

结果如下：

![](img/537787_1_En_3_Figl_HTML.jpg)

一张表格截图，包含以下数据：序号、URL 评级描述、页面 URL、页面标题、引用域、dofollow、nofollow、重定向、首次发现时间、大小。该数据框或表格有 210404 行和 13 列。

有超过 210,000 个带有反向链接的 URL，这非常棒！数据量相当大，所以我们先简化一下，删除一些列并重命名部分列，以便后续连接数据：

```
ahrefs_df = ahrefs_raw[['page_url', 'url_rating_desc', 'referring_domains']]
ahrefs_df = ahrefs_df.rename(columns = {'url_rating_desc': 'page_authority', 'page_url': 'url'})
ahrefs_df
```

结果如下：

![](img/537787_1_En_3_Figm_HTML.jpg)

一张表格截图，包含以下数据：URL、页面权威度、引用域。该数据框或表格有 210404 行和 3 列。

现在我们得到了简化形式的数据，这很重要，因为我们不关心链接的细节，而是关心它们为目标网站带来的预估页面级权威度。

### 按站点层级分析

数据导入并清理完毕后，就可以开始分析了。

我们总是很好奇在不同站点层级上分别有多少个 URL。通过一个快速的 `groupby` 聚合函数就能实现这一点：

```python
redir_live_urls.groupby(['crawl_depth']).size()
```

结果如下：

```
crawl_depth
0             1
1            70
10            5
11            1
12            1
13            2
14            1
2           303
3           378
4           347
5           253
6           194
7            96
8            33
9            19
Not Set    2351
dtype: int64
```

我们可以看到 Python 将 `crawl_depth` 视为字符串字符，而非数字类别，我们稍后会修正这个问题。

大部分站点 URL 都位于站点深度 2 到 6 之间。有超过 2351 个孤立 URL，这意味着除非它们有反向链接，否则无法继承任何权重。

现在，我们将筛选出重定向和有效的链接：

```python
redir_live_urls = crawl_data[['url', 'crawl_depth', 'http_status_code', 'indexable', 'no_internal_links_to_url', 'host', 'title']]
```

数据框被筛选为仅包含可索引的 URL：

```python
redir_live_urls = redir_live_urls = redir_live_urls.loc[redir_live_urls['indexable'] == 'Yes']
```

将 `crawl_depth` 设置为类别并进行排序，以便 Python 将该列变量视为数字而非字符串字符类型：

```python
redir_live_urls['crawl_depth'])
redir_live_urls['crawl_depth'] = redir_live_urls['crawl_depth'].astype('category')
redir_live_urls['crawl_depth'] = redir_live_urls['crawl_depth'].cat.reorder_categories(['0', '1', '2', '3', '4',
'5', '6', '7', '8', '9',
'10', 'Not Set'
])
redir_live_urls = redir_live_urls.loc[redir_live_urls.host == website]
redir_live_urls.drop('host', axis = 1, inplace = True)
redir_live_urls
```

结果如下：

![一张表格截图](img/537787_1_En_3_Fign_HTML.jpg)

一张表格截图，数据包含 `url`、`crawl_depth`、`http_status_code`、`indexable`、`indexable_status`、`no_internal_links_to_url` 和 `title`。文中提到该数据框或表格有 3483 行和 7 列。

让我们按站点层级查看 URL 的数量。

```python
redir_live_urls.groupby(['crawl_depth']).size()
crawl_depth
0             1
1            66
2           169
3           280
4           253
5           201
6           122
7            64
8            17
9             6
10            1
Not Set    2303
dtype: int64
```

注意，数量略微下降到了 2303 个 URL。那 48 个不可索引的 URL 很可能是分页页面。

让我们将分布情况可视化：

```python
from plotnine import *
import matplotlib.pyplot as plt
pd.set_option('display.max_colwidth', None)
%matplotlib inline
# 按站点层级划分的指向 URL 的内部链接分布
ove_intlink_dist_plt = (ggplot(redir_live_urls, aes(x = 'no_internal_links_to_url')) +
geom_histogram(fill = 'blue', alpha = 0.6, bins = 7) +
labs(y = '# 指向 URL 的内部链接数') +
theme_classic() +
theme(legend_position = 'none')
)
ove_intlink_dist_plt.save(filename = 'images/1_overall_intlink_dist_plt.png',
height=5, width=5, units = 'in', dpi=1000)
ove_intlink_dist_plt
```

图 3-3 中的 `ove_intlink_dist_plt` 图是一个直方图，展示了指向一个 URL 的内部链接数量。

![一个直方图](img/537787_1_En_3_Fig3_HTML.png)

一个直方图绘制了指向 URL 的内部链接数量与 `no_internal_links_to_url` 的关系。最大范围大约在 (0, 3500) 处。

**图 3-3**  
指向一个 URL 的内部链接数量的直方图

该分布呈负偏态，大多数页面的链接数接近于零。这可能会引起 SEO 经理的担忧。

虽然整体分布提供了一种视角，但深入探究按爬取深度划分的内部链接分布会更好：

```python
redir_live_urls.groupby('crawl_depth').agg({'no_internal_links_to_url': ['describe']}).sort_values('crawl_depth')
```

结果如下：

![一张表格截图](img/537787_1_En_3_Figo_HTML.jpg)

一张表格截图，展示了 `crawl_depth`、计数、均值、标准差、最小值、25%、50%、75% 和最大值的数据列表。表格顶部的文字是 `no_internal_links_to_url` 和 describe。

该表格描述了按爬取深度或站点层级划分的内部链接分布。任何距离首页 3 次或更多点击的 URL，平均预计有 2 个内部链接。这很可能是博客内容，因为营销团队会制作大量此类内容。

为了以图形方式可视化：

```python
from plotnine import *
import matplotlib.pyplot as plt
pd.set_option('display.max_colwidth', None)
%matplotlib inline
intlink_dist_plt = (ggplot(redir_live_urls, aes(x = 'crawl_depth', y = 'no_internal_links_to_url')) +
geom_boxplot(fill = 'blue', alpha = 0.8) +
labs(y = '# 指向 URL 的内部链接数', x = '站点层级') +
theme_classic() +
theme(legend_position = 'none')
)
intlink_dist_plt.save(filename = 'images/1_intlink_dist_plt.png', height=5, width=5, units = 'in', dpi=1000)
intlink_dist_plt
```

图 3-4 中的 `intlink_dist_plt` 图是一个箱线图，展示了按站点层级划分的指向一个 URL 的内部链接数量。

![一个箱线图](img/537787_1_En_3_Fig4_HTML.png)

一个带有散点的箱线图展示了指向 URL 的内部链接数量与站点层级的关系。站点层级 1 的范围最大，其下四分位数和上四分位数大约在 4000 到 7000 之间。

**图 3-4**  
按站点层级划分的指向一个 URL 的内部链接数量的箱线图分布

正如所料，最大的变异出现在紧邻首页的第一层级，之后的层级变异非常小。

然而，我们可以比较站点层级 2 及更深层级内容的变异情况。为了快速查看，我们将对指向 URL 的内部链接数量使用对数刻度：

```python
from mizani.formatters import comma_format
intlink_dist_plt = (ggplot(redir_live_urls, aes(x = 'crawl_depth', y = 'no_internal_links_to_url')) +
geom_boxplot(fill = 'blue', alpha = 0.8) +
labs(y = '# 指向 URL 的内部链接数', x = '站点层级') +
scale_y_log10(labels = comma_format()) +
theme_classic() +
theme(legend_position = 'none')
)
intlink_dist_plt.save(filename = 'images/1_log_intlink_dist_plt.png', height=5, width=5, units = 'in', dpi=1000)
intlink_dist_plt
```

图片更清晰、更具洞察力，因为我们可以看到较低站点层级之间的分布相比彼此有多好、变化有多大（图 3-5）。

![一个箱线图](img/537787_1_En_3_Fig5_HTML.png)

一个带有散点的箱线图展示了指向 URL 的内部链接数量与站点层级的关系。站点层级 1 的范围最大，其下四分位数和上四分位数大约在 6000 到 9000 之间，其次是站点层级 2。

**图 3-5**  
按站点层级划分的内部链接数量的箱线图分布（纵轴取对数）

例如，更明显的是，站点层级 2 上页面的入站内部链接中位数远高于较低层级。

同样非常明显的是，站点层级 3 和 4 中页面的内部入站链接变异高于层级 5 和 6。

不过请记住，前面的示例是通过对同一输入变量使用对数刻度实现的。

我们在这里学到的是，创建一个对内部链接取对数的新变量，将能提供一个更有帮助的视角来比较层级 2 到 10。

我们将通过创建一个新的列变量 `log_intlinks` 来实现这一点，该变量是内部链接计数的对数。为了避免对零取对数产生负无穷值，我们将在计算中加上 0.01：

```python
redir_live_urls['log_intlinks'] = np.log2(redir_live_urls['no_internal_links_to_url'] + .01)
```

现在，我们将使用新的对数化变量进行绘图：

```python
intlink_dist_plt = (ggplot(redir_live_urls, aes(x = 'crawl_depth', y = 'log_intlinks')) +
geom_boxplot(fill = 'blue', alpha = 0.8) +
labs(y = '# 指向 URL 的内部链接对数', x = '站点层级') +
theme_classic() +
theme(legend_position = 'none')
)
intlink_dist_plt.save(filename = 'images/1c_loglinks_dist_plt.png', height=5, width=5, units = 'in', dpi=1000)
intlink_dist_plt
```

`intlink_dist_plt` 图（图 3-6）与对数化刻度非常相似，只是这次数字更易读，因为我们使用了常规刻度作为纵轴。比较平均值和变异程度也更容易。

![一个箱线图](img/537787_1_En_3_Fig6_HTML.png)

带点的箱线图展示了指向 URL 的对数化内部链接数量与站点层级的关系。站点层级 1 的范围最大，其下四分位数和上四分位数大约在 12 到 13 之间，其次是站点层级 2，范围在 1 到 3 之间。

**图 3-6** 按站点层级划分的对数化内部链接箱线图分布

### 站点层级中链接不足的 URL

现在我们已经了解了站点深度层级上分布的整体情况，可以开始深入挖掘，看看每个站点层级有多少 URL 存在链接不足的问题。

例如，如果某个站点层级上，指向 URL 的内部链接数量的第 35 百分位数为 10，那么有多少 URL 低于该百分位数？这正是我们要找出的答案。为什么是 35%而不是 25%？这其实无关紧要，只需要选择一个较低的分界点即可，因为分界点是人为设定的。

第一步是计算非对数和对数版本的内部链接平均值，稍后这些平均值将合并到主数据框中：

```python
intlink_dist = redir_live_urls.groupby('crawl_depth').agg({'no_internal_links_to_url': ['mean'],
'log_intlinks': ['mean']
}).reset_index()
intlink_dist.columns = ['_'.join(col) for col in intlink_dist.columns.values]
intlink_dist = intlink_dist.rename(columns = {'no_internal_links_to_url_mean': 'avg_int_links',
'log_intlinks_mean': 'logavg_int_links',
})
intlink_dist
```

结果如下：

![](img/537787_1_En_3_Figp_HTML.jpg)

一张表格截图，数据位于列标题 `crawl_depth`、`avg_int_links` 和 `logavg_int_links` 下。行条目为数字 0 到 11。

平均值已按站点层级排列。注意，对数列如何帮助减少不同爬取深度之间数值范围的极端性和偏态性，例如，平均值的范围从 4239 到 0.06，而对数平均值的范围从 12 到 -6.39，这使得数据更容易标准化。

现在，我们将所有站点层级的下分位数设置为 35%。这将使用自定义函数 `quantile_lower`：

```python
def quantile_lower(x):
    return x.quantile(.35).round(0)

quantiled_intlinks = redir_live_urls.groupby('crawl_depth').agg({'log_intlinks':
[quantile_lower]}).reset_index()
quantiled_intlinks.columns = ['_'.join(col) for col in quantiled_intlinks.columns.values]
quantiled_intlinks = quantiled_intlinks.rename(columns = {'crawl_depth_': 'crawl_depth',
'log_intlinks_quantile_lower': 'sd_intlink_lowqua'})
quantiled_intlinks
```

结果如下：

![](img/537787_1_En_3_Figq_HTML.jpg)

一张表格截图，数据位于列标题 `crawl_depth` 和 `sd_intlink_lowqua` 下。行条目为数字 0 到 11。

下分位数统计已设置完成。四分位数仅限于第 25 百分位数，而分位数则可以将下限设置为任意数值，例如第 11、18、24 百分位数等，这就是我们使用分位数而非四分位数的原因。接下来的步骤是将数据合并到主数据框中，然后应用一个函数来标记在其给定站点层级中链接不足的 URL：

```python
redir_live_urls_underidx = redir_live_urls.merge(quantiled_intlinks, on = 'crawl_depth', how = 'left')
```

以下函数用于评估 URL 的链接数量是否低于下分位数。如果是，则 `sd_int_uidx` 的值为 1，否则为 0：

```python
def sd_intlinkscount_underover(row):
    if row['sd_intlink_lowqua'] > row['log_intlinks']:
        val = 1
    else:
        val = 0
    return val

redir_live_urls_underidx['sd_int_uidx'] = redir_live_urls_underidx.apply(sd_intlinkscount_underover, axis=1)
```

还有一些代码用于处理“未设置”的情况，这些实际上是被孤立的 URL。在此例中，我们将这些 URL 设置为 1——表示它们链接不足：

```python
redir_live_urls_underidx['sd_int_uidx'] = np.where(redir_live_urls_underidx['crawl_depth'] == 'Not Set', 1,
redir_live_urls_underidx['sd_int_uidx'])
redir_live_urls_underidx
```

结果如下：

![](img/537787_1_En_3_Figr_HTML.png)

一张表格截图，数据位于列标题 `url`、`crawl_depth`、`http_status_code`、`indexable`、`indexable_status`、`no_internal_links_to_url`、`title` 以及其他 3 个列下。

数据框显示，该列已到位，将内部链接不足的 URL 标记为 `1`。标记好 URL 后，我们就可以概览 URL 的链接不足情况，这可以通过按爬取深度聚合，并求和链接不足的 URL 总数来实现：

```python
intlinks_agged = redir_live_urls_underidx.groupby('crawl_depth').agg({'sd_int_uidx': ['sum', 'count']}).reset_index()
```

下面一行代码通过列表推导式插入下划线来整理列名：

```python
intlinks_agged.columns = ['_'.join(col) for col in intlinks_agged.columns.values]
intlinks_agged = intlinks_agged.rename(columns = {'crawl_depth_': 'crawl_depth'})
```

为了得到比例（或百分比），我们将总和除以计数并乘以 100：

```python
intlinks_agged['sd_uidx_prop'] = (intlinks_agged.sd_int_uidx_sum) / intlinks_agged.sd_int_uidx_count * 100
print(intlinks_agged)
```

结果如下：

```python
crawl_depth  sd_int_uidx_sum  sd_int_uidx_count  sd_uidx_prop
0            0                0                  1      0.000000
1            1               38                 66     57.575758
2            2               67                169     39.644970
3            3               75                280     26.785714
4            4               57                253     22.529644
5            5               31                201     15.422886
6            6                9                122      7.377049
7            7                9                 64     14.062500
8            8                3                 17     17.647059
9            9                2                  6     33.333333
10          10                0                  1      0.000000
11     Not Set             2303               2303    100.000000
```

因此，尽管第 1 层和第 2 层的内容链接数量多于任何较低层级，但它们的链接不足 URL 比例高于任何其他站点层级（当然，除了“未设置”中的孤立页面）。例如，首页正下方的页面中有 57% 存在链接不足的情况。

让我们进行可视化：

```python
# plot the table
depth_uidx_plt = (ggplot(intlinks_agged, aes(x = 'crawl_depth', y = 'sd_int_uidx_sum')) +
geom_bar(stat = 'identity', fill = 'blue', alpha = 0.8) +
labs(y = '# Under Linked URLs', x = 'Site Level') +
scale_y_log10() +
theme_classic() +
theme(legend_position = 'none')
)
depth_uidx_plt.save(filename = 'images/1_depth_uidx_plt.png', height=5, width=5, units = 'in', dpi=1000)
depth_uidx_plt
```

使用 `depth_uidx_plt` 进行可视化是很好的，因为我们还可以看到（图 3-7）第 2、3 和 4 层在数量上拥有最多的链接不足 URL。

![](img/537787_1_En_3_Fig7_HTML.png)

柱状图展示了从 0 到 10 以及“未设置”的站点层级中，链接不足 URL 的数量。 “未设置”范围包含超过 1000 个链接不足的 URL。 其他站点层级则少于 100 个链接不足的 URL。

**图 3-7** 按站点层级划分的内部链接不足 URL 数量的柱状图

让我们绘制 `intlinks_agged` 表格：

```python
depth_uidx_prop_plt = (ggplot(intlinks_agged, aes(x = 'crawl_depth', y = 'sd_uidx_prop')) +
geom_bar(stat = 'identity', fill = 'blue', alpha = 0.8) +
labs(y = '% URLs Under Linked', x = 'Site Level') +
theme_classic() +
theme(legend_position = 'none')
)
depth_uidx_prop_plt.save(filename = 'images/1_depth_uidx_prop_plt.png', height=5, width=5, units = 'in', dpi=1000)
depth_uidx_prop_plt
```

绘制 `depth_uidx_prop_plt`（图 3-8），我们看到尽管第 1 层的数量较低，但其比例却更高。 直观上，这表明有太多页面从首页链接，但链接分配不均。

![](img/537787_1_En_3_Fig8_HTML.png)

柱状图绘制了从 0 到 10 以及“未设置”的站点层级中，链接不足 URL 的百分比值。 在其他站点层级范围中，“未设置”范围的比例大约超过了 100%。

**图 3-8** 按站点层级划分的内部链接不足 URL 比例的柱状图

并非所有处于某个站点层级且链接不足的 URL 都一定是个问题，或者可能更多是设计使然。 然而，它们值得审查，因为也许它们本应处于该站点层级，或者毕竟它们确实应该获得更多的内部链接。

以下代码将链接不足的 URL 导出到一个 CSV 文件，该文件可以在 Microsoft Excel 中查看：

```python
underlinked_urls = redir_live_urls_underidx.loc[redir_live_urls_underidx.sd_int_uidx == 1]
underlinked_urls = underlinked_urls.sort_values(['crawl_depth', 'no_internal_links_to_url'])
underlinked_urls.to_csv('exports/underlinked_urls.csv')
```

### 按页面权威度划分

来自外部网站的入站链接是 PageRank（或者，如果我们保持搜索引擎中立的话，称为页面权威度）的来源。

鉴于并非所有页面都能获得入站链接，SEO 通常希望让没有反向链接的页面被更频繁地抓取。因此，分析并探索如何将这些 PageRank 重新分配给网站内其他页面是合理的。

我们首先将 AHREFs 数据附加到主数据框上，这样就能按页面权威度查看内部链接。

```python
intlinks_pageauth = redir_live_urls_underidx.merge(ahrefs_df, on = 'url', how = 'left')
intlinks_pageauth.head()
```

结果如下：

![](img/537787_1_En_3_Figs_HTML.jpg)

一张表格的截图，其列标题为：`url`、`crawl_depth`、`http_status_code`、`indexable_status`、`no_internal_links_to_url`、`title`、`log_internal_links`、`page_authority`和`referring_domains`。

现在我们在 URL 级别拥有了页面权威度和引荐域名。可以预见，首页拥有大量引荐域名（超过 3000 个），并且页面级权威度最高，达到 81。

像往常一样，我们将进行一些聚合操作，并探索 PageRank（可与页面权威度互换使用）的分布情况。

首先，我们清理数据，确保将空值替换为零：

```python
intlinks_pageauth['page_authority'] = np.where(intlinks_pageauth['page_authority'].isnull(),
0, intlinks_pageauth['page_authority'])
```

按页面权威度聚合：

```python
intlinks_pageauth.groupby('page_authority').agg({'no_internal_links_to_url': ['describe']})
```

结果如下：

![](img/537787_1_En_3_Figt_HTML.jpg)

一张表格的截图，展示了页面权威度、计数、平均值、`std`、最小值、25%、50%、75%和最大值的数据列表。表格顶部的文字为`no_internal_links_to_url`和`describe`。

上表展示了不同页面权威度级别下内部链接的分布情况。

在较低级别，大多数 URL 大约有两个内部链接。

一张图表将为我们呈现全貌：

```python
# distribution of page_authority
page_authority_dist_plt = (ggplot(intlinks_pageauth, aes(x = 'page_authority')) +
geom_histogram(fill = 'blue', alpha = 0.6, bins = 30 ) +
labs(y = '# URLs', x = 'Page Authority') +
#scale_y_log10() +
theme_classic() +
theme(legend_position = 'none')
)
page_authority_dist_plt.save(filename = 'images/2_page_authority_dist_plt.png',
height=5, width=5, units = 'in', dpi=1000)
page_authority_dist_plt
```

`page_authority_dist_plt`（图 3-9）所示的分布，在绘制原始数据时呈现严重的负偏态。网站中大多数 URL 的 PageRank 为 15，而权威度更高的 URL 数量急剧减少。大量 URL 没有权威度，因为它们成为了孤页。

![](img/537787_1_En_3_Fig9_HTML.png)

一张柱状图，绘制了 URL 数量（从 0 到 2000）与页面权威度（从 0 到 80）的关系。页面权威度为 0 时有 1300 个 URL，页面权威度在 12 左右时有 1900 个 URL。数值为近似值。

**图 3-9**  
按页面权威度划分的 URL 分布

使用对数刻度，我们可以比较更高权威度级别的情况：

```python
# distribution of page_authority
page_authority_dist_plt = (ggplot(intlinks_pageauth, aes(x = 'page_authority')) +
geom_histogram(fill = 'blue', alpha = 0.6, bins = 30 ) +
labs(y = '# URLs (Log)', x = 'Page Authority') +
scale_y_log10() +
theme_classic() +
theme(legend_position = 'none')
)
page_authority_dist_plt.save(filename = 'images/2_page_authority_dist_log_plt.png',
height=5, width=5, units = 'in', dpi=1000)
page_authority_dist_plt
```

突然间，`page_authority_dist_plt`（图 3-10）显示的视图变得更有趣了，因为权威度每增加一个单位，URL 数量就减少到之前的十分之一——这是一种相当严苛的 PageRank 分布。

![](img/537787_1_En_3_Fig10_HTML.png)

一张柱状图，以对数刻度绘制了 URL 数量与页面权威度的关系。图表趋势在页面权威度 0 到 40 之间是动态变化的。

**图 3-10**  
按对数刻度划分的 URL 分布图

鉴于这个更具洞察力的视图，对`page_authority`取对数以形成新的列变量`log_pa`是合理的：

```python
intlinks_pageauth['page_authority'] = np.where(intlinks_pageauth['page_authority'] == 0, .1, intlinks_pageauth['page_authority'])
intlinks_pageauth['log_pa'] = np.log2(intlinks_pageauth.page_authority)
intlinks_pageauth.head()
```

![](img/537787_1_En_3_Figu_HTML.jpg)

一张表格的截图，其列标题为：`url`、`crawl_depth`、`http_status_code`、`indexable_status`、`no_internal_links_to_url`、`title`、`log_internal_links`、`page_authority`、`referring_domains`和`log_pa`。

`log_pa`列已就位；让我们进行可视化：

```python
page_authority_trans_dist_plt = (ggplot(intlinks_pageauth, aes(x = 'log_pa')) +
geom_histogram(fill = 'blue', alpha = 0.6, bins = 30 ) +
labs(y = '# URLs (Log)', x = 'Log Page Authority') +
scale_y_log10() +
theme_classic() +
theme(legend_position = 'none')
)
page_authority_trans_dist_plt.save(filename = 'images/2_page_authority_trans_dist_plt.png',
height=5, width=5, units = 'in', dpi=1000)
page_authority_trans_dist_plt
```

如`page_authority_trans_dist_plt`（图 3-11）所示，取对数压缩了 PageRank 的范围，使其不那么极端，因为首页的`log_pa`值为 6，使其更接近网站的其他部分。

![](img/537787_1_En_3_Fig11_HTML.png)

一张柱状图，以对数刻度（从 1 到 1000）绘制了 URL 数量与对数页面权威度（从-4 到 6）的关系。URL 数量最初很高，超过 1000，从 3.7 到 5.6 的范围内，URL 数量从 1100 减少到 5。数值为估计值。

**图 3-11**  
按对数页面权威度划分的 URL 分布

小数位将被四舍五入，以便更容易地对 3000 多个 URL 进行分类：

```python
intlinks_pageauth['pa_band'] = intlinks_pageauth['log_pa'].apply(np.floor)
# display updated DataFrame
intlinks_pageauth
```

![](img/537787_1_En_3_Figv_HTML.jpg)

一张表格的截图，其列标题为：`crawl_depth`、`http_status_code`、`indexable`、`indexable_status`、`no_internal_links_to_url`、`title`、`log_internal_links`、`page_authority`、`referring_domains`、`log_pa`和`pa_band`。

### 内部链接不足的页面权威 URL

将 URL 按 PA（页面权威）等级分组后，我们需要检查它们的内链数量是否与其权威等级不匹配。我们将阈值设定为 40%，即任何 URL 在其 PA 等级下内链数量低于该阈值，即被视为“链接不足”。

在此阶段，选择 40%并非绝对重要，因为每个网站（甚至每个市场）情况各异。存在更科学的方法来确定最佳阈值，例如分析搜索空间中的顶级竞争对手；但现阶段，我们暂以 40%作为阈值。

```python
def quantile_lower(x):
return x.quantile(.4).round(0)
quantiled_pageau = intlinks_pageauth.groupby('pa_band').agg({'no_internal_links_to_url': [quantile_lower]}).reset_index()
quantiled_pageau.columns = ['_'.join(col) for col in quantiled_pageau.columns.values]
quantiled_pageau = quantiled_pageau.rename(columns = {'pa_band_': 'pa_band',
'no_internal_links_to_url_quantile_lower': 'pa_intlink_lowqua'})
quantiled_pageau
```

结果如下：

![](img/537787_1_En_3_Figw_HTML.jpg)

一张表格截图，列标题为`pa_band`和`pa_intlink_lowqua`，行条目为 0 到 4 的数字。

基于 PageRank，我们现在得到了预期的内链最低阈值。接下来合并数据，标记出那些在其权威等级下链接不足的 URL：

```python
intlinks_pageauth_underidx = intlinks_pageauth.merge(quantiled_pageau, on = 'pa_band', how = 'left')
def pa_intlinkscount_underover(row):
if row['pa_intlink_lowqua'] > row['no_internal_links_to_url']:
val = 1
else:
val = 0
return val
intlinks_pageauth_underidx['pa_int_uidx'] = intlinks_pageauth_underidx.apply(pa_intlinkscount_underover, axis=1)
```

此函数将允许我们进行聚合，以查看每个 PageRank 等级下有多少个 URL，以及其中有多少是链接不足的：

```python
pageauth_agged = intlinks_pageauth_underidx.groupby('pa_band').agg({'pa_int_uidx': ['sum', 'count']}).reset_index()
pageauth_agged.columns = ['_'.join(col) for col in pageauth_agged.columns.values]
pageauth_agged['uidx_prop'] = pageauth_agged.pa_int_uidx_sum / pageauth_agged.pa_int_uidx_count * 100
print(pageauth_agged)
```

结果如下：

```
pa_band_  pa_int_uidx_sum  pa_int_uidx_count  uidx_prop
0      -4.0                0               1320   0.000000
1       3.0                0               1950   0.000000
2       4.0               77                203  37.931034
3       5.0                4                  9  44.444444
4       6.0                0                  1   0.000000
```

大部分链接不足的内容似乎集中在页面权威最高的部分，这与网站层面的分析结论（即层级较低的页面链接不足）略有出入。这假设了大多数高权威页面更接近首页。

正确答案是什么？这取决于我们的目标。让我们继续深入分析，并将权威数据可视化：

```python
# distribution of page_authority
pageauth_agged_plt = (ggplot(intlinks_pageauth_underidx.loc[intlinks_pageauth_underidx['pa_int_uidx'] == 1],
aes(x = 'pa_band')) +
geom_histogram(fill = 'blue', alpha = 0.6, bins = 10) +
labs(y = '# URLs Under Linked', x = 'Page Authority Level') +
theme_classic() +
theme(legend_position = 'none')
)
pageauth_agged_plt.save(filename = 'images/2_pageauth_agged_hist.png',
height=5, width=5, units = 'in', dpi=1000)
pageauth_agged_plt
```

我们在`pageauth_agged_plt`（图 3-12）中看到，PageRank 等级 4 下有近 80 个 URL 链接不足，等级 5 下也有几个。不可否认，这是一个相当抽象的概念。

![](img/537787_1_En_3_Fig12_HTML.png)

一张柱状图，横轴为页面权威等级（3.9 至 5.1），纵轴为链接不足的 URL 数量（0 至 80）。最大值位于(4.0, 78)，最小值位于(5.0, 5)。数值为近似值。

**图 3-12** 按页面权威等级划分的链接不足 URL 分布

### 内容类型

或许通过“快速粗略”的分析，利用第一级子目录按内容类型进行可视化会更有用：

```python
intlinks_content_underidx = intlinks_depthauth_underidx.copy()
```

为了获取第一级子文件夹，我们将定义一个函数，以便在失败时（例如首页 URL 没有子文件夹）继续执行操作。`k`参数指定 URL 中用于定位目标文件夹并解析子目录名称的斜杠数量：

```python
def get_folder(fp, k=3):
try:
return os.path.split(fp)[0].split(os.sep)[k]
except:
return 'home'
intlinks_content_underidx['content'] = intlinks_content_underidx['url'].apply(lambda x: get_folder(x))
```

检查按子文件夹划分的链接分布：

```python
intlinks_content_underidx.groupby('content').agg({'no_internal_links_to_url': ['describe']})
```

结果如下：

![](img/537787_1_En_3_Figx_HTML.jpg)

一张表格截图，列出了`content`、`count`、`mean`、`std`、`min`、`25%`、`50%`、`75%`和`max`下的数据。表格顶部文字为`no_internal_links_to_url`和`describe`。该数据框或表格共有 183 行和 8 列。

哇，183 个子文件夹！这对于分类分析来说太多了。我们可以使用第 9 章描述的 n-gram 技术将其分解并聚合为更少的类别；欢迎尝试。

无论如何，网站架构似乎过于扁平，可以更好地结构化，使其更具层次性，即更像金字塔结构。

此外，许多内容文件夹只有一个入站内链，因此即使没有数据科学的帮助，也明显需要 SEO 关注。

## 结合站点层级与页面权威性

将站点层级与页面权威性结合起来进行可视化，或许会更有用？

```python
intlinks_depthauth_underidx = intlinks_pageauth_underidx.copy()
intlinks_depthauth_underidx['depthauth_uidx'] = np.where((intlinks_depthauth_underidx['sd_int_uidx'] +
intlinks_depthauth_underidx['pa_int_uidx'] == 2), 1, 0)
'''intlinks_depthauth_underidx['depthauth_uidx'] = np.where((intlinks_depthauth_underidx['sd_int_uidx'] == 1) &
(intlinks_depthauth_underidx['pa_int_uidx'] == 1), 1, 0)'''
depthauth_uidx = intlinks_depthauth_underidx.groupby(['crawl_depth', 'pa_band']).agg({'depthauth_uidx': 'sum'}).reset_index()
depthauth_urls = intlinks_depthauth_underidx.groupby(['crawl_depth', 'pa_band']).agg({'url': 'count'}).reset_index()
depthauth_stats = depthauth_uidx.merge(depthauth_urls,
on = ['crawl_depth', 'pa_band'], how = 'left')
depthauth_stats['depthauth_uidx_prop'] = (depthauth_stats['depthauth_uidx'] / depthauth_stats['url']).round(2)
depthauth_stats.sort_values('depthauth_uidx', ascending = False)
```

结果如下：

![](img/537787_1_En_3_Figy_HTML.jpg)

一张表格截图，其列标题分别为 `crawl_depth`、`pa_band`、`depthauth_uidx`、`url`、`depthauth_uidx_prop`。

大多数内链不足的 URL 都是孤立页面，并且具有页面权威性（可能来自反向链接）。

进行可视化以获取更全面的信息：

```python
depthauth_stats_plt = (
ggplot(depthauth_stats,
aes(x = 'pa_band', y = 'crawl_depth', fill = 'depthauth_uidx')) +
geom_tile(stat = 'identity', alpha = 0.6) +
labs(y = '', x = '') +
theme_classic() +
theme(legend_position = 'right')
)
depthauth_stats_plt.save(filename = 'images/3_depthauth_stats_plt.png',
height=5, width=10, units = 'in', dpi=1000)
depthauth_stats_plt
```

至此，我们得到了 `depthauth_stats_plt`（图 3-13），它显示大部分注意力应集中在孤立 URL 上（这本来就是应该做的），但更重要的是，我们知道了哪些孤立 URL 需要优先处理。

![](img/537787_1_En_3_Fig13_HTML.jpg)

一张关于页面权威性级别、站点层级和内链不足 URL 的热力图，其数值大多较低。`depthauth_uidx` 的渐变标尺范围从 0 到 40。

**图 3-13** 页面权威性级别、站点层级与内链不足 URL 的热力图

我们还可以看到问题的严重程度。第二高优先级的索引不足 URL 组位于站点层级 2、3 和 4。

## 锚文本

如果说数量及其分布代表了内部链接的量化方面，那么锚文本则可以代表其质量。

锚文本向搜索引擎和用户提示，点击超链接后会看到什么内容。这使得锚文本成为一个重要的信号，值得优化。

我们首先汇总来自 Sitebulb 的爬取数据，以了解问题概况：

```python
anchor_issues_agg = crawl_data.agg({'no_anchors_with_empty_href': ['sum'],
'no_anchors_with_leading_or_trailing_whitespace_in_href': ['sum'],
'no_anchors_with_local_file': ['sum'],
'no_anchors_with_localhost': ['sum'],
'no_anchors_with_malformed_href': ['sum'],
'no_anchors_with_no_text': ['sum'],
'no_anchors_with_non_descriptive_text': ['sum'],
'no_anchors_with_non-http_protocol_in_href': ['sum'],
'no_anchors_with_url_in_onclick': ['sum'],
'no_anchors_with_username_and_password_in_href': ['sum'],
'no_image_anchors_with_no_alt_text': ['sum']
}).reset_index()
anchor_issues_agg = pd.melt(anchor_issues_agg, var_name=['issues'],
value_vars=['no_anchors_with_empty_href',
'no_anchors_with_leading_or_trailing_whitespace_in_href',
'no_anchors_with_local_file','no_anchors_with_localhost',
'no_anchors_with_malformed_href', 'no_anchors_with_no_text',
'no_anchors_with_non_descriptive_text',
'no_anchors_with_non-http_protocol_in_href',
'no_anchors_with_url_in_onclick',
'no_anchors_with_username_and_password_in_href',
'no_image_anchors_with_no_alt_text'],
value_name='instances'
)
anchor_issues_agg
```

结果如下：

![](img/537787_1_En_3_Figz_HTML.jpg)

一张表格截图，其列标题分别为 `issues` 和 `instances`。行条目为 0 到 10 的数字。

超过 4000 个链接没有描述性锚文本，这是最常见的问题，更不用说还有 19 个锚点带有空的 `HREF`（尽管数量非常少）。

进行可视化：

```python
anchor_issues_count_plt = (ggplot(anchor_issues_agg, aes(x = 'reorder(issues, instances)', y = 'instances')) +
geom_bar(stat = 'identity', fill = 'blue', alpha = 0.6) +
labs(y = '# instances of Anchor Text Issues', x = '') +
theme_classic() +
coord_flip() +
theme(legend_position = 'none')
)
anchor_issues_count_plt.save(filename = 'images/4_anchor_issues_count_plt.png',
height=5, width=5, units = 'in', dpi=1000)
anchor_issues_count_plt
```

`anchor_issues_count_plt`（图 3-14）直观地确认了具有非描述性锚文本的内部链接数量。

![](img/537787_1_En_3_Fig14_HTML.jpg)

一张水平条形图，展示了锚文本问题的实例数量。`no_anchors_with_non_descriptive_text` 的范围为 4000，其次是 `no_anchors_with_leading_or_trailing_whitespace_in_href`，为 3700。数值为近似值。

**图 3-14** 锚文本问题条形图

### 按站点层级划分的锚点问题

我们将按站点层级深入分析前面的示例，以便更清晰地了解问题所在：

```python
anchor_issues_levels = crawl_data.groupby('crawl_depth').agg({'no_anchors_with_empty_href': ['sum'],
'no_anchors_with_leading_or_trailing_whitespace_in_href': ['sum'],
'no_anchors_with_local_file': ['sum'],
'no_anchors_with_localhost': ['sum'],
'no_anchors_with_malformed_href': ['sum'],
'no_anchors_with_no_text': ['sum'],
'no_anchors_with_non_descriptive_text': ['sum'],
'no_anchors_with_non-http_protocol_in_href': ['sum'],
'no_anchors_with_url_in_onclick': ['sum'],
'no_anchors_with_username_and_password_in_href': ['sum'],
'no_image_anchors_with_no_alt_text': ['sum']
}).reset_index()
anchor_issues_levels.columns = ['_'.join(col) for col in anchor_issues_levels.columns.values]
anchor_issues_levels.columns = [str.replace(col, '_sum', '') for col in anchor_issues_levels.columns.values]
anchor_issues_levels.columns = [str.replace(col, 'no_anchors_with_', '') for col in anchor_issues_levels.columns.values]
anchor_issues_levels = anchor_issues_levels.rename(columns = {'crawl_depth_': 'crawl_depth'})
anchor_issues_levels = pd.melt(anchor_issues_levels, id_vars=['crawl_depth'], var_name=['issues'],
value_vars=['empty_href',
'leading_or_trailing_whitespace_in_href',
'local_file','localhost',
'malformed_href', 'no_text',
'non_descriptive_text',
'non-http_protocol_in_href',
'url_in_onclick',
'username_and_password_in_href',
'no_image_anchors_with_no_alt_text'],
value_name='instances'
)
print(anchor_issues_levels)
```

结果如下：

```
crawl_depth                                  issues  instances
111     Not Set                    non_descriptive_text       2458
31      Not Set  leading_or_trailing_whitespace_in_href       2295
104           3                    non_descriptive_text        350
24            3  leading_or_trailing_whitespace_in_href        328
105           4                    non_descriptive_text        307
..          ...                                     ...        ...
85           13                                 no_text          0
84           12                                 no_text          0
83           11                                 no_text          0
82           10                                 no_text          0
0             0                              empty_href          0
[176 rows x 3 columns]
```

大多数问题出现在孤立页面上，其次是深度为三到四层的 URL。

可视化效果如下：

```python
anchor_levels_issues_count_plt = (ggplot(anchor_issues_levels, aes(x = 'crawl_depth',
y = 'issues', fill = 'instances')) +
geom_tile() +
labs(y = '# instances of Anchor Text Issues', x = '') +
scale_fill_cmap(cmap_name='viridis') +
theme_classic()
)
anchor_levels_issues_count_plt.save(filename = 'images/4_anchor_levels_issues_count_plt.png',
height=5, width=5, units = 'in', dpi=1000)
anchor_levels_issues_count_plt
```

`anchor_levels_issues_count_plt` 图表（图 3-15）使问题更加清晰：锚文本的技术问题主要出现在孤立页面上。

![](img/537787_1_En_3_Fig15_HTML.jpg)

热力图绘制了锚文本问题实例数量与站点层级（从 0 到 9，以及未设置）的对应关系。实例数量的渐变标尺范围为 0 到 2000。未设置层级在非描述性文本和 `href` 中前导或尾随空格方面拥有最多的实例。

**图 3-15** 站点层级、锚文本问题及实例的热力图

## 锚文本相关性

当然，这并不是 SEO 人员对锚文本唯一感兴趣的方面。SEO 人员还想了解锚文本与目标 URL 之间的相关程度。

为此，我们将对 Sitebulb 链接报告使用字符串匹配技术来衡量这种相关性，然后进行汇总以查看整体情况：

```python
link_df = link_data[['target_url', 'referring_url', 'anchor_text', 'location']]
link_df = link_df.rename(columns = {'target_url':'url'})
```

使用 `url` 作为主键与爬取数据合并，然后仅筛选出可索引的 URL：

```python
anchor_merge = crawl_data.merge(link_df, on = 'url', how = 'left')
anchor_merge = anchor_merge.loc[anchor_merge['host'] == website]
anchor_merge = anchor_merge.loc[anchor_merge['indexable'] == 'Yes']
anchor_merge['crawl_depth'] = anchor_merge['crawl_depth'].astype('category')
anchor_merge['crawl_depth'] = anchor_merge['crawl_depth'].cat.reorder_categories(['0', '1', '2', '3', '4',
'5', '6', '7', '8', '9',
'10', 'Not Set'])
```

然后，我们比较锚文本与目标 URL 标题标签的字符串相似度：

```python
anchor_merge['anchor_relevance'] = anchor_merge.loc[:, ['title',
'anchor_text']].apply(lambda x: sorensen_dice(*x), axis=1)
```

任何相关性得分低于 70% 的 URL 将在新列 `irrel_anchors` 中被标记为不相关，值为 1。

为什么是 70%？这是基于经验得出的，您完全可以尝试不同的阈值。

使用 Sorensen-Dice 算法，它不仅速度快，而且能满足 SEO 衡量相关性的需求。70% 似乎是相关与不相关之间的合理界限，尤其是在考虑标题标签字符串中的站点标识时：

```python
anchor_merge['irrel_anchors'] = np.where(anchor_merge['anchor_relevance'] < .7, 1, 0)
```

使用单一因子可以更轻松地按列聚合整个数据框，尽管还有其他方法可以实现：

```python
anchor_merge['project'] = target_name
anchor_merge
```

结果如下：

![](img/537787_1_En_3_Figaa_HTML.jpg)

一张表格的截图显示了列标题 `rect_url`、`redirect_url_status`、`redirect_url_status_code`、`referring_url`、`anchor_text`、`anchor_relevance`、`irrelevant_anchors` 和 `project` 下的数据。

由于引用页面和目标 URL 之间存在多对多关系（即一个目标 URL 可以接收来自多个 URL 的链接，而前者也可以链接到多个 URL），数据框已从 8611 行扩展到超过 350,000 行。

让我们通过统计每个引用 URL 的 URL 数量来进行汇总：

```python
anchor_rel_stats_site_agg = anchor_merge.groupby('project').agg({'irrel_anchors': 'sum'}).reset_index()
anchor_rel_stats_site_agg['total_urls'] = anchor_merge.shape[0]
anchor_rel_stats_site_agg['irrel_anchors_prop'] = anchor_rel_stats_site_agg['irrel_anchors'] /anchor_rel_stats_site_agg['total_urls']
print(anchor_rel_stats_site_agg)
project  irrel_anchors  total_urls  irrel_anchors_prop
0    ON24         333946      350643            0.952382
```

该站点约 95% 的锚文本是不相关的。这与他们的竞争对手相比如何？这是留给您的作业。

让我们再深入一点，按站点深度分析这个问题：

```python
anchor_rel_depth_irrels = anchor_merge.groupby(['crawl_depth']).agg({'irrel_anchors': 'sum'}).reset_index()
anchor_rel_depth_urls = anchor_merge.groupby(['crawl_depth']).agg({'project': 'count'}).reset_index()
anchor_rel_depth_stats = anchor_rel_depth_irrels.merge(anchor_rel_depth_urls, on = 'crawl_depth', how = 'left')
anchor_rel_depth_stats['irrel_anchors_prop'] = anchor_rel_depth_stats['irrel_anchors'] / anchor_rel_depth_stats['project']
anchor_rel_depth_stats
```

结果如下：

![](img/537787_1_En_3_Figab_HTML.jpg)