# 打开文件过多

如果你遇到类似这样的错误：

```
java.net.SocketException: Too many open files
at java.net.Socket.createImpl(Socket.java:447)
at java.net.Socket.getImpl(Socket.java:510)
at java.net.Socket.setSoLinger(Socket.java:984)
```

这意味着你已经达到了允许打开的最大文件数限制。默认值是 1024。在使用 Cassandra 时，非常有可能超过这个限制。我们可以通过修改 `/etc/security/limits.conf` 文件来提高这个限制，示例如下：

```
root soft nofile 65535
```

这里的 `65535` 是允许打开的文件描述符数量。这样做应该能解决问题。此外，我们还可以通过监控正在运行的 Java 进程来监视文件描述符的数量，以验证是否属于内存泄漏的情况！

使用 `lsof` 命令，我们可以列出所有打开的文件。例如，我们可以按如下方式列出某个进程 ID（PID）的所有打开文件：

```
lsof –p 13241
COMMAND    PID      USER    FD     TYPE    DEVICE    SIZE/OFF    NODE NAME
init       13241    root    cwd    DIR     225,0         4096    1 /
init       13241    root    rtd    DIR     225,0         4096    1 /
```

