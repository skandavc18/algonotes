# A Small but Elegant Technique: the Difference Array


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1094. Car Pooling](https://leetcode.com/problems/car-pooling/) | [1094. Car Pooling](https://leetcode.cn/problems/car-pooling/) | 🟠 |
| [1109. Corporate Flight Bookings](https://leetcode.com/problems/corporate-flight-bookings/) | [1109. Corporate Flight Bookings](https://leetcode.cn/problems/corporate-flight-bookings/) | 🟠 |
| [370. Range Addition](https://leetcode.com/problems/range-addition/)🔒 | [370. Range Addition](https://leetcode.cn/problems/range-addition/)🔒 | 🟠 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Array Basics](https://labuladong.online/algo/data-structure-basic/array-basic/)
> - [Prefix Sum Technique](https://labuladong.online/algo/data-structure/prefix-sum/)

The [Prefix Sum Technique](https://labuladong.online/algo/data-structure/prefix-sum/) is mainly used when the original array is not modified and we frequently query the sum of a range. Core code:

```java
class PrefixSum {
    // Prefix-sum array
    private int[] preSum;

    // Build the prefix sums from the input array
    public PrefixSum(int[] nums) {
        // preSum[0] = 0 makes range sums easier to compute
        preSum = new int[nums.length + 1];
        // Compute the running sum of nums
        for (int i = 1; i < preSum.length; i++) {
            preSum[i] = preSum[i - 1] + nums[i - 1];
        }
    }
    
    // Sum of the closed range [left, right]
    public int sumRange(int left, int right) {
        return preSum[right + 1] - preSum[left];
    }
}
```

![](https://labuladong.online/algo/images/difference/1.jpeg)


`preSum[i]` is the sum of `nums[0..i-1]`. To get the sum of `nums[i..j]`, compute `preSum[j+1] - preSum[i]` — no need to scan the range.

This article covers a similar trick: the **difference array**, which is mainly used when **we frequently increment/decrement elements over a range of the original array**.

For example, given an array `nums`: add 1 to `nums[2..6]`, subtract 3 from `nums[3..9]`, add 2 to `nums[0..4]`, ...

After all that, what is `nums`?

The naive approach: for each operation, loop through `[i, j]` and add `val` — O(N) per update. With many updates this is slow.

This is where the difference-array trick helps. Like prefix sums, we build a `diff` array, where **`diff[i]` is `nums[i] - nums[i-1]`**:

```java
int[] diff = new int[nums.length];
// Build the difference array
diff[0] = nums[0];
for (int i = 1; i < nums.length; i++) {
    diff[i] = nums[i] - nums[i - 1];
}
```

![](https://labuladong.online/algo/images/difference/2.jpeg)


We can recover `nums` from `diff`:

```java
int[] res = new int[diff.length];
// Reconstruct nums from diff
res[0] = diff[0];
for (int i = 1; i < diff.length; i++) {
    res[i] = res[i - 1] + diff[i];
}
```

**With `diff`, we can perform fast range increments**: to add 3 to all elements in `nums[i..j]`, just do `diff[i] += 3` and `diff[j+1] -= 3`:

![](https://labuladong.online/algo/images/difference/3.jpeg)

**Why? Recall that we recover `nums` from `diff`. `diff[i] += 3` means "add 3 to all of `nums[i..]`"; `diff[j+1] -= 3` means "subtract 3 from all of `nums[j+1..]`". Combined: add 3 to `nums[i..j]`.**

Updating `diff` is O(1) per range. After multiple updates, derive the modified `nums` from `diff`.

Let's wrap the trick in a class with `increment` and `result` methods:

```java
// Difference-array helper class
class Difference {
    // Difference array
    private int[] diff;
    
    // Take an initial array; range operations apply to it
    public Difference(int[] nums) {
        assert nums.length > 0;
        diff = new int[nums.length];
        // Build the diff from nums
        diff[0] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            diff[i] = nums[i] - nums[i - 1];
        }
    }

    // Add val (can be negative) to closed range [i, j]
    public void increment(int i, int j, int val) {
        diff[i] += val;
        if (j + 1 < diff.length) {
            diff[j + 1] -= val;
        }
    }

    // Reconstruct the resulting array
    public int[] result() {
        int[] res = new int[diff.length];
        // Build the result from diff
        res[0] = diff[0];
        for (int i = 1; i < diff.length; i++) {
            res[i] = res[i - 1] + diff[i];
        }
        return res;
    }
}
```

Note the `if` in `increment`:

```java
void increment(int i, int j, int val) {
    diff[i] += val;
    if (j + 1 < diff.length) {
        diff[j + 1] -= val;
    }
}
```

When `j+1 >= diff.length`, the update applies to all elements from `nums[i]` onward, so we don't need to subtract `val` from anything later.

<visual slug="diff-array-example" >

Click the visualization below; click <code type="click">diff[i] = nums[i] - nums[i - 1]</code> repeatedly to see `diff` being built; click <code type="click">df.increment</code> repeatedly to see range updates:

</visual>

## Practice

LeetCode 370 "Range Addition" tests the difference-array trick directly:

<Problem slug="range-addition" />

Reuse `Difference`:

```java
class Solution {
    public int[] getModifiedArray(int length, int[][] updates) {
        // nums initialized to all zeros
        int[] nums = new int[length];
        // Build the difference helper
        Difference df = new Difference(nums);
        
        for (int[] update : updates) {
            int i = update[0];
            int j = update[1];
            int val = update[2];
            df.increment(i, j, val);
        }
        
        return df.result();
    }
}
```

Real problems may need more imagination to spot the technique. LeetCode 1109 "Corporate Flight Bookings":

<Problem slug="corporate-flight-bookings" />

Signature:

```java
int[] corpFlightBookings(int[][] bookings, int n)
```

The wording is roundabout. In plain terms:

Given an array `nums` of length `n` (all zeros) and a list of triples `(i, j, k)`, for each triple, add `k` to `nums[i-1..j-1]`. Return `nums`.

> [!NOTE]
> The problem uses 1-based numbering, but arrays are 0-indexed; for triple `(i, j, k)`, the range is `[i-1, j-1]`.

This is a textbook difference-array problem:

```java
class Solution {
    public int[] corpFlightBookings(int[][] bookings, int n) {
        // nums initialized to all zeros
        int[] nums = new int[n];
        // Build the difference helper
        Difference df = new Difference(nums);

        for (int[] booking : bookings) {
            // Convert to 0-based
            int i = booking[0] - 1;
            int j = booking[1] - 1;
            int val = booking[2];
            // Add val to nums[i..j]
            df.increment(i, j, val);
        }
        // Return the final array
        return df.result();
    }
}
```

Solved.

A similar problem is LeetCode 1094 "Car Pooling":

You're a bus driver with capacity `capacity`, passing several stops. Given `int[][] trips` where `trips[i] = [num, start, end]` means `num` passengers board at `start` and alight at `end`. Determine if all passengers can be carried without exceeding `capacity`.

Signature:

```java
boolean carPooling(int[][] trips, int capacity);
```

Example input:

```
trips = [[2,1,5],[3,3,7]], capacity = 4
```

This can't all be carried in one go: `trips[1]` adds passengers exceeding capacity.

The difference-array approach: **`trips[i]` is a range update; boarding/alighting are range increments. If all values stay below `capacity`, success**.

What length should the difference array be (number of stops)? Not stated directly, but the constraints give us:

```java
0 <= trips[i][1] < trips[i][2] <= 1000
```

Stops are numbered 0..1000 — up to 1001 of them. Set the diff array length to 1001:

```java
class Solution {
    public boolean carPooling(int[][] trips, int capacity) {
        // Up to 1001 stops
        int[] nums = new int[1001];

        // Build the difference helper
        Difference df = new Difference(nums);

        for (int[] trip : trips) {
            // Number of passengers
            int val = trip[0];

            // Boarding at stop trip[1]
            int i = trip[1];

            // Alight at stop trip[2]; passengers are on the bus over [trip[1], trip[2] - 1]
            int j = trip[2] - 1;

            // Range update
            df.increment(i, j, val);
        }

        int[] res = df.result();

        // The bus must never exceed capacity
        for (int i = 0; i < res.length; i++) {
            if (capacity < res[i]) {
                return false;
            }
        }
        return true;
    }
}
```

Solved.

Difference arrays and prefix sums are common, elegant techniques for different scenarios — trivial once you know them, baffling if you don't. Mastered the difference array yet?


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Tricks for Traversing 2D Arrays](https://labuladong.online/algo/practice-in-action/2d-array-traversal-summary/)
 - [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Sweep-Line Technique: Meeting Rooms](https://labuladong.online/algo/frequency-interview/scan-line-technique/)
 - [Key Points and Pitfalls in Practice](https://labuladong.online/algo/intro/how-to-learn-algorithms/)

</details><hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)