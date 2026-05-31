# 附录 A Streamlit 应用程序接口

#### 使用 `st.cache` 进行缓存

`warning` (`boolean`) — 关于在缓存函数内调用内嵌函数的警告。

`hash_funcs` (`dict` 或 `None`) — 通过此参数，您可以覆盖 Streamlit 对缓存中对象的处理方式；当哈希函数在字典中遇到指定对象时，它将使用关联函数进行响应。

`max_entries` (`int` 或 `None`) — 此参数指定要缓存的最大条目数，每当有新条目添加到列表中时，最旧的条目将被相应移除。

`ttl` (`float` 或 `None`) — 此参数决定条目在缓存中的保留时间（秒）；若未设置，缓存将不会被清除。

```python
@st.cache
def dataframe(rows):
    df = pd.DataFrame(
        np.random.randn(rows, 5),
        columns=('col %d' % i for i in range(5)))
    return df

##### 首次调用以缓存数据框
df = dataframe(100000)

##### 后续调用将访问缓存的数据框，无需重新执行函数
df = dataframe(100000)

##### 由于输入参数已修改，将重新执行函数并缓存新的数据框
df = dataframe(200000)
```

### 缓存函数

Streamlit 还允许您通过 `st.experimental_memo(func, persist, show_spinner, suppress_st_warning, max_entries, ttl)` 命令持久地缓存函数执行结果。此功能与 `st.cache` 命令非常相似。当您不再需要缓存时，可以使用 `st.experimental_memo.clear()` 命令将其移除。请注意，以 `experimental` 开头的命令在功能成熟后，将在未来版本中移除此前缀。

**表 A-52.** `st.experimental_memo` 参数

| 参数 | 描述 |
|---|---|
| `func` (`callable`) | 需要缓存的函数。 |
| `persist` (`boolean`) | 此参数指定是否持久保留缓存数据。 |
| `show_spinner` (`boolean`) | 如果设置为 `True`，则在缓存未命中时将显示加载动画。 |
| `suppress_st_warning` (`boolean`) | 将此参数设置为 `True` 将抑制关于在缓存函数内调用内嵌函数的任何警告。 |
| `max_entries` (`int` 或 `None`) | 此参数指定要缓存的最大条目数，每当有新条目添加到列表中时，最旧的条目将被相应移除。 |
| `ttl` (`float` 或 `None`) | 此参数决定条目在缓存中的保留时间（秒）；若未设置，缓存将不会被清除。 |

```python
@st.experimental_memo
def dataframe(rows):
    df = pd.DataFrame(
        np.random.randn(rows, 5),
        columns=('col %d' % i for i in range(5)))
    return df

#### 首次调用以缓存函数执行结果
df = dataframe(100000)

#### 后续调用将访问缓存的执行结果，无需重新执行函数
df = dataframe(100000)

#### 由于输入参数已修改，将重新执行函数并缓存新的执行结果
df = dataframe(200000)
```

### 缓存对象

与 `st.cache` 类似，Streamlit 允许您通过 `st.experimental_singleton(func, show_spinner, suppress_st_warning)` 命令持久地缓存对象。当您不再需要缓存时，可以使用 `st.experimental_singleton.clear()` 命令将其移除。需要特别强调的是，单例对象必须是线程安全的，因为它们将被应用程序的所有用户共享。`st.experimental_singleton` 的典型用例包括数据库连接和语料库。然而，对于 `st.memo` 则不是这种情况，因为每个调用者将收到缓存数据的独立副本。

**表 A-53.** `st.experimental_singleton` 参数

| 参数 | 描述 |
|---|---|
| `func` (`callable`) | 需要缓存的函数。 |
| `show_spinner` (`boolean`) | 如果设置为 `True`，则在缓存未命中时将显示加载动画。 |
| `suppress_st_warning` (`boolean`) | 将此参数设置为 `True` 将抑制关于在缓存函数内调用内嵌函数的任何警告。 |



