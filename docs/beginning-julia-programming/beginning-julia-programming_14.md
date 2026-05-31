# 12. 输入输出

## 12.1 引言

数据的输入和输出对于编程语言来说是如此基本的操作，以至于它们常常被视为理所当然，而与之相关的强大结构也大多被忽略。Julia 提供了多种输入和输出数据的方式。区分它们并为特定应用选择合适的方式非常重要。

Julia 提供了丰富的接口生态系统来处理流式 I/O 对象，例如终端、管道和 TCP 套接字。Julia 倾向于以流的形式处理数据；数据是连续地流入 Julia 程序，而不是作为内存块进行处理的。流有几种不同的类型（用于输入和/或输出目的）。本章将介绍 Julia 处理数据输入和/或输出流的各种方式。

## 12.2 控制台 I/O

实际上，这种模式下的 Julia 是输入和输出数据的媒介。这属于控制台 I/O 的范畴。我们已经为此目的使用了两个函数：`print()` 和 `println()`。它们将其输入内容打印到 Julia 终端上，其中 `println()` 还会在输出后附加一个换行符。`print` 使用的表示形式包含最小格式化，并尽量避免 Julia 特定的细节：

```
julia> println("Hello World")
Hello World
julia> print("Hello World")
Hello World
```

终端输入可以使用内置的 `readline()` 函数实现。它会读取键盘输入，直到遇到第一个换行符。当使用 `readline()` 函数进行输入时，换行符 `\n` 也会被存储：

```
julia> a = readline();
Hi
julia> a
"Hi\n"
```



## 12.3 基本流式 I/O

Julia 的基本函数 `read()` 和 `write()` 将流作为它们的第一个参数。我们先来探索内置的写入函数 `write()`：

```
julia> write(STDOUT,"Hello")
Hello5
julia> write(STDOUT,"Hello\n")
Hello

julia> write(STDOUT,"Hello\nWorld")
Hello
World11
julia> write(STDOUT,"Hello\tWorld")
Hello   World11
julia> write(STDOUT,"Hello World")
Hello World11
```

`STDOUT` 是一个全局变量，指向标准输出流：

```
julia> a = STDOUT
Base.TTY(RawFD(14) open, 0 bytes waiting)
```

`TTY` 代表计算机终端。默认情况下，`STDOUT` 是一组输出数据流，指向计算机终端。当我们执行以下命令时：

```
write(STDOUT,"Hello")
```

我们得到了输出 `Hello5`。这里的 `Hello` 是输入给 `write()` 函数的字符串内容，而 `5` 是该流中的字节数。当我们执行以下命令时：

```
write(STDOUT,"Hello\n")
```

我们在流中添加了一个新字符——换行符（由 `\n` 表示）；因此，字节数增加到了 `6`。此外，当它在 `TTY`（计算机终端）上打印时，你可以观察到换行符也被打印了——即在打印 `Hello` 和 `6` 之间，存在一个换行符（有一行间距）。其他测试命令也展示了相同的功能。

可以通过在命令末尾加上 `;` 字符来抑制返回字节数。这一点可以在以下 Julia 代码中轻松验证：

```
julia> write(STDOUT,"Hello")
Hello5
julia> write(STDOUT,"Hello");
Hello #也抑制了\n
julia>
```

内置函数 `read()` 的工作方式类似。顾名思义，这个函数将输入一个流。和 `STDOUT` 一样，存在一个指向标准输入流的全局变量 `STDIN`，它将被用于 `read()` 函数中。我们首先在以下 Julia 代码中使用它，并从输出中观察其用法和行为：

```
julia> read(STDIN,Char)
'\n': ASCII/Unicode U+000a
(category Cc: Other, control)
```

`read()` 函数会将来自标准输入（默认设置在键盘上，并存储在全局变量 `STDIN` 中）的流视为字符数据类型。在这个例子中，按下了回车键，它代表一个换行符 `\n`，然后该字符被打印出来。类似地，你也可以打印像 `a` 这样的字母，如下所示：

```
julia> read(STDIN,Char)
a
'a': ASCII/Unicode U+0061
(category Ll: Letter, lowercase)
julia>
```

在同样的命令结构中，甚至一个数字键也会产生字符数据类型：

```
julia> read(STDIN,Char)

'1': ASCII/Unicode U+0031
(category Nd: Number, decimal digit)
julia>
```

当输入两个数字然后按回车键时，第二个数字将被送入 Julia 命令提示符：

