---
title:  Toy-React基础版
date: 2022-03-23 20:18:33
categories: 
- web前端
tags:
- React
- 前端
- 轮子
---

这个Toy-React 是我选择在入职前的练手项目，比较基础，后续亟待完善（主要想用上Typescript）。主要目的是希望它可以作为一把开启React源码大门的钥匙。进一步学习React的原理知识，更加深刻理解React。
<!-- more -->

# React框架背后的核心机理

## 1. 项目初始化

```bash
// 1.创建toy-react文件夹
mkdir toy-react

// 2.进入toy-react文件夹
cd toy-react

// 3.创建项目
npm init -y
```

## 2. 配置webpack环境

### 1.安装webpack

```bash
npm install webpack webpack-cli --save-dev
```

### 2.配置webpack

建立 `webpack.config.js `

```js
// node的标准export
module.exports = {
	// 入口文件
	entry: {
		main: './main.js'
	}
    // 增加build后文件可读性，不压缩打包后文件
    mode: "development",
    optimization: {
    	minimize: false
    }
}
```

### 3.配置babel

安装babel

```bash
// babel-loader webpack将babel打包到main.js
// @babel/core  babel核心
// @babel/preset-env 将babel转义到所需js版本
// https://www.babeljs.cn/docs/babel-preset-env
npm install babel-loader @babel/core @babel/preset-env --save-dev
```

在 `webpack.config.js `中配置 `babel-loader `， `babel-loader` 将es高级语法转化为浏览器能够读懂的语法， `@babel/preset-env `作为预转译插件

```js
module.exports = {
    // modules 打包规则
    module: {
    	rules: [
        	// js 文件需经过babel
        	{
            	test: /\.js$/,
                use: {
                	loader: 'babel-loader',
                    // 配置babel-loader
                    options: {
                    	presets: ['@babel/preset-env']
                    }
                }
            }
        ]
    }
}
```

安装babel的jsx插件，`@babel/plugin-transform-react-jsx` 解析jsx语法糖

```bash
npm install @babel/plugin-transform-react-jsx --save-dev
```

配置babel的jsx插件

```js
module.exports = {
    // modules 打包规则
    module: {
    	rules: [
        	// js 文件需经过babel
        	{
            	test: /\.js$/,
                use: {
                	loader: 'babel-loader',
                    // 配置babel-loader
                    options: {
                    	presets: ['@babel/preset-env'],
                        plugins: ['@babel/plugin-transform-react-jsx']
                    }
                }
            }
        ]
    }
}
```

因为是手写的react~~借助react-jsx的塑料react~~，所以我们可以重新配置`@babel/plugin-transform-react-jsx`，让它看起来没有那么react

```js
module.exports = {
    module: {
    	rules: [
        	{
            	test: /\.js$/,
                use: {
                	loader: 'babel-loader',
                    // 配置babel-loader
                    options: {
                    	presets: ['@babel/preset-env'],
                        // 这样打包后的‘React.createElement就会变成‘ToyReact.createElement’
                        plugins: [['@babel/plugin-transform-react-jsx', {pragma: 'ToyReact.createElement'}]]
                    }
                }
            }
        ]
    }
}
```

## 3. 实现toyReact

为了更加直观的展示效果, 我们可以将打包后的script文件, 引入到`dist/main.html`中。

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    <body>
    </body>
    <script src="main.js" ></script>
</html>
```

使用`@babel/plugin-transform-react-jsx`打包之后jsx(`main.js`)是这个样子

```jsx
// 打包前的jsx
let a = <div id="a" class="c">
	<div></div>
    <div></div>
</div>

// 打包后的jsx
var a = React.createElement("div", 
{id: "a", "class": "c"}, 
React.createElement("div", null), 
React.createElement("div", null))
```

所以我们可以知道，`React.createElement()`会传入三个参数`tagName`,`tag`的属性列表，`tag`的子元素

```js
React.createElement(tagName, attributes, ...children)
```

那么我们可以实现最简单的`createElement`

```js
export class ToyReact {
    static createElement (tagName, attributes, ...children) {
        return document.createElement(tagName)
    }
}
```

现在为我们的`createElement`增加属性和子节点的配置

```js
export class ToyReact {
    static createElement (tagName, attributes, ...children) {
        let e = document.createElement(tagName)
        // 增加属性
        for (let i in attributes) {
            e.setAttribute(i, attributes[i])
        }
        // 增加子节点
        // 扩展运算符将children包装为一个数组
        for (let child of children) {
            e.appendChild(child)
        }
        return e
    }
}
```

我们的`createElement`已经可以实现使用，但是尚未考虑**文本节点**

```js
export class ToyReact {
    static createElement (tagName, attributes, ...children) {
        let e = document.createElement(tagName)
        for (let i in attributes) {
            e.setAttribute(i, attributes[i])
        }

        for (let child of children) {
            // 将child创建为文本节点
            if (typeof child === "string") {
                child = document.createTextNode(child)
            }
            e.appendChild(child)
        }
        return e
    }
}
```

目前，我们的`createElement`可以支持基础的dom操作，是合格的语法糖🍬了。

但问题依旧存在：在react的jsx中，小写的tagName对应生成原生dom对象，而大写的tagName则对应**自定义组件**。`tagName`参数支持传入的，不仅仅是字符串，还支持**class组件**以及**函数组件**

故而，`tagName`其实是`tagType`参数，我们需要根据`tagType`生成不同的dom对象 （目前只支持class组件）

### 3.1 普通节点的wrapper实现

新建ElementWrapper类,并且新增 `创建实体DOM`方法 以及 `setAttribute`和 `appendChild` 方法。

appendChild方法中`component` 都是经过 `ElementWrapper` 或者

`TextWrapper` 实例化后的值。 `component.root` 指代的即是元素节点或者文本节点

```js
class ElementWrapper {
	constructor (type) {
    	// 创建根元素
    	this.root = document.createElement(type)
    }
    // 配置属性
    setAttribute (name, value) {
    	this.root.setAttribute(name, value)
    }
    // 添加子元素
    // 添加的是component，所以要取出传入的component的root
    appendChild (component) {
    	this.root.appendChild(component.root)
    }

}
```

新建TextNodeWrapper类, 并且只需要创建一个文本节点即可。

```js
// 文本节点不需要设置属性及添加子元素
class TextWrapper {
	constructor (content) {
    	this.root = document.createTextNode(content)
    }
}
```

### 3.2 实现Component类

自定义组件需要继承Component类，限定它的默认行为

```jsx
class MyComponent extends Component {
	render () {
    	return <div>
          <h1>myComponent</h1>
          {this.children}
        </div>
    }
}
```

那么模仿react的实现，我们的Component类大概是这个样子：

```js
class Component {
	constructor () {
    	// 不需要有什么行为
        // 取到props
        this.props = Object.create(null)
        this.children = []
        // 初始化root
        this._root = null
    }
    // 把Component的属性存起来
    setAttribute (name, value) {
    	this.props[name] = value
    }
    // 添加子元素
    appendChild (component) {
    	this.children.push(component)
    }
    // 设置 root 的getter
    get root () {
    	if (!this._root) {
        // 渲染组件，调用组件的render方法
        // 如果render之后是component，则会递归调用，直至其成为elementWrapper或者textWrapper
        	this._root = this.render().root
        }
        return this._root
    }
}
```

这里可能会有疑问, 为什么需要新增一个额外的类。

当babel解析到`TestComponent`的时候, 我们直接实例化它, 并且给实例化后的值 `setAttribute`属性以及` appendChild` 子节点。 我们当然不能在main.js中写这些方法的具体实现。因此我们让TestComponent去`继承` Component, 让Component类去实现这两个方法。

### 3.3 实现render

```js
export class ToyReact {
    static render (component, parentElement) {
        // parentElement为实际dom
        parentElement.appendChild(component.root)
    }
}
```

### 3.4 重构`createElement`

判断element的类型, 如果是元素标签的字符串类型, 那么就通过ElementWrapper创建实DOM, 否则就直接实例化本身返回其render的jsx, 进行重新调用createElement构建元素。

```js
export class ToyReact {
    static createElement (tagType, attributes, ...children) {
        let element
        if (typeof tagType === 'string') {
            // 如果是小写的tagName，则生成ElementWrapper对象
            element = new ElementWrapper(tagType)
        } else {
            // 如果是组件，则生成对应的组件对象
            element = new tagType
        }
        // 增加属性
        for (let name in attributes) {
            // 调用元素的setAttribute方法
            element.setAttribute(name, attributes[name])
        }
        // 增加子节点
        // 扩展运算符将children包装为一个数组
        let insertChildren = (children) => {
            for (let child of children) {
                // 如果child为null，不做任何处理
                if (child === null) {
                    continue
                }
                // 将child创建为文本节点，如果child是文本节点
                if (typeof child === 'string') {
                    child = new TextWrapper(child)
                }
                // 当child是数组的时候，即component中的children，需要展开child
                if (typeof child === 'object' && child instanceof Array) {
                    // 递归调用
                    insertChildren(child)
                } else {
                    // 调用元素的appendChild方法
                    element.appendChild(child)
                }
            }
        }
        insertChildren(children)
        return element
    }
}
```

我们通过一个简单的递归函数去处理child是数组的情况。现在我们的页面可以正常的显示DOM结构, 并且拥有props的能力。 

### 3.5 main.js文件

```jsx
import { Component, ToyReact } from './toy-react.js'

