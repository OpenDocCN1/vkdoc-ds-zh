# 附录 A Streamlit 应用程序编程接口

### 数值输入

我们可以使用以下命令向网站添加一个数值输入部件：`st.number_input(label, min_value, max_value, value=0.0, step=1, format, key, help, on_change, args, kwargs, disabled=False)` ➤ `int` 或 `float`。

**表 A-30.** `st.number_input` 参数

| 参数 | 说明 |
|-----------|-------------|
| `label( *str*)` | 数值输入部件的标签。 |
| `min_value( *int or float or None*)` | 接受的最小值。 |
| `max_value( *int or float or None*)` | 接受的最大值。 |
| `value( *int or float or None*)` | 此部件的首次渲染值。默认为 `min_value`，如果 `min_value` 为无，则默认为 `0.0`。 |
| `step( *int or float or None*)` | 步进间隔。默认情况下，整数设置为 1，其他所有选项设置为 0.01。对于未指定的值，将默认使用 `format` 参数。 |
| `format( *str or None*)` | 一个自定义格式字符串，指定界面如何以 printf 样式格式渲染数字。返回值不受转换影响。支持 `%d`、`%e`、`%f`、`%g` 和 `%i` 用于格式化整数/浮点数值。对于日期/时间/日期时间格式化，请使用 Moment.js 语法：[`momentjs.com/docs/#/displaying/format/`](https://momentjs.com/docs/#/displaying/format/)。 |
| `key( *str or int*)` | 用于在代码中引用按钮的唯一键，可以是字符串或整数。该参数为可选项，如果未提供，将自动生成。 |
| `help( *str*)` | 当光标悬停在部件上时显示的工具提示。 |
| `on_change( *callable*)` | 当部件值发生变化时调用的回调函数。 |
| `args( *tuple*)` | 传递给回调函数的参数元组。 |
| `kwargs( *dict*)` | 传递给回调函数的关键字参数字典。 |
| `disabled( *bool*)` | 用于禁用部件的布尔参数。 |

一个简单的 1 到 100 选择，步长为 1，数值输入命令的代码片段实现如下所示：

```python
selection = st.number_input('请从 1 到 100 中选择一个数字：',
                            min_value=0, max_value=100, value=50, step=1)
st.write('当前数字是 ', selection)
```

### 文本区域

我们可以使用以下命令向网站添加一个多行文本输入：`st.text_area(label, value, height, max_chars, key, help, on_change, args, kwargs, placeholder, disabled=False)` ➤ `str`。

**表 A-31.** `st.text_area` 参数

| 参数 | 说明 |
|-----------|-------------|
| `label( *str*)` | 说明输入部件用途的标签。 |
| `value( *any*)` | 部件首次渲染时显示的默认文本。在内部，这将被转换为 `str`。 |
| `height( *int or None*)` | UI 元素的高度（像素）。 |
| `max_chars( *int or None*)` | 文本区域允许的最大字符数。 |
| `key( *str or int*)` | 用于在代码中引用按钮的唯一键，可以是字符串或整数。该参数为可选项，如果未提供，将自动生成。 |



