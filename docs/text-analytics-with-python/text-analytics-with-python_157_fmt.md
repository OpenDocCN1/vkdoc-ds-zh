# 打印完整的上位词层次结构

打印上位词层次结构：

```python
print('Hypernym Hierarchy')
print(' -> '.join(synset.name() for synset in hypernym_paths[0]))
```

输出：

```
Hypernym Hierarchy
entity.n.01 -> physical_entity.n.01 -> object.n.01 -> whole.n.02 -> living_thing.n.01 -> organism.n.01 -> plant.n.02 -> vascular_plant.n.01 -> woody_plant.n.01 -> tree.n.01
```

从该输出中，你可以看到 `entity` 是 `tree` 所属的最通用概念，并且展示了完整的上下位层级结构，其中显示了每一层级对应的上位词或超类。当你进一步向下浏览时，会得到更具体的概念/实体；如果反向浏览，则会得到更通用的概念/实体。

### 整体词（Holonyms）与部分词（Meronyms）

整体词包含一个特定的关注实体。基本上，它们被定义为表示整体的实体与表示整体特定部分的术语之间的关系。以下代码片段展示了 `tree` 的整体词。

```python
member_holonyms = tree.member_holonyms()
print('Total Member Holonyms:', len(member_holonyms))
print('Member Holonyms for [tree]:-')
for holonym in member_holonyms:
    print(holonym.name(), '-', holonym.definition())
print()
```

输出：

```
Total Member Holonyms: 1
Member Holonyms for [tree]:-
forest.n.01 - the trees and other plants in a large densely wooded area
```

从输出中，我们可以看到 `forest` 是 `tree` 的一个整体词，这在语义上是正确的。这很合理，因为森林是树木的集合。部分词是语义关系，它将一个术语或实体关联为另一个术语或实体的组成部分或构成部分。以下代码片段展示了单词 `tree` 的不同类型的部分词。

```python
part_meronyms = tree.part_meronyms()
print('Total Part Meronyms:', len(part_meronyms))
print('Part Meronyms for [tree]:-')
for meronym in part_meronyms:
    print(meronym.name(), '-', meronym.definition())
print()
```

输出：

```
Total Part Meronyms: 5
Part Meronyms for [tree]:-
burl.n.02 - a large rounded outgrowth on the trunk or branch of a tree
crown.n.07 - the upper branches and leaves of a tree or other plant
limb.n.02 - any of the main branches arising from the trunk or a bough of a tree
stump.n.01 - the base part of a tree that remains standing after the tree has been felled
trunk.n.01 - the main stem of a tree; usually covered with bark; the bole is usually the part that is commercially useful for lumber
```