# Classic DP: game theory


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you'll not only learn the algorithmic pattern but also be able to solve:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [486. Predict the Winner](https://leetcode.com/problems/predict-the-winner/) | [486. Predict the Winner](https://leetcode.cn/problems/predict-the-winner/) | 🟠 |
| [877. Stone Game](https://leetcode.com/problems/stone-game/) | [877. Stone Game](https://leetcode.cn/problems/stone-game/) | 🟠 |

**-----------**


> [!NOTE]
> Before reading, you should first study:
> 
> - [Dynamic programming core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)

The earlier post [Brain teasers](https://labuladong.online/algo/frequency-interview/one-line-solutions/) discussed an interesting "stone game" — given the constraints, the first player always wins. But brain teasers are brain teasers. Real algorithm problems can't be cheesed by clever tricks. So this article uses the stone game to discuss how to use DP to solve "assuming both players are smart enough, who wins?" problems.

Game-theory problems share patterns. The discussion below references the approach in [this YouTube video](https://www.youtube.com/watch?v=WxpIHvsu1RI). The core idea: on top of 2D dp, use a tuple to store both players' game results. With this technique, when someone asks you about pirates dividing gold or two people picking coins, you can say: I'm too lazy to think — let me write an algorithm to compute it.


Let's generalize LeetCode 877 "Stone Game":

You and a friend face a row of stone piles, given as an array `piles`, where `piles[i]` is the number of stones in pile `i`. You take turns taking one whole pile each turn — only the leftmost or rightmost pile. After all stones are taken, whoever has more wins.

The number of piles can be any positive integer, and the total number of stones can be any positive integer too — breaking the "first player always wins" constraint. For example, with three piles `piles = [1, 100, 3]`, no matter whether the first player picks 1 or 3, the decisive 100 goes to the second player who wins.

**Assuming both players are smart**, write a `stoneGame` function that returns the difference between the first and second player's final scores (totals of stones). For the example above, the first player gets 4 and the second gets 100; the algorithm returns -96:

```java
int stoneGame(int[] nums);
```

Generalized like this, it becomes a fairly hard DP problem. LeetCode 486 "Predict the Winner" is a similar problem:

<Problem slug="predict-the-winner" />

Function signature:

```java
boolean predictTheWinner(int[] nums);
```

Given a `stoneGame` function that computes the score difference, this problem's solution falls right out:

```java
public boolean predictTheWinner(int[] nums) {
    // first player wins iff first's score >= second's
    return stoneGame(nums) >= 0;
}
```

How do we write `stoneGame`? The challenge of game theory: two players take turns choosing, and both are super smart — how do we represent that in code? It's actually not hard. As emphasized many times in the [DP core framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/), first define the `dp` array, then identify "states" and "choices", and everything else falls into place.

## 1. Defining the `dp` array

Defining the `dp` array meaningfully is a real skill. The same problem may admit multiple definitions; different definitions lead to different state-transition equations, but as long as the logic is sound, you'll arrive at the same answer.

I recommend not falling for clever-looking, super-short solutions. Aim for something steady, with the best explainability and most generalizable approach. This article gives a general framework for game-theory problems.

Before introducing the `dp` definition, here's what the final `dp` array looks like:

![](https://labuladong.online/algo/images/stone-game/1.png)

In what follows, treat the tuple as a class with `first` and `second` properties, abbreviated as `fir` and `sec` to save space. For example, with the figure above, `dp[1][3].fir = 11`, `dp[0][1].sec = 2`.


First answer a few likely reader questions:

The 2D dp table holds tuples — how do we represent that in code? Half the dp table is unused — how do we optimize? Easy — ignore those for now; first get the solution idea straight.

**Here is the `dp` array definition:**

`dp[i][j].fir = x` means: for piles `piles[i...j]`, the maximum score the first player can get is `x`.

`dp[i][j].sec = y` means: for piles `piles[i...j]`, the maximum score the second player can get is `y`.

Example: with `piles = [2, 8, 3, 5]` (0-indexed):

`dp[0][1].fir = 8` means: facing piles `[2, 8]`, the first player can get up to 8. `dp[1][3].sec = 5` means: facing piles `[8, 3, 5]`, the second player can get up to 5.

We want the difference between first and second players' final scores — by this definition, that's `dp[0][n-1].fir - dp[0][n-1].sec` — facing the whole `piles`, first's optimal score minus second's optimal score.


## 2. State-transition equation

Writing the state-transition equation is easy: find every "state" and the "choices" available at each state, then take the best.

Per the `dp` definition above, **there are clearly three states: starting index `i`, ending index `j`, and whose turn it is.**

```python
dp[i][j][fir or sec]
where:
0 <= i < piles.length
i <= j < piles.length
```

For each state, there are two **choices: take the leftmost pile or the rightmost.** We can enumerate every state:

```python
n = piles.length
for 0 <= i < n:
    for j <= i < n:
        for who in {fir, sec}:
            dp[i][j][who] = max(left, right)
```

The pseudocode above is roughly the DP framework. The challenge here: both players are smart and they alternate. How do we express that?

By our `dp` definition, this is easy. **State-transition equation**:


```python
dp[i][j].fir = max(piles[i] + dp[i+1][j].sec, piles[j] + dp[i][j-1].sec)
dp[i][j].fir = max(   pick the leftmost pile     ,   pick the rightmost pile  )
# Explanation: as the first player facing piles[i...j], I have two choices:

# Either pick the leftmost pile piles[i], the situation becomes piles[i+1...j],
# then it's the opponent's turn — I'm now the second player; my optimal score as second is dp[i+1][j].sec

# Or pick the rightmost pile piles[j], the situation becomes piles[i...j-1],
# then it's the opponent's turn — I'm now the second player; my optimal score as second is dp[i][j-1].sec

if first picks left:
    dp[i][j].sec = dp[i+1][j].fir
if first picks right:
    dp[i][j].sec = dp[i][j-1].fir
# Explanation: as the second player, I wait for the first player to choose. Two cases:

# If the first player picked the leftmost pile, I get piles[i+1...j].
# Now it's my turn — I become the first player; my optimal score is dp[i+1][j].fir

# If the first player picked the rightmost pile, I get piles[i...j-1].
# Now it's my turn — I become the first player; my optimal score is dp[i][j-1].fir
```


By the dp definition, we can also identify the **base case** — the simplest situation:


```python
dp[i][j].fir = piles[i]
dp[i][j].sec = 0
where 0 <= i == j < n
# Explanation: i == j means there's only one pile piles[i] in front
# Obviously, the first player gets piles[i]
# The second player has no piles to pick — score 0
```

![](https://labuladong.online/algo/images/stone-game/2.png)


One thing to note: the base case is on the diagonal, and computing `dp[i][j]` requires `dp[i+1][j]` and `dp[i][j-1]`:

![](https://labuladong.online/algo/images/stone-game/3.png)

By the principle in the earlier post [DP FAQ](https://labuladong.online/algo/dynamic-programming/faq-summary/) for choosing a `dp` traversal direction, we should iterate the `dp` array in reverse:

```java
for (int i = n - 2; i >= 0; i--) {
    for (int j = i + 1; j < n; j++) {
        dp[i][j] = ...
    }
}
```

![](https://labuladong.online/algo/images/stone-game/4.png)

## 3. Implementation

How do we represent the (fir, sec) tuple? In Python, use the built-in tuple. In C++, use `pair`. Or use a 3D array `dp[n][n][2]` with the last dimension acting as the tuple. Or write a Pair class:

```java
class Pair {
    int fir, sec;
    Pair(int fir, int sec) {
        this.fir = fir;
        this.sec = sec;
    }
}
```

Then translate the state-transition equation directly into code, remembering to iterate in reverse:

```java
// returns the score difference between first and second players at game's end
int stoneGame(int[] piles) {
    int n = piles.length;
    // initialize dp array
    Pair[][] dp = new Pair[n][n];
    for (int i = 0; i < n; i++) 
        for (int j = i; j < n; j++)
            dp[i][j] = new Pair(0, 0);
    // fill in the base case
    for (int i = 0; i < n; i++) {
        dp[i][i].fir = piles[i];
        dp[i][i].sec = 0;
    }

    // iterate in reverse
    for (int i = n - 2; i >= 0; i--) {
        for (int j = i + 1; j < n; j++) {
            // first player's score for picking left or right
            int left = piles[i] + dp[i+1][j].sec;
            int right = piles[j] + dp[i][j-1].sec;
            // apply the state-transition equation
            // first player will pick the larger result; second player follows
            if (left > right) {
                dp[i][j].fir = left;
                dp[i][j].sec = dp[i+1][j].fir;
            } else {
                dp[i][j].fir = right;
                dp[i][j].sec = dp[i][j-1].fir;
            }
        }
    }
    Pair res = dp[0][n-1];
    return res.fir - res.sec;
}
```

DP solutions, without a state-transition equation as a guide, are pure mystery. With the detailed explanation above, you should be able to clearly understand this large block of code.

Notice that computing `dp[i][j]` only depends on its left and bottom neighbors, so there's room for optimization — you could compress the 2D plane onto 1D. But 1D `dp` is more complex and harder to explain — don't waste time on that.

## 4. Final summary

This article gave a DP solution for game-theory problems. Game-theory problems are usually played between two smart players; the general way to encode such games is a 2D dp array whose entries are tuples representing both players' optimal decisions.

The reason for this design: after the first player makes a choice, they become the second player; after the second player makes a choice (responding to first's), they become the first player. **This role swap lets us reuse previous results — a classic DP signature**.

By now you should understand the algorithmic pattern for game-theory problems. When learning algorithms, focus on templates and frameworks, not flashy ideas. Don't try to write the optimal solution on the first attempt. Don't be stingy with extra space, don't optimize prematurely, and don't fear multidimensional arrays. The `dp` array is for storing info to avoid recomputation — use it freely until you're satisfied.


<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [Greedy: interval scheduling](https://labuladong.online/algo/frequency-interview/interval-scheduling/)

</details><hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

