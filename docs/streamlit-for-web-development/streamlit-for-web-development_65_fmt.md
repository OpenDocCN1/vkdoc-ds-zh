# 逻辑回归训练函数

```python
@st.cache_resource
def log_train(df, feature_cols, label_col, test_size, penalty, tol, fit_intercept, class_weight, solver, multi_class,
              warm_start, dual, C, intercept_scaling, random_state, max_iter, verbose, l1_ratio):
    x = df[feature_cols]
    y = df[label_col]
    x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=test_size, random_state=0)
    logreg = LogisticRegression(penalty=penalty, dual=dual, tol=tol, C=C, fit_intercept=fit_intercept,
                                intercept_scaling=intercept_scaling, class_weight=class_weight,
                                random_state=random_state, solver=solver, max_iter=max_iter,
                                multi_class=multi_class, verbose=verbose, warm_start=warm_start, l1_ratio=l1_ratio)
    logreg.fit(x_train, y_train)
    y_pred = logreg.predict(x_test)
    return x_train, x_test, y_train, y_test, y_pred, logreg
```