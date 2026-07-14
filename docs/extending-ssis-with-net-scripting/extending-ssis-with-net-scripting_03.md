# 3. .NET 基础

电子补充材料 本章的在线版本 (doi:[10.1007/978-1-4842-0638-6_3](http://dx.doi.org/10.1007/978-1-4842-0638-6_3)) 包含补充材料，可供授权用户使用。

如果您不熟悉 .NET Framework，或者之前没有编写过 VB.NET 或 C# 代码，那么本章正适合您。在本章中，您将学习 .NET 开发的基础知识和基本原理。您将了解 .NET 中的数据类型、变量以及编写代码的结构；您将通过 C# 和 VB.NET 两种语言来学习。作为本书最早章节之一，本章的主要目的是帮助您理解 .NET 脚本编写。您将在后续章节中提升知识水平，因为随着您继续阅读本书，您将看到更复杂的代码行，这些代码需要对 .NET 基础知识有很好的理解。如果您熟悉 .NET，或者您具有 C#/VB.NET 开发背景，那么可以直接跳过本章。

## 引言

.NET Framework 是一个由微软开发的专有且部分开源的框架（.NET Core 是开源的，包括运行时和框架库）；它主要运行在微软 Windows 平台上。它的两个主要组件是庞大的框架类库（`FCL`）和公共语言运行时（`CLR`），后者提供了语言互操作性，这意味着每种语言都可以使用其他语言编写的代码。`CLR` 是一个托管的软件环境，允许使用 .NET Framework 编写的程序和应用程序执行。`CLR` 是一种应用程序虚拟机，负责处理安全性、内存管理和异常处理。`FCL` 和 `CLR` 共同构成了 .NET Framework。

微软为 .NET Framework 提供了一个名为 Visual Studio 的集成开发环境。

有关 .NET Framework 基本概念的更多信息，可以在 MSDN 上找到，网址为 [`https://msdn.microsoft.com/en-us/library/ff361664(v=vs.110).aspx`](https://msdn.microsoft.com/en-us/library/ff361664(v=vs.110).aspx)。

## .NET 数据类型与数据流数据类型

虽然它们之间存在相似之处，但 .NET 中使用的数据类型与 SSIS 中使用的数据类型之间也存在若干根本性差异。SSIS 中使用的数据类型是数据流类型，这些类型由管道缓冲区支持，并通过专用类型系统公开。

该类型系统不同于 SSIS 变量或数据提供程序（如 OLE DB 和 ADO.NET）所使用的类型系统。表 3-1 展示了 .NET 数据类型与 SSIS 管道缓冲区类型之间的对比。

**表 3-1.** .NET 与 SSIS 管道中的数据类型

| .NET 类型 | SSIS 管道缓冲区类型 |
| --- | --- |
| `Int64` | `DT_I8` |
| `Byte 数组` | `DT_BYTES` |
| `Boolean` | `DT_BOOL` |
| `String` | `DT_STR` |
| `DateTime` | `DT_DBDATE` |
| `DateTime` | `DT_DBTIMESTAMP` |
| `DateTime` | `DT_DBTIMESTAMP2` |
| `DateTimeOffset` | `DT_DBTIMESTAMPOFFSET` |
| SQL Server 2012 之前：`Object`* / 从 SQL Server 2012 起：`Decimal` | `DT_NUMERIC` |
| `Double` | `DT_R8` |
| `Object`* | `DT_IMAGE` |
| `Int32` | `DT_I4` |
| `String` | `DT_WSTR` |
| `String` | `DT_NTEXT` |
| `Single` | `DT_R4` |
| `Int16` | `DT_I2` |
| `TimeSpan` | `DT_DBTIME2` |
| `Byte` | `DT_UI1` |
| `GUID`** | `DT_GUID` |

*.NET Framework 中的所有数据类型都派生自 `Object` 类型。因此，所有数据类型都可以转换为 `Object`。这意味着，如果您在 SSIS 中有一种数据类型缺少合适的对应类型，您始终可以将其转换为 `Object` 类型。**即使 `GUID` 是 .NET 原生类型，它也不是可用的 SSIS 变量类型系统的一部分。要创建 `GUID`，您必须借助脚本任务或脚本组件。如果要将 `GUID` 分配给变量，则必须先将其转换为字符串；例如：

```csharp
Dts.Variable["MyGUID"].Value = "{" + Guid.NewGuid().ToString() + "}";
```

### 数据类型

元素的数据类型指明了该元素可以保存哪种数据以及数据是如何存储的。所有可以作为表达式评估的一部分或可以存储到计算机内存中的值都具有数据类型。这些元素可以是变量、常量、枚举、参数、自变量和属性。过程返回值也具有数据类型。在 .NET Framework 中，必须事先声明数据类型。语法因您使用 C# 还是 VB.NET 作为编程语言而异。表 3-2 展示了两种语言中元素的声明方式。

**表 3-2.** .NET 数据类型声明

| 编程元素 | C# 声明 | VB.NET 声明 |
| --- | --- | --- |
| 变量 | `Double Amount; String Name;` | `Dim Amount as Double` / `Dim Name as String` |
| 字面量 | `Double Amount = 10.5; String Name = "Regis";` | `Dim Amount as Double = 10.5` / `Dim Name as String = "Regis"` |
| 常量 | `const Double VAT = 18.6;` | `Const VAT as Double = 18.6` |
| 枚举 | `enum Colors { blue, green }` | `Enum Colors` / `Green` / `Blue` / `End Enum` |
| 属性 | `public int MyProperty { get; set; }` | `Public Property MyProperty() As Integer` / `Get Return m_MyProperty End Get` / `Set m_MyProperty = Value End Set` / `End Property` / `Private m_MyProperty As Integer` |
| 参数 | `public void myMethod(string parameter1)` | `Sub myMethod(ByVal parameter1 As String)` |
| 自变量 | `newString = inputArgument.Substring(0,2);` | `newString = Left(inputArgument, 2)` |
| 返回值 | `public string myNewMethod(String parameter)` | `Function myNewMethod(ByVal parameter as String) As String` |

### 变量

变量是一个存储位置，包含一个已知或未知数量的信息，称为值。在 VB.NET 中，变量是维度化的，因此得名 `Dim`。变量可以具有指示其作用域的访问修饰符。在类级别，具有 `public` 作用域的变量对该类的所有实例以及其他对象（包括其他类）都是可见的。如果变量是 `private` 的，则仅在该类内部可用。相同的规则适用于属性：`public` 属性对所有其他对象可见。在方法或函数内部声明的变量的作用域仅限于该函数或方法，不能在函数作用域之外使用。此外，如果在函数的 `if` 或循环结构内部声明变量，则该变量的作用域仅限于该 `if` 或循环结构。

### 运算符

.NET Framework 中有不同类别的运算符，如表 3-3 所示。

**表 3-3.** 运算符类别

| 类别 | 示例 |
| --- | --- |
| 主要 | `x.y`、`typeof`、`x++`、`x--` |
| 一元 | `!x`、`++x`、`--x` |
| 乘法 | `x*y`、`x/y`、`x%y` |
| 加法 | `x+y`、`x-y` |
| 移位 | `x>>y`、`x<<y` |
| 关系 | `x>y`、`x<y`、`x<=y`、`x>=y` |
| 类型测试 | `is`、`as` |
| 相等 | `x==y`、`x!=y` |
| 逻辑与 | `x&y` |
| 逻辑异或 | `x^y` |
| 逻辑或 | `x|y` |
| 条件与 | `x&&y` |
| 条件或 | `x||y` |
| 空合并 | `x??y` |

| 条件 | `?:` |

| 赋值和 Lambda 表达式 | `x = y`、`x += y`、`x -= y`、`x *= y`、`x /= y`、`x %= y`、`x &= y`、`x |= y`、`x ^= y`、`x <<= y`、`x >>= y`、`=>` |

有关运算符及其用法的更多信息，请访问 MSDN 网站 [`msdn.microsoft.com/zh-cn/library/6a71f45d.aspx`](https://msdn.microsoft.com/zh-cn/library/6a71f45d.aspx)；有关 VB.NET 的更多信息，请访问 [`msdn.microsoft.com/zh-cn/library/a1w3te48.aspx`](https://msdn.microsoft.com/zh-cn/library/a1w3te48.aspx)。

## Using/Import、类和命名空间

为了能够引用类型名称而无需使用其命名空间对其进行限定，您需要使用 `using`（C#）或 `Imports`（VB.NET）关键字。这通常在文件级别完成，也可以在命名空间、类等内部完成。每个源文件可以包含任意数量的 `Import` 或 `using` 语句。

注意：`Import` 和 `using` 都不会使其他项目或程序集中的元素对您的项目可用。您仍然需要为项目或程序集设置引用。`using` 或 `Imports` 关键字仅消除了对项目中已可用的名称进行限定的需要。

以下是 C# 中 `using` 语句的示例：

```csharp
using System;
using System.Collections.Generic;
using System.Data;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

在 VB.NET 中，等效的是 `Import` 语句；例如：

```vb
Imports System
Imports System.Collections.Generic
Imports System.Data
Imports System.Linq
Imports System.Text
Imports System.Threading.Tasks
```

### 数组

C# 和 VB.NET 中的数组是零索引的，这意味着索引从零开始。在 C# 中声明数组时，方括号位于类型之后。如下所示，注意 C# 中的标识符：

```csharp
int[] myArray;
```

或者在 VB.NET 中编写如下：

```vb
Dim myArray as int[]
```

C# 或 VB.NET 中的数组与其他语言中的数组的另一个区别是，数组的大小不是其类型的一部分。这样，就可以声明一个数组并为其分配对象，而不管数组长度如何。

```csharp
int[] myArray;          // 声明一个任意大小的 int 数组
myNumberedArray = new int[10]; // myNumberedArray 是一个包含 10 个元素的数组
myNumberedArray = new int[15]; // myNumberedArray 现在是一个包含 15 个元素的数组
```

#### 数组的不同类型

在 C# 和 VB.NET 中，你可以声明不同类型的数组：一维数组、多维数组和数组的数组。表 3-4 列出了这些类型。

**表 3-4.** 数组的类型

| 数组类型 | 声明示例 |
| --- | --- |
| 一维数组 | `int[ ] myArray;` |
| 多维数组 | `String[,] myArray;` |
| 数组的数组 | `Byte[ ][ ] matrix;` |

如表 3-1 所示，声明数组并不会实际创建它们。在 C# 和 VB.NET 中，数组是对象，必须像其他任何对象一样进行实例化。表 3-5 提供了一些示例。

**表 3-5.** 实例化数组

| 数组类型 | 实例化示例 |
| --- | --- |
| 一维数组 | `int[] myArray = new int[5];` |
| 多维数组 | `String[,] myArray = new string[2,3];` |
| 数组的数组 | `Byte[][] matrix = new byte[5][];` |
| | `for(int I = 0; I < matrix.length; i++)` |
| | `{` |
| | `matrix[x] = new byte[4];` |
| | `}` |

也可以声明更大的数组；例如，一个三维数组可以在 C# 中编写如下：

`int[,,] 3DArray = new int[3,4,5];`

而在 VB.NET 中则如下所示：

`Dim anArray As Integer(,,) = New Integer(2, 3, 4) {}`

#### 初始化数组

初始化数组简单直接；在声明时通过将值放在花括号内即可完成。

`Int[] numbers = new int[]{1,2,3,4,5};`

这是 VB.NET 版本：

`Dim numbers As Int() = New Integer() {1, 2, 3, 4, 5}`

#### 访问数组成员

访问数组成员也很直接；与其他编程语言中的方式非常相似。以下 C# 代码声明了一个数组，并将值 12 赋给第五个元素。

```csharp
int[] numbers = new int[]{1,2,3,4,5,6,7,8,9};
numbers[4] = 12;
```

以下是用 VB.NET 编写的相同示例：

```vb
Dim numbers As Int() = New Integer() {1, 2, 3, 4, 5, 6, 7, 8, 9}
numbers(4) = 12
```

如前所述，数组都是派生自基类型 `System.Array` 的对象。因此，它们拥有属性和方法；例如，`length` 属性可以让你获取数组中的元素数量。

> **注意：** 数组索引从 0 开始，但其长度基于 1。

## 集合

集合包含可用于定义对象集合的接口和类。这些集合可以是泛型或非泛型的，并且可以容纳各种对象，例如列表、队列、数组、字典和哈希表。

### 列表

列表是动态的、强类型的对象集合，可以通过索引进行访问。列表具有帮助你搜索、排序和处理数据的方法。它非常适合通过索引访问的元素。以下 C# 示例创建了一个包含四个 `int` 类型元素的列表。

```csharp
List<int> myList = new List<int>();
myList.Add(1);
myList.Add(2);
myList.Add(3);
myList.Add(4);
```

> **注意：** 尖括号是 C# 中声明类型的一部分；它们不是运算符（小于号和大于号）。

用 VB.NET 编写的相同示例如下：

```vb
Dim myList As New List(Of Integer)()
myList.Add(1)
myList.Add(2)
myList.Add(3)
myList.Add(4)
```

### 泛型

前面的示例向列表中添加了原始类型，但你也可以将对象类型和引用类型作为元素添加进去。

列表是派生自 `System.Collection.Generic` 类的对象，因此它们拥有属性和方法，使其成为非常实用的类型。数组的元素数量通过其 `length` 属性获取，而列表的元素数量则通过其 `count` 属性获取。

泛型列表是强类型的，与非泛型列表相比，这通常能提供更好的安全性和性能；例如，`List<T>` 是非泛型 `ArrayList` 的泛型版本。

有关列表的更多信息，请访问 MSDN 网站 [`https://msdn.microsoft.com/en-us/library/6sh2ey19(v=vs.110).aspx`](https://msdn.microsoft.com/en-us/library/6sh2ey19(v=vs.110).aspx)。

## 循环

循环显然是一种非常有用的编程工具。通过循环，你可以将逻辑应用于集合、列表或数组中的每一个元素。

让我们从讨论 `while` 循环开始。

### while 循环

`while` 循环会一直执行，直到 `while` 条件计算结果为 false。因为 `while` 条件的测试是在循环体之前执行的，所以 `while` 循环可能执行零次或多次。

以下是 C# 中 `while` 循环的示例：

```csharp
int i = 0;
while(i++ < 10)
{
    // 执行某些操作
}
```

在 VB.NET 中，你可以编写相同的循环如下：

```vb
Dim index As Integer = 0
While index <= 10
    Debug.Write(index.ToString & " ")
    index += 1
End While
```

> **注意：** 如果你需要一个至少执行一次的循环，那么有一种称为 `do` 循环的结构，其在 [https://msdn.microsoft.com/en-us/library/370s1zax.aspx](https://msdn.microsoft.com/en-us/library/370s1zax.aspx) 中有详细解释。

.NET Framework 在 SSIS 中有两种类型的循环：`foreach` 循环和 `for` 循环。

### for 循环

`for` 循环通常用于遍历数组或列表。数组具有 `length` 属性，列表具有 `count` 属性；两者都可用于循环的初始化。以下示例包含一个包含三个元素的一维数组，以及一个包含五个元素的列表。你想要遍历它们所有的元素：

```
//实例化数组
int[] numberArray = new int[]{1,2,3};

//实例化列表
List<string> numberList = new List<string>();

//向列表添加元素
numberList.Add("a");
numberList.Add("b");
numberList.Add("c");
numberList.Add("d");
numberList.Add("e");

//遍历数组的元素
for(int i = 0; i < numberArray.Length;i++)
{
    //打印值
    Console.WriteLine(numberArray[i]);
}

//以相同方式遍历列表的元素
for(int i = 0; i < numberList.Count;i++)
{
    //打印值
    Console.WriteLine(numberList[i]);
}
```

前面的示例是用 C# 编写的。你可以通过编写以下 VB.NET 代码达到同样的效果：

```
'实例化数组
Dim numberArray As Integer() = New Integer() {1, 2, 3}

'实例化列表
Dim numberList As New List(Of String)()

'向列表添加元素
numberList.Add("a")
numberList.Add("b")
numberList.Add("c")
numberList.Add("d")
numberList.Add("e")

'遍历数组的元素
For i As Integer = 0 To numberArray.Length - 1
    '打印值
    Console.WriteLine(numberArray(i))
Next

'以相同方式遍历列表的元素
For i As Integer = 0 To numberList.Count - 1
    '打印值
    Console.WriteLine(numberList(i))
Next
```

在迭代过程中的任何时刻，你可以使用 `break` 关键字跳出循环，或者使用 `continue` 关键字进入下一次迭代。也可以使用以下关键字退出 `for` 循环：`goto`、`return`、`throw`。

> **注意：** 如果你想要反向遍历列表或数组，只需按如下方式递减计数：`for(int i = myArray.Length -1; i = 0; i--)`。

# `foreach` 循环

与 `for` 循环类似，`foreach` 循环会对数组或集合中的每个元素重复执行一条或多条语句。`foreach` 循环用于按照列表或数组提供的顺序执行这些语句，但语句不能修改列表或数组，因为迭代这些语句是为了避免副作用。

在迭代过程中的任何时候，您都可以使用 `break` 关键字跳出循环，或者使用 `continue` 关键字跳转到下一次迭代。也可以使用以下关键字退出 `foreach` 循环：`goto`、`return`、`throw`。

您要迭代的列表或集合需要满足的唯一条件是必须实现 `IEnumerable` 接口。泛型列表和集合正是如此。它们位于 `System.Collections.Generic` 命名空间中。

以下示例演示了如何使用 `foreach` 循环迭代数组的元素。

```csharp
int[] numbers = { 1, 2, 4, 6, 7, 9, -3, -1, 0 };

foreach (int i in numbers)
{
    Console.Write("{0} ", i);
}

// Output: 1 2 4 6 7 9 -3 -1 0
```

以下是 VB.NET 中相同的循环类型：

```vbnet
Dim numbers As Integer() = {1, 2, 4, 6, 7, 9, -3, -1, 0}

For Each i As Integer In numbers
    Console.Write("{0} ", i)
Next

' Output: 1 2 4 6 7 9 -3 -1 0
```

也可以使用 `foreach` 循环来迭代多维数组中的项，但使用嵌套的 `for` 循环可以更好地控制元素。

## 错误处理

设计良好的脚本必须处理错误和异常，以防止崩溃。以下是关于如何处理错误和引发异常的指南。

### `throw` 语句

`throw` 语句用于在程序执行过程中出现异常时发出信号。它用于引发异常。异常始终是派生自 `System.Exception` 类的对象；例如，在 C# 中如下所示：

```csharp
class exampleException : System.Exception{}

// ... do something
throw new exampleException();
```

在 VB.NET 中，引发异常的方式如下：

```vbnet
Private Class exampleException
    Inherits System.Exception
End Class

' ... do something
Throw New exampleException()
```

最终，异常派生自 `System.Exception` 类这一事实意味着，您可以根据情况创建自己的异常类并在代码中抛出它们。结果可能类似于以下 C# 代码片段：

```csharp
try
{
    // Do something and throw different type of exceptions.
}
catch (CustomException ce)
{
    ...
}
catch (AnotherCustomException ace)
{
    ...
}
catch (Exception ex)
{
    //the generic exception
}
```

在 VB.NET 中，它将如下所示：

```vbnet
Try
    ' Do something and throw different type of exceptions.
Catch ce As CustomException
    'do something
Catch ace As AnotherCustomException
    ' do somerhing
Catch ex As Exception
    'the generic exception
End Try
```

### `try-catch` 语句

通过使用 `try-catch`，您可以通过编程方式检查并处理可能发生的异常。`try-catch` 语句始终由一个 `try` 块后跟一个或多个 `catch` 子句组成，这些子句指定如何处理不同类型的异常。当捕获到异常时，CLR 会查找处理该异常的 `catch` 语句。如果执行的方法不包含 `catch` 语句，则错误会向上冒泡到调用此方法的方法，以此类推，直到 CLR 遇到可以使用的 `catch` 块。如果没有 `catch` 块，则程序或应用程序将返回一个未处理的错误异常。可以引发的异常有几种类型；对于更细粒度的异常处理，您可以在一个 `try-catch` 块中检查几种类型。当然，通过从基类 `System.Exception` 派生，您可以始终构建自己的异常类来处理适合您应用程序或程序的错误。

### `try-catch-finally` 语句

`try-catch-finally` 的一个常见用法是在 `try` 中获取资源，在 `catch` 中处理异常，并在 `finally` 中释放资源。通过使用 `finally`，您可以确保无论代码是成功执行还是引发了异常，`finally` 子句都会被执行。这是处理连接、阻塞 IO 资源以及您希望使用后释放的对象的好方法。

另一种实现此目的的方法（通常是更好的方法）是在 C# 中使用 `using(){}` 块，因为它可以确保资源被清理，只要这些资源实现了 `iDisposable` 接口。

> **注意**

> 在 VB.NET 中，可以过滤异常并构建条件性的 `catch` 子句。在 .NET Framework 4.6（随 SQL Server 2016 一起提供）之前，C# 中无法实现此功能。

## 总结

本章的目标是介绍在使用 C# 和 VB.NET 编写 SSIS 脚本时使用的数据类型和最常用的构造。可以想象，这个框架的内容远非一个章节所能涵盖。我们只是触及了皮毛。建议您从 [`www.microsoft.com/net`](http://www.microsoft.com/net) 开始，在 MSDN 上阅读更多关于 .NET Framework 及其能力和优势的内容。



# 第二部分

## 脚本任务



