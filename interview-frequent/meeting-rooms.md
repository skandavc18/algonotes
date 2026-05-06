# Sweep-Line Technique: Meeting Rooms



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [253. Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)🔒 | [253. Meeting Rooms II](https://leetcode.cn/problems/meeting-rooms-ii/)🔒 | 🟠 |

**-----------**



I was once asked a classic and practical problem in an interview: meeting room scheduling.

LeetCode's analog is a paid problem, but the technique is worth knowing.

LeetCode 253 "Meeting Rooms II":

Given intervals `[begin, end]` representing meetings, compute the minimum number of rooms required.

```java
// Returns the number of rooms required
int minMeetingRooms(int[][] meetings);
```

E.g., `meetings = [[0,30],[5,10],[15,20]]` → 2 (the second and third meetings conflict with the first).

If meetings overlap, you need extra rooms. The minimum equals the maximum number of concurrently-running meetings.

**Treat each meeting's start/end as an interval; the question becomes: what's the maximum number of overlapping intervals?**

We've covered interval algorithms before. With [Difference Arrays](https://labuladong.online/algo/data-structure/diff-array/) in mind, you might think to use that.

This problem is essentially: given an all-zero array and several intervals, increment each by 1; what's the max?

But difference arrays require building the all-zero array — its length depends on the maximum time. For `meetings = [[0,30],[5,10],[10^8,10^9]]`, you'd need an array of length 10^9 — a problem. The given constraint here is up to 10^6, so it works, but the technique below avoids the issue.







## Problem Spectrum

Several articles cover interval-scheduling problems; let me line them up:

**Scenario 1**: one room, many meetings — fit as many as possible.

Sort by end time and greedily pick. See [Greedy Time Management](https://labuladong.online/algo/frequency-interview/interval-scheduling/).

**Scenario 2**: short video clips and a long target clip — minimize the number of clips chosen to cover the target.

Sort clips by start time. See [Cutting a Video Greedily](https://labuladong.online/algo/frequency-interview/cut-video/).

**Scenario 3**: remove intervals fully covered by other intervals.

Sort by start; find and remove. See [Removing Covered Intervals](https://labuladong.online/algo/practice-in-action/interval-problem-summary/).

**Scenario 4**: merge overlapping intervals.

Sort by start; sweep. See [Merging Overlapping Intervals](https://labuladong.online/algo/practice-in-action/interval-problem-summary/).

**Scenario 5**: two departments booked the same room; find conflict periods.

Find the intersection of two interval lists. Sort by start. See [Interval Intersection](https://labuladong.online/algo/practice-in-action/interval-problem-summary/).

**Scenario 6**: one room, many meetings — minimize idle time.

A 0/1 knapsack variant: room = knapsack; each meeting = item; value = duration. The constraint isn't weight but mutual non-overlap. Sort by end time and use a TreeMap with the [0/1 knapsack](https://labuladong.online/algo/dynamic-programming/knapsack1/) idea.

LeetCode 1235 "Maximum Profit in Job Scheduling" is similar; see my Chrome plugin for a detailed answer.

**Scenario 7** — today's: minimize the number of rooms.

OK, let's solve it.







## Analysis

The essence:

**Given intervals, find the maximum number that overlap at any single point in time.**

Key: at any moment, can you say how many meetings are running?

If yes, scan all moments and find the max.

What data structure / algorithm tells me, given many intervals, how many overlap at each point?

Veteran readers will recall [Difference Arrays](https://labuladong.online/algo/data-structure/diff-array/).

Treat the timeline as an all-zero array; each meeting `[i, j]` is a subarray to increment by 1. Then scan to find the max — that's the room count.

E.g., `meetings = [[0,30],[5,10],[15,20]]`: increment those three subarrays; scan for max.

Difference arrays do range increments in O(1), so they fit. But not the most efficient — I'll skip the explicit code; refer to [Difference Arrays](https://labuladong.online/algo/data-structure/diff-array/) and try yourself.







**From the difference-array idea, derive a more elegant, more efficient solution.**

Project the meeting intervals onto a timeline:

![](https://labuladong.online/algo/images/arrange-room/1.jpeg)

Red dots = start times; green dots = end times.

Imagine a sweep line moving left to right with a counter. Hit a red dot → `count++`; hit a green dot → `count--`:

![](https://labuladong.online/algo/images/arrange-room/2.jpeg)

**Counter `count` is the number of concurrent meetings; max `count` is the room count.**

If you're familiar with difference arrays, this sweep is essentially traversing the difference array.

## Code

Project the intervals — sort starts and ends separately:

![](https://labuladong.online/algo/images/arrange-room/3.jpeg)

```java
int minMeetingRooms(int[][] meetings) {
    int n = meetings.length;
    int[] begin = new int[n];
    int[] end = new int[n];
    // Extract starts and ends
    for(int i = 0; i < n; i++) {
        begin[i] = meetings[i][0];
        end[i] = meetings[i][1];
    }
    // Sorted starts = red dots
    Arrays.sort(begin);
    // Sorted ends = green dots
    Arrays.sort(end);

    // ...
}
```

Then sweep — red increments, green decrements; track the max:

```java
class Solution {
    public int minMeetingRooms(int[][] meetings) {
        int n = meetings.length;
        int[] begin = new int[n];
        int[] end = new int[n];
        for(int i = 0; i < n; i++) {
            begin[i] = meetings[i][0];
            end[i] = meetings[i][1];
        }
        Arrays.sort(begin);
        Arrays.sort(end);

        // Sweep counter
        int count = 0;
        // Two-pointer
        int res = 0, i = 0, j = 0;
        while (i < n && j < n) {
            if (begin[i] < end[j]) {
                // A red dot
                count++;
                i++;
            } else {
                // A green dot
                count--;
                j++;
            }
            // Track max during the sweep
            res = Math.max(res, count);
        }
        
        return res;
    }
}
```

We use [two pointers](https://labuladong.online/algo/essential-technique/array-two-pointers-summary/) — `i` and `j` simulate the sweep.

Done. A variant: given meetings and `k` rooms, decide if `k` is enough — the same code easily handles this.








<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1235. Maximum Profit in Job Scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/?show=1) | [1235. Maximum Profit in Job Scheduling](https://leetcode.cn/problems/maximum-profit-in-job-scheduling/?show=1) | 🔴 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
