# 11. SEO 的未来

将数据科学方法应用于 SEO 的探索，最终会引发关于 SEO 作为一个行业和职业演变的进一步问题。随着付费搜索日益自动化和商品化，SEO 是否也会发生同样的情况？为了回答这个问题，让我们回顾一下本书中数据科学如何应用于 SEO，以及自动化的局限性和机遇。

### 聚合

`聚合`是一种数据分析技术，将数据按组级别汇总，例如按内容组统计平均点击次数。在 Microsoft Excel 中，这可以通过数据透视表实现。`聚合`是可以且应该通过自动化实现的技术。`聚合`已被用于帮助我们理解 SEO 中众多不同的领域（本书已涵盖）。借助提供的代码，这可以集成到基于云的数据管道中，用于 SEO 应用，以加载数据仓库并驱动仪表板。对于大多数用例，`聚合`已经足够。然而，对于数据的科学探索和假设检验，我们需要考虑数据的分布、变异及其他统计特性。

用于报告和 SEO 咨询中许多分析方面的`聚合`当然可以自动化，并在云端执行。鉴于 Apache 已有的数据管道技术，这相当容易实现。

### 分布

统计分布之所以强大，是因为我们能够理解什么是正常情况，从而识别出任何显著高于或低于性能的数据。我们利用分布来发现增长关键词，即 Google Search Console (GSC) 中展示次数超过其排名位置第 95 百分位数的关键词。我们还利用分布来识别网站架构和层级中缺乏内部链接的内容。

