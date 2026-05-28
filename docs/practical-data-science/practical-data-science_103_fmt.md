# 聚类算法探索

## 你可以调查深度为 5 的情况

```python
depth = 5

incons = inconsistent(Z, depth)

print(incons[-10:])
```

你看到了什么？

将深度改为 3。

```python
depth = 3

incons = inconsistent(Z, depth)

print(incons[-10:])
```

你看到了什么？用图形展示会看得更清楚。

```python
last = Z[-10:, 2]

last_rev = last[::-1]

idxs = np.arange(1, len(last) + 1)

plt.plot(idxs, last_rev)
```

现在你应该观察加速度值。

```python
acceleration = np.diff(last, 2)

acceleration_rev = acceleration[::-1]

plt.plot(idxs[:-2] + 1, acceleration_rev)

plt.show()

k = acceleration_rev.argmax() + 2  # 如果索引 0 是最大值，我们想要 2 个聚类

print("Clusters:", k)

c = np.random.multivariate_normal([40, 40], [[20, 1], [1, 30]], size=[200,])

d = np.random.multivariate_normal([80, 80], [[30, 1], [1, 30]], size=[200,])

e = np.random.multivariate_normal([0, 100], [[100, 1], [1, 100]], size=[200,])

X2 = np.concatenate((X, c, d, e),)

plt.scatter(X2[:,0], X2[:,1])

plt.show()
```

你能看到这些聚类吗？下面是一个正式的聚类图：

```python
Z2 = linkage(X2, 'ward')

plt.figure(figsize=(10,10))

fancy_dendrogram(
    Z2,
    truncate_mode='lastp',
    p=30,
    leaf_rotation=90.,
    leaf_font_size=12.,
    show_contracted=True,
    annotate_above=40,
    max_d=170,
)

plt.show()
```