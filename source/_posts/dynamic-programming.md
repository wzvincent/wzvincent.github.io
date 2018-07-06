---
title: Dynamic Programming
date: 2018-06-15 15:52:12
categories: 
    - algorithm
tags: 
    - leetcode
    - dynamic programming
---
># <font color=red>Longest XXX Substring / Subarray / Subsequence</font> 

### *5. Longest Palindromic Substring*
Given a string s, find the longest palindromic substring in s. You may assume that the maximum length of s is 1000.

**Example 1:**
```
Input: "babad"
Output: "bab"
Note: "aba" is also a valid answer.
```
<!-- more -->
**Example 2:**
```
Input: "cbbd"
Output: "bb"
```
#### Solution:
> D[i][j] represents whether s(i ... j) can form a palindromic substring, D[i][j] is true when s(i) equals to s(j) and s(i+1 ... j-1) is a palindromic substring. When we found a palindrome, check if it's the longest one. Time complexity O(n^2).
```
public static String longestPalindrome(String s) {
        if (s == null) {
            return null;
        }
        int len = s.length();
        boolean[][] D = new boolean[len][len];
        int max = 0;
        int retB = 0;
        int retE = 0;
        for (int j = 0; j < len; j++) {
            for (int i = 0; i <= j; i++) {
                if (s.charAt(i) == s.charAt(j) && (j - i <= 2 || D[i + 1][j - 1])){
                    D[i][j] = true;
                    if (j - i + 1 > max) {
                        retB = i;
                        retE = j;
                        max = j - i + 1;
                    }
                } 
                else {
                    D[i][j] = false;
                }            
            }
        }
        return s.substring(retB, retE + 1);
    }
```
&nbsp;
### *53. Maximum Subarray*
Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

**Example:**
```
Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
```
#### Solution:
```
class Solution {
    public int maxSubArray(int[] nums) {
        int local = nums[0];
        int global = nums[0];
        for (int i = 1; i<nums.length; i++){
            local = Math.max(nums[i],local+nums[i]);
            global = Math.max(local,global);
        }
        return global;
    }
}

public int maxSubArray(int[] A) {
    int n = A.length;
    int[] dp = new int[n];//dp[i] means the maximum subarray ending with A[i];
    dp[0] = A[0];
    int max = dp[0];        
    for(int i = 1; i < n; i++){
        dp[i] = A[i] + (dp[i - 1] > 0 ? dp[i - 1] : 0);
        max = Math.max(max, dp[i]);
    }      
    return max;
}
```

&nbsp;
### *152. Maximum Product Subarray*
Given an integer array nums, find the contiguous subarray within an array (containing at least one number) which has the largest product.

**Example:**
```
Input: [2,3,-2,4]
Output: 6
Explanation: [2,3] has the largest product 6.
```
#### Solution:
Due to a negative num * negative num could be a positive num, so we need to record the local min num as minhere.
```
public int maxProduct(int[] A) {
    if (A.length == 0) {
        return 0;
    }

    int maxherepre = A[0];
    int minherepre = A[0];
    int maxsofar = A[0];
    int maxhere, minhere;

    for (int i = 1; i < A.length; i++) {
        maxhere = Math.max(Math.max(maxherepre * A[i], minherepre * A[i]), A[i]);
        minhere = Math.min(Math.min(maxherepre * A[i], minherepre * A[i]), A[i]);
        maxsofar = Math.max(maxhere, maxsofar);
        maxherepre = maxhere;
        minherepre = minhere;
    }
    return maxsofar;
}
```
&nbsp;
### *300. Longest Increasing Subsequence*
Given an unsorted array of integers, find the length of longest increasing subsequence.

**Example:**
```
Input: [10,9,2,5,3,7,101,18]
Output: 4 
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4. 
```
Note:

