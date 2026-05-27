# 清空字典

`clear`方法移除字典中的所有键/值对，换句话说，它清空字典的内容，但不会从内存中移除变量。

```
#removing all the key/value pairs
numbers.clear()
```

输出：

```
{}
```

进一步阅读：了解更多关于字典的信息：[`https://docs.python.org/3/tutorial/datastructures.html#dictionaries`](https://docs.python.org/3/tutorial/datastructures.html%2523dictionaries)

## 集合

集合是一个容器，包含无序且无索引的元素。集合的主要特征是它是*唯一*元素的集合。请注意，如果在创建集合时添加重复元素，Python 不会抛出错误。但是，在对集合执行操作时，所有重复元素都会被忽略，只考虑不同的元素。

### 集合定义

与字典一样，集合使用花括号声明，并且元素无序。然而，字典中的值可以通过键作为索引访问，而集合中的值不能通过索引访问。

以下是集合定义的示例：

```
a={1,1,2}
a
```

输出：

```
{1, 2}
```

从输出中可以看出，集合定义中存在的重复值 1 被忽略。

### 集合操作

可用于集合的方法和函数在表 2-3 中说明。

进一步阅读：了解更多关于集合的信息：[`https://docs.python.org/3/tutorial/datastructures.html#sets`](https://docs.python.org/3/tutorial/datastructures.html%2523sets)

**表 2-3 集合操作**

| 操作 | 方法/函数 | 示例 |
| --- | --- | --- |
| 查找集合的长度 | `len`函数计算集合中元素的数量，仅考虑不同的值。 | `len(a)` |
| 集合迭代 | `for`循环可以遍历集合并打印其元素。 | `for x in a:` `print(x)` |
| 添加项或值 | 可以使用`add`方法向集合添加单个项。要添加多个值，使用`update`方法。 | `a.add(3)` `#or` `a.update([4,5])` |
| 移除项 | 可以使用`remove`或`discard`方法移除项。 | `a.remove(4)` `# Or` `a.discard(4)` `#注意：当我们尝试删除集合中不存在的元素时，discard 方法不会报错，而 remove 方法会引发 KeyError。` |

现在我们已经涵盖了 Python 语言的所有要点——我们在前一章学到的概念以及本章对各种容器及其方法的理解——我们需要决定使用哪种风格或编程范式。在各种编程范式中，包括过程式、函数式和面向对象编程，我们将在下一节讨论面向对象编程。

## 面向对象编程

面向对象编程（通常也称为“OOPS”）作为过程式编程的替代方案出现，过程式编程是传统的编程方法。

过程式编程涉及使用一系列步骤顺序执行程序。过程式编程的一个主要缺点是存在容易意外操作的全局变量。OOPS 相对于过程式编程提供了几个优势，包括代码重用能力、消除全局变量、防止对数据的未授权访问以及管理代码复杂性的能力。

Python 遵循面向对象范式。类和对象构成了面向对象编程的构建块。类提供蓝图或结构，而对象实现此结构。类使用`class`关键字定义。

例如，假设你有一个名为“Baby”的类，其属性包括婴儿的名字、性别和体重。这个类的方法（或在类内部定义的函数）可以是婴儿执行的动作，如微笑、哭泣和进食。实例/对象是类的实现，并拥有自己的一组属性和方法。在这个例子中，每个婴儿都有其独特的特征（数据）和行为（功能）。

一个类可以有一组属性或变量，这些变量可以是类变量或实例变量。类的所有实例共享类变量，而实例变量对每个实例是唯一的。

让我们通过以下示例了解如何在 Python 中定义类：

```
#example of a class
class Rectangle:
    sides=4
    def __init__(self,l,b):
        self.length=l
        self.breadth=b
    def area(self):
        print("Area:",self.length*self.breadth)
my_rectangle=Rectangle(4,5)
my_rectangle.area()
```

输出：

```
Area: 20
```

`class`关键字后面跟着类名和冒号。接下来，我们定义一个名为`sides`的类变量，并将其初始化为`4`。这个变量对该类的所有对象都是通用的。

之后，有一个构造函数用于设置或初始化变量。请注意构造函数的特殊语法——`def`关键字后面跟两个下划线，然后是`init`关键字，再跟两个下划线。

类中定义的任何方法的第一个参数都是`self`关键字，它指向类的一个实例。然后是初始化参数`l`和`b`，分别表示矩形的长度和宽度。这些值在创建对象时作为参数提供。实例变量`self.length`和`self.breadth`使用前面提到的参数进行初始化。接下来是另一个计算矩形面积的方法。请记住，在定义类的任何方法时，都需要将`self`作为参数添加。

类定义完成后，我们可以定义该类的一个实例，也称为对象。创建对象就像创建变量一样，给它一个名字并初始化它。`my_rectangle`是创建的对象/实例的名称，后面跟着`=`号。然后我们提到类的名称以及构造函数中用于初始化对象的参数。我们正在创建一个长度为`4`、宽度为`5`的矩形。然后我们调用`area`方法来计算面积，该方法计算并打印面积。

进一步阅读：在 Python 中了解更多关于类的信息：[`https://docs.python.org/3/tutorial/classes.html`](https://docs.python.org/3/tutorial/classes.html)

### 面向对象编程原则

面向对象编程的主要原则是封装、多态、数据抽象和继承。让我们逐一了解这些概念。

**封装**：封装指的是将数据（在类中定义的变量）与可以修改它的功能（方法）绑定在一起。封装还包括数据隐藏，因为类中定义的数据对于类外部定义的任何函数来说都是安全的，不会被篡改。一旦我们创建了类的对象，其变量只能通过与该对象关联的方法（或函数）来访问和修改。

让我们考虑以下示例：

代码：

```
class Circle():
    def __init__(self, r):
        self.radius = r
    def area(self):
        return 3.14 * self.radius * self.radius
c = Circle(5)
c.radius  # 访问实例变量的正确方式
```

这里，`Circle`类有一个实例变量`radius`和一个方法`area`。变量`radius`只能通过该类的对象访问，而不能通过其他任何方式访问，如下面的语句所示。

代码：

```
c.radius  # 访问实例变量的正确方式
radius    # 错误，会导致错误
```

**多态**：多态（一个接口，多种形式）提供了使用相同接口（方法或函数）而无需考虑数据类型的能力。

让我们通过`len`函数来理解多态原则。

代码：

```
# 使用 len 函数处理字符串
len("Hello")
```

输出：

```

```

代码：

```
# 使用 len 函数处理列表
len([1, 2, 3, 4])
```

输出：

```

```

代码：

```
# 使用 len 函数处理元组
len((1, 2, 3))
```

输出：

```

```

代码：

```
# 使用 len 函数处理字典
len({'a': 1, 'b': 2})
```

输出：

```

```

`len`函数用于计算其参数的长度，它可以接受任何类型的参数。我们将字符串、列表、元组和字典作为参数传递给该函数，函数返回了每个对象的长度。无需为每种数据类型编写单独的函数。

**继承**：继承指的是从其父类创建另一个类（称为子类）的能力。子类继承父类的一些属性和函数，但也可能拥有自己的功能和变量。

下面演示了 Python 中继承的一个示例。

```
### 继承
class Mother():
    def __init__(self, fname, sname):
        self.firstname = fname
        self.surname = sname
    def nameprint(self):
        print("Name:", self.firstname + " " + self.surname)
class Child(Mother):
    pass
```

父类名为`Mother`，其属性`firstname`和`surname`通过`init`构造函数方法进行初始化。子类名为`Child`，继承自`Mother`类。在定义子类时，父类的名称作为参数传递。关键字`pass`告诉 Python 子类不需要做任何事情（这个类只是继承父类的一切，而不添加任何内容）。

然而，即使子类没有实现任何其他方法或添加任何额外属性，关键字`pass`对于防止抛出任何错误也是必不可少的。

进一步阅读：了解更多关于继承的信息：[`https://docs.python.org/3/tutorial/classes.html#inheritance`](https://docs.python.org/3/tutorial/classes.html%2523inheritance)

**数据抽象**：数据抽象是只呈现功能而隐藏实现细节的过程。例如，WhatsApp 的新用户只需要学习其基本功能，如发送消息、附加照片和拨打电话，而不需要了解编写该应用程序代码的开发人员是如何实现这些功能的。

在下面的示例中，我们声明了一个`Circle`类的对象，并使用`area`方法计算面积，在调用`area`方法时，我们不需要知道面积是如何计算的。

```
class Circle():
    def __init__(self, r):
        self.r = r
    def area(self):
        return 3.14 * self.r * self.r
circle1 = Circle(3)
circle1.area()
```

输出：

```
28.259999999999998
```

## 总结

- 容器是基本数据类型（如`int`、`float`、`str`）对象的集合。Python 中有四种内置容器——列表、元组、字典和集合。
- 每种容器都有不同的属性，并且可以应用于容器的各种函数彼此不同，这取决于元素是否可以排序和更改（可变性）。列表是可变的且有序的，元组是不可变的且有序的，字典和集合是可变的且无序的。
- Python 遵循面向对象编程的原则，如继承（从一个类派生另一个类）、数据抽象（只呈现相关细节）、封装（将数据与功能绑定）和多态（能够使用一个接口处理多种数据类型）。
- 类包含构造函数（使用特殊语法定义）、实例变量以及操作这些变量的方法。所有方法都必须包含关键字`self`作为参数，它指向类的一个对象。

在下一章中，我们将学习 Python 如何应用于正则表达式以及解决数学问题，并了解用于这些应用的库。

## 复习练习

**问题 1**：如何将列表转换为元组，反之亦然？

**问题 2**：

与列表推导式类似，字典推导式是从现有可迭代对象创建字典的快捷方式。使用字典推导式创建以下字典（基于两个列表，一个包含键（`a`–`f`），另一个包含值（`1`–`6`））：

```
{'a': 0, 'b': 1, 'c': 2, 'd': 3, 'e': 4, 'f': 5}
```

### 问题 3

以下哪条代码语句*不会*导致错误？

1.  `'abc'[0]='d'`
2.  `list('abc')[0]='d'`
3.  `tuple('abc')[0]='d'`
4.  `dict([('a',1),('b',2)])[0]=3`

### 问题 4

编写一个程序，计算句子“Every cloud has a silver lining”中元音字母的数量。

#### 问题 5

以下代码的输出是什么？

```
x=1,2
y=1,
z=(1,2,3)
type(x)==type(y)==type(z)
```

#### 问题 6

以下代码的输出是什么？

```
numbers={
'English':{'1':'One','2':'Two'},
'Spanish':{'1':'Uno','2':'Dos'},
'German':{'1':'Ein','2':'Zwei'}
}
numbers['Spanish']['2']
```

#### 问题 7

考虑以下字典：

```
eatables={'chocolate':2,'ice cream':3}
```

使用以下方法向此字典添加另一个条目（键为`"biscuit"`，值为`4`）：

*   `If` 语句
*   `setdefault` 方法

#### 问题 8

创建一个包含 1 到 20 之间奇数的列表，并使用适当的列表方法执行以下操作：

*   在末尾添加元素 `21`
*   在第 4 个位置插入数字 `23`
*   向此列表添加另一个包含 1 到 20 之间偶数的列表
*   查找数字 `15` 的索引
*   移除并返回最后一个元素
*   删除第 10 个元素
*   过滤此列表，创建一个包含所有小于或等于 `13` 的数字的新列表
*   使用 `map` 函数创建一个包含列表中数字平方的新列表
*   使用列表推导式从现有列表创建一个新列表。如果原数字是奇数，则新列表包含该数字；否则，包含该数字的一半。

## 答案

### 问题 1

使用列表方法将元组转换为列表：

```
list((1,2,3))
```

使用元组方法将列表转换为元组：

```
tuple([1,2,3])
```

### 问题 2

代码：

```
#包含键的列表
l=list('abcdef')
#包含值的列表
m=list(range(6))
#字典推导式
x={i:j for i,j in zip(l,m)}
x
```

### 问题 3

正确选项：b 和 d。在选项 a 和 c 中，代码语句试图分别更改字符串和元组中的元素，而它们是不可变对象，因此不允许这些操作。在选项 b（列表）和 d（字典）中，允许元素赋值。

### 问题 4

解决方案：

```
message="Every cloud has a silver lining"
m=message.lower()
count={}
vowels=['a','e','i','o','u']
for character in m:
if character.casefold() in vowels:
count.setdefault(character,0)
count[character]=count[character]+1
print(count)
```

#### 问题 5

输出：

```
True
```

这三种方法都是定义元组的可接受方式。

#### 问题 6

输出：

```
'Dos'
```

此问题使用了嵌套字典（字典中的字典）的概念。

#### 问题 7

解决方案：

```
eatables={'chocolate':2,'ice cream':3}
#If 语句
if 'biscuit' not in eatables:
eatables['biscuit']=4
#setdefault 方法（替代方法）
eatables.setdefault('biscuit',4)
```

#### 问题 8

解决方案：

```
odd_numbers=list(range(1,20,2))
#在末尾添加元素 21
odd_numbers.append(21)
#在第 4 个位置插入数字 23
odd_numbers.insert(3,23)
#向此列表添加另一个包含 1 到 20 之间偶数的列表
even_numbers=list(range(2,20,2))
odd_numbers.extend(even_numbers)
#查找数字 15 的索引
odd_numbers.index(15)
#移除并返回最后一个元素
odd_numbers.pop()
#删除第 10 个元素
del odd_numbers[9]
#过滤此列表，包含所有小于或等于 13 的数字
nos_less_13=filter(lambda x:x<=13,odd_numbers)
list(nos_less_13)
#使用 map 函数创建一个包含平方的列表
squared_list=map(lambda x:x**2,odd_numbers)
#使用列表推导式创建新列表
new_list=[x/2 if x%2==0 else x for x in odd_numbers]
new_list
```