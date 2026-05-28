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