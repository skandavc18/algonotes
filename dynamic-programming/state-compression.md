# Dimensional reduction on dynamic programming



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**



**-----------**



> [!NOTE]
> Before reading, you should first study:
> 
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)

> [!NOTE]
> Space compression isn't hard — it's mainly used to optimize the space complexity of certain DP problems. Most coding interviews don't have tight space requirements, so you can usually pass without it. Personally, I think state compression isn't a must-learn technique. Interested readers can study it carefully.

We've written over a dozen DP articles. DP techniques produce huge efficiency gains, generally turning exponential or factorial time complexity into O(N^2). It's the algorithm world's "two-dimensional foil" — flattening all manner of monsters into 2D.

But DP solutions can also be optimized further. If you carefully observe certain DP problems' state-transition equations, you can lower their space complexity from O(N^2) to O(N).







> [!NOTE]
> Earlier, I incorrectly used the term "state compression" in this article. A reader pointed out that "state compression" actually means representing multiple states with a single integer via bitwise operations, thereby reducing `dp` array dimensionality. The optimization described here observes the dependencies of the state-transition equation to reduce the `dp` array's dimensions — different from "state compression". For accuracy, I've replaced "state compression" with "space compression" in this article to avoid term misuse.

DP problems that can use space compression are 2D `dp` problems. **Look at the state-transition equation: if computing state `dp[i][j]` only needs neighbors of `dp[i][j]`, then space compression applies** — converting the 2D `dp` array into 1D, reducing space from O(N^2) to O(N).

