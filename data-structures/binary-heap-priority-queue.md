# Binary Heap In-Depth: Implementing a Priority Queue

This article has been rewritten. Please read [Binary Heap Basics](https://labuladong.online/algo/data-structure-basic/binary-heap-basic/) and [Implementing a Priority Queue with a Binary Heap](https://labuladong.online/algo/data-structure-basic/binary-heap-implement/).





**＿＿＿＿＿＿＿＿＿＿＿＿＿**

**"labuladong's Algorithm Notes" has been published. Follow the official account for details; reply with "all-in-one bundle" to download the companion PDF and the full problem-solving bundle**:

![](https://labuladong.online/algo/images/souyisou2.png)

====== Code in Other Languages ======

### javascript

```js
/**
 * Max-heap
 */
function left(i) {
  return i * 2 + 1;
}

function right(i) {
  return i * 2 + 2;
}

function swap(A, i, j) {
  const t = A[i];
  A[i] = A[j];
  A[j] = t;
}

class Heap {
  constructor(arr) {
    this.data = [...arr];
    this.size = this.data.length;
  }

  /**
   * Rebuild the heap
   */
  rebuildHeap() {
    const L = Math.floor(this.size / 2);
    for (let i = L - 1; i >= 0; i--) {
      this.maxHeapify(i);
    }
  }

  isHeap() {
    const L = Math.floor(this.size / 2);
    for (let i = L - 1; i >= 0; i++) {
      const l = this.data[left(i)] || Number.MIN_SAFE_INTEGER;
      const r = this.data[right(i)] || Number.MIN_SAFE_INTEGER;

      const max = Math.max(this.data[i], l, r);

      if (max !== this.data[i]) {
        return false;
      }
      return true;
    }
  }

  sort() {
    for (let i = this.size - 1; i > 0; i--) {
      swap(this.data, 0, i);
      this.size--;
      this.maxHeapify(0);
    }
  }

  insert(key) {
    this.data[this.size++] = key;
    if (this.isHeap()) {
      return;
    }
    this.rebuildHeap();
  }

  delete(index) {
    if (index >= this.size) {
      return;
    }
    this.data.splice(index, 1);
    this.size--;
    if (this.isHeap()) {
      return;
    }
    this.rebuildHeap();
  }

  /**
   * Every other part of the heap satisfies the property;
   * only the root node violates it, so restore the heap property.
   * @param {*} i
   */
  maxHeapify(i) {
    let max = i;

    if (i >= this.size) {
      return;
    }

    // Find the index of the larger child
    const l = left(i);
    const r = right(i);
    if (l < this.size && this.data[l] > this.data[max]) {
      max = l;
    }

    if (r < this.size && this.data[r] > this.data[max]) {
      max = r;
    }

    // If the current node is the largest, the max-heap property already holds
    if (max === i) {
      return;
    }

    swap(this.data, i, max);

    // Recurse downward
    return this.maxHeapify(max);
  }
}

module.exports = Heap;
```

