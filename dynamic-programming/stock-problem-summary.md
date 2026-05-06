# One method demolishes the LeetCode stock buy/sell series



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | 力扣 | Difficulty |
| :----: | :----: | :----: |
| [121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) | [121. 买卖股票的最佳时机](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/) | 🟢 |
| [122. Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/) | [122. 买卖股票的最佳时机 II](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/) | 🟠 |
| [123. Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/) | [123. 买卖股票的最佳时机 III](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/) | 🔴 |
| [188. Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/) | [188. 买卖股票的最佳时机 IV](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/) | 🔴 |
| [309. Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/) | [309. 最佳买卖股票时机含冷冻期](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/) | 🟠 |
| [714. Best Time to Buy and Sell Stock with Transaction Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/) | [714. 买卖股票的最佳时机含手续费](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/) | 🟠 |

**-----------**



> [!NOTE]
> Before reading, you should first study:
> 
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)

Many readers complain that the LeetCode stock series has too many solutions. If they actually face one in an interview, they wouldn't think of those clever tricks. What to do? **So this article doesn't cover overly clever ideas. Steady and solid — one universal method to solve them all**.

This article references the [English upvoted answer](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/discuss/108870/Most-consistent-ways-of-dealing-with-the-series-of-stock-problems), using the state-machine technique. All submissions pass. Don't be intimidated by the term — it's just a DP table; once you see it, you'll get it.

Let me randomly pull out one and look at someone's solution:





```java
int maxProfit(int[] prices) {
    if(prices.empty()) return 0;
    int s1 = -prices[0], s2 = INT_MIN, s3 = INT_MIN, s4 = INT_MIN;

    for(int i = 1; i < prices.size(); ++i) {            
        s1 = max(s1, -prices[i]);
        s2 = max(s2, s1 + prices[i]);
        s3 = max(s3, s2 - prices[i]);
        s4 = max(s4, s3 + prices[i]);
    }
    return max(0, s4);
}
```



Can you understand it? Could you write it yourself? Of course not — that's normal. Even if you barely understand it, you couldn't solve the next problem. Why can others write such weird, efficient solutions? Because there's a framework, but they won't tell you. Once told, you'd grasp it in five minutes, and the algorithm wouldn't be mysterious anymore.

This article gives you that framework, then walks through one problem after another. Using state-machine techniques, all six problems pass. Don't be scared by the term — it's just a DP table.

These 6 problems share commonality. We just need to extract LeetCode 188 "Best Time to Buy and Sell Stock IV" — the most generalized form; the others are simplifications. Look at the problem:

<Problem slug="best-time-to-buy-and-sell-stock-iv" />

Problem 1 allows only one transaction (`k = 1`); problem 2 has unlimited transactions (`k = +infinity`); problem 3 allows only 2 transactions (`k = 2`); the other two are unlimited but add a "cooldown" or "transaction fee" — variants of problem 2, easy to handle.

Now let's solve them.







## 1. Enumeration framework

First, the same idea: how do we enumerate?

[DP core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/) says DP essentially enumerates "states" and picks the optimum from "choices".

For this problem, look at each day: how many possible "states" are there, and what "choices" correspond to each? We enumerate every state to update each via its corresponding choices. Sounds abstract — just remember "state" and "choice"; we'll see it concretely.

```python
for state1 in all values of state1:
    for state2 in all values of state2:
        for ...
            dp[state1][state2][...] = best(choice1, choice2, ...)
```

For this problem, **each day has three "choices"**: buy, sell, rest — denoted `buy`, `sell`, `rest`.

But you can't freely pick any of the three each day. `sell` must follow a `buy`, and `buy` must follow a `sell`. So `rest` should split into two states: `rest` after `buy` (holding stock) and `rest` after `sell` (not holding). And don't forget the transaction cap `k`: you can only `buy` if `k > 0`.

> [!NOTE]
> I'll often use "transaction"; **one buy + one sell counts as one "transaction"**.







