## 7. 高级主题

本章深入探讨了保护和管理 Google Cloud Platform (`GCP`) 资源的高级方面、使用 `Google Cloud Source Repositories` 进行版本控制，以及强大的数据集成工具：`Dataplex` 和 `Cloud Data Fusion`。

我们首先强调使用身份与访问管理 (`IAM`) 进行安全的资源管理，涵盖角色、权限以及实现强访问控制的最佳实践。

我们将 `Google Cloud Source Repositories` 作为一个托管版本控制系统进行探讨，用于可扩展的代码管理和协作。`Dataplex` 被介绍为一个数据编织平台，有助于数据发现、谱系和治理，而 `Cloud Data Fusion` 则简化了数据管道的创建和管理。

在整个过程中，我们将获得增强 `GCP` 安全性、简化代码协作以及优化数据集成以满足各种处理需求的见解。

### 使用 IAM 保护和管理 GCP 资源

`IAM`（身份与访问管理）是 Google Cloud Platform (`GCP`) 提供的一项服务，用于简化对 `GCP` 资源的访问管理。`IAM` 使您能够控制资源的可访问性，并定义个人可以对这些资源执行的操作。它提供了一个集中管理访问控制的地方，从而更容易实施安全策略并降低未经授权访问的风险。

以下是 `IAM` 相关的一些关键概念：

1.  资源：可以应用 `IAM` 策略的 `GCP` 实体，例如项目、存储桶或实例。

2.  角色：角色是权限的集合，指定了对特定资源允许执行的操作。这些角色可以分配给用户、组或服务账号，从而确定其操作范围。

3.  成员：可以被分配角色的身份。成员可以是用户、组或服务账号。

4.  策略：一组角色绑定，定义了谁对资源拥有什么权限。

以下是 `IAM` 的一些主要特性：

1.  基于角色的访问控制 (`RBAC`)：`IAM` 使用 `RBAC` 来控制对资源的访问。`RBAC` 允许您定义角色并将其分配给用户、组或服务账号。

2.  自定义角色：`IAM` 提供了具有预定义权限的预定义角色，但您可以创建适合您特定需求的自定义角色。这允许您向用户或服务授予更细粒度的权限。

3.  **服务账号**：服务账号是一种特殊类型的账号，可供应用程序或服务用于向 GCP API 进行身份验证。它们可以像人类用户一样被授予权限，但不与特定个人绑定。

4.  **审计日志**：IAM 提供审计日志，允许您跟踪谁有权访问您的资源以及他们执行的操作。

5.  **细粒度访问控制**：IAM 提供对资源的细粒度访问控制。您可以控制对项目内特定资源的访问，甚至可以控制对特定文件夹或组织内资源的访问。

6.  **条件访问**：IAM 提供条件访问，允许您为访问资源的用户或服务设置条件。例如，您可以根据 IP 地址或设备设置条件。

总之，IAM 是一项强大的服务，允许您管理对 GCP 资源的访问。使用 IAM，您可以实施安全策略，降低未经授权访问的风险，并确保您的资源得到良好管理。

### 使用资源管理器 API 授予和移除 IAM 角色

1.  为确保您拥有必要的权限，请创建一个新项目，或选择一个您已拥有这些权限的现有项目。

2.  启用 API：

    - `Identity and Access Management (IAM) API`

    - `Cloud Resource Manager API`

3.  为项目中的一位主账号授予 `Logs Viewer` 角色。

![](img/606032_1_En_7_Fig1_HTML.jpg)

截图显示了 Google Cloud 网站页面的一个片段，左侧面板选中了“IAM 与管理”选项卡，该选项卡下显示了一个子菜单，其中“IAM”选项卡被高亮。

**图 7-1** IAM 与管理

4.  点击 `GRANT ACCESS`。

![](img/606032_1_En_7_Fig2_HTML.jpg)

截图显示了 Google Cloud 网站页面的一个部分，左侧面板选中了“IAM”选项卡，右侧显示 IAM 页面，顶部的 `GRANT ACCESS` 选项卡被高亮。

**图 7-2** `GRANT ACCESS`

5.  输入主账号的电子邮件地址。

