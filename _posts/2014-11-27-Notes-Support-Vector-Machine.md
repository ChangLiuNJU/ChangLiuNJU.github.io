---
layout: 	post
title:  	"[Machine Learning]Support Vector Machine"
date:   	2014-11-27 00:00:00-0000
modified:	2014-11-27 00:00:00-0000
categories: 
- Notes
tags:		[Notes, ML]
---

###Introduction

>In machine learing, support vector machine are supervised learning models with associated learning algorithms that analyze data and recognize patterns, used for classification and regression analysis. 
>
>A SVM model is a representation of the examples as points in space, mapped so that the examples of the separate categories are divided by a clear gap that is as wide as possible. New examples are then mapped into that same space and predicted to belong to a category based on which side of the gap they fall on.

![SVM][SVM]

###Hypothsis

![Hypothsis][Hypothsis]

Hypothsis function in SVM(red line) has a little different from what we used in logistic regression. In some way, they have pretty similar effects, but the SVM hypothsis turns out support vector machine computational advantage. 

###Cost Function

$$ \underset{\theta}{min}C\sum_{i=1}^{m}\left[y^{(i)}cost_1(\theta^Tx^{(i)})+(1-y^{(i)})cost_0(\theta^Tx^{(i)})\right]+\frac{1}{2}\sum_{i=1}^{n}\theta_j^2 $$

In SVM cost function, the regularzation term is not $$ \frac{\lambda}{2m}\sum_{j=1}^n\theta_j^2 $$ which was used in logsitic regression. Here, without using the constant term $$ \frac{1}{m} $$ , we alternate regularzation parameter $$ \lambda $$ to $$ C $$ which is equal to $$ \frac{1}{\lambda} $$ in number, but the same in essence.

###Large Margin

When we set $$ C $$ to a large value, which means we have a huge punishment on miss-classify, we want the first term of cost function $$ C\sum_{i=1}^{m}\left[y^{(i)}cost_1(\theta^Tx^{(i)})+(1-y^{(i)})cost_0(\theta^Tx^{(i)})\right] $$ to be zero. 

That means we want:

$$ \begin{Bmatrix}\theta^{T}x\geq1&when \ y = 1\\\theta^{T}x\leq-1&when \ y=0\end{Bmatrix} $$

And our cost function can come down to:

$$ \underset{\theta}{min}\frac{1}{2}\sum_{i=1}^{n}\theta_j^2 $$

![p][p]

Here $$ \theta_j^2 $$ can be write as $$ \left\|\theta\right\| $$, which denote the length of vector $$ \theta $$. And vector $$ p $$ denotes the projection of $$ x $$ on  $$ \theta $$. So $$ \theta^{T}x $$ can be represent as $$ \left\|p\right\|\cdot \left \| \theta \right \| $$ 

![DecisionBoundary][DecisionBoundary]

If we simplify the $$ \theta_0 $$ to be zero, Decision boundary comes to a vector crosses the origin, and $$ \theta $$ is a vector vertical to it in SVM. Here we can see $$ p $$ is actually the margin between sample $$ x $$ and the decision boundary. And if we want our optimalzation objective $$ p\cdot\left\|\theta\right\| $$ to be great than 1 or less than -1, we need a large $$ \left\|p\right\| $$, which means we need a large margin!

###Kernel Function

SVM can not only solve the linear separable problem, it also works well in non-linear situation by the powerful tool-Kernel Function.

In the previous claasification method we mentioned, the way to solve non-linear separable problem is to import some hige degree polynomial term as new featrue. It may not much efficient when the problem already has lots of featrues at the first.
Kernel function gives us a way to map the orginal sample space to a new high demension space.

**Gaussian Kernel** maps the origin sample space to $$ \mathbb{R}^m $$ space(m = # of sample). For a give $$ x^{i} $$, gaussian kernal take the similarity between $$ x^{i} $$ and $$ x^1, x^2, ..., x^m $$(landmarks) as new features. It works well especially when the sample amount is much larger than the number of feature. The similarity is defined as:

$$ f^{(i)}=similarity(x,l^{(i)})=exp\left(-\frac{\left\|x-l^{(i)}\right\|^2}{2\sigma^2}\right) $$

Let's down to the similarity function. If $$ x $$ is similar to $$ l^{(i)} $$, the function will output something close to 1, and if $$ x $$ is far from the landmark, the output will be zero.

For Gaussian Kernel SVM, there are two parameters $$ C $$ and $$ \sigma $$. Parameter $$ C $$ controls the punish on miss classified sample. With large $$ C $$, the model will be more overfitting, and has lower bias and high variance. Parameter $$ \sigma $$ controls the smoothly of new feature $$ f^{(i)} $$. With large $$ \sigma $$, feature $$ f^{(i)} $$ vary more smoothly, and the model will have higer bias and lower variance.

**Linear Kernel** is also a common kernel. But actually it means we do SVM with out kernel. And it has a similar power to Logistic Regression.

Kernel function is a powerful tool for SVM. Actually, we can perform kernel on other classifier, but it is much more computationally expensive than SVM. Excepte Gaussian Kernel and Linear Kernel, there are also some other [kernel function][Kernel function] for diffierent problem such as String Kernel, Graph Kernel and so on.

Nowaday, SVM is one of the most powerful classifier in machine learning, and it's widely used as "black box" by the complex implement. 


[Hypothsis]:/images/SVM_Hypothsis.png
[SVM]:/images/SVM.png
[DecisionBoundary]:/images/SVM_db.png
[p]:/images/SVM_p.png
[Kernel function]:http://en.wikipedia.org/wiki/Kernel_method