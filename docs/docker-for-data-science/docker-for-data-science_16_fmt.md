# 使用`joyvan`安装库

在清单 7-22 中，你使用了 `Dockerfile` `RUN` 语法的一种变体。之前你使用 `RUN` 指令时采用的是 `RUN <command>` 语法。这被称为 `RUN` 指令的 shell 形式。以这种形式使用命令的效果是，通过 `/bin/sh` 或默认 shell 将命令发送到镜像。使用此语法通过 `pip` 安装将无法生效。这是因为，为了安装到正确的 conda 环境中，你需要使用 `source` 命令激活相应的环境。`source` 命令在默认 shell（`/bin/sh`）中不可用，但在 `bash` 中可用。为了使用 `bash`，你必须使用 `RUN` 指令的另一种语法，即 exec 形式。exec 形式使用语法 `RUN ["executable", "param1", "param2"]`。你使用 `RUN ["bash", "-c", "source activate <environment> && pip install twitter"]`。这会在你的镜像中打开一个 `bash` shell，并执行以下操作：
1. 激活相应的环境
2. 通过 `pip` 安装 `twitter`