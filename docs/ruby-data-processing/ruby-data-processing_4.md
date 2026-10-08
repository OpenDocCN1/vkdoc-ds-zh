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

# 将姓名列表转换为 CSV 格式

一个常见的问题是需要将文本文件导入为 CSV 格式。例如，在`names.txt`中，你可能有以下内容：

```
John Smith
Azamat Bagatov
Hafaz Aladeen
Ramachandran Balasubrahmaniam
Ping Li
Wilfredo Caguiat
```

但要导入到 CSV 中，你需要类似这样的格式：

```
Surname,First Name
Smith,John
Bagatov,Azamat
Aladeen,Hafaz
Balasubrahmaniam,Ramachandran
Li,Ping
Caguiat,Wilfredo
```

首先要做的是读取文件：

```
file_contents = File.open("names.txt","r")
```

接下来要将各行拆分为数组：

```
names = file_contents.split("\n")
```

这样会得到类似这样的结果：

```
["John Smith", "Azamat Bagatov" # 等等
]
```

对于每个姓名，你需要拆分名和姓并将它们颠倒顺序：

```
"John Smith".split(" ")
```

这样会得到以下结果：

```
["John","Smith"]
```

如下所示将其颠倒顺序：

```
"John Smith".split(" ").reverse
```

这会产生以下结果：

```
["Smith","John"]
```

要使其成为正确的 CSV 行，需要用逗号连接这些元素：

```
["Smith","John"].join(",")
```

这会产生以下结果：

```
"Smith,John"
```

现在，我们需要对列表的每一行都执行此操作：

```
headerless_names =
names.map do |name|
name.split(" ").reverse.join(",")
end
```

这样会得到除表头之外的所有内容。我们可以使用数组的`unshift()`方法来添加表头信息：

```
headered_names
headerless_names.unshift("Surname,First Name","")
```

要将数组连接成可写入 CSV 文件的字符串，请执行以下操作：

```
headered_names.join("\n")
```

这样就能得到所需的结果。完整的计算过程如下：

```
csv_file_contents =
File.open("names.txt","r").split("\n").map do |name|
name.split(" ").reverse.join(",")
end.unshift("Surname,First  Name"),"").join("\n")
```

# 生成随机姓名列表

你有数据处理任务要做，需要一个庞大的姓名列表。例如：

```
John Smith
Azamat Bagatov
Hafaz Aladeen
Tamir Moufrad
```

不过，你不想耗费一整天来手写这些名字。那就用电脑来帮你完成吧。

## 准备数据

首先，在`first.txt`中准备一个名字列表。这个列表想写多长就写多长。越长越好。

```
Art
Bart
Cart
Dart
Eart
Fred
George
Harry
Ian
John
Ken
Lionel
Mark
```

然后在`last.txt`中准备一个姓氏列表：

```
Armey
Burr
Catherwood
Donaldson
Elwood
Firestone
Graves
Harvey
Illington
Jask
Knowles
Lescon
Merriweather
```

## 处理流程

首先，你需要读取这些名字：

```
last_name_file_content = File.open("last.txt")
first_name_file_content = File.open("first.txt")
```

接着，你需要将它们拆分为数组：

```
last_names = last_name_file_content.split("\n")
first_names = first_name_file_content.split("\n")
```

然后，你需要将每个名字与每个姓氏组合起来。先尝试将每个名字与第一个姓氏组合：

```
test_list = first_names.map do |first_name|
"#{first_name} Armey"
end
```

到目前为止一切顺利。这会生成一个如下所示的数组：

```
["Art Armey", "Bart Armey", "Cart Armey" .... 以此类推
```

现在，你只需要对所有姓氏都执行此操作：

```
test2_list =
last_names.map do |last_name|
first_names.map do |first_name|
"#{first_name} #{last_name}"
end
end
```

这样会得到一个如下所示的列表：

```
[["Art Armey", "Bart Armey", ... ],["Art Burr", "Bart Burr", ....]]
```

已经很接近了。我们只需要展平这个数组：

```
test2_list.flatten
```

这样我们就得到了人员列表。之后，我们只需要将其连接起来，得到要写入文件的文本：

```
test2_list.flatten.join("\n")
```

那么，完整的计算过程如下：

```
last_names = File.open("last.txt").split("\n")
first_names = File.open("first.txt").split("\n")
name_list_text =
last_names.map do |last_name|
first_names.map do |first_name|
"#{first_name} #{last_name}"
end
end.flatten.join("\n")
```

这样做的美妙之处在于，只需输入 32 个名字和 32 个姓氏，就能生成 1,024 个不重复的姓名。

# 清洗数据集

## 处理省份名称变体

分析数据集时一个常见的问题是脏数据。例如，你有一个名为`name_prov.txt`的 CSV 文件，里面是姓名和省份列表。

```
John,Ontario
Azamat,ON
Hafaz,Alta
Tamir,QC
Jacques,Quebec
Bill,AB
Bart,Ont
Carn,Que
Dave,Alta
Don,SK
Fred,Saskatchewan
Gary,Que
Hari,Sask
Ian,SK
Jerry,Alberta
Karl,ON
```

加拿大人看到这个列表通常会知道，"ON"、"Ont"和"Ontario"都代表安大略省，"AB"、"Alta"和"Alberta"代表阿尔伯塔省，其他省份也类似。处理这个问题的一种方法是清洗数据集并标准化省份名称。数据转换将接收"ON"、"Ont"和"Ontario"中的任意一个，并将其替换为标准名称"Ontario"。（作为数据清洗者，你可以选择任何一种形式。我这里选择使用全称）。

### 获取省份列表

第一步是获取省份列表：

```ruby
province_file = File.open("name_prov.txt").read.split(("\n")
```

这会得到一个行数组。现在，我们将每一行按逗号拆分，并只取第二个元素（数组中的索引 1）

```ruby
provinces = province_file.map do |row|
  row.split(",")[1]
end
=> ["Ontario","ON","Alta","QC","Quebec","AB","Ont","Que","Alta","SK","Saskatchew\
an","Que","Sask","SK","Alberta","ON"]
```

这样就得到了一个省份名称数组，但有些名称是重复的。我们需要去重后的名称。为此，我们使用数组方法`uniq()`：

```ruby
provinces.uniq
```

这样我们就得到了一个无重复的省份标签列表。

### 编码标准化规则

现在，我们来编码标准化规则。我们将使用一个`Hash`（哈希表）来查找名称并返回标准名称。

```ruby
standard_name_lookup =
  { "ON" => "Ontario",
    "Ont" => "Ontario",
    "Ontario" => "Ontario",
    "Alta" => "Alberta",
    "Alberta" => "Alberta",
    "AB" => "Alberta",
    "QC" => "Quebec",
    "Que" => "Quebec",
    "Quebec" => "Quebec",
    "SK" => "Saskatchewan",
    "Sask" => "Saskatchewan",
    "Saskatchewan" => "Saskatchewan"
  }
```

现在，如果我们要查找名为"AB"的省份，只需这样做：

```ruby
standard_name_lookup["AB"]
=> "Alberta"
```

我们希望将这些规则编码到一个名为`standard_name_lookup.txt`的 CSV 文件中，内容如下所示：

```
ON,Ontario
Ont,Ontario
Ontario,Ontario
Alta,Alberta
Alberta,Alberta
AB,Alberta
QC,Quebec
Que,Quebec
Quebec,Quebec
SK,Saskatchewan
Sask,Saskatchewan
Saskatchewan,Saskatchewan
```

这使得查找策略数据（编码在`standard_name_lookup.txt`中）与源代码分离。这样，如果你想要更改查找策略，只需编辑此文件——无需更改软件。

