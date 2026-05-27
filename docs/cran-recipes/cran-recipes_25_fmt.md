# R 脚本调试指南

假设`my.word.docx`存在于工作目录中；注意重命名的文件名中随机添加的数字：

```
from.file <- c("my.word.docx")
another.name <- sample(seq(1:99999),size = 1)
another.name <- paste("new.word.doc.name",as.character(another.name), ".docx")
to.file <- another.name; file.rename(from.file, to.file);to.file
## Warning in file.rename(from.file, to.file): cannot rename file 'my.word.docx'
## to 'new.word.doc.name 71317 .docx', reason 'The system cannot find the file
## specified'
## [1] FALSE
## [1] "new.word.doc.name 71317 .docx"
```

## 20.3 调试 R 代码

R 的错误信息有时会被取一些令人沮丧的名字，比如晦涩、迟钝、令人反感，甚至难闻。此处无意不敬，但当数小时过去问题仍未解决时，我能理解这种感受。

幸运的是，情况已有改善。`tidyverse` 包及其他一些包会提出有意义的问题，例如“您是不是指……”，并常常提供有用的建议。R 是开源软件，经过众多贡献者和大量包的长期发展，其各组件间的互操作性挑战导致了许多不易理解的错误信息。例如，如本书前面所述，如果您在加载 `tidyverse` 之后加载 `Zeilig` 包，那么 `select` 命令将使用 `Zeilig` 的版本，而非 `tidyverse` 的版本。您会收到一条令您无比沮丧的错误信息，因为您很可能已经按照 `tidyverse`/`dplyr`（而非 `Zeilig`）的要求正确地编写了 `select` 命令。

以下是您在开发 R 脚本时可能遇到的一些常见编码错误：

- 使用单引号（`'`）代替双引号（`"`）。
- 在赋值运算符的字符“`< -`”之间留有空格，而非正确的“`<-`”。
- 变量类别错误。例如，您的日期列可能是字符型而非 `POSIX` 型。
- 来自前一个会话的不需要的对象残留在内存中。为确保程序全新启动，请输入命令`rm(list = ls())`。
- 在需要数值型变量的地方使用了分类变量，反之亦然。
- 对缺失数据（`NA`）处理不当。

RStudio 提供了三种调试工具：

- RStudio 的错误检查器和`traceback()`函数，它们会列出导致错误的函数调用序列。
- RStudio 的“使用调试重新运行”工具和`options(error = browser)`选项，它们会在错误发生处打开一个交互式会话。
- RStudio 的断点和`browser()`函数，它们会在代码中的任意位置打开一个交互式会话。

如果您使用这些工具，并结合使用有意义的名称和清晰结构编写的代码，应该能够解决大多数错误。如果您的数据量庞大，请避免使用全部数据来开发代码。先使用一个子集。此外，如果您的代码用于查找一个罕见条件，请通过故意插入一个“命中”来测试，以确保它能正常工作。否则，您将无法知道是代码有错误，还是您的数据确实没有符合该条件的记录。

我经常使用的另一种技术是，从 RStudio 日志错误信息中复制粘贴到 Google 搜索（精确复制，不要改写）。令人惊讶的是，即使来自不太知名的包的错误信息也会在论坛中被讨论。大多数情况下，您能找到自己的错误或至少是某种变通方法。