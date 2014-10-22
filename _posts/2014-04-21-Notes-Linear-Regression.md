---
layout: 	post
title:  	"[Machine Learning]Linear Regression"
date:   	2014-10-22 00:00:00-0000
modified:	2014-10-22 00:00:00-0000
categories: 
- Notes
tags:		[Notes]
---

###Introduction

![Linear Regression][Linear Regression]

In statistics, linear regression is an approach for modeling the relationship between a scalar dependent variable y and one or more explanatory variables denoted X.

###Hypothesis

In machine learning, Hypothesis is a function outputted by Learning Algorithm that maps from known data to the estimated result.In linear regression, we represent hypothesis as follow:

$$ h_\theta=\theta^Tx=\theta_0+\theta_1x_1 $$

###Cost Function

Cost Funcition to return a number representing how well the neural network performed to map training examples to correct output.The most widely used cost function is [Least quares][Least squares] function:

$$ J(\theta)=\frac{1}{2m}\sum_{i=1}^{m}(h_\theta(x^{(i)})-y^{(i)})^2 $$

Note: m is the number of traing examples. 

Our goal in Linear Regression is to fix the vector \\(\theta\\) to minimize the cost function \\(J\\).

###Gradient Descent

Gradient descent is a first-order optimization algorithm. To find a local minimum of a function using gradient descent, one takes steps proportional to the negative of the gradient (or of the approximate gradient) of the function at the current point. If each step of gradient descent uses all the training examples, we call it *"Batch" Gradient Descent*.

![Gradient Descent][Gradient Descent]

Especially, when the cost function \\(J\\) is a convex function, the local optimal is right the global optimal.

In batch gradient descent, each iteration performs the update:

$$ \theta_j=\theta_j-\alpha\frac{\partial }{\partial \theta_j}J(\theta)\\\mathit{(simultaneously
~update~\theta_j~for~all~j)} $$

In the formula above, \\(\alpha\\) is called as *"learning rate"*. It controls the size we take for each step. If \\(\alpha\\) is too small, gradient descent can be slow. If \\(\alpha\\) is too large, gradient descent can overshoot the minimum. It may fail to converge, or even diverge.

Fortunately, Gradient descent can converge to a local minimum, even with the learning rate \\(\alpha\\) fixed. As we approach a local minimum, gradient descent will automatically take smaller steps. So, no need to decrease \\(\alpha\\) over time. 

[Linear Regression]:/images/LinearRegression0.png
[Least squares]:http://en.wikipedia.org/wiki/Least_squares
[Gradient Descent]:/images/GradientDescent.png