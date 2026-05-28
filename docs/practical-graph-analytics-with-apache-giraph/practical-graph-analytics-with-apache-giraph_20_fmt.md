# 在每个超步结束后，于每个工作节点上调用

要理解如何扩展`WorkerContext`抽象类，首先必须了解`WorkerContext`对象的生命周期。对于 Giraph 作业运行的每个机器工作节点，Giraph 都会创建一个`WorkerContext`实例。在你的算法开始执行之前，Giraph 会为每个工作节点调用`preApplication()`方法。你可以使用此方法来设置`WorkerContext`。执行开始后，Giraph 会在每个超步开始前调用每个`WorkerContext`的`preSuperstep()`方法。超步结束后，Giraph 会调用`postSuperstep()`方法。最后，在你的算法执行完毕时，Giraph 会为每个工作节点调用`postApplication()`方法。在算法的整个执行过程中，顶点计算可以通过调用`getWorkerContext()`方法来访问`WorkerContext`对象。

接下来，你将看到如何在我们特定的场景中实现这些方法。你将创建一个`WorkerContext`，用于联系外部服务以获取性别分类器对象，并将其共享给同一工作节点上的所有顶点。清单 9-4 展示了实现。

## 清单 9-4. `WorkerContext`的示例实现

```
public class MyWorkerContext extends WorkerContext {

    private GenderClassifier classifier;              #1

    @Override
    public void preApplication() throws InstantiationException,
        IllegalAccessException {
        classifier = getClassifierFromService();        #2
    }

    public GenderClassifier getClassifier() {
        return classifier;                              #3
    }

}
```
#1 保存性别分类器的变量
#2 在应用程序启动前从外部服务加载数据
#3 提供对分类器变量的访问权限

首先，`WorkerContext`实现包含一个`GenderClassifier`类型的变量，这与原始算法中的类型相同，用于保存所有顶点将要共享的信息。回想一下，在这种情况下，你需要访问一个外部服务并读取一些数据以在所有顶点之间共享。这是一个你只需要执行一次的步骤，因为这些数据在算法执行过程中不会改变。`preApplication()`方法是执行此操作的理想选择。你使用此方法从外部服务获取分类器，使用与之前相同的 API 调用来连接服务。这确保了数据在任何顶点的计算发生之前被加载，并且只加载一次。在此示例中，你将保持`WorkerContext`类的其他方法为空。

现在，让我们看看如何修改算法，以便从顶点计算方法内部访问分区上下文中维护的共享数据。这如清单 9-5 所示。

## 清单 9-5. 修改后的算法以访问`WorkerContext`中的共享数据

```
public static class CountFollowersByGender
    extends AbstractComputation< Text, Text, NullWritable, Text, Text> {

    @Override
    public void compute(Vertex<Text, Text, NullWritable> vertex,
        Iterable<Text> messages) throws IOException {

        if (getSuperstep()==0) {
            MyWorkerContext context = (MyWorkerContext)getWorkerContext();   #1
            GenderClassifier classifier = context.getClassifier();           #2
            String gender = classifier.get(vertex.getId());
            sendMessageToAllEdges(vertex, new Text(gender));
            voteToHalt();
            return;
        }

        int femaleFollowers = 0;
        int maleFollowers = 0;

        for (Text msg : messages) {
            if (msg.toString().equals("female")) {
                femaleFollowers++;
            } else {
                maleFollowers++;
            }
        }

        String vertexValue =
            "female:"+femaleFollowers+", male:"+maleFollowers;
        vertex.setValue(new Text(vertexValue));
        voteToHalt();
    }

}
```
#1 获取工作上下文的引用并将其转换为你的实现类型
#2 获取`WorkerContext`中包含的`GenderClassifier`对象

