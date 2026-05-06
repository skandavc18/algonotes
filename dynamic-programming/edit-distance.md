# Classic DP: edit distance



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | 力扣 | Difficulty |
| :----: | :----: | :----: |
| [72. Edit Distance](https://leetcode.com/problems/edit-distance/) | [72. 编辑距离](https://leetcode.cn/problems/edit-distance/) | 🔴 |

**-----------**



> [!NOTE]
> Before reading, you should first study:
> 
> - [Binary tree algorithm series (outline)](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)


> Tip: this article has a video version: [Edit distance DP explained](https://www.bilibili.com/video/BV1uv411W73P/). Subscribe to my Bilibili channel — I lead readers through tougher algorithmic techniques in video form.



The other day I saw a Tencent interview question set; the algorithm portion was mostly DP, and the last problem was to write a function computing edit distance. So today we'll dedicate an article to this problem.

LeetCode 72 "Edit Distance" is the problem:





<Problem slug="edit-distance" />

```java
// function signature
int minDistance(String s1, String s2)
```

For readers new to DP, this problem is non-trivial — feel completely lost?

But the problem itself is quite practical. I once used this algorithm in real life. A WeChat public-account article had a misordered section I wanted to fix, but WeChat allows at most 20 character changes and only insert/delete/replace operations (exactly the edit-distance setup). I used the algorithm to find the optimal plan and made the edit in just 16 steps.

A fancier application: DNA sequences are strings of A, G, C, T — like strings. Edit distance measures their similarity; smaller distance means more similar — maybe the two DNA owners are distant relatives.

Now to the topic — I'll explain edit distance in detail. I'm sure you'll get something out of it.







## 1. Approach

The edit-distance problem: given two strings `s1` and `s2`, using only three operations, transform `s1` into `s2`, with the minimum number of operations. To be clear, transforming `s1` to `s2` or `s2` to `s1` gives the same result, so I'll use `s1 → s2` going forward.

> [!TIP]
> For DP problems on two strings, generally use two pointers `i, j` pointing to the heads or tails of the two strings, and try to write the state-transition equation.
> 
> For example, with `i, j` pointing to the tails, define `dp[i], dp[j]` as the edit distance of `s1[0..i], s2[0..j]`. As `i, j` move one step at a time toward the front, the problem size (substring length) shrinks gradually.
> 
> You could also have `i, j` point to the heads and move forward — essentially equivalent, just change the `dp` function/array definition.

Let the two strings be `"rad"` and `"apple"`. With `i, j` pointing to the tails of `s1, s2`, to transform `s1` into `s2`, the algorithm proceeds like this:

![](https://labuladong.online/algo/images/editDistance/edit.gif)

![](https://labuladong.online/algo/images/editDistance/1.jpg)

Remember this GIF — that's how to compute edit distance. The key is the right operations, discussed shortly.

From the GIF, we see there are not just three operations — actually a fourth: do nothing (skip). For example:

![](https://labuladong.online/algo/images/editDistance/2.jpg)

These two characters are already equal, so to minimize edit distance, no operation is needed — just advance `i, j`.

Another easy case: when `j` finishes `s2` but `i` hasn't finished `s1`, we can only use delete to shorten `s1` to `s2`. Like:

![](https://labuladong.online/algo/images/editDistance/3.jpg)

Similarly, if `i` finishes `s1` but `j` hasn't finished `s2`, we can only insert all of `s2`'s remaining characters into `s1`. As we'll see, these two are the algorithm's **base cases**.

Now let's translate the approach into code.







## 2. Code in detail

Recap:

Base case: when `i` finishes `s1` or `j` finishes `s2`, just return the remaining length of the other string.

For each pair `s1[i]` and `s2[j]`, four operations:

```python
if s1[i] == s2[j]:
    do nothing (skip)
    advance both i and j
else:
    pick one of three:
        insert
        delete
        replace
```

With this framework, the problem is solved. Reader may ask: how do we choose among the three? Easy — try them all, and pick the one that yields the smallest edit distance. Recursion handles this. Brute-force code:

```java
class Solution {
    public int minDistance(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        // i, j initialized to point to the last index
        return dp(s1, m - 1, s2, n - 1);
    }

    // definition: return the minimum edit distance of s1[0..i] and s2[0..j]
    int dp(String s1, int i, String s2, int j) {
        // base case
        if (i == -1) return j + 1;
        if (j == -1) return i + 1;

        if (s1.charAt(i) == s2.charAt(j)) {
            // do nothing
            return dp(s1, i - 1, s2, j - 1);
        }
        return min(
            // insert
            dp(s1, i, s2, j - 1) + 1,
            // delete
            dp(s1, i - 1, s2, j) + 1,
            // replace
            dp(s1, i - 1, s2, j - 1) + 1
        );
    }

    int min(int a, int b, int c) {
        return Math.min(a, Math.min(b, c));
    }
}
```

Let me explain this recursive code in detail. Base case is straightforward; let me explain the recursive part.

People say recursive code is highly self-explanatory — true: as long as you understand the function definition, the algorithm's logic is clear. Our `dp` is defined as:

```java
// definition: return the minimum edit distance of s1[0..i] and s2[0..j]
int dp(String s1, int i, String s2, int j)
```

**Hold this definition in mind**, and look at this snippet:

```python
if s1[i] == s2[j]:
    # do nothing
    return dp(s1, i - 1, s2, j - 1)
# Explanation:
# already equal — no operation needed
# minimum edit distance of s1[0..i] and s2[0..j] equals
# minimum edit distance of s1[0..i-1] and s2[0..j-1]
# i.e. dp(i, j) equals dp(i-1, j-1)
```

If `s1[i] != s2[j]`, we recurse over the three operations — needs a moment of thought:

```python
# insert
dp(s1, i, s2, j - 1) + 1,
# Explanation:
# I directly insert into s1[i] a character equal to s2[j]
# so s2[j] is matched; advance j and keep comparing with i
# don't forget to add 1 to the operation count
```

![](https://labuladong.online/algo/images/editDistance/insert.gif)

```python
# delete
dp(s1, i - 1, s2, j) + 1,
# Explanation:
# I directly delete s1[i]
# advance i and keep comparing with j
# add 1 to the operation count
```

![](https://labuladong.online/algo/images/editDistance/delete.gif)

```python
# replace
dp(s1, i - 1, s2, j - 1) + 1
# Explanation:
# I directly replace s1[i] with s2[j], so they match
# advance both i and j
# add 1 to the operation count
```

![](https://labuladong.online/algo/images/editDistance/replace.gif)



Now you should fully understand this short and elegant code. One issue: this is brute force — there are overlapping subproblems, and we need DP to optimize.

**How can you spot overlapping subproblems at a glance?** Covered in [DP FAQ](https://labuladong.online/algo/dynamic-programming/faq-summary/). Briefly: extract this algorithm's recursion framework:

```java
int dp(i, j) {
    dp(i - 1, j - 1); // #1
    dp(i, j - 1);     // #2
    dp(i - 1, j);     // #3
}
```

How can subproblem `dp(i-1, j-1)` be reached from the original problem `dp(i, j)`? More than one path: e.g. `dp(i, j) -> #1` and `dp(i, j) -> #2 -> #3`. Once a duplicate path appears, there are many duplicate paths — overlapping subproblems exist.

## 3. DP optimization

For overlapping subproblems, the earlier [DP framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/) explained: optimize either by adding a memo to the recursion or by writing the DP iteratively with a DP table. Let's cover both.

### Memoized solution

With the brute-force recursion already written, adding a memo is easy — small modifications to the original code:

```java
class Solution {
    // memo
    int[][] memo;
        
    public int minDistance(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        // initialize memo with a special value meaning not yet computed
        memo = new int[m][n];
        for (int[] row : memo) {
            Arrays.fill(row, -1);
        }
        return dp(s1, m - 1, s2, n - 1);
    }

    int dp(String s1, int i, String s2, int j) {
        if (i == -1) return j + 1;
        if (j == -1) return i + 1;
        // look up the memo to avoid overlapping subproblems
        if (memo[i][j] != -1) {
            return memo[i][j];
        }
        // state transition; store result into memo
        if (s1.charAt(i) == s2.charAt(j)) {
            memo[i][j] = dp(s1, i - 1, s2, j - 1);
        } else {
            memo[i][j] =  min(
                dp(s1, i, s2, j - 1) + 1,
                dp(s1, i - 1, s2, j) + 1,
                dp(s1, i - 1, s2, j - 1) + 1
            );
        }
        return memo[i][j];
    }

    int min(int a, int b, int c) {
        return Math.min(a, Math.min(b, c));
    }
}
```

### DP table solution

Now the DP-table solution. We need to define a `dp` array and then perform the state transition on it.

First clarify the meaning of `dp`. Since this problem has two states (indices `i` and `j`), `dp` is 2D — roughly:

![](https://labuladong.online/algo/images/editDistance/dp.jpg)

State transitions are the same as the recursive solution. `dp[..][0]` and `dp[0][..]` correspond to base cases. The meaning of `dp[i][j]` is similar to the previous `dp` function:





```java
int dp(String s1, int i, String s2, int j)
// returns the minimum edit distance of s1[0..i] and s2[0..j]

dp[i-1][j-1]
// stores the minimum edit distance of s1[0..i] and s2[0..j]
```



The recursive `dp` function's base case has `i, j == -1`, but array indices are at least 0, so the `dp` array shifts indices by one.

Since `dp` array and recursive `dp` function have the same meaning, we can reuse the previous logic. **The only difference: recursion is top-down (start from the original problem and decompose to base cases); DP table is bottom-up (start from base cases and build up to the original problem)**:

```java
class Solution {
    public int minDistance(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        // definition: minimum edit distance of s1[0..i] and s2[0..j] is dp[i+1][j+1]
        int[][] dp = new int[m + 1][n + 1];
        // base case 
        for (int i = 1; i <= m; i++)
            dp[i][0] = i;
        for (int j = 1; j <= n; j++)
            dp[0][j] = j;
        // solve bottom-up
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (s1.charAt(i-1) == s2.charAt(j-1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = min(
                        dp[i - 1][j] + 1,
                        dp[i][j - 1] + 1,
                        dp[i - 1][j - 1] + 1
                    );
                }
            }
        }
        // stores the minimum edit distance of the entire s1 and s2
        return dp[m][n];
    }

    int min(int a, int b, int c) {
        return Math.min(a, Math.min(b, c));
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/edit-distance/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Code visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>



## 4. Extension

Generally, two-string DP problems follow this article's approach — build a DP table. Why? Because it makes state transitions easy to find. E.g. the edit-distance DP table:

![](https://labuladong.online/algo/images/editDistance/4.jpg)

Another detail: since each `dp[i][j]` only depends on three nearby states, space can be compressed to $O(\min(M, N))$ where M, N are the string lengths. Not hard, but readability suffers. Try optimizing yourself.

You may also ask: **we only computed the minimum edit distance — what are the actual operations?** For my WeChat-article example, just the minimum distance isn't enough; we need to know how to actually edit.

Easy — modify the code slightly to add extra info to the dp array:

```java
// int[][] dp;
Node[][] dp;

class Node {
    int val;
    int choice;
    // 0 means do nothing
    // 1 means insert
    // 2 means delete
    // 3 means replace
}
```

`val` is the dp number from before; `choice` is the operation. While picking the best choice, also record the operation; then trace back from the result to recover the actual operations.

Our final answer is `dp[m][n]`. `val` stores the minimum edit distance; `choice` stores the last operation. If it's insert, we move left one step:

![](https://labuladong.online/algo/images/editDistance/5.jpg)

Repeat to walk back to `dp[0][0]`, forming a path. The operations along that path are the optimal edit plan.

![](https://labuladong.online/algo/images/editDistance/6.jpg)

By popular request, here's the implementation — try running it:

```java
int minDistance(String s1, String s2) {
    int m = s1.length(), n = s2.length();
    Node[][] dp = new Node[m + 1][n + 1];
    // base case
    for (int i = 0; i <= m; i++) {
        // turn s1 into s2 by deleting one character
        dp[i][0] = new Node(i, 2);
    }
    for (int j = 1; j <= n; j++) {
        // turn s1 into s2 by inserting one character
        dp[0][j] = new Node(j, 1);
    }
    // state-transition equation
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (s1.charAt(i-1) == s2.charAt(j-1)){
                // if the two characters are equal, do nothing
                Node node = dp[i - 1][j - 1];
                dp[i][j] = new Node(node.val, 0);
            } else {
                // otherwise, record the lowest-cost operation
                dp[i][j] = minNode(
                    dp[i - 1][j],
                    dp[i][j - 1],
                    dp[i-1][j-1]
                );
                // and increment the edit distance
                dp[i][j].val++;
            }
        }
    }
    // walk back through the dp table and print the operations
    printResult(dp, s1, s2);
    return dp[m][n].val;
}

// pick the lowest-cost operation among delete, insert, replace
Node minNode(Node a, Node b, Node c) {
    Node res = new Node(a.val, 2);
    
    if (res.val > b.val) {
        res.val = b.val;
        res.choice = 1;
    }
    if (res.val > c.val) {
        res.val = c.val;
        res.choice = 3;
    }
    return res;
}
```

Finally, `printResult` traces back the result and prints the operations:

```java
void printResult(Node[][] dp, String s1, String s2) {
    int rows = dp.length;
    int cols = dp[0].length;
    int i = rows - 1, j = cols - 1;
    System.out.println("Change s1=" + s1 + " to s2=" + s2 + ":\n");
    while (i != 0 && j != 0) {
        char c1 = s1.charAt(i - 1);
        char c2 = s2.charAt(j - 1);
        int choice = dp[i][j].choice;
        System.out.print("s1[" + (i - 1) + "]:");
        switch (choice) {
            case 0:
                // skip — both pointers advance
                System.out.println("skip '" + c1 + "'");
                i--; j--;
                break;
            case 1:
                // insert s2[j] into s1[i] — s2 pointer advances
                System.out.println("insert '" + c2 + "'");
                j--;
                break;
            case 2:
                // delete s1[i] — s1 pointer advances
                System.out.println("delete '" + c1 + "'");
                i--;
                break;
            case 3:
                // replace s1[i] with s2[j] — both pointers advance
                System.out.println(
                    "replace '" + c1 + "'" + " with '" + c2 + "'");
                i--; j--;
                break;
        }
    }
    // if s1 isn't done, the rest must all be deleted
    while (i > 0) {
        System.out.print("s1[" + (i - 1) + "]:");
        System.out.println("delete '" + s1.charAt(i - 1) + "'");
        i--;
    }
    // if s2 isn't done, the rest must all be inserted into s1
    while (j > 0) {
        System.out.print("s1[0]:");
        System.out.println("insert '" + s2.charAt(j - 1) + "'");
        j--;
    }
}
```



<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [DP template for subsequence problems](https://labuladong.online/algo/dynamic-programming/subsequence-problem/)
 - [Optimal substructure and dp-array traversal direction](https://labuladong.online/algo/dynamic-programming/faq-summary/)
 - [Classic DP: regular expression matching](https://labuladong.online/algo/dynamic-programming/regular-expression-matching/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems citing this article</strong></summary>

<strong>Install [my Chrome extension](https://labuladong.online/algo/intro/chrome/) and click any problem below to view its solution outline:</strong>

| LeetCode | 力扣 | Difficulty |
| :----: | :----: | :----: |
| [97. Interleaving String](https://leetcode.com/problems/interleaving-string/?show=1) | [97. 交错字符串](https://leetcode.cn/problems/interleaving-string/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**

