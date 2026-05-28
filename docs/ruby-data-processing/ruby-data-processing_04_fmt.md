# 1. 基础 Ruby

本节将介绍或帮助你回顾使用 Ruby 命令行的基本方式，以及一些相关的 Ruby 编码技巧。

如果你已经熟悉 Ruby 编程并且对 Ruby 的 Enumerable 库有较好的理解，可以跳过本节。

## 命令行

安装 Ruby 后，你可以在 Windows、Linux 或 Mac OSX 上启动交互式命令行。我使用的是 Windows。

```
C:\>  irb
irb(main):001:0>
```

为简洁起见，我不会每次都写出完整的 `irb` 提示符。

你可以逐行执行 Ruby 语句。每个表达式返回的值前面都会有 `⇒`。

```
irb> a = 4
=> 4
irb> a
=> 4
irb> a + 7
=> 11
```

你也可以将多条语句放在同一行，用分号隔开。

```
irb> list = ["one", "two","three"];1
=>1
irb> list
=>["one", "two", "three"]
```

你也可以编写跨越多行的语句。

```
irb> [2,3,4,5].each do |n|
irb>    if n%2==0
irb>        puts "even"
irb>    else
irb>        puts "odd"
irb>    end
irb> end
=> "even"
=> "odd"
=> "even"
=> "odd"
```

你可以将代码放入一个名为 `sample.rb` 的文件中，并将其放置在与运行 `irb` 相同的目录或文件夹中。

```ruby
1  [2,3,4,5].each do |n|
2      if n%2 == 0
3          puts "even"
4      else
5          puts "odd"
6      end
7  end
```

然后，在 `irb` 中：

```
irb> load "sample.rb"
irb> end
=> "even"
=> "odd"
=> "even"
=> "odd"
irb>
```

你也可以从文本编辑器复制代码块并直接粘贴到命令行中，以获得相同的结果，只要你在缩进时只使用空格。（如果从命令提示符加载文件，使用制表符进行缩进是可以正常工作的，但如果直接将制表符粘贴到 `irb` 中，则会产生错误）。

你可以将本书中的代码示例直接输入到 `irb` 中，或者输入到文本编辑器中，然后按刚才所示的方式加载文件。

## 对象作用域

在 Ruby 程序中，对对象 `obj` 执行方法 `f` 的一般方式如下：

```ruby
obj.f
```

无论你是在 Ruby 程序中还是在命令行中，情况都是如此。当代码位于主程序文件中，或者位于 `irb` 命令行解释器中时，许多函数（例如 `puts`）都会有一个隐含的上下文。这就是为什么你可以这么做：

```ruby
puts "Hello world"
```

你不需要指定一个类来限定 `puts`，因为 `puts` 是底层上下文对象的一个方法。

## 字符串

字符串是所有语言中的基本构造。让我们看看本书中使用的一些基本操作。请自行在 `irb` 命令行中尝试它们。

`length` 或 `size`
- 返回字符串的长度。

`downcase`
- 将所有字母转换为小写。

`upcase`
- 将所有字母转换为大写。

`capitalize`
- 将字符串的第一个字母大写。

`split()`
- 在子字符串中搜索参数字符串，并将字符串分割成一个数组，该数组包含 `split` 参数两侧的子字符串，而 `split` 参数的子字符串被丢弃。如果没有匹配项，则返回包含整个字符串的数组：

```ruby
irb> base_string = "abc def  ghi"
irb> base_string.split(" ")
=> ["abc","def","ghi"]
irb> base_string.split("  ")
=> ["abc def","ghi"]
irb> base_string.split(" d")
=> ["abc","ef ghi"]
irb> base_string.split("efghi")
=> ["abc def ghi"]
```

`join()`
- 将字符串数组的每个元素与 `join` 参数中的字符串作为分隔符连接起来。

