# 9. 面向对象编程

## 9.1 引言

定义对象的概念对人类来说非常自然，因为我们在日常生活中就与各种对象打交道。我们将对象理解为具有以下特征的事物：一组属性，以及一组相关的行为。婴儿很早就开始学习认识环境中的对象。例如，作为婴儿，我们了解到一个球（对象）有颜色、形状和大小（属性），并且它可以滚动、滑动和弹跳（行为）。20 世纪 70 年代初，施乐帕克研究中心的艾伦·凯致力于面向对象编程（OOP）[1] 的概念。在开发名为 `Smalltalk` [2] 的编程语言时，他运用了 OOP 的思想。他的核心思想基于将计算机编程应用于物理模拟。大多数人将现实世界理解为各种对象（具有属性和行为）相互交互。因此，在构建模拟世界时采用相同的理念是很自然的。所有基于 GUI 的系统都继承了凯在这些思想上的努力成果，而现在所有主流编程语言都严格遵循这些思想。

## 9.2 过程式编程 vs. 面向对象编程

在定义计算任务时，你可以定义一组过程，通过使用数据块并按照过程规定的方式连接它们来解决问题。这种强调过程设置而忽略数据类型及其不同使用模式的范式称为过程式编程。另一方面，面向对象编程则强调对象以及它们之间通过运算符（作用于对象，改变其值和其他属性）定义的关系，以解决计算任务。`Python` 是一种 OOP 语言。

## 9.3 对象

在 `Python` 的世界里，一切都是对象。但什么是对象呢？对象是一个抽象概念，用来表示可以执行计算的实体。就像物理对象一样，计算机对象也有一组属性和一组相关的行为。数字、字符串、图片、视频和文件都可以被视为对象。在数字中，你可以将对象进一步细分为其他对象，例如整数、浮点数和布尔数，或者它们的无序或有序集合。在字符串中，你可以有字符、单词、句子等等。在文件中，你可以有文本文件、媒体文件、数据文件、脚本文件等等。让我们探索一些常见的 `Python` 对象：

```
>>>a=10.0
>>>help(a)
Help on float object :

class float(object)
|   float(x) −> floating point number
|
|   Convert a string or number to a floating point number,
if possible.
.
.
.
>>>b=1+2j
>>>help(b)
Help on complex object:

class complex (object)
|  complex(real[, imag]) −> complex number
|
|  Create a complex number from a real part and an optional
imaginary part.
|  This is equivalent to (real + imag∗1j)  where imag defaults to 0.
.
.
.
>>>c=10
>>>help(c)
Help on int object:

class int(object)
|  int(x=0) −> int or long
|  int(x, base=10) −> int or long
|
|  Convert a number or string to an integer, or return 0 if        no arguments
|  are given.   If x is floating point, the conversion        truncates towards zero.
|  If x is outside the integer range, the function returns a
long instead.
|
|  If x is not a number or if base is given, then x must       be a string or
|  Unicode object representing an integer literal in the given
base. The
|  literal can be preceded by '+' or '−' and be surrounded       by whitespace.
|  The base defaults to 10\.  Valid bases are 0 and 2−36\.       Base 0 means t o
|  interpret the base from the string as an integer literal.
|>>> int('0b100', base=0)
|  4
.
.
.
```

有趣的是，`Python` 将存储在变量 `a`、`b` 和 `c` 中的三个数字归类为不同的对象类。帮助字符串详细说明了它们的用法。任何需要计算的事物都可以被想象为一个称为对象的实体，它拥有一组属性和一组相关的行为。属性集存储在实例变量中，其行为由称为方法的一组函数来探查。

命令 `dir()` 会列出与该对象关联的所有方法和属性：

