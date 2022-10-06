---
title:  bigfrontend的Coding Problems讨论
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

# [Coding Problems](https://bigfrontend.dev/problem)

## [1. implement curry()](https://bigfrontend.dev/problem/implement-curry)

### 题目

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

Related Problems

[2. implement curry() with placeholder support ](https://bigfrontend.dev/problem/implement-curry-with-placeholder)

### 答案

使用闭包返回执行的结果，函数柯里化

```js
/**
 * @param { (...args: any[]) => any } fn
 * @returns { (...args: any[]) => any }
 */
function curry(func) {
  // here ...args collects arguments as array (rest)
  return function curriedFunc(...args) {
    // Here we check if current args passed equals the number of args func expects
    if(args.length >= func.length) {
      // if yes, we spread args elements to pass into func (spread). This is our base case.
      return func(...args)
    } else {
      /* if not, we return a function that collects the next arguments passed in next and 
      we recursively call curriedFunc, accumalating and spreading the values of args first and then
      the values of next. next will take into consideration a variable amount of next arguments
      e.g (1, 2) (1) (1,2,3) */
      return function(...next) {
        return curriedFunc(...args,...next);
      }
    }
  }
}

const join = (a, b, c) => {
   return `${a}_${b}_${c}`
}
const curriedJoin = curry(join)

// curriedJoin(1, 2, 3) // '1_2_3'

// curriedJoin(1)(2, 3) // '1_2_3'

curriedJoin(1, 2)(3) // '1_2_3'
```



## [2. implement curry() with placeholder support](https://bigfrontend.dev/problem/implement-curry-with-placeholder)

### 题目

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

### 答案

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

完整版

```js
/**
 * @param { Function } func
 */
function curry(func) {
  
  return function curried(...args) {   // we need to return a function to make it curry-able.
    
    // 1. If the arguments are extra then eliminate them
    // we don't want to pass 6 arguments when the expected is 3.
    // it will interfere with our placeholder logic
    const sanitizedArgs = args.slice(0, func.length);
    
    // see if placeholder is available in arguments
    const hasPlaceholder = sanitizedArgs.some(arg => arg == curry.placeholder);

    // if no placeholder and arguements are equal to what expected then it is normal function call
    if(!hasPlaceholder && sanitizedArgs.length == func.length) {
      return func.apply(this, sanitizedArgs);
    }
    
    // else we need to replace placeholders with actual values
    // we call helper function `mergeArgs` for this
    // we pass first and next arguments to helper function
    return function next(...nextArgs) {
      return curried.apply(this, mergeArgs(sanitizedArgs, nextArgs));
    }
    
  } 
}

function mergeArgs(args, nextArgs) {

  let result = [];

  // iterate over args (because we need to replace from it) 
  // in each iteration, if we find element == curry.placeholder
  // then we replace that placeholder with first element from nextArgs
  // else we put current element
  args.forEach((arg, idx) => {
    if(arg == curry.placeholder) {
      result.push(nextArgs.shift());
    } else {
      result.push(arg);
    }
  });

  // we merge both, because there might be chance that args < nextArgs
  return [...result, ...nextArgs];
}

curry.placeholder = Symbol()
```



## [3. implement Array.prototype.flat()](https://bigfrontend.dev/problem/implement-Array-prototype.flat)

### 题目

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

### 答案

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



## [4. implement basic throttle()](https://bigfrontend.dev/problem/implement-basic-throttle)

### 题目

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

### 答案

利用标志位isWaiting保存状态，判断是否在等待，lastCallArgs保存上一次的参数。

```js
/**
 * @param {(...args:any[]) => any} func
 * @param {number} wait
 * @returns {(...args:any[]) => any}
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



## [5. implement throttle() with leading & trailing option](https://bigfrontend.dev/problem/implement-basic-throttle)

### 题目

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

### 答案

基于上一题的版本增加一个判断

```js

/**
 * @param {Function} func
 * @param {number} wait
 * @param {boolean} option.leading
 * @param {boolean} option.trailing
 */
function throttle(func, wait, option = {leading: true, trailing: true}) {
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



## [6. implement basic debounce()](https://bigfrontend.dev/problem/implement-basic-debounce)

### 题目

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

### 答案

基本版防抖，使用定时器

```js
/**
 * @param {(...args: any[]) => any} func
 * @param {number} wait
 * @returns {(...args: any[]) => any}
 */
function debounce(func, wait) {
  // definition: invoke function only when the last function has passed `wait` time
  let timer = null;
  return function debounced(...args) {
    // clear timer (doesn't matter if the execution is going or has completed)
    // 1. nothing will happen in the case when execution has completed
    // 2. it will clear the timer and restart if an ongoing timer was running
    if (timer) clearTimeout(timer);

    // call supplied function `func` after `wait` time
    timer = setTimeout(() => {
      func.apply(this, args);
    }, wait)
  }
}
```



## [7. implement debounce() with leading & trailing option](https://bigfrontend.dev/problem/implement-debounce-with-leading-and-trailing-option)

### 题目

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

### 答案

```js
/**
   * @param {Function} func
   * @param {number} wait
   * @param {boolean} option.leading
   * @param {boolean} option.trailing
   */
function debounce(func, wait, option = {leading: false, trailing: true}) {
  
  // in basic debounce, we kept only timerId
  // here, we will keep lastArgs too as we trailing function call with last arguments
  let timerId = null;
  let lastArgs = null;


  return function debounced(...args) {

    // if timer is over and leading is true
    // then immediately call supplied function
    // else capture arguments in lastArgs
    if(!timerId && option.leading) {
      func.apply(this, args);
    } else {
      lastArgs = args;
    }

    // clear timer so that next call is exactly after `wait` time
    clearTimeout(timerId);

    timerId = setTimeout(() => {
      // invoke only if lastArgs is present and trailing is true
      if(option.trailing && lastArgs) func.apply(this, lastArgs); 
      
      // reset variables as they need to restart new life after calling this function 
      lastArgs = null;
      timerId = null;
    }, wait);
  }
}
```



## [8. can you shuffle() an array?](https://bigfrontend.dev/problem/can-you-shuffle-an-array)

### 题目

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

### 答案

In order to have a uniform distribution after `shuffle`, the probability of picking any items in the array should be the same. That means if there are **n** items, the probability should be **1/n**

The probability of picking 1st random item when we use `getRandomInt` helper by passing arguments (0, n-1) is 1/n. Simple math.

The probability of picking 2nd random item when we use `getRandomInt` helper by passing arguments (1, n-1) is 1/n as well. Why? Because the 2nd item was not picked at the first iteration and that probability is n-1/n. Multiplying those give us 1/n by `n-1/n * 1/n-1 => 1/n`

The probability of picking 3rd item is 1/n as you expect already `n-1/n * n-2/n-1 * 1/n-2 => 1/n`

Now all we need do is set the incremental insert position (starting form 0) and pick a random item, then swap them.

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



## [9. decode message](https://bigfrontend.dev/problem/decode-message)

### 题目

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

### 答案

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



## [10. first bad version](https://bigfrontend.dev/problem/first-bad-version)

### 题目

Say you have multiple versions of a program, write a program that will find and return the first bad revision given a `isBad(version)` function.

Versions after first bad version are supposed to be all bad versions.

*notes*

1. Inputs are all non-negative integers
2. if none found, return -1

[Source for this ](https://www.glassdoor.com/Interview/If-you-have-500-revisions-of-a-program-write-a-program-that-will-find-and-return-the-FIRST-bad-revision-given-a-isBad-revi-QTN_1255475.htm)

### 答案

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



## [11. what is Composition? create a pipe()](https://bigfrontend.dev/problem/what-is-composition-create-a-pipe)

### 题目

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

### 答案

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

当然也可以直观迭代函数

```js
/**
 * @param {Array<(arg: any) => any>} funcs 
 * @return {(arg: any) => any}
 */
function pipe(funcs) {
	return (arg) => {
     let lastArg = arg;
     for (const func of funcs) {
       lastArg = func(lastArg);
     }
     return lastArg;
  }
}
```



## [12. implement Immutability helper](https://bigfrontend.dev/problem/implement-Immutability-helper)

### 题目

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

### 答案

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

我觉得没有大佬写的这一版直观

```js
/**
 * @param {any} data
 * @param {Object} command
 */
function update(data, command) {
  for (const [key, value] of Object.entries(command)) {
    switch (key) {
      case '$push':
        return [...data, ...value];
      case '$set':
        return value;
      case '$merge':
        if (!(data instanceof Object)) {
          throw Error("bad merge");
        }
        return {...data, ...value};
      case '$apply':
        return value(data);
      default:
        if (data instanceof Array) {
          const res = [...data];
          res[key] = update(data[key], value);
          return res;
        } else {
          return {
            ...data,
            [key]: update(data[key], value)
          }
        }
    }
  }
}
```



## [13. Implement a Queue by using Stack](https://bigfrontend.dev/problem/implement-a-queue-by-using-stack)

### 题目

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

### 答案

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
  constructor() {
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



## [14. Implement a general memoization function - `memo()](https://bigfrontend.dev/problem/implement-general-memoization-function)`

### 题目

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

### 答案

使用了map+闭包保存之前计算的数据

```js
/**
 * @param {Function} func
 * @param {(args:[]) => string }  [resolver] - cache key generator
 */
// With this context in mind, check test cases below.
function memo(func, resolver) {
  // your code here
  const cache = new Map();

  // Map<cacheKey, Map<context, value>>
  return function() {
    const cacheKey = resolver ?  resolver(...arguments) : Array.from(arguments).join(',');

    const contextMap = cache.get(cacheKey);
    // If there is a corresponding context map to cachekey
    // Check if context is in the map, if so, return value.
    // Else if no corresponding add contextMap, add new entry to the context map
    if (!contextMap) {
      const value = func.apply(this, arguments);
      cache.set(cacheKey, new Map([[ this, value ]]));
      return value;
    }

    if (contextMap.has(this)) {
      return contextMap.get(this);
    } 
    // If context not in the map, calculate and add to context map.
    const value = func.apply(this, arguments);
    contextMap.set(this, value);
    return value;
  }
}
```



## [15. implement a simple DOM wrapper to support method chaining like jQuery](https://bigfrontend.dev/problem/implement-a-simple-DOM-wrapper-to-support-method-chaining-like-jQuery)

### 题目

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

### 答案

通过返回的闭包设置参数，返回this实现链式调用

```js
/**
 * @param {HTMLElement} el - element to be wrapped
 */
function $(el) {
  return {
    css: function(property, value) {
      el.style[property] = value;
      return this;
    }
  }
}
```

或者通过class实现

```js
/**
 * @param {HTMLElement} el - element to be wrapped
 */
function $(el) {
  return new Wrapper(el);
}

class Wrapper {
  constructor(el) {
    this.el = el;
  }

  css(p, v) {
    this.el.style[p] = v;

    return this;
  }
}
```



## [16. create an Event Emitter](https://bigfrontend.dev/problem/create-an-Event-Emitter)

### 题目

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

### 答案

发布订阅模式的简化版，subscribe订阅事件，emit发布事件

Closures:  https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures

```js
// please complete the implementation
class EventEmitter {
  constructor () {
    // 维护一个订阅该对象的map
    // map:  (eventName,[callback...]),key为eventName，value是由相同eventName的回调函数组成的数组
    // key: eventName,value: An array of callback functions with the same eventName
    this.callbacks = new Map();
  }
  subscribe(eventName, callback) {
  	if (this.callbacks.has(eventName)) {
      // 存在则向对应value里增加callback
      // eventName exists,add callback to the corresponding value
      const callbacks = this.callbacks.get(eventName);
      callbacks.push(callback)
    } else {
      // eventName不存在，则添加
      // eventName does not exist,add
      this.callbacks.set(eventName, [callback]);
    }

    return {
      // 返回一个包含release方法的对象
      // return an object containing the release method
      release: () => {
        // 找到对应的callback，删除
        // find the corresponding callback and delete it
        // 此时利用闭包，使用的是第10行的watcher
        // At this point,the Closures is used,and the watcher on line 10 is used
        const callbacks = this.callbacks.get(eventName);
        const index = callbacks.indexOf(callback);
        callbacks.splice(index, 1);
      }
    }
  }
  
  emit(eventName, ...args) {
  	const callbacks = this.callbacks.get(eventName);
    if (!callbacks.length) return;
    // eventName存在则依此调用watcher里的callback
    // if eventName exists,call the callback in watcher accordingly
    for (const callback of callbacks) {
      callback.apply(this, args);
    }
  }
}
```



## [17. Create a simple store for DOM element](https://bigfrontend.dev/problem/create-a-simple-store-for-DOM-node)

### 题目

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

### 答案

使用对象存储节点

1. Use the Object as a memory.
2. Because object keys can contain only strings or Symbol, for 100% elimination of collisions with node properties it is better to use Symbol.
3. For each node, add a property with a value from Symbol and save there a serial number (or another unique id, for example, uuid)
4. Save the value with this id in memo.

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



## [18. Improve a function](https://bigfrontend.dev/problem/improve-a-function)

### 题目

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

### 答案

Let's assume the `n` is the length of `items`, `k` is the number of properties in an item, `m` is the length of `excludes`

Time complexity: **O(m) + O(nk)** for 1) building `excludesMap` takes `O(m)` and for 2) filtering `items` take `O(nk)`

Space complexity: **O(m)**

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



## [19. find corresponding node in two identical DOM tree](https://bigfrontend.dev/problem/find-corresponding-node-in-two-identical-DOM-tree)

### 题目

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

### 答案

使用迭代法进行操作，用队列保存层级节点

```js

/**
 * @param {HTMLElement} rootA
 * @param {HTMLElement} rootB - rootA and rootB are clone of each other
 * @param {HTMLElement} nodeA
 */
const findCorrespondingNode = (rootA, rootB, target) => {
  const stackA = [rootA];
  const stackB = [rootB];

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

递归

```c++

/**
 * @param {HTMLElement} rootA
 * @param {HTMLElement} rootB - rootA and rootB are clone of each other
 * @param {HTMLElement} nodeA
 */
const findCorrespondingNode = (rootA, rootB, target) => {
  if(rootA === target){
    return rootB;
  }
  
  for(let i = 0; i < rootA.children.length; i++){
    const res = findCorrespondingNode(rootA.children[i], rootB.children[i], target);
    if (res) {
      return res;
    }
  }
}
```

一个大佬写的其他方法

```js
// Approach 1: Recursive
const findCorrespondingNode = (rootA, rootB, target) => {
    
    if(rootA == target){
        return rootB;
    }
    
    for(let i = 0; i < rootA.children.length; i++){
        const res = findCorrespondingNode(rootA.children[i], rootB.children[i], target)
        if(res){
            return res;
        }
    }
}

//Approach 2: Iterative DFS: Using stack
const findCorrespondingNode = (rootA, rootB, target) => {
    const stack = [[rootA, rootB]];
    
    while(stack.length > 0) {
        const [leftNode, rightNode] = stack.pop();
        if (leftNode === target) return rightNode;
        
        for (let i = 0; i < leftNode.children.length; i++) {
            stack.push([leftNode.children[i], rightNode.children[i]]);
        }
    }
}

/**
* Approach 3: Iterative BFS: Using Queue
*/
const findCorrespondingNode = (rootA, rootB, target) => {
    if (rootA === target) {
        return rootB;
    }
    
    const queueA = [rootA];
    const queueB = [rootB];
    
    while(queueA.length) {
        // removes the first element from an array and returns that removed element
        const currentElementA = queueA.shift(); 
        const currentElementB = queueB.shift();
        
        if (currentElementA === target) {
            return currentElementB;
        }
        // adds one or more elements to the end of an array and returns the new length of the array.
        queueA.push(...currentElementA.children); 
        queueB.push(...currentElementB.children);    
    }
    return null;    
}

/**
* Approach 4: Using DOM API
*/

const findCorrespondingNode = (rootA, rootB, target) => {
    // if 'target' is itself rootA then directly return rootA, this will make 'path' array empty, and it will return rootB in reduceRight
    if (rootA === target)
    return rootB;
    
    // we can track 'target' in rootB using indexes stored during tracing 'target' in rootA
    let path = getRootAPath(rootA, target);
    
    // reduceRight is same as reduce but it iterate values from right to left
    // https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduceRight
    return path.reduceRight((accumulator, currentValue, index) => {
        return accumulator.children[currentValue];
    }, rootB) // rootB pointing to initialValue from where start the processing, this will be the accumulator when we start
}

// get path from target to rootA in the form of index arr, index pointing to position of a node in its parent HTML collection
function getRootAPath(rootA, target) {
    let path = [];
    
    let node = target;
    
    while (node !== rootA && node.parentNode) { // we will iterate till we reach top of the DOM tree
        const children = Array.from(node.parentNode.children); // convert HTMLCollection into Array
        path.push(children.indexOf(node)); // push index where 'node' found
        node = node.parentNode; // this will make sure we move from down to top
    }
    return path;
}


/**
* Approach 5: Using Tree Walker API
* https://developer.mozilla.org/en-US/docs/Web/API/Document/createTreeWalker
*/
const findCorrespondingNode = (rootA, rootB, target) => {
    const rootAWalker = document.createTreeWalker(rootA, NodeFilter.SHOW_ELEMENT);
    const rootBWalker = document.createTreeWalker(rootB, NodeFilter.SHOW_ELEMENT);
    
    let currentNodes = [rootAWalker.currentNode, rootBWalker.currentNode];
    
    while (currentNodes[0] !== target) {
        currentNodes = [rootAWalker.nextNode(), rootBWalker.nextNode()];
    }
    
    return currentNodes[1];
}
```



## [20. Detect data type in JavaScript](https://bigfrontend.dev/problem/detect-data-type-in-JavaScript)

### 题目

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

### 答案

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

简化版

```js
/**
 * @param {any} data
 * @return {string}
 */
function detectType(data) {
  if(data instanceof FileReader) return 'object';
  return Object.prototype.toString.call(data).slice(1, -1).split(' ')[1].toLowerCase();
}
```



## [21. implement JSON.stringify()](https://bigfrontend.dev/problem/implement-JSON-stringify)

### 题目

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

### 答案

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

严谨一点的写法

```js
/**
 * @param {any} data
 * @return {string}
 */
function stringify(data) {
  if ([NaN, null, undefined, Infinity].includes(data)) {
    return 'null';
  }
  const type = typeof data;
  switch (type) {
    case 'function': return undefined;
    case 'bigint': throw Error('bigints are not supported');
    case 'string': return `"${data}"`;
    case 'object': {
      if (Array.isArray(data)) {
        return `[${data.map(e => ((typeof e) == 'symbol') ? 'null' : stringify(e)).join()}]`;
      }
      if (data instanceof Date) {
        return `"${data.toISOString()}"`;
      }
      return '{' + Object.keys(data).filter(key => data[key] !== undefined).map(key => `"${key}":${stringify(data[key])}`).join() + '}';
    }
    default: return String(data);
  }
}
```



## [22. implement JSON.parse()](https://bigfrontend.dev/problem/implement-JSON-parse)

### 题目

I believe you've used `JSON.stringify()` before, do you know the details of how it handles arbitrary data?

Please have a guess on the details and then take a look at the [explanation on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify), it is actually pretty complex.

In this problem, you are asked to implement your own version of `JSON.stringify()`.

In a real interview, you are not expected to cover all the cases, just decide the scope with interviewer. But for a goal of practicing, your code here will be tested against a lot of data types. Please try to cover as much as you can.

Attention to the circular reference.

*note*

**Don't use JSON.parse() in your code here** It doesn't help you practicing your skills.

Related Problems

[21. implement JSON.stringify() ](https://bigfrontend.dev/problem/implement-JSON-stringify)

### 答案

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
  if(str === '') {
    throw Error();
  }
  if(str[0] === "'") {
    throw Error();
  }
  if(str === 'null') {
    return null;
  }
  if(str === '{}') {
    return {};
  }
  if(str === '[]') {
    return [];
  }
  if(str === 'true') {
    return true;
  }
  if(str === 'false') {
    return false;
  }
  if(str[0] === '"') {
    return str.slice(1, -1);
  }
  if(+str === +str) {
    return Number(str);
  }
  if(str[0] === '{') {
    return str.slice(1, -1).split(',').reduce((acc, item) => {
      const index = item.indexOf(':');
      const key = item.slice(0, index)
      const value = item.slice(index + 1);
      acc[parse(key)] = parse(value);
      return acc;
    }, {});
  }
  if(str[0] === '[') {
    return str.slice(1, -1).split(',').map((value) => parse(value));
  }
}
```



## [23. create a sum()](https://bigfrontend.dev/problem/create-a-sum)

### 题目

Create a `sum()`, which makes following possible

```js
const sum1 = sum(1)
sum1(2) == 3 // true
sum1(3) == 4 // true
sum(1)(2)(3) == 6 // true
sum(5)(-1)(2) == 6 // true
```

### 答案

函数柯里化+valueOf求值

```js
/**
 * @param {number} num
 */
function sum(num) {
  const func = function(num2) { // #4
    return num2 ? sum(num + num2) : num; // #3
  }
  
  func.valueOf = () => num; // #2
  return func; // #1
}
/*** ==== Explanation  ====

We know that `sum(1)(2)` can be done by returning a function from a function. Example:


function sum(num) {
  return function(num2) {
    return num+num2;
  }
}


but we can have `sum(1)(2)....(n)` up to `n`.

How do we solve such problems? We first see a pattern, the pattern is that we need to return function `n` times.
When we see a pattern then we can write concise code using recursion. <br />

So I solved this problem using recursion. But before that let's demystify these 8 lines of code. <br />

#1: Why do we need to use `func` variable, why can't we just directly return `function(num2)...` (#4)? <br />

Because we are comparing non-primitive (Object, functions are Objects in JS) value against a primitive value (Number). <br />
`sum(1)(2)(3) == 6`

When we do such comparisons then JS has to do "type coercion". How does JS do this?

It has `valueOf` and `toString` to do that. Essentially, one of them will be called. 
What we do here is that we override that method and tell the JS engine to return our custom value (which is `num`) in our case.
That's why we needed to store #4 in a variable so that we can override the `valueOf` method.

#2: Okay, I get it that you wanted to use the `valueOf` method, but why do you on this beautiful earth want to do that? 
Because if `sum(1)(2)` will return us another function and we can't compare below -

`function(num2) { return num2 ? sum(num+num2) : num }  == 3`

So what we do is we tell the JS engine to use our `valueOf` method to return value, which is 'num'.
So we can now compare `3 == 3`

#3: Okay, then why do we have ternary on #3?
Because in case you want to use call `sum` function normally and get value out of it.
`sum(1)(2)()` will return 3

***/
```

With Symbol

```js
/**
 * @param {number} num
 */
function sum(num) {
  const func = function(num2) { // #4
    return num2 ? sum(num + num2) : num; // #3
  }
  
  func[Symbol.toPrimitive] = () => num; // #2
  return func; // #1
}
```



## [24. create a sum()](https://bigfrontend.dev/problem/create-a-priority-queue-in-JavaScript)

### 题目

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

### 答案

实现堆，这个直接抄了，比较复杂，主要是上移和下移操作，参考算法导论中的内容

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



## [25. Reorder array with new indexes](https://bigfrontend.dev/problem/reorder-array-with-new-indexes)

### 题目

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

### 答案

**The key insight is that you have to swap the newOrder array when you swap the items array so that order information is preserved. Notice the inner while loop as well, the current testcase is not the best**

```js
/**
 * @param {any[]} items
 * @param {number[]} newOrder
 * @return {void}
 */
function sort(items, newOrder) {
  for (let i = 0; i < newOrder.length; i++) {
    while (newOrder[i] !== i) {
      swap(items, i, newOrder[i]);
      swap(newOrder, i, newOrder[i]);
    }
  }
}

function swap(arr, i, j) {
  [arr[i], arr[j]] = [arr[j], arr[i]];
} 
```



## [26. implement Object.assign()](https://bigfrontend.dev/problem/implement-object-assign)

### 题目

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

### 答案

```js
/**
 * @param {any} target
 * @param {any[]} sources
 * @return {object}
 */
function objectAssign(target, ...sources) {
  if (target === null || target === undefined) {
    throw new Error('Not an object');
  }
  
  if (typeof target !== `object`) {
    target = new target.__proto__.constructor(target); 
  }
  
  for (const source of sources) {
    if (source === null || source === undefined) {
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



## [27. implement completeAssign()](https://bigfrontend.dev/problem/implement-completeAssign)

### 题目

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

### 答案

类似的做法

```js
function completeAssign(target, ...sources) {
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



## [28. implement clearAllTimeout()](https://bigfrontend.dev/problem/implement-clearAllTimeout)

### 题目

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

### 答案

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
  for (const timer of timerCache) {
    clearTimeout(timer);
  }
}
```



## [29. implement async helper - `sequence()`](https://bigfrontend.dev/problem/implement-async-helper-sequence)

### 题目

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

### 答案

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



## [30. implement async helper - `parallel()](https://bigfrontend.dev/problem/implement-async-helper-parallel)`

### 题目

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

### 答案

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

我看了大佬的简化版写法，很是优雅

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
const promisify = fn => input => new Promise((res, rej) => {
  fn((err, output) => err ? rej(err) : res(output), input);
})

function parallel(fns) {
  return (cb, input) => {
    Promise
      .all(fns.map(fn => promisify(fn)(input)))
      .then(outputs => cb(undefined, outputs))
      .catch(err => cb(err, undefined));
  };
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



## [31. implement async helper - `race()`](https://bigfrontend.dev/problem/implement-async-helper-race)

### 题目

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

### 答案

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

直接使用Promise.race逻辑

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



## [32. implement `Promise.all()`](https://bigfrontend.dev/problem/implement-Promise-all)

### 题目

> The Promise.all() method takes an iterable of promises as an input, and returns a single Promise that resolves to an array of the results of the input promises

source - [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)

Could you write your own `all()` ? which should works the same as `Promise.all()`

*note*

**Do not use Promise.all() directly**, it is not helping

Related Problems

[30. implement async helper - `parallel()`](https://bigfrontend.dev/problem/implement-async-helper-parallel)plement `Promise.allSettled()`](https://bigfrontend.dev/problem/implement-Promise-allSettled)[34. implement `Promise.any()`](https://bigfrontend.dev/problem/implement-Promise-any)ment `Promise.race()`](https://bigfrontend.dev/problem/implement-Promise-race)

### 答案

记录完成的数组

```js
/**
 * @param {Array<any>} promises - notice input might have non-Promises
 * @return {Promise<any[]>}
 */
function all(promises) {
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



## [33. implement `Promise.allSettled()`](https://bigfrontend.dev/problem/implement-Promise-all)

### 题目

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

### 答案

需要标记任务

```js
/**
 * @param {Array<any>} promises - notice that input might contains non-promises
 * @return {Promise<Array<{status: 'fulfilled', value: any} | {status: 'rejected', reason: any}>>}
 */
function allSettled(promises) {
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



## [34. implement `Promise.any()`](https://bigfrontend.dev/problem/implement-Promise-any)

### 题目

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

### 答案

类似的写法

```js
/**
 * @param {Array<Promise>} promises
 * @return {Promise}
 */
function any(promises) {
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



## [35. implement `Promise.race()`](https://bigfrontend.dev/problem/implement-Promise-race)

### 题目

This problem is similar to [31. implement async helper - `race()`](https://bigfrontend.dev/problem/implement-async-helper-race), but with Promise.

> The Promise.race() method returns a promise that fulfills or rejects as soon as one of the promises in an iterable fulfills or rejects, with the value or reason from that promise. [source: MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race)

Can you create a `race()` which works the same as `Promise.race()`?

Related Problems

[32. implement `Promise.all()` ](https://bigfrontend.dev/problem/implement-Promise-all)
[33. implement `Promise.allSettled()` ](https://bigfrontend.dev/problem/implement-Promise-allSettled)
[34. implement `Promise.any()` ](https://bigfrontend.dev/problem/implement-Promise-any)

### 答案

```js
/**
 * @param {Array<Promise>} promises
 * @return {Promise}
 */
function race(promises) {
  if (promises.length === 0) return Promise.resolve();
  return new Promise((resolve, reject) => {
    for (const promise of promises) {
      if (!promise instanceof Promise) promise = Promise.resolve(promise);
      promise.then(res => resolve(res)).catch(err => reject(err));
    }
  })
}
```

还可以更简单，不考虑是否是promise实例

```js
/**
 * @param {Array<Promise>} promises
 * @return {Promise}
 */
function race(promises) {
  return new Promise((resolve, reject) => {
    promises.forEach((promise) => {
      promise.then(resolve, reject);
    });
  });
}
```



## [36. create a fake timer(setTimeout)](https://bigfrontend.dev/problem/create-a-fake-timer)

### 题目

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

### 答案

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



## [37. implement Binary Search (unique)](https://bigfrontend.dev/problem/implement-Binary-Search-Unique)

### 题目

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

### 答案

二分查找

```js

/**
 * @param {number[]} arr - ascending unique array
 * @param {number} target
 * @return {number}
 */
function binarySearch(arr, target){
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



## [38. implement `jest.spyOn()`](https://bigfrontend.dev/problem/implement-spyOn)

### 题目

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

### 答案

返回一个spy对象

```js
/**
 * @param {object} obj
 * @param {string} methodName
 */
function spyOn(obj, methodName) {
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

或者直接用proxy，参考https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/apply

```js
/**
 * @param {object} obj
 * @param {string} methodName
 */
function spyOn(obj, methodName) {
  const spy = {
    calls: [],
  };

  const proxyedObj = new Proxy(obj[methodName], {
    apply: function (target, thisArg, args) {
      spy.calls.push(args);
      return target.apply(thisArg, args);
    }
  })
  obj[methodName] = proxyedObj;

  return spy;
}
```



## [39. implement range()](https://bigfrontend.dev/problem/implement-range)

### 题目

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

### 答案

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

直接返回数组

```js
/**
 * @param {integer} from
 * @param {integer} to
 */
function range(from, to) {
  return new Array(to - from + 1).fill(0).map((_, i) => i + from);
}
```



## [40. implement Bubble Sort](https://bigfrontend.dev/problem/implement-Bubble-Sort)

### 题目

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

### 答案

冒泡排序

```js
/**
 * @param {number[]} arr
 */
function bubbleSort(arr) {
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



## [41. implement Merge Sort](https://bigfrontend.dev/problem/implement-Merge-Sort)

### 题目

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

### 答案

```js
/**
 * @param {number[]} arr
 */
function mergeSort(arr) {
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

简化版

```js
/**
 * @param {number[]} arr
 */
function mergeSort(arr) {
  if (arr.length < 2) return;
  let mid = Math.floor(arr.length / 2);
  const left = arr.slice(0, mid), right = arr.slice(mid);
  mergeSort(left);
  mergeSort(right);
  let l = 0, r = 0;
  while (l < left.length || r < right.length) {
    if (r === right.length || (l < left.length && left[l] <= right[r]))
      arr[l + r] = left[l++];
    else
      arr[l + r] = right[r++];
  }
}
```



## 42. implement Insertion Sort

### 题目

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

### 答案

插入排序

```js
/**
 * @param {number[]} arr
 */
function insertionSort(arr) {
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

简单版

```js
/**
 * @param {number[]} arr
 */
function insertionSort(arr) {
  for(let i = 1; i < arr.length; i++) {
    for(let j = 0; j < i; j++) {
      const insert = arr[i];
      const curr = arr[j];
      if(curr > insert) {
        [arr[i], arr[j]] = [arr[j], arr[i]];
      }
    }
  }
}
```



## [43. implement Quick Sort](https://bigfrontend.dev/problem/implement-Quick-Sort)

### 题目

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

### 答案

快速排序

```js
/**
 * @param {number[]} arr
 */
function quickSort(arr) {
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

随机种子

```js
/**
 * @param {number[]} arr
 */
function quickSort(arr, lo = 0, hi = arr.length - 1) {
  if (lo >= hi) return;
  const pivot = partition(arr, lo, hi) ;
  quickSort(arr, lo, pivot-1);
  quickSort(arr, pivot + 1, hi);
}

function partition(arr, lo, hi) {
  const p = random(lo, hi + 1);
  const pivotNum = arr[p];
  
  // move pivot to the rightmost
  swap(arr, p, hi);

  let insertIndex = lo;
  for (let i = lo; i < hi; i++) {
    if (arr[i] <= pivotNum) {
      swap(arr, insertIndex, i);
      insertIndex++;
    }
  }

  // move pivot back to insertIndex
  swap(arr, insertIndex, hi);
  return insertIndex;
}

function swap(arr, i, j) {
  const tmp = arr[i];
  arr[i] = arr[j];
  arr[j] = tmp;
}

function random(lo, hi) {
  return Math.floor(Math.random() * (hi - lo)) + lo;
}
```



## [44. implement Selection Sort](https://bigfrontend.dev/problem/implement-Selection-Sort)

### 题目

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

### 答案

```js
/**
 * @param {number[]} arr
 */
function selectionSort(arr) {
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



## [45. find the K-th largest element in an unsorted array](https://bigfrontend.dev/problem/find-the-K-th-largest-element-in-an-unsorted-array)

### 题目

You are given an unsorted array of numbers, which might have duplicates, find the K-th largest element.

The naive approach would be sort it first, but it costs `O(nlogn)`, could you find a better approach?

Maybe you can recall what is happening in [Quick Sort](https://bigfrontend.dev/problem/implement-Quick-Sort) or [Priority Queue](https://bigfrontend.dev/problem/create-a-priority-queue-in-JavaScript)

Related Problems

[43. implement Quick Sort ](https://bigfrontend.dev/problem/implement-Quick-Sort)

### 答案

快速选择

```js
/**
 * @param {number[]} arr
 * @param {number} k
 */
function findKThLargest(arr, k) {
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

简化版

```js

/**
 * @param {number[]} arr
 * @param {number} k
 */
function findKThLargest(arr, k, lo = 0, hi = arr.length - 1) {
  const pivot = partition(arr, lo, hi);
  if (pivot === arr.length - k) return arr[pivot];
  if (pivot < arr.length - k) return findKThLargest(arr, k, pivot + 1, hi);
  else return findKThLargest(arr, k, lo, pivot - 1);
}

function partition(arr, lo, hi) {
  let pivot = lo;
  for (let i = lo; i < hi; i++) {
    if (arr[i] < arr[hi]) swap(i, pivot++, arr);
  }
  swap(pivot, hi, arr);
  return pivot;
}

function swap(a, b, arr) {
  [arr[a], arr[b]] = [arr[b], arr[a]];
}
```

或者使用小根堆，保证堆顶是第k小的元素

```js

/**
 * @param {number[]} arr
 * @param {number} k
 */
function findKThLargest(arr, k) {
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



## [46. implement `_.once()`](https://bigfrontend.dev/problem/implement-once)

### 题目

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

### 答案

本质就是单例模式

```js
/**
 * @param {Function} func
 * @return {Function}
 */
function once(func) {
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



## [47. reverse a linked list](https://bigfrontend.dev/problem/Reverse-a-linked-list)

### 题目

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

### 答案

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

用解构赋值非常优雅的一版

In this problem, temporary variables are required so you don't overwrite variable values that you still need later on in the loop. (e.g. (1) `prev = node;` (2) `node.next = prev;` Here, `prev` was overwritten in (1), therefore `node.next` is incorrectly assigned).

Notice the use of array destructuring here to efficiently store temporary variables: The array on the right is a 'temporary variable array', which holds the references stored in the *current* `prev`, `node.next`, and `node` variables. For each iteration of the while loop:

- the `node.next` variable points to the temporary `prev` reference - i.e. the current node's next will point to the previous node, reversing the link
- the `node` variable points to the temporary `node.next` reference - the 'node' runner variable will progress to the next node in the linked list
- the `prev` variable points to temporary `node` reference - the 'prev' runner variable will progress to the previous node.

Following this process to the end of the list will reverse all the links, hence reversing the linked list

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
    let cur = list, prev = null;
    while (cur !== null) {
        [cur.next, cur, prev] = [prev, cur.next, cur];
    }
    return prev;
}
```



## [48. search first index with Binary Search(possible duplicate array)](https://bigfrontend.dev/problem/search-first-index-with-Binary-Search-duplicate-array)

### 题目

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

### 答案

lowerbound函数

```js
/**
 * @param {number[]} arr - ascending array with duplicates
 * @param {number} target
 * @return {number}
 */
function firstIndex(arr, target){
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



## [49. search last index with Binary Search(possible duplicate array)](https://bigfrontend.dev/problem/search-last-index-with-Binary-Search-possible-duplicate-array)

### 题目

This is a variation of [37. implement Binary Search (unique)](https://bigfrontend.dev/problem/implement-Binary-Search-Unique).

Your are given a sorted ascending array of number, but **might have duplicates**, you are asked to return the **last index** of a target number.

If not found return -1.

*note*

Please don't use `Array.prototype.lastIndexOf()`, it is not our goal.

Related Problems

[37. implement Binary Search (unique) ](https://bigfrontend.dev/problem/implement-Binary-Search-Unique)
[48. search first index with Binary Search(possible duplicate array) ](https://bigfrontend.dev/problem/search-first-index-with-Binary-Search-duplicate-array)

### 答案

类似的upperbound

```js
/**
 * @param {number[]} arr - ascending array with duplicates
 * @param {number} target
 * @return {number}
 */
function lastIndex(arr, target){
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



## [50. search element right before target with Binary Search(possible duplicate array)](https://bigfrontend.dev/problem/search-element-right-before-target-with-Binary-Search-possible-duplicate-array)

### 题目

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

### 答案

使用了lowerbound找到左边界，然后找到左边界左边的数

```js
/**
 * @param {number[]} arr - ascending array with duplicates
 * @param {number} target
 * @return {number}
 */
function elementBefore(arr, target){
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



## [51. search element right after target with Binary Search(possible duplicate array)](https://bigfrontend.dev/problem/search-element-right-after-target-with-Binary-Search-possible-duplicate-array)

### 题目

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

### 答案

使用了upperbound找到右边界，然后找到右边界右边的数

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



## [52. create a middleware system](https://bigfrontend.dev/problem/create-a-middleware-system)

### 题目

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

### 答案

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

放入队列时执行错误

```js
class Middleware {
  /**
   * @param {MiddlewareFunc} func
   */
  constructor() {
    this.callbacks = [];
    this.errHandlers = [];
  }

  use(func) {
    if (func.length === 2) {
      this.callbacks.push(func);
    }
    if (func.length === 3) {
      this.errHandlers.push(func);
    }
  }

  /**
   * @param {Request} req
   */
  start(req) {
    let idx = 0;
    let errIdx = 0;
    let self = this;
    function next(nextError) {
      let args = [req, next];
      let func;
      if (nextError) {
        func = self.errHandlers[errIdx++];
        args.unshift(nextError);
      } else {
        func = self.callbacks[idx++];
      }
      try {
        func && func(...args);
      } catch(error) {
        next(error);
      }
    }
    next();
  }
}
```

l另一版简化答案

```js
class Middleware {
  /**
   * @param {MiddlewareFunc} func
   */
  constructor() {
    // Create a function queue to help with execution
    this.funcs = [];
    this.req = null;
  }
  use(func) {
    // Push the function into Queue
    this.funcs.push(func);
  }

  /**
   * @param {Request} req
   */
  start(req) {
    this.req = req;
    // Start the chain
    this.next();
  }

  next = (err) => {
    // take out the function to execute from the queue
    const toExecute = this.funcs.shift();
    // Catch execution error when a function throw an error
    try {
      // args length tells us if its a normal call or an error call
      if (toExecute.length === 2) {
        // there is no error, execute the function with current request and next()
        if (!err) {
          toExecute(this.req, this.next);
        }
        // There is an error, call next() immediately for error handling. This will now keep on dequeuing funs queue 
        // till we get an error handler function with 3 args 
        else{
          this.next(err);
        }
      }
      // There's an error and now we got a func with more than 2 args, i.e. an error handler
      else {
        toExecute(err, this.req, this.next);
      }
    }
    catch (e) {
      this.next(e);
    }

  }
}
```



## [53. write your own `extends` in es5](https://bigfrontend.dev/problem/write-your-own-extends-in-es5)

### 题目

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

### 答案

改变原型链

```js
const myExtends = (SuperType, SubType) => {
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



## [54. flatten Thunk](https://bigfrontend.dev/problem/flatten-Thunk)

### 题目

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

### 答案

返回一个闭包

```js
/**
 * @param {Thunk} thunk
 * @return {Thunk}
 */
// 简单来说就是劫持callback,自己实现一个支持链式调用的callback;
function flattenThunk(thunk) { // thunk: 即案例的func3
  return function(cb) { // cb: 给定的回调
    function wrapper(err, res) {
      typeof res === "function" ? res(wrapper) : cb(err, res); // 如果data是一个"Thunk"即函数
    }
    thunk(wrapper);
  }
}
```



## [55. highlight keywords in HTML string](https://bigfrontend.dev/problem/highlight-keywords-in-HTML-string)

### 题目

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

### 答案

分隔单词，进行操作，正则表达式

```js
/**
 * @param {string} html
 * @param {string[]} keywords
 */
function highlightKeywords(html, keywords) {
  const regexp = new RegExp(keywords.join('|'), 'gi');
  return html.split(' ').map((word) => {
    if (keywords.includes(word)) return `<em>${word}</em>`;
    return word.replace(regexp, (w) => `<em>${w}</em>`).replace('</em><em>', '');
  }).join(' ');
};
```

This fails when using words with the same letters.

```javascript
highlightKeywords(
  'Hello JavaScript Lovers', 
  ['Hello', 'Front', 'Scrip', 'vaScript', 'Java']
)
```

字符串操作

```js
/**
 * @param {string} html
 * @param {string[]} keywords
 */
function highlightKeywords(html, keywords) {
  const  bold = new Array(html.length + 1);
    
  keywords.forEach(w => {
      let start = html.indexOf(w, 0);
      // until you keep getting valid pattern mark bold array true at those spots
      while (start != -1) {
          bold.fill(true, start, start + w.length);
          start = html.indexOf(w, start + 1);
      }
  });
  
  let res = (bold[0] ? "<em>" : "");
  
  for (let i = 0; i < bold.length - 1; i++) {
      res += html.charAt(i);
      
      // notbold && bold we start new tag
      if (!bold[i] && bold[i + 1])
      res += "<em>";
      // bold && notbold we end current tag
      else if (bold[i] && !bold[i + 1])
      res += "</em>";
  }
  return res;
};
```



## [56. call APIs with pagination](https://bigfrontend.dev/problem/call-APIs-with-pagination)

### 题目

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

### 答案

记录顺序

```js
// fetchList is provided for you
// const fetchList = (since?: number) => 
//   Promise<{items: Array<{id: number}>}>

/**
 * Using async/await loop 
 */
// you can change this to generator function if you want
const fetchListWithAmount = async (amount = 5) => {
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

重写异步迭代逻辑

```js
// fetchList is provided for you
// const fetchList = (since?: number) => 
//   Promise<{items: Array<{id: number}>}>

/**
 * Using async iterator 
 */
// you can change this to generator function if you want
const fetchListWithAmount = async (amount = 5) => {
  const result = [];

  function fetchListIterator() {
    let totalAmountFetched = 0;
    let cursor;

    return {
      [Symbol.asyncIterator]() {
        return {
          async next() {
            const { items } = await fetchList(cursor);
            // If API is exhausted OR we reached desired amount -> stop
            if (items.length === 0 || totalAmountFetched > amount) {
              return {
                done: true
              };
            }

            totalAmountFetched += items.length;
            cursor = items[items.length - 1].id;

            return {
              done: false,
              value: items
            }
          }
        }
      }
    }
  }

  for await (const nextItems of fetchListIterator()) {
    result.push(...nextItems);
  }
  return result.slice(0, amount);
}
```

设计异步生成器

```js
// fetchList is provided for you
// const fetchList = (since?: number) => 
//   Promise<{items: Array<{id: number}>}>

/**
 * Using async generator 
 */
// you can change this to generator function if you want
const fetchListWithAmount = async (amount = 5) => {
  const result = [];

  async function* fetchListGenerator() {
    let totalAmountFetched = 0;
    let cursor;

    while (totalAmountFetched < amount) {
      const { items } = await fetchList(cursor);
      if (items.length === 0) break;
      cursor = items[items.length - 1].id;
      totalAmountFetched += items.length;
      yield items;
    }
  }

  for await (const nextItems of fetchListGenerator()) {
    result.push(...nextItems);
  }
  return result.slice(0, amount);
}
```

使用异步promise

```js
// fetchList is provided for you
// const fetchList = (since?: number) => 
//   Promise<{items: Array<{id: number}>}>

/**
 * Using recursion and Promise
 */
// you can change this to generator function if you want
const fetchListWithAmount = async (amount = 5) => {
  return new Promise((resolve) => {
    const result = [];
    getItems();

    function getItems(cursor) {
      fetchList(cursor).then(({ items }) => {
        result.push(...items);
        if (items.length === 0 || items.length >= amount) {
          return resolve(result.slice(0, amount));
        }
        getItems(items[items.length - 1].id);
      });
    }
  });
}
```



## [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable)

### 题目

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

### 答案

If you were not familiar with Observable pattern like me, I think it's important to have a good understanding of how Observable works. Then looking at this question, it's just adding a wrapper function to present a different interface mentioned below

After watching Javascript Design Patterns #5 - Observer Pattern, by DevSage on Youtube, it helped me understand the basics

Now looking at the problem prompt, I realized that observer and subscriber are exactly the same thing.

So the problem now is, how can we add some functionalities (unsubscribe) and state (unsubscribed) to the subscriber provided to us via the subscribe method? We use a wrapper function (or decorator for fancy name) -> I read https://javascript.info/call-apply-decorators to understand what a decorator is.

and then the code

```js
class Observable {
  
  constructor(setup) {
    this.setup = setup;
  }
 
  subscribe(subscriber) {
    // equivalent to fire
    // a wrapper function/ object 
    // is used to share the closure of outer function and modify the logics
    const subscriberWrapper = {
      unsubscribed: false,
      next(value) {
        if (this.unsubscribed) return;
        // we are relying on the scope of subscriber
        if (subscriber instanceof Function) {
          return subscriber(value);
        }
        return subscriber.next ? subscriber.next(value) : null;
      },
      error(value) {
        if (this.unsubscribed) return;
        this.unsubscribe();
        return subscriber.error ? subscriber.error(value) : null;
      },
      complete() {
        if (this.unsubscribed) return;
        this.unsubscribe();
        return subscriber.complete ? subscriber.complete() : null;
      },
      unsubscribe() {
        this.unsubscribed = true;
      }
    }
    this.setup(subscriberWrapper);
    return subscriberWrapper;
  }
}
```

** [if you watched the youtube video and can't figure out how that applies to the problem] In the video, the subscribers/observers is an array of functions, which are iterated to trigger the observer to do its thing. the reason that observers is an array is because observable pattern is a one to many pattern, meaning one observable can be subscribed by many observers. We should note that for this question, we are implementing a 1-1 relationship, with the only subscriber provided to you



## [58. get DOM tree height](https://bigfrontend.dev/problem/get-DOM-tree-height)

### 题目

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

### 答案

使用递归层次DFS

```js
/**
 * @param {HTMLElement | null} tree
 * @return {number}
 */
function getHeight(tree) {
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



## [59. create a browser history](https://bigfrontend.dev/problem/create-a-browser-history)

### 题目

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

### 答案

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

简化版

```js
class BrowserHistory {
  
  /**
   * @param {string} url
   * if url is set, it means new tab with url
   * otherwise, it is empty new tab
   */
  constructor(url) {
    this.history = [];
    this.pointer = 0;
    if(url !== undefined) {
        this.history.push(url);
    }
  }
  /**
   * @param { string } url
   */
  visit(url) {
    this.history.length = this.pointer+1;
    this.history.push(url);
    this.pointer++;
  }
  
  /**
   * @return {string} current url
   */
  get current() {
    return this.history[this.pointer];
  }
  
  // go to previous entry
  goBack() {
     this.pointer = Math.max(0, --this.pointer);
  }
  
  // go to next visited url
  forward() {
     this.pointer = Math.min(this.history.length - 1, ++this.pointer);
  }
}
```



## [60. create your own `new` operator](https://bigfrontend.dev/problem/create-your-own-new-operator)

### 题目

[`new` operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new) is used to create new instance objects.

Do you know exactly what `new` does?

You are asked to implement `myNew()`, which should return an object just as what `new` does but without using `new`.

Pay attention to the return type of constructor.

### 答案

**Important:** https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new

```js
/**
 * @param {Function} constructor
 * @param {any[]} args - argument passed to the constructor
 * `myNew(constructor, ...args)` should return the same as `new constructor(...args)`
 */
const myNew = (constructor, ...args) => {
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



## [61. create your own `Function.prototype.call](https://bigfrontend.dev/problem/create-call-method)`

### 题目

[Function.prototype.call](https://tc39.es/ecma262/#sec-function.prototype.call) is very useful when we want to alter the `this` of a function.

Can you implement your own `myCall`, which returns the same result as `Function.prototype.call`?

For the [newest ECMAScript spec](https://tc39.es/ecma262/#sec-function.prototype.call), `thisArg` are not transformed. And not replaced with window in [Strict Mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode).

Your implementation should follow above spec and do what *non* Strict Mode does.

`Function.prototype.cal/apply/bind` and [Reflect.apply](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/apply) should not be used.

### 答案

```js
Function.prototype.mycall = function(thisArg, ...args) {
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



## [62. implement BigInt addition](https://bigfrontend.dev/problem/add-BigInt-string)

### 题目

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

### 答案

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



## [63. create `_.cloneDeep()`](https://bigfrontend.dev/problem/create-cloneDeep)

### 题目

`Object.assign()` could be used to do shallow copy, while for recursive deep copy, [_.cloneDeep](https://lodash.com/docs/4.17.15#cloneDeep) could be very useful.

Can you create your own `_.cloneDeep()`?

The lodash implementation actually covers a lot of data types, for simplicity, your code just need to cover

1. [primitive types](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#Primitive_values) and their wrapper Object
2. Plain Objects (Object literal) with all enumerable properties
3. Array

### 答案

处理循环引用

```js
// Use WeakMap that stores cloned results to handle circular reference.
function cloneDeep(data, map = new WeakMap()) {
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
    res[key] = cloneDeep(data[key], map);
  }

  return res;
}
```



## [64. auto-retry Promise on rejection](https://bigfrontend.dev/problem/retry-promise-on-rejection)

### 题目

For a web application, fetching API data is a common task.

But the API calls might fail because of Network problems. Usually we could show a screen for Network Error and ask users to retry.

One approach to handle this is **auto retry when network error occurs**.

You are asked to create a `fetchWithAutoRetry(fetcher, count)`, which automatically fetch again when error happens, until the maximum count is met.

For the problem here, there is no need to detect network error, you can just retry on all promise rejections.

### 答案

循环迭代

```js
/**
 * @param {() => Promise<any>} fetcher
 * @param {number} maximumRetryCount
 * @return {Promise<any>}
 */
function fetchWithAutoRetry(fetcher, maximumRetryCount) {
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

递归

```js
/**
 * @param {() => Promise<any>} fetcher
 * @param {number} maximumRetryCount
 * @return {Promise<any>}
 */
function fetchWithAutoRetry(fetcher, maximumRetryCount) {
  return fetcher().catch((e) => {
    if (maximumRetryCount === 0) {
      throw e;
    } else {
      return fetchWithAutoRetry(fetcher, maximumRetryCount - 1);
    }
  })
}
```



## [65. add comma to number](https://bigfrontend.dev/problem/add-comma-to-number)

### 题目

Given a number, please create a function to add commas as thousand separators.

```js
addComma(1) // '1'
addComma(1000) // '1,000'
addComma(-12345678) // '-12,345,678'
addComma(12345678.12345) // '12,345,678.12345'
```

Input are all valid numbers.

### 答案

正则表达式

```js
/**
 * @param {number} num
 * @return {string}
 */
function addComma(num) {
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



## [66. remove duplicates from an array](https://bigfrontend.dev/problem/remove-duplicates-from-an-array)

### 题目

Given an array containing all kinds of data, please implement a function `deduplicate()` to remove the duplicates.

You should modify the array in place. Order doesn't matter.

> What is time & space cost of your approach?

### 答案

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

还有别的方法，如排序+双指针



## [67. create your own Promise](https://bigfrontend.dev/problem/create-your-own-Promise)

### 题目

[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) is widely used nowadays, hard to think how we handled [Callback Hell](http://callbackhell.com/) in the old times.

Can you implement a `MyPromise` Class by yourself?

At least it should match following requirements

1. new promise: `new MyPromise((resolve, reject) => {})`
2. chaining : `MyPromise.prototype.then()` *[then handlers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) should be called asynchronously*
3. rejection handler: `MyPromise.prototype.catch()`
4. static methods: `MyPromise.resolve()`, `MyPromise.reject()`.

This is a challenging problem. Recommend you read about Promise thoroughly first.

### 答案

#### Explanation

##### The Constructor of `MyPromise`

The `promise` object created by the class `Promise` has a `state` property. The `state` is initially `pending`. So in the constructor of the `MyPromise` class, I need to define a property `state` and set it to `pending`.

```js
class MyPromise {
  constructor() {
    this.state = 'pending';
  }
}
```

##### Handling the function that is passed to `new Promise`

The function is called the **executor**. It is executed immediately and automatically by `new Promise` and is able to `resolve` or `reject` the promise. So I should call it in the `constructor` of the `MyPromise` class with the arguments `resolve` and `reject`.

🙋‍♀️🙋‍♂️ Error thrown by the executor function should be handled. If there is an error, the promise should be rejected.

```js
class MyPromise {
  constructor(executor) {
    // ...
    try {
      executor(/* resolve */, /* reject */);
    } catch (error) {
      // reject the promise
    }
  }
}
```

#### Implementing `resolve` and `reject` that are passed to the executor function

A executor function that resolves the promise looks like this:

```js
function(resolve, reject) {
  setTimeout(() => {
    resolve('Done!');
  }, 1000);
}
```

A executor function that rejects the promise looks like this:

```js
function(resolve, reject) {
  setTimeout(() => {
    reject(new Error('Something went wrong!'));
  }, 1000);
}
```

`resolve` and `reject` are both functions which receive one argument. So I should define the two methods in the class `MyPromise`.

When `resolve` is called, the `state` of the `promise` is changed to `fulfilled`; when `reject` is called, the `state` is changed to `rejected`.

Besides the `state` property, the `promise` object should also have a `result` property to store the promise result. Initially, it is set to `undefined`. Its value changes either to the resolved value if `resolve(value)` is called, or to the rejected value if `reject(error)` is called.

A `Promise` can only be resolved or rejected once.

Thus, I can define my `resolve` and `reject` like the following:

```js
_resolve(value) {
  // Ensure Promise is only resolved or rejected once.
  if (this.state !== 'pending') return;

  this.state = 'fulfilled';
  this.result = value;
}

_reject(error) {
  // Ensure Promise is only resolved or rejected once.
  if (this.state !== 'pending') return;

  this.state = 'rejected';
  this.result = error;
}
```

Since `MyPromise` will also have two static methods `resolve` and `reject`, use underscores to differentiate them.

Because class methods are not bound by default and `_resolve` or `_reject` will be called in the executor function with a plain, undecorated function reference, `this` inside both methods will be lost. Therefore I need to `bind` both methods in the `constructor` or use the experimental fat arrow class methods.

 

#### Implementing `then` method

`Promise.prototype.then()` takes up to two arguments:

1. The first argument is a function that is invoked when the promise is resolved, and receives the result. If it is not a function, it is replaced with a function that simply returns the received result.
2. The second argument is a function that runs when the promise is rejected, and receives the error. If it is not a function, it is replaced with a "Thrower" function.

The syntax is:

```js
promise.then(
  (result) => {
    // handle a successful result
  },
  (error) => {
    // handle an error
  }
);
```

Both arguments are optional.

The `Promise.prototype.then()` returns a `Promise`:

```js
const promise = Promise.resolve('from promise');
const thenPromise = promise.then((result) => {});

console.log(promise);
// Promise {
//   [[PromiseState]]: 'fulfilled',
//   [[PromiseResult]]: 'from promise',
// }
console.log(thenPromise);
// Promise {
//   [[PromiseState]]: 'pending',
//   [[PromiseResult]]: undefined,
// }

setTimeout(() => {
  console.log(thenPromise);
});
// Promise {
//   [[PromiseState]]: 'fulfilled',
//   [[PromiseResult]]: undefined,
// }
```

So the `then` method of the class `MyPromise` should be look like this:

```js
then(onFulfilled, onRejected) {
  return new MyPromise((resolve, reject) => {});
}
```

##### Handling the first argument in my `then` method

Since the callback function runs when the promise is resolved, it cannot be executed within the `then`. For example:

```js
class MyPromise {
  constructor(executor) {
    this.state = 'pending';
    try {
      executor(this._resolve.bind(this), this._reject.bind(this));
    } catch (error) {
      this._reject(error);
    }
  }

  _resolve(result) {
    // ...
    this.state = 'fulfilled';
    this.result = result;
  }

  then(onFulfilled) {
    onFulfilled(this.result);
    // Call onFulfilled asynchronously.
    // queueMicrotask(() => {
    //   onFulfilled(this.result);
    // });
  }
}

const p = new MyPromise((resolve) => {
  resolve(10);
}).then((result) => {
  console.log(result); // 10
});
```

Although the code above seems to work, it doesn't work as intended when the promise is resolved asynchronously, even if the `onFulfilled` is called asynchronously:

```js
const p = new MyPromise((resolve) => {
  setTimeout(() => {
    resolve(10);
  }, 0);
}).then((result) => {
  console.log(result); // undefined
});
```

Therefore, the `onFulfilled` function should be called in the `_resolve` method, and the `then` method just registers the `onFulfilled` function. The `onFulfilled` function is like a subscriber, subscribing to the promised result, and the `then` method is kind of like the function `subscribe` in the Publisher/Subscriber Pattern, which receives subscriber callbacks and stores/registers them in certain data structures.

```js
class MyPromise {
  constructor(executor) {
    // ...
  }

  _resolve(value) {
    //...
    this.onFulfilled(this.result);
  }

  then(onFulfilled) {
    // If onFulfilled is not a function, replace it with a function
    // that simply returns the received result.
    const isOnFulfilledFunction = typeof onFulfilled === 'function';
    this.onFulfilled = isOnFulfilledFunction ? onFulfilled : (value) => value;

    return new Promise((resolve, reject) => {});
  }
}
```

Although the `then` method will be triggered instantly, the callback functions (handlers) will be invoked asynchronously. `Promise` uses the microtask queue to run the callbacks. When a promise is settled, its `.then` handlers are add into the microtask queue. Immediately after every *macrotask*, all tasks from *microtask* queue get executed, prior to any other macrotask runs.

```js
console.log('Start!');

setTimeout(() => {
  console.log('Timeout!');
}, 0);

Promise.resolve('Promise!').then((result) => {
  console.log(result);
});

console.log('End!');

// Logs, in order:
// 'Start!'
// 'End!'
// 'Promise!'
// 'Timeout!'
```

To queue an function for execution in the microtask queue, I can use the function [queueMicrotask()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/queueMicrotask).

```js
_resolve(value) {
  //...
  queueMicroTask(() => {
    this.onFulfilled(this.result);
  });
}
```

Next, I need to handle the return value returned by the `onFulfilled` function. In the `Promise`, if the `onFulfilled` function:

- returns a value, the promise returned by `then` gets resolved with the returned value as its value.

```js
const promise = Promise.resolve('from promise');

const thenPromise = promise.then((result) => {
  return 'from then handler';
});

setTimeout(() => {
  console.log(thenPromise);
});
// Promise {
//   [[PromiseState]]: 'fulfilled',
//   [[PromiseResult]]: 'from then handler',
// }
```

- doesn't return anything, the promise returned by `then` gets resolved with an `undefined` value.

```js
const promise = Promise.resolve('from promise');

const thenPromise = promise.then((result) => {
  console.log(result); // 'from promise'
});

setTimeout(() => {
  console.log(thenPromise);
});
// Promise {
//   [[PromiseState]]: 'fulfilled',
//   [[PromiseResult]]: undefined,
// }
```

- throws an error, the promise returned by `then` gets rejected with the error as its value.

```js
const promise = Promise.resolve('from promise');

const thenPromise = promise.then((result) => {
  throw new Error('error from then handler');
});

setTimeout(() => {
  console.log(thenPromise);
});
// Promise {
//   [[PromiseState]]: 'rejected',
//   [[PromiseResult]]: Error: error from then handler.
// }
```

- returns an already fulfilled promise, the promise returned by `then` gets resolved with that promise's value as its value.

```js
const promise = Promise.resolve('from promise');

const thenPromise = promise.then((result) => {
  return Promise.resolve('resolved promise returned by then handler');
});

setTimeout(() => {
  console.log(thenPromise);
});
// Promise {
//   [[PromiseState]]: 'fulfilled',
//   [[PromiseResult]]: 'resolved promise returned by then handler',
// }
```

- returns an already rejected promise, the promise returned by `then` gets rejected with that promise's value as its value.

```js
const promise = Promise.resolve('promise');

const thenPromise = promise.then((result) => {
  return Promise.reject('rejected promise returned by then handler');
});

setTimeout(() => {
  console.log(thenPromise);
});
// Promise {
//   [[PromiseState]]: 'rejected',
//   [[PromiseResult]]: 'rejected promise returned by then handler',
// }
```

- returns a **pending** promise, the promise returned by `then` gets resolved or rejected after the the promise returned by the handler gets resolved or rejected. The resolved value of the promise returned by `then` will be the same as the resolved value of the promise returned by the handler.

```js
const promise = Promise.resolve('promise');

const thenPromise = promise.then((result) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('resolved promise returned by then handler');
    }, 3000);
  });
});

setTimeout(() => {
  console.log(thenPromise); // Promise {<pending>}
});

setTimeout(() => {
  console.log(thenPromise);
  // Promise {<fulfilled>: "resolved promise returned by then handler"}
}, 4000);
```

Therefore, I need to make the `resolve` and `reject` of the promise returned by `then` available in `_resolve` method, so that they can be invoked after the `onFulfilled` function is completed and receive the value returned by the `onFulfilled` function. We can create two properties in current promise object to store both functions:

```js
then(onFulfilled) {
  // ...
  return new Promise((resolve, reject) => {
    this.thenPromiseResolve = resolve;
    this.thenPromiseReject = reject;
  });
}
```

In `_resolve` I can use `try...catch` to catch the error thrown by the `onFulfilled` method. In the `catch` block, call `this.thenPromiseReject()` with the error as argument to reject the promise returned by `then`. In the `try` block, store the value returned by the `onFulfilled` method. If the returned value is a promise, the promise returned by `then` will only get fulfilled or rejected once that promise gets resolved or rejected. To accomplish this, I can call the `then` method of the returned value and pass `this.thenPromiseResolve()` and `this.thenPromiseReject()` as the `then` handlers. If the return value is not a promise, call `this.thenPromiseResolve()` with the return value as argument to resolve the promise returned by `then`.

```js
_resolve(value) {
  // ...
  queueMicroTask(() => {
    try {
      const returnValue = this.onFulfilled(this.result);
      const isReturnValuePromise = returnValue instanceof MyPromise;

      if (!isReturnValuePromise) {
        this.thenPromiseResolve(returnValue);
      } else {
        returnValue.then(
          this.thenPromiseResolve,
          this.thenPromiseReject,
        );
      }
    } catch (error) {
      this.thenPromiseReject(error);
    }
  });
}
```

In addition, before I call `this.onFulfilled`, I also need to ensure it is not `undefined`, since `.then()` might not be called:

```js
const promise = Promise.resolve('foo');

promise.then((result) => {
  return 'bar';
});
// The promise returned by `then` is resolved, but there
// is no further action with the promise. Therefore, when
// the method `_resolve` of the returned promise runs,
// `this.onFulfilled` is undefined.
```

Add the check:

```js
_resolve(value) {
  // ...
  queueMicroTask(() => {
    if (this.onFulfilled === undefined) return;

    // ...
  });
}
```

##### Handling the second argument in my `then` method

The second callback function runs when the promise is rejected. Like the first argument, `then` should register the second callback function, so that `_reject` can execute it asynchronously whenever the promise is rejected.

```js
then(onFulfilled, onRejected) {
  // ...
  this.onRejected = onRejected;
  // ...
}
_reject(error) {
  // ...
  queueMicrotask(() => {
    try {
      this.onRejected(error);
    } catch (error) {

    }
  })
}
```

Now I need to handle the consequences of calling `onRejected`. In the `Promise`, if the `onRejected` :

- is not a function, the `onRejected` is replaced with a function that throws the received argument, and the promise returned by `then` gets rejected with that promise's value as its value.

```js
const promise = Promise.reject('error from promise');

const thenPromise = promise.then((result) => {});

setTimeout(() => {
  console.log(thenPromise);
});
// Uncaught (in promise) error from promise
// Promise {
//   [[PromiseState]]: 'rejected',
//   [[PromiseResult]]: 'error from promise',
// }
```

- throws an error, the promise returned by `then` gets rejected with the thrown error as its value.

```js
const promise = Promise.reject('error from promise');

const thenPromise = promise.then(null, (reason) => {
  throw new Error('Error from onRejected');
});

setTimeout(() => {
  console.log(thenPromise);
});
// Promise {
//   [[PromiseState]]: 'rejected',
//   [[PromiseResult]]: Error: Error from onRejected
// }
```

- doesn't return anything, the promise returned by `then` gets resolved with an `undefined` value.

```js
const promise = Promise.reject('error from promise');

const thenPromise = promise.then(null, (reason) => {
  console.log(reason);
});

setTimeout(() => {
  console.log(thenPromise);
});
// Promise {
//   [[PromiseState]]: 'fulfilled',
//   [[PromiseResult]]: undefined
// }
```

- returns a value, the promise returned by `then` gets resolved with the returned value as its value.

```js
const promise = Promise.reject('error from promise');

const thenPromise = promise.then(null, (reason) => {
  return 'value returned by onRejected handler';
});

setTimeout(() => {
  console.log(thenPromise);
});
// Promise {
//   [[PromiseState]]: 'fulfilled',
//   [[PromiseResult]]: 'value returned by onRejected handler'
// }
```

- returns an already fulfilled promise, the promise returned by `then` gets resolved with that promise's value as its value.

```js
const promise = Promise.reject('error from promise');

const thenPromise = promise.then(null, (reason) => {
  return Promise.resolve('resolved promise returned by onRejected handler');
});

setTimeout(() => {
  console.log(thenPromise);
});
// Promise {
//   [[PromiseState]]: 'fulfilled',
//   [[PromiseResult]]: 'resolved promise returned by onRejected handler'
// }
```

- returns an already rejected promise, the promise returned by `then` gets rejected with that promise's value as its value.

```js
const promise = Promise.reject('error from promise');

const thenPromise = promise.then(null, (reason) => {
  return Promise.reject('rejected promise returned by onRejected handler');
});

setTimeout(() => {
  console.log(thenPromise);
});
// Promise {
//   [[PromiseState]]: 'rejected',
//   [[PromiseResult]]: 'rejected promise returned by onRejected handler'
// }
```

- returns a **pending** promise, the promise returned by `then` gets resolved or rejected after the promise returned by the handler gets resolved or rejected.

```js
const promise = Promise.reject('error from promise');

const thenPromise = promise.then(null, (reason) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('resolved promise returned by onRejected handler');
    }, 3000);
  });
});

setTimeout(() => {
  console.log(thenPromise); // Promise {<pending>}
});

setTimeout(() => {
  console.log(thenPromise);
  // Promise {<fulfilled>: "resolved promise returned by onRejected handler"}
}, 4000);
```

In my `then` method, I need to replace `onRejected` with a function that throws the 'value' it receives as argument.

```js
then(onFulfilled, onRejected) {
  // ...
  const isOnRejectedFunction = typeof onRejected === 'function';
  this.onRejected = isOnRejectedFunction ? onRejected : (error) => { throw error };
  // ...
}
```

And in my `_reject` method, I first try to call `this.onRejected()` and store its return value. If the returned value is not an instance of `MyPromise`, resolve the promise returned by `then` by calling `this.thenPromiseResolve()` with the returned value as argument. Otherwise call the `then` method of the returned value with `this.thenPromiseResolve()` and `this.thenPromiseReject()` as arguments. Catch any errors thrown by `this.OnRejected()`; call `this.thenPromiseReject()` and pass the error. I also need to ensure `this.onRejected` is not `undefined`.

```js
_reject(error) {
  // ...
  queueMicrotask(() => {
    if (this.onRejected === undefined) return;

    try {
      const returnValue = this.onRejected(this.result);
      const isReturnValuePromise = returnValue instanceof MyPromise;

      if (!isReturnValuePromise) {
        this.thenPromiseResolve(returnValue);
      } else {
        returnValue.then(
          this.thenPromiseResolve;
          this.thenPromiseReject;
        );
      }
    } catch (error) {
      this.thenPromiseReject(error);
    }
  })
}
```

 

Implementing `catch` method

In `Promise`, we can also use the `catch` method to handle rejected cases, and the `catch` method also returns a `Promise`.

The syntax is:

```js
const promise1 = new Promise((resolve, reject) => {
  throw 'Uh-oh!';
});

promise1.catch((error) => {
  console.error(error);
});
// expected output: Uh-oh!
```

It is exactly the same as calling `Promise.prototype.then(null, errorHandlingFunction)`. [In fact, calling obj.catch(onRejected) internally calls obj.then(undefined, onRejected)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch).

So I can define my `catch` method this way:

```js
catch(onRejected) {
  return this.then(null, onRejected);
}
```

 

#### Implementing the static method `MyPromise.resolve()`

The static method `Promise.resolve()` returns a `Promise` object that is resolved with a given value:

- Resolving a string:

```js
Promise.resolve('Success').then(
  function (value) {
    console.log(value); // "Success"
  },
  function (value) {
    // not called
  }
);
```

- Resolving another `Promise`:

```js
const original = Promise.resolve(33);
const cast = Promise.resolve(original);
cast.then(function (value) {
  console.log('value: ' + value);
});
console.log('original === cast ? ' + (original === cast));

// logs, in order:
// original === cast ? true
// value: 33
```

[Promise.resolve() also handle the case where the value is a thenable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve#resolving_thenables_and_throwing_errors), but I don't need to cover it here.

In `MyPromise.resolve`, first check if the value received is a promise; if it is a promise, return the promise; otherwise return a new instance of `MyPromise` and resolve the promise with the given value.

```js
static resolve(value) {
  const isValuePromise = value instanceof MyPromise;

  if (isValuePromise) {
    return value;
  }

  return new MyPromise((resolve) => {
    resolve(value);
  })
}
```

 

#### Implementing the static method `MyPromise.reject()`

The static method `Promise.reject()` returns a `Promise` object that is rejected with a given reason:

```js
Promise.reject(new Error('fail')).then(
  (result) => {
    // not called
  },
  (reason) => {
    console.log(reason); // Error: fail
  }
);
```

`MyPromise.reject()`:

```js
static reject(value) {
  return new MyPromise((_, reject) => {
    reject(value);
  });
}
```

 

#### Solution

```js
class MyPromise {
  constructor(executor) {
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
    // Register consuming functions.
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
    return this.then(undefined, onRejected);
  }

  static resolve(value) {
    const isValuePromise = value instanceof MyPromise;

    if (isValuePromise) {
      return value;
    }

    return new MyPromise((resolve) => {
      resolve(value);
    });
  }

  static reject(value) {
    return new MyPromise((_, reject) => {
      reject(value);
    });
  }
}
```

 

#### Reference

- [Promises](https://javascript.info/promise-basics)
- [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [Promise.prototype.then()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then)
- [Promise.prototype.catch()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)
- [Promise.resolve()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve)
- [Promise.reject()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/reject)
- [Using microtasks in JavaScript with queueMicrotask()](https://developer.mozilla.org/en-US/docs/Web/API/HTML_DOM_API/Microtask_guide)
- [Event loop: microtasks and macrotasks](https://javascript.info/event-loop)
- [Microtasks](https://javascript.info/microtask-queue)



## [68. get DOM tags](https://bigfrontend.dev/problem/get-DOM-tags)

### 题目

Given a DOM tree, please return all the tag names it has.

Your function should return a unique array of tags names in lowercase, order doesn't matter.

### 答案

遍历节点，用set保存

```js
/**
 * @param {HTMLElement} tree
 * @return {string[]}
 */
function getTags(tree) {
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

或者队列

```js
/**
 * @param {HTMLElement} tree
 * @return {string[]}
 */
function getTags(tree) {
  const treeWalker = document.createTreeWalker(tree, NodeFilter.SHOW_ELEMENT = true);
  ans = new Set();
  let cur = treeWalker.currentNode;
  while (cur) {
    ans.add(cur.tagName.toLowerCase());
    cur = treeWalker.nextNode();
  }
  return Array.from(ans);
}
```



## [69. implement deep equal `_.isEqual()`](https://bigfrontend.dev/problem/implement-deep-equal-isEqual)

### 题目

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

### 答案

深度遍历，找到最后的结果

```js
/**
 * @param {any} a
 * @param {any} b
 * @return {boolean}
 */
function isEqual(a, b, map = new WeakMap()) {
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



## [70. implement Observable.from()](https://bigfrontend.dev/problem/implement-Observable-from)

### 题目

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

### 答案

分类讨论

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

直接根据每种情况写出对应的函数

```js
/**
 * @param {Array | ArrayLike | Promise | Iterable | Observable} input
 * @return {Observable}
 */
function from(input) {
  if (input instanceof Observable) {
    return observableFromObservable(input);
  }
  if (input instanceof Promise) {
    return observableFromPromise(input);
  }
  if(Array.isArray(input) || typeof input[Symbol.iterator] === 'function') {
    return observableFromIterable(input);
  }
  if('length' in input) {
    return observableFromArrayLike(input);
  }
  throw new Error('Incorrect input type');
}


function observableFromObservable(input) {
  return new Observable((sub) => {
    input.subscribe(sub)
  });
}

function observableFromPromise(input) {
  return new Observable((sub) => {
      input.then((val) => {
        sub.next(val);
      }, (err) => {
        sub.error(err);
      }).then(() => {
        sub.complete();
      });
  });
}

function observableFromIterable(input) {
  return new Observable((sub) => {
    try {
      for(let el of input) {
        sub.next(el);
      }
    } catch (err) {
      sub.error(err);
    }
    sub.complete();
  });
}

function observableFromArrayLike(input) {
  return new Observable((sub) => {
    try {
      for(let i = 0; i < input.length; i++) {
        sub.next(input[i]);
      }
    } catch (err) {
      sub.error(err);
    }
    sub.complete();
  });
}
```



## [71. implement Observable Subject](https://bigfrontend.dev/problem/implement-Observable-Subject)

### 题目

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

### 答案

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

也可以利用箭头函数实现this指向实例对象

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
    this.subscribers = [];
  }
  subscribe(subscriber) {
    const sub = new Observer(subscriber);
    this.subscribers.push(sub);
    return {
      unsubscribe: () => {
        this.subscribers = this.subscribers.filter(s => s !== sub);
      }
    }
  }
  next = (value) => {
    this.subscribers.forEach((subscriber) => {
      subscriber.next(value);
    });
  }
  error = (err) => {
    this.subscribers.forEach((subscriber) => {
      subscriber.error(err);
    });
  }
  complete = () => {
    this.subscribers.forEach((subscriber) => {
      subscriber.complete();
    });
  }
}
```



## [72. implement Observable interval()](https://bigfrontend.dev/problem/implement-Observable-interval)

### 题目

This is a follow-up on [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable).

Suppose you have solved [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable), here you are asked to implement a creation operator `interval()`.

From the [document](https://rxjs-dev.firebaseapp.com/api/index/function/interval), `interval()`

> Creates an Observable that emits sequential numbers every specified interval of time

```js
interval(1000).subscribe(console.log);
```

Above code prints 0, 1, 2 .... with an interval of 1 seconds.

**Note** Observable is already given for you, no need to create it.

### 答案

使用闭包保存数据，返回一个observe变量

```js
/**
 * @param {number} period
 * @return {Observable}
 */
function interval(period) {
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

只要将数据初始化放在定时器外面即可

```js
/**
 * @param {number} period
 * @return {Observable}
 */
/**
 * @param {number} period
 * @return {Observable}
 */
function interval(period) {
  return new Observable((subscriber) => {
    let num = 0;
    setInterval(() => {
      // subscriber.next(num++);
      subscriber.next(num);
      num++;
    }, period);
  })
}
```



## [73. implement Observable fromEvent()](https://bigfrontend.dev/problem/implement-Observable-fromEvent)

### 题目

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



## [74. implement Observable Transformation Operators](https://bigfrontend.dev/problem/implement-Observable-transformation-operators)

### 题目

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

### 答案

重新修改事件

```js
/**
 * @param {any} input
 * @return {(observable: Observable) => Observable}
 * returns a function which trasnform Observable
 */
function map(transform) {
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



## [75. implement BigInt subtraction](https://bigfrontend.dev/problem/implement-BigInt-subtraction)

### 题目

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

### 答案

其实本质和大数加法没有不同

```js
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
function subtract(num1, num2) {
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



## [76. implement BigInt addition with sign](https://bigfrontend.dev/problem/implement-BigInt-addition-with-sign)

### 题目

This is a follow-up on [62. implement BigInt addition](https://bigfrontend.dev/problem/add-BigInt-string).

You are asked to implement a string addition function, **with possible negative integers**. Also, '+' plus sign should also be supported

```js
add('-999999999999999999', '-1')
// '-1000000000000000000'

add('-999999999999999999', '+1')
// '-999999999999999998'
```

Don't use BigInt directly, it is not our goal here.

Related Problems

[62. implement BigInt addition ](https://bigfrontend.dev/problem/add-BigInt-string)
[75. implement BigInt subtraction ](https://bigfrontend.dev/problem/implement-BigInt-subtraction)
[77. implement BigInt subtraction with sign](https://bigfrontend.dev/problem/implement-BigInt-subtraction-with-sign)

### 答案

先判断符号，再进行计算

```js
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
function add(num1, num2) {
  /**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
function add(num1, num2) {
  const isNum1Negative = num1[0] == '-';
  const isNum2Negative = num2[0] == '-';
  // remove sign
  num1 = num1.replace(/^[+|-]/, '');
  num2 = num2.replace(/^[+|-]/, '');
  if (isNum1Negative == isNum2Negative) {
    return (isNum1Negative ? '-' : '') + absAdd(num1, num2);
  } else {
    const res = absSub(num1, num2);
    if (res[0] == '-' && isNum1Negative) {
      return res.slice(1);
    }
    return (isNum1Negative ? '-' : '') + res;
  }
}

function absAdd(num1, num2) {
  const maxLen = Math.max(num1.length, num2.length);
  num1 = num1.padStart(maxLen, '0')
  num2 = num2.padStart(maxLen, '0')
  let extra = 0;
  let ans = '';
  for (let i = maxLen - 1; i >= 0; i--) {
    const n1 = parseInt(num1[i]), n2 = parseInt(num2[i]);
    const currNum = n1 + n2 + extra;
    if (currNum >= 10) {
      extra = 1;
      ans = String(currNum - 10) + ans;
    } else {
      extra = 0;
      ans = String(currNum) + ans;
    }
  }
  if (extra) return '1' + ans;
  return ans;
}

function absSub(num1, num2) {
  if (num1 == num2) return '0';

  const maxLen = Math.max(num1.length, num2.length);
  // to aviod negative calculation, we need to make sure num1 >= num2;
  let reverse = false;
  if (num1.length !== num2.length) {
    if (num1.length < num2.length) {
      reverse = true;
      const temp = num1;
      num1 = num2;
      num2 = temp;
    }
  } else {
    for (let i = 0; i < num1.length; i++) {
      if (num1[i] < num2[i]) {
        reverse = true;
        const temp = num1;
        num1 = num2;
        num2 = temp;
        break;
      }
    }
  }

  num1 = num1.padStart(maxLen, '0')
  num2 = num2.padStart(maxLen, '0')
  let borrow = 0;
  let ans = '';
  for (let i = maxLen - 1; i >= 0; i--) {
    const n1 = parseInt(num1[i]), n2 = parseInt(num2[i]);
    const currNum = 10 + n1 - n2 - borrow;
    if (currNum < 10) {
      borrow = 1;
      ans = String(currNum) + ans;
    } else {
      borrow = 0;
      ans = String(currNum - 10) + ans;
    }
  }
  ans = ans.replace(/^0*/g, '');
  ans = !!ans ? ans : '0';
  if (reverse) return '-' + ans;
  return ans;
}
```

大佬写的方法

```js
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
function add(num1, num2) {
  const isNum1Negative = num1[0] === '-';
  const isNum2Negative = num2[0] === '-';
  
  // remove sign
  num1 = num1.replace(/^[+|-]/, '');
  num2 = num2.replace(/^[+|-]/, '');
  
  //case 1: Both same sign we just add numbers
  //        1.1: +,+ 
  //        1.2: -,- mark result -

  // case 2: Diff sign we subtract
  //      2.1 : (-,+)
  //          2.1.1    (n1 < n2) : subtractAbs result will be - , Ex: (-8)+(9) = + (remove '-' sign from final res)
  //          2.1.2    (n1 > n2) : subtractAbs result will be + , Ex: (-9)+(8) = - (add '-' sign to final res)
  //      2.2 : (+,-) 
  //          2.2.1    (n1 < n2) : subtractAbs result will be - , Ex: (8)+(-9) = - (keep subtractAbs result as it is)
  //          2.2.2    (n1 > n2) : result will be - , Ex: (9)+(-8) = + (keep subtractAbs result as it is)

  if (isNum1Negative === isNum2Negative) {
    return (isNum1Negative ? '-' : '') + addAbs(num1, num2); // case 1.1, 1.2
  } else {
    const res = subtractAbs(num1, num2);

    if (res[0] === '-' && isNum1Negative) { // case: 2.1.1
      return res.slice(1);
    }
    // case 2.1.2 & case 2.2.1/2.2.2
    return (isNum1Negative ? '-' : '') + res;
  }
}


// prb: 62
function addAbs(num1, num2) {
  num1 = num1.split('');
  num2 = num2.split('');
  let res = [], carry = 0;
  
  while (num1.length || num2.length || carry) {
    let sum = (Number(num1.pop()) || 0) + (Number(num2.pop()) || 0) + carry;
    carry = Math.floor(sum / 10);
    res.unshift(sum % 10);
  }
  
  return res.join('');
}

// prb: 75
function subtractAbs(num1, num2) {
  let negative = false;
  // Before proceeding further, make sure num1 is bigger
  if (isSmaller(num1, num2)) {
    negative = true;
    [num1, num2] = [num2, num1];
  }
  num1 = num1.split('');
  num2 = num2.split('');
  
  let borrow = 0, result = [];
  
  while (num1.length || num2.length || borrow) {
    // Check the value of 1st number after it has been borrowed from
    const val1 = Number(num1.pop() || 0) - borrow;
    const val2 = Number(num2.pop()) || 0;
    borrow = val2 > val1 ? 1 : 0; // if num2 is smaller then no need for another borrow and we can set it as zero
    
    // Difference would be borrow*10 with val1
    const diff = ((borrow * 10) + val1) - val2;
    //Add the result to start of array;
    result.unshift(diff);
  }
  // Remove leading zeroes, if numbers are equal we add 0 to result so remove them now
  // we can do like this too
  // result = result.replace(/^0*/g, ''); result = !!result ? result : '0';
  while (result[0] === 0 && result.length !== 1) {
    result.shift();
  };
  result = result.join('')
  return negative ? '-' + result : result;
}

// Returns true if num1 is smaller than num2.
function isSmaller(num1, num2) {
  // Calculate lengths of both string
  let n1 = num1.length, n2 = num2.length;
  if (n1 < n2) return true;
  if (n2 < n1) return false;
  
  // if we are here num1 and num2 have same length, we start comparring from MSB
  for (let i = 0; i < n1; i++) {
    if (num1[i] < num2[i]) return true;
    else if (num1[i] > num2[i]) return false;
  }
  
  return false;
}
```



## [77. implement BigInt subtraction with sign](https://bigfrontend.dev/problem/implement-BigInt-subtraction-with-sign)

### 题目

This is a follow-up on [75. implement BigInt subtraction](https://bigfrontend.dev/problem/implement-BigInt-subtraction).j

You are asked to implement a string substraction function, **with possible negative integers**. Also, '+' plus sign should also be supported

```js
substract('-999999999999999999', '-1')
// '-999999999999999998'

substract('-999999999999999999', '+1')
// '-1000000000000000000'
```

Don't use BigInt directly, it is not our goal here.

Related Problems

[62. implement BigInt addition ](https://bigfrontend.dev/problem/add-BigInt-string)
[75. implement BigInt subtraction ](https://bigfrontend.dev/problem/implement-BigInt-subtraction)
[76. implement BigInt addition with sign ](https://bigfrontend.dev/problem/implement-BigInt-addition-with-sign)

### 答案

和上题中的加法类似

```js
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
function subtract(num1, num2) {
  const isNum1Negative = num1[0] == '-';
  const isNum2Negative = num2[0] == '-';
  // remove sign
  num1 = num1.replace(/^[+|-]/, '');
  num2 = num2.replace(/^[+|-]/, '');
  if (isNum1Negative == isNum2Negative) {
    let res = absSub(num1, num2);
    if (res[0] == '-' && isNum1Negative) {
      return res.slice(1);
    }
    res = (isNum1Negative ? '-' : '') + res;
    if(res.length == 2 && res[0] == '-' && res[1] == '0') return '0';
    return res;
  } else {
    res = (isNum1Negative ? '-' : '') + absAdd(num1, num2);
    if(res.length == 2 && res[0] == '-' && res[1] == '0') return '0';
    return res;
  }
}

// prb: 62
function absAdd(num1, num2) {
  const maxLen = Math.max(num1.length, num2.length);
  num1 = num1.padStart(maxLen, '0')
  num2 = num2.padStart(maxLen, '0')
  let extra = 0;
  let ans = '';
  for (let i = maxLen - 1; i >= 0; i--) {
    const n1 = parseInt(num1[i]), n2 = parseInt(num2[i]);
    const currNum = n1 + n2 + extra;
    if (currNum >= 10) {
      extra = 1;
      ans = String(currNum - 10) + ans;
    } else {
      extra = 0;
      ans = String(currNum) + ans;
    }
  }
  if (extra) return '1' + ans;
  return ans;
}

// prb: 75
function absSub(num1, num2) {
  if (num1 == num2) return '0';

  const maxLen = Math.max(num1.length, num2.length);
  // to aviod negative calculation, we need to make sure num1 >= num2;
  let reverse = false;
  if (num1.length !== num2.length) {
    if (num1.length < num2.length) {
      reverse = true;
      const temp = num1;
      num1 = num2;
      num2 = temp;
    }
  } else {
    for (let i = 0; i < num1.length; i++) {
      if (num1[i] < num2[i]) {
        reverse = true;
        const temp = num1;
        num1 = num2;
        num2 = temp;
        break;
      }
    }
  }

  num1 = num1.padStart(maxLen, '0')
  num2 = num2.padStart(maxLen, '0')
  let borrow = 0;
  let ans = '';
  for (let i = maxLen - 1; i >= 0; i--) {
    const n1 = parseInt(num1[i]), n2 = parseInt(num2[i]);
    const currNum = 10 + n1 - n2 - borrow;
    if (currNum < 10) {
      borrow = 1;
      ans = String(currNum) + ans;
    } else {
      borrow = 0;
      ans = String(currNum - 10) + ans;
    }
  }
  ans = ans.replace(/^0*/g, '');
  ans = !!ans ? ans : '0';
  if (reverse) return '-' + ans;
  return ans;
}
```

大佬的解法

```js

/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
function subtract(num1, num2) {
  const isNum1Negative = num1[0] === '-';
  const isNum2Negative = num2[0] === '-' ? false: true; // treat it as if we are doing bigIntAddition
  
  // remove sign
  num1 = num1.replace(/^[+|-]/, '');
  num2 = num2.replace(/^[+|-]/, '');

  if (isNum1Negative === isNum2Negative) {
      return (isNum1Negative ? '-' : '') + addAbs(num1, num2);
  } else {
    const res = subtractAbs(num1, num2);

    if (res[0] === '-' && isNum1Negative) {
      return res.slice(1);
    }
    return (isNum1Negative && res[0] !== '0' ? '-' : '') + res; // (-9 - (-9)) check res[0] != 0 otherwise we get -0
  }
}


// prb: 62
function addAbs(num1, num2) {
  num1 = num1.split('');
  num2 = num2.split('');
  let res = [], carry = 0;
  
  while(num1.length || num2.length || carry){
      
    let sum = (Number(num1.pop()) || 0) + (Number(num2.pop()) || 0) + carry;
    carry = Math.floor(sum / 10);
    res.unshift(sum % 10);
  }
  
  return res.join('');
}

// prb: 75
function subtractAbs(num1, num2) {
  let negative = false;
  // Before proceeding further, make sure num1 is bigger
  if (isSmaller(num1, num2)) {
    negative = true;
    [num1, num2] = [num2, num1];
  }
  // your code here
  num1 = num1.split('');
  num2 = num2.split('');
  
  let borrow = 0, result = [];
  
  while (num1.length || num2.length || borrow) {
    // Check the value of 1st number after it has been borrowed from
    const val1 = Number(num1.pop() || 0) - borrow;
    const val2 = Number(num2.pop()) || 0;
    borrow = val2 > val1 ? 1 : 0; // if num2 is smaller then no need for another borrow and we can set it as zero
    
    // Difference would be borrow*10 with val1
    const diff = ((borrow * 10) + val1) - val2;
    //Add the result to start of array;
    result.unshift(diff);
  }
  // Remove leading zeroes, if numbers are equal we add 0 to result so remove them now
  // we can do like this too
  // result = result.replace(/^0*/g, ''); result = !!result ? result : '0';
  while (result[0] === 0 && result.length !== 1) {
    result.shift();
  };
  result = result.join('');
  return negative ? '-' + result : result;
}

// Returns true if num1 is smaller than num2.
function isSmaller(num1, num2) {
  // Calculate lengths of both string
  let n1 = num1.length, n2 = num2.length;
  if (n1 < n2) return true;
  if (n2 < n1) return false;
  
  // if we are here num1 and num2 have same length, we start comparring from MSB
  for (let i = 0; i < n1; i++) {
    if (num1[i] < num2[i]) return true;
    else if (num1[i] > num2[i]) return false;
  }
  
  return false;
}
```



## [78. convert HEX color to RGBA](https://bigfrontend.dev/problem/convert-HEX-color-to-RGBA)

### 题目

Suppose you write some CSS code, you need to set [colors](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value). You can choose hexadecimal notation `#fff` or Functional notation `rgba(255,255,255,1)`.

Can you write a function to convert hexadecimal notation to functional notation?

```js
hexToRgb('#fff')
// 'rgba(255,255,255,1)'
```

1. Alpha channel should have **maximum 2 digits after decimal point, round up if needed.**
2. Don't forget to do input validation

### 答案

强行判断并转换

```ts
function normalize(hex: string): string {
  const digits = hex.toLowerCase().slice(1);

  if (digits.length === 3) {
    return [...digits].map(item => item.repeat(2)).join('') + 'ff';
  }

  if (digits.length === 4) {
    return [...digits].map(item => item.repeat(2)).join('');
  }

  if (digits.length === 6) {
    return digits + 'ff';
  }

  return digits;
}

// max 2 digits for the fraction, 0 should be supressed
function roundUp(num: number): string {
  return num.toFixed(2).replace(/([.0]+)$/, '') || '0';
}

function hexToRgba(hex: string): string {
  // 1. validate payload
  const regValidHexColor = /^#[0-9a-fA-F]+$/;
  let len = hex.length;
  if ((len !== 4 && len !== 7 && len !== 5 && len !== 9) || !regValidHexColor.test(hex)) {
    throw("error input hex");
  }
  const normalized = normalize(hex);

  const regColorParts = /(.{2})(.{2})(.{2})(.{2})/;
  const match = normalized.match(regColorParts)!;

  const [r, g, b, a] = match.slice(1, 5).map(part => parseInt(part, 16));

  return `rgba(${r},${g},${b},${roundUp(a / 255)})`;
}
```

简化版

```js
function hexToRgba(hex: string): string {
  const validChars = /^#[a-fA-F\d]+$/.test(hex);
  const validLength = [4, 5, 7, 9].includes(hex.length);

  if (!validLength || !validChars) {
    throw new Error('Invalid HEX');
  }

  const [r, g, b, a = 255] = hex
    .toLowerCase()
    .split('')
    .slice(1)
    .reduce((a, c) => `${a}${hex.length < 7 ? c.repeat(2) : c}`, '')
    .match(/(..)/g)!
    .map((hex) => parseInt(hex, 16));

  return `rgba(${r},${g},${b},${Math.round(a / 255 * 100) / 100})`;
}
```



## [79. convert snake_case to camelCase](https://bigfrontend.dev/problem/convert-snake_case-to-camelCase)

### 题目

Do you prefer [snake_case](https://en.wikipedia.org/wiki/Snake_case) or [camelCase](https://en.wikipedia.org/wiki/Camel_case) ?

Anyway, please create a function to convert snake_case to camcelCase.

```js
snakeToCamel('snake_case') 
// 'snakeCase'
snakeToCamel('is_flag_on') 
// 'isFlagOn'
snakeToCamel('is_IOS_or_Android') 
// 'isIOSOrAndroid'
snakeToCamel('_first_underscore') 
// '_firstUnderscore'
snakeToCamel('last_underscore_') 
// 'lastUnderscore_'
snakeToCamel('_double__underscore_') 
// '_double__underscore_'
```

contiguous underscore `__`, leading underscore` _a`, and trailing underscors `a_` should be kept untouched.

### 答案

正则表达式找到中间的"_"

```js
/**
 * @param {string} str
 * @return {string}
 */
function snakeToCamel(str) {
  return str.replace(/[a-z]_[a-z]/gi, match => {
    return match[0] + match[2].toUpperCase();
  });
}
```

继续简化写法

```js
/**
 * @param {string} str
 * @return {string}
 */
function snakeToCamel(str) {
  return str.replace(/([a-z])_([a-z])/gi, (_, left, right) => left + right.toUpperCase());
}
```

也可以用常规的字符串操作

```js
/**
 * @param {string} str
 * @return {string}
 */
function snakeToCamel(str) {
  let result = str[0]; // in any case we want to keep first char as it is

  for (let i = 1; i < str.length; i++) { // begin from i=1 as we already have 0th index char

    /** main logic: Details explanation is coming soon.
    1. current char `i` must be '_'
    2. previous char must not be '_'
    3. next char must not be '_'
    4. current char must less than 2nd last of string
    **/
    if (str[i] === '_' && str[i - 1] !== '_' && str[i + 1] !== '_' && i < str.length - 1) {
      result += str[i+1].toUpperCase();
      i++; // increment because we already consider i+1 in previous line.
    } else {
      result += str[i]; // else just add in the result string
    }
  }
  return result; // 🍻 return the camelCase because that's the way
}
```



## [80. implement your own URLSearchParams](https://bigfrontend.dev/problem/implement-your-own-URLSearchParams)

### 题目

When we want to extract parameters from query string, [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) could be very handy.

Can you implement `MyURLSearchParams` which works the same ?

```js
const params = new MyURLSearchParams('?a=1&a=2&b=2')
params.get('a') // '1'
params.getAll('a') // ['1', '2']
params.get('b') // '2'
params.getAll('b') // ['2']


params.append('a', 3)
params.set('b', '3')
params.toString() // 'a=1&a=2&b=3&a=3'
```

There are [a few methods](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) on URLSearchParams, please implement them all.

### 答案

返回所有的键值对

```js
class MyURLSearchParams {
  /**
  * @params {string} init
  */
  constructor(init) {
    this.map = new Map();
    const queryString = init.split('?')[1] || init;
    const qsPairs = queryString.split('&');
    qsPairs.forEach((pair => {
      const [key, value] = pair.split('=');
      if (this.map.has(key)) {
        this.append(key, value);
      }
      else {
        this.set(key, value);
      }
    }));
  }
  
  /** 
  * @params {string} name
  * @params {any} value
  */
  append(name, value) {
    this.map.get(name).push(String(value));
  }
  
  /**
  * @params {string} name
  */
  delete(name) {
    this.map.delete(name);
  }
  
  /**
  * @returns {Iterator} 
  */
  entries() {
    let that = this;
    return (function* () {
      let pairs = [];
      that.map.forEach((value, key) => {
        value.forEach(v => {
          pairs.push([key, v]);
        });
      });
      while (pairs.length) {
        yield pairs.shift();
      }
    })();
  }
  
  /**
  * @param {(value, key) => void} callback
  */
  forEach(callback) {
    this.map.forEach((value, key) => {
      value.forEach(val => callback.call(this, val, key));
    });
  }
  
  /**
  * @param {string} name
  * returns the first value of the name
  */
  get(name) {
    name = name.split('?')[1] || name;
    return this.map.has(name) ? this.map.get(name)[0] : null;
  }
  
  /**
  * @param {string} name
  * @return {string[]}
  * returns the value list of the name
  */
  getAll(name) {
    return this.map.get(name) || [];
  }
  
  /**
  * @params {string} name
  * @return {boolean}
  */
  has(name) {
    return Boolean(this.map.has(name));
  }
  
  /**
  * @return {Iterator}
  */
  keys() {
    return this.map.keys();
  }
  
  /**
  * @param {string} name
  * @param {any} value
  */
  set(name, value) {
    this.map.set(String(name), [String(value)]);
  }
  
  // sor all key/value pairs based on the keys
  sort() {
    this.map = new Map([...this.map].sort());
  }
  
  /**
  * @return {string}
  */
  toString() {
    let result = '';
    let index = 0;
    this.map.forEach((value, key) => {
      value.forEach(v => {
        result += `${index !== 0 ? '&' : ''}${key}=${v}`;
      });
      index++;
    });
    return result;
  }
  
  /**
  * @return {Iterator} values
  */
  values() {
    const that = this;
    return function* () {
      let valueArray = [];
      [...that.map.values()].forEach(v => valueArray.push(...v));
      while (valueArray.length) {
        yield valueArray.shift();
      }
    }();
  }
}
```



## [81. merge sorted arrays](https://bigfrontend.dev/problem/merge-sorted-arrays)

### 题目

You are given a list of sorted non-descending integer arrays, write a function to merge them into one sorted non-descending array.

```js
merge(
  [
    [1,1,1,100,1000,10000],
    [1,2,2,2,200,200,1000],
    [1000000,10000001],
    [2,3,3]
  ]
)
// [1,1,1,1,2,2,2,2,3,3,100,200,200,1000,1000,10000,1000000,10000001]
```

What is time complexity of your solution?

[Source for this](https://www.glassdoor.com/Interview/ByteDance-Front-End-Developer-Interview-Questions-EI_IE1624196.0,9_KO10,29.htm?filter.jobTitleFTS=Front+End+Developer)

### 答案

22归并合并

```js
/**
 * @param {number[][]} arrList
 * non-descending integer array
 * @return {number[]} 
 */
function merge(arrList) {
  return mergeImpl(arrList, 0, arrList.length - 1);
}

function mergeImpl(arrList, start, end) {
  if (start >= end) return arrList[end] || [];

  const mid = start + Math.floor((end - start) / 2);

  const left = mergeImpl(arrList, start, mid);
  const right = mergeImpl(arrList, mid + 1, end);
  return mergeSort(left, right);
}

function mergeSort(arrOne, arrTwo) {
  const mergedArr = [];
  let idxOne = 0;
  let idxTwo = 0;

  while (idxOne !== arrOne.length || idxTwo !== arrTwo.length) {
    const firstElement = arrOne[idxOne] || Infinity;
    const secondElement = arrTwo[idxTwo] || Infinity;

    if (firstElement < secondElement) {
      mergedArr.push(firstElement);
      idxOne++;
    } else {
      mergedArr.push(secondElement);
      idxTwo++;
    }
  }

  return mergedArr;
}
```

别人写的更有条理

```js
/**
 * @param {number[][]} arrList
 * non-descending integer array
 * @return {number[]} 
 */
function merge(arrList) {
  if (arrList.length === 0) return [];
  if (arrList.length === 1) return arrList[0];
  if (arrList.length === 2) return merge2Lists(arrList[0], arrList[1]);
  const mid = Math.floor(arrList.length / 2)
  const left = merge(arrList.slice(0, mid));
  const right = merge(arrList.slice(mid, arrList.length));
  return merge2Lists(left, right);
}

function merge2Lists(arr1, arr2) {
  let [i, j] = [0, 0];
  let res = []
  while (i < arr1.length || j < arr2.length) {
    if (i >= arr1.length || arr1[i] > arr2[j]) {
      res.push(arr2[j]);
      j++;
    } else {
      res.push(arr1[i]);
      i++;
    }
  }
  return res;
}
```



## [82. find ava ilable meeting slots](https://bigfrontend.dev/problem/find-available-meeting-slots)

### 题目

`[start, end]` is a time interval, with all integers from 0 to 24.

Given schedules for all team members,

```js
[
  [[13,15], [11,12], [10,13]], //schedule for member 1
  [[8, 9]], // schedule for member 2
  [[13, 18]] // schedule for member 3
]
```

You need to create a function `findMeetingSlots()` to return the time slots available for all the members to have a meeting.

For the input above, below slots should be returned

```js
[[0,8],[9,10],[18,24]]
```

*Notes*

1. the input schedule intervals might be unsorted
2. one member's schedule might have overlapping intervals.

### 答案

排序+贪心

```js
// type Interval = [number, number]

/**
 * @param {Interval[][]} schedules
 * @return {Interval[]}
 */
function findMeetingSlots(schedules) {
  const mergedSchedules = mergeSchedules(schedules);
  mergedSchedules.sort((a, b) => a[0] - b[0]);

  const availableTimeBlocks = [];
  let currentEndTime = 0;

  for (const timeBlock of mergedSchedules) {
    const [startTime, endTime] = timeBlock;

    if (startTime > currentEndTime) {
      availableTimeBlocks.push([currentEndTime, startTime]);
    }

    if (endTime > currentEndTime) {
      currentEndTime = endTime;
    }
  }

  if (currentEndTime < 24) {
    availableTimeBlocks.push([currentEndTime, 24]);
  }

  return availableTimeBlocks;
}

function mergeSchedules(schedules) {
  const mergedSchedules = [];
  for (const schedule of schedules) {
    mergedSchedules.push(...schedule);
  }
  return mergedSchedules;
}
```

也可以利用Array自带的flat方法

```js
// type Interval = [number, number]

/**
 * @param {Interval[][]} schedules
 * @return {Interval[]}
 */
function findMeetingSlots(schedules) {
  let times = schedules.flat();

  times.sort((a, b) => a[0] - b[0]);

  let result = [];

  let prevEnd = 0;
  times.forEach(t => {
    let [start, end] = t;
    if (prevEnd < start) {
      result.push([prevEnd, start]);
    }

    prevEnd = Math.max(end, prevEnd);
  })

  if (prevEnd !== 24) {
    result.push([prevEnd, 24]);
  }

  return result;
}
```



## [83. create an interval](https://bigfrontend.dev/problem/create-an-interval)

### 题目

You are asked to create a new `mySetInterval(a, b)` which has a different behavior from `window.setInterval`, the time between calls is a linear function, growing larger and larger `period = a + b * count`.

```js
let prev = Date.now()
const func = () => {
  const now = Date.now()
  console.log('roughly ', Date.now() - prev)
  prev = now
}

const id = mySetInterval(func, 100, 200)

// roughly 100, 100 + 200 * 0
// roughly 400,  100 + 200 * 1
// roughly 900,  100 + 200 * 2
// roughly 1600,  100 + 200 * 3
// ....

myClearInterval(id) // stop the interval
```

1. Interface is `mySetInterval(delay, period)`, the first `delay` is used for the first call, and then `period` is used.

1. because `window.setTimeout` and `window.setInterval` are not accurate in browser environment, they are replaced to other implementation when judging your code. They still have the same interface, and internally keep track of the timing for testing purpose.

Something like below will be used to do the test. (You don't need to read following code to accomplish this task)

```js
let currentTime = 0

const run = (delay, period, clearAt) => {
  currentTime = 0
  pipeline.length = 0
  
  const logs = []

  const func = () => {
    logs.push(currentTime)
  }

  mySetInterval(func, delay, period)
  if (clearAt !== undefined) {
    setTimeout(() => {
      myClearInterval(id)
    }, clearAt)
  }

  while (pipeline.length > 0 && calls.length < 5) {
    const [time, callback] = pipeline.shift()
    currentTime = time
    callback()
  }

  return calls
}

expect(run(100, 200)).toEqual([100,400,900,1600,2500])
expect(run(100, 200, 450)).toEqual([100,400])
expect(run(100, 200, 50)).toEqual([])
```

### 答案

设置时间，用map保存定时器id

```js
const timerIds = new Map();
/**
 * @param {Function} func
 * @param {number} delay
 * @param {number} period
 * @return {number}
 */
function mySetInterval(func, delay, period) {
  let multiplier = 0;

  const initialTimerId = setTimeout(run, delay + period * multiplier);

  function run() {
    func();
    multiplier++;
    const nextTimerId = setTimeout(run, delay + period * multiplier);
    timerIds.set(initialTimerId, nextTimerId);
  }

  return initialTimerId;
}

/**
 * @param { number } id
 */
function myClearInterval(id) {
  clearTimeout(id);
  if (timerIds.has(id)) {
    clearTimeout(timerIds.get(id));
    timerIds.delete(id);
  }
}
```



## [84. create a fake timer (setInterval)](https://bigfrontend.dev/problem/create-a-fake-timer-setInterval)

### 题目

This is a follow-up on [36. create a fake timer(setTimeout)](https://bigfrontend.dev/problem/create-a-fake-timer)

Like `setTimeout`, `setInterval` also is not accurate. (please refer [Event Loop](https://javascript.info/event-loop) for detail).

This is OK in general web application, but might be problematic in test.

Could you implement your own `setInterval()` and `clearInterval()` to be sync? so that they have **accurate timing** for test. This is what [FakeTimes](https://github.com/sinonjs/fake-timers) are for.

By "accurate", it means **suppose all functions cost no time**, we start our function at time `0`, then `setInterval(func1, 100)` would schedule `func1` exactly at `100, 200, 300 .etc`.

You need to replace `Date.now()` as well to provide the time.

```js
class FakeTimer {
  install() {
    // replace window.setInterval, window.clearInterval, Date.now
    // with your implementation
  }
  
  uninstall() {
    // restore the original implementation of
    // window.setInterval, window.clearInterval, Date.now
  }
  
  tick() {
    // run the scheduled functions without waiting
  }
}
```

**Be careful about the infinite loops **. Your code is tested like this:

```js
const fakeTimer = new FakeTimer()
fakeTimer.install()

const logs = []
const log = () => {
    logs.push(Date.now())
}

let count = 0
const id = setInterval(() => {
  if (count > 1) {
    clearInterval(id)
  } else {
    log()
  }
  count += 1
}, 100)
// log 'A' at every 100, stop at 200
fakeTimer.tick()
fakeTimer.uninstall()
 
expect(logs).toEqual([100,200])
```

*Note*

Only `Date.now()` is used when judging your code, you can ignore other time related apis.

Related Problems

[36. create a fake timer(setTimeout)](https://bigfrontend.dev/problem/create-a-fake-timer)

### 答案

保存原生的函数

```js
class FakeTimer {
  constructor () {
    this.originSetInterval = window.setInterval;
    this.originClearInterval = window.clearInterval;
    this.originDateNow = Date.now;

    this.taskQueue = [];
    this.currentTime = 0;
  }
  install() {
    // replace window.setInterval, window.clearInterval, Date.now
    // with your implementation
    window.setInterval = (fn, delay) => {
      const id = this.taskQueue.length;
      this.taskQueue.push({
        id,
        fn,
        delay,
      });
      return id;
    };

    window.clearInterval = (id) => {
      this.taskQueue = this.taskQueue.filter((task) => task.id !== id);
    };

    Date.now = () => this.currentTime;
  }
  
  uninstall() {
    // restore the original implementation of
    // window.setInterval, window.clearInterval, Date.now
    window.setInterval = this.originSetInterval;
    window.clearInterval = this.originClearInterval;
    Date.now = this.originDateNow;
  }
  
  tick() {
    // run the scheduled functions without waiting
    while (this.taskQueue.length > 0) {
      const task = this.taskQueue.shift();
      this.currentTime = task.delay;
      this.taskQueue.push({
        ...task,
        delay: this.currentTime + task.delay,
      });
      task.fn();
    }
  }
}
```



## [85. implement `_.get()](https://bigfrontend.dev/problem/implement-lodash-get)`

### 题目

[_.get(object, path, [defaultValue\])](https://lodash.com/docs/4.17.15#get) is a handy method to help retrieving data from an arbitrary object. if the resolved value from `path` is `undefined`, `defaultValue` is returned.

Can you create your own `get()`?

```js
const obj = {
  a: {
    b: {
      c: [1,2,3]
    }
  }
}

get(obj, 'a.b.c') // [1,2,3]
get(obj, 'a.b.c.0') // 1
get(obj, 'a.b.c[1]') // 2
get(obj, ['a', 'b', 'c', '2']) // 3
get(obj, 'a.b.c[3]') // undefined
get(obj, 'a.c', 'bfe') // 'bfe'
```

Related Problems

[156. implement `_.set()`](https://bigfrontend.dev/problem/lodash-set)

### 答案

切割路径，然后迭代或递归得到最后的结果

```js
/**
 * @param {object} source
 * @param {string | string[]} path
 * @param {any} [defaultValue]
 * @return {any}
 */
function get(source, path, defaultValue = undefined) {
  path = Array.isArray(path) ? path : path.split(/\.|\[|\]/);

  if (path[path.length - 1] === '') path.pop();
  if (path.length === 0) return defaultValue;

  let currentSource = source;
  let index = 0;

  while (currentSource && index < path.length) {
    const property = path[index];
    currentSource = currentSource[property];
    index++;
  }

  return currentSource || defaultValue;
}
```

使用replaceAll

```js
/**
 * @param {object} source
 * @param {string | string[]} path
 * @param {any} [defaultValue]
 * @return {any}
 */
function get(source, path, defaultValue = undefined) {
  const props = Array.isArray(path) ? path : path.replaceAll('[', '.').replaceAll(']', '').split('.');
  let curNode = source;
  for (let i = 0; i < props.length; i++) {
    let k = props[i];
    if (curNode[k] === undefined) return defaultValue;
    if (i === props.length - 1) return curNode[k];
    else curNode = curNode[k];
  }
}
```



## [86. Generate Fibonacci Number](https://bigfrontend.dev/problem/fibonacci-number)

### 题目

```js
0
1
1 = 0 + 1
2 = 1 + 1
3 = 1 + 2
5 = 2 + 3
8 = 3 + 5
13 = 5 + 8
....

[0,1,1,2,3,5,8,13 ...]
```

Given 2 initial numbers, we can generate a sequence by adding the sum of last two numbers as a new lement.

This is [Fibonacci number](https://en.wikipedia.org/wiki/Fibonacci_number).

You are asked to create a `fib(n)` function, which generate the n-th Fibonacci number.

What is the time & space cost of your solution?

Related Problems

[93. Generate Fibonacci Number with recursion](https://bigfrontend.dev/problem/Generate-Fibonacci-Number-with-recursion)

### 答案

动态规划

```js
/**
 * @param {number} n - non-negative integer
 * @return {number}
 */
function fib(n) {
  if(n < 2) return n;
  let dp0 = 0, dp1 = 1;
  for (let i = 2; i <= n; i++) {
    let temp = dp0 + dp1;
    dp0 = dp1;
    dp1 = temp;
  }
  return dp1;
}
```

尾递归

```js
/**
 * @param {number} n - non-negative integer
 * @return {number}
 */
function fib(n, ac1 = 0, ac2 = 1) {
  if(n == 0) return 0;
  if(n == 1) {
    if(ac2 == 1) return 1;
    return ac2;
  }
  return fib (n - 1, ac2, ac1 + ac2);
}
```

多种方法

```js
// Approach 1 - Recursion
// Time: O(2^n)
// Space: O(n)
function fib(n) {
  if (n < 2) return n;
  return fib(n-1) + fib(n-2);
}

// Approach 2 - Memoized Recursion
// Time: O(n)
// Space: O(n)
function fib(n) {
  const cache = {};
  function memo(n) {
    if (n < 2) return n;
    cache[n] ??= memo(n-1) + memo(n-2);
    return cache[n];
  }
  return memo(n);
}

// Approach 3 - Tabulation
// Time: O(n)
// Space: O(n)
function fib(n) {
  const res = [0,1,1];
  for(let i = 3; i <= n; i++) {
    res.push(res[i-1] + res[i-2]);
  }
  return res[n];
}

// Approach 4 - Dynamic Tabulation
// Time: O(n)
// Space: O(1)
function fib(n) {
  if (n < 2) return n;
  const res = [1,1];
  for(let i = 2; i < n; i++) {
    [res[0], res[1]] = [res[1], res[0] + res[1]];
  }
  return res[1];
}

// Approach 5 - Math (god tier)
// Time: O(1)
// Space: O(1)
function fib (n) {
  const A = (1 + Math.sqrt(5)) / 2;
  const B = (1 - Math.sqrt(5)) / 2;
  const fib = (Math.pow(A, n) - Math.pow(B, n)) / Math.sqrt(5);
  return Math.floor(fib);
}
```



## [87. longest substring with unique characters](https://bigfrontend.dev/problem/longest-substring-with-unique-characters)

### 题目

Given a string, please find the **longest substring that has no repeated characters**.

If there are multiple result, any one substring is fine.

```js
longestUniqueSubstr('aaaaa')
// 'a'
longestUniqueSubstr('abcabc')
// 'abc', or 'bca', or 'cab'
longestUniqueSubstr('a12#2')
// 'a12#'
```

*Follow-up*

What is the time & space cost of your solution ? Could you do it better?

### 答案

滑动窗口，哈希记录上个出现字母的位置

```js
/**
 * @param {string} str
 * @return {string}
 */
function longestUniqueSubstr(str) {
  let left = 0, right = 0;
  let maxLen = 0;
  let leftIndex = 0;
  let set = new Set();
  const len = str.length;
  while (right < len) {
    while (!set.has(str[right]) && right < len) {
      set.add(str[right]);
      right++;
    }
    if (right - left > maxLen) {
      maxLen = right - left;
      leftIndex = left;
    }
    if (right === len) break;
    while (set.has(str[right])) {
      set.delete(str[left]);
      left++;
    }
  }
  return str.slice(leftIndex, leftIndex + maxLen);
}
```

保存上个字母的坐标

```js
/**
 * @param {string} str
 * @return {string}
 */
function longestUniqueSubstr(str) {
  let map = new Map();
  let start = 0, end = 0, len = 0, i = 0;

  while (end < str.length) {
      if (map.has(str[end])) {
      // move start to repeating char indx +1 as one by one reducing window size is not optimized
        start = map.get(str[end]) + 1;
      }
      map.set(str[end], end);
      end++;

      if (len < (end - start)) {
        len = end - start;
        i = start;
      }

  }

  return str.slice(i, i + len);
}
```



## [88. support negative Array index in JavaScript](https://bigfrontend.dev/problem/support-negative-Array-index)

### 题目

Python supports negative list index , while JavaScript doesn't.

Can you write a wrapper function to make **negative array index** possible?

```js
const originalArr = [1,2,3]
const arr = wrap(originalArr)

arr[0] // 1
arr[1] // 2
arr[2] // 3
arr[3] // undefined
arr[-1] // 3
arr[-2] // 2
arr[-3] // 1
arr[-4] // undefined
```

All methods on `arr` should be applied to the original array, which means

```js
arr.push(4)
arr[3] // 4
originalArr[3] // 4

arr.shift()
arr[0] // 2
originalArr[0] // 2

arr.bfe = 'bfe'
originalArr.bfe // 'bfe'

arr[-1] = 5
arr // [2,3,5]
originalArr // [2,3,5]

originalArr[2] = 6
arr // [2,3,6]
originalArr // [2,3,6]
```

使用Proxy

```js
/**
 * @param {any[]} arr
 * @returns {?} - sorry no type hint for this
 */
function wrap(arr) {
  return new Proxy(arr, {
    get(target, key) {
      if (key === Symbol.iterator) {
        return Reflect.get(target, Symbol.iterator);
      }
      return +key < 0 ? Reflect.get(target, target.length + +key) : Reflect.get(target, key);
    }, 
    set(target, key, value) {
      if (+key < 0) {
        if (target.length + +key < 0) throw new Error('');
        Reflect.set(target, target.length + +key, value);
        return true;
      }
      Reflect.set(target, key, value);
      return true;
    }
  });
}
```

使用函数同一处理index，保留receiver处理

```js
/**
 * @param {any[]} arr
 * @returns {?} - sorry no type hint for this
 */
function wrap(arr) {
  const isNumber = (prop) => typeof prop === 'string' && !Number.isNaN(Number(prop));
  const normalize = (idx, arrLength) => idx >= 0 ? idx : idx + arrLength;
  const assert = (idx) => {
    if (idx < 0) {
      throw new Error('incorrect index');
    }
  };

  return new Proxy(arr, {
    get (target, prop, receiver) {
      if (isNumber(prop)) {
        prop = normalize(Number(prop), target.length);
      }

      return Reflect.get(target, prop, receiver);
    },

    set(target, prop, value, receiver) {
      if (isNumber(prop)) {
        prop = normalize(Number(prop), target.length);
        assert(prop);
      }

      return Reflect.set(target, prop, value, receiver);
    }
  });
}
```



## [89. Next Right Sibling](https://bigfrontend.dev/problem/Next-Right-Sibiling)

### 题目

Given a DOM tree and a target element, please return the **next right sibling**.

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20211014204116.png)

Like above, the next right sibling of `<button/>` is the blue `<a/>`. Notice that **they don't necessarily have the same parent element.**

If no right sibling, then return `null`.

What is time & space cost of your solution ?

### 答案

队列+BFS

```js

/**
 * @param {HTMLElement} root
 * @param {HTMLElement} target
 * @return {HTMLElemnt | null}
 */
function nextRightSibling(root, target) {
  if (!root) return null;
  const queue = [root];
  while (queue.length) {
    const node = queue.shift();
    if (node === target) return queue[0] || null;
    for(const child of node.children){
      queue.push(child);
    }
  }
  return null;
}
```



## [90. write your own `instanceof`](https://bigfrontend.dev/problem/write-your-own-instanceof)

### 题目

Do you know how [instanceOf](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof) works ?

If so, please write you own `myInstanceOf()`.

```js
class A {}
class B extends A {}

const b = new B()
myInstanceOf(b, B) // true
myInstanceOf(b, A) // true
myInstanceOf(b, Object) // true

function C() {}
myInstanceOf(b, C) // false
C.prototype = B.prototype
myInstanceOf(b, C) // true
C.prototype = {}
myInstanceOf(b, C) // false
```

### 答案

我用的是迭代方法

```js
/**
 * @param {any} obj
 * @param {target} target
 * @return {boolean}
 */
function myInstanceOf(obj, target) {
  if (obj === null || typeof obj !== 'object') return false;
  if (typeof target !== 'function') return false;

  while (obj !== null) {
    const proto = obj.__proto__;
    if (proto === target.prototype) {
      return true;
    }
    obj = proto;
  }

  return false;
}
```

也可以用递归

```js
/**
 * @param {any} obj
 * @param {target} target
 * @return {boolean}
 */
function myInstanceOf(obj, target) {
  // If the object does not exist or we've reached the base Object constructor, return false
  if (!obj || typeof obj !== "object") return false;

  // Check if the target is a valid object
  if (!target.prototype) throw Error;

  // If the object's prototype matches our target's prototype, return true
  // Otherwise, recurse down the prototypal chain
  if (Object.getPrototypeOf(obj) === target.prototype) {
    return true;
  } else {
    return myInstanceOf(Object.getPrototypeOf(obj), target);
  }
}
```



## [91. invert a binary tree](https://bigfrontend.dev/problem/invert-a-binary-tree)

### 题目

Can you invert a binary tree and get an offer from Google?

<iframe id="twitter-widget-0" scrolling="no" frameborder="0" allowtransparency="true" allowfullscreen="true" class="" title="Twitter Tweet" src="https://platform.twitter.com/embed/Tweet.html?creatorScreenName=BFEdev&amp;dnt=false&amp;embedId=twitter-widget-0&amp;features=eyJ0ZndfZXhwZXJpbWVudHNfY29va2llX2V4cGlyYXRpb24iOnsiYnVja2V0IjoxMjA5NjAwLCJ2ZXJzaW9uIjpudWxsfSwidGZ3X2hvcml6b25fdHdlZXRfZW1iZWRfOTU1NSI6eyJidWNrZXQiOiJodGUiLCJ2ZXJzaW9uIjpudWxsfSwidGZ3X3NwYWNlX2NhcmQiOnsiYnVja2V0Ijoib2ZmIiwidmVyc2lvbiI6bnVsbH19&amp;frame=false&amp;hideCard=false&amp;hideThread=false&amp;id=608682016205344768&amp;lang=en&amp;origin=https%3A%2F%2Fbigfrontend.dev%2Fproblem%2Finvert-a-binary-tree&amp;sessionId=65b8d9394f59e4de1628a84b773129a4de1c098d&amp;siteScreenName=BFEdev&amp;theme=light&amp;widgetsVersion=fcb1942%3A1632982954711&amp;width=550px" __idm_frm__="1102" data-tweet-id="608682016205344768" style="box-sizing: border-box; max-width: 100%; position: static; visibility: visible; width: 550px; height: 273px; display: block; flex-grow: 1;"></iframe>

Inverting a node means swapping its left child and right child. You need to apply this to all nodes. As following figure illustrates.

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20211014220149.png)

### 答案

dfs递归

```js
// This is the type for the node
// type Node = null | {
//   value: number
//   left: Node
//   right: Node
// }


/**
 * @param {Node} node
 * @returns {Node}
 */
function invert(node) {
  if (!node) return node;
  if (node.left) invert(node.left);
  if (node.right) invert(node.right);
  [node.left, node.right] = [node.right, node.left];
  return node;
}
```

看看人家写的多简洁

```js
// This is the type for the node
// type Node = null | {
//   value: number
//   left: Node
//   right: Node
// }


/**
 * @param {Node} node
 * @returns {Node}
 */
function invert(node) {
  if (!node) return null;
  [node.left, node.right] = [invert(node.right), invert(node.left)];
  return node;
}
```



## [92. throttle Promises](https://bigfrontend.dev/problem/throttle-Promises)

### 题目

Say you need to fetch some data through 100 APIs, and as soon as possible.

If you use `Promise.all()`, 100 requests go to your server at the same time, which is a burden to low spec servers.

Can you **throttle your API calls so that always maximum 5 API calls at the same time**?

You are asked to create a general `throttlePromises()` which takes an array of functions returning promises, and a number indicating the maximum concurrent pending promises.

```js
throttleAsync(callApis, 5).then((data) => {
  // the data is the same as `Promise.all` 
}).catch((err) => {
  // any error occurs in the callApis would be relayed here
})
```

By running above code, at any time, no more than 5 APIs are requested, so low spec servers are saved.

Related Problems

[4. implement basic throttle() ](https://bigfrontend.dev/problem/implement-basic-throttle)
[5. implement throttle() with leading & trailing option ](https://bigfrontend.dev/problem/implement-throttle-with-leading-and-trailing-option)

### 答案

包装每个函数，利用promise.all

```js
/**
 * @param {() => Promise<any>} func
 * @param {number} max
 * @return {Promise}
 */
function throttlePromises(funcs, max){
  const results = [];
  async function doWork(iterator) {
    for (let [index, item] of iterator) {
      const result = await item();
      results[index] = result;
    }
  }

  const iterator = Array.from(funcs).entries();
  const workers = Array(max).fill(iterator).map(doWork);
  return Promise.all(workers).then(() => results);
}
```

简单计数

```js
/**
 * @param {() => Promise<any>} func
 * @param {number} max
 * @return {Promise}
 */
function throttlePromises(funcs, max){
  let results = [];
  return new Promise((resolve, reject) => {
    let runningCount = 0;
    let queue = [...funcs];
    function run() {
      while(runningCount<max && queue.length > 0) {
          const fn = queue.shift();
          runningCount++;
          fn().then(data => {
            runningCount--;
            results.push(data)
            run();
          }).catch((err) => reject(err));
      }
      if (results.length === funcs.length) {
        resolve(results)
      }
    }
    run();
  });
}
```

把整个函数改造成async/await

```js
/**
 * @param {() => Promise<any>} func
 * @param {number} max
 * @return {Promise}
 */
async function throttlePromises(funcs, max){
  let results = [];
  const len = funcs.length;
  while (results.length < len) {
    try{
      let vals = await Promise.all(funcs.splice(0, max).map(fn => fn()));
      results.push(...vals);
    }
    catch(err){
      throw err;
    }
  }
  return results;
}
```



## [93. Generate Fibonacci Number with recursion](https://bigfrontend.dev/problem/Generate-Fibonacci-Number-with-recursion)

### 题目

In [86. Generate Fibonacci Number](https://bigfrontend.dev/problem/fibonacci-number) you are asked to create a `fib(n)`.

This could be simply done by a recursion, but it costs so much time that your browser freezes, don't try it with large numbers.

```js
const fib = (n) => {
  if (n === 0) return 0
  if (n === 1) return 1
  return fib(n - 1) + fib(n - 2)
}

fib(10) // 55
fib(1000) // timeout
```

Can you improve above implementation to make it work for `fib(1000)` ? recursion should still be used.

Related Problems

[86. Generate Fibonacci Number](https://bigfrontend.dev/problem/fibonacci-number)

### 答案

尾递归

```js
// please modify code below to make it work for large number like `fib(1000)`
// recursion should still be used

function fib(n, a = 0, b = 1){
  if (n === 0) return a;
  if (n === 1) return b;
  return fib(n - 1, b, a + b);
}
```

大佬的一行

```js
// please modify code below to make it work for large number like `fib(1000)`
// recursion should still be used

const fib = (n, m = { 0: 0, 1: 1 }) => m[n] ??= fib(n - 1, m) + fib(n - 2, m);
```



## [94. implement your own `Object.create](https://bigfrontend.dev/problem/implement-your-own-Object-create)`

### 题目

You can use [Object.create()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create) to create a new object.

Can you write your own `myObjectCreate()` to do the same(well for the basic usage) ?

**Note**

1. you don't need to support `propertiesObject `- 2nd parameter of Object.create
2. throw an Error if non-object is passed in. ([why](https://stackoverflow.com/questions/18198178/null-prototype-object-prototype-and-object-create)?)
3. `Object.create()` and `Object.setPrototypeOf()` should not be used.

### 答案

```js
/**
 * @param {any} proto
 * @return {object}
 */
function myObjectCreate(proto) {
  if (typeof proto !== 'object' || proto === null) throw new Error('');
  const obj = {};
  obj.__proto__ = proto;
  return obj;
}
```

或者用构造函数

```js
/**
 * @param {any} proto
 * @return {object}
 */
function myObjectCreate(proto) {
  function Constructor() {}
  Constructor.prototype = proto.prototype || proto;
  return new Constructor();
}
```



## [95. implement String.prototype.trim()](https://bigfrontend.dev/problem/implement-String-prototype-trim)

### 题目

[String.prototype.trim()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/Trim) is commonly used when processing strings.

It is very easy, can you implement your own one?

There are many ways to do it, can you think of different approaches?

### 答案

正则表达式

'/s' => it indicates single white space character
 '/s+' => here '+' is a greedy character that indicates one or more
For example 'a+' signifies one or more a
 '^' indicates starting/leading characters of the expression
 '$' indicates end/trailing characters of the expression
'|' similar to logical OR operator

```js
/**
 * @param {string} str
 * @return {string}
 */
function trim(str) {
  return str.replace(/^\s\s*/, '').replace(/\s\s*$/, '');
}
```

字符串操作

```js
const WHITESPACES = [" ", "", "\s", "\t", "\n", "\u3000"];
/**
 * @param {string} str
 * @return {string}
 */
function trim(str) {
  let wordStart = 0;
  let wordEnd = str.length;

  for (let i = 0; i < str.length; i++) {
    if (WHITESPACES.indexOf(str[i]) === -1) {
      wordStart = i;
      break;
    }
  }

  for(let j = str.length - 1; j >= 0; j--) {
    if (WHITESPACES.indexOf(str[j]) === -1) {
      wordEnd = j;
      break;
    }
  }

  return str.slice(wordStart, wordEnd + 1);
}
```



## [96. count "1" in binary form](https://bigfrontend.dev/problem/how-many-1s-in-binary)

### 题目

Given an integer, count "1" in its binary form.

```js
countOne(1) // 1,  "1"
countOne(257799) // 12, "111110111100000111"
```

1. If you use built-in string methods in JavaScript, please do understand the time complexity, they are not free.
2. Actually this could be easily done by counting the digit one by one. Could you think of some other approaches?

### 答案

汉明距离

```js
/**
 * @param {number} num - integer
 * @return {number} count of 1 bit
 */
function countOne(num) {
  let res = 0;
  while (num) {
    num &= (num - 1);
    res++;
  }
  return res;
}
```



## [97. compress a string](https://bigfrontend.dev/problem/compress-a-string)

### 题目

Given a string, compress the repeating letters with count number

```js
compress('a') // 'a'
compress('aa') // 'a2'
compress('aaa') // 'a3'
compress('aaab') // 'a3b'
compress('aaabb') // 'a3b2'
compress('aaabba') // 'a3b2a'
```

### 答案

使用正则表达式

```js
/**
 * @param {string} str
 * @return {string}
 */
function compress(str) {
  str = str.replace(/(\w)\1+/g, ($0) => {
      return $0[0] + $0.length;
  });
  return str;
}
```

字符串操作

```js
/**
 * @param {string} str
 * @return {string}
 */
function compress(str) {
  const res = [];
  let count = 0;
  for (let i = 0; i < str.length; i++) {
    count++;
    if (str[i] !== str[i + 1]) {
      res.push(str[i]);
      if (count > 1) res.push(count); 
      count = 0;
    }
  }
  return res.join("");
}
```



## [98. validate an IP address](https://bigfrontend.dev/problem/validate-an-ip-address)

### 题目

#### IPv4

An [IPv4 Address](https://en.wikipedia.org/wiki/IP_address#IPv4_addresses) is represented in dot-decimal notation, consisting of 4 numbers( called 'octet'), each ranging from 0 to 255, like `1.22.33.255`. It has 32 bit, so there are maximum 2^32 = 4,294,967,296 IPv4 addresses.

`Leading zeroes` are not allowed in IPv4, like `01.01.01.01` is invalid.

#### IPv6

An [IPv6 Address](https://en.wikipedia.org/wiki/IPv6_address) have 128 bits (which is a lot), separated by 8 groups (called 'hexadectet', or 'hextet', not fixed yet). Each group has 16 bits, so could be represented by a **hexadecimal string at 4 digits**, such as `2001:0db8:85a3:0000:0000:8a2e:0370:7334`.

Notice that `leading zeroes` are allowed in IPv6.

There are other valid format of IPv6 addresses, like [Zero compression](https://tools.ietf.org/html/rfc5952#section-2.2), but it is beyond the scope here, you can ignore them.

#### Task

You are given some random string, please write a function if it is valid IPv4 or IPv6 address.

#### Follow-up

Can you solve it with regular expressions ?

### 答案

分割判断每一位，不然更复杂

```js

/**
 * @param {string} str
 * @return {boolean}
 */
function isValidIP(str) {
  const arr4 = str.split('.');
  const arr6 = str.split(':');

  if (arr4.length === 4) {
    if (arr4.every(part => part.match(/^0$|^([1-9]\d{0,2})$/) && Number(part) <= 255)) {
      return true;
    }
  } else if (arr6.length === 8) {
    if (arr6.every(part => part.match(/^[0-9a-fA-F]{1,4}$/))) {
      return true;
    }
  }
  return false;
}
```

完全正则表达式

```js
/**
 * @param {string} str
 * @return {boolean}
 */
function isValidIP(str) {
  const ipv4Regex = /^([0-9]\.|[1-9][0-9]\.|1[0-9][0-9]\.|2[0-4][0-9]\.|25[0-5]\.){4}$/g;
  const ipv6Regex = /^([0-9a-fA-F]{1,4}:){8}$/g;
  return ipv4Regex.test(`${str}.`) || ipv6Regex.test(`${str}:`);
}
```



## [99. extract all anchor element from HTML string](https://bigfrontend.dev/problem/extract-all-anchor-elements-from-HTML-string)

### 题目

Given a HTML string, write a function to extract the anchor `<a/>` tag from it.

```js
extract(`
<div>
    <a>link1< / a><a href="https://bfe.dev">link1< / a>
    <div<abbr>bfe</abbr>div>
    <div>
<abbr>bfe</abbr><a href="https://bfe.dev" class="link2"> <abbr>bfe</abbr>   <span class="l">l</span><span  class="i">i</span>   nk2   </a>
    </div>
</div>
`)

// [
//    '<a>link1< / a>',
//    '<a href="https://bfe.dev">link1< / a>',
//    '<a href="https://bfe.dev" class="link2"> <abbr>bfe</abbr>   <span class="l">l</span><span  //class="i">i</span>   nk2   </a>'
 //]
```

### 答案

正则表达式

```js
/**
 * @param {string} str
 * @return {string[]}
 */
function extract(str) {
  return str.match(/<a(\s[^>]*)?>.*?<\s*\/\s*a>/g) || [];
}
```

纯粹字符串操作

```js
/**
 * @param {string} str
 * @return {string[]}
 */
function extract(str) {
  let i = 0;
  let res = [];
  while (i < str.length) {
    let start = str.indexOf('<a', i);
    if (start === -1) return res;
    if (str[start + 2] !== ' ' && str[start + 2] !== '>') {
      i += 2;
    } else {
      let end = str.indexOf('a>', start + 2);
      if (end === 1) return res;
      res.push(str.slice(start, end + 2));
      i = end + 2;
    }
  }
  return res;
}
```



## [100. detect circle in linked list](https://bigfrontend.dev/problem/detect-circle-in-linked-list)

### 题目

A [Singly Linked List](https://en.wikipedia.org/wiki/Linked_list#Singly_linked_list) is a bunch of nodes linked in one direction.

```ts
class Node {
  val: any
  next: Node
  constructor(val: any, next:Node) {
    this.val = val
    this.next = next
  }
}

const node2 = new Node(2)
const node1 = new Node(1, node2) // connect 1 -> 2
```

A Node might link to a node before it, thus creating a circle.

Can you write a function to detect it?

**Follow-up**

What is the space cost for your approach? Can you solve it **without extra space**?

### 答案

快慢指针

```js
/**
 * @param {Node} head
 * @return {boolean}
 */
function hasCircle(head) {
  // your code here
  if (!head) return false;
  let fast = head, slow = head;

  while (fast.next && fast.next.next) {
    fast = fast.next.next;
    slow = slow.next;

    if (fast === slow) {
      return true;
    }
  }

  return false;
}
```

哈希

```js
/**
 * @param {Node} head
 * @return {boolean}
 */
function hasCircle(head) {
  let node = head;
  let nodes = new Set();
  
  while (node) {
    if (nodes.has(node.next)) {
      return true
    }
    
    node = node.next;
    nodes.add(node);
  }
  
  return false;
}
```



## [101. merge identical API calls](https://bigfrontend.dev/problem/merge-identical-API-calls)

### 题目

Suppose we have a utility function `getAPI()` which fetches data.

```ts
const getAPI =  <T>(
  path: string, 
  config: SomeConfig
): Promise<T> => { ... }

const list1 = await getAPI('/list', { keyword: 'bfe'})
const list2 = await getAPI('/list', { keyword: 'dev'})
```

It works great. Util the UI become so complicated that same API might be called for multiple time within a relatively short period of time.

You want to avoid the unnecessary API calls, based on following assumption:

**GET API call response hardly changes within 1000ms.**

So identical GET API calls should return the same response within 1000ms. By **identical**, it means `path` and `config` are [deeply equal](https://bigfrontend.dev/problem/implement-deep-equal-isEqual).

You create `getAPIWithMerging(path: string, config: SomeConfig)`, which works like following.

```js
getAPIWithMerging('/list', { keyword: 'bfe'}).then(...)  
// 1st call,  this will call getAPI

getAPIWithMerging('/list', { keyword: 'bfe'}).then(...) 
// 2nd call is identical to 1st call, 
// so getAPI is not called, 
// it resolves when 1st call resolves

getAPIWithMerging('/list', { keyword: 'dev'}).then(...)
// 3rd call is not identical, so getAPI is called

// after 1000ms
getAPIWithMerging('/list', {keyword: 'bfe'}).then(...)
// 4th call is identical to 1st call, 
// but since after 1000ms, getAPI is called.
```

#### Attention for memory leak!

Your cache system should not bloat. For this problem, you should have 5 cache entries at maximum, which means:

```js
getAPIWithMerging('/list1', { keyword: 'bfe'}).then(...) 
// 1st call, call callAPI(), add a cache entry
getAPIWithMerging('/list2', { keyword: 'bfe'}).then(...) 
// 2nd call, call callAPI(), add a cache entry
getAPIWithMerging('/list3', { keyword: 'bfe'}).then(...) 
// 3rd call, call callAPI(), add a cache entry
getAPIWithMerging('/list4', { keyword: 'bfe'}).then(...) 
// 4th call, call callAPI(), add a cache entry
getAPIWithMerging('/list5', { keyword: 'bfe'}).then(...) 
// 5th call, call callAPI(), add a cache entry

getAPIWithMerging('/list6', { keyword: 'bfe'}).then(...) 
// 6th call, call callAPI(), add a cache entry
// cache of 1st call is removed

getAPIWithMerging('/list1', { keyword: 'bfe'}).then(...) 
// identical with 1st call, but cache of 1st call is removed
// new cache of entry is added
```

#### clear()

For test purpose, please provide a clear method to clear all cache.

```js
getAPIWithMerging.clearCache()
```

### 答案

保存各值

```js
// getAPI is bundled with your code, config will only be some plain objects.
// const getAPI = <T>(path: string, config: SomeConfig): Promise<T> => { ... }


const cache = new Map();

/**
 * @param {string} path
 * @param {object} config
 * only plain objects/array made up serializable primitives
 * @returns {Promise<any>}
 */
function getAPIWithMerging(path, config) {
  const key = getHashKey(path, config);
  const result = cache.get(key);
  if(result) {
    if(result.expiredAt > Date.now()) {
      return Promise.resolve(result.promise);
    }
    cache.delete(key);
  }
  const promise = getAPI(path, config);
  cache.set(key, { promise, expiredAt: Date.now() + 1000 })
  return promise;
}

getAPIWithMerging.clearCache = () => {
  cache.clear();
}

function getHashKey(path, config) {
  const arr = [path];
  const keys = Object.keys(config);
  keys.sort()
  for(let key of keys) {
    arr.push([key, config[key]]);
  }
  return JSON.stringify(arr);
}
```



## [102. validate string of parentheses](https://bigfrontend.dev/problem/validate-parenthesis)

### 题目

Given a string containing only following characters:

1. parentheses : `(` or `)`
2. brackets: `[` or `]`
3. braces: `{` or `}`

write a function to **determine if they are valid**.

By 'valid', it means all should be rightly paired, and with the valid order.

```js
validate('{}[]()') 
// true

validate('{[()]}') 
// true

validate('{[}]') 
// false, they are not in the right order

validate('{}}') 
// false, last `}` is not paired with `{`
```

**Follow-up**

What is time & space complexity of your approach ? Can you do it better?

### 答案

使用栈

```js
/**
 * @param {string} str
 * @return {boolean} 
 */
function validate(str) {
  const len = str.length;
  if (!len) return true;
  if (len % 2 !== 0) return false;
  const map = new Map();
  map.set('}', '{');
  map.set(']', '[');
  map.set(')', '(');
  const stack = [];
  for (let i = 0; i < len; i++) {
    const char = str[i];
    if (map.has(char)) {
      if (!stack.length) return false;
      if (stack.pop() !== map.get(char)) return false;
    } else {
      stack.push(char);
    }
  }
  return stack.length === 0;
}
```



### 103. implement Math.sqrt()

[Math.sqrt()](https://tc39.es/ecma262/#sec-math.sqrt) helps us getting the square root of a number.

Can your write your own `mySqrt()` ? You should return the integer part only, truncating the fraction part.

```js
mySqrt(0)
// 1

mySqrt(1)
// 1

mySqrt(2)
// 1

mySqrt(4)
// 2

mySqrt(NaN)
// NaN
```

Attention for the [special case listed up in the spec](https://tc39.es/ecma262/#sec-math.sqrt).

**Follow-up**

What is time & space complexity of your solution ?Can you do better?

[Source for this ](https://www.glassdoor.com/Interview/Implement-a-square-root-function-Question-related-to-array-data-manipulation-QTN_819390.htm)

### 答案

二分查找

```js
/**
 * @param {any} x
 * @return {number}
 */
function mySqrt(x) {
  if(typeof x !== 'number' || isNaN(x) || x < 0) return NaN;
  let start = 0, end = Math.floor(x / 2);
  let res = 1;
  
  while (start <= end) {
    const mid = start + Math.floor((end - start) / 2);
    const square = mid * mid;
    if (square === x) return mid;
    if (square > x) {
      end = mid - 1;
    } else {
      res = Math.max(res, mid);
      start = mid + 1;
    }
  }
  return res;
}
```



## [104. Traverse DOM level by level](https://bigfrontend.dev/problem/Traverse-DOM-level-by-level)

### 题目

Given a DOM tree, flatten it into an one dimensional array, in the order of layer by layer, like below.

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20211016160242.png)

### 答案

队列层次遍历

```js
/**
 * @param {HTMLElement | null} root
 * @return {HTMLElement[]}
 */
function flatten(root) {
  const res = [];
  if (!root) {
    return res;
  }
  const queue = [root];
  while (queue.length) {
    const node = queue.shift();
    res.push(node);
    for (const child of node.children) {
      queue.push(child);
    }
  }
  return res;
}
```



## [105. find the first duplicate character in a string](https://bigfrontend.dev/problem/find-the-first-duplicate-character-in-a-string)

### 题目

Given a string which might have duplicate letters, write a function to find the first duplicate.

```js
firstDuplicate('abca')
// 'a'

firstDuplicate('abcdefe')
// 'e'

firstDuplicate('abcdef')
// null
```

What is the time & space cost of your approach ? Can you do better?

### 答案

哈希表

```js
/**
 * @param {string} str
 * @return {string | null}
 */
function firstDuplicate(str) {
  const visited = new Set();
  for (const char of str) {
    if (visited.has(char)) {
      return char;
    } else {
      visited.add(char);
    }
  }
  return null;
}
```



## [106. Find two numbers that sum up to 0](https://bigfrontend.dev/problem/Find-two-numbers-that-sum-up-to-0)

### 题目

Given an array of integers, find two number that sums up to 0, return their indices.

There might be multiple pairs, any of them would do. If not found, return `null`

```js
findTwo([1,2,3,-1])
// [0,3]

findTwo([1,2,3,-1,-2,0])
// [0,3] or [1,4] or [5, 5]

findTwo([1,2,3,4])
// null
```

### 答案

使用哈希记录数据和位置

```js
/**
 * @param {number[]} arr
 * @return {number[]}
 */
function findTwo(arr) {
  if (!arr || arr.length < 2) {
    return null;
  }
  let visited = new Map();
  for (let i in arr) {
    if (visited.has(-arr[i])) return [visited.get(-arr[i]), i];
    visited.set(arr[i], i);
  }
  return null;
}
```



## [107. Find the largest difference](https://bigfrontend.dev/problem/Find-the-largest-difference)

### 题目

Given an array of numbers, pick any two numbers `a` and `b`, we could get the difference by `Math.abs(a - b)`.

Can you write a function to get the largest difference?

```js
largestDiff([-1, 2,3,10, 9])
// 11,  obviously Math.abs(-1 - 10) is the largest

largestDiff([])
// 0

largestDiff([1])
// 0
```

### 答案

找最大值和最小值，相减

```js
/**
 * @param {number[]} arr
 * @return {number}
 */
function largestDiff(arr) {
  if (!arr) return 0;
  const n = arr.length;
  if (n < 2) return 0;
  let min = Infinity;
  let max = -Infinity;

  for (let i = 0; i < n; i++) {
    min = Math.min(arr[i], min);
    max = Math.max(arr[i], max);
  }

  return max - min;
}
```



## [108. Implement a Stack by using Queue](https://bigfrontend.dev/problem/Implement-a-Stack-by-using-Queue)

### 题目

> This is reversed problem of [13. Implement a Queue by using Stack](https://bigfrontend.dev/problem/implement-a-queue-by-using-stack)

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

now suppose you have a Queue, which has only follow interface:

```js
class Queue {
  enqueue(element) { /* add element to queue, similar to Array.prototype.push */ }
  peek() { /* get the head element*/ }
  dequeue() { /* remove the head element, similar to Array.prototype.pop */ }
  size() { /* count of elements */ }
}
```

Could you implement a Stack by using only above Queue? A Stack must have following interface

```js
class Stack {
  push(element) { /* add element to stack */ }
  peek() { /* get the top element */ }
  pop() { /* remove the top element */}
  size() { /* count of elements */}
}
```

*note*

you can only use Queue as provided. Don't use Array, it is not what this is for.

Related Problems

[13. Implement a Queue by using Stack ](https://bigfrontend.dev/problem/implement-a-queue-by-using-stack)

### 答案

封装一个栈

```js
/* you can use this Queue which is bundled together with your code
class Queue {
  enqueue(element) {
    // add new element to the queue
  }
  peek() { 
    // return the head element
  }
  dequeue() { 
    // remove head element from the queue
  }
  size() { 
    // return the queue size
  }
}
*/


// you need to complete the following Stack, using only Queue
class Stack {
  constructor() {
    this.queue = new Queue();
  }
  push(element) {
    // push an element into the stack
    let rotations = this.size();
    this.queue.enqueue(element);
    while (rotations > 0) {
      this.queue.enqueue(this.queue.dequeue());
      rotations--;
    }
  }
  peek() { 
    // get the top element
    return this.queue.peek();
  }
  pop() { 
    // remove top element from stack
    return this.queue.dequeue();
  }
  size() { 
    // return count of elements
    return this.queue.size();
  }
}
```



## [109. implement `Math.pow()`](https://bigfrontend.dev/problem/implement-Math-pow)

### 题目

Can you write your own [Math.pow()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/pow) ? The power would only be integers.

```js
pow(1, 2)
// 1

pow(2, 10)
// 1024

pow(4, -1)
// 0.25
```

All inputs are safe.

**Follow-up**

You can easily solve this problem by multiplying the base one after another, but it is slow. For power of `n`, it is needed to do the multiplication `n` times, can you think of a faster solution ?

### 答案

按2的次方计算

```js
/**
 * @param {number} base
 * @param {number} power - integer
 * @return {number}
 */
function pow(base, power){
  if (power < 0) {
    return 1 / powBinary(base, -power);
  }
  
  return powBinary(base, power);
}

function powBinary(base, power) {
  if (power === 0) return 1;
  if (power === 1) return base;
  
  const halfRes = pow(base, Math.floor(power / 2));
  return power % 2 == 0 ? halfRes * halfRes : halfRes * halfRes * base;
}
```



## [110. serialize and deserialize binary tree](https://bigfrontend.dev/problem/serialize-and-deserialize-binary-tree)

### 题目

Can you transform(serialize) a binary tree into a string and restore(deserialize) a binary tree from the string? Just like what [JSON.stringify()](https://bigfrontend.dev/problem/implement-JSON-stringify) and [JSON.parse()](https://bigfrontend.dev/problem/implement-JSON-parse) do.

For example, for a tree from [91. invert a binary tree](https://bigfrontend.dev/problem/invert-a-binary-tree)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20211016212854.png)

BFE.dev would serialize it to `[1,2,3,4,null,null,5,6,7,8,null,null,null,null,9]`

But there are more ways of doing it rather than above, any would be fine as long as your `deserialize()` and `serialize()` work as a pair.

Your code is tested like this:

```js
const tree1 = ...
expect(typeof serialize(tree1)).toBe('string')

const tree2 = deserialize(serialize(tree1)) 
expect(isIdentical(tree1, tree2)).toBe(true)
```

Binary tree in this problem consists of value of integers.

### 答案

递归实现

```js
// This is the class for the node
// you can use this directly as it is bundled with your code

// class Node {
//   value: number
//   left: null | Node
//   right: null | Node
//   constructor(val) {
//     this.value = val
//     this.left = null
//     this.right = null
//   }
// }

/**
 * @param {Node} root
 * @return {string}
 */
function serialize(root) {
  if (!root) return 'null';

  return `${root.val},${serialize(root.left)},${serialize(root.right)}`
}

/**
 * @param {string} str
 * @return {Node}
 */
function deserialize(str) {
  const arr = str.split(',');

  return dfs(arr);

  function dfs(queue) {
    if (!queue.length) return null;
    const v = queue.shift();

    if (v === 'null') {
      return null;
    } else {
      const node = new Node(v);
      node.left = dfs(queue);
      node.right = dfs(queue);
      return node;
    }
  }
}
```



## [111. Count palindromic substrings](https://bigfrontend.dev/problem/Count-palindromic-substrings)

### 题目

A palindromic string reads the same backward as forward, such as `'madam'`.

Now given a string, count how many substrings it has?

Like `'madam'`, it has following palindromic strings:

```js
'm'
'a'
'd'
'a'
'm'
'ada'
'madam'
```

What is the time and space cost of your solution ? Could you improve it ?

> Thanks to [@TechieQian](https://bigfrontend.dev/user/TechieQian) for helping with the test cases.

### 答案

```js
/**
 * @param {string} str
 * @return {number}
 */
function countPalindromicSubstr(str) {
  let countPal = 0;
  if(str.length === 0) return countPal;

  const n = str.length;
  for (let i = 0; i < n; i++) {
    expand(i, i); // odd palindrome
    expand(i, i + 1); // even palindrome
  }

  function expand(left, right) {
    while (left >= 0 && right < n && str[left] === str[right]) {
      countPal++;
      left--;
      right++;
    }
  }

  return countPal;
}
```

巧妙利用0.5

```js
/**
 * @param {string} str
 * @return {number}
 */
function countPalindromicSubstr(str) {
  let count = 0;
  
  for (let center = 0; center < str.length; center = center + 0.5) {
    let low = Math.floor(center);
    let high = Math.ceil(center);
    
    while (low >= 0 && high < str.length) {
      if (str.charAt(low) === str.charAt(high)) {
          count++;
          low--;
          high++;
      } else {
        break; 
      }
    }
  }
  return count;  
}
```



## [112. remove duplicate characters in a string](https://bigfrontend.dev/problem/remove-duplicate-letters-in-a-string)

### 题目

Given a string which, write a function to remove the duplicate characters to make sure that each character only occurs once.

For example

```js
'xyzabcxyzabc'
```

Each character appears twice, we could make it unique as follows

```js
'xyzabc'
'xyabcz'
'xabcyz'
'abcxyz'
'abxyzc'
.....
```

Above all substrings subsequences ([*](https://bigfrontend.dev/problem/112/discuss/394)) contains unique characters, but you need to return the **smallest** one in lexicographical order( 'a' -> 'z'), which is `'abcxyz'`.

All input only contains valid lowercase alphabets only.

### 答案

找到重复的字母

```js
/**
 * @param {string} str
 * @return {string}
 */
function smallestUniqueSubstr(str) {
  if (str.length === 0) return str;

  let subStr = new Set();
  let lastElem = str[str.length - 1];
  subStr.add(lastElem);
  for (let i = str.length - 2; i >= 0; i--) {
    let char = str[i];
    if (char < lastElem || !subStr.has(char)) {
      subStr.delete(char);
      subStr.add(char);
      lastElem = char;
    }
  }

  return Array.from(subStr).reverse().join('');
}
```



## [113. Virtual DOM I](https://bigfrontend.dev/problem/Virtual-DOM-I)

### 题目

Suppose you have solved [110. serialize and deserialize binary tree](https://bigfrontend.dev/problem/serialize-and-deserialize-binary-tree), have you wondered how to do similar task to DOM tree ?

HTML string could be thought as some sort of [serialization](https://en.wikipedia.org/wiki/Serialization), the browser parses(deserialize) the HTML → construct the DOM tree.

Besides XML base, we could try JSON for this. If we log the element presentation in React, like below

```jsx
const el = 
  this is 
  a  button  from BFE.dev
 
;

console.log(el)
```

we would get this( ref, key .etc are stripped off)

```js
{
  type: 'div',
  props: {
    children: [
      {
        type: 'h1',
        props: {
          children: ' this is '
        }
      },
      {
        type: 'p',
        props: {
          className: 'paragraph',
          children: [
            ' a ',
            {
              type: 'button',
              props: {
                children: ' button '
              }
            },
            ' from',
            {
              type: 'a',
              props: {
                href: 'https://bfe.dev',
                children: [
                  {
                    type: 'b',
                    props: {
                      children: 'BFE'
                    }
                  },
                  '.dev'
                ]
              }
            }
          ]
        }
      }
    ]
  }
}
```

Clearly this is the same tree structure but only in object literal.

Now you are asked to serialize/deserialize the DOM tree, like what React does.

**Note**

**Functions like event handlers and custom components are beyond the scope of this problem, you can ignore them**, just focus on basic HTML tags.

You should support:

1. TextNode (string) as children
2. single child and multiple children
3. camelCased properties.

`virtualize()` takes in a real DOM tree and create an object literal `render()` takes in a object literal presentation and recreate a DOM tree.

Related Problems

[118. Virtual DOM II - createElement](https://bigfrontend.dev/problem/virtual-dom-II-createElement)
[140. Virtual DOM III - Functional Component](https://bigfrontend.dev/problem/virtual-DOM-III-Functional-Component)
[143. Virtual DOM IV - JSX 1](https://bigfrontend.dev/problem/virtual-dom-iv-jsx-1)
[150. Virtual DOM V - JSX 2](https://bigfrontend.dev/problem/virtual-dom-v-jsx-2)

### 答案

BFS转化

```js
/**
 * @param {HTMLElement} 
 * @return {object} object literal presentation
 */
function virtualize(element) {
  // virtualize top level element
  // recursively handle the children (childNodes)
  const result = {
    type: element.tagName.toLowerCase(),
    props: {}
  }
  // props (without children)
  for (let attr of element.attributes) {
    const name = attr.name === 'class' ? 'className' : attr.name;
    result.props[name] = attr.value;
  }
  // children
  const children = [];
  for (let node of element.childNodes) {
    if (node.nodeType === 3) {
      children.push(node.textContent);
    } else {
      children.push(virtualize(node));
    }
  }
  result.props.children = children.length === 1 ? children[0] : children;
  
  return result;
}


/**
 * @param {object} valid object literal presentation
 * @return {HTMLElement} 
 */
function render(obj) {
  // create the top level emlement
  // recursively append the children
  // textnode
  if (typeof obj === 'string') {
    return document.createTextNode(obj);
  }

  // element
  const {type, props: {children, ...attrs}} = obj;
  const element = document.createElement(type);

  for (let [attr, value] of Object.entries(attrs)) {
    element[attr] = value;
  }

  const childrenArr = Array.isArray(children) ? children : [children];
  
  for (let child of childrenArr) {
    element.append(render(child));
  }
  
  return element;
}
```

The core of this problem is familiar with DOM API, and there are few I used:

Create

- document.createElement(tagName)
- document.createTextNode(tagName)

Add

- node.append(childNode)
- node.classList.add(className)

Set

- node.setAttribute(name, value)

Check

- node.hasChildNodes()
- node.hasAttributes()

Get

- node.childNodes
- node.tagName
- node.attributes // {0: {name: xxx, value: xxx}, 1: {name: xxx, value: xxx}...}

NodeType

- node.nodeType == 1 // element node
- node.nodeType == 2 // attribute node
- node.nodeType == 3 // text node

```js
function virtualize(element) {
  const result = {
    type: element.tagName.toLowerCase(),
    props: {}
  };

  const props = {};

  // handle props
  if (element.hasAttributes()) {
    for (let { name, value } of element.attributes) {
      props[name === 'class' ? 'className' : name] = value;
    }
  }

  // handle children
  const children = [];
  if (element.hasChildNodes()) {
    for (let node of element.childNodes) {
      if (node.nodeType === 1) {
        children.push(virtualize(node));
      } else if (node.nodeType === 3) {
        children.push(node.textContent)
      }
    }
  }

  if (children.length) {
    if (children.length === 1) {
      props.children = children[0];
    } else {
      props.children = children;
    }
  }

  result.props = props;
  return result;
}


/**
 * @param {object} valid object literal presentation
 * @return {HTMLElement} 
 */
function render(obj) {
  // render self
  let { type, props: { className, children, ...restProps } } = obj;
  const ele = document.createElement(type);

  // add className
  if (className) ele.classList.add(className);

  // append children
  if (children) {
    // make sure children is Array
    if (!(children instanceof Array)) {
      children = [children];
    }
    // render each child and append it to parent
    children.forEach(child => {
      // text node
      if (typeof child === 'string') {
        ele.append(document.createTextNode(child));
      } else {
        ele.append(render(child));
      }
    })
  }

  // add rest props to ele
  if (restProps) {
    Object.entries(restProps).forEach(([key, value]) => {
      ele.setAttribute(key, value);
    });
  }
  return ele;
}
```



## [114. implement BigInt multiplication](https://bigfrontend.dev/problem/implement-BigInt-multiplication)

### 题目

This is a follow-up on [76. implement BigInt addition with sign](https://bigfrontend.dev/problem/implement-BigInt-addition-with-sign).

You are asked to create a function that multiplies two big integers in string.

```js
multiply(
  '1123456787654323456789', 
  '1234567887654323456'
)
// '1386983673205309924427166592431045142784'
```

### 答案

按照每位进行计算

```js
/**
 * @param {string} a 
 * @param {string} b
 * @return {string}
 */
function multiply(a, b) {
  let sign = '';

  if (a === '0' || b === '0') return '0';

  if(a[0] === '-' && b[0] !== '-') {
    sign = '-';
    a = a.substr(1);
  } else if (a[0] !== '-' && b[0] === '-') {
    sign = '-';
    b = b.substr(1);
  } else if (a[0] === '-' && b[0] === '-') {
    a = a.substr(1);
    b = b.substr(1);
  }

  let result = new Array(a.length + b.length).fill(0);

  for(let i = a.length - 1; i >= 0; i --) {
    for(let j = b.length - 1; j >= 0; j--) { 
      const m = i + j + 1;
      const n = i + j;

      const s = (+a[i]) * (+b[j]) + result[m] 
      result[m] = s % 10;
      result[n] += Math.floor(s / 10);
    }
  }

  while (result[0] === 0) {
    result.shift();
  }

  return sign + result.join('');
}
```



## [115. implement BigInt division](https://bigfrontend.dev/problem/implement-BigInt-division)

### 题目

This is a follow-up on [114. implement BigInt multiplication](https://bigfrontend.dev/problem/implement-BigInt-multiplication).

You are asked to create a BigInt division function.

```js
divide(
  '1123456787654323456789', 
  '1234567887654323456'
)
// '910'

divide(
  '-1123456787654323456789', 
  '1234567887654323456'
)
// '-910'
```

Notice the result should be **rounded towards 0**.

```js
divide(
  '5', 
  '2'
)
// '2'

divide(
  '-3', 
  '2'
)
// '-1'
```

### 答案

```js

/**
 * @param {string} a
 * @param {string} b
 * @return {string}
 */
function divide(a, b) {
  const negRes = ((a.startsWith('-') && !b.startsWith('-')) || (b.startsWith('-') && !a.startsWith('-')));
  if (a.startsWith('-')) a = a.slice(1);
  if (b.startsWith('-')) b = b.slice(1); 
  if (b === '0') throw new Error('');
  if (b === '1') return negRes ? '-' + a : a;
  
  let res = b;
  let count = 1;
  while((+res < +a || res < a) && res.length <= a.length) {
    res = add(res, b);
    count++;
  }
  count--
  return negRes ?  '-' + count : ''  + count;
}

function add(num1, num2) {
  if (num1.length > num2.length) {
    num2 = num2.padStart(num1.length, 0);
  } else if (num2.length > num1.length) {
    num1 = num1.padStart(num2.length, 0);
  }
  let carry = 0;
  let res = '';
  for (let i = num1.length - 1; i >= 0; i--) {
    const ans = +num1[i] + +num2[i] + carry;
    if (ans >= 10) {
      carry = Math.floor(ans/10);
      res = `${ans%10}${res}`;
    } else {
      carry = 0;
      res = `${ans}${res}`;
    }
  }
  if (carry > 0) {
    res = `${carry}${res}`;
  }
  return res;
}
```



## [116. implement Object.is()](https://bigfrontend.dev/problem/implement-Object.is)

### 题目

[Object.is()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) is similar to `===` except following cases

```js
Object.is(0, -0) // false
0 === -0 // true

Object.is(NaN, NaN) // true
NaN === NaN // false
```

Here is the [detailed spec](https://www.ecma-international.org/ecma-262/6.0/#sec-samevalue), can you implement your own `is()`?

### 答案

特别判断一下类型

```js
/**
 * @param {any} a
 * @param {any} b
 * @return {boolean}
 */
function is(a, b) {
  if (typeof a === 'number' && typeof b === 'number') {
    if (Number.isNaN(a) && Number.isNaN(b)) {
      return true;
    }

    // 区分正负0
    if (a === 0 && b === 0 && 1 / a !== 1 / b) {
      return false;
    }
  }
  return a === b;
}
```

大佬的巧妙方法

```js
/**
 * @param {any} a
 * @param {any} b
 * @return {boolean}
 */
function is(a, b) {
  if (a !== a) { // Only NaN is not equal to itself
    return b !== b; // returns true if the second parameter is NaN too
  }

  if (a === 0 && b === 0) { // -0 === 0 is true, so when both parameters equals to 0
    return 1 / a === 1 / b; // 1 / -0 is -Infinity and -Infinity === -Infinity
  }

  return a === b; // All other cases with regular === comparison
}
```



## [117. event delegation](https://bigfrontend.dev/problem/event-delegation)

### 题目

[What is Event Delegation?](https://bigfrontend.dev/question/What-is-Event-Delegation)

Can you create a function which works like [jQuery.on()](https://api.jquery.com/on/), that attaches event listeners to selected elements.

In jQuery, selector is used to target the elements, in this problem, it is changed to a predicate function.

```js
onClick(
  // root element
  document.body,  
  // predicate
  (el) => el.tagName.toLowerCase() === 'div',  
  function(e) {
    console.log(this);
    // this logs all the `div` element
  }
)
```

1. [event.stopPropagation()](https://developer.mozilla.org/en-US/docs/Web/API/Event/stopPropagation) and [event.stopImmediatePropagation()](https://developer.mozilla.org/en-US/docs/Web/API/Event/stopImmediatePropagation) should also be supported.
2. you should only attach one real event listener to the root element.

### 答案

添加监听器

```js
const allHandlers = new Map();
/**
 * @param {HTMLElement} root
 * @param {(el: HTMLElement) => boolean} predicate
 * @param {(e: Event) => void} handler
 */
function onClick(root, predicate, handler) {
  if (allHandlers.has(root)) {
    allHandlers.get(root).push([predicate, handler]);
    return;
  }

  allHandlers.set(root, [[predicate, handler]]);

  // attach the real handler
  root.addEventListener('click', function(e) {
    // from e.target -> root
    // check if element shoulded applied witht handler

    let el = e.target;
    const handlers = allHandlers.get(root);
    let isPropagationStopped = false;

    e.stopPropagation = () => {
      isPropagationStopped = true;
    }

    while (el) {
      let isImmediatePropagationStopped = false;

      e.stopImmediatePropagation = () => {
        isImmediatePropagationStopped = true;
        isPropagationStopped = true;
      }

      for (const [predicate, handler] of handlers) {
        if (predicate(el)) {
          handler.call(el, e);

          // check immediatepropagtion
          if (isImmediatePropagationStopped) {
            break;
          }
        }
      }

      // check propagation
      if (el === root || isPropagationStopped) break;

      el = el.parentElement;
    }
  }, false);
}
```

不需要用map存储

```js
/**
 * @param {HTMLElement} root
 * @param {(el: HTMLElement) => boolean} predicate
 * @param {(e: Event) => void} handler
 */
function onClick(root, predicate, handler) {
  if (root.handlers) {
    root.handlers.push([predicate, handler]);
  } else {
    const originalstopImmediatePropagation = Event.prototype.stopImmediatePropagation;
    Event.prototype.stopImmediatePropagation = function () {
      this.immediatePropagationStopped = true;
      originalstopImmediatePropagation.apply(this, arguments);
    };
    root.addEventListener('click', function (e) {
      let node = e.target;
      while (node !== root) {
        for (const [p, h] of root.handlers) {
          if (p(node)) h.call(node, e);
          if (e.immediatePropagationStopped) return;
        }
        if (e.cancelBubble) return;
        node = node.parentElement;
      }
    });
    root.handlers = [[predicate, handler]];
  }
}
```



## [118. Virtual DOM II - createElement](https://bigfrontend.dev/problem/virtual-dom-II-createElement)

### 题目

> This is a follow-up on [113. Virtual DOM I](https://bigfrontend.dev/problem/Virtual-DOM-I).

Suppose you have solved above problem, now let's take a look at [React.createElement()](https://reactjs.org/docs/react-api.html#createelement):

```js
React.createElement(
  type,
  [props],
  [...children]
)
```

1. First argument is type, it could be set to Custom Component, but here in this problem, it would only be HTML tag name
2. Second argument is props, here in this problem, it would only be the (common) camelCased HTML attributes
3. the rest arguments are the children, which in React supports many data types, but in this problem, it only has the element type of MyElement, or string for TextNode.

**You are asked to create your own createElement() and render()**, so that following code could create the exact HTMLElement in [113. Virtual DOM I](https://bigfrontend.dev/problem/Virtual-DOM-I).

```js
const h = createElement

render(h(
  'div',
  {},
  h('h1', {}, ' this is '),
  h(
    'p',
    { className: 'paragraph' },
    ' a ',
    h('button', {}, ' button '),
    ' from ',
    h('a', 
      { href: 'https://bfe.dev' }, 
      h('b', {}, 'BFE'),
      '.dev')
  )
))
```

**Notes**

1. The goal of this problem is not to create the replica of React implementation, you can have your own object representation format other than the one in [113. Virtual DOM I](https://bigfrontend.dev/problem/Virtual-DOM-I).
2. Details about ref, key are ignored here, they will be put in other problems. Re-render is not covered here, it will be in another problem as well.

Related Problems

[113. Virtual DOM I ](https://bigfrontend.dev/problem/Virtual-DOM-I)
[140. Virtual DOM III - Functional Component](https://bigfrontend.dev/problem/virtual-DOM-III-Functional-Component)
[143. Virtual DOM IV - JSX 1](https://bigfrontend.dev/problem/virtual-dom-iv-jsx-1)
[150. Virtual DOM V - JSX 2](https://bigfrontend.dev/problem/virtual-dom-v-jsx-2)

### 答案

遍历插入属性

```js
/**
 * MyElement is the type your implementation supports
 *
 * type MyNode = MyElement | string
 */

/**
 * @param { string } type - valid HTML tag name
 * @param { object } [props] - properties.
 * @param { ...MyNode} [children] - elements as rest arguments
 * @return { MyElement }
 */
function createElement(type, props, ...children) {
  return {
    type,
    props: {
      ...props,
      children
    }
  }
}


/**
 * @param { MyElement }
 * @returns { HTMLElement } 
 */
function render(myElement) {
  // create the top level emlement
  // recursively append the children
  // textnode
  if (typeof myElement === 'string') {
    return document.createTextNode(myElement);
  }
  
  // element
  const {type, props: {children, ...attrs}} = myElement;
  const element = document.createElement(type);

  for (let [attr, value] of Object.entries(attrs)) {
    element[attr] = value;
  }

  const childrenArr = Array.isArray(children) ? children : [children];

  for (let child of childrenArr) {
    element.append(render(child));
  }

  return element;
}
```



## [119. create a tokenizer](https://bigfrontend.dev/problem/create-a-tokenizer)

### 题目

> Given a character sequence and a defined document unit, tokenization is the task of chopping it up into pieces, called tokens , perhaps at the same time throwing away certain characters, such as punctuation. ([ref](https://nlp.stanford.edu/IR-book/html/htmledition/tokenization-1.html))

For tasks of string processing, in many cases we are given a string, and are asked to understand this string in specific logic and then return the result.

For example, if we are to calculate the result of following expression:

```js
 1 * (20 -   300      ) 
```

before we implement the logic, first we need to get the "keywords"(tokens) and ignore the spaces, like following:

```js
'1','*', '(', '20', '-', '300', ')'
```

Then we can process above tokens and get the result easier.

**You are asked to implement a tokenize() for arithmetic expression** , which works as below:

```js
const tokens = tokenize(' 1 * (20 -   300      ) ')


while (true) {
  let token = tokens.next()
  if (token.done) {
    break
  }
  console.log(token.value)
}
```

or you can use `for ... of`

```js
for (let token of tokens) {
    console.log(token)   
}
```

Because it is trivial, in a real interview you talk to interviewer and implement tokenizer later, this could save yourself some time for more important tasks.

**Input**

1. input only contains valid non-negative integers with `+`, `-`, `*`, `/`, `(`, `)` and spaces, space should be ignored.
2. your method should return an [Generator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator) object.

### 答案

使用正则表达式

```js
/**
 * @param {string} str
 * @return {Generator}
 */
function* tokenize(str) {
  let tokens = str.match(/(\d+)|[\+\-\*\/\(\)]/g);
  for (let token of tokens) {
    yield token;
  }
}
```

一个个查找遍历

```js
/**
 * @param {string} str
 * @return {Generator}
 */
function* tokenize(str) {
  let buffer = '';
  const set = new Set(['+', '-', '*', '/', '(', ')']);
  for (let s of str) {
    if (set.has(s)) {
      if (buffer !== '') {
        yield buffer;
        buffer = '';
      }
      yield s;
      continue;
    }
    if(s !== ' ') buffer += s;
  }
  if (buffer !== '') {
    yield buffer;
  }
}
```



## [120. create `isPrime()`](https://bigfrontend.dev/problem/isPrime)

### 题目

A [Prime number](https://en.wikipedia.org/wiki/Prime_number) is a natural number greater than 1 that is divisible only by itself and 1, such as 2,3,5....

You are asked to implement `isPrime()` to check if a number is prime.

**Follow-up**

What is the time cost of your implementation ? can you improve your approach to have the fewest comparisons?

### 答案

小学时代的方法

```js
/**
 * @param {number} num - positive integer
 */
function isPrime(num) {
  if (num <= 1) return false;
  const sqrtNum = Math.floor(Math.sqrt(num));
  for (let i = 2; i <= sqrtNum; i++) {
    if (num % i === 0) return false;
  }
  return true;
}
```

利用费马小定理判断素数

```js
/**
 * @param {number} num - positive integer
 */
function isPrime(num) {
  if(num <= 1 || num === 4) return false;
  if(num === 2 || num === 3) return true;

  let times = 5;
  while(times-- > 0) {
    // [0, n - 3) => [2, n - 1)
    let rand = Math.floor(Math.random() * (num - 3) + 2);
    if(mod(rand, num) !== 1) return false;
  }
  return true;
}
// a^(n-1) % n
function mod(a, n) {
  let res = 1, power = n - 1;

  while (power > 0){
    if (power % 2 === 1) {
      res = (res * a) % n;
    } 
    power = power >> 1; 
    a = (a * a) % n;
  }
  return res;
}
```



## [121. A number sequence](https://bigfrontend.dev/problem/A-number-sequence)

### 题目

Here is a sequence:

1. `'1'`, first number is 1
2. `'11'`, since previous number has One(1) 1
3. `'21'`, since previous number has Two(2) 1s
4. `'1211'`, since previous number has One(1) 2 and One(1) 1
5. `'111221'`, since previous number has One(1) 1, One(1) 2, Two(2) 1s
6. `'312211'`, since previous number has Three(3) 1s, Two(2) 2s, One(1) 1
7. ....

As explained above, the sequence is generated by counting the digits of previous number.

Please create `getNthNum(n)` to return the n-th number string in the sequence, n starts from 1.

[Source for this ](https://www.glassdoor.com/Interview/Given-a-value-N-return-the-corresponding-string-according-to-this-pattern-1-11-21-1211-111221-312211-QTN_604640.htm)

### 答案

直接每次遍历字符串统计数字

```js
/**
 * @param {number} n - integer
 * @returns {string}
 */
function getNthNum(n) {
  let num = "1";
  while (n > 1) {
    num = transform(num);
    n--;
  }
  return num;
}

const transform = (num) => {
  let currentDigit = '';
  let currentCount = 0;
  let result = '';
  for (let i = 0; i <= num.length; i++) {
    if (num[i] === currentDigit) {
      currentCount ++;
    } else {
      if (currentCount > 0) {
        result += currentCount + currentDigit;
      }

      currentDigit = num[i];
      currentCount = 1;
    }
  }
  return result;
}
```



## [122. implement memoizeOne()](https://bigfrontend.dev/problem/A-number-sequence)

### 题目

In problem [14. Implement a general memoization function](https://bigfrontend.dev/problem/implement-general-memoization-function), you are asked to implement a memo function without space concern.

But in reality, it could be a problem if cache bloats.

You might need to restrict the cache capacity, just like [memoize-one](https://github.com/alexreardon/memoize-one) , it only remembers the latest arguments and result.

Please implement your own `memoizeOne()`, it takes 2 arguments

1. target function
2. (optional) a equality check function to compare current and last arguments

Default equality check function should be a shallow comparison on array items with strict equal `===`.

Related Problems

[14. Implement a general memoization function - `memo()` ](https://bigfrontend.dev/problem/implement-general-memoization-function)

### 答案

```js
/**
 * @param {Function} func
 * @param {(args: any[], newArgs: any[]) => boolean} [isEqual]
 * @returns {any}
 */
const defaultIsEqual = (args1, args2) => {
  return JSON.stringify(args1) === JSON.stringify(args2);
}

function memoizeOne(func, isEqual = defaultIsEqual) {
  let cache = {};

  return function(...args) {
    if (this === cache.self && isEqual(args, cache.args)) {
      return cache.value;
    }

    cache.args = args;
    cache.self = this;
    cache.value = func.apply(this, args);

    return cache.value;
  }
}
```



## [123. implement Promise.prototype.finally()](https://bigfrontend.dev/problem/implement-Promise-prototype-finally)

### 题目

[Promise.prototype.finally()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/finally) could be used to run a callback when a promise is settled(either fulfilled or rejected).

Notice that the callback passed `finally()` doesn't receive any argument, meaning it doesn't modify the value in the promise chain (care for rejection).

### 答案

利用resolve和reject

```js
/**
 * @param {Promise<any>} promise
 * @param {() => void} onFinally
 * @returns {Promise<any>}
 */
function myFinally(promise, onFinally) {
  return promise.then((value) => {
    return Promise.resolve(onFinally())
      .then(() => value);
  }, (reason) => {
    return Promise.resolve(onFinally())
      .then(() => Promise.reject(reason));
  })
}
```



## [124. calculate arithmetic expression](https://bigfrontend.dev/problem/calculate-arithmetic-expression)

### 题目

In [119. create a tokenizer](https://bigfrontend.dev/problem/create-a-tokenizer), you are able to extract the tokens from a string with invalid spaces.

Now please `calculate()` the result of the string. You can use the tokenizer you wrote before.

```js
calculate('1 * (20 -   300      ) ')
// -280

calculate('     1/0 ')
// Infinity
```

1. the input expression is syntactically valid, containing non-negative integers, `+`, `-`, `*`, `/`, `(`, `)` and spaces
2. Don't use `eval()`

### 答案

逆波兰表达式+栈

```js
/**
 * @param {string} str
 * @returns {Number}
 */
function calculate(str) {
  function calc(start) {
    const stack = [];
    let num = 0;
    let sign  = '+';
    let lastIndex;
    let signs = new Set(['+', '-', '/', '*', ')']);
    for (let i=start; i<str.length; i++) {
      const char = str.charAt(i);
      if (char === ' ') continue;
      if (!isNaN(+char)) {
        num = num * 10 + +char;
      } else if (signs.has(char)) {
        switch (sign) {
          case '+':
            stack.push(num);
            break;
          case '-':
            stack.push(-num);
            break;
          case '*':
            stack[stack.length - 1] *= num;
            break;
          case '/':
            stack[stack.length - 1] /= num;
            break;
        }
        if (char === ')') {
          lastIndex = i;
          break;
        }
        sign = char;
        num = 0;
      } else if (char === '(') {
        const [newNum, index] = calc(i + 1);
        i = index;
        lastIndex = i;
        num = newNum;
      }
    }
    const val = stack.reduce((accm, curr) => accm + curr, 0);
    return [val, lastIndex];
  }
  str = str + '+';
  return calc(0)[0];
}
```



## [125. implement classNames()](https://bigfrontend.dev/problem/implement-classnames)

### 题目

If using React, you can set the prop `className` to add class name to an element, it is string so you can add multiple class names like this:

```jsx
  BFE.dev can help
```

When class names are dynamically generated, it becomes verbose.

```jsx
  BFE.dev can help
```

[classnames](https://www.npmjs.com/package/classnames) can help with this.

`classNames()` accepts arbitrary arguments, filter out the falsy values, and generate the final className string.

1. string and number are used directly.

```js
classNames('BFE', 'dev', 100) 
// 'BFE dev 100'
```

1. other primitives are ignored.

```js
classNames(
  null, undefined, Symbol(), 1n, true, false
) 
// ''
```

1. Object's enumerable property keys are kept if the key is string and value is truthy. [Array should be flattened](https://bigfrontend.dev/problem/implement-Array-prototype.flat).

```js
const obj = new Map()
obj.cool = '!'

classNames({BFE: [], dev: true, is: 3}, obj) 
// 'BFE dev is cool'

classNames(['BFE', [{dev: true}, ['is', [obj]]]])
// 'BFE dev is cool'
```

**Please implement your own classNames()**.

**note**

It is not the goal to reimplement the original package, so the spec might be a little different, please follow the above description.

### 答案

```js
/**
 * @param {any[]} args
 * @returns {string}
 */
function classNames(...args) {
  return args.flat(Infinity).reduce((result, item) => {
    if (item === null) return result;
    
    switch (typeof item) {
      case 'string':
      case 'number':
        result.push(item);
        break;
      case 'object':
        for (let [key, value] of Object.entries(item)) {
          if (!!value) {
            result.push(key);
          }
        }
        break;
    }

    return result;
  }, []).join(' ');
}
```

极简写法

```js
/**
 * @param {any[]} args
 * @returns {string}
 */
const classNames = (...args) =>
  args.flat(Infinity).reduce((outterAcc, cur) => [
    ...outterAcc,
    ...(() => {
      if (['string', 'number'].includes(typeof cur)) {
        return [cur];
      }

      if (cur && (typeof cur === 'object')) {
        return Object.entries(cur).reduce((innerAcc, [key, val]) => [
          ...innerAcc,
          ...(val ? [key] : []),
        ], []);
      }

      return [];
    })(),
  ], []).join(' ');
```



## [126. BigDecimal addition](https://bigfrontend.dev/problem/decimal-addition)

### 题目

As you know, number data type in JavaScript cannot represent (all) float numbers accurately due to binary nature.

For some basic calculations, you might use `Number.prototype.toFixed()` to overcome this, yet for more extreme cases that requires perfect accuracy, it is not enough.

[Proposal of BigDecimal](https://github.com/tc39/proposal-decimal) to JavaScript is still at an early stage, before it is adopted, you can use other libraries like [Big.js](https://github.com/MikeMcl/big.js/).

In this problem, you are asked to **implement the addition of two decimals with arbitrary digits**.

```js
add('-999999999999999999', '-1')
// '-1000000000000000000'

add(
  '-999999999999999999.999999999999999999999999999999', 
  '1.0000000000000000000000000001')
// '-999999999999999998.999999999999999999999999999899'

add(
  '999999999999999999.9999999999999999999999999999', 
  '1.0000000000000000000000000001')
// '1000000000000000001'
```

1. This problem covers [76. implement BigInt addition with sign](https://bigfrontend.dev/problem/implement-BigInt-addition-with-sign).
2. trailing zeroes in the result should be removed.

Related Problems

[127. BigDecimal subtraction](https://bigfrontend.dev/problem/bigdecimal-subtraction)

### 答案

转化为bigInt和小数点位数

```js
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
function add(num1, num2) {
  const a = toBig(num1), b = toBig(num2);
  const maxPow = Math.max(a.pow, b.pow);

  if (maxPow) {
    const bigA = normalize(a, maxPow), bigB = normalize(b, maxPow);
    let res = String(bigA + bigB);
    let sign = '';
    if (res[0] === '-') {
      sign = '-';
      res = res.substring(1);
    }
    if (res.length <= maxPow) {
      res = '0'.repeat(maxPow - res.length + 1) + res;
    }
    const pointPos = res.length - maxPow;
    res = res.substr(0, pointPos) + '.' + res.substr(pointPos);
    return sign + res.replace(/\.?0+$/, '');
  } else {
    return String(a.num + b.num);
  }
}

function toBig(n) {
  let pow = 0;
  n = n.replace(/\.(\d+?)0*$/, (_, f) => {
    pow = f.length;
    return f;
  })
  return { num: BigInt(n), pow };
}

function normalize(big, maxPow) {
  return big.num * (10n ** BigInt(maxPow - big.pow));
}
```



## [127. BigDecimal subtraction](https://bigfrontend.dev/problem/bigdecimal-subtraction)

### 题目

> This is a follow-up on [126. BigDecimal addition](https://bigfrontend.dev/problem/decimal-addition)

In this problem, you are asked to **implement the subtraction of two decimals with arbitrary digits**.

```js
subtract('-999999999999999999', '-1')
// '-999999999999999998'

subtract(
  '-999999999999999999.999999999999999999999999999999', 
  '1.0000000000000000000000000001')
// '-1000000000000000001.000000000000000000000000000099'

subtract(
  '999999999999999999.999999999999999999999999999999', 
  '-1.000000000000000000000000000001')
// '1000000000000000001'
```

1. This problem covers [77. implement BigInt subtraction with sign](https://bigfrontend.dev/problem/implement-BigInt-subtraction-with-sign).
2. trailing zeroes in the result should be removed.

Related Problems

[126. BigDecimal addition ](https://bigfrontend.dev/problem/decimal-addition)

### 答案

与加法类似

```js
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
function subtract(num1, num2) {
  const a = toBig(num1), b = toBig(num2);
  const maxPow = Math.max(a.pow, b.pow);

  if (maxPow) {
    const bigA = normalize(a, maxPow), bigB = normalize(b, maxPow);
    let res = String(bigA - bigB);
    let sign = '';
    if (res[0] === '-') {
      sign = '-';
      res = res.substring(1);
    }
    if (res.length <= maxPow) {
      res = '0'.repeat(maxPow - res.length + 1) + res;
    }
    const pointPos = res.length - maxPow;
    res = res.substr(0, pointPos) + '.' + res.substr(pointPos);
    return sign + res.replace(/\.?0+$/, '');
  } else {
    return String(a.num - b.num);
  }
}

function toBig(n) {
  let pow = 0;
  n = n.replace(/\.(\d+?)0*$/, (_, f) => {
    pow = f.length;
    return f;
  })
  return { num: BigInt(n), pow };
}

function normalize(big, maxPow) {
  return big.num * (10n ** BigInt(maxPow - big.pow));
}
```



## [128. BigDecimal multiplication](https://bigfrontend.dev/problem/bigdecimal-multiplication)

### 题目

> This is a follow-up on [126. BigDecimal addition](https://bigfrontend.dev/problem/decimal-addition)

In this problem, you are asked to **implement the multiplication of two decimals with arbitrary digits**.

```js
multiply(
  '1123456787654323456789', 
  '1234567887654323456'
)
// '1386983673205309924427166592431045142784'

multiply(
  '-1123456787654323456789', 
  '1234567887654323456.12348'
)
// '-1386983673205309924565891036570601003228.30572'

multiply(
  '-0.12345', 
  '-1.6789012'
)
// '0.20726035314'
```

1. This problem covers [114. implement BigInt multiplication](https://bigfrontend.dev/problem/implement-BigInt-multiplication).
2. trailing zeroes in the result should be removed.
3. Big.js defaults return exponential notation when it is too big, in this problem, **don't do that**

### 答案

正则表达式+Bigint

```js
/**
 * @param {string} a 
 * @param {string} b
 * @return {string}
 */
function multiply(a, b) {
  let shift = 0;

  function removePoint(_, f) {
    shift += f.length;
    return f;
  }

  a = a.replace(/\.(\d+)$/, removePoint);
  b = b.replace(/\.(\d+)$/, removePoint);

  let bigA = BigInt(a), bigB = BigInt(b);
  let res = String(bigA * bigB);
  if (!shift) {
    return res;
  }

  let sign = ''
  if (res[0] === '-') {
    sign = '-';
    res = res.substring(1);
  }
  if (res.length <= shift) {
    res = '0'.repeat(shift - res.length + 1) + res;
  }
  res = res.substr(0, res.length - shift) + '.' + res.substr(-shift);
  return sign + res.replace(/\.?0+$/, '');
}
```



## [129. BigDecimal Division](https://bigfrontend.dev/problem/bigdecimal-division)

### 题目

> This is a follow-up on [126. BigDecimal addition](https://bigfrontend.dev/problem/decimal-addition)

In this problem, you are asked to **implement the division of two decimals with arbitrary digits**.

```js
divide(
  '100000000000000.1', 
  '-0.001'
)
// '-100000000000000100'

divide(
  '-0.123', 
  '-0.00971'
)
// '12.66735324407826982492'
```

1. This problem covers [115. implement BigInt division](https://bigfrontend.dev/problem/implement-BigInt-division).
2. trailing zeroes in the result should be removed.
3. return the result with **max 20 digit fraction part**, rest be truncated.

### 答案

与加减统一写法

```js
/**
 * @param {string} a
 * @param {string} b
 * @return {string}
 */
function divide(a, b) {
  const bigA = toBig(a), bigB = toBig(b);
  const pow = 20 + bigB.pow - bigA.pow;
  const multi = 10n ** BigInt(pow);
  let res = String(bigA.num * multi / bigB.num)

  let sign = '';
  if (res[0] == '-') {
    sign = '-';
    res = res.substring(1);
  }
  const resPow = pow - bigB.pow + bigA.pow;
  if (res.length <= resPow) {
    res = '0'.repeat(resPow - res.length + 1) + res;
  }
  res = res.slice(0, res.length - resPow) + '.' + res.slice(-resPow);

  return sign + res.replace(/\.?0+$/, '');
}

function toBig(n) {
  let pow = 0;
  let num = n.replace(/\.(\d{1,20})\d*$/, (_, f) => {
    pow = f.length;
    return f;
  });
  return { num: BigInt(num), pow };
}
```



## [130. create LazyMan()](https://bigfrontend.dev/problem/create-lazyman)

### 题目

`LazyMan` is very lazy, he only eats and sleeps.

`LazyMan(name: string, logFn: (log: string) => void)` would output a message, the passed `logFn` is used.

```js
LazyMan('Jack', console.log)
// Hi, I'm Jack.
```

He can `eat(food: string)`

```js
LazyMan('Jack', console.log).eat('banana').eat('apple')
// Hi, I'm Jack.
// Eat banana.
// Eat Apple.
```

He also `sleep(time: number)`, time is based on seconds.

```js
LazyMan('Jack', console.log).eat('banana').sleep(10).eat('apple').sleep(1)
// Hi, I'm Jack.
// Eat banana.
// (after 10 seconds)
// Wake up after 10 seconds.
// Eat Apple.
// (after 1 second)
// Wake up after 1 second.
```

He can `sleepFirst(time: number)`, which has the highest priority among all tasks, no matter what the order is.

```js
LazyMan('Jack', console.log).eat('banana').sleepFirst(10).eat('apple').sleep(1)
// (after 10 seconds)
// Wake up after 10 seconds.
// Hi, I'm Jack.
// Eat banana
// Eat apple
// (after 1 second)
// Wake up after 1 second.
```

Please create such `LazyMan()`

### 答案

使用队列保存各任务，结合sleep函数实现延时执行

```js
const sleep = (ms) => new Promise(resolve => setTimeout(resolve, ms));
// interface Laziness {
//   sleep: (time: number) => Laziness
//   sleepFirst: (time: number) => Laziness
//   eat: (food: string) => Laziness
// }

/**
 * @param {string} name
 * @param {(log: string) => void} logFn
 * @returns {Laziness}
 */
function LazyMan(name, logFn) {
  const cmds = [["greet", name]];

  const actions = {
    greet: name => logFn(`Hi, I'm ${name}.`),
    eat: food => logFn(`Eat ${food}.`),
    sleep: ms => sleep(ms * 1000).then(() => logFn(`Wake up after ${ms} second${ms > 1 ? 's' : ''}.`)),
  };

  async function exec() {
    for (const [cmd, val] of cmds) {
      await actions[cmd](val);
    }
  }

  Promise.resolve().then(exec);

  return {
    sleep(ms) {
      cmds.push(['sleep', ms]);
      return this;
    },
    sleepFirst(ms) {
      cmds.unshift(['sleep', ms]);
      return this;
    },
    eat(food) {
      cmds.push(['eat', food]);
      return this;
    },
  };
}
```



## [131. implement _.chunk()](https://bigfrontend.dev/problem/implement-lodash-chunk)

### 题目

[_.chunk()](https://lodash.com/docs/4.17.15#chunk) splits array into groups with the specific size.

Please implement your `chunk(arr: any[], size: number)`

```js
chunk([1,2,3,4,5], 1)
// [[1], [2], [3], [4], [5]]

chunk([1,2,3,4,5], 2)
// [[1, 2], [3, 4], [5]]

chunk([1,2,3,4,5], 3)
// [[1, 2, 3], [4, 5]]

chunk([1,2,3,4,5], 4)
// [[1, 2, 3, 4], [5]]

chunk([1,2,3,4,5], 5)
// [[1, 2, 3, 4, 5]]
```

for size smaller than 1, return an empty array.

### 答案

对原生数组依次切片

```js
/** 
 * @param {any[]} items
 * @param {number} size
 * @returns {any[][]}
 */
function chunk(items, size) {
  if (size === 0) return [];
  const res = [];
  for (let i = 0; i < items.length; i += size) {
    res.push(items.slice(i, i + size));
  }
  return res;
}
```



## [132. the angle between hour hand and minute hand of a clock](https://bigfrontend.dev/problem/the-angle-between-hour-hand-and-minute-hand-of-a-clock)

### 题目

Given a time string in format `HH:mm`, please return the **angle between hour hand and minute hand**.

You should return rounded integer representing the smaller angle in degrees.

```js
angle('12:00')
// 0

angle('23:30')
// 165
```

### 答案

分别计算分针和时针的角度，进行相减

```js
/**
 * @param {string} time
 * @returns {number} 
 */
function angle(time) {
  const [hour, minute] = time.split(":").map(Number);
  const minuteAngle = minute * 6;
  const hourAngle = (hour + minute / 60) % 12 * 30;
  const angleBetween = Math.abs(hourAngle - minuteAngle);
  return Math.round(Math.min(angleBetween, 360 - angleBetween));
}
```



## [133. roman numerals to integer](https://bigfrontend.dev/problem/roman-numerals-to-integer)

### 题目

[Roman numerals](https://en.wikipedia.org/wiki/Roman_numerals#Standard_form) are represented by combinations of following seven symbols, each with a fixed integer value.

| Symbol |  I   |  V   |  X   |  L   |  C   |  D   |  M   |
| :----: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| Value  |  1   |  5   |  10  |  50  | 100  | 500  | 1000 |

For [Standard form](https://en.wikipedia.org/wiki/Roman_numerals#Standard_form), subtractive notation is used, meaning 4 is `IV` rather than `IIII`, 9 is `IX` rather than `VIIII`. Same rule applies to 40(`XL`) and 900(`CM`) .etc.

Simply speaking, the roman numerals in standard form follow these rules.

1. symbols are listed from highest to lowest, from left to right
2. from left to right, if the next symbol value is bigger than current one, it means subtracting, otherwise adding.

Please implement `romanToInteger()`. The input are all valid strings.

```js
romanToInteger('CXXIII')
// 123

romanToInteger('MCMXCIX')
// 1999

romanToInteger('MMMCDXX')
// 3420
```

### 答案

从左往右，当前数大则相加，否则相减

```js
/**
 * @param {string} str - roman numeral string
 * @returns {number} integer
 */
function romanToInteger(str) {
  const numerals = new Map([
    ["I", 1],
    ["V", 5],
    ["X", 10],
    ["L", 50],
    ["C", 100],
    ["D", 500],
    ["M", 1000],
  ]);

  let value = 0;
  for (let i = 0; i < str.length; i++) {
    const cur = numerals.get(str[i]);
    const next = i + 1 < str.length ? numerals.get(str[i + 1]) : 0;

    if (cur < next) {
      value -= cur;
    } else {
      value += cur;
    }
  }

  return value;
}
```



## [134. create your own Cookie](https://bigfrontend.dev/problem/create-your-own-Cookie)

### 题目

We can get and set cookie by `document.cookie`.

```js
document.cookie = 'bfe=dev'
// "bfe=dev"

document.cookie = 'bfe1=dev1'
// "bfe1=dev1"

document.cookie
// "bfe=dev; bfe1=dev1"
```

Please create your own `myCookie`.

1. it should support get and set.

```js
document.myCookie = 'bfe=dev'
// "bfe=dev"

document.myCookie = 'bfe1=dev1'
// "bfe1=dev1"

document.myCookie
// "bfe=dev; bfe1=dev1"
```

2. there a few options to cookie, but in this problem, you only need to support `max-age` which means the cookie should be deleted after certain time(in seconds).

```js
document.myCookie = 'bfe=dev; max-age=1'
// "bfe=dev; max-age=1"

document.myCookie
// "bfe=dev"
```

after 1 second

```js
document.myCookie
// ""
```

3. in your code, please enable `myCookie` in `install()` and remove the logic in `uninstall()`, these are used in judging.

### 答案

劫持对象实现字符串变化

```js

// enable myCookie
function install() {
  // Map<string, {value: string, maxAge?: number, createdAt: number}>
  const store = new Map();
  // use getter and setter
  Object.defineProperty(document, "myCookie", {
    get() {
      const result = [];
      for (const [key, entry] of store.entries()) {
        if (entry.maxAge !== undefined) {
          if (Date.now() - entry.createdAt >= entry.maxAge) {
            // expire
            store.delete(key);
            continue;
          }
        }

        result.push(`${key}=${entry.value}`);
      }
      return result.join("; ");
    },

    set(valueStr) {
      const [keyValuePair, option] = valueStr.replace(/ /g, '').split(";");
      const [key, value] = keyValuePair.split("=");

      if (!key || !value) return;

      const entry = {
        value,
        createdAt: Date.now()
      }
      if (option) {
        const [optionKey, optionValue] = option.split("=");
        if (optionKey) {
          const maxAge = Number(optionValue);
          entry.maxAge = maxAge * 1000;
        }
      }

      store.set(key, entry);
    },

    configurable: true
  })
}

// disable myCookie
function uninstall() {
  delete document.myCookie;
}
```



## [135. localStorage with expiration](https://bigfrontend.dev/problem/localStorage-with-expiration)

### 题目

[localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) is a simple and handy client-side storage, but [you should avoid using it](https://www.youtube.com/watch?v=NNuTV-gjlZQ) because it is synchronous.

Also [Safari's ITP](https://webkit.org/blog/10218/full-third-party-cookie-blocking-and-more/) actually deletes client-side script-writable storage after 7 days of Safari use without interacting on your website, and localStorage is included.

Unlike Cookie, localStorage doesn't expire.

In this problem, **please create a localStorage wrapper with expiration support**

```js
myLocalStorage.setItem('bfe', 'dev', 1000)
myLocalStorage.getItem('bfe')
// 'dev'
```

after 1 second:

```js
myLocalStorage.getItem('bfe')
// null
```

**FYI**

localStorage is replaced with our own implementation to avoid security error. But the interface is the same, actually you don't need to care :)

Related Problems

[134. create your own Cookie ](https://bigfrontend.dev/problem/create-your-own-Cookie)

### 答案

重写几个方法

```js
window.myLocalStorage = {
  getItem(key) {
    return window.localStorage.getItem(key);
  },
  
  setItem(key, value, maxAge) {
    window.localStorage.setItem(key, value);
    if (maxAge === 0) {
      window.localStorage.removeItem(key);
    } else if (maxAge > 0) {
      setTimeout(() => {
        window.localStorage.removeItem(key);
      }, maxAge);
    }
  },
  
  removeItem(key) {
    window.localStorage.removeItem(key);
  },
  
  clear() {
    window.localStorage.clear();
  }
}
```



## [136. find median of two sorted array](https://bigfrontend.dev/problem/find-median-of-2-sorted-array)

### 题目

Given two sorted array of integers, please return the median.

```js
median([1,2],[3,4,5])
// 3
```

If there are even numbers, return the average.

```js
median([1,2],[3,4])
// 2.5
```

**follow-up**

What is the time & space cost of your approach? Could you do better?

### 答案

特别的二分查找

```js
/**
 * @param {number[]} arr1 - sorted integer array
 * @param {number[]} arr2 - sorted integer array
 * @returns {number}
 */
function median(arr1, arr2) {
  let a = arr1, b = arr2;
  const total = arr1.length + arr2.length;
  const mid = Math.floor(total / 2);

  if (a.length > b.length) {
    a = arr2;
    b = arr1;
  }

  let left = 0, right = a.length - 1;
  while (true) {
    let aMid = Math.floor(left + (right - left) / 2);
    let bMid = mid - aMid - 2;
    let aLeft = aMid >= 0 ? a[aMid] : -Infinity;
    let bLeft = bMid >= 0 ? b[bMid] : -Infinity;
    let aRight = aMid + 1 < a.length ? a[aMid + 1] : Infinity;
    let bRight = bMid + 1 < b.length ? b[bMid + 1] : Infinity;
    if (aLeft <= bRight && bLeft <= aRight) {
      if (total % 2 === 0) {
        return (Math.max(aLeft, bLeft) + Math.min(aRight, bRight)) / 2;
      } else {
        return Math.min(aRight, bRight);
      }
    } else if (aLeft > bRight) {
      right = aMid - 1;
    } else {
      left = aMid + 1;
    }
  }
}
```



## [137. binary tree vertical traversal](https://bigfrontend.dev/problem/binary-tree-vertical-traversal)

### 题目

Traverse a binary tree vertically from left to right, from top to bottom, the binary tree contains positive integers as node values.

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20221005225301.png)

For above binary tree, vertical traversal should return

```js
[6,4,2,7,1,9,10,3,8,5]
```

If two nodes are at the same position, their order should inherit from their parent node. For example, 9 and 10 are at the same position, since 7 is before 8, so 9 should be before 10.

Related Problems

[110. serialize and deserialize binary tree ](https://bigfrontend.dev/problem/serialize-and-deserialize-binary-tree)

### 答案

遍历时加入列参数

```js
// This is the class for the node
// you can use this directly as it is bundled with your code

// class Node {
//   value: number
//   left: null | Node
//   right: null | Node
//   constructor(val) {
//     this.value = val
//     this.left = null
//     this.right = null
//   }
// }

/**
 * @param {Node} root
 * @returns {number[]}
 */
function traverse(root) {
  let result = {};
  let queue = [[root, 0]];
  
  while (queue.length) {
    const size = queue.length;
    queue.sort((a,b) => (a[1] - b[1])); 

    for (i = 0; i < size; i++) {
      const [node, column] = queue.shift();

      if (result[column]) {
        result[column].push(node.value);
      } else {
        result[column] = [node.value];
      }

      if (node.left) {
        queue.push([node.left, column - 1]);
      }
      if (node.right) {
        queue.push([node.right, parseInt(column) + 1]);
      }
    }
  }

  return Object.keys(result)
    .sort((a, b) => (a - b))
    .reduce((res, key) => ([...res, ...result[key]]), []);
}
```



## [138. Intersection of two sorted arrays](https://bigfrontend.dev/problem/intersection-of-two0-sorted-Arrays)

### 题目

Given 2 sorted array of integers, find the elements that exist in both arrays.

```js
intersect(
  [1,2,2,3,4,4],
  [2,2,4,5,5,6,2000]
)
// [2,2,4]
```

1. The arrays might have duplicate numbers.
2. The order of returning result doesn't matter.
3. What is the time & space cost of your approach? Could you improve it?

### 答案

双指针

```js
/**
 * @param {number[]} arr1 - integers
 * @param {number[]} arr2 - integers
 * @returns {number[]}
 */
function intersect(arr1, arr2) {
  let index1 = 0, index2 = 0;
  const result = [];

  while (index1 < arr1.length && index2 < arr2.length) {
    if (arr1[index1] === arr2[index2]) {
      result.push(arr1[index1]);
      index1++;
      index2++;
    } else if (arr1[index1] < arr2[index2]) {
      index1++;
    } else {
      index2++;
    }
  }
  return result;
}
```



## [139. implement _.partial()](https://bigfrontend.dev/problem/implement-partial)

### 题目

[_.partial()](https://lodash.com/docs/4.17.15#partial) works like [Function.prototype.bind()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) but `this` is not bound.

please create your own `partial()`

```ts
const func = (...args) => args

const func123 = partial(func, 1,2,3)

func123(4)
// [1,2,3,4]
```

It should also support placeholder.

```ts
const _ = partial.placeholder
const func1_3 = partial(func, 1, _, 3)

func1_3(2,4)
// [1,2,3,4]
```

### 答案

merge args1 and args2 and add remaining of args2

```js
/**
 * @param {Function} func
 * @param {any[]} args
 * @returns {Function}
 */
function partial(func, ...args) {
  return function(...restArgs) {
    const copyArgs = args.map(
      (arg) => arg === partial.placeholder ? restArgs.shift() : arg
    );
    return func.call(this, ...copyArgs, ...restArgs);
  }
}

partial.placeholder = Symbol();
```



## 140. Virtual DOM III - Functional Component

### 题目

> This is a follow-up on [118. Virtual DOM II - createElement](https://bigfrontend.dev/problem/virtual-dom-II-createElement).

In problem 118, you are asked to implement `createElement()` and `render()` function which supports intrinsic HTML elements, like `<p/>`, `<div/>` etc.

In this problem, you are ask to support custom **Functional Component**.

[Functional Component](https://reactjs.org/docs/components-and-props.html#function-and-class-components) are functions that:

1. accept **single object argument** -`props`, which contains `children`, `className` and other properties.
2. returns an MyElement by calling `createElement()`.

Say we have a Functional Component - `Title`

```js
const h = createElement
const Title = ({children, ...res}) => h('h1', res, ...children)
```

Then we should be able to use it in `createElement` and `render()`, just the same way as an intrinsic element.

```js
h(Title, {}, 'This is a title')

h(Title, {className: 'class1'}, 'This is a title')
```

Please **modify your createElement() and render()** from [118. Virtual DOM II - createElement](https://bigfrontend.dev/problem/virtual-dom-II-createElement) if necessary, so that the example in problem 118 could be rewritten as below:

```js
const Link = 
  ({children, ...res}) => h('a', res, ...children)
const Name = 
  ({children, ...res}) => h('b', res, ...children)
const Button = 
  ({children, ...res}) => h('button', res, ...children)
const Paragraph = 
  ({children, ...res}) => h('p', res, ...children)
const Container =  
  ({children, ...res}) => h('div', res, ...children)

h(
  Container,
  {},
  h(Title, {}, ' this is '),
  h(
    Paragraph,
    { className: 'paragraph' },
    ' a ',
    h(Button, {}, ' button '),
    ' from ',
    h(
      Link, 
      { href: 'https://bfe.dev' }, 
      h(Name, {}, 'BFE'), 
      '.dev')
  )
)
```

Related Problems

[113. Virtual DOM I ](https://bigfrontend.dev/problem/Virtual-DOM-I)

[118. Virtual DOM II - createElement ](https://bigfrontend.dev/problem/virtual-dom-II-createElement)

[143. Virtual DOM IV - JSX 1 ](https://bigfrontend.dev/problem/virtual-dom-iv-jsx-1)

[150. Virtual DOM V - JSX 2](https://bigfrontend.dev/problem/virtual-dom-v-jsx-2)

### 答案

多叉树遍历渲染DOM

```js
/**
 * MyElement is the type your implementation supports
 *
 * type MyNode = MyElement | string
 * type FunctionComponent = (props: object) => MyElement
 */

/**
 * @param { string | FunctionComponent } type - valid HTML tag name or Function Component
 * @param { object } [props] - properties.
 * @param { ...MyNode} [children] - elements as rest arguments
 * @return { MyElement }
 */
function createElement(type, props, ...children) {
  if (typeof type === "function") {
    return type({...props, children});
  }

  return {
    type,
    props: {
      ...props,
      children,
    }
  }
}


/**
 * @param { MyElement }
 * @returns { HTMLElement } 
 */
function render(myElement) {
  if (typeof myElement === "string") {
    return document.createTextNode(myElement);
  }
  const {type, props: { children, ...attrs}} = myElement;
  const node = document.createElement(type);
  for (const [attr, value] of Object.entries(attrs)) {
    node[attr] = value;
  }
  const childrenArr = Array.isArray(children) ? children : [children];
  for (const child of childrenArr) {
    node.append(render(child));
  }
  return node;
}
```



## [141. implement btoa()](https://bigfrontend.dev/problem/implement-btoa)

### 题目

[btoa()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/btoa) accepts a binary string and returns a Base64-encoded ASCII string from it. Characters in a binary string are the ASCII character for each byte of the binary data.

Please read [Base64 wiki](https://en.wikipedia.org/wiki/Base64) and implement your own `btoa()`.

```js
myBtoa('BFE')
// 'QkZF'

myBtoa('BFE.dev')
// 'QkZFLmRldg=='
```

**note**

1. Please don't use `window.btoa()` in your code.
2. The binary string passed to your function are all valid ASCII characters, there will be another problem on the general Base64 encoding/decoding.

Related Problems

[160. implement atob()](https://bigfrontend.dev/problem/implement-atob)

### 答案

map操作

```js
const base64 = `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/`;

/**
 * @param {string} str - binary string
 * @returns {string}
 */
function myBtoa(str) {
  let op1 = "", op2 = "";

  for (const c of str) {
    op1 += c.charCodeAt().toString(2).padStart(8, 0);
  }

  for (let i = 0; i < op1.length; i += 6) {
    const num = op1.slice(i, i + 6).padEnd(6, 0);
    const ascii = parseInt(num, 2);
    op2 += base64[ascii];
  }

  // padding to make the length multiple of 4;
  const padding = op2.length % 4;

  return op2 + "=".repeat(padding);
}
```



## [142. lit-html 1 - tagged templates](https://bigfrontend.dev/problem/lit-html-1-tagged-templates)

### 题目

According to [lit-html homepage](https://lit-html.polymer-project.org/),

> lit-html lets you write HTML templates in JavaScript, then efficiently render and re-render those templates together with data to create and update DOM

[This video](https://www.youtube.com/watch?v=ruql541T7gc&feature=emb_title) explains it pretty well about how it works. Let's take a look at the example.

```js
import {html, render} from 'lit-html'
const helloTemplate = (name) => html`<div>Hello ${name}!</div>`

// This renders <div>Hello Steve!</div> to the document body
render(helloTemplate('Steve'), document.body)

// This updates to <div>Hello Kevin!</div>, but only updates the ${name} part
render(helloTemplate('Kevin'), document.body);
```

The magic happens in the second call of `render()` which only updates the necessary parts.

But there will be a series of problems on BFE.dev leading to that, here you are asked to :

**implement html() and render() to make above example work, without considering the rerender**, so html() could just return the raw HTML string.

The input data are all valid.

### 答案

模板引擎操作

```js
function html(strs, ...keys) {
  return strs.map((str, i) => `${str}${keys[i] || ""}`).join("");
}


// render the result from html() into the container
function render(result, container) {
  container.innerHTML = result;
}
```



## [143. Virtual DOM IV - JSX 1](https://bigfrontend.dev/problem/virtual-dom-iv-jsx-1)

### 题目

If you are using React, you must be familiar with [JSX](https://facebook.github.io/jsx/).

With JSX syntax support, transpilers are able to understand below non-standard code directly in JavaScript.

```js
<p> this is <button className="button">button</button> </p>
```

Then it is transpiled to standard JavaScript function calls.

```js
React.createElement("p", null,
  " this is ",
  React.createElement("button", { className: "button" }, "button"),
  " ");
```

> have a try at [TypeScript Playground](https://www.typescriptlang.org/play?#code/DwBwfABALgFglgZwoiwBGBXKUD2A7CAYwBsBDBBAOVIFsBTAXgCJNt8mxXc9gB6L-JD7ggA)

To illustrate how the transpilation works, let's start with a simple example.

```js
<a>bfe.dev</a>
```

First the parser will create an AST(Abstract Syntax Tree) from the code.

Open above code [in AST Explorer](https://astexplorer.net/#/gist/46044fc473a92974cd8f933efc7635f6/8a876a4240ecf38d64c0e0af3c693a1c54d80525), you can see the AST in the right pannel, roughly something like this:

```js
expression: JSXElement {
  openingElement: JSXOpeningElement {
    name: JSXIdentifier {
      name: "a"
    }
  }
  closingElement: JSXClosingElement {
    name: JSXIdentifier {
      name: "a"
    }
  }
  children: [
    JSXText {
      value: "bfe.dev"
    }
  ]
}
```

Obviously above AST follows the [JSX Spec](https://facebook.github.io/jsx/):

```js
JSXElement:
  JSXOpeningElement JSXChildren? JSXClosingElement

JSXOpeningElement:
  < JSXElementName JSXAttributes? >

JSXChildren:
  JSXChild JSXChildren?

JSXClosingElement:
  < / JSXElementName >

JSXChild:
  JSXText
  JSXElement
  { JSXChildExpression? }
```

With the above AST, it is fairly easy to generate code, we only need to traverse the AST and insert `React.createElement`:

```js
React.createElement("p", null,
  " this is ",
  React.createElement("button", { className: "button" }, "button"),
  " ");
```

Also instead of React method, we could use `h()` defined in [140. Virtual DOM III - Functional Component](https://bigfrontend.dev/problem/virtual-DOM-III-Functional-Component) instead.

```js
h("p", null,
  " this is ",
  h("button", { className: "button" }, "button"),
  " ");
```

**Now, please create your own parse() and generate() to transpile JSX Element code**.

1. please generate code which uses `h()`, `h()` is bundled with your code.
2. Goal of this problem is not to recreate the full parser, so only need to support the minumum spec below:

```js
JSXElement:
  JSXOpeningElement JSXChildren? JSXClosingElement

JSXOpeningElement:
  < JSXElementName >

JSXChildren:
  JSXChild

JSXClosingElement:
  < / JSXElementName >

JSXChild:
  JSXText
```

- you can choose not to follow the naming
- there is no newlines in the input, you can ignore [the whitespace rules](https://github.com/facebook/react/pull/480#issuecomment-31296039)
- all input tags are smallcase HTML tags

1. for simplicity, the AST creating process with `parse()` won't be tested, rather `parse()` and `generate()` are tested together like this:

```js
const result = eval(generate(parse('<a>bfe.dev</a>')))
expect(result).toEqual(h('a', null, 'bfe.dev'))
```

1. An error should be thrown if code is not valid JSXElement, for example, the JSXOpeningElement and JSXClosingElement might not be matched.

   > The test cases only cover some of the common errors.

Related Problems

[113. Virtual DOM I ](https://bigfrontend.dev/problem/Virtual-DOM-I)

[118. Virtual DOM II - createElement ](https://bigfrontend.dev/problem/virtual-dom-II-createElement)

[140. Virtual DOM III - Functional Component ](https://bigfrontend.dev/problem/virtual-DOM-III-Functional-Component)

[150. Virtual DOM V - JSX 2](https://bigfrontend.dev/problem/virtual-dom-v-jsx-2)

### 答案

Fully parsed (All Levels Down) AST with Children Parsed via Stack

```js
const types = {
  openingElement: "JSXOpeningElement",
  closingElement: "JSXClosingElement",
  element: "JSXElement",
  textElement: "JSXText",
};

/**
 * @param {code} string
 * @returns {any} AST 
 */

const parse = (code) => {
  const str = code.trim();
  if (!str.startsWith("<") || !str.endsWith(">")) {
    throw new Error("One root elment expected");
  }

  let stack = [];
  let cursor = 0;
  while (cursor < str.length) {
    if (str.charAt(cursor) === "<") {
      const [elem, next] = parseTag(str, cursor);

      if (elem.type === types.closingElement) {
        // Collect children
        let children = [];

        while (
          stack.length &&
          stack[stack.length - 1].type !== types.openingElement
        ) {
          children.unshift(stack.pop());
        }

        // Check tags matching
        const openingElement = stack.pop();

        if (elem.name !== openingElement.name) {
          throw new Error("Opening and closing tags must match");
        }

        // Create new Element
        stack.push({
          openingElement,
          children,
          closingElement: elem,
          type: types.element,
        });
      } else {
        stack.push(elem);
      }

      cursor = next;
    } else {
      const [textElement, next] = parseText(str, cursor);
      stack.push(textElement);
      cursor = next;
    }
  }

  if (stack.length !== 1 && stack[0].type !== types.element) {
    throw new Error("Single root element expected");
  }

  return stack[0];
};

const parseTag = (str, cursor) => {
  if (str.length < 2 || str.charAt(cursor) !== "<") {
    throw new Error("Not a valid tag");
  }

  let i = cursor + 1;
  let start = null;
  let end = null;

  let isClosing = false;
  while (i < str.length) {
    if (!start && str.charAt(i) === "/") {
      isClosing = true;
    }

    if (!start && str.charAt(i) !== " " && str.charAt(i) !== "/") {
      start = i;
    }

    if ((start && str.charAt(i) === " ") || str.charAt(i) === ">") {
      end = i;
      break;
    }

    i++;
  }

  if (start === null || end === null) {
    throw new Error("Error while parsing tag name");
  }

  let tag = {
    type: isClosing ? types.closingElement : types.openingElement,
    name: str.slice(start, end).trim(),
  };

  if (!isClosing && str.charAt(end) !== ">") {
    const [attributes, next] = parseAttributes(str, end);
    tag.attributes = attributes;
    end = next;
  }

  while (isClosing && end < str.length && str.charAt(end) !== ">") {
    end++;
  }

  return [tag, end + 1];
};

const parseText = (str, cursor) => {
  let i = cursor;
  while (i < str.length && str.charAt(i) !== "<") {
    // Don't agree with testcase invalid: "<a>></a>", this is valid JSX with ">" as a text child - try AST explorer
    if (str.charAt(i) === ">") {
      throw new Error("Unexpected Character");
    }
    i++;
  }

  return [
    {
      type: types.textElement,
      value: str.slice(cursor, i),
    },
    i,
  ];
};

const parseAttributes = (str, cursor) => {
  let i = cursor;
  let end = null;
  while (i < str.length) {
    if (str.charAt(i) === ">") {
      end = i;
      break;
    }

    i++;
  }

  if (end == null) {
    throw new Error("Error while parsing attributes");
  }

  let attributes = [];
  if (end != null) {
    const attrStr = str.slice(cursor, end).trim();

    attributes = attrStr.split(/\s/);
    attributes = attributes.map((attr) => attr.split("="));
  }

  return [attributes, end];
};

/**
 * @param {any} your AST
 * @returns {string} 
 */
function generate(ast) {
  const {openingElement, children} = ast;

  const childrenStr = children.reduce((acc, ch) => {
    if (ch.type === types.textElement) {
      return acc + ch.value;
    } else if (ch.type === types.element) {
      return acc + generate(ch);
    }

    return acc;
  }, "");


  if (childrenStr.length) {
    return h(openingElement.name, null, childrenStr);
  } else {
    return h(openingElement.name, null)
  }

}
```



## [144. serialize and deserialize data types not supported in JSON](https://bigfrontend.dev/problem/serialize-and-deserialize-data-types-not-supported-in-JSON)

### 题目

Obviously, [JSON.parse()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) and [JSON.stringify()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) are unable to handle data types that are not supported in JSON.

```ts
JSON.stringify({a:1n}) // Error
```

Also `undefined` is ignored in object properties or changed to `null`.

```ts
JSON.stringify([undefined]) // "[null]"
JSON.stringify({a: undefined }) // "{}"
NaN` and `Infinity` are also treated as `null
JSON.stringify([NaN, Infinity]) // "[null,null]"
JSON.stringify({a: NaN, b:Infinity}) // "{"a":null,"b":null}"
```

for more info, please refer to [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#description).

But sometimes we might want to be able to serialize these data types.

**Now please implement functions to serialize and deserialize following data types:**

1. primitives (symbol is exluded)
2. object literals
3. array

Object literals and arrays are consisting of primitives and might be nested

Code below is expected to work:

```ts
parse(stringify([1n, null, undefined, NaN])) // [1n, null, undefined, NaN]
parse(stringify({a: undefined, b: NaN}) // {a: undefined, b: NaN}
```

You can use JSON.stringify() and JSON.parse() in your code or write your own.

### 答案

无法处理的数据特殊处理

```ts
type SerializablePrimitives = undefined | null | number | string | bigint | boolean

type Serializable = {
  [index: string]: Serializable
} | Serializable[] | SerializablePrimitives

const stringify = (data: Serializable): string => {
  return JSON.stringify(data, function (_key, value) {
    if (typeof value === 'bigint') {
      return value.toString() + 'n';
    }
    if (value === undefined) {
      return "__undefined__";
    }
    
    if (Number.isNaN(value)) {
      return "__NaN__";
    }
    if (value === Infinity) {
      return "__Infinity__";
    }
    if (value === -Infinity) {
      return "__-Infinity__";
    }
    return value;
  })
}

const parse = (data: string): Serializable => {
  let obj = JSON.parse(data, function(_key, value) {
    
    if (typeof value == 'string') {
      // check if its a number that ends with n
      if (/^[\d]+n+$/.test(value)) {
        return BigInt(parseInt(value.slice(0, -1)));
      }
      if (value ===  "__NaN__") {
        return NaN;
      }
      if (value === "__Infinity__") {
        return Infinity;
      }
      if (value === "__-Infinity__") {
        return -Infinity;
      }
      
    }
    return value;
  })
  // handle undefined as reviver function doesnt allow us to return undefined
  return changeToUndefined(obj);
}

function changeToUndefined(obj: any){
  if (obj) {
    for (let k in obj) {
      if (obj[k]==='__undefined__') {
        obj[k] = undefined;
      }
      if (obj.hasOwnProperty(k) && typeof obj[k] === 'object'){
        changeToUndefined(obj[k]);
      }
    }
  }
  if (obj === '__undefined__') {
    return undefined;
  }
  return obj;
}
```



## [145. most frequently occurring character](https://bigfrontend.dev/problem/most-frequently-occurring-character)

### 题目

Given a non-empty string, return the most frequently ocurring character.

If there are multiple characters with same occurrance, return an array of them.

```ts
count('abbccc')
// 'c'

count('abbcccddd')
// ['c', 'd']
```

Follow-up: What is the time & space complexity of your approach?

### 答案

map统计各字母出现顺序

```ts
function count(str: string): string | string[] {
  const map = new Map();
  const result = [];

  for(const char of str) {
    map.set(char, (map.get(char) || 0) + 1);
  }

  const max = Math.max(...map.values());

  for(const [key, value] of map) {
    if(value === max) {
      result.push(key);
    }
  }

  return result.length === 1 ? result[0] : result;
}
```



## [146. implement Array.prototype.reduce()](https://bigfrontend.dev/problem/implement-Array-prototype-reduce)

### 题目

[Array.prototype.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) is a handy method to process arrays.

Here is a simple task - **Could you implement it by yourself?**

```ts
[1,2,3].myReduce((sum, item) => sum + item)
// 6
```

1. do not use native Array.prototype.reduce() in your code
2. your function is only tested against valid array (no array-like objects)
3. thanks to [pajadev](https://bigfrontend.dev/category/5/discuss/693?focus=859) for suggesting this

### 答案

注意初始值

```ts
// copied from lib.es5.d.ts
declare interface Array<T> {
  myReduce(callbackfn: (previousValue: T, currentValue: T, currentIndex: number, array: T[]) => T): T;
  myReduce(callbackfn: (previousValue: T, currentValue: T, currentIndex: number, array: T[]) => T, initialValue: T): T;
  myReduce<U>(callbackfn: (previousValue: U, currentValue: T, currentIndex: number, array: T[]) => U, initialValue: U): U
}

Array.prototype.myReduce = function (callback: Function, ...rest: any[]) {
  let accumulator = rest[0];
  let index = 0;
  if (rest.length === 0) {
    if (this.length === 0) {
      throw new Error('Reduce of empty array with no initial value');
    }
    accumulator = this[0];
    index++;
  }
  while (index < this.length) {
    accumulator = callback(accumulator, this[index], index, this);
    index++;
  }
  return accumulator;
}
```



## [147. Pick up stones](https://bigfrontend.dev/problem/pickup-the-stones)

### 题目

There is a pile of `n` (n > 0) stones.

Player A and Player B take turns to pick 1 or 2 stones from the pile. A starts first.

Who picks the last stone loses the game.

Now here is the question, **is there a winning strategy for A or B ?** If so, returns the player name. If there is none, return null.

```ts
winningStonePicking(1)
// 'B'

winningStonePicking(2)
// 'A'

winningStonePicking(3)
// 'A'

winningStonePicking(4)
// 'B'
```

### 答案

找规律，可以通过递归分析

```ts
function canWinStonePicking(n: number): 'A' | 'B' | null {
  return n % 3 === 1 ? 'B' : 'A';
}
```



## [148. create a counter object](https://bigfrontend.dev/problem/create-a-counter-object)

### 题目

Create an object with property `count`, which increments every time `count` is accessed, initial value is 0.

```js
const counter = createCounter()
counter.count // 0, then it should increment
counter.count // 1
counter.count // 2
counter.count = 100 // it cannot be altered
counter.count // 3
```

### 答案

使用proxy劫持set

```ts
function createCounter(): {count: number } {
  return new Proxy({count: 0}, {
    get: (obj, prop: "count") => obj[prop]++,
    set: () => false
  })
}
```



## [149. interpolation](https://bigfrontend.dev/problem/interpolation)

### 题目

Have you ever added i18n support to your projects?

Take [i18next](https://www.i18next.com/) as an example, generally the keys and translations are kept separately, like this JSON below.

```json
{
  "evaluation": "BFE.dev is {{evaluation}}"
}
```

At places where this key is used, we can then interpolate the string by passing a data object.

```ts
t('evaluation', {evaluation: 'fantastic'});
// "BFE.dev is fantastic"
```

Now, **please create a similar `t()` function which accepts the translation directly**.

#### 1. it supports `{{` and `}}` as delimiters

Let's make it clearer and simpler, when a new pair `{{` is met, characters until the following `}}` are treated as the property name.

For all the other cases, they should not be treated as delimiters.

```ts
t('BFE.dev is {{{evaluation}', {evaluation: 'fantastic'});
// "BFE.dev is {{{evaluation}"

t('BFE.dev is {{{evaluation}}}', {'{evaluation': 'fantastic'});
// "BFE.dev is fantastic}"

t('BFE.dev is {{evalu ation}}', {'evalu ation': 'fantastic'});
// "BFE.dev is fantastic"
```

#### 2. if no data is passed or no property exists, just leave it empty

```ts
t('BFE.dev is {{evaluation}}');
// "BFE.dev is "
```

### 答案

字符串操作

```ts
/**
 * @param {string} translation
 * @param {any} data
 * @returns {string}
 */
function t(translation: string, data?: any): string {
  // linear scanning, if we haven't encountered a "{{", keep adding to new string
  // if we encountered a "{{", don't add until we see a corresponding "}}"
  // if we a see a corresponding "}}", we add the mapped value or empty string

  let leftIndex = -1;
  let output = "";

  for (let i = 0; i < translation.length; i++) {
    const left = translation.substring(i, i+2);

    if (left !== "{{" && leftIndex === -1) {
      output += translation[i];
      continue;
    }

    if (leftIndex === -1 && left === "{{") {
      leftIndex = i;
      continue;
    }

    if (leftIndex !== -1 && left === "}}") {
      const key = translation.substring(leftIndex+2, i);

      if (data instanceof Object && key in data) {
        output += data[key];
      }
      leftIndex = -1;
      i+=1;
    }
  }

  // if never found a corresponding "}}" and we had "{{"
  if (leftIndex !== -1) {
    output += translation.substring(leftIndex, translation.length);
  }

  return output;
}
```

正则表达式

```ts
/**
 * @param {string} translation
 * @param {any} data
 * @returns {string}
 */
function t(translation: string, data?: any): string {
  return translation.replace(/{{(.*?)}}/g, (_, key) => data && data[key] || '');
}
```

