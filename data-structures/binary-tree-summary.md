# Core Outline of the Binary-Tree Algorithm Series


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/) | [104. Maximum Depth of Binary Tree](https://leetcode.cn/problems/maximum-depth-of-binary-tree/) | 🟢 |
| [144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/) | [144. Binary Tree Preorder Traversal](https://leetcode.cn/problems/binary-tree-preorder-traversal/) | 🟢 |
| [543. Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/) | [543. Diameter of Binary Tree](https://leetcode.cn/problems/diameter-of-binary-tree/) | 🟢 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Binary Tree Structure Basics](https://labuladong.online/algo/data-structure-basic/binary-tree-basic/)
> - [DFS/BFS Traversal of Binary Trees](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/)

> [!IMPORTANT]
> This article abstracts and generalizes many algorithms, so it contains many cross-links.
> 
> **First-time readers should not study this article via DFS-of-the-links — when you hit an algorithm you haven't seen, just skim past it. Walk away with an impression of the framework presented here.** Once you have studied later chapters, you'll naturally appreciate this article and you can come back for a deeper read.

The structure of every article on this site follows the framework proposed in [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/). The importance of binary-tree problems is heavily emphasized there, so this article belongs in the must-read list of Chapter 1.

After many years of practice, I distilled a binary-tree outline. The terminology may not be especially academic and you won't find these summaries in textbooks, but I have not seen a binary-tree problem on any platform that escapes this framework. If you find one, please let me know.

To begin, two thinking modes for binary-tree problems:

**1. Can the answer be obtained by traversing the tree once?** If so, use a `traverse` function with external variables — the "traversal" mindset.

**2. Can a recursive function be defined that derives the original problem's answer from sub-problems' (subtrees') answers?** If so, write the recursive definition and exploit its return value — the "decomposition" mindset.

Either way, ask:

**What does an individual node need to do, and at what point (preorder / inorder / postorder) should it do it?** Don't worry about the rest — recursion handles every node.

The examples are the simplest possible, so don't worry about getting lost. From these tiny problems we'll extract every binary-tree commonality, then transfer the binary-tree mindset over to [Dynamic Programming](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/), [Backtracking](https://labuladong.online/algo/essential-technique/backtrack-framework/), [Divide and Conquer](https://labuladong.online/algo/essential-technique/divide-and-conquer/), and [Graph Algorithms](https://labuladong.online/algo/data-structure-basic/graph-basic/) — which is exactly why I keep emphasizing framework thinking. After learning those advanced topics, come back to this article — you'll see them with fresh eyes.

First, let me reiterate the importance of binary trees and their algorithms.


## Why Binary Trees Matter

Take two classic sorts: [Quicksort](https://labuladong.online/algo/practice-in-action/quick-sort/) and [Merge Sort](https://labuladong.online/algo/practice-in-action/merge-sort/). What's your understanding of them?

**If you tell me that quicksort is a binary-tree preorder traversal and merge sort is a binary-tree postorder traversal, I'll know you're an algorithm pro.**

Why are these sorts related to binary trees? Briefly:

Quicksort: to sort `nums[lo..hi]`, find a pivot `p`, swap so `nums[lo..p-1] <= nums[p]` and `nums[p+1..hi] > nums[p]`, then recursively find new pivots in `nums[lo..p-1]` and `nums[p+1..hi]`.

Quicksort framework:

```java
void sort(int[] nums, int lo, int hi) {
    // ****** Preorder ******
    // Construct the pivot p via swaps
    int p = partition(nums, lo, hi);
    // ************************

    sort(nums, lo, p - 1);
    sort(nums, p + 1, hi);
}
```

Construct the pivot first, then recurse on left/right — that's binary-tree preorder traversal!

Merge sort: to sort `nums[lo..hi]`, sort `nums[lo..mid]`, sort `nums[mid+1..hi]`, then merge.

Merge-sort framework:

```java
// Definition: sort nums[lo..hi]
void sort(int[] nums, int lo, int hi) {
    int mid = (lo + hi) / 2;
    // Sort nums[lo..mid]
    sort(nums, lo, mid);
    // Sort nums[mid+1..hi]
    sort(nums, mid + 1, hi);

    // ****** Postorder ******
    // Merge nums[lo..mid] and nums[mid+1..hi]
    merge(nums, lo, mid, hi);
    // *********************
}
```

Sort subtrees first, then merge (similar to merging sorted lists) — that's binary-tree postorder! And it's also classic divide-and-conquer.

If you can spot these patterns immediately, do you still need to memorize sorts? No — you can derive them from the binary-tree traversal framework.

The point: binary-tree thinking is broadly applicable. Anything involving recursion can be modeled as a binary-tree problem.

Next we explore the three traversal orders to truly appreciate the structure.

## Deeper into Pre-, In-, and Postorder

Three questions; pause and think for 30 seconds:

1. What do pre-, in-, postorder actually mean? Are they just three differently-ordered lists?

2. What's special about postorder?

3. Why don't multiway trees have an inorder traversal?

If you're stumped, your understanding is at the textbook level. Let me explain my view.

Recall the recursive traversal framework from [DFS/BFS Binary Tree Traversal](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/):

```java
void traverse(TreeNode root) {
    if (root == null) {
        return;
    }
    // Preorder
    traverse(root.left);
    // Inorder
    traverse(root.right);
    // Postorder
}
```

Forget pre/in/post for a moment. What does `traverse` actually do?

It visits every node, no different in essence from iterating over an array or linked list:

```java
// Iterative array traversal
void traverse(int[] arr) {
    for (int i = 0; i < arr.length; i++) {

    }
}

// Recursive array traversal
void traverse(int[] arr, int i) {
    if (i == arr.length) {
        return;
    }
    // Preorder
    traverse(arr, i + 1);
    // Postorder
}

// Iterative singly linked list traversal
void traverse(ListNode head) {
    for (ListNode p = head; p != null; p = p.next) {

    }
}

// Recursive singly linked list traversal
void traverse(ListNode head) {
    if (head == null) {
        return;
    }
    // Preorder
    traverse(head.next);
    // Postorder
}
```

Lists and arrays can be iterated either way; **a binary tree is essentially a binary linked list**. There is no simple iterative for-loop form, so we usually traverse trees recursively.

Recursive traversals naturally have preorder and postorder positions — before and after the recursive calls.

**Preorder = "just entered a node"; postorder = "about to leave a node"**. Where you place code matters:

![](https://labuladong.online/algo/images/binary-tree-summary/1.jpeg)

For example, if you wanted to **print a singly linked list in reverse** order:

There are many ways, but if you understand recursion you can use postorder:

```java
// Recursive list traversal printing in reverse
void traverse(ListNode head) {
    if (head == null) {
        return;
    }
    traverse(head.next);
    // Postorder
    print(head.val);
}
```

Combined with the figure, you can see why this prints in reverse — the recursion stack inverts the order.

Same for binary trees, with one extra "inorder" position.

Textbooks only ask "what is the pre/in/postorder result?", so a graduate of just one data-structures course probably believes pre/in/postorder are just three differently-ordered `List<Integer>`s.

But: **pre-, in-, postorder are three special points in time when traversing the tree, not three differently-ordered lists**:

Preorder code runs when entering a node;

Postorder code runs when about to leave;

Inorder code runs after the left subtree has been visited and before the right subtree begins.

Note the wording — "preorder/inorder/postorder positions" — to distinguish from the common "preorder/inorder/postorder traversal results". You can put code at the preorder position to push elements into a list (and thus produce the preorder traversal), but you can also write more complex code to do other things.

Visually, the three positions on a tree look like:

![](https://labuladong.online/algo/images/binary-tree-summary/2.jpeg)

**Each node has its own "unique" pre/in/postorder positions**, which is why I call them three special points in time during traversal.

This also explains why multiway trees have no inorder: a binary tree has exactly one switch from left subtree to right subtree, while a multiway tree has many children and many switches — so there is no single "inorder" point.

The big idea:

**Every binary-tree problem boils down to placing clever code at the pre/in/postorder positions to achieve your goal. You only need to think about what each node should do; everything else is taken care of by the recursion framework.**

In [Graph Algorithm Basics](https://labuladong.online/algo/data-structure-basic/graph-basic/) we extend the binary-tree traversal to graphs and build many classic graph algorithms on it — but that's another story.


## Two Solving Mindsets

In [My Algorithm Practice Insights](https://labuladong.online/algo/essential-technique/algorithm-summary/) I said:

**Recursive solutions to binary-tree problems split into two mindsets: traverse the tree once, or compute the answer by decomposing the problem. These correspond to the [Backtracking Framework](https://labuladong.online/algo/essential-technique/backtrack-framework/) and the [Dynamic Programming Framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/), respectively.**

> [!TIP]
> A note on my function-naming conventions: the "traverse" style is `void traverse(...)` with no return value, updating an external variable; the "decomposition" style is named according to its specific function and typically returns a value (the answer to the subproblem).
> 
> Correspondingly, in [Backtracking Framework](https://labuladong.online/algo/essential-technique/backtrack-framework/) the function is `void backtrack(...)`, while in [Dynamic Programming Framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/) it's a `dp` function with a return value. This emphasizes the deep ties between binary trees, backtracking, and DP.
> 
> Naming conventions aren't strictly required, but I recommend following them — they make your function's role and your problem-solving mindset clearer.

I previously used the maximum-depth problem to compare these two ideas with backtracking and DP. Here we focus on how each solves binary-tree problems.

LeetCode 104 "Maximum Depth of Binary Tree" — the depth is the number of nodes on the longest path from root to a "farthest" leaf. For the tree below the answer is 3:

![](https://labuladong.online/algo/images/binary-tree-summary/tree.jpg)

How would you solve this? Traverse the tree, track each node's depth in an external variable, take the max — **the traversal mindset**.

Code:

```java
class Solution {
    // Records the maximum depth
    int res = 0;

    // Records the depth of the current node during traversal
    int depth = 0;

    public int maxDepth(TreeNode root) {
        traverse(root);
        return res;
    }

    // Tree-traversal framework
    void traverse(TreeNode root) {
        if (root == null) {
            return;
        }
        // Preorder
        depth++;
        if (root.left == null && root.right == null) {
            // Reached a leaf; update max depth
            res = Math.max(res, depth);
        }
        traverse(root.left);
        traverse(root.right);
        // Postorder
        depth--;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/mydata-maxdepth1/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Animated Code Visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>

Why increment `depth` at preorder and decrement at postorder?

Because preorder is "entering a node" and postorder is "leaving a node"; `depth` is the depth of the current recursion. Treat `traverse` as a pointer walking the tree.

`res` can be updated at any of the three positions, so long as it's after the increment and before the decrement.

You can also see that the maximum depth of a tree can be derived from those of its subtrees — **the decomposition mindset**.

Code:

```java
class Solution {
    // Definition: given a root, return the max depth of the tree
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        // Use the definition to compute subtree max depths
        int leftMax = maxDepth(root.left);
        int rightMax = maxDepth(root.right);
        // The max depth equals the larger of the two subtree depths plus 1
        int res = Math.max(leftMax, rightMax) + 1;

        return res;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/mydata-maxdepth2/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Animated Code Visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>

If you understand the function definition, this one is easy too. Why is the main logic in postorder?

Because the answer depends on the subtrees' answers; we must first compute them, then combine. So the main work is in postorder.

With both mindsets clear, **let's revisit the basic preorder traversal** — LeetCode 144 "Binary Tree Preorder Traversal".

The familiar "traversal" version:

```java
class Solution {
    // Stores the preorder result
    List<Integer> res = new LinkedList<>();

    // Returns the preorder result
    public List<Integer> preorderTraversal(TreeNode root) {
        traverse(root);
        return res;
    }

    // Tree-traversal function
    void traverse(TreeNode root) {
        if (root == null) {
            return;
        }
        // Preorder
        res.add(root.val);
        traverse(root.left);
        traverse(root.right);
    }
}
```

Can you do it via "decomposition"?

That is — without a `traverse` helper or external variables, just using the given `preorderTraverse` recursively?

Preorder's structure: root first, then left subtree's preorder result, then right subtree's preorder result:

![](https://labuladong.online/algo/images/binary-tree-summary/3.jpeg)

So **a tree's preorder result = root + left subtree's preorder result + right subtree's preorder result**.

Code:

```java
class Solution {
    // Definition: given the root, return the preorder traversal of this tree
    List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new LinkedList<>();
        if (root == null) {
            return res;
        }
        // root.val comes first
        res.add(root.val);
        // Then left subtree's preorder
        res.addAll(preorderTraversal(root.left));
        // Then right subtree's preorder
        res.addAll(preorderTraversal(root.right));
        return res;
    }
}
```

Inorder and postorder are similar — just place `add(root.val)` at the corresponding position.

This solution is concise. Why isn't it more common?

Because **its complexity is hard to control** — it depends on the language. In Java, `addAll` is O(N) for both ArrayList and LinkedList, so the worst-case total is O(N^2), unless you implement an O(1) `addAll` (possible with linked lists via pointer manipulation).

The other reason: textbooks just don't teach it this way…

Two simple examples, but many binary-tree problems can be solved by either mindset. Practice and reflect; don't settle for the only solution you know.

The general workflow when meeting a binary-tree problem:

**1. Can the answer come from traversing the tree once?** If yes, use `traverse` + external variables.

**2. Can it be defined recursively from subtree answers?** If yes, define a recursive function and use its return value.

**3. Either way, decide what each node should do and at what point (pre/in/post).**

The site's [Recursion Practice Set](https://labuladong.online/algo/intro/binary-tree-practice/) lists 100+ binary-tree exercises walking you through both mindsets to fully internalize recursive thinking.


## Why Postorder is Special

Briefly on preorder/inorder.

Preorder has no special property by itself — many algorithms place code at preorder simply because the code doesn't care about position.

Inorder is mainly used for BSTs; you can think of BST inorder as iterating a sorted array.

> [!IMPORTANT]
> **Carefully observe: the three positions are increasingly powerful.**
> 
> Preorder code can only access data passed in via the function parameters from the parent.
> 
> Inorder code can access parameter data plus data returned from the left subtree.
> 
> Postorder code is the most powerful: it can access parameter data plus data returned from both left and right subtrees.
> 
> So sometimes moving code to postorder is most efficient; certain things can only be done at postorder.

Two simple questions to feel the difference:

1. If the root is at level 1, how do you print each node's level?

2. How do you print, for each node, the number of nodes in its left and right subtrees?

For (1):

```java
// Tree traversal function
void traverse(TreeNode root, int level) {
    if (root == null) {
        return;
    }
    // Preorder
    printf("Node %s at level %d", root.val, level);
    traverse(root.left, level + 1);
    traverse(root.right, level + 1);
}

// Call as
traverse(root, 1);
```

For (2):

```java
// Definition: given a tree, return the total node count
int count(TreeNode root) {
    if (root == null) {
        return 0;
    }
    int leftCount = count(root.left);
    int rightCount = count(root.right);
    // Postorder
    printf("Node %s has %d nodes in its left subtree and %d in its right",
            root, leftCount, rightCount);

    return leftCount + rightCount + 1;
}
```

> [!NOTE]
> A node's depth can be tracked along the way via a recursion parameter; but the size of the subtree rooted at a node is only known after fully traversing the subtree, via the recursive return value.
> 
> Note: only postorder lets you read subtree info via return values.
> 
> **In other words, if a problem involves subtrees, you'll likely set a return value and put logic at postorder.**

Now an actual example: LeetCode 543 "Diameter of Binary Tree" — find the longest diameter.

The "diameter" is the longest path between any two nodes; it doesn't have to pass through the root. Example:

![](https://labuladong.online/algo/images/binary-tree-summary/tree1.png)

Diameter 3, e.g. `[4,2,1,3]`, `[4,2,1,9]`, or `[5,2,1,3]`.

**Key insight: each node's "diameter" through it equals the sum of its left and right subtrees' max depths.**

Direct approach: for each node, compute the diameter from its subtrees' depths; take the max:

```java
class Solution {
    // Records the max diameter
    int maxDiameter = 0;

    public int diameterOfBinaryTree(TreeNode root) {
        // Compute the diameter at each node
        traverse(root);
        return maxDiameter;
    }

    // Tree traversal
    void traverse(TreeNode root) {
        if (root == null) {
            return;
        }
        // Diameter at this node
        int leftMax = maxDepth(root.left);
        int rightMax = maxDepth(root.right);
        int myDiameter = leftMax + rightMax;
        maxDiameter = Math.max(maxDiameter, myDiameter);
        
        traverse(root.left);
        traverse(root.right);
    }

    // Compute max depth
    int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int leftMax = maxDepth(root.left);
        int rightMax = maxDepth(root.right);
        return 1 + Math.max(leftMax, rightMax);
    }
}
```

Correct, but slow: `traverse` visits each node and `maxDepth` traverses subtrees → worst-case O(N^2).

This is the problem we discussed: **preorder can't access subtree info, so we have to call `maxDepth` for each node**.

How to optimize? Move the diameter logic to postorder of `maxDepth`, where left and right subtrees' depths are known:

```java
class Solution {
    int maxDiameter = 0;

    public int diameterOfBinaryTree(TreeNode root) {
        maxDepth(root);
        return maxDiameter;
    }

    int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int leftMax = maxDepth(root.left);
        int rightMax = maxDepth(root.right);
        // Postorder: also compute the diameter
        int myDiameter = leftMax + rightMax;
        maxDiameter = Math.max(maxDiameter, myDiameter);

        return 1 + Math.max(leftMax, rightMax);
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/mydata-diameter-of-binary-tree/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>


Now it's O(N) — just `maxDepth`.

Echoing earlier: when you encounter a subtree problem, give the function a return value and use postorder.

> [!NOTE]
> Quiz: do problems using postorder use the "traversal" mindset or the "decomposition" mindset?


> [!NOTE]
> Postorder problems generally use the "decomposition" mindset, because the current node consumes information returned by subtrees — the original problem is broken into "current node + left/right subproblems".

Conversely, if you write a recursion-inside-recursion solution, ask yourself whether postorder can optimize it.

For more postorder problems see [Walk-Through: Binary Trees (Postorder)](https://labuladong.online/algo/data-structure/binary-tree-part3/), [Walk-Through: BST (Postorder)](https://labuladong.online/algo/data-structure/bst-part4/), and [[Practice] Postorder Position](https://labuladong.online/algo/problem-set/binary-tree-post-order-i/).


## DP / Backtracking / DFS — Binary-Tree-Style Distinctions and Connections

I've said DP and backtracking are two manifestations of binary-tree thinking. Sharp readers ask: you haven't really written a separate article on DFS — what about it?

I do use DFS in [Sweeping All Island Problems](https://labuladong.online/algo/frequency-interview/island-dfs-summary/), but I haven't written a dedicated DFS article — **DFS and backtracking are nearly identical, with one detail difference**.

The difference: where the "make/undo choice" lives — DFS puts it outside the for loop, backtracking puts it inside.

Why? Through the binary-tree lens:

> [!IMPORTANT]
> DP / DFS / backtracking can all be viewed as extensions of binary-tree problems, but with different focal points:
> 
> - DP follows the decomposition (divide-and-conquer) mindset — focus on the entire "subtree".
> - Backtracking follows the traversal mindset — focus on the "edges" between nodes.
> - DFS follows the traversal mindset — focus on a single "node".

Three quick examples make this clear.

### Example 1: Decomposition Mindset

Given a tree, count nodes (decomposition):

```java
// Definition: given a tree, return the total node count
int count(TreeNode root) {
    if (root == null) {
        return 0;
    }
    // The current node cares about its subtrees' sizes
    // because the original answer follows from them
    int leftCount = count(root.left);
    int rightCount = count(root.right);
    // Postorder: subtree sizes plus self = total size
    return leftCount + rightCount + 1;
}
```

**Decomposition focuses on a structurally identical subproblem — the "subtree".**

In actual DP problems, e.g. Fibonacci in [DP Framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/), focus is on subtree return values:

```java
int fib(int N) {
    if (N == 1 || N == 2) return 1;
    return fib(N - 1) + fib(N - 2);
}
```

![](https://labuladong.online/algo/images/dynamic-programming/2.jpg)


### Example 2: Backtracking Mindset

Given a tree, traverse it and print the steps:

```java
void traverse(TreeNode root) {
    if (root == null) return;
    printf("From node %s into node %s", root, root.left);
    traverse(root.left);
    printf("From node %s back to node %s", root.left, root);

    printf("From node %s into node %s", root, root.right);
    traverse(root.right);
    printf("From node %s back to node %s", root.right, root);
}
```

Generalizes to multiway trees similarly:

```java
// Multiway-tree node
class Node {
    int val;
    Node[] children;
}

void traverse(Node root) {
    if (root == null) return;
    for (Node child : root.children) {
        printf("From node %s into node %s", root, child);
        traverse(child);
        printf("From node %s back to node %s", child, root);
    }
}
```

This multiway-tree framework leads to the [Backtracking Framework](https://labuladong.online/algo/essential-technique/backtrack-framework/):

```java
// Backtracking framework
void backtrack(...) {
    // base case
    if (...) return;

    for (int i = 0; i < ...; i++) {
        // Make a choice
...

        // Recurse to next decision level
        backtrack(...);

        // Undo the choice
...
    }
}
```


**Backtracking traversal focuses on movement between nodes — the "edges".**

In real backtracking problems, e.g. permutations in [Permutation/Combination/Subset Sweep](https://labuladong.online/algo/essential-technique/permutation-combination-subset-all-in-one/), focus is on the edges:

```java
// Core backtracking code
void backtrack(int[] nums) {
    // Backtracking framework
    for (int i = 0; i < nums.length; i++) {
        // Make a choice
        used[i] = true;
        track.addLast(nums[i]);

        // Recurse
        backtrack(nums);

        // Undo
        track.removeLast();
        used[i] = false;
    }
}
```

![](https://labuladong.online/algo/images/permutation/2.jpeg)


### Example 3: DFS Mindset

Given a tree, increment every node's value:

```java
void traverse(TreeNode root) {
    if (root == null) return;
    // Increment each visited node
    root.val++;
    traverse(root.left);
    traverse(root.right);
}
```

**DFS traversal focuses on individual nodes — the "node".**

In real DFS problems, e.g. the early island problems in [Sweeping All Island Problems](https://labuladong.online/algo/frequency-interview/island-dfs-summary/), focus is on each grid cell:

```java
// Core DFS
void dfs(int[][] grid, int i, int j) {
    int m = grid.length, n = grid[0].length;
    if (i < 0 || j < 0 || i >= m || j >= n) {
        return;
    }
    if (grid[i][j] == 0) {
        return;
    }
    // Mark each visited cell as 0
    grid[i][j] = 0;
    dfs(grid, i + 1, j);
    dfs(grid, i, j + 1);
    dfs(grid, i - 1, j);
    dfs(grid, i, j - 1);
}
```

![](https://labuladong.online/algo/images/island/5.jpg)


DP focuses on the subtree, backtracking on the edge, DFS on the node.

Now you can see why backtracking puts "make/undo choice" inside the for loop and DFS outside:

```java
// DFS: make/undo choice OUTSIDE the for loop
void dfs(Node root) {
    if (root == null) return;
    // Make choice
    print("enter node %s", root);
    for (Node child : root.children) {
        dfs(child);
    }
    // Undo choice
    print("leave node %s", root);
}

// Backtracking: make/undo choice INSIDE the for loop
void backtrack(Node root) {
    if (root == null) return;
    for (Node child : root.children) {
        // Make choice
        print("I'm on the branch from %s to %s", root, child);
        backtrack(child);
        // Undo choice
        print("I'll leave the branch from %s to %s", child, root);
    }
}
```

For backtracking, the choice operations must be inside the for loop — otherwise you can't access the two endpoints of the "edge".

## Level-Order Traversal

Binary-tree problems mainly train recursive thinking; level-order traversal is iterative and simple. The framework:

```java
// Given the root, perform level-order traversal
void levelTraverse(TreeNode root) {
    if (root == null) return;
    Queue<TreeNode> q = new LinkedList<>();
    q.offer(root);

    // Top-down through every level
    while (!q.isEmpty()) {
        int sz = q.size();
        // Left-to-right at each level
        for (int i = 0; i < sz; i++) {
            TreeNode cur = q.poll();
            // Enqueue next level's nodes
            if (cur.left != null) {
                q.offer(cur.left);
            }
            if (cur.right != null) {
                q.offer(cur.right);
            }
        }
    }
}
```

The `while` handles top-down; the `for` handles left-to-right:

![](https://labuladong.online/algo/images/dijkstra/1.jpeg)

[BFS Framework](https://labuladong.online/algo/essential-technique/bfs-framework/) extends from level-order traversal and is commonly used for **shortest path in unweighted graphs**.

The framework can be tweaked: when you don't need to track level (steps), drop the `for`. See [Dijkstra](https://labuladong.online/algo/data-structure/dijkstra/) for shortest paths in weighted graphs as a BFS extension.

Worth noting: some problems that obviously call for level-order can also be solved by recursion — more techniques, more practice on pre/in/postorder.

This article is long enough. Around the three positions, we've covered binary-tree patterns thoroughly. How much you can apply depends on your own practice and reflection.

Explore as many solutions as possible. Once you grasp this fundamental data structure, learning advanced algorithms becomes much easier — you'll close the loop (pun intended).

Finally, the [Recursion Practice Set](https://labuladong.online/algo/intro/binary-tree-practice/) walks you through applying these techniques.

## Answering Reader Comments

A note on level-order traversal (and the [BFS framework](https://labuladong.online/algo/essential-technique/bfs-framework/)).

If you know binary trees well, you can produce level-order via recursion in many ways, e.g.:

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();

    public List<List<Integer>> levelTraverse(TreeNode root) {
        if (root == null) {
            return res;
        }
        // Treat root as level 0
        traverse(root, 0);
        return res;
    }

    void traverse(TreeNode root, int depth) {
        if (root == null) {
            return;
        }
        // Preorder: ensure depth-th level exists
        if (res.size() <= depth) {
            // First time at depth
            res.add(new LinkedList<>());
        }
        // Preorder: append root.val at depth
        res.get(depth).add(root.val);
        traverse(root.left, depth + 1);
        traverse(root.right, depth + 1);
    }
}
```

Result-wise this gives a level-order layout, but it's actually a preorder DFS — it works because preorder visits top-down, left-to-right.

**It's a "column-order" traversal (left-to-right) rather than top-down level order.** For shortest-distance scenarios, this is just DFS — no BFS performance advantage.

A reader contributed another recursive level-order:

```java
class Solution {

    List<List<Integer>> res = new LinkedList<>();

    public List<List<Integer>> levelTraverse(TreeNode root) {
        if (root == null) {
            return res;
        }
        List<TreeNode> nodes = new LinkedList<>();
        nodes.add(root);
        traverse(nodes);
        return res;
    }

    void traverse(List<TreeNode> curLevelNodes) {
        // base case
        if (curLevelNodes.isEmpty()) {
            return;
        }
        // Preorder: compute current level values and next level's nodes
        List<Integer> nodeValues = new LinkedList<>();
        List<TreeNode> nextLevelNodes = new LinkedList<>();
        for (TreeNode node : curLevelNodes) {
            nodeValues.add(node.val);
            if (node.left != null) {
                nextLevelNodes.add(node.left);
            }
            if (node.right != null) {
                nextLevelNodes.add(node.right);
            }
        }
        // Adding at preorder position gives top-down level order
        res.add(nodeValues);
        traverse(nextLevelNodes);
        // Adding at postorder position gives bottom-up level order
        // res.add(nodeValues);
    }
}
```

This `traverse` resembles a recursive list traversal — each level is treated as a "node" in a list.

Compared to the previous, this is genuine top-down level order — closer to BFS spirit. A nice recursive BFS extension.


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Trie / Prefix Tree Implementation](https://labuladong.online/algo/data-structure/trie-implement/)
 - [[Practice] Classic BFS Problems II](https://labuladong.online/algo/problem-set/bfs-ii/)
 - [[Practice] Classic BST Problems I](https://labuladong.online/algo/problem-set/bst1/)
 - [[Practice] Classic BST Problems II](https://labuladong.online/algo/problem-set/bst2/)
 - [[Practice] Postorder Position I](https://labuladong.online/algo/problem-set/binary-tree-post-order-i/)
 - [[Practice] Postorder Position II](https://labuladong.online/algo/problem-set/binary-tree-post-order-ii/)
 - [[Practice] Postorder Position III](https://labuladong.online/algo/problem-set/binary-tree-post-order-iii/)
 - [[Practice] Combining Both Mindsets](https://labuladong.online/algo/problem-set/binary-tree-combine-two-view/)
 - [[Practice] Hash Table Problems](https://labuladong.online/algo/problem-set/hash-table/)
 - [[Practice] Classic Backtracking Problems II](https://labuladong.online/algo/problem-set/backtrack-ii/)
 - [[Practice] Classic Backtracking Problems III](https://labuladong.online/algo/problem-set/backtrack-iii/)
 - [[Practice] Decomposition Mindset I](https://labuladong.online/algo/problem-set/binary-tree-divide-i/)
 - [[Practice] Decomposition Mindset II](https://labuladong.online/algo/problem-set/binary-tree-divide-ii/)
 - [[Practice] Traversal Mindset I](https://labuladong.online/algo/problem-set/binary-tree-traverse-i/)
 - [[Practice] Traversal Mindset II](https://labuladong.online/algo/problem-set/binary-tree-traverse-ii/)
 - [[Practice] Traversal Mindset III](https://labuladong.online/algo/problem-set/binary-tree-traverse-iii/)
 - [[Practice] Level-Order Traversal I](https://labuladong.online/algo/problem-set/binary-tree-level-i/)
 - [[Practice] Level-Order Traversal II](https://labuladong.online/algo/problem-set/binary-tree-level-ii/)
 - [One Method to Sweep LeetCode House Robber Problems](https://labuladong.online/algo/dynamic-programming/house-robber/)
 - [Sweeping All Island Problems](https://labuladong.online/algo/frequency-interview/island-dfs-summary/)
 - [BST Tactics (Postorder)](https://labuladong.online/algo/data-structure/bst-part4/)
 - [Binary Tree Tactics (Postorder)](https://labuladong.online/algo/data-structure/binary-tree-part3/)
 - [Binary Tree Tactics (Serialization)](https://labuladong.online/algo/data-structure/serialize-and-deserialize-binary-tree/)
 - [Binary Tree Tactics (Approach)](https://labuladong.online/algo/data-structure/binary-tree-part1/)
 - [Binary Tree Tactics (Construction)](https://labuladong.online/algo/data-structure/binary-tree-part2/)
 - [Recursive / Level-Order Traversal of Binary Trees](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/)
 - [Divide-and-Conquer Framework](https://labuladong.online/algo/essential-technique/divide-and-conquer/)
 - [Divide-and-Conquer: Operator Precedence](https://labuladong.online/algo/fname.html?fname=divide-and-conquer)
 - [Mind-Switch Between DP and Backtracking](https://labuladong.online/algo/dynamic-programming/word-break/)
 - [Two Perspectives on DP Enumeration](https://labuladong.online/algo/dynamic-programming/two-views-of-dp/)
 - [Backtracking Practice: Set Partitioning](https://labuladong.online/algo/practice-in-action/partition-to-k-equal-sum-subsets/)
 - [Backtracking Sweeps All Permutation/Combination/Subset Problems](https://labuladong.online/algo/essential-technique/permutation-combination-subset-all-in-one/)
 - [Backtracking Framework](https://labuladong.online/algo/essential-technique/backtrack-framework/)
 - [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Extension: Merge Sort Details and Applications](https://labuladong.online/algo/practice-in-action/merge-sort/)
 - [Extension: Quicksort Details and Applications](https://labuladong.online/algo/practice-in-action/quick-sort/)
 - [Extension: Lowest Common Ancestor Series Framework](https://labuladong.online/algo/practice-in-action/lowest-common-ancestor-summary/)
 - [Extension: Iterative Tree Traversal Using a Stack](https://labuladong.online/algo/data-structure/iterative-traversal-binary-tree/)
 - [Cycle Detection and Topological Sort](https://labuladong.online/algo/data-structure/topological-sort/)
 - [Box-and-Ball Model: Two Backtracking Perspectives](https://labuladong.online/algo/practice-in-action/two-views-of-backtrack/)
 - [Algorithm Practice and Flow](https://labuladong.online/algo/fname.html?fname=flow)
 - [Classic DP: Edit Distance](https://labuladong.online/algo/dynamic-programming/edit-distance/)
 - [Answers to Common Backtracking/DFS Questions](https://labuladong.online/algo/essential-technique/backtrack-vs-dfs/)

</details><hr>
<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [100. Same Tree](https://leetcode.com/problems/same-tree/?show=1) | [100. Same Tree](https://leetcode.cn/problems/same-tree/?show=1) | 🟢 |
| [1008. Construct Binary Search Tree from Preorder Traversal](https://leetcode.com/problems/construct-binary-search-tree-from-preorder-traversal/?show=1) | [1008. Construct Binary Search Tree from Preorder Traversal](https://leetcode.cn/problems/construct-binary-search-tree-from-preorder-traversal/?show=1) | 🟠 |
| [101. Symmetric Tree](https://leetcode.com/problems/symmetric-tree/?show=1) | [101. Symmetric Tree](https://leetcode.cn/problems/symmetric-tree/?show=1) | 🟢 |
| [1022. Sum of Root To Leaf Binary Numbers](https://leetcode.com/problems/sum-of-root-to-leaf-binary-numbers/?show=1) | [1022. Sum of Root To Leaf Binary Numbers](https://leetcode.cn/problems/sum-of-root-to-leaf-binary-numbers/?show=1) | 🟢 |
| [1026. Maximum Difference Between Node and Ancestor](https://leetcode.com/problems/maximum-difference-between-node-and-ancestor/?show=1) | [1026. Maximum Difference Between Node and Ancestor](https://leetcode.cn/problems/maximum-difference-between-node-and-ancestor/?show=1) | 🟠 |
| [108. Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/?show=1) | [108. Convert Sorted Array to Binary Search Tree](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/?show=1) | 🟢 |
| [1080. Insufficient Nodes in Root to Leaf Paths](https://leetcode.com/problems/insufficient-nodes-in-root-to-leaf-paths/?show=1) | [1080. Insufficient Nodes in Root to Leaf Paths](https://leetcode.cn/problems/insufficient-nodes-in-root-to-leaf-paths/?show=1) | 🟠 |
| [110. Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/?show=1) | [110. Balanced Binary Tree](https://leetcode.cn/problems/balanced-binary-tree/?show=1) | 🟢 |
| [111. Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree/?show=1) | [111. Minimum Depth of Binary Tree](https://leetcode.cn/problems/minimum-depth-of-binary-tree/?show=1) | 🟢 |
| [1110. Delete Nodes And Return Forest](https://leetcode.com/problems/delete-nodes-and-return-forest/?show=1) | [1110. Delete Nodes And Return Forest](https://leetcode.cn/problems/delete-nodes-and-return-forest/?show=1) | 🟠 |
| [1120. Maximum Average Subtree](https://leetcode.com/problems/maximum-average-subtree/?show=1)🔒 | [1120. Maximum Average Subtree](https://leetcode.cn/problems/maximum-average-subtree/?show=1)🔒 | 🟠 |
| [113. Path Sum II](https://leetcode.com/problems/path-sum-ii/?show=1) | [113. Path Sum II](https://leetcode.cn/problems/path-sum-ii/?show=1) | 🟠 |
| [114. Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/?show=1) | [114. Flatten Binary Tree to Linked List](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/?show=1) | 🟠 |
| [116. Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/?show=1) | [116. Populating Next Right Pointers in Each Node](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/?show=1) | 🟠 |
| [124. Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/?show=1) | [124. Binary Tree Maximum Path Sum](https://leetcode.cn/problems/binary-tree-maximum-path-sum/?show=1) | 🔴 |
| [1245. Tree Diameter](https://leetcode.com/problems/tree-diameter/?show=1)🔒 | [1245. Tree Diameter](https://leetcode.cn/problems/tree-diameter/?show=1)🔒 | 🟠 |
| [1261. Find Elements in a Contaminated Binary Tree](https://leetcode.com/problems/find-elements-in-a-contaminated-binary-tree/?show=1) | [1261. Find Elements in a Contaminated Binary Tree](https://leetcode.cn/problems/find-elements-in-a-contaminated-binary-tree/?show=1) | 🟠 |
| [129. Sum Root to Leaf Numbers](https://leetcode.com/problems/sum-root-to-leaf-numbers/?show=1) | [129. Sum Root to Leaf Numbers](https://leetcode.cn/problems/sum-root-to-leaf-numbers/?show=1) | 🟠 |
| [1315. Sum of Nodes with Even-Valued Grandparent](https://leetcode.com/problems/sum-of-nodes-with-even-valued-grandparent/?show=1) | [1315. Sum of Nodes with Even-Valued Grandparent](https://leetcode.cn/problems/sum-of-nodes-with-even-valued-grandparent/?show=1) | 🟠 |
| [1325. Delete Leaves With a Given Value](https://leetcode.com/problems/delete-leaves-with-a-given-value/?show=1) | [1325. Delete Leaves With a Given Value](https://leetcode.cn/problems/delete-leaves-with-a-given-value/?show=1) | 🟠 |
| [1339. Maximum Product of Splitted Binary Tree](https://leetcode.com/problems/maximum-product-of-splitted-binary-tree/?show=1) | [1339. Maximum Product of Splitted Binary Tree](https://leetcode.cn/problems/maximum-product-of-splitted-binary-tree/?show=1) | 🟠 |
| [1367. Linked List in Binary Tree](https://leetcode.com/problems/linked-list-in-binary-tree/?show=1) | [1367. Linked List in Binary Tree](https://leetcode.cn/problems/linked-list-in-binary-tree/?show=1) | 🟠 |
| [1372. Longest ZigZag Path in a Binary Tree](https://leetcode.com/problems/longest-zigzag-path-in-a-binary-tree/?show=1) | [1372. Longest ZigZag Path in a Binary Tree](https://leetcode.cn/problems/longest-zigzag-path-in-a-binary-tree/?show=1) | 🟠 |
| [1373. Maximum Sum BST in Binary Tree](https://leetcode.com/problems/maximum-sum-bst-in-binary-tree/?show=1) | [1373. Maximum Sum BST in Binary Tree](https://leetcode.cn/problems/maximum-sum-bst-in-binary-tree/?show=1) | 🔴 |
| [1376. Time Needed to Inform All Employees](https://leetcode.com/problems/time-needed-to-inform-all-employees/?show=1) | [1376. Time Needed to Inform All Employees](https://leetcode.cn/problems/time-needed-to-inform-all-employees/?show=1) | 🟠 |
| [1379. Find a Corresponding Node of a Binary Tree in a Clone of That Tree](https://leetcode.com/problems/find-a-corresponding-node-of-a-binary-tree-in-a-clone-of-that-tree/?show=1) | [1379. Find a Corresponding Node of a Binary Tree in a Clone of That Tree](https://leetcode.cn/problems/find-a-corresponding-node-of-a-binary-tree-in-a-clone-of-that-tree/?show=1) | 🟢 |
| [1430. Check If a String Is a Valid Sequence from Root to Leaves Path in a Binary Tree](https://leetcode.com/problems/check-if-a-string-is-a-valid-sequence-from-root-to-leaves-path-in-a-binary-tree/?show=1)🔒 | [1430. Check If a String Is a Valid Sequence from Root to Leaves Path in a Binary Tree](https://leetcode.cn/problems/check-if-a-string-is-a-valid-sequence-from-root-to-leaves-path-in-a-binary-tree/?show=1)🔒 | 🟠 |
| [1443. Minimum Time to Collect All Apples in a Tree](https://leetcode.com/problems/minimum-time-to-collect-all-apples-in-a-tree/?show=1) | [1443. Minimum Time to Collect All Apples in a Tree](https://leetcode.cn/problems/minimum-time-to-collect-all-apples-in-a-tree/?show=1) | 🟠 |
| [1448. Count Good Nodes in Binary Tree](https://leetcode.com/problems/count-good-nodes-in-binary-tree/?show=1) | [1448. Count Good Nodes in Binary Tree](https://leetcode.cn/problems/count-good-nodes-in-binary-tree/?show=1) | 🟠 |
| [145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/?show=1) | [145. Binary Tree Postorder Traversal](https://leetcode.cn/problems/binary-tree-postorder-traversal/?show=1) | 🟢 |
| [1457. Pseudo-Palindromic Paths in a Binary Tree](https://leetcode.com/problems/pseudo-palindromic-paths-in-a-binary-tree/?show=1) | [1457. Pseudo-Palindromic Paths in a Binary Tree](https://leetcode.cn/problems/pseudo-palindromic-paths-in-a-binary-tree/?show=1) | 🟠 |
| [1469. Find All The Lonely Nodes](https://leetcode.com/problems/find-all-the-lonely-nodes/?show=1)🔒 | [1469. Find All The Lonely Nodes](https://leetcode.cn/problems/find-all-the-lonely-nodes/?show=1)🔒 | 🟢 |
| [1485. Clone Binary Tree With Random Pointer](https://leetcode.com/problems/clone-binary-tree-with-random-pointer/?show=1)🔒 | [1485. Clone Binary Tree With Random Pointer](https://leetcode.cn/problems/clone-binary-tree-with-random-pointer/?show=1)🔒 | 🟠 |
| [1490. Clone N-ary Tree](https://leetcode.com/problems/clone-n-ary-tree/?show=1)🔒 | [1490. Clone N-ary Tree](https://leetcode.cn/problems/clone-n-ary-tree/?show=1)🔒 | 🟠 |
| [1593. Split a String Into the Max Number of Unique Substrings](https://leetcode.com/problems/split-a-string-into-the-max-number-of-unique-substrings/?show=1) | [1593. Split a String Into the Max Number of Unique Substrings](https://leetcode.cn/problems/split-a-string-into-the-max-number-of-unique-substrings/?show=1) | 🟠 |
| [1602. Find Nearest Right Node in Binary Tree](https://leetcode.com/problems/find-nearest-right-node-in-binary-tree/?show=1)🔒 | [1602. Find Nearest Right Node in Binary Tree](https://leetcode.cn/problems/find-nearest-right-node-in-binary-tree/?show=1)🔒 | 🟠 |
| [1612. Check If Two Expression Trees are Equivalent](https://leetcode.com/problems/check-if-two-expression-trees-are-equivalent/?show=1)🔒 | [1612. Check If Two Expression Trees are Equivalent](https://leetcode.cn/problems/check-if-two-expression-trees-are-equivalent/?show=1)🔒 | 🟠 |
| [1740. Find Distance in a Binary Tree](https://leetcode.com/problems/find-distance-in-a-binary-tree/?show=1)🔒 | [1740. Find Distance in a Binary Tree](https://leetcode.cn/problems/find-distance-in-a-binary-tree/?show=1)🔒 | 🟠 |
| [2049. Count Nodes With the Highest Score](https://leetcode.com/problems/count-nodes-with-the-highest-score/?show=1) | [2049. Count Nodes With the Highest Score](https://leetcode.cn/problems/count-nodes-with-the-highest-score/?show=1) | 🟠 |
| [2096. Step-By-Step Directions From a Binary Tree Node to Another](https://leetcode.com/problems/step-by-step-directions-from-a-binary-tree-node-to-another/?show=1) | [2096. Step-By-Step Directions From a Binary Tree Node to Another](https://leetcode.cn/problems/step-by-step-directions-from-a-binary-tree-node-to-another/?show=1) | 🟠 |
| [226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/?show=1) | [226. Invert Binary Tree](https://leetcode.cn/problems/invert-binary-tree/?show=1) | 🟢 |
| [250. Count Univalue Subtrees](https://leetcode.com/problems/count-univalue-subtrees/?show=1)🔒 | [250. Count Univalue Subtrees](https://leetcode.cn/problems/count-univalue-subtrees/?show=1)🔒 | 🟠 |
| [254. Factor Combinations](https://leetcode.com/problems/factor-combinations/?show=1)🔒 | [254. Factor Combinations](https://leetcode.cn/problems/factor-combinations/?show=1)🔒 | 🟠 |
| [257. Binary Tree Paths](https://leetcode.com/problems/binary-tree-paths/?show=1) | [257. Binary Tree Paths](https://leetcode.cn/problems/binary-tree-paths/?show=1) | 🟢 |
| [267. Palindrome Permutation II](https://leetcode.com/problems/palindrome-permutation-ii/?show=1)🔒 | [267. Palindrome Permutation II](https://leetcode.cn/problems/palindrome-permutation-ii/?show=1)🔒 | 🟠 |
| [270. Closest Binary Search Tree Value](https://leetcode.com/problems/closest-binary-search-tree-value/?show=1)🔒 | [270. Closest Binary Search Tree Value](https://leetcode.cn/problems/closest-binary-search-tree-value/?show=1)🔒 | 🟢 |
| [294. Flip Game II](https://leetcode.com/problems/flip-game-ii/?show=1)🔒 | [294. Flip Game II](https://leetcode.cn/problems/flip-game-ii/?show=1)🔒 | 🟠 |
| [298. Binary Tree Longest Consecutive Sequence](https://leetcode.com/problems/binary-tree-longest-consecutive-sequence/?show=1)🔒 | [298. Binary Tree Longest Consecutive Sequence](https://leetcode.cn/problems/binary-tree-longest-consecutive-sequence/?show=1)🔒 | 🟠 |
| [332. Reconstruct Itinerary](https://leetcode.com/problems/reconstruct-itinerary/?show=1) | [332. Reconstruct Itinerary](https://leetcode.cn/problems/reconstruct-itinerary/?show=1) | 🔴 |
| [333. Largest BST Subtree](https://leetcode.com/problems/largest-bst-subtree/?show=1)🔒 | [333. Largest BST Subtree](https://leetcode.cn/problems/largest-bst-subtree/?show=1)🔒 | 🟠 |
| [339. Nested List Weight Sum](https://leetcode.com/problems/nested-list-weight-sum/?show=1)🔒 | [339. Nested List Weight Sum](https://leetcode.cn/problems/nested-list-weight-sum/?show=1)🔒 | 🟠 |
| [366. Find Leaves of Binary Tree](https://leetcode.com/problems/find-leaves-of-binary-tree/?show=1)🔒 | [366. Find Leaves of Binary Tree](https://leetcode.cn/problems/find-leaves-of-binary-tree/?show=1)🔒 | 🟠 |
| [386. Lexicographical Numbers](https://leetcode.com/problems/lexicographical-numbers/?show=1) | [386. Lexicographical Numbers](https://leetcode.cn/problems/lexicographical-numbers/?show=1) | 🟠 |
| [404. Sum of Left Leaves](https://leetcode.com/problems/sum-of-left-leaves/?show=1) | [404. Sum of Left Leaves](https://leetcode.cn/problems/sum-of-left-leaves/?show=1) | 🟢 |
| [426. Convert Binary Search Tree to Sorted Doubly Linked List](https://leetcode.com/problems/convert-binary-search-tree-to-sorted-doubly-linked-list/?show=1)🔒 | [426. Convert Binary Search Tree to Sorted Doubly Linked List](https://leetcode.cn/problems/convert-binary-search-tree-to-sorted-doubly-linked-list/?show=1)🔒 | 🟠 |
| [437. Path Sum III](https://leetcode.com/problems/path-sum-iii/?show=1) | [437. Path Sum III](https://leetcode.cn/problems/path-sum-iii/?show=1) | 🟠 |
| [501. Find Mode in Binary Search Tree](https://leetcode.com/problems/find-mode-in-binary-search-tree/?show=1) | [501. Find Mode in Binary Search Tree](https://leetcode.cn/problems/find-mode-in-binary-search-tree/?show=1) | 🟢 |
| [508. Most Frequent Subtree Sum](https://leetcode.com/problems/most-frequent-subtree-sum/?show=1) | [508. Most Frequent Subtree Sum](https://leetcode.cn/problems/most-frequent-subtree-sum/?show=1) | 🟠 |
| [513. Find Bottom Left Tree Value](https://leetcode.com/problems/find-bottom-left-tree-value/?show=1) | [513. Find Bottom Left Tree Value](https://leetcode.cn/problems/find-bottom-left-tree-value/?show=1) | 🟠 |
| [515. Find Largest Value in Each Tree Row](https://leetcode.com/problems/find-largest-value-in-each-tree-row/?show=1) | [515. Find Largest Value in Each Tree Row](https://leetcode.cn/problems/find-largest-value-in-each-tree-row/?show=1) | 🟠 |
| [530. Minimum Absolute Difference in BST](https://leetcode.com/problems/minimum-absolute-difference-in-bst/?show=1) | [530. Minimum Absolute Difference in BST](https://leetcode.cn/problems/minimum-absolute-difference-in-bst/?show=1) | 🟢 |
| [538. Convert BST to Greater Tree](https://leetcode.com/problems/convert-bst-to-greater-tree/?show=1) | [538. Convert BST to Greater Tree](https://leetcode.cn/problems/convert-bst-to-greater-tree/?show=1) | 🟠 |
| [549. Binary Tree Longest Consecutive Sequence II](https://leetcode.com/problems/binary-tree-longest-consecutive-sequence-ii/?show=1)🔒 | [549. Binary Tree Longest Consecutive Sequence II](https://leetcode.cn/problems/binary-tree-longest-consecutive-sequence-ii/?show=1)🔒 | 🟠 |
| [559. Maximum Depth of N-ary Tree](https://leetcode.com/problems/maximum-depth-of-n-ary-tree/?show=1) | [559. Maximum Depth of N-ary Tree](https://leetcode.cn/problems/maximum-depth-of-n-ary-tree/?show=1) | 🟢 |
| [563. Binary Tree Tilt](https://leetcode.com/problems/binary-tree-tilt/?show=1) | [563. Binary Tree Tilt](https://leetcode.cn/problems/binary-tree-tilt/?show=1) | 🟢 |
| [572. Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree/?show=1) | [572. Subtree of Another Tree](https://leetcode.cn/problems/subtree-of-another-tree/?show=1) | 🟢 |
| [582. Kill Process](https://leetcode.com/problems/kill-process/?show=1)🔒 | [582. Kill Process](https://leetcode.cn/problems/kill-process/?show=1)🔒 | 🟠 |
| [606. Construct String from Binary Tree](https://leetcode.com/problems/construct-string-from-binary-tree/?show=1) | [606. Construct String from Binary Tree](https://leetcode.cn/problems/construct-string-from-binary-tree/?show=1) | 🟢 |
| [617. Merge Two Binary Trees](https://leetcode.com/problems/merge-two-binary-trees/?show=1) | [617. Merge Two Binary Trees](https://leetcode.cn/problems/merge-two-binary-trees/?show=1) | 🟢 |
| [623. Add One Row to Tree](https://leetcode.com/problems/add-one-row-to-tree/?show=1) | [623. Add One Row to Tree](https://leetcode.cn/problems/add-one-row-to-tree/?show=1) | 🟠 |
| [654. Maximum Binary Tree](https://leetcode.com/problems/maximum-binary-tree/?show=1) | [654. Maximum Binary Tree](https://leetcode.cn/problems/maximum-binary-tree/?show=1) | 🟠 |
| [663. Equal Tree Partition](https://leetcode.com/problems/equal-tree-partition/?show=1)🔒 | [663. Equal Tree Partition](https://leetcode.cn/problems/equal-tree-partition/?show=1)🔒 | 🟠 |
| [666. Path Sum IV](https://leetcode.com/problems/path-sum-iv/?show=1)🔒 | [666. Path Sum IV](https://leetcode.cn/problems/path-sum-iv/?show=1)🔒 | 🟠 |
| [669. Trim a Binary Search Tree](https://leetcode.com/problems/trim-a-binary-search-tree/?show=1) | [669. Trim a Binary Search Tree](https://leetcode.cn/problems/trim-a-binary-search-tree/?show=1) | 🟠 |
| [671. Second Minimum Node In a Binary Tree](https://leetcode.com/problems/second-minimum-node-in-a-binary-tree/?show=1) | [671. Second Minimum Node In a Binary Tree](https://leetcode.cn/problems/second-minimum-node-in-a-binary-tree/?show=1) | 🟢 |
| [687. Longest Univalue Path](https://leetcode.com/problems/longest-univalue-path/?show=1) | [687. Longest Univalue Path](https://leetcode.cn/problems/longest-univalue-path/?show=1) | 🟠 |
| [776. Split BST](https://leetcode.com/problems/split-bst/?show=1)🔒 | [776. Split BST](https://leetcode.cn/problems/split-bst/?show=1)🔒 | 🟠 |
| [865. Smallest Subtree with all the Deepest Nodes](https://leetcode.com/problems/smallest-subtree-with-all-the-deepest-nodes/?show=1) | [865. Smallest Subtree with all the Deepest Nodes](https://leetcode.cn/problems/smallest-subtree-with-all-the-deepest-nodes/?show=1) | 🟠 |
| [894. All Possible Full Binary Trees](https://leetcode.com/problems/all-possible-full-binary-trees/?show=1) | [894. All Possible Full Binary Trees](https://leetcode.cn/problems/all-possible-full-binary-trees/?show=1) | 🟠 |
| [897. Increasing Order Search Tree](https://leetcode.com/problems/increasing-order-search-tree/?show=1) | [897. Increasing Order Search Tree](https://leetcode.cn/problems/increasing-order-search-tree/?show=1) | 🟢 |
| [938. Range Sum of BST](https://leetcode.com/problems/range-sum-of-bst/?show=1) | [938. Range Sum of BST](https://leetcode.cn/problems/range-sum-of-bst/?show=1) | 🟢 |
| [951. Flip Equivalent Binary Trees](https://leetcode.com/problems/flip-equivalent-binary-trees/?show=1) | [951. Flip Equivalent Binary Trees](https://leetcode.cn/problems/flip-equivalent-binary-trees/?show=1) | 🟠 |
| [965. Univalued Binary Tree](https://leetcode.com/problems/univalued-binary-tree/?show=1) | [965. Univalued Binary Tree](https://leetcode.cn/problems/univalued-binary-tree/?show=1) | 🟢 |
| [968. Binary Tree Cameras](https://leetcode.com/problems/binary-tree-cameras/?show=1) | [968. Binary Tree Cameras](https://leetcode.cn/problems/binary-tree-cameras/?show=1) | 🔴 |
| [971. Flip Binary Tree To Match Preorder Traversal](https://leetcode.com/problems/flip-binary-tree-to-match-preorder-traversal/?show=1) | [971. Flip Binary Tree To Match Preorder Traversal](https://leetcode.cn/problems/flip-binary-tree-to-match-preorder-traversal/?show=1) | 🟠 |
| [979. Distribute Coins in Binary Tree](https://leetcode.com/problems/distribute-coins-in-binary-tree/?show=1) | [979. Distribute Coins in Binary Tree](https://leetcode.cn/problems/distribute-coins-in-binary-tree/?show=1) | 🟠 |
| [987. Vertical Order Traversal of a Binary Tree](https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree/?show=1) | [987. Vertical Order Traversal of a Binary Tree](https://leetcode.cn/problems/vertical-order-traversal-of-a-binary-tree/?show=1) | 🔴 |
| [988. Smallest String Starting From Leaf](https://leetcode.com/problems/smallest-string-starting-from-leaf/?show=1) | [988. Smallest String Starting From Leaf](https://leetcode.cn/problems/smallest-string-starting-from-leaf/?show=1) | 🟠 |
| [99. Recover Binary Search Tree](https://leetcode.com/problems/recover-binary-search-tree/?show=1) | [99. Recover Binary Search Tree](https://leetcode.cn/problems/recover-binary-search-tree/?show=1) | 🟠 |
| [993. Cousins in Binary Tree](https://leetcode.com/problems/cousins-in-binary-tree/?show=1) | [993. Cousins in Binary Tree](https://leetcode.cn/problems/cousins-in-binary-tree/?show=1) | 🟢 |
| [998. Maximum Binary Tree II](https://leetcode.com/problems/maximum-binary-tree-ii/?show=1) | [998. Maximum Binary Tree II](https://leetcode.cn/problems/maximum-binary-tree-ii/?show=1) | 🟠 |
| - | [Sword to Offer 06. Print Linked List from Tail to Head](https://leetcode.cn/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/?show=1) | 🟢 |
| - | [Sword to Offer 26. Substructure of a Tree](https://leetcode.cn/problems/shu-de-zi-jie-gou-lcof/?show=1) | 🟠 |
| - | [Sword to Offer 27. Mirror of a Binary Tree](https://leetcode.cn/problems/er-cha-shu-de-jing-xiang-lcof/?show=1) | 🟢 |
| - | [Sword to Offer 28. Symmetric Binary Tree](https://leetcode.cn/problems/dui-cheng-de-er-cha-shu-lcof/?show=1) | 🟢 |
| - | [Sword to Offer 33. Postorder Traversal Sequence of a BST](https://leetcode.cn/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/?show=1) | 🟠 |
| - | [Sword to Offer 34. Paths with Given Sum in a Binary Tree](https://leetcode.cn/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/?show=1) | 🟠 |
| - | [Sword to Offer 36. Convert BST to Doubly Linked List](https://leetcode.cn/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/?show=1) | 🟠 |
| - | [Sword to Offer 55 - I. Depth of a Binary Tree](https://leetcode.cn/problems/er-cha-shu-de-shen-du-lcof/?show=1) | 🟢 |
| - | [Sword to Offer 55 - II. Balanced Binary Tree](https://leetcode.cn/problems/ping-heng-er-cha-shu-lcof/?show=1) | 🟢 |
| - | [Sword to Offer II 044. Maximum Value at Each Level](https://leetcode.cn/problems/hPov7L/?show=1) | 🟠 |
| - | [Sword to Offer II 045. Bottom Left Tree Value](https://leetcode.cn/problems/LwUNpT/?show=1) | 🟠 |
| - | [Sword to Offer II 049. Sum Root to Leaf Numbers](https://leetcode.cn/problems/3Etpl5/?show=1) | 🟠 |
| - | [Sword to Offer II 050. Path Sum III](https://leetcode.cn/problems/6eUYwP/?show=1) | 🟠 |
| - | [Sword to Offer II 051. Binary Tree Maximum Path Sum](https://leetcode.cn/problems/jC7MId/?show=1) | 🔴 |
| - | [Sword to Offer II 052. Increasing-Order Search Tree](https://leetcode.cn/problems/NYBBNL/?show=1) | 🟢 |
| - | [Sword to Offer II 054. Sum of Values of All Greater-or-Equal Nodes](https://leetcode.cn/problems/w6cpku/?show=1) | 🟠 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
