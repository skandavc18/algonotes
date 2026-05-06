# Binary Search Tree Tactics (Properties)



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1038. Binary Search Tree to Greater Sum Tree](https://leetcode.com/problems/binary-search-tree-to-greater-sum-tree/) | [1038. Binary Search Tree to Greater Sum Tree](https://leetcode.cn/problems/binary-search-tree-to-greater-sum-tree/) | 🟠 |
| [230. Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) | [230. Kth Smallest Element in a BST](https://leetcode.cn/problems/kth-smallest-element-in-a-bst/) | 🟠 |
| [538. Convert BST to Greater Tree](https://leetcode.com/problems/convert-bst-to-greater-tree/) | [538. Convert BST to Greater Tree](https://leetcode.cn/problems/convert-bst-to-greater-tree/) | 🟠 |

**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Binary Tree Structure Basics](https://labuladong.online/algo/data-structure-basic/binary-tree-basic/)
> - [DFS/BFS Traversal of Binary Trees](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/)

In earlier articles I walked through binary trees in [Part 1](https://labuladong.online/algo/data-structure/binary-tree-part1/), [Part 2 (Construction)](https://labuladong.online/algo/data-structure/binary-tree-part2/), [Part 3 (Postorder)](https://labuladong.online/algo/data-structure/binary-tree-part3/), and [Serialization](https://labuladong.online/algo/data-structure/serialize-and-deserialize-binary-tree/).

Today we begin the Binary Search Tree (BST) series and walk through BSTs hand-in-hand.

First, the BST properties (see [Binary Tree Basics](https://labuladong.online/algo/data-structure-basic/binary-tree-basic/) in the basics chapter):

1. For every node `node`, all values in its left subtree are smaller than `node.val`, and all values in its right subtree are larger.

2. For every node `node`, both its left and right subtrees are BSTs.

BSTs aren't very complex, but I'd say they account for half of the data-structure landscape: AVL trees, red-black trees, and other self-balancing BSTs (with logN insert/delete/search) are direct extensions of BSTs. B+ trees, segment trees, and other structures are also designed around BST ideas.

**For the purposes of solving algorithm problems, in addition to the definition there's one important property: the in-order traversal of a BST yields its values in sorted (ascending) order**.

That is, given a BST, the following code prints every node's value in ascending order:

```java
void traverse(TreeNode root) {
    if (root == null) return;
    traverse(root.left);
    // In-order position
    print(root.val);
    traverse(root.right);
}
```

Using this property, let's solve two problems.

## Finding the Kth Smallest Element

LeetCode 230 "Kth Smallest Element in a BST". Look at the problem:

<Problem slug="kth-smallest-element-in-a-bst" />

Common requirement; one direct idea is to sort in ascending order and pick the `k`-th element. The BST in-order traversal already gives ascending order, so finding the `k`-th element is easy.

Direct code:

```java
class Solution {
    int kthSmallest(TreeNode root, int k) {
        // Use BST's in-order traversal property
        traverse(root, k);
        return res;
    }

    // Records the result
    int res = 0;
    // Records the rank of the current element
    int rank = 0;
    void traverse(TreeNode root, int k) {
        if (root == null) {
            return;
        }
        traverse(root.left, k);

        // In-order position
        rank++;
        if (k == rank) {
            // Found the kth smallest element
            res = root.val;
            return;
        }

        traverse(root.right, k);
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/kth-smallest-element-in-a-bst/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Animated Code Visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>



This solves the problem, but a few more words: this isn't the most efficient solution — it just works for this problem.

In the earlier article [Computing the Median of a Data Stream Efficiently](https://labuladong.online/algo/practice-in-action/find-median-from-data-stream/), I posed the same question:

> [!NOTE]
> If you were asked to implement `select(int k)` to retrieve the element at rank `k` in a BST, how would you design it?

Using the method above, every call to find the `k`-th smallest does a full in-order traversal in the worst case at $O(N)$, where `N` is the number of nodes.

BST properties are powerful — improved self-balancing BSTs like red-black trees do insert/delete/search in $O(\log N)$ — yet computing the `k`-th smallest costs $O(N)$ here, which is inefficient.

So the best algorithm should also be logarithmic. That depends on how much information each BST node records.

Why are BST operations efficient? Take searching for an element: BSTs locate any element in logarithmic time fundamentally because of the BST definition — left small, right large — so each node compares its value and decides whether to descend left or right, avoiding a full traversal.

Back to this problem: to find the element of rank `k` in logarithmic time, the key is that each node knows its own rank.

Suppose I want the element of rank `k`, and the current node knows it has rank `m`. Compare:

1. If `m == k`, this is the element of rank `k`; return the current node.

2. If `k < m`, the rank-`k` element is in the left subtree; recurse left for the `k`-th element.

3. If `k > m`, the rank-`k` element is in the right subtree; recurse right for the `(k - m - 1)`-th element.

This brings the complexity to $O(\log N)$.

How does each node know its own rank?

As we said, we maintain extra information in the tree. **Each node records the total number of nodes in the subtree rooted at itself.**

That is, our `TreeNode` should look like:

```java
class TreeNode {
    int val;
    // Total number of nodes in the subtree rooted at this node
    int size;
    TreeNode left;
    TreeNode right;
}
```

Given `size` plus the BST left-small / right-large property, for any node `node` we can derive its rank using `node.left`, achieving the logarithmic algorithm.

The `size` field must be maintained correctly on inserts and deletes. LeetCode's `TreeNode` doesn't have a `size` field, so we can only use the in-order BST property here. But this optimization idea is a common BST operation worth understanding.

## Converting a BST to a Greater Sum Tree

LeetCode 538 and 1038 are the same problem; you can do both at once. Let's read the problem:

<Problem slug="convert-bst-to-greater-tree" />

The problem is easy to understand. For example, for node 5 in the figure, nodes greater than 5 are 6, 7, 8, plus 5 itself, so 5 becomes 5+6+7+8 = 26 in the greater-sum tree.

We need to convert the BST into a greater-sum tree. Signature:

```java
TreeNode convertBST(TreeNode root)
```

Following the generic binary-tree thinking, we ask what each node should do. But this problem is hard to attack that way.

A BST node is "left small, right large" — useful information. Since the cumulative sum is the sum of all elements ≥ the current value, can each node simply compute the sum of its right subtree?

No. For a given node, the right subtree is indeed all greater, but its parent could also be greater than it — and we have no parent pointer. So the generic approach doesn't work.

**This path is blocked, so let's try a different idea — still using the BST in-order property.**

We said the in-order traversal prints values in ascending order. What if we want them in descending order?

Easy: swap the recursion order — right subtree first, then left subtree:

```java
void traverse(TreeNode root) {
    if (root == null) return;
    // Recurse on the right subtree first
    traverse(root.right);
    // In-order position
    print(root.val);
    // Then recurse on the left subtree
    traverse(root.left);
}
```

**This prints node values in descending order. If we maintain an external running sum `sum` and assign `sum` to each node, we convert the BST into a greater-sum tree.**

The code makes it clear:

```java
class Solution {
    TreeNode convertBST(TreeNode root) {
        traverse(root);
        return root;
    }

    // Running sum
    int sum = 0;
    void traverse(TreeNode root) {
        if (root == null) {
            return;
        }
        traverse(root.right);
        // Maintain the running sum
        sum += root.val;
        // Convert BST into greater-sum tree
        root.val = sum;
        traverse(root.left);
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/convert-bst-to-greater-tree/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🥳 Animated Code Visualization 🥳</strong>
</summary>
</details>
</a>
<hr/>



Done. The core is again the BST in-order property — we just changed the recursion order to traverse values in descending order, matching the problem's greater-sum tree requirement.

In short: BST problems either exploit the left-small/right-large property to make algorithms efficient, or use the in-order property to satisfy requirements. Pretty much that's all there is.

That's it for this article. For more classic binary-tree problems and recursion practice, see the [Practice section](https://labuladong.online/algo/problem-set/bst1/) in the binary-tree chapter.







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Classic BST Problems I](https://labuladong.online/algo/problem-set/bst1/)
 - [Binary Search Tree Tactics (Basic Operations)](https://labuladong.online/algo/data-structure/bst-part2/)
 - [Binary Search Tree Tactics (Construction)](https://labuladong.online/algo/data-structure/bst-part3/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| - | [Sword to Offer II 054. Sum of Values of All Greater-or-Equal Nodes](https://leetcode.cn/problems/w6cpku/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
