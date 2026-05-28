# 5. 打开数据之门

*读取、解析和处理生物数据文件*

| ![../images/476668_1_En_5_Chapter/476668_1_En_5_Figa_HTML.gif](img/476668_1_En_5_Figa_HTML.gif) | *“救命啊，谁来帮帮我！”爱丽丝喊道。“因为我的研究，我 literally（字面意义上）要被数据淹死了。”* *“不，我的孩子，你没有。”一个神秘的声音说道。* *“什么？你是谁？”爱丽丝朝着声音传来的方向问。* *“我是语法仙女，”那声音说，“我来告诉你，没有人能 literally（字面意义上）被数据淹死。你可以 literally 被水淹死，但只能 figuratively（比喻意义上）被数据淹死。”* *“哦，拜托！”爱丽丝翻了个白眼。* *“抱歉，”语法仙女说，“但像你这样滥用‘literally’这个词的人，会让我 figuratively（比喻意义上）发疯的。”* |
|---|---|
| ![../images/476668_1_En_5_Chapter/476668_1_En_5_Figb_HTML.gif](img/476668_1_En_5_Figb_HTML.gif) | *文件、*`open()`*、*`close()`*、鸭子类型、类型转换、*`float()`*、*`int()`*、循环、*`for`*、异常、*`try`*、*`except`*、字典、*`string strip()`* |
| ![../images/476668_1_En_5_Chapter/476668_1_En_5_Figc_HTML.gif](img/476668_1_En_5_Figc_HTML.gif) | *基因、基因序列、DNA、FASTA 文件* |

到目前为止，你一直在愉快地编写循环、创建变量以及享受各种编程乐趣。但现在，假设你想要开始真正处理大量数据。本章的主题正是如何从外部文件中提取这类数据，因为大部分数据很可能都“居住”在那里。为了感受一下如果不从文件读取数据会是什么样子，我们假设有一个包含表达值的基因列表，你想计算其平均值。那么，你可以像这样创建一堆变量：

```
expr1 = 2.1
expr2 = 0.9
expr3 = 0.1
```

然后计算平均值：

```
mean = (expr1 + expr2 + expr3) / 3
```

但如果需要平均的基因不止几个，这种方法很快就会过时。而且，手动将这些数据复制粘贴到变量中也会变得有点乏味。此外，这意味着我们将基因表达值“硬编码”到了 Python 代码本身。这里所说的硬编码并不意味着编码很困难，而是指这些值被内置到代码中，就像干椰丝被烘焙到拉明顿蛋糕里一样。^(⁶⁰)

不过，尽管拉明顿蛋糕很美味，但如果能从文件读取相同的数据，你的代码会灵活得多。顺便提一下，在某些情况下*确实*需要硬编码，但我们还是另找时间讨论。Python 已经为你考虑到了这一点。

让我们用你最喜欢的文本编辑器，将基因表达数据放入一个简单的文本文件中，并将其命名为 `gene_expression.txt`。在文件中写入以下内容（每个值占一行）：

```
2.1
0.9
0.1
```

## 使用 Python 打开文件

首先需要理解的概念是文件句柄。可以把它想象成烹饪那些美味拉明顿蛋糕酱料的锅的把手。把手只是用来整合那些美味食材的工具，它本身并不是食材。那么，让我们先使用 `open()` 命令为文件 `gene_expression.txt` 创建一个名为 `f` 的文件句柄：

```
f = open("gene_expression.txt", 'r')
```

我们来稍微解释一下。`open()` 命令的两个参数中，第一个很清楚：就是文件名；第二个参数则涉及我们是否要修改文件的*内容*。`'r'` 表示我们希望文件是*只读*的。我们不会更改文件内容，只想要数据：请不要乱动我们的食材！

现在，我们可以使用 `f` 句柄来访问文件中的食材了。像这里的 `gene_expression.txt` 这样的文本文件，基本上可以被当作一个行的“列表”来处理。但首先，我们需要从文件句柄中获取这个“食材列表”，而完成此任务的可靠命令就是——听好了——`readlines()`：

