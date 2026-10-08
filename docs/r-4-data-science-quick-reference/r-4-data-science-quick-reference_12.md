# 9. 字符串处理：`stringr`

`stringr` 包提供了字符串操作函数。当你加载 `tidyverse` 包时，该包也会被加载：

```
library(tidyverse)
```

你也可以单独加载该包：

```
library(stringr)
```

## 统计字符串模式

`str_count()` 函数用于统计字符串中包含的标记（token）数量，其中标记可以是字符或单词等。默认情况下，`str_count()` 会统计字符串中的字符数。

```
strings <- c(
    "给我一个冰淇淋",
    "给你自己买个冰淇淋",
    "我们的冰淇淋都卖完了",
    "我尖叫，你尖叫，人人都爱冰淇淋。",
    "一个冰淇淋,
    两个冰淇淋,
    三个冰淇淋",
    "我想要一个冰淇淋。你想要一个冰淇淋吗？"
)

str_count(strings)
## [1]  10  11  12  16  14  18
```

对于 `strings` 中的每个字符串，我们得到了字符数。结果与我们使用 `nchar()` 得到的结果相同（但不同于 `length()`，后者会返回包含这些字符串的列表长度，在此例中为 6）。

你可以通过为 `str_count()` 指定 `boundary()` 选项来明确要求其统计字符数。该选项决定了标记之间的边界（如字符、单词、行等），也就是计数的单位。

```
str_count(strings, boundary("character"))
## [1]  10  11  12  16  14  18
```

如果你想统计单词数量，可以使用 `boundary("word")`：

```
str_count(strings, boundary("word"))
## [1]  5  5  7  8  9  11
```

你还可以在 `boundary()` 中使用另外两个选项：`"line_break"` 和 `"sentence"`。它们会根据语言环境（`locale()`）使用启发式规则来判断文本包含多少换行符和句子。

```
str_count(strings, boundary("line_break"))
## [1]  5  5  7  8  11  11

str_count(strings, boundary("sentence"))
## [1]  1  1  1  1  3  2
```

请注意，这里的换行符并非文本中的换行符。这些换行符是指在你所使用的 `locale()` 中期望放置换行符的位置。

最后，`str_count()` 可以让你统计子字符串在字符串中出现的次数：

```
str_count(strings, "冰淇淋")
## [1]  1  1  1  1  3 2

str_count(strings, "冰") # 也统计到了“冰”字
## [1]  2  1  4  3  6  4
```

你要求 `str_count()` 统计的模式不仅仅是一个字符串。它实际上是一个正则表达式。某些字符在正则表达式中具有特殊含义。^(17) 例如，点号（`.`）代表任意单个字符，而不是句号。

```
str_count(strings, ".")
## [1]  10  11  12  16  14  18
```

如果你希望将模式作为字面字符串处理，而不是正则表达式，可以将其包裹在 `fixed()` 中：

```
str_count(strings, fixed("。"))
## [1]  0  0  0  1  0  1
```

由于该模式是正则表达式，我们可以使用它来统计标点符号：

```
str_count(strings, "[[:punct:]]")
## [1]  0  0  0  1  1  2
```

或者统计字符串末尾出现“冰淇淋”的次数：

```
str_count(strings, "冰淇淋$")
## [1]  1  1  1  0  1  0
```

这里的 `$` 表示字符串的末尾。更准确地说，除了末尾可能后跟一个标点符号外，它表示在字符串的末尾。

```
str_count(strings, "冰淇淋[[:punct:]]?$")
## [1]  1  1  1  1  1  1
```

## 拆分字符串

有时，你需要根据某个分隔符来拆分字符串——这与在逗号分隔值（CSV）文件中根据逗号进行拆分类似。`stringr` 包中用于此功能的函数是 `str_split()`。

例如，我们可以按空格拆分：

```
strings <- c(
    "一",
    "二",
    "一 二",
    "一  二",
    "一。 二。"
)

str_split(strings, " ")
## [[1]]
## [1] "一"
##
## [[2]]
## [1] "二"
##
## [[3]]
## [1] "一" "二"
##
## [[4]]
## [1] "一"  ""   "二"
##
## [[5]]
## [1] "一。" "二。"
```

## 字符串拆分

### 默认拆分

由于我们是按单个空格进行拆分，对于包含三个空格的 `"一  二"`，我们会得到空字符串。如果你希望任意长度的空格序列都作为分隔符，可以这样操作：

```r
str_split(strings, "[[:space:]]+")
```

### 使用 `boundary()` 函数拆分

你也可以使用 `boundary()` 函数进行拆分。例如，你可以使用 `boundary("word")` 将字符串拆分成单词：

```r
str_split(strings, boundary("word"))
```

这样操作时，我们排除了第一次尝试中出现的空字符串，同时去掉了最后一个字符串中的句号。

## 字符串大小写转换

你可以使用 `str_to_lower()` 将字符串转换为全小写。