```
>>> a=1
>>> dir(a)
['__abs__', '__add__', '__and__', '__bool__', '__ceil__',    '__class__', '__delattr__', '__dir__', '__divmod__',     '__doc__', '__eq__', '__float__', '__floor__',     '__floordiv__', '__format__', '__ge__',          '__getattribute__', '__getnewargs__', '__gt__',          '__hash__', '__index__', '__init__',          '__init_subclass__', '__int__', '__invert__', '__le__',          '__lshift__', '__lt__', '__mod__', '__mul__', '__ne__',          '__neg__', '__new__', '__or__', '__pos__', '__pow__',          '__radd__', '__rand__', '__rdivmod__', '__reduce__',          '__reduce_ex__', '__repr__', '__rfloordiv__',          '__rlshift__', '__rmod__', '__rmul__', '__ror__',          '__round__', '__rpow__', '__rrshift__', '__rshift__',          '__rsub__', '__rtruediv__', '__rxor__', '__setattr__',          '__sizeof__', '__str__', '__sub__', '__subclasshook__',          '__truediv__', '__trunc__', '__xor__', 'bit_length',          'conjugate', 'denominator', 'from_bytes', 'imag',          'numerator', 'real', 'to_bytes']

>>> a.__abs__() # 绝对值
1
>>> a.__neg__() # 负值
-1
>>> a.__float__() # 浮点值
1.0
>>> a.bit_length() # 以二进制表示自身所需的位数
1
```

对象可以通过 `for operator` 使用属性进行操作。让我们以上一个案例为例：

*   `a.__abs__()` 定义了整数对象的绝对值（即值 1）。

*   `a.__neg__()` 定义了整数对象的负值（即值 -1）。

*   `a.__float__()` 定义了整数对象的浮点值（值 1.0）。

*   `a.__hex__()` 定义了整数对象的十六进制值（值 0x1）。

*   `a.__oct__()` 定义了整数对象的八进制值（值 01）。

*   `a.bit_length` 返回以二进制表示一个整数所需的位数，不包括符号位和前导零。

接下来的章节将解释定义对象的方法及其用法。首先，我们需要理解，在计算机中，不同类型的数据必须根据其数学性质以及智能定义计算资源需求的方式，进行不同的处理。

## 9.4 类型

每个对象都有一个关联的类型。类型决定了内存存储需求以及可以对对象进行的计算操作。例如，`int`和`float`是两种不同的对象类型，因为`int`存储整数，而`float`存储浮点数。虽然`float`需要存储关于小数点前后有多少位数字的信息，但`int`对象则无需考虑这些。类似地，复数存储在另一种类型的对象中（恰当地命名为`complex`），因为它需要存储复数的两个部分：实部和虚部。因此，它们在计算机内存中的存储方式必然大不相同，并且在后续计算中的使用方式也截然不同。

对象的类型可以通过使用名为`type()`的内置函数来获取，该函数接受需要扫描其类型的对象。例如：

```
1  >>> a =1
2  >>> type(a)

4  >>> b=1.0
5  >>> type(b)

7  >>> c =2+3j
8  >>> type(c)

10  >>> d = 'd'
11  >>> type(d)

13  >>> e = 'addition'
14  >>> type(e)

16  >>> f = [1,2,3]
17  >>> type(f)

19  >>> g = 1,2,3
20  >>> type(g)

22  >>> import numpy as np
23  >>> a=np.array([1,2,3])
24  >>> type(a)

26  type(np.sin)
```

从这段练习代码中，很容易理解：第1行创建了一个整数类型（`int`）的对象，第4行创建了一个浮点数类型（`float`）的对象，第7行创建了一个复数类型（`complex`）的对象，第10行和第13行创建了字符串类型（`str`）的对象，第16行创建了列表类型（`list`）的对象，第19行创建了元组类型（`tuple`）的对象，第23行创建了NumPy数组类型（`numpy.ndarray`）的对象，而第26行创建了一个名为`numpy.ufunc`的NumPy函数类型的对象。

## 9.5 对象引用

Python对象被表示为对内存中某个对象的引用。引用是一个指向内存位置的值。这可以通过以下使用`id()`函数的例子来理解。`id()`函数返回一个唯一数字，代表输入变量存储的内存地址：

```
1  >>> a1 = 10
2  >>> id(a1)
3  4469844832
4  >>> a2=a1
5  >>> id(a2)
6  4469844832
7  >>> a3=20
8  >>> id(a3)
9  4469845152
10  >>> a3=a2
11  >>> id(a2)
12  4469844832
13  >>> id(a3)
14  4469844832
15  >>> a1
16  10
17  >>> a2
18  10
19  >>> a3
20  10
```

