# Discrete Summary Statistics
Mode: most often category

Quantiles: category occurring > *t* times

- Mean and std are sensitive to extreme values/outliers

# Supervised Learning
with discrete labels, SL is classification

- Take features of objects & corresponding labels as inputs
- Output a program that can predict the labels of a new object

## Decision Trees

Decision Stump: accuracy score

Pseudo-code

```
Input: feature matrix X & label vector y
for each feature j
	for each example i
		set threshold to feature j in example i
		find mode of y when j > threshold
		find mode y when j <= threshold
		classify all examples based on t
		count # of errors
		store this rule if error is the lowest so far
Output: a decision stump rule
```
* Sort rules, update scores O(ndlogn)

## Greedy Recursive Splitting
Find the decision stump with the best score, split into 2 small datasets, find a decision stump to each dataset.

## IID (Independent and Identically Distributed) Assumption
All objects: same distribution, sampled independently

## Optimization Error
when not many models to choose, optimization error is small when lots of models big

## Parametric vs. Non-parametric
Parametric models:

- Fixed number of parameters
- Estimate more accurately with more data

Non-parametric models:

- Number of params grows with 'n'
- Model gets more complicated with more data

## KNN (K Nearest Neighbours)

*Non-parametric*

Assumption: objects with similar feature likely have similar labels.

Algorithm:

- Find *k* training examples xi that are most similar to x
- Classify using the mode of their yi.

Notes:

- No training phase, expensive prediction
- Problematic if features have different scales

# Ensemble Methods
Classifiers that have classifiers as input

## Boosting (AdaBoost)
Take simple classifier that underfits, improve training error

- Fit a classifier on the training data —> give a higher weight to examples that the classifier got wrong —> Fit a classifier on the weighted data —> Recurse

Prediction: weighted vote of individual classifier prediction

## Averaging
Take complex classifiers that overfit, improve test error

Input: the predictions of a set of models

- Take the mode of the predictions (or average)

Stacking: fit another classifier that uses the predictions as features

## Random Forests
Average a set of deep decision trees

Fast prediction

- Bootstrap Aggregation/Bagging:

  *Random sampling examples*

  - Generate several bootstrap examples (63% of original objects)
  - Fit a classifier to each bootstrap sample
  - At test time, average predictions

- Random Trees

  When tilting each decision stump to construct deep decision tree:

  - Do not consider all features
  - Each split only consider a small number of randomly-chosen features

# Unsupervised Learning
Only have X, no explicit target labels

Uses:

- outlier detection
- data visualization
- association rules (xi and xj occur together)
- latent-factors (which parts are xi made from)
- ranking
- clustering

## Clustering
Input: set of objects described by features xi

Output: an assignment of objects to 'groups'

## K-means

Input: K, initial guesses of means/centers

Algorithm: assign xi to closest, update means, repeat until no object change groups

- Guaranteed to converge with Euclidean distance
- New object assigned to nearest mean
- Bootstrapping, random initial means and choose best/combine
- label switching: is xi in the came cluster as xj?

## DBSCAM (Density-Based Clustering)

*Non-parametric*:

- Clusters more complicated with more data
- No fixed *k*

Hyperparams: radius, minPts

- radius: max distance between points to be considered close or reachable
- minPts: # of reachable points
- core point: point with ≥ minPts meighbours with distance ≤ radius

Pseudo-code:

```
for each xi:
	if xi is already in a cluster do nothing
	Test if xi is a core point
	--> yes: expand cluster
	--> no: do nothing

Expand fn:
	Assign all xj within distance 'r' of core point xi to cluster
	for each newly-assigned neighbour that is a core point, expand cluster
```

- Sensitive to choices of hyperparams
- For new examples, finding cluster is expensive (compute distances to training points)

## Density-Based Hierarchical Clustering
Fix minPoints, record clusters as you vary the radius

### Agglomerative (Bottom-up Clustering)
Start with each point in its own cluster, each step merges 2 closest clusters, stop when everything is one big cluster

Closest clusters:

- Average link: average distance between points in clusters
- Single-link: min distance between points in clusters
- Complete-link: max distance between points in clusters
- Ward's method: minimize within cluster variance

It's possible to use supervised learning for clustering — Image segmentation.

## Outlier Detection

*unsupervised*

### 1 Model Based Outlier Detection

Fit a probabilistic model, outliers are examples with low probability

