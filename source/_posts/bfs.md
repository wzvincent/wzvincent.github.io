---
title: BFS
date: 2018-08-07 09:38:49
categories: 
    - algorithm
tags: 
    - leetcode
    - BFS
---
### *101. Symmetric Tree*
Given a binary tree, check whether it is a mirror of itself (ie, symmetric around its center).

For example, this binary tree [1,2,2,3,4,4,3] is symmetric:
```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```
But the following [1,2,2,null,3,null,3] is not:
```

    1
   / \
  2   2
   \   \
   3    3
```
<!-- more -->
#### Solution:
```
public boolean isSymmetric(TreeNode root) {
    if (root == null) return true;
    Queue<TreeNode> queue = new LinkedList<TreeNode>();
    queue.offer(root.left);
    queue.offer(root.right);
    while (!queue.isEmpty()) {
        TreeNode left = queue.poll();
        TreeNode right = queue.poll();
        if (left == null && right == null) continue;
        if (left == null || right == null) return false;
        if (left.val != right.val) return false;
        queue.offer(left.left);
        queue.offer(right.right);
        queue.offer(left.right);
        queue.offer(right.left);
    }
    return true;
}
```
&nbsp;
### *102. Binary Tree Level Order Traversal*
Given a binary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).

**Example:**
Given binary tree [3,9,20,null,null,15,7],
```
    3
   / \
  9  20
    /  \
   15   7
```
return its level order traversal as:
```
[
  [3],
  [9,20],
  [15,7]
]
```
#### Solution:
```
public List<List<Integer>> levelOrder(TreeNode root) {
    Queue<TreeNode> queue = new LinkedList<TreeNode>();
    List<List<Integer>> wrapList = new LinkedList<List<Integer>>();
    if (root == null) return wrapList;
    queue.offer(root);
    while(!queue.isEmpty()) {
        int BreadthOfLevel = queue.size();
        List<Integer> subList = new LinkedList<Integer>();
        for (int i=0; i<BreadthOfLevel; i++) {
            if (queue.peek().left != null) queue.offer(queue.peek().left);
            if (queue.peek().right != null) queue.offer(queue.peek().right);
            subList.add(queue.poll().val);
        }
        wrapList.add(subList);
    }
    return wrapList;
}
```