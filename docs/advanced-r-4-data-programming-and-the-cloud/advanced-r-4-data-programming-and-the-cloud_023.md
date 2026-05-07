# 6: 中型地铁

在 R 中，函数接收*输入*并使用它们来创建所需的精确*输出*。

当最初构建时，程序员会指定参数的数量、这些参数的预期顺序以及参数的名称。在前面的示例中，`fread()`依赖于第一个参数的*预期顺序*。换句话说，文件位置和名称被*预期*作为**第一个**输入。然而，接下来的两个输入——我们确保文本的第一行将成为我们的列名并强制分隔符为逗号——实际上*不是按顺序*的。因为我们为这两个参数使用了预期的变量名，R 仍然正确地解析了该函数，并且正确的事情发生了。这个函数原本会期望先输入分隔符。此外，默认值为`sep = "auto"`，它执行一些可能有效也可能无效的魔法来检测分隔字符。通过将其明确改为逗号，我们确保文件被正确读取。此外，默认值也是`header = "auto"`。对于许多读取输入的函数，默认值是`TRUE`。

取决于`fread()`自动处理过程的构成，这可能做出也可能不做出正确的选择。在撰写本文时，如果第一行仅包含字符数据，自动处理过程会假定第一行是列名。

现在，前面我们提到这个函数还有两个特性值得注意，但我们保留了默认设置。其中一个特性是默认选择`stringsAsFactors = FALSE`。因子，如第 1 章所述，是一种强制分类数据具有数值顺序的方式。然而，在输入时，进行排序的默认方式是“按数据读取的顺序”。这可能不是正确的顺序（例如，在大多数情况下，将“强烈同意”编码为 1，“中立”编码为 2，“同意”编码为 3 是不好的）。与许多默认将字符串转换为因子的基础 R 读取 CSV 输入的函数不同，`fread()`默认**不进行因子化**。

最后一个特性非常不错，并且随着我们更深入地探索数据表，它会变得更好。与一些仅将数据读取为数据框的函数不同，`fread()`默认将数据读取为数据表：

```
is.data.table(countiesTxCSV)
```

```
## [1] TRUE
```

在处理数据时，虽然逗号分隔值很常见，但通常还有其他格式需要读入内存。*Stata*就是其中一种格式，此类文件格式可以通过文件后缀`.dta`来识别，而函数`read_dta()`来自`haven`包。请记住，许多更新版本的 R 数据结构以数据框作为其底层格式，并在此基础上增加了一些额外功能，值得注意的是，从技术上讲，这个输入是一个`tibble`。正如第 1 章所述，我们将在第 9 章更详细地讨论这些内容。目前，它可以当作任何数据框处理。在这种情况下，虽然我们保留了默认的不跳过设置，但我们明确展示了`skip = 0`选项，该选项可用于跳过任意数量的顶部行（例如，`skip = 2`将跳过前两行）：

```
countiesTxDTA <- read_dta("data/ch02/Counties_in_Texas.dta", skip = 0)
tail(countiesTxDTA)
```

```
## # A tibble: 6 x 10
##   CountyName FIPSNumber CountyNumber PublicHealthReg˜ HealthServiceRe˜
##   <chr>            <dbl>        <dbl>            <dbl> <chr>
## 1 Wise               497          249                3 2/3
## 2 Wood               499          250                4 4/5N
## 3 Yoakum             501          251                1 1
## 4 Young              503          252                2 2/3
## 5 Zapata             505          253               11 11
## 6 Zavala             507          254                8 8
## # ... with 5 **more** variables: MetropolitanStatisticalArea_MSA <chr>,
## #   MetropolitanDivisions <chr>, MetroArea <chr>,
## #   NCHSUrbanRuralClassification_2006 <chr>,
## #   NCHSUrbanRuralClassification_2013 <chr>
```

```
is.data.frame(countiesTxDTA)
```

```
## [1] TRUE
```

类似地，我们也可以通过`read_spss()`函数导入 SPSS 文件。在这种情况下，从我们的头部视图来看，一切正常。以下示例中唯一特别新颖的特性是更改了`header()`中要显示的默认行数：

```
countiesTxSPSS <- read_spss("data/ch02/Counties_in_Texas.sav", skip = 0)
head(countiesTxSPSS, n = 10)
```

```
## # A tibble: 10 x 10
##    CountyName   FIPSNumber CountyNumber PublicHealthReg˜
##    <chr>              <dbl>        <dbl>            <dbl>
##  1 Anderson               1            1                4
##  2 Andrews                3            2                9
##  3 Angelina               5            3                5
##  4 Aransas                7            4               11
##  5 Archer                 9            5                2
##  6 Armstrong             11            6                1
##  7 Atascosa              13            7                8
##  8 Austin                15            8                6
##  9 Bailey                17            9                1
## 10 Bandera               19           10                8
## # ... with 6 **more** variables: HealthServiceRegion <chr>,
## #   MetropolitanStatisticalArea_MSA <chr>,
## #   MetropolitanDivisions <chr>, MetroArea <chr>,
## #   NCHSUrbanRuralClassification_2006 <chr>,
## #   NCHSUrbanRuralClassification_2013 <chr>
```

```
is.data.frame(countiesTxSPSS)
```

```
## [1] TRUE
```

到现在为止，通过文件位置读取数据应该相当平常了。值得注意的是，虽然我们的示例侧重于工作目录子文件夹中的文件，但没有理由要求文件位置必须是本地的。在具有共享驱动器的网络环境中，完全可以使用那些远程位置。如果驱动器在您的本地机器上被映射到一个字母（例如，“N:”驱动器），那么这些位置的文件可以像我们在这些示例中一样被访问。一个警告：使用此类完整文件路径编写的代码通常*不能*移植到其他机器上。有一些包和技术试图解决此类问题，但这超出了本书的范围。对于小型办公环境，统一驱动器映射字母名称是解决这一挑战的一种笨拙方法（前提是代码打算留在内部使用）：

```
countiesTxXl <- read_xlsx("data/ch02/Counties_in_Texas.xlsx")
head(countiesTxXl)
```

```
## # A tibble: 6 x 10
##   CountyName FIPSNumber CountyNumber PublicHealthReg˜ HealthServiceRe˜
##   <chr>      <chr>             <dbl>            <dbl> <chr>
## 1 Anderson   001                   1                4 4/5N
## 2 Andrews    003                   2                9 9/10
## 3 Angelina   005                   3                5 4/5N
## 4 Aransas    007                   4               11 11
## 5 Archer     009                   5                2 2/3
## 6 Armstrong  011                   6                1 1
## # ... with 5 **more** variables: MetropolitanStatisticalArea_MSA <chr>,
## #   MetropolitanDivisions <chr>, MetroArea <chr>,
## #   NCHSUrbanRuralClassification_2006 <chr>,
```



