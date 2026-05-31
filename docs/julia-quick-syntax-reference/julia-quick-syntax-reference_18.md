# 定义宏

定义宏与定义普通函数类似。区别在于，需要使用 `macro` 关键字代替 `function` 关键字，并构建宏应返回的表达式。例如，使用引号块（quote block）时，作为参数的变量需通过美元符号（`$`）进行解引用。这很合理，因为宏的参数是表达式，对绑定到参数的变量使用美元符号，意味着将其内容（一个表达式）注入到引号块中定义的整个表达式的更广泛上下文中。

让我们看一个示例：

```julia
macro customLoop(controlExpr,workExpr)
    return quote
        for i in $controlExpr
            $workExpr
        end
    end
end
```

这里，你使用一个宏来定义通用循环，该循环包含应用于 `for` 循环的控制表达式以及在循环内部求值的一个或多个表达式。

