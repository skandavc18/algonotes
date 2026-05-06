# Binary Search Core Code Template



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/) | [34. Find First and Last Position of Element in Sorted Array](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/) | 🟠 |
| [704. Binary Search](https://leetcode.com/problems/binary-search/) | [704. Binary Search](https://leetcode.cn/problems/binary-search/) | 🟢 |
| - | [Sword to Offer 53 - I. Find a Number in a Sorted Array](https://leetcode.cn/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/) | 🟢 |

**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Array Basics](https://labuladong.online/algo/data-structure-basic/array-basic/)

> tip: a video version is available: [Binary Search Core Framework](https://www.bilibili.com/video/BV1Gt4y1b79Q/). Follow my Bilibili account; I'll guide you through more difficult algorithm techniques on video.



This is a revised version of the older article on binary search, with more detailed analysis.

A joke first:

One day Adong borrowed `N` books from the library. The alarm went off as he left, so the guard stopped him to find which book wasn't checked out. Adong was about to scan each book under the alarm one by one, but the guard sneered: "You don't even know binary search?"

The guard split the books into two stacks; the first stack triggered the alarm — so the troublemaker is in there. Split it into two again; the first stack triggers the alarm — repeat...

After `logN` checks, the guard found the culprit and grinned smugly. Adong walked off with the rest.

The library lost `N - 1` books that day (j/k).







Binary search isn't trivial. Knuth (yes, the KMP guy) said: **the idea is simple; the details are devilish**. People love to talk about integer overflow, but the real traps are: `mid + 1` or `mid - 1`? `<=` or `<` in `while`?

Without these details, your binary search is mystical programming — bugs are revealed only by chance.

This article explores three common scenarios: searching one number, searching the left bound, searching the right bound. We dive into details — should the inequality include equality, do we add 1 to `mid`, etc.

Also note: for each scenario we'll show several variants. They have no inherent superiority — pick the one you like.

## 0. The Framework

```java
int binarySearch(int[] nums, int target) {
    int left = 0, right = ...;

    while(...) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            ...
        } else if (nums[mid] < target) {
            left = ...
        } else if (nums[mid] > target) {
            right = ...
        }
    }
    return ...;
}
```

**Tip: don't use `else` — write all cases as `else if` for clarity.** Use `else if` throughout this article; once you understand, you can simplify.

The `...` marks are where details lurk. We'll vary them with examples.

**Compute `mid` to avoid overflow**: `left + (right - left) / 2` equals `(left + right) / 2` but avoids overflow for large `left` and `right`.







## 1. Searching for a Number (Standard Binary Search)

The simplest scenario: search for a number; return its index, or -1 if absent.

```java
class Solution {
    // Standard binary search; returns -1 if not found
    public int search(int[] nums, int target) {
        int left = 0;
        // Note
        int right = nums.length - 1;

        while(left <= right) {
            int mid = left + (right - left) / 2;
            if(nums[mid] == target) {
                return mid;   
            } else if (nums[mid] < target) {
                // Note
                left = mid + 1;
            } else if (nums[mid] > target) {
                // Note
                right = mid - 1;
            }
        }
        return -1;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/binary-search/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌟 Animated Code Visualization 🌟</strong>
</summary>
</details>
</a>
<hr/>



This solves LeetCode 704. Let's dive into details.







### Why `<=` and not `<` in `while`?

`right` is initialized to `nums.length - 1`, the last index — not `nums.length`.

These appear in different binary searches: the former is the closed interval `[left, right]`, the latter the half-open `[left, right)`. Index `nums.length` is out of bounds, so `right` is treated as the open end there.

This algorithm uses the closed `[left, right]`. **The interval is what we search at each step.**

When to stop? When found:

```java
    if(nums[mid] == target)
        return mid; 
```

Otherwise, terminate the `while` and return -1. **Terminate when the interval is empty** — meaning nothing left to search.

`while(left <= right)` terminates at `left == right + 1`, i.e., interval `[right + 1, right]` — empty (e.g., `[3, 2]`). Correct: return -1.

`while(left < right)` terminates at `left == right`, i.e., `[right, right]` — non-empty (e.g., `[2, 2]` still has index 2). The loop terminates without checking index 2 — incorrect, since `-1` may be returned wrongly.

If you insist on `<`, patch it:

```java
    // ...
    while(left < right) {
        // ...
    }
    return nums[left] == target ? left : -1;
```

### Why `left = mid + 1`, `right = mid - 1`?

Some code uses `right = mid` or `left = mid` — when?

If you understand the "search interval" idea, this is easy. Here the interval is closed: `[left, right]`. After checking `mid` and not matching, where to search next?

`[left, mid - 1]` or `[mid + 1, right]` — **`mid` was checked; remove it**.







### Limitations

You should now grasp the algorithm and the reasoning. But this version is limited.

For `nums = [1,2,2,2,3]`, `target = 2`, it returns 2 — correct. But if you want the leftmost occurrence (index 1) or rightmost (index 3), this won't help.

You could find any occurrence and scan outward, but that breaks the logarithmic complexity.

We'll discuss those variants next.

## 2. Searching for the Left Bound

The most common form, with details to note:

```java
int left_bound(int[] nums, int target) {
    int left = 0;
    // Note
    int right = nums.length;
    
    // Note
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            right = mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            // Note
            right = mid;
        }
    }
    return left;
}
```

### Why `<` and not `<=`?

`right = nums.length`, not `nums.length - 1`. So the search interval is `[left, right)` — left-closed, right-open.

`while(left < right)` terminates at `left == right`, where `[left, left)` is empty — correct termination.

> [!NOTE]
> A frequent question: **earlier `right` was `nums.length - 1`; why now `nums.length` (half-open)?**
> 
> This is a common style for left/right-bound binary searches; learning it lets you understand similar code elsewhere. If you prefer the closed-interval form, that's simpler — we'll unify all three searches in closed form below.

### What does `target` not existing mean?

If `nums` doesn't contain `target`, what does the returned index mean? How to return -1 instead?







::: important Meaning of `left_bound`'s return when `target` is absent

A great and important question. With this understood, you won't be confused in real applications.

Conclusion: **if `target` is absent, the left-bound search returns the smallest index with a value greater than `target`.**

E.g., `nums = [2,3,5,7], target = 4`. `left_bound` returns 2 — element 5 is the smallest greater than 4.

Confusing? `left_bound` searches the left bound, but returns the smallest index greater than `target` when `target` is absent. No need to memorize — try a small example.

Binary search is conceptually simple; details are devilish — many traps.

This isn't gratuitous complexity — binary search really has these details. If you didn't know the absent-`target` behavior, you'd struggle to debug.

A nice consequence: implement `floor` using `left_bound`:

```java
// In a sorted array, the largest index with value < target
// nums = [1,2,2,2,3], target = 2 → returns 0 (1 is the largest < 2).
// nums = [1,2,3,5,6], target = 4 → returns 2 (3 is the largest < 4).
int floor(int[] nums, int target) {
    // Absent: nums = [4,6,8,10], target = 7 →
    // left_bound = 2; 2 - 1 = 1; element 6 is the largest < 7.
    // Present: nums = [4,6,8,8,8,10], target = 8 →
    // left_bound = 2; 2 - 1 = 1; element 6 is the largest < 8.
    return left_bound(nums, target) - 1;
}
```

If you must hand-write binary search, know the variants. If not, prefer the language's standard library — saves time and clearly documented.

:::

To return -1 when absent, just check `nums[left] == target` after the loop, with bounds:

```java
while (left < right) {
    // ...
}
// Out of bounds → not present
if (left < 0 || left >= nums.length) {
    return -1;
}
// Note: left < 0 is unreachable here (left only increases from 0)
// I include it anyway as a safety habit and for template uniformity (matching the right-bound variant).

return nums[left] == target ? left : -1;
```

### Why `left = mid + 1` and `right = mid`?

The interval is `[left, right)`. After checking `mid`, search `[left, mid)` or `[mid + 1, right)`.

### Why does it find the left bound?

The key is in the `nums[mid] == target` case:

```java
    if (nums[mid] == target)
        right = mid;
```

Don't return immediately; shrink the right bound to keep searching left.







### Why return `left` not `right`?

Same — the loop ends at `left == right`.

### Can we unify with closed-interval style?

Yes — keep `right = nums.length - 1`:

```java
int left_bound(int[] nums, int target) {
    // Closed [left, right]
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        // if else ...
    }
}
```

For closed intervals searching the left bound:

```java
if (nums[mid] < target) {
    // [mid+1, right]
    left = mid + 1;
} else if (nums[mid] > target) {
    // [left, mid-1]
    right = mid - 1;
} else if (nums[mid] == target) {
    // Shrink right
    right = mid - 1;
}
```

To return -1 when absent:

```java
// All numbers were less than target
if (left == nums.length) return -1;
// Check
return nums[left] == target ? left : -1;
```

Full code:

```java
int left_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    // Closed
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // Shrink right
            right = mid - 1;
        }
    }
    // Bounds check
    if (left < 0 || left >= nums.length) {
        return -1;
    }
    return nums[left] == target ? left : -1;
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/binary-search-left-bound/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌟 Animated Code Visualization 🌟</strong>
</summary>
</details>
</a>
<hr/>



