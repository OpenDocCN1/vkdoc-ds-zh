# 9. 字符串

## 9.1 简介

处理基于文本的数据是所有编程框架的重要特性。字符串被简单地定义为一组字符。这些字符包括由以下内容构成的字符和单词（字符组）：

*   大写字母，例如：`A, B, C ...`
*   小写字母，例如：`a, b, c ...`
*   印度-阿拉伯数字，例如：`1, 2, 3 ...`
*   一些特殊符号，例如：`!, @, #, $, %, ^, &, *`

它们可以在大多数基于英语的键盘上找到。那么其他语言呢？它们也必须被包含在计算框架内。然而，人类使用的自然语言并非计算机科学中计算的首选语言。相反，计算机使用的是二进制数的语言，所有实体都被定义为由两个比特（`1` 或 `0`）组成的字节组。因此，这些字符及其组必须在一个被国际接受的特定协议中与二进制数进行映射。由此，ASCII 和 Unicode 系统被开发出来。

## 9.2 ASCII 系统

ASCII 字符系统将英文字符、数字（作为字符）以及一些特殊字符映射到 `0` 到 `127` 之间的整数值。这 `128` 组组合涵盖了大多数英语相关工作所需的字符。一个 `7` 位（2⁷ = 128）的比特可以存储一个唯一的 ASCII 字符。

ASCII 系统运行良好，但其范围有限。随着更多国家加入计算社区，更多的语言符号需要被纳入计算体系中。一系列数学符号也需要被纳入。

## 9.3 Unicode 系统

Unicode 系统是 ASCII 的扩展，它增加了用于存储单个字符的字节数，从而增加了可以被唯一定义的字符数量。这个系统涵盖了多种语言以及用于数学符号的特殊符号。Julia 支持 Unicode 字符定义，这意味着它们可以像任何其他字符一样在计算中使用。这对于数学环境来说是一个巨大的优势，因为直接使用数学符号使得理解变得容易。例如，`π` 可以直接作为符号本身写入，而不是使用 `pi` 作为该符号的记忆辅助。熟悉 LATeX 公式编写的人会理解，这些符号是通过在其命令前加上 `\` 运算符来编写的；Julia 中也是如此。要写入 `π`，你可以输入 `\pi` 然后按键盘上的 Tab 键。这将显示 Unicode 符号 `π`。

现在让我们看看 Julia 如何理解和解释文本信息。我们将从字符开始，然后扩展到称为字符串的字符组。字符主要通过键盘输入，但它们也可以来源于机器内部或与外界交互的仪器中的文件。Julia 提供了强大的功能来处理所有与字符串处理相关的工具。

## 9.4 字符

由于 Julia 是一种面向对象的编程语言，字符也必须被定义为对象。Julia 中用于字符的数据类型是 `Char`。让我们先通过一个例子来理解字符：

```
julia> a = 'a'
'a'
julia> typeof(a)
Char
```

这里，变量 `a` 引用了一个存储类型为 `Char` 的对象的存储位置，该对象的值为 `'a'`。巧合的是，变量的名称也是 `a`。虽然 `a` 对于 Julia 来说是一个引用名，但 `'a'` 指的是一个 `Char`（字符）对象。



### 9.5 对应的整数值

由于字符是以二进制位集的形式存储的，这些二进制位可以被解释为数字。因此，每个字符都有一个对应的整数值。通过将字符定义为一个整数对象，可以说明这一点，如下所示：

```
julia> Int32('a')

julia> Int32('z')

julia> Int32('!')

julia> Int32('#')

```

反之亦然；整数也对应特定的字符：

```
julia> Char(121)
'y'
julia> Int32('y')

```

并非所有整数值都是有效的 Unicode 字符。有效的 Unicode 码位（以十六进制表示）为 U+00 - U+d7ff 和 U+e000 - U+10ffff。所有这些数字目前尚未被赋予可识别的含义，但它们是有效的 Unicode 字符。Julia 使用机器的区域和语言设置来确定必须打印的字符。

由于整数与字符相关联，因此它们可以用于某些算术运算符。例如，可以计算 `'a'-'b'` 和 `Int32('a') - Int32('b')`，并且发现它们的结果是相似的，因为 `'a'` 和 `'b'` 对应于整数值（分别由 `Int32('a')` 和 `Int32('b')` 给出）：

```
julia> 'a' - 'b'

