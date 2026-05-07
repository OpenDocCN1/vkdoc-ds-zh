# 9. 七大神器

R 使用七个正则表达式函数进行模式匹配和替换。如果你知道如何使用这些函数配合适当的正则表达式模式，那么你就拥有了一套适用于大多数数据科学应用的强大且高效的工具包。

让我们看看每个函数的使用代码。

## 9.1 grep

### 9.1.1 模式匹配与替换

`grep`：一个用于匹配字符串向量的正则表达式模式。如果指定了`value = TRUE`，则返回匹配的字符。换句话说，如果输入为`"aabbcc"`且模式为`"aa"`，输出将是`"aabbcc"`。如果`value = FALSE`，输出将是匹配向量元素的索引号。

`grep`查找至少一个数字，因此字符串被认为与`\\d+`匹配：

```
text <- "abc2"
grep("\\d+", text, value = TRUE)
[1] "abc2"
```

这里没有找到数字，因此字符串与`\\d+`模式不匹配：

```
text <- "abc"
grep("\\d+", text, value = TRUE)
character(0)
```

`grep`显示包含“apple”的向量元素编号：

```
grep("apple", c("crab apple", "Apple jack", "apple sauce", "apple_store"))
## [1] 1 3 4
```

回答“字符串 A 是否在字符串 B 中？”这个问题。答案是 1，因为在`"Post Box"`中找到了`"ox"`字符：

```
stringa <- "ox"
stringb <- "Post Box"
answer <- grep(stringa, stringb)
answer
## [1] 1
```

尝试在`"Post Box"`末尾增加一个`"ox"`。答案仍然是 1，因为增加一个`"ox"`不会改变结果：

```
stringb <- "Post Boxox"
answer <- grep(stringa, stringb)
answer
## [1] 1
```

将`"ox"`替换为`"xx"`。整数零表示未找到`"ox"`：

```
stringb <- "Post Bxx"
answer <- grep(stringa,stringb)
answer
## integer(0)
```

否定方法。检查一个字符串中*不包含*另一个字符串。答案 = 1 表示`"zz"`不在`"Post Bxx"`中：

```
stringa <- "zz"
answer <- grep(stringa, stringb, invert = TRUE)
answer
## [1] 1
```

模式中使用大小写。大小写选项默认为 TRUE 答案。由于`stringa`是大写的，因此没有匹配：

```
stringa <- "OX"
stringb <- "Post Box"
answer <- grep(stringa,stringb)
answer
## integer(0)
```

在多个字符串中查找`"ox"`：

```
stringa <- "ox"
stringb.as.vector <- c( "Roxan", "bird", "frog", "moxy","MOXY")
answer <- grep(stringa, stringb.as.vector, value = TRUE)
answer
[1] "Roxan" "moxy"
```

### 9.1.2 函数结构

`grep`的一般结构如下：

- `pattern` – 你要查找的字符串。
- `x` – 你要测试的向量。
- `ignore.case` – 如果模式匹配区分大小写则为`FALSE`，否则为`TRUE`。
- `perl` – `FALSE`表示告诉`grep`使用 R 版本的正则表达式。`TRUE`表示使用 Perl 语法。大多数人忽略这个参数。
- `fixed` – `FALSE`。如果设置为`TRUE`，所有冲突的参数都将被覆盖。这个参数不常用。
- `useBytes` – `TRUE`表示逐字节匹配，否则按字符匹配。不常用。
- `invert` – `TRUE`表示告诉`grep`返回**不**匹配的元素的索引或值。

```
grep(pattern, x, ignore.case = FALSE, perl = FALSE, value = FALSE, fixed = FALSE, useBytes = FALSE, invert = FALSE)
```

## 9.2 grepl

`grepl`：如果字符串包含正则表达式模式则返回`TRUE`，否则返回`FALSE`。`grepl`中的“l”代表逻辑值（logical）。`grepl`接受一个向量，并为每个元素返回`TRUE`或`FALSE`。`"\\d"`表示测试一个数字，而`"+"`扩展为包含多个数字，例如：

```
test <- "the earth has a radius of 3,986 miles"
digits <- grepl("\\d+", test)
digits
[1] TRUE
```

在三个字符串中查找包含`"on"`字符的元素。这有点类似于 Excel 的`FIND`函数，只不过不是返回起始位置数字或`NA`，而是返回逻辑值`TRUE`或`FALSE`：

```
test.vector <- c("London","Boston","888Knoxville99")
grepl("*on",test.vector)
[1]  TRUE  TRUE FALSE
```

查看`test.vector`中是否存在任何数字。正则表达式包含`"d+"`，表示一个或多个数字：

