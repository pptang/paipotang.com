#

——
**title**: Implement your own Heap in JavaScript
**description**: Implement your own Heap data structure in JavaScript for coding interview
**date**: 2021-05-20
**tags**:

- algorithm
  **layout**: layouts/post.njk
  ——

There are a big portion of coding interview questions that you have to solve using `Heap` data structure.

**## Ugh🤢 JavaScript doesn’t come with `Heap`**

It’s quite a disadvantage that we JavaScripter don’t have `Heap` battery in our toolbox,
so normally we go for alternatives listed below:

**#### 1. Import libraries**

For example, I often make use of this one: https://www.collectionsjs.com/heap

_*Issues:*_

1. Interviewers don’t allow you to do that
2. If it’s online coding test, chances are that you just can’t do that.

**#### 2. Explain verbally**

You can tell interviewers that you’re gonna use `Heap` to solve this question and focus on the core algorithm. You should provide at least the interface of the `Heap` you will use and briefly explain the time complexity of each operation.

_*Issues:*_

1. Interviewers in turn ask you to implement a real `Heap`
2. You can’t use this approach during online coding tests.

**#### 3. Implement the `Heap` using `sort` function**

For each `push` and `pop` to the `Heap`, you can make use of the JavaScript’s `sort` function to adjust the array.

_*Issues:*_

1. The time complexity of `sort` function is `O(NlogN)`, so your algorithm may go over the time limitation.

**#### 4. Implement the real `Heap` on your own**

Although it’s still a half-baked version, it follows how a real `Heap` would work under the hood. As a result, the time complexity for `push` and `pop` are `O(logN)` and you definitely can pass the time limitation of the given coding questions.

_*Issues:*_

1. It takes time to implement your own `Heap`.

**#### 5. Use `Python` instead**

😳😳😳
I believe people would feel more comfortable solving coding interview questions with the programming language they use for daily works.

---

Each option mentioned above can be a valid one depending on the situation. What we can do to prepare is to mitigate the impact of issues for each option. It turns out the only thing we can make more efforts is to master yourself in implementing your own `Heap` \***\*faster\*\***, so that’s what I do. The below is the implementation I always use, check it out!

**## My implementation**

```js
class Heap {
  constructor(compare) {
    this.compare = compare;
    this.arr = [];
  }

  get length() {
    return this.arr.length;
  }

  peek() {
    return this.arr.length > 0 ? this.arr[0] : null;
  }

  push(node) {
    this.arr.push(node);
    this.float(this.arr.length - 1);
  }

  *// Brings a node up until its parent is greater*
  *// (or smaller, depending on compare function) than it*
  float(index) {
    const childNode = this.arr[index];
    while (index > 0) {
      *// Compute the parent index (childIndex = parentIndex * 2 + 1 || parentIndex * 2 + 2)*
      const parentIndex = Math.floor((index - 1) / 2);
      const parentNode = this.arr[parentIndex];
      if (this.compare(childNode, parentNode) > 0) {
        [this.arr[index], this.arr[parentIndex]] = [
          this.arr[parentIndex],
          this.arr[index],
        ];
        index = parentIndex;
      } else {
        break;
      }
    }
  }

  pop() {
    if (this.length === 0) return null;
    if (this.length === 1) return this.arr.pop();
    *// The top node will be return*
    const currTop = this.arr[0];
    *// Get the new top node from the end of the array*
    *// because if we use the second top one, it might*
    *// mess up the order of the tree*
    const newTop = this.arr.pop();
    this.arr[0] = newTop;
    if (this.arr.length > 1) {
      this.sink(0);
    }
    return currTop;
  }
  *// Brings a node down until its children are both less than*
  *// (or greater than, depending on compare function) than it*
  sink(index) {
    const heapSize = this.arr.length;
    while (true) {
      const leftChildIndex = index * 2 + 1;
      const rightChildIndex = index * 2 + 2;
      let swapIndex = index, needsSwap = false;
      let leftChildNode, rightChildNode;
      if (leftChildIndex < heapSize) {
        leftChildNode = this.arr[leftChildIndex];
        const parentNode = this.arr[index];
        if (this.compare(leftChildNode, parentNode) > 0) {
          swapIndex = leftChildIndex;
          needsSwap = true;
        }
      }
      if (rightChildIndex < heapSize) {
        rightChildNode = this.arr[rightChildIndex];
        const swapNode = this.arr[swapIndex];
        if (
          this.compare(rightChildNode, swapNode) >
          0
        ) {
          swapIndex = rightChildIndex;
          needsSwap = true;
        }
      }
      if (needsSwap) {
        [this.arr[index], this.arr[swapIndex]] = [
          this.arr[swapIndex],
          this.arr[index],
        ];
        index = swapIndex;
        *// continue sinking*
      } else {
        *// Both children are greater than (or less than) the parent node*
        break;
      }
    }
  }
}
```

To test your own `Heap` implementation, you can try:

```js
const maxHeap = new Heap((a, b) => a - b);
maxHeap.push(2);
console.log(maxHeap.peek()); *// 2*
maxHeap.push(8);
console.log(maxHeap.peek()); *// 8*
maxHeap.push(1);
console.log(maxHeap.pop()); *// 8*
console.log(maxHeap.peek()); *// 2*
```

That’s it 👨‍💻! JavaScripters are not second-class citizens anymore 😄!