julia> Int32('a') - Int32('b')

julia>  Int32('a')

julia>  Int32('b')

julia> 'A' == 'a' # 大写字母
### 与小写字母的整数值不同
false
julia> 'A'  Int32('A')

julia> 'A'+1
'B': ASCII/Unicode U+0042
(类别 Lu: 字母，大写)
julia> 'A'+2
'C': ASCII/Unicode U+0043
(类别 Lu: 字母，大写)
julia> 'A'+58 # 结果对应于
### 符号 "}" 对应的整数值
'{': ASCII/Unicode U+007b
(类别 Ps: 标点，开括号)
```

当我们输入像 `'A'+1` 这样的命令时，我们得到一个字符作为结果，该字符对应于一个整数值，而这个整数值又对应于计算的结果。由于 `'A'` 对应整数值 65，加 1 得到 66，而 66 对应 `'B'`。输出还显示了附加信息——ASCII/Unicode U+0042（类别 Lu: 字母，大写）。这表明输出是一个 ASCII/Unicode 对象，其类别是 Lu——一种被定义为大写的字母。

## 9.6 + 运算符与字符

当我们连接两个 `Char` 对象时会发生什么？在大多数编程语言中，`+` 运算符对字符和字符串起到连接运算符的作用。当需要将字符 `a` 和 `!` 组成字符串 `a!` 时，我们通常写 `'a'+'!'` 或 `+('a','!')`。让我们检验一下是否可以在 Julia 中实现：

```
julia> char1 = 'a' # 定义第一个字符
'a': ASCII/Unicode U+0061
(类别 Ll: 字母，小写)
julia> char2 = '!' # 定义第二个字符
'!': ASCII/Unicode U+0021
(类别 Po: 标点，其他)
julia> typeof(char1) # 验证对象类型
Char
julia> typeof(char2) # 验证对象类型
Char
julia> +(char1,char2) # 运算符 + 作用于 char1 和 char2
ERROR: MethodError: no method matching +(::Char, ::Char)
最接近的候选方法为:
+(::Any, ::Any, ::Any, ::Any...) at operators.jl:424
+(::Char, ::Integer) at char.jl:40
+(::Integer, ::Char) at char.jl:41
```

如前面的 Julia 代码所示，我们得到了一个 `MethodError`，因为连接运算符不处理 `Char` 对象。所有运算符都被定义为 Julia 函数（参见第 10 章）。方法是与对象相关联以探测其属性的函数。Julia 有一个特性——多重分派——它允许根据数据类型进行不同的函数定义。因此，虽然 `+` 函数能识别像 `Int64` 和 `Complex64` 这样的对象，但它不能识别 `Char`，因为在源代码中没有为此定义。因此，不能使用 `+` 运算符连接两个字符。

如果字符串是字符的集合，那么在不允许连接的情况下，字符如何构成字符串呢？我们将在下一节探讨这个想法。

### 9.6.1 字符和字符串是两种数据类型

Julia 使用单引号定义字符。字符串定义需要使用双引号或三引号括起单个字符或一组字符：

```
julia> a1 = 'a' # 字符 'a' 由 a1 引用
'a'
julia> typeof(a1) # a1 的类型是 Char
Char
julia> a2 = "a" # 字符串 "a" 由 a2 引用
"a"
julia> typeof(a2) # a2 的类型是 String
String
julia> a2 == a1 # a2 和 a1 不相等，因为
#### 它们存储的是不同的对象
false
```

当我们希望将单引号或双引号作为字符串的一部分打印出来时，会使用三引号：

```
julia> """You've been "warned" already !Don't repeat!"""
"You've been \"warned\" already !Don't repeat!"
```

## 9.7 + 运算符与字符串

代码行 `a2 == a1` 很重要。尽管 `Char` 和 `String` 对象的值使用相同的字母，即 'a'，但它们实际上并不相同。字符和字符串在行为上有很大差异。字符串能用 `+` 运算符连接吗？

```
julia> a1 = "Hello "
"Hello "
julia> a2 = "world"
"world"
julia> a1+a2
ERROR: MethodError: no
method matching +(::String, ::String)
最接近的候选方法为:
+(::Any, ::Any, ::Any, ::Any...)
at operators.jl:424
```

我们现在可以看到 `+` 运算符不能连接字符串。Julia 必须提供一种替代方法来添加或删除字符串中的元素。

## 9.8 连接

字符串的连接是使用 `string()` 函数执行的，该函数连接由 `,` 分隔符分隔的多个字符串：

```
julia> string('a','!') #  输入是字符
"a!"
julia> string("a","!") #  输入是字符串
"a!"
julia> string("a",'!') # 输入是字符串和字符
"a!"
julia> string("a",'!',' ', "wow", "#") # 第三个字符
### 是一个空白字符
"a! wow#"
```

另一个例子将使其更清晰：

```
julia> h = "hello"
"hello"
julia> w = "world"
"world"
julia> ws = " "# 一个空白字符串
" "
julia> str = string(h,ws,w)
"hello world!"
```

或者，`*` 运算符也执行连接操作：

```
julia> "a"*"!"
"a!"
julia> *("a","!")
"a!"
julia> h = "hello"
"hello"
julia> w  =  "world"
"world"
julia> ws = " "
" "
julia> *(h,ws,w)
"hello world"
```

## 9.9 插值

如果变量存储了一些值（在本例中是 `String` 对象），它们的详细调用方式如下：

```
julia> h = "hello"
"hello"
julia> ws = " "
" "
julia> e = "!"
"!"
julia> "$h$ws$e"
"hello !"
```

这是一种更好的连接方式，因为它更方便，特别是在我们需要在作为输出的字符串中填充变量值时：

```
julia> a = 10

