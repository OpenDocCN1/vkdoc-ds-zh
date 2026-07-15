
# 8. 超越基础 API

本章涵盖：
- 图突变
- 聚合器 API
- 顶点协调
- 编写模块化应用

在前面的章节中，你学习了如何使用 Giraph 编程 API 的基础部分来实现各种图算法。虽然基础 API 已经为你提供了足够的灵活性来构建大量有用的图挖掘算法，但本章将介绍 Giraph API 中超越基础的功能特性，这些特性使你能够编写更复杂的应用。Giraph API 功能丰富，允许你为应用添加更多功能、更高效地编写算法，甚至从编程角度让你的工作更轻松。

虽然我们通常将输入图视为不可变的数据结构，但本章将讨论你想要通过添加或删除顶点和边来改变输入图的场景。Giraph 提供了一个允许你对输入图执行此类突变的 API。我们将使用示例来描述使用此 API 的不同方式。

接下来，我们将重新审视聚合器，这是一种允许你计算整个图的全局统计信息的工具。在前面的章节中，你已经看到了典型聚合器的示例，并学习了如何在应用中使用它们。在此，我们将介绍 Giraph 提供的用于编写自定义聚合器的 Java API；并通过实现聚合器来进行说明。

此外，分布式算法中最重要的特性之一就是协调。到目前为止，你已经学会了以顶点为中心的方式进行思考，编写程序使得顶点之间通过消息以分布式方式进行通信。Giraph 负责协调算法的执行。在此，你将学习如何在希望进一步自定义算法执行时干预这一协调过程。

本章还将涵盖 Giraph API 中那些从编程角度让你更容易编写复杂应用的特性。你将学习如何将一个可能很复杂的算法分解为执行不同逻辑操作的模块，从而产生更清晰、更易于理解的代码。反过来，你也将学习如何将这些模块组合到不同的应用中，从而提高代码复用率并节省编程工作量。

## 图表突变

在本小节中，您将探索在算法执行期间改变图结构的能力。通常，改变图意味着添加或移除顶点和边。虽然大部分情况下，您考虑的是分析输入的图，但在这里您会看到，改变图通常是此分析过程的一部分。

出于不同的原因，图突变可能是必要的。在某些场景中，您可能只是想转换输入图；例如，作为预处理步骤。您已经在第 3 章和第 5 章中看到了图转换的示例，那里您学习了如何将有向图转换为无向图。在这种情况下，图突变是一个自然的需求。

在其他场景中，您分析的结果可能是一个与输入图完全不同的图。一个常见的例子是，您想将一个输入图划分为社区或集群，然后分析这些社区之间的连接关系。社区检测是社交网络分析中的一个常见应用。在在线社交网络中，您可能希望找到具有相似特征的用户社区，并输出这些社区本身是如何相互连接的。在这种情况下，社区检测算法的输出是一个代表社区的图，这是一个与您的输入图完全不同的图结构。这意味着，在算法执行期间，您必须以某种方式创建一个图；也就是说，创建新的顶点并用新的边将它们连接起来。

图生成器是另一个需要图突变的应用场景。图生成器是有用的工具，允许用户构建符合某些模型（如流行的“小世界”模型）的合成图。事实上，Giraph 代码已经包含了几个图生成器的实现。在这种情况下，图突变是自然而然的；这些工具可以从一个空图或一个初始种子图开始，逐步添加顶点和边，直到构建出最终的图。

在深入了解允许您更改图的 API 之前，理解改变输入图意味着什么非常重要。在这一点上，您必须记住，Giraph 将输入图加载到内存中，并在内部表示中维护一个副本。Giraph 操作并可以（如果需要）改变的就是这个图的副本。图的原始副本保持不变。例如，如果您将图存储在 Hadoop 集群的 HDFS 目录中，然后执行一个使用突变 API 的算法，那么 HDFS 上的图数据在执行后将完全相同。

当然，用户可以改变这个图的内存副本，并将一个新的副本输出到某个外部存储系统。在将有向图转换为无向图的情况下，您将创建该图的一个无向形式的新副本。这将是 Giraph 作业的输出。

注：用户始终可以在顶点计算函数内部修改原始图。假设该图存储在一个表存储系统中，例如 HBase。没有任何东西可以阻止用户编写一个连接到 HBase 并修改图的顶点计算。然而，这样的操作将超出 Giraph 的范围。Giraph 不保证这些更改的一致性。例如，如果某个 Giraph 工作节点发生故障，Giraph 保证能产生正确的结果，但不保证这种外部更改的正确性。这是 Giraph 编程 API 灵活性带来的一种能力和风险。如果您选择利用这种灵活性，您应该意识到所有风险，并格外小心地设计您的程序，以确保它不会引起任何问题，尤其是在生产环境中使用时。

既然我们已经讨论了图突变的实用性，那么让我们来探索赋予您这种能力的实际 API。第 5 章简要介绍了突变 API，而这里将通过多种示例对其进行详细解释。

## 突变 API

Giraph 提供了三种在执行期间改变图结构的方法：（i）对顶点的直接突变，（ii）突变请求，以及（iii）通过消息进行突变。这些方法可能允许不同类型的图突变，并且在突变何时实现方面具有不同的语义。接下来，您将回顾每种突变类型的 API 并说明它们的用法。

### 直接突变

为了说明直接突变 API 的用法，让我们使用一个您已经在第 5 章中看过的示例。在这个示例中，您想要将一个输入图从有向图转换为无向图。图 8-1 展示了这种转换。

![A978-1-4842-1251-6_8_Fig1_HTML.jpg](img/A978-1-4842-1251-6_8_Fig1_HTML.jpg)

**图 8-1.** 将有向图转换为无向图

在图 8-1 中，您看到这种转换需要您更改图结构。特别是，您需要在现有顶点之间添加一些额外的边。

`Vertex` 接口包含了许多方法，允许顶点计算函数直接更改顶点的结构——即更改其边。

*   它们只能更改调用它们的顶点。
*   这种调用的效果是立即生效的。

让我们看看允许改变其结构的 `Vertex` 方法：
*   `addEdge(Edge<I, E> edge)`：向顶点添加一条边。边的 ID 和值通过传递的 `Edge` 对象指定。
*   `removeEdges(I targetVertexId)`：从顶点中移除所有具有指定目标 ID 的边。
*   `setEdges(Iterable<Edge<I, E>> edges)`：通过遍历传递的 `Iterable` 并逐条添加边来设置顶点的出边。

在清单 8-1 中，让我们重新审视第 5 章中的示例代码；接下来将详细解释它使用突变 API 的方式。

**清单 8-1.** 将图从有向图转换为无向图

```java
public class ConvertToUndirected extends
  BasicComputation<Text, DoubleWritable, NulleWritable, Text> {

  static final NullWritable DEFAULT_EDGE_VALUE = NullWritable.get();

  @Override
  public void compute(Vertex<Text, DoubleWritable, DoubleWritable> vertex,
                      Iterable<Text> messages) {
    if (getSuperstep() == 0) {                                           #1
      sendMessageToAllEdges(vertex, vertex.getId());                     #1
    } else {
       for (Text m : messages) {                                         #2
          if (vertex.getEdgeValue(m)==null) {                            #3
           vertex.addEdge(EdgeFactory.create(m, DEFAULT_EDGE_VALUE));    #4
         }
      }
    }
    vertex.voteToHalt();
  }
}
```

在第一个超步中，每个顶点都将其自身的 ID 发送给它的所有邻居。
在第二个超步中，顶点接收到来自每个拥有指向它的边的顶点的消息。
对于收到的每一条此类消息，顶点检查对应的边是否已经存在。
如果边不存在，顶点则添加该边。

