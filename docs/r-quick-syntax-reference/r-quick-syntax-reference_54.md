# if 语句与逻辑向量

当进行逻辑测试时，被测试的对象长度往往大于一。R 语言不喜欢这种情况，并会给出警告，提示只使用了第一个逻辑元素。假设你想测试一个逻辑对象中是否有任何元素为 TRUE。此时，`any()` 函数会很有用。如果对象中存在任何 `TRUE`，`any()` 函数返回 `TRUE`，否则返回 `FALSE`。例如：

```
> a.logical=c( T, T, F, T )
> a.logical
[1]  TRUE  TRUE FALSE  TRUE
> test=8
> test
[1] 8
> if ( a.logical==T ) test=1
Warning message:
In if (a.logical == T) test = 1 :
the condition has length > 1 and only the first element will be used
> test
[1] 1
> if ( any( a.logical ) ) test=2
> test
[1] 2
> if ( any( !a.logical ) ) test=3
> test
[1] 3
> if ( any( !a.logical[1:2] ) ) test=4
> test
[1] 3
```

注意，在第三次和第四次测试中，测试是针对 `FALSE` 值的。`!` 用于逻辑取反对象 `as.logical`，以测试 `FALSE`。

函数 `all()` 用于测试所有元素是否均为 TRUE。

你可以在 R 提示符下输入 `?any` 或使用 R Studio 的帮助标签页，获取关于 `any()` 和 `all()` 的更多信息。

