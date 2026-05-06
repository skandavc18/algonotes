# DP: four-keys keyboard

<p align='center'>
<a href="https://github.com/labuladong/fucking-algorithm" target="view_window"><img alt="GitHub" src="https://img.shields.io/github/stars/labuladong/fucking-algorithm?label=Stars&style=flat-square&logo=GitHub"></a>
<a href="https://labuladong.online/algo/" target="_blank"><img class="my_header_icon" src="https://img.shields.io/static/v1?label=Premium%20course&message=View&color=pink&style=flat"></a>
<a href="https://www.zhihu.com/people/labuladong"><img src="https://img.shields.io/badge/%E7%9F%A5%E4%B9%8E-@labuladong-000000.svg?style=flat-square&logo=Zhihu"></a>
<a href="https://space.bilibili.com/14089380"><img src="https://img.shields.io/badge/Bilibili-@labuladong-000000.svg?style=flat-square&logo=Bilibili"></a>
</p>

![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: [the new site membership](https://labuladong.online/algo/intro/site-vip/) is about to get a price hike; existing users can renew. Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [651. 4 Keys Keyboard](https://leetcode.com/problems/4-keys-keyboard/)🔒 | [651. 4 Keys Keyboard](https://leetcode.cn/problems/4-keys-keyboard/)🔒 | 🟠

**-----------**

LeetCode 651 "4 Keys Keyboard" is interesting and clearly shows: different `dp` array definitions need completely different logic, leading to completely different solutions.

First, the problem:

Suppose you have a special keyboard with only four keys:

1. `A` key: print one `A` on the screen.

2. `Ctrl-A` key: select the whole screen.

3. `Ctrl-C` key: copy the selected region into the buffer.

4. `Ctrl-V` key: paste the buffer's content at the cursor.

These are the same select/copy/paste functions we use every day; the problem treats `Ctrl` combos as a single key. You can perform `N` operations total — what's the maximum number of `A`'s shown on the screen?

Function signature:

<!-- muliti_language -->
```java
int maxA(int N);
```

For example, with `N = 3`, the algorithm returns 3 — pressing `A` three times is optimal.

With `N = 7`, the algorithm returns 9. The optimal sequence:

`A`, `A`, `A`, `Ctrl-A`, `Ctrl-C`, `Ctrl-V`, `Ctrl-V`

This produces 9 `A`'s.

How do we get the most `A`'s after `N` keystrokes? Brute force — at each keystroke, enumerate the four possibilities. Clearly a DP problem.

### Approach 1

Easy to understand but inefficient. Let's go through the routine: **for a DP problem, first identify the "states" and the "choices"**.

For this problem, the "choices" at each keystroke are obvious: 4 — the four keys mentioned, namely `A`, `C-A`, `C-C`, `C-V` (`C` = `Ctrl`).

Now, what "states" does this problem have? **In other words, what info do we need to break the original problem into smaller subproblems?**

Try defining three states: the first is the remaining number of operations `n`; the second is the current count of `A` characters on screen, `a_num`; the third is the count of `A` characters in the clipboard, `copy`.

With this state definition, the base case is clear: when `n` is 0, `a_num` is the answer.

Combined with the 4 choices above, we can express them via state transitions:

```python
dp(n - 1, a_num + 1, copy),    # A
explanation: press A — one more character on screen
costs 1 operation

dp(n - 1, a_num + copy, copy), # C-V
explanation: paste — the clipboard's characters join the screen
costs 1 operation

dp(n - 2, a_num, a_num)        # C-A C-C
explanation: select-all and copy must be used together;
clipboard's count of A becomes the screen's count
costs 2 operations
```

The problem size `n` keeps shrinking and must reach `n = 0`, the base case, so the approach is correct:

<!-- muliti_language -->
```python
def maxA(N: int) -> int:

    # for state (n, a_num, copy),
    # the screen can ultimately have at most dp(n, a_num, copy) A's
    def dp(n, a_num, copy):
        # base case
        if n <= 0: return a_num;
        # try each choice; take the max
        return max(
                dp(n - 1, a_num + 1, copy),    # A
                dp(n - 1, a_num + copy, copy), # C-V
                dp(n - 2, a_num, a_num)        # C-A C-C
            )

    # we have N keystrokes; screen and clipboard are empty
    return dp(N, 0, 0)
```

This solution is intuitive — semantics are clear. Continue the routine: use a memo to eliminate overlapping subproblems:

<!-- muliti_language -->
```python
def maxA(N: int) -> int:
    # memo
    memo = dict()
    def dp(n, a_num, copy):
        if n <= 0: return a_num;
        # avoid recomputing overlapping subproblems
        if (n, a_num, copy) in memo:
            return memo[(n, a_num, copy)]

        memo[(n, a_num, copy)] = max(
                # same choices as before
            )
        return memo[(n, a_num, copy)]

    return dp(N, 0, 0)
```

After this optimization, subproblems no longer repeat, but their count is still huge — submitting on LeetCode TLEs.

We try to analyze the time complexity and find it not easy. We can rewrite the dp function as a dp array:

```python
dp[n][a_num][copy]
# total state count (time/space complexity) is the volume of this 3D array
```

We know `n` is at most `N`, but `a_num` and `copy` are hard to bound — complexity is at least O(N^3). So this algorithm is poor — too high a complexity, and no obvious further optimization.

This means our state definition isn't great. Below, we try a different definition.

### Approach 2

Slightly more complex but more efficient. Same routine; the 4 choices remain, but we define just one state — the remaining keystrokes `n`.

This algorithm rests on the fact that **the optimal key sequence has only two forms**:

Either keep pressing `A`: `A,A,...A` (when N is small).

Or this form: `A,A,...C-A,C-C,C-V,C-V,...C-V` (when N is large).

When the character count is small (small N), the cost of `C-A C-C C-V` may be too high — better to press `A` repeatedly. When N is large, later `C-V`'s yield big rewards. In this case, the operation sequence roughly is: **a few `A`'s at the start, then `C-A C-C` followed by several `C-V`'s, then another `C-A C-C` followed by several more `C-V`'s, repeating**.

In other words, the last keystroke is either `A` or `C-V`. With that in mind, we can design the algorithm based on these two cases:

```java
int[] dp = new int[N + 1];
// definition: dp[i] is the maximum number of A's after i operations
for (int i = 0; i <= N; i++) 
    dp[i] = max(
            press A this time,
            press C-V this time
        )
```

For "press `A`" — just one new A added to the state at `i - 1`:

```java
// pressing A — just one more A than last time
dp[i] = dp[i - 1] + 1;
```
For "press `C-V`", we also need to consider where we did `C-A C-C`.

**As noted, the optimal sequence is `C-A C-C` followed by a series of `C-V`'s, so we use a variable `j` as the start of those `C-V`'s**. Then the 2 operations before `j` should be `C-A C-C`:

<!-- muliti_language -->
```java
public int maxA(int N) {
    int[] dp = new int[N + 1];
    dp[0] = 0;
    for (int i = 1; i <= N; i++) {
        // press A
        dp[i] = dp[i - 1] + 1;
        for (int j = 2; j < i; j++) {
            // select-all & copy at dp[j-2], paste i - j times consecutively
            // screen has dp[j - 2] * (i - j + 1) A's
            dp[i] = Math.max(dp[i], dp[j - 2] * (i - j + 1));
        }
    }
    // max number of A's after N keystrokes
    return dp[N];
}
```

Subtracting 2 from `j` reserves operations for `C-A C-C` — see the figure:

![](https://labuladong.online/algo/images/4keyboard/1.jpg)

The algorithm is done — time O(N^2), space O(N), reasonably efficient.

### Final summary

DP's challenge is finding the state transition. Different definitions yield different transition logic. Even though all yield correct results, efficiency can vary enormously.

Reviewing approach 1, overlapping subproblems are eliminated, but efficiency is still low. Where? Abstract the recursion:

```python
def dp(n, a_num, copy):
    dp(n - 1, a_num + 1, copy),    # A
    dp(n - 1, a_num + copy, copy), # C-V
    dp(n - 2, a_num, a_num)        # C-A C-C
```

Looking at this enumeration, sequences like `C-A C-C, C-A C-C...` or `C-V, C-V, ...` are possible. They aren't optimal, but we didn't avoid them — adding many unnecessary subproblem computations.

In approach 2, with a moment's thought, we realize the optimal sequence has the form `A, A.. C-A, C-C, C-V, C-V.. C-A, C-C, C-V..`.

Based on this fact, we redefined the state and the transition, logically reducing invalid subproblems and improving efficiency.


<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [One method demolishes the LeetCode House Robber series](https://labuladong.online/algo/dynamic-programming/house-robber/)
 - [Optimal substructure and dp-array traversal direction](https://labuladong.online/algo/dynamic-programming/faq-summary/)

</details><hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

**"labuladong's algorithm notes" has been published. Follow my public account for details; reply "bundle" in the backend to download the companion PDF and the problem-solving bundle**:

![](https://labuladong.online/algo/images/souyisou2.png)

====== Other language code ======

### javascript

[651. Four Keys Keyboard](https://leetcode-cn.com/problems/4-keys-keyboard)

**1. Approach 1**

```js
let maxA = function (N) {
    // memo
    let memo = {}

    let dp = function (n, a_num, copy) {
        if (n <= 0) {
            return a_num;
        }

        let key = n + ',' + a_num + ',' + copy
        // avoid recomputing overlapping subproblems
        if (memo[key] !== undefined) {
            return memo[key]
        }

        memo[key] = Math.max(
            dp(n - 1, a_num + 1, copy),    // A
            dp(n - 1, a_num + copy, copy), // C-V
            dp(n - 2, a_num, a_num)        // C-A C-C
        )

        return memo[key]
    }

    return dp(N, 0, 0)
}
```

**2. Approach 2**

```js
var maxA = function (N) {
    let dp = new Array(N + 1);
    dp[0] = 0;
    for (let i = 1; i <= N; i++) {
        // press A
        dp[i] = dp[i - 1] + 1;
        for (let j = 2; j < i; j++) {
            // select-all & copy dp[j-2], paste i - j times
            // screen has dp[j - 2] * (i - j + 1) A's
            dp[i] = Math.max(dp[i], dp[j - 2] * (i - (j - 2) - 1));
        }
    }
    // max number of A's after N keystrokes
    return dp[N];
}
```