```
lines = f.readlines()
```

现在，变量 `lines` 包含了一个行的列表，列表中的每个元素都以字符串形式包含了该行的内容。现在，我们可以结合循环的知识，来检索列表 5-1 中所有那些美味的文件内容了。

```
f = open("gene_expression.txt", 'r')
lines = f.readlines()
for line in lines:
    print(line)
```

列表 5-1 中的代码打开了文件，获取了各行，然后遍历每一行并简单地将该行的内容打印到屏幕上。如果你运行上述代码，应该会……嗯，等一下。我们忘了清理！重要的是要记住，像任何好厨师一样，我们应该收拾好自己的锅碗瓢盆。这意味着要记得 `close()` 我们的文件句柄！这告诉计算机我们不再需要那个文件的内容了。

好了，现在我们有了列表 5-2 中的代码来解决这个问题。

```
f = open("gene_expression.txt", 'r')
lines = f.readlines()
for line in lines:
    print(line)
f.close()
```

运行这段代码应该会输出以下内容：

```
2.1
0.9
0.1
```

这一切看起来都挺好，但我们怎样才能*实际使用*刚刚读取的数据呢？首先要意识到的是，输出到屏幕的这些数字其实并不是数字！“你说什么？这简直是胡扯！我明明看到它们就是数字，显而易见。”

实际上，它们是伪装的字符串^(⁶¹)，证明这一点的方法之一是，当我们把每一行打印到屏幕时，它包含了所谓的换行符。不，这并不是地铁线路的终点站（比如我们美丽家乡波士顿^(62) 红线上的 Alewife 或 Braintree）。它是一个*不可见字符*，告诉计算机该行的字符已经处理完毕，可以开始下一行了。这就是为什么输出行的每一行之间都有一个空行，而原始文件中却没有。

因此，循环中第一个 `line` 的*实际内容*是 `"2.1\n"`，其中 `\n` 代表前面提到的换行符。目前你不需要了解换行符的细节，但当我们进入像输出这样真正有趣的内容时，我们会再次提到它，所以请记住它。

文件句柄、换行符、不可见字符……这些抽象概念已经够了，我到底怎样才能得到实际数值呢？

## 改变变量类型，乐趣与收益并存：类型转换

现在我们需要稍微偏离一下主题，来谈谈类型转换。不，这不是你在即将上映的好莱坞大片里做的那种选角工作。这里的类型转换指的是将变量从一种 `type`（比如字符串）转换为另一种类型（比如整数）。你可能还记得第 2 章中提到过，Python 使用鸭子类型，也就是说，变量会被赋予一种特定的类型，而无需事先明确告诉计算机会期待这种类型的变量。

那么，类型转换在当前这个场景中能如何帮助我们呢？

在我们的具体案例中，这其实非常简单。我们真正想从字符串 `"2.1\n"` 中得到的是其中包含的浮点数 `2.1`。所以我们只需使用 `float()` 函数将字符串转换为浮点数。你可以自己尝试一下：

```
line = float("2.1\n")
print(line)
```

输出：
```
2.1
```