在这个实现中，唯一的区别在于你如何获取`GenderClassifier`对象。你不会每次都从外部服务读取它，而是从一个已经为你加载好它的`WorkerContext`对象中获取它。Computation API 提供了`getWorkerContext()`方法，该方法返回`WorkerContext`对象。请记住，此方法返回的是`WorkerContext`父类的对象，因此在你开始使用它之前，必须将其强制转换为刚刚实现的类型。此时，你可以随心所欲地访问它。在这种情况下，你只需获取`GenderClassifier`对象的引用，并像之前一样使用它即可。

通过使用这种基于分区计算的简单概念，你不仅成功简化了应用程序，还使其更加高效。这种方式避免了为每个顶点加载和维护一个共享数据结构。这节省了计算开销、通信开销和内存，因为你不再需要多次复制相同的数据结构。本质上，你在此案例中所做的只是创建了一个简单的对象缓存，该缓存一次填充，然后在所有顶点之间重用。一旦你开始构建更复杂的应用程序，这项技术可能会非常有用。

最后你必须注意的一个细节是，如何告诉 Giraph 在启动作业时使用特定的`WorkerContext`实现。与 Giraph 的许多可定制方面一样，你可以在启动作业时将其作为命令行参数传递。具体来说，你可以将`giraph.workerContextClass`参数设置为你`WorkerContext`实现的完整类名。你可以通过在命令行中添加以下内容来实现：

```
–ca giraph.workerContextClass=MyWorkerContext.
```

## 使用案例：每工作节点性能统计

每工作节点计算的另一个有用场景，是监控并计算算法运行时的细粒度统计信息。请注意，Giraph 已经为您维护了一些总体统计信息，例如总处理时间、图的总体规模等。除此之外，您还可以在 Giraph 任务的日志中找到有用的性能统计信息。然而，您可能希望维护更精细的算法性能视图，例如检测特定于应用程序的性能问题。例如，您可能希望计算每个工作节点持有的顶点数，或每个工作节点发生的计算次数，以监控计算在各工作节点间的分布均匀程度。

为此，您将使用 `WorkerContext` 提供的相同功能：能够跨一个工作节点上的所有顶点共享一个对象。在此案例中，该对象持有性能统计信息，并可被每个顶点计算访问和修改。

在了解如何实现之前，我们先确定要维护的信息类型。在这个场景中，您希望统计每个工作节点上顶点计算的发生次数。这能让您了解每个工作节点的处理负载，并可能有助于检测节点间分布不均的情况。在应用程序结束时，您还需要以某种方式输出这些信息，以便呈现给运行算法的人。

现在，假设您已经构建了一个名为 `Performance` 的类，用于保存运行时性能信息，例如顶点计算次数。我们将保持此类简单，并假设它只包含一个递增计算次数的方法。显然，您可以扩展此类并维护更多的性能指标，如代码清单 9-6 所示。

## 代码清单 9-6. 用于维护简单性能统计的类

## Performance类与WorkerContext

```java
public class Performance {
    private int numComputations;         #1
    public void getNumComputations() {   #2
        return numComputations;
    }
    public void incNumComputations() {   #3
        numComputations++;
    }
}
```

#1 保存工作节点上的顶点计算次数
#2 返回当前工作节点的计算次数
#3 将工作节点的计算次数增加 1

接下来，您将实现一个 `WorkerContext`，通过它来操作这些性能统计信息。在此应用程序中，您需要在每次 `compute` 方法执行时更新性能统计信息；同时还需要输出这些统计信息。让我们看看代码清单 9-7 中 `WorkerContext` 的实现。

### 代码清单 9-7. 用于维护性能统计的 `WorkerContext` 实现

