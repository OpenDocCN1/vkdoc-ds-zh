# 5. Dockerfile

Joshua Cook¹
(1) 美国加利福尼亚州圣莫尼卡

每个 Docker 镜像都被定义为一组层，每一层都定义了镜像的基本、无状态更改。第一层可能是虚拟机的操作系统（Debian 或 Ubuntu Docker 镜像），下一层是应用程序运行所需的依赖项的安装，一直到应用程序的源代码。利用此系统的最佳方式是通过 `Dockerfile`。

## 最佳实践

`Dockerfile` 是一种编写构建镜像脚本的方式。此脚本使用领域特定语言来告诉 Docker 守护进程如何顺序构建 Docker 镜像。当守护进程被指示构建镜像时，它会通过从 `Dockerfile` 中读取必要的指令来完成。Docker 将以下内容视为创建 `Dockerfile` 时的最佳实践：

- 容器应该是短暂的或无状态的，即它们可以通过最少的设置和配置重新实例化。
- 使用 `.dockerignore` 文件，类似于 `.gitignore`。
- 避免安装不必要的包。
- 每个容器应该只关注一件事。
- 最小化层数。
- 对多行参数进行排序。

### 无状态容器

现代软件开发的最佳实践之一是软件以隔离进程的方式运行，彼此之间不共享任何内容，这通常被称为微服务¹。虽然一个应用可能同时运行多个进程，但这些进程应该是无状态的。如果任何信息需要在进程终止后持久保存，则应将其写入有状态的后端服务，例如数据库（如 `MongoDB` 或 `Postgres`）或键值存储（如 `Redis`）。我们将 Docker 容器视为系统进程的抽象，实际上，可以将 Docker 容器视为由 Docker 守护进程管理的进程。因此，最佳实践也是将我们的 Docker 镜像及其定义的运行容器定义为完全无状态的。我们应该能够关闭一个容器并将其从系统中移除，然后使用相同的镜像启动一个相同的容器，并运行命令，而不会对我们的工作产生任何影响。

### 单一职责容器

早期版本的 Dockerfile 最佳实践遵循“每个容器一个进程”的原则。随着 Docker 的成熟，坚持每个容器只运行一个进程已被证明是不可行的。Jupyter 实际上就是一个无法通过单个进程来定义的容器的典型例子。尽管如此，最佳实践仍然认为每个容器应被定义为具有单一职责。Jupyter 可能需要多个进程才能正常运行，但我们会用一个单独的容器来专门运行 Jupyter。如果我们希望与 Postgres 数据库交互，我们会使用另一个专门负责该数据库的容器。最终目标是保持应用职责的分离和模块化。

## 项目：一个 Docker 镜像仓库

在本章中，你将开发一个 `Dockerfile` 仓库，每个 `Dockerfile` 定义了一个你工作中可能用到的独立镜像。在通过 `Dockerfile` 开发和维护这些镜像的过程中，你将探索定义镜像的语法以及构建和维护镜像的最佳实践。你的最终目标是创建一套镜像，这些镜像将成为你开发模块化系统以执行数据科学家工作的主要构建块。

### 准备本地开发环境

首先，你将准备好本地机器以进行后续的开发工作。在代码清单 5-1 中，你通过为项目创建一个目录并将其初始化为 git 仓库来完成此操作。

```
$ mkdir ch_5_dockerfiles && cd ch_5_dockerfiles
$ git init
$ touch README.md
$ git add README.md
$ git commit -m 'init'
```

*代码清单 5-1. 准备本地开发环境*

### 配置 GitHub

在 GitHub 上，创建一个名为 `Dockerfiles` 的仓库。创建好仓库后，将你的本地目录连接到远程 GitHub 仓库（代码清单 5-2）。

