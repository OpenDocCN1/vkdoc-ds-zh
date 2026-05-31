# 优化 MapReduce 和 YARN 性能

Mapper 的平均运行时间不足一分钟。您可以增大 `mapred.min.split.size`，以便分配更少的 Mapper，从而减少 Mapper 初始化开销。您可以调整以下参数：

- `Mapreduce.map.minsplitsize`
- `Mapreduce.map.maxsplitsize`
- `Number of reducers`（Reducer 数量）
- `mapreduce.task.io.sort.factor`：对于任何正在运行 Map 任务的作业，每当内存缓冲区达到溢出阈值时，都会生成一个新的溢出文件。因此，Map 任务完成写入后，可能会有多个溢出文件，这些文件会合并成一个排序后的输出文件。此配置属性控制一次合并的最大流（文件）数。默认值为 10，但可根据您的数据量以及溢出阈值（默认为 80%）进行调整。
- `mapreduce.task.io.sort.mb`：当 Map 任务执行时，它会写入一个循环内存缓冲区，该缓冲区的大小由 `io.sort.mb` 属性定义。当此循环内存缓冲区被填满（达到溢出阈值）时，输出会被溢出到磁盘（使用单独的线程并行执行）。如果溢出线程写入速度缓慢且缓冲区达到 100% 满，Map 执行将会停止，因此调整此属性以获得最佳 MapReduce 性能非常重要。默认值为 100 MB，应根据您环境的性能和生成的溢出文件进行调整。
- `mapreduce.map.memory.mb` / `mapreduce.reduce.memory.mb`：这是 Hadoop 为每个 Mapper 或 Reducer 任务强制执行的硬性限制，定义了可分配给 Mapper 或 Reducer 容器的最大内存。默认值为 1 GB，但您应根据需要为您环境设置这些值。
- `mapreduce.map.java.opts` / `mapreduce.reduce.java.opts`：这是 Mapper 或 Reducer 任务的 JVM 最大堆大小（`-Xmx`），该值应始终低于属性 `mapreduce.[map|reduce].memory.mb` 的值。通常，此值应为 `mapreduce.map.memory.mb` / `mapreduce.reduce.memory.mb` 属性值的 80%。
- `mapreduce.reduce.shuffle.parallelcopies`：当您的作业执行时，多个 Mapper 在（您集群的）节点上执行，它们的 Map 输出文件位于本地（在执行 Map 任务的节点上）。这些 Map 任务可能在不同时间完成，因此 Reducer 任务会在这些输出完成后立即开始复制。Reducer 任务可用于并行获取 Map 输出的复制线程数由属性 `mapreduce.reduce.shuffle.parallelcopies` 定义。默认值为 5，应根据您的数据量和平均 Mapper 数量进行调整。

