# Rexster

`Rexster` 是一个多功能的图服务器，它通过多种机制（主要侧重于 REST）暴露任何 `Blueprints` 图。它通过 REST API 和 `RexPro` 协议暴露图服务器。`RexPro` 是 `Rexster` 的一种二进制协议，可用于将 `Gremlin` 脚本发送到远程的 `Rexster` 实例。脚本在服务器上处理，结果被序列化并返回给调用客户端。它还提供了基于浏览器的界面工具，称为 Dog House 和 `Rexster` 控制台（后者将在 Titan 生态系统中讨论）。