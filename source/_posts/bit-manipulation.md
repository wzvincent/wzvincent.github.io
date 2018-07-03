---
title: Bit Manipulation
date: 2018-06-11 17:20:11
categories: 
    - algorithm
tags: 
    - leetcode
    - bit manipulation
---
# ^(exlusive or) tricks
`save the distinct bits and remove the same`

### *371. Sum of Two Integers*
`Calculate the sum of two integers a and b, but you are not allowed to use the operator + and -.`

For this, problem, for example, we have a = 1, b = 3,
<!-- more -->

In bit representation, a = 0001, b = 0011,

First, we can use "and"("&") operation between a and b to find a carry.

carry = a & b, then carry = 0001

Second, we can use "xor" ("^") operation between a and b to find the different bit, and assign it to a,

Then, we shift carry one position left and assign it to b, b = 0010.

Iterate until there is no carry (or b == 0)

```
    var getSum = function(a, b) {
        if (b === 0) return a;
        var sum = a ^ b;
        var carry = (a & b) << 1;
        return getSum(sum, carry);
    };
```
&nbsp;
### *338. Counting Bits*
Given a non negative integer number num. For every numbers i in the range 0 ≤ i ≤ num calculate the number of 1's in their binary representation and return them as an array.

**Example:**
For num = 5 you should return [0,1,1,2,1,2].
#### Solution:
We can determine the parity(odd number or even number) of a number by &1.
```
public int[] countBits(int num) {
    int[] f = new int[num + 1];
    for (int i=1; i<=num; i++) f[i] = f[i >> 1] + (i & 1);
    return f;
}
```
&nbsp;
&nbsp;
&nbsp;
*To be continued...*
