# 基于条件逻辑识别字符串中的模式

大多数字符串解析函数都可以通过 Power Query 的原生功能相对轻松地处理。但当你的字符串解析基于条件逻辑时，代码可能会变得冗长且难以理解。幸运的是，许多需要基于条件逻辑进行字符串解析的情况，可以通过 R 和 Python 中的正则表达式更简洁地处理。让我们通过虚构公司 MC Diesel 如何利用基于条件业务逻辑的正则表达式解析字符串来解决业务问题的例子来说明这一点。

Rodney 是 MC Diesel 的执行董事。他知道 MC Diesel 过去在制造的两个零件上出现过问题。这两个零件是燃油泵（零件号 #561769）和燃油喷射器（零件号 #561394）。问题集中在 AL、LA 和 OH 的工厂，并且只发生在 A 型和 B 型中。

Rodney 委托他的数据科学家 Annie Su 开发一个具有向下钻取功能的仪表板，以帮助他进一步诊断和监控问题。Annie 能够获得的唯一数据是一份报告，其中包含表 7-1 中列出的信息。

表 7-1 零件报告

| 日期键 | SKU | 班次 | 门店 ID | 已召回 |
| --- | --- | --- | --- | --- |
| 20190813 | 75197726D3 | 3 | 263 | 否 |
| 20191211 | 70136522D1 | 1 | 221 | 否 |
| 20190401 | 70195609A3 | 3 | 92 | 否 |
| 20191228 | 75102709B1 | 1 | 91 | 否 |
| 20190304 | 70162016C3 | 3 | 161 | 是 |
| 20191218 | 70136526D2 | 2 | 263 | 否 |
| 20190322 | 70162029D1 | 1 | 291 | 否 |
| 20191012 | 70136539C2 | 2 | 395 | 否 |
| 20190318 | 56176926B2 | 2 | 262 | 否 |

报告似乎不包含任何标识仓库位置或零件号的信息，而这些信息是分析所必需的。Annie 非常聪明，她记得 SKU 编号并非随意数字。她知道编号的每个部分都有含义。她知道前六位数字代表零件号，第 7 和第 8 位数字是产品制造仓库所在州的 FIPS 代码，第 9 位的字符代表型号，第 10 位的数字代表产品制造的班次。因此，一个 SKU 为 `56176922A2` 的物品代表以下含义：

-   燃油泵，因为前六位数字 `561769` 是燃油泵的产品编号
-   在路易斯安那州制造，因为第 7 和第 8 位数字 `22` 是路易斯安那州的 FIPS 代码
-   型号类型为 A，因为第 9 位的字符是 "A"
-   在第二班次制造，因为第 10 位的数字是 2

Annie 需要创建一个计算列，在满足以下条件时从 SKU 中返回*零件号*：

-   是在 AL、LA 或 OH 制造的型号为 A 或 B 的燃油泵。
-   是在 AL、LA 或 OH 制造的型号为 A 或 B 的燃油喷射器。

如果她使用 Power Query 执行此任务，生成的代码将非常冗长，但使用 R 或 Python 则会简洁得多。以下步骤说明了 Annie 如何使用 R 创建解决方案。

## 在 Power BI 中使用 R 基于条件逻辑识别字符串中的模式

我们将利用正则表达式，根据之前定义的条件来匹配产品。此示例将说明我们能够多么简洁地描述查找所需 SKU 所需的逻辑。如果必须使用 M 代码，同样的条件在 Power Query 中会非常冗长。让我们逐步了解这些步骤。

### 步骤 1：导入 tidyverse 和 stringr 包

第一步是导入所需的包。`tidyverse` 中的 `readr` 用于将 `ProductionOrders.csv` 文件的内容读入 R 数据框。`dplyr` 包用于处理数据框操作任务，`stringr` 包用于处理字符串操作任务。以下是导入上述包的代码：

```r
library(tidyverse)
library(stringr)
```

### 步骤 2：更改工作目录

使用 `setwd()` 函数将工作目录更改为包含 `ProductionOrders.csv` 文件的路径，如下代码所示：

```r
setwd("")
```

### 步骤 3：创建识别产品的函数

处理此任务的函数名为 `monitoredProducts`。以下是创建该函数的步骤：

1.  使用以下代码创建函数外壳：

    ```r
    monitoredProducts <- function(sku){
    }
    ```

    `monitoredProducts` 函数只需要一个参数 `sku`。