如Python代码所示，`a1`被赋值为10。用Python的语言来说，就是创建了一个整数对象（其内存位置的id为4469844832）。内置函数`id()`可用于探查内存位置的id。

当执行`a2=a1`语句时，我们发现相同的id被分配给了引用值`a1`和`a2`。接下来对此进行了验证。现在创建了另一个值为20的整数对象，并用引用值`a3`来引用它。探查其id后发现，它与`a1`和`a2`的id不同。因此，每次创建一个对象时，它都会被存储在不同的内存位置。换句话说，它被赋予了不同的id。

当一个对象被两个引用值引用时会发生什么？当我们发出命令`a3 = a2`时，`a3`和`a2`引用的是存储在`a2`中的同一个对象。而`a2`与`a1`共享引用，`a1`存储着一个值为10的整数对象。因此，现在所有三个引用值`a1`、`a2`和`a3`都引用同一个值为10的整数对象。

### 9.5.1 垃圾回收

在第7行创建的整数对象20发生了什么？这个对象被解引用了。因此，这个内存位置必须被释放；其状态应从“正在使用”变为“可重用”。这个过程称为垃圾回收，对于高效的内存管理非常重要。Python会自动执行此操作。

最原始的方法之一是维护一个引用计数。对于每个对象，都会维护一个指向该对象的引用总数。如果该计数降至0，则立即释放该对象。但现在使用了更高级的算法[3, 4]，以排除这种原始想法可能带来问题的情况。遗憾的是，讨论Python垃圾回收方式的细节超出了本书的范围。如果你对这个主题感兴趣，以下参考文献将有所帮助[5, 6, 7]。

### 9.5.2 复制与深拷贝

用两个引用值指向同一个对象，看起来像是在复制。之前的讨论中已经观察到，当用户对原始对象进行修改时，两个副本都会受到影响。在处理`list`对象时，这可能会成为一个问题：

```
1  >>> list1 = [1,2,3]
2  >>> list2 = list1
3  >>> list1
4  [1, 2, 3]
5  >>> list2
6  [1, 2, 3]
7  >>> list1[1]=-1
8  >>> list1
9  [1, -1, 3]
10  >>> list2
11  [1, -1, 3]
```

首先，我们创建了一个值为`[1,2,3]`的列表对象，其引用值为`list1`。接着，我们创建了另一个引用值`list2`，它与原始引用值`list1`都指向同一个原始列表对象（`[1,2,3]`）。当列表中的第二个元素被第7行代码修改后，由于这两个引用值都指向同一个对象，因此修改会同时反映在两者上。因此，`list2`看起来像是`list1`的一个副本，它忠实地跟随其原始对象。让我们探究一下，当列表的成员本身也是列表时，这种模式是否依然成立：

```
1  >>> list1 = [1,2,3]
2  >>> list2 = [list1,list1]
3  >>> list1
4  [1, 2, 3]
5  >>> list2
6  [[1, 2, 3], [1, 2, 3]]
7  >>> list3=list2
8  >>> list3
9  [[1, 2, 3], [1, 2, 3]]
10  >>> list2[1]=[-1,-2,-3]
11  >>> list1
12  [1, 2, 3]
13  >>> list2
14  [[1, 2, 3], [-1, -2, -3]]
15  >>> list3
16  [[1, 2, 3], [-1, -2, -3]]
```

由此可见，即使列表对象的成员本身也是列表对象，复制行为的工作方式与成员是其他对象时相同。

以下代码展示了使用`list`构造函数时会发生什么：

```
1  >>> list1 = list([1,2,3])
2  >>> list2 = list([list1,list1,list1])
3  >>> list2
4  [[1, 2, 3], [1, 2, 3], [1, 2, 3]]
5  >>> list3 = list(list2)
6  >>> list3
7  [[1, 2, 3], [1, 2, 3], [1, 2, 3]]
8  >>> list2[1]=list([-1,0,1])
9  >>> list2
10  [[1, 2, 3], [-1, 0, 1], [1, 2, 3]]
11  >>> list3
12  [[1, 2, 3], [1, 2, 3], [1, 2, 3]]
13  >>> id(list2)
14  4416356864
15  >>> id(list3)
16  4416356936
```

