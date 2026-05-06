# Union-Find (Disjoint Set) Algorithm


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [130. Surrounded Regions](https://leetcode.com/problems/surrounded-regions/) | [130. Surrounded Regions](https://leetcode.cn/problems/surrounded-regions/) | 🟠 |
| [323. Number of Connected Components in an Undirected Graph](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/)🔒 | [323. Number of Connected Components in an Undirected Graph](https://leetcode.cn/problems/number-of-connected-components-in-an-undirected-graph/)🔒 | 🟠 |
| [684. Redundant Connection](https://leetcode.com/problems/redundant-connection/) | [684. Redundant Connection](https://leetcode.cn/problems/redundant-connection/) | 🟠 |
| [990. Satisfiability of Equality Equations](https://leetcode.com/problems/satisfiability-of-equality-equations/) | [990. Satisfiability of Equality Equations](https://leetcode.cn/problems/satisfiability-of-equality-equations/) | 🟠 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [N-ary Tree Basics and Traversal](https://labuladong.online/algo/data-structure-basic/n-ary-tree-traverse-basic/)
> - [Graph Basics and General Implementation](https://labuladong.online/algo/data-structure-basic/graph-basic/)

The Union-Find algorithm is dedicated to "dynamic connectivity". I've written about it twice before; it's commonly tested and a prerequisite for minimum spanning trees, so this article consolidates everything.

Let's start with what dynamic connectivity is.

## 1. Dynamic Connectivity

In short, dynamic connectivity is about adding edges to a graph. The graph below has 10 disconnected nodes labeled 0-9:

![](https://labuladong.online/algo/images/unionfind/1.jpg)

Our Union-Find needs to support these APIs:

```java
class UF {
    // Connect p and q
    public void union(int p, int q);
    // Whether p and q are connected
    public boolean connected(int p, int q);
    // Number of connected components
    public int count();
}
```

"Connected" here is an equivalence relation — three properties:

1. Reflexive: `p` is connected to `p`.

2. Symmetric: if `p` and `q` are connected, then `q` and `p` are connected.

3. Transitive: if `p`-`q` and `q`-`r` are connected, then `p`-`r` is connected.

In the figure, no two **distinct** points are connected; `connected` returns false for any pair, and there are 10 components.

Calling `union(0, 1)` connects 0 and 1; the component count drops to 9.

Calling `union(1, 2)` connects 0, 1, 2; `connected(0, 2)` returns true; component count is 8.

![](https://labuladong.online/algo/images/unionfind/2.jpg)

This equivalence relation is useful in many places — compilers tracking variable references, social-network friend circles, etc.

So, you've roughly got dynamic connectivity. The crux is the efficiency of `union` and `connected`. What model represents the graph's connectivity? What data structure?


## 2. Basic Idea

Note we separate "model" from "data structure". The reason: we model with a **forest** (multiple trees) and implement it with an **array**.

How does a forest represent connectivity? Each tree node has a pointer to its parent; a root points to itself. With 10 disconnected nodes:

![](https://labuladong.online/algo/images/unionfind/3.jpg)

```java
class UF {
    // Number of connected components
    private int count;
    // x's parent is parent[x]
    private int[] parent;

    // n: number of nodes
    public UF(int n) {
        // Initially, each is its own component
        this.count = n;
        // Each node's parent points to itself
        parent = new int[n];
        for (int i = 0; i < n; i++)
            parent[i] = i;
    }

    // Other methods
}
```

**To connect two nodes, attach one's root to the other's root**:

![](https://labuladong.online/algo/images/unionfind/4.jpg)

```java
class UF {
    // Earlier code omitted for brevity...

    public void union(int p, int q) {
        int rootP = find(p);
        int rootQ = find(q);
        if (rootP == rootQ)
            return;
        // Merge two trees into one
        parent[rootP] = rootQ;
        // parent[rootQ] = rootP works equally well

        // Two components become one
        count--;
    }

    // Find x's root
    private int find(int x) {
        // The root has parent[x] == x
        while (parent[x] != x)
            x = parent[x];
        return x;
    }

    // Number of components
    public int count() { 
        return count;
    }
}
```

**If `p` and `q` are connected, they share a root**:

![](https://labuladong.online/algo/images/unionfind/5.jpg)

```java
class UF {
    // Earlier code omitted for brevity...

    public boolean connected(int p, int q) {
        int rootP = find(p);
        int rootQ = find(q);
        return rootP == rootQ;
    }
}
```

Union-Find is essentially done. Amazing — an array simulates a forest and elegantly handles a complex problem!

Complexity? Both `connected` and `union` are dominated by `find`, so they share its complexity.

`find` walks from a node up to the root, so its time is the tree's height. We habitually expect height to be `logN`, but only balanced binary trees guarantee this. For general trees, extreme imbalance can degrade them into linked lists with height `N`.

![](https://labuladong.online/algo/images/unionfind/6.jpg)

So `find`, `union`, `connected` are all O(N) — bad. Graph problems often involve large data (e.g. social networks); linear-time `union`/`connected` is unacceptable.

**Key question: how do we avoid imbalance?** A small trick suffices.

## 3. Balance Optimization

Where does imbalance come from? `union`:

```java
class UF {
    // Earlier code omitted for brevity...

    public void union(int p, int q) {
        int rootP = find(p);
        int rootQ = find(q);
        if (rootP == rootQ)
            return;
        // Merge
        parent[rootP] = rootQ;
        // parent[rootQ] = rootP works too
        count--;
    }
}
```

Naively attaching `p`'s tree under `q`'s root can create top-heavy imbalance:

![](https://labuladong.online/algo/images/unionfind/7.jpg)

Over time the tree may grow lopsided. **We want the smaller tree under the larger to stay balanced.** Use an extra `size` array recording each tree's node count ("weight"):

```java
class UF {
    private int count;
    private int[] parent;
    // New: weight of each tree
    private int[] size;

    public UF(int n) {
        this.count = n;
        parent = new int[n];
        // Each tree starts with 1 node — initial weight 1
        size = new int[n];
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            size[i] = 1;
        }
    }
    // Other methods
}
```

`size[3] = 5` means the tree rooted at 3 has 5 nodes. Updated `union`:

```java
class UF {
    // Earlier code omitted for brevity...

    public void union(int p, int q) {
        int rootP = find(p);
        int rootQ = find(q);
        if (rootP == rootQ)
            return;
        
        // Smaller tree under larger — more balanced
        if (size[rootP] > size[rootQ]) {
            parent[rootQ] = rootP;
            size[rootP] += size[rootQ];
        } else {
            parent[rootP] = rootQ;
            size[rootQ] += size[rootP];
        }
        count--;
    }
}
```

Comparing weights keeps trees roughly balanced — height around `logN`, dramatically faster.

`find`, `union`, `connected` are all O(logN) — fast even with hundreds of millions of items.

## 4. Path Compression

The optimization is short but the idea is clever.

**We don't care about the tree's exact shape — only the root.**

So can we further compress tree heights so they stay constant?

![](https://labuladong.online/algo/images/unionfind/8.jpg)

Now every node's parent is the root; `find` is O(1); so are `connected` and `union`.

We modify `find`. Two common implementations:

First, add one line to `find`:

```java
class UF {
    // Earlier code omitted for brevity...

    private int find(int x) {
        while (parent[x] != x) {
            // Path-compression line
            parent[x] = parent[parent[x]];
            x = parent[x];
        }
        return x;
    }
}
```

Looks magical; a GIF clarifies it (an extreme tree for clarity):

![](https://labuladong.online/algo/images/unionfind/9.gif)

Each loop iteration moves some children up; `find` walks to the root while compacting the tree.

Second form:

```java
class UF {
    // Earlier code omitted for brevity...
    
    // Second path-compression version of find
    public int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]);
        }
        return parent[x];
    }
}
```

I used to think this was equivalent to the iterative form, but a reader pointed out it actually compresses paths more thoroughly.

This recursion is a bit hard to grasp; trace it on paper. Iterative equivalent for clarity:

```java
// Iterative form to clarify the recursion's behavior
public int find(int x) {
    // Find the root
    int root = x;
    while (parent[root] != root) {
        root = parent[root];
    }
    // Then attach every node on the path directly to the root
    int old_parent = parent[x];
    while (x != root) {
        parent[x] = root;
        x = old_parent;
        old_parent = parent[old_parent];
    }
    return root;
}
```

The compression effect:

![](https://labuladong.online/algo/images/unionfind/10.jpeg)

Compared to the first version, this is more thorough — flattens the entire branch. Even a tall tree becomes flat after one pass. By [amortized analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/), all operations are O(1) on average. Recommended.

**With path compression, the `size` balancing optimization isn't necessary.** A typical Union-Find:

```java
class UF {
    // Number of connected components
    private int count;
    // Each node's parent
    private int[] parent;

    // n: number of nodes
    public UF(int n) {
        this.count = n;
        parent = new int[n];
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
    }
    
    // Connect p and q
    public void union(int p, int q) {
        int rootP = find(p);
        int rootQ = find(q);
        
        if (rootP == rootQ)
            return;
        
        parent[rootQ] = rootP;
        // Two components merge into one
        count--;
    }

    // Whether p and q are connected
    public boolean connected(int p, int q) {
        int rootP = find(p);
        int rootQ = find(q);
        return rootP == rootQ;
    }

    public int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]);
        }
        return parent[x];
    }

    // Number of connected components
    public int count() {
        return count;
    }
}
```

Complexity: constructor O(N) time and space; `union`, `connected`, `count` all O(1).

Recap of the optimizations:

1. `parent[x]` records each node's parent — pointers in array form. `parent` represents a forest (multiple multiway trees).

2. `size` records each tree's weight, keeping trees balanced after `union` — APIs are O(logN) instead of O(N).

3. Path compression in `find` keeps tree heights constant — APIs are O(1). With path compression, `size` balancing is unnecessary.

> [!TIP]
> Most coding tests let you use your own IDE; have a `UF` class ready in your favorite language so you don't waste time rewriting it on the spot.


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Kruskal MST Algorithm](https://labuladong.online/algo/data-structure/kruskal/)
 - [Prim MST Algorithm](https://labuladong.online/algo/data-structure/prim/)
 - [Union Find Principles](https://labuladong.online/algo/data-structure-basic/union-find-basic/)
 - [[Practice] Classic BFS Problems II](https://labuladong.online/algo/problem-set/bfs-ii/)
 - [[Practice] Classic Union-Find Problems](https://labuladong.online/algo/problem-set/union-find/)
 - [[Practice] Level-Order Traversal II](https://labuladong.online/algo/problem-set/binary-tree-level-ii/)
 - [Sweeping All Island Problems](https://labuladong.online/algo/frequency-interview/island-dfs-summary/)
 - [Binary Tree Basics and Common Types](https://labuladong.online/algo/data-structure-basic/binary-tree-basic/)
 - [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Beat Algorithms with Algorithms](https://labuladong.online/algo/fname.html?fname=algorithm-in-pdf)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1361. Validate Binary Tree Nodes](https://leetcode.com/problems/validate-binary-tree-nodes/?show=1) | [1361. Validate Binary Tree Nodes](https://leetcode.cn/problems/validate-binary-tree-nodes/?show=1) | 🟠 |
| [200. Number of Islands](https://leetcode.com/problems/number-of-islands/?show=1) | [200. Number of Islands](https://leetcode.cn/problems/number-of-islands/?show=1) | 🟠 |
| [261. Graph Valid Tree](https://leetcode.com/problems/graph-valid-tree/?show=1)🔒 | [261. Graph Valid Tree](https://leetcode.cn/problems/graph-valid-tree/?show=1)🔒 | 🟠 |
| [310. Minimum Height Trees](https://leetcode.com/problems/minimum-height-trees/?show=1) | [310. Minimum Height Trees](https://leetcode.cn/problems/minimum-height-trees/?show=1) | 🟠 |
| [368. Largest Divisible Subset](https://leetcode.com/problems/largest-divisible-subset/?show=1) | [368. Largest Divisible Subset](https://leetcode.cn/problems/largest-divisible-subset/?show=1) | 🟠 |
| [547. Number of Provinces](https://leetcode.com/problems/number-of-provinces/?show=1) | [547. Number of Provinces](https://leetcode.cn/problems/number-of-provinces/?show=1) | 🟠 |
| [582. Kill Process](https://leetcode.com/problems/kill-process/?show=1)🔒 | [582. Kill Process](https://leetcode.cn/problems/kill-process/?show=1)🔒 | 🟠 |
| [737. Sentence Similarity II](https://leetcode.com/problems/sentence-similarity-ii/?show=1)🔒 | [737. Sentence Similarity II](https://leetcode.cn/problems/sentence-similarity-ii/?show=1)🔒 | 🟠 |
| [765. Couples Holding Hands](https://leetcode.com/problems/couples-holding-hands/?show=1) | [765. Couples Holding Hands](https://leetcode.cn/problems/couples-holding-hands/?show=1) | 🔴 |
| [924. Minimize Malware Spread](https://leetcode.com/problems/minimize-malware-spread/?show=1) | [924. Minimize Malware Spread](https://leetcode.cn/problems/minimize-malware-spread/?show=1) | 🔴 |
| [947. Most Stones Removed with Same Row or Column](https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/?show=1) | [947. Most Stones Removed with Same Row or Column](https://leetcode.cn/problems/most-stones-removed-with-same-row-or-column/?show=1) | 🟠 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
