# `foreach` 循环

与 `for` 循环类似，`foreach` 循环会对数组或集合中的每个元素重复执行一条或多条语句。`foreach` 循环用于按照列表或数组提供的顺序执行这些语句，但语句不能修改列表或数组，因为迭代这些语句是为了避免副作用。

在迭代过程中的任何时候，您都可以使用 `break` 关键字跳出循环，或者使用 `continue` 关键字跳转到下一次迭代。也可以使用以下关键字退出 `foreach` 循环：`goto`、`return`、`throw`。

您要迭代的列表或集合需要满足的唯一条件是必须实现 `IEnumerable` 接口。泛型列表和集合正是如此。它们位于 `System.Collections.Generic` 命名空间中。

以下示例演示了如何使用 `foreach` 循环迭代数组的元素。

```
int[] numbers = { 1, 2, 4, 6, 7, 9, -3, -1, 0 };

foreach (int i in numbers)
{
    Console.Write("{0} ", i);
}

// Output: 1 2 4 6 7 9 -3 -1 0
```

以下是 VB.NET 中相同的循环类型：

```
Dim numbers As Integer() = {1, 2, 4, 6, 7, 9, -3, -1, 0}

For Each i As Integer In numbers
    Console.Write("{0} ", i)
Next

' Output: 1 2 4 6 7 9 -3 -1 0
```

也可以使用 `foreach` 循环来迭代多维数组中的项，但使用嵌套的 `for` 循环可以更好地控制元素。

### 错误处理

设计良好的脚本必须处理错误和异常，以防止崩溃。以下是关于如何处理错误和引发异常的指南。

### `throw` 语句

`throw` 语句用于在程序执行过程中出现异常时发出信号。它用于引发异常。异常始终是派生自 `System.Exception` 类的对象；例如，在 C# 中如下所示：

```
class exampleException : System.Exception{}

// ... do something
throw new exampleException();
```

在 VB.NET 中，引发异常的方式如下：

```
Private Class exampleException
    Inherits System.Exception
End Class

' ... do something
Throw New exampleException()
```

最终，异常派生自 `System.Exception` 类这一事实意味着，您可以根据情况创建自己的异常类并在代码中抛出它们。结果可能类似于以下 C# 代码片段：

```
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

```
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

### 总结

本章的目标是介绍在使用 C# 和 VB.NET 编写 SSIS 脚本时使用的数据类型和最常用的构造。可以想象，这个框架的内容远非一个章节所能涵盖。我们只是触及了皮毛。建议您从 [`www.microsoft.com/net`](http://www.microsoft.com/net) 开始，在 MSDN 上阅读更多关于 .NET Framework 及其能力和优势的内容。

