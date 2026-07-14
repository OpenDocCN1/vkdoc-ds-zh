# 11. 过滤器

过滤器让你能够像预处理器一样操作文档。与预处理器不同的是，它们不会在 Pandoc 处理之前修改文本，而是被插入到 Pandoc 所做的文本转换过程中。你可以把它们看作是后处理器——这样说虽不完全准确，但也相差不远，只不过 Pandoc 会在它们完成后继续运行。

预处理和过滤器各有优缺点。它们可以对文件做同样的事情，但有些事情用预处理器更容易实现，而有些事情用过滤器则更简单。

Pandoc 可以从标准输入读取并写入标准输出——这是它的默认行为——并且你可以使用 `--from` 和 `--to` 选项控制输入和输出的格式。与任何管道一样，你可以连接多个程序，因此，不同于使用预处理器操作 Pandoc 的输入，你可以读取 Pandoc 的输出，按需进行转换，经过任意多个步骤，然后将其通过管道传回 Pandoc 进行最终格式化（见图 11-1）。

![../images/486315_1_En_11_Chapter/486315_1_En_11_Fig1_HTML.png](img/486315_1_En_11_Fig1_HTML.png)

**图 11-1** 使用过滤器的文档格式化管道

通过这种方式，你可以将任意数量的程序串联起来，只要一个程序的输出格式与下一个程序的输入格式匹配。但是，Pandoc 所认为的过滤器，以及你可以使用 `--filter` 或 `-F` 选项添加的过滤器，应该读写 JSON 格式。因此，任何能够读写 JSON 的程序都可以用作过滤器。不过，如果可能的话，你肯定不想自己解析 JSON，因此要编写过滤器，你希望使用能够帮助你重写输入的软件包/模块/库。

许多语言都支持过滤器，有些语言甚至有多个支持包；请参阅 `https://tinyurl.com/y2fsn89s` 上的列表。我最熟悉 Python，所以在以下示例中，我将使用该语言。我将使用的包是 `panflute`，它是我编写 Pandoc 过滤器的最爱。要安装 `panflute`，你可以运行：

```
$ pip3 install panflute
```

文档的 JSON 表示可以看作一棵树。一个文档包含段落，段落包含单词和空格，有些单词被强调，等等。Pandoc 过滤器包通常会遍历这棵树结构，并将你提供的函数应用于每个节点。这个函数可以保持局部树结构不变，也可以返回修改后的树。过滤器的输出将是经过你的修改后的输入树。

你可以使用 `pandoc <inputfile> --to json` 来查看文件的 JSON 表示，但我发现使用 `pandoc <inputfile> --native` 更容易看清结构。原生格式是 Pandoc 内部使用的格式，也是你用过滤器遍历的格式。我建议使用它来识别文档的结构，识别需要匹配什么来找到你想要重写的部分，以及重写后的结构应该是什么样子。

以一个像这样的文档为例：

```
## 这是一个一级标题
这是一个段落
```

该文档的 JSON 格式如下：

```
{"blocks":[
{"t":"Header",
"c":[
1,
["this-is-a-level-one-header",[],[]],
[{"t":"Str","c":"This"},
{"t":"Space"},
{"t":"Str","c":"is"},
{"t":"Space"},
{"t":"Str","c":"a"},
{"t":"Space"},
{"t":"Str","c":"level"},
{"t":"Space"},
{"t":"Str","c":"one"},
{"t":"Space"},
{"t":"Str","c":"header"}
]]},
{"t":"Para",
"c":[{"t":"Str","c":"This"},
{"t":"Space"},
{"t":"Str","c":"is"},
{"t":"Space"},
{"t":"Str","c":"a"},
{"t":"Space"},
{"t":"Str","c":"paragraph"}]}
],
"pandoc-api-version":[1,17,5,4],
"meta":{}
}
```

这有点冗长，这就是为什么我更喜欢原生格式。