那么，读取文件并将其转换为`Hash`就变成了：

```ruby
standard_name_lookup =
  File.open("standard_name_lookup.txt","r").read.split("\n").map do |row|
    row.split(",")
  end.reduce({}) do |lookup_table, row|
    lookup_table.merge row[0] => row[1]
  end
```

### 替换省份标签

因此，剩下的工作就是用标准名称替换各种省份标签：

```ruby
standardized_province_file =
  province_file.map do |row|
    row.split(",")
  end.map do |split_row|
    [split_row[0], standard_name_lookup[split_row[1]]
  end
```

这会得到一个名称已被替换的数组。接下来只需要用逗号连接每个行的元素，然后再用回车连接每一行。完整的代码就变成如下所示：

```ruby
standard_name_lookup =
  File.open("standard_name_lookup.txt","r").read.split("\n").map do |row|
    row.split(",")
  end.reduce({}) do |lookup_table, row|
    lookup_table.merge row[0] => row[1]
  end
standardized_province_file =
  province_file.map do |row|
    row.split(",")
  end.map do |split_row|
    [split_row[0], standard_name_lookup[split_row[1]]
  end.map do |standardized_split_row|
    standardized_split_row.join(",")
  end.join("\n")
```

# 为销售数据序列添加累计总额注释

从一组数字序列开始——例如，表示销售额的美元数值。

```ruby
sales_data = [1,2,4,7,9,11]
```

为每个数字添加当前的累计总额注释。最终列表应如下所示：

```ruby
[[1,1],[2,3],[4,7],[7,14],[9,23],[11,34]]
```

乍看之下，这似乎是 `map()` 的一个应用，因为我们正在为每个销售额数据添加注解数字。然而，这个数字是先前销售额数据的累计总和，直觉上表明应该使用 `reduce()`。

例如，以下任一等效操作：

```ruby
[1,2,4,7,9,11].reduce(0,:+)
```

或

```ruby
[1,2,4,7,9,11].reduce(0){|run_total, sales_fig| run_total + sales_fig}
```

或

```ruby
[1,2,4,7,9,11].reduce(0) do |running_total, sales_figure|
  running_total + sales_figure
end
```

这将得出总计为 34。

此外，我们知道结果将是一个数组的数组，并且初始值将是一个空数组。同时，每次迭代都必须将一个包含两个元素的数组推入初始数组。内部数组的第一个元素保存当前销售额，第二个元素保存累计总额。尝试以下操作以构建正确的结构：

```ruby
test1 =
  sales_data.reduce([]) do |memo, sales_figure|
    memo.push([sales_figure, 666])
  end
```

这将得出以下结果：

```ruby
[[1, 666], [2, 666], [4, 666], [7, 666], [9, 666], [11, 666]]
```

我们走对了方向，但现在需要弄清楚如何将累计总额放入内部数组的第二个元素。当然，累计总额就是上次的累计总额加上当前的销售额。

```ruby
test2 =
  sales_data.reduce([]) do |memo, sales_figure|
    memo.push([sales_figure, memo.last[-1] + sales_figure])
  end
```

这会引发一个异常，因为在第一次迭代运行时 `memo.last` 中没有任何内容。我们需要通过将其赋值为 0 来处理这种情况，如下所示：

```ruby
test2 =
  sales_data.reduce([]) do |memo, sales_figure|
    last_total = memo.last.nil? ? 0 : memo.last[1]
    memo.push([sales_figure, last_total + sales_figure])
  end
```

这将得出正确答案：

```ruby
[[1, 1], [2, 3], [4, 7], [7, 14], [9, 23], [11, 34]]
```

## 杨辉三角

计算机科学中的一个经典问题是生成杨辉三角。例如，一个六行的三角形如下所示：

```
1
1  1
1  2  1
1  3  3  1
1  4  6  4  1
1  5 10 10  5  1
1  6 15 20 15  6  1
```

通常，如果将其存储为二维数组，公式如下：

```
a[n][0] = 1   # 其中 n >=0
a[n][n] = 1
a[n][m] = a[n-1][m] + a[n-1][m-1]
```

首先，这显然是一个二维数组。所以，对于一个四行的三角形，它看起来像这样：

```ruby
[[1],[1,1],[1,2,1],[1,3,3,1]]
```

为了方便打印，我们暂时这样做：

```ruby
triangle.each{|row| p row.inspect}
```

考虑到我们正在构建一个数组的结构，使用 `reduce()` 似乎很合适。初始值是 `[]`。

```ruby
triangle = (0..5).reduce([]) do |memo, n|
  memo.push []
end
triangle.each{|row| p row.inspect}
"[]"
"[]"
"[]"
"[]"
"[]"
"[]"
=> [[], [], [], [], [], []]
```

这生成了一个数组的数组，但其中包含的内容不够。不过，我们知道第一个和最后一个值是 1。我们可以将其余的值默认为零。我们还知道每个数组有 `n-1` 个元素。（注意 `row[-1]` 是表示数组最后一个元素的方法）。

```ruby
triangle = (0..5).reduce([]) do |memo, n|
  row = n==0 ? [] : Array.new(n+1)
  row[0] = 1
  row[-1] = 1
  memo << row
end
triangle.each{|row| p row.inspect}
```

这得出：

```
"[1]"
"[1, 1]"
"[1, nil, 1]"
"[1, nil, nil, 1]"
"[1, nil, nil, nil, 1]"
"[1, nil, nil, nil, nil, 1]"
=> [[1], [1, 1], [1, nil, 1], [1, nil, nil, 1], [1, nil, nil, nil, 1], [1, nil, nil, nil, nil, 1]]
```

结构逐渐成形，但我们也知道第 n 行是前一行的一个函数。因此，我们需要回溯到上一行并进行处理以生成下一行。

```ruby
triangle = (0..5).reduce([]) do |memo, n|
  if n==0
    row = [1]
  else
    prev = memo[-1]
    row = prev.each_cons(2).map { |a,b| a+b }
    row.unshift(1)
    row.push(1)
  end
  memo << row
end
triangle.each{|row| p row.inspect}
```

这得出：

```
"[1]"
"[1, 1]"
"[1, 2, 1]"
"[1, 3, 3, 1]"
"[1, 4, 6, 4, 1]"
"[1, 5, 10, 10, 5, 1]"
=> [[1], [1, 1], [1, 2, 1], [1, 3, 3, 1], [1, 4, 6, 4, 1], [1, 5, 10, 10, 5, 1]]
```

这好一些了。行开始充实起来。现在，要构建当前行，你需要前一行。

例如，你可以对前一行使用 `reduce` 来得到当前行。要获取一行的最后两个元素，你需要记住它们。例如：

```ruby
[1,3,3,1].reduce([nil,nil]) do |memo, element|
  memo.push element
  memo.shift
  puts "After pushing #{element}, the memo is #{memo.inspect}"
  memo
end
After pushing 1, the memo is [nil, 1]
After pushing 3, the memo is [1, 3]
After pushing 3, the memo is [3, 3]
After pushing 1, the memo is [3, 1]
=> [3, 1]
```

我们越来越接近了。让我们把和保存在 `memo` 中。现在，请记住 `memo` 的结构必须改变。我们需要一个地方来保存最后两个元素的数组以及它们的和。因此，`memo` 将是一个 `Hash`，以 `:sum` 和 `:last_2` 作为键。

```ruby
initial_memo = {sum: 0, last_2: [nil,nil]}
[1,3,3,1].reduce(initial_memo) do |memo, element|
  memo[:last_2].push element
  memo[:last_2].shift
  memo[:sum] = memo[:last_2].compact.reduce(0,:+)
  puts "After pushing element #{element}, memo is #{memo}"
  memo
end
```

