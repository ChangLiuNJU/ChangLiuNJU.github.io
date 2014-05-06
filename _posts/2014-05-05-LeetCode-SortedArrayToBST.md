---
layout: 	post
title:  	"Convert Sorted Array to Binary Search Tree"
date:   	2014-05-05
categories: 
- LeetCode
tags:		[LeetCode]
---

>Given an array where elements are sorted in ascending order, convert it to a height balanced BST.

It is a simple recursion problem. For a list L, we just chonse L[len(L)/2] as the root. To generate the childs of root, we just do the same to L[:pivot] and L[pivot+1:].

Here is my code.


{% highlight python %}
# Definition for a  binary tree node
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
	# @param num, a list of integers
	# @return a tree node
	def sortedArrayToBST(self, num):
		if len(num) == 0:
			return None
		pivot = len(num) / 2
		root = TreeNode(num[pivot])
		root.left  = self.sortedArrayToBST(num[ : pivot])
		root.right = self.sortedArrayToBST(num[pivot + 1 : ]) 
		return root
{% endhighlight %}
[See the problem on LeetCode](http://oj.leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)