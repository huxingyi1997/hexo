---
title:  ES7-ES11新功能
date:  2021-03-29 13:37:27
categories: 
- web前端
tags:
- javascript
- ES7
- ES8
- ES9
- ES10
- ES11
---
关于ES5和ES6的书也好、博客也好，都有很多了，在最近读了ES12的新功能并总结之后，何不来归纳总结一下ES7-ES11的呢，其中有一些已经成为我们日常使用的常用方案了，比如ES8的async/await方法，即使不常用，总结一下也方便查询，不多说了，赶紧进入正题，标注重点关注的，我会牢牢记住，并使用

<!-- more -->

## 1.ES7（ES2016）

ES2016添加了两个小的特性来说明标准化过程：

- 数组includes()方法，用来判断一个数组是否包含一个指定的值，根据情况，如果包含则返回true，否则返回false。
- a ** b指数运算符，它与 Math.pow(a, b)相同。

### 1.1 Array.prototype.includes() 重点关注

`includes()` 函数用来判断一个数组是否包含一个指定的值，如果包含则返回 `true`，否则返回`false`。

`includes` 函数与 `indexOf` 函数很相似，下面两个表达式是等价的：

```
arr.includes(x);
arr.indexOf(x) >= 0;
```

接下来我们来判断数字中是否包含某个元素：

> 在ES7之前的做法

使用`indexOf()`验证数组中是否存在某个元素，这时需要根据返回值是否为-1来判断：

```
let arr = ['react', 'angular', 'vue'];

if (arr.indexOf('react') !== -1) {
    console.log('react存在');
}
```

> 使用ES7的includes()

使用includes()验证数组中是否存在某个元素，这样更加直观简单：

```
let arr = ['react', 'angular', 'vue'];

if (arr.includes('react')) {
    console.log('react存在');
}
```

### 1.2 指数操作符 重点关注

还记得初学编程就记得Qbasic的7种运算操作符+-*/（加减乘除） \（整除）mod（区域）^（乘方）

但ES7以前的JS种，需要使用Math.pow()进行计算。在ES7中引入了指数运算符`**`，`**`具有与`Math.pow(..)`等效的计算结果。

> 使用指数操作符

使用指数运算符**，就像+、-等操作符一样：

```
console.log(2**10); // 输出1024
```

## 2. ES8（ES2017）

### 2.1 async/await 重点关注

ES2018引入异步迭代器（asynchronous iterators），这就是迭代器的语法糖，除了`next()`方法返回一个Promise。因此`await`可以和`for...of`循环一起使用，以串行的方式运行异步操作。例如：

```
async function process(array) {
    for await (let i of array) {
    	doSomething(i);
    }
}
```

### 2.2 Object.values() 重点关注

`Object.values()`是一个与`Object.keys()`类似的新函数，但返回的是Object自身属性的所有值，不包括继承的值。

假设我们要遍历如下对象`obj`的所有值：

```
const obj = {a: 1, b: 2, c: 3};
```

> 不使用Object.values() :ES7

```
const vals = Object.keys(obj).map(key => obj[key]);
console.log(vals); // [1, 2, 3]
```

> 使用Object.values() :ES8

```
const values = Object.values(obj1);
console.log(values); // [1, 2, 3]
```

从上述代码中可以看出`Object.values()`为我们省去了遍历key，并根据这些key获取value的步骤。

### 2.3 Object.entries() 重点关注

`Object.entries()`函数返回一个给定对象自身可枚举属性的键值对的数组。

接下来我们来遍历上文中的`obj`对象的所有属性的key和value：

> 不使用Object.entries() :ES7

```
Object.keys(obj).forEach(key => {
	console.log('key:' + key + ' value:' + obj[key]);
})
// key:a value:1
// key:b value:2
// key:c value:3
```

> 使用Object.entries() :ES8

```
for(let [key,value] of Object.entries(obj)){
	console.log(`key: ${key} value:${value}`);
}
// key:a value:1
// key:b value:2
// key:c value:3
```

### 2.4 String padding

