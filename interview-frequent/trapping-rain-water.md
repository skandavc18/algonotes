# Solving the Trapping Rain Water Problem Efficiently



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [11. Container With Most Water](https://leetcode.com/problems/container-with-most-water/) | [11. Container With Most Water](https://leetcode.cn/problems/container-with-most-water/) | 🟠 |
| [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) | [42. Trapping Rain Water](https://leetcode.cn/problems/trapping-rain-water/) | 🔴 |

**-----------**




> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Array Two-Pointer Tricks Summary](https://labuladong.online/algo/essential-technique/array-two-pointers-summary/)

LeetCode 42 "Trapping Rain Water" is fun and frequently appears in interviews. Let's improve it step by step.

The problem:

<Problem slug="trapping-rain-water" />

An array represents a bar chart; how much water can it trap?

```java
int trap(int[] height);
```

We'll progress: brute force → memoized → two-pointer (O(N) time, O(1) space).

## 1. Core Idea

> [!TIP]
> If you have no idea how to start, simplify and write the most naive solution first; you may find a path to the optimal.

For position `i`, how much water can it trap?

![](https://labuladong.online/algo/images/rain-water/0.jpg)

Two units. `height[i] = 0`; the cell can hold 2 units.

Why 2? Because the water column at `i` is bounded by the tallest bars to its left and right, `l_max` and `r_max`. **At `i`, the column height is `min(l_max, r_max)`.**

So:

```python
water[i] = min(
    # Tallest bar to the left
    max(height[0..i]),  
    # Tallest bar to the right
    max(height[i..end]) 
) - height[i]
```

![](https://labuladong.online/algo/images/rain-water/1.jpg)

![](https://labuladong.online/algo/images/rain-water/2.jpg)



The brute force:

```java
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        int res = 0;
        for (int i = 1; i < n - 1; i++) {
            int l_max = 0, r_max = 0;
            // Tallest bar to the right
            for (int j = i; j < n; j++)
                r_max = Math.max(r_max, height[j]);
            // Tallest bar to the left
            for (int j = i; j >= 0; j--)
                l_max = Math.max(l_max, height[j]);
            // If i itself is the tallest,
            // l_max == r_max == height[i]
            res += Math.min(l_max, r_max) - height[i];
        }
        return res;
    }
}
```

O(N²) time, O(1) space. Recomputing `l_max` and `r_max` is wasteful — let's optimize.

## 2. Memoized

Precompute the maxima:

**Two arrays `l_max` and `r_max`: `l_max[i]` is the tallest bar at or before `i`; `r_max[i]` is the tallest at or after `i`**:

```java
class Solution {
    public int trap(int[] height) {
        if (height.length == 0) {
            return 0;
        }
        int n = height.length;
        int res = 0;
        // Memo arrays
        int[] l_max = new int[n];
        int[] r_max = new int[n];
        // Base cases
        l_max[0] = height[0];
        r_max[n - 1] = height[n - 1];
        // Compute l_max left-to-right
        for (int i = 1; i < n; i++)
            l_max[i] = Math.max(height[i], l_max[i - 1]);
        // Compute r_max right-to-left
        for (int i = n - 2; i >= 0; i--)
            r_max[i] = Math.max(height[i], r_max[i + 1]);
        // Compute the answer
        for (int i = 1; i < n - 1; i++)
            res += Math.min(l_max[i], r_max[i]) - height[i];
        return res;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/trapping-rain-water/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>



Same idea, no recomputation. O(N) time but O(N) space. We can reduce space to O(1).

## 3. Two-Pointer Solution

> [!NOTE]
> Treat this as a stretch — don't obsess over the optimum. The simpler memoized version is usually accepted in interviews.
> 
> Only optimize further if needed.

Same idea, but compute on the fly with two pointers.

A first pass:

```java
int trap(int[] height) {
    int left = 0, right = height.length - 1;
    int l_max = 0, r_max = 0;
    
    while (left < right) {
        l_max = Math.max(l_max, height[left]);
        r_max = Math.max(r_max, height[right]);
        // What do l_max and r_max mean here?
        left++; right--;
    }
}
```

What do `l_max` and `r_max` mean?

**`l_max` is the tallest in `height[0..left]`; `r_max` is the tallest in `height[right..end]`.**

Solution:

```java
class Solution {
    public int trap(int[] height) {
        int left = 0, right = height.length - 1;
        int l_max = 0, r_max = 0;

        int res = 0;
        while (left < right) {
            l_max = Math.max(l_max, height[left]);
            r_max = Math.max(r_max, height[right]);

            // res += min(l_max, r_max) - height[i]
            if (l_max < r_max) {
                res += l_max - height[left];
                left++;
            } else {
                res += r_max - height[right];
                right--;
            }
        }
        return res;
    }
}
```

Same idea, slightly different mechanics.

In the memoized version, `l_max[i]` and `r_max[i]` were the maxima over `height[0..i]` and `height[i..end]`:

```java
res += Math.min(l_max[i], r_max[i]) - height[i];
```

![](https://labuladong.online/algo/images/rain-water/3.jpg)

Here `l_max` and `r_max` are over `height[0..left]` and `height[right..end]`:

```java
if (l_max < r_max) {
    res += l_max - height[left];
    left++; 
} 
```

![](https://labuladong.online/algo/images/rain-water/4.jpg)

`l_max` is the tallest left of `left`, but `r_max` may not be the tallest right of `left`. Is this still correct?

Yes — we only care about `min(l_max, r_max)`. **Above, we already know `l_max < r_max`; whether `r_max` is the absolute right max doesn't matter — `height[i]`'s water depends only on the smaller side `l_max`**:

![](https://labuladong.online/algo/images/rain-water/5.jpg)

Trapping rain water solved.

## Extension: Container With Most Water

A similar problem — LeetCode 11:

<Problem slug="container-with-most-water" />

```java
// Signature
int maxArea(int[] height);
```

Similar idea, but **the previous problem was a bar chart with bar widths; here each line has no width**.

In the previous problem, `l_max` and `r_max` were needed to compute the water at each `i`. Without widths, that's simpler.

If you knew `height[left]` and `height[right]` in trapping rain water, could you compute the water between them? No — you'd need to know each interior bar's `l_max` and `r_max`.

In this problem, you can:

```python
min(height[left], height[right]) * (right - left)
```

Like before, the smaller side bounds the height.

Two pointers:

**`left` and `right` close in from both ends; track the area between them; take the maximum.**

```java
class Solution {
    public int maxArea(int[] height) {
        int left = 0, right = height.length - 1;
        int res = 0;
        while (left < right) {
            // Area between left and right
            int cur_area = Math.min(height[left], height[right]) * (right - left);
            res = Math.max(res, cur_area);
            // Two-pointer: move the lower side
            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }
        return res;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/container-with-most-water/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>



Why move the lower side?

```java
// Two-pointer: move the lower side
if (height[left] < height[right]) {
    left++;
} else {
    right--;
}
```

**Because the rectangle's height is bounded by the lower side: `min(height[left], height[right])`.**

Moving the lower side may raise it, possibly increasing the area. Moving the higher side never increases the height — so the area can't grow.

Done.









**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