```
grepl("\\d+",test.vector)
[1] FALSE FALSE  TRUE
```

如果你在方括号内放一个数字范围和脱字符`"^"`，它表示**非**（NOT）后面的内容。在这种情况下，模式意味着根据字符串开头是否包含一个或多个数字来评估`TRUE`/`FALSE`。此时，你可能有理有据地觉得正则表达式被加入了一些疯狂的元素。同一个字符根据它在方括号内还是外，同时表示**开头**和**非**。为什么发明者要这样做？也许是个宇宙玩笑。

```
grepl("^([⁰-9])",test.vector)
[1]  TRUE  TRUE FALSE
```



### 9.2.1 空白字符

在正则表达式中，空白字符指空格、制表符、回车、换行或换页符。空白字符的符号是`\s`。

即使`Mary`没有被空白字符包围，以下表达式也为真：

```
x <- "wwwMarywww"
grepl("Mary|Jane|Sue",x)
[1] TRUE
```

元字符`\b`是一个锚点，类似于脱字符和美元符号。它匹配被称为“单词边界”的位置。在以下示例中，`Mary`被`w`包围，没有边界。因此，模式匹配结果为假：

```
grepl("\b(Mary|Jane|Sue)\b",x)
[1] FALSE
```

如果我们改用`\S`，即在所有名称前没有空白字符，表达式为`TRUE`：

```
grepl("\\S(Mary|jane|Sue)",x)
[1] TRUE
```

日期模式匹配的示例：

```
date1 <- "2008-08-08"
pattern1 <- "\\b(\\d{4})-(\\d{2})-(\\d{2})\\b"
grepl(pattern1, date1)
[1] TRUE
```

**挫折提示**

如果你收到类似“……无法识别的转义字符……”的错误信息，请尝试使用双斜杠而不是单斜杠。

当然，你也可以直接在`grepl`函数的引号中输入模式：

```
grepl( "\\b(\\d{4})-(\\d{2})-(\\d{2})\\b", date1)
[1] TRUE
```

## 9.3 sub

`sub`仅替换模式的第一个匹配项。它返回一个长度相同的字符向量。未匹配的元素保持不变。`sub`的意思是“在向量`v`中，用`y`替换`x`”。在`sub`中仅替换第一个元素。参见`gsub`以进行多次替换。

以下代码将以字母`a`开头及其相邻字母的字符串替换为以`z`开头的字符串，并在`a`字符串的末尾加上一个`z`。每个字符串元素中仅替换第一个匹配项：

```
sub("(q+)", "z\\1z", c("qbc", "def", "cbq q", "qq"), perl=TRUE)
[1] "zqzbc"   "def"     "cbzqz q" "zqqz"
```

请注意，`cbq q`中的第二个`q`未受影响。如果希望替换字符串中的所有匹配项，请使用`gsub`。

`sub`可用于字符替换。在`new.vector1`中，原始数字被替换为单个数字`7`。

```
vector1 <- c("department 123", "Think ahea", "99 bottles of beverage on the wall")
new.vector1 <- sub("[[:digit:]]","7",vector1)
new.vector1
[1] "department 723"                     "Think ahea"
[3] "79 bottles of beverage on the wall"
```

完全替换找到的任何数字为单个数字`7`（使用`+`符号）：

```
new.vector1 <- sub("[[:digit:]]+","7",vector1)
new.vector1
[1] "department 7"                      "Think ahea"
[3] "7 bottles of beverage on the wall"
```

## 9.4 gsub

`gsub`的工作方式与`sub`完全相同，不同之处在于它会替换字符串中所有出现的模式。

在此示例中，任何等于`0-9`的字符在测试中都被设为空：

```
test.vector <- c("ppp - 82", "g - 67.4", "w = 12.544")
scrubbed.var <- gsub("[⁰-9]", "", test.vector)
scrubbed.var
[1] "82"    "674"   "12544"
```

将现有向量替换（替换）为空，但保留`0`到`9`之间的所有内容以及句点字符：

```
test.vector.with.period <- gsub("[⁰-9\\.]", "", test.vector)
test.vector.with.period
[1] "82"     "67.4"   "12.544"
```

在`gsub`输出中仅包含小写字母：

```
test.vector.lower.case.alpha <- gsub("[^a-z]", "", test.vector)
test.vector.lower.case.alpha
[1] "ppp" "g"   "w"
```

删除数字，将任何数字替换为空。`\d`表示数字：

```
test.vector.no.numbers <- gsub("\\d", "",test.vector)
test.vector.no.numbers
[1] "ppp - " "g - ."  "w = ."
```

