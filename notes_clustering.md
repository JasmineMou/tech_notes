## mean shift

https://stackoverflow.com/a/18364570/2687773
https://scikit-learn.org/stable/modules/generated/sklearn.cluster.MeanShift.html

X = np.array() # array-like, shape=[n_samples, n_features]
ms = MeanShift(bandwidth=estimate_bandwidth(X, quantile=0.1))
ms.fit(X)

## kmeans 

## kshape