```java
public class PerfStatsWorkerContext extends WorkerContext {
    private Performance perf;

    @Override
    public void preApplication()
        throws InstantiationException, IllegalAccessException {
        perf = new Performance();                               #1
    }

    synchronized public void incComputations() {
        perf.incComputations();                                 #2
    }

    @Override
    public void postApplication() {                            #3
        String id =
            getContext().getConfiguration().get("mapred.task.id");  #4
        Counter counter =                                       #5
            getContext().getCounter(                             #5
                "Worker Computations",                          #5
                "Worker_"+id);                                  #5
        counter.increment(perf.getNumComputations());           #5
    }

    @Override
    public void postSuperstep() {                              #6
        String id =
            getContext().getConfiguration().get("mapred.task.id");
        Counter counter =
            getContext().getCounter(                             #7
                "Worker Computations",                          #7
                "Worker_"+id+"_"+getSuperstep());               #7
        counter.increment(perf.getNumComputations());
    }
}
```

### 性能统计

`#1` 初始化性能统计类
`#2` 在每个顶点计算时调用
`#3` 在最后输出统计数据
`#4` 获取工作节点 ID
`#5` 获取 Hadoop 计数器并设置值
`#6` 在每个超步后输出统计信息，用于细粒度分析
`#7` 根据工作节点 ID 和超步号构造计数器

### 使用 `Mapper.Context`

通常你可能需要从 `Mapper.Context` 对象中获取一些配置信息，该对象继承自 Hadoop。每个 Giraph 工作节点都被实现为一个 Hadoop 映射器，`Mapper.Context` 提供了映射器配置的相关信息。在这个场景中，你使用它来：（i）获取映射器的唯一索引，即 `mapred.task.id` 属性的值，也就是 Giraph 工作节点的唯一索引；（ii）访问 Hadoop 计数器接口。

在此场景中，`WorkerContext` 实现包含一个名为 `Performance` 的字段来保存统计信息。你使用 `preApplication()` 方法初始化该字段。该 `WorkerContext` 实现还提供了一个名为 `incComputations()` 的公共方法，通过调用 `Performance` 对象中的相应方法来增加计算次数。接下来你将看到如何以及何时调用此方法。

此外，你实现了 `postApplication()` 方法，用于输出算法执行期间收集的信息，即每个工作节点的总计算次数。除此之外，你还希望计算并输出所有工作节点的总计算次数。换句话说，你希望聚合不同工作节点维护的部分统计信息。Giraph 提供了多种向用户输出信息的方式。例如，第 7 章讨论了输出格式。这里，你使用了另一种机制，一种从 Hadoop 借鉴的机制，称为计数器。Hadoop 计数器通过简单的 API 使统计信息的聚合和输出变得容易。

除了最终的计算次数，你可能还想以更细的粒度衡量此指标，并观察每个超步期间的性能。你可以在 `WorkerContext` 的 `postSuperstep()` 方法中轻松执行相同的操作。Giraph 在每个超步结束后调用此方法，在算法执行期间提供详细信息。

### 使用 Hadoop 计数器

计数器是 Hadoop 提供的一种机制，用于以友好的方式向用户输出有用的统计信息。如果你运行过 Hadoop 或 Giraph 作业，你应该已经在输出中看到过计数器。Hadoop Web 界面或终端中显示的输出包含此类计数器。

计数器通常属于一个具有特定名称的计数器组，例如“Giraph Stats”。每个组可能包含多个计数器，每个计数器都有一个名称和关联值。例如，Giraph 计数器组包含显示每个超步所用时间的计数器。

你可以使用 Hadoop API 在代码中创建新计数器并为其赋值。实际上，计数器类似于聚合器；你可以从代码中的任何位置根据计数器名称递增其值，Hadoop 会负责计算计数器值的最终总和。

现在让我们来看最后缺失的部分：如何在应用程序内部更新性能统计信息。清单 9-8 展示了一个计算函数的实现。你省略了计算的细节，专注于如何访问 `WorkerContext`。可以想象，这可以是任何你实现的计算。

### 清单 9-8. 从应用程序内部修改性能统计信息

```java
public class MyComputation extends BasicComputation {

    @Override
    public void compute(Vertex<I, V, E> vertex,
        Iterable<M1> messages) {

        PerfStatsWorkerContext context =        #1
            (PerfStatsWorkerContext)getWorkerContext();

        context.incComputations()               #2
        ...                                       #3
        ...                                       #3
    }
}
```

