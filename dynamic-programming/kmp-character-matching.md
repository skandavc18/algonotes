# DP: KMP character-matching algorithm

<p align='center'>
<a href="https://github.com/labuladong/fucking-algorithm" target="view_window"><img alt="GitHub" src="https://img.shields.io/github/stars/labuladong/fucking-algorithm?label=Stars&style=flat-square&logo=GitHub"></a>
<a href="https://labuladong.online/algo/" target="_blank"><img class="my_header_icon" src="https://img.shields.io/static/v1?label=Premium%20course&message=View&color=pink&style=flat"></a>
<a href="https://www.zhihu.com/people/labuladong"><img src="https://img.shields.io/badge/%E7%9F%A5%E4%B9%8E-@labuladong-000000.svg?style=flat-square&logo=Zhihu"></a>
<a href="https://space.bilibili.com/14089380"><img src="https://img.shields.io/badge/Bilibili-@labuladong-000000.svg?style=flat-square&logo=Bilibili"></a>
</p>

![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: [the new site membership](https://labuladong.online/algo/intro/site-vip/) is about to get a price hike; existing users can renew. Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | 力扣 | Difficulty |
| :----: | :----: | :----: |
| [28. Find the Index of the First Occurrence in a String](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/) | [28. 找出字符串中第一个匹配项的下标](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/) | 🟠

**-----------**

::: tip

Before reading, I recommend studying another string-matching algorithm: [Rabin-Karp](https://labuladong.online/algo/practice-in-action/rabinkarp/).

:::

KMP (Knuth-Morris-Pratt) is a famous string-matching algorithm — efficient but admittedly complex.

Many readers complain they can't understand KMP. Normal — thinking about university textbook explanations of KMP, who knows how many future Knuths, Morrises, and Pratts were prematurely scared off. Some good students manually trace KMP to help understand — that's one approach. This article helps you understand the algorithm at the logical level. In ten lines of code, KMP's mystery vanishes.

**Convention up-front: `pat` denotes the pattern string of length `M`; `txt` is the text of length `N`. KMP searches for the substring `pat` in `txt`; if found, return the start index; otherwise -1**.

Why do I think KMP is a DP problem? I'll explain shortly. For DP, we've emphasized many times: clarify the `dp` array's meaning. The same problem may admit multiple `dp` definitions — different definitions yield different solutions.

The KMP you've seen is probably: a weird process on `pat` produces a 1D `next` array; then another complex process matches `txt` using that array. Time O(N), space O(M). The `next` array is essentially the `dp` array; its entries' meaning relates to `pat`'s prefix-suffix info — complex rules, hard to follow. **This article uses a 2D `dp` array (still O(M) space) with redefined entries, dramatically shortening code and improving explainability**.

::: note

The code references *Algorithms 4th ed.*; the original used the array name `dfa` (deterministic finite automaton). Since our public account has a DP series, I won't use such fancy terms — I made small modifications and reused the name `dp`.

:::

### 1. KMP overview

First a quick comparison: how KMP differs from brute force, where the difficulty lies, and the connection to DP.

LeetCode 28 "Implement strStr" is the string-matching problem. Brute force is easy:

<!-- muliti_language -->
```java
// brute force (pseudo-code)
int search(String pat, String txt) {
    int M = pat.length;
    int N = txt.length;
    for (int i = 0; i <= N - M; i++) {
        int j;
        for (j = 0; j < M; j++) {
            if (pat[j] != txt[i+j])
                break;
        }
        // pat all matched
        if (j == M) return i;
    }
    // no pat substring in txt
    return -1;
}
```

Brute force: on a mismatch, both `txt` and `pat` pointers back up. Nested for loops, time `O(MN)`, space `O(1)`. Main issue: with many repeated characters, the algorithm looks dumb.

E.g. `txt = "aaacaaab", pat = "aaab"`:

![](https://labuladong.online/algo/images/kmp/1.gif)

Obviously `pat` has no `c`, so we shouldn't back up `i`. Brute force does many useless operations.

KMP differs: it spends space recording info, looking smart in such cases:

![](https://labuladong.online/algo/images/kmp/2.gif)

Similarly for `txt = "aaaaaaab", pat = "aaab"` — brute force still backs up `i` dumbly, while KMP is clever:

![](https://labuladong.online/algo/images/kmp/3.gif)

Because KMP knows the `a`'s before `b` are matched, each time it only compares `b`.

**KMP never backs up `txt`'s pointer `i` — never re-scans `txt`. Instead, using info in `dp`, it moves `pat` to the right position to continue matching**, with O(N) time — space for time, hence I view it as DP.

KMP's hard part: how to compute `dp`? How to use it to correctly move `pat`'s pointer? This needs the **deterministic finite automaton** — don't be scared by the fancy term; it's just like DP's `dp` array. After learning, you can use this term to scare others.

One more thing: **computing `dp` only depends on `pat`**. Given `pat`, I can compute `dp`, and you can give me different `txt`'s — using `dp`, I can match in O(N).

E.g. the two examples above:

```python
txt1 = "aaacaaab" 
pat = "aaab"
txt2 = "aaaaaaab" 
pat = "aaab"
```

Different `txt` but same `pat`, so KMP uses the same `dp` array.

For `txt1`'s upcoming mismatch:

![](https://labuladong.online/algo/images/kmp/txt1.jpg)

`dp` directs `pat` to move:

![](https://labuladong.online/algo/images/kmp/txt2.jpg)

::: note

Don't think of `j` as an index — more accurately it's a **state**, which is why it appears in odd positions. We'll explain later.

:::

For `txt2`'s upcoming mismatch:

![](https://labuladong.online/algo/images/kmp/txt3.jpg)

`dp` directs `pat` to move:

![](https://labuladong.online/algo/images/kmp/txt4.jpg)

Knowing `dp` only depends on `pat`, design KMP elegantly:

<!-- muliti_language -->
```java
public class KMP {
    private int[][] dp;
    private String pat;

    public KMP(String pat) {
        this.pat = pat;
        // build dp from pat
        // O(M) time
    }

    public int search(String txt) {
        // use dp to match txt
        // O(N) time
    }
}
```

When using the same `pat` against different `txt`'s, no need to rebuild `dp`:

```java
KMP kmp = new KMP("aaab");
int pos1 = kmp.search("aaacaaab"); //4
int pos2 = kmp.search("aaaaaaab"); //4
```

### 2. State machine overview

Why is KMP related to a state machine? Because `pat`-matching is state transitions. With pat = "ABABC":

![](https://labuladong.online/algo/images/kmp/state.jpg)

The numbers in circles are states; state 0 is the initial state, state 5 (`pat.length`) is the terminal state. Match starts at state 0; reaching the terminal means we found `pat` in `txt`. State 2 means "AB" has matched:

![](https://labuladong.online/algo/images/kmp/state2.jpg)

Different states have different transition behaviors. Suppose we're at state 4: on character A we go to state 3; on C, state 5; on B, state 0:

![](https://labuladong.online/algo/images/kmp/state4.jpg)

Concretely: `j` points to the current state, `pat` matched up to state 4:

![](https://labuladong.online/algo/images/kmp/exp1.jpg)

On character "A", per the arrow, going to state 3 is smartest:

![](https://labuladong.online/algo/images/kmp/exp3.jpg)

On character "B", per the arrow, only state 0 (back to square one):

![](https://labuladong.online/algo/images/kmp/exp5.jpg)

On character "C", per the arrow, terminal state 5 — match complete:

![](https://labuladong.online/algo/images/kmp/exp7.jpg)

Other characters may appear, e.g. Z — clearly state 0, since `pat` has no Z:

![](https://labuladong.online/algo/images/kmp/z.jpg)

For clarity, when drawing state transitions we omit arrows for "other characters" going to state 0, drawing only the characters in `pat`:

![](https://labuladong.online/algo/images/kmp/allstate.jpg)

KMP's most crucial step is constructing this state-transition graph. **To determine the transition behavior, we need two variables: the current matching state, and the encountered character**. With these two, we know which state to transition to.

Below is the matching of `txt` using this state-transition graph:

![](https://labuladong.online/algo/images/kmp/kmp.gif)

**Remember this GIF — that's KMP's core logic**!

To describe the transition graph, define a 2D dp:

```python
dp[j][c] = next
0 <= j < M, the current state
0 <= c < 256, the encountered character (ASCII code)
0 <= next <= M, the next state

dp[4]['A'] = 3 means:
current state is 4; on character A,
pat should transition to state 3

dp[1]['B'] = 2 means:
current state is 1; on character B,
pat should transition to state 2
```

By this `dp` definition, we can write KMP's `search`:

<!-- muliti_language -->
```java
public int search(String txt) {
    int M = pat.length();
    int N = txt.length();
    // pat's initial state is 0
    int j = 0;
    for (int i = 0; i < N; i++) {
        // current state j; on character txt[i],
        // which state should pat transition to?
        j = dp[j][txt.charAt(i)];
        // reached terminal state — return the match's start index
        if (j == M) return i - M + 1;
    }
    // didn't reach the terminal — match failed
    return -1;
}
```

Easy so far. `dp` is the state-transition graph above. If unclear, replay the GIF. Now: how to build `dp` from `pat`?

### 3. Building the state-transition graph

Recall: **to determine transitions, we need the current matching state and the encountered character**. We've defined `dp` accordingly. The framework for building `dp` is:

```python
for 0 <= j < M:    # state
    for 0 <= c < 256:  # character
        dp[j][c] = next
```

How do we compute `next`? Clearly, **if `c` matches `pat[j]`**, the state advances by one: `next = j + 1`. Call this **state advance**:

![](https://labuladong.online/algo/images/kmp/forward.jpg)

**If `c` doesn't match `pat[j]`**, the state must rewind (or stay). Call this **state restart**:

![](https://labuladong.online/algo/images/kmp/back.jpg)

How do we know where to restart? Define another concept: **shadow state** (my term), denoted `X`. **The shadow state shares the longest matching prefix with the current state**:

![](https://labuladong.online/algo/images/kmp/shadow.jpg)

Current state `j = 4`; shadow `X = 2`; both share prefix "AB". Because state `X` and state `j` share a prefix, when `j` is about to restart (on a `c` that doesn't match `pat[j]`), we can use `X`'s transition graph to find the **nearest restart position**.

E.g. above: if state `j` encounters "A", where to go? Only "C" advances; "A" forces a restart. **State `j` delegates this character to `X`: `dp[j]['A'] = dp[X]['A']`**:

![](https://labuladong.online/algo/images/kmp/shadow1.jpg)

Why this works: since `j` confirmed "A" can't advance, we **must rewind**, and KMP wants to **rewind as little as possible** to avoid extra work. So `j` asks `X` (which shares its prefix); if `X` on "A" can advance, we follow that — fewest rewinds.

![](https://labuladong.online/algo/images/kmp/A.gif)

Of course, on "B", state `X` can't advance either — must rewind; `j` follows `X`'s direction:

![](https://labuladong.online/algo/images/kmp/shadow2.jpg)

How does `X` know to rewind to state 0 on "B"? Because `X` always lags behind `j`, so `X`'s transitions were computed earlier. DP — using past results to solve the present.

So we refine the framework:

```python
int X # shadow state
for 0 <= j < M:
    for 0 <= c < 256:
        if c == pat[j]:
            # state advance
            dp[j][c] = j + 1
        else: 
            # state restart
            # delegate to X to compute the restart position
            dp[j][c] = dp[X][c] 
```

### 4. Implementation

If you understand the above, congrats — only one question remains: how do we get the shadow state `X`? Full code:

<!-- muliti_language -->
```java
public class KMP {
    private int[][] dp;
    private String pat;

    public KMP(String pat) {
        this.pat = pat;
        int M = pat.length();
        // dp[state][char] = next state
        dp = new int[M][256];
        // base case
        dp[0][pat.charAt(0)] = 1;
        // shadow state X starts at 0
        int X = 0;
        // current state j starts at 1
        for (int j = 1; j < M; j++) {
            for (int c = 0; c < 256; c++) {
                if (pat.charAt(j) == c) 
                    dp[j][c] = j + 1;
                else 
                    dp[j][c] = dp[X][c];
            }
            // update shadow state
            X = dp[X][pat.charAt(j)];
        }
    }

    public int search(String txt) {...}
}
```

Explain this line:

```java
// base case
dp[0][pat.charAt(0)] = 1;
```

Base case: only on character `pat[0]` does the state go from 0 to 1; on other characters we stay at state 0 (Java initializes arrays to 0).

Shadow `X` is initialized to 0 and updated as `j` advances. How **to update `X`**:

```java
int X = 0;
for (int j = 1; j < M; j++) {
    ...
    // update shadow state
    // current state X; on character pat[j],
    // which state should pat transition to?
    X = dp[X][pat.charAt(j)];
}
```

Updating `X` is very similar to updating `j` in `search`:

```java
int j = 0;
for (int i = 0; i < N; i++) {
    // current state j; on character txt[i],
    // which state should pat transition to?
    j = dp[j][txt.charAt(i)];
    ...
}
```

**The principle is subtle**. Note the for-loop initial values. The latter matches `pat` in `txt`; the former matches `pat[1..end]` in `pat`. State `X` always lags state `j` by one, sharing the longest common prefix. So my "shadow state" metaphor fits.

Building `dp` proceeds from the base case `dp[0][..]` forward. That's why I consider KMP DP.

Below is the full construction:

![](https://labuladong.online/algo/images/kmp/dfa.gif)

So KMP's core is finally written! Full code:

<!-- muliti_language -->
```java
public class KMP {
    private int[][] dp;
    private String pat;

    public KMP(String pat) {
        this.pat = pat;
        int M = pat.length();
        // dp[state][char] = next state
        dp = new int[M][256];
        // base case
        dp[0][pat.charAt(0)] = 1;
        // shadow state X starts at 0
        int X = 0;
        // build the state-transition graph (slightly compacted)
        for (int j = 1; j < M; j++) {
            for (int c = 0; c < 256; c++)
                dp[j][c] = dp[X][c];
            dp[j][pat.charAt(j)] = j + 1;
            // update shadow state
            X = dp[X][pat.charAt(j)];
        }
    }

    public int search(String txt) {
        int M = pat.length();
        int N = txt.length();
        // pat's initial state is 0
        int j = 0;
        for (int i = 0; i < N; i++) {
            // compute pat's next state
            j = dp[j][txt.charAt(i)];
            // reached terminal — return the result
            if (j == M) return i - M + 1;
        }
        // didn't reach the terminal — match failed
        return -1;
    }
}
```

After the detailed explanation, you should understand. You can also rewrite KMP as a single function. Core code is the for-loop in two functions — count: more than ten lines?

### 5. Final summary

Traditional KMP uses a 1D `next` array recording prefix info. This article uses a 2D `dp` array from a state-transition view. Space is still O(256·M) = O(M).

While matching `pat` in `txt`, knowing "current state" and "encountered character" determines the transition (advance or rewind).

For pattern `pat`, there are M states; ASCII has at most 256 characters. So we build `dp[M][256]` to cover everything, with meaning:

`dp[j][c] = next` means: at state `j`, on character `c`, transition to `next`.

Once meaning is clear, `search` is easy.

To build `dp`, use auxiliary state `X`, always one state behind `j`, sharing the longest common prefix — the "shadow state".

When building state `j`'s transitions, only `pat[j]` advances (`dp[j][pat[j]] = j+1`); other characters cause rewind — ask the shadow `X` where to rewind (`dp[j][other] = dp[X][other]`, where `other` is anything but `pat[j]`).

Initialize shadow `X` to 0; as `j` advances, update `X` similarly to how `search` updates `j` (`X = dp[X][pat[j]]`).

KMP is just DP. Our public account has a DP series following one framework: describe the problem logic, clarify the `dp` array's meaning, define base cases — that's it. I hope this article deepens your understanding of DP.



<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [My problem-solving insights: the essence of algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Sliding-window extension: Rabin-Karp string matching](https://labuladong.online/algo/practice-in-action/rabinkarp/)

</details><hr>





**＿＿＿＿＿＿＿＿＿＿＿＿＿**

**"labuladong's algorithm notes" has been published. Follow my public account for details; reply "bundle" in the backend to download the companion PDF and the problem-solving bundle**:

![](https://labuladong.online/algo/images/souyisou2.png)

====== Other language code ======

[28. Implement strStr()](https://leetcode-cn.com/problems/implement-strstr)

### python

[MoguCloud](https://github.com/MoguCloud) provides a complete Python implementation of strStr():

```python
class Solution:
  def strStr(self, haystack: str, needle: str) -> int:
    # boundary check
    if not needle:
      return 0
    pat = needle
    txt = haystack

    M = len(pat)
    # dp[state][char] = next state
    dp = [[0 for _ in range(256)] for _ in pat]
    # base case
    dp[0][ord(pat[0])] = 1
    # shadow state X starts at 0
    X = 0
    for j in range(1, M):
      for c in range(256):
        dp[j][c] = dp[X][c]
        dp[j][ord(pat[j])] = j + 1
        # update shadow state
        X = dp[X][ord(pat[j])]

        N = len(txt)
        # pat starts at state 0
        j = 0
        for i in range(N):
          # compute pat's next state
          j = dp[j][ord(txt[i])]
          # reached terminal — return result
          if j == M:
            return i - M + 1
          # didn't reach terminal — match failed
          return -1
```



### javascript

```js
class KMP {
  constructor(pat) {
    this.pat = pat;
    let m = pat.length;

    // dp[state][char] = next state — initialize an m*256 integer matrix
    this.dp = new Array(m);
    for (let i = 0; i < m; i++) {
      this.dp[i] = new Array(256);
      this.dp[i].fill(0, 0, 256);
    }

    // base case
    this.dp[0][this.pat[0].charCodeAt()] = 1;

    // shadow state X starts at 0
    let x = 0;

    // build the state-transition graph
    for (let j = 1; j < m; j++) {
      for (let c = 0; c < 256; c++) {
        this.dp[j][c] = this.dp[x][c];
      }

      // dp[][corresponding ASCII code]
      this.dp[j][this.pat[j].charCodeAt()] = j + 1;

      // update shadow state
      x = this.dp[x][this.pat[j].charCodeAt()]
    }
  }

  search(txt) {

    let m = this.pat.length;
    let n = txt.length;

    // pat's initial state is 0
    let j = 0;
    for (let i = 0; i < n; i++) {
      // compute pat's next state
      j = this.dp[j][txt[i].charCodeAt()];

      // reached terminal — return result
      if (j === m) return i - m + 1;
    }

    // didn't reach terminal — match failed
    return -1;
  }

}

/**
 * @param {string} haystack
 * @param {string} needle
 * @return {number}
 */
var strStr = function(haystack, needle) { 
  if(haystack === ""){
    if(needle !== ""){
      return -1;
    }
    return 0;
  }

  if(needle === ""){
    return 0;
  }
  let kmp = new KMP(needle);
  return kmp.search(haystack)
};
```