```
julia> read(STDIN,Char)

'1': ASCII/Unicode U+0031
(category Nd: Number, decimal digit)
julia> 0 # 从前一个输入送入 REPL

julia> typeof(ans)
Int64
```

这里，按下了数字 1 和 0 的键，然后按下了回车键。在这种情况下，1（作为字符的数字 `1`）成为流的一部分并被正确显示，之后 0 被送入下一个命令提示符，它计算出结果 0 并显示出来。由于最后求值的实体存储在变量 `ans` 中，检查 0 的数据类型确认它是 `Int64`，而不像 1 那样是 `Char`：

```
julia> read(STDIN,Char)
ab
'a': ASCII/Unicode U+0061
(category Ll: Letter, lowercase)
julia> b
ERROR: UndefVarError: b not defined
```

对一组字符执行类似操作会导致 `julia> b` 出现错误，因为变量 `b` 未定义且无法求值。

使用命令 `methods(read)`，你可以查看可以输入给函数 `read()` 的数据类型种类。另外，值得注意的是，`write()` 将要写入的数据作为其第二个参数，而 `read()` 将要读取的数据类型作为其第二个参数。

## 12.4 字节数组流

就像单个字符（字节）可以被流式传输一样，字节数组也可以按如下方式进行流式传输：

```
julia> x = rand(UInt8,3)
3-element Array{UInt8,1}:
0x72
0x5a
0x37
julia> read!(STDIN,x)
abc
3-element Array{UInt8,1}:
0x61
0x62
0x63
julia> x
3-element Array{UInt8,1}:
0x61
0x62
0x63
```

在第一个命令期间，三个数据类型为 `UInt8`（8 位 = 1 字节）的随机数被存储在一个名为 `x` 的变量中。这些数字被送入 `read!()` 函数。`!` 的添加迫使该函数用新值更改存储的值。现在字节数组 `x` 已准备好读取三个字节。当在键盘上输入字符 `a`、`b` 和 `c` 时，这些字节被存储在字节数组中。可以验证它们的十六进制表示存在于 `x` 的元素值中。相反，另一种将字节值流式传输到字节数组中的方法如下：

```
julia> read(STDIN,3)
abc
3-element Array{UInt8,1}:
0x61
0x62
0x63
julia> ans
3-element Array{UInt8,1}:
0x61
0x62
0x63
```

这里三个值被流式传输到数组默认输出变量 `ans` 中：

```
julia> read(STDIN,3)

3-element Array{UInt8,1}:
0x31
0x32
0x33
julia>
julia> read(STDIN,3)
1s3
3-element Array{UInt8,1}:
0x31
0x73
0x33
julia> read(STDIN,3)
#$%
3-element Array{UInt8,1}:
0x23
0x24
0x25
```

注意，流式传输的字节输出以其十六进制表示形式显示。因此，输入流中的输入可以是任何类型的 Unicode 字符。

## 12.5 流式传输一行字符

当必须流式传输一行字符时，可以使用一个更简单的内置函数（`readline()`），它可以输入字符以组成一行。在键盘上按下回车键会打印一个换行符，它标志着一行的结束。`readline()` 函数的输出是一个字符串。这可以通过在 Julia 命令提示符下执行 `typeof(ans)` 来验证：

```
julia> readline(STDIN)
Hi, How are you
"Hi, How are you\n"
julia> readline(STDIN)
123.345
"123.345\n"
julia> readline(STDIN)
#$%^yY&*(
"#\$%^yY&*(\n"
julia> typeof(ans) #在 REPL 中探查最后一个输入
String
```

请注意，根据特定终端的设置，`TTY` 可能是行缓冲的，因此可能需要额外输入一个回车，数据才会被发送到 Julia REPL。



## 12.6 文本 I/O

写入文本数据的能力取决于输出介质。例如，某些软件及关联硬件只能理解 ASCII 字符，而另一些则还能理解 Unicode 字符。例如，一个七段数码管只能使用 ASCII 码显示英文字母和罗马数字。因此，开发者务必牢记特定应用的目标硬件-软件组合类型。通常，人们编写的 Julia 代码需要在图形显示器（例如台式机或笔记本电脑的屏幕）上进行显示。