**注意：** 你需要配置你的 GitHub 账户以通过 SSH 进行连接。相关文档可在 [`help.github.com/articles/connecting-to-github-with-ssh/`](https://help.github.com/articles/connecting-to-github-with-ssh/) 找到。

```
$ git remote add origin git@github.com:<username>/dockerfiles.git
$ git push -u origin master
```

*代码清单 5-2. 将本地仓库连接到 GitHub*

### 使用 Dockerfile 构建镜像

`Dockerfile` 与构建（编译）后的 Docker 镜像之间的连接是通过 `docker build` 命令实现的。该构建命令告诉 Docker 守护进程使用指定的上下文和一个 `Dockerfile` 来构建一个镜像。上下文指的是用于构建特定镜像的文件集合。上下文将通过包含的 `PATH` 来指定。你可以查看 `docker build` 的要求来了解其具体形式（代码清单 5-3）。

```
$ docker build
"docker build" requires exactly 1 argument(s).
See 'docker build --help'.

Usage:  docker build [OPTIONS] PATH | URL | -
```

*代码清单 5-3. 显示 `docker build` 帮助信息*

如你所见，`docker build` 需要一个 `PATH` 或 `URL` 作为最终参数。这就是上下文。

### Dockerfile 语法

`Dockerfile` 使用一种简单的领域特定语言构建（见代码清单 5-4）。指令不区分大小写，但按惯例通常全部大写。指令按顺序执行，`Dockerfile` 应被视为传递给 Docker 守护进程的脚本。

```
# Comment
INSTRUCTION arguments
```

*代码清单 5-4. `Dockerfile` 语法*

### 设计 gsl 镜像

你将构建的第一个镜像与你在第 3 章中使用的镜像相同，即 GSL 镜像。此镜像用于使用 GNU 科学库（GSL）编译代码，GSL 是一套用于计算数学的 C 语言工具，尤其在使用 BLAS 生态系统时。你使用 `gcc`² 镜像作为基础镜像来构建此镜像，这样做可以确保你拥有编译 C 代码所需的所有工具。

#### 创建 gsl 源码目录

首先，你为这个特定的镜像创建一个目录，并实例化你的 `Dockerfile`（代码清单 5-5）。

```
$ mkdir gsl
$ touch gsl/Dockerfile
```

*代码清单 5-5. 为 `gsl` 镜像创建一个包含空 `Dockerfile` 的目录*

在代码清单 5-6 中，为了构建 `gsl` 镜像，你使用了 `docker build` 命令，并将相对引用的文件夹 `gsl` 作为上下文。`-t` 标志告诉守护进程将该镜像命名为 `joshuacook/gsl`。你最初的构建尝试失败了，因为你还没有向 `Dockerfile` 中添加任何命令。

```
$ docker build -t joshuacook/gsl gsl
sending build context to Docker daemon 53.25 kB
Error response from daemon: The Dockerfile (Dockerfile) cannot be empty
```

*代码清单 5-6. 运行 Docker 构建*

#### 定义 gsl 镜像

让我们像在第 3 章中那样定义 `gsl` 镜像，使用三个命令定义三个层，如代码清单 5-7 所示。

```
FROM gcc
LABEL maintainer=@joshuacook
RUN apt-get update && \
      apt-get install -y \
      gsl-bin \
      libgsl0-dbg \
      libgsl0-dev \
      libgsl0ldbl
```

*代码清单 5-7. `gsl/Dockerfile`*

#### 构建 gsl 镜像

在代码清单 5-8 中，使用 `Dockerfile` 定义了镜像后，你使用 `docker build` 命令构建该镜像，再次命名镜像并将 `gsl` 目录作为上下文提供。

```
# Docker 构建示例：GSL 镜像

### 初始构建

```bash
$ docker build -t joshuacook/gsl gsl
sending build context to Docker daemon 14.85 kB
Step 1 : FROM gcc
latest: Pulling from library/gcc
693502eb7dfb: Pull complete
...
e6a66f7b6a7a: Pull complete
Digest: sha256:c1fa0b3eeba33a7b9da5ab7de7fa2c520760f778b5e5d1db38791d0da7b841b9
Status: Downloaded newer image for gcc:latest
 ---> 408d218617ca
Step 2 : LABEL maintainer @joshuacook
 ---> Running in 723583dd01d6
 ---> 20bbe26b8b8a
Removing intermediate container 723583dd01d6
Step 3 : RUN apt-get update &&     apt-get install -y     gsl-bin     libgsl0-dbg     libgsl0-dev     libgsl0ldbl
 ---> Running in ed02ca384066
Get:1 http://security.debian.org jessie/updates InRelease [63.1 kB]
...
```

*代码清单 5-8. 运行 Docker 构建*

### Dockerfile 指令详解

#### `FROM gcc`

第一层使用 `FROM` 指令来定义构建镜像的基础镜像。一个有效的 Dockerfile 必须始终以 `FROM` 指令开头。最佳实践建议，除高级用例之外，所有镜像都应从 Docker [公共仓库](https://docs.docker.com/engine/tutorials/dockerrepos/)中拉取 `FROM` 基础镜像。重申一下，`FROM` 必须是 Dockerfile 中第一个非注释指令。此处，你正在从 [gcc 镜像](https://hub.docker.com/_/gcc/) 中拉取。`FROM` 可以使用以下三种语法：

*   `FROM <image>`

*   `FROM <image>:<tag>`

*   `FROM <image>@<digest>`

**注意：** `tag` 或 `digest` 的使用是可选的。如果两者都未提供，则 `tag` 默认为 `latest`，对应于 Docker Hub 上最新的可用构建版本。

#### `LABEL maintainer=@joshuacook`

第二层使用 `LABEL` 指令来定义与镜像关联的元数据。每个 `LABEL` 都是一个键值对。在此例中，你将键 `maintainer`（镜像的维护者）与值（Docker Hub 用户）`joshuacook` 关联起来。镜像可以有多个 `LABEL`。要查看镜像的 `LABEL`，可以使用 `docker inspect` 命令。在清单 5-9 中，你对刚刚构建的 `joshuacook/gsl` 镜像执行了此操作。

```bash
$ docker inspect joshuacook/gsl
...
   "Labels": {
       "maintainer": "@joshuacook"
   }
...
```

**清单 5-9.** 检查 `joshuacook/gsl` 镜像

#### `RUN apt-get update && apt-get install`

镜像的最后一层使用了 `RUN` 指令，在此例中用于安装使用 GNU 科学库所需的库。`RUN` 指令使用 `/bin/sh` 在当前镜像之上的新层中执行任何提供的命令。执行的结果成为新的镜像。

**注意：** 我们使用 `\`（反斜杠）来将单个指令延续到多行。因此，清单 5-10 和清单 5-11 中的指令在功能上是等效的。

```dockerfile
RUN apt-get update && \
      apt-get install -y \
      gsl-bin \
      libgsl0-dbg \
      libgsl0-dev \
      libgsl0ldbl
```

**清单 5-10.** `apt` 跨多行安装

```dockerfile
RUN apt-get update && apt-get install -y gsl-bin libgsl0-dbg libgsl0-dev libgsl0ldbl
```

**清单 5-11.** `apt` 单行安装

### 提交更改到 GitHub

在继续之前，你应该 `git commit` 对 Dockerfile 所做的更改，并将更改推送到 GitHub（参见清单 5-12）。

```bash
$ git add gsl/Dockerfile
$ git commit -m 'GSL IMAGE - initial build'
$ git push
```

**清单 5-12.** 提交更改并推送到 GitHub

## Docker 构建缓存

回想一下，Docker 镜像的结构被定义为一堆镜像，每个镜像定义了对镜像的一个无状态更改。由于每个镜像都是作为单独的抽象层构建的，因此在重建镜像时，对 Docker 镜像的更改是从上到下进行的。如果你只对位于最顶层的代码进行了更改，那么这将是镜像中唯一会被重建的层。

你可以通过向 GSL 镜像添加一个新层来检查构建缓存。让我们向镜像添加一个新层，其中包含你可能感兴趣的用于在 C 语言中执行数据科学任务的另一个库。将清单 5-13 中的行添加到你的 Dockerfile 中。

```dockerfile
RUN apt-get update && \
    apt-get install -y gdb
```

**清单 5-13.** 向 `joshuacook/gsl` 添加额外的依赖层

在清单 5-14 中，你重建了镜像。

```bash
$ docker build -t joshuacook/gsl gsl
Sending build context to Docker daemon 2.048 kB
Step 1/4 : FROM gcc
 ---> 408d218617ca
Step 2/4 : LABEL maintainer @joshuacook
 ---> Using cache
 ---> ba0d6482c28a
Step 3/4 : RUN apt-get update &&       apt-get install -y       gsl-bin       libgsl0-dbg       libgsl0-dev       libgsl0ldbl
 ---> Using cache
 ---> 8706282586fc
Step 4/4 : RUN apt-get install -y gdb
 ---> Running in c491003e5a95
...
 ---> 53111721b96a
Removing intermediate container c491003e5a95
Successfully built 53111721b96a
...
```

**清单 5-14.** 重新运行 `joshuacook/gsl` 镜像构建

**注意：** 在此构建过程中，步骤 1-3 并未像之前那样执行。在执行每个步骤之前，Docker 守护进程会将该步骤与本地缓存中同一镜像的现有实例的各个层进行比较。如果匹配，构建将使用现有层（即 Docker 构建缓存）来构建镜像。因此，没有更改的构建是幂等的，这意味着初始命令会构建镜像，但后续构建不会产生额外影响。

清单 5-15 演示了 `docker build` 过程的幂等性。

```bash
$ docker build -t joshuacook/gsl gsl
Sending build context to Docker daemon 2.048 kB
Step 1/4 : FROM gcc
 ---> 408d218617ca
Step 2/4 : LABEL maintainer @joshuacook
 ---> Using cache
 ---> ba0d6482c28a
Step 3/4 : RUN apt-get update &&       apt-get install -y       gsl-bin       libgsl0-dbg       libgsl0-dev       libgsl0ldbl
 ---> Using cache
 ---> 8706282586fc
Step 4/4 : RUN apt-get install -y gdb
 ---> Using cache
 ---> 53111721b96a
Successfully built 53111721b96a
```

**清单 5-15.** 再次运行相同的构建

一个更有趣且更有用的结果是，构建将只运行在你做出更改的第一个步骤之后的步骤，如清单 5-15 所示。
```

### Anaconda

Anaconda 是 Python 和 R 编程语言的免费增值发行版，用于大规模数据处理、预测分析和科学计算。你将使用 Anaconda 来驱动你的 Jupyter 平台。`conda` 命令是管理 Anaconda 安装的主要接口。Miniconda 是一个小的“引导”版本，仅包含 `conda` 和 `conda-build`，并安装 Python。然而，作为练习，你将设计自己的 miniconda 镜像，该镜像模仿 Continuum Analytics 分发的 miniconda 镜像，可用于最小化地运行 IPython。

### 设计 miniconda3 镜像

在第 3 章中，你使用了 `jupyter/scipy-notebook` 镜像来使用 `scipy` 库运行 IPython。这是对该镜像的完全合理的使用。它包含了你需要的所有库。此外，为了拥有单一用途的镜像而存储两个独立定义的镜像（一个用于运行带有 `scipy` 的 Jupyter，另一个用于运行带有 `scipy` 的 IPython）会消耗不必要的磁盘空间。最后，从 `jupyter` 镜像运行 IPython 是 IPython 团队推荐的最佳实践。³ 最终，你也会这样做。

#### 创建 miniconda3 源码目录

同样，在清单 5-16 中，你首先创建一个目录来存放与此镜像关联的源代码。

```
$ mkdir miniconda3
$ touch miniconda3/Dockerfile
```

清单 5-16. 为包含空 Dockerfile 的 `miniconda3` 镜像创建目录

在清单 5-17 中，你使用 `tree` 工具来显示到目前为止你的 docker 镜像项目的整体结构。

```
$ tree .
├── README.md
├── gsl
│   └── Dockerfile
└── miniconda3
    └── Dockerfile
```

清单 5-17. 使用 `tree` 显示项目进度

### 以 `FROM`、`ARG` 和 `MAINTAINER` 指令开始构建镜像

你需要使用 `FROM` 指令来开始构建镜像，该指令用于设定构建镜像所基于的基础镜像。一个有效的 Dockerfile 必须以 `FROM` 指令作为第一条指令。在清单 5-18 中，你借鉴了 Jupyter 项目的做法，并基于 [debian](https://hub.docker.com/_/debian/) 镜像进行构建。

```
FROM debian
```

清单 5-18. `miniconda3/Dockerfile`

在清单 5-19 中，你构建了该镜像。

```
$ docker build -t miniconda3 miniconda3
Sending build context to Docker daemon 2.048 kB
Step 1/1 : FROM debian
latest: Pulling from library/debian
6d827a3ef358: Pull complete
Digest: sha256:72f784399fd2719b4cb4e16ef8e369a39dc67f53d978cd3e2e7bf4e502c7b793
Status: Downloaded newer image for debian:latest
 ---> 8cedef9d7368
Successfully built 8cedef9d7368
```

清单 5-19. 构建 `miniconda3` 镜像

在这里，你构建了一个仅包含单层的镜像：即底层操作系统。在清单 5-20 中，你列出了镜像缓存中的镜像。

```
$ docker images
REPOSITORY        TAG       IMAGE ID        CREATED          SIZE
debian            latest    47af6ca8a14a    8 days ago       125.1 MB
miniconda3        latest    47af6ca8a14a    5 minutes ago    125.1 MB
...
```

清单 5-20. 显示缓存的镜像

**注意：** `debian` 镜像和 `miniconda3` 镜像拥有完全相同的 `IMAGE ID`。这是因为它们是同一个镜像。

#### 将更改提交到本地仓库

你将在此处构建一个相当复杂的镜像，因此最佳实践是频繁地提交到本地 git 仓库。在清单 5-21 中，你为此镜像进行了首次提交。

```
$ git add Dockerfile
$ git commit -m 'MINICONDA3 IMAGE. Added FROM instruction.'
```

清单 5-21. 提交更改

在清单 5-22 中，你为镜像的维护者添加了一个标签，就像之前为 `gsl` 镜像所做的那样。同样，这不是必需的，但这是惯例。

```
FROM debian
LABEL maintainer=@joshuacook
```

清单 5-22. `miniconda3/Dockerfile`

在清单 5-23 中，你添加了一条 `ARG` 指令。`ARG` 指令用于定义一个在构建时或运行时可用的环境变量。

```
FROM debian
LABEL maintainer=@joshuacook
ARG DEBIAN_FRONTEND=noninteractive
```

清单 5-23. `miniconda3/Dockerfile`

在这里，你使用 `ARG` 指令定义了一个环境变量，该变量将描述你与 Debian 容器交互的行为。你也可以定义一个没有值的 `ARG`，然后将该值作为参数传递给构建过程（参见清单 5-24）。

```
$ DEBIAN_FRONTEND=noninteractive docker build -t some_image .
```

清单 5-24. 在构建时传递的 `ARG`

**注意：** 你可以在 `docker build` 命令中内联声明参数的值。还有第三种声明环境变量的方法。Jupyter 开发团队定义的 Jupyter 镜像使用 `ENV` 指令来实现相同的目的。在阅读了 Docker 团队 GitHub 页面上的[这个讨论串](https://github.com/docker/docker/issues/6345)后，我选择使用 `ARG` 指令。它们中的任何一个都可以工作，但重要的是只使用其中一个。

### 幂等地运行构建

在清单 5-25 中，你运行了构建。由于你之前已经为第一层运行过构建，因此这一步无需再次执行，构建过程只会拾取最新的添加内容。

```
$ docker build -t miniconda3 miniconda3
Sending build context to Docker daemon 2.048 kB
Step 1/3 : FROM debian
 ---> a2ff708b7413
Step 2/3 : LABEL maintainer @joshuacook
 ---> Using cache
 ---> c140313c988e
Step 3/3 : ARG DEBIAN_FRONTEND=noninteractive
 ---> Running in b927c25267f0
 ---> da987fd59d24
Removing intermediate container b927c25267f0
Successfully built da987fd59d24
```

清单 5-25. 运行构建

现在你取得了一些进展。你已经在 Dockerfile 中添加了三行。当你运行构建时，它要么 1) 像步骤 1 那样使用预先存在的层，要么 2) 创建一个包含该步骤结果的新层。

**注意：** 这次没有拉取 `debian` 镜像。这里没有需要做的新事情！

#### 将更改提交到本地仓库

在清单 5-26 中，你再次将更改提交到本地仓库。

```
$ git add Dockerfile
$ git commit -m 'MINICONDA3 IMAGE. Added maintainer LABEL and ARG instruction'
```

清单 5-26. 提交更改

### 配置 `miniconda3` 镜像

最终，你的 Jupyter 应用程序将运行在一个容器化的 Debian 机器上。与任何应用程序一样，它会有许多依赖项，或者说系统级应用程序，Debian 系统必须能够执行这些程序才能正常运行。如果你手动构建这个系统（即不使用 Docker），你会使用适合你操作系统的命令行包管理器（Ubuntu 或 Debian 使用 `apt`，CentOS 使用 `yum`，Mac OS X 使用 `brew`），这个过程称为配置。Docker 守护进程本身没有配置的方法，但它有一种机制，可以通过 `RUN` 指令来利用基础系统的包管理器（清单 5-27）。

```
RUN <command>
```

## 清单 5-27. `RUN` 指令

这相当于在镜像的 shell 中运行 `<command>`（即`/bin/sh -c <command>`）。与在 shell 语言中编写脚本类似，你可以在行尾添加反斜杠（`\`）来将命令延续到下一行。换句话说，清单 5-28 和清单 5-29 中的语句是等价的。

```
RUN /bin/bash -c 'source $HOME/.bashrc ; echo $HOME'
```

清单 5-28. 一条 `RUN` 指令

```
RUN /bin/bash -c 'source $HOME/.bashrc ;\
echo $HOME'
```

清单 5-29. 另一条 `RUN` 指令

在清单 5-30 中，你为 `miniconda3` 镜像配置了精简的操作系统。

```
RUN apt-get update --fix-missing && \
    apt-get install -y \
    wget bzip2 ca-certificates \
    libglib2.0-0 libxext6 libsm6 libxrender1
```

清单 5-30. `miniconda3/Dockerfile`

### 运行构建

此时，你的镜像中已经有了一些实质内容。让我们继续构建它（清单 5-31）。注意，在第 4/4 步中，`RUN` 指令并不会感知到那些换行符。这些换行符仅仅是为了让我们更容易阅读 Dockerfile 而存在的。

```
$ docker build -t miniconda3 miniconda3
Sending build context to Docker daemon  2.56 kB
Step 1/4 : FROM debian
 ---> a2ff708b7413
Step 2/4 : LABEL maintainer @joshuacook
 ---> Using cache
 ---> c140313c988e
Step 3/4 : ARG DEBIAN_FRONTEND=noninteractive
 ---> Using cache
 ---> 6be1e058f2de
Step 4/4 : RUN apt-get update &&     apt-get install -yq --no-install-recommends     build-essential     bzip2     ca-certificates     git     libglib2.0-0     libsm6     libxrender1     wget&& apt-get clean &&     rm -rf /var/lib/apt/lists/*
 ---> Running in 642c7d024e55
Get:1 http://security.debian.org jessie/updates InRelease [63.1 kB]
Ign http://deb.debian.org jessie InRelease
Get:2 http://deb.debian.org jessie-updates InRelease [145 kB]
...
```

清单 5-31. 运行构建

构建的这一阶段会花费一些时间。现在，我们先不检查输出结果。再次强调，运行 Docker 构建是幂等的，这意味着无论你运行多少次，都会得到相同的输出。你需要等待这次运行完成，然后再次运行构建，以获得一个简洁的输出（清单 5-32）。

```
$ docker build -t miniconda3 miniconda3
Sending build context to Docker daemon  2.56 kB
Step 1/4 : FROM debian
 ---> a2ff708b7413
Step 2/4 : LABEL maintainer @joshuacook
 ---> Using cache
 ---> c140313c988e
Step 3/4 : ARG DEBIAN_FRONTEND=noninteractive
 ---> Using cache
 ---> c140313c988e
Step 4/4 : RUN apt-get update &&     apt-get install -yq --no-install-recommends     build-essential     bzip2     ca-certificates     git     libglib2.0-0     libsm6     libxrender1     wget&& apt-get clean &&     rm -rf /var/lib/apt/lists/*
 ---> Using cache
 ---> bb1ae69e4c8a
Successfully built bb1ae69e4c8a
```

清单 5-32. 幂等地运行构建

在清单 5-32 中，你可以看到构建输出包含四个步骤，对应 Dockerfile 中的四条指令。Docker 的工作方式是，它为每个步骤创建或使用一个缓存的镜像。在第 1 步，你告诉 Docker 守护进程你将使用哪个镜像作为基础镜像 `FROM debian`。每完成一个步骤，你就会有一个新的层，从而产生一个新的镜像和镜像 ID。让我们再次查看缓存中的镜像（清单 5-33）。

```
$ docker images
REPOSITORY       TAG       IMAGE ID        CREATED             SIZE
debian           latest    47af6ca8a14a    8 days ago          125.1 MB
miniconda3       latest    bb1ae69e4c8a    About a minute ago  1.636 GB
...
```

清单 5-33. 显示本地镜像缓存中的镜像

注意，最后一层的 ID `bb1ae69e4c8a` 与 `miniconda3` 镜像的镜像 ID 相匹配。

### 提交更改到本地仓库

在清单 5-34 中，你再次将更改提交到本地仓库。

```
$ git add Dockerfile
$ git commit -m 'MINICONDA3 IMAGE. OS provision statement.'
```

清单 5-34. 提交更改

## 安装 Miniconda

在清单 5-35 中，你通过一条 `RUN` 指令安装 Miniconda。这个命令直接取自 Continuum Analytics 为 Miniconda 提供的 Docker 镜像。⁵ 它与 `jupyter/base-notebook` 镜像中安装 Miniconda 的方式略有不同。

```
RUN echo 'export PATH=/opt/conda/bin:$PATH' > /etc/profile.d/conda.sh && \
    wget --quiet \
        https://repo.continuum.io/miniconda/Miniconda3-4.3.11-Linux-x86_64.sh -O ∼/miniconda.sh && \
        /bin/bash ∼/miniconda.sh -b -p /opt/conda && \
        rm ∼/miniconda.sh
```

清单 5-35. `miniconda3/Dockerfile`

### 运行构建

在清单 5-36 中，你运行构建来安装 Miniconda3。

```
$ docker build -t miniconda3 miniconda3
Sending build context to Docker daemon  2.56 kB
...
Step 5/5 : RUN echo 'export PATH=/opt/conda/bin:$PATH' > /etc/profile.d/conda.sh &&     wget --quiet https://repo.continuum.io/miniconda/Miniconda3-4.3.11-Linux-x86_64.sh -O ∼/miniconda.sh &&     /bin/bash ∼/miniconda.sh -b -p /opt/conda &&     rm ∼/miniconda.sh
 ---> Running in 6e3605df5b68
...
Python 3.6.0 :: Continuum Analytics, Inc.
creating default environment...
installation finished.
 ---> 02b8f5d04aeb
Removing intermediate container 6e3605df5b68
Successfully built 02b8f5d04aeb
```

清单 5-36. 运行构建

#### 提交更改到本地仓库

在清单 5-37 中，你再次将更改提交到本地仓库。

```
$ git add Dockerfile
$ git commit -m 'MINICONDA3 IMAGE. Install miniconda3.'
```

**清单 5-37.** 提交更改

### tini

我认为在目前这个进度下，开始讨论 PID 1 “僵尸”进程问题有点为时过早。简单来说就是：Docker 的最佳实践是，在运行容器时不要运行不必要的进程。这包括通常由操作系统运行来处理进程和信号的 `systemd` 或 `sysvinit`。这可能导致容器无法优雅地停止，或者本应终止的僵尸容器持续存在。现在，我们就像 Continuum 开发团队那样处理这种情况，使用 `tini`。`tini` 是解决这个问题的一个轻量级方案，没有额外的依赖。它可以回收僵尸进程，生成一个单一进程（该进程将作为 PID 1 运行），并且当 `tini` 的第一个子进程退出时，`tini` 也会退出。在清单 5-38 中，你将 `tini` 包含到容器中。

```
RUN apt-get install -y curl grep sed dpkg && \
    TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
    curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
    dpkg -i tini.deb && \
    rm tini.deb && \
    apt-get clean
```

**清单 5-38.** `miniconda3/Dockerfile`

#### 运行构建

在清单 5-39 中，你运行构建来安装 `tini`。

```
$ docker build -t miniconda3 miniconda3
Sending build context to Docker daemon  2.56 kB
...
Step 6/6 : RUN apt-get install -y curl grep sed dpkg &&     TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` &&     curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb &&     dpkg -i tini.deb &&     rm tini.deb &&     apt-get clean
 ---> Running in b6fa6f29121c
...
Successfully built ce5eb4345473
```

**清单 5-39.** 运行构建

#### 提交更改到本地仓库

在清单 5-40 中，你再次将更改提交到本地仓库。

```
$ git add Dockerfile
$ git commit -m 'MINICONDA3 IMAGE. Install tini.'
```

**清单 5-40.** 提交更改

#### 使用 `ENV` 配置环境变量

为了确保容器正常运行，你需要配置一些环境变量。在清单 5-41 中，你使用 `ENV` 指令来完成此操作。你首先指定了一些语言相关的变量，⁶ 然后确保 `conda` 二进制文件在 `PATH` 中。

```
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/conda/bin:$PATH
```

**清单 5-41.** `miniconda3/Dockerfile`

### `ENTRYPOINT`

最终，你需要为容器提供一个 `ENTRYPOINT`。`ENTRYPOINT` 指令指定了当镜像被实例化时要启动的进程。在清单 5-42 中，你告诉 Docker 将 `tini` 作为 PID 1 运行。

```
ENTRYPOINT [ "/usr/bin/tini", "--" ]
```

清单 5-42. `miniconda3/Dockerfile`

#### 运行构建

在清单 5-43 中，你运行构建来设置环境变量和 `ENTRYPOINT`。

```
$ docker build -t miniconda3 miniconda3
Sending build context to Docker daemon 3.072 kB
...
Step 7/9 : ENV LANG C.UTF-8 LC_ALL C.UTF-8
...
Step 8/9 : ENV PATH /opt/conda/bin:$PATH
...
Step 9/9 : ENTRYPOINT /usr/bin/tini --
 ---> Running in 3c3776056958
 ---> 014cc8d97486
Removing intermediate container 3c3776056958
Successfully built 014cc8d97486
Successfully tagged miniconda3:latest
```

清单 5-43. 运行构建

#### 将更改提交到本地仓库

在清单 5-44 中，你再次将更改提交到本地仓库。由于这标志着你的 `miniconda3` 镜像的完成，你还需要将更改推送到 GitHub。

```
$ git add Dockerfile
$ git commit -m 'MINICONDA3 IMAGE. Set env and entrypoint.'
$ git push
```

清单 5-44. 提交更改

### 设计 `ipython` 镜像

你的 `miniconda3` 镜像比 Jupyter 堆栈中的大多数其他镜像要小得多，正是因为它几乎预装了任何东西。如果你想运行 `ipython`，你需要安装这个库。让我们再次使用 `FROM` 指令，基于你刚刚设计的 `miniconda3` 镜像来构建一个 `ipython` 镜像。

#### 创建 `ipython` 源目录

首先，你为这个特定的镜像创建一个目录，并实例化你的 `Dockerfile`（清单 5-45）。

```
$ mkdir ipython
$ touch ipython/Dockerfile
```

清单 5-45. 为包含空 `Dockerfile` 的 `ipython` 镜像创建一个目录

#### 定义 `ipython` 镜像

让我们定义 `ipython` 镜像，使用三个命令定义的三层，如清单 5-46 所示。

```
FROM miniconda3
LABEL maintainer=@joshuacook
RUN conda update conda && \
    conda install --quiet --yes ipython && \
    conda clean -tipsy
```

清单 5-46. `ipython/Dockerfile`

#### 使用 `conda` 安装 `ipython`

这里唯一的新命令是使用 `conda` 来安装 `ipython`。`conda` 是一个类似于 `apt` 的包管理器，但它专为管理 Python 包而设计。在这里，你使用 `conda` 来安装 `ipython`。敏锐的 Python 开发者会注意到，我们避开了配置任何类型的虚拟环境。

#### 定义默认运行时命令

你在 `ipython` `Dockerfile` 中的最后一条指令是通过 `CMD` ⁷ 指令为镜像提供一个默认的运行时命令。每个 `Dockerfile` 可以包含一个 `CMD` 指令，尽管这个指令不是必需的。根据 Dockerfile 最佳实践，⁸ `CMD` 指令应用于运行镜像中包含的软件，并且应始终以 `CMD ["executable", "param1", "param2"...]` 的形式使用。在清单 5-47 中，你向 `Dockerfile` 添加了一条指令，以便在运行时执行 `ipython`。

```
FROM miniconda3
LABEL maintainer=@joshuacook
RUN conda update conda && \
    conda install --quiet --yes ipython && \
    conda clean -tipsy
CMD ["ipython"]
```

清单 5-47. `ipython/Dockerfile`

#### 构建 `ipython` 镜像

在清单 5-48 中，使用 `Dockerfile` 定义了镜像后，你使用 `docker build` 命令构建镜像，再次命名镜像并提供 `ipython` 目录作为上下文。

# 5.6 构建并运行镜像

## 清单 5-48. 运行 Docker 构建

```console
$ docker build -t ipython ipython
Sending build context to Docker daemon 2.048 kB
Step 1/4 : FROM jupyter-base
 ---> 014cc8d97486
Step 2/4 : LABEL maintainer @joshuacook
 ---> Using cache
 ---> 938a406795a2
Step 3/4 : RUN conda update conda &&     conda install --quiet --yes ipython &&     conda clean -tipsy
 ---> Running in 64910e75091f
Fetching package metadata .........
Solving package specifications: .
Package plan for installation in environment `/opt/conda`:

The following packages will be UPDATED:

    conda: 4.3.11-py36_0 --> 4.3.14-py36_0
...
 ---> f9a032f0a9a5
Removing intermediate container 64910e75091f
Step 4/4 : CMD ipython
 ---> Running in 5839183d3d00
 ---> 98f0b133173e
Removing intermediate container 5839183d3d00
Successfully built 98f0b133173e
Successfully tagged ipython:latest
```

### 将更改提交到 GitHub

在清单 5-49 中，你提交这个新镜像并将更改推送到 GitHub。

```console
$ git add ipython/Dockerfile
$ git commit -m 'IPYTHON. Initial build'
$ git push
```

## 清单 5-49. 提交更改并推送到 GitHub

### 将 `ipython` 镜像作为新容器运行

最后，在完成设计并构建了你的 `ipython` 镜像之后，让我们将该镜像作为新容器运行。在清单 5-50 中，你将 `ipython` 作为一个交互式终端容器化进程运行。你可以通过 `Ctrl+D` 终止该进程。

```console
$ docker run -it ipython
Python 3.6.0 |Continuum Analytics, Inc.| (default, Dec 23 2016, 12:22:00)
Type "copyright", "credits" or "license" for more information.

IPython 5.3.0 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: import requests

In [2]: resp = requests.get('http://google.com')

In [3]: resp.status_code
Out[3]: 200

In [4]: Do you really want to exit ([y]/n)?
```

## 清单 5-50. 将 `ipython` 镜像作为新的交互式终端容器化进程运行

## 总结

在本章中，我介绍了 `Dockerfile`，这是在 Docker 生态系统中用于定义镜像的文件类型。你探索了在定义 `Dockerfiles` 时使用的几个常见指令。你还定义了三个镜像，第三个是使用第二个作为基础镜像构建的。阅读本章后，你应该熟悉使用 Docker 最佳实践来定义新镜像。

## 脚注

1. [`en.wikipedia.org/wiki/Microservices`](https://en.wikipedia.org/wiki/Microservices)
2. [`en.wikipedia.org/wiki/GNU_Compiler_Collection`](https://en.wikipedia.org/wiki/GNU_Compiler_Collection)
3. [`hub.docker.com/r/ipython/ipython/`](https://hub.docker.com/r/ipython/ipython/)
4. [`github.com/docker/docker/issues/4032`](https://github.com/docker/docker/issues/4032)
5. [`github.com/ContinuumIO/docker-images/blob/master/miniconda3/Dockerfile`](https://github.com/ContinuumIO/docker-images/blob/master/miniconda3/Dockerfile)
6. [`unix.stackexchange.com/questions/87745/what-does-lc-all-c-do`](http://unix.stackexchange.com/questions/87745/what-does-lc-all-c-do)
7. [`docs.docker.com/engine/reference/builder/#cmd`](https://docs.docker.com/engine/reference/builder/#cmd)
8. [`docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#cmd`](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#cmd)

© Joshua Cook 2017

Joshua Cook

*Docker for Data Science*

`doi.org/10.1007/978-1-4842-3012-1_6`