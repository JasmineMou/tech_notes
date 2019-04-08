## Feature selection
https://en.wikipedia.org/wiki/Dimensionality_reduction
https://en.wikipedia.org/wiki/Feature_selection
3 strategies:
- filter (ex. information gain)
- wrapper (ex. search guided by accuracy)
- embedded (ex. features are selected to add or to be removed while building the model based on the prediction errors)


## kernel tricks    
https://en.wikipedia.org/wiki/Kernel_method

kernel methods/trick: transforming data into another dimension that has a clear dividing margin btw classes of data. it achieves so by using the kernel functions. 

kernel functions: instead of computing the coordinates of the data in their high-dimensional, implicit feature space, kernel methods compute the inner products btw the images of all pairs of data in the feature space. such operation is computationally cheaper than the explicit computation of coordinates. kernel functions have been introduced for sequence data, graphs, text, images, and vectors. 

which algorithms are capable of operating with kernels?
- kernel perceptron, SVM, gaussian processes, PCA, canonical correlation analysis, ridge regression, linear adaptive filters, spectral clusterings, etc,.
- any linear model can be turned into a non-linear model by applying the kernel trick to the model: replacing its feature/predictors by a kernel function. 

## Positive (Semi) Definite matrices
- a special case of Hermitian Matrix
- defineness is useful for optimization by quadratic forms on P.D. matrics: 
	(x^T)(A)(x)
- are always positive for nonzero x, and are convex
- desirable for optimization as it guarantees minima and maxima
- allows Hessian matrix to optimize multivariate functions
- helps solve higher rank matrics with Cholesky's decomposition: A = (L)(L^T)
	Ax=b --> x = (A^(-1))b      [1]
	A is P.D. --> decompose A into a product of a lower triangular matrix L and its transpose. 
	--> A = (L)(L^T)      [2]
	find inverse of (L) and (L^T) makes it computationally effective: [1][2] --> x = (L^T)^(-1) (L^(-1)) b
https://en.wikipedia.org/wiki/Kernel_method   
https://towardsdatascience.com/what-is-a-positive-definite-matrix-181e24085abd 
https://math.stackexchange.com/questions/217244/what-is-the-importance-of-definite-and-semidefinite-matrices    


## Hermitian Matrix
- complex matrix: each of off-diagonal entry is conjugate to each other, with diagonal entries being real (don't have to be symmetric). 
ex. M = 
	| 1		2+3i |
	| 2-3i     8 |
- real matrix: symetric matrices satisfying M = M^T; a real symmetric metric is a special case of Hermitian. 


## instance-based/memory-based learning
- called instance-based as its conducts hypotheses directly from the training instances themselves. 
- instead of performing explicit generalization, compares new problem instances with instances seen in training that have been stored in memory. when predicting a value/class for a new instance, they compute distances/similarities btw this instance and the training instances to make decisions. 

advantage
	ability to adapt its model to previously unseen data
disadvantage
	hypothesis complexity can grow with the data. 
	worst-case: a hypothesis is a list of n training items; computational complexity of classifiying a single new instance is O(n). 
	to battle complexity of storing all training instances & overfitting to noise in the training set: instance reduction algorithm.
example
	k-nearest neighbors algorithm
	kernel machines
	RBF networks


## homoscedasticity
homogeneity of variance/covariance: variance among group variables are the same across levels of predictors. 
	LDA can be used when covarances are equal; quadratic discriminant analysis can be used when covariances are not equal.
https://en.wikipedia.org/wiki/Homoscedasticity


## multicollinearity
predictive power can decrease w/ an increased correlation btw predictor variables.





