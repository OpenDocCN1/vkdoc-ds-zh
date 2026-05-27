# 9. Docker Compose

Joshua Cook (1) 美国加利福尼亚州圣莫尼卡

到目前为止，我的讨论都集中在单个容器或运行在同一系统上的、单独管理的容器对上。在本章中，你将扩展自己的能力，使用 `Docker Compose` 工具开发由多个容器组成的应用程序。系统配置指的是安装必要的库、配置资源分配和网络连接，以及管理持久化和状态特定数据。过去，系统的配置和维护可能由系统管理员或运维工程师手动完成。近年来，`DevOps` 社区围绕一个名为“基础设施即代码”（`IaC`）的软件应用构建概念蓬勃发展。`IaC` 是使用为特定目的开发的软件来配置系统的实践，而不是手动完成。

你已经看到了如何使用 `Docker` 工具集来配置系统。通过使用 `Dockerfile`，可以用代码来配置特定容器所需的系统库和 Python 库。在本章中，你将探索如何使用 `Docker Compose` 工具来配置资源分配、多个容器之间的连接、环境变量以及其他状态特定数据，此外还有持久化数据。掌握了这些概念后，你将开始将你设计的多容器系统视为单一的容器化应用程序。此外，使用 `Docker Compose`，你将能够通过简单的命令行界面来启动、停止和扩展你的应用程序。

在接下来的内容中，我将讨论这一切对数据科学家意味着什么。具体来说，我将强调以这种方式工作的三个强大优势，即能够：

1. 在本地开发，在任何地方部署

2. 快速轻松地与利益相关者分享复杂的应用程序

3. 无需依赖 IT 资源，个人即可管理基础设施

## 安装 docker-compose

如果你正在运行 `Docker for Linux`，可以使用此处提供的说明安装 `docker-compose`：[`github.com/docker/compose/releases`](https://github.com/docker/compose/releases)。这包括那些一直遵循推荐的最佳实践，在可丢弃的云系统上进行操作的读者。使用 `Docker for Linux` 的用户需要遵循这些说明。如果你使用的是 `Docker for Mac`、`Docker for Windows` 或 `Docker Toolbox`，可以跳过下一节，因为 `docker-compose` 会在安装 `Docker` 时默认安装。

在撰写本文时，安装过程如下所述。第一组命令（清单 9-1）从 GitHub 获取最新的编译好的二进制文件，然后将该二进制文件移动到系统上的适当位置。第二个命令（清单 9-2）允许本地系统执行该二进制文件。

```
$ sudo curl -L https://github.com/docker/compose/releases/download/1.15.0/docker-compose-`uname -s`-`uname -m` > docker-compose
sudo mv docker-compose /usr/local/bin/docker-compose
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   600   0   600   0   0     2327      0 --:--:-- --:--:-- --:--:--  2334
100 8066k 100 8066k   0   0    1122k      0  0:00:07  0:00:07 --:--:-- 1556k
```

清单 9-1. 下载 `docker-compose` 二进制文件

```
$ sudo chmod +x /usr/local/bin/docker-compose
```

清单 9-2. 授予 `docker-compose` 二进制文件执行权限

在清单 9-3 中，你测试你的 `docker-compose` 安装。

```
$ docker-compose --version
docker-compose version 1.12.0, build b31ff33
```

清单 9-3. 测试 `docker-compose`