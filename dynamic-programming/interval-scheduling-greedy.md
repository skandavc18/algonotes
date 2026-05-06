# Greedy: interval scheduling



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you'll not only learn the algorithmic pattern but also be able to solve the following problems:

| LeetCode | 力扣 | Difficulty |
| :----: | :----: | :----: |
| [435. Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/) | [435. 无重叠区间](https://leetcode.cn/problems/non-overlapping-intervals/) | 🟠 |
| [452. Minimum Number of Arrows to Burst Balloons](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/) | [452. 用最少数量的箭引爆气球](https://leetcode.cn/problems/minimum-number-of-arrows-to-burst-balloons/) | 🟠 |

**-----------**



What is the greedy algorithm? You can think of it as a special case of dynamic programming. Compared with DP, greedy requires more conditions (the greedy-choice property), but it's more efficient.

For example, an algorithmic problem that takes exponential time by brute force can be reduced to polynomial time by DP eliminating overlapping subproblems. If it also satisfies the greedy-choice property, the time complexity can be reduced further to linear.

What is the greedy-choice property? Simply put: at every step, make a locally optimal choice, and the final result is globally optimal. Note this is a special property — only some problems have it.

For example, suppose 100 banknotes are placed in front of you and you can take ten — how do you maximize the total value? Obviously, picking the largest-denomination bill at each step gives the optimal result.

However, most problems clearly don't have the greedy-choice property. Take Doudizhu (Fight the Landlord): the opponent plays a pair of 3s. By a greedy strategy, you should play the smallest pair that beats them — but in real games you might even drop a "bomb". This case can't be solved greedily, only by DP. See the earlier post [Game theory via DP](https://labuladong.online/algo/dynamic-programming/game-theory/).

## 1. Problem overview

Back to the topic: this article solves a classic greedy problem — Interval Scheduling — which is LeetCode 435 "Non-overlapping Intervals":

You're given many closed intervals of the form `[start, end]`. Design an algorithm that **counts the maximum number of mutually non-overlapping intervals**.

```java
int intervalSchedule(int[][] intvs);
```

For example, with `intvs = [[1,3], [2,4], [3,6]]`, the maximum number of mutually non-overlapping intervals is 2, namely `[[1,3], [3,6]]`. Your algorithm should return 2. Note that touching at a boundary is not considered overlapping.

This problem has many real-world applications. For example, you have several activities today, each represented by an interval `[start, end]`. **What's the maximum number of activities you can attend today?** Obviously you can't attend two at once, so the problem is to find the largest non-overlapping subset of these time intervals.







## 2. Greedy solution

This problem has many seemingly-good greedy ideas that don't yield the right answer. For example:

Maybe pick the interval that starts earliest among the remaining? But some intervals start very early but are very long, causing us to incorrectly miss several short ones. Or pick the shortest? Or pick the one with the fewest conflicts? Each of these has easy counterexamples and is wrong.

The correct idea is actually simple, in three steps:

1. From the interval set `intvs`, pick an interval `x` that **ends earliest** (smallest `end`) among all current intervals.

2. Remove from `intvs` every interval that overlaps with `x`.

3. Repeat steps 1 and 2 until `intvs` is empty. The chosen `x`'s form the largest non-overlapping subset.

To turn this into an algorithm, sort the intervals by `end` ascending — both step 1 and step 2 become much easier. See the GIF below:

![](https://labuladong.online/algo/images/interval/1.gif)

Now, the implementation: step 1 is easy after sorting by `end`. The key is how to remove the intervals overlapping with `x` and pick the next `x`.

**Since we sorted in advance**, every interval overlapping with `x` must overlap with `x`'s `end`. If an interval doesn't want to overlap with `x`'s `end`, its `start` must be greater than (or equal to) `x`'s `end`:

![](https://labuladong.online/algo/images/interval/2.jpg)

Here's the code:

```java
class Solution {
    public int intervalSchedule(int[][] intvs) {
        if (intvs.length == 0) return 0;
        // sort by end ascending
        Arrays.sort(intvs, (a, b) -> Integer.compare(a[1], b[1]));
        // at least one interval is non-overlapping
        int count = 1;
        // after sorting, the first interval is x
        int x_end = intvs[0][1];
        for (int[] interval : intvs) {
            int start = interval[0];
            if (start >= x_end) {
                // found the next interval to choose
                count++;
                x_end = interval[1];
            }
        }
        return count;
    }
}
```

## 3. Application examples

Let's apply the interval-scheduling algorithm to a few specific problems.

First is LeetCode 435 "Non-overlapping Intervals":

Given a set of intervals, compute the minimum number of intervals you need to remove to make the rest non-overlapping. Function signature:

```java
int eraseOverlapIntervals(int[][] intvs);
```

You may assume that an interval's end is always greater than its start. Touching boundaries count as touching, not as overlapping.

For example, with `intvs = [[1,2],[2,3],[3,4],[1,3]]`, the algorithm returns 1 — once we remove `[1,3]`, the rest don't overlap.

We already know how to find the maximum number of non-overlapping intervals — the rest is exactly the minimum we must remove:

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intvs) {
        int n = intvs.length;
        return n - intervalSchedule(intvs);
    }

    private int intervalSchedule(int[][] intvs) {
        // see above
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/non-overlapping-intervals/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🍭 Code visualization 🍭</strong>
</summary>
</details>
</a>
<hr/>



Next, LeetCode 452 "Minimum Number of Arrows to Burst Balloons". Description:

Suppose there are many circular balloons in a 2D plane. Their projections onto the x-axis form intervals. Given these intervals, you walk along the x-axis and can shoot arrows vertically upward. What's the minimum number of arrows needed to burst all balloons?

Function signature:

```java
int findMinArrowShots(int[][] intvs);
```

For example, with input `[[10,16],[2,8],[1,6],[7,12]]`, the algorithm returns 2: shoot one arrow at `x = 6` to burst `[2,8]` and `[1,6]`, then shoot one at `x = 10`, 11, or 12 to burst `[10,16]` and `[7,12]`.

Think a moment — this problem is the same as interval scheduling! If at most `n` intervals are non-overlapping, we need at least `n` arrows to pierce all of them:

![](https://labuladong.online/algo/images/interval/3.jpg)

The only difference: in `intervalSchedule`, two intervals touching at a boundary don't count as overlapping. But here, an arrow that hits the boundary still bursts the balloon, so touching boundaries do count as overlapping:

![](https://labuladong.online/algo/images/interval/4.jpg)

So we slightly modify the previous algorithm to get the answer:

```java
class Solution {
    public int findMinArrowShots(int[][] intvs) {
        if (intvs.length == 0) return 0;
        Arrays.sort(intvs, (a, b) -> Integer.compare(a[1], b[1]));
        int count = 1;
        int x_end = intvs[0][1];
        
        for (int[] interval : intvs) {
            int start = interval[0];
            // change >= to >
            if (start > x_end) {
                count++;
                x_end = interval[1];
            }
        }
        return count;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/minimum-number-of-arrows-to-burst-balloons/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Code visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>



<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [One method solves three interval problems](https://labuladong.online/algo/practice-in-action/interval-problem-summary/)
 - [Editing video clips inspired a greedy algorithm](https://labuladong.online/algo/frequency-interview/cut-video/)
 - [Sweep-line technique: scheduling meeting rooms](https://labuladong.online/algo/frequency-interview/scan-line-technique/)

</details><hr>





**＿＿＿＿＿＿＿＿＿＿＿＿＿**

