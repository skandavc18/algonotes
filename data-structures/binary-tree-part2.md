# Binary Tree Tactics (Construction)


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) | [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) | 🟠 |
| [106. Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/) | [106. Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/) | 🟠 |
| [654. Maximum Binary Tree](https://leetcode.com/problems/maximum-binary-tree/) | [654. Maximum Binary Tree](https://leetcode.cn/problems/maximum-binary-tree/) | 🟠 |
| [889. Construct Binary Tree from Preorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-postorder-traversal/) | [889. Construct Binary Tree from Preorder and Postorder Traversal](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-postorder-traversal/) | 🟠 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Binary Tree Structure Basics](https://labuladong.online/algo/data-structure-basic/binary-tree-basic/)
> - [DFS/BFS Traversal of Binary Trees](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/)
> - [Binary Tree Tactics (Outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/)

This is the second article continuing from [Binary Tree Tactics (Outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/). First, recall the outline:

> [!NOTE]
> Two thinking modes for binary-tree problems: 
> 
> **1. Can the answer be obtained by traversing the tree once?** If so, use a `traverse` function with external variables — the "traversal" mindset.
> 
> **2. Can a recursive function be defined that derives the original answer from sub-problems' (subtrees') answers?** If so, write the recursive definition and exploit its return value — the "decomposition" mindset.
> 
> Either way, ask:
> 
> **What does an individual node need to do, and at what point (preorder / inorder / postorder) should it do it?** Don't worry about the rest — recursion handles every node.

The first article, [Binary Tree Tactics (Approach)](https://labuladong.online/algo/data-structure/binary-tree-part1/), discussed both mindsets. This article focuses on tree-construction problems.

**Construction problems are typically solved with the "decomposition" mindset: build the whole tree = root + build left subtree + build right subtree**.

Let's dive in.

## Build a Maximum Binary Tree

LeetCode 654 "Maximum Binary Tree":

<Problem slug="maximum-binary-tree" />

```java
// Signature
TreeNode constructMaximumBinaryTree(int[] nums);
```

Each tree node is the root of a subtree. For the root, we first need to construct itself, then construct its left and right subtrees.

So we scan the array for the maximum `maxVal` to make the root, then recursively build the left subtree from the left portion and the right subtree from the right portion.

Example: input `[3,2,1,6,0,5]`. For the overall root:

```java
TreeNode constructMaximumBinaryTree([3,2,1,6,0,5]) {
    // Find the maximum
    TreeNode root = new TreeNode(6);
    // Recurse on the left and right portions
    root.left = constructMaximumBinaryTree([3,2,1]);
    root.right = constructMaximumBinaryTree([0,5]);
    return root;
}

// The current max in nums is the root; recurse on the left/right portions
// Pseudo-code:
TreeNode constructMaximumBinaryTree(int[] nums) {
    if (nums is empty) return null;
    // Find the max in nums
    int maxVal = Integer.MIN_VALUE;
    int index = 0;
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] > maxVal) {
            maxVal = nums[i];
            index = i;
        }
    }

    TreeNode root = new TreeNode(maxVal);
    // Recurse on the subarrays
    root.left = constructMaximumBinaryTree(nums[0..index-1]);
    root.right = constructMaximumBinaryTree(nums[index+1..nums.length-1]);
    return root;
}
```

**The current max in `nums` is the root; recurse on the left/right portions.**

With the idea clear, write a helper `build` to control indices into `nums`:

```java
class Solution {

    public TreeNode constructMaximumBinaryTree(int[] nums) {
        return build(nums, 0, nums.length - 1);
    }

    // Definition: build the tree from nums[lo..hi] and return the root
    TreeNode build(int[] nums, int lo, int hi) {
        // base case
        if (lo > hi) {
            return null;
        }

        // Find the max and its index
        int index = -1, maxVal = Integer.MIN_VALUE;
        for (int i = lo; i <= hi; i++) {
            if (maxVal < nums[i]) {
                index = i;
                maxVal = nums[i];
            }
        }

        // Build the root
        TreeNode root = new TreeNode(maxVal);
        // Recurse on the subtrees
        root.left = build(nums, lo, index - 1);
        root.right = build(nums, index + 1, hi);
        
        return root;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/maximum-binary-tree/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>👾 Animated Code Visualization 👾</strong>
</summary>
</details>
</a>
<hr/>


That's it — pretty simple. Two harder problems follow.

## Build a Tree from Preorder and Inorder Traversals

LeetCode 105 "Construct Binary Tree from Preorder and Inorder Traversal":

<Problem slug="construct-binary-tree-from-preorder-and-inorder-traversal" />

```java
// Signature
TreeNode buildTree(int[] preorder, int[] inorder);
```

Think about what the root should do.

**Like the previous problem, we figure out the root's value, build the root, then recursively build the subtrees.**

Recall the characteristics of preorder and inorder traversals:

```java
void traverse(TreeNode root) {
    // Preorder
    preorder.add(root.val);
    traverse(root.left);
    traverse(root.right);
}

void traverse(TreeNode root) {
    traverse(root.left);
    // Inorder
    inorder.add(root.val);
    traverse(root.right);
}
```

As discussed in [There Are Only a Few Binary-Tree Frameworks](https://labuladong.online/algo/data-structure/flatten-nested-list-iterator/), the order difference produces these element distributions in `preorder` and `inorder`:

![](https://labuladong.online/algo/images/binary-tree-ii/1.jpeg)

Finding the root is easy — `preorder[0]` is the root's value.

The key is using the root's value to split `preorder` and `inorder` into left and right halves to build the subtrees.

What goes in the `?` below:

```java
TreeNode buildTree(int[] preorder, int[] inorder) {
    // Use the function definition to build from preorder and inorder
    return build(preorder, 0, preorder.length - 1,
                inorder, 0, inorder.length - 1);
}

// Definition:
// Given preorder[preStart..preEnd] and inorder[inStart..inEnd],
// build the tree and return its root.
TreeNode build(int[] preorder, int preStart, int preEnd, 
            int[] inorder, int inStart, int inEnd) {
    // The root's value is the first element of preorder
    int rootVal = preorder[preStart];
    // Index of rootVal in inorder
    int index = 0;
    for (int i = inStart; i <= inEnd; i++) {
        if (inorder[i] == rootVal) {
            index = i;
            break;
        }
    }

    TreeNode root = new TreeNode(rootVal);
    // Recurse to build the subtrees
    root.left = build(preorder, ?, ?,
                    inorder, ?, ?);

    root.right = build(preorder, ?, ?,
                    inorder, ?, ?);
    return root;
}
```

For `rootVal` and `index`, see the figure:

![](https://labuladong.online/algo/images/binary-tree-ii/2.jpeg)

Note: scanning to find `index` isn't optimal. We can speed it up with a HashMap from value to index — the problem guarantees no duplicate values:

```java
// Maps inorder value -> index
HashMap<Integer, Integer> valToIndex = new HashMap<>();

public TreeNode buildTree(int[] preorder, int[] inorder) {
    for (int i = 0; i < inorder.length; i++) {
        valToIndex.put(inorder[i], i);
    }
    return build(preorder, 0, preorder.length - 1,
                 inorder, 0, inorder.length - 1);
}

TreeNode build(int[] preorder, int preStart, int preEnd, 
               int[] inorder, int inStart, int inEnd) {
    int rootVal = preorder[preStart];
    // Avoid scanning to find rootVal
    int index = valToIndex.get(rootVal);
    // ...
}
```

Now, fill in the blanks:

```java
root.left = build(preorder, ?, ?,
                  inorder, ?, ?);

root.right = build(preorder, ?, ?,
                   inorder, ?, ?);
```

The inorder range for each subtree is straightforward:

![](https://labuladong.online/algo/images/binary-tree-ii/3.jpeg)

```java
root.left = build(preorder, ?, ?,
                  inorder, inStart, index - 1);

root.right = build(preorder, ?, ?,
                   inorder, index + 1, inEnd);
```

For preorder, we deduce the ranges from the size of the left subtree. Let `leftSize` be the number of nodes in the left subtree. Then the preorder index layout is:

![](https://labuladong.online/algo/images/binary-tree-ii/4.jpeg)

Fill in the preorder ranges:

```java
int leftSize = index - inStart;

root.left = build(preorder, preStart + 1, preStart + leftSize,
                  inorder, inStart, index - 1);

root.right = build(preorder, preStart + leftSize + 1, preEnd,
                   inorder, index + 1, inEnd);
```

Add the base case:

```java
class Solution {
    // Maps inorder value -> index
    HashMap<Integer, Integer> valToIndex = new HashMap<>();

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        for (int i = 0; i < inorder.length; i++) {
            valToIndex.put(inorder[i], i);
        }
        return build(preorder, 0, preorder.length - 1,
                    inorder, 0, inorder.length - 1);
    }

    // Definition:
    // Given preorder[preStart..preEnd] and inorder[inStart..inEnd],
    // build the tree and return its root.
    TreeNode build(int[] preorder, int preStart, int preEnd, 
               int[] inorder, int inStart, int inEnd) {

        if (preStart > preEnd) {
            return null;
        }

        // The root's value is the first element of preorder
        int rootVal = preorder[preStart];
        // Index of rootVal in inorder
        int index = valToIndex.get(rootVal);

        int leftSize = index - inStart;

        // Build the root
        TreeNode root = new TreeNode(rootVal);
        // Recurse to build the subtrees
        root.left = build(preorder, preStart + 1, preStart + leftSize,
                        inorder, inStart, index - 1);

        root.right = build(preorder, preStart + leftSize + 1, preEnd,
                        inorder, index + 1, inEnd);
        return root;
    }
}
```

The main function just calls `buildTree`. The function may look intimidating with all those parameters, but they're just controlling subarray ranges — easy with a picture.

## Build a Tree from Inorder and Postorder Traversals

Like before, but using **postorder** and **inorder**. LeetCode 106:

<Problem slug="construct-binary-tree-from-inorder-and-postorder-traversal" />

```java
// Signature
TreeNode buildTree(int[] inorder, int[] postorder);
```

Postorder vs inorder:

```java
void traverse(TreeNode root) {
    traverse(root.left);
    traverse(root.right);
    // Postorder
    postorder.add(root.val);
}

void traverse(TreeNode root) {
    traverse(root.left);
    // Inorder
    inorder.add(root.val);
    traverse(root.right);
}
```

The element layouts:

![](https://labuladong.online/algo/images/binary-tree-ii/5.jpeg)

Key difference vs. the previous problem: the root's value is the **last** element of `postorder`.

The framework is similar; helper `build`:

```java
class Solution {
    // Maps inorder value -> index
    HashMap<Integer, Integer> valToIndex = new HashMap<>();

    public TreeNode buildTree(int[] inorder, int[] postorder) {
        for (int i = 0; i < inorder.length; i++) {
            valToIndex.put(inorder[i], i);
        }
        return build(inorder, 0, inorder.length - 1,
                    postorder, 0, postorder.length - 1);
    }

    // Definition:
    // Given postorder[postStart..postEnd] and inorder[inStart..inEnd],
    // build the tree and return its root.
    TreeNode build(int[] inorder, int inStart, int inEnd,
                int[] postorder, int postStart, int postEnd) {
        // The root's value is the last element of postorder
        int rootVal = postorder[postEnd];
        // Index of rootVal in inorder
        int index = valToIndex.get(rootVal);

        TreeNode root = new TreeNode(rootVal);
        // Recurse to build the subtrees
        root.left = build(inorder, ?, ?,
                        postorder, ?, ?);

        root.right = build(inorder, ?, ?,
                        postorder, ?, ?);
        return root;
    }
}
```

Postorder/inorder layout:

![](https://labuladong.online/algo/images/binary-tree-ii/6.jpeg)

Fill in the indices:

```java
int leftSize = index - inStart;

root.left = build(inorder, inStart, index - 1,
                  postorder, postStart, postStart + leftSize - 1);

root.right = build(inorder, index + 1, inEnd,
                   postorder, postStart + leftSize, postEnd - 1);
```

Full solution:

```java
class Solution {
    // Maps inorder value -> index
    HashMap<Integer, Integer> valToIndex = new HashMap<>();

    public TreeNode buildTree(int[] inorder, int[] postorder) {
        for (int i = 0; i < inorder.length; i++) {
            valToIndex.put(inorder[i], i);
        }
        return build(inorder, 0, inorder.length - 1,
                    postorder, 0, postorder.length - 1);
    }

    // Definition:
    // Given postorder[postStart..postEnd] and inorder[inStart..inEnd],
    // build the tree and return its root.
    TreeNode build(int[] inorder, int inStart, int inEnd,
                int[] postorder, int postStart, int postEnd) {

        if (inStart > inEnd) {
            return null;
        }
        // The root's value is the last element of postorder
        int rootVal = postorder[postEnd];
        // Index of rootVal in inorder
        int index = valToIndex.get(rootVal);
        // Number of nodes in the left subtree
        int leftSize = index - inStart;
        TreeNode root = new TreeNode(rootVal);
        // Recurse to build the subtrees
        root.left = build(inorder, inStart, index - 1,
                            postorder, postStart, postStart + leftSize - 1);
        
        root.right = build(inorder, index + 1, inEnd,
                            postorder, postStart + leftSize, postEnd - 1);
        return root;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/construct-binary-tree-from-inorder-and-postorder-traversal/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Animated Code Visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>


With the previous setup, this one comes quickly — just `rootVal` becomes the last element and the parameters change a bit.

## Build a Tree from Preorder and Postorder Traversals

LeetCode 889 "Construct Binary Tree from Preorder and Postorder Traversal" gives preorder and postorder, asks you to build the tree.

```java
TreeNode constructFromPrePost(int[] preorder, int[] postorder);
```

A key difference from the previous two:

**Preorder + inorder (or postorder + inorder) uniquely determines a binary tree, but preorder + postorder does NOT.**

The problem says you may return any valid answer.

Why? Construction is "find the root, then recursively build the subtrees".

In the previous problems, preorder/postorder gave us the root, and inorder gave us the left/right partitioning (with no duplicate values).

Here, you find the root but cannot tell which nodes belong to which subtree.

Example: `preorder = [1,2,3], postorder = [3,2,1]`.

Both of these trees are valid but structurally different:

![](https://labuladong.online/algo/images/binary-tree-ii/7.png)

Still, the logic is similar:

**1. The first element of preorder (or last of postorder) is the root.**

**2. The second element of preorder is the root of the left subtree.**

**3. Find the left-subtree-root in postorder; this fixes the left-subtree boundary, and thus the right-subtree boundary too. Recurse.**

![](https://labuladong.online/algo/images/binary-tree-ii/8.jpeg)

Code:

```java
class Solution {
    // Maps postorder value -> index
    HashMap<Integer, Integer> valToIndex = new HashMap<>();

    public TreeNode constructFromPrePost(int[] preorder, int[] postorder) {
        for (int i = 0; i < postorder.length; i++) {
            valToIndex.put(postorder[i], i);
        }
        return build(preorder, 0, preorder.length - 1,
                    postorder, 0, postorder.length - 1);
    }

    // Definition: build the tree from preorder[preStart..preEnd]
    // and postorder[postStart..postEnd], return the root.
    TreeNode build(int[] preorder, int preStart, int preEnd,
                   int[] postorder, int postStart, int postEnd) {
        if (preStart > preEnd) {
            return null;
        }
        if (preStart == preEnd) {
            return new TreeNode(preorder[preStart]);
        }

        // root's value is the first element of preorder
        int rootVal = preorder[preStart];
        // root.left's value is the second element of preorder
        // The trick is: find the left subtree's root and use it to
        // determine the left/right element ranges in preorder/postorder
        int leftRootVal = preorder[preStart + 1];
        // leftRootVal's index in postorder
        int index = valToIndex.get(leftRootVal);
        // Number of nodes in the left subtree
        int leftSize = index - postStart + 1;

        // Build root
        TreeNode root = new TreeNode(rootVal);
        // Recurse on the subtrees
        // Use the left-subtree root index and size to derive the ranges
        root.left = build(preorder, preStart + 1, preStart + leftSize,
                postorder, postStart, index);
        root.right = build(preorder, preStart + leftSize + 1, preEnd,
                postorder, index + 1, postEnd - 1);

        return root;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/construct-binary-tree-from-preorder-and-postorder-traversal/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>👾 Animated Code Visualization 👾</strong>
</summary>
</details>
</a>
<hr/>


Why is the result not unique? The key line:

```java
int leftRootVal = preorder[preStart + 1];
```

We assume the second preorder element is the left subtree's root, but the left subtree may be empty — in which case it's the right subtree's root. We can't tell, so the result isn't unique.

That wraps up all three reconstruction problems.

To echo the introduction: **construction problems use the "decomposition" mindset — build the tree = root + build left subtree + build right subtree**. Find the root, partition the elements, recurse.

Hope this clears up the trick.


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Classic BST Problems I](https://labuladong.online/algo/problem-set/bst1/)
 - [[Practice] Decomposition Mindset I](https://labuladong.online/algo/problem-set/binary-tree-divide-i/)
 - [[Practice] Decomposition Mindset II](https://labuladong.online/algo/problem-set/binary-tree-divide-ii/)
 - [BST Tactics (Properties)](https://labuladong.online/algo/data-structure/bst-part1/)
 - [Binary Tree Tactics (Serialization)](https://labuladong.online/algo/data-structure/serialize-and-deserialize-binary-tree/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1008. Construct Binary Search Tree from Preorder Traversal](https://leetcode.com/problems/construct-binary-search-tree-from-preorder-traversal/?show=1) | [1008. Construct Binary Search Tree from Preorder Traversal](https://leetcode.cn/problems/construct-binary-search-tree-from-preorder-traversal/?show=1) | 🟠 |
| - | [Sword to Offer 07. Reconstruct Binary Tree](https://leetcode.cn/problems/zhong-jian-er-cha-shu-lcof/?show=1) | 🟠 |
| - | [Sword to Offer 33. Postorder Traversal Sequence of a BST](https://leetcode.cn/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/?show=1) | 🟠 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
