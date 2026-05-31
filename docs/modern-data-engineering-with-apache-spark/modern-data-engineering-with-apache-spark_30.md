# 复制应用程序配置
COPY conf /opt/spark/app/conf
EXPOSE 4040
```

`COPY` 指令只是在构建时将配置从应用程序根目录移动到容器中。这样，您可以将任何相关配置移动到一个更合适的最终位置，而不是依赖 `spark-submit` 的运行时配置。这样做的方式更不容易出错。

### 练习 11-3：总结

本练习是展示如何在 Apache Spark 生态系统中处理可序列化结构化数据的最后一步。您已经完成了处理可序列化结构化数据的完整流程：从消息（protobuf）定义、编译、随机记录生成，到以端到端模块化方式写入（生产）和读取（消费）消息。我希望这能激发您关于如何以可互操作方式处理数据的一些新思路，而不必退回到 JSON 或 CSV。

## 总结

Apache Kafka 是数据工程师的瑞士军刀。它为处理近实时的丰富数据流提供了一个可靠的平台。虽然在 Kafka 和 Spark 生态系统中有多种处理数据的方式，但值得一提的是，使用显式类型在这些独立系统之间提供了丰富的数据契约，可以为构建可靠且更重要的是可互操作的数据系统提供更坚实、更稳定的基础。

在下一章中，我们将继续使用结构化流式处理，重点放在更高级的聚合和分析处理上。如果您准备好了，让我们开始吧。

# 12. 分析处理与洞察

在整本书中，您一直专注于构建数据工程工具箱，并学习处理与转换数据（包括静态数据和流数据）的强大技术。在学习 Spark 的同时，您也一直在动手学习使用容器以及操作与 Apache Spark 生态系统良好互操作的其他技术。

鉴于现代数据工程师身兼多职，职责涵盖了数据生态系统的广泛范围——从数据生产和摄取、数据验证和清理、数据传输和加载、批处理和流处理，到更分析性的职责如简单和复杂聚合，甚至流式分析处理与洞察，本章专注于填补与分析数据预处理相关的知识空白，并学习使用强大的聚合运算符。

## 练习与资料

本章的练习将向您介绍 Apache Spark 丰富的分析处理能力。为了充分利用本章内容，所有练习都可以使用 Apache Zeppelin 交互式运行。

本章分为以下两个动手实践部分：

* 使用常见的 Spark 函数进行分析预处理
* 分析处理与洞察工程

学习如何交互式地使用 Spark 的高阶函数，将使您能够编写和操作第 13 章中有状态的结构化流式处理分析应用程序。

练习资料

第 12 章的练习资料位于 [`https://github.com/newfront/spark-moderndataengineering/tree/main/ch-12`](https://github.com/newfront/spark-moderndataengineering/tree/main/ch-12)。位于 `ch-12/exercises` 目录中的 `README` 文件将为您提供一整套设置说明。




### 环境搭建

再次重申，我们将依赖老伙计 Apache Zeppelin，不过这次你将构建一个名为 `zeppelin-spark` 的 Zeppelin 容器。你可能会想，为什么需要创建一个不同的容器呢？很高兴你能这么问。

在整本书中，你一直需要使用*卷挂载*和*环境变量*，在运行时将本地 Apache Spark 接入 Zeppelin 容器。在第 10 章中，你应该还记得，我们迁移到了使用基础的 Spark 容器来构建一致的容器化 Spark 应用程序。鉴于本地数据平台的复杂性日益增加，并且要确保应用程序在运行时拥有一致的环境，这一步是必要的。到目前为止，Zeppelin 要求你自带 Spark，但这个新容器将把官方 Zeppelin 容器与你过去几章一直在使用的基础 Spark 容器结合起来。

*Zeppelin-Spark 目录布局和容器依赖*

```
ch-12
├── docker
│   ├── README.md
│   └── zeppelin-spark
│       ├── Dockerfile
│       ├── conf
│       │   ├── interpreter.json
│       │   └── zeppelin-env.sh
│       └── spark
│           ├── conf
│           └── jars
```

在终端中，切换到 `docker/zeppelin-spark` 目录。位于该目录下的 *Dockerfile* 展示了 Docker 多阶段构建模式，它实质上是从基础 Spark 容器中精选资源，同时也在核心的 `apache/zeppelin` 容器之上添加资源。该文件的内容如代码清单 12-1 所示。

```
ARG spark_image=spark-3.1.2-jre-11-scala-2.12
FROM newfrontdocker/apache-spark-base:${spark_image} as spark
FROM apache/zeppelin:0.9.0
COPY --from=spark /opt/spark /opt/spark
COPY --from=spark /usr/local/openjdk-11 /opt/openjdk-11
COPY ./spark/conf/hive-site.xml /opt/spark/conf/
COPY ./spark/conf/spark-defaults.conf /opt/spark/conf/
COPY ./spark/jars /opt/spark/user_jars/
