---
layout: 	post
title:  	"[Machine Learning] K-Means"
date:   	2014-12-01 00:00:00-0000
modified:	2014-12-02 00:00:00-0000
categories: 
- Notes
tags:		[Notes, ML]
---

###Unsupervised Learning and Clustering

In pratice, we do not have the label of data all the time, and how can we get some inner information by the unlabeled data. In machine learning, the problem of unsupervised learning is that of trying to find hidden structure in unlabeled data. Clustering is the task of grouping a set of objects in such a way that objects in the same group (called a **cluster**) are more similar (in some sense or another) to each other than to those in other groups (clusters). 

###K-means

![K-means][K-means]

K-means clustering is a pupular clustering algorithm which aims to partition n observations into k clusters in which each observation belongs to the cluster with the nearest mean.

Firstly, we have some denotes:

+ $$ K $$:number of clusters
+ $$ c^{(i)} $$: index(from 1 to K) of cluster to which example $$ x^{(i)} $$ is currently assigned
+ $$ \mu_k $$: cluster centroid $$ k $$

The optimization objective can be represent as:

$$ J(c^{(1)},...,c^{(m)},\mu_1,...,\mu_l)=\frac{1}{m}\sum_{i=1}^{m}\left\|x{(i)}-\mu_{c^{(i)}}\right\|^2 $$


![iter][iter]

The algorithm first random initialize $$ \mu $$, then repeatly do the two things followed until $$ c $$ and $$ \mu $$ does not change:

1. Update $$ c $$:assign example $$ x^{(i)} $$ to the closest centroid $$ \mu_k $$
2. Update $$ \mu $$:compute each cluster centroids $$ \mu_k $$ by average of points assgigned to it.

###Random Initialization

It should have the constrain that $$ K < m $$ or else each sample will be a single "cluster". And when people implements K-means, we randomly select K samples as the initial $$ \mu $$. As the different initial $$ \mu $$ value, the K-means may fall into local optimal. To get the global optimal, we should try different initial $$ \mu $$ and iterate K-means several times.

###Chose K

When we run K-means, what the appropriate value of K?

![Elbow][Elbow]

There is one way called **Elbow method**. We plot the cost function $$ J $$ relevent to $$ K $$, and chose the minimal $$ K $$ that from which the cost decrease smoothly. As the graph looks like human's elbow, we call it Elbow method.

But sometimes, there is no obvious "Elbow" in the K-J graph. By this time, we may run K-means to get clusters to use of some later/downstream purpose(Such as T-shirt's size S, M, L, XL...). So evaluate K-means based on a metric for how well it performs for the later purpose.






[K-means]:/images/K-means.png
[iter]:/images/K-means_iter.png
[Elbow]:/images/K-means_elbow.png
