---
layout:	post
title:	"Machine Learning Notes: Lecture 2"
description: "Andrew Ng Machine Learning Notes for Lecture 2: Linear Regression With One Variable"
date:	2019-07-13
tags:	["Machine Learning", "Andrew Ng"]
---

# 2. Linear Regression with One Var
Also called *univariate linear regression*. It is a supervised learning.

## 2.1 Model
Suppose the training set (dataset) is $$T=\{(x^{(i)}, y^{(i)})|1\leq i\leq m\}$$, where $$x$$ is feature(s) and $$y$$ is target(s), we are going to get a hypothesis function denoted by $$h$$ through a learning algorithm with the training set $$T$$ as input. And the $$h$$ takes $$x$$ as input and estinates $$y$$. It is like the following:

$$h_\theta(x) = \theta_0 + \theta_1x$$

## 2.2 Cost Function
Our goal is to choose a $$\theta = (\theta_0, \theta_1)$$ to minimize the distance between $$h_\theta(x)$$ and $$y$$. And we denote the cost function as:

$$J(\theta_0, \theta_1) = \frac{1}{2m}\sum_{i=1}^m (h_\theta(x^{(i)})-y^{(i)})^2$$

It is also called the *Squared Error Cost Function*. And our goal is to find $$\theta_0, \theta_1$$ such that we get: $$\min_{\theta_0, \theta_1} J(\theta_0, \theta_1)$$

## 2.3 Gradient Descent
The cost function is a curve. And our goal is to find its lowest point. You start from a point you like, and then find a direction in which you can go down as rapidly as possible, and finally when all directions go up, you are at the local lowest point. The algorithm is depicted in the picture below:

![Gradient Descent]({{ 'assets/img/post_img/2019-07-13-GradientDescent.png' | relative_url }})

The algorithm can be described as follows:

> repeat until converge {
> 
> &emsp;$$\theta_j := \theta_j - \alpha \frac{\partial}{\partial \theta_j} J(\theta_0, \theta_1), j=0,1$$
> 
> }

The $$\alpha$$ is called *learning rate*, controlling how big a step is.

One thing to attention: the assignment should be simultaneous update, which is to say:

> &emsp;$$temp0 := \theta_0 - \alpha \frac{\partial}{\partial \theta_0} J(\theta_0, \theta_1)$$
> 
> &emsp;$$temp1 := \theta_1 - \alpha \frac{\partial}{\partial \theta_1} J(\theta_0, \theta_1)$$
> 
> &emsp;$$\theta_0 := temp0$$
> 
> &emsp;$$\theta_1 := temp1$$

The algorithm is of general purpose, which means it works when there are more than 2 args. It can work with any number of arguments. But here, for the convenience of demonstration, we only take 2 arguments.

And we apply the definition of the cost function and get the update rules:
> &emsp;$$\theta_0 := \theta_0 - \alpha \frac{1}{m} \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})$$
>
> &emsp;$$\theta_1 := \theta_1 - \alpha \frac{1}{m} \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})x^{(i)}$$

There are in fact various types of gradient descent. The one we talked above is called *batch gradient descent*, which uses all the training set in each step of gradient descent. In fact, you do not have to do the iteration if you have some knowledge on linear algebra - there is a method called *normal equation method*. But the gradient descent scales better than the normal equation method.


