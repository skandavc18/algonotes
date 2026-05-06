# BFS Algorithm Framework


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [752. Open the Lock](https://leetcode.com/problems/open-the-lock/) | [752. Open the Lock](https://leetcode.cn/problems/open-the-lock/) | 🟠 |
| [773. Sliding Puzzle](https://leetcode.com/problems/sliding-puzzle/) | [773. Sliding Puzzle](https://leetcode.cn/problems/sliding-puzzle/) | 🔴 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Recursive / Level-Order Traversal of Binary Trees](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/)
> - [Recursive / Level-Order Traversal of Multiway Trees](https://labuladong.online/algo/data-structure-basic/n-ary-tree-traverse-basic/)
> - [DFS/BFS Traversal of Graphs](https://labuladong.online/algo/data-structure-basic/graph-traverse-basic/)

I've stressed many times that DFS / backtracking / BFS are essentially abstractions of concrete problems into trees, then traversed via brute force. So this code is, at heart, tree traversal.

The causal chain:

DFS/backtracking is recursive traversal of a brute-force enumeration tree (multiway). Multiway-tree recursion is derived from binary-tree recursion. Hence DFS/backtracking is binary-tree recursion at heart.

BFS is graph traversal — and as you'll see, BFS's framework is the BFS code in [DFS/BFS Graph Traversal](https://labuladong.online/algo/data-structure-basic/graph-traverse-basic/).

Graph traversal is multiway-tree traversal plus a `visited` array to avoid cycles. Multiway-tree traversal is derived from binary-tree traversal. Hence BFS is binary-tree level-order traversal at heart.

Why is BFS often used for shortest paths? In [Recursive / Level-Order Binary Tree Traversal](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/), I covered the minimum-depth binary tree problem.

Shortest paths can be analogized to "min depth from root to nearest leaf". Recursion has to traverse the entire tree; level-order doesn't. So BFS suits this kind of shortest-path problem.

Clear?

Before reading on, ensure you've read [Binary Tree Traversal](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/), [Multiway-Tree Traversal](https://labuladong.online/algo/data-structure-basic/n-ary-tree-traverse-basic/), and [DFS/BFS Graph Traversal](https://labuladong.online/algo/data-structure-basic/graph-traverse-basic/) — get the basic traversals down first; everything else builds on them.

**This article focuses on abstracting and converting concrete problems and applying the BFS framework**.

In real interviews, problems usually aren't "traverse this tree/graph" — they're scenario problems. You abstract the scenario into a graph/tree and then BFS to enumerate the answer.

E.g., a maze: minimum steps to the exit? With teleporters? Two words: replace/insert/delete characters to transform; minimum operations? In a click-the-pair (Lianliankan) game, two tiles can be cleared if their pattern matches and the shortest connection has at most two corners — how does the game decide?

These don't seem to relate to trees/graphs, but a small abstraction reveals they are.

Let's go through examples.


## 1. The Framework

The BFS framework is the BFS graph-traversal code from [DFS/BFS Graph Traversal](https://labuladong.online/algo/data-structure-basic/graph-traverse-basic/) — three styles.

For practical problems, the first is simplest but limited; the second is most common and handles medium-difficulty BFS; the third is more complex but most flexible. The next chapter's [BFS Practice](https://labuladong.online/algo/problem-set/bfs/) has tougher problems using the third — try those later.

This article uses the second style:

```java
// BFS from s; record the number of steps
// Return the step count when target is reached
int bfs(int s, int target) {
    boolean[] visited = new boolean[graph.size()];
    Queue<Integer> q = new LinkedList<>();
    q.offer(s);
    visited[s] = true;
    // Steps from s to current node
    int step = 0;
    while (!q.isEmpty()) {
        int sz = q.size();
        for (int i = 0; i < sz; i++) {
            int cur = q.poll();
            System.out.println("visit " + cur + " at step " + step);
            // Reached target?
            if (cur == target) {
                return step;
            }
            // Add unvisited neighbors
            for (int to : neighborsOf(cur)) {
                if (!visited[to]) {
                    q.offer(to);
                    visited[to] = true;
                }
            }
        }
        step++;
    }
    // Target not reachable
    return -1;
}
```

Almost copy-paste from [DFS/BFS Graph Traversal](https://labuladong.online/algo/data-structure-basic/graph-traverse-basic/), with a `target` parameter; on first reaching `target`, return the steps.

Now apply it.

## 2. LeetCode 773 "Sliding Puzzle"

You're given a 2x3 sliding puzzle as `board`. Digits 0-5; **0 represents the empty cell**. Moving digits, when `board` becomes `[[1,2,3],[4,5,0]]`, you win. Compute the minimum moves to win, or -1 if impossible.

`board = [[4,1,2],[5,0,3]]` returns 5:

![](https://labuladong.online/algo/images/sliding_puzzle/5.jpeg)

`board = [[1,2,3],[5,4,0]]` returns -1.

I find this fun — reminds me of Hua Rong Dao as a kid:

![](https://labuladong.online/algo/images/sliding_puzzle/2.jpeg)

You move tiles to get Cao Cao from the start to the bottom exit.

Hua Rong Dao is harder — varying tile sizes; here all "tiles" are equal.

How do we abstract this into a tree/graph for BFS?

The initial board is the start:

```
[[2,4,1],
 [5,0,3]]
```

The goal:

```
[[1,2,3],
 [4,5,0]]
```

So this becomes a graph problem — minimum-path from start to goal.

Neighbors of a state? Swap 0 with one of its up/down/left/right neighbors:

![](https://labuladong.online/algo/images/sliding_puzzle/3.jpeg)

Each of those four states has its own neighbors — a graph.

BFS from the start; the first time we reach the goal, the step count is the answer.

Pseudocode:

```java
int bfs(int[][] board, int[][] target) {
    Queue<int[][]> q = new LinkedList<>();
    HashSet visited = new HashSet<>();

    // Push start
    q.offer(board);
    visited.add(board);

    int step = 0;
    while (!q.isEmpty()) {
        int sz = q.size();
        for (int i = 0; i < sz; i++) {
            int[][] cur = q.poll();
            // Reached target?
            if (cur == target) {
                return step;
            }
            // Add neighbors
            for (int[][] neighbor : getNeighbors(cur)) {
                if (!visited.contains(neighbor)) {
                    q.offer(neighbor);
                    visited.add(neighbor);
                }
            }
        }
        step++;
    }
    return -1;
}

List<int[][]> getNeighbors(int[][] board) {
    // Swap 0 with up/down/left/right and return up to 4 neighbor states
}
```

The graph has cycles (we can move 0 right, then left, returning to the same state), so we need `visited`.

E.g., `[[2,4,1],[5,0,3]]` → 0 right → `[[2,4,1],[5,3,0]]` → 0 left → `[[2,4,1],[5,0,3]]`. Cycle.

The board is a 2D array, which (per [Hash Map / Hash Set Principles](https://labuladong.online/algo/data-structure-basic/hashmap-basic/)) can't go directly into a hash set.

Workaround: serialize the 2D array to an immutable string and store that.

**The trick: 2D has up/down/left/right; once flattened to 1D, how do you swap 0 with its up/down/left/right neighbors?**

For 2x3, hard-code:

```java
// Adjacency for the flattened indices
int[][] neighbor = new int[][]{
    {1, 3},
    {0, 4, 2},
    {1, 5},
    {0, 4},
    {3, 1, 5},
    {4, 2}
};
```

**Meaning: in the 1D string, index `i`'s 2D-array neighbors are `neighbor[i]`**:

![](https://labuladong.online/algo/images/sliding_puzzle/4.jpeg)

:::: details For a general `m x n` array

Hand-coding is impractical; generate it programmatically.

Observe: if 2D element `e` is at flat index `i`, then `e`'s left/right neighbors are at `i - 1` / `i + 1`, and its top/bottom neighbors are at `i - n` / `i + n`, where `n` is the number of columns.

For `m x n`:

```java
int[][] generateNeighborMapping(int m, int n) {
    int[][] neighbor = new int[m * n][];
    for (int i = 0; i < m * n; i++) {
        List<Integer> neighbors = new ArrayList<>();

        // Not in the first column — has a left neighbor
        if (i % n != 0) neighbors.add(i - 1);
        
        // Not in the last column — has a right neighbor
        if (i % n != n - 1) neighbors.add(i + 1);
        
        // Not in the first row — has a top neighbor
        if (i - n >= 0) neighbors.add(i - n);
        
        // Not in the last row — has a bottom neighbor
        if (i + n < m * n) neighbors.add(i + n);

        // Java specifics: convert List to int[]
        neighbor[i] = neighbors.stream().mapToInt(Integer::intValue).toArray();
    }
    return neighbor;
}
```

::::


With the mapping, swap 0 with any neighbor by index. Full code:

```java
class Solution {
    public int slidingPuzzle(int[][] board) {
        String target = "123450";
        // Convert the 2x3 array into a string as the BFS start
        String start = "";
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                start = start + board[i][j];
            }
        }

        // ****** Begin BFS framework ******
        Queue<String> q = new LinkedList<>();
        HashSet<String> visited = new HashSet<>();
        // BFS from start
        q.offer(start);
        visited.add(start);

        int step = 0;
        while (!q.isEmpty()) {
            int sz = q.size();
            for (int i = 0; i < sz; i++) {
                String cur = q.poll();
                // Reached target?
                if (target.equals(cur)) {
                    return step;
                }
                // Swap 0 with a neighbor
                for (String neighborBoard : getNeighbors(cur)) {
                    // Avoid revisiting
                    if (!visited.contains(neighborBoard)) {
                        q.offer(neighborBoard);
                        visited.add(neighborBoard);
                    }
                }
            }
            step++;
        }
        // ****** End BFS framework ******
        return -1;
    }

    private List<String> getNeighbors(String board) {
        // Adjacency for the flattened indices
        int[][] mapping = new int[][]{
                {1, 3},
                {0, 4, 2},
                {1, 5},
                {0, 4},
                {3, 1, 5},
                {4, 2}
        };

        int idx = board.indexOf('0');
        List<String> neighbors = new ArrayList<>();
        for (int adj : mapping[idx]) {
            String new_board = swap(board.toCharArray(), adj, idx);
            neighbors.add(new_board);
        }
        return neighbors;
    }

    private String swap(char[] chars, int i, int j) {
        char temp = chars[i];
        chars[i] = chars[j];
        chars[j] = temp;
        return new String(chars);
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/sliding-puzzle/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Animated Code Visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>


Done. BFS itself is mechanical — the hard part is converting the problem into a BFS model and serializing the board for hashing.

Another scenario problem.

## 3. Minimum Moves to Open a Combination Lock

LeetCode 752 "Open the Lock":

<Problem slug="open-the-lock" />

```java
int openLock(String[] deadends, String target)
```

A typical combination lock. Without constraints, minimum moves is easy: to reach `"1234"`, just turn each dial — 1+2+3+4 = 10 moves.

The twist: avoid `deadends`. How to handle them while minimizing moves?

Don't get lost in details. The essence of any algorithm is enumeration — start from `"0000"`, brute-force every possible turning sequence; finding the minimum is then trivial.

**Step 1: ignore the constraints. How would you enumerate all possible combinations starting from `"0000"`?**

With one turn: 4 dials × 2 directions = 8 possibilities (`"1000", "9000", "0100", "0900"...`).

Each of those 8 yields 8 more, etc.

That's an octenary tree; each node has 8 children.

Pseudocode for level-order traversal:

```java
// Turn s[j] up by one
String plusOne(String s, int j) {
    char[] ch = s.toCharArray();
    if (ch[j] == '9')
        ch[j] = '0';
    else
        ch[j] += 1;
    return new String(ch);
}
// Turn s[i] down by one
String minusOne(String s, int j) {
    char[] ch = s.toCharArray();
    if (ch[j] == '0')
        ch[j] = '9';
    else
        ch[j] -= 1;
    return new String(ch);
}

// BFS for the minimum number of turns
void BFS(String target) {
    Queue<String> q = new LinkedList<>();
    q.offer("0000");

    int step = 0;

    while (!q.isEmpty()) {
        int sz = q.size();
        // Expand the current frontier
        for (int i = 0; i < sz; i++) {
            String cur = q.poll();
            // Reached target?
            if (cur.equals(target)) {
                return step;
            }

            // 8 neighbors
            for (String neighbor : getNeighbors(cur)) {
                q.offer(neighbor);
            }
        }
        step++;
    }
}
// Up or down on each digit gives 8 neighbors
List<String> getNeighbors(String s) {
    List<String> neighbors = new ArrayList<>();
    for (int i = 0; i < 4; i++) {
        neighbors.add(plusOne(s, i));
        neighbors.add(minusOne(s, i));
    }
    return neighbors;
}
```

Some issues remain.

1. We can revisit: `"0000"` → `"1000"` → `"0000"` → infinite loop.

This is just a cycle; use a `visited` set.

2. We haven't handled `deadends`.

Use a `deadends` set; skip them when adding neighbors. Or simpler: pre-populate `visited` with `deadends`.

Final code:

```java
class Solution {
    public int openLock(String[] deadends, String target) {
        // Dead combinations to skip
        Set<String> deads = new HashSet<>();
        for (String s : deadends) deads.add(s);
        if (deads.contains("0000")) return -1;

        // Visited set to avoid revisiting
        Set<String> visited = new HashSet<>();
        Queue<String> q = new LinkedList<>();
        // BFS from start
        int step = 0;
        q.offer("0000");
        visited.add("0000");
        
        while (!q.isEmpty()) {
            int sz = q.size();
            // Expand frontier
            for (int i = 0; i < sz; i++) {
                String cur = q.poll();
                
                // Reached target?
                if (cur.equals(target))
                    return step;
                
                // Add valid neighbors
                for (String neighbor : getNeighbors(cur)) {
                    if (!visited.contains(neighbor) && !deads.contains(neighbor)) {
                        q.offer(neighbor);
                        visited.add(neighbor);
                    }
                }
            }
            step++;
        }
        // Not found
        return -1;
    }

    String plusOne(String s, int j) {
        char[] ch = s.toCharArray();
        if (ch[j] == '9')
            ch[j] = '0';
        else
            ch[j] += 1;
        return new String(ch);
    }

    String minusOne(String s, int j) {
        char[] ch = s.toCharArray();
        if (ch[j] == '0')
            ch[j] = '9';
        else
            ch[j] -= 1;
        return new String(ch);
    }

    List<String> getNeighbors(String s) {
        List<String> neighbors = new ArrayList<>();
        for (int i = 0; i < 4; i++) {
            neighbors.add(plusOne(s, i));
            neighbors.add(minusOne(s, i));
        }
        return neighbors;
    }
}
```

## 4. Bidirectional BFS Optimization

A BFS optimization: **bidirectional BFS**, faster than ordinary BFS.

Treat this as extension reading; ordinary BFS is enough for most interviews. If you TLE, or an interviewer probes further, consider bidirectional BFS.

It's derived from standard BFS:

**Standard BFS expands from start until reaching target; bidirectional BFS expands from both start and target, stopping when they meet**.

Why is it faster?

A and B want to meet: in standard BFS, A goes to B while B stays still; in bidirectional, both walk toward each other — they meet sooner.

![](https://labuladong.online/algo/images/bfs/1.jpeg)

![](https://labuladong.online/algo/images/bfs/2.jpeg)

If the target is at the bottom of a tree, standard BFS visits the whole tree to reach it; bidirectional BFS only explores half before meeting.

In Big-O terms, both are $O(N)$ in the worst case; in practice bidirectional BFS is faster.

::: info Limitation

**You must know where the target is.**

We always know the start. But sometimes you don't know the explicit target.

For the lock and sliding puzzle, the targets are explicit — bidirectional applies.

But for the binary tree minimum-depth problem in [DFS/BFS Binary Tree Traversal](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/), the target (a specific leaf) isn't known up front — bidirectional doesn't apply.

:::

Code for the lock with bidirectional BFS:

```java
class Solution {
    public int openLock(String[] deadends, String target) {
        Set<String> deads = new HashSet<>();
        for (String s : deadends) deads.add(s);
        // base case
        if (deads.contains("0000")) return -1;
        if (target.equals("0000")) return 0;

        // Use sets, not queues, to test membership quickly
        Set<String> q1 = new HashSet<>();
        Set<String> q2 = new HashSet<>();
        Set<String> visited = new HashSet<>();
        
        int step = 0;
        q1.add("0000");
        visited.add("0000");
        q2.add(target);
        visited.add(target);

        while (!q1.isEmpty() && !q2.isEmpty()) {
            // Increment steps
            step++;

            // Can't modify a set while iterating; collect into newQ1
            Set<String> newQ1 = new HashSet<>();

            // Expand q1
            for (String cur : q1) {
                // Add unvisited neighbors
                for (String neighbor : getNeighbors(cur)) {
                    // Reached the other side?
                    if (q2.contains(neighbor)) {
                        return step;
                    }
                    if (!visited.contains(neighbor) && !deads.contains(neighbor)) {
                        newQ1.add(neighbor);
                        visited.add(neighbor);
                    }
                }
            }
            // newQ1 is q1's neighbors
            q1 = newQ1;
            // Always expand the smaller set as q1
            if (q1.size() > q2.size()) {
                Set<String> temp = q1;
                q1 = q2;
                q2 = temp;
            }
        }
        return -1;
    }

    String plusOne(String s, int j) {
        char[] ch = s.toCharArray();
        if (ch[j] == '9')
            ch[j] = '0';
        else
            ch[j] += 1;
        return new String(ch);
    }

    String minusOne(String s, int j) {
        char[] ch = s.toCharArray();
        if (ch[j] == '0')
            ch[j] = '9';
        else
            ch[j] -= 1;
        return new String(ch);
    }

    List<String> getNeighbors(String s) {
        List<String> neighbors = new ArrayList<>();
        for (int i = 0; i < 4; i++) {
            neighbors.add(plusOne(s, i));
            neighbors.add(minusOne(s, i));
        }
        return neighbors;
    }
}
```

Differences:

1. Use [hash sets](https://labuladong.online/algo/data-structure-basic/hash-set/) instead of queues for fast intersection checks.

2. The `return step` location moves: we no longer check for reaching a single target but for set intersection.

3. Always expand the smaller set, reducing space growth.

In BFS, larger frontier → larger next frontier. Always expanding the smaller one is more efficient.

**Big-O is the same — bidirectional is a refinement, not a complexity change.** Mastery is optional.

Most importantly, internalize the BFS framework. Try the [BFS Practice](https://labuladong.online/algo/problem-set/bfs/).


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Prim MST Algorithm](https://labuladong.online/algo/data-structure/prim/)
 - [[Practice] Classic BFS Problems I](https://labuladong.online/algo/problem-set/bfs/)
 - [[Practice] Classic BFS Problems II](https://labuladong.online/algo/problem-set/bfs-ii/)
 - [[Practice] Classic Backtracking Problems II](https://labuladong.online/algo/problem-set/backtrack-ii/)
 - [[Practice] Classic Union-Find Problems](https://labuladong.online/algo/problem-set/union-find/)
 - [[Practice] Level-Order Traversal I](https://labuladong.online/algo/problem-set/binary-tree-level-i/)
 - [[Practice] Level-Order Traversal II](https://labuladong.online/algo/problem-set/binary-tree-level-ii/)
 - [Bipartite Detection](https://labuladong.online/algo/data-structure/bipartite-graph/)
 - [Binary Tree Basics and Common Types](https://labuladong.online/algo/data-structure-basic/binary-tree-basic/)
 - [Recursive / Level-Order Binary Tree Traversal](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/)
 - [Binary Tree Algorithm Outline](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
 - [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Travel Cost Saving: Weighted Shortest Path](https://labuladong.online/algo/dynamic-programming/cheap-travel/)
 - [Cycle Detection and Topological Sort](https://labuladong.online/algo/data-structure/topological-sort/)
 - [Beat Algorithms with Algorithms](https://labuladong.online/algo/fname.html?fname=algorithm-in-pdf)
 - [Algorithm Practice and Flow](https://labuladong.online/algo/fname.html?fname=flow)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1091. Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/?show=1) | [1091. Shortest Path in Binary Matrix](https://leetcode.cn/problems/shortest-path-in-binary-matrix/?show=1) | 🟠 |
| [111. Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree/?show=1) | [111. Minimum Depth of Binary Tree](https://leetcode.cn/problems/minimum-depth-of-binary-tree/?show=1) | 🟢 |
| [117. Populating Next Right Pointers in Each Node II](https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/?show=1) | [117. Populating Next Right Pointers in Each Node II](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node-ii/?show=1) | 🟠 |
| [127. Word Ladder](https://leetcode.com/problems/word-ladder/?show=1) | [127. Word Ladder](https://leetcode.cn/problems/word-ladder/?show=1) | 🔴 |
| [1926. Nearest Exit from Entrance in Maze](https://leetcode.com/problems/nearest-exit-from-entrance-in-maze/?show=1) | [1926. Nearest Exit from Entrance in Maze](https://leetcode.cn/problems/nearest-exit-from-entrance-in-maze/?show=1) | 🟠 |
| [2850. Minimum Moves to Spread Stones Over Grid](https://leetcode.com/problems/minimum-moves-to-spread-stones-over-grid/?show=1) | [2850. Minimum Moves to Spread Stones Over Grid](https://leetcode.cn/problems/minimum-moves-to-spread-stones-over-grid/?show=1) | 🟠 |
| [286. Walls and Gates](https://leetcode.com/problems/walls-and-gates/?show=1)🔒 | [286. Walls and Gates](https://leetcode.cn/problems/walls-and-gates/?show=1)🔒 | 🟠 |
| [310. Minimum Height Trees](https://leetcode.com/problems/minimum-height-trees/?show=1) | [310. Minimum Height Trees](https://leetcode.cn/problems/minimum-height-trees/?show=1) | 🟠 |
| [329. Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/?show=1) | [329. Longest Increasing Path in a Matrix](https://leetcode.cn/problems/longest-increasing-path-in-a-matrix/?show=1) | 🔴 |
| [365. Water and Jug Problem](https://leetcode.com/problems/water-and-jug-problem/?show=1) | [365. Water and Jug Problem](https://leetcode.cn/problems/water-and-jug-problem/?show=1) | 🟠 |
| [431. Encode N-ary Tree to Binary Tree](https://leetcode.com/problems/encode-n-ary-tree-to-binary-tree/?show=1)🔒 | [431. Encode N-ary Tree to Binary Tree](https://leetcode.cn/problems/encode-n-ary-tree-to-binary-tree/?show=1)🔒 | 🔴 |
| [433. Minimum Genetic Mutation](https://leetcode.com/problems/minimum-genetic-mutation/?show=1) | [433. Minimum Genetic Mutation](https://leetcode.cn/problems/minimum-genetic-mutation/?show=1) | 🟠 |
| [490. The Maze](https://leetcode.com/problems/the-maze/?show=1)🔒 | [490. The Maze](https://leetcode.cn/problems/the-maze/?show=1)🔒 | 🟠 |
| [505. The Maze II](https://leetcode.com/problems/the-maze-ii/?show=1)🔒 | [505. The Maze II](https://leetcode.cn/problems/the-maze-ii/?show=1)🔒 | 🟠 |
| [542. 01 Matrix](https://leetcode.com/problems/01-matrix/?show=1) | [542. 01 Matrix](https://leetcode.cn/problems/01-matrix/?show=1) | 🟠 |
| [547. Number of Provinces](https://leetcode.com/problems/number-of-provinces/?show=1) | [547. Number of Provinces](https://leetcode.cn/problems/number-of-provinces/?show=1) | 🟠 |
| [863. All Nodes Distance K in Binary Tree](https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/?show=1) | [863. All Nodes Distance K in Binary Tree](https://leetcode.cn/problems/all-nodes-distance-k-in-binary-tree/?show=1) | 🟠 |
| [994. Rotting Oranges](https://leetcode.com/problems/rotting-oranges/?show=1) | [994. Rotting Oranges](https://leetcode.cn/problems/rotting-oranges/?show=1) | 🟠 |
| - | [Sword to Offer II 109. Open the Combination Lock](https://leetcode.cn/problems/zlDJc7/?show=1) | 🟠 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
