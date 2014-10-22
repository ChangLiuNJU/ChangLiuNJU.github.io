---
layout: 	post
title:  	"[Computer System]2.Representing and manipulating Information"
date:   	2014-04-21 00:00:00-0000
modified:	2014-07-07 00:00:00-0000
categories: 
- Notes
tags:		[Notes]
---

###INT_MIN in C

{% highlight cpp %}

/* Minimum and maximum values a 'signed int' can hold */
#define INT_MAX 2147483647
#define INT_MIN (-INT_MAX -1)

{% endhighlight %}

Why not simply wirte it as either -2147483648 or 0x8000000?

Firstly, let's see the CONSTANT defined in C grammar.

{D}+{IS}?

Here {D} is [0-9], {IS} is (u|U|l|L)*. It is to say that -2147483648 is not a constant but a constant expression -(2147483648). Note that 2147483648 is over the range of int. According to C89, for an interger constant without suffix, compiler will match it in order of int, long int, unsigned long int. So if we write -2147483648 diretley, it will be an long int but not a int.