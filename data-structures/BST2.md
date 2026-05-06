# Binary Search Tree Tactics (Basic Operations)



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [450. Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/) | [450. Delete Node in a BST](https://leetcode.cn/problems/delete-node-in-a-bst/) | 🟠 |
| [700. Search in a Binary Search Tree](https://leetcode.com/problems/search-in-a-binary-search-tree/) | [700. Search in a Binary Search Tree](https://leetcode.cn/problems/search-in-a-binary-search-tree/) | 🟢 |
| [701. Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree/) | [701. Insert into a Binary Search Tree](https://leetcode.cn/problems/insert-into-a-binary-search-tree/) | 🟠 |
| [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/) | [98. Validate Binary Search Tree](https://leetcode.cn/problems/validate-binary-search-tree/) | 🟠 |

**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Binary Tree Structure Basics](https://labuladong.online/algo/data-structure-basic/binary-tree-basic/)
> - [DFS/BFS Traversal of Binary Trees](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/)

In the earlier article [BST Tactics (Properties)](https://labuladong.online/algo/data-structure/bst-part1/), we covered BST's basic properties and used the "in-order traversal is sorted" property to solve a few problems. This article implements BST's basic operations: validity check, insert, delete, search. Delete and validity check are slightly tricky.

BST basic operations rely on the "left small, right large" property — searching is similar to binary search and very efficient. For example, the following is a valid BST:

![](https://labuladong.online/algo/images/bst/0.png)

For BST problems, you'll often see code patterns like:

```java
void BST(TreeNode root, int target) {
    if (root.val == target)
        // Found the target; do something
    if (root.val < target) 
        BST(root.right, target);
    if (root.val > target)
        BST(root.left, target);
}
```

This is essentially the binary-tree traversal framework, just leveraging the "left small, right large" property. Let's see how the basic BST operations are implemented.

## 1. Validate a BST

LeetCode 98 "Validate Binary Search Tree":

<Problem slug="validate-binary-search-tree" />

There's a pitfall here. By the BST property, doesn't each node only need to compare itself with its left and right children? Like:

```java
boolean isValidBST(TreeNode root) {
    if (root == null) return true;
    // root's left should be smaller
    if (root.left != null && root.left.val >= root.val)
        return false;
    // root's right should be larger
    if (root.right != null && root.right.val <= root.val)
        return false;

    return isValidBST(root.left)
        && isValidBST(root.right);
}
```

This is wrong. Each BST node must be smaller than **all** nodes in its right subtree. The following tree is not a BST because node 6 sits inside the right subtree of node 10, but our algorithm reports it as valid:

![](https://labuladong.online/algo/images/bst/假BST.png)

**The bug: for each `root`, the code only checks the immediate children, but BST requires the entire left subtree to be `< root.val` and the entire right subtree to be `> root.val`.**

So how do we propagate `root`'s constraint down to its left and right subtrees, when `root` only has direct access to its children? Correct code:

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return _isValidBST(root, null, null);
    }

    // Returns true iff every node in the subtree rooted at root satisfies max.val > root.val > min.val
    public boolean _isValidBST(TreeNode root, TreeNode min, TreeNode max) {
        // Base case
        if (root == null) return true;
        // If root.val violates min/max bounds, not a valid BST
        if (min != null && root.val <= min.val) return false;
        if (max != null && root.val >= max.val) return false;
        // By definition, the left subtree's max is root.val and the right subtree's min is root.val
        return _isValidBST(root.left, min, root) 
            && _isValidBST(root.right, root, max);
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/validate-binary-search-tree/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🎃 Animated Code Visualization 🎃</strong>
</summary>
</details>
</a>
<hr/>



We use a helper with extra parameters carrying additional information, propagating constraints to descendants. This is a small but useful binary-tree trick.

## Search for an Element in a BST

LeetCode 700 "Search in a Binary Search Tree" asks you to find the node with value `target` in a BST. Signature:

```java
TreeNode searchBST(TreeNode root, int target);
```

In a generic binary tree, we'd write:

```java
TreeNode searchBST(TreeNode root, int target) {
    if (root == null) return null;
    if (root.val == target) return root;
    // Otherwise recurse on both subtrees
    TreeNode left = searchBST(root.left, target);
    TreeNode right = searchBST(root.right, target);

    return left != null ? left : right;
}
```

This is correct but exhaustive — fine for any binary tree. How do we exploit BST's left-small/right-large property?

We don't need to recurse on both sides — like binary search, comparing `target` with `root.val` rules one side out. Slight tweak:

```java
TreeNode searchBST(TreeNode root, int target) {
    if (root == null) {
        return null;
    }
    // Search the left subtree
    if (root.val > target) {
        return searchBST(root.left, target);
    }
    // Search the right subtree
    if (root.val < target) {
        return searchBST(root.right, target);
    }
    // Current node is the target
    return root;
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/search-in-a-binary-search-tree/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>👾 Animated Code Visualization 👾</strong>
</summary>
</details>
</a>
<hr/>



## Insert a Number into a BST

Operating on a data structure boils down to traverse + access — traverse "finds", access "modifies". Concretely, inserting a number means finding the insertion position, then inserting.

BSTs typically don't have duplicate values, so we don't insert values that already exist. **The code below assumes no duplicates are inserted.**

The previous problem gave us the BST traversal framework — the "find" part. Apply the framework and add the "modify" step.

**Whenever modification is involved (similar to binary-tree construction), the function should return `TreeNode` and we should capture the return value of recursive calls.**

LeetCode 701 "Insert into a Binary Search Tree":

<Problem slug="insert-into-a-binary-search-tree" />

Solution code (read with the comments):

```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
            // Found the empty slot; create a new node
            return new TreeNode(val);
        }

        // Search the right subtree for insertion
        if (root.val < val) {
            root.right = insertIntoBST(root.right, val);
        }
        // Search the left subtree for insertion
        if (root.val > val) {
            root.left = insertIntoBST(root.left, val);
        }
        // Return root; the caller assigns it as a child
        return root;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/insert-into-a-binary-search-tree/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🌈 Animated Code Visualization 🌈</strong>
</summary>
</details>
</a>
<hr/>



## 3. Delete a Number from a BST

LeetCode 450 "Delete Node in a BST" asks you to delete the node with value `key`:

<Problem slug="delete-node-in-a-bst" />

Slightly more complex; like insert, "find then modify". Framework first:

```java
TreeNode deleteNode(TreeNode root, int key) {
    if (root.val == key) {
        // Found it; perform delete
    } else if (root.val > key) {
        // Search the left subtree
        root.left = deleteNode(root.left, key);
    } else if (root.val < key) {
        // Search the right subtree
        root.right = deleteNode(root.right, key);
    }
    return root;
}
```

Once the target node `A` is found, deleting it without breaking the BST property is the tricky part. Three cases (with figures):

**Case 1**: `A` is a leaf — both children null. It can simply be removed.

![](https://labuladong.online/algo/images/bst/bst_deletion_case_1.png)

```java
if (root.left == null && root.right == null)
    return null;
```



**Case 2**: `A` has exactly one non-null child. The child takes `A`'s place.

![](https://labuladong.online/algo/images/bst/bst_deletion_case_2.png)

```java
// After case 1 is excluded
if (root.left == null) return root.right;
if (root.right == null) return root.left;
```



**Case 3**: `A` has two children. To preserve BST, `A` must be replaced by either the largest node in its left subtree or the smallest in its right subtree. We use the latter.

![](https://labuladong.online/algo/images/bst/bst_deletion_case_3.png)

```java
if (root.left != null && root.right != null) {
    // Find the smallest node in the right subtree
    TreeNode minNode = getMin(root.right);
    // Replace root with minNode
    root.val = minNode.val;
    // Then delete minNode
    root.right = deleteNode(root.right, minNode.val);
}
```



All three cases handled, fill them into the framework and simplify:

```java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if (root == null) return null;
        if (root.val == key) {
            // These two ifs handle cases 1 and 2 correctly
            if (root.left == null) return root.right;
            if (root.right == null) return root.left;
            // Handle case 3
            // Get the min node of the right subtree
            TreeNode minNode = getMin(root.right);
            // Delete the min node from the right subtree
            root.right = deleteNode(root.right, minNode.val);
            // Replace root with minNode
            minNode.left = root.left;
            minNode.right = root.right;
            root = minNode;
        } else if (root.val > key) {
            root.left = deleteNode(root.left, key);
        } else if (root.val < key) {
            root.right = deleteNode(root.right, key);
        }
        return root;
    }

    TreeNode getMin(TreeNode node) {
        // The leftmost in the BST is the smallest
        while (node.left != null) node = node.left;
        return node;
    }
}
```


<hr/>
<a href="https://labuladong.online/algo-visualize/leetcode/delete-node-in-a-bst/" target="_blank">
<details style="max-width:90%;max-height:400px">
<summary>
<strong>🥳 Animated Code Visualization 🥳</strong>
</summary>
</details>
</a>
<hr/>



Done. In case 3, we swap `root` and `minNode` via a few pointer operations:

```java
// Handle case 3
// Get the min node of the right subtree
TreeNode minNode = getMin(root.right);
// Delete the min node from the right subtree
root.right = deleteNode(root.right, minNode.val);
// Replace root with minNode
minNode.left = root.left;
minNode.right = root.right;
root = minNode;
```



You may wonder why this is necessary — why not just modify `val`? Like:

```java
// Handle case 3
// Get the min node of the right subtree
TreeNode minNode = getMin(root.right);
// Delete the min node from the right subtree
root.right = deleteNode(root.right, minNode.val);
// Replace root with minNode
root.val = minNode.val;
```



That works for this problem, but it's not ideal — generally we don't swap nodes by mutating internal values. In real applications, the BST's data field can be a complex user-defined payload, and the BST as a data structure should be decoupled from that payload. Pointer manipulation is preferred and doesn't require knowledge of the payload.

Quick recap of the techniques:

1. If a current node imposes a constraint on its descendants, use a helper with extra parameters to carry the constraint down.

2. Master BST insert/delete/search/update.

3. When recursive calls modify the structure, capture the return value and return the modified node.

That's it. For more classic binary-tree problems and recursion practice, see [Recursion Practice](https://labuladong.online/algo/problem-set/bst1/) in the binary-tree chapter.







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Trie / Prefix Tree Implementation](https://labuladong.online/algo/data-structure/trie-implement/)
 - [[Practice] Classic BST Problems II](https://labuladong.online/algo/problem-set/bst2/)
 - [BST Tactics (Postorder)](https://labuladong.online/algo/data-structure/bst-part4/)
 - [BST Tactics (Construction)](https://labuladong.online/algo/data-structure/bst-part3/)

</details><hr>




<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| - | [Sword to Offer 33. Postorder Traversal of a BST](https://leetcode.cn/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/?show=1) | 🟠 |

</details>
<hr>



**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
