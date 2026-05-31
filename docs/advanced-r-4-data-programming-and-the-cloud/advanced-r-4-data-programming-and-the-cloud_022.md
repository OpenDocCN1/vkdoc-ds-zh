# 第 2 章：编程实用工具

若需了解文件修改、变更或访问时间的更详细信息，可使用 `file.info()` 函数。该函数同样接受字符串作为输入。

输出结果包含文件大小、是否为目录、按读/写/执行顺序排列的文件权限整数、最后修改时间、最后变更时间、最后访问时间，以及文件是否可执行：

```
file.info("data/ch02/ch02_text.txt", "NOSUCHFILE.FAKE")
```

```
          size isdir mode mtime
data/ch02/ch02_text.txt   31 FALSE  666 2020-04-25 17:03:35
NOSUCHFILE.FAKE          NA    NA <NA>               <NA>
                      ctime               atime exe
data/ch02/ch02_text.txt 2020-02-15 15:27:15 2020-04-25 17:03:55  no
NOSUCHFILE.FAKE               <NA>               <NA> <NA>
```

借鉴第 1 章中将数据存储到变量中的思路，考虑将文件结果赋值给某个变量名，例如 `ch02FileData`。这实际上是一个数据框，可以按数据框来处理。可以访问列、确定值、执行逻辑运算，以了解哪些文件可能超过特定大小。在以下代码中，我们读取了两个潜在文件的系统信息，确认这确实是一个数据框，并更仔细地检查了其中一列，最后对该列执行了一些逻辑测试：

```
ch02FileData <- file.info("data/ch02/ch02_text.txt", "NOSUCHFILE.FAKE")
is.data.frame(ch02FileData)
```

```
[1] TRUE
```

```
ch02FileData$size
```

```
[1] 31 NA
```

```
is.numeric(ch02FileData$size)
```

```
[1] TRUE
```

```
ch02FileData$size > 5
```

```
[1] TRUE NA
```

在某些情况下，为特定文件设置时间戳可能会很有用。`Sys.setFileTime()` 函数可以实现这一点，它接受两个输入。第一个是文件名或路径加文件名。第二个输入是你希望在文件上设置的时间。在以下示例中，我们还从当前系统时间中减去了 20 秒。在此例中，被更改的是 *mtime*（修改时间）变量：

```
file.info("data/ch02/ch02_text.txt")
```

```
          size isdir mode               mtime
data/ch02/ch02_text.txt   31 FALSE  666 2020-04-25 17:03:35
                      ctime               atime exe
data/ch02/ch02_text.txt 2020-02-15 15:27:15 2020-04-29 19:13:56  no
```

```
newTime <- Sys.time() - 20
newTime
```

```
[1] "2020-04-29 19:13:36 CDT"
```

```
Sys.setFileTime("data/ch02/ch02_text.txt", newTime)
file.info("data/ch02/ch02_text.txt")
```

```
          size isdir mode               mtime
data/ch02/ch02_text.txt   31 FALSE  666 2020-04-29 19:13:36
                      ctime               atime exe
data/ch02/ch02_text.txt 2020-02-15 15:27:15 2020-04-29 19:13:56  no
```

现在将注意力转向文件的创建和删除，`file.create()` 和 `file.remove()` 函数的功能名副其实。除了执行指定的操作外，这些函数还会返回一个布尔值，显示其操作的成功或失败。值得再次提醒的是，删除文件应谨慎处理。特别是如果你在 Windows 系统上，运行以下代码后，请尝试检查回收站：

```
file.create("ch02_created.docx", showWarnings = TRUE)
```

```
[1] TRUE
```

```
file.remove("ch02_created.docx")
```

```
[1] TRUE
```

```
file.remove("NOSUCHFILE.FAKE")
```

```
Warning in file.remove("NOSUCHFILE.FAKE"): cannot remove file 'NOSUCHFILE.FAKE', reason 'No such file'
```

```
[1] FALSE
```

文件也可以被复制和重命名。`file.copy()` 函数可以授予覆盖权限，甚至可以通过 `recursive = TRUE` 选项来复制整个文件夹和子文件夹。此外，它还有选项可以复制模式（即文件权限）以及文件日期数据（或者，当然，也可以让副本拥有新的修改日期）。以下代码示例展示了所有这些功能是如何运行的：

