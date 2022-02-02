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

国外前端会考察哪些问题？js函数、系统设计、TS类型体操的用法，大大拓宽了我的眼界，去思考和实战更加相关的问题吧，输入输出考验了对js语法的掌握程度，值得多次学习。
<!-- more -->

## 输入输出

综合考察对js的熟悉程度

### 1. Promise order

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

### 2. Promise executor

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

Promise链式调用返回的值和值的穿透

```sh
1
```

### 3. Promise then callbacks

What does the code snippet to the right output by `console.log`?

```js
Promise.resolve(1)
.then(() => 2)
.then(3)
.then((value) => value * 3)
.then(Promise.resolve(4))
.then(console.log)
```

Promise链式调用返回的值和值的穿透

```sh
6
```

### 4. Promise then callbacks II

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

### 5. block scope

What does the code snippet to the right output by `console.log`?

```js
for (var i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 0)
}

for (let i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 0)
}
```

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

### 6. Arrow Function

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

### 7. Increment Operator

What does the code snippet to the right output by `console.log`?

```js
let a = 1
const b = ++a
const c = a++
console.log(a)
console.log(b)
console.log(c)
```

考察++的执行顺序，输出

```sh
3
2
2
```

### 8. Implicit Coercion I

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

### 9. null and undefined

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

### 10. Equal

What does the code snippet to the right output by `console.log`?

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

### 11. Implicit Coercion II

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

### 12. arguments

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

要注意d没有被重新赋值，只有arguments[3]被重新赋值

```sh
1,2,3,undefined
"bfe",2,3,undefined
```

### 13. Operator precedence

What does the code snippet to the right output by `console.log`?

```js
console.log(0 == 1 == 2)
console.log(2 == 1 == 0)
console.log(0 < 1 < 2)
console.log(1 < 2 < 3)
console.log(2 > 1 > 0)
console.log(3 > 2 > 1)
```

从左到右判断，bool要转成number

```sh
false
true
true
true
true
false
```

### 14. Addition vs Unary Plus

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

三个加号会无视掉中间的

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

### 15. instanceOf

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

### 16. parseInt

What does the code snippet to the right output by `console.log`?

```js
console.log(['0'].map(parseInt))
console.log(['0','1'].map(parseInt))
console.log(['0','1','1'].map(parseInt))
console.log(['0','1','1','1'].map(parseInt))
```

注意parseInt第二个参数，没有默认是0

```sh
[0]
[0,NaN]
[0,NaN,1]
[0,NaN,1,1]
```

### 17. reduce

What does the code snippet to the right output by `console.log`?

```js
[1,2,3].reduce((a,b) => {
  console.log(a,b)
});

[1,2,3].reduce((a,b) => {
  console.log(a,b)
}, 0)
```

reduce根据初始值决定后续的值

```sh
1,2
undefined,3
0,1
undefined,2
undefined,3
```

### 18. Promise executor II

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

p1、p3

```sh
false
true
false
false
```

### 19. `this`

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

### 20. name for Function expression

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

主要是考察匿名函数的重新赋值无效

```sh
"function"
"function"
"function"
"undefined"
"function"
"function"
```

### 21. Array I

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

### 22. min max

What does the code snippet to the right output by `console.log`?

```js
console.log(Math.min())
console.log(Math.max())
console.log(Math.min(1))
console.log(Math.max(1,2))
console.log(Math.min([1,2,3]))
console.log(Math.min([1,2,3],1))
```

最大的数和最小的数默认是-Infinity和Infinity，一旦输入有NaN，输出必然是NaN

```sh
Infinity
-Infinity
1
2
NaN
```

### 23. Promise.all()

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

一旦有reject，进入catch

```sh
[]
[1,2,3,4]
"error"
```

### 24. Equality & Sameness

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

### 25. zero

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

### 66. if

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

### 67. if II

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

### 68. undefined

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
