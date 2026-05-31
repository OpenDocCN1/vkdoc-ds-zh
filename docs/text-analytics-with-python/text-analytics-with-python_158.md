# substance based meronyms for tree
substance_meronyms = tree.substance_meronyms()
print('Total Substance Meronyms:', len(substance_meronyms))
print('Substance Meronyms for [tree]:-')
for meronym in substance_meronyms:
print(meronym.name(), '-', meronym.definition())
print()
Total Substance Meronyms: 2
Substance Meronyms for [tree]:-
heartwood.n.01 - the older inactive central wood of a tree or woody plant; usually darker and denser than the surrounding sapwood
sapwood.n.01 - newly formed outer wood lying between the cambium and the heartwood of a tree or woody plant; usually light colored; active in water conduction
```

该输出展示了部分词，包括树的各种构成部分，如“`stump`”和“`trunk`”，以及从树中衍生的各种物质，如“`heartwood`”和“`sapwood`”。



# 语义关系与相似性

在前几节中，我们探讨了与词汇语义关系相关的各种概念。现在，我们将研究如何根据实体间的语义关系来连接相似实体，并衡量它们之间的语义相似度。语义相似性不同于我们在第 6 章中讨论的传统相似性度量方法。我们将使用一些与生物实体相关的示例同义词集（如以下代码片段所示）进行分析。

```
tree = wn.synset('tree.n.01')
lion = wn.synset('lion.n.01')
tiger = wn.synset('tiger.n.02')
cat = wn.synset('cat.n.01')
dog = wn.synset('dog.n.01')
