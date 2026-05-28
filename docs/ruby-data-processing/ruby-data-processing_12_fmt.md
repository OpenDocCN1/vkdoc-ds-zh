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
