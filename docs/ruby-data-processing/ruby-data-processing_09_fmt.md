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