---
title:  bigfrontend的TS题目讨论
date: 2022-09-09 22:00:00
categories: 
- web前端
tags:
- bigfrontend
- 前端
- 题库
---

国外前端会考察哪些问题，其实CSS才是我的最大弱点，我的感觉是吃力不讨好。
<!-- more -->

# [CSS](https://bigfrontend.dev/typescript)

## [1. center an element vertically](https://bigfrontend.dev/css/center-an-element-vertically)

### 题目

This is a very basic question and good to be the first CSS problem on BFE.dev.

Suppose you have an HTML structure as below

```html
<div class="outer">
  <div class="inner">
</div>
```

Please center the inner div vertically without changing their dimensions and colors.

#### 1.Should be centered vertically

![](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220909220750.png)

#### 2.even if container size changes

![](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220909220758.png)

### 答案

```css
/* // position relative and absolute 
// transform: translate so it moves back 50% of its height and width
.outer {
  width: 100%;
  height: 100%;
  background-color: #efefef;
  position: relative;
}

.inner {
  width: 100px;
  height: 100px;
  background-color: #f44336;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
} */


/* // display flex
// transform: translate so it moves back 50% of its height and width 
.outer {
  width: 100%;
  height: 100%;
  background-color: #efefef;
  display: flex;
  justify-content: center;
  align-items: center
}

.inner {
  width: 100px;
  height: 100px;
  background-color: #f44336;
} 
*/

/* // display flex
// transform: translate so it moves back 50% of its height and width */
.outer {
  width: 100%;
  height: 100%;
  background-color: #efefef;
  display: flex;
  align-items: center;
  justify-content: center;
}

.inner {
  width: 100px;
  height: 100px;
  background-color: #f44336;
}
```



## [2. truncate text in one line(with ellipsis)](https://bigfrontend.dev/css/truncate-text-with-ellipsis-in-one-line)

### 题目

Truncate text in one line, if needed add ellipsis.

#### 1.When no overflow

![](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220909222124.png)

#### 2.when there is need to truncate, add ellipsis

![](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220909222151.png)

### 答案

See : https://www.w3schools.com/cssref/css3_pr_text-overflow.asp

```css
.one-line {
  /* 设置文本强制在一行显示 */
  white-space: nowrap;
  /* 容器溢出隐藏 */
  overflow: hidden;
  /* 文本溢出显示省略号 */
  text-overflow: ellipsis;
}
```



## [3. truncate text in multiple lines(with ellipsis)](https://bigfrontend.dev/css/truncate-text-in-multiple-lines-with-ellipsis)

### 题目

Just like [2. truncate text in one line(with ellipsis)](https://bigfrontend.dev/css/truncate-text-with-ellipsis-in-one-line), please do the same for max 3 lines.

#### 1.short text(1 line)

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910163603.png)

#### 2.short text (2 lines)

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910163627.png)

#### 3. lines(no overflow)

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910163713.png)

#### 4. more than 3 lines

![4](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910164550.png)

### 答案

https://css-tricks.com/almanac/properties/l/line-clamp/

```css
/* 1. webkit-line-clamp will allow you to clamp text to 3 lines,
   2. It must be used in combination with either display:-webkit-box or display-webkit-inline-box and  
 webkit-box-orient set to vertical
   3. In most cases you'll also need overflow set to hidden because otherwise remaining text will be visible
   More: https://developer.mozilla.org/en-US/docs/Web/CSS/-webkit-line-clamp
     */
.max-three-lines {
  /* 适用范围：Webkit浏览器及移动端*/
  text-overflow: ellipsis;
  display: -webkit-box;
  /* 限制块元素中文本的显示行数，这里是3行 */
  -webkit-line-clamp: 3;
  /* 指定子元素按垂直方向排列 */
  -webkit-box-orient: vertical;
  overflow: hidden;
}
```



## [4. two-column layout](https://bigfrontend.dev/css/two-column-layout)

### 题目

Implement a basic two-column layout, in which left column takes up 25% width of the container with minimum width of 100px and right column fills up the rest.

