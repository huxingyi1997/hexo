---
title:  js函数手写（旧版）
date: 2020-10-18 00:00:00
categories: 
- web前端
tags:
- javascript
- ES6
---
去看新版本吧，这一版本不折腾了，好多抄的，自己都没有整明白。

手写常见js函数，面试必备，多练几遍，争取手撕。
<!-- more -->

## 1.手动实现call,apply,bind

### 模拟实现call

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
}
```

### 模拟实现apply

`apply`实现类似`call`，参数为数组

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
}
```

### 模拟实现bind

1.处理参数，返回一个闭包

2.判断是否为构造函数调用，如果是则使用`new`调用当前函数

3.如果不是，使用`apply`，将`context`和处理好的参数传入

```js
Function.prototype.myBind = function (context = window, ...args) {
	if (this === Function.prototype) return undefined;
	const _this = this
    // 返回一个函数
    return function F(...arguments) {
        // 因为返回了一个函数，我们可以 new F()，所以需要判断
        if (this instanceof F) {
            return new _this(...args, ...arguments)
        }
        return _this.apply(context, args.concat(...arguments))
    }
}
```

### 面试够用版

```
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
    }
}
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

test1(1, 2, 3);
test2(1, 2, 3);
test3(1, 2, 3);
test4(1, 2, 3);
```



## 2.观察者模式

### 优点

- 可以广泛应用于异步编程，它可以代替我们传统的回调函数
- 我们不需要关注对象在异步执行阶段的内部状态，我们只关心事件完成的时间点
- 取代对象之间硬编码通知机制，一个对象不必显式调用另一个对象的接口，而是松耦合的联系在一起 。
- 虽然不知道彼此的细节，但不影响相互通信。更重要的是，其中一个对象改变不会影响另一个对象。

### Nodejs的EventEmitter

`Nodejs`的`EventEmitter`就是观察者模式的典型实现，`Nodejs`的`events`模块只提供了一个对象： `events.EventEmitter``。EventEmitter` 的核心就是事件触发与事件监听器功能的封装。

> Node.js 里面的许多对象都会分发事件：一个 net.Server 对象会在每次有新连接时触发一个事件， 一个 fs.readStream 对象会在文件被打开的时候触发一个事件。 所有这些产生事件的对象都是 events.EventEmitter 的实例。

### Api

**addListener(event, listener)**

为指定事件添加一个监听器，默认添加到监听器数组的尾部。

**removeListener(event, listener)**

移除指定事件的某个监听器，监听器必须是该事件已经注册过的监听器。它接受两个参数，第一个是事件名称，第二个是回调函数名称。

**setMaxListeners(n)**

默认情况下， EventEmitters 如果你添加的监听器超过 10 个就会输出警告信息。 setMaxListeners 函数用于提高监听器的默认限制的数量。

**once(event, listener)**

为指定事件注册一个单次监听器，即 监听器最多只会触发一次，触发后立刻解除该监听器。

**emit(event, [arg1], [arg2], [...])**

按监听器的顺序执行执行每个监听器，如果事件有注册监听返回 `true`，否则返回 `false`。

### 基本使用

```js
var events = require('events');
var eventEmitter = new events.EventEmitter();

// 监听器 #1
var listener1 = function listener1() {
   console.log('监听器 listener1 执行。');
}

// 监听器 #2
var listener2 = function listener2() {
  console.log('监听器 listener2 执行。');
}

// 绑定 connection 事件，处理函数为 listener1 
eventEmitter.addListener('connection', listener1);

// 绑定 connection 事件，调用一次，处理函数为 listener2
eventEmitter.once('connection', listener2);

// 处理 connection 事件 
eventEmitter.emit('connection');

// 处理 connection 事件 
eventEmitter.emit('connection');
```

### 手动实现EventEmitter

```js
function EventEmitter() {
    this._maxListeners = 10;
    this._events = Object.create(null);
}

// 向事件队列添加事件
// prepend为true表示向事件队列头部添加事件
EventEmitter.prototype.addListener = function (type, listener, prepend) {
    if (!this._events) {
    	this._events = Object.create(null);
    }
    if (this._events[type]) {
        if (prepend) {
        	this._events[type].unshift(listener);
        } else {
            this._events[type].push(listener);
        }
    } else {
    	this._events[type] = [listener];
    }
};

// 移除某个事件
EventEmitter.prototype.removeListener = function (type, listener) {
    if (Array.isArray(this._events[type])) {
        if (!listener) {
        	delete this._events[type]
        } else {
        	this._events[type] = this._events[type].filter(e => e !== listener && e.origin !== listener)
        }
    }
};

// 向事件队列添加事件，只执行一次
EventEmitter.prototype.once = function (type, listener) {
    const only = (...args) => {
        listener.apply(this, args);
        this.removeListener(type, listener);
    }
    only.origin = listener;
    this.addListener(type, only);
};

// 执行某类事件
EventEmitter.prototype.emit = function (type, ...args) {
    if (Array.isArray(this._events[type])) {
        this._events[type].forEach(fn => {
        	fn.apply(this, args);
        });
    }
};

// 设置最大事件监听个数
EventEmitter.prototype.setMaxListeners = function (count) {
	this.maxListeners = count;
};
```

测试代码：

```js
var emitter = new EventEmitter();

var onceListener = function (args) {
	console.log('我只能被执行一次', args, this);
}

var listener = function (args) {
	console.log('我是一个listener', args, this);
}

emitter.once('click', onceListener);
emitter.addListener('click', listener);

emitter.emit('click', '参数');
emitter.emit('click');

emitter.removeListener('click', listener);
emitter.emit('click');
```

### JavaScript自定义事件

```js
//1、创建事件
var myEvent = new Event("myEvent");

//2、注册事件监听器
elem.addEventListener("myEvent",function(e){

})

//3、触发事件
elem.dispatchEvent(myEvent);
```



## 3.防抖(debounce)

### 原理

不管事件触发频率多高，一定在事件触发`n`秒后才执行，如果你在一个事件触发的 `n` 秒内又触发了这个事件，就以新的事件的时间为准，`n`秒后才执行，总之，触发完事件 `n` 秒内不再触发事件，`n`秒后再执行。

在前端开发中会遇到一些频繁的事件触发，比如：

1. window 的 resize、scroll
2. mousedown、mousemove
3. keyup、keydown
   ……

为此，我们举个示例代码来了解事件如何频繁的触发：

我们写个 `index.html` 文件：

```
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

```
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

### 第一版

根据这段表述，我们可以写第一版的代码：

```
// 第一版
function debounce(func, wait) {
    var timeout;
    return function () {
        clearTimeout(timeout)
        timeout = setTimeout(func, wait);
    }
}
```

如果我们要使用它，以最一开始的例子为例：

```
container.onmousemove = debounce(getUserAction, 1000);
```

现在随你怎么移动，反正你移动完 1000ms 内不再触发，我才执行事件。看看使用效果：

![debounce 第一版](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210428111557.gif)

顿时就从 165 次降低成了 1 次!

棒棒哒，我们接着完善它。

### this

如果我们在 `getUserAction` 函数中 `console.log(this)`，在不使用 `debounce` 函数的时候，`this` 的值为：

```
<div id="container"></div>
```

但是如果使用我们的 debounce 函数，this 就会指向 Window 对象！

所以我们需要将 this 指向正确的对象。

我们修改下代码：

```
// 第二版
function debounce(func, wait) {
    var timeout;

    return function () {
        var context = this;

        clearTimeout(timeout)
        timeout = setTimeout(function(){
            func.apply(context)
        }, wait);
    }
}
```

现在 this 已经可以正确指向了。让我们看下个问题：

### event 对象

JavaScript 在事件处理函数中会提供事件对象 event，我们修改下 getUserAction 函数：

```
function getUserAction(e) {
    console.log(e);
    container.innerHTML = count++;
};
```

如果我们不使用 debouce 函数，这里会打印 MouseEvent 对象，如图所示：

![MouseEvent](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210428120949.png)

但是在我们实现的 debounce 函数中，却只会打印 undefined!

所以我们再修改一下代码：

```
// 第三版
function debounce(func, wait) {
    var timeout;

    return function () {
        var context = this;
        var args = arguments;

        clearTimeout(timeout)
        timeout = setTimeout(function(){
            func.apply(context, args)
        }, wait);
    }
}
```

到此为止，我们修复了两个小问题：

1. this 指向
2. event 对象

### 立刻执行

这个时候，代码已经很是完善了，但是为了让这个函数更加完善，我们接下来思考一个新的需求。

这个需求就是：

我不希望非要等到事件停止触发后才执行，我希望立刻执行函数，然后等到停止触发 n 秒后，才可以重新触发执行。

想想这个需求也是很有道理的嘛，那我们加个 immediate 参数判断是否是立刻执行。

```
// 第四版
function debounce(func, wait, immediate) {
    var timeout;
    
    return function () {
        var context = this;
        var args = arguments;

        if (timeout) clearTimeout(timeout);
        if (immediate) {
            // 如果已经执行过，不再执行
            var callNow = !timeout;
            timeout = setTimeout(function(){
                timeout = null;
            }, wait)
            if (callNow) func.apply(context, args)
        } else {
            timeout = setTimeout(function(){
                func.apply(context, args)
            }, wait);
        }
    }
}
```

再来看看使用效果：

![debounce 第四版](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210514223513.gif)

### 返回值

此时注意一点，就是 getUserAction 函数可能是有返回值的，所以我们也要返回函数的执行结果，但是当 immediate 为 false 的时候，因为使用了 setTimeout ，我们将 func.apply(context, args) 的返回值赋给变量，最后再 return 的时候，值将会一直是 undefined，所以我们只在 immediate 为 true 的时候返回函数的执行结果。

```
// 第五版
function debounce(func, wait, immediate) {

    var timeout, result;

    return function () {
        var context = this;
        var args = arguments;

        if (timeout) clearTimeout(timeout);
        if (immediate) {
            // 如果已经执行过，不再执行
            var callNow = !timeout;
            timeout = setTimeout(function(){
                timeout = null;
            }, wait)
            if (callNow) result = func.apply(context, args)
        } else {
            timeout = setTimeout(function(){
                func.apply(context, args)
            }, wait);
        }
        return result;
    }
}
```

### 取消

最后我们再思考一个小需求，我希望能取消 debounce 函数，比如说我 debounce 的时间间隔是 10 秒钟，immediate 为 true，这样的话，我只有等 10 秒后才能重新触发事件，现在我希望有一个按钮，点击后，取消防抖，这样我再去触发，就可以又立刻执行啦，是不是很开心？

为了这个需求，我们写最后一版的代码：

```
// 第六版
function debounce(func, wait, immediate) {
    var timeout, result;

    var debounced = function () {
        var context = this;
        var args = arguments;

        if (timeout) clearTimeout(timeout);
        if (immediate) {
            // 如果已经执行过，不再执行
            var callNow = !timeout;
            timeout = setTimeout(function(){
                timeout = null;
            }, wait)
            if (callNow) result = func.apply(context, args)
        }
        else {
            timeout = setTimeout(function(){
                func.apply(context, args)
            }, wait);
        }
        return result;
    };

    debounced.cancel = function() {
        clearTimeout(timeout);
        timeout = null;
    };

    return debounced;
}
```

那么该如何使用这个 cancel 函数呢？依然是以上面的 demo 为例：

```
var count = 1;
var container = document.getElementById('container');

function getUserAction(e) {
    container.innerHTML = count++;
};

var setUseAction = debounce(getUserAction, 10000, true);

container.onmousemove = setUseAction;

document.getElementById("button").addEventListener('click', function(){
    setUseAction.cancel();
})
```

演示效果如下：

![debounce-cancel](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210514223714.gif)

至此我们已经完整实现了一个 underscore 中的 debounce 函数，恭喜，撒花！

### 面试版代码

```js
// func是用户传入需要防抖的函数
// wait是等待时间
const debounce = function(func, wait = 50) {
    // 缓存一个定时器id
    let timer = 0;
    // 这里返回的函数是每次用户实际调用的防抖函数
    // 如果已经设定过定时器了就清空上一次的定时器
    // 开始一个新的定时器，延迟执行用户传入的方法
    return function(...args) {
        if (timer) clearTimeout(timer);
        timer = setTimeout(() => {
            func.apply(this, args);
        }, wait)
    }
}
```

**适用场景：**

> 按钮提交场景：防止多次提交按钮，只执行最后提交的一次 服务端验证场景：表单验证需要服务端配合，只执行一段连续的输入事件的最后一次，还有搜索联想词功能类似

## 4.节流(throttle)

### 原理

规定在一个单位时间内，只能触发一次函数。如果这个单位时间内触发多次函数，只有一次生效

### 节流是什么

节流的原理很简单：

如果你持续触发事件，每隔一段时间，只执行一次事件。

根据首次是否执行以及结束后是否执行，效果有所不同，实现的方式也有所不同。
我们用 leading 代表首次是否执行，trailing 代表结束后是否再执行一次。

关于节流的实现，有两种主流的实现方式，一种是使用时间戳，一种是设置定时器。

### 使用时间戳

让我们来看第一种方法：使用时间戳，当触发事件的时候，我们取出当前的时间戳，然后减去之前的时间戳(最一开始值设为 0 )，如果大于设置的时间周期，就执行函数，然后更新时间戳为当前的时间戳，如果小于，就不执行。

看了这个表述，是不是感觉已经可以写出代码了…… 让我们来写第一版的代码：

```
// 第一版
function throttle(func, wait) {
    var context, args;
    var previous = 0;

    return function() {
        var now = +new Date();
        context = this;
        args = arguments;
        if (now - previous > wait) {
            func.apply(context, args);
            previous = now;
        }
    }
}
```

例子依然是用讲 debounce 中的例子，如果你要使用：

```
container.onmousemove = throttle(getUserAction, 1000);
```

效果演示如下：

![使用时间戳](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210428141501.gif)

我们可以看到：当鼠标移入的时候，事件立刻执行，每过 1s 会执行一次，如果在 4.2s 停止触发，以后不会再执行事件。

### 使用定时器

接下来，我们讲讲第二种实现方式，使用定时器。

当触发事件的时候，我们设置一个定时器，再触发事件的时候，如果定时器存在，就不执行，直到定时器执行，然后执行函数，清空定时器，这样就可以设置下个定时器。

