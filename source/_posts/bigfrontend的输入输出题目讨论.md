---
title:  bigfrontend的输入输出题目讨论（缓慢更新中）
date: 2021-06-17 21:07:33
categories: 
- web前端
tags:
- bigfrontend
- 前端
- 题库
---

国外前端会考察哪些问题？js函数、系统设计、TS类型体操的用法，大大拓宽了我的眼界，去思考和实战更加相关的问题吧，输入输出考验了对js语法的掌握程度，值得多次学习。
<!-- more -->

# 输入输出

综合考察对js的熟悉程度

## [1. Promise order](https://bigfrontend.dev/quiz/1-promise-order)

### 题目

What does the code snippet to the right output by `console.log`?

```js
console.log(1)
const promise = new Promise((resolve) => {
  console.log(2)
  resolve()
  console.log(3)
})

console.log(4)

promise.then(() => {
  console.log(5)
}).then(() => {
  console.log(6)
})

console.log(7)

setTimeout(() => {
  console.log(8)
}, 10)

setTimeout(() => {
  console.log(9)
}, 0)
```

### 答案

实践循环宏任务微任务

```sh
1
2
3
4
7
5
6
9
8
```

### 详细解析

#### Part 1 - Sync Code

```js
console.log(1) // sync JS

// promise run constructor function
// because here resolve is NOT async, then still in sequence to run
const promise = new Promise((resolve) => {
  console.log(2) 
  resolve() // only mark the promise's internal state to be "Fulfilled"
  console.log(3) // still run to the end of this callback function
})

console.log(4) // continue to run sequentially
```

Until now the result output is **Sequential & Sync**:

```js
1
2
3
4
```

#### Part 2 - Async & Sync

```js
// Async: Job Queue for promise
promise.then(() => {
  console.log(5) 
}).then(() => {
  console.log(6) 
})

// Sync: should be run immediately compared to ASYNC
console.log(7) 

// Async: event loop & callback queue for Web API
setTimeout(() => {
  console.log(8) 
}, 10)

// Async: event loop & callback queue for Web API
setTimeout(() => {
  console.log(9) 
}, 0) // even though time is 0, still an ASYNC Call
```

For the 2nd part, it has both **Sync & Async**, then the priority and order becomes:

- 1st: Sync code
- 2nd: Async for Job Queue -> process promise
- 3rd: Async for Callback Queue -> process Web API call

Output for second part should be:

```js
7 // sync code
5 // job queue
6 // job queue
9 // callback queue 0s
8 // callback queue 10s
```



## [2. Promise executor](https://bigfrontend.dev/quiz/2-promise-executor)

### 题目

What does the code snippet to the right output by `console.log`?

```js
new Promise((resolve, reject) => {
  resolve(1)
  resolve(2)
  reject('error')
}).then((value) => {
  console.log(value)
}, (error) => {
  console.log('error')
})
```

### 答案

Promise链式调用返回的值和值的穿透

```sh
1
```



## [3. Promise then callbacks](https://bigfrontend.dev/quiz/3-promise-then-callbacks)

### 题目

What does the code snippet to the right output by `console.log`?

```js
Promise.resolve(1)
.then(() => 2)
.then(3)
.then((value) => value * 3)
.then(Promise.resolve(4))
.then(console.log)
```

### 答案

Promise链式调用返回的值和值的穿透

```sh
6
```

### 详细解析

```js
> Promise.resolve(1)
  .then(console.log)
// > 1
```

Is the same as

```js
> new Promise((resolve) => resolve(1))
  .then((val) => console.log(val))
// > 1
```

Therefore,

```js
Promise.then(() => 2)
  .then(console.log)
// > 2
```

But don't forget that if you don't use the new value you are chaining, it won't be used, e.g.

```js
Promise.resolve(1)
  .then(() => 2)
  .then(console.log)
// > 2 # as 1 isn't passed in the first 'then', e.g. .then(num => num + 2)
```

And you can't have numbers in .then calls, they need to be functions, ts throws this error, therefore that goes onto the reject pile and we skip over it

```js
Promise.resolve(1)
  .then(3) // Argument of type '3' is not assignable to parameter of type '(value: number) => number | PromiseLike<number>'.ts(2345)
Promise.resolve(1).then(Promise.resolve(4)).then(console.log)
// > 1
then(Promise.resolve(4))
```

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then

> returns another pending promise object, the resolution/rejection of the promise returned by then will be subsequent to the resolution/rejection of the promise returned by the handler. Also, the resolved value of the promise returned by then will be the same as the resolved value of the promise returned by the handler.

```js
Promise.resolve(1) // 1
.then(() => 2) // 2 (as 1 isn't used)
.then(3) // skip
.then((value) => value * 3) // 2 * 3 == 6
.then(Promise.resolve(4)) // creates a Pending promise
.then(console.log) // funnels 6 into console.log
```



## [4. Promise then callbacks II](https://bigfrontend.dev/quiz/4-Promise-then-callbacks-II)

### 题目

What does the code snippet to the right output by `console.log`?

```js
Promise.resolve(1)
.then((val) => {
  console.log(val)
  return val + 1
}).then((val) => {
  console.log(val)
}).then((val) => {
  console.log(val)
  return Promise.resolve(3)
    .then((val) => {
      console.log(val)
    })
}).then((val) => {
  console.log(val)
  return Promise.reject(4)
}).catch((val) => {
  console.log(val)
}).finally((val) => {
  console.log(val)
  return 10
}).then((val) => {
  console.log(val)
})
```

### 答案

Promise链式调用返回的值和值的穿透

```sh
1
2
undefined
3
undefined
4
undefined
undefined
```

### 详细解析

I was told that in other classical languages eg: C++, or java, the template order of these 3 keyword is impressive in my memory:

```js
try{}
catch{}
finally{}
```

But let's **think in JavaScript** .

#### 1. finally() never receive an argument

docs: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/finally

#### 2. Normal return value in finally won't make effect on promise object

#### 3. throw Error in finally()

Note: A throw (or returning a rejected promise) in the finally callback will reject the new promise with the rejection reason specified when calling throw.

```js
Promise.reject(1)
.finally(() => {                                                                                                                 
    throw new  Error(2); 
});
// or 
Promise.reject(1)
.finally(() => {                                                                                                                          
    return Promise.reject(2); 
});
```

The return promise object rejected value will be affected with **2**.

#### 4. Order of then() & catch()

Remember `then()` & `catch()` can be called to handle the promise at any time and at any order. It will use the latest final state of the promise object, and affects the new value of the promise object.

```js
Promise.reject(1)
.catch((val)=>{
    console.log(val); // 1 : rejected value is 1 
    // return nothing
    // will return undefined for promise object
})
.then((val)=>{
    console.log(val); // undefine: current promise object is already handled by "catch()"
})
```

#### 5. Full solution of original problem

```js
Promise.resolve(1)
.then((val) => {
  console.log(val) // resolve with value 1
  return val + 1  //  return 2  
}).then((val) => {
  console.log(val) // 2
  // return undefined
}).then((val) => {
  console.log(val)  // undefined   
  return Promise.resolve(3)
    .then((val) => {
      console.log(val) // 3
      // return undefined
    })
}).then((val) => {
  console.log(val)  // undefined 
  return Promise.reject(4)  // return 4    
}).catch((val) => {
  console.log(val)  // 4
  // return undefined
}).finally((val) => {
  console.log(val)  // undefined: finally has no arguments
  return 10   // no effect on promise object
}).then((val) => {
  console.log(val)  // undefined: because last 'catch()' handled the promise object with 'undefined'
})
```

#### Final Output:

```js
1
2
undefined
3
undefined
4
undefined
undefined
```



## [5. block scope](https://bigfrontend.dev/quiz/block-scope-1)

### 题目

What does the code snippet to the right output by `console.log`?

```js
for (var i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 0)
}

for (let i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 0)
}
```

### 答案

考的是对于声明变量和作用域的理解

```sh
5
5
5
5
5
0
1
2
3
4
```

### 详细解析

Using let means program work as you expect. e.g. this below snippet prints

0 1 2 3 4

```js
for (let i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 0)
}
```

However, with

```js
for (var i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 0)
}
```

If we use var, then var gets hoisted outside of the block scope into the outer function scope, as var makes it function scoped instead of block scoped.

And, if we have any closures created in the loop, let variables will be bound to the value from only that iteration of the loop, whereas var variables will be the current value of the variable, which at that point of the settimeout it is 5, hence it prints.

5 5 5 5 5



## [6. Arrow Function](https://bigfrontend.dev/quiz/6-Arrow-Function)

