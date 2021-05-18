---
title:  深究 JavaScript 数组 —— 演进&性能
date:  2021-03-22 12:30:27
categories: 
- web前端
tags:
- javascript
- 数组
---
用了这么久的js，多次使用js的数组，突然好奇，js数组到底是怎样的机制，很明显它与C语言的数组有很多不同，C语言数组就是内存中一块连续的区域，那么JS呢？感谢原作者：[Paul Shan](http://link.zhihu.com/?target=http%3A//voidcanvas.com/author/paulshan/) 原文：[Diving deep into JavaScript array - evolution & performance](http://link.zhihu.com/?target=http%3A//voidcanvas.com/javascript-array-evolution-performance/) 以及辛苦的翻译人员

<!-- more -->

## 深究 JavaScript 数组 —— 演进&性能

写文章前我要说一下，这篇文章不是讲 JavaScript 数组基础的，也不会教相关的语法和用法。文章更多的是讲数组在内存中的存储方式、优化、不同语法导致的行为差异、性能和最近的改进。

我接触 JavaScript 的时候，已经对 C/C++/C# 等语言相当熟悉了。但和许多 C/C++使用者一样，第一次与 JavaScipt 的「约会」并不愉快。

我不喜欢 JavaScipt 的一个主要原因是它的Array。 JavaScript 的数组通过哈希映射或者字典的方式来实现，所以不是连续的。我觉得这是一门劣等语言：连数组都不能正确的实现。但时至今日， JavaScript 以及我对 JavaScript 的理解都有相当大的变化。

## **为什么 JavaScript 数组不是真正的数组**

在讲 JavaScript 相关的东西前，让我先告诉你什么是 Array。

数组( Array )在内存中用一串连续的区域来存放一些值。注意「连续」一词，它至关重要。

![img](https://user-gold-cdn.xitu.io/2017/9/5/c68a7cf972e76a0c7eabc638064e9139?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

上图表示存储在内存中的一个数组。存储4个4 bit 的元素,一共需要16 bit 存储区域，每个元素顺序保持一致。

假设，我声明了tinyInt arr[4]，它占用从1201位置开始的一溜存储区域。当某个时刻我尝试读取a[2]，那么只要做简单的计算，找到a[2]的位置就可以了。例如1201+(2X4)然后直接从1209位置读取数据。

![img](https://user-gold-cdn.xitu.io/2017/9/5/195788ff466cc6fcb7c9f481baa39508?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

在 JavaScript 中，数组是哈希映射。它可以通过多种数据结构实现，其中一种是链表。所以，如果在 JavaScript 中声明var arr = new Array(4)；它会产生类似上图的结构。因此，如果你想在程序中某一处读取a[2]，它必须从1201位置开始溯寻a[2]的位置。

这就是 JavaScript 数组和真正的数组不同的地方。显然数学计算要比链表遍历花的时间少。遇到长点的数组，日子就不好过了啊。

## **JavaScript 数组演进**

还记得以前如果某个朋友的电脑有 256MB RAM我们多嫉妒吗？但现在，8GB RAM 已经稀松平常了。

跟它一样，JavaScript 这门语言也进化了许多。由于V8 、SpiderMonkey、TC39以及日益增多的 web 用户的努力，世界已经离不开 JavaScript 了。拥有如此巨大的用户群体，性能提升也势在必行。

近些日子， JavaScript 引擎已经在为同种数据类型的数组分配连续的存储空间了。优秀的开发者总是保持数组的数据类型一致，这样即时编译器 (JIT) 就能像 C 编译器一样通过计算读取数组了。

但是，如果你想在同种类型的数组中插入不同类型的元素，JIT 会销毁整个数组然后用以前的办法重建。

所以，如果你没写垃圾代码的话，JavaScript 的Array对象会维护一个真正的数组，这对现代 JS 开发者来说是一件大好事。

另外，在 ES2015/ES6 中， 数组还有其它改进。 TC39 决定在 JavaScript 中引入类型化数组，所以如今我们有 ArrayBuffer了。

ArrayBuffer 会有一大块连续的存储位置，你能用它做任何你想做的事情。不过，直接处理内存涉及非常底层的操作，相当复杂。

所以我们有 [Views](http://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Web/API/ArrayBufferView) 来处理 ArrayBuffer。已经有一些可用的 View 了，未来还会加：

```
var buffer = new ArrayBuffer(8);
var view  =  new Int32Array(buffer);
view[0]=100;
```

*如果你想知道更多有关类型化数组的信息，可以去看看[MDN 文档](http://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays)*

类型化数组性能良好且非常高效。WebGL 开发者因为缺少高效处理二进制数据的手段而经常面临性能问题，所以提出了类型化数组。你还可以使用[SharedArrayBuffer](http://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer)在多个 web-workers 间共享内存数据来提升性能。

惊讶吗？从简单的哈希映射开始，我们现在已经在讨论SharedArrayBuffer了。

## **旧数组 vs 类型化数组-性能**

我们已经讲了大量 JavaScript 数组的改进了。现在看看类型化数组的好处。我在Mac 上用 Node.js 8.4.0 跑了一些小测试：

**旧数组-插入**

```
var LIMIT = 10000000;
var arr = new Array(LIMIT);
console.time('Array insertion time');
for(var i=0;i<LIMIT; i++){
    arr[i]=i;
}
console.timeEnd('Array insertion time');
```

**所需时间**：55ms

**类型化数组-插入**

```
var LIMIT = 10000000;
var buffer = new ArrayBuffer(LIMIT * 4);
var arr = new Int32Array(buffer);
console.time("ArrayBuffer insertion time");
for (var i = 0; i &lt; LIMIT; i++) {
    arr[i] = i;
}
console.timeEnd("ArrayBuffer insertion time");
```

**所需时间**：52ms

我擦，我看到了啥？旧数组和 ArraryBuffer 的性能一样？不。回顾一下，前面我已经说过了，如今编译器已经在为类型一致的数组分配连续的存储空间了。所以在第一个例子中，即使我用的是new Array(LIMIT)，它仍然维护的是一个类型化数组。

让我们把第一个例子改成类型不一致的数组看看性能有没有变化。

**旧数组-插入（类型不一致）**

```
var LIMIT = 10000000;
var arr = new Array(LIMIT);
arr.push({a: 22});
console.time('Array insertion time');
for(var i=0;i<LIMIT; i++){
    arr[i]=i;
}
console.timeEnd('Array insertion time');
```

**所需时间**：1207ms

我在上面第三行插入了一个表达式让数组的类型不一致，其它所有的都跟前面一模一样。但是性能上有了巨大的变化：慢了整整22倍。

**旧数组-读取**

```
var arr = new Array(LIMIT);
arr.push({a: 22});
for (var i = 0; i &lt; LIMIT; i++) {
    arr[i] = i;
}
var p;
console.time("Array read time");
for (var i = 0; i &lt; LIMIT; i++) {
    //arr[i] = i;
    p = arr[i];
}
console.timeEnd("Array read time");
```

**所需时间**：196ms

**类型化数组-读取**

```
var LIMIT = 10000000;
var buffer = new ArrayBuffer(LIMIT * 4);
var arr = new Int32Array(buffer);
console.time("ArrayBuffer insertion time");
for (var i = 0; i &lt; LIMIT; i++) {
    arr[i] = i;
}
console.time("ArrayBuffer read time");
for (var i = 0; i &lt; LIMIT; i++) {
    var p = arr[i];
}
console.timeEnd("ArrayBuffer read time");
```

**所需时间**:27ms

## **结论**

在 JavaScript 中引入类型化数组是一个巨大的进步，Int8Array, Uint8Array, Uint8ClampedArray, Int16Array, Uint16Array, Int32Array, Uint32Array, Float32Array, Float64Array 等都是类型化数组 view,按照原生的 byte 数排序。你也可以看看 [DataView ](http://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/DataView)创建自己的 view 窗口。希望在将来会有更多 DataView 库方便我们使用 ArrayBuffer。

JavaScript 对数组做的改进很棒。现在它们快速、高效并且在分配内存时足够聪明了。

## **参考**

1. [Is JavaScript really interpreted or compiled language?](http://link.zhihu.com/?target=http%3A//voidcanvas.com/is-javascript-really-interpreted-or-compiled-language/)
2. [Create / filter an array to have only unique elements in it](http://link.zhihu.com/?target=http%3A//voidcanvas.com/create-filter-an-array-to-have-only-unique-elements-in-it/)
3. [Object.entries() & Object.values() in EcmaScript2017 (ES8) with examples](http://link.zhihu.com/?target=http%3A//voidcanvas.com/object-entries-object-values-ecmascript2017-es8-examples/)
4. [import vs require – ESM & commonJs module differences](http://link.zhihu.com/?target=http%3A//voidcanvas.com/import-vs-require/)
5. [A deep dive into ember routers – Ember.js Tutorial part 5](http://link.zhihu.com/?target=http%3A//voidcanvas.com/deep-dive-ember-routers-ember-js-tutorial-part-5/)
6. [Myths and Facts of JavaScript](http://link.zhihu.com/?target=http%3A//voidcanvas.com/myths-facts-javascript/)