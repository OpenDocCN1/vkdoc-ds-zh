# Retrieve to Assess
################################################################
AnimalGood1 = AnimalRetrieve.fillna('0', inplace=False)
AnimalGood2=AnimalGood1[AnimalGood1.ItemName!=0]
AnimalGood2[['ItemID','ParentID']]=AnimalGood2[['ItemID','ParentID']].astype(np.int32)
AnimalAssess=AnimalGood2
print(AnimalAssess.shape)
################################################################
sFileName=sFileAssessDir + '/' + InputAssessFileName
print('###########')
print('Storing Assess :',sFileName)
AnimalAssess.to_csv(sFileName, index = False)
################################################################
print('################')
sTable='All_Animals'
print('Storing :',sDatabaseName,' Table:',sTable)
AnimalAssess.to_sql(sTable, conn, if_exists="replace")
print('################')
```

你开始执行流程步骤，将扁平数据处理成图谱。现在，你可以提取节点，如下所示：

```
################################################################
print('################')
sTable='All_Animals'
print('Loading Nodes :',sDatabaseName,' Table:',sTable)
sSQL=" SELECT DISTINCT"
sSQL=sSQL+ " CAST(ItemName AS VARCHAR(200)) AS NodeName,"
sSQL=sSQL+ " CAST(ItemLevel AS INT) AS NodeLevel"
sSQL=sSQL+ " FROM"
sSQL=sSQL+ " " + sTable + ";"
AnimalNodeData=pd.read_sql_query(sSQL, conn)
print(AnimalNodeData.shape)
```

你已成功提取了节点。做得好。现在你可以提取边。你将开始执行流程步骤，将数据转换为合适的图谱结构。

```
################################################################
print('################')
sTable='All_Animals'
print('Loading Edges :',sDatabaseName,' Table:',sTable)
sSQL=" SELECT DISTINCT"
sSQL=sSQL+ " CAST(A1.ItemName AS VARCHAR(200)) AS Node1,"
sSQL=sSQL+ " CAST(A2.ItemName AS VARCHAR(200)) AS Node2"
sSQL=sSQL+ " FROM"
sSQL=sSQL+ " " + sTable + " AS A1"
sSQL=sSQL+ " JOIN"
sSQL=sSQL+ " " + sTable + " AS A2"
sSQL=sSQL+ " ON"
sSQL=sSQL+ " A1.ItemID=A2.ParentID;"
AnimalEdgeData=pd.read_sql_query(sSQL, conn)
print(AnimalEdgeData.shape)
```

你已提取了边。那么，让我们来构建一个图谱。

```
################################################################
G=nx.Graph()
t=0
G.add_node('world', NodeName="World")
################################################################
```

首先，你添加节点。


```python
GraphData=AnimalNodeData
print(GraphData)
################################################################
m=GraphData.shape[0]
for i in range(m):
t+=1
sNode0Name=str(GraphData['NodeName'][i]).strip()
print('Node :',t,' of ',m,sNode0Name)
sNode0=sNode0Name.replace(' ', '-').lower()
G.add_node(sNode0, NodeName=sNode0Name)
if GraphData['NodeLevel'][i] == 0:
G.add_edge(sNode0,'world')
################################################################
```

**第二步，添加边。**

```python
GraphData=AnimalEdgeData
t=0
################################################################
m=GraphData.shape[0]
for i in range(m):
t+=1
sNode0Name=str(GraphData['Node1'][i]).strip()
sNode1Name=str(GraphData['Node2'][i]).strip()
print('Link :',t,' of ',m,sNode0Name,' to ',sNode1Name)
sNode0=sNode0Name.replace(' ', '-').lower()
sNode1=sNode1Name.replace(' ', '-').lower()
G.add_edge(sNode0,sNode1)
```

现在你已经有了节点和边。接下来，将你自己添加为一个节点。

```python
################################################################
RCode=ReaderCode.replace(' ', '-').lower()
G.add_node(RCode,NodeName=ReaderName)
G.add_edge('homo-sapiens',RCode)
################################################################
```

**现在，存储这个图。**

```python
sFileName= sFileAssessDir + '/' + InputAssessGraphName
print('################################')
print('Storing :', sFileName)
print('################################')
nx.write_gml(G,sFileName)
sFileName=sFileName +'.gz'
nx.write_gml(G,sFileName)
```

我们来思考一下我们已经能够提取的一些知识。观察以下对象之间如何相互关联：

- 你自己？
- 作者？
- Angus，我的狗？
- Tigger，我的玩具老虎？
- Chris Hillman，我的技术顾问和朋友？

将以下 Python 文件输入到编辑器中，你将发现新的知识。

```python
################################################################