在ES8中String新增了两个实例函数`String.prototype.padStart`和`String.prototype.padEnd`，允许将空字符串或其他字符串添加到原始字符串的开头或结尾。

> String.padStart(targetLength,[padString])

- targetLength:当前字符串需要填充到的目标长度。如果这个数值小于当前字符串的长度，则返回当前字符串本身。
- padString:(可选)填充字符串。如果字符串太长，使填充后的字符串长度超过了目标长度，则只保留最左侧的部分，其他部分会被截断，此参数的缺省值为 " "。

```
console.log('0.0'.padStart(4,'10')) //10.0
console.log('0.0'.padStart(20)) // 0.00    
```

> String.padEnd(targetLength,padString])

- targetLength:当前字符串需要填充到的目标长度。如果这个数值小于当前字符串的长度，则返回当前字符串本身。
- padString:(可选) 填充字符串。如果字符串太长，使填充后的字符串长度超过了目标长度，则只保留最左侧的部分，其他部分会被截断，此参数的缺省值为 " "；

```
console.log('0.0'.padEnd(4,'0')) // 0.00    
console.log('0.0'.padEnd(10,'0')) // 0.00000000
```

### 2.5 函数参数列表结尾允许逗号

主要作用是方便使用git进行多人协作开发时修改同一个函数减少不必要的行变更。

### 2.6 Object.getOwnPropertyDescriptors()

`Object.getOwnPropertyDescriptors()`函数用来获取一个对象的所有自身属性的描述符,如果没有任何自身属性，则返回空对象。

> 函数原型：

```
Object.getOwnPropertyDescriptors(obj)
```

返回`obj`对象的所有自身属性的描述符，如果没有任何自身属性，则返回空对象。

```
const obj2 = {
	name: 'Jine',
	get age() {
		return '18';
 	}
};
Object.getOwnPropertyDescriptors(obj2)
// {
//   age: {
//     configurable: true,
//     enumerable: true,
//     get: function age(){}, //the getter function
//     set: undefined
//   },
//   name: {
//     configurable: true,
//     enumerable: true,
//		value:"Jine",
//		writable:true
//   }
// }
```

### 2.7 SharedArrayBuffer对象

SharedArrayBuffer 对象用来表示一个通用的，固定长度的原始二进制数据缓冲区，类似于 ArrayBuffer 对象，它们都可以用来在共享内存（shared memory）上创建视图。与 ArrayBuffer 不同的是，SharedArrayBuffer 不能被分离。

```
/**
 * 
 * @param {*} length 所创建的数组缓冲区的大小，以字节(byte)为单位。  
 * @returns {SharedArrayBuffer} 一个大小指定的新 SharedArrayBuffer 对象。其内容被初始化为 0。
 */
new SharedArrayBuffer(length);
```

### 2.8 Atomics对象

Atomics 对象提供了一组静态方法用来对 SharedArrayBuffer 对象进行原子操作。

这些原子操作属于 Atomics 模块。与一般的全局对象不同，Atomics 不是构造函数，因此不能使用 new 操作符调用，也不能将其当作函数直接调用。Atomics 的所有属性和方法都是静态的（与 Math  对象一样）。

多个共享内存的线程能够同时读写同一位置上的数据。原子操作会确保正在读或写的数据的值是符合预期的，即下一个原子操作一定会在上一个原子操作结束后才会开始，其操作过程不会中断。

- Atomics.add()

> 将指定位置上的数组元素与给定的值相加，并返回相加前该元素的值。

- Atomics.and()

> 将指定位置上的数组元素与给定的值相与，并返回与操作前该元素的值。

- Atomics.compareExchange()

> 如果数组中指定的元素与给定的值相等，则将其更新为新的值，并返回该元素原先的值。

- Atomics.exchange()

> 将数组中指定的元素更新为给定的值，并返回该元素更新前的值。

- Atomics.load()

> 返回数组中指定元素的值。

- Atomics.or()

> 将指定位置上的数组元素与给定的值相或，并返回或操作前该元素的值。

- Atomics.store()

> 将数组中指定的元素设置为给定的值，并返回该值。

