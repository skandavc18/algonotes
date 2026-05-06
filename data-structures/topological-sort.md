# Cycle Detection and Topological Sort



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [207. Course Schedule](https://leetcode.com/problems/course-schedule/) | [207. Course Schedule](https://leetcode.cn/problems/course-schedule/) | 🟠 |
| [210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) | [210. Course Schedule II](https://leetcode.cn/problems/course-schedule-ii/) | 🟠 |

**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Graph Basics and General Implementation](https://labuladong.online/algo/data-structure-basic/graph-basic/)
> - [DFS/BFS Traversal of Graphs](https://labuladong.online/algo/data-structure-basic/graph-traverse-basic/)

> [!IMPORTANT]
> Topological sort can only be performed on a graph without cycles.
> 
> The reverse of a graph's postorder traversal is its topological-sort result.

There are several special graph algorithms — bipartite detection, cycle detection (acyclic check), topological sort, the classic minimum spanning tree, single-source shortest path, and more advanced ones like network flow.

If you aren't doing competitions, network flow and similar topics can be skipped. [MST](https://labuladong.online/algo/data-structure/prim/) and [shortest path](https://labuladong.online/algo/data-structure/dijkstra/) are classic but rarely seen in problems — learn them if you have time. [Bipartite detection](https://labuladong.online/algo/data-structure/bipartite-graph/) and topological sort are essentially graph traversals — basic and worth mastering.

**This article uses concrete problems to discuss two graph algorithms: cycle detection in directed graphs and topological sort.**

Both can be solved with DFS or BFS. The BFS code is somewhat shorter, but the DFS approach helps you understand recursive traversal more deeply. We'll start with DFS and then do BFS.

## Cycle Detection (DFS Version)

LeetCode 207 "Course Schedule":

<Problem slug="course-schedule" />

```java
// Signature
boolean canFinish(int numCourses, int[][] prerequisites);
```

When can't all courses be finished? When circular dependencies exist.

This scenario is common in real life — when writing code, importing packages requires a sane directory structure to avoid circular dependencies; compilers use similar algorithms to detect this.

**On any dependency problem, the natural model is a directed graph: a cycle means circular dependencies.**

We treat courses as graph nodes (numbered `0, 1, ..., numCourses-1`) and dependencies as directed edges.

For example, if course `1` must be finished before course `3`, there's a directed edge from `1` to `3`.

So we build a graph from `prerequisites`:

![](https://labuladong.online/algo/images/topological-sort/1.jpeg)

**If the graph has a cycle, you can't finish everything; otherwise you can.**

So we first build the graph from the input, then check for cycles.

How to build the graph? In [Graph Storage](https://labuladong.online/algo/data-structure-basic/graph-basic/) we covered the two forms — adjacency matrix and adjacency list.

Here we'll use an adjacency list. The graph-build function:

```java
List<Integer>[] buildGraph(int numCourses, int[][] prerequisites) {
    // numCourses nodes
    List<Integer>[] graph = new LinkedList[numCourses];
    for (int i = 0; i < numCourses; i++) {
        graph[i] = new LinkedList<>();
    }
    for (int[] edge : prerequisites) {
        int from = edge[1], to = edge[0];
        // Add a directed edge from -> to
        // Direction is "is depended on": finish from before to
        graph[from].add(to);
    }
    return graph;
}
```

How to detect a cycle?

Easy — traverse all paths in the graph; whether a cycle exists falls out naturally.

[DFS/BFS Graph Traversal Basics](https://labuladong.online/algo/data-structure-basic/graph-traverse-basic/) covers all-paths DFS — review if needed.

We adapt the all-paths DFS template, using `hasCycle` to record cycle existence: **when we revisit a node already in `onPath`, we've found a cycle, set `hasCycle = true`**.

First version (will TLE):

```java
class Solution {
    // Nodes currently on the recursion stack
    boolean[] onPath;
    // Whether a cycle exists
    boolean hasCycle = false;

    public boolean canFinish(int numCourses, int[][] prerequisites) {
        List<Integer>[] graph = buildGraph(numCourses, prerequisites);
        
        onPath = new boolean[numCourses];
        
        for (int i = 0; i < numCourses; i++) {
            // Traverse all nodes
            traverse(graph, i);
        }
        // Can finish iff there is no cycle
        return !hasCycle;
    }

    // Graph traversal: visits all paths
    void traverse(List<Integer>[] graph, int s) {
        if (hasCycle) {
            // Already found a cycle; no need to keep going
            return;
        }

        if (onPath[s]) {
            // s is on the current recursion path -> cycle
            hasCycle = true;
            return;
        }
        
        // Preorder
        onPath[s] = true;
        for (int t : graph[s]) {
            traverse(graph, t);
        }
        // Postorder
        onPath[s] = false;
    }

    List<Integer>[] buildGraph(int numCourses, int[][] prerequisites) {
        // See above
    }
}
```

Note: not all nodes are connected, so we call DFS from every node as a starting point.

This solution is logically correct — visiting all paths definitively detects cycles. But it TLEs. The reason: **redundant work**.

Where? Suppose we DFS from node `2` and find no cycles in any reachable path.

Now suppose another node `5` has an edge to `2`. When we DFS from `5`, we reach `2` again. Do we still need to explore all paths from `2`?

No — we already know there's no cycle reachable from `2`. So skip nodes already explored.

Optimized:

```java
class Solution {
    // Nodes on the current recursion path
    boolean[] onPath;
    // Whether a node has been visited
    boolean[] visited;
    // Whether a cycle exists
    boolean hasCycle = false;

    public boolean canFinish(int numCourses, int[][] prerequisites) {
        List<Integer>[] graph = buildGraph(numCourses, prerequisites);
        
        onPath = new boolean[numCourses];
        visited = new boolean[numCourses];
        
        for (int i = 0; i < numCourses; i++) {
            traverse(graph, i);
        }
        return !hasCycle;
    }

    void traverse(List<Integer>[] graph, int s) {
        if (hasCycle) {
            return;
        }

        if (onPath[s]) {
            // Cycle
            hasCycle = true;
            return;
        }
        
        if (visited[s]) {
            // No need to revisit
            return;
        }

        // Preorder
        visited[s] = true;
        onPath[s] = true;
        for (int t : graph[s]) {
            traverse(graph, t);
        }
        // Postorder
        onPath[s] = false;
    }


    List<Integer>[] buildGraph(int numCourses, int[][] prerequisites) {
        // See above
    }
}
```

<visual slug='course-schedule'>

Visited nodes are green; nodes in `onPath` are orange.

Click <code type="click">if (onPath[s])</code> repeatedly to step through the DFS.

</visual>



Done — the core is detecting a cycle in a directed graph.

What if we also need to identify which nodes form the cycle?

You might think the indices where `onPath` is true are the cycle. But not quite — starting from node `0` for example, the green nodes are the recursion path with `onPath` = true, but only some of them form the cycle:

![](https://labuladong.online/algo/images/topological-sort/4.jpeg)

There are several solutions; here's a common one.

> [!NOTE]
> Simplest: maintain a `Stack<Integer> path` alongside `boolean[] onPath` to record the order of nodes on the recursion path.
> 
> For the path above, `path` from bottom to top is `[0,4,5,9,8,7,6]`. When we re-encounter `5`, we know `[5,9,8,7,6]` is the cycle.

Now let's do topological sort.

## Topological Sort (DFS Version)

LeetCode 210 "Course Schedule II":

<Problem slug="course-schedule-ii" />

A continuation: not just whether all courses can be finished, but a valid order such that every course's prerequisites come before it.

```java
int[] findOrder(int numCourses, int[][] prerequisites);
```

A quick word on topological sort. Online definitions are mathematical; here's a picture from Baidu Baike:

![](https://labuladong.online/algo/images/topological-sort/top.jpg)

**Visually: lay the graph flat such that all edges point in the same direction** (in the figure, all rightward).

A graph with a cycle can't be topologically sorted. A directed acyclic graph (DAG) can.

How does this relate to our problem? **Treat courses as nodes and dependencies as directed edges; the topological order of this graph is a valid course order.**

First, check for a cycle (no cycle, no topological sort), reusing the previous solution:

```java
public int[] findOrder(int numCourses, int[][] prerequisites) {
    if (!canFinish(numCourses, prerequisites)) {
        // Impossible to finish all courses
        return new int[]{};
    }
    // ...
}
```

Now the key: how to topologically sort?

**Reverse the postorder traversal of the graph.**

::: note Why reverse?

Some readers see DFS topological-sort code online that doesn't reverse postorder — why?

It's because of how their graph is built. In my graph, an edge `1 -> 2` means "1 is depended on by 2" (finish 1 before 2). This matches our intuition.

If you flip the convention so an edge encodes "depends on", the entire graph's edges flip and you can omit the reversal. Concretely, change `graph[from].add(to);` to `graph[to].add(from);` and skip the reverse.

:::

Code, on top of the cycle-detection version, recording postorder:

```java
class Solution {
    // Postorder result
    List<Integer> postorder = new ArrayList<>();
    // Cycle flag
    boolean hasCycle = false;
    boolean[] visited, onPath;

    // Main
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        List<Integer>[] graph = buildGraph(numCourses, prerequisites);
        visited = new boolean[numCourses];
        onPath = new boolean[numCourses];
        // Traverse the graph
        for (int i = 0; i < numCourses; i++) {
            traverse(graph, i);
        }
        // Cyclic graph has no topological order
        if (hasCycle) {
            return new int[]{};
        }
        // Reverse postorder is the topological sort
        Collections.reverse(postorder);
        int[] res = new int[numCourses];
        for (int i = 0; i < numCourses; i++) {
            res[i] = postorder.get(i);
        }
        return res;
    }

    void traverse(List<Integer>[] graph, int s) {
        if (onPath[s]) {
            // Cycle
            hasCycle = true;
        }
        if (visited[s] || hasCycle) {
            return;
        }
        // Preorder
        onPath[s] = true;
        visited[s] = true;
        for (int t : graph[s]) {
            traverse(graph, t);
        }
        // Postorder
        postorder.add(s);
        onPath[s] = false;
    }

    // Build graph
    List<Integer>[] buildGraph(int numCourses, int[][] prerequisites) {
        // See above
    }
}
```

<visual slug='course-schedule-ii'>

Visited nodes are green; nodes in `onPath` are orange.

Click <code type="click">if (onPath[s])</code> repeatedly to step through the DFS.

</visual>



The code is longer but the logic is clear: if no cycle, DFS the graph, record postorder, reverse it.

**Why does reverse postorder give a topological order?**

Skipping math; let's use binary trees:

```java
void traverse(TreeNode root) {
    // Preorder
    traverse(root.left)
    // Inorder
    traverse(root.right)
    // Postorder
}
```

When does postorder execute? After both subtrees are done — i.e., after all child nodes are added to the result list, the root is added.

**This is why postorder is the basis for topological sort: a task can start only after all its dependencies are done.**

Treat the binary tree as a directed graph with edges from parent to children:

![](https://labuladong.online/algo/images/topological-sort/2.jpeg)

In standard postorder the root appears last; reversing it gives the topological order:

![](https://labuladong.online/algo/images/topological-sort/3.jpeg)

Some ask: how is reverse-postorder related to preorder?

For binary trees they may look related, but they aren't identical. Don't assume reverse-postorder equals preorder.

The key difference is in [Binary Tree Tactics (Outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/): postorder runs only after subtrees are processed — it captures dependency. Other orders don't.








## Cycle Detection (BFS Version)

So far DFS with `onPath`, and DFS topological sort with reverse postorder.

BFS can do both using an `indegree` array. Refer to [BFS Core Framework](https://labuladong.online/algo/essential-technique/bfs-framework/) if needed.

In a directed graph, "out-degree" / "in-degree" are intuitive: if `x` has `a` outgoing edges and `b` incoming edges, its out-degree is `a` and its in-degree is `b`.

Cycle detection (BFS):

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        // Build graph; edges represent "is depended on"
        List<Integer>[] graph = buildGraph(numCourses, prerequisites);
        // Build the in-degree array
        int[] indegree = new int[numCourses];
        for (int[] edge : prerequisites) {
            int from = edge[1], to = edge[0];
            // to's in-degree increments
            indegree[to]++;
        }

        // Initialize the queue with all zero-in-degree nodes
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (indegree[i] == 0) {
                // No incoming edges -> no dependencies; can be a starting point
                q.offer(i);
            }
        }

        // Count of visited nodes
        int count = 0;
        // BFS
        while (!q.isEmpty()) {
            // Pop cur and decrement neighbors' in-degree
            int cur = q.poll();
            count++;
            for (int next : graph[cur]) {
                indegree[next]--;
                if (indegree[next] == 0) {
                    // All next's dependencies are visited
                    q.offer(next);
                }
            }
        }

        // If all nodes were visited, there is no cycle
        return count == numCourses;
    }

    List<Integer>[] buildGraph(int n, int[][] edges) {
        // See above
    }
}
```

Summary of the BFS approach:

1. Build the adjacency list as before; edges encode "is depended on".

2. Build `indegree` recording each node's in-degree.

3. Initialize the BFS queue with all zero-in-degree nodes.

**4. BFS: pop a node, decrement neighbors' in-degree, enqueue any that drop to zero.**

**5. If all nodes are visited (`count == numCourses`), no cycle; otherwise, a cycle exists.**

A figure makes this clearer. Numbers in the nodes show in-degree:

![](https://labuladong.online/algo/images/topological-sort/5.jpeg)

After init, zero-in-degree nodes are enqueued:

![](https://labuladong.online/algo/images/topological-sort/6.jpeg)

BFS pops a node, decrements neighbors, enqueues new zero-in-degree nodes:

![](https://labuladong.online/algo/images/topological-sort/7.jpeg)

Continue popping; this round no new zero-in-degree nodes:

![](https://labuladong.online/algo/images/topological-sort/8.jpeg)

Continue:

![](https://labuladong.online/algo/images/topological-sort/9.jpeg)

Until the queue is empty:

![](https://labuladong.online/algo/images/topological-sort/10.jpeg)

All nodes visited — no cycle.

Conversely, if some nodes remain unvisited, there's a cycle.

Example with an initial queue of one zero-in-degree node:

![](https://labuladong.online/algo/images/topological-sort/11.jpeg)

After popping it and decrementing neighbors, the queue is empty without producing new zero-in-degree nodes; BFS halts:

![](https://labuladong.online/algo/images/topological-sort/12.jpeg)

Some nodes never enter the queue — there's a cycle. With this picture the BFS code should be easy to follow.







## Topological Sort (BFS Version)

**If you understand BFS cycle detection, BFS topological sort is immediate — the order in which nodes leave the queue is the topological order.**

In the first BFS example, the values in the nodes show the enqueue order:

![](https://labuladong.online/algo/images/topological-sort/13.jpeg)

That order is a valid topological sort.

Just record the popped order:

```java
class Solution {

    public int[] findOrder(int numCourses, int[][] prerequisites) {
        // Build graph as before
        List<Integer>[] graph = buildGraph(numCourses, prerequisites);
        // In-degree as before
        int[] indegree = new int[numCourses];
        for (int[] edge : prerequisites) {
            int from = edge[1], to = edge[0];
            indegree[to]++;
        }

        // Initialize the queue
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (indegree[i] == 0) {
                q.offer(i);
            }
        }

        // Topological-sort result
        int[] res = new int[numCourses];
        // Order index
        int count = 0;
        // BFS
        while (!q.isEmpty()) {
            int cur = q.poll();
            // Pop order = topological order
            res[count] = cur;
            count++;
            for (int next : graph[cur]) {
                indegree[next]--;
                if (indegree[next] == 0) {
                    q.offer(next);
                }
            }
        }

        if (count != numCourses) {
            // There is a cycle; no topological sort
            return new int[]{};
        }
        
        return res;
    }

    List<Integer>[] buildGraph(int n, int[][] edges) {
        // See above
    }
}
```

Normally [graph traversals](https://labuladong.online/algo/data-structure-basic/graph-basic/) need a `visited` array to avoid revisiting. Here, the `indegree` array effectively serves as the visited mechanism — only zero-in-degree nodes enter the queue, so no infinite loops.

That's the BFS implementations of cycle detection and topological sort. A puzzle to ponder:

For the BFS cycle-detection algorithm, how would you identify the specific nodes that form the cycle?







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Kruskal MST Algorithm](https://labuladong.online/algo/data-structure/kruskal/)
 - [[Practice] Classic BFS Problems I](https://labuladong.online/algo/problem-set/bfs/)
 - [Graph Basics and General Code Implementation](https://labuladong.online/algo/data-structure-basic/graph-basic/)
 - [DFS/BFS Traversal of Graphs](https://labuladong.online/algo/data-structure-basic/graph-traverse-basic/)
 - [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Beat Algorithms with Algorithms](https://labuladong.online/algo/fname.html?fname=algorithm-in-pdf)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [310. Minimum Height Trees](https://leetcode.com/problems/minimum-height-trees/?show=1) | [310. Minimum Height Trees](https://leetcode.cn/problems/minimum-height-trees/?show=1) | 🟠 |
| - | [Sword to Offer II 113. Course Order](https://leetcode.cn/problems/QA2IGt/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
