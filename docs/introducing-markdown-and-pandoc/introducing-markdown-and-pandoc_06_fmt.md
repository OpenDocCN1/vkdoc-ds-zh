# 5. 翻译文档

当我们用 Markdown 编写好文档后，需要使用`Pandoc`将其翻译成其他文件格式。不过，首先需要下载`Pandoc`。请访问 Pandoc 的安装指南 [`http://pandoc.org/installing.html`](http://pandoc.org/installing.html)，并按照适用于你平台的说明进行操作。

## 使用 Pandoc 格式化 Markdown 文档

在第一个示例中，我们可以用下面这个简短的 Markdown 文档：

```
## 这是一个测试文档
文档中的一些文本。
* 这是一个列表
* 包含两个项目
```

如果将此 Markdown 文档保存为名为`input.md`的文件，我们可以使用以下命令将其转换为 HTML 文件`output.html`：

```
pandoc -o output.html input.md
```

`-o`选项指定输出文件。`input.md`文件在指定时无需任何选项。输入文件不需要任何选项，并且你可以提供多个输入文件。如果提供多个输入文件，Pandoc 在处理它们之前会先将其拼接在一起。因此，如果你想将分别写在多个文件中的几个章节组装成一本书，只需在命令行中按章节在书中出现的顺序提供这些文件即可。

`Pandoc`会根据文件扩展名识别输入和输出格式。因此，如果你使用上述命令，它会识别出输入是 Markdown（文件后缀`.md`），输出应为 HTML（文件后缀`.html`）。你还可以显式指定输入和输出格式。可以使用`--from`选项指定输入格式，使用`--to`选项指定输出格式。在大多数情况下，你无需指定格式——文件名已包含全部所需信息——但有时不同格式会共享相同的文件后缀，例如 EPUB 和 EPUB3 格式都使用文件后缀`.epub`。在这种情况下，你就需要用到这些选项了。

如果你指定了输入和输出文档格式，还可以将`pandoc`视为一个可接受管道输入并输出格式化文档的程序。例如，你可以这样写：

```
cat input.md | \
pandoc --from markdown --to html \
> output.html
```

这种做法本身用途不大，但结合预处理（第 10 章）和过滤器（第 11 章）后，就会变得非常方便。

回到`pandoc`的输出。如果你在终端中运行之前的命令：

```
pandoc -o output.html input.md
```

那么`output.html`文件现在应包含以下 HTML 内容：

```
这是一个测试文档

文档中的一些文本。

这是一个列表
包含两个项目
```

如果你不熟悉 HTML，可能看起来不太容易理解，但我希望你至少能识别出输入 Markdown 中的元素。

这段 HTML 并非一个完整的 HTML 文件。它只是对应 Markdown 文档的一个 HTML 片段，缺少完整 HTML 页面所需的页眉和页脚标记。默认情况下，Pandoc 生成的 HTML 标记可以添加到网页中，但不能作为独立文档使用。要同时添加页眉和页脚，你可以使用`--standalone`选项。

```
pandoc --standalone -o output.html input.md
```

如果你想要生成一个完整的文档，`--standalone`选项对于 HTML 输出是必需的。如果你选择的输出格式通常不以片段形式存在（例如 PDF 文档（后缀`.pdf`）、EPUB 文档（后缀`.epub`或`.epub3`）或 Word 文件（后缀`.docx`）），Pandoc 会自动创建完整文档，此时不需要`--standalone`选项。

如果你运行`pandoc --standalone -o output.html input.md`，你会收到一条警告：

```
[警告] 此文档格式需要一个非空元素。
请在元数据中指定'title'或'pagetitle'，
例如，在命令行中使用 --metadata pagetitle="..."。
回退为'input'
```

但尽管有警告，你仍然会得到一个包含所有必要元素的 HTML 文档：

```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" lang="" xml:lang="">
<head>
  <meta charset="utf-8" />
  <meta name="generator" content="pandoc" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes" />
  <title>input</title>
  <style>
    code{white-space: pre-wrap;}
    span.smallcaps{font-variant: small-caps;}
    span.underline{text-decoration: underline;}
    div.column{display: inline-block; vertical-align: top; width: 50%;}
  </style>
</head>
<body>
<h1 id="this-is-a-test-document">这是一个测试文档</h1>
<p>文档中的一些文本。</p>
<ul>
<li>这是一个列表</li>
<li>包含两个项目</li>
</ul>
</body>
</html>
```

这里的通用 HTML 代码比我们的文本多得多，但幸运的是我们无需为此担心；我们可以专注于 Markdown，而让 Pandoc 处理其余部分。

我们收到的警告与这行中获得的标题有关：

```
<title>input</title>
```

Pandoc 只是从输入文件中获取了名称，但它原本期望得到显式提供的标题。你可以使用元数据来实现这一点；请参见第 8 章。现在先忽略它。

你可以尝试使用以下命令生成不同的输出格式：

```
pandoc -o output.pdf input.md
pandoc -o output.docx input.md
pandoc -o output.epub input.md
pandoc --to=epub3 -o output.epub input.md
```

在最后一个示例中，我们明确指定输出中使用的 EPUB 格式为 EPUB3。

另一个可能需要指定`--to`格式的情况是 PDF。默认情况下，Pandoc 会使用 LaTeX 创建 PDF 文件，但你可以通过以下命令指定它改用 ConTeXt：

```
pandoc --to=context -o output.pdf input.md
```

要获取支持的输入和输出格式的完整列表，请分别运行以下命令：

```
pandoc --list-input-formats
```

和：

```
pandoc --list-output-formats
```

Pandoc 可以在多种不同的输入和输出格式之间进行转换，但在本书中，我们只考虑 Markdown 输入以及如何将 Markdown 转换为其他格式。

### 常用实用选项

你可以使用许多选项来影响 Pandoc 转换文档的方式。完整列表请参考在线手册³。这里，我将列出一些在我自己的写作中觉得特别有用的选项。

#### 节与章

首先，我们考虑与节解释方式相关的选项。在 Markdown 中，我们通过井号的数量来指定不同级别的节标题，但在生成文档时，我们有时需要考虑顶级结构是部分、章还是节。如果我们写的是简短报告或论文，我们希望一级标题开始新的节；但如果我们写的是书，我们希望它们开始新的章。默认行为取决于我们生成的输出格式，Pandoc 会为我们做一些猜测，但你可以使用`--top-level-division`选项显式选择顶级结构。对于我的书籍，我希望一级标题作为章标题，因此我使用`--top-level-division=chapter`。

#### 目录

要为输出文档添加目录，可以使用`--toc`选项或`--table-of-contents`选项；前者只是后者的缩写形式。你可以使用`--toc-depth`选项指定目录中包含的标题级别。在制作电子书时，我通常只需要章级别的目录，因此我使用`--toc-depth=1`。在制作 PDF 时，我接受默认设置。你可以尝试不同的选项值，看看哪种更符合你的偏好。

#### 图片扩展名

如果你希望从 Markdown 输入同时生成 PDF 和电子书，你可能希望在 PDF 输出中使用 PDF 矢量图形，而在电子书版本中使用位图 PNG。在 PDF 输出中使用位图图形意味着你需要考虑分辨率问题，但在电子书中使用 PDF 图形并不总是有效。当你使用如下语法在文档中插入图片时：

```
![图片标题](img/graphics-file)
```

你需要指定输入文件名，但如果你想同时生成电子书和 PDF，你并不想因为输出格式不同而修改所有文件名。

你可以省略图形文件的文件扩展名，并使用以下选项来指定所需的扩展名：

```
--default-image-extension
```

这样，对于任何你没有显式写出文件扩展名的图形文件，Pandoc 都会使用默认扩展名。我在生成 PDF 文档时总是使用
```
--default-image-extension=pdf
```
而在生成电子书时使用
```
--default-image-extension=png
```

### 电子书封面

电子书除了文本内容外还包含封面图片。如果你制作电子书，你也需要指定封面图片。你可以使用 `--epub-cover-image` 选项来实现。如果你的封面图片位于 `cover.png` 文件中，你可以这样写：
```
--epub-cover-image=cover.png
```

## 使用 Makefile

对于本书，我将所有文本都放在一个 `book.md` 文档中。对于像这样的一本短书来说这没问题，但通常我会将每一章放在单独的文件中。编译我的书籍的命令行可能会变得相当长，而且我经常需要记住不同输出格式的各种选项，所以如果每次构建新版本书籍时都使用命令行，很快就会变得繁琐且极易出错。因此，我使用 Make (`https://tinyurl.com/nyc2ec2`) 来编译我的书籍。

如果你不熟悉 Make，我会提供足够的细节让你能够阅读我作为示例给出的 Makefile，并可能为你自己的 Pandoc Makefile 提供一个起点，但全面介绍 Make 超出了本书的范围。许多程序都能解决 Make 所解决的相同问题，所以如果你不喜欢 Make，还有其他选择。

我使用的 Makefile 并不复杂，了解以下几点就足够了：

1.  通过编写 `VARIABLE_NAME := VALUES` 来定义一个变量。

2.  使用 `$(VARIABLE_NAME)` 来引用变量所持有的值。

3.  当你编写 `target: dependencies` 时，意思是如果你想要得到 `target`，那么如果自上次构建 `target` 以来任何依赖项发生了变化，你就需要重新构建它。如果依赖项的依赖项发生了变化，那么你需要先构建依赖项，然后再构建目标，以此类推。

4.  `target: dependencies` 之后用制表符缩进的行是让你的计算机生成 `target` 所需的指令。

5.  Makefile 中的第一个 `target: dependencies` 行是默认目标，如果你在命令行中没有提供其他目标，Make 将处理这个目标。

我为本书使用的 Makefile 大致如下（尽管我省略了一些选项）。我将在这里为你逐行讲解。

```
CHAPTERS := header.yml book.md
PANDOC := pandoc
OPTS_ALL := --toc --smart \
--top-level-division=chapter
PDF_OPTS := $(OPTS_ALL) \
--default-image-extension=pdf
EPUB_OPTS := $(OPTS_ALL) \
--default-image-extension=png \
-t epub3 --toc-depth=1 \
--epub-cover-image=cover.png
all: book.pdf book.epub book.docx
book.pdf: $(CHAPTERS) Makefile
$(PANDOC) $(PDF_OPTS) -o $@ $(CHAPTERS)
book.epub: $(CHAPTERS) Makefile
$(PANDOC) $(EPUB_OPTS) -o $@ $(CHAPTERS)
book.docx: $(CHAPTERS) Makefile
$(PANDOC) $(PDF_OPTS) -o $@ $(CHAPTERS)
clean:
rm book.pdf book.epub book.docx
```

我使用一个变量来保存输入文件。本例中只是头文件和一个 Markdown 文件。你可以在 Pandoc 命令行或 YML 文件中指定元数据（见第 8 章）。你可以将元数据放在 Markdown 文本中，但那样的话它必须位于输入的开头，并且你不能在不包含第一章的情况下翻译单个章节。我更喜欢将元数据放在一个单独的文件中，本例中就是 `header.yml`。

本书我只有一个 Markdown 文件。我是在 Ulysses 编辑器中写作的，我可以在其中将书籍分成不同的部分，但将其导出为一个合并的文件。由于章节和部分的划分保存在 Ulysses 中，而不是在单独的文件中，所以我没有每章一个文件。

我将输入文件放在变量 `CHAPTERS` 中。如果你有多个文件，可以将其添加到 `CHAPTERS` 变量中。我也将 Pandoc 命令行工具放在一个变量中。我安装了多个版本，可以通过更新该变量来切换。之后，我将所有 Pandoc 运行共享的参数放在 `OPTS_ALL` 中，然后将 PDF 和 EPUB 特定的选项分别放在 `PDF_OPTS` 和 `EPUB_OPTS` 中。我也会制作 Word 文档，但我可以为此使用 PDF 参数；Pandoc 会知道如何用与 PDF 相同的选项来制作 Word 文档。接下来是所有目标、依赖项以及生成目标的命令。目标 `all` 和 `clean` 是特殊的。第一个本身不构建任何东西；它只是触发其依赖项的构建——因此它是间接构建这些依赖项——这些依赖项是 Makefile 知道如何制作的三种书籍格式。`clean` 目标没有任何依赖项，但它会删除我们用中间三条命令生成的书籍。

然后我定义了一些我想用于所有输出格式的选项，以及一些我只想用于 PDF 的选项，以及其他我只想用于电子书的选项。在元数据中，我设置了 PDF 输出，我也可以把它放在头文件中——如果这样做，它们不会干扰 EPUB 输出——但我选择在这里设置它们。

我在 Makefile 中构建 EPUB 书籍，但如果你计划在 iBooks 上发布，我不建议使用这个文件。我发现使用 Pages 来排版和提交书籍要容易得多。Pages 可以读取 Word 文档，这也是我通常向 iBooks 提交书籍的方式：我制作一个 Word 文档，在 Pages 中打开它，然后提交。你可能会认为以正确的格式构建书籍然后使用 iTunes Connect 提交会更简单。你错了。如果你想在亚马逊（通过 Kindle Direct Publishing）上发布，你最好也使用他们的工具 Kindle Create。Kindle Create 可以读取 Word 文件，你可以从那里提交。有一个命令行工具可以将 EPUB 转换为 Kindle 上使用的 MOBI 文件格式，但 Kindle Create 更易于使用。
```