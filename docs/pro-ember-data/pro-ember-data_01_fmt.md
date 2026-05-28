# Pro Ember Data：精通 Ember Data

![](img/978-1-4842-6561-1_CoverFigure.jpg)

`ISBN 978-1-4842-6560-4` `e-ISBN 978-1-4842-6561-1` [`doi.org/10.1007/978-1-4842-6561-1`](https://doi.org/10.1007/978-1-4842-6561-1) © David Tang 2021 本作品受版权保护。本书全部或部分内容的权利，包括翻译、重印、插图复用、朗诵、广播、微缩胶片或其他任何物理形式的复制、传输或信息存储与检索、电子改编、计算机软件，或使用目前已知或未来开发的类似或不同方法，均由出版商独家授权。本书中通用描述性名称、注册商标、商标、服务标志等的使用，即便未作特别说明，也不意味着这些名称不受相关保护性法律和法规的约束，因而可自由使用。出版商、作者和编辑可合理假定，本书中的建议和信息在出版之日是真实准确的。出版商、作者或编辑均不对书中所含材料或可能存在的任何错误或疏漏作任何明示或暗示的担保。出版商对已出版地图中的管辖权主张和机构归属持中立态度。本书由施普林格科学与商业媒体纽约公司（Springer Science+Business Media New York, 1 New York Plaza, New York, NY 10004）在全球发行。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问网址：`www.springeronline.com`。Apress Media, LLC 是一家加利福尼亚有限责任公司，其唯一成员（所有者）是施普林格科学与商业媒体金融公司（SSBM Finance Inc）。SSBM Finance Inc 是一家特拉华州公司。

*本书谨献给我的妻子伊莱恩·林（Elaine Lin），她曾多次因为我想要学习、研究或撰写关于 Ember 的内容而独自入睡。没有你的爱与支持，这本书将无法问世，我将永远心怀感激。*

## 引言
### 我为何撰写本书？

让我来告诉你我的故事。我之所以被 Ember 吸引，是因为它拥有诸多优点，例如约定优于配置、开箱即用的测试框架，以及稳定而不停滞的特性。我还非常喜欢 Ember 通过其配套库 Ember Data，提供了一套有主见的方式来与 API 协作并在 JavaScript 应用中处理数据。Ember Data 具备模型、模型关联和身份映射等功能，这些功能解决了我之前在构建客户端应用时遇到的挑战。我跟着各种教程学习使用 Ember Data，这些教程中的 API 都是预先搭建好的。后来，我决定在一个实际项目中使用 Ember，但我并未掌控该项目中的 API。该 API 的请求和响应负载与 Ember Data 预期的格式不匹配。例如，我的负载结构如下，这无法与 Ember Data 开箱即用：

```
{
"data": [
{ "id": 1, "firstName": "Yehuda" },
{ "id": 2, "firstName": "Tom" }
]
}
```

我进行了一些研究，并开始接触诸如适配器、序列化器、转换器、内嵌记录和快照之类的术语；查阅了庞大的 Ember Data API 文档；以及各种论坛上针对我无法实现的解决方案所提出的建议。我很快就感到不知所措，难以继续推进。我确实考虑过使用在服务中封装的老式 `$.ajax` 方法，但我知道那样会错失 Ember Data 中的许多优秀特性，我的应用也会缺乏约定性，而且我可能是在重复造轮子。我最终决定深入钻研 Ember Data，并尽可能多地学习它的相关知识，以便能够充分利用这个神奇库的强大功能。我很庆幸自己做出了这个选择。

那么，我为什么写这本书呢？我想帮助你学会如何让 Ember Data 适配你的 API，这样你就不会感到沮丧而放弃 Ember 或 Ember Data。一旦你理解了 Ember Data 的工作原理，我敢打赌你会像我一样欣赏并乐于使用它。
### 本书适合我吗？

本书适合所有有兴趣深入了解 Ember Data 的读者。也许你一直在使用 `$.ajax`，并希望开始使用 Ember Data。也许你已通过遵循默认约定的 API 使用过 Ember Data，并想进一步了解其内部机制。也许你想编写自己的适配器和序列化器来适配特定的后端。本书适合所有对学习 Ember Data 感兴趣的人。如果你以前从未使用过 Ember Data，第 1 章“Ember Data 概述”会提供一个简短的介绍，但我建议你首先阅读 Ember 指南^(¹) 中关于 Ember Data 的章节以熟悉它。
### 工具

要使用本书中的示例应用程序，请安装以下工具：
*   Git（[`https://git-scm.com/`](https://git-scm.com/)）。
*   Node.js（含 npm）。任何现代版本均可。我测试时使用的是 `v12.*`，它是本书写作时的 LTS 版本（[`https://nodejs.org/`](https://nodejs.org/)）。
*   Google Chrome 浏览器。
*   Ember Inspector Google Chrome 扩展程序（[`https://github.com/emberjs/ember-inspector`](https://github.com/emberjs/ember-inspector)）。
*   Ember CLI（[`https://ember-cli.com/`](https://ember-cli.com/)）。
### 约定

当我使用“JSON:API”时，我指的是 JSON:API 规范。^(²)
### API 文档

在本书中，我将引用 Ember Data 中的各种 API，例如类、函数和混入。如果在任何时候你不确定从哪里导入它们，请查阅 Ember Data API 文档^(³) 或 Ember Data 包 RFC。^(⁴)
### 勘误

我已尽最大努力确保本书没有拼写和错误。如果你确实发现了错误，请在本书的 GitHub 仓库（[`https://github.com/Apress/pro-ember-data`](https://github.com/Apress/pro-ember-data)）中提交问题。
### 联系我

你对本书中的任何内容有疑问吗？需要帮助让 API 与 Ember Data 协同工作吗？请随时通过 Twitter 联系我，我的账号是 `@iamdtang`，或者发送电子邮件至 `david@thejsguycom`。

## 致谢

我要感谢 Ember 团队以及所有为 Ember 做出贡献的人，感谢你们打造了这样一个高效且令人愉悦的现代 web 应用开发框架。我尤其要感谢克里斯·索伯恩（Chris Thoburn）与我分享他对 Ember Data 的理解。
### 关于作者
### 关于技术审稿人
### 脚注

1  

2  

3  

4