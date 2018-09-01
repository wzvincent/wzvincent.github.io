---
title: Binary Search
date: 2018-08-24 17:05:43
categories: 
    - algorithm
tags: 
    - leetcode
    - Binary Search
---
># <font color=red> Fundemental Solutions</font>
#### While Condition:
start + 1 < end
#### Middle Item:
mid = (end - start) / 2 + start 
#### Next Process:
start = mid
end = mid
<!-- more -->
```
public int binarySearch(int nums, int target) {
  if (nums == null || nums.length == 0) return -1;

  int start = 0, end = nums.length - 1;

  while (start + 1 < end) {
    int mid = (end - start) / 2 + start;
    if (nums[mid] == target) {
      return mid;
    } else if (nums[mid] < target) {
      start = mid;
    } else {
      end = mid;
    }
  }

  if (nums[start] == target) {
    return start;
  }

  if (nums[end] == target) {
    return end;
  }
  
  return -1;
}
```
&nbsp;
&nbsp;
># <font color=red> Problems </font>
### *33. Search in Rotated Sorted Array*

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., [0,1,2,4,5,6,7] might become [4,5,6,7,0,1,2]).

You are given a target value to search. If found in the array return its index, otherwise return -1.

You may assume no duplicate exists in the array.

Your algorithm's runtime complexity must be in the order of O(log n).

**Example:**
```
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
```
#### Solution:
```
public int search(int[] nums, int target) {
    if (nums == null || nums.length == 0) return -1;     
    int start = 0, end = nums.length - 1;
    
    while (start + 1 < end) {
        int mid = (end - start) / 2 + start;
        if (target == nums[mid]) {
            return mid;
        }
        if (nums[start] < nums[mid]) {
            if (target <= nums[mid] && target >= nums[start]) {
                end = mid;
            } else {
                start = mid;
            }  
        } else {
            if (target <= nums[end] && target >= nums[mid]) {
                start = mid;
            } else {
                end = mid;
            }
        }
    }
    
    if (nums[start] == target) {
        return start;
    }
    if (nums[end] == target) {
        return end;
    }
    return -1;
}
```
&nbsp;
&nbsp;
### *33. Search in Rotated Sorted Array*
Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e.,  [0,1,2,4,5,6,7] might become  [4,5,6,7,0,1,2]).

Find the minimum element.

You may assume no duplicate exists in the array.
**Example:**
```
Input: [3,4,5,1,2] 
Output: 1
```
#### Solution:
```
public int findMin(int[] nums) {
    if (nums == null || nums.length == 0) return 0;
    if (nums.length == 1) return nums[0];
    int start = 0, end = nums.length - 1;
    while (start + 1 < end) {
        int mid = start + (end - start) / 2;
        if (mid > 0 && nums[mid] < nums[mid - 1]) {
            return nums[mid];
        } else if (nums[mid] < nums[end]) {
            end = mid;
        } else {
            start = mid;
        }
    }
    if (nums[start] < nums[end]) {
        return nums[start];
    }
    return nums[end];
}
```
&nbsp;
&nbsp;
### *74. Search a 2D Matrix*
Write an efficient algorithm that searches for a value in an m x n matrix. This matrix has the following properties:

* Integers in each row are sorted from left to right.
* The first integer of each row is greater than the last integer of the previous row.

**Example:**
```
Input:
matrix = [
  [1,   3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 50]
]
target = 3
Output: true
```
#### Solution:
Treat this matrix as a sorted array.
```
public boolean searchMatrix(int[][] matrix, int target) {
    if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return false;
    int m = matrix[0].length, n = matrix.length;
    int start = 0, end = m * n - 1;
    while (start + 1 < end) {
        int mid = start + (end - start) / 2;
        int val = matrix[mid / m][mid % m];
        if (target == val) {
            return true;
        } else if (target > val) {
            start = mid;
        } else {
            end = mid;
        }
    }
    if (matrix[start / m][start % m] == target || matrix[end / m][end % m] == target) {
        return true;
    }
    return false;
}
```