`#1` 获取工作上下文句柄
`#2` 增加计算次数
`#3` 执行剩余的计算逻辑

在 `compute()` 方法内部，你只需获取工作上下文对象的引用。回想一下，你需要先将其强制转换为自定义实现的类型。之后，你可以使用任何已实现的自定义功能。在此示例中，你只是递增了计算次数。至此，实现已完成。请记住，要启用此 `WorkerContext` 实现，你必须在命令行中添加以下选项：

`–ca giraph.workerContextClass=PerfStatsWorkerContext`

如果忘记这样做，`Giraph`将使用默认实现，并且在运行时显然无法将`WorkerContext`对象强制转换为你的自定义实现。

### Giraph 中的线程安全

前两节介绍的两个用例都利用了同一功能——在顶点之间共享`WorkerContext`，但有一个重要区别：在第一个用例中，顶点只读取共享数据结构，而在第二个用例中，它们还会修改该数据结构。作为 Giraph 用户，你需要关注这一区别，因为它会影响程序的线程安全性。

你一定已经注意到，尽管本书整本都在讲述并行算法的设计与实现，但这是第一次讨论线程安全。在编写并行程序时，这通常是一个重要问题，也往往是各种问题的根源。不过，截至目前你所使用的 Giraph 核心编程模型，刻意隐藏了所有复杂的同步和线程安全问题。当你编写以顶点为中心的程序时，无需担心顶点是否同时执行，也无需关心它们的计算顺序。在 BSP 模型中，你假设每个顶点的计算都是独立的，并通过消息进行显式同步。

然而，随着`WorkerContext`的引入，你开始接触到 Giraph 内部机制的更多细节。这带来了你已看到的益处，但同时也要求你更加谨慎。具体来说，你现在必须意识到，同一`WorkerContext`实例可能被多个线程同时访问。回想一下，`Giraph`将图分割成多个分区，而单个工作机可能持有多个分区。每个分区的处理可能由不同的线程完成，从而利用同一工作机内部的并行化机会。

因此，你必须确保应用程序的线程安全性。在某些情况下，你可能只读取`WorkerContext`维护的数据结构，因此保证线程安全相对容易。前面的例子——从外部服务读取数据并让所有顶点读取——就属于这一类。在其他情况下，比如性能监控示例，你可能还会修改`WorkerContext`对象。在该示例中，你通过`incComputations()`方法实现了这一点。你可能已经注意到，你将此方法声明为`synchronized`，因为你预计该方法会被不同线程调用。这确保了性能统计数据的一致访问与修改。随着你在`WorkerContext`内部维护越来越复杂的数据结构，线程安全是需要关注的问题。

**注意**
你无需对实现的`preApplication()`、`postApplication()`、`preSuperstep()`和`postSuperstep()`方法进行同步。`Giraph`在每个超步中只调用这些方法一次。

在本节中，你重新审视了图的分区与分布概念，并学习了如何通过每次工作计算来利用其优势。你只了解了可以用`WorkerContext`实现的一小部分功能。你可以发挥更多创意，找到更高级的使用方式。通常，这需要你对算法有更多的信息和理解。无论如何，你应记住：将某些共享进程或共享数据从主应用逻辑中提取出来，放入`WorkerContext`中，可能会提升算法的性能。接下来，你将更进一步，学习如何实际控制 `Giraph` 在工作机之间对图进行分区的方式。

### 控制图分区

到目前为止，你并未关心`Giraph`如何将图分割成多个分区，因为这并不影响你编写算法。仔细想想，你只是在逻辑层面指定了每个顶点的计算和消息交换。顶点位于何处、消息如何交换其实并不重要。即使你在上一节进行了每工作机计算，你也无需关心工作机持有哪些顶点；`Giraph`抽象了这些细节。