There may be more than one LIS combination, it is only necessary for you to return the length.
Your algorithm should run in O(n2) complexity.
#### Solution:
let dp[i] be the max length of subsequence ended by nums[i]
```
public int lengthOfLIS(int[] nums) {
    if(nums == null || nums.length == 0) {
        return 0;
    }
    int len = nums.length, max = 0;
    int[] dp = new int[len];
    
    for(int i = 0; i < len; i++) {
        dp[i] = 1;
        for(int j = 0; j < i; j++) {
            if(nums[i] > nums[j]) {
                dp[i] = Math.max(dp[j]+1, dp[i]);
            }
        }
        max = Math.max(max, dp[i]);
    }
    
    return max;
}
```
use binary search to optimize:
```
public int lengthOfLIS(int[] nums) {  
    int[] increasing = new int[nums.length];  
    int size = 0;  
    for(int i=0; i<nums.length; i++) {  
        int left=0, right=size-1;  
        while (left<=right) {  
            int m=(left+right)/2;  
            if (nums[i] > increasing[m]) left = m + 1;  
            else right = m - 1;  
        }  
        increasing[left] = nums[i];  
        if (left==size) size ++;  
    }  
    return size;  
} 
```

&nbsp;
### *368. Largest Divisible Subset*
Given a set of distinct positive integers, find the largest subset such that every pair (Si, Sj) of elements in this subset satisfies: Si % Sj = 0 or Sj % Si = 0.

If there are multiple solutions, return any subset is fine.
**Example:**

```
nums: [1,2,3]

Result: [1,2] (of course, [1,3] will also be ok)
```

#### Solution:
1. Sort the array
2. For each element in nums, find the length of largest subset it has.
3. Record every item's prev in the subset.
4. Return the subset due to the prev chain.
```
public List<Integer> largestDivisibleSubset(int[] nums) {
    int[] dp = new int[nums.length];
    int[] pre = new int[nums.length];
    Arrays.sort(nums);
    int max = 0, index = -1;
    for (int i = 0; i < nums.length; i++) {
        dp[i] = 1;
        pre[i] = -1;
        for (int j = i-1; j >= 0; j--) {
            if (nums[i] % nums[j] == 0) {
                if (dp[j]+1 > dp[i]) {
                    dp[i] = dp[j] + 1;
                    pre[i] = j;
                }
            }
            
        }
        if (dp[i] > max) {
            max = dp[i];
            index = i;
        }
    }
    List<Integer> res = new ArrayList<Integer>();
    while (index != -1) {
        res.add(nums[index]);
        index = pre[index];
    }
    return res;
}
```

&nbsp;
&nbsp;
&nbsp;
># <font color=red>One Dimension DP</font>
### *70. Climbing Stairs*
You are climbing a stair case. It takes n steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

Note: Given n will be a positive integer.

**Example:**
```
Input: 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```
#### Solution:
>The total number of ways to reach ith step is equal to sum of ways of reaching (i - 1)th step and ways of reaching (i - 2)th step.
*State Transition Equation:&nbsp; dp[i] = dp[i-1] + dp[i-2]*
```
public int climbStairs(int n) {
    if(n==1)
        return 1;
    int[]dp = new int[n+1];
    dp[1] = 1;
    dp[2] = 2;
    for(int i = 3; i<=n; i++){
        dp[i] = dp[i-1]+dp[i-2];
    }
    return dp[n];
}
```
&nbsp;
### *91. Decode Ways*
A message containing letters from A-Z is being encoded to numbers using the following mapping:
```
'A' -> 1
'B' -> 2
...
'Z' -> 26
```
Given a non-empty string containing only digits, determine the total number of ways to decode it.