class MyComponent extends Component {
    render () {
        return <div>
            <h1>myComponent</h1>
            {this.children}
        </div>
    }
}

ToyReact.render(<MyComponent id="a" class="c">
    <h2>ssss</h2>
</MyComponent>, document.body)
```

## 4. 一些笔记

通过[阅读Babel官网](https://www.babeljs.cn/docs/babel-preset-env)，了解到@babel/plugin-transform-react-jsx插件有两种编译jsx的方式：

- 运行时编译方式（React Automatic Runtime）
- 手动引入React.createElement的方式（React Classic Runtime）

这解释了困扰我的两个问题：

> 1、为什么定义了createElement方法却没有调用？
>
> 因为babel jsx转换插件是“运行时编译”且pagram参数为createElement，所以代码编译时会自动解析jsx并调用createElement方法。

> 2、为什么render方法里父节点要接收一个component.root作为参数而不是component？
>
> 同样是由于babel jsx插件的“运行时”编译，调用createElement方法后会实例化一个Component对象，该对象初始root为null，从而会调用render方法（即MyComponent中的render方法），该方法返回一个JSX，从而又会调用createElement方法，此时是一个真实的DOM节点，所以会初始化一个ElementWrapper对象，该对象包含一个root属性，这时root就不为null了，而是一个div

# 为toy-react添加生命周期

在实现了ToyReact的自定义组件的jsx语法后，我们的ToyReact可以跑起来啦

✿✿ヽ(°▽°)ノ✿

但是，ToyReact不能自动更新，只是一个空壳子

(⊙︿⊙)

现在就让我们实现dom的更新，以及setState的支持吧~

## 1. state的实现

在react中，自定义组件拥有自己的state，所以我们可以理解为，`Component`是不需要有自己的state，但需要设置setState方法，已使自定义组件调用setState

```jsx
class MyComponent extends Component {
	constructor () {
    	// 执行Component的构造函数
    	super()
        this.state = {
        	a: 1,
            b: 2
        }
    }
    
    render () {
    	return <div>
     		<h1>MyComponent</h1>
            <span>{this.state.a.toString()}</span>
            {this.children}
        </div>
    
    }
}
```

## 2. ToyReact dom更新

之前的render函数是基于root实现的，是一个“取得自定义组件的root -> 取得root中自定义组件的root -> 递归直至TextWrapper或者ElementWrapper(包含真正的root)”

要更新dom，我们需要锁定节点的位置。在react中，因为采用了虚拟dom，所以更新dom会十分精巧。而目前ToyReact采用的是实际dom，所以**更新dom意味着重新渲染整个`this.root`**，但是我们依旧可以通过`Range`API实现dom的定位

我们先来了解一下一个不太常用的api `range`：[ Range的MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Range)

### range的定义和 API的简单使用

MDN是这样定义它的: `Range 接口表示一个包含节点与文本节点的一部分的文档片段`.我认为在此基础上,稍稍修改一下会更好理解。 ` Range 接口能够表示文档中任意节点之间的一部分文档(HTML DOM)片段。`。

```html
<p id="p1"> hello<span> world !</span><span> world !</span></p>
```

- Range.setStart(startNode, startOffset) 设置Range的起点

  接收二个参数第一个参数是节点, 第二个参数是节点的偏移量。比如拿上面的例子来说:

  ```js
  const p1 = document.getElementById('p1')
  range.setStart(p1, 1)
  ```

  range的起始位置应该是

  ```html
              range起始位置
                  |
                  |
                  |  
  <p id="p1">hello <span> world !</span><span> world !</span></p>
  ```

  那么如果`setStart`的第二个参数是0,那么range的起始位置则是:

  ```html
          range起始位置
              |
              |
              |  
  <p id="p1">hello <span> world !</span><span> world !</span></p>
  ```

  其实很容易理解，p1元素节点下面有三个子节点。一个是文本节点hello, 另外两个则是元素节点 `<span> world !</span>`。

- Range.setEnd(startNode, startOffset) 设置Range的结束位置。

  接收二个参数第一个参数是节点, 第二个参数是节点的偏移量。我们还是拿上面的例子来说:

  ```js
  const p1 = document.getElementById('p1')
  range.setEnd(p1, p1.childNodes.length)
  ```
  
  ```html
  													range结束位置
                                                          |
                                                          |
                                                          |  
  <p id="p1">hello <span> world !</span><span> world !</span> </p>
  ```
  
- Range.insertNode(Node) 在Range的起始位置插入节点。

  ```html
  <p id="p1"> hello<span> world !</span><span> world !</span><span> world!</span></p>
  ```

  ````js
  const range = document.createRange()
  const p1 = document.getElementById('p1')
  const element = document.createElement('p')
  element.appendChild(document.createTextNode('123'))
  range.setStart(p1, 0)
  range.setEnd(p1, p1.childNodes.length)
  range.insertNode(element)
  ````

  当执行完`insertNode` 方法后,会在文本节点hello前面添加一个p元素节点。

- Range.deleteContents() 移除来自 Document的Range 内容。

  调用此方法会删除range内的所有节点。

  ```html
  <p id="p1"> hello<span> world !</span><span> world !</span><span> world!</span></p>
  ```

  ````js
   const range = document.createRange()
   const p1 = document.getElementById('p1')
   range.setStart(p1, 0)
   range.setEnd(p1, p1.childNodes.length)
   range.deleteContents()
  ````

以上代码执行后p1节点下面的所有节点都将被删除。

> range的其他api本篇文章中不会涉及,因此就不一一介绍了。

### 2.1 重写Component的get root

我们为什么要用range去重构之前的代码呢？我认为主要是出于以下的考虑:

- 1.使用range我们可以在任意节点处插入DOM
- 2.为接下来的重新渲染与虚拟DOM的比对做铺垫

我们修改的基本思路是:

- 从渲染DOM的地方开始着手, 使用range去完成DOM的实际操作
- 仔细阅读之前的代码, 你会发现它无法进行重新渲染。因此我们需要定义一个私有的方法能够让DOM树重新render。

为了让渲染DOM树的方法, 变得不那么容易让外部调用, 我们使用`Symbol` 返回的唯一标识符作为函数名。

```js
// 通过symbol定义方法名称，保证私有性
const RENDER_TO_DOM = Symbol("render to dom")