- Atomics.sub()

> 将指定位置上的数组元素与给定的值相减，并返回相减前该元素的值。

- Atomics.xor()

> 将指定位置上的数组元素与给定的值相异或，并返回异或操作前该元素的值。

wait() 和 wake() 方法采用的是 Linux 上的 futexes 模型（fast user-space mutex，快速用户空间互斥量），可以让进程一直等待直到某个特定的条件为真，主要用于实现阻塞。

- Atomics.wait()

> 检测数组中某个指定位置上的值是否仍然是给定值，是则保持挂起直到被唤醒或超时。返回值为 "ok"、"not-equal" 或 "time-out"。调用时，如果当前线程不允许阻塞，则会抛出异常（大多数浏览器都不允许在主线程中调用 wait()）。

- Atomics.wake()

> 唤醒等待队列中正在数组指定位置的元素上等待的线程。返回值为成功唤醒的线程数量。

- Atomics.isLockFree(size)

> 可以用来检测当前系统是否支持硬件级的原子操作。对于指定大小的数组，如果当前系统支持硬件级的原子操作，则返回 true；否则就意味着对于该数组，Atomics 对象中的各原子操作都只能用锁来实现。此函数面向的是技术专家。-->

## 3. ES9（ES2018）

### 3.1 异步迭代

在`async/await`的某些时刻，你可能尝试在同步循环中调用异步函数。例如：

```
async function process(array) {
    for (let i of array) {
    	await doSomething(i);
    }
}
```

这段代码不会正常运行，下面这段同样也不会：

```
async function process(array) {
    array.forEach(async i => {
    	await doSomething(i);
    });
}
```

这段代码中，循环本身依旧保持同步，并在在内部异步函数之前全部调用完成。

ES2018引入异步迭代器（asynchronous iterators），这就像常规迭代器，除了`next()`方法返回一个Promise。因此`await`可以和`for...of`循环一起使用，以串行的方式运行异步操作。例如：

```
async function process(array) {
    for await (let i of array) {
    	doSomething(i);
    }
}
```

### 3.2 Promise.finally()  重点关注

一个Promise调用链要么成功到达最后一个`.then()`，要么失败触发`.catch()`。在某些情况下，你想要在无论Promise运行成功还是失败，运行相同的代码，例如清除，删除对话，关闭数据库连接等。

`.finally()`允许你指定最终的逻辑：

```
function doSomething() {
    doSomething1()
    .then(doSomething2)
    .then(doSomething3)
    .catch(err => {
    	console.log(err);
    })
    .finally(() => {
    	// finish here!
    });
}
```

### 3.3 Rest/Spread 属性 重点关注

ES6(ES2015)引入了[Rest参数](https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FFunctions%2FRest_parameters)和[扩展运算符](https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FOperators%2FSpread_syntax)。三个点（...）仅用于数组。Rest参数语法允许我们将一个不定数量的参数表示为一个数组。

```
restParam(1, 2, 3, 4, 5);

function restParam(p1, p2, ...p3) {
    // p1 = 1
    // p2 = 2
    // p3 = [3, 4, 5]
}
```

展开操作符以相反的方式工作，将数组转换成可传递给函数的单独参数。例如`Math.max()`返回给定数字中的最大值：

```
const values = [99, 100, -1, 48, 16];
console.log( Math.max(...values) ); // 100
```

ES2018为对象解构提供了和数组一样的Rest参数（）和展开操作符，一个简单的例子：

```
const myObject = {
    a: 1,
    b: 2,
    c: 3
};

const { a, ...x } = myObject;
// a = 1
// x = { b: 2, c: 3 }
```

或者你可以使用它给函数传递参数：

```
restParam({
    a: 1,
    b: 2,
    c: 3
});

function restParam({ a, ...x }) {
	console.log(a, x);
    // a = 1
    // x = { b: 2, c: 3 }
}
```

跟数组一样，Rest参数只能在声明的结尾处使用。此外，它只适用于每个对象的顶层，如果对象中嵌套对象则无法适用。

