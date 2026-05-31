# define two outlier detection tools to be compared
classifiers = {
"One-Class SVM": svm.OneClassSVM(nu=0.95 * outliers_fraction + 0.05,
kernel="rbf", gamma=0.1),
"Robust covariance": EllipticEnvelope(contamination=outliers_fraction),
"Isolation Forest": IsolationForest(max_samples=n_samples,
contamination=outliers_fraction,
random_state=rng),
"Local Outlier Factor": LocalOutlierFactor(
n_neighbors=35,
contamination=outliers_fraction)}
