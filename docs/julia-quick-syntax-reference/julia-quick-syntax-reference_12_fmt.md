# 嵌套函数：

以下是 Julia 中嵌套函数的示例：

```julia
function f1(x)
    function f2(x, y)
        x + y
    end
    f2(x, 2)
end
```