export class Component {
    constructor () {
        this.props = Object.create(null)
        this.children = []
        this._root = null
        this._range = null
    }
    
    setAttribute (name, value) {
        this.props[name] = value
    }
    
    appendChild (component) {
        this.children.push(component)
    }
    
    // 使用[]将Symbol作为函数名
    // 传入的是range
    [RENDER_TO_DOM] (range) {
    	// 组件的this.render()会返回组件，之后再调用组件的[RENDER_TO_DOM]方法
    	this.render()[RENDER_TO_DOM](range)
    }
    // 之前的get root 方法被[RENDER_TO_DOM]替代
}
```

我们在Component类中添加一个私有的方法, 因为this.render()返回的值有可能是一个Component, ElementWrapper, TextWrapper。因此在其余二个类中, 我们 也需要去添加`RENDER_TO_DOM` 方法。

### 2.2 重写TextWrapper和ElementWrapper

在更新了Component的`[RENDER_TO_DOM]`方法之后，需要在TextWrapper和ElementWrapper中增加对应的`[RENDER_TO_DOM]`方法，将实DOM渲染到页面。因此在`RENDER_TO_DOM` 中我们需要往range中插入节点。

```js
class ElementWrapper {
    constructor (type) {
        this.root = document.createElement(type)
    }
    
    setAttribute (name, value) {
        this.root.setAttribute(name, value)
    }
    
    // 增加[RENDER_TO_DOM]方法
    [RENDER_TO_DOM] (range) {
    	// 首先从文档中移除 Range 包含的内容。
    	range.deleteContents()
        // 再将root插入range，完成渲染
        range.insertNode(this.root)
    }
    
    // 由于采用了range，所以增加child也要修改
    appendChild (component) {
    	let range = document.createRange()
    	// 将新增的元素置于range末尾
    	range.setStart(this.root, this.root.childNodes.length)
    	range.setEnd(this.root, this.root.childNodes.length)
    	component[RENDER_TO_DOM](range)
    }
}
// 文本节点不需要设置属性及添加子元素
class TextWrapper {
    constructor (content) {
        this.root = document.createTextNode(content)
    }
    
    // 增加[RENDER_TO_DOM]方法
    [RENDER_TO_DOM] (range) {
    	// 首先从文档中移除 Range 包含的内容。
    	range.deleteContents()
        // 再将root插入range，完成渲染
        range.insertNode(this.root)
    }
}
```

### 2.3 更新render函数

由于我们不再使用`get root()` 方法来获取实DOM, 因此我们通过调用 `RENDER_TO_DOM` 来插入节点。

```js
export class ToyReact {
    static render (component, parentElement) {
        // 在parentElement尾部增加range
        let range = document.createRange()

        // 将range的start节点设置为parentElement，offset为0，说明range将包含parentElement的全部children
        range.setStart(parentElement, 0)

        // 因为parentElement中会有文本节点和注释节点，所以offset不是parentElement.children.length
        range.setEnd(parentElement, parentElement.childNodes.length)

        // 清空range
        range.deleteContents()

        // 调用[RENDER_TO_DOM]方法
        component[RENDER_TO_DOM](range)
    }
}
```

### 2.4 支持重新绘制dom

需要修改`[RENDER_TO_DOM]`方法

```js
class ElementWrapper {
    constructor (type) {
        this.root = document.createElement(type)
    }
    
    setAttribute (name, value) {
        this.root.setAttribute(name, value)
    }
    
    // 支持dom重绘
    [RENDER_TO_DOM] (range) {
    	range.deleteContents()
        range.insertNode(this.root)
    }
    
    // 由于采用了range，所以增加child也要修改
    appendChild (component) {
    	let range = document.createRange()
    	// 将新增的元素置于range末尾
    	range.setStart(this.root, this.root.childNodes.length)
    	range.setEnd(this.root, this.root.childNodes.length)
    	component[RENDER_TO_DOM](range)
    }
}

// 文本节点不需要设置属性及添加子元素
class TextWrapper {
    constructor (content) {
        this.root = document.createTextNode(content)
    }
    
    // 增加[RENDER_TO_DOM]方法
    [RENDER_TO_DOM] (range) {
    	range.deleteContents()
        range.insertNode(this.root)
    }
}

export class Component {
    constructor () {
        this.props = Object.create(null)
        this.children = []
        this._root = null
        // 初始化_range
        this._range = null      
    }
    
    setAttribute (name, value) {
        this.props[name] = value
    }
    
    appendChild (component) {
        this.children.push(component)
    }
    
    [RENDER_TO_DOM] (range) {
    	this._range = range
    	this.render()[RENDER_TO_DOM](range)
    }
    
    // 定义重绘方法
    rerender () {
    	this._range.deleteContents()
        this[RENDER_TO_DOM](this._range)
    }
}
```

这样，我们就可以在自定义组件中调用重绘方法，支持setState的操作了。我们需要有一个主动的行为去更新页面。 我们在页面添加一个计数器, 每点击一次按钮, 页面上的数字加一.

```jsx
class MyComponent extends Component {
	constructor () {
    	// 执行Component的构造函数
    	super()
        this.state = {
        	a: 1,
            b: 2
        }
    }
    
