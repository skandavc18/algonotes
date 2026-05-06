# A Compendium of Reversal Tricks for Singly Linked Lists



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/) | [206. Reverse Linked List](https://leetcode.cn/problems/reverse-linked-list/) | 🟢 |
| [25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/) | [25. Reverse Nodes in k-Group](https://leetcode.cn/problems/reverse-nodes-in-k-group/) | 🔴 |
| [92. Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/) | [92. Reverse Linked List II](https://leetcode.cn/problems/reverse-linked-list-ii/) | 🟠 |

**-----------**



The iterative solution to reversing a singly linked list isn't hard, but the recursive one is more challenging. If we add the twist of reversing only part of a linked list — can you do both iteratively and recursively? And taking it further, what about reversing nodes k at a time?

This article tackles these problems step by step. We'll use both recursion and iteration, with visualization panels to strengthen your recursive intuition and pointer skills.

## Reversing the Whole Singly Linked List

On LeetCode, the standard `ListNode` is:

```java
// Singly linked list node
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}
```

Reversing a singly linked list is a basic problem — LeetCode 206 "Reverse Linked List":

<Pronlem slug="reverse-linked-list" />

We'll try several approaches.

### Iterative Solution

The standard approach uses a few pointers to flip each node's `next`. Nothing tricky, just pointer details.

Code (with comments and the visualization, this should be clear):

```java
class Solution {
    // Reverse the list starting at head
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        // For a singly linked list we need three pointers
        // cur is the current node, pre is its predecessor, nxt its successor
        ListNode pre, cur, nxt;
        pre = null; cur = head; nxt = head.next;
        while (cur != null) {
            // Reverse one node at a time
            cur.next = pre;
            // Advance pointers
            pre = cur;
            cur = nxt;
            if (nxt != null) {
                nxt = nxt.next;
            }
        }
        // Return the new head
        return pre;
    }
}
```

<visual slug="reverse-linked-list-iter" >

Click the visualization below; click the line <code type="click">cur.next = pre</code> repeatedly to see the reversal step by step:

</visual>

> [!TIP]
> The pointer logic isn't complex and there are other correct ways to write it. Two simple tips help keep things clear:
> 
> 1. As soon as you write something like `nxt.next`, reflexively check whether `nxt` is null first — otherwise you risk a null pointer exception.
> 
> 2. Pay attention to loop termination conditions. Know where each pointer ends up when the loop exits, so you return the right answer. If it gets confusing, draw the simplest case (e.g. `1->2`) and trace through.

### Recursive Solution

Iteration is fiddly but straightforward. What about recursion?

Beginners may not see the trick — that's normal. After studying recursive thinking via the binary-tree series, you may come back and figure it out yourself.

Binary trees are essentially an extension of singly linked lists (a "binary linked list"), so the same recursive thinking applies.

**The key to recursive list reversal: the problem itself has subproblem structure.**

Given a list `1->2->3->4`, ignore the head `1` and look at the sublist `2->3->4` — that's also a singly linked list.

So if `reverseList` reverses any singly linked list, can we use it to reverse `2->3->4` first, then attach `1` to the end of the reversed `4->3->2`? That gives the full reversal.

```java
reverseList(1->2->3->4) = reverseList(2->3->4) -> 1
```



**This is the "decomposition" mindset: use the recursion's definition to break the original problem into smaller, structurally identical subproblems, then assemble the answer.**

We have a chapter dedicated to this mindset later; here we won't go deeper.

Code:

```java
class Solution {
    // Definition: given the head of a singly linked list, reverse it and return the new head
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode last = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return last;
    }
}
```

This algorithm is often used to showcase the elegance of recursion. Let's walk through it; afterwards, the visualization panel lets you explore the recursion yourself.

For decomposition-style recursion, the most important thing is to nail down the function's definition. Here:

**Given a node `head`, reverse the list starting at `head` and return the new head.**

With this definition, consider reversing:

![](https://labuladong.online/algo/images/reverse-linked-list/1.jpg)

`reverseList(head)` recurses here:

```java
ListNode last = reverseList(head.next);
```

Don't dive into the recursion (your stack can only hold so much!) — instead, use the definition to figure out the result:

![](https://labuladong.online/algo/images/reverse-linked-list/2.jpg)

After `reverseList(head.next)`, the list looks like:

![](https://labuladong.online/algo/images/reverse-linked-list/3.jpg)

By definition, `reverseList` returns the new head; we capture it in `last`.

Now this line:

```java
head.next.next = head;
```

![](https://labuladong.online/algo/images/reverse-linked-list/4.jpg)

Then:

```java
head.next = null;
return last;
```

![](https://labuladong.online/algo/images/reverse-linked-list/5.jpg)

The list is fully reversed! Recursion is concise and elegant. Two things to watch for:

1. The base case:

```java
if (head == null || head.next == null) {
    return head;
}
```

If the list is empty or has a single node, the reversal is itself; return as is.

2. After reversal, the new head is `last` and the old `head` becomes the tail; remember to set its `next` to null:

```java
head.next = null;
```

The list is now fully reversed. Visualization of the recursive reversal:


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/reverse-linked-list/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>

> [!NOTE]
> The visualization shows every detail, but I don't recommend beginners obsess over them. Understand the recursion using the figures above first, then use the visualization to deepen the understanding.

> [!NOTE]
> Recursion is not efficient for linked lists.
> 
> Both versions are O(N) time, but the iterative version uses O(1) extra space, while the recursive version uses O(N) for the call stack.
> 
> Use recursion to practice recursive thinking, but for performance use iteration.

## Reverse the First N Nodes

Now implement:

```java
// Reverse the first n nodes (n <= length of the list)
ListNode reverseN(ListNode head, int n)
```

For the list below, `reverseN(head, 3)`:

![](https://labuladong.online/algo/images/reverse-linked-list/6.jpg)

### Iterative Solution

Easy by tweaking the previous `reverseList`:

```java
ListNode reverseN(ListNode head, int n) {
    if (head == null || head.next == null) {
        return head;
    }
    ListNode pre, cur, nxt;
    pre = null; cur = head; nxt = head.next;
    while (n > 0) {
        cur.next = pre;
        pre = cur;
        cur = nxt;
        if (nxt != null) {
            nxt = nxt.next;
        }
        n--;
    }
    // cur is now the (n+1)-th node; head is the new tail of the reversed segment
    head.next = cur;
    // pre is the new head of the reversed segment
    return pre;
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/reverse-n-iter/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌟 Animated Code Visualization 🌟</strong>
</summary>
</details>
</a>
<hr/>



### Recursive Solution

Similar to the full reversal with small tweaks:

```java
// Successor node
ListNode successor = null;

// Reverse the first n nodes starting at head; return the new head
ListNode reverseN(ListNode head, int n) {
    if (n == 1) {
        // Record the (n+1)-th node
        successor = head.next;
        return head;
    }
    // Reverse the first n - 1 nodes starting at head.next
    ListNode last = reverseN(head.next, n - 1);

    head.next.next = head;
    // Connect the reversed head to the rest
    head.next = successor;
    return last;
}
```

Differences:

1. The base case becomes `n == 1`. Reversing one element gives itself; **also record the successor** — the (n+1)-th node.

2. Previously we set `head.next` to null because after full reversal `head` becomes the last node. Now `head` is not necessarily the last node, so we record `successor` (the (n+1)-th node) and connect `head` to it.

![](https://labuladong.online/algo/images/reverse-linked-list/7.jpg)


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/list-reverse-n/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Animated Code Visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>



## Reversing Part of a Linked List

We can go further: given an index range, reverse just that segment.

LeetCode 92 "Reverse Linked List II":

<Problem slug="reverse-linked-list-ii" />

Given indices `[m, n]` (1-indexed), reverse only that segment. Signature:

```java
ListNode reverseBetween(ListNode head, int m, int n)
```

### Iterative Solution

Find the (m-1)-th node, then reuse `reverseN`:

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int m, int n) {
        if (m == 1) {
            return reverseN(head, n);
        }
        // Find the predecessor of the m-th node
        ListNode pre = head;
        for (int i = 1; i < m - 1; i++) {
            pre = pre.next;
        }
        // Start reversing from the m-th node
        pre.next = reverseN(pre.next, n - m + 1);
        return head;
    }

    ListNode reverseN(ListNode head, int n) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode pre, cur, nxt;
        pre = null; cur = head; nxt = head.next;
        while (n > 0) {
            cur.next = pre;
            pre = cur;
            cur = nxt;
            if (nxt != null) {
                nxt = nxt.next;
            }
            n--;
        }
        // cur is now the (n+1)-th node; head is the new tail of the reversed segment
        head.next = cur;
        // pre is the new head of the reversed segment
        return pre;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/tutorial/reverse-linked-list-ii-iter/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>👾 Animated Code Visualization 👾</strong>
</summary>
</details>
</a>
<hr/>



### Recursive Solution

Same idea: find the (m-1)-th node, then reuse `reverseN`.

How do we find the (m-1)-th node recursively?

If we treat `head`'s index as 1, we want to start reversing at the m-th element. If we treat `head.next` as index 1, we'd be reversing starting from the (m-1)-th element relative to `head.next`. And for `head.next.next`...

That's just iteration via recursion:

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int m, int n) {
        // Base case
        if (m == 1) {
            return reverseN(head, n);
        }
        // Advance to the start of the reversal to trigger the base case
        head.next = reverseBetween(head.next, m - 1, n - 1);
        return head;
    }

    // Successor node
    ListNode successor = null;

    // Reverse the first n nodes starting at head; return the new head
    ListNode reverseN(ListNode head, int n) {
        if (n == 1) {
            // Record the (n+1)-th node
            successor = head.next;
            return head;
        }
        ListNode last = reverseN(head.next, n - 1);

        head.next.next = head;
        head.next = successor;
        return last;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/reverse-linked-list-ii/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>



## Reverse Nodes in k-Group

A classic interview problem; LeetCode rates it Hard:

<Problem slug="reverse-nodes-in-k-group" />

After all that buildup, is it really hard? Decomposition mindset + reuse of `reverseN` does it.

### Idea

This problem **has recursive structure**.

Calling `reverseKGroup(head, 2)`:

![](https://labuladong.online/algo/images/kgroup/1.jpg)

If we reverse the first 2 nodes, the rest is still a linked list — just shorter. That's a smaller, structurally identical subproblem.

We move `head` to the start of the rest and recursively call `reverseKGroup(head, 2)`:

![](https://labuladong.online/algo/images/kgroup/2.jpg)

Sketch:

**1. Reverse the first `k` elements starting at `head`.** Reuse `reverseN`.

![](https://labuladong.online/algo/images/kgroup/3.jpg)

**2. Recursively call `reverseKGroup` with the (k+1)-th element as the new head.**

![](https://labuladong.online/algo/images/kgroup/4.jpg)

**3. Stitch the two results together.**

![](https://labuladong.online/algo/images/kgroup/5.jpg)

### Code

I'll use the iterative `reverseN`; recursive works too:

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null) return null;
        // Range [a, b) covers the k elements to reverse
        ListNode a, b;
        a = b = head;
        for (int i = 0; i < k; i++) {
            // Less than k elements left; no need to reverse
            if (b == null) return head;
            b = b.next;
        }
        // Reverse the first k elements
        ListNode newHead = reverseN(a, k);
        // b now points to the head of the next group
        // Recursively reverse the rest and connect
        a.next = reverseKGroup(b, k);
        return newHead;
    }

    // The reverseN function defined above
    ListNode reverseN(ListNode head, int n) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode pre, cur, nxt;
        pre = null; cur = head; nxt = head.next;
        while (n > 0) {
            cur.next = pre;
            pre = cur;
            cur = nxt;
            if (nxt != null) {
                nxt = nxt.next;
            }
            n--;
        }
        head.next = cur;
        return pre;
    }
}
```

Done.


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/reverse-nodes-in-k-group/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Animated Code Visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>


## Wrap-Up

Recursion is harder to grasp than iteration. The trick: don't dive into the recursion — use a clear definition to drive the implementation.

For a hard problem, try breaking it down: modify simpler solutions to handle harder cases.







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Classic Two-Pointer Linked List Problems](https://labuladong.online/algo/problem-set/linkedlist-two-pointers/)
 - [Binary Tree Tactics (Approach)](https://labuladong.online/algo/data-structure/binary-tree-part1/)
 - [Determining a Palindrome Linked List](https://labuladong.online/algo/data-structure/palindrome-linked-list/)
 - [Pancake Sorting](https://labuladong.online/algo/frequency-interview/pancake-sorting/)
 - ["Trick" Patterns for Algorithm Quizzes](https://labuladong.online/algo/other-skills/tips-in-exam/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [2. Add Two Numbers](https://leetcode.com/problems/add-two-numbers/?show=1) | [2. Add Two Numbers](https://leetcode.cn/problems/add-two-numbers/?show=1) | 🟠 |
| [24. Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/?show=1) | [24. Swap Nodes in Pairs](https://leetcode.cn/problems/swap-nodes-in-pairs/?show=1) | 🟠 |
| [445. Add Two Numbers II](https://leetcode.com/problems/add-two-numbers-ii/?show=1) | [445. Add Two Numbers II](https://leetcode.cn/problems/add-two-numbers-ii/?show=1) | 🟠 |
| - | [Sword to Offer 24. Reverse Linked List](https://leetcode.cn/problems/fan-zhuan-lian-biao-lcof/?show=1) | 🟢 |
| - | [Sword to Offer II 024. Reverse Linked List](https://leetcode.cn/problems/UHnkqh/?show=1) | 🟢 |
| - | [Sword to Offer II 025. Add Two Numbers in Linked List](https://leetcode.cn/problems/lMSNwu/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
