---
title:  HTML和浏览器
date:  2021-03-09 20:42:39
categories: 
- web前端
tags:
- HTML
- 浏览器
- 面试
---
HTML也是学习前端最开始学习的基础知识，虽然使用时强调实用性，但一些常见的面试考点还是要掌握的，浏览器的运行机制也需要进行了解，比如浏览器怎么加载页面的，对前端而言是必须掌握的。
<!-- more -->

## 1.行内元素和块级元素

### 1.1 什么叫行内元素，什么叫块级元素

#### 什么叫行内元素？

常见的span、a、lable、strong、b等html标签都是行内元素

#### 什么叫块级元素？

常见的div、p、li、h1、h2、h3、h4等html标签都是块级元素

### 1.2  行内元素和块级元素有哪些

#### 行内元素：

a, span, label, strong, em, br, img, input, select, textarea, cite,

#### 块级元：

div, h1~h6, p, form, ul, li, ol, dl, address, hr, menu, table, fieldset

### 1.3  行内元素和块级元素有哪些

#### （行内元素）内联元素(inline element)

- a - 锚点
- abbr - 缩写
- acronym - 首字
- b - 粗体(不推荐)
- bdo - bidi override
- big - 大字体
- br - 换行
- cite - 引用
- code - 计算机代码(在引用源码的时候需要)
- dfn - 定义字段
- em - 强调
- font - 字体设定(不推荐)
- i - 斜体
- img - 图片
- input - 输入框
- kbd - 定义键盘文本
- label - 表格标签
- q - 短引用
- s - 中划线(不推荐)
- samp - 定义范例计算机代码
- select - 项目选择
- small - 小字体文本
- span - 常用内联容器，定义文本内区块
- strike - 中划线
- strong - 粗体强调
- sub - 下标
- sup - 上标
- textarea - 多行文本输入框
- tt - 电传文本
- u - 下划线
- var - 定义变量

#### 块元素(block element)

- address - 地址
- blockquote - 块引用
- center - 举中对齐块
- dir - 目录列表
- div - 常用块级容易，也是css layout的主要标签
- dl - 定义列表
- fieldset - form控制组
- form - 交互表单
- h1 - 大标题
- h2 - 副标题
- h3 - 3级标题
- h4 - 4级标题
- h5 - 5级标题
- h6 - 6级标题
- hr - 水平分隔线
- isindex - input prompt
- menu - 菜单列表
- noframes - frames可选内容，（对于不支持frame的浏览器显示此区块内容
- noscript - ）可选脚本内容（对于不支持script的浏览器显示此内容）
- ol - 排序表单
- p - 段落
- pre - 格式化文本
- table - 表格
- ul - 非排序列表

#### 可变元素

可变元素为根据上下文语境决定该元素为块元素或者行内元素。

- applet - java applet
- button - 按钮
- del - 删除文本
- iframe - inline frame
- ins - 插入的文本
- map - 图片区块(map)
- object - object对象
- script - 客户端脚本

### 驾轻就熟：

#### 区别：

1. 块级元素会独占一行，其宽度自动填满其父元素宽度
   行内元素不会独占一行，相邻的行内元素会排列在同一行里，知道一行排不下，才会换行，其宽度随元素的内容而变化
2. 块级元素可以设置 width, height属性，【注意：块级元素即使设置了宽度，仍然是独占一行的】
   行内元素设置width, height无效;
3. 块级元素可以设置margin 和 padding.
   行内元素的水平方向的padding-left,padding-right,margin-left,margin-right 都产生边距效果，但是竖直方向的padding-top,padding-bottom,margin-top,margin-bottom都不会产生边距效果。（水平方向有效，竖直方向无效）

### 青出于蓝:

- 行内元素与块级元素直观上的区别
  行内元素会在一条直线上排列，都是同一行的，水平方向排列
  块级元素各占据一行，垂直方向排列。块级元素从新行开始结束接着一个断行。
- 块级元素可以包含行内元素和块级元素。行内元素不能包含块级元素。
- 行内元素与块级元素属性的不同，主要是盒模型属性上
  行内元素设置width无效，height无效(可以设置line-height)，margin上下无效，padding上下无效

```stylelint
     display：inline 行内元素/内联元素
     display: block 块级元素
     display:inline-block 设置成行内块级元素。
```

> 行内块级元素:和其他元素同一行（行内元素特点），可以设置元素的宽高等（块级元素特点）；这样的元素有img input；它们为行内元素，但可以改变宽和高；
> 但我在我印象中，貌似没有默认样式是inline-block的元素。

### 融会贯通：

- 行内元素属性
  1. 行内元素属性标签它和其它标签处在同一行内
  2. 行内元素属性标签无法设置宽度，高度 行高 距顶部距离 距底部距离
  3. 行内元素属性标签的宽度是直接由内部的文字或者图片等内容撑开的
  4. 行内元素属性标签内部不能嵌套行属性标签（a链接内不能嵌套其他链接）
- 块级元素属性
  1. 每一个块级元素属性标签都是从新的一行开始，而且之后的元素也都会从新的一行开始（因为每一个块属性标签都会直接占据一整行的内容，导致下面的内容也只能从新的一行开始）
  2. 块级元素属性标签都是可以设置宽度、高度，行高，距顶部距离，距底部距离
  3. 块级元素属性标签的宽度假如不做设置，会直接默认为父元素宽度的100%
  4. 块级元素属性标签是可以直接嵌套的
  5. p标签中不能嵌套div标签

### 出类拔萃：

- CSS设置行内元素的

  - 水平居中

    ```
    div{text-align:center} /*DIV内的行内元素均会水平居中*/ 
    ```

  - 垂直居中

    ```
    div{height:30px; line-height:30px} /*DIV内的行内元素均会垂直居中*/ 
    ```

- CSS设置块级元素的
  \- 水平居中
  `div p{margin:0 auto; width:500px} /*块级元素p一定要设置宽度， 才能相当于DIV父容器水平居中*/`
  \- 垂直居中
  `div{width:500px} /*DIV父容器设置宽度*/ div p{margin:0 aut0; height:30px; line-height:30px} /*块级元素p也可以加个宽度， 以达到相对于DIV父容器的水平居中效果*/`

> 在以后的实际项目中，块级元素的垂直居中布局方式可能会碰到比这个更复杂, 会尝试用inline-block去解决问题，希望后续多多关注；另外推荐各位一本书肖志华《CSS核心技术详解》

### 返璞归真：

在标准文档流里面，块级元素具有以下特点：

```
① 总是在新行上开始，占据一整行；
② 高度，行高以及外边距和内边距都可控制；
③ 宽带始终是与浏览器宽度一样，与内容无关；
④ 它可以容纳内联元素和其他块元素。
```

行内元素的特点：

```
① 和其他元素都在一行上；
② 高，行高及外边距和内边距部分可改变；
③ 宽度只与内容有关；
④ 行内元素只能容纳文本或者其他行内元素。
不可以设置宽高，其宽度随着内容增加，高度随字体大小而改变，内联元素可以设置外边界，但是外边界不对上下起作用，只能对左右起作用，也可以设置内边界，但是内边界在ie6中不对上下起作用，只能对左右起作用
```

# 2.跨页面通信

## 引言

在浏览器中，我们可以同时打开多个Tab页，每个Tab页可以粗略理解为一个“独立”的运行环境，即使是全局对象也不会在多个Tab间共享。然而有些时候，我们希望能在这些“独立”的Tab页面之间同步页面的数据、信息或状态。

正如下面这个例子：我在列表页点击“收藏”后，对应的详情页按钮会自动更新为“已收藏”状态；类似的，在详情页点击“收藏”后，列表页中按钮也会更新。

