[![Star History Chart](https://api.star-history.com/svg?repos=labuladong/fucking-algorithm&type=Date)](https://star-history.com/#labuladong/fucking-algorithm&Date)


English version is on [labuladong.online](https://labuladong.online/algo/en/) too. Just enjoy：)

# labuladong's Algorithm Notes

This repository contains over 60 original articles, all based on LeetCode problems, covering every kind of problem and technique. The goal is to **draw inferences from one example and stay easy to understand** — not just pile up code. The full table of contents is below.

Let me vent for a moment. **Grinding problems is about training your thinking, and the purpose of this repo is to convey that algorithmic way of thinking**. If I just wrote a repo containing LeetCode problem solutions, what good would it be? With no explanation of the thought process and no thinking framework — at most a time-complexity note that anyone can see at a glance.

Looking for an answer is easy: any problem's discussion section has all kinds of solutions, often a flashy "Python one-liner" with tons of upvotes. The question is: when you do an algorithm problem, are you learning programming-language tricks, or are you learning algorithmic thinking? Is your joy from copying someone else's one-liner that passes the tests and bumping your "completed" count by one, or from working out a solution yourself through logical reasoning and an algorithmic framework, without peeking at the answer?

People online sometimes attack me, saying what I write is too basic, or that you shouldn't learn algorithms with frameworks. I can only say: most of us are grinding LeetCode to land a job and put food on the table, not to compete in contests. I scraped my way through this myself; what we want is clear understanding and real takeaways, not mystification with no actionable point.

If you don't try to be approachable, are you supposed to put *Introduction to Algorithms* on a pedestal and scare everyone away in awe?

**Do anything enough times and you start spotting patterns. I've summarized algorithm patterns and frameworks here, and I believe they can help others avoid detours**. As a self-taught beginner, I spent a year grinding problems and writing summaries — my own little "algorithm cheat sheet". The table of contents is below; no more rambling here.

## Before you start

**1. Star this repo first to feed my vanity** — the article quality is absolutely worth a star. I'm still creating; give me a bit of motivation to keep writing. Thanks.

**2. Bookmark my online site. Each article opens with a link to the corresponding LeetCode problem, so you can read and grind at the same time. In total it walks you through 500 problems step by step**:

Latest 2024 link: https://labuladong.online/algo/

~~GitHub Pages: https://labuladong.online/algo/~~

~~Gitee Pages: https://labuladong.gitee.io/algo/~~

## Intro to the labuladong "problem-solving bundle"

### 1. Algorithm visualization panel

My algorithm site and all the companion plugins integrate an algorithm visualization tool that visualizes data structures and recursive processes, dramatically lowering the bar for understanding algorithms. Almost every problem's solution code has an accompanying visualization panel — see below for details.


### 2. Learning website

The content is, of course, the most important part of my algorithm tutorial series. All my tutorials are published at [labuladong.online](https://labuladong.online/algo/). I think you'll be spending plenty of study time there, not just adding it to bookmarks.

![](https://labuladong.github.io/pictures/简介/web_intro1.jpg)

### 3. Chrome extension

**Main features**: The Chrome extension lets you quickly view my "solutions" or "ideas" on Chinese LeetCode (力扣) or English LeetCode, and adds cross-references between problems and algorithm techniques. It works together with my site / public account / courses to give readers the smoothest grinding experience. Installation guide is in the table of contents below.

![](https://labuladong.github.io/pictures/简介/chrome_intro.jpg)


### 4. VS Code extension

**Main features**: Essentially the same as the Chrome extension. Readers who prefer grinding inside VS Code can use this plugin. Installation guide below.

![](https://labuladong.github.io/pictures/简介/vs_intro.jpg)


### 5. JetBrains plugin

**Main features**: Essentially the same as the Chrome extension. Readers who prefer grinding in JetBrains IDEs (PyCharm / IntelliJ / GoLand etc.) can use this plugin. Installation guide below.

![](https://labuladong.github.io/pictures/简介/jb_intro.jpg)


Wishing everyone happy learning, and free swimming through the sea of problems!


# Table of Contents

<!-- table start -->


* [About this site](https://labuladong.online/algo/home/)

* [Learning plans for beginners and crash-course learners](https://labuladong.online/algo/menu/plan/)
  * [Crash-course learning plan](https://labuladong.online/algo/intro/quick-learning-plan/)
  * [Full learning plan](https://labuladong.online/algo/intro/beginner-learning-plan/)
  * [Key points and pitfalls of grinding algorithms](https://labuladong.online/algo/intro/how-to-learn-algorithms/)
  * [How to practice / review the exercise chapters](https://labuladong.online/algo/intro/how-to-practice/)

* [Guide to the companion learning tools](https://labuladong.online/algo/menu/tools/)
  * [AI assistant for instant Q&A](https://labuladong.online/algo/intro/ai-assistant/)
  * [Algorithm visualization panel — usage](https://labuladong.online/algo/intro/visualize/)
  * [Algorithm games — how to play and overview](https://labuladong.online/algo/intro/game/)
  * [Companion Chrome extension](https://labuladong.online/algo/intro/chrome/)
  * [Companion VS Code / Cursor extension](https://labuladong.online/algo/intro/vscode/)
  * [Companion JetBrains plugin](https://labuladong.online/algo/intro/jetbrains/)
  * [Site paid membership](https://labuladong.online/algo/intro/site-vip/)

* [Getting started: programming-language basics and practice](https://labuladong.online/algo/menu/)
  * [Chapter intro](https://labuladong.online/algo/intro/programming-language-basic/)
  * [C++ language basics](https://labuladong.online/algo/programming-language-basic/cpp/)
  * [Java language basics](https://labuladong.online/algo/programming-language-basic/java/)
  * [Golang language basics](https://labuladong.online/algo/programming-language-basic/golang/)
  * [Python language basics](https://labuladong.online/algo/programming-language-basic/python/)
  * [JavaScript language basics](https://labuladong.online/algo/intro/js/)
  * [Things to know when solving problems on LeetCode](https://labuladong.online/algo/intro/leetcode/)
  * [Programming-language practice on LeetCode](https://labuladong.online/algo/programming-language-basic/lc-practice/)
  * [ACM-mode code template](https://labuladong.online/algo/intro/acm-mode/)

* [Foundations: data structures and sorting in depth](https://labuladong.online/algo/menu/quick-start/)
  * [Chapter intro](https://labuladong.online/algo/intro/data-structure-basic/)
  * [Intro to time and space complexity](https://labuladong.online/algo/intro/complexity-basic/)

  * [Implementing a dynamic array step by step](https://labuladong.online/algo/menu/dynamic-array/)
    * [Basic principles of arrays (sequential storage)](https://labuladong.online/algo/data-structure-basic/array-basic/)
    * [Dynamic-array implementation](https://labuladong.online/algo/data-structure-basic/array-implement/)

  * [Implementing singly / doubly linked lists step by step](https://labuladong.online/algo/menu/linked-list/)
    * [Basic principles of linked lists (linked storage)](https://labuladong.online/algo/data-structure-basic/linkedlist-basic/)
    * [Linked-list implementation](https://labuladong.online/algo/data-structure-basic/linkedlist-implement/)
    * [[Game] Implementing Snake](https://labuladong.online/algo/game/snake/)

  * [Variations on arrays and linked lists](https://labuladong.online/algo/menu/arr-linked/)
    * [Circular array — technique and implementation](https://labuladong.online/algo/data-structure-basic/cycle-array/)
    * [Skip list — core principles](https://labuladong.online/algo/data-structure-basic/skip-list-basic/)
    * [Bitmap — principles and implementation](https://labuladong.online/algo/data-structure-basic/bitmap/)

  * [Implementing queues / stacks step by step](https://labuladong.online/algo/menu/queue-stack/)
    * [Queue / stack basic principles](https://labuladong.online/algo/data-structure-basic/queue-stack-basic/)
    * [Implementing queue / stack with a linked list](https://labuladong.online/algo/data-structure-basic/linked-queue-stack/)
    * [Implementing queue / stack with an array](https://labuladong.online/algo/data-structure-basic/array-queue-stack/)
    * [Deque — principles and implementation](https://labuladong.online/algo/data-structure-basic/deque-implement/)

  * [Hash table — principles and implementation](https://labuladong.online/algo/menu/hash-table/)
    * [Hash table — core principles](https://labuladong.online/algo/data-structure-basic/hashmap-basic/)
    * [Implementing a hash table with chaining](https://labuladong.online/algo/data-structure-basic/hashtable-chaining/)
    * [Two pain points of linear probing](https://labuladong.online/algo/data-structure-basic/linear-probing-key-point/)
    * [Two implementations of linear probing](https://labuladong.online/algo/data-structure-basic/linear-probing-code/)
    * [Hash set — principles and implementation](https://labuladong.online/algo/data-structure-basic/hash-set/)

  * [Variations on hash-table structures](https://labuladong.online/algo/menu/hash-table-variation/)
    * [Augmenting a hash table with a linked list (LinkedHashMap)](https://labuladong.online/algo/data-structure-basic/hashtable-with-linked-list/)
    * [Augmenting a hash table with an array (ArrayHashMap)](https://labuladong.online/algo/data-structure-basic/hashtable-with-array/)
    * [Bloom filter — principles and implementation](https://labuladong.online/algo/data-structure-basic/bloom-filter/)

  * [Binary trees — structure and traversal](https://labuladong.online/algo/menu/binary-tree/)
    * [Binary tree basics and common types](https://labuladong.online/algo/data-structure-basic/binary-tree-basic/)
    * [Recursive / level-order traversal of binary trees](https://labuladong.online/algo/data-structure-basic/binary-tree-traverse-basic/)
    * [When to use DFS vs BFS](https://labuladong.online/algo/data-structure-basic/use-case-of-dfs-bfs/)
    * [Recursive / level-order traversal of N-ary trees](https://labuladong.online/algo/data-structure-basic/n-ary-tree-traverse-basic/)

  * [Variations on binary tree structures](https://labuladong.online/algo/menu/binary-tree/)
    * [BST — applications and visualization](https://labuladong.online/algo/data-structure-basic/tree-map-basic/)
    * [Red-black trees — perfect balance and visualization](https://labuladong.online/algo/data-structure-basic/rbtree-basic/)
    * [Trie / prefix tree — principles and visualization](https://labuladong.online/algo/data-structure-basic/trie-map-basic/)
    * [Binary heap — core principles and visualization](https://labuladong.online/algo/data-structure-basic/binary-heap-basic/)
    * [Binary heap / priority queue implementation](https://labuladong.online/algo/data-structure-basic/binary-heap-implement/)
    * [Segment tree — core principles and visualization](https://labuladong.online/algo/data-structure-basic/segment-tree-basic/)
    * [Data compression and Huffman tree](https://labuladong.online/algo/data-structure-basic/huffman-tree/)
    * [Updating in progress](https://labuladong.online/algo/intro/updating/)

  * [Graphs — basics and algorithm overview](https://labuladong.online/algo/menu/graph-theory/)
    * [Basic terminology of graph theory](https://labuladong.online/algo/data-structure-basic/graph-terminology/)
    * [Generic implementation of graphs](https://labuladong.online/algo/data-structure-basic/graph-basic/)
    * [DFS / BFS traversal of graphs](https://labuladong.online/algo/data-structure-basic/graph-traverse-basic/)
    * [Eulerian graphs and the one-stroke game](https://labuladong.online/algo/data-structure-basic/eulerian-graph/)
    * [Overview of shortest-path algorithms on graphs](https://labuladong.online/algo/data-structure-basic/graph-shortest-path/)
    * [Overview of minimum spanning tree algorithms](https://labuladong.online/algo/data-structure-basic/graph-minimum-spanning-tree/)
    * [Union-Find — principles](https://labuladong.online/algo/data-structure-basic/union-find-basic/)
    * [Updating in progress](https://labuladong.online/algo/intro/updating/)

  * [The top 10 sorting algorithms — principles and visualization](https://labuladong.online/algo/menu/sorting/)
    * [Chapter intro](https://labuladong.online/algo/intro/sorting/)
    * [Key metrics for sorting algorithms](https://labuladong.online/algo/data-structure-basic/sort-basic/)
    * [Issues with selection sort](https://labuladong.online/algo/data-structure-basic/select-sort/)
    * [Stability achieved: bubble sort](https://labuladong.online/algo/data-structure-basic/bubble-sort/)
    * [Reverse thinking: insertion sort](https://labuladong.online/algo/data-structure-basic/insertion-sort/)
    * [Breaking O(N^2): Shell sort](https://labuladong.online/algo/data-structure-basic/shell-sort/)
    * [Clever use of the binary-tree pre-order position: quicksort](https://labuladong.online/algo/data-structure-basic/quick-sort/)
    * [Clever use of the binary-tree post-order position: merge sort](https://labuladong.online/algo/data-structure-basic/merge-sort/)
    * [Use of the binary-heap structure: heap sort](https://labuladong.online/algo/data-structure-basic/heap-sort/)
    * [A brand-new sorting principle: counting sort](https://labuladong.online/algo/data-structure-basic/counting-sort/)
    * [Best of all worlds: bucket sort](https://labuladong.online/algo/data-structure-basic/bucket-sort/)
    * [Radix sort](https://labuladong.online/algo/data-structure-basic/radix-sort/)

  * [Updating in progress](https://labuladong.online/algo/intro/updating/)


* [Chapter 0 — Core problem-solving frameworks summary](https://labuladong.online/algo/menu/core/)
  * [Chapter intro](https://labuladong.online/algo/intro/core-intro/)
  * [The framework mindset for learning data structures and algorithms](https://labuladong.online/algo/essential-technique/algorithm-summary/)
  * [Two-pointer technique demolishes seven linked-list problems](https://labuladong.online/algo/essential-technique/linked-list-skills-summary/)
  * [Two-pointer technique demolishes seven array problems](https://labuladong.online/algo/essential-technique/array-two-pointers-summary/)
  * [Sliding-window algorithm — core code template](https://labuladong.online/algo/essential-technique/sliding-window-framework/)
  * [Core outline of the binary-tree algorithms series](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
  * [One perspective + two thinking modes to master recursion](https://labuladong.online/algo/essential-technique/understand-recursion/)
  * [Dynamic programming problem-solving framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)
  * [Backtracking problem-solving framework](https://labuladong.online/algo/essential-technique/backtrack-framework/)
  * [BFS problem-solving framework](https://labuladong.online/algo/essential-technique/bfs-framework/)
  * [Backtracking demolishes all permutation / combination / subset problems](https://labuladong.online/algo/essential-technique/permutation-combination-subset-all-in-one/)
  * [Greedy algorithm problem-solving framework](https://labuladong.online/algo/essential-technique/greedy/)
  * [Divide-and-conquer problem-solving framework](https://labuladong.online/algo/essential-technique/divide-and-conquer/)
  * [A practical guide to time/space-complexity analysis](https://labuladong.online/algo/essential-technique/complexity-analysis/)


* [Chapter 1 — Classic data-structure algorithms](https://labuladong.online/algo/menu/ds/)
  * [Linked-list algorithms — step by step](https://labuladong.online/algo/menu/linked-list/)
    * [Two-pointer technique demolishes seven linked-list problems](https://labuladong.online/algo/essential-technique/linked-list-skills-summary/)
    * [Classic two-pointer linked-list problems](https://labuladong.online/algo/problem-set/linkedlist-two-pointers/)
    * [A summary of fancy ways to reverse a singly linked list](https://labuladong.online/algo/data-structure/reverse-linked-list-recursion/)
    * [How to determine whether a linked list is a palindrome](https://labuladong.online/algo/data-structure/palindrome-linked-list/)

  * [Array algorithms — step by step](https://labuladong.online/algo/menu/array/)
    * [Two-pointer technique demolishes seven array problems](https://labuladong.online/algo/essential-technique/array-two-pointers-summary/)
    * [[Game] Match-three game](https://labuladong.online/algo/game/match-three/)
    * [Fancy traversal techniques for 2D arrays](https://labuladong.online/algo/practice-in-action/2d-array-traversal-summary/)
    * [Classic two-pointer array problems](https://labuladong.online/algo/problem-set/array-two-pointers/)
    * [[Game] Game of Life](https://labuladong.online/algo/game/life-game/)
    * [One method demolishes the nSum problem family](https://labuladong.online/algo/practice-in-action/nsum/)
    * [A small but elegant trick: prefix-sum array](https://labuladong.online/algo/data-structure/prefix-sum/)
    * [Classic prefix-sum problems](https://labuladong.online/algo/problem-set/perfix-sum/)
    * [A small but elegant trick: difference array](https://labuladong.online/algo/data-structure/diff-array/)
    * [Sliding-window algorithm — core code template](https://labuladong.online/algo/essential-technique/sliding-window-framework/)
    * [Classic sliding-window problems](https://labuladong.online/algo/problem-set/sliding-window/)
    * [Sliding-window extension: Rabin-Karp string matching](https://labuladong.online/algo/practice-in-action/rabinkarp/)
    * [Binary search — core code template](https://labuladong.online/algo/essential-technique/binary-search-framework/)
    * [Binary search — left-closed, right-open form](https://labuladong.online/algo/essential-technique/binary-search-left-open/)
    * [Mental framework for applying binary search in practice](https://labuladong.online/algo/frequency-interview/binary-search-in-action/)
    * [Classic binary-search problems](https://labuladong.online/algo/problem-set/binary-search/)
    * [Weighted random selection algorithm](https://labuladong.online/algo/frequency-interview/random-pick-with-weight/)
    * [The algorithmic decision behind "Tian Ji's horse race"](https://labuladong.online/algo/practice-in-action/advantage-shuffle/)


  * [Classic queue / stack algorithms](https://labuladong.online/algo/menu/queue-stack/)
    * [Implementing a stack with queues, and a queue with stacks](https://labuladong.online/algo/data-structure/stack-queue/)
    * [Classic stack problems](https://labuladong.online/algo/problem-set/stack/)
    * [Summary of parentheses-style problems](https://labuladong.online/algo/problem-set/parentheses/)
    * [Classic queue problems](https://labuladong.online/algo/problem-set/queue/)
    * [Monotonic-stack template — three example problems](https://labuladong.online/algo/data-structure/monotonic-stack/)
    * [Variants of monotonic stack and classic problems](https://labuladong.online/algo/problem-set/monotonic-stack/)
    * [Monotonic queue solves the sliding-window problem](https://labuladong.online/algo/data-structure/monotonic-queue/)
    * [Generic monotonic-queue implementation and classic problems](https://labuladong.online/algo/problem-set/monotonic-queue/)

  * [Binary-tree algorithms — step by step](https://labuladong.online/algo/menu/binary-tree/)
    * [Core outline of the binary-tree algorithms series](https://labuladong.online/algo/essential-technique/binary-tree-summary/)
    * [Binary-tree mind-set (thinking)](https://labuladong.online/algo/data-structure/binary-tree-part1/)
    * [Binary-tree mind-set (construction)](https://labuladong.online/algo/data-structure/binary-tree-part2/)
    * [Binary-tree mind-set (post-order)](https://labuladong.online/algo/data-structure/binary-tree-part3/)
    * [Binary-tree mind-set (serialization)](https://labuladong.online/algo/data-structure/serialize-and-deserialize-binary-tree/)
    * [BST mind-set (properties)](https://labuladong.online/algo/data-structure/bst-part1/)
    * [BST mind-set (basic ops)](https://labuladong.online/algo/data-structure/bst-part2/)
    * [BST mind-set (construction)](https://labuladong.online/algo/data-structure/bst-part3/)
    * [BST mind-set (post-order)](https://labuladong.online/algo/data-structure/bst-part4/)

  * [Binary-tree problem set](https://labuladong.online/algo/menu/100-bt/)
    * [Chapter intro](https://labuladong.online/algo/intro/binary-tree-practice/)
    * [Solving with the "traversal" mindset I](https://labuladong.online/algo/problem-set/binary-tree-traverse-i/)
    * [Solving with the "traversal" mindset II](https://labuladong.online/algo/problem-set/binary-tree-traverse-ii/)
    * [Solving with the "traversal" mindset III](https://labuladong.online/algo/problem-set/binary-tree-traverse-iii/)
    * [Solving with the "decompose the problem" mindset I](https://labuladong.online/algo/problem-set/binary-tree-divide-i/)
    * [Solving with the "decompose the problem" mindset II](https://labuladong.online/algo/problem-set/binary-tree-divide-ii/)
    * [Solving with both mindsets at once](https://labuladong.online/algo/problem-set/binary-tree-combine-two-view/)
    * [Solving via the post-order position I](https://labuladong.online/algo/problem-set/binary-tree-post-order-i/)
    * [Solving via the post-order position II](https://labuladong.online/algo/problem-set/binary-tree-post-order-ii/)
    * [Solving via the post-order position III](https://labuladong.online/algo/problem-set/binary-tree-post-order-iii/)
    * [Solving with level-order traversal I](https://labuladong.online/algo/problem-set/binary-tree-level-i/)
    * [Solving with level-order traversal II](https://labuladong.online/algo/problem-set/binary-tree-level-ii/)
    * [Classic BST problems I](https://labuladong.online/algo/problem-set/bst1/)
    * [Classic BST problems II](https://labuladong.online/algo/problem-set/bst2/)

  * [Binary-tree extensions](https://labuladong.online/algo/menu/more-bt/)
    * [Extension: framework for the lowest-common-ancestor series](https://labuladong.online/algo/practice-in-action/lowest-common-ancestor-summary/)
    * [Extension: how to count the nodes of a complete binary tree](https://labuladong.online/algo/data-structure/count-complete-tree-nodes/)
    * [Extension: lazily flattening a multi-way tree](https://labuladong.online/algo/data-structure/flatten-nested-list-iterator/)
    * [Extension: merge sort in detail and applications](https://labuladong.online/algo/practice-in-action/merge-sort/)
    * [Extension: quicksort in detail and applications](https://labuladong.online/algo/practice-in-action/quick-sort/)
    * [Extension: simulating recursion with a stack to iterate over a binary tree](https://labuladong.online/algo/data-structure/iterative-traversal-binary-tree/)

  * [Classic data-structure design](https://labuladong.online/algo/menu/design/)
    * [Algorithms are like LEGO: hand-rolling LRU](https://labuladong.online/algo/data-structure/lru-cache/)
    * [Algorithms are like LEGO: hand-rolling LFU](https://labuladong.online/algo/frequency-interview/lfu/)
    * [Constant-time delete / find of arbitrary array elements](https://labuladong.online/algo/data-structure/random-set/)
    * [More hash-table problems](https://labuladong.online/algo/problem-set/hash-table/)
    * [Classic priority-queue problems](https://labuladong.online/algo/problem-set/binary-heap/)
    * [TreeMap / TreeSet implementation](https://labuladong.online/algo/data-structure-basic/tree-map-implement/)
    * [Basic segment-tree implementation](https://labuladong.online/algo/data-structure/segment-tree-implement/)
    * [Optimization: dynamic segment tree](https://labuladong.online/algo/data-structure/segment-tree-dynamic/)
    * [Optimization: lazy-propagation segment tree](https://labuladong.online/algo/data-structure/segment-tree-lazy-update/)
    * [Classic segment-tree problems](https://labuladong.online/algo/problem-set/segment-tree/)
    * [Trie tree implementation](https://labuladong.online/algo/data-structure/trie-implement/)
    * [Trie tree problems](https://labuladong.online/algo/problem-set/trie/)
    * [Designing an exam-room seat-allocation algorithm](https://labuladong.online/algo/frequency-interview/exam-room/)
    * [More classic design problems](https://labuladong.online/algo/problem-set/ds-design/)
    * [Implementing the Huffman coding compression algorithm](https://labuladong.online/algo/data-structure/huffman-tree-implementation/)
    * [Consistent hashing — principles and implementation](https://labuladong.online/algo/data-structure/consistent-hashing/)
    * [Extension: how to implement a calculator](https://labuladong.online/algo/data-structure/implement-calculator/)
    * [Extension: median-from-stream using two binary heaps](https://labuladong.online/algo/practice-in-action/find-median-from-data-stream/)
    * [Extension: array deduplication (hard mode)](https://labuladong.online/algo/frequency-interview/remove-duplicate-letters/)


  * [Classic graph algorithms](https://labuladong.online/algo/menu/graph/)
    * [Bipartite graph detection](https://labuladong.online/algo/data-structure/bipartite-graph/)
    * [Hierholzer's algorithm for finding Eulerian paths](https://labuladong.online/algo/data-structure/eulerian-graph-hierholzer/)
    * [Classic Eulerian path problems](https://labuladong.online/algo/problem-set/eulerian-path/)
    * [Cycle detection](https://labuladong.online/algo/data-structure/cycle-detection/)
    * [Topological sort](https://labuladong.online/algo/data-structure/topological-sort/)
    * [Union-Find algorithm](https://labuladong.online/algo/data-structure/union-find/)
    * [Classic Union-Find problems](https://labuladong.online/algo/problem-set/union-find/)
    * [Dijkstra — core principles and implementation](https://labuladong.online/algo/data-structure/dijkstra/)
    * [Dijkstra extension: shortest path with constraints](https://labuladong.online/algo/data-structure/dijkstra-follow-up/)
    * [Classic Dijkstra problems](https://labuladong.online/algo/problem-set/dijkstra/)
    * [A* — core principles and implementation](https://labuladong.online/algo/data-structure/a-star/)
    * [Kruskal — minimum spanning tree](https://labuladong.online/algo/data-structure/kruskal/)
    * [Prim — minimum spanning tree](https://labuladong.online/algo/data-structure/prim/)

* [Chapter 2 — Classic brute-force search algorithms](https://labuladong.online/algo/menu/braute-force-search/)
  * [DFS / backtracking](https://labuladong.online/algo/menu/dfs/)
    * [Backtracking problem-solving framework](https://labuladong.online/algo/essential-technique/backtrack-framework/)
    * [Backtracking in practice: Sudoku and N-Queens](https://labuladong.online/algo/practice-in-action/sudoku-nqueue/)
    * [[Game] Implementing a Sudoku cheat tool](https://labuladong.online/algo/game/sudoku/)
    * [Backtracking demolishes all permutation / combination / subset problems](https://labuladong.online/algo/essential-technique/permutation-combination-subset-all-in-one/)
    * [Common questions about backtracking / DFS](https://labuladong.online/algo/essential-technique/backtrack-vs-dfs/)
    * [One article demolishes all island problems](https://labuladong.online/algo/frequency-interview/island-dfs-summary/)
    * [[Game] Minesweeper II](https://labuladong.online/algo/game/minesweeper-ii/)
    * [Ball-and-box model: two perspectives on backtracking enumeration](https://labuladong.online/algo/practice-in-action/two-views-of-backtrack/)
    * [Backtracking in practice: generating parentheses](https://labuladong.online/algo/practice-in-action/generate-parentheses/)
    * [Backtracking in practice: set partitioning](https://labuladong.online/algo/practice-in-action/partition-to-k-equal-sum-subsets/)
    * [Classic backtracking problems I](https://labuladong.online/algo/problem-set/backtrack-i/)
    * [Classic backtracking problems II](https://labuladong.online/algo/problem-set/backtrack-ii/)
    * [Classic backtracking problems III](https://labuladong.online/algo/problem-set/backtrack-iii/)

  * [BFS algorithms](https://labuladong.online/algo/menu/bfs/)
    * [BFS problem-solving framework](https://labuladong.online/algo/essential-technique/bfs-framework/)
    * [[Game] Solving a maze](https://labuladong.online/algo/game/maze/)
    * [[Game] Huarong Road sliding puzzle](https://labuladong.online/algo/game/huarong-road/)
    * [[Game] Connect-two game](https://labuladong.online/algo/game/connect-two/)
    * [Classic BFS problems I](https://labuladong.online/algo/problem-set/bfs/)
    * [Classic BFS problems II](https://labuladong.online/algo/problem-set/bfs-ii/)

* [Chapter 3 — Classic dynamic programming](https://labuladong.online/algo/menu/dp/)
  * [Basic DP techniques](https://labuladong.online/algo/menu/dp-basic/)
    * [Dynamic programming problem-solving framework](https://labuladong.online/algo/essential-technique/dynamic-programming-framework/)
    * [DP design: longest increasing subsequence](https://labuladong.online/algo/dynamic-programming/longest-increasing-subsequence/)
    * [How to choose base cases and memo initial values?](https://labuladong.online/algo/dynamic-programming/memo-fundamental/)
    * [Two perspectives on DP enumeration](https://labuladong.online/algo/dynamic-programming/two-views-of-dp/)
    * [The mental switch between DP and backtracking](https://labuladong.online/algo/dynamic-programming/word-break/)
    * [Space optimization for DP](https://labuladong.online/algo/dynamic-programming/space-optimization/)
    * [Optimal substructure and dp-array traversal direction](https://labuladong.online/algo/dynamic-programming/faq-summary/)

  * [Subsequence-style problems](https://labuladong.online/algo/menu/subsequence/)
    * [Classic DP: edit distance](https://labuladong.online/algo/dynamic-programming/edit-distance/)
    * [DP design: maximum subarray](https://labuladong.online/algo/dynamic-programming/maximum-subarray/)
    * [Classic DP: longest common subsequence](https://labuladong.online/algo/dynamic-programming/longest-common-subsequence/)
    * [DP template for subsequence problems](https://labuladong.online/algo/dynamic-programming/subsequence-problem/)

  * [Knapsack-style problems](https://labuladong.online/algo/menu/knapsack/)
    * [Classic DP: 0-1 knapsack](https://labuladong.online/algo/dynamic-programming/knapsack1/)
    * [Classic DP: subset-sum knapsack](https://labuladong.online/algo/dynamic-programming/knapsack2/)
    * [Classic DP: unbounded knapsack](https://labuladong.online/algo/dynamic-programming/knapsack3/)
    * [Knapsack variant: target sum](https://labuladong.online/algo/dynamic-programming/target-sum/)

  * [Playing games with DP](https://labuladong.online/algo/menu/dp-game/)
    * [DP: minimum path sum](https://labuladong.online/algo/dynamic-programming/minimum-path-sum/)
    * [DP helped me clear "Magic Tower"](https://labuladong.online/algo/dynamic-programming/magic-tower/)
    * [DP helped me clear "Fallout 4"](https://labuladong.online/algo/dynamic-programming/freedom-trail/)
    * [Travel saving trick: weighted shortest path](https://labuladong.online/algo/dynamic-programming/cheap-travel/)
    * [Multi-source shortest path: Floyd algorithm](https://labuladong.online/algo/data-structure/floyd/)
    * [Classic DP: regular expression matching](https://labuladong.online/algo/dynamic-programming/regular-expression-matching/)
    * [Classic DP: super-egg-drop](https://labuladong.online/algo/dynamic-programming/egg-drop/)
    * [Classic DP: burst balloons](https://labuladong.online/algo/dynamic-programming/burst-balloons/)
    * [Classic DP: game theory](https://labuladong.online/algo/dynamic-programming/game-theory/)
    * [One method demolishes the LeetCode House Robber series](https://labuladong.online/algo/dynamic-programming/house-robber/)
    * [One method demolishes the LeetCode stock buy/sell series](https://labuladong.online/algo/dynamic-programming/stock-problem-summary/)

  * [DP problem set](https://labuladong.online/algo/menu/dp-basic/)
    * [House Robber problem patterns](https://labuladong.online/algo/problem-set/rob-house/)
    * [Classic knapsack problems](https://labuladong.online/algo/problem-set/knapsack/)
    * [Classic DP problems I](https://labuladong.online/algo/problem-set/dynamic-programming-i/)
    * [Classic DP problems II](https://labuladong.online/algo/problem-set/dynamic-programming-ii/)

  * [Greedy-style problems](https://labuladong.online/algo/menu/greedy/)
    * [Greedy problem-solving framework](https://labuladong.online/algo/essential-technique/greedy/)
    * [Veteran-driver gas-station algorithm](https://labuladong.online/algo/frequency-interview/gas-station-greedy/)
    * [Greedy: interval scheduling](https://labuladong.online/algo/frequency-interview/interval-scheduling/)
    * [Sweep-line technique: scheduling meeting rooms](https://labuladong.online/algo/frequency-interview/scan-line-technique/)
    * [Editing video clips inspired a greedy algorithm](https://labuladong.online/algo/frequency-interview/cut-video/)


* [Chapter 4 — Other common algorithm techniques](https://labuladong.online/algo/menu/other/)
  * [Math tricks](https://labuladong.online/algo/menu/math/)
    * [Algorithm problems solvable in one line of code](https://labuladong.online/algo/frequency-interview/one-line-solutions/)
    * [Common bitwise operations](https://labuladong.online/algo/frequency-interview/bitwise-operation/)
    * [Must-know math techniques](https://labuladong.online/algo/essential-technique/math-techniques-summary/)
    * [[Game] Minesweeper map generator](https://labuladong.online/algo/game/minesweeper/)
    * [Random algorithms in games](https://labuladong.online/algo/frequency-interview/random-algorithm/)
    * [Two commonly tested factorial problems](https://labuladong.online/algo/frequency-interview/factorial-problems/)
    * [How to find primes efficiently](https://labuladong.online/algo/frequency-interview/print-prime-number/)
    * [Finding missing and duplicate elements at the same time](https://labuladong.online/algo/frequency-interview/mismatch-set/)
    * [A few counter-intuitive probability problems](https://labuladong.online/algo/frequency-interview/probability-problem/)
    * [Math-trick problems](https://labuladong.online/algo/problem-set/math-tricks/)

  * [Classic interview problems](https://labuladong.online/algo/menu/interview/)
    * [Solving the trapping-rain-water problem efficiently](https://labuladong.online/algo/frequency-interview/trapping-rain-water/)
    * [One article demolishes the ugly-number series](https://labuladong.online/algo/frequency-interview/ugly-number-summary/)
    * [One method solves three interval problems](https://labuladong.online/algo/practice-in-action/interval-problem-summary/)
    * [Who knew — Doudizhu (Fight the Landlord) yields an algorithm](https://labuladong.online/algo/practice-in-action/split-array-into-consecutive-subsequences/)
    * [Pancake sorting algorithm](https://labuladong.online/algo/frequency-interview/pancake-sorting/)
    * [String multiplication](https://labuladong.online/algo/practice-in-action/multiply-strings/)
    * [How to determine a perfect rectangle](https://labuladong.online/algo/frequency-interview/perfect-rectangle/)

* [More content](https://labuladong.online/algo/menu/appendix/)
  * [Computer-science fundamentals](https://labuladong.online/algo/menu/computer-basics/)
    * [Front-end intro tutorial in the AI era](https://labuladong.online/algo/computer-science/frontend-introduction/)
    * [Intro to modern cryptography](https://labuladong.online/algo/computer-science/encryption-intro/)
    * [Deep dive: session and cookie](https://labuladong.online/algo/other-skills/session-and-cookie/)
    * [Deep dive: JSON Web Token (JWT)](https://labuladong.online/algo/computer-science/how-jwt-works/)
    * [Authentication vs authorization — differences and connections](https://labuladong.online/algo/computer-science/authentication-vs-authorization/)
    * [Deep dive: OAuth 2.0 authorization framework](https://labuladong.online/algo/computer-science/oauth2-explained/)
    * [OAuth 2.0 and OIDC authentication](https://labuladong.online/algo/computer-science/oidc/)
    * [OAuth 2.0 and PKCE](https://labuladong.online/algo/computer-science/pkce/)
    * [Deep dive: Single Sign-On (SSO)](https://labuladong.online/algo/computer-science/sso/)
    * [Deep dive: digital certificates and CAs](https://labuladong.online/algo/computer-science/certificate-and-ca/)
    * [Deep dive: TLS key exchange](https://labuladong.online/algo/computer-science/tls-key-exchange/)
    * [Deep dive: mTLS mutual authentication](https://labuladong.online/algo/computer-science/mtls/)
    * [Getting started with Linux file systems](https://labuladong.online/algo/other-skills/linux-file-system/)
    * [What are Linux processes, threads, and file descriptors?](https://labuladong.online/algo/other-skills/linux-process/)
    * [Pitfalls of Linux pipes](https://labuladong.online/algo/other-skills/linux-pipeline/)
    * [Tips for using the Linux shell](https://labuladong.online/algo/other-skills/linux-shell/)
    * [Storage systems: design principles of LSM-trees](https://labuladong.online/algo/other-skills/lsm-tree/)
    * [Updating in progress](https://labuladong.online/algo/intro/updating/)

  * [Design patterns](https://labuladong.online/algo/menu/design-pattern/)
    * [Singleton pattern](https://labuladong.online/algo/design-pattern/singleton/)
    * [Factory method pattern](https://labuladong.online/algo/design-pattern/factory-method/)
    * [Abstract factory pattern](https://labuladong.online/algo/design-pattern/abstract-factory/)
    * [Builder pattern](https://labuladong.online/algo/design-pattern/builder/)
    * [Prototype pattern](https://labuladong.online/algo/design-pattern/prototype/)
    * [Adapter pattern](https://labuladong.online/algo/design-pattern/adapter/)
    * [Composite pattern](https://labuladong.online/algo/design-pattern/composite/)
    * [Decorator pattern](https://labuladong.online/algo/design-pattern/decorator/)
    * [Bridge pattern](https://labuladong.online/algo/design-pattern/bridge/)
    * [Observer pattern](https://labuladong.online/algo/design-pattern/observer/)
    * [Strategy pattern](https://labuladong.online/algo/design-pattern/strategy/)
    * [Updating in progress](https://labuladong.online/algo/intro/updating/)


<!-- table end -->

# Thanks to the following contributors for translation help

In alphabetical order of nickname:

[ABCpril](https://github.com/ABCpril), 
[andavid](https://github.com/andavid), 
[bryceustc](https://github.com/bryceustc), 
[build2645](https://github.com/build2645), 
[CarrieOn](https://github.com/CarrieOn), 
[cooker](https://github.com/xiaochuhub), 
[Dong Wang](https://github.com/Coder2Programmer), 
[ExcaliburEX](https://github.com/ExcaliburEX), 
[floatLig](https://github.com/floatLig), 
[ForeverSolar](https://github.com/foreversolar), 
[Fulin Li](https://fulinli.github.io/), 
[Funnyyanne](https://github.com/Funnyyanne), 
[GYHHAHA](https://github.com/GYHHAHA), 
[Hi_archer](https://hiarcher.top/), 
[Iruze](https://github.com/Iruze), 
[Jieyixia](https://github.com/Jieyixia), 
[Justin](https://github.com/Justin-YGG), 
[Kevin](https://github.com/Kevin-free), 
[Lrc123](https://github.com/Lrc123), 
[lriy](https://github.com/lriy), 
[Lyjeeq](https://github.com/Lyjeeq), 
[MasonShu](https://greenwichmt.github.io/), 
[Master-cai](https://github.com/Master-cai), 
[miaoxiaozui2017](https://github.com/miaoxiaozui2017), 
[natsunoyoru97](https://github.com/natsunoyoru97), 
[nettee](https://github.com/nettee), 
[PaperJets](https://github.com/PaperJets), 
[qy-yang](https://github.com/qy-yang), 
[realism0331](https://github.com/realism0331), 
[SCUhzs](https://github.com/brucecat), 
[Seaworth](https://github.com/Seaworth), 
[shazi4399](https://github.com/shazi4399), 
[ShuozheLi](https://github.com/ShuoZheLi/), 
[sinjoywong](https://blog.csdn.net/SinjoyWong), 
[sunqiuming526](https://github.com/sunqiuming526), 
[Tianhao Zhou](https://github.com/tianhaoz95), 
[timmmGZ](https://github.com/timmmGZ), 
[tommytim0515](https://github.com/tommytim0515), 
[ucsk](https://github.com/ucsk), 
[wadegrc](https://github.com/wadegrc), 
[walsvid](https://github.com/walsvid), 
[warmingkkk](https://github.com/warmingkkk), 
[Wonderxie](https://github.com/Wonderxie), 
[wsyzxxxx](https://github.com/wsyzxxxx), 
[xiaodp](https://github.com/xiaodp), 
[youyun](https://github.com/youyun), 
[yx-tan](https://github.com/yx-tan), 
[Zero](https://github.com/Mr2er0), 
[Ziming](https://github.com/ML-ZimingMeng/LeetCode-Python3)

# Donate

If this repo helped you, you can buy the author a cup of instant coffee.

<img src="pictures/pay.jpg" width = "200" align=center />
