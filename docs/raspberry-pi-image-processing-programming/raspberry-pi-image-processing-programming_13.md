# 色彩空间转换

你可以使用 `convert()` 函数更改图像的模式，如列表 4-2 所示。

***列表 4-2.*** `prog02.py`

```
from PIL import Image, ImageTk
import tkinter as tk

im1 = Image.open("/home/pi/DIP/Dataset/4.1.07.tiff")

res1 = im1.convert("L")

root = tk.Tk()
root.title("Colorspace Conversion Demo")
photo = ImageTk.PhotoImage(res1)
l = tk.Label(root, image=photo)
l.pack()
l.photo = photo
root.mainloop()
```

列表 4-2 中的代码将图像的模式更改为 `L`。函数 `convert()` 支持 `RGB`、`CMYK` 和 `L` 模式之间的所有可能转换。你可以在 [`www.color-management-guide.com/color-spaces.html`](https://www.color-management-guide.com/color-spaces.html) 了解更多关于色彩空间的信息。

