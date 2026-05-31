# Find Lists of Objects
################################################################
TargetNodes=[ReaderCode,'Andre Vermeulen','Angus', 'Tigger', 'Chris Hillman']
for j in range(len(TargetNodes))  :
TargetNodes[j]=TargetNodes[j].replace(' ', '-').lower()
################################################################
for TargetNode in TargetNodes:
if TargetNode in nx.nodes(G):
print('=============================')
print('Path:','World',' to ',G.node[TargetNode]['NodeName'])
print('=============================')
for nodecode in nx.shortest_path(G,source='world',target=TargetNode):
print(G.node[nodecode]['NodeName'])
print('=============================')
else:
print('=============================')
print('No data - ', TargetNode, ' is missing!')
print('=============================')
```

如果你被归类为智人，干得好！

现在，我们来看看 Angus 要变成 Tigger（除了减重 87 公斤并允许他上床之外），需要经过怎样的 DNA 路径？

```python
################################################################
print('=============================')
print(' How do we turn Angus into Tigger?')
print('=============================')
for nodecode in nx.shortest_path(G,source='angus',target='tigger'):
print(G.node[nodecode]['NodeName'])
################################################################
```

我们来看看 Chris 要成为 Dr. Chris 需要经过什么路径。

```python
print('=============================')
print('How do you make Chris a Doctor?')
print('=============================')
for nodecode in nx.shortest_path(G,source='chris-hillman',target='dr-chris'):
print(G.node[nodecode]['NodeName'])
print('=============================')
################################################################
print('### Done!! ############################################')
################################################################
```

现在你已经成功创建了一个新图，你将用它来填充数据保险库中动物对象的部分。在你的 Python 编辑器中，在目录 `..\VKHCG\03-Hillman\03-Process` 下创建一个名为 `Process-Animal-Graph.py` 的新文件。将以下 Python 代码复制到该文件中。首先，我们将加载一些你需要的库，并创建一组基本的文件名和目录，以启用生态系统。

```python
################################################################
