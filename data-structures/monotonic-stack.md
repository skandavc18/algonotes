# Solving Three Examples with the Monotonic Stack Algorithm Template



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [496. Next Greater Element I](https://leetcode.com/problems/next-greater-element-i/) | [496. Next Greater Element I](https://leetcode.cn/problems/next-greater-element-i/) | 🟢 |
| [503. Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/) | [503. Next Greater Element II](https://leetcode.cn/problems/next-greater-element-ii/) | 🟠 |
| [739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/) | [739. Daily Temperatures](https://leetcode.cn/problems/daily-temperatures/) | 🟠 |
| - | [Sword to Offer II 038. Daily Temperatures](https://leetcode.cn/problems/iIQa4I/) | 🟠 |

**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Array Basics](https://labuladong.online/algo/data-structure-basic/array-basic/)
> - [Linked List Basics](https://labuladong.online/algo/data-structure-basic/linkedlist-basic/)
> - [Queue/Stack Basics](https://labuladong.online/algo/data-structure-basic/queue-stack-basic/)

A stack is a very simple data structure with last-in-first-out logic, suited to certain problems such as the function call stack. A monotonic stack is just a stack with some clever logic so that after each new element is pushed, the elements in the stack remain ordered (monotonically increasing or decreasing).

Sounds a bit like a heap? It isn't — monotonic stacks aren't widely used and only handle a class of typical problems, e.g. "next greater element", "previous smaller element", etc. This article uses the monotonic-stack template to solve "next greater element" problems and discusses how to handle a "circular array". I'll cover other variants and classic problems in the next article, [Monotonic-Stack Variants and Classic Problems](https://labuladong.online/algo/problem-set/monotonic-stack/).

## Monotonic-Stack Template

Here's the problem: given an array `nums`, return an array of the same length whose entries are the next greater element. If there is none, store -1. Signature:

```java
int[] calculateGreaterElement(int[] nums);
```

For example, given `nums = [2,1,2,4,3]`, return `[4,2,4,-1,-1]`. The first 2's next greater is 4; 1's next greater is 2; the second 2's next greater is 4; nothing greater after 4 → -1; nothing greater after 3 → -1.

The brute-force solution is easy: scan to the right of each element to find the first greater one. But brute force is $O(n^2)$.

We can think about this abstractly: imagine each element of the array as a person standing in line, and their value as their height. They face you. To find the next greater element of "2", just look at "2" — the first person you can see behind it is the next greater element, because shorter people are blocked by "2".

![](https://labuladong.online/algo/images/monotonic-stack/1.jpeg)

Easy to picture. Here's the code:

```java
int[] calculateGreaterElement(int[] nums) {
    int n = nums.length;
    // Array to store the answer
    int[] res = new int[n];
    Stack<Integer> s = new Stack<>(); 
    // Push from right to left
    for (int i = n - 1; i >= 0; i--) {
        // Compare heights
        while (!s.isEmpty() && s.peek() <= nums[i]) {
            // Shorter people step aside — they're blocked anyway...
            s.pop();
        }
        // The next greater element after nums[i]
        res[i] = s.isEmpty() ? -1 : s.peek();
        s.push(nums[i]);
    }
    return res;
}
```

That's the monotonic-queue (here, stack) template. The for loop iterates right to left because we use a stack: pushing in reverse is, effectively, popping forward. The while loop discards any elements between two "tall ones" — they're irrelevant because they're shadowed by a taller element ahead and can never be the next greater for any future element.

The time complexity isn't obvious. With a for loop nested with a while loop, you might think $O(n^2)$, but it is actually $O(n)$.

Analyze it as a whole: there are `n` elements, each is pushed exactly once and popped at most once — no redundant work. Total work is proportional to `n`, hence $O(n)$.

## Variants

The monotonic-stack code is short. Let's apply it.

### 496. Next Greater Element I

A simple variant, LeetCode 496 "Next Greater Element I":

<Problem slug="next-greater-element-i" />

You're given two arrays `nums1` and `nums2`; find the next greater element of each element of `nums1` in `nums2`. Signature:

```java
int[] nextGreaterElement(int[] nums1, int[] nums2)
```

A small tweak of our earlier code does it. Since `nums1` is a subset of `nums2`, first compute the next greater element for every element of `nums2` and store it in a map; then look up `nums1` in the map:

```java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        // Next greater element of each element in nums2
        int[] greater = calculateGreaterElement(nums2);
        // Convert to map: x -> next greater of x
        HashMap<Integer, Integer> greaterMap = new HashMap<>();
        for (int i = 0; i < nums2.length; i++) {
            greaterMap.put(nums2[i], greater[i]);
        }
        // nums1 is a subset of nums2, so we can use greaterMap
        int[] res = new int[nums1.length];
        for (int i = 0; i < nums1.length; i++) {
            res[i] = greaterMap.get(nums1[i]);
        }
        return res;
    }

    int[] calculateGreaterElement(int[] nums) {
        // See above
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/next-greater-element-i/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Animated Code Visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>




### 739. Daily Temperatures

LeetCode 739 "Daily Temperatures":

Given `temperatures` (the temperatures over recent days), return an array of the same length giving, for each day, the number of days you must wait to get a warmer temperature; if no such day exists, fill with 0. Signature:

```java
int[] dailyTemperatures(int[] temperatures);
```

For example, given `temperatures = [73,74,75,71,69,76]`, return `[1,1,3,2,1,0]`. Day 1 is 73°F; day 2 is 74°F (warmer), so day 1 needs to wait 1 day. Same idea for the rest.

Essentially the next-greater problem again — except now the answer is the index distance to the next greater element, not its value.

Same idea, slight modification of the monotonic-stack template:

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        int[] res = new int[n];
        // Store indices instead of values
        Stack<Integer> s = new Stack<>(); 
        // Monotonic-stack template
        for (int i = n - 1; i >= 0; i--) {
            while (!s.isEmpty() && temperatures[s.peek()] <= temperatures[i]) {
                s.pop();
            }
            // Index distance
            res[i] = s.isEmpty() ? 0 : (s.peek() - i); 
            // Push the index, not the value
            s.push(i); 
        }
        return res;
    }
}
```

Monotonic stack is covered. Now the next focus: handling a "circular array".

## How to Handle a Circular Array

Same problem, but the array is circular. LeetCode 503 "Next Greater Element II" is exactly this: given a circular array, compute each element's next greater element.

For example, given `[2,1,2,4,3]`, return `[4,2,4,-1,4]` — thanks to the circular property, **the last element 3 wraps around and finds 4 as its next greater**.

If you've read [Circular Array Tricks](https://labuladong.online/algo/data-structure-basic/cycle-array/) in the basics chapter, this is familiar — we generally use the modulo operator `%` to simulate the circular behavior:

```java
int[] arr = {1,2,3,4,5};
int n = arr.length, index = 0;
while (true) {
    // Loop through the circular array
    print(arr[index % n]);
    index++;
}
```

We still use the monotonic-stack template, but the trick here is, given input `[2,1,2,4,3]`, how does the last element 3 see element 4 as its next greater?

**The common pattern is to double the array length**:

![](https://labuladong.online/algo/images/monotonic-stack/2.jpeg)

Now element 3 finds element 4, and the others are still computed correctly.

The simplest implementation is to actually build the doubled array and apply the template. But **we can avoid building a new array by using the circular trick to simulate a doubled length**:

```java
class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];
        Stack<Integer> s = new Stack<>();
        // Double the length to simulate the circular array
        for (int i = 2 * n - 1; i >= 0; i--) {
            // Take i modulo n; otherwise same as the template
            while (!s.isEmpty() && s.peek() <= nums[i % n]) {
                s.pop();
            }
            res[i % n] = s.isEmpty() ? -1 : s.peek();
            s.push(nums[i % n]);
        }
        return res;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/next-greater-element-ii/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>



That cleanly solves the circular-array case in $O(N)$ time.

A few questions to close: the template here is `nextGreaterElement`, computing each element's next greater. What if a problem asks for the previous greater, or the previous greater-or-equal? How would you adapt the template? And in real problems, you won't be asked for the next/previous greater/smaller directly; how do you reformulate the problem into a monotonic-stack one?

I'll compare several monotonic-stack variants and present classic problems in [Monotonic-Stack Variants and Problems](https://labuladong.online/algo/problem-set/monotonic-stack/). For more data-structure-design problems, see [Classic Data-Structure Design Problems](https://labuladong.online/algo/problem-set/ds-design/).







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Monotonic-Stack Variants and Classic Problems](https://labuladong.online/algo/problem-set/monotonic-stack/)
 - [[Practice] General Monotonic-Queue Implementation and Classic Problems](https://labuladong.online/algo/problem-set/monotonic-queue/)
 - [One Method to Sweep LeetCode House Robber Problems](https://labuladong.online/algo/dynamic-programming/house-robber/)
 - [Solving Sliding-Window Problems with the Monotonic Queue](https://labuladong.online/algo/data-structure/monotonic-queue/)
 - [Common Bit Operations](https://labuladong.online/algo/frequency-interview/bitwise-operation/)
 - [Extension: Array Deduplication (Hard Version)](https://labuladong.online/algo/frequency-interview/remove-duplicate-letters/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1019. Next Greater Node In Linked List](https://leetcode.com/problems/next-greater-node-in-linked-list/?show=1) | [1019. Next Greater Node In Linked List](https://leetcode.cn/problems/next-greater-node-in-linked-list/?show=1) | 🟠 |
| [1944. Number of Visible People in a Queue](https://leetcode.com/problems/number-of-visible-people-in-a-queue/?show=1) | [1944. Number of Visible People in a Queue](https://leetcode.cn/problems/number-of-visible-people-in-a-queue/?show=1) | 🔴 |
| [402. Remove K Digits](https://leetcode.com/problems/remove-k-digits/?show=1) | [402. Remove K Digits](https://leetcode.cn/problems/remove-k-digits/?show=1) | 🟠 |
| [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/?show=1) | [42. Trapping Rain Water](https://leetcode.cn/problems/trapping-rain-water/?show=1) | 🔴 |
| [901. Online Stock Span](https://leetcode.com/problems/online-stock-span/?show=1) | [901. Online Stock Span](https://leetcode.cn/problems/online-stock-span/?show=1) | 🟠 |
| [918. Maximum Sum Circular Subarray](https://leetcode.com/problems/maximum-sum-circular-subarray/?show=1) | [918. Maximum Sum Circular Subarray](https://leetcode.cn/problems/maximum-sum-circular-subarray/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
