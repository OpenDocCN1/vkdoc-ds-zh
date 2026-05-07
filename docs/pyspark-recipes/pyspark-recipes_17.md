# 创建 Lambda 函数

你想要创建一个 lambda 函数，它接受一个整数作为输入，如果输入是偶数则返回 `True`，否则返回 `False`。

### 解决方案

一个 lambda 函数也被称为匿名函数。lambda 函数是一个没有名字的函数。Python 中的 lambda 函数使用 `lambda` 关键字定义。

### 工作原理

让我们创建一个 lambda 函数，检查一个给定的数字是否为偶数：

```
>>> isEvenInteger = lambda ourNum : ourNum%2 == 0
```

我们的 lambda 函数是 `lambda ourNum : ourNum%2 == 0`。你可以看到我们使用了关键字 `lambda` 来定义它。在关键字 `lambda` 之后，我们写入了函数的参数。然后在冒号之后，我们有一个逻辑表达式。我们的逻辑表达式将检查输入的数字是否能被 2 整除。在 Python 中，函数是对象，所以我们的 lambda 函数也是一个对象。因此，我们可以将我们的对象放入一个变量中。这就是我们如何将 lambda 函数放入变量 `isEvenInteger` 的方法：

```
>>> isEvenInteger(4)
```

输出如下：

```
True
>>> isEvenInteger(5)
```

输出如下：

```
False
```

lambda 函数的缺点是只能编写一个表达式。该特定表达式的结果将被返回。Python 关键字 `return` 不需要用于返回表达式的值。lambda 函数不能扩展到超过一行。

