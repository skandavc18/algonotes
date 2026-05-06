# Dynamic programming problem-solving framework



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | 力扣 | Difficulty |
| :----: | :----: | :----: |
| [322. Coin Change](https://leetcode.com/problems/coin-change/) | [322. 零钱兑换](https://leetcode.cn/problems/coin-change/) | 🟠 |
| [509. Fibonacci Number](https://leetcode.com/problems/fibonacci-number/) | [509. 斐波那契数](https://leetcode.cn/problems/fibonacci-number/) | 🟢 |

**-----------**



> [!NOTE]
> Before reading, you should first study:
> 
> - [Binary tree traversal framework](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/)
> - [N-ary tree structure and traversal framework](https://labuladong.online/algo/data-structure-basic/n-ary-tree-traverse-basic/)

> Tip: this article has a video version: [DP framework explained](https://www.bilibili.com/video/BV1XV411Y7oE). Subscribe to my Bilibili channel — I lead readers through tougher algorithmic techniques in video form.



This article is the upgraded version of an old, 200+ tipped [DP detailed explanation](https://mp.weixin.qq.com/s/1V3aHVonWBEXlNUvK3S28w) from years ago. I've added more substance, hoping this article serves as a "guide" to solving DP.

DP is probably a headache for many readers, but it's also among the most technique-driven and most fun. This book devotes an entire chapter to this topic — its importance is clear.

This article addresses:

What is DP? What techniques solve DP problems? How do you learn DP?

Grind enough problems and you'll find algorithm techniques fall into a few patterns. Our subsequent DP chapters all use this article's framework. With this in mind, your work becomes much easier. So this article goes in chapter one, intended as a guide. Substance below.

First, **DP problems are generally optimization problems**. DP is actually an optimization method from operations research, often applied to computer problems — e.g. longest increasing subsequence, minimum edit distance, etc.

Since we're optimizing, what's the core question? **The core of solving DP is enumeration**. To find an optimum, we must enumerate all feasible answers and pick the best.

DP is so simple — just enumerate? But the DP problems I see are very hard!







First, while DP's core idea is enumerate-then-pick-the-optimum, problems vary widely. Enumerating all feasible solutions isn't easy — you must be fluent in recursive thinking; only by listing the **correct "state-transition equation"** can you enumerate correctly. Plus you need to determine whether the algorithm has **optimal substructure** — whether you can derive the original problem's optimum from subproblem optima. Also, DP problems have **overlapping subproblems**; brute-force enumeration is inefficient, so you need a memo or DP table to avoid redundant computation.

The three elements are: overlapping subproblems, optimal substructure, and state-transition equation. We'll detail each shortly. In practice, writing the state-transition equation is hardest — that's why many find DP hard. Here's my mental framework:

**identify "state" -> identify "choice" -> define `dp` array/function meaning**.

Following this routine, the solution code looks like:







```python
# top-down recursive DP
def dp(state1, state2, ...):
    for choice in all possible choices:
        # state changes after the choice is made
        result = best(result, dp(state1, state2, ...))
    return result

# bottom-up iterative DP
# initialize base case
dp[0][0][...] = base case
# perform state transition
for state1 in all values of state1:
    for state2 in all values of state2:
        for ...
            dp[state1][state2][...] = best(choice1, choice2, ...)
```

We'll cover Fibonacci and coin-change problems to illustrate DP basics. The former mainly explains overlapping subproblems (Fibonacci doesn't seek an optimum, so strictly it's not a DP problem); the latter focuses on writing the state-transition equation.

## 1. Fibonacci

LeetCode 509 "Fibonacci Number". Don't dismiss this example — **only simple examples let you focus on the universal ideas behind the algorithm, instead of being lost in obscure details**. Hard examples are coming in the DP chapter.

### Brute-force recursion

Fibonacci's mathematical form is recursive. Code:

```java
int fib(int N) {
    if (N == 1 || N == 2) return 1;
    return fib(N - 1) + fib(N - 2);
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/mydata-fib/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Code visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>



Nothing to add — recursion teachers in school all use this. Concise but very inefficient. Why? Suppose n = 20. Draw the recursion tree:

![](https://labuladong.online/algo/images/dynamic-programming/1.jpg)

> [!TIP]
> Whenever recursion is involved, draw the recursion tree — it greatly helps with complexity analysis and finding inefficiencies.

How do we read this tree? To compute `f(20)`, we first compute `f(19)` and `f(18)`; for `f(19)` we compute `f(18)` and `f(17)`; and so on. Once we hit `f(1)` or `f(2)`, the result is known and we return — the tree stops growing.

**Recursive time complexity = subproblem count × time per subproblem**.

Subproblem count = total nodes in the recursion tree. For a binary tree it's exponential — O(2^n).

Time per subproblem: no loop, just `f(n-1) + f(n-2)` — O(1).

So total complexity = O(2^n). Exponential — explosive.

Looking at the tree, the inefficiency is clear: enormous redundant computation. `f(18)` is computed twice; the subtree rooted at `f(18)` is huge — recomputing wastes a lot of time. And `f(18)` isn't the only repeated node, so the algorithm is extremely inefficient.

This is DP's first property: **overlapping subproblems**. Now let's solve it.







### Memoized recursive solution

Once the problem is clear, it's half-solved. Since the cause is recomputation, build a "memo": after computing a subproblem, save the result before returning; before computing, check the memo first — if already solved, just return.

Generally use an array as the memo (hash table works too). Same idea.

```java
int fib(int N) {
    // memo all initialized to 0
    int[] memo = new int[N + 1];
    // run the memoized recursion
    return dp(memo, N);
}

// recursion with memo
int dp(int[] memo, int n) {
    // base case
    if (n == 0 || n == 1) return n;
    // already computed — no need to recompute
    if (memo[n] != 0) return memo[n];
    memo[n] = dp(memo, n - 1) + dp(memo, n - 2);
    return memo[n];
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/mydata-fib2/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Code visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>



Now draw the recursion tree to see what the memo did.

![](https://labuladong.online/algo/images/dynamic-programming/2.jpg)

The memoized version "prunes" the redundant tree, turning it into a redundancy-free graph — drastically fewer subproblems (nodes).

![](https://labuladong.online/algo/images/dynamic-programming/3.jpg)

**Recursive time = subproblem count × per-subproblem time**.

Subproblem count = nodes in this graph; with no redundancy, the subproblems are `f(1), f(2), ..., f(20)` — proportional to input size n = 20, so O(n).

Per-subproblem time: same as before, O(1).

Total: O(n) — vast improvement over brute force.







By now the memoized recursion is as efficient as the iterative DP. It's already similar to the common DP solution — the difference is "top-down recursion" vs the more common "bottom-up iteration".

What's "top-down"? Notice the recursion tree (or graph) we drew extends downward — from a larger problem `f(20)` down to base cases `f(1)` and `f(2)`, then returns answers up the layers. That's "top-down".

What's "bottom-up"? Conversely, start from the smallest, simplest, base-case problems `f(1)` and `f(2)` and build up to `f(20)` — that's "iteration", which is why DP usually drops recursion in favor of loops.

### Iterative `dp` array

Inspired by the memo, separate the memo into a table called the DP table, and complete the bottom-up computation on it:

```java
int fib(int N) {
    if (N == 0) return 0;
    int[] dp = new int[N + 1];
    // base case
    dp[0] = 0; dp[1] = 1;
    // state transition
    for (int i = 2; i <= N; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }

    return dp[N];
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/mydata-fib3/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌟 Code visualization 🌟</strong>
</summary>
</details>
</a>
<hr/>



Drawing the diagram makes it clear — the DP table looks much like the pruned tree, just computed in reverse:

![](https://labuladong.online/algo/images/dynamic-programming/4.jpg)

Actually, the `memo` array in the memoized solution becomes the `dp` array here — compare the two algorithms in the visualization panel.

So top-down and bottom-up are essentially the same; mostly with similar efficiency.







### Extension

We've introduced the term "state-transition equation" — really just describing the problem structure mathematically:

![](https://labuladong.online/algo/images/dynamic-programming/fib.png)

Why "state-transition equation"? Just to sound fancier.

`f(n)`'s argument changes constantly; treat `n` as a state — state `n` transitions (sums) from states `n - 1` and `n - 2`. That's all.

You'll see all operations in the previous solutions — `return f(n - 1) + f(n - 2)`, `dp[i] = dp[i - 1] + dp[i - 2]`, and the memo/DP-table initialization — are different forms of this equation.

That shows why writing the state-transition equation matters — it's the core, and it's also the brute-force solution.

**Don't underestimate the brute force. The hardest part of DP is writing this brute force, i.e. the state-transition equation**.

Once written, optimization is just memo or DP-table — no further mystery.

Final detail: a small optimization.

Careful readers see that current state `n` only depends on previous states `n-1, n-2`; we don't need such a long DP table — just track the previous two states.

So we can further optimize space to O(1) — the most common Fibonacci algorithm:

```java
int fib(int n) {
    if (n == 0 || n == 1) {
        // base case
        return n;
    }
    // represent dp[i - 1] and dp[i - 2]
    int dp_i_1 = 1, dp_i_2 = 0;
    for (int i = 2; i <= n; i++) {
        // dp[i] = dp[i - 1] + dp[i - 2];
        int dp_i = dp_i_1 + dp_i_2;
        // rolling update
        dp_i_2 = dp_i_1;
        dp_i_1 = dp_i;
    }
    return dp_i_1;
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/fibonacci-number/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌟 Code visualization 🌟</strong>
</summary>
</details>
</a>
<hr/>



This is usually DP's last optimization step: if each transition only needs part of the DP table, shrink the table to record only the necessary data, lowering space.

Above shrunk DP table size from `n` to 2 — one order of magnitude space reduction. The later post [Dimensional reduction on DP](https://labuladong.online/algo/dynamic-programming/space-optimization/) explores space compression in depth — usually compressing 2D DP to 1D, from O(n^2) to O(n).

Some ask: DP's other key property "optimal substructure" — what about that? Coming below. The Fibonacci example strictly isn't DP since no optimum is sought; it's mainly to illustrate eliminating overlapping subproblems and the path to optimal solutions. Now the second example: coin change.

## 2. Coin change

LeetCode 322 "Coin Change":

Given `k` coin denominations `c1, c2, ..., ck` (each in unlimited supply) and a target amount `amount`, what's the **minimum** number of coins needed to make up that amount? Return -1 if impossible. Function signature:

```java
// coins are the available denominations; amount is the target
int coinChange(int[] coins, int amount);
```

E.g. `k = 3`, denominations 1, 2, 5; `amount = 11`. Answer: 3 coins (11 = 5 + 5 + 1).

How should the computer solve this? Obviously, enumerate every possible coin combination and find the minimum.

### Brute-force recursion

This is DP because it has **optimal substructure**. **For optimal substructure, subproblems must be mutually independent**. What does that mean? You don't want a math proof — let me give an intuitive example.

Suppose in an exam each subject's score is independent. Original problem: get the highest total. Subproblems: max Chinese, max math, etc. To max each subject, max each multiple-choice section, max each fill-in section, etc. Eventually, perfect on every subject = the highest total.

Correct result: highest total = total of perfect scores. Because each subproblem ("max each subject") is independent.

But add a condition: your Chinese and math scores constrain each other — they can't both be perfect; higher math means lower Chinese, and vice versa.

Now you can't reach the total-perfect-score, and the previous approach gives a wrong result. The "max each subject" subproblems aren't independent — Chinese and math interact, so they can't be simultaneously optimal — optimal substructure breaks.

Back to coin change: why does it have optimal substructure? Suppose denominations are `1, 2, 5` and we want the minimum coins for `amount = 11` (original). If we know the minimum coins for `amount = 10, 9, 6` (subproblems), just add 1 (one more coin of denomination 1, 2, or 5) and take the minimum — that's the original answer. Coins are unlimited, so subproblems don't constrain each other — independent.







> [!TIP]
> The later post [DP FAQ](https://labuladong.online/algo/dynamic-programming/faq-summary/) explores optimal substructure further.

Knowing this is DP, how do we write the right state-transition equation?

**1. Identify "state" — the variable that changes between original and subproblems**. Coins are unlimited, denominations are given by the problem, only the target amount keeps approaching the base case. So the only "state" is the target amount `amount`.

**2. Identify "choice" — the action that changes the state**. Why does the target change? Because you choose a coin — each choice reduces the target. So the "choice" is the set of all coin denominations.

**3. Clarify the `dp` function/array meaning**. We're doing the top-down version, so we have a recursive `dp` function. Generally, the parameters are the changing state (above), and the return value is what the problem asks for. Here, only one state (target amount), and we want the minimum coins.

**So `dp(n)` = given target amount `n`, return the minimum coins needed**.

By this definition, the final answer is `dp(amount)`.

With these clear, write the pseudocode:

```java
// pseudocode framework
int coinChange(int[] coins, int amount) {
    // the answer is dp(amount)
    return dp(coins, amount);
}

// definition: to make up amount n, at least dp(coins, n) coins
int dp(int[] coins, int n) {
    // make a choice — pick the one needing the fewest coins
    for (int coin : coins) {
        res = min(res, 1 + dp(coins, n - coin));
    }
    return res;
}
```

Add base cases: target 0 needs 0 coins; target < 0 has no solution, return -1:

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        // the answer is dp(amount)
        return dp(coins, amount);
    }

    // definition: to make up amount n, at least dp(coins, n) coins
    int dp(int[] coins, int amount) {
        // base case
        if (amount == 0) return 0;
        if (amount < 0) return -1;

        int res = Integer.MAX_VALUE;
        for (int coin : coins) {
            // compute the subproblem
            int subProblem = dp(coins, amount - coin);
            // skip if the subproblem has no solution
            if (subProblem == -1) continue;
            // pick the optimal subproblem result, then add one
            res = Math.min(res, subProblem + 1);
        }

        return res == Integer.MAX_VALUE ? -1 : res;
    }
}
```

> [!NOTE]
> `coinChange` and `dp` have identical signatures; in theory we don't need a separate `dp` function. But for clarity I kept it.
> 
> Readers often ask why we add 1 to the subproblem result (`subProblem + 1`) instead of the coin amount. Reminder: DP's key is the `dp` function/array's definition — what does the return value represent? Once clear, you'll see why we add 1 to the subproblem return.


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/coin-change-brute-force/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Code visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>

State-transition equation done — this is brute force. Math form:

![](https://labuladong.online/algo/images/dynamic-programming/coin.png)

We just need to remove overlapping subproblems. With `amount = 11, coins = {1,2,5}`, draw the recursion tree:

![](https://labuladong.online/algo/images/dynamic-programming/5.jpg)

**Recursive time = subproblem count × per-subproblem time**.

Subproblem count = recursion tree node count, but the algorithm prunes; pruning depends on coin denominations, so the tree grows irregularly — exact node count is hard to compute. We typically estimate worst-case upper bound.

Suppose target `n` and `k` denominations. The tree's worst-case height is `n` (all denomination-1 coins); assume it's a perfect `k`-ary tree — total nodes ~`k^n`.

Per-subproblem complexity: each call has a for-loop, $O(k)$. Total $O(k^n)$ — exponential.

### Memoized recursion

Like Fibonacci, slight modification adds a memo:

```java
class Solution {
    int[] memo;

    public int coinChange(int[] coins, int amount) {
        memo = new int[amount + 1];
        // initialize memo with a special value meaning not computed
        Arrays.fill(memo, -666);

        return dp(coins, amount);
    }

    int dp(int[] coins, int amount) {
        if (amount == 0) return 0;
        if (amount < 0) return -1;
        // check memo to avoid recomputation
        if (memo[amount] != -666)
            return memo[amount];

        int res = Integer.MAX_VALUE;
        for (int coin : coins) {
            // compute subproblem
            int subProblem = dp(coins, amount - coin);
            // skip if no solution
            if (subProblem == -1) continue;
            // pick optimal subproblem, add one
            res = Math.min(res, subProblem + 1);
        }
        // store in memo
        memo[amount] = (res == Integer.MAX_VALUE) ? -1 : res;
        return memo[amount];
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/coin-change/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🥳 Code visualization 🥳</strong>
</summary>
</details>
</a>
<hr/>



No diagram — clearly the memo greatly reduces the subproblem count, eliminating redundancy. Subproblem count is bounded by amount $n$, so $O(n)$. Per-subproblem time is still $O(k)$, so total $O(kn)$.

### Iterative dp-array solution

We can also use a bottom-up DP table. State, choices, base case unchanged. The `dp` array's meaning mirrors the `dp` function — target amount as the variable. The function uses parameters; the array uses indices:

**`dp` array definition: at target amount `i`, at least `dp[i]` coins are needed**.

By the framework above:

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        // array size = amount + 1, init value = amount + 1
        Arrays.fill(dp, amount + 1);

        // base case
        dp[0] = 0;
        // outer for-loop iterates over all states and their values
        for (int i = 0; i < dp.length; i++) {
            // inner for-loop takes the min over all choices
            for (int coin : coins) {
                // skip if subproblem has no solution
                if (i - coin < 0) {
                    continue;
                }
                dp[i] = Math.min(dp[i], 1 + dp[i - coin]);
            }
        }
        return (dp[amount] == amount + 1) ? -1 : dp[amount];
    }
}
```

> [!NOTE]
> Why initialize `dp` with `amount + 1`? The minimum coins to make `amount` is at most `amount` (all 1-denomination coins), so `amount + 1` is effectively positive infinity, convenient for taking minimums. Why not `Integer.MAX_VALUE`? Because `dp[i - coin] + 1` could overflow.

![](https://labuladong.online/algo/images/dynamic-programming/6.jpg)

## 3. Final summary

The first problem (Fibonacci) explained how to optimize the recursion tree via "memo" or "DP table" — they're essentially the same, just top-down vs bottom-up.

The second problem (coin change) showed how to systematically derive the "state-transition equation". Once you have the equation, the rest is just optimizing the recursion tree to eliminate overlapping subproblems.

If you didn't know DP and made it this far, congrats — you've grasped the design technique.

**Computers have no special tricks for solving problems — the only method is enumeration**. Algorithm design is first about "how to enumerate", then "how to enumerate cleverly".

Listing the state-transition equation is "how to enumerate". It's hard because (1) most enumerations need recursion, (2) some problems' solution spaces are complex and not easily fully enumerated.

Memo and DP table are "how to enumerate cleverly". Trading space for time is the only way to lower time complexity. What other tricks could there be?

We have a chapter dedicated to DP problems later. Re-read this article anytime. When reading each problem and solution, lean on "state" and "choice" to internalize this framework.







<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [How to choose base cases and memo initial values?](https://labuladong.online/algo/dynamic-programming/memo-fundamental/)
 - [Practice: Classic BFS problems II](https://labuladong.online/algo/problem-set/bfs-ii/)
 - [Practice: Classic binary-search problems](https://labuladong.online/algo/problem-set/binary-search/)
 - [Practice: Generic monotonic-queue implementation and classic problems](https://labuladong.online/algo/problem-set/monotonic-queue/)
 - [Practice: Solving with both mindsets at once](https://labuladong.online/algo/problem-set/binary-tree-combine-two-view/)
 - [Practice: Math-trick problems](https://labuladong.online/algo/problem-set/math-tricks/)
 - [One method demolishes the LeetCode House Robber series](https://labuladong.online/algo/dynamic-programming/house-robber/)
 - [One method demolishes the LeetCode stock buy/sell series](https://labuladong.online/algo/dynamic-programming/stock-problem-summary/)
 - [Binary tree basics and common types](https://labuladong.online/algo/data-structure-basic/binary-tree-basic/)
 - [Core outline of the binary-tree algorithms series](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
 - [Divide-and-conquer problem-solving framework](https://labuladong.online/algo/essential-technique/divide-and-conquer/)
 - [DP template for subsequence problems](https://labuladong.online/algo/dynamic-programming/subsequence-problem/)
 - [DP: minimum path sum](https://labuladong.online/algo/dynamic-programming/minimum-path-sum/)
 - [Mental switch between DP and backtracking](https://labuladong.online/algo/dynamic-programming/word-break/)
 - [DP helped me clear "Fallout 4"](https://labuladong.online/algo/dynamic-programming/freedom-trail/)
 - [DP helped me clear "Magic Tower"](https://labuladong.online/algo/dynamic-programming/magic-tower/)
 - [Two perspectives on DP enumeration](https://labuladong.online/algo/dynamic-programming/two-views-of-dp/)
 - [DP design: maximum subarray](https://labuladong.online/algo/dynamic-programming/maximum-subarray/)
 - [DP design: longest increasing subsequence](https://labuladong.online/algo/dynamic-programming/longest-increasing-subsequence/)
 - [The framework mindset for learning data structures and algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Dimensional reduction on dynamic programming](https://labuladong.online/algo/dynamic-programming/space-optimization/)
 - [Extension: merge sort in detail and applications](https://labuladong.online/algo/practice-in-action/merge-sort/)
 - [Travel saving trick: weighted shortest path](https://labuladong.online/algo/dynamic-programming/cheap-travel/)
 - [Optimal substructure and dp-array traversal direction](https://labuladong.online/algo/dynamic-programming/faq-summary/)
 - [Algorithm learning and the flow experience](https://labuladong.online/algo/fname.html?fname=心流)
 - [A practical guide to time/space-complexity analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/)
 - [Cheap tricks for algorithm tests](https://labuladong.online/algo/other-skills/tips-in-exam/)
 - [Classic DP: 0-1 knapsack](https://labuladong.online/algo/dynamic-programming/knapsack1/)
 - [Classic DP: game theory](https://labuladong.online/algo/dynamic-programming/game-theory/)
 - [Classic DP: subset-sum knapsack](https://labuladong.online/algo/dynamic-programming/knapsack2/)
 - [Classic DP: unbounded knapsack](https://labuladong.online/algo/dynamic-programming/knapsack3/)
 - [Classic DP: burst balloons](https://labuladong.online/algo/dynamic-programming/burst-balloons/)
 - [Classic DP: longest common subsequence](https://labuladong.online/algo/dynamic-programming/longest-common-subsequence/)
 - [Classic DP: regular expression matching](https://labuladong.online/algo/dynamic-programming/regular-expression-matching/)
 - [Classic DP: edit distance](https://labuladong.online/algo/dynamic-programming/edit-distance/)
 - [Classic DP: super-egg-drop](https://labuladong.online/algo/dynamic-programming/egg-drop/)
 - [Veteran-driver gas-station algorithm](https://labuladong.online/algo/frequency-interview/gas-station-greedy/)
 - [Knapsack variant: target sum](https://labuladong.online/algo/dynamic-programming/target-sum/)
 - [Greedy problem-solving framework](https://labuladong.online/algo/essential-technique/greedy/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems citing this article</strong></summary>

<strong>Install [my Chrome extension](https://labuladong.online/algo/intro/chrome/) and click any problem below to view its solution outline:</strong>

| LeetCode | 力扣 | Difficulty |
| :----: | :----: | :----: |
| [111. Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree/?show=1) | [111. 二叉树的最小深度](https://leetcode.cn/problems/minimum-depth-of-binary-tree/?show=1) | 🟢 |
| [112. Path Sum](https://leetcode.com/problems/path-sum/?show=1) | [112. 路径总和](https://leetcode.cn/problems/path-sum/?show=1) | 🟢 |
| [115. Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/?show=1) | [115. 不同的subsequence](https://leetcode.cn/problems/distinct-subsequences/?show=1) | 🔴 |
| [139. Word Break](https://leetcode.com/problems/word-break/?show=1) | [139. 单词拆分](https://leetcode.cn/problems/word-break/?show=1) | 🟠 |
| [1696. Jump Game VI](https://leetcode.com/problems/jump-game-vi/?show=1) | [1696. 跳跃游戏 VI](https://leetcode.cn/problems/jump-game-vi/?show=1) | 🟠 |
| [221. Maximal Square](https://leetcode.com/problems/maximal-square/?show=1) | [221. 最大正方形](https://leetcode.cn/problems/maximal-square/?show=1) | 🟠 |
| [240. Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/?show=1) | [240. 搜索二维矩阵 II](https://leetcode.cn/problems/search-a-2d-matrix-ii/?show=1) | 🟠 |
| [256. Paint House](https://leetcode.com/problems/paint-house/?show=1)🔒 | [256. 粉刷房子](https://leetcode.cn/problems/paint-house/?show=1)🔒 | 🟠 |
| [279. Perfect Squares](https://leetcode.com/problems/perfect-squares/?show=1) | [279. 完全平方数](https://leetcode.cn/problems/perfect-squares/?show=1) | 🟠 |
| [343. Integer Break](https://leetcode.com/problems/integer-break/?show=1) | [343. 整数拆分](https://leetcode.cn/problems/integer-break/?show=1) | 🟠 |
| [365. Water and Jug Problem](https://leetcode.com/problems/water-and-jug-problem/?show=1) | [365. 水壶问题](https://leetcode.cn/problems/water-and-jug-problem/?show=1) | 🟠 |
| [542. 01 Matrix](https://leetcode.com/problems/01-matrix/?show=1) | [542. 01 矩阵](https://leetcode.cn/problems/01-matrix/?show=1) | 🟠 |
| [576. Out of Boundary Paths](https://leetcode.com/problems/out-of-boundary-paths/?show=1) | [576. 出界的路径数](https://leetcode.cn/problems/out-of-boundary-paths/?show=1) | 🟠 |
| [62. Unique Paths](https://leetcode.com/problems/unique-paths/?show=1) | [62. 不同路径](https://leetcode.cn/problems/unique-paths/?show=1) | 🟠 |
| [63. Unique Paths II](https://leetcode.com/problems/unique-paths-ii/?show=1) | [63. 不同路径 II](https://leetcode.cn/problems/unique-paths-ii/?show=1) | 🟠 |
| [70. Climbing Stairs](https://leetcode.com/problems/climbing-stairs/?show=1) | [70. 爬楼梯](https://leetcode.cn/problems/climbing-stairs/?show=1) | 🟢 |
| [91. Decode Ways](https://leetcode.com/problems/decode-ways/?show=1) | [91. 解码方法](https://leetcode.cn/problems/decode-ways/?show=1) | 🟠 |
| - | [剑指 Offer 04. 二维数组中的查找](https://leetcode.cn/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/?show=1) | 🟠 |
| - | [剑指 Offer 10- I. 斐波那契数列](https://leetcode.cn/problems/fei-bo-na-qi-shu-lie-lcof/?show=1) | 🟢 |
| - | [剑指 Offer 10- II. 青蛙跳台阶问题](https://leetcode.cn/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/?show=1) | 🟢 |
| - | [剑指 Offer 14- I. 剪绳子](https://leetcode.cn/problems/jian-sheng-zi-lcof/?show=1) | 🟠 |
| - | [剑指 Offer 46. 把数字翻译成字符串](https://leetcode.cn/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/?show=1) | 🟠 |
| - | [剑指 Offer II 091. 粉刷房子](https://leetcode.cn/problems/JEj789/?show=1) | 🟠 |
| - | [剑指 Offer II 097. subsequence的数目](https://leetcode.cn/problems/21dk04/?show=1) | 🔴 |
| - | [剑指 Offer II 098. 路径的数目](https://leetcode.cn/problems/2AoeFn/?show=1) | 🟠 |
| - | [剑指 Offer II 103. 最少的硬币数目](https://leetcode.cn/problems/gaM7Ch/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**
