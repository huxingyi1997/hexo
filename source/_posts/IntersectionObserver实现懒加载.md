---
title:  IntersectionObserver实现懒加载
date:  2021-04-01 20:40:00
categories: 
- web前端
tags:
- JavaScript
- 懒加载
- IntersectionObserver
---
懒加载等到用户滚动页面，当图片滚动到视区时才发出对应的网络请求，达到节省带宽的目的，使得网页加载更快。其实懒加载有很常规的实现方案，先通过data-src存放真正需要显示的图片路径，而img自带的src放一张占位图片。监听鼠标滚动事件，通过 `el.getBoundingClientRect()` 获取到当前元素与视口的位置关系，确定图片是否加载。在加载完成之后为了性能考虑，删除 `data-src` ，这样就可以避免重复的图片加载，其中要注意 `getBoundingClientRect` 会触发浏览器的回流。IntersectionObserver是个还在草案中的API，不过大部分浏览器均已实现（除了我大IE），适用该API也能实现上述的懒加载功能。

<!-- more -->

# 1.IntersectionObserver是什么

## 1.1 概念

> IntersectionObserver接口(从属于Intersection Observer API)为开发者提供了一种可以异步监听目标元素与其祖先或视窗(viewport)交叉状态的手段。祖先元素与视窗(viewport)被称为根(root)。

这是MDN上给的官方概念，不用去管它，我粘出来只是为了显得专业点嘛...

重点看这里**监听目标元素与其祖先或视窗交叉状态的手段**，其实就是观察一个元素是否在视窗可见。

![是否可见](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418203027.webp)



可以看到，交叉了就是说明当前元素在视窗里，当前就是可见的了。

![示意图](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418203034.webp)



