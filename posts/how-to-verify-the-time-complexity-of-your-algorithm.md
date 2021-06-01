---
title: How to verify the time complexity of your algorithm?
description: Today I learned how I can verify the time complexity of my algorithm using another program.
date: 2021-06-01
tags:
  - TIL
  - JavaScript
layout: layouts/post.njk
---

Probably most of you already know how to use Big-O notation to estimate the time complexity of your algorithm, but how do you prove it?

## Calculation speed of modern computers
Let's start with knowing the calculation speed of modern computers. Nowadays, `10^8` operations can be performed within a second by an average computer. As a result, it basically implies that your algorithm can finish the task in 1 second for the below cases:

1. when you claim the time complexity of your algorithm is `O(N)` or `O(NlogN)` => _size of your input `N` can be between `10^6` to `10^8`._ 
2. when you claim the time complexity of your algorithm is `O(N^2)` => _size of your input `N` can be up to `10^4` (cause `10^4 * 10^4 = 10^8)._
3. when you claim the time complexity of your algorithm is `O(N^3)` => _size of your input `N` can be up to `500` (cause `500 * 500 * 500 ~= 10^8)._

## Track how long an operation takes
In JavaScript, you can make use of [`console.time()`](https://developer.mozilla.org/en-US/docs/Web/API/Console/time) and [`console.timeEnd()`](https://developer.mozilla.org/en-US/docs/Web/API/Console/timeEnd) functions to track the time your algorithm takes, by doing as follows:
```js
console.time('super-algorithm');
superAlgorithm();
console.timeEnd('super-algorithm');
// => super-algorithm: 60.403ms
```

## Examples
Given two arrays `n1` and `n2`, find each element of `n2` in `n1` and return another array containing the first occurence index of `n1`.

- `O(N^2)` solution
```js
function find(n1, n2) {
  let result = [];
  for (let i = 0; i < n2.length; i++) {
    for (let j = 0; j < n1.length; j++) {
      if (n2[i] == n1[j]) {
        result.push(j);
        break;
      } else if (j == n1.length - 1){
        result.push(-1);
      }
    }
  }
  return result;
}
```

_Verify the consuming time when the input size is **<10^4**_
```js
let testArr1 = [], testArr2 = [];

for (let i = 0; i < 10 ** 4; i++) {
  testArr1.push(i);
  testArr2.push(i);
}

console.time('find');
find(testArr1, testArr2);
console.timeEnd('find');
// => find: 89.401ms (< 1 second)
```

_Verify the consuming time when the input size is **>10^4**_
```js
let testArr1 = [], testArr2 = [];

for (let i = 0; i < 10 ** 5; i++) {
  testArr1.push(i);
  testArr2.push(i);
}

console.time('find');
find(testArr1, testArr2);
console.timeEnd('find');
// => find: 8201.013ms (> 1 second)
```

- `O(N)` solution
```js
function find(n1, n2) {
  let result = [];
  let map = {};
  for (let i = 0; i < n1.length; i++) {
    const currNum = n1[i];
    if (currNum in map) {
      continue;
    } else {
      map[currNum] = i;
    }
  }
  for (let j = 0; j < n2.length; j++) {
    if (n2[j] in map) {
      result.push(map[n2[j]]);
    } else {
      result.push(-1);
    }
  }
  return result;
}
```

_Verify the consuming time when the input size is **<10^7**_
```js
let testArr1 = [], testArr2 = [];

for (let i = 0; i < 10 ** 7; i++) {
  testArr1.push(i);
  testArr2.push(i);
}

console.time('find');
find(testArr1, testArr2);
console.timeEnd('find');
// => find: 659.739ms (< 1 second)
```

**NOTE: The input size supposed can be around `10^8`, but due to the size limit of the key of JavaScript's `Object`, if try with `10^8`, you'll receive `Javascript heap out of memory` error.** 


_Verify the consuming time when the input size is **>10^8**_
```js
let testArr1 = [], testArr2 = [];

for (let i = 0; i < 10 ** 9; i++) {
  testArr1.push(i);
  testArr2.push(i);
}

console.time('find');
find(testArr1, testArr2);
console.timeEnd('find');
// => it'll be hanging, I think it's also `JavaScript heap out of memory` error.
```

---
That's it! Try to speak out loud the time complexity of your algorithm in confidence 😤!