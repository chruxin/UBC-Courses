## Types of Data
Categorical vs. numerical

## Bag of Words
Replace document with word count

## Feature Transformation
Math functions, discretization or binning, scaling to comparable scales

## Discrete Summary Statistics
Mode: most often category

Quantiles: category occurring > *t* times

 - Mean and std are sensitive to extreme values/outliers

# Supervised Learning
with discrete labels, SL -> classification

 - Take features of objects & corresponding labels as inputs
 - Output a program that can predict the labels of a new object

## Decision Trees
Decision Stump: accuracy score

Pseudo-code

```
Input: feature matrix X & label vector y
for each feature j
	for each example i
		set threshold to feature j m example i
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

## Supervised Learning Notation
Prediction y_hat on test data Xtest

## IID (Independent and Identically Distributed) Assumption
All objects: same distribution, sampled independently

## Validation
use part of your dataset to approximate test error: split training objects into training set and validation set
 -> validation error gives an unbiased approximation of test error
 
## Optimization Error
when not many models to chose, optimization error is small when lots of models big
 
## Cross Validation
k-fold cross-validation {1,2,3,...,k} average errors
 
## Parametric vs. Non-parametric
Parametric models:
 
- Fixed number of parameters
- Estimate more accurately with more data

Non-parametric models:

 - Number of params grows with 'n'
 - Model gets more complicated with more data

## KNN (K Nearest Neighbours)
### Supervised learning
Non-parametric

Assumption: objects with similar feature likely have similar labels.
 - No training phase, expensive prediction
 - Problematic if features have different scales

# Ensemble Methods
Classifiers that have classifiers as input

## Boosting (AdaBoost)
Take simple classifier that underfits, improve training error

 - Fit a classifier on the training data -> give a higher weight to examples that the classifier got wrong -> Fit a classifier on the weighted data -> Tecurse

Prediction: weighted vote of individual classifier prediction
 
## Averaging
Take complex classifiers that overfit, improve test error

Input: the predictions of a set of models

Take the mode of the predictions (or average)

Stacking: fit another classifier that uses the predictions as features

## Random Forests
Average a set of deep decision trees

Fast prediction

 - Generate several bootstrap examples (63% of original objects)
 - Fit a classifier to each bootstrap sample
 - At test time, average predictions

Random Trees

When tilting each decision stump to construct deep decision tree:
 - Do not consider all features
 - Each split only consider a small number of randomly-chosen features