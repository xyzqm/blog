---
author: Daniel Zhu
pubDatetime: 2025-08-01T13:09:62-08:00
title: 'Editorial: "Adjacent Delete"'
slug: adjacent-delete
featured: true
draft: false
tags:
  - programming
  - ad-hoc
description: A simple but nice AtCoder problem.
---
Here's a [link to the problem](https://atcoder.jp/contests/arc196/tasks/arc196_a).

The first key observation to make is that instead of using absolute difference when deleting $A_i$ and $A_{i + 1}$, we can instead choose either to increment score by $A_i$ then decrement it by $A_{i + 1}$, or vice versa. In any optimal configuration we will obviously increment by the larger of the two values and decrement by the other, which reduces to absolute value, but the added flexibility provided by this rephrasing allows for easier analysis.

In fact, using this model when $n$ is even allows us to choose *any* $n / 2$ elements to increment by (while decrementing by the other $n / 2$). How?

First, notice that all valid pairings correspond to an RBS. For example, if we choose to pair $(1, 4)$ and $(2, 3)$ in the array $[1, 2, 3, 4]$, this corresponds to the RBS `(())`. Pairing $(1, 2)$, $(3, 6)$, and $(4, 5)$ in the array $[1, 2, 3, 4, 5, 6]$ would correspond to `()(())`. Now, in our rephrasing of the problem, we can choose to assign either $+/-$ or $-/+$ to a pair of brackets `()`. For instance, we can assign either `++--`, `+-+-`, `-+-+`, or `--++` to `(())`, but we couldn't assign `-++-`. From this, we see that it is necessary to have $n/2$ `+` and $n/2$ `-`. But is it *sufficient*?

Actually, yes! This will be left as an exercise to you, the reader, but the proof should be quite elegant.

This gives us a really simple solution for even $n$: simply subtract the smallest $n/2$ values from the largest $n/2$ values. Odd $n$ is just slightly more complicated, since we have to enumerate the single element remaining in the end. This will also be left as an exercise to the reader.
