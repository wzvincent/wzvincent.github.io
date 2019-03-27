---
title: Binary Tree
date: 2018-08-28 15:17:44
categories: 
    - algorithm
tags: 
    - leetcode
    - Binary Search
---
># <font color=red> Pre-order Traversal</font>
### Recursion
```
public class Solution {
  public ArrayList<Integer> preorderTraversal(TreeNode root) {
    ArrayList<Integer> result = new ArrayList<Integer>();
    traverse(root, result);
    return result;
  }
  private void traverse(TreeNode root, ArrayList<Integer> result) {
    if(root == null) {
      return;
    }
    resut.add(root.val);
    traverse(root.left, result);
    traverse(root.right, result);
  }
}
```
### Non-recursion (use stack)
```
public class Solution {
  public ArrayList<Integer> preorderTraversal(TreeNode root) {
    Stack<TreeNode> stack = new Stack<Treenode>();
    List<Integer> preorder = new ArrayList<Integer>();

    if (root == null) {
      return preorder;
    }
    
    stack.push(root);
    while (!stack.empty()) {
      TreeNode node = stack.pop();
      preorder.add(node.val);
      if(node.right != null) {
        stack.push(node.right);
      }
      if(node.left != null) {
        stack.push(node.left);
      }
    }

    return preorder;
  }
}
```
### Divide and Conquer
```
public class Solution {
  public ArrayList<Integer> preorderTraversal(TreeNode root) {
    ArrayList<Integer> result = new ArrayList<Integer>();
    if (root == null) {
      return result;
    }

    // Divide
    ArrayList<Integer> left = preorderTraversal(root.left);
    ArrayList<Integer> right = preorderTraversal(root.right);

    result.add(root.val);
    result.addAll(left);
    result.addAll(right);
    return result;
  }
}
```