在本节中，你将进一步探讨`Giraph`如何并行化处理的具体细节，特别是`Giraph`如何对图进行分区。你将看到，尽管`Giraph`在工作机之间分配图的方式不会影响算法的正确执行，但它可能会对图算法的执行性能产生影响。你将简要了解智能分区如何提升性能，然后重点关注`Giraph`为用户提供的可自由控制图分区的机制。

### 分区的重要性

在深入探讨`Giraph` API 的细节之前，我们先来看看为什么需要关注图分区。图分区决定了图的不同顶点如何分配到各个分区，并以不同方式影响算法执行的性能。首先，它直接影响图算法执行期间集群上的网络流量。我们通过图 9-2 中的示例图来理解这一点，看看不同分区策略对网络流量的影响。

![A978-1-4842-1251-6_9_Fig2_HTML.jpg](img/A978-1-4842-1251-6_9_Fig2_HTML.jpg)

**图 9-2.** 一个包含六名用户的社交网络示例图

假设你的集群只有两台工作机器`A`和`B`，并且`Giraph`以随机方式将图分区到这些机器上。具体来说，`Giraph`会计算顶点 ID（本例中为用户名称）的哈希值。如果哈希值为偶数，则将顶点分配到机器`A`；如果为奇数，则分配到机器`B`。这几乎等同于随机决定顶点的放置位置，就像`Giraph`掷硬币一样，将顶点分配到两台机器中的一台。实际上，这就是`Giraph`的默认分区策略。假设最终放置结果如图 9-3 所示。

![A978-1-4842-1251-6_9_Fig3_HTML.jpg](img/A978-1-4842-1251-6_9_Fig3_HTML.jpg)

**图 9-3.** 六节点社交图在两台工作机器上的示例分区

### Giraph 中的哈希分区

`Giraph`中的默认分区策略是哈希分区。假设`Giraph`需要将图划分到`P`个分区中。对于每个顶点，`Giraph`计算顶点 ID 的哈希码`H`，然后计算`H mod P`（即`H`除以`P`的余数）。该运算的结果就是该顶点所属的分区。

哈希分区是一种非常常见的数据分区技术。它计算简单，并且能实现数据项（本例中为顶点）在分区间的均匀分布。

现在，假设在算法某个超步的执行过程中，每个顶点都向其所有邻居发送一条消息。这是一种非常常见的模式；例如，你在第 3 章中看到的`PageRank`算法就是这样运行的。从该图中可以明显看出，有 10 条消息会跨机器传输，从而在集群上产生网络流量。其余消息，例如从 Natalie 发送给 Anna 的消息，则会在各工作机器的内存内部进行交换。

接下来，我们看看如果不采用掷硬币式的随机放置方式，而是以更智能、更知情的方式来决定顶点放置，会发生什么。具体来说，我们尝试确保每个顶点的大部分邻居都位于同一台工作机器上。图 9-4 展示了另一种分区方案。目前，我们假设你有办法指示`Giraph`按照你的意愿放置顶点。

![A978-1-4842-1251-6_9_Fig4_HTML.jpg](img/A978-1-4842-1251-6_9_Fig4_HTML.jpg)

**图 9-4.** 另一种跨两台工作机器的分区方案，产生的网络流量更少

显然，就产生的网络流量而言，这种分区方案更为有利。图中只有一条图边跨越了两台工作机器，因此只有两条消息通过网络传输：一条从 Peter 到 Anna，另一条从 Anna 到 Peter。

尽管对于如此小的图，不同的分区方案在性能上可能看不出明显差异，但当你开始使用真实的大型图时，分区就变得至关重要了。跨工作机器的大量消息会给你的网络带来巨大负担，可能影响可扩展性并增加`Giraph`作业的处理时间。

