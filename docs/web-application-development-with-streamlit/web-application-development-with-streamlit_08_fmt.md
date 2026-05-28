# 附录 A Streamlit 应用程序编程接口

## 文件上传器

可以使用以下命令将文件上传器添加到我们的网站：

`st.file_uploader(label, type=None, accept_multiple_files=False, key, help, on_change, args, kwargs, disabled=False)` ➤ `list`

上传文件的默认大小为 200MB。该值可通过 `server.maxUploadSize` 配置选项进行配置。

**表 A-34.** `st.file_uploader` 参数

| 参数 | 描述 |
|------|------|
| `label` (`str`) | 用于解释期望上传何种文件的小部件标签。 |
| `type` (`str` 或 `str` 列表，或 `None`) | 允许的扩展名数组，例如 `['gif', 'jpg']`。要允许所有扩展名，请使用 `none`。 |
| `accept_multiple_files` (`bool`) | 当为 `True` 时，允许用户同时上传多个文件，并返回上传文件的列表。默认选项为 `false`。 |
| `key` (`str` 或 `int`) | 用于在代码中引用按钮的唯一键（字符串或整数）。该参数是可选的，如果未提供，将自动生成。 |
| `help` (`str`) | 当光标悬停在小部件上时显示的工具提示。 |
| `on_change` (`callable`) | 小部件值发生更改时调用的回调函数。 |
| `args` (`tuple`) | 传递给回调函数的参数元组。 |
| `kwargs` (`dict`) | 传递给回调函数的关键字参数字典。 |
| `disabled` (`bool`) | 用于禁用小部件的布尔参数。 |

以下代码可用于一起上传多个文件：

```python
files_to_upload = st.file_uploader(label = 'Upload photos:',
                                   accept_multiple_files=True)
```

## 摄像头输入

可以使用以下命令从用户的网络摄像头返回静态画面：

`st.camera_input(label, key, help, on_change, args, kwargs, disabled=False)` ➤ `any`

**表 A-35.** `st.time_input` 参数

| 参数 | 描述 |
|------|------|
| `label` (`str`) | 小部件的标签。 |
| `key` (`str` 或 `int`) | 用于在代码中引用小部件的唯一键（字符串或整数）。该参数是可选的，如果未提供，将自动生成。 |
| `help` (`str`) | 当光标悬停在小部件上时显示的工具提示。 |
| `on_change` (`callable`) | 小部件值发生更改时调用的回调函数。 |
| `args` (`tuple`) | 传递给回调函数的参数元组。 |
| `kwargs` (`dict`) | 传递给回调函数的关键字参数字典。 |
| `disabled` (`bool`) | 用于禁用小部件的布尔参数。 |

以下示例展示了如何使用摄像头输入命令通过网络摄像头拍照，并使用光学字符识别读取图像中的文本：

```python
import pytesseract
from PIL import Image

pytesseract.pytesseract.tesseract_cmd = r'C:\Program Files\Tesseract-OCR\tesseract.exe'
photo = st.camera_input('Take a photo')
if photo is not None:
    st.subheader('Your text:')
    text = pytesseract.image_to_string(Image.open(photo))
    st.write(text)
```

## 颜色选择器

可以使用以下命令将颜色选择器小部件添加到我们的网站：

`st.color_picker(label, value, key, help, on_change, args, kwargs, disabled=False)` ➤ `str`

**表 A-36.** `st.color_picker` 参数

| 参数 | 描述 |
|------|------|
| `label` (`str`) | 解释小部件用途的标签。 |
| `value` (`str`) | 首次渲染时该小部件显示的十六进制值。如果为 `none`，则默认为黑色。 |
| `key` (`str` 或 `int`) | 用于在代码中引用按钮的唯一键（字符串或整数）。该参数是可选的，如果未提供，将自动生成。 |
| `help` (`str`) | 当光标悬停在小部件上时显示的工具提示。 |