sensitive to outliers example: Z-score

### 2 Graphical Outlier Detection
Look at plots of data and human decides

Examples:

- Box plot (only 1 variable)
- Scatterplot (detect complex patterns, only 2 variables)
- Scatterplot array (all combination of variables, only 2 variables at a time)
- Scatterplot of 2-dimensional PCA (see high-dimensional structure, PCA sensitive to outliers, might be info in higher PCs

### 3 Cluster-Based Outlier Detection
Cluster data, find points which don't belong to clusters

Examples:

- K-Means (find points far from an mean, find clusters with a small number of pints)
- DBSCAN (outliers: points not assigned to any cluster)
- Hierarchical DBSCAM (outliers take longer to join other groups, good for outlier groups)

### 4 Distance-Based Outlier Detection
Directly measure how close objects are to their neighbours

Global Distance-Based Outlier Detection — KNN Outlier Detection:

For each point, compute the average distance to its KNN, sort these values choose the biggest values as outliers

### 5 Supervised outlier Detection
Use supervised learning: yi = 0 if xi is a regular pint, yi=1 if xi is an outlier

Needs supervision: what outliers look like, may not detect new types of outliers

# Regression

*supervised*

Classification (discrete yi) —> Regression (mumerical yi)

## Linear Regression based on squared error

### Linear Regression in 1D (1 feature)
yi = wXi (w: weights/regression coefficient of Xi — Linear Model

Make predictions: y_hat = wXi_hat

Sum of squared errors:

$f(w) = \sum_{i=1}^{n}{(wX_i-y_i)^2}$

Minimizers: All *w* where ∇f(w)=0

### Notation
​                  |w1 |				  |y1 |				   | xi1 |				  |—— x1^T —— |

w (d x 1) =|w2 |  		y (n x 1) = |y2 |		xi (d x 1) = | xi2 |		X (n x d) = |—— x2^T —— |

​                  |wd |                   	   |yn|				   | xid |				   |—— xn^T —— |

### Least squares in D-Dimensions

yi = w^Txi  	f(w) = (1/2) sum (i=1 to n) (w^Txi - yi)^2 = (1/2) sum (i=1 to n) ri^2 = (1/2) ||Xw - y||^2

∇w[c] = 0, ∇w[w^Tb] = b, ∇w[(1/2)w^TAw] = Aw for symmetric A

∇f(w) = X^TXw - X^Ty

```python
w = solve(X.T@X, X.T@Y)
yhat = X@w
```

### Least Square Issues

- solution may not be unique
- sensitive tto outliers
- always uses all features
- data can be big that X^TX can't be stored
- may predict outside of range yi values
- assume linear relationship

### Model y intercept

Add a column of 1's to X (new matrix Z)

# Norms of Vectors

L1: ||r||_1 = sum (i=1 to d) |rj|

L2: ||r ||_2 = ||r|| = sqrt(sum (i=1 to d) ri^2)

L∞: max {|ri|}

# Collinearity
Two features identical for all examples. Collinear solution is not unique.

# Convex Function
*eg. e^x, X^2*

**Key:** a local minimum is also a global minimum

# Optimization
Objective function: the thing being maximized/ minimized

Dimensionalities: input can be non-scalar-values, the function should be scalar-valued

# Robust Regression
Least absolute error f(w) = ||Xw - y||_1

## Gradient Descent
An iterative optimization (minimization) algorithm: finds local minima of differentiable function

## 'Brittle' Regression
Care about outliers (for example, want the best performance on worst training example): use infinity norm

## Log-Sum-Exp Function
As with L1-norm, L∞ norm is convex but non-smooth.

Log-Sum-exp function is a smooth approximation to the max function

max(z_i) = log( \sum{exp(z_i)} )​

Intuition: largest element is magnified exponentially while smaller elements become negligible

# Runtime Summary


|                     | Decision Stump | Decision Tree                | KNN                                      | K-Means                     | DBSCAN                    |
| ------------------- | -------------- | ---------------------------- | ---------------------------------------- | --------------------------- | ------------------------- |
| Cost:               | O(ndlongn)     | O(mndlogn), m: depth of tree | size of model: O(nd) cost of prediction: O(nd) for 1 test object | O(ndk), update means: O(nd) | dompute distances O(n^2d) |
| sensitive to scales | No             |                              | Yes                                      | Yes                         | Yes                       |