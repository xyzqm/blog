---
author: Daniel Zhu
pubDatetime: 2025-01-24T15:07:35-08:00
title: Breakdown
slug: breakdown
featured: true
draft: false
tags:
  - usaco
  - dnq
description: My editorial for the USACO problem "Breakdown."
---
Here's a [link to the problem](https://usaco.org/index.php?page=viewproblem2&cpid=1260).

The first thing we should consider is how to solve the problem without any updates. Naively, we enumerate all possible paths in $\mathcal{O}(N^{K - 2})$ time. However, note that simply writing $K - 2$ nested loops recomputes a ton of redundant information.

In particular, if, for a node $i$, there are $x$ paths from node 1 to node $i$ that use $a$ edges and $y$ paths from node $i$ to node $n$ that use $K - a$ edges, our loop takes $\mathcal{O}(xy)$ to process all of these paths, when we could instead determine the shortest path in $\mathcal{O}(x + y)$ by picking the shortest path on both sides.

This motivates a divide-and-conquer approach. If, for every node $1 \leq i \leq n$, we determine the shortest paths from $1$ to $i$ and from $i$ to $n$ which each use exactly $K / 2$ edges (we assume $K$ is even for convenience),
we can determine the shortest path from $1$ to $n$ which uses $K$ edges in $\mathcal{O}(n)$ time by enumerating the path's midpoint.

Thus, we can write the following recurrence relation for time complexity:
$$
T(K) = 2N \cdot T(K / 2)
$$
where $T(K)$ denotes the time complexity of finding the shortest path between a fixed pair of nodes $s, t$ which uses exactly $K$ edges. The factor of 2 arises from the fact that we must find the shortest path both from $s$ and to $t$, and the factor of $N$ is from enumerating all midpoints.

Solving the recurrence relation with base case $T(1) = \mathcal{O}(1)$, we get 
$T(K) = \mathcal{O}(K * N^{\log K})$, and substituting $K = 8$, this is $\mathcal{O}(N^3)$. So far, so good;
but how do we handle updates?

First, as is typical of graph problems which involve deleting edges, we consider
reversing the order of operations. Thus, we just need to figure out how to support adding edges, where each edge is added *exactly once*.

Intuitively, not too much information changes after adding a single edge;
we now just have to quantify exactly how much "not too much" is.

Note that our divide-and-conquer structure means that the answer to our problem
can change only when the answers to its left/right subproblems change. This
crucial property of DNQ is also what allows segment trees to work so quickly. You
can thus think of DNQ structures as a method to bundle information in a way such
that as much information is preserved between updates as possible.

Going back to our problem, we can write another recurrence relation for the total
number of times our states will change:
$$
C(K) = 2N\cdot C(K/2) \cdot U(K)
$$
where $U(K)$ represents the time it takes to process the change of a single substate. Luckily for us, $U(K) = 1$ for all $K$ (hopefully you understand why!), and $C(1) = 1$ as well (since each edge is added *exactly once*) so in fact, we have 
$$
C(K) = T(K) = \mathcal{O}(N^3)
$$

Therefore, our DNQ approach will still run in time, even with updates!