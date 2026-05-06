# Implementing a Stack with a Queue and a Queue with Stacks



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [225. Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/) | [225. Implement Stack using Queues](https://leetcode.cn/problems/implement-stack-using-queues/) | 🟢 |
| [232. Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/) | [232. Implement Queue using Stacks](https://leetcode.cn/problems/implement-queue-using-stacks/) | 🟢 |
| - | [Sword to Offer 09. Implement Queue with Two Stacks](https://leetcode.cn/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/) | 🟢 |

**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Array Basics](https://labuladong.online/algo/data-structure-basic/array-basic/)
> - [Linked List Basics](https://labuladong.online/algo/data-structure-basic/linkedlist-basic/)
> - [Queue Basics](https://labuladong.online/algo/data-structure-basic/queue-stack-basic/)

A queue is a first-in-first-out (FIFO) data structure, while a stack is a last-in-first-out (LIFO) data structure. Visually:

![](https://labuladong.online/algo/images/stack-queue/1.jpg)

Both are ultimately implemented on top of arrays or linked lists; their APIs simply restrict their behavior. For details see [Principles and Implementations of Queues/Stacks](https://labuladong.online/algo/data-structure-basic/queue-stack-basic/) in the basics chapter.

Today we look at how to use a stack to implement a queue, and how to use a queue to implement a stack.

### 1. Implementing a Queue with Stacks

LeetCode 232 "Implement Queue using Stacks" asks us to implement the following API:

```java
class MyQueue {
    
    // Add an element to the tail of the queue
    public void push(int x);
    
    // Remove and return the front element of the queue
    public int pop();
    
    // Return the front element of the queue
    public int peek();
    
    // Determine whether the queue is empty
    public boolean empty();
}
```

We can implement a queue using two stacks `s1, s2` (arranging the stacks like this may make it easier to understand):

![](https://labuladong.online/algo/images/stack-queue/2.jpg)

When `push` is called to enqueue an element, simply push the element onto `s1`. For example, if we push 3 elements 1, 2, 3, the underlying structure looks like:

![](https://labuladong.online/algo/images/stack-queue/3.jpg)

Now what if we call `peek` to look at the front element? Logically the front should be 1, but in `s1` the 1 is at the bottom. This is where `s2` plays the role of a relay: when `s2` is empty, we pop everything from `s1` and push it into `s2`, **at which point the elements in `s2` are in FIFO order**:

![](https://labuladong.online/algo/images/stack-queue/4.jpg)

When `s2` is non-empty, we directly call `pop` on `s2`, which yields the earliest-inserted element — implementing the queue's `pop`.

Full code:

```java
class MyQueue {
    private Stack<Integer> s1, s2;
    
    public MyQueue() {
        s1 = new Stack<>();
        s2 = new Stack<>();
    }

    // Add an element to the tail of the queue
    public void push(int x) {
        s1.push(x);
    }
    
    // Return the front element of the queue
    public int peek() {
        if (s2.isEmpty())
            // Move s1's elements onto s2
            while (!s1.isEmpty()) {
                s2.push(s1.pop());
            }
        return s2.peek();
    }

    // Remove and return the front element of the queue
    public int pop() {
        // Call peek first to ensure s2 is non-empty
        peek();
        return s2.pop();
    }

     // Determine whether the queue is empty
     // The queue is empty only when both stacks are empty
    public boolean empty() {
        return s1.isEmpty() && s2.isEmpty();
    }
}
```

We have now implemented a queue using stacks; the core idea is letting two stacks cooperate.

It's worth asking what the time complexity of these operations is.

The interesting one is `peek`: calling it may trigger the `while` loop, giving O(N) complexity, but most of the time the `while` loop does not run and the complexity is O(1). Since `pop` calls `peek`, its complexity is the same.

In such cases we say their **worst-case time complexity** is O(N), because the `while` loop may need to move elements from `s1` to `s2`.

But their **amortized time complexity** is O(1). The intuition: each element can be moved at most once, so the average per-element cost of `peek` is O(1).

For an analysis of time complexity, see [Practical Time/Space Complexity Analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/).

### 2. Implementing a Stack with a Queue

If implementing a queue with two stacks was clever, implementing a stack with a queue is more brute-force — we only need a single queue.

LeetCode 225 "Implement Stack using Queues" asks us to implement:

```java
class MyStack {
    
    // Push an element onto the top of the stack
    public void push(int x);
    
    // Remove and return the top element
    public int pop();
    
    // Return the top element
    public int top();
    
    // Determine whether the stack is empty
    public boolean empty();
}
```

For `push`, just enqueue the element and remember the tail element, since the queue's tail is the stack's top — so `top` can return it directly:

```java
class MyStack {
    Queue<Integer> q = new LinkedList<>();
    int top_elem = 0;

    // Push an element onto the top of the stack
    public void push(int x) {
        // x is at the tail of the queue, i.e., the top of the stack
        q.offer(x);
        top_elem = x;
    }
    
    // Return the top element
    public int top() {
        return top_elem;
    }

    public boolean empty() {
        return q.isEmpty();
    }
}
```

Our underlying structure is a FIFO queue, so each `pop` only pops from the front; but a stack is LIFO, meaning `pop` should remove the queue's tail:

![](https://labuladong.online/algo/images/stack-queue/5.jpg)

The brute-force solution: take everything from the front and re-enqueue it at the back, so the previous tail moves to the front and can then be removed:

![](https://labuladong.online/algo/images/stack-queue/6.jpg)

```java
class MyStack {
    // Earlier code omitted for brevity...

    // Remove and return the top element
    public int pop() {
        int size = q.size();
        while (size > 1) {
            q.offer(q.poll());
            size--;
        }
        // The previous tail is now at the front
        return q.poll();
    }
}
```

There's a small issue: after the previous tail is moved to the front and removed, `top_elem` is not updated. A small fix:

```java
class MyStack {
    // Earlier code omitted for brevity...

    // Remove and return the top element
    public int pop() {
        int size = q.size();
        // Leave the last 2 elements at the back
        while (size > 2) {
            q.offer(q.poll());
            size--;
        }
        // Record the new tail element
        top_elem = q.peek();
        q.offer(q.poll());
        // Remove the previous tail
        return q.poll();
    }
}
```

The complete code:

```java
class MyStack {
    Queue<Integer> q = new LinkedList<>();
    int top_elem = 0;

    // Push an element onto the top of the stack
    public void push(int x) {
        q.offer(x);
        top_elem = x;
    }
    
    // Remove and return the top element
    public int pop() {
        int size = q.size();
        while (size > 2) {
            q.offer(q.poll());
            size--;
        }
        top_elem = q.peek();
        q.offer(q.poll());
        return q.poll();
    }
    
    // Return the top element
    public int top() {
        return top_elem;
    }
    
    // Determine whether the stack is empty
    public boolean empty() {
        return q.isEmpty();
    }
}
```

Clearly, when implementing a stack with a queue, `pop` is O(N) and the others are O(1).

In my opinion, implementing a stack with a queue isn't very interesting; the two-stack queue is the one worth learning.

![](https://labuladong.online/algo/images/stack-queue/4.jpg)

After moving elements from `s1` to `s2`, they end up in FIFO order in `s2` — somewhat like "two negatives make a positive". It's not an obvious idea.







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Classic Stack Problems](https://labuladong.online/algo/problem-set/stack/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| - | [Sword to Offer 09. Implement Queue with Two Stacks](https://leetcode.cn/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/?show=1) | 🟢 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
