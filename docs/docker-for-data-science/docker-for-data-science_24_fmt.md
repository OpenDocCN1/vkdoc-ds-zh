# 什么是 docker-compose？

Docker Compose 是一款用于管理多容器 Docker 应用的工具。Compose 通过一个 `docker-compose.yml` 文件来定义应用。开发者维护一个包含应用源代码的目录，该目录可能包含 Python 或 C 语言文件库、零个或多个定义容器的 `Dockerfile`、CSV、feather 或 JSON 等原始数据文件，以及诸如 Python 项目的 `requirements.txt` 文件等其他配置脚本。`docker-compose.yml` 文件位于此目录的顶层。通过单个 `docker-compose` 命令即可构建、运行、停止和移除应用。`docker-compose` 工具在与 Docker 引擎通信时会引用该文件，就好像整个应用是一个单独的 Docker 容器一样。

## Docker Compose 版本

Docker Compose 文件语法目前为第 3 版。对于新项目，Docker 建议使用最新版本。尽管如此，所有版本都是向后兼容的，并且互联网上许多 `docker-compose.yml` 文件仍在使用早期版本。版本号在 `docker-compose.yml` 文件中作为第一个值指定，如清单 9-4 所示。如果未指定版本，则将使用第 1 版。

```
version: '3'
services:
  db:
    image: postgres
    volumes:
      - data:/var/lib/postgresql/data
volumes:
  data:
    driver: mydriver
```

清单 9-4. 显示版本规范的 `docker-compose.yml` 文件