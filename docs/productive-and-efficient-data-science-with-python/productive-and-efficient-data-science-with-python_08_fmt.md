# 使用集合来查找唯一元素

集合是 Python 中一种强大的数据结构（[https://realpython.com/python-sets/](https://realpython.com/python-sets/)），当你需要从一个长列表或数组中找出唯一元素时，可以创造性地使用它。考虑以下包含两个函数定义的代码：

```python
import random
random_lst = [random.randint(1,100) for _ in range(100000)]

def unique_for_loop():
    unique_elements = []
    for ele in random_lst:
        if ele not in random_lst:
            unique_elements.append(ele)
    return unique_elements

def unique_set():
    return list(set(random_lst))
```

像往常一样，你使用 Jupyter 的 `%%timeit` 命令进行测试，对于使用 `for` 循环的函数，得到以下结果：

```python
%%timeit -r20 -n5
unique_for_loop()
# 109 ms ± 4 ms per loop (mean ± std. dev. of 20 runs, 5 loops each)
```

使用 `set` 后，你得到以下结果：

```python
%%timeit -r20 -n5
unique_set()
# 788 μs ± 181 μs per loop (mean ± std. dev. of 20 runs, 5 loops each)
```

使用 `set` 的方法要快得多！因此，在任何需要从长数组中查找唯一元素的场景中，使用它都是合理的。

此外，`in` 运算符在被用于集合时被设计得非常快。因此，如果你想检查一个元素是否存在于一个长列表中（即检查该元素是否在列表中存在），并且你有理由认为该列表包含大量重复条目，那么你可以通过首先移除所有重复项并基于该列表创建一个集合，来显著减少搜索时间。一个伪代码看起来像这样：

```python
a_long_list = ...
duplicates_removed = set(a_long_list)
ele = ...
if ele in duplicates_removed:
    print(f"元素 {ele} 存在于列表中")
```

上面展示的方法并不是改变搜索操作算法复杂度的基本原则（如前一节所述）。它是一种技巧，可以在特定情况下利用 Python 的内建数据结构及其优化的方法和运算符来获益。在数据科学任务中（或者，一般来说，在编程中），你应该对这些技巧保持敏锐的洞察力，因为它们随处可见，并且存在于每一种编程语言中。

## 使用专门的数据结构进行计数

在许多情况下，你可能需要从大量文本或数据块中统计变量或元素的频率。一种本能的反应是构建一个字典，将变量作为键，其对应的计数作为整数值。一个简单的实现方法是编写如下函数：

```python
def word_counts(text):
    dict_words = {}
    for w in text.split(' '):
        if w in dict_words.keys():
            dict_words[w] += 1
        else:
            dict_words[w] = 1
    return dict_words
```

在来自熟悉的《双城记》的文本样本上运行它：

```python
text = """It was the best of times, it was the worst of times,
it was the age of wisdom, it was the age of foolishness,
it was the epoch of belief, it was the epoch of incredulity,
it was the season of Light, it was the season of Darkness,
it was the spring of hope, it was the winter of despair,
we had everything before us, we had nothing before us,
we were all going direct to Heaven, we were all going direct the other way – in short, the period was so far like the present period, that some of its noisiest authorities insisted on its being received, for good or for evil, in the superlative degree of comparison only
"""
```

你会得到以下结果：

```python
%%timeit -r1000 -n10
word_counts(text)
# 28.6 μs ± 13.9 μs per loop (mean ± std. dev. of 1000 runs, 10 loops each)
```

对于这种涉及计数的场景，你可以使用 Python 的 `collections` 模块（[https://docs.python.org/3/library/collections.html#collections.Counter](https://docs.python.org/3/library/collections.html#collections.Counter)）中一个名为 `Counter` 的专用数据结构。你会看到，通过使用这个内置数据结构，你可以让代码更紧凑、更有条理，并且运行速度更快。

以下是用单行代码从给定文本创建 `Counter` 对象的方法：

```python
counter_words = Counter(text.split(' '))
```

这个 `counter_words` 对象拥有类似字典的 API，与 `word_counts` 函数返回的 `dict_words` 对象一致。例如，你可以使用 `.items()` 方法轻松打印（每个唯一单词的）计数：

```python
counter_words.items()
# dict_items([('It', 1), ('was', 11), ('the', 14), ('best', 1), ('of', 12), ('times,', 2), ('it', 5), ('worst', 1), ('\nit', 4), ('age', 2), ('wisdom,', 1), ('foolishness,', 1), ('epoch', 2), ...
```

`输出已截断以节省空间`

请注意，`counter_words` 拥有比普通字典 `dict_words` 更多有用的内置方法。例如，最常用的数据科学任务之一（在自然语言处理或 NLP 流程中反复使用）是列出出现频率最高前 5 个（或前 10 个）单词。如果你使用原生的 Python 字典方法，则需要额外编写一小段代码来获取该列表：

```python
dict_words = word_counts(text)
top_5 = sorted([(v,i) for i,v in dict_words.items()], reverse=True)[:5]
```

这将得到包含五个最常出现单词的元组列表：

```python
[(14, 'the'), (12, 'of'), (11, 'was'), (5, 'it'), (4, '\nit')]
```

但你可以使用内置的 `most_common()` 方法获得相同的结果，该方法接受一个参数，即你想提取的前几位单词数量：

```python
counter_words.most_common(5)
# [('the', 14), ('of', 12), ('was', 11), ('it', 5), ('\nit', 4)]
```

这种方法不仅速度更快（建议你使用 Jupyter 魔法命令测量执行速度），而且更简洁、更不易出错，因为你无需自己编写带有单独列表变量（如 `top_5`）的代码。你只需将数字作为参数传递给内置方法，即可返回一个列表。

## 使用 `itertools` 库处理组合结构

假设你正在处理一个机器学习模型，该数据集包含四个数值特征和四个类别特征。你想要构建所有由一个人工数值特征和一个类别特征组成的双特征模型组合，并比较所有这些模型的性能。

一种构建数值特征和类别特征组合列表的朴素方法是使用嵌套的 `for` 循环，如下所示：

```python
lst_features = []
for i in num_features:
    for j in cat_features:
        lst_features.append((i,j))
```

结果列表可能如下所示：

# 数据特征组合示例

```python
[('num_feature-1', 'cat_feature-1'),
 ('num_feature-1', 'cat_feature-2'),
 ('num_feature-1', 'cat_feature-3'),
 ('num_feature-1', 'cat_feature-4'),
 ('num_feature-2', 'cat_feature-1'),
 ('num_feature-2', 'cat_feature-2'),
 ('num_feature-2', 'cat_feature-3'),
 ('num_feature-2', 'cat_feature-4'),
 ('num_feature-3', 'cat_feature-1'),
 ('num_feature-3', 'cat_feature-2'),
 ('num_feature-3', 'cat_feature-3'),
 ('num_feature-3', 'cat_feature-4'),
 ('num_feature-4', 'cat_feature-1'),
 ('num_feature-4', 'cat_feature-2'),
 ('num_feature-4', 'cat_feature-3'),
 ('num_feature-4', 'cat_feature-4')]
```

对于此类组合数据结构，你可以使用 `itertools` 模块（Python 内置库）。你可以通过使用该库中的 `product` 函数获得与上述相同的结果。以下是单行代码：

```python
lst_features = list(product(num_features, cat_features, repeat=1))
```

建议你测量这两种方法的执行时间。`itertools` 函数很可能运行得更快。

此外，你可能希望将数值特征和类别特征混合在一起，构建所有五特征模型的组合。同样，只需一行代码，使用该库中的 `combinations` 函数即可完成整个组合的构建。请注意函数中的参数 `r=5`，表示需要五特征组合：

```python
comb_features = list(combinations(num_features+cat_features, r=5))
```

结果如下所示（输出已截断）：

```
Model 0: num_feature-1, num_feature-2, num_feature-3, num_feature-4, cat_feature-1,
Model 1: num_feature-1, num_feature-2, num_feature-3, num_feature-4, cat_feature-2,
Model 2: num_feature-1, num_feature-2, num_feature-3, num_feature-4, cat_feature-3,
Model 3: num_feature-1, num_feature-2, num_feature-3, num_feature-4, cat_feature-4,
Model 4: num_feature-1, num_feature-2, num_feature-3, cat_feature-1, cat_feature-2,
Model 5: num_feature-1, num_feature-2, num_feature-3, cat_feature-1, cat_feature-3,
```

## 从示例中学到的经验

在上述示例中，你涵盖了重要的计算任务，例如：
* 过滤
* 查找唯一元素
* 统计出现频率和最常见元素
* 构建组合数据结构

在所有这些案例中，我首先展示了一种使用 Python 代码完成任务的朴素方法，然后演示了一种使用 Python 中专用数据结构或内置函数完成相同任务的更快、更简洁的方法。尽管这些示例涵盖了任何数据科学工作流中的许多常见任务，但还有更多情况可以应用此处学到的经验。

那么，核心经验教训是什么？以下是一个简短的列表：
*   始终寻找**最佳的数据结构**来存储和操作你的数据。针对不同情况，不同的数据结构可能是最优的。
*   **不必局限于默认的容器**，例如列表、集合、元组和字典。Python 还有其他模块提供了专用容器和数据结构，在许多情况下都能派上用场并提供更快的性能。
*   **更干净的代码就是高效且富有成效的代码**。一行干净的紧凑代码可能并不比它所替代的五行代码更快，但它能提高整个代码库的可维护性和可读性。从长远来看，这能提升生产力并提高效率。
*   始终注意**测量执行时间**，并尝试各种选项（如上所列），以确定最适合特定情况的方法。没有测量，你无法断言任何事情。