Complex, right? Don't worry. Our goal is enumeration — no matter how many states, we list them all.

**This problem has three "states"**: day index, max-allowed-transaction count, and whether currently holding (the earlier `rest` state — 1 = holding, 0 = not holding). Use a 3D array to hold all combinations:

```python
dp[i][k][0 or 1]
0 <= i <= n - 1, 1 <= k <= K
n is the number of days, K is the upper limit on transactions; 0/1 is hold-state.
n × K × 2 states total — enumerate them all.

for 0 <= i < n:
    for 1 <= k <= K:
        for s in {0, 1}:
            dp[i][k][s] = max(buy, sell, rest)
```

We can also describe each state in natural language. E.g. `dp[3][2][1]` means: today is day 3, I'm holding stock, with at most 2 transactions used. `dp[2][3][0]`: today is day 2, not holding, with at most 3 transactions used.

We want `dp[n - 1][K][0]` — last day, at most `K` transactions, max profit.

Why not `dp[n - 1][K][1]`? Because that means we still hold on the last day. `dp[n - 1][K][0]` means we sold on the last day — the latter is clearly bigger.

Remember how to interpret "states" — translate to natural language whenever stuck.







## 2. State transition framework

Now we've enumerated "states". Let's think about which "choices" each state has and how to update.

Looking only at "hold state", draw the state-transition graph:

