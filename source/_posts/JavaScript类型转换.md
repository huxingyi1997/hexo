---
title:  JavaScript类型转换
date:  2021-04-05 19:20:00
categories: 
- web前端
tags:
- JavaScript
- 数据类型
- 类型转换
---
在群里有人发了个搞笑图片，图片上包含这两个题目

```javascript
[] + {} /* [object Object] */
{} + [] /* 0/
```

好像记得为什么这么输出原因，自己推又忘了原因（第2个有点看不懂了），于是决定查找资料，复习一下JavaScript的类型转换

<!-- more -->

### 1.[] + {}和{} + []

1. number + number
2. string + string

所以，在JavaScript隐式转换规则中首先会推算两个操作数是不是number类型，如果是则直接运算得出结果。
如果有一个数为string，则将另一个操作数隐式的转换为string，然后通过字符串拼接得出结果。
如果为布尔值这种简单的数据类型，那么将会转换为number类型来进行运算得出结果。
如果操作数为对象或者是数组这种复杂的数据类型，那么就将两个操作数都转换为字符串，进行拼接 。

在题目中

[] + {} 中，[]会通过隐式转换规则，调用toString方法转换为 "" ，同理{}转换为[object Object], 相加得出字符串拼接结果 [object Object]

```javascript
var arr = new Array;
arr.toString() // ""
var obj = new Object;
obj.toString() // "[object Object]"
```

而 {} + []为什么结果会是0呢？
在js中{}代表复合语句，在一些js解释器会将开头的 {} 看作一个代码块，而不是一个Object（在es6以前只有函数作用域与全局作用域，还没有块级作用域）而这里的{}只是空符号，不表明任何意思。这里的+[]是一个隐式转换，所以参与运算的只有+[]，在这里将[]转换成了number类型，所以得出结果为0。

```javascript
{} + []; // [].toString() == "";  +"" == 0
```

### 2.类型转换的分类

按照转换方式分类，将值从一种类型转换成另一种类型，有两种情况，显示强制类型转换和隐式强制类型转换。

```javascript
var a = 66;
var b = a + ''; // 隐式强制类型转换
var c = String(a); // 显式强制类型转换
```

按照转换目标分，在 `JS` 中类型转换有三种情况，分别是：

- 转换为布尔值
- 转换为数字
- 转换为字符串

### 3.详细的类型转换结果

![](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418202828.png)

#### 3.1 ToString： 非字符串到字符串的强制类型转换。`String(value)`

基本类型值的字符串化规则：

|      值类型      |      转换规则       |
| :--------------: | :-----------------: |
|       null       |      `"null"`       |
|    undefined     |    `"undefined"`    |
|       true       |      `"true"`       |
|        []        |        `""`         |
|     普通对象     | `"[object Object]"` |
| 极小和极大的数字 |      指数形式       |

如果对象有自己的 toString() 方法，字符串化时就会调用该方法并使用其返回值；

```javascript
var a = [1, 2, 3];
a.toString(); // "1,2,3"
```

##### JSON 字符串化

对大多数简单的值都可以使用`JSON.stingify(...)`字符串化。
 JSON.stringify(..) 在对象中遇到 undefined、function 和 symbol 时会自动将其忽略，在数组中则会返回 null

```javascript
var a = {
    name: 'Tina',
    age: 20,
    sayHi: function() {
        alert('Hi');
    },
    height: null,
    weight: undefined,
    hobbies: [
        'swimming',
        'reading',
        'singing',
        null,
        undefined,
        function () {
            console.log('hobbies');
        }
    ]
}
JSON.stringify(a);
// "{"name": "Tina", "age": 20, "height": null, "hobbies": ["swimming", "reading", "singing", null, null, null]}"
```

如果要对含有非法 JSON 值的对象做字符串化，或者对象中的某些值无法被序列化时，就需要定义 toJSON() 方法来返回一个安全的 JSON 值。
 如果对象中定义了 toJSON() 方法，JSON 字符串化时会首先调用该方法，然后用它的返回值来进行序列化。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418202901.webp)

图中循环引用会产生错误

 toJSON() 是返回一个能够被字符串化的安全的 JSON 值。

##### `JSON.stringify(value[, replacer [, space]])`

replacer表示要字符化的属性
 space用于美化输出

