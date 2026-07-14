## 引言

本书旨在教授 Docker 及其生态系统在数据科学领域应用的概念和技术。除了介绍核心的 Docker 技术（容器和镜像、引擎、`Dockerfile`），本书还讨论了如何使用 Jupyter Notebook 服务器和开源数据存储 `MongoDB`、`PostgreSQL` 及 `Redis` 构建更大的集成系统。

第一章引导读者使用 AWS `t2.micro` 完成本书所需的推荐硬件配置。第二章和第三章介绍了本书使用的核心技术 Docker 和 Jupyter，以及交互式编程的概念。第四章、第五章、第六章和第九章深入探讨了 Docker 生态系统的特定领域。第七章探讨了由 Jupyter 开发团队开发和维护的官方 Jupyter Docker 镜像。第八章介绍了三个开源数据存储的 Docker 镜像。第九章和第十章将所有内容整合在一起，使用 `Docker Compose` 将 Jupyter 连接到数据存储。

完成本书后，鼓励读者重读第三章和第十章，以开始发展自己的交互式软件开发风格。

本文提出的概念可能具有挑战性，尤其是在计算机资源和流程的抽象方面。话虽如此，本书不假设读者具备任何预备知识。我们尝试从基本原理出发构建讨论。考虑到这一点，读者应能熟练使用命令行，并具有冒险和好奇的精神。我们希望具有 Docker、Jupyter 或两者中级到高级理解的读者能够更深入地理解这些概念，并学习使用这些工具解决计算问题的新颖方法。





## 致谢

感谢 Mike Frantz、Gilad Gressel、Devon Muraoka、Bharat Ramanathan、Nash Taylor、Matt Zhou 以及 DSI Santa Monica 第 3 和第 4 期学员与我讨论本文中一些更抽象的概念。感谢 Chad Arnett 保持其独特风格。感谢 Jim Kruidenier 和 Jussi Eloranta 教会老狗新把戏。感谢我的父亲不断激励我，感谢我的母亲给予我对善良的坚定信念。感谢 Momlo 为我铺平道路，感谢 Dablo 的好奇心。感谢我的妻子艾琳对我的信任以及对我念叨“特征向量”一词的容忍。









