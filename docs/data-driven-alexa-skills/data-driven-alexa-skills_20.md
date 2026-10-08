## 17. 下一步是什么？

### 结论

在第一部分中，我们开始学习语音用户界面的世界。然后，我们研究了 Alexa 例程和蓝图。接着，我们创建并发布了一个身体质量指数 Alexa 技能。这也介绍了 Alexa 对话。

然后，在第二部分中，我们通过 90 年日历技能超越了“Hello World”。我们使用 `Botmock` 设计了 VUI，使用 Alexa 演示语言创建了可视化界面，并编写了后端 `lambda` 函数。接着，我们看到了几种对 `lambda` 函数和 Alexa 技能进行单元测试的方法。

最后，在第三部分中，我们从房地产和股票市场 API 中提取数据，构建了一个个人净资产技能。

### 下一步做什么？

在本书中，你已经接触了多种数据源，包括像 `DynamoDb` 这样的数据库，以及像 `Realty Mole Property API` 这样的接口。此外，你还创建并测试了作为云端后端应用的 `lambda` 函数。这些 `Lambda` 后端可以从任何地方拉取数据，并通过 Alexa 技能接口将其呈现出来。

下一步做什么？例如，你可以从犯罪数据浏览器（`https://crime-data-explorer.app.cloud.gov/`）中拉取 FBI 犯罪数据，创建一个 Alexa 犯罪数据浏览器。然后，或许再通过谷歌地图平台（`https://developers.google.com/maps/gmp-get-started`）添加地理空间数据，构建一个“这个城市的犯罪率是多少？”的技能。

你也可以与你喜欢的企业云应用的 API 配合使用，创建一个能处理该应用数据的 Alexa 技能。例如，开发一个通过 `Salesforce Lightning Platform REST API`（`https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_what_is_rest_api.htm`）查询和更新 `Salesforce` 的技能。

或许可以构建一个 Alexa 对话界面，通过 `ServiceNow` 的 `Now Platform Rest API`（`https://docs.servicenow.com/bundle/rome-application-development/page/integrate/inbound-rest/concept/c_RESTAPI.html`）来记录 `ServiceNow` 工单。

你可以通过 `Quandl API`（`www.quandl.com/tools/api`）添加股市详情，来扩展个人净资产技能。你可以让 Alexa 通过 `Alpaca API`（`https://alpaca.markets/docs/api-documentation/api-v2/`）为你进行交易。再结合 `AWS Sagemaker`（`https://sagemaker.readthedocs.io/en/stable/`）加入一些机器学习，你就能让 Alexa 成为你的算法交易伙伴和智能经纪人。想象一下这样的对话：

> *你：Alexa，今天有什么股票推荐？*
>
> *Alexa：你好 Dave，我推荐 `OTRK`。`Ontrak Inc.` 是一家利用人工智能和远程医疗技术实现虚拟化门诊治疗的医疗公司。其市值接近 5 亿美元，流通股为 1860 万股。2021 年 3 月，其股价从 93.46 美元跌至 24.39 美元。基于当前 32 美元的价格，你应该在 30 美元时做空，因为其股价仍处于下跌趋势。*
>
> *你：很好。在 30 美元时做空 1000 股 `OTRK`，止损设在 31 美元，目标价设在 28 美元。*
>
> *Alexa：以 30 美元的价格做空 1000 股 `OTRK`（`Ontrak Inc.`），止损设在 31 美元，目标价设在 28 美元。我需要您的 PIN 码来确认此交易。*
>
> *你：1234。*
>
> *Alexa：PIN 码已确认。我已下达订单。*

这本书只是你数据驱动型 Alexa 技能之旅的起点。我迫不及待想听听你会构建出什么。
