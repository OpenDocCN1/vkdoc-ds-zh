# 附录 A Streamlit 应用程序编程接口

以下代码实现了一个滑块，用于选择 9:00 至 16:00 之间的时间：

```python
from datetime import time

meeting_time = st.slider('Select time:', value=time(11, 00),
                         max_value=time(16, 00), min_value=time(9, 00),
                         format='hh:mm')
```

`st.write('You are scheduled for: %s' % meeting_time)`

## 选择滑块

可以使用以下命令将选择滑块部件添加到我们的网站：`st.select_slider(label, options, value, format_func, key, help, on_change, args, kwargs)` → `value`。

### 表 A-28. `st.select_slider`参数

| 参数 | 描述 |
|------|------|
| `label`（`str`） | 解释滑块用途的标签。 |
| `options`（`Sequence`, `numpy.ndarray`, `pandas.Series`, `pandas.DataFrame`, 或 `pandas.Index`） | 单选按钮选项标签。它会自动转换为`str`。当使用`pandas.DataFrame`时，第一列将被选为数据源。 |
| `value`（支持的类型或`tuple`/`list`类型或`None`） | 首次显示时滑块的值。如果在此处提供包含两个值的`tuple`/`list`，则会创建一个具有下限和上限的范围滑块。默认为第一个选项。 |
| `format_func`（`function`） | 用于修改单选按钮选项标签的辅助函数。标签的`str`值作为参数传递，并根据函数逻辑返回修改后的`str`。此选项纯粹是装饰性的，对单选按钮部件的操作或返回值没有影响。 |
| `key`（`str`或`int`） | 用于在代码中引用按钮的唯一键（字符串或整数）。此参数是可选的，如果未提供，将自动生成。 |
| `help`（`str`） | 鼠标悬停在部件上时显示的工具提示。 |
| `on_change`（`callable`） | 当部件值发生变化时调用的回调函数。 |
| `args`（`tuple`） | 传递给回调函数的参数元组。 |
| `kwargs`（`dict`） | 传递给回调函数的关键字参数字典。 |
| `disabled`（`bool`） | 用于禁用部件的布尔参数。 |

选择滑块是滑块部件的一种版本，适用于非数值、日期或时间值。以下示例复制了简单滑块中的示例，但使用了分类值：

```python
start_time, end_time = st.select_slider('Select time',
                                        options=['nine', 'ten', 'eleven', 'twelve', 'one', 'two',
                                                 'three', 'four'],
                                        value=('eleven', 'twelve'))
```

`st.write('You are scheduled from %s to %s' % (start_time, end_time))`

## 文本输入

可以使用以下命令将单行文本输入框添加到我们的网站：`st.text_input(label, value, max_chars, key, type='default', help, autocomplete, on_change, args, kwargs, placeholder, disabled=False)` → `str`。

### 表 A-29. `st.text_input`参数

| 参数 | 描述 |
|------|------|
| `label`（`str`） | 解释此输入用途的标签。 |
| `value`（`any`） | 选择复选框在首次渲染时是否默认选中。内部会将其转换为`str`。 |
| `max_chars`（`int`或`None`） | 文本输入中允许的最大字符数。 |
| `key`（`str`或`int`） | 用于在代码中引用按钮的唯一键（字符串或整数）。此参数是可选的，如果未提供，将自动生成。 |
| `type`（`str`） | 文本输入类型：常规文本输入（“default”选项）或掩码文本（用于“password”类型输入）。 |
| `help`（`str`） | 鼠标悬停在部件上时显示的工具提示。 |
| `autocomplete`（`str`） | `autocomplete`属性是 HTML 5 新增的方法，适用于希望用户能够自动完成输入的网站。 |

在无需提供用户名和密码的情况下完成一项任务。一个可选值将被提供给 `<input>` 元素的 `autocomplete` 属性。对于 `"password"` 输入类型，该值将设置为 `"new-password"`；对于 `"default"` 输入类型，该值将设置为空字符串。更多详情请参阅 [`developer.mozilla.org/en-US/docs/Web/HTML/Attributes/autocomplete`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/autocomplete)。

`on_change`（`callable`）当部件值发生变化时调用的回调函数。

`args`（`tuple`）传递给回调函数的参数元组。

`kwargs`（`dict`）传递给回调函数的关键字参数字典。

`placeholder`（`str or None`）当没有文本输入时显示的可选字符串。

`disabled`（`bool`）用于禁用部件的布尔参数。

以下示例展示了如何在单行文本框中遮罩文本：

```python
password = st.text_input('请输入您的密码：', value='1234',
                         max_chars=24,
                         type='password')
st.write('您输入的密码是', password)
```