需要监视的数据通过一个软件通道进行流式传输，该通道负责根据显示器的配置对其进行解释。由于是图形终端，除了文本数据外，它还能处理多种图形格式。从广义上讲，打印文本对象和打印图形对象是相似的任务，并且可以针对计算机编程环境进行泛化。Julia 代码能够处理这些对象并根据其定义的属性进行解释。一个 Julia 函数能够处理多少种对象类型，仅仅取决于为该函数定义了多少个方法。（参见第 10 章。）

我们已经了解了内置函数 `write()` 的主要用法。函数 `write()` 对二进制流进行操作，文本表示形式会被原样写入。例如，字符 `a` 存储在一个字节中，并赋予一个由十六进制数 `0x61` 表示的 Unicode。要打印 `a`，可以使用 `write()` 方法：

```
julia> write(STDOUT,'a')
a1
julia> write(STDOUT,'a');
a
julia> write(STDOUT,0x61)
a
julia> write(STDOUT,0x61);
a1
```

值得注意的是，当在命令末尾使用分号 `;` 时，它会抑制打印字节数（此处为一个）。还有一些其他函数可以更结构化地处理文本对象：

*   `show()`
*   `print()`
*   `println()`
*   `display()`

### 12.6.1 show()

大多数显示函数，最终都会调用 `show()` 来将一个对象 `x` 写入给定的 I/O 流（通常是内存缓冲区），如果可能的话，会为其指定一个 MIME 类型。默认的 MIME 类型是纯文本。函数 `show()` 需要两个输入参数——I/O 流类型和数据。`show()` 函数可以处理多种文本数据，并使用其定义的 MIME 类型来表示它们。例如，一个复数必须根据其 MIME 类型进行相应显示，该 MIME 类型规定了以下顺序的文本信息：

1.  表示实部的数字
2.  一个空格
3.  符号 `+` 或 `-`（根据所定义的数字情况而定）
4.  一个空格
5.  表示虚部的数字
6.  紧跟在虚部数字后面的字母 `im`

让我们使用 `show()` 函数打印一些数据类型。在每种情况下，数据类型都定义在变量 `a` 中，该变量作为第二个参数传递给 `show()`，因为第一个参数是 `STDOUT`，它默认设置为图形终端。在当前情况下，这就是 Julia 终端：

```
julia> show(STDOUT,'a')
'a'
julia> show(STDOUT,'1')
'1'
julia> a = "sandeep"
"sandeep"
julia> a = "sandeep nagar"
"sandeep nagar"
julia> show(STDOUT,a)
"sandeep nagar"
julia> show(STDOUT,a)
"sandeep"
julia> a = complex(2,3)
2 + 3im
julia> show(STDOUT,a)
2 + 3im
julia> a = 2//3
2//3
julia> show(STDOUT,a)
2//3
```

类似地，字符的 MIME 类型规定在字符周围使用单引号，而字符串的 MIME 类型则规定在定义字符串的字符组周围使用双引号。特殊字符（如空格）不会被显示，而是根据其行为进行解释并相应显示。同样地，有理数在分子和分母之间使用符号 `//` 进行打印。

`IOContext` 选项可用于传递来自 `show()` 函数的有关输出的上下文信息。它可以作为 `show()` 函数的第一个参数，指定输出格式属性。例如，`:compact` 指定小数值应以紧凑形式打印。对于数字，它们应以更少的位数打印。类似地，`:displaysize` 可用于设置文本数据显示的行数和列数，从而覆盖调用函数所规定的信息。当您使用具有固定行数和列数来处理字母数字数据的 LCD 显示器时，这会很有用。此处，显示大小可以设置为特定 LCD 显示单元的给定行数和列数。以类似的方式，可以通过对 `IOContext` 使用 `:limit` 选项来截断数据显示，即根据定义的值截断文本信息的显示。在所有情况下，值得注意的是，数据存储在内存中，这些函数仅影响其显示行为；它们不会更改内存中的数据。即使数据显示被截断，这些函数也不会出于存储目的而截断数据。

### 12.6.2 print()

内置函数 `print()` 以最小的格式将输入简单地打印到输出流（默认情况下设置为 Julia 终端）。如果它无法处理格式化的特性，则会调用 `show()` 函数。让我们看一些例子。当我们将变量 `a` 设置为数值 `1` 时，它会打印相同的值。但是，当我们将 `a` 设置为字符值 `'1'` 时，`print()` 仍然显示其数值。这种行为与 `show()` 函数不同：

```
julia> a = 1

julia> print(a)

julia> show(STDOUT,a)

julia> a = '1'
'1'
julia> print(a)

julia> show(STDOUT,a)
'1'
```