结果如下：

```
After pushing element 1, memo is {:sum=>1, :last_2=>[nil, 1]}
After pushing element 3, memo is {:sum=>4, :last_2=>[1, 3]}
After pushing element 3, memo is {:sum=>6, :last_2=>[3, 3]}
After pushing element 1, memo is {:sum=>4, :last_2=>[3, 1]}
=> {:sum=>4, :last_2=>[3, 1]}
```

我们更接近了。这个和遵循模式 1,4,6,4，这几乎就是下一行（缺少最后一个 1）。所以，现在我们需要将下一行保存在 `memo` 中，然后在计算完成时将其提取出来，如下所示：

# 帕斯卡三角的生成与逆波兰表示法解析器

## 帕斯卡三角的生成

我们使用 `reduce` 方法来计算帕斯卡三角的行。

```ruby
initial_memo = {last_2: [nil,nil], next_row: []}
[1,3,3,1].reduce(initial_memo) do |memo, element|
  memo[:last_2].push element
  memo[:last_2].shift
  memo[:next_row].push memo[:last_2].compact.reduce(0,:+)
  puts "After pushing element #{element}, memo is #{memo}"
  memo
end
```

结果如下：

```
推送元素 1 后，memo 为 {:last_2=>[nil, 1], :next_row=>[1]}
推送元素 3 后，memo 为 {:last_2=>[1, 3], :next_row=>[1, 4]}
推送元素 3 后，memo 为 {:last_2=>[3, 3], :next_row=>[1, 4, 6]}
推送元素 1 后，memo 为 {:last_2=>[3, 1], :next_row=>[1, 4, 6, 4]}
=> {:last_2=>[3, 1], :next_row=>[1, 4, 6, 4]}
```

差不多完成了！我们只需要从结果中提取 `:next_row`，然后在下一行的末尾添加一个 `1`。同时我也会移除 `puts` 语句。

```ruby
initial_memo = {last_2: [nil,nil], next_row: []}
[1,3,3,1].reduce(initial_memo) do |memo, element|
  memo[:last_2].push element
  memo[:last_2].shift
  memo[:next_row].push memo[:last_2].compact.reduce(0,:+)
  memo
end[:next_row].push 1
=> [1, 4, 6, 4, 1]
```

于是，我们得到了每一行的公式。来看看它对几个例子是否有效。首先是顶层行：

```ruby
initial_memo = {last_2: [nil,nil], next_row: []}
[1].reduce(initial_memo) do |memo, element|
  memo[:last_2].push element
  memo[:last_2].shift
  memo[:next_row].push memo[:last_2].compact.reduce(0,:+)
  memo
end[:next_row].push 1
=> [1, 1]
```

如果是空行呢（例如 `[]`）？

```ruby
initial_memo = {last_2: [nil,nil], next_row: []}
[].reduce(initial_memo) do |memo, element|
  memo[:last_2].push element
  memo[:last_2].shift
  memo[:next_row].push memo[:last_2].compact.reduce(0,:+)
  memo
end[:next_row].push 1
=> [1]
```

看起来没问题。接下来需要对每一行都执行这个操作。我们再次使用 `reduce`，因为每次迭代都会将 `memo` 传递到下一次。尝试如下（比如，七行）：

```ruby
inital_memo = [[]]
(1..7).reduce([[]]) do |memo, num|
  initial_memo = {last_2: [nil,nil], next_row: []}
  memo.push(
    memo[-1].reduce(initial_memo) do |memo, element|
      memo[:last_2].push element
      memo[:last_2].shift
      memo[:next_row].push memo[:last_2].compact.reduce(0,:+)
      memo
    end[:next_row].push(1)
  )
  puts "处理第 #{num} 行后，memo 为 #{memo}"
  memo
end
```

这样可行。接下来需要移除 `puts` 语句并以适当格式打印出来。

```ruby
inital_memo = [[]]
triangle =
(1..7).reduce([[]]) do |memo, num|
  initial_memo = {last_2: [nil,nil], next_row: []}
  memo.push(
    memo[-1].reduce(initial_memo) do |memo, element|
      memo[:last_2].push element
      memo[:last_2].shift
      memo[:next_row].push memo[:last_2].compact.reduce(0,:+)
      memo
    end[:next_row].push(1)
  )
  puts "处理第 #{num} 行后，memo 为 #{memo}"
  memo
end
triangle.each{|row| p row.inspect}
```

输出为：

```
"[1]"
"[1, 1]"
"[1, 2, 1]"
"[1, 3, 3, 1]"
"[1, 4, 6, 4, 1]"
"[1, 5, 10, 10, 5, 1]"
"[1, 6, 15, 20, 15, 6, 1]"
```

更简洁的版本如下：

```ruby
triangle.each{|row| puts row.join (", ") };1
```

```
1,  1
1,  2,  1
1,  3,  3,  1
1,  4,  6,  4,  1
1,  5, 10, 10,  5,  1
1,  6, 15, 20, 15,  6,  1
```

## 逆波兰表示法解析器

描述算术运算的一种方式是使用操作数在前、运算符在后的表示法。例如，表达式

```
3 + 2
```

可以写成

```
3 2 +
```

这称为后缀表示法或逆波兰表示法（在我上学时的 1980 年代，这曾是惠普计算器的一大特色，深受工程极客们喜爱）。它可以避免使用括号。处理起来也更简单，因为只需要一个栈。每读取一个词法单元（数字或运算符），就将其压入栈中。当运算符被压入栈时，这是一个特殊情况。运算符会与接下来的两个元素（即操作数）一起弹出栈，执行运算，然后将结果压回栈中。

例如，表达式

```
3 3 4 * 4 7 8 + - * +
```

的处理过程可以用一系列栈快照来表示，每行对应一个快照。随着词法单元的扫描，栈向右增长；如果运算符被压入栈中，RPN 解析器会弹出最后三个词法单元，执行计算，并将答案压回栈中。

```
3
3 3
3 3 4
3 3 4 *
3 12                 (12 是 4*3)
3 12 4
3 12 4 7
3 12 4 7 8
3 12 4 7 8 +
3 12 4 15            (15 是 7+8)
3 12 4 15 -
3 12 -11             (-11 是 4-15)
3 12 -11 *
3 -132               (-132 是 12*(-11))
3 -132 +
-129                 (-129 是 3 + (-132))
```

那么如何解决这个问题？首先，我们通过以空格分割字符串来对其进行词法分析（这不是最健壮的词法分析方法，但足以应对这个例子）。一旦从字符串中得到了词法单元数组，我们可能需要在每次迭代后记住栈的状态。在 Ruby 中，可以使用数组的 `push` 和 `pop` 操作将其用作栈。此外，栈的初始值为空数组。试试下面的代码：

```ruby
expression = "1 2 2 + 3 * -"
answer = expression.split(" ").reduce([]) do |stack, token|
  stack.push(token)
  puts "推送 #{token} 后栈的状态为 #{stack.inspect}"
  stack
end
```

这将输出以下内容：

```
推送 1 后栈的状态为 ["1"]
推送 2 后栈的状态为 ["1", "2"]
推送 2 后栈的状态为 ["1", "2", "2"]
推送 + 后栈的状态为 ["1", "2", "2", "+"]
推送 3 后栈的状态为 ["1", "2", "2", "+", "3"]
推送 * 后栈的状态为 ["1", "2", "2", "+", "3", "*"]
推送 - 后栈的状态为 ["1", "2", "2", "+", "3", "*", "-"]
=> ["1", "2", "2", "+", "3", "*", "-"]
```

