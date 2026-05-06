# Sweeping Puzzle Games with BFS

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
| [773. Sliding Puzzle](https://leetcode.com/problems/sliding-puzzle/) | [773. Sliding Puzzle](https://leetcode.cn/problems/sliding-puzzle/) | 🔴

**-----------**

Most people have played the sliding puzzle. Below is a 4x4 sliding puzzle:

![](https://labuladong.online/algo/images/sliding_puzzle/1.jpeg)

There's one empty cell; you can use it to slide other tiles. The goal is to reach a specific arrangement.

As a kid I also played "Hua Rong Dao" (Klotski), similar to the sliding puzzle:

![](https://labuladong.online/algo/images/sliding_puzzle/2.jpeg)

The sliding puzzle is also called the digital Hua Rong Dao — they're similar.

How do you solve them? There are tricks, like Rubik's cube formulas. But today we won't dig into hair-pulling tricks; **all these puzzles can be solved by brute-force search, so we'll use the BFS framework**.

### 1. Problem

LeetCode 773 "Sliding Puzzle":

You're given a 2x3 sliding puzzle as a 2x3 array `board` containing digits 0-5, where **0 represents the empty cell**. You can move the digits; when `board` becomes `[[1,2,3],[4,5,0]]`, you win.

Write an algorithm computing the minimum number of moves to win, or -1 if unwinnable.

E.g. `board = [[4,1,2],[5,0,3]]` returns 5:

![](https://labuladong.online/algo/images/sliding_puzzle/5.jpeg)

`board = [[1,2,3],[5,4,0]]` returns -1 — it cannot be solved.

### 2. Idea

Minimum-moves problems should immediately suggest BFS.

Two questions to address:

1. Standard BFS goes from `start` to `target` in a search space. The puzzle isn't pathfinding — it's swapping numbers. How to convert?

2. Even if convertible, both `start` and `target` are arrays. Putting arrays in a queue and applying BFS sounds clunky.

For (1), **BFS isn't only a pathfinding algorithm — it's a brute-force search**. Anything that can be brute-forced can use BFS, often the fastest.

Think about how computers solve problems: brute-force enumerate possibilities, pick an optimal answer. Nothing magical.

So our problem becomes: **how do we enumerate all states reachable from the current `board`?** Easy — swap 0 with one of its up/down/left/right neighbors:

![](https://labuladong.online/algo/images/sliding_puzzle/3.jpeg)

This is now a BFS problem: each step finds 0 and swaps with a neighbor, producing a new state, and we enqueue. The first time we reach `target`, we have the minimum number of moves.

For (2): the `board` is only 2x3, so we can flatten it to a string. **The trick: in 2D we have "up/down/left/right"; in 1D, how do we get an index's neighbors?**

For 2x3, hard-code the mapping:

<!-- muliti_language -->
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

For an `m x n` 2D array, hand-coding is impractical — generate it programmatically:

Observe: if 2D element `e` is at flat index `i`, then `e`'s left/right neighbors are at `i - 1` / `i + 1`, and its top/bottom neighbors are at `i - n` / `i + n`, where `n` is the column count.

So for `m x n`:

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

Now we've fully reduced the problem to standard BFS. Following the [BFS Framework](https://labuladong.online/algo/essential-technique/bfs-framework/):

<!-- muliti_language -->
```java
class Solution {
    public int slidingPuzzle(int[][] board) {
        int m = 2, n = 3;
        StringBuilder sb = new StringBuilder();
        String target = "123450";
        // Convert the 2x3 array to a string as the BFS start state
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                sb.append(board[i][j]);
            }
        }
        String start = sb.toString();

        // Adjacency for flattened indices
        int[][] neighbor = new int[][]{
                {1, 3},
                {0, 4, 2},
                {1, 5},
                {0, 4},
                {3, 1, 5},
                {4, 2}
        };

        /******* Begin BFS framework *******/
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
                // Find the index of '0'
                int idx = 0;
                for (; cur.charAt(idx) != '0'; idx++) ;
                // Swap '0' with each neighbor
                for (int adj : neighbor[idx]) {
                    String new_board = swap(cur.toCharArray(), adj, idx);
                    // Avoid revisiting
                    if (!visited.contains(new_board)) {
                        q.offer(new_board);
                        visited.add(new_board);
                    }
                }
            }
            step++;
        }
        /******* End BFS framework *******/
        return -1;
    }

    private String swap(char[] chars, int i, int j) {
        char temp = chars[i];
        chars[i] = chars[j];
        chars[j] = temp;
        return new String(chars);
    }
}
```

<visual slug='sliding-puzzle'/>

Done. The framework is unchanged — we just spent more time turning the puzzle into a BFS problem.

Many puzzle games look clever but can't withstand brute-force enumeration. Backtracking and BFS are the usual tools.



<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [BFS Algorithm Framework](https://labuladong.online/algo/essential-technique/bfs-framework/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou |
| :----: | :----: |
| [365. Water and Jug Problem](https://leetcode.com/problems/water-and-jug-problem/?show=1) | [365. Water and Jug Problem](https://leetcode.cn/problems/water-and-jug-problem/?show=1) |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**

**"labuladong's Algorithm Notes" has been published. Follow the official account for details; reply with "all-in-one bundle" to download the companion PDF and the full problem-solving bundle**:

![](https://labuladong.online/algo/images/souyisou2.png)