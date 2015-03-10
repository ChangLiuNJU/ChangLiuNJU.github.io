---
layout: 	post
title:  	"[Algorithm] Master Theorem"
date:   	2015-01-31 00:00:00-0000
modified:	2015-01-31 00:00:00-0000
categories: 
- Notes
tags:		[Algorithm]
---

###Master Threorem

For an recurrence relation of the form:

$$ T(n)\le aT(\frac{n}{b})+cn^d  $$

The constants and function take on the following sigificance:

+ $$ n $$ is the size of the problem.
+ $$ a $$ is the number of subproblems in recursion.
+ $$ \frac{n}{b} $$ is the size of subproblem.
+ $$ cn^d $$ is the cost of the work done outside the recursive calls.

Master Threorem gives the follow asymptotic tight bound:

$$ T(n)=\begin{Bmatrix}O(logn)&a=b^d\\O(n^d)&a<b^d\\O(n^{log_{b}a})&a>b^d\end{Bmatrix} $$ 

###Proof

Assume that:

+ \\(T(1)\le 1\\) 
+ \\(T(n)\le aT(\frac{n}{b})+cn^d\\)
+ $$ n $$ is a power of $$ b $$.

Consider the recursion tree. At level \\(j,j=0,1,...,log{_b}n\\), there are \\(a^j\\) subprolbems, each of size \\(\frac{n}{b^j}\\).

Total work at level \\(j\le a^j\cdot c[\frac{n}{b^j}]^d=cn^d(\frac{a}{b^d})^j\\), total work \\(\le cn^d \sum_{j=0}^{log_bn}[\frac{a}{b^d}]^j\\)

Case 1: \\(a=b^d\\)

$$ Total work \le cn^d(log_bn+1)=O(n^dlogn) $$

Case 2: \\(a<b^d\\)

$$ Total work = O(n^d) $$

Case 3: \\(a>b^d\\)

$$ Total work = O(n^d\cdot(\frac{a}{b^d})^{log_bn})=O(a^{log_bn}) = O(n^{log_ba})$$

