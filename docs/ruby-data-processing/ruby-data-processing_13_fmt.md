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