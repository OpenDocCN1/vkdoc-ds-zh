# 截断主题

Kafka 中的主题（Topic）只会保留达到配置保留毫秒数的记录。了解这一点后，你可以通过修改主题配置来截断（清除所有记录）一个主题。你需要先缩短主题（记录）的保留期限，这样 Kafka 才会开始清理主题记录。等待一两分钟后，Kafka 不会立即删除这些记录，因此只需为你的主题重新添加一个合理的保留期限（常见为一天或七天）。

## 缩短主题保留期限

第一步使用 `--alter` 命令，将主题的 `retention.ms` 值设置为 1 秒（1000 毫秒）。执行`清单 11-9` 中的命令来修改主题保留期限。

```
docker exec -it kafka_kafka-0_1 \
/opt/bitnami/kafka/bin/kafka-configs.sh \
--bootstrap-server kafka_0:9092,kafka_1:9092,kafka_2:9092 \
--entity-type topics \
--alter \
--entity-name com.coffeeco.coffee.v1.orders \
--add-config retention.ms=1000
```
`清单 11-9` 截断 Kafka 主题以清除所有记录

现在主题正在被清理，等待一两分钟，然后恢复主题的保留期限。

## 增加主题保留期限

既然主题已经清理完毕（假设你已经等待了一段时间），现在你可以将主题保留配置改回一天的保留期限（参见`清单 11-10`）。

```
docker exec -it kafka_kafka-0_1 \
/opt/bitnami/kafka/bin/kafka-configs.sh \
--bootstrap-server kafka_0:9092,kafka_1:9092,kafka_2:9092 \
--entity-type topics \
--alter \
--entity-name com.coffeeco.coffee.v1.orders \
--add-config retention.ms=86400000
```
`清单 11-10` 为 Kafka 主题添加一天的保留期限

当你需要清除主题内的所有记录时，可以使用此技术。在练习 11-2 中创建 `CoffeeOrder` 生成器后，你可能想在测试完毕后清理该主题。这也可以用于测试 Kafka 的不同故障模式。