julia> int = "Integer"
"Integer"
julia> b = 10.0
10.0
julia> float_number = "Floating point number"
"Floating point number"
julia> "$a is stored as $int"
"10 is stored as Integer"
julia> "$b is stored as $float_number"
"10.0 is stored as Floating point number"
```

如果我们需要打印字符 `$` 本身呢？在这种情况下，我们应该在其前面加上反斜杠字符：

```
julia> statement = "I have "
"I have "
julia> currency = "US Dollars"
"US Dollars"
julia> value = 100

julia> print("$statement \$100 ($currency) in my account")
I have  $100 (US Dollars) in my account
```



## 9.10 字符串与数组相似

就像数组一样，字符串的字符也具有索引。在 Julia 中，索引始终从 1 开始，最后一个索引可以通过使用`end`来访问。这可以通过以下示例来理解：

```
julia> str = """You've been "warned" alread !Don't repeat!"""
"You've been \"warned\" alread !Don't repeat!"
julia> str[1]
'Y'
julia> str[end]
'!'
julia> str[end-20]
'a'
julia> length(str)

julia> str[10:20]
"en \"warned\""
julia> str[21:end]
alread !Don't repeat!"
```

变量`str`引用了已定义的字符串。上述代码中依次执行了以下操作：

* `str[1]` 输出该字符串对象的第一个字符。
* `str[end]` 输出该字符串的最后一个字符。
* `str[end-20]` 输出从末尾开始的第二十个字符。
* 字符串的总长度可以通过 `length(str)` 计算得出。
* 使用小于 1 或大于`end`的索引会引发`BoundsError`。
* 可以使用`:`运算符进行切片。例如，`str[10:20]` 输出从第十个字符到第二十个字符。类似地，`str[21:end]` 输出从第二十一个字符到字符串末尾（最后一个字符）。

如果我们编写`str[10]`和`str[10:10]`，应该期望得到什么结果？第 10 个字符是`e`：

```
julia> str[10]
'e'
julia> str[10:10]
"e"
julia> typeof(str[10])
Char
julia> typeof(str[10:10])
String
julia> str[10:10] == str[10]
false
```

可以观察到，`str[10]`的输出是一个`Char`类型的对象，而`str[10:10]`的输出是一个`String`类型的对象。即使它们的值相同，它们也不是相似的对象。因此，相等运算符`==`显示`false`作为其输出。

### 9.10.1 search()

内置函数 `search()` 可用于搜索字符串中特定字符的索引。第一个参数是需要被搜索的字符串，第二个参数是需要被搜索的字符/字符串。在搜索单个字符时，输出是索引号。在搜索字符串时，输出是范围对象。如果在输入字符串中未找到输入的字符，则输出为 `0`。如果在给定的字符串中未找到输入的字符串，它会输出一个 `0:-1` 的范围对象，表示在任何给定字符串中都找不到输入的字符串。以下 Julia 代码对此进行了演示：

```
julia> search("Sandeep Nagar", 'N')

