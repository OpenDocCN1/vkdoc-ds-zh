# 数据框介绍与API数据获取

## 数据框结构
数据框的列信息如下：
```
#   Column                      Non-Null Count  Dtype
---  ------                      --------------  -----
0   landingPage                  376 non-null    object
1   dateRange                    376 non-null    datetime64[ns]
2   activeUsers                  376 non-null    float64
3   screenPageViewsPerSession    376 non-null    float64
4   bounceRate                   376 non-null    float64
5   averageSessionDuration       376 non-null    float64
6   userConversionRate           376 non-null    float64
7   ecommercePurchases           376 non-null    float64
dtypes: datetime64[ns], float64(6), object(1)
memory usage: 23.6+ KB
```

运行 `df` 的结果如下：

![数据框截图](img/537787_1_En_8_Figb_HTML.jpg)

各维度条目及其指标的数据框截图。列标题分别为：着陆页、数据范围、活跃用户、每次会话的屏幕页面浏览量、跳出率、平均会话时长、用户转化率和电子商务购买量。结果是一个可供转换的数据框。

## DataForSEO SERPs API
`DataForSEO SERPs API` 专为返回指定关键词的 Google 搜索结果而设计。这对于检查排名、理解关键词的搜索意图以及其他 SEO 研究非常有用。

我们首先定义目标关键词列表：
```python
keywords_lst
```

运行结果如下：
```python
['airtime app',
 'airtime to cash app',
 'airtime transfer',
 'app to sell airtime',
 'app to transfer airtime from one network to another',
 'bet with airtime and win cash',
 'buy airtime online',
 'buy airtime with discount',
 'buy recharge card online',
 'buy recharge card online with debit card',
 'can i subscribe dstv with airtime?',
 'can i use my airtime to buy electricity?',
 'can you convert airtime to cash?', ...]
```