这也可以在云端实现自动化，从而可能催生为 SEO 行业发布的应用，用于自动识别内部链接机会。当然，存在一个轻微的风险，即所有内部链接都纯粹基于分布进行，而未考虑并非针对搜索渠道的内容，这需要成为软件设计的一部分。
```


### 字符串匹配

使用 Sorensen-Dice 算法的字符串匹配已被应用于帮助将内容与 URL 进行匹配，适用于关键词映射、迁移规划等用例。由于该算法相对快速且扩展性良好，结果尚可，但它首先依赖于描述性的 URL 和标题标签。它还依赖于预处理，例如在应用字符串匹配之前移除 URL 的网站域名部分，这很容易自动化。较难处理的是，需要人为判断标题和 URL 别名在多大程度上相似才算良好匹配。阈值应该设为 70%、83% 还是 92%？

这不太容易，并且可能需要某种形式的 AI 进行自我学习，更具体地说，是循环神经网络（RNN）。当然，这并非不可能，因为你需要确定一个衡量结果好坏的指标，才知道如何训练模型。此外，你至少需要一百万个数据点来训练模型。

关键词映射的一个关键问题是：“对于搜索 X 关键词的用户来说，哪个 URL 是最佳的衡量指标是什么？”RNN 在这里可能很有用，因为它可以从排名靠前的 SERP 内容中学习，将其简化为一个对象，然后将网站内容与该对象进行比较，从而将关键词映射到 URL。

对于将已失效的 URL（带有反向链接）重定向到返回 200 HTTP 响应的有效 URL，这可能更直接，不需要 AI。你可以使用基于决策树的算法，利用用户行为来确定最佳的替换 URL，也就是说，在可用的其他 URL 选项中，“URL A”上的用户总是会前往 URL X。

非 AI 解决方案不依赖于数百万的 SERP 或互联网数据，因此在内部构建成本（相对）较低。另一方面，基于 AI 的解决方案很可能要么作为 SaaS 构建，要么由像亚马逊、Quora 或 Tripadvisor 这样依赖自然流量的超大型企业品牌构建。

### 聚类

在本书中，聚类被用于通过大规模比较搜索结果来确定搜索意图。其原理基于比较数据点之间的距离，当距离相对较小时，就存在一个聚类。本书未采用词干提取，因为尽管它速度快，但缺乏所需的精度。

聚类不仅有助于理解关键词，还有助于对关键词进行分组以报告性能，以及对内容进行分组以设置网站层级结构。作为 SEO 专家，你的想象力就是极限。SEO 市场中已经存在通过比较搜索结果来根据搜索意图对关键词进行聚类的应用，因此 Artios、Keyword Insights、Keyword Cupid、SEO Scout 等公司已经可以在云端实现自动化。

### 机器学习建模

试图理解自然搜索中的运作方式是 SEO 的诸多乐趣和考验之一，尤其是在当前谷歌更新的时代。机器学习辅助建模在这方面有所帮助，因为你实际上是将数据输入到机器学习算法中，它会显示出算法更新背后最具影响力的因素。

机器学习模型当然可以部署到云端，作为自动化应用的一部分，用于突出显示最具影响力的 SERP 因素和反向链接的质量，作为更广泛的仪表盘智能系统或独立应用的一部分。

由于不需要神经网络，构建和部署的成本相对较低，这使得 SEO 专业人员能够专注于理解模型输出以及如何应用它们。

### 集合论

集合论是指我们比较数据集（可以理解为列表），例如关键词，并将它们与另一个集合进行比较。这可以用来查看两个数据集之间的差异。它被用于内容差距分析，以找到共同的关键词（即两个网站关键词的交集），并找到目标网站与核心关键词集之间的差距。

这相当直接，并且可以使用 SEMRush 和 AHREFs 等工具轻松实现自动化。那么为什么还要用 Python 来做呢？因为它是免费的，并且让你对所需的交集程度有更多控制。

确定完美的交集程度则不那么明确，因为这需要研究和开发来确定所需的交集程度，而这又取决于被取交集网站的数量。

然而，关键在于首先要知道将哪些竞争对手纳入集合中，这对于机器来说可能不太容易辨别。

### 计算机能做什么和不能做什么

从上述内容中，我们看到了一个共同模式，即当涉及到诸如响应统计属性或基于数值模式做出决策等直接任务时，计算机表现出色（尽管需要一些数据清理和准备工作）。

当问题有些模糊且需要解释（例如语言）时，计算机仍然可以应对挑战，但这需要大量的资源来获取数据和训练 AI 模型。

### 给 SEO 专家的话

我们应该学习用于数据分析的 Python，因为这是我们生成见解和相应建议的方式。不利用技术和数据科学思维来解决 SEO 问题是没有道理的，尤其是当 SERP 由一个一致（因此更可预测）的算法决定时。

学习 Python 和数据科学不仅有助于让你的 SEO 职业生涯面向未来，还能让你更深入地理解搜索引擎的工作原理（鉴于它们是数学化的），并使你能够投入更多的时间和精力来定义要测试的假设并创建 SEO 解决方案。花更少的时间收集和分析数据，花更多的时间响应数据，这才是当下的要务。

在指定云应用（无论是报告、自动化还是其他方面）时，你也能更好地与软件工程师合作。

当然，创造力源于知识，因此你对 SEO 了解得越多，你就越能向数据提出更好的问题，从而产生更好的见解和更有针对性的建议。

## 总结

在本章中，我们从非常高的层面整合了用于使 SEO 数据驱动化的思路和技术：

*   聚合
*   分布
*   字符串匹配
*   聚类
*   机器学习建模
*   集合论

我们还探讨了计算机能做什么和不能做什么，并提醒了 SEO 专家为何应该转向数据科学。

为 SEO 的未来干杯。

# 索引

## A

*   A/A 测试
*   `aa_means` 数据框
*   `aa_model.summary()`
*   `aa_test_box_plt`
*   `dataframe`
*   数据结构
*   日期范围
*   组的分布
*   直方图
*   `.merge()` 函数
*   `NegativeBinomial()` 模型
*   优化
*   测试前和测试期间组
*   p 值
*   SearchPilot
*   西格玛
*   统计模型
*   统计属性
*   `.summary()` 属性
*   测试期间
*   加速移动页面 (AMP)
*   基于客户的营销
*   附加功能
*   Adobe Analytics
*   聚合
*   AHREFs
*   赤池信息量准则 (AIC)
*   替代方法
*   亚马逊云服务 (AWS)
*   `anchor_levels_issues_count_plt` 图表
*   `anchor_rel_stats_site_agg_plt` 图
*   锚文本
*   `anchor_issues_count_plt`
*   HREF
*   按站点级别的问题
*   非描述性
*   搜索引擎和用户
*   Sitebulb
*   锚文本词汇
*   Andreas
*   反垃圾算法
*   API 库
*   API 输出
*   API 响应
*   `Append()` 函数
*   `apply_pcn` 函数
*   `astype()` 函数
*   增强迪基-福勒检验 (ADF)
*   权威性
    *   聚合
    *   反向链接
    *   数据与清洗
    *   数据特征
    *   数据框
    *   描述性统计
    *   分布
    *   域名评级
    *   域名
    *   链接
    *   数学方法
    *   排名
    *   搜索引擎
    *   SEO 难度
    *   SEO 行业
    *   电子表格
*   权威性预优化
*   权威性评分
*   自动化



# averageSessionDuration

## B

- 反向链接域名
- 贝叶斯信息准则 (BIC)
- 米色风衣
- 网络研讨会最佳实践
- BlackHatWorld 论坛
- 箱线图分布

## C

- 关键词自相蚕食
- 被蚕食的搜索结果页
- 通用与品牌混合关键词
- 关键词
- 分类分析
- 变点分析
- 子节点
- 子 URL 节点文件夹
- Chrome 开发者工具
- 点击率 (CTR)
- 云计算服务
- 云网络爬虫
- `CLS_cwv_landscape_plt`
- 聚类标题
- 聚类
- 聚类
- 列重新分配
- 结合网站级别与页面权威性
- 孤立 URL
- 索引不足的 URL
- 链接不足的 URL
- 比较平均值与变异
- 竞争市场
- 竞争对手分析
- AHREFs 文件
- 缓存年龄
- 竞争力
- `concat()` 函数
- `crawl_path`
- `dataframe`
- 衍生新特征
- 全域特征
- `groupby()` 函数
- 关键词
- 线性回归
- 机器学习
- `merge()` 函数
- 排名与搜索结果
- 排名检查工具
- 排名
- 排名因素
- 排名页面
- 稳健分析
- 搜索引擎
- SEO 分析
- SERPs 数据
- 字符串格式列
- 标签品牌化比例
- 跟踪代码
- 变量
- 可见性指标
- 零错误
- `Competitor_count_cumsum_plt` 图
- 竞争对手
- 计算广告学
- 内容
- 内容整合
- 内容创作
- 数据源
- 关键词映射
- 用户查询
- 内容创作（规划着陆页内容）
- 清理与选择标题
- 聚类标题
- 爬取
- 提取标题
- 主机名
- 反思
- SERP 数据
- 顶级域名
- URL
- 垂直领域
- 内容差距分析
- `combinations` 函数
- 内容差距
- 内容交集
- 核心内容集
- `dataframe`
- 获取数据
- 列表与集合函数
- 映射客户需求
- 搜索引擎
- SEMRush 文件
- SEMRush 网站
- 内容交集
- 内容管理系统 (CMS)
- 核心网页指标 (CWV)
- Google 倡议
- 倡议
- 格局
- 站内 CWV
- 技术性 SEO
- 网络开发
- 爬取数据
- 爬取深度
- 爬取软件
- 爬取工具
- 创建有效的网络研讨会
- 累积平均反向链接流量
- 累积布局偏移 (CLS)
- 累积和
- 自定义函数
- CWV 指标值
- CWV 分数

## D

- 仪表盘
- 数据源
- ETL
- 参见 提取、转换与加载 (ETL)
- SEO
- 类型，数据源
- 数据驱动方法
- CWV
- 内部链接优化
- 参见 内部链接优化
- 建模页面权威性
- 数据驱动的关键词研究
- 数据驱动的 SEO
- DataForSEO SERPs API
- 数据框
- 迁移后数据
- 数据科学
- 可自动化
- 成本低
- 数据丰富
- 数据源
- 数据可视化
- 数据仓库
- 基于决策树的算法
- 去重
- 对列表去重
- 定义 ABM
- `depthauth_stats_plt`
- `Describe()` 函数
- 目标 URL
- `df.info()`
- `diag_conds`
- 诊断
- Distilled ODN
- 分布
- DNA 测序
- 文档
- 域名权威性
- 域名评级 (DR)
- 域名
- 创建新列
- 设备搜索结果类型
- HubSpot
- 排名
- 覆盖范围
- 分层覆盖范围
- 重命名列
- 按阶段作为参数分隔面板
- 可见性
- 加权平均搜索量
- WorkCast
- `drop_col` 函数

## E

- 消除缺失值
- 实验
- `ab_assign_box_plt`
- `ab_assign_log_box_plt`
- `ab_assign_plt`
- `ab_group`
- A/B 组
- `ab_model.summary()`
- A/B 测试
- 分析数据
- 数组
- `dataframe`
- 数据集
- 分布，测试组
- 直方图
- 假设
- 结果
- `pd.concat` 数据框
- p 值
- `simul_abgroup_trend.head()`
- `simul_abgroup_trend_plt`
- `test_analytics_expanded`
- 测试组与对照组
- 测试组与对照组
- 测试与对照会话
- 网站分析软件
- 实验设计
- A/A 测试
- 参见 A/A 测试
- 实际拆分测试
- API
- `dataframe`
- 数据类型
- 会话分布
- Pandas 数据框
- 样本量
- 基本原则
- `dataframe`
- 因子
- 统计显著性水平
- 显著性水平
- 最小 URL 数
- 参数
- `python_rzip` 函数
- `run_simulations`
- SEO 实验
- `split_ab_dev` 数据框
- 测试组与对照组
- `testing_days`
- 测试着陆页
- `urls_control` 数据框
- 标准差 (sd) 值
- `to_datetime()` 函数
- 网站分析数据
- 网站分析包
- 零膨胀
- 提取、转换与加载 (ETL)
- 提取过程
- DataForSEO SERPs API
- Google Analytics (GA)
- Google Search Console (GSC)
- PageSpeed API
- 加载数据
- 转换数据

## F

- `facet_wrap()` 函数
- `FCP_cwv_landscape_plt`
- `FID_cwv_landscape_plt`
- 金融证券
- 首次内容绘制 (FCP)
- 首次输入延迟 (FID)
- 预测
- 客户提案与报告
- 分解
- 探索你的数据
- 未来
- 模型测试
- SARIMA
- SEO 的未来
- 聚合
- 聚类
- 分布



# 排版后的文本

## A

`machine learning (ML)`建模

`SEO`专家

`set theory`

`string matching`

## G

`geom_bar()`函数

`Geom_histogram`函数

`get_api_result`

`getSTAT`数据

`Google`

`Google`算法更新

`cannibalization`

`dataset`

`dedupe`

`domains`

`SeeDomains`

`getstat_after`

`getSTAT`数据

导入`SERPs`数据，`getSTAT`

`keywords`

`token length`

`token length deep dive`

`np.select()`函数

`ON24`

`result types`

`segments`

`np.select()`函数

`snippets`

`top competitors`

`visibility`

`strip_subdomains`

`target level`

`keywords`

`pages`

`urisplit`函数

`zero search volumes`

`Google Analytics (GA)`

和`GSC URLs`

`tabular exports`

`URLs`

`version 4`

`Google Cloud Platform (GCP)`

`Google Data Studio (GDS)`

`Google PageSpeed API`

`Google rank`

`Google Search Console (GSC)`

`activation`

`data`

`data explore`

`filter and export to CSV`

`import, clean, and arrange the data`

`position data into whole numbers`

`search queries`

`segment average and variation`

`segment by query type`

`Google`的知识

`Google Trends`

`multiple keywords`

`ps4`和`ps5`

`Python`

`single keywords`

`time series data`

`visualizing`

`GoToMeeting`

`Groupby aggregation`函数

`groupby()`函数

`gsc_ba_diag`

`GSC traffic data`

## H

`Heading`

`Heatmap`

`Hindering search engines`

`HTTP protocol`

`HubSpot`

`Hypothesis generation`

`competitor analysis`

`conference events`

`industry peers`

`past experiment failures`

`recent website updates`

`SEO performance`

`social media`

`team`的想法

`website articles`

## I, J

`ia_current_mapping`

`Inbound internal links`

`Inbound links`

`Indexable URLs`

`Individual CWV metrics`

`Inexact (data) science of SEO`

`channel`的 diminishing value

`high costs`

`lacking sample data`

`making ads look`

`noisy feedback loop`

`things can’t be measured`

`Internal link optimization`

`anchor text relevance`

`Anchor texts`

`content type`

`crawl dataframe`

`external inbound link data`

`hyperlinked URL`的

`inbound links`

`link dataframe`

`by page authority`

`probability`

`Sitebulb`

`Sitebulb auditing software`

`by site level`

`URLs`

`URLs with backlinks`

`website optimization`

`Internal links distribution`

`intlink_dist_plt`图

`Irrel_anchors`

`Irrelevant anchors`

`Irrelevant anchor texts`

## K

`keysv_df`

`Keyword mapping`

`approaches`

`definition`

`string matching`

`Keyword research`

`data-driven methods`

`data sources`

`forecasts`

`Google Search Console (GSC)`

`Google Trends`

`search intent`

`SERP competitors`

`Keywords`

`token length`

`token length deep dive`

`Keywords_dict`

## L

`LCP_cwv_landscape_plt`图

`Levenshtein distance`

`Life insurance`

`Linear models`

`Link acquisition program`

`Link capital`

`Link capital velocity`

`Link checkers`

`Link quality`

`Link velocity`

`Link volumes`

`Listdir()`函数

`Live Webcast Elite`

`Live webinar`

`Logarithmic scale`

`Logarized internal links`

`log_intlinks`

`log_pa`

`Log page authority`

`Long short-term memory (LSTM)`

`Looker Studio bar chart`

`Looker Studio graph`

## M

`Machine learning (ML)`

`Management`

`Management content`

`Many-to-many relationship`

`Marketing channels`

`The mean`

`Median`

`Medium`

`melt()`函数

`Mens jeans`

`Metrics`

`Migration forensics`

`analysis impact`

`diagnostics`

`segmented time trends`

`segmenting URLs`

`time trends and change point analysis`

`traffic trend`

`Migration mapping`

`Migration planning`

`Migration URLs`

`MinMaxScaler()`

`ML algorithm`

`ML model`

`ML modeling`

`ML processes`

`ML software library`

`Modeling page authority`

`approach`

`calculating new distribution`

`dataframe`

`examining authority distribution`

`filters`

`Sitebulb desktop crawler`

`Modeling SERPs`

`Multicollinearity`

`Multiple audit measurements`

## N

`Natural language processing (NLP)`

`Near identical code`

`Near Zero Variance (NZVs)`

`API`

`highvar_variables`

`scaled_images`列

`search query`

`title_relevance`

`new_branch`

`Non-CWV factors`

`Nonindexable URLs`

`Nonnumeric columns`

`np.select()`函数

## O

`old_branch`

`ON24`

`One hot encoding (OHE)`

`Online webinars`

`Onsite indexable URLs`

`Open source data science tools`

`Organic results`

`Orphaned URLs`

`ove_intlink_dist_plt`

## P

`pageauth_newdist_plt`

`page_authority_dist_plt`

`Page authority level`

`page_authority_trans_dist_plt`

`PageRank`

`PageSpeed API`

`PageSpeed data`

`Paid search ads`

`Pandas dataframe`

`parent_child_map` dataframe

`parent_child_nodes`

`Parent URL node folders`

`Pattern identification`

`PCMag`

`perf_crawl`

`perf_diags`

`perf_recs` dataframe

`Phase`

`Plot impressions vs. rank_bracket`

`plot intlink_dist_plt`

`Power network`

`PS4`

`PS5`

`Python`

`Python code`

## Q

`Quantile`

`Query data vs. expected average`

“Quick and dirty” analysis

## R

`Random forest`

`Rank checking tool`

`Ranking factors`

`Ranking position`

`Rankings`

`RankScience`

`Rank tracking costs`

`Reach`

`Reallocation authority`

`Recurrent neural network (RNN)`

`Referring domains`

`Referring URL`

`Repetitive work`

`Root Mean Squared Error (RMSE)`

`r-squared`

## S

`Salesforce webinars`

`SARIMA`

`Screaming Frog`

`Search engine`

`Search engine optimization (SEO)`

`Search Engine Results Pages (SERPs)`

`Search intent`

`convert SERPs URL into string`

`core updates`

`DataForSEO`的`SERP API`

`keyword content mapping`

`Ladies trench coats`

`Life insurance`

`paid search ads`

`queries`

`rank tracking costs`

`SERPs comparison`

`Split-Apply-Combine (SAC)`

`Trench coats`

`Search query`

`Search volume`

`Segment`

`SEMRush`

`semrush_csvs`

`SEMRush domain`

`SEMRush files`

`SEMRush visibility`

`SEO benefits`

`SEO campaigns and operations`

`SEO manager`

`SEO rank checking tool`

`SERP competitors`

`extract keywords from page title`

`filter and clean data`

`SEMRush`

`SERPs data`

`SERP dataframe`

`SERP results`

`SERPs comparison`

`SERPs data`

`SERPs model`

`Serps_raw` dataframe

`set_post_data`

`Set theory`

`Single-level factor (SLFs)`

`dataset`

`parameterized URLs`

`ranking URL titles`

`SIS_cwv_landscape_plt`

`Site architecture`

`Sitebulb crawl data`

`Site depth`

`Site migration`

`Snippets`

`Sorensen-Dice`

`speed_ex_plt`图表

`Speed Index Score (SIS)`

`Speed score`

`Split A/B test`

`Split heading`

`Standard deviations`

`Statistical distribution`

`Statistically robust`

`stop_doms`列表

`String matching`

`cross-product merge`

`dataframe`

`DNA sequencing`

`groupby()`函数

`libraries`

`np.where()`

`simi`列

`Sitebulb`

`Sorensen-Dice`

`sorensen_dice`函数

`string distance`

`to URLs`

`values`

`String matching`

`String methods`

`String similarity`

`Structured Query Language (SQL)`

## T

`target_CLS_plt`

`target_crawl_unmigrated`

`target_FCP_plt`

`target_FID_plt`

`target_LCP_plt`

`target_speedDist_plt`图

`Technical SEO`

`data-driven approach`

`SeeData-driven approach`

`search engines and websites interaction`

`Tech SEO diagnosis`

`TF-IDF`

`Think vlookup/index match`

`Time series data`

`TLD extract package`

`Token length`

`Token size`

`Top-level domain (TLD)`

`Touched Interiors`

`Traffic post migration`

`Traffic/ranking changes`

`parent and child nodes`

`separate migration documents`

`site levels`

`site taxonomy/hierarchy`

`Travel nodes`

`Two-step aggregation approach`

## U

`Underindexed URLs`

`Underlinked page authority URLs`

`optimal threshold`

`pageauth_agged_plt`

`PageRank`

`site-level approach`

`Underlinked site-level URLs`

`average internal links`

`code exports`

`depth_uidx_plt`

`depth_uidx_prop_plt`

`intlinks_agged`表

`list comprehension`

`lower levels`

`orphaned URLs`

`percentile number`

`place marking`

`quantiles`

`Upper quantile`

`Urisplit()`函数

`URL by site level`

`URL Rating`

`452 URLs`

`URLs by site level`

`URL strings`

`URL structure`

`URL visibility`

`User experience (UX)`

`User query`

## V

`Variance inflation factor (VIF)`

`Visibility`

`Visualization`

## W

`wavg_rank`

`wavg_rank_imps`

`wavg_rank_sv()`函数

`WAVG search volume`

`Webinar`

`Webinar best practices`

`Webinar events`

`Webmaster tools`

`Webmaster World`

`Website analytics`

`Website analytics data`

`Winning benchmark`

`Wordcloud`函数

`WorkCast`

## X, Y

`xbox series x`

## Z

`Zero inflation`

`Zero string similarity`