![](https://labuladong.online/algo/images/stock/1.png)

This shows clearly how each state (0 and 1) transitions. Write the state-transition equation:

```python
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
              max( today rest,         today sell        )
```

Explanation: today I don't hold; two possibilities — pick the max:

1. Yesterday I didn't hold, with max transactions `k`; today I `rest`, so today I still don't hold, max transactions `k`.

2. Yesterday I held, with max transactions `k`; today I `sell`, so today I don't hold, max transactions still `k`.

```python
dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
              max( today rest,           today buy          )
```

Explanation: today I hold, max transactions `k`; for yesterday, two possibilities — pick the max:

1. Yesterday I held, max transactions `k`; today I `rest`, so today I still hold, max transactions still `k`.

2. Yesterday I didn't hold, max transactions `k - 1`; today I `buy`, so today I hold, max transactions `k`.

> [!NOTE]
> Important reminder: **always remember the "state" definition**. State `k` is NOT "transactions already done" — it's "the upper limit on transactions". If you do one transaction today and want today's upper limit at `k`, then yesterday's upper limit must be `k - 1`. Concrete example: bank account today must have at least $100, and you'll definitely earn $10 today — yesterday's account must have at least $90.

Should be clear: `buy` subtracts `prices[i]` from profit; `sell` adds `prices[i]`. Today's max profit is the max of the two.

Note `k`'s constraint: choosing `buy` opens a new transaction, so yesterday's transaction limit `k` must decrement by 1.

> [!NOTE]
> Correction: I used to think decrementing `k` on `sell` and on `buy` were equivalent, but careful readers questioned this. After deeper thought, the former is wrong. A transaction starts with `buy`, so if `buy` doesn't change `k`, transactions can exceed the limit.







We've completed DP's hardest step — the state transition. **If you understand everything above, you can already demolish all the problems — just plug into this framework**. One last bit: define the base case (simplest situation).

```python
dp[-1][...][0] = 0
Explanation: i starts at 0, so i = -1 means before-start; profit is 0.

dp[-1][...][1] = -infinity
Explanation: before-start, you can't be holding stock.
We seek a max, so initialize to a very small value to ease the max operation.

dp[...][0][0] = 0
Explanation: k starts at 1, so k = 0 means no trading allowed; profit is 0.

dp[...][0][1] = -infinity
Explanation: with no trading allowed, you can't be holding stock.
We seek a max, so initialize to a very small value to ease the max operation.
```

Summarizing:

```python
base case:
dp[-1][...][0] = dp[...][0][0] = 0
dp[-1][...][1] = dp[...][0][1] = -infinity

state-transition equation:
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
```

Readers may ask: how do we represent index -1, or negative infinity? Implementation details — many ways. Now the framework is complete; let's specialize.







## 3. Demolishing the problems

### 121. Best Time to Buy and Sell Stock

**First, LeetCode 121 "Best Time to Buy and Sell Stock" — the `k = 1` case**:

<Problem slug="best-time-to-buy-and-sell-stock" />

Plug into the state transition; with the base case, simplify:

```python
dp[i][1][0] = max(dp[i-1][1][0], dp[i-1][1][1] + prices[i])
dp[i][1][1] = max(dp[i-1][1][1], dp[i-1][0][0] - prices[i]) 
            = max(dp[i-1][1][1], -prices[i])
Explanation: k = 0 base case, so dp[i-1][0][0] = 0.

k is always 1, doesn't change — k no longer affects state transitions.
Simplify by dropping all k:
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], -prices[i])
```

Code:

```java
int n = prices.length;
int[][] dp = new int[n][2];
for (int i = 0; i < n; i++) {
    dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i]);
    dp[i][1] = Math.max(dp[i-1][1], -prices[i]);
}
return dp[n - 1][0];
```

Clearly `i = 0` makes `i - 1` invalid. We didn't handle the `i` base case — special-case it:





```java
if (i - 1 == -1) {
    dp[i][0] = 0;
    // by the state transition:
    //   dp[i][0] 
    // = max(dp[-1][0], dp[-1][1] + prices[i])
    // = max(0, -infinity + prices[i]) = 0

    dp[i][1] = -prices[i];
    // by the state transition:
    //   dp[i][1] 
    // = max(dp[-1][1], dp[-1][0] - prices[i])
    // = max(-infinity, 0 - prices[i]) 
    // = -prices[i]
    continue;
}
```



First problem done. Handling base cases like this is verbose. The transition only depends on the immediately previous state, so per [DP space-compression](https://labuladong.online/algo/dynamic-programming/space-optimization/), we don't need the whole `dp` array — just a variable storing the previous state. Space drops to O(1):

```java
// original version
int maxProfit_k_1(int[] prices) {
    int n = prices.length;
    int[][] dp = new int[n][2];
    for (int i = 0; i < n; i++) {
        if (i - 1 == -1) {
            // base case
            dp[i][0] = 0;
            dp[i][1] = -prices[i];
            continue;
        }
        dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i]);
        dp[i][1] = Math.max(dp[i-1][1], -prices[i]);
    }
    return dp[n - 1][0];
}

// space-optimized version
int maxProfit_k_1(int[] prices) {
    int n = prices.length;
    // base case: dp[-1][0] = 0, dp[-1][1] = -infinity
    int dp_i_0 = 0, dp_i_1 = Integer.MIN_VALUE;
    for (int i = 0; i < n; i++) {
        // dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
        dp_i_0 = Math.max(dp_i_0, dp_i_1 + prices[i]);
        // dp[i][1] = max(dp[i-1][1], -prices[i])
        dp_i_1 = Math.max(dp_i_1, -prices[i]);
    }
    return dp_i_0;
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/best-time-to-buy-and-sell-stock/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Code visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>

Same logic, much cleaner — but without the prior state-transition guidance, hard to follow. For the rest, see how we optimize away the `dp` array's space.

### 122. Best Time to Buy and Sell Stock II

**Second, LeetCode 122 "Best Time to Buy and Sell Stock II" — `k = +infinity`**:

<Problem slug="best-time-to-buy-and-sell-stock-ii" />

The problem stresses you can sell on the same day, but I find this redundant — buying and selling the same day yields 0 profit, same as no trade. The defining feature: no transaction count limit, equivalent to `k = +infinity`.

If `k` is +infinity, then `k` and `k - 1` are the same:

```python
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
            = max(dp[i-1][k][1], dp[i-1][k][0] - prices[i])

k no longer changes — drop it:
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i])
```

Code:

```java
// original version
int maxProfit_k_inf(int[] prices) {
    int n = prices.length;
    int[][] dp = new int[n][2];
    for (int i = 0; i < n; i++) {
        if (i - 1 == -1) {
            // base case
            dp[i][0] = 0;
            dp[i][1] = -prices[i];
            continue;
        }
        dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i]);
        dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0] - prices[i]);
    }
    return dp[n - 1][0];
}

