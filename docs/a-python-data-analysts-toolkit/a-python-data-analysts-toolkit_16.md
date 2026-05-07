# 使用 `append` 方法

代码：
```
combined_ages = combined_ages.append({'class 1': 35, 'class 2': 33, 'class 3': 21}, ignore_index=True)
```
`combined_ages`

输出：
![../images/498042_1_En_6_Chapter/498042_1_En_6_Figl_HTML.jpg](img/498042_1_En_6_Figl_HTML.jpg)

`append` 方法的参数——需要添加的数据——被定义为一个字典。然后，这个字典作为参数传递给 `append` 方法。设置 `ignore_index=True` 参数可以防止抛出错误。该参数会重置索引。在使用 `append` 方法时，我们需要确保要么使用 `ignore_index` 参数，要么在将 Series 追加到 DataFrame 之前为其命名。请注意，`append` 方法没有 `inplace` 参数来确保更改反映到原始对象中；因此，我们需要将原始对象指向使用 append 创建的新对象，如前面的代码所示。

