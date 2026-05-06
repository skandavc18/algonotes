# Two-Pointer Tricks Sweep 7 Array Problems


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [167. Two Sum II - Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) | [167. Two Sum II - Input Array Is Sorted](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/) | 🟠 |
| [26. Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/) | [26. Remove Duplicates from Sorted Array](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/) | 🟢 |
| [27. Remove Element](https://leetcode.com/problems/remove-element/) | [27. Remove Element](https://leetcode.cn/problems/remove-element/) | 🟢 |
| [283. Move Zeroes](https://leetcode.com/problems/move-zeroes/) | [283. Move Zeroes](https://leetcode.cn/problems/move-zeroes/) | 🟢 |
| [344. Reverse String](https://leetcode.com/problems/reverse-string/) | [344. Reverse String](https://leetcode.cn/problems/reverse-string/) | 🟢 |
| [5. Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/) | [5. Longest Palindromic Substring](https://leetcode.cn/problems/longest-palindromic-substring/) | 🟠 |
| [83. Remove Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list/) | [83. Remove Duplicates from Sorted List](https://leetcode.cn/problems/remove-duplicates-from-sorted-list/) | 🟢 |
| - | [Sword to Offer 57. Two Numbers Summing to s](https://leetcode.cn/problems/he-wei-sde-liang-ge-shu-zi-lcof/) | 🟢 |
| - | [Sword to Offer II 006. Two-Sum in a Sorted Array](https://leetcode.cn/problems/kLl5u1/) | 🟢 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Array Basics](https://labuladong.online/algo/data-structure-basic/array-basic/)
> - [Six Linked-List Problem Patterns](https://labuladong.online/algo/essential-technique/linked-list-skills-summary/)


Two-pointer techniques come up often when working with arrays and linked lists. They split into two flavors: **left/right pointers** and **fast/slow pointers**.

Left/right pointers move toward each other or away from each other; fast/slow pointers move in the same direction at different speeds.

For singly linked lists, most techniques are fast/slow — covered by [Six Linked-List Patterns](https://labuladong.online/algo/essential-technique/linked-list-skills-summary/) (cycle detection, k-th to last, etc.) — they all use a `fast` and a `slow` pointer.

Arrays don't have real pointers, but indices play that role. **This article focuses on array two-pointer algorithms.**

## 1. Fast/Slow Pointers

### In-place Modification

**A common fast/slow pointer use case is in-place array modification.**

LeetCode 26 "Remove Duplicates from Sorted Array":

<Problem slug="remove-duplicates-from-sorted-array" />

```java
int removeDuplicates(int[] nums);
```

What does "in place" mean?

If we weren't required to modify in place, we could allocate a new `int[]`, place the deduplicated elements there, and return it.

But the problem says: in place, no new array — return a length, and the deduplicated elements occupy the prefix of the original array.

The array is sorted, so duplicates are contiguous and easy to find. But deleting each duplicate immediately would shift later elements — O(N) per delete, O(N²) total.

Use fast/slow pointers:

`slow` lags behind, `fast` scouts ahead. When `fast` finds a non-duplicate, assign it to `slow` and advance `slow`.

This keeps `nums[0..slow]` deduplicated. After `fast` traverses `nums`, `nums[0..slow]` is the deduplicated result.

Code:

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums.length == 0) {
            return 0;
        }
        int slow = 0, fast = 0;
        while (fast < nums.length) {
            if (nums[fast] != nums[slow]) {
                slow++;
                // Maintain nums[0..slow] deduplicated
                nums[slow] = nums[fast];
            }
            fast++;
        }
        // Length = index + 1
        return slow + 1;
    }
}
```

<visual slug='remove-duplicates-from-sorted-array' >

Click the visualization below; click <code type="click">while (fast < nums.length)</code> repeatedly to see the two pointers maintain `nums[0..slow]`:

</visual>

A small extension: LeetCode 83 "Remove Duplicates from Sorted List". Same idea on a sorted singly linked list:

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) return null;
        ListNode slow = head, fast = head;
        while (fast != null) {
            if (fast.val != slow.val) {
                // nums[slow] = nums[fast];
                slow.next = fast;
                // slow++;
                slow = slow.next;
            }
            // fast++
            fast = fast.next;
        }
        // Disconnect from any trailing duplicates
        slow.next = null;
        return head;
    }
}
```

The visualization:


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/remove-duplicates-from-sorted-list/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🥳 Animated Code Visualization 🥳</strong>
</summary>
</details>
</a>
<hr/>

> [!NOTE]
> A reader may ask: the duplicate nodes aren't actually deleted — they're just dangling. Is that OK?
> 
> Depends on the language. Java/Python's GC reclaims unreachable nodes; C++ requires manual `delete`.
> 
> For algorithm thinking, the fast/slow pointer technique is what matters.

**Beyond deduplication, you may be asked to "remove" specific elements in place.**

LeetCode 27 "Remove Element":

<Problem slug="remove-element" />

```java
// Signature
int removeElement(int[] nums, int val);
```

Remove every occurrence of `val` from `nums`. Same fast/slow pattern:

If `fast` sees `val`, skip; else copy to `slow` and advance `slow`.

Code:

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int fast = 0, slow = 0;
        while (fast < nums.length) {
            if (nums[fast] != val) {
                nums[slow] = nums[fast];
                slow++;
            }
            fast++;
        }
        return slow;
    }
}
```

<visual slug='remove-element' >

Click <code type="click">while (fast !== null)</code> repeatedly to see two pointers maintain `nums[0..slow]`:

</visual>

A small detail vs. dedup: here we assign first, then `slow++`, ensuring `nums[0..slow-1]` excludes `val`. Final length is `slow`.

Now LeetCode 283 "Move Zeroes":

Given `nums`, move all zeros to the end **in place**:

```java
void moveZeroes(int[] nums);
```

`nums = [0,1,4,0,2]` becomes `[1,4,2,0,0]`.

Combine with the previous:

Removing all 0s, then filling the remaining slots with 0:

```java
class Solution {
    public void moveZeroes(int[] nums) {
        // Remove all 0s; return length of zero-free prefix
        int p = removeElement(nums, 0);
        // Fill nums[p..] with 0
        for (; p < nums.length; p++) {
            nums[p] = 0;
        }
    }

    public int removeElement(int[] nums, int val) {
        // See above
    }
}
```

<visual slug='move-zeroes' >

Click <code type="click">while (fast < nums.length)</code> repeatedly to see the fast/slow pointers; then click <code type="click">nums[p] = 0;</code> repeatedly to fill the rest with 0s:

</visual>

That covers in-place modification.

### Sliding Window

Another big class of fast/slow pointer problems is the sliding window. See [Sliding Window Framework](https://labuladong.online/algo/essential-technique/sliding-window-framework/) for the framework:

```java
// Sliding-window framework (pseudocode)
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

The fast/slow nature: `left` lags, `right` leads; the segment between them is the "window". Expand and shrink to solve the problem.

## 2. Common Left/Right Pointer Algorithms

### Binary Search

Detail in [Binary Search Framework](https://labuladong.online/algo/essential-technique/binary-search-framework/). Here, just the simplest form:

```java
int binarySearch(int[] nums, int target) {
    // Two pointers moving toward each other
    int left = 0, right = nums.length - 1;
    while(left <= right) {
        int mid = (right + left) / 2;
        if(nums[mid] == target)
            return mid; 
        else if (nums[mid] < target)
            left = mid + 1; 
        else if (nums[mid] > target)
            right = mid - 1;
    }
    return -1;
}
```

### nSum

LeetCode 167 "Two Sum II":

<Problem slug="two-sum-ii-input-array-is-sorted" />

When the array is sorted, think two pointers. Adjust `left` and `right` to tweak `sum`:

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        // Two pointers moving toward each other
        int left = 0, right = numbers.length - 1;
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            if (sum == target) {
                // Indices are 1-based per the problem
                return new int[]{left + 1, right + 1};
            } else if (sum < target) {
                // Increase sum
                left++;
            } else if (sum > target) {
                // Decrease sum
                right--;
            }
        }
        return new int[]{-1, -1};
    }
}
```

A general nSum approach using similar pointers is in [One Function Sweeps All nSum Problems](https://labuladong.online/algo/practice-in-action/nsum/).

### Reverse an Array

`reverse` is built in many languages, but the underlying idea is simple. LeetCode 344 "Reverse String":

```java
void reverseString(char[] s) {
    // Two pointers moving toward each other
    int left = 0, right = s.length - 1;
    while (left < right) {
        // Swap s[left] and s[right]
        char temp = s[left];
        s[left] = s[right];
        s[right] = temp;
        left++;
        right--;
    }
}
```

For more reversal variants, see [Tricks for Traversing 2D Arrays](https://labuladong.online/algo/practice-in-action/2d-array-traversal-summary/).

### Palindrome Detection

A palindrome reads the same forwards and backwards (e.g., `aba`, `abba`); `abac` is not.

Naturally a left/right pointer problem — to check whether a string is a palindrome:

```java
boolean isPalindrome(String s) {
    // Two pointers moving toward each other
    int left = 0, right = s.length() - 1;
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

A bigger challenge: find the longest palindromic substring. LeetCode 5:

<Problem slug="longest-palindromic-substring" />

```java
String longestPalindrome(String s);
```

The challenge: the palindrome length may be odd or even. **Use a "center-out" two-pointer expansion.**

Odd-length palindromes have a single center; even-length ones can be considered to have two. Helper:

```java
// Find the longest palindrome centered at s[l] and s[r]
String palindrome(String s, int l, int r) {
    // Bounds check
    while (l >= 0 && r < s.length()
            && s.charAt(l) == s.charAt(r)) {
        // Expand outward
        l--; r++;
    }
    // Return the longest palindrome centered between (l, r)
    return s.substring(l + 1, r);
}
```

Same `l` and `r` finds odd-length palindromes; adjacent `l`, `r` finds even-length.

Then the main loop:

```python
for 0 <= i < len(s):
    Find the longest palindrome centered at s[i]
    Find the longest palindrome centered between s[i] and s[i+1]
    Update the answer
```

Code:

```java
String longestPalindrome(String s) {
    String res = "";
    for (int i = 0; i < s.length(); i++) {
        // Longest palindrome centered at s[i]
        String s1 = palindrome(s, i, i);
        // Longest palindrome centered between s[i] and s[i+1]
        String s2 = palindrome(s, i, i + 1);
        // res = longest(res, s1, s2)
        res = res.length() > s1.length() ? res : s1;
        res = res.length() > s2.length() ? res : s2;
    }
    return res;
}
```

<visual slug='longest-palindromic-substring' >

Click <code type="click">while (l >= 0 && r < s.length && s[l] === s[r])</code> repeatedly to see the pointers expand outward:

</visual>

Note this differs from earlier left/right pointer problems: those moved toward each other; this one expands from a center. Specific to palindromes, but I still bucket it under left/right pointers.

That covers array two-pointer techniques. For more, see [More Classic Two-Pointer Array Problems](https://labuladong.online/algo/problem-set/array-two-pointers/).


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Classic Binary Search Problems](https://labuladong.online/algo/problem-set/binary-search/)
 - [[Practice] Hash Table Problems](https://labuladong.online/algo/problem-set/hash-table/)
 - [[Practice] Classic Backtracking Problems I](https://labuladong.online/algo/problem-set/backtrack-i/)
 - [[Practice] Classic Backtracking Problems III](https://labuladong.online/algo/problem-set/backtrack-iii/)
 - [[Practice] Math Trick Problems](https://labuladong.online/algo/problem-set/math-tricks/)
 - [[Practice] Classic Two-Pointer Array Problems](https://labuladong.online/algo/problem-set/array-two-pointers/)
 - [[Practice] More Classic Design Problems](https://labuladong.online/algo/problem-set/ds-design/)
 - [[Practice] Classic Two-Pointer Linked-List Problems](https://labuladong.online/algo/problem-set/linkedlist-two-pointers/)
 - [One Method Sweeps nSum Problems](https://labuladong.online/algo/practice-in-action/nsum/)
 - [Tricks for Traversing 2D Arrays](https://labuladong.online/algo/practice-in-action/2d-array-traversal-summary/)
 - [DP Subsequence Problem Template](https://labuladong.online/algo/dynamic-programming/subsequence-problem/)
 - [Bucket Sort](https://labuladong.online/algo/data-structure-basic/bucket-sort/)
 - [Determining a Palindrome Linked List](https://labuladong.online/algo/data-structure/palindrome-linked-list/)
 - [Solving the Trapping Rain Water Problem Efficiently](https://labuladong.online/algo/frequency-interview/trapping-rain-water/)
 - [Quicksort Using Preorder Position](https://labuladong.online/algo/data-structure-basic/quick-sort/)
 - [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Sweep-Line Technique: Meeting Rooms](https://labuladong.online/algo/frequency-interview/scan-line-technique/)
 - [Extension: Array Deduplication (Hard)](https://labuladong.online/algo/frequency-interview/remove-duplicate-letters/)
 - [Sliding Window Core Code Template](https://labuladong.online/algo/essential-technique/sliding-window-framework/)
 - [Beat Algorithms with Algorithms](https://labuladong.online/algo/fname.html?fname=algorithm-in-pdf)
 - [Algorithm Decisions Behind Tian Ji's Horse Race](https://labuladong.online/algo/practice-in-action/advantage-shuffle/)
 - [Key Points and Pitfalls in Practice](https://labuladong.online/algo/intro/how-to-learn-algorithms/)
 - [Practical Guide to Time/Space Complexity Analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/)
 - ["Trick" Patterns for Algorithm Quizzes](https://labuladong.online/algo/other-skills/tips-in-exam/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1. Two Sum](https://leetcode.com/problems/two-sum/?show=1) | [1. Two Sum](https://leetcode.cn/problems/two-sum/?show=1) | 🟢 |
| [125. Valid Palindrome](https://leetcode.com/problems/valid-palindrome/?show=1) | [125. Valid Palindrome](https://leetcode.cn/problems/valid-palindrome/?show=1) | 🟢 |
| [131. Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/?show=1) | [131. Palindrome Partitioning](https://leetcode.cn/problems/palindrome-partitioning/?show=1) | 🟠 |
| [267. Palindrome Permutation II](https://leetcode.com/problems/palindrome-permutation-ii/?show=1)🔒 | [267. Palindrome Permutation II](https://leetcode.cn/problems/palindrome-permutation-ii/?show=1)🔒 | 🟠 |
| [281. Zigzag Iterator](https://leetcode.com/problems/zigzag-iterator/?show=1)🔒 | [281. Zigzag Iterator](https://leetcode.cn/problems/zigzag-iterator/?show=1)🔒 | 🟠 |
| [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/?show=1) | [42. Trapping Rain Water](https://leetcode.cn/problems/trapping-rain-water/?show=1) | 🔴 |
| [543. Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/?show=1) | [543. Diameter of Binary Tree](https://leetcode.cn/problems/diameter-of-binary-tree/?show=1) | 🟢 |
| [658. Find K Closest Elements](https://leetcode.com/problems/find-k-closest-elements/?show=1) | [658. Find K Closest Elements](https://leetcode.cn/problems/find-k-closest-elements/?show=1) | 🟠 |
| [75. Sort Colors](https://leetcode.com/problems/sort-colors/?show=1) | [75. Sort Colors](https://leetcode.cn/problems/sort-colors/?show=1) | 🟠 |
| [80. Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/?show=1) | [80. Remove Duplicates from Sorted Array II](https://leetcode.cn/problems/remove-duplicates-from-sorted-array-ii/?show=1) | 🟠 |
| [82. Remove Duplicates from Sorted List II](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/?show=1) | [82. Remove Duplicates from Sorted List II](https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/?show=1) | 🟠 |
| [9. Palindrome Number](https://leetcode.com/problems/palindrome-number/?show=1) | [9. Palindrome Number](https://leetcode.cn/problems/palindrome-number/?show=1) | 🟢 |
| - | [Sword to Offer 21. Move Odd Numbers Before Even Numbers](https://leetcode.cn/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/?show=1) | 🟢 |
| - | [Sword to Offer 57. Two Numbers Summing to s](https://leetcode.cn/problems/he-wei-sde-liang-ge-shu-zi-lcof/?show=1) | 🟢 |
| - | [Sword to Offer II 018. Valid Palindrome](https://leetcode.cn/problems/XltzEq/?show=1) | 🟢 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
