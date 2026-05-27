# 字符串宏

最后，一种便捷的宏类型（其冗长但技术上准确的名称是“非标准字符串字面量”）允许开发者对以 `xxx"...text..."` 形式输入的文本执行编译时的自定义操作。这是一个自定义前缀，直接附加到待处理的文本上，并以字符串形式输入（也可以使用三重引号，例如 `xxx"""...多行文本..."""`）。

例如，以下宏定义了将输入文本按自定义的八列格式显示：

```julia
macro print8_str(mystr)
    limits = collect(1:8:length(mystr))
    for (i,j) in enumerate(limits)
        st = j
        en = i==length(limits) ? length(mystr) : j+7
        println(mystr[st:en])
    end
end
```

这些名称以 `_str` 结尾的特殊宏，随后可以使用非标准字符串字面量进行调用，其中前缀与宏名称去掉 `_str` 部分后匹配：

```julia
julia> print8"123456789012345678"
```

你将在下一章“Julia 与其他语言交互”中看到这些宏的应用，其中字符串宏将用于处理代表外语言（C++、Python 或 R）代码的文本。