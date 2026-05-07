# fit the data and tag outliers
if clf_name == "Local Outlier Factor":
y_pred = clf.fit_predict(X)
scores_pred = clf.negative_outlier_factor_
else:
clf.fit(X)
scores_pred = clf.decision_function(X)
y_pred = clf.predict(X)
threshold = stats.scoreatpercentile(scores_pred,
100 * outliers_fraction)
n_errors = (y_pred != ground_truth).sum()