#### 1.400x300

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910170029.png)

#### 2.600x300

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910170037.png)

#### 3.300x300

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910170043.png)

### 答案

flex布局

```css
.container {
  height: 300px;
  display: flex;
}

.left {
  background-color: #f44336;
  width: 25%;
  min-width: 100px;
}

.right {
  background-color: #2973af;
  /* your code here */
  width: 75%;
}
```

grid布局

```css
.container {
  height: 300px;
  display: grid;
  grid-template-columns: minmax(100px, 25%) auto;
}

.left {
  background-color: #f44336;
}

.right {
  background-color: #2973af;
}
```



## [5. modal with max height](https://bigfrontend.dev/css/modal-with-max-height)

### 题目

Let's create a modal

1. it has header(fixed height: 50px) and stretching body
2. width of 300px and total max height 300px
3. centered in viewport
4. need mininum space of 30px to viewport top and bottom.

The HTML structure is something like

```html
<div class="modals">
  <div class="modal">
    <div class="modal-header"></div>
    <div class="modal-body"></div>
  </div>
</div>
```

#### 1.400x150

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910170519.png)

#### 2.400x300

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910170536.png)

#### 3.400x400

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910170720.png)

#### 4.400x600

![4](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910171201.png)

### 答案

flex布局

Most discussions here are not providing context; so hopefully this will help beginners (like myself) to learn. Feel free to ask any questions

```css
.modals {
  /* req 3: modal is centered in viewport */
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  /* req 4: min 30px space to viewport top & bottom. */
  padding: 30px 0;
}

.modal {
  width: 300px; /* provided by question */
  display: flex;
  flex-direction: column;
  /* req 2: max height 300px */
  height: 100%;
  max-height: 300px;
}

.modal-header {
  background-color: #f44336; /* provided by question */
  width: 100%;
  /* req 1: fixed height basis of 50px header (flex-dir of container is "column") */
  /* alternatively, can set height:50px, flex-grow: 0, flex-shrink: 0 */
  flex: 0 0 50px;
}

.modal-body {
  background-color: #2973af; /* provided by question */
  width: 100%;
  /* req 1: stretching height body */
  flex: 1;
}
```

grid布局

```css
.modals {
  width: 100%;
  height: 100%;
  padding: 30px;
  display: grid;
  place-items: center;
}

.modal {
  width: 300px;
  max-height: 300px;
  height: 100%;
  display: grid;
  grid-template-rows: 50px auto;
}

.modal-header {
  background-color: #f44336;
}

.modal-body {
  background-color: #2973af;
}
```



## [6. different checkbox style](https://bigfrontend.dev/css/checkbox-style)

### 题目

The default checkbox style might not be what you want for most of the time, in this problem you are asked to create a different checkbox style.

1. when unchecked, show a gray circle (`'color: gray'`)
2. when checked, show a green circle (`'color: green'`)

Set the circle with radius of 5px and don't add extra padding, the HTML structure is

```html
<label class="my-checkbox">
  <input type="checkbox" />
  <span>a checkbox</span>
</label>
```

#### 1.unchecked

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910171904.png)

#### 2.checked

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910171924.png)

### 答案

Most solutions proposed here are either:

1. using the [unreliable appearance property](https://developer.mozilla.org/en-US/docs/Web/CSS/appearance), or
2. hiding the native input element completely from DOM tree with display:none or visibility:hidden ([It reduces a11y support for visually impaired users who rely on keyboard and audio](https://webaim.org/techniques/css/invisiblecontent/))

Here is the common practice to override native element without losing a11y support, reliably across browsers. Feel free to ask me any questions.

All credits go to Aditya Bhandari, I stumbled upon [his excellent article](https://medium.com/claritydesignsystem/pure-css-accessible-checkboxes-and-radios-buttons-54063e759bb3) while learning about this challenge.

```css
.my-checkbox {
  position: relative;
}

.my-checkbox input {
  /* refer to https://webaim.org/techniques/css/invisiblecontent/ */
  position: absolute;
  left: -99999px;
}

.my-checkbox span::before {
  background-color: gray;
  content: ''; /* "content" prop has to be set to render something */
  display: inline-block;
  /* circle with radius 5px */
  border-radius: 50%;
  height: 10px;
  width: 10px;
}

.my-checkbox input:checked + span::before {
  background-color: green;
}
```



## [7. a row](https://bigfrontend.dev/css/nth-child)

### 题目

Suppose we have a row structured as below:

```html
<div class="row"></div>
```

Please complete the CSS according to following requirements:

1. height 50px
2. set background to `#eee`, but if there are adjacent rows, set even rows' background to `#ddd`
3. set 1px border on top & bottom with color `#ccc`, but if there are adjacent rows, adjacent borders should be collapsed into one

> if you have to decide which row to dispplay both top and bottom border, add it to first row, [ref](https://bigfrontend.dev/css/7/discuss/8065?focus=8082)

#### 1.one row

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910175729.png)

#### 2.two rows

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910175759.png)

#### 3.three rows

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910175810.png)

### 答案

```css
.row {
  height: 50px;
  border-bottom: 1px solid #ccc;
}

.row:nth-of-type(even) {
  background: #ddd;
}

.row:nth-of-type(odd) {
  background: #eee;
}

.row:first-of-type {
  border-top: 1px solid #ccc;
}
```



## [8. Twitter's website layout](https://bigfrontend.dev/css/twitter-layout)

### 题目

Open Twitter's website and change the window size, you'll notice that layout changes responsively.

Let's do something similar in this problem, suppose we have HTML structure as below.

```html
<div class="container">
  <div class="left">left</div>
  <div class="middle">middle</div>
  <div class="right">right</div>
</div>
```

Now please complete the CSS to satisfy following requirement

1. when viewport width is not enough, set left column to 40px wide and middle column to stretching.
2. middle column has maximum width of 240px
3. when there is enough space, show right column which has width of 120px
4. if there is more space, set left column to 80px
5. when right column is visible, set minimum 10px space horizontally to the viewport border

It is a bit hard to explain clearly, but following screenshots might be easier to understand.

#### 1.100 x 150

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910222225.png)

#### 2.200 x 150

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910222302.png)

#### 3.300 x 150

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910222311.png)

#### 4.360 x 150

![4](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910222327.png)

#### 5.400 x 150

![5](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910222353.png)

#### 6.420 x 150

![6](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910222848.png)

#### 7.460 x 150

![7](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910222958.png)

#### 8.500 x 150

![8](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220910223011.png)

### 答案

媒体查询

```css
.container {
  display: flex;
  justify-content: center;
  height: 150px;
}

.left {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  /* Req 1: the left column is 40px by default */
  min-width: 40px;
  background-color: #eee;
}

.middle {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  background-color: #ddd;
  /* Req 2: Allow middle to grow until 240px */
  width: 100%;
  max-width: 240px;
}
.right {
  background-color: #eee;
  display: none;
}


@media screen and (max-width: 420px) {
  .right {
    display: none;
  }
}


@media screen and (min-width: 420px) {
  /* Req 3: When there is enough space, the right container will come into view */
  .right {
    display: flex;
    align-items: center;
    justify-content: center;
    /* Important property for determining when container should be shown */
    min-width: 120px;
  }

  /* Req 5: set margins of the container */
  .container {
    margin-right: 10px;
    margin-left: 10px;
  }

  /* Req 4: allow the column to grow up to 80px */
  .left {
    flex-grow: 1;
    max-width: 80px;
  }
}
```



## [9. multi-column text](https://bigfrontend.dev/css/multi-column-text)

### 题目

It is very common to see text in multiple columns on newspaper, please achieve something similar in this problem.

Suppose you have HTML structure like below.

```html
<div class="three-column-text">
  some very very long text
</div>
```

Complete the CSS code according to following requirements

1. divide into 3 columns
2. use 1px line of `#ddd` as separator
3. adding 10px space around each column, to container border and to the separator