![](img/606032_1_En_7_Fig3_HTML.jpg)

截图显示了“授予对实践项目 1 的访问权限”页面，其中包含“资源”、“添加主账号”和“分配角色”等标题，以及相应的输入字段和“IAM 条件”。页面底部的“保存”按钮被选中。

**图 7-3** `GRANT ACCESS` — 信息

6.  从 `Select a role` 菜单中，选择您要授予访问权限的产品或服务。

7.  如果您想撤销访问权限，可以按照以下步骤操作：

    **确定要撤销其角色的成员和角色**：在撤销 IAM 角色之前，您需要知道哪个成员被分配了该角色，以及您要撤销哪个角色。

    **导航到 IAM 页面**：点击左侧导航菜单中的 `IAM` 选项卡，打开 Google Cloud 控制台中的 IAM 页面。

    **查找成员**：使用页面顶部的搜索栏查找被分配了您要撤销的角色的成员。点击该成员的姓名以查看其被分配的角色。

    **移除角色**：找到您要撤销的角色，点击 `X` 图标以从该成员处移除该角色。将出现一个确认对话框。点击 `Remove` 以确认您要撤销该角色。

    **验证角色已被撤销**：撤销角色后，您应验证该成员不再拥有与该角色关联的资源的访问权限。您可以使用 Google Cloud 控制台或 `Cloud Identity and Access Management API` 来检查该成员的角色和权限。

需要注意的是，撤销 IAM 角色并不会从您的项目或组织中移除该成员。该成员可能仍被分配有其他角色，并且可能仍能访问某些资源。要完全从您的项目或组织中移除一个成员，您需要移除其所有被分配的角色和权限。

### 使用 Google Cloud Source Repositories 进行版本控制

Google Cloud Source Repositories 是 Google Cloud Platform (GCP) 提供的一项源代码管理服务。它允许您以安全且可扩展的方式存储和管理源代码，并提供高级功能，例如代码搜索、代码审查以及持续集成和持续部署 (CI/CD)。

以下是 Google Cloud Source Repositories 的一些关键特性：

- **可扩展性**：Google Cloud Source Repositories 可以处理任意大小的仓库，并能扩展以满足大型企业的需求。

- **代码搜索**：Google Cloud Source Repositories 提供高级代码搜索功能，允许您跨所有仓库搜索特定的代码片段、文件甚至函数。

- **代码审查**：Google Cloud Source Repositories 使审查代码更改和与其他开发者协作变得容易。您可以创建拉取请求、分配审查者并实时跟踪更改。

- **集成的 CI/CD**：Google Cloud Source Repositories 与 `Cloud Build` 和 `Cloud Functions` 等其他 GCP 服务集成，允许您自动构建和部署代码。

- **安全性**：Google Cloud Source Repositories 提供多层安全保护来保护您的代码，包括静态和传输中加密、基于 IAM 角色的访问控制以及审计日志。

- **灵活性**：Google Cloud Source Repositories 支持广泛的源代码管理工具和协议，包括 `Git`、`Mercurial` 和 `Subversion`。

Google Cloud Source Repositories 与其他 GCP 服务完全集成，允许您将其与其他 GCP 工具和服务无缝结合使用。它是管理源代码、与其他开发者协作以及自动化软件开发工作流的强大解决方案。

### Dataplex

`Dataplex` 是谷歌云于 2021 年推出的一款全新数据平台。它旨在通过提供统一、无服务器且智能化的数据平台，来简化现代数据管理的复杂性。`Dataplex` 允许用户通过单一界面管理跨多云和本地数据存储的数据。

该平台提供了许多关键特性，使其成为数据管理的宝贵工具：

1.  **统一数据架构**：`Dataplex` 提供统一的数据架构，使用户能够管理跨多个数据存储的数据，包括 `Google Cloud Storage`、`BigQuery`、`Cloud Spanner` 和 `Cloud SQL`。这使得用户能够将数据存储和管理在最适合其用例的数据存储中，同时保持数据视图的一致性。

2.  **无服务器架构**：`Dataplex` 构建于无服务器架构之上，这意味着用户无需担心管理基础设施。这使得设置和扩展数据管理系统变得容易，同时降低了成本并提高了敏捷性。

