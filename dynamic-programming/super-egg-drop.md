# Classic DP: super egg drop



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | 力扣 | Difficulty |
| :----: | :----: | :----: |
| [887. Super Egg Drop](https://leetcode.com/problems/super-egg-drop/) | [887. 鸡蛋掉落](https://leetcode.cn/problems/super-egg-drop/) | 🔴 |

**-----------**



> [!NOTE]
> Before reading, you should first study:
> 
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)

This article covers a classic algorithm problem: given several floors and several eggs, compute the minimum number of trials in the worst case to find the floor where the egg just doesn't break. Domestic big-tech and Google/Facebook interviews often ask this — they sometimes consider eggs wasteful and use cups or bowls instead.

The problem details come shortly. There are many techniques: even DP alone has several approaches with different efficiencies, and there's even an extremely efficient mathematical solution. Following this book's style, I avoid overly tricky methods because they don't generalize and aren't worth learning.

Below, we use our usual general DP routine to study this problem.

## 1. Problem analysis

This is LeetCode 887 "Super Egg Drop". Description:

You stand in front of a building with floors numbered 1 to `N`, and you have `K` eggs (`K >= 1`). The building has some floor `0 <= F <= N` such that an egg dropped from this floor **just doesn't break** (any floor higher than `F` makes it break; any lower doesn't. If an egg doesn't break, you can pick it up and reuse it). Find the **minimum** number of egg drops in the **worst case** to **determine** floor `F`.

So we want to find the highest floor where the egg just doesn't break (`F`). What does "minimum in the worst case" mean? Examples make it clear.

Suppose **we ignore the egg-count constraint** with 7 floors. The most primitive way is linear scan: floor 1 — doesn't break; floor 2 — doesn't break; floor 3 — and so on. Worst case is testing all the way to floor 7 with no break (`F = 7`) — i.e. 7 drops. **Egg breaking happens once the search range is exhausted** — that's the worst case.

Now still ignoring egg constraint, the best strategy is binary search: drop at `(1 + 7) / 2 = 4` first. If it breaks, `F < 4`, try `(1+3)/2 = 2`. If not, try `(5+7)/2 = 6`. Worst case is `ceil(log7) = 3` trials — fewer than 7 — that's the "minimum".

But **with an egg constraint `K`, plain binary search doesn't work**. With 1 egg and 7 floors: drop at floor 4 — if it doesn't break, fine, try higher; but if it breaks, you have no eggs left to test, and can't determine `F`. So with 1 egg you must linearly scan from floor 1 upward — worst case 7 drops, algorithm returns 7.

Some readers might think: binary first, then linear when only 1 egg remains — would that give the minimum? No. Take 100 floors, 2 eggs. Drop at 50: it breaks. You can only linearly scan 1–49 — worst case 50 drops.

Skipping plain binary works better when you "five-split" or "ten-split". E.g. drop the first egg every 10 floors, and once it breaks, linearly scan with the second — total at most 20. The actual optimum is 14. Many optimal strategies exist with no obvious pattern.

All this just to make sure you understand the problem and see it's genuinely complex — even by hand it's not easy. So how do we solve it algorithmically?

## 2. Approach analysis

For DP problems, just plug into our familiar framework: identify "states" and "choices", then enumerate.

**The "state" is clearly the current number of eggs `K` and the number of floors `N` to test**. As testing proceeds, eggs may decrease and the floor search range shrinks — that's the state evolving.

**The "choice" is which floor to drop the egg from**. Recall linear scan vs. binary: binary picks the middle, linear goes one floor at a time. Different choices cause different state transitions.

With states and choices clear, **the basic DP idea is set**: a 2D `dp` array or a `dp` function with two state parameters represents the state transitions; plus a for-loop enumerates all choices and picks the best:

```java
// definition: state is K eggs facing N floors;
// returns the minimum egg drops in this state
int dp(int K, int N) {
    int res;
    for (int i = 1; i <= N; i++) {
        res = Math.min(res, this drop is at floor i);
    }
    return res;
}
```

This pseudo-code doesn't show recursion or transition yet, but the framework is mostly there.

After dropping at floor `i`, two cases: egg breaks, egg doesn't break. **Now the state transition appears**:

