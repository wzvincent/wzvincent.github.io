---
title: Depth-First-Search
date: 2018-07-25 09:36:48
categories: 
    - algorithm
tags: 
    - leetcode
    - DFS
---
### *98. Validate Binary Search Tree*
Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is defined as follows:
* The left subtree of a node contains only nodes with keys less than the node's key.
* The right subtree of a node contains only nodes with keys greater than the node's key.
* Both the left and right subtrees must also be binary search trees.

#### Solution:
With Stack (In-order Traversal):
```
public boolean isValidBST(TreeNode root) {
    if (root == null) return true;
    Stack<TreeNode> stack = new Stack<>();
    TreeNode pre = null;
    while (root != null || !stack.isEmpty()) {
        while (root != null) {
            stack.push(root);
            root = root.left;
        }
        root = stack.pop();
        if (pre != null && pre.val >= root.val) return false;
        pre = root;
        root = root.right;
    }
    return true;
}
```
With Recursion:
```
public class Solution {
    public boolean isValidBST(TreeNode root) {
        return isValidBST(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }
    
    public boolean isValidBST(TreeNode root, long minVal, long maxVal) {
        if (root == null) return true;
        if (root.val >= maxVal || root.val <= minVal) return false;
        return isValidBST(root.left, minVal, root.val) && isValidBST(root.right, root.val, maxVal);
    }
}
```