```
// 第二版
function throttle(func, wait) {
    var timeout;
    var previous = 0;

    return function() {
        context = this;
        args = arguments;
        if (!timeout) {
            timeout = setTimeout(function(){
                timeout = null;
                func.apply(context, args)
            }, wait)
        }
    }
}
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

所以我们综合两者的优势，然后双剑合璧，写一版代码：

```
// 第三版
function throttle(func, wait) {
    var timeout, context, args, result;
    var previous = 0;

    var later = function() {
        previous = +new Date();
        timeout = null;
        func.apply(context, args)
    };

    var throttled = function() {
        var now = +new Date();
        //下次触发 func 剩余的时间
        var remaining = wait - (now - previous);
        context = this;
        args = arguments;
         // 如果没有剩余的时间了或者你改了系统时间
        if (remaining <= 0 || remaining > wait) {
            if (timeout) {
                clearTimeout(timeout);
                timeout = null;
            }
            previous = now;
            func.apply(context, args);
        } else if (!timeout) {
            timeout = setTimeout(later, remaining);
        }
    };
    return throttled;
}
```

效果演示如下：

![throttle3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210514224321.gif)

我们可以看到：鼠标移入，事件立刻执行，晃了 3s，事件再一次执行，当数字变成 3 的时候，也就是 6s 后，我们立刻移出鼠标，停止触发事件，9s 的时候，依然会再执行一次事件。

### 优化

但是我有时也希望无头有尾，或者有头无尾，这个咋办？

那我们设置个 options 作为第三个参数，然后根据传的值判断到底哪种效果，我们约定:

leading：false 表示禁用第一次执行
trailing: false 表示禁用停止触发的回调

我们来改一下代码：

```
// 第四版
function throttle(func, wait, options) {
    var timeout, context, args, result;
    var previous = 0;
    if (!options) options = {};

    var later = function() {
        previous = options.leading === false ? 0 : new Date().getTime();
        timeout = null;
        func.apply(context, args);
        if (!timeout) context = args = null;
    };

    var throttled = function() {
        var now = new Date().getTime();
        if (!previous && options.leading === false) previous = now;
        var remaining = wait - (now - previous);
        context = this;
        args = arguments;
        if (remaining <= 0 || remaining > wait) {
            if (timeout) {
                clearTimeout(timeout);
                timeout = null;
            }
            previous = now;
            func.apply(context, args);
            if (!timeout) context = args = null;
        } else if (!timeout && options.trailing !== false) {
            timeout = setTimeout(later, remaining);
        }
    };
    return throttled;
}
```

### 取消

在 debounce 的实现中，我们加了一个 cancel 方法，throttle 我们也加个 cancel 方法：

```
// 第五版 非完整代码，完整代码请查看最后的演示代码链接
...
throttled.cancel = function() {
    clearTimeout(timeout);
    previous = 0;
    timeout = null;
}
...
```

### 注意

我们要注意 underscore 的实现中有这样一个问题：

那就是 `leading：false` 和 `trailing: false` 不能同时设置。

如果同时设置的话，比如当你将鼠标移出的时候，因为 trailing 设置为 false，停止触发的时候不会设置定时器，所以只要再过了设置的时间，再移入的话，就会立刻执行，就违反了 leading: false，bug 就出来了，所以，这个 throttle 只有三种用法：

```
container.onmousemove = throttle(getUserAction, 1000);
container.onmousemove = throttle(getUserAction, 1000, {
    leading: false
});
container.onmousemove = throttle(getUserAction, 1000, {
    trailing: false
});
```

至此我们已经完整实现了一个 underscore 中的 throttle 函数，恭喜，撒花！

### 面试版代码

```js
// func是用户传入需要防抖的函数
// wait是等待时间
const throttle = function (func, wait = 50) {
    // 上一次执行该函数的时间
    let lastTime = 0;
    return function(...args) {
        // 当前时间
        let now = +new Date();
        // 将当前时间和上一次执行函数时间对比
        // 如果差值大于设置的等待时间就执行函数
        if (now - lastTime > wait) {
            lastTime = now;
            func.apply(this, args)
        }
    }
}

// 使用方法：定时器
setInterval(
    throttle(() => {
        console.log(1)
    }, 500),
    1
)
```

**适用场景：**

> - 拖拽场景：固定时间内只执行一次，防止超高频次触发位置变动
> - 缩放场景：监控浏览器`resize`
> - 动画场景：避免短时间内多次触发动画引起性能问题

## 5.浅拷贝和深拷贝

**深拷贝和浅拷贝都是针对的引用类型，JS中的变量类型分为值类型（基本类型）和引用类型；对值类型进行复制操作会对值进行一份拷贝，而对引用类型赋值，则会进行地址的拷贝，最终两个变量指向同一份数据。**对于引用类型，会导致a b指向同一份数据，此时如果对其中一个进行修改，就会影响到另外一个，有时候这可能不是我们想要的结果，如果对这种现象不清楚的话，还可能造成不必要的bug。

那么如何切断a和b之间的关系呢，可以拷贝一份a的数据，根据拷贝的层级不同可以分为浅拷贝和深拷贝，浅拷贝就是只进行一层拷贝，深拷贝就是无限层级拷贝**假设B复制了A，当修改A时，看B是否会发生变化，如果B也跟着变了，说明这是浅拷贝，拿人手短，如果B没变，那就是深拷贝，自食其力。**

### 浅拷贝

```
arr.slice();
arr.concat();
```

### 深拷贝

#### 简单版：

```
JSON.parse(JSON.stringify(obj))
```

##### 局限性：

- 他无法实现对函数 、RegExp等特殊对象的克隆
- 会抛弃对象的constructor,所有的构造函数会指向Object
- 对象有循环引用,会报错

#### 面试版:递归

考虑到数组和对象

```
function deepCopy(obj) {
	let res 
	// 判断是否是简单数据类型
	if (typeof obj == "object") {
		// 复杂数据类型
		res = obj.constructor == Array ? [] : {};
		for (let i in obj) {
			res[i] = typeof obj[i] == "object" ? deepCopy(obj[i]) : obj[i]
		}
	} else {
		// 简单数据类型 直接 == 赋值
		res = obj;
	}
	return res;
}
```

#### 循环引用

上述版本执行下面这样一个测试用例：

```javascript
const target = {
    field1: 1,
    field2: undefined,
    field3: {
        child: 'child'
    },
    field4: [2, 4, 8]
};
target.target = target;
```

因为递归进入死循环导致栈内存溢出了。

原因就是上面的对象存在循环引用的情况，即对象的属性间接或直接的引用了自身的情况：

解决循环引用问题，我们可以额外开辟一个存储空间，来存储当前对象和拷贝对象的对应关系，当需要拷贝当前对象时，先去存储空间中找，有没有拷贝过这个对象，如果有的话直接返回，如果没有的话继续拷贝，这样就巧妙化解的循环引用的问题。

这个存储空间，需要可以存储 `key-value`形式的数据，且 `key`可以是一个引用类型，我们可以选择 `Map`这种数据结构：

- 检查`map`中有无克隆过的对象
- 有 - 直接返回
- 没有 - 将当前对象作为`key`，克隆对象作为`value`进行存储
- 继续克隆

```javascript
function deepCopy(obj, map = new Map()) {
	let res;
    if (typeof obj === 'object') {
        res = obj.constructor == Array ? [] : {};
        if (map.get(obj)) {
            return obj;
        }
        map.set(obj, res);
        for (let i in obj) {
            res[i] = typeof obj[i] == "object" ? deepCopy(obj[i]) : obj[i];
        }
        return res;
    } else {
        // 简单数据类型 直接 == 赋值
		res = obj;
	}
	return res;
};
```

可以看到，执行没有报错，且 `target`属性，变为了一个 `Circular`类型，即循环应用的意思。

接下来，我们可以使用， `WeakMap`提代 `Map`来使代码达到画龙点睛的作用。

```javascript
function deepCopy(obj, map = new WeakMap()) {
    // ...
};
```

为什么要这样做呢？，先来看看 `WeakMap`的作用：

> WeakMap 对象是一组键/值对的集合，其中的键是弱引用的。其键必须是对象，而值可以是任意的。

什么是弱引用呢？

> 在计算机程序设计中，弱引用与强引用相对，是指不能确保其引用的对象不会被垃圾回收器回收的引用。一个对象若只被弱引用所引用，则被认为是不可访问（或弱可访问）的，并因此可能在任何时刻被回收。

我们默认创建一个对象：`const obj={}`，就默认创建了一个强引用的对象，我们只有手动将 `obj=null`，它才会被垃圾回收机制进行回收，如果是弱引用对象，垃圾回收机制会自动帮我们回收。

举个例子：

如果我们使用 `Map`的话，那么对象间是存在强引用关系的：

```javascript
let obj = { name : 'ConardLi'}
const target = {
    obj:'code秘密花园'
}
obj = null;
```

虽然我们手动将 `obj`，进行释放，然是 `target`依然对 `obj`存在强引用关系，所以这部分内存依然无法被释放。

再来看 `WeakMap`：

```javascript
let obj = { name : 'ConardLi'}
const target = new WeakMap();
target.set(obj,'code秘密花园');
obj = null;
```

如果是 `WeakMap`的话， `target`和 `obj`存在的就是弱引用关系，当下一次垃圾回收机制执行时，这块内存就会被释放掉。

设想一下，如果我们要拷贝的对象非常庞大时，使用 `Map`会对内存造成非常大的额外消耗，而且我们需要手动清除 `Map`的属性才能释放这块内存，而 `WeakMap`会帮我们巧妙化解这个问题。

我也经常在某些代码中看到有人使用 `WeakMap`来解决循环引用问题，但是解释都是模棱两可的，当你不太了解 `WeakMap`的真正作用时。我建议你也不要在面试中写这样的代码，结果只能是给自己挖坑，即使是准备面试，你写的每一行代码也都是需要经过深思熟虑并且非常明白的。

能考虑到循环引用的问题，你已经向面试官展示了你考虑问题的全面性，如果还能用 `WeakMap`解决问题，并很明确的向面试官解释这样做的目的，那么你的代码在面试官眼里应该算是合格了。

#### 性能优化

在上面的代码中，我们遍历数组和对象都使用了 `forin`这种方式，实际上 `forin`在遍历时效率是非常低的，对比下常见的三种循环 `for、while、forin`的执行效率：

![img](https://ask.qcloudimg.com/http-save/yehe-3713434/sf9725l2cd.jpeg?imageView2/2/w/1620)

 `while`的效率是最好的，所以，我们可以想办法把 `forin`遍历改变为 `while`遍历。

我们先使用 `while`来实现一个通用的 `forEach`遍历， `iteratee`是遍历的回掉函数，他可以接收每次遍历的 `value`和 `index`两个参数：

```javascript
function forEach(array, iteratee) {
    let index = -1;
    const length = array.length;
    while (++index < length) {
        iteratee(array[index], index);
    }
    return array;
}
```

下面对我们的 `cloen`函数进行改写：当遍历数组时，直接使用 `forEach`进行遍历，当遍历对象时，使用 `Object.keys`取出所有的 `key`进行遍历，然后在遍历时把 `forEach`会调函数的 `value`当作 `key`使用：

```javascript
function deepCopy(obj, map = new WeakMap()) {
	let res;
    if (typeof obj === 'object') {
        res = obj.constructor == Array ? [] : {};
        if (map.get(obj)) {
            return obj;
        }
        map.set(obj, res);
        
        const keys = obj.constructor == Array ?  undefined : Object.keys(target);
        forEach(keys || target, (value, key) => {
            if (keys) {
                key = value;
            }
            res[key] = deepCopy(obj[key], map);
        });
        return res;
    } else {
        // 简单数据类型 直接 == 赋值
		res = obj;
	}
	return res;
};
```

#### 其他数据类型

在上面的代码中，我们其实只考虑了普通的 `object`和 `array`两种数据类型，实际上所有的引用类型远远不止这两个，还有很多，下面我们先尝试获取对象准确的类型。

##### 合理的判断引用类型

首先，判断是否为引用类型，我们还需要考虑 `function`和 `null`两种特殊的数据类型：

```javascript
function isObject(obj) {
    const type = typeof obj;
    return obj !== null && (type === 'object' || type === 'function');
}
if (!isObject(obj)) {
        return obj;
    }
    // ...
```

##### 获取数据类型

我们可以使用 `toString`来获取准确的引用类型：

> 每一个引用类型都有 `toString`方法，默认情况下， `toString()`方法被每个 `Object`对象继承。如果此方法在自定义对象中未被覆盖，t `oString()`返回 `"[object type]"`，其中type是对象的类型。

注意，上面提到了如果此方法在自定义对象中未被覆盖， `toString`才会达到预想的效果，事实上，大部分引用类型比如 `Array、Date、RegExp`等都重写了 `toString`方法。

我们可以直接调用 `Object`原型上未被覆盖的 `toString()`方法，使用 `call`来改变 `this`指向来达到我们想要的效果。

```javascript
function getType(target) {
    return Object.prototype.toString.call(target);
}
```

![img](https://ask.qcloudimg.com/http-save/yehe-3713434/nb0dwyns7q.jpeg?imageView2/2/w/1620)

下面我们抽离出一些常用的数据类型以便后面使用：

```javascript
const mapTag = '[object Map]';
const setTag = '[object Set]';
const arrayTag = '[object Array]';
const objectTag = '[object Object]';

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

我们分别为它们做不同的拷贝。

##### 可继续遍历的类型

上面我们已经考虑的 `object`、 `array`都属于可以继续遍历的类型，因为它们内存都还可以存储其他数据类型的数据，另外还有 `Map`， `Set`等都是可以继续遍历的类型，这里我们只考虑这四种，如果你有兴趣可以继续探索其他类型。

有序这几种类型还需要继续进行递归，我们首先需要获取它们的初始化数据，例如上面的 `[]`和 `{}`，我们可以通过拿到 `constructor`的方式来通用的获取。

例如：`consttarget={}`就是 `consttarget=newObject()`的语法糖。另外这种方法还有一个好处：因为我们还使用了原对象的构造方法，所以它可以保留对象原型上的数据，如果直接使用普通的 `{}`，那么原型必然是丢失了的。

```javascript
function getInit(target) {
    const Ctor = target.constructor;
    return new Ctor();
}
```

下面，我们改写 `clone`函数，对可继续遍历的数据类型进行处理：