请注意，寻找好的分区方案是图管理研究的一个完整领域，可能取决于多种因素，例如图的类型和所使用的算法。研究不同的分区技术超出了本章的范围。相反，我们这里重点介绍`Giraph` API，它允许你插入不同的分区方案。也就是说，一旦你找到了将顶点映射到分区的合适技术，就可以通过 API 提供的一种简便方式将其插入`Giraph`，并在执行过程中强制执行该映射。后续小节将对此进行介绍。

### 实现自定义分区器

如您所见，`Giraph`使用的默认哈希分区技术并未考虑产生的网络流量，因此您可能希望用更智能的分区方式替换它。通常，您可以通过实现一个名为`WorkerGraphPartitioner`的接口来完成此操作，该接口负责将顶点分配给各个分区。

为了说明该接口的用法，我们将以网页图为例，描述一种在处理如图 9-5 所示的网页图时常用的替代分区技术。

![A978-1-4842-1251-6_9_Fig5_HTML.jpg](img/A978-1-4842-1251-6_9_Fig5_HTML.jpg)

**图 9-5.** 一个网页图示例

回顾一下，网页图描述了网页之间如何通过链接相互连接。在第 3 章中，您看到了可能希望在网页图上运行的算法示例，例如`PageRank`。在此图中，顶点代表网页，边代表从一个页面到另一个页面的链接。在`Giraph`中，您将网页 URL 用作顶点的 ID。

为简单起见，假设我们的计算集群再次由两台工作机器`A`和`B`组成。和之前一样，如果使用默认的哈希分区，`Giraph`会随机放置顶点，分区结果将如图 9-6 所示，其中有多条边跨越这两台机器。

![A978-1-4842-1251-6_9_Fig6_HTML.jpg](img/A978-1-4842-1251-6_9_Fig6_HTML.jpg)

**图 9-6.** 在两个工作节点上对网页图进行基于哈希的分区

在这个网页图中，我们可以采用一种更好的方法。请记住，在 Twitter 图的例子中，智能分区基于这样一个理念：您应尽最大努力将某个顶点及其邻居顶点放在同一个工作节点上。在小型 Twitter 图中，这很简单，您只需直观地查看图就能轻松完成分配。在这里，我们将使用一种更有原则性的策略来决定放置代表网页的顶点的位置。具体来说，该策略基于这样一个事实：网页上的大多数链接都指向通常位于同一域名下的其他网页。例如，`cnn.com/index.html` 上的大多数链接都指向诸如 `cnn.com/technology.html` 和 `cnn.com/economy.html` 之类的页面，这些页面都属于 `cnn.com` 域名。这应该已经让您对如何划分图有了提示。我们将使用一种类似哈希分区的技术，但不是对顶点 ID 进行哈希，而是仅对域名前缀进行哈希；例如，前缀 `cnn.com`。这确保了具有相同域名下 URL 的顶点被放置在同一工作节点上。图 9-7 展示了这种分区方式。

![A978-1-4842-1251-6_9_Fig7_HTML.jpg](img/A978-1-4842-1251-6_9_Fig7_HTML.jpg)

图 9-7. 基于每个网页域名前缀对同一网页图的分区

接下来，我们将研究如何实现 `WorkerGraphPartitioner` 来应用此逻辑。但在深入分区器的核心之前，我们先介绍一些您必须首先实现的辅助性但必要的其他接口。

首先，您必须实现 `GraphPartitionerFactory` 接口。`GraphPartitionerFactory` 对象负责创建实际分区器实现的实例。具体来说，Giraph 使用 `GraphPartitionerFactory` 来实例化 `MasterGraphPartitioner` 和 `WorkerGraphPartitioner` 对象，以执行所有主要操作。在代码清单 9-9 中，您可以看到该接口中您必须实现的仅有的两个方法。

**代码清单 9-9.** `GraphPartitionerFactory` 接口

```
public interface GraphPartitionerFactory<I extends WritableComparable,
    V extends Writable, E extends Writable> extends
    ImmutableClassesGiraphConfigurable {
```