---
layout: post
title: How Math became fun
description: Learning to love Linear (and other maths too)
date: 2025-04-26 18:53 -0400
categories: [Blog, Misc]
Tags: [math, linear-algebra]
math: true
---

I used to hate math. Well, maybe *hate* isn't the best word, but I wouldn't say I necessarily *enjoyed* my math classes in High School. Indifferent, I guess, is the best descriptor of my past relationship with math. That is, until this semester.

This semester (Spring 2025) I'm in MA 26200 - Linear Algebra and Differential Equations. Having very limited experience with either of the topics, I was apprehensive about entering a new realm of math. Until I discovered the YouTube channel [3 Blue 1 Brown](https://www.youtube.com/@3blue1brown) and their Intro to Linear Algebra series. I was immediately fascinated with their complex (and often beautiful) visuals explaining how Linear Algebra worked on a quantitative and qualitative level - and completely changed my view towards math. The previously uninteresting world of math now seemed inviting, and I became curious to see what was inside. After my lecture on Eigenvectors and Eigenvalues, I immediately went and watched [3b1b's video](https://youtu.be/PFDu9oVAE-g?si=yKGDEsKRTnkzAhYW) on the topic. And to my surprise (and delight), he ended the video with this challenge problem:

<img src="assets/posts/learning-to-love-math/problem.png" alt="problem image" width="650"/>

Instinctively, I almost rejected the idea of doing a math problem what I wasn't required to do. But after staring at the problem a few more times, my curiosity took over and I decided to give it a shot. How hard could it be? (spoiler alert: actually kinda hard but in a good way)

The problem starts by telling you to compute the first few powers of the matrix $$A = \begin{bmatrix}0 & 1\\1 & 1\end{bmatrix}$$ by hand, yielding the following results:

$$
$$
$$
\qquad
A ^ 1 = \begin{bmatrix}0 & 1\\1 & 1\end{bmatrix} \quad
A ^ 2 = \begin{bmatrix}1 & 1\\1 & 2\end{bmatrix} \quad
A ^ 3 = \begin{bmatrix}1 & 2\\2 & 3\end{bmatrix} \quad
A ^ 4 = \begin{bmatrix}2 & 3\\3 & 5\end{bmatrix} \quad
A ^ 5 = \begin{bmatrix}3 & 5\\5 & 8\end{bmatrix} \quad
A ^ 6 = \begin{bmatrix}5 & 8\\8 & 13\end{bmatrix} \quad
$$

Initially the first three powers appear to follow a simple pattern (each term gets added by one), and at first I almost stopped there and declared the problem solved. But after computing the 4th power, I realized that the pattern was not as simple as I thought, and maybe I actually had to do some math.

In order to develop a formula for $$A^n$$, we first have to 'diagonalize' it - that is, convert it to a matrix where all values outside of the diagonal are zero. That's because the nth power of a diagonal matrix is the nth power of each term. For example: if $$A = \begin{bmatrix}a & 0\\0 & b\end{bmatrix}$$, then $$A^n = \begin{bmatrix}a^n & 0\\0 & b^n\end{bmatrix}$$

Now, in order to convert our original transformation matrix $$A$$ into a diagonal matrix, we can take advantage of a property of diagonal matrices: they are scalar transformations and have no rotational effect when applied (multiplied) to a vector. Now, this property should be setting off alarm bells for any linear algebra student. Why? It's very similar to the definition of an eigenvector! Thus, if we can express $$A$$ *in terms of the eigenvectors*, we will have a diagonal matrix, which we can then easily raise to the power of n. 

There's just one problem: how can we express a matrix in terms of another set of vectors? Well, it's conceptually pretty simple: right now, we're used to expressing all 2D vectors and matrices in terms of our standard basis vectors $$\hat{\imath} = \begin{bmatrix}1 \\ 0\end{bmatrix}$$ and $$\hat{\jmath} = \begin{bmatrix}0 \\ 1\end{bmatrix}$$. What this means *mathematically* is that a vector $$\begin{bmatrix}a \\ b\end{bmatrix}$$ can represented as $$a\cdot\hat{\imath}  +  b\cdot\hat{\jmath}$$. By this logic, we could utilize any set of vectors as our basis vectors, and express any vector or matrix in terms of our new basis vectors. And here's the important piece: if we choose the eigenvectors of a matrix as the basis vectors, then said matrix in terms of that new basis would be a diagonal matrix.

That may be a lot of information at once, but take a second to conceptualize it, and recall the definition of an eigenvector: $$\vec{V}M = \lambda M$$. 
