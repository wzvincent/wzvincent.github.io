---
title: divided-conquer
date: 2018-07-16 12:27:14
categories: 
    - algorithm
tags: 
    - leetcode
    - divided and conquer
---

### *241. Different Ways to Add Parentheses*

Given a string of numbers and operators, return all possible results from computing all the different possible ways to group numbers and operators. The valid operators are +, - and *.

**Example:**
```
Input: "2*3-4*5"
Output: [-34, -14, -10, -10, 10]
Explanation: 
(2*(3-(4*5))) = -34 
((2*3)-(4*5)) = -14 
((2*(3-4))*5) = -10 
(2*((3-4)*5)) = -10 
(((2*3)-4)*5) = 10
```

#### Solution:
```
public List<Integer> diffWaysToCompute(String input) {
        List<Integer> res = new ArrayList<Integer>();
        for (int i = 0; i < input.length(); i++) {
            char c = input.charAt(i);
            if (c == '+' || c == '-' || c == '*') {
                String part1 = input.substring(0, i);
                String part2 = input.substring(i+1);
                List<Integer> part1Ret = diffWaysToCompute(part1);
                List<Integer> part2Ret = diffWaysToCompute(part2);
                for (int p1 : part1Ret) {
                    for (int p2 : part2Ret) {
                        if (c == '-') {
                            res.add(p1 - p2);
                        } else if (c == '+') {
                            res.add(p1 + p2);
                        } else if (c == '*') {
                            res.add(p1 * p2);
                        }
                    }
                }
            }
        }
        if (res.size() == 0) res.add(Integer.valueOf(input));
        return res;
    }
```
&nbsp;
&nbsp;
*To be continued...*