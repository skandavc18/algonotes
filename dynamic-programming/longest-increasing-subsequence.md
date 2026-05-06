# DP design: longest increasing subsequence


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) | [300. Longest Increasing Subsequence](https://leetcode.cn/problems/longest-increasing-subsequence/) | 🟠 |
| [354. Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/) | [354. Russian Doll Envelopes](https://leetcode.cn/problems/russian-doll-envelopes/) | 🔴 |

**-----------**


> [!NOTE]
> Before reading, you should first study:
> 
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)


Some readers may have read the [DP framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/) and learned the routine: identify the problem's "state", clarify the meaning of `dp` array/function, and define the base case. But they don't know how to identify the "choice" — i.e. the state-transition relation — and still can't write a DP solution. What now?

Don't worry — the hard part of DP is finding the right state-transition equation. This article uses the classic "longest increasing subsequence" problem to teach a general DP-design technique: **mathematical induction**.

The longest increasing subsequence (LIS) is a very classic problem. The DP solution comes to mind relatively easily, with O(N^2) time complexity. We'll use this problem to walk through finding state transitions and writing DP solutions step by step. The harder-to-think-of solution uses binary search at O(N log N); we'll use a simple card game to help understand that clever solution.

LeetCode 300 "Longest Increasing Subsequence" is exactly this problem:


<Problem slug="longest-increasing-subsequence"/>

```java
// function signature
int lengthOfLIS(int[] nums);
```

For `nums = [10,9,2,5,3,7,101,18]`, the longest increasing subsequence is `[2,3,7,101]`, so the algorithm should return 4.

Note the difference between "subsequence" and "substring": a substring must be contiguous; a subsequence may not be. Let's design a DP solution first.

## 1. DP solution

The core of DP design is mathematical induction.

Mathematical induction should be familiar — we learned it in high school. Idea is simple: **assume the conclusion holds for `k < n`, then derive that it also holds for `k = n`**. If that derivation works, the conclusion holds for any `k`.

Similarly, when designing DP, we have a dp array. We can assume `dp[0...i-1]` are already computed and ask: how do we compute `dp[i]` from these results?

Let's see this directly with the LIS problem. First, define `dp` clearly: what does `dp[i]` actually represent?

**Our definition: `dp[i]` is the length of the longest increasing subsequence ending at `nums[i]`**.

> [!NOTE]
> Why this definition? It's a routine for subsequence problems; the later post [DP template for subsequence problems](https://labuladong.online/algo/dynamic-programming/subsequence-problem/) summarizes several common patterns. After reading all the DP problems in this chapter, you'll find there are only a few canonical `dp` definitions.

By this definition, the base case is: `dp[i]` initialized to 1, since the longest increasing subseq ending at `nums[i]` at minimum contains itself.

Two examples:

![](https://labuladong.online/algo/images/lis/8.jpeg)

This GIF shows the algorithm's evolution:

![](https://labuladong.online/algo/images/lis/gif1.gif)

By this definition, the final result (the maximum subsequence length) is the maximum entry in the dp array.


```java
int res = 0;
for (int i = 0; i < dp.length; i++) {
    res = Math.max(res, dp[i]);
}
return res;
```


Readers may ask: in the algorithm trace, we eyeballed each `dp[i]`. How do we design the algorithm to compute each `dp[i]` correctly?

This is the crux of DP — how to design state-transition logic for correct execution. This is where mathematical induction helps:

**Suppose we already know all of `dp[0..4]`. How do we derive `dp[5]`?**

![](https://labuladong.online/algo/images/lis/6.jpeg)

By our `dp` definition, computing `dp[5]` means computing the longest increasing subseq ending at `nums[5]`.

**`nums[5] = 3`. Since this is an increasing subseq, just find earlier subsequences that end with a value smaller than 3, then append 3 to those subsequences — that forms a new increasing subseq with length one more**.

Which elements before `nums[5]` are smaller than `nums[5]`? Easy — a for-loop comparison finds them.

What's the longest-increasing-subseq length for those elements as endings? Recall the `dp` definition — that's exactly what `dp` records.

In our example, `nums[0]` and `nums[4]` are both less than `nums[5]`. Comparing `dp[0]` and `dp[4]`, we attach `nums[5]` to the longer subseq, yielding `dp[5] = 3`:


![](https://labuladong.online/algo/images/lis/7.jpeg)

```java
for (int j = 0; j < i; j++) {
    if (nums[i] > nums[j]) {
        dp[i] = Math.max(dp[i], dp[j] + 1);
    }
}
```


When `i = 5`, this code computes `dp[5]`. We're basically done with this problem.

Readers may ask: we only computed `dp[5]`. What about `dp[4]`, `dp[3]`, etc.? Like induction — if you can compute `dp[5]`, you can compute all the others:


```java
for (int i = 0; i < nums.length; i++) {
    for (int j = 0; j < i; j++) {
        // find elements in nums[0..i-1] smaller than nums[i]
        if (nums[i] > nums[j]) {
            // append nums[i] after them, forming an increasing subseq
            // of length dp[j] + 1 ending at nums[i]
            dp[i] = Math.max(dp[i], dp[j] + 1);
        }
    }
}
```


With our base case, the complete code:

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        // definition: dp[i] = length of LIS ending at nums[i]
        int[] dp = new int[nums.length];
        // base case: initialize all dp entries to 1
        Arrays.fill(dp, 1);
        for (int i = 0; i < nums.length; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
        }
        
        int res = 0;
        for (int i = 0; i < dp.length; i++) {
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/longest-increasing-subsequence/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Code visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>


That solves it, in $O(N^2)$. Summary of how to find the DP state-transition relation:

1. Clearly define the `dp` array. This is critical for any DP problem; an inadequate or unclear definition blocks subsequent steps.

2. Per the `dp` definition, use induction: assume `dp[0...i-1]` are known; figure out `dp[i]`. Once this step is done, the problem is essentially solved.

If you can't complete this step, the `dp` definition is probably inadequate — redefine it; or maybe `dp` doesn't store enough info to derive the next answer — extend `dp` to 2D or even 3D.

The current solution is standard DP, but for LIS this isn't optimal — it may not pass all test cases. Let's discuss a more efficient solution.


## 2. Binary-search solution

This solution runs in $O(N \log N)$, but honestly, normal people basically don't think of it (maybe people who've played certain card games can). So just be aware of it; in normal interviews, giving the DP solution is already pretty good.

It's hard to imagine LIS could be related to binary search. In fact, LIS connects to a card game called patience game, and there's even a sorting method called patience sorting.

For simplicity, we'll skip all math proofs and use a simplified example to understand the algorithm.

First, given a row of cards. Like iterating an array, we go through them left to right and finally divide them into several piles.

![](https://labuladong.online/algo/images/lis/poker1.jpeg)


**The rules for handling the cards**:

You can only place a smaller-rank card on top of a larger-rank card; if the current card has a large rank and no pile to place on, start a new pile and put the card there; if the current card can go on multiple piles, place it on the leftmost.

For example, the cards above end up split into 5 piles (treating ace as the highest, 2 as the lowest).

![](https://labuladong.online/algo/images/lis/poker2.jpeg)

Why place on the leftmost when multiple piles are available? Because that way the pile-tops stay in sorted order (2, 4, 7, 8, Q). Proof omitted.

![](https://labuladong.online/algo/images/lis/poker3.jpeg)

Following these rules, you can compute the LIS — the number of piles equals the LIS length. Proof omitted.

![](https://labuladong.online/algo/images/lis/poker4.jpeg)

We just need to code the card-handling process. For each card, we look for a suitable pile-top to place on; the pile-tops are **sorted**, which is where binary search comes in: search for where the current card should be placed.

> [!TIP]
> The earlier post [Binary search detailed](https://labuladong.online/algo/essential-technique/binary-search-framework/) covers binary search and its variants in detail — it's a perfect application here. Highly recommended if you haven't read it.

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int[] top = new int[nums.length];
        // initialize the number of piles to 0
        int piles = 0;
        for (int i = 0; i < nums.length; i++) {
            // the playing card to handle
            int poker = nums[i];

            // ***** binary search for the left boundary *****
            int left = 0, right = piles;
            while (left < right) {
                int mid = (left + right) / 2;
                if (top[mid] > poker) {
                    right = mid;
                } else if (top[mid] < poker) {
                    left = mid + 1;
                } else {
                    right = mid;
                }
            }
            // *********************************
            
            // no suitable pile found - start a new pile
            if (left == piles) piles++;
            // place this card on top of the pile
            top[left] = poker;
        }
        // the number of piles equals the LIS length
        return piles;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/mydata-lis/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Code visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>


That covers the binary-search solution.

It's genuinely hard to come up with. First the math proof — who'd think these rules give the LIS? Then the binary search itself; without firm grasp of the details, even with the hint it's hard to write right.

So treat this as a thinking-expansion exercise. But fully understand the DP design technique: assume previous answers known and use induction to correctly derive state transitions.


## 3. Extension to 2D

Let's look at a fun problem that often shows up in life — LeetCode 354 "Russian Doll Envelopes":

<Problem slug="russian-doll-envelopes" />

**This problem is a variant of LIS, because each valid nesting puts a smaller envelope inside a larger one — equivalent to finding the longest increasing subseq in a 2D plane, whose length is the maximum number of nested envelopes**.

Standard LIS finds the longest subseq in a 1D array, but our envelopes are 2D pairs `(w, h)`. How do we apply LIS?

![](https://labuladong.online/algo/images/nest-envelope/0.jpg)

Readers may think: compute area `w × h` and run standard LIS on the areas. With a moment's thought, this fails. E.g. `1 × 10` is bigger than `3 × 3`, but obviously these can't nest each other.


The trick:

**First sort by width `w` ascending; on equal `w`, sort by height `h` descending; then take all the `h`'s as an array and compute LIS on that — that's the answer**.

Visualize. First sort the pairs:

![](https://labuladong.online/algo/images/nest-envelope/1.jpg)

Then find the LIS in the `h`'s — that subseq is the optimal nesting plan:

![](https://labuladong.online/algo/images/nest-envelope/2.jpg)

**Why does this find a sequence of mutually nestable envelopes?** A moment's thought:

First, sorting `w` ascending ensures the `w` dimension is mutually nestable, so we only need to focus on `h` being mutually nestable.

Second, two equal-`w` envelopes can't nest each other; for envelopes with the same `w`, sort `h` descending — so the 2D LIS won't include multiple envelopes with the same `w` (the problem says equal dimensions can't nest).

Solution code:

```java
class Solution {
    // envelopes = [[w, h], [w, h]...]
    public int maxEnvelopes(int[][] envelopes) {
        int n = envelopes.length;
        // sort by width ascending; on equal widths, by height descending
        Arrays.sort(envelopes, (int[] a, int[] b) -> {
            return a[0] == b[0] ? 
                b[1] - a[1] : a[0] - b[0];
        });
        // find LIS on the height array
        int[] height = new int[n];
        for (int i = 0; i < n; i++)
            height[i] = envelopes[i][1];

        return lengthOfLIS(height);
    }

    int lengthOfLIS(int[] nums) {
        // see above
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/russian-doll-envelopes/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌟 Code visualization 🌟</strong>
</summary>
</details>
</a>
<hr/>


To reuse the previous function, I split into two functions; you could merge them and save the `height` array space.

Because the test cases are large, you must use the binary-search version of `lengthOfLIS` to pass them all. Total time complexity is $O(N \log N)$ — sorting and LIS each take $O(N \log N)$, so combined still $O(N \log N)$. Space is $O(N)$ since LIS needs a `top` array.


<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [Generic monotonic-queue implementation and classic problems](https://labuladong.online/algo/problem-set/monotonic-queue/)
 - [DP template for subsequence problems](https://labuladong.online/algo/dynamic-programming/subsequence-problem/)
 - [Two perspectives on DP enumeration](https://labuladong.online/algo/dynamic-programming/two-views-of-dp/)
 - [DP design: maximum subarray](https://labuladong.online/algo/dynamic-programming/maximum-subarray/)
 - [The framework mindset for learning data structures and algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Optimal substructure and dp-array traversal direction](https://labuladong.online/algo/dynamic-programming/faq-summary/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems citing this article</strong></summary>

<strong>Install [my Chrome extension](https://labuladong.online/algo/intro/chrome/) and click any problem below to view its solution outline:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1425. Constrained Subsequence Sum](https://leetcode.com/problems/constrained-subsequence-sum/?show=1) | [1425. Constrained Subsequence Sum](https://leetcode.cn/problems/constrained-subsequence-sum/?show=1) | 🔴 |
| [256. Paint House](https://leetcode.com/problems/paint-house/?show=1)🔒 | [256. Paint House](https://leetcode.cn/problems/paint-house/?show=1)🔒 | 🟠 |
| [368. Largest Divisible Subset](https://leetcode.com/problems/largest-divisible-subset/?show=1) | [368. Largest Divisible Subset](https://leetcode.cn/problems/largest-divisible-subset/?show=1) | 🟠 |
| - | [Sword to Offer II 091. Paint House](https://leetcode.cn/problems/JEj789/?show=1) | 🟠 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

