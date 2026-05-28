# 第九章 形态学、阈值处理与图像分割

```python
dilation = ndi.grey_dilation(img, size=(3, 3), structure=np.ones((3, 3)))
erosion = ndi.grey_erosion(img, size=(3, 3), structure=np.ones((3, 3)))
```