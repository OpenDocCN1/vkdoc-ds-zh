# 配置你的 Azure 环境

本书将使用`Azure`的不同部分。`Microsoft 认知服务`将用于将人工智能应用于你的 Power BI 数据模型。此外，`数据科学虚拟机 (DSVM)`是本书推荐的开发环境。`DSVM`是可选的，但强烈推荐。为了能够运行本书中的许多示例，你需要一个配置了多种工具的环境，例如：

- `SQL Server 2017` 或更高版本
- 启用了 `R` 和 `Python` 的 `SQL Server Machine Learning Services 2017` 或更高版本
- `Python` 的 `Anaconda` 发行版
- `R` 编程语言
- `R Studio`
- `VS Code`
- `Power BI Desktop`

手动配置包含这些资源的环境可能是一项挑战，但如果你使用`DSVM`，大部分配置都会为你处理。在接下来的章节中，你将学习如何设置`Azure`，以便能够使用`Microsoft 认知服务`，你还将学习如何启动一个`DSVM`。

## 注册 Azure

在此处注册 Azure：[`https://azure.microsoft.com/zh-cn/free/`](https://azure.microsoft.com/zh-cn/free/)。你可以获得选定服务 12 个月的免费使用期，并在第一个月获得 200 美元的信用额度！

## 注册 Microsoft 认知服务

`Microsoft 认知服务`将用于在 Power BI 内部使用`Python`执行情感分析。你首先需要在`Azure`中设置好`Microsoft 认知服务`，然后才能从 Power BI 调用它。以下是设置该服务所需的步骤：

1. 登录到你的 Azure 门户。
2. 在搜索框中输入`Cognitive Services`，然后按 Enter。
3. 上一步操作应带你进入`认知服务`注册页面。点击`创建`按钮以启动注册流程。
4. 填写以下信息：
   - 名称
   - 订阅
   - 位置
   - 定价层
   - 资源组
5. 勾选`我确认我已阅读并理解以下通知`复选框。
6. 点击`创建`按钮。

请注意，使用`Microsoft 认知服务`会产生费用。要获取定价信息，请转到你的`Microsoft 认知服务`资源，在搜索框中输入`定价层`，然后在结果中点击它。选择你想要使用的定价层。这样做将带你进入一个页面，该页面会根据你的使用情况和`Azure`区域提供定价信息。本书中使用`Microsoft 认知服务`的练习相对便宜，你第一个月的信用额度足以支付其费用。

## 创建数据科学虚拟机 (DSVM)

首选的设置是从一个`数据科学虚拟机 (DSVM)`开始，然后将`DSVM`中未预装的其他资源添加到其中。这种设置非常推荐，因为完全按照本书中每个练习所需的方式配置环境所需的时间可能很长且充满挑战。使用`DSVM`可以让你在几分钟内配置好一个环境，而如果你尝试自己配置，可能需要花费数天时间进行反复试验。如果你决定自己配置环境，下一节将提供相关说明。以下是设置`DSVM`的说明。

### 在 Azure 中创建 DSVM 的步骤

1. 转到[`https://portal.azure.com`](https://portal.azure.com)。如果出现提示，请使用在步骤 1 中创建的凭据登录。
2. 点击左上角的`创建资源`。
3. 在搜索框中，输入`Data Science Virtual Machine - Windows 2019`。
4. 点击`创建`按钮。此操作将显示一个表单，你需要填写该表单以配置`DSVM`。你将进入`基本信息`选项卡。以下步骤将告诉你如何填写该表单。
5. `订阅`：选择你想要使用的订阅。它应默认为你在步骤 1 中设置的订阅。
6. `资源组`：如果你已有想要使用的资源组，请选择它。否则，为你的`DSVM`创建一个新的资源组。
7. `虚拟机名称`：你希望你的`DSVM`拥有的名称。
8. `区域`：离你较近的 Azure 区域。
9. `映像`：确保已选择`Data Science Virtual Machine – Windows 2016`。
10. `大小`：我使用`B4ms`，因为它是 16 GB 内存选项中较便宜的选择。内存对于`R`、`Python`和`Power BI`很重要。
11. `用户名`：`<创建用户名>`。
12. `密码`：`<创建密码>`。
13. `确认密码`：`<确认密码>`。
14. 点击`下一步：磁盘 >`。
15. `OS 磁盘类型`：选择`Standard SSD`。此选项足以满足我们的需求。
16. 点击`下一步：网络 >`。
17. 确保所有必填字段都已填写。你可以通过`***`来识别必填字段。它们应已填充默认值；如果没有，请点击它们下方的`新建`链接并接受默认设置。
18. 点击`下一步：管理 >`。
19. 接受默认设置并点击`下一步：高级 >`。
20. 接受默认设置并点击`下一步：标记 >`。
21. 点击`下一步：查看 + 创建 >`。
22. 你将看到你配置的`DSVM`的摘要。你还会看到运行`DSVM`的成本。如果你同意该配置，请点击`创建`按钮。

*请确保在每次使用后停止你的 DSVM，以免产生不必要的费用*。作为一项安全措施，你应该设置自动关机，以便在超过指定时间后，如果你的`DSVM`仍在运行，它将自动关闭。执行以下步骤来设置自动关机：

1. 在`Azure 门户`中进入你的`DSVM`机器。
2. 在搜索框中输入`auto-shutdown`，然后在列表中选择它。
3. 通过在`已启用`按钮中选择`开`来启用自动关机。
4. 在`计划关机`文本框中，选择你想要自动关机的时间。
5. 在`时区`组合框中，选择你想要基于的时间的时区。
6. 如果你希望收到通知，告知你`DSVM`即将关闭，你可以打开`在自动关机前发送通知`。通知将发送到你填入`电子邮件地址`文本框的邮箱。

## 在 DSVM 中配置 R

你将使用与已安装版本不同的`R`发行版。我们将使用的`R`发行版是`Microsoft R Open (MRO)`。这个`R`发行版与`CRAN`上的发行版完全兼容，但它附带了一些增强功能，可以提高某些类型计算的性能，并包含许多额外的工具。执行以下步骤在你的`DSVM`中下载`MRO`：

1. 获取 Power BI 服务中正在使用的`R`版本。你可以在以下 Microsoft 文档中找到该信息：[`https://docs.microsoft.com/zh-cn/power-bi/visuals/service-r-visuals`](https://docs.microsoft.com/zh-cn/power-bi/visuals/service-r-visuals)。
2. 在`DSVM`中打开一个浏览器，并访问以下网站：[`https://mran.microsoft.com/open`](https://mran.microsoft.com/open)。`DSVM`中预装了两个浏览器。它们是`Microsoft Edge`和`Firefox`。
3. 点击右侧的`下载`按钮，你将进入下载页面。进入下载页面后，点击位于页面右侧的`Past Releases`链接。点击该链接将带你进入一个包含所有先前版本`Microsoft R Open`链接的页面。点击 Power BI 服务正在使用的版本的链接。
4. 选择`Windows`的下载。
5. 执行下载。
6. 在`DSVM`中打开`R Studio`。
7. 选择`工具` ➤ `全局选项`。验证你刚刚安装的`MRO`发行版是否已被选中。如果没有，请点击`更改...`按钮，你应该会看到它作为选项之一。选择它，然后点击`确定`。

### 在 DSVM 中配置 Python

使用 DSVM 的好处之一是，它预装了一个非常适合数据分析的 Python 发行版。该发行版名为`Anaconda`。`Anaconda`版的 Python 预装了超过 1500 个在数据分析领域流行的库。它还附带了一个名为`conda`的包管理和环境管理系统。由于`conda`管理包依赖关系的方式，推荐通过`conda`安装库。`conda`中的环境管理系统可以轻松地为特定版本的 Python 创建一个隔离副本，其中包含特定版本的 Python 库。

让我们在 DSVM 中为本书创建一个专用的 Python 环境，并将其命名为`pbi`。为此，您需要执行以下步骤：

1. 登录 DSVM。
2. 点击 Windows 标志旁边的搜索栏，输入`cmd`，打开命令提示符。
3. 输入以下代码，创建一个基于 Python 3.7 的名为`pbi`的 conda 环境：

```
conda create -n pbi python=3.7
```

选择使用 Python 3.7 是基于从以下 Microsoft 文档中获得的信息：[`https://docs.microsoft.com/en-us/business-applications-release-notes/october18/intelligence-platform/power-bi-service/pervasive-artificial-intelligence-bi/python-service`](https://docs.microsoft.com/en-us/business-applications-release-notes/october18/intelligence-platform/power-bi-service/pervasive-artificial-intelligence-bi/python-service)。根据该文档，Power BI 服务与 Python 3.x 兼容，因此当前的 3.x 版本的 Python 应该是兼容的。

现在，我们有了一个可以在本书中用于 Python 开发的 Python 环境。

### 在 DSVM 中配置 SQL Server 机器学习服务

本书中的几个示例需要使用 SQL Server 机器学习服务 (`SSMLS`)。`SSMLS`提供的工具使您能够使用 R、Python 在数据库内部执行高级分析，以及一些便于处理大数据的工具。`SSMLS`还提供了一些由 Microsoft 构建的、您可以利用的预训练模型。上述功能并非默认功能，但`SSMLS`在 DSVM 中默认是启用的。如果您没有使用 DSVM，则需要启用它，相关说明可在此处找到：[`https://docs.microsoft.com/en-us/sql/machine-learning/install/sql-machine-learning-services-windows-install?view=sql-server-ver15`](https://docs.microsoft.com/en-us/sql/machine-learning/install/sql-machine-learning-services-windows-install?view=sql-server-ver15)。本书中将使用的预训练模型可以作为后安装任务添加到您的 SQL Server 实例中。请访问此 URL 获取如何作为后安装任务安装预训练模型的说明：[`https://docs.microsoft.com/en-us/sql/machine-learning/install/sql-pretrained-models-install?view=sql-server-ver15`](https://docs.microsoft.com/en-us/sql/machine-learning/install/sql-pretrained-models-install?view=sql-server-ver15)。

### 安装 R 包

本书中的一些 R 脚本可能包含您机器上未安装的 R 包。在 R 中安装包非常简单直接。以下代码通过 R 控制台安装一个名为`data.table`的流行 R 包：

```
install.packages("data.table")
```

有时您可能希望一次安装多个包。例如，您可能希望同时安装 R 包`data.table`和`dplyr`。您可以通过创建一个包含两个元素的字符向量来实现，一个用于`data.table`，另一个用于`dplyr`，并将结果赋值给一个名为`pkgs`的变量。然后将该变量传递给`install.packages()`函数，如下所示：

```
pkgs <- c("data.table", "dplyr")
install.packages(pkgs)
```

R 字符向量数据类型是字符数据类型的一维数组。您将在本书中进一步了解此数据类型以及其他 R 数据类型。

当您在 Power BI 中创建 R 可视化时，需要注意 Power BI 服务正在使用的包版本。您可以在此 URL 获取 Power BI 服务中所有可用 R 包及其版本的列表：[`https://docs.microsoft.com/en-us/power-bi/service-r-packages-support`](https://docs.microsoft.com/en-us/power-bi/service-r-packages-support)。

如果您使用的是来自 CRAN 的 R 发行版，`install.packages()`将从您使用的仓库下载该包的最新版本。如果您使用的是 Microsoft R Open，它将根据快照日期安装最新的包。这两种方法都可能导致安装的版本与服务中的版本不同。要下载服务中使用的包版本，您首先需要从上述 URL 的页面获取包版本，然后需要使用`devtools`包来安装它。以下是一个使用`devtools`包从 CRAN 安装`ggplot2 0.9.1`的示例：

```
library(devtools)
install_version(
"ggplot2",
version = "0.9.1",
repos = "http://cran.us.r-project.org")
```

### 安装 Python 库

在 Python 中安装库有多种方法。在本书中，您将使用两种方法：`conda`安装和`pip`安装。在 Python 中安装库不像在 R 中安装包那样简单直接。在本书中，您将使用`conda`提示符来安装 Python 库。执行以下步骤，使用`conda`安装一个 Python 库：

1. 转到 Windows 图标右下角的 Windows 搜索栏。
2. 输入 Anaconda，返回的列表中应出现 Anaconda Prompt。点击它以启动 Anaconda Prompt。
3. 通过在命令提示符中输入以下代码，激活您用于 Power BI 的环境：

```
conda activate "<环境名称>"
```

强烈建议为与本书相关的 Python 开发使用一个环境。如何创建环境的说明在下一节中。

4. 使用 conda 通过以下代码安装包：

```
conda install <"包名称">
```

因此，如果您要安装 pandas，代码将如下所示：

```
conda install pandas
```

如果您想安装 pandas 1.0.4，您将使用以下代码：

```
conda install pandas=1.0.4
```

并非所有包都可用于`conda`安装。请访问以下 URL 获取可在 Python 3.6 中使用`conda`安装的包列表：[`https://docs.anaconda.com/anaconda/packages/py3.6_win-64/`](https://docs.anaconda.com/anaconda/packages/py3.6_win-64/)。本书中将使用的一个包在 conda 中不可用，但在 PyPI 中可用。该包名为`CensusData`。您必须使用`pip`来安装`CensusData`库，如下所示：

```
pip install CensusData
```

### 在 DSVM 中配置 Power BI

您需要在 Power BI Desktop 中进行一些配置才能启用 R 和 Python。这些步骤在本书的 GitHub 代码仓库中有详细说明。您还会找到关于如何在 Python 中创建和使用`conda`环境的说明。以下是本书代码仓库的 URL：[`https://github.com/Apress/adv-analytics-in-power-bi-w-r-and-python`](https://github.com/Apress/adv-analytics-in-power-bi-w-r-and-python)。

## 替代方案

`DSVM` 是最佳选择，但可能并非适用于所有人。如果情况如此，您将需要手动安装所需的软件。以下是您需要安装的软件的链接：

- 手动安装 Power BI：[`www.microsoft.com/en-us/download/details.aspx?id=58494`](http://www.microsoft.com/en-us/download/details.aspx%253Fid%253D58494)
- 手动安装 R Studio：[`https://rstudio.com/products/rstudio/download/`](https://rstudio.com/products/rstudio/download/)
- 手动安装 Microsoft R Open：[`https://mran.microsoft.com/download`](https://mran.microsoft.com/download)
- 手动安装 Anaconda：[`www.anaconda.com/products/individual`](http://www.anaconda.com/products/individual)
- 手动安装 VS Code：[`https://code.visualstudio.com/download`](https://code.visualstudio.com/download)
- 手动安装 SQL Server 2019 开发者版：[`www.microsoft.com/en-us/sql-server/sql-server-downloads`](http://www.microsoft.com/en-us/sql-server/sql-server-downloads)
- 手动安装 SQL Server 机器学习服务：[`https://docs.microsoft.com/en-us/sql/machine-learning/install/sql-machine-learning-services-windows-install?view=sql-server-ver15`](https://docs.microsoft.com/en-us/sql/machine-learning/install/sql-machine-learning-services-windows-install%253Fview%253Dsql-server-ver15)

如果您选择此路线，我强烈建议您使用运行`Windows Server 2016`或更高版本的虚拟机。以下是一个 YouTube 视频的链接，该视频提供了在`VirtualBox`中安装`Windows Server 2019`的分步说明：[`www.youtube.com/watch?v=ZjQSuyuN0nA&t=8s`](http://www.youtube.com/watch%253Fv%253DZjQSuyuN0nA%2526t%253D8s)。

## 向 SQL Server 机器学习服务添加 R 包

在本书的第 10 章中，您将学习如何通过`SQL Server 机器学习服务 2019（含 R）`将机器学习模型投入生产。执行此操作时，您需要确保所需的 R 包已加载到`SSMLS 2019`中。以下是一个 T-SQL 脚本，可用于查看您的`SSMLS 2019`实例中加载了哪些包：

```
EXECUTE sp_execute_external_script
@language=N'R',
@script = N'
packagematrix <- installed.packages();
Name <- packagematrix[,1];
Version <- packagematrix[,3];
OutputDataSet <- data.frame(Name, Version);'
WITH RESULT SETS ((PackageName nvarchar(250), PackageVersion nvarchar(max) ))
```

如果您需要的包不在列表中，则需要手动加载它。以下是在`SSMLS 2019`中加载 R 包的推荐步骤。

### 步骤 1：将 sqlmlutils 下载到您机器上的 Documents 文件夹

`sqlmlutils` 的下载地址位于此 URL：[`https://github.com/Microsoft/sqlmlutils/tree/master/R/dist`](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist)。从此 GitHub 仓库下载 zip 文件并将其保存到您的`Documents`文件夹。

### 步骤 2：在命令提示符中运行以下代码

以管理员身份打开命令提示符并运行以下代码：

```
R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.7.1.zip
```

如果您已将 `sqlmlutils` 安装在用户配置文件下的 `Documents` 文件夹中，则上述代码将生效。如果 `sqlmlutils` 保存在其他位置，则需要将文件路径更改为相应的位置。

### 步骤 3：加载所需的包

执行步骤 2 后，您将能够通过 `R Studio` 中的 R 脚本将包加载到 `SSMLS 2019`。以下是将 `dplyr` 包加载到 `SSMLS 2019` 的代码片段：

```
library(sqlmlutils)
connection <- connectionInfo(
server   = "server",
database = "database",
uid      = "username",
pwd      = "password")
sql_install.packages(connectionString = connection,
pkgs = "dplyr", verbose = TRUE, scope = "PUBLIC")
```

您不限于一次只加载一个包。假设您想一次加载两个包，`dplyr` 和 `data.table`。您可以通过创建一个包含这两个包的字符向量并将其用于 `pkgs` 参数来实现，如下所示：

```
library(sqlmlutils)
connection <- connectionInfo(
server="",
database="",
uid="",
pwd="")
pkgList <- c("dplyr","data.table")
sql_install.packages(connectionString = connection,
pkgs = pkgList, verbose = TRUE, scope = "PUBLIC")
```

## 向 SQL Server 机器学习服务添加必要的 Python 库

与`SQL Server 机器学习服务 2019（含 R）`类似，您需要了解如何在第 10 章中为`SQL Server 机器学习服务 2019（含 Python）`加载 Python 包。以下是您应采取的步骤，以使用 `sqlmlutils` 在 `SSMLS 2019` 中加载 Python 库。

### 步骤 1：将 sqlmlutils 下载到客户端机器的 Documents 文件夹

`sqlmlutils` 的下载地址位于此 URL：[`https://github.com/Microsoft/sqlmlutils/tree/master/Python/dist`](https://github.com/Microsoft/sqlmlutils/tree/master/Python/dist)。下载上述 URL 中的 zip 文件并将其保存到您的 `Documents` 文件夹。

### 步骤 2：打开命令提示符并运行以下代码

```
pip install "pymssql<3.0"
pip install --upgrade --upgrade-strategy only-if-needed c:\temp\sqlmlutils-0.7.2.zip
```

### 步骤 3：加载所需的包

执行步骤 2 后，您将能够通过 `VS Code` 中的 Python 脚本将包加载到 `SSMLS 2019`。以下是将 `pandas` 包加载到 `SSMLS 2019` 的代码片段：

```
import sqlmlutils
connection = sqlmlutils.ConnectionInfo(
server="", database="",
uid="", pwd=""))
sqlmlutils.SQLPackageManager(connection).install("pandas")
```

## 本地数据网关

`本地数据网关`是一种工具，用于实现从本地数据源到 Azure 云的安全数据传输。`本地数据网关`可以以两种不同的模式运行：个人模式和标准模式。如果您想将第 3 章至第 9 章中创建的解决方案部署到 Power BI 服务并定期运行，那么您需要以个人模式运行`本地数据网关`。截至撰写本文时，Power Query 中使用的 R 和 Python 脚本只能通过处于个人模式的`本地数据网关`运行。以个人模式使用`本地数据网关`的缺点是您的解决方案将不是企业级解决方案。它将是一个只有您能访问的解决方案，因为顾名思义，个人模式是为个人使用而设计的。

然而，在第 10 章中，您将学习如何通过`SQL Server 机器学习服务 (SSMLS)` 在企业级 Power BI 解决方案中同时利用 R 和 Python。当您使用 `SSMLS 2019` 时，您的 R 和 Python 代码将被封装在一个特殊的 T-SQL 存储过程中。T-SQL 存储过程可以在`本地数据网关`的标准模式下使用。在第 10 章中，您将学习如何将您在第 3 章至第 9 章中编写的 R 和 Python 代码重构为 `SSMLS 2019` 中使用的特殊存储过程的基础知识。请注意，R 可视化不依赖于`本地数据网关`，因为它们是使用位于 Power BI 服务中的 R 实例呈现的。

## 资源

本书涉及许多技术，因此不可能对所有技术进行全面覆盖。认识到这一点，我决定提供一个精选的资源列表，您可以使用这些资源来帮助填补缺失的部分。我还提供了本书代码仓库的链接，以及一些优秀的通用教育资源。

### 本书的代码仓库

本书中每个练习的代码都包含在本书的代码仓库中，网址为：[`https://github.com/Apress/adv-analytics-in-power-bi-w-r-and-python`](https://github.com/Apress/adv-analytics-in-power-bi-w-r-and-python)。完整的 R 和 Python 脚本按章节和主题分组存放在代码仓库中。此外，示例中使用的实际数据源或有关如何获取这些数据源的信息也位于代码仓库中。

### R 语言资源

**书籍**

- *《R for Data Science》*：这本出色的书籍以堪称最多产的 R 包创建者 Hadley Wickham 所推荐的方式，恰当地向你介绍 R。它通过利用属于 `tidyverse` 的包来实现这一点。该书可在此网址免费获取：[`https://r4ds.had.co.nz/`](https://r4ds.had.co.nz/)。

- *《An Introduction to Statistical Learning: With Applications in R》*：这本书很好地介绍了通过 R 进行机器学习所重要的统计概念。该书已出版数年，在 R 社区中非常受欢迎。它常被许多学院和大学用作本科和研究生课程的主要教材。你可以在此网址免费获取该书：[`https://faculty.marshall.usc.edu/gareth-james/ISL/ISLR%20Seventh%20Printing.pdf`](https://faculty.marshall.usc.edu/gareth-james/ISL/ISLR%2520Seventh%2520Printing.pdf)。

**网站**

- *RStudio*：该网站提供了丰富的 R 语言教育资源。要访问网站上的教育资源，请转到菜单栏中的 *Resource* 选项卡，你将看到访问免费网络研讨会、速查表和书籍的选项。这也是你获取推荐的 R 语言集成开发环境 `R Studio` 的网站。网站网址如下：[`https://rstudio.com/`](https://rstudio.com/)。

- *The R Graph Gallery*：该网站提供了使用 R 创建的可视化示例，并附有底层 R 代码。你可以使用此网站获取可视化灵感。网站网址如下：[`www.r-graph-gallery.com/`](http://www.r-graph-gallery.com/)。

- *R Bloggers*：一个很棒的网站，可以找到社区贡献的博客，涵盖关于 R 编程语言的各种主题。博客网址如下：[`www.r-bloggers.com/`](http://www.r-bloggers.com/)。

**教程**

- *dplyr 教程 第 1 部分*：这是一个关于 `dplyr` 的两部分视频系列的第一部分，由该包的主要作者 Hadley Wickham 讲解。该视频制作于 2014 年，但至今仍非常实用。教程网址如下：[`www.youtube.com/watch?v=8SGif63VW6E&t=15s`](http://www.youtube.com/watch%253Fv%253D8SGif63VW6E%2526t%253D15s)。

- *dplyr 教程 第 2 部分*：这是前一条中提到的系列视频的第二部分：[`www.youtube.com/watch?v=Ue08LVuk790`](http://www.youtube.com/watch%253Fv%253DUe08LVuk790)。

### Python 资源

**书籍**

- *《Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow》*：这是一本极好的资源，教你如何使用 Python 中的工具将*机器学习*和*人工智能*应用于你的数据。该书可在大多数书商处购买。

- *《Python for Data Analysis: Data Wrangling with Pandas, NumPy, and IPython》*：本书由 `pandas` 库的原作者 Wes McKinney 编写。`pandas` 库可以说是 Python 中最流行的数据处理库。该书可在大多数书商处购买。

**视频博客、播客和培训**

- *Data School*：这个 YouTube 频道由 Kevin Markham 创建。它提供了大量视频，涵盖如何使用诸如 `pandas`、`matplotlib`、`scikit-learn` 等 Python 库在数据科学中执行任务。他非常擅长以易于理解的方式分解复杂概念。这个优秀 YouTube 频道的网址如下：[`www.youtube.com/channel/UCnVzApLJE2ljPZSeQylSEyg`](http://www.youtube.com/channel/UCnVzApLJE2ljPZSeQylSEyg)。

- *Google’s Python Class*：这是一个相对较旧但仍然相关的 Python 入门教程，由 Google 提供。该教程在介绍数据结构以及构建坚实基础所需的其他必要主题方面做得很好。它还包含一个非常有用的正则表达式部分。这个为期两天的免费 Python 课程网站网址如下：[`https://developers.google.com/edu/python`](https://developers.google.com/edu/python)。

- *Talk Python to Me*：这是一个内容非常丰富的播客，涵盖了广泛的 Python 主题，其中许多与数据分析相关。播客网站网址如下：[`https://talkpython.fm/`](https://talkpython.fm/)。

**网站**

- *PEP 8*：Python 最好的特性之一是其代码与其他编程语言相比的可读性。与大多数语言不同，Python 强制使用严格的代码风格以促进代码可读性。代码风格在 Python 的 `PEP 8` 中有详细解释。你可以在此网址了解更多关于 `PEP 8` 的信息：[`https://pep8.org/`](https://pep8.org/)。

### Power BI 资源

#### 视频博客

- `Guy in a Cube`：这无疑是关于 Power BI 所有内容的首选视频博客。他们有大量的 YouTube 视频，涵盖 Power BI 的各个方面，包括 Power BI 管理、Power BI 数据建模和 Power BI 数据可视化。他们的 YouTube 频道网址如下：[`www.youtube.com/channel/UCFp1vaKzpfvoGai0vE5VJ0w`](http://www.youtube.com/channel/UCFp1vaKzpfvoGai0vE5VJ0w)。

#### 网站

- `SQLBI`：当涉及到 Power BI 中使用的 `DAX` 表达式和查询语言时，该网站的创建者是权威声音。他们的网站包含大量与 `DAX` 相关的信息和工具。你将找到涵盖所有 `DAX` 相关内容的详尽文章、在线培训以及诸如 `DAX Studio` 之类的工具。
- `Tabular Editor`：当你开始进行严肃的 Power BI 开发时，`Tabular Editor` 是一个必备工具。它是一个开源工具，很快将集成到 Power BI 中。要了解更多关于此工具的信息，请访问此网址，它将带你进入一个专门介绍这个出色工具的网站：[`https://tabulareditor.com/`](https://tabulareditor.com/)。

#### 书籍

- 《The Definitive Guide to DAX》：对于那些希望透彻理解 Power BI 中使用的 `DAX` 表达式和查询语言的人来说，这是一本“必读”书籍。该书可在大多数书商处购买。
- 《M Is for Data Monkeys》：这本书很好地介绍了 Power Query 中使用的 `M` 函数式编程语言。作者以非常易于理解的方式，出色地展示了使用 `M` 进行常见数据处理的配方。它提供了一个非常坚实的基础，使你能够处理更复杂的 `M` 主题。该书可在大多数书商处购买。

#### 播客

- `BIFOCAL`：一个很棒的播客，可以让你了解与 Power BI 相关的所有最新动态。你可以在流行的播客平台上找到这个播客。

### 通用资源

#### 书籍

- 《数据科学商业应用》（Foster Provost 与 Tom Fawcett 著）：本书以与编程语言无关的方式介绍数据科学原理。在数据科学社区中，对于希望学习如何将数据科学应用于商业领域的人而言，这是一本广受欢迎的书籍。该书可在大多数书商处购买。

#### 网站

- `Data Science Central`：这是数据科学领域的顶级网站之一。网站地址为：[`www.datasciencecentral.com/`](http://www.datasciencecentral.com/)。
- `Kaggle`：该网站最初是一个数据科学竞赛平台，但现已发展成为一个功能更为丰富的网站。它是寻找可用于机器学习和人工智能学习的数据集的绝佳去处。网站地址为：[`www.kaggle.com/`](http://www.kaggle.com/)。
- `ExcelTv`：Microsoft Excel 过去是，并且在可预见的未来仍将是数据科学领域的必备工具。`ExcelTv` 的团队制作了涵盖 Excel 各个方面的内容，帮助你熟练掌握该软件。他们的网站地址为：[`https://excel.tv/`](https://excel.tv/)。

#### 视频博客与教程

- `Excel on Fire`：该视频博客的创作者 Oz，可以说是我见过的在通过 `Power Query` 进行 Excel 数据整理方面最有热情的人。在过去几年中，他制作了大量 YouTube 视频，涵盖了他使用 `Power Query` 解决的常见数据整理任务。视频制作质量一流，而他化繁为简、深入浅出地讲解难以理解的 `Power Query` 概念的能力更是无人能及。你可以通过以下网址找到他的 YouTube 频道链接：[`www.youtube.com/user/WalrusCandy/featured`](http://www.youtube.com/user/WalrusCandy/featured)。
- `正则表达式教程`（Corey Schafer 制作）：这是一个出色的入门级但内容广泛的教程，涵盖了正则表达式的模式匹配。相信我，看完这个视频后，你不仅能理解正则表达式中那些奇怪字符的含义，还会体会到它们是多么强大和实用！视频博客链接如下：[`www.youtube.com/watch?v=sa-TUpSx1JA&t=554s`](http://www.youtube.com/watch%253Fv%253Dsa-TUpSx1JA%2526t%253D554s)。

#### 播客

- `Analytics on Fire 播客`：这是你每周获取商业智能/分析领域大师课的一站式平台，内容来自行业顶级影响者、客户和思想领袖。每周收听，即可获得这些寓教于乐的精彩讨论的幕后通行证。你可以在主流播客平台上找到该播客。
- `Data Skeptic`：这个与编程语言无关的播客以通俗易懂且有趣的方式介绍高级数据科学主题。他通过一些非常有趣的例子来讲解。你可以在主流播客平台上找到该播客。
- `Freakonomics`：学习数据科学的技术层面只是成功的一半。培养分析洞察力同样至关重要。这个播客能帮助你培养这项技能。你可以在主流播客平台上找到该播客。
- `SQL Data Partners`：这是一个非常有趣且富有教育意义的播客，涵盖了 `Microsoft 数据平台` 的各个主题。节目主持人对 `Microsoft 数据平台` 的方方面面都非常了解。有时他们还会展现出喜剧天赋。谁知道呢，也许他们将来能在喜剧界发展。哈哈。你可以在主流播客平台上找到他们的播客。
- `Storytelling with Data`：该播客由 Cole Knaflic 主持，是学习数据可视化最佳实践技巧的绝佳资源。她也是同名畅销书的作者。你可以在主流播客平台上找到她的播客。