julia> search("Sandeep Nagar", 's')

julia> search("Sandeep Nagar", 'a',3) # 偏移量为 3

search("Sandeep Nagar", "Sandeep")
1:7
julia> search("Sandeep Nagar", "sandeep")
0:-1
julia> search("Sandeep Nagar", "randeep")
0:-1
```

### 9.10.2 contains()

内置函数 `contains()` 可用于测试特定字符或字符串是否包含在某个测试字符串中。与 `search()` 函数相比，其输出的不同之处在于 `contains()` 函数的输出是一个布尔对象。（如果找到输入的字符串，则为 `true`，否则为 `false`）。另外，此函数仅适用于 `String` 对象，不适用于 `Char` 对象，因此会引发 `MethodError`。因此，如果需要搜索某个字符（例如前面的 `S`），则必须将其作为字符串（`"S"`）输入。以下 Julia 代码对此进行了说明：

```
julia> contains("Sandeep Nagar", "Sandeep")
true
julia> contains("Sandeep Nagar", " ")
true
julia> contains("Sandeep Nagar", "S")
true
julia> contains("Sandeep Nagar",'S')
ERROR: MethodError: no method matching contains
(::String, ::Char)
Closest candidates are:
contains(::Function, ::Any, ::Any)
at reduce.jl:664
contains(::AbstractString, ::AbstractString)
at strings/search.jl:378
```

另一种方法是使用成员函数 `in()`，如下所示：

```
julia> in('S',"Sandeep Nagar")
true
```

## 9.11 常用字符串函数

多种字符串函数可以对字符串进行操作以执行特定任务。其中一些将在以下章节中讨论。

### 9.11.1 repeat()

可以通过使用 `repeat()` 函数将字符串重复特定次数：

```
julia> repeat("Hi",3)
"HiHiHi"
```

或者，`^` 运算符也可以执行相同的操作。这在数学上也非常明显，因为幂就是连续的乘法；例如，2³ = 2 × 2 × 2。由于运算符 `*` 用于连接，`^` 则对类似的操作执行指定次数的连续运算：

```
julia> "Hi"³
"HiHiHi"
julia> ^("Hi",3)
"HiHiHi"
```

### 9.11.2 join()

内置函数 `join(io, items, delim, [last])` 将 `items` 的元素打印到 `io`，并在它们之间使用 `delim`（分隔符）。如果指定了 `last`，它将用作最终分隔符，而不是 `delim`：

```
julia> a = join(["Beginner","Intermediate","Advanced"],","," and ")
"Beginner,Intermediate and Advanced"
julia> println("""Three stages of julia learner are \n $a""")
Three stages of julia learner are
Beginner,Intermediate and Advanced
```

### 9.11.3 start()， endof() 和 next()

内置函数 `start()` 返回第一个有效索引。这通常是 1。内置函数 `endof()` 返回可用于索引的最大（字节）索引。另一个内置函数 `next()` 返回索引 `i` 处或之后的第一个字符以及紧随其后的下一个有效字符索引。如果我们尝试搜索超出输入字符串中最大索引的范围，会遇到 `BoundsError`：

```
julia> start("Sandeep Nagar")

julia> endof("Sandeep Nagar")

julia> length("Sandeep Nagar")

