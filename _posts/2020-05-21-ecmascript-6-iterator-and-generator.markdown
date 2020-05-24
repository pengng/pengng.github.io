---
layout: post
title:  "ECMAScript 6 迭代器和生成器"
categories: ECMAScript
---

### 迭代器 Iterator

迭代器对象有一个 `next` 方法，每次执行后返回一个结果对象。结果对象有两个属性，一个是 `value` ，另一个是 `done` 。

迭代器对象还有一个内部属性，用于指向集合中的当前位置。类似于 `for` 循环的变量 `i` 。

迭代器通常会保存集合的引用。这意味着集合的元素发生改变时，会影响迭代器的返回值。

如下代码所示：

```javascript
let arr = [1, 3, 5];

let iterator = (function() {
  let i = 0;
  
  return {
    next: function() {
      let done = i >= arr.length;
      let value = done ? undefined : arr[i++];
  
      return {
        done: done,
        value: value
      };
    }
  }
})()
```

使用立即执行函数表达式(IIFE)，将返回值赋给 `iterator` ，此时 `iterator` 为迭代器对象。
同时变量 `i` 只有这个迭代器对象的 `next` 方法可以访问到，并且每次执行都会将 `i` 的值递增，这意味着迭代器对象只能迭代一遍。

另外，因为迭代器对象保存的是 `arr` 的引用，所以当 `arr` 内的元素改变时，也会影响迭代器的 `next` 方法的返回值。

以上代码是 ECMAScript 6 之前创建迭代器的方式，在 ECMAScript 6 标准里，使用 **生成器函数 Generator Function** 更方便创建迭代器。

### 生成器 Generator

生成器函数通常配合新的关键字 `yield` 一起使用。如下代码所示：

```javascript
let arr = [1, 3, 5];

let iterator = (function *(arr) {
  for (let i = 0, len = arr.length; i < len; i++) {
    yield arr[i];
  }
})(arr)
```

生成器函数创建的 `iterator` 对象也有 `next` 方法，且行为和之前迭代器部分的示例代码类似。

但两种方式创建得到的迭代器有一些不同，原来的方法得到的 `iterator` 只是一个拥有 `next` 方法的普通对象，是 `Object` 的实例。
而生成器函数创建的对象是 `Generator` 的一个实例，且除了 `next` 方法，还有 `return` 和 `throw` 方法。

生成器函数和普通函数有一些不同，不只是定义函数时多写一个星号，在行为上也有不同。

在普通函数中使用 `return` 关键字会提前从被调函数返回到主调函数，也会影响函数的返回值。
而在生成器函数中，使用 `return` 关键字只会提前返回，但无法改变函数的返回值，生成器函数的返回值始终是一个迭代器对象。
而 `return` 的值会作为这个迭代器对象调用 `next` 方法的最终结果值。

另外，新的 `yield` 关键字只能在生成器函数中使用，在普通函数中使用会报错，即使是嵌套关系也不行。
如下代码所示：

```javascript
(function *() {
  (function () {
    yield 'value';
  })()
})()
```

与生成器和迭代器相关的，还有一种 **可迭代对象 Iterable** 。

### 可迭代对象 Iterable

可迭代对象有一个属性，值为函数，而键名是 `Symbol.iterator` 这个唯一值。

新的 `for-of` 循环和展开运算符都需要接收一个 **可迭代对象** 。
如果处理的是一个对象，则会判断对象上是否有 `Symbol.iterator` 这个属性，属性值是否是一个函数，是则执行这个函数，得到迭代器。
否则抛出一个错误，提示该对象不是一个 **可迭代对象** 。

`ES6` 中数组和 `Map`、`Set` 的实例都默认有 `Symbol.iterator` 属性，所以可以用于 `for-of` 循环和展开运算符。

要自定义一个 **可迭代对象** ，可以在一个对象上增加 `Symbol.iterator` 属性，代码如下：

```javascript
let arr = [1, 3, 5];

let iterator = (function() {
  let i = 0;
  
  return {
    next: function() {
      let done = i >= arr.length;
      let value = done ? undefined : arr[i++];
  
      return {
        done: done,
        value: value
      };
    }
  }
})()

iterator[Symbol.iterator] = function() {
  return this;
}
```

`iterator` 既是一个 **迭代器对象**，同是又是一个 **可迭代对象** 。
因为它既有 `next` 方法，又有 `Symbol.iterator` 属性。

这个 `iterator` 对象也可以用于 `for-of` 循环和展开运算符，如下所示：

```javascript
let arr_new = [...iterator];  // arr_new = [1, 3, 5]
```

这意味着， **可迭代对象** 的 `Symbol.iterator` 属性值不一定非得是生成器函数，即使是普通函数，但只要函数返回值是迭代器即可。

另外，`Symbol.iterator` 属性所对应的函数里可以使用 `this` 。
在 `for-of` 循环和展开运算符调用的情况下，`this` 会指向 **可迭代对象** 。

而且，`Symbol.iterator` 属性所对应函数的返回值不一定非得是一个 `Generator` 的实例。
即使是一个普通对象，但有 `next` 方法即可。

这里的 `iterator` 作为 **可迭代对象** 有一个缺点，因为它的 `Symbol.iterator` 属性对应的函数多次调用都会返回 `iterator` 自身 ，但 `iterator` 的内部属性 `i` 在经历了一遍迭代后，就不会再变回零值，所以 `iterator` 只能用一次。如下代码所示：

 ```javascript
let arr_new = [...iterator];      // arr_new = [1, 3, 5]
let arr_latest = [...iterator];   // arr_latest = []
 ```

数组和 `Map` 、 `Set` 的实例都有 `values` 、`keys` 、 `entries` 方法，这些方法的返回值都是 **可迭代对象** ，同是又是 **迭代器对象** 。
因为这些方法所返回的对象上，`Symbol.iterator` 属性对应的函数执行后，返回值是迭代器自身，所以也有上述特点。如下代码所示：

```javascript
let arr = [1, 3, 5];
let iterable = arr.values();
let arr_new = [...iterable];      // arr_new = [1, 3, 5]
let arr_latest = [...iterable];   // arr_latest = []
let isSame = iterable[Symbol.iterator]() === iterable;    // isSame = true
```

处理这种情况，建议将这些方法的返回值作为 **立即值** 使用，如下所示：

```javascript
let arr = [1, 3, 5];
let arr_new = [...iterable.values()];      // arr_new = [1, 3, 5]
let arr_latest = [...iterable.values()];   // arr_latest = [1, 3, 5]
```