2.  接下来，您需要定义一个正则表达式，用于查找在 AL、LA 或 OH 制造且型号为 A 或 B 的所有燃油泵 (`561769`) 和燃油喷射器 (`561394`)。您还希望仅返回 SKU 的产品编号部分，而不是整个 SKU 编号。您可以通过在正则表达式中使用*正向先行断言*来实现这一点。

    网站 [`www.rexegg.com`](http://www.rexegg.com) 指出，正向先行断言断言字符串中当前位置之后的内容正是您要查找的模式。让我们查看此脚本中的正则表达式并分解该语句的含义。以下是相关的正则表达式：

    ```r
    pattern = "^(561769|561394)(?=(01|22|39)(A|B))"
    ```

    上述正则表达式的正向先行断言部分是 `(?=(01|22|39)(A|B))`。正向先行断言的结构是一个以 `?=` 开头的括号分组正则表达式。`?=` 之后的模式是必须位于前一个模式之后才能使匹配通过的模式。

    因此，`pattern` 变量中定义的正则表达式将在 SKU 编号中找到匹配项，如果它：
    -   以 `561769` 或 `561394` 开头
    -   前面是 AL、LA 或 OH 的州 FIPS 代码
    -   型号类型为 A 或 B

    当您在步骤 5 的函数中使用它时，这将更有意义。

3.  在此步骤中，您使用以下代码提取与正则表达式模式匹配的 SKU 部分：

    ```r
    mp = str_extract(sku, pattern = pattern)
    ```

4.  该代码使用 `str_extract()` 函数提取与您在步骤 2 中定义的正则表达式匹配的字符串。如果匹配，它将仅返回字符串的产品代码部分。让我们通过一个例子来说明它是如何工作的。如果您将 `56176939A2` 传递给上述函数，R 将返回 `561769`。这是因为该字符串不仅通过了第一个条件（以 `561769` 开头），而且还通过了正向先行断言测试，因为字符串的第 7-8 位是 `39`，这是可接受的州 FIPS 代码之一，并且第 9 位是 `A`，这是可接受的型号之一。相反，`56176910D2` 返回 `NA`，因为正向先行断言失败。州 FIPS 代码 `10` 不是您要查找的 FIPS 代码之一。型号也是如此，因为型号 D 不是您要查找的型号。

### 步骤 4：将 ProductionOrders.csv 文件中的数据读入 R 数据框

使用 `readr` 包，通过以下代码将 `ProductionOrders.csv` 文件的内容读入 R 并存储在 `df` 变量中：

```r
df <- read_csv("ProductionOrders.csv")
```

### 步骤 5：向 df 添加名为“受监控产品”的列

接下来，您编写代码，使用 dplyr 包中的 `mutate()` 函数执行一个简单的工作流程：向数据框添加一个名为 `Monitored Products` 的新列。以下是执行该操作所需的代码：

```r
df %>%
  mutate(`Monitored Products` = monitoredProducts(SKU))
```

上述代码使用 dplyr 中的 `mutate()` 动词创建 `Monitored Products` 列，该列基于将自定义函数 `monitoredProducts()` 应用于 SKU 列的结果。

### 步骤 6：将脚本复制到 Power BI

以下是完整的脚本：

```r
library(tidyverse)
library(stringr)
setwd("")
monitoredProducts <- function(SKU) {
  str_detect(SKU, "^(561769|561394)(?=(01|22|39)(A|B)\\d{1}$)")
}
df <- df %>%
  mutate(`Monitored Products` = monitoredProducts(SKU))
```

此脚本创建了一个名为 `df` 的数据框，可通过 `GetData` 中的 `R script` 编辑器将其添加到 Power BI 数据模型中。

## 在 Power BI 中使用 Python 基于条件逻辑识别字符串中的模式

在本节中，我们将使用 Python 完成刚才在 R 中执行的任务。与 R 示例类似，主要工具仍是正则表达式。你将在此示例中看到，Python 中正则表达式的实现方式与 R 不同。以下是在 Python 中执行该任务所需的步骤。

### 步骤 1：导入 pandas、re 和 os 库

此脚本中使用的三个库是 `pandas`、`re` 和 `os`。`pandas` 包用于将数据读入脚本，并用于向数据框添加新列。`re` 包用于利用正则表达式，而 `os` 包用于文件系统任务。用于执行导入的代码如下所示：

```python
import pandas as pd
import re
import os
```

### 步骤 2：更改工作目录

接下来，你需要使用 `os` 中的 `chdir()` 函数，将工作目录更改为计算机上 `ProductionOrders.csv` 文件所在的位置。以下代码片段展示了代码的样式：

```python
os.chdir("")
```

### 步骤 3：编译所需的正则表达式

接下来，你需要定义一个正则表达式，用于查找所有在 AL、LA 或 OH 创建且型号为 A 或 B 的燃油泵（561769）和喷油器（561394）。你还希望仅返回 SKU 的产品编号部分，而非整个 SKU 编号。你可以通过使用*正向先行断言*在正则表达式中实现这一点。

网站 [`www.rexegg.com`](http://www.rexegg.com) 指出，正向先行断言断言字符串中当前位置之后的内容正是你要查找的模式。让我们查看此脚本中的正则表达式，以分解该语句的含义。以下是使用*正向先行断言*的相关正则表达式：

```python
pattern = "^(561769|561394)(?=(01|22|39)(A|B))"
```

上述正则表达式的正向先行断言部分是 `(?=(01|22|39)(A|B))`。正向先行断言的结构是一个用括号分组并以 `?=` 开头的正则表达式。`?=` 之后的模式是必须跟在前面模式之后才能使匹配通过的模式。

因此，`pattern` 变量中标识的正则表达式在满足以下条件时将通过：

*   以 561769 或 561394 开头
*   前面是 AL、LA 或 OH 的州 FIPS 代码
*   型号为 A 或 B

当你在步骤 4 的函数中使用它时，这一点会更加清晰。定义正则表达式后，你通过使用 `re` 包中的 `compile()` 函数编译它，创建一个名为 `pattern` 的正则表达式对象。这一切通过以下代码完成：

```python
pattern = re.compile(r"^(561769|561394)(?=(01|22|39)(A|B)\d{1}$)")
```

### 步骤 4：定义返回受监控产品的函数

你将创建的用于识别受监控产品的自定义函数名为 `monitoredProducts`。以下是创建该函数的步骤：

1.  首先，你需要创建接受 SKU 作为其唯一参数的函数外壳。你可以使用以下代码实现：

    ```python
    def monitoredProducts(sku):
    ```

2.  接下来，你尝试使用之前定义的 `pattern` 对象创建一个匹配对象，使用以下代码：

    ```python
    m = pattern.match(sku)
    ```

    如果传递给它的 SKU 与 `pattern` 对象中编译的正则表达式匹配，则返回一个匹配组。如果传递给它的 SKU 未通过测试，则返回 `None`，这是 Python 中 null 的等价物。

3.  接下来，如果存在匹配，你从传递给步骤 2 中匹配函数的 SKU 编号中提取模式。你可以使用以下代码实现：

    ```python
    if m:
        return m.group(0)
    else:
        return "Not Monitored"
    ```

    如步骤 2 所述，当步骤 2 中的代码找到匹配时，会返回一个匹配对象（其布尔值为 `True`）。否则，返回关键字 `None`。上述 `if` 语句用于检查是否返回了匹配对象。如果返回了，则获取位置 0 的组，该组代表完整匹配。如果未返回，则函数返回“Not Monitored”。

### 步骤 5：将数据读入 Pandas 数据框

在此步骤中，你使用以下代码将 `ProductionOrders.csv` 的内容读入名为 `df` 的数据框：

```python
df = pd.read_csv("ProductionOrders.csv")
```

### 步骤 6：创建一个名为“Monitored Products”的新列

在此步骤中，你使用以下代码添加一个名为 `Monitored Products` 的列：

```python
df["Monitored Products"] = df["SKU"].apply(monitoredProducts)
```

上述代码将你创建的 `monitoredProducts()` 自定义函数应用于 SKU 列，并将结果分配给名为 `Monitored Products` 的新列。

### 步骤 7：将脚本复制到 Power BI

以下是完整的脚本：

```python
import pandas as pd
import re
import os
os.chdir("")
pattern = re.compile(r"^(561769|561394)(?=(01|22|39)(A|B)\d{1}$)")
def monitoredProducts(sku):
    m = pattern.match(sku)
    if m:
        return m.group(0)
    else:
        return "Not Monitored"
df = pd.read_csv("ProductionOrders.csv")
df["Monitored Products"] = df["SKU"].apply(monitoredProducts)
```

复制上述脚本，并通过 Power BI 中的 `GetData` 将其粘贴到 `Python editor` 中，以便将其添加到 Power BI 数据模型中。

## 总结

在本章中，我们介绍了通过 R 和 Python 在 Power BI 中可用的几种不同字符串操作方法。所涵盖的示例可能不会直接与你数据整理任务中遇到的情况相关，但我相信通过对代码进行少量修改，你可以找到将其中一些技术应用于你实际情况的方法。