请注意，`addEdge` 方法将一个实现了 `Edge` 接口的对象作为输入。虽然您可以实现自己的 `Edge` 类型，但 Giraph 已经提供了在大多数情况下应该足够使用的默认实现。特别是，您可以使用 `EdgeFactory` 实用类的 `create()` 方法来创建 `Edge` 对象。

### 变异请求

接下来，你将看到一个示例，说明直接变异可能并不总是适合修改图结构，并了解变异请求如何提供更高的灵活性。假设你想分析一个类似 Twitter 的社交网络，并探索不同国家的用户是如何关联的——而不是像你之前做的那样分析单个用户连接。从原始的社交图谱开始，你想创建一个新图谱，其中每个顶点对应一个国家，边表示这些国家的用户之间存在连接关系。这让你能够构建 Twitter 图谱的高层视图，并在国家层面观察社交影响力，如图 8-2 所示。

![A978-1-4842-1251-6_8_Fig2_HTML.jpg](img/A978-1-4842-1251-6_8_Fig2_HTML.jpg)

**图 8-2.** 分析不同国家用户之间的连接关系

很明显，在分析原始图谱的同时，你也在创建一个新图谱。事实上，新图谱才是该算法的实际输出。然而，由于这个新图谱的顶点并非预先存在，你无法像之前那样直接使用直接变异。让我们看看变异请求是如何解决这个问题的。

变异请求是 `Computation` 接口中的方法。就像直接变异一样，你可以在顶点计算函数中调用这些方法。但是，与直接变异不同，变异请求具有以下属性：

-   它可以修改图的任何部分，无论发起请求的是哪个顶点。例如，顶点 A 可以请求在顶点 B 和 C 之间添加一条边。

-   变异请求的效果只有在当前超步完成后、下一个超步开始前才可见。

### 注意

第二个属性起初可能看起来有点不自然，但它实际上是为了简化你的程序。这个属性允许你将处理顶点和边创建或删除的逻辑与其余编程逻辑分离开来。即使顶点请求修改了同一个顶点，该请求对发起请求的顶点剩余的当前超步计算也没有影响。它也不会影响其他顶点在请求发出后、但仍在同一超步内进行的计算。

接下来，让我们看看这个 API 的实际应用。

清单 8-2 显示了这个算法的实现。虽然逻辑可能不是一目了然，但所有部分稍后都会解释清楚。

**清单 8-2.** 在 Twitter 图谱中查找国家连接关系

```java
public class CreateCountryGraph extends
BasicComputation<Text, Text, NullWritable, Text> {

  static final NullWritable DEFAULT_EDGE_VALUE = NullWritable.get();

  @Override
  public void compute(Vertex<Text, Text, NullWritable> vertex,
                      Iterable<Text> messages) {
    Text myCountry = vertex.getValue();
    if (getSuperstep()==0) {                                             #1
      sendMessageToAllEdges(vertex, myCountry);                          #1
    } else {
      for (Text m : messages) {                                          #2
        addEdgeRequest(m,                                                #3
          EdgeFactory.create(myCountry, DEFAULT_EDGE_VALUE));            #3
      }
      removeVertexRequest(vertex.getId());                               #4
      vertex.voteToHalt();
    }
  }
}
```

#1 向我关注的每个用户发送我自己国家的名称。

#2 在超步 1，每条消息都包含一个关注我的用户所在的国家。

#3 对于每条消息，创建一条从对方国家指向我所在国家的新边。

#4 每个用户顶点将自己从图中移除，这样最后剩下的只有国家顶点。

在这个场景中，顶点的 ID 类型为 `Text`，代表用户的名称，而顶点的值类型也是 `Text`，代表用户所在的国家。算法运行的大致思路应该很熟悉。你需要了解关注关系图中隐藏着哪些国家之间的配对关系。自然的做法是让每个顶点将其所属国家告知其邻居。算法的第一个超步正是实现了这一逻辑。每个顶点向它关注的顶点发送自己的顶点值，即其对应的国家（#1）。

在第二个超步中，每个顶点现在都知道它的粉丝来自哪些国家。换句话说，它知道了在新的国家图谱中应该存在怎样的边。此时，它可以使用 `addEdgeRequest()` 方法（#3）简单地请求创建一条从粉丝所在国家指向自己国家的边。`addEdgeRequest()` 方法会请求在指定源 ID 的顶点处添加一条边。边的值和目标 ID 通过传递的边对象来指定。

### 注意

你不需要显式创建代表国家的顶点。通过请求在两个国家之间创建一条边，Giraph 会自动创建相应的顶点（如果它们尚不存在）。

不过，你还没有完成。通过创建这些新边，算法执行结束时存在两种类型的顶点：构成原始图谱的用户顶点和构成国家图谱的国家顶点。如果你使用输出格式，你会看到输出包含这两种类型的顶点。但在这个案例中，你只关心国家图谱，所以理想情况下你希望最终的图只包含代表国家的顶点。

变异请求 API 为此提供了解决方案。除了修改顶点的边之外，变异请求 API 还允许你创建或删除顶点。为此，你可以使用 `removeVertexRequest()` API。在超步 0，除了创建新边和新顶点，你还可以移除不需要的顶点。每个顶点还会通过调用 `removeVertexRequest()` 方法并传入自己的 ID 作为参数，来请求移除自身。

### 算法细节

细心的读者可能已经注意到，在超步 0 中没有任何顶点停止。想象一下，如果那些没有粉丝的顶点在超步 0 就停止了会发生什么。它们永远不会在超步 1 中收到消息，因此它们不会被激活并运行 `compute()` 函数。通过在超步 0 不让顶点停止，你可以让每个顶点都在超步 1 运行 `compute()` 函数，从而允许它们通过 `removeVertexRequest()` 方法从图中移除自身。

最后，请记住，通过调用 `addEdgeRequest()` 和 `removeVertexRequest()` 方法，顶点只是向 Giraph 注册了这些请求。边并不会立即创建，顶点也不会立即被移除。你必须等到下一个超步，这些更改才会真正生效。

除了你在示例中看到的这两个方法之外，`Computation` 接口还提供了以下方法：

-   `addVertexRequest(I id, V value, OutEdges<I, E> edges)`: 发起请求，创建一个具有指定 ID、值和边的顶点。

-   `addVertexRequest(I id, V value)`: 发起请求，创建一个具有指定 ID 和值的顶点。创建的顶点没有边。

-   `removeVertexRequest(I vertexId)`: 发起请求，移除具有指定 ID 的顶点。如果具有指定 ID 的顶点不存在，则不执行任何操作。

-   `addEdgeRequest(I sourceId, Edge<I, E> edge)`: 发起请求，在具有指定源 ID 的顶点处添加一条边。边的值和目标 ID 通过传入的边对象指定。请注意，此请求仅影响具有源 ID 的顶点。

-   `removeEdgesRequest(I sourceId, I targetId)`: 发起请求，从具有传入源 ID 的顶点中移除一条边。具体来说，它移除具有传入目标 ID 的那条边。如果该边不存在，则不执行任何操作。此调用仅影响源顶点，不影响目标顶点。

尽管这个示例没有使用这些方法，但它们具有相似的语义。它们允许的修改并非立即生效，而是在下一个超步开始时才实现。

## 通过消息进行变更

接下来，让我们看看在 Giraph 中修改图的第三种替代方式。除了通过之前的 API 进行显式图变更外，Giraph 还允许通过向不存在的顶点发送消息来隐式地创建一个顶点。让我们来看看这究竟意味着什么，以及它如何发挥作用。