> The text used in test doesn't contain space but has forced line break to show padding/margin clearly.

#### text in 3 columns

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912105148.png)

### 答案

https://css-tricks.com/almanac/properties/c/columns/

```css
.three-column-text {
  columns: 3 auto;
  column-rule: 1px solid #ddd;
  column-gap: 20px;
  padding: 10px;
}
```



## [10. golden-ratio rectangle](https://bigfrontend.dev/css/golden-ratio-rectangle)

### 题目

Let's create a golden-ratio rectangle, which means width/height = 1.618.

This rectangle should have the fixed ratio even when width changes.

```html
<div class="golden-ratio"></div>
```

#### 1.width: 50px

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912113029.png)

#### 2.width: 100px

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912113039.png)

#### 3.width: 200px

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912113056.png)

### 答案

https://developer.mozilla.org/en-US/docs/Web/CSS/aspect-ratio

```css
/*   Aspect-ratio

The CSS property aspect-ratio lets you create boxes 
that maintain proportional dimensions where the height
 and width of a box are calculated automatically as a ratio. 
 It’s a little math-y, but the idea is that you can divide one 
 value by another on this property and the calculated value 
 ensures a box stays in that proportion.

*/
.golden-ratio {
  background-color: #ccc;
  /* 为box容器规定了一个期待的纵横比, 此处为width/height = 1.618 */
  aspect-ratio: 1.618;
}
```



## [11. fit the image](https://bigfrontend.dev/css/fit-the-image)

### 题目

Suppose we have following image of ratio `1:4`

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912114022.svg)

Now in an HTML structure like below, please fit the image to the container div, just as if it is used as container's background-image with `background-size:cover`.

```html
<div>
  <img class="image" src="..."/>
</div>
```

#### 1.50x50

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912114031.png)

#### 2.100x100

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912114040.png)

#### 3.200x100

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912114046.png)

#### 4.100x200

![4](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912114052.png)

#### 5.300x100

![5](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912114059.png)

#### 6.100x300

![6](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912114108.png)

#### 7.700x100

![7](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912114116.png)

### 答案

https://developer.mozilla.org/en-US/docs/Web/CSS/object-fit

The object-fit CSS property sets how the content of a replaced element, such as an ![img]() or <video>, should be resized to fit its container.</video>

You can alter the alignment of the replaced element's content object within the element's box using the object-position property.

```css
.image {
  width: 100%;
  height: 100%;
  /* 被替换的内容在保持其宽高比的同时填充元素的整个内容框 */
  /* 如果对象的宽高比与内容框不相匹配，该对象将被剪裁以适应内容框 */
  object-fit: cover;
}
```



## [12. close button in CSS](https://bigfrontend.dev/css/css-cross-button)

### 题目

Create a close button in CSS.

Lines for the cross should have

1. length : 3/4 of button width
2. thickness: 2px
3. color: `#aaa`

```html
<button class="close"></button>
```

#### 1.20x20

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912115042.png)

#### 2.40x40

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912115111.png)

#### 3.60x60

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912115117.png)

### 答案

伪元素

```css
.close {
  border: 1px solid #aaa;
  border-radius: 50%;
  position: relative;
}

.close::before,
.close::after {
  content: '';
  position: absolute;
  left: 50%;
  top: 50%;
  background-color: #aaa;
  width: calc(100% * 3 / 4);
  height: 2px;
}

.close::before {
  transform: translate(-50%, -50%) rotate(45deg);
}

.close::after {
  transform: translate(-50%, -50%) rotate(-45deg);
}
```



## [13. list numbering](https://bigfrontend.dev/css/list-numbering)

### 题目

Suppose we have some lists, they could be simple as

```html
<ol>
  <li>BFE.dev</li>
  <li>JavaScript</li>
  <li>CSS</li>
  <li>System Design</li>
</ol>
```

or nested.