扩展运算符可以在其他对象内使用，例如：

```
const obj1 = { a: 1, b: 2, c: 3 };
const obj2 = { ...obj1, z: 26 };
console.log(obj2);
// obj2 is { a: 1, b: 2, c: 3, z: 26 }
```

可以使用扩展运算符拷贝一个对象，像是这样`obj2 = {...obj1}`，但是 **这只是一个对象的浅拷贝**。另外，如果一个对象A的属性是对象B，那么在克隆后的对象cloneB中，该属性指向对象B。

### 3.4 正则表达式命名捕获组

JavaScript正则表达式可以返回一个匹配的对象——一个包含匹配字符串的类数组，例如：以`YYYY-MM-DD`的格式解析日期：

```
const
    reDate = /([0-9]{4})-([0-9]{2})-([0-9]{2})/,
    match  = reDate.exec('2018-04-30'),
    year   = match[1], // 2018
    month  = match[2], // 04
    day    = match[3]; // 30
```

这样的代码很难读懂，并且改变正则表达式的结构有可能改变匹配对象的索引。

ES2018允许命名捕获组使用符号`?<name>`，在打开捕获括号`(`后立即命名，示例如下：

```
const
    reDate = /(?<year>[0-9]{4})-(?<month>[0-9]{2})-(?<day>[0-9]{2})/,
    match  = reDate.exec('2018-04-30'),
    year   = match.groups.year,  // 2018
    month  = match.groups.month, // 04
    day    = match.groups.day;   // 30
```

任何匹配失败的命名组都将返回`undefined`。

命名捕获也可以使用在`replace()`方法中。例如将日期转换为美国的 MM-DD-YYYY 格式：

```
const
    reDate = /(?<year>[0-9]{4})-(?<month>[0-9]{2})-(?<day>[0-9]{2})/,
    d = '2018-04-30',
    usDate = d.replace(reDate, '$<month>-$<day>-$<year>');
```

### 3.5 正则表达式反向断言

目前JavaScript在正则表达式中支持先行断言（lookahead）。这意味着匹配会发生，但不会有任何捕获，并且断言没有包含在整个匹配字段中。例如从价格中捕获货币符号：

```
const
    reLookahead = /\D(?=\d+)/,
    match       = reLookahead.exec('$123.89');

console.log( match[0] ); // $
```

ES2018引入以相同方式工作但是匹配前面的反向断言（lookbehind），这样就可以忽略货币符号，单纯的捕获价格的数字：

```
const
    reLookbehind = /(?<=\D)\d+/,
    match        = reLookbehind.exec('$123.89');

console.log( match[0] ); // 123.89
```

以上是 **肯定反向断言**，非数字`\D`必须存在。同样的，还存在 **否定反向断言**，表示一个值必须不存在，例如：

```
const
  reLookbehindNeg = /(?<!\D)\d+/,
  match           = reLookbehind.exec('$123.89');

console.log( match[0] ); // null
```

### 3.6 正则表达式dotAll模式

正则表达式中点`.`匹配除回车外的任何单字符，标记`s`改变这种行为，允许行终止符的出现，例如：

```
/hello.world/.test('hello\nworld');  // false
/hello.world/s.test('hello\nworld'); // true
```

### 3.7 正则表达式 Unicode 转义

到目前为止，在正则表达式中本地访问 Unicode 字符属性是不被允许的。ES2018添加了 Unicode 属性转义——形式为`\p{...}`和`\P{...}`，在正则表达式中使用标记 `u` (unicode) 设置，在`\p`块儿内，可以以键值对的方式设置需要匹配的属性而非具体内容。例如：

```
const reGreekSymbol = /\p{Script=Greek}/u;
reGreekSymbol.test('π'); // true
```

此特性可以避免使用特定 Unicode 区间来进行内容类型判断，提升可读性和可维护性。

### 3.8 非转义序列的模板字符串

