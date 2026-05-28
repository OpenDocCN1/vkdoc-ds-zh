# 17. 最终实践练习

本章包含的多项选择题对 Splunk 管理员和架构师认证考试很有帮助。你将更清楚地了解这些考试中出现的题目类型。

## 问题

1.  要在索引器集群中激活对某个索引的复制，必须在所有对等节点上的`indexes.conf`中配置哪个属性？

    1.  `repFactor = 0`

    2.  `replicate = 0`

    3.  `repFactor = auto`

    4.  `replicate = auto`

2.  当 Splunk 在非集群环境中索引数据时，默认会创建哪种类型的文件？

    1.  索引和`.tsidx`文件

    2.  原始数据和索引文件

    3.  压缩文件和`.tsidx`文件

    4.  压缩文件和元数据文件

3.  在搜索头集群（SHC）中，部署器（deployer）的作用是什么？（请选择所有适用的选项。）

    1.  向 SHC 成员分发应用

    2.  为 SHC 引导一个干净的 Splunk 安装

    3.  分发与搜索无关的手动配置文件更改

    4.  在整个 SHC 中分发由用户做出的运行时知识对象更改

4.  使用搜索作业检查器可以展开哪两个部分？

    1.  执行成本

    2.  已保存搜索历史记录

    3.  搜索作业属性

    4.  优化建议

5.  在退役索引器集群中的一个站点时，应将哪个`server.conf`属性添加到主节点的`server.conf`文件中？

    1.  `site_mappings`

    2.  `available_sites`

    3.  `site_search_factor`

    4.  `site_replication_factor`

6.  在索引桶内的以下文件类型中，哪种文件类型可能消耗最多的磁盘空间？

    1.  原始数据

    2.  布隆过滤器（Bloom filter）

    3.  元数据（`.data`）

    4.  倒排索引（`.tsidx`）

7.  为了提高 Splunk 性能，可以在 Splunk 架构中的以下哪些组件上调整`parallelIngestionPipelines`设置？（请选择所有适用的选项。）

    1.  索引器

    2.  转发器

    3.  搜索头

    4.  集群主节点

8.  哪个 Splunk 服务器角色负责管理索引器集群的运行？

    1.  索引器

    2.  部署器

    3.  主节点

    4.  监控控制台

9.  哪个 CLI 命令可以将 Splunk 实例转换为许可证从节点？

    1.  `splunk add licenses`

    2.  `splunk list licenser-slaves`

    3.  `splunk edit licenser-localslave`

    4.  `splunk list licenser-localslave`

10. 可以使用哪些工具来诊断索引器和转发器之间的连接问题？（请选择所有适用的选项。）

    1.  Telnet

    2.  `tcpdump`

    3.  `Splunk btool`

    4.  `Splunk btprobe`

11. 何时应启用多个搜索管道？

    1.  仅当磁盘 IOPS 达到 800 或更高时。

    2.  仅当并发用户少于 12 人时。

    3.  仅当运行 Splunk Enterprise 6.6 或更高版本时。

    4.  仅当 CPU 和内存资源明显未充分利用时。

12. 如果你怀疑 monitor 配置块中的正则表达式解释存在问题，你会搜索哪个日志文件来确认？

    1.  `btool.log`

    2.  `metrics.log`

    3.  `splunkd.log`

    4.  `tailing_processor.log`

13. 在一个四站点索引器集群中，哪个配置会在源站点存储两个可搜索副本，在 site2 存储一个可搜索副本，总共存储四个可搜索副本？

    1.  `site_search_factor = origin:2, site1:2, total:4`

    2.  `site_search_factor = origin:2, site2:1, total:4`

    3.  `site_replication_factor = origin:2, site1:2, total:4`

    4.  `site_replication_factor = origin:2, site2:1, total:4`

14. 规划搜索头集群时，以下哪项是正确的？

    1.  所有搜索头必须使用相同的操作系统。

    2.  所有搜索头必须是集群的成员（不允许有独立搜索头）。

    3.  搜索头主管（captain）必须分配给集群中最大的搜索头。

    4.  所有索引器必须属于底层索引器集群（不允许有独立索引器）。

15. 在 Splunk Enterprise 数据管道的哪个阶段处理索引提取配置？

    1.  输入

    2.  搜索

    3.  解析

    4.  索引

16. 安装 Splunk 后，内部索引默认存储在何处？

    1.  `SPLUNK_HOME/bin`

    2.  `SPLUNK_HOME/var/lib`

    3.  `SPLUNK_HOME/var/run`

    4.  `SPLUNK_HOME/etc/system/default`

17. Splunk 内部日志的默认日志大小是多少？

    1.  10 MB

    2.  20 MB

    3.  25 MB

    4.  30 MB

18. 哪个 Splunk 内部索引包含与许可证相关的事件？

    1.  `_audit`

    2.  `_license`

    3.  `_internal`

    4.  `_introspection`

19. 哪个搜索头集群组件负责将知识包推送到搜索对等节点、将配置更改复制到搜索头集群成员，并在整个搜索头集群中调度作业？

    1.  主节点

    2.  主管

    3.  部署器

    4.  部署服务器

## 答案

1.  4

2.  3

3.  4

4.  2, 3

5.  3

6.  3

7.  1, 2

8.  2

9.  1

10. 2, 3

11. 1

12. 2

13. 3

14. 2

15. 3

16. 3

17. 2

18. 3

19. 3

## 总结

本章提供了一些对管理员和架构师认证有用的多项选择题。您已经完成了第 3 模块的学习。下一章将在 AWS 平台上搭建一个 Splunk 环境。