类似地，字符和字符串也可以像下面这样打印在终端上：

```
julia> a = 'z'
'z'
julia> print(a)
z
julia> show(STDOUT,a)
'z'
julia> a = "sandeep nagar"
"sandeep nagar"
julia> print(a)
sandeep nagar
julia> show(STDOUT,a)
"sandeep nagar"
```

对于其他类型的数据，如复数、有理数和包含特殊字符的字符串，可以使用 `print()` 函数。当需要特殊考虑显示格式时，它会调用 `show()` 函数。因此，`print()` 和 `show()` 的输出是等价的：

```
julia> a = complex(2,3)
2 + 3im
julia> print(a)
2 + 3im
julia> show(STDOUT,a)
2 + 3im
julia> a = 2//3
2//3
julia> print(a)
2//3
julia> show(STDOUT,a)
2//3
julia> a = "sandeep@nagar"
"sandeep@nagar"
julia> print(a)
sandeep@nagar
julia> show(STDOUT,a)
"sandeep@nagar"
```

如前所述，可以使用 `$` 将变量的值打印在字符串中。`print()` 和 `show()` 的输出明显不同，因为数值在 `show()` 函数中显示为字符串，因为第二个参数被作为字符串数据类型提供：

```
julia> a = 2

julia> print("$a")

julia> show(STDOUT,"$a")
"2"
```

### 12.6.3 println()

函数 `println()` 打印输入内容并加上一个换行符。这允许用户在输入时无需定义换行符，并且在每次调用 `print()` 函数的新实例时，输出始终在新的一行中显示：

```
julia> a = 1

julia> print(a)

julia> println(a)

julia> a = 'z'
'z'
julia> print(a)
z
julia> println(a)
z
julia> a = "sandeep@nagar"
"sandeep@nagar"
julia> print(a)
sandeep@nagar
julia> println(a)
sandeep@nagar
julia>
```

值得注意的是，在每种情况下，尽管两个函数的输出看起来相似，但 `println()` 在打印输入后会插入一个换行符。这一点可以通过以下事实观察到：在使用 `println()` 输出后，Julia 提示符出现在再下一行，而在使用 `print()` 时，它出现在下一行。



#### 12.6.4 `display()`

内置函数`display()`使用显示堆栈中最顶层的适用显示器来显示输入，通常会为`x`使用支持的最丰富的多媒体输出，并以纯文本`STDOUT`输出作为回退。当`display(d, x)`变体尝试仅在给定的显示器`d`上显示`x`时，可以选择这种方式。如果`d`无法显示给定类型的对象，它会`throws`一个`MethodError`。显示单元可以连接到安装了 Julia 的机器上。每个显示单元在显示堆栈中都有一个位置作为内存位置。因此，它们可以根据需要被调用。默认情况下，显示器是 Julia 控制台：

```
julia> a = 1

julia> display(a)

julia> a = '1'
'1'
julia> display(a)
'1'
julia> a = "1"
"1"
julia> display(a)
"1"
julia> a = "sandeep@nagar"
"sandeep@nagar"
julia> display(a)
"sandeep@nagar"
```

## 12.7 不同的显示单元

第 12.6 节仅集中在文本数据上，但可能有许多种对象需要在多种显示单元上显示。像数据表、图形、2D 和 3D 绘图、地图、照片和电影等对象由各种显示单元处理。现代计算机配备了多媒体控制台，这些控制台具有适当的硬件和软件来流式传输和解释多媒体数据对象。这些对象通常被定义为一个特定格式的文件或文件集。为了处理这些类型的数据对象，Julia 必须能够解释它们以进行输入，并以正确的格式生成它们以进行输出。但即使这样做了，流也必须从正确的输入和/或输出设备（该设备能够处理特定类型的数据）输入和/或输出。因此，了解显示单元至关重要。

除了多媒体控制台，你还可以有单色显示单元，它们不输出颜色，数据使用简单的黑白屏幕。其中一些无法处理图形对象。在某些情况下，图形是使用文本信息创建的。例如，可以使用`–`符号制作线条，圆圈可以用`o`或`0`符号表示。

LCD 屏幕有多种格式，从色彩缤纷的大型广告牌到 LCD 电视，再到仅一行八个字符的简单 LCD 单元。无论连接到机器的 LCD 单元是什么类型，其连接端口、显示驱动程序的内存位置等，都必须预先被 Julia 知道，以便它们能够用于与 Julia 代码通信。LED 屏幕遵循与 LCD 屏幕相同的模式。

