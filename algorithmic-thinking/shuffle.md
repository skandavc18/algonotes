# Shuffle Algorithm

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
| [384. Shuffle an Array](https://leetcode.com/problems/shuffle-an-array/) | [384. Shuffle an Array](https://leetcode.cn/problems/shuffle-an-array/) | 🟠

**-----------**

You probably know various sorting algorithms, but if asked to shuffle an array, are you confident? And even if you sketch one, how do you prove it's correct? Unlike sorting, the result of shuffling isn't unique — "random" can mean many things. How do you prove your shuffling is "really random"?

Two questions:

1. What does "really random" mean?

2. What algorithm makes shuffling truly random?

This is called a "random shuffle algorithm" or simply "shuffle".

This article has two parts. Part 1 explains the most common shuffle in detail; the algorithm has subtle pitfalls and several variants — all correct in subtle different ways — so we'll present a simple framework that guarantees a correct shuffle. Part 2 explains using the Monte Carlo method to verify the shuffle is truly random.

### 1. The Shuffle Algorithm

These shuffles work by random swaps. Pseudocode — there are 4 correct forms:

```java
// Returns a random integer in the closed interval [min, max]
int randInt(int min, int max);

// Form 1
void shuffle(int[] arr) {
    int n = arr.length();
    /******** Only these two lines differ ********/
    for (int i = 0 ; i < n; i++) {
        // Pick a random element from i to the end
        int rand = randInt(i, n - 1);
        /*************************/
        swap(arr[i], arr[rand]);
    }
}

// Form 2
    for (int i = 0 ; i < n - 1; i++)
        int rand = randInt(i, n - 1);

// Form 3
    for (int i = n - 1 ; i >= 0; i--)
        int rand = randInt(0, i);

// Form 4
    for (int i = n - 1 ; i > 0; i--)
        int rand = randInt(0, i);

```

**Correctness criterion: the algorithm must be capable of producing all n! outcomes**. This is intuitive: a length-n array has n! permutations. The algorithm must be able to reflect that.

Let's apply this to **Form 1**:

```java
// Suppose this is the input
int[] arr = {1,3,5,7,9};

void shuffle(int[] arr) {
    int n = arr.length(); // 5
    for (int i = 0 ; i < n; i++) {
        int rand = randInt(i, n - 1);
        swap(arr[i], arr[rand]);
    }
}
```

First iteration: `i = 0`, `rand` ∈ `[0, 4]` — 5 choices.

![](https://labuladong.online/algo/images/shuffle/1.png)

Second: `i = 1`, `rand` ∈ `[1, 4]` — 4 choices.

![](https://labuladong.online/algo/images/shuffle/2.png)

And so on, until `i = 4`, `rand` ∈ `[4, 4]` — 1 choice.

![](https://labuladong.online/algo/images/shuffle/3.png)

Total combinations: `n! = 5! = 5*4*3*2*1`, so this is correct.

**Form 2** is the same except the last iteration is omitted; the last meaningful iteration is `i = 3` with `rand` ∈ `[3, 4]` — 2 choices:

```java
// Form 2
// arr = {1,3,5,7,9}, n = 5
    for (int i = 0 ; i < n - 1; i++)
        int rand = randInt(i, n - 1);
```

So `5*4*3*2 = 5! = n!` (multiplying by 1 doesn't matter). Correct.

If you understood, you'll see **Form 3** is Form 1 in reverse, and **Form 4** is Form 2 in reverse. All correct.

A reader's instinct might produce the following — but **it's wrong**:

```java
void shuffle(int[] arr) {
    int n = arr.length();
    for (int i = 0 ; i < n; i++) {
        // Each iteration picks from [0, n-1]
        int rand = randInt(0, n - 1);
        swap(arr[i], arr[rand]);
    }
}
```

Why wrong? This produces `n^n` outcomes, not `n!`. And `n^n` isn't a multiple of `n!`.

For `arr = {1,2,3}`, the correct count is `3! = 6`, but this gives `3^3 = 27`. Since 27 isn't divisible by 6, some outcomes are favored — not "truly random".

We've intuitively argued correctness without rigorous math. For probabilities, we can use Monte Carlo to verify.

### 2. Verifying with Monte Carlo

**Correctness for shuffle: every outcome must be equally likely — sufficient randomness**.

Without rigorous proof, we can approximate using Monte Carlo.

You may recall a high-school problem: throw points randomly in a square containing an inscribed circle, count how many fall in the circle, and approximate π:

![](https://labuladong.online/algo/images/shuffle/4.png)

That's Monte Carlo: with enough points, the count approximates the area. From the area ratio you derive π. More points → more accurate.

Similarly, we can shuffle an array a million times, count the frequency of each outcome, and use frequencies as probabilities to verify the shuffle. The idea is simple, but implementations vary.

**Idea 1**: enumerate all permutations of `arr` as a histogram (say `arr = {1,2,3}`):

![](https://labuladong.online/algo/images/shuffle/5.jpg)

After each shuffle, increment the corresponding bin. Repeat 1M times; if all bins are roughly equal, the algorithm is correct. Pseudocode:

```java
void shuffle(int[] arr);

// Monte Carlo
int N = 1000000;
HashMap count; // histogram
for (i = 0; i < N; i++) {
    int[] arr = {1,2,3};
    shuffle(arr);
    // arr is now shuffled
    count[arr] += 1;
}
for (int feq : count.values()) 
    print(feq / N + " "); // frequencies
```

This works, but with `n!` permutations, large `n` blows up the space.

For verification, `n` doesn't need to be large — 5 or 6 suffices.

**Idea 2**: have `arr` be all zeros except one 1. Shuffle 1M times; record how often the 1 lands at each index. If counts are roughly equal, the shuffle is uniform.

```java
void shuffle(int[] arr);

// Monte Carlo
int N = 1000000;    
int[] arr = {1,0,0,0,0};
int[] count = new int[arr.length];
for (int i = 0; i < N; i++) {
    shuffle(arr); // shuffle arr
    for (int j = 0; j < arr.length; j++) 
        if (arr[j] == 1) {
            count[j]++;
            break;
        }
}
for (int feq : count) 
    print(feq / N + " "); // frequencies
```

![](https://labuladong.online/algo/images/shuffle/6.png)

This avoids factorial space at the cost of a nested loop. For our small test sets it doesn't matter.

A careful reader may notice the two ideas declare `arr` differently — inside or outside the loop. Either works since shuffling permutes; only the multiset of elements matters.

### 3. Wrap-Up

Part 1 covered the shuffle, proved 4 forms via a simple counting argument, and showed a common wrong version.

Part 2 gave the correctness criterion — uniform outcome probability — and used Monte Carlo to verify with a sledgehammer. Different ideas work; we just want a quick sanity check.





**＿＿＿＿＿＿＿＿＿＿＿＿＿**

**"labuladong's Algorithm Notes" has been published. Follow the official account for details; reply with "all-in-one bundle" to download the companion PDF and the full problem-solving bundle**:

![](https://labuladong.online/algo/images/souyisou2.png)

====== Code in Other Languages ======

[384. Shuffle an Array](https://leetcode-cn.com/problems/shuffle-an-array)

### javascript

```js
// Returns a random integer in the closed interval [min, max]
const randInt = function (minNum, maxNum) {
    return parseInt(Math.random() * (maxNum - minNum + 1) + minNum, 10);
};


// Form 1
let shuffle = function (arr) {
    const swap = (i, j) => {
        let t = arr[i];
        arr[i] = arr[j];
        arr[j] = t;
    }

    let n = arr.length;

    /******** Only these two lines differ ********/
    for (let i = 0; i < n; i++) {
        // Pick a random element from i to the end
        let rand = randInt(i, n - 1);
        /*************************/
        // Swap arr[i] and arr[rand]
        swap(i, rand);
    }
}

// Form 2
let shuffle = function (arr) {
    const swap = (i, j) => {
        let t = arr[i];
        arr[i] = arr[j];
        arr[j] = t;
    }

    let n = arr.length;


    /******** Only these two lines differ ********/
    for (let i = 0; i < n - 1; i++) {
        let rand = randInt(i, n - 1);
        /*************************/
        // Swap arr[i] and arr[rand]
        swap(i, rand);
    }
}

// Form 3
let shuffle = function (arr) {
    const swap = (i, j) => {
        let t = arr[i];
        arr[i] = arr[j];
        arr[j] = t;
    }

    let n = arr.length;


    /******** Only these two lines differ ********/
    for (let i = n - 1; i >= 0; i--) {
        let rand = randInt(0, i);

        /*************************/
        // Swap arr[i] and arr[rand]
        swap(i, rand);
    }
}

// Form 4
let shuffle = function (arr) {
    const swap = (i, j) => {
        let t = arr[i];
        arr[i] = arr[j];
        arr[j] = t;
    }

    let n = arr.length;


    /******** Only these two lines differ ********/
    for (let i = n - 1; i > 0; i--) {
        let rand = randInt(0, i);
        /*************************/
        // Swap arr[i] and arr[rand]
        swap(i, rand);
    }
}
```

