# Backtracking Algorithm Framework


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [46. Permutations](https://leetcode.com/problems/permutations/) | [46. Permutations](https://leetcode.cn/problems/permutations/) | 🟠 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Binary Tree Structure Basics](https://labuladong.online/algo/data-structure-basic/binary-tree-basic/)
> - [Binary Tree Traversal Framework](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/)
> - [N-ary Tree Structure and Traversal Framework](https://labuladong.online/algo/data-structure-basic/n-ary-tree-traverse-basic/)

> tip: a video version is available: [Backtracking Framework Walk-through](https://www.bilibili.com/video/BV1P5411N7Xc/). Follow my Bilibili account; I'll guide you through more difficult algorithm techniques on video.


This is the upgraded version of an older article on backtracking. Once the framework is clear, all backtracking problems follow the same pattern.

This article answers:

What is backtracking? What are the techniques for solving backtracking problems? How do I learn it? Are there patterns in the code?

Backtracking and what we usually call DFS are essentially the same algorithm; their subtle differences are explained in [Some Questions about DFS and Backtracking](https://labuladong.online/algo/essential-technique/backtrack-vs-dfs/). This article focuses on backtracking and won't expand on the differences.

**Abstractly, solving a backtracking problem is traversing a decision tree where each leaf is a valid answer. Traverse the entire tree and collect all leaves to get all valid answers.**

At any node of the backtracking tree, you only need to think about three things:

1. Path: the choices you've already made.

2. Choices: the choices currently available.

3. Termination: when no more choices can be made.

Don't worry if these terms are unclear — we'll use the classic permutations problem to make them concrete.

In code, the backtracking framework is:

```python
result = []
def backtrack(path, choices):
    if termination condition met:
        result.add(path)
        return
    
    for choice in choices:
        make choice
        backtrack(path, choices)
        undo choice
```


**At its core, recursion inside a for loop, "making a choice" before each recursive call and "undoing the choice" after**. Very simple.

What does "make/undo choice" mean? What's the underlying principle? Let's solve the classic permutations problem.

## Permutations

LeetCode 46 "Permutations": given an array `nums`, return all permutations.

> [!NOTE]
> **The version here doesn't include duplicates; the duplicates extension is in [Backtracking Sweeps Permutations/Combinations/Subsets](https://labuladong.online/algo/essential-technique/permutation-combination-subset-all-in-one/).**
> 
> Some readers may have seen permutation code using `swap`, which differs from this article. Those are two perspectives on backtracking; see [Box-and-Ball Model: Two Perspectives on Backtracking](https://labuladong.online/algo/practice-in-action/two-views-of-backtrack/). For now, follow this article.

In high school we learned `n!` for the number of permutations of `n` distinct items. How did we enumerate them?

For `[1,2,3]`, you wouldn't shuffle randomly — you'd do something like:

Fix the first as 1; then the second is 2 → third must be 3; flip second to 3 → third becomes 2; then change first to 2; ...

That's exactly backtracking — many of us learned it intuitively in school. Or you'd draw this tree:

![](https://labuladong.online/algo/images/backtracking/1.jpg)

Walking from the root, the path's numbers form a permutation. **We call this the "decision tree" of backtracking.**

**Why a "decision" tree? Because at each node you make a decision.** At the red node:

![](https://labuladong.online/algo/images/backtracking/2.jpg)

You're deciding whether to take 1 or 3. Why only those two? Because 2 is behind you — you've already chosen it; permutations don't allow repeats.

**Now we can define the terms: `[2]` is the "path" (choices made); `[1,3]` is the "choices" (currently available); the "termination condition" is reaching a leaf — i.e., the choices list is empty.**

If clear, treat "path" and "choices" as attributes of each node. The figure shows a few blue nodes' attributes:

![](https://labuladong.online/algo/images/backtracking/3.jpg)

**`backtrack` is like a pointer walking the tree, maintaining each node's attributes; whenever it hits a leaf, the "path" is a permutation.**

How to traverse a tree? Per [Framework Thinking for DS&A](https://labuladong.online/algo/essential-technique/algorithm-summary/), search problems are tree traversals; the multiway-tree framework:

```java
void traverse(TreeNode root) {
    for (TreeNode child : root.childern) {
        // Preorder operations
        traverse(child);
        // Postorder operations
    }
}
```

> [!NOTE]
> Sharp readers will ask: shouldn't preorder and postorder positions be outside the for loop, not inside?
> 
> Yes — for DFS. Backtracking and DFS differ slightly; see [Some Questions about DFS and Backtracking](https://labuladong.online/algo/essential-technique/backtrack-vs-dfs/). Ignore this for now.

Preorder/postorder are simply two useful "moments". A picture:

![](https://labuladong.online/algo/images/backtracking/4.jpg)

**Preorder code runs just before entering a node; postorder code runs just after leaving.**

Recall: "path" and "choices" are node attributes; the function walking the tree must update them at these two moments:

![](https://labuladong.online/algo/images/backtracking/5.jpg)

So the core framework:

```python
for choice in choices:
    # Make choice
    remove choice from choices
    path.add(choice)
    backtrack(path, choices)
    # Undo choice
    path.remove(choice)
    add choice back to choices
```

**Make the choice before recursion; undo it after** — that's all.

Permutations code:

```java
class Solution {
    List<List<Integer>> res = new LinkedList<>();

    // Main: given distinct numbers, return all permutations
    List<List<Integer>> permute(int[] nums) {
        // Records the path
        LinkedList<Integer> track = new LinkedList<>();
        // used[i] is true if nums[i] is in the path
        boolean[] used = new boolean[nums.length];
        
        backtrack(nums, track, used);
        return res;
    }

    // Path: kept in track
    // Choices: nums elements not in track (used[i] == false)
    // Termination: all nums in track
    void backtrack(int[] nums, LinkedList<Integer> track, boolean[] used) {
        // Termination
        if (track.size() == nums.length) {
            res.add(new LinkedList(track));
            return;
        }
        
        for (int i = 0; i < nums.length; i++) {
            // Skip invalid choices
            if (used[i]) {
                // nums[i] already in track
                continue;
            }
            // Make choice
            track.add(nums[i]);
            used[i] = true;
            // Recurse
            backtrack(nums, track, used);
            // Undo choice
            track.removeLast();
            used[i] = false;
        }
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/permutations/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Animated Code Visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>


We didn't track an explicit "choices" list — `used` tells us which `nums` elements are already in `track`:

![](https://labuladong.online/algo/images/backtracking/6.jpg)

That walks through backtracking via the permutations problem. This isn't the most efficient permutations algorithm — some solutions don't even use `used` and just swap elements. Those are slightly harder to understand; see [Box-and-Ball Model: Two Backtracking Perspectives](https://labuladong.online/algo/practice-in-action/two-views-of-backtrack/).

But however you optimize, you stick to the framework, and the time complexity can't go below O(N!) — you must enumerate all N! results.

**Backtracking is brute-force enumeration; unlike DP, there are no overlapping subproblems to exploit. Complexity is generally high.**

## Wrap-Up

Backtracking is multiway-tree traversal; key code goes at preorder and postorder positions:

```python
def backtrack(...):
    for choice in choices:
        make choice
        backtrack(...)
        undo choice
```

**In `backtrack`, maintain the "path" and "choices"; when you hit termination, append the path to the result.**

Backtracking and DP — similar in some ways? In DP we emphasize "state", "choice", and "base case" — corresponding to "path", "choices", and "termination" here.

Both abstract the problem into a tree, but their reasoning differs. See [Binary Tree Tactics (Outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/) for the deeper distinctions.


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Trie / Prefix Tree Implementation](https://labuladong.online/algo/data-structure/trie-implement/)
 - [Choosing Base Cases and Memo Initial Values](https://labuladong.online/algo/dynamic-programming/memo-fundamental/)
 - [[Practice] Combining Both Mindsets](https://labuladong.online/algo/problem-set/binary-tree-combine-two-view/)
 - [[Practice] Classic Backtracking Problems I](https://labuladong.online/algo/problem-set/backtrack-i/)
 - [[Practice] Classic Backtracking Problems II](https://labuladong.online/algo/problem-set/backtrack-ii/)
 - [[Practice] Classic Backtracking Problems III](https://labuladong.online/algo/problem-set/backtrack-iii/)
 - [Sweeping All Island Problems](https://labuladong.online/algo/frequency-interview/island-dfs-summary/)
 - [Binary Tree Basics and Common Types](https://labuladong.online/algo/data-structure-basic/binary-tree-basic/)
 - [Binary Tree Algorithm Core Outline](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
 - [Divide-and-Conquer Framework](https://labuladong.online/algo/essential-technique/divide-and-conquer/)
 - [Mind-Switch Between DP and Backtracking](https://labuladong.online/algo/dynamic-programming/word-break/)
 - [Backtracking in Practice: Generate Parentheses](https://labuladong.online/algo/practice-in-action/generate-parentheses/)
 - [Backtracking in Practice: Sudoku and N-Queens](https://labuladong.online/algo/practice-in-action/sudoku-nqueue/)
 - [Backtracking in Practice: Set Partitioning](https://labuladong.online/algo/practice-in-action/partition-to-k-equal-sum-subsets/)
 - [Backtracking Sweeps All Permutation/Combination/Subset Problems](https://labuladong.online/algo/essential-technique/permutation-combination-subset-all-in-one/)
 - [Framework Thinking for Learning Data Structures and Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Box-and-Ball Model: Two Backtracking Perspectives](https://labuladong.online/algo/practice-in-action/two-views-of-backtrack/)
 - [Algorithm Practice and Flow](https://labuladong.online/algo/fname.html?fname=flow)
 - [Practical Guide to Time/Space Complexity Analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/)
 - ["Trick" Patterns for Algorithm Quizzes](https://labuladong.online/algo/other-skills/tips-in-exam/)
 - [Classic DP: Burst Balloons](https://labuladong.online/algo/dynamic-programming/burst-balloons/)
 - [Knapsack Variant: Target Sum](https://labuladong.online/algo/dynamic-programming/target-sum/)
 - [Answers to Common Backtracking/DFS Questions](https://labuladong.online/algo/essential-technique/backtrack-vs-dfs/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [111. Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree/?show=1) | [111. Minimum Depth of Binary Tree](https://leetcode.cn/problems/minimum-depth-of-binary-tree/?show=1) | 🟢 |
| [112. Path Sum](https://leetcode.com/problems/path-sum/?show=1) | [112. Path Sum](https://leetcode.cn/problems/path-sum/?show=1) | 🟢 |
| [113. Path Sum II](https://leetcode.com/problems/path-sum-ii/?show=1) | [113. Path Sum II](https://leetcode.cn/problems/path-sum-ii/?show=1) | 🟠 |
| [131. Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/?show=1) | [131. Palindrome Partitioning](https://leetcode.cn/problems/palindrome-partitioning/?show=1) | 🟠 |
| [140. Word Break II](https://leetcode.com/problems/word-break-ii/?show=1) | [140. Word Break II](https://leetcode.cn/problems/word-break-ii/?show=1) | 🔴 |
| [1593. Split a String Into the Max Number of Unique Substrings](https://leetcode.com/problems/split-a-string-into-the-max-number-of-unique-substrings/?show=1) | [1593. Split a String Into the Max Number of Unique Substrings](https://leetcode.cn/problems/split-a-string-into-the-max-number-of-unique-substrings/?show=1) | 🟠 |
| [17. Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/?show=1) | [17. Letter Combinations of a Phone Number](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/?show=1) | 🟠 |
| [22. Generate Parentheses](https://leetcode.com/problems/generate-parentheses/?show=1) | [22. Generate Parentheses](https://leetcode.cn/problems/generate-parentheses/?show=1) | 🟠 |
| [301. Remove Invalid Parentheses](https://leetcode.com/problems/remove-invalid-parentheses/?show=1) | [301. Remove Invalid Parentheses](https://leetcode.cn/problems/remove-invalid-parentheses/?show=1) | 🔴 |
| [332. Reconstruct Itinerary](https://leetcode.com/problems/reconstruct-itinerary/?show=1) | [332. Reconstruct Itinerary](https://leetcode.cn/problems/reconstruct-itinerary/?show=1) | 🔴 |
| [39. Combination Sum](https://leetcode.com/problems/combination-sum/?show=1) | [39. Combination Sum](https://leetcode.cn/problems/combination-sum/?show=1) | 🟠 |
| [51. N-Queens](https://leetcode.com/problems/n-queens/?show=1) | [51. N-Queens](https://leetcode.cn/problems/n-queens/?show=1) | 🔴 |
| [638. Shopping Offers](https://leetcode.com/problems/shopping-offers/?show=1) | [638. Shopping Offers](https://leetcode.cn/problems/shopping-offers/?show=1) | 🟠 |
| [698. Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/?show=1) | [698. Partition to K Equal Sum Subsets](https://leetcode.cn/problems/partition-to-k-equal-sum-subsets/?show=1) | 🟠 |
| [77. Combinations](https://leetcode.com/problems/combinations/?show=1) | [77. Combinations](https://leetcode.cn/problems/combinations/?show=1) | 🟠 |
| [78. Subsets](https://leetcode.com/problems/subsets/?show=1) | [78. Subsets](https://leetcode.cn/problems/subsets/?show=1) | 🟠 |
| [784. Letter Case Permutation](https://leetcode.com/problems/letter-case-permutation/?show=1) | [784. Letter Case Permutation](https://leetcode.cn/problems/letter-case-permutation/?show=1) | 🟠 |
| [89. Gray Code](https://leetcode.com/problems/gray-code/?show=1) | [89. Gray Code](https://leetcode.cn/problems/gray-code/?show=1) | 🟠 |
| [93. Restore IP Addresses](https://leetcode.com/problems/restore-ip-addresses/?show=1) | [93. Restore IP Addresses](https://leetcode.cn/problems/restore-ip-addresses/?show=1) | 🟠 |
| - | [Sword to Offer 34. Paths with a Given Sum in a Binary Tree](https://leetcode.cn/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/?show=1) | 🟠 |
| - | [Sword to Offer II 079. All Subsets](https://leetcode.cn/problems/TVdhkn/?show=1) | 🟠 |
| - | [Sword to Offer II 080. Combinations of k Elements](https://leetcode.cn/problems/uUsW3B/?show=1) | 🟠 |
| - | [Sword to Offer II 081. Combinations Allowing Repeats](https://leetcode.cn/problems/Ygoe9J/?show=1) | 🟠 |
| - | [Sword to Offer II 083. Permutations of Distinct Elements](https://leetcode.cn/problems/VvJkup/?show=1) | 🟠 |
| - | [Sword to Offer II 085. Generate Matching Parentheses](https://leetcode.cn/problems/IDBivT/?show=1) | 🟠 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