注意，引用值`list3`拥有不同的数据，因为它们现在指向两个不同的对象（通过它们的内存id值可以看出）。因此，在使用`list`构造函数时，原始列表的副本并没有忠实地跟随其原始对象。所以，`list3`被称为`list2`的`浅拷贝`。

有时，即使使用了`list`构造函数，我们也希望创建一个副本，并在各种变换下保持其不变。为此，名为`copy`的模块提供了两个方法：`copy.copy()`和`copy.deepcopy()`。根据Python文档[8]：

*   `浅拷贝`构造一个新的复合对象，然后（尽可能）将原始对象中找到的对象的引用插入其中。这是通过`copy`模块的`copy.copy()`方法执行的。

*   `深拷贝`构造一个新的复合对象，然后递归地将原始对象中找到的对象的副本插入其中。这是通过`copy`模块的`copy.deepcopy()`方法执行的。

```
## 9.6 类

到目前为止，我们只讨论了预定义的对象，并查看了它们的 `type` 和内存 `id`。通过使用一种称为类的特殊对象类型，我们可以创建其他对象。

类的概念也源自自然界。现实世界中的对象根据其属性和行为进行分类。一个对象仅仅是其类的一个实例。例如，男人和女人仅仅是智人这个类的实例。类似地，关键字 `class` 定义了一类对象，可以为该类定义各种实例。例如，一个数字类可以有整数、浮点数、复数、有理数和无理数等实例。一旦定义，该类的所有成员将共享数字共有的某些属性和行为，但它们可以根据差异进一步细分。该类的所有成员将与语言类（包含字符、单词、句子和段落）截然不同。现在，可以定义一个数字类的实例（如整数）和一个语言类的实例（如字符）用于计算。这些各自类的实例将拥有自己的属性和方法（其中一些与它们的类共享，一些是它们自己独有的）。这种面向对象的方法帮助我们将一个复杂系统分解成小的基本单元，以便于研究。

因此，类定义了一种新的抽象实体的行为，而对象只是该实体的一个特定实例。类和对象有助于以可管理的方式定义函数对数据的操作，无论数据和函数的性质如何，都可以以类似的方式进行管理。接口描述了一个对象的功能，而其实际实现则定义了如何执行这些功能。类具有构造函数（我们已经在 9.5.2 节中讨论过构造函数 `list()`），它规定了特定类型对象的创建。封装和继承描述了类与其超类的层次结构。这些概念将在后续章节中进一步阐述。

### 9.6.1 创建类

关键字 `class` 用于创建一个类。例如，一个名为 `int` 的内置类定义了用于存储整数的对象的行为。当一个变量与该类的一个对象关联时，它被称为该类的一个实例。例如，语句 `a=1` 创建了一个名为 `a` 的对象，它是 `int` 类的一个实例。另一方面，语句 `b=1.0` 创建了一个名为 `b` 的对象，它是 `float` 类的一个实例。根据存储在 `a` 和 `b` 中的对象类型，它们在使用时的行为会有显著差异。

关键字 `class` 用于创建一个对象。其语法定义规则如下：

```
class 类名:
    '''可选的类文档字符串'''
    语句
```

通常，为了便于使用，类名会以有意义的方式命名。同样，通常会将使用细节放在文档字符串中。该字符串可以通过以下命令访问：

```
类名.__doc__
```

属于特定对象（由类定义）的变量称为字段。这些字段可以通过类内部为此目的定义的函数进行探查。这样的函数称为方法。

### 9.6.2 类变量与类方法

`类变量`表示普遍适用于类内定义的所有对象的数据。它们在类定义中定义，并在类的作用域内有效。这意味着该类中定义的每个对象都可以访问它们。无需引用类即可访问这些变量。

另一方面，`实例变量`仅在类对象的实例中有效。因此，它是在类实例的定义中定义的（在定义实例对象行为的 `def` 块内）。

`类方法`与普通函数仅有一点不同：它们需要额外添加一个第一个参数。这个参数名需要添加到参数列表的开头。用户在调用方法时无需为此参数提供值。它由 Python 的主程序提供，该程序默认由 Python 解释器运行。这个特定的变量指向对象本身。因此，它被命名为 `self`。那些不接受任何参数的方法至少也有 `self` 这个参数。清单 9-1 将使这个概念更清晰。

```
#Python code to explain defining
#and using class