这样，我们就在每次迭代后正确地保存了栈的状态。现在，我们需要识别运算符 `+`、`-`、`*` 和 `/`。试试这个：

# 用Ruby实现逆波兰表示法（RPN）计算器

## 展示代码与输出

以下是计算逆波兰表达式 `"1 2 2 + 3 * -"` 的Ruby代码及其执行时的栈状态输出：

```ruby
expression = "1 2 2 + 3 * -"
answer = expression.split(" ").reduce([]) do |stack, token|
  stack.push(token)
  puts "推送 #{token} 后栈的状态为 #{stack.inspect}"
  if ["+","-","*","/"].include?(token)
    puts " 词法单元 #{token} 是一个运算符"
  end
  stack
end
```

执行上述代码的输出如下：

```
推送 1 后栈的状态为 ["1"]
推送 2 后栈的状态为 ["1", "2"]
推送 2 后栈的状态为 ["1", "2", "2"]
推送 + 后栈的状态为 ["1", "2", "2", "+"]
词法单元 + 是一个运算符
推送 3 后栈的状态为 ["1", "2", "2", "+", "3"]
推送 * 后栈的状态为 ["1", "2", "2", "+", "3", "*"]
词法单元 * 是一个运算符
推送 - 后栈的状态为 ["1", "2", "2", "+", "3", "*", "-"]
词法单元 - 是一个运算符
=> ["1", "2", "2", "+", "3", "*", "-"]
```

## 执行过程分析

现在，根据RPN规则，如果词法单元是运算符，我们需要执行运算。要执行运算，我们必须从栈中弹出运算符，然后弹出第一个操作数，最后弹出第二个操作数。我将在第一次遇到运算符时停止这次迭代，以便查看栈的状态。

```ruby
expression = "1 2 2 + 3 * -"
answer = expression.split(" ").reduce([]) do |stack, token|
  stack.push(token)
  puts "The stack after pushing #{token} is #{stack.inspect}"
  if ["+","-","*","/"].include?(token)
    puts " Token #{token} is an operator"
    operator = stack.pop
    second_operand = stack.pop
    first_operand = stack.pop
    puts " Second operand is #{second_operand}. First operand is #{first_operand}"
    raise "Stop for a bit"
  end
  stack
end
```

这会打印出以下内容：

```
The stack after pushing 1 is ["1"]
The stack after pushing 2 is ["1", "2"]
The stack after pushing 2 is ["1", "2", "2"]
The stack after pushing + is ["1", "2", "2", "+"]
Token + is an operator
Second operand is 2\. First operand is 2
Stop for a bit
(repl):11:in `block in initialize'
(repl):2:in `each'
(repl):2:in `reduce'
(repl):2:in `initialize'
```

`Stop for a bit` 这个异常只让我们到达了第一个运算符。我只是想展示运算符和操作数从栈中被弹出。现在，我们只需向第一个操作数发送 `"operator"` 消息，并将第二个操作数作为参数传递。（请记住，5 和 7 都是 `Fixnum` 类的 Ruby 对象，它们都有一个名为 `:+` 的方法）。例如，

```ruby
5 + 7
```

等同于

```ruby
5.send(:+,7)
```

如果你不信，可以在命令行上试试。

现在，从栈中弹出的每个令牌都是字符串。操作数必须转换为整数（使用 `to_i`），而运算符必须转换为符号（使用 `to_sym`）。因此，代码片段如下所示：

```ruby
first_operand.to_i.send(operator.to_sym, second_operand.to_i)
```

这会告诉解释器调用 `5` 这个对象的 `:+` 方法（因为 `"+".to_sym` 是 `:+`），该对象恰好是一个 `Fixnum`。一旦计算出该结果，就必须将结果重新压入栈中。（`stack` 变量是此次 `reduce` 操作的记忆体）。

## 改进代码

```ruby
expression = "1 2 2 + 3 * -"
answer = expression.split(" ").reduce([]) do |stack, token|
  stack.push(token)
  puts "The stack after pushing #{token} is #{stack.inspect}"
  if ["+","-","*","/"].include?(token)
    puts " Token #{token} is an operator"
    operator = stack.pop
    second_operand = stack.pop
    first_operand = stack.pop
    puts " Second operand is #{second_operand}. First operand is #{first_operand}"
    result = first_operand.to_i.send(operator.to_sym, second_operand.to_i)
    puts " The result of this expression is #{result}"
    stack.push(result)
    puts " The stack after evaluating the expression is #{stack.inspect}"
  end
  stack
end
```

这会打印出以下内容：

```
The stack after pushing 1 is ["1"]
The stack after pushing 2 is ["1", "2"]
The stack after pushing 2 is ["1", "2", "2"]
The stack after pushing + is ["1", "2", "2", "+"]
Token + is an operator
Second operand is 2\. First operand is 2
The result of this expression is 4
The stack after evaluating the expression is ["1", 4]
The stack after pushing 3 is ["1", 4, "3"]
The stack after pushing * is ["1", 4, "3", "*"]
Token * is an operator
Second operand is 3\. First operand is 4
The result of this expression is 12
The stack after evaluating the expression is ["1", 12]
The stack after pushing - is ["1", 12, "-"]
Token - is an operator
Second operand is 12\. First operand is 1
The result of this expression is -11
The stack after evaluating the expression is [-11]
```

## 最终代码与结果

现在，要清理代码并打印结果，我们执行以下操作：

```ruby
expression = "1 2 2 + 3 * -"
answer = expression.split(" ").reduce([]) do |stack, token|
  stack.push(token)
  if ["+","-","*","/"].include?(token)
    operator = stack.pop
    second_operand = stack.pop
    first_operand = stack.pop
    result = first_operand.to_i.send(operator.to_sym, second_operand.to_i)
    stack.push(result)
  end
  stack
end[0]
puts "The expression is #{expression}. The answer is #{answer}."
```

## 生成队服姓名条列表

假设你有一个简单的文本文件 `names.txt`，里面包含一个名字列表。这些是篮球队里女孩们的名字。

首先，将文件读入一个巨大的字符串：

```ruby
names = File.open("names.txt","r").read
```

你可以像这样将字符串拆分为数组：

```ruby
namelist = names.split("\n")
```

这会生成一个如下所示的数组：

```
["Emily Muller", "Lois Makad", "Sophie Wheaton", # 等等
]
```

首先，你想用姓氏来注释列表以作辅助：

```ruby
annotated_namelist = namelist.map do |name|
[name.split(" ")[1], name]
end
```

然后，你想按不同的姓氏对名字进行分组。Ruby 哈希是一种按姓氏键存储的有用方式：

```ruby
grouped_list =
annotated_namelist.reduce({}) do |memo, annotated_name|
if memo[annotated_name[0]].nil?
memo[annotated_name[0]] = [annotated_name[1]]
else
memo[annotated_name[0]] << annotated_name[1]
end
memo
end
```

此时，我们已经按不同的姓氏建立了哈希键，它看起来像这样：

```
{"Muller"   =>   ["Emily Muller", "Abby Muller"],
"Makad"    =>   ["Lois Makad"],
"Wheaton"  =>   ["Sophie Wheaton"],
"Gee"      =>   ["Zoe Gee"],
"Semanova" =>   ["Anastasia Semanova", "Iulia Semanova"],
"Smith"     =>   ["Jane Smith"]
"Sundstrom" =>   ["Megan Sundstrom"],
"Schmidt"   =>   ["Brunhilda Schmidt"],
"Park"      =>   ["Sally Park"],
"Bailey"    =>   ["Jill Bailey"]
}
```

