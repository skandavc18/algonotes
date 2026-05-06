# Mental switch between DP and backtracking


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [139. Word Break](https://leetcode.com/problems/word-break/) | [139. Word Break](https://leetcode.cn/problems/word-break/) | 🟠 |
| [140. Word Break II](https://leetcode.com/problems/word-break-ii/) | [140. Word Break II](https://leetcode.cn/problems/word-break-ii/) | 🔴 |

**-----------**


> [!NOTE]
> Before reading, you should first study:
> 
> - [Binary tree algorithm series (outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)

In the earlier [Binary tree (outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/), I categorized recursive enumeration into "traversal" and "decompose-the-problem" approaches. The "traversal" approach extends to [backtracking](https://labuladong.online/algo/essential-technique/backtrack-framework/); the "decompose-the-problem" approach extends to [DP](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/).

This mental switch isn't limited to binary-tree problems. This article steps outside binary trees to look at how to abstract real algorithm problems into tree shapes, optimize step by step, and switch smoothly between "traversal" and "decompose the problem" — from backtracking to DP.

A side note first: the earlier [DP framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/) said **standard DP problems must seek an optimum**, because DP requires "optimal substructure" — the original problem's optimum is derived from subproblems' optima.

But colloquially, even when not seeking an optimum, as long as we use a memo to eliminate overlapping subproblems, we usually call it DP. Strictly speaking that's not the DP definition; "DFS with a memo" might be more accurate. Don't fuss over the naming — if calling it DP rolls off the tongue, fine.

The two problems in this article aren't optimum-seeking, but I still call their solutions DP — heads up so detail-oriented readers aren't confused. No more chatter — let's see the problems.

## Word Break I

LeetCode 139 "Word Break":

<Problem slug="word-break" />

Function signature:

```java
boolean wordBreak(String s, List<String> wordDict);
```

This is a high-frequency interview problem. Let's tackle it via "traversal" and "decompose the problem" approaches.

### Traversal approach (backtracking)

**Backtracking is the most classic application for permutation/combination problems**. Let's rephrase: given a word list `wordDict` (no duplicates) and a string `s`, decide whether you can pick a sequence of words (with repetition) from `wordDict` to form `s`.

This is the last variant from [Backtracking demolishes the 9 permutation/combination variants](https://labuladong.online/algo/essential-technique/permutation-combination-subset-all-in-one/): permutations of distinct elements with repetition. I wrote a `permuteRepeat` function:

```java
class Solution {
    List<List<Integer>> res = new LinkedList<>();
    LinkedList<Integer> track = new LinkedList<>();

    // permutations of distinct elements with repetition
    public List<List<Integer>> permuteRepeat(int[] nums) {
        backtrack(nums);
        return res;
    }

    // backtracking core function
    void backtrack(int[] nums) {
        // base case — reached a leaf
        if (track.size() == nums.length) {
            // collect the path from root to leaf
            res.add(new LinkedList(track));
            return;
        }

        // standard backtracking framework
        for (int i = 0; i < nums.length; i++) {
            // make a choice
            track.add(nums[i]);
            // recurse into the next layer
            backtrack(nums);
            // undo the choice
            track.removeLast();
        }
    }
}
```

For input `nums = [1,2,3]`, output is 3^3 = 27 combinations:


```java
[
  [1,1,1],[1,1,2],[1,1,3],[1,2,1],[1,2,2],[1,2,3],[1,3,1],[1,3,2],[1,3,3],
  [2,1,1],[2,1,2],[2,1,3],[2,2,1],[2,2,2],[2,2,3],[2,3,1],[2,3,2],[2,3,3],
  [3,1,1],[3,1,2],[3,1,3],[3,2,1],[3,2,2],[3,2,3],[3,3,1],[3,3,2],[3,3,3]
]
```


This essentially traverses a perfect `N`-ary tree of height `N + 1` (`N` = length of `nums`), where each root-to-leaf path is one permutation:

![](https://labuladong.online/algo/images/word-break/1.jpeg)

By analogy, this article's problem is similar. With `wordDict = ["a", "aa", "ab"], s = "aaab"`, we want to use `wordDict` to form `s`. We face a similar `M`-ary tree (`M` = number of words). **At each node, look at which word can match a prefix of `s[i..]`, deciding which branch to take**:

![](https://labuladong.online/algo/images/word-break/2.jpeg)

Then, per the [Backtracking framework](https://labuladong.online/algo/essential-technique/backtrack-framework/), think of `backtrack` as a pointer roaming the backtracking tree, maintaining a per-node variable `i` to traverse the whole tree and find combinations matching `s`.

Backtracking solution:

```java
class Solution {
    List<String> wordDict;
    // record whether a valid answer was found
    boolean found = false;
    // record the backtracking path
    LinkedList<String> track = new LinkedList<>();

    // main function
    public boolean wordBreak(String s, List<String> wordDict) {
        this.wordDict = wordDict;
        // run backtracking to enumerate all combinations
        backtrack(s, 0);
        return found;
    }

    // backtracking framework
    void backtrack(String s, int i) {
        // base case
        if (found) {
            // already found an answer — stop recursing
            return;
        }
        if (i == s.length()) {
            // entire s matched — found a valid answer
            found = true;
            return;
        }

        // backtracking framework
        for (String word : wordDict) {
            // see which word matches a prefix of s[i..]
            int len = word.length();
            if (i + len <= s.length()
                && s.substring(i, i + len).equals(word)) {
                // a word matches s[i..i+len)
                // make a choice
                track.addLast(word);
                // descend, continue matching s[i+len..]
                backtrack(s, i + len);
                // undo the choice
                track.removeLast();
            }
        }
    }
}
```

Strictly per the backtracking framework — easy to follow. But it doesn't pass all tests. Let's analyze time complexity per [Time/space complexity guide](https://labuladong.online/algo/essential-technique/complexity-analysis/).

A rough estimate: number of recursive calls × per-call work. Here, each tree node is one cut on `s`. How many cuts are possible at most? For length-`N` `s`, there are `N - 1` "gaps" — each "cut" or "no cut" — so up to $O(2^N)$ cut configurations, i.e. up to $O(2^N)$ tree nodes.

Realistically pruning helps, but worst-case is exponential.

Per-call work? Mostly looping `wordDict` to find a prefix match for `s[i..]`:

```java
// iterate every word in wordDict
for (String word : wordDict) {
    // see which word matches a prefix of s[i..]
    int len = word.length();
    if (i + len <= s.length()
        && s.substring(i, i + len).equals(word)) {
        // a word matches s[i..i+len)
        // ...
    }
}
```

Let `M` = `wordDict` length, `N` = `s` length. Worst case is $O(MN)$ per call (loop $O(M)$, Java's `substring` $O(N)$). So total is $O(2^N \cdot MN)$.

Side optimization: enumerate `s[i..]`'s prefixes and check if `wordDict` contains them:

```java
// note: convert to a hash set so contains() is fast
HashSet<String> wordDict = new HashSet<>(wordDict);

// iterate every prefix of s[i..]
for (int len = 1; i + len <= s.length(); len++) {
    // see if wordDict has a word matching s[i..]
    String prefix = s.substring(i, i + len);
    if (wordDict.contains(prefix)) {
        // a word matches s[i..i+len)
        // ...
    }
}
```

Same result, but per-call complexity becomes $O(N^2)$.

Which is better? Depends on the constraints. The problem says `1 <= s.length <= 300, 1 <= wordDict.length <= 1000`, so $O(N^2)$ is smaller — slightly faster. Detail optimization; do it yourself, I won't write it.

Even with this optimization, total is still exponential $O(2^N \cdot N^2)$ — still won't pass. What's the issue?

Take `wordDict = ["a", "aa", "b"], s = "aaab"`. Backtracking enumerates duplicate situations:

![](https://labuladong.online/algo/images/word-break/3.jpeg)

The two red parts in the figure: although they came from different cuts, they yield the same `"aab"`, so their subtrees are identical — duplicate work, hence exponential complexity.

### Optimizing using the post-order position

Whatever the algorithm, the way to eliminate redundant computation is a memo. Backtracking can also have a memo — we call this "pruning" — cutting redundant subtrees.

Take the `"aab"` situation. I want the memo to tell me whether `"aab"` can be successfully cut. If we previously found it can't, skip — don't traverse the subtree to enumerate cuts. If we previously found it can, the memo isn't needed since `found == true` is itself a base case and the whole recursion terminates.

As [the binary tree / recursion mind-set](https://labuladong.online/algo/essential-technique/binary-tree-summary/) explains for pre/post-order positions: to make the memo do that, update it in the post-order position, since `"aab"` is a subtree. **You record in the memo whether the subtree can be cut after traversing it**.

The backtracking function `backtrack` is built for traversal — no return value, no info passed back from subtrees. But for this problem we have a workaround — the external `found` variable can tell us:

**If `found` is false, no successful cut was found yet, indirectly meaning the current subtree can't be cut**. So we can record in the memo and prune.

Concretely, just slight modification — only the changed parts:

```java
class Solution {
    // memo storing un-cuttable substrings (subtrees) to avoid recomputation
    HashSet<String> memo = new HashSet<>();

    // ...

    void backtrack(String s, int i) {
        if (found) {
            return;
        }
        if (i == s.length()) {
            found = true;
            return;
        }

        // newly added pruning: query whether the substring (subtree) was computed
        String suffix = s.substring(i);
        if (memo.contains(suffix)) {
            // current substring (subtree) can't be cut — don't recurse
            return;
        }

        for (String word : wordDict) {
            // ...
        }

        // post-order: record un-cuttable substrings (subtrees) into the memo
        if (!found) {
            memo.add(suffix);
        }
    }
}
```

### Decompose-the-problem approach (DP)

Above we used backtracking. That's only because this problem is relatively easy — we could update the memo at the post-order position via the `found` variable. You'll see Word Break II below can't do that.

To update the memo with subtree answers at the post-order position, we generally need the recursive function's return value — i.e. the "decompose" approach.

We just thought via permutation/combination. Now let's switch perspective: can we decompose the original problem into smaller, structurally identical subproblems, then derive the original answer from subproblem results?

For input `s`, if I find a word in `wordDict` matching the prefix `s[0..k]`, then I just need to construct `s[k+1..]` to construct `s`. In other words, I decompose larger `wordBreak(s[0..])` into smaller `wordBreak(s[k+1..])`.

Define a `dp` function:

```java
// definition: returns whether s[i..] can be assembled
int dp(String s, int i);

// to compute whether the entire s can be assembled, call dp(s, 0)
```

Translate the logic into pseudocode:

```java
List<String> wordDict;

// definition: returns whether s[i..] can be assembled
int dp(String s, int i) {
    // base case — s[i..] is empty
    if (i == s.length()) {
        return true;
    }
    // iterate wordDict, find words that are prefixes of s[i..]
    for (Strnig word : wordDict) {
        // word is a prefix of s[i..]
        if (s.substring(i).startsWith(word)) {
            int len = word.length();
            // as long as s[i+len..] can be assembled, s[i..] can be assembled
            if (dp(s, i + len) == true) {
                return true;
            }
        }
    }
    // tried every word — can't assemble the entire s
    return false;
}
```

Like backtracking, the for-loop in `dp` can be optimized:

```java
// note: use a hash set for fast existence check
HashSet<String> wordDict;

// definition: returns whether s[i..] can be assembled
int dp(String s, int i) {
    // base case — s[i..] is empty
    if (i == s.length()) {
        return true;
    }
    
    // iterate every prefix of s[i..]; see which are in wordDict
    for (int len = 1; i + len <= s.length(); len++) {
        // wordDict contains s[i..len)
        if (wordDict.contains(s.substring(i, i + len))) {
            // as long as s[i+len..] can be assembled, s[i..] can be assembled
            if (dp(s, i + len) == true) {
                return true;
            }
        }
    }
    // tried every word — can't assemble the entire s
    return false;
}
```

For this `dp` function, the pointer `i` is the "state", so add a memo to optimize and avoid redundant subproblem computation. Final solution:

```java
class Solution {
    // hash set for fast existence check
    HashSet<String> wordDict;
    // memo: -1 = not computed, 0 = can't assemble, 1 = can assemble
    int[] memo;

    // main function
    public boolean wordBreak(String s, List<String> wordDict) {
        // convert to hash set for fast existence check
        this.wordDict = new HashSet<>(wordDict);
        // initialize memo to -1
        this.memo = new int[s.length()];
        Arrays.fill(memo, -1);
        return dp(s, 0);
    }

    // definition: whether s[i..] can be assembled
    boolean dp(String s, int i) {
        // base case
        if (i == s.length()) {
            return true;
        }
        // avoid redundant computation
        if (memo[i] != -1) {
            return memo[i] == 0 ? false : true;
        }

        // iterate every prefix of s[i..]
        for (int len = 1; i + len <= s.length(); len++) {
            // see which prefixes are in wordDict
            String prefix = s.substring(i, i + len);
            if (wordDict.contains(prefix)) {
                // a word matches s[i..i+len)
                // as long as s[i+len..] can be assembled, s[i..] can be assembled
                boolean subProblem = dp(s, i + len);
                if (subProblem == true) {
                    memo[i] = 1;
                    return true;
                }
            }
        }
        // s[i..] can't be assembled
        memo[i] = 0;
        return false;
    }
}
```

> [!TIP]
> Computing `prefix`, we directly call the language's substring function — $O(N)$. Note the start index is fixed at `i`, end index `j` is increasing — manually maintain the `prefix` substring to avoid the substring call. Try this small optimization yourself.


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/word-break/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Code visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>

This solution passes all tests. By the [complexity guide](https://labuladong.online/algo/essential-technique/complexity-analysis/):

The memo eliminates duplicate nodes in the recursion tree, so call count drops from exponential to the state count $O(N)$. Per-call work is $O(N^2)$. Total $O(N^3)$ — much better than backtracking.

## Word Break II

With the previous problem in mind, LeetCode 140 "Word Break II" is easier:

<Problem slug="word-break-ii" />

This time we don't just want to know whether `s` can be assembled — we want to know **how**. Just slightly modify the previous solution.

### Traversal approach (backtracking)

The previous backtracking maintained a `found` variable to early-stop after finding one combination. Here we don't early-stop — collect every viable combination:

```java
class Solution {
    // record results
    List<String> res = new LinkedList<>();
    // record the backtracking path
    LinkedList<String> track = new LinkedList<>();
    List<String> wordDict;

    // main function
    public List<String> wordBreak(String s, List<String> wordDict) {
        this.wordDict = wordDict;
        // run backtracking to enumerate all combinations
        backtrack(s, 0);
        return res;
    }

    // backtracking framework
    void backtrack(String s, int i) {
        // base case
        if (i == s.length()) {
            // found a valid combination assembling the entire s; convert to a string
            res.add(String.join(" ", track));
            return;
        }

        // backtracking framework
        for (String word : wordDict) {
            // see which word matches a prefix of s[i..]
            int len = word.length();
            if (i + len <= s.length()
                && s.substring(i, i + len).equals(word)) {
                // a word matches s[i..i+len)
                // make a choice
                track.addLast(word);
                // descend, continue matching s[i+len..]
                backtrack(s, i + len);
                // undo the choice
                track.removeLast();
            }
        }
    }
}
```

Time complexity is similar to the previous problem — $O(2^N \cdot MN)$ — but the data scale is small, so it passes.

### Can we use the post-order position?

As before, optimization is possible — same situation:

![](https://labuladong.online/algo/images/word-break/3.jpeg)

Duplicate subtrees still cause unnecessary repeated traversal. We can still use memoization to cache the `"aab"` cut result.

But adding a memo to backtracking is awkward — the `track` variable only records the path from root to current node, not subtree info.

So problems like this typically need the decompose-the-problem approach with the function's return value updating the memo.

### Decompose-the-problem approach (DP)

This problem can also be solved via decomposition. Slight tweak to the previous `dp`:

```java
class Solution {
    HashSet<String> wordDict;
    // memo
    List<String>[] memo;

    public List<String> wordBreak(String s, List<String> wordDict) {
        this.wordDict = new HashSet<>(wordDict);
        memo = new List[s.length()];
        return dp(s, 0);
    }

    // definition: returns all ways to assemble s[i..] using wordDict
    List<String> dp(String s, int i) {
        List<String> res = new LinkedList<>();
        if (i == s.length()) {
            res.add("");
            return res;
        }
        // avoid redundant computation
        if (memo[i] != null) {
            return memo[i];
        }
        
        // iterate every prefix of s[i..]
        for (int len = 1; i + len <= s.length(); len++) {
            // see which prefixes are in wordDict
            String prefix = s.substring(i, i + len);
            if (wordDict.contains(prefix)) {
                // a word matches s[i..i+len)
                List<String> subProblem = dp(s, i + len);
                // every way to assemble s[i+len..] prefixed with `prefix`
                // is a way to assemble s[i..]
                for (String sub : subProblem) {
                    if (sub.isEmpty()) {
                        // avoid an extra space
                        res.add(prefix);
                    } else {
                        res.add(prefix + " " + sub);
                    }
                }
            }
        }
        // store in memo
        memo[i] = res;
        
        return res;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/word-break-ii/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Code visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>

This solution still uses memoization to eliminate overlapping subproblems, so `dp` calls drop to $O(N)$. But per-call work goes up because `subProblem` is a list of subsets — exponentially long.

Plus string concatenation is slow, and the memo consumes lots of memory storing every subproblem result. So big-O-wise, this is no better than backtracking — still exponential. But it does eliminate overlapping subproblems, so it's slightly cleverer than backtracking.

Conclusion: for permutation/combination problems we usually use backtracking to "traverse" the backtracking tree, not "decompose". Storing subproblem results requires lots of time and space; only when overlapping subproblems are extremely many is it worth it.

That's it. Hope you have a deeper understanding of backtracking and decompose-the-problem.


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