**If the egg breaks**, eggs `K` decreases by 1, and the floor search range shrinks from `[1..N]` to `[1..i-1]` — `i-1` floors;

**If the egg doesn't break**, eggs `K` stays the same, and the floor search range shrinks from `[1..N]` to `[i+1..N]` — `N-i` floors.

![](https://labuladong.online/algo/images/drop-egg/1.jpg)

> [!NOTE]
> Careful readers may ask: if dropped at floor `i` and it doesn't break, shouldn't the upper search range include floor `i`? No, it's already included. As stated, `F` can equal 0; after recursion, floor `i` becomes floor 0, so it's reachable — no error.

Since we want the **minimum drops in the worst case**, whether the egg breaks or not at floor `i` depends on which case yields the **larger** result:

```java
int dp(int K, int N):
    for 1 <= i <= N:
        // minimum egg drops in the worst case
        res = min(res, max(
                // breaks
                dp(K - 1, i - 1),
                // doesn't break
                dp(K, N - i),
            ) + 1
            // dropped once at floor i, so add 1
        )
    return res
```

Recursion base cases are easy: when `N == 0` no drops needed; when `K == 1` we can only linearly scan all floors:

```java
int dp(int K, int N) {
    // base case
    if (K == 1) return N;
    if (N == 0) return 0;
    // ...
}
```

Done! Just add a memo to eliminate overlapping subproblems:

```java
class Solution {
    // memo
    int[][] memo;

    public int superEggDrop(int K, int N) {
        // m won't exceed N (linear scan)
        memo = new int[K + 1][N + 1];
        for (int[] row : memo) {
            Arrays.fill(row, -666);
        }
        return dp(K, N);
    }

    // definition: with K eggs facing N floors, minimum drops is dp(K, N)
    int dp(int K, int N) {
        // base case
        if (K == 1) return N;
        if (N == 0) return 0;

        // check memo to avoid redundant computation
        if (memo[K][N] != -666) {
            return memo[K][N];
        }
        // state-transition equation
        int res = Integer.MAX_VALUE;
        for (int i = 1; i <= N; i++) {
            // try every floor; take the minimum drops
            res = Math.min(
                res,
                // breaks vs not — take the worst
                Math.max(dp(K, N - i), dp(K - 1, i - 1)) + 1
            );
        }
        // store in memo
        memo[K][N] = res;
        return res;
    }
}
```

Time complexity? **DP time = number of subproblems × time per call**. The function itself has a for-loop, so each call is O(N). Subproblems = total state combos = O(KN). Total: O(K·N²); space O(KN).

Complex problem, simple code — DP's hallmark: enumerate + memo/DP-table. No magic.

Some readers may not understand why we use a for-loop over floors `[1..N]` — confusing it with linear scan. No, **this is just making one "choice"**.

With 2 eggs facing 10 floors, which floor do you choose **this time**? You don't know — try all 10. The next choice is for the recursion to decide; the algorithm computes each choice's cost, and we pick the optimum.

There are even better solutions: replace the for-loop with binary search to get O(K·N·logN); further refine to O(KN); use math to reach optimal O(K·logN) time and O(1) space.

The binary-search version is misleading — it has nothing to do with the binary-search egg drop strategy from earlier. We can use binary search because the state-transition equation's function shape is monotone, so we can quickly find the optimum.

Now let's see the optimization.

## 3. Binary-search optimization

The optimization's core is the monotonicity of the state transition. First, recap the original DP:

1. Brute-force try every floor `1 <= i <= N`; pick the floor minimizing the worst-case drops.
2. Each drop has two outcomes: break or not.
3. If the egg breaks, `F` is below floor `i`; otherwise, `F` is at or above `i`.
4. Whether it breaks depends on which case has more trials, since we want the worst case.

Core code:

```java
// state: K eggs facing N floors
// returns the optimal result for this state
int dp(int K, int N):
    for 1 <= i <= N:
        // minimum drops in worst case
        res = min(res, max(
                    // breaks
                    dp(K - 1, i - 1),
                    // doesn't break
                    dp(K, N - i),
                ) + 1
                // dropped once at floor i, add 1
            )
    return res
```

This for-loop implements the state-transition equation:

![](https://labuladong.online/algo/images/drop-egg/formula1.png)

If you understand it, the binary-search optimization is easy.

By the `dp(K, N)` definition (K eggs, N floors, minimum drops needed), **with K fixed, this function is strictly increasing in N** — no matter how clever, more floors means more trials.

Now look at `dp(K - 1, i - 1)` and `dp(K, N - i)`. With `i` going from 1 to N, fixing K and N, **viewing them as functions of `i`, the former is strictly increasing in `i`, the latter strictly decreasing**:

![](https://labuladong.online/algo/images/drop-egg/2.jpg)

Taking max of the two and then min over `i` is exactly finding the intersection of the two lines — the lowest point of the red broken line.

Earlier in [Binary search application in practice](https://labuladong.online/algo/frequency-interview/binary-search-in-action/), we said binary search is widely applicable — wherever you find a monotone relation, binary search likely beats linear search. The lowest point we want corresponds to:

```java
for (int i = 1; i <= N; i++) {
    if (dp(K - 1, i - 1) == dp(K, N - i))
        return dp(K, N - i);
}
```

Familiar with binary search? This is finding a Valley value — quickly findable by binary. Code transforms the linear search in `dp` into binary search:

```java
class Solution {
    // memo
    int[][] memo;

    public int superEggDrop(int K, int N) {
        // m won't exceed N (linear scan)
        memo = new int[K + 1][N + 1];
        for (int[] row : memo) {
            Arrays.fill(row, -666);
        }
        return dp(K, N);
    }

    // definition: with K eggs facing N floors, minimum drops is dp(K, N)
    int dp(int K, int N) {
        // base case
        if (K == 1) return N;
        if (N == 0) return 0;

        // check memo to avoid redundant computation
        if (memo[K][N] != -666) {
            return memo[K][N];
        }

        // for (int i = 1; i <= N; i++) {
        //     res = Math.min(
        //         res,
        //         Math.max(dp(K, N - i), dp(K - 1, i - 1)) + 1
        //     );
        // }

        // use binary search instead of linear search
        int res = Integer.MAX_VALUE;
        int lo = 1, hi = N;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            // egg breaks vs doesn't break at floor mid
            int broken = dp(K - 1, mid - 1);
            int not_broken = dp(K, N - mid);
            // res = min(max(broken, not_broken) + 1)
            if (broken > not_broken) {
                hi = mid - 1;
                res = Math.min(res, broken + 1);
            } else {
                lo = mid + 1;
                res = Math.min(res, not_broken + 1);
            }
        }
        memo[K][N] = res;
        return res;
    }
}
```

Time complexity: each `dp` call is O(logN); subproblem count is O(KN); total O(KN·logN), space O(KN). Better than the previous O(KN²).

## 4. Redefining the state transition

Finding state transitions is partly art. Different definitions yield different solutions of vastly different complexities. This is a great example.

Recall our earlier `dp` definition:

```java
int dp(int k, int n)
// state: k eggs facing n floors
// returns minimum egg drops in this state
```

In array form:

```java
dp[k][n] = m
// state: k eggs facing n floors
// minimum drops in this state is m
```

By this definition, **given current eggs and floors, we know minimum drops**. Final answer is `dp(K, N)`.

This forces enumeration of all drop strategies; binary search just prunes the search space.

Now, **we slightly change the `dp` definition: given current eggs and maximum allowed drops, we know the highest floor we can determine `F` for**. Concretely:

```java
dp[k][m] = n
// k eggs, allowed up to m drops
// in this state, in the worst case we can pinpoint F for an n-floor building

// e.g. dp[1][7] = 7 means:
// 1 egg, 7 drops allowed,
// in this state, with at most 7 floors,
// you can determine F (the floor where the egg just doesn't break)
// (linear scan floor by floor)
```

This is essentially the "reverse" of our original idea. Don't worry about the state transition yet — first think: given this definition, what's the final answer?

We ultimately want drops `m`, but `m` is in the state, not the result. Handle it like this:

```java
int superEggDrop(int K, int N) {

    int m = 0;
    while (dp[K][m] < N) {
        m++;
        // state transition...
    }
    return m;
}
```

The problem is **given K eggs, N floors, find minimum worst-case drops `m`**. The while loop ends when `dp[K][m] == N`, i.e. **K eggs, m drops, worst-case can determine `F` for at most N floors**.

Look at these two descriptions — exactly equivalent! So the structure is correct. The key is finding the state transition.

The earlier figure for state transitions:

![](https://labuladong.online/algo/images/drop-egg/1.jpg)

That figure shows just one floor `i`; the original solution scans every floor. With this new `dp` definition, we don't need that. Two facts:

**1. Wherever you drop the egg, it either breaks (test below) or doesn't (test above)**.

**2. Whether you go up or down, total floors = upper floors + lower floors + 1 (current floor)**.

So:

```python
dp[k][m] = dp[k][m - 1] + dp[k - 1][m - 1] + 1
```

**`dp[k][m - 1]` is upper floors** — eggs `k` unchanged (didn't break), drops `m` decrement;

**`dp[k - 1][m - 1]` is lower floors** — eggs `k` decremented (broke), drops `m` decrement.

> [!NOTE]
> Why subtract 1, not add? As defined, `m` is an upper bound on allowed drops, not a count of how many we've dropped.

![](https://labuladong.online/algo/images/drop-egg/3.jpg)

Plug the transition into the framework:

```java
class Solution {
    public int superEggDrop(int K, int N) {
        // m won't exceed N (linear scan)
        int[][] dp = new int[K + 1][N + 1];
        // base case:
        // dp[0][..] = 0
        // dp[..][0] = 0
        // Java initializes arrays to 0 by default
        int m = 0;
        while (dp[K][m] < N) {
            m++;
            for (int k = 1; k <= K; k++)
                dp[k][m] = dp[k][m - 1] + dp[k - 1][m - 1] + 1;
        }
        return m;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/super-egg-drop/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>👾 Code visualization 👾</strong>
</summary>
</details>
</a>
<hr/>



If this still seems hard, equivalently:

```java
for (int m = 1; dp[K][m] < N; m++)
    for (int k = 1; k <= K; k++)
        dp[k][m] = dp[k][m - 1] + dp[k - 1][m - 1] + 1;
```

This form is more familiar. Since we want a particular index `m` rather than a value in `dp`, we use a `while` loop to find that `m`.

Time complexity: O(KN), the two nested loops.

Also note `dp[m][k]` only depends on its left and upper-left neighbors, so per the earlier [DP space-compression technique](https://labuladong.online/algo/dynamic-programming/space-optimization/), it can be optimized to a 1D array. We'll skip that.

## 5. Further optimization

Beyond this, more optimization is possible — I won't expand, just sketch the idea.

On top of the above, **`dp(m, k)` is increasing in `m`**, since with `k` fixed, more allowed drops means a higher reachable building.

Binary search can quickly approach the termination `dp[K][m] == N`, dropping time to O(K·logN). I think writing the O(K·N·logN) binary-optimized algorithm is enough. The further refinements aren't worth memorizing — keeping desire within ability brings happiness!

The code framework would replace the linear `m` enumeration with binary:

```java
// change linear search to binary search
// for (int m = 1; dp[K][m] < N; m++)
int lo = 1, hi = N;
while (lo < hi) {
    int mid = (lo + hi) / 2;
    if (... < N) {
        lo = ...
    } else {
        hi = ...
    }
    
    for (int k = 1; k <= K; k++) {
        // state-transition equation
    }
}
```

Quick summary: the first binary optimization uses the monotonicity of the `dp` function to quickly find the answer; the second cleverly modifies the state-transition equation, simplifying the procedure but harder to think of; further math + binary on the second is possible but not worth memorizing.







<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [Mental framework for applying binary search in practice](https://labuladong.online/algo/frequency-interview/binary-search-in-action/)
 - [Optimal substructure and dp-array traversal direction](https://labuladong.online/algo/dynamic-programming/faq-summary/)
 - [Classic DP: burst balloons](https://labuladong.online/algo/dynamic-programming/burst-balloons/)

</details><hr>





**＿＿＿＿＿＿＿＿＿＿＿＿＿**
