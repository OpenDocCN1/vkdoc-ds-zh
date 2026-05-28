# 3. 复杂解决方案

本节中的示例将向你展示如何使用`map`、`reduce`和`select`来解决更复杂的问题。我将从一个关于调试的部分开始，然后介绍复杂的解决方案。请先阅读调试部分，因为这是理解问题以及深入理解`map`、`reduce`和`select`的关键。

## `Map`、`Reduce`和`Select`的调试代码块

在解决问题的过程中，了解问题的最佳方法是插入如`puts`这样的打印语句，来观察循环执行时发生了什么。

### 调试`Map`代码块

需要记住的是，`map`在每次运行代码块时都会对集合中的每个元素进行操作。调试时，你想看到代码块如何对每个元素进行操作。

```
[1,2,3,4,5].map do |number|
  result = number*number
  puts "The result of the function on element: #{number} is #{result}"
  result
end
```

将结果保存在一个局部变量中很重要，这样你才能将它作为代码块的最后一条语句返回。如果不这样做，`puts`语句的结果将会被返回（通常是`nil`）。

### 调试`Reduce`代码块

调试`reduce`稍有不同，因为备忘录会在元素之间传递。因此，你需要打印出元素以及备忘录。

```
final_answer =
[1,2,3,4,5].reduce(0) do |memo, number|
  memo = memo + number
  puts "After operating on number: #{number}, the memo is #{memo.inspect} "
  memo
end
```

```
After operating on number: 1, the memo is 1
After operating on number: 2, the memo is 3
After operating on number: 3, the memo is 6
After operating on number: 4, the memo is 10
After operating on number: 5, the memo is 15
=> 15
```

再次强调，确保在打印备忘录之前保存它，然后在代码块结束时返回它。

如果你在`reduce`代码块中使用`memo`来构建不同的数据结构，检查备忘录对于收敛到一个好的解决方案变得极其有用。

### 调试`Select`代码块

调试`select`很简单，因为你唯一需要知道的是谓词对于特定元素是否为`true`。

例如，一个只保留偶数（`number%2==0`）的`select`过滤器可以这样调试：

```
[1,2,3,4,5].select do |number|
  predicate = number%2==0
  puts "The predicate is #{predicate} for number #{number}"
  predicate
end
```

```
The predicate is false for number 1
The predicate is true for number 2
The predicate is false for number 3
The predicate is true for number 4
The predicate is false for number 5
=> [2, 4]
```

在这里，你可以看到只有那些谓词为`true`的数字才会出现在结果中（`[2,4]`）。

### FizzBuzz

该程序被用作一道编程题。编写一个程序，遍历整数列表`1..N`。如果数字能被2整除，则输出“fizz”；如果能被3整除，则输出“buzz”；如果能同时被2和3整除，则输出“fizzbuzz”；否则，输出该数字本身。

输入可以被建模为一个范围：`(1..15)`。

输出应如下所示：

```
fizz
buzz
fizz

fizzbuzz

fizz
buzz
fizz

fizzbuzz

fizz
buzz
```

输出的数据结构应当是一个数组。我们需要对数组中的每个元素进行操作来得到输出。先从fizz开始，它要求数字能被2整除（`n%2==0`）：

```
(1..15).map do |n|
  if n%2 == 0
    "fizz"
  else
    n.to_s
  end
end
```

这将得到以下结果：

```
=> ["1", "fizz", "3", "fizz", "5", "fizz", "7", "fizz", "9", "fizz", "11", "fizz", "13", "fizz", "buzz"]
```

结果很接近了，但我们还没有处理buzz：

```
(1..15).map do |n|
  if n%2 == 0
    "fizz"
  elsif n%3 ==0
    "buzz"
  else
    n.to_s
  end
end
```

这将得到以下结果：

```
=> ["1", "fizz", "buzz", "fizz", "5", "fizz", "7", "fizz", "buzz", "fizz", "11", "fizz", "13", "fizz", "buzz"]
```

很接近了，但fizzbuzz的例子在6和12处没有出现。让我们添加另一个条件。能被2和3整除意味着它必须能被6整除：

```
fizzbuzz_list =
  (1..15).map do |n|
    if n%2 == 0
      "fizz"
    elsif n%3 ==0
      "buzz"
    elsif n%6 == 0
      "fizzbuzz"
    else
      n.to_s
    end
  end
```

这与上一次尝试得到了相同的结果。仔细检查代码会发现，如果一个数字能被6整除，它必然是偶数，因此会被`n%2==0`这个条件捕获。但是，如果我们先捕获`n%6==0`条件，会发生什么呢？

```
fizzbuzz_list =
  (1..15).map do |n|
    if n%6 == 0
      "fizzbuzz"
    elsif n%3 ==0
      "buzz"
    elsif n%2 == 0
      "fizz"
    else
      n.to_s
    end
  end
```

这将得到以下结果：

```
=> ["1", "fizz", "buzz", "fizz", "5", "fizzbuzz", "7", "fizz", "buzz", "fizz", "11", "fizzbuzz", "13", "fizz", "buzz"]
```

要打印它，只需执行以下操作：

```
fizzbuzz_list.each{|e| puts e}
```

### 奇数立方和

这是一个典型的数学问题，可以使用`map`、`reduce`和`select`来解决。

你需要计算从1到1000的奇数的立方和。例如，对于10以下的奇数，计算如下：

```
1**3 + 3**3 + 5**3 + 7**3 + 9**3
=> 1225
```

1225是合理的，因为它是1 + 27 + 125 + 343 + 729的结果。

