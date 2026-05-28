# 函数 `save()`

函数 `save()` 用于保存 R 对象，默认以二进制格式保存到文件中。保存的对象可以通过 `load()` 或偶尔使用 `data()` 加载到工作空间中，或通过 `attach()` 附加到工作空间。关于 `load()`、`data()` 和 `attach()` 的信息，请参阅前一章。

函数 `save()` 接受参数 `...`、`list`、`file`、`ascii`、`version`、`envir`、`compress`、`compression_level`、`eval.promises` 和 `precheck`。待保存对象的名称可以通过两种方式输入：用逗号分隔的符号或包含对象名称的字符串，或包含对象名称的字符向量（或两者兼用）。参数 `file` 指定对象保存的位置。例如：

```
> save( "ClintonCorpus", "mat", list=c( "junk", "trst" ), file="save.bin" )
> load( "save.bin", ver=T )
Loading objects:
junk
trst
ClintonCorpus
mat
> class( junk )
[1] "list"
> class( trst )
[1] "asS4"
attr(,"package")
[1] ".GlobalEnv"
> class( ClintonCorpus )
[1] "SimpleCorpus" "Corpus"
> class( mat )
[1] "data.frame"
```

此处，四个对象被保存到文件 `"save.bin"` 中，随后重新加载。四个对象属于不同的类。

任何类型的对象都可以使用 `save()` 保存。加载时，对象会以其原始名称加载到工作空间中，并且不会在控制台显示。

参数 `ascii` 如果赋值为 `TRUE`，则告诉 `save()` 写入一个 ASCII 文件。如果赋值为 `FALSE`（默认值），则创建二进制文件。对于 `NA`，请参阅 `save()` 的帮助页面。根据 `save()` 的帮助页面，参数 `version` 告诉 `save()` 使用哪个版本的工作空间格式。可选值为 `NULL`（表示当前默认格式），以及 `1`、`2` 或 `3`（分别对应 R 0.99.0 到 R 1.3.1、R 1.4.0 以及从 R 3.5.0 开始的默认格式）。

参数 `envir` 告诉 `save()` 查找对象的环境。该参数的模式为环境，默认值为 `parent.frame()`。参数 `compress` 指示进行何种压缩，或者是否进行压缩。如果为 `FALSE`，则不进行压缩。如果为 `TRUE`，则进行 `"gzip"` 压缩。如果将该值设置为 `"gzip"`、`"bzip2"` 或 `"xz"`，则告诉 `save()` 使用该压缩方法。默认值为 `isTRUE(!ascii)`，因此如果 `ascii` 为 `FALSE`，则默认进行压缩。根据 `save()` 的帮助页面，如果文件参数是一个连接，或者工作空间格式是版本 1，则忽略此参数。

参数 `compression_level` 在 `compress` 不等于 `FALSE` 时给出压缩级别。如果压缩方法是 `"gzip"`，默认级别是 `"6"`。对于 `"bzip2"` 或 `"xz"`，默认级别是 `"9"`。

参数 `precheck` 是一个逻辑参数，当设置为 `TRUE`（默认值）时，告诉 `save()` 在打开文件或连接之前检查对象是否存在。如果设置为 `FALSE`，则即使没有保存任何内容，也会打开文件或连接。对于版本 1，`precheck` 不适用——根据 `save()` 的帮助页面。

参数 `safe` 是一个逻辑参数，当设置为 `TRUE` 时，告诉 `save()` 在保存工作空间时打开一个临时文件，以防保存失败。`TRUE` 是默认值，但会导致保存期间使用更多磁盘空间。如果设置为 `FALSE`，则如果保存失败，工作空间可能会丢失。

有关 `save()` 的更多信息，请在 R 提示符下输入 `?save` 或使用 R Studio 中的“帮助”选项卡。

## 函数 `saveRDS()`

函数 `saveRDS()` 将单个对象保存到文件中。使用 `saveRDS()` 保存的对象可以使用 `readRDS()` 加载——请参阅前一章。`saveRDS()` 的参数有 `object`、`file`、`ascii`、`compress` 和 `refhook`。

参数 `object` 设置为对象的名称，不加引号。参数 `file` 是要分配给文件的名称，是一个字符串或连接。参数 `ascii` 的行为与 `save()` 相同。

下一个参数是 `version`。根据 `saveRDS()` 的帮助页面，将 `version` 设置为 `NULL` 会告诉函数使用默认值——目前是 `2`——自 R 1.4.0 起。对于 R 3.5.0 及更高版本，`version` 的合法选项是 `2` 和 `3`。

参数 `compress` 的行为与 `save()` 中的相同。有关参数 `refhook` 的信息，请参阅帮助页面。

更多信息请在 R 提示符下输入 `?saveRDS` 或使用 R Studio 中的“帮助”选项卡。

## 匹配的导入和导出函数

许多导入和导出函数是成对出现的。例如：`source()` 与 `dump()`；`save()` 与 `load()`、`data()` 或 `attach()`；`dput()` 与 `dget()`；以及 `write.table()` 与 `read.table()`。表 10-1 列出了基于配对关系的导入和导出函数。

## 其他导出函数

与读取数据的函数类似，也有多种写入数据的函数。CRAN 上关于用于导入和导出数据的 `rio` 包的页面列出了许多包及其功能。CRAN 插件文档可在 [`https://cran.r-project.org/web/packages/rio/vignettes/rio.html`](https://cran.r-project.org/web/packages/rio/vignettes/rio.html) 找到。

对于 SPSS、SAS 和 Stata，函数 `write.foreign()`（可在 `foreign` 包中找到）可以以正确的格式导入和导出。`write.foreign()` 函数还可以导出其他一些格式。其他导出函数也可以在 `foreign` 包中找到。

`foreign` 包是默认安装的包之一。要查看 `foreign` 的内容，请在 R 提示符下输入 `help(package=foreign)` 或使用 R Studio 中的“包”选项卡。在包列表中单击 `"foreign"`。要加载 `foreign`，请在 R 提示符下输入 `library(foreign)`，或在 R Studio 的“包”选项卡下勾选 `"foreign"` 左侧的复选框。

一个用于读写 SPSS、SAS 和 Stata 文件的较新的包是 `haven` 包。与 `foreign` 包不同，`haven` 包并非默认安装，因此必须先安装 `haven` 才能加载它。安装 `haven` 后，您可以通过在 R 提示符下输入 `help(package=haven)` 或使用 R Studio 中的“包”选项卡来查看 `haven` 的内容。在包列表中单击 `"haven"`。要加载 `haven`，请在 R 提示符下输入 `library(haven)`，或在 R Studio 的“包”选项卡下勾选 `"haven"` 左侧的复选框。

对于 Excel，有一个专门的包 `xlsx` 用于处理 Excel 文件。`xlsx` 包不是 R 中的默认包，因此必须安装。安装 `xlsx` 后，可以通过在 R 提示符下输入 `help(package=xlsx)` 找到关于 `xlsx` 的信息。对于较旧的 Excel 文件，包 `readxl` 提供了写入和读取 Excel 文件的函数。与 `xlsx` 包一样，`readxl` 并非默认安装，因此必须在加载前先安装。