// space-optimized version
int maxProfit_k_inf(int[] prices) {
    int n = prices.length;
    int dp_i_0 = 0, dp_i_1 = Integer.MIN_VALUE;
    for (int i = 0; i < n; i++) {
        int temp = dp_i_0;
        dp_i_0 = Math.max(dp_i_0, dp_i_1 + prices[i]);
        dp_i_1 = Math.max(dp_i_1, temp - prices[i]);
    }
    return dp_i_0;
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/best-time-to-buy-and-sell-stock-ii/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌟 Code visualization 🌟</strong>
</summary>
</details>
</a>
<hr/>

### 309. Best Time to Buy and Sell Stock with Cooldown

**Third, LeetCode 309 "Best Time to Buy and Sell Stock with Cooldown" — `k = +infinity` with a cooldown**:

<Problem slug="best-time-to-buy-and-sell-stock-with-cooldown" />

Same as before, but after every `sell` you wait one day before trading. Just merge this into the previous transition:

```python
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], dp[i-2][0] - prices[i])
Explanation: when buying on day i, transition from day i-2, not i-1.
```

Code:

```java
// original version
int maxProfit_with_cool(int[] prices) {
    int n = prices.length;
    int[][] dp = new int[n][2];
    for (int i = 0; i < n; i++) {
        if (i - 1 == -1) {
            // base case 1
            dp[i][0] = 0;
            dp[i][1] = -prices[i];
            continue;
        }
        if (i - 2 == -1) {
            // base case 2
            dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i]);
            // when i - 2 < 0, derive the corresponding base case from the state-transition equation
            dp[i][1] = Math.max(dp[i-1][1], -prices[i]);
            //   dp[i][1] 
            // = max(dp[i-1][1], dp[-1][0] - prices[i])
            // = max(dp[i-1][1], 0 - prices[i])
            // = max(dp[i-1][1], -prices[i])
            continue;
        }
        dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i]);
        dp[i][1] = Math.max(dp[i-1][1], dp[i-2][0] - prices[i]);
    }
    return dp[n - 1][0];
}

// space-optimized version
int maxProfit_with_cool(int[] prices) {
    int n = prices.length;
    int dp_i_0 = 0, dp_i_1 = Integer.MIN_VALUE;
    // represents dp[i-2][0]
    int dp_pre_0 = 0;
    for (int i = 0; i < n; i++) {
        int temp = dp_i_0;
        dp_i_0 = Math.max(dp_i_0, dp_i_1 + prices[i]);
        dp_i_1 = Math.max(dp_i_1, dp_pre_0 - prices[i]);
        dp_pre_0 = temp;
    }
    return dp_i_0;
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/best-time-to-buy-and-sell-stock-with-cooldown/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Code visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>

### 714. Best Time to Buy and Sell Stock with Transaction Fee



**Fourth, LeetCode 714 "Best Time to Buy and Sell Stock with Transaction Fee" — `k = +infinity` with a per-transaction fee**:

<Problem slug="best-time-to-buy-and-sell-stock-with-transaction-fee" />

Each transaction costs a fee — just subtract it from profit:

```python
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i] - fee)
Explanation: equivalent to the buy price going up.
Subtracting in the first equation is also equivalent — sell price goes down.
```

> [!NOTE]
> If you put `fee` in the first equation, some test cases fail due to integer overflow, not logic. One fix: change `int` to `long`.

Code (note base case adjusts):

```java
// original version
int maxProfit_with_fee(int[] prices, int fee) {
    int n = prices.length;
    int[][] dp = new int[n][2];
    for (int i = 0; i < n; i++) {
        if (i - 1 == -1) {
            // base case
            dp[i][0] = 0;
            dp[i][1] = -prices[i] - fee;
            //   dp[i][1]
            // = max(dp[i - 1][1], dp[i - 1][0] - prices[i] - fee)
            // = max(dp[-1][1], dp[-1][0] - prices[i] - fee)
            // = max(-inf, 0 - prices[i] - fee)
            // = -prices[i] - fee
            continue;
        }
        dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
        dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] - prices[i] - fee);
    }
    return dp[n - 1][0];
}