如何解决这个问题？分阶段进行。首先，我们必须找出如何筛选奇数。奇数是指当进行`模 2`运算时，结果为1的数字。例如：

```
num = 15
num%2
=> 1

num = 1000
num%2
=> 0
```

我们可以尝试从一个范围中选择数字。出于本练习的目的，我将把数字限制在20以下。

```
(1..20).select do |number|
  number%2 == 1
end
```

```
=> [1, 3, 5, 7, 9, 11, 13, 15, 17, 19]
```

到目前为止还不错。我们只筛选出了奇数。下一步是计算每个数的立方。换句话说，我们必须将每个数字映射到它的立方。

```
(1..20).select do |number|
  number%2 == 1
end.map do |odd_number|
  odd_number**3
end
```

```
=> [1, 27, 125, 343, 729, 1331, 2197, 3375, 4913, 6859]
```

这个新数组显然是一个奇数立方列表。（如果不相信，可以手动计算几个验证一下。）现在，我们要做的就是将它们求和（或者通过累加元素来缩减列表）。和的初始值为0。例如：

```
(1..20).select do |number|
  number%2 == 1
end.map do |odd_number|
  odd_number**3
end.reduce(0) do |previous_sum, odd_cube|
  previous_sum + odd_cube
end
=> 19900
```

现在，我将数字范围改为 1000：

```
(1..1000).select do |number|
  number%2 == 1
end.map do |odd_number|
  odd_number**3
end.reduce(0) do |previous_sum, odd_cube|
  previous_sum + odd_cube
end
=> 124999750000
```

这个答案很简洁，但更重要的是，我们能够通过使用 `select`、`map` 和 `reduce` 操作以级联方式分阶段处理数据来构建它。

### 按姓氏对姓名列表进行排序

假设你有一个简单的文本文件 `names.txt`，里面包含一个姓名列表。

```
John Smith
Azamat Bagatov
Hafaz Aladeen
Ramachandran Balasubrahmaniam
Ping Li
Wilfredo Caguiat
Eriks Ivanans
Canaan Banana
Ion Iliescu
Werner Klempner
Thierry Giscard
Joao Soares
```

首先，你将文件读入一个巨大的字符串中。

```
names = File.open("names.txt","r").read
```

你可以像这样将字符串分割成一个数组：

```
namelist = names.split("\n")
```

这将产生一个如下所示的数组：

```
> namelist
["John Smith", "Azamat Bagatov", "Hafaz Aladeen", "Ramachandran Balasubrahmaniam", "Ping Li", "Wilfredo Caguiat", "Eriks Ivanans", "Canaan Banana", "Ion Iliescu", "Werner Klempner", "Thierry Giscard", "Joao Soares"]
```

问题是我们不能简单地按字符串排序，因为我们需要按姓氏排序。让我们看看是否可以先拆分出姓氏并对它们进行排序。首先，我们需要使用字符串的 `split` 函数来拆分姓名：

```
namelist.map do |fullname|
  fullname.split(" ")
end
```

这将得到以下结果：

```
[["John", "Smith"], ["Azamat", "Bagatov"], ["Hafaz","Aladeen"] # 以此类推]
```

从那里，我们可以通过取姓名数组的 `[1]` 元素来分离出姓氏：

```
namelist.map do |fullname|
  fullname.split(" ")
end.map do |namearray|
  namearray[1]
end
```

现在，我们得到了一个姓氏列表，如下所示：`["Smith", "Bagatov", "Aladeen" # 以此类推 ]`

我们可以通过调用 `sort` 函数来对这些姓氏进行排序：

```
namelist.map do |fullname|
  fullname.split(" ")
end.map do |namearray|
  namearray[1]
end.sort
```

现在，我们得到了一个如下所示的列表：

```
["Aladeen", "Bagatov", "Balasubrahmaniam" #以此类推 ...
]
```

挑战在于按姓氏顺序打印完整的姓名。一种方法是用排序键来注释数组。排序键就是数组的第二个元素，它是通过拆分全名得到的。换句话说：

```
fullname.split(" ")[1].
namelist.map do |fullname|
  [fullname.split(" ")[1], fullname]
end
```

在这里，第一次调用 `map` 会产生如下所示的数组元素：

```
[ ["Smith", "John Smith"], ["Bagatov", "Azamat Bagatov"] #...以此类推]
```

现在，我们只需要根据数组的第一个元素对数组进行排序：

```
namelist.map do |fullname|
  [fullname.split(" "), fullname]
end.sort do |a,b|
  a[0] <=> b[0]
end
```

这将产生类似下面的结果：

```
[["Aladeen", "Hafaz Aladeen"], ["Bagatov", "Azamat Bagatov"], ["Balasubrahmaniam", "Ramachandran Balasubrahmaniam"] #...以此类推
]
```

在此操作之后，只需剥离排序键即可获得原始列表，然后重新组合列表。

```
namelist.map do |fullname|
  [fullname.split(" "), fullname]
end.sort do |a,b|
  a[0] <=> b[0]
end.map do |key_with_name|
  key_with_name[1]
end.join("\n")
```

完整的数据处理操作如下所示：

```
File.open("names.txt","r")
  .read
  .split("\n")
  .map do |fullname|
    fullname.split(" ")
  end.map do |fullname|
    [fullname.split(" ")[1], fullname]
  end.sort do |a,b|
    a[0] <=> b[0]
  end.map do |key_with_name|
    key_with_name[1]
  end.join("\n")
```

这是一个非常优雅的解决方案。这也可以通过 `for` 循环和 `if`-`then`-`else` 语句来编码，但那会导致更多的代码。