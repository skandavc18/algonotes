# Practical Framework for Applying Binary Search



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1011. Capacity To Ship Packages Within D Days](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/) | [1011. Capacity To Ship Packages Within D Days](https://leetcode.cn/problems/capacity-to-ship-packages-within-d-days/) | 🟠 |
| [410. Split Array Largest Sum](https://leetcode.com/problems/split-array-largest-sum/) | [410. Split Array Largest Sum](https://leetcode.cn/problems/split-array-largest-sum/) | 🔴 |
| [875. Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/) | [875. Koko Eating Bananas](https://leetcode.cn/problems/koko-eating-bananas/) | 🟠 |

**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Binary Search Framework](https://labuladong.online/algo/essential-technique/binary-search-framework/)

In [Binary Search Framework](https://labuladong.online/algo/essential-technique/binary-search-framework/) we covered the details — searching one element, the left bound, and the right bound — and how to write bug-free code.

**That framework is for the basic case "search a sorted array for an element". Real problems are rarely so direct; you may not even spot the binary-search angle.**

This article presents a thinking framework for applying binary search to real problems.







## Vanilla Binary Search

Vanilla binary search: find `target` in a sorted array, return its index.

If absent, return some sentinel — minor adjustment.

If `target` occurs multiple times, you may want the leftmost or rightmost — the "search left/right boundary" variants.

**[Binary Search Framework](https://labuladong.online/algo/essential-technique/binary-search-framework/) explains all this; review it if needed.**

**For real problems, "search left boundary" and "search right boundary" are most common**, not "find one element".

Most problems ask for an extreme — the minimum eating speed, the minimum ship capacity, etc. Finding extremes is finding boundaries.

> [!NOTE]
> I use left-closed, right-open here. Adapt if you prefer fully closed.

Left-bound search:

```java
// Search left bound
int left_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int left = 0, right = nums.length;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            // Shrink right when target found
            right = mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid;
        }
    }
    return left;
}
```

For `nums = [1,2,3,3,3,5,7]`, `target = 3` → returns 2.

![](https://labuladong.online/algo/images/binary-search-in-action/1.jpeg)

Right-bound search:

```java
// Search right bound
int right_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int left = 0, right = nums.length;

    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            // Shrink left when target found
            left = mid + 1;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid;
        }
    }
    return left - 1;
}
```

Same input → returns 4:

![](https://labuladong.online/algo/images/binary-search-in-action/2.jpeg)

Review done. Whenever a problem reduces to such a graph, binary search applies.







## Generalizing Binary Search

What kind of problems can use binary search?

**Abstract a variable `x`, a function `f(x)`, and a target value `target`.**

Conditions:

**1. `f(x)` is monotonic in `x` (increasing or decreasing).**

**2. The problem asks for the value of `x` satisfying `f(x) == target`.**

Concrete example:

Given a sorted array `nums` and `target`, find `target`'s index. With duplicates, return the smallest.

That's the left-bound case. What are `x`, `f(x)`, `target`?

Treat the index as `x`:

```java
// f(x) is increasing in x
// nums is fixed, so it's not a true variable
int f(int x, int[] nums) {
    return nums[x];
}
```

`f` accesses `nums`, which is sorted ascending → `f(x)` is increasing in `x`.

What does the problem ask? The smallest `x` with `f(x) == target`.

![](https://labuladong.online/algo/images/binary-search-in-action/3.jpeg)

**Whenever a problem maps to this picture, you can apply binary search.**

```java
// f is increasing in x
int f(int x, int[] nums) {
    return nums[x];
}

int left_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int left = 0, right = nums.length;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (f(mid, nums) == target) {
            // Shrink right when target found
            right = mid;
        } else if (f(mid, nums) < target) {
            left = mid + 1;
        } else if (f(mid, nums) > target) {
            right = mid;
        }
    }
    return left;
}
```

Wrapping `nums[mid]` in `f` is overkill here, but it makes the abstraction explicit.

## The Application Framework

To apply binary search:

```java
// f is monotonic in x
int f(int x) {
    // ...
}

// Find an extreme x subject to f(x) == target
int solution(int[] nums, int target) {
    if (nums.length == 0) return -1;
    // Ask: minimum value of x?
    int left = ...;
    // Ask: maximum value of x?
    int right = ... + 1;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (f(mid) == target) {
            // Ask: left or right boundary?
            // ...
        } else if (f(mid) < target) {
            // Ask: how to make f(x) larger?
            // ...
        } else if (f(mid) > target) {
            // Ask: how to make f(x) smaller?
            // ...
        }
    }
    return left;
}
```

Steps:

**1. Identify `x`, `f(x)`, `target`; write `f`.**

**2. Find the search range; initialize `left` and `right`.**

**3. Decide left/right bound; write the code.**

Examples:

## Example 1: Koko Eating Bananas

LeetCode 875 "Koko Eating Bananas":

<Problem slug="koko-eating-bananas" />

Koko eats at most one pile per hour; if she finishes a pile early she waits till the next hour for the next pile. She wants to finish all bananas before the guards return. Find the minimum eating speed `K`:

```java
int minEatingSpeed(int[] piles, int H);
```

Apply the framework.

**1. `x`, `f(x)`, `target`:**

`x` = whatever the problem asks → eating speed.

Speed and total time are inversely related — monotonic. Define:

`f(x)` = hours to eat all bananas at speed `x`.

```java
// f(x) decreases monotonically
long f(int[] piles, int x) {
    long hours = 0;
    for (int i = 0; i < piles.length; i++) {
        hours += piles[i] / x;
        if (piles[i] % x > 0) {
            hours++;
        }
    }
    return hours;
}
```

> [!NOTE]
> Why `long`? `piles[i]` can be 10^9; up to 10^4 piles; with `x = 1`, hours can reach 10^13 — beyond `int` range (~2×10^9).

`target` = `H` (max allowed hours).

**2. Range of `x`:**

Min speed = 1; max speed = the largest pile (eating more wastes — only one pile per hour). Use the constraint `1 <= piles[i] <= 10^9`:

```java
public int minEatingSpeed(int[] piles, int H) {
    int left = 1;
    // Left-closed, right-open; +1 for the open end
    int right = 1000000000 + 1;

    // ...
}
```

Binary search is logarithmic, so the size doesn't hurt.

**3. Left or right bound?**

`x` = speed; `f(x)` decreases; `target` = `H`. We want the minimum `x`:

![](https://labuladong.online/algo/images/binary-search-in-action/4.jpeg)

Left-bound search — but `f(x)` is decreasing, so adapt the comparisons:

```java
class Solution {
    public int minEatingSpeed(int[] piles, int H) {
        int left = 1;
        int right = 1000000000 + 1;
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (f(piles, mid) == H) {
                // Left bound → shrink right
                right = mid;
            } else if (f(piles, mid) < H) {
                // Need larger f(x)
                right = mid;
            } else if (f(piles, mid) > H) {
                // Need smaller f(x)
                left = mid + 1;
            }
        }
        return left;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/koko-eating-bananas/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Animated Code Visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>



> [!TIP]
> If you prefer fully closed `[left, right]`:
> 
> ```java
> // Fully closed
> int minEatingSpeed(int[] piles, int H) {
>     int left = 1;
>     // closed → max value
>     int right = 1000000000;
> 
>     // closed → use <=
>     while (left <= right) {
>         int mid = left + (right - left) / 2;
>         if (f(piles, mid) <= H) {
>             // closed → mid - 1
>             right = mid - 1;
>         } else {
>             left = mid + 1;
>         }
>     }
>     return left;
> }
> ```
> 
> Details in [Binary Search Framework](https://labuladong.online/algo/essential-technique/binary-search-framework/).

Done. Merge redundant branches for performance:

```java
class Solution {
    public int minEatingSpeed(int[] piles, int H) {
        int left = 1;
        int right = 1000000000 + 1;
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (f(piles, mid) <= H) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }

    // f(x) decreases monotonically
    long f(int[] piles, int x) {
        long hours = 0;
        for (int i = 0; i < piles.length; i++) {
            hours += piles[i] / x;
            if (piles[i] % x > 0) {
                hours++;
            }
        }
        return hours;
    }
}
```

## Example 2: Capacity to Ship Packages

LeetCode 1011 "Capacity to Ship Packages Within D Days":

<Problem slug="capacity-to-ship-packages-within-d-days" />

Find the minimum ship capacity to deliver all packages (in order) within `D` days.

```java
int shipWithinDays(int[] weights, int days);
```

Apply the framework:

**1. `x`, `f(x)`, `target`:**

`x` = capacity. Capacity vs days is inversely monotonic:

```java
// f(x) = days needed at capacity x
// f(x) decreases in x
int f(int[] weights, int x) {
    int days = 0;
    for (int i = 0; i < weights.length; ) {
        // Pack as much as possible
        int cap = x;
        while (i < weights.length) {
            if (cap < weights[i]) break;
            else cap -= weights[i];
            i++;
        }
        days++;
    }
    return days;
}
```

`target` = `D` (max days).

**2. Range of `x`:**

Min capacity = max single weight (must carry the heaviest in one go); max capacity = sum of weights (one trip):

```java
int shipWithinDays(int[] weights, int days) {
    int left = 0;
    // Right-open; +1
    int right = 1;
    for (int w : weights) {
        left = Math.max(left, w);
        right += w;
    }
    
    // ...
}
```

**3. Left or right bound?**

`x` = capacity; `f(x)` decreases; `target` = `D`. Want minimum `x`:

![](https://labuladong.online/algo/images/binary-search-in-action/5.jpeg)

Left-bound search:

```java
public int shipWithinDays(int[] weights, int days) {
    int left = 0;
    int right = 1;
    for (int w : weights) {
        left = Math.max(left, w);
        right += w;
    }
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (f(weights, mid) == days) {
            right = mid;
        } else if (f(weights, mid) < days) {
            right = mid;
        } else if (f(weights, mid) > days) {
            left = mid + 1;
        }
    }
    
    return left;
}
```

Merge branches:

```java
class Solution {
    public int shipWithinDays(int[] weights, int days) {
        int left = 0;
        int right = 1;
        for (int w : weights) {
            left = Math.max(left, w);
            right += w;
        }

        while (left < right) {
            int mid = left + (right - left) / 2;
            if (f(weights, mid) <= days) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }

        return left;
    }

    int f(int[] weights, int x) {
        int days = 0;
        for (int i = 0; i < weights.length; ) {
            int cap = x;
            while (i < weights.length) {
                if (cap < weights[i]) break;
                else cap -= weights[i];
                i++;
            }
            days++;
        }
        return days;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/capacity-to-ship-packages-within-d-days/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>



## Example 3: Split Array

LeetCode 410 "Split Array Largest Sum" (Hard):

<Problem slug="split-array-largest-sum" />

```java
int splitArray(int[] nums, int m);
```

Reminiscent of the egg-drop DP problem, the wording is twisty.

Plain words: split `nums` into `m` subarrays. Among all splits, the largest subarray sum is some value; minimize that value.

Daunting — but it's the same as the shipping problem.

Restatement: one ship; goods of weights `nums[i]`; deliver in `m` days. Minimum capacity?

That's exactly LeetCode 1011.

A day = a contiguous subarray; `m` days = `m` subarrays; minimizing capacity = minimizing the maximum subarray sum.

Just paste the shipping solution:

```java
class Solution {
     public int splitArray(int[] nums, int m) {
        return shipWithinDays(nums, m);
    }

    int shipWithinDays(int[] weights, int days) {
        // See above
    }

    int f(int[] weights, int x) {
        // See above
    }
}
```

Whenever there's monotonicity in the problem, try binary search. Identify the monotonicity, choose the boundary, sketch, write.







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Classic Binary Search Problems](https://labuladong.online/algo/problem-set/binary-search/)
 - [[Practice] Classic Backtracking Problems II](https://labuladong.online/algo/problem-set/backtrack-ii/)
 - [Sweeping All Ugly-Number Problems](https://labuladong.online/algo/frequency-interview/ugly-number-summary/)
 - [Binary Search Core Code Template](https://labuladong.online/algo/essential-technique/binary-search-framework/)
 - [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Beat Algorithms with Algorithms](https://labuladong.online/algo/fname.html?fname=algorithm-in-pdf)
 - [Classic DP: Egg Drop](https://labuladong.online/algo/dynamic-programming/egg-drop/)
 - [Two Common Factorial Problems](https://labuladong.online/algo/frequency-interview/factorial-problems/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1201. Ugly Number III](https://leetcode.com/problems/ugly-number-iii/?show=1) | [1201. Ugly Number III](https://leetcode.cn/problems/ugly-number-iii/?show=1) | 🟠 |
| [1723. Find Minimum Time to Finish All Jobs](https://leetcode.com/problems/find-minimum-time-to-finish-all-jobs/?show=1) | [1723. Find Minimum Time to Finish All Jobs](https://leetcode.cn/problems/find-minimum-time-to-finish-all-jobs/?show=1) | 🔴 |
| - | [Sword to Offer II 073. Koko Eating Bananas](https://leetcode.cn/problems/nZZqjQ/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
