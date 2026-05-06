# "Cheap tricks" for algorithm tests



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**



**-----------**



First, a question: when grinding LeetCode, is it better to do it directly in the browser, or in a local IDE?

If it's a Niuke-style test where you have to handle your own input/output, you absolutely should write in an IDE — no contest. **But for the LeetCode-style judge, I personally prefer writing right in the browser**, for two reasons:

**1. Convenience**

LeetCode has some custom data structures, like `TreeNode` and `ListNode`. You'd have to copy these classes locally.

There's no testing in the IDE either — after writing code you still have to paste it into the website to run the tests. You may as well write in the browser.

Algorithm code is small in volume and IDE auto-completion barely matters.

**2. Practical for interviews**

Most live coding interviews expect you to solve problems directly in the browser, ideally talking through your thought process as you write.

If your daily practice is without IDE auto-completion — handwriting code that compiles in your head — then in the actual interview you'll write code faster and more confidently.

When I interviewed at Kuaishou, the interviewer asked me to [implement the LRU algorithm](https://labuladong.online/algo/data-structure/lru-cache/). I wrote out the doubly linked list and the hash linked list right in the browser, bug-free on the first try — I could see the surprise on the interviewer's face 😂

I was an offer-collector during fall recruiting in large part because handwriting algorithms in interviews exceeded interviewers' expectations — and that came from practicing in the browser.

Of course, if you really don't want to grind in the browser, you can use my VS Code or JetBrains plugins, which integrate seamlessly with my site's content.

Below I introduce a few practical "shortcuts" and debugging tips that can broadly increase your odds of passing tests.







## Skip what's hard, hit what's easy

As you know, most test problems require you to handle the input data yourself, then have your program print output. The judging mechanism redirects your program's output to a file via Linux's `>` and compares it against the correct answer.

So the "hard parts" of some problems are basically nominal — we can cut corners. Let's simplify: suppose a problem says "given a string of space-separated chars representing a singly linked list, reverse the list — and you must convert the input into a singly linked list before reversing!".

What do you do? Define a `ListNode` class for real, write code to convert input into a linked list, and reverse it with dizzying pointer ops?

Be clear: we're here to AC the problem, not to learn algorithmic thinking. The judge can't really inspect your algorithm logic — only your output. So a smart move is to dump input into an array, use a couple of [two-pointer technique](https://labuladong.online/algo/essential-technique/array-two-pointers-summary/) lines to reverse it, and print.

I've seen problems like this — they say the input is a singly linked list and you must reverse it in groups, and they specifically demand recursion as in [Reverse linked list in K-groups](https://labuladong.online/algo/data-structure/reverse-linked-list-recursion/). Well, with an array I can reverse it in two minutes — heh.

There's also our earlier problem [Flatten nested list iterator](https://labuladong.online/algo/data-structure/flatten-nested-list-iterator/), where the idea is clever — but on a test with input like `[1,[4,[6]]]` as a string, you can just use a regex to pull out the digits and you've got a flat list...







## Choosing a programming language

For algorithm problems specifically, I personally recommend using Java for tests. JetBrains' IntelliJ is just amazing — way better than other languages' editors. Not only does it have shortcuts like `psvm` and `sout` (if you don't know these, go reflect), it also catches lots of typos: `while` loops where you forgot to increment the variable, accidentally putting `return` inside a loop — that kind of careless mistake.

C++ is okay too, but I find it less convenient than Java. I recall C++ doesn't even have a string `split`. That alone makes me not want to use C++...

Also, C++ has tighter time limits — other languages get 4000ms, C++ only 2000ms. I think that's unfair. No wonder folks who use C++ for algorithms don't even use the standard `vector` — they go straight to raw `int[]` arrays for speed. Looks painful to me.

I haven't done much Python on LeetCode because I don't love dynamic languages — they're hard to debug. That said, Python offers many handy features. If you know your way around it, you can sometimes cheese problems. For example, the [calculator-evaluation algorithm](https://labuladong.online/algo/data-structure/implement-calculator/) is a Hard, but with Python's `exec` you can compute the answer instantly.

That's a huge advantage in tests, because as I said, we want results — no one cares how you got them.







## Code layering

Layering is a good habit that speeds up writing code and makes debugging easier.

In short: don't write everything inside `main`. My usual pattern is: `main` handles input, a `solution` function uniformly processes data and outputs the answer, and a function like `backtrack` handles the actual algorithmic logic.

Example: say a problem I decide to solve with memoized DP — code roughly:





```java
public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        // mainly handles input
        int N = scanner.nextInt();
        int[][] orders = new int[N][2];
        for (int i = 0; i < N; i++) {
            orders[i][0] = scanner.nextInt();
            orders[i][1] = scanner.nextInt();
        }
        // delegate solving to solution
        solution(orders);
    }

    static void solution(int[][] orders) {
        // handle some basic edge cases
        if (orders.length == 0) {
            System.out.println("None");
            return;
        }
        // delegate the actual algorithm to dp
        int res = dp(orders, 0);
        // handle output
        System.out.println(res);
    }

    // memo
    static HashMap<String, Integer> memo = new HashMap<>();
    static int dp(int[][] orders, int start) {
        // the actual algorithm
    }
}
```



See how clean that is — each function has its own job, and if anything goes wrong you can debug easily.

I'm not saying write code to industrial standards. Skip `private` if you want; pinyin variable names are fine. The key is just: don't dump everything into `main` — it gets messy, and if it works fine, great; if it doesn't, you'll spend a long time debugging, get lost, and panic.

## How to debug an algorithm

Bugs are unavoidable. Sometimes the whole approach is wrong; sometimes it's a detail like swapping `i` and `j`. How do you track those down?

For ordinary algorithm problems, tracking down bugs isn't hard — eyeballing the code usually finds them. If not, `print` some key variables and you'll usually spot the issue.

**The trickier ones are recursive algorithms**.

Without some experience, the recursive process is hard to fully picture mentally — let's focus on debugging recursive algorithms efficiently.

You might say: just paste the code into an IDE, set breakpoints, step through. Sure, that works. But as I've said in many earlier articles, a recursive function is best understood from a global perspective, not by jumping into specific details.

If you're not yet familiar with recursion and lack the global view, stepping with breakpoints can easily get you tangled up.

**My suggestion is: print key values inside the recursive function, with indentation, so you can intuitively observe the recursion**.

The most useful tool is indentation. Besides the solution function, define a function `printIndent` and a global variable `count`:





```java
// global variable, tracks the recursion depth
int count = 0;

// given n, prints n tabs of indentation
void printIndent(int n) {
    for (int i = 0; i < n; i++) {
        printf("   ");
    }
}
```



Now the trick:

**At the start of the recursive function, call `printIndent(count++)` and print the key variables; before every `return`, call `printIndent(--count)` and print the return value**.

Concrete example: in the previous article [DP in the Fallout 4 game](https://labuladong.online/algo/dynamic-programming/freedom-trail/), I wrote a recursive `dp` function roughly like this:





```java
int dp(String ring, int i, String key, int j) {
    // base case
    if (j == key.length()) {
        return 0;
    }
    
    // state transition
    for (int k : charToIndex.get(key.charAt(j))) {
        int subProblem = dp(ring, k, key, j + 1);
    }
    
    return res;
}
```



After debugging instrumentation, the recursive `dp` function becomes:





```java
int count = 0;
void printIndent(int n) {
    for (int i = 0; i < n; i++) {
        System.out.print("   ");
    }
}

int dp(String ring, int i, String key, int j) {
    // printIndent(count++);
    // printf("i = %d, j = %d\n", i, j);
    
    if (j == key.length()) {
        // printIndent(--count);
        // printf("return 0\n");
        return 0;
    }
    

    for (int k : charToIndex.get(key.charAt(j))) {
        int subProblem = dp(ring, k, key, j + 1);
    }
    
    // printIndent(--count);
    // printf("return %d\n", res);
    return res;
}
```



**Just add some print statements at the function start and at all `return` points.**

Uncomment them and run a test case — output looks like:

![](https://labuladong.online/algo/images/algo-debug-tech/1.jpg)

By comparing matching indents, you can see the key parameters `i, j` at each recursive call and the value returned by each.

**Most importantly, you can intuitively see the recursion process — notice this is essentially a recursion tree?**

![](https://labuladong.online/algo/images/algo-debug-tech/2.jpg)

The earlier article [DP problem-solving framework explained](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/) said the most important thing about understanding a recursive function is drawing the recursion tree. With this trick, you don't even have to draw it yourself — and you can clearly see the return values.

**This is one of the small tricks that boosts the "happiness" of grinding the most — more efficient than IDE breakpoints**.

I support this directly in my visualization panel — values printed inside recursive functions automatically gain indentation. See [Visualization panel usage](https://labuladong.online/algo/intro/visualize/) for details.







## Pre-test review strategy

Before a test, don't agonize over a single problem — it's not worth it.

Look at as many different problems as possible. Think for five minutes, and if you don't see the solution, just look at someone else's answer. Understand the idea — you don't even need to write it out yourself, that's a waste of time.

The scariest thing on a test is having no idea what to do, so skim every type of problem so your mind isn't blank. Once you have an idea, an average problem in 20–30 minutes isn't hard.

Like I said before, no problem is unsolvable by brute force. Hammer with [the backtracking framework](https://labuladong.online/algo/essential-technique/backtrack-framework/) — at worst add memoization and now it's [the DP framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/) — at worst skip the problem; passing 60% of cases by brute force is still pretty OK.

Not much else to add. Patterns sound simple — once you see them, you get them; the problem is, until you see them, you don't. I introduced a few test-taking algorithm tips here. Savor them~







<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [Weighted random selection](https://labuladong.online/algo/frequency-interview/random-pick-with-weight/)

</details><hr>





**＿＿＿＿＿＿＿＿＿＿＿＿＿**

