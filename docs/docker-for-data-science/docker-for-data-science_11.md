# 将 Echo 作为服务运行

接下来，让我们将短语“Hello, World!”作为一项服务来运行。在 Docker 生态系统中，服务是一个被“容器化”的进程。你可能已经注意到，我一直将 `docker run` 命令的结果称为容器/进程。这个想法对于我们正在尝试做的事情至关重要。我们将一个进程封装在容器中，就像“Hello World!”一样，你向容器传递一个要执行的命令（清单 4-11）。

```
$ docker run alpine /bin/echo 'Hello, World!'
Hello World!
```

清单 4-11. 将 Echo “Hello, World!” 作为服务运行

再次，你显示当前正在运行的容器/进程，以确认 alpine 容器已再次关闭（清单 4-12）。同样，你之前操作的所有进程都没有在运行。alpine 镜像启动，使用 `echo` 命令输出“Hello World!”，然后关闭。

```
$ docker ps
CONTAINER ID     IMAGE     COMMAND     CREATED     STATUS     PORTS     NAMES
```

清单 4-12. 显示当前正在运行的容器/进程

这件事的重要性可能并不明显。为了完全理解正在发生的事情，让我们对整个流程进行计时。让我们使用内置的 `time` 命令来查看将字符串作为服务输出需要多长时间。在清单 4-13 中，你通过运行 `time` 命令，然后运行之前执行过的命令来完成此操作。

```
$ time docker run alpine /bin/echo 'Hello World!'
Hello World!
real    0m1.300s
user    0m0.019s
sys     0m0.030s
```

清单 4-13. 对 Echo 服务的执行进行计时

现在，你使用内置的 `time` 命令来查看在本地（即直接在您的机器上）执行相同操作需要多长时间；请参见清单 4-14。

```
$ time echo 'Hello World!'
Hello World!
real  0m0.006s
user  0m0.000s
sys   0m0.001s
```

清单 4-14. 对 `echo` 在本地执行进行计时

这是一个相当大的跳跃。在 Docker 实例中运行该命令使任务耗时增加了 200 倍。但请从另一个角度考虑这个问题。相反，请考虑这样一个事实：启动整个 alpine 实例仅增加了大约一秒钟的处理时间。

## 隔离启动时间

在清单 4-15 中，你尝试隔离启动时间。首先，你运行一个硬休眠并对其进行计时，同样使用 `time` 命令。`sleep` 命令只是让 shell 休眠指定的时间。这将允许你显式地控制时间。

```
$ time sleep 2
real    0m2.012s
user    0m0.001s
sys     0m0.003s
```

清单 4-15. 在本地对硬休眠进行计时

在清单 4-16 中，你将此硬休眠作为服务运行并对其执行进行计时。花点时间看看这是如何完成的是很有用的。在清单 4-16 中，你运行 `time docker run alpine /bin/sleep 2`。你正在对使用 Docker 管理在 alpine 镜像定义的容器中运行的 2 秒休眠进行计时。

```
$ time docker run alpine /bin/sleep 2
real    0m3.061s
user    0m0.014s
sys     0m0.013s
```

清单 4-16. 将硬休眠作为服务进行计时

请注意，你看到了大致相同的增长。在你的初步测试中，你可以将此理解为“容器化”你的服务会增加大约一秒钟的运行时间。这在输出“Hello World!”或执行 2 秒的硬休眠时是显著的。但在运行 Jupyter 服务器或使用 NumPy 执行长时间计算时，这完全可以忽略不计。

## 守护化的 Hello World

你最终会希望将 Docker 容器守护化（即，将它们设置为作为后台进程无限期运行）。你可以通过 `-d` 标志以分离模式运行容器来实现这一点。让我们以分离模式运行 alpine（即，将镜像作为容器运行，并使其在后台保持运行）。为了让它保持运行，你需要给它分配一个任务，即无限期地每秒输出“hello world”。或者，你可以给它分配一个更复杂的任务，例如执行一个长的算术运算或监听 Web 请求，但出于演示目的，这个任务就足够了（清单 4-17）。

```
$ docker run -d alpine /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

清单 4-17. 以分离模式运行 Alpine 镜像

在清单 4-18 中，你再次显示正在运行的容器/进程。因为你向 `run` 命令传递了用于分离模式的 `-d` 标志，所以它仍在运行。

```
$ docker ps
CONTAINER ID  IMAGE   COMMAND        CREATED         STATUS  PORTS   NAMES
9201755545d1  alpine  "/bin/sh ..."  51 seconds ago  Up ...  upbeat_easley
```

清单 4-18. 显示当前正在运行的容器/进程

但是，你如何确认它正在执行分配给它的任务呢？你可以通过 `docker logs` 命令来实现。在清单 4-19 中，你请求与名为 `upbeat_easley` 的 alpine 容器关联的日志。

```
$ docker logs upbeat_easley
hello world
hello world
...
```

清单 4-19. 显示分离容器的日志

在这里，你查看当前以分离模式运行的容器的日志。虽然不是最令人兴奋的日志，但足以确认你的容器正在执行其任务。让我们让它休息一下，使用 `stop` 命令将其关闭。在清单 4-20 中，你停止了 alpine 容器 `upbeat_easley`。

```
$ docker stop upbeat_easley
```

清单 4-20. 停止一个分离容器

## 总结

本章是本书迄今为止最短的一章。在本章中，你探讨了要求 Docker 将进程作为 Docker 容器进行管理的几个方面。本章的内容有些抽象。严格来说，透彻理解这些材料对于掌握本书中的宏观概念并非必需。鼓励有兴趣深入探究 Docker 引擎细微差别的读者查阅 [`docs.docker.com`](http://docs.docker.com) 上提供的优秀且深入的文档。

© Joshua Cook 2017  
Joshua Cook  
*Docker for Data Science*  
`doi.org/10.1007/978-1-4842-3012-1_5`