```ruby
irb> stringlist = ["This", "is", "a", "sentence"]
irb> stringlist.join(" ")
=> "This is a sentence"
irb> stringlist.join(",")
=> "This,is,a,sentence"
irb> stringlist.join(" A SPACE ")
=> "This A SPACE is A SPACE a A SPACE sentence "
```

字符串插值
- 允许你定义一个带有参数的字符串模板。字符串 `#{}` 为你提供了一个放置变量的位置。

```ruby
irb> first = "Jack"; last = "Black"
irb> full_name = "#{first} #{last}"
=> "Jack Black"
```

- 这在遍历列表时尤其有用：

```ruby
irb> smith_brothers = ["Terry", "Jerry", "Harry"]
irb> smith_brothers.each{|brother| puts "#{brother} Smith"}
Terry Smith
Jerry Smith
Harry Smith
=> ["Terry", "Jerry", "Harry"]
```

- C/C++ 程序员会认识到字符串插值与 C 标准库中的 `sprintf` 函数类似。

## 数组

Ruby 中的数组与其他语言中的数组类似。它们是由整数（例如 0,1,2,...）索引的事物的集合。在 Ruby 中，数组可以在一个索引处包含任何 Ruby 对象。Ruby 中的数组索引从 `0` 开始。

数组实现了 Ruby 的 Enumerable 接口，因此它们将拥有诸如 `each`、`map`、`reduce`、`select` 等关键方法。

### 特殊方法

`compact()`
- 此方法可移除数组中的 `nil` 成员。例如：

```ruby
irb> [1,nil,2,nil,nil,3,nil,[]].compact
=> [1,2,3,[]]
```

`flatten()`
- 此方法可展平任意深度的嵌套数组。处理嵌套的 `map` 和 `reduce` 语句时，此方法非常实用。例如：

```ruby
irb> [[1,[2,3]],4,[5,6]].flatten
=> [1,2,3,4,5,6]
```

`push()`
- 此方法将一个元素推入数组末尾，并返回一个新数组。例如：

```ruby
irb> [1,2,3,4,5].push(666)
=> [1,2,3,4,5,666]
```

`pop`
- 此方法移除数组的最后一个元素并返回该元素，同时返回一个新数组。例如：

```ruby
irb> the_array = [1,2,3,4,5]
irb> tail = the_array.pop
irb> tail
=> 5
irb> the_array
=> [1, 2, 3, 4]
```

`unshift()`
- 此方法将一个元素推入数组的开头，并返回一个新数组。例如：

```ruby
irb> the_array = [1,2,3,4,5]
irb> the_array.unshift(666)
=> [666, 1, 2, 3, 4, 5]
```

`shift`
- 此方法移除数组的第一个元素并返回该元素，同时修改原数组。例如：

```ruby
irb> the_array = [1,2,3,4,5]
irb> head = the_array.shift
irb> head
=> 1
irb> the_array
=> [2, 3, 4, 5]
```

## 哈希（Hash）

`Hash`的另一个术语是“关联数组”或“字典”。`Hash`通过键对象进行索引，每个键对象对应一个值（另一个对象）。

```ruby
irb> newhash = Hash.new
=> {}
irb> newhash2 = {}
=> {}
```

这是创建新哈希的两种方法。哈希的键可以是任何对象或符号。