What does "neighbor of `dp[i][j]`" mean? Take the earlier post [Longest palindromic subsequence](https://labuladong.online/algo/dynamic-programming/subsequence-problem/), where the final code is:

```java
int longestPalindromeSubseq(String s) {
    int n = s.length();
    // initialize the entire dp array to 0
    int[][] dp = new int[n][n];
    // base case
    for (int i = 0; i < n; i++) {
        dp[i][i] = 1;
    }
    // iterate in reverse to ensure correct state transitions
    for (int i = n - 1; i >= 0; i--) {
        for (int j = i + 1; j < n; j++) {
            // state-transition equation
            if (s.charAt(i) == s.charAt(j)) {
                dp[i][j] = dp[i + 1][j - 1] + 2;
            } else {
                dp[i][j] = Math.max(dp[i + 1][j], dp[i][j - 1]);
            }
        }
    }
    // length of the longest palindromic subsequence of the whole s
    return dp[0][n - 1];
}
```

> [!TIP]
> This article doesn't discuss how to derive state-transition equations — only how to apply space compression to a 2D DP problem. The technique is general, so even if you haven't read the previous article and don't follow the code's logic, you can still learn space compression.

The update of `dp[i][j]` only depends on three states: `dp[i+1][j-1]`, `dp[i][j-1]`, `dp[i+1][j]`:

![](https://labuladong.online/algo/images/space-optimal/1.jpeg)

These are the "neighbors" of `dp[i][j]`. Since computing `dp[i][j]` only needs these three neighbors, we don't really need such a large 2D dp table. **The core idea of space compression: "project" the 2D array onto a 1D array**:

![](https://labuladong.online/algo/images/space-optimal/2.jpeg)

The word "project" should be intuitive — we want a 1D array to play the role of the original 2D array.

The idea is direct, but there's an obvious problem: in the figure, `dp[i][j-1]` and `dp[i+1][j-1]` are in the same column. A 1D array can only hold one of them; projected to 1D, one will overwrite the other — so how do we still compute `dp[i][j]`?

That's the crux of space compression. Let's analyze and solve this. Stick with the longest-palindromic-subsequence problem; its state-transition logic is essentially:





```java
for (int i = n - 2; i >= 0; i--) {
    for (int j = i + 1; j < n; j++) {
        // state-transition equation
        if (s.charAt(i) == s.charAt(j)) {
            dp[i][j] = dp[i + 1][j - 1] + 2;
        } else {
            dp[i][j] = Math.max(dp[i + 1][j], dp[i][j - 1]);
        }
    }
}
```



Recall the earlier figure: "projection" turns multiple rows into one. So compressing 2D `dp` into 1D usually drops the first dimension `i`, leaving only `j`. **The compressed 1D `dp` array is the row `dp[i][..]` of the previous 2D `dp` array**.

First, transform the code by mindlessly dropping `i` and turning `dp` into 1D:





```java
for (int i = n - 2; i >= 0; i--) {
    for (int j = i + 1; j < n; j++) {
        // What do the entries in this 1D dp array represent?
        if (s.charAt(i) == s.charAt(j)) {
            dp[j] = dp[j - 1] + 2;
        } else {
            dp[j] = Math.max(dp[j], dp[j - 1]);
        }
    }
}
```



The 1D `dp` array can only represent one row `dp[i][..]` of the 2D `dp` array. But we want `dp[i+1][j-1]`, `dp[i][j-1]`, `dp[i+1][j]` for the state transition.

So consider two questions:

1. Before assigning a new value to `dp[j]`, what 2D `dp` entry does `dp[j]` correspond to?

2. What 2D `dp` entry does `dp[j-1]` correspond to?

**For question 1, before reassigning `dp[j]`, its value is whatever the previous outer-loop iteration computed — i.e. `dp[i+1][j]` in the 2D array**.

**For question 2, `dp[j-1]` is the value computed by the previous inner-loop iteration — i.e. `dp[i][j-1]` in the 2D array**.

That's most of the problem solved. Only `dp[i+1][j-1]` from the 2D array can't be obtained directly from the 1D array:





```java
for (int i = n - 2; i >= 0; i--) {
    for (int j = i + 1; j < n; j++) {
        if (s.charAt(i) == s.charAt(j)) {
            // dp[i][j] = dp[i+1][j-1] + 2;
            dp[j] = ?? + 2;
        } else {
            // dp[i][j] = max(dp[i+1][j], dp[i][j-1]);
            dp[j] = Math.max(dp[j], dp[j - 1]);
        }
    }
}
```



Because the for loops iterate `i` and `j` from left to right, bottom to top, when we update the 1D `dp` array, `dp[i+1][j-1]` will be overwritten by `dp[i][j-1]`. The figure shows the order in which these four positions are visited:

![](https://labuladong.online/algo/images/space-optimal/3.jpeg)

**So to access `dp[i+1][j-1]`, we must save it in a temporary variable `temp` before it's overwritten, and keep that value until we compute `dp[i][j]`**. Combined with the figure, here's the code:





```java
for (int i = n - 2; i >= 0; i--) {
    // variable storing dp[i+1][j-1]
    int pre = 0;
    for (int j = i + 1; j < n; j++) {
        int temp = dp[j];
        if (s.charAt(i) == s.charAt(j)) {
            // dp[i][j] = dp[i+1][j-1] + 2;
            dp[j] = pre + 2;
        } else {
            dp[j] = Math.max(dp[j], dp[j - 1]);
        }
        // by next loop iteration, pre will be dp[i+1][j-1]
        pre = temp;
    }
}
```



Don't underestimate this code — it's the most elegant part of 1D `dp`: easy if you know it, impossible if you don't. For clarity, let's trace this with concrete numbers:

Suppose `i = 5, j = 7` and `s[5] == s[7]`. We enter this branch:





```java
for (int i = 5; i--) {
    for (int j = 7; j++) {
        if (s[5] == s[7]) {
            // dp[5][7] = dp[i+1][j-1] + 2;
            dp[7] = pre + 2;
        }
    }
}
```



What is `pre`? It's the `temp` from the previous inner-loop iteration.

What's that **previous inner-loop iteration's `temp`**? It's `dp[j-1]`, i.e. `dp[6]` — but note this is the **previous outer-loop iteration's** `dp[6]`, not the current `dp[6]`.

Map this back to the 2D array. The current `dp[6]` is `dp[i][6] = dp[5][6]` in the 2D `dp` array. That `temp` is `dp[i+1][6] = dp[6][6]` in the 2D `dp`.

So `pre` is `dp[i+1][j-1] = dp[6][6]` — exactly what we want.

Now we've successfully reduced the state-transition equation in dimension — the toughest bone is chewed. But there's still the base case to handle:

```java
// initialize the entire dp array to 0
int[][] dp = new int[n][n];
// base case
for (int i = 0; i < n; i++) {
    dp[i][i] = 1;
}
```

How do we flatten the base case? Easy — remember, space compression is projection. Let's project the base case to 1D:

![](https://labuladong.online/algo/images/space-optimal/4.jpeg)

The base case in the 2D `dp` array all map to the 1D `dp` array with no conflicts or overwrites. So we can just write:

```java
// base case: initialize the entire 1D dp array to 1
int[] dp = new int[n];
Arrays.fill(dp, 1);
```

So far we've reduced the dimension of both base case and state transition. We've actually written the complete code:

```java
class Solution {
    public int longestPalindromeSubseq(String s) {
        int n = s.length();
        // base case: initialize the entire 1D dp array to 1
        int[] dp = new int[n];
        Arrays.fill(dp, 1);

        for (int i = n - 2; i >= 0; i--) {
            int pre = 0;
            for (int j = i + 1; j < n; j++) {
                int temp = dp[j];
                // state-transition equation
                if (s.charAt(i) == s.charAt(j))
                    dp[j] = pre + 2;
                else
                    dp[j] = Math.max(dp[j], dp[j - 1]);
                pre = temp;
            }
        }
        return dp[n - 1];
    }
}
```

That's the end of this article. As powerful as space compression is, it's still built on top of standard DP thinking.

You can see that after applying space compression to a 2D `dp` array, the code's readability drops significantly. Anyone reading this kind of solution out of context would be baffled. Algorithm optimization is a process: first write a readable brute-force recursive algorithm, then apply DP to eliminate overlapping subproblems, then try space compression for space.

In other words, you should at least be fluent with the [DP framework routine](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/) to find state-transition equations and write a correct DP solution. Only then can you observe the state transition and decide whether space compression applies.

I hope readers progress steadily, layer by layer. For these extreme optimizations, it's fine to skip them. As long as the patterns are in your head, you can travel anywhere fearlessly!







<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [One method demolishes the LeetCode stock buy/sell series](https://labuladong.online/algo/dynamic-programming/stock-problem-summary/)
 - [DP: minimum path sum](https://labuladong.online/algo/dynamic-programming/minimum-path-sum/)
 - [Dynamic programming problem-solving framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)
 - [DP design: maximum subarray](https://labuladong.online/algo/dynamic-programming/maximum-subarray/)
 - [The framework mindset for learning data structures and algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Classic DP: subset-sum knapsack](https://labuladong.online/algo/dynamic-programming/knapsack2/)
 - [Classic DP: unbounded knapsack](https://labuladong.online/algo/dynamic-programming/knapsack3/)
 - [Classic DP: longest common subsequence](https://labuladong.online/algo/dynamic-programming/longest-common-subsequence/)
 - [Classic DP: super-egg-drop](https://labuladong.online/algo/dynamic-programming/egg-drop/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems citing this article</strong></summary>

<strong>Install [my Chrome extension](https://labuladong.online/algo/intro/chrome/) and click any problem below to view its solution outline:</strong>

| LeetCode | 力扣 | Difficulty |
| :----: | :----: | :----: |
| [63. Unique Paths II](https://leetcode.com/problems/unique-paths-ii/?show=1) | [63. 不同路径 II](https://leetcode.cn/problems/unique-paths-ii/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**