```r
macdonald <- "Old MACDONALD had a farm."

str_to_lower(macdonald)
## [1] "old macdonald had a farm."
```

同样，你可以使用 `str_to_upper()` 将其转换为全大写。

```r
str_to_upper(macdonald)
## [1] "OLD MACDONALD HAD A FARM."
```

如果使用 `str_to_sentence()`，则首字母大写，其余字母小写。

```r
str_to_sentence(macdonald)
## [1] "Old macdonald had a farm."
```

`str_to_title()` 函数会将字符串中的所有单词首字母大写。

```r
str_to_title(macdonald)
## [1] "Old Macdonald Had A Farm."
```

## 换行、填充与修剪

### 换行

如果你想要对字符串进行换行（即添加换行符），使其适配指定宽度，可以使用 `str_wrap()`。

```r
strings <- c(
  "给我一个冰淇淋",
  "给自己拿一个冰淇淋",
  "我们的冰淇淋都没了",
  "我尖叫，你尖叫，大家都爱冰淇淋。",
  "一个冰淇淋，\n  两个冰淇淋，\n  三个冰淇淋",
  "我想要一个冰淇淋。你也想要一个冰淇淋吗？"
)

str_wrap(strings)
```

默认宽度是 80 个字符，但你可以通过 `width` 参数来更改。

```r
str_wrap(strings, width = 10)
```

你可以在换行时使用 `indent` 参数来缩进字符串的第一行。

```r
str_wrap(strings, width = 10, indent = 2)
```

### 填充

如果你希望字符串左对齐、右对齐或居中对齐，可以使用 `str_pad()`。

默认情况下是右对齐字符串。

```r
str_pad(strings, width = 50)
```

如果你想改为左对齐，可以将 `"right"` 传递给 `side` 参数。

```r
str_pad(strings, width = 50, side = "right")
```

你需要使用 `"right"` 来实现左对齐，因为 `side` 参数决定的是在哪一侧进行填充；对于左对齐的文本，填充是在右侧。

如果你想让文本居中，应该使用 `"both"`；你会在左侧和右侧都进行填充。

```r
str_pad(strings, width = 50, side = "both")
```

在这些填充示例中，我们没有让字符串长度保持在填充宽度以下。如果字符串长度超过填充宽度，它将保持不变。你可以使用 `str_trunc()` 函数来将宽度裁剪到某个特定值。例如，我们可以在填充之前先将所有字符串截断为 25 个字符：

```r
strings |> str_trunc(25) |> str_pad(width = 25, side = "left")
```

### 修剪

`str_trim()` 函数用于移除字符串左侧和右侧的空白字符：

```r
str_trim(c(
  " 一小杯可乐",
  "两大杯可乐 ",
  " 三杯中杯可乐 "
))
```

它会保留字符串内部的空白字符。

由于 `str_trim()` 不会处理不在左或右边缘的空白字符，因此我们不能用它来去除字符串内部多余的空格。例如，如果两个单词之间有两个空格，如下所示，`str_trim()` 会保留它们原样：

```r
str_trim(c(
  " 一小杯可乐",
  "两大  杯可乐 ",
  " 三杯中杯可乐 "
))
```

如果我们希望将两个空格缩短为一个空格，可以改用 `str_squish()`。

```r
str_squish(c(
  " 一小杯可乐",
  "两大  杯可乐 ",
  " 三杯中杯可乐 "
))
```

## 检测子串

要检查一个子串是否出现在另一个字符串中，可以使用 `str_detect()`。

```
str_detect(strings,"我")
```

## [1]  TRUE FALSE FALSE FALSE FALSE FALSE

```
str_detect(strings,"你")
```

## [1] FALSE FALSE FALSE  TRUE FALSE  TRUE

```
str_detect(strings,"冰淇淋")
```

## [1] TRUE TRUE TRUE TRUE TRUE TRUE

该模式是一个正则表达式，所以要测试“冰淇淋”后面紧跟句号的情况，你不能直接搜索“冰淇淋。”。

```
str_detect(strings, "冰淇淋.")
```

## [1] FALSE FALSE  TRUE  TRUE  TRUE  TRUE

你可以再次使用 `fixed()` 字符串。

```
str_detect(strings, fixed("冰淇淋."))
```

## [1] FALSE FALSE FALSE  TRUE FALSE  TRUE

或者，你可以对句点进行转义。

```
str_detect(strings,"冰淇淋\\.")
```

## [1] FALSE FALSE FALSE  TRUE FALSE  TRUE

你可以通过将 `negate` 参数设置为 `TRUE` 来测试子串是否 *未* 在字符串中找到。

```
str_detect(strings, fixed("冰淇淋."), negate = TRUE)
```

## [1]  TRUE  TRUE  TRUE FALSE  TRUE FALSE

有两个特殊函数用于测试字符串的开头或结尾：

```
str_starts(strings, "你")
```

## [1] FALSE FALSE FALSE  TRUE FALSE  TRUE

