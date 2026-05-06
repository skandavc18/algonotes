# Graph Algorithm Basics

<p align='center'>
<a href="https://github.com/labuladong/fucking-algorithm" target="view_window"><img alt="GitHub" src="https://img.shields.io/github/stars/labuladong/fucking-algorithm?label=Stars&style=flat-square&logo=GitHub"></a>
<a href="https://labuladong.online/algo/" target="_blank"><img class="my_header_icon" src="https://img.shields.io/static/v1?label=Premium%20Course&message=View&color=pink&style=flat"></a>
<a href="https://www.zhihu.com/people/labuladong"><img src="https://img.shields.io/badge/Zhihu-@labuladong-000000.svg?style=flat-square&logo=Zhihu"></a>
<a href="https://space.bilibili.com/14089380"><img src="https://img.shields.io/badge/Bilibili-@labuladong-000000.svg?style=flat-square&logo=Bilibili"></a>
</p>

![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: [the new website's membership](https://labuladong.online/algo/intro/site-vip/) is about to increase in price; renewals for existing users are supported. It's also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [797. All Paths From Source to Target](https://leetcode.com/problems/all-paths-from-source-to-target/) | [797. All Paths From Source to Target](https://leetcode.cn/problems/all-paths-from-source-to-target/) | 🟠
| - | [Sword to Offer II 110. All Paths](https://leetcode.cn/problems/bP4bmD/) | 🟠

**-----------**

> tip: a video version is available: [Graph Theory Basics and Traversal](https://www.bilibili.com/video/BV19G41187cL/). Follow my Bilibili account; I'll guide you through more difficult algorithm techniques on video.



Readers often ask about graphs. As I discussed in [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/abstraction-of-algorithm/), although graphs unlock many algorithms and tackle more complex problems, they can essentially be considered an extension of multiway trees.

Graph problems rarely appear in interviews and online tests; even when they do, they are usually simple traversal problems and basically reuse the multiway-tree traversal.

So this article sticks with our style — only the most practical, accessible parts of graphs, giving you an intuitive feel. At the end I list other classic graph algorithms; with this article in hand you should be able to tackle them.

### Logical Structure and Concrete Implementation

A graph consists of **nodes** and **edges**. Logical structure:

![](https://labuladong.online/algo/images/图/0.jpg)

**What is a "logical structure"? It's the abstract picture for studying convenience.**

Each node could be implemented as:

```java
/* Logical structure of a graph node */
class Vertex {
    int id;
    Vertex[] neighbors;
}
```

Looks familiar? It's almost identical to a multiway-tree node:

```java
/* A basic N-ary tree node */
class TreeNode {
    int val;
    TreeNode[] children;
}
```

So graphs really aren't deep — they're just slightly fancier multiway trees. DFS/BFS traversals on trees apply directly to graphs.

But this implementation is "logical". In practice we rarely use a `Vertex` class; we use **adjacency lists and adjacency matrices**.

For the same graph:

![](https://labuladong.online/algo/images/图/0.jpg)

Stored as adjacency list / matrix:

![](https://labuladong.online/algo/images/图/2.jpeg)

Adjacency list: store each node `x`'s neighbors in a list and associate `x` with that list. Then we can find all of `x`'s neighbors via this list.

Adjacency matrix: a 2D boolean array `matrix`. If `x` and `y` are connected, `matrix[x][y] = true` (the green cells). To find `x`'s neighbors, scan `matrix[x][..]`.

In code:

```java
// Adjacency list
// graph[x] stores all neighbors of x
List<Integer>[] graph;

// Adjacency matrix
// matrix[x][y] indicates whether there is an edge from x to y
boolean[][] matrix;
```

**Why two storage forms? Each has pros and cons.**

Adjacency list: less space.

Adjacency matrix has many empty slots, costing more space.

But adjacency lists can't quickly tell whether two nodes are adjacent.

To check whether node `1` is adjacent to node `3`, we'd scan `1`'s neighbor list. With a matrix it's just `matrix[1][3]` — fast.

So choose by situation.

::: tip

In ordinary algorithm problems, adjacency lists are more common — easier to operate. But matrices shouldn't be dismissed: they are a powerful mathematical tool, and some subtle properties of graphs are revealed via matrix operations. This article doesn't go into the math; interested readers can explore on their own.

:::

Finally, the graph-specific notion of **degree**: in an undirected graph, the "degree" of a node is the number of edges incident to it.

In a directed graph, "degree" splits into **in-degree** and **out-degree**:

![](https://labuladong.online/algo/images/图/0.jpg)

Node `3`'s in-degree is 3 (three edges point to it) and out-degree is 1 (one edge points away).

That's plenty for "graph" basics.

You may ask: what we described is just a "directed unweighted graph"; what about weighted, undirected, etc.?

**These are derived from this simplest model.**

**Directed weighted graph?**

Adjacency list: store each `x`'s neighbors and the weight of each edge to that neighbor.

Adjacency matrix: `matrix[x][y]` is now an int, where 0 means no edge and other values are the weight.

In code:

```java
// Adjacency list
// graph[x] stores x's neighbors and the corresponding weights
List<int[]>[] graph;

// Adjacency matrix
// matrix[x][y] is the weight of the edge from x to y; 0 means no edge
int[][] matrix;
```

**Undirected graphs?** "Undirected" is essentially "bidirectional".

![](https://labuladong.online/algo/images/图/3.jpeg)

To connect `x` and `y`, set `matrix[x][y]` and `matrix[y][x]` to `true`; for adjacency lists, add `y` to `x`'s neighbor list and vice versa.

Combine these for undirected weighted graphs.

That covers the basics. Whatever variants come, you should be ready.

Now the universal task for any data structure: traversal.

### Graph Traversal

**[Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/abstraction-of-algorithm/) said: data structures exist to be traversed and accessed, so traversal is foundational.**

How to traverse a graph? Start with multiway-tree DFS:

```java
/* Multiway-tree traversal framework */
void traverse(TreeNode root) {
    if (root == null) return;
    // Preorder
    for (TreeNode child : root.children) {
        traverse(child);
    }
    // Postorder
}
```

The big difference: graphs may have cycles. Starting from a node and following edges, you might come back to the same node — trees don't have this issue.

If a graph has cycles, the framework needs a `visited` array:

```java
// Records nodes that have been visited
boolean[] visited;
// Records the path from the start to the current node
boolean[] onPath;

/* Graph traversal framework */
void traverse(Graph graph, int s) {
    if (visited[s]) return;
    // Visit node s; mark as visited
    visited[s] = true;
    // Choice: mark s as on the current path
    onPath[s] = true;
    for (int neighbor : graph.neighbors(s)) {
        traverse(graph, neighbor);
    }
    // Undo choice: leave s
    onPath[s] = false;
}
```

Note the difference between `visited` and `onPath`. Since a binary tree is a special graph, use a binary-tree traversal to understand the difference:

![](https://labuladong.online/algo/images/迭代遍历二叉树/1.gif)

**The GIF above shows recursive binary-tree traversal: nodes marked `true` in `visited` are gray; nodes marked `true` in `onPath` are green.** Like Snake: `visited` is the squares the snake has visited; `onPath` is the snake's body. In graph traversal, `onPath` is used to detect cycles — like Snake biting itself.

For path-related problems, `onPath` is essential — used in [topological sort](https://labuladong.online/algo/data-structure/topological-sort/), for example.

Note that `onPath` operations look like the "make/undo choice" in [the backtracking framework](https://labuladong.online/algo/essential-technique/backtrack-framework/), but their position differs: in backtracking, "make/undo choice" is **inside** the for loop; here, `onPath` operations are **outside** the for loop.

Why? As discussed in [Binary Tree Tactics (Outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/): backtracking focuses on edges, not nodes. Re-read it if you need.

For backtracking, choices are made on "edges":

![](https://labuladong.online/algo/images/backtracking/5.jpg)

In code:

```java
// DFS: focus on nodes
void traverse(TreeNode root) {
    if (root == null) return;
    printf("Enter node %s", root);
    for (TreeNode child : root.children) {
        traverse(child);
    }
    printf("Leave node %s", root);
}

// Backtracking: focus on edges
void backtrack(TreeNode root) {
    if (root == null) return;
    for (TreeNode child : root.children) {
        // Make a choice
        printf("From %s to %s", root, child);
        backtrack(child);
        // Undo
        printf("From %s to %s", child, root);
    }
}
```

Run this code and you'll find the root is missed:

```java
void traverse(TreeNode root) {
    if (root == null) return;
    for (TreeNode child : root.children) {
        printf("Enter node %s", child);
        traverse(child);
        printf("Leave node %s", child);
    }
}
```

So for graph traversal we use the DFS form — `onPath` operations outside the for loop — otherwise we'd miss the start node.

As for `visited`: graphs may contain cycles, and `visited` prevents re-entering nodes (avoiding infinite recursion).

If the problem says the graph is acyclic, `visited` can be omitted; it's basically multiway-tree traversal.

### Practice

Look at LeetCode 797 "All Paths From Source to Target":

```java
List<List<Integer>> allPathsSourceTarget(int[][] graph);
```

Given a **directed acyclic graph** with `n` nodes labeled `0, 1, 2, ..., n - 1`, return all paths from node `0` to node `n - 1`.

The input `graph` is an adjacency-list representation: `graph[i]` is the list of neighbors of node `i`.

For `graph = [[1,2],[3],[3],[]]`, the corresponding graph:

![](https://labuladong.online/algo/images/图/1.jpg)

Return `[[0,1,3],[0,2,3]]` — all paths from `0` to `3`.

**Solution: traverse the graph from `0`, recording the path; when reaching the end, record it.**

The graph is acyclic, so no `visited` array — just the framework:

```java
class Solution {
    // Records all paths
    List<List<Integer>> res = new LinkedList<>();
        
    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        // Maintains the current path during recursion
        LinkedList<Integer> path = new LinkedList<>();
        traverse(graph, 0, path);
        return res;
    }

    /* Graph traversal framework */
    void traverse(int[][] graph, int s, LinkedList<Integer> path) {
        // Add s to path
        path.addLast(s);

        int n = graph.length;
        if (s == n - 1) {
            // Reached the end
            res.add(new LinkedList<>(path));
            // Could return here, but be sure to removeLast to maintain path correctly
            // path.removeLast();
            // return;
            // Returning isn't required since the graph is acyclic — no infinite recursion
        }

        // Recurse on neighbors
        for (int v : graph[s]) {
            traverse(graph, v, path);
        }
        
        // Remove s from path
        path.removeLast();
    }
}
```

<visual slug='all-paths-from-source-to-target'/>

Solved. Note the Java specifics: parameters pass object references, so when adding `path` to `res` we copy a new list — otherwise everything in `res` would be empty.

Wrap-up: the main storage forms are adjacency list and matrix. Whatever fancy graph appears, these two forms work.

In tests, the most common algorithm is graph traversal — very similar to multiway-tree traversal.

There are many other interesting graph algorithms: [Bipartite Detection](https://labuladong.online/algo/data-structure/bipartite-graph/), [Cycle Detection and Topological Sort](https://labuladong.online/algo/data-structure/topological-sort/) (compilers detect circular references with a similar algorithm), [Minimum Spanning Tree](https://labuladong.online/algo/data-structure/kruskal/), [Dijkstra](https://labuladong.online/algo/data-structure/dijkstra/), and more — interested readers can explore further.



<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Dijkstra Template and Applications](https://labuladong.online/algo/data-structure/dijkstra/)
 - [Prim MST Algorithm](https://labuladong.online/algo/data-structure/prim/)
 - [[Practice] Postorder Position II](https://labuladong.online/algo/problem-set/binary-tree-post-order-2/)
 - [[Practice] Postorder Position III](https://labuladong.online/algo/problem-set/binary-tree-post-order-3/)
 - [[Practice] Level-Order Traversal II](https://labuladong.online/algo/problem-set/binary-tree-level-2/)
 - [Sweeping Through All Island Problems](https://labuladong.online/algo/frequency-interview/island-dfs-summary/)
 - [Binary Tree Tactics (Outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
 - [Bipartite Detection](https://labuladong.online/algo/data-structure/bipartite-graph/)
 - [Binary Tree Basics and Common Types](https://labuladong.online/algo/data-structure-basic/binary-tree-basic/)
 - [Find the Celebrity Problem](https://labuladong.online/algo/frequency-interview/find-celebrity/)
 - [Trie Template Sweeps Five Problems](https://labuladong.online/algo/data-structure/trie/)
 - [Implementing a Dynamic Array](https://labuladong.online/algo/data-structure-basic/array-implement/)
 - [Backtracking Framework](https://labuladong.online/algo/essential-technique/backtrack-framework/)
 - [Union-Find Algorithm](https://labuladong.online/algo/data-structure/union-find/)
 - [My Practice Insights: The Essence of Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Cycle Detection and Topological Sort](https://labuladong.online/algo/data-structure/topological-sort/)
 - [Beat Algorithms with Algorithms](https://labuladong.online/algo/other-skills/algorithm-in-pdf/)
 - [Algorithm Practice and Flow](https://labuladong.online/algo/other-skills/hert-flow/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou |
| :----: | :----: |
| [1245. Tree Diameter](https://leetcode.com/problems/tree-diameter/?show=1)🔒 | [1245. Tree Diameter](https://leetcode.cn/problems/tree-diameter/?show=1)🔒 |
| [133. Clone Graph](https://leetcode.com/problems/clone-graph/?show=1) | [133. Clone Graph](https://leetcode.cn/problems/clone-graph/?show=1) |
| [1443. Minimum Time to Collect All Apples in a Tree](https://leetcode.com/problems/minimum-time-to-collect-all-apples-in-a-tree/?show=1) | [1443. Minimum Time to Collect All Apples in a Tree](https://leetcode.cn/problems/minimum-time-to-collect-all-apples-in-a-tree/?show=1) |
| [200. Number of Islands](https://leetcode.com/problems/number-of-islands/?show=1) | [200. Number of Islands](https://leetcode.cn/problems/number-of-islands/?show=1) |
| [2049. Count Nodes With the Highest Score](https://leetcode.com/problems/count-nodes-with-the-highest-score/?show=1) | [2049. Count Nodes With the Highest Score](https://leetcode.cn/problems/count-nodes-with-the-highest-score/?show=1) |
| [310. Minimum Height Trees](https://leetcode.com/problems/minimum-height-trees/?show=1) | [310. Minimum Height Trees](https://leetcode.cn/problems/minimum-height-trees/?show=1) |
| [542. 01 Matrix](https://leetcode.com/problems/01-matrix/?show=1) | [542. 01 Matrix](https://leetcode.cn/problems/01-matrix/?show=1) |
| [582. Kill Process](https://leetcode.com/problems/kill-process/?show=1)🔒 | [582. Kill Process](https://leetcode.cn/problems/kill-process/?show=1)🔒 |
| [79. Word Search](https://leetcode.com/problems/word-search/?show=1) | [79. Word Search](https://leetcode.cn/problems/word-search/?show=1) |
| - | [Sword to Offer II 110. All Paths](https://leetcode.cn/problems/bP4bmD/?show=1) |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**

**"labuladong's Algorithm Notes" has been published. Follow the official account for details; reply with "all-in-one bundle" to download the companion PDF and the full problem-solving bundle**:

![](https://labuladong.online/algo/images/souyisou2.png)