使用此 API，你需要将 [DataForSEO 客户端文件](https://docs.dataforseo.com/v3/) 放置在与运行此代码的 Jupyter notebook 脚本文件相同的文件夹中：
```python
from client import RestClient
client = RestClient("[your-username]", "xxxxxxxxxxxxxxx")
```

API 需要知道你想要查看哪个国家的搜索结果、设备类型以及语言，定义如下。国家/地区列表可在此处找到（[`https://docs.dataforseo.com/v3/serp/google/locations/?bash`](https://docs.dataforseo.com/v3/serp/google/locations/?bash)）。
```python
location = 2826
language = "en"
device_input = 'mobile'
```

以下是用于查询 API 的函数。`set_post_data` 将设置搜索参数：
```python
def set_post_data(search_query):
    post_data = dict()
    post_data[len(post_data)] = dict(
        language_code = language,
        location_code = location,
        device = device_input,
        keyword = search_query,
        calculate_rectangles = True)
    return post_data
```

函数 `get_api_result` 使用上述函数来构建输入，该输入将用于请求搜索结果。API 结果存储在一个名为 `response` 的变量中。其中包含一个 `try` 循环，以便在 API 调用出现问题时，函数能够继续执行并处理下一个关键词，从而避免阻塞整个操作或导致停滞：
```python
def get_api_result(search_query):
    post_data = set_post_data(search_query)
    response = client.post("/v3/serp/google/organic/live/advanced", post_data)
    try:
        return response
    except Exception as e:
        print(response)
        print(e)
        return None
```

由于需要查询多个关键词，我们将使用 `for` 循环多次调用该函数。

初始化一个空字典来存储各个 API 结果：
```python
desktop_serps_returned = {}
```

添加一个 `for` 循环，为列表中的每个关键词查询 API：
```python
i = 0
for search_query in set(keywords_lst):
    print(search_query, i + 1, len(keywords_lst) - i - 1)
    i += 1
    serp_dict = get_api_result(search_query)
    desktop_serps_returned[search_query] = serp_dict
```

在本书和 Jupyter notebook 中打印全部输出不太现实。因此，我们将打印存储数据的字典的键，这些键显示了拥有 API 数据的关键词。
```python
desktop_serps_returned.keys()
dict_keys(['buy recharge card online with debit card', 'can i use my airtime to buy electricity?', 'buy recharge card online', 'can you convert airtime to cash?', 'airtime to cash app', 'app to sell airtime', 'bet with airtime and win cash', 'airtime sell', 'buy airtime online', 'airtime sharing', 'can i subscribe dstv with airtime?', 'buy mtn', 'airtime buy', 'airtime transfer', 'buy airtime with discount', 'airtime app', 'airtime application', 'airtime bills', 'airtime funding', 'app to transfer airtime from one network to another'])
```

数据存储后，需要将字典解包为数据框格式，具体操作如下。

初始化一个空列表：
```python
desktop_serps_flat_df = []
```

使用 `for` 循环遍历字典的键，这些键将用于按关键词选取字典的对应部分。然后，我们遍历该关键词对应的字典数据内容，并将其添加到之前初始化的空列表中：
```python
for serp in desktop_serps_returned.keys():
    single_serp = desktop_serps_returned[serp]
    keyword = serp
    for task in single_serp['tasks']:
        cost = task['cost']
        task_id = task['id']
        se = task['data']['se']
        device = task['data']['device']
        os = task['data']['os']
        for res in task['result']:
            for idx, item in enumerate(res['items']):
                desktop_serps_flat_df.append(
                    (
                        cost, task_id, se, device, os, res['keyword'],
                        res['location_code'], res['language_code'],
                        res['se_results_count'], res['type'], res['se_domain'],
                        res['check_url'], item['rank_group'], item['rank_absolute'],
                        item.get('url', None), item.get('domain'), item.get('is_image'), item.get('is_featured_snippet'),
                        item.get('is_video'), item.get('is_malicious'), item.get('is_web_story'),
                        item.get('description'), item.get('pre_snippet'), item.get('amp_version'),
                        item.get('rating'), item.get('price'), item.get('highlighted'),
                        item.get('links'), item.get('faq'), item.get('extended_people_also_search'),
                        item.get('timestamp'), item.get('rectangle'),
                        res['datetime'], item.get('title'), item.get('cache_url')
                    )
                )
```

当列表包含了所有添加的关键词 SERP 数据后，将其转换为数据框：
```python
desktop_full_df = pd.DataFrame(
    desktop_serps_flat_df,
    columns=[
        'cost', 'task_id', 'se', 'device', 'os', 'keyword',
        'location_code', 'language_code', 'se_results_count',
        'type', 'se_domain', 'check_url', 'rank_group',
        'rank_absolute', 'url', 'domain', 'is_image', 'is_featured_snippet',
        'is_video', 'is_malicious', 'is_web_story',
        'description', 'pre_snippet', 'amp_version',
        'rating', 'price', 'highlighted', 'links',
        'faq', 'extended_people_also_search', 'timestamp', 'breadcrumb',
        'datetime', 'title', 'cache_url'
    ]
)
desktop_full_df.head(2)
```

结果如下：

![API数据结果截图](img/537787_1_En_8_Figc_HTML.jpg)

API 数据结果的截图。列标题包括 `cost`、`task_id`、`se`、`device`、`os`、`keyword`、`location_code`、`language_code`、`se_results_count`、`type`、`price` 和 `highlighted`。结果是以数据框形式呈现的 API 数据，可直接用于报告或报告前的转换处理。

## Google Search Console (GSC)
`Google Search Console (GSC)` 是第一方数据，也是大多数 SEO 从业者的数据权威来源。在此，我们将展示如何从 API 中提取数据，该 API 能提供比界面中标准的 1000 行更多的数据行。使用该 API 需要一个 `Google Cloud Platform (GCP)` 账户，您需要在该账户中创建一个 `GCP` 项目，并在该项目中创建带有 `JSON` 密钥的凭据。

首先，导入一些库：
```python
from apiclient import errors
from apiclient.discovery import build
import datetime
import httplib2
import re
import pandas as pd
import numpy as np
from collections import defaultdict
from oauth2client.client import OAuth2WebServerFlow
from datetime import datetime, timedelta, date
from dateutil.relativedelta import relativedelta
import calendar
import time
```

该脚本设计用于查询多个域名，这对于需要管理多个客户的代理机构报告系统，或者如果您是客户方需要管理多个站点，都非常有用：
```python
site_list = ['https://www.babywishiest.com']
site = 'https://www.babywishiest.com'
client_name = 'babywishiest'
```

维度将提供数据的细分，而不指定维度则返回指定日期范围内的汇总数据：
```python
dimensions = ['query', 'page']
```

要按设备筛选，请输入 `MOBILE`、`DESKTOP` 或 `TABLET`，留空则包含所有设备：
```python
device_filter = ''
```

要按搜索类型筛选，请输入 `WEB`、`IMAGE`、`VIDEO` 或 `discover`。留空则默认为 `WEB`：
```python
search_filter = ''
```

要按特定的三位字母国家代码（例如 `FRA`）进行筛选。国家代码列表可在此处获取（[`https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3`](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3)）。如果留空，API 将默认为所有国家：
```python
country_filter = ''
```

要筛选包含特定字符串的页面，可以使用 `"equals"`、`"contains"`、`"notContains"` 或 `"notEquals"` 等运算符：
```python
page_filter_string = ''
page_filter_operator = 'equals'
```

同样的方法也适用于查询：
```python
query_filter_string = ''
query_filter_operator = ''
```

指定查询的日期范围，该范围将被转换为日期时间格式：

### Google Search Console API

```python
start_date = '2022-08-01'
end_date = '2022-11-30'
start_date_datetime = datetime.strptime(start_date, '%Y-%m-%d').date()
end_date_datetime = datetime.strptime(end_date, '%Y-%m-%d').date()
print(start_date_datetime, end_date_datetime)
```

输入日期分组以细分数据。使用 `D`（天）、`W`（周）、`M`（月）或 `A`（全部）：

```python
date_grouping = 'A'
```

输入可从 GCP 项目的 API 部分获取的 API 凭据：

```python
CLIENT_ID = 'xxxxxxx'
CLIENT_SECRET = 'xxxxxx'
```

在请求之间添加休眠时间。如果遇到限制或错误，请增加此值：

```python
sleep_time = 10
```

指定参数后，下一个代码块处理使用 OAuth 方法的身份验证：

```python
OAUTH_SCOPE = 'https://www.googleapis.com/auth/webmasters.readonly'
REDIRECT_URI = 'urn:ietf:wg:oauth:2.0:oob'
```

执行 OAuth 流程并获取凭据：

```python
flow = OAuth2WebServerFlow(CLIENT_ID, CLIENT_SECRET, OAUTH_SCOPE, redirect_uri=REDIRECT_URI)
authorize_url = flow.step1_get_authorize_url()
print('在浏览器中访问以下链接：' + authorize_url)
code = input('输入验证码：').strip()
credentials = flow.step2_exchange(code)
```

创建一个 `httplib2.Http` 对象，并使用您的凭据对其进行授权：

```python
http = httplib2.Http()
http = credentials.authorize(http)
webmasters_service = build('searchconsole', 'v1', http=http)
```

身份验证完成后，设置每次 API 请求要检索的自定义行数：

```python
row_limit = 25000
```

创建一个数据框来存储完整的输出结果：

```python
output = pd.DataFrame()
request = {
    'rowLimit': row_limit,
    'startRow': 0
}
if dimensions:
    request['dimensions'] = dimensions
if search_filter:
    request['searchFilterGroups'] = [{'filters':[{'dimension':'search','expression':search_filter}]}]
```

根据设置构建维度过滤器。

```python
dimension_filters = []
if device_filter:
    dimension_filters.append({'dimension':'device', 'expression':device_filter})
if country_filter:
    dimension_filters.append({'dimension':'country', 'expression':country_filter})
if page_filter_string:
    dimension_filters.append({'dimension':'page','expression':page_filter_string, 'operator': page_filter_operator})
if query_filter_string:
    dimension_filters.append({'dimension':'query','expression':query_filter_string, 'operator': query_filter_operator,})
request['dimensionFilterGroups'] = [{'filters':dimension_filters}]
print(f'Filter: {dimension_filters}')
```

遍历从起始日期到结束日期的所有日期（含首尾），并用循环中的日期填充请求的起始和结束日期：

```python
for site in site_list:
    for single_date in daterange(start_date_datetime, end_date_datetime, date_grouping):
        request['startDate'] = f"{single_date[0].strftime('%Y')}-{single_date[0].strftime('%m')}-{single_date[0].strftime('%d')}"
        request['endDate'] = f"{single_date[1].strftime('%Y')}-{single_date[1].strftime('%m')}-{single_date[1].strftime('%d')}"
        print(site + ' - ' + request['startDate'] + ' to ' + request['endDate'])
        run = True
        rowstart = 0
        request['startRow'] = rowstart
        while run:
            try:
                response_page = execute_request(webmasters_service, site, request)
                scDict_results = defaultdict(list)
                try:
                    for row in response_page['rows']:
                        if dimensions:
                            for i,dimension in enumerate(dimensions):
                                scDict_results[dimension].append(row['keys'][i] or 0)
                        scDict_results['clicks'].append(row['clicks'] or 0)
                        scDict_results['ctr'].append(row['ctr'] or 0)
                        scDict_results['impressions'].append(row['impressions'] or 0)
                        scDict_results['position'].append(row['position'] or 0)
                    df = pd.DataFrame(data = scDict_results)
                    df['start_date'] = request['startDate']
                    df['end_date'] = request['endDate']
                    df['site'] = site
                    frames = [output, df]
                    output = pd.concat(frames)
                    print(str(len(df)) + ' results')
                    time.sleep(sleep_time)
                    if len(df) == row_limit:
                        rowstart += row_limit
                        request['startRow'] = rowstart
                    else:
                        run=False
                except:
                    print('No results found for this date range')
                    run=False
            except HttpError:
                print('Got an error. Retrying in 1m.')
                time.sleep(60)
```

筛选条件：`[]`
`https://www.babywishiest.com - 2022-08-01 to 2022-11-30`
`1672 results`
`output`

结果如下：

![](img/537787_1_En_8_Figd_HTML.jpg)

一大块 API 数据结果的截图。列标题为 `query`、`page`、`clicks`、`ctr`、`impressions`、`positions`、`start date`、`end date` 和 `site`。

虽然这是一大段代码，但该 API 可用于提取多达 10 万行数据，甚至更多。

### Google PageSpeed API

PageSpeed API 是 SEO 人员的另一个核心指标，尤其是考虑到 Google 在 2020 年 4 月推出的核心网页指标（CWV）计划。该 API 不仅可用于检查自己网站的 CWV 得分，还可用于检查 SERP 竞争对手的得分。要使用此 API，需要从 Google Cloud Platform (GCP) 的 API 部分获取一个密钥。

首先，定义要检查 CWV 得分的 URL 列表：

```python
desktop_serps_urls = ['https://pay.jumia.com.ng/services/airtime',
'https://pay.jumia.com.ng/',
'https://vtpass.com/',
'https://www.gloverapp.co/products/airtime-to-cash',
'https://www.zoranga.com/',
'https://airtimeflip.com/',
'https://www.tingtel.com/blog/sell-airtime-over-charged-your-line-dont-panic',
'https://vtpass.com/payment',
'https://pay.jumia.com.ng/services/mobile-data/mtn-mobile-data', ...]
```

`"https://www.googleapis.com/pagespeedonline/v5/runPagespeed?url=[test-url]&key=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"`

设置 API 的参数：

```python
base_url = 'https://www.googleapis.com/pagespeedonline/v5/runPagespeed?url='
strategy = '&strategy=desktop'
api_url = '&key=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'
```

初始化一个空字典来存储数据，以及一个计数器来跟踪正在查询的 URL 数量：

```python
desktop_cwv = {}
i = 0
```

遍历 URL 列表以查询 API：

```python
for url in desktop_serps_urls:
    request_url = base_url + url + strategy + api_url
    response = json.loads(requests.get(request_url).text)
    i += 1
    print(i, " ", request_url)
    desktop_cwv[url] = response
```

打印键以列出成功查询的 URL：

# 提取与处理数据

## 从 PageSpeed API 提取 CWV 指标

从空列表开始，遍历 PageSpeed API JSON 响应字典：

```python
desktop_psi_lst = []
```

通过键循环遍历字典，提取不同的 CWV 指标并将其存储在列表中：

```python
for key, data in desktop_cwv.items():
    if 'lighthouseResult' in data:
        FCP = data['lighthouseResult']['audits']['first-contentful-paint']['numericValue']
        LCP = data['lighthouseResult']['audits']['largest-contentful-paint']['numericValue']
        CLS = data['lighthouseResult']['audits']['cumulative-layout-shift']['numericValue']
        FID = data['lighthouseResult']['audits']['max-potential-fid']['numericValue']
        SIS = data['lighthouseResult']['audits']['speed-index']['score'] * 100
        desktop_psi_lst.append([key, FCP, LCP, CLS, FID, SIS])
desktop_psi_df = pd.DataFrame(desktop_psi_lst, columns = ['url', 'FCP', 'LCP', 'CLS', 'FID', 'SIS'])
desktop_psi_df
```

结果如下：

![一个数据框的截图，其中包含每个 URL 的所有 CWV 得分条目。列标题为 `URL`、`FCP`、`LCP`、`CLS`、`FID` 和 `SIS`。](img/537787_1_En_8_Fige_HTML.jpg)

结果是一个数据框，显示了每个 URL 的所有 CWV 得分。

## 数据转换

转换通过 API 或其他方式从数据源提取的数据，其目的是：

*   清理数据，以便进一步计算指标
*   推导有意义的统计数据，例如月环比（`MoM`）差异
*   将数据插入（即加载）到数据仓库中

代码将继续处理之前提取的 Google Analytics (`GA`) 数据，我们将涵盖上述几点。

### 清理与格式化数据

我们首先复制 `GA` 数据框：

```python
df_clean = df.copy()
```

将 `averageSessionDuration` 重新配置为秒数：

```python
df_clean['averageSessionDuration'] = (df_clean['averageSessionDuration'] / 60).round(1)
```

基于时间和日历日期单位创建新列，以便于转换：

```python
df_clean['month'] = df_clean['dateRange'].dt.strftime('%m')
df_clean['year'] = df_clean['dateRange'].dt.strftime('%Y')
df_clean['month_year'] = df_clean['dateRange'].dt.strftime('%Y-%m')
df_clean
```

结果如下：

![一个数据框的截图，其中包含趋势数据的新列。列标题为 `landing page`、`data range`、`active users`、`screen page views per session`、`bounce rate`、`average session duration`、`user conversion rate`、`ecommerce purchases` 和 `month`。](img/537787_1_En_8_Figf_HTML.jpg)

数据格式化后，我们可以开始转换以推导趋势数据的新列，例如：

*   平均值
*   标准差（用于衡量变异）
*   周期性变化（例如月环比）

### 计算基础汇总统计量

让我们复制一份并重命名，以反映我们正在按着陆页和月份进行聚合：

```python
ga4_lp_agg_month = df_clean.copy()
```

我们将创建一些基本的汇总统计量，即使用 `groupby()` 和 `agg()` 函数计算各种 `GA` 指标的平均值（`"mean"`）和总和（`"sum"`）：

```python
ga4_lp_agg_month_basic = ga4_lp_agg_month.groupby(['landingPage', 'month_year']).agg({'activeUsers':'sum',
'screenPageViewsPerSession':'mean',
'bounceRate':'mean',
'averageSessionDuration':'mean',
'userConversionRate':'mean',
'ecommercePurchases':'sum'
}).reset_index()
ga4_lp_agg_month_basic
```

结果如下：

![一张数据框截图，条目按着陆页和年月汇总。列标题分别为：着陆页、年月、活跃用户、每次会话浏览页数、跳出率、平均会话时长、用户转化率和电子商务购买量。](img/537787_1_En_8_Figg_HTML.jpg)

如上所示的指标按着陆页和 `month_year` 汇总，可用于为基础 `SEO` 仪表盘报告系统提供数据。通常在所有情况下，我们都会追踪平均值和标准差。平均值能为我们提供一个有用的指标，表明某个渠道在性能方面处于什么水平，因为它会指出在给定时间点，对于给定数据类别，大多数数据点位于或将要位于何处。正如每一位统计学家（以及许多具备统计意识的人）会告诉你的那样，平均值在单独用于推断甚至决策时可能很危险。这就是为什么我们还要追踪标准差，因为这个指标能告诉我们某个指标的变异情况，即其一致性如何。

实际上，标准差告诉我们数据点与平均值的接近程度。我们能从中推断出什么？我们可以推断出哪些平均值更值得信赖或依赖，以便在月份之间进行比较。因此，标准差可以在一定程度上告诉我们平均值的质量，以便我们对数据建立信心、进行比较，并了解我们追踪的指标随时间的变化情况。

例如，你可能会发现标准差在增加或减少，因此应该尝试理解其背后的原因。可能是：

*   谷歌通过算法更新带来的变化？
*   用户行为、查询搜索意图、品牌定位或市场的变化？
*   网站的用户体验、内容或架构的变化？

追踪标准差可以帮助你发现某些事情是在变好还是变坏。

### 计算平均值与标准差

执行聚合以得出各种平均值和标准差统计量：

```python
ga4_lp_agg_month = df_clean.copy()
ga4_lp_agg_month_mean = ga4_lp_agg_month.groupby(['landingPage',
'month_year']).agg({'activeUsers':'mean',
}).reset_index()
ga4_lp_agg_month_mean = ga4_lp_agg_month_mean.rename(columns = {'activeUsers':'activeUsers_avg'})
```

`"mean"` 计算平均值，而 `"std"` 计算标准差：

```python
ga4_lp_agg_month_std = ga4_lp_agg_month.groupby(['landingPage',
'month_year']).agg({'activeUsers':'std',
'bounceRate':'std',
}).reset_index()
```

重命名列：

```python
ga4_lp_agg_month_std = ga4_lp_agg_month_std.rename(columns = {'activeUsers':'activeUsers_std',
'bounceRate':'bounceRate_std'
})
```

将数据连接到之前创建的基础数据框：

```python
ga4_lp_agg_month_stats = ga4_lp_agg_month_basic.merge(ga4_lp_agg_month_mean,
on = ['landingPage', 'month_year'], how = 'left')
ga4_lp_agg_month_stats = ga4_lp_agg_month_stats.merge(ga4_lp_agg_month_std,
on = ['landingPage', 'month_year'], how = 'left')
ga4_lp_agg_month_stats.head()
```

结果如下：

![一张显示新增列结果的截图。列标题分别为：每次会话浏览页数、跳出率、平均会话时长、用户转化率、电子商务购买量、活跃用户平均值和标准差、跳出率标准差。](img/537787_1_En_8_Figh_HTML.jpg)

我们现在可以看到之前创建的额外列。

### 计算环比变化

下一段代码计算各种性能数据的环比。

首先，我们对值进行排序，使每个着陆页的行按月份顺序排列，因为环比计算将依赖于行的位置：

```python
ga4_lp_agg_month_moms = ga4_lp_agg_month_stats.sort_values(['landingPage', 'month_year'])
```

我们只计算 `activeUsers` 和 `bounceRate` 的月度统计量；但是，你可以对所有其他指标列使用相同的方法。首先，我们使用 `shift()` 函数计算当前行与上一行（上个月）的绝对变化。

请注意，`shift()` 函数传入的参数是 `"1"`，表示 1 行。如果你想计算同比差异，那么可以传入 `"12"`（即 `.shift(12)`），它会查看前 12 行的值。

```python
ga4_lp_agg_month_moms['activeUsers_delta'] = ga4_lp_agg_month_moms['activeUsers'] - ga4_lp_agg_month_moms['activeUsers'].shift(1)
```

然后，通过将绝对变化除以当前月份的值并乘以 100 来计算环比：

```python
ga4_lp_agg_month_moms['activeUsers_mom'] = ((ga4_lp_agg_month_moms['activeUsers_delta'] / ga4_lp_agg_month_moms['activeUsers'].shift(1) * 100)).round(1)
```

对跳出率重复此过程：

```python
ga4_lp_agg_month_moms['bounceRate_delta'] = ga4_lp_agg_month_moms['bounceRate'] - ga4_lp_agg_month_moms['bounceRate'].shift(1)
ga4_lp_agg_month_moms['bounceRate_mom'] = ((ga4_lp_agg_month_moms['bounceRate_delta'] / ga4_lp_agg_month_moms['bounceRate'].shift(1) * 100)).round(1)
ga4_lp_agg_month_moms
```

结果如下：

![一张数据框截图，包含额外列。列标题分别为：每次会话浏览页数、跳出率、平均会话时长、用户转化率、电子商务购买量、活跃用户标准差、跳出率标准差。](img/537787_1_En_8_Figi_HTML.jpg)

新增了差值（`delta`）和环比（`month-on-month`）列。注意第一行的 `NaN`，这是因为没有前一行供 `shift()` 函数使用。要覆盖 `NaN`，你可以使用 `np.where()` 函数将 `.isnull()` 替换为零。另一种方法是使用特殊函数来避免对行进行排序。但是，如果你计划将其自动化成一个包罗万象的 `SEO` 数据仓库仪表盘报告系统，那么在云端运行时，这种方法可能在计算上更昂贵。

完成后，你就可以上传到你选择的数据仓库了。

## 加载数据

正如本章前面提到的，加载涉及将转换后的数据移入数据仓库。上传后，最好检查你的数据模式并预览你上传的内容。

以下 `SQL` 将按月份和渠道生成用户趋势：

```sql
select yearMonth, channel, sum(users) as users from (
select yearMonth, 'organic' as channel, users_sum  as users from
google_analytics.multichannel_ga_monthly
where channel in ('Organic Traffic')
and
DATE_DIFF(CURRENT_DATE(), PARSE_DATE('%Y-%m-%d', CONCAT(SUBSTR(CAST(yearMonth AS STRING), 1, 4),"-",
SUBSTR(CAST(yearMonth AS STRING), 5, 2),"-",'01')), MONTH) <= 12
union all
select yearMonth, 'non_seo' as channel, all_users - organic as users
from (
SELECT yearMonth
, MAX(IF(channel = 'Organic Traffic', users_sum, 0)) organic
, MAX(IF(channel = 'All Users', users_sum, 0)) all_users
from google_analytics.multichannel_ga_monthly
where channel in ('Organic Traffic', 'All Users')
group by yearMonth
)
)
where
DATE_DIFF(CURRENT_DATE(), PARSE_DATE('%Y-%m-%d', CONCAT(SUBSTR(CAST(yearMonth AS STRING), 1, 4),"-",
SUBSTR(CAST(yearMonth AS STRING), 5, 2),"-",'01')), MONTH) <= 12
group by yearMonth, channel
order by yearMonth LIMIT 100;
```

以下 `SQL` 将按月生成用户流量统计，并包含同比数据：

```sql
select
yearMonth,
CASE
WHEN channel = 'All Users' THEN "non_seo"
ELSE channel
END as channel,
users_yoy
from google_analytics.multichannel_ga_monthly
where
channel in ("All Users", "Organic Traffic")
and
DATE_DIFF(CURRENT_DATE(), PARSE_DATE('%Y-%m-%d', CONCAT(SUBSTR(CAST(yearMonth AS STRING), 1, 4),"-", SUBSTR(CAST(yearMonth AS STRING), 5, 2),"-",'01')), MONTH) <= 12
order by yearMonth desc LIMIT 100;
```

以下 `SQL` 将按月生成今年与去年截至当前月份的同比用户流量统计。

```sql
SELECT yearMonth
, year
, SUBSTR(CAST(yearMonth AS STRING), 5, 6) as mon_x
, users_sum
from google_analytics.multichannel_ga_monthly
where
channel in ("Organic Traffic")
order by mon_x, year desc;
```

结果如下：

运行后，结果会由 Google BigQuery 在“结果”选项卡中生成（图 8-1）。

![](img/537787_1_En_8_Fig1_HTML.jpg)

`SQL` 执行后的 Google BigQuery 结果截图。结果选项卡的列名包括 `row`、`year month`、`year`、`mon underscore x` 和 `users underscore sum`。

`图 8-1` SQL 执行后的 Google BigQuery 结果预览

## 可视化

如果你对 `SQL` 结果满意，可以使用相同的查询并在前端（如 `Looker Studio`、`Tableau` 等）中进行可视化，如下所示。自然搜索与其他渠道相比如何？按数量（顶部）和同比（底部）随时间变化，如 `Looker Studio` 图表所示（图 8-2）。

![](img/537787_1_En_8_Fig2_HTML.png)

`Looker Studio` 的折线图。它绘制了非 `SEO` 和自然流量，分别从 2019 年 7 月的 71326 和 15656 用户开始，到 2020 年 6 月的 221321 和 60106 用户结束。两者均呈波动趋势。

`图 8-2` `Looker Studio` 图表显示过去 12 个月自然流量与非 `SEO` 渠道的对比

自然搜索与其他渠道的同比表现如何？图 8-3 中的 `Looker Studio` 图表可视化了计算自然流量和非 `SEO` 渠道同比流量数据的 `SQL` 语句。这有助于了解 `SEO` 在一年中特定时段的表现（即不受季节性影响）。它还能衡量 `SEO` 在同一时期相对于非 `SEO` 渠道的表现。

![](img/537787_1_En_8_Fig3_HTML.png)

`Looker Studio` 的双柱状图绘制了用户数与年份的关系。自然流量和非 `SEO` 在 2020 年 4 月达到最高，分别为 409.69 和 279.28 用户。

`图 8-3` `Looker Studio` 柱状图显示自然流量和非 `SEO` 渠道按月的同比流量数据

自然搜索用户与去年相比如何？图 8-4 中的 `Looker Studio` 图表仅显示了自然流量的今年和去年流量数据。这有助于单独比较今年与去年的 `SEO` 表现。

![](img/537787_1_En_8_Fig4_HTML.png)

`Looker Studio` 折线图绘制了 2020 年和 2019 年的两条线。它从 1 月的 16524 和 20465 用户开始，分别到 6 月和 12 月的 60106 和 12518 用户结束。2020 年在 4 月达到峰值 85022 用户，而 2019 年保持平稳趋势。

`图 8-4` `Looker Studio` 图表显示仅自然流量的今年和去年流量数据

## 自动化

当然，这一切都可以自动化；你只需要一个称职的云软件工程师团队来实现自动化：

*   通过 `API` 轮询数据（提取）
*   清洗、重构并创建新的计算字段（转换）
*   加载（到 `BigQuery`、`Amazon Redshift` 或其他系统）

结果很简单，尽管实际执行要复杂得多，这依赖于云工程技能。

## 总结

在构建仪表板时，重要的是要始终以终为始，思考仪表板的目的是什么以及为谁而建（即你的受众）。一旦确定了输出，再反向推导。仪表板由数据驱动，因此你需要考虑需要哪些数据源。原始数据很少适合直接接入 `Looker Studio` 等前端，因为它可能会使前端过载，导致加载缓慢或崩溃。相反，你应该将数据汇总成有意义的趋势。

提取、转换和加载（`ETL`）是自动化数据收集、汇总数据并将其加载到系统中的过程。我们提供了代码来帮助你：

*   从常见 `SEO` 数据源（包括 `Google Analytics`）提取 `SEO` 数据
*   按渠道汇总进行转换
*   查看数据加载到 `Looker Studio` 后的样子

# 9. 网站迁移规划

本章涵盖网站迁移映射，以便你可以设置新网站的结构，并半自动化地形成迁移 `URL`。我们将使用以下一些技术：

*   字符串操作
*   通过将数据框行转换为列表来迭代
*   使用自然语言处理（`NLP`）比较 `URL` 字符串

虽然这些技术会加快网站迁移的数据处理速度，但它们也可以轻松应用于其他用例。

## 验证流量和排名变化

虽然验证重新上线后的流量和/或排名变化并非严格必要，但如果任何同事怀疑流量或排名的变化是否归因于你声称的日期，那么进行验证是好的。不过，如果时间紧迫，你可以跳过此步骤。

```python
import re
import time
import random
import pandas as pd
import numpy as np
import datetime
from textdistance import sorensen_dice
from textdistance import jaccard
pd.set_option('display.max_colwidth', None)
target_site_search = 'Saga travel'
first_gen = ['Holidays', 'Cruises', 'Travel Updates', 'Accessibility and Support', 'Brochure Request', 'My Travel', 'Trade']
target_roots = first_gen
source_root_url = 'https://travel.saga.co.uk/'
target_root_url = 'https://www.saga.co.uk/'
```

数据来自一个电子表格，该表格代表了网站的分类或层级结构，即文件夹和子文件夹，站点级别按列组织：

```python
hierarchy_raw = pd.read_csv('data/saga_hierarchy.csv')
hierarchy_raw
```

结果如下：

![](img/537787_1_En_9_Figa_HTML.jpg)

一个包含 372 行和 7 列的表格，提供了数据层级结构的结果。它包含了现有客户、主页、保险、联系我们、更新、新冠假期客人、邮轮新冠疫苗、假期新冠疫苗以及旅行销售。

在上表中，我们可以看到电子表格的样式：顶部数字表示站点层级，每行中的页面（我们称之为节点）及其直接父节点名称。

让我们获取每个父节点的站点层级：

```python
site_levels = pd.DataFrame(hierarchy_raw.unstack())
site_levels = site_levels.dropna().drop_duplicates()
site_levels = site_levels.rename(columns = {0 : 'node'})
site_levels = site_levels.reset_index()
site_levels = site_levels[['level_0', 'node']]
site_levels = site_levels.rename(columns = {'level_0': 'level'})
site_levels
```

结果如下：

![](img/537787_1_En_9_Figb_HTML.jpg)

一个包含 330 行和 2 列的表格，提供了数据层级结构的结果，用于查找父节点和子节点。它包含了现有客户、主页、保险、船险、房车保险、`Noordam`、套房、`Jools Holland`、艺术家以及构建进度。

定义了站点节点（稍后会派上用场）后，我们将查找父节点和子节点的配对。

### 识别父节点和子节点

子节点是距离父节点仅一次点击的直属页面。为此，我们需要几个函数。`apply_pcn` 函数将数据帧视为行集合，并应用第二个函数。这种方法比使用 `.iterrows()` 逐行遍历数据帧更快，后者以速度极慢而闻名。
`parent_child_nodes` 函数将获取行，将其转换为列表，然后使用列表推导式忽略空白单元格（`NaN`，即“非数字”的缩写），并将内容追加到列表 `pairs` 中。
完成后，`pairs` 列表将被放入一个新的数据帧 `parent_child_map` 中。

让我们逐行迭代以选取配对：

```python
pairs = []
def parent_child_nodes(row):
    data = row.values.tolist()
    data = [e for e in data if str(e) not in ('nan')]
    print(data)
    pairs.append(data)
def apply_pcn(df):
    return df.apply(
        lambda row:
        parent_child_nodes(
            row),
        axis=1
    )
apply_pcn(hierarchy_raw)
parent_child_map = pd.DataFrame(pairs,columns=['parent', 'child'])
parent_child_map
```

我们现在有了一个显示父节点和子节点的表格：

![](img/537787_1_En_9_Figc_HTML.jpg)

一个包含 372 行和 2 列的表格，提供了父节点和子节点的数据层级结构结果。它包含了现有客户、主页、保险、联系我们、更新、新冠假期客人、邮轮新冠疫苗、假期新冠疫苗以及旅行销售。

当然，如果我们想要完整的 `URL` 路径，我们需要使用 `hierarchy_raw` 的副本进一步处理数据。首先向下填充主页的第一列，然后如果右侧相邻单元格不为空（使用函数 `has_data_right` 检查），则填充该单元格：

```python
hierarchy_fp = hierarchy_raw
```