```javascript
function deepCopy(obj, map = new WeakMap()) {
    // 克隆原始类型
    if (!isObject(obj)) {
        return obj;
    }

    // 初始化
    const type = getType(obj);
    let res;
    if (deepTag.includes(type)) {
        res = getInit(obj, type);
    }

    // 防止循环引用
    if (map.get(obj)) {
        return obj;
    }
    map.set(obj, res);

    // 克隆set
    if (type === setTag) {
        ob.forEach(value => {
            res.add(deepCopy(value));
        });
        return res;
    }

    // 克隆map
    if (type === mapTag) {
        target.forEach((value, key) => {
            res.set(key, deepCopy(value));
        });
        return res;
    }

    // 克隆对象和数组
    const keys = type === arrayTag ? undefined : Object.keys(obj);
    forEach(keys || target, (value, key) => {
        if (keys) {
            key = value;
        }
        res[key] = deepCopy(obj[key], map);
    });
    
    return res;
}
```

我们执行clone5.test.js对下面的测试用例进行测试：

```javascript
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
```

执行结果：

![img](https://ask.qcloudimg.com/http-save/yehe-3713434/fh73smmild.jpeg?imageView2/2/w/1620)

没有问题，继续处理其他类型：

##### 不可继续遍历的类型

其他剩余的类型我们把它们统一归类成不可处理的数据类型，我们依次进行处理：

`Bool`、 `Number`、 `String`、 `String`、 `Date`、 `Error`这几种类型我们都可以直接用构造函数和原始数据创建一个新对象：

```javascript
function cloneOtherType(obj, type) {
    const Ctor = obj.constructor;
    switch (type) {
        case boolTag:
        case numberTag:
        case stringTag:
        case errorTag:
        case dateTag:
            return new Ctor(obj);
        case regexpTag:
            return cloneReg(obj);
        case symbolTag:
            return cloneSymbol(obj);
        default:
            return null;
    }
}
```

克隆 `Symbol`类型：

```javascript
function cloneSymbol(obj) {
    return Object(Symbol.prototype.valueOf.call(obj));
}

克隆正则：

function cloneReg(obj) {
    const reFlags = /\w*$/;
    const res = new obj.constructor(obj.source, reFlags.exec(obj));
    res.lastIndex = obj.lastIndex;
    return res;
}
```

实际上还有很多数据类型我这里没有写到，有兴趣的话可以继续探索实现一下。

能写到这里，面试官已经看到了你考虑问题的严谨性，你对变量和类型的理解，对 `JS API`的熟练程度，相信面试官已经开始对你刮目相看了。

#### 克隆函数

最后，我把克隆函数单独拎出来了，实际上克隆函数是没有实际应用场景的，两个对象使用一个在内存中处于同一个地址的函数也是没有任何问题的，我特意看了下 `lodash`对函数的处理：

```javascript
const isFunc = typeof value == 'function'
 if (isFunc || !cloneableTags[tag]) {
        return object ? value : {}
 }
```

可见这里如果发现是函数的话就会直接返回了，没有做特殊的处理，但是我发现不少面试官还是热衷于问这个问题的，而且据我了解能写出来的少之又少。。。

实际上这个方法并没有什么难度，主要就是考察你对基础的掌握扎实不扎实。

首先，我们可以通过 `prototype`来区分下箭头函数和普通函数，箭头函数是没有 `prototype`的。

我们可以直接使用 `eval`和函数字符串来重新生成一个箭头函数，注意这种方法是不适用于普通函数的。

我们可以使用正则来处理普通函数：

分别使用正则取出函数体和函数参数，然后使用 `newFunction([arg1[,arg2[,...argN]],]functionBody)`构造函数重新构造一个新的函数：

```javascript
function cloneFunction(func) {
    const bodyReg = /(?<={)(.|\n)+(?=})/m;
    const paramReg = /(?<=\().+(?=\)\s+{)/;
    const funcString = func.toString();
    if (func.prototype) {
        // console.log('普通函数');
        const param = paramReg.exec(funcString);
        const body = bodyReg.exec(funcString);
        if (body) {
            // console.log('匹配到函数体：', body[0]);
            if (param) {
                const paramArr = param[0].split(',');
                console.log('匹配到参数：', paramArr);
                return new Function(...paramArr, body[0]);
            } else {
                return new Function(body[0]);
            }
        } else {
            return null;
        }
    } else {
        return eval(funcString);
    }
}
```

最后，我们再来执行clone6.test.js对下面的测试用例进行测试：

```javascript
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
```

执行结果：

![img](https://ask.qcloudimg.com/http-save/yehe-3713434/xtb15ikjai.jpeg?imageView2/2/w/1620)

#### 最后

为了更好的阅读，我们用一张图来展示上面所有的代码：

![img](https://ask.qcloudimg.com/http-save/yehe-3713434/im20c61hq4.jpeg?imageView2/2/w/1620)

完整代码：https://github.com/ConardLi/ConardLi.github.io/blob/master/demo/deepClone/src/clone_6.js

可见，一个小小的深拷贝还是隐藏了很多的知识点的。

千万不要以最低的要求来要求自己，如果你只是为了应付面试中的一个题目，那么你可能只会去准备上面最简陋的深拷贝的方法。

但是面试官考察你的目的是全方位的考察你的思维能力，如果你写出上面的代码，可以体现你多方位的能力：

- 基本实现
  - 递归能力
- 循环引用
  - 考虑问题的全面性
  - 理解weakmap的真正意义
- 多种类型
  - 考虑问题的严谨性
  - 创建各种引用类型的方法，JS API的熟练程度
  - 准确的判断数据类型，对数据类型的理解程度
- 通用遍历：
  - 写代码可以考虑性能优化
  - 了解集中遍历的效率
  - 代码抽象能力
- 拷贝函数：
  - 箭头函数和普通函数的区别
  - 正则表达式熟练程度

看吧，一个小小的深拷贝能考察你这么多的能力，如果面试官看到这样的代码，怎么能够不惊艳呢？

其实面试官出的所有题目你都可以用这样的思路去考虑。不要为了应付面试而去背一些代码，这样在有经验的面试官面前会都会暴露出来。你写的每一段代码都要经过深思熟虑，为什么要这样用，还能怎么优化...这样才能给面试官展现一个最好的你。

#### 参考

- WeakMap
- lodash

## 6.数组去重、扁平、最值

### 去重

#### Object

```
function unique (array) {
    let container = {};
    return array.filter((item, index) =>  container.hasOwnProperty(item) ? false : (container[item] = true));
}
```

####  indexOf + filter

```
function unique (arr) {
	return arr.filter((e,i) => arr.indexOf(e) === i);
}
```

#### Set

```
function unique (arr) {
	return  [...new Set(arr)];
}
```

### 扁平

> https://github.com/NieZhuZhu/Blog)

#### 一段代码总结 `Array.prototype.flat()` 特性

> 注：数组拍平方法 `Array.prototype.flat()` 也叫数组扁平化、数组拉平、数组降维。 本文统一叫：数组拍平

```
const animals = ["🐷", ["🐶", "🐂"], ["🐎", ["🐑", ["🐲"]], "🐛"]];

// 不传参数时，默认“拉平”一层
animals.flat();
// ["🐷", "🐶", "🐂", "🐎", ["🐑", ["🐲"]], "🐛"]

// 传入一个整数参数，整数即“拉平”的层数
animals.flat(2);
// ["🐷", "🐶", "🐂", "🐎", "🐑", ["🐲"], "🐛"]

// Infinity 关键字作为参数时，无论多少层嵌套，都会转为一维数组
animals.flat(Infinity);
// ["🐷", "🐶", "🐂", "🐎", "🐑", "🐲", "🐛"]

// 传入 <=0 的整数将返回原数组，不“拉平”
animals.flat(0);
animals.flat(-10);
// ["🐷", ["🐶", "🐂"], ["🐎", ["🐑", ["🐲"]], "🐛"]];

// 如果原数组有空位，flat()方法会跳过空位。
["🐷", "🐶", "🐂", "🐎",,].flat();
// ["🐷", "🐶", "🐂", "🐎"]
```

#### `Array.prototype.flat()` 特性总结

- `Array.prototype.flat()` 用于将嵌套的数组“拉平”，变成一维的数组。该方法返回一个新数组，对原数据没有影响。
- 不传参数时，默认“拉平”一层，可以传入一个整数，表示想要“拉平”的层数。
- 传入 `<=0` 的整数将返回原数组，不“拉平”
- `Infinity` 关键字作为参数时，无论多少层嵌套，都会转为一维数组
- 如果原数组有空位，`Array.prototype.flat()` 会跳过空位。

#### 面试官 N 连问

##### 第一问：实现一个简单的数组拍平 `flat` 函数

首先，我们将花一点篇幅来探讨如何实现一个简单的数组拍平 `flat` 函数，详细介绍多种实现的方案，然后再尝试接住面试官的连环追问。

###### 实现思路

如何实现呢，思路非常简单：实现一个有数组拍平功能的 `flat` 函数，**我们要做的就是在数组中找到是数组类型的元素，然后将他们展开**。这就是实现数组拍平 `flat` 方法的关键思路。

有了思路，我们就需要解决实现这个思路需要克服的困难：

- **第一个要解决的就是遍历数组的每一个元素；**
- **第二个要解决的就是判断元素是否是数组；**
- **第三个要解决的就是将数组的元素展开一层；**

###### 遍历数组的方案

遍历数组并取得数组元素的方法非常之多，**包括且不限于下面几种**：

- `for 循环`
- `for...of`
- `for...in`
- `forEach()`
- `entries()`
- `keys()`
- `values()`
- `reduce()`
- `map()`

```
const arr = [1, 2, 3, 4, [1, 2, 3, [1, 2, 3, [1, 2, 3]]], 5, "string", { name: "弹铁蛋同学" }];
// 遍历数组的方法有太多，本文只枚举常用的几种
// for 循环
for (let i = 0; i < arr.length; i++) {
	console.log(arr[i]);
}
// for...of
for (let value of arr) {
	console.log(value);
}
// for...in
for (let i in arr) {
	console.log(arr[i]);
}
// forEach 循环
arr.forEach(value => {
	console.log(value);
});
// entries（）
for (let [index, value] of arr.entries()) {
	console.log(value);
}
// keys()
for (let index of arr.keys()) {
	console.log(arr[index]);
}
// values()
for (let value of arr.values()) {
	console.log(value);
}
// reduce()
arr.reduce((pre, cur) => {
	console.log(cur);
}, []);
// map()
arr.map(value => console.log(value));
```

只要是能够遍历数组取到数组中每一个元素的方法，都是一种可行的解决方案。

###### 判断元素是数组的方案

- `instanceof`
- `constructor`
- `Object.prototype.toString`
- `isArray`

```
const arr = [1, 2, 3, 4, [1, 2, 3, [1, 2, 3, [1, 2, 3]]], 5, "string", { name: "弹铁蛋同学" }];
arr instanceof Array;
// true
arr.constructor === Array;
// true
Object.prototype.toString.call(arr) === '[object Array]';
// true
Array.isArray(arr);
// true
```

**说明**：

- `instanceof` 操作符是假定只有一种全局环境，如果网页中包含多个框架，多个全局环境，如果你从一个框架向另一个框架传入一个数组，那么传入的数组与在第二个框架中原生创建的数组分别具有各自不同的构造函数。（所以在这种情况下会不准确）

- `typeof` 操作符对数组取类型将返回 `object`

- 因为` constructor` 可以被重写，所以不能确保一定是数组。

  ```
  const str = 'abc';
  str.constructor = Array;
  str.constructor === Array;
  // true
  ```

###### 将数组的元素展开一层的方案

- ###### 扩展运算符 +concat
- `concat()` 方法用于合并两个或多个数组，在拼接的过程中加上扩展运算符会展开一层数组。详细见下面的代码。
- `conca`t +` apply`

主要是利用 `apply` 在绑定作用域时，传入的第二个参数是一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 `func` 函数。也就是在调用 `apply` 函数的过程中，会将传入的数组一个一个的传入到要执行的函数中，也就是相当对数组进行了一层的展开。

- `toString` + `split`

不推荐使用 `toString` + `split` 方法，因为操作字符串是和危险的事情，在[上一文章](https://segmentfault.com/a/1190000021230185)中我做了一个操作字符串的案例还被许多小伙伴们批评了。如果数组中的元素所有都是数字的话，`toString` +` split` 是可行的，并且是一步搞定。

```
const arr = [1, 2, 3, 4, [1, 2, 3, [1, 2, 3, [1, 2, 3]]], 5, "string", { name: "弹铁蛋同学" }];
// 扩展运算符 + concat
[].concat(...arr)
// [1, 2, 3, 4, 1, 2, 3, [1, 2, 3, [1, 2, 3]], 5, "string", { name: "弹铁蛋同学" }];

// concat + apply
[].concat.apply([], arr);
// [1, 2, 3, 4, 1, 2, 3, [1, 2, 3, [1, 2, 3]], 5, "string", { name: "弹铁蛋同学" }];

// toString  + split
const arr2 =[1, 2, 3, 4, [1, 2, 3, [1, 2, 3, [1, 2, 3]]]]
arr2.toString().split(',').map(v=>parseInt(v))
// [1, 2, 3, 4, 1, 2, 3, 1, 2, 3, 1, 2, 3]
```

总结完要解决的三大困难，那我们就可以非常轻松的实现一版数组拍平 `flat` 函数了。

```
const arr = [1, 2, 3, 4, [1, 2, 3, [1, 2, 3, [1, 2, 3]]], 5, "string", { name: "弹铁蛋同学" }];
// concat + 递归
function flat(arr) {
  let arrResult = [];
  arr.forEach(item => {
    if (Array.isArray(item)) {
      arrResult = arrResult.concat(arguments.callee(item));   // 递归
      // 或者用扩展运算符
      // arrResult.push(...arguments.callee(item));
    } else {
      arrResult.push(item);
    }
  });
  return arrResult;
}
flat(arr)
// [1, 2, 3, 4, 1, 2, 3, 1, 2, 3, 1, 2, 3, 5, "string", { name: "弹铁蛋同学" }];
```

到这里，恭喜你成功得到了面试官对你手撕代码能力的基本认可🎉。但是面试官往往会不止于此，将继续考察面试者的各种能力。

##### 第二问：用 `reduce` 实现 `flat` 函数

我见过很多的面试官都很喜欢点名道姓的要面试者直接用 `reduce` 去实现 `flat` 函数。想知道为什么？文章后半篇我们考虑数组空位的情况的时候就知道为啥了。其实思路也是一样的。

```
const arr = [1, 2, 3, 4, [1, 2, 3, [1, 2, 3, [1, 2, 3]]], 5, "string", { name: "弹铁蛋同学" }]

// 首先使用 reduce 展开一层
arr.reduce((pre, cur) => pre.concat(cur), []);
// [1, 2, 3, 4, 1, 2, 3, [1, 2, 3, [1, 2, 3]], 5, "string", { name: "弹铁蛋同学" }];

// 用 reduce 展开一层 + 递归
const flat = arr => {
  return arr.reduce((pre, cur) => {
    return pre.concat(Array.isArray(cur) ? flat(cur) : cur);
  }, []);
};
// [1, 2, 3, 4, 1, 2, 3, 1, 2, 3, 1, 2, 3, 5, "string", { name: "弹铁蛋同学" }];
```

##### 第三问：使用栈的思想实现 `flat` 函数

```
// 栈思想
function flat(arr) {
  const result = []; 
  const stack = [].concat(arr);  // 将数组元素拷贝至栈，直接赋值会改变原数组
  //如果栈不为空，则循环遍历
  while (stack.length !== 0) {
    const val = stack.pop(); 
    if (Array.isArray(val)) {
      stack.push(...val); //如果是数组再次入栈，并且展开了一层
    } else {
      result.unshift(val); //如果不是数组就将其取出来放入结果数组中
    }
  }
  return result;
}
const arr = [1, 2, 3, 4, [1, 2, 3, [1, 2, 3, [1, 2, 3]]], 5, "string", { name: "弹铁蛋同学" }]
flat(arr)
// [1, 2, 3, 4, 1, 2, 3, 1, 2, 3, 1, 2, 3, 5, "string", { name: "弹铁蛋同学" }];
```

##### 第四问：通过传入整数参数控制“拉平”层数

```
// reduce + 递归
function flat(arr, num = 1) {
  return num > 0
    ? arr.reduce(
        (pre, cur) =>
          pre.concat(Array.isArray(cur) ? flat(cur, num - 1) : cur),
        []
      )
    : arr.slice();
}
const arr = [1, 2, 3, 4, [1, 2, 3, [1, 2, 3, [1, 2, 3]]], 5, "string", { name: "弹铁蛋同学" }]
flat(arr, Infinity);
// [1, 2, 3, 4, 1, 2, 3, 1, 2, 3, 1, 2, 3, 5, "string", { name: "弹铁蛋同学" }];
```

##### 第五问：使用 `Generator` 实现 `flat` 函数

```
function* flat(arr, num) {
  if (num === undefined) num = 1;
  for (const item of arr) {
    if (Array.isArray(item) && num > 0) {   // num > 0
      yield* flat(item, num - 1);
    } else {
      yield item;
    }
  }
}
const arr = [1, 2, 3, 4, [1, 2, 3, [1, 2, 3, [1, 2, 3]]], 5, "string", { name: "弹铁蛋同学" }];
// 调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象。
// 也就是遍历器对象（Iterator Object）。所以我们要用一次扩展运算符得到结果
[...flat(arr, Infinity)];
// [1, 2, 3, 4, 1, 2, 3, 1, 2, 3, 1, 2, 3, 5, "string", { name: "弹铁蛋同学" }];
```

##### 第六问：实现在原型链上重写 `flat` 函数

```
Array.prototype.fakeFlat = function(num = 1) {
  if (!Number(num) || Number(num) < 0) {
    return this;
  }
  let arr = this.concat();    // 获得调用 fakeFlat 函数的数组
  while (num > 0) {           
    if (arr.some(x => Array.isArray(x))) {
      arr = [].concat.apply([], arr);    // 数组中还有数组元素的话并且 num > 0，继续展开一层数组 
    } else {
      break; // 数组中没有数组元素并且不管 num 是否依旧大于 0，停止循环。
    }
    num--;
  }
  return arr;
};
const arr = [1, 2, 3, 4, [1, 2, 3, [1, 2, 3, [1, 2, 3]]], 5, "string", { name: "弹铁蛋同学" }]
arr.fakeFlat(Infinity)
// [1, 2, 3, 4, 1, 2, 3, 1, 2, 3, 1, 2, 3, 5, "string", { name: "弹铁蛋同学" }];
```

##### 第七问：考虑数组空位的情况

由最开始我们总结的 `flat` 特性知道，`flat` 函数执行是会跳过空位的。ES5 大多数数组方法对空位的处理都会选择跳过空位包括：`forEach()`, `filter()`, `reduce()`, `every()` 和 `some()` 都会跳过空位。

所以我们可以利用上面几种方法来实现 flat 跳过空位的特性

```
// reduce + 递归
Array.prototype.fakeFlat = function(num = 1) {
  if (!Number(num) || Number(num) < 0) {
    return this;
  }
  let arr = [].concat(this);
  return num > 0
    ? arr.reduce(
        (pre, cur) =>
          pre.concat(Array.isArray(cur) ? cur.fakeFlat(--num) : cur),
        []
      )
    : arr.slice();
};
const arr = [1, [3, 4], , ,];
arr.fakeFlat()
// [1, 3, 4]

// foEach + 递归
Array.prototype.fakeFlat = function(num = 1) {
  if (!Number(num) || Number(num) < 0) {
    return this;
  }
  let arr = [];
  this.forEach(item => {
    if (Array.isArray(item)) {
      arr = arr.concat(item.fakeFlat(--num));
    } else {
      arr.push(item);
    }
  });
  return arr;
};
const arr = [1, [3, 4], , ,];
arr.fakeFlat()
// [1, 3, 4]
```

##### 扩展阅读：**由于空位的处理规则非常不统一，所以建议避免出现空位。**

**ES5 对空位的处理，就非常不一致，大多数情况下会忽略空位。**

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

#### 总结

面试官现场考察一道写代码的题目，其实不仅仅是写代码，在写代码的过程中会遇到各种各样的知识点和代码的边界情况。虽然大多数情况下，面试官不会那么变态，就 `flat` 实现去连续追问面试者，并且手撕好几个版本，但面试官会要求在你写的那版代码的基础上再写出一个更完美的版本是常有的事情。只有我们沉下心来把基础打扎实，不管面试官如何追问，我们都能自如的应对。`flat` 的实现绝对不会只有文中列出的这几个版本，敲出自己的代码是最好的进步，在评论区或者在 [issue](https://github.com/NieZhuZhu/Blog/issues/2) 中写出你自己的版本吧！

####  基本实现

递归调用

```
function flat (array) {
    let result = [];
    for (let i = 0; i < array.length; i++) {
        if (Array.isArray(array[i])) {
        	result = result.concat(flat(array[i]));
        } else {
        	result.push(array[i]);
        }
    }
    return result;
}
```

#### 使用reduce简化

```js
function flatten(array) {
    return array.reduce(
        (target, current) => Array.isArray(current) ? target.concat(flatten(current)) : target.concat(current)
    , [])
}
```

#### 直接调用

```js
// 第一种处理
arr_flat = arr.flat(Infinity);
```

#### 正则表达式

```
// 第二种处理
ary = arr.toSting()).replace(/(\[|\])/g, '').split(',');
```

```js
// 第三种处理：递归处理
let result = [];
let fn = function(ary) {
  for(let i = 0; i < ary.length; i++) }{
    let item = ary[i];
    if (Array.isArray(ary[i])){
      fn(item);
    } else {
      result.push(item);
    }
  }
}
```

### 最值

#### reduce

```
array.reduce((c,n) => Math.max(c,n));
```

#### Math.max

```
const array = [3,2,1,4,5];
Math.max.apply(null, array);
Math.max(...array);
```

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

## 7.数组乱序-洗牌算法

```js
 function disorder(array) {
     const length = array.length;
     let current = length - 1;
     let random;
     while (current > -1) {
         random = Math.floor(length * Math.random());
         [array[current], array[random]] = [array[random], array[current]];
         current--;
     }
     return array;
 }
```

## 8.函数柯里化

### 定义

把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数且返回结果的新函数的技术

通俗易懂的解释：用闭包把参数保存起来，当参数的数量足够执行函数了，就开始执行函数。

```
function curry(fn, args = []) {
    let length = fn.length;
    return function() {
        newArgs = args.concat(Array.prototype.slice.call(arguments));
        if (newArgs.length < length) {
            return curry.call(this, fn, newArgs);
        } else {
            return fn.apply(this, newArgs);
        }
    }
}

function multiFn(a, b, c) {
    return a * b * c;
}

var multi = curry(multiFn);

console.log(multi(2)(3)(4)); // 24
console.log(multi(2,3,4)); // 24
console.log(multi(2)(3,4)); // 24
console.log(multi(2,3)(4)); // 24
```

### ES6写法

```
const curry = (fn, arr = []) => (...args) => (
  arg => arg.length === fn.length ? fn(...arg) : curry(fn, arg)
)([...arr, ...args])

function multiFn(a, b, c) {
    return a * b * c;
}

var multi = curry(multiFn);

console.log(multi(2)(3)(4));
console.log(multi(2,3,4));
console.log(multi(2)(3,4));
console.log(multi(2,3)(4));
```

### 简单写法版

```
function currying(fn, ...args) {
    if (args.length >= fn.length) {
    	// 判断当前函数传入的参数是否大于或等于fn需要参数的数量，如果是，直接执行fn
    	return fn(...args);
    } else {
    	// 如果传入参数数量不够，返回一个闭包，暂存传入的参数，并重新返回currying函数
    	return (...args2) => curry(fn, ...args, ...args2);
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

## 9.手动实现JSONP

### 原理

`JSONP` 的原理很简单，就是利用 `<script>` 标签没有跨域限制的漏洞。通过 `<script>`标签指向一个需要访问的地址并提供一个回调函数来接收数据当需要通讯时

- 1.将传入的data数据转化为url字符串形式
- 2.处理url中的回调函数
- 3.创建一个script标签并插入到页面中
- 4.挂载回调函数

```
(function (window,document) {
    "use strict";
    var jsonp = function (url, data, callback) {

        // 1.将传入的data数据转化为url字符串形式
        // 例子{id:1,name:'jack'} => id=1&name=jack
        var dataString = url.indexof('?') == -1? '?': '&';
        for(var key in data){
            dataString += key + '=' + data[key] + '&';
        };

        // 2 处理url中的回调函数
        // cbFuncName回调函数的名字 ：my_json_cb_名字的前缀 + 随机数（把小数点去掉）
        var cbFuncName = 'my_json_cb_' + Math.random().toString().replace('.','');
        dataString += 'callback=' + cbFuncName;

        // 3.创建一个script标签并插入到页面中
        var scriptEle = document.createElement('script');
        scriptEle.src = url + dataString;

        // 4.挂载回调函数
        window[cbFuncName] = function (data) {
            callback(data);
            // 处理完回调函数的数据之后，删除jsonp的script标签
            document.body.removeChild(scriptEle);
        }

        document.body.appendChild(scriptEle);
    }

    window.$jsonp = jsonp;

})(window,document)
```

### 简单实现

```js
function jsonp(url, jsonpCallback, success) {
    // 利用script属性
    let script = document.createElement('script');
    script.src = url;
    script.async = true;
    script.type = 'text/javascript';
    window[jsonpCallback] = function(data) {
    	success && success(data);
    }
    document.body.appendChild(script);
}
jsonp('http://xxx', 'callback', function(value) {
	console.log(value);
})
```

## 10.模拟实现promise

为什么用Promise？在传统的异步编程中，如果异步之间存在依赖关系，我们就需要通过层层嵌套回调来满足这种依赖，如果嵌套层数过多，可读性和可维护性都变得很差，产生所谓“回调地狱”，而Promise将回调嵌套改为链式调用，增加可读性和可维护性。

Promise有三种状态.，Promise一旦新建就立刻执行, 此时的状态是Pending(进行中),它接受两个参数分别是resolve和reject。它们是两个函数.
 resolve函数的作用是将Promise对象的状态从'未完成'变为'成功'(由Pending变为Resolved), 在异步操作成功时,将操作结果作为参数传递出去;
 reject函数的作用是将Promise对象的状态从'未完成'变为失败(由Pending变为Rejected),在异步操作失败时调用,并将异步操作的错误作为参数传递出去.

### 简单使用

```
// 使用
var promise = new Promise((resolve,reject) => {
    if (操作成功) {
        resolve(value)
    } else {
        reject(error)
    }
})
promise.then(function (value) {
    // success
},function (value) {
    // failure
})
```

### 基础版本

```js
function myPromise(constructor) {
    let self = this;
    self.status = "pending"   // 定义状态改变前的初始状态
    self.value = undefined;   // 定义状态为resolved的时候的状态
    self.reason = undefined;  // 定义状态为rejected的时候的状态
    function resolve(value) {
        if(self.status === "pending") {
            self.value = value;
            self.status = "resolved";
        }
    }
    function reject(reason) {
        if(self.status === "pending") {
            self.reason = reason;
            self.status = "rejected";
        }
    }
    // 捕获构造异常
    try {
    	constructor(resolve, reject);
    } catch(e) {
    	reject(e);
    }
}
```

### then方法

```
// 添加 then 方法
myPromise.prototype.then = function(onFullfilled, onRejected) {
   let self = this;
   switch(self.status) {
      case "resolved":
        onFullfilled(self.value);
        break;
      case "rejected":
        onRejected(self.reason);
        break;
      default:       
   }
}

var p = new myPromise(function(resolve,reject) {
    resolve(1)
});
p.then(function(x) {
    console.log(x) // 1
})
```

### catch方法

```
MyPromise.prototype.catch = function(onRejected) {
	return this.then(null, onRejected);
};
```

### finally方法

```
MyPromise.prototype.finally = function(fn) {
    return this.then(value => {
       fn();
       return value;
    }, reason => {
        fn();
        throw reason;
    });
};
```

### 面试够用版

#### promise

```
class Promise {
    constructor(fn) {
        //三个状态
        this.status = 'pending',
        this.resolve = undefined;
        this.reject = undefined;
        let resolve = value => {
            if (this.status === 'pending') {
                this.status = 'resolved';
                this.resolve = value;
            }
        };
        let reject = value => {
            if (this.status === 'pending') {
                this.status = 'rejected';
                this.reject = value;
            }
        }
        try {
            fn(resolve, reject)
        } catch (e) {
            reject(e)
        }
    };
    then(onResolved, onRejected) {
        switch (this.status) {
            case 'resolved': onResolved(this.resolve); break;
            case 'rejected': onRejected(this.resolve); break;
            default:
        }
    };
    catch(onRejected) {
        return this.then(null, onRejected);
    };
    finally (fn) {
        return this.then(value => {
           fn();
           return value;
        }, reason => {
            fn();
            throw reason;
        });
	}
}
```

#### Promise.all

Promise.all() 它接收一个promise对象组成的数组作为参数，并返回一个新的promise对象。

当数组中所有的对象都resolve时，新对象状态变为fulfilled，所有对象的resolve的value依次添加组成一个新的数组，并以新的数组作为新对象resolve的value。
当数组中有一个对象reject时，新对象状态变为rejected，并以当前对象reject的reason作为新对象reject的reason。

    Promise.prototype.all(promises) {
        if (!Array.isArray(promises)) {
        	throw new Error("promises must be an array")
        }
        return new Promise(function (resolve, reject) {
            let promsieNum = promises.length;
            let resolvedCount = 0;
            let resolveValues = new Array(promsieNum);
            for (let i = 0; i < promsieNum; i++) {
                Promise.resolve(promises[i].then(function (value) {
                        resolveValues[i] = value;
                        resolvedCount++;
                        if (resolvedCount === promsieNum) {
                            return resolve(resolveValues)
                        }
                    }, function (reason) {
                    	return reject(reason);
                    }
               ))
            }
        })
    }
#### Promise.race

Promise.race() 它同样接收一个promise对象组成的数组作为参数，并返回一个新的promise对象。

与Promise.all()不同，它是在数组中有一个对象（最早改变状态）resolve或reject时，就改变自身的状态，并执行响应的回调。

```
Promise.prototype.race(promises) {
    if (!Array.isArray(promises)) {
    	throw new Error("promises must be an array")
    }
    return new Promise(function (resolve, reject) {
        promises.forEach(p =>
            Promise.resolve(p).then(data => {
                resolve(data)
            }, err => {
                reject(err)
            })
        )
    })
}
```

## 11.手动实现ES5继承

Child继承Parent

### 原型继承

> 子类的原型指向父类。

```js
Child.prototype = new Parent();
```

缺点：原型是所有子类实例共享的，改变一个其他也会改变。

### 构造继承

> 在子类构造函数中调用父类构造函数

```js
function Child(name) {
    Parent.call(this);
}
```

缺点：不能继承父类原型，函数在构造函数中，每个子类实例不能共享函数，浪费内存。

### 组合继承

> 使用构造继承继承父类参数，使用原型继承继承父类函数

```js
function Child(name) {
    Parent.call(this);
}

Child.prototype = new Parent();
```

缺点：Parent的构造函数会多执行了一次（`Child.prototype = new Parent()`;）

### 寄生组合继承

> 将父类原型对象直接给到子类，父类构造函数只执行一次，而且父类属性和方法均能访问

```js
function Child(name) {
    Parent.call(this);
}

Child.prototype = Parent.prototype;
```

父类原型和子类原型是同一个对象，无法区分子类真正是由谁构造。

### 寄生组合继承优化

```js
function Child() {
    Parent.call(this);
}
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;
```

最推荐的一种方式，接近完美的继承。

## 12.手动实现instanceof

### 原理

```js
a instanceof Object
```

判断`Object`的prototype是否在`a`的原型链上。

### 代码实现

按照target原型链的向上查找，直到找到 origin 或 null

```js
function myInstanceof(target, origin) {
    let proto = target.__proto__;
    if (proto) {
        if (origin.prototype == proto) {
            return true;
        } else {
            return myInstanceof(proto, origin)
        }
    } else {
        return false
    }
}
```

改用循环而不是递归  

    // target instanceof origin
    // 变量origin的原型 存在于变量target的原型链上
    function myInstanceof(target, origin){    
        // 验证如果为基本数据类型，就直接返回false
        const baseType = ['string', 'number','boolean','undefined','symbol']
        if(baseType.includes(typeof(target))) return false;
        let oP  = origin.prototype;  // 取 origin 的显示原型
        proto = target.__proto__;       // 取 target 的隐式原型
        while(true){           // 无线循环的写法（也可以使 for(;;) ）
            if(proto === null){    // 找到最顶层
                return false;
            }
            if(proto === oP){       // 严格相等
                return true;
            }
            proto = proto.__proto__;  //没找到继续向上一层原型链查找
        }
    }
## 13.基于Promise的ajax封装

基于把原生`ajax`封装为`Promise`形式

```js
function ajax(url, method = 'get', param = {}) {
    return new Promise((resolve, reject) => {
        const xhr = new XMLHttpRequest();
        const paramString = getStringParam(param);
        if (method === 'get' && paramString) {
        	url.indexOf('?') > -1 ? url += paramString : url += `?${paramString}`
        }
        xhr.open(method, url);
        xhr.onload = function () {
            const result = {
                status: xhr.status,
                statusText: xhr.statusText,
                headers: xhr.getAllResponseHeaders(),
                data: xhr.response || xhr.responseText
            }
            if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
            	resolve(result);
            } else {
            	reject(result);
            }
        }
        // 设置请求头
        xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
        // 跨域携带cookie
        xhr.withCredentials = true;
        // 错误处理
        xhr.onerror = function () {
        	reject(new TypeError('请求出错'));
        }
        xhr.timeout = function () {
        	reject(new TypeError('请求超时'));
        }
        xhr.onabort = function () {
        	reject(new TypeError('请求被终止'));
        }
        if (method === 'post') {
        	xhr.send(paramString);
        } else {
        	xhr.send();
        }
    })
}

function getStringParam(param) {
    let dataString = '';
    for (const key in param) {
    	dataString += `${key}=${param[key]}&`
    }
    return dataString;
}
```

## 14.单例模式

在合适的时候才创建对像，并且只创建唯一的一个。创建对象和管理单例的职责被分布在两个不同的方法中，这两个方法组合起来才具有单例模式的威力。使用闭包实现：

```
function Singleton (name) {
	this.name = name;
};


Single.getInstance = (function(name) {
	let instance;
	return function(name) {
		if (!instance) {
			instance = new Singleton(name);
		}
		return instance;
	}
})();

var a = Singleton.getInstance('ConardLi');
var b = Singleton.getInstance('ConardLi2');

console.log(a === b);   //true
```

> 另一种实现方式，核心要点: 用闭包和Proxy属性拦截

```js
function proxy(func) {
    let instance;
    let handler = {
      	constructor (target, args) {
            if (!instance) {
                instance = Reflect.constructor(fun, args);
            }
            return instance;
        }  
    };
    return new Proxy(func, handler);
}
```

## 15.异步循环打印

使用`promise + async await`实现异步循环打印

```js
var sleep = function (time, i) {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            resolve(i);
        }, time);
    })
};


