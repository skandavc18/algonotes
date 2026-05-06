# Framework Thinking for Learning Data Structures and Algorithms

<p align='center'>
<a href="https://github.com/labuladong/fucking-algorithm" target="view_window"><img alt="GitHub" src="https://img.shields.io/github/stars/labuladong/fucking-algorithm?label=Stars&style=flat-square&logo=GitHub"></a>
<a href="https://labuladong.online/algo/" target="_blank"><img class="my_header_icon" src="https://img.shields.io/static/v1?label=Premium%20Course&message=View&color=pink&style=flat"></a>
<a href="https://www.zhihu.com/people/labuladong"><img src="https://img.shields.io/badge/Zhihu-@labuladong-000000.svg?style=flat-square&logo=Zhihu"></a>
<a href="https://space.bilibili.com/14089380"><img src="https://img.shields.io/badge/Bilibili-@labuladong-000000.svg?style=flat-square&logo=Bilibili"></a>
</p>

![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: [the new website's membership](https://labuladong.online/algo/intro/site-vip/) is about to increase in price; renewals for existing users are supported. It's also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



**-----------**

> tip: a video version is available: [Framework Thinking for Learning DS&A](https://www.bilibili.com/video/BV1EN4y1M79p/). Follow my Bilibili account; I'll guide you through more difficult algorithm techniques on video.



This is a revised version of an older article. The original was well-received; if you haven't read it, no worries — this article covers everything plus many code examples.

First, this is about ordinary data structures — we're not in competitive programming. The goal is to rapidly build algorithm intuition; no need for obscure topics. The following are my personal experiences, not what algorithm books would write — try to get my angle and don't fixate on details. The point is to build a framework-level understanding.

Top-down, from abstract to concrete framework thinking is universal — useful for any subject, not just algorithms.

### 1. How Data Structures Are Stored

**There are only two storage forms: arrays (sequential) and linked lists (linked).**

What about hash tables, stacks, queues, heaps, trees, graphs?

We must think recursively: top-down, abstract to concrete. Those are "upper structures"; arrays and linked lists are the foundation. The variety of structures all reduce to special operations on arrays or linked lists with different APIs.

E.g. queues and stacks can be implemented either way. Arrays require resizing; linked lists need extra memory for pointers.

A graph's two representations: adjacency list (linked) and adjacency matrix (2D array). Matrices give fast connectivity checks and matrix algebra but waste space on sparse graphs. Lists save space but lose performance for some operations.

A hash table maps keys via a hash function into a big array. For collisions, chaining uses linked lists (simple, extra space for pointers); linear probing uses contiguous addressing in arrays (no pointer space, slightly more complex operations).

A "tree" implemented with arrays is a "heap" — a complete binary tree. With pointers we get the usual trees that aren't necessarily complete; on top of these we built clever variants like BSTs, AVL trees, red-black trees, interval trees, B-trees, etc.

If you know Redis: lists, strings, sets — each with at least two underlying storage forms, chosen based on actual data.

So data structures are many; you can invent your own. But the underlying storage is arrays or linked lists. **Pros and cons:**

**Arrays** are compactly stored, support random access via indices, and are space-efficient. But contiguous storage means resize requires reallocating + copying — O(N). Inserting/removing in the middle requires shifting — O(N).

**Linked lists** don't need resizing because elements aren't contiguous; they're connected by pointers. Given a node's previous and next, insertion and deletion are O(1). But you can't do random access by index, and pointers cost extra memory.

### 2. Basic Operations on Data Structures

For any data structure, basic operations are traversal + access — concretely, insert/delete/search/update.

**Many structures, but they all exist to perform insert/delete/search/update efficiently for different scenarios.** That's their purpose.

How do we traverse + access? At the highest level, traversals are linear or non-linear.

Linear: for/while iteration. Non-linear: recursion. More concretely, the frameworks below.

Array traversal (linear):

```java
void traverse(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        // Iteratively visit arr[i]
    }
}
```

Linked-list traversal (iterative or recursive):

```java
/* A basic singly linked list node */
class ListNode {
    int val;
    ListNode next;
}

void traverse(ListNode head) {
    for (ListNode p = head; p != null; p = p.next) {
        // Iteratively visit p.val
    }
}

void traverse(ListNode head) {
    // Recursively visit head.val
    traverse(head.next);
}
```

Binary tree traversal (recursive):

```java
/* A basic binary tree node */
class TreeNode {
    int val;
    TreeNode left, right;
}

void traverse(TreeNode root) {
    traverse(root.left);
    traverse(root.right);
}
```

Look at the binary-tree recursion — similar to linked-list recursion, no? And look at the binary-tree structure — similar to a linked list, no? With more branches you get an N-ary tree:

```java
/* A basic N-ary tree node */
class TreeNode {
    int val;
    TreeNode[] children;
}

void traverse(TreeNode root) {
    for (TreeNode child : root.children)
        traverse(child);
}
```

N-ary tree traversal generalizes to graph traversal — a graph is essentially a union of trees. What about cycles? Use a `visited` array — easy.

**A "framework" is a pattern. Insert/delete/search/update can't escape these structures**; treat them as the outline and add code per problem.

### 3. Practice Plan

First, data structures are tools; algorithms use the right tools to solve problems. So learn the common structures and their characteristics first.

My recommended order:

**1. Start with basic structures' common algorithms** — linked-list reversal, prefix sums, binary search, etc.

These are easy when you know them and develop a taste for algorithms.

**2. Don't rush into backtracking and DP. Do binary trees first. Binary trees first. Binary trees first.**

::: tip Tip

LeetCode has a binary-tree tag:

[https://leetcode.cn/tag/binary-tree/](https://leetcode.cn/tag/binary-tree/)

:::

This is from years of practice — here's my early submission screenshot:

![](https://labuladong.online/algo/images/others/leetcode.jpeg)

The data shows most readers care about DP/backtracking/divide-and-conquer rather than data-structure algorithms. Why binary trees? **Because binary trees are the easiest way to develop framework thinking, and all recursive algorithm techniques are essentially tree traversal.**

If you have no idea where to start with a binary-tree problem, you probably don't yet understand the "framework".

**Don't underestimate these few lines — almost every binary-tree problem reduces to this:**

```java
void traverse(TreeNode root) {
    // Preorder
    traverse(root.left);
    // Inorder
    traverse(root.right);
    // Postorder
}
```

Some examples — ignore the details, just see the framework.

LeetCode 124 (Hard): max path sum in a binary tree:

```java
int res = Integer.MIN_VALUE;
int oneSideMax(TreeNode root) {
    if (root == null) return 0;
    int left = max(0, oneSideMax(root.left));
    int right = max(0, oneSideMax(root.right));
    // Postorder
    res = Math.max(res, left + right + root.val);
    return Math.max(left, right) + root.val;
}
```

It's a postorder traversal — `traverse` renamed to `oneSideMax`.

LeetCode 105 (Medium): build a tree from preorder and inorder:

```java
TreeNode build(int[] preorder, int preStart, int preEnd, 
               int[] inorder, int inStart, int inEnd) {
    // Preorder: find indices for left/right subtrees
    if (preStart > preEnd) {
        return null;
    }
    int rootVal = preorder[preStart];
    int index = 0;
    for (int i = inStart; i <= inEnd; i++) {
        if (inorder[i] == rootVal) {
            index = i;
            break;
        }
    }
    int leftSize = index - inStart;
    TreeNode root = new TreeNode(rootVal);

    // Recursively build subtrees
    root.left = build(preorder, preStart + 1, preStart + leftSize,
                      inorder, inStart, index - 1);
    root.right = build(preorder, preStart + leftSize + 1, preEnd,
                       inorder, index + 1, inEnd);
    return root;
}
```

Lots of parameters but only to control indices. The recursive call is at preorder — this is preorder traversal with extra logic.

LeetCode 230 (Medium): k-th smallest in a BST:

```java
int res = 0;
int rank = 0;
void traverse(TreeNode root, int k) {
    if (root == null) {
        return;
    }
    traverse(root.left, k);
    /* Inorder position */
    rank++;
    if (k == rank) {
        res = root.val;
        return;
    }
    /*****************/
    traverse(root.right, k);
}
```

Inorder traversal — for a BST it's an obvious choice.

So binary-tree problems are nothing more than: write the framework and add code at the right position.

For someone who understands binary trees, a problem doesn't take long. If you're stuck or scared, start with binary trees — the first 10 may hurt; combine them with the framework on 20 more, you'll start to see the pattern; finish the topic and you'll find **anything involving recursion is a tree problem**.

::: tip

The site's [Binary Tree Practice section](https://labuladong.online/algo/problem-set/binary-tree-traverse-1/) walks through 150 binary-tree problems with a fixed approach.

:::

A few earlier examples:

[DP detailed](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/) covered the coin change problem; the brute force is N-ary tree traversal:

![](https://labuladong.online/algo/images/dp-detailed进阶/5.jpg)

```java
int dp(int[] coins, int amount) {
    // base case
    if (amount == 0) return 0;
    if (amount < 0) return -1;

    int res = Integer.MAX_VALUE;
    for (int coin : coins) {
        int subProblem = dp(coins, amount - coin);
        // Skip if no solution to subproblem
        if (subProblem == -1) continue;
        // Take the optimal subproblem result, add 1
        res = Math.min(res, subProblem + 1);
    }
    return res == Integer.MAX_VALUE ? -1 : res;
}
```

If the code is too much, extract the framework:

```python
# Just an N-ary tree traversal
int dp(int amount) {
    for (int coin : coins) {
        dp(amount - coin);
    }
}
```

Many DP problems are tree traversals. With tree traversal at your fingertips, translating ideas to code (or extracting others' core ideas) is straightforward.

Backtracking — as covered in [Backtracking Framework](https://labuladong.online/algo/essential-technique/backtrack-framework/) — is N-ary tree pre/post-order traversal, no exceptions.

For example, full permutations: traversing a tree where leaf paths are permutations:

![](https://labuladong.online/algo/images/backtracking/1.jpg)

Code:

```java
void backtrack(int[] nums, LinkedList<Integer> track) {
    if (track.size() == nums.length) {
        res.add(new LinkedList(track));
        return;
    }
    
    for (int i = 0; i < nums.length; i++) {
        if (track.contains(nums[i]))
            continue;
        track.add(nums[i]);
        // Recurse to next decision level
        backtrack(nums, track);
        track.removeLast();
    }
}
```

Confused? Extract the recursion:

```java
/* Extracted N-ary tree traversal framework */
void backtrack(int[] nums, LinkedList<Integer> track) {
    for (int i = 0; i < nums.length; i++) {
        backtrack(nums, track);
}
```

N-ary tree traversal again. Important?

**For those afraid of algorithms: start with tree problems and view them through a framework lens, not the details.**

Detail-fixation is, e.g., agonizing over `i < n` vs. `i < n - 1` or whether the array length should be `n` or `n + 1`.

Framework view: extract and extend from the framework, both for understanding others' solutions and for finding your own direction.

Detail mistakes lead to wrong answers, but with the framework you can't go too wrong — your direction is right.

Without the framework you can't solve the problem, and even with the answer in front of you, you'd miss that it's a tree problem.

This kind of thinking matters. The state-transition steps in [DP detailed](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/) sometimes produce solutions you can't justify but that work...

**That's the power of frameworks: even half-asleep you can write correct programs; even when you don't know anything, you're a level above someone without one.**

Wrap-up:

Storage: linked or sequential. Operations: insert/delete/search/update. Traversal: iteration or recursion.

After mastering the basics, do binary trees first. With framework thinking, understand trees deeply, then tackle backtracking, DP, divide-and-conquer — your understanding will be much deeper.





<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Dijkstra Template and Applications](https://labuladong.online/algo/data-structure/dijkstra/)
 - [Sweeping All Island Problems](https://labuladong.online/algo/frequency-interview/island-dfs-summary/)
 - [Binary Tree Tactics (Serialization)](https://labuladong.online/algo/data-structure/serialize-and-deserialize-binary-tree/)
 - [Binary Tree Tactics (Outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
 - [Bipartite Detection](https://labuladong.online/algo/data-structure/bipartite-graph/)
 - [Binary Heap Basics](https://labuladong.online/algo/data-structure-basic/binary-heap-basic/)
 - [Trie Template Sweeps Five Problems](https://labuladong.online/algo/data-structure/trie/)
 - [Backtracking Sweeps All Permutation/Combination/Subset Problems](https://labuladong.online/algo/essential-technique/permutation-combination-subset-all-in-one/)
 - [Backtracking Framework](https://labuladong.online/algo/essential-technique/backtrack-framework/)
 - [Graph Theory Basics and Traversal](https://labuladong.online/algo/data-structure/graph-traverse/)
 - [Reverse a Linked List in k-Groups](https://labuladong.online/algo/data-structure/reverse-nodes-in-k-group/)
 - [Determining a Palindrome Linked List](https://labuladong.online/algo/data-structure/palindrome-linked-list/)
 - [Merge Sort Details and Applications](https://labuladong.online/algo/practice-in-action/merge-sort/)
 - [My Practice Insights: The Essence of Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Array (Sequential Storage) Basics](https://labuladong.online/algo/data-structure-basic/array-basic/)
 - [Storage Systems: LSM Tree Design Principles](https://labuladong.online/algo/other-skills/lsm-tree/)
 - [Cycle Detection and Topological Sort](https://labuladong.online/algo/data-structure/topological-sort/)
 - [Iterative Tree Traversal Using a Stack](https://labuladong.online/algo/data-structure/iterative-traversal-binary-tree/)
 - [Implementing Queues/Stacks with Linked Lists](https://labuladong.online/algo/data-structure-basic/linked-queue-stack/)
 - [Target Sum: A Knapsack Variant](https://labuladong.online/algo/dynamic-programming/target-sum/)
 - [Algorithm Practice and Flow](https://labuladong.online/algo/other-skills/hert-flow/)
 - [Practical Guide to Time/Space Complexity Analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/)
 - [When the Problem Says Don't, Do It Anyway](https://labuladong.online/algo/data-structure/flatten-nested-list-iterator/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou |
| :----: | :----: |
| [341. Flatten Nested List Iterator](https://leetcode.com/problems/flatten-nested-list-iterator/?show=1) | [341. Flatten Nested List Iterator](https://leetcode.cn/problems/flatten-nested-list-iterator/?show=1) |
| [589. N-ary Tree Preorder Traversal](https://leetcode.com/problems/n-ary-tree-preorder-traversal/?show=1) | [589. N-ary Tree Preorder Traversal](https://leetcode.cn/problems/n-ary-tree-preorder-traversal/?show=1) |
| [590. N-ary Tree Postorder Traversal](https://leetcode.com/problems/n-ary-tree-postorder-traversal/?show=1) | [590. N-ary Tree Postorder Traversal](https://leetcode.cn/problems/n-ary-tree-postorder-traversal/?show=1) |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**

**"labuladong's Algorithm Notes" has been published. Follow the official account for details; reply with "all-in-one bundle" to download the companion PDF and the full problem-solving bundle**:

![](https://labuladong.online/algo/images/souyisou2.png)

====== Code in Other Languages ======
