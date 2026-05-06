# Weighted Random Selection



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [528. Random Pick with Weight](https://leetcode.com/problems/random-pick-with-weight/) | [528. Random Pick with Weight](https://leetcode.cn/problems/random-pick-with-weight/) | 🟠 |
| - | [Sword to Offer II 071. Random Number with Weight](https://leetcode.cn/problems/cuyjEf/) | 🟠 |

**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Prefix Sum Technique](https://labuladong.online/algo/data-structure/prefix-sum/)
> - [Binary Search Framework](https://labuladong.online/algo/essential-technique/binary-search-framework/)

This article was inspired by playing LoL Mobile. A friend complained his ranked teammates were terrible. I said mine were fine — was I just lucky?

He said, "Players with high hidden MMR get matched with poor teammates if no equally-strong teammates are available."

Hmm — was he saying my hidden MMR is low, or that I'm the noob? I demanded a duo to prove him wrong.

After playing, I had thoughts on matchmaking.

![](https://labuladong.online/algo/images/random-weight/images.png)

**I don't know if "hidden MMR" is real; matchmaking is core to competitive games and probably much more complex than a few metrics.**

But simplifying the mechanism, this is a worthwhile algorithmic question: how does the system match with different random probabilities?

In short: **weighted random selection**.

It's not trivial. With a length-`n` array, picking uniformly at random is easy — random in `[0, n-1]`, each with probability `1/n`.

But with weights, where larger weights mean larger selection probabilities, how would you write the algorithm?

LeetCode 528 "Random Pick with Weight":

<Problem slug="random-pick-with-weight" />

Let's solve it.







## Approach

A quick history:

[Designing a Random-Delete Data Structure](https://labuladong.online/algo/data-structure/random-set/) used clever data structures to avoid shifting elements.

[Random Algorithms in Games](https://labuladong.online/algo/frequency-interview/random-algorithm/) covered "reservoir sampling" — uniform sampling from an infinite stream with simple math.

[Test Tricks](https://labuladong.online/algo/other-skills/tips-in-exam/) shared a probability-maximization trick to game test cases.

**None of those help here. But [Prefix Sums](https://labuladong.online/algo/data-structure/prefix-sum/) plus [Binary Search](https://labuladong.online/algo/essential-technique/binary-search-framework/) does.**

How do prefix sums and binary search relate to randomness?

Suppose `w = [1,3,2,1]`. Draw a colored line proportional to weights:

![](https://labuladong.online/algo/images/random-weight/1.jpeg)

If I throw a stone at this line, the color it hits gives the index — and the probability of each index is proportional to its weight.

**Look closer — this colored line is a [prefix-sum array](https://labuladong.online/algo/data-structure/prefix-sum/)**:

![](https://labuladong.online/algo/images/random-weight/2.jpeg)







How do we simulate "throwing a stone"?

A random number, of course. For `preSum`, valid range is `[1, 7]`; pick `target = 5`:

![](https://labuladong.online/algo/images/random-weight/3.jpeg)

But `preSum` doesn't contain 5; we want the smallest element ≥ 5, which is 6 at index 3:

![](https://labuladong.online/algo/images/random-weight/4.jpeg)

**Finding the smallest element ≥ a target — [binary search](https://labuladong.online/algo/essential-technique/binary-search-framework/).**

Steps:

1. Build `preSum` from `w`.

2. Generate a random number in `preSum`'s range; binary-search for the smallest index whose `preSum` ≥ target.

3. Subtract 1 (because of the prefix-sum offset) to get the index in `w`:

![](https://labuladong.online/algo/images/random-weight/5.jpeg)







## Code

The idea is clear, but the code has pitfalls — open/closed intervals, off-by-one, binary search variants — get any wrong and you have hard-to-debug bugs.

Continuing the example:

![](https://labuladong.online/algo/images/random-weight/3.jpeg)

What's the valid range for `target`? `[0, 7]` or `[0, 7)`?

Neither — `[1, 7]` (closed). **The 0 in `preSum` is just a sentinel**:

![](https://labuladong.online/algo/images/random-weight/6.jpeg)

So:

```java
int n = preSum.length;
// target ranges over [1, preSum[n - 1]]
int target = rand.nextInt(preSum[n - 1]) + 1;
```

Which binary-search variant? Search the left bound:

```java
// Left-bound binary search
int left_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int left = 0, right = nums.length;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            right = mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid;
        }
    }
    return left;
}
```

In [Binary Search Detailed](https://labuladong.online/algo/essential-technique/binary-search-framework/) I focused on duplicates. Here's the absent-target case:

**When `target` isn't in `nums`, the left-bound search's return value can be interpreted as**:

1. The smallest index in `nums` whose value is ≥ `target`.

2. The position where `target` should be inserted to keep the array sorted.

3. The number of elements in `nums` strictly less than `target`.

E.g., `nums = [2,3,5,7]`, `target = 4` → return 2; all three interpretations are correct.

Equivalent — pick the most useful one. Here we want interpretation 1.

Final code:

```java
class Solution {
    // Prefix-sum array
    private int[] preSum;
    private Random rand = new Random();
    
    public Solution(int[] w) {
        int n = w.length;
        // Build prefix sums, offset by 1 with preSum[0] = 0
        preSum = new int[n + 1];
        preSum[0] = 0;
        // preSum[i] = sum(w[0..i-1])
        for (int i = 1; i <= n; i++) {
            preSum[i] = preSum[i - 1] + w[i - 1];
        }
    }
    
    public int pickIndex() {
        int n = preSum.length;
        // Java's nextInt(n) returns [0, n)
        // +1 maps to closed [1, preSum[n - 1]]
        int target = rand.nextInt(preSum[n - 1]) + 1;
        // Find the index of target in preSum
        // Don't forget the offset between preSum and w
        return left_bound(preSum, target) - 1;
    }

    // Left-bound binary search
    private int left_bound(int[] nums, int target) {
        int left = 0, right = nums.length;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                right = mid;
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else if (nums[mid] > target) {
                right = mid;
            }
        }
        return left;
    }
}
```

With the prep above, this should make sense — the weighted-random problem is solved.

Readers often joke they "cloud-solve" by reading my articles. Many problems are clear once explained, but the details have traps — like this one. So practice and reflect.









<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Boosting Hash Tables with Arrays (ArrayHashMap)](https://labuladong.online/algo/data-structure-basic/hashtable-with-array/)
 - [Random Algorithms in Games](https://labuladong.online/algo/frequency-interview/random-algorithm/)

</details><hr>





**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