**Example:**
```
Input: "226"
Output: 3
Explanation: It could be decoded as "BZ" (2 26), "VF" (22 6), or "BBF" (2 2 6).
```
#### Solution:
> dp[0] means an empty string will have one way to decode, dp[1] means the way to decode a string of size 1. Then check one digit and two digit combination and save the results along the way. In the end, dp[n] will be the end result.
```
public int numDecodings(String s) {
    int n = s.length();
    if(n == 0) return 0;
    
    int[] m = new int[n+1];
    m[n] = 1;
    m[n-1] = s.charAt(n-1) != '0' ? 1 : 0;
    
    for(int i = n-2; i>=0; i--){
        if(s.charAt(i)=='0') continue;
        else m[i] = (Integer.parseInt(s.substring(i,i+2))<=26) ? m[i+1] + m[i+2] : m[i+1];
    }
    return m[0];
}
```
From back to front:
```
public int numDecodings(String s) {
    int n = s.length();
    if(n == 0) return 0;
    
    int[] m = new int[n+1];
    m[n] = 1;
    m[n-1] = s.charAt(n-1) != '0' ? 1 : 0;
    
    for(int i = n-2; i>=0; i--){
        if(s.charAt(i)=='0') continue;
        else m[i] = (Integer.parseInt(s.substring(i,i+2))<=26) ? m[i+1] + m[i+2] : m[i+1];
    }
    return m[0];
}
```
&nbsp;
### *96. Unique Binary Search Trees*
Given n, how many structurally unique BST's (binary search trees) that store values 1 ... n?

**Example:**
```
Input: 3
Output: 5
Explanation:
Given n = 3, there are a total of 5 unique BST's:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```
#### Solution:
> G(n): the number of unique BST for a sequence of length n.
F(i, n), 1 <= i <= n: the number of unique BST, where the number i is the root of BST, and the sequence ranges from 1 to n.
*G(n) = F(1, n) + F(2, n) + ... + F(n, n)*
The tricky part is that we could consider the number of unique BST out of sequence [1,2] as G(2), and the number of of unique BST out of sequence [4, 5, 6, 7] as G(4). Therefore, F(3,7) = G(2) * G(4).
*F(i, n) = G(i-1) * G(n-i) &nbsp;&nbsp; 1 <= i <= n*
Therefore:
G(n) = G(0) * G(n-1) + G(1) * G(n-2) + … + G(n-1) * G(0)
```
public int numTrees(int n) {
    int[]G = new int[n+1];
    G[0]=1;
    G[1]=1;
    for(int i=2; i<=n; i++){
        for(int j = 1; j<=i; j++){
            G[i] += G[j-1]*G[i-j];
        }
    }
    return G[n];
}
```
&nbsp;
### *95. Unique Binary Search Trees II*
Given an integer n, generate all structurally unique BST's (binary search trees) that store values 1 ... n.

**Example:**
```
Input: 3
Output:
[
  [1,null,3,2],
  [3,2,null,1],
  [3,1,null,null,2],
  [2,1,3],
  [1,null,2,null,3]
]
Explanation:
The above output corresponds to the 5 unique BST's shown below:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```
#### Solution:
```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<TreeNode> generateTrees(int n) {
        if(n < 1)
            return new ArrayList<TreeNode>();
        return helper(1, n);
    }
    
    public List<TreeNode> helper(int start, int end){
        List <TreeNode>res = new ArrayList();
        if(start>end){
            res.add(null);
            return res;
        }
        for(int i=start; i<=end; i++){
            List<TreeNode>leftChild = helper(start,i-1);
            List<TreeNode>rightChild = helper(i+1,end);
            for(TreeNode left: leftChild){
                for(TreeNode right: rightChild){
                    TreeNode root = new TreeNode(i);
                    root.left = left;
                    root.right = right;
                    res.add(root);
                }
            }
        }
        return res;
    }
}
```
&nbsp;
### 121. Best Time to Buy and Sell Stock
Say you have an array for which the ith element is the price of a given stock on day i.

If you were only permitted to complete at most one transaction (i.e., buy one and sell one share of the stock), design an algorithm to find the maximum profit.

Note that you cannot sell a stock before you buy one.

