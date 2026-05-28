```python
# -*- coding: utf-8 -*-

################################################################

import sys

import os

import matplotlib

import numpy as np

import matplotlib.cm as cm

import matplotlib.mlab as mlab

import matplotlib.pyplot as plt

################################################################

if sys.platform == 'linux' or sys.platform == ' darwin':

Base=os.path.expanduser('~') + '/VKHCG'

else:

Base='C:/VKHCG'

print('################################')

print('Working Base :',Base, ' using ', sys.platform)

print('################################')

################################################################

matplotlib.rcParams['xtick.direction'] = 'out'

matplotlib.rcParams['ytick.direction'] = 'out'
```

这是你的数据集。

```python
delta = 0.025

x = np.arange(-3.0, 3.0, delta)

y = np.arange(-2.0, 2.0, delta)

X, Y = np.meshgrid(x, y)

Z1 = mlab.bivariate_normal(X, Y, 1.0, 1.0, 0.0, 0.0)

Z2 = mlab.bivariate_normal(X, Y, 1.5, 0.5, 1, 1)
```