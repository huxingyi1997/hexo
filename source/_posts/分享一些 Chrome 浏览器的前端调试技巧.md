---
title:  分享一些 Chrome 浏览器的前端调试技巧
date:  2021-03-21 09:20:20
categories: 
- web前端
tags:
- JavaScript
- Chrome 浏览器
- 调试
---
转自[分享一些 Chrome 浏览器的前端调试技巧](https://juejin.cn/post/6844903873279164423)

相信大部分前端同学都是用Chrome浏览器进行开发，这篇博客要分享的基本上是除了我们常用`console.log`之外的，Chrome开发者工具控制面板提供的调试方法~

首先在地址栏敲入：`about:blank` 创建一个空白页，再打开控制台~

开始操作演示~（多图预警！~~

<!-- more -->

## 1.关于console

关于console对象，其实提供了很丰富的API，可自查文档~

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421092735.webp)



## 2.关于Console控制面板

以下示例方法只存在于Chrome控制台Console面板~在JavaScripts中写是没有的哦!

### 2.1 $家族

#### 2.1.1 $_

返回上一个被执行过的值~

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421092751.webp)

虽说很类似于命令行里的`!!`，但是$_并不会再执行一次表达式，如下图可证：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421092816.webp)

如果之前的值没有保存在变量里，可以通过这个方法临时访问~（为什么说临时，因为当你执行完下一个表达式后，$_已经更新了哈）

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421092836.webp)

#### 2.1.2 $0 - $4

$0、$1、$2、$3、$4五个指令相当于在Elements面板最近选择过的五个引用。 比如我在Elements面板上随意点击了掘金网站上的五个DOM节点。从时间线上，$4是我第一个点击的。而$0是我第五个，也即是最后一个点击的。利用此方法可以快速在Console面板调试你选中的节点！

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421092915.webp)

补充一下，还有点类似正则匹配~如下所示

```
function replacer(match, $1, $2, $3, $4, $5) {
	return [$1, $2, $3, $4, $5].join(' - ');
}
const str = 'abc12345#$*%[hello]{world}'
    .replace(/([^\d]*)(\d*)([^\w]*)(\[.*\])(\{.*\})/, replacer);

console.log(str); // abc - 12345 - #$*% - [hello] - {world}
```

#### 2.1.3 $

类似于`document.querySelector()`。不过比较少为人知的应该是它的第二个参数。指定从哪个节点开始选择。有时候想减少范围时，尤其管用！

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421093041.webp)

P.S. 函数签名`$(selector, [startNode])`。

#### 2.1.4 ?

类似于`document.querySelectorAll()`，可参考同上。

P.S. 函数签名`?(selector, [startNode])`

#### 2.1.5 $x

根据XPath表达式去查找节点。如下图示例：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421093112.webp)

查找掘金站内所有含有href属性的a节点，然后遍历过滤含有http或https的节点~ 当然好像目前来说，大部分情况直接用`$`、`?`可以覆盖，说不定特殊情况下`$x`会很有用。有需要的同学可以了解学习一下~ XPath表达式规则可参考：[XPath 语法](https://www.w3school.com.cn/xpath/xpath_syntax.asp)

P.S. 函数签名`$x(selector, [startNode])`

### 2.2 API工具方法

以下方法同样只存在于Chrome控制台Console面板里，同学们请注意哦~

#### 2.2.1 keys/values

见名知意。功能类似于`Object.keys`，`Object.values`

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421093147.webp)

#### 2.2.2 monitor/unmonitor

用来观察函数调用的工具方法。在函数调用的时候，可以同步输出函数名以及参数。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421093502.webp)

当不再需要观察该函数时，调用unmonitor取消即可。

但是匿名函数不会生效，因为获取不到名字.

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421093522.webp)

#### 2.2.3 monitorEvents/unmonitorEvents

可以观察对像的事件~

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421093533.webp)

也可以同时观察对象的多个事件~

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421093546.webp)

同样，使用unmonitorEvents取消观察。结合以上的$家族一起使用更便利哦

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421093559.webp)

P.S. 函数签名：`monitorEvents(object[, events])`

#### 2.2.4 copy

快速拷贝一个对象为字符串表示方式到剪切板~

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421095229.webp)

#### 2.2.5 getEventListeners

获取注册到一个对象上的所有事件监听器~

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421095255.webp)

其实还有内置的inspect、debug/undebug等方法，大家可以自行搜索，都很有用~这里就不一一介绍了~



## 3.关于断点调试

断点调试十分重要，以往我们可能直接在代码里添加debugger，然后刷新浏览器调试。实际上除了这种方法外还有很多种断点。

### 3.1 DOM breakpoint

在Elements面板，右键点击节点唤出菜单，添加对应的DOM断点，可以监测指定节点的子树修改、属性修改、以及节点的移除。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421095332.webp)

### 3.2 Source breakpoint

有时候无需在源码中添加debugger。直接在Source面板添加断点即可调试。见下图行号上的小蓝色箭头！

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421095355.webp)

### 3.3 Conditional breakpoint

条件断点。只有符合条件时，才会触发断点。见下图行号上的小橙色箭头！

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421095748.webp)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421095805.webp)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421095819.webp)

除此之外，还有blackbox、XHR(fetch) breakpoint等各种Chrome提供的工具，建议同学们多去了解一下，说不定关键时候可以发挥很大的作用~



## 4.小技巧

如果找不到对应的指令，可以在控制台使用快捷键Ctrl + Shift + P。MacOS的话就是Command + Shift + P（这个和编辑器是一样的道理）。快速搜索你想要的控制面板工具~

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210421095831.webp)



## 小结

其实长久以来，我也一直只会用console.log和简单的debugger来调试Web应用，有时候遇到复杂的问题时，匮乏的调试方法种类难以快速定位问题，从而降低工作效率。因此针对此类情况，学习如何更好的调试相信是会对工作有极大的帮助！

最后，欢迎同学们补充或指正这些调试工具方法~

当然，对大家如有帮助，不甚荣幸~