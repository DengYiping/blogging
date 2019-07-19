---
title: Learn CS from scratch -- MapReduce
date: "2017-05-01T22:12:03.284Z"
description: Learn about the idea of MapReduce and how to use it to solve simple problem.
---
In functional world, there is C-style for and while loop, and everything is based on recursion. However, sometimes, recursive algorithms can leads to problems in efficiency and readability.

In may functional programming language, and in the world of Big Data processing, we use a technique called MapReduce.

In map function,  the **first** argument of map function is a functor( you can think of it as a function)  that is defined on every element of the lists, and map it to the other data type. the **second** argument takes a lists. It will apply this function to every element of the lists, and return a new lists.

Example: I want to twice the number in the lists, we can write down

```Haskell

    Prelude> let let = [1..]
    Prelude> let lst2 = map (\x-> x * 2) lst
    Prelude> take 4 lst2
    [2,4,6,8]
    Prelude> take 4 lst
    [1,2,3,4]

```
This small piece code demonstrates that how map works in Haskell.

Map function is extremely useful when you want to create a key-value pair. For example, we take the original set **lst** as the key, and the value is the elements in **lst2**, so how do we do that?

```Haskell

    Prelude> let lst3 = map (\x -> (x, x * 2)) lst
    Prelude> take 4 lst3
    [(1,2),(2,4),(3,6),(4,8)]
    Prelude> 

```
See, now it is actually a key-value pair.

Another useful technique in functional world is **reduce**: somehow combines the elements in the lists and formulate the results.

Imagine that we want to calculate the appearance of each character in a world. We first map each character into a key-value pair, with value of 1 for each character, and then we combine the one with the same key.

```Haskell

    import Data.List

    -- Normal Reduce Function
    rd :: (a -> a -> a) -> [a] -> a
    rd _ [] = error "the list to be reduced should not be empty"
    rd f (x:xs) = rdHelper x xs
        where rdHelper a [] = a
              rdHelper a (y:ys) = rdHelper (f a y) ys

    -- Sort by the key
    sortByKey :: Ord k => [(k, v)] -> [(k, v)]
    sortByKey lst = sortBy keyCmp lst
        where keyCmp x y = compare (fst x) (fst y)

    -- Reduce by Key
    rdByKey :: Eq k => (v -> v -> v) -> [(k,v)] -> [(k,v)]
    rdByKey _ [] = []
    rdByKey _ (x:[]) = [x]
    rdByKey f (x:y:xs) = if sameKey x y then rdByKey f ([(combinePair f x y)] ++ xs) else ([x] ++ (rdByKey f ([y] ++ xs)))
        where combinePair f x y = (fst x, f (snd x) (snd y)) 
              sameKey (k1, v1) (k2, v2) = k1 == k2

```
I write this short reduce by key function help you create this functionality.
To count the char, we simply do

```Haskell

    *Main> let s = "asdfasdfasqewr"
    *Main> let countchar = rdByKey (+) . sortByKey . map (\x -> (x, 1))
    *Main> countchar s
    [('a',3),('d',2),('e',1),('f',2),('q',1),('r',1),('s',3),('w',1)]

```
Only three lines of code.