**Example:**
```
Input: [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
             Not 7-1 = 6, as selling price needs to be larger than buying price.
```
#### Solution:
Easy to Understand Solution:
```
public int maxProfit(int[] prices) {
    if(prices == null || prices.length <2)
        return 0;
    int profit=0, minPriceSoFar=prices[0];
    for(int i=1; i<prices.length;i++){
        profit=Math.max(prices[i]-minPriceSoFar, profit);
        minPriceSoFar = Math.min(prices[i], minPriceSoFar);
    }
    
    return profit;
}
```
Think it as accumulated maximum number:
```
public int maxProfit(int[] prices) {
    int curProfit = 0;
    int gloProfit = 0;
    for (int i = 1; i < prices.length; i++){
        curProfit = Math.max((prices[i]-prices[i-1]+curProfit),0);
        gloProfit = Math.max(curProfit,gloProfit);
    }
    return gloProfit;
}
```
&nbsp;
### 309. Best Time to Buy and Sell Stock with Cooldown
Say you have an array for which the ith element is the price of a given stock on day i.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times) with the following restrictions:
*You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).*
*After you sell your stock, you cannot buy stock on next day. (ie, cooldown 1 day)*

**Example:**
```
Input: [1,2,3,0,2]
Output: 3 
Explanation: transactions = [buy, sell, cooldown, buy, sell]
```
#### Solution:
buy[i] means before day i what is the maxProfit for any sequence end with buy.

sell[i] means before day i what is the maxProfit for any sequence end with sell.

rest[i] means before day i what is the maxProfit for any sequence end with rest.
```
buy[i]  = max(rest[i-1]-price, buy[i-1]) 
sell[i] = max(buy[i-1]+price, sell[i-1])
rest[i] = max(sell[i-1], buy[i-1], rest[i-1])
```
Well, the answer lies within the fact that buy[i] <= rest[i] which means rest[i] = max(sell[i-1], rest[i-1]). That made sure [buy, rest, buy] is never occurred.

A further observation is that and rest[i] <= sell[i] is also true therefore

```
rest[i] = sell[i-1]
```

```
public int maxProfit(int[] prices) {
    int sell = 0, prev_sell = 0, buy = Integer.MIN_VALUE, prev_buy;
    for (int price : prices) {
        prev_buy = buy;
        buy = Math.max(prev_sell - price, prev_buy);
        prev_sell = sell;
        sell = Math.max(prev_buy + price, prev_sell);
    }
    return sell;
}
```

&nbsp;

### 139. Word Break

Given a non-empty string s and a dictionary wordDict containing a list of non-empty words, determine if s can be segmented into a space-separated sequence of one or more dictionary words.
**Note:**
The same word in the dictionary may be reused multiple times in the segmentation.
You may assume the dictionary does not contain duplicate words.

**Example:**

```
Input: s = "leetcode", wordDict = ["leet", "code"]
Output: true
Explanation: Return true because "leetcode" can be segmented as "leet code".
```

#### Solution:

dp[i] stands for the string from s[0] to s[i] could be represented by words in dist.
```
public boolean wordBreak(String s, List<String> wordDict) {
    boolean []dp = new boolean[s.length()+1];
    dp[0] = true;
    Set<String> set = new HashSet<>(wordDict);
    for(int i = 1; i <= s.length(); i++){
        for(int j = 0; j < i; j++){
            if(dp[j] && set.contains(s.substring(j,i))){
                dp[i] = true;
                break;
            }
        }
    }
    return dp[s.length()];
}
```

&nbsp;

### 198. House Robber

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security system connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police.

**Example:**
```
Input: [2,7,9,3,1]
Output: 12
Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
             Total amount you can rob = 2 + 9 + 1 = 12.
```

#### Solution:

For every house, we could make 2 decisions, rob or not.

