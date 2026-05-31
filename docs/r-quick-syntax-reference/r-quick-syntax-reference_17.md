# 默认包与原始函数

当用户启动 R 会话时，包 `base`、`datasets`、`utils`、`grDevices`、`graphics`、`stats` 和 `methods` 是默认加载到工作空间中的包。（可通过修改函数 `options()` 中的 `defaultPackages` 来更改加载的默认包。详见第 15 章。）通常，根据用户的计算需求，无需再加载其他包。

那些用 C 语言编写并在 R 编译时一同编译的函数被称为**原始**函数。根据在 R 提示符下输入 `?primitive` 或在 R Studio 帮助选项卡下输入 `primitive` 所显示的帮助页面，所有原始函数都位于总是被加载的 `base` 包中。使用原始函数的优势在于这些函数已经编译完成，因此运行速度更快。原始函数包括运算符、大多数数学函数，以及 R 运行和结构的基础函数。原始函数的列表可在 [`http://cran.r-project.org/doc/manuals/R-ints.html#g_t_002eInternal-vs-_002ePrimitive`](http://cran.r-project.org/doc/manuals/R-ints.html%2523g_t_002eInternal-vs-_002ePrimitive) 或 `base` 包的帮助页面中找到。根据参数的传递方式，原始函数分为 `built-in` 或 `special` 类型。用 R 编写的函数属于 `closure` 类型。函数 `is.primitive()` 用于测试一个对象是否为原始函数。

