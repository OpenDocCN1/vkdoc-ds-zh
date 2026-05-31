# Streamlit 应用程序编程接口

### 日期输入

可使用以下命令向我们的网站添加一个日期显示输入小部件：`st.date_input(label, value, min_value, max_value, key, help, on_change, args, kwargs, disabled=False)` ➤ `value`。

**表 A-32.** `st.date_input` 参数

| 参数 | 描述 |
|-----------|-------------|
| `label( str )` | 用于说明日期选择意图的标签。 |
| `value( datetime.date 或 datetime.datetime 或包含 datetime.date 或 datetime.datetime 的列表/元组 或 无值 )` | 此小部件在首次渲染时显示的值。如果提供的列表/元组包含零到两个日期/日期时间值，则会被视为一个范围。如果未选择任何值，默认值为今天。 |
| `min_value( datetime.date 或 datetime.datetime )` | 默认的最小可选日期。如果该值是一个日期，则将其设置为从现在起前推 10 年。对于区间 [start, finish]，默认值为 start - 10 年。 |
| `max_value( datetime.date 或 datetime.datetime )` | 最大可选日期。如果该值是一个日期，则将其更新为当前值加上 10 年。如果该值是区间 [start, end]，则将其更新为 end + 10 年。 |
| `key( str 或 int )` | 用于在代码中引用此按钮的唯一键，可以是字符串或整数。此参数是可选的，如果未提供，将自动生成。 |
| `help( str )` | 当光标悬停在小部件上时显示的工具提示。 |
| `on_change( callable )` | 小部件值发生变化时调用的回调函数。 |
| `args( tuple )` | 传递给回调函数的参数元组。 |
| `kwargs( dict )` | 传递给回调函数的关键字参数字典。 |
| `disabled( bool )` | 用于禁用此小部件的布尔参数。 |

以下代码行使用日期输入小部件实现了一个简单的按天计算年龄的计算器，并将最小日期限制为 1920 年之后出生的人：

```python
birth_date = st.date_input('出生日期：', value = datetime.date(2018,12,29), min_value = datetime.date(1920,1,1))
st.write('您的年龄（天）：', datetime.date.today() - birth_date)
```

### 时间输入

可使用以下命令向我们的网站添加一个时间输入小部件：`st.time_input(label, value, key, help, on_change, args, kwargs, disabled=False)` ➤ `value`。

**表 A-33.** `st.time_input` 参数

| 参数 | 描述 |
|-----------|-------------|
| `label( str )` | 小部件的标签。 |
| `value( datetime.time / datetime.datetime )` | 此小部件在首次渲染时显示的值。内部会转换为字符串。默认为当前时间。 |
| `key( str 或 int )` | 用于在代码中引用此按钮的唯一键，可以是字符串或整数。此参数是可选的，如果未提供，将自动生成。 |
| `help( str )` | 当光标悬停在小部件上时显示的工具提示。 |
| `on_change( callable )` | 小部件值发生变化时调用的回调函数。 |
| `args( tuple )` | 传递给回调函数的参数元组。 |
| `kwargs( dict )` | 传递给回调函数的关键字参数字典。 |
| `disabled( bool )` | 用于禁用此小部件的布尔参数。 |

此示例展示了时间输入的使用，其渲染时的默认值为上午 7:00：

```python
sleep_time = st.time_input('睡几个小时就够了？', ...)
```



