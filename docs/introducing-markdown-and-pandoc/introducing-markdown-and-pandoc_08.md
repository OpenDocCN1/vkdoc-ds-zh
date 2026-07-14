# 10. 预处理

Markdown 只是一个纯文本文档，你可以在将其传递给 `Pandoc` 之前对文本进行任何重写。在将文本交给 `Pandoc` 之前对其进行的任何重写都称为预处理。`Pandoc` 会从标准输入读取内容，因此我们可以将预处理的结果通过管道传递到命令行中的 `Pandoc`（参见图 10-1）。

假设预处理器将输入文件作为输入，并将其输出写入标准输出，那么管道可以如下所示：

```bash
preprocessor infile.md | \
pandoc --from markdown ... -o outfile
```

你需要告诉 `Pandoc` 它从标准输入接收的是 Markdown 格式，可以通过 `--from` 选项来实现这一点。

只要预处理器输出的文件是 `Pandoc` 能够处理的，它就可以执行你希望的任何操作。输出不必是 Markdown 格式——如果不是，你可以更改 `--from` 选项——但它必须是 `Pandoc` 能够读取的格式。在接下来的内容中，我将使用 Markdown 作为输出格式。

![../images/486315_1_En_10_Chapter/486315_1_En_10_Fig1_HTML.png](img/486315_1_En_10_Fig1_HTML.png)

图 10-1：包含预处理步骤的文档格式化管道

## 示例

在以下示例中，前两个将使用 `GPP`^(¹⁶)，最后一个将使用 `Python`^(¹⁷)。`GPP` 是一个功能相对有限的预处理器，但在包含文件以及选择性地包含或排除文件片段方面，它表现得非常出色。让 `Python` 实现相同的功能则需要更多工作。另一方面，由于 `Python` 是一种通用编程语言，我们可以让它对输入文档执行任何我们想要的操作。

### 包含文件

使用预处理器的一个场景是，将一些可以在多个文件中重复使用的信息放在一些文件中，而将特定于文档的输入（如文档正文）放在另一个文件中。这正是模板的概念，但在其他情况下我们可能也会有类似的设置。

想象一下，你正在教一个班级，并且每周都会布置练习。一些信息，例如班级名称和讲师姓名，每周都不变，但其他信息会变，例如周次。

我们可以创建一个包含通用信息的 `header.yml` 文件：

```yaml
class: Markdown and Pandoc
instructor: Thomas Mailund
```

当然，这里的头部信息是人为简化的。你只想包含一个具有一定复杂性的文件，但示例展示了基本原理。

对于特定的一周，我们可以指定该周的信息，例如周次和该周的实际练习。这是一个文件；我们称之为 `exercises.md`。它包含该班级第 14 周的练习。

```markdown
#include "header.yml"
week: Week 14

#### 这是一个练习
做点难的事情
#### 这是另一个练习
做点更难的事情
```

`#include "header.yml"` 就是预处理器发挥作用的地方。

请注意，界定 YAML 规范的三个短横线*不*在 `header.yml` 中。如果它在里面，那么我们就无法包含它，并且仍然在 `exercises.md` 文件中设置 `week` 变量。当我们将其包含到 YAML 头部时，我们可以将 `header.yml` 中设置的通用变量与特定于文件的变量结合起来。

如果我们通过预处理器传递文档：

```bash
gpp < exercises.md
```

我们会得到以下结果：

```yaml
class: Markdown and Pandoc
instructor: Thomas Mailund
week: Week 14

#### 这是一个练习
做点难的事情
#### 这是另一个练习
做点更难的事情
```

我们可以将其与模板结合使用：

```latex
\documentclass{article}
\usepackage{hyperref}
\title{$class$: $week$}
\author{$instructor$}
\begin{document}
\maketitle
\end{document}
```

现在，将预处理器和 `Pandoc` 结合起来，我们就可以构建一个包含练习的文档：

```bash
gpp exercises.md | \
pandoc --template exercises.tex \
--from markdown \
-o exercises.pdf
```

### 条件包含

继续以练习示例为例，可以设想你为课程配备了助教，并希望向他们提供练习的解答。将解答与练习放在同一个文档中会更方便，但你不想把解答交给学生。因此，你需要一种方法：在为助教生成文档时包含解答，而在其他情况下则排除它们。这正是 GPP 所擅长的。

你可以使用 `#ifdef` 来测试变量是否已定义。请注意，这里的*变量*不应与 `Pandoc` 操作的变量混淆。回忆一下，预处理器在 `Pandoc` 之前处理文档，并且除了将输出传递给 `Pandoc` 之外，不与 `Pandoc` 进行通信。

如果我们想包含或排除一段文本，可以将其放在 `#ifdef` 和 `#endif` 之间。我们可以对练习的解答这样做：

```
#include "header.yml"
week: Week 14

#### 这是一个练习
做些困难的事情
#ifdef SOLUTIONS
这是练习的解答
#endif
#### 这是另一个练习
做更困难的事情
#ifdef SOLUTIONS
这是练习的解答
#endif
```

如果你像上面这样构建文档，则输出中不会包含解答。要获取解答，你需要定义 `SOLUTIONS`。你可以在文件中使用 `#define` 语句来做到这一点，但对于这个特定的应用，我们也可以在命令行中将其传递给 `gpp`。这里我们可以使用 `-D` 选项。下面的命令行将构建一个同时包含练习和解答的 PDF。

```bash
gpp -DSOLUTIONS week14_exercises.md | \
pandoc --template exercises.tex \
--from markdown \
-o week14_exercises_solutions.pdf
```

### 运行代码

离开练习的话题，想象一下你正在写一本关于编程的书，并且里面有代码示例。你想展示运行代码的结果，因此需要评估所有代码并将结果插入到文档中。

例如，你有以下代码：

```python
for i in range(10):
    print(i, end = ' ')
```

```python
for i in range(10):
    print(-i, end = ' ')
```

并且你希望第一个代码块后面跟着数字 0–9，第二个代码块后面跟着数字 0 到 -9。

这段 Python 代码会遍历输入中的所有行。它使用 `sys.stdin` 来读取输入，所以你*必须*通过管道传递输入，而不能直接使用文件名调用。对于每一行，它会检查是否为代码块行，即是否以三个反引号开头。如果是，并且以 `python` 开头，那么它会开始收集行，直到看到代码块的结束标记。到达结束时，它会使用 `exec` 来评估 Python 代码。这个函数会执行代码，产生代码打印的所有输出——这正是我们想要的。由于我们使用了 `exec`，早期代码块中定义的函数和变量可以在后面的代码块中使用。

```python
from sys import stdin

def main():
    exec_env = {}
    incode = False
    codeblock = []
    for line in stdin:
        print(line, end="")
        if line.startswith("```python"):
            incode = True
            continue
        if incode:
            if line.startswith("```"):
                exec("".join(codeblock), exec_env)
                incode = False
                codeblock = []
                continue
            codeblock.append(line)

if __name__ == "__main__":
    main()
```

你可以像这样调用预处理：

```bash
python3 evalpy.py < eval-python.md
```

并得到这个结果：

```python
for i in range(10):
    print(i, end = ' ')

0 1 2 3 4 5 6 7 8 9

for i in range(10):
    print(-i, end = ' ')

0 -1 -2 -3 -4 -5 -6 -7 -8 -9
```

## 练习

如果你已经安装了 `gpp`，请预处理一个文档，使得使用一个标志，在创建 `HTML` 和 `LaTeX` 输出时得到不同的结果。你需要显式设置变量来完成此操作，但如何处理过滤器中的输出格式，请参见下一章。

