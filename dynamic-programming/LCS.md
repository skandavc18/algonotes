# Classic DP: longest common subsequence


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1143. Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/) | [1143. Longest Common Subsequence](https://leetcode.cn/problems/longest-common-subsequence/) | 🟠 |
| [583. Delete Operation for Two Strings](https://leetcode.com/problems/delete-operation-for-two-strings/) | [583. Delete Operation for Two Strings](https://leetcode.cn/problems/delete-operation-for-two-strings/) | 🟠 |
| [712. Minimum ASCII Delete Sum for Two Strings](https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/) | [712. Minimum ASCII Delete Sum for Two Strings](https://leetcode.cn/problems/minimum-ascii-delete-sum-for-two-strings/) | 🟠 |

**-----------**


> [!NOTE]
> Before reading, you should first study:
> 
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)

I'm not sure how others feel about doing algorithm problems, but my distilled trick is: zoom from the big problem down to a single point, study that point first, then expand to the whole problem via recursion or iteration.

For example, in the earlier [Binary tree algorithms part 3](https://labuladong.online/algo/data-structure/binary-tree-part3/), when solving binary-tree problems we narrow the entire problem down to one node, imagine standing on that node and ask what to do, then plug into the binary-tree recursion framework.

DP problems work the same, especially subsequence problems. **This article expands from the longest-common-subsequence problem and summarizes three subsequence problems**, walking through the routine carefully so you can feel this mindset.

## Longest common subsequence

Computing the longest common subsequence (LCS) is a classic DP problem — LeetCode 1143 "Longest Common Subsequence":

Given two strings `s1` and `s2`, find the longest common subsequence and return its length. Function signature:

```java
int longestCommonSubsequence(String s1, String s2);
```

For `s1 = "zabcde", s2 = "acez"`, the longest common subseq is `lcs = "ace"` with length 3, so the algorithm returns 3.

If you've never solved this, the simplest brute force is to enumerate every subseq of `s1` and `s2`, check for common ones, then find the longest among the common ones.

Clearly that complexity is enormous — enumerating all subsequences is exponential. Not practical.

The right idea is to forget the whole strings and zoom in on each character of `s1` and `s2`. The earlier [subsequence template](https://labuladong.online/algo/dynamic-programming/subsequence-problem/) summarized this rule:


**For two-string subsequence problems, use two pointers `i` and `j` moving in the two strings — almost certainly DP**.

LCS follows this rule. Define a `dp` function:

```java
// definition: compute the LCS length of s1[i..] and s2[j..]
int dp(String s1, int i, String s2, int j)
```

By this definition, the answer is `dp(s1, 0, s2, 0)`. The base case: `i == len(s1)` or `j == len(s2)` — then `s1[i..]` or `s2[j..]` is the empty string, and the LCS length is obviously 0:

```java
int longestCommonSubsequence(String s1, String s2) {
    return dp(s1, 0, s2, 0);
}

// definition: compute the LCS length of s1[i..] and s2[j..]
int dp(String s1, int i, String s2, int j) {
    // base case
    if (i == s1.length() || j == s2.length()) {
        return 0;
    }
    // ...
}
```

**Now don't look at `s1` and `s2` as strings; zoom in on individual characters and ask what each character should do**.

![](https://labuladong.online/algo/images/LCS/1.jpeg)

Look at `s1[i]` and `s2[j]`. **If `s1[i] == s2[j]`, this character must be in `lcs`**:

![](https://labuladong.online/algo/images/LCS/2.jpeg)

So we found one character of `lcs`. Refine the code per the `dp` definition:

```java
// definition: compute the LCS length of s1[i..] and s2[j..]
int dp(String s1, int i, String s2, int j) {
    if (s1.charAt(i) == s2.charAt(j)) {
        // s1[i] and s2[j] must be in lcs
        // add the lcs length of s1[i+1..] and s2[j+1..]
        return 1 + dp(s1, i + 1, s2, j + 1);
    } else {
        // ...
    }
}
```

That's `s1[i] == s2[j]`. What if `s1[i] != s2[j]`?

**`s1[i] != s2[j]` means at least one of `s1[i]` and `s2[j]` is not in `lcs`**:

![](https://labuladong.online/algo/images/LCS/3.jpeg)

As shown, three cases are possible. How do we know which?

We don't, so compute all three and take the largest — the problem asks for the "longest" common subseq.

How do we compute the three cases? Recall our `dp` definition — designed precisely for that!

Refine further:

```java
// definition: compute the LCS length of s1[i..] and s2[j..]
int dp(String s1, int i, String s2, int j) {
    if (s1.charAt(i) == s2.charAt(j)) {
        return 1 + dp(s1, i + 1, s2, j + 1);
    } else {
        // at least one of s1[i] and s2[j] is not in lcs
        // enumerate the three cases and take the largest
        return max(
            // case 1: s1[i] not in lcs
            dp(s1, i + 1, s2, j),
            // case 2: s2[j] not in lcs
            dp(s1, i, s2, j + 1),
            // case 3: neither is in lcs
            dp(s1, i + 1, s2, j + 1)
        );
    }
}
```

We're very close to the final answer. **One small optimization: case 3 ("neither `s1[i]` nor `s2[j]` is in lcs") can be ignored**.

Because we're maximizing, case 3 computes the lcs length of `s1[i+1..]` and `s2[j+1..]`, which is at most case 2's lcs length of `s1[i..]` and `s2[j+1..]`, since `s1[i+1..]` is shorter — its lcs can't be longer.

Same for case 1. **Case 3 is contained in cases 1 and 2**, so we ignore it. Final code:

```java
class Solution {
    // memo to eliminate overlapping subproblems
    int[][] memo;

    // main function
    public int longestCommonSubsequence(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        // memo value -1 means not yet computed
        memo = new int[m][n];
        for (int[] row : memo) 
            Arrays.fill(row, -1);
        // compute the lcs length of s1[0..] and s2[0..]
        return dp(s1, 0, s2, 0);
    }

    // definition: compute the LCS length of s1[i..] and s2[j..]
    int dp(String s1, int i, String s2, int j) {
        // base case
        if (i == s1.length() || j == s2.length()) {
            return 0;
        }
        // return the memoized answer if computed before
        if (memo[i][j] != -1) {
            return memo[i][j];
        }
        // choose based on s1[i] and s2[j]
        if (s1.charAt(i) == s2.charAt(j)) {
            // s1[i] and s2[j] must be in lcs
            memo[i][j] = 1 + dp(s1, i + 1, s2, j + 1);
        } else {
            // at least one of s1[i] and s2[j] is not in lcs
            memo[i][j] = Math.max(
                dp(s1, i + 1, s2, j),
                dp(s1, i, s2, j + 1)
            );
        }
        return memo[i][j];
    }
}
```

The above follows the [DP framework routine](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/) and should be intuitive. As for why the `memo` is needed — we've covered this many times — for new readers, briefly: extract our `dp` function's recursion framework:

```java
int dp(int i, int j) {
    dp(i + 1, j + 1); // #1
    dp(i, j + 1);     // #2
    dp(i + 1, j);     // #3
}
```

To get from `dp(i, j)` to `dp(i+1, j+1)`, there are several paths: directly via `#1`, or `#2 -> #3`, or `#3 -> #2`.

That's overlapping subproblems. Without `memo`, `dp(i+1, j+1)` is computed multiple times — unnecessary.

LCS is now solved with top-down memoized DP. We can also use bottom-up iterative DP — same idea, key is the `dp` array definition. Bottom-up code:

```java
class Solution {
    public int longestCommonSubsequence(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        int[][] dp = new int[m + 1][n + 1];
        // definition: dp[i][j] = lcs length of s1[0..i-1] and s2[0..j-1]
        // target: lcs length of s1[0..m-1] and s2[0..n-1], i.e. dp[m][n]
        // base case: dp[0][..] = dp[..][0] = 0

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                // i and j now start at 1, so subtract one
                if (s1.charAt(i - 1) == s2.charAt(j - 1)) {
                    // s1[i-1] and s2[j-1] must be in lcs
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                } else {
                    // at least one of s1[i-1] and s2[j-1] is not in lcs
                    dp[i][j] = Math.max(dp[i][j - 1], dp[i - 1][j]);
                }
            }
        }

        return dp[m][n];
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/longest-common-subsequence/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Code visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>


The bottom-up `dp` array definition differs slightly from the recursive solution but the idea is the same. If you got the recursive version, this one should be easy.

A subtle detail beginners may miss: `s1.charAt(i - 1) == s2.charAt(j - 1)` — string indices and `dp` indices differ by one, called **index offset**. Recall the `dp` definition:

```java
        // definition: dp[i][j] = lcs length of s1[0..i-1] and s2[0..j-1]
```

In the `i`-th loop iteration we update `dp[i]`. By definition we compare the **i-th character** of `s1`, namely `s1[i-1]`. Same for `s2[j-1]`. This kind of detail is very common in string DP — pay attention.

The bottom-up solution can be further optimized via the [DP space-compression technique](https://labuladong.online/algo/dynamic-programming/space-optimization/) to O(N) space. We won't expand on it here.

Now let's see two LCS-like problems.

## Delete operation for two strings

LeetCode 583 "Delete Operation for Two Strings":

Given two words `s1` and `s2`, return the minimum number of deletions to make them equal. Each step deletes one character from either string.

Function signature:

```java
int minDistance(String s1, String s2);
```

For `s1 = "sea" s2 = "eat"`, the algorithm returns 2: first turn `"sea"` into `"ea"`, then `"eat"` into `"ea"`.

The problem asks for the minimum deletions. What do the two strings look like after deletion?

The result is exactly their longest common subsequence!

So the deletion count derives from the LCS length:

```java
int minDistance(String s1, String s2) {
    int m = s1.length(), n = s2.length();
    // reuse the lcs-length function from earlier
    int lcs = longestCommonSubsequence(s1, s2);
    return m - lcs + n - lcs;
}
```

Done!

## Minimum ASCII delete sum

LeetCode 712 "Minimum ASCII Delete Sum for Two Strings": similar to the previous problem, but minimize the ASCII sum of deleted characters.

Function signature:

```java
int minimumDeleteSum(String s1, String s2)
```

For `s1 = "sea", s2 = "eat"`, the algorithm returns 231.

Because deleting `"s"` from `"sea"` and `"t"` from `"eat"` makes them equal, with the minimum deleted-ASCII sum: `s(115) + t(116) = 231`.

**This problem can't directly reuse the LCS function, but we can adapt the approach by tweaking the base case and the state transition to write the solution**:

```java
class Solution {
    // memo
    int memo[][];
    // main function
    public int minimumDeleteSum(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        // memo value -1 means not yet computed
        memo = new int[m][n];
        for (int[] row : memo) 
            Arrays.fill(row, -1);
        
        return dp(s1, 0, s2, 0);
    }

    // definition: delete characters from s1[i..] and s2[j..] to make them equal,
    // minimum sum of ASCII codes is dp(s1, i, s2, j).
    int dp(String s1, int i, String s2, int j) {
        int res = 0;
        // base case
        if (i == s1.length()) {
            // if s1 is exhausted, every remaining char of s2 must be deleted
            for (; j < s2.length(); j++)
                res += s2.charAt(j);
            return res;
        }
        if (j == s2.length()) {
            // if s2 is exhausted, every remaining char of s1 must be deleted
            for (; i < s1.length(); i++)
                res += s1.charAt(i);
            return res;
        }
        
        if (memo[i][j] != -1) {
            return memo[i][j];
        }
        
        if (s1.charAt(i) == s2.charAt(j)) {
            // s1[i] and s2[j] are both in lcs - no deletion needed
            memo[i][j] = dp(s1, i + 1, s2, j + 1);
        } else {
            // at least one of s1[i] and s2[j] is not in lcs - delete one
            memo[i][j] = Math.min(
                s1.charAt(i) + dp(s1, i + 1, s2, j),
                s2.charAt(j) + dp(s1, i, s2, j + 1)
            );
        }
        return memo[i][j];
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/minimum-ascii-delete-sum-for-two-strings/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Code visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>


The base case differs: when computing `lcs` length, an empty string yields lcs length 0; here, an empty string means the other string must be entirely deleted, so we sum up all of its characters' ASCII codes.

For state transitions: when `s1[i] == s2[j]`, no deletion; otherwise, delete one. Use the `dp` function to compute both cases and take the minimum. The rest is similar — won't expand.

So three subseq problems are solved. The key is to zoom in on individual characters and decide whether each pair belongs to the result subseq, avoiding subseq enumeration.

This is the standard approach for two-string subsequence problems — practice well.


<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [DP template for subsequence problems](https://labuladong.online/algo/dynamic-programming/subsequence-problem/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems citing this article</strong></summary>

<strong>Install [my Chrome extension](https://labuladong.online/algo/intro/chrome/) and click any problem below to view its solution outline:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [97. Interleaving String](https://leetcode.com/problems/interleaving-string/?show=1) | [97. Interleaving String](https://leetcode.cn/problems/interleaving-string/?show=1) | 🟠 |
| - | [Sword to Offer II 095. Longest Common Subsequence](https://leetcode.cn/problems/qJnOS7/?show=1) | 🟠 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

