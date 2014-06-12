---
layout: 	post
title:  	"Roman to Integer"
date:   	2014-05-11 00:00:00-0000
modified: 	2014-05-11 00:00:00-0000
categories: 
- LeetCode
tags:		[LeetCode]
---

>Given a roman numeral, convert it to an integer.

>Input is guaranteed to be within the range from 1 to 3999.

A String manipulation problem.

NOTE: Roman numerals has some rules, which can make the transform more convinient. [Roman numerals-Wikipedia](http://en.wikipedia.org/wiki/Roman_numerals)

Here is my code.

{% highlight python %}
class Solution:
	# @return an integer
	def ctoi(self, c):
		if   c == 'I':
			return 1
		elif c == 'V':
			return 5
		elif c == 'X':
			return 10
		elif c == 'L':
			return 50
		elif c == 'C':
			return 100
		elif c == 'D':
			return 500
		elif c == 'M':
			return 1000
	def romanToInt(self, s):
		result = 0
		prv = 1001
		for c in s:
			i = self.ctoi(c)
			if i <= prv:
				result += i
			else:
				result = result + i - prv*2
			prv = i
		return result
{% endhighlight %}
[See the problem on LeetCode](http://oj.leetcode.com/problems/roman-to-integer/)