删除所有空格。`\s`是空白指示符：

```
test.vector.no.spaces <- gsub("\\s","",test.vector)
test.vector.no.spaces
[1] "ppp-82"   "g-67.4"   "w=12.544"
```

#### 9.4.1 更多 gsub 示例

```
x <- "Lawrence of Arabia"
gsub("Arabia","Chicken scratch Tennessee",x)
## [1] "Lawrence of Chicken scratch Tennessee"
```

现在尝试将`Arabia`中的小写字母`a`进行替换。替换失败，`x`保持不变：

```
gsub("arabia", "Chicken scratch Tennessee",x)
## [1] "Lawrence of Arabia"
```

忽略大小写，替换再次生效：

```
gsub("arabia","Chicken scratch Tennessee", x,ignore.case=T)
## [1] "Lawrence of Chicken scratch Tennessee"
```

将数字替换为星号：

```
x <- "HMS Victory is a 104-gun ship of the line of the Royal Navy, launched in 1765."
y <- gsub("\\d+","***",x)
y
## [1] "HMS Victory is a ***-gun ship of the line of the Royal Navy, launched in ***."
```

## 9.5 regexpr

`regexpr`确定在字符串中找到匹配项的位置。输出的第一个元素显示每个元素中匹配项的起始位置。值为`-1`表示不匹配。第二个元素“匹配长度”显示匹配的长度。在下面的第一个示例中，匹配长度为`2`，因为`ve`是两个字符的字符串。其他模式的匹配长度不会如此明显。第三个元素（属性`useBytes`）的值为`TRUE`，表示匹配是逐字节而不是逐字符进行的：

```
x <- c("version 1.2", "vvv24", "engine is a v8", "bigfoot", "have you been there?")
regexpr("ve", x)
## [1]  1 -1 -1 -1  3
## attr(,"match.length")
## [1]  2 -1 -1 -1  2
## attr(,"index.type")
## [1] "chars"
## attr(,"useBytes")
## [1] TRUE
x <- "Alexander the Great was born in 356 BCE"
y <- regexpr("\\d+",x)
y
## [1] 33
## attr(,"match.length")
## [1] 3
## attr(,"index.type")
## [1] "chars"
## attr(,"useBytes")
## [1] TRUE
```

## 9.6 gregexpr

`gregexpr`用于识别模式在字符向量中的位置，其中每个元素被单独搜索。返回的对象是一个列表。相比之下，`regexpr`函数返回一个向量，例如：

```
v1  <- " Do rats prefer pie 3.141 What does the golden rat prefer 1.6180 "
v2  <- gregexpr("\\d+",v1)
v2
## [[1]]
## [1] 21 23 59 61
## attr(,"match.length")
## [1] 1 3 1 4
## attr(,"index.type")
## [1] "chars"
## attr(,"useBytes")
## [1] TRUE
```

以下是解释此输出的方法：

- 长度结果——在此示例中，第一个数字`3`的长度为`1`。
- 第二个数字（句点之后）的长度为`3`。
- 第四个数字`1`的长度为`1`。
- 第五个数字`6180`的长度为`4`。
- 索引结果——独立的（非连续的）数字起始于位置`21`、`23`、`61`和`63`。

## 9.7 regexec

`regexec`执行模式匹配、逻辑测试、识别字符串字符位置、向量索引号、替换、反转（显示不匹配的内容）、部分匹配、区分大小写和不区分大小写的匹配，我确定还有更多功能深藏在技术手册中。`strsplit`和其他一些函数也使用正则表达式，但我认为“宏伟七侠”比“宏伟八侠”听起来更好。

`regexec`返回一个列表，其长度与文本的每个元素相同，如果无匹配则为`-1`，否则为一个整数序列，包含匹配项的起始位置以及对应于模式中带括号子表达式的所有子字符串。它还包含属性`match.length`，一个给出匹配长度的向量（如果无匹配则为`-1`）。位置和长度的解释以及属性遵循`regexpr`。

将 URL 分解为其组成部分的示例：

```
x <- "http://stat.umn.edu:80/xyz"
m <- regexec("^(([^:]+)://)?([^:/]+)(:([0-9]+))?(/.*)", x)
m
## [[1]]
## [1]  1  1  1  8 20 21 23
## attr(,"match.length")
## [1] 26  7  4 12  3  2  4
## attr(,"index.type")
## [1] "chars"
## attr(,"useBytes")
## [1] TRUE
regmatches(x, m)
## [[1]]
## [1] "http://stat.umn.edu:80/xyz" "http://"
## [3] "http"                       "stat.umn.edu"
```

脚注 1  2  3



