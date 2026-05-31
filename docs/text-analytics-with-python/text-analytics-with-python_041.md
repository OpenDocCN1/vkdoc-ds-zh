# 随机梯度下降支持向量机

```
from sklearn.linear_model import SGDClassifier
svm_sgd = SGDClassifier(loss='hinge', penalty="l2", max_iter=5, random_state=42)
svm_sgd.fit(cv_train_features, train_label_names)
svmsgd_bow_cv_scores = cross_val_score(svm_sgd, cv_train_features, train_label_names, cv=5)
svmsgd_bow_cv_mean_score = np.mean(svmsgd_bow_cv_scores)
print('CV Accuracy (5-fold):', svmsgd_bow_cv_scores)
print('Mean CV Accuracy:', svmsgd_bow_cv_mean_score)
svmsgd_bow_test_score = svm_sgd.score(cv_test_features, test_label_names)
print('Test Accuracy:', svmsgd_bow_test_score)
```

```
CV Accuracy (5-fold): [ 0.66030069  0.62459283  0.65185487  0.63209473  0.64157119]
Mean CV Accuracy: 0.642082864709
Test Accuracy: 0.633007780169
```

### 随机森林

```
from sklearn.ensemble import RandomForestClassifier
rfc = RandomForestClassifier(n_estimators=10, random_state=42)
rfc.fit(cv_train_features, train_label_names)
rfc_bow_cv_scores = cross_val_score(rfc, cv_train_features, train_label_names, cv=5)
rfc_bow_cv_mean_score = np.mean(rfc_bow_cv_scores)
print('CV Accuracy (5-fold):', rfc_bow_cv_scores)
print('Mean CV Accuracy:', rfc_bow_cv_mean_score)
rfc_bow_test_score = rfc.score(cv_test_features, test_label_names)
print('Test Accuracy:', rfc_bow_test_score)
```

```
CV Accuracy (5-fold): [ 0.52052011  0.51669381  0.53485528  0.51327072  0.5212766 ]
Mean CV Accuracy: 0.521323304518
Test Accuracy: 0.52987915908
```

### 梯度提升机

```
from sklearn.ensemble import GradientBoostingClassifier
gbc = GradientBoostingClassifier(n_estimators=10, random_state=42)
gbc.fit(cv_train_features, train_label_names)
gbc_bow_cv_scores = cross_val_score(gbc, cv_train_features, train_label_names, cv=5)
gbc_bow_cv_mean_score = np.mean(gbc_bow_cv_scores)
print('CV Accuracy (5-fold):', gbc_bow_cv_scores)
print('Mean CV Accuracy:', gbc_bow_cv_mean_score)
gbc_bow_test_score = gbc.score(cv_test_features, test_label_names)
print('Test Accuracy:', gbc_bow_test_score)
```

```
CV Accuracy (5-fold): [ 0.55424624  0.53827362  0.54219323  0.55206207  0.55441899]
Mean CV Accuracy: 0.548238828239
Test Accuracy: 0.547922529383
```

有趣的是，像朴素贝叶斯和逻辑回归这样更简单的模型，其表现远优于集成模型。现在，我们来看看下一个模型管道。

## 基于 TF-IDF 特征的分类模型

我们使用 TF-IDF 特征来训练分类模型。假设 TF-IDF 会降低不重要特征的权重，那么我们可能会得到性能更优的模型。让我们来验证这个假设！

```
from sklearn.feature_extraction.text import TfidfVectorizer
# build BOW features on train articles
tv = TfidfVectorizer(use_idf=True, min_df=0.0, max_df=1.0)
tv_train_features = tv.fit_transform(train_corpus)
# transform test articles into features
tv_test_features = tv.transform(test_corpus)
print('TFIDF model:> Train features shape:', tv_train_features.shape,
' Test features shape:', tv_test_features.shape)
```

```
TFIDF model:> Train features shape: (12263, 66865)  Test features shape: (6041, 66865)
```

现在，我们在这些特征上使用训练数据构建多个分类器，并使用所有分类模型在测试数据集上测试其性能。同时，我们像之前一样，通过五折交叉验证来检查模型的准确率。



