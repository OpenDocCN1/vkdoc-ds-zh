# ... with 141 more rows

我们展示的最后一个过滤器示例使用了几个参数。使用多个参数不是必需的，但可以是一种将代码分解成人类可读块的有用方式。多个参数在逻辑上等同于 `&`。

```
filter(diris,
       Sepal.Length == 4.3 | Sepal.Length == 4.4,
       Petal.Width == 0.2)
```