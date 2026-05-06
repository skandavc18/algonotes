# Pancake Sorting


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [969. Pancake Sorting](https://leetcode.com/problems/pancake-sorting/) | [969. Pancake Sorting](https://leetcode.cn/problems/pancake-sorting/) | 🟠 |

**-----------**


LeetCode 969 "Pancake Sorting" is an interesting practical problem: suppose a plate has `n` pancakes of **different sizes**. How can you, using a spatula, perform a series of flips so the pancakes end up sorted (smallest on top, largest at the bottom)?

![](https://labuladong.online/algo/images/pancakeSort/1.jpg)

Flipping a stack of pancakes with a spatula has a constraint: each flip can only flip the topmost few pancakes:

![](https://labuladong.online/algo/images/pancakeSort/2.png)

The question: **how do we devise an algorithm that produces a flip sequence to sort the stack**?

First, abstract the problem with an array representing the stack:

<Problem slug="pancake-sorting" />

Similar to the earlier article [Recursive Linked-List Reversal in Parts](https://labuladong.online/algo/data-structure/reverse-linked-list-recursion/), this also relies on **recursive thinking**.

### 1. Idea

Why is this problem recursive? Suppose we want to implement:

```java
// cakes is a stack of pancakes; the function sorts the first n
void sort(int[] cakes, int n);
```

If we find the largest among the first `n` pancakes and flip it to the bottom:

![](https://labuladong.online/algo/images/pancakeSort/3.jpg)

Then the problem shrinks; recursively call `pancakeSort(A, n-1)`:

![](https://labuladong.online/algo/images/pancakeSort/4.jpg)

Now how to sort the remaining `n - 1` pancakes? Find the largest, send it to the bottom, recursively call `pancakeSort(A, n-1-1)`...

That's the recursion. Summary:

1. Find the largest among the top `n` pancakes.

2. Move it to the bottom.

3. Recurse on `pancakeSort(A, n - 1)`.

Base case: when `n == 1`, no flips needed.

Finally, **how do we send a particular pancake to the bottom?**

Easy. Suppose pancake at position 3 is the largest and we want it at position `n`:

1. Flip the top 3 — the largest is now on top.

2. Flip the top `n` — the largest is now at position `n`.

With that we can write the solution. The problem also asks for the actual flip sequence — just record each flip.

### 2. Implementation

Just translate the idea. Note: array indices start at 0, but the answer uses 1-based positions.

```java
class Solution {
    // Records the flip operations
    LinkedList<Integer> res = new LinkedList<>();

    public List<Integer> pancakeSort(int[] cakes) {
        sort(cakes, cakes.length);
        return res;
    }

    void sort(int[] cakes, int n) {
        // base case
        if (n == 1) return;
        
        // Find the index of the largest pancake
        int maxCake = 0;
        int maxCakeIndex = 0;
        for (int i = 0; i < n; i++)
            if (cakes[i] > maxCake) {
                maxCakeIndex = i;
                maxCake = cakes[i];
            }
        
        // First flip: bring the largest to the top
        reverse(cakes, 0, maxCakeIndex);
        res.add(maxCakeIndex + 1);
        // Second flip: bring the largest to the bottom
        reverse(cakes, 0, n - 1);
        res.add(n);

        // Recurse
        sort(cakes, n - 1);
    }

    void reverse(int[] arr, int i, int j) {
        while (i < j) {
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
            i++; j--;
        }
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/pancake-sorting/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌟 Animated Code Visualization 🌟</strong>
</summary>
</details>
</a>
<hr/>


With the explanation above, the code should be clear.

Time complexity: there are `n` recursive calls, each scanning O(n), so the total is O(n^2).

**One last question to ponder**: by our approach, the operation sequence has length `2(n - 1)` — each recursion does 2 flips, and there are `n` levels (the base case does no flips), so the length is fixed at `2(n - 1)`.

Clearly this isn't optimal (shortest). E.g. `[3,2,4,1]` — our algorithm yields `[3,4,2,3,1,2]`, but the fastest is `[2,3,4]`:

```
Initial    : [3,2,4,1]
Flip top 2 : [2,3,4,1]
Flip top 3 : [4,3,2,1]
Flip top 4 : [1,2,3,4]
```

If the algorithm has to compute the **shortest** flip sequence, how would you do it? More generally, what is the core idea / algorithmic technique for finding the optimal solution to this kind of problem?

Feel free to share your thoughts.


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)