    render () {
    	return <div>
            <h1>MyComponent</h1>
            <button onClick={() => {
                this.state.a++
                this.rerender()
            }}></button>
            <span>{this.state.a.toString()}</span>
            <span>{this.state.b.toString()}</span>
        </div>
    }
}
```

### 2.5 支持自定义事件事件绑定

我们点击onClick页面似乎没有反应。其实这边有二个很重要的点没有处理:

- 我们需要处理类似`onClick` 等事件
- 我们需要将改变后count的值重新渲染到页面上

首先只有元素节点上才能绑定事件, 因此我们肯定是在`ElementWrapper`类中进行修改。我们写一个简单的正则来匹配所有on开头的事件, 比如onClick, onHover, onMouseUp.....。

```js
class ElementWrapper {
    constructor (type) {
        this.root = document.createElement(type)
    }
    // 支持事件绑定
    setAttribute (name, value) {
    	// 采用正则，判断name是否为on开头
        if (name.match(/^on([\s\S]+)/) {
        	// [\s\S] 表示全部字符 \s为非空白，\S为空白，两个集合互补
            // 由于此处采用match，所以RegExp.$1将拿到匹配的字符，即on之后的部分
            // RegExp.$1.replace(/^[\s\S]/, c => c.toLowerCase())
            // 确保事件名小写，将第一个字母转换为小写
            this.root.addEventListener(RegExp.$1.replace(/^[\s\S]/, c => c.toLowerCase()), value)
        } else {
        	// 其他属性，直接调用root的setAttribute方法
        	this.root.setAttribute(name, value)
        }
    }
    
    [RENDER_TO_DOM] (range) {
    	range.deleteContents()
        range.insertNode(this.root)
    }
    
    appendChild (component) {
    	let range = document.createRange()
    	range.setStart(this.root, this.root.childNodes.length)
    	range.setEnd(this.root, this.root.childNodes.length)
    	component[RENDER_TO_DOM](range)
    }
}
```

## 3. 实现setState

目前，我们的state可以实现更新，但是并不能实现state原有状态的存储，只是单纯的覆盖，所以我们要完善setState，实现state的深拷贝，更新setState方法主要是将新的state与老的state比较, 然后进行一个深拷贝的操作。如果this.state不存在或者类型不是对象的时候, 我们直接使用新的state去替换它。 然后通过递归将新的state中的值直接赋值到旧的对应的state值。

```jsx
// Component的setState方法
setState (newState) {
    // state为null时的处理
    if (this.state === null || typeof this.state !== "object") {
        // 如果state为null或不是对象，直接为state赋值newState，并重新渲染组件
        this.state = newState
        this.rerender()
        return
    }

    // 采用递归的方式访问state
    let merge = (oldState, newState) => {
        for (let key in newState) {
            if (oldState[key] === null || typeof oldState[key] !== 'object') {
                oldState[key] = newState[key]
            } else {
                // 如果oldSate的p属性为对象，那么就递归调用merge，实现深拷贝
                merge(oldState[key], newState[key])
            }
        }
    }
    merge(this.state, newState)
    this.rerender()
}
class MyComponent extends Component {
    constructor () {
        // 执行Component的构造函数
        super()
        this.state = {
            a: 1,
            b: 2
        }
    }

    render () {
        return <div>
            <h1>MyComponent</h1>
            <button onClick={() => {
                    this.setState({a: this.state.a + 1});
                }}></button>
            <span>{this.state.a.toString()}</span>
            <span>{this.state.b.toString()}</span>
        </div>
    }
}
```

此时的main.js全部内容正常通过编译和运行

```jsx
import { Component, ToyReact } from './toy-react.js'

class MyComponent extends Component{
    constructor() {
        // 执行Component的构造函数
        super();
        this.state = {
            a: 1,
            b: 2
        }
    }
    render() {
        return <div>
            <h1>my component</h1>
            <button onClick={() => {
                    this.setState({a: this.state.a + 1});
                }}>add</button>
            <span>{this.state.a.toString()}</span>
            <span>{this.state.b.toString()}</span>
            {this.children}
        </div>
    }
}

ToyReact.render(<MyComponent id="a" class="c">
           <div>abc</div>
           <div></div>
           <div></div>
       </MyComponent>, document.body)
```

## 4. 集成React官网示例TicTacToe demo

[TicTacToe是react的官方教程](https://codepen.io/gaearon/pen/VbbVLg)，我们拿现在toyReact试着跑一下中间的一个过渡例子，顺便对ToyReact进行一些小修小补吧~

🏃🏃🏃

不负众望✿✿ヽ(°▽°)ノ✿没有运行起来

调试后发现，主要问题存在于 createElement函数的insertChildren操作，没有兼顾到children为null的状况，而TicTacToe demo采用了children为null的设置

我们来修复这个问题

### 4.1 insertChildren支持child为null

```js
export class ToyReact {
    static createElement (tagType, attributes, ...children) {
        ... ...

        let insertChildren = (children) => {
            for (let child of children) {

                // 如果child为null，不做任何处理
                if (child === null) {
                    continue
                }

                if (typeof child === "string") {
                    child = new TextWrapper(child)
                }

                if (typeof child === "object" && child instanceof Array) {
                    insertChildren(child)
                } else {
                    element.appendChild(child)
                }

            }
        }

        insertChildren(children)

        return element
    }
}
```

需要注意的是，demo中的采用了函数组件，我们需要修改为class组件。另外，我们的toyReact目前还没有支持className的绑定

我们需要在ElementWrapper的setAttribute方法中，实现className的绑定

### 4.2 className的绑定

修改ElementWrapper类支持className。我们需要单独处理`className`这个属性, 因为元素节点的类名是通过赋值到class上才能生效

```js
class ElementWrapper {
    constructor (type) {
        this.root = document.createElement(type)
    }
    // 采用正则，判断name是否为on开头
    if (name.match(/^on([\s\S]+)/)) {
        // [\s\S] 表示全部字符 \s为非空白，\S为空白，两个集合互补
        // 由于此处采用match，所以RegExp.$1将拿到匹配的字符，即on之后的部分
        // RegExp.$1.replace(/^[\s\S]/, c => c.toLowerCase())
        // 确保事件名小写，将第一个字母转换为小写
        this.root.addEventListener(RegExp.$1.replace(/^[\s\S]/, c => c.toLowerCase()), value)
    } else if (name === 'className') { // 配置属性
        this.root.setAttribute('class', value)
    } else {
        this.root.setAttribute(name, value)
    }
   
    appendChild (component) {
        this.root.appendChild(component.root)
    }
}
```

### 4.3 修复由于range被吞导致的问题

我们通过range API实现了组件的重新渲染，但是我们使用`range.deleteContents()`的时机，导致TicTacToe demo在重新渲染时，存在丢失dom的情况，所以需要修复

问题存在于Component的`rerender`方法，函数执行时，会先清空range，这会导致空range被相邻的range“吞了”，所以在`rerender`执行时，需要保证range不空。

所以，我们需要先插入range，再删除它

```js
export class Component {
    rerender () {
        let range = document.createRange()
        // 新创建的range没有宽度
        range.setStart(this._range.startContainer, this._range.startOffset)
        range.setEnd(this._range.startContainer, this._range.startOffset)
        this[RENDER_TO_DOM](range)
        this._range.deleteContents()
    }
}
```

但是在Component`[RENDER_TO_DOM]`方法中，我们保留了`this._range`，所以不能直接在`rerender`时直接清空

```js
export class Component {
    // [RENDER_TO_DOM]方法保留了this._range 
    [RENDER_TO_DOM] (range) {
        this._range = range
        this.render()[RENDER_TO_DOM](range)
    }
}
```

所以我们需要改进一下

```js
export class Component {
    rerender () {
        // 保存this._range
        let oldRange = this._range

        // 新创建的range没有宽度，但会改变oldRange的宽度
        // 新创建的range在this._range的start处
        let range = document.createRange()
        range.setStart(oldRange.startContainer, oldRange.startOffset)
        range.setEnd(oldRange.startContainer, oldRange.startOffset)

        this[RENDER_TO_DOM](range)

        // 重设oldRange的start节点，跳过插入的range
        oldRange.setStart(range.endContainer, range.endOffset)
        // 清除oldRange的内容
        oldRange.deleteContents()
    }

