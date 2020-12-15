---
layout:	post
title:	"Machine Learning Notes: Lecture 6"
description: "Andrew Ng Machine Learning Notes for Lecture 6: Logistic Regression"
date:	2019-07-15
tags:	["Machine Learning", "Andrew Ng"]
---

# 6. Logistic Regression

## 6.1 Binary Classification Problem
Suppose we want to classify a tumor into {malignant, benign} according to its size, and we use linear regression. We have $$h_\theta(x) = \theta^T x$$, and we define malignant = 1 and benign = 0, then we say a tumor is malignant if $$h_\theta(x) \geq 0.5$$:

![Use Linear Regression in Classification 1]({{ 'assets/img/post_img/2019-07-14-LinearClassification-1.png' | relative_url }})

But when a new row of training data is added, the thredshould 0.5 might be unreasonable:

![Use Linear Regression in Classification 2]({{ 'assets/img/post_img/2019-07-14-LinearClassification-2.png' | relative_url }})

Besides, a linear function $$h_\theta(x)$$ can be greater than 1 or less than 0, which also makes no sense.

## 6.2 Logistic Function
Thus, we apply a non-linear function on the origin hypothesis function $$h_\theta(x)$$. The function is called *Sigmoid Function* or *Logistic Function* as well: $$\frac{1}{1+e^{-z}}$$. Its range is $$[0, 1]$$ and its value is 0.5 when $$z = 0$$. As a result, the hypothesis function becomes:

$$h_\theta(x) = \frac{1}{1 + e^{-\theta^T x}}$$

We can see that when $$\theta^T x \geq 0$$, $$h_\theta(x) \geq 0.5$$, and when $$\theta^T x < 0$$, $$h_\theta(x) < 0.5$$. So we can consider the function as the probability for $$y = 1$$ on input $$x$$ parameterized by $$\theta$$, which is to say: 
$$h_\theta(x) = P (y=1|x;\theta)$$

## 6.3 Decision Boundary
Here comes a special case when $$h_\theta(x) = 0$$. It means the probability is 0.5, so we call it *the decision boundary*. E.g. suppose we have $$h_\theta(x) = g(\theta_0 + \theta_1 x_1 + \theta_2 x_2)$$ where $$g(z)$$ is the logistic function, and $$\theta = \begin{bmatrix}-3\\1\\1\end{bmatrix}$$, then we can tell that the decision boundary is $$x_1 + x_2 - 3 = 0$$, which is shown below:

![Decision Boundary]({{ 'assets/img/post_img/2019-07-14-DecisionBoundary.png' | relative_url }})

The decision boundary may not be a linear function. Suppose we have $$h_\theta(x) = g(\theta_0 + \theta_1 x_1 + \theta_2 x_2 + \theta_3 x_1^2 + \theta_4 x_2^2)$$, and $$\theta = \begin{bmatrix}-1\\0\\0\\1\\1\end{bmatrix}$$, then the decision boundary is $$x_1^2 + x_2^2 = 1$$, like the picture below:

![Decision Boundary Circle]({{ 'assets/img/post_img/2019-07-14-DecisionBoundaryCircle.png' | relative_url }})

## 6.4 Cost Function
As you may have already worried about, after we applied the logistic function on the original hypothesis function, the cost function may not be a convex. And it is the truth. We cannot apply gradient descent method unless the cost function is a convex, and the reason is we have to ensure that there is only one local minimum point which is also the very global minimum point. As a result, we have to change our cost function into a convex. We define:

$$J(\theta) = \frac{1}{m} \sum_{i=1}^m Cost(h_\theta(x^{(i)}), y^{(i)})$$, and 

$$Cost(h_\theta(x), y) = -y log(h_\theta(x)) - (1-y) log(1 - h_\theta(x))$$, or we can rewrite it in a more intuitive way:

$$
Cost(h_\theta(x), y) = \left\{
\begin{array}{ll}
-log(h_\theta(x)), y=1\\
-log(1 - h_\theta(x)), y=0
\end{array}
\right.
$$

And the plots for $$y = 0$$ and $$y = 1$$ is as below:

![Cost-when-y=1]({{ 'assets/img/post_img/2019-07-15-Cost-1.png' | relative_url }})
![Cost-when-y=2]({{ 'assets/img/post_img/2019-07-15-Cost-2.png' | relative_url }})

We can easily find that when $$y = 1$$ and $$h_\theta(x) = 1$$, then the cost is 0; when $$y = 1$$ but $$h_\theta(x) = 0$$, then the cost would be positive infinity. Also, when $$y = 0$$ and $$h_\theta(x) = 0$$, then the cost is 0; when $$y = 0$$ and $$h_\theta(x) = 1$$, then the cost would be positive infinity.

So the cost function would be:

$$J(\theta) = -\frac{1}{m} \sum_{i=1}^m y^{(i)} log h_\theta(x^{(i)}) + (1 - y^{(i)}) log(1 - h_\theta(x^{(i)}))$$

## 6.5 Gradient Descent & Other Optimized Algorithms
Our update rules in gradient descent looks identical with that in linear regression:

$$\theta_j := \theta_j - \alpha \frac{1}{m} \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)}) x_j^{(i)}$$

Given the code to calculate $$J(\theta)$$ and $$\frac{\partial}{\partial \theta_j} J(\theta)$$, we have several optimized algorithms instead of gradient descent:
* Conjugate Gradient
* BFGS
* L-BFGS

And they have the several pros and cons:
* No need to define learning rate $$\alpha$$.
* Often faster than gradient descent.
* More complex.

Here is the usage. Suppose we have $$J(\theta) = (\theta_1 - 5)^2 + (\theta_2 - 5)^2$$ and then $$\frac{\partial}{\partial \theta_1} J(\theta) = 2(\theta_1 - 5)$$ and $$\frac{\partial}{\partial \theta_2} J(\theta) = 2(\theta_2 - 5)$$. We have (in Octave):
```
function [jVal, gradient] = costFunction(theta)
    jVal = (theta(1) - 5) ^ 2 + (theta(2) - 5) ^ 2;
    gradient = zeros(2, 1);
    gradient(1) = 2 * (theta(1) - 5);
    gradient(2) = 2 * (theta(2) - 5);
```

Then we can make use of this costFunction and the command `fminunc`:
```
options = optimset('GradObj', 'on', 'MaxIter', '100');
initialTheta = zeros(2, 1);
[optTheta, functionVal, exitFlag] = fminunc(@costFunction, initialTheta, options);
```

## 6.6 Multi-class Classification
It is quite easy to adjust the method for binary classification to multi-class cases. The adjustment is called "One vs All" or "One vs Rest". Suppose we have 3 classes A, B, and C to classify into, then we can classify the data into (A, not-A), (B, not-B), (C, not-C) seperately, and get 3 hypotheses functions and their parameters. Then for a given data, we seperately calculate its probability of being in class A, B, and C and take the highest probable class as the result.

![One vs All]({{ 'assets/img/post_img/2019-07-15-OneVsAll.png' | relative_url }})
