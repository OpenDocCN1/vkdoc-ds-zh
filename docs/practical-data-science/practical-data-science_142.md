# -*- coding: utf-8 -*-
################################################################
import sys
import os
import pandas as pd
from folium.plugins import FastMarkerCluster, HeatMap
from folium import Marker, Map
import webbrowser
################################################################
if sys.platform == 'linux' or sys.platform == ' darwin':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
```

导入公司在德国的广告牌所需数据。

```
sFileName=Base+'/02-Krennwallner/01-Retrieve/01-EDS/02-Python/Retrieve_DE_Billboard_Locations.csv'
df = pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
df.fillna(value=0, inplace=True)
print(df.shape)
```

数据需要处理一些缺失值。我建议直接通过 `try-except` 流程来移除“质量差”的记录。

```
################################################################
t=0
for i in range(df.shape[0]):
try:
sLongitude=df["Longitude"][i]
sLongitude=float(sLongitude)
except Exception:
sLongitude=float(0.0)
try:
sLatitude=df["Latitude"][i]
sLatitude=float(sLatitude)
except Exception:
sLatitude=float(0.0)
try:
sDescription=df["Place_Name"][i] + ' (' + df["Country"][i]+')'
except Exception:
sDescription='VKHCG'
if sLongitude != 0.0 and sLatitude != 0.0:
DataClusterList=list([sLatitude, sLongitude])
DataPointList=list([sLatitude, sLongitude, sDescription])
t+=1
if t==1:
DataCluster=[DataClusterList]
DataPoint=[DataPointList]
else:
DataCluster.append(DataClusterList)
DataPoint.append(DataPointList)
data=DataCluster
```

现在你拥有了一份可用于创建网页内容的数据集。你可以先创建聚集图网页。

```
pins=pd.DataFrame(DataPoint)
pins.columns = [ 'Latitude','Longitude','Description']
################################################################
billbords_map1 = Map(location=[48.1459806, 11.4985484], zoom_start=5)
marker_cluster = FastMarkerCluster(data).add_to(billbords_map1)
sFileNameHtml=Base+'/01-Vermeulen/06-Report/01-EDS/02-Python/Billboard1.html'
billbords_map1.save(sFileNameHtml)
```

网页已保存，现在打开它，看看你的成果。

```
webbrowser.open('file://' + os.path.realpath(sFileNameHtml))
```

接下来，你可以生成仅包含标记的网页。

```
################################################################
billbords_map2 = Map(location=[48.1459806, 11.4985484], zoom_start=5)
for name, row in pins.iloc[:100].iterrows():
Marker([row["Latitude"],row["Longitude"]], popup=row["Description"]).add_to(billbords_map2)
sFileNameHtml=Base+'/01-Vermeulen/06-Report/01-EDS/02-Python/Billboard2.html'
billbords_map2.save(sFileNameHtml)
webbrowser.open('file://' + os.path.realpath(sFileNameHtml))
```

这个示例进展顺利。你还可以将数据制作成热力图版本的网页。

```
################################################################
billbords_heatmap = Map(location=[48.1459806, 11.4985484], zoom_start=5)
billbords_heatmap.add_child(HeatMap([[row["Latitude"], row["Longitude"]] for name, row in pins.iloc[:100].iterrows()]))
sFileNameHtml=Base+'/01-Vermeulen/06-Report/01-EDS/02-Python/Billboard_heatmap.html'
billbords_heatmap.save(sFileNameHtml)
webbrowser.open('file://' + os.path.realpath(sFileNameHtml))
################################################################
print('### Done!! ############################################')
################################################################
```

#### 希尔曼公司

希尔曼博士刚刚安装了一套摄像系统，使公司能够拍摄视频，从而间接获取所有进出仓库的集装箱的图像。你能将集装箱侧面的数字转换成数字编码吗？



# 读取容器

我将协助你通过一个快速实验来证明你能够完成所需任务。打开你的 Python 编辑器并搭建如下环境：

```
from time import time
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import offsetbox
from sklearn import (manifold, datasets, decomposition, ensemble,
discriminant_analysis, random_projection)
digits = datasets.load_digits(n_class=6)
X = digits.data
y = digits.target
n_samples, n_features = X.shape
n_neighbors = 30
```

创建一个用于缩放和可视化嵌入向量的函数。

```
def plot_embedding(X, title=None):
    x_min, x_max = np.min(X, 0), np.max(X, 0)
    X = (X - x_min) / (x_max - x_min)
    plt.figure(figsize=(10, 10))
    ax = plt.subplot(111)
    for i in range(X.shape[0]):
        plt.text(X[i, 0], X[i, 1], str(digits.target[i]),
                 color=plt.cm.Set1(y[i] / 10.),
                 fontdict={'weight': 'bold', 'size': 9})
    if hasattr(offsetbox, 'AnnotationBbox'):
        # only print thumbnails with matplotlib > 1.0
        shown_images = np.array([[1., 1.]])  # just something big
        for i in range(digits.data.shape[0]):
            dist = np.sum((X[i] - shown_images) ** 2, 1)
            if np.min(dist) < 4e-3:
                # don't show points that are too close
                continue
            shown_images = np.r_[shown_images, [X[i]]]
            imagebox = offsetbox.AnnotationBbox(
                offsetbox.OffsetImage(digits.images[i], cmap=plt.cm.gray_r),
                X[i])
            ax.add_artist(imagebox)
    plt.xticks([]), plt.yticks([])
    if title is not None:
        plt.title(title)
