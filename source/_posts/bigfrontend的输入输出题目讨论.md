---
title:  bigfrontend的输入输出题目讨论
date: 2021-06-17 21:07:33
categories: 
- web前端
tags:
- bigfrontend
- 前端
- 题库
---

国外前端会考察哪些问题？js函数、系统设计、TS类型体操的用法，大大拓宽了我的眼界，去思考和实战更加相关的问题吧，输入输出考验了对js语法的掌握程度，值得多次学习。虽然有的实际使用中不会碰到，但至少可以养成遇到问题查文档的好习惯。
<!-- more -->

# 输入输出

该部分综合考察对js的熟悉程度

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



## [25. zero](https://bigfrontend.dev/quiz/zero)

### 题目

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

### 答案

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

### 详细解析

In Javascript, `number` data type has only one integer with multiple representations: 0 is represented as both -0 and +0 (where 0 is an alias for +0). In practice, there is almost no difference between the different representations; for example, +0 === -0 is true. However, you are able to notice this when you divide by zero

```js
console.log(42 / +0) // Infinity
console.log(42 / -0) // -Infinity
```

[Object.is()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#description) method determines whether two values are the same value. This is not the same as being equal according to the == operator. The == operator applies various coercions to both sides (if they are not the same Type)

[Math.sign()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/sign) function returns either a positive or negative +/- 1, indicating the sign of a number passed into the argument. If the number passed into Math.sign() is 0, it will return a +/- 0.

```js
console.log(1 / 0) // Infinity
console.log(-1 / 0) // -Infinity
console.log(0 / 0) // NaN
console.log(0 === -0) // true

// false. This is confusing so remember this case for
// `Object.is`, `+0` & `-0` are different.
console.log(Object.is(0, -0)) // false

// false. This is confusing too. Remember `Math.round(-0.5)===-0`
// & `Math.round(-0.51)===-1`. Here we have `Object.is(0,-0)`=> false
console.log(Object.is(0, Math.round(-0.5))) // Object.is(0, -0) = false

// false. This is confusing too. Remember `Math.round(0.5)===1`
// & `Math.round(0.49)===0`. Here we have `Object.is(0,1)`=> false
console.log(Object.is(0, Math.round(0.5))) // Object.is(0, 1) = false
console.log(0 * Infinity) // NaN
console.log(Infinity / Infinity) // NaN

// true. Remember for negative number `Math.sign` gives `-1`, for
// postive `1` & for zero `0`. Here we have `Object.is(0,0)`=> true
console.log(Object.is(0, Math.sign(0))) // Object.is(0, 0) = true

// false. we have `Object(0,-1)` => false
console.log(Object.is(0, Math.sign(-0))) // Object.is(0, -0) = false
console.log(1 / -0) // -Infinity
console.log(1 / 0) // Infinity

// Error. For bigInt divion by `0n` throw TypeError. Otherwise
// other division just give us quotient just like other languages.
// e.g. 5n/2n=2n
console.log(1n / 0n) // gives RangeError in BigInt
```



## [26. true or false](https://bigfrontend.dev/quiz/true-or-false)

### 题目

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

### 答案

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

### 详细解析

`==` follows certain rules when comparing operands. If the operands are of different types, try to convert them to the same type before comparing

In `Boolean()`, if the value is omitted or is `0, -0, null, false, NaN, undefined, empty string ("")` it is falsy. All other values are truthy. Any object of which the value is not `undefined` or `null`, including a Boolean object whose value is `false`, evaluates to `true`

```js
// Number([]) // 0
// Number(false) // 0

console.log([] == 0)  // 0 == 0 is true, [] is converted to number [] => 0,
console.log([] == false) // 0 == 0 is true, converted to number and then to boolean. 0 == false => false == false
console.log(!![]) // !(![]) = !(!true) = !(false) = true, Boolean convertion, anything other than '', 0, false is true, !!true => !false => true
console.log([1] == 1) // 1 == 1 = true. number convertion
console.log(!![1]) // !(![1]) = !(false) =true
console.log(Boolean([])) // true

// new Boolean([]) returns an object
// new Boolean(false) This also returns an object
// Boolean(any object) will be true

console.log(Boolean(new Boolean([]))) // Boolean(some object) is true
console.log(Boolean(new Boolean(false))) // Boolean(some object) is true
```



## [27. Hoisting I](https://bigfrontend.dev/quiz/Hoisting-I)

### 题目

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

### 答案

变量提升

```sh
1
undefined
undefined
Error
```

### 详细解析

```js
const a = 1
console.log(a)  // a will be 1 since initialised before this

var b
console.log(b) // b will be declared a value and not assigned
b = 2

console.log(c) // c is not declared with var below this so c default value will // be undefined even though it is declared after this
var c = 3

console.log(d) // d is not declared it is in Temporal Dead Zone so can't be used before assigned or declared
let d = 2
```



## [28. Hoisting II](https://bigfrontend.dev/quiz/Hoisting-II)

### 题目

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

### 答案

变量提升

```sh
1
2
Error
```

### 详细解析

```js
const func1 = () => console.log(1)

func1() // 1

func2() // 2

function func2() {
  console.log(2)
}

func3() // Error

var func3 = function func4() {
  console.log(3)
}
```

> [Hoisting](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting) is the process whereby the interpreter moves the declaration of functions, variables, or classes to the top of their scope, prior to execution of the code.

`func1` is normal execution

We are able to execute `func2` before declaring it because of hoisting.

```
func3` is a Function Expression. Only declarations are hoisted and not initialization, hence `func3` is available but it is `undefined` before initialization. When we try to run `func3()` it throws an error `Uncaught TypeError: func3 is not a function
```



## [29. Hoisting III](https://bigfrontend.dev/quiz/Hoisting-III)

### 题目

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

### 答案

变量提升，b提前声明

```sh
2
1
undefined
```

### 详细解析

> [Hoisting](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting) is JavaScript's default behavior of moving all declarations to the top of the current scope (to the top of the current script or the current function).

Here, in this example inside function `func` the `var a` gets hoisted to top and hence `a=2` in that function. More so, since `a` is declared in that function only it is having a function scope i.e. it doesn't leak outside. That's why outside `func`, `a` is still 1

The variable `b` on the other hand is just declared inside a block so it gets hoisted to the top of the script. That's why `'b' in window` evaluates to true and its not going into `if` block and prints `undefined` instead

```js
var a = 1

// variable declared with `var` are hoisted to the top of the function scope
// which is just like:
// function func() {
// 	var a
//  a = 2
//  console.log(a)
// }

function func() {
  a = 2
  console.log(a)
  var a
}

func() // 2 (inside the func)

// the variable 'a' declared in function will be destoried as soon as the function exists
// print 1, the global variable 'a' is printed out
console.log(a) // 1 (outside value doesnt change)


// the same as 'a', 'b' is hoisted, like:
// var b
// if (!('b' in window)) {
//  b = 1
// }
if (!('b' in window)) { // b is present though its undefined
  var b = 1
}

// b is declared but not defined
console.log(b)
```



## [30. Equal II](https://bigfrontend.dev/quiz/Equal-II)

### 题目

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

### 答案

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

### 详细解析

> The equality operator (==) checks whether its two operands are equal, returning a Boolean result.

If one of the operands is a boolean, it'll convert the boolean operand to 1 if it is `true` and +0 if it is `false`. Other operands are also converted to Number and then compared. This also happens if operands are of different data types.

Also, remember the following -

```js
Number([1]) // 1
Number(['0']) // 0
Number(true) // 1 
Number(false) // 0
Boolean(true) // primitive true
new Boolean(true) // object true
```

Also, If the operands are both objects, return true only if both operands reference the same object (remember Arrays are also objects)

```js
/* 
whenever an object and a primitive are equated then objects 
are converted to primitive by calling their `toString` or `valueOf`
method. 

if both point to different objects then they are not equal.

`==` calls 'toString' first, then `valueOf, whereas
inequlities like `<` & `>`, calls 'valueOf` first then `toString`
*/
console.log([1] == 1) // ([1]).toSting()=== `1` => `1` == 1 => 1(here JS converted string in number) == 1 => true 
console.log([1] == '1') // same as above => 1 == 1 = true
console.log(['1'] == '1') // 1 == 1 = true
console.log(['1'] == 1) // 1 == 1 = true
console.log([1] == ['1']) // false as both are arrays (not the same reference)
console.log(new Boolean(true) == 1) // (new Boolean(true)).toString() === 'true' => 1 => 1 == 1 => true
console.log(new Boolean(true) == new Boolean(true)) // false as both are actually objects
console.log(Boolean(true) == '1') // true == '1' = 1 == 1 = true
console.log(Boolean(false) == [0]) // false == [0] = 0 == 0 = true
console.log(new Boolean(true) == '1') // object true == '1' = 1 == 1 = true
console.log(new Boolean(false) == [0]) // object fasle == [0] = // false as both are actually objects
console.log(null == undefined) // true
```



## [31. Math](https://bigfrontend.dev/quiz/Math)

### 题目

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

### 答案

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

### 详细解析

If we know how [Operator Precedence](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence) works this becomes much easy to understand.

Note that, if two operators have same precedence (ex. * and /) its evaluated from left to right

```js
/* 
Normal maths limit rules are appliation. In math, for four conditions limit 
doesn't exist
 - 0 * Infinity
 - 0 / 0
 - Infinity - Infinity
 - Infinity / Infinity

 wherever limit doesn't exist JS returns `NaN`.
*/
console.log(1 / 0) // Infinity 
console.log(0 / 0) // NaN
console.log(-1 / 0) // -Infinity

console.log(1 / 0 * 0) // NaN
// Infinity * 0 = NaN

console.log(1 / 0 * 1) // Infinity
// Infinity * 1 = Infinity
 
console.log(1 / 0 * -1) // -Infinity
// Infinity * -1 = -Infinity

console.log(1 / 0 * 1 + 1 / 0 * 1) // Infinity
// Infinity * 1 + Infinity * 1
// Infinity + Infinity = Infinity

console.log(1 / 0 * 1 - 1 / 0 * 1) // NaN
// Infinity * 1 - Infinity *1
// Infinity - Infinity = NaN

console.log(1 / 0 * 1 * (1 / 0 * 1)) // Infinity
// Infinity * 1 * (Infinity * 1)
// Infinity * Infinity = Infinity 


console.log(1 / 0 * 1 / (1 / 0 * 1)) // NaN
// Infinity * 1 / (Infinity * 1)
// Infinity / Infinity = NaM

console.log(0 / Infinity) // 0

console.log(0 * Infinity) // NaN
```



## [32. Hoisting IIII](https://bigfrontend.dev/quiz/Hoisting-IIII)

### 题目

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

### 答案

先提升var声明，再提升函数声明

```sh
"number"
"number"
"number"
"string"
"number"
"number"
```

### 详细解析

[Hoisting](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting) refers to the process whereby the interpreter appears to move the declaration of functions, variables, or classes to the top of their scope, prior to execution of the code.

In this example, imagine all the function declarations moved to the top prior to the execution of code. So, all the initializations of `a`, `b`, and `c` to 1 happen after they are declared(or functions are declared). Hence, `typeof` returns `"number"`

Then we have an [IIFE](https://developer.mozilla.org/en-US/docs/Glossary/IIFE) within which `d='2'` hence typeof inside the function prints `"string"`. However, after IIFE is executed the scope gets destroyed and hence after that it logs `"number"` as `d` is 1

Lastly, `e` is a named function expression but this name is then local only to the function body (scope). Thus, outside the expression body `e` is still 1 i.e. `"number"`

```js
var a = 1
function a() {
}

// function declaration is hoisted
console.log(typeof a)

var b
function b() {
}
b = 1

// function declaration is hoisted
console.log(typeof b)

function c() {
}
var c = 1;

console.log(typeof c)

var d = 1;

(function(){
// declar an variable without 'var, let, const' operators will make it become global variable
  d = '2'
  console.log(typeof d)
// function declaration is hoisted to the top of the function scope
  function d() {
  }
})()

// the same as:
(function(){
  function d() {
  }
  d = '2'
  console.log(typeof d)
})()

// the trick is that if function d() is removed, the d is changed to string 
// We know variables declared in function will be destoried when function exists.
// As I said above, function d() is hoisted to the top of its function scope,
// which make the 'd' become local variable instead of global variable,
// so global 'd' has never been re-defined.
console.log(typeof d)

var e = 1
const f = function e() {}

// none of function expression's business
console.log(typeof e)
```



## [33. `this` II](https://bigfrontend.dev/quiz/this-II)

### 题目

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

### 答案

只有直接调用或IIFE保存this.a才能为1

```sh
1
undefined
undefined
undefined
1
undefined
```

### 详细解析

Things to know-

- In Normal Functions, the value of [this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this#function_context) is determined by how a function is called i.e. runtime binding.
- The [comma operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comma_Operator) (,) evaluates each of its operands (from left to right) and returns the value of the last operand
- The [ternary operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) takes three operands: a condition followed by a question mark (?), then an expression to execute if the condition is truthy followed by a colon (:), and finally the expression to execute if the condition is falsy.
- The [assignment operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Assignment) returns the value of the object specified by the left operand after the assignment

```js
/* 
Global Execution Context is `created` with following scope chain:
  - window: global object
  - this => window
  - obj => undefined,

After `creation` phase `execution` phase starts
  - `obj` point to object: `{ a: 1, b: fn() }` . Here `b` is pointing to yer another
    object `fn===function(){ return this.a;}`
 */
const obj = {
  a: 1,
  b() {
    return this.a;
  },
};

/* 
`obj.b Execution Context` is `created` with following scope chain:
  - arguments: { length: 0 }
  - this -> point to `obj==={ a: 1, b: fn() }`

  - window: global object
  - this => window
  - obj => undefined,

After `creation` phase `execution` phase starts
  - returns this.a===1

Finally, `obj.b Execution Context` is destroyed.
*/
console.log(obj.b());

/* 
since it is `true` we get `obj.b`. It is like
const x = obj.b, then
x() ; here `x` will execute in global context and `this` points to window.

`x` Execution Context` is `created` with following scope chain:
  - arguments: { length: 0 }
  - this -> window

  - window: global object
  - this => window
  - obj => undefined,

After `creation` phase `execution` phase starts
  - returns this.a===undefined

Finally, `x` Execution Context` is destroyed.
*/
console.log((true ? obj.b : a)());
/* Same as last example */
console.log((true, obj.b)());
/* same as last example */
console.log((3, obj["b"])());
/* same as 1st example */
console.log(obj.b());
/* same as undefined examples */
console.log((obj.c = obj.b)());
```

`1.` When `b` is called as a method of `obj`, it's `this` is set to the object the method is called on (i.e. `obj` object). Hence , `this.a` is 1

```js
 /**
   * 1
   * undefined => conditional operator return value <-- f b(){return this.a}  -->, context is lost.
   * undefined => comma operator return value <-- f b(){return this.a}  -->, context is lost.
   * undefined => comma operator return value <-- f b(){return this.a}  -->, context is lost. array is cobverted to dtring
   * 1         => grouping operator, returns Reference 
   * undefined => assingment opertaor return value  <-- f b(){return this.a}  -->, context is lost.
   */
```



## [34. precedence](https://bigfrontend.dev/quiz/precedence)

### 题目

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

### 答案

优先执行单目运算符，执行结束后a=2, b=1, c=0, d=1

```sh
3
2
1
0
```

### 详细解析

Whenever three unary operators are placed one after another it actually becomes a post-increment/decrement operator followed by another operator [See Reason](https://en.wikipedia.org/wiki/Maximal_munch)

But, if there are spaces between the operators, it actually means multiple unary operators one after another

Basically,

```js
a +++ a // is same as (a++ + a)
a + + + a // is same as (a +  +(+a)) = a + a
```

Also, remember If used [postfix](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Increment), with operator after operand (for example, x++), the increment operator increments and returns the value before incrementing.

```js
let a = 1
console.log(a +++ a) // 3
// (a++  + a) = 1 + 2 = 3 (Note that a after + gets incremented)

let b = 1
console.log(b + + + b) // 2
// (1 + +(+1)) = (1 + +1) = 1 + 1 = 2


let c = 1
console.log(c --- c) // 1
// (c--  - c) = 1 - 0 = 1 (Note that c after - gets decremented)

let d = 1
console.log(d - - - d) // 0
// (1 - -(-1)) = (1 - 1) = 0
```



## [35. Implicit Coercion III](https://bigfrontend.dev/quiz/Implicit-Conversion-III)

### 题目

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

### 答案

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

### 详细解析

In mathematical operators, `+` works on both numbers and strings (used in string concatenation). Hence, if any of the operands is not a number, using `+` converts all operand/s to string and concatenates.

> The unary plus operator (+) precedes its operand and attempts to convert it into a number if it isn't already.

Also, unary operators have higher precedence over addition/concatenation

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence#table

Let's know a few things first-

```js
+ "1" // converts into number 1
+ {} // NaN
+ [] // 0

({}).toString() // "[object Object]"
[].toString() // ""
```

Breaking it down line by line—

```js
/* 
When two objects are added then they are converted to primitives using `toString()`
and `valueOf()` methods.


({}).toString() => '[object Object]'
([]).toString() => ''
*/

console.log( [] + {} ) // "" + "[object Object]" = "[object Object]"

console.log( + {} ) // `+` converts to number thus Number([object Object]) === NaN

console.log( + [] ) // Number('') === 0

console.log( {} + []) // "[object Object]" + "" = "[object Object]"

console.log( ({}) + []) // "[object Object]" + "" = "[object Object]"

console.log( ({}) + []) // "[object Object]" + "" = "[object Object]"

console.log( ({}) + []) // "[object Object]" + "" = "[object Object]"

console.log( {} +  + []) // "[object Object]" + "0" = "[object Object]0"
// think of it as console.log({} + (+[]))
// unary operator performs first which means it is now console.log({} + 0)

console.log( {} +  + [] + {} ) //  "[object Object]" + "0" + "[object Object]" = "[object Object]0[object Object]"
// think of it as console.log({} + (+[]) + {})
// unary operator performs first which means it is now console.log({} + 0 + {})

console.log( {} +  + [] + {}  + []) //  "[object Object]" + "0" + "[object Object]" + "" = "[object Object]0[object Object]"
// think of it as console.log({} + (+[]) + {} + [])
// unary operator performs first which means it is now console.log({} + 0 + {} + [])
```

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Unary_plus



## [36. Promise.prototype.finally()](https://bigfrontend.dev/quiz/Promise-prototype-finally)

### 题目

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

### 答案

finally不接收参数

```sh
undefined
"error"
undefined
2
"error2"
```

### 详细解析

Here, Promise resolves with the value 1 and control goes to the first finally block. A `finally` callback will not receive any argument hence it logs `undefined` and it returns a rejected promise.

Next control goes to the first catch block and "error" parameter is passed to it. It throws another error "error2" and control goes to the chained finally. Again, no argument is passed to finally so it logs `undefined`

This `finally` returns a resolved promise that logs `2`.

Lastly, the previously thrown `error2` is catched and logs `error2`

```js
/* Promise is resolved immeditely, then 
finally handler is queued to `microtask queue` */
Promise.resolve(1)
  /* This finally handler is dequeued and executed. Since finally receives
no arguments thus `data===undefined` */
.finally((data) => { 
  console.log(data) // undefined because .finally() does not receive any argument
  return Promise.reject('error')
})
/* after return from finally handler, catch error handler is queued to
  'microtask queue'. Thus, it is dequeued and executed. It receives `error` 
  as argument from previous reject and prints `error`. Finally it throw an error */
.catch((error) => {
  console.log(error) // "error" from the rejected promise in previous .finally()
  throw 'error2'
})
/* it is queued to microtask queue after execution completion of last catch 
  error handler. Then it is dequeued, and executed. Since finally doesn't receive
  any argument thus `data===undefined`. It prints `undefined`. */
.finally((data) => {
  console.log(data) // undefined because .finally() does not receive any argument
  /* Promise is resolved instantnially. Then `console.log` queued and dequeued 
    from microtask queue. Print `2`. This function returns `undefined. */
  return Promise.resolve(2).then(console.log) // 2
})
  /* it is skipped because there was rejected promise before finally */
.then(console.log) // ignored
  /* rejected promise is printed i.e. `error2` */
.catch(console.log) // "error2" from previous .catch()
```



## [37. push unshift](https://bigfrontend.dev/quiz/push-unshift)

### 题目

What does the code snippet to the right output by `console.log`?

```js
const arr = [1,2]
arr.push(3,4)
arr.unshift(5,6)
console.log(arr)
```

### 答案

多个数/数组push/unshift按照顺序插入

```sh
[5,6,1,2,3,4]
```

### 详细解析

> The `push()` method adds one or more elements to the end of an array

> The `unshift()` method inserts one or more values to the beginning of an array.

The important thing to note is that, if multiple elements are passed as parameters, they're inserted as a chunk at the beginning, in the exact same order they were passed as parameters. This is different than adding it one by one.

```js
/* 
Global Execution Context is `created` with following scope chain:
  - window: global object
  - this => window
  - arr => undefined,

After `creation` phase `execution` phase starts
  - `arr` points to a object `[1,2]`
 */ 
const arr = [1, 2];
// two more elements insert in object that is pointed by `arr`
arr.push(3, 4);
// two more elements insert in object that is pointed by `arr`
arr.unshift(5, 6);
console.log(arr);
```



## [38. Hoisting IV](https://bigfrontend.dev/quiz/Hoisting-IV)

### 题目

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

### 答案

var提前声明，let块状作用域

```sh
undefined
Error
```

### 详细解析

[Hoisting](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting) refers to the process whereby the interpreter appears to move the declaration of functions, variables, or classes to the top of their scope, prior to execution of the code.

The default initialization of the `var` is `undefined`. A variable declared with `let` is also hoisted but, unlike `var`, its not initialized with a default value. An exception will be thrown if we try to read before it is initialized.

```js
/* 
Global Execution Context is `created` with following scope chain:
  - window: global object
  - this => window
  - foo = undefined
  - func1 => it points to an object which is also a function.
  - func2 => it points to an object which is also a function.

After `creation` phase `execution` phase starts
  - `foo` is assigned `10` 
 */
let foo = 10;
function func1() {
  console.log(foo);
  var foo = 1;
}
/*
`func1 Execution Context` is `created` with following scope chain:
    - arguments: { length: 0 }
    - this: points to `window`
    - foo: undefined

    - window: global object
    - this => window
    - foo = undefined
    - func1 => it points to an object which is also a function.
    - func2 => it points to an object which is also a function.

After `creation` phase `execution` phase starts
    - `foo` is assigned `1` 
    - return undefined

Finally, `func1 Execution Context` is destroyed.
*/
func1();

function func2() {
  console.log(foo);
  let foo = 1;
}

/*
`func2 Execution Context` is `created` with following scope chain:
    - arguments: { length: 0 }
    - this: points to `window`
    - foo: undefined

    - window: global object
    - this => window
    - foo = undefined
    - func1 => it points to an object which is also a function.
    - func2 => it points to an object which is also a function.

After `creation` phase `execution` phase starts
    - ReferenceError: foo is not defined

Finally, `func2 Execution Context` is destroyed.
*/
func2();
```



## [39. var](https://bigfrontend.dev/quiz/var)

### 题目

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

### 答案

var提前声明

```sh
undefined
0
1
2
```

### 详细解析

```js
/* 
Global Execution Context is `created` with following scope chain:
  - window
  - this => window
  - foo => it points to an object which is also a function.

After `creation` phase `execution` phase starts
 */

function foo() {
  console.log(i); // i gets hoisted but value not assigned therefore it returns undefined
  for (var i = 0; i < 3; i++) {
    console.log(i); // 0 1 2
  }
}

/* 
`foo Execution Context` is `created` with following scope chain:
  - arguments: { length: 0 }
  - this: points to `window`
  - 'i'=== undefined

  - window
  - this => points to `window`
  - foo => it points to an object which is also a function.

After `creation` phase `execution` phase starts
  - `i` will change from 0 to 3
  - returns undefined

Finally, `foo Execution Context` is destroyed. 
*/
foo();
```



## [40. RegExp.prototype.test](https://bigfrontend.dev/quiz/RegExp-prototype-test)

### 题目

What does the code snippet to the right output by `console.log`?

```js
console.log(/^4\d\d$/.test('404'))
console.log(/^4\d\d$/.test(404))
console.log(/^4\d\d$/.test(['404']))
console.log(/^4\d\d$/.test([404]))
```

### 答案

测试数据会转化为 字符串加入判断

```sh
true
true
true
true
```

### 详细解析

```js
console.log(/^4\d\d$/.test('404')) // true
console.log(/^4\d\d$/.test(404)) // true
console.log(/^4\d\d$/.test(['404'])) // true
console.log(/^4\d\d$/.test([404])) // true
```

The `test()` method expects a `string` as input, against which to match the regular expression.

So, if the input is not a string it simply converts the input to a string and then matches with regex.

`^4\d\d$` 👉🏻 Starts with 4 followed by exactly one digit and ending with another digit

```js
"404" // true
404.toString() // "404" true
["404"].toString() // "404" true
[404].toString() // "404" true
```



## [41. `this` III](https://bigfrontend.dev/quiz/this-III)

### 题目

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

### 答案

obj.b不是函数相当于window.a + 1，箭头函数this指向非箭头函数的外层作用域

```sh
NaN
NaN
2
2
```

### 详细解析

> `Normal Function` — the value of this is determined by how a function is called i.e. runtime binding.

> `Arrow Function` — don't provide their own this binding; it retains the `this` value of the enclosing lexical context i.e. static binding.

Also, In Javascript, only function calls establish a new `this` context. An object literal constructor is not a method call, so it does not affect this in any way; it will still refer to whatever it was referring to outside of the object literal.

```js
/* 
Global Execution Context is `created` with following scope chain:
  - window
  - this => window
  - obj => undefined,

After `creation` phase `execution` phase starts
  - `obj` points to `{ a: 1, b: NaN, c: fn(), d: fn(), e: fn() }`. 
 */
const obj = {
  a: 1,
  /*  `this` is created in two times
          - globalthis
          - in methods defined with normal function where `this` in scope chain refers object on which 
            method is called. 
   */
  b: this.a + 1,
  c: () => this.a + 1,
  d() {
    return this.a + 1;
  },
  e() {
    return (() => this.a + 1)();
  },
};
const f = (g) => g;

/* Here `this` refers to `globleThis`. It doesn't have `a`, thus 
  `this.a===undefined`. When `undefined` is added to `1`, then is converted to
  `number` as `Number(undefined)` => NaN, and `NaN+1===NaN`
   */
console.log(obj.b);

/* Since the arrow function doesn't update `this` in their scope chain with
an object on which it is invoked Thus here `this` refers to `globalThis`. Same as
the previous `console.log`, we will get `NaN */
console.log(obj.c());

/* `d` method is defined with normal function. It will update its `this` with
object on which it is invoked which is here `obj`. Thus it returns `1+1===2` */
console.log(obj.d());

/* As talked earllier only global and functions create new execution environement
and so new `this`. `e()` created new execution environment and refer `obj` and `this`.
Arrow use this and return `1+1===2` */
console.log(obj.e());
```



## [42. Hoisting V](https://bigfrontend.dev/quiz/hoisting-v)

### 题目

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

### 答案

有判断函数声明内部判断逻辑会提升，将函数定义为undefined

```sh
"2"
"4"
Error
```

### 详细解析

We are using [IIFE](https://developer.mozilla.org/en-US/docs/Glossary/IIFE) here to execute functions.

[Hoisting](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting) refers to the process whereby the interpreter appears to move the declaration of functions, variables, or classes to the top of their scope, prior to execution of the code. The default initialization of the var is undefined.

`fn` is a [Conditionally Created Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function#conditionally_created_functions) whose behavior differs across browsers. In chrome, `fn` gets hoisted but since its `undefined` the `if` evaluates to `true` adn `fn()` gets defined printing `"2"` later

`fn1` is declared twice (both outside and inside IIFE). However, inside the IIFE because of Hoisting `fn1` is actually `undefined` hence `if` evaluates to true and `fn1` gets redeclared 👉🏻 prints `"4"
fn3` gets hoisted too, however the difference is this time `if` is using `false` so it never gets inside it and `fn3` remains undefined. Executing `fn3()` thus throws `Uncaught TypeError: fn3 is not a function

```js
(() => {
  // var fn;  Because of Hoisting
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
  // var fn1; Because of Hoisting
  if (!fn1) {
    function fn1() {
      console.log('4')
    }
  }
  fn1()
})()


// another one!
(() => {
  // var fn3; Because of Hoisting
  if (false) {
    function fn3() {
      console.log('5')
    }
  }
  fn3()
})()

// "2"
// "4"
// Error
```

for more details: https://stackoverflow.com/questions/31419897/what-are-the-precise-semantics-of-block-level-functions-in-es6



## 43. JSON.stringify()

### 题目

What does the code snippet to the right output by `console.log`?

> Please refer to the format guide for cases like quotes in quotes

```js
console.log(JSON.stringify(['false', false]))
console.log(JSON.stringify([NaN, null, Infinity, undefined]))
console.log(JSON.stringify({a: null, b: NaN, c: undefined}))
```

### 答案

JSON.stringify()能区分string、bool和number类型，但无法区分NaN, null, Infinity和undefined，并且undefined用作对象的值会消失

```sh
"["false",false]"
"[null,null,null,null]"
"{"a":null,"b":null}"
```

### 详细解析

`JSON.stringify()` method converts a JavaScript object or value to a JSON string following these rules-

1. `Boolean`, `Number`, and `String` are converted to the corresponding primitive values during stringification
2. `undefined` is not a valid JSON value. Such values encountered during conversion are either omitted (in an object) or changed to `null` ( in an array)
3. `Infinity`, `NaN`, as well as the value `null`, are all considered `null`

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#description

```js
/*  In Array & Objects, stringify make `NaN, Infinity, undefined` => 'null'. Then wrap
it in string and return.

In object, it removes keys which has `undefined` value
 */

// both primitives remain as it is
console.log(JSON.stringify(['false', false])) // "["false",false]"

// in an array these values are converted to null
console.log(JSON.stringify([NaN, null, Infinity, undefined])) // "[null,null,null,null]"

// in an object, undefined keys are omitted, while NaN gets converted to null
console.log(JSON.stringify({a: null, b: NaN, c: undefined})) // "{"a":null,"b":null}"
```



## [44. Function call](https://bigfrontend.dev/quiz/Function-call)

### 题目

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

### 答案

先执行外面的函数a()，打印1，再执行返回的函数，打印2，最后再次执行外面的函数a()，打印1

```sh
1
2
1
```

### 详细解析

In this example, when we call the outer function, it prints 1 and then returns an object with another function a. By calling it like `a().a()` we invoke the `a` method of this returned object too. That's why it prints 2.

On the last line, when we write `return a()`, its actually a function invocation of outer function `a`. Hence, it prints 1 again

```js
/* 
Global Execution Context is `created` with following scope chain:
  - window
  - this => window
  - `a` => it points to an object which is also a function.

After `creation` phase `execution` phase starts and it continue till end of script.
when script stops executing, then only it will be destroyed.
 */
function kaka() {
  console.log(1); // 1
  return {
    kaka: function huha() {
      console.log(2);
      return kaka();
    },
  };
}

/* `kaka Execution Context` is `created` with following scope chain:
      - arguments: { length: 0 }
      - this: points to `window`

      - window
      - this => points to `window`
      - `Kaka` => it points to an object which is also a function.

    After `creation` phase `execution` phase starts
      - it returns an object which has property named `kaka` which points to 
        yet other callable object

    Here, `foo Execution Context` is not destroyed because of closure. It
    will destroy after execution of clouser destroyed.
 */
const temp = kaka(); // it will call `kaka` function => 1

/* 
temp = {
    kaka: function () {
      console.log(2); // 2 
      return kaka();
    },
  }
*/

/* `temp.kaka Execution Context` is `created` with following scope chain:
      - arguments: { length: 0 }
      - this: points to return value of `temp` i.e. `{ a: f() }`

      - arguments: { length: 0 }
      - this: points to `window`

      - window
      - this => points to `window`
      - `a` => it points to an object which is also a function.

    After `creation` phase `execution` phase starts
      - it returns `a` and executes it.
      - `a Execution Context` is `created` with following scope chain:
          - arguments: { length: 0 }
          - this: points to `window`

          - arguments: { length: 0 }
          - this: points to return value of `temp` i.e. `{ a: f() }`

          - arguments: { length: 0 }
          - this: points to `window`

          - window
          - this => points to `window`
          - `a` => it points to an object which is also a function.

        After `creation` phase `execution` phase starts
          - it returns `kaka`. 
          - This `kaka` is in global context and its `this===window`.
          - It will print 1 and return `temp` again.


    Finally, all `Execution Context` are destroyed.
*/

const temp2 = temp.a();
```





## [45. Hoisting VI](https://bigfrontend.dev/quiz/Hoisting-VI)

### 题目

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

### 答案

注意IFEE内部变量和全局变量

```sh
undefined
1
2
3
1
```

### 详细解析

[Hoisting](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting) refers to the process whereby the interpreter appears to move the declaration of functions, variables, or classes to the top of their scope, prior to execution of the code. JavaScript only hoists declarations, not initializations! The default initialization of the `var` is `undefined`

Here, we have created a global variable `foo` that will also be available on `window`. Then we are executing an [IIFE](https://developer.mozilla.org/en-US/docs/Glossary/IIFE) and in this function, a local `foo` is created.

Note that, this `foo` is different from `window.foo` as, `foo` will refer to the local variable while `window.foo` points to the outer variable `foo = 1`

```js
/* 
Global Execution Context is `created` with following scope chain:
  - window
  - this => window
  - foo === undefined,

After `creation` phase `execution` phase starts
  - `foo` is assigned `1`. 
 */
var foo = 1;

/* This anonmous function's `Execution Context` is `created` with following scope chain:
      - arguments: { length: 0 }
      - this: points to `window`.
      - foo===undefined (because of hoisting)

      - window
      - this => points to `window`
      - bar=1, foo=>f(), a=> { bar: 10, foo1: fn(), foo2: fn() }

    After `creation` phase `execution` phase starts
      - `foo=2` statement will assign `foo` value `2`.
      - `var foo = 3` statement will assign `foo` value `3`.
      it returns `a.bar` and then increase it by one.

    Finally, `foo Execution Context` is destroyed.  */
(function () {
  // 'var foo = 3' is hoisted here -> var foo = undefined
  console.log(foo); // undefined - local variable
  foo = 2;
  console.log(window.foo); // 1 - window's variable
  console.log(foo); // 2 - local variable
  var foo = 3;
  console.log(foo); // 3 - local variable
  console.log(window.foo); // 1 - window's variable
})();
```



## [46. Implicit Coercion IV](https://bigfrontend.dev/quiz/implicit-coersion-2)

### 题目

What does the code snippet to the right output by `console.log`?

```js
const foo = [0]
if (foo) {
  console.log(foo == true)
} else {
  console.log(foo == false)
}
```

### 答案

[0]转化为bool为true，转化为字符为"0"

```sh
false
```

### 详细解析

In this example, `if(foo)` is trying to check if `[0]` is truthy or falsy.

Basically, if the value is or is `0`, `-0`, `null`, `false`, `NaN`, `undefined`, or `""` its `falsy`. All other values, including any object, an empty array ([]), or the string "false" are `truthy`

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean

That's why it goes into the if `block`.

The way `==` works, If the operands are of different types, it'll try to convert them to the same type before comparing. For example, here one of the operands is a boolean, and the other is a number so it'll convert them to numbers and then compare.

```js
Number(true) // 1
Number([0]) // 0
// 1 != 0 hence the answer is false
```

so

```js
/* 
Global Execution Context is `created` with following scope chain:
  - window
  - this => window
  - foo === undefined,

After `creation` phase `execution` phase starts
  - `foo` points to an object `[0]`. 
 */
const foo = [0];

/* Falsy Value: 
    - empty string: "",'',``
    - zero: 0, -0, +0
    - null, undefined, NaN, false
*/

// Since `foo` is not a falsy value, thus we will land in the `if` block

if (foo) {
  /* 
    1. When comparing with primitives, JS calls `toSting` and `valueOf` methods 
    on objects
    2. ([]).toString() => "" i.e. empty string
    3. Since empty string ("") is  primitive & falsy, therefore it is converted to `false`
    4. Since, false != true, thus we get `false`
     */
  console.log(foo == true);
} else {
  console.log(foo == false);
}
```



## [47. Promise Order II](https://bigfrontend.dev/quiz/promise-order-II)

### 题目

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

### 答案

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

### 详细解析 

Key points to remember are-

- All synchronous code gets executed first
- The Promise object represents the eventual completion (or failure) of an asynchronous operation and its resulting value. Basically, it's a nonblocking block of code that executes when the call stack is empty
- The executor is called synchronously by the Promise constructor before even the Promise constructor returns the created object
- `Promises` create `Microtask` which has priority over the `Macrotask` created by `setTimeout`.

So all code execution can be explained in these steps-

- `1` gets printed
- `3` and `2` get pushed to the Macrotask queue (remember 3 gets pushed before 2 because of less delay)
- `4` and `6` get printed next and the promise is queued to the Microtask queue
- `13` also gets printed being a synchronous code
- Now since, the call stack is empty. Microtask queue is checked first and promise gets executed. Since it is rejected, it goes into catch block and `8` gets printed
- Because of promise chaining as long as no error happens, all subsequent `then` get executed i.e. `9`, `11`, `undefined` and then `finally` gets invoked so `12` gets printed
- Lastly, queued setTimeouts get executed i.e. 3 and 2 will get printed

```js
console.log(1); // 1st => 1

/* 10ms timer is initiated. It will insert callback function in
 `Task Queue` after 10ms. */
setTimeout(() => {
  console.log(2); //11th => 2
}, 10);

/* 0ms timer is initiated. It will insert callback function in
 `Task Queue` after 0ms. */
setTimeout(() => {
  console.log(3); // 10th => 3
}, 0);

/* `executor` function of this promise is immediately executed by placing 
it in `call stack` */
new Promise((_, reject) => {
  console.log(4); // 2nd => 4
  reject(5);
  console.log(6); //3rd => 6
})
  .then(() => console.log(7))
  /* This `catch` handler will be placed in `microtask queue` after execution
  of `console.log(13)`. Once entire script is read, which will be immeditely after
  `console.log(13)`, `error handler` will be moved to `call stack` and executed. */
  .catch(() => console.log(8)) // 5th => 8
  /* After `error handler` execution completion. `resolve handler`[() => console.log(9)]
   is inserted in `microtask queue`. Then, it will be pulled out from there and 
   inserted into call stack and executed*/
  .then(() => console.log(9)) // 6th =>9
  /* it will be skipped because last promise was fullfilled and not rejected */
  .catch(() => console.log(10))
  /* same story to microtask and call stack will repeat */
  .then(() => console.log(11)) // 7th => 11
  .then(console.log) // 8th => `undefined` was returned from last `then`, so it will print `undefined`
  .finally(() => console.log(12)); // 9th => 12

console.log(13); // 4th => 13
```



## [48. Prototype](https://bigfrontend.dev/quiz/prototype)

### 题目

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

### 答案

Foo.prototype是改变原型链上的数，Foo.prototype = {bar: 3}是重新改变原型链，原来已声明的实例

```sh
1
2
2
2
2
3
```

### 详细解析

[Prototypes](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object_prototypes) are the mechanism by which JavaScript objects inherit features from one another. Basically, when you try to access a property of an object: if the property can't be found in the object itself, the prototype is searched for the property. If the property still can't be found, then the prototype's prototype is searched, and so on until either the property is found.

Here, we have added `bar` property to the `prototype` of Foo. However, in the first two instances viz. `Foo.prototype.bar = 1` and `Foo.prototype.bar = 2` we are just updating this property to a value thus this change affects both previously created objects and newly created objects.

In the last instance, we are actually replacing the the Foo.prototype to a new object which will break the prototype chain. Hence, only newly created objects beyond this will have bar as `3`

```js
/* 
Global Execution Context is `created` with following scope chain:
  - window
  - this => window
  - a,b,&c => undefined,
  - Foo => it points to `Foo` object which is also a function. This object has 
    a property `prototype` which point to another object that contain single
    property `bar` with value `1`.

After `creation` phase `execution` phase starts
   - `Foo Execution Context` is `created` with following scope chain:
        - arguments: { length: 0 }
        - this: points to new created object in it. This newly created object has
          `__proto__` property which points to same object that `Foo.prototype` 
          points to.

        - window
        - this => points to `window`
        - a,b,&c => undefined,
        - Foo => it points to `Foo` object which is also a function.

      After `creation` phase `execution` phase starts
        - newly created Object is returned.
      Finally, `Foo Execution Context` is destroyed.
    - `a` now points to return object from `Foo` execution.

 */ 
function Foo() {}
Foo.prototype.bar = 1;
const a = new Foo();
/* `a` is empty object, then it check `Foo.prototype` pointed object with help
of `__proto__` and finds `bar=1` and prints `1`. */
console.log(a.bar);

/* Value of bar in `Foo.prototype` is updated */
Foo.prototype.bar = 2;
/* same story repeats as in case of `a` */
const b = new Foo();
/* prints 2 in both cases */
console.log(a.bar);
console.log(b.bar);

/* Now `Foo.prototype` starts pointing to a completly new object */
Foo.prototype = { bar: 3 };
const c = new Foo();
/* However `__proto__` property of `a` and `b` is still pointing to the object
which was pointed by previous `prototype` of `Foo`. Thus `a.bar` & `b.bar` will 
print `2`.

When `c` was created then its `__proto__` proerty was pointing to new object pointed
by `Foo.prototype` so it will print `bar` value of this new object which is `3`.*/
console.log(a.bar);
console.log(b.bar);
console.log(c.bar);
```



## [49. `this` IV](https://bigfrontend.dev/quiz/this-4)

### 题目

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

### 答案

只有a.foo1()调用的是对象里的bar，其余this都指向window

```sh
1
10
2
3
```

### 详细解析

The [call() method](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) calls a function with a given `this` value and arguments provided individually.

When we don't specify `this`, it'll refer to the globalThis aka `window` in the browser's context.

1. `a.foot.call()` makes `this` point to the window.bar and hence 1 is returned (it also increments bar because of post-increment)
2. `a.foo1()` this time its a normal function call so `this` will refer to `a` and returns 10 (again increments it too)
3. `a.foo2.call` is similar to the first case, no argument is passed hence `this` is the window and it prints 2 (again incremented after returning)
4. In `a.foo2()`, inside the function body `foo` is executed independently losing context of `a`. Hence, it again prints global `bar` i.e. 3

```js
/* 
Global Execution Context is `created` with following scope chain:
  - window
  - this => window
  - bar,& a => undefined,
  - foo => it points to `foo` object which is also a function.

After `creation` phase `execution` phase starts
  - `bar` is assigned `1`.
  - object `a` is defined as `{bar: 10, foo1: [this point to same object that 
    global `foo` points ], foo2: [this points to object which contains defnition 
    of foo2]}`. 
 */
var bar = 1;

function foo() {
  return this.bar++;
}

const a = {
  bar: 10,
  foo1: foo,
  foo2: function () {
    return foo();
  },
};

/*
1. Every scope in JavaScript has a `this` object that 
represents the calling object for the function.

2. When a function is called while attached to an object, 
the value of this is equal to that object by default.

3. When you're inside a method, we can access the object 
the method belongs to using the special value `this`:
*/

/* `foo Execution Context` is `created` with following scope chain:
      - arguments: { length: 0 }
      - this: points to same object that global `a` points to.

      - window
      - this => points to `window`
      - bar=1, foo=>f(), a=> { bar: 10, foo1: fn(), foo2: fn() }

    After `creation` phase `execution` phase starts
      - object which is pointed by `foo1` is executed, 
      it returns `a.bar` and then increase it by one.

    Finally, `foo Execution Context` is destroyed.
*/
console.log(a.foo1()); // 10

/* when nothing passed then surrounding object is taken a invoking object, 
`window` in this case. 

`foo1 Execution Context` is `created` with following scope chain:
      - window
      - this => points to `window`
      - bar=1, foo=>f(), a=> { bar: 10, foo1: fn(), foo2: fn() }

    After `creation` phase `execution` phase starts
      - object which is pointed by `foo1` is executed, 
      it returns `window.bar` and then increase it by one.

    Finally, `foo1 Execution Context` is destroyed.*/
console.log(a.foo1.call()); //1

/* `foo2 Execution Context` is `created` with following scope chain:
      - window
      - this => points to `window`
      - bar=1, foo=>f(), a=> { bar: 10, foo1: fn(), foo2: fn() }

    After `creation` phase `execution` phase starts
      - `foo` is returned.

    Finally, `foo2 Execution Context` is destroyed.

    Next `foo Execution Context` is `created` with following scope chain:
      - window
      - this => points to `window`
      - bar=1, foo=>f(), a=> { bar: 10, foo1: fn(), foo2: fn() }

    After `creation` phase `execution` phase starts
      - object which is pointed by `foo` is executed, 
      it returns `window.bar` and then increase it by one.

    Finally, `foo Execution Context` is destroyed. */
console.log(a.foo2.call()); // 2

/* Same as last example */
console.log(a.foo2()); // 3
```



## [50. async await](https://bigfrontend.dev/quiz/async-await)

### 题目

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

### 答案

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

### 详细解析

An async function is a function declared with the [async keyword](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function), and the [await keyword](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await) is permitted within it. The async and await keywords enable asynchronous, promise-based behavior.

Key points to remember are-

- All synchronous code gets executed first
- The Promise object represents the eventual completion (or failure) of an asynchronous operation and its resulting value. Basically, it's a nonblocking block of code that executes when the call stack is empty
- The executor is called synchronously by the Promise constructor before even the Promise constructor returns the created object
- `Promises` create `Microtask` which has priority over the `Macrotask` created by `setTimeout`.
- If the value of the expression following the await operator is not a Promise, it's converted to a resolved Promise i.e. await will also trigger an item into microtask queue

```js
async function async1(){
  console.log(1) // 2️⃣
  await async2()
  console.log(2) // 6️⃣
}

async function async2(){
  console.log(3) //  3️⃣
}

console.log(4) // 1️⃣

setTimeout(function(){
  console.log(5) // 8️⃣
}, 0)

async1()

new Promise(function(resolve){
  console.log(6) // 4️⃣
  resolve()
}).then(function(){
  console.log(7) // 7️⃣
})

console.log(8) // 5️⃣

// 4
// 1
// 3
// 6
// 8
// 2
// 7
// 5
```

So all code execution can be explained in these steps-

- `4` gets printed as its synchronous statement
- `5` is a setTimeout get pushed to the Macrotask queue
- `async1` gets called and `1` gets printed, then `async2` is invoked and `3` gets printed. `2` gets queued to Microtask queue
- `6` gets printed as it's synchronous inside executer function of the promise while `7` gets queued to Microtask queue
- `8` gets printed being synchronous
- Now since, the call stack is empty. Microtask queue is checked first and `2` and `7` gets printed
- Lastly, queued setTimeout get executed i.e. `5` will get printed

Note that, this order may vary in browsers because of underlying implementations. For example, the same code in Safari prints `4,1,3,6,8,7,2,5`



## [51. method](https://bigfrontend.dev/quiz/async-await)

### 题目

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

### 答案

编译时报错，super只在方法中有效

```sh
Error
```

### 详细解析

In `obj1` we have defined an object method `foo`. However, in case of `obj2` we have defined a property `foo` which is a function. There's a subtle difference.

This code throws `Uncaught SyntaxError: 'super' keyword unexpected here` error because `super` is valid only inside methods and not normal property/function.

```js
// case 1
const obj1 = {
  foo() {
    console.log(super.foo());
  },
};

/* 

`obj1` is normal instance of `Object` and it doesn't have `prototype`, only
constructor function can have `prototype` property. Instance can only has `__proto__`
property which point to object pointed by `prototype` property of its constructor 
funciton.

Here. we are making `__proto__` property of `obj1` to a different object.

*/

Object.setPrototypeOf(obj1, {
  foo() {
    return "bar";
  },
});

/* 
`foo` method of `obj1` is invoked which invokes `foo` method of object pointed by
`__proto__` which result into printing `bar`
*/

obj1.foo(); // 'bar'

// case 2

const obj2 = {
  foo: function () {
    /* This will throw error because `super` can only be used in methods.
      What is method? It is a function which has [[HomeObject]] internal 
      property pointing to the object to which the method belongs.
      
    Any reference to super uses the [[HomeObject]] to determine what to do. 
    The first step in the process is to call Object.getPrototypeOf() on the 
    [[HomeObject]] to retrieve a reference to the prototype. Next, 
    the prototype is searched for a function with the same name. 
    Then, the this binding is set and the method is called.


    [[HomeObject]] property is created only when method is created using 
    ES2015 method styld. Since here normal style is used to declare `foo`,
    it doesn't have [[HomeObject]], thus it will give error
      */
    console.log(super.foo()); // error
  },
};

Object.setPrototypeOf(obj2, {
  foo() {
    return "bar";
  },
});

obj2.foo();

// Ans: Error. It will throw error during `excution creation phase` itself
```



## [52. requestAnimationFrame](https://bigfrontend.dev/quiz/requestanimationframe)

### 题目

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

### 答案

requestAnimationFrame里的 setTimeout在执行别的 setTimeout事件后重新执行

```sh
1
6
3
4
2
5
```

### 详细解析

[setTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout) method sets a timer which executes a function or specified piece of code once the timer expires. These tasks are queued to the `Macrotask Queue`

[requestAnimationFrame()](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) method tells the browser that you wish to call a specified function to update an animation before the next repaint. These tasks are queued to the `Animation Queue`

Since, JS is single-threaded, the main thread can only execute one statement at a time. The order in which this execution happens is—

1. First all synchronous tasks are queued
2. The micro task queue (promises) will execute immediately when the JS run stack is empty
3. The animation queue is executed
4. The priority of macro task queue is lower than that of micro task queue and animation queue

So here, first the synchronous statement `1` gets logged. Then setTimeout of `2` is added to macrotask queue. Then, RAF `3` is queued in the animation queue. Next, RAF `4` is queued to the animation queue and `5` is queued to the macrotask queue. Lastly, `6` is a synchronous statement so it gets printed second.

Now, since the main call stack is empty, animation queue tasks are executed (as they have higher priority over macrotask queue). Hence, order in which they get printed is 1 -> 6 -> 3 -> 4 -> 2 -> 5

```js
/* 
Order:
    -Statement
    - microtask, 
    - requestAnimationFrame, 
    - worker/port message
    - (macro)task. 
*/

console.log(1); // 1st

const mc = new MessageChannel();

mc.port1.onmessage = () => {
  console.log(200); //6th
};

setTimeout(() => {
  console.log(2);//7th
}, 100);

Promise.resolve().then(() => {
  console.log(300); // 3rd
});

requestAnimationFrame(() => {
/* Assuming 60fps => frame is repainted in every 16.6ms.
It will push its callback in queue after 16.6ms */
  console.log(3); // 4th
});

requestAnimationFrame(() => {
  console.log(4); // 5th
  setTimeout(() => {
    console.log(5); // 8th
  }, 10);
});

const end = Date.now() + 200;

// by the time this will over all timers would have completed
// their time and queued into task queue.
while (Date.now() < end) {} 

mc.port2.postMessage("");

console.log(6); // 2nd

// Answer: 1, 6, 300, 3, 4, 200, 2, 5
```



## [53. Prototype 2](https://bigfrontend.dev/quiz/prototype2)

### 题目

What does the code snippet to the right output by `console.log`?

```js
function F() {
  this.foo = 'bar'
}

const f = new F()
console.log(f.prototype)
```

### 答案

实例无prototype

```sh
undefined
```

### 详细解析

Functions like `F` have prototypes. Object instances, like `f`, don't.

The instance object, in your case `f`, has an internal reference to the prototype object. This reference is used internally to resolve properties and methods in the prototype chain. However it is not accessible directly, and thus we can't access `prototype` property of `f`.

REFER: https://stackoverflow.com/questions/14450731/why-is-javascript-prototype-property-undefined-on-new-objects

```js
// Only constructor functions have ".prototype".
function F() {
  this.foo = "bar";
}

/* when the new object is created, it gets its internal [[Prototype]] which cannot be 
directly accessed. However, an instance has the `__proto__` property which points to 
the object which is pointed by the `prototype` property of the constructor function. */
const f = new F();
console.log(f.prototype);

// result: undefined
```



## [54. setTimeout(0ms)](https://bigfrontend.dev/quiz/setTimeout-0ms)

### 题目

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

### 答案

实例无prototype

```sh
1
0
2
```

### 详细解析

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

// 1
// 0
// 2
```

`setTimeout()` method sets a timer that executes a function or specified piece of code once the timer expires. When this happens the handler gets pushed to a queue where they are executed one by one (if the call stack is empty)

Here, `2ms` is long enough for other timeouts to be pushed to the queue before it. However, `1ms` is not enough and is pushed earlier than even the following `0ms`.

Hence, `1` gets pushed first, then `0` and lastly `2`

This order is different in `Firefox` as the underlying implementation might be different. `0 -> 1 -> 2`



## [55. sparse array](https://bigfrontend.dev/quiz/sparse-array)

### 题目

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

### 答案

ES5的方法跳过空格，ES6保持空格，别的方法将空格转为undefined

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

### 详细解析

[Array.forEach()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach#no_operation_for_uninitialized_values_sparse_arrays) is not invoked for index properties that have been deleted or are uninitialized

Similarly, In [Array.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map#description) callback is invoked only for indexes of the array which have assigned values (including undefined). It is not called for missing elements of the array. But output arrays does contain the holes.

The [for...of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) statement creates a loop iterating over iterable objects like an Array. For a sparse array, the holes are treated as `undefined`

The [Spread Operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#spread_in_array_literals) (...) follows a similar behavior and expands an array such that holes are `undefined`

```js
const arr = [1, , , 2];
// forEach
arr.forEach((i) => console.log(i)); // 1,2

/* 
`forEach`, `map`, and `filter` don't loop throught holes but resulted array
still has those rules. e.g.

case 1:
console.log(arr.map((i,x) => {
    console.log(i,x) // printed logs: 1 0, 2 3
    return i * 2;
}));
 But returned array: [ 2, <2 empty items>, 4 ]


*/

// map
console.log(arr.map((i) => i * 2)); // [ 2, <2 empty items>, 4 ]

// for ... of
for (const i of arr) {
  console.log(i); // 1,undefined, undefined, 2
}

// spread
console.log([...arr]); // [ 1, undefined, undefined, 2 ]
```



## [56. to primitive](https://bigfrontend.dev/quiz/primitive)

### 题目

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

### 答案

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

### 详细解析

[valueOf()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/valueOf) method returns the primitive value of the specified object.

[Symbol.toPrimitive](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/toPrimitive) is a symbol that specifies a function valued property that is called to convert an object to a corresponding primitive value. The function is called with a string argument hint, which specifies the preferred type of the result primitive value. The hint argument can be one of "number", "string", and "default"

Let's break it down case by case

### Case 1

For `obj1`, we have defined `valueOf` and `toString`. While adding we use `valueOf` to get the primitive value and add 1 to it thus logging 2. `parseInt` will convert the input using `toString` method that gives '100' converting it to number gives 100

### Case 2

For `obj2`, we have defined `Symbol.toPrimitive`, `valueOf` and `toString` methods. In this example, both operations viz. addition and parseInt make use of `toPrimitive` method i.e. using 200

### Case 3

For `obj3`, we have only defined `toString`. All three operations here, Unary operator, Addition and parseInt will use `toString` method i.e. "100"

### Case 4

For `obj4`, we have only defined `valueOf`. So addition uses this value 1. But in parseInt, since we have not defined toString method, the string value of obj4 will be `[object Object]` hence parseInt returns `NaN`

### Case 5

For `obj5`, we have only defined Symbol.toPrimitive with custom handling for hints. For `Addition(+)`, the hint is implicitly `number`, which means toPrimitive returns 1 and 1+1=2. `parseInt` implicitly uses toPrimitive method with the hint passed as string and thus it uses "100" returning 100

```js
// case 1
const obj1 = {
  valueOf() {
    return 1;
  },
  toString() {
    return "100";
  },
};

console.log(obj1 + 1); // since `1` is number so `valueOf` is called => 1+1=2
console.log(parseInt(obj1)); // since `parseInt` takes string input, `toString` is called => 100


/* 
whenever an object is converted to primitive, `Symbol.toPrimitive` is used. It either calls
`toStrng` or `valueOf` based on `hint` (which are of three type:  number, string, default). We 
can define `Symbol.toPrimitive` ourselves also. e.g. 

// An object without Symbol.toPrimitive property.
var obj1 = {};
console.log(+obj1);     // NaN
console.log(`${obj1}`); // "[object Object]"
console.log(obj1 + ''); // "[object Object]"

// An object with Symbol.toPrimitive property.
var obj2 = {
  [Symbol.toPrimitive](hint) {
    if (hint == 'number') {
      return 10;
    }
    if (hint == 'string') {
      return 'hello';
    }
    return true;
  }
};
console.log(+obj2);     // 10        -- hint is "number"
console.log(`${obj2}`); // "hello"   -- hint is "string"
console.log(obj2 + ''); // "true"    -- hint is "default"
*/

// case 2
const obj2 = {
  [Symbol.toPrimitive]() {
    return 200;
  },

  valueOf() {
    return 1;
  },
  toString() {
    return "100";
  },
};

console.log(obj2 + 1);  // 201
console.log(parseInt(obj2)); // 200

// case 3
const obj3 = {
  toString() {
    return "100";
  },
};

console.log(+obj3); //100
console.log(obj3 + 1); //1001
console.log(parseInt(obj3)); //100

// case 4
const obj4 = {
  valueOf() {
    return 1;
  },
};

console.log(obj4 + 1); // 2

// parseInt take string as input, so `toString` was called. It returned "[object Object]"
// converting this string to number resulted into => NaN
console.log(parseInt(obj4));  // NaN

// case 5
const obj5 = {
  [Symbol.toPrimitive](hint) {
    return hint === "string" ? "100" : 1;
  },
};

console.log(obj5 + 1);  //2
console.log(parseInt(obj5)); // 100
```



## [57. non-writable](https://bigfrontend.dev/quiz/inherit-writable-flag)

### 题目

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

### 答案

Object.defineProperty劫持属性，不会被修改

```sh
1
1
1
2
```

### 详细解析

[Object.defineProperty()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) defines a new property directly on an object, or modifies an existing property on an object, and returns the object.

This takes in 3 parameters; the target object, nameof the property and descriptor. The `writable` descriptor describes if the value associated with the property can be changed with an assignment operator. **Defaults to false**

Here, when defining `foo1`, since the `writable` property is not defined explicitly it'll default to false. Meaning, that the value cannot be changed later on hence b.foo1 is locked at 1 and is immutable.

[Object.create()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create) method creates a new object, using an existing object as the prototype of the newly created object. Here, we have created a new object `b` using `a` as prototype

```js
/* 
Global execution context: `creation phase`
    - window: global object
    - this: window
    - a, b: undefined
Execution Phase:
    - a: { foo1: 1 }
    - b: points to a empty object, which has `__proto__` perperty which points to `a` object
*/
const a = {};
Object.defineProperty(a, "foo1", {
  value: 1,
});
const b = Object.create(a);
b.foo2 = 1;

console.log(b.foo1); // 1 (available throught `__proto__``)
console.log(b.foo2); // 1

b.foo1 = 2; // fails because it is non-writable property
b.foo2 = 2;

console.log(b.foo1); // 1
console.log(b.foo2); // 2
```



## [58. inherit getter setter](https://bigfrontend.dev/quiz/inherit-getter-setter)

### 题目

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

### 答案

B没有重写getter/setter，C重写getter/setter

```sh
0
1
1
1
1
```

### 详细解析

Here we have a global `val` and then three classes. `A` has both [setter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set) and [getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get) defined. `B` extends `A` without any overriding. `C` extends `A` with `get foo()` overridden.

Note that since we have not used `this` keyword while setting, it'll actually update the outer variable `val`

So, when we create a new object b, the value of val is 0 and hence b.foo returns 0. Then we do `b.foo = 1` which means val also gets updated to 1 and hence it prints 1 next.

Next we create a new object c, c.foo returns 1 (from above). However, `c.foo = 2` will not update val as there is no setter defined hence val remains 1 thus printing 1 for both `c.foo` and `b.foo` Apparently, when we override the get method, it appears that the set method must also be overridden, otherwise undefined is returned [See this](https://stackoverflow.com/questions/28950760/override-a-setter-and-the-getter-must-also-be-overridden)

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
console.log(b.foo) // 0
b.foo = 1 // val also gets updated to 1
console.log(b.foo) // 1

const c = new C()
console.log(c.foo) // 1
c.foo = 2
console.log(c.foo) // 1
console.log(b.foo) // 1
```



## [59. override setter](https://bigfrontend.dev/quiz/override-setter)

### 题目

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

### 答案

B重新修改val

```sh
1
undefined
3
undefined
```

#### 详细解析

Here, we have two classes defined. `A` has a property `val` initialized to 1 and a [getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get) `foo` defined. `B` also has a property `val` and a [setter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set) `foo`

Remember that, when we override the get method, it appears that the set method must also be overridden, otherwise undefined is returned and vice-versa [See this](https://stackoverflow.com/questions/28950760/override-a-setter-and-the-getter-must-also-be-overridden)

So, when we create two objects `a` and `b`; `a.foo` will return the value 1 as getter is defined but `b.foo` returns undefined.

Also, when we do `b.foo=3`, the setter works and val gets updated to 3. However, as there is no getter defined `b.foo` returns undefined. Note that, accessing `b.val` directly returns the new value 3 though

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
console.log(a.foo) // 1
console.log(b.foo) // undefined
b.foo = 3
console.log(b.val) // 3
console.log(b.foo) // undefined
```



## [60. postMessage](https://bigfrontend.dev/quiz/postMessage)

### 题目

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

### 答案

window.onmessage是微任务，优先级在promise之后

```sh
1
5
6
3
2
4
```

### 详细解析

[MessageChannel](https://developer.mozilla.org/en-US/docs/Web/API/MessageChannel) interface of the Channel Messaging API allows us to create a new message channel and send data through it

[Promise.resolve()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve) method returns a Promise object that is resolved with a given value.

[setTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout) method sets a timer which executes a function or specified piece of code once the timer expires. These tasks are queued to the `Macrotask Queue`

Since JS is single-threaded, the main thread can only execute one statement at a time. The order in which this execution happens is—

1. First all synchronous tasks are queued
2. The micro task queue (promises, worker threads, messaagechannel) will execute immediately when the JS run stack is empty
3. The priority of macro task queue is lower than that of micro task queue

So here, first the synchronous statement `1` gets logged. Then `onmessage` handler gets associated with messagechannel. `3` is a resolved promise so it's added to the microtask queue. setTimeout of `4` is added to macrotask queue. `5` is a synchronous statement so it gets printed second. Using postMessage we trigger the handler and `2` gets added to micro task queue. Lastly, `6` is a synchronous statement so it gets printed third.

Now, since the main call stack is empty, microtask queue tasks are executed (as they have higher priority over macrotask queue). Hence, order in which they get printed is 1 -> 5 -> 6 -> 3 -> 2 -> 4

```js
/* 
Order:
    - Statement
    - Promise/callbacks
    - Web worker (port/window.eventHander, etc..)
    - Settimeout
        */

console.log(1) // 1️⃣

window.onmessage = () => {
  console.log(2) // 5️⃣
}

Promise.resolve().then(() => {
  console.log(3) // 4️⃣
})

setTimeout(() => {
  console.log(4) // 6️⃣
}, 0)

console.log(5) // 2️⃣

window.postMessage('')

console.log(6) // 3️⃣

// 1
// 5
// 6
// 3
// 2
// 4
```



## [61. onClick](https://bigfrontend.dev/quiz/messsage-channel-is-async)

### 题目

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

### 答案

onClick是一个同步任务

```sh
1
5
2
6
3
4
```

### 详细解析

Key points to remember are-

1. Synchronous code (including event listeners) gets executed first in order.
2. The Promise object represents the eventual completion (or failure) of an asynchronous operation and its resulting value. Basically, it's a non-blocking block of code that executes when the call stack is empty
3. `Promises` create `Microtask` which has priority over the `Macrotask` created by `setTimeout`.

In this example, `1` gets printed, Promise `3` gets queued to Micro Stack, setTimeout `4` gets queued as Macro task. `5`, `2`, and `6` then get printed synchronously

Once the call stack is empty,`3` is printed. Once the Microtask queue is empty then `4` gets printed

```js
console.log(1) // 1️⃣

document.body.addEventListener('click', () => {
  console.log(2) // 3️⃣
})

Promise.resolve().then(() => {
  console.log(3) // 5️⃣
})

setTimeout(() => {
  console.log(4) // 6️⃣
}, 0)

console.log(5) // 2️⃣

document.body.click()

console.log(6) // 4️⃣

// 1
// 5
// 2
// 6
// 3
// 4
```



## [62. MessageChannel](https://bigfrontend.dev/quiz/message-channel)

### 题目

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

### 答案

MessageChannel是微任务，优先级在promise之后

```sh
1
5
6
3
2
4
```

### 详细解析

[setTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout) method sets a timer which executes a function or specified piece of code once the timer expires. These tasks are queued to the `Macrotask Queue`

[Promise.resolve()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve) method returns a Promise object that is resolved with a given value.

[MessageChannel](https://developer.mozilla.org/en-US/docs/Web/API/MessageChannel) interface of the Channel Messaging API allows us to create a new message channel and send data through it

Since JS is single-threaded, the main thread can only execute one statement at a time. The order in which this execution happens is—

1. First all synchronous tasks are queued
2. The micro task queue (promises, worker threads, messaagechannel) will execute immediately when the JS run stack is empty
3. The priority of macro task queue is lower than that of micro task queue

So here, first the synchronous statement `1` gets logged. Then `onmessage` handler gets associated with messagechannel. `3` is a resolved promise so it's added to the microtask queue. setTimeout of `4` is added to macrotask queue. `5` is a synchronous statement so it gets printed second. Using postMessage we trigger the handler and `2` gets added to micro task queue. Lastly, `6` is a synchronous statement so it gets printed third.

Now, since the main call stack is empty, microtask queue tasks are executed (as they have higher priority over macrotask queue). Hence, order in which they get printed is 1 -> 5 -> 6 -> 3 -> 2 -> 4

This requires understaning of the event loop.

console.log is immediately queued in the stack. promise callback is queued in a microtask queue. others like `setTimeout` and `onmessage callback` are queued in the task queue.

The stack determines execution order. The microtasks are pushed to the stack before the task queue.

So, in this example

```js
/* 
Order:
    - Statement
    - Promise/callbacks
    - Web worker (port/window.eventHander, etc..)
    - Settimeout
        */
console.log(1) // 1️⃣

const mc = new MessageChannel()

mc.port1.onmessage = () => {
  console.log(2) // 5️⃣
}

Promise.resolve().then(() => {
  console.log(3) // 4️⃣
})

setTimeout(() => {
  console.log(4) // 6️⃣
}, 0)

console.log(5) // 2️⃣

mc.port2.postMessage('')

console.log(6) // 3️⃣

// 1
// 5
// 6
// 3
// 2
// 4
```



## [63. in](https://bigfrontend.dev/quiz/in-coercion)

### 题目

What does the code snippet to the right output by `console.log`?

```js
const obj = {
  foo: 'bar'
}

console.log('foo' in obj)
console.log(['foo'] in obj)
```

### 答案

键转化为字符串

```sh
true
true
```

### 详细解析

> The in operator returns true if the specified property is in the specified object or its prototype chain

The property could be a string or symbol representing a property name or array index (non-symbols will be coerced to strings). So, `['foo']` gets coerced to `"foo"` which is present in `obj`

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/in

```js
const obj = {
  foo: 'bar',
};

console.log('foo' in obj);   // true
console.log(['foo'] in obj); // true - ['foo'] converts to string
```



## [64. reference type](https://bigfrontend.dev/quiz/reference-type)

### 题目

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

### 答案

第一二个调用了obj.msg，第三个obj.foo执行，this指向window

```sh
"BFE"
"BFE"
undefined
```

### 详细解析

In normal javascript functions, `this` keyword points to who is calling the function (dynamic binding). Thus, when we invoke a function as `obj.foo()` the `this` keyword points to `obj`.

Note that, `(obj.foo)()` is the same as `obj.foo()` and `this` again points to `obj`

`()` is a grouping operator and is evaluated before execution. Here the logical OR expression `||` returns the first truthy value i.e. `obj.foo` which is a plain function `foo({console.log(this.msg)}`. Later on, when we execute this function, it's just a function without any connection to `obj` and hence it points to `window` and `window.msg` is `undefined`

```js
const obj = {
  msg: "BFE",
  foo() {
    console.log(this.msg);
  },
  bar() {
    console.log("dev");
  },
};

/*  `foo` called as method of `obj` thus prints 'BFE' */
obj.foo(); // 'BFE'

/* Since there is no operator in parenthesis `()` thus it will return funciton
as it is therefore `(obj.foo)()===obj.foo()`. So it is same like above  */
(obj.foo)(); // 'BFE'

/* Here there are operators in parentheis, thus it will return `obj.foo` and change
it context environment from function to global. And there is no `msg` in
global environment, hence it will print `undefined` */
(obj.foo || obj.bar)(); // undefined
```



## [65. Function name](https://bigfrontend.dev/quiz/Function-name)

### 题目

What does the code snippet to the right output by `console.log`?

```js
var foo = function bar(){ 
  return 'BFE'; 
};

console.log(foo());
console.log(bar());
```

### 答案

明明函数可以直接调用，函数名无法声明

```sh
"BFE"
Error
```

### 详细解析

Here, because the name `bar` is used within a function expression, it doesn't get declared in the outer scope. With named function expressions, the name of the function expression is enclosed within its own scope.

That's why `foo()` return "BFE", while `bar()` throws a `ReferenceError`

```js
var foo = function bar() {
  return "BFE";
};
/*  In `named function expression`, identifier ( in this case `bar`) is
only available in the function body. 

`bar` is never declared outside of the function body. Even if we do `console.log(bar)` 
outside of the function, it will throw `ReferenceError`
*/
console.log(foo()); // "BFE"
console.log(bar()); // Error
```



## [66. comma](https://bigfrontend.dev/quiz/comma)

### 题目

What does the code snippet to the right output by `console.log`?

```js
var obj = {
  a: "BFE",
  b: "dev",
  func: (function foo(){ return this.a; }, function bar(){ return this.b; })
}

console.log(obj.func())
```

### 答案

 common operator evaluates from left to right and returns the last(right most) operand, so func is assined with bar()

```sh
"dev"
```

### 详细解析

The [comma operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comma_Operator) (,) evaluates each of its operands (from left to right) and returns the value of the last operand.

This means, `func` is actually assigned the function `bar` as it is the last operand; and when invoked it prints `this.b` i.e. `"dev"`

```js
var obj = {
  a: "BFE",
  b: "dev",
  /* Comma operator returns last value. Thus, here `func=bar()`, which will
  print `dev` */
  func:
    (function foo() {
      return this.a;
    },
    function bar() {
      return this.b;
    }),
};

console.log(obj.func());
// comma operator evaluates from left to right and returns the last(right most) operand, so func is assined with bar()
```



## [67. if](https://bigfrontend.dev/quiz/if)

### 题目

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

### 答案

foo是函数，bar是undefined

```sh
"BFE"
Error
```

### 详细解析

Function declarations are not block scoped. This means a function declared in a block will leak outside it. If and when Javascript actually executes the block and creates the function for you.

In the first `if`, JS will actually create the function `foo` that is available outside the block too. When we are declaring the function inside the `if` block, with a `false` condition, JS won’t execute the if block hence won’t create the function `bar`.

So, you get an error while calling the function `bar`.

However, this [behavior may be inconsistent](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function#conditionally_created_functions)

```js
/* 
When functions are declared inside the if-else block then their name is hoisted.
However, their definition is attached only in the if-else block.

If we write on first-line  `console.log(foo, bar)` then it will print `undefined undefined`,
but `foo` gets defined in the first `if` block but not `bar`. Thus, invoking `foo` will 
print `BFE` whereas invoking `bar` which is `undefined` will throw TypeError.
*/
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

foo(); // 'BFE'
bar(); // Error
```



## [68. if II](https://bigfrontend.dev/quiz/if-II)

### 题目

What does the code snippet to the right output by `console.log`?

```js
if (function foo(){ console.log('BFE') }) {
  console.log('dev')
}
foo()
```

### 答案

foo是undefined，函数转化为bool进行判断

```sh
"dev"
Error
```

### 详细解析

Here, because we are using a function inside an `if` it's supposed to be evaluated as a boolean. Boolean(any function) is always `true`. So, if block gets through and `"dev"` gets printed.

In simple terms, `foo` gets evaluated and it exists for that evaluation step only. Unlike other normal function declarations `foo` will not get hoisted and will not be available to execute anywhere (not even inside that if block)

Trying to run `foo` after will give `ReferenceError: foo is not defined`

```js
if (function foo(){ console.log('BFE') }) {
  console.log('dev') // "dev"  => 'if' convert statement to Boolean, Boolean of function is true. 
}
foo() // Error  => in 'if' condition foo() was function expression not function declaration. so foo is not hoisted, so foo is not defined. calling the function which is not defined throws error

// "dev"
// Error
```



## [69. undefined](https://bigfrontend.dev/quiz/undefined)

### 题目

What does the code snippet to the right output by `console.log`?

```js
function foo(a, b, undefined, undefined) {
  console.log('BFE.dev')
}
console.log(foo.length)
```

### 答案

函数参数的个数

```sh
4
```

### 详细解析

[length](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/length) is a property of a function object, and indicates how many arguments the function expects, i.e. the number of formal parameters. In this case, `foo` has 4 parameters hence it logs 4

Note that we are just logging the `foo.length` and not invoking `foo`

```javascript
function foo(a, b, undefined, undefined) {
  console.log('BFE.dev')
}
console.log(foo.length) // 4
```

Reason:

length method when used will function returns the number of parameters - (a, b, undefined, undefined)

Total parameter = 4

```javascript
console.log('BFE.dev') 
//this line will not exceute since we never called the function foo
```



## [70. function](https://bigfrontend.dev/quiz/function)

### 题目

What does the code snippet to the right output by `console.log`?

```js
function foo(){ console.log(1) }
var foo = 2
function foo(){ console.log(3) }
foo()
```

### 答案

最后foo=2

```sh
Error
```

### 详细解析

[Hoisting](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting) is the process whereby the interpreter appears to move the declaration of functions, and variables to the top of their scope, prior to execution of the code.

JavaScript only hoists declarations, not initializations! However, with function declarations, the entire function gets moved to the top.

So, effectively the above code becomes —

```js
function foo(){ console.log(1) }
function foo(){ console.log(3) }
foo = 2;
foo(); // trying to invoke a number throws error
```

Reason:

Line 1: Hoisting will take place, Function declaration will be hoisted and in the global object foo will be equal to a function.

Line 2: foo variable will be hoisted. Now since foo keyword is already present in the global object as a function it will get replaced by foo variable (foo=2).

Why did this happen?

Because variable assignment has more importance than function declaration (when the name of variable and function are the same)

Order of Precedence: variable Assigment > function declaration > variable declaration

Line 3: It will have no effect due to the above-mentioned rule.

Line 4: Since foo is not a function it will throw an error.



## [71. two-way generator](https://bigfrontend.dev/quiz/generator-2-way)

### 题目

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

### 答案

第一次返回的结果为1，打印100，第二次打印2*1=2

```sh
100
2
undefined
```

### 详细解析

With a [generator function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator), values are not evaluated until they are needed. Therefore a generator allows us to define a potentially infinite data structure.

The [next()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/next) method returns an object with two properties done and value. You can also provide a parameter to the next method to send a value to the generator. The value will be assigned as a result of a yield expression.

1. Because `Grouping Operator` gets evaluated first, the generator function `gen` yields/returns 100
2. Since we have passed `1` to the next() method. `yield 100` is basically replaced with 1 and it returns 2*1 = 2
3. Since generator is completed it returns `undefined`

The equivalent syntax of the gen() method is this:

```js
function* gen() {
   yield (2 * (yield 100))
}
```

to resolve the outer yield, the inner yield need to resolve first in order to calculate the entire express similar to when we say

```js
return 3 * getValue();
```

Where we need to call and resolve getValue before we get the result.

```js
function* gen() {
  yield 2 * (yield 100)
}


const generator = gen()
console.log(generator.next().value)  // inner yield rersolves first: 100
console.log(generator.next(1).value) // repalce yield 100 with 1 and execute yield 2*1: 2
console.log(generator.next(1).value) // gen status is done at this point: undefined
```

类似的题目

```js
function *foo(x) {
    var y = 2 * (yield (x + 1));
    var z = yield (y / 3);
    return (x + y + z);
}

var it = foo( 5 );

console.log( it.next() );      // get x+1=6
console.log( it.next( 3 ) );   // turn "yield(x+1)" into 3, so y=6, so for the next yield, we get y/3=2
console.log( it.next( 88 ) );  // turn "yield (y / 3)" into 88, so z=88, at this point, there's no "yield" left in the code, done:true, but we have a return, we return x+y+z=5+6+88=99
console.log( it.next() );  // there's no "yield" left in the code, we get undefined, done: true
```



## [72. Array length](https://bigfrontend.dev/quiz/array-length)

### 题目

What does the code snippet to the right output by `console.log`?

```js
// This is a JavaScript Quiz from BFE.dev

class MyArray extends Array {
  get length() {
    return 3
  }
}

const arr1 = new MyArray(10)
console.log(arr1.length)

const arr2 = new Array(10)
console.log(arr2.length)
```

### 答案

```sh
10
10
```

### 详细解析

Subclasses cannot override parentClass properties. This is by design.

[Explaination](https://github.com/Microsoft/TypeScript/issues/338#issuecomment-51093979)

[Workaround](https://github.com/Microsoft/TypeScript/issues/338#issuecomment-99180950)

The parent's `length` is an instance property, and it overshadows the child class's `length` property, which is part of the instance's prototype. Basically, you will not be able to override the parent class's properties.

I think this behavior might be because variable overriding might break methods inherited from the Parent if we change its type in the Child class.

```js
class MyArray extends Array {
  get length() {
    return 3 // this will not override parent's length
  }
}

const arr1 = new MyArray(10)
console.log(arr1.length) // 10

const arr2 = new Array(10)
console.log(arr2.length) // 10
```



## [73. window name](https://bigfrontend.dev/quiz/window-name)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev

var a = 1;
(function() {
  console.log(a + this.a);
  var a = '2'
  console.log(a + this.a);
})();

var name = 1;
(function() {
  console.log(name + this.name);
  var name = '2'
  console.log(name + this.name);
})();
```

### 答案

```sh
NaN
"21"
"undefined1"
"21"
```

### 详细解析

The [Window.name](https://developer.mozilla.org/en-US/docs/Web/API/Window/name) property gets/sets the name of the window's browsing context.

`name` is a special case such that when you are using the variable named `name` in the global scope it is treated as `window.name` In Google Chrome, window.name gets the value as a string.

```js
var name = {}; // this is converted using toString() 
console.log(name) // [object Object] 
```

In each of these functions, the inner variable gets hoisted inside the function and hence its value is `undefined`. However, both `this.a` and `this.name` refer to global variables.

```js
var a = 1;
(function () {
  // 'var a = 2' is hoisted here -> var a = undefined
  // 'this.a' is number -> this.a = 1 (undefined + number -> NaN)
  console.log(a + this.a); // NaN
  var a = "2";
  console.log(a + this.a); // "21"
})();

var name = 1;
(function () {
  // 'var name = 2' is hoisted here -> var name = undefined
  /* Any global variable named `name` overwrites `window.name` pproperty.
  Also, before overwriting, value of `name` variable is passed to `String`
  to convert it to string and then it is overwritten. Since `symbol` can't be converted to 
  string thus if we declare global variable `name` as symbol then while converting it 
  to `string`, TypeError will be thrown.
   */
  // 'this.name' is string -> this.name = "1" (undefined + string -> "undefinedString")
  console.log(name + this.name); // "undefined1"
  var name = "2";
  console.log(name + this.name); "21"
})();
```



## [74. Typed Array length](https://bigfrontend.dev/quiz/Typed-Array-length)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

class MyArray extends Uint8Array {
  get length() {
    return 3
  }
}

const arr1 = new MyArray(10)
console.log(arr1.length)

const arr2 = new Uint8Array(10)
console.log(arr2.length)
```

### 答案

```sh
3
10
```

### 详细解析

This is quite similar to [this previous problem](https://bigfrontend.dev/quiz/array-length) but the difference being we are extending from [Uint8Array typed array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) that uses [ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) and in this case **Subclass constructors may over-ride it to change the constructor assignment** [See this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer/@@species#:~:text=Subclass constructors may over-ride it to change the constructor assignment.)

```js
class MyArray extends Uint8Array {
  get length() {
    return 3
  }
}

const arr1 = new MyArray(10)
console.log(arr1.length) // 3 MyArray's length is 3


const arr2 = new Uint8Array(10)
console.log(arr2.length) // Original Unit8Array will take in the arg
```



## [75. meaningless calculation](https://bigfrontend.dev/quiz/meaningless-calculation)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

const num = +((~~!+[])+(~~!+[])+[]+(~~!+[]))
console.log(num)
```

### 答案

```sh
21
```

### 详细解析

Few things to keep in mind,

The [unary plus operator (+)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Unary_plus) precedes its operand attempts to convert it into a number, if it isn't already.

The [bitwise NOT operator (~)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_NOT#description) inverts the bits of its operand.

So basically,

```js
+[] // 0 since Number([]) = 0
!0 // true since 0 is falsy
~true // -2
~-2 // 1

// another way to think will be is that ~~ cancel out each other and Number(true) is 1

// If we use all this,
(~~!+[]) // 1
```

In the case of unary operators, evaluation happens from Right to Left. Of course, the Grouping operator () is evaluated first. But the overall evaluation will happen from Left to Right. See [Operator Precedence](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence#table)

Let's break this down,

```js
const num = +((~~!+[])+(~~!+[])+[]+(~~!+[]))

// We know that (~~!+[]) = 1
// num = +(1 + 1 + [] + 1)
// num = +(2 + [] + 1)
// since not all operands are number JS performs string concatenation, String([]) = ""
// num = +('2' + '' + '1') = +("21") = 21

console.log(num) // 21
```



## [76. const](https://bigfrontend.dev/quiz/const)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

function func() {
  const a = b = c = 1
}
func()
console.log(typeof a, typeof b, typeof c)
```

### 答案

```sh
"undefined","number","number"
```

### 详细解析

In javascript, the assignment happens from right to left. So, `const` statement is only applicable to `a`, not `b` and `c`. All the other variables are considered Global without the var/let/const identifier, hence `b` and `c` will be globally scoped.

To visualize, think of it like

```js
const a = (b = (c = 1));
// which effectively becomes
const a = (window.b = ( window.c = 1))
```

that's why `a` is unavailable outside the function block, while `b` and `c` are 1; Hence, the output `undefined, number, number`

See https://stackoverflow.com/questions/41551270/is-there-a-way-to-do-multiple-assignment-and-initialization-with-block-scope for possible workarounds

```js
/* 
const a=1, b=4,c=6; This defines all 3 variables with `const`.

const a=b=c=3; This defines `b` & `c` as global variable with value 3, whereas
`a` as block scoped variable with value 3.

const a = (b=c=1,4); `b` & `c` global variable with value `1` & `a` block variable
with value 4.

const a = (b=c=1); `b` & `c` global variable with value `1` & `a` block variable
with value `undefined`.

*/

function func() {
  const a = (b = c = 1);
}
func();
console.log(typeof a, typeof b, typeof c); // undefined, number, number
```



## [77. parseInt 2](https://bigfrontend.dev/quiz/parseInt-2)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

console.log(parseInt(0.00001))
console.log(parseInt(0.000001))
console.log(parseInt(0.0000001))
console.log(parseInt("0x12"))
console.log(parseInt("1e2"))
```

### 答案

```sh
0
0
1
18
1
```

### 详细解析

Before converting a string to a number `parseInt` first converts a number to a string. If the number >= 1e+21 or number <= 1e-7 , it's represented in its scientific notation.

`1. & 2.` are straightforward, the integer part is 0

`3.` Since the number is equal to 1e-7, 0.0000001 gets converted to its scientific notation 1e-7. Now, as soon as parseInt encounters a character that is not a numeral in the specified radix, it ignores it and all succeeding characters and returns the integer value parsed up to that point

```js
// 0.0000001.toString() becomes 1e-7
parseInt(0.0000001) // becomes
parseInt(1e-7) // so e-7 gets ignored and it returns only 1
```

`4.` If the input string begins with "0x" or "0X" (a zero, followed by lowercase or uppercase X), radix is assumed to be 16 and the rest of the string is parsed as a hexadecimal number. `0x12` in HEX is 18 in decimal

`5.` Similar to 3. it ignores `e2` and returns 1

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/parseInt

```js
/* `parseInt` operate on string. Thus, it call casting function `String`
and pass its argument to it. 

Note: If number has more than 7 decimal digits then `String` convert it to
exponential. e.g.
    - String(0.000001) => '0.000001'
    - String(0.0000001) => '1e-7'
*/

console.log(parseInt(0.00001)); // String(0.00001)=>'0.00001' => 0
console.log(parseInt(0.000001)); // String(0.000001) => '0.000001' => 0 
console.log(parseInt(0.0000001)); // String(0.0000001) => '1e-7' => 1
console.log(parseInt("0x12",16)); // 18 (parseInt output is always in decimal. )

// 1 (parseInt can't understand if it is exponential, it will stop at first alphabhet)
// assume parsing `1z4` so what will be output of parseInt? Again 1.
console.log(parseInt("1e2")); // 1
```



## [78. RegExp](https://bigfrontend.dev/quiz/RegExp)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

const arr = ['a', 'b', 'c', '1']
const regExp = /^[a-z]$/gi
const chars = arr.filter(elem => regExp.test(elem))
console.log(chars)
```

### 答案

```sh
["a","c"]
```

### 详细解析

Intuitively, it seems that output should be `["a","b","c"]` since these items match the regular expression.

But when a regex has the [global flag](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test#using_test_on_a_regex_with_the_global_flag) set, `test()` will advance the lastIndex of the regex. [lastIndex](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/lastIndex) is a property of RegExp that specifies the index at which to start the next match.

Basically,

1. As long as `test()` returns `true`, lastIndex will not reset—even when testing a different string!
2. When `test()` returns `false`, the calling regex's lastIndex property will reset to 0.

> `/^[a-z]$/gi` 👉🏻 Any character from character set [a-z], `g` means global flag for multiple matches, `i` means character insensitive same as [a-zA-Z]

So the loop effectively becomes,

```js
regExp.test('a') // true and it sets lastIndex = 1
regExp.test('b') // false as the lastIndex i.e. staring point is not 0, lastIndex resets
regExp.test('c') // true as lastIndex is 0 and regex satisfies
regExp.test('1') // false 
```

So

```js
/* When a regex has the global flag set, test() will advance the lastIndex of 
the regex. Further calls to test(str) will resume searching str starting from 
lastIndex. The lastIndex property will continue to increase each time test() 
returns true.

As long as test() returns true, lastIndex will not reset—even when testing a 
different string!

const regex = /foo/g; // the "global" flag is set

// regex.lastIndex is at 0
regex.test('foo')     // true

// regex.lastIndex is now at 3
regex.test('foo')     // false

// regex.lastIndex is at 0
regex.test('barfoo')  // true

// regex.lastIndex is at 6
regex.test('foobar')  //false

// regex.lastIndex is at 0
// (...and so on)


"a" -> true, increase index
"b" -> false, reset index
"c" -> true, increase index
"1" -> false, reset index
*/

const arr = ["a", "b", "c", "1"];
const regExp = /^[a-z]$/gi;
const chars = arr.filter((elem) => regExp.test(elem)); 
console.log(chars); // ["a", "c"]
```



## [79. Equal III](https://bigfrontend.dev/quiz/equal-iii)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

console.log(2.0 == "2" == new Boolean(true) == "1")
```

### 答案

```sh
true
```

### 详细解析

Here, it'll convert the string and boolean values to number and then compare

```js
Number(2.0) // 2
Number("2") // 2
Number(true) // 1
```

As per [Operator Precedence](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence#table), [equality operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Equality) `==` attempts to convert and compare operands that are of different types and comparisons happen from Left to Right.

```js
console.log(2.0 == "2" == new Boolean(true) == "1") // true

// 2 == 2 == true == "1"
// true == true == "1"
// true == "1"
// 1 == 1
// true
```



## [80. Proxy I](https://bigfrontend.dev/quiz/proxy-i)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

const obj = new Map()
const map = new Map()
obj.foo = 1
map.set('foo', 2)
console.log(obj.foo)
console.log(map.get('foo'))

const proxyObj = new Proxy(obj, {})
const proxyMap = new Proxy(map, {})
console.log(proxyObj.foo)
console.log(proxyMap.get('foo'))


```

### 答案

```sh
1
2
1
Error
```

### 详细解析

The [Proxy object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy#description) allows you to create an object that can be used in place of the original object, but which may redefine fundamental Object operations like getting, setting, and defining properties.

If a `handler` has not been defined, the default behavior is to forward the operation to the target, however that only applies to standard behaviors like property access, and not the internal slots of exotic objects.

```js
const obj = new Map();
const map = new Map();
obj.foo = 1;
map.set("foo", 2);
console.log(obj.foo); // 1
console.log(map.get("foo")); // 2

const proxyObj = new Proxy(obj, {});
const proxyMap = new Proxy(map, {});
console.log(proxyObj.foo); // 1


/* 
Many built-in objects, for example, Map, Set, Date, Promise and others make use 
of so-called internal slots. These are like properties but reserved for internal, 
specification-only purposes. For instance, Map stores items in the internal slot 
[[MapData]]. Built-in methods access them directly, not via [[Get]]/[[Set]] 
internal methods. So Proxy can’t intercept that. e.g.

let map = new Map();
let proxy = new Proxy(map, {});
proxy.set('name', 'Pravin'); // Error
 */
console.log(proxyMap.get("foo")); // TypeError
```

⚠️ Details are pretty technical 🤯

If the `target` does not have a [[[MapData\]] internal slot](https://262.ecma-international.org/6.0/#sec-map.prototype.get), throw a TypeError exception. This is also explained [here](https://262.ecma-international.org/6.0/#sec-proxy-object-internal-methods-and-internal-slots)

Similar behavior in ES6 `Set` is explained at https://stackoverflow.com/questions/43927933/why-is-set-incompatible-with-proxy



## [81. setTimeout II](https://bigfrontend.dev/quiz/setTimeout-2)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

let num

for (let i = 0; i < 5; i++) {
  num = i
  setTimeout(() => {
    console.log(num)
  }, 100)
}
```

### 答案

```sh
4
4
4
4
4
```

### 详细解析

Here, `num` behaves like `var` The variable `num` is actually declared within the for loop and the setTimeout's inner function accesses it. So when the for loop is done running, each of the inner functions refers to the same variable `num`, which at the end of the loop is equal to 4

```js
let num

for (let i = 0; i < 5; i++) {
  num = i
  setTimeout(() => {
    console.log(num) // 4 => five times (it is same as using var because here `num` has global scope)
  }, 100)
}

// 4
// 4
// 4
// 4
// 4
```



## [82. Proxy II](https://bigfrontend.dev/quiz/Proxy-II)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

class Dev {
  #name
  constructor(name) {
    this.#name = name
  }
  get name() {
    return this.#name;
  }
}

const dev = new Dev('BFE')
console.log(dev.name)

const proxyDev = new Proxy(dev, {})
console.log(proxyDev.name)
```

### 答案

```sh
"BFE"
Error
```

### 详细解析

[Private class members](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Private_class_fields) can be created by using a hash # prefix. Private fields are accessible on the class constructor from inside the class declaration itself and is not accessible from the derived Subclass.

A proxy cannot read private member `#name` from the `dev` object.

```js
class Dev {
  #name
  constructor(name) {
    this.#name = name
  }
  get name() {
    return this.#name;
  }
}

const dev = new Dev('BFE')
console.log(dev.name) // "BFE"

const proxyDev = new Proxy(dev, {})
console.log(proxyDev.name) // TypeError: can't read private property
```



## [83. Plus Plus](https://bigfrontend.dev/quiz/Plus-Plus)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

console.log(1 + 1)
console.log(1 + + 1)
console.log(1 + + 1 + 1)
console.log(1 + + 1 + + 1)
console.log(1 + + + 1)

console.log(1 + + '1' + + '1')
console.log('1' + + '1' + + '1')
console.log('a' + + 'b')
console.log('a' + + 'b' + 'c')
console.log('a' + + 'b' + + 'c')
```

### 答案

```sh
2
2
3
3
2
3
"111"
"aNaN"
"aNaNc"
"aNaNNaN"
```

### 详细解析

> The unary plus operator (+) precedes its operand and attempts to convert it into a `number` if it isn't already. If it's not possible it returns `NaN`

In mathematical operators, `+` works on both numbers and strings (used in string concatenation). Hence, if any of the operands is not a number, using `+` converts all operand/s to string and concatenates.

The unary operator has higher precedence over the addition operator.

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence

```js
console.log(1 + 1) // 2
console.log(1 + + 1) // 1 + (+1) = 1 + 1 = 2
console.log(1 + + 1 + 1) // 1 + (+1) + 1 = 1 + 1 + 1 = 3
console.log(1 + + 1 + + 1) // 1 + (+1) + (+1) =  1 + 1 + 1 = 3
console.log(1 + + + 1) // 1 + (+(+1)) = 1 + (+1) = 1 + 1 = 2

console.log(1 + + '1' + + '1') // 1 + (+'1') + (+'1') = 1 + 1 + 1 = 3
console.log('1' + + '1' + + '1') // "1" + (+'1') + (+'1') = "1" + 1 + 1 = "1" + "1" + "1" = "111"
console.log('a' + + 'b') // "a" + (+'b') = a + "NaN" = "aNaN"
console.log('a' + + 'b' + 'c') // "a" + (+'b') +'c' = a + "NaN"  + "c" = "aNaNc"
console.log('a' + + 'b' + + 'c') // "a" + (+'b') + (+'c') = a + "NaN"  + "NaN" = "aNaNNaN"
```



## [84. Array.prototype.sort()](https://bigfrontend.dev/quiz/Array-prototype-sort)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 



const a = [999, 1111, 111, 2, 0] 
const b = a.sort()

console.log(a)
console.log(b)
```

### 答案

```sh
2
2
3
3
2
3
"111"
"aNaN"
"aNaNc"
"aNaNNaN"
```

### 详细解析

`Array.sort()` method sorts the elements of an array in place and returns the sorted array

> Note that the array is sorted in place, and no copy is made.

This explains why both `a` and `b` get sorted. `a` is sorted in place while this sorted array is then assigned to `b`.

As to why numbers don't appear in order, remember that sort expects a compare function that defines the sort order. If omitted, the array elements are converted to strings, then sorted according to each character's Unicode code point value.

When compared in strings, `"111" < "2"`, `"1111" < "999"`, etc.

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort

```js
// In global excution environment `a` variable created and it points to an array object
const a = [999, 1111, 111, 2, 0];

// `a` pointed array object is sorted. Further its location is also stored in `b`.
// Now, `a` & `b` both point to same object.
const b = a.sort();

// both prints same object
console.log(a);  // [ 0, 111, 1111, 2, 999 ]
console.log(b);  // [ 0, 111, 1111, 2, 999 ]
```



## [85. String.raw()](https://bigfrontend.dev/quiz/String-raw)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 


console.log(String.raw`BFE\n.${'dev'}`)
console.log(String.raw({raw: 'BFE'}, 'd', 'e','v'));

// when you enter your input in text box below
// keep in mind it is treated as raw string and will be escaped
```

### 答案

```sh
"BFE\n.dev"
"BdFeE"
```

### 详细解析

```js
/* static `String.raw()` method is a tag function of template literals. Two
types of argument can be passed to it
  - String.raw(callSite, ...substitutions)
  - String.raw`templateString`  
*/

/*
It's used to get the raw string form of template literals, that is, 
substitutions (e.g. ${foo}) are processed, but escapes (e.g. \n) are not */
console.log(String.raw`BFE\n.${"dev"}`); // BFE\n.dev


/* here it is used in form of `String.raw(callSite, ...substitutions)`
thus each of substitute arguments will be placed between callSite letters. */
console.log(String.raw({ raw: "BFE" }, "d", "e", "v")); // BdFeV (we can't place `v` because there is not space available.)
```

The static [String.raw()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/raw#description) method is a tag function of template literals. It's used to get the raw string form of template literals, that is, substitutions (e.g. ${foo}) are processed, but escapes (e.g. \n) are not. Note that it works just like the default template function and performs string concatenation. The important difference is that it'll not escape characters.

Example,

```js
console.log("BFE\ndev") // \n gets escaped
// BFE
// dev

console.log(`BFE\ndev`) // \n gets escaped
// BFE
// dev

console.log(String.raw`BFE\ndev`) // \n remains as it is
// BFE\ndev
```

`String.raw()` works like an interweaving function. The first argument is an object with a `raw` property whose value is an iterable(could be a string or an array) representing the separated strings in the template literal. The rest of the arguments are the substitutions. Extra substitutions are ignored

Example,

```js
// using array
String.raw({ raw: [0,2,4] }, 1, 3, 5, 6, 7) // "01234"

// using string
String.raw({ raw: '024' }, 1, 3, 5, 6, 7) // "01234"
```



## [86. setTimeout III](https://bigfrontend.dev/quiz/setTimeout-III)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 



let func = () => {
  console.log(1)
}
setTimeout(() => {
  func = () => {
    console.log(2)
  }
}, 0)

setTimeout(func, 100)
```

### 答案

```sh
1
```

### 详细解析

[setTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout) method sets a timer that executes a function or specified piece of code once the timer expires.

Here, the `func` is changed inside the first setimeout but since this is part of the callback it will not execute until the first setTimeout is invoked. That is why, the original `func` is passed as a reference to the second timeout which eventually gets invoked after 100ms logging 1

```js
let func = () => {
  console.log(1);
};
//1. this is queued in task queue
//3. `func` starts pointing to new callable object
setTimeout(() => {
  func = () => {
    console.log(2);
  };
}, 0);

//2. this is queued in task queue
//4. callback definition of this function doesn't change. It remain 
// same whatever was passed initially. Thus it prints `1`
setTimeout(func, 100);
```



## [87. instanceOf 2](https://bigfrontend.dev/quiz/instanceOf-2)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 


console.log(Function instanceof Object)
console.log(Object instanceof Function)
console.log(Function instanceof Function)
console.log(Object instanceof Object)
```

### 答案

```sh
true
true
true
true
```

### 详细解析

> The [instanceof](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof) operator tests to see if the prototype property of a constructor appears anywhere in the prototype chain of an object. The return value is a boolean value.

Simply put,

```js
console.log(Function instanceof Object) // true
// Object.getPrototypeOf(Function) is not equal to Object.prototype
// it goes further in chain
// Object.getPrototypeOf(Object.getPrototypeOf((Function)) is equal to Object.prototype

console.log(Object instanceof Function) // true
// Object.getPrototypeOf(Object) is equal to Function.prototype
```

See more insightful answers on this thread https://stackoverflow.com/questions/23622695/why-in-javascript-both-object-instanceof-function-and-function-instanceof-obj



## [88. try...catch](https://bigfrontend.dev/quiz/try-catch)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

var a = 'a'
try {
  throw new Error('BFE.dev')
} catch {
  var a = 'a1'
}
console.log(a)

var b = 'b'
try {
  throw new Error('BFE.dev')
} catch (b) {
  var b = 'b1'
}
console.log(b)

var c = 'c'
try {
  throw new Error('BFE.dev')
} catch (error) {
  var c = 'c1'
}
console.log(c)
```

### 答案

```sh
"a1"
"b"
"c1"
```

### 详细解析

The [try...catch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch#description) statement marks a `try` block and a `catch` block. If the code in the `try` block throws an exception then the code in the `catch` block will be executed. In all these examples, we are throwing an exception in `try` which is why the control immediately goes to the `catch` block.

The important thing to know is that the catch-block specifies an identifier that holds the value of the exception; **this value is only available in the scope of the catch-block**

```js
var a = 'a'
try {
  throw new Error('BFE.dev')
} catch { // no local variable being used
  var a = 'a1' // overwrites outer varibale a, redeclaring global a
}
console.log(a) // a1

var b = 'b'
try {
  throw new Error('BFE.dev')
} catch (b) { // local variable b references the passed error
  var b = 'b1' // No longer pointing to the global variable, its a locally scoped variable only
}
console.log(b) // b

var c = 'c'
try {
  throw new Error('BFE.dev')
} catch (error) { // local variable error references the passed error
  var c = 'c1' // overwrites outer variable c, redeclaring global c
}
console.log(c) // c1
```

1. We have just written `catch` and are not even capturing the error parameter so `var a` declared inside actually becomes global and overwrites outer `a` hence printing `a1` later
2. We have written `catch(b)` which means we are using `b` to hold the exception value which is only available locally inside this catch block. Hence, even after declaring `b` inside, the global value remains unchanged. Thus, printing `b`
3. We have written `catch(error)` and are using `error` variable to capture the error value , so `error` is a locally scoped variable but `c` is not. Hence, similar to 1. `var c` declared inside actually becomes global and overwrites outer `c` hence printing `c1` later.



## [89. let](https://bigfrontend.dev/quiz/let)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 


let a = 1;
(function() {
  let foo = () => a
  let a = 2;
  console.log(foo())
}())
```

### 答案

```sh
2
```

### 详细解析

The answer is 2 I think that we can image function foo's scope chain as below:

```js
[foo.AO , IIFE.AO , globalContext.VO]
```

when foo return a , it will find a in foo.AO where it is 2

```js
let a = 1;
(function() {
  let foo = () => a
  let a = 2;
  console.log(foo()) // 2
}())
```

Here, inside the [IIFE](https://developer.mozilla.org/en-US/docs/Glossary/IIFE) the function foo simply returns `a`. Whenever we execute it, it'll return the value of `a` defined at that point.

In this example, even though `a` is assigned a value 2 after `foo` is declared it is still before `foo` is invoked. Thus, at the time of execution it prints 2



## [90. array keys](https://bigfrontend.dev/quiz/array-keys)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 


console.log(Reflect.ownKeys([]))
console.log(Reflect.ownKeys([,]))
console.log(Reflect.ownKeys([1,,2]))
console.log(Reflect.ownKeys([...[1,,2]]))



```

### 答案

```sh
["length"]
["length"]
["0","2","length"]
["0","1","2","length"]
```

### 详细解析

> The static [Reflect.ownKeys()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect/ownKeys) method returns an array of the target object's own property keys.

Arrays under the hood in JavaScript are Objects. Their keys are numbers, and their values are the elements. By default, all arrays also have a property `length` that reflects the number of elements in that array. In the case of a sparse array, holes do not add the corresponding key to the array.

Also, the [spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) converts these holes to `undefined`

`1.` Its an empty array so answer only contains `length

`2.` This array [,] is also an empty sparse array hence holes are ignored

`3.` [1,,2] has values defined at index 0 and 2, hence it prints ["0","2","length"]

`4.` Using Spread Operator changes the input array to [1,undefined,2] thus all the keys give following output ["0","1","2","length"]

```js
console.log(Reflect.ownKeys([])) // ["length"]
console.log(Reflect.ownKeys([,])) // ["length"]
console.log(Reflect.ownKeys([1,,2])) // ["0","2","length"]
console.log(Reflect.ownKeys([...[1,,2]])) // ["0","1","2","length"]
```



## [91. largest Array index](https://bigfrontend.dev/quiz/largest-Array-index)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 


const arr = []
arr[(2 ** 32) - 2] = 1
arr[(2 ** 32) - 1] = 2
console.log(arr.at(-1))
```

### 答案

```sh
1
```

### 详细解析

The [at()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/at) method takes an integer value and returns the item at that index, allowing for positive and negative integers. Negative integers count back from the last item in the array. For the last item `array[array.length-1]`, you can also call `array.at(-1)`

JavaScript arrays are zero-based and use 32-bit indexes: the index of the first element is 0, and the highest possible index is 4294967294 (2^32 − 2) which we have assigned as `1`

Accessing, `arr.at(-1)` thus returns the last element `1`.

Also, note that `arr[2^32 - 1] = 2` does store 2 at that index however since its more than the max possible length it cannot be accessed by `at`

```js
const arr = []
arr[(2 ** 32) - 2] = 1
arr[(2 ** 32) - 1] = 2
console.log(arr.at(-1)) // 1
```



## [92. NaN](https://bigfrontend.dev/quiz/NaN)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

console.log(NaN == NaN)
console.log(NaN === NaN)
console.log(Object.is(NaN, NaN))
console.log([NaN].indexOf(NaN))
console.log([NaN].includes(NaN))
console.log(Math.max(NaN, 1))
console.log(Math.min(NaN, 1))
console.log(Math.min(NaN, Infinity))




```

### 答案

```sh
false
false
true
-1
true
NaN
NaN
NaN
```

### 详细解析

> The global [NaN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/NaN) property is a value representing Not-A-Number.

`1.` and `2.` NaN compares unequal (via both == and ===) to any other value **including to another NaN value**.

`3.` Object.is() determines whether two values are the same value and returns true when we compare NaN

`4.` and `5.` **indexOf** uses [Strict Equality Comparison](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness#strict_equality_using) and thus `[NaN].indexOf(NaN) === -1` , **array.includes** uses [SameValueZero comparison](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness#same-value-zero_equality) algorithm , thus making `[NaN].includes(NaN)` true.

`6.` , `7.` and `8.` The Math.max() and Math.min() functions return `NaN` if any parameter isn't a number and can't be converted into one (of course NaN cannot be converted into a number).

```js
console.log(NaN == NaN) // false
console.log(NaN === NaN) // false
console.log(Object.is(NaN, NaN)) // true
console.log([NaN].indexOf(NaN)) // -1
console.log([NaN].includes(NaN)) // true
console.log(Math.max(NaN, 1)) // NaN
console.log(Math.min(NaN, 1)) // NaN
console.log(Math.min(NaN, Infinity)) // NaN
```



## [93. string](https://bigfrontend.dev/quiz/string)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

let a = 'bfe.dev'
a[0] = 'c'
console.log(a)
```

### 答案

```sh
"bfe.dev"
```

### 详细解析

Starting from ECMAScript 5, a string can be treated as an array-like object, where individual characters correspond to a numerical index.

However, when using bracket notation for character access, **attempting to delete or assign a value to these properties will not succeed.** The properties involved are neither writable nor configurable.

```js
let a = 'bfe.dev'
a[0] = 'c' // this will make no difference
console.log(a) // "bfe.dev"
```



## [94. emoji](https://bigfrontend.dev/quiz/emoji)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

console.log('👍'.length)
```

### 答案

```sh
2
```

### 详细解析

JavaScript strings are represented using UTF-16 code units. Each code unit can be used to represent a code point in the [U+0000, U+FFFF] range – also known as the “basic multilingual plane” (BMP).

For code points beyond U+FFFF, you’d represent them as a surrogate pair. That is to say, two contiguous code units. For instance, the emoji '👍' code point is represented with the '\uD83D\uDC4D' contiguous code units.

[String.length](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/length) property returns the number of code units in the string. While this is usually the same as character length, using characters that are outside the BMP([U+0000, U+FFFF] range) can return a different length

If you're curious, [Read this](https://ponyfoo.com/articles/es6-strings-and-unicode-in-depth#unicode)

```js
console.log('👍'.length) // 2
```



## [95. number format](https://bigfrontend.dev/quiz/number-format)

### 题目

What does the code snippet to the right output by `console.log`

```js
// This is a JavaScript Quiz from BFE.dev 

console.log(017 - 011)
console.log(018 - 011)
console.log(019 - 011)
```

### 答案

```sh
6
9
10
```

### 详细解析

A leading zero represents an octal (base 8) number unless any digit is 8 or 9, in which case, the leading 0 is ignored

Here, 017 and 011 are valid octal numbers while 018 and 019 are treated as decimal. We have, 017 = 15 and 011 = 9 in decimal system

```js
console.log(017 - 011) // 15 - 9 = 6
console.log(018 - 011) // 18 - 9 = 9
console.log(019 - 011) // 19 - 9 = 10
```