# Common Bit Operations


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [136. Single Number](https://leetcode.com/problems/single-number/) | [136. Single Number](https://leetcode.cn/problems/single-number/) | 🟢 |
| [191. Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/) | [191. Number of 1 Bits](https://leetcode.cn/problems/number-of-1-bits/) | 🟢 |
| [231. Power of Two](https://leetcode.com/problems/power-of-two/) | [231. Power of Two](https://leetcode.cn/problems/power-of-two/) | 🟢 |
| [268. Missing Number](https://leetcode.com/problems/missing-number/) | [268. Missing Number](https://leetcode.cn/problems/missing-number/) | 🟢 |

**-----------**


Bit manipulation has many tricks. The site "Bit Twiddling Hacks" collects nearly every dark-magic trick:

http://graphics.stanford.edu/~seander/bithacks.html

Most are too obscure to study one by one — treat them as a reference. But the interesting and useful ones are worth knowing.

This article goes from light to heavy: a few fun (but useless) tricks first, then practical ones for problems and engineering.

## 1. A Few Fun Bit Tricks

```java
// 1. Use OR `|` with a space to lowercase an ASCII letter
('a' | ' ') = 'a'
('A' | ' ') = 'a'

// 2. Use AND `&` with an underscore to uppercase an ASCII letter
('b' & '_') = 'B'
('B' & '_') = 'B'

// 3. Use XOR `^` with a space to swap case
('d' ^ ' ') = 'D'
('D' ^ ' ') = 'd'

// These work because of ASCII encoding.
// ASCII characters are just numbers; space and underscore happen to flip case via bit ops.
// Curious readers can check the ASCII table.


// 4. Swap two numbers without a temporary
int a = 1, b = 2;
a ^= b;
b ^= a;
a ^= b;
// Now a = 2, b = 1


// 5. Increment
int n = 1;
n = -~n;
// Now n = 2


// 6. Decrement
int n = 2;
n = ~-n;
// Now n = 1


// 7. Whether two numbers have opposite signs
int x = -1, y = 2;
boolean f = ((x ^ y) < 0); // true

int x = 3, y = 2;
boolean f = ((x ^ y) < 0); // false
```


The first 6 are mostly toys; trick 7 is genuinely useful — it leverages the **two's complement** sign bit. The high bit is the sign (1 for negative, 0 otherwise); XOR yields the sign of the product's parity.

Without bit tricks you'd need an `if/else`, which is clunkier. Multiplying signs invites overflow.


## Using `index & (arr.length - 1)`

In [Monotonic Stack Patterns](https://labuladong.online/algo/data-structure/monotonic-stack/) I introduced circular arrays — modulo makes the array seem to wrap:

```java
int[] arr = {1,2,3,4};
int index = 0;
while (true) {
    // Loop in the circular array
    print(arr[index % arr.length]);
    index++;
}
// Output: 1,2,3,4,1,2,3,4,1,2,3,4...
```

But `%` is somewhat expensive. We can use `&`:

```java
int[] arr = {1,2,3,4};
int index = 0;
while (true) {
    print(arr[index & (arr.length - 1)]);
    index++;
}
// Output: 1,2,3,4,1,2,3,4,1,2,3,4...
```

> [!IMPORTANT]
> This trick only works when the array length is a power of 2 (2, 4, 8, 16, 32, ...). To round up to the next power of 2 see https://graphics.stanford.edu/~seander/bithacks.html#RoundUpPowerOf2

In short, `& (arr.length - 1)` substitutes for `% arr.length`, with better performance.

What if you decrement instead? With `%`, going negative requires special handling. With `&`, it just works:

```java
int[] arr = {1,2,3,4};
int index = 0;
while (true) {
    print(arr[index & (arr.length - 1)]);
    index--;
}
// Output: 1,4,3,2,1,4,3,2,1,4,3,2,1...
```

You may not write this yourself, but you'll see it in libraries — at least now you'll recognize it.

## Using `n & (n-1)`

**`n & (n-1)` clears the lowest set bit of `n`.**

A picture makes it clear:

![](https://labuladong.online/algo/images/bit-op/1.png)

The intuition: `n - 1` clears the lowest 1 and sets all bits below it to 1; ANDing with `n` keeps the higher bits and clears the lowest 1.

### Hamming Weight

LeetCode 191 "Number of 1 Bits":

<Problem slug="number-of-1-bits" />


Return the number of 1s in `n`'s binary representation. Loop, clearing one 1 at a time:

```java
int hammingWeight(int n) {
    int res = 0;
    while (n != 0) {
        n = n & (n - 1);
        res++;
    }
    return res;
}
```

### Power of Two

LeetCode 231 "Power of Two".

A power of 2 has exactly one 1 in binary:

```java
2^0 = 1 = 0b0001
2^1 = 2 = 0b0010
2^2 = 4 = 0b0100
```


With `n & (n-1)` (note operator precedence — keep the parentheses):

```java
boolean isPowerOfTwo(int n) {
    if (n <= 0) return false;
    return (n & (n - 1)) == 0;
}
```

## Using `a ^ a = 0`

Remember XOR's properties:

`a ^ a = 0`, and `a ^ 0 = a`.

### Single Element

LeetCode 136 "Single Number":

<Problem slug="single-number" />

XOR all numbers; pairs cancel to 0; the lone element remains:

```java
int singleNumber(int[] nums) {
    int res = 0;
    for (int n : nums) {
        res ^= n;
    }
    return res;
}
```

### Missing Number

LeetCode 268 "Missing Number":

<Problem slug="missing-number" />

A length-`n` array should hold indices in `[0, n)`, but it actually contains `n + 1` elements `[0, n]` minus one. Find the missing element.

Easy ideas: sort and scan; or use a HashSet.

Sorting: O(N log N). HashSet: O(N) time and space.

A simpler approach: arithmetic sum.

The numbers `0, 1, 2, ..., n` form an arithmetic series with one missing. Compute `sum(0, ..., n) - sum(nums)`:

```java
int missingNumber(int[] nums) {
    int n = nums.length;
    // Use long to avoid overflow, even though the constraints are small
    // (first + last) * count / 2
    long expect = (0 + n) * (n + 1) / 2;
    long sum = 0;
    for (int x : nums) {
        sum += x;
    }
    return (int)(expect - sum);
}
```

But this article is about bit tricks. XOR works too.

Recall: `a ^ a = 0`, `a ^ 0 = a`. And XOR is commutative/associative:

```java
2 ^ 3 ^ 2 = 3 ^ (2 ^ 2) = 3 ^ 0 = 3
```

For `nums = [0,3,1,4]`:

![](https://labuladong.online/algo/images/missing-elem/1.jpg)

Pad indices and pair each element with its equal index:

![](https://labuladong.online/algo/images/missing-elem/2.jpg)

Every pair cancels except the missing index — index 2 here, the answer.

**XOR all elements with all indices; pairs cancel; only the missing element remains:**

```java
class Solution {
    public int missingNumber(int[] nums) {
        int n = nums.length;
        int res = 0;
        // XOR with the extra index
        res ^= n;
        // XOR with every element and index
        for (int i = 0; i < n; i++)
            res ^= i ^ nums[i];
        return res;
    }
}
```

![](https://labuladong.online/algo/images/missing-elem/3.jpg)


XOR's commutativity/associativity cancels all pairs, leaving the missing element.

That's the common bit ops. Easy when you know them, baffling otherwise — no rote memorization required, just leave an impression.


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Hash Table Problems](https://labuladong.online/algo/problem-set/hash-table/)
 - [[Practice] Traversal Mindset I](https://labuladong.online/algo/problem-set/binary-tree-traverse-i/)
 - [Sweeping All Ugly-Number Problems](https://labuladong.online/algo/frequency-interview/ugly-number-summary/)
 - [Finding Missing and Duplicate Elements Together](https://labuladong.online/algo/frequency-interview/mismatch-set/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [1457. Pseudo-Palindromic Paths in a Binary Tree](https://leetcode.com/problems/pseudo-palindromic-paths-in-a-binary-tree/?show=1) | [1457. Pseudo-Palindromic Paths in a Binary Tree](https://leetcode.cn/problems/pseudo-palindromic-paths-in-a-binary-tree/?show=1) | 🟠 |
| [389. Find the Difference](https://leetcode.com/problems/find-the-difference/?show=1) | [389. Find the Difference](https://leetcode.cn/problems/find-the-difference/?show=1) | 🟢 |
| - | [Sword to Offer 15. Number of 1 Bits](https://leetcode.cn/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/?show=1) | 🟢 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