请注意，`float()` 神奇地提取了浮点数，并删除了那个碍眼的换行符。你现在已经成功地将原始字符串转换为了一个浮点数，它现在被保存在变量 `line` 中。如果你遇到的数据不是浮点数，而是一堆整数，你可以使用一个类似的函数 `int()`；或者，你也可以使用 `str()` 函数将整数或数字转换回字符串；更多详情请参见 [Python 内置函数文档](https://docs.python.org/2/library/functions.html)^(⁶³)。

好了，短暂的偏离主题到此结束。让我们把类型转换整合回我们最初的循环中，请注意，我们现在将新转换后的值*存储*到一个新变量 `level` 中，以便后面使用。在代码清单 5-3 中，是改进后的新循环。

```
f = open("gene_expression.txt", 'r')
lines = f.readlines()
for line in lines:
    level = float(line)
    print(level)
f.close()
```

我们还使用 `print()` 函数将数据输出回屏幕。如果你运行这个程序，你会看到一个简短得多的输出：

```
2.1
0.9
0.1
```

嗒哒！你现在已经成功地从文件中获取了实际值并放入了你的程序！恭喜恭喜。是时候喝杯咖啡了。别担心，我会等你的。

回来了？好的，我们得承认一个小问题：我们还不能*马上*进入用*数据*做有趣事情的部分。是的，我们必须处理一个小问题，那就是输入文件中有时可能存在*错误*。

哎呀，老兄！

是的，亲爱的读者，你要明白，我们说的不是你*代码*中的错误，而是*其他人*可能引入到你输入数据文件中的错误。那些显然远不如你细心，并且可能也不像你这样正直的人。

也许那个人给了你一个看起来像这样的文件：

```
2.1
0.9
3.1xx
0.1
```

然后你运行同样的代码，它会戛然而止，并出现以下错误信息：

```
2.1
0.9
Traceback (most recent call last):
  File "./File_IO.py", line 8, in <module>
    level = float(line)
ValueError: invalid literal for float(): 3.1xx
```

哎哟！

你必须通过错误检测的魔法，尽可能防范这些捣蛋鬼。但是，为了不伤害那些其他人（他们可能碰巧是很好的人、合作者，或者你的实验室导师）的感情，我们将采用更友善、更温和的 Python 说法：[异常处理。](https://docs.python.org/2/tutorial/errors.html)^(⁶⁴)

### 当事情出错时：Python 异常处理

如果你编写的程序所有内容都是硬编码的，并且没有外部输入，那你可能永远不需要担心异常。但那样的程序会非常无聊：每次运行时，它都会做完全相同的事情。而且，如果你不能处理数据，你在生命科学或计算生物学领域肯定做不了任何有趣的事情！即使你使用 Python 建立一些理论模型，你也会希望能够修改参数或读取模型规格。

在生命科学中，就像在生活中一样，事情并非总能按计划进行，这是一个不争的事实。因此，尽早为你的程序建立一些健壮性是值得的。是的，处理这些异常并不是最有趣的事情，有点像吃药，但请相信我们，你以后会感谢我们的。

处理错误的基本格式是，我们首先尝试*做我们原本要做的事情*，这在所有情况下都会执行，除非我们遇到错误，在这种情况下我们就*做其他事情*。这听起来很有道理，对吧？结构如下所示：

```
try:
    做我们原本要做的事情
except 某种错误类型:
    做其他事情
```

让我们回到最初的例子。出问题的那一行代码是在将字符串转换为浮点数时。`float()` 函数不知道拿这个字符串怎么办，因为它显然不是一个数字。之前显示的灰色原始错误信息告诉我们，我们遇到的错误类型是 `ValueError`，所以我们需要添加一个异常来处理这种情况。我们可以选择当异常发生时想做什么，最简单的方法就是忽略它。为此，你可以调用 `pass` 函数，这相当于告诉电脑“继续前进，这里没什么好看的”：

```
try:
    level = float(line)
except ValueError:
    pass
```

但一个好的调试实践是识别出有问题的行，这样你就可以向发送文件给你的人报告他们的错误之处。

```
try:
    level = float(line)
except ValueError:
    print("line is not a number:", line)
```

请注意，你可能会遇到多种不同类型的错误，你可以通过使用多个 `except` 子句来分别处理它们，不过我们暂时保持简单。

现在，完整的程序开始成形了（记得保持缩进正确），如代码清单 5-4 所示。

#### 代码清单 5-4

```
f = open("gene_expression.txt", 'r')
lines = f.readlines()
for line in lines:
    try:
        level = float(line)
    except ValueError:
        print("line is not a number:", line)
    print(level)
f.close()
```

#### 带异常处理的打开文件操作

现在让我们来做一些真正的计算！回到我们最初计算平均值的例子，我们现在可以通过在代码清单 5-5 中跟踪总表达水平值(`sum`)和表达次数(`count`)来实现这一点。

#### 代码清单 5-5

```
f = open("gene_expression.txt", 'r')
lines = f.readlines()
sum = 0
count = 0
for line in lines:
    try:
        level = float(line)
    except ValueError:
        print("line is not a number:", line)
    print(level)
    sum += level
    count += 1
print("average:", sum/float(count))
f.close()
```

#### 即时从文件计算平均值

这将输出以下内容：

```
2.1
0.9
0.1
average: 1.03333333333
```

这样，我们就达到了最初的目标：读取一个文件并计算一组表达水平值的平均值。但是，你现在可能会问，我难道不能在电子表格里做同样的事情吗？为什么我需要所有这些 Python 的东西？是的，没错，但计算平均值只是开始。你会从各种类型的文件中获得各种数据，它们可能有许多不同的格式。Python 将为你提供处理几乎所有情况的灵活性。接下来，我们将基于现有技能，展示如何解析一种最常见的序列数据文件：[FASTA](https://en.wikipedia.org/wiki/FASTA) 文件。

![../images/476668_1_En_5_Chapter/476668_1_En_5_Figd_HTML.jpg](img/476668_1_En_5_Figd_HTML.jpg)

### 重新发明轮子：解析 FASTA 文件

FASTA 文件是生物信息学、基因组学和进化生物学中最常见的序列格式之一。我们首先要说明的是，如果你最终以序列分析为生，你很可能会用到现有的解析器，例如 [Biopython](http://biopython.org/) ^(⁶⁵) 提供的那些，它们既能*读取*也能*写入*多种序列格式。我们强烈建议你在扎实掌握我们在此介绍的基础知识后，去研究这些软件包。例如，[解析 FASTA 文件](http://biopython.org/wiki/SeqIO)^(⁶⁶) 就会像运行代码清单 5-6 中的代码一样简单（`import` 语句将在第 6 章中更详细地描述）。另外请注意，这段代码需要先安装 Biopython 包才能运行（参见第 1 章如何使用 `pip` 安装 Biopython）；但是，我们***不***期望你现在就自己执行这段代码，我们展示它仅仅是为了说明代码的简洁性。

```
from Bio import SeqIO
handle = open("example.fasta", "rU")
for record in SeqIO.parse(handle, "fasta"):
    print(record.id)
handle.close()
```

#### 代码清单 5-6
#### 使用 Biopython 解析文件

那么，假设你想将一个生物体的某些序列读入你的程序，以便最终能搜索其中的各种基序或基因（这是我们将在第 7 章关于 Python 正则表达式的主题中深入探讨的话题）。*首先*需要从文件中读取序列。我们创建了一个测试数据文件 `Sequences.fasta`，位于 `genomes` 子目录中，供你练习。请注意，FASTA 文件的扩展名差异很大；有时可能是 `.fasta`，就像本例这样，有时是 `.fas`，或者 `.fna`：（代表**核**酸的 **F**ASTA 文件指示符）。那么让我们直接开始，看看前几行：

```
>Gene1
AGCTTTTCATTCTGACTGCAACGGGCAATATGTCTCTGTGTGGATTAAAAAAAGAGTGTCTGATAGCAGC
TTCTGAACTGGTTACCTGCCGTGAGTAAATTAAAATTTTATTGACTTAGGTCACTAAATACTTTAACCAA
TATAGGCATAGCGCACAGACAGATAAAAATTACAGAGTACACAACATCCATGAAACGCATTAGCACCACC
ATTACCACCACCATCACCATTACCACAGGTAACGGTGCGGGCTGACGCGTACAGGAAACACAGAAAAAAG
CCCGCACCTGACAGTGCGGGCTTTTTTTTCGACCAAAGGTAACGAGGTAACAACCATGCGAGTGTT
```

如你所见，FASTA 文件中每个序列的基本格式非常简单：

1.  *标识符*：以 ">" 开头，后跟一个唯一字符串（标识符本身）。
2.  *序列*：从标识符的下一行开始。此序列由多个符号（核酸或氨基酸）组成，这些符号可以出现在单行中，也可以跨越多行。

然而，细节是魔鬼，由于 FASTA 格式相当松散（例如，它允许在序列之间有空行），我们的解析器应该对这些微小的格式变化有足够的鲁棒性。显然，一个完整的 FASTA 解析器可能需要处理更多复杂情况，例如捕捉不属于标准核苷酸“字母表”的字母。但在介绍所有异常情况之前，我们将先在代码清单 5-7 中展示我们的解析器。它的目标是读取一个 FASTA 格式的文件，将每个序列保存到一个**字典**中，并打印该字典。

```
f = open("Sequences.fasta", 'r')
lines = f.readlines()
seq_dict = {}
seq_name = None  # 初始状态下，我们尚未找到任何基因
for line in lines:
    if line[0] == '>':
        ### 获取基因名称，用于字典
        seq_name = line[1:].strip()
        seq_dict[seq_name] = ""
    else:
        if seq_name:  # 我们已经找到一个序列！
            ### 将序列追加到字典中
            seq_dict[seq_name] = seq_dict[seq_name] + line.strip()
print(seq_dict)
f.close()
```

代码清单 5-7 使用标准 Python 库解析 FASTA 文件

如你所见，文件打开部分和遍历文件中每一行的 `for` 循环与我们之前的示例相同。我们需要解决的关键挑战是，根据当前的 `line`，判断我们当前处于以下三种不同状态中的哪一种：（`i`）尚未开始任何序列，（`ii`）正在 `开始` 一个新序列，或者（`iii`）正在向一个 `已有` 序列添加内容。我们可以使用当前序列的名称 `seq_name` 来跟踪状态。

在文件开头，尚未找到任何序列，因此在开始 `for` 循环之前，我们将 `seq_name` 设置为 Python 的 `None` 常量。正如我们在前几章中提到的，`None` 最常用于指示值 `缺失`，在这种情况下它很有用，因为我们可以在实际给 `seq_name` 变量赋值 `之前` 引用它。我们还初始化了一个空字典 `seq_dict`。

现在我们准备好进行循环了。我们在循环 `内部` 要做的 `第一件事` 是检查当前行是否包含序列起始字符 `">"`；如果是，那么我们就开始工作了！我们现在可以使用 Python 切片 `line[1:]` 将 `seq_name` 赋值为该行中 `第一个字符之后` 的所有内容。请注意，与上一节关于读取基因表达数据类似，我们需要去掉讨厌的行尾字符。这里我们可以使用内建的 `strip()` 函数，该函数作用于字符串，用于移除所有 `前导和尾随的空格` 以及特殊字符（如行尾字符）。我们还用这个新的 `seq_name` 将字典初始化为空字符串 `""`。这是一个空的容器，我们将开始用序列数据填充它。

如果该行 `没有` 以序列起始字符开头，那么我们需要首先检查当前是否已设置 `seq_name`。这处理了我们尚未找到序列标识符的情况——例如，如果文件的第一行是空行，那么 `seq_name` 将被设置为 `None`，这是一个需要确保代码鲁棒性的示例。如果它 `已经` 设置，那么太好了！——我们准备开始将这些序列添加到 `seq_name` 当前对应的字典条目中。这是通过提取当前序列并使用 `+` 运算符，将当前行经过 `strip()` 处理后的版本追加到字符串末尾来实现的——同样请注意，这巧妙处理了行为空行或仅包含空白字符的情况。最后，我们打印出字典。

为了全面测试这个程序，让我们创建一个测试文件 `Sequences.fasta`，它不仅仅是一个标准的 FASTA 文件，而是特意包含了一些复杂情况：

```
>Gene1
AGCTTTTCATTCTGACTGCAACGGGCAATATGTCTCTGTGTGGATTAAAAAAAGAGTGTCTGATAGCAGC
TTCTGAACTGGTTACCTGCCGTGAGTAAATTAAAATTTTATTGACTTAGGTCACTAAATACTTTAACCAA
TATAGGCATAGCGCACAGACAGATAAAAATTACAGAGTACACAACATCCATGAAACGCATTAGCACCACC
ATTACCACCACCATCACCATTACCACAGGTAACGGTGCGGGCTGACGCGTACAGGAAACACAGAAAAAAG
CCCGCACCTGACAGTGCGGGCTTTTTTTTCGACCAAAGGTAACGAGGTAACAACCATGCGAGTGTT
>Gene2
GGCGCGCGTCTTTGCAGCGATGTCACGCGCCCGTATTTCCGTGGTGCTGATTACGCAATCATCTTCCGAA
TACAGTATCAGTTTCTGCGTTCCGCAAAGCGACTGTGTGCGAGCTGAACGGGCAATGCAGGAAGAGTTCT
AGGTGATGGTATGCGCACCTTACGTGGGATCTCGGCGAAATTCTTTGCCGCGCTGGCCCGCGCCAATATC
AACATTGTCGCCATTGCTCAGGGATCTTCTGAACGCTCAATCTCTGTCGTGGTCAATAACGATGATGCGA
CCACTGGCGTGCGCGTTACTCATCAGATGCTGTTCAATACCGATCAGGTTATCGAAGTGTTTGTGAT
AAAACTGGCGTGCGCGTTACTCATCAGATGCTGTTCAATACCGATCAGGTTATCGAAGTGTTTGTGAT
> Gene 3 (other comment)
TACAGTATCAGTTTCTGCGTTCCGCAAAGCGACTGTGTGCGAGCTGAACGGGCAATGCAGGAAGAGTTCTATTTCCCAATTTTTAGGAACCC
```

请注意，我们引入了两个格式不同的额外序列：`Gene2` 在标识符和序列之间有一个空行，而 `Gene 3` 包含带有额外空格的标识符，并且序列位于单行中（而不是分成多行）。如果我们运行程序 `PFTLS_Chapter_05_02.py`，我们应该会看到序列以字典形式打印出来：

```
{'Gene1':'AGCTTTTCATTCTGACTGCAACGGGCAATATGTCTCTGTGTGGATTAAAAAAAGAGTGTCTGATAGCAGCTTCTGAACTGGTTACCTGCCGTGAGTAAATTAAAATTTTATTGACTTAGGTCACTAAATACTTTAACCAATATAGGCATAGCGCACAGACAGATAAAAATTACAGAGTACACAACATCCATGAAACGCATTAGCACCACCATTACCACCACCATCACCATTACCACAGGTAACGGTGCGGGCTGACGCGTACAGGAAACACAGAAAAAAGCCCGCACCTGACAGTGCGGGCTTTTTTTTCGACCAAAGGTAACGAGGTAACAACCATGCGAGTGTT','Gene2': 'GGCGCGCGTCTTTGCAGCGATGTCACGCGCCCGTATTTCCGTGGTGCTGATTACGCAATCATCTTCCGAATACAGTATCAGTTTCTGCGTTCCGCAAAGCGACTGTGTGCGAGCTGAACGGGCAATGCAGGAAGAGTTCTAGGTGATGGTATGCGCACCTTACGTGGGATCTCGGCGAAATTCTTTGCCGCGCTGGCCCGCGCCAATATCAACATTGTCGCCATTGCTCAGGGATCTTCTGAACGCTCAATCTCTGTCGTGGTCAATAACGATGATGCGACCACTGGCGTGCGCGTTACTCATCAGATGCTGTTCAATACCGATCAGGTTATCGAAGTGTTTGTGATAAAACTGGCGTGCGCGTTACTCATCAGATGCTGTTCAATACCGATCAGGTTATCGAAGTGTTTGTGAT','Gene 3 (other comment)': 'TACAGTATCAGTTTCTGCGTTCCGCAAAGCGACTGTGTGCGAGCTGAACGGGCAATGCAGGAAGAGTTCTATTTCCCAATTTTTAGGAACCC'}
```

请注意，程序像冠军一样处理了每个测试用例，所有标识符都被正确用作字典键，所有序列都由连续的核苷酸标识符组成。

干得好，各位！在下一章中，我们将介绍 Python **正则表达式**，这将极大地提升我们在基因组数据中搜索序列“针”（如本章所示）的能力。