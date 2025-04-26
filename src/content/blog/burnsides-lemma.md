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

Before we apply Burnside's Lemma to this problem, let's formalize the lemma's general conditions. The lemma requires a [**group**](https://en.wikipedia.org/wiki/Group_(mathematics)) of actions $G$ which act on a finite set $X$. In our case; in our case, $G$ consists of rotation by 0°, 90°, 180°, and 270°, and $X$ consists of all possible paintings, not necessarily rotationally distinct.

Note that the term [**group**](https://en.wikipedia.org/wiki/Group_(mathematics)) has a very specific meaning in a math context. Importantly, if $a \cdot b$ denotes the action derived by first performing $b$, then $a$, then a group $G$ must satisfy the following four conditions:
>[!Note] 
> - If two actions $a, b$ are in $G$, $a \cdot b$ must be in $G$ as well. 
> - There must be an identity element $0$.
> - Each element $a$ must have an inverse element $a^{-1}$ such that $a \cdot a^{-1} = 0$
> - $+$ must be associative; that is, $(a \cdot b) \cdot c$ = $a \cdot (b \cdot c)$

 For instance, if we removed 270° = 180° + 90° from $G$, $G$ would no longer be a group.

You can see this is basically a generalization on the traditional notions of multiplication and division, although it does drop the commutative property.

With that out of the way, let's return to our original problem. In group theory terms, our problem is equivalent to finding the number of *orbits* of $X$ when acted upon by $G$, denoted $|X / G|$. 
![](../../assets/images/Pasted%20image%2020250202135054.png)
*The six orbits for our problem. This set of sets is $X / G$, and its size $|X / G|$ is the number of orbits.*

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

Let's visualize the action of $G \cdot x$ like so:
![md](../../assets/images/Pasted%20image%2020250425120436.png)
If we construct such graphs for other possible states and transformations, we notice an interesting property: for every possible initial state $x$, every final state will have the same number of edges going into it. For instance, in the image above, with the initial state being the bottom grid, every final state has exactly 2 edges going into it. Let's denote this number of edges as $z_x$.

>[!Note]
> I hope the fact that each state has the same number of in-edges feels intuitive to you, and as such, I will not provide a proof here. If you are stuck, the most important property to remember here is the closure property of $G$.

This means that the total number of actions, $|G|$, can be written as $|G| = \text{(number of states)} \cdot \text{(number of edges entering each state)} = |G \cdot x| \cdot z_x$. 

Now we just need to know the best way to determine $z_x$. Since the number of incoming edges to all final states is the same, the most intuitive choice for the final state to consider is $x$ itself. Thus, we can define $G_x$ simply as the set of operations which leave $x$ unchanged, more commonly referred to as the [stabilizer of $x$](https://mathworld.wolfram.com/Stabilizer.html), and it then follows that $z_x = |G_x|$. We now arrive at the following important result, the **orbit-stabilizer theorem**:
$$
|G| = |G \cdot x| \cdot |G_x|
$$
This means we can rewrite our above summation like so:
$$
|X/G| = \sum_{x\in X}\frac{1}{|G \cdot x|} = \sum_{x\in X}\frac{|G_x|}{|G|} = \frac{1}{|G|} \sum_{x\in X} |G_x|
$$
To get Burnside's lemma, we need to apply one more trick to evaluate $\displaystyle\sum_{x\in X}|G_x|$. Consider the following diagram:
![md](../../assets/images/Pasted%20image%2020250425123825.png)
A check/cross in a given position indicates whether each operation is a stabilizer of each state. Our current summation sums the red numbers (along the rows and where each red number is derived by counting the number of check marks in that row) but a standard combinatorial trick is to try summing the blue numbers (along the columns) instead. Observe that the blue number corresponding to each operation $g$ is precisely the number of elements $x$ for which $g \cdot x = x$, also referred to as the number of **fixed points** of $g$, $|X^g|$. 

>[!Note]
>This trick is more commonly referred to as *swapping the order of summation* as we can rewrite the visual approach presented above like so:
>$$
> \sum_{x\in X} |G_x| = \sum_{x\in X} \sum_{g\in G_x} 1 
> = \sum_{g\in G}\sum_{x, g\in G_x} 1 = \sum_{g\in G} |X^g|
> $$

Putting it all together, we finally arrive at Burnside's lemma:
 $$
 |X/G| = \sum_{x\in X}\frac{1}{|G \cdot x|} = \sum_{x\in X}\frac{|G_x|}{|G|} = \frac{1}{|G|} \sum_{x\in X} |G_x| = \frac{1}{|G|} \sum_{g\in G} |X^g|
 $$

I hope this article demystified the lemma for you, and please feel free to open an issue on GitHub if you have any further questions!

