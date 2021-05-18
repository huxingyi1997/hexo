---
title:  JavaScript手写数组常用函数
date:  2021-04-03 22:40:00
categories: 
- web前端
tags:
- JavaScript
- 数组
- 函数手写
---
在代码开发过程中，我们常常使用数组的一些 原生`api`进行操作，大大方便了我们的操作。其中包含 `forEach`、`filter`、`find`、`findIndex`、`map`、`some`、`every`、`reduce`等函数方法，我们能不能自己写出这些方法。为了统一代码，我在前面增加了参数说明，看了说明之后代码可谓一目了然

<!-- more -->

------

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

```
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

```
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

```
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

```
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

```
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

```
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

```
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

```
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

```
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
