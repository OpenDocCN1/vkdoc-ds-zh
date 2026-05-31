# 嵌套函数：

`JULIA`

```julia
function f1(x)
    function f2(x, y)
        x + y
    end
    f2(x, 2)
end
```



