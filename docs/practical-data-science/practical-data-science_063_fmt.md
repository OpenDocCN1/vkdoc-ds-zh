# Data generation

```
X1 = 0.3 * `np.random.randn`(n_inliers // 2, 2) - offset

X2 = 0.3 * `np.random.randn`(n_inliers // 2, 2) + offset

X = `np.r_`[X1, X2]
```