让我们看看如何通过前面的例子来使用这种创建顶点的方式。事实上，你将通过计算更多信息来丰富它。假设现在你不仅想知道哪些国家相互连接，还想知道来自一个国家的用户有多少关注了来自另一个国家的用户。这为我们提供了对国与国之间关系更全面的视角；你还可以了解国家间关系的强度，以及从总粉丝数来看，哪个国家是被关注最多的。图 8-3 展示了通过分析原始输入图你希望得到的结果。

![A978-1-4842-1251-6_8_Fig3_HTML.jpg](img/A978-1-4842-1251-6_8_Fig3_HTML.jpg)

**图 8-3.** 计数来自一个国家的用户关注了来自另一个国家的用户的数量

现在让我们来看一下计算这些更详细统计数据的算法。与之前的示例不同，这里我们假设边的值类型为`IntWritable`，用于在程序执行结束时保存粉丝数。清单 8-3 展示了其实现。

清单 8-3。计算国家间粉丝关注的数量

```
public class CreateCountryGraphWithCounts extends
    BasicComputation<Text, Text, IntWritable, Text> {

  @Override
  public void compute(Vertex<Text, Text, IntWritable> vertex,
      Iterable<Text> messages) {
    Text myCountry = vertex.getValue();
    if (getSuperstep()==0) {                                             // #1
      sendMessageToAllEdges(vertex, myCountry);                          // #1
    } else if (getSuperstep()==1) {                                      // #2
      for (Text m : messages) {                                          // #2
        sendMessage(m, myCountry)                                        // #3
      }
      removeVertexRequest(vertex.getId());                               // #4
      vertex.voteToHalt();
    } else {                                                             // #5
      for (Text m : messages) {                                          // #6
        IntWritable edgeValue = vertex.getEdgeValue(m);                  // #7
        if (edgeValue == null) {                                         // #7
          vertex.addEdge(EdgeFactory.create(m, new IntWritable(1)));     // #7
        } else {                                                         // #8
          vertex.setEdgeValue(m, new IntWritable(edgeValue.get()+1));    // #8
        }
      }
      vertex.voteToHalt();
    }
  }
}
```

- `#1` 告诉我正在关注的用户，我来自哪个国家。
- `#2` 在超步 1，每条消息都包含一个关注我的用户所在的国家。
- `#3` 对于每一个关注我的国家，向一个 ID 设置为该特定国家的目标发送一条消息。消息中包含此顶点自己的国家名称。
- `#4` 和之前一样，每个用户顶点将自己从图中移除。
- `#5` 在超步 2，代表国家的顶点被创建并接收消息。
- `#6` 消息包含一个国家名称，代表该国家中一个用户的关注关系。
- `#7` 对于每一个这样的国家，检查此顶点是否已经有一条到它的边。如果没有，添加一条新边并将其初始值设为 1。
- `#8` 如果该国家已经存在，则对于每一条包含相应国家名称的消息，将其边的值加一。

## 算法细节

在初始的用户图中，边的值无关紧要，你只需假设当图通过使用`InputFormat`构建时，其值被设置为`0`。

回顾一下，Giraph 模型允许一个顶点通过简单地指定目标的 ID 来发送消息。在大多数情况下，一个顶点会向其邻居之一发送消息。它通常通过查看其边（在加载输入图期间构建）来选择一个 ID。然而，Giraph 的 API 实际上更加灵活，允许你在`sendMessage()`方法中使用任何 ID。既然你可以指定任何 ID，你可能想知道如果你指定了一个输入图中实际不存在的 ID 会发生什么。

答案是 Giraph 会为你创建该顶点。这样，你的程序可以通过向一个顶点发送消息来动态地创建它。在这种情况下，创建的顶点被初始化为没有边，并且顶点值被设置为`null`。

> **注意**：作为程序员，你有责任编写代码来处理这种初始化。

该算法的第一部分与之前相同。在超步 0，每个顶点通过消息将自己的国家发送给它关注的顶点（`#1`）。因此，在超步 1，每个用户顶点都会收到包含国家名称的消息。

在开始超步 2 之前，Giraph 发现有消息是发送给代表国家的顶点的。这些顶点尚不存在。因此，Giraph 会创建它们，然后超步 2 像往常一样开始执行。它将消息传递给所有顶点，并调用`compute()`函数。

在这种情况下，只有新创建的国家顶点有消息。消息同样包含国家名称。国家 A 收到的一条包含国家名称 B 的消息意味着存在一条从国家 A 到国家 B 的单个关注关系。首先，这意味着代表国家 A 的顶点和代表国家 B 的顶点之间应该有一条边（`#7`）。其次，通过统计包含国家 B 的消息数量，你就能够确切地知道来自国家 A 的用户中有多少人关注了来自国家 B 的用户。这个数量就是所创建边的值。

现在，你已经了解了在算法执行过程中修改图结构的三种不同方式。通常，使用哪一种取决于你的算法以及你的创意。有时你会发现可以用这些方式中的任何一种来实现一个算法，而在其他场景中，你会发现更倾向于某一种变更类型。例如，当你只需要向顶点添加边，并且每个顶点只负责添加自己的边时，就像第一个例子那样，直接变更可能是更好的选择，因为它们更快。另一方面，如果你需要创建顶点，或者某个顶点负责向其他顶点添加边，那么变更请求就是解决方案。总的来说，你需要稍微思考一下算法的设计，然后选择最合适的选项。

## 处理变更冲突

尽管之前的示例中并未体现，但变更请求中可能发生冲突。例如，两个顶点可能发出以下冲突请求：一个顶点请求添加一条具有特定值的边，而另一个顶点可能请求创建同一条边但带有不同的值。此时这条边的值应如何确定？有时甚至会出现更令人困惑的请求，比如某个程序可能同时发出向一个顶点添加边的请求，以及删除该顶点的请求。

乍看之下，这类请求似乎不合逻辑；你可能会疑惑为什么会有人编写这样的程序。但你会发现，在编写分布式程序时，不同计算过程之间出现此类分歧是不可避免的。事实上，只要存在解决冲突的方式，这种设计就是分布式算法的一部分。不妨想想一个简单的多线程程序：两个线程试图递增同一个共享计数器。如果程序设计不当，结果将是未定义的，而编程语言通常提供同步原语来帮助正确设计程序。接下来你将看到`Giraph`如何解决这类冲突。

首先，回顾一下创建推特国家关系图的示例。代码如 8-4 所示。请注意，"Mark"顶点和"Anna"顶点都发出请求，为"Germany"顶点创建一条目标 ID 为"UK"的边。

每当一个顶点发生一组变更（如添加或删除边）时，`Giraph`会使用`VertexChange`对象表示这些变更，并使用`VertexResolver`对象（它以`VertexChange`作为输入）来定义实际处理变更的方式。我们先从描述 8-4 中的`VertexResolver`接口开始。

**代码清单 8-4.`VertexResolver`接口**

```
public interface VertexResolver<I extends WritableComparable,
                                V extends Writable, E extends Writable> {
    Vertex<I, V, E> resolve(I vertexId,                        #1
                            Vertex<I, V, E> vertex,            #2
                            VertexChanges<I, V, E> vertexChanges, #3
                            boolean hasMessages);              #4
}
```

`#1` 待解析的顶点 ID。

`#2` 应用变更前的原始顶点。

`#3` 该顶点的变更集合。

`#4` 定义该顶点在上一个超步中是否有发往它的消息。

`VertexResolver` 接口只有一个方法。`Giraph` 会为每个顶点调用该方法，并将已发生的变更集合传递给它。当你实现此方法时，你本质上就定义了变更后 `Vertex` 应呈现的状态。如 8-5 所示的 `VertexChange` 接口有助于确定请求对该顶点执行的变更。

