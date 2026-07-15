
# 10. 高级输入输出

本章内容包括：
- 使用 Hive 表进行输入输出
- 通过 Gora 访问数据

在本章中，我们将继续探讨 Giraph 的更高级特性，重点介绍读取输入数据和写入输出数据的方法。回想一下，你的输入图通常以某种特殊格式存储在存储系统上，因此必须指示 Giraph 如何从你的存储系统读取数据，以及如何将其转换为内部表示形式；即，使用 `VertexInputFormat` 和 `EdgeInputFormat` 实现来创建顶点和边。例如，在第 7 章中，你了解了从 Hadoop 分布式文件系统（HDFS）读写数据的基础知识，这些数据通常为文本格式，这是存储图数据的一种非常常见的场景。

然而，HDFS 并非唯一的选择。有各种各样的存储系统和不同格式可以存储你的数据。例如，你可以使用 NoSQL 类型的存储系统（如 Hive 和 HBase）以半结构化表格的形式存储数据。Accumulo 是另一个分布式键值存储，它允许你以表格格式存储数据，并提供访问控制机制以实施与安全相关的策略。像 Redis 这样的内存数据存储也越来越流行，因为它们允许低延迟的数据访问。

为了支持所有这些不同的存储选项，Giraph 提供了一个 API，使得扩展至不同格式和存储系统变得容易。除此之外，它还提供了针对多种存储系统的这些 API 的实现。

在本章中，你将研究两个特定的存储系统案例：Hive 和 Gora。请注意，本章的目标并非穷举所有系统或数据格式。相反，通过本章学习，你将了解如何定制现有的 API 实现，或扩展它以满足你的特定应用场景。

## 访问 Hive 中的数据

首先，你将了解基于表格的格式——特别是 Hive。通常，用于存储数据的基于表格的格式非常普遍。Hive 允许你将数据视为半结构化表格，并使用类似 SQL 的查询语言查询数据。Hive 运行在 Hadoop 集群之上。Hive 表存储在 HDFS 上，Hive 查询作为 Hadoop 作业执行，为存储和查询半结构化表数据提供了一个可扩展的平台。基于这些原因，它是一种流行的存储系统。

Hive 表按行存储数据，每行由多个列组成。在 Hive 中，一行中各列包含的数据数量和类型是预先协商好的；这就是表的模式（`Schema`）。

在这里，你将看到 Hive 表如何存储代表输入图的信息。现在，就像你在早期章节中看到的基于文本的输入格式一样，Hive 表可能以基于边或基于顶点的格式存储有关输入图的数据。在基于边的格式中，一行 Hive 数据包含构建一条边所需的所有信息；即，边两个端点的标识符及其边值。在基于顶点的格式中，一行 Hive 数据可能包含构建一个顶点所需的所有信息；即，顶点 ID、其值以及该顶点所有边的信息。例如，在基于顶点的格式中，一个 Hive 列可能包含一个顶点所有边的列表。

### 读取输入数据

假设你正在运营一个微博客服务，用户可以发布文章并与其他用户分享。通常，每当有人发布这样的文章时，你需要将其存储起来以便进一步分析。例如，找出哪些用户发布的文章最多，过去一周哪些用户互动最频繁，用户最常讨论的话题是什么，以及其他有用的指标。基于表的存储是存储这些数据的好选择。`表 10-1.` 展示了一个如何存储这些数据的示例。

`表 10-1.` 示例服务中存储文章分享事件的表

