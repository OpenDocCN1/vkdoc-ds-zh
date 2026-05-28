# 第 7 章 可视化图与网络

要以粗体显示节点标签，请使用以下代码：

```
nx.draw(G, with_labels=True, font_weight='bold')
plt.show()
```

可视化如图 7-4 所示。

***图 7-4.** 带粗体标签的节点*

## 更多类型的图

接下来，我们可以看看更多类型的图及其可视化。

第一种是**线性图或路径图**。图中连接到某个节点的节点数称为该节点的**度**。我们可以说路径图是一种有两个节点度为 1、其余节点度为 2 的图。如果我们在视觉上排列一个路径图，它看起来像一条分段线。要创建路径图，请运行以下代码：

`G = nx.path_graph(4)`

要显示节点和边，请使用以下代码：

```
print('Nodes of the graph G: ')
print(G.nodes())
print('Edges of the graph G: ')
print(G.edges())
```

![](img/index-118_1.jpg)