Now both variants use closed intervals and return `left`. Pick whichever style you prefer.

## 3. Searching for the Right Bound

Same structure, two versions. First the half-open form — only two differences from the left bound:

```java
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            // Note
            left = mid + 1;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid;
        }
    }
    // Note
    return left - 1;
}
```

### Why does it find the right bound?

```java
if (nums[mid] == target) {
    left = mid + 1;
}
```

Don't return immediately; expand `left` to keep searching right.







### Why return `left - 1`?

Why not `left`? Or `right` since this is the right bound?

`while` ends at `left == right`, so `left == right`; if you want to emphasize "right", return `right - 1`.

Why minus one? Because of the `nums[mid] == target` update:

```java
// Increase left to lock the right bound
if (nums[mid] == target) {
    left = mid + 1;
    // Think: mid = left - 1
}
```

![](https://labuladong.online/algo/images/binary-search/3.jpg)



Since `left = mid + 1`, when the loop ends `nums[left]` may not equal `target`, but `nums[left-1]` may.

`left = mid + 1` is required to remove `mid` from the search interval.

### Return when `target` is absent?

If `nums` doesn't contain `target`, what does the returned index mean?

::: important Meaning of `right_bound`'s return when `target` is absent

The opposite of `left_bound`: **if `target` is absent, the right-bound search returns the largest index with a value less than `target`.**

E.g., `nums = [2,3,5,7], target = 4`. `right_bound` returns 1 — element 3 is the largest less than 4.

As before, prefer the language's standard library when possible.

:::


To return -1 when absent, check `nums[left-1]`:

```java
while (left < right) {
    // ...
}
// left - 1 out of bounds → absent
if (left - 1 < 0 || left - 1 >= nums.length) {
    return -1;
}
return nums[left - 1] == target ? (left - 1) : -1;
```

**Can we unify in closed form?** Yes — small tweaks:

```java
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // Shrink left bound here
            left = mid + 1;
        }
    }
    // Return left - 1
    if (left - 1 < 0 || left - 1 >= nums.length) {
        return -1;
    }
    return nums[left - 1] == target ? (left - 1) : -1;
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/binary-search-right-bound/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>



Since the loop ends at `right == left - 1`, you can replace `left - 1` with `right`:

```java
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // Shrink left
            left = mid + 1;
        }
    }
    // Return right
    if (right < 0 || right >= nums.length) {
        return -1;
    }
    return nums[right] == target ? right : -1;
}
```

Both right-bound variants are done. The closed form is easier to remember.

## 4. Unified Logic

With these, you can solve LeetCode 34. Let's review the cause-effect:

**Standard binary search**:

```java
right = nums.length - 1
→ closed interval [left, right]
→ while (left <= right)
→ left = mid + 1, right = mid - 1