```
public int rob(int[] nums) {
    int prevNo = 0; 	// No: means we don't rob the current house
    int prevYes = 0; 	// Yes: means we rob the current house
    for (int n : nums) {
        int temp = prevNo;
        prevNo = Math.max(prevNo, prevYes);  // not rob current house, current max will depends on previous value: Math.max(prevNo, prevYes)
        prevYes = n + temp;	// rob current one, add current stashed money value
    }
    return Math.max(prevNo, prevYes);
}
```

&nbsp;

### 213. House Robber II

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed. All houses at this place are arranged in a circle. That means the first house is the neighbor of the last one. Meanwhile, adjacent houses have security system connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police.

**Example:**

```
Input: [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
             Total amount you can rob = 1 + 3 = 4.
```

#### Solution:

Result = max (robFirstHouse, notrob);
```
    public int rob(int[] nums) {
        if (nums.length == 1) return nums[0];
        return Math.max(rob(nums, 0, nums.length-2), rob(nums, 1, nums.length-1));
    }
    
    private int rob(int[] nums, int lo, int hi) {
        int prevNo = 0;
        int prevYes = 0;
        for (int i = lo; i <= hi; i++) {
            int temp = prevNo;
            prevNo = Math.max(prevNo, prevYes);
            prevYes = nums[i] + temp;
        }
        return Math.max(prevNo, prevYes);
    }
```

&nbsp;
&nbsp;

### 264. Ugly Number II

Write a program to find the n-th ugly number.
Ugly numbers are positive numbers whose prime factors only include 2, 3, 5.
**Example:**

```
Input: n = 10
Output: 12
Explanation: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 is the sequence of the first 10 ugly numbers.
``` 

#### Solution:

because every number can only be divided by 2, 3, 5, one way to look at the sequence is to split the sequence to three groups as below:

```
(1) 1×2, 2×2, 3×2, 4×2, 5×2, …
(2) 1×3, 2×3, 3×3, 4×3, 5×3, …
(3) 1×5, 2×5, 3×5, 4×5, 5×5, …
```

```
public int nthUglyNumber(int n) {
    int index2 = 0, index3 = 0, index5 = 0;
    int factor2 = 2, factor3 = 3, factor5 = 5;
    int []ugly = new int[n];
    ugly[0] = 1;
    int min = 1;
    for ( int i = 1; i < n; i++ ) {
        min = Math.min(Math.min(factor2, factor3), factor5);
        ugly[i] = min;
        if (min == factor2) factor2 = 2 * ugly[++index2];
        if (min == factor3) factor3 = 3 * ugly[++index3];
        if (min == factor5) factor5 = 5 * ugly[++index5];
    }
    return ugly[n-1];
}
```

&nbsp;

### 279. Perfect Squares

Given a positive integer n, find the least number of perfect square numbers (for example, 1, 4, 9, 16, ...) which sum to n.

**Example:**

```
Input: n = 13
Output: 2
Explanation: 13 = 4 + 9.
```

#### Solution:

dp[n] indicates that the perfect squares count of the given n, and we have:
```
dp[0] = 0 
dp[1] = dp[0]+1 = 1
dp[2] = dp[1]+1 = 2
dp[3] = dp[2]+1 = 3
dp[4] = Min{ dp[4-1*1]+1, dp[4-2*2]+1 } 
      = Min{ dp[3]+1, dp[0]+1 } 
      = 1				
dp[5] = Min{ dp[5-1*1]+1, dp[5-2*2]+1 } 
      = Min{ dp[4]+1, dp[1]+1 } 
      = 2
						.
						.
						.
dp[13] = Min{ dp[13-1*1]+1, dp[13-2*2]+1, dp[13-3*3]+1 } 
       = Min{ dp[12]+1, dp[9]+1, dp[4]+1 } 
       = 2
						.
						.
						.1
dp[n] = Min{ dp[n - i*i] + 1 },  n - i*i >=0 && i >= 1
```

