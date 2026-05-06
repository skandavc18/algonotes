# Extension: How to Implement a Calculator


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [224. Basic Calculator](https://leetcode.com/problems/basic-calculator/) | [224. Basic Calculator](https://leetcode.cn/problems/basic-calculator/) | 🔴 |
| [227. Basic Calculator II](https://leetcode.com/problems/basic-calculator-ii/) | [227. Basic Calculator II](https://leetcode.cn/problems/basic-calculator-ii/) | 🟠 |
| [772. Basic Calculator III](https://leetcode.com/problems/basic-calculator-iii/)🔒 | [772. Basic Calculator III](https://leetcode.cn/problems/basic-calculator-iii/)🔒 | 🔴 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Queue/Stack Principles](https://labuladong.online/algo/data-structure-basic/queue-stack-basic/)

The calculator we will eventually implement has the following features:

1. Input a string that may contain `+ - * /`, digits, parentheses, and spaces; the algorithm returns the computed result.

2. Obey arithmetic rules: parentheses have the highest priority; multiplication/division before addition/subtraction.

3. Division is integer division, truncating toward 0 (5/2 = 2, -5/2 = -2).

4. We may assume the input is valid and that no integer overflow or division by zero occurs during evaluation.

For example, given the following input, the algorithm returns 9:

```java
  3 * (2 - 6 / (3 - 7))
= 3 * (2 - 6 / (-4))
= 3 * (2 - (-1))
= 9
```

Looks very close to a real-life calculator. We have all used calculators, but if you think briefly about the algorithm, you may panic:

1. To handle parentheses you'd compute the innermost first and simplify outward. Easy to mess up by hand, let alone in code!

2. Multiplication/division before addition/subtraction is easy to teach a kid, but harder to teach a computer.

3. Spaces. We habitually put spaces between numbers and operators for readability — those need to be ignored during evaluation.

I recall many university data-structure textbooks use the calculator example when introducing stacks, but frankly the explanations are awful and may have driven future computer scientists away from such a simple data structure.

So this article walks through implementing a fully-featured calculator. **The key is to break the problem down step by step**: a few simple rules can handle very complex expressions. This thinking pattern should help you solve many complex problems.

Let's break it down, starting from the simplest sub-problem.

## 1. String to Integer

That's right, just this simple problem first: how do you convert a **positive** integer in string form to an `int`?

```java
String s = "458";

int n = 0;
for (int i = 0; i < s.length(); i++) {
    char c = s.charAt(i);
    n = 10 * n + (c - '0');
}
// n now equals 458
```

Standard pattern. But even this has a pitfall: **`(c - '0')` cannot drop the parentheses, or you risk integer overflow.**

Because `c` is an ASCII value, without parentheses you would add first and subtract later. Imagine `s` close to INT_MAX — overflow. Parentheses ensure subtract-then-add.

## 2. Handling Addition and Subtraction

Now: **if the input expression contains only `+` and `-` and has no spaces**, how do you compute the result? Take `1-12+3` as an example. A simple idea:

1. Prepend a default `+` to the first number, getting `+1-12+3`.

2. Pair each operator with the following number, giving three pairs `+1`, `-12`, `+3`. Convert them to numbers and push them onto a stack.

3. Sum all numbers in the stack.

Code, with a picture, makes it clear:

```java
int calculate(String s) {
    Stack<Integer> stk = new Stack<>();
    // The current number being read
    int num = 0;
    // Sign before num, initialized to +
    char sign = '+';
    for (int i = 0; i < s.length(); i++) {
        char c = s.charAt(i);
        // If digit, read into num
        if (Character.isDigit(c)) {
            num = 10 * num + (c - '0');
        }
        // If not a digit, we hit the next operator, or the end of the string
        // Then the previous number/sign pair must be pushed onto the stack
        if (c == '+' || c == '-' || i == s.length() - 1) {
            switch (sign) {
                case '+':
                    stk.push(num); break;
                case '-':
                    stk.push(-num); break;
            }
            // Update sign to current; reset num
            sign = c;
            num = 0;
        }
    }
    // The sum of everything on the stack is the answer
    int res = 0;
    while (!stk.isEmpty()) {
        res += stk.pop();
    }
    return res;
}
```

The `switch` part may be the only confusing piece. `i` scans left to right, with `sign` and `num` trailing. When `s[i]` reaches an operator:

![](https://labuladong.online/algo/images/calculator/1.jpg)

So we use `sign` to push `num` with the appropriate sign, then update `sign` and reset `num` to read the next operator/number pair.

Also note: not just operators trigger the push — when `i` reaches the end (`i == s.size() - 1`), we must also push the latest number to be summed.

![](https://labuladong.online/algo/images/calculator/2.jpg)

Done — an algorithm for compact addition/subtraction. Make sure you understand this; everything else builds on this framework.

## 3. Handling Multiplication and Division

The idea is the same. Take `2-3*4+5`. Decompose into pairs `+2`, `-3`, `*4`, `+5`. We didn't handle `*` and `/` before; just **add cases** in `switch`:

```java
int calculate(String s) {
    Stack<Integer> stk = new Stack<>();
    // Current number
    int num = 0;
    // Sign before num, initialized to +
    char sign = '+';
    for (int i = 0; i < s.length(); i++) {
        char c = s.charAt(i);
        if (Character.isDigit(c)) {
            num = 10 * num + (c - '0');
        }

        if (c == '+' || c == '-' || c == '/' || c == '*' || i == s.length() - 1) {
            int pre;
            switch (sign) {
                case '+':
                    stk.push(num); break;
                case '-':
                    stk.push(-num); break;
                // Pop the previous number and apply the operation
                case '*':
                    pre = stk.pop();
                    stk.push(pre * num);
                    break;
                case '/':
                    pre = stk.pop();
                    stk.push(pre / num);
                    break;
            }
            // Update sign; reset num
            sign = c;
            num = 0;
        }
    }
    // Sum the stack
    int res = 0;
    while (!stk.isEmpty()) {
        res += stk.pop();
    }
    return res;
}
```

![](https://labuladong.online/algo/images/calculator/3.jpg)


**The fact that multiplication/division come before addition/subtraction shows up as: `*`/`/` combine with the top of the stack, while `+`/`-` simply push themselves onto the stack.**

Now let's deal with possible spaces. With the current code we don't actually have to do anything special. Look at the `if`: when `c` is a space, neither branch matches and we skip it.

So the algorithm now correctly handles `+ - * /` and ignores spaces. What's left is parentheses.

## 4. Handling Parentheses

Parentheses look hardest, but really aren't. First, refactor:

```java
int calculate(String s) {
    return _calculate(s, 0, s.length() - 1);
}

// Returns the value of expression s[start..end]
int _calculate(String s, int start, int end) {
    Stack<Integer> stk = new Stack<>();
    int num = 0;
    char sign = '+';
    for (int i = start; i <= end; i++) {
        char c = s.charAt(i);
        if (Character.isDigit(c)) {
            num = 10 * num + (c - '0');
        }

        if (c == '+' || c == '-' || c == '/' || c == '*' || i == s.length() - 1) {
            int pre;
            switch (sign) {
                case '+':
                    stk.push(num);
                    break;
                case '-':
                    stk.push(-num);
                    break;
                // Pop the previous number and apply the operation
                case '*':
                    pre = stk.pop();
                    stk.push(pre * num);
                    break;
                case '/':
                    pre = stk.pop();
                    stk.push(pre / num);
                    break;
            }
            // Update sign; reset num
            sign = c;
            num = 0;
        }
    }
    int res = 0;
    while (!stk.isEmpty()) {
        res += stk.pop();
    }
    return res;
}
```

We define `_calculate` taking `s` and a range `[start, end]`, so we can compute any sub-expression of `s`.

Why aren't parentheses as hard as they look? **Because parentheses are recursive in nature.** Take `3*(4-5/2)-6`:

```java
calculate(3 * (4 - 5/2) - 6)
= 3 * calculate(4 - 5/2) - 6
= 3 * 2 - 6
= 0
```

No matter how deeply nested, recursive calls of `_calculate` evaluate the inside. **In other words, treat the parenthesized expression as a single number.**

The remaining question: when I see `(`, how do I find its matching `)`? With another stack — pre-process `s` to map each `(` to its matching `)`.

Code, building on the `_calculate` above:

```java
class Solution {
    public int calculate(String s) {
        // key: index of '(', value: index of matching ')'
        Map<Integer, Integer> rightIndex = new HashMap<>();
        // Use a stack to find matches
        Stack<Integer> stack = new Stack<>();
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                stack.push(i);
            } else if (s.charAt(i) == ')') {
                rightIndex.put(stack.pop(), i);
            }
        }
        return _calculate(s, 0, s.length() - 1, rightIndex);
    }

    // Returns the value of expression s[start..end]
    private int _calculate(String s, int start, int end, Map<Integer, Integer> rightIndex) {
        Stack<Integer> stk = new Stack<>();
        int num = 0;
        char sign = '+';
        for (int i = start; i <= end; i++) {
            char c = s.charAt(i);
            if (Character.isDigit(c)) {
                num = 10 * num + (c - '0');
            }
            if (c == '(') {
                // Recursively evaluate the parenthesized expression
                num = _calculate(s, i + 1, rightIndex.get(i) - 1, rightIndex);
                i = rightIndex.get(i);
            }
            if (c == '+' || c == '-' || c == '*' || c == '/' || i == end) {
                int pre;
                switch (sign) {
                    case '+':
                        stk.push(num);
                        break;
                    case '-':
                        stk.push(-num);
                        break;
                    // Pop the previous number and apply the operation
                    case '*':
                        pre = stk.pop();
                        stk.push(pre * num);
                        break;
                    case '/':
                        pre = stk.pop();
                        stk.push(pre / num);
                        break;
                }
                // Update sign; reset num
                sign = c;
                num = 0;
            }
        }
        int res = 0;
        while (!stk.isEmpty()) {
            res += stk.pop();
        }
        return res;
    }
}
```

![](https://labuladong.online/algo/images/calculator/4.jpg)

![](https://labuladong.online/algo/images/calculator/5.jpg)

![](https://labuladong.online/algo/images/calculator/6.jpg)


A couple of extra lines and parentheses are handled — that's the magic of recursion. The full calculator is done. By breaking the problem down step by step, the problem looks much less daunting.

## 5. Wrap-Up

This article uses the calculator problem to demonstrate a way of thinking about complex problems.

We started from the simple "string to integer" question, then handled add/sub-only expressions, then four-operator expressions, then spaces, then parentheses.

Hard problems aren't usually solved in one shot — they spiral outward. If you can't solve the original or even understand the answer at first, that's fine. The key is to simplify and tackle smaller problems.

Once you understand the algorithm, **save the resulting full-featured calculator code**. Other problems may ask you to evaluate an expression — you can drop this class in directly without rewriting it.


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - ["Trick" Patterns for Algorithm Quizzes](https://labuladong.online/algo/other-skills/tips-in-exam/)

</details><hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
