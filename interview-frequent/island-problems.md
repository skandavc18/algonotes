# Sweeping All Island Problems



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1020. Number of Enclaves](https://leetcode.com/problems/number-of-enclaves/) | [1020. Number of Enclaves](https://leetcode.cn/problems/number-of-enclaves/) | 🟠 |
| [1254. Number of Closed Islands](https://leetcode.com/problems/number-of-closed-islands/) | [1254. Number of Closed Islands](https://leetcode.cn/problems/number-of-closed-islands/) | 🟠 |
| [1905. Count Sub Islands](https://leetcode.com/problems/count-sub-islands/) | [1905. Count Sub Islands](https://leetcode.cn/problems/count-sub-islands/) | 🟠 |
| [200. Number of Islands](https://leetcode.com/problems/number-of-islands/) | [200. Number of Islands](https://leetcode.cn/problems/number-of-islands/) | 🟠 |
| [694. Number of Distinct Islands](https://leetcode.com/problems/number-of-distinct-islands/)🔒 | [694. Number of Distinct Islands](https://leetcode.cn/problems/number-of-distinct-islands/)🔒 | 🟠 |
| [695. Max Area of Island](https://leetcode.com/problems/max-area-of-island/) | [695. Max Area of Island](https://leetcode.cn/problems/max-area-of-island/) | 🟠 |

**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Binary Tree Algorithm Outline](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
> - [Backtracking Framework](https://labuladong.online/algo/essential-technique/backtrack-framework/)
> - [Some Questions about DFS and Backtracking](https://labuladong.online/algo/essential-technique/backtrack-vs-dfs/)

Island problems are classic interview questions. They are easy at the basic level but extend to interesting variants — counting sub-islands, distinct island shapes, etc. We'll cover them here.

**The core trick: traverse a 2D grid with DFS/BFS.**

We focus on DFS; BFS is symmetric — just rewrite.

How to DFS in a 2D matrix? Treat each cell as a node; up/down/left/right are neighbors. The matrix becomes a graph.

Per [Framework Thinking for DS&A](https://labuladong.online/algo/essential-technique/algorithm-summary/), adapt the binary-tree traversal to 2D:

```java
// Binary-tree traversal
void traverse(TreeNode root) {
    traverse(root.left);
    traverse(root.right);
}

// 2D grid traversal
void dfs(int[][] grid, int i, int j, boolean[][] visited) {
    int m = grid.length, n = grid[0].length;
    if (i < 0 || j < 0 || i >= m || j >= n) {
        // Out of bounds
        return;
    }
    if (visited[i][j]) {
        // Already visited
        return;
    }

    // Enter (i, j)
    visited[i][j] = true;

    // Visit neighbors (4-way)
    // Up
    dfs(grid, i - 1, j, visited);
    // Down
    dfs(grid, i + 1, j, visited);
    // Left
    dfs(grid, i, j - 1, visited);
    // Right
    dfs(grid, i, j + 1, visited);
}
```

Since the 2D matrix is essentially a graph, we need a `visited` array to avoid cycles.

A common style uses a "directions" array, similar to [Union-Find](https://labuladong.online/algo/data-structure/union-find/):

```java
// Up, Down, Left, Right
int[][] dirs = new int[][]{{-1,0}, {1,0}, {0,-1}, {0,1}};

void dfs(int[][] grid, int i, int j, boolean[][] visited) {
    int m = grid.length, n = grid[0].length;
    if (i < 0 || j < 0 || i >= m || j >= n) {
        return;
    }
    if (visited[i][j]) {
        return;
    }

    visited[i][j] = true;
    for (int[] d : dirs) {
        int next_i = i + d[0];
        int next_j = j + d[1];
        dfs(grid, next_i, next_j, visited);
    }
}
```

A loop-based 4-neighbor visit. Use whichever style you prefer.







## Number of Islands

LeetCode 200 "Number of Islands" — the classic. Given a 2D grid of `0` (water) and `1` (land); the matrix is surrounded by water on all sides.

Connected lands form an island; count islands:

```java
int numIslands(char[][] grid);
```

Example with 4 islands:

![](https://labuladong.online/algo/images/island/1.jpg)

The trick: find an island and mark it. DFS does it.

```java
class Solution {
    // Main
    int numIslands(char[][] grid) {
        int res = 0;
        int m = grid.length, n = grid[0].length;
        // Scan grid
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    // Found a new island
                    res++;
                    // Sink it via DFS
                    dfs(grid, i, j);
                }
            }
        }
        return res;
    }

    // From (i, j), turn connected land into water
    void dfs(char[][] grid, int i, int j) {
        int m = grid.length, n = grid[0].length;
        if (i < 0 || j < 0 || i >= m || j >= n) {
            // Out of bounds
            return;
        }
        if (grid[i][j] == '0') {
            // Already water
            return;
        }
        // Sink (i, j)
        grid[i][j] = '0';
        // Sink neighbors
        dfs(grid, i + 1, j);
        dfs(grid, i, j + 1);
        dfs(grid, i - 1, j);
        dfs(grid, i, j - 1);
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/number-of-islands/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌟 Animated Code Visualization 🌟</strong>
</summary>
</details>
</a>
<hr/>



**Why "sink" each found island? To avoid maintaining a `visited` array.**

`dfs` returns immediately on `0`, so setting visited cells to `0` prevents revisiting.

> [!TIP]
> This kind of DFS is also called FloodFill — apt name.

That's the basic case. Variants follow.

## Number of Closed Islands

The previous problem's grid was surrounded by water, so border lands counted as islands.

LeetCode 1254 "Number of Closed Islands" differs:

1. `0` = land, `1` = water.

2. Count "closed" islands — fully surrounded by `1` on all sides; **border-touching lands are not closed**.

```java
int closedIsland(int[][] grid)
```

Example:

![](https://labuladong.online/algo/images/island/2.png)

Returns 2 (the gray-shaded islands).

**Trick: pre-sink border-touching islands; the remaining ones are closed.**

```java
class Solution {
    // Main
    public int closedIsland(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        for (int j = 0; j < n; j++) {
            // Sink top border
            dfs(grid, 0, j);
            // Sink bottom border
            dfs(grid, m - 1, j);
        }
        for (int i = 0; i < m; i++) {
            // Sink left border
            dfs(grid, i, 0);
            // Sink right border
            dfs(grid, i, n - 1);
        }
        // Remaining islands are closed
        int res = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 0) {
                    res++;
                    dfs(grid, i, j);
                }
            }
        }
        return res;
    }

    void dfs(int[][] grid, int i, int j) {
        int m = grid.length, n = grid[0].length;
        if (i < 0 || j < 0 || i >= m || j >= n) {
            return;
        }
        if (grid[i][j] == 1) {
            // Already water
            return;
        }
        grid[i][j] = 1;
        dfs(grid, i + 1, j);
        dfs(grid, i, j + 1);
        dfs(grid, i - 1, j);
        dfs(grid, i, j - 1);
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/number-of-closed-islands/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🥳 Animated Code Visualization 🥳</strong>
</summary>
</details>
</a>
<hr/>



Pre-sink the border lands; count what remains.

> [!TIP]
> Beyond DFS/BFS, Union-Find is also an option. [Union-Find Applications](https://labuladong.online/algo/data-structure/union-find/) covers a similar problem.

A small variation gives LeetCode 1020 "Number of Enclaves" — instead of counting closed islands, count their total area. Same idea: sink the border lands; count remaining `1`s. Note: 1020 uses `1` for land, `0` for water.

I'll skip the code; let's keep going.

## Max Area of Island

LeetCode 695 "Max Area of Island" — `0` = water, `1` = land. Return the area of the largest island:

```java
int maxAreaOfIsland(int[][] grid)
```

Example:

![](https://labuladong.online/algo/images/island/3.jpg)

The orange island has area 6; return 6.

**Same idea, but `dfs` should also count the cells sunk.**

Make `dfs` return the count:

```java
class Solution {
    public int maxAreaOfIsland(int[][] grid) {
        int res = 0;
        int m = grid.length, n = grid[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    // Sink island and update max area
                    res = Math.max(res, dfs(grid, i, j));
                }
            }
        }
        return res;
    }

    // Sink connected land starting at (i, j); return area sunk
    int dfs(int[][] grid, int i, int j) {
        int m = grid.length, n = grid[0].length;
        if (i < 0 || j < 0 || i >= m || j >= n) {
            return 0;
        }
        if (grid[i][j] == 0) {
            return 0;
        }
        grid[i][j] = 0;

        return dfs(grid, i + 1, j)
            + dfs(grid, i, j + 1)
            + dfs(grid, i - 1, j)
            + dfs(grid, i, j - 1) + 1;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/max-area-of-island/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Animated Code Visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>



The next two are more interesting.

## Sub-Island Count

LeetCode 1905 "Count Sub Islands" requires a bit of thought:

<Problem slug="count-sub-islands" />

**Key: how to decide if an island is a sub-island?** Could use Union-Find, but we'll stick with DFS.

When is `B` (in `grid2`) a sub-island of an island `A` (in `grid1`)? When every cell of `B` is land in `A`.

**Conversely: if any cell of `B` is water in `A`, `B` is not a sub-island.**

Strategy: scan `grid2`'s islands; eliminate those that can't be sub-islands; count what remains.

```java
class Solution {
    public int countSubIslands(int[][] grid1, int[][] grid2) {
        int m = grid1.length, n = grid1[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid1[i][j] == 0 && grid2[i][j] == 1) {
                    // This island can't be a sub-island; sink it
                    dfs(grid2, i, j);
                }
            }
        }
        // Remaining islands in grid2 are sub-islands; count them
        int res = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid2[i][j] == 1) {
                    res++;
                    dfs(grid2, i, j);
                }
            }
        }
        return res;
    }

    void dfs(int[][] grid, int i, int j) {
        int m = grid.length, n = grid[0].length;
        if (i < 0 || j < 0 || i >= m || j >= n) {
            return;
        }
        if (grid[i][j] == 0) {
            return;
        }

        grid[i][j] = 0;
        dfs(grid, i + 1, j);
        dfs(grid, i, j + 1);
        dfs(grid, i - 1, j);
        dfs(grid, i, j - 1);
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/count-sub-islands/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>



Same flavor as closed-islands — except we exclude ones that can't be sub-islands.

## Number of Distinct Islands

LeetCode 694 "Number of Distinct Islands" — given the same grid, count islands of different shapes:

```java
int numDistinctIslands(int[][] grid)
```

Example:

![](https://labuladong.online/algo/images/island/5.jpg)

Four islands, but the bottom-left and top-right have the same shape — distinct = 3.

We need to convert each island into something hashable (e.g., a string), then use a HashSet.

Converting an island to a string = serialization = traversal. See [Serialize a Binary Tree](https://labuladong.online/algo/data-structure/serialize-and-deserialize-binary-tree/) for the analog.

**Same-shape islands traversed from the same starting point in the same DFS order produce the same sequence.**

The DFS order is fixed in the code:

```java
void dfs(int[][] grid, int i, int j) {
    // Recursion order:
    // Up
    dfs(grid, i - 1, j);
    // Down
    dfs(grid, i + 1, j);
    // Left
    dfs(grid, i, j - 1);
    // Right
    dfs(grid, i, j + 1);
}
```

So traversal order describes shape:

![](https://labuladong.online/algo/images/island/6.png)

Suppose the order is:

```
Down, Right, Up, Undo Up, Undo Right, Undo Down
```

Encode up/down/left/right as 1/2/3/4, and their undos as -1/-2/-3/-4. Then:

```
2, 4, 1, -1, -4, -2
```

**This is a serialization — generate it during DFS to compare distinct islands.**

::: info Why must we record "undo" steps?

Why does undo matter? Without it, "Down, Right, Undo Right, Undo Down" and "Down, Undo Down, Right, Undo Right" produce the same sequence "Down, Right" — but they're different traversals.

:::

Modify `dfs` to record direction:

```java
void dfs(int[][] grid, int i, int j, StringBuilder sb, int dir) {
    int m = grid.length, n = grid[0].length;
    if (i < 0 || j < 0 || i >= m || j >= n 
        || grid[i][j] == 0) {
        return;
    }
    // Preorder: enter (i, j)
    grid[i][j] = 0;
    sb.append(dir).append(',');
    
    // Up
    dfs(grid, i - 1, j, sb, 1);
    // Down
    dfs(grid, i + 1, j, sb, 2);
    // Left
    dfs(grid, i, j - 1, sb, 3);
    // Right
    dfs(grid, i, j + 1, sb, 4);
    
    // Postorder: leave (i, j)
    sb.append(-dir).append(',');
}
```

> [!NOTE]
> Make a choice before recursion; undo after. This is [backtracking](https://labuladong.online/algo/essential-technique/backtrack-framework/) — focused on edges (the order), not nodes (cells).
> 
> You can rewrite this as a backtracking template.

Final solution:

```java
class Solution {
    public int numDistinctIslands(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        // Set of all island serializations
        HashSet<String> islands = new HashSet<>();
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    StringBuilder sb = new StringBuilder();
                    // Initial dir is arbitrary
                    dfs(grid, i, j, sb, 666);
                    islands.add(sb.toString());
                }
            }
        }
        // Distinct island count
        return islands.size();
    }

    private void dfs(int[][] grid, int i, int j, StringBuilder sb, int dir) {
        // See above
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/number-of-distinct-islands/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🥳 Animated Code Visualization 🥳</strong>
</summary>
</details>
</a>
<hr/>



The initial `dir` is arbitrary because this is backtracking — it focuses on edges, not nodes (see [Graph Basics](https://labuladong.online/algo/data-structure-basic/graph-basic/) for the difference).

That's all the island problems. The first few are routine; the last two are clever.







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Classic BFS Problems II](https://labuladong.online/algo/problem-set/bfs-ii/)
 - [[Practice] Classic Backtracking Problems I](https://labuladong.online/algo/problem-set/backtrack-i/)
 - [[Practice] Classic Backtracking Problems II](https://labuladong.online/algo/problem-set/backtrack-ii/)
 - [[Practice] Classic Union-Find Problems](https://labuladong.online/algo/problem-set/union-find/)
 - [Binary Tree Algorithm Outline](https://labuladong.online/algo/essential-technique/binary-tree-summary/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1219. Path with Maximum Gold](https://leetcode.com/problems/path-with-maximum-gold/?show=1) | [1219. Path with Maximum Gold](https://leetcode.cn/problems/path-with-maximum-gold/?show=1) | 🟠 |
| [547. Number of Provinces](https://leetcode.com/problems/number-of-provinces/?show=1) | [547. Number of Provinces](https://leetcode.cn/problems/number-of-provinces/?show=1) | 🟠 |
| [79. Word Search](https://leetcode.com/problems/word-search/?show=1) | [79. Word Search](https://leetcode.cn/problems/word-search/?show=1) | 🟠 |
| [924. Minimize Malware Spread](https://leetcode.com/problems/minimize-malware-spread/?show=1) | [924. Minimize Malware Spread](https://leetcode.cn/problems/minimize-malware-spread/?show=1) | 🔴 |
| [Sword to Offer 13. Robot Range](https://leetcode.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/?show=1) | [Sword to Offer 13. Robot Range](https://leetcode.cn/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/?show=1) | 🟠 |
| - | [Sword to Offer II 105. Max Area of Island](https://leetcode.cn/problems/ZL6zAn/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
