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