**代码清单 8-5. `VertexChanges` 接口**

```java
public interface VertexChanges<I extends WritableComparable,
                               V extends Writable, E extends Writable> {
    List<Vertex<I, V, E>> getAddedVertexList();   #1
    int getRemovedVertexCount();                   #2
    List<Edge<I, E>> getAddedEdgeList();           #3
    List<I> getRemovedEdgeList();                  #4
}
```

#1 返回请求针对此顶点 ID 添加的顶点列表。

#2 返回针对此顶点 ID 的删除请求数量。

#3 返回请求添加到此顶点的边列表。

#4 返回请求从此顶点移除的边列表。

在实现你自己的 `VertexResolver` 之前，先看看如果让 `Giraph` 使用默认的 `VertexResolver` 会发生什么。默认的顶点解析器会执行以下操作：如果存在任何边移除请求，首先执行这些移除操作。如果存在顶点移除请求，则将其移除。这通过将返回的 `Vertex` 对象设置为 `null` 来实现。如果存在顶点添加请求，且该顶点不存在，则创建该顶点。如果顶点有消息发送给它，且该顶点不存在，则创建该顶点。如果存在向顶点添加边的请求，若该顶点不存在，则先创建顶点再添加边；否则，直接添加边。

此列表的顺序很重要，因为它精确地定义了 Giraph 默认解决冲突的方式。这意味着，例如，如果同时存在移除顶点和添加顶点的请求，那么由于默认解析器在删除顶点之后才检查顶点创建，因此最终会创建该顶点。

## 默认顶点解析器实现

尽管此处未展示 `DefaultVertexResolver` 的实现，但建议你查看其源代码作为额外示例。这能让你更深入地了解 `VertexResolver` 的用法。

现在假设你想更改此默认行为。具体来说，让我们稍微不同地处理同一边的多个添加请求。与仅仅将边添加到顶点不同，你实际上希望统计存在的请求数量，并将此数量设置为边的值。这本质上为你提供了一种替代方法来统计一个国家关注另一个国家用户的数量。在清单 8-6 中，让我们看看如何通过创建一个自定义 `VertexResolver` 来简单地实现这一点。

**清单 8-6. 一个计算边添加次数的自定义 VertexResolver**

```java
public class MyVertexResolver implements VertexResolver<Text, Text, IntWritable, Text> {

    @Override
    public Vertex<Text, Text, IntWritable> resolve(
        Text vertexId,
        Vertex< Text, Text, IntWritable> vertex,
        VertexChanges<Text, Text, IntWritable > changes,
        boolean hasMessages) {

        if (changes!=null) {
            if (!changes.getAddedEdgeList().isEmpty()) {         #1
                if (vertex==null) {                             #2
                    vertex = getConf().createVertex();          #2
                    vertex.initialize(vertexId,                 #2
                        getConf().createVertexValue());
                }
                for (Edge<I, E> edge : changes.getAddedEdgeList()) {
                    IntWritable edgeValue =                     #3
                        vertex.getEdgeValue(edge.getTargetVertexId()); #3
                    if (edgeValue==null) {
                        edge.setValue(new IntWritable(1));      #4
                        vertex.addEdge(edge);                   #4
                    } else {
                        vertex.setEdgeValue(edge.getTargetVertexId(),  #5
                            new IntWritable(edgeValue.get()+1));       #5
                    }
                }
            }
        }
    }
}
```

#1 首先，检查是否存在边添加请求。

#2 如果存在边添加请求，且该顶点尚不存在，则创建它。

#3 对于每条边的添加，检查该边是否已存在。

#4 如果不存在，则添加它并将其值设置为 1。

#5 如果已存在，则仅将其值加 1。