之前，`\u`开始一个 unicode 转义，`\x`开始一个十六进制转义，`\`后跟一个数字开始一个八进制转义。这使得创建特定的字符串变得不可能，例如Windows文件路径 `C:\uuu\xxx\111`。更多细节参考[模板字符串](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/template_strings)。

## 4.ES10（ES2019）

### 4.1 行分隔符（U + 2028）和段分隔符（U + 2029）符号

行分隔符（U + 2028）和段分隔符（U + 2029）符号现在允许在字符串文字中，与JSON匹配，这些符号在字符串文字中被视为行终止符，因此使用它们会导致SyntaxError异常。

### 4.2 更加友好的 JSON.stringify

如果输入 Unicode 格式但是超出范围的字符，在原先JSON.stringify返回格式错误的Unicode字符串。现在实现了一个改变JSON.stringify的[第3阶段提案](https://github.com/tc39/proposal-well-formed-stringify)，因此它为其输出转义序列，使其成为有效Unicode（并以UTF-8表示）

### 4.3 新增了Array的`flat()`方法和`flatMap()`方法 重点关注

`flat()`和`flatMap()`本质上就是是归纳（reduce） 与 合并（concat）的操作。

#### Array.prototype.flat()

`flat()` 方法会按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。

- `flat()`方法最基本的作用就是数组降维

```
var arr1 = [1, 2, [3, 4]];
arr1.flat(); 
// [1, 2, 3, 4]

var arr2 = [1, 2, [3, 4, [5, 6]]];
arr2.flat();
// [1, 2, 3, 4, [5, 6]]

var arr3 = [1, 2, [3, 4, [5, 6]]];
arr3.flat(2);
// [1, 2, 3, 4, 5, 6]

//使用 Infinity 作为深度，展开任意深度的嵌套数组
arr3.flat(Infinity); 
// [1, 2, 3, 4, 5, 6]
```

- 其次，还可以利用`flat()`方法的特性来去除数组的空项

```
var arr4 = [1, 2, , 4, 5];
arr4.flat();
// [1, 2, 4, 5]
```

#### Array.prototype.flatMap()

`flatMap()` 方法首先使用映射函数映射每个元素，然后将结果压缩成一个新数组。它与 map 和 深度值1的 flat 几乎相同，但 flatMap 通常在合并成一种方法的效率稍微高一些。 这里我们拿map方法与flatMap方法做一个比较。

```
var arr1 = [1, 2, 3, 4];

arr1.map(x => [x * 2]); 
// [[2], [4], [6], [8]]

arr1.flatMap(x => [x * 2]);
// [2, 4, 6, 8]

// 只会将 flatMap 中的函数返回的数组 “压平” 一层
arr1.flatMap(x => [[x * 2]]);
// [[2], [4], [6], [8]]
```

### 4.4 新增了String的trimStart()方法和trimEnd()方法

新增的这两个方法很好理解，分别去除字符串首尾空白字符，这里就不用例子说声明了。

### 4.5 Object.fromEntries()

`Object.entries()`方法的作用是返回一个给定对象自身可枚举属性的键值对数组，其排列与使用 for...in 循环遍历该对象时返回的顺序一致（区别在于 for-in 循环也枚举原型链中的属性）。

**而`Object.fromEntries()` 则是 `Object.entries()` 的反转。**

`Object.fromEntries()` 函数传入一个键值对的列表，并返回一个带有这些键值对的新对象。这个迭代参数应该是一个能够实现@iterator方法的的对象，返回一个迭代器对象。它生成一个具有两个元素的类似数组的对象，第一个元素是将用作属性键的值，第二个元素是与该属性键关联的值。

- 通过 Object.fromEntries， 可以将 Map 转化为 Object:

```
const map = new Map([ ['foo', 'bar'], ['baz', 42] ]);
const obj = Object.fromEntries(map);
console.log(obj); // { foo: "bar", baz: 42 }
```

- 通过 Object.fromEntries， 可以将 Array 转化为 Object:

```
const arr = [ ['0', 'a'], ['1', 'b'], ['2', 'c'] ];
const obj = Object.fromEntries(arr);
console.log(obj); // { 0: "a", 1: "b", 2: "c" }
```

### 4.6 Symbol.prototype.description

通过工厂函数Symbol（）创建符号时，您可以选择通过参数提供字符串作为描述：

```
const sym = Symbol('The description');
```

以前，访问描述的唯一方法是将符号转换为字符串：

```
assert.equal(String(sym), 'Symbol(The description)');
```

现在引入了getter Symbol.prototype.description以直接访问描述：

```
assert.equal(sym.description, 'The description');
```

### 4.7 String.prototype.matchAll

`matchAll()` 方法返回一个包含所有匹配正则表达式及分组捕获结果的迭代器。 在 matchAll 出现之前，通过在循环中调用regexp.exec来获取所有匹配项信息（regexp需使用/g标志：

```
const regexp = RegExp('foo*','g');
const str = 'table football, foosball';

