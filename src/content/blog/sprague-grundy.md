---
author: Daniel Zhu
pubDatetime: 2025-07-11T09:05:44-08:00
title: The Sprague-Grundy theorem
slug: sprague-grundy
featured: true
draft: false
tags:
  - programming
  - math
  - games
description: Motivating the Sprague-Grundy theorem from first principles.
---
First, a definition of an impartial game:

> [!note] Definition
> Any impartial game can be thought of as DAG (directed acyclic graph) where nodes correspond to game states and edges correspond to possible moves. A player loses such a game when they have no moves left to play.

Theoretically, to solve any impartial game, we need only to determine one bit of information about every possible game state: whether the next player (about to make a move) wins, or loses. If the next player wins, the state is called an *N-position*; otherwise, if the previous player wins, the state is a *P-position*. Together, N-positions and P-positions comprise the two possible **outcome classes** of any impartial game.

However, let's say we want equivalence classes that are a bit more fine-grained. Why? Well, consider the operation of combining two games $G$ and $H$ in parallel, which means that these two games will be played side-by-side: on each player's turn, they can choose play on *exactly one* of the games, and leave the other one unchanged (a classic example of this would be a two-pile nim game). 

From now on, we will denote this combination of games as $G + H$. 
>[!Note] 
> Note that the $+$ operation as defined here is both commutative and associative. It also defines a group because the sum of two games is still a game.

Importantly, in order to determine the outcome class of $G + H$, it's not sufficient to know only the outcome classes of $G$ and $H$ individually. Specifically, if both $G$ and $H$ are N-positions, $G + H$ could still be either an N- or a P-position.

This motivates a new definition of equivalence: 
>[!info] Definition
>$G \equiv G'$ iff for all games $H$, $G + H$ and $G' + H$ belong to the same outcome class.

And the equivalence classes of games under this definition are precisely what the Sprague-Grundy theorem helps us find!

>[!note]
> While the Sprague-Grundy theorem is often thought of as a very general result, it's only really useful when games are being combined in parallel. Otherwise, the Grundy number has no real meaning.

---
Let's consider the identity equivalence class, $0$. All games $G$ of this class, when added to any other game, will have no effect on their equivalence class; that is, $A \equiv A + G$ for all games $A$. A trivial example of such a game is the empty game $\{\}$, but there are actually more. In fact:

>[!info] Lemma 1
> $0$ is exactly the set of all P-positions. 

**Proof:**
Let's pick some P-position $G$. We wish to prove that for all games $A$, $A \equiv A + G$. Note that this is actually equivalent to proving the simpler statement that for all games $A$, $A$ and $A + G$ belong to the same outcome class.

If $A$ is an N-position, the first player can win $A + G$ like so:
- Always play in $A$ according to their original strategy for $A$.
- If the second player plays in $G$, the first player will always have a response since $G$ is a P-position.

A similar argument holds for the case when $A$ is a P-position.

On the contrary, no N-position can be in $0$: a simple counter-case is $A = \{\}$.

Another useful lemma:
>[!info] Lemma 2
> For all impartial games $G$, $G + G = 0$.

**Proof:**
Player 2 can always simply mirror Player 1's move. Thus, $G + G$ is a P-position and therefore is in $0$.

...Wait a minute. $G + G = 0$? This $+$ operation is starting to look suspiciously like XOR! In fact, Lemma 2 is *precisely the reason why XOR appears in the Sprague-Grundy theorem:* **not** because of the nim-sum, but because of this much more fundamental "mirror" property.

By the way, this property also gives us an easier way to check equivalence:
>[!info] Lemma 3
> $G \equiv G'$ iff $G + G' = 0$.

