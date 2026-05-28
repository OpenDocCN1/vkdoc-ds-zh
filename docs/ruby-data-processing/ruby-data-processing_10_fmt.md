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