我们应该注意到，键是字符串，值是一个或多个名字的数组。我们需要将名字简化为姓名条。如果数组中只有一个元素（名字），我们只需拆分字符串并取第二个元素，即姓氏。如果有多个元素，我们需要拆分字符串，取第一个元素的第一个字母，并将其与第二个元素连接起来。详见此处：

```ruby
grouped_label_names =
grouped_list.map do |key,value|
if value.size>1
res = value.map do |name|
"#{name.split(' ')[0][0]}. #{name.split(' ')[1]}"
end
res
else
value[0].split(' ')[1]
end
end
```

这会生成以下内容：

```
[["E. Muller", "A. Muller"], "Makad", "Wheaton", "Gee", ["A. Semanova", "I. Semanova"], "Smith", "Sundstrom", "Schmidt", "Park", "Bailey"]
```

这已经很接近了。列表只需展平即可：

```ruby
grouped_label_names.flatten
```

这会生成以下内容：

```
["E. Muller", "A. Muller", "Makad", "Wheaton", "Gee", "A. Semanova", "I. Semanova", "Smith", "Sundstrom", "Schmidt", "Park", "Bailey"]
```

然后，只需通过回车符连接即可重新构建列表，如下所示：

```ruby
grouped_label_names.flatten.join("\n")
```

完成后，需要将数组展平并重新构建为文本文件：

```ruby
namebar_names =
grouped_label_names.flatten.join("\n")
```

因此，完整的数据处理解决方案如下：

```ruby
namebar_names =
File.open("names.txt","r").read.split("\n").map do |name|
[name.split(" ")[1], name]
end.inject({}) do |memo, annotated_name|
if memo[annotated_name[0]].nil?
memo[annotated_name[0]] = [annotated_name[1]]
else
memo[annotated_name[0]] << annotated_name[1]
end
memo
end.map do |key,value|
if value.size>1
res = value.map do |name|
"#{name.split(' ')[0][0]}. #{name.split(' ')[1]}"
end
res
else
value[0].split(' ')[1]
end
end.flatten.join("\n")
```

# 求两个函数的交点

假设你有两个函数：`y = x` 和 `y = 10sin(x)`。你想找到它们的交点。一些已知事实如下：

- `y = 10sin(x)` 的周期为 `2*pi`，约等于 `6.2`，因此每隔半个周期（约 `3.14`）便有一个真正的零点。

- `y = 10sin(x)` 的最大值为 `10`，最小值为 `-10`，因此如果我们要沿着直线 `y = x` 进行搜索，可以将 `x` 的取值范围限制在 `-10` 到 `10` 之间。

- 根据微积分知识，我们知道 `y = 10sin(x)` 的斜率为 `y = 10cos(x)`，当 `x=0` 以及其他一些位置时，斜率达到最大值 `10`。

- 对于斜率为 `10` 的情况，如果你想让 `y` 的变化量不超过某个很小的值，那么 `x` 的变化量最多只能是该变化量的十分之一。

- 当 `y = 10sin(x)` 与 `y = x` 的差值为 `0` 时，即发生交点。但在不完美的现实条件下，我们可以认为，只要差值小于某个值（例如 `0.02`），就算作交点。

- 要保证差值小于 `0.02`，我们必须以该差值的十分之一（即 `0.002`）为步长来测试交点。因此，定义域（即要测试的 `x` 值集合）可以表示如下：

`(-10..10).step(0.002)`

通过以下方式打印出来：

```
(-10..10).step(0.002).map{|n| n}
```

这会生成 `2000` 个待测试的 `x` 值。对于每个这样的值，你需要收集两个函数对应的数值范围。每个范围是一个包含三个元素的数组，由 `x` 值、第一个函数的值（`y=x`）和第二个函数的值（`y=10sin(x)`）组成。如下所示：

```
function_values =
(-10..10).step(0.002).map do |x|
  [x, x, 10*Math.sin(x)]
end
```

现在，当这两个函数之间的差值（例如，三元组中的第一个元素与第二个元素）极小时，便发生交点。在我们的示例中，我们每次将 `x` 值增加 `0.002`，因此预期的最大变化量为 `0.02`。因此，差值小于 `0.02` 的值就可以实际视为零点。在这大约两千个三元组中，我们只需选出元素 `1` 和元素 `2`（而不是元素 `0`）非常接近的那些。换句话说，差值的绝对值应小于 `0.02`。请看以下代码：

```
zeroes = function_values.select do |three_tuple|
  (three_tuple[0] - three_tuple[1]).abs < 0.02
end
```

这会得到一组两个函数中差值足够小，可以视为零点的点。

```
zeroes =
(-10..10).step(0.002).map do |x|
  [x, x, 10*Math.sin(x)]
end.select do |three_tuple|
  (three_tuple[1] - three_tuple[2]).abs < 0.02
end
```

得出如下结果（已格式化以便阅读）：

```
=> [
  [-8.426, -8.426, -8.408100860402287],
  [-8.424, -8.424, -8.418910641243016],
  [-8.422, -8.422, -8.429686746452397],
  [-7.07, -7.07, -7.081077089878837],
  [-7.068, -7.068, -7.066940848453371],
  [-7.066, -7.066, -7.052776339273938],
  [-2.8539999999999996, -2.8539999999999996, -2.836445737876892],
  [-2.852, -2.852, -2.855618641886548],
  [-0.001999999999999792, -0.001999999999999792, -0.01999998666666725],
  [2.0816681711721685e-16, 2.0816681711721685e-16, 2.0816681711721685e-15],
  [0.002000000000000208, 0.002000000000000208, 0.019999986666671413],
  [2.8520000000000003, 2.8520000000000003, 2.8556186418865437],
  [2.854, 2.854, 2.8364457378768875],
  [7.066000000000001, 7.066000000000001, 7.0527763392739455],
  [7.0680000000000005, 7.0680000000000005, 7.066940848453377],
  [7.07, 7.07, 7.081077089878837],
  [8.422, 8.422, 8.429686746452397],
  [8.424000000000001, 8.424000000000001, 8.418910641243006],
  [8.426, 8.426, 8.408100860402287]
]
```

这些结果很有趣，因为列表中包含了 `19` 个零点（诚然，从 `2000` 个可能的点中大大减少了）。然而，如果仔细观察，它们聚集在 `-8.41`、`-7.07`、`-2.84`、`0`、`2.84`、`7.07` 和 `8.41` 附近。问题是，这些集群的出现是因为确实有那么多零点，还是因为两个函数在超过一个步长（`0.02`）的范围内足够接近（例如，差值不超过 `0.02`）但并未相交？解决这个问题的一种方法是在 `map()` 步骤中收集函数差值的相关信息，如下所示：

```
intersection_points =
(-10..10).step(0.002).map do |x|
  [x, x, 10*Math.sin(x), x - (10*Math.sin(x))]
end.select do |four_tuple|
  four_tuple[3].abs < 0.02
end
```

得出如下结果：

