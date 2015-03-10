---
layout: 	post
title:  	"[Algorithm] Closest Pair"
date:   	2015-03-10 00:00:00-0000
modified:	2015-03-10 00:00:00-0000
categories: 
- Notes
tags:		[Algorithm]
---

###Problem

Given a set of n points \\(P= {p_1,p_2,\cdots,p_n}\\) int plane \\(R^2\\), find a pair p, q in P that has the minimal distance over all point pairs in P.

###Solution in \\(O(n^2)\\) time

Intuitively, brute-force search could slove the problem by taking \\(O(n^2)\\) time.

###Solution in \\(O(nlog(n))\\) time

Some denotes:

+ \\(P_x, P_y\\) denotes the \\(P\\) sorted by x-coordinate and y-coordinate. Take \\(O(nlog(n))\\) by compare based sorting.
+ \\(Q\\) denotes the left part of \\(P\\) according to x-coordinate.
+ \\(Q_x, Q_y\\) denotes the \\(Q\\) sorted by x-coordinate and y-coordinate. Take \\(O(n)\\) by search in \\(P_x\\) and \\(P_y\\).
+ \\(R\\) denotes the right part of \\(P\\) according to x-coordinate.
+ \\(R_x, R_y\\) denotes the \\(R\\) sorted by x-coordinate and y-coordinate. Take \\(O(n)\\) by search in \\(P_x\\) and \\(P_y\\).


For a minimal distance pair p, q, both of them lie in the same half of P or they lie splited. We define the solution as function \\(ClosestPair(P_x,P_y)\\), and our solution for finding the min-distance pair when splited as \\(ClosestSplitPair(P_x,P_y)\\)

\\(ClosestPair(P_x,P_y)\\)

1. \\((p_1, q_1)=ClosestPair(Q_x,Q_y)\\)
2. \\((p_2, q_2)=ClosestPair(R_x,R_y)\\)
3. Let \\(\delta=min(d(p_1,q_1),d(p_2,q_2))\\)
4. \\((p_3, q_3)=ClosestSplitPair(P_x,P_y,\delta)\\)
5. return best of \\((p_1,q_1),(p_2,q_2),(p_3,q_3)\\)

So, if we implement the \\(ClosestSplitPair(P_x,P_y,\delta)\\) in \\(O(n)\\) time. We will get our cost \\(T(n)=O(nlog(n))\\) by [master theorem][master theorem] with recursive function \\(T(n)=2T(\frac{n}{2})+O(n)\\)

###ClosestSplitPair in \\(O(n)\\) time

![CSP][CSP]

+ Let \\(\overline{x}\\) be the biggest x-coordinate in left of P.

+ Let \\(S_y\\) be the points of P with x-coordinate in \\([\overline{x}-\delta,\overline{x}+\delta]\\), sorted by y-coordinate.

{% highlight cpp %}
Initialize best = delta, best pair = NULL
for i = 1 to Sy.size() - 7	                    //O(n)
	for j = 1 to 7                 			    //O(1)
		Let p,q = i-th, (i+j)-th points of Sy
		if d(p, q) < best 
			best pair = (p, q), best = d(p, q) 
{% endhighlight %}

###Correctness Proof

Claim: Let $$ p\in Q, q \in R $$ be a split pair with $$ d(p,q)<\delta $$

1. p and q are members of Sy
2. p and q are at most 7 positions apart in Sy

![Proof][Proof]

Proof:

1. Trivial.
2. Draw $$ \frac{\delta}{2} \times \frac{\delta}{2} $$ boxes with center $$ \overline{x} $$ and bottom $$ min(y_1, y_2) $$ 
Sinsce the min-distance point pair on the same half is $$ \delta $$, so at most one point of P in each box.
Recall y-coordinates of p, q differ by < $$ \delta $$, so p and q lie in one of these 8 boxed.

QED.

[master theorem]:http://charlesliuchang.com/notes/2015/01/31/Notes-Master-Theorem.html
[CSP]:/images/closestsplitpair.png
[Proof]:/images/csp_proof.png