# 技巧 7-6. 使用 Standalone 集群管理器和 Mesos 集群管理器执行 PySpark 脚本

### 问题

你希望使用 Standalone 集群管理器和 Mesos 集群管理器来执行 PySpark 脚本。

### 解决方案

我们可以使用 `spark-submit` 来执行脚本。但首先需要启动 Standalone 集群管理器。它可以通过 SparkHome 目录下 `sbin` 中的 `start-all.sh` 脚本来启动：

```bash
[pysparkbook@localhost sbin]$ /allPySpark/spark/sbin/start-all.sh
```

以下是输出结果：

```
starting org.apache.spark.deploy.master.Master, logging to /allPySpark/logSpark//spark-pysparkbook-org.apache.spark.deploy.master.Master-1-localhost.localdomain.out
localhost: starting org.apache.spark.deploy.worker.Worker, logging to /allPySpark/logSpark//spark-pysparkbook-org.apache.spark.deploy.worker.Worker-1-localhost.localdomain.out
```

类似地，要在 Mesos 上运行，我们需要启动 Mesos 的 master 和 slave：

```bash
[pysparkbook@localhost bookCode]$ mesos-master --work_dir=/allPySpark/mesos/workdir &
[root@localhost bookCode]# mesos-slave --master=127.0.0.1:5050 --work_dir=/allPySpark/mesos/workdir --systemd_runtime_directory=/allPySpark/mesos/systemd &
```

### 工作原理

首先，我们使用 Standalone 集群管理器来运行脚本：

```bash
[pysparkbook@localhost bookCode]$ spark-submit --master spark://localhost.localdomain:7077 --num-executors 2 innerJoinInPySpark.py
```

以下是输出结果：

```
[('si3', (['Julie', 'F'], 'Java')), ('si3', (['Julie', 'F'], 'Ruby')), ('si2', (['Maria', 'F'], 'Python')), ('si2', (['Maria', 'F'], 'Java')), ('si1', (['Robin', 'M'], 'Python')), ('si1', (['Robin', 'M'], 'Java')), ('si4', (['Bob', 'M'], 'C++')), ('si4', (['Bob', 'M'], 'Python'))]
```

在命令中，master 选项的值是 Standalone 管理器的 URL。类似地，我们可以使用 Mesos 管理器的 URL 在 Mesos 上执行：

```bash
spark-submit --master mesos://127.0.0.1:5050 --conf spark.executor.uri=/home/pysparkbook/binaries/spark-1.6.2-bin-hadoop2.6.tgz innerJoinInPySpark.py
```