3D 打印机是最新型的显示单元，可以打印 3D 对象进行展示。它们通过简单的 USB 端口或专用硬件连接到机器。必须正确安装将数据从机器解释到 3D 打印机的驱动程序，然后可以编写 Julia 代码向 3D 打印机发送命令以获取用于打印的流式数据。数据本质上采用机器码的形式，包含关于控制打印机的电机和耗材进给机制（针对基于耗材的 3D 打印机）的信息。

列出所有类型的显示单元及其功能超出了本书的范围，但我们提供了有关显示单元的基本原理的一般性描述。本节的主要收获是，大多数情况下，文件在 Julia 代码和机器的操作系统之间交换，以便与从中流式输入或输出数据的本地或远程机器进行交互。因此，研究 Julia 如何处理文件以实现此目的非常重要。

## 12.8 文件 I/O

处理文件是计算过程的重要组成部分。Julia 提供了许多功能来执行此操作。文件是一组符号，作为一个单元以特定格式组合在一起。文件存在多种格式，因此，任何享有文件处理能力的编程语言都必须提供处理多种文件格式以及根据需要轻松打开、创建、编辑和删除文件的功能。

UNIX 及类似系统将所有计算资源视为文件，其中包括计算机的外设，甚至包括键盘。读取按键输入到程序中的值仍然是任何编程语言的关键功能。我们已经了解到，键盘被设置为全局变量`STDIN`的默认值。我们还了解到，可以通过函数`read()`和`readline()`来读取按键。函数`read()`也可以用来读取文件，但每个文件必须先打开，操作完成后必须关闭，以便它可以再次被打开。为了执行这些操作，存在`open()`和`close()`操作。

