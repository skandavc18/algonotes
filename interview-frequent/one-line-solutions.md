# Algorithm Problems Solvable in One Line

<p align='center'>
<a href="https://github.com/labuladong/fucking-algorithm" target="view_window"><img alt="GitHub" src="https://img.shields.io/github/stars/labuladong/fucking-algorithm?label=Stars&style=flat-square&logo=GitHub"></a>
<a href="https://labuladong.online/algo/" target="_blank"><img class="my_header_icon" src="https://img.shields.io/static/v1?label=Premium%20Course&message=View&color=pink&style=flat"></a>
<a href="https://www.zhihu.com/people/labuladong"><img src="https://img.shields.io/badge/Zhihu-@labuladong-000000.svg?style=flat-square&logo=Zhihu"></a>
<a href="https://space.bilibili.com/14089380"><img src="https://img.shields.io/badge/Bilibili-@labuladong-000000.svg?style=flat-square&logo=Bilibili"></a>
</p>

![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: [the new website's membership](https://labuladong.online/algo/intro/site-vip/) is about to increase in price; renewals for existing users are supported. It's also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [292. Nim Game](https://leetcode.com/problems/nim-game/) | [292. Nim Game](https://leetcode.cn/problems/nim-game/) | 🟢
| [319. Bulb Switcher](https://leetcode.com/problems/bulb-switcher/) | [319. Bulb Switcher](https://leetcode.cn/problems/bulb-switcher/) | 🟠
| [877. Stone Game](https://leetcode.com/problems/stone-game/) | [877. Stone Game](https://leetcode.cn/problems/stone-game/) | 🟠

**-----------**

Three fun "brain teaser" problems collected while practicing — they can be solved by algorithms, but a bit of thought reveals patterns that yield the answer directly.

### 1. Nim Game

LeetCode 292 "Nim Game":

You and a friend face a pile of stones, taking turns. Each turn you can remove 1, 2, or 3 stones. Whoever takes the last stone wins.

Both players play optimally; you go first. Given `n`, return whether you win (true/false).

E.g., 4 stones: false. Whatever you take, opponent finishes — you lose.

This could be DP — there's overlapping subproblems. But two-player optimal play makes DP messy.

**Reverse the thinking**:

To win, on my turn there should be 1–3 stones left.

To force that, on my opponent's turn there should be 4 stones — whatever they take leaves 1–3 for me.

To force that, on my turn there should be 5–7 stones — I can leave them with 4.

To force 5–7 for me, the opponent should face 8 — whatever they take leaves 5–7 for me.

In general: stepping on a multiple of 4 is a trap — if you start on a multiple of 4, you lose.

```java
boolean canWinNim(int n) {
    // If we start on a multiple of 4, we lose
    // Otherwise we can keep the opponent on a multiple of 4 and win
    return n % 4 != 0;
}
```

### 2. Stone Game

LeetCode 877 "Stone Game":

Stone piles in a row, `piles[i]` stones in pile `i`. You take turns; each turn you take an entire pile from either end. After all piles are taken, whoever has more stones wins.

**Both play optimally; you go first.** Given `piles`, return whether you win.

The number of piles is even; the total is odd, so a draw is impossible.

E.g., `piles = [2, 1, 9, 5]`. You take 2 (left).

`[1, 9, 5]` — opponent takes 5 (right).

`[1, 9]` — you take 9.

`[1]` — opponent takes 1.

You: 2 + 9 = 11. Opponent: 5 + 1 = 6. You win → return true.

It's not "always take the bigger end". Why pick 2 first, not 5? Because 5's neighbor is 9 — if you take 5, you expose 9 and lose.

The "both optimal" condition lets the algorithm assume best decisions.

You could DP this. But thinking deeper:

```java
boolean stoneGame(int[] piles) {
    return true;
}
```

Why? Two key conditions: even number of piles; odd total. These seemingly fairness-promoting conditions actually rig the game in favor of the first player.

For `piles = [2, 1, 9, 5]` (indices 1..4):

Split into even-index piles and odd-index piles. Their sums differ (the total is odd).

As the first player, you can guarantee taking either all-even-index or all-odd-index piles.

You can pick pile 1 or 4 first. Want even-index? Pick pile 4; opponent must pick from {1, 3}; whatever they pick, pile 2 becomes available to you. Want odd-index? Pick pile 1; pile 3 becomes available to you.

So decide which group has more stones, and steer the game accordingly. You always win.

### 3. Bulb Switcher

LeetCode 319 "Bulb Switcher":

`n` bulbs, all off. Run `n` rounds:

Round 1: toggle every bulb (all on).

Round 2: toggle every 2nd bulb (2, 4, 6, ... off).

Round 3: toggle every 3rd bulb (3, 6, 9, ... — 3 off, 6 back on, etc.).

...

Round `n`: toggle the `n`-th bulb only.

Given `n`, how many bulbs are on at the end?

You could simulate, but there's a clever solution:

```java
int bulbSwitch(int n) {
    return (int)Math.sqrt(n);
}
```

Square root? Yes — neat once you see why.

A bulb ends up on iff toggled an odd number of times.

Take 6 bulbs; consider bulb 6. Rounds 1, 2, 3, 6 toggle it.

Why those? Because `6 = 1*6 = 2*3`. Divisors usually come in pairs → even toggles. Exception: perfect squares. For 16: `16 = 1*16 = 2*8 = 4*4` — divisor 4 is repeated, so 5 toggles (odd) → bulb 16 ends on.

So a bulb ends on iff its index is a perfect square. The count is `floor(sqrt(n))`.

For 16: sqrt(16) = 4 → 4 bulbs on (1, 4, 9, 16). For non-square `n`, the integer square root gives the count of perfect squares ≤ n.


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Sweeping All Ugly-Number Problems](https://labuladong.online/algo/frequency-interview/ugly-number-summary/)
 - [My Practice Insights: The Essence of Algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
 - [Classic DP: Game Theory](https://labuladong.online/algo/dynamic-programming/game-theory/)

</details><hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

**"labuladong's Algorithm Notes" has been published. Follow the official account for details; reply with "all-in-one bundle" to download the companion PDF and the full problem-solving bundle**:

![](https://labuladong.online/algo/images/souyisou2.png)

====== Code in Other Languages ======

[292. Nim Game](https://leetcode-cn.com/problems/nim-game)

[877. Stone Game](https://leetcode-cn.com/problems/stone-game)

[319. Bulb Switcher](https://leetcode-cn.com/problems/bulb-switcher)


### python

Provided by [JodyZ0203](https://github.com/JodyZ0203) — 292. Nim Game (Python3):

```Python
class Solution:
    def canWinNim(self, n: int) -> bool:
        # If divisible by 4, we lose
        # Otherwise we win
        return n % 4 != 0  
```

877. Stone Game (Python3):

```Python
class Solution:
    def stoneGame(self, piles: List[int]) -> bool:
        # With both playing optimally, the first player always wins
        # By observing whether even-indexed or odd-indexed piles have more stones in advance
        return True
```


319. Bulb Switcher (Python3):

```Python
class Solution:
    def bulbSwitch(self, n: int) -> int:
        # Floor of sqrt(n)
        return floor(sqrt (n))       
```


### c++

877. Stone Game (C++):

```cpp
class Solution {
public:
    bool stoneGame(vector<int>& piles) {
       // First player wins under optimal play
       return true;
    }
};
```


319. Bulb Switcher (C++):

```cpp
class Solution {
public:
    int bulbSwitch(int n) {
        // Floor of sqrt(n)
        return floor(sqrt (n));
    }
};
```


### javascript

[292. Nim Game](https://leetcode-cn.com/problems/nim-game)

```js
/**
 * @param {number} n
 * @return {boolean}
 */
var canWinNim = function(n) {
    // If we start on a multiple of 4, we lose
    // Otherwise we can keep the opponent on a multiple of 4 and win
    return n % 4 !== 0;
};
```

[877. Stone Game](https://leetcode-cn.com/problems/stone-game)

```js
var stoneGame = function(piles) {
    return true;
};
```

[319. Bulb Switcher](https://leetcode-cn.com/problems/bulb-switcher)

```js
/**
 * @param {number} n
 * @return {number}
 */
var bulbSwitch = function(n) {
    return Math.floor(Math.sqrt(n));
};
```

