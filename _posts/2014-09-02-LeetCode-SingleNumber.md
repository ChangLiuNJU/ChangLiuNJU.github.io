---
layout: 	post
title:  	"Single Number"
date:   	2014-09-02 00:00:00-0000
modified:	2014-09-02 00:00:00-0000
categories: 
- LeetCode
tags:		[LeetCode]
---

>Given an array of integers, every element appears twice except for one. Find that single one.

>**Note:**
Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

A ^ A = 0

A ^ B ^ A = B

{% highlight python %}
class Solution:
	# @param A, a list of integer
	# @return an integer
	def singleNumber(self, A):
		for i in range(1,len(A)):
			A[i] = A[i] ^ A[i-1]
		return A[-1]
{% endhighlight %}
[See the problem on LeetCode](https://oj.leetcode.com/problems/single-number/) 