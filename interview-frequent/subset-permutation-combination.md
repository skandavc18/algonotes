# Backtracking Sweeps All Permutation/Combination/Subset Problems



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [216. Combination Sum III](https://leetcode.com/problems/combination-sum-iii/) | [216. Combination Sum III](https://leetcode.cn/problems/combination-sum-iii/) | 🟠 |
| [39. Combination Sum](https://leetcode.com/problems/combination-sum/) | [39. Combination Sum](https://leetcode.cn/problems/combination-sum/) | 🟠 |
| [40. Combination Sum II](https://leetcode.com/problems/combination-sum-ii/) | [40. Combination Sum II](https://leetcode.cn/problems/combination-sum-ii/) | 🟠 |
| [46. Permutations](https://leetcode.com/problems/permutations/) | [46. Permutations](https://leetcode.cn/problems/permutations/) | 🟠 |
| [47. Permutations II](https://leetcode.com/problems/permutations-ii/) | [47. Permutations II](https://leetcode.cn/problems/permutations-ii/) | 🟠 |
| [77. Combinations](https://leetcode.com/problems/combinations/) | [77. Combinations](https://leetcode.cn/problems/combinations/) | 🟠 |
| [78. Subsets](https://leetcode.com/problems/subsets/) | [78. Subsets](https://leetcode.cn/problems/subsets/) | 🟠 |
| [90. Subsets II](https://leetcode.com/problems/subsets-ii/) | [90. Subsets II](https://leetcode.cn/problems/subsets-ii/) | 🟠 |
| - | [Sword to Offer II 082. Combinations with Duplicates](https://leetcode.cn/problems/4sjJUc/) | 🟠 |
| - | [Sword to Offer II 084. Permutations with Duplicates](https://leetcode.cn/problems/7p8L0Z/) | 🟠 |

**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Binary Tree Algorithm Outline](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
> - [Backtracking Framework](https://labuladong.online/algo/essential-technique/backtrack-framework/)

> tip: a video version is available: [Backtracking Sweeps Permutation/Combination/Subset Problems](https://www.bilibili.com/video/BV1Yt4y1t7dK/). Follow my Bilibili account; I'll guide you through harder algorithm techniques on video.



Permutations, combinations, and subsets are familiar from high school. But programming them tests algorithmic thinking. This article gives the core idea for variants you'll encounter.

These problems all "pick certain elements from `nums` per a rule". Three variants:

**Form 1: distinct elements, pick each at most once (the basic case).**

E.g., for `nums = [2,3,6,7]`, combinations summing to 7: only `[7]`.

**Form 2: duplicates allowed in `nums`; each element used at most once.**

For `nums = [2,5,2,1,2]`, combinations summing to 7: `[2,2,2,1]` and `[5,2]`.

**Form 3: distinct elements, each can be used multiple times.**

For `nums = [2,3,6,7]`, combinations summing to 7: `[2,2,3]` and `[7]`.

A "fourth form" — duplicates plus reuse — reduces to Form 3 (just deduplicate `nums`).

These three forms apply equally to permutations, combinations, and subsets — 9 variants total.

The problem can also add constraints (e.g., size = `k`), creating more variants.

**No matter the form, it's brute-force enumeration over a tree. The backtracking framework handles them all with small tweaks.**

Read [Backtracking Framework](https://labuladong.online/algo/essential-technique/backtrack-framework/) first, then memorize these two trees:

![](https://labuladong.online/algo/images/permutation/1.jpeg)

![](https://labuladong.online/algo/images/permutation/2.jpeg)

Why only two? **Combinations and subsets are equivalent (covered later); the three forms are just additions/removals of branches.**

We'll go through all 9 variants below.

> [!NOTE]
> Some readers may have seen alternative permutation/combination/subset code (using `swap`). Backtracking has two perspectives — see [Box-and-Ball Model: Two Backtracking Perspectives](https://labuladong.online/algo/practice-in-action/two-views-of-backtrack/). Stick with this article for now.







## Subsets (Distinct, No Reuse)

LeetCode 78 "Subsets":

Given `nums` with distinct elements, return all subsets.

```java
List<List<Integer>> subsets(int[] nums)
```

For `nums = [1,2,3]`:

```java
[ [],[1],[2],[3],[1,2],[1,3],[2,3],[1,2,3] ]
```

Forget code for a moment — recall from high school how to enumerate subsets.

`S_0` = subsets of size 0 = `[]`.

`S_1` = subsets of size 1, derived from `S_0`:

![](https://labuladong.online/algo/images/permutation/3.jpeg)

`S_2` from `S_1`:

![](https://labuladong.online/algo/images/permutation/4.jpeg)

Why does `[2]` only extend with 3, not 1? Subsets are unordered: `[2,1]` would duplicate `[1,2]` we already have.

**We enforce a relative order on elements to prevent duplicates.**

`S_3` from `S_2` — only `[1,2,3]`, derived from `[1,2]`.

The whole derivation is a tree:

![](https://labuladong.online/algo/images/permutation/5.jpeg)

Properties:

**With root at level 0, edge labels representing each node's value, level `n` contains all subsets of size `n`.**

E.g., size-2 subsets are at level 2:

![](https://labuladong.online/algo/images/permutation/6.jpeg)

> [!NOTE]
> "Node value" here means the path from root to that node; the root is level 0.

Collect all node values to get all subsets:

```java
class Solution {

    List<List<Integer>> res = new LinkedList<>();
    // Recursion path
    LinkedList<Integer> track = new LinkedList<>();

    public List<List<Integer>> subsets(int[] nums) {
        backtrack(nums, 0);
        return res;
    }

    // Traverse the subset tree
    void backtrack(int[] nums, int start) {

        // Preorder: each node's value is a subset
        res.add(new LinkedList<>(track));
        
        // Backtracking framework
        for (int i = start; i < nums.length; i++) {
            // Choose
            track.addLast(nums[i]);
            // start prevents duplicate subsets
            backtrack(nums, i + 1);
            // Undo
            track.removeLast();
        }
    }
}
```

`start` controls branch growth; `track` accumulates path values; preorder collects every node:

![](https://labuladong.online/algo/images/permutation/5.jpeg)

`backtrack` looks base-case-less, but when `start == nums.length` the for loop doesn't execute, ending recursion.






<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/subsets/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Animated Code Visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>



## Combinations (Distinct, No Reuse)

If subsets are sorted by size, level `k` is the combinations of size `k`. **Combinations of size `k` = subsets of size `k`.**

LeetCode 77 "Combinations":

```java
List<List<Integer>> combine(int n, int k)
```

`combine(3, 2)` →

```java
[ [1,2],[1,3],[2,3] ]
```

A standard combination problem; rephrased as subsets:

**Given `nums = [1,2,...,n]` and `k`, generate all subsets of size `k`.**

For `nums = [1,2,3]`, level 2 of the subset tree:

![](https://labuladong.online/algo/images/permutation/6.jpeg)

Adjust the base case to collect only level-`k` nodes:

```java
class Solution {

    List<List<Integer>> res = new LinkedList<>();
    LinkedList<Integer> track = new LinkedList<>();

    public List<List<Integer>> combine(int n, int k) {
        backtrack(1, n, k);
        return res;
    }

    void backtrack(int start, int n, int k) {
        // base case
        if (k == track.size()) {
            // Reached level k; record
            res.add(new LinkedList<>(track));
            return;
        }
        
        for (int i = start; i <= n; i++) {
            // Choose
            track.addLast(i);
            backtrack(i + 1, n, k);
            // Undo
            track.removeLast();
        }
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/combinations/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Animated Code Visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>

## Permutations (Distinct, No Reuse)

Covered in [Backtracking Framework](https://labuladong.online/algo/essential-technique/backtrack-framework/).

LeetCode 46 "Permutations":

```java
List<List<Integer>> permute(int[] nums)
```

For `nums = [1,2,3]`:

```java
[
    [1,2,3],[1,3,2],
    [2,1,3],[2,3,1],
    [3,1,2],[3,2,1]
]
```

For combinations/subsets we used `start` to fix the relative order. For permutations the order matters and we need to permit revisiting elements left of the current position — use a `used` array.

The decision tree:

![](https://labuladong.online/algo/images/permutation/7.jpeg)

`used` marks placed elements; collect leaves:

```java
class Solution {

    List<List<Integer>> res = new LinkedList<>();
    LinkedList<Integer> track = new LinkedList<>();
    boolean[] used;

    public List<List<Integer>> permute(int[] nums) {
        used = new boolean[nums.length];
        backtrack(nums);
        return res;
    }

    void backtrack(int[] nums) {
        // base case: leaf
        if (track.size() == nums.length) {
            // Collect leaf
            res.add(new LinkedList(track));
            return;
        }

        for (int i = 0; i < nums.length; i++) {
            if (used[i]) {
                continue;
            }
            used[i] = true;
            track.addLast(nums[i]);
            backtrack(nums);
            track.removeLast();
            used[i] = false;
        }
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/permutations/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>



For permutations of size `k`, change the base case to collect level `k`:

```java
void backtrack(int[] nums, int k) {
    if (track.size() == k) {
        res.add(new LinkedList(track));
        return;
    }

    for (int i = 0; i < nums.length; i++) {
        // ...
        backtrack(nums, k);
        // ...
    }
}
```

## Subsets/Combinations (Duplicates, No Reuse)

LeetCode 90 "Subsets II":

```java
List<List<Integer>> subsetsWithDup(int[] nums)
```

For `nums = [1,2,2]`:

```java
[ [],[1],[2],[1,2],[2,2],[1,2,2] ]
```

Strictly speaking, sets shouldn't have duplicates, but we'll set that aside.

For `nums = [1,2,2']`, the standard subset tree has duplicates:

![](https://labuladong.online/algo/images/permutation/8.jpeg)

```text
[ 
    [],
    [1],[2],[2'],
    [1,2],[1,2'],[2,2'],
    [1,2,2']
]
```

`[2]` and `[1,2]` repeat. Prune: when a node has multiple equal-valued sibling branches, take only the first:

![](https://labuladong.online/algo/images/permutation/9.jpeg)

**Sort first; when `nums[i] == nums[i-1]`, skip:**

```java
class Solution {

    List<List<Integer>> res = new LinkedList<>();
    LinkedList<Integer> track = new LinkedList<>();

    public List<List<Integer>> subsetsWithDup(int[] nums) {
        // Sort to bring duplicates together
        Arrays.sort(nums);
        backtrack(nums, 0);
        return res;
    }

    void backtrack(int[] nums, int start) {
        // Preorder: collect each node
        res.add(new LinkedList<>(track));
        
        for (int i = start; i < nums.length; i++) {
            // Skip duplicate sibling branches
            if (i > start && nums[i] == nums[i - 1]) {
                continue;
            }
            track.addLast(nums[i]);
            backtrack(nums, i + 1);
            track.removeLast();
        }
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/subsets-ii/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Animated Code Visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>



Same idea: a sort plus a pruning line.

**Combinations and subsets are equivalent**, so let's directly look at LeetCode 40 "Combination Sum II":

Given `candidates` (with duplicates) and `target`, find all combinations summing to `target` using each element at most once.

Restated: subsets of `candidates` summing to `target`.

Add a `trackSum` and update the base case:

```java
class Solution {

    List<List<Integer>> res = new LinkedList<>();
    // Recursion path
    LinkedList<Integer> track = new LinkedList<>();
    // Sum of track
    int trackSum = 0;

    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        if (candidates.length == 0) {
            return res;
        }
        // Sort to bring duplicates together
        Arrays.sort(candidates);
        backtrack(candidates, 0, target);
        return res;
    }

    void backtrack(int[] nums, int start, int target) {
        // base case: target hit
        if (trackSum == target) {
            res.add(new LinkedList<>(track));
            return;
        }
        // base case: overshoot
        if (trackSum > target) {
            return;
        }

        for (int i = start; i < nums.length; i++) {
            // Skip duplicate sibling branches
            if (i > start && nums[i] == nums[i - 1]) {
                continue;
            }
            // Choose
            track.add(nums[i]);
            trackSum += nums[i];
            // Recurse
            backtrack(nums, i + 1, target);
            // Undo
            track.removeLast();
            trackSum -= nums[i];
        }
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/combination-sum-ii/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>



## Permutations (Duplicates, No Reuse)

LeetCode 47 "Permutations II":

```java
List<List<Integer>> permuteUnique(int[] nums)
```

For `nums = [1,2,2]`:

```java
[ [1,2,2],[2,1,2],[2,2,1] ]
```

Solution:

```java
class Solution {

    List<List<Integer>> res = new LinkedList<>();
    LinkedList<Integer> track = new LinkedList<>();
    boolean[] used;

    public List<List<Integer>> permuteUnique(int[] nums) {
        // Sort to bring duplicates together
        Arrays.sort(nums);
        used = new boolean[nums.length];
        backtrack(nums);
        return res;
    }

    void backtrack(int[] nums) {
        if (track.size() == nums.length) {
            res.add(new LinkedList(track));
            return;
        }

        for (int i = 0; i < nums.length; i++) {
            if (used[i]) {
                continue;
            }
            // New pruning: fix relative order of duplicate elements
            if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) {
                continue;
            }
            track.add(nums[i]);
            used[i] = true;
            backtrack(nums);
            track.removeLast();
            used[i] = false;
        }
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/permutations-ii/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>



Two changes from standard permutations: sort and a pruning condition.

The pruning differs from subsets/combinations: we add `!used[i - 1]`.

This needs a bit more explanation. Mark duplicates with primes: `nums = [1,2,2']`. Standard permutations would yield:

```
[
    [1,2,2'],[1,2',2],
    [2,1,2'],[2,2',1],
    [2',1,2],[2',2,1]
]
```

Duplicates exist — `[1,2,2']` and `[1,2',2]` are the same.

**Fix the relative order of duplicates** — say `2` always before `2'`:

```
[ [1,2,2'],[2,1,2'],[2,2',1] ]
```

This is the right answer.

For `nums = [1,2,2',2'']`, fix the order `2 -> 2' -> 2''`.

The standard algorithm produces duplicates because it treats permutations of duplicates as distinct. Fixing the relative order avoids that.

Pruning:

```java
// Fix relative order of duplicate elements
if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) {
    // Skip if previous equal element wasn't used yet
    continue;
}
// Choose nums[i]
```

**For duplicates like `nums = [1,2,2',2'']`: `2'` is only chosen if `2` is already used, and `2''` only after `2'` — fixing the relative order.**

If you change `!used[i - 1]` to `used[i - 1]`, the test cases still pass but the algorithm is slower — it's pruning less.

That logic maintains the order `2'' -> 2' -> 2` (still a fixed order), so it deduplicates, but cuts fewer branches.

For `nums = [2,2',2'']`, the recursion tree:

![](https://labuladong.online/algo/images/permutation/12.jpeg)

Green = traversed; red = pruned. With `!used[i - 1]`:

![](https://labuladong.online/algo/images/permutation/13.jpeg)

With `used[i - 1]`:

![](https://labuladong.online/algo/images/permutation/14.jpeg)

`!used[i - 1]` prunes more — recommended.


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/permutations-ii/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>👾 Animated Code Visualization 👾</strong>
</summary>
</details>
</a>
<hr/>

Another pruning idea readers have suggested:

```java
void backtrack(int[] nums, LinkedList<Integer> track) {
    if (track.size() == nums.length) {
        res.add(new LinkedList(track));
        return;
    }

    // Track previous branch's value
    // Constraints: -10 <= nums[i] <= 10, so -666 is a sentinel
    int prevNum = -666;
    for (int i = 0; i < nums.length; i++) {
        if (used[i]) {
            continue;
        }
        if (nums[i] == prevNum) {
            continue;
        }

        track.add(nums[i]);
        used[i] = true;
        // Record the value used on this branch
        prevNum = nums[i];

        backtrack(nums, track);

        track.removeLast();
        used[i] = false;
    }
}
```

Picture: when a node has equal-valued sibling branches:

![](https://labuladong.online/algo/images/permutation/11.jpeg)

Without pruning, those duplicate branches grow identical subtrees → duplicate permutations. After sorting, equal values are adjacent; tracking the previous branch's value prevents revisiting equal values.

Done.

## Subsets/Combinations (Distinct, Reusable)

LeetCode 39 "Combination Sum":

Given `candidates` (distinct) and `target`, find all combinations summing to `target`. Each element may be reused.

```java
List<List<Integer>> combinationSum(int[] candidates, int target)
```

For `candidates = [1,2,3], target = 3`:

```
[ [1,1,1],[1,2],[3] ]
```

Subsets of `candidates` summing to `target`.

Standard subsets prevent reuse via `start` advancing to `i + 1`:

```java
// No-reuse framework
void backtrack(int[] nums, int start) {
    for (int i = start; i < nums.length; i++) {
        // ...
        backtrack(nums, i + 1);
        // ...
    }
}
```

To allow reuse, use `i` instead of `i + 1`:

```java
// Reusable framework
void backtrack(int[] nums, int start) {
    for (int i = start; i < nums.length; i++) {
        // ...
        backtrack(nums, i);
        // ...
    }
}
```

This allows infinite reuse:

![](https://labuladong.online/algo/images/permutation/10.jpeg)

The tree grows infinitely; we need a base case (e.g., sum overshoot):

```java
class Solution {

    List<List<Integer>> res = new LinkedList<>();
    LinkedList<Integer> track = new LinkedList<>();
    int trackSum = 0;

    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        if (candidates.length == 0) {
            return res;
        }
        backtrack(candidates, 0, target);
        return res;
    }

    void backtrack(int[] nums, int start, int target) {
        // base case: hit
        if (trackSum == target) {
            res.add(new LinkedList<>(track));
            return;
        }
        // base case: overshoot
        if (trackSum > target) {
            return;
        }
        for (int i = start; i < nums.length; i++) {
            // Choose
            trackSum += nums[i];
            track.add(nums[i]);
            // Recurse — same i for reuse
            backtrack(nums, i, target);
            // Undo
            trackSum -= nums[i];
            track.removeLast();
        }
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/combination-sum/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Animated Code Visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>



## Permutations (Distinct, Reusable)

LeetCode doesn't have this directly, but consider: distinct elements, reuse allowed. For `nums = [1,2,3]`, there are 3^3 = 27 permutations:

```java
[
  [1,1,1],[1,1,2],[1,1,3],[1,2,1],[1,2,2],[1,2,3],[1,3,1],[1,3,2],[1,3,3],
  [2,1,1],[2,1,2],[2,1,3],[2,2,1],[2,2,2],[2,2,3],[2,3,1],[2,3,2],[2,3,3],
  [3,1,1],[3,1,2],[3,1,3],[3,2,1],[3,2,2],[3,2,3],[3,3,1],[3,3,2],[3,3,3]
]
```

**The standard permutation algorithm uses `used` to prevent reuse. With reuse, drop `used`:**

```java
class Solution {

    List<List<Integer>> res = new LinkedList<>();
    LinkedList<Integer> track = new LinkedList<>();

    public List<List<Integer>> permuteRepeat(int[] nums) {
        backtrack(nums);
        return res;
    }

    void backtrack(int[] nums) {
        // base case: leaf
        if (track.size() == nums.length) {
            // Collect leaf
            res.add(new LinkedList(track));
            return;
        }

        for (int i = 0; i < nums.length; i++) {
            // Choose
            track.add(nums[i]);
            backtrack(nums);
            // Undo
            track.removeLast();
        }
    }
}
```

That covers all 9 variants.

## Wrap-Up

Recap of templates by form. Subsets and combinations are essentially the same; we group them.

**Form 1: distinct elements, used at most once.**

```java
// Combinations/Subsets framework
void backtrack(int[] nums, int start) {
    for (int i = start; i < nums.length; i++) {
        // Choose
        track.addLast(nums[i]);
        // Note the parameter
        backtrack(nums, i + 1);
        // Undo
        track.removeLast();
    }
}

// Permutations framework
void backtrack(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        // Pruning
        if (used[i]) {
            continue;
        }
        // Choose
        used[i] = true;
        track.addLast(nums[i]);

        backtrack(nums);
        // Undo
        track.removeLast();
        used[i] = false;
    }
}
```

**Form 2: duplicates, used at most once. Sort + skip equal siblings:**

```java
Arrays.sort(nums);
// Combinations/Subsets framework
void backtrack(int[] nums, int start) {
    for (int i = start; i < nums.length; i++) {
        // Skip equal siblings
        if (i > start && nums[i] == nums[i - 1]) {
            continue;
        }
        track.addLast(nums[i]);
        backtrack(nums, i + 1);
        track.removeLast();
    }
}


Arrays.sort(nums);
// Permutations framework
void backtrack(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        if (used[i]) {
            continue;
        }
        // Fix relative order of duplicates
        if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) {
            continue;
        }
        used[i] = true;
        track.addLast(nums[i]);

        backtrack(nums);
        track.removeLast();
        used[i] = false;
    }
}
```

**Form 3: distinct elements, reusable. Drop deduping:**

```java
// Combinations/Subsets framework
void backtrack(int[] nums, int start) {
    for (int i = start; i < nums.length; i++) {
        track.addLast(nums[i]);
        // Note: i, not i + 1
        backtrack(nums, i);
        track.removeLast();
    }
}

// Permutations framework
void backtrack(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        track.addLast(nums[i]);
        backtrack(nums);
        track.removeLast();
    }
}
```

Once you think in trees, these problems collapse to small base-case tweaks. That's why I emphasize tree problems in [Framework Thinking for DS&A](https://labuladong.online/algo/essential-technique/algorithm-summary/) and [Binary Tree Tactics (Outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/).

Try analyzing complexities with the methods in [Practical Time/Space Complexity Analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/).







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Classic Backtracking Problems I](https://labuladong.online/algo/problem-set/backtrack-i/)
 - [[Practice] Classic Backtracking Problems II](https://labuladong.online/algo/problem-set/backtrack-ii/)
 - [[Practice] Classic Backtracking Problems III](https://labuladong.online/algo/problem-set/backtrack-iii/)
 - [Binary Tree Algorithm Outline](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
 - [Mind-Switch Between DP and Backtracking](https://labuladong.online/algo/dynamic-programming/word-break/)
 - [Backtracking Framework](https://labuladong.online/algo/essential-technique/backtrack-framework/)
 - [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Box-and-Ball Model: Two Backtracking Perspectives](https://labuladong.online/algo/practice-in-action/two-views-of-backtrack/)
 - [Practical Guide to Time/Space Complexity Analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/)
 - [Answers to Common Backtracking/DFS Questions](https://labuladong.online/algo/essential-technique/backtrack-vs-dfs/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1079. Letter Tile Possibilities](https://leetcode.com/problems/letter-tile-possibilities/?show=1) | [1079. Letter Tile Possibilities](https://leetcode.cn/problems/letter-tile-possibilities/?show=1) | 🟠 |
| [131. Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/?show=1) | [131. Palindrome Partitioning](https://leetcode.cn/problems/palindrome-partitioning/?show=1) | 🟠 |
| [17. Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/?show=1) | [17. Letter Combinations of a Phone Number](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/?show=1) | 🟠 |
| [254. Factor Combinations](https://leetcode.com/problems/factor-combinations/?show=1)🔒 | [254. Factor Combinations](https://leetcode.cn/problems/factor-combinations/?show=1)🔒 | 🟠 |
| [267. Palindrome Permutation II](https://leetcode.com/problems/palindrome-permutation-ii/?show=1)🔒 | [267. Palindrome Permutation II](https://leetcode.cn/problems/palindrome-permutation-ii/?show=1)🔒 | 🟠 |
| [368. Largest Divisible Subset](https://leetcode.com/problems/largest-divisible-subset/?show=1) | [368. Largest Divisible Subset](https://leetcode.cn/problems/largest-divisible-subset/?show=1) | 🟠 |
| [491. Non-decreasing Subsequences](https://leetcode.com/problems/non-decreasing-subsequences/?show=1) | [491. Non-decreasing Subsequences](https://leetcode.cn/problems/non-decreasing-subsequences/?show=1) | 🟠 |
| [638. Shopping Offers](https://leetcode.com/problems/shopping-offers/?show=1) | [638. Shopping Offers](https://leetcode.cn/problems/shopping-offers/?show=1) | 🟠 |
| [967. Numbers With Same Consecutive Differences](https://leetcode.com/problems/numbers-with-same-consecutive-differences/?show=1) | [967. Numbers With Same Consecutive Differences](https://leetcode.cn/problems/numbers-with-same-consecutive-differences/?show=1) | 🟠 |
| [996. Number of Squareful Arrays](https://leetcode.com/problems/number-of-squareful-arrays/?show=1) | [996. Number of Squareful Arrays](https://leetcode.cn/problems/number-of-squareful-arrays/?show=1) | 🔴 |
| - | [Sword to Offer 38. String Permutations](https://leetcode.cn/problems/zi-fu-chuan-de-pai-lie-lcof/?show=1) | 🟠 |
| - | [Sword to Offer II 079. All Subsets](https://leetcode.cn/problems/TVdhkn/?show=1) | 🟠 |
| - | [Sword to Offer II 080. k-Element Combinations](https://leetcode.cn/problems/uUsW3B/?show=1) | 🟠 |
| - | [Sword to Offer II 081. Combinations Allowing Repeats](https://leetcode.cn/problems/Ygoe9J/?show=1) | 🟠 |
| - | [Sword to Offer II 083. Permutations of Distinct Elements](https://leetcode.cn/problems/VvJkup/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
