# One method demolishes the LeetCode House Robber series


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [198. House Robber](https://leetcode.com/problems/house-robber/) | [198. House Robber](https://leetcode.cn/problems/house-robber/) | 🟠 |
| [213. House Robber II](https://leetcode.com/problems/house-robber-ii/) | [213. House Robber II](https://leetcode.cn/problems/house-robber-ii/) | 🟠 |
| [337. House Robber III](https://leetcode.com/problems/house-robber-iii/) | [337. House Robber III](https://leetcode.cn/problems/house-robber-iii/) | 🟠 |

**-----------**


> [!NOTE]
> Before reading, you should first study:
> 
> - [Binary tree algorithm series (outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)

Today let's cover the "House Robber" series, which is a representative and tricky DP problem set.

The series has three problems with reasonable, progressive difficulty. The first is a fairly standard DP problem; the second adds a circular-array twist; the third combines DP's bottom-up and top-down solutions with binary trees, which I find very illuminating.

Let's start with the first one.

## House Robber I

LeetCode 198 "House Robber":

A row of houses is given as a non-negative integer array `nums`, where `nums[i]` is the cash in house `i`. You're a professional thief who wants to steal **as much cash as possible** from these houses. But **adjacent houses cannot be robbed at the same time** — that triggers the alarm and you're done.

Write an algorithm to compute the most cash you can steal without setting off any alarms. Function signature:

```java
int rob(int[] nums);
```

For example, with `nums=[2,1,7,9,3,1]`, the algorithm returns 12. The thief robs `nums[0], nums[3], nums[5]`, totaling 2 + 9 + 1 = 12 — the optimal choice.

The problem is easy to understand and clearly DP. As summarized in the earlier [DP framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/), **solving DP problems is just finding "states" and "choices", that's it**.


Imagine you're this professional robber walking left to right past the houses. At each house, you have two **choices**: rob or skip.

If you rob a house, you **cannot** rob the immediately next one — you can only choose starting from the house after that.

If you skip, you walk to the next house and choose again.

Once you pass the last house, there's nothing left to rob — the cash you can steal is 0 (**base case**).

Pretty simple logic — we've already identified the "state" and "choices": **the index of the house in front of you is the state; rob or skip is the choice**.

![](https://labuladong.online/algo/images/robber/1.jpg)

Each step, pick the larger of the two outcomes; the final answer is the most money you can steal:

```java
class Solution {
    // main function
    public int rob(int[] nums) {
        return dp(nums, 0);
    }

    // definition: return the max value robbable from nums[start..]
    private int dp(int[] nums, int start) {
        if (start >= nums.length) {
            return 0;
        }
        
        int res = Math.max(
                // skip, move to next house
                dp(nums, start + 1), 
                // rob, jump two houses ahead
                nums[start] + dp(nums, start + 2)
            );
        return res;
    }
}
```

With the state transition clear, you can see overlapping subproblems for the same `start`:

![](https://labuladong.online/algo/images/robber/2.jpg)

The thief has multiple ways to reach this position — recursing each time wastes work. So there are overlapping subproblems; use a memo:

```java
class Solution {

    private int[] memo;
    // main function
    public int rob(int[] nums) {
        // initialize the memo
        memo = new int[nums.length];
        Arrays.fill(memo, -1);
        // the robber starts robbing from house 0
        return dp(nums, 0);
    }

    // definition: return the max value robbable from dp[start..]
    private int dp(int[] nums, int start) {
        if (start >= nums.length) {
            return 0;
        }
        // avoid redundant computation
        if (memo[start] != -1) return memo[start];
        
        int res = Math.max(
            dp(nums, start + 1), 
            dp(nums, start + 2) + nums[start]
        );
        // record into memo
        memo[start] = res;
        return res;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/house-robber/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Code visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>


That's the top-down DP solution. We can lightly modify it to write a **bottom-up** version:

```java
class Solution {
    public int rob(int[] nums) {
        int n = nums.length;
        // dp[i] = x means:
        // starting from house i, max cash robbable is x
        // base case: dp[n] = 0
        int[] dp = new int[n + 2];
        for (int i = n - 1; i >= 0; i--) {
            dp[i] = Math.max(dp[i + 1], nums[i] + dp[i + 2]);
        }
        return dp[0];
    }
}
```

We notice the state transition only depends on the two states immediately following `dp[i]`, so we can further reduce the space complexity to O(1):

```java
class Solution {
    public int rob(int[] nums) {
        int n = nums.length;
        // record dp[i+1] and dp[i+2]
        int dp_i_1 = 0, dp_i_2 = 0;
        // record dp[i]
        int dp_i = 0; 
        for (int i = n - 1; i >= 0; i--) {
            dp_i = Math.max(dp_i_1, nums[i] + dp_i_2);
            dp_i_2 = dp_i_1;
            dp_i_1 = dp_i;
        }
        return dp_i;
    }
}
```

The above flow is detailed in our [DP framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/) — by now this should be second nature. What I find interesting is the follow-up, which requires a clever twist of our current approach.

## House Robber II

LeetCode 213 "House Robber II" is essentially the same as before — robber still can't rob adjacent houses, input still an array — but now **the houses aren't in a row, they're arranged in a circle**.

So now the first and last houses are adjacent and can't both be robbed. For example, with `nums=[2,3,2]`, the algorithm should return 3, not 4 — first and last can't both be robbed.

This constraint shouldn't be too hard. The earlier [Monotonic stack problem set](https://labuladong.online/algo/data-structure/monotonic-stack/) covered one way to handle circular arrays — how do we handle it here?

First, since first and last can't both be robbed, only three cases are possible: neither is robbed; first is robbed but not last; last is robbed but not first.

![](https://labuladong.online/algo/images/robber/3.jpg)

Easy then — among these three cases, whichever yields the most is the answer. But we don't even need to compare all three; just compare cases 2 and 3, **because they offer more freedom of choice than case 1, and since house values are non-negative, the optimal result with more options can't be worse**.

So we just slightly modify the previous solution:

```java
class Solution {
    public int rob(int[] nums) {
        int n = nums.length;
        if (n == 1) return nums[0];
        return Math.max(robRange(nums, 0, n - 2), 
                        robRange(nums, 1, n - 1));
    }

    // definition: return the max value robbable in the closed range [start, end]
    int robRange(int[] nums, int start, int end) {
        int n = nums.length;
        int dp_i_1 = 0, dp_i_2 = 0;
        int dp_i = 0;
        for (int i = end; i >= start; i--) {
            dp_i = Math.max(dp_i_1, nums[i] + dp_i_2);
            dp_i_2 = dp_i_1;
            dp_i_1 = dp_i;
        }
        return dp_i;
    }
}
```

Problem 2 solved.

## House Robber III

LeetCode 337 "House Robber III" gets even more creative. Now the robber faces houses that are neither in a row nor in a circle — they're on a binary tree! Houses sit at tree nodes, and two connected houses can't both be robbed. Truly high-IQ crime. Function signature:

```java
int rob(TreeNode root);
```

For example, given this binary tree:

```
     3
    / \
   2   3
    \   \ 
     3   1
```

The algorithm should return 7 — robbing layer 1 and layer 3 yields the maximum 3 + 3 + 1 = 7.

Given:

```
     3
    / \
   4   5
  / \   \ 
 1   3   1
```

The algorithm should return 9 — robbing layer 2 yields the maximum 4 + 5 = 9.

The overall idea is unchanged — choose rob or skip and take the larger result. We can directly write the routine code:

```java
class Solution {
    Map<TreeNode, Integer> memo = new HashMap<>();
    public int rob(TreeNode root) {
        if (root == null) return 0;
        // use memo to eliminate overlapping subproblems
        if (memo.containsKey(root)) 
            return memo.get(root);
        // rob, then jump two houses ahead
        int do_it = root.val
            + (root.left == null ? 
                0 : rob(root.left.left) + rob(root.left.right))
            + (root.right == null ? 
                0 : rob(root.right.left) + rob(root.right.right));
        // skip, move to the next house
        int not_do = rob(root.left) + rob(root.right);
        
        int res = Math.max(do_it, not_do);
        memo.put(root, res);
        return res;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/house-robber-iii/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>👾 Code visualization 👾</strong>
</summary>
</details>
</a>
<hr/>


Time-complexity analysis: although the recursion looks like a 4-ary tree, with memoization the function effectively visits each node once — so time complexity is $O(N)$ where `N` is the number of tree nodes. Space complexity is the memo size, $O(N)$.

If you're confused about time/space complexity, see the [Practical guide to time/space complexity analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/).

The clever part of this problem: there's an even prettier solution. A reader commented this:

```java
class Solution {
    int rob(TreeNode root) {
        int[] res = dp(root);
        return Math.max(res[0], res[1]);
    }

    // returns an array of size 2, arr
    // arr[0] = max cash if we don't rob root
    // arr[1] = max cash if we rob root
    int[] dp(TreeNode root) {
        if (root == null)
            return new int[]{0, 0};
        int[] left = dp(root.left);
        int[] right = dp(root.right);
        // rob — children can't be robbed
        int rob = root.val + left[0] + right[0];
        // skip — children may or may not be robbed; pick the larger
        int not_rob = Math.max(left[0], left[1])
                    + Math.max(right[0], right[1]);
        
        return new int[]{not_rob, rob};
    }
}
```

Time complexity is still $O(N)$, but space is just the recursion stack — the tree's height $O(H)$ — no memo needed.

Notice this differs from our approach: the recursive function's definition is changed, the idea slightly tweaked, the logic is self-consistent, the answer is still correct, and the code is prettier. This leverages the post-order position trick from the earlier [Binary tree mind-set (outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/).

In practice this solution runs much faster than ours, even though the asymptotic time complexity matches. Reason: it doesn't use a memo, reducing data-operation overhead, so actual runtime is faster.


<hr>
<details class="hint-container details">
<summary><strong>Problems citing this article</strong></summary>

<strong>Install [my Chrome extension](https://labuladong.online/algo/intro/chrome/) and click any problem below to view its solution outline:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| - | [Sword to Offer II 089. House Robber](https://leetcode.cn/problems/Gu0c2T/?show=1) | 🟠 |
| - | [Sword to Offer II 090. House Robber II](https://leetcode.cn/problems/PzWKhm/?show=1) | 🟠 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