while ((matches = regexp.exec(str)) !== null) {
    console.log(`Found ${matches[0]}. Next starts at ${regexp.lastIndex}.`);
    // expected output: "Found foo. Next starts at 9."
    // expected output: "Found foo. Next starts at 19."
}
```

如果使用matchAll ，就可以不必使用while循环加exec方式（且正则表达式需使用／g标志）。使用matchAll 会得到一个迭代器的返回值，配合 for...of, array spread, or Array.from() 可以更方便实现功能：

```
const regexp = RegExp('foo*','g'); 
const str = 'table football, foosball';
let matches = str.matchAll(regexp);

for (const match of matches) {
	console.log(match);
}
// Array [ "foo" ]
// Array [ "foo" ]

// matches iterator is exhausted after the for..of iteration
// Call matchAll again to create a new iterator
matches = str.matchAll(regexp);

Array.from(matches, m => m[0]);
// Array [ "foo", "foo" ]
```

#### matchAll可以更好的用于分组

```
var regexp = /t(e)(st(\d?))/g;
var str = 'test1test2';

str.match(regexp); 
// Array ['test1', 'test2']

let array = [...str.matchAll(regexp)];

array[0];
// ['test1', 'e', 'st1', '1', index: 0, input: 'test1test2', length: 4]
array[1];
// ['test2', 'e', 'st2', '2', index: 5, input: 'test1test2', length: 4]
```

### 4.8 `Function.prototype.toString()`现在返回精确字符，包括空格和注释 重点关注

```
function /* comment */ foo /* another comment */() {}

// 之前不会打印注释部分
console.log(foo.toString()); // function foo(){}

// ES2019 会把注释一同打印
console.log(foo.toString()); // function /* comment */ foo /* another comment */ (){}

// 箭头函数
const bar /* comment */ = /* another comment */ () => {};

console.log(bar.toString()); // () => {}
```

### 4.9 修改 `catch` 绑定 重点关注

在 ES10 之前，我们必须通过语法为 catch 子句绑定异常变量，无论是否有必要。很多时候 catch 块是多余的。 ES10 提案使我们能够简单的把变量省略掉。

不算大的改动。

之前是

```
try {} catch(e) {}
```

现在是

```
try {} catch {}
```

### 4.10 新的基本数据类型`BigInt` 重点关注

现在的基本数据类型（值类型）不止5种（ES6之后是六种）了哦！加上BigInt一共有七种基本数据类型，分别是： String、Number、Boolean、Null、Undefined、Symbol、BigInt，加上Object一共八种数据类型

## 5.ES11（ES2020）

### 5.1 可选链式调用(Optional Chaining)

大部分开发者都遇到过这个问题：

```
TypeError: Cannot read property ‘x’ of undefined
```

这个错误表示我们正在访问一个不属于对象的属性。

#### 5.1.1 访问对象的属性

```
const flower = {
    colors: {
        red: true
    }
}

console.log(flower.colors.red) // 正常运行

console.log(flower.species.lily) // 抛出错误：TypeError: Cannot read property 'lily' of undefined
```

在这种情况下，JavaScript 引擎会像这样抛出错误。但是某些情况下值是否存在并不重要，因为我们知道它会存在。于是，可选链式调用就派上用场了！

我们可以使用由一个问号和一个点组成的可选链式操作符，去表示不应该引发错误。如果没有值，应该返回 **undefined**。

```
console.log(flower.species?.lily) // 输出 undefined
```

当访问数组或调用函数时，也可以使用可选链式调用。

#### 5.1.2 访问数组

```
let flowers =  ['lily', 'daisy', 'rose']