**Proof:**
For all $A$, $A + G \equiv A + G + (G + G') \equiv A + (G + G) + G' \equiv A + G'$. 

---
Now, let's define a *basis* of games $X_i$ such that every game $G$ is equivalent to the sum of *exactly one* subset of $X$. Essentially, the games in $X$ must all be independent, as well as span all possible games under the $+$ operation. 

Then, using this basis, every equivalence class $G$ has a unique binary representation $f_X(G)$, where the $i$th bit of $f_X(G)$ denotes whether $X_i$ is a member of the equivalent subset or not. Importantly, combining two games in parallel then corresponds to simply XOR-ing these binary representations!

So, are these binary representations the Grundy numbers, then?

Well, not quite. See, we don't just have one choice of basis. Consider the game of Nim, for instance. The classic basis is $\{*1, *2, *4, *8,...\}$ where $*x$ denotes a single pile of size $x$. In this basis, we would represent the game $*7$ as $111_2$. However, we could also use something like $\{*1, *3, *6, *8, *16,...\}$, in which case $*7$ would be represented as $101_2$ instead. Yet, no matter what basis $X$ we choose, remember that $f_X(G + H)$ **always** equals $f_X(G) \oplus f_X(H)$ due to Lemma 2.

What basis should we use then? And how do we even ensure our basis elements are independent?

Enter: mex! Just like XOR, the use of the mex function in the Sprague-Grundy theorem is typically justified by converting all games into Nim. However, there again exists a more fundamental reason for its use: it encapsulates a clever *greedy algorithm* that helps us quickly find a valid basis.  

Whaa? OK, let me describe this greedy algorithm to you first, without using mex at all.
1. Topologically sort all game states (guaranteed to be possible since game is a DAG).
2. Initialize our basis $X$ as an empty *list*, $[]$ (because order matters!).
3. In reverse topological order, check if $G$ is independent of $X$. If yes, append $G$ to the end of $X$.

And that's it!

Wait, what? That's it? How is this even remotely related to mex? The key idea is to show that because of this greedy approach, the $i$th basis element $X_i$ has the property that it can transition to an equivalent state of **every** subset of the first $i$ basis elements. That is, for every $z < 2^i$, $X_i$ can transition to a state $Z$ such that $f_X(Z) = z$.

In fact, together with the properties of XOR, this claim generalizes to the following:
>[!info] Lemma 4
> For every state $Y$ and every $z < f_X(Y)$, $Y$ can transition to some state $Z$ such that $f_X(Z) = z$.

Do you see where mex could come in now?

**Proof:**
We induct. Let's say this claim is true for every state we've encountered so far in our reverse topological order: the base case is rather simple. Now, let our current state be $G$, and the set of possible next states as $T_i$. Let $m$ be the mex of $f_X(T_i)$ over all $T_i$. We wish to show that $f_X(G) = m$.

To do this, consider some game $M$ in equivalence class $m$ and spanned by our current basis $X$. If no such game exists, $G$ is independent of $X$ and thus will be added as a new element to the basis. Note that this also means we can transition to every game currently spanned by $X$, so our inductive hypothesis still holds.

Otherwise, we then need to prove is that $G \equiv M$, or that $G + M = 0$. We can show this by splitting into two cases:
1. The first player moves either $G$ or $M$ to a state $Z$ with $f_X(Z) < m$. The second player can then match this value by moving on the other game. Both states will have value $f_X(Z)$ and will thus form a P-position.
2. The first player moves either $G$ or $M$ to a state $Z$ with $f_X(Z) > m$. The second player can then move this state back to one with value $m$. Both games will now have value $m$ and will thus form a P-position.

Since the second player can always force the first player to begin their next turn on a P-position, it follows that $G + M$ must be a P-position itself. Therefore, $G \equiv M$, as desired.

---
OK, that was a lot. But the primary takeaway is that the **entire reason** mex is used is because of the (surprisingly elegant) *greedy strategy* we used in order to find a valid basis. In fact, (don't quote me on this) this may be one of the *only* efficient strategies to compute a valid basis. In my description of the greedy algorithm earlier, Step 3 required checking whether the current game $G$ was indeed independent of the basis elements $X$ found thus far. If $X$ was just some arbitrary basis, this could have taken forever to check, and maybe even have been impossible. Thankfully, due to the nice structure our greedy algorithm imposes on $X$, we instead end up with a very quick and elegant way of evaluating independence.

The best part? We derived all these beautiful results without referencing the game of Nim at all! The XOR relation arose naturally from the "mirror" nature of combining identical impartial games, and *mex* showed up as a direct result of the simple greedy strategy we defined to find a valid basis. This, I believe, is truly the best way to understand the *fundamental nature* of the Sprague-Grundy theorem.