```
=> [
  [-8.426, -8.426, -8.408100860402287, -0.017899139597712832],
  [-8.424, -8.424, -8.418910641243016, -0.005089358756983131],
  [-8.422, -8.422, -8.429686746452397, 0.00768674645239642],
  [-7.07, -7.07, -7.081077089878837, 0.011077089878837043],
  [-7.068, -7.068, -7.066940848453371, -0.0010591515466282786],
  [-7.066, -7.066, -7.052776339273938, -0.013223660726061404],
  [-2.8539999999999996, -2.8539999999999996, -2.836445737876892, -0.017554262123107733],
  [-2.852, -2.852, -2.855618641886548, 0.0036186418865482572],
  [-0.001999999999999792, -0.001999999999999792, -0.01999998666666725, 0.01799998666666746],
  [2.0816681711721685e-16, 2.0816681711721685e-16, 2.0816681711721685e-15, -1.8735013540549517e-15],
  [0.002000000000000208, 0.002000000000000208, 0.019999986666671413, -0.017999986666671207],
  [2.8520000000000003, 2.8520000000000003, 2.8556186418865437, -0.0036186418865433723],
  [2.854, 2.854, 2.8364457378768875, 0.01755426212311262],
  [7.066000000000001, 7.066000000000001, 7.0527763392739455, 0.013223660726055186],
  [7.0680000000000005, 7.0680000000000005, 7.066940848453377, 0.0010591515466238377],
  [7.07, 7.07, 7.081077089878837, -0.011077089878837043],
  [8.422, 8.422, 8.429686746452397, -0.00768674645239642],
  [8.424000000000001, 8.424000000000001, 8.418910641243006, 0.005089358756995566],
  [8.426, 8.426, 8.408100860402287, 0.017899139597712832]
]
```

在所有情况下，函数似乎都在两个或三个步长的区间内交叉，因为第三个元素（差值）改变了符号。此时，我们需要将这些点划分到不同的集群中。当集群内的 `x` 值并不完全相同时，按 `x` 值进行分组会比较棘手。不过，我们可以调整机制。一般来说，如果两个交点的 `x` 值彼此相差在 0.01 以内（连续五个点），则可以认为它们属于同一个集群。此外，上面的点列表是按 `x` 排序的，因此只需检查下一个点的 `x` 值即可。如下所示：

```
intersection_clusters =
intersection_points.reduce([]) do |cluster_list, zero_four_tuple|
  if !cluster_list[-1].nil? &&
    cluster_list[-1][0][0] - zero_four_tuple[0] > -0.01
    cluster_list[-1].push zero_four_tuple
  else
    cluster_list.push [zero_four_tuple]
  end
  cluster_list
end
```

这得出一个有趣的结果：

```python
[
[
[-8.426, -8.426, -8.408100860402287, -0.017899139597712832],
[-8.424, -8.424, -8.418910641243016, -0.005089358756983131],
[-8.422, -8.422, -8.429686746452397, 0.00768674645239642]
],
[
[-7.07, -7.07, -7.081077089878837, 0.011077089878837043],
[-7.068, -7.068, -7.066940848453371, -0.0010591515466282786],
[-7.066, -7.066, -7.052776339273938, -0.013223660726061404]
],
[
[-2.8539999999999996, -2.8539999999999996, -2.836445737876892, -0.017554262123107733],
[-2.852, -2.852, -2.855618641886548, 0.0036186418865482572]
],
[
[-0.001999999999999792, -0.001999999999999792, -0.01999998666666725, 0.01799998666666746],
[2.0816681711721685e-16, 2.0816681711721685e-16, 2.0816681711721685e-15, -1.8735013540549517e-15],
[0.002000000000000208, 0.002000000000000208, 0.019999986666671413, -0.017999986666671207]
],
[
[2.8520000000000003, 2.8520000000000003, 2.8556186418865437, -0.0036186418865433723],
[2.854, 2.854, 2.8364457378768875, 0.01755426212311262]
],
[
[7.066000000000001, 7.066000000000001, 7.0527763392739455, 0.013223660726055186],
[7.0680000000000005, 7.0680000000000005, 7.066940848453377, 0.0010591515466238377],
[7.07, 7.07, 7.081077089878837, -0.011077089878837043]
],
[
[8.422, 8.422, 8.429686746452397, -0.00768674645239642],
[8.424000000000001, 8.424000000000001, 8.418910641243006, 0.005089358756995566],
[8.426, 8.426, 8.408100860402287, 0.017899139597712832]
]
]
```

这清晰地显示了七个明显的零点簇。此时，你可以决定只接受簇中的第一个零点，认为其"足够好"。

```ruby
intersection_clusters.map do |cluster|
cluster[0][0]
end
```

或者，你可能希望将 `x` 值设为簇中所有 `x` 值的平均值，并四舍五入到一个美观的数字：

```ruby
intersection_clusters.map do |cluster|
(cluster.reduce(0) do |sum, zero_four_tuple|
sum + zero_four_tuple[0]
end / cluster.size).round(4)
end
```

这将得到以下结果：

```ruby
=> [-8.424, -7.068, -2.853, 0.0, 2.853, 7.068, 8.424]
```

完整代码如下：

对于一个相当复杂的计算来说，只用十八行代码就完成，真是令人印象深刻。

**练习：** 你可以检查一个簇中的交叉点，这种情况发生在某个簇的元组差值（元组中的第四个元素）同时包含正值和负值时。

## 按区号分组

有时，你需要根据某个元素的函数来对数据集进行分组。考虑以下电话号码列表：

```
John,613-555-1234
Matt,613-555-2345
Andrew,613-555-3456
Bob,416-555-2222
Sheila,905-555-7777
Sara,416-555-9999
Grocery,416-492-9999
Pizza,416-967-1111
Gabers,613-310-7777
Shah,905-555-1212
```

你需要根据区号来对列表进行分组。本例中，区号是每个电话号码的前三位数字。

通过对电话号码执行 `split()` 并取第一个元素（`索引 0`），就可以提取出区号。例如：

```ruby
phone_number = "613-555-1234"
area_code = phone_number.split("-")[0]
=> "613"
```

一旦获取到区号，它就可以作为以区号为键的 `Hash` 的键。在我们的示例中：

```ruby
area_code_groups[area_code] = phone number
```

但是，如果某个区号下有多个号码，我们就需要一个数组。对于新的区号，如果对应的数组不存在，则必须创建一个新数组，如下所示：

```ruby
if area_code_groups[area_code].nil?
area_code_groups[area_code] = [phone_number]
else
area_code_groups[area_code].push phone_number
end
```

因此，完整的代码如下：

```ruby
phonelist_text.split("\n").map do |row|
row.split(",")
end.map do |split_row|
[split_row[0], split_row[1].split("-")]
end.reduce({}) do |area_code_groups, ac_split_row|
if area_code_groups[ac_split_row[1][0]].nil?
area_code_groups[ac_split_row[1][0]] = [ [ac_split_row[0], ac_split_row[1].join("-")].join(",") ]
else
area_code_groups[ac_split_row[1][0]].push [ac_split_row[0], ac_split_row[1].join("-")].join(",")
end
area_code_groups
end
```

# 滑动窗口平均值

有时，你拥有的数据集波动剧烈，但长期趋势却遵循固定模式。假设你有一份渥太华 10 月 1 日至 31 日的秋季气温列表。
```
dailytemps = [10,20,22,23,15,18,17,10,10,10,17,20,21,18,8,16,20,8,7,8,8,18,23,24\
,5,6,15,18,19,0,2]
```

你希望计算最近五天的平均值。对于前四天，只需计算之前四天的平均值。对于前三天，只需计算之前三天的平均值，以此类推。

我们可以使用`reduce`，因为我们需要进行依赖先前计算记录的运算。让我们看看能否找出最近五天的气温。初始化的记录是一个包含五个`nil`的数组。每次迭代时，我们将当前气温添加到记录末尾，并移除记录中最旧的那个。为节省篇幅，我们只对前七个元素进行测试。示例如下：
```
initial_last_5 = [nil,nil,nil,nil,nil]
dailytemps.first(7).reduce(initial_last_5) do |memo, temperature|
memo.push temperature
memo.shift
puts "After pushing #{temperature}, the last 5 are: #{memo.inspect}"
memo
end
After pushing 10, the last 5 are: [nil, nil, nil, nil, 10]
After pushing 20, the last 5 are: [nil, nil, nil, 10, 20]
After pushing 22, the last 5 are: [nil, nil, 10, 20, 22]
After pushing 23, the last 5 are: [nil, 10, 20, 22, 23]
After pushing 15, the last 5 are: [10, 20, 22, 23, 15]
After pushing 18, the last 5 are: [20, 22, 23, 15, 18]
After pushing 17, the last 5 are: [22, 23, 15, 18, 17]
=> [22, 23, 15, 18, 17]
```

