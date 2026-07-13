# 6. 通过 API 将数据读取到 Power BI 数据模型中

许多政府机构和私营公司已通过数据 API 使其数据更易于访问。数据 API 提供了接口，使你能够以编程方式从数据提供商处检索数据。这些数据 API 提供的数据集可以为你的 Power BI 数据模型带来巨大价值。

使用 M 代码与数据 API 交互非常困难，但使用 R 和 Python 则不然。在本章中，你将学习如何通过美国人口普查局的数据 API，使用 R 和 Python 检索数据并将其加载到 Power BI 数据模型中。首先，你将看到如何使用 R 加载数据。然后，你将看到使用 Python 加载相同数据的过程。

## 使用 R 通过 API 将人口普查数据读取到 Power BI 中

场景如下：你是 Fleek 公司的市场分析师。Fleek 是一家零售服装店，目标市场是年轻人。该公司在美国东南部地区拥有强大的市场影响力，但在印第安纳州没有任何门店。你的任务是找出印第安纳州中具有已知会光顾 Fleek 门店的人口特征的县。你希望使用美国人口普查局的人口统计数据来帮助你在 Power BI 中进行分析。以下各节展示了使用 R 从美国人口普查局获取人口统计数据的步骤。

### 步骤 1：获取个人人口普查 API 密钥

