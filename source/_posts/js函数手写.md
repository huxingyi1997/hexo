---
title:  js函数手写
date: 2021-02-10 00:00:00
categories: 
- web前端
tags:
- javascript
- 函数手写
---
手写常见js函数，面试必备，多练几遍，争取手撕成功，按照顺序补充，加油啊，除了算法题这种题也很关键，总算重新整理了一遍，以该版本为作为自己最终收藏的版本更新了。

## 目录

1.[手写 call](#1.手写call)✅

2.[手写apply](#2.手写apply)✅

3.[手写bind](#3.手写bind)✅️

4.[手写new](#4.手写new)✅

5.[手写Object.create](#5.手写Object.create)✅

6.[手写ES5继承](#6.手写ES5继承)✅

7.[手动实现instanceof](#7.手动实现instanceof)✅

8.[手写Array.isArray](#8.手写Array.isArray)✅

9.[实现一个函数判断数据类型](#9.实现一个函数判断数据类型)✅

10.[手写深拷贝](#10.手写深拷贝)✅

11.[数组扁平化](#11.数组扁平化)✅

12.[数组去重](#12.数组去重)✅

13.[手写数组ES5常见方法](#13.手写数组ES5常见方法)✅

14.[实现数组原地反转](#14.实现数组原地反转)✅

15.[reduce的应用汇总](#15.reduce的应用汇总)✅

16.[洗牌算法](#16.洗牌算法)✅

17.[对象扁平化](#17.对象扁平化)✅

18.[手写偏函数](#18.手写偏函数)✅

19.[函数柯里化](#19.函数柯里化)✅

20.[手写compose函数](#20.手写compose函数)✅

21.[实现 (5).add(3).minus(2) 功能](#21.实现 (5).add(3).minus(2) 功能)✅

22.[实现一个 add 函数](#22.实现一个 add 函数)✅

23.[计算两个数组的交集](#23.计算两个数组的交集)✅

24.[手写对象深度比较](#24.手写对象深度比较)✅

25.[扁平数组转树状结构](#25.扁平数组转树状结构)✅

26.[防抖(debounce)](#26.防抖(debounce))✅

27.[节流(throttle)](#27.节流(throttle))✅

28.[手写const](#28.手写const)✅

29.[手写双向绑定](#29.实现一个双向绑定)✅

30.[图片懒加载](#30.图片懒加载)✅

31.[区间随机数生成器](#31.区间随机数生成器)✅

32.[打印菱形](#32.打印菱形)✅

33.[手写parseInt](#33.手写parseInt)✅

34.[手写JSON.stringify](#34.手写JSON.stringify)✅

35.[手写JSON.parse](#35.手写JSON.parse)✅

36.[解析 URL Params 为对象](#36.解析 URL Params 为对象)✅

37.[模板引擎实现](#37.模板引擎实现)✅

38.[驼峰命名-中划线转换](#38.驼峰命名-中划线转换)✅

39.[查找字符串中出现最多的字符和个数](#39.查找字符串中出现最多的字符和个数)✅

40.[字符串查找](#40.字符串查找)✅

41.[实现千位分隔符](#41.实现千位分隔符)✅

42.[正则表达式的基本运用](#42.正则表达式的基本运用)✅

43.[手写trim](#43.手写trim)✅

44.[版本号比较](#44.版本号比较)✅

45.[手写Object.freeze](#45.手写Object.freeze)✅

46.[实现ES6的extends](#46.实现ES6的extends)✅

47.[手写实现Set](#47.手写实现Set)✅

48.[手写实现Map](#48.手写实现Map )✅

49.[检测对象循环引用](#49.检测对象循环引用)✅

50.[单例模式](#50.单例模式)✅

51.[观察者模式](#51.观察者模式)✅

52.[发布/订阅模式 (EventBus/EventEmitter)](#52.发布/订阅模式 (EventBus/EventEmitter))✅

53.[手写事件代理](#53.手写事件代理)✅

54.[手写JSONP跨域](#54.手写JSONP跨域)✅

55.[手写Promise](#55.手写Promise)✅

56.[手写ajax封装](#56.手写ajax封装)✅

57.[手写实现sleep](#57.手写实现sleep)✅

58.[手写promisify](#58.手写promisify)✅

59.[实现延时执行队列](#59.实现延时执行队列)✅

60.[setTimeout实现setInterval](#60.setTimeout实现setInterval)✅

61.[手写fetch](#61.手写fetch)✅

62.[手写实现Generator](#62.手写实现Generator)✅

63.[手写实现async/await](#63.手写实现async/await)✅

64.[手写异步串行和异步并行](#64.手写异步串行和异步并行)✅

65.[异步并发数限制](#65.异步并发数限制)✅

66.[LazyMan](#66.LazyMan)✅

67.[Promise超时重新请求](#67.Promise超时重新请求)✅



<!-- more -->

## 1.手写call

### ES5实现及过程分析：

```js
function fnFactory(context) {
    var unique_fn = "fn";
    while (context.hasOwnProperty(unique_fn)) {
    	unique_fn = "fn" + Math.random();
    }
    return unique_fn;
}
Function.prototype.myCall = function(context) {
    // 1. 若是传入的context是null或者undefined时指向window;
    // 2. 若是传入的是原始数据类型, 原生的call会调用 Object() 转换
    context = (context !== null && context !== undefined) ? Object(context) : window;
    // 3. 创建一个独一无二的fn函数的命名
    var fn = fnFactory(context);
    // 4. 这里的this就是指调用call的那个函数
    // 5. 将调用的这个函数赋值到context中, 这样之后执行context.fn的时候, fn里的this就是指向context了
    context[fn] = this;
    // 6. 定义一个数组用于放arguments的每一项的字符串: ['agruments[1]', 'arguments[2]']
    var args = [];
    // 7. 要从第1项开始, 第0项是context
    for (var i = 1, l = arguments.length; i < l; i++) {
    	args.push("arguments[" + i + "]");
    }
    // 8. 使用eval()来执行fn并将args一个个传递进去
    var result = eval("context[fn](" + args + ")");
    // 9. 给context额外附件了一个属性fn, 所以用完之后需要删除
    delete context[fn];
    // 10. 函数fn可能会有返回值, 需要将其返回
    return result;
};
```

### ES6实现（手写）

1.判断当前`this`是否为函数，防止`Function.prototype.myCall()` 直接调用

2.`context` 为可选参数，如果不传的话默认上下文为 `window`

3.为`context` 创建一个 `Symbol`（保证不会重名）属性，将当前函数赋值给这个属性

4.处理参数，传入第一个参数后的其余参数

5.调用函数后即删除该`Symbol`属性

```js
/*
 * context为函数运行时要使用的this值 默认将context指向window 注意仅在非严格模式下会有这种行为
 * args 为一个数组或者类数组对象，是调用函数时的参数列表
 */
Function.prototype.myCall = function (context = window, ...args) {
    // 用于防止 Function.prototype.myCall() 直接调用
    if (this === Function.prototype) return undefined;
    // context = context || window;
    // 为了防止原来的属性被覆盖，用Symbol去创建一个独一无二的值
    let fn = Symbol();
    // 这里this指向调用myCall的函数
    context[fn] = this;
    // 重点代码，利用this指向，调用myCall的函数,并接收返回值
    let result = context[fn](...args);
    // 最后删除这个临时属性
    delete context[fn];
    // 返回结果
    return result;
};
```



## 2.手写apply

`apply`实现类似`call`，参数为数组

### ES5实现及过程分析：

```js
function fnFactory(context) {
    var unique_fn = "fn";
    while (context.hasOwnProperty(unique_fn)) {
    	unique_fn = "fn" + Math.random();
    }
    return unique_fn;
}
Function.prototype.apply2 = function(context, arr) {
    // 1. 若是传入的context是null或者undefined时指向window;
    // 2. 若是传入的是原始数据类型, 原生的call会调用 Object() 转换
    context = context ? Object(context) : window;
    // 3. 创建一个独一无二的fn函数的命名
    var fn = fnFactory(context);
    // 4. 这里的this就是指调用call的那个函数
    // 5. 将调用的这个函数赋值到context中, 这样之后执行context.fn的时候, fn里的this就是指向context了
    context[fn] = this;

    var result;
    // 6. 判断有没有第二个参数
    if (!arr) {
    	result = context[fn]();
    } else {
        // 7. 有的话则用args放每一项的字符串: ['arr[0]', 'arr[1]']
        var args = [];
        for (var i = 0, len = arr.length; i < len; i++) {
            args.push("arr[" + i + "]");
        }
        // 8. 使用eval()来执行fn并将args一个个传递进去
        result = eval("context[fn](" + args + ")");
    }
    // 9. 给context额外附件了一个属性fn, 所以用完之后需要删除
    delete context[fn];
    // 10. 函数fn可能会有返回值, 需要将其返回
    return result;
};
```

### ES6实现：

```js
/*
 * context为函数运行时要使用的this值 默认将context指向window 注意仅在非严格模式下会有这种行为
 * args 为一个数组或者类数组对象，是调用函数时的参数列表
 */
Function.prototype.myApply = function (context = window, args) {
    // 用于防止 Function.prototype.myApply() 直接调用
    if (this === Function.prototype) return undefined;
    // context = context || window;
    // 为了防止原来的属性被覆盖，用Symbol去创建一个独一无二的值
    let fn = Symbol();
    // 这里this指向调用myApply的函数
    context[fn] = this;
    // 重点代码，利用this指向，相当于context.caller(...args)
    let result = context[fn](...args);
    // 最后删除这个临时属性
    delete context[fn];
    // 返回结果
    return result;
};
```



## 3.手写bind

**提示**:

1. 函数内的`this`表示的就是调用的函数
2. 可以将上下文传递进去, 并修改`this`的指向
3. 返回一个函数
4. 可以传入参数
5. 柯里化
6. 一个绑定的函数也能使用`new`操作法创建对象, 且提供的`this`会被忽略

### ES5实现及过程分析：

```js
Function.prototype.myBind1 = function(context) {
    // 1. 判断调用bind的是不是一个函数
    if (typeof this !== "function") {
        throw new Error(
        	"Function.prototype.bind - what is trying to be bound is not callable";
        );
    }
    // 2. 外层的this指向调用者(也就是调用的函数)
    var self = this;
    // 3. 收集调用bind时的其它参数
    var args = Array.prototype.slice.call(arguments, 1);

    // 4. 创建一个返回的函数
    var fBound = function() {
        // 6. 收集调用新的函数时传入的其它参数
        var innerArgs = Array.prototype.slice.call(arguments);
        // 7. 使用apply改变调用函数时this的指向
        // 作为构造函数调用时this表示的是新产生的对象, 不作为构造函数用的时候传递context
        return self.apply(
            this instanceof fNOP ? this : context,
            args.concat(innerArgs)
        );
    };
    // 5. 创建一个空的函数, 且将原型指向调用者的原型(为了能用调用者原型中的属性)
    // 下面三步的作用有点类似于 fBoun.prototype = this.prototype 但有区别
    var fNOP = function() {};
    fNOP.prototype = this.prototype;
    fBound.prototype = new fNOP();
    // 8. 返回最后的结果
    return fBound;
};
```

### ES6实现：

1.处理参数，返回一个闭包

2.判断是否为构造函数调用，如果是则使用`new`调用当前函数

3.如果不是，使用`apply`，将`context`和处理好的参数传入

```js
Function.prototype.myBind = function (context = window, ...args) {
	if (this === Function.prototype) return undefined;
	const _this = this;
    // 返回一个函数
    return function F(...arguments) {
        // 因为返回了一个函数，我们可以 new F()，所以需要判断
        if (this instanceof F) {
            return new _this(...args, ...arguments);
        }
        return _this.apply(context, args.concat(...arguments));
    }
}
```
使用和之前apply、call类似的思想，结合闭包实现bind

```js
/*
 * context为函数运行时要使用的this值 默认将context指向window 注意仅在非严格模式下会有这种行为
 * args 为一个数组或者类数组对象，是调用函数时的参数列表
 */
Function.prototype.myBind = function (context = window, ...args) {
    // 为了防止原来的属性被覆盖，用Symbol去创建一个独一无二的值
    let fn = Symbol();
    // 这里this指向调用myApply的函数
    context[fn] = this;
    // 返回闭包函数
    return function (..._args) {
        // 与当前参数组合
        args = args.concat(_args);
        // 重点代码，执行函数
        context[fn](...args);
        // 最后删除这个临时属性
        delete context[fn];
    };
};
```

### 扩展

获取函数中的参数：

```js
// 获取argument对象 类数组对象 不能调用数组方法
function test1() {
	console.log('获取argument对象 类数组对象 不能调用数组方法', arguments);
}

// 获取参数数组  可以调用数组方法
function test2(...args) {
	console.log('获取参数数组  可以调用数组方法', args);
}

// 获取除第一个参数的剩余参数数组
function test3(first, ...args) {
	console.log('获取argument对象 类数组对象 不能调用数组方法', args);
}

// 透传参数
function test4(first, ...args) {
    fn(...args);
    fn(...arguments);
}

function fn() {
	console.log('透传', ...arguments);
}

console.log(test1(1, 2, 3)); // 获取argument对象 类数组对象 不能调用数组方法 Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
console.log(test2(1, 2, 3)); // 获取参数数组  可以调用数组方法 (3) [1, 2, 3]
console.log(test3(1, 2, 3)); // 获取argument对象 类数组对象 不能调用数组方法 (2) [2, 3]
console.log(test4(1, 2, 3)); // 透传 2 3 透传 1 2 3
console.log(fn(1, 2, 3)); // 透传 1 2 3
```



## 4.手写new

**new操作符做了这些事：**

- 创建一个全新的对象，这个对象的`__proto__`要指向构造函数的原型对象
- 执行构造函数
- 返回值为object类型则作为new方法的返回值返回，否则返回上述全新对象

```js
function myNew(fn, ...args) {
    let instance = Object.create(fn.prototype);
    let res = fn.apply(instance, args);
    return typeof res === 'object' ? res: instance;
}
```

我们要实现一个`new`，首先要明白它有哪些特性。

看下面这个例子：

```js
function Person (name) {
	this.name = name;
}
Person.prototype.eat = function () {
	console.log('Eatting');
}
var lindaidai = new Person('LinDaiDai');
console.log(lindaidai);
lindaidai.eat();
```

使用`new`创建的实例：

- 能访问到构造函数里的属性(`name`)
- 能访问原型中的属性(`eat`)

**new操作符做了这些事：**

- 创建一个全新的对象，这个对象的`__proto__`要指向构造函数的原型对象
- 执行构造函数
- 返回值为object类型则作为new方法的返回值返回，否则返回上述全新对象

根据特性，我们可以这样实现：

```js
function myNew() {
    // 1. 获取构造函数，并且删除 arguments 中的第一项
    var fn = [].shift.call(arguments);
    // 2. 创建一个空的对象并链接到构造函数的原型，使它能访问原型中的属性
    var obj = Object.create(fn.prototype);
    // 3. 使用apply改变构造函数中this的指向实现继承，使obj能访问到构造函数中的属性
    var res = fn.apply(obj, arguments);
    // 4. 优先返回构造函数返回的对象
    return res instanceof Object ? res : obj;
}
```

可以简化写作

```js
function myNew(fn, ...args) {
    var obj = Object.create(fn.prototype);
    var res = fn.apply(obj, args);
    return typeof res === 'object' ? res: obj;
}
```

这里要提一嘴，第四步中为什么要做这么一个判断呢？

主要是你要考虑构造函数它有没有返回值。

像我们案例中的构造函数`Person`它是没有返回值的。

```js
// 1.有返回值且为对象
function Person (name, sex) {
	this.name = name;
	return {
		sex: sex
	}
}
// 2. 没有返回值
function Person (name) {
	this.name = name;
}
// 3. 返回值为基本类型
function Person (name) {
	this.name = name;
	return 'str';
}
```

1. 构造函数中有返回值且为对象，那么创建的实例就只能访问到返回对象中的属性，所以要判断一下`ret`的类型，如果是对象的话，则返回这个对象。
2. 构造函数中没有返回值，那么创建的实例就能访问到这个构造函数中的所有属性了，此时`ret`就会为`undefined`，所以返回`obj`。
3. 构造函数中有返回值但是返回值是`undefined`以外的其它基本类型(比如字符串)，这种情况当成第二种情况(没有返回值)来处理。

**验证：**

来验证一下可行性：

```js
function Person (name) {
	this.name = name;
}
Person.prototype.eat = function () {
	console.log('Eatting');
}
function myNew() {
    // 1. 获取构造函数，并且删除 arguments 中的第一项
    var fn = [].shift.call(arguments);
    // 2. 创建一个空的对象并链接到构造函数的原型，使它能访问原型中的属性
    var obj = Object.create(fn.prototype);
    // 3. 使用apply改变构造函数中this的指向实现继承，使obj能访问到构造函数中的属性
    var res = fn.apply(obj, arguments);
    // 4. 优先返回构造函数返回的对象
    return res instanceof Object ? res : obj;
}
var lindaidai = myNew(Person, 'LinDaiDai');
console.log(lindaidai); // Person{ name: 'LinDaiDai' }
lindaidai.eat(); // 'Eatting'
```



## 5.手写Object.create

Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__，`Object.create`方法的实质是新建一个空的构造函数`F`，然后让`F.prototype`属性指向参数对象`obj`，最后返回一个`F`的实例，从而实现让该实例继承`obj`的属性。

```js
// 模拟 Object.create
function create (proto) {
    // 新建一个构造函数
    function F() {}
    // 继承对象原型
    F.prototype = proto;
	// 返回一个改构建函数实力
    return new F();
}
```

使用方法，特别注意和ES5手写继承时用法不同，因此其传递得时构造函数

```js
var p = {
    name: 'smyhvae'
};
var obj1 = Object.create(p);  // 此方法创建的对象，是用原型链连接的
console.log(obj1.__proto__ === p);
var obj2 = Object.create(p.prototype);  // 此方法创建的对象，是用原型链连接的
console.log(obj2); /* VM3968:4 Uncaught TypeError: Object prototype may only be an Object or null: undefined
    at Function.create (<anonymous>)
    at <anonymous>:4:19 */
```

或者

```js
Object.create = function (obj) {
    var B = {};
    Object.setPrototypeOf(B, obj);
    return B;  
};
```

更直观点

```js
Object.create = function (obj) {
    var B = {};
    B.__proto__ = obj;
    return B;  
};
```



## 6.手写ES5继承

面试题如下，想让student继承person，先写下基本框架

```js
function person() {
	this.kind = "person";
}

person.prototype.eat = function (food) {
	console.log(this.name + " is eating " + food);
}

function student() {

}
```

先写了一下继承后上述prototype和\__proto__的关系

```js
student.prototype.__proto__ === person.prototype
```

### 原型继承

> 子类的原型指向父类。

```js
function person() {
	this.kind = "person";
}

person.prototype.eat = function (food) {
	console.log(this.name + " is eating " + food);
}

function student() {

}

student.prototype = new person();
```

优点：

1.简单，易于实现

2.父类新增原型方法、原型属性，子类都能访问到

缺点：

1.无法实现多继承，因为原型一次只能被一个实例更改

2.来自原型对象的所有属性被所有实例共享,改变一个其他也会改变。

3.创建子类 Child实例时，无法向父构造函数Parent传参

该打，我现场写没写完全对，写的是

```js
student = new person();
```

脑子怎么想的，这样student怎么继承person的实例和共享



### 构造继承

> 在子类构造函数中调用父类构造函数

```js
function person() {
	this.kind = "person";
}

person.prototype.eat = function (food) {
	console.log(this.name + " is eating " + food);
}

function student( ) {
	person.call(this);
}
```

优点：

1.避免了原型链继承中子类实例共享父类引用属性的问题。

2.创建子类Child 实例时，可以向父类 Parent 传递参数。

3.可以实现多继承（call多个父类对象）

缺点：

1.实例并不是父类的实例，只是子类的实例

2.只能继承父类实例的属性和方法,不能继承父类原型的属性和方法

3.方法都在构造函数中定义，每次创建实例都会创建一遍父类实例函数的副本，浪费内存，且无法实现函数复用。



这个当时倒是完全写对了。



### 组合继承

> 使用构造继承继承父类参数，使用原型继承继承父类函数

```js
function person() {
	this.kind = "person";
}

person.prototype.eat = function (food) {
	console.log(this.name + " is eating " + food);
}

function student() {
	person.call(this);
}

student.prototype = new person();
```

优点：

1.融合原型链继承和构造函数的优点，既可以继承实例的属性和方法，也可以继承原型的属性和方法。

2.既是子类的实例，也是父类的实例

3.可以向父类传递参数，函数可以复用

缺点：

1.调用了两次父类构造函数，生成了两份实例，即person的构造函数会多执行了一次（`Child.prototype = new Parent()`;）

2.constructor指向问题，子类实例constructor指向父类

哭了，又没有完全写对，错误同原型继承



### 原型式继承

```js
function person() {
	this.kind = "person";
}

person.prototype.eat = function (food) {
	console.log(this.name + " is eating " + food);
}

function student() {

}

student.prototype = Object.create(person.prototype);
```

缺点：包含引用类型的属性值始终都会共享相应的值，这点跟原型链继承一样。

又搞错了

写成了

```js
student.prototype = Object.create(person());
```

不知道怎么想的，面试官提示我知不知道Object.create怎么一回事，还口述了手写过程

```js
Object.prototype.myCreate(proto) {
    function f() {};
    f.prototype = proto;
    return new f();
}
```

口述的方法倒是对的，就是这样传递的参数是什么，我怎么思考



口述之后我发现传递person()不对劲，又重写了

```js
student.prototype = Object.create(person);
```

真是该打



### 寄生组合继承

> 将父类原型对象直接给到子类，父类构造函数只执行一次，而且父类属性和方法均能访问

```js
function person() {
	this.kind = "person";
}

person.prototype.eat = function (food) {
	console.log(this.name + " is eating " + food);
}

function student() {
	person.call(this);
}

student.prototype = person.prototype;
// 或者 student.prototype = Object.create(person.prototype);
```

缺点：这种继承方法父类原型和子类原型是同一个对象，无法区分子类真正是由谁构造。

我的错误跟上面的一样，不再赘述



### 寄生组合优化继承

最后当然是最完美的寄生组合优化继承

```js
function person() {
	this.kind = "person";
}

person.prototype.eat = function (food) {
	console.log(this.name + " is eating " + food);
}

function student() {
	person.call(this);
}

student.prototype = person.prototype;
// 或者 student.prototype = Object.create(person.prototype);
student.prototype.constructor = student;
```

引用《JavaScript高级程序设计》中对寄生组合式继承的夸赞就是：

这种方式的高效率体现它只调用了一次 Parent 构造函数，并且因此避免了在 Parent.prototype 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用 instanceof 和 isPrototypeOf。开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。

总之错误真多，觉得没脸见人了，手写和口述讲思路难度差距真大，自己把错误的地方好好反思

这篇讲的很好[前端高频面试题整理](https://juejin.cn/post/6844904148899463175) 



## 7.手动实现instanceof

使用方法

```js
a instanceof Object
```

原理：判断`Object`的prototype是否在`a`的原型链上。

### 递归实现

按照target原型链的向上查找，直到找到 origin 或 null

```js
// target instanceof origin
// 变量origin的prototype 存在于变量target的原型链上
function myInstanceof(target, origin) {
    // 获得target对象的原型
    let proto = target.__proto__;
	// 判断是否target的原型是否为空
    if (proto) {
        if (proto === origin.prototype) {
            // origin的prototype在target的原型链上
            return true;
        } else {
            // 继续沿着target原型链查找origin的prototype
            return myInstanceof(proto, origin);
        }
    } else {
        return false;
    }
}
```

### 迭代实现

改用循环而不是递归 ，可以参考一下[js函数式编程](https://hxy1997.xyz/2021/04/22/js%E5%87%BD%E6%95%B0%E5%BC%8F%E7%BC%96%E7%A8%8B/#more)里面的蹦床函数思想

```js
// target instanceof origin
// 变量origin的prototype 存在于变量target的原型链上
function myInstanceof(target, origin){    
    // 验证如果为基本数据类型，就直接返回false可以不写
    const baseType = ['string', 'number', 'boolean', 'undefined', 'symbol']
    if(baseType.includes(typeof(target))) return false;
    // 取 origin 的显式原型origin的prototype
    let oP  = origin.prototype;
    // 取 target 的隐式原型
    proto = target.__proto__;
    // 无线循环的写法（也可以使 for(;;) ）
    while (true) {
        // 找到最顶层
        if (proto === null) {
            return false;
        }
        // 严格相等
        if(proto === oP){
            return true;
        }
        // 没找到继续向上一层原型链查找
        proto = proto.__proto__;
    }
}
```



## 8.手写Array.isArray 

先总结一下判断一个数据是否是一个数组

```js
Array.isArray(arr);
arr instanceof Array;
arr.constructor === Array;
Object.prototype.toString.call(arr) === '[object Array]';
```

测试一下结果

```js
let arr = [];
console.log(Array.isArray(arr)); // true
console.log(arr instanceof Array); // true
console.log(arr.constructor === Array); // true
console.log(Object.prototype.toString.call(arr) === '[object Array]'); // true
```

### 使用toString实现Array.isArray

```js
Array.myIsArray = function(obj) {
	return Object.prototype.toString.call(Object(obj)) === '[object Array]';
}

console.log(Array.myIsArray([])); // true
```

### 使用instanceof实现Array.isArray

```js
Array.myIsArray = function(obj) {
	return obj instanceof Array;
}

console.log(Array.myIsArray([])); // true
```

### 使用constructor实现Array.isArray

```js
Array.myIsArray = function(obj) {
	return obj.constructor === Array;
}

console.log(Array.myIsArray([])); // true
```



## 9.实现一个函数判断数据类型

```js
function getType(obj) {
    if (obj === null) return String(obj);
    // 对象类型 "[object XXX]"->XXX的小写 简单类型typeof obj
    return typeof obj === 'object' ? Object.prototype.toString.call(obj).replace('[object ', '').replace(']', '').toLowerCase() : typeof obj;
}

// 调用
console.log(getType(null)); // null
console.log(getType(undefined)); // undefined
console.log(getType({})); // object
console.log(getType([])); // array
console.log(getType(123)); // number
console.log(getType(true)); // boolean
console.log(getType('123')); // string
console.log(getType(/123/)); // regexp
console.log(getType(new Date())); // date
```



## 10.手写深拷贝

**深拷贝和浅拷贝都是针对的引用类型，JS中的变量类型分为值类型（基本类型）和引用类型；对值类型进行复制操作会对值进行一份拷贝，而对引用类型赋值，则会进行地址的拷贝，最终两个变量指向同一份数据。**对于引用类型，会导致a b指向同一份数据，此时如果对其中一个进行修改，就会影响到另外一个，有时候这可能不是我们想要的结果，如果对这种现象不清楚的话，还可能造成不必要的bug。

那么如何切断a和b之间的关系呢，可以拷贝一份a的数据，根据拷贝的层级不同可以分为浅拷贝和深拷贝，浅拷贝就是只进行一层拷贝，深拷贝就是无限层级拷贝**假设B复制了A，当修改A时，看B是否会发生变化，如果B也跟着变了，说明这是浅拷贝，拿人手短，如果B没变，那就是深拷贝，自食其力。**

### 浅拷贝

```js
arr.slice();
arr.concat();
```

### 深拷贝极简版

```js
JSON.parse(JSON.stringify(obj));
```

估计这个api能覆盖大多数的应用场景，没错，谈到深拷贝，我第一个想到的也是它。但是实际上，对于某些严格的场景来说，这个方法是有巨大的坑的。问题如下：

> 无法解决`循环引用`的问题。举个例子：

```js
let a = {
    val: 2
};
a.target = a;
let res = JSON.parse(JSON.stringify(a));
console.log(res.target);
/* VM1156:5 Uncaught TypeError: Converting circular structure to JSON
    --> starting at object with constructor 'Object'
    --- property 'target' closes the circle
    at JSON.stringify (<anonymous>)
    at <anonymous>:5:27 */
```

拷贝a会出现系统栈溢出，因为出现了`无限递归`的情况。

> 无法拷贝一写`特殊的对象`，诸如 RegExp, Date, Set, Map等。

> 无法拷贝`函数`(划重点)。

总结：

**该方法的局限性：**

- 无法实现对函数 、RegExp等特殊对象的克隆
- 会抛弃对象的constructor,所有的构造函数会指向Object
- 对象有循环引用,会报错
- 所有以 symbol 为属性键的属性都会被完全忽略掉
- 无法区分布尔值、数字、字符串及其包装对象
- NaN 和 Infinity 格式的数值及 null 都会被当做 null。
- 其他类型的对象，包括 Map/Set/WeakMap/WeakSet，仅会序列化可枚举的属性。

### 面试够用的版本：递归法

考虑到数组和对象

```js
function deepCopy(obj) {
	let res;
	// 判断是否是引用类型，特别注意typeof null === "object"
	if (typeof obj === "object" && obj !== null) {
		// 复杂数据类型的类型
		res = obj.constructor === Array ? [] : {};
		for (let i in obj) {
			// 遍历对象中的每个元素是否为对象类型
			res[i] = typeof obj[i] === "object" ? deepCopy(obj[i]) : obj[i];
		}
	} else {
		// 简单数据类型 直接 = 赋值
		res = obj;
	}
	return res;
}
```

### 循环引用

上述版本执行下面这样一个测试用例：

```js
function deepCopy(obj) {
	let res;
	// 判断是否是引用类型，特别注意typeof null === "object"
	if (typeof obj === "object" && obj !== null) {
		// 复杂数据类型的类型
		res = obj.constructor === Array ? [] : {};
		for (let i in obj) {
			// 遍历对象中的每个元素是否为对象类型
			res[i] = typeof obj[i] === "object" ? deepCopy(obj[i]) : obj[i];
		}
	} else {
		// 简单数据类型 直接 = 赋值
		res = obj;
	}
	return res;
}

let a = {
    val: 2
};
a.target = a;
let res = deepCopy(a);
console.log(res.target);
/* VM948:12 Uncaught RangeError: Maximum call stack size exceeded
	....
*/
```

因为递归进入死循环导致栈内存溢出了。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210424092024.png)

原因就是上面的对象存在循环引用的情况，即对象的属性间接或直接的引用了自身的情况：

解决循环引用问题，我们可以额外开辟一个存储空间，来存储当前对象和拷贝对象的对应关系，当需要拷贝当前对象时，先去存储空间中找，有没有拷贝过这个对象，如果有的话直接返回，如果没有的话继续拷贝，这样就巧妙化解的循环引用的问题。

这个存储空间，需要可以存储 `key-value`形式的数据，且 `key`可以是一个引用类型，我们可以选择 `Map`这种数据结构：

- 检查`map`中有无克隆过的对象
- 有 - 直接返回
- 没有 - 将当前对象作为`key`，克隆对象作为`value`进行存储
- 继续克隆

```js
function deepCopy(obj, map = new Map()) {
	let res;
	// 判断是否是引用类型，特别注意typeof null === "object"
	if (typeof obj === "object" && obj !== null) {
		// 复杂数据类型的类型
		res = obj.constructor === Array ? [] : {};
        // map中有克隆过的对象,直接返回
        if (map.get(obj)) {
            return obj;
        }
        // map中没有克隆过的对象,进行存储
        map.set(obj, res);
		for (let i in obj) {
			// 遍历对象中的每个元素是否为对象类型
			res[i] = typeof obj[i] === "object" ? deepCopy(obj[i], map) : obj[i];
		}
	} else {
		// 简单数据类型 直接 = 赋值
		res = obj;
	}
	return res;
}

// 测试
let a = {
    val: 2
};
a.target = a;
let res = deepCopy(a);
console.log(res.target);
```

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210424092125.jpeg)

可以看到，执行没有报错，且 `target`属性，变为了一个 `Circular`类型，即循环应用的意思。

接下来，可以使用 `WeakMap`替代 `Map`。

```js
function deepCopy(obj, map = new WeakMap()) {
    // ...
};
```

为什么要这样做呢？，先来看看 `WeakMap`的作用：

> WeakMap 对象是一组键/值对的集合，其中的键是弱引用的。其键必须是对象，而值可以是任意的。

什么是弱引用呢？

> 在计算机程序设计中，弱引用与强引用相对，是指不能确保其引用的对象不会被垃圾回收器回收的引用。一个对象若只被弱引用所引用，则被认为是不可访问（或弱可访问）的，并因此可能在任何时刻被回收。

我们默认创建一个对象：`const obj={}`，就默认创建了一个强引用的对象，我们只有手动将 `obj=null`，它才会被垃圾回收机制进行回收，如果是弱引用对象，垃圾回收机制会自动帮我们回收。

举例：

如果我们使用 `Map`的话，那么对象间是存在强引用关系的：

```js
let obj = { name : 'ConardLi' }
const target = {
    obj:'code秘密花园'
}
obj = null;
```

虽然我们手动将 `obj`，进行释放，然是 `target`依然对 `obj`存在强引用关系，所以这部分内存依然无法被释放。

再来看 `WeakMap`：

```js
let obj = { name : 'ConardLi' }
const target = new WeakMap();
target.set(obj, 'code秘密花园');
obj = null;
```

如果是 `WeakMap`的话， `target`和 `obj`存在的就是弱引用关系，当下一次垃圾回收机制执行时，这块内存就会被释放掉。

设想一下，如果我们要拷贝的对象非常庞大时，使用 `Map`会对内存造成非常大的额外消耗，而且我们需要手动清除 `Map`的属性才能释放这块内存，而 `WeakMap`会帮我们巧妙化解这个问题。

我也经常在某些代码中看到有人使用 `WeakMap`来解决循环引用问题，但是解释都是模棱两可的，当你不太了解 `WeakMap`的真正作用时。我建议你也不要在面试中写这样的代码，结果只能是给自己挖坑，即使是准备面试，你写的每一行代码也都是需要经过深思熟虑并且非常明白的。

能考虑到循环引用的问题，你已经向面试官展示了你考虑问题的全面性，如果还能用 `WeakMap`解决问题，并很明确的向面试官解释这样做的目的，那么你的代码在面试官眼里应该算是合格了。

### 性能优化（可以跳过，实在有点偏）

在上面的代码中，我们遍历数组和对象都使用了 `forin`这种方式，实际上 `for in`在遍历时效率是非常低的，常见的三种循环 `for、while、forin`的执行效率中，`while`的效率是最好的，所以，我们可以想办法把 `forin`遍历改变为 `while`遍历。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210424085011.jpeg)

我们先使用 `while`来实现一个通用的 `forEach`遍历， `iteratee`是遍历的回调函数，它可以接收每次遍历的 `value`和 `index`两个参数：

```js
function forEach(array, iteratee) {
    let index = -1;
    const length = array.length;
    while (++index < length) {
        iteratee(array[index], index);
    }
    return array;
}
```

下面对我们的 `deepCopy`函数进行改写：当遍历数组时，直接使用 `forEach`进行遍历，当遍历对象时，使用 `Object.keys`取出所有的 `key`进行遍历，然后在遍历时把 `forEach`会调函数的 `value`当作 `key`使用：

```js
function forEach(array, iteratee) {
    let index = -1;
    const length = array.length;
    while (++index < length) {
        iteratee(array[index], index);
    }
    return array;
}

function deepCopy(obj, map = new Map()) {
	let res;
	// 判断是否是引用类型，特别注意typeof null === "object"
	if (typeof obj === "object" && obj !== null) {
		// 复杂数据类型的类型
		res = obj.constructor === Array ? [] : {};
        // map中有克隆过的对象,直接返回
        if (map.get(obj)) {
            return obj;
        }
        // map中没有克隆过的对象,进行存储
        map.set(obj, res);
        const keys = obj.constructor === Array ?  undefined : Object.keys(obj);
        forEach(keys || obj, (value, key) => {
            if (keys) {
                key = value;
            }
            res[key] = deepCopy(obj[key], map);
        });
	} else {
		// 简单数据类型 直接 = 赋值
		res = obj;
	}
	return res;
}

// 测试
let a = {
    val: 2
};
a.target = a;
let res = deepCopy(a);
console.log(res.target);
```

### 其他数据类型

在上面的代码中，我们其实只考虑了普通的 `object`和 `array`两种数据类型，实际上所有的引用类型远远不止这两个，还有很多，下面我们先尝试获取对象准确的类型。

#### 合理的判断引用类型

首先，判断是否为引用类型，我们还需要考虑 `function`和 `null`两种特殊的数据类型：

```js
function isObject(obj) {
    const type = typeof obj;
    return obj !== null && (type === 'object' || type === 'function');
}
if (!isObject(obj)) {
    return obj;
}
// ...
```

#### 获取数据类型

我们可以使用 `toString`来获取准确的引用类型：

> 每一个引用类型都有 `toString`方法，默认情况下， `toString()`方法被每个 `Object`对象继承。如果此方法在自定义对象中未被覆盖， `toString()`返回 `"[object type]"`，其中type是对象的类型。

注意，上面提到了如果此方法在自定义对象中未被覆盖， `toString`才会达到预想的效果，事实上，大部分引用类型比如 `Array、Date、RegExp`等都重写了 `toString`方法。

我们可以直接调用 `Object`原型上未被覆盖的 `toString()`方法，使用 `call`来改变 `this`指向来达到我们想要的效果。

```js
function getType(target) {
    return Object.prototype.toString.call(target);
}
```

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210424095633.jpeg)

下面我们抽离出一些常用的数据类型以便后面使用：

```js
const mapTag = '[object Map]';
const setTag = '[object Set]';
const arrayTag = '[object Array]';
const objectTag = '[object Object]';
const argsTag = '[object Arguments]';

const boolTag = '[object Boolean]';
const dateTag = '[object Date]';
const errorTag = '[object Error]';
const numberTag = '[object Number]';
const regexpTag = '[object RegExp]';
const stringTag = '[object String]';
const symbolTag = '[object Symbol]';
```

在上面的集中类型中，我们简单将他们分为两类：

- 可以继续遍历的类型
- 不可以继续遍历的类型

我们分别为它们做不同的拷贝。更详细的写法请参考[如何写出一个惊艳面试官的深拷贝?](https://segmentfault.com/a/1190000020255831)，完整代码请参考[完整版代码](https://github.com/ConardLi/ConardLi.github.io/blob/master/demo/deepClone/src/clone_6.js)

#### 可继续遍历的类型

上面我们已经考虑的 `object`、 `array`都属于可以继续遍历的类型，因为它们内存都还可以存储其他数据类型的数据，另外还有 `Map`， `Set`等都是可以继续遍历的类型，这里我们只考虑这四种，如果你有兴趣可以继续探索其他类型。

有序这几种类型还需要继续进行递归，我们首先需要获取它们的初始化数据，例如上面的 `[]`和 `{}`，我们可以通过拿到 `constructor`的方式来通用的获取。

例如：`const target = {}`就是 `const target = new Object()`的语法糖。另外这种方法还有一个好处：因为我们还使用了原对象的构造方法，所以它可以保留对象原型上的数据，如果直接使用普通的 `{}`，那么原型必然是丢失了的。

下面，我们改写 `clone`函数，对可继续遍历的数据类型进行处理：

```js
const getType = obj => Object.prototype.toString.call(obj);

const mapTag = '[object Map]';
const setTag = '[object Set]';
const arrayTag = '[object Array]';
const objectTag = '[object Object]';
const argsTag = '[object Arguments]';

const canTraverse = {
    '[object Map]': true,
    '[object Set]': true,
    '[object Array]': true,
    '[object Object]': true,
    '[object Arguments]': true,
};

function deepCopy(obj, map = new WeakMap()) {
    // 简单数据类型 直接 = 赋值
    if(typeof obj !== "object" || obj === null) 
        return obj;
    let type = getType(obj);
	let res;
    if(!canTraverse[type]) {
        // 处理不能遍历的对象
        return;
    } else {
        // 这波操作相当关键，可以保证对象的原型不丢失！
        let ctor = obj.constructor;
        res = new ctor();
    }
    
    // map中有克隆过的对象,直接返回
    if (map.get(obj)) {
        return obj;
    }
    // map中没有克隆过的对象,进行存储
    map.set(obj, res);
    
    if(type === mapTag) {
        // 处理Map
        obj.forEach((item, key) => {
            res.set(deepCopy(key, map), deepCopy(item, map));
        })
    }

    if(type === setTag) {
        // 处理Set
        obj.forEach(item => {
            res.add(deepCopy(item, map));
        })
    }

    // 处理数组和对象
    for (let prop in obj) {
        if (obj.hasOwnProperty(prop)) {
            res[prop] = deepCopy(obj[prop], map);
        }
    }
    return res;
}

const map = new Map();
map.set('key', 'value');
map.set('ConardLi', 'code秘密花园');

const set = new Set();
set.add('ConardLi');
set.add('code秘密花园');

const target = {
    field1: 1,
    field2: undefined,
    field3: {
        child: 'child'
    },
    field4: [2, 4, 8],
    empty: null,
    map,
    set,
};

let res = deepCopy(target);
console.log(res);
console.log(res.map === target.map);
```

执行结果：

![preview](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210424153532.jpeg)

没有问题，继续处理其他类型：

#### 不可继续遍历的类型

其他剩余的类型我们把它们统一归类成不可处理的数据类型，我们依次进行处理：

`Bool`、 `Number`、 `String`、 `String`、 `Date`、 `Error`这几种类型我们都可以直接用构造函数和原始数据创建一个新对象：

```js
const boolTag = '[object Boolean]';
const numberTag = '[object Number]';
const stringTag = '[object String]';
const dateTag = '[object Date]';
const errorTag = '[object Error]';
const symbolTag = '[object Symbol]';
const regexpTag = '[object RegExp]';
const funcTag = '[object Function]';

function cloneNotTraverse(obj, tag) {
    // 构造函数
    const Ctor = obj.constructor;
    switch(tag) {
        case boolTag:
        case numberTag:
        case stringTag:
        case errorTag: 
        case dateTag:
            // 大部分都使用构造函数新建
            return new Ctor(obj);
        case symbolTag:
            return cloneSymbol(obj);
        case regexpTag:
            return cloneRegExp(obj);
        case funcTag:
            return cloneFunc(obj);
        default:
            return new Ctor(obj);
    }
}
```

克隆 `Symbol`类型：

```js
function cloneSymbol(obj) {
    return Object(Symbol.prototype.valueOf.call(obj));
}
```

克隆正则：

```js
function cloneRegExp(obj) {
    const { source, flags } = obj;
    return new obj.constructor(source, flags);
}
```

实际上还有很多数据类型我这里没有写到，有兴趣的话可以继续探索实现一下。

如下所示:

```js
const obj = new Boolean(false);
const Ctor = obj.constructor;
new Ctor(obj); // 结果为 Boolean {true} 而不是 false。
```

对于这样一个bug，我们可以对 Boolean 拷贝做最简单的修改， 调用valueOf: new obj.constructor(obj.valueOf())。

但实际上，这种写法是不推荐的。因为在ES6后不推荐使用【new 基本类型()】这 样的语法，所以es6中的新类型 Symbol 是不能直接 new 的，只能通过 new Object(SymbelType)。

因此我们接下来统一一下:

```js
const boolTag = '[object Boolean]';
const numberTag = '[object Number]';
const stringTag = '[object String]';
const dateTag = '[object Date]';
const errorTag = '[object Error]';
const symbolTag = '[object Symbol]';
const regexpTag = '[object RegExp]';
const funcTag = '[object Function]';

function cloneNotTraverse(obj, tag) {
    // 构造函数
    const Ctor = obj.constructor;
    switch(tag) {
        case boolTag:
            return new Object(Boolean.prototype.valueOf.call(obj));
        case numberTag:
            return new Object(Number.prototype.valueOf.call(obj));
        case stringTag:
            return new Object(String.prototype.valueOf.call(obj));
        case errorTag: 
        case dateTag:
            // 大部分都使用构造函数新建
            return new Ctor(obj);
        case symbolTag:
            return cloneSymbol(obj);
        case regexpTag:
            return cloneRegExp(obj);
        case funcTag:
            return cloneFunc(obj);
        default:
            return new Ctor(obj);
    }
}

function cloneSymbol(obj) {
    return Object(Symbol.prototype.valueOf.call(obj));
}

function cloneRegExp(obj) {
    const { source, flags } = target;
    return new obj.constructor(source, flags);
}
```

能写到这里，面试官已经看到了你考虑问题的严谨性，你对变量和类型的理解，对 `JS API`的熟练程度，相信面试官已经开始对你刮目相看了。

### 克隆函数

最后，我把克隆函数单独拎出来了，实际上克隆函数是没有实际应用场景的，两个对象使用一个在内存中处于同一个地址的函数也是没有任何问题的，我特意看了下 `lodash`对函数的处理：

```js
const isFunc = typeof value === 'function';
if (isFunc || !cloneableTags[tag]) {
    return object ? value : {};
}
```

可见这里如果发现是函数的话就会直接返回了，没有做特殊的处理，但是我发现不少面试官还是热衷于问这个问题的，而且据我了解能写出来的少之又少。。。

实际上这个方法并没有什么难度，主要就是考察你对基础的掌握扎实不扎实。

首先，我们可以通过 `prototype`来区分下箭头函数和普通函数，箭头函数是没有 `prototype`的。

我们可以直接使用 `eval`和函数字符串来重新生成一个箭头函数，注意这种方法是不适用于普通函数的。

我们可以使用正则来处理普通函数：

分别使用正则取出函数体和函数参数，然后使用 `new Function([arg1[, arg2[, ...argN]], ]functionBody)`构造函数重新构造一个新的函数：

```js
function cloneFunction(func) {
    // 箭头函数直接返回自身
    if(!func.prototype) return func;
    const bodyReg = /(?<={)(.|\n)+(?=})/m;
    const paramReg = /(?<=\().+(?=\)\s+{)/;
    const funcString = func.toString();
    // 分别匹配 函数参数 和 函数体
    const param = paramReg.exec(funcString);
    const body = bodyReg.exec(funcString);
    if(!body) return null;
    if (param) {
        const paramArr = param[0].split(',');
        return new Function(...paramArr, body[0]);
    } else {
        return new Function(body[0]);
    }
}
```

### 完整代码展示

最后，我们再来执行clone6.test.js对下面的测试用例进行测试：

```js
const getType = obj => Object.prototype.toString.call(obj);

const mapTag = '[object Map]';
const setTag = '[object Set]';
const arrayTag = '[object Array]';
const objectTag = '[object Object]';
const argsTag = '[object Arguments]';

const boolTag = '[object Boolean]';
const numberTag = '[object Number]';
const stringTag = '[object String]';
const dateTag = '[object Date]';
const errorTag = '[object Error]';
const symbolTag = '[object Symbol]';
const regexpTag = '[object RegExp]';
const funcTag = '[object Function]';

const canTraverse = {
    '[object Map]': true,
    '[object Set]': true,
    '[object Array]': true,
    '[object Object]': true,
    '[object Arguments]': true,
};

function deepCopy(obj, map = new WeakMap()) {
    // 简单数据类型 直接 = 赋值
    if(typeof obj !== "object" || obj === null) 
        return obj;
    let type = getType(obj);
	let res;
    if(!canTraverse[type]) {
        // 处理不能遍历的对象
        return cloneNotTraverse(obj, type);
    } else {
        // 这波操作相当关键，可以保证对象的原型不丢失！
        let ctor = obj.constructor;
        res = new ctor();
    }
    
    // map中有克隆过的对象,直接返回
    if (map.get(obj)) {
        return obj;
    }
    // map中没有克隆过的对象,进行存储
    map.set(obj, res);
    
    if(type === mapTag) {
        // 处理Map
        obj.forEach((item, key) => {
            res.set(deepCopy(key, map), deepCopy(item, map));
        })
    }

    if(type === setTag) {
        // 处理Set
        obj.forEach(item => {
            res.add(deepCopy(item, map));
        })
    }

    // 处理数组和对象
    for (let prop in obj) {
        if (obj.hasOwnProperty(prop)) {
            res[prop] = deepCopy(obj[prop], map);
        }
    }
    return res;
}

function cloneNotTraverse(obj, tag) {
    // 构造函数
    const Ctor = obj.constructor;
    switch(tag) {
        case boolTag:
            return new Object(Boolean.prototype.valueOf.call(obj));
        case numberTag:
            return new Object(Number.prototype.valueOf.call(obj));
        case stringTag:
            return new Object(String.prototype.valueOf.call(obj));
        case symbolTag:
            return new Object(Symbol.prototype.valueOf.call(obj));
        case errorTag: 
        case dateTag:
            // 大部分都使用构造函数新建
            return new Ctor(obj);
        case regexpTag:
            return cloneRegExp(obj);
        case funcTag:
            return cloneFunc(obj);
        default:
            return new Ctor(obj);
    }
}

function cloneFunction(func) {
    // 箭头函数直接返回自身
    if(!func.prototype) return func;
    const bodyReg = /(?<={)(.|\n)+(?=})/m;
    const paramReg = /(?<=\().+(?=\)\s+{)/;
    const funcString = func.toString();
    // 分别匹配 函数参数 和 函数体
    const param = paramReg.exec(funcString);
    const body = bodyReg.exec(funcString);
    if(!body) return null;
    if (param) {
        const paramArr = param[0].split(',');
        return new Function(...paramArr, body[0]);
    } else {
        return new Function(body[0]);
    }
}

function cloneSymbol(obj) {
    return Object(Symbol.prototype.valueOf.call(obj));
}

function cloneRegExp(obj) {
    const { source, flags } = target;
    return new obj.constructor(source, flags);
}

const map = new Map();
map.set('key', 'value');
map.set('ConardLi', 'code秘密花园');

const set = new Set();
set.add('ConardLi');
set.add('code秘密花园');

const target = {
    field1: 1,
    field2: undefined,
    field3: {
        child: 'child'
    },
    field4: [2, 4, 8],
    empty: null,
    map,
    set,
    bool: new Boolean(true),
    num: new Number(2),
    str: new String(2),
    symbol: Object(Symbol(1)),
    date: new Date(),
    reg: /\d+/,
    error: new Error(),
    func1: () => {
        console.log('code秘密花园');
    },
    func2: function (a, b) {
        return a + b;
    }
};

let res = deepCopy(target);
console.log(res);
```

执行结果：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210424185842.jpeg)



## 11.数组扁平化

面试被问到这题，只能说做出来一半

多维数组=>一维数组

```js
let arr = [1, [2, [3, [4, ,5]]], 6]; // -> [1, 2, 3, 4, 5, 6]
```

如何实现呢，思路非常简单：**我们要做的就是在数组中找到是数组类型的元素，然后将他们展开**。这就是实现数组拍平 `flat` 方法的关键思路。

有了思路，我们就需要解决实现这个思路需要克服的困难：

- **第一个要解决的就是遍历数组的每一个元素；**
- **第二个要解决的就是判断元素是否是数组；**
- **第三个要解决的就是将数组的元素展开一层；**

### 调用ES6中的flat方法

面试时机智如我先说了arr自带的flat方法

```js
function flat(arr) {
    return arr.flatten(Infinity);
}
```

哈哈，又写错了，正确的应该是

```js
function flat(arr) {
    return arr.flat(Infinity);
}
let arr = [1, [2, [3, [4, ,5]]], 6];
console.log(flat(arr));
// [1, 2, 3, 4, 5, 6]
```

面试官说就是要实现它，不要用自带的flat函数

### 正则表达式

我说可以用正则表达式，然后手写了

```js
function flat(arr) {
    return arr.toString().replace(/[|]/g, '').split(',');
}
let arr = [1, [2, [3, [4, ,5]]], 6];
console.log(flat(arr));
// ["1", "2", "3", "4", "", "5", "6"]
```

面试官提醒，正则表达式中`[`和`]`有什么作用

赶紧解释[]表示匹配中间对应的字符，用转义符才能实现匹配

```js
function flat(arr) {
    return arr.toString().split(',');
}
let arr = [1, [2, [3, [4, ,5]]], 6];
console.log(flat(arr));
// ["1", "2", "3", "4", "", "5", "6"]
```

面试官又说如果本身里面带字符串，字符串中有`,`就不正确，数字、字符串无法区分。

其实这种时候可以JSON实现

```js
function flat(arr) {
    return JSON.stringify(arr).replace(/\[|\]/g, '').split(',');
}
let arr = [1, [2, [3, [4, ,5]]], 6];
console.log(flat(arr));
```

使用JSON.parse还原为原先的格式

```js
function flat(arr) {
    let str = '[' + JSON.stringify(arr).replace(/\[|\]/g, '').split(',') + ']';
    return JSON.parse(str);
}
let arr = [1, [2, [3, [4, ,5]]], 6];
console.log(flat(arr));
// [1, 2, 3, 4, null, 5, 6]
```
###  递归
我终于决定使用递归的方案做了一版

```js
function flat(arr) {
    let res = [];
    for (let item of arr) {
        if (item.constructor === Array) {
            res.push(...flat(item));
        } else {
            res.push(item);
        }
    }
    return res;
}
let arr = [1, [2, [3, [4, ,5]]], 6];
console.log(flat(arr));
```

其中我还写了一下判断数组的方法，可以参见Array.isArray手写

面试官又说这样数组有什么情况如果有空位这种方法不管用

Cannot read property 'constructor' of undefined

查了查有可能的情况，发现是数组空位，ES5 大多数数组方法对空位的处理都会选择跳过空位包括：`forEach()`, `filter()`, `reduce()`, `every()` 和 `some()` 都会跳过空位。

**ES5 对空位的处理，非常不一致，大多数情况下会忽略空位。**

- `forEach()`, `filter()`, `reduce()`, `every()` 和`some()` 都会跳过空位。
- `map()` 会跳过空位，但会保留这个值。
- `join()` 和 `toString()` 会将空位视为 `undefined`，而`undefined` 和 `null` 会被处理成空字符串。

**ES6 明确将空位转为 `undefined`。**

- `entries()`、`keys()`、`values()`、`find()` 和 `findIndex()` 会将空位处理成 `undefined`。
- `for...of` 循环会遍历空位。
- `fill()` 会将空位视为正常的数组位置。
- `copyWithin()` 会连空位一起拷贝。
- 扩展运算符（`...`）也会将空位转为 `undefined`。
- `Array.from` 方法会将数组的空位，转为 `undefined`。

使用forEach实现数组遍历可破

```js
function flat(arr) {
    let res = [];
    arr.forEach(function (item){
        if (item.constructor === Array) {
        	// if (instanceof(item) === Array) {
            res.push(...flat(item));
        } else {
            res.push(item);
        }
    })
    return res;
}
let arr = [1, [2, [3, [4, ,5]]], 6];
console.log(flat(arr));
// [1, 2, 3, 4, 5, 6]
```

### 利用reduce函数迭代(原地展开)

使用reduce实现

```js
function flat(arr) {
    return arr.reduce((pre, cur) => {
    	return pre.concat(Array.isArray(cur) ? flat(cur) : cur);
    }, []);
}
let arr = [1, [2, [3, [4, ,5]]], 6];
console.log(flat(arr));
// [1, 2, 3, 4, 5, 6]
```

### 扩展运算符

```js
// 只要有一个元素有数组，那么循环继续
let arr = [1, [2, [3, [4, ,5]]], 6];
while (arr.some(Array.isArray)) {
    arr = [].concat(...arr);
}
console.log(arr);
//  [1, 2, 3, 4, empty, 5, 6]
```

更多牛逼方法可参考面试官连环追问：[数组拍平（扁平化） flat 方法实现](https://github.com/NieZhuZhu/Blog/issues/2)



## 12.数组去重

尽可能总结全，不过实战很可能会忘掉大部分，特别要注意NaN和{}

### 双层 for 循环

> 思想: 双重 `for` 循环是比较笨拙的方法，它实现的原理很简单：先定义一个包含原始数组第一个元素的数组，然后遍历原始数组，将原始数组中的每个元素与新数组中的每个元素进行比对，如果不重复则添加到新数组中，最后返回新数组；因为它的时间复杂度是O($n^2$)，如果数组长度很大，效率会很低

```js
function unique(arr) {
    for (let i = 0, len = arr.length; i < len; i++) {
        for (let j = i + 1; j < len; j++) {
            // 防止出现类型转换 如果是==NaN只有一个，null消失3
            if (arr[i] === arr[j]) {
                // splice 会改变数组长度，所以要将数组长度 len 和下标 j 减一
                arr.splice(j, 1);
                len--;
                j--;
            }
        }
    }
    return arr;
}

let arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 0, 0, 'a', 'a', {}, {}];
console.log(unique(arr));
// [1, "true", true, 15, false, undefined, null, NaN, NaN, "NaN", 0, "a", {…}, {…}]
```

NaN、{}没有去重

### 利用indexOf或者includes去重

> 新建一个空的结果数组，`for` 循环原数组，判断结果数组是否存在当前元素，如果有相同的值则跳过，不相同则`push`进数组Object

#### 使用indexOf判断

```js
function unique(arr) {
    let res = [];
    for (let i = 0; i < arr.length; i++) {
        if (res.indexOf(arr[i]) === -1) {
            res.push(arr[i]);
        }
    }
    return res;
}

let arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 0, 0, 'a', 'a', {}, {}];
console.log(unique(arr));
// [1, "true", true, 15, false, undefined, null, NaN, NaN, "NaN", 0, "a", {…}, {…}]
```

NaN、{}没有去重

#### 使用includes判断

```js
function unique(arr) {
    let res = [];
    for (let i = 0; i < arr.length; i++) {
        if (!res.includes(arr[i])) {
            res.push(arr[i]);
        }
    }
    return res;
}

let arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 0, 0, 'a', 'a', {}, {}];
console.log(unique(arr));
// [1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {…}, {…}]
```

 {}没有去重

### Array.filter() + indexOf

> 思想: 利用`indexOf`检测元素在数组中第一次出现的位置是否和元素现在的位置相等，如果不等则说明该元素是重复元素

```js
function unique (arr) {
	return arr.filter((item, index) => arr.indexOf(item) === index);
}

let arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 0, 0, 'a', 'a', {}, {}];
console.log(unique(arr));
// [1, "true", true, 15, false, undefined, null, "NaN", 0, "a", {…}, {…}]
```

 {}没有去重, NaN消失

### Array.reduce() + indexOf/includes

#### 使用includes判断

```js
function unique (arr) {
    return arr.reduce((prev, cur) => prev.includes(cur) ? prev : [...prev, cur], []);
}

let arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 0, 0, 'a', 'a', {}, {}];
console.log(unique(arr));
// [1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {…}, {…}]
```

{}没有去重

#### 使用indexOf判断

```js
function unique (arr) {
    return arr.reduce((prev, cur) => prev.indexOf(cur) !== -1 ? prev : [...prev, cur], []);
}

let arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 0, 0, 'a', 'a', {}, {}];
console.log(unique(arr));
// [1, "true", true, 15, false, undefined, null, NaN, NaN, "NaN", 0, "a", {…}, {…}]
```

NaN和{}没有去重

### 利用ES6 Set去重

```js
function unique (arr) {
    return Array.from(new Set(arr));
}

let arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 0, 0, 'a', 'a', {}, {}];
console.log(unique(arr));
// [1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {…}, {…}]
```

{}没有去重

#### 利用展开运算符简写

```js
function unique (arr) {
	return  [...new Set(arr)];
}

let arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 0, 0, 'a', 'a', {}, {}];
console.log(unique(arr));
// [1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {…}, {…}]
```

同样{}没有去重

### 利用Map数据结构去重

```js
function unique (arr) {
	let map = new Map();
    // 数组用于返回结果
    let res = new Array();
    for (let i = 0; i < arr.length; i++) {
        // 如果没有该key值
        if(!map.has(arr[i])) {
            res.push(arr[i]);
			map.set(arr[i], true);
		}
	}
	return res;
}

let arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 0, 0, 'a', 'a', {}, {}];
console.log(unique(arr));
// [1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {…}, {…}]
```

{}没有去重

### 利用hasOwnProperty

> 利用`hasOwnProperty` 判断是否存在对象属性这种方法是利用一个空的 Object 对象，我们把数组的值存成 Object 的 key 值。 1 和 '1' 是不同的，但是这种直接作为key会判断为同一个值，这是因为对象的键值只能是字符串，所以我们可以使用 `typeof item + item` 拼成字符串作为 key 值来避免这个问题：

使用filter实现

```js
function unique(arr) {
    var obj = {};
    return arr.filter(function(item){
    	return obj,hasOwnProperty(typeof item + item) ? false : (obj[typeof item + item] = true);
    })
}

let arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 0, 0, 'a', 'a', {}, {}];
console.log(unique(arr));
// [1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {…}]
```

同时实现NaN和{}去重，这也是最完美的一种去重了，更详细的可以参考冴羽大大的[JavaScript专题之数组去重](https://juejin.cn/post/6844903482093387783)



## 13.手写数组ES5常见方法

这个之前写过了，这里复习一下写法

### 参数说明

- callback 回调函数
- context 执行 callback时使用的 this 值
- current 数组中正在处理的元素
- index 当前索引
- array 源数组
- accumulator 累加器
- initialValue reduce或者reduceRight 第一次调用 callbackFn 函数时的第一个参数的值默认值
- self 自己实现的 this 对象

### 1.forEach 函数

**语法：** `arr.forEach(callback(current [, index [, array]])[, context])`

**方法功能：**回调参数为：每一项、索引、原数组, 对数组的每个元素执行一次给定的函数。

**返回：** undefined。

自定义函数：myForEach。

```js
Array.prototype.myForEach = function(callback, context) {
    if (typeof callback !== 'function') throw ('callback参数必须是函数');
    let self = this,
        len = self && element.length || 0;
    if (!context) context = self;
    for (let index = 0; index < len; index++) {
        callback.call(context, self[index], index, self);
    }
};
```

### 2.filter 函数

**语法：** `var newArray = arr.filter(callback(current[, index[, array]])[, context])`

**方法功能：** 创建一个新数组, 过滤掉回调函数返回值不为true的项,其包含通过所提供函数实现的测试的所有元素。

**返回：** 一个新的、由通过测试的元素组成的数组，如果没有任何数组元素通过测试，则返回空数组。

自定义函数：myFilter。

```js
Array.prototype.myFilter = function(callback, context) {
    if (typeof callback !== 'function') throw ('callback参数必须是函数');
    let self = this,
        len = self && self.length || 0,
        newArray = [];
    if (!context) context = self;
    for (let index = 0; index < len; index++) {
        if (callback.call(context, self[index], index, self)) newArray.push(self[index]);
    }
    return newArray;
};
```

### 3.find 函数

**语法：**`arr.find(callback[, context])`

**方法功能：** 返回数组中满足提供的测试函数的第一个元素的值。否则返回 undefined。

**返回：** 数组中第一个满足所提供测试函数的元素的值，否则返回 undefined。

自定义函数：myFind。

```js
Array.prototype.myFind = function(callback, context) {
    if (typeof callback !== 'function') throw ('callback参数必须是函数');
    let self = this,
        len = self && self.length || 0;
    if (!context) context = self;
    for (let index = 0; index < len; index++) {
        if (callback.call(context, self[index], index, self)) {
            return self[index];
        }
    }
    return undefined;
}
```

### 4.findIndex 函数

**语法：** `arr.findIndex(callback[, context])`

**方法功能：** 返回数组中满足提供的测试函数的第一个元素的索引。否则返回 -1。

**返回：** 数组中通过提供测试函数的第一个元素的索引。否则，返回-1。

自定义函数：myFindIndex。

```js
Array.prototype.myFindIndex = function(callback, context) {
    if (typeof callback !== 'function') throw ('callback参数必须是函数');
    let self = this,
        len = self && self.length || 0;
    if (!context) context = self;
    for (let index = 0; index < len; index++) {
        if (callback.call(context, self[index], index, self)) return index;
    }
    return -1;
}
```

### 5.fill函数

**语法：** `arr.fill(value[, start[, end]])`

**方法功能：** 用一个固定值填充一个数组中从起始索引到终止索引内的全部元素。不包括终止索引。

**返回：** 返回替换的值，原数组发生改变。

自定义函数：myFill。

```js
Array.prototype.myFill = function(value, start = 0, end) {
    let self = this,
        len = self && self.length || 0;
    end = end || len;
    let loopStart = start < 0 ? 0 : start, // 设置循环开始值
        loopEnd = end >= len ? len : end; // 设置循环结束值

    for (; loopStart < loopEnd; loopStart++) {
        self[loopStart] = value;
    }
    return self;
}
```

### 6.map 函数

**语法：** `var newArray = arr.map(function callback(current[, index[, array]]) {// Return self for newArray }[, context])`

**方法功能：** 创建一个新数组，其结果是该数组中的每个元素是调用一次提供的函数后的返回值。

**返回：** 测试数组中是不是至少有1个元素通过了被提供的函数测试。它返回的是一个Boolean类型的值。 一个由原数组每个元素执行回调函数的结果组成的新数组。

自定义函数：myMap。

```js
Array.prototype.myMap = function(callback, context) {
    if (typeof callback !== 'function') throw ('callback参数必须是函数');
    let self = this,
        len = self && self.length || 0,
        result = [];
    if (!context) context = self;
    for (let index = 0; index < len; index++) {
        result[index] = callback.call(context, self[index], index, self);
    }
    return result;
}
```

### 7.some 函数

**语法：** `arr.some(callback(current[, index[, array]])[, context])`

**方法功能：** 测试数组中是不是至少有1个元素通过了被提供的函数测试,回调函数返回值一个为true 结果就为true, 否则为false。它返回的是一个Boolean类型的值。

**返回：** 数组中有至少一个元素通过回调函数的测试就会返回true；所有元素都没有通过回调函数的测试返回值才会为false。

自定义函数：mySome。

```js
Array.prototype.mySome = function(callback, context) {
    if (typeof callback !== 'function') throw ('callback参数必须是函数');
    let self = this,
        len = self && self.length || 0;
    if (!context) context = self;
    for (let index = 0; index < len; index++) {
        if (callback.call(context, self[index], index, self)) return true;
    }
    return false;
}
```

### 8.every 函数

**语法：** `arr.every(callback(current[, index[, array]])[, context])`

**方法功能**：测试一个数组内的所有元素是否都能通过某个指定函数的测试,所有回调函数返回值都为true时 结果为true,否则为false。它返回一个布尔值。

**返回：** 如果回调函数的每一次返回都为 true 值，返回 true，否则返回 false。

自定义函数：myEvery。

```js
Array.prototype.myEvery = function(callback, context) {
    if (typeof callback !== 'function') throw ('callback参数必须是函数');
    let self = this,
        len = self && self.length || 0;
    if (!context) context = self;
    for(let index = 0; index < len; index++) {
        if (!callback.call(context, element[index], index, element)) return false;
    }
    return true;
}
```

### 9.reduce 函数

**语法：**`arr.reduce(callback(accumulator, current[, index[, array]])[, initialValue])`

**方法功能：**对数组中的每个元素执行一个由您提供的reducer函数(升序执行)，将其结果汇总为单个返回值。相比其他方法多了一个参数即上次调用的返回值，
最后一个回调函数的返回值为reduce的结果，可以指定累积的初始值，不指定初始值从第二项开始遍历

**返回：** 函数累计处理的结果。

自定义函数：myReduce。

```js
Array.prototype.myReduce = function(callback, initialValue) {
    if (typeof callback !== 'function') throw ('callback参数必须是函数');
    let self = this,
        len = self.length || 0;
    let result = initialValue ? initialValue : self[0]; // 不传默认取数组第一项
  	let index = initialValue ? 0 : 1;

    while (index < len) {
        if (index in self) result = callback(result, self[index], index, self);
        index++;
    }
    return result;
}
```

可以使用一些方法实现别的方法，如下面两个例子

### 使用reduce实现map

```js
Array.prototype.reduceToMap = function (handler) {
    return this.reduce((target, current, index) => {
        target.push(handler.call(this, current, index))
        return target;
    }, [])
};
```

### 使用reduce实现filter

```js
Array.prototype.reduceToFilter = function (handler) {
    return this.reduce((target, current, index) => {
        if (handler.call(this, current, index)) {
        	target.push(current);
        }
        return target;
    }, [])
};
```



## 14.实现数组原地反转

用了双指针，第三变量交换法

```js
function revert(arr, start, end) {
	while (start < end) {
		let temp = arr[start];
		arr[start] = arr[end];
		arr[end] = temp;
        start++;
        end--;
	}
}

let arr = [0, 1, 4, 9, 16, 25];
revert(arr, 2, 5);
console.log(arr);
// [0, 1, 25, 16, 9, 4]
```

有什么别的方法。

解构赋值，

```js
function revert(arr, start, end) {
	while (start < end) {
		[arr[start], arr[end]] = [arr[end], arr[start]];
        start++;
        end--;
	}
}

let arr = [0, 1, 4, 9, 16, 25];
revert(arr, 2, 5);
console.log(arr);
// [0, 1, 25, 16, 9, 4]
```

利用和或者位运算

```js
function revert(arr, start, end) {
	while (start < end) {
		arr[start] += arr[end];
        arr[end] = arr[start] - arr[end];
        arr[start] = arr[start] - arr[end];
        start++;
        end--;
	}
}

let arr = [0, 1, 4, 9, 16, 25];
revert(arr, 2, 5);
console.log(arr);
// [0, 1, 25, 16, 9, 4]
```

或者

```js
function revert(arr, start, end) {
	while (start < end) {
		arr[start] ^= arr[end];
        arr[end] ^= arr[start];
        arr[start] ^= arr[end];
        start++;
        end--;
	}
}

let arr = [0, 1, 4, 9, 16, 25];
revert(arr, 2, 5);
console.log(arr);
```



## 15.reduce的应用汇总

**reduce语法**

```js
array.reduce(function(total, currentValue, currentIndex, arr), initialValue);
/*
  total: 必需。初始值, 或者计算结束后的返回值。
  currentValue： 必需。当前元素。
  currentIndex： 可选。当前元素的索引；                     
  arr： 可选。当前元素所属的数组对象。
  initialValue: 可选。传递给函数的初始值，相当于total的初始值。
*/
```

> `reduceRight()` ,该方法用法与`reduce()`其实是相同的，只是遍历的顺序相反，它是从数组的最后一项开始，向前遍历到第一项

### 数组求和

基础版本

```js
const arr = [12, 34, 23];
const sum = arr.reduce((total, num) => total + num);
console.log(sum); // 69
```

设定初始值求和

```js
const arr = [12, 34, 23];
const sum = arr.reduce((total, num) => total + num, 10);  // 以10为初始值求和
console.log(sum); // 79
```

对象数组求和

```js
var result = [
    { subject: 'math', score: 88 },
    { subject: 'chinese', score: 95 },
    { subject: 'english', score: 80 }
];
const sum1 = result.reduce((accumulator, cur) => accumulator + cur.score, 0); 
console.log(sum1); // 263
const sum2 = result.reduce((accumulator, cur) => accumulator + cur.score, -10); // 总分扣除10分
console.log(sum2); // 253
```

### 数组最大值

```js
const arr = [23, 123, 342, 12];
const max = arr.reduce((pre, cur) => pre > cur ? pre : cur, Number.MIN_SAFE_INTEGER);
console.log(max); // 342
```

### 数组转对象

```js
var streams = [{name: '技术', id: 1}, {name: '设计', id: 2}];
var obj = streams.reduce((accumulator, cur) => {accumulator[cur.id] = cur; return accumulator;}, {});
```

### 数组扁平化

见[数组扁平化](#利用reduce函数迭代(原地展开))

### 数组去重

实现的基本原理如下：

① 初始化一个空数组
② 将需要去重处理的数组中的第1项在初始化数组中查找，如果找不到（空数组中肯定找不到），就将该项添加到初始化数组中
③ 将需要去重处理的数组中的第2项在初始化数组中查找，如果找不到，就将该项继续添加到初始化数组中
④ ……
⑤ 将需要去重处理的数组中的第n项在初始化数组中查找，如果找不到，就将该项继续添加到初始化数组中
⑥ 将这个初始化数组返回

见[数组去重](#Array.reduce() 加 indexOf/includes)

### 对象数组去重

根据每个对象的某一个具体属性来进行去重,利用高阶函数 `reduce` 进行去重， 这里只需要注意`initialValue`得放一个空数组[]，不然没法`push`

```js
let resources = [
    { name: "张三", age: "18" },
    { name: "张三", age: "19" },
    { name: "张三", age: "20" },
    { name: "李四", age: "19" },
    { name: "王五", age: "20" },
    { name: "赵六", age: "21" }
];
function dedup (data, key) {
    return data.reduce((res, cur) => {
        const keys = res.map(item => item[key]);
        // 如果临时对象没有就把这个名字加进去，同时把当前的这个对象加入到res中
        return keys.includes(cur[key]) ? res : [...res, cur];
    }, []);
}
console.log(dedup(resources, 'name'));
/*
    0: {name: "张三", age: "18"}
    1: {name: "李四", age: "19"}
    2: {name: "王五", age: "20"}
    3: {name: "赵六", age: "21"}
*/
```

### 求字符串中字母出现的次数

```js
const str = 'sfhjasfjgfasjuwqrqadqeiqsajsdaiwqdaklldflas-cmxzmnha';

const res = str.split('').reduce((count, next) => {
    count[next] ? count[next]++ : count[next] = 1;
    return count;
}, {});
console.log(res);
// 结果
/*
    -: 1
    a: 8
    c: 1
    d: 4
    e: 1
    f: 4
    g: 1
    h: 2
    i: 2
    j: 4
    k: 1
    l: 3
    m: 2
    n: 1
    q: 5
    r: 1
    s: 6
    u: 1
    w: 2
    x: 1
    z: 1
*/
```

### compose函数

> `redux compose` 源码实现

```js
function compose(...funs) {
    if (funs.length === 0) {
        return arg => arg;
    }
    if (funs.length === 1) {
       return funs[0];
    }
    return funs.reduce((a, b) => (...arg) => a(b(...arg)));
}

const partial = (fn, ...args) => (..._args) =>
	fn(...args, ..._args);
	
const partialRight = (fn, ...args) => (..._args) =>
	fn(..._args, ...args);
	
function add(x, y) {
	return x + y;
}

function pow(x, y) {
  	return Math.pow(x, y);
}

function double(x) {
  	return x * 2;
}

function multiply(x, y) {
	return x * y;
}

compose(
    console.log, 
    partial(add, 10),
    partialRight(pow, 3),
    partial(multiply, 5)
)(2); // 1010
```

或者使用reduceRight

```js
function compose(...funs) {
    if (funs.length === 0) {
        return arg => arg;
    }
    if (funs.length === 1) {
       return funs[0];
    }
    return funs.reduceRight((a, b) => (...arg) => b(a(...arg)))
}

const partial = (fn, ...args) => (..._args) =>
	fn(...args, ..._args);
	
const partialRight = (fn, ...args) => (..._args) =>
	fn(..._args, ...args);
	
function add(x, y) {
	return x + y;
}

function pow(x, y) {
  	return Math.pow(x, y);
}

function double(x) {
  	return x * 2;
}

function multiply(x, y) {
	return x * y;
}

compose(
    console.log, 
    partial(add, 10),
    partialRight(pow, 3),
    partial(multiply, 5)
)(2); // 1010
```

更多写法请参考[手写compose函数](#20.手写compose函数)

### 实现多维数组的回溯

 实现[['a', 'b'], ['n', 'm'], ['0', '1']] => ["an0", "an1", "am0", "am1", "bn0", "bn1", "bm0", "bm1"]

```js
function backtrack(arr) {
	return arr.reduce((prev, cur) => {
		let list = [];
		for (let i = 0; i < prev.length; i++) {
			for (let j = 0; j < cur.length; j++) {
				list.push(prev[i] + cur[j]);
			}
		}
        return list;
	}, [])
}

console.log(backtrack([['a', 'b'], ['n', 'm'], ['0', '1']]));
// ["an0", "an1", "am0", "am1", "bn0", "bn1", "bm0", "bm1"]
```

不适用reduce也可以实现

```js
function backtrack(arr) {
    let arr1 = [''];
    for(let i = 0; i < arr.length; i++) {
        arr1 = seq(arr1, arr[i]);
    }
    return arr1;
}
function seq(arr1, arr2) {
    let list = [];
    for(let i = 0; i < arr1.length; i++) {
        for(let j = 0; j < arr2.length; j++) {
            list.push(arr1[i] + arr2[j]);
  }
    }
    return list;
}

console.log(backtrack([['a', 'b'], ['n', 'm'], ['0', '1']]));
// ["an0", "an1", "am0", "am1", "bn0", "bn1", "bm0", "bm1"]
```



## 16.洗牌算法

最简单的一种形式，遍历的时候进行交换

```js
function shuffle(array) {
    const length = array.length;
    for (let i = 0; i < length; i++) {
        let random = Math.floor(length * Math.random());
        [array[i], array[random]] = [array[random], array[i]];
    }
}
let arr = Array.from(Array(100), (item, index) => index);
shuffle(arr);
console.log(arr);
```

公认成熟的洗牌算法(Fisher-Yates),简单的思路如下：

- 1. 定义一个数组，以数组的最后一个元素为基准点。
- 2. 在数组开始位置到基准点之间随机取一个位置，将所取位置上的元素和基准点上的元素互换。
- 3. 基准点左移一位。
- 4. 重复2，3步骤，直到基准点为数组的开始位置。

```js
function shuffle(arr) {
    let length = arr.length;
    for (let i = length - 1; i >= 0; i--) {
        let random = Math.floor(Math.random() * (i + 1)); // 生成起始位置到基准位置之间的随机位置，并将基准从结束位置不停左移。
        // es3实现
        // var newA = arr[i];
        // arr[i] = arr[random];
        // arr[random] = newA;
        // es6 实现
        [arr[i], arr[random]] = [arr[random], arr[i]]; // 本质为交换元素位置。
    }
    return arr;
}
let arr = Array.from(Array(100), (item, index) => index);
shuffle(arr);
console.log(arr);
```

更多方法请看[打造属于自己的underscore系列（六）- 洗牌算法](https://juejin.cn/post/6844903778315943949)



## 17.对象扁平化

 实现一个 objectFlat 函数，实现如下的转换功能

```js
const obj = {
    a: 1,
    b: [ 1, 2, { c: true }],
    c: { e: 2, f: 3 },
    g: null,
};
// 转换为
let objRes = {
    a: 1,
    "b[0]": 1,
    "b[1]": 2,
    "b[2].c": true,
    "c.e": 2,
    "c.f": 3,
    g: null,
};
```

我们从结果入手，可以知道我们需要对象进行遍历，把里面的属性值依次输出，所以我们可以知道核心方法体就是：传入对象的 key 值和 value，对 value 再进行递归遍历。

我们知道 js 的数据类型可以基础数据类型和引用数据类型，对于题目而言，基础数据类型无需再进行深层次遍历，引用数据类型需要再次进行递归。

```js
function objectFlat(obj = {}) {
    const res = {};
    function flat(value, key = '') {
        // 首先判断是基础数据类型还是引用数据类型
        if (typeof value !== "object" || value === null) {
            // 基础数据类型
            if (key) {
                res[key] = value;
            }
        } else if (Array.isArray(value)) { // 判断是数组
            for (let i = 0; i < value.length; i++) {
                flat(value[i], key + `[${i}]`);
            }
        } else { // 判断是对象
            let keys = Object.keys(value);
            keys.forEach(item => {
                flat(value[item], key ? `${key}.${item}` : `${item}`);
            })
            // 空对象
            if (!keys.length && key) {
                res[key] = {};
            }
        }
    }
    flat(obj);
    return res;
}

// 测试
const source = {
    a: {
        b: [
            1,
            2,
            {
                c: 1,
            	d: 2
            }
        ],
        e: 3
    },
    f: {
        g: 2
    }
};
console.log(objectFlat(source));
/*
 * a.b[0]: 1
 * a.b[1]: 2
 * a.b[2].c: 1
 * a.b[2].d: 2
 * a.e: 3
 * f.g: 2
 */
```



## 18.手写偏函数

一天在面试中，面试官给了我一道手写代码题

```js
/**
    * 实现函数 partialUsingArguments，调用之后满足如下条件：
    1、返回一个函数 result
    2、调用 result 之后，返回的结果与调用函数 fn 的结果一致
    3、fn 的调用参数为 partialUsingArguments 的第一个参数之后的全部参数以及 result 的调用参数
*/ 
```

我当时的第一版思路，将两个参数数组进行拼接，通过闭包返回结果，面试官提示如果参数为空，怎么办，我增加了args = args || [];这一句

```js
function partialUsingArguments(fn, ...args) {
    args = args || [];
    return function (..._args) {
        return fn(args.concat(_args));
    }
}
```

面试官说如果参数不是数组，是对象怎么办，提示ES6还有什么拼接方法，使用展开运算符

### 偏函数ES6常规写法

```js
function partialUsingArguments(fn, args) {
    return function (_args) {
        return fn(...args, ..._args);
    }
}
```

我当场问面试官，是不是函数柯里化，其实这和柯里化一样，叫作偏函数，和函数柯里化一样，都属于函数式编程的范畴。

我面试的那个问题本质上就是partial偏函数，和柯里化有点类似，所以没见过的我当场做完之后问了一下是不是函数的柯里化，想想也知道不是，知识两者类似。

### 偏函数ES6简化写法

```js
const partialUsingArguments = (fn, ...args) => (..._args) =>
	fn(...args, ..._args);
```



## 19.函数柯里化

定义

把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数且返回结果的新函数的技术

通俗易懂的解释：用闭包把参数保存起来，当参数的数量足够执行函数了，就开始执行函数。

### 函数柯里化ES6常规写法

```js
function curry(fn, ...args) {
	const len = fn.length;
	if (args.length >= len) {
    	// 判断当前函数传入的参数是否大于或等于fn需要参数的数量，如果是，直接执行fn
    	return fn(...args);
    } else return function(..._args) {
    	// 如果传入参数数量不够，返回一个闭包，暂存传入的参数，并重新返回curry函数
		return curry.call(this, fn, ...args, ..._args);
	}
}

function multiFn(a, b, c) {
    return a * b * c;
}

var multi = curry(multiFn);

console.log(multi(2)(3)(4)); // 24
console.log(multi(3, 4, 5)); // 60
console.log(multi(4)(5, 6)); // 120
console.log(multi(5, 6)(7)); // 210
```

经过一些简化的方法

```js
function curry(fn, ...args) {
	if (args.length >= fn.length) {
    	// 判断当前函数传入的参数是否大于或等于fn需要参数的数量，如果是，直接执行fn
    	return fn(...args);
    } else {
    	// 如果传入参数数量不够，返回一个闭包，暂存传入的参数，并重新返回curry函数
    	return (..._args) => curry(fn, ...args, ..._args);
    }
}

function multiFn(a, b, c) {
    return a * b * c;
}

var multi = curry(multiFn);

console.log(multi(2)(3)(4)); // 24
console.log(multi(3, 4, 5)); // 60
console.log(multi(4)(5, 6)); // 120
console.log(multi(5, 6)(7)); // 210
```

### 函数柯里化ES6简化写法

```js
const curry = (fn, arr = []) => (..._args) => (
	args => args.length === fn.length ? fn(...args) : curry(fn, args);
)([...arr, ..._args])

function multiFn(a, b, c) {
    return a * b * c;
}

var multi = curry(multiFn);

console.log(multi(2)(3)(4)); // 24
console.log(multi(3, 4, 5)); // 60
console.log(multi(4)(5, 6)); // 120
console.log(multi(5, 6)(7)); // 210
```



## 20.手写compose函数

如果我们想，对一个值执行一系列操作，并打印出来，考虑以下代码：

```js
// import { partial, partialRight } from 'lodash';
const partial = (fn, ...args) => (..._args) =>
	fn(...args, ..._args);
	
const partialRight = (fn, ...args) => (..._args) =>
	fn(..._args, ...args);
	
function add(x, y) {
	return x + y;
}

function pow(x, y) {
  	return Math.pow(x, y);
}

function double(x) {
  	return x * 2;
}

const add10 = partial(add, 10);
const pow3 = partialRight(pow, 3);

console.log(add10(pow3(double(2)))); // 74
```

备注：`partialRight`和`partial`见名知意，相当于是彼此的镜像函数。

> _.partialRight: This method is like _.partial except that partially applied arguments are appended to the arguments it receives.

原文从lodash导入，我自己仿照partial重写了一版。无需否认，这段示例代码的确毫无意义。但是为了达成这一系列操作，我最终执行了这一长串嵌套了四层的函数调用：`console.log(add10(pow3(double(2))))`。（说实话，我的确觉得有点难以阅读了...），如果更长了，怎么办？可能有的同学会给出以下答案:

```js
function mixed(x) {
	return add10(pow3(double(2)));
}

// import { partial, partialRight } from 'lodash';
const partial = (fn, ...args) => (..._args) =>
	fn(...args, ..._args);
	
const partialRight = (fn, ...args) => (..._args) =>
	fn(..._args, ...args);
	
function add(x, y) {
	return x + y;
}

function pow(x, y) {
  	return Math.pow(x, y);
}

function double(x) {
  	return x * 2;
}

const add10 = partial(add, 10);
const pow3 = partialRight(pow, 3);

console.log(mixed(2)); // 74
```

的确，看似好了点，但是也只是将这个冗长的调用封装了一下而已。会不会有更好的做法？

### 基于栈的compose函数

```js
function compose(...args) {
    return function(result) {
        const funcs = [...args];
        while(funcs.length > 0) {
            result = funcs.pop()(result);
        }
        return result;
    };
}

// import { partial, partialRight } from 'lodash';
const partial = (fn, ...args) => (..._args) =>
	fn(...args, ..._args);
	
const partialRight = (fn, ...args) => (..._args) =>
	fn(..._args, ...args);
	
function add(x, y) {
	return x + y;
}

function pow(x, y) {
  	return Math.pow(x, y);
}

function double(x) {
  	return x * 2;
}

const add10 = partial(add, 10);
const pow3 = partialRight(pow, 3);
compose(console.log, add10, pow3, double)(2) // 74
```

欧耶！我们通过实现了一个简单的`compose`函数，然后发现调用的过程`compose(console.log, add10, pow3, double)(2)`竟然变得如此优雅！多个函数的调用从代码阅读上，多层嵌套被拍平变成了线性！（当然实际上本质上还是嵌套的函数调用的）。

### 使用函数reduce的compose函数

当然，关于`compose`的更加函数式的实现如下：

```js
function compose(...funcs) {
    return result => funcs
        .reverse()
        .reduce((result, fn) => fn(result), result);
}

// import { partial, partialRight } from 'lodash';
const partial = (fn, ...args) => (..._args) =>
	fn(...args, ..._args);
	
const partialRight = (fn, ...args) => (..._args) =>
	fn(..._args, ...args);
	
function add(x, y) {
	return x + y;
}

function pow(x, y) {
  	return Math.pow(x, y);
}

function double(x) {
  	return x * 2;
}

const add10 = partial(add, 10);
const pow3 = partialRight(pow, 3);
compose(console.log, add10, pow3, double)(2); // 74
```

那么有同学可能也发现了，上述`compose`之后的函数是只可以传递一个参数的。这无疑显得有点蠢？难道不可以优化实现支持多个参数么？

考虑以下代码：

```js
/* function compose(...funcs) {
    return funcs
        .reverse()
        .reduce((fn1, fn2) => (...args) => fn2(fn1(...args)));
} */

function compose(...funcs) {
    return funcs
        .reduce((fn1, fn2) => (...args) => fn1(fn2(...args)));
}

// import { partial, partialRight } from 'lodash';
const partial = (fn, ...args) => (..._args) =>
	fn(...args, ..._args);
	
const partialRight = (fn, ...args) => (..._args) =>
	fn(..._args, ...args);
	
function add(x, y) {
	return x + y;
}

function pow(x, y) {
  	return Math.pow(x, y);
}

function double(x) {
  	return x * 2;
}

const add10 = partial(add, 10);
const pow3 = partialRight(pow, 3);
compose(console.log, add10, pow3, double)(2); // 74
```

细心观察，通过将参数传递进行懒执行，从而巧妙的完成了这个任务！示例如下：

```js
function compose(...funcs) {
    return funcs
        .reduce((fn1, fn2) => (...args) => fn1(fn2(...args)));
}

// import { partial, partialRight } from 'lodash';
const partial = (fn, ...args) => (..._args) =>
	fn(...args, ..._args);
	
const partialRight = (fn, ...args) => (..._args) =>
	fn(..._args, ...args);
	
function add(x, y) {
	return x + y;
}

function pow(x, y) {
  	return Math.pow(x, y);
}

function double(x) {
  	return x * 2;
}

const add10 = partial(add, 10);
const pow3 = partialRight(pow, 3);

function multiply(x, y) {
	return x * y;
}

compose(
	console.log, add10, pow3, multiply
)(2, 5); // 1010
```

当然上述代码最终也可以这么写：

```js
function compose(...funcs) {
    return funcs
        .reduce((fn1, fn2) => (...args) => fn1(fn2(...args)));
}

// import { partial, partialRight } from 'lodash';
const partial = (fn, ...args) => (..._args) =>
	fn(...args, ..._args);
	
const partialRight = (fn, ...args) => (..._args) =>
	fn(..._args, ...args);
	
function add(x, y) {
	return x + y;
}

function pow(x, y) {
  	return Math.pow(x, y);
}

function double(x) {
  	return x * 2;
}

function multiply(x, y) {
	return x * y;
}

compose(
    console.log, 
    partial(add, 10),
    partialRight(pow, 3),
    partial(multiply, 5)
)(2); // 1010
```

### 使用递归来实现compose

递归版本的`compose`本质上更接近概念，但是可能也会让人难以理解。了解一下也不错~

代码如下：

```js
function compose(...funcs) {
    const [fn1, fn2, ...rest] = funcs.reverse();

    function composed(...args) {
    	return fn2(fn1(...args));
    };

    if (rest.length === 0) return composed;

    return compose(
        ...rest.reverse(),
        composed
    );
}

// import { partial, partialRight } from 'lodash';
const partial = (fn, ...args) => (..._args) =>
	fn(...args, ..._args);
	
const partialRight = (fn, ...args) => (..._args) =>
	fn(..._args, ...args);
	
function add(x, y) {
	return x + y;
}

function pow(x, y) {
  	return Math.pow(x, y);
}

function double(x) {
  	return x * 2;
}

const add10 = partial(add, 10);
const pow3 = partialRight(pow, 3);

function multiply(x, y) {
	return x * y;
}

compose(
	console.log, add10, pow3, multiply
)(2, 5); // 1010
```



## 21.实现 (5).add(3).minus(2) 功能

> 例： 5 + 3 - 2，结果为 6

```js
Number.prototype.add = function(n) {
    return this.valueOf() + n;
}
Number.prototype.minus = function(n) {
    return this.valueOf() - n;
}
console.log((5).add(3).minus(2)); // 6
```



##  22.实现一个 add 函数

满足以下功能

```js
add(1); // 1
add(1)(2); // 3
add(1)(2)(3); // 6
add(1)(2, 3); // 6
add(1, 2)(3); // 6
add(1, 2, 3); // 6
```
需要结合上述的偏函数和toString()方法实现功能,打印函数时会自动调用 `toString()`方法
```js
function add(...args) {
    let fn = function(..._args) {
        return add(...args, ..._args);
    }
    
    fn.toString = function() {
        return args.reduce((a, b) => a + b);
    }
    
    return fn;
}
console.log(add(1)); // 1
console.log(add(1)(2)); // 3
console.log(add(1)(2)(3)); // 6
console.log(add(1)(2, 3)); // 6
console.log(add(1, 2)(3)); // 6
console.log(add(1, 2, 3)); // 6
```



## 23.计算两个数组的交集

> 例如：给定 nums1 = [1, 2, 2, 1]，nums2 = [2, 2]，返回 [2, 2]。

排序+双指针

```js
function union (nums1, nums2) {
    nums1.sort((x, y) => x - y);
    nums2.sort((x, y) => x - y);
    const length1 = nums1.length, length2 = nums2.length;
    // 双指针
    let index1 = 0, index2 = 0;
    const intersection = [];
    while (index1 < length1 && index2 < length2) {
        const num1 = nums1[index1], num2 = nums2[index2];
        if (num1 === num2) {
            intersection.push(num1);
            index1++;
            index2++;
        } else if (num1 < num2) {
            index1++;
        } else {
            index2++;
        }
    }
    return intersection;
};

 const a = [1, 2, 2, 1];
 const b = [2, 3, 2];
 console.log(union(a, b)); // [2, 2]
```



## 24.手写对象深度比较

> 思路：深度比较两个对象，就是要深度比较对象的每一个元素。=> 递归

- 递归退出条件：
  - 被比较的是两个值类型变量，直接用“===”判断
  - 被比较的两个变量之一为null，直接判断另一个元素是否也为null
- 提前结束递推：
  - 两个变量keys数量不同
  - 传入的两个参数是同一个变量
- 递推工作： - 深度比较每一个key

```js
function isEqual(obj1, obj2){
    // 其中一个为值类型或null
	if (!isObject(obj1) || !isObject(obj2)) return obj1 === obj2;

    // 判断是否两个对象是同一个变量
    if(obj1 === obj2) return true;

    // 判断keys数是否相等
    const obj1Keys = Object.keys(obj1);
    const obj2Keys = Object.keys(obj2);
    if(obj1Keys.length !== obj2Keys.length) return false;

    // 深度比较每一个key
    for(let key of obj1Keys){
    	// 递归查询
    	if (!isEqual(obj1[key], obj2[key])) return false;
    }

    return true;
}
```



## 25.扁平数组转树状结构

怎么进行格式转换，将data转换成result形式（手写代码）

```js
const data = [
    { id: 10, parentId: 0, text: "一级菜单-1" }, 
    { id: 20, parentId: 0, text: "一级菜单-2" },
    { id: 30, parentId: 20, text: "二级菜单-3" },
    { id: 25, parentId: 30, text: "三级菜单-25" },
    { id: 35, parentId: 30, text: "三级菜单-35" }
];

let result = [
  {
    id: 10,
    text: '一级菜单-1',
    parentId: 0
  },
  {
    id: 20,
    text: '一级菜单-2',
    parentId: 0,
    children: [
      {
        id: 10,
        text: '一级菜单-3',
        parentId: 20,
        children: [...]
      }
    ]
  }
];
```

一开始以为只有一层子节点，打算先把有子节点的放入，再遍历有父节点的，写了一半重新理了理思路，写了以下的代码，先根据id从小到大排序，反着遍历，将子节点塞进父节点的children数组中，这是面试当场写的代码。

```js
function conver(data) {
    data.sort((a, b) => a.parentId - b.parentId);
    for (let i = data.length - 1; i >= 0; i--) {
        for (let j = i - 1; j >= 0; j--) {
            if (findParent(data[i], data[j])) {
                data.slice(i, 1);
                break;
            }
        }
    }
    return data;
    function findParent(a, b) {
        if (a.parentId == b.id) {
            b.children = b.children || [];
            b.child.push({...a});
            return true;
        }
        return false;
    }
}
```
代码有小错，面试官放我过了，毕竟总算搞对了思路，将代码进行纠错和改进，能正常使用了
```js
const data = [
    { id: 10, parentId: 0, text: "一级菜单-1" }, 
    { id: 20, parentId: 0, text: "一级菜单-2" },
    { id: 30, parentId: 20, text: "二级菜单-3" },
    { id: 25, parentId: 30, text: "三级菜单-25" },
    { id: 35, parentId: 30, text: "三级菜单-35" }
];
function convert(data) {
    data.sort((a, b) => a.parentId - b.parentId);
    for (let i = data.length - 1; i >= 0; i--) {
    	if (data[i].parentId === 0) break;
        for (let j = i - 1; j >= 0; j--) {
            if (findParent(i, j)) {
                break;
            }
        }
    }
    return data;
    function findParent(a, b) {
        if (data[a].parentId == data[b].id) {
            data[b].children = data[b].children || [];
            data[b].children.push(data.splice(a, 1));
            return true;
        }
        return false;
    }
}
console.log(convert(data));
```

想要多转换方法，可以参考[JS树形结构处理](https://juejin.cn/post/6918634850396307469)



## 26.防抖(debounce)

不管事件触发频率多高，一定在事件触发`n`秒后才执行，如果你在一个事件触发的 `n` 秒内又触发了这个事件，就以新的事件的时间为准，`n`秒后才执行，总之，触发完事件 `n` 秒内不再触发事件，`n`秒后再执行。

在前端开发中会遇到一些频繁的事件触发，比如：

1. window 的 resize、scroll
2. mousedown、mousemove
3. keyup、keydown
   ……

为此，我们举个示例代码来了解事件如何频繁的触发：

我们写个 `index.html` 文件：

```html
<!DOCTYPE html>
<html lang="zh-cmn-Hans">

<head>
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=edge, chrome=1">
    <title>debounce</title>
    <style>
        #container{
            width: 100%;
            height: 200px;
            line-height: 200px;
            text-align: center;
            color: #fff;
            background-color: #444;
            font-size: 30px;
        }
    </style>
</head>

<body>
    <div id="container"></div>
    <script src="debounce.js"></script>
</body>

</html>
```

`debounce.js` 文件的代码如下：

```js
var count = 1;
var container = document.getElementById('container');

function getUserAction() {
    container.innerHTML = count++;
};

container.onmousemove = getUserAction;
```

我们来看看效果：

![debounce](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210428112307.gif)

从左边滑到右边就触发了 165 次 getUserAction 函数！

因为这个例子很简单，所以浏览器完全反应的过来，可是如果是复杂的回调函数或是 ajax 请求呢？假设 1 秒触发了 60 次，每个回调就必须在 1000 / 60 = 16.67ms 内完成，否则就会有卡顿出现。

为了解决这个问题，一般有两种解决方案：

1. debounce 防抖
2. throttle 节流

### 防抖是什么

今天重点讲讲防抖的实现。

防抖的原理就是：你尽管触发事件，但是我一定在事件触发 n 秒后才执行，如果你在一个事件触发的 n 秒内又触发了这个事件，那我就以新的事件的时间为准，n 秒后才执行，总之，就是要等你触发完事件 n 秒内不再触发事件，我才执行，真是任性呐!

防抖和节流的概念都比较简单，所以我们就不在“防抖节流是什么”这个问题上浪费过多篇幅了，简单点一下：

> 防抖，即`短时间内大量触发同一事件，只会执行一次函数`，实现原理为`设置一个定时器，约定在xx毫秒后再触发事件处理，每次触发事件都会重新设置计时器，直到xx毫秒内无第二次操作`，防抖常用于搜索框/滚动条的监听事件处理，如果不做防抖，每输入一个字/滚动屏幕，都会触发事件处理，造成性能浪费。
>
> 不管事件触发频率多高，一定在事件触发`n`秒后才执行，如果你在一个事件触发的 `n` 秒内又触发了这个事件，就以新的事件的时间为准，`n`秒后才执行，总之，触发完事件 `n` 秒内不再触发事件，`n`秒后再执行。

**适用场景：**

> 按钮提交场景：防止多次提交按钮，只执行最后提交的一次 服务端验证场景：表单验证需要服务端配合，只执行一段连续的输入事件的最后一次，还有搜索联想词功能类似

### 防抖代码（第一版）

根据这段表述，我们可以写第一版的代码：

```js
// 第一版
function debounce(func, wait) {
    var timeout;
    return function () {
        clearTimeout(timeout);
        timeout = setTimeout(func, wait);
    }
}
```

如果我们要使用它，以最一开始的例子为例：

```js
container.onmousemove = debounce(getUserAction, 1000);
```

现在随你怎么移动，反正你移动完 1000ms 内不再触发，我才执行事件。看看使用效果：

![debounce 第一版](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210428111557.gif)

顿时就从 165 次降低成了 1 次!

棒棒哒，我们接着完善它。

### this指向（第二版）

如果我们在 `getUserAction` 函数中 `console.log(this)`，在不使用 `debounce` 函数的时候，`this` 的值为：

```html
<div id="container"></div>
```

但是如果使用我们的 debounce 函数，this 就会指向 Window 对象！

所以我们需要将 this 指向正确的对象。

我们修改下代码：

```js
// 第二版
// func是用户传入需要防抖的函数
// wait是等待时间
function debounce(func, wait) {
	// 缓存一个定时器id
    let timer = 0;
    // 这里返回的函数是每次用户实际调用的防抖函数
    // 如果已经设定过定时器了就清空上一次的定时器
    // 开始一个新的定时器，延迟执行用户传入的方法
    return function () {
        let context = this;
        if (timer) clearTimeout(timer);
        timer = setTimeout(function(){
            func.apply(context);
        }, wait);
    }
}
// 利用箭头函数改变this指向
function debounce(func, wait = 50) {
    // 缓存一个定时器id
    let timer = 0;
    // 这里返回的函数是每次用户实际调用的防抖函数
    // 如果已经设定过定时器了就清空上一次的定时器
    // 开始一个新的定时器，延迟执行用户传入的方法
    return function() {
        if (timer) clearTimeout(timer);
        timer = setTimeout(() => {
            func.apply(this);
        }, wait);
    }
}
```

现在 this 已经可以正确指向了。让我们看下个问题：

### event 对象（第三版）

JavaScript 在事件处理函数中会提供事件对象 event，我们修改下 getUserAction 函数：

```js
function getUserAction(e) {
    console.log(e);
    container.innerHTML = count++;
};
```

如果我们不使用 debouce 函数，这里会打印 MouseEvent 对象，如图所示：

![MouseEvent](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210428120949.png)

但是在我们实现的 debounce 函数中，却只会打印 undefined!

所以我们再修改一下代码：

```js
// 第三版
// func是用户传入需要防抖的函数
// wait是等待时间
function debounce(func, wait = 50) {
    // 缓存一个定时器id
    let timer = 0;
    // 这里返回的函数是每次用户实际调用的防抖函数
    // 如果已经设定过定时器了就清空上一次的定时器
    // 开始一个新的定时器，延迟执行用户传入的方法
    return function(...args) {
        if (timer) clearTimeout(timer);
        timer = setTimeout(() => {
            func.apply(this, args);
        }, wait);
    }
}
```

### 返回值（第四版，已经完成基本功能）

再注意一个小点，我们要返回函数的执行结果

```js
// 第四版
// func是用户传入需要防抖的函数
// wait是等待时间
function debounce(func, wait = 50) {
    // 缓存一个定时器id
    let timer = 0, res;
    // 这里返回的函数是每次用户实际调用的防抖函数
    // 如果已经设定过定时器了就清空上一次的定时器
    // 开始一个新的定时器，延迟执行用户传入的方法
    return function(...args) {
        if (timer) clearTimeout(timer);
        timer = setTimeout(() => {
            res = func.apply(this, args);
        }, wait);
        return res;
    }
}
```

到此为止，我们修复了三个小问题：

1. this 指向
2. event 对象
3. 返回值

### 立刻执行（第五版）

这个时候，代码已经很是完善，但是为了让这个函数更加完善，我们接下来思考一个新的需求。

这个需求就是：

我不希望非要等到事件停止触发后才执行，我希望立刻执行函数，然后等到停止触发n秒后，才可以重新触发执行。

想想这个需求也是很有道理的嘛，那我们加个 immediate 参数判断是否是立刻执行。

```js
// 第五版
// func是用户传入需要防抖的函数
// wait是等待时间
function debounce(func, wait = 50, immediate) {
    // 缓存一个定时器id
    let timer = 0, res;
    // 这里返回的函数是每次用户实际调用的防抖函数
    // 如果已经设定过定时器了就清空上一次的定时器
    // 开始一个新的定时器，延迟执行用户传入的方法
    return function(...args) {
        if (timer) clearTimeout(timer);
        if (immediate) {
            // 如果已经执行过，不再执行
            let callNow = !timer;
            timer = setTimeout(() => {
                if (timer) clearTimeout(timer);
            }, wait);
            if (callNow) res = func.apply(this, args);
        } else {
            timer = setTimeout(() => {
                res = func.apply(this, args);
            }, wait);
        }
        return res;
    }
}
```

![debounce-4](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210808001039.gif)

### 取消

最后我们再思考一个小需求，我希望能取消 debounce 函数，比如说我 debounce 的时间间隔是 10 秒钟，immediate 为 true，这样的话，我只有等 10 秒后才能重新触发事件，现在我希望有一个按钮，点击后，取消防抖，这样我再去触发，就可以又立刻执行啦，是不是很开心？

为了这个需求，我们写最后一版的代码：

```js
// 第六版
// func是用户传入需要防抖的函数
// wait是等待时间
function debounce(func, wait = 50, immediate) {
    // 缓存一个定时器id
    let timer = 0, res;
    // 这里返回的函数是每次用户实际调用的防抖函数
    // 如果已经设定过定时器了就清空上一次的定时器
    // 开始一个新的定时器，延迟执行用户传入的方法
    let debounced = function(...args) {
        if (timer) clearTimeout(timer);
        if (immediate) {
            // 如果已经执行过，不再执行
            let callNow = !timeout;
            timer = setTimeout(() => {
                if (timer) clearTimeout(timer);
            }, wait);
            if (callNow) res = func.apply(this, args);
        } else {
            timer = setTimeout(() => {
                res = func.apply(this, args);
            }, wait);
        }
        return res;
    }
    
    debounced.cancel = function() {
        clearTimeout(timer);
    };

    return debounced;
}
```

![debounce-cancel](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210808000649.gif)

功能更丰富的防抖函数请参考[JavaScript专题之跟着underscore学防抖](https://juejin.cn/post/6844903480239325191)



## 27.节流(throttle)

### 节流是什么

节流规定在一个单位时间内，只能触发一次函数。如果这个单位时间内触发多次函数，只有一次生效

防抖是`延迟执行`，而节流是`间隔执行`，函数节流即`每隔一段时间就执行一次`，实现原理为`设置一个定时器，约定xx毫秒后执行事件，如果时间到了，那么执行函数并重置定时器`，和防抖的区别在于，防抖每次触发事件都重置定时器，而节流在定时器到时间后再清空定时器。规定在一个单位时间内，只能触发一次函数。如果这个单位时间内触发多次函数，只有一次生效。

节流的原理很简单：

如果你持续触发事件，每隔一段时间，只执行一次事件。

根据首次是否执行以及结束后是否执行，效果有所不同，实现的方式也有所不同。
我们用 leading 代表首次是否执行，trailing 代表结束后是否再执行一次。

关于节流的实现，有两种主流的实现方式，一种是使用时间戳，一种是设置定时器。

**适用场景：**

> - 拖拽场景：固定时间内只执行一次，防止超高频次触发位置变动
> - 缩放场景：监控浏览器`resize`
> - 动画场景：避免短时间内多次触发动画引起性能问题

### 时间戳版代码

让我们来看第一种方法：使用时间戳，当触发事件的时候，我们取出当前的时间戳，然后减去之前的时间戳(最一开始值设为 0 )，如果大于设置的时间周期，就执行函数，然后更新时间戳为当前的时间戳，如果小于，就不执行。

看了这个表述，是不是感觉已经可以写出代码了…… 让我们来写第一版的代码：

```js
// 第一版
// func是用户传入需要防抖的函数
// wait是等待时间
function throttle (func, wait = 50) {
    // 上一次执行该函数的时间
    let lastTime = 0;
    return function (...args) {
        // 当前时间
        let now = +new Date();
        // 将当前时间和上一次执行函数时间对比
        // 如果差值大于设置的等待时间就执行函数
        if (now - lastTime > wait) {
            lastTime = now;
            func.apply(this, args);
        }
    }
}

// 使用方法：定时器
setInterval(
    throttle(() => {
        console.log(1);
    }, 500), 1
);
```

使用两个时间戳`prev旧时间戳`和`now新时间戳`，每次触发事件都判断二者的时间差，如果到达规定时间，执行函数并重置旧时间戳

例子依然是用讲 debounce 中的例子，如果你要使用：

```js
container.onmousemove = throttle(getUserAction, 1000);
```

效果演示如下：

![使用时间戳](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210428141501.gif)

我们可以看到：当鼠标移入的时候，事件立刻执行，每过 1s 会执行一次，如果在 4.2s 停止触发，以后不会再执行事件。

### 定时器版代码

接下来，我们讲讲第二种实现方式，使用定时器。

当触发事件的时候，我们设置一个定时器，再触发事件的时候，如果定时器存在，就不执行，直到定时器执行，然后执行函数，清空定时器，这样就可以设置下个定时器。

```js
// 第二版
// func是用户传入需要防抖的函数
// wait是等待时间
function throttle(func, wait = 50) {
    // 上一次执行该函数的时间
    let timer = null;
    return function(...args) {
        let context = this;
        if (!timer) {
            timer = setTimeout(function() {
                timer = null;
                func.apply(context, args);
            }, wait);
        }
    }
}
// 或者采用箭头函数
function throttle(func, wait = 50) {
    // 上一次执行该函数的时间
    let timer = null;
    return function(...args) {
        if (!timer) {
            timer = setTimeout(() => {
                timer = null;
                func.apply(this, args);
            }, wait);
        }
    }
}
// 使用方法：定时器
setInterval(
    throttle(() => {
        console.log(1);
    }, 500),
    1
);
```

为了让效果更加明显，我们设置 wait 的时间为 3s，效果演示如下：

![使用定时器](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210428144007.gif)

我们可以看到：当鼠标移入的时候，事件不会立刻执行，晃了 3s 后终于执行了一次，此后每 3s 执行一次，当数字显示为 3 的时候，立刻移出鼠标，相当于大约 9.2s 的时候停止触发，但是依然会在第 12s 的时候执行一次事件。

所以比较两个方法：

1. 第一种事件会立刻执行，第二种事件会在 n 秒后第一次执行
2. 第一种事件停止触发后没有办法再执行事件，第二种事件停止触发后依然会再执行一次事件

### 双剑合璧

那我们想要一个什么样的呢？

有人就说了：我想要一个有头有尾的！就是鼠标移入能立刻执行，停止触发的时候还能再执行一次！

所以我们综合两者的优势，然后双剑合璧，写一版代码

```js
// 第三版
// func是用户传入需要防抖的函数
// wait是等待时间
function throttle(func, wait = 50) {
    // 定时器，环境this指针，结果
    let timer = null, context, res;
    // 上一次执行该函数的时间
    let lastTime = 0;
    
    // 下一次触发还原
    let later = function () {
        lastTime  = +new Date();
        timer = null;
        func.apply(context, args);
    }
    let throttled = function(...args) {
        let now = +new Date();
        // 下次触发 func 剩余的时间
        let remaining = wait - (now - lastTime);
        context = this;
         // 如果没有剩余的时间了或者你改了系统时间
        if (remaining <= 0 || remaining > wait) {
            if (timer) {
                clearTimeout(timer);
                timer = null;
            }
            lastTime = now;
            func.apply(context, args);
        } else if (!timer) {
            timer = setTimeout(later, remaining);
        }      
    }
    return throttled;
}
// 使用方法：定时器
setInterval(
    throttle(() => {
        console.log(1);
    }, 500),
    1
);
```

效果演示如下：

![throttle3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210808142600.gif)

我们可以看到：鼠标移入，事件立刻执行，晃了 3s，事件再一次执行，当数字变成 3 的时候，也就是 6s 后，我们立刻移出鼠标，停止触发事件，9s 的时候，依然会再执行一次事件。

### 优化

但是我有时也希望无头有尾，或者有头无尾，这个咋办？

那我们设置个 options 作为第三个参数，然后根据传的值判断到底哪种效果，我们约定:

leading：false 表示禁用第一次执行 trailing: false 表示禁用停止触发的回调

我们来改一下代码：

```js
// 第四版
// func是用户传入需要防抖的函数
// wait是等待时间
function throttle(func, wait = 50, options = {}) {
    // 定时器，环境this指针，结果
    let timer = null, context, res, args;
    // 上一次执行该函数的时间
    let lastTime = 0;
    
    // 下一次触发还原
    let later = function () {
        lastTime  = options.leading === false ? 0 : new Date().getTime();
        timer = null;
        func.apply(context, args);
        if (!timer) context = null;
    }
    let throttled = function() {
        let now = new Date().getTime();
        if (!lastTime && options.leading === false) lastTime = now;
        // 下次触发 func 剩余的时间
        let remaining = wait - (now - lastTime);
        context = this;
        args = [...arguments];
         // 如果没有剩余的时间了或者你改了系统时间
        if (remaining <= 0 || remaining > wait) {
            if (timer) {
                clearTimeout(timer);
                timer = null;
            }
            lastTime = now;
            func.apply(context, args);
        	if (!timer) context = null;
        } else if (!timer && options.trailing !== false) {
            timer = setTimeout(later, remaining);
        }      
    }
    return throttled;
}
// 使用方法：定时器
setInterval(
    throttle(() => {
        console.log(1);
    }, 500),
    1
);
```

### 取消

在 debounce 的实现中，我们加了一个 cancel 方法，throttle 我们也加个 cancel 方法：

```js
// 第五版
// func是用户传入需要防抖的函数
// wait是等待时间
function throttle(func, wait = 50, options = {}) {
    // 定时器，环境this指针，结果
    let timer = null, context, args, res;
    // 上一次执行该函数的时间
    let lastTime = 0;
    
    // 下一次触发还原
    let later = function () {
        lastTime  = options.leading === false ? 0 : new Date().getTime();
        timer = null;
        func.apply(context, args);
        if (!timer) context = null;
    }
    let throttled = function() {
        let now = new Date().getTime();
        if (!lastTime && options.leading === false) lastTime = now;
        // 下次触发 func 剩余的时间
        let remaining = wait - (now - lastTime);
        context = this;
        args = [...arguments];
         // 如果没有剩余的时间了或者你改了系统时间
        if (remaining <= 0 || remaining > wait) {
            if (timer) {
                clearTimeout(timer);
                timer = null;
            }
            lastTime = now;
            func.apply(context, args);
        	if (!timer) context = null;
        } else if (!timer && options.trailing !== false) {
            timer = setTimeout(later, remaining);
        }      
    }
    throttled.cancel = function() {
        clearTimeout(timer);
    }
    return throttled;
    lastTime = 0;
    timer = null;
}
// 使用方法：定时器
setInterval(
    throttle(() => {
        console.log(1);
    }, 500),
    1
);
```

功能更丰富的节流函数请参考[JavaScript专题之跟着 underscore 学节流](https://juejin.cn/post/6844903481761857543)



## 28.手写const

### 在ES5环境下实现let

> 这个问题实质上是在回答`let`和`var`有什么区别，对于这个问题，我们可以直接查看`babel`转换前后的结果，看一下在循环中通过`let`定义的变量是如何解决变量提升的问题

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210422113441.jpeg)

babel在let定义的变量前加了道下划线，避免在块级作用域外访问到该变量，除了对变量名的转换，我们也可以通过自执行函数（闭包）来模拟块级作用域

```js
(function(){
    for(var i = 0; i < 5; i ++){
    	console.log(i)  // 0 1 2 3 4
    }
})();

console.log(i);      // Uncaught ReferenceError: i is not defined
```

不过这个问题并没有结束，我们回到`var`和`let/const`的区别上：

- `var`声明的变量会挂到window上，而`let`和`const`不会
- `var`声明的变量存在变量提升，而`let`和`const`不会
- `let`和`const`声明形成块作用域，只能在块作用域里访问，不能跨块访问，也不能跨函数访问
- 同一作用域下`let`和`const`不能声明同名变量，而`var`可以
- 暂时性死区，`let`和`const`声明的变量不能在声明前被使用

babel的转化，其实只实现了第2、3、5点

### const的特点

实现const的关键在于`Object.defineProperty()`这个API，这个API用于在一个对象上增加或修改属性。通过配置属性描述符，可以精确地控制属性行为。`Object.defineProperty()` 接收三个参数：

> Object.defineProperty(obj, prop, desc)

|    参数    |            说明            |
| :--------: | :------------------------: |
|    obj     |   要在其上定义属性的对象   |
|    prop    |  要定义或修改的属性的名称  |
| descriptor | 将被定义或修改的属性描述符 |

除了以上参数，`Object.defineProperty()` 具有以下属性，下标进行了详细的属性参数配置的说明：

|  属性描述符  | 说明                                                         |  默认值   |
| :----------: | :----------------------------------------------------------- | :-------: |
|    value     | 该属性对应的值。可以是任何有效的 JavaScript 值（数值，对象，函数等）。默认为 undefined | undefined |
|     get      | 一个给属性提供 getter 的方法，如果没有 getter 则为 undefined | undefined |
|     set      | 一个给属性提供 setter 的方法，如果没有 setter 则为 undefined。当属性值修改时，触发执行该方法 | undefined |
|   writable   | 当且仅当该属性的writable为true时，value才能被赋值运算符改变。默认为 false |   false   |
|  enumerable  | enumerable定义了对象的属性是否可以在 for...in 循环和 Object.keys() 中被枚举 |   false   |
| configurable | configurable特性表示对象的属性是否可以被删除，以及除value和writable特性外的其他特性是否可以被修改 |   false   |

### 在ES5环境下实现const

由于ES5环境没有`block`的概念，所以是无法百分百实现`const`，只能是挂载到某个对象下，要么是全局的`window`，要么就是自定义一个`object`来当容器对于const不可修改的特性，我们通过设置writable属性来实现

```js
var _const = function __const (data, value) {
	// 把要定义的data挂载到window下，并赋值value
	window.data = value;
	// 利用Object.defineProperty的能力劫持当前对象，并修改其属性描述符
	Object.defineProperty(window, data, {
		// 不可枚举
		enumerable: false,
		// 不可删除
		configurable: false,
		get: function() {
			// 返回值
			return value;
		},
		set: function(data) {
			if (data !== value) {
				// 当要对当前属性进行重新赋值时，则抛出错误！
				throw new TypeError('Assignment to constant variable.');
			} else {
				return value;
			}
		}
	})
}
// 测试
// 定义a
_const('a', 10);
console.log(a); // 10
delete a; // false
console.log(a); // 10
// 因为const定义的属性在global下也是不存在的，所以用到了enumerable: false来模拟这一功能
for (let item in window) { 
    if (item === 'a') {
    	// 因为不可枚举，所以不执行
    	console.log(window[item]);
    }
}
a = 20; // 报错
// 定义obj
_const('obj', {a: 1});
console.log(obj);
// 可以正常给obj的属性赋值
obj.b = 2;
console.log(obj);
obj = {}; // 无法赋值新对象 报错
```

参考资料：[如何在 ES5 环境下实现一个const ？](https://juejin.im/post/6844903848008482824)



## 29.实现一个双向绑定

### defineProperty 版本

利用Object.defineProperty劫持对象的访问器,在属性值发生变化时我们可以获取变化,然后根据变化进行后续响应,在vue3.0中通过Proxy代理对象进行类似的操作。

```js
// 数据
const data = {
	text: 'default'
};
const input = document.getElementById('input');
const span = document.getElementById('span');
// 数据劫持 对象名称和属性名称
Object.defineProperty(data, 'text', {
	enumerable: true,
	configurable: true,
	// 数据变化 --> 修改视图
	set(value) {
		input.value = value,
		span.innerHTML = value;
		return value;
	}
})
// 视图更改 --> 数据变化
input.addEventListener('keyup', function(e) {
	data.text = e.target.value;
})
```

### proxy 版本

Object.defineProperty() 的问题主要有三个： 

-  不能监听数组的变化 
-  必须遍历对象的每个属性 
-  必须深层遍历嵌套的对象

 Proxy 在 ES2015 规范中被正式加入，它有以下几个优势点 

-  针对对象：针对整个对象，而不是对象的某个属性，所以也就不需要对 keys 进行遍历。这解决了上述 Object.defineProperty() 第二个问题 
-  支持数组：Proxy 不需要对数组的方法进行重载，省去了众多 hack，减少代码量等于减少了维护成本，而且标准的就是最好的。 
-  Proxy 的第二个参数可以有 13 种拦截方法，这比起 Object.defineProperty() 要更加丰富 
-  Proxy 作为新标准受到浏览器厂商的重点关注和性能优化，相比之下 Object.defineProperty() 是一个已有的老方法，可以享受新版本红利。

```js
// 数据
const data = {
	text: 'default'
};
const input  = document.getElementById('input');
const span  = document.getElementById('span');
// 数据劫持 对象名称和文本名称
const handler = {
	set(target, key, vlaue) {
		// target = 目标对象
        // prop = 设置的属性
        // value = 修改后的值
		target[key] = value;
		// 数据变化 --> 修改视图
		input.value = value;
		span.innerHTML = value;
		return value;
	}
} 
// 实现代理
const proxy = new Proxy(data, handler);

// 视图更改 --> 数据变化
input.addEventLisener('keyup', function(e) {
	proxy.text = e.target.value;
});
```



## 30.图片懒加载

### 监听scroll事件法

图片，用一个其他属性存储真正的图片地址：

```html
  <img src="loading.gif" data-src="https://cdn.pixabay.com/photo/2015/09/09/16/05/forest-931706_1280.jpg" alt="">
  <img src="loading.gif" data-src="https://cdn.pixabay.com/photo/2014/08/01/00/08/pier-407252_1280.jpg" alt="">
  <img src="loading.gif" data-src="https://cdn.pixabay.com/photo/2014/12/15/17/16/pier-569314_1280.jpg" alt="">
  <img src="loading.gif" data-src="https://cdn.pixabay.com/photo/2010/12/13/10/09/abstract-2384_1280.jpg" alt="">
  <img src="loading.gif" data-src="https://cdn.pixabay.com/photo/2015/10/24/11/09/drop-of-water-1004250_1280.jpg" alt="">
```

通过图片`offsetTop`和`window`的`innerHeight`，`scrollTop`判断图片是否位于可视区域。

```js
// 节流函数，保证每200ms触发一次
function throttle(func, wait = 200) {
    let timer = null;
    return function (...args) {
        if (!timer) {
            timer = setTimeout(() => {
                timer = null;
                func.apply(this, args);
            }, wait);
        }
    }
}

// 获取所有img标签
var imgs = document.getElementsByTagName("img");
// 存储图片已经实现加载的位置，避免每次都从第一张图片开始遍历
var n = 0;
// 页面载入完毕加载可是区域内的图片
lazyload();

// 监听页面滚动事件
window.addEventListener('scroll', throttle(lazyload, 200));
// 懒加载函数
function lazyload() {
    // 可见区域高度
    let visualHeight = window.innerHeight;
	// 滚动条距离顶部高度，注意要兼容IE浏览器
    let scrollTop = document.documentElement.scrollTop || document.body.scrollTop
    // 从上一个没有加载完毕的img便利到最后的img
    for (let i = n; i < imgs.length; i++) {
        // 在视窗范围以内
        if (img[i].offsetTop < visualHeight + scrollTop) {
            if (img[i].getAttribute('src') === "loading.gif") {
                // 将src替换为data-src
                img[i].src = img[i].getAttribute("data-src");
            }
            n = i + 1;
        }
    }
}
```

### IntersectionObserver

> IntersectionObserver接口 (从属于Intersection Observer API) 提供了一种异步观察目标元素与其祖先元素或顶级文档视窗(viewport)交叉状态的方法。祖先元素与视窗(viewport)被称为根(root)。

`Intersection Observer`可以不用监听`scroll`事件，做到元素一可见便调用回调，在回调里面我们来判断元素是否可见。

```js
// 获取所有img标签
var imgs = document.getElementsByTagName("img");

// 判断IntersectionObserver可用
if (IntersectionObserver) {
    let lazyloadObserver = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
            
        })
    })
    let lazyImgObserver = new IntersectionObserver((entries, observer) => {
        entries.forEach((entry, index) => {
            // 懒加载图片
            let lazyImg = entry.target;
            // 如果元素可见
            if (entry.intersectionRatio > 0) {
                if (lazyImg.getAttribute("src") === "loading.gif") {
                    lazyImg.src = lazyImg.getAttribute("data-src");
                }
                // 图片加载后即停止监听该元素
                lazyImgObserver.unobserve(lazyImg);
            }
        })
    })
    // observe遍历监听所有img节点
    for (let i = 0; i < imgs.length; i++) {
        lazyImgObserver.observe(imgs[i]);
    }
}
```



## 31.区间随机数生成器

```js
function random(m, n) {
	return Math.floor(Math.random() * (n - m)) + m;
	// return parseInt(Math.random() * (n - m)) + m;
}

for (let i = 0; i < 10; i++) {
	console.log(random(28, 45));
}
```



## 32.打印菱形

```js
function printDiamond(n) {
	for (let i = 0; i < n; i++) {
		for (let j = 0; j <= i; j++) {
			document.write("* ");
		}
		document.write("<br/>");
	}
	for (let i = n - 2; i >= 0; i--) {
		for (let j = 0; j <= i; j++) {
			document.write("* ");
		}
		document.write("<br/>");
	}
}
```



## 33.手写parseInt

一开始以为很简单，写着写着 发现好难，还是先写只带有数字、字母的，0x什么开头的我怕是头想秃了都不会

```js
function getNum(char) {
    if ('0' <= char && char <= '9') return Number(char);
    if ('a' <= char && char <= 'z') return char.charCodeAt() - 'a'.charCodeAt() + 10;
    if ('A' <= char && char <= 'Z') return char.charCodeAt() - 'A'.charCodeAt() + 10;
}
function _parseInt(str, radix) {
    // 字符串类型
    let strType = Object.prototype.toString.call(str);
    // 如果类型不是 string 或 number 类型返回NaN
    if (strType !== '[object String]' && strType !== '[object Number]') return NaN;

    // 如果 radix 为0 null undefined
    if (!radix) {
        // 则转化为 10
        radix = 10;
    }

    if (Object.prototype.toString.call(radix) !== '[object Number]' || radix < 2 || radix > 36 || Math.floor(radix) < radix){
        return NaN;
    }

    // 正则表达式，表示数
    const re = /^[\-|\+]?[0-9a-zA-Z]*(\.[0-9a-zA-Z]+)?/;
    // 字符串处理，把小数点以后的去除
    str = (str + '').trim().match(re)[0];
    if (!str.length) return NaN;
    let sign = "+";
    // 处理特殊情况
    if (str[0] === '+') {
        str = str.slice(1);
    }
    if (str[0] === '-') {
        sign = "-";
        str = str.slice(1);
    }
    if (str[0] === '.') {
        if (str[1]) {
            let num = getNum(str[1]);
            if (num < radix && sign === '+') return 0;
            if (num < radix && sign === '-') return -0;
            return NaN;
        }
        return NaN;
    }
    // 把小数点后面的去除
    str = str.split('.')[0];
    if (!str.length) return NaN;
    let res = getNum(str[0]);
    if (res >= radix) return NaN;
    for (let i = 1; i < str.length; i++) {
        let num = getNum(str[i]);
        if (num >= radix) return sign === '+' ? res : -res;
        res = res * radix + num;
    }
    return sign === '+' ? res : -res;
}

console.log(_parseInt("F", 16));
console.log(_parseInt("17", 8));
console.log(_parseInt("015", 10));
console.log(_parseInt(15.99, 10));
console.log(_parseInt("15,123", 10));
console.log(_parseInt("FXX123", 16));
console.log(_parseInt("1111", 2));
console.log(_parseInt("15 * 3", 10));
console.log(_parseInt("15e2", 10));
console.log(_parseInt("15px", 10));
console.log(_parseInt("12", 13));
console.log('-------------------------------');
console.log(_parseInt("Hello", 8));
console.log(_parseInt("546", 2));
console.log('-------------------------------');
console.log(_parseInt("-F", 16));
console.log(_parseInt("-0F", 16));
console.log(_parseInt(-15.1, 10));
console.log(_parseInt(" -17", 8));
console.log(_parseInt(" -15", 10));
console.log(_parseInt("-1111", 2));
console.log(_parseInt("-15e1", 10));
console.log(_parseInt("-12", 13));
```

大佬的版本，可以参考下，让我当场写肯定不会

```js
function compare(str, radix) {
    let code = str.toUpperCase().charCodeAt(0),
        num;
    if (radix >= 11 && radix <= 36) {
        if (code >= 65 && code <= 90) {
            num = code - 55;
        } else {
            num = code - 48;
        }
    } else {
        num = code - 48;
    }
    return num;
}

function isHex(first, str) {
    return first === '0' && str[1].toUpperCase() === 'X'
}

function _parseInt(str, radix) {
    str = String(str);
    if (typeof str !== 'string') return NaN;
    str = str.trim();
    let first = str[0],
        sign;
    //处理第一个字符为 '-' || '+' 的情况
    if (first === '-' || first === '+') {
        sign = str[0];
        str = str.slice(1);
        first = str[0];
    }
    //当 radix 不存在或者小于 11 时，第一个字符只能为数字
    if (radix === undefined || radix < 11) {
        if (isNaN(first)) return NaN;
    }

    let reg = /^(0+)/;
    //截取 str 前面符合要求的一段，直到遇到非数字和非字母的字符
    let reg2 = /^[0-9a-z]+/i;
    str = str.match(reg2)[0];
    let len = str.length;
    //在没有第二个参数时或者不是数字时，给第二个参数赋值
    //isNaN('0x12') 会执行 Number('0x12') 可以转换成十进制
    if (radix === undefined || isNaN(radix) || radix === 0) {
        if (len === 1) return str;
        //如果 str 是十六进制形式，就转换成十进制
        if (isHex(first, str)) {
            if (sign === '-') {
                return Number(-str);
            } else {
                return Number(str);
            }
        } else {
            //不能直接返回 Number(str) 比如 Number('0ff23') 会返回 NaN，但是应该返回 0
            radix = 10;
        }
    } else {
        //如果有第二个参数，并且是数字，要处理第二个参数
        radix = String(radix);
        //如果有小数点，取小数点前面一段，处理不为整数的情况
        radix = radix.split('.')[0];
        //如果 radix 前面有零将零去除，十六进制除外
        if (radix.length > 1) {
            let twoR = radix[1].toUpperCase();
            if (radix[0] === '0' && twoR !== 'X') radix = radix.replace(reg, '');
        }
        //如果 radix 是十六进制的字符串类型，也会转变成十进制的数字类型
        radix = Number(radix);
        //radix 是否在正确的区间
        if (radix >= 2 && radix <= 36) {
            //如果 radix 为 16，且 str 是十六进制形式的话，直接将十六进制转换成十进制
            if (radix === 16 && isHex(first, str)) return Number(str);
        } else {
            //只要 radix 是一个有效的数字，但不在正确的区间里，就返回 NaN
            return NaN;
        }
    }
    //去除 str 前面的零
    str = str.replace(reg, '');
    if (str.length === 0) return 0;
    let strArr = str.split(''),
        numArr = [],
        result = 0,
        num;
    for (let i = 0; i < strArr.length; i++) {
        num = compare(strArr[i], radix);
        if (num < radix) {
            numArr.push(num);
        } else {
            break;
        }
    }
    let lenN = numArr.length;
    if (lenN > 0) {
        numArr.forEach(function (item, index) {
            result += item * Math.pow(radix, lenN - index - 1);
        });
    } else {
        //str 开头有零的话要返回零
        return first === '0' ? 0 : NaN;
    }
    if (sign === '-') result = -result;
    return result;
}
```



## 34.手写JSON.stringify

先熟悉JSON.stringify的用法

```js
JSON.stringify(value[, replacer [, space]])：
```

- `Boolean | Number| String`类型会自动转换成对应的原始值。
- `undefined`、任意函数以及`symbol`，会被忽略（出现在非数组对象的属性值中时），或者被转换成 `null`（出现在数组中时）。
- 不可枚举的属性会被忽略如果一个对象的属性值通过某种间接的方式指回该对象本身，即循环引用，属性也会被忽略
- 如果一个对象的属性值通过某种间接的方式指回该对象本身，即循环引用，属性也会被忽略

手写代码如下

```js
function jsonStringify(obj) {
    let type = typeof obj;
    if (type !== 'object') {
        // 不是字符串 undefined 和 function 类型
        if (/string|undefined|function/.test(type)) {
            obj = '"' + obj + '"';
        }
        return String(obj);
    }
    // JSON为空数组
    let json = [];
    // 是否为数组
    let arr = Array.isArray(obj);
    for (let key in obj) {
        // 递归调用
        let value = jsonStringify(obj[key]);
        json.push((arr ? "" : '"' + key + '":') + String(value));
    }
    return (arr ? "[" : "{") + String(json) + (arr ? "]" : "}");
}

console.log(jsonStringify({x : 5})); // {"x":5}
console.log(jsonStringify([1, "false", false])); // [1,"false",false]
console.log(jsonStringify({b: undefined})); // {"b":"undefined"}
```

手写了一下，是不是对于JSON.stringify的不足之处又有了全新的理解了呢，再次强调如下：

- 非数组对象的属性不能保证以特定的顺序出现在序列化后的字符串中。
- 布尔值、数字、字符串的包装对象在序列化过程中会自动转换成对应的原始值。
- `undefined`、任意的函数以及 symbol 值，在序列化过程中会被忽略（出现在非数组对象的属性值中时）或者被转换成 `null`（出现在数组中时）。函数、undefined 被单独转换时，会返回 undefined，如`JSON.stringify(function(){})` or `JSON.stringify(undefined)`.
- 对包含循环引用的对象（对象之间相互引用，形成无限循环）执行此方法，会抛出错误。
- 所有以 symbol 为属性键的属性都会被完全忽略掉，即便 `replacer` 参数中强制指定包含了它们。
- Date 日期调用了 toJSON() 将其转换为了 string 字符串（同Date.toISOString()），因此会被当做字符串处理。
- NaN 和 Infinity 格式的数值及 null 都会被当做 null。
- 其他类型的对象，包括 Map/Set/WeakMap/WeakSet，仅会序列化可枚举的属性。



## 35.手写JSON.parse

先熟悉JSON.parse的用法

```js
JSON.parse(text[, reviver])
```

> 用来解析JSON字符串，构造由字符串描述的JavaScript值或对象。提供可选的reviver函数用以在返回之前对所得到的对象执行变换(操作)

### 直接调用 eval

```js
function jsonParse(opt) {
    return eval('(' + opt + ')');
}

console.log(jsonParse(JSON.stringify({x : 5}))); // [object Object]: { x: 5}
console.log(jsonParse(JSON.stringify([1, "false", false]))); // [object Array]: [1, "false", false]
console.log(jsonParse(JSON.stringify({b: undefined}))); // [object Object]: {}
```

> 避免在不必要的情况下使用 `eval`，`eval()` 是一个危险的函数，他执行的代码拥有着执行者的权利。如果你用`eval()`运行的字符串代码被恶意方（不怀好意的人）操控修改，您最终可能会在您的网页/扩展程序的权限下，在用户计算机上运行恶意代码。它会执行JS代码，有XSS漏洞。

如果你只想记这个方法，就得对参数json做校验。

```js
var rx_one = /^[\],:{}\s]*$/;
var rx_two = /\\(?:["\\\/bfnrt]|u[0-9a-fA-F]{4})/g;
var rx_three = /"[^"\\\n\r]*"|true|false|null|-?\d+(?:\.\d*)?(?:[eE][+\-]?\d+)?/g;
var rx_four = /(?:^|:|,)(?:\s*\[)+/g;
if (
    rx_one.test(
        json
            .replace(rx_two, "@")
            .replace(rx_three, "]")
            .replace(rx_four, "")
    )
) {
    var obj = eval("(" +json + ")");
}
```

### 调用Function

> 核心：Function与eval有相同的字符串参数特性

```js
var func = new Function(arg1, arg2, ..., functionBody);
```

在转换JSON的实际应用中，只需要这么做

```js
var jsonStr = '{ "age": 20, "name": "jack" }'
var json = (new Function('return ' + jsonStr))();
```

> `eval` 与 `Function`都有着动态编译js代码的作用，但是在实际的编程中并不推荐使用

测试结果如下

```js
let jsonStr = JSON.stringify({x : 5});
console.log((new Function('return ' + jsonStr))()); // [object Object]: {x: 5}
jsonStr = JSON.stringify([1, "false", false]);
console.log((new Function('return ' + jsonStr))()); // [object Array]: [1, "false", false]
jsonStr = JSON.stringify({b: undefined});
console.log((new Function('return ' + jsonStr))()); // [object Object]: {}
```

`eval` 与 `Function` 都有着动态编译js代码的作用，但是在实际的编程中并不推荐使用。

**第三，第四种方法，涉及到繁琐的递归和状态机相关原理，具体可以看：[JSON.parse 三种实现方式](https://github.com/youngwind/blog/issues/115)**



## 36.解析 URL Params 为对象

尽可能的全面正确的解析一个任意 url 的所有参数为 Object，注意边界条件的处理
要求如下：
• 1. 重复出现的 key 要组装成数组
• 2. 能被转成数字的就转成数字类型
• 3. 中⽂需解码
• 4. 未指定值的 key 约定为 true

```js
let url = 'http://www.domain.com/?user=anonymous&id=123&id=456&city=%E5%8C%97%E4%BA%AC&enabled';
parseParam(url);
/* 结果
{ user: 'anonymous',
  id: [ 123, 456 ], // 重复出现的 key 要组装成数组，能被转成数字的就转成数字类型
  city: '北京', // 中文需解码
  enabled: true, // 未指定值得 key 约定为 true
}
*/
```
具体实现代码思路如下，对url进行分割
```js
function parseParam(url) {
    // 将 ? 后面的字符串取出来
    const paramsStr = url.split('?')[1];
    // 将字符串以 & 分割后存到数组中
    const paramsArr = paramsStr.split('&');
    // 将 params 存到对象中
    let paramsObj = {};
    for (let i = 0; i < paramsArr.length; i++) {
        // 分割 key 和 value
        let [key, value] = paramsArr[i].split('=');
        // 处理没有值的参数，约定值为true
        if (!value) value = true;
        // 中文解码
        value = decodeURIComponent(value);
        // 转为数字类型，必须放中文解码后面
        if (/^\d+(\.\d+)?$/.test(value)) value = Number(value);
        // 处理重复出现的key，组装成数组
        if (paramsObj[key]) {
            // 主要是在这里做了一下处理,判断值是不是一个数组
            paramsObj[key] = Array.isArray(paramsObj[key]) ? [...paramsObj[key], value] : [paramsObj[key], value];
        } else {
            paramsObj[key] = value;
        }
    }
    return paramsObj;
}
let url = 'http://www.domain.com/?user=anonymous&id=123&id=456&city=%E5%8C%97%E4%BA%AC&enabled';
console.log(parseParam(url));
```

也可以使用正则表达式分割，这是网上找的大佬版本

```js
function parseParam(url) {
 	// 将 ? 后面的字符串取出来
    const paramsStr = /.+\?(.+)$/.exec(url)[1];
    // 将字符串以 & 分割后存到数组中
    const paramsArr = paramsStr.split('&');
    let paramsObj = {};
    // 将 params 存到对象中
    paramsArr.forEach(param => {
     	// 处理有 value 的参数
        if (/=/.test(param)) {
        	// 分割 key 和 value
        	let [key, val] = param.split('=');
        	// 递归调用解码
        	val = decodeURIComponent(val);
        	// 判断是否转为数字
        	val = /^\d+$/.test(val) ? parseFloat(val) : val; 

			// 如果对象有 key，则添加一个值
            if (paramsObj.hasOwnProperty(key)) {
            	paramsObj[key] = [].concat(paramsObj[key], val);
            } else {
            	// 如果对象没有这个 key，创建 key 并设置值
            	paramsObj[key] = val;
            }
        } else {
        	// 处理没有 value 的参数
        	paramsObj[param] = true;
        }
    })
    return paramsObj;
}
let url = 'http://www.domain.com/?user=anonymous&id=123&id=456&city=%E5%8C%97%E4%BA%AC&enabled';
console.log(parseParam(url));
```



## 37.模板引擎实现

将对象data中的数据渲染至template模板中

```js
let template = '我是{{name}}，年龄{{age}}，性别{{sex}}';
let data = {
    name: '姓名',
    age: 18
}
render(template, data); // 我是姓名，年龄18，性别undefined
```

自己跟着大佬手写的代码

```js
function render(template, data) {
	// 模板字符串正则
	const reg = /\{\{(\w+)\}\}/;
	// 判断模板里是否有模板字符串
	if (reg.test(template)) {
		// 查找当前模板里第一个模板字符串的字段，对应正则表达式()中的内容
		const name = reg.exec(template)[1];
		// 将第一个模板字符串渲染
		template = template.replace(reg, data[name]);
		// 递归的渲染并返回渲染后的结构
        return render(template, data);
	}
	// 如果模板没有模板字符串直接返回
	return template;
}
let template = '我是{{name}}，年龄{{age}}，性别{{sex}}';
let data = {
    name: '姓名',
    age: 18
}
console.log(render(template, data)); // 我是姓名，年龄18，性别undefined
```

递归改为迭代

```js
function render(template, data) {
	// 模板字符串正则
	const reg = /\{\{(\w+)\}\}/;
	// 判断模板里是否有模板字符串
	while (reg.test(template)) {
		// 查找当前模板里第一个模板字符串的字段，对应正则表达式()中的内容
		const name = reg.exec(template)[1];
		// 将第一个模板字符串渲染
		template = template.replace(reg, data[name]);
	}
	// 如果模板没有模板字符串直接返回
	return template;
}
let template = '我是{{name}}，年龄{{age}}，性别{{sex}}';
let data = {
    name: '姓名',
    age: 18
}
console.log(render(template, data)); // 我是姓名，年龄18，性别undefined
```

这只是自行车级别的模板引擎，想要火箭级别的请参考underscore 提供的模板引擎功能，冴羽大大提供了一步一步实现改模板引擎的手把手教程[underscore 系列之实现一个模板引擎(上)](https://github.com/mqyqingfeng/Blog/issues/63)和[underscore 系列之实现一个模板引擎(下)](https://github.com/mqyqingfeng/Blog/issues/70)



## 38.驼峰命名-中划线转换

### 中划线转驼峰

```js
// 把-后面的字母替换为大写字母
function fn(str) {
	return str.replace(/-\w/g, function (v) {
		// 首字母大写
		return v.slice(1).toUpperCase();
	})
}
let s1 = "get-element-by-id"; // 转化为 getElementById
console.log(fn(s1));
```

简化代码

```js
// 把-后面的字母替换为大写字母
function fn(str) {
	return str.replace(/-\w/g, v => v[1].toUpperCase());
}
let s1 = "get-element-by-id"; // 转化为 getElementById
console.log(fn(s1));
```

或者

```js
// 把-后面的字母替换为大写字母
function fn(str) {
	return str.replace(/-(\w)/g, (v1, v2) => v2.toUpperCase());
}
let s1 = "get-element-by-id"; // 转化为 getElementById
console.log(fn(s1));
```

### 驼峰转中划线

```js
// 把大写字母替换为-和小写字母
function fn(str) {
	return str.replace(/[A-Z]/g, v => '-' + v.toLowerCase());
}
let s2 = "getElementById"; // 转化为 get-element-by-id
console.log(fn(s2));
```

### 拓展到4种模式

编程语言中常见的命名风格有如下四种：
1.全部首字母大写
2.第一个单词首字母小写，其余单词首字母大写
3.单词全部小写，由下划线连接
4.单词全部小写，由减号连接

请设计并实现一个caseTransform函数，使得一个字符串str可以被方便地转成四种形式，并且将四种形式通过空格拼接成一个字符串返回
为方便起见，这里假设输入字符串全部符合以上四种形式的英文字母组合

**输入描述:**

```
PascalCaseTest
```

**输出描述:**

```
PascalCaseTest  pascalCaseTest  pascal_case_test pascal-case-test
```

判断是哪种模式，识别之后进行拼接操作

```js
function caseTransform(s) {
	let list = new Array(4);
	if (s.indexOf('_') != -1) {
		// 第3种情况
		// 自身
		list[2] = s;
		// 切割
		let arr = s.split('_');
		// 第4种模式
		list[3] = arr.join('-');
		// 第1种模式
		list[0] = arr.map((item) => item[0].toUpperCase() + item.slice(1)).join('');
		// 第2种模式
		list[1] = list[0][0].toLowerCase() + list[0].slice(1);
	} else if (s.indexOf('-') != -1) {
		// 第4种情况
		// 自身
		list[3] = s;
		// 切割
		let arr = s.split('-');
		// 第3种模式
		list[2] = arr.join('_');
		// 第1种模式
		list[0] = arr.map((item) => item[0].toUpperCase() + item.slice(1)).join('');
		// 第2种模式
		list[1] = list[0][0].toLowerCase() + list[0].slice(1);
	} else if (s[0] >= 'A' && s[0] <= 'Z') {
		// 第1种情况
		list[0] = s;
		// 第2种模式
		list[1] = s[0].toLowerCase() + s.slice(1);
		// 第3种模式
		list[2] = list[1].replace(/[A-Z]/g, function(x) {
            return '_' + x[0].toLowerCase();
        });
		// 第4种模式
		list[3] = list[2].replace(/_/g, '-');
	} else {
		// 第2种模式
		list[1] = s;
		// 第1种模式
		list[0] = s[0].toUpperCase() + s.slice(1);
		// 第3种模式
		list[2] = s.replace(/[A-Z]/g, function(x) {
            return '_' + x[0].toLowerCase();
        });
		// 第4种模式
		list[3] = list[2].replace(/_/g, '-');
	}
	return(list);
}
let str = 'PascalCaseTest';
console.log(caseTransform(str).join(' '));
```



## 39.查找字符串中出现最多的字符和个数

### 排序+正则统计单个字符个数

1.使其按照⼀定的次序对数据进行排列

2.利用正则匹配数据

​	反向引用

​		()相关匹配会被存储到一个临时缓冲区,所捕获的每个子匹配都会按照正则模式中从左到右出现的顺序存储.缓冲区编号从1开始,最多99个捕获的子表达式,

​		每个缓冲区都可用\n表示,其中 n 为一个标识特定缓冲区的一位或两位十进制数。如:

​			指定第一个子匹配项,指定正则表达式的第二部分是对前面捕获的子匹配项的引用，即第二个匹配项正好由括号表达式匹配.

3.利用replace的参数特性,得到最多字符及个数
  replace() 方法用于在字符串中用一些字符替换另一些字符，或替换一个与正则表达式匹配的子串。

语法:

​	stringObject.replace(regexp/substr,replacement)

参数:

​	regexp/substr: 规定子字符串或要替换的模式的 RegExp 对象

​	replacement: 规定了替换文本或生成替换文本的函数。

​		可以是字符串，也可以是函数

​		字符串: 每个匹配都由字符串替换 

​		函数:

​			参数特性: 

​				第一个参数:匹配模式的字符串

​				其他参数:模式中的子表达式匹配的字符串,可以有0或多个

​				下一个参数:整数,声明匹配在stringObject 中出现的位置

​				最后一个参数: stringObject本身

```js
let str = "abcabcabcbbccccc";
let num = 0;
let char = '';
// 使其按照一定的次序排列
str = str.split('').sort().join('');
// "aaabbbbbcccccccc"

// 定义正则表达式
let re = /(\w)\1+/g;
str.replace(re, ($0, $1) => {
	if (num < $0.length) {
		// num始终储存次数最大的那个
		num = $0.length;
		char = $1;
	} else if (num === $0.length){
		if (Array.isArray(char)) {
			char.push($1);
		} else {
			char = [char, $1];
		}
	}
})
console.log(`字符最多的是${char}，出现了${num}次`);
// 字符最多的是c，出现了8次
```

### 哈希表统计单个字符个数

```js
let str = "abcabcabcbbccccc";
let num = 0;
let char = '';
// 哈希表
let obj = {};
// 使其按照一定的次序排列
for (let i = 0; i < str.length; i++) {
    let char = str[i];
    if (obj[char]) {
        // 次数加1
        obj[char]++;
    } else {
        //若第一次出现，次数记为1
        obj[char] = 1;
    }
}
// 输出的是完整的对象，记录着每一个字符及其出现的次数
console.log(obj);
/*
 * a: 3
 * b: 5
 * c: 8
 */

for (let key in obj) {
    if (num < obj[key]) {
		num = obj[key];
		char = key;
	} else if (num === obj[key]){
		if (Array.isArray(char)) {
			char.push(key);
		} else {
			char = [char, key];
		}
	}
}
/*
 * a: 3
 * b: 5
 * c: 8
 */
console.log(`字符最多的是${char}，出现了${num}次`);
```



## 40.字符串查找

> 请使用最基本的遍历来实现判断字符串 a 是否被包含在字符串 b 中，并返回第一次出现的位置（找不到返回 -1）。

### 暴力解

**思路及算法**

我们可以让字符串 needle 与字符串 haystack 的所有长度为 m 的子串均匹配一次。

为了减少不必要的匹配，我们每次匹配失败即立刻停止当前子串的匹配，对下一个子串继续匹配。如果当前子串匹配成功，我们返回当前子串的开始位置即可。如果所有子串都匹配失败，则返回 -1。

**时间复杂度**：$O(n×m)$，其中 n 是字符串 haystack 的长度，m 是字符串 needle 的长度。最坏情况下我们需要将字符串 needle 与字符串haystack 的所有长度为 m 的子串均匹配一次。

**空间复杂度**：$O(1)$。我们只需要常数的空间保存若干变量。

```js
var strStr = function(haystack, needle) {
    let m =  haystack.length;
    let n = needle.length;
    for (let i = 0; i <= m - n; i++) {
        let j;
        for (j = 0; j < n; j++) {
            if (needle[j] !== haystack[i + j]) break;
        }
        // needle子串全都匹配了
        if (j === n) return i;
    }
    // haystack中不存在needle
    return -1;
};
```

### KMP算法

KMP 算法是一个快速查找匹配串的算法，它的作用其实就是本题问题：如何快速在「原字符串」中找到「匹配字符串」。

上述的朴素解法，不考虑剪枝的话复杂度是 $O(m∗n)$ 的，而 KMP 算法的复杂度为 $O(m+n)$。

KMP 之所以能够在$O(m+n)$ 复杂度内完成查找，是因为其能在「非完全匹配」的过程中提取到有效信息进行复用，以减少「重复匹配」的消耗。

1. 匹配过程
在模拟 KMP 匹配过程之前，我们先建立两个概念：

前缀：对于字符串 abcxxxxefg，我们称 abc 属于 abcxxxxefg 的某个前缀。
后缀：对于字符串 abcxxxxefg，我们称 efg 属于 abcxxxxefg 的某个后缀。
然后我们假设原串为 abeababeabf，匹配串为 abeabf：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210430220903.png)
我们可以先看看如果不使用 KMP，会如何进行匹配（不使用 substring 函数的情况下）。

首先在「原串」和「匹配串」分别各自有一个指针指向当前匹配的位置。

首次匹配的「发起点」是第一个字符 a。显然，后面的 abeab 都是匹配的，两个指针会同时往右移动（黑标）。

在都能匹配上 abeab 的部分，「朴素匹配」和「KMP」并无不同。

直到出现第一个不同的位置（红标）：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210430221006.png)

接下来，正是「朴素匹配」和「KMP」出现不同的地方：

先看下「朴素匹配」逻辑：
1.  将原串的指针移动至本次「发起点」的下一个位置（b 字符处）；匹配串的指针移动至起始位置。

2.  尝试匹配，发现对不上，原串的指针会一直往后移动，直到能够与匹配串对上位置。

如图：

![image.png](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210430221056.png)

也就是说，对于「朴素匹配」而言，一旦匹配失败，将会将原串指针调整至下一个「发起点」，匹配串的指针调整至起始位置，然后重新尝试匹配。

这也就不难理解为什么「朴素匹配」的复杂度是O(m∗n) 了。

然后我们再看看「KMP 匹配」过程：
首先匹配串会检查之前已经匹配成功的部分中里是否存在相同的「前缀」和「后缀」。如果存在，则跳转到「前缀」的下一个位置继续往下匹配：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210430221448.jpeg)

跳转到下一匹配位置后，尝试匹配，发现两个指针的字符对不上，并且此时匹配串指针前面不存在相同的「前缀」和「后缀」，这时候只能回到匹配串的起始位置重新开始：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210430221558.png)

到这里，你应该清楚 KMP 为什么相比于朴素解法更快：

因为 KMP 利用已匹配部分中相同的「前缀」和「后缀」来加速下一次的匹配。

因为 KMP 的原串指针不会进行回溯（没有朴素匹配中回到下一个「发起点」的过程）。

第一点很直观，也很好理解。

我们可以把重点放在第二点上，原串不回溯至「发起点」意味着什么？

其实是意味着：随着匹配过程的进行，原串指针的不断右移，我们本质上是在不断地在否决一些「不可能」的方案。

当我们的原串指针从 i 位置后移到 j 位置，不仅仅代表着「原串」下标范围为 [i,j)[i,j) 的字符与「匹配串」匹配或者不匹配，更是在否决那些以「原串」下标范围为 [i,j)[i,j) 为「匹配发起点」的子集。

2. 分析实现

到这里，就结束了吗？要开始动手实现上述匹配过程了吗？

我们可以先分析一下复杂度。如果严格按照上述解法的话，最坏情况下我们需要扫描整个原串，复杂度为 $O(n)$。同时在每一次匹配失败时，去检查已匹配部分的相同「前缀」和「后缀」，跳转到相应的位置，如果不匹配则再检查前面部分是否有相同「前缀」和「后缀」，再跳转到相应的位置 ... 这部分的复杂度是 $O(m^2)$ ，因此整体的复杂度是 $O(n * m^2)$，而我们的朴素解法是 $O(m * n)$ 的。

说明还有一些性质我们没有利用到。

显然，扫描完整原串操作这一操作是不可避免的，我们可以优化的只能是「检查已匹配部分的相同前缀和后缀」这一过程。

再进一步，我们检查「前缀」和「后缀」的目的其实是「为了确定匹配串中的下一段开始匹配的位置」。

同时我们发现，对于匹配串的任意一个位置而言，由该位置发起的下一个匹配点位置其实与原串无关。

举个例子，对于匹配串 abcabd 的字符 d 而言，由它发起的下一个匹配点跳转必然是字符 c 的位置。因为字符 d 位置的相同「前缀」和「后缀」字符 ab 的下一位置就是字符 c。

可见从匹配串某个位置跳转下一个匹配位置这一过程是与原串无关的，我们将这一过程称为找 next 点。

显然我们可以预处理出 next 数组，数组中每个位置的值就是该下标应该跳转的目标位置（next 点）。

当我们进行了这一步优化之后，复杂度是多少呢？

预处理 next 数组的复杂度未知，匹配过程最多扫描完整个原串，复杂度为$O(n)$。

因此如果我们希望整个 KMP 过程是 $O(m+n)$ 的话，那么我们需要在 $O(m)$ 的复杂度内预处理出 next数组。

所以我们的重点在于如何在 $O(m)$ 复杂度内处理处 next 数组。

3. next 数组的构建
接下来，我们看看 next 数组是如何在 $O(m)$的复杂度内被预处理出来的。

假设有匹配串 aaabbab，我们来看看对应的 next 是如何被构建出来的。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210430221727.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210430221743.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210430221800.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210430221911.png)

这就是整个 `next` 数组的构建过程，时空复杂度均为 $O(m)$。

至此整个 KMP 匹配过程复杂度是 $O(m+n)$ 的。


```js
var strStr = function(haystack, needle) {
    // next数组当前位指针，原串和匹配串的长度
    let k = -1, n = haystack.length, p = needle.length;
    if (p == 0) return 0;
    // -1表示不存在相同的最大前缀和后缀
    let next = Array(p).fill(-1);
    // 计算next数组
    calNext(needle, next);
    for (let i = 0; i < n; i++) {
        while (k > -1 && needle[k + 1] !== haystack[i]) {
            // 有部分匹配，往前回溯
            k = next[k];
        }
        if (needle[k + 1] === haystack[i]) {
            k++;
        }
        if (k === p - 1) {
            // 说明k移动到needle的最末端，返回相应的位置
            return i - p + 1;
        }
    }
    return -1;
};

// 辅函数- 计算next数组
function calNext(needle, next) {
    // 构造过程 j = 1，p = -1 开始
    for (let j = 1, p = -1; j < needle.length; j++) {
        while (p > -1 && needle[p + 1] !== needle[j]) {
            // 如果下一位不同，往前回溯
            p = next[p];
        }
        if (needle[p + 1] === needle[j]) {
            // 如果下一位相同，更新相同的最大前缀和最大后缀长
            p++;
        }
        // 位置j处更新最长前缀
        next[j] = p;
    }
}
```

马拉车水平不够，看都没看懂，就不写了。



## 41.实现千位分隔符

### 反转整数部分

实现思路是将数字转换为字符数组，再循环整个数组， 每三位添加一个分隔逗号，最后再合并成字符串。因为分隔符在顺序上是从后往前添加的：比如 1234567添加后是1,234,567 而不是 123,456,7 ，所以方便起见可以先把数组倒序，添加完之后再倒序回来，就是正常的顺序了。要注意的是如果数字带小数的话，要把小数部分分开处理。

```js
function numFormat(num) {
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

let a = 1234567894532;
let b = 673439.4542;
console.log(numFormat(a)); // "1,234,567,894,532"
console.log(numFormat(b)); // "673,439.4542"
```

### 自带函数toLocaleSting

使用JS自带的函数 [toLocaleString](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)

> 语法：  `numObj.toLocaleString([locales [, options]])`

`toLocaleString()` 方法返回这个数字在特定语言环境下的表示字符串。

```js
let a = 1234567894532;
let b = 673439.4542;

console.log(a.toLocaleString()); // "1,234,567,894,532"
console.log(b.toLocaleString()); // "673,439.454"  （小数部分四舍五入了）
```

要注意的是这个函数在没有指定区域的基本使用时，返回使用默认的语言环境和默认选项格式化的字符串，所以不同地区数字格式可能会有一定的差异。最好确保使用 locales 参数指定了使用的语言。
 注：我测试的环境下小数部分会根据四舍五入只留下三位。

### 正则表达式

使用[正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)和[replace](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)函数，相对前两种我更喜欢这种方法，虽然正则有点难以理解。

> replace 语法：`str.replace(regexp|substr, newSubStr|function)`

其中第一个 [RegExp](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/RegExp) 对象或者其字面量所匹配的内容会被第二个参数的返回值替换。

```js
function numFormat(num) {
    let res = num.toString().replace(/\d+/, function(n) {
        // 先提取整数部分
        // console.log(n);
        return n.replace(/(\d)(?=(\d{3})+$)/g, function($1) {
            // 正向搜索后面有3个倍数的数字
            // console.log($1);
            return $1 + ",";
        })
    })
    return res;
}

let a = 1234567894532;
let b = 673439.4542;
console.log(numFormat(a)); // "1,234,567,894,532"
console.log(numFormat(b)); // "673,439.4542"
```



## 42.正则表达式的基本运用

### 判断是否是电话号码

```js
function isPhone(tel) {
	let regx = /^1[345789]\d{9}$/;
	return regx.test(tel);
}
```

### 验证是否是邮箱

```js
function isEmail(email) {
	let regx = /^([a-zA-Z0-9_\-]+@([a-zA-Z0-9_\-]+\.)+([a-zA-Z]+)$/;
    return regx.test(email);
}
```

###  验证是否是身份证

```js
function isCardNo(number) {
    // 15位身份证，18位身份，
    let regx = /(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/;
    return regx.test(number);
}
```



## 43.手写trim

记住空格的转义符是\s

### 字符串拆分数组

```js
String.prototype.myTrim = function() {
    let arr = this.split('');
    let i = 0;
    while (arr[i] === ' ') {
        arr.shift();
    }
    i = arr.length - 1;
    while (arr[i] === ' ') {
        arr.pop();
        i--;
    }
    return arr.join('');
}

console.log('   ab cdd  '.myTrim());
```

### 正则表达式

```js
String.prototype.myTrim = function() {
    return this.replace(/^\s+/, '').replace(/\s+$/, '');
}

console.log('   ab cdd  '.myTrim());
```

可以利用g后缀合并

```js
String.prototype.myTrim = function() {
    return this.replace(/^\s+|\s+$/g, '');
}

console.log('   ab cdd  '.myTrim());
```

上述方法假设至少存在一个空白符，因此效率较低，效率较高的写法如下

```js
String.prototype.myTrim = function() {
    return this.replace(/^\s\s*/, '').replace(/\s\s*$/, '');
}

console.log('   ab cdd  '.myTrim());
```

### 字符串截取

普通的原生字符串截取方法是远胜于正则替换，虽然是复杂一点。但只要正则不过于复杂，我们就可以利用浏览器对正则的优化，改善程序执行效率。

```js
String.prototype.myTrim = function() {
	// 优化正则替换前面的空格
    let str = this.replace(/^\s\s*/, '');
    let ws = /\s/;
    // 从后向前查找末尾空格
    let i = str.length;
    // while循环字符串charAt查找效率比较高
    while (ws.test(str.charAt(--i)));
    return str.slice(0, i + 1);
}

console.log('   ab cdd  '.myTrim());
```

更多方法及效率分析请参考[JavaScript trim函数大赏](https://www.cnblogs.com/rubylouvre/archive/2009/09/18/1568794.html)



## 44.版本号比较

将输入字符串数组，按照版本号排序，

例如：
输入：var versions=['1.45.0','1.5','6','3.3.3.3.3.3.3']
输出：var sorted=['1.5','1.45.0','3.3.3.3.3.3','6']

```js
// 比较两个版本的大小
function compareVersion(version1, version2) {
	// 先判断2个版本号是否是字符串
	if (!version1 || !version2 || Object.prototype.toString.call(version1) !== '[object String]'|| Object.prototype.toString.call(version2) !== '[object String]') throw new Error("Version is null!");
	// 按.将2个version进行分割
	let arr1 = version1.trim().split('.');
	let arr2 = version2.trim().split('.');
	
	// 长度
	const len = Math.min(arr1.length, arr2.length);
	for(let i = 0; i < len; i++) {
		if (Number(arr1[i]) < Number(arr2[i])) return - 1;
		else if (Number(arr1[i]) > Number(arr2[i])) return 1;
	}
	return 0;
}
let versions = ['1.45.0', '1.5', '6', '3.3.3.3.3.3.3'];
console.log(versions.sort((a, b) => compareVersion(a, b)));
```



## 45.手写Object.freeze

**Object.freeze()功能介绍**

> `Object.freeze`冻结一个对象，让其不能再添加/删除属性，也不能修改该对象已有属性的可枚举性、可配置可写性，也不能修改已有属性的值和它的原型属性，最后返回一个和传入参数相同的对象

需要用到**`Object.seal()`**，该方法封闭一个对象，阻止添加新属性并将所有现有属性标记为不可配置。当前属性的值只要原来是可写的就可以改变。

```js
function freeze(obj){
    // 判断参数是否为Object类型
    if (obj instanceof Object) {
        // 封闭对象
        Object.seal(obj);
    }
    for (let key in obj) {
        if (obj.hasOwnProperty(key)) {
            // 设置只读
            Object.defineProperty(obj, key, {
                writable: false
            });
            // 如果属性值依然为对象，要通过递归来进行进一步的冻结
            if (isObject(obj[key])) freeze(obj[key]);
        }
    }
}
```



## 46.实现ES6的extends

**Object.setPrototypeOf():**

该方法设置一个指定的对象的原型 ( 即, 内部[[Prototype]]属性）到另一个对象或  [`null`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/null)。

语法

```js
Object.setPrototypeOf(obj, proto);
```

参数

obj：要设置原型对象的对象。

proto：该对象的新原型对象或null，否则抛出TypeError异常。

返回值

设置了新的原型对象的对象。

**Object.getPrototypeOf():**

该方法用于获取指定对象的原型对象。

语法

```js
Object.getPrototypeOf(obj);
```

参数

obj：要获取原型对象的对象。

返回值

返回指定对象的原型对象或null。

```js
function B(name) {
	this.name = name;
}

function A(name, age) {
	// 1.将A的原型指向B
	Object.setPrototypeOf(A, B);
	// 2.用A的实例作为this调用B,得到继承B之后的实例，这一步相当于调用super
	Object.getPrototypeOf(A).call(this, name);
	// 3.将A原有的属性添加到新实例上
	this.age = age;
	// 4.返回新实例对象
	return this;
}

let a = new A('poetry',22);
console.log(a);
/*
 * age: 22
 * name: "poetry"
 */
```



## 47.手写实现Set

Set是ES6提供给我们的构造函数，能够造出一种新的存储数据的结构，只有属性值，成员值唯一（不重复）。手写全部方法有点难，只有部分常用的add、has、delete一定要写出来,引用类型测试错误，估计也不会挖那么深

```js
class MySet{
	constructor(iterator = []) {
		// 判断构造的初始数据是否是可迭代对象
		if (typeof iterator[Symbol.iterator] !== 'function') {
			throw new Error(`你提供的${iterator}不是一个可迭代的对象`);
		}
		// 存储数据
		this.items = {};
		// 长度;
		this.size = 0;
		// 循环可迭代对象，将结果加入到MySet中
		for (const item of iterator) {
			this.add(item);
		}
	}
	// 在MySet对象尾部添加一个元素。返回该MySet对象。
	add(data) {
		if (!this.has(data)) {
			this.items[data] = data;
			this.size++;
		}
		return this;
	}
	// 返回一个布尔值，表示该值在MySet中存在与否
	has(data) {
		return this.items.hasOwnProperty(data);
	}
	// 移除MySet中与这个值相等的元素
	delete(data) {
		if (this.has(data)) {
			delete this.items[data];
			this.size--;
			return true;
		} else {
			return false;
		}
	}
	// 移除MySet对象内的所有元素。
	clear() {
		this.items = {};
		this.size = 0;
	}
	// 返回一个新的迭代器对象，该对象包含MySet对象中的按插入顺序排列的所有元素的值。
	keys() {
		let keys = [];
		for (let key in this.items) {
			if (this.items.hasOwnProperty(key)) {
				keys.push(key);
			}
		}
		return keys;
	}
	// 返回一个新的迭代器对象，该对象包含MySet对象中的按插入顺序排列的所有元素的值。
	values() {
		let values = [];
		for (let key in this.items) {
			if (this.items.hasOwnProperty(key)) {
				values.push(this.items[key]);
			}
		}
		return values;
	}
	// 返回一个新的迭代器对象，该对象包含MySet对象中的按插入顺序排列的所有元素的值的[value, value]数组。为了使这个方法和Map对象保持相似， 每个值的键和值相等。
	entries() {
		let entries = [];
		for (let key in this.items) {
			if (this.items.hasOwnProperty(key)) {
				entries.push([key, this.items[key]]);
			}
		}
		return entries;
	}
	// 遍历，返回一个新的迭代器对象，该对象包含MySet对象中的按插入顺序排列的所有元素的值。
    *[Symbol.iterator]() {
        for (const item of this.items) {
            yield item;
        }
    }
    // 按照插入顺序，为MySet对象中的每一个值调用一次callBackFn。如果提供了thisArg参数，回调中的this会是这个参数。
    forEach(callBackFn, thisArgs = this) {
        for (const item of this.items) {
            callBackFn.call(thisArgs, item, item, this.items);
        }
    }
}

let mySet = new MySet();

mySet.add(1); // Set [ 1 ]
mySet.add(5); // Set [ 1, 5 ]
mySet.add(5); // Set [ 1, 5 ]
mySet.add("some text"); // Set [ 1, 5, "some text" ]
console.log(mySet);
let o = {a: 1, b: 2};
mySet.add(o);
console.log(mySet);

mySet.add({a: 1, b: 2}); // o 指向的是不同的对象，所以没问题
console.log(mySet);

console.log(mySet.has(1)); // true
console.log(mySet.has(3)); // false
console.log(mySet.has(5));              // true
console.log(mySet.has(Math.sqrt(25)));   // true
console.log(mySet.has("Some Text".toLowerCase())); // true
console.log(mySet.has(o)); // true

console.log(mySet.size); // 5

console.log(mySet.delete(5));  // true,  从set中移除5
console.log(mySet.has(5));     // false, 5已经被移除

console.log(mySet.size); // 4, 刚刚移除一个值

console.log(mySet);
// logs Set(4) { 1, "some text", {…}, {…} }
```

还可以尝试着[实现基本集合操作](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set#实现基本集合操作)或者[js模拟实现一个Set集合](https://blog.csdn.net/tian_123456789/article/details/89400461)，实现两个集合的并集、交集、差集和子集。

请教了大佬实现size私有化，避免手动修改size

```js
const SIZE = Symbol();
class MySet{
    constructor(){
       this[SIZE] = 0;
    }
    get size(){
        return this[SIZE];
    }
}
```

或者使用Proxy实现

![](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210502151428.png)

大佬的实用改进版

![image-20210502151520837](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210502151527.png)



## 48.手写实现Map 

map也是ES6提供给我们的构造函数，能够造出一种新的存储数据的结构。本质上是键值对的集合。key对应value，key和value唯一，任何值都可以当属性。自己的手写版问题和Set类似。

```js
class MyMap {
    constructor(iterator = []) {
        // 判断构造的初始数据是否是可迭代对象
        if (typeof iterator[Symbol.iterator] !== 'function') {
            throw new Error(`你提供的${iterator}不是一个可迭代的对象`);
        }
        // 存储数据
        this.items = {};
        // 长度;
        this.size = 0;
        // 循环可迭代对象，将结果加入到MySet中
        for (const item of iterator) {
            // item也是一个可迭代的对象
            if (typeof item[Symbol.iterator] !== "function") {
                throw new Error(`你提供的${item}不是一个可迭代的对象`);
            }
            const iterator = item[Symbol.iterator]();
            const key = iterator.next().value;
            const value = iterator.next().value;
            this.set(key, value);
        }
    }
    // 设置MyMap对象中键的值。返回该MyMap对象。
    set(key, value) {
        if (!this.items.hasOwnProperty(key)) {
            this.size++;
        }
        this.items[key] = value;
        return this;
    }
    // 返回一个布尔值，表示MyMap实例是否包含键对应的值
    has(key) {
        return this.items.hasOwnProperty(key);
    }
    // 返回键对应的值，如果不存在，则返回undefined。
    get(key) {
        if (this.items.hasOwnProperty(key)) {
            return this.items[key];
        } else {
            return undefined;
        }
    }
    // 如果MyMap对象中存在该元素，则移除它并返回 true；否则如果该元素不存在则返回 false
    delete(key) {
        if (this.items.hasOwnProperty(key)) {
            delete this.items[key];
            this.size--;
            return true;
        } else {
            return false;
        }
    }
    // 移除MyMap对象内的所有元素。
    clear() {
        this.items = {};
        this.size = 0;
    }
    // 返回一个新的迭代器对象，该对象包含MyMap对象中的按插入顺序排列的所有元素的值。
    keys() {
        let keys = [];
        for (let key in this.items) {
            if (this.items.hasOwnProperty(key)) {
                keys.push(key);
            }
        }
        return keys;
    }
    // 返回一个新的迭代器对象，该对象包含MyMap对象中的按插入顺序排列的所有元素的值。
    values() {
        let values = [];
        for (let key in this.items) {
            if (this.items.hasOwnProperty(key)) {
                values.push(this.items[key]);
            }
        }
        return values;
    }
    // 返回一个新的迭代器对象，该对象包含Set对象中的按插入顺序排列的所有元素的值的[value, value]数组。为了使这个方法和Map对象保持相似， 每个值的键和值相等。
    entries() {
        let entries = [];
        for (let key in this.items) {
            if (this.items.hasOwnProperty(key)) {
                entries.push([key, this.items[key]]);
            }
        }
        return entries;
    }
    // 遍历，返回一个新的迭代器对象，该对象包含MyMap对象中的按插入顺序排列的所有元素的值。
    *[Symbol.iterator]() {
        for (const key in this.items) {
            yield [this.items[key], key];
        }
    }
    // 按照插入顺序，为MyMap对象中的每一个值调用一次callBackFn。如果提供了thisArg参数，回调中的this会是这个参数。
    forEach(callBackFn, thisArgs = this) {
        for (const key in this.items) {
            callBackFn.call(thisArgs, this.items[key], key, this.items);
        }
    }
}

let myMap = new MyMap();

let keyObj = {};
let keyFunc = function() {};
let keyString = 'a string';

// 添加键
myMap.set(keyString, "和键'a string'关联的值");
myMap.set(keyObj, "和键keyObj关联的值");
myMap.set(keyFunc, "和键keyFunc关联的值");
console.log(myMap);
console.log(myMap.size); // 3

// 读取值
console.log(myMap.get(keyString));    // "和键'a string'关联的值"
console.log(myMap.get(keyObj));       // "和键keyObj关联的值"
console.log(myMap.get(keyFunc));      // "和键keyFunc关联的值"

console.log(myMap.get('a string'));   // "和键'a string'关联的值"
                         // 因为keyString === 'a string'
console.log(myMap.get({}));           // undefined, 因为keyObj !== {}
console.log(myMap.get(function() {})); // undefined, 因为keyFunc !== function () {}
```

除了上述问题，还有最重要的一个问题，Map 和 Object 是有区别，虽然两者都是键/值对的对象 ；

ES6中Map相对于Object对象有几个区别：

​	1：Object对象有原型， 也就是说他有默认的key值在对象上面， 除非我们使用Object.create(null)创建一个没有原型的对象；
　2：在Object对象中， 只能把String和Symbol作为key值， 但是在Map中，key值可以是任何基本类型(String, Number, Boolean, undefined, NaN….)，或者对象(Map, Set, Object, Function , Symbol , null….);
　3：通过Map中的size属性， 可以很方便地获取到Map长度， 要获取Object的长度， 你只能用别的方法了；
​	Map实例对象的key值可以为一个数组或者一个对象，或者一个函数，比较随意 ，而且Map对象实例中数据的排序是根据用户push的顺序进行排序的， 而Object实例中key,value的顺序就是有些规律了， (他们会先排数字开头的key值，然后才是字符串开头的key值)；



## 49.检测对象循环引用

检测对象自身是否循环引用，其实改进后的深拷贝已经囊括了这一检查方案

为此，WeakSet非常适合处理这种情况使用WeakSet简化，注意需要在第一次运行时创建`WeakSet`，并将其与每个后续函数调用一起传递（使用内部参数_refs）。 WeakSet只能存放对象，且对象的数量或它们的遍历顺序无关紧要，因此，WeakSet比[`Set`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)更适合（和执行）跟踪对象引用，尤其是在涉及大量对象时。

```js
// 对传入的obj对象 检查有无循环引用情况
function execRecursively(obj) {
    // 存储前层级对象
    let ws = new WeakSet();
    // 标志位
    let flag = false;
    function dp(obj) {
        // 保证当前的元素是对象，若已经存在循环，也直接返回
        if (typeof obj !== "object" || flag) return;
        // 存储当前层对象
        let cws = new WeakSet();
        if (!ws.has(obj)) ws.add(obj);
        // 一次遍历检查当前层是否有相同元素
        for (let key in obj) {
            if (typeof obj[key] === "object") {
                // 如果同层级的引用相同，把它删除
                if (cws.has(obj[key])) {
                    // 找到循环引用
                    delete obj[key];
                } else {
                    cws.add(obj[key]);
                }
            }
        }
        // 二次遍历检查当前层是否存在循环引用
        for (let key in obj) {
            if (typeof obj[key] === "object") {
                if (ws.has(obj[key])) {
                    // 找到循环引用
                    flag = true;
                    break;
                } else {
                    ws.add(obj[key]);
                }
                // 递归检查有无循环使用
                dp(obj[key]);
            }
        }
    }
    dp(obj);
    return flag;
}

let obj1 = { a: "1" };
obj1.b = {};
obj1.b.a = obj1.b;
obj1.b.b = obj1.b;


let obj2 = { a: { c: "1" } };
obj2.a.b = obj2;

let obj3 = { a: 1, b: 2, c: { d: 4 }, d: {}, e: {} };

let obj4 = { a: "1" };
obj4.b = { c: 1 };
obj4.aa = obj4.b;
obj4.bb = obj4.b;

let obj5 = { a: "1" };
obj5.b = {};
obj5.b.a = obj5.b;
obj5.b.b = obj5.b;

let obj6 = { a: {c: "1"} };
obj6.b = {};
obj6.b.d = obj6.a;

console.log(execRecursively(obj1)); // true
console.log(execRecursively(obj2)); // true
console.log(execRecursively(obj3)); // false
console.log(execRecursively(obj4)); // false
console.log(execRecursively(obj5)); // true
console.log(execRecursively(obj6)); // false
```

第6个case目前正在思考算不算循环引用。



## 50.单例模式

在合适的时候才创建对象，并且只创建唯一的一个。在单例模式下创建对象和管理单例的职责被分布在两个不同的方法中，这两个方法组合起来才具有单例模式的威力。

使用闭包实现单例模式，我写的这个又被称为懒汉式单例模式，没有一开始就对这个类进行实例化：

```js
function Singleton (name) {
	this.name = name;
};

Singleton.getInstance = (function(name) {
	// 实例对象
	let instance;
	return function(name) {
		if (!instance) {
			instance = new Singleton(name);
		}
		return instance;
	}
})();

// 测试
var a = Singleton.getInstance('ConardLi');
var b = Singleton.getInstance('ConardLi2');

console.log(a === b); // true
```



## 51.观察者模式

首先想分析一下观察者模式和发布/订阅模式的异同

### 观察者模式与发布/订阅模式区别

在翻阅资料的时候，有人把观察者（Observer）模式等同于发布（Publish）/订阅（Subscribe）模式，也有人认为这两种模式还是存在差异，而我认为确实是存在差异的，本质上的区别是调度的地方不同。

#### 观察者模式

比较概念的解释是，目标和观察者是基类，目标提供维护观察者的一系列方法，观察者提供更新接口。具体观察者和具体目标继承各自的基类，然后具体观察者把自己注册到具体目标里，在具体目标发生变化时候，调度观察者的更新方法。

比如有个“天气中心”的具体目标A，专门监听天气变化，而有个显示天气的界面的观察者B，B就把自己注册到A里，当A触发天气变化，就调度B的更新方法，并带上自己的上下文。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210503155419.png)

#### 发布/订阅模式

比较概念的解释是，订阅者把自己想订阅的事件注册到调度中心，当该事件触发时候，发布者发布该事件到调度中心（顺带上下文），由调度中心统一调度订阅者注册到调度中心的处理代码。

比如有个界面是实时显示天气，它就订阅天气事件（注册到调度中心，包括处理程序），当天气变化时（定时获取数据），就作为发布者发布天气信息到调度中心，调度中心就调度订阅者的天气处理程序。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210503160337.png)

#### 总结

​	1.从两张图片可以看到，最大的区别是调度的地方。

虽然两种模式都存在订阅者和发布者（具体观察者可认为是订阅者、具体目标可认为是发布者），但是观察者模式是由具体目标调度的，而发布/订阅模式是统一由调度中心调的，所以观察者模式的订阅者与发布者之间是存在依赖的，而发布/订阅模式则不会。

​	2.两种模式都可以用于松散耦合，改进代码管理和潜在的复用。

### 观察者模式的实现

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210503171033.webp)

观察者模式的优点

- 可以广泛应用于异步编程，它可以代替我们传统的回调函数
- 我们不需要关注对象在异步执行阶段的内部状态，我们只关心事件完成的时间点
- 角色很明确，没有事件调度中心作为中间者一个对象不必显式调用另一个对象的接口，而是松耦合的联系在一起 。目标对象`Subject`和观察者`Observer`都要实现约定的成员方法。
- 双方联系紧密，目标对象的主动性很强，自己收集和维护观察者，并在状态变化时主动通知观察者更新。虽然不知道彼此的细节，但不影响相互通信。更重要的是，其中一个对象改变不会影响另一个对象。

订阅者的能力非常简单，作为被动的一方，它的行为只有两个——被通知、去执行（本质上是接受发布者的调用，这步我们在发布者中已经做掉了）。

发布者的基本操作首先是增加订阅者，然后是通知订阅者，最后是移除订阅者。

```js
// 观察者
class Observer {
    /**
    * 构造器
    * @param {Function} cb 回调函数，收到目标对象通知时执行
    */
    constructor(cb) {
    	if (typeof cb === 'function') {
    		this.cb = cb;
    	} else {
    		throw new Error('Observer构造器必须传入函数类型！');
    	}
    }
    /**
    * 被目标对象通知时执行回调函数
    */
    update() {
    	this.cb();
    }
}

// 目标对象，发布者类
class Subject {
	constructor() {
		// 维护观察者列表
		this.observers = [];
	}
	/**
    * 添加一个观察者
    * @param {Observer} observer Observer实例
    */
    add(observer) {
    	this.observers.push(observer);
    }
	/**
    * 移除一个观察者
    * @param {Observer} observer Observer实例
    */
    remove(observer) {
    	this.observers.forEach((item, i) => {
    		if (item === observer) {
    			this.observers.splice(i, 1);
    		}
    	});
    }
    /**
    * 通知所有的观察者
    */
    notify() {
    	this.observers.forEach(observer => {
    		observer.update();
    	});
    }
}

const observerCallback = function() {
    console.log('我被通知了');
}
const observer = new Observer(observerCallback);

const subject = new Subject();
subject.add(observer);
subject.notify(); // 我被通知了
```

### 手写Vue Reactive

#### Vue数据双向绑定（响应式系统）的实现原理

Vue 框架是热门的渐进式 JavaScript框架。在 Vue 中，当我们修改状态时，视图会随之更新，这就是Vue的数据双向绑定（又称响应式原理）。数据双向绑定是Vue 最独特的特性之一。如果读者没有接触过 Vue，强烈建议阅读[Vue官方对响应式原理的介绍 (opens new window)](https://cn.vuejs.org/v2/guide/reactivity.html)。此处我们用官方的一张流程图来简要地说明一下Vue响应式系统的整个流程：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210503194414.png)

在 `Vue` 中，每个组件实例都有相应的 `watcher` 实例对象，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的 `setter` 被调用时，会通知 `watcher` 重新计算，从而致使它关联的组件得以更新——这是一个典型的观察者模式。这道面试题考察了受试者对Vue底层原理的理解、对观察者模式的实现能力以及一系列重要的JS知识点，具有较强的综合性和代表性。

**在Vue数据双向绑定的实现逻辑里，有这样三个关键角色：**

- `observer`（监听器）：注意，此 `observer` 非彼 `observer`。在我们上面的解析中，`observer` 作为设计模式中的一个角色，代表“订阅者”。但在`Vue`数据双向绑定的角色结构里，所谓的 `observer` 不仅是一个数据监听器，它还需要对监听到的数据进行**转发**——也就是说它**同时还是一个发布者**。
- `watcher`（订阅者）：`observer` 把数据转发给了**真正的订阅者**——`watcher`对象。`watcher` 接收到新的数据后，会去更新视图。
- `compile`（编译器）：`MVVM` 框架特有的角色，负责对每个节点元素指令进行扫描和解析，指令的数据初始化、订阅者的创建这些“杂活”也归它管~

这三者的配合过程如图所示：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210503210054.png)

#### 核心代码

> 下面实现订阅者 `Dep`：

```js
// 定义订阅者类Dep
class Dep {
	// 使用栈存储目标对象
	static stack = [];
	// 订阅者目标对象
	static target = null;
	// 订阅任务
	deps = null;
    constructor() {
        // 初始化订阅任务集合
        this.deps = new Set();
    }
    
    // 增加观察者
    depend() {
    	// 存在目标对象，添加到订阅任务集合
    	if (Dep.target) {
    		this.deps.add(Dep.target);
    	}
    }
    
    // 通知所有观察者
    notify() {
    	this.deps.forEach(watcher => watcher.update());
    }
    
    // 目标对象入栈
    static pushTarget(t) {
    	if (this.target) {
        	this.stack.push(this.target);
    	}
    	this.target = t;
    }
    
    // 目标对象出栈
    static popTarget() {
    	this.target = this.stack.pop();
    }
}
```

> 下面实现观察者 `Watcher`：

```js
// 定义观察者类Watcher
class Watcher {
    // 创建过程
    constructor(cb) {
        this.cb = cb;
    	// 执行一次回调函数
    	this.update();
    }
    // 被目标对象通知时执行回调函数
    update() {
    	// 实例入栈
    	Dep.pushTarget(this);
    	// 回调函数的值
    	this.value = this.cb();
    	Dep.popTarget();
    	return this.value;
    }
}
```

**实现reactive方法**

> 首先我们需要实现一个方法，这个方法会对需要监听的数据对象进行遍历、给它的属性加上定制的 `getter` 和 `setter` 函数。这样但凡这个对象的某个属性发生了改变，就会触发 `setter` 函数，进而通知到订阅者。这个 `setter` 函数，就是我们的监听器：

```js
// reactive方法遍历并包装对象属性
function reactive(obj) {
	// 若obj是一个对象，则遍历它
	if (obj && typeof obj === 'object') {
		Object.keys(obj).forEach(key => {
            // defineReactive方法会给目标属性装上“监听器”
			defineReactive(obj, key, obj[key]);
		})
	}
	return obj;
}

// 定义defineReactive方法
function defineReactive(obj, key, value) {
	// 新建一个订阅者
	let dep = new Dep();
	// 为当前属性安装监听器，劫持其属性
	Object.defineProperty(obj, key, { 
        get() {
        	// 订阅者更新
        	dep.depend();
        	return value;
        },
        set(newValue) {
        	// 更新值
        	value = newValue;
        	// 通知所有观察者
        	dep.notify();
        }
        
	});
	// 递归深度包装obj
    if (value && typeof value === 'object') {
    	reactive(value);
    }
}
```

将上诉三段代码整合进行测试

```js
// 定义订阅者类Dep
class Dep {
	// 使用栈存储目标对象
	static stack = [];
	// 订阅者目标对象
	static target = null;
	// 订阅任务
	deps = null;
    constructor() {
        // 初始化订阅任务集合
        this.deps = new Set();
    }
    
    // 增加观察者
    depend() {
    	// 存在目标对象，添加到订阅任务集合
    	if (Dep.target) {
    		this.deps.add(Dep.target);
    	}
    }
    
    // 通知所有观察者
    notify() {
    	this.deps.forEach(watcher => watcher.update());
    }
    
    // 目标对象入栈
    static pushTarget(t) {
    	if (this.target) {
        	this.stack.push(this.target);
    	}
    	this.target = t;
    }
    
    // 目标对象出栈
    static popTarget() {
    	this.target = this.stack.pop();
    }
}

// 定义观察者类Watcher
class Watcher {
    // 创建过程
    constructor(cb) {
        this.cb = cb;
    	// 执行一次回调函数
    	this.update();
    }
    // 被目标对象通知时执行回调函数
    update() {
    	// 实例入栈
    	Dep.pushTarget(this);
    	// 回调函数的值
    	this.value = this.cb();
    	Dep.popTarget();
    	return this.value;
    }
}

// reactive方法遍历并包装对象属性
function reactive(obj) {
	// 若obj是一个对象，则遍历它
	if (obj && typeof obj === 'object') {
		Object.keys(obj).forEach(key => {
            // defineReactive方法会给目标属性装上“监听器”
			defineReactive(obj, key, obj[key]);
		});
	}
	return obj;
}

// 定义defineReactive方法
function defineReactive(obj, key, value) {
	// 新建一个订阅者
	let dep = new Dep();
	// 为当前属性安装监听器，劫持其属性
	Object.defineProperty(obj, key, { 
        get() {
        	// 订阅者更新
        	dep.depend();
        	return value;
        },
        set(newValue) {
        	// 更新值
        	value = newValue;
        	// 通知所有观察者
        	dep.notify();
        }
        
	});
	// 递归深度包装obj
    if (value && typeof value === 'object') {
    	reactive(value);
    }
}

// 测试代码
const data = reactive({
	msg: 'aaa'
});

new Watcher(() => {
	console.log('执行：', data.msg);
});

setTimeout(() => {
	data.msg = 'hello';
}, 1000);
```

改用proxy，优势是不用遍历每个属性，需要深层遍历了

```js
// 定义订阅者类Dep
class Dep {
	// 使用栈存储目标对象
	static stack = [];
	// 订阅者目标对象
	static target = null;
	// 订阅任务
	deps = null;
    constructor() {
        // 初始化订阅任务集合
        this.deps = new Set();
    }
    
    // 增加观察者
    depend() {
    	// 存在目标对象，添加到订阅任务集合
    	if (Dep.target) {
    		this.deps.add(Dep.target);
    	}
    }
    
    // 通知所有观察者
    notify() {
    	this.deps.forEach(watcher => watcher.update());
    }
    
    // 目标对象入栈
    static pushTarget(t) {
    	if (this.target) {
        	this.stack.push(this.target);
    	}
    	this.target = t;
    }
    
    // 目标对象出栈
    static popTarget() {
    	this.target = this.stack.pop();
    }
}

// 定义观察者类Watcher
class Watcher {
    // 创建过程
    constructor(cb) {
        this.cb = cb;
    	// 执行一次回调函数
    	this.update();
    }
    // 被目标对象通知时执行回调函数
    update() {
    	// 实例入栈
    	Dep.pushTarget(this);
    	// 回调函数的值
    	this.value = this.cb();
    	Dep.popTarget();
    	return this.value;
    }
}

// reactive方法遍历并包装对象属性
function reactive(obj) {
	// 若obj是一个对象，则遍历它
	if (obj && typeof obj === 'object') {
		// 新建一个订阅者
		let dep = new Dep();
		// 为当前属性安装监听器，劫持其属性
		const handler = {
			get(obj, key) {
				// 订阅者更新
				dep.depend();
				return obj[key];
			},
			set(obj, key, newValue) {
				// 更新值
				obj[key] = newValue;
				// 通知所有观察者
				dep.notify();
			}
		} 
		// 实现代理
		return new Proxy(obj, handler);
	}
	return obj;
}

// 测试代码
const data = reactive({
	msg: 'aaa'
});

new Watcher(() => {
	console.log('执行：', data.msg);
});

setTimeout(() => {
	data.msg = 'hello';
}, 1000);
```

更完善的程序请参考[vue2.0响应式到vue3.0响应式原理](https://juejin.cn/post/6844903974282379271)

### 变种Reactive问题

原题目实现vue里的reactive函数。

```js
// 实现
function reactive(obj){
    // ...res
}
// 用例
const obj1 = reactive({
	a: 1,
	b: 2,
});

obj1.subscribe((newState, key) => {
	console.log(newState, key);
});

obj1.a = 3; // {a:3, b:2} 'a'
obj1.b = 4; // {a:3, b:4} 'b'
```

大佬提供的Object.defineProperty版本

```js
function reactive(obj) {
	// 代理对象
	const proxyObj = {};
	// 订阅事件
	const subs = [];
	// 使用Object.defineProperty需要遍历所有的键
	for (let key in obj) {
		if (obj.hasOwnProperty(key)) {
			// 使用Object.defineProperty使用getter、setter劫持对象属性
			Object.defineProperty(proxyObj, key, {
				enumerable: true,
				configurable: true,
				// 取值
				get() {
					return obj[key];
				},
				// 设置值
				set(newVal) {
					// 相同无需操作
					if(obj[key] === newVal) return;
					// 赋值
					obj[key] = newVal;
					// 遍历回调函数，打印对象和发生变化的key
					subs.forEach(fn => fn(obj, key));
					return obj[key];
				}
			});
		}
	}
	// 存储回调函数
	proxyObj.subscribe = function(fn) {
		subs.push(fn);
	}
	return proxyObj;
}

// 用例测试
const obj1 = reactive({
	a: 1,
	b: 2,
});
// 订阅函数
obj1.subscribe((newState, key) => {
	console.log(newState, key);
});

// 监测值发生变化
obj1.a = 3; // {a: 3, b: 2} 'a'
obj1.b = 4; // {a: 3, b: 4} 'b'
```

自己改用Proxy做了一版

```js
function reactive(obj) {
	// 订阅事件
	const subs = [];
	// 使用proxy无需遍历所有的键
	const handler = {
		get(target, key) {
			// 取值
			return target[key];
		},
		set(target, key, newVal) {
			// 相同无需操作
			if(target[key] === newVal) return;
			// 赋值
			target[key] = newVal;
			// 遍历回调函数，打印对象和发生变化的key
			subs.forEach(fn => fn(target, key));
			return target[key];
		}
	}
	// 代理对象
	const proxyObj = new Proxy(obj, handler);
	// 存储回调函数
	proxyObj.subscribe = function(fn) {
		subs.push(fn);
	}
	return proxyObj;
}

// 用例测试
const obj1 = reactive({
	a: 1,
	b: 2,
});
// 订阅函数
obj1.subscribe((newState, key) => {
	console.log(newState, key);
});

// 监测值发生变化
obj1.a = 3; // {a: 3, b: 2} 'a'
obj1.b = 4; // {a: 3, b: 4} 'b'
```



## 52.发布/订阅模式 (EventBus/EventEmitter)

`EventEmitter`是一个典型的发布/订阅模式，实现了事件调度中心。发布订阅模式中，包含发布者，事件调度中心，订阅者三个角色。发布者和订阅者是松散耦合的，互不关心对方是否存在，他们关注的是事件本身。发布者借用事件调度中心提供的`emit`方法发布事件，而订阅者则通过`on`进行订阅。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210503213226.webp)

**优点：**

- 发布订阅模式中，对于发布者`Publisher`和订阅者`Subscriber`没有特殊的约束，他们好似是匿名活动，借助事件调度中心提供的接口发布和订阅事件，互不了解对方是谁。
- 松散耦合，灵活度高，常用作事件总线
- 易理解，可类比于`DOM`事件中的`dispatchEvent`和`addEventListener`。

**缺点：**

- 当事件类型越来越多时，难以维护，需要考虑事件命名的规范，也要防范数据流混乱。

`Event Bus`（Vue、Flutter 等前端框架中有出镜）和 `Event Emitter`（Node中有出镜）出场的“剧组”不同，但是它们都对应一个共同的角色——**全局事件总线**。

**在Vue中使用Event Bus来实现组件间的通讯**

> `Event Bus/Event Emitter` 作为全局事件总线，它起到的是一个**沟通桥梁**的作用。我们可以把它理解为一个事件中心，我们所有事件的订阅/发布都不能由订阅方和发布方“私下沟通”，必须要委托这个事件中心帮我们实现。

在Vue中，有时候 A 组件和 B 组件中间隔了很远，看似没什么关系，但我们希望它们之间能够通信。这种情况下除了求助于 `Vuex` 之外，我们还可以通过 `Event Bus` 来实现我们的需求。整个调用过程中，没有出现具体的发布者和订阅者（比如上面的`PrdPublisher`和`DeveloperObserver`），全程只有`bus`这个东西一个人在疯狂刷存在感。这就是全局事件总线的特点——所有事件的发布/订阅操作，必须经由事件中心，禁止一切“私下交易”！

```js
class EventEmitter {
	constructor() {
		// 存储事件监听器及回调函数
		this.listeners = {};
	}
	
	/**
    * 注册事件监听者 on方法用于安装事件监听器，它接受目标事件名和回调函数作为参数
    * @param {String} eventName 事件类型
    * @param {Function} cb 回调函数
    */
    on(eventName, cb) {
    	// 先检查一下目标事件名有没有对应的监听函数队列
    	if (!this.listeners[eventName]) {
    		// 如果没有，那么首先初始化一个监听函数队列
    		this.listeners[eventName] = [];
    	}
    	
    	// 把回调函数推入目标事件的监听函数队列里去
    	this.listeners[eventName].push(cb);
    }
    
    /**
    * 发布事件 emit方法用于触发目标事件，它接受事件名和监听函数入参作为参数
    * @param {String} eventName 事件类型
    * @param  {...any} args 参数列表，把emit传递的参数赋给回调函数
    */
    emit(eventName, ...args) {
    	// 检查目标事件是否有监听函数队列
    	if (this.listeners[eventName]) {
    		// 如果有，则逐个调用队列里的回调函数
    		this.listeners[eventName].forEach((cb) => {
    			cb(...args);
    		})
    	}
    }
    
    /**
    * off方法移除某个事件的一个监听者,移除某个事件回调队列里的指定回调函数
    * @param {String} eventName 事件类型
    * @param {Function} cb 回调函数
    */
    off(eventName, cb) {
    	if (this.listeners[eventName]) {
    		const callbacks = this.listeners[eventName];
            const index = callbacks.indexOf(cb);
            if(index !== -1) callbacks.splice(index, 1);
            if (this.listeners[eventName].length === 0) delete this.listeners[eventName];
    	}
    }
    
    /**
     * offAll移除某个事件的所有监听者
     * @param {String} eventName事件类型
     */
    offAll(eventName) {
    	if (this.listeners[eventName]) {
    		delete this.listeners[eventName];
    	}
    }
    
    /**
    * once方法为事件注册单次监听器
    * @param {String} eventName 事件类型
    * @param {Function} cb 回调函数
    */
    once(eventName, cb) {
    	// 对回调函数进行包装，使其执行完毕自动被移除
    	const wrapper = (...args) => {
    		// 使用箭头函数使this指向EventEmitter实例
    		cb.apply(this, args);
    		// 移除该回调函数
    		this.off(eventName, cb);
    	}
    	this.on(eventName, wrapper);
    }
}

// 测试
// 创建事件管理器实例
const ee = new EventEmitter();
// 注册一个chifan事件监听者
ee.on('chifan', function() { console.log('吃饭了，我们走！') });
// 发布事件chifan
ee.emit('chifan');
// 也可以emit传递参数
ee.on('chifan', function(address, food) { console.log(`吃饭了，我们去${address}吃${food}！`) });
ee.emit('chifan', '三食堂', '铁板饭'); // 此时会打印两条信息，因为前面注册了两个chifan事件的监听者

// 测试移除事件监听
const toBeRemovedListener = function() { console.log('我是一个可以被移除的监听者') };
ee.on('testoff', toBeRemovedListener);
ee.emit('testoff');
ee.off('testoff', toBeRemovedListener);
ee.emit('testoff'); // 此时事件监听已经被移除，不会再有console.log打印出来了

// 测试移除chifan的所有事件监听
ee.offAll('chifan');
console.log(ee); // 此时可以看到ee.listeners已经变成空对象了，再emit发送chifan事件也不会有反应了
```



## 53.手写事件代理

> 事件代理，可能是代理模式最常见的一种应用方式，也是一道实打实的高频面试题。它的场景是一个父元素下有多个子元素，像这样：

事件代理，可能是代理模式最常见的一种应用方式，也是一道实打实的高频面试题。它的场景是一个父元素下有多个子元素，像这样：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>事件代理</title>
</head>
<body>
  <div id="father">
    <a href="#">链接1号</a>
    <a href="#">链接2号</a>
    <a href="#">链接3号</a>
    <a href="#">链接4号</a>
    <a href="#">链接5号</a>
    <a href="#">链接6号</a>
  </div>
</body>
</html>
```

我们现在的需求是，希望鼠标点击每个 a 标签，都可以弹出“我是xxx”这样的提示。比如点击第一个 a 标签，弹出“我是链接1号”这样的提示。这意味着我们至少要安装 `6` 个监听函数给 `6` 个不同的的元素(一般我们会用循环，代码如下所示），如果我们的 `a` 标签进一步增多，那么性能的开销会更大。

```js
// 假如不用代理模式，我们将循环安装监听函数
// 所有a标签节点
const aNodes = document.getElementById('father').getElementsByTagName('a');

// 循环安装监听函数
for (let i = 0; i < aNodes.length; i++) {
	aNodes[i].addEventListener('click', funtion(e) {
		// 阻止事件默认行为
		e.preventDefault();
		alert(`我是${aNodes[i].innerText}`);
	})
}
```

> 考虑到事件本身具有“冒泡”的特性，当我们点击 a 元素时，点击事件会“冒泡”到父元素 div 上，从而被监听到。如此一来，点击事件的监听函数只需要在 div 元素上被绑定一次即可，而不需要在子元素上被绑定 N 次——这种做法就是事件代理，它可以很大程度上提高我们代码的性能。

**事件代理的实现**

用代理模式实现多个子元素的事件监听，代码会简单很多：

```js
// 获取父元素
const father = document.getElementId('father');

// 给父元素安装一次监听函数
father.addEventListener('click', function(e) {
	// 识别是否是目标子元素
	if (e.target.tagName === 'A') {
		// 以下是监听函数的函数体
		// 阻止事件默认行为
		e.preventDefault();
		alert(`我是${e.target.innerText}`);
	}
})
```

在这种做法下，我们的点击操作并不会直接触及目标子元素，而是由父元素对事件进行处理和分发、间接地将其作用于子元素，因此这种操作从模式上划分属于代理模式。



## 54.手写JSONP跨域

`JSONP` 的原理很简单，就是利用 `<script>` 标签没有跨域限制的漏洞。利用`<script>`标签不受跨域限制的特点，缺点是只能支持 `get` 请求。

- 1.创建一个script标签
- 2.设置好`src`属性，设置好回调函数`callback`名称
- 3.将script标签插入到HTML页面中
- 4.调用回调函数，获取最终的结果

代码实现

```js
function jsonp(url, callback, success) {
    // 创建一个script标签
    let script = document.createElement('script');
    // 设置好 src属性
    script.src = url;
    script.async = true;
    // src类型
    script.type = 'text/javascript'
    window[callback] = function(data) {
        success && success(data);
    }
    document.body.appendChild(script);
}
jsonp('http://xxx', 'callback', function(value) {
	console.log(value);
})
```

### 其他跨域方案

#### CORS

- `CORS`需要浏览器和后端同时支持。`IE 8` 和 `9` 需要通过 `XDomainRequest` 来实现。
- 浏览器会自动进行 `CORS` 通信，实现`CORS`通信的关键是后端。只要后端实现了 `CORS`，就实现了跨域。
- 服务端设置 `Access-Control-Allow-Origin` 就可以开启 `CORS`。 该属性表示哪些域名可以访问资源，如果设置通配符则表示所有网站都可以访问资源。

#### document.domain

- 该方式只能用于二级域名相同的情况下，比如 `a.test.com` 和 `b.test.com` 适用于该方式。
- 只需要给页面添加 `document.domain = 'test.com'` 表示二级域名都相同就可以实现跨域

#### postMessage

> 这种方式通常用于获取嵌入页面中的第三方页面数据。一个页面发送消息，另一个页面判断来源并接收消息

```js
// 发送消息端
window.parent.postMessage('message', 'http://test.com');
// 接收消息端
var mc = new MessageChannel();
mc.addEventListener('message', (event) => {
    var origin = event.origin || event.originalEvent.origin;
    if (origin === 'http://test.com') {
        console.log('验证通过')
    }
});
```



## 55.手写Promise

为什么用Promise？在传统的异步编程中，如果异步之间存在依赖关系，我们就需要通过层层嵌套回调来满足这种依赖，如果嵌套层数过多，可读性和可维护性都变得很差，产生所谓“回调地狱”，而Promise将回调嵌套改为链式调用，增加可读性和可维护性。

romise本质是一个状态机，且状态只能为以下三种：`Pending（等待态）`、`Fulfilled（执行态）`、`Rejected（拒绝态）`，状态的变更是单向的，只能从Pending -> Fulfilled 或 Pending -> Rejected，状态变更不可逆P，Promise一旦新建就立刻执行, 此时的状态是Pending(进行中)。

`then方法`接收两个可选参数，分别对应状态改变时触发的回调，resolve和reject。它们是两个函数.
 resolve函数的作用是将Promise对象的状态从'未完成'变为'成功'(由Pending变为Resolved), 在异步操作成功时,将操作结果作为参数传递出去;
 reject函数的作用是将Promise对象的状态从'未完成'变为失败(由Pending变为Rejected),在异步操作失败时调用,并将异步操作的错误作为参数传递出去。

### 基础版本

```js
class MyPromise {
	// 构造方法接收一个回调
	constuctor(fn) {
		// Promise三种状态
		this.status = 'pending';
		// 定义状态为resolved(fulfilled)的时候的状态
    	this.value = undefined;
    	// 定义状态为rejected的时候的状态
    	this.reason = undefined;
    	
    	// 成功队列, 存放成功的回调，resolve时触发
    	this.onResolvedCallbacks = [];
    	// 失败队列,  存放失败的回调，reject时触发
    	this.onRejectedCallbacks = [];
    	
    	// 由于resolve/reject是在fn内部被调用, 因此需要使用箭头函数固定this指向,指向Promise实例
    	let resolve = (value) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.value = value;
    			// 变更状态
    			this.status = 'fulfilled';
    			this.onResolvedCallbacks.forEach(callback => callback(value));
    		}
    	}
    	// 实现同resolve
    	let reject = (reason) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.reason = reason;
    			// 变更状态
    			this.status = 'rejected';
    			this.onRejectedCallbacks.forEach(callback => callback(reason));
    		}
    	}
        // 执行时可能会发生异常
        try {
            // new Promise()时立即执行fn,并传入resolve和reject
            fn(resolve, reject);
        } catch(e) {
        	reject(e);
        }
	}
}
```

### then方法

在基础版本上增加then方法

```js
class MyPromise {
	// 构造方法接收一个回调
	constructor(fn) {
		// Promise三种状态
		this.status = 'pending';
		// 定义状态为resolved(fulfilled)的时候的状态
    	this.value = undefined;
    	// 定义状态为rejected的时候的状态
    	this.reason = undefined;
    	
    	// 成功队列, 存放成功的回调，resolve时触发
    	this.onResolvedCallbacks = [];
    	// 失败队列,  存放失败的回调，reject时触发
    	this.onRejectedCallbacks = [];
    	
    	// 由于resolve/reject是在fn内部被调用, 因此需要使用箭头函数固定this指向,指向Promise实例
    	let resolve = (value) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.value = value;
    			// 变更状态
    			this.status = 'fulfilled';
    			this.onResolvedCallbacks.forEach(callback => callback(value));
    		}
    	}
    	// 实现同resolve
    	let reject = (reason) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.reason = reason;
    			// 变更状态
    			this.status = 'rejected';
    			this.onRejectedCallbacks.forEach(callback => callback(reason));
    		}
    	}
        // 执行时可能会发生异常
        try {
            // new Promise()时立即执行fn,并传入resolve和reject
            fn(resolve, reject);
        } catch(e) {
        	reject(e);
        }
	}

	// then方法,接收一个成功的回调和一个失败的回调
	then(onFullfilled, onRejected) {
		// 防止值的穿透
		if (typeof onFullfilled !== 'function') onFullfilled = v => v;
		if (typeof onRejected !== 'function') onRejected = v => v;
		// return一个新的promise
		return new MyPromise((resolve, reject) => {
			// 把onFullfilled重新包装一下,再push进resolve执行队列,这是为了能够获取回调的返回值进行分类讨论，使用箭头函数，使this指向实例
			const fulfilledFn = value => {
                try {
                    // 执行第一个(当前的)Promise的成功回调,并获取返回值
                    let x = onFullfilled(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 把onRejected重新包装一下，使用箭头函数，使this指向实例
           	const rejectedFn = value => {
                try {
                    // 执行第一个(当前的)Promise的失败回调,并获取返回值
                    let x = onRejected(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 根据状态变换
           	switch(this.status) {
           		// 当状态为pending时,把then回调push进resolve/reject执行队列,等待执行
           		case 'pending':
           			this.onResolvedCallbacks.push(fulfilledFn);
           			this.onRejectedCallbacks.push(rejectedFn);
           			break;
               	// 当状态已经变为resolve/reject时,直接执行then回调
               	case 'fulfilled':
           			fulfilledFn(this.value);
           			break;
               	case 'rejected':
           			rejectedFn(this.reason);
           			break;
           	}
		})
	}
}

let promise1 = new MyPromise((resolve, reject) => {
    resolve('Success!');
});

promise1.then((value) => {
    console.log(value);
    // expected output: "Success!"
});
```

### catch方法

catch方法本质上就是第一个参数为空函数的then方法

```js
class MyPromise {
	// 构造方法接收一个回调
	constructor(fn) {
		// Promise三种状态
		this.status = 'pending';
		// 定义状态为resolved(fulfilled)的时候的状态
    	this.value = undefined;
    	// 定义状态为rejected的时候的状态
    	this.reason = undefined;
    	
    	// 成功队列, 存放成功的回调，resolve时触发
    	this.onResolvedCallbacks = [];
    	// 失败队列,  存放失败的回调，reject时触发
    	this.onRejectedCallbacks = [];
    	
    	// 由于resolve/reject是在fn内部被调用, 因此需要使用箭头函数固定this指向,指向Promise实例
    	let resolve = (value) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.value = value;
    			// 变更状态
    			this.status = 'fulfilled';
    			this.onResolvedCallbacks.forEach(callback => callback(value));
    		}
    	}
    	// 实现同resolve
    	let reject = (reason) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.reason = reason;
    			// 变更状态
    			this.status = 'rejected';
    			this.onRejectedCallbacks.forEach(callback => callback(reason));
    		}
    	}
        // 执行时可能会发生异常
        try {
            // new Promise()时立即执行fn,并传入resolve和reject
            fn(resolve, reject);
        } catch(e) {
        	reject(e);
        }
	}

	// then方法,接收一个成功的回调和一个失败的回调
	then(onFullfilled, onRejected) {
		// 防止值的穿透
		if (typeof onFullfilled !== 'function') onFullfilled = v => v;
		if (typeof onRejected !== 'function') onRejected = v => v;
		// return一个新的promise
		return new MyPromise((resolve, reject) => {
			// 把onFullfilled重新包装一下,再push进resolve执行队列,这是为了能够获取回调的返回值进行分类讨论，使用箭头函数，使this指向实例
			const fulfilledFn = value => {
                try {
                    // 执行第一个(当前的)Promise的成功回调,并获取返回值
                    let x = onFullfilled(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 把onRejected重新包装一下，使用箭头函数，使this指向实例
           	const rejectedFn = value => {
                try {
                    // 执行第一个(当前的)Promise的失败回调,并获取返回值
                    let x = onRejected(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 根据状态变换
           	switch(this.status) {
           		// 当状态为pending时,把then回调push进resolve/reject执行队列,等待执行
           		case 'pending':
           			this.onResolvedCallbacks.push(fulfilledFn);
           			this.onRejectedCallbacks.push(rejectedFn);
           			break;
               	// 当状态已经变为resolve/reject时,直接执行then回调
               	case 'fulfilled':
           			fulfilledFn(this.value);
           			break;
               	case 'rejected':
           			rejectedFn(this.reason);
           			break;
           	}
		})
	}
    
    // catch方法,接收一个失败的回调
	catch(onRejected) {
        return this.then(undefined, onRejected);
    }
}

let p1 = new MyPromise(function(resolve, reject) {
    resolve('Success');
});

p1.then(function(value) {
    console.log(value); // "Success!"
    throw 'oh, no!';
}).catch(function(e) {
    console.log(e); // "oh, no!"
}).then(function(){
    console.log('after a catch the chain is restored');
}, function () {
    console.log('Not fired due to the catch');
});
```

### finally方法

无论当前 Promise 是成功还是失败，调用finally之后都会执行 finally 中传入的函数，并且将值原封不动的往下传。

```js
class MyPromise {
    // 构造方法接收一个回调
    constructor(fn) {
        // Promise三种状态
        this.status = 'pending';
        // 定义状态为resolved(fulfilled)的时候的状态
        this.value = undefined;
        // 定义状态为rejected的时候的状态
        this.reason = undefined;

        // 成功队列, 存放成功的回调，resolve时触发
        this.onResolvedCallbacks = [];
        // 失败队列,  存放失败的回调，reject时触发
        this.onRejectedCallbacks = [];

        // 由于resolve/reject是在fn内部被调用, 因此需要使用箭头函数固定this指向,指向Promise实例
        let resolve = (value) => {
            // 对应规范中的"状态只能由pending到fulfilled或rejected"
            if (this.status === 'pending') {
                this.value = value;
                // 变更状态
                this.status = 'fulfilled';
                this.onResolvedCallbacks.forEach(callback => callback(value));
            }
        }
        // 实现同resolve
        let reject = (reason) => {
            // 对应规范中的"状态只能由pending到fulfilled或rejected"
            if (this.status === 'pending') {
                this.reason = reason;
                // 变更状态
                this.status = 'rejected';
                this.onRejectedCallbacks.forEach(callback => callback(reason));
            }
        }
        // 执行时可能会发生异常
        try {
            // new Promise()时立即执行fn,并传入resolve和reject
            fn(resolve, reject);
        } catch(e) {
            reject(e);
        }
    }

    // then方法,接收一个成功的回调和一个失败的回调
    then(onFullfilled, onRejected) {
        // 防止值的穿透
        if (typeof onFullfilled !== 'function') onFullfilled = v => v;
        if (typeof onRejected !== 'function') onRejected = v => v;
        // return一个新的promise
        return new MyPromise((resolve, reject) => {
            // 把onFullfilled重新包装一下,再push进resolve执行队列,这是为了能够获取回调的返回值进行分类讨论，使用箭头函数，使this指向实例
            const fulfilledFn = value => {
                try {
                    // 执行第一个(当前的)Promise的成功回调,并获取返回值
                    let x = onFullfilled(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
            }
            // 把onRejected重新包装一下，使用箭头函数，使this指向实例
            const rejectedFn = value => {
                try {
                    // 执行第一个(当前的)Promise的失败回调,并获取返回值
                    let x = onRejected(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
            }
            // 根据状态变换
            switch(this.status) {
                    // 当状态为pending时,把then回调push进resolve/reject执行队列,等待执行
                case 'pending':
                    this.onResolvedCallbacks.push(fulfilledFn);
                    this.onRejectedCallbacks.push(rejectedFn);
                    break;
                    // 当状态已经变为resolve/reject时,直接执行then回调
                case 'fulfilled':
                    fulfilledFn(this.value);
                    break;
                case 'rejected':
                    rejectedFn(this.reason);
                    break;
            }
        })
    }

    // catch方法,接收一个失败的回调
    catch(onRejected) {
        return this.then(undefined, onRejected);
    }

    // finally方法
    finally(cb) {
        return this.then(
            // 执行回调,并return value传递给后面的then
            value => MyPromise.resolve(cb()).then(() => value),
            // reject同理
            reason => MyPromise.resolve(cb()).then(() => {throw reason})
        )
    }
}

let promise1 = new MyPromise((resolve, reject) => {
    resolve('Success!');
});

promise1.then((value) => {
    console.log(value);
    // expected output: "Success!"
}).finally(() => console.log('Finally!'));

let p1 = new MyPromise(function(resolve, reject) {
    resolve('Success');
});

p1.then(function(value) {
    console.log(value); // "Success!"
    throw 'oh, no!';
}).finally(function(){
    console.log('Finally!');
});
```

### Promise.resolve和Promise.reject

`Promise.resolve(value)`方法返回一个以给定值解析后的Promise 对象。如果该值为promise，返回这个promise；如果这个值是thenable（即带有"then" 方法)），返回的promise会“跟随”这个thenable的对象，采用它的最终状态；否则返回的promise将以此值完成。此函数将类promise对象的多层嵌套展平。

而`Promise.reject()`方法返回一个带有拒绝原因的Promise对象。

```js
class MyPromise {
	// 构造方法接收一个回调
	constructor(fn) {
		// Promise三种状态
		this.status = 'pending';
		// 定义状态为resolved(fulfilled)的时候的状态
    	this.value = undefined;
    	// 定义状态为rejected的时候的状态
    	this.reason = undefined;
    	
    	// 成功队列, 存放成功的回调，resolve时触发
    	this.onResolvedCallbacks = [];
    	// 失败队列,  存放失败的回调，reject时触发
    	this.onRejectedCallbacks = [];
    	
    	// 由于resolve/reject是在fn内部被调用, 因此需要使用箭头函数固定this指向,指向Promise实例
    	let resolve = (value) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.value = value;
    			// 变更状态
    			this.status = 'fulfilled';
    			this.onResolvedCallbacks.forEach(callback => callback(value));
    		}
    	}
    	// 实现同resolve
    	let reject = (reason) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.reason = reason;
    			// 变更状态
    			this.status = 'rejected';
    			this.onRejectedCallbacks.forEach(callback => callback(reason));
    		}
    	}
        // 执行时可能会发生异常
        try {
            // new Promise()时立即执行fn,并传入resolve和reject
            fn(resolve, reject);
        } catch(e) {
        	reject(e);
        }
	}

	// then方法,接收一个成功的回调和一个失败的回调
	then(onFullfilled, onRejected) {
		// 防止值的穿透
		if (typeof onFullfilled !== 'function') onFullfilled = v => v;
		if (typeof onRejected !== 'function') onRejected = v => v;
		// return一个新的promise
		return new MyPromise((resolve, reject) => {
			// 把onFullfilled重新包装一下,再push进resolve执行队列,这是为了能够获取回调的返回值进行分类讨论，使用箭头函数，使this指向实例
			const fulfilledFn = value => {
                try {
                    // 执行第一个(当前的)Promise的成功回调,并获取返回值
                    let x = onFullfilled(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 把onRejected重新包装一下，使用箭头函数，使this指向实例
           	const rejectedFn = value => {
                try {
                    // 执行第一个(当前的)Promise的失败回调,并获取返回值
                    let x = onRejected(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 根据状态变换
           	switch(this.status) {
           		// 当状态为pending时,把then回调push进resolve/reject执行队列,等待执行
           		case 'pending':
           			this.onResolvedCallbacks.push(fulfilledFn);
           			this.onRejectedCallbacks.push(rejectedFn);
           			break;
               	// 当状态已经变为resolve/reject时,直接执行then回调
               	case 'fulfilled':
           			fulfilledFn(this.value);
           			break;
               	case 'rejected':
           			rejectedFn(this.reason);
           			break;
           	}
		})
	}
    
    // catch方法,接收一个失败的回调
	catch(onRejected) {
        return this.then(undefined, onRejected);
    }
    
    // finally方法
    finally(cb) {
    	return this.then(
    		// 执行回调,并return value传递给后面的then
    		value => MyPromise.resolve(cb()).then(() => value),
    		// reject同理
    		reason => MyPromise.resolve(cb()).then(() => {throw reason})
      	)
    }
    
    // 静态的resolve方法
    static resolve(value) {
    	// 根据规范, 如果参数是Promise实例, 直接return这个实例
    	if (value instanceof MyPromise) return value;
    	return new MyPromise(resolve => resolve(value));
    }
    
    // 静态的reject方法
    static reject(reason) {
    	return new MyPromise((resolve, reject) => reject(reason));
    }
}

let promise1 = MyPromise.resolve(123);

promise1.then((value) => {
    console.log(value);
    // expected output: 123
});

function resolved(result) {
	console.log('Resolved');
}

function rejected(result) {
	console.error(result);
}

MyPromise.reject(new Error('fail')).then(resolved, rejected);
// expected output: Error: fail
```

### Promise.all

Promise.all() 它接收一个promise对象组成的数组作为参数，并返回一个新的promise对象。

当数组中所有的对象都resolve时，新对象状态变为fulfilled，所有对象的resolve的value依次添加组成一个新的数组，并以新的数组作为新对象resolve的value。
当数组中有一个对象reject时，新对象状态变为rejected，并以当前对象reject的reason作为新对象reject的reason。

```js
class MyPromise {
	// 构造方法接收一个回调
	constructor(fn) {
		// Promise三种状态
		this.status = 'pending';
		// 定义状态为resolved(fulfilled)的时候的状态
    	this.value = undefined;
    	// 定义状态为rejected的时候的状态
    	this.reason = undefined;
    	
    	// 成功队列, 存放成功的回调，resolve时触发
    	this.onResolvedCallbacks = [];
    	// 失败队列,  存放失败的回调，reject时触发
    	this.onRejectedCallbacks = [];
    	
    	// 由于resolve/reject是在fn内部被调用, 因此需要使用箭头函数固定this指向,指向Promise实例
    	let resolve = (value) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.value = value;
    			// 变更状态
    			this.status = 'fulfilled';
    			this.onResolvedCallbacks.forEach(callback => callback(value));
    		}
    	}
    	// 实现同resolve
    	let reject = (reason) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.reason = reason;
    			// 变更状态
    			this.status = 'rejected';
    			this.onRejectedCallbacks.forEach(callback => callback(reason));
    		}
    	}
        // 执行时可能会发生异常
        try {
            // new Promise()时立即执行fn,并传入resolve和reject
            fn(resolve, reject);
        } catch(e) {
        	reject(e);
        }
	}

	// then方法,接收一个成功的回调和一个失败的回调
	then(onFullfilled, onRejected) {
		// 防止值的穿透
		if (typeof onFullfilled !== 'function') onFullfilled = v => v;
		if (typeof onRejected !== 'function') onRejected = v => v;
		// return一个新的promise
		return new MyPromise((resolve, reject) => {
			// 把onFullfilled重新包装一下,再push进resolve执行队列,这是为了能够获取回调的返回值进行分类讨论，使用箭头函数，使this指向实例
			const fulfilledFn = value => {
                try {
                    // 执行第一个(当前的)Promise的成功回调,并获取返回值
                    let x = onFullfilled(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 把onRejected重新包装一下，使用箭头函数，使this指向实例
           	const rejectedFn = value => {
                try {
                    // 执行第一个(当前的)Promise的失败回调,并获取返回值
                    let x = onRejected(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 根据状态变换
           	switch(this.status) {
           		// 当状态为pending时,把then回调push进resolve/reject执行队列,等待执行
           		case 'pending':
           			this.onResolvedCallbacks.push(fulfilledFn);
           			this.onRejectedCallbacks.push(rejectedFn);
           			break;
               	// 当状态已经变为resolve/reject时,直接执行then回调
               	case 'fulfilled':
           			fulfilledFn(this.value);
           			break;
               	case 'rejected':
           			rejectedFn(this.reason);
           			break;
           	}
		})
	}
    
    // catch方法,接收一个失败的回调
	catch(onRejected) {
        return this.then(undefined, onRejected);
    }
    
    // finally方法
    finally(cb) {
    	return this.then(
    		// 执行回调,并return value传递给后面的then
    		value => MyPromise.resolve(cb()).then(() => value),
    		// reject同理
    		reason => MyPromise.resolve(cb()).then(() => {throw reason})
      	)
    }
    
    // 静态的resolve方法
    static resolve(value) {
    	// 根据规范, 如果参数是Promise实例, 直接return这个实例
    	if (value instanceof MyPromise) return value;
    	return new MyPromise(resolve => resolve(value));
    }
    
    // 静态的reject方法
    static reject(reason) {
    	return new MyPromise((resolve, reject) => reject(reason));
    }
    
    // 静态的all方法
    static all(promises) {
    	// 计数器，用来累计promise的已执行次数
    	let index = 0;
    	// 存放 promise执行后的结果
    	let res = [];
    	// 返回一个MyPromise对象
    	return new MyPromise(function (resolve, reject) {
    		for (let i = 0; i < promises.length; i++) {
    			MyPromise.resolve(promises[i]).then(
    				function(value) {
    					index++;
    					// 结果数组按照原数组的顺序依次输出
    					res[i] = value;
    					// 所有MyPromise都resolve
    					if (index === promises.length) resolve(res);
    				}, function(reason) {
    					// 第一个reject的MyPromise
    					reject(reason);
    				}
    			)
    		}
    	})
    }
}

let promise1 = MyPromise.resolve(3);
let promise2 = 42;
let promise3 = new MyPromise((resolve, reject) => {
	setTimeout(resolve, 100, 'foo');
});

MyPromise.all([promise1, promise2, promise3]).then((values) => {
	console.log(values);
});
// expected output: Array [3, 42, "foo"]
```

### Promise.race

Promise.race() 它同样接收一个promise对象组成的数组作为参数，并返回一个新的promise对象。

与Promise.all()不同，它是在数组中有一个对象（最早改变状态）resolve或reject时，就改变自身的状态，并执行响应的回调。

```js
class MyPromise {
	// 构造方法接收一个回调
	constructor(fn) {
		// Promise三种状态
		this.status = 'pending';
		// 定义状态为resolved(fulfilled)的时候的状态
    	this.value = undefined;
    	// 定义状态为rejected的时候的状态
    	this.reason = undefined;
    	
    	// 成功队列, 存放成功的回调，resolve时触发
    	this.onResolvedCallbacks = [];
    	// 失败队列,  存放失败的回调，reject时触发
    	this.onRejectedCallbacks = [];
    	
    	// 由于resolve/reject是在fn内部被调用, 因此需要使用箭头函数固定this指向,指向Promise实例
    	let resolve = (value) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.value = value;
    			// 变更状态
    			this.status = 'fulfilled';
    			this.onResolvedCallbacks.forEach(callback => callback(value));
    		}
    	}
    	// 实现同resolve
    	let reject = (reason) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.reason = reason;
    			// 变更状态
    			this.status = 'rejected';
    			this.onRejectedCallbacks.forEach(callback => callback(reason));
    		}
    	}
        // 执行时可能会发生异常
        try {
            // new Promise()时立即执行fn,并传入resolve和reject
            fn(resolve, reject);
        } catch(e) {
        	reject(e);
        }
	}

	// then方法,接收一个成功的回调和一个失败的回调
	then(onFullfilled, onRejected) {
		// 防止值的穿透
		if (typeof onFullfilled !== 'function') onFullfilled = v => v;
		if (typeof onRejected !== 'function') onRejected = v => v;
		// return一个新的promise
		return new MyPromise((resolve, reject) => {
			// 把onFullfilled重新包装一下,再push进resolve执行队列,这是为了能够获取回调的返回值进行分类讨论，使用箭头函数，使this指向实例
			const fulfilledFn = value => {
                try {
                    // 执行第一个(当前的)Promise的成功回调,并获取返回值
                    let x = onFullfilled(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 把onRejected重新包装一下，使用箭头函数，使this指向实例
           	const rejectedFn = value => {
                try {
                    // 执行第一个(当前的)Promise的失败回调,并获取返回值
                    let x = onRejected(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 根据状态变换
           	switch(this.status) {
           		// 当状态为pending时,把then回调push进resolve/reject执行队列,等待执行
           		case 'pending':
           			this.onResolvedCallbacks.push(fulfilledFn);
           			this.onRejectedCallbacks.push(rejectedFn);
           			break;
               	// 当状态已经变为resolve/reject时,直接执行then回调
               	case 'fulfilled':
           			fulfilledFn(this.value);
           			break;
               	case 'rejected':
           			rejectedFn(this.reason);
           			break;
           	}
		})
	}
    
    // catch方法,接收一个失败的回调
	catch(onRejected) {
        return this.then(undefined, onRejected);
    }
    
    // finally方法
    finally(cb) {
    	return this.then(
    		// 执行回调,并return value传递给后面的then
    		value => MyPromise.resolve(cb()).then(() => value),
    		// reject同理
    		reason => MyPromise.resolve(cb()).then(() => {throw reason})
      	)
    }
    
    // 静态的resolve方法
    static resolve(value) {
    	// 根据规范, 如果参数是Promise实例, 直接return这个实例
    	if (value instanceof MyPromise) return value;
    	return new MyPromise(resolve => resolve(value));
    }
    
    // 静态的reject方法
    static reject(reason) {
    	return new MyPromise((resolve, reject) => reject(reason));
    }
    
    // 静态的all方法
    static all(promises) {
    	// 计数器，用来累计promise的已执行次数
    	let index = 0;
    	// 存放 promise执行后的结果
    	let res = [];
    	// 返回一个MyPromise对象
    	return new MyPromise(function (resolve, reject) {
    		for (let i = 0; i < promises.length; i++) {
    			MyPromise.resolve(promises[i]).then(
    				function(value) {
    					index++;
    					// 结果数组按照原数组的顺序依次输出
    					res[i] = value;
    					// 所有MyPromise都resolve
    					if (index === promises.length) resolve(res);
    				}, function(reason) {
    					// 第一个reject的MyPromise
    					reject(reason);
    				}
    			)
    		}
    	})
    }
    
    // 静态的race方法,只要有一个promise成功了 就算成功。如果第一个失败了就失败了
    static race(promises) {
    	// 返回一个MyPromise对象
    	return new MyPromise(function(resolve, reject) {
    		// 同时执行Promise,如果有一个Promise的状态发生改变,就变更新MyPromise的状态
    		for (let promise of promises) {
    			// Promise.resolve(promise)用于处理传入值不为Promise的情况
    			MyPromise.resolve(promise).then(function(value) {
    				// 注意这个resolve是上边new MyPromise的
    				resolve(value);
    			}, function(error) {
    				reject(error);
    			})
    		}
    	})
    }
}

let promise1 = new MyPromise((resolve, reject) => {
	setTimeout(resolve, 500, 'one');
});

let promise2 = new MyPromise((resolve, reject) => {
	setTimeout(resolve, 100, 'two');
});

MyPromise.race([promise1, promise2]).then((value) => {
    console.log(value);
    // Both resolve, but promise2 is faster
});
// expected output: "two"
```

### Promise.allSettled

接受的结果与入参时的promise实例一一对应，且结果的每一项都是一个对象，告诉你结果和值，对象内都有一个属性叫“status”，用来明确知道对应的这个promise实例的状态（fulfilled或rejected），fulfilled时，对象有value属性，rejected时有reason属性，对应两种状态的返回值。

重要的一点是，他不论接受入参的promise本身的状态，会返回所有promise的结果，但这一点`Promise.all`做不到，如果你需要知道所有入参的异步操作的所有结果，或者需要知道这些异步操作是否全部结束，应该使用`promise.allSettled()`。

```js
class MyPromise {
	// 构造方法接收一个回调
	constructor(fn) {
		// Promise三种状态
		this.status = 'pending';
		// 定义状态为resolved(fulfilled)的时候的状态
    	this.value = undefined;
    	// 定义状态为rejected的时候的状态
    	this.reason = undefined;
    	
    	// 成功队列, 存放成功的回调，resolve时触发
    	this.onResolvedCallbacks = [];
    	// 失败队列,  存放失败的回调，reject时触发
    	this.onRejectedCallbacks = [];
    	
    	// 由于resolve/reject是在fn内部被调用, 因此需要使用箭头函数固定this指向,指向Promise实例
    	let resolve = (value) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.value = value;
    			// 变更状态
    			this.status = 'fulfilled';
    			this.onResolvedCallbacks.forEach(callback => callback(value));
    		}
    	}
    	// 实现同resolve
    	let reject = (reason) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.reason = reason;
    			// 变更状态
    			this.status = 'rejected';
    			this.onRejectedCallbacks.forEach(callback => callback(reason));
    		}
    	}
        // 执行时可能会发生异常
        try {
            // new Promise()时立即执行fn,并传入resolve和reject
            fn(resolve, reject);
        } catch(e) {
        	reject(e);
        }
	}

	// then方法,接收一个成功的回调和一个失败的回调
	then(onFullfilled, onRejected) {
		// 防止值的穿透
		if (typeof onFullfilled !== 'function') onFullfilled = v => v;
		if (typeof onRejected !== 'function') onRejected = v => v;
		// return一个新的promise
		return new MyPromise((resolve, reject) => {
			// 把onFullfilled重新包装一下,再push进resolve执行队列,这是为了能够获取回调的返回值进行分类讨论，使用箭头函数，使this指向实例
			const fulfilledFn = value => {
                try {
                    // 执行第一个(当前的)Promise的成功回调,并获取返回值
                    let x = onFullfilled(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 把onRejected重新包装一下，使用箭头函数，使this指向实例
           	const rejectedFn = value => {
                try {
                    // 执行第一个(当前的)Promise的失败回调,并获取返回值
                    let x = onRejected(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 根据状态变换
           	switch(this.status) {
           		// 当状态为pending时,把then回调push进resolve/reject执行队列,等待执行
           		case 'pending':
           			this.onResolvedCallbacks.push(fulfilledFn);
           			this.onRejectedCallbacks.push(rejectedFn);
           			break;
               	// 当状态已经变为resolve/reject时,直接执行then回调
               	case 'fulfilled':
           			fulfilledFn(this.value);
           			break;
               	case 'rejected':
           			rejectedFn(this.reason);
           			break;
           	}
		})
	}
    
    // catch方法,接收一个失败的回调
	catch(onRejected) {
        return this.then(undefined, onRejected);
    }
    
    // finally方法
    finally(cb) {
    	return this.then(
    		// 执行回调,并return value传递给后面的then
    		value => MyPromise.resolve(cb()).then(() => value),
    		// reject同理
    		reason => MyPromise.resolve(cb()).then(() => {throw reason})
      	)
    }
    
    // 静态的resolve方法
    static resolve(value) {
    	// 根据规范, 如果参数是Promise实例, 直接return这个实例
    	if (value instanceof MyPromise) return value;
    	return new MyPromise(resolve => resolve(value));
    }
    
    // 静态的reject方法
    static reject(reason) {
    	return new MyPromise((resolve, reject) => reject(reason));
    }
    
    // 静态的all方法
    static all(promises) {
    	// 计数器，用来累计promise的已执行次数
    	let index = 0;
    	// 存放 promise执行后的结果
    	let res = [];
    	// 返回一个MyPromise对象
    	return new MyPromise(function (resolve, reject) {
    		for (let i = 0; i < promises.length; i++) {
    			MyPromise.resolve(promises[i]).then(
    				function(value) {
    					index++;
    					// 结果数组按照原数组的顺序依次输出
    					res[i] = value;
    					// 所有MyPromise都resolve
    					if (index === promises.length) resolve(res);
    				}, function(reason) {
    					// 第一个reject的MyPromise
    					reject(reason);
    				}
    			)
    		}
    	})
    }
    
    // 静态的race方法,只要有一个promise成功了 就算成功。如果第一个失败了就失败了
    static race(promises) {
    	// 返回一个MyPromise对象
    	return MyPromise(function(resolve, reject) {
    		// 同时执行Promise,如果有一个Promise的状态发生改变,就变更新MyPromise的状态
    		for (let promise of promises) {
    			// Promise.resolve(promise)用于处理传入值不为Promise的情况
    			MyPromise.resolve(promise).then(function(value) {
    				// 注意这个resolve是上边new MyPromise的
    				resolve(value);
    			}, function(error) {
    				reject(error);
    			})
    		}
    	})
    }

    // 静态的allSettled方法
    static allSettled(promises) {
    	// 计数器，用来累计promise的已执行次数
    	let index = 0;
    	// 存放 promise执行后的结果
    	let res = [];
    	// 返回一个MyPromise对象
    	return new MyPromise(function (resolve, reject) {
    		for (let i = 0; i < promises.length; i++) {
    			MyPromise.resolve(promises[i]).then(
    				function(value) {
    					index++;
    					// 结果数组按照原数组的顺序依次输出
    					res[i] = { status: 'fulfilled', value: value };
    					// 所有MyPromise都resolve
    					if (index === promises.length) resolve(res);
    				}, function(reason) {
    					// 和第一个类似，但要注意状态
    					index++;
    					// 结果数组按照原数组的顺序依次输出
    					res[i] = { status: 'rejected', reason: reason };
    					// 所有MyPromise都resolve
    					if (index === promises.length) resolve(res);
    				}
    			)
    		}
    	})
    }
}

let resolved = MyPromise.resolve(42);
let rejected = MyPromise.reject(-1);

Promise.allSettled([resolved, rejected])
.then(function (results) {
	console.log(results);
});
// [
//    { status: 'fulfilled', value: 42 },
//    { status: 'rejected', reason: -1 }
// ]
```

### Promise.any

Promise.any() 是 ES2021 新增的特性，它接收一个 Promise 可迭代对象（例如数组），

只要其中的一个 promise 成功，就返回那个已经成功的 promise
 如果可迭代对象中没有一个 promise 成功（即所有的 promises 都失败/拒绝），就返回一个失败的 promise 和 AggregateError 类型的实例，它是 Error 的一个子类，用于把单一的错误集合在一起

```js
class MyPromise {
	// 构造方法接收一个回调
	constructor(fn) {
		// Promise三种状态
		this.status = 'pending';
		// 定义状态为resolved(fulfilled)的时候的状态
    	this.value = undefined;
    	// 定义状态为rejected的时候的状态
    	this.reason = undefined;
    	
    	// 成功队列, 存放成功的回调，resolve时触发
    	this.onResolvedCallbacks = [];
    	// 失败队列,  存放失败的回调，reject时触发
    	this.onRejectedCallbacks = [];
    	
    	// 由于resolve/reject是在fn内部被调用, 因此需要使用箭头函数固定this指向,指向Promise实例
    	let resolve = (value) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.value = value;
    			// 变更状态
    			this.status = 'fulfilled';
    			this.onResolvedCallbacks.forEach(callback => callback(value));
    		}
    	}
    	// 实现同resolve
    	let reject = (reason) => {
    		// 对应规范中的"状态只能由pending到fulfilled或rejected"
    		if (this.status === 'pending') {
    			this.reason = reason;
    			// 变更状态
    			this.status = 'rejected';
    			this.onRejectedCallbacks.forEach(callback => callback(reason));
    		}
    	}
        // 执行时可能会发生异常
        try {
            // new Promise()时立即执行fn,并传入resolve和reject
            fn(resolve, reject);
        } catch(e) {
        	reject(e);
        }
	}

	// then方法,接收一个成功的回调和一个失败的回调
	then(onFullfilled, onRejected) {
		// 防止值的穿透
		if (typeof onFullfilled !== 'function') onFullfilled = v => v;
		if (typeof onRejected !== 'function') onRejected = v => v;
		// return一个新的promise
		return new MyPromise((resolve, reject) => {
			// 把onFullfilled重新包装一下,再push进resolve执行队列,这是为了能够获取回调的返回值进行分类讨论，使用箭头函数，使this指向实例
			const fulfilledFn = value => {
                try {
                    // 执行第一个(当前的)Promise的成功回调,并获取返回值
                    let x = onFullfilled(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 把onRejected重新包装一下，使用箭头函数，使this指向实例
           	const rejectedFn = value => {
                try {
                    // 执行第一个(当前的)Promise的失败回调,并获取返回值
                    let x = onRejected(value);
                    // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
                    x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
                } catch (error) {
                    reject(error);
                }
           	}
           	// 根据状态变换
           	switch(this.status) {
           		// 当状态为pending时,把then回调push进resolve/reject执行队列,等待执行
           		case 'pending':
           			this.onResolvedCallbacks.push(fulfilledFn);
           			this.onRejectedCallbacks.push(rejectedFn);
           			break;
               	// 当状态已经变为resolve/reject时,直接执行then回调
               	case 'fulfilled':
           			fulfilledFn(this.value);
           			break;
               	case 'rejected':
           			rejectedFn(this.reason);
           			break;
           	}
		})
	}
    
    // catch方法,接收一个失败的回调
	catch(onRejected) {
        return this.then(undefined, onRejected);
    }
    
    // finally方法
    finally(cb) {
    	return this.then(
    		// 执行回调,并return value传递给后面的then
    		value => MyPromise.resolve(cb()).then(() => value),
    		// reject同理
    		reason => MyPromise.resolve(cb()).then(() => {throw reason})
      	)
    }
    
    // 静态的resolve方法
    static resolve(value) {
    	// 根据规范, 如果参数是Promise实例, 直接return这个实例
    	if (value instanceof MyPromise) return value;
    	return new MyPromise(resolve => resolve(value));
    }
    
    // 静态的reject方法
    static reject(reason) {
    	return new MyPromise((resolve, reject) => reject(reason));
    }
    
    // 静态的all方法
    static all(promises) {
    	// 计数器，用来累计promise的已执行次数
    	let index = 0;
    	// 存放 promise执行后的结果
    	let res = [];
    	// 返回一个MyPromise对象
    	return new MyPromise(function (resolve, reject) {
    		for (let i = 0; i < promises.length; i++) {
    			MyPromise.resolve(promises[i]).then(
    				function(value) {
    					index++;
    					// 结果数组按照原数组的顺序依次输出
    					res[i] = value;
    					// 所有MyPromise都resolve
    					if (index === promises.length) resolve(res);
    				}, function(reason) {
    					// 第一个reject的MyPromise
    					reject(reason);
    				}
    			)
    		}
    	})
    }
    
    // 静态的race方法,只要有一个promise成功了 就算成功。如果第一个失败了就失败了
    static race(promises) {
    	// 返回一个MyPromise对象
    	return MyPromise(function(resolve, reject) {
    		// 同时执行Promise,如果有一个Promise的状态发生改变,就变更新MyPromise的状态
    		for (let promise of promises) {
    			// Promise.resolve(promise)用于处理传入值不为Promise的情况
    			MyPromise.resolve(promise).then(function(value) {
    				// 注意这个resolve是上边new MyPromise的
    				resolve(value);
    			}, function(error) {
    				reject(error);
    			})
    		}
    	})
    }

    // 静态的allSettled方法
    static allSettled(promises) {
    	// 计数器，用来累计promise的已执行次数
    	let index = 0;
    	// 存放 promise执行后的结果
    	let res = [];
    	// 返回一个MyPromise对象
    	return new MyPromise(function (resolve, reject) {
    		for (let i = 0; i < promises.length; i++) {
    			MyPromise.resolve(promises[i]).then(
    				function(value) {
    					index++;
    					// 结果数组按照原数组的顺序依次输出
    					res[i] = { status: 'fulfilled', value: value };
    					// 所有MyPromise都resolve
    					if (index === promises.length) resolve(res);
    				}, function(reason) {
    					// 和第一个类似，但要注意状态
    					index++;
    					// 结果数组按照原数组的顺序依次输出
    					res[i] = { status: 'rejected', reason: reason };
    					// 所有MyPromise都resolve
    					if (index === promises.length) resolve(res);
    				}
    			)
    		}
    	})
    }

	// 静态的any方法
	static any(promises) {
		// 计数器，用来累计promise的已执行次数
    	let index = 0;
    	// 存放 promise执行后的结果
    	let reasons = [];
    	// 返回一个MyPromise对象
    	return new MyPromise(function (resolve, reject) {
    		for (let i = 0; i < promises.length; i++) {
    			MyPromise.resolve(promises[i]).then(
    				function(value) {
    					// 第一个resolve的MyPromise
    					resolve(value);
    				}, function(reason) {
    					// 结果数组按照原数组的顺序依次输出
    					index++;
						reasons.push(reason);
						// 所有MyPromise都resolve
						if (index === promises.length) reject(new AggregateError('All promises were rejected', reasons));
    				}
    			)
    		}
    	})
	}
}

let promises1 = [
	MyPromise.reject('ERROR A'),
	MyPromise.reject('ERROR B'),
	MyPromise.resolve('result'),
]

MyPromise.any(promises1).then((value) => {
	console.log('value: ', value);
}).catch((err) => {
	console.log('err: ', err);
})

// value:  result

//   如果所有传入的 promises 都失败：

let promises2 = [
	MyPromise.reject('ERROR A'),
	MyPromise.reject('ERROR B'),
	MyPromise.reject('ERROR C'),
]

MyPromise.any(promises2).then((value) => {
	console.log('value：', value);
}).catch((err) => {
	console.log('err：', err);
	console.log(err.message);
	console.log(err.name);
	console.log(err.errors);
})

// err：AggregateError: All promises were rejected
// All promises were rejected
// AggregateError
// ["ERROR A", "ERROR B", "ERROR C"]
```

最后的全部reject的失败了。



## 56.手写ajax封装

### 原生ajax封装

**步骤**

- 创建 `XMLHttpRequest` 实例
- 发出 HTTP 请求
- 服务器返回 XML 格式的字符串
- JS 解析 XML，并更新局部页面
- 不过随着历史进程的推进，XML 已经被淘汰，取而代之的是 JSON。

了解了属性和方法之后，根据 AJAX 的步骤，手写最简单的 GET 请求。

```js
function ajax(url, method = 'get', param = {}) {
	// 创建 XMLHttpRequest 对象
	let xhr = new XMLHttpRequest();
	// 三个参数，规定请求的类型、URL 以及是否异步处理请求。
	xhr.open(method, url, true);
	// 设置请求头，发送信息至服务器时内容编码类型，可以不写
	xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
	// 每当 readyState 属性改变时，就会调用该函数。
	xhr.onreadystatechange = function() {
		// XMLHttpRequest 代理当前所处状态。
		if (xhr.readyState === 4) {
			// 200-300请求成功
			if ((xhr.status >= 200 && xhr.status < 300) || xhr === 304) {
				// JSON.parse() 方法用来解析JSON字符串，构造由字符串描述的JavaScript值或对象
				success(JSON.parse(xhr.responseText));
			} else {
				fail && fail();
			}
		}
	}
	// 发送请求，用于实际发出 HTTP 请求。不带参数为GET请求
	xhr.send(null);
}
```

### Promise封装ajax

- 返回一个新的Promise实例
- 创建HMLHttpRequest异步对象
- 调用open方法，打开url，与服务器建立链接（发送前的一些处理）
- 监听Ajax状态信息
- 如果`xhr.readyState == 4`（表示服务器响应完成，可以获取使用服务器的响应了）
  - `xhr.status == 200`，返回resolve状态
  - `xhr.status == 404`，返回reject状态
- `xhr.readyState !== 4`，把请求主体的信息基于send发送给服务器

```js
function ajax(url, method = 'get', param = {}) {
    return new Promise((resolve, reject) => {
        // 创建 XMLHttpRequest 对象
        let xhr = new XHLHttpRequest();
        // 三个参数，规定请求的类型、URL 以及是否异步处理请求。
        xhr.open(method, url, true);
        // 设置请求头，发送信息至服务器时内容编码类型，可以不写
        xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
        // 每当 readyState 属性改变时，就会调用该函数。
        xhr.onreadystatechange() = function() {
            // XMLHttpRequest 代理当前所处状态。
            if (xhr.readyState === 4) {
                // 200-300请求成功
                if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
                    resolve(JSON.parse(xhr.responseText));
                } else {
                    reject('请求出错');
                }
            }
        }
    })
}
```



## 57.手写实现sleep

### 使用Promise

```js
function sleep(time) {
	return new Promise(function(resolve) {
		setTimeout(resolve, time);
	});
}

// 使用
sleep(1000).then(() => {
	console.log(1);
});
```

### 使用生成器Generator

```js
function* sleepGenerator(time){
	yield new Promise(function(resolve, reject) {
		setTimeout(resolve, time);
	});
}

// 使用
sleepGenerator(1000).next().value.then(() => {
	console.log(1);
});
```

### 使用async/await

```js
function sleep(time) {
	return new Promise(function(resolve) {
		setTimeout(resolve, time);
	});
}

async function output(time) {
	let out = await sleep(time);
	console.log(1);
	return out;
}

output(1000);
```

### ES5

```js
function sleep(callback, time) {
	if (typeof(callback) === 'function') {
		setTimeout(callback, time);
	}
}

function output() {
	console.log(1);
}
sleep(output, 1000);
```

### 变种题1 将setTimeout包装成sleep的函数

手写f函数

```js
setTimeout(() => console.log('hi'), 500);
const sleep = f(setTimeout);
sleep(500).then(() => console.log('hi'));
```

即实现高阶函数

```js
function f(fn) {
    return function(...args) {
        return new Promise(function(resolve) {
        	args.unshift(resolve);
        	fn(...args);
        })
    }
}

const sleep = f(setTimeout);
sleep(500).then(() => console.log('hi'));
```

或者简化成为

```js
function f(fn) {
    return function(...args) {
        return new Promise(function(resolve) {
        	fn(resolve, ...args);
        })
    }
}

const sleep = f(setTimeout);
sleep(500).then(() => console.log('hi'));
```

### 变种题2 异步循环打印

使用`promise + async await`实现异步循环打印

```js
function sleep(time, value) {
	return new Promise(function (resolve, reject) {
		setTimeout(function() {
			resolve(value);
		}, time);
	})
}

async function start() {
	for (let i = 0; i < 6; i++) {
		let res = await sleep(1000, i);
		console.log(res);
	}
}

start();
```

### 变种题3 实现 setTimeout 的同步

JS中，如果需要一系列的等待，就需要进行 setTimeout 嵌套，或者 setTimeout 时间进行倍数增长，代码可读性非常低。可以利用async/await，实现setTimeout的同步

```js
function sleep(ms) {
	return new Promise((resolve, reject) => {
		setTimeout(() => {
			console.log('Done sleeping');
			resolve(ms);
		}, ms);
	})
}
// 立即执行函数
(async() => { // 函数声明使用async，实现异步代码同步化
	console.log('Starting...');
	// 测试是否阻塞Ended!
	await sleep(500);
	console.log('Ended!');
	
	// 测试多个setTimeout是否同步执行
	for(var i = 0; i < 6; i++) {
		console.log('loop ' + i);
		await sleep(200);
	}
})();
// 外部代码依然按照事件循环顺序执行
console.log("end");
```



## 58.手写promisify

promisify函数，实现

```js
fs.readFile('1.txt', (err, data) => {
});

const newReadFile = promisify(fs.readFile);

newReadFile('1.txt')
  .then(data => {})
  .catch(err => {});
```

把代码进行实现拆解

```js
// 接受一个函数作为输入参数
function promisify(fn) {
	// 返回一个闭包函数
	return function(...args) {
		// 函数返回一个Promise
		return new Promise(function(resolve, reject) {
			// 参数最后增加一个函数的参数
			args.push(function(err, data) {
				// 根据回调函数顺序执行对应的函数
				if (err) reject(err);
				else resolve(data);
			});
			
			fn(...args);
		});
	}
}
```

或者写成以下形式方便理解：

```js
// 接受一个函数作为输入参数
function promisify(fn) {
	// 返回一个闭包函数
	return function(...args) {
		// 函数返回一个Promise
		return new Promise(function(resolve, reject) {
			// 参数最后增加一个函数的参数
			fn(...args, function(err, data) {
				// 根据回调函数顺序执行对应的函数
				if(err) reject(err);
				else resolve(data);
			});
		});
	}
}
```



## 59.实现延时执行队列

  实现一个延时执行队列，  要求分别在 1,3,4 秒后打印出 "1", "2", "3"

```js
new Queue()
    .task(1000, () => { 
        console.log(1) 
    }) 
    .task(2000, () => { 
        console.log(2) 
    }) 
        console.log(3) 
    }) 
    .start();
```

### 累加计时

```js
class Queue {
	constructor() {
		// 事件队列
		this.queue = [];
		// 定时器数组
		this.timer = [];
		// 累积开始时间
		this.startTime = 0;
	}
	// 加入事件队列
	task(time, fn) {
		// 开始时间累加
		this.startTime += time;
		// 存入开始时间和对应事件
		this.queue.push([fn, this.startTime]);
		// 链式调用
		return this;
	}
	// 开始任务
	start() {
		// 遍历队列，依次打开定时器
		for (let i = 0; i < this.queue.length; i++) {
			this.timer[i] = setTimeout(this.queue[i][0],this.queue[i][1]);
		}
	}
	// 关闭所有任务
	stop() {
        for (let i = 0; i < this.timer.length; i++) {
            clearTimeout(this.timer[i]);
        }
    }
}

const q = new Queue();
q.task(1000, () => {
	console.log(1);
})
.task(2000, () => {
	console.log(2);
})
.task(1000, () => {
	console.log(3);
})
.start();

q.stop(); // 可以随时终止任务
```

### 使用Promise

```js
class Queue {
    constructor() {
		// 事件队列
        this.queue = [];
        // 定时器
        this.timer = null;
        // 进行Promise的链式调用
        this.pro = Promise.resolve();
    }
    // 加入事件队列
    task(time, fn) {
    	// 把时间和回调函数存入事件队列
        this.queue.push([time, fn]);
		// 链式调用
        return this;
    };
	// 开始任务
    start() {
    	// 保证内部定时器this指向Queue实例
        this.queue.forEach((item) => {
        	// promise链式调用
            this.pro = this.pro.then(() => {
            	// 返回下一个promise
                return new Promise((resolve, reject) => {
                	// 打开定时器
                    this.timer = setTimeout(() => {
                    	// 允许这个任务完成后下一个任务执行
                    	resolve(item[1]());
                    }, item[0]);
                });
            });
        });
    };
	// 关闭所有任务
    stop() {
    	// 禁止链式调用
        this.pro = Promise.reject();
        // 关闭定时器
        clearTimeout(this.timer);
    }
}

const q = new Queue();
q.task(1000, () => {
	console.log(1);
})
.task(2000, () => {
	console.log(2);
})
.task(1000, () => {
	console.log(3);
})
.start();

q.stop(); // 可以随时终止任务
```

### 使用async/await

```js
class Queue {
    constructor() {
    	// 事件队列
        this.queue = [];
        // 定时器
        this.timer = null;
    }
    // 加入事件队列
    task(time, fn) {
        this.queue.push([time, fn]);
        return this;
    }
	// 开始任务
    async start() {
        for(let i = 0; i < this.queue.length; i++){
        	// 异步任务阻塞
            await new Promise((resolve)=>{
            	// 定时器
                this.timer = setTimeout(resolve, this.queue[i][0]);
            }).then(() => {
            	// 执行回调函数
                this.queue[i][1]();
            })
        }
    }
	// 关闭所有任务
    stop() {
        this.queue = [];
        clearTimeout(this.timer);
    }
}

const q = new Queue();
q.task(1000, () => {
	console.log(1);
})
.task(2000, () => {
	console.log(2);
})
.task(1000, () => {
	console.log(3);
})
.start();

q.stop(); // 可以随时终止任务
```



## 60.setTimeout实现setInterval

### setTimeout实现setInterval

`setInterval` 需要不停循环调用，这让我们想到了递归调用自身，通过 setTimeout 执行完成再递归执行，达到仿真 setInterval 的效果，先不考虑`clearInterval` 的存在

```js
function mySetInterval(callback, time) {
	function fn() {
		// 执行回调函数
		callback();
		// 延时
		setTimeout(fn, time);
	}
	// 初始化执行函数
	setTimeout(fn, time);
}

// 测试
mySetInterval(() => {
	console.log(new Date());
}, 1000);
```

`clearInterval` 的用法是 `clearInterval(id)`。而这个 `id` 是 `setInterval`的返回值，通过这个 `id` 值就能够清除指定的定时器。具体实现请参考[用setTimeout和clearTimeout简单实现setInterval与clearInterval](https://juejin.cn/post/6844903839934447629)和[你会用 setInterval, setTimeout 互相实现吗？](https://juejin.cn/post/6844903887749513223)

### setInterval实现setTimeout

```js
function mySetTimeout(callback, time) {
	// 定时器命名
	let timer = setInterval(function(){
		// 执行回调函数
		callback();
		// 执行一次关闭定时器
		clearInterval(timer);
	}, time);
	// 返回定时器
	return timer;
}

function myClearTimeout(timer) {
	// 关闭定时器
	clearInterval(timer);
}

// 测试
let timer = mySetTimeout(() => {
	console.log(new Date());
}, 1000);
myClearTimeout(timer);
```



## 61.手写fetch

### promise实现fetch

```js
function ajax(url, method = 'get', param = {}) {
    return new Promise((resolve, reject) => {
        // 创建 XMLHttpRequest 对象
        let xhr = new XHLHttpRequest();
        // 三个参数，规定请求的类型、URL 以及是否异步处理请求。
        xhr.open(method, url, true);
        // 设置请求头，发送信息至服务器时内容编码类型，可以不写
        xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
        // 每当 readyState 属性改变时，就会调用该函数。
        xhr.onreadystatechange() = function() {
            // XMLHttpRequest 代理当前所处状态。
            if (xhr.readyState === 4) {
                // 200-300请求成功
                if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
                    resolve(JSON.parse(xhr.responseText));
                } else {
                    reject('请求出错');
                }
            }
        }
    });
}

function myFetch(url) {
	// 返回一个Promise
	return new Promise(function (resolve, reject) {
		// 通过ajax发送请求
		ajax(url, function(res) {
			// 成功
			resolve(res);
		}, function (err) {
			// 失败
			reject(err);
		});
	})
}
```

### 给fetch添加一个超时控制

```js
function ajax(url, method = 'get', param = {}) {
    return new Promise((resolve, reject) => {
        // 创建 XMLHttpRequest 对象
        let xhr = new XHLHttpRequest();
        // 三个参数，规定请求的类型、URL 以及是否异步处理请求。
        xhr.open(method, url, true);
        // 设置请求头，发送信息至服务器时内容编码类型，可以不写
        xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
        // 每当 readyState 属性改变时，就会调用该函数。
        xhr.onreadystatechange() = function() {
            // XMLHttpRequest 代理当前所处状态。
            if (xhr.readyState === 4) {
                // 200-300请求成功
                if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
                    resolve(JSON.parse(xhr.responseText));
                } else {
                    reject('请求出错');
                }
            }
        }
    })
}

function myFetch(url, timeout) {
	// 返回一个Promise
	return new Promise(function (resolve, reject) {
		// 通过ajax发送请求
		ajax(url, function(res) {
			// 成功
			resolve(res);
		}, function (err) {
			// 失败
			reject(err);
		});
		// 增加一个计时器
		setTimeout(function() {
			reject('超时');
		}, timeout);
	})
}
```



## 62.手写实现Generator

### ES6对迭代器的实现

JS原生的集合类型数据结构，只有`Array`（数组）和`Object`（对象）；而`ES6`中，又新增了`Map`和`Set`。四种数据结构各自有着自己特别的内部实现，但我们仍期待以同样的一套规则去遍历它们，所以`ES6`在推出新数据结构的同时也推出了一套**统一的接口机制**——迭代器（`Iterator`）。

> `ES6`约定，任何数据结构只要具备`Symbol.iterator`属性（这个属性就是`Iterator`的具体实现，它本质上是当前数据结构默认的迭代器生成函数），就可以被遍历——准确地说，是被`for...of...`循环和迭代器的next方法遍历。 事实上，`for...of...`的背后正是对`next`方法的反复调用。

在ES6中，针对`Array`、`Map`、`Set`、`String`、`TypedArray`、函数的 `arguments` 对象、`NodeList` 对象这些原生的数据结构都可以通过`for...of...`进行遍历。原理都是一样的，此处我们拿最简单的数组进行举例，当我们用`for...of...`遍历数组时：

```js
const arr = [1, 2, 3];
const len = arr.length;
for(let item of arr) {
    console.log(`当前元素是${item}`);
}
```

> 之所以能够按顺序一次一次地拿到数组里的每一个成员，是因为我们借助数组的`Symbol.iterator`生成了它对应的迭代器对象，通过反复调用迭代器对象的`next`方法访问了数组成员，像这样：

```js
const arr = [1, 2, 3];
// 通过调用iterator，拿到迭代器对象
const iterator = arr[Symbol.iterator]();

// 对迭代器对象执行next，就能逐个访问集合的成员
for (let i = 0; i <= arr.length; i++) {
    console.log(iterator.next());
}
```

丢进控制台，我们可以看到`next`每次会按顺序帮我们访问一个集合成员：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210508104820.png)

> 而`for...of...`做的事情，基本等价于下面这通操作：

```js
const arr = [1, 2, 3];
// 通过调用iterator，拿到迭代器对象
const iterator = arr[Symbol.iterator]();

// 初始化一个迭代结果
let now = {
    done: false
};

// 循环往外迭代成员
while(!now.done) {
    now = iterator.next();
    if(!now.done) {
        console.log(`现在遍历到了${now.value}`);
    }
}
```

> 可以看出，`for...of...`其实就是`iterator`循环调用换了种写法。在ES6中我们之所以能够开心地用`for...of...`遍历各种各种的集合，全靠迭代器模式在背后给力。

ps：此处推荐阅读[迭代协议](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols)，相信大家读过后会对迭代器在ES6中的实现有更深的理解。

### 手写实现迭代器生成函数

我们说**迭代器对象**全凭**迭代器生成函数**帮我们生成。在`ES6`中，实现一个迭代器生成函数并不是什么难事儿，因为ES6早帮我们考虑好了全套的解决方案，内置了贴心的**生成器**（`Generator`）供我们使用：

```js
// 编写一个迭代器生成函数
function *iteratorGenerator() {
    yield '1号选手';
    yield '2号选手';
    yield '3号选手';
}

// 实例化
const iterator = iteratorGenerator();

// 不断调用
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
```

丢进控制台，不负众望：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210508104849.png)

写一个生成器函数并没有什么难度，但在面试的过程中，面试官往往对生成器这种语法糖背后的实现逻辑更感兴趣。下面我们要做的，不仅仅是写一个迭代器对象，而是用`ES5`去写一个能够生成迭代器对象的迭代器生成函数（解析在注释里）：

```js
// 定义生成器函数，入参是任意集合
function iteratorGenerator(list) {
    // index记录当前访问的索引
    let index = 0;
    // len记录传入集合的长度
    const len = list.length;
    return {
        // 自定义next方法
        next: function() {
            // 如果索引还没有超出集合长度，done为false
            let done = index >= len;
            // 如果done为false，则可以继续取值
            let value = done ? undefined : list[index++];
            
            // 将当前值与遍历是否完毕（done）返回
            return {
                done: done,
                value: value
            };
        }
    }
}

// 实例化
var iterator = iteratorGenerator(['1号选手', '2号选手', '3号选手']);

// 不断调用
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
```

此处为了记录每次遍历的位置，我们实现了一个闭包，借助自由变量来做我们的迭代过程中的“游标”。

运行一下我们自定义的迭代器，结果符合预期：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210508104909.png)

 

##  63.手写实现async/await

> 核心：传递给我一个`Generator`函数，把函数中的内容基于`Iterator`迭代器的特点一步步的实现

```js
// 接收一个Generator函数作为输入
function asyncToGenerator(generatorFunc) {
	// return一个function，和async保持一致
	return function() {
		// 先调用generator函数 生成迭代器
    	const gen = generatorFunc.apply(this, arguments);
    	
    	// 返回一个promise 因为外部是用.then的方式 或者await的方式去使用这个函数的返回值的
    	return new Promise((resolve, reject) => {
    		/* 内部定义一个_next函数 用来一步一步的跨过yield的阻碍
            	arg参数则是用来把promise resolve出来的值交给下一个yield
            */
            function _next(arg) {
            	let generatorResult;
            	
            	// 这个方法需要包裹在try catch中
        		// 如果报错了 就把promise给reject掉 外部通过.catch可以获取到错误
        		try {
        			generatorResult = gen.next(arg);
        		} catch(error) {
        			return reject(error);
        		}
        		
        		// gen.next() 得到的结果是一个 { value, done } 的结构
        		const { value, done } = generatorResult;
        		
        		// 如果已经完成了 这个done是在最后一次调用next后才会为true
        		if (done) {
        			// 就直接resolve这个promise
        			return resolve(value);
        		} else {
                    // 除了最后结束的时候外，每次调用gen.next()
                    // 其实是返回 { value: Promise, done: false } 的结构，
                    // 这里要注意的是Promise.resolve可以接受一个promise为参数
                    // 并且这个promise参数被resolve的时候，这个then才会被调用
                    return Promise.resolve(
                    	// 这个value对应的是yield后面的promise
                    	value
                    ).then(
                        // value这个promise被resove的时候，就会执行_next
                		// 并且只要done不是true的时候 就会递归的往下解开promise
                		function onResolve(val) {
                			_next(val);
                		},
                		// 如果promise被reject了 就再次进入_next函数
                        // 不同的是，这次的try catch中调用的是gen.throw(err)
                        // 那么自然就被catch到 然后把promise给reject掉啦
                        function onReject(err) {
                        	throw(err);
                        }
                    )
        		}
            }
            _next();
    	})
	}
}

// 测试
function* myGenerator() {
    try {
        console.log(yield Promise.resolve(1)) ;
        console.log(yield 2);   // 2
        console.log(yield Promise.reject('error'));
    } catch (error) {
        console.log(error);
    }
}
// result是一个Promise
const result = asyncToGenerator(myGenerator)();
// 输出 1 2 error
```

`async`函数就是将 `Generator` 函数的星号（`*`）替换成`async`，将`yield`替换成`await`，仅此而已

**async函数对 Generator 函数的改进，体现在以下四点**

1. 内置执行器

> `Generator`函数的执行必须靠执行器，所以才有了`co`模块，而`async`函数自带执行器。也就是说，`async`函数的执行，与普通函数一模一样，只要一行

```js
asyncReadFile();
```

> 上面的代码调用了`asyncReadFile`函数，然后它就会自动执行，输出最后结果。这完全不像 `Generator` 函数，需要调用`next`方法，或者用`co`模块，才能真正执行，得到最后结果

2.更好的语义

> `async`和`await`，比起星号和`yield`，语义更清楚了。`async`表示函数里有异步操作，`await`表示紧跟在后面的表达式需要等待结果

3.更广的适用性

> `co`模块约定，`yield`命令后面只能是 `Thunk` 函数或 `Promise` 对象，而`async`函数的`await`命令后面，可以是 `Promise` 对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）

4.返回值是 `Promise`

> `async`函数的返回值是 `Promise` 对象，这比 `Generator` 函数的返回值是 `Iterator` 对象方便多了。你可以用`then`方法指定下一步的操作

进一步说，`async`函数完全可以看作多个异步操作，包装成的一个 `Promise` 对象，而`await`命令就是内部`then`命令的语法糖



## 64.手写异步串行和异步并行

### 实现异步加法

```js
// 解决方案
// 1. promisify 实现promise版本的加法
const promiseAdd = (a, b) => new Promise((resolve, reject) => {
	// 实现一个异步加法
	setTimeout(function() {
		let res;
        try {
        	res = a + b;
        } catch(err) {
        	reject(err);
        }
        resolve(res);
	}, 100);
})

// 2. 串行处理
async function serialSum(...args) {
	// 通过数组reduce办法实现加法，初始值不是简单的0，需要设置为Promise.resolve(0);
	return args.reduce((task, now) => task.then(res => promiseAdd(res, now)), Promise.resolve(0));
}

// 3. 并行处理 需要两两分组求和递归处理
async function parallelSum(...args) {
	// 递归重点，只有一个数
	if (args.length === 1) return args[0];
	// 存放本轮的异步任务
	const tasks = [];
	for (let i = 0; i < args.length; i+= 2) {
		// 两两相加，注意最后可能需要补零
		tasks.push(promiseAdd(args[i], args[i + 1] || 0));
	}
	// 使用Promise.all并行处理
	const results = await Promise.all(tasks);
	return parallelSum(...results);
}

// 测试
(async () => {
    console.log('Running...');
    const res1 = await serialSum(1, 2, 3, 4, 5, 8, 9, 10, 11, 12);
    console.log(res1);
    const res2 = await parallelSum(1, 2, 3, 4, 5, 8, 9, 10, 11, 12);
    console.log(res2);
    console.log('Done');
})()
```

### 实现异步串行的方法

初始代码如下

```js
function sleep(time) {
	return new Promise(function(resolve) {
		console.log(`wait ${time}s`)
		setTimeout(function() {
			console.log('execute');
			resolve();
		}, time * 100);
	})
}

const arr = [3, 4, 5];
```

一个封装的延迟函数，然后一个装有 3,4,5 的数组，需求就是在开始执行时依次等待 3, 4, 5 秒，并在之后打印对应输出

```sh
wait 3s // 等待3s

execute
wait 4s // 等待4s

execute
wait 5s // 等待5s

execute
```

#### async/await

```js
function sleep(time) {
	return new Promise(function(resolve) {
		console.log(`wait ${time / 10}s`);
		setTimeout(function() {
			console.log('execute');
			resolve();
		}, time * 100);
	})
}

const arr = [3, 4, 5];

(async function() {
	for (let i = 0; i < arr.length; i++) {
		await sleep(arr[i]);
	}
})();
```

#### reduce

```js
function sleep(time) {
	return new Promise(function(resolve) {
		console.log(`wait ${time / 10}s`);
		setTimeout(function() {
			console.log('execute');
			resolve();
		}, time * 100);
	})
}

const arr = [3, 4, 5];

// 初始化为Promise.resolve()
arr.reduce((promise, time) => {
	return promise.then(() => sleep(time));
}, Promise.resolve());
```

更多方法参考[来说一下如何串行执行多个 Promise](https://www.manman.io/2019/03/21/promise_series/)

### 实现异步串行的方法

不使用Promise.all，但本质上很接近其实现，准确来说更接近Promise.allSettled的实现，将rejected的结果转换为null，返回输出数组

```js
execter([
    Promise.resolve(1),
    Promise.reject(2),
    Promise.resolve(3)
]).then(res => {
	console.log(res); // [1,null,3]
})
```

实现execter函数

```js
function execter(arr) {
	// 返回一个Prommise
	return new Promise((resolve) => {
		// 结果数组
		const len = arr.length;
		let result = new Array(len);
		let index = 0;
		// 将结果放入结果数组
		function setData(data, i) {
			result[i] = data;
			index++;
			if (index === len) {
				resolve(result);
			}
		}
		for (let i = 0; i < len; i++) {
			Promise.resolve(arr[i]).then(data => {
				setData(data, i);
			}, err => {
				setData(null, i);
			});
		}
	})
}

// 测试
execter([
    Promise.resolve(1),
    Promise.reject(2),
    Promise.resolve(3)
]).then(res => {
	console.log(res); // [1, null, 3]
})
```



## 65.异步并发数限制

### 使用队列缓存并发任务

```js
/**
 * 关键点
 * 1. new promise 一经创建，立即执行
 * 2. 使用 Promise.resolve().then 可以把任务加到微任务队列，防止立即执行迭代方法
 * 3. 微任务处理过程中，产生的新的微任务，会在同一事件循环内，追加到微任务队列里
 * 4. 使用 race 在某个任务完成时，继续添加任务，保持任务按照最大并发数进行执行
 * 5. 任务完成后，需要从 doingTasks 中移出
 */
function limit(count, array, iterateFunc) {
    // 所有任务列表结果
    const tasks = [];
    const doingTasks = [];
    // 完成的任务数量
    let i = 0;
    // 依次入栈
    function enqueue() {
        // 所有任务都完成了，实现任务完成
        if (i === array.length) {
            return Promise.resolve();
        }
        // 可以把任务加到微任务队列，防止立即执行迭代方法
        const task = Promise.resolve().then(() => iterateFunc(array[i++]));
        // 将任务存入任务数组中
        tasks.push(task);
        // 任务完成后从工作数组中删除
        const doing = task.then(() => doingTasks.splice(doingTasks.indexOf(doing), 1));
        // doingTasks任务队列
        doingTasks.push(doing);
        // doingTasks长度超过count完成最快的Promise，保持任务按照最大并发数进行执行
        const res = doingTasks.length >= count ? Promise.race(doingTasks) : Promise.resolve();
        // 链式调用
        return res.then(enqueue);
    }
    return enqueue().then(() => Promise.all(tasks));
}

// test
// 本质就是sleep函数
const timeout = i => new Promise(resolve => setTimeout(() => resolve(i), i));
limit(2, [1000, 1000, 1000, 1000], timeout).then((res) => {
    console.log(res);
})
```

### 并发数目限制为2的异步调度器

我们都知道promise.all方法可以执行多个promise，你给他多少个他就执行多少个，而且是一起执行，也就是并发执行。如果你给他100个，他会同时执行100个，如果这100个promise内都包含网络请求呢？

可能有人说，这种场景不多吧，一个页面内加起来就没几个接口，何况是并发请求了

但是如果让你做个文件分片上传呢？一个几百兆的文件分片后可能有几百个片段了吧。当然这也是一种极端情况，不过这确实是一个很明显的问题，还是需要解决的。

所以需要我们控制同时执行的promise个数，比如控制为2个，后面的所有promise都排队等待前面的执行完成。

进入正题，要求的代码实现

```js
// JS实现一个带并发限制的异步调度器Scheduler，保证同时运行的任务最多有两个。完善代码中Scheduler类，使得以下程序能正确输出
class Scheduler {
    add(promiseCreator) { ... }
    // ...
}

const timeout = (time) => new Promise(resolve => {
	setTimeout(resolve, time);
})

const scheduler = new Scheduler();
const addTask = (time, order) => {
    scheduler.add(() => timeout(time))
    .then(() => console.log(order));
}

addTask(1000, '1');
addTask(500, '2');
addTask(300, '3');
addTask(400, '4');
```

不控制并发的情况下的执行顺序应该是

```sh
3
4
2
1
```

控制并发为2后的执行结果是

```sh
2
3
1
4
```

详细情况如下：

 一开始，1、2两个任务进入队列
500ms时，2完成，输出2，任务3进队
800ms时，3完成，输出3，任务4进队
1000ms时，1完成，输出1
1200ms时，4完成，输出4

这个题本身也并不难，主要还是考察对题目的理解。

**题目分析**

- 最后执行的是 `addTask` 方法，那我们首先从这个方法入手。函数执行体中执行了 `scheduler.add(fn)`，这个方法后面紧接着 `then` 方法，意味着 `scheduler.add` 返回的是一个 `Promise` 对象。
- 按照题意，当前最多只能有两个任务在运行，那么我们在 `Scheduler` 类中定义一个任务队列 `tasks` 属性，定义一个当前正在运行的任务数量 `runningTaskCount` 属性，当 `runningTaskCount` 小于2 时，马上执行 `add(promiseCreator)` 中的 `promiseCreator` 函数，否则当某一个任务执行完后再执行一个新的任务。

**思路**

注意add方法里面传入的是函数并返回Promise，这是难点，很多人都是改题，我见过拿getter、setter写的，我觉得跟题目要考的主旨不同。

先把要执行的promise function 存到数组内

既然是最多为2个，那我们必然是要启动的时候就要让两个promise函数执行

设置一个临时变量，表示当前执行ing几个promise，判断执行队列中是否满员，未满直接进队

然后一个promise执行完成将临时变量-1

然后借助递归重复执行

第三个及以后则需要判断前两者是否resolve，注意这里前两者和前两个的概念不同（由于是一层抽象，这里举例说明：目前处于第三个，那么前两者的前者指第一个到第一个，后者指第二个；目前处于第四个，那么前两者的前者指第一个到第二个，后者指第三个；以此类推），resolve后从等待队列按顺序加入到执行队列。

说下原因，有两种情况。前者先完成，也就是集合中的任务全部执行完成，那么后者一定会进入执行（未完成），那么执行队列中一定会剩下一个位置；后者先完成，这个没什么可说的，后者完成后一定会剩下一个位置。


代码如下：

```js
// JS实现一个带并发限制的异步调度器Scheduler，保证同时运行的任务最多有两个。完善代码中Scheduler类，使得以下程序能正确输出
class Scheduler {
	constructor() {
		// 待运行的任务
		this.tasks = [];
		// 正在运行的任务数量
		this.active = 0;
    	// 限制同时运行的最多任务个数
    	this.limit = 2;
    	// 完成后Promise的resolve函数
    	this.resolves = [];
	}
	// promiseCreator 是一个异步函数，返回Promise
    add(promiseCreator) {
    	// 将异步函数加入任务
    	this.tasks.push(promiseCreator);
        // 返回Promise
    	return new Promise((resolve) => {
    		// resolve队列
    		this.resolves.push(resolve);
    		// 执行任务
    		this.run();
    	})
    }
    // 执行任务
    run() {
    	// 确保异步任务不超过限制且仍存在待执行任务
		if (this.active < this.limit && this.tasks.length > 0) {
			// 活动任务增加
			this.active++;
			// 按照任务顺序找对应的异步任务和其对应resolve函数
			const resolveFn = this.resolves.shift();
			const task = this.tasks.shift();
			task().then(() => {
				// 活动任务减少
				this.active--;
				// 执行任务
				resolveFn();
				// 递归执行
				this.run();
			});
		}
    }
}

const timeout = (time) => new Promise(resolve => {
	setTimeout(resolve, time);
});

const scheduler = new Scheduler();
const addTask = (time, order) => {
    scheduler.add(() => timeout(time))
    .then(() => console.log(order));
}

addTask(1000, '1');
addTask(500, '2');
addTask(300, '3');
addTask(400, '4');
// 2 3 1 4
```

或者

```js
class Scheduler{
    constructor(){
        // 正在运行的任务数量
        this.taskNum = 0;
        // 待执行的任务
        this.taskQueue = [];
    }

    async add(promiseCreator) {
        // 在Promise内部把resolve放到任务队列中，只有当resolve被调用，后面的的代码才被执行
        if (this.taskNum >= 2) {
            await new Promise((resolve) => {
                this.taskQueue.push(resolve);
            });
        }

        this.taskNum++;
        let result = await promiseCreator();
        this.taskNum--;
        if (this.taskQueue.length > 0) {
        	// 当前任务完成后，如果任务队列里有resolve，那么就调用resolve，之前被堵住的部分就可以得到执行
            this.taskQueue.shift()();
        }
        return result;
    }
}

let scheduler = new Scheduler();

let timeout = time => new Promise((resolve) => {
    setTimeout(resolve, time);
});

function addTask(delay, num){
    scheduler.add(() => (
        timeout(delay).then(() => {
            console.log(num);
        });
    ));
}

addTask(1000, '1');
addTask(500, '2');
addTask(300, '3');
addTask(400, '4');
```

### 实现带有执行器和拦截器的并发控制

**前言**
实际场景中，我们常常会对请求并发做一些限制，比如微信小程序中wx.request的最大并发限制就是 10 个，那如何实现一个并发的限制呢？

**实现**
首先考虑下实现最大并发的流程：

请求需要先被拦截器拦截，判断是否等于限制数量
如果大于限制是数量，就把请求生成一个 Promise 先放进队列中
每个请求结束的时候都要判断队列是否为空
假设请求的 API 返回一个 Promise（当然不是的话也可以转换）

```js
/**
 * 通过promise 实现一个并发限制类
 * 两个要点：一个执行器，一个拦截器
 */
class Plimit {
	constructor(limit) {
		// 并发限制，默认为2
		this.limit = limit || 2;
		// 缓存队列
		this.queue = [];
		// 当前活跃的任务总数
		this.active = 0;
	}

	// 把任务放入队列中
	enqueue(fn) {
		// 返回一个Promise
		return new Promise((resolve, reject) => {
			// 关键代码: fn, resolve, reject 统一管理
			this.queue.push({fn, resolve, reject});
		})
	}

	// 从队列起始位置开始执行任务
	dequeue() {
		if (this.active < this.limit && this.queue.length !== 0) {
			// 等到 Promise 计数器小于阈值时，则出队执行
			const {fn, resolve, reject} = this.queue.shift();
			// 执行任务
			this.run(fn).then(resolve, reject);
		}
	}

	// async/await 执行器 简化错误处理
	async run(fn) {
		// 当前活动任务数量增加
		this.active++;
		const value = await fn();
		// 当前活动任务数量增加
		this.active++;
		// 执行完，看看队列有东西没
		this.deque();
		return value;
	}

	// 拦截器
    interceptor(fn) {
        if (this.active > this.limit) {
        	// 如果没有到达阈值，直接执行
            return this.enqueue(fn);
        } else {
        	// 如果超出阈值，则先扔到队列中，等待有空闲时执行
            return this.run(fn);
        }
    }
}
```

### [使用Promise并发限制](https://www.cnblogs.com/fuGuy/p/13112876.html)

#### 背景

我们在需要保证代码在多个异步处理之后执行,我们通常会使用

```js
Promise.all(promises: []).then(fun: function);
```

Promise.all可以保证，promises数组中所有promise对象都达到resolve状态，才执行then回调

那么会出现的情况是，你在瞬间发出几十万http请求（tcp连接数不足可能造成等待），或者堆积了无数调用栈导致内存溢出.

这个时候需要我们对HTTP的连接数做限制。

#### 内容

```js
// promise并发限制
class Plimit{
	constructor(limit, fn) {
		// 最大并发数
		this.limit = limit;
		// 自定义的请求函数
		this.fn = fn;
		// 并发队列
		this.queue = [];
		// 剩余的请求地址
		this.urls = [];
	}
	// 初始化链接
	start(urls) {
		this.urls = urls;
		// 先循环把并发池塞满
		while(this.queue.length < this.limit) {
			let url = this.urls.shift();
			this.setTask(url);
		}
		// 利用Promise.race方法来获得并发池中某任务完成的信号
		let race = Promise.race(this.queue);
		return this.run(race);
	}
	// 递归调用
	run(race) {
		race.then(res => {
			// 每当并发池跑完一个任务，就再塞入一个任务
			let url = this.urls.shift();
			this.setTask(url);
			// 递归调用
			return this.run(Promise.race(this.queue));
		})
	}
	// 网址请求
	setTask(url) {
		if (!url) return;
		// 执行函数
		let task = this.fn(url);
		// 将该任务推入queue并发队列中
		this.queue.push(task);
        console.log(`\x1B[43m ${url} 开始，当前并发数：${this.queue.length}`)
        task.then(res => {
            // 请求结束后将该Promise任务从队列中移除
            this.queue.splice(this.queue.indexOf(task), 1);
            console.log(`\x1B[43m ${url} 结束，当前并发数：${this.queue.length}`);
        })
	}
}

// 测试
const URLS = [
    'bytedance.com',
    'tencent.com',
    'alibaba.com',
    'microsoft.com',
    'apple.com',
    'hulu.com',
    'amazon.com'
];
// 自定义请求函数
var requestFn = url => {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve(`任务${url}完成`);
        }, 1000)
    }).then(res => {
        console.log('外部逻辑', res);
    });
}
const plimit = new Plimit(5, requestFn); // 并发数为5
plimit.start(URLS);
```

从上面可以看出，思路如下：定义一个 PromisePool 对象，初始化一个 pool 作为并发池，然后先循环把并发池塞满，不断地调用 setTask 然后通过自己自定义的任务函数(任务函数可以是网络请求封装的 promise 对象，或者是其他的)，而且每个任务是一个Promise对象包装的，执行完就 pop 出连接池， 任务push 进并发池 pool 中。

```js
// 利用Promise.race方法来获得并发池中某任务完成的信号
let race = Promise.race(this.queue);
return this.run(race);
// 递归调用
run(race) {
    race.then(res => {
        // 每当并发池跑完一个任务，就再塞入一个任务
        let url = this.urls.shift();
        this.setTask(url);
        // 递归调用
        return this.run(Promise.race(this.queue));
    });
}
```

这个地方就是不断通过递归的方式，每当并发池跑完一个任务，就再塞入一个任务



## 66.LazyMan

### 要求与分析

设计一个LazyMan类，实现以下功能：

```js
LazyMan('Tony');
// Hi I am Tony
LazyMan('Tony').sleep(10).eat('lunch');
// Hi I am Tony
// 等待10秒...
// I am eating lunch
LazyMan('Tony').eat('lunch').sleep(10).eat('dinner');
// Hi I am Tony
// I am eating lunch
// 等待10秒...
// I am eating dinner
LazyMan('Tony').eat('lunch').eat('dinner').sleepFirst(5).sleep(10).eat('junk food');
// Hi I am Tony
// 等待了5秒...
// I am eating lunch
// I am eating dinner
// 等待10秒
// I am eating junk food
```

分析题目：
1. 普通调用
2. 很显然要等待10秒再执行下一步
3. 前面两个按顺序调用，然后等待10秒再执行下一个，其实跟第二个差不多
4. 问题：
	a. sleep(5)写在后面却要在lunch和dinner之间调用
	b. sleep(5)没执行后面的只能等着
	c. 很长的一个链式调用

知识点:
1. 整体是一个js的面向对象编程的题目
2. 涉及到异步控制的思想
3. 执行顺序不同于调用顺序
4. 可以考虑内部维护一个数组控制调用顺序
5. 可以考虑使用Promise实现
6. 可以考虑使用async实现

### 代码实现

```js
class LazyManClass {
    // 构造函数
    constructor(name) {
        this.name = name;
        // 定义一个数组存放执行队列
        this.queue = [];
        console.log(`Hi I am ${name}`);
        // 在调用LazyManClass时首先会打印 Hi I am ${name}
        setTimeout(() => {
            this.next();
        }, 0);
    }
    // 定义原型方法
    eat (food) {
        let fn = () => {
            console.log(`I am eating ${food}`);
            this.next();
        }
        this.queue.push(fn);
        return this;
    }
    sleep (time) {
        let fn = () => {
            // 等待了time秒...
            setTimeout(() => {
                console.log(`等待了${time}秒`)
                this.next();
            }, 1000 * time);
        }
        this.queue.push(fn);
        return this;
    }
    sleepFirst (time) {
        let fn = () => {
            // 等待了time秒...
            setTimeout(() => {
                console.log(`等待了${time}秒`)
                this.next();
            }, 1000 * time);
        }
        // 置于第一个
        this.queue.unshift(fn);
        return this;
    }
    next () {
        let fn = this.queue.shift();
        fn && fn();
    }
}
function LazyMan (name) {
    return new LazyManClass(name);
}
LazyMan('Tony').eat('lunch').eat('dinner').sleepFirst(5).sleep(10).eat('junk food');
// Hi I am Tony
// 等待了5秒
// I am eating lunch
// I am eating dinner
// 等待10秒
// I am eating junk food
```

总结一下最后一步：

1.JavaScript引擎中存在着一个主线程，所有的同步任务都会在这个主线程上执行，每当一个同步任务要执行了，主线程就会把这个同步任务推入函数堆栈中，待执行完成后，主线程读取下一个同步任务到堆栈中，继续执行；

而在这个过程中若存在被注册的异步任务回调函数，这些异步任务会交给引擎中的其他模块进行处理，并在异步任务完成或是合适的时机（比如setTimeout指定的时间间隔达到了）将回调函数放到任务队列当中，一般来说不同的异步任务会有不同的任务队列，而不是所有回调都放在同一个任务队列当中。当主线程中的函数堆栈中不再有更多的同步任务时，主线程就会开始读取任务队列中的回调函数。

2.当我们调用LazyMan的时候，constructor里面首先会立即打印信息

3.然后继续往下走，遇到了settimeout，JS发现他是一个异步任务（先放到一边），settimeout是一个宏任务，然后继续执行，走到eat('lunch')，这时候创建了一个函数fn（未执行），继续走，将fn放到事件队列queue[]

此时的queue为 [eat('dinner')]

4.继续往下走 ，遇到了eat('dinner')，这时候又创建了一个fn（未执行），继续走，将fn放到事件队列queue[]

此时的queue为 [eat('lunch'),eat('dinner')]

5.再继续往下走，遇到了sleepFirst(5),看题目，题目要求它要跑到前面去，很简单，那就插个队，unshift

此时的queue为 [sleepFirst(time),eat('lunch'),eat('dinner')]

6.再继续往下走，遇到了sleep(10),这里要等待10秒，那就创建一个定时器fn，它在后面调用，正常排队

此时的queue为[sleepFirst(5),fneat'lunch'),eat('dinner'),sleep(10)]

7.再继续往下走，遇到了eat('junk food'),创建一个fn，正常排队

此时的queue为      [sleepFirst(5),fneat'lunch'),eat('dinner'),sleep(10),eat('junk food')]

8.JS会问还有没有要执行的，其实刚开始我们把settimeout放到了异步队列，
同步任务：我执行完了
异步任务：我知道了
异步任务：主线程大哥，该执行我的异步任务了，此时开始执行settimeout里面的this.next()
然后按照顺序调用。

### Promise实现

如果有promise，相当于把队列的数据结构改成链表。就必须实现一个尾插和一个头插。尾插这里也做了。头插因为promise立即执行的机制，必须和尾插错开，用settimeout就可以了。

```js
class LazyManClass {
    // 构造函数
    constructor(name) {
        console.log(`Hi I am ${name}`);
        this.head = null;
        setTimeout(() => {
            this.cur = this.head();
        }, 0);
        this.head = this.init;
    }
    init() {
        return new Promise(resolve => {
			resolve();
        });
    }
    // 定义原型方法
    eat (food) {
        setTimeout(() => {
            this.cur = this.cur.then(() => {
                return new Promise(resolve => {
                    console.log(`I am eating ${food}`);
                    resolve();
                });
            });
        }, 0);
        return this;
    }
    sleep (time) {
        setTimeout(() => {
            this.cur = this.cur.then(() => {
                return new Promise(resolve => {
                    setTimeout(() => {
                        console.log(`等待了${time}秒`);
                        resolve();
                    }, 1000 * time);
                });
            });
        }, 0);
        return this;
    }
    sleepFirst (time) {
        const temp = this.head;
        this.head = () => {
            return new Promise(resolve => {
                setTimeout(() => {
                    console.log(`等待了${time}秒`);
                    resolve();
                }, 1000 * time);
            }).then(() => temp());
        };
        return this;
    }
}
function LazyMan (name) {
    return new LazyManClass(name);
}
LazyMan('Tony').eat('lunch').eat('dinner').sleepFirst(5).sleep(10).eat('junk food');
// Hi I am Tony
// 等待了5秒...
// I am eating lunch
// I am eating dinner
// 等待10秒
// I am eating junk food
```

### 变种：实现PlayBoy类

```js
// 1. 链式调用
// 2. 实现sleep函数
class PlayBoy {
    constructor (name) {
        this.name = name;
    }
    
    sayHi () {
        console.log('大家好，我是' + this.name);
        return this;
    }
    // 时间戳
    sleep (time) {
        const start = new Date().getTime();
        while (new Date().getTime() - start < time) {}
        return this;
    }
    play (game) {
        console.log('我在玩儿' + game);
        return this;
    }
}

const playBoy = new PlayBoy('Bob');
playBoy.sayHi().sleep(2000).play('王者荣耀').sleep(3000).play('奇迹暖暖');
// 输出
// Bob
// 等待2s
// 我在玩儿王者荣耀
// 等待3s
// 我在玩儿奇迹暖暖
```



## 67.Promise超时重新请求

```js
function resend(fn，times，interval) {
    return new Promise((resolve, reject) => {
        // 记录状态
        let promise = null;
        // 执行Promise
        let executePromise = timer => {
            if (times < 1) {
                window.clearInterval(timer);
                reject(new Error('promise not until timeout'));
                return null;
            }
            times--;
            return Promise.resolve(fn).then(res => {
                window.clearInterval(timer);
                resolve(res);
            }).catch((e) => {
                throw new Error(e);
            });
        }
        // 定时器
        let timer = window.setInterval(() => {
            promise = executePromise(timer);
        }, interval);
        promise = executePromise(timer);
    })
}
```



## 68.实现Promise的first等各种变体

在标准的ES6规范中，提供了`Promise.all`和`Promise.race`两种，我们首先来了解下这两个方法是干嘛的，方便我们后面工作的展开。Promise.all中所有的Promise实例都处于完成状态，该方法才进入完成状态，否则任意一个被拒绝，则该方法进入拒绝状态，并舍弃其他所有完成的结果，拒绝原因是第一个被拒绝的实例的原因。Promise.race中任意的一个Promise实例变成完成状态或者拒绝状态，则race结束，race的结果即为第一个变成最终状态的结果！更详细的可以参考下阮一峰的文章[Promise对象之Promise.all](https://link.zhihu.com/?target=http%3A//es6.ruanyifeng.com/%23docs/promise%23Promise-all)。

### 准备工作

在开始编写各种变体方法之前，这里我们首先定义几个一会儿要使用的几个Promise实例：

```js
/**
 * 创建一个Promise对象的实例
 * @param name {string} 该实例的名称
 * @param flag {boolean} 返回的结果状态：完成还是拒绝
 * @param diff {number} 延迟的时间
 */
var createPromiseCase = (name, flag, diff) => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            flag ? resolve(name) : reject(new Error('testPromise is error, name: ' + name));
        }, diff);
    });
};

var p1_suc_100 = createPromiseCase('p1-suc-100', true, 100);
var p2_suc_500 = createPromiseCase('p2-suc-500', true, 500);
var p3_suc_300 = createPromiseCase('p3-suc-300', true, 300);
var p4_fail_400 = createPromiseCase('p4-fail-400', false, 400);
var p5_fail_200 = createPromiseCase('p5-fail-200', false, 200);
```

### Promise.first

场景：一个页面当前正处于loading状态，同时请求了多个接口，无论哪个接口正确返回结果，则loading效果取消！或者其他的要获取获取第一个完成状态的值。

这里就要用到了`Promise.first`了，只要任意一个Promise实例变成完成状态，则Promise.first变成完成状态。其实这里并不适合`Promise.race`方法，因为第一个变成拒绝状态的实例也会激活Promise.race，

```js
// get first resolve result
Promise.first = promiseList => {
    return new Promise((resolve, reject) => {
        let num = 0;
        const len = promiseList.length;
        promiseList.forEach(promise => {
            Promise.resolve(promise)
                .then(resolve)
                .catch(() => {
                num++;
                if (num === len) {
                    reject('all promises not resolve');
                }
            });
        });
    });
}
```

调用方式：

```js
Promise.first([p4_fail_400, p2_suc_500, p3_suc_300])
    .then(res => console.log(res)) // p3-suc-300
    .catch(e => console.error(e));
```

完整测试代码

```js
/**
 * 创建一个Promise对象的实例
 * @param name {string} 该实例的名称
 * @param flag {boolean} 返回的结果状态：完成还是拒绝
 * @param diff {number} 延迟的时间
 */
var createPromiseCase = (name, flag, diff) => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            flag ? resolve(name) : reject(new Error('testPromise is error, name: ' + name));
        }, diff);
    });
};

var p1_suc_100 = createPromiseCase('p1-suc-100', true, 100);
var p2_suc_500 = createPromiseCase('p2-suc-500', true, 500);
var p3_suc_300 = createPromiseCase('p3-suc-300', true, 300);
var p4_fail_400 = createPromiseCase('p4-fail-400', false, 400);
var p5_fail_200 = createPromiseCase('p5-fail-200', false, 200);

Promise.first = promiseList => {
    return new Promise((resolve, reject) => {
        let num = 0;
        const len = promiseList.length;
        promiseList.forEach(promise => {
            Promise.resolve(promise)
                .then(resolve)
                .catch(() => {
                num++;
                if (num === len) {
                    reject('all promises not resolve');
                }
            });
        });
    });
}

Promise.first([p4_fail_400, p2_suc_500, p3_suc_300])
    .then(res => console.log(res)) // p3-suc-300
    .catch(e => console.error(e));
```

可以看到每次获取的p3_suc_300的值，因为p4是失败的状态，p2的完成状态没有p3快，因此这里获取到了p3的结果。

### Promise.last

与Promise.first对应的则是`Promise.last`，获取最后变成完成状态的值。这里与Promise.first不同的是，只有最后一个Promise都变成最终态（完成或拒绝），才能知道哪个是最后一个完成的，这里我采用了计数的方式，`then`和`catch`只能二选一，等计数器达到list.length时，执行外部的resolve。

```js
// get last resolve result
Promise.last = promiseList => {
    return new Promise((resolve, reject) => {
        let num = 0;
        const len = promiseList.length;
        let lastRes;
        
        const fn = () => {
            if (++num === len) {
                lastRes ? resolve(lastRes) : reject('all promises rejected');
            }
        }
        promiseList.forEach(promise => {
            Promise.resolve(promise)
                .then(res => {
                lastRes = res;
                fn();
            })
                .catch(fn);
        });
    });
}
```

调用方式：

```js
Promise.last([p1_suc_100, p2_suc_500, p5_fail_200, p3_suc_300, p4_fail_400])
    .then(res => console.log(res)) // p2-suc-500
    .catch(e => console.error(e));
```

p2需要500ms才能完成，是最晚完成的。

完整测试代码

```js
/**
 * 创建一个Promise对象的实例
 * @param name {string} 该实例的名称
 * @param flag {boolean} 返回的结果状态：完成还是拒绝
 * @param diff {number} 延迟的时间
 */
var createPromiseCase = (name, flag, diff) => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            flag ? resolve(name) : reject(new Error('testPromise is error, name: ' + name));
        }, diff);
    });
};

var p1_suc_100 = createPromiseCase('p1-suc-100', true, 100);
var p2_suc_500 = createPromiseCase('p2-suc-500', true, 500);
var p3_suc_300 = createPromiseCase('p3-suc-300', true, 300);
var p4_fail_400 = createPromiseCase('p4-fail-400', false, 400);
var p5_fail_200 = createPromiseCase('p5-fail-200', false, 200);

Promise.last = promiseList => {
    return new Promise((resolve, reject) => {
        let num = 0;
        const len = promiseList.length;
        let lastRes;
        
        const fn = () => {
            if (++num === len) {
                lastRes ? resolve(lastRes) : reject('all promises rejected');
            }
        }
        promiseList.forEach(promise => {
            Promise.resolve(promise)
                .then(res => {
                lastRes = res;
                fn();
            })
                .catch(fn);
        });
    });
}

Promise.last([p1_suc_100, p2_suc_500, p5_fail_200, p3_suc_300, p4_fail_400])
    .then(res => console.log(res)) // p2-suc-500
    .catch(e => console.error(e));
```

### Promise.none

`Promise.none`与Promise.all正好相反，所有的promise都被拒绝了，则Promise.none变成完成状态。该方法可以用Promise.first来切换，当执行Promise.first的catch时，则执行Promise.none中的resolve。不过这里我们使用Promise.all来实现。

```js
// if all the promises rejected, then success
Promise.none = promiseList => {
    return Promise.all(promiseList.map(promise => {
        return new Promise((resolve, reject) => {
            // 将promise的resolve和reject反过来
            return Promise.resolve(promise).then(reject, resolve);
        });
    }));
}
```

调用方式：

```js
Promise.none([p5_fail_200, p4_fail_400])
    .then(res => console.log(res))
    .catch(e => console.error(e));

// then的输出结果：
// [
//     Error: testPromise is error, name: p5-fail-200, 
//     Error: testPromise is error, name: p4-fail-400
// ]
```

两个promise都失败后，则Promise.none进入完成状态。

完整测试代码

```js
/**
 * 创建一个Promise对象的实例
 * @param name {string} 该实例的名称
 * @param flag {boolean} 返回的结果状态：完成还是拒绝
 * @param diff {number} 延迟的时间
 */
var createPromiseCase = (name, flag, diff) => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            flag ? resolve(name) : reject(new Error('testPromise is error, name: ' + name));
        }, diff);
    });
};

var p1_suc_100 = createPromiseCase('p1-suc-100', true, 100);
var p2_suc_500 = createPromiseCase('p2-suc-500', true, 500);
var p3_suc_300 = createPromiseCase('p3-suc-300', true, 300);
var p4_fail_400 = createPromiseCase('p4-fail-400', false, 400);
var p5_fail_200 = createPromiseCase('p5-fail-200', false, 200);

Promise.none = promiseList => {
    return Promise.all(promiseList.map(promise => {
        return new Promise((resolve, reject) => {
            // 将promise的resolve和reject反过来
            return Promise.resolve(promise).then(reject, resolve);
        });
    }));
}

Promise.none([p5_fail_200, p4_fail_400])
    .then(res => console.log(res))
    .catch(e => console.error(e));

// then的输出结果：
// [
//     Error: testPromise is error, name: p5-fail-200, 
//     Error: testPromise is error, name: p4-fail-400
// ]
```

### Promise.every

最后一个的实现比较简单，所有的promise都进入完成状态，则返回true，否则返回false。

```js
// get the boolean if all promises resolved
Promise.every = promiseList => {
	return Promise.all(promiseList)
    	.then(() => Promise.resolve(true))
    	.catch(() => Promise.resolve(false));
}
```

调用方式：

```js
Promise.every([p1_suc_100, p2_suc_500, p3_suc_300])
    .then(result => console.log('Promise.every', result)); // Promise.every true

Promise.every([p1_suc_100, p4_fail_400])
    .then(result => console.log('Promise.every', result)); // Promise.every false
```

完整测试代码

```js
/**
 * 创建一个Promise对象的实例
 * @param name {string} 该实例的名称
 * @param flag {boolean} 返回的结果状态：完成还是拒绝
 * @param diff {number} 延迟的时间
 */
var createPromiseCase = (name, flag, diff) => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            flag ? resolve(name) : reject(new Error('testPromise is error, name: ' + name));
        }, diff);
    });
};

var p1_suc_100 = createPromiseCase('p1-suc-100', true, 100);
var p2_suc_500 = createPromiseCase('p2-suc-500', true, 500);
var p3_suc_300 = createPromiseCase('p3-suc-300', true, 300);
var p4_fail_400 = createPromiseCase('p4-fail-400', false, 400);
var p5_fail_200 = createPromiseCase('p5-fail-200', false, 200);

Promise.every = promiseList => {
	return Promise.all(promiseList)
    	.then(() => Promise.resolve(true))
    	.catch(() => Promise.resolve(false));
}

Promise.every([p1_suc_100, p2_suc_500, p3_suc_300])
    .then(result => console.log('Promise.every', result)); // Promise.every true

Promise.every([p1_suc_100, p4_fail_400])
    .then(result => console.log('Promise.every', result)); // Promise.every false
```



## 69.异步加载脚本

```js
function LoadScript(url) {
    return new Promise((resolve, reject) => {
        const script = document.createElement('script');
        script.src = src;
        script.onload = resolve;
        script.onerror = reject;
        document.head.appendChild(script);
    })
}
```