We just need any index of target
→ return immediately when nums[mid] == target
```

**Left bound**:

```java
right = nums.length
→ half-open [left, right)
→ while (left < right)
→ left = mid + 1, right = mid

We need the leftmost index of target
→ don't return immediately when nums[mid] == target
→ shrink right to lock the left bound
```

**Right bound**:

```java
right = nums.length
→ half-open [left, right)
→ while (left < right)
→ left = mid + 1, right = mid

We need the rightmost index of target
→ don't return immediately when nums[mid] == target
→ shrink left to lock the right bound

Since shrinking left requires left = mid + 1
→ return left - 1 (or right) at the end
```

For left/right-bound searches, the half-open form is common; **but unifying in closed form is more memorable, with only two changes between variants**:

If you've followed all this, congrats — binary search's details are behind you.

Lessons:

1. While analyzing, expand to `else if` for clarity. Combine branches once you've got the logic right.

2. Mind the search interval and the loop's termination; if elements may be missed, check at the end.

3. For half-open intervals, only the `nums[mid] == target` case differs; the right bound returns minus one.

4. For closed intervals, the unified form is easy: tweak the `nums[mid] == target` case and the return. Recommended template.

Lastly, the framework above is the "art"; the "tao" is: **binary thinking aggressively halves the search space using known information**, accelerating enumeration.

This article ensures you write correct binary searches. Real problems rarely ask for raw binary search — see [Binary Search in Action](https://labuladong.online/algo/frequency-interview/binary-search-in-action/) and [More Binary Search Practice](https://labuladong.online/algo/problem-set/binary-search/) for applications.







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Choosing Base Cases and Memo Initial Values](https://labuladong.online/algo/dynamic-programming/memo-fundamental/)
 - [[Practice] Classic Binary Search Problems](https://labuladong.online/algo/problem-set/binary-search/)
 - [Sweeping All Ugly-Number Problems](https://labuladong.online/algo/frequency-interview/ugly-number-summary/)
 - [DP Design: Longest Increasing Subsequence](https://labuladong.online/algo/dynamic-programming/longest-increasing-subsequence/)
 - [Two-Pointer Tricks Sweep 7 Array Problems](https://labuladong.online/algo/essential-technique/array-two-pointers-summary/)
 - [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Practical Framework for Applying Binary Search](https://labuladong.online/algo/frequency-interview/binary-search-in-action/)
 - [Weighted Random Selection](https://labuladong.online/algo/frequency-interview/random-pick-with-weight/)
 - [Extension: Quicksort Details and Applications](https://labuladong.online/algo/practice-in-action/quick-sort/)
 - [Storage Systems: LSM Tree Design Principles](https://labuladong.online/algo/fname.html?fname=lsm-tree)
 - [Beat Algorithms with Algorithms](https://labuladong.online/algo/fname.html?fname=algorithm-in-pdf)
 - [Key Points and Pitfalls in Practice](https://labuladong.online/algo/intro/how-to-learn-algorithms/)
 - [Two Common Factorial Problems](https://labuladong.online/algo/frequency-interview/factorial-problems/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1201. Ugly Number III](https://leetcode.com/problems/ugly-number-iii/?show=1) | [1201. Ugly Number III](https://leetcode.cn/problems/ugly-number-iii/?show=1) | 🟠 |
| [1235. Maximum Profit in Job Scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/?show=1) | [1235. Maximum Profit in Job Scheduling](https://leetcode.cn/problems/maximum-profit-in-job-scheduling/?show=1) | 🔴 |
| [162. Find Peak Element](https://leetcode.com/problems/find-peak-element/?show=1) | [162. Find Peak Element](https://leetcode.cn/problems/find-peak-element/?show=1) | 🟠 |
| [240. Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/?show=1) | [240. Search a 2D Matrix II](https://leetcode.cn/problems/search-a-2d-matrix-ii/?show=1) | 🟠 |
| [33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/?show=1) | [33. Search in Rotated Sorted Array](https://leetcode.cn/problems/search-in-rotated-sorted-array/?show=1) | 🟠 |
| [35. Search Insert Position](https://leetcode.com/problems/search-insert-position/?show=1) | [35. Search Insert Position](https://leetcode.cn/problems/search-insert-position/?show=1) | 🟢 |
| [658. Find K Closest Elements](https://leetcode.com/problems/find-k-closest-elements/?show=1) | [658. Find K Closest Elements](https://leetcode.cn/problems/find-k-closest-elements/?show=1) | 🟠 |
| [74. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/?show=1) | [74. Search a 2D Matrix](https://leetcode.cn/problems/search-a-2d-matrix/?show=1) | 🟠 |
| [792. Number of Matching Subsequences](https://leetcode.com/problems/number-of-matching-subsequences/?show=1) | [792. Number of Matching Subsequences](https://leetcode.cn/problems/number-of-matching-subsequences/?show=1) | 🟠 |
| [793. Preimage Size of Factorial Zeroes Function](https://leetcode.com/problems/preimage-size-of-factorial-zeroes-function/?show=1) | [793. Preimage Size of Factorial Zeroes Function](https://leetcode.cn/problems/preimage-size-of-factorial-zeroes-function/?show=1) | 🔴 |
| [81. Search in Rotated Sorted Array II](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/?show=1) | [81. Search in Rotated Sorted Array II](https://leetcode.cn/problems/search-in-rotated-sorted-array-ii/?show=1) | 🟠 |
| [852. Peak Index in a Mountain Array](https://leetcode.com/problems/peak-index-in-a-mountain-array/?show=1) | [852. Peak Index in a Mountain Array](https://leetcode.cn/problems/peak-index-in-a-mountain-array/?show=1) | 🟠 |
| - | [Sword to Offer 04. Search a 2D Array](https://leetcode.cn/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/?show=1) | 🟠 |
| - | [Sword to Offer 53 - I. Find a Number in a Sorted Array](https://leetcode.cn/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/?show=1) | 🟢 |
| - | [Sword to Offer 53 - II. Missing Number in 0..n-1](https://leetcode.cn/problems/que-shi-de-shu-zi-lcof/?show=1) | 🟢 |
| - | [Sword to Offer II 068. Search Insert Position](https://leetcode.cn/problems/N6YdxV/?show=1) | 🟢 |
| - | [Sword to Offer II 069. Peak of a Mountain Array](https://leetcode.cn/problems/B1IidL/?show=1) | 🟢 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
