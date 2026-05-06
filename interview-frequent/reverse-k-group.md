# Reverse a Linked List in k-Groups

<p align='center'>
<a href="https://github.com/labuladong/fucking-algorithm" target="view_window"><img alt="GitHub" src="https://img.shields.io/github/stars/labuladong/fucking-algorithm?label=Stars&style=flat-square&logo=GitHub"></a>
<a href="https://labuladong.online/algo/" target="_blank"><img class="my_header_icon" src="https://img.shields.io/static/v1?label=Premium%20Course&message=View&color=pink&style=flat"></a>
<a href="https://www.zhihu.com/people/labuladong"><img src="https://img.shields.io/badge/Zhihu-@labuladong-000000.svg?style=flat-square&logo=Zhihu"></a>
<a href="https://space.bilibili.com/14089380"><img src="https://img.shields.io/badge/Bilibili-@labuladong-000000.svg?style=flat-square&logo=Bilibili"></a>
</p>

![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: [the new website's membership](https://labuladong.online/algo/intro/site-vip/) is about to increase in price; renewals for existing users are supported. It's also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/) | [25. Reverse Nodes in k-Group](https://leetcode.cn/problems/reverse-nodes-in-k-group/) | 🔴

**-----------**

The earlier article [Recursive Linked List Reversal](https://labuladong.online/algo/data-structure/reverse-linked-list-recursion/) covered recursive reversal. Some readers asked about iterative reversal — Part 1 here covers that.

With that helper, we'll solve LeetCode 25 "Reverse Nodes in k-Group" recursively — a test of your recursive thinking. Ready?

The problem:

<Problem slug="reverse-nodes-in-k-group" />

A common interview problem; LeetCode rates it Hard. Really that hard?

Algorithms on basic data structures usually aren't hard if you decompose carefully.

### 1. Analysis

In [Framework Thinking for DS&A](https://labuladong.online/algo/essential-technique/abstraction-of-algorithm/), I noted linked lists support both recursion and iteration. **This problem is recursive**.

Why recursive? See the picture. Calling `reverseKGroup(head, 2)`:

![](https://labuladong.online/algo/images/kgroup/1.jpg)

If we reverse the first 2 nodes, what about the rest? The rest is also a linked list, smaller — a **subproblem**.

![](https://labuladong.online/algo/images/kgroup/2.jpg)

We move `head` to the start of the rest and recursively call `reverseKGroup(head, 2)` — same structure, just smaller.

Sketch:

**1. Reverse the first `k` elements starting at `head`.**

![](https://labuladong.online/algo/images/kgroup/3.jpg)

**2. Recursively call `reverseKGroup` from the (k+1)-th element as the new head.**

![](https://labuladong.online/algo/images/kgroup/4.jpg)

**3. Stitch the two results together.**

![](https://labuladong.online/algo/images/kgroup/5.jpg)

Base case: if fewer than `k` elements remain, leave them as is.

### 2. Implementation

We need a `reverse` to reverse a range. First, a simpler version: reverse the entire list given the head:

```java
// Reverse the list with head a
ListNode reverse(ListNode a) {
    ListNode pre, cur, nxt;
    pre = null; cur = a; nxt = a;
    while (cur != null) {
        nxt = cur.next;
        // Flip one node
        cur.next = pre;
        // Advance pointers
        pre = cur;
        cur = nxt;
    }
    // Return the new head
    return pre;
}
```

GIF:

![](https://labuladong.online/algo/images/kgroup/8.gif)

Iterative version — should be easy with the animation.

"Reverse the list rooted at `a`" is "reverse nodes from `a` up to null". Could you "reverse nodes from `a` up to `b`"?

Just change the signature and replace `null` with `b`:

```java
/** Reverse nodes in [a, b) — left-closed, right-open */
ListNode reverse(ListNode a, ListNode b) {
    ListNode pre, cur, nxt;
    pre = null; cur = a; nxt = a;
    // Just change the while condition
    while (cur != b) {
        nxt = cur.next;
        cur.next = pre;
        pre = cur;
        cur = nxt;
    }
    // Return the new head
    return pre;
}
```

With that helper, `reverseKGroup`:

```java
ListNode reverseKGroup(ListNode head, int k) {
    if (head == null) return null;
    // Range [a, b) contains the k elements to reverse
    ListNode a, b;
    a = b = head;
    for (int i = 0; i < k; i++) {
        // Fewer than k — leave alone (base case)
        if (b == null) return head;
        b = b.next;
    }
    // Reverse the first k elements
    ListNode newHead = reverse(a, b);
    // Recursively reverse the rest and connect
    a.next = reverseKGroup(b, k);
    return newHead;
}
```

`reverse` operates on `[a, b)`, so:

![](https://labuladong.online/algo/images/kgroup/6.jpg)

The recursion completes the full result:

![](https://labuladong.online/algo/images/kgroup/7.jpg)

<visual slug='reverse-nodes-in-k-group'/>

### 3. Closing Thoughts

Articles on basic data structures get less attention — a mistake.

Many people prefer DP because it shows up often in interviews. But many algorithmic ideas grow from data structures. As [Framework Thinking for DS&A](https://labuladong.online/algo/essential-technique/abstraction-of-algorithm/) noted, DP / backtracking / divide-and-conquer are tree traversals — and trees are multiway linked lists. Master basic data structures, and ordinary algorithm problems become much easier.

How to decompose problems and find recursion? Practice. My data-structure course covers [Recursive Implementations of a Singly Linked List](https://aep.h5.xeknow.com/s/1RQzXc) for deeper recursion intuition.


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Binary Tree Tactics (Approach)](https://labuladong.online/algo/data-structure/binary-tree-part1/)
 - ["Trick" Patterns for Algorithm Quizzes](https://labuladong.online/algo/other-skills/tips-in-exam/)
 - [Recursive Magic: Reversing a Singly Linked List](https://labuladong.online/algo/data-structure/reverse-linked-list-recursion/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou |
| :----: | :----: |
| [24. Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/?show=1) | [24. Swap Nodes in Pairs](https://leetcode.cn/problems/swap-nodes-in-pairs/?show=1) |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

**"labuladong's Algorithm Notes" has been published. Follow the official account for details; reply with "all-in-one bundle" to download the companion PDF and the full problem-solving bundle**:

![](https://labuladong.online/algo/images/souyisou2.png)

====== Code in Other Languages ======

[25. Reverse Nodes in k-Group](https://leetcode-cn.com/problems/reverse-nodes-in-k-group)

### javascript

```js
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */


// Example 1: reverse the list rooted at a
let reverse = function (a) {
    let pre, cur, nxt;
    pre = null;
    cur = a;
    nxt = a;
    while (cur != null) {
        nxt = cur.next;
        // Flip one node
        cur.next = pre;
        // Advance pointers
        pre = cur;
        cur = nxt;
    }
    // Return the new head
    return pre;
}

/** Reverse nodes in [a, b) — left-closed, right-open */
let reverse = (a, b) => {
    let pre, cur, nxt;
    pre = null;
    cur = a;
    nxt = a;
    // Just change the while condition
    while (cur !== b) {
        nxt = cur.next;
        cur.next = pre;
        pre = cur;
        cur = nxt;
    }
    // Return the new head
    return pre;
}


/**
 * @param {ListNode} head
 * @param {number} k
 * @return {ListNode}
 */
let reverseKGroup = (head, k) => {
    if (head == null) return null;
    // Range [a, b) contains k elements to reverse
    let a, b;
    a = b = head;
    for (let i = 0; i < k; i++) {
        // Fewer than k — base case
        if(b==null) return head;
        b = b.next;
    }

    // Reverse the first k elements
    let newHead = reverse(a,b);

    // Recursively reverse the rest and connect
    a.next = reverseKGroup(b,k);
    return newHead;
}
```