// space-optimized version
int maxProfit_with_fee(int[] prices, int fee) {
    int n = prices.length;
    int dp_i_0 = 0, dp_i_1 = Integer.MIN_VALUE;
    for (int i = 0; i < n; i++) {
        int temp = dp_i_0;
        dp_i_0 = Math.max(dp_i_0, dp_i_1 + prices[i]);
        dp_i_1 = Math.max(dp_i_1, temp - prices[i] - fee);
    }
    return dp_i_0;
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/best-time-to-buy-and-sell-stock-with-transaction-fee/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Code visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>



### 123. Best Time to Buy and Sell Stock III

**Fifth, LeetCode 123 "Best Time to Buy and Sell Stock III" — `k = 2`**:

<Problem slug="best-time-to-buy-and-sell-stock-iii" />

`k = 2` differs from previous cases. Earlier `k` either was infinity (transitions independent of `k`) or 1 (close to `k = 0` base case, no presence).

Here with `k = 2`, and later with arbitrary positive `k`, `k`'s handling becomes prominent. Let's write code and analyze.

```java
Original state-transition equation, no simplification:
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
```

Naively, you might write this (wrong):

```java
int k = 2;
int[][][] dp = new int[n][k + 1][2];
for (int i = 0; i < n; i++) {
    if (i - 1 == -1) {
        // handle base case
        dp[i][k][0] = 0;
        dp[i][k][1] = -prices[i];
        continue;
    }
    dp[i][k][0] = Math.max(dp[i-1][k][0], dp[i-1][k][1] + prices[i]);
    dp[i][k][1] = Math.max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i]);
}
return dp[n - 1][k][0];
```

Why wrong? I copied the equation!

Remember the "enumeration framework"? We must enumerate every state. Earlier solutions enumerated all states too — `k` just got simplified away.

E.g. problem 1, `k = 1`:

```java
int n = prices.length;
int[][] dp = new int[n][2];
for (int i = 0; i < n; i++) {
    dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i]);
    dp[i][1] = Math.max(dp[i-1][1], -prices[i]);
}
return dp[n - 1][0];
```

But with `k = 2`, since `k` isn't eliminated, we must enumerate it:

```java
// original version
int maxProfit_k_2(int[] prices) {
    int max_k = 2, n = prices.length;
    int[][][] dp = new int[n][max_k + 1][2];
    for (int i = 0; i < n; i++) {
        for (int k = max_k; k >= 1; k--) {
            if (i - 1 == -1) {
                // handle base case
                dp[i][k][0] = 0;
                dp[i][k][1] = -prices[i];
                continue;
            }
            dp[i][k][0] = Math.max(dp[i-1][k][0], dp[i-1][k][1] + prices[i]);
            dp[i][k][1] = Math.max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i]);
        }
    }
    // enumerated n × max_k × 2 states - correct.
    return dp[n - 1][max_k][0];
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/best-time-to-buy-and-sell-stock-iii/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Code visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>



> [!NOTE]
> **Some readers may wonder: `k`'s base case is 0; shouldn't we enumerate `k` from 1 upward? And if you really enumerate `k` from small to large, the submission also passes**.

Good question. The earlier [DP FAQ](https://labuladong.online/algo/dynamic-programming/faq-summary/) explains how to determine `dp` traversal order — based on the base case, building toward the result.

But why does descending `k` also work? Note that `dp[i][k][..]` doesn't depend on `dp[i][k - 1][..]` — it depends on `dp[i - 1][k - 1][..]`, and all of `dp[i - 1][..][..]` are already computed. So whether you go `k = max_k, k--` or `k = 1, k++`, both yield the right answer.

Why do I use `k = max_k, k--`? Because it matches semantics:

When buying stocks, the initial "state" is: starting on day 0, no trading done — so the max-transactions limit `k` should be `max_k`. As "state" advances, you trade, so the upper limit decreases. This way `k = max_k, k--` matches the real-world scenario.

Of course, `k`'s range is small here, so we could skip the for-loop and enumerate `k = 1, 2`:

```java
// state transition:
// dp[i][2][0] = max(dp[i-1][2][0], dp[i-1][2][1] + prices[i])
// dp[i][2][1] = max(dp[i-1][2][1], dp[i-1][1][0] - prices[i])
// dp[i][1][0] = max(dp[i-1][1][0], dp[i-1][1][1] + prices[i])
// dp[i][1][1] = max(dp[i-1][1][1], -prices[i])

// space-optimized version
int maxProfit_k_2(int[] prices) {
    // base case
    int dp_i10 = 0, dp_i11 = Integer.MIN_VALUE;
    int dp_i20 = 0, dp_i21 = Integer.MIN_VALUE;
    for (int price : prices) {
        dp_i20 = Math.max(dp_i20, dp_i21 + price);
        dp_i21 = Math.max(dp_i21, dp_i10 - price);
        dp_i10 = Math.max(dp_i10, dp_i11 + price);
        dp_i11 = Math.max(dp_i11, -price);
    }
    return dp_i20;
}
```

With the state transition and meaningful variable names, this should be readable. We could deliberately obscure it by replacing the four variables with `a, b, c, d` — others would be amazed at your "genius" code.

### 188. Best Time to Buy and Sell Stock IV

Sixth, LeetCode 188 "Best Time to Buy and Sell Stock IV" — `k` is given:

<Problem slug="best-time-to-buy-and-sell-stock-iv" />

After the `k = 2` warmup, this should be similar — just replace 2 with the input `k`.

But you'll get a memory-overrun error: input `k` can be huge, making `dp` too big. How big can the effective `k` be?

A transaction = buy + sell, takes at least 2 days. So the effective `k` is at most `n/2`; beyond that, `k` is unconstrained — already handled.

Reuse previous code:

```java
int maxProfit_k_any(int max_k, int[] prices) {
    int n = prices.length;
    if (n <= 0) {
        return 0;
    }
    if (max_k > n / 2) {
        // reuse the unlimited-k case
        return maxProfit_k_inf(prices);
    }

    // base case:
    // dp[-1][...][0] = dp[...][0][0] = 0
    // dp[-1][...][1] = dp[...][0][1] = -infinity
    int[][][] dp = new int[n][max_k + 1][2];
    // base case for k = 0
    for (int i = 0; i < n; i++) {
        dp[i][0][1] = Integer.MIN_VALUE;
        dp[i][0][0] = 0;
    }

    for (int i = 0; i < n; i++) 
        for (int k = max_k; k >= 1; k--) {
            if (i - 1 == -1) {
                // handle the i = -1 base case
                dp[i][k][0] = 0;
                dp[i][k][1] = -prices[i];
                continue;
            }
            dp[i][k][0] = Math.max(dp[i-1][k][0], dp[i-1][k][1] + prices[i]);
            dp[i][k][1] = Math.max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i]);     
        }
    return dp[n - 1][max_k][0];
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/best-time-to-buy-and-sell-stock-iv/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌟 Code visualization 🌟</strong>
</summary>
</details>
</a>
<hr/>