#A class named python_program is defined
class first_program:
    '''
    This program has two methods: hi and hi_again
    '''
    greeting = 'Hello Everybody\n' #Class variable definition

    #Now we define two instances of this class as "hi" and        "hi_again"
    def hi(self):
        greet = 'Hello World!\n'
        print(greet)
    def hi_again(self):
        greet_again = 'Hello again!\n'
        print(greet_again)

#Procedures to call the class

x = first_program() # Calling the class
print('The type of object storing class calling is:', type(x))
x.hi() #Calling an instance of "x" class
x.hi_again() # Calling another instance of "x" class
# Results of both instances depend on thier definitions

#Acessing class variable
#Class variable can be accessed from anywhere
print(first_program.greeting)

#Printing the documentation string
print(first_program.__doc__)
```

Listing 9-1 Ex–class.py

输出结果如下所示：

```
$python Ex-class.py
The type of object storing class calling is: 
Hello World!

Hello again!

Hello Everybody

This program has two methods: hi and hi_again
```

### 9.6.3 构造函数

类中的所有对象都只是该类的实例。当这个实例被初始化时，会调用一种特殊的函数。构造函数是在对象实例化时执行的函数。Python 总是将构造函数命名为 `__init()__`，因为它设置了对象的初始状态。它定义了该实例的初始状态和基本性质。它在类定义中定义，但用户可以省略此定义，因为每个类都会自动创建一个默认构造函数。它通常用于定义实例变量的初始值，如清单 9-2 所示。

```
>>> import copy
>>> a = [[1, 2, 3], [4, 5, 6]]
>>> b = list(a)
>>> a
[[1, 2, 3], [4, 5, 6]]
>>> b
[[1, 2, 3], [4, 5, 6]]
>>> a[0][1] = 10
>>> a
[[1, 10, 3], [4, 5, 6]]
>>> b   # 列表 b 也改变了 => 不是深拷贝。
[[1, 10, 3], [4, 5, 6]]
```

现在，让我们看看 `deepcopy` 是如何工作的：

```
>>> b = copy.deepcopy(a)
>>> a
[[1, 10, 3], [4, 5, 6]]
>>> b
[[1, 10, 3], [4, 5, 6]]
>>> a[0][1] = 9
>>> a
[[1, 9, 3], [4, 5, 6]]
>>> b    # 列表 b 没有改变 => 深拷贝
[[1, 10, 3], [4, 5, 6]]
```

如你所见，即使 `a` 被修改，它也能使 `b` 保持其值不变。
```

```markdown
# 代码 `Ex-class1.py` 的解释

这段 Python 代码展示了如何定义和使用一个 `library` 类来表示书籍信息。

## 代码示例

```
#Python code to show usage of __init__

# Defining a class "library"
# which stores information about book's name and price

#Defining class
class library:
    'The base class for all books'
    bookCount = 0 #Class variable setting count of books to zero

    def __init__(self, name, price):# Initializing the instance of class with name, price and updated book count
       self.name = name
       self.price = price
       library.bookCount += 1

    def displayCount(self):
      print("Total Employee %d",library.bookCount)

    def displayBook(self):
       print("Name : ", self.name,  ", Price: ", self.price)

#Creating objects

book1 = library("Introduction to python",100) # First instance of object "library"
print(type(book1))

book2 = library("Basic Python",150) # Second instance of object "library"
print(type(book2))

book3 = library("Intermediate Python",200) # Third instance of object "library"
print(type(book3))

book4 = library("Advanced Python",300) # Four instance of object "library"
print(type(book2))

