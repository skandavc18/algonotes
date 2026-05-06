# Finding Missing and Duplicate Elements Together



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [645. Set Mismatch](https://leetcode.com/problems/set-mismatch/) | [645. Set Mismatch](https://leetcode.cn/problems/set-mismatch/) | 🟢 |

**-----------**



A problem that looks simple but is quite clever — finding both the missing and duplicate elements. The earlier article [Common Bit Operations](https://labuladong.online/algo/frequency-interview/bitwise-operation/) covered a similar problem with different techniques.

LeetCode 645 "Set Mismatch":

Given an array `nums` of length `N` originally containing the `N` numbers `[1..N]` (out of order). Some error caused one element to appear twice, also causing another to be missing. Write an algorithm to find the duplicate and missing values.

```java
// Returns two numbers: {dup, missing}
int[] findErrorNums(int[] nums);
```

E.g., `nums = [1,2,2,4]` → `[2,3]`.

The straightforward solution: scan once to count occurrences with a hash table, then scan `[1..N]` to find which is duplicated and which is missing. Done.

But that uses O(N) extra space. Given the conditions — exactly one duplicate, exactly one missing — there must be a clever trick.

The O(N) scan can't be avoided; can we use O(1) extra space?







## Idea

The key observation: each element corresponds in some way to an index.

Let's reframe — **temporarily treat `nums` as containing `[0..N-1]`, so each element matches an index exactly. Easier to reason about.**

If there were no duplicates or missing, every element would correspond to a unique index.

Now: one duplicate, one missing. **Result: one index has two elements pointing to it; another index has none.**

If we can find the index that is "hit twice", that's the duplicate; the index "hit zero times" is the missing.

How do we count hits without extra space? The trick:

**Negate the element at each visited index** to mark it as visited. The GIF:

![](https://labuladong.online/algo/images/dupmissing/1.gif)

If `4` is the duplicate, the value at index `4` is already negative when we hit it again:

![](https://labuladong.online/algo/images/dupmissing/2.jpg)

If `3` is missing, the value at index `3` stays positive:

![](https://labuladong.online/algo/images/dupmissing/3.jpg)

In code:

```java
int[] findErrorNums(int[] nums) {
    int n = nums.length;
    int dup = -1;
    for (int i = 0; i < n; i++) {
        int index = Math.abs(nums[i]);
        // nums[index] < 0 → duplicate visit
        if (nums[index] < 0)
            dup = Math.abs(nums[i]);
        else
            nums[index] *= -1;
    }

    int missing = -1;
    for (int i = 0; i < n; i++)
        // nums[i] > 0 → never visited
        if (nums[i] > 0)
            missing = i;
    
    return new int[]{dup, missing};
}
```

That's the gist. We assumed `[0..N-1]`; the actual problem uses `[1..N]`, so two small tweaks:

```java
class Solution {
    public int[] findErrorNums(int[] nums) {
        int n = nums.length;
        int dup = -1;
        for (int i = 0; i < n; i++) {
            // Elements start from 1 now
            int index = Math.abs(nums[i]) - 1;
            if (nums[index] < 0)
                dup = Math.abs(nums[i]);
            else
                nums[index] *= -1;
        }

        int missing = -1;
        for (int i = 0; i < n; i++)
            if (nums[i] > 0)
                // Convert index to element
                missing = i + 1;

        return new int[]{dup, missing};
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/set-mismatch/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🥳 Animated Code Visualization 🥳</strong>
</summary>
</details>
</a>
<hr/>



Why must elements start from 1? If 0 were valid, negating 0 doesn't change it — we couldn't tell whether 0 was visited.

## Wrap-Up

For these problems, **the key is that elements pair with indices; common methods are sort, XOR, and mapping.**

Mapping: as analyzed — pair elements with indices and use signs to mark visits.

Sorting: imagine the elements sorted; mismatches between index and element reveal the answer.

XOR: use `a ^ a = 0`, `a ^ 0 = a` — XOR all indices and elements; pairs cancel, leaving the mismatch. See [Common Bit Operations](https://labuladong.online/algo/frequency-interview/bitwise-operation/).







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Hash Table Problems](https://labuladong.online/algo/problem-set/hash-table/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [442. Find All Duplicates in an Array](https://leetcode.com/problems/find-all-duplicates-in-an-array/?show=1) | [442. Find All Duplicates in an Array](https://leetcode.cn/problems/find-all-duplicates-in-an-array/?show=1) | 🟠 |
| [448. Find All Numbers Disappeared in an Array](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/?show=1) | [448. Find All Numbers Disappeared in an Array](https://leetcode.cn/problems/find-all-numbers-disappeared-in-an-array/?show=1) | 🟢 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
