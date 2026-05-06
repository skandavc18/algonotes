# Optimal substructure and dp-array traversal direction


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**


**-----------**


> [!NOTE]
> Before reading, you should first study:
> 
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)

> Tip: this article has a video version: [DP detailed (advanced)](https://www.bilibili.com/video/BV1uv411W73P/). Subscribe to my Bilibili channel — I lead readers through tougher algorithmic techniques in video form.


This article is a revised version of the older [DP FAQ post](https://mp.weixin.qq.com/s/qvlfyKBiXVX7CCwWFR-XKg). Based on continued learning, summarization, and reader feedback, I expanded more content, aiming to make this a comprehensive Q&A follow-up to [DP core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/). Here's the body.

This article will clarify these questions:

1. What does "optimal substructure" actually mean and how is it related to DP?

2. How do you tell whether a problem is a DP problem — i.e. how do you spot overlapping subproblems?

3. Why is the `dp` array often sized `n + 1` instead of `n`?

4. Why do DP solutions traverse the `dp` array in so many ways — sometimes forward, sometimes backward, sometimes diagonally?


## 1. Optimal substructure in detail

"Optimal substructure" is a property of certain problems — not exclusive to DP. Many problems have it, but most don't have overlapping subproblems, so we don't classify them as DP problems.

A simple example first: suppose your school has 10 classes, and you've already computed the highest exam score in each class. Now I ask you for the school's highest score — can you compute it? Yes, and you don't need to re-traverse all student scores; just take the max of those 10 highest scores.

This problem **satisfies optimal substructure**: the optimal of the larger problem can be derived from the optimums of subproblems. Asking for each class's best is the subproblem; once you know all subproblem answers, you can derive the school's best.

So even a simple problem has optimal substructure — it just lacks overlapping subproblems, so we don't need DP for a simple max.

Another example: suppose your school has 10 classes and you know each class's largest score gap (max - min). Now I ask for the school's largest score gap — can you compute it? You can try, but you can't derive it from the 10 classes' gaps. Because those 10 gaps don't necessarily contain the school's gap; e.g. the school's largest gap might be between class 3's max and class 6's min.

This problem **does not satisfy optimal substructure** — you can't derive the school's optimum from each class's optimum, you can't derive the larger problem's optimum from the subproblems'. As [DP framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/) said: for optimal substructure to hold, subproblems must be mutually independent. The school's largest gap can occur between two classes, so subproblems aren't independent — the problem itself doesn't satisfy optimal substructure.

**So when optimal substructure fails, what do we do? Strategy: reformulate the problem**. For the largest-score-gap problem, we couldn't use each class's known gap, so we'd have to write brute-force code:


```java
int result = 0;
for (Student a : school) {
    for (Student b : school) {
        if (a is b) continue;
        result = max(result, |a.score - b.score|);
    }
}
return result;
```


Reformulate, i.e. equivalently transform the problem: largest score gap is just the difference between the highest and lowest scores. So we want highest and lowest scores — that's exactly the first problem we discussed, which has optimal substructure! Now use optimal substructure to solve the max problem first, then come back and solve the gap problem — much more efficient.

Sure, this example is too simple, but think about it: when we do DP, we always seek some optimum. Essentially no different from our example, except we have to deal with overlapping subproblems.

The earlier [Super egg drop problem](https://labuladong.online/algo/dynamic-programming/egg-drop/) showed how to reformulate a problem; different optimal substructures lead to different solutions and efficiencies.

Another simple example: find the maximum value in a binary tree (assume all node values are non-negative for simplicity):

```java
int maxVal(TreeNode root) {
    if (root == null)
        return -1;
    int left = maxVal(root.left);
    int right = maxVal(root.right);
    return max(root.val, left, right);
}
```

This problem also satisfies optimal substructure — the max of the tree rooted at `root` is derivable from the max of its two subtrees (subproblems). Easy to understand combined with the school/class example.

This isn't a DP problem either, but it shows: optimal substructure isn't unique to DP. Most max/min problems satisfy it. **But conversely, optimal substructure as a necessary condition for DP problems must involve seeking an optimum**. So when you encounter nasty optimum problems, reach for DP — that's the routine.

DP works by deriving from a simple base case forward — picture a chain reaction, leveraging small to win big. But only problems with optimal substructure exhibit this chain-reaction property.

Finding optimal substructure is essentially proving the state-transition equation correct; if the equation has it, you can write the brute force; once you have brute force, you can spot overlapping subproblems and optimize. Routine — frequent grinders feel this.

We won't list canonical DP examples here — readers can browse historical articles to see how state transitions follow optimal substructure. Topic over; let's look at other DP confusions.


## 2. How to spot overlapping subproblems at a glance

Readers often say:

After reading the [DP core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/), I know how to step-by-step optimize a DP problem.

After reading the earlier [DP design: mathematical induction](https://labuladong.online/algo/dynamic-programming/longest-increasing-subsequence/), I know how to use induction to write the brute force (state-transition equation).

**But even with the brute force, I find it hard to tell whether overlapping subproblems exist** — so I can't decide whether memoization etc. can optimize it.

I've covered this question several times across DP articles; let me collect a unified summary here.

**First, the simplest brute method is to draw the recursion tree and look for repeated nodes**.

For the simplest example, the Fibonacci recursion tree from [DP core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/):

![](https://labuladong.online/algo/images/dynamic-programming/1.jpg)

Clearly there are repeated nodes, so we use a memo to avoid redundant computation.

Fibonacci is too simple. Real DP problems are more complex — 2D or even 3D DP. You can still draw the tree, but it's painful.

For example, in the [Minimum path sum problem](https://labuladong.online/algo/dynamic-programming/minimum-path-sum/), we wrote this brute force:

```java
int dp(int[][] grid, int i, int j) {
    if (i == 0 && j == 0) {
        return grid[0][0];
    }
    if (i < 0 || j < 0) {
        return Integer.MAX_VALUE;
    }

    return Math.min(
            dp(grid, i - 1, j), 
            dp(grid, i, j - 1)
        ) + grid[i][j];
}
```

You don't need to read the earlier post — just from this function, you see the parameters `i, j` change during recursion, i.e. the "state" is `(i, j)`. Can you tell whether overlapping subproblems exist?

For input `i = 8, j = 7`, the 2D-state recursion tree is below; clearly overlapping subproblems exist:

![](https://labuladong.online/algo/images/optimal/2.jpeg)

**With a moment's thought, you don't need to draw — you can decide directly from the recursion framework**.

Just delete the code details and abstract the recursive framework:


```java
int dp(int[][] grid, int i, int j) {
    dp(grid, i - 1, j), // #1
    dp(grid, i, j - 1)  // #2
}
```


We see `i, j` both decrease. Question: how many paths transition state `(i, j)` to `(i-1, j-1)`?

Two paths: `(i, j) -> #1 -> #2` or `(i, j) -> #2 -> #1`. More than one means `(i-1, j-1)` is computed multiple times — overlapping subproblems exist.

Slightly more complex example: brute force from the earlier [Regex matching problem](https://labuladong.online/algo/dynamic-programming/regular-expression-matching/):

```java
boolean dp(String s, int i, String p, int j) {
    int m = s.length(), n = p.length();
    // base case
    if (j == n) {
        return i == m;
    }
    if (i == m) {
        if ((n - j) % 2 == 1) {
            return false;
        }
        for (; j + 1 < n; j += 2) {
            if (p.charAt(j + 1) != '*') {
                return false;
            }
        }
        return true;
    }

    boolean res = false;

    if (s.charAt(i) == p.charAt(j) || p.charAt(j) == '.') {
        if (j < n - 1 && p.charAt(j + 1) == '*') {
            res = dp(s, i, p, j + 2) || dp(s, i + 1, p, j);
        } else {
            res = dp(s, i + 1, p, j + 1);
        }
    } else {
        if (j < n - 1 && p.charAt(j + 1) == '*') {
            res = dp(s, i, p, j + 2);
        } else {
            res = false;
        }
    }
    
    return res;
}
```

Code is somewhat complex; drawing the tree is annoying. But we don't need to — ignore all detail code and conditionals, abstract the recursive framework:

```java
boolean dp(String s, int i, String p, int j) {
    dp(s, i, p, j + 2);     // #1
    dp(s, i + 1, p, j);     // #2
    dp(s, i + 1, p, j + 1); // #3
}
```

Same as before, the "state" is `(i, j)`. Now: how many paths transition state `(i, j)` to `(i+2, j+2)`?

At least two: `(i, j) -> #1 -> #2 -> #2` and `(i, j) -> #3 -> #3`. So this solution has many overlapping subproblems.

So without drawing, we know this also has overlapping subproblems and needs the memo technique to optimize.

## 3. dp array sizing

For example, in the earlier [Edit distance problem](https://labuladong.online/algo/dynamic-programming/edit-distance/), I first showed the top-down recursive solution with this `dp` function:

```java
class Solution {
    public int minDistance(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        // by the dp function definition, compute the minimum edit distance of s1 and s2
        return dp(s1, m - 1, s2, n - 1);
    }

    // definition: minimum edit distance of s1[0..i] and s2[0..j] is dp(s1, i, s2, j)
    int dp(String s1, int i, String s2, int j) {
        // handle base cases
        if (i == -1) {
            return j + 1;
        }
        if (j == -1) {
            return i + 1;
        }

        // perform the state transition
        if (s1.charAt(i) == s2.charAt(j)) {
            return dp(s1, i - 1, s2, j - 1);
        } else {
            return min(
                dp(s1, i, s2, j - 1) + 1,
                dp(s1, i - 1, s2, j) + 1,
                dp(s1, i - 1, s2, j - 1) + 1
            );
        }
    }

    int min(int a, int b, int c) {
        return Math.min(a, Math.min(b, c));
    }
}
```

Then converted to the bottom-up iterative solution:

```java
class Solution {
    public int minDistance(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        // definition: minimum edit distance of s1[0..i] and s2[0..j] is dp[i+1][j+1]
        int[][] dp = new int[m + 1][n + 1];
        // initialize base cases
        for (int i = 1; i <= m; i++)
            dp[i][0] = i;
        for (int j = 1; j <= n; j++)
            dp[0][j] = j;
        
        // solve bottom-up
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                // perform the state transition
                if (s1.charAt(i-1) == s2.charAt(j-1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = min(
                        dp[i - 1][j] + 1,
                        dp[i][j - 1] + 1,
                        dp[i - 1][j - 1] + 1
                    );
                }
            }
        }
        // by the dp definition, this stores the minimum edit distance of s1 and s2
        return dp[m][n];
    }
}
```

These are the same idea, but readers ask: why is the `dp` array sized `int[m+1][n+1]`? Why is the minimum edit distance of `s1[0..i]` and `s2[0..j]` stored at `dp[i+1][j+1]` with a one-position index offset?

Can we mimic the `dp` function and size the array `int[m][n]`, storing `s1[0..i]` and `s2[0..j]` at `dp[i][j]`?

**In theory, you can define it however you like, as long as base cases are handled accordingly**.

By the `dp` function definition, `dp(s1, i, s2, j)` computes edit distance of `s1[0..i]` and `s2[0..j]`. So `i, j` equal to -1 represents the empty-string base case; the function handles those two cases at the start.

For the `dp` array, of course you can also define `dp[i][j]` as edit distance of `s1[0..i]` and `s2[0..j]`. But how to handle base cases? Can the index be -1?

So we size the `dp` array as `int[m+1][n+1]`, shifting indices by one and reserving index 0 as the base case representing the empty string. Then define `dp[i+1][j+1]` to store the edit distance of `s1[0..i]` and `s2[0..j]`.

## 4. dp array traversal direction

I'm sure readers worry about traversal order when doing DP. Take 2D `dp` arrays. Sometimes we go forward:


```java
int[][] dp = new int[m][n];
for (int i = 0; i < m; i++)
    for (int j = 0; j < n; j++)
        // compute dp[i][j]
```


Sometimes backward:


```java
for (int i = m - 1; i >= 0; i--)
    for (int j = n - 1; j >= 0; j--)
        // compute dp[i][j]
```


Sometimes diagonally:


```java
// traverse the array diagonally
for (int l = 2; l <= n; l++) {
    for (int i = 0; i <= n - l; i++) {
        int j = l + i - 1;
        // compute dp[i][j]
    }
}
```


More confusingly, sometimes both forward and backward yield correct answers — e.g. in the [Stock-buy-sell summary](https://labuladong.online/algo/dynamic-programming/stock-problem-summary/), some places are bidirectional.

If you observe carefully, the reason: just hold to two principles:

**1. During traversal, the states we need must already be computed**.

**2. After traversal ends, the position storing the result must be computed**.

Let me explain these in concrete terms.

Take the classic [Edit distance](https://labuladong.online/algo/dynamic-programming/edit-distance/). By the `dp` definition, the base cases are `dp[..][0]` and `dp[0][..]`, the final answer is `dp[m][n]`. By the state transition, `dp[i][j]` comes from `dp[i-1][j]`, `dp[i][j-1]`, `dp[i-1][j-1]`, as below:

![](https://labuladong.online/algo/images/optimal/1.jpg)

Per the two principles, how should you traverse `dp`? Forward:


```java
for (int i = 1; i < m; i++)
    for (int j = 1; j < n; j++)
        // via dp[i-1][j], dp[i][j - 1], dp[i-1][j-1]
        // compute dp[i][j]
```


Because each iteration's left, top, and top-left positions are either base cases or already computed, and we end at our desired `dp[m][n]`.

Another example: palindromic subsequence — see the earlier [Subsequence template](https://labuladong.online/algo/dynamic-programming/subsequence-problem/). By the `dp` definition, base cases are on the middle diagonal, `dp[i][j]` comes from `dp[i+1][j]`, `dp[i][j-1]`, `dp[i+1][j-1]`, and we want `dp[0][n-1]`:

![](https://labuladong.online/algo/images/lps/4.jpg)

By the two principles, two correct traversals work:

![](https://labuladong.online/algo/images/lps/5.jpg)

Either traverse diagonally from upper-left to lower-right, or bottom-up left-to-right — only then can we ensure `dp[i][j]`'s left, bottom, lower-left neighbors are already computed.

Now you should understand the two principles. Just look at the base case and the result-storage position — ensure the data used during traversal are computed. Sometimes multiple methods yield the right answer; pick what suits your taste.


<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [One method demolishes the LeetCode stock buy/sell series](https://labuladong.online/algo/dynamic-programming/stock-problem-summary/)
 - [DP template for subsequence problems](https://labuladong.online/algo/dynamic-programming/subsequence-problem/)
 - [Dynamic programming problem-solving framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)
 - [Classic DP: game theory](https://labuladong.online/algo/dynamic-programming/game-theory/)
 - [Classic DP: burst balloons](https://labuladong.online/algo/dynamic-programming/burst-balloons/)
 - [Classic DP: regular expression matching](https://labuladong.online/algo/dynamic-programming/regular-expression-matching/)
 - [Classic DP: edit distance](https://labuladong.online/algo/dynamic-programming/edit-distance/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems citing this article</strong></summary>

<strong>Install [my Chrome extension](https://labuladong.online/algo/intro/chrome/) and click any problem below to view its solution outline:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [115. Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/?show=1) | [115. Distinct Subsequences](https://leetcode.cn/problems/distinct-subsequences/?show=1) | 🔴 |
| [139. Word Break](https://leetcode.com/problems/word-break/?show=1) | [139. Word Break](https://leetcode.cn/problems/word-break/?show=1) | 🟠 |
| [221. Maximal Square](https://leetcode.com/problems/maximal-square/?show=1) | [221. Maximal Square](https://leetcode.cn/problems/maximal-square/?show=1) | 🟠 |
| [256. Paint House](https://leetcode.com/problems/paint-house/?show=1)🔒 | [256. Paint House](https://leetcode.cn/problems/paint-house/?show=1)🔒 | 🟠 |
| [343. Integer Break](https://leetcode.com/problems/integer-break/?show=1) | [343. Integer Break](https://leetcode.cn/problems/integer-break/?show=1) | 🟠 |
| [576. Out of Boundary Paths](https://leetcode.com/problems/out-of-boundary-paths/?show=1) | [576. Out of Boundary Paths](https://leetcode.cn/problems/out-of-boundary-paths/?show=1) | 🟠 |
| [63. Unique Paths II](https://leetcode.com/problems/unique-paths-ii/?show=1) | [63. Unique Paths II](https://leetcode.cn/problems/unique-paths-ii/?show=1) | 🟠 |
| [91. Decode Ways](https://leetcode.com/problems/decode-ways/?show=1) | [91. Decode Ways](https://leetcode.cn/problems/decode-ways/?show=1) | 🟠 |
| - | [Sword to Offer II 091. Paint House](https://leetcode.cn/problems/JEj789/?show=1) | 🟠 |
| - | [Sword to Offer II 097. Number of Distinct Subsequences](https://leetcode.cn/problems/21dk04/?show=1) | 🔴 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

