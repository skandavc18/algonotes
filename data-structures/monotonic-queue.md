# Solving Sliding-Window Problems with the Monotonic Queue



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) | [239. Sliding Window Maximum](https://leetcode.cn/problems/sliding-window-maximum/) | 🔴 |
| [Sword to Offer 59 - II. Maximum Value of a Queue (LCOF)](https://leetcode.com/problems/dui-lie-de-zui-da-zhi-lcof/) | [Sword to Offer 59 - II. Maximum Value of a Queue](https://leetcode.cn/problems/dui-lie-de-zui-da-zhi-lcof/) | 🟠 |

**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Array Basics](https://labuladong.online/algo/data-structure-basic/array-basic/)
> - [Linked List Basics](https://labuladong.online/algo/data-structure-basic/linkedlist-basic/)
> - [Queue/Stack Basics](https://labuladong.online/algo/data-structure-basic/queue-stack-basic/)

The earlier article [Solving Three Problems with the Monotonic Stack](https://labuladong.online/algo/data-structure/monotonic-stack/) introduced the monotonic-stack data structure. This article presents a similar one — the "monotonic queue".

You may not have heard of it, but it's nothing complicated: it's just a "queue" that uses a clever trick so that the elements inside are always monotonically increasing (or decreasing).

Why invent this structure? Mainly to handle the following scenario:

**Given an array `window` whose maximum is `A`, if a number `B` is added to `window`, comparing `A` and `B` immediately yields the new max. But if a number is removed from `window`, the max isn't immediately known: if the removed number happened to be `A`, you must scan all of `window` to find a new max.**

This is common, and you might think a monotonic queue isn't needed — for example, [priority queues (binary heap)](https://labuladong.online/algo/data-structure-basic/binary-heap-basic/) are designed to track the running max/min: build a max- (or min-) heap, get the top quickly.

For tracking only the max/min, priority queues do the job. But they don't preserve the queue's **temporal** "first-in-first-out" order — the heap orders by value, so dequeue order is by element size, with no relation to insertion order.

So we need a new queue structure that maintains FIFO time order while also correctly maintaining the max of all elements — the monotonic queue.

Monotonic queues mainly help with sliding-window problems. The earlier article [Sliding Window Core Framework](https://labuladong.online/algo/essential-technique/sliding-window-framework/) treated sliding window as part of two-pointer techniques, but slightly more complex sliding-window problems can't be solved by two pointers alone — we need a more advanced data structure.

In particular, in the problems covered in [Sliding Window Core Framework](https://labuladong.online/algo/essential-technique/sliding-window-framework/), at each `right++` (window grows) and `left++` (window shrinks), you can decide the answer based solely on the element entering or leaving.

But in the maximum-of-window scenario above, you cannot update the max from the removed element alone — unless you scan everything, which raises the time complexity (we don't want this).

LeetCode 239 "Sliding Window Maximum" is a standard sliding-window problem:

Given an array `nums` and a positive integer `k`, a window of size `k` slides over `nums` from left to right. Output the maximum among the `k` elements at each window position.

Signature:

```java
int[] maxSlidingWindow(int[] nums, int k);
```

A sample from LeetCode:

```
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
Explanation:
Window position                 Max
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```



We will use a monotonic queue to compute the max of each window in $O(1)$ amortized time, giving a linear-time algorithm overall.

### 1. Solution Framework

Before introducing the monotonic-queue API, compare a [standard queue](https://labuladong.online/algo/data-structure-basic/queue-stack-basic/) with our monotonic queue:

```java
// Standard queue API
class Queue {
    // enqueue: add element n at the tail
    void push(int n);
    // dequeue: remove the head element
    void pop();
}

// Monotonic-queue API
class MonotonicQueue {
    // Add element n at the tail
    void push(int n);
    // Return the current max in the queue
    int max();
    // If the head element is n, remove it
    void pop(int n);
}
```

The implementation will differ from a regular queue, but assume for now that all these operations are O(1). First, the framework for the sliding-window problem:

```java
int[] maxSlidingWindow(int[] nums, int k) {
    MonotonicQueue window = new MonotonicQueue();
    List<Integer> res = new ArrayList<>();
    
    for (int i = 0; i < nums.length; i++) {
        if (i < k - 1) {
            // Fill the first k - 1 window slots
            window.push(nums[i]);
        } else {
            // The window starts to slide
            // Add the new element
            window.push(nums[i]);
            // Record the max of the current window
            res.add(window.max());
            // Remove the oldest element
            window.pop(nums[i - k + 1]);
        }
    }
    // Convert List to int[] for the return value
    int[] arr = new int[res.size()];
    for (int i = 0; i < res.size(); i++) {
        arr[i] = res.get(i);
    }
    return arr;
}
```

![](https://labuladong.online/algo/images/monotonic-queue/1.png)



The idea is simple. Now the main act: implementing the monotonic queue.

### 2. Implementing the Monotonic Queue

Watching the sliding window in action, you can see that implementing a monotonic queue requires a structure supporting fast insert/remove at both ends — a [doubly linked list](https://labuladong.online/algo/data-structure-basic/linkedlist-basic/) fits.

The core idea is similar to the monotonic stack: `push` still appends at the tail, but first removes any preceding elements smaller than itself:

```java
class MonotonicQueue {
    // Doubly linked list, fast head/tail insert/remove
    // Maintained so elements are monotonically increasing from tail to head
    private LinkedList<Integer> maxq = new LinkedList<>();

    // Push n at the tail, preserving the monotonic property
    public void push(int n) {
        // Remove all preceding elements smaller than n
        while (!maxq.isEmpty() && maxq.getLast() < n) {
            maxq.pollLast();
        }
        maxq.addLast(n);
    }
}
```

Imagine the value as the person's weight: a heavier person flattens lighter people in front until a heavier or equal one is found.

![](https://labuladong.online/algo/images/monotonic-queue/3.png)

If every element does this on insertion, the queue stays in **monotonically decreasing** order from head to tail. Then `max` is easy — return the head; `pop` also operates on the head: if the head equals the element to remove `n`, remove it:

```java
class MonotonicQueue {
    // Earlier code omitted for brevity...

    public int max() {
        // The head is the max
        return maxq.getFirst();
    }

    public void pop(int n) {
        if (n == maxq.getFirst()) {
            maxq.pollFirst();
        }
    }
}
```

`pop` checks `n == maxq.getFirst()` because the element we want to remove may have already been "flattened away" during a `push`, so it might not be present — in that case we don't need to remove anything:

![](https://labuladong.online/algo/images/monotonic-queue/2.png)

That completes the design. Full solution:

```java
// Monotonic-queue implementation
class MonotonicQueue {
    LinkedList<Integer> maxq = new LinkedList<>();
    public void push(int n) {
        // Remove all elements smaller than n
        while (!maxq.isEmpty() && maxq.getLast() < n) {
            maxq.pollLast();
        }
        // Then push n at the tail
        maxq.addLast(n);
    }
    
    public int max() {
        return maxq.getFirst();
    }
    
    public void pop(int n) {
        if (n == maxq.getFirst()) {
            maxq.pollFirst();
        }
    }
}

class Solution {
    int[] maxSlidingWindow(int[] nums, int k) {
        MonotonicQueue window = new MonotonicQueue();
        List<Integer> res = new ArrayList<>();
        
        for (int i = 0; i < nums.length; i++) {
            if (i < k - 1) {
                // Fill the first k - 1 of the window
                window.push(nums[i]);
            } else {
                // Window slides; add new number
                window.push(nums[i]);
                // Record the current max
                res.add(window.max());
                // Remove the oldest number
                window.pop(nums[i - k + 1]);
            }
        }
        // Convert to int[]
        int[] arr = new int[res.size()];
        for (int i = 0; i < res.size(); i++) {
            arr[i] = res.get(i);
        }
        return arr;
    }
}
```

A small detail: we used Java's `LinkedList` for `MonotonicQueue` because it supports fast head/tail insert/remove; `res` uses `ArrayList` because we later index into it, where arrays are better. Implementations in other languages should pay attention to the same details.

You may wonder about complexity: `push` contains a `while` loop, with worst case $O(N)$. With the outer `for`, doesn't this make it $O(N^2)$?

This is an amortized analysis (see [Practical Time/Space Complexity Analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/)):

A single `push` is $O(N)$ in the worst case but $O(1)$ on average. We typically use average complexity to characterize APIs, so the algorithm is $O(N)$, not $O(N^2)$.

You can also analyze it holistically: the algorithm pushes and pops each element of `nums` **at most once**. No element enters/leaves the window more than once, so the total work is $O(N)$.

Space complexity is the window size, $O(k)$.







### Extensions

Some questions to ponder:

1. The `MonotonicQueue` here only implements `max`. Can you add a `min` method that returns the min of all elements in the queue in $O(1)$ time?

2. The `pop` method requires a parameter, which is inelegant and inconsistent with the standard queue API. Fix this defect.

3. Implement `size`, returning the count of elements in the monotonic queue (note: each `push` may delete elements from the underlying `q`, so the size of `q` is not the size of the monotonic queue).

That is, can you implement a general-purpose monotonic queue:

```java
// General-purpose monotonic queue, efficient max and min
class MonotonicQueue<E extends Comparable<E>> {

    // Standard queue API: enqueue at the tail
    public void push(E elem);

    // Standard queue API: dequeue from the head, FIFO
    public E pop();

    // Standard queue API: return number of elements
    public int size();

    // Monotonic-queue API: max in O(1)
    public E max();

    // Monotonic-queue API: min in O(1)
    public E min();
}
```

I'll provide a general-purpose implementation and classic problems in [General-Purpose Monotonic Queue and Applications](https://labuladong.online/algo/problem-set/monotonic-queue/). For more data-structure-design problems, see [Classic Data-Structure Design Problems](https://labuladong.online/algo/problem-set/ds-design/).







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] General-Purpose Monotonic Queue and Classic Problems](https://labuladong.online/algo/problem-set/monotonic-queue/)
 - [Practical Guide to Time/Space Complexity Analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1425. Constrained Subsequence Sum](https://leetcode.com/problems/constrained-subsequence-sum/?show=1) | [1425. Constrained Subsequence Sum](https://leetcode.cn/problems/constrained-subsequence-sum/?show=1) | 🔴 |
| [1696. Jump Game VI](https://leetcode.com/problems/jump-game-vi/?show=1) | [1696. Jump Game VI](https://leetcode.cn/problems/jump-game-vi/?show=1) | 🟠 |
| [862. Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/?show=1) | [862. Shortest Subarray with Sum at Least K](https://leetcode.cn/problems/shortest-subarray-with-sum-at-least-k/?show=1) | 🔴 |
| [918. Maximum Sum Circular Subarray](https://leetcode.com/problems/maximum-sum-circular-subarray/?show=1) | [918. Maximum Sum Circular Subarray](https://leetcode.cn/problems/maximum-sum-circular-subarray/?show=1) | 🟠 |
| - | [Sword to Offer 59 - I. Sliding Window Maximum](https://leetcode.cn/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/?show=1) | 🔴 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
