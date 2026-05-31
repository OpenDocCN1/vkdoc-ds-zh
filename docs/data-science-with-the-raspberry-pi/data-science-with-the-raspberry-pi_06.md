# for 循环

`for` 循环使用一个迭代变量运行，该变量在每次迭代时递增，直到变量到达循环所操作序列的末尾。在每次迭代中，取出序列中与迭代变量所指示位置相对应的项目，并执行循环中的语句。`for` 循环的语法如下：

```
for <variable> in <sequence>:
    <statements>
```

`range()` 函数在 `for` 循环中非常有用，因为它可以生成一个数字序列，可以使用 `for` 循环对其进行迭代。`range()` 函数的语法是 `range([start,] stop [,step])`，其中 `start` 表示序列的起始位置（如果未指定，则从零开始），`stop` 表示需要生成数字到的值（不包括该数字本身），`step` 表示生成的序列中每两个连续数字之间的差值。`start` 和 `step` 值是可选的。`range` 参数生成的值应始终为整数。清单 2-16 展示了一个用于逐个打印字符串中元素的 `for` 循环。

```python
x = "Hello"
for i in x:
    print(i)
# Output:
# H
# e
# l
# l
# o
```

**清单 2-16** for 循环操作

清单 2-17 展示了如何使用 `range()` 函数打印整数序列。

```python
for i in range(4):
    print(i)
# Output:
# 0
# 1
# 2
# 3
```

**清单 2-17** 使用 range 函数的 for 循环操作

## 异常处理

异常就是在执行过程中检测到的错误。当程序中发生异常时，执行会终止，从而中断程序的正常流程。通过异常处理，可以向用户提供有关错误的有意义信息，而不是系统生成的消息。异常可以是内置的或用户定义的。用户定义的异常是用户创建的自定义异常，可以使用 `try...except` 语句实现，如清单 2-18 所示。

```python
while True:
    try:
        n = int(input("Enter a number"))
        print("The number you entered is", n)
        break
    except ValueError:
        print("The number you entered is not the correct data type")
        print("Enter a different number")

# Output:
# Enter a number 5
# The number you have entered is 5
# Enter a number 3.6
# The number you entered is not the correct data type
# Enter a different number
```

**清单 2-18** 异常处理

在清单 2-18 中，当变量接收到不适当数据类型的值时，会发生 `ValueError` 异常。如果没有发生异常（即输入的数字是整数），则跳过 `except` 块，只执行 `try` 块。如果在输入不同数据类型的数字时发生异常，则跳过 `try` 块中的其余语句，执行 `except` 块，并将程序返回到 `try` 块。

## 函数

函数是 Python 编程中的基本构建块，当需要在程序中多次执行一组语句时可以使用函数。可以通过将这组语句分组并为其命名来创建函数，这样可以在程序的任何部分通过该名称调用这些语句，而无需重复整个代码块。因此，函数可以通过消除冗余代码来减小程序规模。函数可以是内置的或用户定义的。

Python 解释器有许多内置函数，我们已经见过其中一些，如 `print()`、`range()`、`len()` 等。另一方面，Python 允许用户定义自己的函数并根据需要使用它们。函数定义的语法如下：

```python
def function_name(parameter1, .... parameter n):
    statement(s)
```

函数名可以包含字母、数字或下划线，但不能以数字开头，也不应与关键字同名。让我们考虑一个简单的函数，它接收单个参数作为输入并计算其平方；请参见清单 2-19。

```python
def sq(a):
    b = a * a
    print(b)

sq(36)
# Output: 1296
```

**清单 2-19** 平方函数

让我们看一个稍微复杂的函数，它计算给定十进制数的二进制表示。

如清单 2-20 所示，计算十进制数二进制表示所需的五行代码可以通过用户定义的函数替换为一行。

```python
import math as mt

def dec2bin(a):
    b = ''
    while a != 0:
        b = b + str(a % 2)  # 连接操作
        a = mt.floor(a / 2)
    return b[::-1]  # 反转字符串 b

print(int(dec2bin(19)))
# Output: 10011
```

**清单 2-20** 十进制转二进制函数

