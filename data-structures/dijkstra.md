# Dijkstra Algorithm Template and Applications



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Graph Basics and General Implementation](https://labuladong.online/algo/data-structure-basic/graph-basic/)
> - [DFS/BFS Traversal of Binary Trees](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/)
> - [DFS/BFS Traversal of Graphs](https://labuladong.online/algo/data-structure-basic/graph-traverse-basic/)

> [!IMPORTANT]
> Dijkstra is an algorithm for the single-source shortest path in a graph. It is essentially a specially adapted BFS, with two modifications:
> 
> 1. Use a [priority queue](https://labuladong.online/algo/data-structure-basic/binary-heap-implement/) instead of a regular queue for the BFS.
> 
> 2. Add a memo recording the shortest path weight from the source to every reachable node.

Before learning Dijkstra's shortest-path algorithm, please first read [Graph Basics and General Code Implementation](https://labuladong.online/algo/data-structure-basic/graph-basic/) — the explanation below uses the generic `Graph` API.

You also need to understand the BFS basics in [DFS/BFS Traversal of Binary Trees](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/) and [DFS/BFS Traversal of Graphs](https://labuladong.online/algo/data-structure-basic/graph-traverse-basic/), since Dijkstra is essentially a modified BFS.

When introducing BFS over binary trees and graphs, I gave three styles of BFS code; review them if needed.

The third style is slightly more complex but the most flexible — it introduces a `State` class so each node can carry extra information.

```java
// Multiway-tree level-order traversal
// Each node carries a State containing its depth, etc.
class State {
    Node node;
    int depth;

    public State(Node node, int depth) {
        this.node = node;
        this.depth = depth;
    }
}

void levelOrderTraverse(Node root) {
    if (root == null) {
        return;
    }
    Queue<State> q = new LinkedList<>();
    // Track the current depth (root is depth 1)
    q.offer(new State(root, 1));

    while (!q.isEmpty()) {
        State state = q.poll();
        Node cur = state.node;
        int depth = state.depth;
        // Visit cur and know its depth
        System.out.println("depth = " + depth + ", val = " + cur.val);

        for (Node child : cur.children) {
            q.offer(new State(child, depth + 1));
        }
    }
}


// BFS on a graph from node s, also tracking accumulated weight
// Each node carries a State recording the weight from s
class State {
    // Current node ID
    int node;
    // Accumulated weight from start s to this node
    int weight;

    public State(int node, int weight) {
        this.node = node;
        this.weight = weight;
    }
}


void bfs(Graph graph, int s) {
    boolean[] visited = new boolean[graph.size()];
    Queue<State> q = new LinkedList<>();

    q.offer(new State(s, 0));
    visited[s] = true;

    while (!q.isEmpty()) {
        State state = q.poll();
        int cur = state.node;
        int weight = state.weight;
        System.out.println("visit " + cur + " with path weight " + weight);
        for (Edge e : graph.neighbors(cur)) {
            if (!visited[e.to]) {
                q.offer(new State(e.to, weight + e.weight));
                visited[e.to] = true;
            }
        }
    }
}
```

Overkill for trees, but very useful for weighted graphs.

<visual slug="graph-node-bfs-traverse3" >

The visualization panel sets up a weighted graph. Click the <code type="click">console.log</code> line repeatedly and watch the output: this style lets you, while traversing a node, also know the path weight from the source.

</visual>

The Dijkstra algorithm we're about to implement extends this idea: each node records the shortest path weight from the source, and combined with a [priority queue](https://labuladong.online/algo/data-structure-basic/binary-heap-implement/) — a structure that orders elements dynamically — we can compute shortest paths efficiently.

Now let's lay out a generic Dijkstra implementation.







## Dijkstra Function Signature

A generic signature:

```java
// Given a graph and a source start, compute the shortest distance from start to every other node
int[] dijkstra(int start, Graph graph);
```

Input: a graph `graph` and a source `start`. Output: an array of shortest path weights. Example:

```java
int[] distTo = dijkstra(3, graph);
```

`distTo` records the shortest weight sums from node `3` to every other node — e.g. the shortest weight from `3` to `6` is `distTo[6]`.

Since this is essentially BFS, the standard Dijkstra explores from `start` and computes the shortest path to every reachable node.

If you only need the shortest path from `start` to a specific `end`, a small tweak makes it more efficient — covered later.

## The `State` Class

We need a `State` class to assist BFS. For clarity, `id` is the current node ID and `distFromStart` is the distance from the source to the current node.

```java
class State {
    // Graph node id
    int id;
    // Distance from start to this node
    int distFromStart;

    State(int id, int distFromStart) {
        this.id = id;
        this.distFromStart = distFromStart;
    }
}
```

## `distTo` Records the Shortest Path

Dijkstra on weighted graphs differs from BFS on unweighted graphs: the first time we reach a node is not necessarily the shortest path. We may visit the same node multiple times with different `distFromStart`. For example:

![](https://labuladong.online/algo/images/dijkstra/3.jpeg)

Suppose I visit node `5` three times with different `distFromStart` each time. The smallest of those is the shortest path weight from `start` to `5`.

So we need a `distTo` array recording the shortest weight from `start` to each node — like a memo.

When the same node is reached again, compare the current `distFromStart` to `distTo`: if smaller, update `distTo`; otherwise, no need to continue exploring along that path.

## Implementation

Pseudocode logic:

```java
// Given a graph and a source start, compute the shortest distance from start to every other node
int[] dijkstra(int start, Graph graph) {
    // Number of nodes in the graph
    int V = graph.size();
    // Records the shortest path weights — think of it as a dp table
    // distTo[i] is the shortest weight from start to node i
    int[] distTo = new int[V];
    // Minimum problem; initialize dp table to +infinity
    Arrays.fill(distTo, Integer.MAX_VALUE);
    // Base case: start to start has distance 0
    distTo[start] = 0;

    // Priority queue: smaller distFromStart first
    Queue<State> pq = new PriorityQueue<>((a, b) -> {
        return a.distFromStart - b.distFromStart;
    });

    // BFS from start
    pq.offer(new State(start, 0));

    while (!pq.isEmpty()) {
        State curState = pq.poll();
        int curNodeID = curState.id;
        int curDistFromStart = curState.distFromStart;

        if (curDistFromStart > distTo[curNodeID]) {
            // A shorter path to curNode has already been found
            continue;
        }
        // Enqueue cur's neighbors
        for (int nextNodeID : graph.neighbors(curNodeID)) {
            // See if going through curNode to nextNode is shorter
            int distToNextNode = distTo[curNodeID] + graph.weight(curNodeID, nextNodeID);
            if (distTo[nextNodeID] > distToNextNode) {
                // Update dp table
                distTo[nextNodeID] = distToNextNode;
                // Enqueue this node along with its distance
                pq.offer(new State(nextNodeID, distToNextNode));
            }
        }
    }
    return distTo;
}
```

You may have the following questions vs. plain BFS:

**1. There's no `visited` set, so a node may be visited and enqueued multiple times. Won't the queue never empty (infinite loop)?**

**2. Why a `PriorityQueue` instead of a regular queue (`LinkedList`)? Why order by `distFromStart`?**

**3. If we only want the shortest path from `start` to a specific `end`, can we modify the algorithm to be faster?**

First, why no infinite loop without `visited`?

Useful general technique: the loop ends when the queue is empty, so watch when elements are added (`offer`) and removed (`poll`).

Each `while` iteration removes one element, but adding is constrained:

```java
// Only enqueue if going through curNode shortens the distance to nextNode
if (distTo[nextNodeID] > distToNextNode) {
    // Update dp table
    distTo[nextNodeID] = distToNextNode;
    pq.offer(new State(nextNodeID, distToNextNode));
}
```

That's why I describe `distTo` as a dp table: the algorithm continually minimizes its values:

If you can shorten the distance to `nextNodeID`, update `distTo[nextNodeID]` and enqueue; otherwise, don't enqueue.

**Because the shortest distance between two nodes is bounded — it cannot decrease without limit — the queue will eventually empty. Once it does, `distTo` holds the shortest distances from `start` to all other nodes.**

Second, why `PriorityQueue` and not a regular `LinkedList`?

A regular queue would also produce a correct answer, but with much worse efficiency.

**Dijkstra uses a priority queue mainly for an efficiency optimization, like a greedy strategy.**

Why "greedy"? Suppose we're computing the shortest path from `start` to `end`:

![](https://labuladong.online/algo/images/dijkstra/4.jpeg)

Suppose so far we've explored these nodes; which one to expand next? All three paths could be part of a shortest path, **but which one looks most "promising"**?

The orange path looks most promising, so we want node `2` near the front of the queue — to be popped first.

Hence we use `PriorityQueue` with smaller `distFromStart` at the front. Similar to the [greedy](https://labuladong.online/algo/essential-technique/greedy/) idea, this can speed things up significantly.

You may have heard of Bellman-Ford — a more general shortest-path algorithm that handles negative edge weights. Bellman-Ford's logic is similar but uses a regular queue. I'll write more about it later.

Third, optimizing for a fixed `end`:

Yes — the standard algorithm computes shortest paths to all nodes, so restricting to one target reduces work.

The change is small — modify the signature and add an `if` check:

```java
// Given start and end, compute the shortest distance between them
int dijkstra(int start, int end, List<Integer>[] graph) {

    // ...

    while (!pq.isEmpty()) {
        State curState = pq.poll();
        int curNodeID = curState.id;
        int curDistFromStart = curState.distFromStart;

        // Add a check here; nothing else changes
        if (curNodeID == end) {
            return curDistFromStart;
        }

        if (curDistFromStart > distTo[curNodeID]) {
            continue;
        }

        // ...
    }

    // If we get here, end is unreachable from start
    return Integer.MAX_VALUE;
}
```

Because the priority queue auto-sorts, **every** poll yields the smallest `distFromStart` so far — so the **first** time we pop `end`, the corresponding `distFromStart` is the shortest distance from `start` to `end`.

This early-return version is faster.

Dijkstra visualization — click the code to see execution:


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/dijkstra-example/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌟 Animated Code Visualization 🌟</strong>
</summary>
</details>
</a>
<hr/>

## Time Complexity Analysis

What is Dijkstra's time complexity? Online sources often say $O(E \log V)$, where `E` is the number of edges and `V` the number of nodes.

Ideally, the priority queue holds at most `V` nodes, with operations proportional to `E`, giving $O(E \log V)$.

That's the ideal. Different implementations and language APIs change this somewhat.

For instance, this article uses Java's `PriorityQueue`, which is a binary heap without an "operate-by-index" API. So the queue may contain duplicate entries — up to `E` of them.

So this implementation's complexity isn't $O(E \log V)$ but $O(E \log E)$, slightly larger because edge count typically exceeds node count.

But logarithms grow slowly, so the practical performance is still very good. The above is just a theoretical reference.

In the next section, [Dijkstra Practice Problems](https://labuladong.online/algo/problem-set/dijkstra/), we'll apply Dijkstra to concrete problems.







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Kruskal MST Algorithm](https://labuladong.online/algo/data-structure/kruskal/)
 - [Prim MST Algorithm](https://labuladong.online/algo/data-structure/prim/)
 - [[Practice] Classic BFS Problems II](https://labuladong.online/algo/problem-set/bfs-ii/)
 - [[Practice] Classic Dijkstra Problems](https://labuladong.online/algo/problem-set/dijkstra/)
 - [Bipartite Graph Detection](https://labuladong.online/algo/data-structure/bipartite-graph/)
 - [Recursive / Level-Order Traversal of Binary Trees](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/)
 - [Binary-Tree Algorithm Core Outline](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
 - [Graph Basics and General Code Implementation](https://labuladong.online/algo/data-structure-basic/graph-basic/)
 - [DFS/BFS Traversal of Graphs](https://labuladong.online/algo/data-structure-basic/graph-traverse-basic/)
 - [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Travel Cost Saving: Weighted Shortest Path](https://labuladong.online/algo/dynamic-programming/cheap-travel/)
 - [Cycle Detection and Topological Sort](https://labuladong.online/algo/data-structure/topological-sort/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1514. Path with Maximum Probability](https://leetcode.com/problems/path-with-maximum-probability/?show=1) | [1514. Path with Maximum Probability](https://leetcode.cn/problems/path-with-maximum-probability/?show=1) | 🟠 |
| [1631. Path With Minimum Effort](https://leetcode.com/problems/path-with-minimum-effort/?show=1) | [1631. Path With Minimum Effort](https://leetcode.cn/problems/path-with-minimum-effort/?show=1) | 🟠 |
| [286. Walls and Gates](https://leetcode.com/problems/walls-and-gates/?show=1)🔒 | [286. Walls and Gates](https://leetcode.cn/problems/walls-and-gates/?show=1)🔒 | 🟠 |
| [310. Minimum Height Trees](https://leetcode.com/problems/minimum-height-trees/?show=1) | [310. Minimum Height Trees](https://leetcode.cn/problems/minimum-height-trees/?show=1) | 🟠 |
| [329. Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/?show=1) | [329. Longest Increasing Path in a Matrix](https://leetcode.cn/problems/longest-increasing-path-in-a-matrix/?show=1) | 🔴 |
| [505. The Maze II](https://leetcode.com/problems/the-maze-ii/?show=1)🔒 | [505. The Maze II](https://leetcode.cn/problems/the-maze-ii/?show=1)🔒 | 🟠 |
| [542. 01 Matrix](https://leetcode.com/problems/01-matrix/?show=1) | [542. 01 Matrix](https://leetcode.cn/problems/01-matrix/?show=1) | 🟠 |
| [743. Network Delay Time](https://leetcode.com/problems/network-delay-time/?show=1) | [743. Network Delay Time](https://leetcode.cn/problems/network-delay-time/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