```html
<ol>
  <li>BFE.dev</li>
  <li>
    JavaScript
    <ol>
      <li>TypeScript</li>
      <li>Quiz</li>
      <li>
        Framework
        <ol>
          <li>React</li>
          <li>Vue.js</li>
        </ol>
      </li>
    </ol>
  </li>
  <li>CSS</li>
  <li>System Design</li>
</ol>
```

please number the list as below, with color `#f44336` and one space between the number and content.

#### 1.simple list

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912141421.png)

#### 2.nested

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912141428.png)

### 答案

https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Counter_Styles/Using_CSS_counters

```css
ol > li::marker {
  color: #f44336;
  content: counters(list-item, ".") " ";
}
```



## [14. 0.5px border](https://bigfrontend.dev/css/hairline)

### 题目

When we set 0.5px in CSS, usually it will be rounded by browsers. But with better and better display like Retina display, 1px would sometimes looks thick.

Please add 0.5px top border of black for the following div.

```html
<div class="hairline"></div>
```

#### should be thin enough

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912142529.png)

### 答案

利用trasform减低宽度

About scale: https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform-function/scale()

About transform-origin: https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform-origin

```css
.hairline {
  width: 150px;
  border-top: 1px solid black;
  /* 通过向量形式定义的缩放值来放大或缩小元素 此处只操作Y轴*/
  transform: scaleY(0.5);
  /* 定义变形中心为(0, 0)，及原点 (默认为元素的中心)*/
  transform-origin: 0 0;
}
```



## [15. doughnut chart](https://bigfrontend.dev/css/doughnut-chart)

### 题目

Let's create a doughnut chart in CSS.

1. radius: 50px
2. thickness: 10px
3. color: `#f44336`

The percentage should be able to set by CSS variable `--percentage`.

For example, to create a doughnut chart with 20%:

```html
<div class="piechart" style="--percentage:20%"></div>
```

A doughnut chart with 75%:

```html
<div class="piechart" style="--percentage:75%"></div>
```

#### 1.percentage: 20%

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912144427.png)

#### 2.percentage: 75%

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912144434.png)

#### 3.percentage: 100%

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912144441.png)

### 答案

To solve this task you have to make a circle using the 'clip-path' property instead of 'border-radius'. In this way, the browser draws a circle using other calculations. Then your solution will be accepted. 

```css
.piechart {
  width: 100px;
  height: 100px;
  clip-path: circle(50px at center);
  background-image: conic-gradient(#f44336 var(--percentage), transparent 0);
  mask-image: radial-gradient(circle closest-side, transparent 80%, black 80%);
}
```



## [16. flex layout 1](https://bigfrontend.dev/css/flex-layout-1)

### 题目

Suppose we have to layout a bunch of items with below requirements.

1. fill out to width:100px, but stretch to fill the available space and shrink if not enough
2. stack them if needed

HTML structure is

```html
<div class="container">
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
</div>
```

#### 1.width:60px

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912152353.png)

#### 2.width:150px

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912152400.png)

#### 3.width:250px

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912152406.png)

#### 4.width:400px

![4](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912152412.png)

### 答案

To solve this task you have to make a circle using the 'clip-path' property instead of 'border-radius'. In this way, the browser draws a circle using other calculations. Then your solution will be accepted. 

```css
.container {
  display: flex;
  flex-wrap: wrap;
}
.item {
  background-color: #7aa4f0;
  height: 50px;
  margin: 5px;
  flex: 1 1 100px;
}
```



## [17. fragment style](https://bigfrontend.dev/css/fragment-style)

### 题目

Suppose we have an `<a/>` tag which is streched to multiple lines, add borders to all its fragments.

1. color `#7aa4f0` with `1px` thickness
2. add `5px` padding horizontally for each line

```html
<a class="border">
  BFE.dev is a website to practice Front-End development skills.
</a>
```

#### 1.width:100px

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912153111.png)

#### 2.width:200px

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912153117.png)

#### 3.width:500px

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912153121.png)

### 答案

https://developer.mozilla.org/en-US/docs/Web/CSS/box-decoration-break

