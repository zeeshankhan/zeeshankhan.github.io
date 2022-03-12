---
title: Big O notation
date: 2021-08-23 13:41:10 +0400
categories: []
tags: [writing, swift]
---

## Drop the constants

Ex: Runtime **$O(2N)$** will become **$O(N)$**


## Drop the non-dominant terms
Since we drop constants therefore **$O(N^2 + N^2)$** becomes **$O(N^2)$**. If we did not care about the latter $N^2$ why would we care about single $N$, we don't.

Ex: 
- **$O(N^2 + N)$** becomes **$O(N^2)$**
- **$O(N logN)$** becomes **$O(N)$**
- **$O(5 * 2^N * 100N^9)$** becomes **$O(2^N)$**

but when we have two different runtime, we do not drop any
- **$O(i + j)$** remain same because both are unknown


## Add runtimes
When an algorithm is in form of _do this and when we are all done, do that_ then we add the runtimes.

Ex:
```Swift
func foo() {
    for x in i {}
    for y in j {}
}
```
runtime would be **$O(i + j)$**


## Multiply runtimes
When an algorithms is in form of _do this for each time we do that_ then we multiply the runtimes.

Ex:
```Swift
func foo() {
    for x in i {
        for y in j {}
    }
}
```
runtime would be **$O(i * j)$**


## Known length
When array length is known that means it is a Constant and we drop the constant to calculate runtime.

```Swift
func foo() {
    for x in i {
        for y in j {
            for z in 1...1000 {}
        }
    }
}
```

Ex: **$O(i * j * 1000)$** -> **$O(i * j)$**


## Recursive runtime
When we have a recursive function and that makes multiple recursive calls then the runtime will often (but not always) look like **$O(branches^d)$** where _branches_ are the number of times each recursive call branches.

multiple recursive  calls means an Exponential Runtime

> Runtime of a recursive function with  multiple branches is 
**$O(branch ^ d)$**

or

> When we see an algorithm with multiple recursive calls, we are looking at Exponential runtime.

## log runtimes
When a number of elements in the problem space gets halved each time that will likely be a **$O(log N)$** runtime

Not all binary search tree has log runtime.


## half runtimes
When we divide the array by a constant it still runs an unknown number of times which is half of original.
After dropping the dividend which is a known constant, runtime becomes **$O(N)$**.


## Amortize time
The average time of best runtime **$O(1)$** and worst runtime **$O(N)$**


## Space
Since functions live in a stack, spaces are calculated likewise

