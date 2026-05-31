# 2. 函数概述与简单示例

Ruby 库包含 `Enumerable` 模块。该库包含 `map()`、`reduce()`、`select()` 等函数。本节将概述使用这三个函数的代码中不同部分的语法和含义。

如果你能轻松地完成本节内容，既能将代码输入 `irb` 又能理解结果，那么你将处于一个有利的位置，能够通过下一章中的复杂示例和逆向工程来加深理解。



## Map（映射）

Ruby Enumerable 库的这个函数看似简单，实则意义深远。`map()` 方法应用于数组或哈希。它的任务是将一个函数或代码块应用于数组中的每个成员，并返回一个新数组。

因此，当你看到以下代码时：

```
def f(x)
x*x
end
output_array =
[1,2,3,4,5].map do |number|
f(number)
end
```

你可以这样理解：对 `[1,2,3,4,5]` 中的每个 `number`，应用 `f(x)`，最终返回数组 `[f(1), f(2), f(3), f(4), f(5)]`。在这个例子中，结果是 `[1,4,9,16,25]`。

用传统的命令式编程可以这样编码：

```
output_array = []
for number in 1..5
output_array.push( f(number) )
end
```

使用 `map` 的另一个例子：

```
output_array =
[1,2,3,4,5].map do |number|
number*number
end
```

或者，你也可以这样编码：

```
output_array =
[1,2,3,4,5].map{|element| element*element}
```

`[1,2,3,4,5]` 是数组。要应用到每个元素的函数（或代码块）是 `{|element| element*element }`。

使用 `{ }` 语法或 `do`-`end` 语法时，竖线内的对象（由 `element` 表示）是当前正被代码块处理的数组元素，而任何函数或代码块中的最后一个对象就是返回值，因此 `element*element` 被返回。最终结果如下：

```
[1,4,9,16,25]
```

假设你想向一个绘制数值平方的图形程序提供数据：

```
[1,2,3,4,5].map{|element| [element, element*element]}
```

在这个例子中，每个“点”都是一个包含元素及其平方的元组（数组）。这将返回以下结果：

```
[[1,1],[2,4],[3,9],[4,16],[5,25]]
```

区别在于，代码块每次都返回一个包含数字及其平方的数组，因此结果是一个数组的数组。

由于 `map` 返回一个数组，你可以级联调用 `map`。例如，你可以先用一个 map 代码块计算每个元素的平方，然后再用第二个 map 代码块加上 100。例如：

```
[1,2,3,4,5].map do |number|
number*number
end.map do |square|
square + 100
end
=> [101, 104, 109, 116, 125]
```

许多更复杂的操作都可以通过级联 `map` 调用来解决。

## Reduce（归约）

Ruby Enumerable 库的这个函数比 `map()` 更复杂，但也非常强大。该方法应用于一个集合（例如，一个数组或哈希）。`reduce()` 的任务是将一个函数累积地应用于集合中的每个成员，然后返回一个对象（可以是数组、单个值、哈希或任何东西）。

例如：

```
[1,2,3,4,5].reduce(0, :+)
```

这就像是在说：对于列表中的每个成员，将该成员加到 `memo` 上，并为下一个元素保存这个 `memo`。`memo` 的初始值是 `0`（第一个参数）。另一种编码方式如下：

```
memo = 0
[1,2,3,4,5].each{|element| memo = memo.+(element)}
```

或者，你可以向 `reduce` 传递一个要运行的代码块。该代码块位于右括号之后，并由 `do`-`end` 结构或 `{}` 结构包围。发出的变量总是第一个是 `memo`，第二个是 `element`。（在这个例子中，`memo` 的初始值是 33）。

```
[1,2,3,4,5].reduce(33) do |memo, element|
memo.+(element)
end
memo
=> 48
```

（你不必非得叫它们 “memo” 或 “element”，但它们的行为模式就是这样）。每次迭代后，memo 会取代码块中最后一个对象的值。例如：

```
[1,2,3,4,5].reduce(33) do |memo, element|
memo.+(element)
77.7
end
=> 77.7
```

这段代码返回了 77.7，因为它是代码块中的最后一个对象。

另一件要记住的事情是，代码块返回的 memo 必须与 `reduce` 函数发出的 memo 属于同一类。例如，当你使用 `reduce()` 构建一个哈希对象时，你必须返回整个哈希对象：