```
Sys.time()
```

```
[1] "2020-04-29 19:13:56 CDT"
```

```
file.copy(
  "data/ch02/ch02_text.txt",
  "ch02_copy.txt",
  overwrite = TRUE,
  recursive = FALSE,
  copy.mode = TRUE,
  copy.date = TRUE
)
```

```
[1] TRUE
```

```
file.info("ch02_copy.txt")
```

```
          size isdir mode               mtime
ch02_copy.txt   31 FALSE  666 2020-04-29 19:13:36
              ctime               atime exe
ch02_copy.txt 2020-04-29 19:13:56 2020-04-29 19:13:56  no
```

```
file.rename("ch02_copy.txt", "ch02.txt")
```

```
[1] TRUE
```

除了创建文件，有时也需要创建目录。实际上，在不存在的目录中创建新文件是行不通的，因此利用 `dir.exists()` 的布尔结果来确保目录存在是很有价值的。如果目录不存在，`dir.create()` 函数可以构建必要的文件夹结构：

```
dir.create("output")
```

```
Warning in dir.create("output"): 'output' already exists
```

```
dir.exists("output")
```

```
[1] TRUE
```

## 2.4 输入

将新数据导入 R 成为下一个挑战。尽管我们在第 1 章中看到了一些手动构建数据的方法，但一般而言这并不可行。本章展示如何导入制表符或逗号分隔的文本文件，以及一些专有格式，包括 Excel、SPSS、SAS 和 Stata。虽然本章不涉及更特殊的数据导入（例如，SQL 数据库、Access、PDF 或 Word 文档），但有许多包可以处理大多数类型的文件导入。对于这些记录中的大多数而言，输入过程是相似的，因此或许不需要面面俱到，而更需要建立稳固的原则。请务必访问本书的 Apress 网站下载代码和文件。特别地，应该有四个名为 `Counties_in_Texas` 的文件，需要保存到我们之前使用过的工作目录下的 `data/ch02` 文件夹中。

我们从 `data.table` 包中的 `fread()` 函数开始，它用于读取 `.csv`（逗号分隔值）文件。该函数通常非常快——尤其对于非常大的文件——并且实际上有几个选项。虽然帮助文件或网络搜索可能会针对你的特定用例提供更具体的选项，但该函数有四个值得注意的特性。

该函数接受一个标题选项，用于决定是否应将文件中的第一行数据视为列名。它接受一个 `sep`（分隔符）参数，用于确定分隔符是什么字符（例如，逗号、空格、制表符）。请花点时间查看 `?fread` 帮助文件以了解所有可能的选项——这确实是一个非常强大的函数。

最后两个特性我们保留了它们的默认设置；这值得讨论一番。我们先花点时间看看数据，然后再回到这两个特性：

```
countiesTxCSV <- fread("data/ch02/Counties_in_Texas.csv",
                       header = TRUE,
                       sep = ",")
head(countiesTxCSV)
```

```
   CountyName FIPSNumber CountyNumber PublicHealthRegion
1:    Anderson          1            1                 4
2:    Andrews           3            2                 9
3:    Angelina          5            3                 5
4:    Aransas           7            4                11
5:    Archer            9            5                 2
6:   Armstrong         11            6                 1
   HealthServiceRegion MetropolitanStatisticalArea_MSA
1:               4/5N                              --
2:               9/10                              --
3:               4/5N                              --
4:                  11                   Corpus Christi
5:               2/3                   Wichita Falls
6:                  1                       Amarillo
   MetropolitanDivisions MetroArea NCHSUrbanRuralClassification_2006
1:                    --  Non-Metro                        Micropolitan
2:                    --  Non-Metro                        Micropolitan
3:                    --  Non-Metro                        Micropolitan
4:                    --      Metro                       Medium Metro
5:                    --      Metro                        Small Metro
6:                    --      Metro                        Small Metro
   NCHSUrbanRuralClassification_2013
1:                        Micropolitan
2:                        Micropolitan
3:                        Micropolitan
4:                       Medium Metro
5:                        Small Metro
```



