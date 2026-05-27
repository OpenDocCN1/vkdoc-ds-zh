# 11. Unicode

## 11.1 ASCII、UTF-8、Latin-1 以及其他编码的世界

我们大多数人熟悉 `ASCII` 编码。不幸的是，`ASCII` 是字符世界中一个简单、朴素、“白面包”般的例外。`Latin-1` 则不那么常用。字符的其他表示形式取决于所选的编码标准，而标准的选择又可能取决于区域设置。以商标符号为例。在 MS Word 和其他 MS Office 应用程序中，`Ctrl-Alt-T` 或 `Alt8482` 将创建商标符号：`™`。在 Unicode 中，它表示为 `\u2122`，在 R 之外则表示为 `U+2122`。在 HTML 十六进制中，使用 `™`。一旦你确定了编码方案，找到对应的值是件简单的事情，因为这些信息在互联网上都有详细记录。要查找各种 Unicode 的相关信息，请参见 Unicode 联盟的官方网站 [`www.Unicode.org`](http://www.unicode.org)。另一个有用的资源是 [`www.utf8-chartable.de/`](http://www.utf8-chartable.de/)。

Unicode 是一个庞大的话题，其复杂性甚至写满了整本书。大多数 R 程序员并不需要深入学习 Unicode。通常，如果你使用的是比较新的、日常使用的文件和数据库，你将不需要研究 Unicode 选项。几十年前创建的数据可能会包含不常见的字符，这可能会让你陷入编码分析的兔子洞。如果是这样，祝你好运。

识别带有商标符号的文本（UTF-8 编码）。

在 RStudio 中获取各种编码的一个技巧是在其他软件包中键入该编码，然后粘贴。例如，为了在 RStudio 中获取一个商标符号，我在 MS Word 中键入了 `control-alt-t`，并将其复制到以下代码行中：

```
x <- "™ this string starts with a trademark sign"
my.regex <- "\u{2122}"
grepl(my.regex, x)
## [1] TRUE
x <- "this string has no trademark sign"
grepl(my.regex, x)
## [1] FALSE
x <- "This string has a colon in it :  "
my.regex <- "\u{003A}"
grepl(my.regex, x)
## [1] TRUE
```

要查看特定 Unicode 在 RStudio 中显示为什么样子，只需将其赋值给一个变量并显示：

```
x <- "\u00b5"
x
## [1] "μ"
```