```json
{
  "formatted": "```\nPandoc (Meta {unMeta = fromList []})\n[Header 1 (\"this-is-a-level-one-header\",[],[])\n[Str \"This\",Space,\nStr \"is\",Space,\nStr \"a\",Space,\nStr \"level\",Space,\nStr \"one\",Space,\nStr \"header\"],\nPara\n[Str \"This\",Space,\nStr \"is\",Space,\nStr \"a\",Space,\nStr \"paragraph\"]\n]\n
```

除了我们编写过滤器时无需关心的 API 版本外，这两种格式包含的信息完全相同（显然，这是因为在过滤器之间传递的是 JSON，而过滤器管道通常以 Pandoc 开头和结尾）。

文档有一个元数据头部（本例中为空），后面跟着一个文档节点列表。这里有两个顶级节点：标题和段落。标题的级别为 1，并附带一组三元组信息。三元组中的第一个元素是它的标识符——当格式化为 HTML 和 LaTeX 时，它用于超链接。三元组中的后两个元素是类和选项。标题内部是一个由字符串和空格组成的列表。这就是文档中的文本。在后续示例中，我们将使用标识符、类和选项。段落中包含一个由字符串和空格组成的列表。

本文档的层级结构并不深。我们看到的由字符串和空格组成的序列嵌套在标题和段落中。它们可以变得更复杂，但通常层级不会太多。请看这个例子：

```
oh: my

This is *very* important
```

如果解析它的结构，我们会得到一个字符串嵌套在强调中，而强调又嵌套在段落中，但深度依然很小。

```
Pandoc
(Meta
{unMeta = fromList
[(\"oh\",MetaInlines [Str \"my\"])]}
)
[Para
[Str \"This\",Space,
Str \"is\",Space,
Emph [Str \"very\"],
Space,
Str \"important\"]
]
```

这里你还看到了一个元数据的例子。它看起来比我们期望从 `oh` 映射到 `my` 的简单映射要复杂，但现实就是如此。在下面的 Python 代码中，`panflute` 模型会将其转换为一个简单的键值映射。

要编写过滤器，可以尝试创建一个小的 Markdown 文件，包含你期望转换的输入以及你希望它变成的输出。然后通过 Pandoc 运行这个例子，查看它的原生结构。据此，再配合用于 Pandoc 过滤器的包，你应该就能得到你想要的结果。

## 探索 `panflute`

在查看具体的过滤器示例之前，让我们先探索一下`panflute`如何让我们遍历文档。作为示例，我将使用这个脚本：

```python
import sys
from panflute import *
def print_structure(elem, doc):
if type(elem) == Header:
    print(\"identifier:\", elem.identifier,
            file = sys.stderr)
    print(\"classes:\", elem.classes,
            file = sys.stderr)
    print(\"attributes:\", elem.attributes,
            file = sys.stderr)
run_filter(print_structure)
```

`run_filter` 会深度优先地遍历整个树结构，并调用我们提供的函数；这里就是 `print_structure`。我写了一个函数，用来展示上一个例子中标题的部分属性。让我为标题再添加一些属性，然后处理这个文档：

```markdown
# This is my header {#header-id
.class1 .class2
foo=bar baz=qux}
This is *very* important
```

我将标题的类分成了多行；Pandoc 并不介意。

在 `print_structure` 函数中，我忽略了所有非 `Header` 类型的文档元素——这只是因为只有当元素是标题元素时我才进行处理。我做的只是打印标题的标识符、类和属性。我将它们打印到标准错误输出——如果打印到标准输出，就会破坏使过滤器正常工作的 JSON 输出。

我打印到标准错误输出的文本如下所示：

```
identifier: header-id
classes: ['class1', 'class2']
attributes: OrderedDict([('foo', 'bar'),
                    ('baz', 'qux')])
```

你可以从标题中识别出这些属性。

```markdown
# This is my header {#header-id
.class1 .class2
foo=bar baz=qux}
```

标识符位于花括号中的第一位，以 `#` 开头。类以点开头，其余部分独立存在，而属性是键值对映射。不同的文档元素有不同的属性，但 `panflute` 的文档很完善，你可以找到每个文档元素所包含的所有属性。

`print_structure` 函数没有显式返回任何值，这意味着它隐式地返回了 `None` 对象。`panflute` 模块会将其解释为该函数不希望进行任何转换，而是保持 `elem` 不变。如果我们想要改变任何内容，就必须返回一个元素来替换输入的 `elem`。

如果你将前面的文档翻译成 HTML，就可以看到各个组件是如何被使用的。标识符变成了标题标签中的 `id`，类变成了类，属性变成了数据属性。

```html
<header id=\"header-id\" class=\"class1 class2\" data-foo=\"bar\" data-baz=\"qux\">
This is my header
</header>

<p>This is very important</p>
```

并非所有组件都会在所有输出格式中使用。例如，在 LaTeX 中，只有标识符被使用了。

```latex
\\hypertarget{header-id}{%
\\section{This is my header}\\label{header-id}}
This is \\emph{very} important
```

类和属性在此输出中用处不大，但这并不意味着它们没用。你只需要自己为它们找到用途。我们可以在自己的过滤器中，为了我们自己的“不法”目的而滥用它们。


## 练习解答的条件性包含

考虑上一章中的示例：我们有包含练习和解答的文本，希望将其编译成两种文档——一种移除了解答，另一种则保留。预处理方案虽然出色，但现在我们可以了解如何通过过滤器实现同样的效果。

假设输入格式如下：

```markdown
Here is an exercise. Everyone can see it.

::: SOLUTION :::

Here is a solution to the exercise.

Do not give it to the students.

:::
```

`::: SOLUTION :::` 语法并不常见，因为它相对罕见。它会在 HTML 中创建一个 `div` 标签（可搭配 CSS 文件进行格式化），并在 LaTeX 中添加超链接目标。我们既不想要标签也不想要目标，但可以在过滤器中使用 `Div` 结构。

运行以下命令即可查看该结构的样式：

```bash
pandoc solutions.md -s --to native
```

这里假设 Markdown 文件名为 `solutions.md`。

```haskell
Pandoc (Meta {unMeta = fromList []})

[Para [

Str "Here",Space,

Str "is",Space,

Str "an",Space,

Str "exercise.",Space,

Str "Everyone",Space,

Str "can",Space,

Str "see",Space,

Str "it."

],

Div ("",["SOLUTION"],[])

[Para [

Str "Here",Space,

Str "is",Space,

Str "a",Space,

Str "solution",Space,

Str "to",Space,

Str "the",Space,

Str "exercise.",Space,

Str "Do",Space,

Str "not",Space,

Str "give",Space,

Str "it",Space,

Str "to",SoftBreak,

Str "the",Space,

Str "students."]

]

]
```

# Pandoc 过滤器与条件内容

## Div 块与 SOLUTION 类

`::: SOLUTION :::` 语法将 `Div` 结构的类设置为 `SOLUTION`（即 `Div` 对象属性中的中间元素）。类始终是列表形式，因此它实际上将类设置为包含单个元素 `SOLUTION` 的列表。若想为 `Div` 对象赋予更多属性，可使用替代语法：

```markdown
::: SOLUTION :::

Solution 1

:::

::: {#solution-2 .SOLUTION .advanced}

Solution 2

:::

::: {#solution-3 .SOLUTION level=difficult }

Solution 3

:::
```

这里的第一个解答与之前语法相同，会将 `Div` 对象的类设置为 `SOLUTION`，其余两个属性保持为空。第二个解答设置了标识符并添加了另一个类。第三个解答恢复为单一类，但向属性中添加了一个键值映射。

如果使用以下过滤器：

```python
import sys
from panflute import *

def print_structure(elem, doc):
    if type(elem) == Div:
        print("id:", elem.identifier,
              file = sys.stderr)
        print("classes:", elem.classes,
              file = sys.stderr)
        print("attributes:", elem.attributes,
              file = sys.stderr)

run_filter(print_structure)
```

处理上述 Markdown 文件时，将得到以下输出：

```
id:
classes: ['SOLUTION']
attributes: OrderedDict()
id: solution-2
classes: ['SOLUTION', 'advanced']
attributes: OrderedDict()
id: solution-3
classes: ['SOLUTION']
attributes: OrderedDict([('level', 'difficult')])
```

## 条件包含解答

我们可以利用这些信息处理示例。我们希望根据元数据包含或排除解答。这在预处理程序中难以实现，但那里可以使用预处理变量，而此处实现起来更复杂。每种方法各有利弊。

上述信息表明，如果有 `::: SOLUTION :::` 块（或替代语法 `::: { .SOLUTION }` 块），则 `Div` 的类中将包含 `'SOLUTION'`。我们需要检查元数据以决定是否包含解答。可以从 `run_filter` 传递给过滤器函数的 `doc` 参数获取元信息。调用 `doc.get_metadata()` 将返回一个表格，从中可获取元变量。我们的目标是：移除所有类为 `SOLUTION` 的 `Div` 对象，除非存在名为 `solutions` 的元变量且其值为真。

以下过滤器可实现此功能：

```python
from panflute import *

def solution(elem, doc):
    if type(elem) == Div:
        if 'SOLUTION' not in elem.classes:
            ## 返回 None 保留当前节点
            return None

        meta = doc.get_metadata()

        if "solutions" not in meta:
            return Null

        if meta["solutions"] != True:
            return Null

        return None

run_filter(solution)
```

首先检查 `'SOLUTION'` 是否在类中。如果不在，则表明不是解答块，返回 `None` 保留原元素（返回 `elem` 效果相同）。否则获取元数据。如果元数据中没有 `solutions` 变量，则必然非真，因此返回 `Null` 作为输出中 `elem` 的替代。注意这里用的是 `Null` 而非 `None`！前者是 Pandoc 中的元素，后者是 Python 中的元素；前者用空块替换 `elem`，有效移除了解答块，后者则保留原 `elem`，即输出中保留了解答块。最后，如果元数据中存在 `solutions` 但值不为真，同样移除解答块。`solutions` 元变量的值可能不止 `True`，但这些值在表达式 `meta["solutions"]` 中也会被视为真，因此我明确检查其是否为 `True`。

如果未通过上述检查，则移除解答块。如果元数据中未设置 `solutions`，或设置为 `True` 以外的任何值，则移除该块。否则返回 `None` 保留该块。

如果不是解答块，或者 `solutions` 未设置或设置为除真值以外的值，则视为解答块——但按预设逻辑，此时应将其移除（注：原文此段逻辑有歧义，实际代码逻辑是：非解答块保留；是解答块且 `solutions` 为真时保留，否则移除）。

回到示例 Markdown：

```markdown
Here is an exercise. Everyone can see it.

::: SOLUTION :::

Here is a solution to the exercise.

Do not give it to the students.

:::
```

可以测试 Pandoc 在元变量 `solutions` 设为假时的反应：

```
pandoc --metadata solutions:false -F solutions.py \
solutions.md --to markdown
```

结果如下：

```
Here is an exercise. Everyone can see it.
```

解答块已在输出中被移除。未设置元变量或设置为其他任何值（真值除外）时效果相同。如果设置为真：

```
pandoc --metadata solutions:true -F solutions.py \
solutions.md -- to markdown
```

解答块会被包含：

```
Here is an exercise. Everyone can see it.

::: {.SOLUTION}

Here is a solution to the exercise.

Do not give it to the students.

:::
```

Pandoc 在输出中使用此语法，但定义 `Div` 块时两种方式均可。

## 基于输出格式的条件包含

有时我们希望根据输出格式输出不同的文本，例如针对 HTML 和 LaTeX 输出不同的内容。

你可以通过文本后跟 `{=format}` 的方式，插入仅在特定输出中才包含的原始文本。根据是需要块级文本还是行内文本，文本分隔符略有不同。块级文本的写法如下：

```{=html}
查看示例

习题 1

习题 2

```

行内文本则使用反引号：

```
查看示例

`习题 1 和

习题 2`{=html}

`\cite{ex:ex1} and \cite{ex:ex2}`{=latex}.
```

无论哪种情况，只有当输出格式匹配时，引用的文本才会被插入。我们在第 6 章已经见过这种语法。

这里包含的文本将 *不* 会经过 Pandoc 处理，因此你不能使用 Pandoc 的特性。这意味着，例如，你不能使用 `文本` 语法，而必须使用 HTML 中的超链接。我们将编写一个过滤器来实现这一功能。

我们将使用两个或更多类。第一个类用于标记这是仅针对某些格式输出的文本，另一个类用于指明我们想要输出该文本的目标格式。

我们已经了解了如何向 `Div` 文本块添加类：

```markdown
:::{.out .html}

此内容仅包含在 HTML 中

:::

:::{.out .latex}

此内容仅包含在 LaTeX 中

:::

:::{.out .html .latex}

此内容同时包含在 HTML 和 LaTeX 中

:::
```

对于行内文本，你需要使用方括号：

```markdown
[仅 HTML]{.out .html} [仅 LaTeX]{.out .latex}
```

对于文本块，我们需要捕获 `Div` 对象；对于行内文本，则需要捕获 `Span` 对象。无论哪种情况，我们都需要检查是否包含 `out` 类。如果没有，则保持对象不变——它可能被其他过滤器用于其他用途。如果包含 `out` 类，我们检查输出格式是否也作为一个类存在。如果不是，则移除该对象；如果格式作为类存在，则将其包含。

过滤器如下所示：

```python
from panflute import *

def format_include(elem, doc):
    if type(elem) == Span:
        if not "out" in elem.classes:
            return elem
        if doc.format not in elem.classes:
            return []
        else:
```

# 编号练习

作为最后一个例子，让我们回到练习示例。这次，我们不关心是否包含或不包含解答，而是希望在输出为 LaTeX 时将练习放入一个 LaTeX 环境中，在其他情况下则对它们进行编号并添加一个标题。

考虑如下输入：

```
::: Exercise :::

第一个练习

:::

::: Exercise :::

第二个练习

:::
```

## 代码求值

现在让我们来看上一章中讨论的另一个示例：在格式化文档时运行 Python 代码并插入结果。

在此版本中，我们将使用类来区分需要求值的代码块和不需要求值的代码块。我们只考虑 Python 代码块——即那些类中包含 `"python"` 的代码块——但为了对其进行求值，我们还需要它们具有 `"eval"` 类。例如，在下面的 Markdown 中，我们将对第二个代码块求值，但不求值第一个代码块。

```
~~~{.python}
for i in range(10):
    print(i, end = ")
~~~

~~~{.python .eval}
for i in range(10):
    print(-i, end = ")
~~~
```

这个过滤器很简单。暂时忽略我们调用的 `run_python`——我会在接下来的文字中列出它，但它如何工作并不重要。关注 `eval_python` 即可。我意识到我的命名不够有创意，但 `run_python_process` 执行一个 Python 进程来求值代码块，而 `eval_python` 则是过滤器。

考虑过滤器 `eval_python`。这里，我们只查看类型为 `CodeBlock` 的元素。当遇到 `CodeBlock` 时，我们获取其类，并检查其中是否同时包含 `"python"` 和 `"eval"`。如果不包含，则不会进入内部的 `if` 语句，因此函数将默认返回 `None`，从而保持 `elem` 不变。如果包含正确的类，则使用 `execute_code` 函数对 Python 代码求值，并将结果插入到 `elem` 之后，然后返回该结果。

```
import sys
from panflute import *

### execute_code 的定义

def eval_python(elem, doc):
    if type(elem) == CodeBlock:
        classes = elem.classes
        code_body = elem.text
        if 'python' in classes and "eval" in classes:
            eval_res = execute_code(code_body)
            return [elem, CodeBlock(eval_res)]

run_filter(eval_python)
```

`execute_code` 函数比我们在预处理程序中使用 `exec` 的方式要稍微复杂一些。在预处理程序中使用 `exec` 并让它打印输出比较简单，而在过滤器中求值代码时，我们不想有任何不必要的输出。

如果 `exec` 向标准输出写入内容，它会破坏 JSON 格式，进而破坏管道的其余部分。

因此，我们需要捕获 `exec` 的输出，然后再次获取它。由于 `exec` 中的代码输出会发送到标准输出，我们需要将其更改到一个可用的文件中，在执行代码时打开该文件，再次关闭它以刷新，然后重新打开并读取结果。这个方法并不优雅，但它能完成任务，你可以像这样做：

```python
PYTHON_IO_FILE = "/tmp/eval-python-io"
real_stdout = sys.stdout
exec_env = {}

def execute_code(code):
    f = open(PYTHON_IO_FILE, "w")
    sys.stdout = f
    exec(code, exec_env)
    sys.stdout.close()
    sys.stdout = real_stdout
    f = open(PYTHON_IO_FILE, "r")
    return f.read()
```

你不需要理解过滤器的这一部分，也能理解过滤器本身的工作原理。

我们可以运行过滤器，在此情况下，结果以 Markdown 形式呈现：

```bash
pandoc -F eval-python.py eval-python.md --to markdown
```

结果如下：

```
\`\`\` {.python}
for i in range(10):
    print(i, end = ")
\`\`\`
\`\`\` {.python .eval}
for i in range(10):
    print(-i, end = ")
\`\`\`
0-1-2-3-4-5-6-7-8-9
```

输出不在“反引号”代码块中，而是缩进的。这只是 Markdown 中表达相同内容的另一种方式。

如果你使用此文档，你会看到我们可以在一个代码块中定义函数，并在另一个代码块中使用它：

```
~~~{.python}
for i in range(10):
    print(i, end = ")
~~~

~~~{.python .eval}
print("defining foo")
def foo():
    for i in range(10):
        print(-i, end = ")
foo()
~~~

~~~{.python .eval}
print("calling foo from different block")
foo()
~~~
```

这是输出结果：

```
\`\`\` {.python}
for i in range(10):
    print(i, end = ")
\`\`\`
\`\`\` {.python .eval}
print("defining foo")
def foo():
    for i in range(10):
        print(-i, end = ")
foo()
\`\`\`
defining foo
0-1-2-3-4-5-6-7-8-9
\`\`\` {.python .eval}
print("calling foo from a different block")
foo()
\`\`\`
calling foo from a different block
0-1-2-3-4-5-6-7-8-9
```

当我们返回要保留的元素时，我们不仅仅是返回 `elem`。我们不一定希望 `Span` 和 `Div` 出现在输出中。相反，我们获取对象的内容。脚本需要将其作为列表返回，因此我们使用 `elem.content.list`。

如果你对以下 Markdown 应用此过滤器：

```
[仅 HTML]{.out .html} [仅 LaTeX]{.out .latex}

:::{.out .html}
此内容仅包含在 HTML 中
:::

:::{.out .latex}
此内容仅包含在 LaTeX 中
:::

:::{.out .html .latex}
此内容同时包含在 HTML 和 LaTeX 中
:::
```

你将得到以下 HTML 输出：

```
仅 HTML

此内容仅包含在 HTML 中

此内容同时包含在 HTML 和 LaTeX 中
```

以及以下 LaTeX 输出：

```
仅 LaTeX

此内容仅包含在 LaTeX 中

此内容同时包含在 HTML 和 LaTeX 中
```

此处没有格式化，因为输入中没有格式化内容。

如果你使用另一种输出格式，例如 Markdown，那么此输入不会产生任何输出；所有文本都仅包含在 HTML 和 LaTeX 中。

```python
def get_elements(elem, doc):
    if type(elem) == Span:
        if "out" in elem.classes:
            return elem.content.list
    if type(elem) == Div:
        if not "out" in elem.classes:
            return elem
        if doc.format not in elem.classes:
            return Null
        else:
            return elem.content.list

run_filter(format_include)
```

我们有两个带有 `Exercise` 类的 `Div` 块，其中包含一些文本。这些就是我们要修改的内容。例如，对于 HTML，我们希望给它们添加一个标题；对于 LaTeX，我们希望将它们放入一个 LaTeX 环境中。

我们可以从 `doc` 对象中获取输出格式。如前所述，过滤器的输入和输出都是 JSON，如果我们只使用 shell 管道，就无法知道最终输出是什么。但是，当我们以过滤器的方式运行脚本时，`Pandoc` 知道最终输出是什么，我们就可以获得该信息。

过滤器的第一个尝试如下所示：

```
1   from panflute import *

3   no_exercise = 1

5   def number_exercises(elem, doc):
6       global no_exercise

7       if type(elem) == Div and \
8           "Exercise" in elem.classes:
10           meta = doc.get_metadata()
12           if doc.format == "latex":
13               exercise_env = "exercises"
14               if "exercise_env" in meta:
15                   exercise_env = meta["exercise_env"]
16               block = [
17                   RawBlock(r"\begin{" + exercise_env + "}",
18                            "latex"),
19                   elem,
20                   RawBlock(r"\end{" + exercise_env + "}",
21                            "latex")
22               ]
23               return block

25           level = 1
26           if "exercise_header_level" in meta:
27               level = int(meta["exercise_header_level"])
29           title = [Str("Exercise"),
30                    Space,
31                    Str(str(no_exercise))]
32           no_exercise += 1
33           return [Header(*title, level = level,
34                   classes = elem.classes), elem]
36   run_filter(number_exercises)
```

我们使用一个全局变量 `no_exercise`（第 3 行）来递增每个练习的标题编号。在过滤器内部，我们首先检查是否有一个带有 `Exercise` 类的 `Div` 块。如果是，我们从 `doc` 元素（第 10 行）获取 `meta` 对象，并使用它来检查其输出格式是 LaTeX 还是 HTML。如果是 LaTeX（第 12 行），我们获取元变量 `exercise_env`（默认值为 `exercises`），并创建一个新的块来替换 `Div` 块。

`RawBlock` 只是原样文本，但仅在输出格式为“latex”时才插入。当然，我们知道这里的输出是 LaTeX，可以省略这个参数，但在其他情况下，当你为所有输出格式输出大致相同的文本，并且不想检查输出格式时，`RawBlock` 会很有用。

对于所有其他格式（第 24 行及以下），我们使用默认级别 1，否则使用元变量 `exercise_header_level`（第 26 和 27 行）。我们创建标题文本（第 29-31 行），递增 `no_exercise` 变量（第 32 行），然后创建 `Header` 元素。它的第一个参数是构成标题的文本对象列表，然后是标题级别，并保留来自 `Div` 块的类。我们将 `elem` 文本放在标题之后。

让我们在 HTML 输出上尝试一下（假设过滤器文件名是 `exercises.py`，输入 Markdown 文件是 `exercises.md`）：

```
pandoc -F exercises.py exercises.md --to html
```

输出如下：

```
练习 1

第一个练习

练习 2

第二个练习
```

如您所见，我们为练习添加了一个标题。

如果我们设置标题级别的元变量，就可以修改标题的级别：

```
pandoc --metadata=exercise_header_level=4 \
-F exercises.py exercises.md --to html
练习 1

第一个练习

练习 2

第二个练习
```

对于 LaTeX，我们得到如下输出：

```
pandoc -F exercises.py exercises.md --to latex

\begin{exercises}

第一个练习

\end{exercises}

\begin{exercises}

第二个练习

\end{exercises}
```

在这里，我们没有创建标题，而是将练习放入 `exercises` 环境中。由于我们没有添加标题，因此标题级别被忽略。

要使此功能生效，您需要在 LaTeX 中定义该环境。如何做到这一点超出了本书的范围，但您可以在 YAML 标题中添加如下咒语：

```
header-includes: |
\newcounter{exercounter}[section]
\newcommand{\theexercise}%
{\thesection.\arabic{exercounter}}
\makeatletter
\newenvironment{exercises}{%
\par\refstepcounter{exercounter}%
\protected@edef\@currentlabel{\theexercise}%
\noindent\textbf{Exercise \theexercise}}{}
\makeatother
```

然后在您的模板中，在 `\begin{document}` 之前添加：

```
$for(header-includes)$
$header-includes$
$endfor$
```

我们可以向 `Div` 块添加引用。

```
::: {#ex1 .Exercise}
第一个练习
:::

::: {#ex1 .Exercise}
第二个练习
:::
```

这些引用会自动保留给 `elem Div` 块。

```
练习 1

第一个练习

练习 2

第二个练习
```

您现在可以使用链接语法 `text` 来创建指向练习的超链接。如果您希望引用出现在标题中而不是 `Div` 块中，可以将第 33 和 34 行替换为：

```
identifier = elem.identifier
if not identifier:
    identifier = ""
header = Header(*title,
    identifier = identifier,
    level = level,
    classes = elem.classes)
elem.identifier = ""
return [header, elem]
```

这会将元素的标识符赋予标题，并将元素的标识符设置为空字符串，这意味着它不会在输出中插入。

对于 LaTeX，我们希望使用 `\ref` 命令；我们确实得到了超引用目标，但这并不是我们想要的。不过，我们需要添加一个 LaTeX 引用命令。为此，我们需要在环境内部添加一个 `\label` 命令。这很简单。只需将第 11 到 22 行替换为：

```
if doc.format == "latex":
    exercise_env = "exercises"
    if "exercise_env" in meta:
        exercise_env = meta["exercise_env"]
    if elem.identifier:
        label = r"\label{" + elem.identifier + "}"
    else:
        label = ""
    block = [
        RawBlock(r"\begin{" + exercise_env + "}" +
                 label,
                 "latex"),
        elem,
        RawBlock(r"\end{" + exercise_env + "}",
                 "latex")
    ]


return block

```
我们获取 `Div` 参数的标识符（我们之前看到过标识符），然后将其插入到 `\begin` 命令之后。如果没有标识符，我们就插入空字符串。

现在，我们在 LaTeX 中有了可以与 `\ref{}` 命令一起使用的标签（并且我们可以根据输出格式有条件地插入这些标签），并且我们可以为其他格式插入链接（依赖于这些标签）。LaTeX 会自动对环境进行编号（如果您使用了前面列出的 LaTeX 魔术来定义环境类型），并自动插入它们的引用编号。对于其他格式，您需要自己在链接中插入编号。

这并不理想。这意味着如果您在文本中插入一个练习，就必须手动更新所有编号。我们需要一个更好的解决方案。我们将使用类似于 `pandoc-crossref` 和 `pandoc-citeproc` 的语法，并且出于与 `pandoc-crossref` 相同的原因，我们需要在 `pandoc-citeproc` 之前运行我们的过滤器。我们不想干扰这两个过滤器处理的引用，因此我们将给它们一个前缀（如 `pandoc-crossref`）。我们的引用将如下所示：

```
[@ex:identifier].
```

然而，在处理引用之前，我们想要收集一个从标识符到练习编号的映射。LaTeX 会为环境和 `\ref{}` 命令处理这个，但对于其他格式，我们必须自己处理。由于我们不能保证在看到练习之前就引用它，因此我们必须遍历整个文档，在再次遍历并修改文档之前创建映射。

遍历文档的最简单方法是使用 `run_filter` 函数，但我们不能多次运行它。还有另一个函数 `run_filters`（注意是复数形式）可以处理这种情况。一个过滤器将提供输入给下一个过滤器；我们不想用收集映射的过滤器修改任何东西，所以我们只需让它返回 `None`（通过不返回其他值来隐式返回）。

用于创建映射的过滤器很简单，如下所示：

```
ex_dict = {}
no_exercise = 1

def collect_numbers(elem, doc):
    global no_exercise
    if type(elem) == Div and \
       "Exercise" in elem.classes:
        if elem.identifier:
            ex_dict[elem.identifier] = no_exercise
            no_exercise += 1
```

当我们在修改文档的过滤器中对练习进行编号时，需要重置 `no_exercise`。当两个脚本一个接一个地被调用时，这并不容易解决，但一个直接的解决方案是使用第二个计数器。如果这样做，那么前面的 `number_exercises` 过滤器将像以前一样工作。它会添加编号，但之前不需要映射，现在也不需要。

相反，我们将编写第三个过滤器来完成这项工作；让我们称之为 `handle_citations`。我将在下文中描述这个函数。

我们可以使用以下方式调用这三个过滤器：

```
run_filters([
    collect_numbers,
    number_exercises,
    handle_citations
])
```

我们必须先运行 `collect_numbers`，再运行 `handle_citations`，但 `number_exercises` 可以放在列表中的任何位置。

如果您获取包含这些 `[@ref]` 引用的文件的 `native` 格式，您将得到一个复杂的文本，但您会看到我们有一个包含 `Citation` 元素的 `Cite` 对象（可能不止一个，但我们这里只处理一个）。我们想要翻译这些元素。我们真正想要处理的是 `Citation`，但如果我们在其上过滤，我们将创建一个对象，该对象会进入包含它的 `Cite` 中，因此我们将处理 `Cite` 对象并从中提取 `Citation` 对象。

一个 `Cite` 对象包含几个属性，包括标识符（`@reference` 文本）、前缀（位于方括号内但引用之前的文本）和后缀（位于文本之后的文本）。这里我们只使用前缀和标识符，并忽略——实际上是移除——后缀。有关包含后缀的内容，请参阅练习部分。

```
def handle_citations(elem, doc):
    if type(elem) == Cite:
        actual_cite = elem.citations[0]
        identifier = actual_cite.id
        if not identifier.startswith("ex:"):
            return elem
        prefix_text = actual_cite.prefix.list
        prefix_text.extend([Space, Str("exercise")])
        if doc.format == "latex":
            return actual_cite.prefix.list + [
                RawInline(r"~\ref{" + identifier + "}",
                          "latex")
            ]
        if identifier in ex_dict:
            ex_num = ex_dict[identifier]
            prefix_text.extend([
                Space, Str(str(ex_num))
            ])
        return [
            Link(*actual_cite.prefix.list,
            url = "#" + identifier)
        ]
```


# 过滤器解析

在过滤器的第一行，我们检查是否有一个 `Cite` 元素。这没什么新奇的。然后我们提取我们真正感兴趣的元素，它是一个 `Citation` 元素。

我们获取标识符，即引用标签（第 4 行），并检查它是否是一个示例标签，即以 `"ex:"` 开头（第 5 行）。如果不是，我们返回该元素；我们不想修改其他引用对象，因为这些对象可能被其他过滤器使用。

现在我们提取 `Citation` 对象的前缀；再次使用 `.list` 获取实际内容。我们在前缀中添加文本 `"exercise"`（第 9 行），这样引用也将包含此文本。我们需要添加一个空格以防止前缀与 `"exercise"` 文本连接。我们还需要在 `"exercise"` 后面添加一个空格，但我在 LaTeX 输出中想要一个不间断空格，所以我想在那使用波浪号而不是空格（见下文）。

我假设引用前面已经有一个空格，也就是说，引用看起来像 `[see @ex:ref]` 而不是 `[see@ex:ref]`；如果不是，我们需要在 `"exercise"` 之前添加一个空格。我们还需要在引用之后添加它。否则，前缀和引用编号将会连接在一起。

我们现在单独处理 LaTeX 输出（第 11 至 15 行）。我们将 `\ref{}` 命令附加到前缀并返回结果。我们将 LaTeX 代码放在一个 `RawInline` 对象中。这与 `RawBlock` 对象类似，但用于行内文本。我们在这里不添加空格，而是添加一个波浪号。

对于其他输出格式（第 17 至 25 行），我们根据映射查找练习编号，假设存在标识符，然后向前缀添加一个空格和该编号。我们在数字前添加一个空格，这样它就不会与 `"exercise"` 连接。最后，我们从引用创建一个链接。

## 练习

### 条件包含

修改过滤器，以便您可以使用元变量来决定应包含哪些难度级别以及应排除哪些难度级别。

### 基于输出的条件处理

在修改输入时，我们移除了 `Span` 和 `Div` 对象。如果这些对象拥有的类不仅仅是输出和格式类，我们可能希望保留它们（但移除 `.out` 和 `.format` 类）。请修改脚本以实现此功能。

### 评估代码

为代码块添加一个类，该类将决定原始代码和代码块是否应保留在输出中，同时您仍然对代码块中的代码进行评估。

### 练习编号

您能否也向输出中添加引用后缀？






