# Scheduling Exam Seats

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
| [855. Exam Room](https://leetcode.com/problems/exam-room/) | [855. Exam Room](https://leetcode.cn/problems/exam-room/) | 🟠

**-----------**

This article covers LeetCode 855 "Exam Room" — an interesting and somewhat tricky problem that, more than DP-style cleverness, tests your understanding of common data structures and your coding skill.

A side note: many readers ask how I summarize "frameworks". They emerge slowly from details. I encourage you to actually solve the problems yourself — there's no substitute.

The problem: an exam room has `N` seats indexed `[0..N-1]`. Students enter and leave at any time.

As the proctor, you arrange seats: **on each entry, maximize the distance to the nearest other person; if multiple seats tie, pick the smallest index**. Reasonable.

Implement:

```java
class ExamRoom {
    // Constructor with total seats N
    public ExamRoom(int N);
    // A new student enters; return the seat assigned
    public int seat();
    // The student at seat p leaves
    // (p is guaranteed to be occupied)
    public void leave(int p);
}
```

E.g., 5 seats `[0..4]`:

First student: any seat works; pick the smallest index, 0.

Second: maximize distance from others; pick 4.

Third: middle, seat 2.

Fourth: seat 1 or 3 — pick the smaller, 1.

And so on.

Without `leave`, you can spot the pattern:

**Treat each pair of adjacent students as endpoints of a segment. New students "bisect" the longest segment; the midpoint is the seat. `leave(p)` removes `p` and merges its two neighboring segments.**

Simple core logic. The challenge is data-structure choice.

### 1. Analysis

We model students as segments — represented by length-2 arrays.

We need to find the **longest** segment, remove segments, add segments.

**Whenever a problem requires extremes during dynamic updates, use an ordered structure: a binary heap or a balanced BST**. Heap operations on extremes are O(log N), but heaps can only delete the max. Balanced BSTs handle min/max plus arbitrary insert/delete — O(log N).

Heap can't support `leave`; use a balanced BST. In Java, that's `TreeSet`, backed by a red-black tree.

A note: don't conflate Set/Map with HashSet/HashMap. Hash-based variants have O(1) operations but no order. Tree-backed variants (red-black trees) maintain order at O(log N) — "ordered Set/Map".

We use `TreeSet` to maintain segment ordering, find the longest, and quickly insert/delete.

### 2. Simplifying

If multiple seats are tied, pick the smallest index. **Let's ignore that tie-break for now** and implement the rest.

We'll use a "virtual segment" to bootstrap, like a "dummy head" in linked list problems.

```java
class ExamRoom {
    // Maps p → segment with p as left endpoint
    private Map<Integer, int[]> startMap;
    // Maps p → segment with p as right endpoint
    private Map<Integer, int[]> endMap;
    // Segments stored sorted by length (ascending)
    private TreeSet<int[]> pq;
    private int N;

    public ExamRoom(int N) {
        this.N = N;
        startMap = new HashMap<>();
        endMap = new HashMap<>();
        pq = new TreeSet<>((a, b) -> {
            // Compute lengths
            int distA = distance(a);
            int distB = distance(b);
            // Longer goes later
            return distA - distB;
        });
        // Insert a virtual segment first
        addInterval(new int[] {-1, N});
    }

    /* Remove a segment */
    private void removeInterval(int[] intv) {
        pq.remove(intv);
        startMap.remove(intv[0]);
        endMap.remove(intv[1]);
    }

    /* Add a segment */
    private void addInterval(int[] intv) {
        pq.add(intv);
        startMap.put(intv[0], intv);
        endMap.put(intv[1], intv);
    }

    /* Compute a segment's length */
    private int distance(int[] intv) {
        return intv[1] - intv[0] - 1;
    }

    // ...
}
```

The "virtual segment" represents all seats as one big segment:

![](https://labuladong.online/algo/images/seat-scheduling/1.jpg)

With that, `seat` and `leave`:

```java
class ExamRoom {
    // ...

    public int seat() {
        // Take the longest segment
        int[] longest = pq.last();
        int x = longest[0];
        int y = longest[1];
        int seat;
        if (x == -1) { // Case 1
            seat = 0;
        } else if (y == N) { // Case 2
            seat = N - 1;
        } else { // Case 3
            seat = (y - x) / 2 + x;
        }
        // Split the longest segment into two
        int[] left = new int[] {x, seat};
        int[] right = new int[] {seat, y};
        removeInterval(longest);
        addInterval(left);
        addInterval(right);
        return seat;
    }

    public void leave(int p) {
        // Find segments adjacent to p
        int[] right = startMap.get(p);
        int[] left = endMap.get(p);
        // Merge into one segment
        int[] merged = new int[] {left[0], right[1]};
        removeInterval(left);
        removeInterval(right);
        addInterval(merged);
    }
}
```

![](https://labuladong.online/algo/images/seat-scheduling/2.jpg)

Mostly there. The code is verbose but the idea is simple: find the longest segment; bisect for `seat()`; merge neighbors for `leave(p)`.

### 3. The Tricky Part

The tie-break: if multiple seats tie, pick the smallest index. Right now we get this case wrong:

![](https://labuladong.online/algo/images/seat-scheduling/3.jpg)

Segments `[0,4]` and `[4,9]`; the longest is the latter, so `seat()` would return 6. But the correct answer is 2 — both 2 and 6 yield the same maximum distance, and we want the smaller.

![](https://labuladong.online/algo/images/seat-scheduling/4.jpg)

**Fix: change the ordering**. Modify `TreeSet`'s comparator:

```java
pq = new TreeSet<>((a, b) -> {
    int distA = distance(a);
    int distB = distance(b);
    // Equal lengths: compare indices
    if (distA == distB)
        return b[0] - a[0];
    return distA - distB;
});
```

Also, change `distance` — **it should not be the gap between endpoints, but the distance from the segment's midpoint to either endpoint**.

```java
class ExamRoom {
    // ...

    private int distance(int[] intv) {
        int x = intv[0];
        int y = intv[1];
        if (x == -1) return y;
        if (y == N) return N - 1 - x;
        // Distance between midpoint and an endpoint
        return (y - x) / 2;
    }
}
```

![](https://labuladong.online/algo/images/seat-scheduling/5.jpg)

Now `[0,4]` and `[4,9]` have equal `distance`; the comparator picks the one with the smaller index. Solved.

### 4. Wrap-Up

Not very hard despite the volume — the core challenge is choosing the right ordered structure.

For dynamic problems with extremes, balanced BSTs and heaps both work; BSTs support more operations.

Why use heaps then? Heaps are simple — backed by an array; see [Binary Heap Detailed](https://labuladong.online/algo/data-structure-basic/binary-heap-implement/). Implementing a red-black tree is much harder and uses more memory.

Hope this helps.


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

**"labuladong's Algorithm Notes" has been published. Follow the official account for details; reply with "all-in-one bundle" to download the companion PDF and the full problem-solving bundle**:

![](https://labuladong.online/algo/images/souyisou2.png)

====== Code in Other Languages ======

[855. Exam Room](https://leetcode-cn.com/problems/exam-room)

### javascript

JavaScript has no built-in TreeSet, and implementing one is awkward.

We use an array to record the occupied positions.

`seat` cases:

- Empty array → `seatNo` defaults to 0.
- One element → choose the side with the larger gap.
- Otherwise scan pairs to find the largest mid-gap.

```js
class ExamRoom {
    /**
     * @param {number} N
     */
    constructor(N) {
        this.array = [];
        this.seatNo = 0;
        this.number = N - 1;
    }
    /**
     * @return {number}
     */
    seat() {
        this.seatNo = 0;
        if (this.array.length == 1) {
            if (this.array[0] == 0) {
                this.seatNo = this.number;
            } else if (this.array[0] == this.number) {
                this.seatNo = 0;
            } else {
                let distance1 = this.array[0];
                let distance2 = this.number - this.array[0];
                if (distance1 >= distance2) {
                    this.seatNo = 0 + distance1;
                } else {
                    this.seatNo = distance1 + distance2;
                }
            }
        } else if ((this.array.length > 1)) {
            let maxDistance = this.array[0], start;
            for (let i = 0; i < this.array.length - 1; i++) {
                let distance = Math.floor((this.array[i + 1] - this.array[i] >>> 1));
                if (maxDistance < distance) {
                    maxDistance = distance;
                    start = this.array[i]
                    this.seatNo = start + maxDistance;
                }
            }
            if (this.number - this.array[this.array.length - 1] > maxDistance) {
                this.seatNo = this.number;
            }
        }
        this.array.push(this.seatNo);
        this.array.sort((a, b) => { return a - b })
        return this.seatNo;
    }
    /** 
     * @param {number} p
     * @return {void}
     */
    leave(p) {
        let index = this.array.indexOf(p)
        this.array.splice(index, 1)
    };
}
```

