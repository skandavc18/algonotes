# Classic DP: regular expression matching



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | 力扣 | Difficulty |
| :----: | :----: | :----: |
| [10. Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/) | [10. regex表达式匹配](https://leetcode.cn/problems/regular-expression-matching/) | 🔴 |

**-----------**



> [!NOTE]
> Before reading, you should first study:
> 
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)
> - [Classic DP: edit distance](https://labuladong.online/algo/dynamic-programming/edit-distance/)

Regular expressions are an extremely powerful tool. This article looks at how a regex implementation actually works under the hood. LeetCode 10 "Regular Expression Matching" asks us to implement a simple regex matcher with the `.` and `*` wildcards.

These two are the most common: `.` matches any single character; `*` lets the previous character repeat any number of times (including 0).

For example, the pattern `".a*b"` matches `"zaaab"` and also `"cb"`; the pattern `"a..b"` matches `"amnb"`; the pattern `".*"` is special — it matches any text.

The problem gives us two strings `s` and `p`: `s` is the text, `p` is the pattern. Decide whether the pattern `p` matches the text `s`. We can assume the pattern only contains lowercase letters and the two wildcards above, and is well-formed (no `*a` or `b**`).

Function signature:

```java
boolean isMatch(string s, string p);
```

What's the hard part of implementing this regex?

The `.` wildcard is easy: any character in `s` against `.` matches blindly. The hard one is `*`: the previous character can repeat once, multiple times, or not appear at all. How do we deal with that?

Answer is simple: brute-force every possibility — as long as one yields a complete match, `p` matches `s`. Whenever brute force over two strings appears, you should reflexively think DP.

## 1. Approach analysis

Imagine the matching process: two pointers `i, j` move in `s` and `p`. If both reach the end of their strings, the match succeeds; otherwise it fails.

**Without `*`, facing two characters `s[i]` and `p[j]` to match, we just check if they match**:

```java
boolean isMatch(String s, String p) {
    int i = 0, j = 0;
    while (i < s.length() && j < p.length()) {
        // the «.» wildcard is the universal joker
        if (s.charAt(i) == p.charAt(j) || p.charAt(j) == '.') {
            // matched, continue with s[i+1..] and p[j+1..]
            i++; j++;
        } else {
            // not matched
            return false;
        }
    }
    return i == j;
}
```

Adding `*` makes it more complex, but case-by-case analysis isn't bad.

**When `p[j + 1]` is `*`, case analysis**:

1. If `s[i] == p[j]`, two cases:

1.1 `p[j]` may match multiple characters. E.g. `s = "aaa", p = "a*"`, then `p[0]` via `*` matches three `"a"`s.

1.2 `p[j]` may match 0 characters. E.g. `s = "aa", p = "a*aa"`, the trailing characters can match `s`, so `p[0]` matches 0 times.

2. If `s[i] != p[j]`, only one case:

`p[j]` matches 0 times, then we check whether the next character matches `s[i]`. E.g. `s = "aa", p = "b*aa"` — here `p[0]` must match 0 times.

So we modify the prior code to handle `*`:

```java
if (s.charAt(i) == p.charAt(j) || p.charAt(j) == '.') {
    // matched
    if (j < p.length() - 1 && p.charAt(j + 1) == '*') {
        // there's a *, can match 0 or more times
    } else {
        // no *, just match once
        i++; j++;
    }
} else {
    // not matched
    if (j < p.length() - 1 && p.charAt(j + 1) == '*') {
        // there's a *, can only match 0 times
    } else {
        // no *, can't proceed
        return false;
    }
}
```

The overall idea is clear, but the question is: when we hit `*`, do we match 0 or many times? How many?

This is a "choice" problem — we need to enumerate every possible choice. The core of DP is "states" and "choices": **states are `i` and `j`; choices are how many characters `p[j]` matches**.

## 2. DP solution

By the "states", define a `dp` function:





```java
boolean dp(String s, int i, String p, int j);
```



The `dp` function is defined as:

**If `dp(s, i, p, j) = true`, then `s[i..]` can be matched by `p[j..]`; otherwise it cannot**.

The answer we want is `dp(s, 0, p, 0)`, so:

```java
boolean isMatch(String s, String p) {
    // pointers i and j start at index 0
    return dp(s, 0, p, 0);
}
```

Based on the earlier code, the main `dp` logic:

```java
// dp function core logic (pseudo-code)
// definition: returns whether s[i..] can be matched by p[j..]
boolean dp(String s, int i, String p, int j) {
    if (s.charAt(i) == p.charAt(j) || p.charAt(j) == '.') {
        // matched
        if (j < p.length() - 1 && p.charAt(j + 1) == '*') {
            // 1.1 wildcard matches 0 or more times
            return dp(s, i, p, j + 2) || dp(s, i + 1, p, j);
        } else {
            // 1.2 normal match, once
            return dp(s, i + 1, p, j + 1);
        }
    } else {
        // not matched
        if (j < p.length() - 1 && p.charAt(j + 1) == '*') {
            // 2.1 wildcard matches 0 times
            return dp(s, i, p, j + 2);
        } else {
            // 2.2 cannot continue matching
            return false;
        }
    }
}
```

**Per the `dp` definition**, these cases are easy to explain:

**1.1 Wildcard matches 0 or more times**

Adding 2 to `j`, leaving `i`, means skip `p[j]` and the wildcard — i.e. wildcard matches 0 times.

Even if `s[i] == p[j]`, this can still happen, as below:

![](https://labuladong.online/algo/images/regex/5.jpeg)

Adding 1 to `i`, leaving `j`, means `p[j]` matched `s[i]` but can keep matching — i.e. matching multiple times:

![](https://labuladong.online/algo/images/regex/2.jpeg)

Either case suffices — OR them together.

**1.2 Normal single match**

This branch has no `*`. If `s[i] == p[j]`, increment both `i` and `j`:

![](https://labuladong.online/algo/images/regex/3.jpeg)

**2.1 Wildcard matches 0 times**

Like 1.1, add 2 to `j` and leave `i`:

![](https://labuladong.online/algo/images/regex/1.jpeg)

**2.2 Without `*` and characters don't match — match fails**

![](https://labuladong.online/algo/images/regex/4.jpeg)

Easy with the figures. Now think about the `dp` base case:

**One base case is `j == p.length()`**: by the definition, the pattern `p` is exhausted; check whether `s` is also exhausted — if so, success:





```java
if (j == p.length()) {
    return i == s.length();
}
```



**Another base case is `i == s.length()`**: per the definition, the text `s` is exhausted; do we just check `j == p.length()`?





```java
if (i == s.length()) {
    // is this right?
    return j == p.length();
}
```



**This is wrong. We can't just check whether `j == p.length()` — as long as `p[j..]` can match the empty string, it's a match**. For example, `s = "a", p = "ab*c*"`: when `i` reaches the end of `s`, `j` hasn't reached the end of `p`, but `p` still matches `s`.

So we write:

```java
int m = s.length(), n = p.length();

if (i == s.length()) {
    // matching the empty string requires character-* pairs
    if ((n - j) % 2 == 1) {
        return false;
    }
    // check whether the remaining is in the form x*y*z*
    for (; j + 1 < p.length(); j += 2) {
        if (p.charAt(j + 1) != '*') {
            return false;
        }
    }
    return true;
}
```

With this, the complete code:

```java
class Solution {
    // memo
    int[][] memo;

    public boolean isMatch(String s, String p) {
        int m = s.length(), n = p.length();
        memo = new int[m][n];
        for (int[] row : memo) {
            Arrays.fill(row, -1);
        }
        // pointers i and j start at index 0
        return dp(s, 0, p, 0);
    }

    // check whether p[j..] matches s[i..]
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

        // check the memo to avoid redundant computation
        if (memo[i][j] != -1) {
            return memo[i][j] == 1;
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
        // record the current result into the memo
        memo[i][j] = res ? 1 : 0;
        return res;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/regular-expression-matching/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Code visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>



The code uses a memo `memo` to eliminate overlapping subproblems. How can you spot overlapping subproblems at a glance? See the technique in the earlier [DP FAQ](https://labuladong.online/algo/dynamic-programming/faq-summary/) — extract the recursion framework of the regex algorithm:

```java
boolean dp(String s, int i, String p, int j) {
    dp(s, i, p, j + 2);     // 1
    dp(s, i + 1, p, j);     // 2
    dp(s, i + 1, p, j + 1); // 3
    dp(s, i, p, j + 2);     // 4
}
```

If you want to get from `dp(s, i, p, j)` to `dp(s, i+2, p, j+2)`, there are at least two paths: `1 -> 2 -> 2` and `3 -> 3`. So state `(i+2, j+2)` is computed multiple times — overlapping subproblems exist; we need a memo to eliminate them and improve efficiency.

DP time complexity = "total number of states" * "time per recursion". The total state count is the number of `(i, j)` combinations, `M * N` (`M` = length of `s`, `N` = length of `p`); the recursive function `dp` has no loops (we ignore base-case loops since they trigger finitely often), so each recursion takes constant time. Multiplied, total time is $O(MN)$.

Space complexity is just the memo's size, $O(MN)$.







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
| - | [剑指 Offer 19. regex表达式匹配](https://leetcode.cn/problems/zheng-ze-biao-da-shi-pi-pei-lcof/?show=1) | 🔴 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**