```
[1,2,3,4,5].reduce({}) do |memo, element|
memo[element] = element.to_s
end
IndexError: index 2 out of string
```

这里出现了一个错误，因为迭代的第一次运行返回了 `memo[element]`，这是一个 `String` 类，但输入期望它是一个 `Hash`。要纠正它，请确保 `Hash` 类的 `memo` 被明确地作为最后一个对象返回，如下所示：

```
[1,2,3,4,5].reduce({}) do |memo, element|
memo[element] = element.to_s
memo
end
=> {1=>"1", 2=>"2", 3=>"3", 4=>"4", 5=>"5"}
```

这样之所以能成功，是因为 `memo` 是一个 `Hash` 类，并且每次都被返回。

你可以将 `reduce` 操作理解为：对每个元素进行操作，并保存某种备忘录（memo）以传递给下一个元素的操作。在之前计算五个数之和的例子中，

```
[1,2,3,4,5].reduce(0,:+)
```

或者

```
[1,2,3,4,5].reduce(0) do |memo, element|
memo + element
end
```

你用一个 0 初始化了要记忆的量，并告诉它记住将当前元素加到先前归约的量上并保存它。`memo` 的初始值是一个整数，`reduce` 代码块返回的最后一个对象也是一个整数。

你也可以在 memo 中记住其他东西。例如，你可以保存一个序列的最后两个数字。你可以从初始值 `[nil,nil]` 开始，作为最后两个要记住的东西。在每次迭代中，你将元素推入（push）memo（数组的末尾），然后移出（shift）数组的第一个元素。

```
initial_memo = [nil,nil]
last_2 =
(1..10).reduce(initial_memo) do |memo, element|
initial_memo.push( element )
initial_memo.shift
initial_memo
end
=> [9,10]
```

一般来说，如果在处理当前元素时需要记住关于之前使用过的元素的信息，请使用 `reduce()`。如果不需要记住任何东西，请使用 `map()`。

有时 `reduce` 会返回一个数组，这种情况下你可以将其与其他 `reduce` 调用或 `map` 调用级联。例如，如果你想计算平方和，你可以使用 `map` 调用先计算每个元素的平方，然后使用 `reduce` 调用将它们相加。例如：

```
[1,2,3,4,5].map do |number|
number*number
end.reduce(0) do |sum, square|
sum + square
end
=> 55
```

或者，也许你想找出一个序列中最后五个数字里的奇数：

```
sequence = [1,2,3,455,5,6,4,3,45,66,77,54,23,4,55,6,7]
sequence.reduce([nil,nil,nil,nil,nil]) do |memo, number|
memo.push(number)
memo.shift
memo
end.select do |one_of_last_5|
one_of_last_5%2 == 1
end
=> [23, 55, 7]
```

如果这看起来复杂且充满惯用法，请不要担心，因为我们会在本书后面探讨这些编程机制。简而言之，`reduce` 可以在数据处理级联的两侧使用。并且请始终记住，输入的 memo 必须与输出的 memo 属于相同的类或结构。

### 简单的 Reduce 示例

我将在这里向你展示如何使用 `reduce()`、`map()` 和 `select()` 来实现一些数组已有的函数。

#### uniq（去重）

这个函数用于找出数组中的一组唯一元素。例如：

```
irb> [1,3,5,6,7,8,6,6,1,1].uniq
=> [1,3,5,6,7,8]
```

要实现 `uniq`，我们需要一种识别唯一元素的方法。首先，要意识到哈希 `Hash` 的键是唯一的。因此，当我们运行 `reduce` 时，只需将每个元素作为哈希的键放入。如果该元素已经存在，哈希中的值将被覆盖。所以，memo 的初始值应该是 `{}`。

```
[1,3,5,6,7,8,6,6,1,1].reduce({}) do |memo, element|
memo[element]=element
memo
end
⇒ {1⇒1, 3⇒3, 5⇒5, 6⇒6, 7⇒7, 8⇒8}
```

差不多了。现在，我们只需使用 `map` 取出所有的键：

```
[1,3,5,6,7,8,6,6,1,1].reduce({}) do |memo, element|
memo[element]=element
memo
end.map do |key, value|
key
end
=> [1, 3, 5, 6, 7, 8]
```


