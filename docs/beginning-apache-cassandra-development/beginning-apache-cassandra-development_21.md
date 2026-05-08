# 栈大小限制

栈大小限制了我们在 Java 虚拟机（JVM）中可以拥有的线程数量。如果你看到类似下面的错误：

```
The stack size specified is too small, Specify at least 228k
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
```

这意味着栈大小设置得太小了。默认分配的值为 180K，按照 Cassandra 的建议，我们可以通过修改 `Cassandra-env.sh` 文件（位于 `conf` 文件夹下）将其设置为 228K，示例如下：

```
JVM_OPTS="$JVM_OPTS –Xss228k"
```

