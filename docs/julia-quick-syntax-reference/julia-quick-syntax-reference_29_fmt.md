# ..如果发生错误该怎么做，例如使用以下方式发送错误消息：

`error("我的详细消息")`

`end`

你也可以捕获*特定*类型的异常。在下面的代码片段中，如果表示数据的字典中未找到特定的键，你可以使用异常返回 `missing`：

```
function volume(region, year)
    try
        return data["volume", region, year]
    catch e
        if isa(e, KeyError)
            return missing
        end
        rethrow(e)
    end
end
```