    // [RENDER_TO_DOM]方法保留了this._range 
    [RENDER_TO_DOM] (range) {
        this._range = range
        this.render()[RENDER_TO_DOM](range)
    }
}
```

现在我们用[TicTacToe的最终结果](https://codepen.io/gaearon/pen/gWWZgR)验证写好的toyReact，这里我用了之前我自学时写好的，当然要注意函数式组件当前不可用，需要改为class声明。

```jsx
import { Component, ToyReact } from './toy-react.js'

// Square 组件渲染了一个单独的 <button>
class Square extends Component {
    render() {
        return (
            // 接收父组件参数
            <button className="square" onClick={() => this.props.onClick()}>
                {/* 使用父组件参数 */}
                {this.props.value}
            </button>
        )
    }
}

// Board 组件渲染了 9 个方块
class Board extends Component {
    renderSquare(i) {
        // return <Square />;
        // 增加参数，修改一下 Square 的点击事件监听函数
        return (
            <Square
                // 从 Game 组件中接收 squares 和 onClick 这两个 props。
                value={this.props.squares[i]}
                onClick={() => this.props.onClick(i)}
                />
        )
    }

    render() {
        return (
            <div>
                <div className="board-row">
                    {this.renderSquare(0)}
                    {this.renderSquare(1)}
                    {this.renderSquare(2)}
                </div>
                <div className="board-row">
                    {this.renderSquare(3)}
                    {this.renderSquare(4)}
                    {this.renderSquare(5)}
                </div>
                <div className="board-row">
                    {this.renderSquare(6)}
                    {this.renderSquare(7)}
                    {this.renderSquare(8)}
                </div>
            </div>
        )
    }
}

// Game 组件渲染了含有默认值的一个棋盘
class Game extends Component {
    // 点击事件
    handleClick(i) {
        // 历史,丢弃stepNumber后的数据
        const history = this.state.history.slice(0, this.state.stepNumber + 1)
        // 当前状态
        const current = history[history.length - 1]
        const squares = current.squares.slice()
        // 当有玩家胜出时，或者某个 Square 已经被填充时，该函数不做任何处理直接返回
        if (calculateWinner(squares) || squares[i]) return
        // 根据xIsNext判断
        squares[i] = this.state.xIsNext ? "X" : "O"
        this.setState({
            history: history.concat([
                {
                    squares: squares,
                },
            ]),
            // squares: squares,
            // 更新时间步
            stepNumber: history.length,
            // 翻转xIsNext
            xIsNext: !this.state.xIsNext,
        })
    }

    // 更新状态 stepNumber
    jumpTo(step) {
        this.setState({
            stepNumber: step,
            xIsNext: step % 2 === 0,
        })
    }

    // 为 Game 组件添加构造函数，保存历史步骤列表
    constructor(props) {
        super(props)
        this.state = {
            history: [
                {
                    squares: Array(9).fill(null),
                },
            ],
            // 步数
            stepNumber: 0,
            // 将 “X” 默认设置为先手棋
            xIsNext: true,
        }
    }

    render() {
        // 状态变化，从子组件提升到父组件
        // 历史
        const history = this.state.history
        // 当前状态，将代码从始终根据最后一次移动渲染修改为根据当前 stepNumber 渲染
        const current = history[this.state.stepNumber]
        // 计算胜者
        const winner = calculateWinner(current.squares)
        // 历史步骤映射为代表按钮的 React 元素，然后可以展示出一个按钮的列表，点击这些按钮，可以“跳转”到对应的历史步骤。
        const moves = history.map((step, move) => {
            const desc = move ? "跳转至第" + move + "步" : "游戏重新开始"
            return (
                // 组件的 key 值并不需要在全局都保证唯一，只需要在当前的同一级元素之前保证唯一即可
                <li key={move}>
                    <button onClick={() => this.jumpTo(move)}>{desc}</button>
                </li>
            )
        })
        // 状态
        let status
        // 判断是否获胜
        if (winner) {
            status = "胜者：" + winner
        } else {
            status = "下一步: " + (this.state.xIsNext ? "X" : "O")
        }

        return (
            <div className="game">
                <div className="game-board">
                    <Board
                        // 绑定参数和事件
                        squares={current.squares}
                        onClick={(i) => this.handleClick(i)}
                        />
                </div>
                <div className="game-info">
                    <div>{status}</div>
                    <ol>{moves}</ol>
                </div>
            </div>
        )
    }
}

ToyReact.render(<Game />, document.getElementById("root"))

// 判断胜者
function calculateWinner(squares) {
    // 获胜的序号
    const lines = [
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
        [0, 3, 6],
        [1, 4, 7],
        [2, 5, 8],
        [0, 4, 8],
        [2, 4, 6],
    ]
    for (let i = 0; i < lines.length; i++) {
        const [a, b, c] = lines[i]
        // 有人获胜
        if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
            return squares[a]
        }
    }
    return null
}
```

还有样式，`main.html`中导入`main.css`

```html
<!DOCTYPE html>
<html lang="en">
    <link rel="stylesheet" href="main.css" type="text/css" />
    <head>
        <meta charset="utf-8" />
        <link rel="icon" href="favicon.ico" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <title>Tic tac toe</title>
    </head>
    <body>
        <div id="root"></div>
    </body>
    <script src="main.js"></script>
</html>
```

`main.css`中的样式

```css
body {
    font: 14px "Century Gothic", Futura, sans-serif;
    margin: 20px;
}

ol,
ul {
    padding-left: 30px;
}

.board-row:after {
    clear: both;
    content: "";
    display: table;
}

.status {
    margin-bottom: 10px;
}

.square {
    background: #fff;
    border: 1px solid #999;
    float: left;
    font-size: 24px;
    font-weight: bold;
    line-height: 34px;
    height: 34px;
    margin-right: -1px;
    margin-top: -1px;
    padding: 0;
    text-align: center;
    width: 34px;
}

.square:focus {
    outline: none;
}

.kbd-navigation .square:focus {
    background: #ddd;
}

.game {
    display: flex;
    flex-direction: row;
}

.game-info {
    margin-left: 20px;
}
```

# 虚拟DOM的原理和关键实现

现在，我们拥有了基于实体dom的ToyReact，可喜可贺

✿✿ヽ(°▽°)ノ✿

但是，react的核心是虚拟dom，毕竟有了虚拟dom，我们的组件刷新才更高效嘛

那么，我们来实现这个功能吧

## 1. 实现虚拟dom树的创建及渲染

要支持虚拟dom，我们需要重构ElementWrapper、TextWrapper以及Component，毕竟它们通过root这一实际dom进行渲染和更新。

### 什么是虚拟DOM

虚拟DOM本质上其实是对真实DOM的一种映射关系。它是一种以对象的形态来表示真实存在的DOM。举个例子:

```html
<ul id="ul1">
    <li name="1">world !</li>
    <li name="2">world !</li>
    <li name="3">world !</li>