julia> a,b = next("Sandeep Nagar",5)
('e', 6)
julia> a,b = next("Sandeep Nagar",13)
('r', 14)
julia> a,b = next("Sandeep Nagar",14)
ERROR: BoundsError: attempt to access
"Sandeep Nagar" at index [14]
Stacktrace:
[1] next(::String, ::Int64) at ./strings/string.jl:197
```

这些函数可用于使用循环结构遍历字符串。

### 9.11.4 split()

内置函数 `split()` 接收一个 `String` 对象作为输入，并返回一个 `Array` 对象，其中的元素是每个单词的单独 `String` 对象，即一个子字符串数组：

```
julia> str = String("Hi, How are you")
"Hi, How are you"
julia> split(str)
4-element  Array{SubString{String},1}:
"Hi,"
"How"
"are"
"you"
```

如果需要正好在指定字符出现的位置创建子字符串，则可以插入一个额外的参数。例如：

```
julia> str = String("Hi, How are you")
julia> split(str,"w")
2-element Array{SubString{String},1}:
"Hi, Ho"
"are you"
julia> split(str,"o")
3-element Array{SubString{String},1}:
"Hi, H"
"w are y"
"u"
julia> split(str,"are")
2-element Array{SubString{String},1}:
"Hi, How "
" you"
julia> split(str,"ow")
2-element Array{SubString{String},1}:
"Hi, H"
" are you"
julia> split(str,"")
15-element Array{SubString{String},1}:
"H"
"i"
","
" "
"H"
"o"
"w"
" "
"a"
"r"
"e"
" "
"y"
"o"
"u"
```

如前面的示例所示，如果使用空字符串进行分割，则每个字符都会构成一个子字符串。

### 9.11.5 uppercase() 和 lowercase()

内置函数 `uppercase()` 和 `lowercase()` 分别将字符串中的字符转换为大写或小写字符。

```
julia> uppercase("Sandeep Nagar")
"SANDEEP NAGAR"
julia> lowercase("Sandeep Nagar")
"sandeep nagar"
```

在对字符串 `Sandeep Nagar` 应用 `uppercase()` 函数的情况下，字符串中的所有字符都会转换为大写字符。那些已经是大写的字符保持不变。`lowercase()` 函数也发生类似的操作，小写字符在操作后保持不变。



### `replace()`

内置函数 `replace()` 返回一个新字符串，其中一部分字符子串被替换为其他内容：

```
julia> name = "Sandip Nagar"
"Sandip Nagar"
julia> replace(name,"i","ee") # 将 "i" 替换为 "ee"
"Sandeep Nagar"
julia> replace("sandeep","e",uppercase)
"sandEEp"
```

如最后一个命令所示，也可以向 `replace()` 函数传入一个函数，以生成所需格式的字符串来替换原字符串。

### `lpad()` 和 `rpad()`

可以使用 `lpad()` 和 `rpad()` 在字符串的左侧和右侧用特定字符填充特定次数：

```
julia> name = "Sandeep Nagar"
"Sandeep Nagar"
julia> length(name)

julia> lpad("Sandeep Nagar",15,"a")
"aaSandeep Nagar"
julia> rpad("Sandeep Nagar",15,"a")
"Sandeep Nagaraa"
```

在接下来的章节中，变量 `name` 引用了一个长度为 13 个字符的 `String` 对象。当使用 `lpad(name,15,"a")` 时，字符 `a` 从左侧被填充两次，以达到所需的 15 个字符长度。`rpad()` 函数则从右侧完成类似的任务。

### `reverse()`

内置函数 `reverse()` 反转字符串：

```
julia> reverse("Sandeep Nagar")
"ragaN peednaS"
```

### `strip()`、`lstrip()` 和 `rstrip()`

在以编程方式处理字符串时，去除字符串中的不必要字符是一项重要功能。内置函数 `strip()` 即执行此操作：

```
julia> name = " Sandeep Nagar "
#### 字符串开头和结尾各有两个空格
Sandeep Nagar "
julia> length(name)