&nbsp;

```
public int numSquares(int n) {
    int[] dp = new int[n + 1];
    Arrays.fill(dp, Integer.MAX_VALUE);
    dp[0] = 0;
    for(int i = 1; i <= n; ++i) {
        int min = Integer.MAX_VALUE;
        int j = 1;
        while(i - j*j >= 0) {
            min = Math.min(min, dp[i - j*j] + 1);
            ++j;
        }
        dp[i] = min;
    }		
    return dp[n];
}


public int numSquares(int n) {
    int[] dp = new int[n+1];
    Arrays.fill(dp, Integer.MAX_VALUE);
    dp[0] = 0;
    for(int i = 1; i <= n; i++){
        for(int j = 1; j*j <= i; j++){
            dp[i] = Math.min(dp[i-j*j] + 1, dp[i]);
        }
    }
    return dp[n];
}
```

&nbsp;

### 343. Integer Break

Given a positive integer n, break it into the sum of at least two positive integers and maximize the product of those integers. Return the maximum product you can get.

For example, given n = 2, return 1 (2 = 1 + 1); given n = 10, return 36 (10 = 3 + 3 + 4).

Note: You may assume that n is not less than 2 and not larger than 58.

#### Solution:

dp[i] stores the max product from i. Suppose we *break the number i* to j (j < i) and i - j, we want to compare dp[i] and j * (i - j) in each iteration. However, j or i - j maybe break into multiple smaller numbers to get greater product itself.

```
public int integerBreak(int n) {
    int[] dp = new int[n+1];
    dp[1] = 1;
    for (int i = 2; i <= n; i++) {
        for (int j = 1; j < i; j++) {
            dp[i] = Math.max( dp[i], Math.max(j, dp[j]) * Math.max(i-j, dp[i-j]) );
        }
    }
    return dp[n];
}
```
Math trick method:

```
public int integerBreak(int n) {
    if(n==2) return 1;
    if(n==3) return 2;
    int product = 1;
    while(n>4){
        product*=3;
        n-=3;
    }
    product*=n;
    
    return product;
}
```

&nbsp;
&nbsp;
> ### Bottom-Up

### 120. Triangle

Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.

For example, given the following triangle
```
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```
The minimum path sum from top to bottom is 11 (i.e., 2 + 3 + 5 + 1 = 11).

#### Solution:
'Bottom-up' DP is very straightforward: we start from the nodes on the bottom row; the min pathsums for these nodes are the values of the nodes themselves. From there, the min pathsum at the ith node on the kth row would be the lesser of the pathsums of its two children plus the value of itself, i.e.:
```
dp[i][j] = min(dp[i+1][j], dp[i+1][j+1]) + triangle[i][j]
```
```
public int minimumTotal(List<List<Integer>> triangle) {
    int[] dp = new int[triangle.size() + 1];
    for (int i = triangle.size() - 1; i >= 0; i--) {
        for (int j = 0; j < triangle.get(i).size(); j++) {
            dp[j] = Math.min(dp[j], dp[j + 1]) + triangle.get(i).get(j);
        }
    }
    return dp[0];
}
```
&nbsp;
### *322. Coin Change*
You are given coins of different denominations and a total amount of money amount. Write a function to compute the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return -1.

**Example:**
```
Input: coins = [1, 2, 5], amount = 11
Output: 3 
Explanation: 11 = 5 + 5 + 1
```
#### Solution:
Top-down:

