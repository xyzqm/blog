---
author: Daniel Zhu
pubDatetime: 2025-01-21T08:56:92-08:00
title: "Haskell: #1"
slug: haskell-1
featured: true
draft: false
tags:
  - programming
description: A quick intro to the world of Haskell and functional programming.
---
Recently, I stumbled across the idea of functional programming (FP) and its flagship language, [Haskell](https://www.haskell.org/).
I was instantly intrigued by the language's unique syntax and simplicity.
For instance, below are two implementations of a [leftist heap](https://en.wikipedia.org/wiki/Leftist_tree) in Java and Haskell, respectively:

```java
public Node merge(Node x, Node y) {
    if (x == null)
        return y;
    if (y == null)
        return x;

    // if this were a max-heap, then the
    // next line would be: if (x.element < y.element)
    if (x.element.compareTo(y.element) > 0)
        // x.element > y.element
        return merge (y, x);

    x.rightChild = merge(x.rightChild, y);

    if (x.leftChild == null) {
        // left child doesn't exist, so move right child to the left side
        x.leftChild = x.rightChild;
        x.rightChild = null;
        // x.s was, and remains, 1
    } else {
        // left child does exist, so compare s-values
        if (x.leftChild.s < x.rightChild.s) {
            Node temp = x.leftChild;
            x.leftChild = x.rightChild;
            x.rightChild = temp;
        }
        // since we know the right child has the lower s-value, we can just
        // add one to its s-value
        x.s = x.rightChild.s + 1;
    }
    return x;
}
```

_Ugly, as expected of Java._

compared to...

```haskell
data LHeap a
  = Leaf
  | Node a (LHeap a) (LHeap a)

rank :: LHeap a -> Integer
rank Leaf = 0
rank (Node _ _ r) = rank r + 1

merge :: Ord a => LHeap a -> LHeap a -> LHeap a
merge Leaf h = h
merge h Leaf = h
merge h@(Node a l r) h'@(Node a' _ _)
  | a > a'           = merge h' h
  | rank r' > rank l = Node a r' l
  | otherwise        = Node a l r'
  where r' = merge r h'
```

_Clean, concise, and elegant._

With that said, here's what I've learned so far about Haskell and functional programming!

## What is Functional Programming?
Obviously, FP revolves around **functions**. An important property of functions in FP is that they are _first-class citizens_. This means that functions can be passed as arguments to other functions, returned as values from other functions, and assigned to variables. Functions also satisfy the _referential transparency_ property, which means that a function always returns the same output for the same input.

Here's a concrete example: imagine a `Counter` class in a standard OOP language with a method `increment()` that increments a counter by 1 and returns the new value. If you call `increment()` twice, the first call will return 1, while the second will return 2. However, in FP, you would define a function `increment` that takes a counter as an argument and returns a new counter with the value incremented by 1. Therefore, `increment counter` will always return the same value, regardless of how many times you call it.

```haskell
let counter = 0
let counter1 = increment counter --will have value 1
let counter2 = increment counter --will have value 1
let counter3 = increment counter1 --will have value 2
```

A consequence of this is that all objects in FP are immutable, meaning that once an object is created, it cannot be modified. Instead, a new object is created with the desired changes. This means that code like this:

```cpp
int a[10];
for (int i = 0; i < 5; i++) ++a[i];
// do stuff with a
for (int i = 5; i < 10; i++) ++a[i];
// do stuff with a
```

Has no simple FP equivalent. Instead, you would have to create a new array with the desired changes:

```haskell
let a = replicate 10 0
let a' = incrementFirstFive a
-- do stuff with a'
let a'' = incrementLastFive a'
-- do stuff with a''
```

Which is obviously less efficient, as you have to copy the array twice. (Haskell does provide ways to get around this, which may be covered in a future post.)

A benefit of this immutability, however, is that all objects are **fully persistent**, meaning that all versions of an object are accessible at all times. For instance, in the C++ code above, only the final version of `a` is accessible at the end of the code. In Haskell, all versions of `a` (`a`, `a'`, `a''`) are accessible at all times. This means that implementing a data structure efficiently in FP reaps the added benefit of having a fully persistent data structure. Functional data structures are an active area of research, and many data structures have already been developed that are both efficient and fully persistent. Some classic data structures that have purely functional equivalents are stacks, sets/multisets, and priority queues (you can read more about such data structures [here](https://en.wikipedia.org/wiki/Purely_functional_data_structure)).

## Solving Some Problems

Let's get our hands dirty by actually writing some code! I decided to turn to [CSES](https://cses.fi/), a collection of competitive programming problems, to practice my Haskell skills (or lack thereof).

### [Weird Algorithm](https://cses.fi/problemset/task/1068)

This problem just asks you to simulate the Collatz conjecture. Here's the Haskell code:

```haskell
collatz :: Int -> [Int] -- function signature: takes an Int and returns a list of Ints
-- Ex: collatz 3 = [3, 10, 5, 16, 8, 4, 2, 1]
collatz 1 = [1] -- base case: if n = 1, return [1]
-- (:) denotes prepending a single value; 1:[2, 3] = [1, 2, 3]
collatz n = n:collatz (if even n then n `div` 2 else 3 * n + 1)

main :: IO()
main = do
  n <- readLn :: IO Int -- equivalent to int n; cin >> n;
  -- (.) denotes function composition: f . g x = f(g(x))
  -- ($) is used to avoid parentheses: f (g x) = f $ g x
  -- map applies a function to each element of a list (ex: map f [1, 2, 3] = [f 1, f 2, f 3])
  -- unwords concatenates a list of strings with spaces in between
  putStrLn . unwords . map show $ collatz n
```

### [Gray Code](https://cses.fi/problemset/task/2205)

This problem requires a little more thought, but is still relatively easy.
Here's the solution: if we have the Gray Code for $n - 1$, simply append 0 to the front of each number, and append 1 to the front of each number in reverse order. This will give us the Gray Code for $n$.

```haskell
code :: Int -> [String]
code 0 = [""] -- base case
code n =
  let a = code (n - 1) -- defining a variable!
  -- (<$>) is an infix version of fmap: f <$> x = fmap f x
  -- ('0':) is a function that takes a string and prepends '0' to it
  -- Note that it's a partial form of something like '0':"123", which would evaluate to "0123"
  -- Similarly, (2+) <$> [1, 2, 3] = [3, 4, 5]
  -- '++' simply concatenates two lists
  in (('0':) <$> a) ++ (('1':) <$> reverse a)

main :: IO()
main = do
  n <- readLn :: IO Int
  -- unlines separates an array w/ newlines instead of spaces
  putStrLn . unlines $ code n
```