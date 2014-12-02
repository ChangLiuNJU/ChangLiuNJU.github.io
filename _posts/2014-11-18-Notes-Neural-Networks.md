---
layout: 	post
title:  	"[Machine Learning] Neural Networks"
date:   	2014-11-18 00:00:00-0000
modified:	2014-11-19 00:00:00-0000
categories: 
- Notes
tags:		[Notes, ML]
---

###Introduction

>In machine Learning and related fields, artificial neural network (ANNS) are computational models inspired by an animal's central nervous systems (in particular the brain), and are used to estimate or approximate functions that can depend on a large number of inputs and are generally unknown.

![Neural][Neural]

In animal's brain, hundreds of thounsands nerve cell called "Neural" comprise the nervous systems. A neural has three parts: dendrites, cell body and axon. Dendrites receive biological signals from the prev-neural and the cell body do some processing, then the axon output the processed signal to the next-neural.  



###Hypothsis

![Artificial Neural][Artificial Neural]

In ANNS, we abstract the neural as logistic unit which consists of an input unit $$ [x_1, x_2, ..., x_n] $$, a processed unit $$ f(x) $$ and an output $$ y $$.  

For a given input vector $$ x $$, the logistic unit caculate the output by:

$$ z=\theta^T{x} $$ 

$$ h_\theta(x)=sigmoid(z) $$

Here, we denote a temporary result for caculating hypothsis as $$ z $$. And generally, we take a bias unit $$ x_0 $$ which equals +1 as the first input. 

![ANNS][ANNS]

Similar as animal's nervous system, lots of logistic unit construct a network. The basic struct of ANNs consists of an input layer, one or few hidder layers and an output layer.

###Forward propagation

For an given ANNS as above, We have few denotations:

+ $$ L $$: total no. of layers in network
+ $$ s_l $$: no. of units(not counting bias unit) in layer $$ l $$
+ $$ K $$: no. of classes(meaningful when $$ K \geq 3 $$)
+ $$ g(x) $$: sigmoid function $$ g(x)=\frac{1}{1+e^{-x}} $$
+ $$ a_i^{(l)} $$: "activation" of unit _i_ in layer _l_
+ $$ \Theta^{(j)} $$: matrix of weghts controlling function mapping from layer _j_ to _j+1_

Forward propagtion caculates a predict result layer by layer. Firstly, we denote input layer $$ x $$ as $$ a^{(1)} $$. Then for a specific layer $$ l, a^l = g((\Theta^{l-1})^Ta^{l-1}) $$. Finally, we have a $$ K\times1 $$ demension output layer $$ a^L $$ which looks like:

$$ a^L\approx\begin{bmatrix}0\\0\\...\\1\\...\\0\end{bmatrix} $$

###Cost Function

$$ J(\Theta)=-\frac{1}{m}\left[\sum_{i=1}^{m}\sum_{k=1}^{K}y_k^{(i)}\log (h_\Theta(x^{(i)}))_k+(1-y_k^{(i)})\log (1-(h_\Theta(x^{(i)}))_k)\right]\\+\frac{\lambda}{2m}\sum_{l=1}^{L-1}\sum_{i=1}^{s_l}\sum_{j=1}^{s_{l+1}}(\Theta_{ji}^{(l)})^2 $$	

###Backpropagation

To minimize cost function $$ J $$, we need to caculate the partial derivative of $$ J $$ which is diffcult to compute for neural networks.

Backpropagation algorithm is a convenient way to compute the partial derivative. In backpropagation, we denote $$ \delta_j^{(l)} $$ as "error" of node j in layer l. After perform forward propagation, we using the output of i-th sample $$ y^{(i)} $$ to compute $$ \delta^{(L)}=a^{(L)}-y^{(i)} $$.

$$ \delta^{(l)}=(\Theta^{(l)})^T\delta^{(l+1)}.*g'(z^{(l)}), ~l\subseteq [1, L-1] $$


$$ \Delta_{ij}^{(l)}=\Delta_{ij}^{(l)}+a_j^{(l)}\delta_i^{(l+1)} $$

$$ \frac{\partial }{\partial \Theta_{ij}^{(l)}}J(\Theta)=D_{ij}^l=
\begin{cases}
\frac{1}{m}\Delta_{ij}^{(l)}+\lambda\Theta_{ij}^{(l)} & \text{ if } j\neq 0 \\ 
\frac{1}{m}\Delta_{ij}^{(l)} & \text{ if } j=0 
\end{cases} $$

To train a neural network:

1. Randomly initialize weights
2. Implement forward propagation to get $$ h_\Theta(x^{(i)}) $$ for any $$ x^{(i)} $$
3. Implement code to compute cost function $$ J(\Theta) $$
4. Implement backprop to compute partial derivatives $$ \frac{\partial }{\partial \Theta_{ij}^{(l)}}J(\Theta) $$
5. Use gradient checking to compare $$ \frac{\partial }{\partial \Theta_{ij}^{(l)}}J(\Theta) $$ computed using backpropagation vs. using numerical estimate of gradient of $$ J(\Theta) $$.
Then disalbe gradient checking code.
6. Use gradient descent or advanced optimizaition method with backpropagation to try to minimize $$ J(\Theta) $$ as a function of parameters $$ \Theta $$  



[Neural]:/images/neural.png
[Artificial Neural]:/images/artificial_neural.png
[ANNS]:/images/anns.png