- [MDN IntersectionObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/IntersectionObserver)
- [阮一峰 IntersectionObserver API 使用教程](https://www.ruanyifeng.com/blog/2016/11/intersectionobserver_api.html)

`IntersectionObserver` 接口 (从属于`Intersection Observer API`) 提供了一种**异步**观察目标元素与其祖先元素或顶级文档视窗(`viewport`)交叉状态的方法。祖先元素与视窗(`viewport`)被称为根(`root`)。由于可见（visible）的本质是，目标元素与视口产生一个交叉区，所以这个 API 叫做"**交叉观察器**"。

简单点说就是**它可以观察 root（默认是视口）和目标元素的交叉情况，当交叉率是 0% 或者 10% 或者更多的时候，可以触发指定的回调。**

当一个 `IntersectionObserver` 对象被创建时，其被配置为监听根中一段给定比例的可见区域。一旦 `IntersectionObserver` 被创建，则无法更改其配置，所以一个给定的观察者对象只能用来监听可见区域的特定变化值；然而，你可以在同一个观察者对象中配置监听多个目标元素。

## 1.2 浏览器支持

此时此刻，你也许想知道关于这项特性的浏览器支持情况。

[caniuse](https://caniuse.com/#search=IntersectionObserver) 兼容性报告目前支持率是 90.12%，还不推荐用于大众化的场景中，但是它的能力和性能非常的好。

![IntersectionObserver](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418203043.webp)

Intersection Observer 现在已被 [Edge、Firefox、Chrome 和 Opera](http://caniuse.com/#feat=intersectionobserver) 支持，这是一个好消息。

# 2.IntersectionObserver的API

## 2.1 使用方法

```
var io = new IntersectionObserver(callback, options)
```

其实就是一个简单的构造函数。

以上代码会返回一个`IntersectionObserver`实例，`callback`是当元素的可见性变化时候的回调函数，`options`是一些配置项（可选）。

我们使用返回的这个实例来进行一些操作。

```
io.observe(document.querySelector('img'))  开始观察，接受一个DOM节点对象
io.unobserve(element)   停止观察 接受一个element元素
io.disconnect() 关闭观察器
```

## 2.2 optionsroot

options是一个对象，用来配置参数，也可以不填。共有三个属性，具体如下：

| 属性       | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| root       | 所监听对象的具体祖先元素。如果未传入值或值为`null`，则默认使用顶级文档的视窗(一般为html)。 |
| rootMargin | 计算交叉时添加到**根(root)边界盒[bounding box](https://developer.mozilla.org/en-US/docs/Glossary/bounding_box)的矩形偏移量， 可以有效的缩小或扩大根的判定范围从而满足计算需要。所有的偏移量均可用像素**(`px`)或**百分比**(`%`)来表达, 默认值为"0px 0px 0px 0px"。 |
| threshold  | 一个包含阈值的列表, 按升序排列, 列表中的每个阈值都是监听对象的交叉区域与边界区域的比率。当监听对象的任何阈值被越过时，都会触发callback。默认值为0。 |

root用于观察的根元素，默认是浏览器的视口，也可以指定具体元素，指定元素的时候用于观察的元素必须是指定元素的子元素

threshold用来指定交叉比例，决定什么时候触发回调函数，是一个数组，默认是`[0]`。

```
const options = {
	root: null,
	threshold: [0, 0.5, 1]
}
var io = new IntersectionObserver(callback, options)
io.observe(document.querySelector('img'))
```

上面代码，我们指定了交叉比例为0，0.5，1，当观察元素img0%、50%、100%时候就会触发回调函数

rootMargin

用来扩大或者缩小视窗的的大小，使用css的定义方法，`10px 10px 30px 20px`表示top、right、bottom 和 left的值

```
const options = {
	root: document.querySelector('.box'),
	threshold: [0, 0.5, 1],
	rootMargin: '30px 100px 20px'
}
```

为了方便理解，我画了张图，如下

![options](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418203055.webp)

首先我们来看下图上的问题，蓝线是什么呢？他就是咱们定义的root元素，我们添加了`rootMargin`属性，将视窗的增大了，虚线就是现在的视窗，所以元素现在也就在视窗里面了。

由此可见，root元素只有在`rootMargin`为空的时候才是绝对的视窗。

说了简单的options，接下来我们看下`callback`。

## 2.3 callback

callback是添加监听后，当监听目标发生滚动变化时触发的回调函数。接收一个参数entries，即IntersectionObserverEntry实例。描述了目标元素与root的交叉状态。具体参数如下：

| 属性                  | 说明                                                         |
| --------------------- | ------------------------------------------------------------ |
| boundingClientRect    | 返回包含目标元素的边界信息，返回结果与element.getBoundingClientRect() 相同 |
| **intersectionRatio** | 返回目标元素出现在可视区的比例                               |
| intersectionRect      | 用来描述root和目标元素的相交区域                             |
| **isIntersecting**    | 返回一个布尔值，下列两种操作均会触发callback：1. 如果目标元素出现在root可视区，返回true。2. 如果从root可视区消失，返回false |
| rootBounds            | 用来描述交叉区域观察者(intersection observer)中的根.         |
| target                | 目标元素：与根出现相交区域改变的元素 (Element)               |
| time                  | 返回一个记录从 IntersectionObserver 的时间原点到交叉被触发的时间的时间戳 |

表格中加粗的两个属性是比较常用的判断条件：**isIntersecting**和**intersectionRatio**

![IntersectionObserverEntry打印的值](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418203105.webp)

上面我们说到，当元素的可见性变化时，就会触发callback函数。

callback函数会触发两次，元素进入视窗（开始可见时）和元素离开视窗（开始不可见时）都会触发

```
var io = new IntersectionObserver((entries)=>{
	console.log(entries)
})

io.observe($0)
```

以上代码，请在chrome控制台进行调试，这里我使用了`$0`选择了上一次我审查元素的选择的节点

运行结果如下

![运行结果](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418203114.webp)

我们可以看到callback函数有个`entries`参数，它是个`IntersectionObserverEntry`对象数组，

## 2.4 方法

介绍了这么多配置项及参数，差点忘了最重要的，IntersectionObserver有哪些方法？ 如果要监听某些元素，则必须要对该元素执行一下observe

| 方法          | 说明                                                |
| ------------- | --------------------------------------------------- |
| observe()     | 开始监听一个目标元素                                |
| unobserve()   | 停止监听特定目标元素                                |
| takeRecords() | 返回所有观察目标的IntersectionObserverEntry对象数组 |
| disconnect()  | 使IntersectionObserver对象停止全部监听工作          |


接下来我们重点说下IntersectionObserverEntry对象

## 2.5 IntersectionObserverEntry

`IntersectionObserverEntry`提供观察元素的信息，有七个属性。

> boundingClientRect  目标元素的矩形信息
>
> intersectionRatio   相交区域和目标元素的比例值   intersectionRect/boundingClientRect 不可见时小于等于0
>
> intersectionRect    目标元素和视窗（根）相交的矩形信息 可以称为相交区域
>
> isIntersecting      目标元素当前是否可见 Boolean值 可见为true
>
> rootBounds			  根元素的矩形信息，没有指定根元素就是当前视窗的矩形信息
>
> target					  观察的目标元素
>
> time                返回一个记录从`IntersectionObserver`的时间到交叉被触发的时间的时间戳

上面几个矩形信息的关系如下

![关系](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418203122.webp)

👇 划重点

**intersectionRatio**和**isIntersecting**是用来判断元素是否可见的，押题咯...

详细示例如下

```
{
    boundingClientRect: DOMRectReadOnly {x: 8, y: 380, width: 300, height: 300, top: 380, …}
    intersectionRatio: 0.023333333432674408
    intersectionRect: DOMRectReadOnly {x: 8, y: 380, width: 300, height: 7, top: 380, …}
    isIntersecting: true
    rootBounds: DOMRectReadOnly {x: 0, y: 0, width: 1903, height: 387, top: 0, …}
    target: img.lazy
    time: 440149.8099999735
}
```

IntersectionObserverEntry上述示例的每项详细配置如下

- `time`：可见性发生变化的时间，是一个高精度时间戳，单位为毫秒，返回一个记录从 `IntersectionObserver` 的时间原点(time origin)到交叉被触发的时间的时间戳(DOMHighResTimeStamp)，图中为440149.8099999735
- `target`：被观察的目标元素，是一个 DOM 节点对象，选择了图像img中的.lazy类型元素
- `rootBounds`：根元素的矩形区域的信息，`getBoundingClientRect()` 方法的返回值，如果没有根元素（即直接相对于视口滚动），则返回 `null`
- `boundingClientRect`：目标元素的矩形区域的信息
- `intersectionRect`：目标元素与视口（或根元素）的交叉区域的信息
- `intersectionRatio`：目标元素的可见比例，即 `intersectionRect` 占 `boundingClientRect` 的比例，完全可见时为 1，完全不可见时小于等于0
- [`isIntersecting`](https://developer.mozilla.org/zh-CN/docs/Web/API/IntersectionObserverEntry/isIntersecting) 是否交叉，交叉为true

## 2.6 配置

`observerConfig.root` 所监听对象的具体祖先元素(element)。如果未传入值或值为 `null`，则默认使用顶级文档的视窗。

`observerConfig.rootMargin` 计算交叉时添加到根(`root`)边界盒 bounding box 的矩形偏移量， 可以有效的缩小或扩大根的判定范围从而满足计算需要。所有的偏移量均可用像素(pixel)(px)或百分比(percentage)(%)来表达, 默认值为"`0px 0px 0px 0px`"，用法和普通的 `margin` 一样（top、right、bottom、left）。

`observerConfig.threshold` **注意 MDN 文档中的 `thresholds` 是错误的**，一个包含阈值的列表, 按升序排列, 列表中的每个阈值都是监听对象的交叉区域与边界区域的比率。当监听对象的任何阈值被越过时，都会生成一个通知(Notification)。如果构造器未传入值, 则默认值为0，也可以是 `[0, 0.25, 0.5, 0.75, 1]`。

# 3.实际应用

通过上面一些概念我们大概了解了`IntersectionObserver`是个什么东西，接下来我们用它来写点代码，投入实战。

## 3.1 图片懒加载

第一个应用写什么呢？没错就是懒加载。

通过IntersectionObserver来实现懒加载，我们只需要设置回调，判断当前元素是否可见，再进行渲染操作就行了，而不用去关心内部的计算，相比于常规方案大大提高了开发效率。

```
const imgList = [...document.querySelectorAll('img')];

var io = new IntersectionObserver((entries) => {
    entries.forEach(item => {
        // isIntersecting是一个Boolean值，判断目标元素当前是否可见
        if (item.isIntersecting) {
            item.target.src = item.target.dataset.src;
            // 图片加载后即停止监听该元素
            io.unobserve(item.target);
        }
    })
}, {
	root: document.querySelector('.root');
})

// observe遍历监听所有img节点
imgList.forEach(img => io.observe(img));
```

## 3.2 埋点曝光

假如有个需求，对一个页面中的特定元素，只有在其完全显示在可视区内时进行埋点曝光。

```
const boxList = [...document.querySelectorAll('.box')];

var io = new IntersectionObserver((entries) => {
    entries.forEach(item => {
        // intersectionRatio === 1说明该元素完全暴露出来，符合业务需求
        if (item.intersectionRatio === 1) {
            // ... 埋点曝光代码
            io.unobserve(item.target)
        }
    })
}, {
    root: null,
    threshold: 1, // 阀值设为1，当只有比例达到1时才触发回调函数
})

// observe遍历监听所有box节点
boxList.forEach(box => io.observe(box))
```

**总之，这是一个相当方便好用的API！**