```javascript
var a = {b: 1, c: 2, d: 3};
JSON.stringify(a, ['b', 'd']); //  "{"b":1,"d":3}"
```

#### 3.2 ToNumber： 非数字值到数字值 `Number(value)`

非数字值到数字值转化规则：

|   值类型    | 转换规则  |
| :---------: | :-------: |
|    null     |     0     |
|  undefined  | undefined |
|    true     |     1     |
| false, null |     0     |
|  转换出错   |    NaN    |

#### 3.3 ToBoolean: 转换为布尔类型 `Boolean(value)`

JavaScript 中的值可以分为以下两类：
 (1) 可以被强制类型转换为 false 的值
 (2) 其他（被强制类型转换为 true 的值）

##### 假值：

- undefined
- +0, -0, NaN
- false
- null
- ""

##### 假值对象

```javascript
var a = new Boolean(false);
var b = new Number(0);
var c = new String("");
```

虽然 JavaScript 代码中会出现假值对象，但它实际上并不属于 JavaScript 语
 言的范畴。

##### 真值

假值列表之外的值

### 4. 强制类型转换

#### 4.1 字符串和数字之间的显式强制类型转换

- 字符串与数字是通过 String(..) 和 Number(..) 这两个内建函数(比较常用)
- `.toString()`
- `+`（比较少用）

```javascript
var a = 42;
var b = a.toString();

var c = '66.6'
var d = +c
```

日期显式转换为数字

```javascript
var d = +new Date; // 可直接转换为时间戳， 不建议这么用，知道可以这么用就可以了
var timestamp = Date.now();  // 获取时间戳
```

~运算符, 字位操作“非”

```javascript
var a = 'hello world'
console.log(~a.indexOf('aaa')) // 0
console.log(~a.indexOf('he')) // -1
```

#### 4.2 字符串和数字之间的隐式强制类型转换

```javascript
var a = [1,2];
var b = [3,4];
a + b; // "1,23,4"
```

a和b都不是字符串，但是它们都被强制转换成字符串并拼接。
 类型转换都做了什么：

- 调用obj[Symbol.toPrimitive] (hint)（如果存在）
- 预期被转换成字符串类型，尝试toString()和valueOf()，否则退出。
- 如如果预期被转换成默认类型或者数字类型时，尝试valueOf()和toString()，否则退出。

也就是说，如果其中一个操作数是对象（包括数组），则首先对其调用ToPrimitive 抽象操作，该抽象操作再调用 [[DefaultValue]]，以数字作为上下文

#### 4.3 显式解析数字字符串

解析允许存在非数字的字符，从左到右，遇到非字符就停止
 转换不允许存在非数字字符， 否则返回NaN

```javascript
var a = "14";
Number(a); // 14
parseInt(a); // 14

var b = "14px";
Number(b); // NaN
parseInt(b); // 14
```

#### 4.4 显式转换为布尔值 `Boolean(value)`

常用方法是 `!!`, 为第二个`!`会将结果反转回原值

```javascript
var a = "0";
var b = [];
var c = {};
var d = "";
var e = 0;
var f = null;
var g;

!!a; // true
!!b; // true
!!c; // true
!!d; // false
!!e; // false
!!f; // false
!!g; // false
```

#### 4.5 布尔值到数字到隐式转换

```javascript
1 + false; // 1
1 + true; // 2
```

#### 4.6 || 和 &&

&& 和 || 运算符的返回值并不一定是布尔类型，而是两个操作数其中一个的值

### 5. `==` 与 `===`的不同

`==` 允许在相等比较中进行强制类型转换，而 `===` 不允许

== 判断的执行时间会比 === 的执行时间多

1. 对于string,number等基础类型，== 和 === 是有区别的
    1）不同类型间比较，== 只比较“转化成同一类型后的值”看“值”是否相等，=== 如果类型不同，其结果就是不等
    2）同类型比较，直接进行“值”比较，两者结果一样
2. 对于Array,Object等高级类型，== 和 === 是没有区别的
    进行“指针地址”比较
3. 基础类型与高级类型，== 和 === 是有区别的
    1）对于==，将高级转化为基础类型，进行“值”比较
    2）因为类型不同，=== 结果为false
    === 的比较结果比 == 精准得多