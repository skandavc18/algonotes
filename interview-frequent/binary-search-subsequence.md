# Efficient Subsequence Test via Binary Search

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
| [392. Is Subsequence](https://leetcode.com/problems/is-subsequence/) | [392. Is Subsequence](https://leetcode.cn/problems/is-subsequence/) | 🟢
| [792. Number of Matching Subsequences](https://leetcode.com/problems/number-of-matching-subsequences/) | [792. Number of Matching Subsequences](https://leetcode.cn/problems/number-of-matching-subsequences/) | 🟠

**-----------**

Binary search isn't hard to understand; the trick is applying it cleverly.

You may not even associate a problem with binary search — for example, [Longest Increasing Subsequence](https://labuladong.online/algo/dynamic-programming/longest-increasing-subsequence/) derives a binary-search solution via a card-game analogy.

Today: another binary-search application — LeetCode 392 "Is Subsequence":

Determine whether `s` is a subsequence of `t` (assume `s` is short and `t` is very long).

Examples:

```
s = "abc", t = "**a**h**b**gd**c**", return true.

s = "axc", t = "ahbgdc", return false.
```

Easy to understand and seemingly easy — but it's hard to see the binary-search angle.

### 1. Analysis

A simple solution:

```java
boolean isSubsequence(String s, String t) {
    int i = 0, j = 0;
    while (i < s.length() && j < t.length()) {
        if (s.charAt(i) == t.charAt(j)) {
            i++;
        }
        j++;
    }
    return i == s.length();
}
```

<visual slug='is-subsequence'/>

Two pointers `i, j` over `s, t`; advance and match:

![](https://labuladong.online/algo/images/subsequence/1.gif)

You may say: this is optimal — O(N) where N is `t`'s length.

True for one query. **But there's a follow-up**:

Given many strings `s1, s2, ...` and one `t`, determine for each whether it's a subsequence of `t` (assume `s` is short and `t` is long).

```java
boolean[] isSubsequence(String[] sn, String t);
```

You may add a for loop over the strings — O(N) per `s`. With binary search we can do roughly O(M log N), where M is `s`'s length. Since N >> M, this is faster.

### 2. Binary-Search Idea

Preprocess `t`: build a dictionary `index` recording the indices of each character (in order):

```java
int m = s.length(), n = t.length();
ArrayList<Integer>[] index = new ArrayList[256];
// Record positions of each character in t
for (int i = 0; i < n; i++) {
    char c = t.charAt(i);
    if (index[c] == null) 
        index[c] = new ArrayList<>();
    index[c].add(i);
}
```

![](https://labuladong.online/algo/images/subsequence/2.jpg)

Suppose we've matched "ab" and want to match "c":

![](https://labuladong.online/algo/images/subsequence/1.jpg)

Previously we'd scan `j` linearly. With `index`, **binary-search `index[c]` for the smallest index larger than `j`** — for example, search in `[0, 2, 6]` for the smallest index > 4:

![](https://labuladong.online/algo/images/subsequence/3.jpg)

That gives us the next "c"'s index. How to find the smallest index larger than 4 via binary search? The left-bound binary search.

### 3. Revisiting Binary Search

In [Binary Search Detailed](https://labuladong.online/algo/essential-technique/binary-search-framework/) we covered three variants. The left-bound search has this property:

**When `val` is absent, the returned index is the smallest element greater than `val`.**

E.g., searching for 2 in `[0,1,3,4]` returns 2 (the index of 3) — 3 is the smallest greater than 2. We exploit this to skip linear scanning.

```java
// Left-bound binary search
int left_bound(ArrayList<Integer> arr, int target) {
    int left = 0, right = arr.size();
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (target > arr.get(mid)) {
            left = mid + 1;
        } else {
            right = mid;
        } 
    }
    if (left == arr.size()) {
        return -1;
    }
    return left;
}
```

Details in [Binary Search Detailed](https://labuladong.online/algo/essential-technique/binary-search-framework/).

For one `s`; for many, lift the preprocessing out:

```java
boolean isSubsequence(String s, String t) {
    int m = s.length(), n = t.length();
    // Preprocess t
    ArrayList<Integer>[] index = new ArrayList[256];
    for (int i = 0; i < n; i++) {
        char c = t.charAt(i);
        if (index[c] == null) 
            index[c] = new ArrayList<>();
        index[c].add(i);
    }
    
    // Pointer over t
    int j = 0;
    // Use index to look up s[i]
    for (int i = 0; i < m; i++) {
        char c = s.charAt(i);
        // Character c isn't in t at all
        if (index[c] == null) return false;
        int pos = left_bound(index[c], j);
        // Binary search didn't find c in the remaining range
        if (pos == -1) return false;
        // Advance j
        j = index[c].get(pos) + 1;
    }
    return true;
}
```

In action:

![](https://labuladong.online/algo/images/subsequence/2.gif)

Binary search greatly speeds things up.

With this idea, LeetCode 792 "Number of Matching Subsequences" — given `words` and `s`, count how many of `words` are subsequences of `s` — falls out:

```java
int numMatchingSubseq(String s, String[] words)
```

```java
int numMatchingSubseq(String s, String[] words) {
    // Preprocess s
    // char -> indices of that char
    ArrayList<Integer>[] index = new ArrayList[256];
    for (int i = 0; i < s.length(); i++) {
        char c = s.charAt(i);
        if (index[c] == null) {
            index[c] = new ArrayList<>();
        }
        index[c].add(i);
    }
    
    int res = 0;
    for (String word : words) {
        // Pointer over word
        int i = 0;
        // Pointer over s
        int j = 0;
        // Look up each char of word in index
        for (; i < word.length(); i++) {
            char c = word.charAt(i);
            // Character c isn't in s at all
            if (index[c] == null) {
                break;
            }
            int pos = left_bound(index[c], j);
            // Binary search didn't find c
            if (pos == -1) {
                break;
            }
            // Advance j
            j = index[c].get(pos) + 1;
        }
        // word fully matched → it's a subsequence
        if (i == word.length()) {
            res++;
        }
    }
    
    return res;
}

// Left-bound binary search
int left_bound(ArrayList<Integer> arr, int target) {
    // See above
}
```


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

**"labuladong's Algorithm Notes" has been published. Follow the official account for details; reply with "all-in-one bundle" to download the companion PDF and the full problem-solving bundle**:

![](https://labuladong.online/algo/images/souyisou2.png)

====== Code in Other Languages ====== 

[392. Is Subsequence](https://leetcode-cn.com/problems/is-subsequence)

### c++

[dekunma](https://www.linkedin.com/in/dekun-ma-036a9b198/) provided the C++ code.
**Solution 1: linear scan (or two pointers):**  

```C++
class Solution {
public:
    bool isSubsequence(string s, string t) {
        // Iterate over s
        for(int i = 0; i < s.size(); i++) {
            // Find s[i] in t
            size_t pos = t.find(s[i]);
            
            // s[i] isn't in t → false
            if(pos == std::string::npos) return false;
            // Otherwise look only at the remaining substring
            else t = t.substr(pos + 1);
        }
        return true;
    }
};
```

**Solution 2: binary search:**  
```C++
class Solution {
public:
    bool isSubsequence(string s, string t) {
        int m = s.size(), n = t.size();
        // Preprocess t
        vector<int> index[256];
        for (int i = 0; i < n; i++) {
            char c = t[i];
            index[c].push_back(i);
        }
        // Pointer over t
        int j = 0;
        // Find s[i] via index
        for (int i = 0; i < m; i++) {
            char c = s[i];
            // c isn't in t
            if (index[c].empty()) return false;
            int pos = left_bound(index[c], j);
            // No suitable c in the remaining range
            if (pos == index[c].size()) return false;
            // Advance j
            j = index[c][pos] + 1;
        }
        return true;
    }
    // Left-bound binary search
    int left_bound(vector<int> arr, int tar) {
        int lo = 0, hi = arr.size();
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (tar > arr[mid]) {
                lo = mid + 1;
            } else {
                hi = mid;
            } 
        }
        return lo;
    }
};
```


### javascript

Two-pointer linear scan:

```js
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
var isSubsequence = function (s, t) {
    let i = 0, j = 0;
    while (i < s.length && j < t.length) {
        if (s[i] === t[j]) i++;
        j++;
    }
    return i === s.length;
};
```


**Upgrade: binary-search version (handles many s's):**

```js
var isSubsequence = function (s, t) {
    let m = s.length, n = t.length;
    let index = new Array(256);
    // Record positions of each character in t
    for (let i = 0; i < n; i++) {
        let c = t[i];
        if (index[c] == null) {
            index[c] = [];
        }
        index[c].push(i)
    }

    // Pointer over t
    let j = 0;
    // Look up s[i] via index
    for (let i = 0; i < m; i++) {
        let c = s[i];
        // c isn't in t
        if (index[c] == null) return false
        let pos = left_bound(index[c], j);
        // No suitable c in the remaining range
        if (pos == index[c].length) return false;

        // Advance j
        j = index[c][pos] + 1;
    }
    return true;
};

var left_bound = function (arr, tar) {
    let lo = 0, hi = arr.length;
    while (lo < hi) {

        let mid = lo + Math.floor((hi - lo) / 2);
        if (tar > arr[mid]) {
            lo = mid + 1;
        } else {
            hi = mid;
        }
    }
    return lo;
}
```

