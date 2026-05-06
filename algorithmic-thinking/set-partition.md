# Backtracking in Practice: Set Partitioning


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [698. Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/) | [698. Partition to K Equal Sum Subsets](https://leetcode.cn/problems/partition-to-k-equal-sum-subsets/) | 🟠 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [N-ary Tree Structure and Traversal Framework](https://labuladong.online/algo/data-structure-basic/n-ary-tree-traverse-basic/)
> - [Binary Tree Algorithm Outline](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
> - [Backtracking Framework](https://labuladong.online/algo/essential-technique/backtrack-framework/)
> - [Box-and-Ball Model: Two Backtracking Perspectives](https://labuladong.online/algo/practice-in-action/two-views-of-backtrack/)


I've said before: backtracking is the best algorithm in tests — when stuck, brute-force with backtracking. Even if it doesn't pass all tests, it gets some. The technique is simple — enumerate a decision tree by "making a choice" before recursion and "undoing" after.

**But brute-force enumeration has good and bad versions.** Here's a classic backtracking problem — LeetCode 698 "Partition to K Equal Sum Subsets" — that helps you internalize backtracking thinking and write recursion fluently.

The problem:

Given `nums` and a positive integer `k`, decide if `nums` can be partitioned into `k` subsets of equal sum.

```java
boolean canPartitionKSubsets(int[] nums, int k);
```

> [!NOTE]
> We did the partition-into-2 case in [Knapsack: Subset Partition](https://labuladong.online/algo/dynamic-programming/knapsack2/), reducing it to a knapsack problem solvable by DP.
> 
> Why does the 2-subset case reduce to knapsack but the k-subset case doesn't, requiring backtracking? Try thinking before reading.

> [!NOTE]
> Why does the 2-subset case reduce to knapsack?
> 
> In [Knapsack: Subset Partition](https://labuladong.online/algo/dynamic-programming/knapsack2/), each item has **two choices**: include or exclude. Partitioning `S` into two equal subsets `S_1, S_2`: each element has **two choices** — go to `S_1` or `S_2`. Same as knapsack.
> 
> But for `k` equal subsets, each element has **`k` choices**, fundamentally different from standard knapsack — you can't reduce.


## Approach

Subsets differ from permutations/combinations, but we can borrow the "box-and-ball model" abstraction with two perspectives.

Distribute `n` numbers into `k` "buckets" so each bucket has the same sum.

The key in [Two Backtracking Perspectives](https://labuladong.online/algo/practice-in-action/two-views-of-backtrack/) is: how do we "make a choice" so recursion can enumerate?

By analogy with the permutation derivation, two perspectives:

**Perspective 1: switching to the `n` numbers' perspective — each number chooses one of the `k` buckets.**

![](https://labuladong.online/algo/images/set-split/5.jpeg)

**Perspective 2: switching to the `k` buckets' perspective — each bucket scans `nums`'s `n` numbers and decides whether to include each.**

![](https://labuladong.online/algo/images/set-split/6.jpeg)

Why do they differ? Same reason as before — different perspectives produce identical results but different code logic and complexities. Pick the cheaper one.

## Numbers' Perspective

Iterating `nums` with for is straightforward:

```java
for (int index = 0; index < nums.length; index++) {
    print(nums[index]);
}
```

Recursive equivalent:

```java
void traverse(int[] nums, int index) {
    if (index == nums.length) {
        return;
    }
    print(nums[index]);
    traverse(nums, index + 1);
}
```

`traverse(nums, 0)` is equivalent to the for loop.

For our problem, from the numbers' view, choose among `k` buckets. With for:

```java
// k buckets — each bucket's running sum
int[] bucket = new int[k];

// Enumerate each number in nums
for (int index = 0; index < nums.length; index++) {
    // Enumerate each bucket
    for (int i = 0; i < k; i++) {
        // nums[index] decides whether to enter bucket i
        // ...
    }
}
```

In recursive form:

```java
// k buckets — each bucket's running sum
int[] bucket = new int[k];

// Recurse over numbers in nums
void backtrack(int[] nums, int index) {
    // base case
    if (index == nums.length) {
        return;
    }
    // Enumerate each bucket
    for (int i = 0; i < bucket.length; i++) {
        // Choice: place into bucket i
        bucket[i] += nums[index];
        // Recurse for the next number
        backtrack(nums, index + 1);
        // Undo
        bucket[i] -= nums[index];
    }
}
```

Just enumeration; flesh it out:

```java
class Solution {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        // Quick checks
        if (k > nums.length) return false;
        int sum = 0;
        for (int v : nums) sum += v;
        if (sum % k != 0) return false;

        // k buckets — each bucket's running sum
        int[] bucket = new int[k];
        // Each bucket's target sum
        int target = sum / k;
        // Enumerate; can nums be split into k subsets each summing to target?
        return backtrack(nums, 0, bucket, target);
    }

    // Recurse over each number in nums
    boolean backtrack(
        int[] nums, int index, int[] bucket, int target) {

        if (index == nums.length) {
            // Check whether all buckets equal target
            for (int i = 0; i < bucket.length; i++) {
                if (bucket[i] != target) {
                    return false;
                }
            }
            // nums was partitioned into k equal subsets
            return true;
        }
        
        // Enumerate which bucket nums[index] goes into
        for (int i = 0; i < bucket.length; i++) {
            // Prune: would overflow this bucket
            if (bucket[i] + nums[index] > target) {
                continue;
            }
            // Place nums[index] into bucket i
            bucket[i] += nums[index];
            // Recurse for the next number
            if (backtrack(nums, index + 1, bucket, target)) {
                return true;
            }
            // Undo
            bucket[i] -= nums[index];
        }

        // No bucket works
        return false;
    }
}
```

Easy to follow. We can optimize.

Look at the recursive section:

```java
for (int i = 0; i < bucket.length; i++) {
    // Pruning
    if (bucket[i] + nums[index] > target) {
        continue;
    }

    if (backtrack(nums, index + 1, bucket, target)) {
        return true;
    }
}
```

**The more often the pruning branch fires, the fewer recursive calls, the better the time.**

How? `index` walks `nums` from 0 in order. Sort `nums` in descending order so larger numbers go first; later numbers are more likely to overflow — pruning more often.

Add code:

```java
boolean canPartitionKSubsets(int[] nums, int k) {
    // Same as before
    // ...
    // Sort nums in descending order
    Arrays.sort(nums);
    // Reverse to get descending
    for (i = 0, j = nums.length - 1; i < j; i++, j--) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
    // *****************
    return backtrack(nums, 0, bucket, target);
}
```

Correct, but slow — won't pass all tests. Let's try the other perspective.

## Buckets' Perspective

As stated, **from the bucket's view, each bucket scans every `nums` element to decide whether to include; once a bucket is full, move on; until all are full**.

Sketch:

```java
// Until all k buckets are filled
while (k > 0) {
    // This bucket's running sum
    int bucket = 0;
    for (int i = 0; i < nums.length; i++) {
        // Decide whether to add nums[i]
        if (canAdd(bucket, num[i])) {
            bucket += nums[i];
        }
        if (bucket == target) {
            // Filled; next bucket
            k--;
            break;
        }
    }
}
```

Convert to recursion. First the signature:

```java
boolean backtrack(int k, int bucket, int[] nums, int start, boolean[] used, int target);
```

A lot of parameters but manageable. With [Two Backtracking Perspectives](https://labuladong.online/algo/practice-in-action/two-views-of-backtrack/) understood, this should be doable.

The parameters:

Bucket `k` is currently deciding whether to add `nums[start]`; bucket `k`'s current sum is `bucket`; `used[i]` indicates whether `nums[i]` is used; `target` is each bucket's target.

Initial call:

```java
class Solution {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        // Quick checks
        if (k > nums.length) return false;
        int sum = 0;
        for (int v : nums) sum += v;
        if (sum % k != 0) return false;
        
        boolean[] used = new boolean[nums.length];
        int target = sum / k;
        // Bucket k is empty initially; start with nums[0]
        return backtrack(k, 0, nums, 0, used, target);
    }
}
```

Bucket-perspective logic:

1. Iterate `nums`; decide which to add.

2. If full (sum reaches `target`), move to the next bucket.

```java
class Solution {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        // See above
    }

    boolean backtrack(int k, int bucket, 
        int[] nums, int start, boolean[] used, int target) {
        // base case
        if (k == 0) {
            // All buckets full; nums must be fully used
            // because target == sum / k
            return true;
        }
        if (bucket == target) {
            // Current bucket is full; recurse on next bucket
            // Start the next bucket from nums[0]
            return backtrack(k - 1, 0 ,nums, 0, used, target);
        }

        // Probe nums[i] from `start` onward to add to current bucket
        for (int i = start; i < nums.length; i++) {
            // Prune
            if (used[i]) {
                // Used elsewhere
                continue;
            }
            if (nums[i] + bucket > target) {
                // Would overflow this bucket
                continue;
            }
            // Choice: place nums[i] into current bucket
            used[i] = true;
            bucket += nums[i];
            // Recurse on the next number
            if (backtrack(k, bucket, nums, i + 1, used, target)) {
                return true;
            }
            // Undo
            used[i] = false;
            bucket -= nums[i];
        }
        // None worked
        return false;
    }
}
```

**Correct but slow — room to optimize.**

Buckets are interchangeable, but our algorithm treats them as distinct, leading to redundant work.

The algorithm enumerates combinations to find `k` buckets summing to `target`.

E.g. `target = 5`. Bucket 1 might contain `{1, 4}`:

![](https://labuladong.online/algo/images/set-split/1.jpeg)

Bucket 2 starts; `{2, 3}` is added:

![](https://labuladong.online/algo/images/set-split/2.jpeg)

And so on for the rest, trying to form buckets of sum 5.

If it fails, the algorithm backtracks; trying `{2, 3}` for bucket 1:

![](https://labuladong.online/algo/images/set-split/3.jpeg)

Bucket 2 then takes `{1, 4}`:

![](https://labuladong.online/algo/images/set-split/4.jpeg)

You see — same situation as before. Useless work.

The algorithm doesn't notice because it treats the buckets as distinct.

Both states share the same `used` array, which we can treat as the "state" of the recursion.

**So memoize: when filling a bucket, record `used`. If we've seen this state, return the cached result.**

Booleans aren't naturally hashable; convert the array to a string.

```java
class Solution {

    // Memo of used states
    HashMap<String, Boolean> memo = new HashMap<>();

    public boolean canPartitionKSubsets(int[] nums, int k) {
        // See above
    }

    boolean backtrack(int k, int bucket, int[] nums, int start, boolean[] used, int target) {        
        // base case
        if (k == 0) {
            return true;
        }
        // Convert used to a string like [true, false, ...]
        String state = Arrays.toString(used);

        if (bucket == target) {
            // Full; recurse on next bucket
            boolean res = backtrack(k - 1, 0, nums, 0, used, target);
            // Cache
            memo.put(state, res);
            return res;
        }
        
        if (memo.containsKey(state)) {
            // Already computed; return
            return memo.get(state);
        }

        // Same as before...
    }
}
```

Still slow — not algorithmic redundancy now but implementation overhead.

**Converting `used` to a string each call is costly. Optimize further.**

`nums.length <= 16` per the constraints; use a "bitmap" — an `int` `used` instead of a boolean array.

The `i`-th bit (`(used >> i) & 1`) being 1/0 represents `used[i]`'s true/false.

Saves space, and `int` keys hash directly without string conversion.

Final code:

```java
class Solution {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        // Quick checks
        if (k > nums.length) return false;
        int sum = 0;
        for (int v : nums) sum += v;
        if (sum % k != 0) return false;
        
        // Bitmap trick
        int used = 0;
        int target = sum / k;
        // Bucket k empty initially; start with nums[0]
        return backtrack(k, 0, nums, 0, used, target);
    }

    HashMap<Integer, Boolean> memo = new HashMap<>();

    boolean backtrack(int k, int bucket,
                    int[] nums, int start, int used, int target) {        
        // base case
        if (k == 0) {
            // All buckets full; nums must be fully used
            return true;
        }
        if (bucket == target) {
            // Current bucket full; recurse on next
            // Next bucket starts from nums[0]
            boolean res = backtrack(k - 1, 0, nums, 0, used, target);
            // Cache
            memo.put(used, res);
            return res;
        }
        
        if (memo.containsKey(used)) {
            // Avoid redundant work
            return memo.get(used);
        }

        for (int i = start; i < nums.length; i++) {
            // Prune
            // Is bit i set?
            if (((used >> i) & 1) == 1) {
                // Used elsewhere
                continue;
            }
            if (nums[i] + bucket > target) {
                continue;
            }
            // Choice
            // Set bit i to 1
            used |= 1 << i;
            bucket += nums[i];
            // Recurse on the next number
            if (backtrack(k, bucket, nums, i + 1, used, target)) {
                return true;
            }
            // Undo
            // Use XOR to clear bit i
            used ^= 1 << i;
            bucket -= nums[i];
        }

        return false;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/partition-to-k-equal-sum-subsets/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌟 Animated Code Visualization 🌟</strong>
</summary>
</details>
</a>
<hr/>


That's the second approach.

## Wrap-Up

Both approaches are correct, but the first — even with sorting — is much slower than the second. Why?

Let `n = nums.length`.

The first approach (numbers' view): `n` numbers, each picks one of `k` buckets, so `k^n` combinations — $O(k^n)$.

The second (buckets' view): each bucket scans `n` numbers, each with two choices (include/exclude), so `2^n` per bucket; with `k` buckets, $O(k \cdot 2^n)$ in the worst case.

**These are loose worst-case upper bounds; the pruning helps a lot.** Still, the first is asymptotically much worse.

So who said backtracking has no technique? Even brute-force has smart and dumb versions — depending on whose perspective you enumerate from.

In other words, prefer "many small choices" (multiplication) to "few huge ones" (exponentiation): `n` rounds of "k-out-of-1" repeated once ($O(k^n)$) is far worse than `n` rounds of "2-out-of-1" repeated `k` times ($O(k \cdot 2^n)$).

The two perspectives, similar code, but very different efficiency. Hopefully this article cements your backtracking intuition.


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Classic Backtracking Problems I](https://labuladong.online/algo/problem-set/backtrack-i/)
 - [Algorithms in the Game of Doudizhu](https://labuladong.online/algo/practice-in-action/split-array-into-consecutive-subsequences/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [473. Matchsticks to Square](https://leetcode.com/problems/matchsticks-to-square/?show=1) | [473. Matchsticks to Square](https://leetcode.cn/problems/matchsticks-to-square/?show=1) | 🟠 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
