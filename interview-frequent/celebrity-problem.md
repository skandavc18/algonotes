# Find the Celebrity Problem



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [277. Find the Celebrity](https://leetcode.com/problems/find-the-celebrity/)🔒 | [277. Find the Celebrity](https://leetcode.cn/problems/find-the-celebrity/)🔒 | 🟠 |

**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Graph Basics and General Implementation](https://labuladong.online/algo/data-structure-basic/graph-basic/)

The classic "celebrity problem":

Given social relations among `n` people (you can ask whether any two know each other), find the "celebrity".

A "celebrity" is defined by:

1. Everyone else knows the celebrity.

2. The celebrity doesn't know anyone else.

This is a graph problem — a social network.

Treat each person as a node and "knows" as a directed edge. The celebrity is a special node:

![](https://labuladong.online/algo/images/celebrity/1.jpeg)

**No outgoing edges; all other nodes have edges pointing to it.**

In graph terms: out-degree 0, in-degree `n - 1`.

How are the relations stored? In [Graph Basics](https://labuladong.online/algo/data-structure-basic/graph-basic/), graphs come as adjacency lists or matrices. Lists save space; matrices give O(1) adjacency tests.

Since we need fast adjacency tests, use the matrix.

In algorithmic form:

Given an `n x n` matrix `graph`. People are nodes 0..n-1. `graph[i][j] == 1` means person `i` knows `j`; 0 means they don't.

Determine if there's a celebrity. If so, return its index; else -1.

```java
int findCelebrity(int[][] graph);
```

E.g.:

![](https://labuladong.online/algo/images/celebrity/2.jpeg)

The answer is 2.

LeetCode 277 "Find the Celebrity" doesn't hand you the matrix; it gives `n` and an API:

```java
// Returns whether i knows j
boolean knows(int i, int j);

// Implement: return the celebrity's index
int findCelebrity(int n) {
    // todo
}
```

`knows` is essentially a matrix access. We'll proceed with LeetCode's interface.

## Brute Force

```java
class Solution extends Relation {
    public int findCelebrity(int n) {
        for (int cand = 0; cand < n; cand++) {
            int other;
            for (other = 0; other < n; other++) {
                if (cand == other) continue;
                // Everyone else must know cand; cand must not know anyone else
                // Otherwise cand isn't a celebrity
                if (knows(cand, other) || !knows(other, cand)) {
                    break;
                }
            }
            if (other == n) {
                // Found
                return cand;
            }
        }
        // No celebrity
        return -1;
    }
}
```

`cand` = candidate. We test each person.

`knows` is O(1) — matrix access. The brute force is worst-case O(N²).

Can we do better? Where is the most time spent?

For each candidate, we run an inner loop to verify it.

The inner loop is wasteful: confirming "is celebrity" requires checking everyone, but confirming "isn't" doesn't.

**Since the celebrity is unique, we can use elimination — exclude obvious non-celebrities to reduce nesting.**







## Optimized

Repeating the definition:

1. Everyone else knows the celebrity.

2. The celebrity knows no one else.

Implies at most one celebrity (otherwise contradiction).

**By inspecting any pair, we can eliminate at least one as a non-celebrity.**

Whether the other is the celebrity needs more testing — that's fine; we've reduced the search.

Why does any pair eliminate one? Two people's relationship has 4 cases.

Red = doesn't know; green = knows:

![](https://labuladong.online/algo/images/celebrity/3.jpeg)

Let them be `cand` and `other`:

Case 1: `cand` knows `other` → `cand` isn't celebrity (a celebrity knows no one).

Case 2: `other` knows `cand` → `other` isn't celebrity.

Case 3: mutual → neither is celebrity; eliminate either.

Case 4: neither knows → neither is celebrity (a celebrity is known by everyone); eliminate either.

In code:

```java
if (knows(cand, other) || !knows(other, cand)) {
    // cand can't be a celebrity
} else {
    // other can't be a celebrity
}
```

**Pick two candidates, eliminate one, repeat until one remains; then verify.**

```java
class Solution extends Relation {
    public int findCelebrity(int n) {
        if (n == 1) return 0;
        // All candidates in a queue
        LinkedList<Integer> q = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            q.addLast(i);
        }
        // Eliminate until one remains
        while (q.size() >= 2) {
            int cand = q.removeFirst();
            int other = q.removeFirst();
            if (knows(cand, other) || !knows(other, cand)) {
                // cand can't be celebrity; keep other
                q.addFirst(other);
            } else {
                // other can't be celebrity; keep cand
                q.addFirst(cand);
            }
        }

        // Verify the lone candidate
        int cand = q.removeFirst();
        for (int other = 0; other < n; other++) {
            if (other == cand) {
                continue;
            }
            if (!knows(other, cand) || knows(cand, other)) {
                return -1;
            }
        }
        return cand;
    }
}
```

O(N) time, O(N) space (the queue).

> [!NOTE]
> `LinkedList` is just a container; the order in which we pull/return candidates doesn't matter. `addFirst` here is for symmetry with the next optimization; `addLast` works just as well.

Can we eliminate the extra space?

## Final Solution

Yes — using two pointers without extra storage:

```java
class Solution extends Relation {
    public int findCelebrity(int n) {
        int cand = 0;
        for (int other = 1; other < n; other++) {
            if (!knows(other, cand) || knows(cand, other)) {
                // cand can't be celebrity
                // Assume other is
                cand = other;
            } else {
                // other can't be celebrity
                // Keep assuming cand is
            }
        }

        // cand may not actually be a celebrity — verify
        for (int other = 0; other < n; other++) {
            if (cand == other) continue;
            if (!knows(other, cand) || knows(cand, other)) {
                return -1;
            }
        }

        return cand;
    }
}
```

`other` and `cand` simulate the queue elimination.

O(N) time, O(1) space — optimal.









**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
