# 本章总结

本章标志着本书的结束。在本章中，你重新审视了交互式编程的理念，并看到了一个交互式软件开发框架的雏形。你定义了用于将`Jupyter`置于结构良好的交互式软件应用程序核心的**项目根设计模式**和**软件设计模式**。你概述了通过交互式开发过程创建代码模块的步骤。最后，你使用`Docker Compose`和`Redis`在应用程序中构建了一个延迟作业处理系统。完成本章后，希望你已准备好并充满热情地开始构建自己的交互式应用程序。

## 脚注

1. Noble, William Stafford; “A Quick Guide to Organizing Computational Biology Projects,” PLOS Computational Biology, July 31, 2009, [`journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1000424`](http://journals.plos.org/ploscompbiol/article%3Fid=10.1371/journal.pcbi.1000424).
2. [`mikegrouchy.com/blog/2012/05/be-pythonic-__init__py.html`](http://mikegrouchy.com/blog/2012/05/be-pythonic-__init__py.html)
3. [`pandas.pydata.org`](http://pandas.pydata.org)
4. [`archive.ics.uci.edu/ml/datasets/Adult`](http://archive.ics.uci.edu/ml/datasets/Adult)
5. [`archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names`](http://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names)
6. [`ss64.com/bash/split.html`](https://ss64.com/bash/split.html)
7. [`github.com/krallin/tini`](https://github.com/krallin/tini)

## 索引

### A
- Amazon Web Services (AWS)
    - 账户创建
    - EC2 实例创建
        - Docker
        - IP 地址
        - 密钥对
        - 启动按钮
        - 安全组
        - 稳定的 Ubuntu 服务器
        - `t2.micro`类型
        - 入站规则
        - 密钥对配置
    - 控制面板
        - EC2 仪表盘
        - 导入 SSH 密钥过程
        - 安全组面板
- Anaconda

### B
- Bessel 函数
- 大数据

### C
- 基于云的版本控制系统
- 容器化
    - 应用程序
    - 虚拟化进程

### D
- 守护进程化的 Hello World
- 数据基础设施
- `DecisionTreeClassifier`
- `jupyter/scipy-notebook`镜像
- `KNeighborsClassifier`
- 局限性
- `LogisticRegression`
- 内存异常
    - 默认值创建
    - `make_classification`导入
    - `MemoryError`
    - 内存占用
    - 笔记本
    - Python 内核
    - 运行中的笔记本
    - 模型
    - 监控内存使用
    - `T2.micro`
- 数据存储技术
- Docker
    - 数据卷与持久化
        - 通过传统链接连接容器
        - 创建与查看
        - 迭代过程
        - 启动 Redis
        - 通过 JSON 转储传递字典
        - 通过字节串传递 numpy 数组
        - Redis 示例
        - Redis 与 Jupyter
    - MongoDB
        - 数据卷，创建与查看
        - 插入推文
        - 安装，与 Jupyter 验证
        - Mongo 与 Twitter
        - 新的 AWS `t2.micro`
        - 新的 AWS `t2.micro`用于 Docker
        - 作为持久化服务
        - 拉取`mono`镜像
        - `pymongo`
        - 结构
        - 按地理位置筛选推文
        - Twitter 凭证
    - PostgreSQL
        - 二进制类型与 numpy
        - 按名称连接容器
        - Docker 容器网络
        - 安装，与 Jupyter 验证
        - Jupyter、pandas 与`psycopg2`
        - Jupyter-PostgreSQL 连接
        - 加载数据
        - 最小化验证
        - 新数据卷，创建为持久化服务
        - `postgres`镜像，拉取
    - Redis
        - 拉取镜像
        - 序列化
            - Python 中的二进制编码
            - 格式与方法
- Docker 客户端
    - compose
        - 概念
        - 容器网络
        - 数据卷与持久化
            - 通过传统链接连接容器
            - 创建与查看
            - 迭代过程
            - 启动 Redis
            - 通过 JSON 转储传递字典
            - 通过字节串传递 numpy 数组
            - Redis 示例
            - Redis 与 Jupyter
        - 生态系统
    - 引擎
    - 容器/进程
    - 通过 Docker 容器的文件服务器
    - Hello, Docker!
    - 异构基础设施
    - 主机
    - 镜像与容器
    - Linux
    - Mac
    - 网络
    - 新的 AWS `t2.micro`
    - 非 root 用户
    - 注册表保存镜像
    - 仓库
    - `SimpleHTTPServer`
    - 工具箱
    - Ubuntu 系统
    - Windows
- Docker Compose
    - 应用程序
    - AWS
    - `ch9jupyterredis_default`
    - 完成计算
    - 目录创建
    - `docker-compose down`
    - `docker-compose.yml`
    - Docker 引擎
    - Docker Hub 注册表
    - 安装 jupyter 与 postgres
    - jupyter 环境变量
    - Jupyter Notebook 服务器
    - `jupyter_redis`
    - 多容器系统
    - 网络
    - Python 项目
    - `redis`
    - `requirements.txt`文件
    - 重启
    - 系统管理员/运维工程师
    - `this_jupyter`服务
    - `t2.micro`
    - 工具集
    - 版本
- Dockerfiles
    - Anaconda
    - `ARG`与`MAINTAINER`
    - 提交更改
    - 创建
    - Docker 构建缓存
    - 环境变量
    - `ENTRYPOINT`
    - `ENV`
    - 运行构建
    - `FROM`
    - gsl 镜像
    - 幂等性
    - `ipython`镜像
    - 构建定义
        - `conda`
        - 设计
        - GitHub
    - 新容器
    - 运行时命令
    - 源目录
    - `joshuacook/gsl`镜像构建
        - 本地仓库
        - 提交更改
        - 运行构建
    - `tini`
    - 微服务
    - `miniconda3`镜像设计
        - 本地仓库
        - 源目录
        - miniconda 安装
    - 仓库构建镜像
        - GitHub
        - 本地开发
    - 语法
    - 运行构建选项
    - `RUN`指令
    - 运行构建
    - 单一关注点容器
    - 无状态容器
- 领域特定语言 (DSL)

### E, F
- Elastic Compute Cloud (EC2)
- 引擎
    - alpine
    - Docker 镜像
        - 生态系统
        - 启动时间
        - 容器化的 Hello, World
        - 隐式与显式拉取
        - 工作站
- 临时容器
    - 扩展
    - 临时包
    - `ipython`魔法
        - shell 进程
        - 命令库
        - 通过`pip`安装包
        - 根环境
        - 半持久化更改
        - Twitter 安装

### G
- GitHub
- GNU Scientific Library (GSL)
    - `apt-get update && apt-get install`
    - 构建镜像
        - 创建
        - 定义
        - `FROM gcc`
        - `LABEL maintainer=@joshuacook`
        - 源目录

### H
- Hub
    - 替代公共注册表
    - 容器 ID 与命名空间
    - 镜像推送
    - `joshuacook/numpy`仓库
    - `jupyter/base-notebook`镜像
    - 本地缓存
    - 本地目录与上下文子目录
    - `numpy-notebook`
    - 拉取镜像
    - 推送命令
    - 仓库
        - 创建
        - 搜索现有仓库
    - 服务端应用
    - 带标签的镜像

### I
- ID 与命名空间
- 基础设施即代码 (IaC)
- 交互式应用
    - `bessel.py`
    - Jupyter (参见 Jupyter)
    - 启动`ipython`
    - 持久化
- 交互式软件开发
    - 计算生物学项目，组织
    - 数据库需求
    - 通过 git 管理项目
    - 向应用程序添加数据库
    - 数据科学特定
    - 默认 Rails 应用程序
    - 向应用程序添加延迟处理
    - 框架
    - 函数的文档字符串
    - 初始化项目
    - 模块
        - postgres 模块
        - Python 模块
        - PostgreSQL
    - 项目框架
    - 项目根设计模式
    - Python 模块，Jupyter
    - `this_postgres`工作原理

### J, K
- `joyvan`
- Jupyter
    - Bessel 函数计算
    - 容器构建上下文
    - 创建
    - 数据持久化
        - `docker ps`
        - `docker stats`
        - 环境文件
        - OAuth
        - 对象服务与数据卷
        - `t2.micro`
        - Twitter 认证
        - `TwitterStream`
    - C 编程语言
    - 执行编译后的二进制文件
    - `jupyter.env`
    - 最小化计算项目
    - `mongo_data`
    - 笔记本数据持久化演示
        - 分离模式
        - 文件系统
        - 前台模式
        - `jupyter/demo`镜像
        - 端口连接
        - 端口映射
        - Python 代码
        - 二次曲线
        - 随机时间序列数据
        - 通过`localhost`的服务器
        - 卷挂载
    - 固执己见的 Docker 栈
    - Python 模块源代码
    - Jupyter Notebook 服务器

### L
- 语言模型，`spacy`与`en`
- 库安装
- Linux
- Linux Containers (LXC) 项目

### M
- Mac
- MongoDB
    - 容器
    - 统计推文
    - `docker-compose build`
    - `docker-compose.yml`
    - `jupyter_mongo`
    - 单条推文
    - 数据卷，创建与查看
    - 插入推文
    - 安装，与 Jupyter 验证
    - Mongo 与 Twitter
    - 新的 AWS `t2.micro`
    - 新的 AWS `t2.micro`用于 Docker
    - 作为持久化服务
    - 拉取`mono`镜像
    - `pymongo`
    - 结构
    - 按地理位置筛选推文
    - Twitter 凭证

### N
- 笔记本镜像
    - `conda`环境
    - 默认环境
    - 高层概览
    - `jupyter/base-notebook`
    - `jupyter/scipy-notebook`镜像
    - 内核选择
    - Python 版本
    - 安全性
    - `semantic_analysis`镜像
    - 栈依赖图

### O
- 固执己见的 Jupyter 栈
    - Linux/Mac/Windows
    - 笔记本服务器
    - 端口映射
    - `-P` (发布所有) 标志
    - `scipy-notebook`
    - 服务器工具箱

### P, Q
- PostgreSQL
    - 二进制类型与 numpy
    - 按名称连接容器
    - 数据库
    - Docker 容器网络
    - 安装，与 Jupyter 验证
    - Jupyter、pandas 与`psycopg2`
    - Jupyter-PostgreSQL 连接
    - 加载数据
    - 最小化验证
    - 新数据卷，创建为持久化服务
    - `postgres`镜像，拉取
- Python 模块
    - `demo`
    - `lib/`目录
    - `pickle`模块
    - 程序更新
    - 使用 Jupyter

### R
- 读取-求值-打印循环 (REPL)
- Redis
    - 示例
    - 与 Jupyter
    - 作为持久化服务
    - 拉取镜像

### S
- 安全外壳 (SSH)
- 序列化
    - Python 中的二进制编码
    - 格式与方法

### T
- 带标签的镜像
    - 构建命令
    - 官方仓库
    - Python 镜像
    - 细微变化

### U
- Ubuntu 系统

### V
- 虚拟化方法
- 虚拟化硬件
- 虚拟机 (VM)

### W, X, Y, Z
- Windows