```

现在你可以绘制已识别数字的图像。

```
n_img_per_row = 20
img = np.zeros((10 * n_img_per_row, 10 * n_img_per_row))
for i in range(n_img_per_row):
    ix = 10 * i + 1
    for j in range(n_img_per_row):
        iy = 10 * j + 1
        img[ix:ix + 8, iy:iy + 8] = X[i * n_img_per_row + j].reshape((8, 8))
plt.figure(figsize=(10, 10))
plt.imshow(img, cmap=plt.cm.binary)
plt.xticks([])
plt.yticks([])
plt.title('从 64 维数字数据集中选取的样本')
```

你可以使用随机酉矩阵进行随机二维投影处理。

```
print("Computing random projection")
rp = random_projection.SparseRandomProjection(n_components=2, random_state=42)
X_projected = rp.fit_transform(X)
plot_embedding(X_projected, "数字的随机投影")
```

现在添加一个在前两个主成分上的投影。

```
print("Computing PCA projection")
t0 = time()
X_pca = decomposition.TruncatedSVD(n_components=2).fit_transform(X)
plot_embedding(X_pca,
              "数字的主成分投影 (用时 %.2fs)" %
              (time() - t0))
```

添加一个在前两个线性判别成分上的投影。

```
print("Computing Linear Discriminant Analysis projection")
X2 = X.copy()
X2.flat[::X.shape[1] + 1] += 0.01  # 使 X 可逆
t0 = time()
X_lda = discriminant_analysis.LinearDiscriminantAnalysis(n_components=2).fit_transform(X2, y)
plot_embedding(X_lda,
              "数字的线性判别投影 (用时 %.2fs)" %
              (time() - t0))
```

添加数字数据集的 Isomap 投影。

```
print("Computing Isomap embedding")
t0 = time()
X_iso = manifold.Isomap(n_neighbors, n_components=2).fit_transform(X)
print("Done.")
plot_embedding(X_iso,
              "数字的 Isomap 投影 (用时 %.2fs)" %
              (time() - t0))
```

添加数字数据集的局部线性嵌入。

```
print("Computing LLE embedding")
clf = manifold.LocallyLinearEmbedding(n_neighbors, n_components=2,
                                      method='standard')
t0 = time()
X_lle = clf.fit_transform(X)
print("Done. Reconstruction error: %g" % clf.reconstruction_error_)
plot_embedding(X_lle,
              "数字的局部线性嵌入 (用时 %.2fs)" %
              (time() - t0))
```

添加数字数据集的修正局部线性嵌入。

```
print("Computing modified LLE embedding")
clf = manifold.LocallyLinearEmbedding(n_neighbors, n_components=2,
                                      method='modified')
t0 = time()
X_mlle = clf.fit_transform(X)
print("Done. Reconstruction error: %g" % clf.reconstruction_error_)
plot_embedding(X_mlle,
              "数字的修正局部线性嵌入 (用时 %.2fs)" %
              (time() - t0))
