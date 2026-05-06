# Algorithms Are Like LEGO: Implementing LRU


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


After reading this article, you will not only master the algorithm pattern but also be able to solve the following problems:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [146. LRU Cache](https://leetcode.com/problems/lru-cache/) | [146. LRU Cache](https://leetcode.cn/problems/lru-cache/) | 🟠 |

**-----------**


> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Linked List Basics](https://labuladong.online/algo/data-structure-basic/linkedlist-basic/)
> - [Hash Map Basics](https://labuladong.online/algo/data-structure-basic/hashmap-basic/)

LRU is a cache eviction strategy. The idea is simple, but writing a bug-free implementation in interviews takes some skill — careful abstraction and decomposition. This article walks through it.

LRU's key data structure is a hash linked list — `LinkedHashMap`. The basics chapter [Implementing a Hash Linked List](https://labuladong.online/algo/data-structure-basic/hashtable-with-linked-list/) covers principles and code. If you haven't read it, that's fine — we'll explain the core ideas here.

Cache capacity is finite. When full, you must evict something to make room. Which? You want to keep useful data and evict useless data. What's "useful"?

LRU = "Least Recently Used": data used recently is "useful"; data not used for a long time is "useless"; when full, evict the latter.

Example: Android phones run apps in the background. You open Settings, then Phone Manager, then Calendar:

![](https://labuladong.online/algo/images/lru/1.jpg)

If you switch back to Settings, it gets bumped to the top:

![](https://labuladong.online/algo/images/lru/2.jpg)

Suppose you can only have 3 apps. Now you open Clock — must close one. Which? LRU says: close the bottom (least recently used) — Phone Manager — and put Clock at the top:

![](https://labuladong.online/algo/images/lru/3.jpg)

Now you understand LRU. Other strategies exist (e.g., LFU — least frequently used). I cover LFU in [LFU Detailed](https://labuladong.online/algo/frequency-interview/lfu/).


## 1. Problem Description

LeetCode 146 "LRU Cache":

Constructor takes `capacity`. APIs: `put(key, val)` and `get(key)` (returns -1 if absent).

Both must run in $O(1)$ time. Example:

```java
// Capacity 2
LRUCache cache = new LRUCache(2);
// Imagine cache as a queue
// Left = head, right = tail
// Most recently used at head, least at tail
// Pairs are (key, val)

cache.put(1, 1);
// cache = [(1, 1)]

cache.put(2, 2);
// cache = [(2, 2), (1, 1)]

// Returns 1
cache.get(1);
// cache = [(1, 1), (2, 2)]
// Key 1 is now MRU; bumped to head

cache.put(3, 3);
// cache = [(3, 3), (1, 1)]
// Cache full; evict the LRU (the tail)
// Insert the new pair at the head

// Returns -1 (not found)
cache.get(2);
// cache = [(3, 3), (1, 1)]

cache.put(1, 4);    
// cache = [(1, 4), (3, 3)]
// Key 1 already exists; overwrite to 4 and bump to head
```

## 2. Design

For O(1) `put` and `get`, we need:

1. Order — distinguish recent from old; evict the LRU on overflow.

2. Fast lookup of `key` to find its `val`.

3. On access, move the key to "most recently used"; the structure must support fast insert/delete at any position.

What structure satisfies all three? Hash maps are fast lookup but unordered; linked lists are ordered with fast insert/delete but slow lookup. Combine them → hash linked list (`LinkedHashMap`).


LRU's core data structure is a hash linked list — a doubly linked list paired with a hash map:

![](https://labuladong.online/algo/images/lru/4.jpg)

Address each requirement:

1. Always append to the tail; the tail is most recent, the head is least recent.

2. The hash map locates a node from its key in O(1).

3. Doubly linked lists support O(1) insert/delete given a node pointer.

**Why doubly linked? Why store `key` and `val` in the list, not just `val`?**

Wait until the implementation — it'll be clear.

## 3. Implementation

Many languages have built-in equivalents, but to internalize the algorithm, let's hand-roll it. Then we'll use Java's `LinkedHashMap`.

[Doubly linked list](https://labuladong.online/algo/data-structure-basic/linkedlist-basic/) node (int keys/values for simplicity):

```java
class Node {
    public int key, val;
    public Node next, prev;
    public Node(int k, int v) {
        this.key = k;
        this.val = v;
    }
}
```

The doubly linked list with the APIs we need:

```java
class DoubleList {  
    // Sentinel head/tail
    private Node head, tail;  
    // Number of elements
    private int size;
    
    public DoubleList() {
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head.next = tail;
        tail.prev = head;
        size = 0;
    }

    // Append node x at the tail — O(1)
    public void addLast(Node x) {
        x.prev = tail.prev;
        x.next = tail;
        tail.prev.next = x;
        tail.prev = x;
        size++;
    }

    // Remove node x (must exist) — O(1) since we have the node
    public void remove(Node x) {
        x.prev.next = x.next;
        x.next.prev = x.prev;
        size--;
    }
    
    // Remove and return the first (LRU) node — O(1)
    public Node removeFirst() {
        if (head.next == tail)
            return null;
        Node first = head.next;
        remove(first);
        return first;
    }

    public int size() { return size; }

}
```

If linked-list operations are unfamiliar, see [Implementing a Doubly Linked List](https://labuladong.online/algo/data-structure-basic/linkedlist-basic/).

Why doubly linked? We need fast deletion. To delete a node we need pointers to both its predecessor and successor; a doubly linked list provides the predecessor in O(1).

> [!IMPORTANT]
> Our doubly linked list only inserts at the tail — tail = MRU, head = LRU.

Combine with a hash map:

```java
class LRUCache {
    // key -> Node(key, val)
    private HashMap<Integer, Node> map;
    // Node(k1, v1) <-> Node(k2, v2)...
    private DoubleList cache;
    // Capacity
    private int cap;
    
    public LRUCache(int capacity) {
        this.cap = capacity;
        map = new HashMap<>();
        cache = new DoubleList();
    }
}
```

Maintaining both the list and the map by hand in `get`/`put` is error-prone (e.g., remove from list but forget to remove from map).

**Solution: layer an abstraction over both.**

Helper functions:

```java
class LRUCache {
    // Earlier code omitted...

    // Promote key to MRU
    private void makeRecently(int key) {
        Node x = map.get(key);
        // Remove from current position
        cache.remove(x);
        // Re-insert at the tail
        cache.addLast(x);
    }

    // Add a new (key, val) as MRU
    private void addRecently(int key, int val) {
        Node x = new Node(key, val);
        // Tail = MRU
        cache.addLast(x);
        // Don't forget the map mapping
        map.put(key, x);
    }

    // Delete a key
    private void deleteKey(int key) {
        Node x = map.get(key);
        // Remove from list
        cache.remove(x);
        // Remove from map
        map.remove(key);
    }

    // Remove the LRU
    private void removeLeastRecently() {
        // Head's first element is LRU
        Node deletedNode = cache.removeFirst();
        // Don't forget to remove from map
        int deletedKey = deletedNode.key;
        map.remove(deletedKey);
    }
}
```

This answers "Why store key and val in the list?". In `removeLeastRecently` we use the deleted node's `key` to remove from the map. Without `key` in the node, we couldn't.

`get`:

```java
class LRUCache {
    // Earlier code omitted...

    public int get(int key) {
        if (!map.containsKey(key)) {
            return -1;
        }
        // Promote key to MRU
        makeRecently(key);
        return map.get(key).val;
    }
}
```

`put` is slightly more complex:

![](https://labuladong.online/algo/images/lru/put.jpg)

```java
class LRUCache {
    // Earlier code omitted...
    
    public void put(int key, int val) {
        if (map.containsKey(key)) {
            // Update existing: delete + re-add
            deleteKey(key);
            addRecently(key, val);
            return;
        }
        
        if (cap == cache.size()) {
            // Evict LRU
            removeLeastRecently();
        }
        // Add as MRU
        addRecently(key, val);
    }
}
```

Full implementation:

```java
// Doubly linked list node
class Node {
    public int key, val;
    public Node next, prev;
    public Node(int k, int v) {
        this.key = k;
        this.val = v;
    }
}

// Doubly linked list
class DoubleList {  
    private Node head, tail;  
    private int size;
    
    public DoubleList() {
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head.next = tail;
        tail.prev = head;
        size = 0;
    }

    // Append at tail — O(1)
    public void addLast(Node x) {
        x.prev = tail.prev;
        x.next = tail;
        tail.prev.next = x;
        tail.prev = x;
        size++;
    }

    // Remove x — O(1)
    public void remove(Node x) {
        x.prev.next = x.next;
        x.next.prev = x.prev;
        size--;
    }
    
    // Remove and return first — O(1)
    public Node removeFirst() {
        if (head.next == tail)
            return null;
        Node first = head.next;
        remove(first);
        return first;
    }

    public int size() { return size; }

}


class LRUCache {
    // key -> Node(key, val)
    private HashMap<Integer, Node> map;
    // Node(k1, v1) <-> Node(k2, v2)...
    private DoubleList cache;
    private int cap;
    
    public LRUCache(int capacity) {
        this.cap = capacity;
        map = new HashMap<>();
        cache = new DoubleList();
    }
    
    public int get(int key) {
        if (!map.containsKey(key)) {
            return -1;
        }
        makeRecently(key);
        return map.get(key).val;
    }
    
    public void put(int key, int val) {
        if (map.containsKey(key)) {
            deleteKey(key);
            addRecently(key, val);
            return;
        }
        
        if (cap == cache.size()) {
            removeLeastRecently();
        }
        addRecently(key, val);
    }
    
    private void makeRecently(int key) {
        Node x = map.get(key);
        cache.remove(x);
        cache.addLast(x);
    }

    private void addRecently(int key, int val) {
        Node x = new Node(key, val);
        cache.addLast(x);
        map.put(key, x);
    }

    private void deleteKey(int key) {
        Node x = map.get(key);
        cache.remove(x);
        map.remove(key);
    }

    private void removeLeastRecently() {
        Node deletedNode = cache.removeFirst();
        int deletedKey = deletedNode.key;
        map.remove(deletedKey);
    }
}
```

Or use Java's built-in `LinkedHashMap` (or the `MyLinkedHashMap` from [Implementing a Hash Linked List](https://labuladong.online/algo/data-structure-basic/hashtable-with-linked-list/)):

```java
class LRUCache {
    int cap;
    LinkedHashMap<Integer, Integer> cache = new LinkedHashMap<>();
    public LRUCache(int capacity) { 
        this.cap = capacity;
    }
    
    public int get(int key) {
        if (!cache.containsKey(key)) {
            return -1;
        }
        // Promote key to MRU
        makeRecently(key);
        return cache.get(key);
    }
    
    public void put(int key, int val) {
        if (cache.containsKey(key)) {
            // Update existing
            cache.put(key, val);
            makeRecently(key);
            return;
        }
        
        if (cache.size() >= this.cap) {
            // The first key in iteration is the LRU
            int oldestKey = cache.keySet().iterator().next();
            cache.remove(oldestKey);
        }
        // Insert at the tail
        cache.put(key, val);
    }
    
    private void makeRecently(int key) {
        int val = cache.get(key);
        // Remove and re-insert to move to tail
        cache.remove(key);
        cache.put(key, val);
    }
}
```


That's all there is to LRU. For more design problems, see [Classic Data-Structure Design Problems](https://labuladong.online/algo/problem-set/ds-design/).


<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [Session and Cookie Explained](https://labuladong.online/algo/fname.html?fname=session-and-cookie)
 - [LFU Algorithm: LEGO-Style Implementation](https://labuladong.online/algo/frequency-interview/lfu/)
 - ["Trick" Patterns for Algorithm Quizzes](https://labuladong.online/algo/other-skills/tips-in-exam/)

</details><hr>


<hr>
<details class="hint-container details">
<summary><strong>Problems that reference this article</strong></summary>

<strong>Install [my Chrome problem-solving plugin](https://labuladong.online/algo/intro/chrome/) to view solutions directly from the problem pages:</strong>

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| - | [Sword to Offer II 031. LRU Cache](https://leetcode.cn/problems/OrIXps/?show=1) | 🟠 |

</details>
<hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
