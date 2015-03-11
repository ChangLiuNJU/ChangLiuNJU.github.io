---
layout: 	post
title:  	"[Algorithm] Random Quick Sort"
date:   	2015-03-10 00:00:00-0000
modified:	2015-03-10 00:00:00-0000
categories: 
- Notes
tags:		[Algorithm]
---

### Proof of $$ O(nlog(n)) $$ time with random pivot

Given an unsorted array $$ a_0,a_1,...,a_n $$, for the i-th smallest element $$ z_i $$ and j-th smallest element $$ z_j $$.

+ Let $$ C(\sigma) $$ be the number of comparison between input elements.
+ Let $$ X_{ij}(\sigma) $$ be the number of comparison between $$ z_i $$ and $$ z_j $$

So the expectation $$ E[C]=\sum_{i=1}^{n-1}\sum_{j=i}^{n}Pr[X_{ij}=1] $$

There is only one case that $$ z_i $$ and $$ z_j $$ get compared, that one of them is chosen as pivot. So $$ Pr[X_{ij}=1] = \frac{2}{j-i+1} $$

$$ E[C]=2\sum_{i=1}^{n-1}\sum_{j=i}^{n}\frac{1}{j-i+1}\le2n\sum_{k=2}^{n}\frac{1}{k}\le2nln(n) $$  