```

添加数字数据集的 Hessian LLE 嵌入。

```
print("Computing Hessian LLE embedding")
clf = manifold.LocallyLinearEmbedding(n_neighbors, n_components=2,
                                      method='hessian')
t0 = time()
X_hlle = clf.fit_transform(X)
print("Done. Reconstruction error: %g" % clf.reconstruction_error_)
plot_embedding(X_hlle,
              "数字的 Hessian 局部线性嵌入 (用时 %.2fs)" %
              (time() - t0))
```

添加数字数据集的 LTSA 嵌入。

```
print("Computing LTSA embedding")
clf = manifold.LocallyLinearEmbedding(n_neighbors, n_components=2,
                                      method='ltsa')
t0 = time()
X_ltsa = clf.fit_transform(X)
print("Done. Reconstruction error: %g" % clf.reconstruction_error_)
plot_embedding(X_ltsa,
              "数字的局部切空间对齐 (用时 %.2fs)" %
              (time() - t0))
```

添加数字数据集的 MDS 嵌入。

```
print("Computing MDS embedding")
clf = manifold.MDS(n_components=2, n_init=1, max_iter=100)
t0 = time()
X_mds = clf.fit_transform(X)
print("Done. Stress: %f" % clf.stress_)
plot_embedding(X_mds,
              "数字的 MDS 嵌入 (用时 %.2fs)" %
              (time() - t0))
```

添加数字数据集的随机树嵌入。

```
print("Computing Totally Random Trees embedding")
hasher = ensemble.RandomTreesEmbedding(n_estimators=200, random_state=0,
                                       max_depth=5)
t0 = time()
X_transformed = hasher.fit_transform(X)
pca = decomposition.TruncatedSVD(n_components=2)
X_reduced = pca.fit_transform(X_transformed)
plot_embedding(X_reduced,
              "数字的随机森林嵌入 (用时 %.2fs)" %
              (time() - t0))
```

添加数字数据集的谱嵌入。

```
print("Computing Spectral embedding")
embedder = manifold.SpectralEmbedding(n_components=2, random_state=0,
                                      eigen_solver="arpack")
t0 = time()
X_se = embedder.fit_transform(X)
plot_embedding(X_se,
              "数字的谱嵌入 (用时 %.2fs)" %
              (time() - t0))
```

添加数字数据集的 t-SNE 嵌入。

```
print("Computing t-SNE embedding")
tsne = manifold.TSNE(n_components=2, init="pca", random_state=0)
t0 = time()
X_tsne = tsne.fit_transform(X)
plot_embedding(X_tsne,
              "数字的 t-SNE 嵌入 (用时 %.2fs)" %
              (time() - t0))
plt.show()
```

干得漂亮！你已成功完成容器实验。你觉得哪种显示格式最好？

正确答案取决于你的选择，因为它必须与你自己对数据的洞察相匹配，而且实际上并没有错误的答案。

#### Clark 有限公司

VKHCG 旗下的金融公司是 Clark 会计师事务所，VKHCG 持有其 60% 的股份。这些会计师是集团的财务顾问，负责处理与国际会计复杂工作相关的所有事务。



#### 财务状况

VKHCG 公司去年表现良好，Clark 团队的成员需要为集团内每家公司准备一份资产负债表。这些公司要求使用示例目录 `(..\VKHCG\04-Clark\00-RawData`) 中的模板文件（`Balance-Sheet-Template.xlsx`）为每家公司生成资产负债表。图 11-3 展示了基本布局。

![A435693_1_En_11_Fig3_HTML.jpg](img/A435693_1_En_11_Fig3_HTML.jpg)

图 11-3  
VKHCG 公司的基本内部资产负债表

我将引导您完成一个流程，使您能够将数据科学与预格式化的 Microsoft Excel 模板相结合，为每家 VKHCG 公司生成资产负债表。

**注意**

一个名为 `openpyxl` 的新库可以帮助您编辑 Microsoft Excel 工作簿。

使用命令 `"conda install -c anaconda openpyxl"` 安装这个新库。打开您的 Python 生态系统，并将以下代码构建到其中。

设置一个标准生态系统。

```python