### 题目

What does the code snippet to the right output by `console.log`?

```js
const obj = {
  dev: 'bfe',
  a: function() {
    return this.dev
  },
  b() {
    return this.dev
  },
  c: () => {
    return this.dev
  },
  d: function() {
    return (() => {
      return this.dev
    })()
  },
  e: function() {
    return this.b()
  },
  f: function() {
    return this.b
  },
  g: function() {
    return this.c()
  },
  h: function() {
    return this.c
  },
  i: function() {
    return () => {
      return this.dev
    }
  }
}

console.log(obj.a())
console.log(obj.b())
console.log(obj.c())
console.log(obj.d())
console.log(obj.e())
console.log(obj.f()())
console.log(obj.g())
console.log(obj.h()())
console.log(obj.i()())
```

### 答案

考察this指向，主要是箭头函数和字面量函数

```sh
"bfe"
"bfe"
undefined
"bfe"
"bfe"
undefined
undefined
undefined
"bfe"
```

### 详细解析

Equivalent

```js
const obj = {
  dev: 'bfe',
  a: function() {
    return this.dev
  },
  b() {
    return this.dev
  },
}

console.log(obj.a()) // bfe
console.log(obj.b()) // bfe
```

Arrow function 'this'

```js
const obj = {
  dev: 'bfe',
  c: () => {
    // When we use arrow functions as object properties
    // it creates a new context, so console.log(this) is
    // everything in this arrow function, dev isn't there
    return this.dev
  },
}
console.log(obj.c()) // undefined
```

IFFE and arrow functions

```js
const obj = {
  dev: 'bfe',
  d: function() {
    // although this is an arrow function
    // it's an IIFE, so when parsed it turns into this
    // return this.dev
    return (() => {
      return this.dev
    })()
  },
  e: function() {
    // b or e don't have arrow functions
    // so they can still access dev in the
    // context of this
    return this.b()
  },
}
console.log(obj.d()) // bfe
console.log(obj.e()) // bfe
const obj = {
  dev: 'bfe',

  b() {
    return this.dev
  },

  f: function() {
    // When we pass a reference to the b function
    // we loose the this context as the callee is outside the obj
    return this.b
  },
  g: function() {
    // we already know c is trapped
    // in an arrow function context
    // so it's always gonna be undefined
    return this.c()
  },
  h: function() {
    // we already know c is trapped
    // in an arrow function context
    // so it's always gonna be undefined
    // even when using obj.h()()
    return this.c
  },
}


console.log(obj.f()()) // undefined
console.log(obj.g()) // undefined
console.log(obj.h()()) // undefined
```

Finally nested arrow functions

```js
const obj = {
  dev: 'bfe',
  i: function() {
    return () => {
      return this.dev // as this was created inside the function
      // it assumes the objects this 
    }
  }
}

console.log(obj.i()()) // bfe
```



## [7. Increment Operator](https://bigfrontend.dev/quiz/Increment-Operator)

### 题目

What does the code snippet to the right output by `console.log`?

```js
let a = 1
const b = ++a
const c = a++
console.log(a)
console.log(b)
console.log(c)
```

### 答案

考察++的执行顺序，输出

```sh
3
2
2
```

### 详细解析

```js
let a = 1;
const b = ++a; // it will increment and return the value *after* incrementing

// a's value will also get updated (allowed because of the 'let')
const c = a++; // this will increment as well, but it will return the value *before* 
 incrementing.
// a's value will also get updated

console.log(a); // 3
console.log(b); // 2
console.log(c); // 2
```



## [8. Implicit Coercion I](https://bigfrontend.dev/quiz/Implicit-Conversion-1)

### 题目

What does the code snippet to the right output by `console.log`?

```js
console.log(Boolean('false'))
console.log(Boolean(false))
console.log('3' + 1)
console.log('3' - 1)
console.log('3' - ' 02 ')
console.log('3' * ' 02 ')
console.log(Number('1'))
console.log(Number('number'))
console.log(Number(null))
console.log(Number(false))
```

### 答案

类型转换和隐式转换

```sh
true
false
"31"
2
1
6
1
NaN
0
0
```

### 详细解析

```js
console.log(Boolean('false')) // ONLY empty string will be false
console.log(Boolean(false)) // if it's already boolean type, NO conversion
console.log('3' + 1) // either of part is string, whole expression will be string concatenation 
console.log('3' - 1) // subtraction operator ONLY trigger ToNumber() conversion, no string
// same as (3-1) = 2
console.log('3' - ' 02 ') // String to Number will trim all white spaces and starting 0
// same as (3 - 2) = 1
console.log('3' * ' 02 ') // multiplicative operator ONLY trigger ToNumber() conversion
// same as (3 * 2) = 6
console.log(Number('1')) // String to Number, here is the valid number 1
console.log(Number('number')) // String to Number, here NOT Valid, return NaN
console.log(Number(null)) // by ECMA spec, it's 0
console.log(Number(false)) // by ECMA spec, it's 0
```

Final solution:

```text
true
false
"31"
2
1
6
1
NaN
0
0
```



## [9. null and undefined](https://bigfrontend.dev/quiz/null-and-undefined)

### 题目

What does the code snippet to the right output by `console.log`?

```js
console.log(JSON.stringify([1,2,null,3]))
console.log(JSON.stringify([1,2,undefined,3]))
console.log(null === undefined)
console.log(null == undefined)
console.log(null == 0)
console.log(null < 0)
console.log(null > 0)
console.log(null <= 0)
console.log(null >= 0)
console.log(undefined == 0)
console.log(undefined < 0)
console.log(undefined > 0)
console.log(undefined <= 0)
console.log(undefined >= 0)
```

### 答案

一定要注意`null == 0`是错误的，只和null或undefined相等，undefined转为数会变为`NaN`

```sh
"[1,2,null,3]"
"[1,2,null,3]"
false
true
false
false
false
true
true
false
false
false
false
false
```

### 详细解析

```js
console.log(JSON.stringify([1,2,null,3]))  // happy path: "[1,2,null,3]"
console.log(JSON.stringify([1,2,undefined,3]))  // JSON doesn't have undefined value, it's replaced with null in JSON data type.
console.log(null === undefined) // null -> 0 and undefined -> NaN, then NOT strictly equal
console.log(null == undefined) // Special rule: true -> Just Remember it
console.log(null == 0) // Special rule: null is not converted to 0 here
console.log(null < 0) // false: null -> 0
console.log(null > 0) // false: null -> 0
console.log(null <= 0) // true: null -> 0 
console.log(null >= 0) // true: null -> 0
console.log(undefined == 0)  // false: undefined -> NaN
console.log(undefined < 0)  // false: undefined -> NaN
console.log(undefined > 0)  // false: undefined -> NaN
console.log(undefined <= 0)  // false: undefined -> NaN
console.log(undefined >= 0)  // false: undefined -> NaN
```

**Notes:**

1. When converting to Number, null and undefined are handled differently: null becomes 0, whereas undefined becomes NaN.
2. When applying `==` to `null` or `undefined`, numeric conversion does not happen. null equals only to null or undefined, and does not equal to anything else.

```js
null == 0  // false, null is not converted to 0
null == null  // true
undefined == undefined  // true
null == undefined  // true
```

**Final Solution:**

```js
"[1,2,null,3]"
"[1,2,null,3]"
false
true
false
false
false
true
true
false
false
false
false
false
```



## [10. Equal](https://bigfrontend.dev/quiz/Equal-1)

### 题目

```js
console.log(0 == false)
console.log('' == false)
console.log([] == false)
console.log(undefined == false)
console.log(null == false)
console.log('1' == true)
console.log(1n == true)
console.log(' 1     ' == true)
```

### 答案

==一般会转为数类型进行对比

```sh
true
true
true
false
false
true
true
true
```

### 详细解析

