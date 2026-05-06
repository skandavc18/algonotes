# DP template for subsequence problems


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1312. Minimum Insertion Steps to Make a String Palindrome](https://leetcode.com/problems/minimum-insertion-steps-to-make-a-string-palindrome/) | [1312. Minimum Insertion Steps to Make a String Palindrome](https://leetcode.cn/problems/minimum-insertion-steps-to-make-a-string-palindrome/) | 🔴 |
| [516. Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/) | [516. Longest Palindromic Subsequence](https://leetcode.cn/problems/longest-palindromic-subsequence/) | 🟠 |

**-----------**


> [!NOTE]
> Before reading, you should first study:
> 
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)

Subsequence problems are common but not easy to solve.

First, subsequence problems are inherently harder than substring or subarray problems, because subsequences are non-contiguous while substrings/subarrays are contiguous — even brute-forcing isn't trivial, let alone solving algorithmic questions.

Plus, subsequence problems often involve two strings (e.g. the earlier [Longest common subsequence](https://labuladong.online/algo/dynamic-programming/longest-common-subsequence/)). Without experience, they're really tough. So this article digs into the patterns of subsequence problems — there are essentially two templates, and as long as you think along these lines, you've nearly always got it.

Generally these problems ask you to find a **longest subsequence** — the shortest is just one character, nothing to ask. Once subsequences and optimization are involved, it's almost certainly **DP, with O(n^2) time complexity**.

Reason is simple: how many possible subsequences does a string have? At least exponential. Without DP, what else would you do?

Since we're using DP, we need to define the `dp` array and find the state transition. The two "thought templates" are essentially `dp` array definitions. Different problems may need different definitions.

## 1. Two thought templates


**1. The first template uses a 1D `dp` array**:

```java
int n = array.length;
int[] dp = new int[n];

for (int i = 1; i < n; i++) {
    for (int j = 0; j < i; j++) {
        dp[i] = best(dp[i], dp[j] + ...)
    }
}
```

For example, the [longest increasing subsequence](https://labuladong.online/algo/dynamic-programming/longest-increasing-subsequence/) and [maximum subarray sum](https://labuladong.online/algo/dynamic-programming/maximum-subarray/) we've covered both use this template.

In this template, `dp` is defined as:

**Within subarray `arr[0..i]`, the length of the subsequence ending at `arr[i]` is `dp[i]`**.

Why does longest-increasing-subsequence need this approach? As that article explains, this fits induction and yields the state transition. We won't repeat it here.

**2. The second template uses a 2D `dp` array**:

```java
int n = arr.length;
int[][] dp = new dp[n][n];

for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
        if (arr[i] == arr[j]) 
            dp[i][j] = dp[i][j] + ...
        else
            dp[i][j] = best(...)
    }
}
```

This is more common, especially when subsequences of two strings/arrays are involved — like the earlier [Longest common subsequence](https://labuladong.online/algo/dynamic-programming/longest-common-subsequence/) and [Edit distance](https://labuladong.online/algo/dynamic-programming/edit-distance/). It also applies when there's only one string/array, like the palindromic-subsequence problem here.

**2.1 Two strings/arrays scenario**, `dp` is defined as:

**Within subarray `arr1[0..i]` and subarray `arr2[0..j]`, the desired subsequence length is `dp[i][j]`**.

**2.2 Single string/array scenario**, `dp` is defined as:

**Within subarray `array[i..j]`, the desired subsequence length is `dp[i][j]`**.

Now let's look at the longest-palindromic-subsequence problem to walk through the second template in detail.

## 2. Longest palindromic subsequence

We solved [longest palindromic substring](https://labuladong.online/algo/essential-technique/array-two-pointers-summary/) earlier. This time, harder: LeetCode 516 "Longest Palindromic Subsequence" — find the length of the longest palindromic subsequence:

Given a string `s`, find the length of the longest palindromic subsequence. Function signature:

```java
int longestPalindromeSubseq(String s);
```

For example, with `s = "aecda"`, the algorithm returns 3, since the longest palindromic subsequence is `"aca"` of length 3.

Our `dp` definition is: **within substring `s[i..j]`, the length of the longest palindromic subsequence is `dp[i][j]`**. Remember this definition to follow the algorithm.

Why this 2D `dp` definition for this problem? In [longest increasing subsequence](https://labuladong.online/algo/dynamic-programming/longest-increasing-subsequence/) I noted that finding state transitions needs inductive thinking — i.e. how to derive the unknown from the known. This definition supports induction and reveals the state-transition relation.

Concretely, to compute `dp[i][j]`, suppose you know the subproblem `dp[i+1][j-1]` (the longest palindromic subseq length of `s[i+1..j-1]`). Can you compute `dp[i][j]` (the longest palindromic subseq length of `s[i..j]`)?

![](https://labuladong.online/algo/images/lps/1.jpg)

Yes! It depends on `s[i]` and `s[j]`:

**If they're equal**, then they plus the longest palindromic subseq of `s[i+1..j-1]` form the longest palindromic subseq of `s[i..j]`:

![](https://labuladong.online/algo/images/lps/2.jpg)

**If they're not equal**, they **can't both** be in the longest palindromic subseq of `s[i..j]`. So add each one separately to `s[i+1..j-1]` and see which yields a longer palindromic subseq:

![](https://labuladong.online/algo/images/lps/3.jpg)

In code:


```java
if (s[i] == s[j])
    // they must be in the longest palindromic subseq
    dp[i][j] = dp[i + 1][j - 1] + 2;
else
    // which yields a longer palindromic subseq, s[i+1..j] or s[i..j-1]?
    dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]);
```


That's the state-transition equation. By the dp definition, we want `dp[0][n - 1]` — the length of the longest palindromic subseq of the entire `s`.

## 3. Implementation

First clarify the base case: with one character, the longest palindromic subseq length is 1, i.e. `dp[i][j] = 1 (i == j)`.

Since `i` is at most `j`, positions with `i > j` don't represent any valid subsequence — initialize them to 0.

Also, looking at the state-transition equation, computing `dp[i][j]` needs `dp[i+1][j-1]`, `dp[i+1][j]`, `dp[i][j-1]`. After filling in the base case, the `dp` array looks like:

![](https://labuladong.online/algo/images/lps/4.jpg)

**To ensure the lower-left and right neighbors are already computed when calculating `dp[i][j]`, we can only iterate diagonally or in reverse**:

![](https://labuladong.online/algo/images/lps/5.jpg)

> [!TIP]
> For details on `dp` array traversal direction, see [DP FAQ](https://labuladong.online/algo/dynamic-programming/faq-summary/).

I choose reverse iteration:

```java
class Solution {
    public int longestPalindromeSubseq(String s) {
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
        // length of the longest palindromic substring of the whole s
        return dp[0][n - 1];
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/longest-palindromic-subsequence/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🥳 Code visualization 🥳</strong>
</summary>
</details>
</a>
<hr/>


That's the longest palindromic subseq problem solved.

## 4. Extension

Palindrome problems don't have especially broad applications, but once you can compute longest palindromic subseq, similar problems become easy.

For example, LeetCode 1312 "Minimum Insertion Steps to Make a String Palindrome":

Given a string `s`, you can insert any character anywhere. To turn `s` into a palindrome, what's the minimum number of insertions?

Function signature:

```java
int minInsertions(String s);
```

For `s = "abcea"`, the algorithm returns 2 — insert 2 characters to form `"abeceba"` or `"aebcbea"`. For `s = "aba"`, returns 0 — already a palindrome, no insertions needed.

This is also a single-string subseq problem, so we use a 2D `dp` array, where `dp[i][j]` is defined as:

**For string `s[i..j]`, `dp[i][j]` insertions are needed (minimum) to make it a palindrome**.

Per the `dp` definition, base case is `dp[i][i] = 0` — single characters are already palindromes, no insertions needed.

Then, by induction, suppose we've computed `dp[i+1][j-1]` — how do we derive `dp[i][j]`?

![](https://labuladong.online/algo/images/palindrome-insert/1.jpeg)

Very similar to longest palindromic subseq. Two cases:


```java
if (s[i] == s[j]) {
    // no insertion needed
    dp[i][j] = dp[i + 1][j - 1];
} else {
    // make either s[i+1..j] or s[i..j-1] a palindrome (whichever needs fewer insertions),
    // then insert one s[i] or s[j] to make s[i..j] a palindrome
    dp[i][j] = min(dp[i + 1][j], dp[i][j - 1]) + 1;
}
```


Finally, again iterate the `dp` array in reverse:

```java
class Solution {
    public int minInsertions(String s) {
        int n = s.length();
        // dp[i][j] = minimum insertions to make s[i..j] a palindrome
        // initialize the entire dp array to 0
        int[][] dp = new int[n][n];
        // iterate in reverse to ensure correct state transitions
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                // state-transition equation
                if (s.charAt(i) == s.charAt(j)) {
                    dp[i][j] = dp[i + 1][j - 1];
                } else {
                    dp[i][j] = Math.min(dp[i + 1][j], dp[i][j - 1]) + 1;
                }
            }
        }
        // minimum insertions for the entire s
        return dp[0][n - 1];
    }
}
```

That's it — the subseq template solves this too. The logic is very similar to longest palindromic subseq. So can we directly reuse the palindromic subseq solution?

Actually yes, and we don't even need to write the state-transition equation. Think about it:

**Compute the longest palindromic subseq of `s` first; the characters not in the longest palindromic subseq are exactly the characters we need to insert**.

So this problem can directly reuse the `longestPalindromeSubseq` function:

```java
class Solution {
    // compute the minimum insertions to make s a palindrome
    public int minInsertions(String s) {
        return s.length() - longestPalindromeSubseq(s);
    }

    // compute the longest palindromic subseq length of s
    int longestPalindromeSubseq(String s) {
        // see above
    }
}
```

That's it for subseq-related algorithms. Hope it inspires you.


<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [DP design: longest increasing subsequence](https://labuladong.online/algo/dynamic-programming/longest-increasing-subsequence/)
 - [Dimensional reduction on dynamic programming](https://labuladong.online/algo/dynamic-programming/space-optimization/)
 - [Optimal substructure and dp-array traversal direction](https://labuladong.online/algo/dynamic-programming/faq-summary/)
 - [Classic DP: longest common subsequence](https://labuladong.online/algo/dynamic-programming/longest-common-subsequence/)

</details><hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