| user | shared_with | date | article |
| --- | --- | --- | --- |
| George | John | 2015-03-01 | [`www.cnn.com/big-data-economy.html`](http://www.cnn.com/big-data-economy.html) |
| Mary | Nick | 2015-02-14 | [`www.howto.com/analyze-graphs.html`](http://www.howto.com/analyze-graphs.html) |
| Mark | George | 2015-04-29 | [`www.techcrunch.com/monetize-data.html`](http://www.techcrunch.com/monetize-data.html) |
| John | Maria | 2015-03-02 | [`www.cnn.com/big-data-economy.html`](http://www.cnn.com/big-data-economy.html) |
| Maria | Mark | 2015-03-03 | [`www.cnn.com/big-data-economy.html`](http://www.cnn.com/big-data-economy.html) |
| … | … | … | … |
| … | … | … | … |

尽管可能不那么显而易见，但在你服务中活跃的用户会形成一种网络。每当用户 A 与另一个用户分享一篇文章时，这就构成了两个用户之间的连接。现在假设 George 是你服务中一个受欢迎且活跃的用户，你希望了解 George 发布的文章在你的服务中传播速度有多快。换句话说，一旦 George 发布一篇文章，它在到达所有用户之前需要被分享多少次？

这种分析听起来可能很熟悉；你本质上是在计算用户之间的距离。在第 4 章中，你看到了如何编写一个 Giraph 应用来计算用户之间的最短路径。这正是你在这里需要的。既然你已经了解了如何计算这个指标，本章中你将只关注如何从输入表数据中构建输入图。

现在，回顾一下，Giraph 提供了两种基本的输入格式：基于边和基于顶点。在基于边的格式中，你的数据包含图中顶点连接的信息；而在基于顶点的格式中，数据包含顶点本身的信息。如果你查看 `表 10-1`，会发现它存储了关于哪个用户与特定另一个用户分享文章的信息；也就是说，用户之间的连接。

与基于文本的格式和 HDFS 一样，Giraph 已经抽象了从 Hive 表读取数据的大部分细节，并提供了一个你需要实现的简单接口。实现这个接口基本上需要你指定如何从表行创建一条边，就像你在 `表 10-1` 中看到的那样。回想一下，一条边定义的组成部分包括：源顶点 ID、目标顶点 ID 以及边值。这些就是你必须从 Hive 表行中提取的信息。但让我们来看一下你需要实现的具体方法。稍后，你将通过一个针对特定场景的示例实现看到它们的作用。`清单 10-1.` 展示了 Giraph 提供的 `SimpleHiveToEdge` 抽象类。

`清单 10-1.` 用于将 Hive 行转换为边对象的 `SimpleHiveToEdge` 类

```java
public abstract class SimpleHiveToEdge<I extends WritableComparable,
E extends Writable> extends AbstractHiveToEdge<I, E> {

  public abstract I getSourceVertexId(HiveReadableRecord hiveRecord);
  public abstract I getTargetVertexId(HiveReadableRecord hiveRecord);
  public abstract E getEdgeValue(HiveReadableRecord hiveRecord);
}
```

这是你可能需要实现的最简单的类，用于从 Hive 读取数据。你需要实现的第一个方法 `getSourceVertexId()` 接收一个 `HiveReadableRecord` 类型的对象作为输入，该对象是 Hive 行的一个抽象。该方法的作用是从 `HiveReadableRecord` 中构建源顶点 ID。你稍后会看到如何实现。第二个方法 `getTargetVertexId()` 有类似的作用，即从 Hive 记录中提取目标 ID。最后，通过 `getEdgeValue()` 方法，你定义了给定输入 Hive 记录时的边值。注意，与前几章中看到的输入格式一样，这种输入格式也有参数：顶点 ID 的类型（`I`）和边值的类型（`E`）。

现在，让我们看看所有这些方法的实际应用。在这个特定场景中，你感兴趣的 Hive 表中有两列：“user”列将成为边的源，“shared_with”列将成为边的目标。顶点 ID 代表用户的名称，因此在你的实现中，它们属于 `Text` 类型。`清单 10-2.` 展示了实现。

`清单 10-2.` 使用 `IntWritable` 顶点 ID 和 `NullWritable` 边值的边的示例实现

# 从 Hive 读取输入数据

首先，注意所有方法都接收一个 `HiveReadableRecord` 类型的对象作为输入，该对象代表一个 Hive 记录，本质上就是表的一行。在你的实现中，你将使用 `HiveReadableRecord` 类的方法来访问 Hive 行的不同列。

让我们逐一查看方法的实现。第一个方法 `getEdgeValue()` 必须返回一个代表边值的对象。在这个特定场景中，你不想为边分配特定值；相反，它的类型是 `NullWritable`，因此该方法简单地通过静态方法 `NullWritable.get()` 返回一个 `NullWritable` 对象。

接下来，你必须读取源 ID 和目标 ID。你已经知道边的源 ID 位于行的第一列，目标 ID 位于第二列。`HiveReadableRecord` 允许你通过列索引（索引从 0 开始）来检索行中存储的数据。清单 10-3 展示了 `HiveReadableRecord` 接口的所有方法。

**清单 10-3.** 使用 `HiveReadableRecord` 接口的方法从 Hive 表行的列中访问各种类型的数据

```
public interface HiveReadableRecord {
  int numColumns();
  int numPartitionValues();
  HiveType columnType(int index);
  Object get(int index, HiveType type);
  boolean getBoolean(int index);
  byte getByte(int index);
  short getShort(int index);
  int getInt(int index);
  long getLong(int index);
  float getFloat(int index);
  double getDouble(int index);
  String getString(int index);
  <K, V> Map<K, V> getMap(int index);
  <T> List<T> getList(int index);
  boolean isNull(int index);
}
```

## Giraph 与 Hive 集成：输入与输出格式

另一个你必须注意的方面是每列所存储的数据类型。每一列可能存储不同类型的数据。例如，用户名可能以字符串形式存储在列中；而日期可能以 `long` 类型的时间戳形式存储在列中。根据每列的数据类型，你需要使用 `HiveReadableRecord` 接口的相应方法。在上一个代码清单中，你可以看到该接口提供了访问所有基本类型数据的方法，例如整数、浮点数、字符串，以及列表和映射等结构。

回到你的实现，现在你将看到如何实现 `getSourceVertexId()` 和 `getTargetVertexId()` 方法。你使用 `HiveReadableRecord` 接口的 `getString()` 方法来分别检索索引为 0 和 1 的列，因为这些列是字符串类型，然后将其放入 `Text` 对象中。这样就完成了。

现在唯一缺少的是指示 Giraph 使用这个特定的输入格式实现，并告诉它从哪个 Hive 表读取数据。你将在启动 Giraph 作业时使用熟悉的 `-eif` 命令行参数来完成此操作。

请注意，这里并未讨论实际计算的实现。重要的是 Giraph 允许你在不同存储系统的数据上运行相同的计算，而无需更改代码。你只需编写一个输入格式，其余部分保持不变。事实上，在很多情况下，你甚至不需要编写自己的实现。Giraph 代码库已包含多种从 Hive 表读取数据的输入格式实现。其中一种可能已经适用于你的特定场景。

在本例中，输入图以基于边的格式存储。如前所述，在 Hive 中也可以使用基于顶点的输入格式。尽管我们不深入细节，但 Hive 中基于顶点的输入格式的概念与你在前几章中看到的基于文本的格式并无太大差异。Giraph 提供了一个接口，用于将单个 Hive 行（就像基于文本格式中的文本行一样）转换为一个顶点。清单 10-4 展示了你需要实现的接口。

### 清单 10-4. 用于将 Hive 行转换为顶点对象的 `SimpleHiveToVertex` 类

```
public abstract class SimpleHiveToVertex<I extends WritableComparable,
    V extends Writable, E extends Writable>
    extends AbstractHiveToVertex<I, V, E> {

    public abstract I getVertexId(Row record);
    public abstract V getVertexValue(Row record);
    public abstract Iterable<Edge<I, E>> getEdges(Row record);
}
```

与基于顶点的格式类似，在这种情况下，你必须指定如何从行中提取以下信息：顶点的 ID、顶点的值以及顶点的边。与基于边的格式一样，你可以使用 `HiveReadableRecord` 接口提供的方法访问 Hive 表的列。作为练习，我们建议你实现一个基于顶点的输入格式，从表 10-2 构建图。

### 表 10-2. 基于顶点格式的示例输入 Hive 表

| user | shared_with |
| --- | --- |
| George | {John, Mark} |
| Mary | {Nick, Maria, Mark, George} |
| Mark | {George} |
| John | {Maria, Helen, George} |
| Maria | {Mark, John, Nick, Helen} |
| … | … |
| … | … |

该表包含与上一表类似的信息；只是在这种情况下，创建顶点所需的所有信息都在同一行中：代表用户的顶点 ID 以及该用户与之共享的人。

## 写入输出数据

到目前为止，我们讨论了如何从 Hive 读取输入数据，但你自然可能希望应用程序的输出存储在 Hive 表中。这样，你可以使用 Hive 查询语言轻松查询分析结果。在本节中，你将看到如何实现这一点。

首先，让我们决定输出 Hive 表的结构。在这个特定场景中，你计算每个用户与 George 的距离。在最简单的形式中，输出表包含每个用户的计算距离（参见表 10-3）。

### 表 10-3. 示例 Hive 输出表

| user | value |
| --- | --- |
| George | 0 |
| Mary | 4 |
| Mark | 3 |
| John | 1 |
| Maria | 2 |
| … | … |
| … | … |

输出表每个用户包含一行。每行有一列用于存储用户名，称为“user”，包含字符串类型的数据，以及一列用于存储计算出的距离，称为“value”，包含双精度类型的数据。这里，我们假设此表已存在并具有此特定架构。如果你想了解更多关于如何创建 Hive 表的信息，请参考 Hive 文档。

与输入格式类似，Giraph 抽象了大部分细节，让你能够轻松地用输出填充此表。实际上，你执行的是逆向操作；你告诉 Giraph 如何从顶点表中提取信息，以及如何将其放入表行中。让我们看一下清单 10-5 中所示的实现。

### 清单 10-5. 用于将顶点对象转换为 Hive 行的 `SimpleVertexToHive` 类

```
public class TextNullHiveRowToEdge
  extends SimpleHiveToEdge<Text, NullWritable> {

  @Override
  public NullWritable getEdgeValue(HiveReadableRecord hiveRecord) {
    return NullWritable.get();
  }

  @Override
  public Text getSourceVertexId(HiveReadableRecord hiveRecord) {
    return new Text(hiveRecord.getString(0));
  }

  @Override
  public Text getTargetVertexId(HiveReadableRecord hiveRecord) {
    return new Text(hiveRecord.getString(1));
  }
}
```

# SimpleVertexToHive 接口与实现

## SimpleVertexToHive 抽象类

```java
public abstract class SimpleVertexToHive<I extends WritableComparable,
    V extends Writable, E extends Writable> extends
    AbstractVertexToHive<I, V, E> {

    public abstract void fillRecord(Vertex<I, V, E> vertex,
        HiveWritableRecord record);
}
```

这里唯一需要实现的方法是`fillRecord()`方法，它接受一个`Vertex`对象和一个`HiveWritableRecord`对象作为输入。Giraph 会为图中的每个`Vertex`调用此方法，并将`Vertex`对象作为输入传递。同时，它还会传入一个代表输出 Hive 表行的`HiveWritableRecord`对象。你的职责是指定如何用从`Vertex`对象中提取的信息填充该行。

与`HiveReadableRecord`类似，`HiveWritableRecord`接口提供了一组方法，使你可以轻松地将不同类型的信息存储到 Hive 行的列中。这些方法如清单 10-6 所示。

### 清单 10-6. 用于修改 Hive 记录的方法

```java
public interface HiveWritableRecord {
    void set(int index, Object value, HiveType type);
    void setBoolean(int index, boolean value);
    void setByte(int index, byte value);
    void setShort(int index, short value);
    void setInt(int index, int value);
    void setLong(int index, long value);
    void setFloat(int index, float value);
    void setDouble(int index, double value);
    void setString(int index, String value);
    void setMap(int index, Map value);
    void setList(int index, List value);
    List<Object> getAllColumns();
}
```

通常，你只需要指定要存储信息的列的索引以及要存储的值。除此之外，你需要确保根据表的架构（即每列存储的数据类型）使用正确的方法。

## MyVertexToHiveRow 实现示例

现在，我们来考虑你的特定场景。输出表有两列：一列是字符串类型，用于存储用户名；另一列是双精度类型，用于存储该用户的计算距离。清单 10-7 显示了实现。

### 清单 10-7. 将顶点对象写入 Hive 行的示例实现

```java
public class MyVertexToHiveRow extends
    SimpleVertexToHive<Text, DoubleWritable, NullWritable> {

    public void fillRecord(Vertex<Text, DoubleWritable, NullWritable> vertex,
        HiveWritableRecord record) {
        record.setString(0, vertex.getId().toString());
        record.setDouble(1, vertex.getValue().get());
    }
}
```

与输入表相同，你需要使用索引来标识所引用的列，列索引从 0 开始。请牢记，必须了解输出表的模式，并将正确的值写入正确的列。如果你试图将某个值写入类型不同的列，将会引发异常。

至此，输出格式的实现就结束了。现在，与输入格式类似，你需要指示 Giraph 使用此特定的输出格式实现，并告知它要将数据写入哪个 Hive 表。为此，你需要在启动 Giraph 作业时使用`–of`命令行参数。

## 访问 Gora 中的数据

接下来，让我们来看另一种存储系统，特别是内存存储系统。内存存储系统是另一类流行的系统，因为它们能提供快速的数据访问。本节将讨论 Apache Gora，这是一个提供内存数据模型的框架，同时也支持将数据持久化到不同的底层存储系统：数据库（如 MySQL）、列存储（如 HBase 和 Cassandra）、键值存储（如 Redis），甚至是 HDFS 上的简单文件。关于所支持存储系统的完整列表，请访问 Apache Gora 网站。

虽然 Apache Gora 提供了对这些系统的持久化能力，但它抽象了如何持久化对象的细节，允许用户操作对象的内存表示，从而从编程角度来看，使得对象的访问和操作更加容易。通常，Gora 系统被配置为映射底层存储系统中的数据；例如，将一个 HBase 行映射成一个内存中的 Java 对象。此后，用户可以像使用索引一样，根据对象键从 Gora 中访问数据。

在本节的剩余部分，你将了解如何使用 Giraph 的输入格式从 Gora 读取数据。本节不会涉及如何配置带有底层持久化存储系统的 Gora，也不会涉及如何定义底层存储系统与内存对象之间的映射。相反，你将了解如何访问内存中的 Gora 对象，并将它们转换为表示 Giraph 内部图的`Vertex`和`Edge`对象。

你将使用与上一节相同的应用场景。输入数据代表一组相互分享文章的用户，从而形成一个社交网络，而你需要计算在这个社交网络中，所有用户与用户 George 之间的距离。

### 读取输入数据

让我们来看看如何从 Gora 内存存储系统读取输入数据。首先，回想一下，Gora 提供了一个内存数据模型。这意味着它是以 Java 对象的形式向应用程序提供数据。虽然底层数据可能持久化在不同的存储系统中，但作为用户，你操作的是其内存版本。这个转换工作交给 Gora 系统本身来处理。

我们暂时假设，Gora 中用户的内存表示是一个名为`GoraUser`的对象，其形式如清单 10-8 所示。

### 清单 10-8. Gora 内存数据模型中用户的一个示例表示

```java
public class GoraUser {
    String name;
    Map<String, String> sharedArticles;
}
```

这个对象代表一个用户，包含的信息与你之前在 Hive 中存储数据时看到的类似。每个用户都有一个用于标识自身的名字，以及一组与之分享文章的用户。`sharedArticles`映射包含从用户名到与该用户分享的文章的映射关系。为简单起见，这里假设一个用户只能与另一个用户分享一篇分享文章。

与一般的输入格式一样，你需要决定是实现基于边的输入格式，还是基于顶点的输入格式。实际上，Giraph 代码既包含基于边的实现，也包含基于顶点的实现，用于从 Gora 读取数据。现在，你应该已经注意到，内存中用户的表示包含了构建一个顶点所需的所有信息；因此，这里自然选择基于顶点的输入格式。

那么，让我们来看看为了从 Gora 读取基于顶点的数据，你需要实现的接口。不出所料，Gora 接口是通过扩展我们熟悉的`VertexInputFormat`类来实现的。`GoraVertexInputFormat`的许多设置细节已经实现好了。剩下的功能，如清单 10-9 所示，主要要求你创建一个`GoraVertexReader`。

### 清单 10-9. 用于将内存对象转换为顶点对象的 GoraVertexInputFormat

```java
public abstract class GoraVertexInputFormat
    <I extends WritableComparable, E extends Writable>
    extends VertexInputFormat<I, E> {

    @Override
    public abstract GoraVertexReader createVertexReader(InputSplit split,
        TaskAttemptContext context) throws IOException;
}
```

正如你在第 7 章中看到的，`VertexReader`负责执行读取输入数据时的大部分工作；即，将输入数据转换为构成图的`Vertex`对象。特别地，`GoraVertexReader`负责将内存中的`Gora`对象转换为`Vertex`。清单 10-10 展示了为了能够从 Gora 读取数据，你需要实现的唯一方法。

### 清单 10-10. GoraVertexReader API

```
protected abstract class GoraVertexReader extends VertexReader<I, V> {
    protected abstract Vertex<I, V> transformVertex(Object goraObject);
}
```

请注意，所提供的 API 相当灵活。它向你传递一个通用的 Java 对象，并期望你将其转换为一个顶点。这个对象实际上就是你之前看到的用户内存表示，而你的输入格式实现则指定了如何将其转换为 Giraph 的 `Vertex` 对象。让我们看看清单 10-11 中的实现。

在深入细节之前，请注意该类如何将顶点 ID、顶点值和边值的类型作为参数。与上一节的应用场景类似，你将 ID 表示为 `Text` 类型的对象，将顶点值（即计算出的距离）表示为 `DoubleWritable` 类型的对象，最后，由于不关心边的值，你使用 `NullWritable` 类型的对象。具体实现见代码清单 10-11。

**代码清单 10-11.** 将 Gora 对象转换为顶点对象的示例实现

```java
public class GoraUserVertexInputFormat
    extends GoraVertexInputFormat<Text, DoubleWritable, NullWritable> {

  public GoraVertexReader createVertexReader(
      InputSplit split, TaskAttemptContext context) throws IOException {
    return new GoraGVertexVertexReader();
  }

  protected class GoraGVertexVertexReader extends GoraVertexReader {

    @Override
    protected Vertex<LongWritable, DoubleWritable, FloatWritable>
        transformVertex(Object goraObject) {

      Vertex<LongWritable, DoubleWritable, FloatWritable> vertex;
      vertex = getConf().createVertex();                                   // #1

      GoraUser user = (GoraUser) goraObject;                              // #2

      Text vrtxId = new Text(user.getName())                              // #3
      DoubleWritable vrtxValue = new DoubleWritable(0);                   // #4

      vertex.initialize(vrtxId, vrtxValue);                               // #5

      for (Map.Entry<String, String> entry : user.getSharedArticles()) {
        String userName = entry.getKey();                                 // #6
        String article = entry.getValue();                                // #6
        Edge<Text, NullWritable> edge = EdgeFactory.create(
            new Text(userName), NullWritable.get());                      // #7
        vertex.addEdge(edge);                                             // #8
      }

      return vertex;                                                      // #9
    }
  }
}
```

`#1` 创建一个空的顶点对象

`#2` 将输入的通用对象强制转换为 `GoraUser` 特定类型

`#3` 提取名称并从中创建一个类型为 `Text` 的新顶点 ID

`#4` 顶点的初始值应为 0

`#5` 使用其 ID 和值初始化该空顶点对象

`#6` 遍历该用户的共享文章映射中的每个条目

`#7` 以条目的键作为目标 ID 创建一条边

`#8` 将每个新创建的边添加到顶点中

`#9` 至此，你已经创建了顶点对象并可以返回它

现在，让我们深入探讨该 API 的实现。第一步是创建一个空的 `Vertex` 对象，然后用来自 Gora 对象的信息填充它。回顾一下，Giraph 已经提供了一些实用方法来实例化对象，例如边和顶点。在这种情况下，你可以直接调用 `getConf().createVertex()` 来创建一个空且未初始化的 `Vertex` 对象。接下来，你将看到如何用必要的信息（即它的值和边）来填充它。

接下来，你需要提取内存中对象的信息。由于 API 的输入是一个通用对象，你首先需要将其转换为期望的内部表示形式；在本例中，就是 `GoraUser` 类。如果你尝试转换的类型与实际对象在内存中的表示不一致，这自然会在运行时抛出异常。

首先，你提取顶点的 ID。为此，你将使用 `GoraUser` 对象的“name”字段，假设用户名可以唯一标识一个用户。正如你已经看到的，在 Giraph 中，你通常将字符串类型的 ID 表示为 `Text` 对象。类似地，从该字符串字段中，你创建一个新的 `Text` 对象来表示顶点 ID。

然后，你设置顶点的值。回顾一下，在计算距离的算法中，顶点的初始值为零。你需要做的就是创建一个设置为 0 的 `DoubleWritable` 对象。既然已经创建了顶点的 ID 和值，你可以调用 `Vertex.initialize()` 方法，将这些值设置到你新创建的 `Vertex` 对象中。

最后，你将提取顶点的边。在这里，你假设每个用户对象以映射的形式存储该用户与他人共享的文章。顶点读取器实现会遍历所有包含用户-文章对的映射条目，并使用 `EdgeFactory` 实用类创建一条边。创建的每条边都有对应的用户名作为目标 ID，即映射条目的键。在这种场景下，你不需要边值，因此将其设置为 `NullWritable`。一旦创建了 `Edge` 对象，你就可以使用熟悉的 `Vertex.addEdge()` API 将其添加到顶点中。

### 写入输出数据

接下来，你将学习如何输出数据。在此情形下，你需要将顶点和对象的 Giraph 表示转换为 Gora 内存模型。同样，你无需担心 Gora 如何持久化对象——这由底层系统负责，从而简化了你的工作。Giraph 通过提供输出格式实现（`GoraVertexOutputFormat` 和 `GoraEdgeOutputFormat`）来抽象这一过程的细节，并要求你实现几个方法，使整个过程变得简单。

在深入输出格式实现的细节之前，与往常一样，你需要决定是否需要基于顶点或基于边的输出格式。在此例中，你需要输出作业计算出的每个顶点的距离，因此基于顶点的输出格式是自然选择。接下来，让我们看看你需要实现的 API。

回顾一下，`VertexWriters` 是 Giraph 将 `Vertex` 对象输出到任意存储系统的方式。这里也不例外。Giraph 将这些细节抽象到 `GoraVertexWriter` 类中，如清单 10-12 所示。

清单 10-12. GoraVertexWriter API

```java
protected abstract class GoraVertexWriter extends VertexWriter<I, V, E> {

    protected abstract Object getGoraKey(Vertex<I, V, E> vertex);

    protected abstract Persistent getGoraVertex(Vertex<I, V, E> vertex);

}
```

该接口非常简单，只需要你实现两个方法。第一个方法 `getGoraKey()` 将 `Vertex` 对象作为输入，并返回一个键。这个键将用于在内存存储中唯一标识该对象。通常，Gora 键与顶点 ID 一致，用于唯一标识顶点。稍后你将看到实际应用。
```

你需要实现的第二个方法是`getGoraVertex()`，它将`Vertex`对象作为输入，并返回一个实现 Gora 的`Persistent`接口的对象。尽管你可以自己实现`Persistent`接口来存储输出，但 Giraph 已经提供了一个通用实现——名为`GVertexResult`——它允许你存储关于顶点的任何信息，例如其 ID 和值。在下面的示例中，你将看到它的实际使用。

首先，让我们看一下清单 10-13 中所示输入格式的实现。如前所述，Giraph 通过`GoraVertexOutputFormat`（一个你需要继承的抽象类）抽象了许多细节。将你的实现命名为`GoraUserVertexOutputFormat`。和往常一样，你需要实现`createVertexWriter()`方法，该方法返回一个实现`VertexWriter`接口的对象，该对象完成大部分工作。我们将其称为`GoraUserVertexWriter`。现在，让我们更详细地看一下`VertexWriter`的实现。

清单 10-13. 将顶点对象保存为 Gora 对象的示例实现

```java
public class GoraUserVertexOutputFormat
    extends GoraVertexOutputFormat<Text, DoubleWritable, NullWritable> {

    @Override
    public VertexWriter<Text, DoubleWritable, NullWritable>
    createVertexWriter(TaskAttemptContext context)
        throws IOException, InterruptedException {
        return new GoraUserVertexWriter();
    }

    protected class GoraUserVertexWriter extends GoraVertexWriter {

        @Override
        protected Object getGoraKey(
            Vertex<Text, DoubleWritable, NullWritable> vertex) {
            String goraKey = vertex.getId().toString();                          #1
            return goraKey;
        }

        @Override
        protected Persistent getGoraVertex(Vertex<Text, DoubleWritable, NullWritable> vertex) {
            GVertexResult userVertex = new GVertexResult();                      #2
            userVertex.setVertexId(vertex.getId().toString());                   #3
            userVertex.setVertexValue(vertex.getValue().get());                  #4
            return userVertex;
        }

    }

}
```

#1 提取顶点 ID，并将其字符串表示作为 Gora 键返回  

#2 创建一个空的`GVertexResult`对象，用于保存该顶点的输出  

#3 使用提供的 API 设置其顶点 ID  

#4 类似地，设置其顶点值  

首先，让我们看一下`getGoraKey()`方法的实现。该方法应返回一个用于在内存中唯一标识对象的对象。尽管这不是必须的，但使用顶点 ID 是最佳选择，因为你知道它唯一标识一个顶点。对于 Gora 键，你不需要`Writable`对象（例如`Text`），因此只需提取键的`String`表示并返回即可。

接下来，让我们看看`getGoraVertex()`方法的实现，它完成了大部分工作。在此方法中，你需要提取所有要存储在`Vertex`对象中的必要信息，并将其封装在 Gora 能理解的类型的对象中；即实现`Persistent`接口的对象。Giraph 源代码包含一个通用对象——名为`GVertexResult`——它实现了该接口，并允许你向其中存储信息。我们不会在此详细描述其实现；重要的是它抽象了`Persistent`接口的细节，并提供了简单的 API 调用，例如获取和设置顶点 ID、值以及边。该实现会处理将这些字段转换为正确底层表示的细节。

现在，让我们看看方法实现的主体。第一步是创建一个新类型的对象`GVertexResult`，它将保存输出。回顾一下，在此特定场景中，你关心的输出是顶点 ID 及其值；即计算出的距离。为此，你使用`GVertexResult`类的`setVertexId()`和`setVertexValue()`方法。尽管此处未展示，但这些方法的内部实现会将字符串和双精度浮点值转换为`GVertexResult`的预期格式。

此外，尽管在你的特定场景中不需要，但`GVertexResult`也允许你设置顶点的边。用于维护边信息的数据结构本质上是一个映射。清单 10-14 中的代码片段展示了如何使用它。

清单 10-14. 设置 GVertexResult 的边

```java
Iterator<Edge<Text, DoubleWritable>> it = vertex.getEdges().iterator();      #1
while (it.hasNext()) {
    Edge<Text, DoubleWritable> edge = it.next();
    userVertex.getEdges().put(                                                  #2
        edge.getTargetVertexId().toString(),                                    #3
        edge.getValue().toString()
    );
}
```

#1 遍历顶点的边  

#2 通过`getEdges()`方法访问边的数据结构  

#3 使用`put()` API 插入目标 ID 和边值  

至此，关于从 Gora 访问数据的内容就结束了。尽管你在此未学到，但 Giraph 也提供了基于边的输出格式实现，用于写入 Gora。同时请注意，尽管 Giraph 代码库中输出格式和接口（例如`GVertexResult`）的实现可以满足你的大部分需求，但你仍可能需要对其进行自定义。不过，现有的实现可以作为扩展你自己实现的完美指南。

## 总结

在前面几章中，你已经了解了如何使用 HDFS 作为数据输入与输出的基本存储。然而，Giraph 提供了一套灵活的 API，允许扩展至多种存储系统。在本章中，你探讨了基于表的内存存储系统。

* 基于表的存储系统因其能够存储和查询半结构化数据而变得相当流行。

* 在其他基于表的存储系统中，Giraph 提供了基于顶点和基于边的 API，用于访问 Hive 表中的数据。通常，你将单个 Hive 表行转换为一个 `Vertex` 或 `Edge` 对象。

* 内存存储系统是另一类流行的系统，允许快速访问数据。Apache Gora 提供了一种内存数据模型，该模型抽象了底层存储的细节，并允许你将其插入不同的存储系统以实现持久化。

* Giraph 进一步隐藏了从 Gora 内存模型中读取数据和写入数据的许多复杂性。你可以使用 `GoraVertexInputFormat` 和 `GoreEdgeInputFormat` API 在通用的 Gora 对象与 Giraph 的 `Vertex` 和 `Edge` 对象之间进行转换。

到现在，你应该已经熟悉了如何从多种数据存储中访问数据。Giraph 为比本章涵盖的更多系统提供了接口。你应该查阅代码库和文档以获取更多信息。你将在下一章继续学习更高级的功能——特别是调优和性能。