好的，作为一名高级文档工程师和翻译员，我将按照您的要求，在保留所有 Markdown 格式和代码内容的前提下，将给定的英文文本翻译成中文。


#### `reverse`

该函数用于反转数组中元素的顺序：

```
irb> [1,3,5,6,7,8,6,0].reverse
=> [0, 6, 8, 7, 6, 5, 3, 1]
```

解决此问题的一种方法是将每个元素推入一个栈中。栈就是一个数组，你使用 `unshift` 将元素推入前端，使用 `shift` 从前端弹出元素。`reduce` 备忘录的初始值是一个空数组。我添加了一个 `print` 语句，以便你可以亲眼看到备忘录是如何构建的。

```
[1,3,5,6,7,8,6,0].reduce([]) do |memo, element|
memo.unshift element
puts "After unshifting element: #{element}, the memo is: #{memo.inspect}"
memo
end
```

输出如下：

```
After unshifting element: 1, the memo is: [1]
After unshifting element: 3, the memo is: [3, 1]
After unshifting element: 5, the memo is: [5, 3, 1]
After unshifting element: 6, the memo is: [6, 5, 3, 1]
After unshifting element: 7, the memo is: [7, 6, 5, 3, 1]
After unshifting element: 8, the memo is: [8, 7, 6, 5, 3, 1]
After unshifting element: 6, the memo is: [6, 8, 7, 6, 5, 3, 1]
After unshifting element : 0, the memo is: [0, 6, 8, 7, 6, 5, 3, 1]
=> [0, 6, 8, 7, 6, 5, 3, 1]
```

#### `max`

该函数用于查找数组中的最大元素：

```
irb> [1,3,5,6,7,8,6,0].max
=> 8
```

`Reduce` 在这里适用，因为当你遍历数组时，会将最大值保存在备忘录中。这个示例甚至不需要显式的初始值，因为第一个元素就是备忘录的默认值。

```
[4,3,5,6,7,8,6,0].reduce do |memo, element|
if element > memo
memo = element
else
memo = memo
end
puts "The largest element after checking #{element} is #{memo}"
memo
end
The largest element after checking 3 is 4
The largest element after checking 5 is 5
The largest element after checking 6 is 6
The largest element after checking 7 is 7
The largest element after checking 8 is 8
The largest element after checking 6 is 8
The largest element after checking 0 is 8
=> 8
```

当然，简洁的编码方式如下：

```
[4,3,5,6,7,8,6,0].reduce do |memo, element|
(element > memo) ? element : memo
end
=> 8
```

## `Select`

Ruby `Enumerable` 库中的这个函数非常简单。`select()` 方法应用于数组或哈希。`select()` 的工作是选择那些对其应用谓词后返回 `true` 的元素。（谓词是一个返回 `true` 或 `false` 的表达式，它在传递给 `select` 的代码块中运行）。

下面的简单示例中，谓词总是返回 `true`，因此选择了集合中的所有成员。在此例中，集合是 `[1,2,3,4,5]`，谓词是 `true`：

```
[1,2,3,4,5].select do |element|
true
end
=> [1,2,3,4,5]
```

一个稍微复杂一点的示例是从原始集合 `[1,2,3,4,5]` 中选择偶数（即谓词为 `element % 2 == 0` 的元素）：

```
[1,2,3,4,5].select do |element|
element % 2 == 0
end
=> [2,4]
```

`Select` 可以级联。假设你想找出 1 到 100 之间以 7 开头的偶数。首先，选择偶数。然后，选择以 7 开头的数字：

```
irb>  (1..100).select{|number| number % 2 == 0}\
irb*> .select{|even_number| even_number.to_s[0] == "7"}
=> [70, 72, 74, 76, 78]
```

你也可以反转级联顺序：

```
irb>  (1..100).select{|number| number.to_s[0] == "7"}\
irb*> .select{|seven_number| seven_number % 2 == 0}
=> [70, 72, 74, 76, 78]
```

由于 `select` 返回一个数组，你可以级联 `select` 调用。例如，你可以使用一个 `select` 代码块选择偶数，然后通过 `map` 调用将它们平方。例如：

```
[1,2,3,4,5].select do |number|
number%2==0
end.map do |even_number|
even_number * even_number
end
=> [4, 16]
```

许多更复杂的操作都可以通过与 `select` 调用级联来解决。

