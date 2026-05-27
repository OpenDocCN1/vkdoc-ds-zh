# 面向数据科学的 Docker

## 围绕 Jupyter Notebook 服务器构建可扩展且可伸缩的数据基础设施

约书亚·库克
美国加利福尼亚州圣莫尼卡

本书作者引用的任何源代码或其他补充材料，读者均可通过本书的产品页面在 GitHub 上获取，网址为 [www.apress.com/978-1-4842-3011-4](http://www.apress.com/978-1-4842-3011-4)。如需更详细信息，请访问 [www.apress.com/source-code](http://www.apress.com/source-code)。

ISBN `978-1-4842-3011-4`
电子版 ISBN `978-1-4842-3012-1`
[`doi.org/10.1007/978-1-4842-3012-1`](https://doi.org/10.1007/978-1-4842-3012-1)

美国国会图书馆控制号：2017952396

© 约书亚·库克 2017

本作品受版权保护。出版商保留所有权利，无论是全部还是部分材料，特别是翻译、重印、重用插图、朗诵、广播、以缩微胶片或任何其他物理方式复制，以及传输或信息存储与检索、电子改编、计算机软件，或现在已知或以后开发的类似或不同方法的权利。

本书中可能出现商标名称、徽标和图像。我们不会在每次出现商标名称、徽标或图像时都使用商标符号，而是仅以编辑方式使用这些名称、徽标和图像，以利于商标所有者，且无意侵犯商标权。

本出版物中对商品名称、商标、服务标志及类似术语的使用，即使未明确标识，也不应被视为对其是否受专有权利保护的看法。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑和出版商均不对可能出现的任何错误或遗漏承担法律责任。出版商对本书所含内容不作任何明示或暗示的保证。

印刷于无酸纸上

由 Springer Science+Business Media New York, 233 Spring Street, 6th Floor, New York, NY 10013 向全球图书贸易发行。电话 1-800-SPRINGER，传真 (201) 348-4505，电子邮件 orders-ny@springer-sbm.com，或访问 `www.springeronline.com`。Apress Media, LLC 是一家加利福尼亚有限责任公司，其唯一成员（所有者）是 Springer Science + Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是一家特拉华州公司。

献给我的妻子，艾琳。

## 引言

本书旨在教授 Docker 及其生态系统在数据科学领域应用的概念和技术。除了介绍核心的 Docker 技术（容器和镜像、引擎、`Dockerfile`），本书还讨论了如何使用 Jupyter Notebook 服务器和开源数据存储 `MongoDB`、`PostgreSQL` 及 `Redis` 构建更大的集成系统。

第一章引导读者使用 AWS `t2.micro` 完成本书所需的推荐硬件配置。第二章和第三章介绍了本书使用的核心技术 Docker 和 Jupyter，以及交互式编程的概念。第四章、第五章、第六章和第九章深入探讨了 Docker 生态系统的特定领域。第七章探讨了由 Jupyter 开发团队开发和维护的官方 Jupyter Docker 镜像。第八章介绍了三个开源数据存储的 Docker 镜像。第九章和第十章将所有内容整合在一起，使用 `Docker Compose` 将 Jupyter 连接到数据存储。

完成本书后，鼓励读者重读第三章和第十章，以开始发展自己的交互式软件开发风格。

本文提出的概念可能具有挑战性，尤其是在计算机资源和流程的抽象方面。话虽如此，本书不假设读者具备任何预备知识。我们尝试从基本原理出发构建讨论。考虑到这一点，读者应能熟练使用命令行，并具有冒险和好奇的精神。我们希望具有 Docker、Jupyter 或两者中级到高级理解的读者能够更深入地理解这些概念，并学习使用这些工具解决计算问题的新颖方法。

## 致谢

感谢 Mike Frantz、Gilad Gressel、Devon Muraoka、Bharat Ramanathan、Nash Taylor、Matt Zhou 以及 DSI Santa Monica 第 3 和第 4 期学员与我讨论本文中一些更抽象的概念。感谢 Chad Arnett 保持其独特风格。感谢 Jim Kruidenier 和 Jussi Eloranta 教会老狗新把戏。感谢我的父亲不断激励我，感谢我的母亲给予我对善良的坚定信念。感谢 Momlo 为我铺平道路，感谢 Dablo 的好奇心。感谢我的妻子艾琳对我的信任以及对我念叨“特征向量”一词的容忍。

## 目录

- 第一章：引言 1
  - “大数据” 1
  - 推荐的学习实践 2
  - 设置新的 AWS 账户 2
  - 配置密钥对 3
  - 数据的基础设施限制 14
  - 拉取 `jupyter/scipy-notebook` 镜像 15
  - 运行 `jupyter/scipy-notebook` 镜像 16
  - 监控内存使用情况 17
  - 多大的数据集会导致内存异常？ 17
  - 多大的数据集太大，无法用于拟合不同类型简单模型？ 22
  - 总结 27
- 第二章：Docker 29
  - Docker 不是虚拟机 30
  - 容器化 30
  - 一个容器化的应用程序 32
  - Docker 容器生态系统 32
  - Docker 客户端 33
  - 主机 34
  - Docker 引擎 34
  - Docker 镜像与 Docker 容器 34
  - Docker 注册表 35
  - 获取 Docker 36
  - Linux 版 Docker 36
  - Mac 版 Docker 40
  - Windows 版 Docker 40
  - Docker Toolbox 41
  - Hello, Docker! 42
  - Docker 中的基本网络 45
  - 总结 47
- 第三章：交互式编程 49
  - Jupyter 作为持久化交互式计算 49
  - 如何不进行交互式编程 49
  - 设置一个最小的计算项目 50
  - 编写用于评估贝塞尔函数的源代码 51
  - 使用 Docker 执行计算 51
  - 编译源代码 53
  - 执行编译后的二进制文件 54
  - 如何进行交互式编程 55
  - 使用 Docker 启动 `IPython` 55
  - 持久化 56
  - Jupyter Notebooks 57
  - 端口连接 63
  - Docker 中的数据持久化 65
  - 挂载卷 69
  - 总结 69
- 第四章：Docker 引擎 71
  - 检查 Docker 工作站 71
  - 容器中的 Hello, World 74
  - 将 `Echo` 作为服务运行 76
  - 隔离启动时间 77
  - 一个守护进程化的 Hello World 78
  - 总结 79
- 第五章：`Dockerfile` 81
  - 最佳实践 81
  - 无状态容器 81
  - 单一职责容器 82
  - 项目：一个 Docker 镜像仓库 82
  - 准备本地开发 82
  - 配置 `GitHub` 83
  - 使用 `Dockerfile` 构建镜像 83
  - `Dockerfile` 语法 83
  - 设计 `gsl` 镜像 84
  - Docker 构建缓存 87
  - Anaconda 88
  - 设计 `miniconda3` 镜像 88
  - `tini` 96
  - `ENTRYPOINT` 97
  - 设计 `ipython` 镜像 98
  - 将 `ipython` 镜像作为新容器运行 100
  - 总结 101
- 第六章：`Docker Hub` 103
  - `Docker Hub` 103
  - `Docker Hub` 的替代方案 103
  - `Docker ID` 和命名空间 104
  - 镜像仓库 104
  - 搜索现有仓库 105
  - 带标签的镜像 106
  - Python 镜像上的标签 107
  - 官方仓库 108
  - 推送到 `Docker Hub` 108
  - 创建新仓库 110
  - 推送镜像 111
  - 从 `Docker Hub` 拉取镜像 113
  - `Docker Hub` 上的带标签镜像 118
  - 总结 118
- 第七章：有主见的 Jupyter 栈 119
  - 高层概览 121
  - `jupyter/base-notebook` 122
  - Notebook 安全性 122
  - 默认环境 124
  - 管理 Python 版本 125
  - 使用 `conda` 环境扩展 Jupyter 镜像 127
  - 使用 `joyvan` 安装库 130

## 第八章：数据存储

### 序列化
- 序列化格式与方法 138
- Python 中的二进制编码 139

### Redis
- 拉取 `redis` 镜像 139
- Docker 数据卷与持久化 141
- 创建并查看新的数据卷 142
- 将 Redis 作为持久化服务启动 142
- 通过传统链接连接容器 143
- 将 Redis 与 Jupyter 结合使用 145
- 一个简单的 Redis 示例 147
- 跨 Notebook 跟踪迭代过程 148
- 通过 JSON 转储传递字典 148
- 以字节串形式传递 Numpy 数组 150

### MongoDB
- 设置新的 AWS `t2.micro` 152
- 为 Docker 配置新的 AWS `t2.micro` 153
- 拉取 `mongo` 镜像 153
- 创建并查看新的数据卷 153
- 将 MongoDB 作为持久化服务启动 154
- 验证 MongoDB 安装 155
- 将 MongoDB 与 Jupyter 结合使用 155
- MongoDB 结构 156
- `pymongo` 157
- Mongo 与 Twitter 158
- 获取 Twitter 凭证 159
- 按地理位置收集推文 162
- 将推文插入 Mongo 164

### PostgreSQL
- 拉取 `postgres` 镜像 165
- 创建新的数据卷 165
- 将 PostgreSQL 作为持久化服务启动 166
- 验证 PostgreSQL 安装 166
- Docker 容器网络 167
- 最小化验证 Jupyter-PostgreSQL 连接 170
- 按名称连接容器 171
- 将 PostgreSQL 与 Jupyter 结合使用 174
- Jupyter、PostgreSQL、Pandas 和 `psycopg2` 174
- 最小化验证 174
- 将数据加载到 PostgreSQL 中 175
- PostgreSQL 二进制类型与 Numpy 176

### 总结
- 总结 178

## 第九章：Docker Compose

### 安装 `docker-compose`
- 安装 `docker-compose` 179

### 什么是 `docker-compose`？
- Docker Compose 版本 181

### 构建一个简单的 Docker Compose 应用程序
- 使用 Compose 运行应用程序 183
- 带持久化的 Jupyter 和 Mongo 187
- 指定构建上下文 188
- 指定环境文件 189
- 数据持久化 190
- 使用 Compose 构建应用程序 191

### 通过实例类型扩展 AWS 应用程序
- 重启 Docker Compose 应用程序 199
- 完成计算 199
- 将推文编码为文档向量 201
- 将 AWS 实例类型切换为 `t2.micro` 202
- 从 MongoDB 检索推文并进行比较 203
- Docker Compose 网络 204

### 带持久化的 Jupyter 和 Postgres
- 指定构建上下文 207
- 使用 Compose 构建并运行应用程序 209

### 总结
- 总结 211

## 第十章：交互式软件开发

### 组织计算生物学项目的快速指南
- 交互式开发的项目框架 215
- 项目根目录设计模式 216
- 初始化项目 217
- 检查数据库需求 218
- 通过 Git 管理项目 224
- 向应用程序添加数据库 226

### 交互式开发
- 使用 Jupyter 创建 Python 模块 234
- 向应用程序添加延迟处理 236
- 扩展 Postgres 模块 242
- 更新 Python 模块 246

### 总结
- 总结 251

## 索引
- 索引 253

---

## 内容概览

- 关于作者 xv
- 关于技术审阅者 xvii
- 致谢 xix
- 引言 xxi
- 第一章：引言 1
- 第二章：Docker 29
- 第三章：交互式编程 49
- 第四章：Docker 引擎 71
- 第五章：Dockerfile 81
- 第六章：Docker Hub 103
- 第七章：有主见的 Jupyter 栈 119
- 第八章：数据存储 137
- 第九章：Docker Compose 179
- 第十章：交互式软件开发 213
- 索引 253

## 关于作者与关于技术审阅者

### 关于作者

约书亚·库克是一位数学家。他使用 Bash、C 和 Python 编写代码，并在地理空间预测建模、量子力学、语义搜索和人工智能领域从事过纯理论和应用计算工作。他还有 10 年在中学和大学阶段教授数学的经验。他的研究兴趣在于高性能计算、交互式计算、特征提取和强化学习。他随时愿意讨论正交性，或在一杯热饮或冷饮旁解释为什么 Fortran 是未来的语言。

### 关于技术审阅者

Jeeva S. Chelladhurai 在过去 9 年中一直担任 IBM GTS Labs 的 DevOps 专家。他是英国 PacktPub 出版的《学习 Docker》一书的合著者。他拥有超过 20 年的 IT 行业经验。他在技术管理和指导全球多元化团队构思和构建开创性电信产品方面经验丰富。他专攻 DevOps、自动化和云解决方案交付，重点关注数据中心优化、软件定义环境 (SDE) 以及使用最新的 Docker 技术进行分布式应用程序的开发、部署和交付。Jeeva 也是敏捷方法论、DevOps 和 IT 自动化的坚定支持者。他拥有印度马诺尼亚姆·桑达拉纳尔大学的计算机科学硕士学位，以及美国马萨诸塞州波士顿大学波士顿分校的项目管理毕业证书。除了本职工作外，他还撰写书籍章节并发表研究论文。他在为 IBM 解决方案架构师和顾问打造可复用的技术资产方面发挥了重要作用。他在技术论坛上就 DevOps 技术和工具发表演讲。他在班加罗尔主持着最大的开源社区之一 ([www.meetup.com/opensourceblr/](http://www.meetup.com/opensourceblr/))。他的 LinkedIn 个人资料可在 [www.linkedin.com/in/JeevaChelladhurai](http://www.linkedin.com/in/JeevaChelladhurai) 找到。

© 约书亚·库克 2017  
约书亚·库克  
*面向数据科学的 Docker*  
[`doi.org/10.1007/978-1-4842-3012-1_1`](https://doi.org/10.1007/978-1-4842-3012-1_1)