```css
.border {
  line-height: 1.5;
  margin: 5px;
  border: 1px solid #7aa4f0;
  box-decoration-break: clone;
  padding: 0 5px;
}
```



## [18. color gradients on text](https://bigfrontend.dev/css/gradient-text)

### 题目

Let's give text some color gradients, from `red` on the left to `yellow` on the right.

```html
<span class="gradient-text">practice on BFE.dev, get an offer</span>
```

#### 1.short text

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912171016.png)

#### 2.longer text

![](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912171022.png)

### 答案

https://developer.mozilla.org/en-US/docs/Web/CSS/box-decoration-break

```css
.gradient-text {
  font-size: 30px;
  font-weight: bold;
  background-image: linear-gradient(to right, red, yellow);
  background-clip: text;
  color: transparent;
}
```



## [19. color of input elements](https://bigfrontend.dev/css/change-color-of-input-elements)

### 题目

We have a slider but we want to change the default color into `#F44336`.

```html
<input type="range" min="0" max="10">
```

#### #F44336

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912171823.png)

### 答案

https://developer.mozilla.org/en-US/docs/Web/CSS/accent-color

```css
input[type="range"] {
  accent-color: #F44336;
}
```



## [20. sticky footer](https://bigfrontend.dev/css/sticky-footer)

### 题目

"Sticky footer" is a layout pattern that

1. if content is short, the footer "sticks" to the bottom
2. otherwise, footer is is displayed after the content as normal.

Suppose we have some HTML structure:

```html
<div class="container">
  <div class="header">header</div>
  <div class="body">
    content here might be tall , might be short
  </div>
  <div class="footer">footer</div>
</div>
```

Below is how Sticky Footer pattern should behave.

#### 1.footer sticks to bottom if content is short

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912172849.png)

#### 2.otherwise footer is displayed as normal

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912172900.png)

#### 3.event be pushed out from viewport if enough content

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912172910.png)

### 答案

 `margin: auto` actually has no specific rules and browser pretty much decides how it effects the element 😅

However, when we give any margin as auto to a child in a flex container, it basically means that margin should take the maximum possible value for that element.

In simple terms, it will push that element to opposite edge, ex. `margin-left : auto` , will push that element to the right edge.

If you want a little more detail, read this explanation on https://css-tricks.com/the-peculiar-magic-of-flexbox-and-auto-margins/

```css
.container{
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}
.header {
  background-color: #555;
  color: #fff;
  padding: 20px 10px;
}
.body {
  padding: 20px 10px;
}
.footer {
  background-color: #555;
  color: #fff;
  padding: 20px 10px;
  margin-top: auto;
}
```



## [21. Holy Grail Layout](https://bigfrontend.dev/css/Holy-Grail-Layout)

### 题目

"Implement Holy Grail Layout which includes header, left sidebar, body, right sidebar and footer.

1. it should be [sticky footer](https://bigfrontend.dev/css/sticky-footer)
2. left sidebar and right sidebar should be fixed width of 100px.
3. styles of color .etc are already set for you, only layout related CSS code is needed.

```html
<div class="container">
  <header class="header">header</header>
  <div class="left-sidebar">left sidebar</div>
  <div class="body">body</div>
  <div class="right-sidebar">right sidebar</div>
  <footer class="footer">footer</footer>
</div>
```

#### 1.footer sticks to bottom if content is short

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912174051.png)

#### 2.otherwise footer is displayed as normal

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912174107.png)

#### 3.event be pushed out from viewport if enough content

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912174118.png)

### 答案

grid布局

```css
.container {
  min-height: 100%;
  display: grid;
  grid-template-areas: "head head head"
                       "left body right"
                       "foot foot foot";
  grid-template-columns: 100px 1fr 100px;
  grid-template-rows: auto 1fr auto;
}

.header {
  grid-area: head;
}
.left-sidebar {
  grid-area: left;
}
.body {
  grid-area: body;
}
.right-sidebar {
  grid-area: right;
}
.footer {
  grid-area: foot;
}
```

flex布局

