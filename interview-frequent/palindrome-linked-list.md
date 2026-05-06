# Determining a Palindrome Linked List


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [234. Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/) | [234. Palindrome Linked List](https://leetcode.cn/problems/palindrome-linked-list/) | 🟢 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Linked List Basics](https://labuladong.online/algo/data-structure-basic/linkedlist-basic/)
> - [Linked List Two-Pointer Tricks](https://labuladong.online/algo/essential-technique/linked-list-skills-summary/)
> - [Array Two-Pointer Tricks Summary](https://labuladong.online/algo/essential-technique/array-two-pointers-summary/)

The earlier article [Array Two-Pointer Tricks Summary](https://labuladong.online/algo/essential-technique/array-two-pointers-summary/) covered palindromic strings/sequences — a quick recap.

**Finding** a palindrome: expand from the center:

```java
// Longest palindrome centered at s[left] and s[right]
String palindrome(String s, int left, int right) {
    // Bounds check
    while (left >= 0 && right < s.length()
            && s.charAt(left) == s.charAt(right)) {
        // Two pointers — expand
        left--;
        right++;
    }
    return s.substring(left + 1, right);
}
```

Palindromes can be odd or even in length — odd has one center, even has two — so the helper takes both `l` and `r`.

**Checking** whether a string is a palindrome is simpler — no parity concerns; just two pointers from the ends inward:

```java
boolean isPalindrome(String s) {
    int left = 0, right = s.length() - 1;
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

**Palindromes are symmetric — read left-to-right or right-to-left, same. That's the key.**

Now: how to check whether a singly linked list is a palindrome.

## 1. Singly Linked List Palindrome Check

LeetCode 234 "Palindrome Linked List":

<Problem slug="palindrome-linked-list" />

```java
boolean isPalindrome(ListNode head);
```

Trick: a singly linked list can't be traversed backward — no two-pointers from the ends.

Simplest approach: reverse the list into a new one and compare. (See [Recursive Linked List Reversal](https://labuladong.online/algo/data-structure/reverse-linked-list-recursion/).)

In [Framework Thinking for DS&A](https://labuladong.online/algo/essential-technique/algorithm-summary/) I said linked lists are recursive, and trees extend lists. **Linked lists also have preorder and postorder traversals — like binary trees, postorder lets us iterate the list in reverse without explicitly reversing**:

```java
// Binary-tree traversal
void traverse(TreeNode root) {
    // Preorder
    traverse(root.left);
    // Inorder
    traverse(root.right);
    // Postorder
}

// Recursive linked list traversal
void traverse(ListNode head) {
    // Preorder
    traverse(head.next);
    // Postorder
}
```

Why does this matter? Print values in order at preorder; print in reverse at postorder:

```java
// Print list values in reverse
void traverse(ListNode head) {
    if (head == null) return;
    traverse(head.next);
    // Postorder
    print(head.val);
}
```

We can mimic the two-pointer palindrome check:

```java
class Solution {
    // Pointer moving left -> right
    ListNode left;
    // Pointer moving right -> left
    ListNode right;

    // Track palindrome state
    boolean res = true;

    boolean isPalindrome(ListNode head) {
        left = head;
        traverse(head);
        return res;
    }

    void traverse(ListNode right) {
        if (right == null) {
            return;
        }

        // Recurse to the tail
        traverse(right.next);

        // Postorder: right is at the far end
        // Compare with left to check palindrome
        if (left.val != right.val) {
            res = false;
        }
        left = left.next;
    }
}
```

**The core logic: push nodes onto a stack, then pop them in reverse — using the recursion stack.**

<visual slug='is-palindrome' >

Click <code type="click">if (right === null)</code> repeatedly to see `right` walk to the tail via recursion; click <code type="click">left = left.next;</code> repeatedly to see `left` advance and `right` retreat to meet:

</visual>

Either approach is O(N) time and space. Can we use O(1) extra space?

## 2. Optimizing Space

Better approach:

**1. Find the midpoint with [linked list fast/slow pointers](https://labuladong.online/algo/essential-technique/linked-list-skills-summary/)**:

```java
ListNode slow, fast;
slow = fast = head;
while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
}
// slow now points to the middle
```

![](https://labuladong.online/algo/images/palindrome-list/1.jpg)


**2. If `fast` is non-null, the length is odd; advance `slow` once more**:

```java
if (fast != null)
    slow = slow.next;
```

![](https://labuladong.online/algo/images/palindrome-list/2.jpg)

**3. Reverse the latter half from `slow`; compare**:

```java
ListNode left = head;
ListNode right = reverse(slow);

while (right != null) {
    if (left.val != right.val)
        return false;
    left = left.next;
    right = right.next;
}
return true;
```

![](https://labuladong.online/algo/images/palindrome-list/3.jpg)


Putting it together — see [Reverse Linked List](https://labuladong.online/algo/data-structure/reverse-linked-list-recursion/) for `reverse`:

```java
class Solution {
    public boolean isPalindrome(ListNode head) {
        ListNode slow, fast;
        slow = fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        if (fast != null)
            slow = slow.next;
        
        ListNode left = head;
        ListNode right = reverse(slow);
        while (right != null) {
            if (left.val != right.val)
                return false;
            left = left.next;
            right = right.next;
        }
        
        return true;
    }

    ListNode reverse(ListNode head) {
        ListNode pre = null, cur = head;
        while (cur != null) {
            ListNode next = cur.next;
            cur.next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }
}
```

GIF:

![](https://labuladong.online/algo/images/kgroup/8.gif)

<visual slug='palindrome-linked-list'>

Click <code type="click">while (right != null)</code> repeatedly to see `left` and `right` move toward each other:

</visual>

O(N) time, O(1) space — optimal.

Some readers ask: this destroys the input. Can we restore it?

The key is to remember `p` and `q` (the boundaries):

![](https://labuladong.online/algo/images/palindrome-list/4.jpg)

Add one line before return:

```java
p.next = reverse(q);
```

Try it yourself.

## 3. Wrap-Up

Finding palindromes: expand from the center. Checking palindromes: contract from the ends. For singly linked lists, you can't traverse backward directly — reverse a copy, use postorder, or use a stack.

For palindrome linked-list checking specifically, the symmetry lets us reverse only the second half — O(1) extra space.


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| - | [Sword to Offer II 027. Palindrome Linked List](https://leetcode.cn/problems/aMhZSa/?show=1) | 🟢 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
