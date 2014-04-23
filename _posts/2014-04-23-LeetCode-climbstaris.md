---
layout: 	post
title:  	"Climbing Stairs"
date:   	2014-04-23
categories: 
- LeetCode
tags:		[LeetCode]
---

>You are climbing a stair case. It takes n steps to reach to the top.

>Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

It's a simple recursion problem. The number of distinct ways to reach the n stair equals the sum of that to reach the n-1 and n-2 stair, so we have the recursive function: 

*T(n) = T(n-1) + T(n-2), T(0) = 1, T(1) = 1*

Note that, T(n) is a Fibonacci number. So we can use the Binet's formula to calculate it in O(1).

Here is my code

{% highlight python %}
class Solution:
    # @param n, an integer
	# @return an integer
	def climbStairs(self, n):
		n = n + 1
		t = 2.23606797749979
		result = (((1 + t)/2)**n - ((1 - t)/2)**n)/t
		return int(round(result.real))
{% endhighlight %}
[See the problem on LeetCode](http://oj.leetcode.com/problems/climbing-stairs/)