成功地在每一步后保存了最近五天的气温。现在，我们还需要保存平均值。为此，我们必须从一个包含最近五天数据的初始数组和一个初始平均值开始。`Hash`是用于记录的良好结构。第一个键是平均值，最后一个键是最近五个元素的数组。当我们添加气温时，需要将其添加到`memo[:last_5]`，而不是直接添加到`memo`。此外，最近五天气温的平均值等于非`nil`元素的总和除以非`nil`元素的个数。（`compact`去除`nil`元素，`length`是剩余非`nil`元素的数量）。例如：
```
example_5 = [nil,nil,6,8,13]
average = (example_5.compact.reduce(0,:+)) / (example_5.compact.length)
=> 9
```
9 是对三个非`nil`数字计算平均值的正确结果。

现在，我们将其应用于计算，如下所示：
```
initial_hash = {average: 0, last_5: [nil,nil,nil,nil,nil]}
dailytemps.first(7).reduce(initial_hash) do |memo, temperature|
memo[:last_5].push temperature
memo[:last_5].shift
memo[:average] =
(memo[:last_5].compact.reduce(0,:+)) / (Float(memo[:last_5].compact.leng\
th))
puts "After pushing #{temperature}, the last 5 are: #{memo.inspect}"
memo
end
```
输出结果如下：
```
After pushing 10, the memo is: {:average=>10.0, :last_5=>[nil, nil, nil, nil, 10\
]}
After pushing 20, the memo is: {:average=>15.0, :last_5=>[nil, nil, nil, 10, 20]}
After pushing 22, the memo is: {:average=>17.333333333333332, :last_5=>[nil, nil\
, 10, 20, 22]}
After pushing 23, the memo is: {:average=>18.75, :last_5=>[nil, 10, 20, 22, 23]}
After pushing 15, the memo is: {:average=>18.0, :last_5=>[10, 20, 22, 23, 15]}
After pushing 18, the memo is: {:average=>19.6, :last_5=>[20, 22, 23, 15, 18]}
After pushing 17, the memo is: {:average=>19.0, :last_5=>[22, 23, 15, 18, 17]}
=> {:average=>19.0, :last_5=>[22, 23, 15, 18, 17]}
```

我们越来越接近目标了。我们已经得到了最近一次平均值和最近五天的数据，但还需要保存所有平均值。这意味着`memo[:average]`应被保存到一个滑动平均值数组中，并在每次迭代时追加。示例如下：
```
initial_hash = {average: 0,
sliding_averages: [],
last_5: [nil,nil,nil,nil,nil]
}
dailytemps.first(7).reduce(initial_hash) do |memo, temperature|
memo[:last_5].push temperature
memo[:last_5].shift
memo[:average] = (memo[:last_5].compact.reduce(0,:+)) / (Float(memo[:last_5]\
.compact.length))
memo[:sliding_averages].push memo[:average]
puts "After pushing #{temperature}, the memo is: #{memo.inspect}"
memo
end
```
然后，最终我们只需要保留滑动平均值数组。我们可以去掉前七个数据，并处理整个气温输入数组，如下所示：
```
initial_hash = {average: 0,
sliding_averages: [],
last_5: [nil,nil,nil,nil,nil]
}
dailytemps.reduce(initial_hash) do |memo, temperature|
memo[:last_5].push temperature
memo[:last_5].shift
memo[:average] = (memo[:last_5].compact.reduce(0,:+)) / (Float(memo[:last_5]\
.compact.length))
memo[:sliding_averages].push memo[:average]
puts "After pushing #{temperature}, the memo is: #{memo.inspect}"
memo
end[:sliding_averages]
```
在返回最终答案之前，有一大堆 `puts` 语句。检查它们，观察每次迭代后的输出是否合理。最终答案如下：
```
=> [10.0, 15.0, 17.333333333333332, 18.75, 18.0, 19.6, 19.0, 16.6, 14.0, 13.0, 1\
2.8, 13.4, 15.6, 17.2, 16.8, 16.6, 16.6, 14.0, 11.8, 11.8, 10.2, 9.8, 12.8, 16.2\
, 15.6, 15.2, 14.6, 13.6, 12.6, 11.6, 10.8]
```
为了清理代码，我们实际上不需要保留`memo[:average]`或`puts`语句。最终版本如下：


```
sliding_averages =
dailytemps.reduce(sliding_averages:[], last_5:[nil,nil,nil,nil,nil]) do |memo, t\ emperature|
memo[:last_5].push temperature
memo[:last_5].shift
memo_average = (memo[:last_5].compact.reduce(0,:+)) / (Float(memo[:last_5].c\
ompact.length))
memo[:sliding_averages].push memo_average
memo
end[:sliding_averages]
```


# 数据集反规范化

有时，你会有一个规范化后的数据集，希望将其反规范化以便于人类使用。

例如，你可能有一个从 SQL 数据库中提取的数据集。第一个表是一个名称列表，通过唯一 ID 标识，每行格式为 `{id,name}`。（注意有两个同名的 John）。

```
1,John
2,Jack
3,Jim
4,Jared
5,John
```

然后，你有一个电话号码列表，包含唯一 ID 和唯一用户 ID，但电话号码可以重复（因为两个人可能共用一个电话）；不过，每条记录都与一个用户关联。格式为 `{id, user_id, phone_number}`。例如：

```
1,1,555-2344
2,1,555-1111
3,2,555-0000
4,3,555-0000
5,4,555-6666
7,5,555-7777
8,5,555-2344
```

然而，你希望反规范化后的列表看起来像下面这样，便于用户使用：

```
John, 555-2344, 555,1111
Jack, 555-0000
Jim, 555-0000
Jared, 555-6666
John, 555-7777, 555-2344
```

这甚至不符合数据规范化的第一范式，但对人类来说没问题。首先，你已经将数据集提取到数组 `names` 和 `phones` 中，如下所示：

```
irb> names
=> [[1,"John"],[2,"Jack"],[3,"Jim"],[4,"Jared"],[5,"John"]]
irb> phones
=> [[1,1,"555-2344"],[2,1,"555-1111"],[3,2,"555-0000"],[4,3,"555-0000"],[5,4,"555-6666"],[7,5,"555-7777"],[8,5,"555-2344"]]
```

现在，你想要将每个电话号码与每个姓名进行匹配：

```
cross_join = names.map do |name|
  phone.map do |phone|
    [name,phone]
  end
end
```

然后，你只想选择那些电话号码的 `user_id`（索引 `[1][1]`）与姓名的 `id`（索引 `[0][0]`）匹配的姓名/电话对。例如：

```
name_phones = cross_join.select do |np|
  np[0][0] == np[1][1]
end
```

之后，你需要按姓名分组，但由于存在两个同名的 John，你必须使用 `name_id`：

```
grouped_name_phones = name_phones.reduce({}) do |namelist, np|
  if namelist[np[0]].nil?
    namelist[np[0]] = np[1][2]
  else
    namelist[np[0]].push np[1][2]
  end
  namelist
end
```

最后，你希望只将姓名和电话号码保存到一个数组中，如下所示：