# Accessing attributes

print("Details of first book:")
book1.displayBook()

print("Details of second book:")
book2.displayBook()

print("Details of third book:")
book3.displayBook()

print("Details of fourth book:")
book4.displayBook()

print("Total Number of books= %d" % library.bookCount)

# Probing the bult-in attributes

print("library.__doc__:", library.__doc__)
print("library.__name__:", library.__name__)
print("library.__module__:", library.__module__)
print("library.__bases__:", library.__bases__)
print("library.__dict__:", library.__dict__)
```

## 代码解析

- 第 7 行定义了一个名为 `library` 的类。该类旨在记录书名及其价格。

- 第 9 行定义了一个名为 `bookCount` 的类变量，并将其设置为零值。
  - 由于它是一个类变量，因此被所有实例共享。
  - 可以在类内部或外部通过 `library.bookCount` 访问它。

- 该类在第 11 行到第 14 行定义了一个构造函数，以及两个名为 `displayCount` 和 `displayBook` 的方法。
- 第 11–14 行定义了用于初始化所有实例的构造函数：
  - 它接受三个参数：`self`、`name` 和 `price`。
  - `self` 指向对象本身。
  - `name` 和 `price` 分别在第 12 行和第 13 行初始化。
  - 这些参数在调用类时是必需的，即 `library(name,price)`。
  - 此初始化信息被对象 `library` 的所有实例使用。

- 第 16–17 行定义了一个名为 `displayCount` 的方法，并输出由 `bookCount` 变量引用的值。
- 第 19–20 行定义了一个名为 `displayBook` 的方法，该方法提供书名和价格的信息。
- 第 21–33 行创建了对象 `library` 的单独实例。
  - 根据构造函数的定义，调用类时的两个输入参数必须是存储在变量 `name` 和 `price` 中的值。
- 第 39、44、48 和 52 行逐个调用对象，探查由方法 `displayBook()` 定义的属性。
- 第 50 行使用方法 `bookCount()` 显示书籍总数。

## 内置类属性

所有 Python 类都拥有某些可通过点运算符访问的内置属性。这些操作在第 54 行至第 58 行完成。

- `__doc__`
  - 返回类的文档字符串
  - 若未定义则返回 `None`

- `__dict__`
  - 返回包含类命名空间的字典
  - 该字典包含对象 `library` 的相关信息，具体包括：
    - `__module__`
    - 定义的方法（本例中为 `displayCount()` 和 `displayBook()`）
    - 定义的类变量（本例中为 `bookCount`）
    - `__doc__`
    - 构造函数 `__init__`

- `__name__`
  - 返回类的名称

- `__module__`
  - 返回定义该类的模块名称
  - 在交互模式下，该值为 `__main__`

- `__bases__`
  - 一个可能为空的元组，包含基类列表中按出现顺序排列的基类

## 代码输出

Python 代码 `class1.py` 的输出如下所示：

```
5  Details of first book:
6  Name:  Introduction to python , Price:  100
7  Details of second book:
8  Name:  Basic Python , Price:  150
9  Details of third book:
10  Name :  Intermediate Python , Price:  200
11  Details of fourth book:
12  Name :  Advanced Python , Price:  300
13  Total Number of books= 4
14  library.__doc__: The base class for all books
15  library.__name__: library
16  library.__module__: __main__
17  library.__bases__: (,)
18  library.__dict__: {'__module__': '__main__', '__doc__': 'The base class for all books', 'bookCount': 4, '__init__': , 'displayCount': , 'displayBook':, '__dict__': , '__weakref__': }
```

## 9.7 总结

在本章中，我们演示了如何使用 `class` 构造定义一种新类型的对象。由于 Python 将一切视为对象，它成为科学计算和模拟的首选之一。因其易于学习，用户可以很快开始进行严肃的科学计算。这一特性使其在教育工作者、学生和研究人员中非常受欢迎。创建类并构建自定义模块的能力，使得开发者能够日复一日地丰富 Python 生态系统。因此，可以说 Python 的面向对象特性及其开源生态系统使其成为最流行的编程语言之一，这并非夸大其词。
```