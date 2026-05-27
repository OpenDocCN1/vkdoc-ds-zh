# 宏调用

宏定义完成后，可以使用其名称调用它，名称前需要加上 `@` 符号。宏后跟上它接受的表达式，这些表达式在同一行中，用空格分隔（可使用 `begin` 块语法将多个表达式组合在一起）。

例如：

```julia
a = 5
@customLoop 1:4 println(i)
@customLoop 1:a println(i)
@customLoop 1:a if i > 3 println(i) end
@customLoop ["apple", "orange", "banana"] println(i)
@customLoop ["apple", "orange", "banana"] begin print("i: "); println(i) end
```

你可以使用另一个名为 `@macroexpand` 的宏来查看“展开”后的宏看起来是什么样：

```julia
julia> @macroexpand @customLoop 1:4 println(i)
quote
    #= /path/to/source/file/metaprogramming.jl:65 =#
    for #92#i = 1:4
        #= /path/to/source/file/metaprogramming.jl:66 =#
        (Main.println)(#92#i)
    end
end
```

请注意，宏不会创建新的作用域，在宏内部声明或赋值的变量可能会与实际调用宏的位置所在作用域中的变量发生冲突。