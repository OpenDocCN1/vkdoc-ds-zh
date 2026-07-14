
# 12. 结论

至此，您已经了解了 Markdown 和 Pandoc 的大部分功能。我并未涵盖所有功能，但您应该对这些工具的能力有了很好的认识，并能够从在线手册中学习更多内容。

使用 Markdown，您对排版和文档结构的控制程度不如在 LaTeX 中那么高，但许多标记指令的语法要简单得多，这使得使用起来更加容易。尤其是在处理表格、列表和图形时，LaTeX 的语法可能会分散您对文档实际内容的注意力。

有时，您需要 Markdown 本身无法提供的功能，但 Pandoc 提供了多种应对方案。如果您需要指定 Markdown 之外的格式，可以使用模板；如果您需要在格式化文档的同时对其进行转换，可以对其进行预处理或使用过滤器来重写它。

如果您必须为每个新文档编写新的模板和新的过滤器，那么与使用 LaTeX 或 Word 手动格式化每个文档相比，使用 Markdown 和 Pandoc 并无增益。但是，如果您和我一样，可以为所有文档重复使用几个模板，而需要新过滤器的情况少之又少——而且我从未遇到过编写了一个过滤器却只使用一次的情况。

我希望这份 Markdown 和 Pandoc 的介绍对您有所启发，并希望您今后能享受编写 Markdown 的乐趣。



## 索引

### A, B

`begin` 命令， 参考文献， 位图图形， 代码块（编写）， 块引用

### C

`cabal` 包管理器， 层叠样式表 (CSS)， `citeproc` 过滤器， 代码块选项， 条件包含（练习解答，格式化）， 交叉引用过滤器， 交叉引用（图形标签， `pandoc-crossref`， 前缀， 标签前缀， 章节， 表格和公式）

### D

`Div` 块， `doc.get_metadata()`， 文档翻译（格式化， Markdown 文档选项）， 电子书封面（图片扩展名， 章节， 目录）

### E

`elem.content.list`， `elem` Div 块， 强调， `eval_python`， `execute_code` 函数， `exercise_header_level`， 扩展（Pandoc Markdown：脚注， 列表， 智能标点， 表格）

### F, G

图形标签， 文件格式， 过滤器（代码评估， 条件包含， 文档格式化流程， `--filter`/`-F` 选项， JSON 原生格式， 练习编号， `panflute`）， 脚注， 格式化样式（`--from` 和 `--to` 选项， `--from` 选项）

### H

超文本标记语言 (HTML)

### I

图片扩展名， 图片

### J, K

JSON 表示法

### L

LaTeX， LaTeX 宏， 链接， 列表

### M

Makefile， Markdown（格式化和样式选择， HTML， LaTeX 版本， 文本编辑器， 包含语义信息的文本：编写块引用， 强调， 超文本链接， 图片， 编号和未编号列表， 章节， 逐字文本）， 支持 Markdown 的软件， 支持 Markdown 的文本编辑器， 标记语言， 数学（编写）， 元数据（列表， 键值绑定， Markdown 注解， 变量， YAML， `--metadata` 选项）， 元变量

### N, O

练习编号： `begin` 命令， `Cite` 对象， `class` Exercise， `collect_numbers` Div 参数， `Div` 块， `doc` 对象， `elem` Div 块， 元素， `identifier`， `elem` 文本， `exercise_header_level`， 练习环境， 过滤器， `handle_citations`， `label` 命令， LaTeX 环境， `link` 语法， 元变量， `metavariable` `exercise_env`， 原生格式， `no_exercise`， `number_exercises` 过滤器， `pandoc-citeproc`， `RawBlock`， `ref{}` 命令， `run_filter` 函数， YAML 头部

### P, Q

Pandoc：代码块（编写）， 格式化（Markdown 文档， 简单文档， 模板， 格式化文本， 翻译， 多个文档， 编写数学）， `pandoc-citeproc` 过滤器， `pandoc-crossref` 过滤器， `pandoc <inputfile> --native`， `pandoc <inputfile> --to json`， Pandoc 用户贡献的模板， `panflute`（类和属性， 数据属性， 文档元素， 安装， Pandoc 过滤器（编写）， `print_structure`， `run_filter`， 遍历）， 预处理（条件包含， 定义， 文档格式化流程， 文档， `exercises.md` 文件， `--from` 选项， GPP， `header.yml` 文件， 运行代码， `print_structure` 函数， Python）

### R

`RawBlock` 对象， `ref{}` 命令， 引用图形， 引用前缀， 引用章节， 引用章节， R 编程语言， `run_filter`， `run_python`， `run_python_process`

### S

章节， 语义元素， 语义结构， 智能标点， `::: SOLUTION :::` 块， 解答元变量， `::: SOLUTION :::` 语法， 样式表， 语法高亮方案

### T

表格， 模板（`$body$` 占位符， 在 LaTeX/HTML 中编写文档， 点表示法， 格式化， HTML， `if-else` 结构， 期刊， LaTeX， `loop` 结构， 元数据变量， 元变量， 姓名和所属机构（作者）， Pandoc 代码， PDF/LaTeX 输出， 文档进展， 文本格式（HTML）， 标题元数据， 标题前缀， 标题变量， `usepackage` 命令， YAML 头部， `text` 语法， `title-prefix`）， 树结构， 三元组

### U

`usepackage` 命令

### V

逐字文本

### W, X

所见即所得编辑器， 所见即所得文字处理器

### Y, Z

YAML（元数据）
```