在此示例中，你假设只需要处理边添加请求。自定义解析器首先检查是否存在任何此类添加请求 (#1)。如果有，则需要检查源顶点是否已存在。回想一下，在这种情况下，你创建了表示国家的顶点，而这些顶点尚不存在。因此，如果顶点尚不存在，则需要创建它 (#2)。之后，每当你看到同一边的请求时，只需将该边的值加 1 (#5)。

现在你已经实现了自定义解析器，唯一缺少的是告诉 Giraph 使用此特定的顶点解析器实现来替代默认实现。和往常一样，通过熟悉的命令行自定义参数来实现。只需将以下内容添加到命令行：`-ca giraph.vertexResolverClass=MyVertexResolver`。

总体而言，突变是一种高级特性，一旦你开始考虑更复杂的应用程序，就会发现它非常有用。接下来，将讨论另一个高级特性：如何编写你自己的聚合器。

## 聚合器 API

聚合器是一种非常有用且易于使用的工具，它允许你计算整个图的全局统计数据。你已经在第 3 章和第 5 章中看到了几个使用聚合器的示例。Giraph 已经提供了一组通用的聚合器函数（例如 sum 或 max），你可以在应用程序中使用它们。同时，Giraph 允许你实现自己的聚合器函数。本节将更详细地解释聚合器接口，并展示如何编写新的聚合器。

Giraph 提供了两种实现自定义聚合器的方式。第一种方式是实现 `Aggregator` 接口，这为你实现聚合器提供了最大的灵活性。清单 8-7 展示了该接口的方法。

**清单 8-7. Aggregator 接口**

```java
public interface Aggregator<A extends Writable> {
    void aggregate(A value);            #1
    A createInitialValue();             #2
    A getAggregatedValue();             #3
    void setAggregatedValue(A value);   #4
    void reset();                       #5
}
```

#1 将输入值聚合到聚合器的当前值。

#2 在任何聚合发生之前创建聚合器的初始值。

#3 返回聚合器的当前值。

#4 设置聚合器的当前值。

#5 重置聚合器的值。

为了理解聚合器的实现，让我们先讨论一下 Giraph 如何使用此接口。`createInitialValue()` 在 Giraph 开始聚合任何值之前被调用。每当顶点想要向聚合器添加一个值时，Giraph 都会调用 `aggregate` 方法。聚合器的实现负责维护适当的数据结构以实现此聚合。例如，如果你正在实现一个求和聚合器，你的实现应维护一个部分和，并向其中添加值。

除了 `Aggregator` 接口之外，Giraph 还提供了一个抽象类，它实现了 `Aggregator` 接口的一部分，涵盖了最常见的聚合器功能。大多数情况下，扩展此抽象类就能满足你的需求。让我们看看 `BasicAggregator` 的详细信息，然后看一个扩展它以实现新聚合器的示例。（参见清单 8-8。）

## 清单 8-8. `BasicAggregator` 抽象类

```
public abstract class BasicAggregator<A extends Writable> implements
    Aggregator<A> {

    private A value;                                #1

    public BasicAggregator() {                      #2
        value = createInitialValue();
    }

    @Override
    public A getAggregatedValue() {                 #3
        return value;
    }

    @Override
    public void setAggregatedValue(A value) {       #4
        this.value = value;
    }

    @Override
    public void reset() {                           #5
        value = createInitialValue();
    }
}
```

#1 聚合器的内部值。它保存当前的部分聚合结果。
#2 默认构造函数将内部值设置为其初始值。
#3 返回聚合器的当前值。
#4 直接设置聚合器的内部值。
#5 将聚合器的值重置为其初始值。

# `BasicAggregator` 与自定义聚合器

`BasicAggregator` 维护的唯一数据结构是聚合器内部的值，其类型与您的聚合体类型相同。这本质上是聚合器当前的局部值。当您扩展 `BasicAggregator` 时，您唯一的职责是通过 `createInitialValue()` 方法定义初始值，并通过 `aggregate()` 方法定义如何将新值聚合到聚合器的当前内部值中。让我们通过一个示例来说明。

您已经见过编写最大值或求和聚合器的逻辑；这里将展示如何编写一个实现布尔 OR 函数的聚合器。您可以使用这样的聚合器来检测图中是否有任何顶点满足某个条件。例如，假设一个社交图中每个顶点都标有用户的年龄。您还假设有时年龄信息可能缺失，在这种情况下您会停止分析并打印一条消息。因此，您真正想知道的是是否存在至少一个顶点缺少年龄信息。布尔 OR 聚合器将以"至少一个"的逻辑实现这一点。换句话说，如果有至少一个顶点向聚合器添加了值 `true`，聚合器的值应为布尔值 `true`，否则为 `false`。清单 8-9 展示了实现。

清单 8-9. 实现布尔 OR 的自定义聚合器

```java
public class BooleanOr extends BasicAggregator<BooleanWritable> {

    @Override
    public BooleanWritable createInitialValue() {       #1
        return new BooleanWritable(false);
    }

    @Override
    public void aggregate(BooleanWritable value) {
        boolean currValue = getAggregatedValue().get();   #2
        boolean newValue = currValue || value.get();      #3
        getAggregatedValue().set(newValue);               #4
    }

}
```

#1 将聚合器的值初始化为 `false`。
#2 获取聚合器的当前值。
#3 在当前聚合器值与聚合值之间执行逻辑 OR 操作。
#4 将操作结果设置为聚合器的新值。

## OR 运算的交换律和结合律

在第 3 章中，您了解到为了让 Giraph 能够高效地执行聚合，运算必须满足交换律和结合律。显而易见，逻辑 OR 运算符 (`||`) 确实同时满足交换律，因为：

`a || b = b || a`

并且满足结合律，因为：

`(a || b) || c = a || (b || c)`

实现相当直接。显然，如果没有顶点向聚合器添加值，结果应为 `false`。因此，`createInitialValue()` 通过使用布尔类型的 `Writable` 实现，将聚合器的值初始化为布尔值 `false`。接着，通过 `aggregate()` 方法，您实际上是在当前聚合器值 (#2) 与新聚合值 (#3) 之间执行逻辑 OR 操作。结果替换了聚合器的值 (#4)。

Giraph 已经为最常见的操作提供了丰富的聚合器实现集合。与往常一样，随着您的应用程序变得越来越定制化和复杂，您越希望超越基本功能并实现自己的功能。该 API 提供了一种简单的方法来实现有用的聚合器，Giraph 能够以可扩展且高效的方式为您计算这些聚合器，而您无需担心底层实现。在下一节中，您将再次遇到聚合器，这次将探讨它们如何帮助协调分布式程序。

## 集中式算法协调

到目前为止，您了解到在 Giraph API 中编程的基本原则是"以顶点为中心"的编程，这要求您从图中顶点的角度思考。相应地，您必须编写一个每个顶点都会执行的 `compute()` 函数。这导致了自然分布式的算法，其中每个顶点不需要知道图中其他顶点的状态，只为自己进行计算。

但在某些情况下，您发现要实现算法，顶点之间需要相互协调。这种协调可能涉及共享某种对整个图通用的信息，或通过执行一些影响整个算法的集中式计算。事实上，您已经在第 5 章中看到了这样的例子，其中您必须通过使用聚合器来计算 Twitter 中最受欢迎的用户。聚合器提供了一种集中式协调和计算的形式，因为所有顶点共同贡献于单个聚合器的值，然后这个值再被提供给所有顶点。这里，我们将讨论 `MasterCompute`，这是另一种协调顶点的方式。

总的来说，除了通过熟悉的 `compute()` 函数在每个顶点上执行的分布式计算之外，您可能还需要执行一些依赖于或影响整个图和计算的操作。例如，考虑一下，应用程序的停止取决于聚合器的值，而不是每个顶点个体。幸运的是，Giraph 提供了 `MasterCompute`，一种为此而设计的机制。您已经见过一些如何使用 `MasterCompute` 的例子，但这里将更详细地解释它，并提供更多关于用它实现什么功能的示例。

`MasterCompute` 本质上提供了一个集中位置，您可以在其中执行操作，例如读取和设置聚合器值、获取关于整个图的统计信息，甚至停止整个作业执行。实际上，您可以将 `MasterCompute` 视为一段应用程序逻辑，它在每个超步结束后只执行一次。（见图 8-4。）

![A978-1-4842-1251-6_8_Fig4_HTML.jpg](img/A978-1-4842-1251-6_8_Fig4_HTML.jpg)

图 8-4. `MasterCompute` 集中式计算点。`compute()` 方法在每个超步之前执行一次。聚合器值从 `MasterCompute` 传递给顶点。

清单 8-10 展示了 `MasterCompute` 抽象类的方法，随后将结合示例进行说明。

清单 8-10. `MasterCompute` 抽象类

```java
public abstract class MasterCompute
    extends DefaultImmutableClassesGiraphConfigurable
    implements MasterAggregatorUsage, Writable {

  public abstract void initialize()        #1
   throws InstantiationException, IllegalAccessException;

  public abstract void compute();          #2

}
```

#1 在算法执行开始前调用。
#2 在每个超步结束后为整个图恰好调用一次。

`MasterCompute` 的 `initialize()` 方法在算法开始执行之前被调用一次；它通常用于设置您的 `MasterCompute` 实现。您稍后将看到，这对于设置聚合器的操作非常有用。`compute()` 方法是 `MasterCompute` 的核心工作方法。Giraph 在每个超步执行之前恰好调用一次此方法；也就是说，在它调用顶点的 `compute()` 方法之前。更具体地说，Giraph 第一次调用 `MasterCompute` 的 `compute()` 方法是在超步 0 执行之前。接下来，让我们看看一些可以在 `MasterCompute` 的 `compute()` 函数中执行的示例。

### 终止计算

决定何时停止执行是分布式图算法的核心方面之一。想一想你在第 4 章中看到的 `PageRank` 算法。`PageRank` 算法在每次迭代中都会改变网页的排名。停止决策可能取决于你目前已执行的迭代次数，或者自上次迭代以来网页排名的变化幅度。无论如何，你都需要一种方法来指示 Giraph 停止计算。

到目前为止，你已经了解到要停止 Giraph 计算，必须满足两个条件：每个顶点都调用了 `voteToHalt()` 方法，并且当前超步中没有发送任何消息。这些条件完全取决于你的顶点计算逻辑。实际上，在第 4 章中，你看到了许多算法示例以及它们如何实现这种停止条件。但你可能需要基于关于图及算法执行的一些聚合信息（而非单个顶点）来做出停止计算的决策。例如，你可能希望这样的决策依赖于某个聚合器的值。以 `PageRank` 为例，如果自上次迭代以来排名的总差异变化不大（即 `PageRank` 已收敛），你可能希望停止计算。这种类型的决策无法由每个顶点单独做出，而必须由某个中心化实体来完成。正如你可能已经猜到的，`MasterCompute` 正是执行此操作的地方。

Giraph 通过 `MasterCompute` API 为你提供了一种额外的方法，可以在中心化控制点终止计算。回想一下，`MasterCompute` 类的 `compute()` 方法是在超步结束之后、下一个超步开始之前调用的。在 `MasterCompute` 的 `compute()` 方法内部，你可以调用 `haltComputation()` 方法来终止执行。请注意，一旦调用了此方法，无论是否仍有顶点发送消息，或者是否并非所有顶点都已调用 `voteToHalt()` 方法（正如你之前所知的那样），执行都会终止。换句话说，从 `MasterCompute` 调用 `haltComputation()` 会优先于通常的终止条件。

让我们看一个如何使用它的示例。决定算法终止的一种典型方式是依据目前已执行的超步数。你在第 4 章中看到的 PageRank 算法就是这样一个例子。在那里，你将停止逻辑放在顶点计算本身内部；而这里你将看到如何从 `MasterCompute` 内部实现这一点。这样做的一个好处是，你可以保持主要计算逻辑更清晰。代码清单 8-11 展示了如何在 `MasterCompute` 实现中执行此操作。

**代码清单 8-11.** 基于超步数的终止

```java
public class MyMasterCompute extends DefaultMasterCompute {

    @Override
    public void compute() {
        if (getSuperstep() == 10) {                         #1
            haltComputation();                              #1
        }
    }

}
```

#1 基于超步编号终止计算。

通常，你可以在 `MasterCompute` 的 `compute()` 方法内实现任意逻辑。此逻辑在超步之间执行。在这个例子中，你简单地检查是否处于超步 10，并通过终止计算来采取相应行动。否则，`compute()` 方法不需要做其他事情。

回想一下，当你在 `MasterCompute` 内部调用 `getSuperstep()` 时，它返回即将执行的超步的编号。在上一个示例中，`compute()` 方法在超步 9 执行完毕之后、超步 10 执行之前调用了 `haltComputation()`。因此，超步 10 将永远不会被执行。这是一个你需要留意的小细节，以避免错误地编写终止条件逻辑。

## 使用聚合器进行协调

你已经了解到聚合器在计算全局统计数据方面的强大作用。除了全局统计，聚合器还有其他实用功能，协调便是其中之一。我们先讨论一个需要协调的具体例子。回想一下你在第 4 章中看到的推荐算法，它用于预测用户与物品之间的推荐关系。这些算法会迭代优化一个机器学习模型，旨在最小化预测误差。预测误差通过所有顶点的聚合误差计算得出，并借助聚合器来完成。这类算法通常在聚合预测误差低于特定阈值时停止执行。将聚合器与 `MasterCompute` 结合使用，便是协调执行停止的一种方式。

更具体地说，你可以将聚合器视为一个所有顶点都能读写数据的全局变量。通过聚合器，你可以将信息从各个顶点收集到一个中心位置（即 `MasterCompute`），然后在算法执行期间从该位置将信息传回所有顶点，让它们根据这些信息做出决策。在这个例子中，你传递的是聚合误差的值给顶点，顶点则根据这个值采取行动。换言之，聚合器是主控节点与顶点通信的途径。具体而言，在顶点 `compute()` 被调用之前，聚合器的值会广播给所有工作节点；而在主控节点 `compute()` 被调用之前，聚合器的值会被收集。清单 8-12 展示了这一示例。

**清单 8-12.** 基于聚合器值终止执行

```java
public class MyMasterCompute extends DefaultMasterCompute {

    @Override
    public void compute() {
        if (((DoubleWritable)getAggregatedValue("error")).get() < 0.001) { #1
            haltComputation();                                             #2
        }
    }
}
```

#1 终止条件基于聚合器预测误差低于阈值。

#2 当此条件成立时，终止执行。

在此示例中，假设顶点使用“error”聚合器来累加总预测误差。每轮超步结束后，聚合误差会提供给 `MasterCompute`，你可以在其 `compute()` 方法中读取该值，以决定是否终止执行。

如前所述，除了读取聚合器的值，在 `MasterCompute` 的 `compute()` 方法内部，你还可以设置聚合器的值，并将其“广播”给图中的每个顶点。该聚合器的值将在下一轮超步执行期间对所有顶点可用。例如，假设在上一个例子中，你希望在每轮超步后计算所有顶点误差的平均值，然后将该平均值反馈给所有顶点。平均误差随后被每个顶点用作反馈，以改进预测。

实现方式之一是先计算所有顶点的预测误差总和。你已了解如何使用“error”聚合器完成这一步。之后，在 `MasterCompute` 中，将聚合器误差除以图中的顶点数，即可轻松计算出平均值，然后将其广播给所有顶点。清单 8-13 展示了这一过程。

**清单 8-13.** 在 `MasterCompute` 中设置聚合器的值

```java
public class MyMasterCompute extends DefaultMasterCompute {

    @Override
    public void compute() {
        double totalError =
            ((DoubleWritable)getAggregatedValue("error")).get();          #1
        double avgError = totalError / (double)getTotalNumVertices();    #2
        setAggregatedValue("avg.error", new DoubleWritable(avgError));   #3
    }
}
```

#1 获取所有顶点的聚合器误差。

#2 通过除以顶点数来计算平均误差。

#3 通过一个新的聚合器将平均误差广播给所有顶点。

在这个 `MasterCompute` 实现中，你首先读取了持有所有顶点预测误差总和的“error”聚合器的值（#1）。之后，利用 `getTotalNumVertices()` 方法，轻松计算出平均误差（#2）。最后，使用 `MasterCompute` 的 `setAggregatedValue()` 方法，将“avg.error”聚合器的值设置为你刚计算出的平均值（#3）。该聚合器的值将在下一轮超步中对所有顶点可用。

总的来说，`MasterCompute` 是协调顶点之间关系的一个非常实用的工具。在这个例子中，它实现了仅使用分布式顶点中心编程接口所无法实现的功能。具体来说，它允许你通过聚合信息来收集顶点间的通信，并使整个图都能获取这些信息。在下一节中，你将了解使用 `MasterCompute` 的另一种独特方式——如何由更简单的模块组合成复杂算法。

# 编写模块化应用

到目前为止，你已经探索了 Giraph API 提供的那些能让你在应用中增加更多功能或编写更高效算法的特性。在本节中，你将了解 Giraph API 的一个特性，该特性有助于你从编程角度编写更好的应用。具体来说，你将看到 Giraph 如何让你将可能很复杂的算法从更简单的逻辑块中组合出来，从而提高代码的可读性和复用性。通过示例，你将学习如何识别那些可能被分解为更简单算法的算法，以及如何使用 Giraph API 来简化其开发过程。

## 将算法结构化为阶段

我们首先讨论属于此类别且能从可组合性中获益的算法类型。这些编程模式出现在许多算法中，且易于识别。不过请注意，这绝非一个详尽的列表。这些仅作为常见案例，帮助您理解可组合的 API，并在出现新场景时能更具创造力地使用它。

在您目前看到的 Giraph 算法中，您可能已经观察到一种常见的编程模式：算法内部的逻辑可能取决于当前正在执行的超步。这是在 Giraph 中将复杂算法分解为更简单算法的基本模式。让我们重新审视一个您在第三章中见过的算法，该算法将有向图转换为无向图。换句话说，该算法确保：如果原始图中存在从顶点 A 到顶点 B 的边，那么输出图中也会包含一条从 B 到 A 的边。许多算法都基于无向图运行，因此这是一个常见的预处理步骤。在第三章中，您看到了该算法的伪代码；在此为您展示实际代码。（参见代码清单 8-14。）

**代码清单 8-14.** 将有向图转换为无向图的算法

# 图算法的实现与代码示例

## 转换图的算法

```java
public class ConvertGraph
    extends BasicComputation<
    IntWritable, NullWritable, NullWritable, IntWritable> {

    @Override
    public void compute(
        Vertex<IntWritable, NullWritable, NullWritable> vertex,
        Iterable<IntWritable> messages) {

        if (getSuperstep() == 0) {
            sendMessageToAllEdges(vertex, vertex.getId());   // #1
            vertex.voteToHalt();
        } else {
            for (IntWritable msg : messages) {              // #2
                IntWritable id = (IntWritable) msg.get();  // #2
                if (vertex.getEdgeValue(id) == null) {      // #3
                    vertex.addEdge(message);                // #3
                }
            }
            vertex.voteToHalt();
        }
    }
}
```
1. 将该顶点的 ID 发送给它的所有邻居。
2. 我收到了一条消息，其中包含一个来自某个指向我的顶点的 ID。
3. 如果不存在指向该目标 ID 的边，则添加它。

在该算法中，在超步 0 期间，每个顶点都向它所指向的所有顶点发送一条包含其自身顶点 ID 的消息。这样一来，在超步 1 期间，如果顶点 B 有一条指向 A 的边，顶点 A 就会收到来自 B 的消息。如果顶点 A 没有指向 B 的边，它就可以添加这条边。图 8-5 展示了这两个步骤。

![A978-1-4842-1251-6_8_Fig5_HTML.jpg](img/A978-1-4842-1251-6_8_Fig5_HTML.jpg)

**图 8-5.** 将有向图转换为无向图的步骤

这是一个非常具有代表性的示例，说明了算法如何从由更简单的模块组合成算法的能力中获益，原因如下。首先，您可以立即观察到它符合前面描述的模式：计算逻辑取决于当前正在执行的超步。具体来说，有两个清晰分离的阶段：一个在超步 0 期间执行，另一个在超步 1 期间执行。

其次，将图转换为无向图通常是一个预处理步骤，相对于应用程序的主要逻辑来说是次要的。这样一来，您必须将这种转换逻辑连同您的主要逻辑一起包含在计算函数中。例如，假设您想在无向图上运行您在第四章中看到的连通分量算法。在这种情况下，在执行算法的主要逻辑之前，您必须通过应用转换逻辑来确保图已被转换为无向图。

## 连通分量算法

为了说明这一点，我们首先来看一下如何实现原始的连通分量算法。第四章用伪代码描述了该算法；代码清单 8-15 展示了实际代码。

**代码清单 8-15.** 连通分量算法

```java
public class ConnectedComponents
    extends BasicComputation<
    IntWritable, NullWritable, NullWritable, IntWritable> {

    @Override
    public void compute(
        Vertex<IntWritable, NullWritable, NullWritable> vertex,
        Iterable<IntWritable> messages) {

        if (getSuperstep() == 0) {
            sendMessageToAllEdges(vertex, vertex.getId());                       // #1
            vertex.voteToHalt();
        } else {
            int maxId = vertex.getValue().get();                                 // #2
            for (IntWritable msg : messages) {                                  // #3
                if (msg.get() > maxId) {                                        // #4
                    maxId = msg.get();                                          // #4
                }                                                               // #4
            }
            if (maxId > vertex.getValue().get()) {                                // #5
                vertex.setValue(maxId);                                           // #5
                sendMessageToAllEdges(vertex, maxId);                              // #5
            }
            vertex.voteToHalt();
        }
    }
}
```
1. 在超步 0 中，每个顶点将自己的 ID 发送给所有邻居。
2. 每个顶点维护迄今为止看到的最大 ID，该 ID 代表该顶点所属分量的 ID。
3. 在后续的每个超步中，顶点会收到其所有邻居的 ID。
4. 顶点在其自身 ID 和其邻居的 ID 中找出最大的 ID。
5. 如果该最大 ID 大于当前存储在顶点值中的 ID，则更新该值，并将新的最大 ID 传播给所有邻居。

现在您已经掌握了连通分量的逻辑，让我们看看如何修改它以执行相同的算法，但在将有向图转换为无向图之后（参见代码清单 8-16）。

**代码清单 8-16.** 在无向图上应用连通分量算法

```
public class ConnectedComponents extends BasicComputation<IntWritable, NullWritable, NullWritable, IntWritable> {
    @Override
    public void compute(Vertex<IntWritable, NullWritable, NullWritable> vertex, Iterable<IntWritable> messages) {
        if (getSuperstep()==0) {
            sendMessageToAllEdges(vertex, vertex.getId()) // #1
            vertex.voteToHalt() // #1
        } else if (getSuperstep()==1) { // #1
            for (IntWritable msg : messages) { // #1
                IntWritable id = (IntWritable)msg.get(); // #1
                if (vertex.getEdgeValue(id))==null) { // #1
                    vertex.addEdge(message) // #1
                } // #1
            } // #1
            vertex.voteToHalt() // #1
        } else if (getSuperstep()==2) { // #2
            sendMessageToAllEdges(vertex, vertex.getId()) // #2
            vertex.voteToHalt() // #2
        } else if (getSuperstep()>2) { // #2
            int maxId = vertex.getValue().get(); // #2
            for (IntWritable msg : messages) { // #2
                if (msg.get()>maxId) { // #2
                    maxId = msg.get(); // #2
                } // #2
            } // #2
            if (maxId>vertex.getValue().get()) { // #2
                vertex.setValue(maxId); // #2
                sendMessageToAllEdges(vertex, maxId) // #2
            } // #2
            vertex.voteToHalt(); // #2
        }
    }
}
```

#1 图转换逻辑在前两个超步中应用。

#2 主要算法逻辑在后续超步中应用。

你可以清楚地看到，该算法混合了两种逻辑：图转换和连通分量算法。同样，它通过超步编号来区分不同阶段：前两个超步用于图转换，之后的超步则执行连通分量的逻辑。

尽管这种组织算法的方式完全正确，但你可能会发现，它会导致代码变得不必要的复杂——因为你的计算逻辑中混入了与核心算法无关的代码。随着算法复杂度的提升，你最终会在同一个计算过程中塞入多个不同的逻辑块。例如，就像这里的图预处理步骤，在某些场景下，主逻辑执行后还需要在后续超步中执行后处理阶段，这进一步增加了应用的复杂度。更糟的是，当你为算法编写单元测试时，即使转换逻辑与主逻辑无关，你也必须设计相应的测试用例来验证它。

第三，将图转换为无向图这一逻辑片段，在不同需要处理无向图的算法中可能会重复出现。事实上，你完全可以将超步 0 和 1 中的代码直接复制到其他算法中。但这并非良好的编程实践，原因如下：首先，这迫使你修改主算法，使其仅从超步 2 开始执行主逻辑；其次，这会增加代码复用和维护的难度。例如，若在转换逻辑中发现缺陷，你必须修复所有使用该逻辑的算法。相反，一旦开始构建算法库，你最好能直接复用这些通用逻辑模块，而不是每次都重新编码。接下来，我们将探讨如何真正解决这些问题。

### 可组合 API

希望到目前为止，你已经意识到从编程角度看，编写算法可能会变得复杂。现在让我们看看 Giraph 如何简化这一过程。从高层来看，Giraph 允许你为不同超步指定不同的计算类，并以简单的方式决定在每个超步执行哪个计算。这样，你不仅能将逻辑分离到不同计算类中，还能将它们组合到单个算法的执行过程中。让我们通过一个示例来了解具体实现方法，随后再解释其带来的所有好处。

具体来说，我们仍以图转换为无向图的场景为例。第一步，将图转换的逻辑拆分为两个独立的计算：第一个计算负责将顶点 ID 发送给所有邻居，这对应原始单体应用中超步 0 的操作；第二个计算负责接收 ID 消息，若对应边不存在则添加边，这对应原始算法中超步 1 的操作。你可以在代码清单 8-17 中看到这两个计算。

**代码清单 8-17.** 将顶点 ID 传播到邻居的计算

```
public class PropagateId extends BasicComputation<IntWritable, NullWritable, NullWritable, IntWritable> {
  @Override
  public void compute(Vertex<IntWritable, NullWritable, NullWritable> vertex, Iterable<IntWritable> messages) {
      sendMessageToAllEdges(vertex, vertex.getId()) // #1
      vertex.voteToHalt()
  }
}
```

#1 将顶点 ID 发送给所有邻居。

在此示例中，假设输入图的顶点 ID 为整数类型，且没有关联的顶点值和边值。因此，这些值被假定为`NullWritable`类型。在第一个计算中，每个顶点简单地将自己的 ID 通过`sendMessageToAllEdges()` API 发送给所有邻居，然后停止。现在来看代码清单 8-18。

**代码清单 8-18.** 添加反向边的计算

```
public class ReverseEdges extends BasicComputation<IntWritable, NullWritable, NullWritable, IntWritable> {
  @Override
  public void compute(Vertex<IntWritable, NullWritable, NullWritable> vertex, Iterable<IntWritable> messages) {
      for (IntWritable msg : messages) {
        IntWritable id = (IntWritable)msg.get();
        if (vertex.getEdgeValue(id))==null) {
          vertex.addEdge(message)
        }
      }
      vertex.voteToHalt()
  }
}
```

至此，你拥有了两个看似互不相关的计算类。这是因为你一直以来都是在单个计算类的框架下思考算法。接下来，我们将改变这种思路，探索如何以非常简单的方式将它们组合在一起。

将不同计算类组合起来，主要就是决定在每个超步中使用哪个计算类——换句话说，就是协调这些计算类的使用。“协调”这个词可能已经暗示你，这需要通过`MasterCompute`来实现。实际上，`MasterCompute`实现中的一项操作就是设置在每个超步要使用的计算。你需要在`MasterCompute`的`compute()`方法中实现选择正确函数的逻辑。回想一下，Giraph 会在每个超步执行前调用`MasterCompute`实现的`compute()`方法。在此方法内部，你可以设置下一个超步使用的计算。代码清单 8-19 展示了如何实现这一操作。

**代码清单 8-19.** 从 MasterCompute 设置计算类

# Giraph 高级特性：集中式协调与代码重用

## MasterCompute 详解

我们来逐步分析这个方法中的操作。首先，你获取 Giraph 即将执行的超步编号。基于此编号，你选择合适的计算逻辑。具体来说，如果 Giraph 即将执行超步 0，你需要执行转换的第一个阶段：让每个顶点向其邻居发送自己的 ID。这就是 `PropagateId` 计算类所实现的逻辑。接着，你使用 `MasterCompute` 的 `setComputation()` API 将此配置告知 Giraph。注意，你传递给该方法的参数是一个 `Class` 类型的对象，它代表了你想要执行的特定计算类。第一个超步执行完毕后，Giraph 会再次调用 `MasterCompute` 的 `compute()` 方法。这时，你需要执行转换的第二个阶段：顶点接收来自第一阶段的消息，并添加相应的边。这对应的是 `ReverseEdges` 计算类。因此，如果即将执行超步 1，你需要再次使用 `setComputation()` 方法，将计算类设置为 `ReverseEdges.class`。

就是这样。借助 `MasterCompute`，你只需简单调用 `setComputation()` 方法，就能轻松协调算法的执行。正如你在第 5 章中已经看到的，要运行这个应用程序，你只需在通过 `-mc` 命令行选项运行 Giraph 作业时指定 `MasterCompute` 的实现即可。剩下的工作都由 Giraph 负责，它会确保每个超步使用正确的计算逻辑。

```java
public class MyMasterCompute extends DefaultMasterCompute {

  @Override
  public final void compute() {
    long superstep = getSuperstep();       // #1

    if (superstep == 0) {                  // #2
        setComputation(PropagateId.class);   // #2
    } else {                               // #3
        setComputation(ReverseEdges.class);  // #3
    }
  }
}
```

1. 获取下一步将要执行的超步编号。
2. 如果即将执行的是超步 0，则将计算逻辑设置为 `PropagateId`。
3. 如果即将执行的是超步 1，则将计算逻辑设置为 `ReverseEdges`。

## 代码分离与重用

但让我们回顾一下，通过将功能分离到不同的模块中，你实现了什么。首先，很明显，你无需在应用程序逻辑中编码超步的选择，从而保持了逻辑的清晰和完整。从代码可读性的角度来看，两个分离的计算类只编码了它们各自意图执行的操作，因此更简单且更易于理解。

除此之外，之前也提到过，能够轻松地在应用程序间重用代码是一件好事。具体来说，现在让我们重新审视一下这个场景：你想在一个无向图上执行连通分量算法。你已经有代码可以将图转换为无向图，也已经有代码实现了连通分量算法。接下来你只需要使用可组合 API，在 `MasterCompute` 中将它们粘合在一起，而无需修改主要逻辑。`MasterCompute` 类的实现如清单 8-20 所示。

### 清单 8-20. 将图转换与连通分量算法结合

```java
public class MasterCompute extends DefaultMasterCompute {

    @Override
    public final void compute() {
        long superstep = getSuperstep();
        if (superstep == 0) {
            setComputation(PropagateId.class);
        } else if (superstep == 1) {
            setComputation(ReverseEdges.class);
        } else {
            setComputation(ConnectedComponents.class);
        }
    }
}
```

你可能已经注意到，你所做的是将每个超步执行逻辑的协调和选择，从主计算逻辑转移到了代码中专为协调而设的部分——即 `MasterCompute`。同样地，结果是你完全不需要修改连通分量算法的逻辑。你只是将三个独立的逻辑片段组合在一起，形成一个新的应用程序。你只需指示 Giraph 在头两个超步中使用图转换逻辑，然后在后续的所有超步中使用你的主计算逻辑。类似地，你可以将头两个计算逻辑与任何其他算法（而非连通分量算法）结合使用，从而简化代码重用，并使构建新应用程序变得容易。

### 注意

在 `MasterCompute` 内部，你可以在每个超步设置计算类；如果你没有显式设置，Giraph 会使用上一次超步设置的值。

## 本章小结

尽管基本的 Giraph API 已经足够灵活，可以让你表达多种多样的算法，但 Giraph 还提供了一些工具，赋予你更强的能力。这些工具使得表达新的编程模式成为可能，例如集中式协调，从而使你的算法更高效，并且从编程角度来看，也可能让你的工作更轻松。

-   图变异 API 允许算法在执行过程中修改图。这通常是许多需要改变图的应用程序的自然需求，但对于那些需要临时创建逻辑顶点的算法来说，它也可能是一个得心应手的工具。
-   Giraph 提供了三种执行变异的方式：(i) 直接对 `Vertex` 对象进行变异，(ii) 变异请求，以及 (iii) 通过消息进行变异。
-   使用变异请求时，可能会发生冲突。使用 `VertexResolver` 来决定你希望如何解决这些冲突。
-   聚合器允许你计算全局统计信息，并且 Giraph 已经提供了丰富的常用操作集合。同时，你可以使用简单的聚合器 API 来实现自定义聚合器。
-   聚合器和 `MasterCompute` 使得表达需要集中式协调的算法成为可能。
-   使用 `MasterCompute compute()` 方法来实现你的集中式协调逻辑。
-   算法可能会很快变得复杂，但它们通常结构化为依赖于所执行超步的多个阶段。使用可组合 API 可以简化编程，实现代码重用，并产生更易于维护的代码。

至此，你已经开始使用 Giraph 的更高级特性。这应该能让你编写更复杂的应用程序。在下一章中，你将继续深入了解 Giraph 的这些高级特性，包括如何控制 Giraph 中的并行化，以编写更高效的应用程序。