</ul>
```

上面的html代码如果以虚拟DOM的形态来表示的话, 那么就是:

```js
{
    type: 'ul',
  	props: {      
    	id: 'ul1'
 	},
	children: [
		{ type: 'li', props: {name: '1'}, children: ['world !']},
        { type: 'li', props: {name: '2'}, children: ['world !']},
        { type: 'li', props: {name: '3'}, children: ['world !']},
	]
}
```

对于元素节点来说, 虚拟DOM应该包含三样东西:

- 节点的类型(比如div, span, p)
- 节点上的props
- 节点的children

然而对于文本节点来说, 它的类型是固定的, 唯一不同的就是他的内容了, 因此它的虚拟DOM就比较简单了

- 节点的类型(text)
- 节点的内容(content)

那么对应到ToyReact中的代码片段应该是`ElementWrapper` 和 `TextWrapper` 这两个类。

### 1.1 ElementWrapper的vdom实现

在ElementWrapper中我们增加`vdom`的get

```js
get vdom () {
	return {
    	type: this.type,
        props: this.props,
        // 拿到每个child的vdom
        children: this.children.map(child => child.vdom)
    }
}
```

在setAtrribute方法中，我们存储了`this.props`，而在appendChild中，我们存储了`this.children`。这两种方法的逻辑，和Component有重合，**所以ElementWrapper和TextWrapper可以继承Component**

```js
class ElementWrapper extends Component {
    constructor(type) {
        super(type)
        this.type = type
        // 创建根元素
        this.root = document.createElement(type)
    }
}
```

和

```js
class TextWrapper extends Component{
    constructor(content) {
        super(content)
        this.content = content
        this.root = document.createTextNode(content)
    }
}
```

### 1.2 TextWrapper的vdom实现

```js
get vdom () {
	return {
    	type: "#text",
        content: this.content
    }
}
```

### 1.3 Component的vdom实现

```js
get vdom () {
	return this.render().vdom
}
```

### 1.4 vdom到实体dom的patch

我们之前在ElementWrapper中设置的setAtrribute和appendChild方法，实际创建了需要渲染的dom，而实现vdom到实体dom的patch的过程，这两个方法可以删除，其逻辑可以合并到`[RENDER_TO_DOM]`方法中。 因为基于vdom，所以保存实际dom的`this.root`属性可以删去，实际dom只在`[RENDER_TO_DOM]`方法中存在

在删除setAtrribute和appendChild方法之后，**因为 get vdom 的操作只返回了对象，并没有返回方法，无法重绘**，所以ElementWrapper和TextWrapper的get vdom的正解应是：

```js
// ElementWrapper
get vdom () {
	this.vchildren = this.children.map(child => child.vdom)
	return this
}

// TextWrapper
constructor(content) {
    super(content);
    this.type = '#text';
    this.content = content;
    this.root = document.createTextNode(content)
}
get vdom () {
	return this
}
```

那么，vdom的对象属性，就是ElementWrapper和TextWrapper中的属性。个人理解，这里将数据和行为进一步解耦。

#### 1.4.1  [RENDER_TO_DOM]方法中patch的实现

我们接下来先将注释掉的二个方法重新补上, 因为`setAttribute` 和 `appendChild`方法都是对实DOM的操作, 所以我打算把这两个 函数的实现全部放到 `RENDER_TO_DOM`函数中。

```js
// ElementWrapper
[RENDER_TO_DOM] (range) {
	range.deleteContents()
    
	// 创建实体dom，root
    let root = document.createElement(this.type)
    
    // props内容抄写，setAttribute逻辑的实现
    for (let name in this.props) {
    
    	let value = this.props[name]
        
    	if (name.match(/^on([\s\S]+)/)){
            root.addEventListener(RegExp.$1.replace(/^[\s\S]/, c => c.toLowerCase()), value)
        } else{
            if (name === "className"){
                root.setAttribute("class", value)
            } else {
                root.setAttribute(name, value)
            }
        }
    }
    
    // children的处理
     for (let child of this.children) {
     	
        let childRange = document.createRange()
        // 将新增的元素置于range末尾
        childRange.setStart(root, root.childNodes.length)
        childRange.setEnd(root, root.childNodes.length)
        child[RENDER_TO_DOM](childRange)
     }
    
    // 挂载 root
    range.insertNode(root)
}
```

### 1.5 修复bug

现在，我们的vdom树已经可以建起来啦~

不过还是留了一些尾巴，特别在于vchildren的处理上，这部分还需要优化，需要去掉Component的vchildren getter

对于ElementWrapper和TextWrapper，我们需要在在[RENDER_TO_DOM]方法中保存之前的range

```js
// TextWrapper
[RENDER_TO_DOM] (range) {
	this._range = range
    let root = document.createTextNode(this.content)
    range.deleteContents()
    range.insertNode(root)
}
```

由于ElementWrapper和TextWrapper的[RENDER_TO_DOM]依旧采用先删除range，再渲染this.root的方式，会导致空range被吞，所以我们要优化[RENDER_TO_DOM]。而[RENDER_TO_DOM]的逻辑在不同位置都有使用，所以我们单独封装一个`repalceContent`方法

```js
function replaceContent (range, node) {

	// 将node插入range，此时node在range的最前位置
	range.insertNode(node)
    
    // range挪到node之后
    range.setStartAfter(node)
    // 清空range
    range.deleteContents()
    
    // 重设range的位置
    range.setStartBefore(node)
    range.setEndAfter(node)
}
```

现在我们来改进[RENDER_TO_DOM]中range的处理

第一部分的for循环其实做的就是 `setAttribute` 的事情, 将属性赋值到元素上, 第二部分的for循环做的事情则是通过递归的方式插入child.

那么如何将虚拟DOM与实DOM结合一起呢。其实很简单, 我们通过遍历虚拟的children, 来构造一颗虚拟的树。最终将这棵虚拟的树转化为实际的树。 那么对应到代码上应该如何修改呢？ 我们还是从 Component中的 `RENDER_TO_DOM`主体函数上着手, 我们仔细研读以下代码, 我们发现这边的children 还是实际的children, 并不是虚拟的children, 因此我们需要在Component类中定义一个vchildren。

```js
get vchildren() {
    return this.children.map(child => child.vdom)
}
```

同理我们遍历的时候就用vchildren进行遍历, 这样我们的这颗树就是虚拟的树。

```js
for (const child of this.vchildren) {
    const childRange = document.createRange();
    childRange.setStart(root, root.childNodes.length);
    childRange.setEnd(root, root.childNodes.length);
    child[RENDER_TO_DOM](childRange);
}
```

完整的虚拟dom这块实现的代码如下：

```js
// TextWrapper
[RENDER_TO_DOM] (range) {
	let root = document.createTextNode(this.content)
	this._range = range
    replaceContent(range, root)
}

