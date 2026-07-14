# 7. 元数据

如果你回过头来看第 5 章中的独立 HTML 文档，该文档是这样的：

```
## 这是一个测试文档
以下是文档中的一些文本。
* 这是一个列表
* 包含两个项目
```

我们是像这样编译它的：
```
pandoc --standalone -o output.html input.md
```

你应该会收到如下警告：
```
[WARNING] This document format requires a nonempty
 element.
Please specify either 'title' or 'pagetitle' in
the metadata,
e.g. by using --metadata pagetitle="..." on the
command line.
Falling back to 'input'
```

然后你的文档标题将正如警告所说，如下所示：
```
input
```

`Pandoc` 在你的文档中插入了一个标题，但它被设置为了默认值 `input`，因为我们没有指定它。尝试改为运行以下命令：
```
pandoc --metadata title="My Title" \
--standalone -o output.html input.md
```

如果你现在读取 `output.html` 文件，你会看到 `Pandoc` 在标题标签之间插入了“My Title”，并插入了一个显示为“My Title”的一级标题。

当 `Pandoc` 生成独立文档时，它会使用诸如标题和作者之类的元数据来填充一些信息。这些数据通常不在 Markdown 输入中指定——没有任何 Markdown 注释用于定义这种元数据——但你可以使用 `--metadata` 选项或 YAML（参见下文）来设置它。

严格来说，生成输出时使用的变量有两种：通过 `--metadata` 指定的元数据，以及通过 `--variable` 指定的变量。它们的区别在于，元数据可以被 `Pandoc` 和 `Pandoc` 过滤器（在输入格式化为输出之前处理输入的脚本）看到和处理，而变量则用于模板中。如果你使用 `--metadata` 标签或在元数据头信息中设置了一个变量，该变量也将对模板可用，因此你通常可以坚持使用元数据。输出并非*完全相同*，因为过滤器可能会对元数据进行一些不会对变量进行的操作，但坚持使用一种选项更容易。因此，除非你有充分的理由不这样做，否则请使用元数据。

## 用于元数据的 YAML

你可能想要指定的元数据有很多，因此你不想仅依赖命令行选项来设置所有这些信息。幸运的是，`Pandoc` 可以从输入文件的头部读取元数据，该头部使用另一种名为 YAML（Yet Another Markup Language，另一种标记语言）的标记语言来指定。YAML 是一种与 Markdown 不同的标记语言。它的目的不是对文本进行标记，而是为工具提供结构化数据。

你可以将包含元数据的 YAML 头部放在输入文本的顶部，以便向 `Pandoc` 提供这些信息。我通常会将元数据放在一个单独的文件中，并在运行 `Pandoc` 时将其作为第一个输入文件给出。由于 `Pandoc` 会连接你给出的所有输入文件，因此这等同于将元数据放在文档顶部，但这样做可以让我在生成不同格式的输出时使用不同的头部，并且我可以轻松地用相同的元数据格式化不同的 Markdown 文件。

一个 YAML 头部以单独一行上的三个连字符 `---` 开始，并以另外三个连字符结束。在头部内部，你可以放置键值对信息。键后面跟一个冒号，值跟在冒号后面。我在这本书中使用的头部如下所示：
```
---
title:
"My Markdown and Pandoc book"
author:
- Thomas Mailund
year: 2019
---
```

它设置了三个值：`title`、`author` 以及我撰写本书的年份，这些就是本书所需的所有信息。我其实不需要给标题加引号。我可以直接按原样书写，就像我在作者字段中写自己的名字一样。然而，如果标题（或任何值）包含冒号，则必须将值放在引号中。此处，我用引号来举例说明。

你会注意到，在 `author:` 字段中，我的名字前面有一个连字符。其实我也不必加这个连字符，但加上是为了向你展示一个列表。当希望一个键指向一系列值时，例如文档有多个作者时，你需要在列表的每个元素前使用连字符。这里，我让 `author` 指向一个长度为 1 的列表。结果与我没有将名字放在列表中是一样的，但如果我有合著者，我们就需要这种列表语法。

我有这样一个头部文件，名为 `header.yml`，我可以通过以下命令将这本书编译成 PDF 文件：
```
pandoc -o book.pdf header.yml book.md
```

实际的命令行会更复杂（参见第 5 章的 `Makefile`），但这个命令足以生成一本电子书。

YAML 语言本质上是一种将键映射到值的方法。在上述头部中，你有三个键：`title`、`author` 和 `year`。一个键可以映射到一个单一的标量值。`title` 和 `year` 就是如此。它们也可以映射到一个列表，就像 `author` 那样。键还可以映射到嵌套的键值映射。考虑以下示例：
```
author:
- name: Thomas Mailund
  affiliation: Unseen University
- name: Karsken Baelg
  affiliation: Brakebills University
```

这里的 `author` 是一个列表——你可以从每个作者前的短横线看出。每个作者都是一个嵌套映射；它们有两个键：`name` 和 `affiliation`。说它是一个映射，是因为它们有键，后面跟冒号，然后右边是值。简而言之，标量是键后跟一个单一值，列表是键后跟一系列用连字符分隔的项目，而嵌套映射则是嵌套的键值映射。对于列表和映射，还有一种更简洁的表示法。对于列表，你可以将值放在方括号内，并用逗号分隔：
```
author_names: ["Thomas Mailund", "Karsken Baelg"]
```

对于映射，你可以改用花括号：
```
author:
- { name: "Thomas Mailund",
    affiliation: "Unseen University" }
- { name: "Karsken Baelg",
    affiliation: "Brakebills University" }
```

这里，列表中的项目具有相同的结构。它们不必一定如此；不过，当它们结构相同时，编写代码来处理它们会更容易。

如果你有一段较长的文本，可以在 YAML 文件中使用 `|` 或 `>` 将其分成多行：
```
abstract: |
  This is a very long abstract and
  it is probably the best paper ever.
  We are sure that you will all agree.
```

两者的区别在于，`|` 会保留换行符，而 `>` 会移除换行符并用空格替换。你可以根据需要在这些块中继续书写文本，只要缩进每一行即可。

你可以编写任意复杂的 YAML，但 `Pandoc` 只会使用它知道如何处理的元数据。`Pandoc` 解释哪些变量取决于输出格式和你所使用的模板（参见第 9 章）。有关默认模板的详细信息，请查看 [`https://tinyurl.com/yyxgole5`](https://tinyurl.com/yyxgole5) 上的 `Pandoc` 手册，并参阅第 9 章了解如何在自己的模板中使用元数据。



