# 10. Stringr 中的正则表达式

你可以在 `Stringr` 包中使用以下预构建的字符类：

- `[:punct:]` – 标点符号
- `[:alpha:]` – 字母
- `[:lower:]` – 小写字母
- `[:upper:]` – 大写字母
- `[:digit:]` – 数字
- `[:xdigit:]` – 十六进制数字
- `[:alnum:]` – 字母和数字
- `[:cntrl:]` – 控制字符
- `[:graph:]` – 字母、数字和标点符号
- `[:print:]` – 字母、数字、标点符号和空白
- `[:space:]` – 空格字符（基本等同于 `\s`）
- `[:blank:]` – 空格和制表符

示例：

```
str_extract_all("The Howdy Doody Show 123", "[:alpha:]")
[[1]]
[1] "T" "h" "e" "H" "o" "w" "d" "y" "D" "o" "o" "d" "y" "S" "h" "o" "w"
str_extract_all("The Howdy Doody Show 123", "[:digit:]")
[[1]]
[1] "1" "2" "3"
```

在这两个例子中，输出都是一个列表。另请注意，预构建的名称是 `"digit"`，而不是 `"digits"`。

