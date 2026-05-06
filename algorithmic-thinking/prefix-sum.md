# A Small but Elegant Technique: the Prefix-Sum Array


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [303. Range Sum Query - Immutable](https://leetcode.com/problems/range-sum-query-immutable/) | [303. Range Sum Query - Immutable](https://leetcode.cn/problems/range-sum-query-immutable/) | 🟢 |
| [304. Range Sum Query 2D - Immutable](https://leetcode.com/problems/range-sum-query-2d-immutable/) | [304. Range Sum Query 2D - Immutable](https://leetcode.cn/problems/range-sum-query-2d-immutable/) | 🟠 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Array Basics](https://labuladong.online/algo/data-structure-basic/array-basic/)

The prefix-sum technique is for fast, frequent queries of the sum of elements in a range.

## Prefix Sum on a 1D Array

Example: LeetCode 303 "Range Sum Query - Immutable" asks you to compute the sum of elements in a range — a standard prefix-sum problem:

<Problem slug="range-sum-query-immutable" />

```java
// You must implement this class
class NumArray {

    public NumArray(int[] nums) {}
    
    // Sum over the closed interval [left, right]
    public int sumRange(int left, int right) {}
}
```

Without prefix sums, you might write:

```java
class NumArray {
    // Prefix-sum array
    private int[] preSum;

    // Build the prefix sums from the input
    public NumArray(int[] nums) {
        // preSum[0] = 0 makes range sums easier to compute
        preSum = new int[nums.length + 1];
        // Compute the running sum of nums
        for (int i = 1; i < preSum.length; i++) {
            preSum[i] = preSum[i - 1] + nums[i - 1];
        }
    }

    // Sum over the closed interval [left, right]
    public int sumRange(int left, int right) {
        return preSum[right + 1] - preSum[left];
    }
}
```

The core idea: build a new array `preSum` where `preSum[i]` is the sum of `nums[0..i-1]`. Note $10 = 3 + 5 + 2$:

![](https://labuladong.online/algo/images/difference/1.jpeg)

To get the sum over `[1, 4]`, compute `preSum[5] - preSum[1]`.

So `sumRange` does just one subtraction — worst case O(1) — avoiding a per-call loop.

<visual slug='range-sum-query-immutable' >

Click the visualization below; click <code type="click">preSum[i] = preSum[i - 1] + nums[i - 1]</code> to see `preSum` being built; click <code type="click">console.log</code> to see `sumRange` calls:

</visual>

Practical analogy: students in a class each have a final-exam score (out of 100). Implement an API that, given a score range, returns how many students fall in it.

Use counting sort, then prefix sums:

```java
// All students' scores
int[] scores;
// Max score is 100
int[] count = new int[100 + 1];
// How many students have each score
for (int score : scores)
    count[score]++;
// Build prefix sums
for (int i = 1; i < count.length; i++)
    count[i] = count[i] + count[i-1];
// Use count as a prefix-sum array for range queries
```

Now let's apply prefix sums to a 2D array.

## Prefix Sums in a 2D Matrix

LeetCode 304 "Range Sum Query 2D - Immutable" — like the 1D version, but for submatrices:

<Problem slug="range-sum-query-2d-immutable" />

You could nest two for loops, but the complexity is poor.

The trick: any submatrix's sum can be obtained from a few "anchored" submatrix sums:

![](https://labuladong.online/algo/images/presum/5.jpeg)

These four anchored submatrices all have `(0, 0)` as their top-left.

So, similar to 1D, maintain a 2D `preSum` recording the sum of each origin-anchored submatrix; any submatrix's sum is then a few additions/subtractions:

```java
class NumMatrix {
    // preSum[i][j] is the sum of matrix [0, 0, i-1, j-1]
    private int[][] preSum;

    public NumMatrix(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        if (m == 0 || n == 0) return;
        // Build the prefix-sum matrix
        preSum = new int[m + 1][n + 1];
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                // Sum of [0, 0, i, j]
                preSum[i][j] = preSum[i-1][j] + preSum[i][j-1] + matrix[i - 1][j - 1] - preSum[i-1][j-1];
            }
        }
    }

    // Sum of submatrix [x1, y1, x2, y2]
    public int sumRegion(int x1, int y1, int x2, int y2) {
        // Computed from four neighboring sums
        return preSum[x2+1][y2+1] - preSum[x1][y2+1] - preSum[x2+1][y1] + preSum[x1][y1];
    }
}
```

`sumRegion` is now O(1) — classic "trade space for time".

<visual slug='range-sum-query-2d-immutable' >

Click the visualization below; click <code type="click">preSum[i][j] = ...</code> to see `preSum` being built; click <code type="click">console.log</code> to see `sumRegion` calls:

</visual>

That covers prefix sums — easy if you know the trick. Practice spotting it.


## Extensions

The technique generalizes beyond sums — to range max/min/products, etc.

Prefix sums often combine with other techniques. See [Prefix-Sum Practice Problems](https://labuladong.online/algo/problem-set/perfix-sum/).

**An important caveat**: prefix sums require `nums` to be **immutable**.

If an element changes, all `preSum` entries past it are invalidated and must be recomputed in O(n) — no better than brute force.

For mutable elements, use [segment trees](https://labuladong.online/algo/data-structure/segment-tree-implement/) instead.


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Segment Tree Implementation](https://labuladong.online/algo/data-structure/segment-tree-implement/)
 - [[Practice] Classic Prefix-Sum Problems](https://labuladong.online/algo/problem-set/perfix-sum/)
 - [[Practice] General Monotonic Queue and Classic Problems](https://labuladong.online/algo/problem-set/monotonic-queue/)
 - [[Practice] Traversal Mindset III](https://labuladong.online/algo/problem-set/binary-tree-traverse-iii/)
 - [Tricks for Traversing 2D Arrays](https://labuladong.online/algo/practice-in-action/2d-array-traversal-summary/)
 - [DP Design: Maximum Subarray](https://labuladong.online/algo/dynamic-programming/maximum-subarray/)
 - [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Small but Elegant Technique: Difference Array](https://labuladong.online/algo/data-structure/diff-array/)
 - [Weighted Random Selection](https://labuladong.online/algo/frequency-interview/random-pick-with-weight/)
 - [Extension: Merge Sort Details and Applications](https://labuladong.online/algo/practice-in-action/merge-sort/)
 - [Key Points and Pitfalls in Practice](https://labuladong.online/algo/intro/how-to-learn-algorithms/)
 - [Segment Tree Core Principles and Visualization](https://labuladong.online/algo/data-structure-basic/segment-tree-basic/)
 - [Issues with Selection Sort](https://labuladong.online/algo/data-structure-basic/select-sort/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1314. Matrix Block Sum](https://leetcode.com/problems/matrix-block-sum/?show=1) | [1314. Matrix Block Sum](https://leetcode.cn/problems/matrix-block-sum/?show=1) | 🟠 |
| [1352. Product of the Last K Numbers](https://leetcode.com/problems/product-of-the-last-k-numbers/?show=1) | [1352. Product of the Last K Numbers](https://leetcode.cn/problems/product-of-the-last-k-numbers/?show=1) | 🟠 |
| [238. Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/?show=1) | [238. Product of Array Except Self](https://leetcode.cn/problems/product-of-array-except-self/?show=1) | 🟠 |
| [325. Maximum Size Subarray Sum Equals k](https://leetcode.com/problems/maximum-size-subarray-sum-equals-k/?show=1)🔒 | [325. Maximum Size Subarray Sum Equals k](https://leetcode.cn/problems/maximum-size-subarray-sum-equals-k/?show=1)🔒 | 🟠 |
| [327. Count of Range Sum](https://leetcode.com/problems/count-of-range-sum/?show=1) | [327. Count of Range Sum](https://leetcode.cn/problems/count-of-range-sum/?show=1) | 🔴 |
| [437. Path Sum III](https://leetcode.com/problems/path-sum-iii/?show=1) | [437. Path Sum III](https://leetcode.cn/problems/path-sum-iii/?show=1) | 🟠 |
| [523. Continuous Subarray Sum](https://leetcode.com/problems/continuous-subarray-sum/?show=1) | [523. Continuous Subarray Sum](https://leetcode.cn/problems/continuous-subarray-sum/?show=1) | 🟠 |
| [525. Contiguous Array](https://leetcode.com/problems/contiguous-array/?show=1) | [525. Contiguous Array](https://leetcode.cn/problems/contiguous-array/?show=1) | 🟠 |
| [560. Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/?show=1) | [560. Subarray Sum Equals K](https://leetcode.cn/problems/subarray-sum-equals-k/?show=1) | 🟠 |
| [724. Find Pivot Index](https://leetcode.com/problems/find-pivot-index/?show=1) | [724. Find Pivot Index](https://leetcode.cn/problems/find-pivot-index/?show=1) | 🟢 |
| [862. Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/?show=1) | [862. Shortest Subarray with Sum at Least K](https://leetcode.cn/problems/shortest-subarray-with-sum-at-least-k/?show=1) | 🔴 |
| [918. Maximum Sum Circular Subarray](https://leetcode.com/problems/maximum-sum-circular-subarray/?show=1) | [918. Maximum Sum Circular Subarray](https://leetcode.cn/problems/maximum-sum-circular-subarray/?show=1) | 🟠 |
| [974. Subarray Sums Divisible by K](https://leetcode.com/problems/subarray-sums-divisible-by-k/?show=1) | [974. Subarray Sums Divisible by K](https://leetcode.cn/problems/subarray-sums-divisible-by-k/?show=1) | 🟠 |
| - | [Sword to Offer 57 - II. Continuous Sequence with Sum s](https://leetcode.cn/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/?show=1) | 🟢 |
| - | [Sword to Offer II 010. Subarray Sum Equals k](https://leetcode.cn/problems/QTMn0o/?show=1) | 🟠 |
| - | [Sword to Offer II 011. Subarray With Equal 0s and 1s](https://leetcode.cn/problems/A1NYOS/?show=1) | 🟠 |
| - | [Sword to Offer II 012. Left and Right Subarrays Equal Sum](https://leetcode.cn/problems/tvdfij/?show=1) | 🟢 |
| - | [Sword to Offer II 013. 2D Submatrix Sum](https://leetcode.cn/problems/O4NDxx/?show=1) | 🟠 |
| - | [Sword to Offer II 050. Path Sum III](https://leetcode.cn/problems/6eUYwP/?show=1) | 🟠 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
