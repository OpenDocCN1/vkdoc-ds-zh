# 显示结果

```python
print('P:', proposition_P)
print('Q:', propositon_Q)
print()
print('表达式结果：-')
print(result_frame)
```

```
P: He is hungry
Q: He will eat a sandwich
Expression Outcomes:-
P      Q (P & Q) (P | Q) (P -> Q) (P  Q)
0  False  False   False   False     True      True
1  False   True   False    True     True     False
2   True  False   False    True    False     False
3   True   True    True    True     True      True
```

## 代码输出与解释

此输出描述了这两个命题的各种真值。当我们用各种逻辑运算符组合它们时，你会发现结果与我们第 1 章中手动评估的结果一致。例如，`P & Q` 表示“他饿了并且他会吃一个三明治”只有在两个独立命题都为真时才为真。我们使用 NLTK 的 `Valuation` 类来创建命题及其各种结果状态的字典。我们使用 `Model` 类来评估每个表达式，其中 `evaluate()` 函数内部调用递归函数 `satisfy()`，该函数有助于根据分配的真值评估每个表达式与命题的结果。

### 一阶逻辑

命题逻辑 (PL) 有几个局限性，例如无法表示事实或复杂的关系与推理。PL 的表达能力也有限，因为对于每个新命题，我们都需要一个独特的符号表示，这使得很难概括事实。而一阶逻辑 (FOL) 凭借其函数、量词、关系、连接词和符号等特性，在这方面表现出色。它为语义信息提供了更丰富、更强大的表示。第 1 章中“语义表示”下的“一阶逻辑”子节详细介绍了 FOL 的工作原理。

在本节中，我们将构建几个 FOL 表示，类似于我们在第 1 章中使用数学表示手动构建的方式。在这里，我们使用类似的语法在代码中构建它们，并利用 NLTK 和某些定理证明器，基于预定义的条件和关系来证明各种表达式的结果，类似于我们对 PL 所做的。

你从本节应该获得的关键收获是了解如何在 Python 中表示 FOL 表示，以及如何基于某个目标和预定义的规则与事件，通过证明来执行一阶逻辑推理。你可以使用多个定理证明器来评估表达式和证明定理。NLTK 包提供了三个证明器，即 `Prover9`、`TableauProver` 和 `ResolutionProver`。第一个是免费的，可以从 `https://www.cs.unm.edu/~mccune/prover9/download/` 下载。你可以将其内容提取到你选择的任何位置。在我们的示例中，我们将同时使用 `ResolutionProver` 和 `Prover9`。以下代码片段有助于设置 FOL 表达式和评估所需的依赖项。

```python
import nltk
import os
```