The idea is very classic dynamic programming: think of the last step we take. Suppose we have already found out the best way to sum up to amount a, then for the last step, we can choose any coin type which gives us a remainder r where *r = a-coins[i]* for all i's. For every remainder, go through exactly the same process as before until either the remainder is 0 or less than 0 (meaning not a valid solution). With this idea, the only remaining detail is to store the minimum number of coins needed to sum up to r so that we don't need to recompute it over and over again.
```
public int coinChange(int[] coins, int amount) {
    if(amount<1) return 0;
    return helper(coins, amount, new int[amount]);
}

private int helper(int[] coins, int rem, int[] count) { // rem: remaining coins after the last step; count[rem]: minimum number of coins to sum up to rem
    if(rem<0) return -1; // not valid
    if(rem==0) return 0; // completed
    if(count[rem-1] != 0) return count[rem-1]; // already computed, so reuse
    int min = Integer.MAX_VALUE;
    for(int coin : coins) {
        int res = helper(coins, rem-coin, count);
        if(res>=0 && res < min)
            min = 1+res;
    }
    count[rem-1] = (min==Integer.MAX_VALUE) ? -1 : min;
    return count[rem-1];
}
```
Bottom-up:
```
public int coinChange(int[] coins, int amount) {
    int max = amount + 1;             
    int[] dp = new int[amount + 1];  
    Arrays.fill(dp, max);  
    dp[0] = 0;   
    for (int i = 1; i <= amount; i++) {
        for (int j = 0; j < coins.length; j++) {
            if (coins[j] <= i) {
                dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1);
            }
        }
    }
    return dp[amount] > amount ? -1 : dp[amount];
}
```
&nbsp;
### *338. Counting Bits*
Given a non negative integer number num. For every numbers i in the range 0 ≤ i ≤ num calculate the number of 1's in their binary representation and return them as an array.