要访问美国人口普查局的数据，你需要一个人口普查 API 密钥。你可以通过以下网址获取：[`https://api.census.gov/data/key_signup.html`](https://api.census.gov/data/key_signup.html)。你需要填写一份简短的表格并接受服务条款。点击“提交密钥请求”按钮后，美国人口普查局将通过电子邮件回复，其中包含你的 API 密钥。请务必将密钥保存在安全位置。

### 步骤 2：加载必要的 R 包

本例中将使用的包是 `tidyverse` 和 `tidycensus`。在本书的这个阶段，你已经非常熟悉 `tidyverse` 包，因此无需解释，但 `tidycensus` 包则不然。`tidycensus` 包使你能够从美国人口普查局和美国社区调查中检索数据。该包以适用于 `tidyverse` 的数据框形式返回数据。你可以通过以下链接找到有关如何使用此包以及可用数据集的更多信息：[`https://walkerke.github.io/tidycensus/`](https://walkerke.github.io/tidycensus/)。使用以下代码加载所需的库以及你从美国人口普查局收到的人口普查 API 密钥到当前会话中：

```r
library(tidycensus)
library(tidyverse)
census_api_key("", install = FALSE)
```

如果你使用上述方法，则需要在所有后续运行中都包含该代码。如果你想将 API 密钥安装到 R 环境中以备将来使用，则需要改用以下代码：

```r
census_api_key("", install = TRUE)
readRenviron("~/.Renviron")
```

将 `install` 参数设置为 `TRUE` 会将密钥安装到你的 R 环境中以备将来使用。下一行代码会重新加载你的环境，以便你可以在当前会话中使用 API 密钥，而无需重启 R。

### 步骤 3：确定要从数据集中返回的变量

美国人口普查局提供了多个数据集。本例中所需的信息包含在 `acs5` 数据集中。

`acs5` 数据集基于五年期间的估算值。其他可用的数据集包括 `sf1`、`sf3`、`acs1`、`acs3`、`acs5`、`acs1/profile`、`acs3/profile`、`acs5/profile`、`acs1/subject`、`acs3/subject` 和 `acs5/subject`。你可以访问 [`www.census.gov/programs-surveys/acs/guidance/handbooks.html`](http://www.census.gov/programs-surveys/acs/guidance/handbooks.html) 获取有关美国人口普查局提供的数据类型的更多信息。

你需要从 `acs5` 数据集中获取 2015 日历年按年龄段划分的人口数据，但你不知道包含这些信息的变量名称。使用以下代码获取 2015 日历年 `acs5` 数据集中所有可用变量的完整列表：

```r
v15 <- load_variables(year = 2015, dataset= "acs5", cache = FALSE)
View(v15)
```

让我们花点时间解释一下这段代码。`load_variables` 函数根据传递给它的参数值创建一个存储变量的数据框。`cache` 选项使你能够在本地缓存数据，以便后续更快地检索。`load_variables` 函数的输出如下表所示：

| name | label | concept |
| --- | --- | --- |
| 1 | B06001_001E | Estimate!!Total |
| 2 | B06001_002E | Estimate!!Total!!Under 5 years |
| 3 | B06001_003E | Estimate!!Total!!5 to 17 years |
| 4 | B06001_004E | Estimate!!Total!!18 to 24 years |
| 5 | B06001_005E | Estimate!!Total!!25 to 34 years |

上述输出中仅列出了数据集的五条记录。生成的数据集实际上包含超过 22,000 个变量的列表！使用 `View` 函数可以让你在带有筛选功能的表格结构中查看变量列表。即使有筛选功能，找到所需的变量也可能具有挑战性。在后续章节中，你将学习更高级的筛选技术，这些技术将通过 `dplyr` 包帮助你找到所需的变量。我使用了其中一些技术来找到下一步中使用的变量。

### 步骤 4：创建一个包含所需返回变量的字符向量

在确定所需变量后，需要将它们放入一个字符向量中。以下是一个字符向量示例，其中包含本例所需的按年龄段划分的人口变量：

```r
cns_vars <- c("B06001_001E","B06001_002E","B06001_003E",
"B06001_004E","B06001_005E","B06001_006E","B06001_007E",
"B06001_008E","B06001_009E","B06001_010E","B06001_011E",
"B06001_012E")
```

`B06001_001E` 代表总人口，`B06001_002E` 代表 5 岁以下人口，`B06001_003E` 代表 5 至 17 岁人口，`B06001_004E` 代表 18 至 24 岁人口，`B06001_005E` 代表 25 至 34 岁人口，`B06001_006E` 代表 35 至 44 岁人口，`B06001_007E` 代表 45 至 54 岁人口，`B06001_008E` 代表 55 至 59 岁人口，`B06001_009E` 代表 60 至 61 岁人口，`B06001_010E` 代表 62 至 64 岁人口，`B06001_011E` 代表 65 至 74 岁人口，`B06001_012E` 代表 75 岁及以上人口。

### 步骤 5：配置 `get_acs` 函数

配置 `get_acs` 函数以检索数据，并将输出分配给名为 `IN_POP_BY_COUNTY_BY_AGE` 的数据框。`get_acs()` 函数从美国人口普查 API 中检索数据，并将其放入 R 数据框中。以下代码获取印第安纳州县级按年龄段划分的所需人口信息：

```r
IN_POP_BY_COUNTY_BY_AGE <-
suppressMessages(
get_acs(
geography = "county",
state = "IN",
variables = cns_vars,
survey = "acs5",
year = 2015,
output = "wide"
)
)
```

我们来解释一下这段代码。`geography` 参数告诉 `tidycensus` 你希望数据的地理层级。你需要的层级是县级，因此使用该值作为参数。为了将县限制在印第安纳州范围内，你将 `state` 参数设置为 `"IN"`。你将 `variables` 参数设置为包含步骤 4 中定义的变量列表的字符向量。`survey` 参数用于告诉 `tidycensus` 你要搜索哪个数据集。`year` 参数是调查年份。`output` 参数告诉 `tidycensus` 你希望如何输出数据。将其设置为 `"wide"` 会将数据格式化为每个变量拥有独立列的形式。整个函数被包裹在 `suppressMessages` 函数中。该函数用于抑制 `get_acs()` 函数向控制台打印信息，这些信息可能会对 Power BI 造成问题。

### 步骤 6：为变量（列）赋予有意义的名称

美国人口普查提供的列名有些晦涩难懂，无法清晰表明其含义。幸运的是，在使用 `load_variables` 函数查找变量时，你已经了解了它们的含义。你可以使用 `dplyr` 中的 `rename` 函数为列重命名，使其更具意义。以下是执行此操作的代码：

```r
IN_POP_BY_COUNTY_BY_AGE =
rename(
IN_POP_BY_COUNTY_BY_AGE,
`Total`=B06001_001E,
`Under 5`=B06001_002E,
`5 to 17`=B06001_003E,
`18 to 24`=B06001_004E,
`25 to 34`=B06001_005E,
`35 to 44`=B06001_006E,
`45 to 54`=B06001_007E,
`55 to 59`=B06001_008E,
`60 and 61`=B06001_009E,
`62 to 64`=B06001_010E,
`65 to 74`=B06001_011E,
`75+`=B06001_012E
)
```

`rename` 函数的第一个参数是包含要重命名列的数据集。其后是一系列值对，左侧是新列名，右侧是旧列名，中间用 `=` 号分隔。

使用 `dplyr` 中的 `rename` 函数重命名列的一个好处是，你可以使用在常规 R 数据框中不被允许的列名。

### 步骤 7：将脚本复制到 Power BI 中

以下是完整的脚本：

```r
library(tidycensus)
library(tidyverse)
census_api_key(
"", overwrite = FALSE,
install = FALSE)
cns_vars <- c("B06001_001E","B06001_002E","B06001_003E","B06001_004E","B06001_005E","B06001_006E","B06001_007E","B06001_008E","B06001_009E","B06001_010E","B06001_011E","B06001_012E")
IN_POP_BY_COUNTY_BY_AGE <-
suppressMessages(
get_acs(
geography = "county",
state = "IN",
variables = cns_vars,
survey = "acs1",
year = 2015,
output = "wide"
)
)
IN_POP_BY_COUNTY_BY_AGE =
rename(
IN_POP_BY_COUNTY_BY_AGE,
`Total`=B06001_001E,
`Under 5`=B06001_002E,
`5 to 17`=B06001_003E,
`18 to 24`=B06001_004E,
`25 to 34`=B06001_005E,
`35 to 44`=B06001_006E,
`45 to 54`=B06001_007E,
`55 to 59`=B06001_008E,
`60 and 61`=B06001_009E,
`62 to 64`=B06001_010E,
`65 to 74`=B06001_011E,
`75+`=B06001_012E
)
```

将整个脚本复制到 Power BI 的 R 脚本编辑器中。数据框的名称默认为 `IN_POP_BY_COUNTY_BY_AGE`。如果你想重命名查询，则需要进入编辑模式进行操作。如果你对该名称满意，可以直接将数据集加载到 Power BI 数据模型中，作为一些出色可视化效果的源数据。

## 通过 API 使用 Python 将人口普查数据读取到 Power BI 中

你也可以选择使用 Python 加载数据。以下部分描述了使用 Python 将人口普查数据导入 Power BI 的步骤。其工作流程与使用 R 的工作流程类似。

### 步骤 1：获取个人人口普查 API 密钥

使用 Python 的 `censusdata` 库访问人口普查数据时，API 密钥是可选的。要获取密钥，请访问以下网址：[`https://api.census.gov/data/key_signup.html`](https://api.census.gov/data/key_signup.html)。你需要填写一份简短的表格，接受*服务条款*，然后点击*提交密钥请求*按钮。人口普查局将通过电子邮件回复，其中包含你的 API 密钥。请务必将密钥保存在安全位置。

### 步骤 2：加载必要的 Python 库

此脚本所需的两个库是 `pandas` 和 `censusdata` 库。`Pandas` 用于数据处理，`censusdata` 库用于与人口普查 API 交互。以下是加载库所需的代码：

```python
import pandas as pd
import censusdata
```

如果上述库未安装，请参考本书引言部分获取如何安装 Python 库的说明。

### 步骤 3：确定数据集中需要返回的变量

你需要在 `acs5` 数据集中搜索包含按年龄段划分的 2015 年人口信息的变量。在控制台中输入以下代码执行搜索：

```python
v15 = censusdata.search(
'acs5',2015,'concept',
'PLACE OF BIRTH BY AGE IN THE UNITED STATES')
```

上述代码并非主脚本的一部分，它用于根据前述条件搜索可用的变量。本章的 GitHub 仓库中提供了一个脚本，该脚本不仅能检索变量信息，还能将信息保存为 csv 文件，方便你以该格式探索数据。

上述代码使用了 `censusdata` 库中的 `search` 函数。第一个参数是包含所需变量的调查，第二个参数是要搜索的年份，第三个参数是要搜索的字段。你可以选择 `label` 或 `concept`。这里使用了 `concept`，因为该字段包含了你正在查找的变量的描述。最后一个参数是搜索条件。

`censusdata.search` 函数返回一个元组列表。

**元组** 是一种在设置后无法更改的对象序列。Python 中元组和列表的主要区别在于，列表可以在创建后修改，而元组则不能。

每个元组包含三个元素。第一个元素是变量名，第二个元素是概念，第三个元素是标签。使用上述代码搜索返回的列表包含 120 个元组。你可以通过以下代码提取前五个元组，了解信息的大致结构：

```python
v15[0:5]
```

上述代码通过获取从位置 0 开始到位置 5（不含）的元素，对 `v15` 列表进行了子集划分。结果是一个包含位置 0、1、2、3 和 4 元素的列表。输出结果的重排版本如下所示。输出结果经过重排，以便你轻松识别每个元组：

```python
[
('B06001_001E',
'B06001\.  PLACE OF BIRTH BY AGE IN THE UNITED STATES',
'Total:'),
('B06001_001M',
'B06001\.  PLACE OF BIRTH BY AGE IN THE UNITED STATES',
'Margin Of Error For!!Total:'),
('B06001_002E',
'B06001\.  PLACE OF BIRTH BY AGE IN THE UNITED STATES',
'Under 5 years'),
('B06001_002M',
'B06001\.  PLACE OF BIRTH BY AGE IN THE UNITED STATES',
'Margin Of Error For!!Under 5 years'),
('B06001_003E',
'B06001\.  PLACE OF BIRTH BY AGE IN THE UNITED STATES',
'5 to 17 years')
]
```

### 步骤 4：基于列表变量创建所需变量

确定所需变量后，需要将它们放入一个 Python 列表中。以下是包含我们需要的每个年龄段人口的 Python 列表：

```python
cns_vars = [
"B06001_001E","B06001_002E","B06001_003E","B06001_004E",
"B06001_005E","B06001_006E","B06001_007E","B06001_008E",
"B06001_009E","B06001_010E","B06001_011E","B06001_012E"
]
```

`B06001_001E` 代表总人口，`B06001_002E` 代表 5 岁以下人口，`B06001_003E` 代表 5 至 17 岁人口，`B06001_004E` 代表 18 至 24 岁人口，`B06001_005E` 代表 25 至 34 岁人口，`B06001_006E` 代表 35 至 44 岁人口，`B06001_007E` 代表 45 至 54 岁人口，`B06001_008E` 代表 55 至 59 岁人口，`B06001_009E` 代表 60 至 61 岁人口，`B06001_010E` 代表 62 至 64 岁人口，`B06001_011E` 代表 65 至 74 岁人口，`B06001_012E` 代表 75 岁及以上人口。

### 步骤 5：创建包含数据集中所需地理区域的元组列表

接下来，你需要在一个元组列表中定义地理需求。列表中的每个元组包含两个元素：第一个元素是你要使用的地理实体类型，第二个元素是你要返回的实际地理实体的 FIPS 代码。

在本例中，你需要印第安纳州按县划分的人口信息。使用以下代码定义需求：
```python
geographies = [('state', '18'),('county', '*')]
```
第一个元组定义了州的需求。使用值`'state'`将元组标识为“州”元组，并使用 FIPS 代码`18`标识印第安纳州。该元组将整个搜索范围限制在印第安纳州。如果你不知道印第安纳州的 FIPS 代码，可以使用`us`包通过以下代码查找该信息：
```python
import us
us.states.IN.fips
```
第二个元组用于定义要返回的县。使用值`'county'`告诉`censusdata`你要返回县，并使用星号，因为你要返回印第安纳州全部 92 个县。

### 步骤 6：使用`censusdata.download()`函数检索数据

以下代码从美国人口普查局获取所需的人口数据，并将数据放入名为`IN_POP_BY_COUNTY_BY_AGE`的数据框中：
```python
IN_POP_BY_COUNTY_BY_AGE = censusdata.download(
'acs5', 2015, censusdata.censusgeo(geographies),
cns_vars)
```
第一个参数用于定义要使用的数据源，在本例中为`acs5`调查。第二个参数用于定义要返回的年份。第三个参数用于定义地理需求。你在步骤 5 中创建的包含地理需求的列表将在此参数中传递给`censusdata.censusgeo()`函数。最后一个参数是`cns_vars`列表。`cns_vars`是你在步骤 4 中创建的变量列表，用于标识要下载的变量。

### 步骤 7：重置步骤 6 中创建的数据框的索引

地理信息包含在数据框的索引中，但该信息需要位于专用列中，以便 Power BI 使用。使用数据框的`reset_index`方法将索引的值放入专用列。代码如下：
```python
IN_POP_BY_COUNTY_BY_AGE = IN_POP_BY_COUNTY_BY_AGE.reset_index()
```
运行上述代码的结果是，索引将被移动到一个名为`index`的列中。现在，该信息将可供 Power BI 使用。

### 步骤 8：定义新列名

步骤 4 中标识的变量将用于命名返回数据集中的列。这些名称有些晦涩难懂，不能很好地表示它们所代表的内容。`pandas`包允许你使用更有意义的名称重命名列。以下 Python 字典将为`pandas`提供重命名需求：
```python
new_names = {
'index':'Geography','B06001_001E':'Total',
'B06001_002E':'Under 5','B06001_003E':'5 to 17',
'B06001_004E':'18 to 24','B06001_005E':'25 to 34',
'B06001_006E':'35 to 44','B06001_007E':'45 to 54',
'B06001_008E':'55 to 59','B06001_009E':'60 and 61',
'B06001_010E':'62 to 64','B06001_011E':'65 to 74',
'B06001_012E':'75+'
}
```
上述数据是一个 Python 字典，包含用于重命名列的键/值对。键/值对中的`keys`代表旧列名，`values`代表新列名。

### 步骤 9：重命名列

使用以下代码重命名列：
```python
IN_POP_BY_COUNTY_BY_AGE = IN_POP_BY_COUNTY_BY_AGE.rename(
columns=new_names)
```
使用`IN_POP_BY_COUNTY_BY_AGE`数据框的`rename`方法来重命名列。`columns`参数使用`new_names`字典获取重命名列所需的信息。

### 步骤 10：将脚本复制到 Power BI

以下是完整的脚本：
```python
import pandas as pd
import censusdata
cns_vars = [
"B06001_001E","B06001_002E","B06001_003E","B06001_004E",
"B06001_005E","B06001_006E","B06001_007E","B06001_008E",
"B06001_009E","B06001_010E","B06001_011E","B06001_012E"
]
geographies = [('state', '18'),('county', '*')]
IN_POP_BY_COUNTY_BY_AGE = censusdata.download(
'acs5', 2015, censusdata.censusgeo(geographies),
cns_vars)
IN_POP_BY_COUNTY_BY_AGE = IN_POP_BY_COUNTY_BY_AGE.reset_index()
new_names = {
'index':'Geography','B06001_001E':'Total',
'B06001_002E':'Under 5','B06001_003E':'5 to 17',
'B06001_004E':'18 to 24','B06001_005E':'25 to 34',
'B06001_006E':'35 to 44','B06001_007E':'45 to 54',
'B06001_008E':'55 to 59','B06001_009E':'60 and 61',
'B06001_010E':'62 to 64','B06001_011E':'65 to 74',
'B06001_012E':'75+'
}
IN_POP_BY_COUNTY_BY_AGE = IN_POP_BY_COUNTY_BY_AGE.rename(
columns=new_names)
```
将整个脚本复制到 Power BI 的 Python 脚本编辑器中。默认情况下，返回的数据框名称将为`IN_POP_BY_COUNTY_BY_AGE`。如果要更改名称，需要进入 Power Query 的编辑模式进行修改。否则，如果对该名称满意，可以直接将数据集加载到 Power BI 数据模型中。

## 总结

在本章中，你学习了如何使用 R 和 Python 将美国人口普查局的数据直接导入 Power BI 数据模型。从外部来源（如美国人口普查局）引入不同的数据，可以极大地增强你的数据模型。你不仅限于美国人口普查局的 API，因为还有许多数据 API 可以让你以编程方式访问一些非常丰富的数据源。一旦将这些数据源添加到 Power BI，它们就可以成为创建出色可视化效果的基础。

