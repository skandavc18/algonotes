# Sliding Window Core Code Template


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | [3. Longest Substring Without Repeating Characters](https://leetcode.cn/problems/longest-substring-without-repeating-characters/) | 🟠 |
| [438. Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/) | [438. Find All Anagrams in a String](https://leetcode.cn/problems/find-all-anagrams-in-a-string/) | 🟠 |
| [567. Permutation in String](https://leetcode.com/problems/permutation-in-string/) | [567. Permutation in String](https://leetcode.cn/problems/permutation-in-string/) | 🟠 |
| [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) | [76. Minimum Window Substring](https://leetcode.cn/problems/minimum-window-substring/) | 🔴 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Array Basics](https://labuladong.online/algo/data-structure-basic/array-basic/)

For fast/slow and left/right two-pointer techniques, see [Two-Pointer Tricks Summary](https://labuladong.online/algo/essential-technique/array-two-pointers-summary/). This article tackles the toughest two-pointer pattern: the sliding window. With the framework below, you'll write correct solutions in your sleep.

## Sliding Window Framework Overview

**Sliding window mainly handles subarray problems — find the longest/shortest subarray meeting some condition**.

Brute force is nested for loops over all subarrays — $O(N^2)$:

```java
for (int i = 0; i < nums.length; i++) {
    for (int j = i; j < nums.length; j++) {
        // nums[i, j] is a subarray
    }
}
```

The sliding-window idea isn't hard: maintain a window that slides, updating the answer:

```java
int left = 0, right = 0;

while (right < nums.size()) {
    // Expand the window
    window.addLast(nums[right]);
    right++;
    
    while (window needs shrink) {
        // Shrink the window
        window.removeFirst(nums[left]);
        left++;
    }
}
```

Code based on the sliding-window framework runs in $O(N)$, faster than the brute force.

::: info Why $O(N)$?

You may ask: a nested while loop too — why is it $O(N)$?

Both pointers `left` and `right` only increase, never decrease. Each element enters and leaves the window once. Total work is proportional to the array length.

In the brute force, `j` resets — elements re-enter the "window" multiple times — hence $O(N^2)$.

[Practical Time/Space Complexity Analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/) explains how to estimate this from theory.

:::

::: info How can sliding window enumerate subarrays in $O(N)$?

The question is wrong — **sliding window doesn't enumerate all substrings**. To do that you need the nested for loop.

But for many problems you don't need to enumerate all substrings to find the answer. Sliding window is a template for those scenarios — it prunes redundant work.

In [The Essence of Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/) I bucket sliding window under "smart enumeration".

:::

What confuses people isn't the idea but the details: how to add elements, when to shrink, when to update the answer. Even understanding details, bugs creep in and are hard to find.

**So here's a sliding-window framework with debug points marked. Memorize it; for similar problems, only three places need changes — bug-free**.

Most examples here are substring problems. Strings are essentially arrays, so the input is a string. Adapt as needed:

```java
// Sliding-window framework (pseudocode)
void slidingWindow(String s) {
    // Choose a data structure for the window's data, depending on the problem
    // E.g., to track frequency, use a map
    // To track sum, use an int
    Object window = ...
    
    int left = 0, right = 0;
    while (right < s.length()) {
        // c is the character entering the window
        char c = s[right];
        window.add(c)
        // Expand the window
        right++;
        // Update the window's data
...

        // *** debug print location ***
        // Don't print in your final solution — IO is slow and may TLE
        printf("window: [%d, %d)\n", left, right);
        // ***********************

        // Should the left side shrink?
        while (left < right && window needs shrink) {
            // d is the character leaving the window
            char d = s[left];
            window.remove(d)
            // Shrink the window
            left++;
            // Update the window's data
...
        }
    }
}
```

**The two `...` slots are where you fill in window-update logic. Notice the two updates (entering vs leaving) are completely symmetric.**

Some readers comment that hash maps are slow and arrays would be faster, or that the code is too verbose. My take: focus on time complexity. As long as it's optimal, LeetCode runtimes are noisy and not worth micro-optimizing. Don't lose sight of the goal.

The point is the algorithmic thinking — internalize the framework first; tweak as you like.

Now four LeetCode problems applied to the framework — the first explained in detail; the rest should fall out instantly.


## 1. Minimum Window Substring

LeetCode 76 "Minimum Window Substring" (Hard):

<Problem slug="minimum-window-substring" />

Find the shortest substring of `S` (source) containing all characters in `T` (target).

Brute force:

```java
for (int i = 0; i < s.length(); i++)
    for (int j = i + 1; j < s.length(); j++)
        if s[i:j] contains all letters of t:
            update answer
```

Direct, but worse than O(N²).

**Sliding-window idea**:

1. Use a left/right pointer pair on `S`, with `left = right = 0`. Treat `[left, right)` as the **left-closed, right-open** window.

::: tip Why "left-closed, right-open"?

**You can pick any kind of interval, but `[a, b)` is the most convenient.**

With `left = right = 0`, `[0, 0)` is empty; advancing `right` to `[0, 1)` includes one element.

If both ends were open, `(0, 1)` is still empty; if both ends were closed, `[0, 0]` already has one element. Both make boundary handling annoying.

:::

2. Increase `right` to expand `[left, right)` until the window contains all characters of `T`.

3. Then increase `left` to shrink the window until the window no longer contains all characters of `T`. Each time `left` advances, update the answer.

4. Repeat 2 and 3 until `right` reaches the end of `S`.

**Step 2 finds a "feasible" answer; step 3 optimizes it; in the end you have the minimum**. Pointers alternate; the window stretches and contracts like a caterpillar — hence "sliding window".


Pictures: `needs` and `window` are counters tracking character frequencies in `T` and the window.

Initial:

![](https://labuladong.online/algo/images/slidingwindow/1.png)

Increase `right` until `[left, right)` contains all characters of `T`:

![](https://labuladong.online/algo/images/slidingwindow/2.png)

Increase `left` to shrink:

![](https://labuladong.online/algo/images/slidingwindow/3.png)

Until the window no longer satisfies the condition:

![](https://labuladong.online/algo/images/slidingwindow/4.png)

Repeat: move `right`, then `left`, until `right` reaches the end.

If you understand this, you've fully grasped sliding window. **Now the framework**:

Initialize `window` and `need` to track window characters and required characters:

```java
// Frequencies in the window
HashMap<Character, Integer> window = new HashMap<>();
// Required frequencies
HashMap<Character, Integer> need = new HashMap<>();
for (int i = 0; i < t.length(); i++) {
    char c = t.charAt(i);
    need.put(c, need.getOrDefault(c, 0) + 1);
}
```

Initialize the two ends. `[left, right)` is left-closed, right-open, so initially the window is empty:

```java
int left = 0, right = 0;
int valid = 0;
while (right < s.length()) {
    // c is the character entering the window
    char c = s.charAt(right);
    // Move right
    right++;
    // Update the window's data
...
}
```

**`valid` counts how many characters in the window already satisfy `need`**; if `valid == need.size()`, the window covers `T`.

**Now apply the template by answering**:

1. When to move `right` (expand)? What to update on entry?

2. When to stop expanding and move `left` (shrink)? What to update on exit?

3. Where to update the answer — on expand or shrink?

Entering: increment `window`. Leaving: decrement `window`. Shrink when `valid` matches `need`. Update the answer during shrink.

Full code:

```java
class Solution {
    public String minWindow(String s, String t) {
        Map<Character, Integer> need = new HashMap<>();
        Map<Character, Integer> window = new HashMap<>();
        for (char c : t.toCharArray()) {
            need.put(c, need.getOrDefault(c, 0) + 1);
        }

        int left = 0, right = 0;
        int valid = 0;
        // Track the start index and length of the minimum window
        int start = 0, len = Integer.MAX_VALUE;
        while (right < s.length()) {
            // c is entering the window
            char c = s.charAt(right);
            // Expand
            right++;
            // Update window
            if (need.containsKey(c)) {
                window.put(c, window.getOrDefault(c, 0) + 1);
                if (window.get(c).equals(need.get(c)))
                    valid++;
            }

            // Should we shrink?
            while (valid == need.size()) {
                // Update minimum window
                if (right - left < len) {
                    start = left;
                    len = right - left;
                }
                // d is leaving
                char d = s.charAt(left);
                // Shrink
                left++;
                // Update window
                if (need.containsKey(d)) {
                    if (window.get(d).equals(need.get(d)))
                        valid--;
                    window.put(d, window.get(d) - 1);
                }                    
            }
        }
        return len == Integer.MAX_VALUE ? "" : s.substring(start, start + len);
    }
}
```

<visual slug='minimum-window-substring' >

Click the visualization below; click <code type="click">while (right < s.length)</code> repeatedly to see the sliding window:

</visual>

::: warning Note for Java readers

When comparing wrapper types (e.g., `Integer`, `String`), use `equals` instead of `==`, or you'll get bugs. So when shrinking, write `window.get(d).equals(need.get(d))`, not `window.get(d) == need.get(d)`. Same for later code.

:::

Whenever a character's count in `window` reaches its `need` count, increment `valid`. The two updates are symmetric.

When `valid == need.size()`, all characters of `T` are covered — start shrinking to find the minimum.

While shrinking, every window in this state is a feasible answer — update the minimum during shrinking.

Algorithm done. **For sliding-window problems, write this code, change three things — bug-free**.

Now apply it to the next problems.


## 2. Permutation in String

LeetCode 567 "Permutation in String" (Medium):

<Problem slug="permutation-in-string" />

`s1` may contain duplicates — non-trivial.

This is sliding window: **given `S` and `T`, does `S` contain a substring with exactly `T`'s character multiset and nothing else?**

Copy the framework, answer the questions:

```java
class Solution {
    // Whether s contains a permutation of t
    public boolean checkInclusion(String t, String s) {
        Map<Character, Integer> need = new HashMap<>();
        Map<Character, Integer> window = new HashMap<>();
        for (char c : t.toCharArray()) {
            need.put(c, need.getOrDefault(c, 0) + 1);
        }

        int left = 0, right = 0;
        int valid = 0;
        while (right < s.length()) {
            char c = s.charAt(right);
            right++;
            // Update window
            if (need.containsKey(c)) {
                window.put(c, window.getOrDefault(c, 0) + 1);
                if (window.get(c).intValue() == need.get(c).intValue())
                    valid++;
            }

            // Shrink?
            while (right - left >= t.length()) {
                // Found a valid window?
                if (valid == need.size())
                    return true;
                char d = s.charAt(left);
                left++;
                // Update window
                if (need.containsKey(d)) {
                    if (window.get(d).intValue() == need.get(d).intValue())
                        valid--;
                    window.put(d, window.get(d) - 1);
                }
            }
        }
        // Not found
        return false;
    }
}
```

<visual slug='permutation-in-string' >

Click <code type="click">while (right < s.length)</code> repeatedly to see the fixed-length window slide:

</visual>

Differences from minimum window substring:

1. Shrink when window size exceeds `t.length()` — permutations must have equal length.

2. When `valid == need.size()` the window is a permutation; return true.

> [!NOTE]
> The window is **fixed length** `t.length()`. Each step shifts a single character; the inner `while` could be `if` with the same effect.


## 3. Find All Anagrams

LeetCode 438 "Find All Anagrams in a String" (Medium):

<Problem slug="find-all-anagrams-in-a-string" />

"Anagrams" are just permutations under a fancy name. **Given `S` and `T`, find all start indices in `S` where a permutation of `T` occurs.**

Just template + adjustments:

```java
class Solution {
    public List<Integer> findAnagrams(String s, String t) {
        Map<Character, Integer> need = new HashMap<>();
        Map<Character, Integer> window = new HashMap<>();
        for (char c : t.toCharArray()) {
            need.put(c, need.getOrDefault(c, 0) + 1);
        }

        int left = 0, right = 0;
        int valid = 0;
        // Result
        List<Integer> res = new ArrayList<>();
        while (right < s.length()) {
            char c = s.charAt(right);
            right++;
            // Update window
            if (need.containsKey(c)) {
                window.put(c, window.getOrDefault(c, 0) + 1);
                if (window.get(c).equals(need.get(c))) {
                    valid++;
                }
            }
            // Shrink?
            while (right - left >= t.length()) {
                // Window is valid — record start index
                if (valid == need.size())
                    res.add(left);
                char d = s.charAt(left);
                left++;
                // Update window
                if (need.containsKey(d)) {
                    if (window.get(d).equals(need.get(d))) {
                        valid--;
                    }
                    window.put(d, window.get(d) - 1);
                }
            }
        }
        return res;
    }
}
```

Same as the permutation problem; just record start indices when a valid window is found.

<visual slug='find-all-anagrams-in-a-string' >

Click <code type="click">while (right < s.length)</code> repeatedly to see the fixed-length window slide:

</visual>

## 4. Longest Substring Without Repeating Characters

LeetCode 3 (Medium):

<Problem slug="longest-substring-without-repeating-characters" />

A bit different — slightly simpler:

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        Map<Character, Integer> window = new HashMap<>();
        int left = 0, right = 0;
        // Result
        int res = 0;
        while (right < s.length()) {
            char c = s.charAt(right);
            right++;
            // Update window
            window.put(c, window.getOrDefault(c, 0) + 1);
            // Shrink?
            while (window.get(c) > 1) {
                char d = s.charAt(left);
                left++;
                // Update window
                window.put(d, window.get(d) - 1);
            }
            // Update answer here
            res = Math.max(res, right - left);
        }
        return res;
    }
}
```

<visual slug='longest-substring-without-repeating-characters' >

Click <code type="click">while (right < s.length)</code> repeatedly to see the window slide and the answer update:

</visual>

Even simpler — no `need`, no `valid`; just the counter `window`.

When `window[c] > 1`, the window has a duplicate; shrink.

Where do we update `res`? We want the longest unique-character substring. After shrinking, the window has no duplicates — so update `res` after shrinking.

That's the sliding-window template. To recap, when you see a subarray/substring problem, ask:

1. When to expand?

2. When to shrink?

3. When to update the answer?

[Sliding Window Practice](https://labuladong.online/algo/problem-set/sliding-window/) has more classic problems for reinforcement.


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Classic Prefix-Sum Problems](https://labuladong.online/algo/problem-set/perfix-sum/)
 - [[Practice] General Monotonic-Queue Implementation and Classic Problems](https://labuladong.online/algo/problem-set/monotonic-queue/)
 - [[Practice] Classic Sliding-Window Problems](https://labuladong.online/algo/problem-set/sliding-window/)
 - [DP Design: Maximum Subarray](https://labuladong.online/algo/dynamic-programming/maximum-subarray/)
 - [Solving Sliding-Window Problems with the Monotonic Queue](https://labuladong.online/algo/data-structure/monotonic-queue/)
 - [Two-Pointer Tricks Sweep 7 Array Problems](https://labuladong.online/algo/essential-technique/array-two-pointers-summary/)
 - [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Extension: Merge Sort Details and Applications](https://labuladong.online/algo/practice-in-action/merge-sort/)
 - [Sliding-Window Extension: Rabin-Karp String Matching](https://labuladong.online/algo/practice-in-action/rabinkarp/)
 - [Circular Array Tricks](https://labuladong.online/algo/data-structure-basic/cycle-array/)
 - [Key Points and Pitfalls in Practice](https://labuladong.online/algo/intro/how-to-learn-algorithms/)
 - [Practical Guide to Time/Space Complexity Analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1004. Max Consecutive Ones III](https://leetcode.com/problems/max-consecutive-ones-iii/?show=1) | [1004. Max Consecutive Ones III](https://leetcode.cn/problems/max-consecutive-ones-iii/?show=1) | 🟠 |
| [1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit](https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/?show=1) | [1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit](https://leetcode.cn/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/?show=1) | 🟠 |
| [1658. Minimum Operations to Reduce X to Zero](https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/?show=1) | [1658. Minimum Operations to Reduce X to Zero](https://leetcode.cn/problems/minimum-operations-to-reduce-x-to-zero/?show=1) | 🟠 |
| [209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/?show=1) | [209. Minimum Size Subarray Sum](https://leetcode.cn/problems/minimum-size-subarray-sum/?show=1) | 🟠 |
| [219. Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/?show=1) | [219. Contains Duplicate II](https://leetcode.cn/problems/contains-duplicate-ii/?show=1) | 🟢 |
| [220. Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/?show=1) | [220. Contains Duplicate III](https://leetcode.cn/problems/contains-duplicate-iii/?show=1) | 🔴 |
| [340. Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/?show=1)🔒 | [340. Longest Substring with At Most K Distinct Characters](https://leetcode.cn/problems/longest-substring-with-at-most-k-distinct-characters/?show=1)🔒 | 🟠 |
| [395. Longest Substring with At Least K Repeating Characters](https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/?show=1) | [395. Longest Substring with At Least K Repeating Characters](https://leetcode.cn/problems/longest-substring-with-at-least-k-repeating-characters/?show=1) | 🟠 |
| [424. Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/?show=1) | [424. Longest Repeating Character Replacement](https://leetcode.cn/problems/longest-repeating-character-replacement/?show=1) | 🟠 |
| [560. Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/?show=1) | [560. Subarray Sum Equals K](https://leetcode.cn/problems/subarray-sum-equals-k/?show=1) | 🟠 |
| [713. Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/?show=1) | [713. Subarray Product Less Than K](https://leetcode.cn/problems/subarray-product-less-than-k/?show=1) | 🟠 |
| [862. Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/?show=1) | [862. Shortest Subarray with Sum at Least K](https://leetcode.cn/problems/shortest-subarray-with-sum-at-least-k/?show=1) | 🔴 |
| - | [Sword to Offer 48. Longest Substring Without Repeating Characters](https://leetcode.cn/problems/zui-chang-bu-han-zhong-fu-zi-fu-de-zi-zi-fu-chuan-lcof/?show=1) | 🟠 |
| - | [Sword to Offer 57 - II. Continuous Sequence with Sum s](https://leetcode.cn/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/?show=1) | 🟢 |
| - | [Sword to Offer II 008. Shortest Subarray with Sum >= target](https://leetcode.cn/problems/2VG8Kg/?show=1) | 🟠 |
| - | [Sword to Offer II 009. Subarray Product < K](https://leetcode.cn/problems/ZVAVXX/?show=1) | 🟠 |
| - | [Sword to Offer II 010. Subarray Sum Equals k](https://leetcode.cn/problems/QTMn0o/?show=1) | 🟠 |
| - | [Sword to Offer II 014. Anagrams in a String](https://leetcode.cn/problems/MPnaiL/?show=1) | 🟠 |
| - | [Sword to Offer II 015. All Anagrams in a String](https://leetcode.cn/problems/VabMRr/?show=1) | 🟠 |
| - | [Sword to Offer II 016. Longest Substring Without Repeating Characters](https://leetcode.cn/problems/wtcaE1/?show=1) | 🟠 |
| - | [Sword to Offer II 017. Shortest String Containing All Characters](https://leetcode.cn/problems/M1oyTv/?show=1) | 🔴 |
| - | [Sword to Offer II 057. Indices Within a Range](https://leetcode.cn/problems/7WqeDu/?show=1) | 🟠 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