**Example:**
For num = 5 you should return [0,1,1,2,1,2].
#### Solution:
```
public int[] countBits(int num) {

    int[] bits = new int[num + 1];    
    for(int i = 1; i <= num; i++){
        bits[i] = bits[i/2];
        if(i%2 == 1) bits[i]++; 
    }
    return bits;
}
```
With this idea, we can also resolve this problem using [bit manipulation](https://wzvincent.github.io/2018/06/11/bit-manipulation/).
&nbsp;
&nbsp;
&nbsp;
># <font color=red>Two Dimension DP</font>
### *62. Unique Path*
A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

How many possible unique paths are there?

**Example:**
```
Input: m = 3, n = 2
Output: 3
Explanation:
From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
1. Right -> Right -> Down
2. Right -> Down -> Right
3. Down -> Right -> Right
```
#### Solution:
```
public int uniquePaths(int m, int n) {
    int[][]path = new int[m][n];
    for(int i = 0; i<m; i++){
        for(int j = 0; j<n; j++){
            if(i==0||j==0)
                path[i][j]=1;
            else
                path[i][j] = path[i-1][j] + path[i][j-1];
        }
    }
    return path[m-1][n-1];
}
```
&nbsp;
### *63. Unique Path II*
A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

Now consider if some obstacles are added to the grids. How many unique paths would there be?

An obstacle and empty space is marked as 1 and 0 respectively in the grid.

Note: m and n will be at most 100.

**Example:**
```
Input:
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
Output: 2
Explanation:
There is one obstacle in the middle of the 3x3 grid above.
There are two ways to reach the bottom-right corner:
1. Right -> Right -> Down -> Down
2. Down -> Down -> Right -> Right
```

#### Solution:
```
public int uniquePathsWithObstacles(int[][] obstacleGrid) {
    int x = obstacleGrid.length;
    int y = obstacleGrid[0].length;
    for (int i = 0; i < x; i++) {
        for (int j = 0; j < y; j++) {
            if(obstacleGrid[i][j] == 1)
                obstacleGrid[i][j] = 0;
            else if(i == 0 && j == 0)
                obstacleGrid[i][j] = 1;
            else if(i == 0)
                obstacleGrid[i][j] = obstacleGrid[i][j - 1];
            else if(j == 0)
                obstacleGrid[i][j] = obstacleGrid[i - 1][j];
            else
                obstacleGrid[i][j] = obstacleGrid[i - 1][j] + obstacleGrid[i][j - 1];
        }
    }
    return obstacleGrid[x-1][y-1];
}
```
&nbsp;
&nbsp;
### *64. Minimum Path Sum*
Given a m x n grid filled with non-negative numbers, find a path from top left to bottom right which minimizes the sum of all numbers along its path.

Note: You can only move either down or right at any point in time.

**Example:**
```
Input:
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
Output: 7
Explanation: Because the path 1→3→1→1→1 minimizes the sum.
```
#### Solution:
```
public int minPathSum(int[][] grid) {
    int m = grid.length;
    int n = grid[0].length; 
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (i == 0 && j != 0) {
                grid[i][j] = grid[i][j] + grid[i][j - 1];
            } else if (i != 0 && j == 0) {
                grid[i][j] = grid[i][j] + grid[i - 1][j];
            } else if (i == 0 && j == 0) {
                grid[i][j] = grid[i][j];
            } else {
                grid[i][j] = Math.min(grid[i][j - 1], grid[i - 1][j])
                        + grid[i][j];
            }
        }
    }
    return grid[m - 1][n - 1];
}
```
&nbsp;
### *221. Maximal Square*
Given a 2D binary matrix filled with 0's and 1's, find the largest square containing only 1's and return its area.

**Example:**
```
Input: 

1 0 1 0 0
1 0 1 1 1
1 1 1 1 1
1 0 0 1 0

Output: 4
```
#### Solution:
```
public int maximalSquare(char[][] matrix) {
    int rows = matrix.length, cols = rows > 0 ? matrix[0].length : 0;
    int [][]dp = new int [rows + 1][cols + 1];
    int max = 0;
    for (int i = 1; i <= rows; i++) {
        for (int j = 1; j <= cols; j++) {
            if (matrix[i-1][j-1] == '1') {
                dp[i][j] = Math.min( Math.min( dp[i-1][j-1], dp[i-1][j] ), dp[i][j-1] ) + 1;
                max = Math.max(dp[i][j], max);
            }
        }
    }
    return max * max;
}
```
&nbsp;
### *375. Guess Number Higher or Lower II*
We are playing the Guess Game. The game is as follows:

I pick a number from 1 to n. You have to guess which number I picked.

Every time you guess wrong, I'll tell you whether the number I picked is higher or lower.

However, when you guess a particular number x, and you guess wrong, you pay $x. You win the game when you guess the number I picked.

**Example:**
```
n = 10, I pick 8.

First round:  You guess 5, I tell you that it's higher. You pay $5.
Second round: You guess 7, I tell you that it's higher. You pay $7.
Third round:  You guess 9, I tell you that it's lower. You pay $9.

Game over. 8 is the number I picked.

You end up paying $5 + $7 + $9 = $21.
```

#### Solution:
Definition of dp[i][j]: minimum number of money to guarantee win for subproblem [i, j].

Target: dp[1][n]

Corner case: dp[i][i] = 0 (because the only element must be correct)

Equation: we can choose k (i<=k<=j) as our guess, and pay price k. After our guess, the problem is divided into two subproblems. Notice we do not need to pay the money for both subproblems. We only need to pay the worst case (because the system will tell us which side we should go) to guarantee win. So dp[i][j] = min (i<=k<=j) { k + max(dp[i][k-1], dp[k+1][j]) }
```
public int getMoneyAmount(int n) {
    int[][] dp = new int[n+1][n+1];
    for (int j = 2; j <= n; j++) {
        for (int i = j - 1; i > 0; i--) {
            int globalMin = Integer.MAX_VALUE;
            for (int k = i + 1; k < j; k++) {
                int localMax = k + Math.max(dp[i][k-1], dp[k+1][j]);
                globalMin = Math.min(globalMin, localMax);
            }
            dp[i][j] = i + 1 == j ? i : globalMin;
        }
    }
    return dp[1][n];
}
```
&nbsp;
&nbsp;
*To be continued...*