# Tricks for Traversing 2D Arrays


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [151. Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/) | [151. Reverse Words in a String](https://leetcode.cn/problems/reverse-words-in-a-string/) | 🟠 |
| [48. Rotate Image](https://leetcode.com/problems/rotate-image/) | [48. Rotate Image](https://leetcode.cn/problems/rotate-image/) | 🟠 |
| [54. Spiral Matrix](https://leetcode.com/problems/spiral-matrix/) | [54. Spiral Matrix](https://leetcode.cn/problems/spiral-matrix/) | 🟠 |
| [59. Spiral Matrix II](https://leetcode.com/problems/spiral-matrix-ii/) | [59. Spiral Matrix II](https://leetcode.cn/problems/spiral-matrix-ii/) | 🟠 |
| [61. Rotate List](https://leetcode.com/problems/rotate-list/) | [61. Rotate List](https://leetcode.cn/problems/rotate-list/) | 🟠 |
| - | [Sword to Offer 29. Print Matrix in Spiral Order](https://leetcode.cn/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/) | 🟢 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Array Basics](https://labuladong.online/algo/data-structure-basic/array-basic/)

Many readers say that after reading the site's articles they've grasped the framework mindset and can solve most pattern-able problems.

But framework thinking isn't a silver bullet. Some specific tricks fall under "easy if you know it, hard if you don't" and can only be accumulated through practice.

This article shares some clever 2D-array operations — take a quick read, and similar problems won't catch you off-guard.

## Rotating a Matrix Clockwise / Counterclockwise

Rotating a 2D array is a common interview problem; LeetCode 48 "Rotate Image":

<Problem slug="rotate-image" />

The problem: rotate an n×n matrix 90° clockwise. **The catch is doing it in place.**

```java
void rotate(int[][] matrix)
```

How to rotate "in place"? Sounds tricky — maybe a clever ring-by-ring rotation:

![](https://labuladong.online/algo/images/2d-array/1.png)

**But this problem doesn't take the obvious path.** Before the clever solution, here's a Google warm-up:

Given a string `s` containing words separated by spaces, write an algorithm that **in place** reverses the order of the words.

E.g.:

```shell
s = "hello world labuladong"
```

Your algorithm reverses the word order in place:

```shell
s = "labuladong world hello"
```

The standard approach: split, reverse the list, join. But that's not in place.

**Correct trick: first reverse the entire string `s`**:

```shell
s = "gnodalubal dlrow olleh"
```

**Then reverse each word**:

```shell
s = "labuladong world hello"
```

That's an in-place word reversal. LeetCode 151 "Reverse Words in a String" is the same — give it a try.

This trick can be repackaged. LeetCode 61 "Rotate List": rotate a singly linked list right by `k` positions.

For `1 -> 2 -> 3 -> 4 -> 5`, `k = 2`, return `4 -> 5 -> 1 -> 2 -> 3`.

Don't move nodes one by one — actually, just move the last `k` nodes to the front, right?

Equivalent: reverse the first `n - k` nodes and the last `k` nodes in place. Same idea as reversing words.

So: reverse the whole list, then reverse the first `n - k` and the last `k`. Done.

There's a small detail: `k` may exceed the length, so set `k = k % n` first.

Try it; I won't paste the code.

What's the point of these examples?

**Sometimes the obvious thinking isn't the most elegant for a computer; conversely, what's elegant to a computer may be unintuitive for us.** That's the charm of algorithms.

Back to clockwise rotation. The naive approach is to derive index mappings; but maybe a leap of thought — flip, mirror — can open new paths.

**Mirror the n×n matrix along the top-left-to-bottom-right diagonal**:

![](https://labuladong.online/algo/images/2d-array/2.jpeg)

**Then reverse each row**:

![](https://labuladong.online/algo/images/2d-array/3.jpeg)

**Result: the matrix rotated 90° clockwise**:

![](https://labuladong.online/algo/images/2d-array/4.jpeg)

Code:

```java
class Solution {
    // Rotate the matrix 90° clockwise in place
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        // Mirror along the diagonal
        for (int i = 0; i < n; i++) {
            for (int j = i; j < n; j++) {
                // swap(matrix[i][j], matrix[j][i]);
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
        // Reverse each row
        for (int[] row : matrix) {
            reverse(row);
        }
    }

    // Reverse a 1D array
    void reverse(int[] arr) {
        int i = 0, j = arr.length - 1;
        while (j > i) {
            // swap(arr[i], arr[j]);
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
            i++;
            j--;
        }
    }
}
```

<visual slug='rotate-image'>

Click the visualization below; click <code type="click">let temp = matrix[i][j]</code> to see the diagonal flip; then click <code type="click">reverse(row)</code> to see the rows reverse:

</visual>

You may ask: how would I think of this without having seen it before?

Without prior exposure, it's not easy. But you've seen it now — easy if you know it, you'll never forget.

**By the way, how do we rotate counterclockwise 90°?**

Mirror along the other diagonal (top-right to bottom-left), then reverse each row:

![](https://labuladong.online/algo/images/2d-array/5.jpeg)

Code:

```java
class Solution {

    // Rotate the matrix 90° counterclockwise in place
    public void rotate2(int[][] matrix) {
        int n = matrix.length;
        // Mirror along the bottom-left-to-top-right diagonal
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n - i; j++) {
                // swap(matrix[i][j], matrix[n-j-1][n-i-1])
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n - j - 1][n - i - 1];
                matrix[n - j - 1][n - i - 1] = temp;
            }
        }
        // Reverse each row
        for (int[] row : matrix) {
            reverse(row);
        }
    }

    void reverse(int[] arr) {
        // See above
    }
}
```

That solves the rotation problem.

## Spiral Traversal

LeetCode 54 "Spiral Matrix":

<Problem slug="spiral-matrix" />

```java
// Signature
List<Integer> spiralOrder(int[][] matrix)
```

**The core idea: traverse in right, down, left, up order, using four variables to bound the unvisited region**:

![](https://labuladong.online/algo/images/2d-array/6.png)

The bounds shrink as we spiral inward, until done:

![](https://labuladong.online/algo/images/2d-array/7.png)

Code:

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        int upper_bound = 0, lower_bound = m - 1;
        int left_bound = 0, right_bound = n - 1;
        List<Integer> res = new LinkedList<>();
        // res.size() == m * n means we're done
        while (res.size() < m * n) {
            if (upper_bound <= lower_bound) {
                // Top: left to right
                for (int j = left_bound; j <= right_bound; j++) {
                    res.add(matrix[upper_bound][j]);
                }
                // Move the top boundary down
                upper_bound++;
            }
            
            if (left_bound <= right_bound) {
                // Right: top to bottom
                for (int i = upper_bound; i <= lower_bound; i++) {
                    res.add(matrix[i][right_bound]);
                }
                // Move the right boundary left
                right_bound--;
            }
            
            if (upper_bound <= lower_bound) {
                // Bottom: right to left
                for (int j = right_bound; j >= left_bound; j--) {
                    res.add(matrix[lower_bound][j]);
                }
                // Move the bottom boundary up
                lower_bound--;
            }
            
            if (left_bound <= right_bound) {
                // Left: bottom to top
                for (int i = lower_bound; i >= upper_bound; i--) {
                    res.add(matrix[i][left_bound]);
                }
                // Move the left boundary right
                left_bound++;
            }
        }
        return res;
    }
}
```

<visual slug='spiral-matrix' >

Click <code type="click">while (res.length < m * n)</code> repeatedly to see the spiral:

</visual>

LeetCode 59 "Spiral Matrix II" is the inverse: generate a matrix in spiral order:

<Problem slug="spiral-matrix-ii" />

```java
// Signature
int[][] generateMatrix(int n)
```

A small tweak:

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int[][] matrix = new int[n][n];
        int upper_bound = 0, lower_bound = n - 1;
        int left_bound = 0, right_bound = n - 1;
        // Number to fill in next
        int num = 1;
        
        while (num <= n * n) {
            if (upper_bound <= lower_bound) {
                // Top: left to right
                for (int j = left_bound; j <= right_bound; j++) {
                    matrix[upper_bound][j] = num++;
                }
                // Move the top boundary down
                upper_bound++;
            }
            
            if (left_bound <= right_bound) {
                // Right: top to bottom
                for (int i = upper_bound; i <= lower_bound; i++) {
                    matrix[i][right_bound] = num++;
                }
                // Move the right boundary left
                right_bound--;
            }
            
            if (upper_bound <= lower_bound) {
                // Bottom: right to left
                for (int j = right_bound; j >= left_bound; j--) {
                    matrix[lower_bound][j] = num++;
                }
                // Move the bottom boundary up
                lower_bound--;
            }
            
            if (left_bound <= right_bound) {
                // Left: bottom to top
                for (int i = lower_bound; i >= upper_bound; i--) {
                    matrix[i][left_bound] = num++;
                }
                // Move the left boundary right
                left_bound++;
            }
        }
        return matrix;
    }
}
```

<visual slug='spiral-matrix-ii' >

Click <code type="click">while (num <= n * n)</code> repeatedly to see the matrix being generated:

</visual>

Both spiral problems are now solved.

These are some 2D-array tricks. For more array techniques see [Prefix Sums](https://labuladong.online/algo/data-structure/prefix-sum/), [Difference Arrays](https://labuladong.online/algo/data-structure/diff-array/), [Array Two-Pointer Collection](https://labuladong.online/algo/essential-technique/array-two-pointers-summary/). For linked lists see [Six Linked-List Techniques](https://labuladong.online/algo/essential-technique/linked-list-skills-summary/).


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Classic Two-Pointer Array Problems](https://labuladong.online/algo/problem-set/array-two-pointers/)
 - [Two-Pointer Tricks Sweep 7 Array Problems](https://labuladong.online/algo/essential-technique/array-two-pointers-summary/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1260. Shift 2D Grid](https://leetcode.com/problems/shift-2d-grid/?show=1) | [1260. Shift 2D Grid](https://leetcode.cn/problems/shift-2d-grid/?show=1) | 🟢 |
| [1329. Sort the Matrix Diagonally](https://leetcode.com/problems/sort-the-matrix-diagonally/?show=1) | [1329. Sort the Matrix Diagonally](https://leetcode.cn/problems/sort-the-matrix-diagonally/?show=1) | 🟠 |
| [867. Transpose Matrix](https://leetcode.com/problems/transpose-matrix/?show=1) | [867. Transpose Matrix](https://leetcode.cn/problems/transpose-matrix/?show=1) | 🟢 |
| - | [Sword to Offer 58 - I. Reverse Word Order](https://leetcode.cn/problems/fan-zhuan-dan-ci-shun-xu-lcof/?show=1) | 🟢 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
