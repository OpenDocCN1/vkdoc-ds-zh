# 调整图像大小

你可以使用 `resize()` 函数调整图像大小，如列表 4-4 所示。

***列表 4-4.*** `prog04.py`

```
from PIL import Image, ImageTk
import tkinter as tk

def show_value_1(size):
```



```python
print('Resize: ', size, ' : ', size)

img = im.resize((int(size), int(size)))

photo = ImageTk.PhotoImage(img)

l['image'] = photo

l.photo = photo

root = tk.Tk()

root.attributes('-fullscreen', True)

im = Image.open("/home/pi/DIP/Dataset/4.1.07.tiff")

photo = ImageTk.PhotoImage(im)

l = tk.Label(root, image=photo)

l.pack()

l.photo = photo

w1 = (tk.Scale(root, label="Resize", from_=128,

to=512, resolution=1, command=show_value_1, orient=tk.

HORIZONTAL))

w1.pack()

root.mainloop()
```

