# 运行 Airflow

初始化完成后，你可以执行清单 8-4 中的命令。

```
docker compose up
清单 8-4
从 Airflow 工作目录运行 docker compose up 命令以启动本地 Airflow 集群
```

Docker Compose 过程将总共启动七个 Docker 容器。这是在本地环境中启动并运行一个功能完备的 Airflow 集群所需的全部组件。需要指出的一点是，与之前章节中我们使用 `run.sh` 脚本来启动或停止 Zeppelin 环境不同，`docker-compose up` 命令只会在终端窗口的生命周期内运行。如果你想将其作为长期运行的进程，接下来你将学习如何以分离模式运行 Airflow。