```
str_ends(strings, fixed("。"))
```

## [1] FALSE FALSE FALSE  TRUE FALSE FALSE

如果你想知道子串所在的位置，可以使用 `str_locate()`。它会返回匹配项的起始和结束索引。

```
str_locate(strings, "冰淇淋")
```

##      start end

## [1,]    -1  -1

## [2,]    -1  -1

## [3,]    -1  -1

## [4,]    -1  -1

## [5,]    -1  -1

## [6,]    -1  -1

这里，你得到每个字符串的起始和结束索引，但第六个字符串中这种模式出现了不止一次。

```
strings[6]
```

## [1] "我想要一个冰淇淋。你也想要一个冰淇淋吗？"

当你使用 `str_locate()` 时，只会得到第一个匹配项的索引。

```
str_locate(strings[6], "冰淇淋")
```

##      start end

## [1,]    -1  -1

`str_locate_all()` 函数会给出所有匹配项。

```
str_locate_all(strings[6],"冰淇淋")
```

## [[1]]

##      start end

## [1,]    -1  -1

## [2,]    -1  -1

如果你想要得到 *匹配项之间* 字符串的起始和结束点，可以使用 `invert_match()`。

```
ice_cream_locations <- str_locate_all(strings[6], "冰淇淋")
ice_cream_locations
```

## [[1]]

##      start end

## [1,]    -1  -1

## [2,]    -1  -1

```
invert_match(ice_cream_locations[[1]])
```

##      start end

## [1,]     0  -1

## [2,]     0  -1

## [3,]     0  -1

## 提取子串

要提取匹配模式的子串，可以使用 `str_extract()`。它会返回第一个匹配正则表达式的子串。

```
str_extract(strings, "(尖叫|冰淇淋)\\w*")
```

## [1] "冰淇淋" "冰淇淋" "冰淇淋"

## [4] "尖叫"   "冰淇淋" "冰淇淋"

它只返回第一个匹配项，但如果你想要所有匹配的子串，可以使用 `str_extract_all()`。

```
strings[4]
```

## [1] "我尖叫，你尖叫，大家都爱冰淇淋。"

```
str_extract(strings[4], "(尖叫|冰淇淋)\\w*")
```

## [1] "尖叫"

```
str_extract_all(strings[4], "(尖叫|冰淇淋)\\w*")
```

## [[1]]

## [1] "尖叫"   "尖叫"   "冰淇淋"

## 字符串转换

我们可以将匹配模式的子字符串替换为其他字符串。

```
lego_str <- str_replace(strings, "ice cream[s]?", "LEGO")
lego_str
```

## [1] "Give me an LEGO"

## [2] "Get yourself an LEGO"

## [3] "We are all out of LEGO"

## [4] "I scream, you scream, everybody loves LEGO."

## [5] "one LEGO,\n  two ice creams,\n  three ice creams"

## [6] "I want an LEGO. Do you want an ice cream?"

```
lego_str <- str_replace(lego_str, "an LEGO", "a LEGO")
lego_str
```

## [1] "Give me a LEGO"

## [2] "Get yourself a LEGO"

## [3] "We are all out of LEGO"

## [4] "I scream, you scream, everybody loves LEGO."

## [5] "one LEGO,\n  two ice creams,\n  three ice creams"

## [6] "I want a LEGO. Do you want an ice cream?"

这两个替换操作可以写成管道形式，使代码更具 `Tidyverse` 风格：

```
strings %>%
  str_replace("ice cream[s]?", "LEGO") %>%
  str_replace("an LEGO", "a LEGO")
```

## [1] "Give me a LEGO"

## [2] "Get yourself a LEGO"

## [3] "We are all out of LEGO"

## [4] "I scream, you scream, everybody loves LEGO."

## [5] "one LEGO,\n  two ice creams,\n  three ice creams"

## [6] "I want a LEGO. Do you want an ice cream?"

与之前的大多数函数一样，该函数仅影响第一个匹配项。要替换所有匹配项，需要使用 `str_replace_all()`。

```
strings %>%
  str_replace_all("ice cream[s]?", "LEGO") %>%
  str_replace_all("an LEGO", "a LEGO")
```

## [1] "Give me a LEGO"

## [2] "Get yourself a LEGO"

## [3] "We are all out of LEGO"

## [4] "I scream, you scream, everybody loves LEGO."

## [5] "one LEGO,\n  two LEGO,\n  three LEGO"

## [6] "I want a LEGO. Do you want a LEGO?"

你可以在替换字符串中引用匹配的分组，这是正则表达式中常见的操作。

```
us_dates <- c(
  valentines = "2/14",
  my_birthday = "2/15",
  # 没人知道，但我们就这么定吧
  jesus_birthday = "12/24"
)

# 将美国日期格式转换为更合理的格式
str_replace(us_dates, "(.*)/(.*)", "\\2/\\1")
```

## [1] "14/2" "15/2" "24/12"