julia> strip(name)
#### 两个空格（开头和结尾）已被去除
"Sandeep Nagar"
julia> lstrip(name)
#### 左侧的一个空格被去除
"Sandeep Nagar "
julia> rstrip(name)
#### 右侧的一个空格被去除
" Sandeep Nagar"
```

### `randstring()`

在测试代码时，生成随机字符串与生成单个或一组随机数同等重要。这可以通过内置函数 `randstring()` 实现，如下所示：

```
julia> randstring(20)
"8bXPxczPGOv2EDweJBtX"
julia> randstring(20)
"3BMgodnhVbgVQnW6h9E0"
julia> randstring(20)
"nMDC8x4yc8UYMsNPdQrx"
julia> randstring(20)
"cZDVojcv2RYjzyZCLJ6B"
```

请注意，上述示例表明，每次使用相同的命令 `randstring()` 都会得到不同的字符串，这验证了其随机性。由于同样原因，用户可能得到与上述不同的字符串集合。

### 从字符串中读取数据为数组

有时数据可能以 `string` 对象的格式存在。为了进行数学处理，这些数据必须转换为数组的元素。要从字符串读取到数组中，可以使用 `IOBuffer()` 函数，该函数在给定字符串的底层数据上创建一个只读的 `IOBuffer`。要理解这个对象，可以尝试使用 `help?> IOBuffer` 进行探索。（这些 I-O（输入-输出）对象将在第 12 章中讨论。因此，此处不讨论细节，但为简单起见，演示了其基本用法。）

```
julia> str = "1 2 3 4 5 6 7 8 9" # 一个字符串对象
#### 其中条目由空格分隔
"1 2 3 4 5 6 7 8 9"
julia> a = IOBuffer(str) # IOBuffer 对象
IOBuffer(data=UInt8[...],
readable=true,
writable=false,
seekable=true,
append=false,
size=17,
maxsize=Inf,
ptr=1,
mark=-1)
julia> readdlm(a) # 使用分隔符读取
1x9 Array{Float64,2}:
1.0  2.0  3.0  4.0  5.0  6.0  7.0  8.0  9.0
julia> str = "1,2,3,4,5,6,7,8,9" #  字符串，
#### 其中条目由逗号分隔
"1,2,3,4,5,6,7,8,9"
julia> a = IOBuffer(str)
IOBuffer(data=UInt8[...],
readable=true,
writable=false,
seekable=true,
append=false,
size=17,
maxsize=Inf,
ptr=1,
mark=-1)
julia> readdlm(a) # 无空格，无列
# 逗号也是元素的一部分
# 因此元素不是数值类型
1x1 Array{Any,2}:
"1,2,3,4,5,6,7,8,9"
```

函数 `readdlm()`（带分隔符读取）将此 `IOBuffer` 对象转换为 `array` 对象。列被认为由一个或多个空格字符分隔。如果所有数据都是数值，结果将是一个数值数组。如果某些元素不能解析为数字，则返回一个由数字和字符串组成的异构数组，数据类型为 `Any`。

当数据由字符串对象组成时，会创建一个子字符串数组，如下所示：

```
julia> str = "Hi How are you"
"Hi How are you"
julia> a = IOBuffer(str)
IOBuffer(data=UInt8[...],
readable=true,
writable=false,
seekable=true,
append=false,
size=14,
maxsize=Inf,
ptr=1,
mark=-1)
julia>  readdlm(a)
1x4 Array{Any,2}:
"Hi"  "How"  "are"  "you"
```

由于数组由 `Any` 对象组成，如果数据由字符串和数字混合组成，也不会有问题。

```
julia> str = "Hi How are you number 1"
"Hi How are you number 1"
julia> a = IOBuffer(str)
IOBuffer(data=UInt8[...],
readable=true,
writable=false,
seekable=true,
append=false,
size=23,
maxsize=Inf,
ptr=1,
mark=-1)
julia> readdlm(a)
1x6 Array{Any,2}:
"Hi"  "How"  "are"  "you"  "number"  1
```

## 字符串的字典序比较

标准比较运算符通过字典序规则 [3] 比较字符串。字典序比较是通常用于在词典中按字母顺序对单词排序的那种比较。因此，它有时被称为字典顺序。

字典序比较涉及顺序比较两个范围内相同位置的元素，直到其中一个元素与另一个不等效。比较这些第一个不匹配元素的结果就是字典序比较的结果：

```
julia> str1 = "abcdefg"
"abcdefg"
julia> str2 = "abcdefh"
"abcdefh"
julia> str1  str3 = "abcdefhi"
"abcdefhi"
julia> str3 > str2
true
julia> str4 > str3
false
julia> "20 March 2017" > "19 March 2017"
true
julia> "20 March 2016" > "19 March 2017"
true
julia> "superhero" == "Superhero"
false
julia> "superhero" > "Superhero"
true
```

## 总结

处理文本数据的能力是 Julia 的一个重要特性。要理解字符在计算机中如何定义和处理，请查看第 12 章，第 12.6 节。以可编程方式处理字符让计算语言学家能够探索自然语言处理（NLP）下的任务，并在人类和非人类语言中推导出有意义的模式。Julia 定义字符串并以多种方式灵活操作它的能力使其成为 NLP 的良好候选者。生物信息学领域也有这些需求。因此，Julia 是这些领域下编写代码以实现更快执行的优秀候选者。