```
grouped_name_phones.map do |name,phones|
  [name[1],phones.map{|p|p[2]}.join(",")]
end
```

# 毕达哥拉斯三元组

勾股定理是数学中的一个重要定理。它指出，对于一个直角三角形，斜边的平方等于另外两条边的平方和。如果直角三角形的三条边长均为整数，那么就构成一个毕达哥拉斯三元组。例如，`[3,4,5]`或`[5,12,13]`或`[8,15,17]`都是这样的例子。世界各地的数学老师会使用这些数组来出题，这样他们就不用费心去批改那些不精确的长除法或平方根计算了。

如何解决生成毕达哥拉斯三元组的问题呢？

首先，斜边是最长的那条边。因此，我们必须搜索斜边所有可能的整数值。我们从 5 开始，因为已知 5 是最小的毕达哥拉斯三角形的斜边长度。我们知道必须返回一个三元组，也就是一个包含三个元素的数组。我们暂时从 5 开始，到 17 结束：

```
(5..17).map do |hypotenuse|
  [nil, nil, hypotenuse]
end
=> [[nil, nil, 5], [nil, nil, 6], [nil, nil, 7], [nil, nil, 8], [nil, nil, 9], [nil, nil, 10], [nil, nil, 11], [nil, nil, 12], [nil, nil, 13], [nil, nil, 14], [nil, nil, 15], [nil, nil, 16], [nil, nil, 17]]
```

目前为止一切顺利。然而，对于对边和邻边，我们需要的是真实的数字，而不是`nil`。我们只需将它们放入嵌套循环中。对边只需从 1 搜索到当前斜边，而邻边只需从 1 搜索到当前对边，如下所示：

```
(5..17).map do |hypotenuse|
  (1..hypotenuse).map do |opposite|
    (1..opposite).map do |adjacent|
      [adjacent, opposite, hypotenuse]
    end
  end
end
=> [[[[1, 1, 5]], [[1, 2, 5], [2, 2, 5]], [[1, 3, 5], [2, 3, 5], [3, 3, 5]], [[1, 4, 5], [2, 4, 5], [3, 4, 5], [4, 4, 5]], [[1, 5, 5], [2, 5, 5], [3, 5, 5], [4, 5, 5], [5, 5, 5]]], [[[1, 1, 6]], [[1, 2, 6], [2, 2, 6]], [[1, 3, 6], [2, 3, 6], [3, 3, 6]], [[1, 4, 6], [2, 4, 6], [3, 4, 6], [4, 4, 6]], [[1, 5, 6], [2, 5, 6], [3, 5, 6], [4, 5, 6], [5, 5, 6]], [[1, 6, 6], [2, 6, 6], [3, 6, 6], [4, 6, 6], [5, 6, 6], [6, 6, 6]]], [[[1, 1, 7]], [[1, 2, 7], [2, 2, 7]], [[1, 3, 7], [2, 3, 7], [3, 3, 7]], [[1, 4, 7], [2, 4, 7], [3, 4, 7], [4, 4, 7]], [[1, 5, 7], [2, 5, 7], [3, 5, 7], [4, 5, 7], [5, 5, 7]], [[1, 6, 7], [2, 6, 7], [3, 6, 7], [4, 6, 7], [5, 6, 7], [6, 6, 7]], [[1, 7, 7], [2, 7, 7], [3, 7, 7], [4, 7, 7], [5, 7, 7], [6, 7, 7], [7, 7, 7]]], [[[1, 1, 8]], [[1, 2, 8], [2, 2, 8]], [[1, 3, 8], [2, 3, 8], [3, 3, 8]], [[1, 4, 8], [2, 4, 8], [3, 4, 8], [4, 4, 8]], [[1, 5, 8], [2, 5, 8], [3, 5, 8], [4, 5, 8], [5, 5, 8]], [[1, 6, 8], [2, 6, 8], [3, 6, 8], [4, 6, 8], [5, 6, 8], [6, 6, 8]], [[1, 7, 8], [2, 7, 8], [3, 7, 8], [4, 7, 8], [5, 7, 8], [6, 7, 8], [7, 7, 8]], [[1, 8, 8], [2, 8, 8], [3, 8, 8], [4, 8, 8], ...]]]
```

返回的结果很混乱，但它包含了所有合理的组合。现在，我们有了所有数字，但我们不想保留那些不是三元组的数字；我们只保留三元组。这意味着，对于“对边”，我们只应选择那些能与其他两条边构成毕达哥拉斯三元组的值。换句话说，在最后一个`map`调用之前，应该加上一个`select`，其判断条件就是毕达哥拉斯三元组的规则：

```
(5..17).map do |hypotenuse|
  (1..hypotenuse).map do |opposite|
    (1..opposite).select do |adjacent|
      adjacent*adjacent + opposite*opposite == hypotenuse*hypotenuse
    end.map do |selected_adjacent|
      [selected_adjacent, opposite, hypotenuse]
    end
  end
end
=> [[[], [], [], [[3, 4, 5]], []], [[], [], [], [], [], []], [[], [], [], [], [], [], []], [[], [], [], [], [], [], [], []], [[], [], [], [], [], [], [], [], []], [[], [], [], [], [], [], [[6, 8, 10]], [], []], [[], [], [], [], [], [], [], [], [], [], []], [[], [], [], [], [], [], [], [], [], [], [], []], [[], [], [], [], [], [], [], [], [], [], [[5, 12, 13]], []], [[], [], [], [], [], [], [], [], [], [], [], [], [], []], [[], [], [], [], [], [], [], [], [], [], [[9, 12, 15]], [], [], []], [[], [], [], [], [], [], [], [], [], [], [], [], [], [], []], [[], [], [], [], [], [], [], [], [], [], [], [], [[8, 15, 17]], [], []]]
```

看起来好多了，但我们得到了很多空数组，并且它们嵌套了三层深。这是因为`map`总是返回一个数组。因此，嵌套的`map`调用会返回数组的数组。首先要做的是尝试展平外层的数组，如下所示：

```
(5..17).map do |hypotenuse|
  (1..hypotenuse).map do |opposite|
    (1..opposite).select do |adjacent|
      adjacent*adjacent + opposite*opposite == hypotenuse*hypotenuse
    end.map do |selected_adjacent|
      [selected_adjacent, opposite, hypotenuse]
    end
  end
end.flatten
=> [3, 4, 5, 6, 8, 10, 5, 12, 13, 9, 12, 15, 8, 15, 17]
```

这是不对的，因为我们在展平数组的数组的数组时做得太过分了。如果我们把它们按三个一组进行分组，实际上得到的正是斜边从 5 到 17 的毕达哥拉斯三元组。但是，我们希望保留实际的三元组。因此，我们需要逐次展平每个`map`的结果。在 Ruby 中，有一个叫做`flat_map`的函数可以做到这一点。我们需要展平外层的`map`和第一个内层的`map`，以便得到一个由三元组数组组成的数组。我还将数字上限提高到 26：

```
(5..26).flat_map do |hypotenuse|
  (1..hypotenuse).flat_map do |opposite|
    (1..opposite).select do |adjacent|
      adjacent*adjacent + opposite*opposite == hypotenuse*hypotenuse
    end.map do |selected_adjacent|
      [selected_adjacent,opposite,hypotenuse]
    end
  end
end
=> [[3, 4, 5], [6, 8, 10], [5, 12, 13], [9, 12, 15], [8, 15, 17], [12, 16, 20], \
[15, 20, 25], [7, 24, 25], [10, 24, 26]]
```

这就是我们想要的结果。你可以将数字上限调得更高，看看会发生什么。
