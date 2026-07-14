# 9. 使用模板

当 `Pandoc` 创建独立文档时，它会使用一个输出模板。模板本质上是一个包含某些占位符变量的文档，元数据和你处理过的 Markdown 文本将被插入其中。模板中使用哪些元数据取决于输出格式；你可以在 Pandoc 手册中获取完整变量列表。^([13]) `Pandoc` 会自动设置一些手册中描述的元数据，但你也可以在文件头中指定其他元数据。

除非你明确指定另一个模板，否则 `Pandoc` 会使用输出格式的默认模板。你可以通过运行 `pandoc -D` 命令让 `Pandoc` 显示它用于特定输出的模板，例如，要查看生成 PDF 文件时（其过程是先生成 LaTeX 文档，然后编译它）会使用什么内容，可以输入 `pandoc -D latex`。你也可以在 [`https://github.com/jgm/pandoc-templates`](https://github.com/jgm/pandoc-templates) 获取默认模板的完整列表及其样式。

如果你不懂 LaTeX，运行 `pandoc -D latex` 得到的模板可能意义不大，那么让我们来看看 `pandoc -D html`。输出内容相当长，我不会在此全部复现，只重点介绍其中几部分。

还记得我们在第 8 章讨论的标题吗？在我们为标题提供元数据之前，它是空的。让我们看看模板中它是什么样子。你会找到这样一行代码：

```
$if(title-prefix)$
$title-prefix$ -
$endif$
$pagetitle$
```

美元符号内的内容指定了占位符以及模板应如何处理这些代码的语法。在 HTML 模板的 `title` 标签内，有两个可插入的元数据变量：`title-prefix` 和 `pagetitle`。只有存在标题前缀时才会插入它；这正是 `$if(title-prefix)$` 代码所检查的内容。严格来说，`pagetitle` 也只有在存在时才会被插入，否则会得到一个空字符串。但因为标题前缀后面应跟一个破折号，所以这里进行了一个显式检查，以确定是否应插入内容。

我们从未为 `title-prefix` 和 `pagetitle` 提供过元数据，因此很难看出它们与我们提供的 `title` 元数据有何关联。我们*可以*显式地提供这两个变量，但 `Pandoc` 会根据我们的标题自动创建它们。它在模板的其他地方直接使用了 `title` 变量，模板中包含：

```
$title$
```

`Pandoc` 和过滤器可以访问元数据并创建新的元数据，这正是它为 `title-prefix` 和 `pagetitle` 所做的。`title` 占位符只是直接插入你在元数据中指定的文本。

我并不知道哪些文档能精确说明每种输出格式下可预期的元数据操作，而且我也不确定你是否应该依赖这些操作，因为它们在不同版本的 `Pandoc` 和过滤器中可能不稳定。然而，如果你不使用派生元数据，而是坚持使用明确定义的元数据，那么数据的使用方式就比较直接了。如果你有一个像 `$title$` 这样的简单占位符，那么你在元数据中指定的字符串就会直接插入到输出文件的相应位置。

正如我们在 `title-prefix` 中看到的，元数据也可以根据其是否被定义来条件性地插入。要仅在元变量 `variable` 被定义时插入 `some text`，模板可以包含这样的结构：

```
$if(variable)$ some text $endif$
```

还有一种 if-else 结构，看起来像这样：

```
$if(variable)$
some text
$else$
some other text
$endif$
```

最后，还有一种循环结构。在 HTML 模板中，你可以找到这样一段文本：

```
$for(author)$
$author$
$endfor$
```

这行代码会遍历元数据中指定的作者，并逐个插入他们。如果 `author` 不是一个列表，它仍然有效；它会被视为一个长度为 1 的列表。但如果我们确实有一个作者列表，我们就会为每个作者生成一个二级标题。

请记住，元数据可以结构化，其值可以包含键值对列表。比如这个例子：

```
author:
- name: Thomas Mailund
  affiliation: Unseen University
- name: Karsken Baelg
  affiliation: Brakebills University
```

在这里，作者不是简单的字符串，而是一个键值结构的列表，每个结构包含姓名和所属机构。在模板内部，这些字段可以使用“点表示法”来访问，因此模板可能包含如下代码：

```
$for(author)$
$if(author.name)$
$author.name$
$if(author.affiliation)$
($author.affiliation$)
$endif$
$else$
$author$
$endif$
$endfor$
```

这段代码遍历作者列表并插入作者的姓名（如果他们有姓名的话，通常应该如此）。如果他们有所属机构，则在姓名后添加所属机构。如果作者列表包含的项目不是由姓名和所属机构构成的结构，那么模板会直接插入该列表项（参见 `$if(author.name)$` 判断条件中的 `$else$` 部分）。

当然，输出中最重要的部分来自输入文本中已处理的 Markdown。在模板中，它被插入到不起眼的 `$body$` 占位符处。它看起来不起眼，但当你所有的 Markdown 被处理成输出格式后，都会被插入到这里。

## 编写你自己的模板

模板是另一个在需要时非常好用、不需要时也无需担心的特性。你可以完全不用关心模板就能使用 `Pandoc`，但如果你需要以特定格式排版文档，也无需为编写文本而放弃 `Pandoc`；你可以创建一个模板来处理格式。例如，如果你像我一样是学术界人士，需要为不同的期刊使用不同的格式，你可以为这些期刊制作模板。期刊通常会提供论文的 LaTeX 模板，你可以选取其中一个模板，放入 `Pandoc` 的占位符，瞬间你就拥有了一个 `Pandoc` 模板，然后就可以用 Markdown 撰写论文，同时其格式仍符合期刊标准。

你可以从 `Pandoc` 的用户贡献模板中获得编写自己模板的灵感。^([14]) 我发现创建新模板最简单的方法是拿一个 `Pandoc` 现有的模板进行修改，或者拿一个现有的 HTML 或 LaTeX 文件，放入元数据和 `$body$` 占位符。

默认模板之所以冗长复杂，是因为它们需要设置很多内容，例如配置数学公式或源代码高亮。如果你需要 `Pandoc` 提供的所有功能，我建议你复制一个现有模板并进行修改。对于本章，我将编写更简单的模板，目标是清晰明了而非功能完备。相应的 Markdown 输入也会很简单。如果你需要更多功能，可以在默认模板中找到所需的代码，通常不难找到。

### 模板示例

请阅读以下文本。其中大部分是元数据，因为这是我们在这里关注的重点。如你所见，YAML 头部有三个变量：`title`、`subtitle` 和 `author`。前两个变量各持有一个单一值，最后一个变量则是一个简单值的列表。

```
title:
一本糟糕的小说
subtitle:
说真的，最差劲的作品之一！
author:
- 托马斯·马伦德
- 卡斯肯·贝尔格

那是一个漆黑而风暴肆虐的夜晚
```

如果我们编写下面的 HTML 模板，我们会在 HTML 头部添加标题和副标题，并在主文档中添加一级标题。如果元数据中有任何作者信息，我们会插入一个 `div` 元素来右对齐作者列表，并通过遍历它们来获取该列表。`$sep$` 变量本身并非元变量，而是一种标记后续元素的方式。如果你使用 `$sep$`，那么该标记（例如单词或逗号）会被放置在你遍历的所有元素之间，但不会跟在最后一个元素后面。试着移除它，你就会明白了。

```
$title$$if(subtitle)$: $subtitle$$endif$

$title$$if(subtitle)$: $subtitle$$endif$

$if(author)$

作者
$for(author)$$author$$sep$ 和 $endfor$

$endif$
$body$
```

如果我们用此模板格式化文本，会得到如下 HTML：

```
一本糟糕的小说: 说真的，最差劲的作品之一！

一本糟糕的小说: 说真的，最差劲的作品之一！

作者
托马斯·马伦德 和 卡斯肯·贝尔格

那是一个漆黑而风暴肆虐的夜晚
```

对于 PDF/LaTeX 输出，该模板的功能与上述 HTML 模板相同。

```
\documentclass{book}
\usepackage{hyperref}
\title{$title$$if(subtitle)$: $subtitle$ $endif$}
\author{$for(author)$$author$$sep$和 $endfor$}
\begin{document}
\maketitle
\end{document}
```

（包含 `hyperref` 对于本例而言并不相关，但它是 Pandoc 期望包含的软件包之一。在我撰写本文时，对于这个特定示例，它是你唯一需要的软件包。）

将该模板应用于我们的文档会得到如下结果：

```
\documentclass{book}
\usepackage{hyperref}
\title{一本糟糕的小说: 说真的，最差劲的作品之一！ }
\author{托马斯·马伦德 和 卡斯肯·贝尔格}
\begin{document}
\maketitle
\end{document}
```

你可以通过“点号”语法访问元数据中的嵌套信息。因此，如果你的数据如下：

```
title:
一本糟糕的小说
subtitle:
说真的，最差劲的作品之一！
author:
- name: "托马斯·马伦德"
affiliation: "看不见的大学"
- name: "卡斯肯·贝尔格"
affiliation: "布雷克比尔斯大学"
```

并且你的模板如下：

```
\documentclass{book}
\usepackage{hyperref}
\title{$title$$if(subtitle)$: $subtitle$ $endif$}
\author{$for(author)$$author.name$
来自 $author.affiliation$
$sep$ 和
$endfor$}
\begin{document}
\maketitle
$body$
\end{document}
```

那么处理文档后将生成以下输出：

```
\documentclass{book}
\usepackage{hyperref}
\title{一本糟糕的小说: 说真的，最差劲的作品之一！ }
\author{托马斯·马伦德 来自 看不见的大学
和 卡斯肯·贝尔格 来自 布雷克比尔斯大学}
\begin{document}
\maketitle
那是一个漆黑而风暴肆虐的夜晚
\end{document}
```

你需要使用 `$author.name$` 和 `$author.affiliation$` 来获取每位作者的姓名和所属机构。你不能简单地再使用 `$author$`。你仍然可以检查某个值是否已设置；只需通过点号语法进入即可。例如，如果某些作者没有所属机构，你可以对其进行测试，并只在存在时才插入它。

```
\documentclass{book}
\usepackage{hyperref}
\title{$title$$if(subtitle)$: $subtitle$ $endif$}
\author{
$for(author)$ $author.name$
$if(author.affiliation)$
来自 $author.affiliation$
$endif$
$sep$ 和 $endfor$
}
\begin{document}
\maketitle
$body$
\end{document}
```

如果你有一个像上面那样的 LaTeX 模板（或任何其他输出格式的模板），你希望它能适用于尽可能多的文档。你不希望每写几个文档就要更新它。不过，每个文档通常还是需要一些调整。例如，在 LaTeX 中，你可能需要导入一个特殊的软件包，或者你想定义一些命令。你不想把这些修改放在实际的模板里，也不想到处保留模板的许多副本。然而，使用元变量来使模板具有适应性很容易。

考虑到我刚才描述的 LaTeX 文件情况，我们可以像这样在模板中添加软件包和定义^((15))：

```
\documentclass{book}
\usepackage{hyperref}
$for(packages)$
\usepackage{$packages$}
$endfor$
$if(definitions)$
$definitions$
$endif$
\title{$title$$if(subtitle)$: $subtitle$ $endif$}
\author{
$for(author)$
$author.name$
$if(author.affiliation)$
来自 $author.affiliation$
$endif$
$sep$ 和
$endfor$
}
\begin{document}
\maketitle
$body$
\end{document}
```

我们遍历 `packages` 列表，将每一个包插入到 `\usepackage` 命令中。我们在此直接插入定义。使用以下输入：

```
title:
一本糟糕的小说
subtitle:
说真的，最差劲的作品之一！
author:
- name: "托马斯·马伦德"
affiliation: "看不见的大学"
- name: "卡斯肯·贝尔格"
affiliation: "布雷克比尔斯大学"
packages:
- amssymb
- amsmath
- booktabs
- xspace
definitions: |
\newcommand{\TMRCA}%
{\ensuremath{T_\textrm{MRCA}}\xspace}
\newcommand{\tAC}%
{\ensuremath{\tau_{AC}}\xspace}
\newcommand{\tBC}%
{\ensuremath{\tau_{BC}}\xspace}
\newcommand{\tABC}%
{\ensuremath{\tau_{ABC}}\xspace}
\newcommand{\tadmix}%
{\ensuremath{\tau_\text{admix}}\xspace}

那是一个漆黑而风暴肆虐的夜晚
```

我们得到：

```
\documentclass{book}
\usepackage{hyperref}
\usepackage{amssymb}
\usepackage{amsmath}
\usepackage{booktabs}
\usepackage{xspace}
\newcommand{\TMRCA}%
{\ensuremath{T_\textrm{MRCA}}\xspace}
\newcommand{\tAC}%
{\ensuremath{\tau_{AC}}\xspace}
\newcommand{\tBC}%
{\ensuremath{\tau_{BC}}\xspace}
\newcommand{\tABC}%
{\ensuremath{\tau_{ABC}}\xspace}
\newcommand{\tadmix}%
{\ensuremath{\tau_\text{admix}}\xspace}
\title{一本糟糕的小说: 说真的，最差劲的作品之一！ }
\author{
托马斯·马伦德
来自 看不见的大学
和
卡斯肯·贝尔格
来自 布雷克比尔斯大学
}
\begin{document}
\maketitle
那是一个漆黑而风暴肆虐的夜晚
\end{document}
```

如果你不想在 HTML 输出中出现这些特定于 LaTeX 的代码，那么只需不要在模板中包含它们即可。如果你想在 HTML 和 PDF 文档中使用独立的头部配置，你可以使用两个不同的元变量。对于 LaTeX 宏（与包含软件包不同），你不需要修改模板。Pandoc 理解宏的定义，并会在生成输出时应用你调用的宏。如果你在数学环境内调用宏，Pandoc 会以输出所需的数学格式生成调用宏的结果。在数学环境之外，调用宏的结果会包含在 LaTeX 和 Markdown 输出中，但在其他格式中会被忽略。

如果你感兴趣的话，我用来格式化本书的模板在几本书的撰写过程中有所演变，但目前看起来如下所示。我没有列出 Pandoc 在其模板中的所有代码，而是在注释中指明了你可以在何处插入它。



```latex
\documentclass[11pt, openright,
twoside, onecolumn, final]{memoir}
%% 设置纸张大小
\setstocksize{9in}{6in}
\settrimmedsize{\stockheight}{\stockwidth}{∗}
\usepackage{canoniclayout}
\nonzeroparskip
\setlength{\parindent}{0pt}
%% 设置字体
\usepackage[T1]{fontenc}
\usepackage{baskervillef}
\usepackage[scale=.95,type1]{cabin}
\usepackage[baskerville,vvarbb]{newtxmath}
\usepackage[cal=boondoxo]{mathalfa}
%% 禁用超文本（在输出中很烦人）
\usepackage{nohyperref}
\usepackage{url}
%% 设置章节标题
\renewcommand*\rmdefault{dayrom}
\chapterstyle{madsen}
%%%%%%%%%%%%
%% 来自默认模板的一长串命令
%%%%%%%%%%%%
\begin{document}
\frontmatter
%% 标题页
\begingroup
\thispagestyle{empty}
{\bfseries\sffamily\noindent
$if(series)$ {\large $series$}\\[50pt]$endif$
% 书名
{\huge $title$}\\[35pt]
% 作者
{\Large $for(author)$$author$$sep$\\$endfor$}
\vfill
\endgroup
%% 版权页
\newpage
~\vfill
\thispagestyle{empty}
% 书名
{\Large $title$}\\[15pt]
% 作者
\noindent 版权所有
$year$
$for(author)$$author$$sep$, $endfor$\\
\clearpage
%% 目录
$if(toc)$
\setcounter{tocdepth}{1}
\pagestyle{empty}
\tableofcontents
\cleardoublepage
$endif$
%% 文档正文
\mainmatter
\counterwithout{figure}{chapter}
\pagestyle{plain}
$body$
\end{document}
```

我为这本书设置的元变量如下：

```yaml
title: "The Beginner's Guide to Markdown and Pandoc"
author:
- Thomas Mailund
year: 2019
```

这里把 `author` 设为列表没有什么好的理由。我只合著过一本书，从那以后它就一直以列表形式存在，但对于一位作者来说，它本可以是一个标量值。

我承认，创建模板比仅仅编写 Markdown 文档稍微多一些工作，但我认为制作模板并不比直接用 `LaTeX` 或 `HTML` 编写文档难多少。而且，你可以将所有复杂的格式放在一个文档中，而将注意力集中在另一个文档的内容上。如果你多次重复使用同一个模板，那么创建它所花费的时间就会分摊到多个写作项目中，与直接使用 `LaTeX` 或 `HTML` 编写相比，很快就能节省时间，特别是在需要多种输出格式的情况下。你还可以与朋友和同事分享你的模板，以换取金钱或荣誉。

## 练习

编写你自己的模板；为 `HTML` 编写一个，为 `LaTeX` 编写一个。

脚注 1   2   3