console.log(flowers[1]) // 输出：daisy

flowers = null

console.log(flowers[1]) // 抛出错误：TypeError: Cannot read property '1' of null
console.log(flowers?.[1]) // 输出：undefined
```

#### 5.1.3 调用函数

```
let plantFlowers = () => {
  return 'orchids'
}

console.log(plantFlowers()) // 输出：orchids

plantFlowers = null

console.log(plantFlowers()) // 抛出错误：TypeError: plantFlowers is not a function

console.log(plantFlowers?.()) // 输出：undefined
```

### 5.2 空值合并（Nullish Coalescing） 

当我们查询某个属性时，经常会遇到，如果没有该属性就会设置一个默认的值。比如下面代码中查询玩家等级。

```
var level =  user.data.level || '暂无等级';
```

在JS中，空字符串、0 ，当进行逻辑操作符判时，会自动转化为 false。在上面的代码里，如果玩家等级本身就是 0 级, 变量 level 就会被赋值 `暂无等级` 字符串，这是逻辑错误，有时候业务上，我们只需容错取值查询到`undefined` 或者 `null` ，比如：

```
// {
//    "level": null
// }
var level = user.level !== undefined && user.level !== null
    ? user.level
    : '暂无等级';
```

来看看用空值合并运算符如何处理

```
// {
//   "level": 0   
// }
var level = user.level ?? '暂无等级'; // level -> 0


// {
//   "an_other_field": 0   
// }
var level = user.level ?? '暂无等级'; // level -> '暂无等级'
```

，用空值合并运算在逻辑正确的前提下，代码更加简洁。

空值合并运算符 与 可选链 相结合，可以很轻松处理多级查询并赋予默认值问题。

```
var level = user.data?.level ?? '暂无等级';
```

### 5.3 私有字段(Private Fields) 

许多具有 **classes** 的编程语言允许定义类作为公共的，受保护的或私有的属性。**Public** 属性可以从类的外部或者子类访问，**protected** 属性只能被子类访问，**private** 属性只能被类内部访问。JavaScript 从 **ES6** 开始支持类语法，但直到现在才引入了私有字段。要定义私有属性，必须在其前面加上散列符号：**`#`**。

```
class Flower {
  #leaf_color = "green";
  constructor(name) {
    this.name = name;
  }

  get_color() {
    return this.#leaf_color;
  }
}

const orchid = new Flower("orchid");

console.log(orchid.get_color()); // 输出：green
console.log(orchid.#leaf_color) // 报错：SyntaxError: Private field '#leaf_color' must be declared in an enclosing class
```

如果我们从外部访问类的私有属性，势必会报错。

### 5.4 静态字段(Static Fields) 

如果想使用类的方法，首先必须实例化一个类，如下所示：

```
class Flower {
    add_leaves() {
    	console.log("Adding leaves");
    }
}

const rose = new Flower();
rose.add_leaves();

Flower.add_leaves() // 抛出错误：TypeError: Flower.add_leaves is not a function
```

试图去访问没有实例化的 **Flower** 类的方法将会抛出一个错误。但由于 **static** 字段，类方法可以被 **static** 关键词声明然后从外部调用。

```
class Flower {
    constructor(type) {
    	this.type = type;
    }
    static create_flower(type) {
    	return new Flower(type);
    }
}

const rose = Flower.create_flower("rose"); // 正常运行
```

### 5.5 顶级 Await(Top Level Await)

目前，如果用 **await** 获取 promise 函数的结果，那使用 **await** 的函数必须用 **async** 关键字定义。

```
const func = async () => {
    const response = await fetch(url)
}
```

头疼的是，在全局作用域中去等待某些结果基本上是不可能的。除非使用 **立即调用的函数表达式（IIFE）**。

```
(async () => {
    const response = await fetch(url)
})()
```

