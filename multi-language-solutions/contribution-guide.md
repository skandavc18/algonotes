# Fixing errors in the labuladong problem-solving plugins

## Background

To help everyone learn algorithms better, I previously wrote many algorithm tutorials and developed a series of problem-solving plugins, collectively called the *labuladong problem-solving bundle*. See [here](https://labuladong.github.io/article/fname.html?fname=全家桶简介) for details.

The solutions in my tutorials and plugins are mostly written in Java, because Java is a fairly conventional language — even if you've never used it, the logic is relatively easy to follow. But now that ChatGPT has come along, I've used ChatGPT to rewrite my solutions in multiple languages, hoping to be friendlier to people from different technical backgrounds.

ChatGPT's rewrites are pretty good, but inevitably some errors slip through, so I'd like to work with everyone to fix them.

## How to report errors

If you find a solution that fails to pass all of LeetCode's test cases (this typically happens with the ChatGPT-rewritten solutions; my own solutions are only published after passing the tests), you can [click here](https://github.com/labuladong/fucking-algorithm/issues/new?assignees=&labels=code+bug&template=bug_report.yml&title=%5Bbug%5D%5B%7B%E8%BF%99%E9%87%8C%E6%9B%BF%E6%8D%A2%E4%B8%BA%E5%87%BA%E9%94%99%E7%9A%84%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80%7D%5D+%7B%E8%BF%99%E9%87%8C%E6%9B%BF%E6%8D%A2%E4%B8%BA%E5%87%BA%E9%94%99%E7%9A%84%E5%8A%9B%E6%89%A3%E9%A2%98%E7%9B%AE%E6%A0%87%E8%AF%86%E7%AC%A6%7D+) and submit an issue using the template. The other contributors and I will submit PRs to fix the errors.

## How to fix errors

First, thank you for being willing to fix solution code in my plugins. After you submit a PR to this repo and fix the errors, you'll become a contributor to this repo and appear in the contributors list on the home page. This repo has already gathered 115k stars, and your contributions will be seen by many.

Fixing the code is simple. All multi-language solutions are stored in [multi-language-solutions/solution_code.md](https://github.com/labuladong/fucking-algorithm/blob/master/%E5%A4%9A%E8%AF%AD%E8%A8%80%E8%A7%A3%E6%B3%95%E4%BB%A3%E7%A0%81/solution_code.md). You only need to modify that file. Its content is organized like this:


    Multi-language solutions for https://leetcode.cn/problems/xxx 👇

    ```cpp
    class Solution {
    public:
        int xxx() {
            // ...
        }
    };
    ```

    ```java
    class Solution {
        public int xxx() {
            // ...
        }
    }
    ```

    ```python
    class Solution:
        def xxx(self):
            # ...
    ```

    ```javascript
    var xxx = function() {
        // ...
    }
    ```

    ```go
    func xxx() {
        // ...
    }
    ```

    Multi-language solutions for https://leetcode.cn/problems/xxx 👆


For example, if you want to modify the JavaScript solution for [https://leetcode-cn.com/problems/longest-palindromic-substring/](https://leetcode-cn.com/problems/longest-palindromic-substring/), search for the keyword `longest-palindromic-substring` in [multi-language-solutions/solution_code.md](https://github.com/labuladong/fucking-algorithm/blob/master/%E5%A4%9A%E8%AF%AD%E8%A8%80%E8%A7%A3%E6%B3%95%E4%BB%A3%E7%A0%81/solution_code.md) to find the multi-language solutions for that problem, then modify the JavaScript code and submit a PR.

My plugins automatically pull the latest content of this file, so once your PR is merged into the `master` branch, the content shown by the plugins will be updated as well.

## PR requirements

1. Your PR must only modify the code section of [multi-language-solutions/solution_code.md](https://github.com/labuladong/fucking-algorithm/blob/master/%E5%A4%9A%E8%AF%AD%E8%A8%80%E8%A7%A3%E6%B3%95%E4%BB%A3%E7%A0%81/solution_code.md). Do not modify any other files or other content.

2. The purpose of translating my solutions into multiple languages is to help readers from different backgrounds understand the algorithmic thinking. Your modified code does not have to be the most efficient, but it should follow my original solution as closely as possible and include the full comments from my original solution.

3. Your PR description should include a screenshot showing the code passing all test cases. The PR title format is `[fix][{lang}] {slug}`, where `{lang}` is the language you fixed (e.g. `[fix][cpp]`), and `{slug}` is the identifier of the problem (the last part of the problem URL). For example, the slug for [https://leetcode.cn/problems/search-a-2d-matrix/](https://leetcode.cn/problems/search-a-2d-matrix/) is `search-a-2d-matrix`.

**You can refer to this PR as an example**: https://github.com/labuladong/fucking-algorithm/pull/1112