I read the [ECMA2020-$7.2.14](https://tc39.es/ecma262/#sec-abstract-equality-comparison) here to finish this quiz

Rules summary:

- Number & String: convert to Number in priority
- Boolean & other: convert boolean to Number, then compare

-String & other Object: convert Object ToPrimitive(), then compare

- undefined, null is special, just remember rules

```js
console.log(0 == false)  // ToNumber(false) = 0 -> 0 == 0 -> true
console.log('' == false)  // "" == 0 -> ToNumber("") == 0 -> true
console.log([] == false)  // [] == 0 -> ToNumber([]) == 0 -> 0 == 0 -> true
console.log(undefined == false) // false
console.log(null == false)  // null is not converted to 0 here, false
console.log('1' == true)  // "1" == 1 -> ToNumber("1") == 1 -> 1 == 1 -> true
console.log(1n == true)  // 1n == 1 -> ToNumber(1n) == 1 -> 1== 1 -> true
console.log(' 1     ' == true)  // "1    " == 1 -> ToNumber("1      ")  == 1 -> true
```

Helpful Notes:

- `ToNumber()` you can explicitly try to call with `Number(.....)` or use Unary Plus Sign expression: `+[], +1n ......`

- String to Number: it first trims the white space and starting zeros, eg:

  ```
  "01      "  ->   "1"   ->   1
  ```

Final solution:

```js
true
true
true
false
false
true
true
true
```



## [11. Implicit Coercion II](https://bigfrontend.dev/quiz/Implicit-Conversion-II)

### 题目

What does the code snippet to the right output by `console.log`?

```js
console.log([] + [])
console.log([] + 1)
console.log([[]] + 1)
console.log([[1]] + 1)
console.log([[[[2]]]] + 1)
console.log([] - 1)
console.log([[]] - 1)
console.log([[1]] - 1)
console.log([[[[2]]]] - 1)
console.log([] + {})
console.log({} + {})
console.log({} - {})
```

### 答案

+转化为字符串，-转化为数

```sh
""
"1"
"1"
"11"
"21"
-1
-1
0
1
"[object Object]"
"[object Object][object Object]"
NaN
```

### 详细解析

Hey, this puzzle really need to fully and clearly understand some weird and bad parts of Javascript Type coercion for Objects!

#### I. How object converts to different types?

**Strongly Suggest Read this article before start this quiz.** [What is {} + [\] in JavaScript?](https://2ality.com/2012/01/object-plus-object.html)

#### II. A Call Out: one of the answer maybe WRONG here!

`{} + {}` - **Firefox & Chrome** handle it differently !!!

Answer is different in two browsers:

```js
// firefox
{} + {}; // NaN 
// chrome
{} + {}; // "[object Object][object Object]"
```

#### III. Explain parts of the problem

I parse all the expression as:

```js
Expression:  value1 +/- value2
prim1 := ToPrimitive(value1)
prim2 := ToPrimitive(value2)
```

**Case1: addition + operator**

```
console.log([[]] + 1)
[[]] + 1
prim1: ToPrimitive([[]]) = ""
prim2: ToPrimitive(1) = 1
one of them is string type, then do string concatenation
= "" + 1
= "1"
```

**Case2: subtraction - operator**

Subtract Op (-) ONLY triggers **ToNumber()** conversion, NO string operation.

```
console.log([[]] - 1)
[[]] - 1
prim1: ToPrimitive([[]]) = ""
prim2: ToPrimitive(1) = 1
Subtract Op (-) makes two parts both convert to Number
= ToNumber("") + ToNumber(1)
= 0 + 1
= 1
```

**Case3: object conversion**

```
console.log([] + {})
[] + {}
prim1: ToPrimitive([]) = ""
prim2: ToPrimitive({}) = "[object Object]"
Both of them are string, then do string concatenation
= ToString("") + ToString("[object Object]")
= "" + "[object Object]"
= "[object Object]"
```

#### IV. Final Output

```js
""
"1"
"1"
"11"
"21"
-1
-1
0
1
"[object Object]"
"[object Object][object Object]"
NaN
```



## [12. arguments](https://bigfrontend.dev/quiz/arguments)

### 题目

What does the code snippet to the right output by `console.log`?

```js
function log(a,b,c,d) {
  console.log(a,b,c,d)
  arguments[0] = 'bfe'
  arguments[3] = 'dev'
 
  console.log(a,b,c,d)
}

log(1,2,3)
```

### 答案

要注意d没有被重新赋值，只有arguments[3]被重新赋值

```sh
1,2,3,undefined
"bfe",2,3,undefined
```

### 详细解析

[arguments](https://bigfrontend.dev/quiz/arguments/discuss) is the actual value passed when function is invoked.

```js
log(1,2,3)
```

Here 1,2 and 3 are arguments.

[parameters](https://developer.mozilla.org/en-US/docs/Glossary/Parameter) are local variables in the function.

```js
function log(a,b,c,d){}
```

Here a,b,c, and d are parameters.

On execution of

```js
arguments[0] = 'bfe'
```

the first argument i.e 1 is replaced with 'bfe'.

However, when

```js
arguments[3] ='dev'
```

is executed it results in

```js
{
    "0": "bfe",
    "1": 2,
    "2": 3,
    "3": "dev"
}
```

so while accessing d parameter, we get undefined since no only 1,2, and 3 are initially passed as arguments and not d.



## [13. Operator precedence](https://bigfrontend.dev/quiz/operator-precedence)

### 题目

What does the code snippet to the right output by `console.log`?

```js
console.log(0 == 1 == 2)
console.log(2 == 1 == 0)
console.log(0 < 1 < 2)
console.log(1 < 2 < 3)
console.log(2 > 1 > 0)
console.log(3 > 2 > 1)
```

### 答案

从左到右判断，bool要转成number

```sh
false
true
true
true
true
false
```

### 详细解析

When we use any comparison operator like `==`, `<` and `>`, if one of the operands is boolean and another is a number it'll convert the boolean into a number and then compare i.e. `false` becomes `0` and `true` becomes `1`

Also, these operators work from left to right [See this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)

```js
console.log(0 == 1 == 2) // false == 2 👉🏻 0 == 2 👉🏻 false
console.log(2 == 1 == 0) // false == 0 👉🏻 0 == 0 👉🏻 true
console.log(0 < 1 < 2) // true < 2 👉🏻 1 < 2 👉🏻 true
console.log(1 < 2 < 3) // true < 3 👉🏻 1 < 3 👉🏻 true
console.log(2 > 1 > 0) // true > 0 👉🏻 1 > 0 👉🏻 true
console.log(3 > 2 > 1) // true > 1 👉🏻 1 > 1 👉🏻 false
```



## [14. Addition vs Unary Plus](https://bigfrontend.dev/quiz/Addition-vs-Unary-Plus)

### 题目

What does the code snippet to the right output by `console.log`?

There is a difference between [Addition Operator(+)](https://tc39.es/ecma262/#sec-addition-operator-plus) and [Unary plus operator(+)](https://tc39.es/ecma262/#sec-unary-plus-operator), even though they use the same '+'.

```js
console.log(1 + 2)
console.log(1 + + 2)
console.log(1 + + + 2)
console.log(1 + '2')
console.log(1 + + '2')
console.log('1' + 2)
console.log('1' + + 2)
console.log(1 + true)
console.log(1 + + true)
console.log('1' + true)
console.log('1' + + true)
console.log(1 + null)
console.log(1 + + null)
console.log('1' + null)
console.log('1' + + null)
console.log(1 + undefined)
console.log(1 + + undefined)
console.log('1' + undefined)
console.log('1' + + undefined)
console.log('1' + + + undefined)
```

### 答案

三个加号会无视掉中间的，准确来说是先执行最后一个转化为number，再执行倒数第二个依然是number。

```sh
3
3
3
"12"
3
"12"
"12"
2
2
"1true"
"11"
1
1
"1null"
"10"
NaN
NaN
"1undefined"
"1NaN"
"1NaN"
```

### 详细解析

Addition operator `+` works on both numbers and strings (used in string concatenation). Hence, if any of the operands is not a number, using `+` converts all operand/s to string and concatenates.

The unary plus operator (+) precedes its operand and attempts to convert it into a number if it isn't already.

Also, few things to know

```js
+1 // 1
+"1" // 1
+true // 1
+null // 0
+undefined // NaN
+NaN // NaN
```

Remember that the unary operator has higher precedence over the addition operator

```js
console.log(1 + 2) // 3
console.log(1 + + 2) // 1 + (+2) = 1 + 2 = 3
console.log(1 + + + 2) // 1 + (+(+2)) = 1 + 2 = 3
console.log(1 + '2') // "1" + "2" = "12" 
console.log(1 + + '2') // 1 + (+2) = 1 + 2 = 3
console.log('1' + 2) // "1" + "2" = "12"
console.log('1' + + 2) // "1" + (+2) = "1" + 2 = "1" + "2" = "12"
console.log(1 + true) // 1 + 1 = 2
console.log(1 + + true) // 1 + (+true) = 1 + 1 = 2
console.log('1' + true) // "1" + "true" = "1true"
console.log('1' + + true) // "1" + (+true) = "1" + 1 = "1" + "1" = "11"
console.log(1 + null) // 1 + 0 = 1
console.log(1 + + null) // 1 + (+null) = 1 + 0 = 1
console.log('1' + null) // "1" + "null" = "1null"
console.log('1' + + null) // "1" + (+null) = "1" + 0 = "1" + "0" = "10"
console.log(1 + undefined) // 1 + NaN = NaN
console.log(1 + + undefined) // 1 + (+undefined) = 1 + NaN = NaN
console.log('1' + undefined) // "1" + "undefined" = "1undefined"
console.log('1' + + undefined) // "1" + (+undefined) = "1" + NaN = "1" + "NaN" = "1NaN"
console.log('1' + + + undefined) // "1" +(+(+undefined)) = "1" + NaN = "1" + "NaN" = "1NaN"
```

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence



## [15. instanceOf](https://bigfrontend.dev/quiz/instanceOf)

### 题目

What does the code snippet to the right output by `console.log`?

```js
console.log(typeof null)
console.log(null instanceof Object) 
console.log(typeof 1)
console.log(1 instanceof Number)
console.log(1 instanceof Object)
console.log(Number(1) instanceof Object)
console.log(new Number(1) instanceof Object)
console.log(typeof true)
console.log(true instanceof Boolean)
console.log(true instanceof Object)
console.log(Boolean(true) instanceof Object)
console.log(new Boolean(true) instanceof Object)
console.log([] instanceof Array)
console.log([] instanceof Object)
console.log((() => {}) instanceof Object)
```

### 答案

对象才有原型链

```sh
"object"
false
"number"
false
false
false
true
"boolean"
false
false
false
true
true
true
true
```

### 详细解析

```js
console.log(typeof null);                          // "object" - 'null' has "object" type in js (backward compatibility)
console.log(null instanceof Object);               // false - 'null' is primitive and doesn't have 'instanceof' keyword
console.log(typeof 1);                             // "number" - one of js types
console.log(1 instanceof Number);                  // false - '1' is primitive and doesn't have 'instanceof' keyword
console.log(1 instanceof Object);                  // false - same as above
console.log(Number(1) instanceof Object);          // false - Number(1) === 1 - same as above
console.log(new Number(1) instanceof Object);      // true - 'new Number(1)' is object, so it's correct
console.log(typeof true);                          // "boolean" - one of js types
console.log(true instanceof Boolean);              // false - 'true' is primitive and doesn't have 'instanceof' keyword
console.log(true instanceof Object);               // false - same as above
console.log(Boolean(true) instanceof Object);      // false - Boolean(true) === true - same as above
console.log(new Boolean(true) instanceof Object);  // true - 'new Boolean(true)' is object, so it's correct
console.log([] instanceof Array);                  // true - '[]' is instanceof Array and Object
console.log([] instanceof Object);                 // true - '[]' is instanceof Array and Object
console.log((() => {}) instanceof Object);         // true - if it's not a primitive it's object. So callback is instanceof object
```



## [16. parseInt](https://bigfrontend.dev/quiz/parseInt)

### 题目

What does the code snippet to the right output by `console.log`?

```js
console.log(['0'].map(parseInt))
console.log(['0','1'].map(parseInt))
console.log(['0','1','1'].map(parseInt))
console.log(['0','1','1','1'].map(parseInt))
```

### 答案

注意parseInt第二个参数，没有默认是0

```sh
[0]
[0,NaN]
[0,NaN,1]
[0,NaN,1,1]
```

### 详细解析

```js
/** 
 * parseItn(string, radix) it's callback in map(el, index, arr)
 * it means second argument in map is index === second argument in parseInt is radix (from 2 to 36 - by default === 10)
 * example ["0", "1", "1"].map(parseInt): parseItn("0", 0), parseInt("1", 1), parseInt("1", 2)
 */
console.log(["0"].map(parseInt));                 // [0] - 1st: parseItn("0", 0) === 0
console.log(["0", "1"].map(parseInt));            // [0,NaN] - 2nd: parseInt("1", 1) === NaN
console.log(["0", "1", "1"].map(parseInt));       // [0,NaN,1] - 3rd: parseInt("1", 2) === 1
console.log(["0", "1", "1", "1"].map(parseInt));  // [0,NaN,1,1] - 4th: parseInt("1", 3) === 1
```



## [17. reduce](https://bigfrontend.dev/quiz/reduce)

### 题目

What does the code snippet to the right output by `console.log`?

```js
[1,2,3].reduce((a,b) => {
  console.log(a,b)
});

[1,2,3].reduce((a,b) => {
  console.log(a,b)
}, 0)
```

### 答案

reduce根据初始值决定后续的值

```sh
1,2
undefined,3
0,1
undefined,2
undefined,3
```

### 详细解析

Modify the example and add semi colons on lines 3 and the last line:

```js
[1,2,3].reduce((a,b) => {
  console.log(a,b)
});

[1,2,3].reduce((a,b) => {
  console.log(a,b)
}, 0);
```

The output is tricky as it's worth knowing the exact API of reduce:

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce

> If you don't supply an initial value then the FIRST value is used (and skipped!)

First:

```js
1,2
undefined,3 // note, undefined as we don't return from our reduce
```

Second

```js
0,1
undefined,2 // undefined as we don't return from our reduce
undefined,3
```



## [18. Promise executor II](https://bigfrontend.dev/quiz/Promise-executor-II)

### 题目

What does the code snippet to the right output by `console.log`?

```js
const p1 = Promise.resolve(1)
const p2 = new Promise((resolve) => resolve(p1))
const p3 = Promise.resolve(p1)
const p4 = p2.then(() => new Promise((resolve) => resolve(p3)))
const p5 = p4.then(() => p4)

console.log(p1 == p2)
console.log(p1 == p3)
console.log(p3 == p4)
console.log(p4 == p5)
```

### 答案

p1、p3

```sh
false
true
false
false
```

### 详细解析

```js
const p1 = Promise.resolve(1) // p1 is 1 Promise, Object=> `Promise { 1 }`
const p2 = new Promise((resolve) => resolve(p1)) // p2 is a pending promise, Object => `Promise { <pending> }`
// new Promise.resolve(arg) // if arg instance of Promise, then return arg directly.
const p3 = Promise.resolve(p1) // p3 is 1 (p3 is essentially Promise.resolve(1) ), Object => pointing to same object that `p1` is pointing i.e. `Promise { 1 }`
const p4 = p2.then(() => new Promise((resolve) => resolve(p3))) // p4 is a pending promise since .then() returns a new promise,  Object => `Promise { <pending> }`
const p5 = p4.then(() => p4) //  p5 is a pending promise since  .then() returns a new promise, / Object => `Promise { <pending> }`

const x = p1.then(x=>x)

console.log(p1, x)

console.log(p1 == p2); // false as both are pointing to different object
console.log(p1 == p3); // true as both point to same object
console.log(p3 == p4); // false as both are pointing to different object
console.log(p4 == p5); // false as both are pointing to different object
```



## [19. `this`](https://bigfrontend.dev/quiz/this)

### 题目

What does the code snippet to the right output by `console.log`?

```js
const obj = {
  a: 1,
  b: function() {
    console.log(this.a)
  },
  c() {
    console.log(this.a)
  },
  d: () => {
    console.log(this.a)
  },
  e: (function() {
    return () => {
      console.log(this.a);
    }
  })(),
  f: function() {
    return () => {
      console.log(this.a);
    }
  }
}

console.log(obj.a)
obj.b()
;(obj.b)()
const b = obj.b
b()
obj.b.apply({a: 2})
obj.c()
obj.d()
;(obj.d)()
obj.d.apply({a:2})
obj.e()
;(obj.e)()
obj.e.call({a:2})
obj.f()()
;(obj.f())()
obj.f().call({a:2})
```

### 答案

注意箭头函数只与所在位置外层函数有关，call、appy无效

```sh
1
1
1
undefined
2
1
undefined
undefined
undefined
undefined
undefined
undefined
1
1
1
```

### 详细解析

```js
/* 
Global Execution Context is `created` with following scope chain:
  - window: global object
  - this => window
  - obj => it points to a object which is also a function.

After `creation` phase `execution` phase starts
  - `obj` is defined 
 */
const obj = {
  a: 1,
  b: function () {
    console.log(this.a);
  },
  c() {
    console.log(this.a);
  },
  d: () => {
    console.log(this.a);
  },
  e: (function () {
    return () => {
      console.log(this.a);
    };
  })(), // IIFE, `e: ()=>console.log(this.a)`
  f: function () {
    /* Arrow function doesn't have `this` binding it use `this` value of nearest
    function or otherwise global object. */
    return () => {
      console.log(this.a);
    };
  },
};

console.log(obj.a); // 1 => straight forward, call property in obj 1

/* In `b` execution environment
  - this -> `a` obj
  Thus, it will print `1` */
obj.b(); // 1 => function b is called from obj
;(obj.b)() // 1 => () IIFE is the expression, it doesnt change the way we call the function. same as above
const b = obj.b;
b(); // undefined => obj.b return the function and reference to the obj is lost, `this===window` => undefined
obj.b.apply({ a: 2 }); // 2 => here function b is binded with {a: 2}, hence 2
obj.c(); // 1 => c function is called with obj, hence 1
obj.d(); // undefined => here d is arrow function, which is lexically scoped, thus `this===window`, so will print `undefined`
;(obj.d)() // undefined => () is the expression, it doesn't change the way we call the function. same as above
obj.d.apply({ a: 2 }); // undefined.  d is arrow function which is lexically scoped, we cant re-bind 'this' reference to arrow function, so `apply`, `call`, & `bind` don't work on arrow function.
obj.e() // undefined => e is the IIFE, which has its own scope. and a is not present in it. hence undefined
;(obj.e)() // undefined => e is IIFE, obj.e is invoked and have return arrow function, which cant be re-bined. It doesnt change the way we call the function. same as above
obj.e.call({ a: 2 }); // undefined => e is IIFE, obj.e is invoked and have return arrow function, which cant be re-bined `call` won't have any effect because it is arrow function

/* Arrow function doesn't have `this` binding it use `this` value of nearest
    function or otherwise global object. */
// `obj.f()===()=>console.log(this.a)`, this arrow function was defined inside
// another fn which had `this===obj`, thus it will return `obj.a===1`.
obj.f()(); // 1 => same as above1
obj.f().call({ a: 2 }); // 1 => same as above. note: arrow function cant be re-bind. its lexically scoped
```



## [20. name for Function expression](https://bigfrontend.dev/quiz/name-for-Function-expression)

### 题目

What does the code snippet to the right output by `console.log`?

```js
function a(){
}
const b = function() {
  
}

const c = function d() {
  console.log(typeof d)
  d = 'e'
  console.log(typeof d)
}

console.log(typeof a)
console.log(typeof b)
console.log(typeof c)
console.log(typeof d)
c()
```

### 答案

主要是考察匿名函数的重新赋值无效

```sh
"function"
"function"
"function"
"undefined"
"function"
"function"
```

### 详细解析

- `a` is a [Function Declaration](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function) and has data type `function`
- `b` and `c` are [Function Expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function#description) and have data type `function`
- `d` is a [Named Function Expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function#named_function_expression) This name `d` is then local only to the function body (scope) hence outside the function body `typeof d` returns `undefined`

```js
/* 
Global Execution Context is `created` with following scope chain:
  - window: global object
  - this => window
  - b, c => undefined,
  - a => it points to an object which is also a function.

After `creation` phase `execution` phase starts
  - b & c => point to yet another but different callable objects
 */ 
function a() {}
const b = function () {};

const c = function d() {

  console.log(typeof d);
  d = "e";
  console.log(typeof d);
};

console.log(typeof a); // "function"
console.log(typeof b); // "function"
console.log(typeof c); // "function"

// undefined.  `d` is hoisted out of function `c` but it was undefined
// assigned happens after function `c` is invoked.
console.log(typeof d);

/* 
`C Execution Context` is `created` with following scope chain:
  - arguments: { length: 0 }
  - this: window 

  - window: global object
  - this => window
  - b, c => undefined,
  - a => it points to an object which is also a function.

After `creation` phase `execution` phase starts
  - b & c => point to yet another but different callable objects

  Now, inside a `named function expression`, function name as a variable is available.
  If another variable is declared using `var` then it will overshadow `function binding,
  but it will be declared without `var` then it can't overshadow `function binding`. 
Thus, inside the function execution environment, it will point to the function itself but after 
  function execution environment destroyed it will be shown value assigned to it.
  
  Thus, in this case, both `console.log` in `function c()` with print "function"
*/

c();
```

The special case is inside the named function `d`. **The function name is un-reassignable inside the function**. You can easily see the difference if you run this in `"use strict"` mode where it gives an error `Uncaught TypeError: Assignment to constant variable`. Thus, `d` will still point to the named function `d` despite being reassigned to `"e"`

Note that, the result would have been different if we had redeclared `d` as `var d = "e"` in which case the next console.log would have printed `string` [See the Diff](https://stackoverflow.com/questions/1470488/what-is-the-purpose-of-the-var-keyword-and-when-should-i-use-it-or-omit-it/1471738#1471738)



## [21. Array I](https://bigfrontend.dev/quiz/Array-I)

### 题目

What does the code snippet to the right output by `console.log`?

```js
const a = [0]
console.log(a.length)
a[3] = 3
console.log(a.length)
for (let item of a) {
  console.log(item)
}
a.map(item => {console.log(item)})
a.forEach(item => {console.log(item)})
console.log(Object.keys(a))
delete a[3]
console.log(a.length)
a[2] = 2
a.length = 1
console.log(a[0],a[1],a[2])
```

### 答案

注意length不会因为delete减少，重新设置后，后面的就算有值也是undefined,ES5方法跳过空位

```sh
1
4
0
undefined
undefined
3
0
3
0
3
["0","3"]
4
0,undefined,undefined
```

### 详细解析

In JavaScript, [arrays](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) aren't primitives but are instead Array objects

The [length](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/length) property of an Array sets or returns the number of elements in that array. You can set the length property to truncate an array at any time. The thing to remember is that the `length` property does not necessarily indicate the number of defined values in the array

In [Array.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map#description) callback is invoked only for indexes of the array which have assigned values (including undefined). It is not called for missing elements of the array

Similarly, [Array.forEach()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach#no_operation_for_uninitialized_values_sparse_arrays) is not invoked for index properties that have been deleted or are uninitialized

Arrays being very similar to Objects allow [Object.keys](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys) to be called on it returning the indexes as an array. However, for sparse arrays, only the defined indexes are returned

Deleting array element using `delete` just unassigns the value (making it empty) at that index

```js
const a = [0]
console.log(a.length) // 1 Since array contains one element
a[3] = 3 // a = [0, empty, empty, 3]
console.log(a.length) // 4 Since array contains four elements now(even though only 2 elements are defined)
// 4, array length increase to contain last index. Unfilled slots in 
// middle remain empty. Accessing them return `undefined`. Such arrays are called
// sparse array. These have holes in them.

// for-of loop iterate to both holes and elments
for (let item of a) {
  console.log(item) // prints all the array items
}
// 0
// undefined
// undefined
// 3

// `map`, `filter`, `forEach` all these skips holes.
a.map(item => {console.log(item)}) // only called for assigned values
// 0
// 3

a.forEach(item => {console.log(item)}) // only called for assigned values
// 0
// 3

// Object.keys also skips holes
console.log(Object.keys(a)) // ["0","3"] only defined indexes are retuned

// deleting array elment does reduce its length. It just makes that index
// hole or emoty. Accessing it will return `undefined`
delete a[3] // deletes/unassigns that index
// a = [0, empty, empty, empty]
console.log(a.length) // 4 since length remains unaffected

a[2] = 2 // a = [0, empty, 2, empty]

// changing length of an array, remove all elements beyond mentined length.
a.length = 1 // this actually truncates the array so that length is only 1 now
// a = [0]

console.log(a[0],a[1],a[2]) // 0,undefined,undefined
```



## [22. min max](https://bigfrontend.dev/quiz/min-max)

### 题目

What does the code snippet to the right output by `console.log`?

```js
console.log(Math.min())
console.log(Math.max())
console.log(Math.min(1))
console.log(Math.max(1,2))
console.log(Math.min([1,2,3]))
console.log(Math.min([1,2,3],1))
```

### 答案

最大的数和最小的数默认是-Infinity和Infinity，一旦输入有NaN，输出必然是NaN

```sh
Infinity
-Infinity
1
2
NaN
```

### 详细解析

```js
console.log(Math.min()) // Infinity
console.log(Math.max()) // -Infinity
console.log(Math.min(1)) // 1
console.log(Math.max(1,2)) // 2
console.log(Math.min([1,2,3])) // NaN
```

`Math.min()` function returns the smallest numbers given as input parameters

`Math.max()` function returns the largest numbers given as input parameters

`1.` If no parameters are passed, `Math.min()` returns `Infinity`. This can be understood if you think of implementing this logic yourself, we'll set the min value as the largest possible value i.e. `Infinity`, and will loop over the parameters and compare the current value with this min value and update if current < min. In the end, we return min. Now, since no parameters are passed, we return `Infinity`.

`2.` The inverse of this logic applies to `Math.max()` that returns `-Infinity` when no parameters are passed.

`3.` & `4.` Usual behavior

`5.` If any one or more of the parameters cannot be converted into a number, `NaN` is returned by both methods.

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/min

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max



## [23. Promise.all()](https://bigfrontend.dev/quiz/Promise-all)

### 题目

What does the code snippet to the right output by `console.log`?

```js
(async () => {
  await Promise.all([]).then((value) => {
    console.log(value)
  }, (error) => {
    console.log(error)
  })
  
  await Promise.all([1,2,Promise.resolve(3), Promise.resolve(4)]).then((value) => {
    console.log(value)
  }, (error) => {
    console.log(error)
  })
  
  await Promise.all([1,2,Promise.resolve(3), Promise.reject('error')]).then((value) => {
    console.log(value)
  }, (error) => {
    console.log(error)
  })
})()
```

### 答案

一旦有reject，进入catch

```sh
[]
[1,2,3,4]
"error"
```

### 详细解析

The [Promise.all()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) method takes an iterable of promises as an input and returns a single Promise that resolves to an array of the results of the input promises

It rejects immediately upon any of the input promises rejecting or non-promises throwing an error and will reject with this first rejection message/error.

```js
(async () => {
  await Promise.all([]).then((value) => {
    console.log(value) // resolves with empty array []
  }, (error) => {
    console.log(error)
  })
  
  await Promise.all([1,2,Promise.resolve(3), Promise.resolve(4)]).then((value) => {
    console.log(value) // all promises resolve so returns [1,2,3,4]
  }, (error) => {
    console.log(error)
  })
  
  await Promise.all([1,2,Promise.resolve(3), Promise.reject('error')]).then((value) => {
    console.log(value)
  }, (error) => {
    console.log(error) // since 4th promise rejected, Promise.all also rejects with that value
  })
})()

// []
// [1,2,3,4]
// "error"           
```



## [24. Equality & Sameness](https://bigfrontend.dev/quiz/Equality-Sameness)

### 题目

What does the code snippet to the right output by `console.log`?

```js
console.log(0 == '0')
console.log(0 === '0')
console.log(Object.is(0, '0'))

console.log(0 == 0)
console.log(0 === 0)
console.log(Object.is(0, 0))

console.log(0 == -0)
console.log(0 === -0)
console.log(Object.is(0, -0))

console.log(NaN == NaN)
console.log(NaN === NaN)
console.log(Object.is(NaN, NaN))

console.log(0 == false)
console.log(0 === false)
console.log(Object.is(0, false))
```

### 答案

==会有隐式转换，但关于NaN，0与-0判断有部分问题，Object.is完全相等

```sh
true
false
false
true
true
true
true
true
false
false
false
true
true
false
false
```

### 详细解析

In a nutshell,

> The equality operator (==) checks whether its two operands are equal, it attempts to convert and compare operands that are of different types

> The strict equality operator (===) checks whether its two operands are equal without any implicit conversion

> The [Object.is()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) method determines whether two values are the same value. Note that this is not the same as being equal according to the == or === operator

Also, note that in Javascript, 0 is represented as both -0 and +0 (where 0 is an alias for +0). In practice, there is almost no difference between the different representations; for example, +0 === -0 is true. This difference can be noticed when using `Object.is` for comparison

`NaN` compares unequal (via ==, !=, ===, and !==) to any other value -- including to another `NaN` value. However, `Object.is` gives a true result.

```js
console.log(0 == '0') // true (after type conversion '0' = 0)
console.log(0 === '0') // false
console.log(Object.is(0, '0')) // false

console.log(0 == 0) // true
console.log(0 === 0) // true
console.log(Object.is(0, 0)) // true

console.log(0 == -0) // true
console.log(0 === -0) // true
console.log(Object.is(0, -0)) // false

console.log(NaN == NaN) // false
console.log(NaN === NaN) // false
console.log(Object.is(NaN, NaN)) // true

console.log(0 == false) // true (after type conversion false = 0)
console.log(0 === false) // false
console.log(Object.is(0, false)) // false
```



## 25. zero

What does the code snippet to the right output by `console.log`?

```js
console.log(1 / 0)
console.log(-1 / 0)
console.log(0 / 0)
console.log(0 === -0)
console.log(Object.is(0, -0))
console.log(Object.is(0, Math.round(-0.5)))
console.log(Object.is(0, Math.round(0.5)))
console.log(0 * Infinity)
console.log(Infinity / Infinity)
console.log(Object.is(0, Math.sign(0)))
console.log(Object.is(0, Math.sign(-0)))
console.log(1 / -0)
console.log(1 / 0)
console.log(1n / 0n)
```

Object.is完全相等，区分0与-0，Math.round(-0.5)=-0， Math.sign(-0)=-0，bigInt除法会报错

```sh
Infinity
-Infinity
NaN
true
false
false
false
NaN
NaN
true
false
-Infinity
Infinity
Error
```

### 26. true or false

What does the code snippet to the right output by `console.log`?

```js
console.log([] == 0)
console.log([] == false)
console.log(!![])
console.log([1] == 1)
console.log(!![1])
console.log(Boolean([]))
console.log(Boolean(new Boolean([])))
console.log(Boolean(new Boolean(false)))
```

==隐式转换

```sh
true
true
true
true
true
true
true
true
```

### 27. Hoisting I

What does the code snippet to the right output by `console.log`?

```js
const a = 1
console.log(a)

var b
console.log(b)
b = 2

console.log(c)
var c = 3

console.log(d)
let d = 2
```

变量提升

```sh
1
undefined
undefined
Error
```

### 28. Hoisting II

What does the code snippet to the right output by `console.log`?

```js
const func1 = () => console.log(1)

func1()

func2()

function func2() {
  console.log(2)
}


func3()

var func3 = function func4() {
  console.log(3)
}
```

变量提升

```sh
1
2
Error
```

### 29. Hoisting III

What does the code snippet to the right output by `console.log`?

```js
var a = 1

function func() {
  a = 2
  console.log(a)
  var a
}

func()

console.log(a)

if (!('b' in window)) {
  var b = 1
}

console.log(b)
```

变量提升，b提前声明

```sh
2
1
undefined
```

### 30. Equal II

What does the code snippet to the right output by `console.log`?

ref to the [The Abstract Equality Comparison Algorithm](https://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3)

```js
console.log([1] == 1)
console.log([1] == '1')
console.log(['1'] == '1')
console.log(['1'] == 1)
console.log([1] == ['1'])
console.log(new Boolean(true) == 1)
console.log(new Boolean(true) == new Boolean(true))
console.log(Boolean(true) == '1')
console.log(Boolean(false) == [0])
console.log(new Boolean(true) == '1')
console.log(new Boolean(false) == [0])
console.log(null == undefined)
```

2个引用类型比较看是不是同一个地址

```sh
true
true
true
true
false
true
false
true
true
true
false
true
```

### 31. Math

What does the code snippet to the right output by `console.log`?

```js
console.log(1 / 0)
console.log(0 / 0)
console.log(-1 / 0)
console.log(1 / 0 * 0)
console.log(1 / 0 * 1)
console.log(1 / 0 * -1)
console.log(1 / 0 * 1 + 1 / 0 * 1)
console.log(1 / 0 * 1 - 1 / 0 * 1)
console.log(1 / 0 * 1 * (1 / 0 * 1))
console.log(1 / 0 * 1 / (1 / 0 * 1))
console.log(0 / Infinity)
console.log(0 * Infinity)
```

0/0 = NaN，正数/0=Infinity，负数/0=-Infinity，0*Infinity=NaN，正数\*Infinity=Infinity，负数\*Infinity=-Infinity

```sh
Infinity
NaN
-Infinity
NaN
Infinity
-Infinity
Infinity
NaN
Infinity
NaN
0
NaN
```

### 32. Hoisting IIII

What does the code snippet to the right output by `console.log`?

```js
var a = 1
function a() {
}

console.log(typeof a)

var b
function b() {
}
b = 1

console.log(typeof b)

function c() {
}
var c = 1;

console.log(typeof c)

var d = 1;

(function(){
  d = '2'
  console.log(typeof d)
  function d() {
  }
})()

console.log(typeof d)

var e = 1
const f = function e() {}

console.log(typeof e)
```

先提升var声明，再提升函数声明

```sh
"number"
"number"
"number"
"string"
"number"
"number"
```

### 33. `this` II

What does the code snippet to the right output by `console.log`?

ref: https://javascript.info/reference-type

```js
const obj = {
  a: 1,
  b() {
    return this.a
  }
}
console.log(obj.b())
console.log((true ? obj.b : a)())
console.log((true, obj.b)())
console.log((3, obj['b'])())
console.log((obj.b)())
console.log((obj.c = obj.b)())
```

只有直接调用或IIFE保存this.a才能为1

```sh
1
undefined
undefined
undefined
1
undefined
```

### 34. precedence

What does the code snippet to the right output by `console.log`?

```js
let a = 1
console.log(a +++ a)

let b = 1
console.log(b + + + b)

let c = 1
console.log(c --- c)

let d = 1
console.log(d - - - d)
```

优先执行单目运算符，执行结束后a=2, b=1, c=0, d=1

```sh
3
2
1
0
```

### 35. Implicit Coercion III

What does the code snippet to the right output by `console.log`?

```js
console.log( [] + {} )
console.log( + {} )
console.log( + [] )
console.log( {} + [])
console.log( ({}) + [])
console.log( ({}) + [])
console.log( ({}) + [])
console.log( {} +  + [])
console.log( {} +  + [] + {} )
console.log( {} +  + [] + {}  + [])
```

优先执行单目运算符，执行结束后a=2, b=1, c=0, d=1

```sh
"[object Object]"
NaN
0
"[object Object]"
"[object Object]"
"[object Object]"
"[object Object]"
"[object Object]0"
"[object Object]0[object Object]"
"[object Object]0[object Object]"
```

### 36. Promise.prototype.finally()

What does the code snippet to the right output by `console.log`?

```js
Promise.resolve(1)
.finally((data) => {
  console.log(data)
  return Promise.reject('error')
})
.catch((error) => {
  console.log(error)
  throw 'error2'
})
.finally((data) => {
  console.log(data)
  return Promise.resolve(2).then(console.log)
})
.then(console.log)
.catch(console.log)
```

finally不接收参数

```sh
undefined
"error"
undefined
2
"error2"
```

### 37. push unshift

What does the code snippet to the right output by `console.log`?

```js
const arr = [1,2]
arr.push(3,4)
arr.unshift(5,6)
console.log(arr)
```

多个数/数组push/unshift按照顺序插入

```sh
[5,6,1,2,3,4]
```

### 38. Hoisting IV

What does the code snippet to the right output by `console.log`?

```js
let foo = 10
function func1() {
    console.log(foo)
    var foo = 1
}
func1 ()


function func2() {
    console.log(foo)
    let foo = 1
}
func2 ()
```

var提前声明，let块状作用域

```sh
undefined
Error
```

### 39. var

What does the code snippet to the right output by `console.log`?

```js
function foo() {
  console.log(i)
  for (var i = 0; i < 3; i++) {
    console.log(i)
  }
}

foo()
```

var提前声明

```sh
undefined
0
1
2
```

### 40. RegExp.prototype.test

What does the code snippet to the right output by `console.log`?

```js
console.log(/^4\d\d$/.test('404'))
console.log(/^4\d\d$/.test(404))
console.log(/^4\d\d$/.test(['404']))
console.log(/^4\d\d$/.test([404]))
```

测试数据会转化为 字符串加入判断

```sh
true
true
true
true
```

### 41. `this` III

What does the code snippet to the right output by `console.log`?

```js
const obj = {
  a: 1,
  b: this.a + 1,
  c: () => this.a + 1,
  d() {
    return this.a + 1
  },
  e() {
    return (() => this.a + 1)()
  }
}
console.log(obj.b)
console.log(obj.c())
console.log(obj.d())
console.log(obj.e())
```

obj.b不是函数相当于window.a + 1，箭头函数this指向非箭头函数的外层作用域

```sh
NaN
NaN
2
2
```

### 42. Hoisting V

What does the code snippet to the right output by `console.log`?

```js
(() => {
  if (!fn) {
    function fn() {
      console.log('2')
    }
  }
  fn()
})()

function fn() {
  console.log('1')
}

// another one
function fn1() {
  console.log('3')
}

(() => {
  if (!fn1) {
    function fn1() {
      console.log('4')
    }
  }
  fn1()
})()


// another one !
(() => {
  if (false) {
    function fn3() {
      console.log('5')
    }
  }
  fn3()
})()
```

有判断函数声明内部判断逻辑会提升，将函数定义为undefined

```sh
"2"
"4"
Error
```

### 43. JSON.stringify()

What does the code snippet to the right output by `console.log`?

```js
console.log(JSON.stringify(['false', false]))
console.log(JSON.stringify([NaN, null, Infinity, undefined]))
console.log(JSON.stringify({a: null, b: NaN, c: undefined}))
```

JSON.stringify()能区分string、bool和number类型，但无法区分NaN, null, Infinity和undefined，并且undefined用作对象的值会消失

```sh
"["false",false]"
"[null,null,null,null]"
"{"a":null,"b":null}"
```

### 44. Function call

What does the code snippet to the right output by `console.log`?

```js
function a() {
  console.log(1)
  return {
    a: function() {
      console.log(2)
      return a()
    }
  }
}

a().a()
```

先执行外面的函数a()，打印1，再执行返回的函数，打印2，最后再次执行外面的函数a()，打印1

```sh
1
2
1
```

### 45. Hoisting VI

What does the code snippet to the right output by `console.log`?

```js
var foo = 1;
(function () {
  console.log(foo);
  foo = 2;
  console.log(window.foo);
  console.log(foo);
  var foo = 3;
  console.log(foo);
  console.log(window.foo)
})()
```

注意IFEE内部变量和全局变量

```sh
undefined
1
2
3
1
```

### 46. Implicit Coercion IV

What does the code snippet to the right output by `console.log`?

```js
const foo = [0]
if (foo) {
  console.log(foo == true)
} else {
  console.log(foo == false)
}
```

[0]转化为bool为true，转化为字符为"0"

```sh
false
```

### 47. Promise Order II

What does the code snippet to the right output by `console.log`?

```js
console.log(1)

setTimeout(() => {
  console.log(2)
}, 10)

setTimeout(() => {
  console.log(3)
}, 0);

new Promise((_, reject) => {
  console.log(4)
  reject(5)
  console.log(6)
}).then(() => console.log(7))
.catch(() => console.log(8))
.then(() => console.log(9))
.catch(() => console.log(10))
.then(() => console.log(11))
.then(console.log)
.finally(() => console.log(12))

console.log(13) 
```

先同步再异步，异步中先微任务再宏任务

```sh
1
4
6
13
8
9
11
undefined
12
3
2
```

### 48. Prototype

What does the code snippet to the right output by `console.log`?

```js
function Foo() { }
Foo.prototype.bar = 1
const a = new Foo()
console.log(a.bar)

Foo.prototype.bar = 2
const b = new Foo()
console.log(a.bar)
console.log(b.bar)

Foo.prototype = {bar: 3}
const c = new Foo()
console.log(a.bar)
console.log(b.bar)
console.log(c.bar)
```

Foo.prototype是改变原型链上的数，Foo.prototype = {bar: 3}是重新改变原型链，原来已声明的实例

```sh
1
2
2
2
2
3
```

### 49. `this` IV

What does the code snippet to the right output by `console.log`?

```js
var bar = 1

function foo() {
  return this.bar++
}

const a = {
  bar: 10,
  foo1: foo,
  foo2: function() {
    return foo()
  },
} 


console.log(a.foo1.call())
console.log(a.foo1())
console.log(a.foo2.call())
console.log(a.foo2())
```

只有a.foo1()调用的是对象里的bar，其余this都指向window

```sh
1
10
2
3
```

### 50. async await

What does the code snippet to the right output by `console.log`?

```js
async function async1(){
  console.log(1)
  await async2()
  console.log(2)
}

async function async2(){
  console.log(3)
}

console.log(4)

setTimeout(function(){
  console.log(5)
}, 0)

async1()

new Promise(function(resolve){
  console.log(6)
  resolve()
}).then(function(){
  console.log(7)
})

console.log(8)
```

Foo.prototype是改变原型链上的数，Foo.prototype = {bar: 3}是重新改变原型链，原来已声明的实例

```sh
4
1
3
6
8
2
7
5
```

### 51. method

What does the code snippet to the right output by `console.log`?

```js
// This is a trick question

// case 1
const obj1 = {
  foo() {
    console.log(super.foo())
  }
}

Object.setPrototypeOf(obj1, {
  foo() {
    return 'bar'
  }
})

obj1.foo()

// case 2

const obj2 = {
  foo: function() {
    console.log(super.foo())
  }
}

Object.setPrototypeOf(obj2, {
  foo() {
    return 'bar'
  }
})

obj2.foo()
```

编译时报错，super只在方法中有效

```sh
Error
```

### 52. requestAnimationFrame

What does the code snippet to the right output by `console.log`?

```js
console.log(1)

setTimeout(() => {
  console.log(2)
}, 100)

requestAnimationFrame(() => {
  console.log(3)
})

requestAnimationFrame(() => {
  console.log(4)
  setTimeout(() => {
    console.log(5)
  }, 10)
})

const end = Date.now() + 200;
while (Date.now() < end) {
}

console.log(6)
```

requestAnimationFrame里的 setTimeout在执行别的 setTimeout事件后重新执行

```sh
1
6
3
4
2
5
```

### 53. Prototype 2

What does the code snippet to the right output by `console.log`?

```js
function F() {
  this.foo = 'bar'
}

const f = new F()
console.log(f.prototype)
```

实例无prototype

```sh
undefined
```

### 54. setTimeout(0ms)

What does the code snippet to the right output by `console.log`?

```js
setTimeout(() => {
    console.log(2)
}, 2)

setTimeout(() => {
    console.log(1)
}, 1)

setTimeout(() => {
    console.log(0)
}, 0)
```

实例无prototype

```sh
1
0
2
```

### 55. sparse array

What does the code snippet to the right output by `console.log`?

```js
const arr = [1,,,2]

// forEach
arr.forEach(i => console.log(i))

// map
console.log(arr.map(i => i * 2))

// for ... of
for (const i of arr) {
  console.log(i)
}

// spread
console.log([...arr])
```

ES5的方法跳过空格，ES保持空格，别的方法将空格转为undefined

```sh
1
2
[2,empty,empty,4]
1
undefined
undefined
2
[1,undefined,undefined,2]
```

### 56. to primitive

What does the code snippet to the right output by `console.log`?

```js
// case 1
const obj1 = {
  valueOf() {
    return 1
  },
  toString() {
    return '100'
  }
}

console.log(obj1 + 1)
console.log(parseInt(obj1))

// case 2
const obj2 = {
  [Symbol.toPrimitive]() {
    return 200
  },

  valueOf() {
    return 1
  },
  toString() {
    return '100'
  }
}

console.log(obj2 + 1)
console.log(parseInt(obj2))

// case 3
const obj3 = {
  toString() {
    return '100'
  }
}

console.log(+obj3)
console.log(obj3 + 1)
console.log(parseInt(obj3))

// case 4
const obj4 = {
  valueOf() {
    return 1
  }
}

console.log(obj4 + 1)
console.log(parseInt(obj4))

// case 5
const obj5 = {
  [Symbol.toPrimitive](hint) {
    return hint === 'string' ? '100' : 1
  },
}

console.log(obj5 + 1)
console.log(parseInt(obj5))
```

优先[Symbol.toPrimitive]，其次toString()，再其次valueOf

```sh
2
100
201
200
100
"1001"
100
2
NaN
2
100
```

### 57. non-writable

What does the code snippet to the right output by `console.log`?

```js
const a = {}
Object.defineProperty(a, 'foo1', {
  value: 1
})
const b = Object.create(a)
b.foo2 = 1

console.log(b.foo1)
console.log(b.foo2)

b.foo1 = 2
b.foo2 = 2


console.log(b.foo1)
console.log(b.foo2)
```

Object.defineProperty劫持属性，不会被修改

```sh
1
1
1
2
```

### 58. inherit getter setter

What does the code snippet to the right output by `console.log`?

```js
let val = 0

class A {
  set foo(_val) {
    val = _val
  }
  get foo() {
    return val
  }
}

class B extends A { }

class C extends A {
  get foo() {
    return val
  }
}

const b = new B()
console.log(b.foo)
b.foo = 1
console.log(b.foo)

const c = new C()
console.log(c.foo)
c.foo = 2
console.log(c.foo)
console.log(b.foo)
```

B没有重写getter/setter，C重写getter/setter

```sh
0
1
1
1
1
```

### 59. override setter

What does the code snippet to the right output by `console.log`?

```js
class A {
  val = 1
  get foo() {
    return this.val
  }
}

class B {
  val = 2
  set foo(val) {
    this.val = val
  }
}
const a = new A()
const b = new B()
console.log(a.foo)
console.log(b.foo)
b.foo = 3
console.log(b.val)
console.log(b.foo)
```

B重新修改val

```sh
1
undefined
3
undefined
```

### 60. postMessage

What does the code snippet to the right output by `console.log`?

```js
console.log(1)

window.onmessage = () => {
  console.log(2)
}

Promise.resolve().then(() => {
  console.log(3)
})

setTimeout(() => {
  console.log(4)
}, 0)

console.log(5)

window.postMessage('')

console.log(6)
```

window.onmessage是微任务，优先级在promise之后

```sh
1
5
6
3
2
4
```

### 61. onClick

What does the code snippet to the right output by `console.log`?

```js
console.log(1)

document.body.addEventListener('click', () => {
  console.log(2)
})

Promise.resolve().then(() => {
  console.log(3)
})

setTimeout(() => {
  console.log(4)
}, 0)

console.log(5)

document.body.click()

console.log(6)
```

onClick是一个同步任务

```sh
1
5
2
6
3
4
```

### 62. MessageChannel

What does the code snippet to the right output by `console.log`?

```js
console.log(1)

const mc = new MessageChannel()

mc.port1.onmessage = () => {
  console.log(2)
}

Promise.resolve().then(() => {
  console.log(3)
})

setTimeout(() => {
  console.log(4)
}, 0)

console.log(5)

mc.port2.postMessage('')

console.log(6)
```

MessageChannel是微任务，优先级在promise之后

```sh
1
5
6
3
2
4
```

### 63. in

What does the code snippet to the right output by `console.log`?

```js
const obj = {
  foo: 'bar'
}

console.log('foo' in obj)
console.log(['foo'] in obj)
```

键转化为字符串

```sh
true
true
```

### 64. reference type

What does the code snippet to the right output by `console.log`?

```js
const obj = {
  msg: 'BFE',
  foo() {
    console.log(this.msg)
  },
  bar() {
    console.log('dev')
  }
}

obj.foo();
(obj.foo)();
(obj.foo || obj.bar)();
```

第一二个调用了obj.msg，第三个obj.foo执行，this指向window

```sh
"BFE"
"BFE"
undefined
```

### 65. Function name

What does the code snippet to the right output by `console.log`?

```js
var foo = function bar(){ 
  return 'BFE'; 
};

console.log(foo());
console.log(bar());
```

明明函数可以直接调用，函数名无法声明

```sh
"BFE"
Error
```

### 66. comma

What does the code snippet to the right output by `console.log`?

```js
var obj = {
  a: "BFE",
  b: "dev",
  func: (function foo(){ return this.a; }, function bar(){ return this.b; })
}

console.log(obj.func())
```

 common operator evaluates from left to right and returns the last(right most) operand, so func is assined with bar()

```sh
"dev"
```

### 67. if

What does the code snippet to the right output by `console.log`?

```js
if (true) {
  function foo() {
    console.log('BFE')
  }
}
if (false) {
  function bar() {
    console.log('dev')
  }
}

foo()
bar()
```

foo是函数，bar是undefined

```sh
"BFE"
Error
```

### 68. if II

What does the code snippet to the right output by `console.log`?

```js
if (function foo(){ console.log('BFE') }) {
  console.log('dev')
}
foo()
```

foo是undefined，函数转化为bool进行判断

```sh
"dev"
Error
```

### 69. undefined

What does the code snippet to the right output by `console.log`?

```js
function foo(a, b, undefined, undefined) {
  console.log('BFE.dev')
}
console.log(foo.length)
```

函数参数的个数

```sh
4
```

### 69. undefined

What does the code snippet to the right output by `console.log`?

```js
function foo(){ console.log(1) }
var foo = 2
function foo(){ console.log(3) }
foo()
```

最后foo=2

```sh
Error
```

### 70. two-way generator

What does the code snippet to the right output by `console.log`?

```js
function* gen() {
  yield 2 * (yield 100)
}

const generator = gen()
console.log(generator.next().value)
console.log(generator.next(1).value)
console.log(generator.next(1).value)
```

第一次返回的结果为1，打印100，第二次打印2*1=2

```sh
100
2
undefined
```

### 71. Array length

What does the code snippet to the right output by `console.log`?
