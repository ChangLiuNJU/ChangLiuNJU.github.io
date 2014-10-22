---
layout: 	post
title:  	"[Machine Learning]Logistic Regression"
date:   	2014-10-22 00:00:00-0000
modified:	2014-10-22 00:00:00-0000
categories: 
- Notes
tags:		[Notes]
---

###Introduction

In statistics, logistic regression, or logit regression, or logit model, is a type of probabilistic statistical classification model.

###Hypothesis

In logistic regression, hypothesis is defined as:

$$ h_\theta(x)=g(\theta^Tx) $$

where function g is the sigmoid function. The sigmoid function is defined as:

$$ g(z)=\frac{1}{1+e^{-z}} $$

![Sigmoid Function][Sigmoid Function]

###Decision Boundary
In a statistical-classification problem with two classes, a decision boundary or decision surface is a hypersurface that partitions the underlying vector space into two sets, one for each class. The classifier will classify all the points on one side of the decision boundary as belonging to one class and all those on the other side as belonging to the other class.

![Decision Boundary][Decision Boundary]

###Cost Function

$$ Cost(h_\theta(x), y) = \left\{\begin{matrix}-log(h_\theta(x))&if~y=1\\-log(1-h_\theta(x))&if~y=0\end{matrix}\right.$$

When \\(y=1\\), captures intuition that if \\(h_\theta(x)=0\\), predict \\(P(y=1\mid x;\theta)=0\\), but \\(y=1\\), we'll penalize learning algorithm by a very large cost.

$$ J(\theta)=\frac{1}{m}\sum_{i=1}^{m}[-y^{(i)}log(h_\theta(x^{(i)}))-(1-y^{(i)})log(1-h_\theta(x^{(i)}))] $$

###One-vs-all

Train a logistic regression classifier \\(h_\theta^{(i)}(x)\\) for each class \\(i\\) to predict the probability that \\(y=i\\).

On a new input \\(x\\), to make a prediction, pick the class \\(i\\) that maximizes \\(\underset{i}{max}h_\theta^{(i)}(x)\\)

[Sigmoid Function]:/images/sigmoid.png
[Decision Boundary]:/images/DecisionBoundary.png