```css
.header, .footer{
  width: 100%;
  height: 50px;
}

.body{
  flex: 1 1 auto;
  min-height: calc(100vh - 100px);
}

.left-sidebar, .right-sidebar{
  width: 100px;
}

.container{
  display: flex;
  flex-wrap: wrap;
  min-height: 100vh;
}
```



## [22. Grid Layout 1](https://bigfrontend.dev/css/grid-layout-1)

### 题目

Layout a 3x3 grid and place `A` to `I` in column-first order.

1. set gap in rows and columns of `10px`
2. container size is already set.

```html
<div class="container">
  <div class="item a">A</div>
  <div class="item b">B</div>
  <div class="item c">C</div>
  <div class="item d">D</div>
  <div class="item e">E</div>
  <div class="item f">F</div>
  <div class="item g">G</div>
  <div class="item h">H</div>
  <div class="item i">I</div>
</div>
```

#### 200x200

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220912225051.png)

### 答案

grid布局

```css
.item {
  background-color: #BFEBFE;
  display: grid;
  place-items: center;
}

.container {
  display: grid;
  grid-template-rows: repeat(3, 1fr);
  grid-auto-flow: column;
  gap: 10px;
}
```

另一种方式

```css
.container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr;
  grid-auto-flow: column;
  gap: 10px;
}

.item {
  background-color: #BFEBFE;
  text-align: center;
  line-height: 60px;
}
```

flex布局也可以

```css
.item {
  flex-basis: 30%;
  height: calc((200px-10px/3));
  background-color: #BFEBFE;
  display: flex;
  align-items: center;
  justify-content: center;
}

.container {
  width: 200px;
  display: flex;
  flex-wrap: wrap;
  flex-direction: column;
  gap: 10px;
}
```



## [23. Grid Layout 2](https://bigfrontend.dev/css/grid-layout-2)

### 题目

Layout the items in a grid so that:

- items have minium width of 100px and fill up the space
- place as many items in a row as possible
- gap between items is 10px

```html
<div class="container">
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
</div>
```

#### 1.width:100px

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220913085648.png)

#### 2.width:150px

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220913085659.png)

#### 3.width:250px

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220913085704.png)

#### 4.width:400px

![4](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220913085710.png)

### 答案

自适应grid布局

```css
.container {
  display: grid;
  gap: 10px;
  grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
}

.item {
  height: 50px;
  background-color: #7aa4f0;
}
```



## [24. fluid font size](https://bigfrontend.dev/css/fluid-font-size)

### 题目

Create a heading of fluid font size.

1. if viewport width is smaller than 200px, use 16px
2. if viewport width is bigger than 400px, use 32px
3. otherwise font-size is linearly scaled. For example if viewport is 300px = (200px + 400px) / 2, then font-size is 24px = (16px + 32px) / 2

```html
<h1 class="title">BFE.dev</h1>
```

#### 1.100px

![1](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220913090844.png)

#### 2.150px

![2](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220913090858.png)

#### 3.200px

![3](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220913090902.png)

#### 4.219px

![4](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220913090908.png)

#### 5.252px

![5](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220913090914.png)

#### 6.300px

![6](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220913090920.png)

#### 7.379px

![7](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220913090930.png)

#### 8.400px

![8](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220913091102.png)

#### 9.500px

![9](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220913091130.png)

### 答案

`clamp()` is a CSS function that clamps a value between an upper and lower bound. clamp() enables selecting a middle value within a range of values between a defined minimum and maximum. It takes three parameters: a minimum value, a preferred value, and a maximum allowed value. https://developer.mozilla.org/en-US/docs/Web/CSS/clamp

```css
.title {
  text-align: center;
  /* 200/16 = 12.5 and also 400/32 = 12.5 so that is our linear ratio to keep when scaling
   we set minimum (16px) and maximum (32px) boundary for clam, and a function to dinamically
   calculate the values in between, which is given by diving our viewport (100vw)
   to our ratio 12.5 */
  font-size: clamp(16px, calc(100vw/12.5), 32px);
}
```

