---
layout:	post
title:	"Machine Learning Notes: Lecture 7"
description: "Andrew Ng Machine Learning Notes for Lecture 7: Regularization"
date:	2019-07-15
tags:	["Machine Learning", "Andrew Ng"]
---

# 7. Regularization

## 7.1 Underfitting & Overfitting
Underfitting happens when a model has too few parameters. It is also called "High Bias". While on the other hand, overfitting happens when a model has too much parameters, which is also called "High Variance". When a model overfits, it is more likely to fit the training set very well, but cannot generalize to new examples.

There are two ways to address overfitting problem:
1. Reduce the number of features.
    * Manually select which features to keep.
    * Use model selection algorithm.
2. Use regularization. It keeps all the features, but reduces the magnitude/values of parameters $$\theta_j$$. It works well when there are plenty of features each of which only contributes a little to predicting $$y$$.

## 7.2 Regularization 
We can use small value of $$\theta_0, \theta_1,...,\theta_n$$ to make our model less prone to overfitting. We do this by adding a regularization term after the original cost function (suppose we are using linear regression):

$$J(\theta) = \frac{1}{2m} (\sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})^2 + \lambda \sum_{j=1}^n \theta_j^2)$$

where $$\lambda$$ is called regularization parameter. When $$\lambda$$ is too small, it is still prone to overfitting; but if $$\lambda$$ is too big, it would be prone to underfitting.

## 7.3 Gradient Descent
The update rules would then become:

$$
\left\{
    \begin{array}{ll}
    \theta_0 = \theta_0 - \alpha \frac{1}{m} \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)}) x_0^{(i)},\\
    \theta_j = \theta_j (1 - \alpha \frac{\lambda}{m}) - \alpha \frac{1}{m} \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)}) x_j^{(i)}, 1 \leq j \leq n
    \end{array}
\right.
$$

## 7.4 Normal Equation
If we use normal equation to get the $$\theta$$, then it would become:

$$\theta = (X^T X + \lambda A)^{-1} X^T y$$

where $$
A = \begin{bmatrix}
    0 & 0 & 0 & ... & 0\\
    0 & 1 & 0 & ... & 0\\
    0 & 0 & 1 & ... & 0\\
      &   &   ... &  \\
    0 & 0 & 0 & ... & 1
\end{bmatrix}
$$, and is of size $$(n+1) \times (n+1)$$. And it can be proved that when $$\lambda > 0$$, $$(X^T X + \lambda A)$$ is always invertible.

## 7.5 Logistic Regression
We add a regularization term to the cost function:

$$J(\theta) = -\frac{1}{m} \sum_{i=1}^m y^{(i)} log h_\theta(x^{(i)}) + (1 - y^{(i)}) log(1 - h_\theta(x^{(i)})) + \frac{\lambda}{2m} \sum_{j=1}^n \theta_j^2$$

And the update rules in gradient descent becomes (also identical with that in linear regression):

$$
\left\{
    \begin{array}{ll}
    \theta_0 = \theta_0 - \alpha \frac{1}{m} \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)}) x_0^{(i)},\\
    \theta_j = \theta_j (1 - \alpha \frac{\lambda}{m}) - \alpha \frac{1}{m} \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)}) x_j^{(i)}, 1 \leq j \leq n
    \end{array}
\right.
$$