// ElementWrapper
get vdom () {
    this.vchildren = this.children.map(child => child.vdom)
	return this
}
[RENDER_TO_DOM] (range) {
	this._range = range
	// 通过replaceContent代替初始时range.deleteContents()
	// range.deleteContents()
	
    let root = document.createElement(this.type)
    
    for (let name in this.props) {
    	let value = this.props[name]
    	if (name.match(/^on([\s\S]+)/)){
            root.addEventListener(RegExp.$1.replace(/^[\s\S]/, c => c.toLowerCase()), value)
        } else{
            if (name === "className"){
                root.setAttribute("class", value)
            } else {
                root.setAttribute(name, value)
            }
        }
    }
    
     for (let child of this.vchildren) {
        let childRange = document.createRange()
        childRange.setStart(root, root.childNodes.length)
        childRange.setEnd(root, root.childNodes.length)
        child[RENDER_TO_DOM](childRange)
     }
    
    // 完成root的挂载
    replaceContent(range, root)
}
```

## 2. vdom比对的实现

在拥有了vdom树的创建能力之后，我们的`rerender`函数可以退休了，代替它的是vdom更新，在Component基类中实现

在Component的[RENDER_TO_DOM]方法中，我们需要先更新vdom，再渲染

```js
// Component
[RENDER_TO_DOM] (range) {
	// 保存range和vdom
    this._range = range
    // 由于this.vdom是getter，所以会重新调用组件的render方法，返回新的vdom，实现vdom更新
    this._vdom = this.vdom
    // 渲染旧的vdom
    this._vdom[RENDER_TO_DOM](range)
    
}
```

### 什么是Diff算法

Diff算法其实是通过比对新旧虚拟DOM树,然后将不同的部分渲染到页面中,从而达到最小化更新DOM的目的。

以下图DOM为例:

![DOM](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220407083210.png)

Diff算法采用按照深度遍历规则遍历的, 因此遍历的过程如下:

- 1. 对比节点1(没有变化)
- 1. 对比节点2(没有变化)
- 1. 对比节点4(没有变化)
- 1. 对比节点5`(节点5被移除, 记录一个删除的操作)`
- 1. 对比节点3(没有变化)
- 1. 对比节点3的children`(新增节点5, 记录一个新增操作)`

因此在实际渲染的过程中, 会执行节点5的删除和新增操作, 其余节点不会发生任何变化。

Diff算法在前面我们已经稍微介绍过了, 我们不会跟React一样去实现他的diff算法, 因为这不是本文的重点。本文的重点旨在让大家理解Diff算法是如何贯穿于虚拟DOM的。但是我们会尽可能的多考虑Diff重绘的情况。那么哪几种情况会导致我们的DOM树重绘制呢?

- 节点的类型不同

![节点的类型不同](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220406223503.png)

- 新老节点的props值不同

![新老节点的props值不同](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220407132424.png)

- 新节点的props少于老节点的props

![新节点的props少于老节点的props](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220407133433.png)

- 文本节点的内容不同

![文本节点的内容不同](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220407133532.png)

### 2.1 vdom更新

在Component的unpdate方法中，我们实现vdom的更新。我们需要做的就是遍历我们的虚拟DOM树.首先判断新的虚拟DOM节点与老的虚拟DOM节点 是否一样, 如果一样那么就直接替换`range`, 并且通过递归的方式去比对子节点。 如果节点不一样, 那么就更新当前节点下的range, 从而达到部分更新的效果。

```js
update () {
	 let update = (oldNode, newNode) => {
     // 更新为newNode
     }
    
    // 保存新的vdom
    let vdom = this.vdom
    // 对比vdom
    update(this._vdom, vdom)
    // 重新赋值
    this._vdom = vdom
}
```

那么在实现更新vdom功能前，我们需要对比根节点，细化vdom更新范围。

### 2.2 简单的 dom diff 算法

我们的节点更新逻辑如下：

- 根节点的type是否一致，不一致，则更新为新的根节点
- 根节点的props是否一致，不一致，则更新为新的根节点
- 根节点的children是否一致，不一致，则更新为新的根节点
- 对于文本节点，content不一致，则更新为新节点

我们采用~~最土，最傻瓜~~最简练的方式更新dom

- 只对比相同位置的vdom
- 采用直接替换的方式更新节点

```js
update () {

	let isSameNode = (oldNode, newNode) => {

		// type不同，则为不同节点
		if (oldNode.type !== newNode.type) {
    		return false
    	}
    
    	// props不同，则为不同节点
    	for (let name in newNode.props) {
    		// 属性值要相同
        	if (newNode.props[name] !== oldNode.props[name]) {
        		return false
        	}
    	}
    	// props的长度不相同，节点不相同
    	if (Object.keys(oldNode.props).length > Object.keys(newNode.props)) {
    		return false
    	}
    
    	// 文本节点，比对content
    	if (newNode.type === "#text") {
    		if (newNode.content !== oldNode.content) {
    			return false
    		}
    	}
    
		return true
	}
    
    let update = () => {}
    
    // 保存新的vdom
    let vdom = this.vdom
    // 对比vdom
    update(this._vdom, vdom)
    // 重新赋值
    this._vdom = vdom
}
```

还差最后一步, 每次更新后, 还需要将虚拟DOM树也更新, 为下次Diff做准备。我们定义一个`vdom` 来接收最新的虚拟DOM树, 执行完更新虚拟的函数后, 我们需要将老的虚拟dom树(this._vdom)换成更新完后 的虚拟dom树。至此整个更新流程就完成了, 我们来查看一下最后的效果。

### 2.3 newNode更新

```js
update () {
	let isSameNode = (oldNode, newNode) => {
    	... ...
    }

	let update = (oldNode, newNode) => {
    
    	// 根节点不同，则全部重新渲染
    	if (!isSameNode(oldNode, newNode)) {
        	// 替换oldNode
        	newNode[RENDER_TO_DOM](oldNode._range)
            return
        }
        newNode._range = oldNode._range
        
        // children的处理
        // 因为children属性是实体dom，所以我们要拿到vchildren
        let newChildren = newNode.vchildren
        let oldChildren = oldNode.vchildren
        
        if (!newChildren || !newChildren.length) {
        	return
        }
        
        // 记录oldChildren的尾部位置
        let tailRange = oldChildren[oldChildren.length - 1]._range
        // 两个数组一起循环，所以不用 for of循环
        for (let i = 0; i < newChildren.length; i++) {
        	let newChild = newChildren[i]
            let oldChild = oldChildren[i]
            if (i < oldChildren.length) {
            	update(oldChild, newChild)
            } else {
                // 如果newChild比oldChild元素多，我们需要在newChild进行节点插入
                // 创建一个需要插入的range
                let range = document.createRange()
                range.setStart(tailRange.endContainer, tailRange.endOffset)
                range.setEnd(tailRange.endContainer, tailRange.endOffset)
                newChild[RENDER_TO_DOM](range)
                tailRange = range
            }
        }
    }
    
    let vdom = this.vdom
    // 更新vdom
    update(this._vdom, vdom)
    // 重新赋值, 此时的“旧vdom”是vdom
    this._vdom = vdom
}
```

之后在setState中调用update，即可实现虚拟dom的更新。

至此，塑料版react toyReact就完成啦✿✿ヽ(°▽°)ノ✿

![塑料版ToyReact使用](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220407173725.gif)

我们发现DOM树已经不再是重头绘制了。它的更新范围已经缩小在了 `Board` 类对应的DOM树范围内了。 但是这边有一个小问题，为什么我们不能单个button格子更新呢？我打算用debug的方式来说明这个问题。 首先，在 `isSameNode` 函数上打一个断点，方便我们调试.

```js
let isSameNode = (oldNode, newNode) => {
    debugger

    if(oldNode.type !== newNode.type) {
        return false
    }

    for (const key in newNode.props) {
        if (oldNode.props[key] !== newNode.props[key]) {
            return false
        }
    }

    if(Object.keys(oldNode.props).length >  Object.keys(newNode.props).length)
        return false

    if(newNode.type === "#text") {
        if(newNode.content !== oldNode.content) {
            return false
        }
    }

    return true;
}
```

点击左上方的格子，打开浏览器调试模式，程序即可进入debugger模式。我们直接进入到最后一次比较格子的地方。

![oldNode](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220407174623.png)

老的虚拟DOM的`content` 是空值，按照正常逻辑此时的对应的新的虚拟DOM中的content值应该是一个`x`。我们来校验一下结果。

![newNode](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220407175146.png)

OK，那么我们接下去需要知道的是，它是在比较什么地方的时候，出现了不同，导致重绘的。我们发现在比对新老节点的props的时候，发生了值不一样的情况。而不一样的key，是 `onClick `。

![onClick导致重绘](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220407180207.png)

调试到这里我们应该明白了为什么它重绘的区域与我们想象的不一样了。在比对事件的时候, 我们每次都会实例化一个新的事件函数。这就导致了我们的ToyReact处理不了关于事件调度方面的diff了。那么如果想要达到React那样, 只更新某个节点 这样的效果的话, 我们可以采取最残暴的手法, 直接忽略所有的事件。

```js
for (const key in newNode.props) {
    if (oldNode.props[key] !== newNode.props[key]) {
        if(typeof newNode.props[key] !== 'function') {
            return false
        }
    }
}
```

我们继续看看, 最后的效果如何?

![忽略所有的事件](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220407180613.gif)

It is Cool! 我们也做到了只更新某个节点的效果了。 不过大家乐呵乐呵就行, 学技术还是得看React官方源码, 哈哈。

# 总结

借助webpack，我们实现了ToyReact，一个塑料react🐶

## 1. JSX 语法解析

JSX 是 JavaScript 与 XML 相结合的一种格式。React发明了JSX，实现了利用HTML 语法来创建虚拟DOM。遇到`<`时，JSX将起作为HTML解析，遇到 `{`JSX将其作为JavaScript解析。

**JSX语法并不是直接把JSX渲染到页面**，而是在React内部先将JSX转换成createElement形式，再去渲染的，同时JSX在编译成JavaScript代码的时候进行了一定的优化，保证了React的更高执行效率。

在ToyReact中，我们通过设置webpack的 ** @babel/plugin-transform-react-jsx** 插件，重新定义了jsx生成dom的函数（并没有实现jsx语法本身）

↓↓↓ webpack 配置如下 ↓↓↓

```js
module.exports = {
    module: {
        rules: [
            {
                test: /\.js$/,
                use: {
                    loader: 'babel-loader',
                    // 配置babel-loader
                    options: {
                        presets: ['@babel/preset-env'],
                        // 这样打包后的‘React.createElement就会变成‘createElement’
                        // 我们定义的 createElement 函数将会覆盖 React.createElement 方法
                        plugins: [['@babel/plugin-transform-react-jsx', {pragma: 'ToyReact.createElement'}]]
                    }
                }
            }
        ]
    }
}
```

## 2. ToyReact生命周期

在Component class里有 [RENDER_TO_DOM](https://link.juejin.cn?target=) 和 update() 两个函数，对应React生命周期，就是在**挂载前**需要的操作和**更新**时需要的操作。

在**挂载**之前：通过setAttribute添加自定义的属性，addEventListener添加事件；然后就会执行一次render

如果有**更新**操作，就会在update()内会通过对比对更新的元素进行替换；再次render

### React生命周期函数

- ~~组件将要挂载时触发的函数：componentWillMount~~
- 组件挂载完成时触发的函数：componentDidMount
- 是否要更新数据时触发的函数：shouldComponentUpdate
- ~~将要更新数据时触发的函数：componentWillUpdate~~
- 数据更新完成时触发的函数：componentDidUpdate
- 组件将要销毁时触发的函数：componentWillUnmount
- 父组件中改变了props传值时触发的函数：~~componentWillReceiveProps~~

### React16废弃生命周期

React16废弃了三个生命周期：componentWillMount、componentWillReceiveProps、componentWillUpdate

废弃原因：在React16的Fiber架构中，调和过程会多次执行will周期，不再是一次执行，所以这些周期失去了原有的意义。此外，由于周期会多次执行， 在周期中设置setState或dom操作，会触发多次重绘，影响性能，也会导致数据错乱

componentWillMount和componentWillUpdate在每一个组件render之前都会去调用componentWillMount()，可以在服务端调用也可以在浏览器端调用，**如果有异步请求，不推荐在此时请求数据，因为在render前并不会返回数据**。

componentWillUpdate()在组件将要更新数据的时候都会触发一次，执行更新操作。

### React16新增2个生命周期：

- getDerivedStateFromProps：16.3是在props变化时触发，16.4则改为在每次组件渲染器调用
- getSnapshotBeforeUpdate：在render之后，更新DOM之前，state已更新

**getDerivedStateFromProps**周期有些难用：

1. 触发时机频繁，16.3是在props变化时触发，16.4则改为在每次组件渲染器调用，

即无论props变化，组件自己setState，父组件render 都会触发 2. 静态方法，本意是隔离访问组件实例，却会造成访问组件的数据和方法十分不便，难以进行数据比较 3. 不能setState，而是返回一个对象来更新state，使用不便，也可能触发多次更新状态

**getSnapshotBeforeUpdate**周期在Fiber架构中，只会调用一次，实现了类似willMount的效果。可以用来读取DOM，强制用户只能在mount阶段读取DOM。

## 3. ToyReact虚拟DOM

React将DOM抽象为虚拟DOM，用JavaScript模拟一颗DOM树，放在浏览器内存中。当变更时，虚拟DOM使用DIFF算法进行新旧虚拟DOM的比较，将变更放到变更队列中，最终只把变化的部分重新渲染，从而提高渲染效率。

在需要**频繁微改动DOM时**，直接修改DOM会引起页面的多次渲染，影响性能。而使用虚拟DOM的时候，先对比差异，再修改JS对象(生成虚拟DOM)，最后把生成的DOM结构插入到页面中，从而减少渲染次数，提升整个页面的渲染效率。

### Range

ToyReact的虚拟DOM实现，借助了Range对象

Range对象表示文档的连续范围区域，相当于**高亮选区**。一个Range的开始点和结束点可以是任意的，开始点和结束点也可以是一样的(空Range)

Range的应用常见于富文本编辑器的相关操作

使用Range时，首先会创建一个range对象(createRange)，将指定节点的终点位置指定为Range对象所代表区域的起点位置(setStartAfter)；紧接着将指定的节点插入到某个Range对象所代表的区域中，插入位置为Range对象所代表区域的起点位置，如果该节点已经存在于页面中，该节点将被移动到Range对象代表的区域的起点处(insertNode)。

Range对象API官网：

[developer.mozilla.org/en-US/docs/…](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FAPI%2FRange%EF%BC%9B)

Range的使用博客：

[laixiazheteng.com/article/pag…](https://link.juejin.cn?target=https%3A%2F%2Flaixiazheteng.com%2Farticle%2Fpage%2Fid%2FuMXiMenCofsB)

[blog.csdn.net/qq_21119773…](https://link.juejin.cn?target=https%3A%2F%2Fblog.csdn.net%2Fqq_21119773%2Farticle%2Fdetails%2F51627382)

