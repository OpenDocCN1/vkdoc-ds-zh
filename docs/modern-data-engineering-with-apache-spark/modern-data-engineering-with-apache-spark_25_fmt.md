# 安装 Airflow

在命令行中，将当前工作目录切换到 `airflow` 目录。运行 `cd ch-08/airflow`。在 `airflow` 目录中，你需要执行一些常规维护任务，以确保流程顺畅。

## 添加目录

以下 Airflow 目录将被卷挂载到 Airflow 容器运行时中，从而让你能够将所有作业定义和运行时日志保留在简单的本地位置。在 `airflow` 目录中运行清单 8-1 中的命令。

```
mkdir ./dags ./logs ./plugins
```

清单 8-1：在 `airflow` 目录下添加 Dags、Logs 和 Plugins 目录。

现在进入下一项任务。

## 为 Docker Compose 添加环境变量

你是否知道可以将环境变量导出到 `.env` 文件中，该文件将在 Docker Compose 过程中被使用？`.env` 文件可用于将可配置的路径、用户、Docker 镜像、密码等内容注入到 Docker Compose 中。这使你能够复用 `docker-compose.yaml` 定义，而无需硬编码用户名、密码或任何可能最终成为安全隐患的内容。

例如，本章练习中的 `.env` 文件（位于 `/ch-08/airflow/.env`）已被预填充。如果你想要更新运行时与 Airflow 关联的用户 ID 和组 ID，或者甚至更改 Airflow UI 端口，你可以直接编辑该文件，或使用追加运算符 `>>` 来添加新变量，如清单 8-2 所示。

```
echo -e "AIRFLOW_UID=$(id -u)\nAIRFLOW_GID=0\n _AIRFLOW_WEB_SERVER_PORT=8088" >> .env
```

清单 8-2：使用 Shell（命令行）、`Echo` 和追加运算符修改 Airflow 的 `.env` 文件。

**端口冲突**
Airflow 默认会绑定到端口 `8080`，这与你的内部 Zeppelin 主机使用的端口相同。清单 8-2 中显示的更新通过将 Airflow 端口切换为 `8088`，使得这两个服务可以协同运行。如果你不想记住服务运行的端口，可以运行 `nginx` 并根据主机名进行路由。

## 初始化 Airflow

是时候下载、设置并初始化 Airflow 了。从 `ch-08/airflow` 工作目录，在命令行中执行清单 8-3 中的命令。这将准备好环境并引导数据库表，但你暂时还不会启动 Airflow 容器。

```
docker compose up airflow-init
```

清单 8-3：使用 Docker Compose 初始化 Airflow。

清单 8-3 中的 Docker Compose 初始化命令可能需要一些时间来下载所有依赖的 Docker 容器。接着，`airflow-init` 命令将激活并引导 Airflow 元数据数据库，该数据库存储了你所有的 Airflow 作业配置，然后设置 Redis 数据库。该数据库与作业调度器一起使用。Redis 是一种极快、高性能且流行的键值对和数据结构存储，Airflow 用它来管理调度和缓存。你将在第 10 章学习如何使用 Redis 来驱动你的第一个结构化流应用程序。

在初始化过程中，你会注意到大量日志输出。这个过程将运行数据库迁移、引导以及所有其他的初始化脚本和程序。如果你感兴趣（或者想从中获得编写自己初始化程序的灵感），可以滚动查看输出；否则，你就可以准备运行 Airflow 了。