# Efficiently Counting Primes



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [204. Count Primes](https://leetcode.com/problems/count-primes/) | [204. Count Primes](https://leetcode.cn/problems/count-primes/) | 🟠 |

**-----------**



Primes are simple to define: a number is prime if it's only divisible by 1 and itself.

The definition is easy, but few can write efficient prime-related algorithms.

LeetCode 204 "Count Primes":

```java
// Returns the number of primes in [2, n)
int countPrimes(int n)

// E.g., countPrimes(10) returns 4
// because 2,3,5,7 are primes
```

Most would write:

```java
int countPrimes(int n) {
    int count = 0;
    for (int i = 2; i < n; i++)
        if (isPrime(i)) count++;
    return count;
}

// Whether n is prime
boolean isPrime(int n) {
    for (int i = 2; i < n; i++)
        if (n % i == 0)
            // Has a non-trivial divisor
            return false;
    return true;
}
```

This is O(n^2) — way too slow. **Using `isPrime` as a helper isn't efficient; even if you use it, this version has redundant work.**

First, **how to test whether a number is prime efficiently** — tweak the for loop:

```java
boolean isPrime(int n) {
    for (int i = 2; i * i <= n; i++)
        ...
}
```

`i` doesn't need to go up to `n`; only up to `sqrt(n)`. Why? For `n = 12`:

```java
12 = 2 × 6
12 = 3 × 4
12 = sqrt(12) × sqrt(12)
12 = 4 × 3
12 = 6 × 2
```

The last two are the first two reversed; the turning point is at `sqrt(n)`.

So if no divisor is found in `[2, sqrt(n)]`, `n` is prime — nothing in `[sqrt(n), n]` will divide it either.

`isPrime` is now $O(\sqrt{N})$. **But for `countPrimes` we won't use this function** — the `sqrt(n)` insight is needed below.

## Efficient `countPrimes`

The "sieve method" goes back to ancient Greece's Eratosthenes — also the first to estimate the Earth's circumference via shadows; called the "father of geography".

The sieve idea is the inverse of the naive approach:

Start from 2. 2 is prime; 2×2=4, 3×2=6, 4×2=8, ... aren't prime.

3 is prime; 3×2=6, 3×3=9, 3×4=12, ... aren't prime either.

The Wikipedia GIF illustrates:

![](https://labuladong.online/algo/images/prime/1.gif)

First version:

```java
class Solution {
    public int countPrimes(int n) {
        boolean[] isPrime = new boolean[n];
        // Initialize all to true
        Arrays.fill(isPrime, true);

        for (int i = 2; i < n; i++) {
            if (isPrime[i]) {
                // Multiples of i can't be prime
                for (int j = 2 * i; j < n; j += i) {
                    isPrime[j] = false;
                }
            }
        }
        
        int count = 0;
        for (int i = 2; i < n; i++) {
            if (isPrime[i]) count++;
        }
        
        return count;
    }
}
```

If you follow this, you've got the gist. Two further optimizations.

First, by symmetry of factors, the outer loop only needs to go up to `sqrt(n)`:

```java
for (int i = 2; i * i < n; i++) 
    if (isPrime[i]) 
        ...
```

Second — less obvious — the inner loop has redundancy:

```java
for (int j = 2 * i; j < n; j += i) 
    isPrime[j] = false;
```

E.g., `n = 25`, `i = 5` marks 5×2=10, 5×3=15, etc., but those were already marked by `i = 2` (2×5) and `i = 3` (3×5).

Start `j` from `i * i`:

```java
for (int j = i * i; j < n; j += i) 
    isPrime[j] = false;
```

The efficient version — the Sieve of Eratosthenes:

```java
class Solution {
    public int countPrimes(int n) {
        boolean[] isPrime = new boolean[n];
        Arrays.fill(isPrime, true);
        for (int i = 2; i * i < n; i++) {
            if (isPrime[i]) {
                for (int j = i * i; j < n; j += i) {
                    isPrime[j] = false;
                }
            }
        }
        
        int count = 0;
        for (int i = 2; i < n; i++) {
            if (isPrime[i]) count++;
        }
        
        return count;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/count-primes/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Animated Code Visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>



**Time complexity is tricky to derive.** It depends on the nested loops; the operation count is:

  n/2 + n/3 + n/5 + n/7 + ...
= n × (1/2 + 1/3 + 1/5 + 1/7...)

The parenthesized terms are reciprocals of primes. The result is $O(N \log \log N)$. Curious readers can look up the proof.

That's everything for prime algorithms. Looks simple, but plenty of detail to polish.







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Classic Two-Pointer Linked List Problems](https://labuladong.online/algo/problem-set/linkedlist-two-pointers/)
 - [Sweeping All Ugly-Number Problems](https://labuladong.online/algo/frequency-interview/ugly-number-summary/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [264. Ugly Number II](https://leetcode.com/problems/ugly-number-ii/?show=1) | [264. Ugly Number II](https://leetcode.cn/problems/ugly-number-ii/?show=1) | 🟠 |
| - | [Sword to Offer 49. Ugly Number](https://leetcode.cn/problems/chou-shu-lcof/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)