var start = async function () {
    for (let i = 0; i < 6; i++) {
        let result = await sleep(1000, i);
        console.log(result);
    }
};

start();
```

## 16.图片懒加载

### 监听图片高度

图片，用一个其他属性存储真正的图片地址：

```html
  <img src="loading.gif" data-src="https://cdn.pixabay.com/photo/2015/09/09/16/05/forest-931706_1280.jpg" alt="">
  <img src="loading.gif" data-src="https://cdn.pixabay.com/photo/2014/08/01/00/08/pier-407252_1280.jpg" alt="">
  <img src="loading.gif" data-src="https://cdn.pixabay.com/photo/2014/12/15/17/16/pier-569314_1280.jpg" alt="">
  <img src="loading.gif" data-src="https://cdn.pixabay.com/photo/2010/12/13/10/09/abstract-2384_1280.jpg" alt="">
  <img src="loading.gif" data-src="https://cdn.pixabay.com/photo/2015/10/24/11/09/drop-of-water-1004250_1280.jpg"
```

通过图片`offsetTop`和`window`的`innerHeight`，`scrollTop`判断图片是否位于可视区域。

```js
var img = document.getElementsByTagName("img");
var n = 0; //存储图片加载到的位置，避免每次都从第一张图片开始遍历
lazyload(); //页面载入完毕加载可是区域内的图片
// 节流函数，保证每200ms触发一次
function throttle(event, time) {
    let timer = null;
    return function (...args) {
        if (!timer) {
            timer = setTimeout(() => {
                timer = null;
                event.apply(this, args);
            }, time);
        }
    }
}
window.addEventListener('scroll', throttle(lazyload, 200))
function lazyload() { //监听页面滚动事件
    var seeHeight = window.innerHeight; //可见区域高度
    var scrollTop = document.documentElement.scrollTop || document.body.scrollTop; //滚动条距离顶部高度
    for (var i = n; i < img.length; i++) {
        console.log(img[i].offsetTop, seeHeight, scrollTop);
        if (img[i].offsetTop < seeHeight + scrollTop) {
            if (img[i].getAttribute("src") == "loading.gif") {
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
if (IntersectionObserver) {
    let lazyImageObserver = new IntersectionObserver((entries, observer) => {
        entries.forEach((entry, index) => {
            let lazyImage = entry.target;
            // 如果元素可见            
            if (entry.intersectionRatio > 0) {
                if (lazyImage.getAttribute("src") == "loading.gif") {
                    lazyImage.src = lazyImage.getAttribute("data-src");
                }
                lazyImageObserver.unobserve(lazyImage)
            }
        })
    })
    for (let i = 0; i < img.length; i++) {
        lazyImageObserver.observe(img[i]);
    }
}
```

## 17.模拟Object.create

> Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__

```js
// 模拟 Object.create
function create (proto) {
    function F() {}
    F.prototype = proto;

    return new F();
}
```

## 18.实现一个JSON.stringify

```js
JSON.stringify(value[, replacer [, space]])：
```

- `Boolean | Number| String`类型会自动转换成对应的原始值。
- `undefined`、任意函数以及`symbol`，会被忽略（出现在非数组对象的属性值中时），或者被转换成 `null`（出现在数组中时）。
- 不可枚举的属性会被忽略如果一个对象的属性值通过某种间接的方式指回该对象本身，即循环引用，属性也会被忽略
- 如果一个对象的属性值通过某种间接的方式指回该对象本身，即循环引用，属性也会被忽略

```js
function jsonStringify(obj) {
    let type = typeof obj;
    if (type !== "object") {
    	// 不是字符串 undefined 和 function 类型
        if (/string|undefined|function/.test(type)) {
            obj = '"' + obj + '"';
        }
        return String(obj);
    } else {
    	// JSON为空数组
        let json = []
        // 是否为数组
        let arr = Array.isArray(obj)
        for (let k in obj) {
            let v = obj[k];
            let type = typeof v;
            if (/string|undefined|function/.test(type)) {
                v = '"' + v + '"';
            } else if (type === "object") {
                v = jsonStringify(v);
            }
            json.push((arr ? "" : '"' + k + '":') + String(v));
        }
        return (arr ? "[" : "{") + String(json) + (arr ? "]" : "}")
    }
}
jsonStringify({x : 5}) // "{"x":5}"
jsonStringify([1, "false", false]) // "[1,"false",false]"
jsonStringify({b: undefined}) // "{"b":"undefined"}"
```

## 19.实现一个JSON.parse

```text
JSON.parse(text[, reviver])
```

> 用来解析JSON字符串，构造由字符串描述的JavaScript值或对象。提供可选的reviver函数用以在返回之前对所得到的对象执行变换(操作)

### 方法1：直接调用 eval

```
function jsonParse(opt) {
    return eval('(' + opt + ')');
}
jsonParse(jsonStringify({x : 5}))
// Object { x: 5}
jsonParse(jsonStringify([1, "false", false]))
// [1, "false", falsr]
jsonParse(jsonStringify({b: undefined}))
// Object { b: "undefined"}
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

### 方法2：Function

> 核心：Function与eval有相同的字符串参数特性

```text
var func = new Function(arg1, arg2, ..., functionBody);
```

在转换JSON的实际应用中，只需要这么做

```js
var jsonStr = '{ "age": 20, "name": "jack" }'
var json = (new Function('return ' + jsonStr))();
```

> `eval` 与 `Function`都有着动态编译js代码的作用，但是在实际的编程中并不推荐使用

## 20.解析 URL Params 为对象

```js
let url = 'http://www.domain.com/?user=anonymous&id=123&id=456&city=%E5%8C%97%E4%BA%AC&enabled';
parseParam(url)
/* 结果
{ user: 'anonymous',
  id: [ 123, 456 ], // 重复出现的 key 要组装成数组，能被转成数字的就转成数字类型
  city: '北京', // 中文需解码
  enabled: true, // 未指定值得 key 约定为 true
}
*/
```

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
```

## 21.模板引擎实现

underscore 提供了模板引擎的功能，举个例子：

```
var tpl = "hello: <%= name %>";

var compiled = _.template(tpl);
compiled({name: 'Kevin'}); // "hello: Kevin"
```

感觉好像没有什么强大的地方，再来举个例子：

在 HTML 文件中：

```
<ul id="name_list"></ul>

<script type="text/html" id="user_tmpl">
    <%for ( var i = 0; i < users.length; i++ ) { %>
        <li>
            <a href="<%=users[i].url%>">
                <%=users[i].name%>
            </a>
        </li>
    <% } %>
</script>
```

JavaScript 文件中：

```
var container = document.getElementById("name_list");

var data = {
    users: [
        { "name": "Kevin", "url": "http://localhost" },
        { "name": "Daisy", "url": "http://localhost" },
        { "name": "Kelly", "url": "http://localhost" }
    ]
}
var precompile = _.template(document.getElementById("user_tmpl").innerHTML);
var html = precompile(data);

container.innerHTML = html;
```

效果为：

![template](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210514224540.png)

那么该如何实现这样一个 _.template 函数呢？

### 实现思路

underscore 的 template 函数参考了 jQuery 的作者 John Resig 在 2008 年发表的一篇文章 [JavaScript Micro-Templating](https://johnresig.com/blog/javascript-micro-templating/#postcomment)，我们先从这篇文章的思路出发，思考一下如何写一个简单的模板引擎。

依然是以这段模板字符串为例：

```
<%for ( var i = 0; i < users.length; i++ ) { %>
    <li>
        <a href="<%=users[i].url%>">
            <%=users[i].name%>
        </a>
    </li>
<% } %>
```

John Resig 的思路是将这段代码转换为这样一段程序：

```
// 模拟数据
var users = [{"name": "Kevin", "url": "http://localhost"}];

var p = [];
for (var i = 0; i < users.length; i++) {
    p.push('<li><a href="');
    p.push(users[i].url);
    p.push('">');
    p.push(users[i].name);
    p.push('</a></li>');
}

// 最后 join 一下就可以得到最终拼接好的模板字符串
console.log(p.join('')) // <li><a href="http://localhost">Kevin</a></li>
```

我们注意，模板其实是一段字符串，我们怎么根据一段字符串生成一段代码呢？很容易就想到用 eval，那我们就先用 eval 吧。

然后我们会发现，为了转换成这样一段代码，我们需要将`<%xxx%>`转换为 `xxx`，其实就是去掉包裹的符号，还要将 `<%=xxx%>`转化成 `p.push(xxx)`，这些都可以用正则实现，但是我们还需要写 `p.push('<li><a href="');` 、`p.push('">');`呐，这些该如何实现呢？

那我们换个思路，依然是用正则，但是我们

1. 将 `%>` 替换成 `p.push('`
2. 将 `<%` 替换成 `');`
3. 将 `<%=xxx%>` 替换成 `');p.push(xxx);p.push('`

我们来举个例子：

```
<%for ( var i = 0; i < users.length; i++ ) { %>
    <li>
        <a href="<%=users[i].url%>">
            <%=users[i].name%>
        </a>
    </li>
<% } %>
```

按照这个替换规则会被替换为：

```
');for ( var i = 0; i < users.length; i++ ) { p.push('
    <li>
        <a href="');p.push(users[i].url);p.push('">
            ');p.push(users[i].name);p.push('
        </a>
    </li>
'); } p.push('
```

这样肯定会报错，毕竟代码都没有写全，我们在首和尾加上部分代码，变成：

```
// 添加的首部代码
var p = []; p.push('

');for ( var i = 0; i < users.length; i++ ) { p.push('
    <li>
        <a href="');p.push(users[i].url);p.push('">
            ');p.push(users[i].name);p.push('
        </a>
    </li>
'); } p.push('

// 添加的尾部代码
');
```

我们整理下这段代码：

```
var p = []; p.push('');
for ( var i = 0; i < users.length; i++ ) { 
    p.push('<li><a href="');
    p.push(users[i].url);
    p.push('">');
    p.push(users[i].name);
    p.push('</a></li>'); 
}
    p.push('');
```

恰好可以实现这个功能，不过还要注意一点，要将换行符替换成空格，防止解析成代码的时候报错，不过在这里为了方便理解原理，就只在代码里实现。

### 第一版

我们来尝试实现第一版：

```
// 第一版
function tmpl(str, data) {
    var str = document.getElementById(str).innerHTML;

    var string = "var p = []; p.push('" +
    str
    .replace(/[\r\t\n]/g, "")
    .replace(/<%=(.*?)%>/g, "');p.push($1);p.push('")
    .replace(/<%/g, "');")
    .replace(/%>/g,"p.push('")
    + "');"

    eval(string)

    return p.join('');
};
```

为了验证是否有用：

HTML 文件：

```
<script type="text/html" id="user_tmpl">
    <%for ( var i = 0; i < users.length; i++ ) { %>
        <li>
            <a href="<%=users[i].url%>">
                <%=users[i].name%>
            </a>
        </li>
    <% } %>
</script>
```

JavaScript 文件：

```
var users = [
    { "name": "Byron", "url": "http://localhost" },
    { "name": "Casper", "url": "http://localhost" },
    { "name": "Frank", "url": "http://localhost" }
]
tmpl("user_tmpl", users)
```

完整的 Demo 可以查看 [template 示例一](https://github.com/mqyqingfeng/Blog/tree/master/demos/template/template1)

### Function

在这里我们使用了 eval ，实际上 John Resig 在文章中使用的是 Function 构造函数。

Function 构造函数创建一个新的 Function 对象。 在 JavaScript 中, 每个函数实际上都是一个 Function 对象。

使用方法为：

```
new Function ([arg1[, arg2[, ...argN]],] functionBody)
```

arg1, arg2, ... argN 表示函数用到的参数，functionBody 表示一个含有包括函数定义的 JavaScript 语句的字符串。

举个例子：

```
var adder = new Function("a", "b", "return a + b");

adder(2, 6); // 8
```

那么 John Resig 到底是如何实现的呢？

### 第二版

使用 Function 构造函数：

```
// 第二版
function tmpl(str, data) {
    var str = document.getElementById(str).innerHTML;

    var fn = new Function("obj",

    "var p = []; p.push('" +

    str
    .replace(/[\r\t\n]/g, "")
    .replace(/<%=(.*?)%>/g, "');p.push($1);p.push('")
    .replace(/<%/g, "');")
    .replace(/%>/g,"p.push('")
    + "');return p.join('');");

    return fn(data);
};
```

使用方法依然跟第一版相同，具体 Demo 可以查看 [template 示例二](https://github.com/mqyqingfeng/Blog/tree/master/demos/template/template2)

不过值得注意的是：其实 tmpl 函数没有必要传入 data 参数，也没有必要在最后 return 的时候，传入 data 参数，即使你把这两个参数都去掉，代码还是可以正常执行的。

这是因为:

> 使用Function构造器生成的函数，并不会在创建它们的上下文中创建闭包；它们一般在全局作用域中被创建。当运行这些函数的时候，它们只能访问自己的本地变量和全局变量，不能访问Function构造器被调用生成的上下文的作用域。这和使用带有函数表达式代码的 eval 不同。

这里之所以依然传入了 data 参数，是为了下一版做准备。

### with

现在有一个小问题，就是实际上我们传入的数据结构可能比较复杂，比如：

```
var data = {
    status: 200,
    name: 'kevin',
    friends: [...]
}
```

如果我们将这个数据结构传入 tmpl 函数中，在模板字符串中，如果要用到某个数据，总是需要使用 `data.name`、`data.friends` 的形式来获取，麻烦就麻烦在我想直接使用 name、friends 等变量，而不是繁琐的使用 `data.` 来获取。

这又该如何实现的呢？答案是 with。

with 语句可以扩展一个语句的作用域链(scope chain)。当需要多次访问一个对象的时候，可以使用 with 做简化。比如：

```
var hostName = location.hostname;
var url = location.href;

// 使用 with
with(location){
    var hostname = hostname;
    var url = href;
}
function Person(){
    this.name = 'Kevin';
    this.age = '18';
}

var person = new Person();

with(person) {
    console.log('my name is ' + name + ', age is ' + age + '.')
}
// my name is Kevin, age is 18.
```

最后：不建议使用 with 语句，因为它可能是混淆错误和兼容性问题的根源，除此之外，也会造成性能低下

### 第三版

使用 with ，我们再写一版代码：

```
// 第三版
function tmpl(str, data) {
    var str = document.getElementById(str).innerHTML;

    var fn = new Function("obj",

    // 其实就是这里多添加了一句 with(obj){...}
    "var p = []; with(obj){p.push('" +

    str
    .replace(/[\r\t\n]/g, "")
    .replace(/<%=(.*?)%>/g, "');p.push($1);p.push('")
    .replace(/<%/g, "');")
    .replace(/%>/g,"p.push('")
    + "');}return p.join('');");

    return fn(data);
};
```

具体 Demo 可以查看 [template 示例三](https://github.com/mqyqingfeng/Blog/tree/master/demos/template/template3)

### 第四版

如果我们的模板不变，数据却发生了变化，如果使用我们的之前写的 tmpl 函数，每次都会 new Function，这其实是没有必要的，如果我们能在使用 tmpl 的时候，返回一个函数，然后使用该函数，传入不同的数据，只根据数据不同渲染不同的 html 字符串，就可以避免这种无谓的损失。

```
// 第四版
function tmpl(str, data) {
    var str = document.getElementById(str).innerHTML;
    var fn = new Function("obj",
    "var p = []; with(obj){p.push('" +
    str
    .replace(/[\r\t\n]/g, "")
    .replace(/<%=(.*?)%>/g, "');p.push($1);p.push('")
    .replace(/<%/g, "');")
    .replace(/%>/g,"p.push('")
    + "');}return p.join('');");

    var template = function(data) {
        return fn.call(this, data)
    }
    return template;
};

// 使用时
var compiled = tmpl("user_tmpl");
results.innerHTML = compiled(data);
```

具体 Demo 可以查看 [template 示例四](https://github.com/mqyqingfeng/Blog/tree/master/demos/template/template4)

### 反斜杠的作用

```
var txt = "We are the so-called "Vikings" from the north."
console.log(txt);
```

我们的本意是想打印带 `""` 包裹的 `Vikings` 字符串，但是在 JavaScript 中，字符串使用单引号或者双引号来表示起始或者结束，这段代码会报 `Unexpected identifier` 错误。

如果我们就是想要在字符串中使用单引号或者双引号呢？

我们可以使用反斜杠用来在文本字符串中插入省略号、换行符、引号和其他特殊字符：

```
var txt = "We are the so-called \"Vikings\" from the north."
console.log(txt);
```

现在 JavaScript 就可以输出正确的文本字符串了。

**这种由反斜杠后接字母或数字组合构成的字符组合就叫做“转义序列”。**

值得注意的是，转义序列会被视为单个字符。

我们常见的转义序列还有 `\n` 表示换行、`\t` 表示制表符、`\r` 表示回车等等。

### 转义序列

在 JavaScript 中，字符串值是一个由零或多个 Unicode 字符（字母、数字和其他字符）组成的序列。

字符串中的每个字符均可由一个转义序列表示。比如字母 `a`，也可以用转义序列 `\u0061` 表示。

> 转义序列以反斜杠 `\` 开头，它的作用是告知 JavaScript 解释器下一个字符是特殊字符。

> 转义序列的语法为 `\uhhhh`，其中 hhhh 是四位十六进制数。

根据这个规则，我们可以算出常见字符的转义序列，以字母 `m` 为例：

```
// 1. 求出字符 `m` 对应的 unicode 值
var unicode = 'm'.charCodeAt(0) // 109
// 2. 转成十六进制
var result = unicode.toString(16); // "6d"
```

我们就可以使用 `\u006d` 表示 `m`，不信你可以直接在浏览器命令行中直接输入字符串 `'\u006d'`，看下打印结果。

值得注意的是: `\n` 虽然也是一种转义序列，但是也可以使用上面的方式：

```
var unicode = '\n'.charCodeAt(0) // 10
var result = unicode.toString(16); // "a"
```

所以我们可以用 `\u000A` 来表示换行符 `\n`，比如在浏览器命令行中直接输入 `'a \n b'` 和 `'a \u000A b'` 效果是一样的。

讲了这么多，我们来看看一些常用字符的转义序列以及含义：

| Unicode 字符值 | 转义序列 | 含义       |
| -------------- | -------- | ---------- |
| \u0009         | \t       | 制表符     |
| \u000A         | \n       | 换行       |
| \u000D         | \r       | 回车       |
| \u0022         | \"       | 双引号     |
| \u0027         | \'       | 单引号     |
| \u005C         | \\       | 反斜杠     |
| \u2028         |          | 行分隔符   |
| \u2029         |          | 段落分隔符 |

### Line Terminators

Line Terminators，中文译文`行终结符`。像空白字符一样，`行终结符`可用于改善源文本的可读性。

在 ES5 中，有四个字符被认为是`行终结符`，其他的折行字符都会被视为空白。

这四个字符如下所示：

| 字符编码值 | 名称       |
| ---------- | ---------- |
| \u000A     | 换行符     |
| \u000D     | 回车符     |
| \u2028     | 行分隔符   |
| \u2029     | 段落分隔符 |

### Function

试想我们写这样一段代码，能否正确运行：

```
var log = new Function("var a = '1\t23';console.log(a)");
log()
```

答案是可以，那下面这段呢：

```
var log = new Function("var a = '1\n23';console.log(a)");
log()
```

答案是不可以，会报错 `Uncaught SyntaxError: Invalid or unexpected token`。

这是为什么呢？

这是因为在 Function 构造函数的实现中，首先会将函数体代码字符串进行一次 `ToString` 操作，这时候字符串变成了：

```
var a = '1
23';console.log(a)
```

然后再检测代码字符串是否符合代码规范，在 JavaScript 中，**字符串表达式中是不允许换行的**，这就导致了报错。

为了避免这个问题，我们需要将代码修改为：

```
var log = new Function("var a = '1\\n23';console.log(a)");
log()
```

其实不止 `\n`，其他三种 `行终结符`，如果你在字符串表达式中直接使用，都会导致报错！

之所以讲这个问题，是因为在模板引擎的实现中，就是使用了 Function 构造函数，如果我们在模板字符串中使用了 `行终结符`，便有可能会出现一样的错误，所以我们必须要对这四种 `行终结符` 进行特殊的处理。

### 特殊字符

除了这四种 `行终结符` 之外，我们还要对两个字符进行处理。

一个是 `\`。

比如说我们的模板内容中使用了`\`:

```
var log = new Function("var a = '1\23';console.log(a)");
log(); // 1
```

其实我们是想打印 '1\23'，但是因为把 `\` 当成了特殊字符的标记进行处理，所以最终打印了 1。

同样的道理，如果我们在使用模板引擎的时候，使用了 `\` 字符串，也会导致错误的处理。

第二个是 `'`。

如果我们在模板引擎中使用了 `'`，因为我们会拼接诸如 `p.push('` `')` 等字符串，因为 `'` 的原因，字符串会被错误拼接，也会导致错误。

所以总共我们需要对六种字符进行特殊处理，处理的方式，就是正则匹配出这些特殊字符，然后比如将 `\n` 替换成 `\\n`，`\` 替换成 `\\`，`'` 替换成 `\\'`，处理的代码为：

```
var escapes = {
    "'": "'",
    '\\': '\\',
    '\r': 'r',
    '\n': 'n',
    '\u2028': 'u2028',
    '\u2029': 'u2029'
};

var escapeRegExp = /\\|'|\r|\n|\u2028|\u2029/g;

var escapeChar = function(match) {
    return '\\' + escapes[match];
};
```

我们测试一下：

```
var str = 'console.log("I am \n Kevin");';
var newStr = str.replace(escapeRegExp, escapeChar);

eval(newStr)
// I am 
// Kevin
```

### replace

我们来讲一讲字符串的 replace 函数：

语法为：

```
str.replace(regexp|substr, newSubStr|function)
```

replace 的第一个参数，可以传一个字符串，也可以传一个正则表达式。

第二个参数，可以传一个新字符串，也可以传一个函数。

我们重点看下传入函数的情况，简单举一个例子：

```
var str = 'hello world';
var newStr = str.replace('world', function(match){
    return match + '!'
})
console.log(newStr); // hello world!
```

match 表示匹配到的字符串，但函数的参数其实不止有 match，我们看个更复杂的例子：

```
function replacer(match, p1, p2, p3, offset, string) {
    // match，表示匹配的子串 abc12345#$*%
    // p1，第 1 个括号匹配的字符串 abc
    // p2，第 2 个括号匹配的字符串 12345
    // p3，第 3 个括号匹配的字符串 #$*%
    // offset，匹配到的子字符串在原字符串中的偏移量 0
    // string，被匹配的原字符串 abc12345#$*%
    return [p1, p2, p3].join(' - ');
}
var newString = 'abc12345#$*%'.replace(/([^\d]*)(\d*)([^\w]*)/, replacer); // abc - 12345 - #$*%
```

另外要注意的是，如果第一个参数是正则表达式，并且其为全局匹配模式， 那么这个方法将被多次调用，每次匹配都会被调用。

举个例子，如果我们要在一段字符串中匹配出 `<%=xxx%>` 中的值：

```
var str = '<li><a href="<%=www.baidu.com%>"><%=baidu%></a></li>'

str.replace(/<%=(.+?)%>/g, function(match, p1, offset, string){
    console.log(match);
    console.log(p1);
    console.log(offset);
    console.log(string);
})
```

传入的函数会被执行两次，第一次的打印结果为：

```
<%=www.baidu.com%>
www.baidu.com
13
<li><a href="<%=www.baidu.com%>"><%=baidu%></a></li>
```

第二次的打印结果为：

```
<%=baidu%>
'baidu'
33
<li><a href="<%=www.baidu.com%>"><%=baidu%></a></li>
```

### 正则表达式的创建

当我们要建立一个正则表达式的时候，我们可以直接创建：

```
var reg = /ab+c/i;
```

也可以使用构造函数的方式：

```
new RegExp('ab+c', 'i');
```

值得一提的是：每个正则表达式对象都有一个 source 属性，返回当前正则表达式对象的模式文本的字符串：

```
var regex = /fooBar/ig;
console.log(regex.source); // "fooBar"，不包含 /.../ 和 "ig"。
```

### 正则表达式的特殊字符

正则表达式中有一些特殊字符，比如 `\d` 就表示了匹配一个数字，等价于 [0-9]。

在上节，我们使用 `/<%=(.+?)%>/g` 来匹配 `<%=xxx%>`，然而在 underscore 的实现中，用的却是 `/<%=([\s\S]+?)%>/g`。

我们知道 \s 表示匹配一个空白符，包括空格、制表符、换页符、换行符和其他 Unicode 空格，\S
匹配一个非空白符，[\s\S]就表示匹配所有的内容，可是为什么我们不直接使用 `.` 呢？

我们可能以为 `.` 匹配任意单个字符，实际上，并不是如此， `.`匹配除`行终结符`之外的任何单个字符，不信我们做个试验：

```
var str = '<%=hello world%>'

str.replace(/<%=(.+?)%>/g, function(match){
    console.log(match); // <%=hello world%>
})
```

但是如果我们在 hello world 之间加上一个`行终结符`，比如说 '\u2029'：

```
var str = '<%=hello \u2029 world%>'

str.replace(/<%=(.+?)%>/g, function(match){
    console.log(match);
})
```

因为匹配不到，所以也不会执行 console.log 函数。

但是改成 `/<%=([\s\S]+?)%>/g` 就可以正常匹配：

```
var str = '<%=hello \u2029 world%>'

str.replace(/<%=([\s\S]+?)%>/g, function(match){
    console.log(match); // <%=hello   world%>
})
```

### 惰性匹配

仔细看 `/<%=([\s\S]+?)%>/g` 这个正则表达式，我们知道 `x+` 表示匹配 `x` 1 次或多次。`x?`表示匹配 `x` 0 次或 1 次，但是 `+?` 是个什么鬼？

实际上，如果在数量词 *、+、? 或 {}, 任意一个后面紧跟该符号（?），会使数量词变为非贪婪（ non-greedy） ，即匹配次数最小化。反之，默认情况下，是贪婪的（greedy），即匹配次数最大化。

举个例子：

```
console.log("aaabc".replace(/a+/g, "d")); // dbc

console.log("aaabc".replace(/a+?/g, "d")); // dddbc
```

在这里我们应该使用非惰性匹配，举个例子：

```
var str = '<li><a href="<%=www.baidu.com%>"><%=baidu%></a></li>'

str.replace(/<%=(.+?)%>/g, function(match){
    console.log(match);
})

// <%=www.baidu.com%>
// <%=baidu%>
```

如果我们使用惰性匹配：

```
var str = '<li><a href="<%=www.baidu.com%>"><%=baidu%></a></li>'

str.replace(/<%=(.+)%>/g, function(match){
    console.log(match);
})

// <%=www.baidu.com%>"><%=baidu%>
```

### template

讲完需要的知识点，我们开始讲 underscore 模板引擎的实现。

与我们上篇使用数组的 push ，最后再 join 的方法不同，underscore 使用的是字符串拼接的方式。

比如下面这样一段模板字符串：

```
<%for ( var i = 0; i < users.length; i++ ) { %>
    <li>
        <a href="<%=users[i].url%>">
            <%=users[i].name%>
        </a>
    </li>
<% } %>
```

我们先将 `<%=xxx%>` 替换成 `'+ xxx +'`，再将 `<%xxx%>` 替换成 `'; xxx __p+='`:

```
';for ( var i = 0; i < users.length; i++ ) { __p+='
    <li>
        <a href="'+ users[i].url + '">
            '+ users[i].name +'
        </a>
    </li>
';  } __p+='
```

这段代码肯定会运行错误的，所以我们再添加些头尾代码，然后组成一个完整的代码字符串：

```
var __p='';
with(obj){
__p+='

';for ( var i = 0; i < users.length; i++ ) { __p+='
    <li>
        <a href="'+ users[i].url + '">
            '+ users[i].name +'
        </a>
    </li>
';  } __p+='

';
};
return __p;
```

整理下代码就是：

```
var __p='';
with(obj){
    __p+='';
    for ( var i = 0; i < users.length; i++ ) { 
        __p+='<li><a href="'+ users[i].url + '"> '+ users[i].name +'</a></li>';
    }
    __p+='';
};
return __p
```

然后我们将 `__p` 这段代码字符串传入 Function 构造函数中：

```
var render = new Function(data, __p)
```

我们执行这个 render 函数，传入需要的 data 数据，就可以返回一段 HTML 字符串：

```
render(data)
```

### 第五版 - 特殊字符的处理

我们接着上篇的[第四版](https://github.com/mqyqingfeng/Blog/tree/master/demos/template/template4)进行书写，不过加入对特殊字符的转义以及使用字符串拼接的方式：

```
// 第五版
var settings = {
    // 求值
    evaluate: /<%([\s\S]+?)%>/g,
    // 插入
    interpolate: /<%=([\s\S]+?)%>/g,
};

var escapes = {
    "'": "'",
    '\\': '\\',
    '\r': 'r',
    '\n': 'n',
    '\u2028': 'u2028',
    '\u2029': 'u2029'
};

var escapeRegExp = /\\|'|\r|\n|\u2028|\u2029/g;

var template = function(text) {

    var source = "var __p='';\n";
    source = source + "with(obj){\n"
    source = source + "__p+='";

    var main = text
    .replace(escapeRegExp, function(match) {
        return '\\' + escapes[match];
    })
    .replace(settings.interpolate, function(match, interpolate){
        return "'+\n" + interpolate + "+\n'"
    })
    .replace(settings.evaluate, function(match, evaluate){
        return "';\n " + evaluate + "\n__p+='"
    })

    source = source + main + "';\n }; \n return __p;";

    console.log(source)

    var render = new Function('obj',  source);

    return render;
};
```

完整的使用代码可以参考 [template 示例五](https://github.com/mqyqingfeng/Blog/tree/master/demos/template/template5)。

### 第六版 - 特殊值的处理

不过有一点需要注意的是：

如果数据中 `users[i].url` 不存在怎么办？此时取值的结果为 undefined，我们知道：

```
'1' + undefined // "1undefined"
```

就相当于拼接了 undefined 字符串，这肯定不是我们想要的。我们可以在代码中加入一点判断：

```
.replace(settings.interpolate, function(match, interpolate){
    return "'+\n" + (interpolate == null ? '' : interpolate) + "+\n'"
})
```

但是吧，我就是不喜欢写两遍 interpolate …… 嗯？那就这样吧：

```
var source = "var __t, __p='';\n";

...

.replace(settings.interpolate, function(match, interpolate){
    return "'+\n((__t=(" + interpolate + "))==null?'':__t)+\n'"
})
```

其实就相当于：

```
var __t;

var result = (__t = interpolate) == null ? '' : __t;
```

完整的使用代码可以参考 [template 示例六](https://github.com/mqyqingfeng/Blog/tree/master/demos/template/template6)。

### 第七版

现在我们使用的方式是将模板字符串进行多次替换，然而在 underscore 的实现中，只进行了一次替换，我们来看看 underscore 是怎么实现的：

```
var template = function(text) {
    var matcher = RegExp([
        (settings.interpolate).source,
        (settings.evaluate).source
    ].join('|') + '|$', 'g');

    var index = 0;
    var source = "__p+='";

    text.replace(matcher, function(match, interpolate, evaluate, offset) {
        source += text.slice(index, offset).replace(escapeRegExp, function(match) {
            return '\\' + escapes[match];
        });

        index = offset + match.length;

        if (interpolate) {
            source += "'+\n((__t=(" + interpolate + "))==null?'':__t)+\n'";
        } else if (evaluate) {
            source += "';\n" + evaluate + "\n__p+='";
        }

        return match;
    });

    source += "';\n";

    source = 'with(obj||{}){\n' + source + '}\n'

    source = "var __t, __p='';" +
        source + 'return __p;\n';

    var render = new Function('obj', source);

    return render;
};
```

其实原理也很简单，就是在执行多次匹配函数的时候，不断复制字符串，处理字符串，拼接字符串，最后拼接首尾代码，得到最终的代码字符串。

不过值得一提的是：在这段代码里，matcher 的表达式最后为：`/<%=([\s\S]+?)%>|<%([\s\S]+?)%>|$/g`

问题是为什么还要加个 `|$` 呢？我们来看下 $：

```
var str = "abc";
str.replace(/$/g, function(match, offset){
    console.log(typeof match) // 空字符串
    console.log(offset) // 3
    return match
})
```

我们之所以匹配 $，是为了获取最后一个字符串的位置，这样当我们 text.slice(index, offset)的时候，就可以截取到最后一个字符。

完整的使用代码可以参考 [template 示例七](https://github.com/mqyqingfeng/Blog/tree/master/demos/template/template7)。

### 最终版

其实代码写到这里，就已经跟 underscore 的实现很接近了，只是 underscore 加入了更多细节的处理，比如：

1. 对数据的转义功能
2. 可传入配置项
3. 对错误的处理
4. 添加 source 属性，以方便查看代码字符串
5. 添加了方便调试的 print 函数
6. ...

但是这些内容都还算简单，就不一版一版写了，最后的版本在 [template 示例八](https://github.com/mqyqingfeng/Blog/tree/master/demos/template/template8)，如果对其中有疑问，欢迎留言讨论

### 面试简化版

```
let template = '我是{{name}}，年龄{{age}}，性别{{sex}}';
let data = {
    name: '姓名',
    age: 18
}
render(template, data); // 我是姓名，年龄18，性别undefined
```

代码

```js
function render(template, data) {
     // 模板字符串正则
    const reg = /\{\{(\w+)\}\}/;
    // 判断模板里是否有模板字符串
    if (reg.test(template)) {
     	// 查找当前模板里第一个模板字符串的字段
        const name = reg.exec(template)[1];
        // 将第一个模板字符串渲染
        template = template.replace(reg, data[name]);
        // 递归的渲染并返回渲染后的结构
        return render(template, data);
    }
    // 如果模板没有模板字符串直接返回
    return template;
}
```

## 22.转化为驼峰命名

```js
var s1 = "get-element-by-id"
// 转化为 getElementById

// 把-后面的字母替换为大写字母
var f = function(s) {
    return s.replace(/-\w/g, function(x) {
        // 首字母大写
        return x.slice(1).toUpperCase();
    })
}

```

## 23.查找字符串中出现最多的字符和个数

```js
let str = "abcabcabcbbccccc";
let num = 0;
let char = '';
// 自己用哈希表
// 使其按照一定的次序排列
str = str.split('').sort().join('');
// "aaabbbbbcccccccc"

// 定义正则表达式
let re = /(\w)\1+/g;
str.replace(re, ($0, $1) => {
    if(num < $0.length){
        num = $0.length;
        char = $1;        
    }
});
console.log(`字符最多的是${char}，出现了${num}次`);
```

## 24.字符串查找

> 请使用最基本的遍历来实现判断字符串 a 是否被包含在字符串 b 中，并返回第一次出现的位置（找不到返回 -1）。

### 暴力解

```
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

### KMP

```js
var strStr = function(haystack, needle) {
    let k = -1, n = haystack.length, p = needle.length;
    if (p == 0) return 0;
    // -1表示不存在相同的最大前缀和后缀
    let next = Array(p).fill(-1);
    // 计算next数组
    calNext(needle, next);
    for (let i = 0; i < n; i++) {
        while (k > -1 && needle[k + 1] != haystack[i]) {
            // 有部分匹配，往前回溯
            k = next[k];
        }
        if (needle[k + 1] == haystack[i]) {
            k++;
        }
        if (k == p - 1) {
            // 说明k移动到needle的最末端，返回相应的位置
            return i - p + 1;
        }
    }
    return -1;
};

// 辅函数- 计算next数组
function calNext(needle, next) {
    for (let j = 1, p = -1; j < needle.length; j++) {
        while (p > -1 && needle[p + 1] != needle[j]) {
            // 如果下一位不同，往前回溯
            p = next[p];
        }
        if (needle[p + 1] == needle[j]) {
            // 如果下一位相同，更新相同的最大前缀和最大后缀长
            p++;
        }
        // 位置j处更新最长前缀
        next[j] = p;
    }
}
```

马拉车水平不够

## 25.实现千位分隔符

### 1.方法一

实现思路是将数字转换为字符数组，再循环整个数组， 每三位添加一个分隔逗号，最后再合并成字符串。因为分隔符在顺序上是从后往前添加的：比如 1234567添加后是1,234,567 而不是 123,456,7 ，所以方便起见可以先把数组倒序，添加完之后再倒序回来，就是正常的顺序了。要注意的是如果数字带小数的话，要把小数部分分开处理。

```swift
function numFormat(num) {
  // 按小数点分割
  num = num.toString().split(".");
  // 转换成字符数组并且倒序排列
  let arr = num[0].split("").reverse();
  let res = [];
  for (let i = 0; i < arr.length; i++) {
    if (i % 3 === 0 && i !== 0) {
      // 添加分隔符
      res.push(",");
    }
    res.push(arr[i]);
  }
  // 再次倒序成为正确的顺序
  res.reverse();
  // 如果有小数的话添加小数部分
  if (num[1]) {
    res = res.join("").concat("." + num[1]);
  } else {
    res = res.join("");
  }
  return res;
}

var a = 1234567894532;
var b = 673439.4542;
console.log(numFormat(a)); // "1,234,567,894,532"
console.log(numFormat(b)); // "673,439.4542"
```

### 2.方法二

使用JS自带的函数 [toLocaleString](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)

> 语法：  `numObj.toLocaleString([locales [, options]])`

`toLocaleString()` 方法返回这个数字在特定语言环境下的表示字符串。

```jsx
var a = 1234567894532;
var b = 673439.4542;

console.log(a.toLocaleString());  // "1,234,567,894,532"
console.log(b.toLocaleString());  // "673,439.454"  （小数部分四舍五入了）
```

要注意的是这个函数在没有指定区域的基本使用时，返回使用默认的语言环境和默认选项格式化的字符串，所以不同地区数字格式可能会有一定的差异。最好确保使用 locales 参数指定了使用的语言。
 注：我测试的环境下小数部分会根据四舍五入只留下三位。

### 3. 方法三

使用[正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)和[replace](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)函数，相对前两种我更喜欢这种方法，虽然正则有点难以理解。

> replace 语法：`str.replace(regexp|substr, newSubStr|function)`

其中第一个 [RegExp
 ](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/RegExp) 对象或者其字面量所匹配的内容会被第二个参数的返回值替换。

```jsx
function numFormat(num) {
  var res = num.toString().replace(/\d+/, function (n) {
    // 先提取整数部分
    return n.replace(/(\d)(?=(\d{3})+$)/g, function ($1) {
      return $1 + ",";
    });
  });
  return res;
}

var a = 1234567894532;
var b = 673439.4542;
console.log(numFormat(a)); // "1,234,567,894,532"
console.log(numFormat(b)); // "673,439.4542"
```

正则表达式

```
var thousandSeparator = function(n) {
    return (n + '').replace(/(?!^)(?=(\d{3})+$)/g, '.');
};
```

根据定义

```
var thousandSeparator = function(n) {
    let count = 0;
    let ans = "";
    do {
        let cur = n % 10;
        n = Math.floor(n / 10);
        ans += cur;
        count++;
        if (count % 3 == 0 && n) {
            ans += ".";
        }
    } while (n);
    return ans.split('').reverse().join('');
};
```

## 26.判断是否是电话号码

```
function isPhone(tel) {
    var regx = /^1[34578]\d{9}$/;
    return regx.test(tel);
}
```

## 27.验证是否是邮箱

```
function isEmail(email) {
    var regx = /^([a-zA-Z0-9_\-]+@([a-zA-Z0-9_\-]+)+$/;
    return regx.test(email);
}
```

##  28.用ES5实现数组的map方法

- 回调函数的参数有哪些，返回值如何处理
- 不修改原来的数组

```js
Array.prototype.MyMap = function(fn, context){
    // 转换类数组，由于是ES5所以就不用...展开符了
    let arr = Array.prototype.slice.call(this);
    let mappedArr = [];
    for (let i = 0; i < arr.length; i++) {
        // 把当前值、索引、当前数组返回去。调用的时候传到函数参数中 [1,2,3,4].map((curr,index,arr))
        mappedArr.push(fn.call(context, arr[i], i, this));
    }
    return mappedArr;
}
```

## 29.用ES5实现数组的reduce方法

- 初始值不传怎么处理
- 回调函数的参数有哪些，返回值如何处理。

```
Array.prototype.myReduce = function(fn, initialValue) {
    let arr = Array.prototpye.slice.call(this);
    let res, startIndex;
    // 不传默认取数组第一项
    res = initialValue ? initialValue : arr[0];
    startIndex = initialValue ? 0 : 1;
    for (let i = startIndex; i < arr.length; i++) {
    	// 把初始值、当前值、索引、当前数组返回去。调用的时候传到函数参数中 [1,2,3,4].reduce((initVal,curr,index,arr))
    	res = fn.call(null, res, arr[i], i, this);
    }
    return res;
}
```

- 对于普通函数，绑定this指向
- 对于构造函数，要保证原函数的原型对象上的属性不能丢失

## 30.请实现一个 add 函数，满足以下功能

```js
add(1); 			// 1
add(1)(2);  	// 3
add(1)(2)(3)；// 6
add(1)(2, 3); // 6
add(1, 2)(3); // 6
add(1, 2, 3); // 6
```

```js
function add() {
	let args = [].slice.call(arguments);
	
	let fn = function() {
		let fn_args = [].slice.call(arguments)
		return add.apply(null, args.concat(fn_args))
	}
	
	fn.toString = function() {
		returm args.reduce((a, b) => a + b)
	}
	
	return fn
}
```

## 31.实现一个 sleep 函数，比如 sleep(1000) 意味着等待1000毫秒

```
const sleep = (time) => {
	return new Promise(resolve => setTimeout(resolve, time))
}

sleep(1000).then(() => {
	// 这里写你的函数
})
```

## 32.实现 (5).add(3).minus(2) 功能

> 例： 5 + 3 - 2，结果为 6

```js
Number.prototype.add = function(n) {
    return this.valueOf() + n;
}
Number.prototype.minus = function(n) {
    return this.valueOf() - n;
}
```

## 33.实现一个双向绑定

### defineProperty 版本

```
// 数据
const data = {
	text: 'default'
};
const input  = document.getElementById('input');
const span  = document.getElementById('span');
// 数据劫持
Object.defineProperty(data, 'text', {
	// 数据变化 --> 修改视图
	set(newVal) {
		input.value = newVal;
		span.innerHTML = newVal;
	}
});
// 数据变化 --> 修改视图
input.addEventLisener('keyup', function(e) {
	data.text = e.target.value;
});
```

### proxy 版本

```
// 数据
const data = {
	text: 'default'
};
const input  = document.getElementById('input');
const span  = document.getElementById('span');
// 数据劫持
const handler = {
	set(target, key, value) {
		target[key] = value;
		// 数据变化 --> 修改视图
		input.value = newVal;
		span.innerHTML = newVal;
		return value;
	}
};
const proxy = new Proxy(data, handler);

// 视图更改 --> 数据变化
input.addEventLisener('keyup', function(e) {
	proxy.text = e.target.value;
});
```

## 34.Array.isArray 实现

```
Array.myIsArray = function(o) {
	return Object.prototype.toString.call(Object(o)) === '[object Array]';
}

console.log(Array.myIsArray([])); // true
```

## 35.实现一个函数判断数据类型

```js
function getType(obj) {
    if (obj === null) return String(obj);
    // 对象类型 "[object XXX]"
    return typeof obj === 'object' ? Object.prototype.toString.call(obj).replace('[object ', '').replace(']', '').toLowerCase() : typeof obj;
}

// 调用
getType(null); // -> null
getType(undefined); // -> undefined
getType({}); // -> object
getType([]); // -> array
getType(123); // -> number
getType(true); // -> boolean
getType('123'); // -> string
getType(/123/); // -> regexp
getType(new Date()); // -> date
```

## 36. 实现Event(event bus)

> event bus既是node中各个模块的基石，又是前端组件通信的依赖手段之一，同时涉及了订阅-发布设计模式，是非常重要的基础

**简单版：**

```js
class EventEmeitter {
  constructor() {
    this._events = this._events || new Map(); // 储存事件/回调键值对
    this._maxListeners = this._maxListeners || 10; // 设立监听上限
  }
}


// 触发名为type的事件
EventEmeitter.prototype.emit = function(type, ...args) {
  // 从储存事件键值对的this._events中获取对应事件回调函数
  let handler = this._events.get(type);
  if (args.length > 0) {
    handler.apply(this, args);
  } else {
    handler.call(this);
  }
  return true;
};

// 监听名为type的事件
EventEmeitter.prototype.addListener = function(type, fn) {
  // 将type事件以及对应的fn函数放入this._events中储存
  if (!this._events.get(type)) {
    this._events.set(type, fn);
  }
};
```

**面试版：**

```js
class EventEmeitter {
  constructor() {
    this._events = this._events || new Map(); // 储存事件/回调键值对
    this._maxListeners = this._maxListeners || 10; // 设立监听上限
  }
}

// 触发名为type的事件
EventEmeitter.prototype.emit = function(type, ...args) {
  let handler;
  handler = this._events.get(type);
  if (Array.isArray(handler)) {
    // 如果是一个数组说明有多个监听者,需要依次此触发里面的函数
    for (let i = 0; i < handler.length; i++) {
      if (args.length > 0) {
        handler[i].apply(this, args);
      } else {
        handler[i].call(this);
      }
    }
  } else {
    // 单个函数的情况我们直接触发即可
    if (args.length > 0) {
      handler.apply(this, args);
    } else {
      handler.call(this);
    }
  }

  return true;
};

// 监听名为type的事件
EventEmeitter.prototype.addListener = function(type, fn) {
  const handler = this._events.get(type); // 获取对应事件名称的函数清单
  if (!handler) {
    this._events.set(type, fn);
  } else if (handler && typeof handler === "function") {
    // 如果handler是函数说明只有一个监听者
    this._events.set(type, [handler, fn]); // 多个监听者我们需要用数组储存
  } else {
    handler.push(fn); // 已经有多个监听者,那么直接往数组里push函数即可
  }
};

EventEmeitter.prototype.removeListener = function(type, fn) {
  const handler = this._events.get(type); // 获取对应事件名称的函数清单

  // 如果是函数,说明只被监听了一次
  if (handler && typeof handler === "function") {
    this._events.delete(type, fn);
  } else {
    let postion;
    // 如果handler是数组,说明被监听多次要找到对应的函数
    for (let i = 0; i < handler.length; i++) {
      if (handler[i] === fn) {
        postion = i;
      } else {
        postion = -1;
      }
    }
    // 如果找到匹配的函数,从数组中清除
    if (postion !== -1) {
      // 找到数组对应的位置,直接清除此回调
      handler.splice(postion, 1);
      // 如果清除后只有一个函数,那么取消数组,以函数形式保存
      if (handler.length === 1) {
        this._events.set(type, handler[0]);
      }
    } else {
      return this;
    }
  }
};
```

##  37.模拟new

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

## 38.手写实现set和map

### 1.Set

ES6提供给我们的构造函数，能够造出一种新的存储数据的结构，只有属性值，成员值唯一（不重复）。

    class MySet{
        constructor(iterator){
            // 判断是否是可迭代对象
            if(typeof iterator[Symbol.iterator] !== "function"){
                throw new Error(`你提供的${iterator}不是一个课迭代的对象`);
            }
            this._datas = [];
            // 循环可迭代对象，将结果加入到set中
            for (const item of iterator) {
            	this.add(item);
            }
        }
        add(data){
            if(!this.has(data)){
            	this._datas.push(data);
            }
        }
        has(data){
            for (const item of this._datas) {
                if(this.isEqual(data,item)){
                	return true;
                }
            }
            return false;
        }
        delete(data){
            for (let i = 0; i < this._datas.length; i++) {
                const element = this._datas[i];
                if(this.isEqual(element, data)){
                    this._datas.splice(i,1);
                    return true;
                }
    
            }
            return false;
        }
        // 遍历
        *[Symbol.iterator](){
            for (const item of this._datas) {
                yield item;
            }
        }
        forEach(callback){
            for (const item of this._datas) {
                callback(item,ietm,this);
            }
        }
        clear(){
            this._datas.length = 0;
        }
    
        /**
         * 判断两个数据是否相等
         * @param {*} data1 
         * @param {*} data2 
         */
        isEqual(data1,data2){
            if(data1 === 0 && data2 === 0){
                return true;
            }
            return Object.is(data1,data2);
        }
    }
### 2.Map 

ES6提供给我们的构造函数，能够造出一种新的存储数据的结构。本质上是键值对的集合。key对应value，key和value唯一，任何值都可以当属性。

    class MyMap{
        constructor(iterable = []){
             //判断是否是可迭代对象
             if(typeof iterable[Symbol.iterator] !== "function"){
                throw new Error(`你提供的${iterable}不是一个课迭代的对象`);
            }
            this._datas = [];
            for (const item of iterable) {
                //item也是一个可迭代的对象
                if(typeof item[Symbol.iterator] !== "function"){
                    throw new Error(`你提供的${item}不是一个课迭代的对象`)
                }
                const iterator = item[Symbol.iterator]();
                const key = iterator.next().value;
                const value = iterator.next().value;
                this.set(key,value);
            }
        }
        set(key, value){
            //看里面有没有，如果有，则直接修改key对应的value值
            const obj = this._getObj(key);
            if(obj){
                //修改
                obj.value = value;
            }else{
                this._datas.push({
                    key,
                    value
                })
            }
    
        }
        get(key){
            const item = this._getObj(key);
            if(item){
                return item.value;
            }
            return undefined;
        }
        get_size(){
            return this._datas.length;
        }
        delete(key){
           for (let i = 0; i < this._datas.length; i++) {
               const element = this._datas[i];
               if(this.isEqual(element.key,key)){
                   this._datas.splice(i,1);
                   return true;
               }
           }
           return false;
        }
        clear(){
            this._datas.length = 0;
        }
        has(key){
            const item = this._getObj(key);
            return item !== undefined;
        }
        /**
         * 根据key值找到对应的数组项
         * @param {*} key 
         */
        _getObj(key){
            for (const item of this._datas) {
                if(this.isEqual(item.key,key)){
                    return item;
                }
            }
            return undefined;
        }
        isEqual(data1,data2){
            if(data1 === 0 && data2 === 0){
                return true;
            }
            return Object.is(data1,data2);
        }
        *[Symbol.iterator](){
            for (const item of this._datas) {
                yield[item.key,item.value];
            }
        }
        forEach(callback){
            for (const item of this._datas) {
                callback(item.value,item.key,this);
            }
        }
    }

