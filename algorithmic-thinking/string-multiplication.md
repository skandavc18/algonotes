# String Multiplication



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [43. Multiply Strings](https://leetcode.com/problems/multiply-strings/) | [43. Multiply Strings](https://leetcode.cn/problems/multiply-strings/) | 🟠 |

**-----------**



For small numbers, we can do arithmetic directly with the operators provided by a programming language, but if the two factors are very large, the language's data types may overflow. An alternative is to take the operands as strings and mimic the elementary-school multiplication procedure, returning the result as a string.

LeetCode 43 "Multiply Strings":

<Problem slug="multiply-strings" />

Note: `num1` and `num2` can be very long, so we cannot just convert them to integers and multiply. The only approach is to mimic hand multiplication.

When we compute `123 × 45` by hand, we'd do this:

![](https://labuladong.online/algo/images/string-multiply/1.jpg)

Compute `123 × 5`, then `123 × 4`, then add with one digit's offset. Any elementary-school student can do this fluently — but can you **mechanize this further** into a program for a (mindless) computer to execute?

This simple process involves carries in multiplication, addition with offsets, and carries in addition; plus less obvious issues — for instance, two-digit × two-digit can yield a three- or four-digit result. How do you devise a uniform handling? That's the charm of algorithms. Without computational thinking, even simple problems can be hard to automate.

First, our hand-computation is too "high-level" — let's go lower. We can break `123 × 5` and `123 × 4` down further, and sum at the end:

![](https://labuladong.online/algo/images/string-multiply/2.jpg)

`123` is small here, but for very large numbers we can't compute the product directly. We can use an array at the bottom to accumulate the partial sums:

![](https://labuladong.online/algo/images/string-multiply/3.jpg)

Roughly the process is: **two pointers `i, j` walk over `num1` and `num2`, compute the product, and add it into the correct position of `res`**, as the GIF below shows:

![](https://labuladong.online/algo/images/string-multiply/4.gif)

One key question remains: how do we add the product to the correct position of `res`? Or: how do we compute `res`'s index from `i, j`?

With careful observation: **the product `num1[i] * num2[j]` corresponds to `res[i+j]` and `res[i+j+1]`**.

![](https://labuladong.online/algo/images/string-multiply/6.jpg)

Knowing this, we can implement the procedure:

```java
class Solution {
    public String multiply(String num1, String num2) {
        int m = num1.length(), n = num2.length();
        // Result has at most m + n digits
        int[] res = new int[m + n];
        // Multiply digit by digit, starting from the units place
        for (int i = m - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                int mul = (num1.charAt(i) - '0') * (num2.charAt(j) - '0');
                // Indices in res for this product
                int p1 = i + j, p2 = i + j + 1;
                // Accumulate into res
                int sum = mul + res[p2];
                res[p2] = sum % 10;
                res[p1] += sum / 10;
            }
        }
        // Skip leading zeros (unused positions)
        int i = 0;
        while (i < res.length && res[i] == 0)
            i++;
        // Convert the result to a string
        StringBuilder str = new StringBuilder();
        for (; i < res.length; i++)
            str.append(res[i]);
        
        return str.length() == 0 ? "0" : str.toString();
    }
}
```

That's it for string multiplication.

**To summarize**: many things we take for granted are very hard for a computer. Our intuitive arithmetic flow isn't complex, but translating it into code is non-trivial. Algorithms simplify the procedure further, accumulating along the way.

We're often told not to fall into mental ruts — to be creative, not robotic. But I think "programmatic" thinking isn't bad: it boosts efficiency and reduces error rates. Algorithms are programmatic thinking; only that lets computers solve complex problems for us.

Maybe an algorithm is the **art of finding the right mental rut**. Hope this article helps.









**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)