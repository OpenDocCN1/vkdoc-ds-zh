# 为键设置默认值

前面讨论的`get`方法也可用于添加键/值对并为键设置默认值。如果键/值对已定义，则忽略默认值。还有另一种方法`setdefault`，也可用于此目的。

请注意，`get`方法不会更改字典对象，而`setdefault`方法确保更改反映在对象中。

## `setdefault` 方法示例

以下是`setdefault`方法的使用示例：

```
numbers.setdefault('Mandarin','yi')
numbers
```

输出：

```
{'English': 'One',
'Spanish': 'Uno',
'German': 'Ein',
'French': 'un',
'Mandarin': 'yi'}
```

如我们所见，添加了一个新的键/值对。

## `get` 方法示例

以下是`get`方法的使用示例：

```
numbers.get('Hindi','Ek')
numbers
```

输出：

```
{'English': 'One',
'Spanish': 'Uno',
'German': 'Ein',
'French': 'un',
'Mandarin': 'yi'}
```

`get`方法设置的值不会添加到字典中。