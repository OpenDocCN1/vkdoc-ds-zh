# 第九章 形态学、阈值处理与图像分割

```python
o2 = ndi.binary_propagation(img, mask=mask, structure=struct).astype(int)
output = [img, o1, o2]
titles = ['原始图像', '输出 1', '输出 2']

for i in range(3):
    print(output[i])
    plt.subplot(1, 3, i+1)
    plt.imshow(output[i], interpolation='nearest')
    plt.title(titles[i])
    plt.axis('off')

plt.show()
```

输出结果如图 9-6 所示。

***图 9-6.** 二值传播*

## 灰度形态学运算

存在一组灰度形态学运算。代码清单 9-7 所示的代码生成八个随机值，并将它们分配到完全黑暗（零值）背景的像素上。然后使用 `grey_dilation()` 方法对其进行膨胀操作。

