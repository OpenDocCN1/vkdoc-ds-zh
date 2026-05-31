# 图像通道

你可以分离多通道图像的图像通道。该过程的代码如“清单 6-7”所示。

***清单 6-7.*** `prog07.py`

```python
import scipy.misc as misc
import matplotlib.pyplot as plt
img = misc.face()
r = img[:, :, 0]
```



