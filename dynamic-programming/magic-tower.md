# DP helped me clear "Magic Tower"



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | 力扣 | Difficulty |
| :----: | :----: | :----: |
| [174. Dungeon Game](https://leetcode.com/problems/dungeon-game/) | [174. 地下城游戏](https://leetcode.cn/problems/dungeon-game/) | 🔴 |

**-----------**



> [!NOTE]
> Before reading, you should first study:
> 
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)

"Magic Tower" is a classic dungeon game: bumping into monsters loses HP, drinking potions adds HP. You collect keys, climb floor by floor, and finally rescue the beautiful princess.

You can still play it on phones today:

![](https://labuladong.online/algo/images/dungeons/0.png)

I bet this game shaped many people's childhoods. I remember as a kid, one person held the gaming device, with two or three others crowding around shouting advice — terrible experience for the player, great fun for the spectators 😂

LeetCode 174 "Dungeon Game" is a similar problem:

<Problem slug="dungeon-game" />

**In short: what's the minimum initial HP needed for a knight to move from the top-left to the bottom-right cell while keeping HP > 0 at all times?**

Function signature:

```java
int calculateMinimumHP(int[][] grid);
```

The earlier post [Minimum path sum](https://labuladong.online/algo/dynamic-programming/minimum-path-sum/) covered a similar problem — the minimum path sum from top-left to bottom-right.

Always try to draw inferences when grinding algorithms. Today's problem feels related to minimum path sum, right?

Minimizing the knight's initial HP — does that mean maximizing the potions consumed along the path? That'd be a "maximum path sum", solvable with the minimum-path-sum approach, right?

But on second thought, that inference fails. Eating the most potions doesn't necessarily yield the smallest initial HP.

For example, in the situation below, to eat the most potions and get the "maximum path sum", you'd take the arrow path shown — initial HP needed is 11:

![](https://labuladong.online/algo/images/dungeons/2.png)

But it's easy to see the correct answer is the arrow path below, requiring only 1 initial HP:

![](https://labuladong.online/algo/images/dungeons/3.png)

**So the key isn't to eat the most potions — it's to lose the least HP**.

Such optimization problems must use DP. We need to define `dp` array/function carefully. Analogous to the [minimum path sum problem](https://labuladong.online/algo/dynamic-programming/minimum-path-sum/), the `dp` function signature should look like:

```java
int dp(int[][] grid, int i, int j);
```

But the definition is interesting. By common sense, the `dp` function should be:

**The minimum HP needed to walk from the top-left (`grid[0][0]`) to `grid[i][j]` is `dp(grid, i, j)`**.

With this definition, the base case is when `i, j` are both 0:

```java
int calculateMinimumHP(int[][] grid) {
    int m = grid.length;
    int n = grid[0].length;
    // we want to compute the minimum HP required to go from top-left to bottom-right
    return dp(grid, m - 1, n - 1);
}

int dp(int[][] grid, int i, int j) {
    // base case
    if (i == 0 && j == 0) {
        // make sure the knight survives upon landing
        return grid[i][j] > 0 ? 1 : -grid[i][j] + 1;
    }
    ...
}
```

> [!NOTE]
> For brevity, I'll write `dp(i, j)` instead of `dp(grid, i, j)` going forward.

Now we need a state transition. Remember how to find one? Can our `dp` function definition support correct state transitions?

We want `dp(i, j)` to be derivable from `dp(i-1, j)` and `dp(i, j-1)` so we keep approaching the base case and the transition is valid.

Specifically, "minimum HP to reach `A`" should be derivable from "minimum HP to reach `B`" and "minimum HP to reach `C`":

![](https://labuladong.online/algo/images/dungeons/4.png)

**But can it actually be derived? Actually, no**.

By our `dp` function definition, you only know "the minimum HP needed to reach `B` from the top-left", but not "the HP at the time of reaching `B`".

The HP at the moment of reaching `B` is essential reference info for the state transition. Here's an example:

![](https://labuladong.online/algo/images/dungeons/5.png)

What's the optimal route for the knight to save the princess?

Clearly the blue path: through `B`, then to `A`. Initial HP needed is just 1. The yellow path through `C` then `A` requires at least 6 initial HP.

Why? Both `B` and `C` need only 1 minimum initial HP — why end up via `B` and not via `C`?

Because when reaching `B`, the knight has 11 HP, but when reaching `C`, the knight still has 1 HP.

If the knight insists on `C` → `A`, the initial HP must be raised to 6. Going `B` → `A` works with initial HP 1, since the knight ate potions along the way and has enough HP to survive the monster on `A`.

Hopefully that's clear. Now look back at our `dp` definition. In the figure above, the algorithm only knows `dp(1, 2) = dp(2, 1) = 1` — both are equal. How would it correctly decide and compute `dp(2, 2)`?

**So our previous `dp` array definition was wrong — insufficient information for the algorithm to make the right state transition**.

The correct approach is reverse thinking. Same `dp` signature:

```java
int dp(int[][] grid, int i, int j);
```

But we modify the definition:

**The minimum HP needed to reach the destination (bottom-right) starting from `grid[i][j]` is `dp(grid, i, j)`**.

Then the code becomes:

```java
int calculateMinimumHP(int[][] grid) {
    // we want to compute the minimum HP required to go from top-left to bottom-right
    return dp(grid, 0, 0);
}

int dp(int[][] grid, int i, int j) {
    int m = grid.length;
    int n = grid[0].length;
    // base case
    if (i == m - 1 && j == n - 1) {
        return grid[i][j] >= 0 ? 1 : -grid[i][j] + 1;
    }
    ...
}
```

With this new definition and base case, we want `dp(0, 0)`. So we should derive `dp(i, j)` from `dp(i, j+1)` and `dp(i+1, j)` to keep approaching the base case correctly.

Specifically, "minimum HP from `A` to bottom-right" should be derivable from "minimum HP from `B` to bottom-right" and "minimum HP from `C` to bottom-right":

![](https://labuladong.online/algo/images/dungeons/6.png)

Can it be derived this time? Yes. Suppose `dp(0, 1) = 5, dp(1, 0) = 4`. Then we'd definitely go `A → C` since 4 < 5.

How do we infer `dp(0, 0)`?

Suppose `A`'s value is 1. Knowing the next step is `C` and `dp(1, 0) = 4` means we need at least 4 HP when reaching `grid[1][0]`. So at `A`, the knight needs `4 - 1 = 3` initial HP.

If `A`'s value is 10 — a big potion on landing, exceeding what's needed — then `4 - 10 = -6`, i.e. the knight's initial HP is negative, which can't happen (HP < 1 = death). In this case the initial HP should be 1.

So the state-transition equation is:





```java
int res = min(
    dp(i + 1, j),
    dp(i, j + 1)
) - grid[i][j];

dp(i, j) = res <= 0 ? 1 : res;
```

With this core logic, plus a memo to eliminate overlapping subproblems, we get the final code:

```java
class Solution {
    // main function
    public int calculateMinimumHP(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        // initialize the memo with all -1
        memo = new int[m][n];
        for (int[] row : memo) {
            Arrays.fill(row, -1);
        }

        return dp(grid, 0, 0);
    }

    // memo to eliminate overlapping subproblems
    int[][] memo;

    // definition: minimum initial HP needed to reach the bottom-right starting from (i, j)
    int dp(int[][] grid, int i, int j) {
        int m = grid.length;
        int n = grid[0].length;
        // base case
        if (i == m - 1 && j == n - 1) {
            return grid[i][j] >= 0 ? 1 : -grid[i][j] + 1;
        }
        if (i == m || j == n) {
            return Integer.MAX_VALUE;
        }
        // avoid redundant computation
        if (memo[i][j] != -1) {
            return memo[i][j];
        }
        // state transition logic
        int res = Math.min(
                dp(grid, i, j + 1),
                dp(grid, i + 1, j)
            ) - grid[i][j];
        // the knight's HP must be at least 1
        memo[i][j] = res <= 0 ? 1 : res;

        return memo[i][j];
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/dungeon-game/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>👾 Code visualization 👾</strong>
</summary>
</details>
</a>
<hr/>



This is the top-down memoized DP solution. With reference to the earlier [DP framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/), it's easy to convert into an iterative `dp` array solution. I'll skip that — try writing it yourself.

The core of this problem is defining the `dp` function and finding the correct state-transition equation, which then yields the right answer.









**＿＿＿＿＿＿＿＿＿＿＿＿＿**

