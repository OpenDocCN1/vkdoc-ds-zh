# 获取 `tokens` 及其词性标注并进行检查

```python
tagged_sent = nltk.pos_tag(nltk.word_tokenize(sentence))
print(tagged_sent)
[('US', 'NNP'), ('unveils', 'JJ'), ('world', 'NN'), ("'s", 'POS'), ('most', 'RBS'), ('powerful', 'JJ'), ('supercomputer', 'NN'), (',', ','), ('beats', 'VBZ'), ('China', 'NNP'), ('.', '.')]
```

```python
# 为示例句子的词元扩展产生式
for word, tag in tagged_sent:
    t = nltk.Tree.fromstring("("+ tag + " " + word  +")")
    for production in t.productions():
        treebank_productions.append(production)

# 重建语法
treebank_grammar = nltk.grammar.induce_pcfg(Nonterminal('S'),
                                            treebank_productions)

# 重建解析器
viterbi_parser = nltk.ViterbiParser(treebank_grammar)

# 获取示例句子的解析树
result = list(viterbi_parser.parse(tokens))[0]

# 打印解析树
print(result)
(S
  (NP-SBJ-2
    (NP (NNP US))
    (NP
      (NP (JJ unveils) (NN world) (POS 's))
      (JJS most)
      (JJ powerful)
      (NN supercomputer)))
  (, ,)
  (VP (VBZ beats) (NP-TTL (NNP China)))
  (. .)) (p=5.08954e-43)

# 可视化解析树
result
```

现在我们已成功为示例新闻标题生成了解析树。你可以在图 3-19 中看到该树的`可视化表示`。请记住，这是一个概率型 PCFG 解析器，你可以在打印解析树时看到输出中提到的该树的整体概率。这里遵循的标签符号均基于先前讨论的`树库`注释。因此，这向我们展示了如何构建自己的基于`成分`的解析器。

## 总结

我们涵盖了许多关于文本处理与整理、句法分析以及理解文本数据的概念、技术和实现。第一章中的许多概念 1 现在应该显得更加相关和清晰，因为我们已经在实际示例中实现了它们。本章涵盖的内容分为两个部分。

我们探讨了与文本处理与整理相关的概念。你现在明白了处理和标准化文本的重要性，随着我们进入后续章节，你将看到为什么拥有良好处理和标准化的文本数据变得越来越重要。我们涵盖了一系列整理技术，包括文本清洗和分词、移除特殊字符、大小写转换以及展开缩写。我们还研究了文本纠正技术，例如拼写纠正。在相同的上下文中，我们还构建了自己的拼写纠正器和缩写展开器。我们找到了一种利用`WordNet`并纠正包含重复字符单词的方法。最后，我们研究了各种词干提取和词形还原技术，并学习了移除无关单词（即`停用词`）的方法。

本章的第二部分专注于分析和理解文本的句法和结构。我们重新审视了第一章 1 中的概念，包括词性标注、浅层解析、依存解析和成分解析。你现在知道了如何在实际文本数据上使用标注器和解析器，以及如何实现自己的标注器和解析器。在后续章节中，我们将更深入地使用各种机器学习技术（包括分类、聚类和摘要）来分析和推导文本洞察。敬请期待！