![跨页面通信实例](https://user-gold-cdn.xitu.io/2019/4/1/169d767c01990c37?imageslim)

这就是我们所说的前端跨页面通信。

你知道哪些跨页面通信的方式呢？如果不清楚，下面我就带大家来看看七种跨页面通信的方式。

------

## 一、同源页面间的跨页面通信

> 以下各种方式的 [在线 Demo 可以戳这里 >>](https://alienzhou.github.io/cross-tab-communication/)

浏览器的[同源策略](https://en.wikipedia.org/wiki/Same-origin_policy)在下述的一些跨页面通信方法中依然存在限制。因此，我们先来看看，在满足同源策略的情况下，都有哪些技术可以用来实现跨页面通信。

### 1. BroadCast Channel

[BroadCast Channel](https://developer.mozilla.org/en-US/docs/Web/API/BroadcastChannel) 可以帮我们创建一个用于广播的通信频道。当所有页面都监听同一频道的消息时，其中某一个页面通过它发送的消息就会被其他所有页面收到。它的API和用法都非常简单。

下面的方式就可以创建一个标识为`AlienZHOU`的频道：

```
const bc = new BroadcastChannel('AlienZHOU');
```

各个页面可以通过`onmessage`来监听被广播的消息：

```
bc.onmessage = function (e) {
    const data = e.data;
    const text = '[receive] ' + data.msg + ' —— tab ' + data.from;
    console.log('[BroadcastChannel] receive message:', text);
};
```

要发送消息时只需要调用实例上的`postMessage`方法即可：

```
bc.postMessage(mydata);
```

> Broadcast Channel 的具体的使用方式可以看这篇[《【3分钟速览】前端广播式通信：Broadcast Channel》](https://juejin.im/post/6844903811228663815)。

### 2. Service Worker

[Service Worker](https://developer.mozilla.org/zh-CN/docs/Web/API/Service_Worker_API) 是一个可以长期运行在后台的 Worker，能够实现与页面的双向通信。多页面共享间的 Service Worker 可以共享，将 Service Worker 作为消息的处理中心（中央站）即可实现广播效果。

> Service Worker 也是 PWA 中的核心技术之一，由于本文重点不在 PWA ，因此如果想进一步了解 Service Worker，可以阅读我之前的文章[【PWA学习与实践】(3) 让你的WebApp离线可用](https://juejin.im/post/6844903588691443725)。

首先，需要在页面注册 Service Worker：

```
/* 页面逻辑 */
navigator.serviceWorker.register('../util.sw.js').then(function () {
    console.log('Service Worker 注册成功');
});
```

其中`../util.sw.js`是对应的 Service Worker 脚本。Service Worker 本身并不自动具备“广播通信”的功能，需要我们添加些代码，将其改造成消息中转站：

```
/* ../util.sw.js Service Worker 逻辑 */
self.addEventListener('message', function (e) {
    console.log('service worker receive message', e.data);
    e.waitUntil(
        self.clients.matchAll().then(function (clients) {
            if (!clients || clients.length === 0) {
                return;
            }
            clients.forEach(function (client) {
                client.postMessage(e.data);
            });
        })
    );
});
```

我们在 Service Worker 中监听了`message`事件，获取页面（从 Service Worker 的角度叫 client）发送的信息。然后通过`self.clients.matchAll()`获取当前注册了该 Service Worker 的所有页面，通过调用每个client（即页面）的`postMessage`方法，向页面发送消息。这样就把从一处（某个Tab页面）收到的消息通知给了其他页面。

处理完 Service Worker，我们需要在页面监听 Service Worker 发送来的消息：

```
/* 页面逻辑 */
navigator.serviceWorker.addEventListener('message', function (e) {
    const data = e.data;
    const text = '[receive] ' + data.msg + ' —— tab ' + data.from;
    console.log('[Service Worker] receive message:', text);
});
```

最后，当需要同步消息时，可以调用 Service Worker 的`postMessage`方法：

```
/* 页面逻辑 */
navigator.serviceWorker.controller.postMessage(mydata);
```

### 3. LocalStorage

LocalStorage 作为前端最常用的本地存储，大家应该已经非常熟悉了；但[`StorageEvent`](https://developer.mozilla.org/en-US/docs/Web/API/StorageEvent)这个与它相关的事件有些同学可能会比较陌生。

当 LocalStorage 变化时，会触发`storage`事件。利用这个特性，我们可以在发送消息时，把消息写入到某个 LocalStorage 中；然后在各个页面内，通过监听`storage`事件即可收到通知。

```
window.addEventListener('storage', function (e) {
    if (e.key === 'ctc-msg') {
        const data = JSON.parse(e.newValue);
        const text = '[receive] ' + data.msg + ' —— tab ' + data.from;
        console.log('[Storage I] receive message:', text);
    }
});
```

在各个页面添加如上的代码，即可监听到 LocalStorage 的变化。当某个页面需要发送消息时，只需要使用我们熟悉的`setItem`方法即可：

```
mydata.st = +(new Date);
window.localStorage.setItem('ctc-msg', JSON.stringify(mydata));
```

注意，这里有一个细节：我们在mydata上添加了一个取当前毫秒时间戳的`.st`属性。这是因为，`storage`事件只有在值真正改变时才会触发。举个例子：

```
window.localStorage.setItem('test', '123');
window.localStorage.setItem('test', '123');
```

由于第二次的值`'123'`与第一次的值相同，所以以上的代码只会在第一次`setItem`时触发`storage`事件。因此我们通过设置`st`来保证每次调用时一定会触发`storage`事件。

### 小憩一下

上面我们看到了三种实现跨页面通信的方式，不论是建立广播频道的 Broadcast Channel，还是使用 Service Worker 的消息中转站，抑或是些 tricky 的`storage`事件，其都是“广播模式”：一个页面将消息通知给一个“中央站”，再由“中央站”通知给各个页面。

> 在上面的例子中，这个“中央站”可以是一个 BroadCast Channel 实例、一个 Service Worker 或是 LocalStorage。

下面我们会看到另外两种跨页面通信方式，我把它称为“共享存储+轮询模式”。

------

### 4. Shared Worker

[Shared Worker](https://developer.mozilla.org/en-US/docs/Web/API/SharedWorker) 是 Worker 家族的另一个成员。普通的 Worker 之间是独立运行、数据互不相通；而多个 Tab 注册的 Shared Worker 则可以实现数据共享。

Shared Worker 在实现跨页面通信时的问题在于，它无法主动通知所有页面，因此，我们会使用轮询的方式，来拉取最新的数据。思路如下：

让 Shared Worker 支持两种消息。一种是 post，Shared Worker 收到后会将该数据保存下来；另一种是 get，Shared Worker 收到该消息后会将保存的数据通过`postMessage`传给注册它的页面。也就是让页面通过 get 来主动获取（同步）最新消息。具体实现如下：

首先，我们会在页面中启动一个 Shared Worker，启动方式非常简单：

```
// 构造函数的第二个参数是 Shared Worker 名称，也可以留空
const sharedWorker = new SharedWorker('../util.shared.js', 'ctc');
```

然后，在该 Shared Worker 中支持 get 与 post 形式的消息：

```
/* ../util.shared.js: Shared Worker 代码 */
let data = null;
self.addEventListener('connect', function (e) {
    const port = e.ports[0];
    port.addEventListener('message', function (event) {
        // get 指令则返回存储的消息数据
        if (event.data.get) {
            data && port.postMessage(data);
        }
        // 非 get 指令则存储该消息数据
        else {
            data = event.data;
        }
    });
    port.start();
});
```

之后，页面定时发送 get 指令的消息给 Shared Worker，轮询最新的消息数据，并在页面监听返回信息：

```
// 定时轮询，发送 get 指令的消息
setInterval(function () {
    sharedWorker.port.postMessage({get: true});
}, 1000);

// 监听 get 消息的返回数据
sharedWorker.port.addEventListener('message', (e) => {
    const data = e.data;
    const text = '[receive] ' + data.msg + ' —— tab ' + data.from;
    console.log('[Shared Worker] receive message:', text);
}, false);
sharedWorker.port.start();
```

最后，当要跨页面通信时，只需给 Shared Worker `postMessage`即可：

```
sharedWorker.port.postMessage(mydata);
```

> 注意，如果使用`addEventListener`来添加 Shared Worker 的消息监听，需要显式调用`MessagePort.start`方法，即上文中的`sharedWorker.port.start()`；如果使用`onmessage`绑定监听则不需要。

### 5. IndexedDB

除了可以利用 Shared Worker 来共享存储数据，还可以使用其他一些“全局性”（支持跨页面）的存储方案。例如 [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) 或 cookie。

> 鉴于大家对 cookie 已经很熟悉，加之作为“互联网最早期的存储方案之一”，cookie 已经在实际应用中承受了远多于其设计之初的责任，我们下面会使用 IndexedDB 来实现。

其思路很简单：与 Shared Worker 方案类似，消息发送方将消息存至 IndexedDB 中；接收方（例如所有页面）则通过轮询去获取最新的信息。在这之前，我们先简单封装几个 IndexedDB 的工具方法。

- 打开数据库连接：

  function openStore() { const storeName = 'ctc_aleinzhou'; return new Promise(function (resolve, reject) { if (!('indexedDB' in window)) { return reject('don't support indexedDB'); } const request = indexedDB.open('CTC_DB', 1); request.onerror = reject; request.onsuccess =  e => resolve(e.target.result); request.onupgradeneeded = function (e) { const db = e.srcElement.result; if (e.oldVersion === 0 && !db.objectStoreNames.contains(storeName)) { const store = db.createObjectStore(storeName, {keyPath: 'tag'}); store.createIndex(storeName + 'Index', 'tag', {unique: false}); } } }); }

- 存储数据

  function saveData(db, data) { return new Promise(function (resolve, reject) { const STORE_NAME = 'ctc_aleinzhou'; const tx = db.transaction(STORE_NAME, 'readwrite'); const store = tx.objectStore(STORE_NAME); const request = store.put({tag: 'ctc_data', data}); request.onsuccess = () => resolve(db); request.onerror = reject; }); }

- 查询/读取数据

  function query(db) { const STORE_NAME = 'ctc_aleinzhou'; return new Promise(function (resolve, reject) { try { const tx = db.transaction(STORE_NAME, 'readonly'); const store = tx.objectStore(STORE_NAME); const dbRequest = store.get('ctc_data'); dbRequest.onsuccess = e => resolve(e.target.result); dbRequest.onerror = reject; } catch (err) { reject(err); } }); }

剩下的工作就非常简单了。首先打开数据连接，并初始化数据：

```
openStore().then(db => saveData(db, null))
```

对于消息读取，可以在连接与初始化后轮询：

```
openStore().then(db => saveData(db, null)).then(function (db) {
    setInterval(function () {
        query(db).then(function (res) {
            if (!res || !res.data) {
                return;
            }
            const data = res.data;
            const text = '[receive] ' + data.msg + ' —— tab ' + data.from;
            console.log('[Storage I] receive message:', text);
        });
    }, 1000);
});
```

最后，要发送消息时，只需向 IndexedDB 存储数据即可：

```
openStore().then(db => saveData(db, null)).then(function (db) {
    // …… 省略上面的轮询代码
    // 触发 saveData 的方法可以放在用户操作的事件监听内
    saveData(db, mydata);
});
```

### 小憩一下

在“广播模式”外，我们又了解了“共享存储+长轮询”这种模式。也许你会认为长轮询没有监听模式优雅，但实际上，有些时候使用“共享存储”的形式时，不一定要搭配长轮询。

例如，在多 Tab 场景下，我们可能会离开 Tab A 到另一个 Tab B 中操作；过了一会我们从 Tab B 切换回 Tab A 时，希望将之前在 Tab B 中的操作的信息同步回来。这时候，其实只用在 Tab A 中监听`visibilitychange`这样的事件，来做一次信息同步即可。

下面，我会再介绍一种通信方式，我把它称为“口口相传”模式。

------

### 6. window.open + window.opener

当我们使用`window.open`打开页面时，方法会返回一个被打开页面`window`的引用。而在未显示指定`noopener`时，被打开的页面可以通过`window.opener`获取到打开它的页面的引用 —— 通过这种方式我们就将这些页面建立起了联系（一种树形结构）。

首先，我们把`window.open`打开的页面的`window`对象收集起来：

```
let childWins = [];
document.getElementById('btn').addEventListener('click', function () {
    const win = window.open('./some/sample');
    childWins.push(win);
});
```

然后，当我们需要发送消息的时候，作为消息的发起方，一个页面需要同时通知它打开的页面与打开它的页面：

```
// 过滤掉已经关闭的窗口
childWins = childWins.filter(w => !w.closed);
if (childWins.length > 0) {
    mydata.fromOpenner = false;
    childWins.forEach(w => w.postMessage(mydata));
}
if (window.opener && !window.opener.closed) {
    mydata.fromOpenner = true;
    window.opener.postMessage(mydata);
}
```

注意，我这里先用`.closed`属性过滤掉已经被关闭的 Tab 窗口。这样，作为消息发送方的任务就完成了。下面看看，作为消息接收方，它需要做什么。

此时，一个收到消息的页面就不能那么自私了，除了展示收到的消息，它还需要将消息再传递给它所“知道的人”（打开与被它打开的页面）:

> 需要注意的是，我这里通过判断消息来源，避免将消息回传给发送方，防止消息在两者间死循环的传递。（该方案会有些其他小问题，实际中可以进一步优化）

```
window.addEventListener('message', function (e) {
    const data = e.data;
    const text = '[receive] ' + data.msg + ' —— tab ' + data.from;
    console.log('[Cross-document Messaging] receive message:', text);
    // 避免消息回传
    if (window.opener && !window.opener.closed && data.fromOpenner) {
        window.opener.postMessage(data);
    }
    // 过滤掉已经关闭的窗口
    childWins = childWins.filter(w => !w.closed);
    // 避免消息回传
    if (childWins && !data.fromOpenner) {
        childWins.forEach(w => w.postMessage(data));
    }
});
```

这样，每个节点（页面）都肩负起了传递消息的责任，也就是我说的“口口相传”，而消息就在这个树状结构中流转了起来。

### 小憩一下

显然，“口口相传”的模式存在一个问题：如果页面不是通过在另一个页面内的`window.open`打开的（例如直接在地址栏输入，或从其他网站链接过来），这个联系就被打破了。

除了上面这六个常见方法，其实还有一种（第七种）做法是通过 WebSocket 这类的“服务器推”技术来进行同步。这好比将我们的“中央站”从前端移到了后端。

关于 WebSocket 与其他“服务器推”技术，不了解的同学可以阅读这篇[《各类“服务器推”技术原理与实例（Polling/COMET/SSE/WebSocket）》](https://juejin.im/post/6844903618043183111)

此外，我还针对以上各种方式写了一个 [在线演示的 Demo >>](https://alienzhou.github.io/cross-tab-communication/)

![Demo页面](data:image/svg+xml;utf8,<?xml version="1.0"?><svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="1280" height="567"></svg>)

## 二、非同源页面之间的通信

上面我们介绍了七种前端跨页面通信的方法，但它们大都受到同源策略的限制。然而有时候，我们有两个不同域名的产品线，也希望它们下面的所有页面之间能无障碍地通信。那该怎么办呢？

要实现该功能，可以使用一个用户不可见的 iframe 作为“桥”。由于 iframe 与父页面间可以通过指定`origin`来忽略同源限制，因此可以在每个页面中嵌入一个 iframe （例如：`http://sample.com/bridge.html`），而这些 iframe 由于使用的是一个 url，因此属于同源页面，其通信方式可以复用上面第一部分提到的各种方式。

页面与 iframe 通信非常简单，首先需要在页面中监听 iframe 发来的消息，做相应的业务处理：

```
/* 业务页面代码 */
window.addEventListener('message', function (e) {
    // …… do something
});
```

然后，当页面要与其他的同源或非同源页面通信时，会先给 iframe 发送消息：

```
/* 业务页面代码 */
window.frames[0].window.postMessage(mydata, '*');
```

其中为了简便此处将`postMessage`的第二个参数设为了`'*'`，你也可以设为 iframe 的 URL。iframe 收到消息后，会使用某种跨页面消息通信技术在所有 iframe 间同步消息，例如下面使用的 Broadcast Channel：

```
/* iframe 内代码 */
const bc = new BroadcastChannel('AlienZHOU');
// 收到来自页面的消息后，在 iframe 间进行广播
window.addEventListener('message', function (e) {
    bc.postMessage(e.data);
});
```

其他 iframe 收到通知后，则会将该消息同步给所属的页面：

```
/* iframe 内代码 */
// 对于收到的（iframe）广播消息，通知给所属的业务页面
bc.onmessage = function (e) {
    window.parent.postMessage(e.data, '*');
};
```

下图就是使用 iframe 作为“桥”的非同源页面间通信模式图。

![img](https://user-gold-cdn.xitu.io/2019/3/31/169d468988a6ba8f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

其中“同源跨域通信方案”可以使用文章第一部分提到的某种技术。

## 总结

今天和大家分享了一下跨页面通信的各种方式。

对于同源页面，常见的方式包括：

- 广播模式：Broadcast Channe / Service Worker / LocalStorage + StorageEvent
- 共享存储模式：Shared Worker / IndexedDB / cookie
- 口口相传模式：window.open + window.opener
- 基于服务端：Websocket / Comet / SSE 等

而对于非同源页面，则可以通过嵌入同源 iframe 作为“桥”，将非同源页面通信转换为同源页面通信。

# 3.前端路由的两种模式：hash模式和 history模式

## 为什么要使用路由

现在的网络应用程序越来越多的使用AJAX异步请求完成页面的无缝刷新，导致浏览器的URL不会发生任何变化而完成了请求，从而提高了用户浏览体验。同时本次浏览的页面内容在用户下次使用URL访问时将无法重新呈现，使用路由可以很好地解决这个问题。

单页面应用利用了JavaScript动态变换网页内容，避免了页面重载；路由则提供了浏览器地址变化，网页内容也跟随变化，两者结合起来则为我们提供了体验良好的单页面web应用。

## 前端路由实现方式

路由需要实现三个功能：

1. 当浏览器地址变化时，切换页面；
2. 点击浏览器【后退】、【前进】按钮，网页内容跟随变化；
3. 刷新浏览器，网页加载当前路由对应内容；

在单页面web网页中, 单纯的浏览器地址改变, 网页不会重载，如单纯的hash网址改变网页不会变化，因此我们的路由主要是通过监听事件，并利用js实现动态改变网页内容，有两种实现方式：

- hash模式：监听浏览器地址hash值变化，执行相应的js切换网页；
- history模式：利用history API实现url地址改变，网页内容改变；

它们的区别最明显的就是hash会在浏览器地址后面增加#号，而history可以自定义地址。

## hash模式

使用window.location.hash属性及窗口的onhashchange事件，可以实现监听浏览器地址hash值变化，执行相应的js切换网页。下面具体介绍几个使用过程中必须理解的要点：

1. hash指的是地址中#号以及后面的字符，也称为散列值。hash也称作锚点，本身是用来做页面跳转定位的。如http://localhost/index.html#abc，这里的#abc就是hash；
2. 散列值是不会随请求发送到服务器端的，所以改变hash，不会重新加载页面；
3. 监听 window 的 hashchange 事件，当散列值改变时，可以通过 location.hash 来获取和设置hash值；
4. location.hash值的变化会直接反应到浏览器地址栏；

## 触发hashchange事件的几种情况：

- 浏览器地址栏散列值的变化（包括浏览器的前进、后退）会触发window.location.hash值的变化，从而触发onhashchange事件；
- 当浏览器地址栏中URL包含哈希如 http://www.baidu.com/#home，这时按下输入，浏览器发送http://www.baidu.com/请求至服务器，请求完毕之后设置散列值为#home，进而触发onhashchange事件；
- 当只改变浏览器地址栏URL的哈希部分，这时按下回车，浏览器不会发送任何请求至服务器，这时发生的只是设置散列值新修改的哈希值，并触发onhashchange事件；
- html中<a>标签的属性 href 可以设置为页面的元素ID如 #top，当点击该链接时页面跳转至该id元素所在区域，同时浏览器自动设置 window.location.hash 属性，地址栏中的哈希值也会发生改变，并触发onhashchange事件；

```
//设置 url 的 hash，会在当前url后加上'#abc'
window.location.hash='abc';
let hash = window.location.hash //'#abc'

window.addEventListener('hashchange',function(){
	//监听hash变化，点击浏览器的前进后退会触发
})
```

## history模式

概述
window.history 属性指向 History 对象，它表示当前窗口的浏览历史。当发生改变时，只会改变页面的路径，不会刷新页面。
History 对象保存了当前窗口访问过的所有页面网址。通过 history.length 可以得出当前窗口一共访问过几个网址。
由于安全原因，浏览器不允许脚本读取这些地址，但是允许在地址之间导航。
浏览器工具栏的“前进”和“后退”按钮，其实就是对 History 对象进行操作。
属性
History 对象主要有两个属性。

```
History.length：当前窗口访问过的网址数量（包括当前网页）
History.state：History 堆栈最上层的状态值（详见下文）
// 当前窗口访问过多少个网页
history.length // 1

// History 对象的当前状态
// 通常是 undefined，即未设置
history.state // undefined
```


方法
History.back()、History.forward()、History.go()
这三个方法用于在历史之中移动。

方法
History.back()、History.forward()、History.go()
这三个方法用于在历史之中移动。

History.back()：移动到上一个网址，等同于点击浏览器的后退键。对于第一个访问的网址，该方法无效果。
History.forward()：移动到下一个网址，等同于点击浏览器的前进键。对于最后一个访问的网址，该方法无效果。
History.go()：接受一个整数作为参数，以当前网址为基准，移动到参数指定的网址。如果参数超过实际存在的网址范围，该方法无效果；如果不指定参数，默认参数为0，相当于刷新当前页面。

```
history.back();
history.forward();
history.go(1);//相当于history.forward()
history.go(-1);//相当于history.back()
history.go(0); // 刷新当前页面
```


注意：移动到以前访问过的页面时，页面通常是从浏览器缓存之中加载，而不是重新要求服务器发送新的网页。


注意：移动到以前访问过的页面时，页面通常是从浏览器缓存之中加载，而不是重新要求服务器发送新的网页。

History.pushState()
该方法用于在历史中添加一条记录。pushState()方法不会触发页面刷新，只是导致 History 对象发生变化，地址栏会有变化。

语法：history.pushState(object, title, url)

该方法接受三个参数，依次为：

object：是一个对象，通过 pushState 方法可以将该对象内容传递到新页面中。如果不需要这个对象，此处可以填 null。
title：指标题，几乎没有浏览器支持该参数，传一个空字符串比较安全。
url：新的网址，必须与当前页面处在同一个域。不指定的话则为当前的路径，如果设置了一个跨域网址，则会报错。

```
var data = { foo: 'bar' };
history.pushState(data, '', '2.html');
console.log(history.state) // {foo: "bar"}
```

注意：如果 pushState 的 URL 参数设置了一个新的锚点值（即 hash），并不会触发 hashchange 事件。反过来，如果 URL 的锚点值变了，则会在 History 对象创建一条浏览记录。

注意：如果 pushState 的 URL 参数设置了一个新的锚点值（即 hash），并不会触发 hashchange 事件。反过来，如果 URL 的锚点值变了，则会在 History 对象创建一条浏览记录。

如果 pushState() 方法设置了一个跨域网址，则会报错。

```
// 报错
// 当前网址为 http://example.com
history.pushState(null, '', 'https://twitter.com/hello');
```


上面代码中，pushState 想要插入一个跨域的网址，导致报错。这样设计的目的是，防止恶意代码让用户以为他们是在另一个网站上，因为这个方法不会导致页面跳转。

History.replaceState()
该方法用来修改 History 对象的当前记录，用法与 pushState() 方法一样。

假定当前网页是 example.com/example.html。

```
history.pushState({page: 1}, '', '?page=1')
// URL 显示为 http://example.com/example.html?page=1

history.pushState({page: 2}, '', '?page=2');
// URL 显示为 http://example.com/example.html?page=2

history.replaceState({page: 3}, '', '?page=3');
// URL 显示为 http://example.com/example.html?page=3

history.back()
// URL 显示为 http://example.com/example.html?page=1

history.back()
// URL 显示为 http://example.com/example.html

history.go(2)
// URL 显示为 http://example.com/example.html?page=3
```


popstate 事件
每当 history 对象出现变化时，就会触发 popstate 事件。

注意：

仅仅调用pushState()方法或replaceState()方法 ，并不会触发该事件;
只有用户点击浏览器倒退按钮和前进按钮，或者使用 JavaScript 调用History.back()、History.forward()、History.go()方法时才会触发。
另外，该事件只针对同一个文档，如果浏览历史的切换，导致加载不同的文档，该事件也不会触发。
页面第一次加载的时候，浏览器不会触发popstate事件。
使用的时候，可以为popstate事件指定回调函数，回调函数的参数是一个 event 事件对象，它的 state 属性指向当前的 state 对象。

```
window.addEventListener('popstate', function(e) {
	//e.state 相当于 history.state
	console.log('state: ' + JSON.stringify(e.state));
	console.log(history.state);
});
```


点击查看 通过history.pushState 实现页面 tab 切换的功能。

history 致命的缺点就是当改变页面地址后，强制刷新浏览器时，（如果后端没有做准备的话）会报错，因为刷新是拿当前地址去请求服务器的，如果服务器中没有相应的响应，会出现 404 页面。



## 4.DOM树：JavaScript是如何影响DOM树构建的

续沿着网络数据流路径来介绍 DOM 树是怎么生成的。然后再基于 DOM 树的解析流程介绍两块内容：第一个是在解析过程中遇到 JavaScript 脚本，DOM 解析器是如何处理的？第二个是 DOM 解析器是如何处理跨站点资源的？

### 4.1 什么是 DOM

从网络传给渲染引擎的 HTML 文件字节流是无法直接被渲染引擎理解的，所以要将其转化为渲染引擎能够理解的内部结构，这个结构就是 DOM。DOM 提供了对 HTML 文档结构化的表述。在渲染引擎中，DOM 有三个层面的作用

- 从页面的视角来看，DOM 是生成页面的基础数据结构。
- 从 JavaScript 脚本视角来看，DOM 提供给 JavaScript 脚本操作的接口，通过这套接口，JavaScript 可以对 DOM 结构进行访问，从而改变文档的结构、样式和内容。
- 从安全视角来看，DOM 是一道安全防护线，一些不安全的内容在 DOM 解析阶段就被拒之门外了。

简言之，DOM 是表述 HTML 的内部数据结构，它会将 Web 页面和 JavaScript 脚本连接起来，并过滤一些不安全的内容。

### 4.2 DOM 树如何生成

在渲染引擎内部，有一个叫HTML 解析器（HTMLParser）的模块，它的职责就是负责将 HTML 字节流转换为 DOM 结构。所以这里我们需要先要搞清楚 HTML 解析器是怎么工作的。

在开始介绍 HTML 解析器之前，我要先解释一个大家在留言区问到过好多次的问题：HTML 解析器是等整个 HTML 文档加载完成之后开始解析的，还是随着 HTML 文档边加载边解析的？

在这里我统一解答下，HTML 解析器并不是等整个文档加载完成之后再解析的，而是网络进程加载了多少数据，HTML 解析器便解析多少数据。

那详细的流程是怎样的呢？网络进程接收到响应头之后，会根据响应头中的 content-type 字段来判断文件的类型，比如 content-type 的值是“text/html”，那么浏览器就会判断这是一个 HTML 类型的文件，然后为该请求选择或者创建一个渲染进程。渲染进程准备好之后，网络进程和渲染进程之间会建立一个共享数据的管道，网络进程接收到数据后就往这个管道里面放，而渲染进程则从管道的另外一端不断地读取数据，并同时将读取的数据“喂”给 HTML 解析器。你可以把这个管道想象成一个“水管”，网络进程接收到的字节流像水一样倒进这个“水管”，而“水管”的另外一端是渲染进程的 HTML 解析器，它会动态接收字节流，并将其解析为 DOM。

解答完这个问题之后，接下来我们就可以来详细聊聊 DOM 的具体生成流程了。

前面我们说过代码从网络传输过来是字节流的形式，那么后续字节流是如何转换为 DOM 的呢？你可以参考下图：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210528223544.png)

从图中你可以看出，字节流转换为 DOM 需要三个阶段。

**第一个阶段，通过分词器将字节流转换为 Token。**

前面《14 | 编译器和解释器：V8 是如何执行一段 JavaScript 代码的？》文章中我们介绍过，V8 编译 JavaScript 过程中的第一步是做词法分析，将 JavaScript 先分解为一个个 Token。解析 HTML 也是一样的，需要通过分词器先将字节流转换为一个个 Token，分为 Tag Token 和文本 Token。上述 HTML 代码通过词法分析生成的 Token 如下所示：

![img](http://blog.poetries.top/img-repo/2019/11/58.png)

由图可以看出，Tag Token 又分 StartTag 和 EndTag，比如

就是 StartTag ，就是EndTag，分别对于图中的蓝色和红色块，文本 Token 对应的绿色块。



**至于后续的第二个和第三个阶段是同步进行的，需要将 Token 解析为 DOM 节点，并将 DOM 节点添加到 DOM 树中**。

> HTML 解析器维护了一个Token 栈结构，该 Token 栈主要用来计算节点之间的父子关系，在第一个阶段中生成的 Token 会被按照顺序压到这个栈中。具体的处理规则如下所示：

- 如果压入到栈中的是StartTag Token，HTML 解析器会为该 Token 创建一个 DOM 节点，然后将该节点加入到 DOM 树中，它的父节点就是栈中相邻的那个元素生成的节点。
- 如果分词器解析出来是文本 Token，那么会生成一个文本节点，然后将该节点加入到 DOM 树中，文本 Token 是不需要压入到栈中，它的父节点就是当前栈顶 Token 所对应的 DOM 节点。
- 如果分词器解析出来的是EndTag 标签，比如是 EndTag div，HTML 解析器会查看 Token 栈顶的元素是否是 StarTag div，如果是，就将 StartTag div 从栈中弹出，表示该 div 元素解析完成。

通过分词器产生的新 Token 就这样不停地压栈和出栈，整个解析过程就这样一直持续下去，直到分词器将所有字节流分词完成。

为了更加直观地理解整个过程，下面我们结合一段 HTML 代码（如下），来一步步分析 DOM 树的生成过程。

```html
<html>
<body>
    <div>1</div>
    <div>test</div>
</body>
</html>
```

这段代码以字节流的形式传给了 HTML 解析器，经过分词器处理，解析出来的第一个 Token 是 StartTag html，解析出来的 Token 会被压入到栈中，并同时创建一个 html 的 DOM 节点，将其加入到 DOM 树中。

这里需要补充说明下，HTML 解析器开始工作时，会默认创建了一个根为 document 的空 DOM 结构，同时会将一个 StartTag document 的 Token 压入栈底。然后经过分词器解析出来的第一个 StartTag html Token 会被压入到栈中，并创建一个 html 的 DOM 节点，添加到 document 上，如下图所示

![img](http://blog.poetries.top/img-repo/2019/11/59.png)

然后按照同样的流程解析出来 StartTag body 和 StartTag div，其 Token 栈和 DOM 的状态如下图所示：

![img](http://blog.poetries.top/img-repo/2019/11/60.png)

接下来解析出来的是第一个 div 的文本 Token，渲染引擎会为该 Token 创建一个文本节点，并将该 Token 添加到 DOM 中，它的父节点就是当前 Token 栈顶元素对应的节点，如下图所示：

![img](http://blog.poetries.top/img-repo/2019/11/61.png)

再接下来，分词器解析出来第一个 EndTag div，这时候 HTML 解析器会去判断当前栈顶的元素是否是 StartTag div，如果是则从栈顶弹出 StartTag div，如下图所示

![img](http://blog.poetries.top/img-repo/2019/11/62.png)

按照同样的规则，一路解析，最终结果如下图所示：

![img](http://blog.poetries.top/img-repo/2019/11/63.png)

通过上面的介绍，相信你已经清楚 DOM 是怎么生成的了。不过在实际生产环境中，HTML 源文件中既包含 CSS 和 JavaScript，又包含图片、音频、视频等文件，所以处理过程远比上面这个示范 Demo 复杂。不过理解了这个简单的 Demo 生成过程，我们就可以往下分析更加复杂的场景了。

## JavaScript 是如何影响 DOM 生成的

我们再来看看稍微复杂点的 HTML 文件，如下所示：

```html
<html>
<body>
    <div>1</div>
    <script>
    let div1 = document.getElementsByTagName('div')[0]
    div1.innerText = 'time.geekbang'
    </script>
    <div>test</div>
</body>
</html>
```

我在两段 div 中间插入了一段 JavaScript 脚本，这段脚本的解析过程就有点不一样了。script标签之前，所有的解析流程还是和之前介绍的一样，但是解析到script标签时，渲染引擎判断这是一段脚本，此时 HTML 解析器就会暂停 DOM 的解析，因为接下来的 JavaScript 可能要修改当前已经生成的 DOM 结构。

通过前面 DOM 生成流程分析，我们已经知道当解析到 script 脚本标签时，其 DOM 树结构如下所示：

![img](http://blog.poetries.top/img-repo/2019/11/64.png)

这时候 HTML 解析器暂停工作，JavaScript 引擎介入，并执行 script 标签中的这段脚本，因为这段 JavaScript 脚本修改了 DOM 中第一个 div 中的内容，所以执行这段脚本之后，div 节点内容已经修改为 time.geekbang 了。脚本执行完成之后，HTML 解析器恢复解析过程，继续解析后续的内容，直至生成最终的 DOM。

以上过程应该还是比较好理解的，不过除了在页面中直接内嵌 JavaScript 脚本之外，我们还通常需要在页面中引入 JavaScript 文件，这个解析过程就稍微复杂了些，如下面代码：

```js
//foo.js
let div1 = document.getElementsByTagName('div')[0]
div1.innerText = 'time.geekbang'
<html>
<body>
    <div>1</div>
    <script type="text/javascript" src='foo.js'></script>
    <div>test</div>
</body>
</html>
```

这段代码的功能还是和前面那段代码是一样的，不过这里我把内嵌 JavaScript 脚本修改成了通过 JavaScript 文件加载。其整个执行流程还是一样的，执行到 JavaScript 标签时，暂停整个 DOM 的解析，执行 JavaScript 代码，不过这里执行 JavaScript 时，需要先下载这段 JavaScript 代码。这里需要重点关注下载环境，因为JavaScript 文件的下载过程会阻塞 DOM 解析，而通常下载又是非常耗时的，会受到网络环境、JavaScript 文件大小等因素的影响。

不过 Chrome 浏览器做了很多优化，其中一个主要的优化是预解析操作。当渲染引擎收到字节流之后，会开启一个预解析线程，用来分析 HTML 文件中包含的 JavaScript、CSS 等相关文件，解析到相关文件之后，预解析线程会提前下载这些文件。

再回到 DOM 解析上，我们知道引入 JavaScript 线程会阻塞 DOM，不过也有一些相关的策略来规避，比如使用 CDN 来加速 JavaScript 文件的加载，压缩 JavaScript 文件的体积。另外，如果 JavaScript 文件中没有操作 DOM 相关代码，就可以将该 JavaScript 脚本设置为异步加载，通过 async 或 defer 来标记代码，使用方式如下所示：

```text
<script async type="text/javascript" src='foo.js'></script>
<script defer type="text/javascript" src='foo.js'></script>
```

async 和 defer 虽然都是异步的，不过还有一些差异，使用 async 标志的脚本文件一旦加载完成，会立即执行；而使用了 defer 标记的脚本文件，需要在 DOMContentLoaded 事件之前执行。

现在我们知道了 JavaScript 是如何阻塞 DOM 解析的了，那接下来我们再来结合文中代码看看另外一种情况：

```text
<head>
    <style src='theme.css'></style>
</head>
<body>
    <div>1</div>
    <script>
            let div1 = document.getElementsByTagName('div')[0]
            div1.innerText = 'time.geekbang' // 需要 DOM
            div1.style.color = 'red'  // 需要 CSSOM
        </script>
    <div>test</div>
</body>
</html>
```

该示例中，JavaScript 代码出现了 div1.style.color = ‘red' 的语句，它是用来操纵 CSSOM 的，所以在执行 JavaScript 之前，需要先解析 JavaScript 语句之上所有的 CSS 样式。所以如果代码里引用了外部的 CSS 文件，那么在执行 JavaScript 之前，还需要等待外部的 CSS 文件下载完成，并解析生成 CSSOM 对象之后，才能执行 JavaScript 脚本。

而 JavaScript 引擎在解析 JavaScript 之前，是不知道 JavaScript 是否操纵了 CSSOM 的，所以渲染引擎在遇到 JavaScript 脚本时，不管该脚本是否操纵了 CSSOM，都会执行 CSS 文件下载，解析操作，再执行 JavaScript 脚本。

所以说 JavaScript 脚本是依赖样式表的，这又多了一个阻塞过程。至于如何优化，我们在下篇文章中再来深入探讨。

通过上面的分析，我们知道了 JavaScript 会阻塞 DOM 生成，而样式文件又会阻塞 JavaScript 的执行，所以在实际的工程中需要重点关注 JavaScript 文件和样式表文件，使用不当会影响到页面性能的

## 总结

好了，今天就讲到这里，下面我来总结下今天的内容。

首先我们介绍了 DOM 是如何生成的，然后又基于 DOM 的生成过程分析了 JavaScript 是如何影响到 DOM 生成的。因为 CSS 和 JavaScript 都会影响到 DOM 的生成，所以我们又介绍了一些加速生成 DOM 的方案，理解了这些，能让你更加深刻地理解如何去优化首次页面渲染。

额外说明一下，渲染引擎还有一个安全检查模块叫 XSSAuditor，是用来检测词法安全的。在分词器解析出来 Token 之后，它会检测这些模块是否安全，比如是否引用了外部脚本，是否符合 CSP 规范，是否存在跨站点请求等。如果出现不符合规范的内容，XSSAuditor 会对该脚本或者下载任务进行拦截。详细内容我们会在后面的安全模块介绍，这里就不赘述了

# 5.事件模型

事件的本质是程序各个组成部分之间的一种通信方式，也是异步编程的一种实现。DOM 支持大量的事件，本章介绍 DOM 的事件编程。

## EventTarget 接口

DOM 的事件操作（监听和触发），都定义在`EventTarget`接口。所有节点对象都部署了这个接口，其他一些需要事件通信的浏览器内置对象（比如，`XMLHttpRequest`、`AudioNode`、`AudioContext`）也部署了这个接口。

该接口主要提供三个实例方法。

- `addEventListener`：绑定事件的监听函数
- `removeEventListener`：移除事件的监听函数
- `dispatchEvent`：触发事件

### EventTarget.addEventListener()

`EventTarget.addEventListener()`用于在当前节点或对象上，定义一个特定事件的监听函数。一旦这个事件发生，就会执行监听函数。该方法没有返回值。

```
target.addEventListener(type, listener[, useCapture]);
```

该方法接受三个参数。

- `type`：事件名称，大小写敏感。
- `listener`：监听函数。事件发生时，会调用该监听函数。
- `useCapture`：布尔值，表示监听函数是否在捕获阶段（capture）触发（参见后文《事件的传播》部分），默认为`false`（监听函数只在冒泡阶段被触发）。该参数可选。

下面是一个例子。

```
function hello() {
  console.log('Hello world');
}

var button = document.getElementById('btn');
button.addEventListener('click', hello, false);
```

上面代码中，`button`节点的`addEventListener`方法绑定`click`事件的监听函数`hello`，该函数只在冒泡阶段触发。

关于参数，有两个地方需要注意。

首先，第二个参数除了监听函数，还可以是一个具有`handleEvent`方法的对象。

```
buttonElement.addEventListener('click', {
  handleEvent: function (event) {
    console.log('click');
  }
});
```

上面代码中，`addEventListener`方法的第二个参数，就是一个具有`handleEvent`方法的对象。

其次，第三个参数除了布尔值`useCapture`，还可以是一个属性配置对象。该对象有以下属性。

> - `capture`：布尔值，表示该事件是否在`捕获阶段`触发监听函数。
> - `once`：布尔值，表示监听函数是否只触发一次，然后就自动移除。
> - `passive`：布尔值，表示监听函数不会调用事件的`preventDefault`方法。如果监听函数调用了，浏览器将忽略这个要求，并在监控台输出一行警告。

`addEventListener`方法可以为针对当前对象的同一个事件，添加多个不同的监听函数。这些函数按照添加顺序触发，即先添加先触发。如果为同一个事件多次添加同一个监听函数，该函数只会执行一次，多余的添加将自动被去除（不必使用`removeEventListener`方法手动去除）。

```
function hello() {
  console.log('Hello world');
}

document.addEventListener('click', hello, false);
document.addEventListener('click', hello, false);
```

执行上面代码，点击文档只会输出一行`Hello world`。

如果希望向监听函数传递参数，可以用匿名函数包装一下监听函数。

```
function print(x) {
  console.log(x);
}

var el = document.getElementById('div1');
el.addEventListener('click', function () { print('Hello'); }, false);
```

上面代码通过匿名函数，向监听函数`print`传递了一个参数。

监听函数内部的`this`，指向当前事件所在的那个对象。

```
// HTML 代码如下
// <p id="para">Hello</p>
var para = document.getElementById('para');
para.addEventListener('click', function (e) {
  console.log(this.nodeName); // "P"
}, false);
```

上面代码中，监听函数内部的`this`指向事件所在的对象`para`。

### EventTarget.removeEventListener()

`EventTarget.removeEventListener`方法用来移除`addEventListener`方法添加的事件监听函数。该方法没有返回值。

```
div.addEventListener('click', listener, false);
div.removeEventListener('click', listener, false);
```

`removeEventListener`方法的参数，与`addEventListener`方法完全一致。它的第一个参数“事件类型”，大小写敏感。

注意，`removeEventListener`方法移除的监听函数，必须是`addEventListener`方法添加的那个监听函数，而且必须在同一个元素节点，否则无效。

```
div.addEventListener('click', function (e) {}, false);
div.removeEventListener('click', function (e) {}, false);
```

上面代码中，`removeEventListener`方法无效，因为监听函数不是同一个匿名函数。

```
element.addEventListener('mousedown', handleMouseDown, true);
element.removeEventListener("mousedown", handleMouseDown, false);
```

上面代码中，`removeEventListener`方法也是无效的，因为第三个参数不一样。

### EventTarget.dispatchEvent()

`EventTarget.dispatchEvent`方法在当前节点上触发指定事件，从而触发监听函数的执行。该方法返回一个布尔值，只要有一个监听函数调用了`Event.preventDefault()`，则返回值为`false`，否则为`true`。

```
target.dispatchEvent(event)
```

`dispatchEvent`方法的参数是一个`Event`对象的实例（详见《Event 对象》章节）。

```
para.addEventListener('click', hello, false);
var event = new Event('click');
para.dispatchEvent(event);
```

上面代码在当前节点触发了`click`事件。

如果`dispatchEvent`方法的参数为空，或者不是一个有效的事件对象，将报错。

下面代码根据`dispatchEvent`方法的返回值，判断事件是否被取消了。

```
var canceled = !cb.dispatchEvent(event);
if (canceled) {
  console.log('事件取消');
} else {
  console.log('事件未取消');
}
```

## 监听函数

浏览器的事件模型，就是通过监听函数（listener）对事件做出反应。事件发生后，浏览器监听到了这个事件，就会执行对应的监听函数。这是事件驱动编程模式（event-driven）的主要编程方式。

JavaScript 有三种方法，可以为事件绑定监听函数。

### HTML 的 on- 属性

HTML 语言允许在元素的属性中，直接定义某些事件的监听代码。

```
<body onload="doSomething()">
<div onclick="console.log('触发事件')">
```

上面代码为`body`节点的`load`事件、`div`节点的`click`事件，指定了监听代码。一旦事件发生，就会执行这段代码。

元素的事件监听属性，都是`on`加上事件名，比如`onload`就是`on + load`，表示`load`事件的监听代码。

注意，这些属性的值是将会执行的代码，而不是一个函数。

```
<!-- 正确 -->
<body onload="doSomething()">

<!-- 错误 -->
<body onload="doSomething">
```

一旦指定的事件发生，`on-`属性的值是原样传入 JavaScript 引擎执行。因此如果要执行函数，不要忘记加上一对圆括号。

使用这个方法指定的监听代码，只会在冒泡阶段触发。

```
<div onClick="console.log(2)">
  <button onClick="console.log(1)">点击</button>
</div>
```

上面代码中，`<button>`是`<div>`的子元素。`<button>`的`click`事件，也会触发`<div>`的`click`事件。由于`on-`属性的监听代码，只在冒泡阶段触发，所以点击结果是先输出`1`，再输出`2`，即事件从子元素开始冒泡到父元素。

直接设置`on-`属性，与通过元素节点的`setAttribute`方法设置`on-`属性，效果是一样的。

```
el.setAttribute('onclick', 'doSomething()');
// 等同于
// <Element onclick="doSomething()">
```

### 元素节点的事件属性

元素节点对象的事件属性，同样可以指定监听函数。

```
window.onload = doSomething;

div.onclick = function (event) {
  console.log('触发事件');
};
```

使用这个方法指定的监听函数，也是只会在冒泡阶段触发。

注意，这种方法与 HTML 的`on-`属性的差异是，它的值是函数名（`doSomething`），而不像后者，必须给出完整的监听代码（`doSomething()`）。

### EventTarget.addEventListener()

所有 DOM 节点实例都有`addEventListener`方法，用来为该节点定义事件的监听函数。

```
window.addEventListener('load', doSomething, false);
```

`addEventListener`方法的详细介绍，参见`EventTarget`章节。

### 小结

上面三种方法，第一种“HTML 的 on- 属性”，违反了 HTML 与 JavaScript 代码相分离的原则，将两者写在一起，不利于代码分工，因此不推荐使用。

第二种“元素节点的事件属性”的缺点在于，同一个事件只能定义一个监听函数，也就是说，如果定义两次`onclick`属性，后一次定义会覆盖前一次。因此，也不推荐使用。

第三种`EventTarget.addEventListener`是推荐的指定监听函数的方法。它有如下优点：

- 同一个事件可以添加多个监听函数。
- 能够指定在哪个阶段（捕获阶段还是冒泡阶段）触发监听函数。
- 除了 DOM 节点，其他对象（比如`window`、`XMLHttpRequest`等）也有这个接口，它等于是整个 JavaScript 统一的监听函数接口。

## this 的指向

监听函数内部的`this`指向触发事件的那个元素节点。

```
<button id="btn" onclick="console.log(this.id)">点击</button>
```

执行上面代码，点击后会输出`btn`。

其他两种监听函数的写法，`this`的指向也是如此。

```
// HTML 代码如下
// <button id="btn">点击</button>
var btn = document.getElementById('btn');

// 写法一
btn.onclick = function () {
  console.log(this.id);
};

// 写法二
btn.addEventListener(
  'click',
  function (e) {
    console.log(this.id);
  },
  false
);
```

上面两种写法，点击按钮以后也是输出`btn`。

## 事件的传播

一个事件发生后，会在子元素和父元素之间传播（propagation）。这种传播分成三个阶段。

- **第一阶段**：从`window`对象传导到目标节点（上层传到底层），称为“捕获阶段”（capture phase）。
- **第二阶段**：在目标节点上触发，称为“目标阶段”（target phase）。
- **第三阶段**：从目标节点传导回`window`对象（从底层传回上层），称为“冒泡阶段”（bubbling phase）。

这种三阶段的传播模型，使得同一个事件会在多个节点上触发。

```
<div>
  <p>点击</p>
</div>
```

上面代码中，`<div>`节点之中有一个`<p>`节点。

如果对这两个节点，都设置`click`事件的监听函数（每个节点的捕获阶段和监听阶段，各设置一个监听函数），共计设置四个监听函数。然后，对`<p>`点击，`click`事件会触发四次。

```
var phases = {
  1: 'capture',
  2: 'target',
  3: 'bubble'
};

var div = document.querySelector('div');
var p = document.querySelector('p');

div.addEventListener('click', callback, true);
p.addEventListener('click', callback, true);
div.addEventListener('click', callback, false);
p.addEventListener('click', callback, false);

function callback(event) {
  var tag = event.currentTarget.tagName;
  var phase = phases[event.eventPhase];
  console.log("Tag: '" + tag + "'. EventPhase: '" + phase + "'");
}

// 点击以后的结果
// Tag: 'DIV'. EventPhase: 'capture'
// Tag: 'P'. EventPhase: 'target'
// Tag: 'P'. EventPhase: 'target'
// Tag: 'DIV'. EventPhase: 'bubble'
```

上面代码表示，`click`事件被触发了四次：`<div>`节点的捕获阶段和冒泡阶段各1次，`<p>`节点的目标阶段触发了2次。

1. 捕获阶段：事件从`<div>`向`<p>`传播时，触发`<div>`的`click`事件；
2. 目标阶段：事件从`<div>`到达`<p>`时，触发`<p>`的`click`事件；
3. 冒泡阶段：事件从`<p>`传回`<div>`时，再次触发`<div>`的`click`事件。

其中，`<p>`节点有两个监听函数（`addEventListener`方法第三个参数的不同，会导致绑定两个监听函数），因此它们都会因为`click`事件触发一次。所以，`<p>`会在`target`阶段有两次输出。

注意，浏览器总是假定`click`事件的目标节点，就是点击位置嵌套最深的那个节点（本例是`<div>`节点里面的`<p>`节点）。所以，`<p>`节点的捕获阶段和冒泡阶段，都会显示为`target`阶段。

事件传播的最上层对象是`window`，接着依次是`document`，`html`（`document.documentElement`）和`body`（`document.body`）。也就是说，上例的事件传播顺序，在捕获阶段依次为`window`、`document`、`html`、`body`、`div`、`p`，在冒泡阶段依次为`p`、`div`、`body`、`html`、`document`、`window`。

## 事件的代理

由于事件会在冒泡阶段向上传播到父节点，因此可以把子节点的监听函数定义在父节点上，由父节点的监听函数统一处理多个子元素的事件。这种方法叫做事件的代理（delegation）。

```
var ul = document.querySelector('ul');

ul.addEventListener('click', function (event) {
  if (event.target.tagName.toLowerCase() === 'li') {
    // some code
  }
});
```

上面代码中，`click`事件的监听函数定义在`<ul>`节点，但是实际上，它处理的是子节点`<li>`的`click`事件。这样做的好处是，只要定义一个监听函数，就能处理多个子节点的事件，而不用在每个`<li>`节点上定义监听函数。而且以后再添加子节点，监听函数依然有效。

如果希望事件到某个节点为止，不再传播，可以使用事件对象的`stopPropagation`方法。

```
// 事件传播到 p 元素后，就不再向下传播了
p.addEventListener('click', function (event) {
  event.stopPropagation();
}, true);

// 事件冒泡到 p 元素后，就不再向上冒泡了
p.addEventListener('click', function (event) {
  event.stopPropagation();
}, false);
```

上面代码中，`stopPropagation`方法分别在捕获阶段和冒泡阶段，阻止了事件的传播。

但是，`stopPropagation`方法只会阻止事件的传播，不会阻止该事件触发`<p>`节点的其他`click`事件的监听函数。也就是说，不是彻底取消`click`事件。

```
p.addEventListener('click', function (event) {
  event.stopPropagation();
  console.log(1);
});

p.addEventListener('click', function(event) {
  // 会触发
  console.log(2);
});
```

上面代码中，`p`元素绑定了两个`click`事件的监听函数。`stopPropagation`方法只能阻止这个事件向其他元素传播。因此，第二个监听函数会触发。输出结果会先是1，然后是2。

如果想要彻底阻止这个事件的传播，不再触发后面所有`click`的监听函数，可以使用`stopImmediatePropagation`方法。

```
p.addEventListener('click', function (event) {
  event.stopImmediatePropagation();
  console.log(1);
});

p.addEventListener('click', function(event) {
  // 不会被触发
  console.log(2);
});
```

上面代码中，`stopImmediatePropagation`方法可以彻底阻止这个事件传播，使得后面绑定的所有`click`监听函数都不再触发。所以，只会输出1，不会输出2。

## Event 对象概述

事件发生以后，会产生一个事件对象，作为参数传给监听函数。浏览器原生提供一个`Event`对象，所有的事件都是这个对象的实例，或者说继承了`Event.prototype`对象。

`Event`对象本身就是一个构造函数，可以用来生成新的实例。

```
event = new Event(type, options);
```

`Event`构造函数接受两个参数。第一个参数`type`是字符串，表示事件的名称；第二个参数`options`是一个对象，表示事件对象的配置。该对象主要有下面两个属性。

- `bubbles`：布尔值，可选，默认为`false`，表示事件对象是否冒泡。
- `cancelable`：布尔值，可选，默认为`false`，表示事件是否可以被取消，即能否用`Event.preventDefault()`取消这个事件。一旦事件被取消，就好像从来没有发生过，不会触发浏览器对该事件的默认行为。

```
var ev = new Event(
  'look',
  {
    'bubbles': true,
    'cancelable': false
  }
);
document.dispatchEvent(ev);
```

上面代码新建一个`look`事件实例，然后使用`dispatchEvent`方法触发该事件。

注意，如果不是显式指定`bubbles`属性为`true`，生成的事件就只能在“捕获阶段”触发监听函数。

```
// HTML 代码为
// <div><p>Hello</p></div>
var div = document.querySelector('div');
var p = document.querySelector('p');

function callback(event) {
  var tag = event.currentTarget.tagName;
  console.log('Tag: ' + tag); // 没有任何输出
}

div.addEventListener('click', callback, false);

var click = new Event('click');
p.dispatchEvent(click);
```

上面代码中，`p`元素发出一个`click`事件，该事件默认不会冒泡。`div.addEventListener`方法指定在冒泡阶段监听，因此监听函数不会触发。如果写成`div.addEventListener('click', callback, true)`，那么在“捕获阶段”可以监听到这个事件。

另一方面，如果这个事件在`div`元素上触发。

```
div.dispatchEvent(click);
```

那么，不管`div`元素是在冒泡阶段监听，还是在捕获阶段监听，都会触发监听函数。因为这时`div`元素是事件的目标，不存在是否冒泡的问题，`div`元素总是会接收到事件，因此导致监听函数生效。

## Event 对象的实例属性

### Event.bubbles，Event.eventPhase

`Event.bubbles`属性返回一个布尔值，表示当前事件是否会冒泡。该属性为只读属性，一般用来了解 Event 实例是否可以冒泡。前面说过，除非显式声明，`Event`构造函数生成的事件，默认是不冒泡的。

`Event.eventPhase`属性返回一个整数常量，表示事件目前所处的阶段。该属性只读。

```
var phase = event.eventPhase;
```

`Event.eventPhase`的返回值有四种可能。

- 0.事件目前没有发生。
- 1.事件目前处于捕获阶段，即处于从祖先节点向目标节点的传播过程中。
- 2.事件到达目标节点，即`Event.target`属性指向的那个节点。
- 3.事件处于冒泡阶段，即处于从目标节点向祖先节点的反向传播过程中。

### Event.cancelable，Event.cancelBubble，event.defaultPrevented

`Event.cancelable`属性返回一个布尔值，表示事件是否可以取消。该属性为只读属性，一般用来了解 Event 实例的特性。

大多数浏览器的原生事件是可以取消的。比如，取消`click`事件，点击链接将无效。但是除非显式声明，`Event`构造函数生成的事件，默认是不可以取消的。

```
var evt = new Event('foo');
evt.cancelable  // false
```

当`Event.cancelable`属性为`true`时，调用`Event.preventDefault()`就可以取消这个事件，阻止浏览器对该事件的默认行为。

如果事件不能取消，调用`Event.preventDefault()`会没有任何效果。所以使用这个方法之前，最好用`Event.cancelable`属性判断一下是否可以取消。

```
function preventEvent(event) {
  if (event.cancelable) {
    event.preventDefault();
  } else {
    console.warn('This event couldn\'t be canceled.');
    console.dir(event);
  }
}
```

`Event.cancelBubble`属性是一个布尔值，如果设为`true`，相当于执行`Event.stopPropagation()`，可以阻止事件的传播。

`Event.defaultPrevented`属性返回一个布尔值，表示该事件是否调用过`Event.preventDefault`方法。该属性只读。

```
if (event.defaultPrevented) {
  console.log('该事件已经取消了');
}
```

### Event.currentTarget，Event.target

`Event.currentTarget`属性返回事件当前所在的节点，即正在执行的监听函数所绑定的那个节点。

`Event.target`属性返回原始触发事件的那个节点，即事件最初发生的节点。事件传播过程中，不同节点的监听函数内部的`Event.target`与`Event.currentTarget`属性的值是不一样的，前者总是不变的，后者则是指向监听函数所在的那个节点对象。

```
// HTML代码为
// <p id="para">Hello <em>World</em></p>
function hide(e) {
  console.log(this === e.currentTarget);  // 总是 true
  console.log(this === e.target);  // 有可能不是 true
  e.target.style.visibility = 'hidden';
}

para.addEventListener('click', hide, false);
```

上面代码中，如果在`para`节点的`<em>`子节点上面点击，则`e.target`指向`<em>`子节点，导致`<em>`子节点（即 World 部分）会不可见。如果点击 Hello 部分，则整个`para`都将不可见。

### Event.type

`Event.type`属性返回一个字符串，表示事件类型。事件的类型是在生成事件的时候。该属性只读。

```
var evt = new Event('foo');
evt.type // "foo"
```

### Event.timeStamp

`Event.timeStamp`属性返回一个毫秒时间戳，表示事件发生的时间。它是相对于网页加载成功开始计算的。

```
var evt = new Event('foo');
evt.timeStamp // 3683.6999999995896
```

它的返回值有可能是整数，也有可能是小数（高精度时间戳），取决于浏览器的设置。

下面是一个计算鼠标移动速度的例子，显示每秒移动的像素数量。

```
var previousX;
var previousY;
var previousT;

window.addEventListener('mousemove', function(event) {
  if (
    previousX !== undefined &&
    previousY !== undefined &&
    previousT !== undefined
  ) {
    var deltaX = event.screenX - previousX;
    var deltaY = event.screenY - previousY;
    var deltaD = Math.sqrt(Math.pow(deltaX, 2) + Math.pow(deltaY, 2));

    var deltaT = event.timeStamp - previousT;
    console.log(deltaD / deltaT * 1000);
  }

  previousX = event.screenX;
  previousY = event.screenY;
  previousT = event.timeStamp;
});
```

### Event.isTrusted

`Event.isTrusted`属性返回一个布尔值，表示该事件是否由真实的用户行为产生。比如，用户点击链接会产生一个`click`事件，该事件是用户产生的；`Event`构造函数生成的事件，则是脚本产生的。

```
var evt = new Event('foo');
evt.isTrusted // false
```

上面代码中，`evt`对象是脚本产生的，所以`isTrusted`属性返回`false`。

### Event.detail

`Event.detail`属性只有浏览器的 UI （用户界面）事件才具有。该属性返回一个数值，表示事件的某种信息。具体含义与事件类型相关。比如，对于`click`和`dbclick`事件，`Event.detail`是鼠标按下的次数（`1`表示单击，`2`表示双击，`3`表示三击）；对于鼠标滚轮事件，`Event.detail`是滚轮正向滚动的距离，负值就是负向滚动的距离，返回值总是3的倍数。

```
// HTML 代码如下
// <p>Hello</p>
function giveDetails(e) {
  console.log(e.detail);
}

document.querySelector('p').onclick = giveDetails;
```

## Event 对象的实例方法

### Event.preventDefault()

`Event.preventDefault`方法取消浏览器对当前事件的默认行为。比如点击链接后，浏览器默认会跳转到另一个页面，使用这个方法以后，就不会跳转了；再比如，按一下空格键，页面向下滚动一段距离，使用这个方法以后也不会滚动了。该方法生效的前提是，事件对象的`cancelable`属性为`true`，如果为`false`，调用该方法没有任何效果。

注意，该方法只是取消事件对当前元素的默认影响，不会阻止事件的传播。如果要阻止传播，可以使用`stopPropagation()`或`stopImmediatePropagation()`方法。

```
// HTML 代码为
// <input type="checkbox" id="my-checkbox" />
var cb = document.getElementById('my-checkbox');

cb.addEventListener(
  'click',
  function (e){ e.preventDefault(); },
  false
);
```

上面代码中，浏览器的默认行为是单击会选中单选框，取消这个行为，就导致无法选中单选框。

利用这个方法，可以为文本输入框设置校验条件。如果用户的输入不符合条件，就无法将字符输入文本框。

```
// HTML 代码为
// <input type="text" id="my-input" />
var input = document.getElementById('my-input');
input.addEventListener('keypress', checkName, false);

function checkName(e) {
  if (e.charCode < 97 || e.charCode > 122) {
    e.preventDefault();
  }
}
```

上面代码为文本框的`keypress`事件设定监听函数后，将只能输入小写字母，否则输入事件的默认行为（写入文本框）将被取消，导致不能向文本框输入内容。

### Event.stopPropagation()

`stopPropagation`方法阻止事件在 DOM 中继续传播，防止再触发定义在别的节点上的监听函数，但是不包括在当前节点上其他的事件监听函数。

```
function stopEvent(e) {
  e.stopPropagation();
}

el.addEventListener('click', stopEvent, false);
```

上面代码中，`click`事件将不会进一步冒泡到`el`节点的父节点。

### Event.stopImmediatePropagation()

`Event.stopImmediatePropagation`方法阻止同一个事件的其他监听函数被调用，不管监听函数定义在当前节点还是其他节点。也就是说，该方法阻止事件的传播，比`Event.stopPropagation()`更彻底。

如果同一个节点对于同一个事件指定了多个监听函数，这些函数会根据添加的顺序依次调用。只要其中有一个监听函数调用了`Event.stopImmediatePropagation`方法，其他的监听函数就不会再执行了。

```
function l1(e){
  e.stopImmediatePropagation();
}

function l2(e){
  console.log('hello world');
}

el.addEventListener('click', l1, false);
el.addEventListener('click', l2, false);
```

上面代码在`el`节点上，为`click`事件添加了两个监听函数`l1`和`l2`。由于`l1`调用了`event.stopImmediatePropagation`方法，所以`l2`不会被调用。

### Event.composedPath()

`Event.composedPath()`返回一个数组，成员是事件的最底层节点和依次冒泡经过的所有上层节点。

```
// HTML 代码如下
// <div>
//   <p>Hello</p>
// </div>
var div = document.querySelector('div');
var p = document.querySelector('p');

div.addEventListener('click', function (e) {
  console.log(e.composedPath());
}, false);
// [p, div, body, html, document, Window]
```

上面代码中，`click`事件的最底层节点是`p`，向上依次是`div`、`body`、`html`、`document`、`Window`。

## CustomEvent 接口

CustomEvent 接口用于生成自定义的事件实例。那些浏览器预定义的事件，虽然可以手动生成，但是往往不能在事件上绑定数据。如果需要在触发事件的同时，传入指定的数据，就可以使用 CustomEvent 接口生成的自定义事件对象。

浏览器原生提供`CustomEvent()`构造函数，用来生成 CustomEvent 事件实例。

```
new CustomEvent(type, options)
```

`CustomEvent()`构造函数接受两个参数。第一个参数是字符串，表示事件的名字，这是必须的。第二个参数是事件的配置对象，这个参数是可选的。`CustomEvent`的配置对象除了接受 Event 事件的配置属性，只有一个自己的属性。

- `detail`：表示事件的附带数据，默认为`null`。

下面是一个例子。

```
var event = new CustomEvent('build', { 'detail': 'hello' });

function eventHandler(e) {
	console.log(e.detail);
}

document.body.addEventListener('build', function (e) {
	console.log(e.detail);
});

document.body.dispatchEvent(event);
```

上面代码中，我们手动定义了`build`事件。该事件触发后，会被监听到，从而输出该事件实例的`detail`属性（即字符串`hello`）。

下面是另一个例子。

```
var myEvent = new CustomEvent('myevent', {
    detail: {
    	foo: 'bar'
    },
    bubbles: true,
    cancelable: false
});

el.addEventListener('myevent', function (event) {
	console.log('Hello ' + event.detail.foo);
});

el.dispatchEvent(myEvent);
```

上面代码也说明，CustomEvent 的事件实例，除了具有 Event 接口的实例属性，还具有`detail`属性。

## 参考链接

- Wilson Page, [An Introduction To DOM Events](http://coding.smashingmagazine.com/2013/11/12/an-introduction-to-dom-events/)
- Mozilla Developer Network, [Using Firefox 1.5 caching](https://developer.mozilla.org/en-US/docs/Using_Firefox_1.5_caching)
- Craig Buckler, [How to Capture CSS3 Animation Events in JavaScript](http://www.sitepoint.com/css3-animation-javascript-event-handlers/)
- Ray Nicholus, [You Don’t Need jQuery!: Events](http://blog.garstasio.com/you-dont-need-jquery/events/)

# 6.彻底理解浏览器的缓存机制

## 概述

浏览器的缓存机制也就是我们说的HTTP缓存机制，其机制是根据HTTP报文的缓存标识进行的，所以在分析浏览器缓存机制之前，我们先使用图文简单介绍一下HTTP报文，HTTP报文分为两种：

HTTP请求(Request)报文，报文格式为：请求行 – HTTP头(通用信息头，请求头，实体头) – 请求报文主体(只有POST才有报文主体)，如下图

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db6358082ff05?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db6358033cdc4?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

HTTP响应(Response)报文，报文格式为：状态行 – HTTP头(通用信息头，响应头，实体头) – 响应报文主体，如下图

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db635806ca887?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db6358079780e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

注：通用信息头指的是请求和响应报文都支持的头域，分别为Cache-Control、Connection、Date、Pragma、Transfer-Encoding、Upgrade、Via；实体头则是实体信息的实体头域，分别为Allow、Content-Base、Content-Encoding、Content-Language、Content-Length、Content-Location、Content-MD5、Content-Range、Content-Type、Etag、Expires、Last-Modified、extension-header。这里只是为了方便理解，将通用信息头，响应头/请求头，实体头都归为了HTTP头。

以上的概念在这里我们不做多讲解，只简单介绍，有兴趣的童鞋可以自行研究。

## 缓存过程分析

浏览器与服务器通信的方式为应答模式，即是：浏览器发起HTTP请求 – 服务器响应该请求。那么浏览器第一次向服务器发起该请求后拿到请求结果，会根据响应报文中HTTP头的缓存标识，决定是否缓存结果，是则将请求结果和缓存标识存入浏览器缓存中，简单的过程如下图：

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db6359673e7d0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

由上图我们可以知道：

- 浏览器每次发起请求，都会先在浏览器缓存中查找该请求的结果以及缓存标识
- 浏览器每次拿到返回的请求结果都会将该结果和缓存标识存入浏览器缓存中

以上两点结论就是浏览器缓存机制的关键，他确保了每个请求的缓存存入与读取，只要我们再理解浏览器缓存的使用规则，那么所有的问题就迎刃而解了，本文也将围绕着这点进行详细分析。为了方便大家理解，这里我们根据是否需要向服务器重新发起HTTP请求将缓存过程分为两个部分，分别是强制缓存和协商缓存。

## 强制缓存

强制缓存就是向浏览器缓存查找该请求结果，并根据该结果的缓存规则来决定是否使用该缓存结果的过程，强制缓存的情况主要有三种(暂不分析协商缓存过程)，如下：

不存在该缓存结果和缓存标识，强制缓存失效，则直接向服务器发起请求（跟第一次发起请求一致），如下图：

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db63596c9de23?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

存在该缓存结果和缓存标识，但该结果已失效，强制缓存失效，则使用协商缓存(暂不分析)，如下图

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db63597182316?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

存在该缓存结果和缓存标识，且该结果尚未失效，强制缓存生效，直接返回该结果，如下图

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db6359acd19d3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

> 那么强制缓存的缓存规则是什么？

当浏览器向服务器发起请求时，服务器会将缓存规则放入HTTP响应报文的HTTP头中和请求结果一起返回给浏览器，控制强制缓存的字段分别是Expires和Cache-Control，其中Cache-Control优先级比Expires高。

### Expires

Expires是HTTP/1.0控制网页缓存的字段，其值为服务器返回该请求结果缓存的到期时间，即再次发起该请求时，如果客户端的时间小于Expires的值时，直接使用缓存结果。

> Expires是HTTP/1.0的字段，但是现在浏览器默认使用的是HTTP/1.1，那么在HTTP/1.1中网页缓存还是否由Expires控制？

到了HTTP/1.1，Expire已经被Cache-Control替代，原因在于Expires控制缓存的原理是使用客户端的时间与服务端返回的时间做对比，那么如果客户端与服务端的时间因为某些原因（例如时区不同；客户端和服务端有一方的时间不准确）发生误差，那么强制缓存则会直接失效，这样的话强制缓存的存在则毫无意义，那么Cache-Control又是如何控制的呢？

### Cache-Control

在HTTP/1.1中，Cache-Control是最重要的规则，主要用于控制网页缓存，主要取值为：

- public：所有内容都将被缓存（客户端和代理服务器都可缓存）
- private：所有内容只有客户端可以缓存，Cache-Control的默认取值
- no-cache：客户端缓存内容，但是是否使用缓存则需要经过协商缓存来验证决定
- no-store：所有内容都不会被缓存，即不使用强制缓存，也不使用协商缓存
- max-age=xxx (xxx is numeric)：缓存内容将在xxx秒后失效

接下来，我们直接看一个例子，如下：

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db635aa7b772b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

由上面的例子我们可以知道：

- HTTP响应报文中expires的时间值，是一个绝对值
- HTTP响应报文中Cache-Control为max-age=600，是相对值

由于Cache-Control的优先级比expires，那么直接根据Cache-Control的值进行缓存，意思就是说在600秒内再次发起该请求，则会直接使用缓存结果，强制缓存生效。

注：在无法确定客户端的时间是否与服务端的时间同步的情况下，Cache-Control相比于expires是更好的选择，所以同时存在时，只有Cache-Control生效。

了解强制缓存的过程后，我们拓展性的思考一下：

> 浏览器的缓存存放在哪里，如何在浏览器中判断强制缓存是否生效？

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db635afa6f7f7?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

这里我们以博客的请求为例，状态码为灰色的请求则代表使用了强制缓存，请求对应的Size值则代表该缓存存放的位置，分别为from memory cache 和 from disk cache。

> 那么from memory cache 和 from disk cache又分别代表的是什么呢？什么时候会使用from disk cache，什么时候会使用from memory cache呢？

from memory cache代表使用内存中的缓存，from disk cache则代表使用的是硬盘中的缓存，浏览器读取缓存的顺序为memory –> disk。

虽然我已经直接把结论说出来了，但是相信有不少人对此不能理解，那么接下来我们一起详细分析一下缓存读取问题，这里仍让以我的博客为例进行分析：

访问[heyingye.github.io/ ](https://heyingye.github.io/ ) –>200 –> 关闭博客的标签页 –> 重新打开[heyingye.github.io>](https://heyingye.github.io>) –>200(from disk cache) –> 刷新 –> 200(from memory cache)

过程如下：

- 访问[heyingye.github.io/](https://heyingye.github.io/)

  ![img](https://user-gold-cdn.xitu.io/2018/4/19/162db635b40660cd?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

- 关闭博客的标签页

- 重新打开[heyingye.github.io/](https://heyingye.github.io/)

  ![img](https://user-gold-cdn.xitu.io/2018/4/19/162db635b4f0233b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

- 刷新

  ![img](https://user-gold-cdn.xitu.io/2018/4/19/162db635bd572192?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

  from disk memory

> 看到这里可能有人小伙伴问了，最后一个步骤刷新的时候，不是同时存在着from disk cache和from memory cache吗？

对于这个问题，我们需要了解内存缓存(from memory cache)和硬盘缓存(from disk cache)，如下:

- 内存缓存(from memory cache)：内存缓存具有两个特点，分别是快速读取和时效性：
- 快速读取：内存缓存会将编译解析后的文件，直接存入该进程的内存中，占据该进程一定的内存资源，以方便下次运行使用时的快速读取。
- 时效性：一旦该进程关闭，则该进程的内存则会清空。
- 硬盘缓存(from disk cache)：硬盘缓存则是直接将缓存写入硬盘文件中，读取缓存需要对该缓存存放的硬盘文件进行I/O操作，然后重新解析该缓存内容，读取复杂，速度比内存缓存慢。

在浏览器中，浏览器会在js和图片等文件解析执行后直接存入内存缓存中，那么当刷新页面时只需直接从内存缓存中读取(from memory cache)；而css文件则会存入硬盘文件中，所以每次渲染页面都需要从硬盘读取缓存(from disk cache)。

## 协商缓存

协商缓存就是强制缓存失效后，浏览器携带缓存标识向服务器发起请求，由服务器根据缓存标识决定是否使用缓存的过程，主要有以下两种情况：

协商缓存生效，返回304，如下

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db635cbfff69d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

304

协商缓存失效，返回200和请求结果结果，如下

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db635cf070ff5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

200

同样，协商缓存的标识也是在响应报文的HTTP头中和请求结果一起返回给浏览器的，控制协商缓存的字段分别有：Last-Modified / If-Modified-Since和Etag / If-None-Match，其中Etag / If-None-Match的优先级比Last-Modified / If-Modified-Since高。

### Last-Modified / If-Modified-Since

Last-Modified是服务器响应请求时，返回该资源文件在服务器最后被修改的时间，如下。

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db635d2a88984?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

last-modify

If-Modified-Since则是客户端再次发起该请求时，携带上次请求返回的Last-Modified值，通过此字段值告诉服务器该资源上次请求返回的最后被修改时间。服务器收到该请求，发现请求头含有If-Modified-Since字段，则会根据If-Modified-Since的字段值与该资源在服务器的最后被修改时间做对比，若服务器的资源最后被修改时间大于If-Modified-Since的字段值，则重新返回资源，状态码为200；否则则返回304，代表资源无更新，可继续使用缓存文件，如下。

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db635db6d62fe?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

If-Modified-Since

### Etag / If-None-Match

Etag是服务器响应请求时，返回当前资源文件的一个唯一标识(由服务器生成)，如下。

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db635e4dd628b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

Etag

If-None-Match是客户端再次发起该请求时，携带上次请求返回的唯一标识Etag值，通过此字段值告诉服务器该资源上次请求返回的唯一标识值。服务器收到该请求后，发现该请求头中含有If-None-Match，则会根据If-None-Match的字段值与该资源在服务器的Etag值做对比，一致则返回304，代表资源无更新，继续使用缓存文件；不一致则重新返回资源文件，状态码为200，如下。

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db635ecb2cae0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

Etag-match

注：Etag / If-None-Match优先级高于Last-Modified / If-Modified-Since，同时存在则只有Etag / If-None-Match生效。

## 总结

强制缓存优先于协商缓存进行，若强制缓存(Expires和Cache-Control)生效则直接使用缓存，若不生效则进行协商缓存(Last-Modified / If-Modified-Since和Etag / If-None-Match)，协商缓存由服务器决定是否使用缓存，若协商缓存失效，那么代表该请求的缓存失效，重新获取请求结果，再存入浏览器缓存中；生效则返回304，继续使用缓存，主要过程如下：

![img](https://user-gold-cdn.xitu.io/2018/4/19/162db635ed5f6d26?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

# 7.Chrome 浏览器架构

## CPU与GPU

﻿CPU和GPU作为计算机中最重要的两个计算单元直接决定了计算性能。

### CPU

![img](https://static001.geekbang.org/infoq/94/9407812940681a84a4b4661b551da819.png)

CPU是计算机的大脑，负责处理各种不同的任务。在过去，大多数CPU是单芯片的，核心被安置在同一个芯片上。更新的CPU可以支持多核心，运算能力大大加强。而最新的的cpu已经达到10核心20线程数的能力了。

### GPU

![img](https://static001.geekbang.org/infoq/8f/8fc555c4a70f7f844dbb34edec74cf1f.png)

GPU是另一个计算机的组成部分，与CPU不同，GPU更擅长利用多核心同时处理单一的任务。像命名那样，GPU最初被用于处理图像。这就是为什么使用GPU可以更快、更顺畅的渲染页面内容。随着GPU的发展，越来越多的计算任务也可以使用GPU来处理。甚至有人说GPU是人工智能的大功臣，可见GPU已经不再仅用于图像处理上了。

## 计算机架构

﻿

![img](https://static001.geekbang.org/infoq/2a/2af38ddf296b92cd6bbbf97322774c33.png)

我们可以把计算机自下而上分成三层：硬件、操作系统和应用。有了操作系统的存在，上层运行的应用可以使用操作系统提供的能力使用硬件资源而不会直接访问硬件资源。

## 进程与线程

﻿

![img](https://static001.geekbang.org/infoq/97/97afac83d32e43397ea484653bb6b1ec.png)

一个进程是应用正在运行的程序。而线程是进程中更小的一部分。当应用被启动，进程就被创建出来。程序可以创建线程来帮助其工作。操作系统会为进程分配私有的内存空间以供使用，当关闭程序时，这段私有的内存也会被释放。其实还有比线程更小的存在就是**协程，而协成是运行在线程中更小的单位。async/await就是基于协程实现的。**

## 进程间通信（IPC）

﻿

![img](https://static001.geekbang.org/infoq/b8/b8306ca61a00c7ccfa68476e61a0b105.png)

﻿

一个进程可以让操作系统开启另一个进程处理不同的任务。当两个进程需要通信时，可以时用IPC(Inter Process Communication)。

﻿多数程序被设计成使用IPC来进行进程间的通信，好处在于当一个进程给另一个进程发消息而没有回应时，并不影响当前的进程继续工作。

## 浏览器架构

﻿

借助进程和线程，浏览器可以被设计成单进程、多线程架构，或者利用IPC实现多进程、多线程架构。

﻿

![img](https://static001.geekbang.org/infoq/23/23162955642bb163b16a5d08bdbb3288.png)

这里我们以Chrome多进程架构介绍，在Chrome中存在这不同种类型的进程，它们各司其职。

﻿

![img](https://static001.geekbang.org/infoq/89/898bb4e5c529e5d3280b6787a7d261a3.png)

浏览器进程做为Chrome中最核心的进程管理着Chrome中的其他进程，而Renderer则负责渲染不同的站点。

### 进程工作内容

﻿

![img](https://static001.geekbang.org/infoq/7b/7bc84e5abe039f836a0af8002d4a63fd.png)

﻿

#### 浏览器进程（Browser process）

浏览器进程负责管理Chrome应用本身，包括地址栏、书签、前进和后退按钮。同时也负责可不见的功能，比如网络请求、文件按访问等，也负责其他进程的调度。

#### 渲染进程（Renderer process）

渲染进程负责站点的渲染，其中也包括JavaScript代码的运行，web worker的管理等。

#### 插件进程（Plugin process）

插件进程负责为浏览器提供各种额外的插件功能，例如flash。

#### GPU进程（GPU process）

GPU进程负责提供成像的功能。

当然还有其他像扩展进程或工具进程等其他进程，可以在Chrome的Task Manager面板中查看，面板中列出了运行的进程和其占用的CPU、内存情况。

### 多进程架构的好处

当我们访问一个站点时，渲染进程会负责运行站点的代码，渲染站点的页面，同时响应用户的交互动作，当我们在Chrome中打开三个页签同时访问三个站点时，如果其中一个没有响应，我们可以关闭它然后使用其他的页签，这是因为Chrome为每个站点创建一个独立的渲染进程，专门处理当前站点的渲染工作。如果所有的页面运行在同一个进程中，当有一个页面没有响应时，所有的页面就都卡住了。

﻿

![img](https://static001.geekbang.org/infoq/0e/0e8966939127660cee3f7aa05303721b.png)

另一个好处是，借助操作系统对进程安全的控制，浏览器可以将页面放置在沙箱中，站点的代码可以运行在隔离的环境中，保证核心进程的安全。

﻿

虽然多进程的架构优于单进程架构，但由于进程独享自己的私有内存，以渲染进程为例，虽然渲染的站点不同，但工作内容大体相似，为了完成渲染工作它们会在自己的内存中包含相同的功能，例如V8引擎（用于解析和运行Javascript），这意味着这部分相同的功能需要占用每个进程的内存空间。为了节省内存，Chrome限制了最大进程数，最大进程数取决于硬件的能力，同时**当使用多个页签访问相同的站点时浏览器不会创建新的渲染进程**。

### 面向服务的架构

Chrome将架构从多进程模型转变成面向服务。浏览器将功能以服务的方式提供，以解决多进程架构中的问题。

当Chrome运行在拥有强大硬件的计算机上时，会将一个服务以多个进程的方式实现，提高稳定性，当计算机硬件资源紧张时，则可以将多个服务放在一个进程中节省资源。

![img](https://static001.geekbang.org/infoq/72/72ab917eee34ffd4b8bb1352a8f73ad6.png)

### 基于站点隔离的渲染进程

﻿利用iframe我们可以在同一个页面访问不同站点的资源，但从安全的角度考虑，同源策略不允许一个站点在未得到同意的情况下访问其他站点的资源，所以从Chrome 67开始每个站点由独立的渲染进程处理被默认启用。

﻿

![img](https://static001.geekbang.org/infoq/fa/fa70a857e645bec8876257580a935282.png)

### 浏览器进程

![img](https://static001.geekbang.org/infoq/40/4006973a23f68ec28402353d48f91a57.png)

浏览器进程负责处理除了渲染外的大部分工作，浏览器进程包括几个线程：

- UI 线程负责绘制工具栏中的按钮、地址栏等。
- 网络线程负责从网络中获取数据。
- 存储线程负责文件等功能。

当我们在地址栏中输入一个地址时，浏览器进程中的UI线程最先得知这个动作，并开始处理。

## 一次访问

下面我们就从一次常见的访问入手，逐步了解浏览器是如何展示页面的。

### Step 1：输入处理

当我们在地址栏中输入时，UI线程会先判断我们输入的内容是要搜索的内容还是要访问一个站点，因为地址栏同时也是一个搜索框。

![img](https://static001.geekbang.org/infoq/d6/d619ac67749e633ae020812aa0b09bdd.png)

### Step 2：访问开始

当我们按下回车开始访问时，UI线程将借助网络线程访问站点资源. 浏览器页签的标题上会出现加载中的图标，同时网络线程会根据适当的网络协议，例如DNS lookup和TLS为这次请求建立连接。

![img](https://static001.geekbang.org/infoq/74/749ccda4aca068ed34465407973dc3f9.png)

当服务器返回给浏览器重定向请求时，网络线程会通知UI线程需要重定向，然后会以新的地址做开始请求资源。

### Step 3：处理响应数据

![img](https://static001.geekbang.org/infoq/a0/a049bd54e903004676c6f75ec11d373b.png)

当网络线程收到来自服务器的数据时，会试图从数据中的前面的一些字节中得到数据的类型（**Content-Type**），以试图了解数据的格式。

当返回的数据类型是HTML时，会将数据传递给渲染进程做进一步的渲染工作。但是如果数据类型是zip文件或者其他文件格式时，会将数据传递给下载管理器做进一步的文件预览或者下载工作。

![img](https://static001.geekbang.org/infoq/b4/b4797a3b5023a9de4ac03ad174e9bd3f.png)

在开始渲染之前，网络线程要先检查数据的安全性，这里也是浏览器保证安全的地方。如果返回的数据来自一些恶意的站点，网络线程会显示警告的页面。同时，Cross Origin Read Blocking(CORB)策略也会确保跨域的敏感数据不会被传递给渲染进程。

### Step 4：渲染过程

当所有的检查结束后，网络线程确信浏览器可以访问站点时，网络线程通知UI线程数据已经准备好了。UI线程会根据当前的站点找到一个渲染进程完成接下来的渲染工作。

![img](https://static001.geekbang.org/infoq/32/320ebdce97d9fb29329fd47c0c5f0c07.png)

在第二步，UI线程将请求地址传递给网络线程时，UI线程就已经知道了要访问的站点。此时UI线程就可以开始查找或启动一个渲染进程，这个动作与让网络线程下载数据是同时的。如果网络线程按照预期获取到数据，则渲染进程就已经可以开始渲染了，这个动作减少了从网络线程开始请求数据到渲染进程可以开始渲染页面的时间。当然，如果出现重定向的请求时，提前初始化的渲染进程可能就不会被使用了，但相比正常访问站点的场景，重定向往往是少数，在实际工作中，也需要根据特定的场景给出特定的方案，不必追求完美的方案。

### Step 5：提交访问

经历前面的步骤，数据和渲染进程都已经准备好了。浏览器进程会通过IPC向渲染进程提交这次访问，同时也会保证渲染进程可以通过网络线程继续获取数据。一旦浏览器进程收到来自渲染进程的确认完毕的消息，就意味着访问的过程结束了，文档渲染的过程就开始了。

这时，地址栏显示出表明安全的图标，同时显示出站点的信息。访问历史中也会加入当前的站点信息。为了能恢复访问历史信息，当页签或窗口被关闭时，访问历史的信息会被存储在硬盘中。

![img](https://static001.geekbang.org/infoq/b9/b9882cfdfbc700b2698116669bf40d4e.png)

### Extra Step：加载完毕

当访问被提交给渲染进程，渲染进程会继续加载页面资源并且渲染页面。当渲染进程"结束"渲染工作，会给浏览器进程发送消息，这个消息会在页面中所有子页面（frame）结束加载后发出，也就是onLoad事件触发后发送。当收到"结束"消息后，UI线程会隐藏页签标题上的加载状态图标，表明页面加载完毕。

但这里"结束"并不意味着所有的加载工作都结束了，因为可能还有JavaScript在加载额外的资源或者渲染新的视图。

![img](https://static001.geekbang.org/infoq/b9/b93f199ad2138be6e445d1ceb4416033.png)

## 访问不同的站点

﻿一次普通的访问到此就结束了。当我们输入另外一个地址时，浏览器进程会重复上面的过程。但是在开始新的访问前，会确认当前的站点是否关心`beforeunload`事件。

`beforeunload`事件可以提醒用户是否要访问新的站点或者关闭页签，如果用户拒绝则新的访问或关闭会被阻止。

由于所有的包括渲染、运行Javascript的工作都发生在渲染进程中，浏览器进程需要在新的访问开始前与渲染进程确认当前的站点是否关心`unload`。

![img](https://static001.geekbang.org/infoq/71/71402d5b3a3f7d5629a242727e606079.png)

如果一次访问是从一个渲染进程中发起的，例如用户点击一个链接或者运行JavaScript代码`location = 'http://newsite.com'`时，渲染进程首先检查`beforeunload`。然后再执行和浏览器进程初始化访问同样的步骤，只不过区别在于这样的访问请求是由渲染进程向浏览器进程发起的。

当新的站点请求被创建时，一个独立的渲染进程将被用于处理这个请求。为了支持像`unload`的事件触发，老的渲染进程需要保持住当前的状态。更详细的生命周期介绍可以参考[Page lifecycle](https://developers.google.com/web/updates/2018/07/page-lifecycle-api#overview_of_page_lifecycle_states_and_events)。

![img](https://static001.geekbang.org/infoq/e0/e074926b6b24cae624f6c1c906c16616.png)

## Service worker

Service worker是一种可以web开发者控制缓存的技术。如果Service worker被实现成从本地存储获取数据时，那么原本的请求就不会被浏览器发送给服务器了。

﻿

值得注意的是，Service worker中的代码是运行在渲染进程中的。当访问开始时，网络线程会根据域名检查是否有Service worker会处理当前地址的请求，如果有，则UI线程会找到对应的渲染进程去执行Service worker的代码，而Service worker可以让开发者决定这个请求是从本地存储还是从网络中获取数据。

![img](https://static001.geekbang.org/infoq/8c/8c45c55d238b901239d0eb4bd40f2892.png)

﻿

![img](https://static001.geekbang.org/infoq/fd/fdeaee16c665e81bc59f42122080916f.png)

### 访问预加载

如果Service worker最终决定要从网络中获取数据时，我们会发现这种跨进程的通信会造成一些延迟。[Navigation Preload](https://developers.google.com/web/updates/2017/02/navigation-preload)是一种可以在Service worker启动的同时加载资源的优化机制。借助特殊的请求头，服务器可以决定返回什么样的内容给浏览器。

﻿

![img](https://static001.geekbang.org/infoq/21/212631fa2520d730f17b0461ddae71d6.png)

### 渲染进程负责页面的内容

渲染进程负责所有发生在浏览器页签中的事情。在一个渲染进程中，主线程负责解析，编译或运行代码等工作，当我们使用Worker时，Worker线程会负责运行一部分代码。合成线程和光栅线程是也是运行在渲染进程中的，负责更高效和顺畅的渲染页面。

渲染进程最重要的工作就是将HTML、CSS和Javascript代码转换成一个可以与用户产生交互的页面。

![img](https://static001.geekbang.org/infoq/bd/bdfa66a4ef1fbd2805797bc4cd90f8d8.png)

### 解析过程

下面的章节主要介绍渲染进程如何将从网络线程中获取的文本转化成图像的过程。

#### DOM的创建

当渲染进程接收到来自浏览器进程提交访问的消息后就开始接受HTML数据，主线程开始解析HTML文本字符串，并且将其转化成**Document Object Model（DOM）**。

DOM是一种浏览器内部用于表达页面结构的数据，同时也为Web开发者提供了操作页面元素的接口，让web开发者可以在Javascript代码中获取和操作页面中的元素。

将HTML文本转化成DOM的标准被[HTML Standard](https://html.spec.whatwg.org/)定义。我们会发现在转化过程中浏览器从来不会抛出异常，类似关闭标签的丢失，开始、关闭标签匹配错误等等。这是因为HTML标准中定义了要静默的处理这些错误，如果对此感兴趣可以阅读[An introduction to error handling and strange cases in the parser](https://html.spec.whatwg.org/multipage/parsing.html#an-introduction-to-error-handling-and-strange-cases-in-the-parser)。

#### 额外资源的加载

一个网站通常还会使用类似图片，样式文件和JavaScript代码等额外的资源。这些资源也需要从网络或缓存中获取。主线程在转化HTML的过程中理应挨个加载它们，但是为了提高效率，预加载扫描（Preload Scanner）与转换过程会同时运行着。当预加载扫描在分析器分析HTML过程中发现了类似img或link这样的标签时，就会发送请求给浏览器进程的网络线程，而主线程会根据这些额外资源是否会阻塞转化过程而决定是否等待资源加载完毕。

![img](https://static001.geekbang.org/infoq/fa/fa689d6a31b8687522c58774c8d9d064.png)

#### JavaScript会阻塞转化过程

当HTML分析器发现`<script>`标签时，会暂停接下来的HTML转化工作，然后加载、解析并且运行Javascript代码。因为在Javascript代码中可能会使用类似`document.write`这样的API去改变DOM的结构。这就是为什么HTML分析器必须等待Javascript代码运行结束才能继续分析的原因。

#### 告诉浏览器要如何加载资源

如果我们的Javascript代码并不需要改变DOM，可以为`<script>`标签添加`async`或`defer`属性，这样浏览器就会异步的加载这些资源并且不会阻塞HTML转化过程。**如果script标签是由JavaScript代码创建的，标签的async属性会默认为true。**同时我们也可以使用一些预加载技术，比如`<link ref="preload">`来通知浏览器这些资源需要越快下载越好。

#### 样式计算（Style calculation）

对于展示一个页面，光有DOM是不够的，因为我们还需要样式来让页面变得更美观。主线程会解析样式（CSS）并决定每个DOM元素的样式。这些样式取决于CSS选择器的范围，在浏览器开发者工具中我们可以看到这些信息。

![img](https://static001.geekbang.org/infoq/fb/fb7d195814a04c6ed98eab8dbda477c6.png)

即使我们没有给DOM指定任何的样式，`<h1>`标签也会比`<h2>`标签显示的大。这是因为浏览器为不同的标签内置了不同的样式。可以通过[Chromium源代码](https://source.chromium.org/chromium/chromium/src/+/master:third_party/blink/renderer/core/html/resources/html.css)得到这些默认样式。

#### 布局（layout）

完成了样式计算工作后，渲染进程已经知道了DOM的结构和每个节点的样式，但是依然不足以渲染一个页面。想象一下，让你在电话中向朋友描述一张图：“图中有一个大红色圆和一个小的、蓝色的方块”是不足以让朋友知道这张图到底是什么样的。

![img](https://static001.geekbang.org/infoq/06/0658494542e14f7db66b913c02c03202.png)

布局是为元素指定几何信息的过程。主线程遍历DOM结构中的元素及其样式，同时创建出带有坐标和元素尺寸信息的布局树（Layout tree）。布局树的结构与DOM树的结构十分相似，但只包含将会在页面中显示的元素。**当一个元素的样式被设置成display: none时，元素就不会出现在布局树中，但那些样式被设置成visiblility：hidden的元素会出现在布局树中。**相似的，当我们使用一个包含内容的伪元素（例如`p::before { content: 'Hi!' }`）时，元素会出现在布局树中即使这个元素不存在于DOM树中，这也是为什么我们**使用DOM提供的API无法获取伪元素**的原因。

![img](https://static001.geekbang.org/infoq/0c/0c1de85206f0d177f93a70931a0f8272.png)

描述页面布局信息是一项具有挑战性的工作，即使在只有块元素的页面中也必须要考虑字体的大小和在哪里换行，因为在计算下一个元素的位置时需要知道上一个元素的尺寸和形状。

CSS可以让元素浮动、可以让元素在父元素中溢出，可以改变文字的方向。可以想象，在布局这个阶段是多么繁重的工作。在Chrome中，有一整个团队在维护布局工作，更详细的信息可以观看[视频](https://www.youtube.com/watch?v=Y5Xa4H2wtVA)。

#### 绘制（Paint）

![img](https://static001.geekbang.org/infoq/d8/d8fe81b968531c8b3d4767006ea9725d.png)

有了DOM、样式和布局还是无法完成渲染工作。试想，当我们试图复制一张图画。我们知道图画中元素的尺寸、形状和位置，我们还需要知道绘制这些元素的顺序。

例如，当一个元素z-index属性被设置后，绘制的顺序会导致渲染成错误的结果。

![img](https://static001.geekbang.org/infoq/11/116fb1ec64e618a7562788911bca8d75.png)

在这个阶段，主线程遍历布局树并创建绘制记录，绘制记录是一系列由绘制步骤组成的流程，例如先绘制背景，然后是文字，然后是形状。

![img](https://static001.geekbang.org/infoq/a6/a68fd128fc59b9b2bed3511fcf223c94.png)

#### 渲染过程是昂贵的

在渲染过程中，任何一个步骤中产生的数据变化都会引起后续一系列的的变化。例如，当布局树改变时，绘制需要重构页面中变化的部分。

当一些元素有动画发生时，浏览器需要在每一帧中绘制这些元素。当无法保证每一帧绘制的连续性时，用户就会感觉到卡顿。

![img](https://static001.geekbang.org/infoq/54/54a9da693ebba8579317ff57be2993ea.png)

正常情况下渲染操作可以与屏幕刷新保持同步，但由于这些操作运行在主线程中，也就意味这些操作可能被正在运行的Javascript代码所阻塞。

![img](https://static001.geekbang.org/infoq/6b/6bd8886a8c2b26a72105c7a2fba7bf3a.png)

为了不影响渲染操作，我们可以将Javascript操作优化成小块，然后使用`requestAnimationFrame()`，关于如何优化可以参考[Optimize JavaScript Exectuion](https://developers.google.com/web/fundamentals/performance/rendering/optimize-javascript-execution)。当需要大量计算时，也可以使用Worker来避免阻塞主进程。

![img](https://static001.geekbang.org/infoq/45/45d6db6a394174c914eb37e13743f94d.png)

#### 合成（Compositing）

现在，浏览器已经知道了文档结构、每一个元素的样式，元素的几何信息，绘制的顺序。将这些信息转化成屏幕上像素的过程叫做光栅化，光栅化是图形学的范畴。

![img](https://static001.geekbang.org/infoq/9e/9ed512afc63c664458faf1bd42247cc0.gif)

传统的做法是将可视区域的内容进行光栅化。随着用户滚动页面，不断的光栅化更多的区域。然而对于现代浏览器，有着更复杂的的过程，这个过程被称做合成。

![img](https://static001.geekbang.org/infoq/ae/ae1b6d19e8aeb45841f04bbfa72760dd.gif)

合成是一种将页面拆分成多层的技术，合成线程可以将各个层在不同线程中光栅化，再组合成一个页面。当滚动时，如果层已经被光栅化，则会使用已经存在的层合成新的帧，动画则可以通过移动层来实现。

﻿

#### 层（Layer）

﻿

为了决定层包含哪些元素，主线程需要遍历布局树以找到需要生成的部分。对开发者来说，当某一部分需要用独立的层渲染，我们可以使用css属性`will-change`让浏览器创建层，关于浏览器如何生成层的标准可自行查阅。

﻿

![img](https://static001.geekbang.org/infoq/b0/b08901dee7af151982f600e6a7a6ba43.png)

layer.png

虽然通过分层可以优化浏览器性能，但并不意味着应该给每个元素一个层，过多的层反而影响性能，所以在层的划分上应该具体形况具体分析。

﻿

#### 栅格线程与合成线程

当布局树和绘制顺序确定以后，主线程会将这些信息提交给合成线程。合成线程会光栅化各个层。一个层包含的内容可能是一个完整的页面，也可能是页面的部分，所以合成线程将层拆分成许多块，并将它们发送给栅格线程。栅格线程光栅化这些块并将它们存储在GPU缓存中。

![img](https://static001.geekbang.org/infoq/37/371b5fa654d59f0c8ccb2f4f0658c20a.png)

合成线程可以决定栅格线程光栅块的优先级，这样可以保证用户能看到的部分可以先被光栅化。一个层也会包含多种块以支持类似缩放这样的功能。

当块被光栅化后，合成线程会使用draw quads收集这些信息并创建合成帧（Compositor frame）。

#### Draw quads

存储在缓存中，包含类似块位置这样的信息，用于描述如何使用块合成页面。

#### Compositor frame

用于存储表现页面一帧中包含哪些Draw quads的集合。

然后一个合成帧被提交给浏览器进程。这时如果浏览器UI有变化，或者插件的UI有变化时，另一个合成帧就会被创建出来。所以每当有交互发生时，合成线程就会创建更多的合成帧然后通过GPU将新的部分渲染出来。

![img](https://static001.geekbang.org/infoq/39/397d4949099dd6d1aaffcb55e8678e37.png)

合成的好处在于其独立于主线程。合成线程不需要等待样式计算和Javascript代码的运行。这也是为什么合成更适合优化交互性能，但如果布局或者绘制需要重新计算则主线程是必须要参与的。

﻿

本质上，浏览器的渲染过程就是将文本转换成图像的过程，而当用户与页面发生交互动作时，则显示新的图像。在这个过程中由渲染进程中的主线程完成计算工作，由合成线程和栅格线程完成图像的绘制工作。而在计算过程中，还有强制布局、重排、重绘等更加细节的概念会在后面的文章中做讲解。

### 从浏览器的角度看事件

当我们听到事件时，通常会联想到在一个文本框中输入或者单击鼠标，但从浏览器的角度看，输入事件意味着所有的用户动作。鼠标滚轮滚动或者屏幕触摸都是输入事件。

﻿

当用户与页面发生交互时，浏览器进程首先接收到事件，然而，浏览器进程只关心事件发生时是在哪个页签中，所以浏览器进程会将事件类型和位置信息等发送给负责当前页签的渲染进程，渲染进程会恰当的找到事件发生的元素并且触发事件监听器。

![img](https://static001.geekbang.org/infoq/7f/7f8581ec78d48302c8ea81f713cdfa56.png)

### 合成线程对事件的处理

在前面的章节中，我们知道了合成线程可以通过合成技术合成不同的光栅层优化性能，如果页面并不监听任何事件，合成线程可以完全独立于主线程生成新的合成帧。但如果页面监听了事件呢？

#### 标记“慢滚动”区域

由于运行Javascript是主线程的工作，当页面被合成线程合成过，合成线程会标记那些有事件监听的区域。有了这些信息，当事件发生在响应的区域时，合成线程就会将事件发送给主线程处理。如果在非事件监听区域，则渲染进程直接创建新的帧而不关心主线程。

![img](https://static001.geekbang.org/infoq/6f/6fda6b7355162ca393787e870630a083.png)

#### 在事件监听时标记

在web开发中常见的方式就是事件代理。利用事件冒泡，我们可以在目标元素的上层元素中监听事件。参照下面的代码。

```
document.body.addEventListener('touchstart', event => {
  if (event.target === area) {
    event.preventDefault();
  }
});
```

﻿通过这种写法，可以更高效的监听事件。但如果从浏览器的角度看，此时整个页面会被标记成“慢滚动”区域。这意味着虽然页面中的某些部分并不需要事件监听，但合成线程依然要在每次交互发生后等待主线程处理事件，合成线程的优化效果不复存在。

![img](https://static001.geekbang.org/infoq/f2/f2e60c76e1cb2b104a46c0da8787d229.png)

为了解决这个问题，我们可在事件代理时传入`passive: true`**（IE不支持）**参数。这样告诉渲染线程，依然需要将事件发送给主线程处理，但不需要等待。

```
document.body.addEventListener('touchstart', event => {
    if (event.target === area) {
        event.preventDefault()
    }
 }, {passive: true});
```

关于使用passive改善滚屏性能，可以参考[MDN 使用passive改善滚屏性能](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener#使用_passive_改善的滚屏性能)。

#### 查找事件目标

![img](https://static001.geekbang.org/infoq/0e/0eb159798080da4adc7ffb76cf184ffa.png)

当渲染线程将事件发送给主线程后，第一件事就是找到事件触发的目标。通过在渲染过程中生成的绘制信息，可以根据坐标找到目标元素。

#### 减少发送给主线程的事件数量

为了保证动画的顺畅，需要显示器在每秒刷新60次。对于典型的触摸事件由合成线程提交给主线程的事件频率可以达到每秒60-120次，对于典型的鼠标事件每秒会发送100次。事件发送的频率通常比屏幕刷新频率要高。

如果类似`touchmove`这样的事件每秒向主线程发送120次可能会造成主线程执行时间过长而影响性能。

﻿

![img](https://static001.geekbang.org/infoq/61/6101cc07df5e744efb2a88208f1d1e52.png)

为了减少发送给主线程的事件数量，Chrome合并了连续的事件。类似`wheel`，`mousewheel`，`mousemove`，`pointermove`，`touchmove`这样的事件会被延迟到下一次`requestAnimationFrame`前触发.

﻿

![img](https://static001.geekbang.org/infoq/53/53005a8114a736ca071a333946664ffd.png)

而任何的离散事件，类似`keydown`, `keyup`, `mouseup`, `mousedown`, `touchstart`和 `touchend`都会立即被发送给主线程处理。

### 总结

到此，我们已经可以通过从用户在浏览器地址栏中的一次输入到页面图像的显示了解浏览器是如何工作的。这里我们总结一下。

- 浏览器进程做为最重要的进程负责大多数页签外部的工作，包括地址栏显示、网络请求、页签状态管理等。
- 不同的渲染进程负责不同的站点渲染工作，渲染进程间彼此独立。
- 渲染进程在渲染页面的过程中会通过浏览器进程获取站点资源，只有安全的资源才会被渲染进程接收到。
- 渲染进程中主线程负责除了图像生成外绝大多数工作，如何减少主线程上代码的运行是交互性能优化的关键。
- 渲染进程中的合成线程和栅格线程负责图像生成，利用分层技术可以优化图像生成的效率。
- 当用户与页面发生交互时，事件的传播途径从浏览器进程到渲染进程的合成线程再根据事件监听的区域决定是否要传递给渲染进程的主线程处理。

# ﻿8.浏览器的工作原理

## 简介

浏览器很可能是使用最广的软件。在这篇入门文章中，我将会介绍它们的幕后工作原理。我们会了解到，从您在地址栏输入 `google.com` 直到您在浏览器屏幕上看到 Google 首页的整个过程中都发生了些什么。

### 我们要讨论的浏览器

目前使用的主流浏览器有五个：Internet Explorer、Firefox、Safari、Chrome 浏览器和 Opera。本文中以开放源代码浏览器为例，即 Firefox、Chrome 浏览器和 Safari（部分开源）。根据 [StatCounter 浏览器统计数据](http://gs.statcounter.com/)，目前（2011 年 8 月）Firefox、Safari 和 Chrome 浏览器的总市场占有率将近 60%。由此可见，如今开放源代码浏览器在浏览器市场中占据了非常坚实的部分。

### 浏览器的主要功能

浏览器的主要功能就是向服务器发出请求，在浏览器窗口中展示您选择的网络资源。这里所说的资源一般是指 HTML 文档，也可以是 PDF、图片或其他的类型。资源的位置由用户使用 URI（统一资源标示符）指定。

浏览器解释并显示 HTML 文件的方式是在 HTML 和 CSS 规范中指定的。这些规范由网络标准化组织 W3C（万维网联盟）进行维护。
多年以来，各浏览器都没有完全遵从这些规范，同时还在开发自己独有的扩展程序，这给网络开发人员带来了严重的兼容性问题。如今，大多数的浏览器都是或多或少地遵从规范。

浏览器的用户界面有很多彼此相同的元素，其中包括：

- 用来输入 URI 的地址栏
- 前进和后退按钮
- 书签设置选项
- 用于刷新和停止加载当前文档的刷新和停止按钮
- 用于返回主页的主页按钮

奇怪的是，浏览器的用户界面并没有任何正式的规范，这是多年来的最佳实践自然发展以及彼此之间相互模仿的结果。HTML5 也没有定义浏览器必须具有的用户界面元素，但列出了一些通用的元素，例如地址栏、状态栏和工具栏等。当然，各浏览器也可以有自己独特的功能，比如 Firefox 的下载管理器。

### 浏览器的高层结构

浏览器的主要组件为 ([1.1](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#1_1))：

1. **用户界面** - 包括地址栏、前进/后退按钮、书签菜单等。除了浏览器主窗口显示的您请求的页面外，其他显示的各个部分都属于用户界面。
2. **浏览器引擎** - 在用户界面和呈现引擎之间传送指令。
3. **呈现引擎** - 负责显示请求的内容。如果请求的内容是 HTML，它就负责解析 HTML 和 CSS 内容，并将解析后的内容显示在屏幕上。
4. **网络** - 用于网络调用，比如 HTTP 请求。其接口与平台无关，并为所有平台提供底层实现。
5. **用户界面后端** - 用于绘制基本的窗口小部件，比如组合框和窗口。其公开了与平台无关的通用接口，而在底层使用操作系统的用户界面方法。
6. **JavaScript 解释器**。用于解析和执行 JavaScript 代码。
7. **数据存储**。这是持久层。浏览器需要在硬盘上保存各种数据，例如 Cookie。新的 HTML 规范 (HTML5) 定义了“网络数据库”，这是一个完整（但是轻便）的浏览器内数据库。

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/layers.png)图：浏览器的主要组件。

值得注意的是，和大多数浏览器不同，Chrome 浏览器的每个标签页都分别对应一个呈现引擎实例。每个标签页都是一个独立的进程。

## 呈现引擎

呈现引擎的作用嘛...当然就是“呈现”了，也就是在浏览器的屏幕上显示请求的内容。

默认情况下，呈现引擎可显示 HTML 和 XML 文档与图片。通过插件（或浏览器扩展程序），还可以显示其他类型的内容；例如，使用 PDF 查看器插件就能显示 PDF 文档。但是在本章中，我们将集中介绍其主要用途：显示使用 CSS 格式化的 HTML 内容和图片。

### 呈现引擎

本文所讨论的浏览器（Firefox、Chrome 浏览器和 Safari）是基于两种呈现引擎构建的。Firefox 使用的是 Gecko，这是 Mozilla 公司“自制”的呈现引擎。而 Safari 和 Chrome 浏览器使用的都是 WebKit。

WebKit 是一种开放源代码呈现引擎，起初用于 Linux 平台，随后由 Apple 公司进行修改，从而支持苹果机和 Windows。有关详情，请参阅 [webkit.org](http://webkit.org/)。

### 主流程

呈现引擎一开始会从网络层获取请求文档的内容，内容的大小一般限制在 8000 个块以内。

然后进行如下所示的基本流程：

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/flow.png)图：呈现引擎的基本流程。

呈现引擎将开始解析 HTML 文档，并将各标记逐个转化成“内容树”上的 [DOM](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#DOM) 节点。同时也会解析外部 CSS 文件以及样式元素中的样式数据。HTML 中这些带有视觉指令的样式信息将用于创建另一个树结构：[呈现树](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#Render_tree_construction)。

呈现树包含多个带有视觉属性（如颜色和尺寸）的矩形。这些矩形的排列顺序就是它们将在屏幕上显示的顺序。

呈现树构建完毕之后，进入“[布局](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#layout)”处理阶段，也就是为每个节点分配一个应出现在屏幕上的确切坐标。下一个阶段是[绘制](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#Painting) - 呈现引擎会遍历呈现树，由用户界面后端层将每个节点绘制出来。

需要着重指出的是，这是一个渐进的过程。为达到更好的用户体验，呈现引擎会力求尽快将内容显示在屏幕上。它不必等到整个 HTML 文档解析完毕之后，就会开始构建呈现树和设置布局。在不断接收和处理来自网络的其余内容的同时，呈现引擎会将部分内容解析并显示出来。

#### 主流程示例

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/webkitflow.png)图：WebKit 主流程

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image008.jpg)图：Mozilla 的 Gecko 呈现引擎主流程 ([3.6](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#3_6))

从图 3 和图 4 可以看出，虽然 WebKit 和 Gecko 使用的术语略有不同，但整体流程是基本相同的。

Gecko 将视觉格式化元素组成的树称为“框架树”。每个元素都是一个框架。WebKit 使用的术语是“呈现树”，它由“呈现对象”组成。对于元素的放置，WebKit 使用的术语是“布局”，而 Gecko 称之为“重排”。对于连接 DOM 节点和可视化信息从而创建呈现树的过程，WebKit 使用的术语是“附加”。有一个细微的非语义差别，就是 Gecko 在 HTML 与 DOM 树之间还有一个称为“内容槽”的层，用于生成 DOM 元素。我们会逐一论述流程中的每一部分：

### 解析 - 综述

解析是呈现引擎中非常重要的一个环节，因此我们要更深入地讲解。首先，来介绍一下解析。

解析文档是指将文档转化成为有意义的结构，也就是可让代码理解和使用的结构。解析得到的结果通常是代表了文档结构的节点树，它称作解析树或者语法树。

示例 - 解析 2 + 3 - 1 这个表达式，会返回下面的树：

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image009.png)图：数学表达式树节点

#### 语法

解析是以文档所遵循的语法规则（编写文档所用的语言或格式）为基础的。所有可以解析的格式都必须对应确定的语法（由词汇和语法规则构成）。这称为[与上下文无关的语法](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#context_free_grammar)。人类语言并不属于这样的语言，因此无法用常规的解析技术进行解析。

#### 解析器和词法分析器的组合

解析的过程可以分成两个子过程：词法分析和语法分析。

词法分析是将输入内容分割成大量标记的过程。标记是语言中的词汇，即构成内容的单位。在人类语言中，它相当于语言字典中的单词。

语法分析是应用语言的语法规则的过程。

解析器通常将解析工作分给以下两个组件来处理：**词法分析器**（有时也称为标记生成器），负责将输入内容分解成一个个有效标记；而**解析器**负责根据语言的语法规则分析文档的结构，从而构建解析树。词法分析器知道如何将无关的字符（比如空格和换行符）分离出来。

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image011.png)图：从源文档到解析树

解析是一个迭代的过程。通常，解析器会向词法分析器请求一个新标记，并尝试将其与某条语法规则进行匹配。如果发现了匹配规则，解析器会将一个对应于该标记的节点添加到解析树中，然后继续请求下一个标记。

如果没有规则可以匹配，解析器就会将标记存储到内部，并继续请求标记，直至找到可与所有内部存储的标记匹配的规则。如果找不到任何匹配规则，解析器就会引发一个异常。这意味着文档无效，包含语法错误。

#### 翻译

很多时候，解析树还不是最终产品。解析通常是在翻译过程中使用的，而翻译是指将输入文档转换成另一种格式。编译就是这样一个例子。编译器可将源代码编译成机器代码，具体过程是首先将源代码解析成解析树，然后将解析树翻译成机器代码文档。

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image013.png)图：编译流程

#### 解析示例

在图 5 中，我们通过一个数学表达式建立了解析树。现在，让我们试着定义一个简单的数学语言，用来演示解析的过程。



词汇：我们用的语言可包含整数、加号和减号。

语法：

1. 构成语言的语法单位是表达式、项和运算符。
2. 我们用的语言可以包含任意数量的表达式。
3. 表达式的定义是：一个“项”接一个“运算符”，然后再接一个“项”。
4. 运算符是加号或减号。
5. 项是一个整数或一个表达式

让我们分析一下 2 + 3 - 1。
匹配语法规则的第一个子串是 2，而根据第 5 条语法规则，这是一个项。匹配语法规则的第二个子串是 2 + 3，而根据第 3 条规则（一个项接一个运算符，然后再接一个项），这是一个表达式。下一个匹配项已经到了输入的结束。2 + 3 - 1 是一个表达式，因为我们已经知道 2 + 3 是一个项，这样就符合“一个项接一个运算符，然后再接一个项”的规则。2 + + 不与任何规则匹配，因此是无效的输入。

#### 词汇和语法的正式定义

词汇通常用[正则表达式](http://www.regular-expressions.info/)表示。

例如，我们的示例语言可以定义如下：

```
INTEGER :0|[1-9][0-9]*
PLUS : +
MINUS: -
```

正如您所看到的，这里用正则表达式给出了整数的定义。

语法通常使用一种称为 [BNF](http://en.wikipedia.org/wiki/Backus–Naur_Form) 的格式来定义。我们的示例语言可以定义如下：

```
expression :=  term  operation  term
operation :=  PLUS | MINUS
term := INTEGER | expression
```

之前我们说过，如果语言的语法是与上下文无关的语法，就可以由常规解析器进行解析。与上下文无关的语法的直观定义就是可以完全用 BNF 格式表达的语法。有关正式定义，请参阅[关于与上下文无关的语法的维基百科文章](http://en.wikipedia.org/wiki/Context-free_grammar)。

#### 解析器类型

有两种基本类型的解析器：自上而下解析器和自下而上解析器。直观地来说，自上而下的解析器从语法的高层结构出发，尝试从中找到匹配的结构。而自下而上的解析器从低层规则出发，将输入内容逐步转化为语法规则，直至满足高层规则。

让我们来看看这两种解析器会如何解析我们的示例：

自上而下的解析器会从高层的规则开始：首先将 2 + 3 标识为一个表达式，然后将 2 + 3 - 1 标识为一个表达式（标识表达式的过程涉及到匹配其他规则，但是起点是最高级别的规则）。

自下而上的解析器将扫描输入内容，找到匹配的规则后，将匹配的输入内容替换成规则。如此继续替换，直到输入内容的结尾。部分匹配的表达式保存在解析器的堆栈中。

| 堆栈         | 输入      |
| ------------ | --------- |
|              | 2 + 3 - 1 |
| 项           | + 3 - 1   |
| 项运算       | 3 - 1     |
| 表达式       | - 1       |
| 表达式运算符 | 1         |
| 表达式       |           |

这种自下而上的解析器称为移位归约解析器，因为输入在向右移位（设想有一个指针从输入内容的开头移动到结尾），并且逐渐归约到语法规则上。

#### 自动生成解析器

有一些工具可以帮助您生成解析器，它们称为解析器生成器。您只要向其提供您所用语言的语法（词汇和语法规则），它就会生成相应的解析器。创建解析器需要对解析有深刻理解，而人工创建并优化解析器并不是一件容易的事情，所以解析器生成器是非常实用的。

WebKit 使用了两种非常有名的解析器生成器：用于创建词法分析器的 [Flex](http://en.wikipedia.org/wiki/Flex_lexical_analyser) 以及用于创建解析器的 [Bison](http://www.gnu.org/software/bison/)（您也可能遇到 Lex 和 Yacc 这样的别名）。Flex 的输入是包含标记的正则表达式定义的文件。Bison 的输入是采用 BNF 格式的语言语法规则。

### HTML 解析器

HTML 解析器的任务是将 HTML 标记解析成解析树。

#### HTML 语法定义

HTML 的词汇和语法在 W3C 组织创建的[规范](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#w3c)中进行了定义。当前的版本是 HTML4，HTML5 正在处理过程中。

#### 非与上下文无关的语法

正如我们在解析过程的简介中已经了解到的，语法可以用 BNF 等格式进行正式定义。

很遗憾，所有的常规解析器都不适用于 HTML（我并不是开玩笑，它们可以用于解析 CSS 和 JavaScript）。HTML 并不能很容易地用解析器所需的与上下文无关的语法来定义。

有一种可以定义 HTML 的正规格式：DTD（Document Type Definition，文档类型定义），但它不是与上下文无关的语法。

这初看起来很奇怪：HTML 和 XML 非常相似。有很多 XML 解析器可以使用。HTML 存在一个 XML 变体 (XHTML)，那么有什么大的区别呢？

区别在于 HTML 的处理更为“宽容”，它允许您省略某些隐式添加的标记，有时还能省略一些起始或者结束标记等等。和 XML 严格的语法不同，HTML 整体来看是一种“软性”的语法。

显然，这种看上去细微的差别实际上却带来了巨大的影响。一方面，这是 HTML 如此流行的原因：它能包容您的错误，简化网络开发。另一方面，这使得它很难编写正式的语法。概括地说，HTML 无法很容易地通过常规解析器解析（因为它的语法不是与上下文无关的语法），也无法通过 XML 解析器来解析。

#### HTML DTD

HTML 的定义采用了 DTD 格式。此格式可用于定义 [SGML](http://en.wikipedia.org/wiki/Standard_Generalized_Markup_Language) 族的语言。它包括所有允许使用的元素及其属性和层次结构的定义。如上文所述，HTML DTD 无法构成与上下文无关的语法。

DTD 存在一些变体。严格模式完全遵守 HTML 规范，而其他模式可支持以前的浏览器所使用的标记。这样做的目的是确保向下兼容一些早期版本的内容。最新的严格模式 DTD 可以在这里找到：[www.w3.org/TR/html4/strict.dtd](http://www.w3.org/TR/html4/strict.dtd)

#### DOM

解析器的输出“解析树”是由 DOM 元素和属性节点构成的树结构。DOM 是文档对象模型 (Document Object Model) 的缩写。它是 HTML 文档的对象表示，同时也是外部内容（例如 JavaScript）与 HTML 元素之间的接口。
解析树的根节点是“[Document](http://www.w3.org/TR/1998/REC-DOM-Level-1-19981001/level-one-core.html#i-Document)”对象。

DOM 与标记之间几乎是一一对应的关系。比如下面这段标记：

```
<html>
  <body>
    <p>
      Hello World
    </p>
    <div> <img src="example.png"/></div>
  </body>
</html>
```

可翻译成如下的 DOM 树：



![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image015.png)图：示例标记的 DOM 树

和 HTML 一样，DOM 也是由 W3C 组织指定的。请参见 [www.w3.org/DOM/DOMTR](http://www.w3.org/DOM/DOMTR)。这是关于文档操作的通用规范。其中一个特定模块描述针对 HTML 的元素。HTML 的定义可以在这里找到：[www.w3.org/TR/2003/REC-DOM-Level-2-HTML-20030109/idl-definitions.html](http://www.w3.org/TR/2003/REC-DOM-Level-2-HTML-20030109/idl-definitions.html)。

我所说的树包含 DOM 节点，指的是树是由实现了某个 DOM 接口的元素构成的。浏览器在具体的实现中会有一些供内部使用的其他属性。

#### 解析算法

我们在之前章节已经说过，HTML 无法用常规的自上而下或自下而上的解析器进行解析。

原因在于：

1. 语言的宽容本质。
2. 浏览器历来对一些常见的无效 HTML 用法采取包容态度。
3. 解析过程需要不断地反复。源内容在解析过程中通常不会改变，但是在 HTML 中，脚本标记如果包含 `document.write`，就会添加额外的标记，这样解析过程实际上就更改了输入内容。

由于不能使用常规的解析技术，浏览器就创建了自定义的解析器来解析 HTML。

[HTML5 规范详细地描述了解析算法](http://www.whatwg.org/specs/web-apps/current-work/multipage/parsing.html)。此算法由两个阶段组成：标记化和树构建。

标记化是词法分析过程，将输入内容解析成多个标记。HTML 标记包括起始标记、结束标记、属性名称和属性值。

标记生成器识别标记，传递给树构造器，然后接受下一个字符以识别下一个标记；如此反复直到输入的结束。

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image017.png)图：HTML 解析流程（摘自 HTML5 规范）

#### 标记化算法

该算法的输出结果是 HTML 标记。该算法使用状态机来表示。每一个状态接收来自输入信息流的一个或多个字符，并根据这些字符更新下一个状态。当前的标记化状态和树结构状态会影响进入下一状态的决定。这意味着，即使接收的字符相同，对于下一个正确的状态也会产生不同的结果，具体取决于当前的状态。该算法相当复杂，无法在此详述，所以我们通过一个简单的示例来帮助大家理解其原理。

基本示例 - 将下面的 HTML 代码标记化：

```
<html>
  <body>
    Hello world
  </body>
</html>
```

初始状态是数据状态。遇到字符 `<` 时，状态更改为**“标记打开状态”**。接收一个 `a-z` 字符会创建“起始标记”，状态更改为**“标记名称状态”**。这个状态会一直保持到接收 `>` 字符。在此期间接收的每个字符都会附加到新的标记名称上。在本例中，我们创建的标记是 `html` 标记。

遇到 `>` 标记时，会发送当前的标记，状态改回**“数据状态”**。`<body>` 标记也会进行同样的处理。目前 `html` 和 `body` 标记均已发出。现在我们回到**“数据状态”**。接收到 `Hello world` 中的 `H` 字符时，将创建并发送字符标记，直到接收 `</body>` 中的 `<`。我们将为 `Hello world` 中的每个字符都发送一个字符标记。

现在我们回到**“标记打开状态”**。接收下一个输入字符 `/` 时，会创建 `end tag token` 并改为**“标记名称状态”**。我们会再次保持这个状态，直到接收 `>`。然后将发送新的标记，并回到**“数据状态”**。`</html>` 输入也会进行同样的处理。

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image019.png)图：对示例输入进行标记化

#### 树构建算法

在创建解析器的同时，也会创建 Document 对象。在树构建阶段，以 Document 为根节点的 DOM 树也会不断进行修改，向其中添加各种元素。标记生成器发送的每个节点都会由树构建器进行处理。规范中定义了每个标记所对应的 DOM 元素，这些元素会在接收到相应的标记时创建。这些元素不仅会添加到 DOM 树中，还会添加到开放元素的堆栈中。此堆栈用于纠正嵌套错误和处理未关闭的标记。其算法也可以用状态机来描述。这些状态称为“插入模式”。

让我们来看看示例输入的树构建过程：

```
<html>
  <body>
    Hello world
  </body>
</html>
```

树构建阶段的输入是一个来自标记化阶段的标记序列。第一个模式是**“initial mode”**。接收 HTML 标记后转为**“before html”**模式，并在这个模式下重新处理此标记。这样会创建一个 HTMLHtmlElement 元素，并将其附加到 Document 根对象上。

然后状态将改为**“before head”**。此时我们接收“body”标记。即使我们的示例中没有“head”标记，系统也会隐式创建一个 HTMLHeadElement，并将其添加到树中。

现在我们进入了**“in head”**模式，然后转入**“after head”**模式。系统对 body 标记进行重新处理，创建并插入 HTMLBodyElement，同时模式转变为**“in body”**。

现在，接收由“Hello world”字符串生成的一系列字符标记。接收第一个字符时会创建并插入“Text”节点，而其他字符也将附加到该节点。

接收 body 结束标记会触发**“after body”**模式。现在我们将接收 HTML 结束标记，然后进入**“after after body”**模式。接收到文件结束标记后，解析过程就此结束。

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image022.gif)图：示例 HTML 的树构建

#### 解析结束后的操作

在此阶段，浏览器会将文档标注为交互状态，并开始解析那些处于“deferred”模式的脚本，也就是那些应在文档解析完成后才执行的脚本。然后，文档状态将设置为“完成”，一个“加载”事件将随之触发。

您可以[在 HTML5 规范中查看标记化和树构建的完整算法](http://www.w3.org/TR/html5/syntax.html#html-parser)

#### 浏览器的容错机制

您在浏览 HTML 网页时从来不会看到“语法无效”的错误。这是因为浏览器会纠正任何无效内容，然后继续工作。

以下面的 HTML 代码为例：

```
<html>
  <mytag>
  </mytag>
  <div>
  <p>
  </div>
    Really lousy HTML
  </p>
</html>
```

在这里，我已经违反了很多语法规则（“mytag”不是标准的标记，“p”和“div”元素之间的嵌套有误等等），但是浏览器仍然会正确地显示这些内容，并且毫无怨言。因为有大量的解析器代码会纠正 HTML 网页作者的错误。

不同浏览器的错误处理机制相当一致，但令人称奇的是，这种机制并不是 HTML 当前规范的一部分。和书签管理以及前进/后退按钮一样，它也是浏览器在多年发展中的产物。很多网站都普遍存在着一些已知的无效 HTML 结构，每一种浏览器都会尝试通过和其他浏览器一样的方式来修复这些无效结构。

HTML5 规范定义了一部分这样的要求。WebKit 在 HTML 解析器类的开头注释中对此做了很好的概括。

> 解析器对标记化输入内容进行解析，以构建文档树。如果文档的格式正确，就直接进行解析。
>
> 遗憾的是，我们不得不处理很多格式错误的 HTML 文档，所以解析器必须具备一定的容错性。
>
> 我们至少要能够处理以下错误情况：
>
> 1. 明显不能在某些外部标记中添加的元素。在此情况下，我们应该关闭所有标记，直到出现禁止添加的元素，然后再加入该元素。
> 2. 我们不能直接添加的元素。这很可能是网页作者忘记添加了其中的一些标记（或者其中的标记是可选的）。这些标签可能包括：HTML HEAD BODY TBODY TR TD LI（还有遗漏的吗？）。
> 3. 向 inline 元素内添加 block 元素。关闭所有 inline 元素，直到出现下一个较高级的 block 元素。
> 4. 如果这样仍然无效，可关闭所有元素，直到可以添加元素为止，或者忽略该标记。

让我们看一些 WebKit 容错的示例：

##### 使用了 </br> 而不是 <br>

有些网站使用了 </br> 而不是 <br>。为了与 IE 和 Firefox 兼容，WebKit 将其与 <br> 做同样的处理。
代码如下：

```
if (t->isCloseTag(brTag) && m_document->inCompatMode()) {
     reportError(MalformedBRError);
     t->beginTag = true;
}
```

请注意，错误处理是在内部进行的，用户并不会看到这个过程。

##### 离散表格

离散表格是指位于其他表格内容中，但又不在任何一个单元格内的表格。
比如以下的示例：

```
<table>
    <table>
        <tr><td>inner table</td></tr>
    </table>
    <tr><td>outer table</td></tr>
</table>
```

WebKit 会将其层次结构更改为两个同级表格：

```
<table>
    <tr><td>outer table</td></tr>
</table>
<table>
    <tr><td>inner table</td></tr>
</table>
```

代码如下：

```
if (m_inStrayTableContent && localName == tableTag)
        popBlock(tableTag);
```

WebKit 使用一个堆栈来保存当前的元素内容，它会从外部表格的堆栈中弹出内部表格。现在，这两个表格就变成了同级关系。

##### 嵌套的表单元素

如果用户在一个表单元素中又放入了另一个表单，那么第二个表单将被忽略。
代码如下：

```
if (!m_currentFormElement) {
        m_currentFormElement = new HTMLFormElement(formTag,    m_document);
}
```

##### 过于复杂的标记层次结构

代码的注释已经说得很清楚了。

> 示例网站 www.liceo.edu.mx 嵌套了约 1500 个标记，全都来自一堆 <b> 标记。我们只允许最多 20 层同类型标记的嵌套，如果再嵌套更多，就会全部忽略。

```
bool HTMLParser::allowNestedRedundantTag(const AtomicString& tagName)
{

unsigned i = 0;
for (HTMLStackElem* curr = m_blockStack;
         i < cMaxRedundantTagDepth && curr && curr->tagName == tagName;
     curr = curr->next, i++) { }
return i != cMaxRedundantTagDepth;
}
```

##### 放错位置的 html 或者 body 结束标记

同样，代码的注释已经说得很清楚了。

> 支持格式非常糟糕的 HTML 代码。我们从不关闭 body 标记，因为一些愚蠢的网页会在实际文档结束之前就关闭。我们通过调用 end() 来执行关闭操作。

```
if (t->tagName == htmlTag || t->tagName == bodyTag )
        return;
```

所以网页作者需要注意，除非您想作为反面教材出现在 WebKit 容错代码段的示例中，否则还请编写格式正确的 HTML 代码。

### CSS 解析

还记得简介中解析的概念吗？和 HTML 不同，CSS 是上下文无关的语法，可以使用简介中描述的各种解析器进行解析。事实上，[CSS 规范定义了 CSS 的词法和语法](http://www.w3.org/TR/CSS2/grammar.html)。

让我们来看一些示例：
词法语法（词汇）是针对各个标记用正则表达式定义的：

```
comment   \/\*[^*]*\*+([^/*][^*]*\*+)*\/
num   [0-9]+|[0-9]*"."[0-9]+
nonascii  [\200-\377]
nmstart   [_a-z]|{nonascii}|{escape}
nmchar    [_a-z0-9-]|{nonascii}|{escape}
name    {nmchar}+
ident   {nmstart}{nmchar}*
```

“ident”是标识符 (identifier) 的缩写，比如类名。“name”是元素的 ID（通过“#”来引用）。

语法是采用 BNF 格式描述的。

```css
ruleset
  : selector [ ',' S* selector ]*
    '{' S* declaration [ ';' S* declaration ]* '}' S*
  ;
selector
  : simple_selector [ combinator selector | S+ [ combinator? selector ]? ]?
  ;
simple_selector
  : element_name [ HASH | class | attrib | pseudo ]*
  | [ HASH | class | attrib | pseudo ]+
  ;
class
  : '.' IDENT
  ;
element_name
  : IDENT | '*'
  ;
attrib
  : '[' S* IDENT S* [ [ '=' | INCLUDES | DASHMATCH ] S*
    [ IDENT | STRING ] S* ] ']'
  ;
pseudo
  : ':' [ IDENT | FUNCTION S* [IDENT S*] ')' ]
  ;
```

解释：这是一个规则集的结构：

```css
div.error , a.error {
    color:red;
    font-weight:bold;
}
```

div.error 和 a.error 是选择器。大括号内的部分包含了由此规则集应用的规则。此结构的正式定义是这样的：

```css
ruleset
    : selector [ ',' S* selector ]*
    	'{' S* declaration [ ';' S* declaration ]* '}' S*
    ;
```

这表示一个规则集就是一个选择器，或者由逗号和空格（S 表示空格）分隔的多个（数量可选）选择器。规则集包含了大括号，以及其中的一个或多个（数量可选）由分号分隔的声明。“声明”和“选择器”将由下面的 BNF 格式定义。

#### WebKit CSS 解析器

WebKit 使用 [Flex 和 Bison](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#parser_generators) 解析器生成器，通过 CSS 语法文件自动创建解析器。正如我们之前在解析器简介中所说，Bison 会创建自下而上的移位归约解析器。Firefox 使用的是人工编写的自上而下的解析器。这两种解析器都会将 CSS 文件解析成 StyleSheet 对象，且每个对象都包含 CSS 规则。CSS 规则对象则包含选择器和声明对象，以及其他与 CSS 语法对应的对象。

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image023.png)图：解析 CSS

### 处理脚本和样式表的顺序

#### 脚本

网络的模型是同步的。网页作者希望解析器遇到 <script> 标记时立即解析并执行脚本。文档的解析将停止，直到脚本执行完毕。如果脚本是外部的，那么解析过程会停止，直到从网络同步抓取资源完成后再继续。此模型已经使用了多年，也在 HTML4 和 HTML5 规范中进行了指定。作者也可以将脚本标注为“defer”，这样它就不会停止文档解析，而是等到解析结束才执行。HTML5 增加了一个选项，可将脚本标记为异步，以便由其他线程解析和执行。

#### 预解析

WebKit 和 Firefox 都进行了这项优化。在执行脚本时，其他线程会解析文档的其余部分，找出并加载需要通过网络加载的其他资源。通过这种方式，资源可以在并行连接上加载，从而提高总体速度。请注意，预解析器不会修改 DOM 树，而是将这项工作交由主解析器处理；预解析器只会解析外部资源（例如外部脚本、样式表和图片）的引用。

#### 样式表

另一方面，样式表有着不同的模型。理论上来说，应用样式表不会更改 DOM 树，因此似乎没有必要等待样式表并停止文档解析。但这涉及到一个问题，就是脚本在文档解析阶段会请求样式信息。如果当时还没有加载和解析样式，脚本就会获得错误的回复，这样显然会产生很多问题。这看上去是一个非典型案例，但事实上非常普遍。Firefox 在样式表加载和解析的过程中，会禁止所有脚本。而对于 WebKit 而言，仅当脚本尝试访问的样式属性可能受尚未加载的样式表影响时，它才会禁止该脚本。

### 呈现树构建

在 DOM 树构建的同时，浏览器还会构建另一个树结构：呈现树。这是由可视化元素按照其显示顺序而组成的树，也是文档的可视化表示。它的作用是让您按照正确的顺序绘制内容。

Firefox 将呈现树中的元素称为“框架”。WebKit 使用的术语是呈现器或呈现对象。
呈现器知道如何布局并将自身及其子元素绘制出来。
WebKits RenderObject 类是所有呈现器的基类，其定义如下：

```
class RenderObject{
  virtual void layout();
  virtual void paint(PaintInfo);
  virtual void rect repaintRect();
  Node* node;  //the DOM node
  RenderStyle* style;  // the computed style
  RenderLayer* containgLayer; //the containing z-index layer
}
```

每一个呈现器都代表了一个矩形的区域，通常对应于相关节点的 CSS 框，这一点在 CSS2 规范中有所描述。它包含诸如宽度、高度和位置等几何信息。
框的类型会受到与节点相关的“display”样式属性的影响（请参阅[样式计算](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#style_computation)章节）。下面这段 WebKit 代码描述了根据 display 属性的不同，针对同一个 DOM 节点应创建什么类型的呈现器。

```
RenderObject* RenderObject::createObject(Node* node, RenderStyle* style)
{
    Document* doc = node->document();
    RenderArena* arena = doc->renderArena();
    ...
    RenderObject* o = 0;

    switch (style->display()) {
        case NONE:
            break;
        case INLINE:
            o = new (arena) RenderInline(node);
            break;
        case BLOCK:
            o = new (arena) RenderBlock(node);
            break;
        case INLINE_BLOCK:
            o = new (arena) RenderBlock(node);
            break;
        case LIST_ITEM:
            o = new (arena) RenderListItem(node);
            break;
       ...
    }

    return o;
}
```

元素类型也是考虑因素之一，例如表单控件和表格都对应特殊的框架。
在 WebKit 中，如果一个元素需要创建特殊的呈现器，就会替换 `createRenderer` 方法。呈现器所指向的样式对象中包含了一些和几何无关的信息。

##### 呈现树和 DOM 树的关系

呈现器是和 DOM 元素相对应的，但并非一一对应。非可视化的 DOM 元素不会插入呈现树中，例如“head”元素。如果元素的 display 属性值为“none”，那么也不会显示在呈现树中（但是 visibility 属性值为“hidden”的元素仍会显示）。

有一些 DOM 元素对应多个可视化对象。它们往往是具有复杂结构的元素，无法用单一的矩形来描述。例如，“select”元素有 3 个呈现器：一个用于显示区域，一个用于下拉列表框，还有一个用于按钮。如果由于宽度不够，文本无法在一行中显示而分为多行，那么新的行也会作为新的呈现器而添加。
另一个关于多呈现器的例子是格式无效的 HTML。根据 CSS 规范，inline 元素只能包含 block 元素或 inline 元素中的一种。如果出现了混合内容，则应创建匿名的 block 呈现器，以包裹 inline 元素。

有一些呈现对象对应于 DOM 节点，但在树中所在的位置与 DOM 节点不同。浮动定位和绝对定位的元素就是这样，它们处于正常的流程之外，放置在树中的其他地方，并映射到真正的框架，而放在原位的是占位框架。



![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image025.png)图：呈现树及其对应的 DOM 树 ([3.1](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#3_1))。初始容器 block 为“viewport”，而在 WebKit 中则为“RenderView”对象。

##### 构建呈现树的流程

在 Firefox 中，系统会针对 DOM 更新注册展示层，作为侦听器。展示层将框架创建工作委托给 `FrameConstructor`，由该构造器解析样式（请参阅[样式计算](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#style)）并创建框架。

在 WebKit 中，解析样式和创建呈现器的过程称为“附加”。每个 DOM 节点都有一个“attach”方法。附加是同步进行的，将节点插入 DOM 树需要调用新的节点“attach”方法。

处理 html 和 body 标记就会构建呈现树根节点。这个根节点呈现对象对应于 CSS 规范中所说的容器 block，这是最上层的 block，包含了其他所有 block。它的尺寸就是视口，即浏览器窗口显示区域的尺寸。Firefox 称之为 `ViewPortFrame`，而 WebKit 称之为 `RenderView`。这就是文档所指向的呈现对象。呈现树的其余部分以 DOM 树节点插入的形式来构建。

请参阅[关于处理模型的 CSS2 规范](http://www.w3.org/TR/CSS21/intro.html#processing-model)。

#### 样式计算

构建呈现树时，需要计算每一个呈现对象的可视化属性。这是通过计算每个元素的样式属性来完成的。

样式包括来自各种来源的样式表、inline 样式元素和 HTML 中的可视化属性（例如“bgcolor”属性）。其中后者将经过转化以匹配 CSS 样式属性。

样式表的来源包括浏览器的默认样式表、由网页作者提供的样式表以及由浏览器用户提供的用户样式表（浏览器允许您定义自己喜欢的样式。以 Firefox 为例，用户可以将自己喜欢的样式表放在“Firefox Profile”文件夹下）。

样式计算存在以下难点：

1. 样式数据是一个超大的结构，存储了无数的样式属性，这可能造成内存问题。

2. 如果不进行优化，为每一个元素查找匹配的规则会造成性能问题。要为每一个元素遍历整个规则列表来寻找匹配规则，这是一项浩大的工程。选择器会具有很复杂的结构，这就会导致某个匹配过程一开始看起来很可能是正确的，但最终发现其实是徒劳的，必须尝试其他匹配路径。

   例如下面这个组合选择器：

   ```
   div div div div{
     ...
   }
   ```

   这意味着规则适用于作为 3 个 div 元素的子代的

    

   ```
   <div>
   ```

   。如果您要检查规则是否适用于某个指定的

    

   ```
   <div>
   ```

    

   元素，应选择树上的一条向上路径进行检查。您可能需要向上遍历节点树，结果发现只有两个 div，而且规则并不适用。然后，您必须尝试树中的其他路径。

3. 应用规则涉及到相当复杂的层叠规则（用于定义这些规则的层次）。

让我们来看看浏览器是如何处理这些问题的：

##### 共享样式数据

WebKit 节点会引用样式对象 (RenderStyle)。这些对象在某些情况下可以由不同节点共享。这些节点是同级关系，并且：

1. 这些元素必须处于相同的鼠标状态（例如，不允许其中一个是“:hover”状态，而另一个不是）
2. 任何元素都没有 ID
3. 标记名称应匹配
4. 类属性应匹配
5. 映射属性的集合必须是完全相同的
6. 链接状态必须匹配
7. 焦点状态必须匹配
8. 任何元素都不应受属性选择器的影响，这里所说的“影响”是指在选择器中的任何位置有任何使用了属性选择器的选择器匹配
9. 元素中不能有任何 inline 样式属性
10. 不能使用任何同级选择器。WebCore 在遇到任何同级选择器时，只会引发一个全局开关，并停用整个文档的样式共享（如果存在）。这包括 + 选择器以及 :first-child 和 :last-child 等选择器。

##### Firefox 规则树

为了简化样式计算，Firefox 还采用了另外两种树：规则树和样式上下文树。WebKit 也有样式对象，但它们不是保存在类似样式上下文树这样的树结构中，只是由 DOM 节点指向此类对象的相关样式。

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image035.png)图：Firefox 样式上下文树 ([2.2](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#2_2))

样式上下文包含端值。要计算出这些值，应按照正确顺序应用所有的匹配规则，并将其从逻辑值转化为具体的值。例如，如果逻辑值是屏幕大小的百分比，则需要换算成绝对的单位。规则树的点子真的很巧妙，它使得节点之间可以共享这些值，以避免重复计算，还可以节约空间。

所有匹配的规则都存储在树中。路径中的底层节点拥有较高的优先级。规则树包含了所有已知规则匹配的路径。规则的存储是延迟进行的。规则树不会在开始的时候就为所有的节点进行计算，而是只有当某个节点样式需要进行计算时，才会向规则树添加计算的路径。

这个想法相当于将规则树路径视为词典中的单词。如果我们已经计算出如下的规则树：

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/tree.png)

假设我们需要为内容树中的另一个元素匹配规则，并且找到匹配路径是 B - E - I（按照此顺序）。由于我们在树中已经计算出了路径 A - B - E - I - L，因此就已经有了此路径，这就减少了现在所需的工作量。



让我们看看规则树如何帮助我们减少工作。

##### 结构划分

样式上下文可分割成多个结构。这些结构体包含了特定类别（如 border 或 color）的样式信息。结构中的属性都是继承的或非继承的。继承属性如果未由元素定义，则继承自其父代。非继承属性（也称为“重置”属性）如果未进行定义，则使用默认值。

规则树通过缓存整个结构（包含计算出的端值）为我们提供帮助。这一想法假定底层节点没有提供结构的定义，则可使用上层节点中的缓存结构。

##### 使用规则树计算样式上下文

在计算某个特定元素的样式上下文时，我们首先计算规则树中的对应路径，或者使用现有的路径。然后我们沿此路径应用规则，在新的样式上下文中填充结构。我们从路径中拥有最高优先级的底层节点（通常也是最特殊的选择器）开始，并向上遍历规则树，直到结构填充完毕。如果该规则节点对于此结构没有任何规范，那么我们可以实现更好的优化：寻找路径更上层的节点，找到后指定完整的规范并指向相关节点即可。这是最好的优化方法，因为整个结构都能共享。这可以减少端值的计算量并节约内存。
如果我们找到了部分定义，就会向上遍历规则树，直到结构填充完毕。

如果我们找不到结构的任何定义，那么假如该结构是“继承”类型，我们会在**上下文树**中指向父代的结构，这样也可以共享结构。如果是 reset 类型的结构，则会使用默认值。

如果最特殊的节点确实添加了值，那么我们需要另外进行一些计算，以便将这些值转化成实际值。然后我们将结果缓存在树节点中，供子代使用。

如果某个元素与其同级元素都指向同一个树节点，那么它们就可以共享**整个样式上下文**。

让我们来看一个例子，假设我们有如下 HTML 代码：

```
<html>
  <body>
    <div class="err" id="div1">
      <p>
        this is a <span class="big"> big error </span>
        this is also a
        <span class="big"> very  big  error</span> error
      </p>
    </div>
    <div class="err" id="div2">another error</div>
  </body>
</html>
```

还有如下规则：

```css
div {margin:5px;color:black}.err {color:red}.big {margin-top:3px}div span {margin-bottom:4px}#div1 {color:blue}#div2 {color:green}
```

为了简便起见，我们只需要填充两个结构：color 结构和 margin 结构。color 结构只包含一个成员（即“color”），而 margin 结构包含四条边。
形成的规则树如下图所示（节点的标记方式为“节点名 : 指向的规则序号”）：

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image027.png)图：规则树


上下文树如下图所示（节点名 : 指向的规则节点）：

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image029.png)图：上下文树

假设我们解析 HTML 时遇到了第二个 <div> 标记，我们需要为此节点创建样式上下文，并填充其样式结构。
经过规则匹配，我们发现该 <div> 的匹配规则是第 1、2 和 6 条。这意味着规则树中已有一条路径可供我们的元素使用，我们只需要再为其添加一个节点以匹配第 6 条规则（规则树中的 F 节点）。
我们将创建样式上下文并将其放入上下文树中。新的样式上下文将指向规则树中的 F 节点。

现在我们需要填充样式结构。首先要填充的是 margin 结构。由于最后的规则节点 (F) 并没有添加到 margin 结构，我们需要上溯规则树，直至找到在先前节点插入中计算过的缓存结构，然后使用该结构。我们会在指定 margin 规则的最上层节点（即 B 节点）上找到该结构。

我们已经有了 color 结构的定义，因此不能使用缓存的结构。由于 color 有一个属性，我们无需上溯规则树以填充其他属性。我们将计算端值（将字符串转化为 RGB 等）并在此节点上缓存经过计算的结构。

第二个 <span> 元素处理起来更加简单。我们将匹配规则，最终发现它和之前的 span 一样指向规则 G。由于我们找到了指向同一节点的同级，就可以共享整个样式上下文了，只需指向之前 span 的上下文即可。

对于包含了继承自父代的规则的结构，缓存是在上下文树中进行的（事实上 color 属性是继承的，但是 Firefox 将其视为 reset 属性，并缓存到规则树上）。
例如，如果我们在某个段落中添加 font 规则：

```
p {font-family:Verdana;font size:10px;font-weight:bold}
```

那么，该段落元素作为上下文树中的 div 的子代，就会共享与其父代相同的 font 结构（前提是该段落没有指定 font 规则）。



在 WebKit 中没有规则树，因此会对匹配的声明遍历 4 次。首先应用非重要高优先级的属性（由于作为其他属性的依据而应首先应用的属性，例如 display），接着是高优先级重要规则，然后是普通优先级非重要规则，最后是普通优先级重要规则。这意味着多次出现的属性会根据正确的层叠顺序进行解析。最后出现的最终生效。

因此概括来说，共享样式对象（整个对象或者对象中的部分结构）可以解决问题 [1](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#issue1) 和问题 [3](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#issue3)。Firefox 规则树还有助于按照正确的顺序应用属性。

##### 对规则进行处理以简化匹配

样式规则有一些来源：

- 外部样式表或样式元素中的 CSS 规则

  ```
  p {color:blue}
  ```

- inline 样式属性及类似内容

  ```
  <p style="color:blue" />
  ```

- HTML 可视化属性（映射到相关的样式规则）

  ```
  <p bgcolor="blue" />
  ```

后两种很容易和元素进行匹配，因为元素拥有样式属性，而且 HTML 属性可以使用元素作为键值进行映射。

我们之前在[第 2 个问题](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#issue2)中提到过，CSS 规则匹配可能比较棘手。为了解决这一难题，可以对 CSS 规则进行一些处理，以便访问。

样式表解析完毕后，系统会根据选择器将 CSS 规则添加到某个哈希表中。这些哈希表的选择器各不相同，包括 ID、类名称、标记名称等，还有一种通用哈希表，适合不属于上述类别的规则。如果选择器是 ID，规则就会添加到 ID 表中；如果选择器是类，规则就会添加到类表中，依此类推。
这种处理可以大大简化规则匹配。我们无需查看每一条声明，只要从哈希表中提取元素的相关规则即可。这种优化方法可排除掉 95% 以上规则，因此在匹配过程中根本就不用考虑这些规则了 ([4.1](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#4_1))。

我们以如下的样式规则为例：

```
p.error {color:red}
#messageDiv {height:50px}
div {margin:5px}
```

第一条规则将插入类表，第二条将插入 ID 表，而第三条将插入标记表。
对于下面的 HTML 代码段：

```
<p class="error">an error occurred </p>
<div id="messageDiv">this is a message</div>
```



我们首先会为 p 元素寻找匹配的规则。类表中有一个“error”键，在下面可以找到“p.error”的规则。div 元素在 ID 表（键为 ID）和标记表中有相关的规则。剩下的工作就是找出哪些根据键提取的规则是真正匹配的了。
例如，如果 div 的对应规则如下：

```
table div {margin:5px}
```

这条规则仍然会从标记表中提取出来，因为键是最右边的选择器，但这条规则并不匹配我们的 div 元素，因为 div 没有 table 祖先。



WebKit 和 Firefox 都进行了这一处理。

##### 以正确的层叠顺序应用规则

样式对象具有与每个可视化属性一一对应的属性（均为 CSS 属性但更为通用）。如果某个属性未由任何匹配规则所定义，那么部分属性就可由父代元素样式对象继承。其他属性具有默认值。

如果定义不止一个，就会出现问题，需要通过层叠顺序来解决。

##### 样式表层叠顺序

某个样式属性的声明可能会出现在多个样式表中，也可能在同一个样式表中出现多次。这意味着应用规则的顺序极为重要。这称为“层叠”顺序。根据 CSS2 规范，层叠的顺序为（优先级从低到高）：

1. 浏览器声明
2. 用户普通声明
3. 作者普通声明
4. 作者重要声明
5. 用户重要声明



浏览器声明是重要程度最低的，而用户只有将该声明标记为“重要”才可以替换网页作者的声明。同样顺序的声明会根据[特异性](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#Specificity)进行排序，然后再是其指定顺序。HTML 可视化属性会转换成匹配的 CSS 声明。它们被视为低优先级的网页作者规则。

##### 特异性

选择器的特异性由 [CSS2 规范](http://www.w3.org/TR/CSS2/cascade.html#specificity)定义如下：

- 如果声明来自于“style”属性，而不是带有选择器的规则，则记为 1，否则记为 0 (= a)
- 记为选择器中 ID 属性的个数 (= b)
- 记为选择器中其他属性和伪类的个数 (= c)
- 记为选择器中元素名称和伪元素的个数 (= d)

将四个数字按 a-b-c-d 这样连接起来（位于大数进制的数字系统中），构成特异性。



您使用的进制取决于上述类别中的最高计数。
例如，如果 a=14，您可以使用十六进制。如果 a=17，那么您需要使用十七进制；当然不太可能出现这种情况，除非是存在如下的选择器：html body div div p ...（在选择器中出现了 17 个标记，这样的可能性极低）。

一些示例：

```
 *             {}  /* a=0 b=0 c=0 d=0 -> specificity = 0,0,0,0 */
 li            {}  /* a=0 b=0 c=0 d=1 -> specificity = 0,0,0,1 */
 li:first-line {}  /* a=0 b=0 c=0 d=2 -> specificity = 0,0,0,2 */
 ul li         {}  /* a=0 b=0 c=0 d=2 -> specificity = 0,0,0,2 */
 ul ol+li      {}  /* a=0 b=0 c=0 d=3 -> specificity = 0,0,0,3 */
 h1 + *[rel=up]{}  /* a=0 b=0 c=1 d=1 -> specificity = 0,0,1,1 */
 ul ol li.red  {}  /* a=0 b=0 c=1 d=3 -> specificity = 0,0,1,3 */
 li.red.level  {}  /* a=0 b=0 c=2 d=1 -> specificity = 0,0,2,1 */
 #x34y         {}  /* a=0 b=1 c=0 d=0 -> specificity = 0,1,0,0 */
 style=""          /* a=1 b=0 c=0 d=0 -> specificity = 1,0,0,0 */
```



##### 规则排序

找到匹配的规则之后，应根据级联顺序将其排序。WebKit 对于较小的列表会使用冒泡排序，而对较大的列表则使用归并排序。对于以下规则，WebKit 通过替换“>”运算符来实现排序：

```
static bool operator >(CSSRuleData& r1, CSSRuleData& r2)
{
    int spec1 = r1.selector()->specificity();
    int spec2 = r2.selector()->specificity();
    return (spec1 == spec2) : r1.position() > r2.position() : spec1 > spec2;
}
```



#### 渐进式处理

WebKit 使用一个标记来表示是否所有的顶级样式表（包括 @imports）均已加载完毕。如果在附加过程中尚未完全加载样式，则使用占位符，并在文档中进行标注，等样式表加载完毕后再重新计算。

### 布局

呈现器在创建完成并添加到呈现树时，并不包含位置和大小信息。计算这些值的过程称为布局或重排。

HTML 采用基于流的布局模型，这意味着大多数情况下只要一次遍历就能计算出几何信息。处于流中靠后位置元素通常不会影响靠前位置元素的几何特征，因此布局可以按从左至右、从上至下的顺序遍历文档。但是也有例外情况，比如 HTML 表格的计算就需要不止一次的遍历 ([3.5](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#3_5))。

坐标系是相对于根框架而建立的，使用的是上坐标和左坐标。

布局是一个递归的过程。它从根呈现器（对应于 HTML 文档的 `<html>` 元素）开始，然后递归遍历部分或所有的框架层次结构，为每一个需要计算的呈现器计算几何信息。

根呈现器的位置左边是 0,0，其尺寸为视口（也就是浏览器窗口的可见区域）。

所有的呈现器都有一个“layout”或者“reflow”方法，每一个呈现器都会调用其需要进行布局的子代的 layout 方法。

#### Dirty 位系统

为避免对所有细小更改都进行整体布局，浏览器采用了一种“dirty 位”系统。如果某个呈现器发生了更改，或者将自身及其子代标注为“dirty”，则需要进行布局。

有两种标记：“dirty”和“children are dirty”。“children are dirty”表示尽管呈现器自身没有变化，但它至少有一个子代需要布局。

#### 全局布局和增量布局

全局布局是指触发了整个呈现树范围的布局，触发原因可能包括：

1. 影响所有呈现器的全局样式更改，例如字体大小更改。
2. 屏幕大小调整。



布局可以采用增量方式，也就是只对 dirty 呈现器进行布局（这样可能存在需要进行额外布局的弊端）。
当呈现器为 dirty 时，会异步触发增量布局。例如，当来自网络的额外内容添加到 DOM 树之后，新的呈现器附加到了呈现树中。

![img](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/reflow.png)图：增量布局 - 只有 dirty 呈现器及其子代进行布局 ([3.6](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#3_6))。

#### 异步布局和同步布局

增量布局是异步执行的。Firefox 将增量布局的“reflow 命令”加入队列，而调度程序会触发这些命令的批量执行。WebKit 也有用于执行增量布局的计时器：对呈现树进行遍历，并对 dirty 呈现器进行布局。
请求样式信息（例如“offsetHeight”）的脚本可同步触发增量布局。
全局布局往往是同步触发的。
有时，当初始布局完成之后，如果一些属性（如滚动位置）发生变化，布局就会作为回调而触发。

#### 优化

如果布局是由“大小调整”或呈现器的位置（而非大小）改变而触发的，那么可以从缓存中获取呈现器的大小，而无需重新计算。
在某些情况下，只有一个子树进行了修改，因此无需从根节点开始布局。这适用于在本地进行更改而不影响周围元素的情况，例如在文本字段中插入文本（否则每次键盘输入都将触发从根节点开始的布局）。



#### 布局处理

布局通常具有以下模式：

1. 父呈现器确定自己的宽度。
2. 父呈现器依次处理子呈现器，并且：
   1. 放置子呈现器（设置 x,y 坐标）。
   2. 如果有必要，调用子呈现器的布局（如果子呈现器是 dirty 的，或者这是全局布局，或出于其他某些原因），这会计算子呈现器的高度。
3. 父呈现器根据子呈现器的累加高度以及边距和补白的高度来设置自身高度，此值也可供父呈现器的父呈现器使用。
4. 将其 dirty 位设置为 false。



Firefox 使用“state”对象 (nsHTMLReflowState) 作为布局的参数（称为“reflow”），这其中包括了父呈现器的宽度。
Firefox 布局的输出为“metrics”对象 (nsHTMLReflowMetrics)，其包含计算得出的呈现器高度。

#### 宽度计算

呈现器宽度是根据容器块的宽度、呈现器样式中的“width”属性以及边距和边框计算得出的。
例如以下 div 的宽度：

```
<div style="width:30%"/>
```

将由 WebKit 计算如下（BenderBox 类，calcWidth 方法）：

- 容器的宽度取容器的 availableWidth 和 0 中的较大值。availableWidth 在本例中相当于 contentWidth，计算公式如下：

  ```
  clientWidth() - paddingLeft() - paddingRight()
  ```

  clientWidth 和 clientHeight 表示一个对象的内部（除去边框和滚动条）。

- 元素的宽度是“width”样式属性。它会根据容器宽度的百分比计算得出一个绝对值。

- 然后加上水平方向的边框和补白。

现在计算得出的是“preferred width”。然后需要计算最小宽度和最大宽度。
如果首选宽度大于最大宽度，那么应使用最大宽度。如果首选宽度小于最小宽度（最小的不可破开单位），那么应使用最小宽度。



这些值会缓存起来，以用于需要布局而宽度不变的情况。



#### 换行

如果呈现器在布局过程中需要换行，会立即停止布局，并告知其父代需要换行。父代会创建额外的呈现器，并对其调用布局。

### 绘制

在绘制阶段，系统会遍历呈现树，并调用呈现器的“paint”方法，将呈现器的内容显示在屏幕上。绘制工作是使用用户界面基础组件完成的。

#### 全局绘制和增量绘制

和布局一样，绘制也分为全局（绘制整个呈现树）和增量两种。在增量绘制中，部分呈现器发生了更改，但是不会影响整个树。更改后的呈现器将其在屏幕上对应的矩形区域设为无效，这导致 OS 将其视为一块“dirty 区域”，并生成“paint”事件。OS 会很巧妙地将多个区域合并成一个。在 Chrome 浏览器中，情况要更复杂一些，因为 Chrome 浏览器的呈现器不在主进程上。Chrome 浏览器会在某种程度上模拟 OS 的行为。展示层会侦听这些事件，并将消息委托给呈现根节点。然后遍历呈现树，直到找到相关的呈现器，该呈现器会重新绘制自己（通常也包括其子代）。

#### 绘制顺序

[CSS2 规范定义了绘制流程的顺序](http://www.w3.org/TR/CSS21/zindex.html)。绘制的顺序其实就是元素进入[堆栈样式上下文](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#stackingcontext)的顺序。这些堆栈会从后往前绘制，因此这样的顺序会影响绘制。块呈现器的堆栈顺序如下：

1. 背景颜色
2. 背景图片
3. 边框
4. 子代
5. 轮廓

#### Firefox 显示列表

Firefox 遍历整个呈现树，为绘制的矩形建立一个显示列表。列表中按照正确的绘制顺序（先是呈现器的背景，然后是边框等等）包含了与矩形相关的呈现器。这样等到重新绘制的时候，只需遍历一次呈现树，而不用多次遍历（绘制所有背景，然后绘制所有图片，再绘制所有边框等等）。

Firefox 对此过程进行了优化，也就是不添加隐藏的元素，例如被不透明元素完全遮挡住的元素。

#### WebKit 矩形存储

在重新绘制之前，WebKit 会将原来的矩形另存为一张位图，然后只绘制新旧矩形之间的差异部分。

### 动态变化

在发生变化时，浏览器会尽可能做出最小的响应。因此，元素的颜色改变后，只会对该元素进行重绘。元素的位置改变后，只会对该元素及其子元素（可能还有同级元素）进行布局和重绘。添加 DOM 节点后，会对该节点进行布局和重绘。一些重大变化（例如增大“html”元素的字体）会导致缓存无效，使得整个呈现树都会进行重新布局和绘制。

### 呈现引擎的线程

呈现引擎采用了单线程。几乎所有操作（除了网络操作）都是在单线程中进行的。在 Firefox 和 Safari 中，该线程就是浏览器的主线程。而在 Chrome 浏览器中，该线程是标签进程的主线程。
网络操作可由多个并行线程执行。并行连接数是有限的（通常为 2 至 6 个，以 Firefox 3 为例是 6 个）。

#### 事件循环

浏览器的主线程是事件循环。它是一个无限循环，永远处于接受处理状态，并等待事件（如布局和绘制事件）发生，并进行处理。这是 Firefox 中关于主事件循环的代码：

```
while (!mExiting)
    NS_ProcessNextEvent(thread);
```

# 9.[内存泄露](https://segmentfault.com/a/1190000020231307)

用户一般不会在一个 Web 页面停留比较久，即使有一点内存泄漏，重载页面内存也会跟着释放。而且浏览器也有自动回收内存的机制，所以我们前端其实并没有像 C、C++ 这类语言一样，特别关注内存泄漏的问题。

但是如果我们对内存泄漏没有什么概念，有时候还是有可能因为内存泄漏，导致页面卡顿。了解内存泄漏，如何避免内存泄漏，也是我们提升前端技能的必经之路。

## 什么是内存？

> 在硬件级别上，计算机内存由大量触发器组成。每个触发器包含几个晶体管，能够存储一个位。单个触发器可以通过唯一标识符寻址，因此我们可以读取和覆盖它们。因此，从概念上讲，我们可以把我们的整个计算机内存看作是一个巨大的位数组，我们可以读和写。

这么底层的概念，了解下就好，绝大多数数情况下，JavaScript 语言作为你们高级语言，无需我们使用二进制进直接进行读和写。

## 内存生命周期

内存也是有**生命周期**的，不管什么程序语言，一般可以按顺序分为三个周期：

- 分配期

  分配所需要的内存

- 使用期

  使用分配到的内存（读、写）

- 释放期

  不需要时将其释放和归还

内存分配 -> 内存使用 -> 内存释放。

## 什么是内存泄漏？

> 在[计算机科学](https://zh.wikipedia.org/wiki/计算机科学)中，**内存泄漏**指由于疏忽或错误造成程序未能释放已经不再使用的[内存](https://zh.wikipedia.org/wiki/内存)。内存泄漏并非指内存在物理上的消失，而是应用程序分配某段内存后，由于设计错误，导致在释放该段内存之前就失去了对该段内存的控制，从而造成了内存的浪费。

如果内存不需要时，没有经过生命周期的**释放期**，那么就存在**内存泄漏**。

内存泄漏简单理解：无用的内存还在占用，得不到释放和归还。比较严重时，无用的内存会持续递增，从而导致整个系统卡顿，甚至崩溃。

## JavaScript 内存管理机制

> 像 C 语言这样的底层语言一般都有底层的内存管理接口，比如 `malloc()`和`free()`。相反，JavaScript是在创建变量（对象，字符串等）时自动进行了分配内存，并且在不使用它们时“自动”释放。 释放的过程称为垃圾回收。这个“自动”是混乱的根源，并让JavaScript（和其他高级语言）开发者错误的感觉他们可以不关心内存管理。

JavaScript 内存管理机制和内存的**生命周期**是一一对应的。首先需要**分配内存**，然后**使用内存**，最后**释放内存**。

其中 JavaScript 语言**不需要程序员手动**分配内存，绝大部分情况下也不需要手动释放内存，对 JavaScript 程序员来说通常就是使用内存（即使用变量、函数、对象等）。

### 内存分配

JavaScript 定义变量就会自动分配内存的。**我们只需了解 JavaScript 的内存是自动分配的就足够了**。

看下内存自动分配的例子：

```
// 给数值变量分配内存
let number = 123; 
// 给字符串分配内存
const string = "xianshannan"; 
// 给对象及其包含的值分配内存
const object = {
  a: 1,
  b: null
}; 
// 给数组及其包含的值分配内存（就像对象一样）
const array = [1, null, "abra"]; 
// 给函数（可调用的对象）分配内存
function func(a){
  return a;
} 
```

### 内存使用

> 使用值的过程实际上是对分配内存进行**读取与写入**的操作。读取与写入可能是写入一个变量或者一个对象的属性值，甚至传递函数的参数。

根据上面的内存自动分配例子，我们继续内存使用的例子：

```
// 写入内存
number = 234;
// 读取 number 和 func 的内存，写入 func 参数内存
func(number);
```

### 内存回收

前端界一般称**垃圾内存回收**为 `GC`（Garbage Collection，即垃圾回收）。

**内存泄漏一般都是发生在这一步，JavaScript 的内存回收机制虽然能回收绝大部分的垃圾内存，但是还是存在回收不了的情况，如果存在这些情况，需要我们手动清理内存。**

以前一些老版本的浏览器的 JavaScript 回收机制没那么完善，经常出现一些 bug 的内存泄漏，不过现在的浏览器基本都没这些问题了，已过时的知识这里就不做深究了。

这里了解下现在的 JavaScript 的垃圾内存的两种回收方式，熟悉下这两种算法可以帮助我们理解一些内存泄漏的场景。

#### 引用计数垃圾收集

> 这是最初级的垃圾收集算法。此算法把“对象是否不再需要”简化定义为“对象有没有其他对象引用到它”。如果没有引用指向该对象（零引用），对象将被垃圾回收机制回收。

看下下面的例子，“这个对象”的内存被回收了吗？

```
// “这个对象”分配给 a 变量
var a = {
  a: 1,
  b: 2,
}
// b 引用“这个对象”
var b = a; 
// 现在，“这个对象”的原始引用 a 被 b 替换了
a = 1;
```

当前执行环境中，“这个对象”内存还没有被回收的，需要手动释放“这个对象”的内存（当然是还没离开执行环境的情况下），例如：

```
b = null;
// 或者 b = 1，反正替换“这个对象”就行了
```

这样引用的"这个对象"的内存就被回收了。

ES6 把**引用**有区分为**强引用**和**弱引用**，这个目前只有在 Set 和 Map 中才有。

**强引用**才会有**引用计数**叠加，只有引用计数为 0 的对象的内存才会被回收，所以一般需要手动回收内存（手动回收的前提在于**标记清除法**还没执行，还处于当前执行环境）。

而**弱引用**没有触发**引用计数**叠加，只要引用计数为 0，弱引用就会自动消失，无需手动回收内存。

#### 标记清除法

> 当变量进入执行环境时标记为“进入环境”，当变量离开执行环境时则标记为“离开环境”，被标记为“进入环境”的变量是不能被回收的，因为它们正在被使用，而标记为“离开环境”的变量则可以被回收

环境可以理解为我们的作用域，但是全局作用域的变量只会在页面关闭才会销毁。

```
// 假设这里是全局变量
// b 被标记进入环境
var b = 2;
function test() {
  var a = 1;
  // 函数执行时，a 被标记进入环境
  return a + b;
}
// 函数执行结束，a 被标记离开环境，被回收
// 但是 b 就没有被标记离开环境
test();
```

## JavaScript 内存泄漏的一些场景

JavaScript 的内存回收机制虽然能回收绝大部分的垃圾内存，但是还是存在回收不了的情况。程序员要让浏览器内存泄漏，浏览器也是管不了的。

**下面有些例子是在执行环境中，没离开当前执行环境，还没触发标记清除法。所以你需要读懂上面 JavaScript 的内存回收机制，才能更好理解下面的场景。**

### 意外的全局变量

```
// 在全局作用域下定义
function count(number) {
  // basicCount 相当于 window.basicCount = 2;
  basicCount = 2;
  return basicCount + number;
}
```

不过在 eslint 帮助下，这种场景现在基本没人会犯了，eslint 会直接报错，了解下就好。

### 被遗忘的计时器

无用的计时器忘记清理是新手最容易犯的错误之一。

就拿一个 vue 组件来做例子。

```
<template>
  <div></div>
</template>

<script>
export default {
  methods: {
    refresh() {
      // 获取一些数据
    },
  },
  mounted() {
    setInterval(function() {
      // 轮询获取数据
      this.refresh()
    }, 2000)
  },
}
</script>
```

上面的组件销毁的时候，`setInterval` 还是在运行的，里面涉及到的内存都是没法回收的（浏览器会认为这是必须的内存，不是垃圾内存），需要在组件销毁的时候清除计时器，如下：

```
<template>
  <div></div>
</template>

<script>
export default {
  methods: {
    refresh() {
      // 获取一些数据
    },
  },
  mounted() {
    this.refreshInterval = setInterval(function() {
      // 轮询获取数据
      this.refresh()
    }, 2000)
  },
  beforeDestroy() {
    clearInterval(this.refreshInterval)
  },
}
</script>
```

### 被遗忘的事件监听器

无用的事件监听器忘记清理是新手最容易犯的错误之一。

还是继续使用 vue 组件做例子。

```
<template>
  <div></div>
</template>

<script>
export default {
  mounted() {
    window.addEventListener('resize', () => {
      // 这里做一些操作
    })
  },
}
</script>
```

上面的组件销毁的时候，resize 事件还是在监听中，里面涉及到的内存都是没法回收的（浏览器会认为这是必须的内存，不是垃圾内存），需要在组件销毁的时候移除相关的事件，如下：

```
<template>
  <div></div>
</template>

<script>
export default {
  mounted() {
    this.resizeEventCallback = () => {
      // 这里做一些操作
    }
    window.addEventListener('resize', this.resizeEventCallback)
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.resizeEventCallback)
  },
}
</script>
```

### 被遗忘的 ES6 Set 成员

如果对 Set 不熟悉，可以看[这里](http://es6.ruanyifeng.com/#docs/set-map#Set)。

如下是有内存泄漏的（成员是引用类型的，即对象）:

```
let map = new Set();
let value = { test: 22};
map.add(value);

value= null;
```

需要改成这样，才没内存泄漏：

```
let map = new Set();
let value = { test: 22};
map.add(value);

map.delete(value);
value = null;
```

有个更便捷的方式，使用 WeakSet，WeakSet 的成员是**弱引用**，内存回收不会考虑到这个引用是否存在。

```
let map = new WeakSet();
let value = { test: 22};
map.add(value);

value = null;
```

### 被遗忘的 ES6 Map 键名

如果对 Map 不熟悉，可以看[这里](http://es6.ruanyifeng.com/#docs/set-map#Map)。

如下是有内存泄漏的（键值是引用类型的，即对象）:

```
let map = new Map();
let key = new Array(5 * 1024 * 1024);
map.set(key, 1);
key = null;
```

需要改成这样，才没内存泄漏：

```
let map = new Map();
let key = new Array(5 * 1024 * 1024);
map.set(key, 1);

map.delete(key);
key = null;
```

有个更便捷的方式，使用 WeakMap，WeakMap 的键名是**弱引用**，内存回收不会考虑到这个引用是否存在。

```
let map = new WeakMap();
let key = new Array(5 * 1024 * 1024);
map.set(key, 1);

key = null;
```

### 被遗忘的订阅发布事件监听器

这个跟上面的**被遗忘的事件监听器**的道理是一样的。

假设订阅发布事件有三个方法 `emit` 、`on` 、`off` 三个方法。

还是继续使用 vue 组件做例子。

```
<template>
  <div @click="onClick"></div>
</template>

<script>
import customEvent from 'event'

export default {
  methods: {
    onClick() {
      customEvent.emit('test', { type: 'click' })
    },
  },
  mounted() {
    customEvent.on('test', data => {
      // 一些逻辑
      console.log(data)
    })
  },
}
</script>
```

上面的组件销毁的时候，自定义 test 事件还是在监听中，里面涉及到的内存都是没法回收的（浏览器会认为这是必须的内存，不是垃圾内存），需要在组件销毁的时候移除相关的事件，如下：

```
<template>
  <div @click="onClick"></div>
</template>

<script>
import customEvent from 'event'

export default {
  methods: {
    onClick() {
      customEvent.emit('test', { type: 'click' })
    },
  },
  mounted() {
    customEvent.on('test', data => {
      // 一些逻辑
      console.log(data)
    })
  },
  beforeDestroy() {
    customEvent.off('test')
  },
}
</script>
```

### 被遗忘的闭包

闭包是经常使用的，闭包能给我们带来很多便利。

首先看下这个代码：

```
function closure() {
  const name = 'xianshannan'
  return () => {
    return name
      .split('')
      .reverse()
      .join('')
  }
}
const reverseName = closure()
// 这里调用了 reverseName
reverseName();
```

上面有没有内存泄漏？

上面是没有内存泄漏的，因为`name` 变量是要用到的（非垃圾）。这也是从侧面反映了闭包的缺点，内存占用相对高，量多了会有性能影响。

但是改成这样就是有内存泄漏的：

```
function closure() {
  const name = 'xianshannan'
  return () => {
    return name
      .split('')
      .reverse()
      .join('')
  }
}
const reverseName = closure()
```

在当前执行环境未结束的情况下，严格来说，这样是有内存泄漏的，`name` 变量是被 `closure` 返回的函数调用了，但是返回的函数没被使用，这个场景下 `name` 就属于垃圾内存。`name` 不是必须的，但是还是占用了内存，也不可被回收。

当然这种也是极端情况，很少人会犯这种低级错误。这个例子可以让我们更清楚的认识内存泄漏。

### 脱离 DOM 的引用

每个页面上的 DOM 都是占用内存的，假设有一个页面 A 元素，我们获取到了 A 元素 DOM 对象，然后赋值到了一个变量（内存指向是一样的），然后移除了页面的 A 元素，如果这个变量由于其他原因没有被回收，那么就存在内存泄漏，如下面的例子：

```
class Test {
  constructor() {
    this.elements = {
      button: document.querySelector('#button'),
      div: document.querySelector('#div'),
      span: document.querySelector('#span'),
    }
  }
  removeButton() {
    document.body.removeChild(this.elements.button)
    // this.elements.button = null
  }
}

const a = new Test()
a.removeButton()
```

上面的例子 button 元素 虽然在页面上移除了，但是内存指向换为了 `this.elements.button`，内存占用还是存在的。所以上面的代码还需要这样写： `this.elements.button = null`，手动释放这个内存。

## 如何发现内存泄漏？

内存泄漏时，内存一般都是会周期性的增长，我们可以借助谷歌浏览器的开发者工具进行判别。

这里不进行详细的开发者工具使用说明，详细看[谷歌开发者工具](https://developers.google.com/web/tools/chrome-devtools/?hl=zh-cn)，不过谷歌浏览器是不断迭代更新的，有些文档落后了，界面长得不一样。

本人测试的谷歌版本为：**版本 76.0.3809.100（正式版本） （64 位）**。

这里针对下面例子进行一步一步的排查和找到问题出现在哪里：

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
  </head>
  <body>
    <div id="app">
      <button id="run">运行</button>
      <button id="stop">停止</button>
    </div>
    <script>
      const arr = []
      for (let i = 0; i < 200000; i++) {
        arr.push(i)
      }
      let newArr = []

      function run() {
        newArr = newArr.concat(arr)
      }

      let clearRun

      document.querySelector('#run').onclick = function() {
        clearRun = setInterval(() => {
          run()
        }, 1000)
      }

      document.querySelector('#stop').onclick = function() {
        clearInterval(clearRun)
      }
    </script>
  </body>
</html>
```

上面例子的代码可以直接运行的，怎么运行我就不多说了。

### 第一步：确定是否是内存泄漏问题

访问上面的代码页面，打开谷歌开发者工具，切换至 **Performance** 选项，勾选 `Memory` 选项。

在页面上点击**运行按钮**，然后在开发者工具上面点击左上角的录制按钮，10 秒后在页面上点击**停止按钮**，5 秒后停止内存录制。得到的内存走势如下：

![clipboard.png](https://segmentfault.com/img/bVbxaaT)

由上图可知，10 秒之前内存周期性增长，10 后点击了停止按钮，内存平稳，不再递增。

我们可以使用内存走势图判断当前页面是否有内存泄漏。经过测试上面的代码 `20000` 个数组项改为 `20` 个数组项，内存走势也一样能看出来。

### 第二步：查找内存泄漏出现的位置

上一步确认是内存泄漏问题后，我们继续利用谷歌开发者工具进行问题查找。

访问上面的代码页面，打开谷歌开发者工具，切换至 **Memory** 选项。页面上点击运行按钮，然后点击开发者工具左上角录制按钮，录制完成后继续点击录制，知道录制完三个为止。然后点击页面的停止按钮，再连续录制 3 次内存（不要清理之前的录制）。下图就是进行这些步骤后的截图：

![clipboard.png](https://segmentfault.com/img/bVbxaaO)

从这里也可以看出，点击运行按钮后，内存在不断递增。点击停止按钮后，内存就平稳了。虽然我们也可以使用这样的方式来判别是否存在内存泄漏，但是不够第一步的方法便捷，走势图也更直观。

然后第二步的主要目的来了，记录 JavaScript 堆内存才是内存录制的主要目的，我们可以看到哪个堆占用的内存更高。

在刚才的录制中选择 Snapshot 3 ，然后按照 **Shallow Size** 进行逆序排序（不了解的可以看[内存术语](https://developers.google.com/web/tools/chrome-devtools/memory-problems/memory-101?hl=zh-cn)），如下：

![clipboard.png](https://segmentfault.com/img/bVbxaaL)

从内存记录中，发现 array 对象占用最大，展开后发现，第一个 `object elements` 占用最大，选择这个 `object elements` 后可以在下面看到 `newArr` 变量，然后点击 `test:23`，只要是高亮下划线的地方都可以进去看看 （测试页面是 test.html），可以跳转到 `newArr` 附近。

## 参考资料

- [维基百科-内存泄漏](https://zh.wikipedia.org/zh-hans/内存泄漏)
- [内存管理](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Memory_Management)
- [4 Types of Memory Leaks in JavaScript and How to Get Rid Of Them](https://auth0.com/blog/four-types-of-leaks-in-your-javascript-code-and-how-to-get-rid-of-them/)
- [javascript 垃圾回收机制](https://juejin.im/post/5b684f30f265da0f9f4e87cf)
- [How JavaScript works: memory management + how to handle 4 common memory leaks](https://blog.sessionstack.com/how-javascript-works-memory-management-how-to-handle-4-common-memory-leaks-3f28b94cfbec)