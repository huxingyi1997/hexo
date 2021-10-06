---
title:  bigfrontend的前端题目讨论
date: 2021-06-17 21:07:33
categories: 
- web前端
tags:
- bigfrontend
- 前端
- 题库
---

国外前端会考察哪些问题？js函数、系统设计、TS类型体操的用法，大大拓宽了我的眼界，去思考和实战更加相关的问题吧
<!-- more -->

## js函数

### 1. implement curry()

[Currying](https://en.wikipedia.org/wiki/Currying) is a useful technique used in JavaScript applications.

Please implement a `curry()` function, which accepts a function and return a curried one.

Here is an example

```js
const join = (a, b, c) => {
   return `${a}_${b}_${c}`
}

const curriedJoin = curry(join)

curriedJoin(1, 2, 3) // '1_2_3'

curriedJoin(1)(2, 3) // '1_2_3'

curriedJoin(1, 2)(3) // '1_2_3'
```

more to read

https://javascript.info/currying-partials

https://lodash.com/docs/4.17.15#curry

使用闭包返回执行的结果，函数柯里化

```js
/**
 * @param { (...args: any[]) => any } fn
 * @returns { (...args: any[]) => any }
 */
function curry(fn) {
  // your code here
  return function curryInner(...args) {
    if (args.length >= fn.length) return fn(...args);
    return (...newArgs) => curryInner(...args, ...newArgs);
  }
}


const join = (a, b, c) => {
   return `${a}_${b}_${c}`;
}

const curriedJoin = curry(join);

console.log(curriedJoin(1, 2, 3)); // '1_2_3'

console.log(curriedJoin(1)(2, 3)); // '1_2_3'

console.log(curriedJoin(1, 2)(3)); // '1_2_3'
```

### 2. implement curry() with placeholder support

This is a follow-up on [1. implement curry()](https://bigfrontend.dev/problem/implement-curry)

please implement `curry()` which also supports placeholder.

Here is an example

```js
const  join = (a, b, c) => {
   return `${a}_${b}_${c}`
}

const curriedJoin = curry(join)
const _ = curry.placeholder

curriedJoin(1, 2, 3) // '1_2_3'

curriedJoin(_, 2)(1, 3) // '1_2_3'

curriedJoin(_, _, _)(1)(_, 3)(2) // '1_2_3'
```

more to read

https://javascript.info/currying-partials

https://lodash.com/docs/4.17.15#curry

https://github.com/planttheidea/curriable

Related Problems

[1. implement curry()](https://bigfrontend.dev/problem/implement-curry)

```js
/**
 * @param { (...args: any[]) => any } fn
 * @returns { (...args: any[]) => any }
 */
function curry(fn) {
  // your code here
  return function curryInner(...args) {
    if (args.length >= fn.length && !args.includes(curry.placeholder)) return fn(...args);
    return (...newArgs) => curryInner(...args.map(item => item === curry.placeholder ? newArgs.shift() : item), ...newArgs);
  }
}


curry.placeholder = Symbol()
```

### 3. implement Array.prototype.flat()

There is already [Array.prototype.flat()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat) in JavaScript (ES2019), which reduces the nesting of Array.

Could you manage to implement your own one?

Here is an example to illustrate

```js
const arr = [1, [2], [3, [4]]];

flat(arr)
// [1, 2, 3, [4]]

flat(arr, 1)
// [1, 2, 3, [4]]

flat(arr, 2)
// [1, 2, 3, 4]
```

**follow up**

Are you able to solve it both recursively and iteratively?

直接递归展开

```js
/**
 * @param { Array } arr
 * @param { number } depth
 * @returns { Array }
 */
function flat(arr, depth = 1) {
  let flatArray = [];
  for (const item of arr) {
    if (Array.isArray(item) && depth > 0) {
      flatArray = flatArray.concat(flat(item, depth - 1));
    } else {
      flatArray.push(item);
    }
  }
  return flatArray;
}
```

改用reduce递归

```js
/**
 * @param { Array } arr
 * @param { number } depth
 * @returns { Array }
 */
function flat(arr, depth = 1) {
  // your imeplementation here
  return arr.reduce(
    (acc, item) => 
      Array.isArray(item) && depth > 0
        ? acc.concat(flat(item, depth - 1))
        : [...acc, item],
    []
  )
}
```

### 4. implement basic throttle()

Throttling is a common technique used in Web Application, in most cases using [lodash solution](https://lodash.com/docs/4.17.15#throttle) would be a good choice.

could you implement your own version of basic `throttle()`?

In case you forgot, `throttle(func, delay)` will return a throttled function, which will invoke the func at a max frequency no matter how throttled one is called.

Here is an example.

Before throttling we have a series of calling like

─A─B─C─ ─D─ ─ ─ ─ ─ ─ E─ ─F─G

After throttling at wait time of 3 dashes

─A─ ─ ─C─ ─ ─D ─ ─ ─ ─ E─ ─ ─G

Be aware that

- call A is triggered right way because not in waiting time
- function call B is swallowed because B, C is in the cooling time from A, and C is latter.

**notes**

1. please follow above spec. the behavior is not exactly the same as `lodash.throttle()`
2. because `window.setTimeout` and `window.clearTimeout` are not accurate in browser environment, they are replaced to other implementation when judging your code. They still have the same interface, and internally keep track of the timing for testing purpose.

Something like below will be used to do the test.

```js
let currentTime = 0

const run = (input) => {
  currentTime = 0
  const calls = []

  const func = (arg) => {
     calls.push(`${arg}@${currentTime}`)
  }

  const throttled = throttle(func, 3)
  input.forEach((call) => {
     const [arg, time] = call.split('@')
     setTimeout(() => throttled(arg), time)
  })
  return calls
}

expect(run(['A@0', 'B@2', 'C@3'])).toEqual(['A@0', 'C@3'])
```

Related Problems

[5. implement throttle() with leading & trailing option](https://bigfrontend.dev/problem/implement-throttle-with-leading-and-trailing-option)

[6. implement basic debounce()](https://bigfrontend.dev/problem/implement-basic-debounce)

[7. implement debounce() with leading & trailing option](https://bigfrontend.dev/problem/implement-debounce-with-leading-and-trailing-option)

利用标志位isWaiting保存状态，判断是否在等待，lastCallArgs保存上一次的参数。

```js

/**
 * @param {Function} func
 * @param {number} wait
 */
function throttle(func, wait) {
  // Track if we are waiting. Initially, we are not.
  let isWaiting = false;
  // Track arguments of last call
  let lastCallArgs = null;

  return function throttled(...args) {
    // If we are waiting,
    if (isWaiting) {
      // ...store arguments of last call
      lastCallArgs = args;
      return;
    }
    
    // If we are not waiting, execute 'func' with passed arguments
    func.apply(this, args);
    // Prevent future execution of 'func'
    isWaiting = true;

    // After wait time,
    setTimeout(() => {
      // ...allow execution of 'func'
      isWaiting = false;

      // If arguments of last call exists,
      if (lastCallArgs) {
        // ...execute function throttled and pass last call's arguments
        // to it. Since now we are not waiting, 'func' will be executed
        // and isWaiting will be reset to true.
        throttled.apply(this, lastCallArgs);
        // ...reset arguments of last call to null.
        lastCallArgs = null;
      }
    }, wait);
  }
}
```

### 5. implement throttle() with leading & trailing option

This is a follow up on [4. implement basic throttle()](https://bigfrontend.dev/problem/implement-basic-throttle), please refer to it for detailed explanation.

In this problem, you are asked to implement a enhanced `throttle()` which accepts third parameter, `option: {leading: boolean, trailing: boolean}`

1. leading: whether to invoke right away
2. trailing: whether to invoke after the delay.

[4. implement basic throttle()](https://bigfrontend.dev/problem/implement-basic-throttle()) is the default case with `{leading: true, trailing: true}`.

**Explanation**

for the previous example of throttling by 3 dashes

─A─B─C─ ─D─ ─ ─ ─ ─ ─ E─ ─F─G

with `{leading: true, trailing: true}`, we get as below

─A─ ─ ─C─ ─ ─D ─ ─ ─ ─ E─ ─ ─G

with `{leading: false, trailing: true}`, A and E are swallowed.

─ ─ ─ ─C─ ─ ─D─ ─ ─ ─ ─ ─ ─G

with` {leading: true, trailing: false}`, only A D E are kept

─A─ ─ ─ ─D─ ─ ─ ─ ─ ─ E

with `{leading: false, trailing: false}`, of course, nothing happens.

**notes**

1. please follow above spec. the behavior is not exactly the same as `lodash.throttle()`
2. because `window.setTimeout` and `window.clearTimeout` are not accurate in browser environment, they are replaced to other implementation when judging your code. They still have the same interface, and internally keep track of the timing for testing purpose.

Something like below will be used to do the test.

```js
let currentTime = 0

const run = (input) => {
  currentTime = 0
  const calls = []

  const func = (arg) => {
     calls.push(`${arg}@${currentTime}`)
  }

  const throttled = throttle(func, 3)
  input.forEach((call) => {
     const [arg, time] = call.split('@')
     setTimeout(() => throttled(arg), time)
  })
  return calls
}

expect(run(['A@0', 'B@2', 'C@3'])).toEqual(['A@0', 'C@3'])
```

Related Problems

[4. implement basic throttle() ](https://bigfrontend.dev/problem/implement-basic-throttle)

[6. implement basic debounce()](https://bigfrontend.dev/problem/implement-basic-debounce)

[7. implement debounce() with leading & trailing option](https://bigfrontend.dev/problem/implement-debounce-with-leading-and-trailing-option)

[92. throttle Promises](https://bigfrontend.dev/problem/throttle-Promises)

基于上一题的版本增加一个判断

```js

/**
 * @param {Function} func
 * @param {number} wait
 * @param {boolean} option.leading
 * @param {boolean} option.trailing
 */
function throttle(func, wait, option = {leading: true, trailing: true}) {
  // your code here
  // Track if we are waiting. Initially, we are not.
  let isWaiting = false;
  // Track arguments of last call
  let lastCallArgs = null;

  const handleTimer = () => {
    isWaiting = false;
    if (lastCallArgs && option.trailing) {
      // ...allow execution of 'func'
      func.apply(this, lastCallArgs);
      lastCallArgs = null;
      // Prevent future execution of 'func'
      isWaiting = true;
      setTimeout(handleTimer, wait);
    }
  };

  return function throttled(...args) {
    // If we are waiting,
    if (isWaiting) {
      // ...store arguments of last call
      lastCallArgs = args;
      return;
    }
    
    // If we are not waiting, execute 'func' with passed arguments
    if (option.leading) {
      func.apply(this, args);
    }
    // Prevent future execution of 'func'
    isWaiting = true;

    // After wait time,
    setTimeout(handleTimer, wait);
  }
}
```

### 6. implement basic debounce()

Debounce is a common technique used in Web Application, in most cases using [lodash solution](https://lodash.com/docs/4.17.15#debounce) would be a good choice.

could you implement your own version of basic `debounce()`?

In case you forgot, `debounce(func, delay)` will returned a debounced function, which delays the invoke.

Here is an example.

Before debouncing we have a series of calling like

─A─B─C─ ─D─ ─ ─ ─ ─ ─E─ ─F─G

After debouncing at wait time of 3 dashes

─ ─ ─ ─ ─ ─ ─ ─ D ─ ─ ─ ─ ─ ─ ─ ─ ─ G

**notes**

1. please follow above spec. the behavior might not be exactly the same as `lodash.debounce()`
2. because `window.setTimeout` and `window.clearTimeout` are not accurate in browser environment, they are replaced to other implementation when judging your code. They still have the same interface, and internally keep track of the timing for testing purpose.

Something like below will be used to do the test.

```js
let currentTime = 0

const run = (input) => {
  currentTime = 0
  const calls = []

  const func = (arg) => {
     calls.push(`${arg}@${currentTime}`)
  }

  const debounced = debounce(func, 3)
  input.forEach((call) => {
     const [arg, time] = call.split('@')
     setTimeout(() => debounced(arg), time)
  })
  return calls
}

expect(run(['A@0', 'B@2', 'C@3'])).toEqual(['C@5'])
```

[Source for this ](https://www.glassdoor.com/Interview/Related-to-question-1-how-could-you-implement-debouncing-Say-you-wanted-the-handleScroll-function-to-be-called-only-aft-QTN_1221446.htm)

Related Problems

[4. implement basic throttle() ](https://bigfrontend.dev/problem/implement-basic-throttle)

[5. implement throttle() with leading & trailing option ](https://bigfrontend.dev/problem/implement-throttle-with-leading-and-trailing-option)

[7. implement debounce() with leading & trailing option](https://bigfrontend.dev/problem/implement-debounce-with-leading-and-trailing-option)

基本版防抖，使用定时器

```js
/**
 * @param {Function} func
 * @param {number} wait
 */
function debounce(func, wait) {
  // your code here
  let timer = null;
  return function debounced(...args) {
    if (timer) clearTimeout(timer);

    timer = setTimeout(() => {
      func.apply(this, args);
    }, wait)
  }
}
```

### 7. implement debounce() with leading & trailing option

This is a follow up on [6. implement basic debounce()](https://bigfrontend.dev/problem/implement-basic-debounce), please refer to it for detailed explanation.

In this problem, you are asked to implement an enhanced `debounce()` which accepts third parameter, `option: {leading: boolean, trailing: boolean}`

1. leading: whether to invoke right away
2. trailing: whether to invoke after the delay.

[6. implement basic debounce()](https://bigfrontend.dev/problem/implement-basic-debounce()) is the default case with `{leading: false, trailing: true}`.

for the previous example of debouncing by 3 dashes

─A─B─C─ ─D─ ─ ─ ─ ─ ─ E─ ─F─G

with {leading: false, trailing: true}, we get as below

─ ─ ─ ─ ─ ─ ─ ─D─ ─ ─ ─ ─ ─ ─ ─ ─ G

with {leading: true, trailing: true}:

─A─ ─ ─ ─ ─ ─ ─D─ ─ ─E─ ─ ─ ─ ─ ─G

with {leading: true, trailing: false}

─A─ ─ ─ ─ ─ ─ ─ ─ ─ ─E

with {leading: false, trailing: false}, of course, nothing happens.

**notes**

1. please follow above spec. the behavior might not be exactly the same as `lodash.debounce()`
2. because `window.setTimeout` and `window.clearTimeout` are not accurate in browser environment, they are replaced to other implementation when judging your code. They still have the same interface, and internally keep track of the timing for testing purpose.

Something like below will be used to do the test.

```js
let currentTime = 0

const run = (input) => {
  currentTime = 0
  const calls = []

  const func = (arg) => {
     calls.push(`${arg}@${currentTime}`)
  }

  const debounced = debounce(func, 3)
  input.forEach((call) => {
     const [arg, time] = call.split('@')
     setTimeout(() => debounced(arg), time)
  })
  return calls
}

expect(run(['A@0', 'B@2', 'C@3'])).toEqual(['C@6'])
```

Related Problems

[4. implement basic throttle() ](https://bigfrontend.dev/problem/implement-basic-throttle)

[5. implement throttle() with leading & trailing option ](https://bigfrontend.dev/problem/implement-throttle-with-leading-and-trailing-option)

[6. implement basic debounce() ](https://bigfrontend.dev/problem/implement-basic-debounce)

```js

/**
 * @param {Function} func
 * @param {number} wait
 * @param {boolean} option.leading
 * @param {boolean} option.trailing
 */
function debounce(func, wait, option = {leading: false, trailing: true}) {
  // your code here
  let timer = null;
  let nextArgs = null;

  return function debounced(...args) {
    if (option.leading && !timer) {
      func.apply(this, args);
    } else {
      clearTimeout(timer);
      nextArgs = args;
    }

    timer = setTimeout(() => {
      if (option.trailing && nextArgs) {
        func.apply(this, nextArgs);
      }
      timer = null;
    }, wait);
  }
}
```

### 8. can you shuffle() an array?

How would you implement a shuffle() ?

When passed with an array, it should modify the array inline to generate a randomly picked permutation at the same probability.

for an array like this:

```js
const arr = [1, 2, 3, 4]
```

there would be possibly 4! = 24 permutations

```js
[1, 2, 3, 4]
[1, 2, 4, 3]
[1, 3, 2, 4]
[1, 3, 4, 2]
[1, 4, 2, 3]
[1, 4, 3, 2]
[2, 1, 3, 4]
[2, 1, 4, 3]
[2, 3, 1, 4]
[2, 3, 4, 1]
[2, 4, 1, 3]
[2, 4, 3, 1]
[3, 1, 2, 4]
[3, 1, 4, 2]
[3, 2, 1, 4]
[3, 2, 4, 1]
[3, 4, 1, 2]
[3, 4, 2, 1]
[4, 1, 2, 3]
[4, 1, 3, 2]
[4, 2, 1, 3]
[4, 2, 3, 1]
[4, 3, 1, 2]
[4, 3, 2, 1]
```

your `shuffle()` should transform the array in one of the above array, at the same 1/24 probability.

*notes*

Your `shuffle()` will be called multiple times, to calculate the probability on each possible result, and test again [standard deviation](https://simple.wikipedia.org/wiki/Standard_deviation)

ref: https://javascript.info/task/shuffle

采用从后向前的顺序

```js

/**
  * @param {any[]} arr
  */
function shuffle(arr) {
  // modify the arr inline to change the order randomly
  const length = arr.length;
  for (let i = length - 1; i >= 0; i--) {
      let random = Math.floor((i + 1) * Math.random());
      [arr[i], arr[random]] = [arr[random], arr[i]];
  }
}
```

### 9. decode message

Your are given a 2-D array of characters. There is a hidden message in it.

```js
I B C A L K A
D R F C A E A
G H O E L A D 
```

The way to collect the message is as follows

1. start at top left
2. move diagonally down right
3. when cannot move any more, try to switch to diagonally up right
4. when cannot move any more, try switch to diagonally down right, repeat 3
5. stop when cannot neither move down right or up right. the character on the path is the message

for the input above, `IROCLED` should be returned.

*notes*

if no characters could be collected, return empty string

[Source for this ](https://www.glassdoor.com/Interview/Given-a-grid-of-characters-output-a-decoded-message-The-message-for-the-following-would-be-IROCKA-diagonally-down-right-QTN_970049.htm)

模拟碰到上下边界反弹

```js
/**
 * @param {string[][]} message
 * @return {string}
 */
function decode(message) {
  const rows = message.length;
  if (rows === 0) return "";
  const cols = message[0].length;
  if (cols === 0) return "";

  let res = "";
  let row = 0;
  let col = 0;
  let directionY = 1;

  while (col < cols && row > -1 && row < rows) {
    res += message[row][col];
    col += 1;

    row += directionY;
    if (row > rows - 1) {
      directionY = -1;
      row -= 2;
    } else if (row < 0) {
      directionY = 1;
      row += 2;
    }
  }

  return res;
}
```

### 10. first bad version

Say you have multiple versions of a program, write a program that will find and return the first bad revision given a `isBad(version)` function.

Versions after first bad version are supposed to be all bad versions.

*notes*

1. Inputs are all non-negative integers
2. if none found, return -1

[Source for this ](https://www.glassdoor.com/Interview/If-you-have-500-revisions-of-a-program-write-a-program-that-will-find-and-return-the-FIRST-bad-revision-given-a-isBad-revi-QTN_1255475.htm)

二分查找都搜索满足条件的下边界

```js
/*
 type TypIsBad = (version: number) => boolean
 */

/**
 * @param {TypIsBad} isBad 
 */
function firstBadVersion(isBad) {
	// firstBadVersion receive a check function isBad
  // and should return a closure which accepts a version number(integer)
  return (version) => {
    // write your code to return the first bad version
    // if none found, return -1
    let low = 0;
    let high = version;

    while (low <= high) {
      const mid = low + Math.floor((high - low) / 2);
      if (isBad(mid)) {
        high = mid - 1;
      } else {
        low = mid + 1;
      }
    }
    return low <= version ? low : -1;
  }
}
```

### 11. what is Composition? create a pipe()

what is Composition? It is actually not that difficult to understand, see [@dan_abramov 's explanation](https://whatthefuck.is/composition).

Here you are asked to create a `pipe()` function, which chains multiple functions together to create a new function.

Suppose we have some simple functions like this

```js
const times = (y) =>  (x) => x * y
const plus = (y) => (x) => x + y
const subtract = (y) =>  (x) => x - y
const divide = (y) => (x) => x / y
```

Your `pipe()` would be used to generate new functions

```js
pipe([
  times(2),
  times(3)
])  
// x * 2 * 3

pipe([
  times(2),
  plus(3),
  times(4)
]) 
// (x * 2 + 3) * 4

pipe([
  times(2),
  subtract(3),
  divide(4)
]) 
// (x * 2 - 3) / 4
```

**notes**

1. to make things simple, functions passed to `pipe()` will all accept 1 argument

Related Problems

[29. implement async helper - `sequence()`](https://bigfrontend.dev/problem/implement-async-helper-sequence)

管道化，正向使用reduce进行计算

```js
/**
 * @param {Array<(arg: any) => any>} funcs 
 * @return {(arg: any) => any}
 */
function pipe(funcs) {
	return (arg) => funcs.reduce((arg, func) => func(arg), arg);
}
```

### 12. implement Immutability helper

If you use React, you would meet the scenario to copy the state for a slight change.

For example, for following state

```js
const state = {
  a: {
    b: {
      c: 1
    }
  },
  d: 2
}
```

if we are to modify d to a new state, we could use [_.cloneDeep](https://lodash.com/docs/4.17.15#cloneDeep), but it is not efficient because `state.a` is cloned while we don't need to change that.

A better way is to do shallow copy like this

```js
const newState = {
  ...state,
  d: 3
}
```

now is the problem, if we want to modify `c`, we would have to do something like

```js
const newState = {
  ...state,
  a: {
    ...state.a,
    b: {
       ...state.b,
       c: 2
    }
  }
}
```

We can see that for simple data structure it would be enough to use spread operator, but for complex data structures, it is verbose.

Here comes the [Immutability Helper](https://reactjs.org/docs/update.html), you are asked to implement your own Immutability Helper `update()`, which supports following features.

#### 1. `{$push: array}` push() all the items in array on the target.

```js
const arr = [1, 2, 3, 4]
const newArr = update(arr, {$push: [5, 6]})
// [1, 2, 3, 4, 5, 6]
```

#### 2. {$set: any} replace the target

```js
const state = {
  a: {
    b: {
      c: 1
    }
  },
  d: 2
}

const newState = update(
  state, 
  {a: {b: { c: {$set: 3}}}}
)
/*
{
  a: {
    b: {
      c: 3
    }
  },
  d: 2
}
*/
```

Notice that we could also update array elements with `$set`

```js
const arr = [1, 2, 3, 4]
const newArr = update(
  arr, 
  {0: {$set: 0}}
)
//  [0, 2, 3, 4]
```

#### 3. {$merge: object} merge object to the location

```js
const state = {
  a: {
    b: {
      c: 1
    }
  },
  d: 2
}

const newState = update(
  state, 
  {a: {b: { $merge: {e: 5}}}}
)
/*
{
  a: {
    b: {
      c: 1,
      e: 5
    }
  },
  d: 2
}
*/
```

#### 4. {$apply: function} custom replacer

```js
const arr = [1, 2, 3, 4]
const newArr = update(arr, {0: {$apply: (item) => item * 2}})
// [2, 2, 3, 4]
```

根据键名分别进行相应的函数处理

```js
const isObject = (data) => {
  return typeof data === 'object' && data !== null;
}
/**
 * @param {any} data
 * @param {Object} command
 */
function update(data, command) {
  // your code here
  if ('$push' in command) {
    if (!Array.isArray(data)) {
      throw new Error('not array');
    }

    return [
      ...data,
      ...command['$push']
    ];
  }

  if ('$merge' in command) {
    if (!isObject(data)) {
      throw new Error('not object for $merge');
    }

    return {
      ...data,
      ...command['$merge']
    };
  }

  if ('$apply' in command) {
    return command['$apply'](data);
  }

  if ('$set' in command) {
    return command['$set'];
  }

  if (!isObject(data)) {
    throw new Error('not object for complex data');
  }

  const newData = Array.isArray(data) ? [...data] : {...data};
  for (const key of Object.keys(command)) {
    newData[key] = update(newData[key], command[key]);
  }
  return newData;
}
```

### 13. Implement a Queue by using Stack

In JavaScript, we could use array to work as both a Stack or a queue.

```js
const arr = [1, 2, 3, 4]

arr.push(5) // now array is [1, 2, 3, 4, 5]
arr.pop() // 5, now the array is [1, 2, 3, 4]
```

Above code is a Stack, while below is a Queue

```js
const arr = [1, 2, 3, 4]

arr.push(5) // now the array is [1, 2, 3, 4, 5]
arr.shift() // 1, now the array is [2, 3, 4, 5]
```

now suppose you have a stack, which has only follow interface:

```js
class Stack {
  push(element) { /* add element to stack */ }
  peek() { /* get the top element */ }
  pop() { /* remove the top element */}
  size() { /* count of elements */}
}
```

Could you implement a Queue by using only above Stack? A Queue must have following interface

```js
class Queue {
  enqueue(element) { /* add element to queue, similar to Array.prototype.push */ }
  peek() { /* get the head element*/ }
  dequeue() { /* remove the head element, similar to Array.prototype.pop */ }
  size() { /* count of elements */ }
}
```

*note*

you can only use Stack as provided, Array should be avoided for the purpose of practicing.

Related Problems

[108. Implement a Stack by using Queue](https://bigfrontend.dev/problem/Implement-a-Stack-by-using-Queue)

最后的代码使用了2个栈

```js
/* you can use this Class which is bundled together with your code

class Stack {
  push(element) { // add element to stack }
  peek() { // get the top element }
  pop() { // remove the top element}
  size() { // count of element }
}
*/

/* Array is disabled in your code */

// you need to complete the following Class
class Queue {
  constructor () {
    this.stack1 = new Stack();
    this.stack2 = new Stack();
  }
  enqueue(element) { 
    // add new element to the rare
    this.stack1.push(element);
  }
  peek() { 
    // get the head element
    if (this.stack2.size() > 0) {
      return this.stack2.peek();
    }
    while(this.stack1.size() > 0){
        this.stack2.push(this.stack1.pop());
    }
    return this.stack2.peek();
  }
  size() { 
    // return count of element
    return this.stack1.size() + this.stack2.size();
  }
  dequeue() {
    // remove the head element
    if (this.stack2.size() > 0) {
      return this.stack2.pop();
    }
    while(this.stack1.size() > 0){
        this.stack2.push(this.stack1.pop());
    }
    return this.stack2.pop();
  }
}
```

### 14. Implement a general memoization function - `memo()`

[Memoization](https://whatthefuck.is/memoization) is a common technique to boost performance. If you use React, you definitely have used `React.memo` before.

Memoization is also commonly used in algorithm problem, when you have a recursion solution, in most cases, you can improve it by memoization, and then you might be able to get a Dynamic Programming approach.

So could you implement a general `memo()` function, which caches the result once called, so when same arguments are passed in, the result will be returned right away.

```js
const func = (arg1, arg2) => {
  return arg1 + arg2
}

const memoed = memo(func)

memoed(1, 2) 
// 3, func is called

memoed(1, 2) 
// 3 is returned right away without calling func

memoed(1, 3)
// 4, new arguments, so func is called
```

The arguments are arbitrary, so memo should accept an extra resolver parameter, which is used to generate the cache key, like what [_.memoize()](https://lodash.com/docs/4.17.15#memoize) does.

```js
const memoed = memo(func, () => 'samekey')

memoed(1, 2) 
// 3, func is called, 3 is cached with key 'samekey'

memoed(1, 2) 
// 3, since key is the same, 3 is returned without calling func

memoed(1, 3) 
// 3, since key is the same, 3 is returned without calling func
```

Default cache key could be just `Array.from(arguments).join('_')`

*note*

It is a trade-off of space for time, so if you use this in an interview, please do analyze how much space it might cost.

[Source for this ](https://whatthefuck.is/memoization)

Related Problems

[122. implement memoizeOne()](https://bigfrontend.dev/problem/implement-memoizeOne)

使用了map+闭包保存之前计算的数据

```js
/**
 * @param {Function} func
 * @param {(args:[]) => string }  [resolver] - cache key generator
 */
function memo(func, resolver) {
  // your code here
  const cache = new Map();

  return function (...args) {
    const key = resolver ? resolver(...args) : args.join('_');
    if (cache.has(key)) {
      return cache.get(key);
    }

    const result = func.apply(this, args);
    cache.set(key, result);
    return result;
  }
}
```

### 15. implement a simple DOM wrapper to support method chaining like jQuery

I believe you've used jQuery before, we often chain the jQuery methods together to accomplish our goals.

For example, below chained call turns button into a black button with white text.

```js
$('#button')
  .css('color', '#fff')
  .css('backgroundColor', '#000')
  .css('fontWeight', 'bold')
```

The chaining makes the code simple to read, could you create a simple wrapper `$` to make above code work as expected?

The wrapper only needs to have `css(propertyName: string, value: any)`

通过返回的闭包设置参数，返回this实现链式调用

```js

/**
 * @param {HTMLElement} el - element to be wrapped
 */
function $(el) {
  // your code here
  return {
    css: function(property, value) {
      el.style[property] = value;
      return this;
    }
  }
}
```

### 16. create an Event Emitter

There is [Event Emitter in Node.js](https://nodejs.org/api/events.html#events_class_eventemitter), Facebook once had [its own implementation](https://github.com/facebookarchive/emitter) but now it is archived.

You are asked to create an Event Emitter Class

```js
const emitter = new Emitter()
```

It should support event subscribing

```js
const sub1  = emitter.subscribe('event1', callback1)
const sub2 = emitter.subscribe('event2', callback2)

// same callback could subscribe 
// on same event multiple times
const sub3 = emitter.subscribe('event1', callback1)
```

`emit(eventName, ...args)` is used to trigger the callbacks, with args relayed

```js
emitter.emit('event1', 1, 2);
// callback1 will be called twice
```

Subscription returned by `subscribe()` has a `release()` method that could be used to unsubscribe

```js
sub1.release()
sub3.release()
// now even if we emit 'event1' again, 
// callback1 is not called anymore
```

[Source for this ](https://www.glassdoor.com/Interview/Flatten-Array-Create-Emitter-QTN_2559028.htm)

发布订阅模式的简化版，subscribe订阅事件，emit发布事件

```js

// please complete the implementation
class EventEmitter {
  constructor () {
    this.callbacks = new Map();
  }
  subscribe(eventName, callback) {
  	if (this.callbacks.has(eventName)) {
      const callbacks = this.callbacks.get(eventName);
      callbacks.push(callback)
    } else {
      this.callbacks.set(eventName, [callback]);
    }

    return {
      release: () => {
        const callbacks = this.callbacks.get(eventName);
        const index = callbacks.indexOf(callback);
        callbacks.splice(index, 1);
      }
    }
  }
  
  emit(eventName, ...args) {
  	const callbacks = this.callbacks.get(eventName);
    if (!callbacks.length) return;

    for (const callback of callbacks) {
      callback.apply(this, args);
    }
  }
}
```

### 17. Create a simple store for DOM element

We have `Map` in es6, so we could use any data as key, such as DOM element.

```js
const map = new Map()
map.set(domNode, somedata)
```

What if we need to support old JavaScript env like es5, how would you create your own Node Store as above?

You are asked to implement a Node Store, which supports DOM element as key.

```js
class NodeStore {

  set(node, value) {

  }
  
  get(node) {

  }
  
  has(node) {

  }
}
```

*note*

Map is disabled when judging your code, it is against the goal of practicing.

You can create a simple general Map polyfill. Or since you are asked to support specially for DOM element, what is special about DOM element?

What is the Time / Space cost of your solution?

[Source for this ](https://www.glassdoor.com/Interview/Implement-a-simple-store-class-with-set-Node-value-get-Node-and-has-Node-methods-which-store-a-given-Nodes-with-corre-QTN_2435822.htm)

使用对象存储节点

```js
class NodeStore {
  constructor() {
    this.nodes = {};
  }
   /**
   * @param {Node} node
   * @param {any} value
   */
  set(node, value) {
   node.__id__ = Symbol();
   this.nodes[node.__id__] = value;
  }
  /**
   * @param {Node} node
   * @return {any}
   */
  get(node) {
   return this.nodes[node.__id__];
  }
  
  /**
   * @param {Node} node
   * @return {Boolean}
   */
  has(node) {
    return !!this.nodes[node.__id__];
  }
}
```

### 18. Improve a function

```js
// Given an input of array, 
// which is made of items with >= 3 properties

let items = [
  {color: 'red', type: 'tv', age: 18}, 
  {color: 'silver', type: 'phone', age: 20},
  {color: 'blue', type: 'book', age: 17}
] 

// an exclude array made of key value pair
const excludes = [ 
  {k: 'color', v: 'silver'}, 
  {k: 'type', v: 'tv'}, 
  ...
] 

function excludeItems(items, excludes) { 
  excludes.forEach( pair => { 
    items = items.filter(item => item[pair.k] === item[pair.v])
  })
 
  return items
} 
```

1. What does this function `excludeItems` do?
2. Is this function working as expected ?
3. What is the time complexity of this function?
4. How would you optimize it ?

*note*

we only judge by the result, not the time cost. please submit the best approach you can.

[Source for this ](https://www.glassdoor.com/Interview/Given-input-could-be-potentially-more-than-3-keys-in-the-object-above-items-color-red-type-tv-age-18-QTN_2372314.htm)

建立map，过滤需要进行过滤的key

```js

/**
 * @param {object[]} items
 * @excludes { Array< {k: string, v: any} >} excludes
 */

/**
 * @param {object[]} items
 * @param { Array< {k: string, v: any} >} excludes
 * @return {object[]}
 */
function excludeItems(items, excludes) {
  const excludesMap = new Map();
  for (const {k, v} of excludes) {
    if (!excludesMap.has(k)) {
      excludesMap.set(k, new Set());
    }
    excludesMap.get(k).add(v);
  }

  return items.filter(item => {
    return Object.keys(item).every(
      key => !excludesMap.has(key) || !excludesMap.get(key).has(item[key])
    )
  })
}
```

### 19. find corresponding node in two identical DOM tree

Given two same DOM tree **A**, **B**, and an Element **a** in **A**, find the corresponding Element **b** in **B**.

By **corresponding**, we mean **a** and **b** have the same relative position to their DOM tree root.

*follow up*

This could a problem on general Tree structure with only `children`.

Could you solve it recursively and iteratively?

Could you solve this problem with special DOM api for better performance?

What are the time cost for each solution?

[Source for this ](https://www.glassdoor.com/Interview/Given-two-identical-DOM-trees-not-the-same-one-and-a-node-from-one-of-them-find-the-node-in-the-other-one-QTN_2435821.htm)

Related Problems

[58. get DOM tree height](https://bigfrontend.dev/problem/get-DOM-tree-height)

使用迭代法进行操作，用队列保存层级节点

```js

/**
 * @param {HTMLElement} rootA
 * @param {HTMLElement} rootB - rootA and rootB are clone of each other
 * @param {HTMLElement} nodeA
 */
const findCorrespondingNode = (rootA, rootB, target) => {
  // your code here
  let stackA = [rootA];
  let stackB = [rootB];

  while (stackA.length > 0) {
    const nodeA = stackA.pop();
    const nodeB = stackB.pop();

    if (nodeA === target) {
      return nodeB;
    }

    stackA.push(...nodeA.childNodes);
    stackB.push(...nodeB.childNodes);
  }

  return undefined;
}
```

### 20. Detect data type in JavaScript

This is an easy problem.

For [all the basic data types](https://javascript.info/types) in JavaScript, how could you write a function to detect the type of arbitrary data?

Besides basic types, you need to also handle also commonly used complex data type including `Array`, `ArrayBuffer`, `Map`, `Set`, `Date` and `Function`

The goal is not to list up all the data types but to show us how to solve the problem when we need to.

The type should be lowercase

```js
detectType(1) // 'number'
detectType(new Map()) // 'map'
detectType([]) // 'array'
detectType(null) // 'null'

// more in judging step
```

除了常规的判断，测试用例中有一个FileReader需要返回`object`

```js

/**
 * @param {any} data
 * @return {string}
 */
function detectType(data) {
  // your code here
  if (data === null) return 'null';
  if (typeof data !== 'object') return typeof data;
  if (data instanceof FileReader) return 'object';
  return Object.prototype.toString.call(data).replace('[object ', '').replace(']', '').toLowerCase();
}
```

### 21. implement JSON.stringify()

I believe you've used `JSON.stringify()` before, do you know the details of how it handles arbitrary data?

Please have a guess on the details and then take a look at the [explanation on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify), it is actually pretty complex.

In this problem, you are asked to implement your own version of `JSON.stringify()`.

In a real interview, you are not expected to cover all the cases, just decide the scope with interviewer. But for a goal of practicing, your code here will be tested against a lot of data types. Please try to cover as much as you can.

Attention to the circular reference.

*note*

`JSON.stringify()` support two more parameters which is not required here.

**Don't use JSON.stringify() in your code here**, it doesn't help you practicing coding skills.

Related Problems

[22. implement JSON.parse()](https://bigfrontend.dev/problem/implement-JSON-parse)

分类进行判断，注意不能枚举的键

```js

/**
 * @param {any} data
 * @return {string}
 */
function stringify(data) {
  // your code here
  let type = typeof data;
  if (type === 'bigint') {
    throw new Error('Do not know how to serialize a BigInt at JSON.stringify');
  }
  if (/undefined|symbol/.test(type) || data === null || Object.is(data, NaN) || data === Infinity || data === -Infinity) {
    return 'null';
  }
  if (type === 'string') {
    return `"${data}"`;
  }
  if (type === 'function') {
    return undefined;
  }
  if (type !== 'object') {  
    return `${data}`;
  }
  if(data instanceof Date) {
    return `"${data.toISOString()}"`;
  }
  if(Array.isArray(data)) {
    // 递归调用
    const arr = data.map((el) => stringify(el));
    return `[${arr.join(',')}]`;
  }
      
  const arr = Object.entries(data).reduce((acc, [key, value]) => {
    if(value === undefined) {
      return acc;
    }
    acc.push(`"${key}":${stringify(value)}`);
    return acc;
  }, []);
  return `{${arr.join(',')}}`;
}
```

### 22. implement JSON.parse()

I believe you've used `JSON.stringify()` before, do you know the details of how it handles arbitrary data?

Please have a guess on the details and then take a look at the [explanation on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify), it is actually pretty complex.

In this problem, you are asked to implement your own version of `JSON.stringify()`.

In a real interview, you are not expected to cover all the cases, just decide the scope with interviewer. But for a goal of practicing, your code here will be tested against a lot of data types. Please try to cover as much as you can.

Attention to the circular reference.

*note*

**Don't use JSON.parse() in your code here** It doesn't help you practicing your skills.

Related Problems

[21. implement JSON.stringify() ](https://bigfrontend.dev/problem/implement-JSON-stringify)

使用eval，注意有可能失败，重新JSON.stringify()转化后不同于自身

```js
/**
 * @param {string} str
 * @return {object | Array | string | number | boolean | null}
 */
function parse(str) {
  // your code here
  const parsed = eval('(' + str + ')');
  if (str !== JSON.stringify(parsed)) {
    throw new Error();
  }
  return parsed;
}
```

使用递归写法

```js
/**
 * @param {string} str
 * @return {object | Array | string | number | boolean | null}
 */
function parse(str) {
  const dataType = detectDataType(str);

  if (dataType === 'object') {
    return parseObj(str);
  }

  if (dataType === 'array') {
    return parseArr(str);
  }

  return parsePrimitive(str);
}

// 检查类型
function detectDataType(str) {
  if (str.startsWith('{') && str.endsWith('}')) {
    return 'object';
  }

  if (str.startsWith('[') && str.endsWith(']')) {
    return 'array';
  }

  return 'primitive';
}

function parseObj(str) {
  const obj = {};

  str = removeQuotationMarks(str);
  if (str.endsWith(':')) {
    throw new Error();
  }

  while (str.length > 0) {
    const regex = /^".+?"(?=:)/;
    let key = str.match(regex);
    if (!key) {
      throw new Error();
    }
    key = key[0];

    const rest = str.slice(key.length + 1);
    let val = rest.match(/\w+(?=,"\w+":)/);
    if (!val) {
      val = rest;
    } else {
      val = val[0];
    }

    const trimmedKey = removeQuotationMarks(key);
    obj[trimmedKey] = parse(val);

    str = rest.slice(val.length);
  }

  return obj;
}

function parseArr(str) {
  const arr = [];

  str = removeQuotationMarks(str);
  if (str.endsWith(',')) {
    throw new Error();
  }

  while (str.length > 0) {
    let item = str.match(/.+?,(?!"\w+":)/);
    if (!item) {
      item = str;
    } else {
      item = item[0].slice(0, -1);
    }
    arr.push(parse(item));

    str = str.slice(item.length + 1);
  }

  return arr;
}

function parsePrimitive(str) {
  if (str.startsWith('"')) {
    return removeQuotationMarks(str);
  }

  if (!isNaN(str)) {
    return Number(str);
  }

  if (str === 'true') {
    return true;
  }

  if (str === 'false') {
    return false;
  }

  if (str === 'undefined') {
    return undefined;
  }

  return null;
}

function removeQuotationMarks(str) {
  return str.slice(1, -1);
}
```



### 23. create a sum()

Create a `sum()`, which makes following possible

```js
const sum1 = sum(1)
sum1(2) == 3 // true
sum1(3) == 4 // true
sum(1)(2)(3) == 6 // true
sum(5)(-1)(2) == 6 // true
```

函数柯里化+valueOf求值

```js
/**
 * @param {number} num
 */
function sum(num) {
  // your code here
  const add = newNum => newNum ? sum(num + newNum) : num;
  add.valueOf = () => num;
  return add;
}
```

### 24. create a sum()

[Priority Queue](https://storm.cis.fordham.edu/~yli/documents/CISC2200Spring15/Graph.pdf) is a commonly used data structure in algorithm problem. Especially useful for **Top K** problem with a huge amount of input data, since it could avoid sorting the whole but keep a fixed-length sorted portion of it.

Since there is no built-in Priority Queue in JavaScript, in a real interview, you should tell interview saying that "Suppose we already have a Priority Queue Class I can use", there is no time for you to write a Priority Queue from scratch.

But it is a good coding problem to practice, so please implement a Priority Queue with following interface

```js
class PriorityQueue {
  // compare is a function defines the priority, which is the order
  // the elements closer to first element is sooner to be removed.
  constructor(compare) {
  
  }
  
  // add a new element to the queue
  // you need to put it in the right order
  add(element) {

  }

  // remove the head element and return
  poll() {
  
  }

  // get the head element
  peek() {

  }

  // get the amount of items in the queue
  size() {

  }
}
```

Here is an example to make it clearer

```js
const pq = new PriorityQueue((a, b) => a - b)
// (a, b) => a - b means
// smaller numbers are closer to index:0
// which means smaller number are to be removed sooner

pq.add(5)
// now 5 is the only element

pq.add(2)
// 2 added

pq.add(1)
// 1 added

pq.peek()
// since smaller number are sooner to be removed
// so this gives us 1

pq.poll()
// 1 
// 1 is removed, 2 and 5 are left

pq.peek()
// 2 is the smallest now, this returns 2

pq.poll()
// 2 
// 2 is removed, only 5 is left
```

实现堆，这个直接抄了，感觉比较复杂，主要是上移和下移操作

```js

// complete the implementation
class PriorityQueue {
  heap = [];
  /**
   * @param {(a: any, b: any) => -1 | 0 | 1} compare - 
   * compare function, similar to parameter of Array.prototype.sort
   */
  constructor(compare) {
    this.compare = compare;
  }

  /**
   * return {number} amount of items
   */
  size() {
    return this.heap.length;
  }

  /**
   * returns the head element
   */
  peek() {
    return this.heap.length ? this.heap[0] : undefined;
  }

  /**
   * @param {any} element - new element to add
   */
  add(element) {
    this.heap.push(element);
    const heapSize = this.size();
    if (heapSize > 1) {
      this.heapifyUp(heapSize - 1);
    }
  }

  /**
   * remove the head element
   * @return {any} the head element
   */
  poll() {
    const heapSize = this.size();
    if (heapSize <= 1) {
      return this.heap.pop();
    }

    this.swap(0, heapSize - 1);
    const head = this.heap.pop();
    this.heapifyDown(0);
    return head;
  }

  heapifyUp(index) {
    const parentIndex = Math.floor((index - 1) / 2);

    if (parentIndex < 0) {
      return;
    }

    const comparison = this.compare(this.heap[index], this.heap[parentIndex]);
    if (comparison < 0) {
      this.swap(index, parentIndex);
      this.heapifyUp(parentIndex);
    }
  }

  heapifyDown(parentIndex) {
    const leftIndex = 2 * parentIndex + 1;
    const rightIndex = 2 * parentIndex + 2;
    let swappableIndex = parentIndex;
    const heapSize = this.size();
    let comparison = 0;

    if (leftIndex < heapSize) {
      comparison = this.compare(
        this.heap[leftIndex],
        this.heap[swappableIndex]
      );
      if (comparison < 0) {
        swappableIndex = leftIndex;
      }
    }

    if (rightIndex < heapSize) {
      comparison = this.compare(
        this.heap[rightIndex],
        this.heap[swappableIndex]
      );
      if (comparison < 0) {
        swappableIndex = rightIndex;
      }
    }

    if (swappableIndex !== parentIndex) {
      this.swap(parentIndex, swappableIndex);
      this.heapifyDown(swappableIndex);
    }
  }

  swap(i, j) {
    [this.heap[i], this.heap[j]] = [this.heap[j], this.heap[i]];
  }
}
```

### 25. Reorder array with new indexes

Suppose we have an array of items - `A`, and another array of indexes in numbers - `B`

```js
const A = ['A', 'B', 'C', 'D', 'E', 'F']
const B = [1,   5,   4,   3,   2,   0]
```

You need to reorder A, so that the A[i] is put at index of B[i], which means B is the new index for each item of A.

For above example A should be modified inline to following

```js
['F', 'A', 'E', 'D', 'C', 'B']
```

The input are always valid.

*follow-up*

It is fairly easy to do this by using extra `O(n)` space, could you solve it with `O(1)` space?

[Source for this ](https://www.glassdoor.com/Interview/Given-an-input-array-and-another-array-that-describes-a-new-index-for-each-element-mutate-the-input-array-so-that-each-ele-QTN_446534.htm)

一次遍历重新排序索引和数据，保证当前位置是正确的数

```js
/**
 * @param {any[]} items
 * @param {number[]} newOrder
 * @return {void}
 */
function sort(items, newOrder) {
  // reorder items inline
  for (let i = 0; i < newOrder.length; i++) {
    const newIndex = newOrder[i];
    if (newIndex !== i) {
      swap(newOrder, newIndex, i);
      swap(items, newIndex, i);
    }
  }
}

function swap(arr, i, j) {
  [arr[i], arr[j]] = [arr[j], arr[i]];
}
```

### 26. implement Object.assign()

*The `Object.assign()` method copies all enumerable own properties from one or more source objects to a target object. It returns the target object.* (source: [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign))

It is widely used, Object Spread operator actually is internally the same as `Object.assign()` ([source](https://github.com/tc39/proposal-object-rest-spread/blob/master/Spread.md)). Following 2 lines of code are totally the same.

```js
let aClone = { ...a };
let aClone = Object.assign({}, a);
```

This is an easy one, could you implement `Object.assign()` with your own implementation ?

*note*

**Don't use Object.assign() in your code** It doesn't help improve your skills

Related Problems

[27. implement completeAssign()](https://bigfrontend.dev/problem/implement-completeAssign)

```js
function completeAssign(target, ...sources) {
  // your code here
  if (!target) {
    throw new Error();
  }

  if (typeof target !== 'object') {
    const constructor = Object.getPrototypeOf(target).constructor;
    target = new constructor(target);
  }

  for(const source of sources) {
    if (!source) {
      continue;
    }

    Object.defineProperties(target, Object.getOwnPropertyDescriptors(source));
    
    for (const symbol of Object.getOwnPropertySymbols(source)) {
      target[symbol] = source[symbol];
    }
  }

  return target;
}
```

### 27. implement completeAssign()

This is a follow-up on [26. implement Object.assign()](https://bigfrontend.dev/problem/implement-object-assign).

`Object.assign()` assigns the enumerable properties, so getters are not copied, non-enumerable properties are ignored.

Suppose we have following source object.

```js
const source = Object.create(
  {
    a: 3 // prototype
  },
  {
    b: {
      value: 4,
      enumerable: true // enumerable data descriptor
    },
    c: {
      value: 5, // non-enumerable data descriptor
    },
    d: { // non-enumerable accessor descriptor 
      get: function() {
        return this._d;
      },
      set: function(value) {
        this._d = value
      }
    },
    e: { // enumerable accessor descriptor 
      get: function() {
        return this._e;
      },
      set: function(value) {
        this._e = value
      },
      enumerable: true
    }
  }
)
```

If we call `Object.assign()` with source of above, we get:

```js
Object.assign({}, source)

// {b: 4, e: undefined}
// e is undefined because `this._e` is undefined
```

Rather than above result, could you implement a `completeAssign()` which have the same parameters as `Object.assign()` but fully copies the data descriptors and accessor descriptors?

In case you are not familiar with the descriptors, [this page from MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) might help.

This problem is solely checking your understanding of how property descriptors work.

Good luck

Related Problems

[26. implement Object.assign() ](https://bigfrontend.dev/problem/implement-object-assign)

类似的做法

```js
function completeAssign(target, ...sources) {
  // your code here
  if (target === null || target === undefined) throw Error('target cannot be null or undefined');

  target = Object(target);

  for(const source of sources) {
    if (source === null || target === undefined) {
      continue;
    }

    Object.defineProperties(target, Object.getOwnPropertyDescriptors(source));
    
    for (const symbol of Object.getOwnPropertySymbols(source)) {
      target[symbol] = source[symbol];
    }
  }

  return target;
}
```

### 28. implement clearAllTimeout()

`window.setTimeout()` could be used to schedule some task in the future.

Could you implement `clearAllTimeout()` to clear all the timers ? This might be useful when we want to clear things up before page transition.

For example

```js
setTimeout(func1, 10000)
setTimeout(func2, 10000)
setTimeout(func3, 10000)

// all 3 functions are scheduled 10 seconds later
clearAllTimeout()

// all scheduled tasks are cancelled.
```

*note*

You need to keep the interface of `window.setTimeout` and `window.clearTimeout` the same, but you could replace them with new logic

[Source for this ](https://www.glassdoor.com/Interview/by-phone-what-is-a-difference-between-Object-and-Array-call-and-apply-what-is-DOM-structure-what-is-time-complexity-of-QTN_2882315.htm)

```js
/**
 * cancel all timer from window.setTimeout
 */
const timerCache = new Set();

// 包装setTimeout
const originalSetTimeout = window.setTimeout;

window.setTimeout = (cb, delay) => {
  const timer = originalSetTimeout(cb, delay);
  timerCache.add(timer);
  return timer;
}

function clearAllTimeout() {
  // your code here
  for (const timer of timerCache) {
    clearTimeout(timer);
  }
}
```

### 29. implement async helper - `sequence()`

This problem is similar to [11. what is Composition? create a pipe()](https://bigfrontend.dev/problem/what-is-composition-create-a-pipe).

You are asked to implement an async function helper, `sequence()` which chains up async functions, like what `pipe()` does.

All async functions have following interface

```ts
type Callback = (error: Error, data: any) => void

type AsyncFunc = (
   callback: Callback,
   data: any
) => void
```

Your `sequence()` should **accept AsyncFunc array**, and **chain them up by passing new data to the next AsyncFunc through data in Callback.**

Suppose we have an async func which just multiple a number by 2

```js
const asyncTimes2 = (callback, num) => {
   setTimeout(() => callback(null, num * 2), 100)
}
```

Your `sequence()` should be able to accomplish this

```js
const asyncTimes4 = sequence(
  [
    asyncTimes2,
    asyncTimes2
  ]
)

asyncTimes4((error, data) => {
   console.log(data) // 4
}, 1)
```

Once an error occurs, it should trigger the last callback without triggering the uncalled functions.

**Follow up**

Can you solve it with and without Promise?

Related Problems

[11. what is Composition? create a pipe() ](https://bigfrontend.dev/problem/what-is-composition-create-a-pipe)

[30. implement async helper - `parallel()`](https://bigfrontend.dev/problem/implement-async-helper-parallel)

[async helper - `race()`](https://bigfrontend.dev/problem/implement-async-helper-race)

使用promise实现

```js
/*
type Callback = (error: Error, data: any) => void

type AsyncFunc = (
   callback: Callback,
   data: any
) => void

*/

/**
 * @param {AsyncFunc[]} funcs
 * @return {(callback: Callback) => void}
 */
function sequence(funcs){
  // your code here
  const promiseFuncs = funcs.map(promisify);

  return function (callback, input) {
    // init promise
    let promise = Promise.resolve(input);

    // add all promiseFuncs to promise
    promiseFuncs.forEach((promiseFunc) => {
      promise = promise.then(promiseFunc);
    });

    // handle resolved or rejected promise
    promise.then((data) => {
      callback(undefined, data)
    }).catch(callback);
  }
}

function promisify(callback) {
  return function (...args) {
    return new Promise((resolve, reject) => {
      callback((err, data) => {
        if (err) {
          reject(err);
          return
        }

        resolve(data);
      }, ...args);
    })
  }
}
```

不使用promise实现

```js

/*
type Callback = (error: Error, data: any) => void

type AsyncFunc = (
   callback: Callback,
   data: any
) => void

*/

/**
 * @param {AsyncFunc[]} funcs
 * @return {(callback: Callback) => void}
 */
function sequence(funcs) {
  let i = 0;

  const sequenced = (finalCallback, input) => {
    if (i === funcs.length) {
      finalCallback(undefined, input);
      return;
    }

    const func = funcs[i];
    func((err, data) => {
      if (err) {
        finalCallback(err);
        return;
      }
      i++;
      sequenced(finalCallback, data);
    }, input);
  };
  return sequenced;
}
```

### 30. implement async helper - `parallel()`

This problem is related to [29. implement async helper - `sequence()`](https://bigfrontend.dev/problem/implement-async-helper-sequence).

You are asked to implement an async function helper, `parallel()` which works like `Promise.all()`. Different from `sequence()`, the async function doesn't wait for each other, rather they are all triggered together.

All async functions have following interface

```ts
type Callback = (error: Error, data: any) => void

type AsyncFunc = (
   callback: Callback,
   data: any
) => void
```

Your `parallel()` should **accept AsyncFunc array**, and return a new function which triggers its own callback when all async functions are done or an error occurs.

Suppose we have an 3 async functions

```js
const async1 = (callback) => {
   callback(undefined, 1)
}

const async2 = (callback) => {
   callback(undefined, 2)
}

const async3 = (callback) => {
   callback(undefined, 3)
}
```

Your `parallel()` should be able to accomplish this

```js
const all = parallel(
  [
    async1,
    async2,
    async3
  ]
)

all((error, data) => {
   console.log(data) // [1, 2, 3]
}, 1)
```

When error occurs, only first error is passed down to the last. Later errors or data are ignored.

Related Problems

[29. implement async helper - `sequence()` ](https://bigfrontend.dev/problem/implement-async-helper-sequence)

[31. implement async helper - `race()`](https://bigfrontend.dev/problem/implement-async-helper-race)

promisify+promise.all

```js

/*
type Callback = (error: Error, data: any) => void

type AsyncFunc = (
   callback: Callback,
   data: any
) => void

*/

/**
 * @param {AsyncFunc[]} funcs
 * @return {(callback: Callback) => void}
 */
function parallel(funcs){
  // your code here
  return function(callback, ...args) {
    Promise.all(funcs.map(func => promisify(func)(...args)))
    .then((res) => callback(undefined, res))
    .catch((err) => callback(err, undefined));
  }
}

function promisify(callback) {
  return function (...args) {
    return new Promise((resolve, reject) => {
      callback((err, data) => {
        if (err) {
          reject(err);
          return
        }

        resolve(data);
      }, ...args);
    })
  }
}
```

不使用的话其实就是参考了Promise.all的原理

```js

/*
type Callback = (error: Error, data: any) => void

type AsyncFunc = (
   callback: Callback,
   data: any
) => void

*/

/**
 * @param {AsyncFunc[]} funcs
 * @return {(callback: Callback) => void}
 */
function parallel(funcs){
  // your code here
  return (finalCallback, input) => {
    const results = Array(funcs.length);
    let count = 0;
    let hasError = false;

    funcs.forEach((func, i) => {
      func((err, data) => {
        if (hasError) {
          return;
        }

        if (err) {
          hasError = true;
          finalCallback(err, undefined);
          return;
        }

        results[i] = data;
        count++;

        if (count === funcs.length) {
          finalCallback(undefined, results);
        }
      }, input);
    });
  };
}
```

### 31. implement async helper - `race()`

This problem is related to [30. implement async helper - `parallel()`](https://bigfrontend.dev/problem/implement-async-helper-parallel).

You are asked to implement an async function helper, `race()` which works like [Promise.race()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race). Different from `parallel()` that waits for all functions to finish, `race()` will finish when any function is done or run into error.

All async functions have following interface

```ts
type Callback = (error: Error, data: any) => void

type AsyncFunc = (
   callback: Callback,
   data: any
) => void
```

Your `race()` should **accept AsyncFunc array**, and return a new function which triggers its own callback when **any** async function is done or an error occurs.

Suppose we have an 3 async functions

```js
const async1 = (callback) => {
   setTimeout(() => callback(undefined, 1), 300)
}

const async2 = (callback) => {
    setTimeout(() => callback(undefined, 2), 100)
}

const async3 = (callback) => {
   setTimeout(() => callback(undefined, 3), 200)
}
```

Your `race()` should be able to accomplish this

```js
const first = race(
  [
    async1,
    async2,
    async3
  ]
)

first((error, data) => {
   console.log(data) // 2, since 2 is the first to be given
}, 1)
```

When error occurs, only first error is passed down to the last. Later errors or data are ignored.

Related Problems

[29. implement async helper - `sequence()` ](https://bigfrontend.dev/problem/implement-async-helper-sequence)
[30. implement async helper - `parallel()` ](https://bigfrontend.dev/problem/implement-async-helper-parallel)

使用Promise.race

```js
/*
type Callback = (error: Error, data: any) => void

type AsyncFunc = (
   callback: Callback,
   data: any
) => void

*/

/**
 * @param {AsyncFunc[]} funcs
 * @return {(callback: Callback) => void}
 */
function race(funcs){
  // your code here
  return function(callback, ...args) {
    Promise.race(funcs.map(func => promisify(func)(...args)))
    .then((res) => callback(undefined, res))
    .catch((err) => callback(err, undefined));
  }
}

function promisify(callback) {
  return function (...args) {
    return new Promise((resolve, reject) => {
      callback((err, data) => {
        if (err) {
          reject(err);
          return
        }

        resolve(data);
      }, ...args);
    })
  }
}
```

直接使用其逻辑

```js
/*
type Callback = (error: Error, data: any) => void

type AsyncFunc = (
   callback: Callback,
   data: any
) => void

*/

/**
 * @param {AsyncFunc[]} funcs
 * @return {(callback: Callback) => void}
 */
function race(funcs){
  // your code here
  return (finalCallback, input) => {
    let isExecuted = false;
    for (const func of funcs) {
      func((err, data) => {
        if (!isExecuted) {
          finalCallback(err, data);
          isExecuted = true;
        }
      }, input);
    }
  }
}
```

### 32. implement `Promise.all()`

> The Promise.all() method takes an iterable of promises as an input, and returns a single Promise that resolves to an array of the results of the input promises

source - [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)

Could you write your own `all()` ? which should works the same as `Promise.all()`

*note*

**Do not use Promise.all() directly**, it is not helping

Related Problems

[30. implement async helper - `parallel()`](https://bigfrontend.dev/problem/implement-async-helper-parallel)plement `Promise.allSettled()`](https://bigfrontend.dev/problem/implement-Promise-allSettled)[34. implement `Promise.any()`](https://bigfrontend.dev/problem/implement-Promise-any)ment `Promise.race()`](https://bigfrontend.dev/problem/implement-Promise-race)

记录完成的数组

```js

/**
 * @param {Array<any>} promises - notice input might have non-Promises
 * @return {Promise<any[]>}
 */
function all(promises) {
  // your code here
  return new Promise((resolve, reject) => {
    const n = promises.length;
    if (n === 0) {
      resolve([]);
      return;
    }
    const res = new Array(n);
    let finishedLength = 0;
    promises.forEach((promise, index) => {
      Promise.resolve(promise).then(value => {
        res[index] = value;
        finishedLength++;
        if (finishedLength === n) resolve(res);
      }).catch(error => {
        reject(error);
      })
    })

  })
}
```

### 33. implement `Promise.allSettled()`

> The Promise.allSettled() method returns a promise that resolves after all of the given promises have either fulfilled or rejected, with an array of objects that each describes the outcome of each promise.

- from [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/allSettled)

Different from `Promise.all()` which rejects right away once an error occurs, `Promise.allSettled()` waits for all promises to settle.

Now can you implement your own `allSettled()` ?

*note*

**Do not use Promise.allSettled() directly**, it helps nothing.

Related Problems

[30. implement async helper - `parallel()` ](https://bigfrontend.dev/problem/implement-async-helper-parallel)
[32. implement `Promise.all()` ](https://bigfrontend.dev/problem/implement-Promise-all)
[34. implement `Promise.any()`](https://bigfrontend.dev/problem/implement-Promise-any)
[35. implement `Promise.race()`](https://bigfrontend.dev/problem/implement-Promise-race)

需要标记任务

```js

/**
 * @param {Array<any>} promises - notice that input might contains non-promises
 * @return {Promise<Array<{status: 'fulfilled', value: any} | {status: 'rejected', reason: any}>>}
 */
function allSettled(promises) {
  // your code here
  if (promises.length === 0) {
    return Promise.resolve([]);
  }

  const len = promises.length;
  const results = Array(len);
  let finishedPromise = 0;

  return new Promise((resolve) => {
    promises.forEach((promise, index) => {
      if (!(promise instanceof Promise)) {
        promise = Promise.resolve(promise);
      }

      promise.then(
        (value) => {
          results[index] = {
            status: 'fulfilled',
            value,
          };

          finishedPromise++;
          if (finishedPromise === len) {
            resolve(results);
          }
        },
        (reason) => {
          results[index] = {
            status: 'rejected',
            reason,
          };

          finishedPromise++;
          if (finishedPromise === len) {
            resolve(results);
          }
        }
      );
    });
  });
}
```

### 34. implement `Promise.any()`

> Promise.any() takes an iterable of Promise objects and, as soon as one of the promises in the iterable fulfils, returns a single promise that resolves with the value from that promise

- from [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/any)

Can you implement a `any()` to work the same as `Promise.any()`?

*note*

`AggregateError` is not supported in Chrome yet, but you can still use it in your code since we will add the Class into your code. Do something like following:

```js
new AggregateError(
  'No Promise in Promise.any was resolved', 
  errors
)
```

Related Problems

[32. implement `Promise.all()` ](https://bigfrontend.dev/problem/implement-Promise-all)
[33. implement `Promise.allSettled()` ](https://bigfrontend.dev/problem/implement-Promise-allSettled)
[35. implement `Promise.race()`](https://bigfrontend.dev/problem/implement-Promise-race)

类似的写法

```js

/**
 * @param {Array<Promise>} promises
 * @return {Promise}
 */
function any(promises) {
  // your code here
  return new Promise((resolve, reject) => {
    const errors = [];
    const len = promises.length;
    let rejectedPromise = 0;

    promises.forEach((promise, index) => {
      if (!(promise instanceof Promise)) {
        promise = Promise.resolve(promise);
      }

      promise.then(
        (value) => {
          resolve(value);
        }
      ).catch(
        (reason) => {
          errors[index] = reason;

          rejectedPromise++;
          if (rejectedPromise === len) {
            reject(new AggregateError(
              'No Promise in Promise.any was resolved', 
              errors
            ));
          }
        }
      );
    });
  })
}
```

### 35. implement `Promise.race()`

This problem is similar to [31. implement async helper - `race()`](https://bigfrontend.dev/problem/implement-async-helper-race), but with Promise.

> The Promise.race() method returns a promise that fulfills or rejects as soon as one of the promises in an iterable fulfills or rejects, with the value or reason from that promise. [source: MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race)

Can you create a `race()` which works the same as `Promise.race()`?

Related Problems

[32. implement `Promise.all()` ](https://bigfrontend.dev/problem/implement-Promise-all)
[33. implement `Promise.allSettled()` ](https://bigfrontend.dev/problem/implement-Promise-allSettled)
[34. implement `Promise.any()` ](https://bigfrontend.dev/problem/implement-Promise-any)

```js

/**
 * @param {Array<Promise>} promises
 * @return {Promise}
 */
function race(promises) {
  // your code here
  if (promises.length === 0) return Promise.resolve();
  return new Promise((resolve, reject) => {
    for (const promise of promises) {
      if (!promise instanceof Promise) promise = Promise.resolve(promise);
      promise.then(res => resolve(res)).catch(err => reject(err));
    }
  })
}
```

### 36. create a fake timer(setTimeout)

`setTimeout` adds task in to a task queue to be handled later, the time actually is no accurate. ([Event Loop](https://javascript.info/event-loop)).

This is OK in general web application, but might be problematic in test.

For example, at [5. implement throttle() with leading & trailing option](https://bigfrontend.dev/problem/implement-throttle-with-leading-and-trailing-option) we need to test the timer with more accurate approach.

Could you implement your own `setTimeout()` and `clearTimeout()` to be sync? so that they have **accurate timing** for test. This is what [FakeTimes](https://github.com/sinonjs/fake-timers) are for.

By "accurate", it means **suppose all functions cost no time**, we start our function at time `0`, then `setTimeout(func1, 100)` would schedule `func1` exactly at `100`.

You need to replace `Date.now()` as well to provide the time.

```js
class FakeTimer {
  install() {
    // setTimeout(), clearTimeout(), and Date.now() 
    // are replaced
  }

  uninstall() {
    // restore the original APIs
    // setTimeout(), clearTimeout() and Date.now()
  }

  tick() {
     // run all the schedule functions in order
  }
}
```

Your code is tested like this

```js
const fakeTimer = new FakeTimer()
fakeTimer.install()

const logs = []
const log = (arg) => {
   logs.push([Date.now(), arg])
}

setTimeout(() => log('A'), 100)
// log 'A' at 100

const b = setTimeout(() => log('B'), 110)
clearTimeout(b)
// b is set but cleared

setTimeout(() => log('C'), 200)

expect(logs).toEqual([[100, 'A'], [200, 'C']])

fakeTimer.uninstall()
```

*Note*

Only `Date.now()` is used when judging your code, you can ignore other time related apis.

Related Problems

[84. create a fake timer (setInterval)](https://bigfrontend.dev/problem/create-a-fake-timer-setInterval)

使用队列存储任务，记录时间

```js
class FakeTimer {
  constructor() {
    this.taskQueue = [];
    this.currTime = 0;
  }
  install() {
    // replace window.setTimeout, window.clearTimeout, Date.now
    // with your implementation
    this.windowSetTimeOut = window.setTimeout;
    this.windowClearTimeout = window.clearTimeout;
    this.dateNow = Date.now;

    window.setTimeout = (cb, wait) => {
      // accumulate the time for nested setTimeout calls.
      wait += this.currTime;

      const task = {
        id: this.taskQueue.length - 1,
        cb,
        wait,
      };
      this.taskQueue.push(task);
      this.taskQueue.sort((taskA, taskB) => taskB.wait - taskA.wait);

      return task.id;
    }

    window.clearTimeout = (id) => {
      // accumulate the time for nested setTimeout calls.
      const taskIndex = this.taskQueue.findIndex((task) => task.id === id);
      this.taskQueue.splice(taskIndex, 1);
    }

    Date.now = () => this.currTime;
  }
  
  uninstall() {
    // restore the original implementation of
    // window.setTimeout, window.clearTimeout, Date.now
    window.setTimeout = this.windowSetTimeOut;
    window.clearTimeout = this.windowClearTimeout;
    Date.now = this.dateNow;
  }
  
  tick() {
    // run the scheduled functions without waiting
    while (this.taskQueue.length > 0) {
      const { cb, wait } = this.taskQueue.pop();
      this.currTime = wait;
      cb();
    }
  }
}
```

### 37. implement Binary Search (unique)

Even in Front-End review, basic algorithm technique like [Binary Search](https://en.wikipedia.org/wiki/Binary_search_algorithm) are likely to be asked.

You are given an **unique & ascending** array of integers, please search for its index with Binary Search.

If not found, return `-1`

*note*

Please don't use `Array.prototype.indexOf()`, it is not our goal.

Related Problems

[48. search first index with Binary Search(possible duplicate array)](https://bigfrontend.dev/problem/search-first-index-with-Binary-Search-duplicate-array)
[49. search last index with Binary Search(possible duplicate array)](https://bigfrontend.dev/problem/search-last-index-with-Binary-Search-possible-duplicate-array)
[50. search element right before target with Binary Search(possible duplicate array)](https://bigfrontend.dev/problem/search-element-right-before-target-with-Binary-Search-possible-duplicate-array)
[51. search element right after target with Binary Search(possible duplicate array)](https://bigfrontend.dev/problem/search-element-right-after-target-with-Binary-Search-possible-duplicate-array)

二分查找

```js

/**
 * @param {number[]} arr - ascending unique array
 * @param {number} target
 * @return {number}
 */
function binarySearch(arr, target){
  // your code here
  let left = 0, right = arr.length - 1;
  while (left <= right) {
    const mid = left + ((right - left) >> 1);
    if (arr[mid] === target) {
      return mid;
    } else if (arr[mid] > target) {
      right = mid - 1;
    } else {
      left = mid + 1;
    }
  }
  return arr[left] === target ? left : -1;
}
```

### 38. implement `jest.spyOn()`

If you did unit test before, you must be familiar with `Spy`.

You are asked to create a `spyOn(object, methodName)`, which works the same as [jest.spyOn()](https://jestjs.io/docs/en/jest-object#jestspyonobject-methodname).

To make it simple, here are the 2 requirements of `spyOn`

1. original method should be called when spied one is called
2. spy should have a `calls` array, which holds all the arguments in each call.

Code to explain everything.

```ts
const obj = {
   data: 1, 
   increment(num) {
      this.data += num
   }
}

const spy = spyOn(obj, 'increment')

obj.increment(1)

console.log(obj.data) // 2

obj.increment(2)

console.log(obj.data) // 4

console.log(spy.calls)
// [ [1], [2] ]
```

返回一个spy对象

```js

/**
 * @param {object} obj
 * @param {string} methodName
 */
function spyOn(obj, methodName) {
  // your code here
  const spy = {
    calls: [],
  };

  const originalMethod = obj[methodName];
  if (!originalMethod || typeof originalMethod !== 'function') {
    throw new Error();
  }

  obj[methodName] = (...args) => {
    spy.calls.push(args);
    originalMethod.apply(obj, args);
  }

  return spy;
}
```

### 39. implement range()

Can you create a `range(from, to)` which makes following work?

```js
for (let num of range(1, 4)) {
  console.log(num)  
}
// 1
// 2
// 3
// 4
```

This is a simple one, could you think **more fancy approaches other than for-loop**?

Notice that you are not required to return an array, but something [iterable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterator_protocol) would be fine

自己手写一版迭代器

```js

/**
 * @param {integer} from
 * @param {integer} to
 */
function range(from, to) {
  // The iterator object that will be returned
  // when calling the Symbol.iterator method of an object.
  // The iterator object has a method named next which
  // generates values for the iteration.
  const iterator = {
    from,
    to,
    next() {
      if(this.from > this.to) {
        return {
          done: true,
        }
      }
      const value = { value: this.from, done: false };
      this.from++;
      return value;
    }
  };

  // Return an object that has a method named
  // Symbol.iterator for the for...of to work.
  // When for..of starts, it calls that method once.
  return {
    [Symbol.iterator]() {
      return iterator;
    }
  }
}
```

利用generator生成器

```js
/**
 * @param {integer} from
 * @param {integer} to
 */
function range(from, to) {
  function* gen() {
    let i = from;
    while (i <= to) {
      yield i;
      i++;
    }
  }

  return gen();
}
```

### 40. implement Bubble Sort

Even for Front-End Engineer, it is a must to understand how basic sorting algorithms work.

Now you are asked to implement [Bubble Sort](https://en.wikipedia.org/wiki/Bubble_sort), which sorts an integer array in ascending order.

Do it **in-place**, no need to return anything.

*Follow-up*

What is time cost for average / worst case ? Is it stable?

Related Problems

[41. implement Merge Sort](https://bigfrontend.dev/problem/implement-Merge-Sort)
[42. implement Insertion Sort](https://bigfrontend.dev/problem/implement-Insertion-Sort)
[43. implement Quick Sort](https://bigfrontend.dev/problem/implement-Quick-Sort)
[44. implement Selection Sort](https://bigfrontend.dev/problem/implement-Selection-Sort)

冒泡排序

```js

/**
 * @param {number[]} arr
 */
function bubbleSort(arr) {
  // your code here
  const len = arr.length;
  for (let i = 0; i < len - 1; i++) {
    let swaped = false;
    for (let j = i + 1; j < len; j++) {
      if (arr[i] > arr[j]) {
        [arr[i], arr[j]] = [arr[j], arr[i]];
        swaped = true;
      }
    }
    if (!swaped) break;
  }
}
```

### 41. implement Merge Sort

Even for Front-End Engineer, it is a must to understand how basic sorting algorithms work.

Now you are asked to implement [Merge Sort](https://en.wikipedia.org/wiki/Merge_sort), which sorts an integer array in ascending order.

Do it **in-place**, no need to return anything.

*Follow-up*

What is time cost for average / worst case ? Is it stable?

Related Problems

[40. implement Bubble Sort ](https://bigfrontend.dev/problem/implement-Bubble-Sort)
[42. implement Insertion Sort](https://bigfrontend.dev/problem/implement-Insertion-Sort)
[43. implement Quick Sort](https://bigfrontend.dev/problem/implement-Quick-Sort)
[44. implement Selection Sort](https://bigfrontend.dev/problem/implement-Selection-Sort)

```js

/**
 * @param {number[]} arr
 */
function mergeSort(arr) {
  // your code here
  mergeSortImpl(arr, 0, arr.length - 1);
}

function mergeSortImpl(arr, start, end) {
  if (start >= end) return;
  const mid = start + Math.floor((end - start) / 2);
  mergeSortImpl(arr, start, mid);
  mergeSortImpl(arr, mid + 1, end);
  merge(arr, start, mid, end)
}

function merge(arr, start, mid, end) {
  const lowHalf = [];
  const highHalf = [];

  let k = start;
  let i;
  let j;
  for (i = 0; k <= mid; i++, k++) {
    lowHalf[i] = arr[k];
  }

  for (j = 0; k <= end; j++, k++) {
    highHalf[j] = arr[k];
  }

  k = start;
  i = 0;
  j = 0;

  while (i < lowHalf.length && j < highHalf.length) {
    if (lowHalf[i] < highHalf[j]) {
      arr[k] = lowHalf[i];
      i++;
    } else {
      arr[k] = highHalf[j];
      j++;
    }
    k++;
  }

  while (i < lowHalf.length) {
    arr[k] = lowHalf[i];
    i++;
    k++;
  }

  while (j < highHalf.length) {
    arr[k] = highHalf[j];
    j++;
    k++;
  }
}
```

### 42. implement Insertion Sort

Even for Front-End Engineer, it is a must to understand how basic sorting algorithms work.

Now you are asked to implement [Insertion Sort](https://en.wikipedia.org/wiki/Insertion_sort), which sorts an integer array in ascending order.

Do it **in-place**, no need to return anything.

*Follow-up*

What is time cost for average / worst case ? Is it stable?

Related Problems

[40. implement Bubble Sort ](https://bigfrontend.dev/problem/implement-Bubble-Sort)
[41. implement Merge Sort](https://bigfrontend.dev/problem/implement-Merge-Sort)
[43. implement Quick Sort](https://bigfrontend.dev/problem/implement-Quick-Sort)
[44. implement Selection Sort](https://bigfrontend.dev/problem/implement-Selection-Sort)

插入排序

```js

/**
 * @param {number[]} arr
 */
function insertionSort(arr) {
  // your code here
  for (let i = 0; i < arr.length; i++) {
    let currentVal = arr[i];
    let j = i - 1;
    while (j >= 0 && currentVal < arr[j]) {
      arr[j + 1] = arr[j];
      j--;
    }
    arr[j + 1] = currentVal;
  }
}
```

### 43. implement Quick Sort

Even for Front-End Engineer, it is a must to understand how basic sorting algorithms work.

Now you are asked to implement [Quick Sort](https://en.wikipedia.org/wiki/Quicksort), which sorts an integer array in ascending order.

Do it **in-place**, no need to return anything.

*Follow-up*

What is time cost for average / worst case ? Is it stable?

Related Problems

[40. implement Bubble Sort ](https://bigfrontend.dev/problem/implement-Bubble-Sort)
[41. implement Merge Sort](https://bigfrontend.dev/problem/implement-Merge-Sort)
[42. implement Insertion Sort ](https://bigfrontend.dev/problem/implement-Insertion-Sort)
[44. implement Selection Sort](https://bigfrontend.dev/problem/implement-Selection-Sort)

快速排序

```js

/**
 * @param {number[]} arr
 */
function quickSort(arr) {
  // your code here
  quickSortImpl(arr, 0, arr.length - 1);
}

function quickSortImpl(arr, low, high) {
  if (low >= high) return ;

  const pivotIndex = partition(arr, low, high);
  quickSortImpl(arr, low, pivotIndex - 1);
  quickSortImpl(arr, pivotIndex + 1, high);
}

function partition(arr, low, high) {
  const pivot = arr[high];
  let i = low;
  for (let j = low; j < high; j++) {
    if (arr[j] < pivot) {
      [arr[j], arr[i]] = [arr[i], arr[j]];
      i++;
    }
  }
  [arr[i], arr[high]] = [arr[high], arr[i]];
  return i;
}
```

### 44. implement Selection Sort

Even for Front-End Engineer, it is a must to understand how basic sorting algorithms work.

Now you are asked to implement [Selection sort](https://en.wikipedia.org/wiki/Selection_sort), which sorts an integer array in ascending order.

Do it **in-place**, no need to return anything.

*Follow-up*

What is time cost for average / worst case ? Is it stable?

Related Problems

[40. implement Bubble Sort ](https://bigfrontend.dev/problem/implement-Bubble-Sort)
[41. implement Merge Sort ](https://bigfrontend.dev/problem/implement-Merge-Sort)
[42. implement Insertion Sort ](https://bigfrontend.dev/problem/implement-Insertion-Sort)
[43. implement Quick Sort ](https://bigfrontend.dev/problem/implement-Quick-Sort)

```js
/**
 * @param {number[]} arr
 */
function selectionSort(arr) {
  // your code here
  for (let i = 0; i < arr.length; i++) {
    let smallestIndex = i;
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[j] < arr[smallestIndex]) {
        smallestIndex = j;
      }
    }
    if (smallestIndex !== i) {
      [arr[smallestIndex], arr[i]] = [arr[i], arr[smallestIndex]];
    }
  }
}
```

### 45. find the K-th largest element in an unsorted array

You are given an unsorted array of numbers, which might have duplicates, find the K-th largest element.

The naive approach would be sort it first, but it costs `O(nlogn)`, could you find a better approach?

Maybe you can recall what is happening in [Quick Sort](https://bigfrontend.dev/problem/implement-Quick-Sort) or [Priority Queue](https://bigfrontend.dev/problem/create-a-priority-queue-in-JavaScript)

Related Problems

[24. create a Priority Queue in JavaScript ](https://bigfrontend.dev/problem/create-a-priority-queue-in-JavaScript)
[43. implement Quick Sort ](https://bigfrontend.dev/problem/implement-Quick-Sort)
快速选择

```js
/**
 * @param {number[]} arr
 * @param {number} k
 */
function findKThLargest(arr, k) {
  // your code here
  if (k === 0 || arr.length === 0) return [];
  return quickSelect(arr, 0, arr.length - 1, k - 1);
}

function quickSelect(arr, lo, hi, k) {
  let j = partition(arr, lo, hi);
  if (j == k) return arr[j];
  return j > k ? quickSelect(arr, lo, j - 1, k) : quickSelect(arr, j + 1, hi, k);
}

function partition(arr, lo, hi) {
  const value = arr[lo];
  let i = lo, j = hi + 1;
  while (true) {
    while (++i <= hi && arr[i] > value);
    while (--j >= i && arr[j] < value);
    if (i >= j) break;
    let t = arr[i];
    arr[i] = arr[j];
    arr[j] = t;
  }
  arr[lo] = arr[j];
  arr[j] = value;
  return j;
}
```

或者使用小根堆，保证堆顶是第k小的元素

```js

/**
 * @param {number[]} arr
 * @param {number} k
 */
function findKThLargest(arr, k) {
  // your code here
  if (!k || !arr.length || k > arr.length) {
      return [];
  }
  createHeap(arr, k);
  for (let i = k; i < arr.length; i++) {
    // 当前值比最小的k个值中的最大值小
    if (arr[i] < arr[0]) {
        [arr[i], arr[0]] = [arr[0], arr[i]];
        ajustHeap(arr, 0, k);
    }
  }
  return arr[k - 1];

  // 构建大顶堆
  function createHeap(arr, length) {
    // 从叶子节点向上进行调整
    for (let i = Math.floor(length / 2) - 1; i >= 0; i--) {
        ajustHeap(arr, i, length);
    }
  }
  // 调整长度为length的堆中元素位置
  function ajustHeap(arr, index, length) {
    for (let i = 2 * index + 1; i < length; i = 2 * i + 1) {
        if (i + 1 < length && arr[i + 1] > arr[i]) {
            i++;
        }
        if (arr[index] > arr[i]) {
            [arr[index], arr[i]] = [arr[i], arr[index]];
            index = i;
        } else {
            break;
        }
    }
  }
}
```

### 46. implement `_.once()`

[_.once(func)](https://lodash.com/docs/4.17.15#once) is used to force a function to be called only once, later calls only returns the result of first call.

Can you implement your own `once()`?

```js
function func(num) {
  return num
}

const onced = once(func)

onced(1) 
// 1, func called with 1

onced(2)
// 1, even 2 is passed, previous result is returned 
```

Related Problems

[14. Implement a general memoization function - `memo()` ](https://bigfrontend.dev/problem/implement-general-memoization-function)

本质就是单例模式

```js
/**
 * @param {Function} func
 * @return {Function}
 */
function once(func) {
  // your code here
  let instance = null;
  let isExcuted = false;
  return function(...args) {
    if (!isExcuted) {
      instance = func.apply(this, args);
      isExcuted = true;
    }
    return instance;
  }
}
```

### 47. reverse a linked list

Another basic algorithm even for Front End developers.

You are asked to **reverse a linked list**.

Suppose we have Node interface like this

```ts
class Node {
   new(val: number, next: Node);
   val: number
   next: Node
}
```

We can then chain nodes together to create a linked list.

```ts
const Three = new Node(3, null)
const Two = new Node(2, Three)
const One = new Node(1, Two)

//now we have  a linked list
// 1 → 2 → 3
```

Now how can you reverse it to 3 → 2 → 1 ? you can modify the `next` property of each node, but not the `val`.

*Follow up*

Could you solve it with and without recursion?

常规反转链表

```js
/** 
 * class Node {
 *  new(val: number, next: Node);
 *    val: number
 *    next: Node
 * }
 */

/**
 * @param {Node} list
 * @return {Node} 
 */
const reverseLinkedList = (list) => {
    // your code
    let pre = null;
    let curr = list;

    while (curr) {
        const next = curr.next;
        curr.next = pre;
        pre = curr;
        curr = next;
    }

    return pre;
}
```

### 48. search first index with Binary Search(possible duplicate array)

This is a variation of [37. implement Binary Search (unique)](https://bigfrontend.dev/problem/implement-Binary-Search-Unique).

Your are given a sorted ascending array of number, but **might have duplicates**, you are asked to return the **first index** of a target number.

If not found return -1.

*note*

Please don't use `Array.prototype.indexOf()`, it is not our goal.

Related Problems

[37. implement Binary Search (unique) ](https://bigfrontend.dev/problem/implement-Binary-Search-Unique)
[49. search last index with Binary Search(possible duplicate array)](https://bigfrontend.dev/problem/search-last-index-with-Binary-Search-possible-duplicate-array)
[50. search element right before target with Binary Search(possible duplicate array)](https://bigfrontend.dev/problem/search-element-right-before-target-with-Binary-Search-possible-duplicate-array)
[51. search element right after target with Binary Search(possible duplicate array)](https://bigfrontend.dev/problem/search-element-right-after-target-with-Binary-Search-possible-duplicate-array)

leftbound函数

```js

/**
 * @param {number[]} arr - ascending array with duplicates
 * @param {number} target
 * @return {number}
 */
function firstIndex(arr, target){
  // your code here
  let left = 0, right = arr.length - 1;
  while (left <= right) {
    const mid = left + Math.floor((right - left) / 2);
    if (arr[mid] >= target) {
      right = mid - 1;
    } else {
      left = mid + 1;
    }
  }
  return arr[left] === target ? left : -1;
}
```

### 49. search last index with Binary Search(possible duplicate array)

This is a variation of [37. implement Binary Search (unique)](https://bigfrontend.dev/problem/implement-Binary-Search-Unique).

Your are given a sorted ascending array of number, but **might have duplicates**, you are asked to return the **last index** of a target number.

If not found return -1.

*note*

Please don't use `Array.prototype.lastIndexOf()`, it is not our goal.

Related Problems

[37. implement Binary Search (unique) ](https://bigfrontend.dev/problem/implement-Binary-Search-Unique)
[48. search first index with Binary Search(possible duplicate array) ](https://bigfrontend.dev/problem/search-first-index-with-Binary-Search-duplicate-array)

类似的rightbound

```js

/**
 * @param {number[]} arr - ascending array with duplicates
 * @param {number} target
 * @return {number}
 */
function lastIndex(arr, target){
  // your code here
  let left = 0, right = arr.length - 1;
  while (left <= right) {
    const mid = left + Math.floor((right - left) / 2);
    if (arr[mid] <= target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }
  return arr[right] === target ? right : -1;
}
```

### 50. search element right before target with Binary Search(possible duplicate array)

This is a variation of [37. implement Binary Search (unique)](https://bigfrontend.dev/problem/implement-Binary-Search-Unique).

Your are given a sorted ascending array of number, but **might have duplicates**, you are asked to return the **element right before first appearance** of a target number.

If not found return `undefined`.

*note*

Please don't use `Array.prototype.indexOf()`, it is not our goal.

Related Problems

[37. implement Binary Search (unique) ](https://bigfrontend.dev/problem/implement-Binary-Search-Unique)
[48. search first index with Binary Search(possible duplicate array) ](https://bigfrontend.dev/problem/search-first-index-with-Binary-Search-duplicate-array)
[49. search last index with Binary Search(possible duplicate array) ](https://bigfrontend.dev/problem/search-last-index-with-Binary-Search-possible-duplicate-array)
[51. search element right after target with Binary Search(possible duplicate array)](https://bigfrontend.dev/problem/search-element-right-after-target-with-Binary-Search-possible-duplicate-array)

使用了leftbound找到左边界，然后找到左边界左边的数

```js

/**
 * @param {number[]} arr - ascending array with duplicates
 * @param {number} target
 * @return {number}
 */
function elementBefore(arr, target){
  // your code here
  let left = 0, right = arr.length - 1;
  while (left <= right) {
    const mid = left + Math.floor((right - left) / 2);
    if (arr[mid] >= target) {
      right = mid - 1;
    } else {
      left = mid + 1;
    }
  }
  return (left > 0 && arr[left] === target) ? arr[left - 1] : undefined;
}
```

### 51. search element right after target with Binary Search(possible duplicate array)

This is a variation of [37. implement Binary Search (unique)](https://bigfrontend.dev/problem/implement-Binary-Search-Unique).

Your are given a sorted ascending array of number, but **might have duplicates**, you are asked to return the **element right after last appearance** of a target number.

If not found return `undefined`.

*note*

Please don't use `Array.prototype.lastIndexOf()`, it is not our goal.

Related Problems

[37. implement Binary Search (unique) ](https://bigfrontend.dev/problem/implement-Binary-Search-Unique)
[48. search first index with Binary Search(possible duplicate array) ](https://bigfrontend.dev/problem/search-first-index-with-Binary-Search-duplicate-array)
[49. search last index with Binary Search(possible duplicate array) ](https://bigfrontend.dev/problem/search-last-index-with-Binary-Search-possible-duplicate-array)
[50. search element right before target with Binary Search(possible duplicate array) ](https://bigfrontend.dev/problem/search-element-right-before-target-with-Binary-Search-possible-duplicate-array)

使用了rightbound找到右边界，然后找到右边界右边的数

```js

/**
 * @param {number[]} arr - ascending array with duplicates
 * @param {number} target
 * @return {number}
 */
function elementAfter(arr, target){
  // your code here
  let left = 0, right = arr.length - 1;
  while (left <= right) {
    const mid = left + Math.floor((right - left) / 2);
    if (arr[mid] <= target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }
  return (right < arr.length - 1 && arr[right] === target) ? arr[right + 1] : undefined;
}
```

### 52. create a middleware system

Have you ever used [Express Middleware](http://expressjs.com/en/guide/using-middleware.html#using-middleware) before?

Middleware functions are functions with fixed interface that could be chained up like following two functions.

```js
app.use('/user/:id', function (req, res, next) {
  next()
}, function (req, res, next) {
  next(new Error('sth wrong'))
})
```

You are asked to create simplified Middleware system:

```ts
type Request = object

type NextFunc =  (error?: any) => void

type MiddlewareFunc = 
  (req: Request, next: NextFunc) => void

type ErrorHandler = 
  (error: Error, req: Request, next: NextFunc) => void

class Middleware {
  use(func: MiddlewareFunc | ErrorHandler) {
    // do any async operations
    // call next() to trigger next function
  }
  start(req: Request) {
    // trigger all functions with a req object
  }
}
```

Now we can do something similar with Express

```js
const middleware = new Middleware()

middleware.use((req, next) => {
   req.a = 1
   next()
})

middleware.use((req, next) => {
   req.b = 2
   next()
})


middleware.use((req, next) => {
   console.log(req)
})

middleware.start({})
// {a: 1, b: 2}
```

Notice that `use()` could also accept an ErrorHandler which has 3 arguments. The error handler is triggered if `next()` is called with an extra argument or uncaught error happens, like following.

```js
const middleware = new Middleware()

// throw an error at first function
middleware.use((req, next) => {
   req.a = 1
   throw new Error('sth wrong') 
   // or `next(new Error('sth wrong'))`
})

// since error occurs, this is skipped
middleware.use((req, next) => {
   req.b = 2
})

// since error occurs, this is skipped
middleware.use((req, next) => {
   console.log(req)
})

// since error occurs, this is called
middleware.use((error, req, next) => {
   console.log(error)
   console.log(req)
})

middleware.start({})
// Error: sth wrong
// {a: 1}
```

直接调用中间件

```js
class Middleware {
  constructor () {
    this.middlewareFuncs = [];
    this.middlewareFuncIndex = 0;
    this.errorHandlers = [];
    this.errorHandlerIndex = 0;
    this.next = this.next.bind(this);
  }
  /**
   * @param {MiddlewareFunc} func
   */
  use(func) {
    if (func.length === 2) {
      this.middlewareFuncs.push(func);
    }
    if (func.length === 3) {
      this.errorHandlers.push(func);
    }
  }

  /**
   * @param {Request} req
   */
  start(req) {
    this.req = req;
    this.next();
  }
  
  next(error) {
    try {
      let func;
      if (error) {
        func = this.errorHandlers[this.errorHandlerIndex++];
        func(error, this.req, this.next);
      } else {
        func = this.middlewareFuncs[this.middlewareFuncIndex++];
        func(this.req, this.next);
      }
    } catch (err) {
      const errorHandler = this.errorHandlers[this.errorHandlerIndex++];
      errorHandler(err, this.req, this.next);
    }
  }
}
```

### 53. write your own `extends` in es5

I believe you've used [extends](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/extends) keyword in you JavaScript programs before.

Could you implement a `myExtends()` function in ES5 to mimic the behavior of `extends`?

`myExtends()` takes a SubType and SuperType, and return a new type.

```js
const InheritedSubType = myExtends(SuperType, SubType)

const instance = new InheritedSubType()

// above should work (almost) the same as follows

class SubType extends SuperType {}
const instance = new SubType()
```

To solve this problem, you need to fully understand what is [Inheritance](https://javascript.info/class-inheritance)

*note*

Your code will be test against following SuperType and SubType

```js
function SuperType(name) {
    this.name = name
    this.forSuper = [1, 2]
    this.from = 'super'
}
SuperType.prototype.superMethod = function() {}
SuperType.prototype.method = function() {}
SuperType.staticSuper = 'staticSuper'

function SubType(name) {
    this.name = name
    this.forSub = [3, 4]
    this.from = 'sub'
}

SubType.prototype.subMethod = function() {}
SubType.prototype.method = function() {}
SubType.staticSub = 'staticSub'
```

改变原型链

```js
const myExtends = (SuperType, SubType) => {
  // your code here
  const constructor = function(...args) {
    // Call the original Constructors with the constructor
    // instance as their context
    SuperType.apply(this, args);
    SubType.apply(this, args);
    Object.setPrototypeOf(this, SubType.prototype)
  }

  Object.setPrototypeOf(SubType.prototype, SuperType.prototype);
  Object.setPrototypeOf(constructor.prototype, SubType.prototype);
  // Inherit static properties
  Object.setPrototypeOf(constructor, SuperType);
  return constructor;
}
```

### 54. flatten Thunk

Suppose we have a Callback type

```ts
type Callback = 
  (error: Error, result: any | Thunk) => void
```

A Thunk is a function that take a Callback as parameter

```ts
type Thunk = (callback: Callback) => void
```

Like following three thunks

```js
const func1 = (cb) => {
  setTimeout(() => cb(null, 'ok'), 10)
}

const func2 = (cb) => {
  setTimeout(() => cb(null, func1), 10)
}

const func3 = (cb) => {
  setTimeout(() => cb(null, func2), 10)
}
```

in above example, three functions are kind of chained up, func3 → func2 → func1, but it don't work without some glue.

OK, now you are asked to implement a `flattenThunk()` which glue them up and returns a new thunk.

```js
flattenThunk(func3)((error, data) => {
   console.log(data) // 'ok'
})
```

*note*

Once error occurs, the rest uncalled functions should be skipped

[Source for this ](https://github.com/kolodny/exercises/tree/master/flatten-thunk)

返回一个闭包

```js

/**
 * @param {Thunk} thunk
 * @return {Thunk}
 */
function flattenThunk(thunk) {
  // your code here
  return function(callback) {
    const callbackWrapper = (err, data) => {
      if (err) {
        callback(err);
      } else if (typeof data === 'function') {
        data(callbackWrapper);
      } else {
        callback(err, data);
      }
    }
    thunk(callbackWrapper);
  }
}
```

### 55. highlight keywords in HTML string

Suppose you are implementing an auto-complete in search input.

When keywords are typed, you need to **highlight the keywords**, how would you do that?

To simplify things, you need to create a function `highlightKeywords(html:string, keywords: string[])`, which wraps the keywords in html string, with `<em>` tag.

Here is an example.

```js
highlightKeywords(
  'Hello FrontEnd Lovers', 
  ['Hello', 'Front', 'JavaScript']
)
// '<em>Hello</em> <em>Front</em>End Lovers'
```

Pay attention to the overlapping and adjacent case. You should use the least tags as possible.

```js
highlightKeywords(
  'Hello FrontEnd Lovers', 
  ['Front', 'End', 'JavaScript']
)
// 'Hello <em>FrontEnd</em> Lovers'

highlightKeywords(
  'Hello FrontEnd Lovers', 
  ['Front', 'FrontEnd', 'JavaScript']
)
// 'Hello <em>FrontEnd</em> Lovers'
```

note that `space` should not be included.

分隔单词，进行操作

```js
/**
 * @param {string} html
 * @param {string[]} keywords
 */
function highlightKeywords(html, keywords) {
  // your code here
  const regexp = new RegExp(keywords.join('|'), 'gi');
  return html.split(' ').map((word) => {
    if (keywords.includes(word)) return `<em>${word}</em>`;
    return word.replace(regexp, (w) => `<em>${w}</em>`).replace('</em><em>', '');
  }).join(' ');
};
```

### 56. call APIs with pagination

Have you ever met some APIs with pagination, and needed to recursively fetch them based on response of previous request ?

Suppose we have a `/list` API, which returns an array `items`.

```ts
// fetchList is provided for you
const fetchList = (since?: number) => 
  Promise<{items: Array<{id: number}>}>
```

1. for initial request, we just fetch `fetchList`. and get the last item id from response.
2. for next page, we need to call `fetchList(lastItemId)`.
3. repeat above process.

The `/list` API only gives us 5 items at a time, with server-side filtering, it might be less than 5. But if none returned, it means nothing to fetch any more and we should stop.

You are asked to create a function that could return arbitrary amount of items.

```ts
const fetchListWithAmount = (amount: number = 5) { }
```

*note*

You can achieve this by regular loop, even fancier solutions with [async iterators or async generators](https://javascript.info/async-iterators-generator). You should try them all.

记录顺序

```js
// fetchList is provided for you
// const fetchList = (since?: number) => 
//   Promise<{items: Array<{id: number}>}>


// you can change this to generator function if you want
const fetchListWithAmount = async (amount = 5) => {
  // your code here
  const items = [];
  let lastItemId = null;

  while (items.length <= amount) {
    const response = lastItemId ? await fetchList(lastItemId) : await fetchList();
    if (!response || !response.items.length) {
      break;
    }
    items.push(...response.items);
    lastItemId = items[items.length - 1].id;
  }

  return items.slice(0, amount);
}

```

### 57. create an Observable

Have you ever used [RxJS](https://rxjs-dev.firebaseapp.com/guide/overview) before? The most important concept in it is [Observable](https://rxjs-dev.firebaseapp.com/guide/observable) and [Observer](https://rxjs-dev.firebaseapp.com/guide/observer).

Observable defines how values are delivered to Observer. Observer is just a set of callbacks.

Let's look at the code.

```js
const observer = {
  next: (value) => {
     console.log('we got a value', value)
  },
  error: (error) => {
    console.log('we got an error', error)
  },
  complete: () => {
    console.log('ok, no more values')
  }
}
```

Above is an Observer which is pretty clear about what it is doing.

Then we could attach this Observer to some Observable. Observable feeds this observer with values or errors.

```js
const observable = new Observable((subscriber)=> {
  subscriber.next(1)
  subscriber.next(2)
  setTimeout(() => {
    subscriber.next(3)
    subscriber.next(4)
    subscriber.complete()
  }, 100)
})
```

The code plainly says when is a subscriber is attached,

1. subscriber is fed with a value `1`
2. subscriber is then fed with a value `2`
3. wait 100 ms
4. subscriber is fed with `3`
5. subscriber is fed with `4`
6. no more values for subscriber

Now if we attach above `observer` to `observable`, `next` and `complete` of subscriber are called in order.(be aware that there is a delay between 2 and 3)

```js
const sub = observable.subscribe(subscriber)
// we got a value 1
// we got a value 2

// we got a value 3
// we got a value 4
// ok, no more values
```

Notice `subscribe()` return a [Subscription](https://rxjs-dev.firebaseapp.com/guide/subscription) which could be used to stop listening to the value delivery.

```js
const sub = observable.subscribe(subscriber)
setTimeout(() => {
  // ok we only subscribe for 100ms
  sub.unsubscribe()
}, 100)
```

So this is the basic idea of Observable and Observer. There will be a few more interesting follow-up problems.

**Now you are asked to implement a basic Observable class**, which makes above possible.

Some extra requirements are listed here.

1. `error` and `complete` can only be delivered once, `next/error/complete` after `error/complete` should not work
2. for a subscriber object, `next/error/complete` callback are all optional. if a function is passed as observer, it is treated as `next`.
3. should support multiple subscription

*Further Reading*

https://github.com/tc39/proposal-observable

[Source for this ](https://www.glassdoor.com/Interview/Signed-NDA-But-phone-interview-questions-were-same-as-mentioned-many-times-here-flatten-array-optimize-a-function-imple-QTN_2512492.htm)

Related Problems

[70. implement Observable.from()](https://bigfrontend.dev/problem/implement-Observable-from)
[71. implement Observable Subject](https://bigfrontend.dev/problem/implement-Observable-Subject)

```js
class Observable {
  
  constructor(setup) {
    this.setup = setup;
  }
 
  subscribe(subscriber) {
    const originalNext = typeof subscriber === 'function' ? subscriber : subscriber.next;
    const originalError = subscriber.error;
    const originalComplete = subscriber.complete;

    let isUnsubscribed = false;

    subscriber.next = (value) => {
      if (isUnsubscribed) {
        return ;
      }

      if (typeof originalNext === 'function') {
        originalNext(value);
      }
    };

    subscriber.error = (error) => {
      if (isUnsubscribed) {
        return ;
      }

      isUnsubscribed = true;
      if (typeof originalError === 'function') {
        originalError(error);
      }
    };

    subscriber.complete = () => {
      if (isUnsubscribed) {
        return;
      }

      isUnsubscribed = true;
      if (typeof originalComplete === 'function') {
        originalComplete();
      }
    };

    this.setup(subscriber);
    return {
      unsubscribe() {
        isUnsubscribed = true;
      },
    };
  }
}
```

### 58. get DOM tree height

Height of a tree is the maximum depth from root node. Empty root node have a height of 0.

If given DOM tree, can you create a function to get the height of it?

For the DOM tree below, we have a height of 4.

```html
<div>
  <div>
    <p>
      <button>Hello</button>
    </p>
  </div>
  <p>
    <span>World!</span>
  </p>
</div>
```

Can you solve this both recursively and iteratively?

Related Problems

[19. find corresponding node in two identical DOM tree ](https://bigfrontend.dev/problem/find-corresponding-node-in-two-identical-DOM-tree)

使用递归层次DFS

```js
/**
 * @param {HTMLElement | null} tree
 * @return {number}
 */
function getHeight(tree) {
  // your code here
  if (tree === null) return 0;

  let maxHeight = 0;
  // Use .children instead of .childNodes to ignore TextNodes.
  for (const child of tree.children) {
    maxHeight = Math.max(maxHeight, getHeight(child));
  }

  return maxHeight + 1;
}
```

使用队列BFS

```js

/**
 * @param {HTMLElement | null} tree
 * @return {number}
 */
function getHeight(tree) {
  // your code here
  if (tree === null) {
    return 0;
  }

  let maxHeight = 0;
  const queue = [[tree, 1]];

  while (queue.length > 0) {
    const [el, height] = queue.shift();
    maxHeight = Math.max(height, maxHeight);

    for (const child of el.children) {
      queue.push([child, height + 1]);
    }
  }

  return maxHeight;
}
```

### 59. create a browser history

I believe you are very familiar about your browser you are currently visiting [https://bigfrontend.dev](https://bigfrontend.dev/) with.

The common actions relating to history are:

1. `new BrowserHistory()` - when you open a new tab, it is set with an empty history
2. `goBack()` - go to last entry, notice the entries are kept so that `forward()` could get us back
3. `forward()` - go to next visited entry
4. `visit()` - when you enter a new address or click a link, this adds a new entry but truncate the entries which we could `forward()` to.

Say we start a new tab, this is the empty history.

```js
[ ] 
```

Then visit url A, B, C in turn.

```js
[ A, B, C]
        ↑
```

We are currently at C, we could `goBack()` to B, then to A

```js
[ A, B, C]
  ↑          
```

`forward()` get us to B

```js
[ A, B, C]
     ↑          
```

Now if we visit a new url D, since we are currently at B, C is truncated.

```js
[ A, B, D]
        ↑
```

You are asked to implement a `BrowserHistory` class to mimic the behavior.

```js
class BrowserHistory {
  
  /**
   * @param {string} url
   * if url is set, it means new tab with url
   * otherwise, it is empty new tab
   */
  constructor(url) {
    // Store the url, since the method `goBack()` should
    // return the initial url if it is out of bounds.
    /** For instance,
     *  const bh = new BrowserHistory('X')
     *  bh.visit('A')
     *  bh.goBack()
     *  bh.goBack()
     *  console.log(bh.current); // should be be 'X' rather than undefined.
     */
    this.initialUrl = url;
    this.urls = url ? [url] : [];
    this.currentIndex = this.urls.length - 1;
  }
  /**
   * @param { string } url
   */
  visit(url) {
    this.currentIndex++;
    this.urls[this.currentIndex] = url;
  }
  
  /**
   * @return {string} current url
   */
  get current() {
    return this.currentIndex >= 0 ? this.urls[this.currentIndex] : this.initialUrl;
  }
  
  // go to previous entry
  goBack() {
    this.currentIndex--;
  }
  
  // go to next visited url
  forward() {
    this.currentIndex = Math.min(this.urls.length - 1, this.currentIndex + 1);
  }
}
```

### 60. create your own `new` operator

[`new` operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new) is used to create new instance objects.

Do you know exactly what `new` does?

You are asked to implement `myNew()`, which should return an object just as what `new` does but without using `new`.

Pay attention to the return type of constructor.

```js
/**
 * @param {Function} constructor
 * @param {any[]} args - argument passed to the constructor
 * `myNew(constructor, ...args)` should return the same as `new constructor(...args)`
 */
const myNew = (constructor, ...args) => {
  // your code here
  // The `Object.create()` method creates a new empty object, using the
  // specified object as the prototype of the newly created object.
  const obj = Object.create(constructor.prototype);

  // The `Object.create()` method creates a new empty object, using the
  // specified object as the prototype of the newly created object.
  const returnValue = constructor.apply(obj, args);

  // If returnValue is an object, return returnValue, otherwise return obj.
  // Usually, constructors do not have return statement. The new operator
  // creates an object, assign it to this, and automatically returns that
  // object as a result. If a constructor has return statement and the return
  // value is an object, the object is returned instead of the newly created
  // object, otherwise the return value is ignored.
  return returnValue instanceof Object ? returnValue : obj;
}
```

### 61. create your own `Function.prototype.call`

[Function.prototype.call](https://tc39.es/ecma262/#sec-function.prototype.call) is very useful when we want to alter the `this` of a function.

Can you implement your own `myCall`, which returns the same result as `Function.prototype.call`?

For the [newest ECMAScript spec](https://tc39.es/ecma262/#sec-function.prototype.call), `thisArg` are not transformed. And not replaced with window in [Strict Mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode).

Your implementation should follow above spec and do what *non* Strict Mode does.

`Function.prototype.cal/apply/bind` and [Reflect.apply](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/apply) should not be used.

```js
Function.prototype.mycall = function(thisArg, ...args) {
  // your code here
  // thisArg can be null or defined.
  thisArg = thisArg || window;

  // Transform primitive value into object, so that we can add property.
  thisArg = Object(thisArg);

  // Create a unique property name.
  const fn = Symbol();

  // Assign the function that has to be called to the unique property.
  thisArg[fn] = this;

  // Call the function as a method to get the correct context.
  const returnValue = thisArg[fn](...args);

  // Delete the unique property so that the original thisArg is not affected.
  delete thisArg[fn];
  return returnValue;
}
```

### 62. implement BigInt addition

Luckily we have [BigInt](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt) in JavaScript so handle big numbers.

What if we need to do it by ourselves for older browsers?

You are asked to implement a string addition function, **with all non-negative integers in string**.

```js
add('999999999999999999', '1')
// '1000000000000000000'
```

Don't use BigInt directly, it is not our goal here.

Related Problems

[75. implement BigInt subtraction](https://bigfrontend.dev/problem/implement-BigInt-subtraction)
[76. implement BigInt addition with sign](https://bigfrontend.dev/problem/implement-BigInt-addition-with-sign)
[77. implement BigInt subtraction with sign](https://bigfrontend.dev/problem/implement-BigInt-subtraction-with-sign)

双指针

```js

/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
function add(num1, num2) {
  // Use two pointers to keep track of the digit in the strings,
  // set both pointers to the end of the strings.
  let i = num1.length - 1;
  let j = num2.length - 1;
  // Keep track of the carry.
  let carry = 0;
  let result = '';

  // Iterate through both strings backwards,
  // until the beginning of both strings is reached and there
  // is no carry that needs to add to the result.
  while (i >= 0 || j >= 0 || carry > 0) {
    // Get digits at current position in both strings.
    // If undefined, set to zero.
    const digit1 = Number(num1[i] || 0);
    const digit2 = Number(num2[j] || 0);
    // Add both digits and previous carry.
    let sum = digit1 + digit2 + carry;
    // If the sum "overflows", bring over the carry to the next iteration.
    carry = sum >= 10 ? 1 : 0;
    // Get the ones digit of the sum.
    sum = sum % 10;
    // Add the ones digit to the beginning of the result.
    result = String(sum) + result;
    i--;
    j--;
  }

  return result;
}
```

### 63. create `_.cloneDeep()`

`Object.assign()` could be used to do shallow copy, while for recursive deep copy, [_.cloneDeep](https://lodash.com/docs/4.17.15#cloneDeep) could be very useful.

Can you create your own `_.cloneDeep()`?

The lodash implementation actually covers a lot of data types, for simplicity, your code just need to cover

1. [primitive types](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#Primitive_values) and their wrapper Object
2. Plain Objects (Object literal) with all enumerable properties
3. Array

处理循环引用

```js
// Use WeakMap that stores cloned results to handle circular reference.
function cloneDeep(data, map = new WeakMap()) {
  // your code here
  if (data === null || typeof data !== 'object') {
    return data;
  }

  // If the source object is already in the WeakMap,
  // its corresponding copy is returned instead of recursing
  // further.
  if (map.has(data)) {
    return map.get(data);
  }

  const res = Array.isArray(data) ? [] : {};
  // Store the source object and its clone in the WeakMap.
  map.set(data, res);

  const keys = [
    ...Object.getOwnPropertyNames(data),
    ...Object.getOwnPropertySymbols(data),
  ];

  for (const key of keys) {
    res[key] = cloneDeep()
  }
}
```

### 64. auto-retry Promise on rejection

For a web application, fetching API data is a common task.

But the API calls might fail because of Network problems. Usually we could show a screen for Network Error and ask users to retry.

One approach to handle this is **auto retry when network error occurs**.

You are asked to create a `fetchWithAutoRetry(fetcher, count)`, which automatically fetch again when error happens, until the maximum count is met.

For the problem here, there is no need to detect network error, you can just retry on all promise rejections.

```js

/**
 * @param {() => Promise<any>} fetcher
 * @param {number} maximumRetryCount
 * @return {Promise<any>}
 */
function fetchWithAutoRetry(fetcher, maximumRetryCount) {
  // your code here
  return new Promise((resolve, reject) => {
    const retry = () => {
      fetcher()
        .then((result) => {
          resolve(result);
        })
        .catch((error) => {
          if (maximumRetryCount > 0) {
            maximumRetryCount--;
            retry();
          } else {
            reject(error);
          }
        })
    }

    retry();
  })
}
```

### 65. add comma to number

Given a number, please create a function to add commas as thousand separators.

```js
addComma(1) // '1'
addComma(1000) // '1,000'
addComma(-12345678) // '-12,345,678'
addComma(12345678.12345) // '12,345,678.12345'
```

Input are all valid numbers.

正则表达式

```js
/**
 * @param {number} num
 * @return {string}
 */
function addComma(num) {
  // your code here
  let str = Number(num).toString();
  return str.replace(/(?<!\d+\.\d*)(\d)(?=(\d{3})+(\.\d+)?$)/g, '$1,');
}
```

自带的toLocaleString

```js
/**
 * @param {number} num
 * @return {string}
 */
function addComma(num) {
  // your code here
  num = Number(num).toString().split('.');
  let res = Number(num[0]).toLocaleString();
  if (num[1]) {
      res += '.' + num[1];
  }
  return res;
}
```

自带字符串函数操作

```js

/**
 * @param {number} num
 * @return {string}
 */
function addComma(num) {
  // 按小数点分割
  num = Number(num).toString().split('.');
  // 将整数部分转换成字符数组并且倒序排列
  let arr = num[0].split('').reverse();
  // 存放添加','的整数
  let res = [arr[0]];
  for (let i = 1; i < arr.length; i++) {
      // 添加分隔符
      if (i % 3 === 0) res.push(',');
      res.push(arr[i]);
  }
  // 再次将整数部分倒序成为正确的顺序，并拼接成字符串
  res = res.reverse().join('');
  // 如果有小数的话添加小数部分
  if (num[1]) {
      res += '.' + num[1];
  }
  return res;
}
```

### 66. remove duplicates from an array

Given an array containing all kinds of data, please implement a function `deduplicate()` to remove the duplicates.

You should modify the array in place. Order doesn't matter.

> What is time & space cost of your approach?

利用set

```js
/**
 * @param {any[]} arr
 */
function deduplicate(arr) {
  let res = new Set(arr);
  arr.splice(0);
  arr.push(...res);
}
```

还有别的方法

### 67. create your own Promise

[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) is widely used nowadays, hard to think how we handled [Callback Hell](http://callbackhell.com/) in the old times.

Can you implement a `MyPromise` Class by yourself?

At least it should match following requirements

1. new promise: `new MyPromise((resolve, reject) => {})`
2. chaining : `MyPromise.prototype.then()` *[then handlers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) should be called asynchronously*
3. rejection handler: `MyPromise.prototype.catch()`
4. static methods: `MyPromise.resolve()`, `MyPromise.reject()`.

This is a challenging problem. Recommend you read about Promise thoroughly first.

```js
class MyPromise {
  constructor(executor) {
    // your code here
    this.state = 'pending';
    try {
      executor(this._resolve.bind(this), this._reject.bind(this));
    } catch (error) {
      this._reject(error);
    }
  }
   _resolve(value) {
    if (this.state !== 'pending') return;

    this.state = 'fulfilled';
    this.result = value;

    queueMicrotask(() => {
      if (this.onFulfilled === undefined) return;

      try {
        const returnValue = this.onFulfilled(this.result);
        const isReturnValuePromise = returnValue instanceof MyPromise;

        if (!isReturnValuePromise) {
          this.thenPromiseResolve(returnValue);
        } else {
          returnValue.then(this.thenPromiseResolve, this.thenPromiseReject);
        }
      } catch (error) {
        this.thenPromiseReject(error);
      }
    });
  }

  _reject(error) {
    if (this.state !== 'pending') return;

    this.state = 'rejected';
    this.result = error;

    queueMicrotask(() => {
      if (this.onRejected === undefined) return;

      try {
        const returnValue = this.onRejected(this.result);
        const isReturnValuePromise = returnValue instanceof MyPromise;

        if (!isReturnValuePromise) {
          this.thenPromiseResolve(returnValue);
        } else {
          returnValue.then(this.thenPromiseResolve, this.thenPromiseReject);
        }
      } catch (error) {
        this.thenPromiseReject(error);
      }
    });
  }

  then(onFulfilled, onRejected) {
    // your code here
    const isOnFulfilledFunction = typeof onFulfilled === 'function';
    this.onFulfilled = isOnFulfilledFunction ? onFulfilled : (value) => value;

    const isOnRejectedFunction = typeof onRejected === 'function';
    this.onRejected = isOnRejectedFunction
      ? onRejected
      : (error) => {
          throw error;
        };

    return new MyPromise((resolve, reject) => {
      // Register `resolve` and `reject`, so that we can
      // resolve or reject this promise in `_resolve`
      // or `_reject`.
      this.thenPromiseResolve = resolve;
      this.thenPromiseReject = reject;
    });
  }
  
  catch(onRejected) {
    // your code here
    return this.then(undefined, onRejected);
  }
  
  static resolve(value) {
    // your code here
    const isValuePromise = value instanceof MyPromise;
    if (isValuePromise) {
      return value;
    }

    return new MyPromise((resolve) => {
      resolve(value);
    })
  }
  
  static reject(value) {
    // your code here
    return new MyPromise((_, reject) => {
      reject(value);
    })
  }
}
```

### 68. get DOM tags

Given a DOM tree, please return all the tag names it has.

Your function should return a unique array of tags names in lowercase, order doesn't matter.

遍历节点，用set保存

```js
/**
 * @param {HTMLElement} tree
 * @return {string[]}
 */
function getTags(tree) {
  // your code here
  const set = new Set();

  const stack = [tree];

  while (stack.length > 0) {
    const top = stack.pop();
    set.add(top.tagName.toLowerCase());
    stack.push(...top.children);
  }
  return [...set];
}
```

### 69. implement deep equal `_.isEqual()`

[_.isEqual](https://lodash.com/docs/4.17.15#isEqual) is useful when you want to compare complex data types by value not the reference.

Can you implement your own version of deep equal `isEqual`? The lodash version covers a lot of data types. In this problem, you are asked to support :

1. primitives
2. plain objects (object literals)
3. array

Objects are compared by their own, not inherited, enumerable properties

```js
const a = {a: 'bfe'}
const b = {a: 'bfe'}

isEqual(a, b) // true
a === b // false

const c = [1, a, '4']
const d = [1, b, '4']

isEqual(c, d) // true
c === d // false
```

Lodash implementation has some strange behaviors. ([github issue](https://github.com/lodash/lodash/issues/2463), like following code

```js
const a = {}
a.self = a
const b = {self: a}
const c = {}
c.self = c
const d = {self: {self: a}}
const e = {self: {self: b}}
```

`lodash.isEqual` gives us following result. Notice there is a case that resulting in `false`.

```js
// result from lodash implementation
_.isEqual(a, b) // true
_.isEqual(a, c) // true
_.isEqual(a, d) // true
_.isEqual(a, e) // true
_.isEqual(b, c) // true
_.isEqual(b, d) // true
_.isEqual(b, e) // false
_.isEqual(c, d) // true
_.isEqual(c, e) // true
_.isEqual(d, e) // true
```

Setting aside the performance concerns mentioned by lodash, **your implement should not have above problem**, which means above all returns **true** and call stack should not exceed the maximum.

深度遍历，找到最后的结果

```js
/**
 * @param {any} a
 * @param {any} b
 * @return {boolean}
 */
function isEqual(a, b, map = new WeakMap()) {
  // your code here
  if (a === null || b === null) return a === b;
  if (typeof a !== 'object' || typeof b !== 'object') {
    return a === b;
  }
  if (map.has(a) || map.has(b)) return true;
  map.set(a, b);
  const a_keys = Object.keys(a);
  const b_keys = Object.keys(b);
  if (a_keys.length !== b_keys.length) return false;
  for (let i = 0; i < a_keys.length; i++) {
    if (!isEqual(a[a_keys[i]], b[b_keys[i]], map)) return false;
  }
  return true;
}
```

### 70. implement Observable.from()

This is a follow-up on [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable).

Suppose you have solved [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable), here you are asked to implement a creation operator `from()`.

From the [document](https://rxjs-dev.firebaseapp.com/api/index/function/from), `from()`

> Creates an Observable from an Array, an array-like object, a Promise, an iterable object, or an Observable-like object.

Your `from()` should accept all above types.

```js
from([1,2,3]).subscribe(console.log);
// 1
// 2
// 3
```

**Note**

1. Observable is already given for you, no need to create it.
2. for the problem here, `Observable-like` means `Observable instance`. Though in real-world you should check `Symbol.observable`

Related Problems

[57. create an Observable ](https://bigfrontend.dev/problem/create-an-Observable)
[71. implement Observable Subject](https://bigfrontend.dev/problem/implement-Observable-Subject)

```js
/**
 * @param {Array | ArrayLike | Promise | Iterable | Observable} input
 * @return {Observable}
 */
function from(input) {
  if (input instanceof Observable) return input;

  const isIterable = typeof input[Symbol.iterator] === 'function';
  const isArrayLike = input.length !== undefined;

  if (isIterable || isArrayLike) {
    return new Observable((subscriber) => {
      try {
        if (isArrayLike) input = Array.from(input);

        for (const value of input) {
          subscriber.next(value);
        }
      } catch (err) {
        subscriber.error(err);
      } finally {
        subscriber.complete();
      }
    });
  }

  if (input instanceof Promise) {
    return new Observable((subscriber) => {
      input
        .then((result) => {
          subscriber.next(result);
        })
        .catch((err) => {
          subscriber.error(err);
        })
        .finally(() => {
          subscriber.complete();
        });
    });
  }

  throw new Error(
    'You can provide an Observable, Promise, Array, or Iterable.'
  );
}
```

### 71. implement Observable Subject

This is a follow-up on [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable).

Plain Observables are unicast, meaning every subscription is independent. To create multicast, you need to use [Subject](https://rxjs-dev.firebaseapp.com/guide/subject).

Following code is easier to understand.

```js
// default behavior with plain Observable
const observable = from([1,2,3])
observable.subscribe(console.log)
observable.subscribe(console.log)
// 1
// 2
// 3
// 1
// 2
// 3
```

You can see that two subscriptions are independent so the logs are grouped by subscription.

with Subject, it works like Event Listeners in DOM world.

```js
const subject = new Subject()
subject.subscribe(console.log)
subject.subscribe(console.log)
 
const observable = from([1, 2, 3])
observable.subscribe(subject)

// 1
// 1
// 2
// 2
// 3
// 3
```

Now the logs are different! That is because Subject first works as a observer, get the values, then works as an Observable and dispatch the value to different observers.

Cool right? Ok, you are asked to **implement a simple `Subject Class`**.

1. `Observable` is given for you, you can just use it.
2. you can use `new Observer({next,error,complete})` or `new Observer(function)` to create an observer.

Related Problems

[57. create an Observable ](https://bigfrontend.dev/problem/create-an-Observable)
[70. implement Observable.from() ](https://bigfrontend.dev/problem/implement-Observable-from)

每个需要绑定this，然后执行函数

```js
// You can use Observer which is bundled to your code

// class Observer {
//   // subscriber could one next function or a handler object {next, error, complete}
//   constructor(subscriber) { }
//   next(value) { }
//   error(error) { }
//   complete() {}
// }


class Subject {
  constructor() {
    this.observers = [];
    this.next = this.next.bind(this);
    this.error = this.error.bind(this);
    this.complete = this.complete.bind(this);
  }
  subscribe(subscriber) {
    const observer = new Observer(subscriber);
    this.observers.push(observer);
    return observer;
  }
  next(value) {
    for (const observer of this.observers) {
      observer.next(value);
    }
  }
  error(error) {
    for (const observer of this.observers) {
      observer.error(error);
    }
  }
  complete() {
    for (const observer of this.observers) {
      observer.complete();
    }
  }
}
```

### 72. implement Observable interval()

This is a follow-up on [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable).

Suppose you have solved [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable), here you are asked to implement a creation operator `interval()`.

From the [document](https://rxjs-dev.firebaseapp.com/api/index/function/interval), `interval()`

> Creates an Observable that emits sequential numbers every specified interval of time

```js
interval(1000).subscribe(console.log);
```

Above code prints 0, 1, 2 .... with an interval of 1 seconds.

**Note** Observable is already given for you, no need to create it.

使用闭包保存数据

```js
/**
 * @param {number} period
 * @return {Observable}
 */
function interval(period) {
  // your code here
  let num = 0;
  return new Observable((subscriber) => {
    setInterval(() => {
      // subscriber.next(num++);
      subscriber.next(num);
      num++;
    }, period);
  })
}
```

### 73. implement Observable fromEvent()

This is a follow-up on [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable).

Suppose you have solved [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable), here you are asked to implement a creation operator `fromEvent()`( for DOM Event)

From the [document](https://rxjs-dev.firebaseapp.com/api/index/function/fromEvent), `fromEvent()`

> Creates an Observable that emits events of a specific type coming from the given event target.

Simply speaking, it is a util to attach event listener in Observable fashion.

```js
const source = fromEvent(node, 'click')
source.subscribe((e) => console.log(e))
```

When `node` is clicked, the event is logged.

**Note**

1. Observable is already given for you, no need to create it.
2. the event listener removal is handled by [add()](https://rxjs-dev.firebaseapp.com/api/index/class/Subscription#add), which is beyond our scope here, you can ignore that.

Related Problems

[57. create an Observable ](https://bigfrontend.dev/problem/create-an-Observable)
[70. implement Observable.from() ](https://bigfrontend.dev/problem/implement-Observable-from)
[71. implement Observable Subject ](https://bigfrontend.dev/problem/implement-Observable-Subject)
[72. implement Observable interval() ](https://bigfrontend.dev/problem/implement-Observable-interval)

增加新的订阅

```js

/**
 * @param {HTMLElement} element
 * @param {string} eventName
 * @param {boolean} capture
 * @return {Observable}
 */
function fromEvent(element, eventName, capture = false) {
  // your code here
  return new Observable((subscriber) => {
    element.addEventListener(
      eventName,
      (e) => {
        subscriber.next(e);
      },
      capture
    );
  });
}
```

### 74. implement Observable Transformation Operators

This is a follow-up on [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable).

There are [a lot of operators](https://rxjs-dev.firebaseapp.com/guide/operators) for Observable, if we think of Observable as event stream, then modifying the stream is a common task, transformation operators are useful at this.

In this problem, you are asked to implement [map()](https://rxjs-dev.firebaseapp.com/api/operators/map), as the name indicates, it maps the value to another value thus creating a new event stream.

Here is an example.

```js
const source = Observable.from([1,2,3])

map(x => x * x)(source) // this transformer doubles numbers and create a new stream
 .subscribe(console.log)
// 1
// 4
// 9
```

Observable has `pipe()` method which could make this more readable.

```js
const source = Observable.from([1,2,3])

source.pipe(map(x => x * x))
 .subscribe(console.log)
// 1
// 4
// 9
```

**Note** Observable is already given for you, no need to create it.

Related Problems

[57. create an Observable ](https://bigfrontend.dev/problem/create-an-Observable)
[70. implement Observable.from() ](https://bigfrontend.dev/problem/implement-Observable-from)
[71. implement Observable Subject ](https://bigfrontend.dev/problem/implement-Observable-Subject)
[72. implement Observable interval() ](https://bigfrontend.dev/problem/implement-Observable-interval)
[73. implement Observable fromEvent() ](https://bigfrontend.dev/problem/implement-Observable-fromEvent)

重新修改事件

```js

/**
 * @param {any} input
 * @return {(observable: Observable) => Observable}
 * returns a function which trasnform Observable
 */
function map(transform) {
  // your code here
  return (source) => {
    return new Observable((subscriber) => {
      const originalNext = subscriber.next;
      subscriber.next = (value) => {
        const newValue = transform(value);
        originalNext.call(subscriber, newValue);
      };
      source.subscribe(subscriber);
    });
  };
}
```

### 75. implement BigInt subtraction

Luckily we already have built-in support of [BigInt](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt) in JavaScript, at least in some browsers.

```js
1000000000000000000000n - 999999999999999999999n
// 1n
```

Suppose BigInt cannot be used, can you implement a string subtraction function by yourself?

```js
subtract('1000000000000000000000', '999999999999999999999')
// '1'
```

All input are valid **non-negative integer strings**, and the result is guaranteed to be non-negative.

*Don't use BigInt directly, it is not our goal here*

Related Problems

[62. implement BigInt addition ](https://bigfrontend.dev/problem/add-BigInt-string)
[76. implement BigInt addition with sign](https://bigfrontend.dev/problem/implement-BigInt-addition-with-sign)
[77. implement BigInt subtraction with sign](https://bigfrontend.dev/problem/implement-BigInt-subtraction-with-sign)

其实本质和大数加法没有不同

```js

/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
function subtract(num1, num2) {
  // your code here
  if (!num1 || !num2) return num1 || num2;

  let i = num1.length - 1, j = num2.length - 1;
  let carry = 0;
  let result = [];

  while (i >= 0 || j >= 0 || carry) {
    let sub = (+num1[i--] || 0) - (+num2[j--] || 0) + carry + 10;
    carry = Math.floor(sub / 10) > 0 ? 0 : -1;
    result.unshift(sub % 10);
  }
  
  while (result.length > 1 && +result[0] === 0) {
    result.shift();
  }

  return result.join('');
}
```