3.  **智能数据管理**：通过采用机器学习算法，`Dataplex` 自动化了多种数据管理任务，包括数据分类、数据沿袭和数据发现。这种自动化增强了数据的有效治理和管理，降低了出错的可能性，并提高了整体效率。

4.  **开放平台**：`Dataplex` 是一个开放平台，允许用户使用他们偏好的工具和技术。这使得它易于与现有系统和工作流程集成，并可通过自定义应用程序和服务扩展平台。

`Dataplex` 是一个强大的数据管理平台，它简化了现代数据管理的复杂性。凭借其多样化的功能，`Dataplex` 被证明是数据科学家、数据工程师以及其他处理海量复杂数据集的专业人士的宝贵工具。

本教程将指导您完成在 `Google Cloud Console` 中配置 `Dataplex` 的基本步骤。

#### 步骤 1：创建 Google Cloud 项目

1.  首先，您必须在 `Google Cloud Console` 中创建一个新项目。但是，如果您已有项目，请继续执行下一步。

2.  转到 [Google Cloud Console](https://console.cloud.google.com/)。

3.  点击页面顶部的项目下拉菜单，然后选择 `NEW PROJECT`。

4.  为您的项目输入一个名称，然后点击 `CREATE`。

#### 步骤 2：启用 Dataplex API

接下来，我们需要为我们的项目启用 `Dataplex API`。

1.  在 `Google Cloud Console` 中，导航到 `APIs and services`。

2.  点击 `+ Enabled APIs and services` 按钮。

![](img/606032_1_En_7_Fig4_HTML.jpg)

一张谷歌云网站页面的截图，左侧面板选中了 `A P Is and Services` 选项卡，该选项卡下显示了一个列出不同选项的子菜单。

图 7-4

`+ Enabled APIs and services`

3.  在搜索栏中搜索 `Dataplex`，然后从结果中选择 `Dataplex API`。

![](img/606032_1_En_7_Fig5_HTML.jpg)

一张截图展示了网页的一部分，搜索栏中填入了单词 `dataplex`，下方显示了搜索结果。

图 7-5

选择 `Dataplex API`

4.  点击 `ENABLE` 按钮以启用该 API。


![](img/606032_1_En_7_Fig6_HTML.jpg)

一张截图展示了谷歌云网页的一个片段，在产品详情标题下显示了 `Cloud Dataplex API`。下方的启用按钮已被点击。

图 7-6

启用 `Dataplex`

## 步骤 3：创建数据湖

1. 在云控制台中转到 `Dataplex`，然后点击 `Explore`。

![](img/606032_1_En_7_Fig7_HTML.png)

一张 `Dataplex API` 页面的截图，在谷歌云网站上选中了 `create lake` 按钮。

图 7-7

`Dataplex`——创建数据湖

2. 点击 `CREATE LAKE` 后，您可以看到下图。填写所需的详细信息。点击 `CREATE`。

![](img/606032_1_En_7_Fig8_HTML.jpg)


# Dataplex API 页面的截图，在“创建新湖”选项卡下显示了显示名称、ID 和区域等输入字段。

**图 7-8** 创建数据湖

1. 创建数据湖后，您将看到以下屏幕。

   ![](img/606032_1_En_7_Fig9_HTML.png)

   Dataplex 网页的截图，显示了显示名称、资产、状态、需要操作的资源、区域、上次修改时间和标签等详细信息。屏幕顶部的“创建”按钮被高亮显示。

**图 7-9** 数据湖已创建

1. 创建数据湖后，您可以添加区域。这些区域用于对非结构化和结构化数据进行逻辑分类。点击数据湖的名称，您将看到以下屏幕。

   ![](img/606032_1_En_7_Fig10_HTML.png)

   Dataplex 网页的截图，显示了需要操作的区域、需要操作的资产、湖 ID、显示名称和状态等详细信息。下方选中了“区域”选项卡，其中显示了显示名称、类型、状态、需要操作的资产、资产、数据位置、上次修改时间和标签等详细信息。

**图 7-10** 管理数据湖

1. 点击 `+添加区域`，您将看到以下屏幕。

   ![](img/606032_1_En_7_Fig11_HTML.jpg)

   Dataplex 网页的截图，在“创建新区域”选项卡下显示了显示名称、区域 ID、类型和描述等输入字段。

**图 7-11** 创建区域

1. 添加区域后，我们可以通过点击该区域向数据湖添加资产。

   ![](img/606032_1_En_7_Fig12_HTML.jpg)

   Dataplex 网页的截图，在标题“示例”下显示了各种详细信息，其中状态通过复选标记指示为“湖已激活”。下方选中了“区域”选项卡，其中显示了不同的详细信息，状态通过复选标记指示为“已激活”。

**图 7-12** 数据湖区域已创建

1. 您将看到以下屏幕。点击 `+添加资产`。

   ![](img/606032_1_En_7_Fig13_HTML.png)

   Dataplex 网页的截图，在标题“湖区域 1”下显示了各种详细信息，其中状态通过复选标记指示为“已激活”。下方选中了“资产”选项卡，其中显示了显示名称、资产类型、状态、发现状态、安全状态、资源状态、上次修改时间和标签等详细信息。

**图 7-13** 添加资产——屏幕 1

1. 从下图，您可以点击 `添加一项资产`。

   ![](img/606032_1_En_7_Fig14_HTML.png)

   Dataplex 网页的截图，在标题“向湖区域 1 添加资产”下列出了三个用于添加资产的选项，选中后会在页面右侧显示“添加一项资产”功能，该功能被高亮显示。

**图 7-14** 添加资产——屏幕 2

1. 在以下表单中填写信息。

   ![](img/606032_1_En_7_Fig15_HTML.jpg)

   Dataplex 网页的截图，在标题“向湖区域 1 添加资产”下列出了三个用于添加资产的选项，选中后显示了类型、显示名称、ID 和描述等输入字段。

**图 7-15** 添加资产信息

1. 这里我们正在添加一个 `BigQuery` 数据集。提供一个显示名称，该名称将自动与 ID 保持一致。向下滚动并浏览数据集。将显示一个数据集列表。选择该数据集。点击“完成”，然后点击“继续”。

2. 将出现发现设置。点击“继续”。

3. 检查您的资产，然后点击“提交”。

4. 如果一切顺利，您将看到下图。

   ![](img/606032_1_En_7_Fig16_HTML.jpg)

   Dataplex 网页的截图，在标题“湖区域 1”下显示了各种详细信息，其中状态通过复选标记指示为“已激活”。下方选中了“资产”选项卡，其中显示了不同的详细信息，状态和安全状态均通过复选标记指示为“已激活”。

**图 7-16** 资产状态

1. 为了避免产生任何费用，如果您不需要已创建的资源，则应将其删除。首先，删除资产。然后，您可以删除区域，最后删除数据湖。

### Cloud Data Fusion

`Cloud Data Fusion` 是一项完全托管的数据集成服务，允许用户在 `Google Cloud Platform` 上高效地构建和管理 ETL/ELT（提取、转换、加载/提取、加载、转换）管道。该服务旨在让数据分析师和数据工程师无需深厚的技术专业知识即可轻松创建、部署和管理大规模数据管道。

`Cloud Data Fusion` 附带一个基于 Web 的 UI，为构建和管理数据管道提供了图形界面。该 UI 包含一个用于连接源和目标的拖放界面，以及一个包含大量预构建连接器（用于常见数据源和数据接收器）的库。`Cloud Data Fusion` 还支持复杂的数据转换，例如连接、合并和过滤操作。

在底层，`Cloud Data Fusion` 使用 `Apache Spark` 和 `Apache Beam` 这两个开源数据处理框架来执行数据转换并在系统之间移动数据。这使得 `Cloud Data Fusion` 能够支持广泛的数据源，包括关系数据库、NoSQL 数据库和文件系统，并且能够扩展以处理大量数据。

借助 `Cloud Data Fusion`，用户可以轻松创建数据管道，在各种系统之间移动数据，例如从本地数据中心到 `Google Cloud`，或者在 `Google Cloud` 上的不同服务之间移动数据。`Cloud Data Fusion` 与 `BigQuery`、`Cloud Storage` 和 `Cloud Pub/Sub` 等其他 `Google Cloud` 服务集成，使得在这些服务之间移动数据变得容易。

总的来说，`Cloud Data Fusion` 可以简化构建和管理数据管道的过程，使用户能够专注于从数据中提取价值，而不是管理底层基础设施。

#### 启用或禁用 `Cloud Data Fusion`

`Cloud Data Fusion` 可以与现有项目一起使用，也可以创建一个新项目。对于现有项目，需要启用 `Cloud Data Fusion API`。

要启用 `Cloud Data Fusion API` 并创建新项目，请按照以下步骤操作：

1. 在 `Google Cloud Console` 中选择或创建一个项目。

2. 确保已为您的项目启用结算功能。

3. 导航到 `Cloud Data Fusion` 的 API 概览页面。

   ![](img/606032_1_En_7_Fig17_HTML.jpg)

   截图显示了 `Google Cloud` 网站页面的一个片段，在标题“产品详情”下显示了 `Cloud Data Fusion API`。下方的“启用”按钮被选中。

**图 7-17** Cloud Data Fusion

4. 点击 `启用` 按钮以激活 `Cloud Data Fusion API`。

   ![](img/606032_1_En_7_Fig18_HTML.jpg)

   截图描绘了“API 和服务”页面的一个片段，左侧面板选中了“已启用的 API 和服务”选项卡，页面右侧显示了 `Cloud Data Fusion API`。下方显示了服务名称、类型和状态等详细信息。

**图 7-18** API 已启用

您可以通过完成以下步骤，开始在您的项目中使用 `Cloud Data Fusion`。

#### 创建数据管道

1.  前往创建 `Data Fusion` 实例。

2.  输入一个实例名称。

3.  如果您使用的是 `Cloud Data Fusion` 6.2.3 或更高版本，请从 `授权` 字段中选择一个 `Dataproc` 服务账号，用于在 `Dataproc` 中运行您的管道。默认选项是 `Compute Engine` 账号。

提供所有必要详细信息后，只需点击 `创建` 按钮。请注意，实例创建过程可能需要最多 30 分钟才能完成。当 `Cloud Data Fusion` 正在创建您的实例时，您会在 `实例` 页面上实例名称旁边看到一个进度轮。一旦过程完成，该进度轮将变成一个绿色的勾选标记，表示实例已准备就绪。

![](img/606032_1_En_7_Fig19_HTML.png)

一张截图展示了 `Google Cloud` 网站页面的一个片段，其中从左侧面板选中了“实例”选项卡，该页面显示了实例名称、操作、版本、区域、可用区、版本和加密等详细信息。页面顶部的“创建实例”和“刷新”按钮被高亮显示。

**图 7-19** Data Fusion 实例

4.  点击 `查看实例`。

![](img/606032_1_En_7_Fig20_HTML.jpg)

一张 `Cloud Data Fusion` 页面的截图，显示了诸如 `wrangle`、`integrate`、`discover and govern`、`monitor` 和 `manage` 等模板。

**图 7-20** Cloud Data Fusion 用户界面

5.  通过 `Cloud Data Fusion Hub`，您可以在 `Cloud Data Fusion` 用户之间重用和共享示例管道、插件和解决方案。点击 `Hub`。

6.  点击左下角的 `管道`。

![](img/606032_1_En_7_Fig21_HTML.png)



## 操作步骤

1. 点击 `Cloud Data Fusion Quickstart` 管道，然后点击 `完成并自定义`。

![图 7-22 Data Fusion Quickstart 管道](img/606032_1_En_7_Fig22_HTML.png)

2. 点击 `部署`。

管道部署完成后，您可以访问其详细信息视图，在此视图中您可以执行以下操作：
*   查看管道的结构和配置。
*   手动运行管道，或配置计划或触发器以实现自动运行。
*   查看管道过去执行的摘要，包括执行时间、日志和指标。

3. 点击 `运行` 以执行您的管道。

4. 几分钟后，管道完成并将其状态转换为“成功”。此外，还会显示每个节点处理的记录数。

5. 要查看结果样本，请执行以下步骤：
    *   导航到 `BigQuery` 用户界面。
    *   打开您项目中的 `DataFusionQuickstart` 数据集。
    *   点击 `top_rated_inexpensive` 表。
    *   运行一个基本查询，例如 `SELECT * FROM `PROJECT_ID`.GCPQuickStart.top_rated_inexpensive LIMIT 10`，以查看数据样本。
    *   确保将 `PROJECT_ID` 替换为您项目的唯一标识符。

6. 为防止您的 `Google Cloud` 账户因本页面使用的资源而产生费用，请执行以下操作：
    *   删除您的管道在此快速入门期间写入的 `BigQuery` 数据集。
    *   删除 `Cloud Data Fusion` 实例。
    *   请注意，删除您的实例不会移除与您的项目关联的任何数据。
    *   （可选）如果需要，您也可以删除整个项目。

## 总结

本章探讨了与保护和管理 `Google Cloud Platform` (`GCP`) 资源、使用 `Google Cloud Source Repositories` 进行版本控制，以及两个强大的数据集成和管理工具：`Dataplex` 和 `Cloud Data Fusion` 相关的高级主题。

我们首先讨论了使用 `Identity and Access Management` (`IAM`) 保护和管理 `GCP` 资源的重要性。我们了解了 `IAM` 角色、权限和策略，以及它们如何允许管理员控制对 `GCP` 资源的访问。通过实施强大的 `IAM` 实践，组织可以确保其 `GCP` 资源的安全性和完整性。

接下来，我们深入探讨了 `Google Cloud Source Repositories`，这是一个版本控制系统，提供了一个可扩展且完全托管的仓库，用于存储和管理源代码。

接着，我们探索了 `Dataplex`，这是一个数据编织平台，使组织能够大规模地发现、理解和管理其数据资产。我们讨论了 `Dataplex` 的关键特性，包括数据发现、数据沿袭和数据治理。我们了解了 `Dataplex` 如何与其他 `Google Cloud` 服务（如 `BigQuery` 和 `Cloud Storage`）集成，以提供统一且高效的数据管理解决方案。

最后，我们学习了 `Cloud Data Fusion`，这是一个完全托管的数据集成服务，简化了构建和管理数据管道的过程。我们讨论了 `Cloud Data Fusion` 的功能，包括其用于设计管道的可视化界面、对广泛数据源和数据接收器的支持，以及内置的数据转换和清洗功能。我们还探索了 `Cloud Data Fusion` 与其他 `Google Cloud` 服务（如 `BigQuery` 和 `Dataproc`）的集成，以创建端到端的数据处理工作流。

在本章中，我们深入了解了高级主题，这些主题增强了我们有效保护和管理 `GCP` 资源、使用版本控制系统协作进行代码开发以及利用强大的数据集成和管理工具的能力。通过理解并运用 `IAM`、`Cloud Source Repositories`、`Dataplex` 和 `Cloud Data Fusion`，我们能够应对 `Google Cloud` 环境中与资源管理、版本控制和数据集成相关的复杂挑战。

凭借本章获得的知识和技能，我们可以自信地驾驭 `Google Cloud Platform` 的高级方面，实施稳健的安全措施，简化代码协作，并高效地管理和集成数据，以满足各种数据处理和分析需求。

### Hub 资源管理器

一张 Cloud Data Fusion 网页的截图，其中在 Hub 页面下选中了“常规”选项卡。Hub 资源管理器显示了诸如 `Amazon Kinesis Sink Plugin`、`Amazon Kinesis streaming source`、`Amazon S 3 client`、`Anaplan plugins`、`Argument Setter action plugin` 和 `Cassandra source and sink` 等模板。

![图 7-21 Hub 资源管理器](img/606032_1_En_7_Fig21_HTML.png)

![图 7-22 Data Fusion Quickstart 管道](img/606032_1_En_7_Fig22_HTML.png)

一张 Cloud Data Fusion 页面的截图展示了 `Data Fusion Quickstart` 流程图，如下所示：`N Y T best sellers` 指向 `parsing and trans`，再指向 `top-rated items`。

![图 7-22 Data Fusion Quickstart 管道](img/606032_1_En_7_Fig22_HTML.png)




