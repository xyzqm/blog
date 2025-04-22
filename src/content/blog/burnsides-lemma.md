---
author: Daniel Zhu
pubDatetime: 2025-02-02T13:39:04-08:00
title: Burnside's lemma
slug: burnsides-lemma
featured: true
draft: false
tags:
  - math
  - discrete
description: An intuitive introduction to Burnside's lemma.
---
Consider the following problem: we have a 2x2 grid with 4 cells, each of which we can paint black or white. Two paintings are considered identical if they can be rotated to look the same. How many distinct paintings are there?

Before we apply Burnside's Lemma to this problem, let's formalize its general conditions. The lemma requires a [**group**](https://en.wikipedia.org/wiki/Group_(mathematics)) of actions $G$ which act on a finite set $X$. In our case; in our case, $G$ consists of rotation by 0°, 90°, 180°, and 270°, and $X$ consists of all possible paintings, not necessarily rotationally distinct.

Note that the term [**group**](https://en.wikipedia.org/wiki/Group_(mathematics)) has a very specific meaning in a math context. Importantly, if $a \cdot b$ denotes the action derived by first performing $b$, then $a$, then a group $G$ must satisfy the following condition:
>[!Note] 
> If two actions $a, b$ are in $G$, $a \cdot b$ must be in $G$ as well. 

 For instance, if we removed 270° = 180° $\cdot$ 90° from $G$, $G$ would no longer be a group.

With that out of the way, let's return to our original problem. In group theory terms, our problem is equivalent to finding the number of *orbits* of $X$ when acted upon by $G$, denoted $|X / G|$. 
![](../../assets/images/Pasted%20image%2020250202135054.png)
*The six orbits for our problem.*

Note that within each orbit, all elements are rotationally identical.
Since each of these orbits $C$ contributes 1 to our final answer, it makes sense to consider dividing each orbit's contribution evenly across its members, in which case each member should have an individual contribution $\frac{1}{|C|}$. 

Formally, let $G \cdot x$ denote the orbit $C$ to which painting $x$ belongs. 

> [!Note]
> As the notation suggests, $C$ is exactly equivalent to the set obtained by applying every action in $G$ to $x$. Make sure you understand why!

Then, we can write the following:
$$
|X/G| = \sum_{x\in X}\frac{1}{|G \cdot x|}
$$
Cool, but how does this help?

