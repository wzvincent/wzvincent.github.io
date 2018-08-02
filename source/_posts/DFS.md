---
title: Depth-First-Search
date: 2018-07-25 09:36:48
categories: 
    - algorithm
tags: 
    - leetcode
    - DFS
---
># <font color=red>Recursion Problem of Tree </font>
### *98. Validate Binary Search Tree*
Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is defined as follows:
* The left subtree of a node contains only nodes with keys less than the node's key.
* The right subtree of a node contains only nodes with keys greater than the node's key.
* Both the left and right subtrees must also be binary search trees.
<!-- more -->
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
&nbsp;
### *100. Same Tree*
Given two binary trees, write a function to check if they are the same or not.

Two binary trees are considered the same if they are structurally identical and the nodes have the same value.

**Example:**
```
Input:     1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]

Output: true

Input:     1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]

Output: false
```
#### Solution:
Use rescursion
```
 public boolean isSameTree(TreeNode p, TreeNode q) {
     if (p == null && q == null) {
         return true;
     }
     if (p == null || q == null) {
         return false;
     }
     if (p.val == q.val) {
         return isSameTree(p.left, q.left) && isSameTree(p.right, q.right)
     } else {
         return false;
     }
 }
```
&nbsp;
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
#### Solution:
```
public boolean isSymmetric(TreeNode root) {
        return helper(root, root);
    }
    
public boolean helper(TreeNode n1, TreeNode n2) {
    if (n1 == null && n2 == null) return true;
    if (n1 == null || n2 == null) return false;
    return (n1.val == n2.val) && helper(n1.left, n2.right) && helper(n2.left, n1.right);
}
```
&nbsp;
### *104. Maximum Depth of Binary Tree*
Given a binary tree, find its maximum depth.

The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

**Example:**
Given binary tree [3,9,20,null,null,15,7],
```
    3
   / \
  9  20
    /  \
   15   7
```
return its depth = 3.
#### Solution:
```
public int maxDepth(TreeNode root) {
    if(root==null){
        return 0;
    }
    return 1+Math.max(maxDepth(root.left),maxDepth(root.right));
}
```

&nbsp;
### *105. Construct Binary Tree from Preorder and Inorder Traversal*
Given preorder and inorder traversal of a tree, construct the binary tree.
Note:
You may assume that duplicates do not exist in the tree.

For example, given
```
preorder = [3,9,20,15,7]
inorder = [9,3,15,20,7]
```
Return the following binary tree:
```
    3
   / \
  9  20
    /  \
   15   7
```
#### Solution:
The basic idea is here:
Say we have 2 arrays, PRE and IN.
Preorder traversing implies that PRE[0] is the root node.
Then we can find this PRE[0] in IN, say it's IN[5].
Now we know that IN[5] is root, so we know that IN[0] - IN[4] is on the left side, IN[6] to the end is on the right side.
Recursively doing this on subarrays, we can build a tree out of it :
```
public TreeNode buildTree(int[] preorder, int[] inorder) {
    Map<Integer, Integer> map = new HashMap<Integer, Integer>();
    for (int i = 0; i < inorder.length; i++) {
        map.put(inorder[i], i);
    }
    return helper(0, preorder.length-1, 0, inorder.length-1, preorder, inorder, map);
}

public TreeNode helper(int preStart, int preEnd, int inStart, int inEnd, int[] preorder, int[] inorder, Map<Integer, Integer> inMap) {
    if(preStart > preEnd || inStart > inEnd) return null;
    TreeNode root = new TreeNode(preorder[preStart]);
    int inRoot = inMap.get(root.val);
    int len = inRoot - inStart;
    root.left = helper(preStart+1, preStart+len, inStart, inRoot-1, preorder, inorder, inMap);
    root.right = helper(preStart+len+1, preEnd, inRoot+1, inEnd, preorder, inorder, inMap);
    return root;
}
```
For postorder and inorder:
```
public TreeNode buildTree(int[] inorder, int[] postorder) {
    if (inorder == null || postorder == null || inorder.length != postorder.length) return null;
    Map<Integer, Integer> map = new HashMap<Integer, Integer>();
    for (int i = 0; i < inorder.length; i++) {
        map.put(inorder[i], i);
    }
    return helper(0, inorder.length-1, 0, postorder.length-1, inorder, postorder, map);
}

public TreeNode helper(int inStart, int inEnd, int postStart, int postEnd, int[] inorder, int[] postorder, Map<Integer, Integer> inMap){
    if (postStart > postEnd || inStart > inEnd) return null;
    TreeNode root = new TreeNode(postorder[postEnd]);
    int inRoot = inMap.get(root.val);
    int len = inRoot - inStart;
    root.left = helper(inStart, inRoot-1, postStart, postStart+len-1, inorder, postorder, inMap);
    root.right = helper(inRoot+1, inEnd, postStart+len, postEnd-1, inorder, postorder, inMap);
    return root;
}
```
&nbsp;
### *108. Convert Sorted Array to Binary Search Tree*
Given an array where elements are sorted in ascending order, convert it to a height balanced BST.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.

**Example:**
```
Given the sorted array: [-10,-3,0,5,9],

One possible answer is: [0,-3,9,-10,null,5], which represents the following height balanced BST:

      0
     / \
   -3   9
   /   /
 -10  5
```
#### Solution:
Find the middle item of the array as root
```
public TreeNode sortedArrayToBST(int[] nums) {
    if(nums.length==0)
        return null;
    TreeNode node = findMiddle(nums,0,nums.length-1);
    return node;
}

public TreeNode findMiddle(int[]num, int left, int right){
    if(left>right)
        return null;
    int mid = (left+right)/2;
    TreeNode root = new TreeNode(num[mid]);
    root.left = findMiddle(num,left,mid-1);
    root.right = findMiddle(num, mid+1, right);
    return root;
}
```
For Sorted List, use two pointers to find the middle item
```
public TreeNode sortedListToBST(ListNode head) {
    if(head==null)
        return null;
    return toBST(head,null);
}

public TreeNode toBST(ListNode head, ListNode tail){
    if(head==tail)
        return null;
    ListNode fast = head;
    ListNode slow = head;
    while(fast!=tail&&fast.next!=tail){
        fast = fast.next.next;
        slow = slow.next;
    }
    TreeNode root = new TreeNode(slow.val);
    root.left = toBST(head,slow);
    root.right = toBST(slow.next,tail);
    return root;
}
```
&nbsp;
### *110. Balanced Binary Tree*
Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as:
> a binary tree in which the depth of the two subtrees of every node never differ by more than 1.

**Example:**
Given the following tree [3,9,20,null,null,15,7]:
```
    3
   / \
  9  20
    /  \
   15   7
```
Return true.
#### Solution:
```
public boolean isBalanced(TreeNode root) {
    if(root == null)
        return true;
    if(Math.abs(treeHeight(root.left)-treeHeight(root.right))>1)
        return false;
    return isBalanced(root.left)&&isBalanced(root.right);
}

public int treeHeight(TreeNode node){
    if(node == null)
        return 0;
    return Math.max(treeHeight(node.left),treeHeight(node.right))+1;
}
```