```markdown
键可以是任何对象，包括符号、字符串、数字、哈希、数组等。使用 `each` 或 `map` 遍历哈希时，它会发出一个包含两个元素的数组（键作为第一个元素，值作为第二个元素），或者直接发出键和值两个元素。（注意，`inspect` 用于检查任何对象的内容。）

```ruby
irb> newhash = {key1: "some_value", key2: 2}
=> {:key1=> "some_value", :key2=>2}
irb> newhash2 = {}
irb> newhash2[:key1] = "key1_symbol"
irb> newhash2["key1"] = "key3_string"
irb> newhash2[3] = 3
irb> newhash2[:key4] = [4,"44"]
irb> newhash2[[3,"33"]] = "wow"
irb> newhash2
=> {:key1=>"key1_symbol", "key2"=>"key2_string", 2=>1, :key4=>[4,"44"], [3,"33]=\
"wow" }
```

```ruby
irb> newhash = {a: 1, b: 2, c: 3}
irb> newhash.each{|e| puts e.inspect}
[:a, 1]
[:b, 2]
[:c, 3]
=> {:a => 1, :b => 2, :c => 3}
irb> newhash.map{|key, value| "The key is #{key} and the value is #{value}"}
=> ["The key is a and the value is 1", "The key is b and the value is 2", "The key is c and the value is 3"]
```

## 块传递语法

Ruby 是支持词法闭包（也称为匿名函数或块）的语言之一。这些函数是动态创建的。函数可以采用已定义函数的形式。例如：

```ruby
irb> def plus_two(a,b)
irb>    a + b + 2
irb> end
irb> plus_two(3,4)
=> 9
```

或者，可以按如下方式定义：

```ruby
irb> plus_two = ->(a,b) do
irb>    a + b + 2
irb> end
```

或者，也可以这样定义：

```ruby
irb> plus_three = ->(a,b){a + b + 3}
irb> plus_three.call(5,7)
=> 15
```

现在，匿名函数或块可以作为参数传递给其他函数。接受函数（已定义的或匿名的）作为参数的函数可以选择在程序流程中执行该函数。假设有一个名为 `printit()` 的函数，定义如下：

```ruby
irb> printit = ->(a) do
irb>    puts a.inspect
irb> end
```

现在，假设想要打印一个范围内的每个元素。可以调用数组的 `each` 方法，它将把传递的块应用于数组的每个元素。

```ruby
irb> (1..5).each(&printit)
```

```text
=> (1..5)
```

也可以直接传递一个代码块来执行。代码块由 `do` 和 `end` 或 `{` 和 `}` 包围。竖线之间的对象可选地传递给块（取决于块的定义），并由块用于执行。块的返回值是块中最后一个表达式的值。

```ruby
irb> (1..5).each do |num|
irb>    puts num.inspect
irb> end
```

你将得到与之前相同的输出。以下方式也是如此：

```ruby
irb> (1..5).each{|num| puts num.inspect }
```

在 Ruby 中，`map`、`reduce` 和 `select` 都接受一个代码块作为参数，该代码块按照函数的定义执行。在本书中，我将最常使用 `do`-`end` 语法，有时也会使用 `{ }` 语法。

## 从文件读取

数据处理中一个常见的操作是读取和写入文件。假设有一个 `names.txt` 文件，内容如下：

```text
1,John
2,Jack
3,Jim
4,Jared
5,John
```

读取它的方式如下：

```ruby
irb> names = File.open("names.txt").read
```

在某些计算机上，换行符由回车符（`\n`）分隔；而在其他计算机上，换行符由换行符和回车符（`\r\n`）分隔。

如果要将此文件拆分为行，可以执行以下操作：

```ruby
irb> names.split("\n")
```

这可能会返回

```text
=>["1,John\r", "2,Jack\r", "3,Jim\r", "4,Jared\r", "5,John\r"]
```

或

```text
=>["1,John", "2,Jack", "3,Jim", "4,Jared", "5,John"]
```

为了确保一致性，请使用 `chomp` 方法，它会移除末尾的空白字符（如果存在）。例如：

```ruby
irb> "John\r".chomp
=> "John"
irb> "John".chomp
=> "John"
```

因此，针对我们的数组，为了确保不会意外捕获多余的换行符，我们可以这样做：

```ruby
irb> names.split("\n").map{|row| row.chomp}
=>["1,John", "2,Jack", "3,Jim", "4,Jared", "5,John"]
```

我假设本书中的示例不使用 `\r\n`，而仅使用 `\n`。如果你的操作系统不支持这种换行符，请在处理数组之前使用 `chomp()` 方法。
```