All 6 problems solved with one state-transition equation.

## All paths lead to one

Made it this far? Round of applause — initially understanding such complex DP must have cost many brain cells. But it's worth it. The stock series is among the harder DP problems; if you can grasp these, others are just shrimp.

**Now you've cleared the first 80 of 81 trials. One last challenge — implement this function**:

```java
int maxProfit_all_in_one(int max_k, int[] prices, int cooldown, int fee);
```

Given prices, at most `max_k` transactions, each transaction costs an extra `fee`, and after each transaction you wait `cooldown` days before the next — return the max profit.

Scared? If someone asked this directly, they'd faint. But step by step, you find this is a combination of the cases we explored.

So just merge the previous code, adding `cooldown` and `fee` to base cases and state transitions:

```java
// consider transaction limit, cooldown, and fee simultaneously
int maxProfit_all_in_one(int max_k, int[] prices, int cooldown, int fee) {
    int n = prices.length;
    if (n <= 0) {
        return 0;
    }
    if (max_k > n / 2) {
        // unlimited-k case
        return maxProfit_k_inf(prices, cooldown, fee);
    }

    int[][][] dp = new int[n][max_k + 1][2];
    // base case for k = 0
    for (int i = 0; i < n; i++) {
        dp[i][0][1] = Integer.MIN_VALUE;
        dp[i][0][0] = 0;
    }

    for (int i = 0; i < n; i++) 
        for (int k = max_k; k >= 1; k--) {
            if (i - 1 == -1) {
                // base case 1
                dp[i][k][0] = 0;
                dp[i][k][1] = -prices[i] - fee;
                continue;
            }

            // base case for cooldown
            if (i - cooldown - 1 < 0) {
                // base case 2
                dp[i][k][0] = Math.max(dp[i-1][k][0], dp[i-1][k][1] + prices[i]);
                // don't forget to subtract fee
                dp[i][k][1] = Math.max(dp[i-1][k][1], -prices[i] - fee);
                continue;
            }
            dp[i][k][0] = Math.max(dp[i-1][k][0], dp[i-1][k][1] + prices[i]);
            // consider both cooldown and fee
            dp[i][k][1] = Math.max(dp[i-1][k][1], dp[i-cooldown-1][k-1][0] - prices[i] - fee);     
        }
    return dp[n - 1][max_k][0];
}

// unlimited k, with fee and cooldown
int maxProfit_k_inf(int[] prices, int cooldown, int fee) {
    int n = prices.length;
    int[][] dp = new int[n][2];
    for (int i = 0; i < n; i++) {
        if (i - 1 == -1) {
            // base case 1
            dp[i][0] = 0;
            dp[i][1] = -prices[i] - fee;
            continue;
        }

        // base case for cooldown
        if (i - cooldown - 1 < 0) {
            // base case 2
            dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i]);
            // don't forget to subtract fee
            dp[i][1] = Math.max(dp[i-1][1], -prices[i] - fee);
            continue;
        }
        dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
        // consider both cooldown and fee
        dp[i][1] = Math.max(dp[i - 1][1], dp[i - cooldown - 1][0] - prices[i] - fee);
    }
    return dp[n - 1][0];
}
```

You can use `maxProfit_all_in_one` to solve all 6 previous problems. Without `dp` array optimizations, it's not the most efficient, but correct.

To summarize: this article showed how to solve complex problems via state transitions, demolishing 6 stock-trading problems with one equation. Looking back, not so scary, right?

The key: enumerate every possible "state" and figure out how to enumerate-update them. Generally use a multi-dim `dp` array, start from base cases, build forward to the answer. This is what "dynamic programming" means.

For stocks specifically, we found 3 states — used a 3D array — really just enumerate + update. We can fancily call this "3D DP" — sounds impressive, doesn't it?







<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [Optimal substructure and dp-array traversal direction](https://labuladong.online/algo/dynamic-programming/faq-summary/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems citing this article</strong></summary>

<strong>Install [my Chrome extension](https://labuladong.online/algo/intro/chrome/) and click any problem below to view its solution outline:</strong>

| LeetCode | 力扣 | Difficulty |
| :----: | :----: | :----: |
| - | [剑指 Offer 63. 股票的最大利润](https://leetcode.cn/problems/gu-piao-de-zui-da-li-run-lcof/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**