但引入了 **顶级 Await** 后，不需要再把代码包裹在一个 async 函数中了，如下即可：

```
const response = await fetch(url)
```

这个特性对于解决模块依赖或当初始源无法使用而需要备用源的时候是非常有用的。

```
let Vue
try {
    Vue = await import('url_1_to_vue')
} catch {
    Vue = await import('url_2_to_vue)
}
```

### 5.6 Promise.allSettled 方法 重点关注

我们都知道 `Promise.all` 具有并发执行异步任务的能力。但它的最大问题就是如果其中某个任务出现异常(reject)，所有任务都会挂掉，Promise直接进入 `reject`  状态。

想象这个场景：你的页面有三个区域，分别对应三个独立的接口数据，使用 `Promise.all` 来并发三个接口，如果其中任意一个接口服务异常，状态是reject,这会导致页面中该三个区域数据全都无法渲染出来，因为任何 `reject` 都会进入catch回调, 很明显，这是无法接受的，如下：

```
Promise.all([
    Promise.reject({code: 500, msg: '服务异常'}),
    Promise.resolve({ code: 200, list: []}),
    Promise.resolve({code: 200, list: []})
])
.then((ret) => {
    // 如果其中一个任务是 reject，则不会执行到这个回调。
    RenderContent(ret);
})
.catch((error) => {
    // 本例中会执行到这个回调
    // error: {code: 500, msg: "服务异常"}
})
```

我们需要一种机制，如果并发任务中，无论一个任务正常或者异常，都会返回对应的的状态（fulfilled 或者 rejected）与结果（业务value 或者 拒因 reason），在 then 里面通过 filter 来过滤出想要的业务逻辑结果，这就能最大限度的保障业务当前状态的可访问性，而 `Promise.allSettled` 就是解决这问题的。

```
Promise.allSettled([
    Promise.reject({code: 500, msg: '服务异常'}),
    Promise.resolve({ code: 200, list: []}),
    Promise.resolve({code: 200, list: []})
])
.then((ret) => {
    /*
        0: {status: "rejected", reason: {…}}
        1: {status: "fulfilled", value: {…}}
        2: {status: "fulfilled", value: {…}}
    */
    // 过滤掉 rejected 状态，尽可能多的保证页面区域数据渲染
    RenderContent(ret.filter((el) => {
        return el.status !== 'rejected';
    }));
});
```

### 5.7 按需导入 (Dynamic Import)

按需 import 提案几年前就已提出，如今终于能进入ES正式规范。这里个人理解成“按需”更为贴切。现代前端打包资源越来越大，打包成几M的JS资源已成常态，而往往前端应用初始化时根本不需要全量加载逻辑资源，为了首屏渲染速度更快，很多时候都是按需加载，比如懒加载图片等。而这些按需执行逻辑资源都体现在某一个事件回调中去加载。

```
// Alert.js
export default {
    show() {
        // 代码
    }
}


// 使用 Alert.js 的文件
import('/components/Alert.js')
    .then(Alert => {
        Alert.show()
    })
```

考虑到许多应用程序使用诸如 webpack 之类的模块打包器来进行代码的转译和优化，这个特性现在还没什么大作用。

### 5.8  全局对象globalThis

Javascript 在不同的环境获取全局对象有不通的方式，node 中通过 global, web中通过 window, self 等，有些甚至通过 this 获取，但通过 this 是及其危险的，this 在 js 中异常复杂，它严重依赖当前的执行上下文，这些无疑增加了获取全局对象的复杂性。

过去获取全局对象，可通过一个全局函数

```
var getGlobal = function () { 
    if (typeof self !== 'undefined') { return self; } 
    if (typeof window !== 'undefined') { return window; } 
    if (typeof global !== 'undefined') { return global; } 
    throw new Error('unable to locate global object'); 
}; 

var globals = getGlobal(); 

// https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/globalThis
```

而 globalThis 目的就是提供一种标准化方式访问全局对象，有了 globalThis 后，你可以在任意上下文，任意时刻都能获取到全局对象。