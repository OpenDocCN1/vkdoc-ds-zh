# 关于 S4 方法的 R 函数

所有函数（除了`selectMethod()`）都有一个参数`where`，这是一个可选的字符变量，用于指定查找方法的环境。`selectMethod()`和`getMethod()`都有参数`optional`、`mlist`和`fdef`。参数`optional`如果设置为`TRUE`，则告诉 R 在`selectMethod()`未找到方法时返回`NULL`而不是报错。该参数似乎不影响`getMethod()`。两个函数的默认值都是`FALSE`。根据这些函数的帮助页面，其他参数很少使用。更多信息可以通过在 R 提示符下输入`?getMethod`或使用 R Studio 的帮助选项卡来获取。

函数`removeMethod()`用于移除一个方法。该函数接受参数`f`、`signature`和`where`，这些参数的含义如前所述。请注意，要修改一个方法，必须先将该方法移除，然后在修改后重新赋值。更多信息，请在 R 提示符下输入`?removeMethod`或使用 R Studio 的帮助选项卡。

泛型函数有时也需要被移除。泛型函数通过`removeGeneric()`来移除。该函数是同一帮助页面下的一组函数之一：**Tools for Managing Generic Functions**。`removeGeneric()`的参数是`f`和`signature`，含义如前所述。帮助页面中列出的其他函数包括：已描述过的`isGeneric()`；用于查找函数位置的`findFunction()`；用于移除与某函数关联的所有方法的`removeMethods()`；以及用于列出所有泛型函数的`getGenerics()`。对于后两个函数，可以指定位置（使用`.GlobalEnv`表示工作区）。函数`standardGeneric()`也在这个帮助页面中。请在 R 提示符下输入`?removeGeneric`或使用 R Studio 的帮助选项卡以获取更多信息。

函数`showMethods()`用于显示 S4 泛型函数的方法。该函数接受八个参数。第一个是`f`，即函数的名称。该参数是可选的。如果不使用，函数将返回所有 S4 泛型函数。第二个是`where`，即查找函数的环境。默认情况下，`where`设置为工作区的父环境。要查看工作区中的方法，请将`where`设置为`.GlobalEnv`。

第三个参数是`classes`，用于限制搜索范围的类列表。该参数是可选的。第四个参数是`includeDefs`，一个逻辑变量。如果为`TRUE`，则打印出函数。默认值为`FALSE`。第五个参数是`inherited`，一个逻辑变量。如果为`TRUE`，则在会话中使用过的继承方法将包含在方法列表中。默认值是`includeDefs`的相反值。

第六个参数是`showEmpty`，一个逻辑变量。详情请参见帮助页面。第七个参数是`printTo`，它告诉 R 将函数调用的结果打印到哪里。默认情况下，函数打印到标准输出，通常是终端。最后一个参数是`fdef`，它允许你选择使用哪个泛型函数定义。该参数是可选的。输入`?showMethods`获取更多信息，或使用 R Studio 的帮助选项卡。

CRAN 对 S4 方法的介绍可以通过在 R 提示符下输入`?methods::Introduction`或使用 R Studio 的帮助选项卡来找到。