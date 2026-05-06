# Binary Tree Tactics (Approach)


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [114. Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/) | [114. Flatten Binary Tree to Linked List](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/) | 🟠 |
| [116. Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/) | [116. Populating Next Right Pointers in Each Node](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/) | 🟠 |
| [226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/) | [226. Invert Binary Tree](https://leetcode.cn/problems/invert-binary-tree/) | 🟢 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Binary Tree Structure Basics](https://labuladong.online/algo/data-structure-basic/binary-tree-basic/)
> - [DFS/BFS Traversal of Binary Trees](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/)
> - [Binary Tree Tactics (Outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/)

> tip: a video version is available: [Framework Thinking for Binary Trees / Recursion (Outline)](https://www.bilibili.com/video/BV1nG411x77H/). Follow my Bilibili account; I'll guide you through more difficult algorithm techniques on video.


This article continues from [Binary Tree Tactics (Outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/). First, recall the outline:

> [!NOTE]
> Two thinking modes for binary-tree problems:
> 
> **1. Can the answer be obtained by traversing the tree once?** If so, use a `traverse` function with external variables — the "traversal" thinking mode.
> 
> **2. Can a recursive function be defined that derives the original problem's answer from sub-problems' (subtrees') answers?** If so, write the recursive definition and exploit its return value — the "subproblem decomposition" thinking mode.
> 
> Either way, you must consider:
> 
> **What does an individual binary-tree node need to do? At what point (preorder / inorder / postorder) should it do it?** You don't need to worry about the other nodes; the recursive function will perform the same operation on every node.

We'll work through a few simple problems to practice these rules and to see the difference and connection between the "traversal" and "decomposition" mindsets.


## Problem 1: Invert Binary Tree

Start with an easy one — LeetCode 226 "Invert Binary Tree". Given the root, mirror-flip the entire tree. For example:

```
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```

Becomes:

```
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

We just need to swap each node's left and right children.

Now run through the binary-tree outline:

**1. Can it be solved with the "traversal" mindset?**

Yes — write a `traverse` function that visits each node and swaps its children.

What does a single node do? Swap its left and right children.

When? Pre-, in-, or postorder all work.

Code:

```java
class Solution {
    // Main function
    public TreeNode invertTree(TreeNode root) {
        // Traverse the tree, swapping each node's children
        traverse(root);
        return root;
    }

    // Tree-traversal function
    void traverse(TreeNode root) {
        if (root == null) {
            return;
        }

        // *** Preorder position ***
        // Each node swaps its left and right children
        TreeNode tmp = root.left;
        root.left = root.right;
        root.right = tmp;

        // Traversal framework: visit subtrees
        traverse(root.left);
        traverse(root.right);
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/mydata-invert-tree/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌟 Animated Code Visualization 🌟</strong>
</summary>
</details>
</a>
<hr/>


You can move the preorder code to postorder; in-order won't work directly without a small change — you can probably see why.

The problem is solved, but for comparison let's continue.

**2. Can it be solved with the "decomposition" mindset?**

Give `invertTree` a definition:

```java
// Definition: invert the tree rooted at root and return the new root
TreeNode invertTree(TreeNode root);
```

Then, for any node `x`, what can `invertTree(x)` do with this definition?

Use `invertTree(x.left)` to invert `x`'s left subtree, `invertTree(x.right)` to invert the right subtree, and then swap `x`'s children. That fully inverts the tree rooted at `x`.

Code:

```java
class Solution {
    // Definition: invert the tree rooted at root and return the new root
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return null;
        }
        // Use the definition to invert the subtrees
        TreeNode left = invertTree(root.left);
        TreeNode right = invertTree(root.right);

        // Then swap children
        root.left = right;
        root.right = left;

        // Consistent with the definition: the tree rooted at root is now inverted
        return root;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/mydata-invert-tree2/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Animated Code Visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>


The key to "decomposition" thinking is to give the recursive function a fitting definition and explain your code in terms of that definition. If the logic is self-consistent, the algorithm is correct.

That's it for this one — both mindsets work. On to the next.

## Problem 2: Populating Next Right Pointers

LeetCode 116 "Populating Next Right Pointers in Each Node":

<Problem slug="populating-next-right-pointers-in-each-node" />

```java
// Signature
Node connect(Node root);
```

Connect every level of the tree using `next` pointers:

![](https://labuladong.online/algo/images/binary-tree-i/1.png)

The input is a "perfect binary tree" — visually a triangle. Every node except the rightmost in each level has a right neighbor.

Outline check:

**1. "Traversal" mindset?**

Sure.

Each node's job: point its `next` pointer to its right neighbor.

You might mimic the previous problem and write:

```java
// Tree-traversal function
void traverse(Node root) {
    if (root == null || root.left == null) {
        return;
    }
    // Point left child's next to right child
    root.left.next = root.right;

    traverse(root.left);
    traverse(root.right);
}
```

But this code has a problem — it only connects two children of the same parent. Look again:

![](https://labuladong.online/algo/images/binary-tree-i/1.png)

Nodes 5 and 6 don't share a parent, so this code can't connect them. Where's the issue?

**The traditional `traverse` visits every node, but here we want to visit the "gaps" between adjacent nodes.**

Abstract one more level — treat each box in the figure as a node:

![](https://labuladong.online/algo/images/binary-tree-i/3.png)

**Now the binary tree is a ternary tree, where each ternary-tree node holds two adjacent binary-tree nodes.**

We just need to traverse this ternary tree; each ternary-tree node connects its two underlying binary-tree nodes:

```java
class Solution {
    // Main
    public Node connect(Node root) {
        if (root == null) return null;
        // Traverse the "ternary tree" and connect adjacent nodes
        traverse(root.left, root.right);
        return root;
    }

    // Ternary-tree traversal framework
    void traverse(Node node1, Node node2) {
        if (node1 == null || node2 == null) {
            return;
        }
        // *** Preorder ***
        // Connect the two given nodes
        node1.next = node2;
        
        // Connect children sharing the same parent
        traverse(node1.left, node1.right);
        traverse(node2.left, node2.right);
        // Connect the two children straddling parents
        traverse(node1.right, node2.left);
    }
}
```

This `traverse` walks the entire "ternary tree", connecting all adjacent binary-tree nodes — solving the issue cleanly.

**2. "Decomposition" mindset?**

There's no especially clean decomposition for this problem.

## Problem 3: Flatten Binary Tree to Linked List

LeetCode 114 "Flatten Binary Tree to Linked List":

<Problem slug="flatten-binary-tree-to-linked-list" />

```java
// Signature
void flatten(TreeNode root);
```

**1. "Traversal" mindset?**

It seems plausible: do a preorder traversal, building a "linked list" along the way:

```java
// Dummy head; result is dummy.right
TreeNode dummy = new TreeNode(-1);
// Pointer to build the list
TreeNode p = dummy;

void traverse(TreeNode root) {
    if (root == null) {
        return;
    }
    // Preorder
    p.right = new TreeNode(root.val);
    p = p.right;

    traverse(root.left);
    traverse(root.right);
}
```

But the signature returns `void` — meaning we must flatten the tree **in place**.

So a simple traversal doesn't work directly.

**2. "Decomposition" mindset?**

Define `flatten`:

```java
// Definition: take root, then the tree rooted at root is flattened to a linked list
void flatten(TreeNode root);
```

Given this definition, how to flatten?

For a node `x`:

1. Use `flatten(x.left)` and `flatten(x.right)` to flatten the subtrees.

2. Attach `x.right` (already flattened) to the bottom of `x.left` (already flattened), then make the entire combined list `x`'s right child (and clear `x.left`).

![](https://labuladong.online/algo/images/binary-tree-i/2.jpeg)

Now the tree rooted at `x` is fully flattened — exactly fulfilling `flatten(x)`'s definition.

Code:

```java
class Solution {
    // Definition: flatten the tree rooted at root into a linked list
    public void flatten(TreeNode root) {
        // base case
        if (root == null) return;
        
        // Use the definition to flatten the subtrees
        flatten(root.left);
        flatten(root.right);

        // *** Postorder ***
        // 1. Subtrees have been flattened
        TreeNode left = root.left;
        TreeNode right = root.right;
        
        // 2. Use the left subtree as the new right
        root.left = null;
        root.right = left;

        // 3. Append the original right subtree to the end of the new right
        TreeNode p = root;
        while (p.right != null) {
            p = p.right;
        }
        p.right = right;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/flatten-binary-tree-to-linked-list/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>


That's the magic of recursion. How exactly does `flatten` flatten the subtrees?

It's not easy to describe step by step, but as long as `flatten`'s definition holds, each node does its job and `flatten` works as defined.

Solved. The recursion idea in [k-Group Reverse Linked List](https://labuladong.online/algo/data-structure/reverse-linked-list-recursion/) is similar.

To close, recap the outline:

Two thinking modes for binary-tree problems:

**1. Can the answer be obtained by traversing the tree once?** If so, use `traverse` + external variables — "traversal" mindset.

**2. Can a recursive function be defined that derives the original answer from subtree answers?** If so, define and exploit it — "decomposition" mindset.

Either way:

**What does a single node need to do, and at what point (preorder / inorder / postorder)?** Don't worry about the rest — the recursion handles every node.

Take this to heart and apply it to all binary-tree problems.

For more classic binary-tree problems and recursion practice, see [Recursion Practice](https://labuladong.online/algo/intro/binary-tree-practice/) in the binary-tree chapter.


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Combining Both Mindsets](https://labuladong.online/algo/problem-set/binary-tree-combine-two-view/)
 - [[Practice] Traversal Mindset I](https://labuladong.online/algo/problem-set/binary-tree-traverse-i/)
 - [[Practice] Traversal Mindset II](https://labuladong.online/algo/problem-set/binary-tree-traverse-ii/)
 - [[Practice] Traversal Mindset III](https://labuladong.online/algo/problem-set/binary-tree-traverse-iii/)
 - [BST Tactics (Construction)](https://labuladong.online/algo/data-structure/bst-part3/)
 - [BST Tactics (Properties)](https://labuladong.online/algo/data-structure/bst-part1/)
 - [Binary Tree Tactics (Construction)](https://labuladong.online/algo/data-structure/binary-tree-part2/)
 - [Divide-and-Conquer: Operator Precedence](https://labuladong.online/algo/fname.html?fname=divide-and-conquer)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| - | [Sword to Offer 26. Substructure of a Tree](https://leetcode.cn/problems/shu-de-zi-jie-gou-lcof/?show=1) | 🟠 |
| - | [Sword to Offer 27. Mirror of a Binary Tree](https://leetcode.cn/problems/er-cha-shu-de-jing-xiang-lcof/?show=1) | 🟢 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
