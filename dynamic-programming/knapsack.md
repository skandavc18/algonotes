# Classic DP: 0-1 knapsack


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**


**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)

> Tip: this article has a video version: [0-1 knapsack explained](https://www.bilibili.com/video/BV15B4y1P7X7/). Subscribe to my Bilibili channel — I lead readers through tougher algorithmic techniques in video form.


People keep asking about the knapsack problem in the comments. It's actually not that hard — using the DP framework, it's still just states + choices, nothing special. Today we'll cover the most common variant: 0-1 knapsack. Description:

You have a knapsack of capacity `W` and `N` items, each with a weight and a value. Item `i` has weight `wt[i]` and value `val[i]`. Use this knapsack to hold items, each item used at most once, without exceeding capacity. What's the maximum total value?

![](https://labuladong.online/algo/images/knapsack/1.png)

A simple example:

```py
N = 3, W = 4
wt = [2, 1, 3]
val = [4, 2, 3]
```

The algorithm returns 6: pick the first two items, total weight 3 ≤ W, max value 6.

Simple problem statement, classic DP. The items can't be split — either packed whole or not at all. That's where the "0-1" name comes from.

There's no clever sorting trick — we have to enumerate all possibilities. Following our [DP framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/), just go through the steps.


## Standard DP routine

Looks like every DP article has to repeat the routine. The DP problems in earlier articles all follow the routine below.

**Step 1: identify the "state" and the "choice"**.

State first. How do we describe a problem state? Given a set of items and a knapsack capacity, we have a knapsack problem. **So there are two states: "knapsack capacity" and "items available"**.

Now choices. Easy too: for each item, what can you do? **Choices are "pack into the knapsack" or "don't pack"**.

Once states and choices are clear, the DP problem is essentially solved. For the bottom-up approach, code skeleton is:

```python
for state1 in all values of state1:
    for state2 in all values of state2:
        for ...
            dp[state1][state2][...] = best(choice1, choice2, ...)
```

**Step 2: define the `dp` array**.

We have two states, so we need a 2D `dp` array.

`dp[i][w]` is defined as: considering the first `i` items, with current knapsack capacity `w`, the maximum value we can pack.

For example, if `dp[3][5] = 6`, it means: considering only the first 3 items and capacity 5, the maximum value packable is 6.


> [!NOTE]
> Why this definition? Because it leads to a clean state-transition relation — or rather, this is the canonical definition for knapsack. Memorize it as part of the routine; for future DP problems, try this style of definition.

By this definition, the final answer we want is `dp[N][W]`. Base case: `dp[0][..] = dp[..][0] = 0`, since with no items or no capacity, the max value is 0.

Refined skeleton:

```python
int[][] dp[N+1][W+1]
dp[0][..] = 0
dp[..][0] = 0

for i in [1..N]:
    for w in [1..W]:
        dp[i][w] = max(
            pack item i,
            don't pack item i
        )
return dp[N][W]
```

**Step 3: from the "choice", figure out the state-transition logic**.

Simply put: how do we express "pack item `i`" and "don't pack item `i`" in code?

We must connect this with the `dp` array's definition and see how each choice affects the state:


Restating our `dp` definition:

`dp[i][w]` represents: considering the first `i` items (1-indexed), with capacity `w`, the maximum packable value.

**If you don't pack item `i`**, then clearly `dp[i][w]` equals `dp[i-1][w]` — inheriting the prior result.

**If you pack item `i`**, then `dp[i][w]` equals `val[i-1] + dp[i-1][w - wt[i-1]]`.

Note that array indices are 0-based but our `i` is 1-based, so `val[i-1]` and `wt[i-1]` are item `i`'s value and weight.

If you choose to pack item `i`, you secure value `val[i-1]`. Then, with remaining capacity `w - wt[i-1]`, you choose from the first `i - 1` items to maximize value, namely `dp[i-1][w - wt[i-1]]`.

That covers both choices and gives us the state-transition equation:

```python
for i in [1..N]:
    for w in [1..W]:
        dp[i][w] = max(
            dp[i-1][w],
            dp[i-1][w - wt[i-1]] + val[i-1]
        )
return dp[N][W]
```

**Final step: translate pseudocode into actual code, handle edge cases**.

Here's a Java translation. It also handles `w - wt[i-1]` being negative (which would cause an out-of-bounds access):

```java
int knapsack(int W, int N, int[] wt, int[] val) {
    assert N == wt.length;
    // base case is already initialized
    int[][] dp = new int[N + 1][W + 1];
    for (int i = 1; i <= N; i++) {
        for (int w = 1; w <= W; w++) {
            if (w - wt[i - 1] < 0) {
                // can only choose not to pack
                dp[i][w] = dp[i - 1][w];
            } else {
                // pack or don't pack — take the best
                dp[i][w] = Math.max(
                    dp[i - 1][w - wt[i-1]] + val[i-1], 
                    dp[i - 1][w]
                );
            }
        }
    }
    
    return dp[N][W];
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/mydata-knapsack/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>👾 Code visualization 👾</strong>
</summary>
</details>
</a>
<hr/>


> [!NOTE]
> Note that the `N` parameter in the signature is just `wt.length`, so passing `N` is technically redundant. But to keep the original 0-1 knapsack flavor, I kept it. You can omit it in your own code.

That's it for the knapsack problem. Comparatively, this is one of the simpler DP problems because the state transition flows naturally — once the `dp` array's definition is clear, the transition follows almost automatically.


<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [Sweep-line technique: scheduling meeting rooms](https://labuladong.online/algo/frequency-interview/scan-line-technique/)
 - [Classic DP: subset-sum knapsack](https://labuladong.online/algo/dynamic-programming/knapsack2/)
 - [Classic DP: unbounded knapsack](https://labuladong.online/algo/dynamic-programming/knapsack3/)
 - [Knapsack variant: target sum](https://labuladong.online/algo/dynamic-programming/target-sum/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems citing this article</strong></summary>

<strong>Install [my Chrome extension](https://labuladong.online/algo/intro/chrome/) and click any problem below to view its solution outline:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1235. Maximum Profit in Job Scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/?show=1) | [1235. Maximum Profit in Job Scheduling](https://leetcode.cn/problems/maximum-profit-in-job-scheduling/?show=1) | 🔴 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