在执行这些操作之前，了解文件位置（其路径）、允许用户拥有何种权限（`read`、`write`和/或`execute`权限），以及用户是否有权限创建、编辑和删除文件非常重要。假设读者已具备这些概念的基本知识，因为这里不会详细讨论。要学习这些内容，您可以参考参考文献编号[[1]](#A458482_1_En_12_Chapter.html#Par110)。在使用 Julia 编程时，了解处理文件的 Linux 命令[[2]](#A458482_1_En_12_Chapter.html#Par111)也很有用。



### 12.8.1 `open()`, `close()`, 和 `read()`

`open()`函数接受一个文件名并返回一个`IOStream`对象，可用于从文件读取/写入数据。要处理文件，我们需要创建或打开一个现有文件。函数`open()`需要一个包含文件路径的字符串。如果该名称的文件不存在，则可以在第二个参数指定为`"w"`（用于写入文件）时创建它。请注意，以下代码在 MacOS 上测试过，并且我相信它在所有基于 Unix 的操作系统上都能统一工作。

以下 Julia 代码将在`\tmp`目录中写入一个名为`t.txt`的文件，并赋予写入权限。然后，它会创建一个变量`f`，该变量引用该文件对象。接着使用`f`向该文件对象写入内容。字符串`"A, B, C, D\n"`被送入此文本文件对象：

```julia
julia> open("/tmp/t.txt", "w") do f
write(f, "A, B, C, D\n")
end
```

需要注意，上述代码在打开文件后并未关闭它。而以下代码执行了相同的操作，但强调了文件使用的正确做法。必须在执行所需操作后关闭文件，以便在同一会话或不同会话中重新打开它：

```julia
julia> open("/tmp/t.txt", "w") do f
write(f, "A, B, C, D\n")
close(f)
end
```

打开 Unix/Linux 控制台并检查`tmp`目录的内容，可以验证该文件已由用户（即当前登录的用户）创建：

```
$ cd /tmp
$ ls
t.txt
$ head t.txt
A, B, C, D
```

命令`ls -l`将让你了解该文件分配了哪些权限。根据用户类型，默认可能已分配读取、写入和/或执行权限，但你可以使用 Linux 命令`chmod`更改这些分配。

也可以创建包含多行多个数据点的文件。假设你想创建一个包含五行随机数的文件，随机数位于定义的字符串中：

```julia
julia> open("/tmp/t.txt", "w") do f
for i in 1:5
random_number = rand()
write(f, "random number is $random_number \n")
end
end
```

这将修改已存在的`t.txt`文件，使其包含所需数据。假设你想每行写入两个随机数。为了视觉清晰和数据完整性，它们必须用一个符号分隔。执行此任务的符号称为分隔符。一个非常常用的分隔符是逗号（`,`）。让我们修改之前的代码，在每行的两个随机数之间包含一个逗号：

```julia
julia> open("/tmp/t.txt", "w") do f
for i in 1:5
num1 = rand()
num2 = rand()
write(f, "num1= $num1,num2=$num2 \n")
end
end
```

除了打印格式化字符串外，相同的结果也可以通过使用`writedlm()`函数实现，该函数将定义的分隔符符号作为参数之一进行写入：

```julia
julia> writedlm("/tmp/test.txt", rand(5,2), ", ")
```

### 12.8.2 数组的读写

函数`writedlm()`和`readdlm()`可用于将数组写入文件，并从文件内容创建数组，如下：

```julia
julia> random_num = rand(2,3)
2x3 Array{Float64,2}:
0.742948  0.346532  0.385416
0.567728  0.345581  0.553949
julia> writedlm("/tmp/t.txt",random_num)
```

这会修改（如果`t.txt`在`\\tmp`目录中不存在则创建）文件`t.txt`，使其包含由变量名`random_num`引用的数组内容。值得注意的是，分隔符可以是任何符号或符号组。

正如使用`writedlm()`函数将数组写入文件一样，`readdlm()`将文件读入数组：

```julia
julia> read_file = readdlm("/tmp/t.txt")
2x3 Array{Float64,2}:
0.742948  0.346532  0.385416
0.567728  0.345581  0.553949
julia> read_file
2x3 Array{Float64,2}:
0.742948  0.346532  0.385416
0.567728  0.345581  0.553949
```

当未定义分隔符时，默认使用空格，因为同一行的数组条目由空格分隔。对于`readdlm()`函数，假定列由一个或多个空格分隔。行尾分隔符采用`\n`。此外，如果整个数据是数值型的，则生成的数组也是数值型的。如果某些元素无法解析为数字，则会返回一个由数字和字符串组成的异构数组：

```julia
julia> writedlm("/tmp/t.txt",random_num,",")
julia> random_num = rand(2,3)
2x3 Array{Float64,2}:
0.899567  0.195336  0.330642
0.951103  0.915678  0.986845
julia> writedlm("/tmp/t.txt",random_num,",")
julia> read_file = readdlm("/tmp/t.txt")
```

当在前一种情况中未对`readdlm()`使用分隔符参数时，每个行元素都会变为字符串，因此`read_file`中存储的是一个 2×1 的数组。文件`t.txt`中的实际分隔符是`，`(逗号)：

```julia
julia> read_file = readdlm("/tmp/t.txt",',')
2x3 Array{Float64,2}:
0.899567  0.195336  0.330642
0.951103  0.915678  0.986845
```

数据类型也可以作为第三个参数指定，以确保所有元素的数据类型统一：

```julia
julia> read_file = readdlm("/tmp/t.txt",',',Float64)
2x3 Array{Float64,2}:
0.899567  0.195336  0.330642
0.951103  0.915678  0.986845
julia> eltype(read_file)
Float64
```

由于数据点通常由逗号分隔，因此处理 csv（逗号分隔值）文件时使用这些函数的 csv 版本`writecsv()`和`readcsv()`。

```julia
julia> read_file = readcsv("/tmp/t.txt")
2x3 Array{Float64,2}:
0.899567  0.195336  0.330642
0.951103  0.915678  0.986845
julia> read_file
2x3 Array{Float64,2}:
0.899567  0.195336  0.330642
0.951103  0.915678  0.986845
```

## 12.9 总结

在本章中，我们定义了数据 I/O 流的概念，并探讨了 Julia 以灵活方式处理各种流的能力。数据流可以从多种输入设备输入，包括键盘、麦克风和摄像机。输入设备的行为可以在 Julia 中对象化，然后使用方法定义。大多数输出设备（如 Julia 终端、图形终端、打印机、绘图仪、LCD 面板、LED 面板，甚至 3D 打印机）的行为是相似的。数值计算涉及在文件中定义任务。Julia 将文件中的数据视为 I/O 流的能力定义了一种抽象层，使开发人员能够轻松编写代码。文件 I/O 操作构成了数值计算的支柱。因此，本章对于认真考虑使用 Julia 进行数据处理的用户来说非常重要。

