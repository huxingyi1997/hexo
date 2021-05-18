---
title:  css面试题
date:  2021-3-6 12:02:39
categories: 
- web前端
tags:
- 前端基础
- CSS
- 面试
---

CSS样式基本布局也是前端的基本功课，相比JS，自己这方面并没有做足功课，只能根据常见的面试题反向逼迫自己尽快复习了，实习春招和秋招加油吧
<!-- more -->

# 1.[css盒模型简介](https://www.cnblogs.com/112233-j/p/12600228.html)

盒子模型，英文即box model。无论是div、span、还是a都是盒子。

但是，图片、表单元素一律看作是文本，它们并不是盒子。这个很好理解，比如说，一张图片里并不能放东西，它自己就是自己的内容。

## 1.1 盒子中的区域

一个盒子中主要的属性就5个：width、height、padding、border、margin。如下：

- width和height：**内容**的宽度、高度（不是盒子的宽度、高度）。
- padding：内边距。
- border：边框。
- margin：外边距。

盒子模型的示意图：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418143847.png)

代码演示：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418143901.png)

上面这个盒子，width:200px; height:200px; 但是真实占有的宽高是302*302。 这是因为还要加上padding、border。

注意：**宽度和真实占有宽度，不是一个概念！**来看下面这例子。

## 1.2 标准盒模型和IE盒模型

> 我们目前所学习的知识中，以标准盒子模型为准。

标准盒子模型：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418143913.jpeg)

IE盒子模型：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418143927.jpeg)

上图显示：

在 CSS 盒子模型 (Box Model) 规定了元素处理元素的几种方式：

- width和height：**内容**的宽度、高度（不是盒子的宽度、高度）。
- padding：内边距。
- border：边框。
- margin：外边距。

CSS盒模型和IE盒模型的区别：

- 在 **标准盒子模型**中，**width 和 height 指的是内容区域**的宽度和高度。增加内边距、边框和外边距不会影响内容区域的尺寸，但是会增加元素框的总尺寸。
- **IE盒子模型**中，**width 和 height 指的是内容区域+border+padding**的宽度和高度。

注：Android中也有margin和padding的概念，意思是差不多的，如果你会一点Android，应该比较好理解吧。区别在于，Android中没有border这个东西，而且在Android中，margin并不是控件的一部分，我觉得这样做更合理一些，呵呵。

## 1.3 `<body>`标签也有margin

`<body>`标签有必要强调一下。很多人以为`<body>`标签占据的是整个页面的全部区域，其实是错误的，正确的理解是这样的：整个网页最大的盒子是`<document>`，即浏览器。而`<body>`是`<document>`的儿子。浏览器给`<body>`默认的margin大小是8个像素，此时`<body>`占据了整个页面的一大部分区域，而不是全部区域。来看一段代码。

```html
<!doctype html>
<html lang="en">
 <head>
  <meta charset="UTF-8">
  <meta name="Generator" content="EditPlus®">
  <meta name="Author" content="">
  <meta name="Keywords" content="">
  <meta name="Description" content="">
  <title>Document</title>

	<style type="text/css">
		div{
			width: 100px;
			height: 100px;
			border: 1px solid red;
			padding: 20px;
			margin: 30px;
		}
	</style>

 </head>

 <body>

	<div>有生之年</div>
	<div>狭路相逢</div>

 </body>

</html>
```

上面的代码中，我们对div标签设置了边距等信息。打开google浏览器，按住F12，显示效果如下：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418143956.png)

## 1.4 认识width、height

一定要知道，在前端开发工程师眼中，世界中的一切都是不同的。

比如说，丈量稿纸，前端开发工程师只会丈量内容宽度：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418144048.png)

下面这两个盒子，真实占有宽高，都是302*302：

盒子1：

```css
	.box1{
		width: 100px;
		height: 100px;
		padding: 100px;
		border: 1px solid red;
	}
```

盒子2：

```css
	.box2{
		width: 250px;
		height: 250px;
		padding: 25px;
		border: 1px solid red;
	}
```

真实占有宽度 = 左border + 左padding + width + 右padding + 右border

上面这两个盒子的盒模型图如下：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418144128.png)

**如果想保持一个盒子的真实占有宽度不变，那么加width的时候就要减padding。加padding的时候就要减width**。因为盒子变胖了是灾难性的，这会把别的盒子挤下去。

## 1.5 认识padding

### 1.5.1 padding区域也有颜色

padding就是内边距。padding的区域有背景颜色，css2.1前提下，并且背景颜色一定和内容区域的相同。也就是说，background-color将填充**所有border以内的区域。**

效果如下：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418144202.png)

### 1.5.2 padding有四个方向

padding是4个方向的，所以我们能够分别描述4个方向的padding。

方法有两种，第一种写小属性；第二种写综合属性，用空格隔开。

小属性的写法：

```
	padding-top: 30px;
	padding-right: 20px;
	padding-bottom: 40px;
	padding-left: 100px;
```

综合属性的写法：(上、右、下、左)（顺时针方向，用空格隔开。margin的道理也是一样的）

```
padding:30px 20px 40px 100px;
```

如果写了四个值，则顺序为：上、右、下、左。

如果只写了三个值，则顺序为：上、右、下。??和右一样。

如果只写了两个值，比如说：

```
padding: 30px 40px;
```

则顺序等价于：30px 40px 30px 40px;

要懂得，**用小属性层叠大属性**。比如：

```
padding: 20px;
padding-left: 30px;
```

上面的padding对应盒子模型为：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418144231.png)

下面的写法：

```
padding-left: 30px;
padding: 20px;
```

第一行的小属性无效，因为被第二行的大属性层叠掉了。

下面的题，会做了，说明你明白了。

### 1.5.3 一些题目

**题目1**：说出下面盒子真实占有宽高，并画出盒模型图。

```css
	div{
		width: 200px;
		height: 200px;
		padding: 10px 20px 30px;
		padding-right: 40px;
		border: 1px solid #000;
	}
```

答案：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418144325.png)

**题目2**：说出下面盒子真实占有宽高，并画出盒模型图。

```css
	div{
		width: 200px;
		height: 200px;
		padding-left: 10px;
		padding-right: 20px;
		padding:40px 50px 60px;
		padding-bottom: 30px;
		border: 1px solid #000;
	}
```

答案：

`padding-left:10px；` 和`padding-right:20px;` 没用，因为后面的padding大属性，层叠掉了他们。

盒子模型如下：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418144500.png)

**题目3**：现在给你一个盒子模型图，请写出代码，试着用最最简单的方法写。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418144514.png)

答案：

```
	width:123px;
	height:123px;
	padding:20px 40px;
	border:1px solid red;
```

**题目4**：现在给你一个盒子模型图，请写出代码，试着用最最简单的方法写。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418144529.png)

答案：

```
	width:123px;
	height:123px;
	padding:20px;
	padding-right:40px;
	border:1px solid red;
```

### 1.5.4 一些元素，默认带有padding

一些元素，默认带有`padding`，比如ul标签。如下：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418145114.png)

上图显示，不加任何样式的ul，也是有40px的padding-left。

所以，我们做站的时候，为了便于控制，总是喜欢清除这个默认的padding。

可以使用`*`进行清除：

```
*{
    margin: 0;
    padding: 0;
}
```

但是，`*`的效率不高，所以我们使用并集选择器，罗列所有的标签（不用背，有专业的清除默认样式的样式表）：

```
body,div,dl,dt,dd,ul,ol,li,h1,h2,h3,h4,h5,h6,pre,code,form,fieldset,legend,input,textarea,p,blockquote,th,td{
    margin:0;
    padding:0;
}
```

## 1.6 认识border

border就是边框。边框有三个要素：像素（粗细）、线型、颜色。

颜色如果不写，默认是黑色。另外两个属性不写，要命了，显示不出来边框。

### 1.6.1 border-style

border的所有的线型如下：（我们可以通过查看`CSS参考手册`得到）

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418145249.png)

比如`border:10px ridge red;`这个属性，在chrome和firefox、IE中有细微差别：（因为可以显示出效果，因此并不是兼容性问题，只是有细微差别而已）

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418145302.png)

如果公司里面的设计师是处女座的，追求极高的**页面还原度**，那么不能使用css来制作边框。就要用到图片，就要切图了。

所以，比较稳定的border-style就几个：solid、dashed、dotted。

### 1.6.2 border拆分

border是一个大综合属性。比如说：

```
border:1px solid red;
```

就是把4个边框，都设置为1px宽度、线型实线、red颜色。

PS：小技巧：在sublime text中，为了快速输入`border:1px solid red;`这个属性，可以直接输入`bd`，然后选第二个后回车。

border属性是能够被拆开的，有两大种拆开的方式：

- （1）按三要素拆开：border-width、border-style、border-color。（一个border属性是由三个小属性综合而成的）
- （2）按方向拆开：border-top、border-right、border-bottom、border-left。

现在我们明白了：**一个border属性，是由三个小属性综合而成的**。如果某一个小属性后面是空格隔开的多个值，那么就是上右下左的顺序。举例如下：

```
border-width:10px 20px;
border-style:solid dashed dotted;
border-color:red green blue yellow;
```

效果如下：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418145328.png)

（1）按三要素拆：

```
border-width:10px;    //边框宽度
border-style:solid;   //线型
border-color:red;     //颜色。
```

等价于：

```
border:10px solid red;
```

(2)按方向来拆：

```
border-top:10px solid red;
border-right:10px solid red;
border-bottom:10px solid red;
border-left:10px solid red;
```

等价于：

```
border:10px solid red;
```

（3）按三要素和方向来拆：(就是把每个方向的，每个要素拆开。3*4 = 12)

```
	border-top-width:10px;
	border-top-style:solid;
	border-top-color:red;
	border-right-width:10px;
	border-right-style:solid;
	border-right-color:red;
	border-bottom-width:10px;
	border-bottom-style:solid;
	border-bottom-color:red;
	border-left-width:10px;
	border-left-style:solid;
	border-left-color:red;
```

等价于：

```
border:10px solid red;
```

工作中到底用什么？很简答：什么简单用什么。但要懂得，用小属性层叠大属性。举例如下：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418145338.png)

为了实现上方效果，写法如下：

```
border:10px solid red;
border-right-color:blue;
```

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418145344.png)

为了实现上方效果，写法如下：

```
border:10px solid red;
border-style:solid dashed;
```

border可以没有：

```
border:none;
```

可以某一条边没有：

```
border-left: none;
```

也可以调整左边边框的宽度为0：

```
border-left-width: 0;
```

### 1.6. 3 举例：利用border属性画一个三角形（小技巧）

步骤如下：

（1）当我们设置盒子的width和height为0时，此时效果如下：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418145413.png)

（2）然后将border的底部取消：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418145420.png)

（3）最后设置border的左边和右边为白色：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418145431.png)

这样，一个三角形就画好了。

[CSS绘制三角形](http://www.yanhuangxueyuan.com/HTML5/icon.html) 讲解的是利用boder外边框属性绘制各种三角形的案例。

[CSS绘制圆(弧)](http://www.yanhuangxueyuan.com/HTML5/icon.html) 讲解如何利用CSS的基本属性圆角border-radius绘制与圆、圆弧相关的图形。

# 2.[CSS 选择器](https://segmentfault.com/a/1190000013424772)

> 摘自 MDN web docs

## 2.0 简介

选择器是 CSS 规则的一部分且位于 CSS 声明块前。

![image](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418165257.png)

选择器可以被分为以下类别：

- `简单选择器（Simple selectors）`：通过元素类型、class 或 id 匹配一个或多个元素。
- `属性选择器（Attribute selectors）`：通过 属性 / 属性值 匹配一个或多个元素。
- `伪类（Pseudo-classes）`：匹配处于确定状态的一个或多个元素，比如被鼠标指针悬停的元素，或当前被选中或未选中的复选框，或元素是 DOM 树中一父节点的第一个子节点。
- `伪元素（Pseudo-elements）`:匹配处于相关的确定位置的一个或多个元素，例如每个段落的第一个字，或者某个元素之前生成的内容。
- `组合器（Combinators）`：这里不仅仅是选择器本身，还有以有效的方式组合两个或更多的选择器用于非常特定的选择的方法。例如，你可以只选择 divs 的直系子节点的段落，或者直接跟在 headings 后面的段落。
- `多用选择器（Multiple selectors）`：这些也不是单独的选择器；这个思路是将以逗号分隔开的多个选择器放在一个 CSS 规则下面， 以将一组声明应用于由这些选择器选择的所有元素。

## 2.1 简单选择器

### 2.1.1 类型选择器（又名：元素选择器）

此选择器只是一个选择器名和指定的HTML元素名的不区分大小写的匹配。这是选择所有指定类型的最简单方式。

### 2.1.2 类选择器（Class selectors）

类选择器由一个点“.”以及类后面的类名组成。类名是在HTML class文档元素属性中没有空格的任何值。由你自己选择一个名字。同样值得一提的是，文档中的多个元素可以具有相同的类名，而单个元素可以有多个类名(以空格分开多个类名的形式书写)。

### 2.1.3 ID 选择器

ID选择器由哈希/磅符号 (#)组成，后面是给定元素的ID名称。 任何元素都可以使用id属性设置唯一的ID名称。 由你自己选择的ID是什么。 这是选择单个元素的最有效的方式。

> 重要提示：一个ID名称必须在文件中是唯一的。关于重复ID的行为是不可预测的，比如在一些浏览器只是第一个实例计算，其余的将被忽略。

### 2.1.4 通用选择器（Universal selector）

通用选择（*）是最终的王牌。它允许选择在一个页面中的所有元素。由于给每个元素应用同样的规则几乎没有什么实际价值，更常见的做法是与其他选择器结合使用。

> 重要提示：使用通用选择时小心。因为它适用于所有的元素，在大型网页利用它可以对性能有明显的影响：网页可以显示比预期要慢。不会有太多的情况下，您想使用此选择。

### 2.1.5 组合器（Combinators）

在CSS中，组合器允许您将多个选择器组合在一起，这允许您在其他元素中选择元素，或者与其他元素相邻。四种可用的类型是：

- 后代选择器——（空格键）——允许您选择嵌套在另一个元素中的某个元素（不一定是直接的后代;例如，它可以是一个孙子）。
- 子选择器—— > ——允许您选择一个元素，该元素是另一个元素的直接子元素。
- 相邻兄弟选择器—— + ——允许您选择一个元素，它是另一个元素的直接兄弟元素(也就是说，在它的旁边，在层次结构的同一层)。
- 通用兄弟选择器—— ~ — —允许您选择其他元素的兄弟元素(例如，在层次结构中的相同级别，但不一定就在它的旁边)。

| Combinators | Select                                                       |
| ----------- | ------------------------------------------------------------ |
| A,B         | 匹配满足A（和/或）B的任意元素.                               |
| A B         | 匹配任意元素，满足条件：B是A的后代结点（B是A的子节点，或者A的子节点的子节点） |
| A > B       | 匹配任意元素，满足条件：B是A的直接子节点                     |
| A + B       | 匹配任意元素，满足条件：B是A的下一个兄弟节点（AB有相同的父结点，并且B紧跟在A的后面） |
| A ~ B       | 匹配任意元素，满足条件：B是A之后的兄弟节点中的任意一个（AB有相同的父节点，B在A之后，但不一定是紧挨着A） |

> 注：相邻兄弟选择器和通用兄弟选择器只会“向后”选择，DOM结构靠前的兄弟元素不在选择范围内。

这里有一个简单的例子来展示这些工作是如何工作的：

```
<section>
  <h2>Heading 1</h2>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
  <div>
    <h2>Heading 2</h2>
    <p>Paragraph 3</p>
    <p>Paragraph 4</p>
  </div>
</section>
section p {
  color: blue;
}

section > p {
  background-color: yellow;
}

h2 + p {
  text-transform: uppercase;
}

h2 ~ p {
  border: 1px dashed black;
}
```

CSS样式的HTML如下所示：

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418165352.png)

选择器是这样工作的：

- section p选择了所有的 `<p>` 元素——前两个 `<p>` 都是 `<section>` 元素的直接子元素，而后面的两个 `<p>` 元素是 `<section>` 元素的孙子元素(它们在 `<div>`里面)。因此，所有的段落文本都是蓝色的。
- section > p 只选择前两个 `<p>` 元素，这两个元素是 `<section>` 元素的直接子元素（但后两个 `<p>`元素不是，它们不是直接的子元素）。所以只有前两段有黄色的背景色。
- h2 + p 只选择在相同层次结构的 `<h2>` 元素之后直接相连的 `<p>` 元素—— 在本例中是第一和第三段。因此，这些文本都是大写的。
- h2 ~ p 选择任何在相同的层级上（还有之后的） `<h2>` 元素的 `<p>` 元素 ——在这种情况下，所有的段落符合此条件。因此，所有的这些都有一个虚线的边界。

## 2.2 属性选择器

属性选择器是一种特殊类型的选择器，它根据元素的 属性和属性值来匹配元素。它们的通用语法由方括号([]) 组成，其中包含属性名称，后跟可选条件以匹配属性的值。 属性选择器可以根据其匹配属性值的方式分为两类： **存在和值属性选择器** 和**子串值属性选择器**。

### 2.2.1 存在和值（Presence and value）属性选择器

这些属性选择器尝试匹配精确的属性值：

- [attr]：该选择器选择包含 attr 属性的所有元素，不论 attr 的值为何。
- [attr=val]：该选择器仅选择 attr 属性被赋值为 val 的所有元素。
- [attr~=val]：该选择器仅选择 attr 属性的值（以空格间隔出多个值）中有包含 val 值的所有元素，比如位于被空格分隔的多个类（class）中的一个类。

### 2.2.2 子串值（Substring value）属性选择器

这种情况的属性选择器也被称为“伪正则选择器”，因为它们提供类似 regular expression 的灵活匹配方式（但请注意，这些选择器并不是真正的正则表达式）：

- [attr|=val] : 选择attr属性的值以val（包括val）或val-开头的元素（-用来处理语言编码）。
- [attr^=val] : 选择attr属性的值以val开头（包括val）的元素。
- [attr$=val] : 选择attr属性的值以val结尾（包括val）的元素。
- [attr*=val] : 选择attr属性的值中包含字符串val的元素。

## 2.3 伪类和伪元素

### 2.3.1 伪类（Pseudo-class）

一个 CSS [伪类（pseudo-class）](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes) 是一个以冒号(:)作为前缀的关键字，当你希望样式在特定状态下才被呈现到指定的元素时，你可以往元素的选择器后面加上对应的伪类（pseudo-class）。你可能希望某个元素在处于某种状态下呈现另一种样式，例如当鼠标悬停在元素上面时，或者当一个 checkbox 被禁用或被勾选时，又或者当一个元素是它在 DOM 树中父元素的第一个孩子元素时。

:active
:any
:checked
:default
:dir()
:disabled
:empty
:enabled
:first
:first-child
:first-of-type
:fullscreen
:focus
:hover
:indeterminate
:in-range
:invalid
:lang()
:last-child
:last-of-type
:left
:link
:not()
:nth-child()
:nth-last-child()
:nth-last-of-type()
:nth-of-type()
:only-child
:only-of-type
:optional
:out-of-range
:read-only
:read-write
:required
:right
:root
:scope
:target
:valid
:visited

### 2.3.2 伪元素

[伪元素（Pseudo-element）](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements)跟伪类很像，但它们又有不同的地方。它们都是关键字 —— 但这次伪元素前缀是两个冒号 (::) —— 同样是添加到选择器后面达到指定某个元素的某个部分。

::after
::before
::first-letter
::first-line
::selection
::backdrop

[css](https://segmentfault.com/t/css)

## 2.4 CSS三大特性——继承、 优先级和层叠。

**继承：**即子类元素继承父类的样式;

**优先级：**是指不同类别样式的权重比较;

**层叠：**是说当数量相同时，通过层叠(后者覆盖前者)的样式。

## 2.5 css选择符分类

首先来看一下css选择符(css选择器)有哪些?


　　1.标签选择器(如：body,div,p,ul,li)

　　2.类选择器(如：class="head",class="head_logo")

　　3.ID选择器(如：id="name",id="name_txt")

　　4.全局选择器(如：*号)

　　5.组合选择器(如：.head .head_logo,注意两选择器用空格键分开)

　　6.后代选择器 (如：#head .nav ul li 从父集到子孙集的选择器)

　　7.群组选择器 div,span,img {color:Red} 即具有相同样式的标签分组显示

　　8.继承选择器(如：div p,注意两选择器用空格键分开)

　　9.伪类选择器(如：就是链接样式,a元素的伪类，4种不同的状态：link、visited、active、hover。)

　　10.字符串匹配的属性选择符(^ $ *三种，分别对应开始、结尾、包含)

　　11.子选择器 (如：div>p ,带大于号>)

　　12.CSS 相邻兄弟选择器器 (如：h1+p,带加号+)

## 2.6 css优先级

当两个规则都作用到了同一个html元素上时，如果定义的属性有冲突，那么应该用谁的值的，CSS有一套优先级的定义。

**不同级别**

1. 在属性后面使用 !important 会覆盖页面内任何位置定义的元素样式。
2. 作为style属性写在元素内的样式
3. id选择器
4. 类选择器
5. 标签选择器
6. 通配符选择器
7. 浏览器自定义或继承

   **总结排序：!important > 行内样式>ID选择器 > 类选择器 > 标签 > 通配符 > 继承 > 浏览器默认属性**

**同一级别**

同一级别中后写的会覆盖先写的样式

上面的级别还是很容易看懂的，但是有时候有些规则是多个级别的组合，像这样

```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style type="text/css">
        div.test{
            background-COLOR:#a00;
            width:100px;
            height: 100px;
        }

        .test.test2{
            background-COLOR:#0e0;
            width:100px;
            height: 100px;
        }
    </style>
</head>
<body>
    <div class="test test2"></div>
</body>
</html>
```

到底div是应用那条规则呢，有个简单的计算方法（经园友提示，权值实际并不是按十进制，用数字表示只是说明思想，一万个class也不如一个id权值高）

- 内联样式表的权值为 1000
- ID 选择器的权值为 100
- Class 类选择器的权值为 10
- HTML 标签选择器的权值为 1

 我们可以把选择器中规则对应做加法，比较权值，如果权值相同那就后面的覆盖前面的了，div.class的权值是1+10=11，而.test1 .test2的权值是10+10=20，所以div会应用.test1 .test2变成绿色

 ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418165716.png)

## 2.7 另外一种理解方式：

　　CSS优先级：是由四个级别和各级别的出现次数决定的。

　　四个级别分别为：行内选择符、ID选择符、类别选择符、元素选择符。

　　优先级的算法：

　　每个规则对应一个初始"四位数"：0、0、0、0

　　若是 行内选择符，则加1、0、0、0

　　若是 ID选择符，则加0、1、0、0

　　若是 类选择符/属性选择符/伪类选择符，则分别加0、0、1、0

　　若是 元素选择符/伪元素选择符，则分别加0、0、0、1

　　算法：将每条规则中，选择符对应的数相加后得到的”四位数“，从左到右进行比较，大的优先级越高。　　

## 2.8 需注意的：

　　①、!important的优先级是最高的，但出现冲突时则需比较”四位数“;

　　②、优先级相同时，则采用就近原则，选择最后出现的样式;

　　③、继承得来的属性，其优先级最低;

　　!important > 行内样式>ID选择器 > 类选择器 > 标签 > 通配符 > 继承 > 浏览器默认属性

　　*css选择器使用强烈建议采用低权重原则，利于充分发挥css的继承性，复用性，模块化、组件化。

## 2.9 CSS选择器的解析原则

​     以前一直认为选择器的定位DOM元素是从左向右的方向，查看了网上的相关资料之后才发现原来自己一直都是错的。郑重的声明选择器定位DOM元素是从右往左的方向，这样的好处是尽早的过滤掉一些无关的样式规则和元素 。[为什么CSS选择器是从右往左解析 ？？？](http://blog.csdn.net/jinboker/article/details/52126021)

## 2.10 简洁、高效的css

​    所谓高效就是让浏览器查找更少的元素标签来确定匹配的style元素。

   1.不要再ID选择器前使用标签名

​    解释：ID选择是唯一的，加上标签名相当于画蛇添足了，没必要。

   2.不要在类选择器前使用标签名

   解释：如果没有相同的名字出现就是没必要，但是如果存在多个相同名字的类选择器则有必要添加标签名防止混淆如（p.colclass{color：red;} 和 span.colclass{color:red;}

   3.尽量少使用层级关系；

​     \#divclass p.colclass{color:red;}改为  .colclass{color:red;}

   4.使用类选择器代替层级关系（如上）

# 3. BFC 原理

## 3.1 常见定位方案

在讲 BFC 之前，我们先来了解一下常见的定位方案，定位方案是控制元素的布局，有三种常见方案:

- 普通流 (normal flow)

> 在普通流中，元素按照其在 HTML 中的先后位置至上而下布局，在这个过程中，行内元素水平排列，直到当行被占满然后换行，块级元素则会被渲染为完整的一个新行，除非另外指定，否则所有元素默认都是普通流定位，也可以说，普通流中元素的位置由该元素在 HTML 文档中的位置决定。

- 浮动 (float)

> 在浮动布局中，元素首先按照普通流的位置出现，然后根据浮动的方向尽可能的向左边或右边偏移，其效果与印刷排版中的文本环绕相似。

- 绝对定位 (absolute positioning)

> 在绝对定位布局中，元素会整体脱离普通流，因此绝对定位元素不会对其兄弟元素造成影响，而元素具体的位置由绝对定位的坐标决定。

## 3.2 BFC 概念

Formatting context(格式化上下文) 是 W3C CSS2.1 规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。



那么 BFC 是什么呢？

BFC 即 Block Formatting Contexts (块级格式化上下文)，它属于上述定位方案的普通流。

**具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。
**

通俗一点来讲，可以把 BFC 理解为一个封闭的大箱子，箱子内部的元素无论如何翻江倒海，都不会影响到外部。

## 3.3 触发 BFC

只要元素满足下面任一条件即可触发 BFC 特性：

- body 根元素
- 浮动元素：float 除 none 以外的值
- 绝对定位元素：position (absolute、fixed)
- display 为 inline-block、table-cells、flex
- overflow 除了 visible 以外的值 (hidden、auto、scroll)

## 3.4 BFC 特性及应用

### 3.4.1 同一个 BFC 下外边距会发生折叠

```html
<head>
div{
    width: 100px;
    height: 100px;
    background: lightblue;
    margin: 100px;
}
</head>
<body>
    <div></div>
    <div></div>
</body>
```

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418165114.png)

从效果上看，因为两个 div 元素都处于同一个 BFC 容器下 (这里指 body 元素) 所以第一个 div 的下边距和第二个 div 的上边距发生了重叠，所以两个盒子之间距离只有 100px，而不是 200px。

首先这不是 CSS 的 bug，我们可以理解为一种规范，**如果想要避免外边距的重叠，可以将其放在不同的 BFC 容器中。**

```html
<div class="container">
    <p></p>
</div>
<div class="container">
    <p></p>
</div>
.container {
    overflow: hidden;
}
p {
    width: 100px;
    height: 100px;
    background: lightblue;
    margin: 100px;
}
```

这时候，两个盒子边距就变成了 200px

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418165123.png)

### 3.4.2 BFC 可以包含浮动的元素（清除浮动）

我们都知道，浮动的元素会脱离普通文档流，来看下下面一个例子

```html
<div style="border: 1px solid #000;">
    <div style="width: 100px;height: 100px;background: #eee;float: left;"></div>
</div>
```

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418165132.png)

由于容器内元素浮动，脱离了文档流，所以容器只剩下 2px 的边距高度。如果使触发容器的 BFC，那么容器将会包裹着浮动元素。

```html
<div style="border: 1px solid #000;overflow: hidden">
    <div style="width: 100px;height: 100px;background: #eee;float: left;"></div>
</div>
```

效果如图：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418165148.png)

### 3.4.3 BFC 可以阻止元素被浮动元素覆盖

先来看一个文字环绕效果：

```html
<div style="height: 100px;width: 100px;float: left;background: lightblue">我是一个左浮动的元素</div>
<div style="width: 200px; height: 200px;background: #eee">我是一个没有设置浮动, 
也没有触发 BFC 元素, width: 200px; height:200px; background: #eee;</div>
```

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418165156.png)

这时候其实第二个元素有部分被浮动元素所覆盖，(但是文本信息不会被浮动元素所覆盖) 如果想避免元素被覆盖，可触第二个元素的 BFC 特性，在第二个元素中加入 **overflow: hidden**，就会变成：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418165204.png)

这个方法可以用来实现两列自适应布局，效果不错，这时候左边的宽度固定，右边的内容自适应宽度(去掉上面右边内容的宽度)。

# 4. position

## 1. static

默认值。没有定位，元素出现在正常的流中（忽略 top, bottom, left, right 或者 z-index 声明）。

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>position</title>
        <style>
            * {
                padding: 0;
                margin: 0;
            }
            .static {
                position: static;
                top: 10px;
                left: 10px;
                width: 100px;
                height: 200px;
                background: yellow;
            }
        </style>
    </head>
    <body>
        <div class="static">
        </div>
    </body>
</html>
```

金黄色方块会在左上角显示，没有偏移。

## 2. fixed

生成绝对定位的元素，相对于浏览器窗口进行定位。元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。

这个比较好理解，只和窗口有关，与父元素，文档流都无关。

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>position</title>
        <style>
            * {
                padding: 0;
                margin: 0;
            }
            .fixed-outer {
                position: fixed;
                top: 20px;
                left: 20px;
                width: 20px;
                height: 20px;
                background: yellow;
            }
            .fixed-inner {
                position: fixed;
                top: 40px;
                left: 40px;
                width: 20px;
                height: 20px;
                background: red;
            }
        </style>
    </head>
    <body  bgcolor="#999">
        <div class="fixed-outer">
            <div class="fixed-inner">
            </div>
        </div>
    </body>
</html>
```

![img](https://images2018.cnblogs.com/blog/670878/201803/670878-20180301141019698-644310256.png)

left就是左边界距离视窗左边距的位置，right就是右边界距离视窗右边距的位置，top，bottom同理。top和bottom或者left和right同时存在，只有top或者left生效。

小广告效果：

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>position</title>
        <style>
            * {
                padding: 0;
                margin: 0;
            }
            .fixed {
                position: fixed;
                right: 0px;
                bottom: 50px;
                width: 200px;
                height: 100px;
                background: yellow;
            }
        </style>
    </head>
    <body>
        <div class="fixed">
        </div>
        <p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p>
        <p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p>
        <p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p>
        <p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p>
        <p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p><p>...</p>
    </body>
</html>
```

## 3. relative

生成相对定位的元素，相对于其正常位置进行定位。

相对元素正常应该在的位置移动，元素所占的空间位置不变，但是显示的位置发生偏移。

left是向右偏移，right是向左偏移（这么说好奇怪，但是…应该没错吧……），top向下偏移，bottom向上偏移。

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>position</title>
        <style>
            * {
                padding: 0;
                margin: 0;
            }
            .div {
                width: 100px;
                height: 100px;
                border: 1px solid;
                float: left;
            }
            .relative1 {
                position: relative;
                left: 10px;
                top: 10px;
                border-color: red;
            }
            .relative2 {
                position: relative;
                right: 10px;
                bottom: 10px;
                border-color: green;
            }
        </style>
    </head>
    <body>
        <div class="div"></div>
        <div class="div relative1"></div>
        <div class="div"></div>
        <div class="div relative2"></div>
    </body>
</html>
```

![img](https://images2018.cnblogs.com/blog/670878/201803/670878-20180301145327838-51205356.png)

##  4. absolute 

生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。(如果没有这样的父元素呢？emmm……应该是整个文档最外层的框……吧……

元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。

absolute元素会附带 display:block 效果，同时，设置absolute后，元素会脱离文档流。

1. 如果不设置"left", "top", "right" 以及 "bottom"，会显示在正常应该显示的位置。

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>position</title>
        <style>
            .relative {
                width: 300px;
                height: 300px;
                border: 1px solid;
                position: relative;
                left: 100px;
                top: 100px;
            }
            .absolute {
                position: absolute;
                width: 100px;
                height: 100px;
                border: 1px solid red;
            }
        </style>
    </head>
    <body>
        <div class="relative">
            outer
            <div class="absolute"></div>
        </div>
    </body>
</html>
```

![img](https://images2018.cnblogs.com/blog/670878/201803/670878-20180301160725057-1706791347.png)

2. 设置了偏移……（一下父元素全部指 static 定位以外的第一个父元素

设置了left 将以父元素的左边界为基准 向右偏移 垂直方向和之前相同。

设置了right 将以父元素的右边界为基准 向左偏移 垂直方向和之前相同。

设置了top 将以父元素的上边界为基准 向下偏移 水平方向和之前相同。

设置了bottom 将以父元素的下边界为基准 向上偏移 水平方向和之前相同。

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>position</title>
        <style>
            .relative {
                width: 300px;
                height: 300px;
                border: 1px solid;
                position: relative;
                left: 100px;
                top: 100px;
            }
            .absolute1, .absolute2, .absolute3, .absolute4, .absolute5 {
                position: absolute;
                width: 100px;
                height: 100px;
                border: 2px solid red;
            }
            .absolute2 {
                left: 60px;
                border-color: green;
            }
            .absolute3 {
                right: 10px;
                border-color: yellow;
            }
            .absolute4 {
                top: 0;
                border-color: pink;
            }
            .absolute5 {
                bottom: 10px;
                border-color: blue;
            }
        </style>
    </head>
    <body>
        <div class="relative">
            outer
            <div class="absolute1"></div>
            <div class="absolute2"></div>
            <div class="absolute3"></div>
            <div class="absolute4"></div>
            <div class="absolute5"></div>
        </div>
    </body>
</html>
```

![img](https://images2018.cnblogs.com/blog/670878/201803/670878-20180301162703536-1368636423.png)

## 5. others

inherit：继承父元素的值。

initial： 初始值，即默认值，static。

unset： 非继承属性，相当于initial，static。

## 6. navigation bar 

效果见右下角

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>position</title>
        <style>
            .right-nav * {
                margin: 0;
                padding: 0;
            }
            .right-nav {
                position: fixed;
                right: 10px;
                bottom: 150px;
            }
            .right-nav li {
                list-style-type: none !important;
                position: relative;
            }
            .right-nav li a {
                text-decoration: none;
                display: block;
                width: 150px;
                line-height: 30px;
                text-align: center;
                background: #666;
                border-bottom: 1px solid;
                color: #fff;
            }
            .right-nav li a:hover, .right-nav li a:active  {
                background: #ccc;
            }
            .right-nav .summary {
                position: absolute;
                right: 150px;
                top: 0px;
                line-height: 30px;
                height: 30px;
                width: 400px;
                background: #ccc;
                color: #fff;
                text-align: center;
                display: none;
            }
            .right-nav li:hover .summary {
                display: block;
                font-size: 12px;
                border-right: 1px solid #fff;
            }
        </style>
    </head>
    <body>
        <div class="right-nav">
            <ul>
                <li><a href="http://www.cnblogs.com/wenruo/p/8488254.html#static">1. static</a>
                    <div class="summary">默认值。没有定位，元素出现在正常的流中。</div>
                </li>
                <li><a href="http://www.cnblogs.com/wenruo/p/8488254.html#fixed">2. fixed</a>
                    <div class="summary">生成绝对定位的元素，相对于浏览器窗口进行定位。</div>
                </li>
                <li><a href="http://www.cnblogs.com/wenruo/p/8488254.html#relative">3. relative</a>
                    <div class="summary">生成相对定位的元素，相对于其正常位置进行定位。</div>
                </li>
                <li><a href="http://www.cnblogs.com/wenruo/p/8488254.html#absolute">4. absolute</a>
                    <div class="summary">生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。</div>
                </li>
                <li><a href="http://www.cnblogs.com/wenruo/p/8488254.html#others">5. others</a>
                    <div class="summary">inherit & initial & unset</div>
                </li>
                <li><a href="http://www.cnblogs.com/wenruo/p/8488254.html#navigation-bar">6. navigation bar</a>
                    <div class="summary">根据定位写一个导航栏。</div>
                </li>
            </ul>
        </div>
    </body>
</html>
```

 

# 5.flex 布局

网页布局（layout）是 CSS 的一个重点应用。

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071001.gif)

布局的传统解决方案，基于[盒状模型](https://developer.mozilla.org/en-US/docs/Web/CSS/box_model)，依赖 [`display`](https://developer.mozilla.org/en-US/docs/Web/CSS/display) 属性 + [`position`](https://developer.mozilla.org/en-US/docs/Web/CSS/position)属性 + [`float`](https://developer.mozilla.org/en-US/docs/Web/CSS/float)属性。它对于那些特殊布局非常不方便，比如，[垂直居中](https://css-tricks.com/centering-css-complete-guide/)就不容易实现。

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071002.png)

2009年，W3C 提出了一种新的方案----Flex 布局，可以简便、完整、响应式地实现各种页面布局。目前，它已经得到了所有浏览器的支持，这意味着，现在就能很安全地使用这项功能。

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071003.jpg)

Flex 布局将成为未来布局的首选方案。本文介绍它的语法，[下一篇文章](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)给出常见布局的 Flex 写法。网友 [JailBreak](http://vgee.cn/) 为本文的所有示例制作了 [Demo](http://static.vgee.cn/static/index.html)，也可以参考。

以下内容主要参考了下面两篇文章：[A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) 和 [A Visual Guide to CSS3 Flexbox Properties](https://scotch.io/tutorials/a-visual-guide-to-css3-flexbox-properties)。

## 一、Flex 布局是什么？

Flex 是 Flexible Box 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。

任何一个容器都可以指定为 Flex 布局。

> ```css
> .box{
>   	display: flex;
> }
> ```

行内元素也可以使用 Flex 布局。

> ```css
> .box{
>   	display: inline-flex;
> }
> ```

Webkit 内核的浏览器，必须加上`-webkit`前缀。

> ```css
> .box{
>       display: -webkit-flex; /* Safari */
>       display: flex;
> }
> ```

注意，设为 Flex 布局以后，子元素的`float`、`clear`和`vertical-align`属性将失效。

## 二、基本概念

采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071004.png)

容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做`main start`，结束位置叫做`main end`；交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`。

项目默认沿主轴排列。单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`。

## 三、容器的属性

以下6个属性设置在容器上。

> - flex-direction
> - flex-wrap
> - flex-flow
> - justify-content
> - align-items
> - align-content

### 3.1 flex-direction属性

`flex-direction`属性决定主轴的方向（即项目的排列方向）。

> ```css
> .box {
>   	flex-direction: row | row-reverse | column | column-reverse;
> }
> ```

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071005.png)

它可能有4个值。

> - `row`（默认值）：主轴为水平方向，起点在左端。
> - `row-reverse`：主轴为水平方向，起点在右端。
> - `column`：主轴为垂直方向，起点在上沿。
> - `column-reverse`：主轴为垂直方向，起点在下沿。

### 3.2 flex-wrap属性

默认情况下，项目都排在一条线（又称"轴线"）上。`flex-wrap`属性定义，如果一条轴线排不下，如何换行。

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071006.png)

> ```css
> .box{
>   	flex-wrap: nowrap | wrap | wrap-reverse;
> }
> ```

它可能取三个值。

（1）`nowrap`（默认）：不换行。

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071007.png)

（2）`wrap`：换行，第一行在上方。

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071008.jpg)

（3）`wrap-reverse`：换行，第一行在下方。

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071009.jpg)

### 3.3 flex-flow

`flex-flow`属性是`flex-direction`属性和`flex-wrap`属性的简写形式，默认值为`row nowrap`。

> ```css
> .box {
>   	flex-flow: <flex-direction> || <flex-wrap>;
> }
> ```

### 3.4 justify-content属性

`justify-content`属性定义了项目在主轴上的对齐方式。

> ```css
> .box {
>   	justify-content: flex-start | flex-end | center | space-between | space-around;
> }
> ```

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071010.png)

它可能取5个值，具体对齐方式与轴的方向有关。下面假设主轴为从左到右。

> - `flex-start`（默认值）：左对齐
> - `flex-end`：右对齐
> - `center`： 居中
> - `space-between`：两端对齐，项目之间的间隔都相等。
> - `space-around`：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

### 3.5 align-items属性

`align-items`属性定义项目在交叉轴上如何对齐。

> ```css
> .box {
>   	align-items: flex-start | flex-end | center | baseline | stretch;
> }
> ```

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071011.png)

它可能取5个值。具体的对齐方式与交叉轴的方向有关，下面假设交叉轴从上到下。

> - `flex-start`：交叉轴的起点对齐。
> - `flex-end`：交叉轴的终点对齐。
> - `center`：交叉轴的中点对齐。
> - `baseline`: 项目的第一行文字的基线对齐。
> - `stretch`（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。

### 3.6 align-content属性

`align-content`属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

> ```css
> .box {
>   	align-content: flex-start | flex-end | center | space-between | space-around | stretch;
> }
> ```

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071012.png)

该属性可能取6个值。

> - `flex-start`：与交叉轴的起点对齐。
> - `flex-end`：与交叉轴的终点对齐。
> - `center`：与交叉轴的中点对齐。
> - `space-between`：与交叉轴两端对齐，轴线之间的间隔平均分布。
> - `space-around`：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
> - `stretch`（默认值）：轴线占满整个交叉轴。

### 四、项目的属性

以下6个属性设置在项目上。

> - `order`
> - `flex-grow`
> - `flex-shrink`
> - `flex-basis`
> - `flex`
> - `align-self`

### 4.1 order属性

`order`属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。

> ```css
> .item {
>   	order: <integer>;
> }
> ```

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071013.png)

### 4.2 flex-grow属性

`flex-grow`属性定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大。

> ```css
> .item {
>   	flex-grow: <number>; /* default 0 */
> }
> ```

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071014.png)

如果所有项目的`flex-grow`属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的`flex-grow`属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

### 4.3 flex-shrink属性

`flex-shrink`属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。

> ```css
> .item {
>   	flex-shrink: <number>; /* default 1 */
> }
> ```

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071015.jpg)

如果所有项目的`flex-shrink`属性都为1，当空间不足时，都将等比例缩小。如果一个项目的`flex-shrink`属性为0，其他项目都为1，则空间不足时，前者不缩小。

负值对该属性无效。

### 4.4 flex-basis属性

`flex-basis`属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。

> ```css
> .item {
>   	flex-basis: <length> | auto; /* default auto */
> }
> ```

它可以设为跟`width`或`height`属性一样的值（比如350px），则项目将占据固定空间。

### 4.5 flex属性

`flex`属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。

> ```css
> .item {
>   	flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
> }
> ```

该属性有两个快捷值：`auto` (`1 1 auto`) 和 none (`0 0 auto`)。

建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。

### 4.6 align-self属性

`align-self`属性允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。

> ```css
> .item {
>   	align-self: auto | flex-start | flex-end | center | baseline | stretch;
> }
> ```

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071016.png)

该属性可能取6个值，除了auto，其他都与align-items属性完全一致。

# 6.css样式优先级

## 定义

1. 权重决定了你css规则怎样被浏览器解析直到生效。“css权重关系到你的css规则是怎样显示的”。
2. 当很多的样式被应用到某一个元素上时，权重是一个决定哪种样式生效，或者是优先级的过程。
3. 每个选择器都有自己的权重。你的每条css规则，都包含一个权重级别。 这个级别是由不同的选择器加权计算的，通过权重，不同的样式最终会作用到你的网页中 。
4. 如果两个选择器同时作用到一个元素上，权重高者生效。

## **权重记忆口诀**：

*从0开始，一个行内样式+1000，一个id选择器+100，一个属性选择器、class或者伪类+10，一个元素选择器，或者伪元素+1，通配符+0。*

![img](https://pic3.zhimg.com/80/v2-b1a9fedf320754acb1d7766c6548d5f6_720w.jpg)css权重值记忆图

接下来增加一下记忆，下面是我瞎写的一个样式，只看选择器那里就可以了，具体样式请忽略，我分别用了id选择器、class选择器和标签选择器各一次。

![img](https://pic3.zhimg.com/80/v2-f27a0588280e867a3bc3ed17c7643cf6_720w.jpg)



## **样式重复多写情况**

在css样式表中，同一个CSS样式你写了两次，后面的会覆盖前面的，在开发中基本不会使用。

```css
#box {
     background-color: green;
}
/* 这条生效 */
#box {
     background-color: blue;
}
```

## **不同的权重，权重值高则生效**

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>权重高的样式生效</title>
		<style>
			/* 权重值：1 */
			div{
				width: 100px;
				height: 100px;
				background-color: red;
			}
 
			/* 权重值：10 */
			.box2{
				width: 100px;
				height: 100px;
				background-color: yellow;
			}
 
			/* 权重值：100 */
			#box{
				width: 100px;
				height: 100px;
				background-color: green;
			}
		</style>
	</head>
	<body>
		<div id='box' class='box2'></div>
	</body>
</html>
```

![img](https://pic1.zhimg.com/80/v2-4428f2f2ad22f0541b5c49ec42f261c4_720w.jpg)id的权重高，所以id选择器中的样式生效

从上面的例子不难看出，id选择器的权重值高于其它2种选择器的权重值，所以id选择器中的样式生效了。

## **!important(提升样式优先级)**

!important的作用是提升样式优先级，如果加了这句的样式的优先级是最高的。不过我这里***建议大家一下，!important最好不要使用。\***

```css
<style>
    div{
        background: blue !important;
    }
    #box{
        background-color: green;
    }
</style>
</head>
    <body>
    	<div id="box" style="background-color: red;width: 100px;height: 100px;"></div>
    </body>
```

![img](https://pic1.zhimg.com/80/v2-dffaab8e727068ed415d2e93f11cab30_720w.jpg)!important 优先级是最高的，不建议使用



## **两种样式都使用!important时**

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>当两个样式都使用!important时</title>
		<style>
			.box{
				width: 100px;
				height: 100px;
				background-color: red !important;
			}
 
			div{
				width: 100px;
				height: 100px;
				background-color: green !important;
			}
		</style>
	</head>
	<body>
		<!-- 当两个样式都使用!important时，权重值大的优先级更高 -->
		<div class='box'></div>
	</body>
</html>
```

![img](https://pic1.zhimg.com/80/v2-78a61330c654eb406693bcbea09fc728_720w.jpg)当两个样式都使用!important时，权重值大的优先级更高



## **!important应用于简写样式**

如果!important被用于一个简写的样式属性，那么这条简写的样式属性所代表的子属性都会被作用上!important。

例如：*background: blue !important;*

![img](https://pic1.zhimg.com/80/v2-7d88d39b30972d1b20ef5ba2e96d46d4_720w.jpg)简单写的样式如果使用!important，子属性也会默认加上important

上述结果可以看出，background的子属性都加上了!important，到这里，我提醒一下开发者们，这种复合性样式不建议大量使用，如果里面的属性大多数是可以用到的，还是可以写复合性样式的。我经常看到一些开发都，给某一元素加上颜色，经常性的写成这样

```css
background: red;
```

这个样式从表面来说，和background-color:red;一样可以实现效果。

这时你可以通过浏览器的调试工具来查看它具体的样式：

![img](https://pic3.zhimg.com/80/v2-891cb936a5d21fd220732e8d01908bb6_720w.jpg)goolge下的效果图

使用复合写法的时候，它不光只加载了背景颜色样式，还加载了其它一些样式。可想而知，如果一个项目的前台全部都采用复合写法的方式，第设置一个background样式时background相关的样式都会被加载进去，这样性能一定非常差，这也是一种不合理的设计方 案，而单例写法却看不到这种情况。 

**行内、内联和外联样式优先级**

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title>css优先级</title>
		<link rel='stylesheet' href='css/styles.css'>
		<style>
	        div{
	        	background-color: blue;
	        }
	        #box{
	            background-color: green;
	        }
		</style>
	</head>
	<body>
		<!-- 行内样式生效 -->
		<div id="box" style="background-color: red;width: 100px;height: 100px;"></div>
	</body>
</html>
```

![img](https://pic4.zhimg.com/80/v2-ca7b3c40edc41399cf0224c91137551b_720w.jpg)效果图

根据权重值来计算，行内样式的权重值最大，所以行内样式生效了。



**内联和外联样式优先级**

这里我曾经一直以为内联样式的优先级一定大于外联样式的，直到最近的几天我才发现我一直都学错了，所以这里也是给大家提个醒，希望自己也牢记这个教训。

```css
/* styles.css */
#box{
	background-color: yellow;
}
#div{
	width: 500px;
	height: 500px;
	background-color: pink;
}
```

------

1.外联样式写前面，内联样式写后面

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>内联和外联样式的优先级问题</title>
		<link rel='stylesheet' href='css/styles.css'>
		<style>
			#div{
				width: 200px;
				height: 200px;
				background-color: yellow;
			}
		</style>
	</head>
	<body>
		<!-- 内联样式生效 -->
		<div id='div'></div>
	</body>
</html>
```

![img](https://pic4.zhimg.com/80/v2-5558422e14755b5dc2ce0eb0f2ae6ec7_720w.jpg)内联样式生效

2.内联样式写前面，外联样式写后面

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>内联和外联样式的优先级问题</title>
		<style>
			#div{
				width: 200px;
				height: 200px;
				background-color: yellow;
			}
		</style>
		<link rel='stylesheet' href='css/styles.css'>
	</head>
	<body>
		<!-- 内联样式生效 -->
		<div id='div'></div>
	</body>
</html>
```

![img](https://pic4.zhimg.com/80/v2-3350f183180f050442665e45f69971cb_720w.jpg)外联样式生效

上面的例子足以说明内联样式的优先级并不一定比外联样式高，因为css样式是单线程，依次从上向下加载的，这也就证明了***内联样式和外联样式的优先级和加载顺序有关\***。

总结一下：***!important > 行内样式 > 内联样式 and 外联样式***

![img](https://pic1.zhimg.com/80/v2-bb8fc596d248f2c30bae77b565fded84_720w.jpg)样式优先级



**样式应用于非目标标签时**

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>样式应用于非目标标签时</title>
		<style>
			div p{color: red};
			#box{color: blue}
		</style>
	</head>
	<body>
		<!-- 选中非目标元素的情况下，离目标越近者优先 -->
		<div id="box">
		  <p>
		    <span>神来之笔</span>
		  </p>
		</div>
	</body>
</html>
```

![img](https://pic4.zhimg.com/80/v2-a2eed077c5ad66f7de820538fb585217_720w.jpg)效果图



**权重相等的情况下**

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>权重相等的情况下</title>
		<style>
			/* 权重值：201 */
			#box #box2 p{
				width: 200px;
				height: 200px;
				background-color: red;
			}
			/* 权重值：201,离目标最近 */
			#box #box3 p{
				width: 200px;
				height: 200px;
				background-color: yellow;
			}
		</style>
	</head>
	<body>
		<!-- 同等权重下,靠近目标的优先 -->
		<div id="box" class="boxs">
			<div id="box2" class="boxs2">
		    	        <div id="box3" class="boxs3">
		      		        <p></p>
		    	        </div>
		 	</div>
		</div>
	</body>
</html>
```

![img](https://pic4.zhimg.com/80/v2-e5bed8244d8296206d76cec0f825dacf_720w.jpg)同等权重下,靠近目标的优先



**总结**

1. 常用选择器权重优先级：***!important > id > class > tag\***
2. !important可以提升样式优先级，但不建议使用。如果!important被用于一个简写的样式属性，那么这条简写的样式属性所代表的子属性都会被应用上!important。 例如：*background: blue !important;*
3. 如果两条样式都使用!important，则权重值高的优先级更高
4. 在css样式表中，同一个CSS样式你写了两次，后面的会覆盖前面的
5. 样式指向同一元素，权重规则生效，权重大的被应用
6. 样式指向同一元素，权重规则生效，权重相同时，就近原则生效，后面定义的被应用
7. 样式不指向同一元素时，权重规则失效，就近原则生效，离目标元素最近的样式被应用

# 7.圣杯布局和双飞翼布局

## 前言

如下图：



![img](https://user-gold-cdn.xitu.io/2019/4/11/16a0c9554c172710?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## 字面意思

圣杯布局和双飞翼布局从字面意思来看是这样的：

> 一个像圣杯或者像展翅的禽类这样的布局

**通俗的来说就是左右两栏固定宽度，中间部分自适应的三栏布局。**

### 两者本质



![img](https://user-gold-cdn.xitu.io/2019/9/5/16cfefeb90d9ec0b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



### 圣杯布局

1. 首先把left、middle、right都放出来
2. 给它们三个设置上float: left, 脱离文档流；
3. 一定记得给container设置上overflow: hidden; 可以形成BFC撑开文档
4. left、right设置上各自的宽度
5. middle设置width: 100%;

**接下来比较重要了：**

1. 给left、middle、right设置position: relative;
2. left设置 left: -leftWidth, right设置 right: -rightWidth;
3. container设置padding: 0, rightWidth, 0, leftWidth;

------

我注意到圣杯布局的left、middle、right都有position: relative;

设:

```
.left width:200px
.right width:220px
```

那么下面的这些属性为什么要存在？

```
.container上面的paddind
.left 的left: -200px;
.right 的right: -220px;
```

**因为不这样设置  会遮挡middle的内容**

可以自己尝试一下下

------

圣杯布局示例代码如下：

```
<!DOCTYPE html>
<html lang="zh">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>圣杯布局</title>
  <style>
    * {
      margin: 0;
      padding: 0;
    }

    .header,
    .footer {
      height: 100px;
      line-height: 100px;
      background-color: green;
      text-align: center;
      font-size: 30px;
      font-weight: bolder;
    }

    .footer {
      background-color: goldenrod;
    }

    .container {
      padding: 0 220px 0 200px;
      overflow: hidden;
    }

    .left,
    .middle,
    .right {
      position: relative;
      float: left;
      min-height: 130px;
      word-break: break-all;
    }

    .left {
      margin-left: -100%;
      left: -200px;
      width: 200px;
      background-color: red;
    }

    .right {
      margin-left: -220px;
      right: -220px;
      width: 220px;
      background-color: green;
    }

    .middle {
      width: 100%;
      background-color: blue;
    }
  </style>
</head>

<body>
  <div class="header">header</div>
  <div class="container">
    <div class="middle">
      <h4>middle</h4>
      <p>
        middlemiddlemiddlemiddlemiddlemiddlemiddlemiddle
        middlemiddlemiddlemiddlemiddlemiddlemiddlemiddle
        middlemiddlemiddlemiddlemiddlemiddlemiddlemiddle
        middlemiddlemiddlemiddlemiddlemiddlemiddlemiddle
        middlemiddlemiddlemiddlemiddlemiddlemiddlemiddle
        middlemiddlemiddlemiddlemiddle
      </p>
    </div>
    <div class="left">
      <h4>left</h4>
      <p>
        leftleftleftleftleftleftleftleftleftleftleftleft
        leftleftleftleftleftleftleftleftleftleftleftleft
        leftleftleftleftleftleftleftleftleftleftleftleft
      </p>
    </div>
    <div class="right">
      <h4>right</h4>
      <p>
        rightrightrightrightrightrightrightrightrightright
        rightrightrightrightrightrightrightrightrightright
        rightrightrightrightrightrightright
      </p>
    </div>
  </div>
  <div class="footer">footer</div>
</body>

</html>
```

------

### 双飞翼布局

双飞翼布局和圣杯布局很类似，不过是在middle的div里又插入一个div，通过调整内部div的margin值，实现中间栏自适应，内容写到内部div中。

这样可以先做好主体部分，然后再将附属部分放到合适的位置！

1. 首先把left、middle、right都放出来, middle中增加inner
2. 给它们三个设置上float: left, 脱离文档流；
3. 一定记得给container设置上overflow: hidden; 可以形成BFC撑开文档
4. left、right设置上各自的宽度
5. middle设置width: 100%;

**接下来与圣杯布局不一样的地方：**

1. left设置 margin-left: -100%, right设置 right: -rightWidth;
2. container设置padding: 0, rightWidth, 0, leftWidth;

```
<!DOCTYPE html>
<html lang="zh">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>双飞翼布局</title>
  <style>
    * {
      margin: 0;
      padding: 0;
    }

    .header,
    .footer {
      height: 100px;
      line-height: 100px;
      background-color: green;
      text-align: center;
      font-size: 30px;
      font-weight: bolder;
    }

    .footer {
      background-color: goldenrod;
    }

    .container {
      overflow: hidden;
    }

    .left,
    .middle,
    .right {
      float: left;
      min-height: 130px;
      word-break: break-all;
    }

    .left {
      margin-left: -100%;
      width: 200px;
      background-color: red;
    }

    .right {
      margin-left: -220px;
      width: 220px;
      background-color: green;
    }

    .middle {
      width: 100%;
      height: 100%;
      background-color: blue;
    }

    .inner {
      margin: 0 220px 0 200px;
      min-height: 130px;
      background: blue;
      word-break: break-all;
    }
  </style>
</head>

<body>
  <div class="header">header</div>
  <div class="container">
    <div class="middle">
      <div class="inner">
        <h4>middle</h4>
        <p>
          middlemiddlemiddlemiddlemiddlemiddlemiddlemiddle
          middlemiddlemiddlemiddlemiddlemiddlemiddlemiddle
          middlemiddlemiddlemiddlemiddlemiddlemiddlemiddle
          middlemiddlemiddlemiddlemiddlemiddlemiddlemiddle
          middlemiddlemiddlemiddlemiddlemiddlemiddlemiddle
          middlemiddlemiddlemiddlemiddle
        </p>
      </div>
    </div>
    <div class="left">
      <h4>left</h4>
      <p>
        leftleftleftleftleftleftleftleftleftleftleftleft
        leftleftleftleftleftleftleftleftleftleftleftleft
        leftleftleftleftleftleftleftleftleftleftleftleft
      </p>
    </div>
    <div class="right">
      <h4>right</h4>
      <p>
        rightrightrightrightrightrightrightrightrightright
        rightrightrightrightrightrightrightrightrightright
        rightrightrightrightrightrightright
      </p>
    </div>
  </div>
  <div class="footer">footer</div>
</body>

</html>
```

### 总结

圣杯布局在DOM结构上显得更加直观和自然；

双飞翼布局省去了很多css，而且由于不用使用定位，可以获得比圣杯布局更小最小宽度；

说到这里需要注意一下  由于双飞翼布局会一直随着浏览器可视区域宽度减小从而不断挤压中间部分宽度。

所以需要设置给页面一个min-width > LeftWidth + RightWidth；

------

还有一件事就是他们在单独部分内容扩充的时候，童鞋们可能发现了 底部会参差不齐。

在我的老师那里知道了最简单的解决办法 / 笑哭

> 给left、middle、right设置上 padding-bottom: 9999px; margin-bottom: -9999px;

就让他变得无限高，但是又给他送回去了。真的是让我脑洞大开！！！

# 8.CSS3新特性

## 1.前言

css3这个相信大家不陌生了，是个非常有趣，神奇的东西！有了css3，js都可以少写很多！我之前也写过关于css3的文章，也封装过css3的一些小动画。个人觉得css3不难，但是很难用得好，用得顺手，最近我也在过一遍css3的一些新特性（不是全部，是我在工作上常用的，或者觉得有用的），以及一些实例，就写了这一篇总结！希望，这篇文章能帮到大家认识css3。写这篇文章主要是让大家能了解css3的一些新特性，以及基础的用法，感觉css3的魅力！如果想要用好css3，这个得靠大家继续努力学习，寻找一些讲得更深入的文章或者书籍了！如果大家有什么其他特性推荐的，欢迎补充！大家一起学习，进步！

> 看这篇文章，代码可以不用看得过于仔细！这里主要是想让大家了解css3的新特性！代码也是很基础的用法。我给出代码主要是让大家在浏览器运行一下，让大家参考和调试。不要只看代码，只看代码的话，不会知道哪个代码有什么作用的，建议边看效果边看代码。

## 2.过渡

过渡，是我在项目里面用得最多的一个特性了！也相信是很多人用得最多的一个例子！我平常使用就是想让一些交互效果（主要是hover动画），变得生动一些，不会显得那么生硬！好了，下面进入正文！

引用菜鸟教程的说法：CSS3 过渡是元素从一种样式逐渐改变为另一种的效果。要实现这一点，必须规定两项内容：指定要添加效果的CSS属性指定效果的持续时间。

### 2-1语法

```
transition： CSS属性，花费时间，效果曲线(默认ease)，延迟时间(默认0)
```

例子1

```
/*宽度从原始值到制定值的一个过渡，运动曲线ease,运动时间0.5秒，0.2秒后执行过渡*/
transition：width,.5s,ease,.2s
```

例子2

```
/*所有属性从原始值到制定值的一个过渡，运动曲线ease,运动时间0.5秒*/
transition：all,.5s
```

上面例子是简写模式，也可以分开写各个属性（这个在下面就不再重复了）

```
transition-property: width;
transition-duration: 1s;
transition-timing-function: linear;
transition-delay: 2s;
```

### 2-2实例-hover效果

![clipboard.png](https://segmentfault.com/img/bVTbJs?w=467&h=139)

上面两个按钮，第一个使用了过渡，第二个没有使用过渡，大家可以看到当中的区别，用了过渡之后是不是没有那么生硬，有一个变化的过程，显得比较生动。
当然这只是一个最简单的过渡例子，两个按钮的样式代码，唯一的区别就是，第一个按钮加了过渡代码`transition: all .5s;`

### 2-3实例-下拉菜单

![clipboard.png](https://segmentfault.com/img/bVTbLy?w=592&h=570)

上面两个菜单，第一个没有使用过渡，第二个使用过渡，大家明显看到区别，使用了过渡看起来也是比较舒服！代码区别就是有过渡的ul的上级元素(祖先元素)有一个类名（ul-transition）。利用这个类名，设置ul的过渡`.ul-transition ul{transform-origin: 0 0;transition: all .5s;}`

可能大家不知道我在说什么！我贴下代码吧

html

```
<div class="demo-hover demo-ul t_c">
    <ul class="fllil">
        <li>
            <a href="javascript:;">html</a>
            <ul>
                <li><a href="#">div</a></li>
                <li><a href="#">h1</a></li>
            </ul>
        </li>
        <li>
            <a href="javascript:;">js</a>
            <ul>
                <li><a href="#">string</a></li>
                <li><a href="#">array</a></li>
                <li><a href="#">object</a></li>
                <li><a href="#">number</a></li>
            </ul>
        </li>
        <li>
            <a href="javascript:;">css3</a>
            <ul>
                <li><a href="#">transition</a></li>
                <li><a href="#">animation</a></li>
            </ul>
        </li>
        <li>
            <a href="javascript:;">框架</a>
            <ul>
                <li><a href="#">vue</a></li>
                <li><a href="#">react</a></li>
            </ul>
        </li>
    </ul>
    <div class="clear"></div>
</div>
<div class="demo-hover demo-ul ul-transition t_c">
    <ul class="fllil">
        <li>
            <a href="javascript:;">html</a>
            <ul>
                <li><a href="#">div</a></li>
                <li><a href="#">h1</a></li>
            </ul>
        </li>
        <li>
            <a href="javascript:;">js</a>
            <ul>
                <li><a href="#">string</a></li>
                <li><a href="#">array</a></li>
                <li><a href="#">object</a></li>
                <li><a href="#">number</a></li>
            </ul>
        </li>
        <li>
            <a href="javascript:;">css3</a>
            <ul>
                <li><a href="#">transition</a></li>
                <li><a href="#">animation</a></li>
            </ul>
        </li>
        <li>
            <a href="javascript:;">框架</a>
            <ul>
                <li><a href="#">vue</a></li>
                <li><a href="#">react</a></li>
            </ul>
        </li>
    </ul>
    <div class="clear"></div>
</div>
```

css

```
.demo-ul{margin-bottom: 300px;}
    .demo-ul li{
        padding: 0 10px;
        width: 100px;
        background: #f90;
        position: relative;
    }
    .demo-ul li a{
        display: block;
        height: 40px;
        line-height: 40px;
        text-align: center;
    }
    .demo-ul li ul{
        position: absolute;
        width: 100%;
        top: 40px;
        left: 0;
        transform: scaleY(0);
        overflow: hidden;
    }
    .ul-transition ul{
        transform-origin: 0 0;
        transition: all .5s;
    }
    .demo-ul li:hover ul{
        transform: scaleY(1);
    }
    .demo-ul li ul li{
        float: none;
        background: #0099ff;

}
```

上面两个可以说是过渡很基础的用法，过渡用法灵活，功能也强大，结合js，可以很轻松实现各种效果（焦点图，手风琴）等，以及很多意想不到的效果。这个靠大家要去挖掘！

## 3.动画

动画这个平常用的也很多，主要是做一个预设的动画。和一些页面交互的动画效果，结果和过渡应该一样，让页面不会那么生硬！

### 3-1.语法

```
animation：动画名称，一个周期花费时间，运动曲线（默认ease），动画延迟（默认0），播放次数（默认1），是否反向播放动画（默认normal），是否暂停动画（默认running）
```

例子1

```
/*执行一次logo2-line动画，运动时间2秒，运动曲线为 linear*/
animation: logo2-line 2s linear;
```

例子2

```
/*2秒后开始执行一次logo2-line动画，运动时间2秒，运动曲线为 linear*/
animation: logo2-line 2s linear 2s;
```

例子3

```
/*无限执行logo2-line动画，每次运动时间2秒，运动曲线为 linear，并且执行反向动画*/
animation: logo2-line 2s linear alternate infinite;
```

还有一个重要属性

```
animation-fill-mode : none | forwards | backwards | both;
/*none：不改变默认行为。    
forwards ：当动画完成后，保持最后一个属性值（在最后一个关键帧中定义）。    
backwards：在 animation-delay 所指定的一段时间内，在动画显示之前，应用开始属性值（在第一个关键帧中定义）。 
both：向前和向后填充模式都被应用。  */      
```

### 3-2.logo展示动画

![clipboard.png](https://segmentfault.com/img/bVTdn3?w=776&h=220)

这个是我用公司logo写的动画，没那么精细

代码如下

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="reset.css">
</head>
<style>
.logo-box{
    width: 600px;
    margin: 100px auto;
    font-size: 0;
    position: relative;
}
.logo-box div{
    display: inline-block;
}
.logo-box .logo-text{
    margin-left: 10px;
}
.logo-box .logo1{
    animation: logo1 1s ease-in 2s;
    animation-fill-mode:backwards;
}
.logo-box .logo-text{
    animation: logoText 1s ease-in 3s;
    animation-fill-mode:backwards;
}
.logo-box .logo2{
    position: absolute;
    top: 20px;
    left: 20px;
    animation: logo2-middle 2s ease-in;
}
.logo-box .logo2 img{
    animation: logo2-line 2s linear;
}
@keyframes logo1 {
    0%{
        transform:rotate(180deg);
        opacity: 0;
    }
    100%{
        transform:rotate(0deg);
        opacity: 1;
    }
}
@keyframes logoText {
    0%{
        transform:translateX(30px);
        opacity: 0;
    }
    100%{
        transform:translateX(0);
        opacity: 1;
    }
}
@keyframes logo2-line {
    0% { transform: translateX(200px)}
    25% { transform: translateX(150px)}
    50% { transform: translateX(100px)}
    75% { transform: translateX(50px)}
    100% { transform: translateX(0); }
}

@keyframes logo2-middle {
    0% { transform: translateY(0);     }
    25% { transform: translateY(-100px);     }
    50% { transform: translateY(0);     }
    75% { transform: translateY(-50px);     }
    100% { transform: translateY(0); }
}
</style>
<body>
<div class="logo-box">
<div class="logo1"><img src="logo1.jpg"/></div>
<div class="logo2"><img src="logo2.jpg"/></div>
<div class="logo-text"><img src="logo3.jpg"/></div>
</div>

<div class="wraper"><div class="item"></div></div>

</body>
</html>
```

下面让大家看一个专业级别的

![clipboard.png](https://segmentfault.com/img/bVTdpk?w=734&h=214)

代码如下

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<style>
    body {
        font-family: Arial,"Helvetica Neue",Helvetica,sans-serif;
        overflow: hidden;
        background: #fff;
    }

    .center {
        margin: 80px auto;
    }

    .so {
        display: block;
        width: 500px;
        height: 156px;
        background: #ffffff;
    }
    .so .inner {
        width: 500px;
        height: 156px;
        position: absolute;
    }
    .so .inner * {
        position: absolute;
        animation-iteration-count: infinite;
        animation-duration: 3.5s;
    }
    .so .inner .name {
        position: absolute;
        font-size: 54px;
        left: 130px;
        top: 95px;
    }
    .so .inner .name .b {
        font-weight: bold;
    }
    .so .inner .stack-box {
        top: 100px;
        width: 115px;
        height: 56px;
    }
    .so .inner .box {
        width: 115px;
        height: 56px;
        left: 0px;
    }
    .so .inner .box div {
        background: #BCBBBB;
    }
    .so .inner .box .bottom {
        bottom: 0px;
        left: 0px;
        width: 115px;
        height: 12px;
    }
    .so .inner .box .left {
        bottom: 11px;
        left: 0px;
        width: 12px;
        height: 34px;
    }
    .so .inner .box .right {
        bottom: 11px;
        left: 103px;
        width: 12px;
        height: 34px;
    }
    .so .inner .box .top {
        top: 0px;
        left: 0px;
        width: 0;
        height: 12px;
    }
    .so .inner .stack {
        left: 22px;
        top: 22px;
    }
    .so .inner .stack .inner-item {
        background: #F48024;
        width: 71px;
        height: 12px;
    }
    .so .inner .stack .item {
        transition: transform 0.3s;
        width: 291px;
    }
    .so .inner .stack div:nth-child(1) {
        transform: rotate(0deg);
    }
    .so .inner .stack div:nth-child(2) {
        transform: rotate(12deg);
    }
    .so .inner .stack div:nth-child(3) {
        transform: rotate(24deg);
    }
    .so .inner .stack div:nth-child(4) {
        transform: rotate(36deg);
    }
    .so .inner .stack div:nth-child(5) {
        transform: rotate(48deg);
    }
    .so .inner .box {
        animation-name: box;
    }
    .so .inner .box .top {
        animation-name: box-top;
    }
    .so .inner .box .left {
        animation-name: box-left;
    }
    .so .inner .box .right {
        animation-name: box-right;
    }
    .so .inner .box .bottom {
        animation-name: box-bottom;
    }
    .so .inner .stack-box {
        animation-name: stack-box;
    }
    .so .inner .stack {
        animation-name: stack;
    }
    .so .inner .stack .inner-item {
        animation-name: stack-items;
    }
    .so .inner .stack .item:nth-child(1) {
        animation-name: stack-item-1;
    }
    .so .inner .stack .item:nth-child(2) {
        animation-name: stack-item-2;
    }
    .so .inner .stack .item:nth-child(3) {
        animation-name: stack-item-3;
    }
    .so .inner .stack .item:nth-child(4) {
        animation-name: stack-item-4;
    }
    .so .inner .stack .item:nth-child(5) {
        animation-name: stack-item-5;
    }
    @keyframes stack {
        0% {
            left: 22px;
        }
        15% {
            left: 22px;
        }
        30% {
            left: 52px;
        }
        50% {
            left: 52px;
        }
        80% {
            left: 22px;
        }
    }
    @keyframes stack-item-1 {
        0% {
            transform: rotate(12deg * 0);
        }
        10% {
            transform: rotate(0deg);
        }
        50% {
            transform: rotate(0deg);
        }
        54% {
            transform: rotate(0deg);
        }
        92% {
            transform: rotate(12deg * 0);
        }
    }
    @keyframes stack-item-2 {
        0% {
            transform: rotate(12deg * 1);
        }
        10% {
            transform: rotate(0deg);
        }
        50% {
            transform: rotate(0deg);
        }
        54% {
            transform: rotate(0deg);
        }
        92% {
            transform: rotate(12deg * 1);
        }
    }
    @keyframes stack-item-3 {
        0% {
            transform: rotate(12deg * 2);
        }
        10% {
            transform: rotate(0deg);
        }
        50% {
            transform: rotate(0deg);
        }
        54% {
            transform: rotate(0deg);
        }
        92% {
            transform: rotate(12deg * 2);
        }
    }
    @keyframes stack-item-4 {
        0% {
            transform: rotate(12deg * 3);
        }
        10% {
            transform: rotate(0deg);
        }
        50% {
            transform: rotate(0deg);
        }
        54% {
            transform: rotate(0deg);
        }
        92% {
            transform: rotate(12deg * 3);
        }
    }
    @keyframes stack-item-5 {
        0% {
            transform: rotate(12deg * 4);
        }
        10% {
            transform: rotate(0deg);
        }
        50% {
            transform: rotate(0deg);
        }
        54% {
            transform: rotate(0deg);
        }
        92% {
            transform: rotate(12deg * 4);
        }
    }
    @keyframes stack-items {
        0% {
            width: 71px;
        }
        15% {
            width: 71px;
        }
        30% {
            width: 12px;
        }
        50% {
            width: 12px;
        }
        80% {
            width: 71px;
        }
    }
    @keyframes box {
        0% {
            left: 0;
        }
        15% {
            left: 0;
        }
        30% {
            left: 30px;
        }
        50% {
            left: 30px;
        }
        80% {
            left: 0;
        }
    }
    @keyframes box-top {
        0% {
            width: 0;
        }
        6% {
            width: 0;
        }
        15% {
            width: 115px;
        }
        30% {
            width: 56px;
        }
        50% {
            width: 56px;
        }
        59% {
            width: 0;
        }
    }
    @keyframes box-bottom {
        0% {
            width: 115px;
        }
        15% {
            width: 115px;
        }
        30% {
            width: 56px;
        }
        50% {
            width: 56px;
        }
        80% {
            width: 115px;
        }
    }
    @keyframes box-right {
        15% {
            left: 103px;
        }
        30% {
            left: 44px;
        }
        50% {
            left: 44px;
        }
        80% {
            left: 103px;
        }
    }
    @keyframes stack-box {
        0% {
            transform: rotate(0deg);
        }
        30% {
            transform: rotate(0deg);
        }
        40% {
            transform: rotate(135deg);
        }
        50% {
            transform: rotate(135deg);
        }
        83% {
            transform: rotate(360deg);
        }
        100% {
            transform: rotate(360deg);
        }
    }
</style>
<body>
<div class="so center">
    <div class="inner">
        <div class="stack-box">
            <div class="stack">
                <div class="item">
                    <div class="inner-item"></div>
                </div>
                <div class="item">
                    <div class="inner-item"></div>
                </div>
                <div class="item">
                    <div class="inner-item"></div>
                </div>
                <div class="item">
                    <div class="inner-item"></div>
                </div>
                <div class="item">
                    <div class="inner-item"></div>
                </div>
            </div>
            <div class="box">
                <div class="bottom"></div>
                <div class="left"></div>
                <div class="right"></div>
                <div class="top"></div>
            </div>
        </div>
        <div class="name">
            stack<span class="b">overflow</span>
        </div>
    </div>
</div>
</body>
</html>
```

### 3-3.loading效果

![clipboard.png](https://segmentfault.com/img/bVTdrH?w=1192&h=951)

这个代码实在太多了，大家直接上网址看吧。[css3-loading](http://www.html5tricks.com/demo/css3-loading-cool-styles/index.html)

### 3-4.音乐震动条

![clipboard.png](https://segmentfault.com/img/bVTdsN?w=260&h=155)

代码如下

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>纯CSS3模拟跳动的音符效果</title>
  <style>
    *{margin:0;padding:0;list-style: none;}
    body{background-color: #efefef;}
    .demo-music {
      position: absolute;
      width: 100%;
      height: 200px;
      top: 120px;
      zoom: 1.5;
    }

    .demo-music .music {
      width: 80px;
      height: 50px;
      top: 50%;
      left: 50%;
      -webkit-transform: translate(-40px, -25px);
      transform: translate(-40px, -25px);
      position: absolute;
    }

    .demo-music #waves {
      width: 80px;
      height: 50px;
      position: absolute;
      top: 12px;
      left: 12px;
    }

    .demo-music #waves li {
      position: relative;
      float: left;
      height: 100%;
      width: 12%;
      overflow: hidden;
      margin-right: 1px;
    }

    .demo-music #waves li span {
      position: absolute;
      bottom: 0;
      display: block;
      height: 100%;
      width: 100px;
      background: #09f;
    }

    .demo-music #waves .li1 span {
      animation: waves 0.8s linear 0s infinite alternate;
      -webkit-animation: waves 0.8s linear 0s infinite alternate;
    }

    .demo-music #waves .li2 span {
      animation: waves 0.9s linear 0s infinite alternate;
      -webkit-animation: waves 0.9s linear 0s infinite alternate;
    }

    .demo-music #waves .li3 span {
      animation: waves 1s linear 0s infinite alternate;
      -webkit-animation: waves 1s linear 0s infinite alternate;
    }

    .demo-music #waves .li4 span {
      animation: waves 0.8s linear 0s infinite alternate;
      -webkit-animation: waves 0.8s linear 0s infinite alternate;
    }

    .demo-music #waves .li5 span {
      animation: waves 0.7s linear 0s infinite alternate;
      -webkit-animation: waves 0.7s linear 0s infinite alternate;
    }

    .demo-music #waves .li6 span {
      animation: waves 0.8s linear 0s infinite alternate;
      -webkit-animation: waves 0.8s linear 0s infinite alternate;
    }
    @-webkit-keyframes waves {
      10% {
        height: 20%;
      }
      20% {
        height: 60%;
      }
      40% {
        height: 40%;
      }
      50% {
        height: 100%;
      }
      100% {
        height: 50%;
      }
    }

    @keyframes waves {
      10% {
        height: 20%;
      }
      20% {
        height: 60%;
      }
      40% {
        height: 40%;
      }
      50% {
        height: 100%;
      }
      100% {
        height: 50%;
      }
    }
  </style>
</head>
<body>
  <div class="demo-music">
    <div class="music">
      <ul id="waves" class="movement">
        <li class="li1"><span class="ani-li"></span></li>
        <li class="li2"><span class="ani-li"></span></li>
        <li class="li3"><span class="ani-li"></span></li>
        <li class="li4"><span class="ani-li"></span></li>
        <li class="li5"><span class="ani-li"></span></li>
        <li class="li6"><span class="ani-li"></span></li>
      </ul>
      <div class="music-state"></div>
    </div>
    </div>
</body>
</html>
```

## 4.形状转换

这一部分，分2d转换和3d转换。有什么好玩的，下面列举几个！

### 4-1.语法

transform:适用于2D或3D转换的元素
transform-origin：转换元素的位置（围绕那个点进行转换）。默认(x,y,z)：(50%,50%,0)

### 4-2.实例

transform:rotate(30deg);

![clipboard.png](https://segmentfault.com/img/bVTdyC?w=284&h=218)

transform:translate(30px,30px);

![clipboard.png](https://segmentfault.com/img/bVTdAC?w=501&h=450)

transform:scale(.8);

![clipboard.png](https://segmentfault.com/img/bVTdAT?w=404&h=373)

transform: skew(10deg,10deg);

![clipboard.png](https://segmentfault.com/img/bVTdBj?w=280&h=160)

transform:rotateX(180deg);

![clipboard.png](https://segmentfault.com/img/bVTdHv?w=142&h=97)

transform:rotateY(180deg);

![clipboard.png](https://segmentfault.com/img/bVTdHA?w=142&h=97)

transform:rotate3d(10,10,10,90deg);

![clipboard.png](https://segmentfault.com/img/bVTdHU?w=182&h=114)

## 5.选择器

css3提供的选择器可以让我们的开发，更加方便！这个大家都要了解。下面是css3提供的选择器。

![clipboard.png](https://segmentfault.com/img/bVTd2d?w=780&h=728)

图片来自w3c。这一块建议大家去w3c看（[CSS 选择器参考手册](http://www.w3school.com.cn/cssref/css_selectors.asp)），那里的例子通俗易懂。我不重复讲了。
提供的选择器里面，基本都挺好用的。但是我觉得有些不会很常用，比如，`:root，:empty，:target，:enabled，:checked`。而且几个不推荐使用，网上的说法是性能较差`[attribute*=value]，[attribute$=value]，[attribute^=value]`，这个我没用过，不太清楚。

## 6.阴影

以前没有css3的时候，或者需要兼容低版本浏览器的时候，阴影只能用图片实现，但是现在不需要，css3就提供了！

### 6-1.语法

```
box-shadow: 水平阴影的位置 垂直阴影的位置 模糊距离 阴影的大小 阴影的颜色 阴影开始方向（默认是从里往外，设置inset就是从外往里）;
```

### 6-1.例子

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8"> 
<title></title> 
<style> 
div
{
    width:300px;
    height:100px;
    background:#09f;
    box-shadow: 10px 10px 5px #888888;
}
</style>
</head>
<body>

<div></div>

</body>
</html>
```

运行效果

![clipboard.png](https://segmentfault.com/img/bVTd9F?w=364&h=151)

## 7.边框

### 7-1.边框图片

#### 7-1-1.语法

border-image: 图片url 图像边界向内偏移 图像边界的宽度(默认为边框的宽度) 用于指定在边框外部绘制偏移的量（默认0） 铺满方式--重复（repeat）、拉伸（stretch）或铺满（round）（默认：拉伸（stretch））;

#### 7-1-2.例子

边框图片（来自菜鸟教程）

![clipboard.png](https://segmentfault.com/img/bVTefk?w=81&h=81)

代码

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title></title>
<style>
.demo {
    border: 15px solid transparent;
    padding: 15px;   
    border-image: url(border.png);
    border-image-slice: 30;
    border-image-repeat: round;
    border-image-outset: 0;
}
</style>
</head>
<body>
    <div class="demo"></div>
</body>
</html>
```

效果

![clipboard.png](https://segmentfault.com/img/bVTeeg?w=601&h=91)

有趣变化

![clipboard.png](https://segmentfault.com/img/bVTefm?w=617&h=444)

![clipboard.png](https://segmentfault.com/img/bVTefl?w=617&h=444)

那个更好看，大家看着办

### 7-2.边框圆角

#### 7-2-1.语法

```
border-radius: n1,n2,n3,n4;
border-radius: n1,n2,n3,n4/n1,n2,n3,n4;
/*n1-n4四个值的顺序是：左上角，右上角，右下角，左下角。*/
```

#### 7-2-2.例子

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8"> 
<title></title> 
<style> 
div
{
    border:2px solid #a1a1a1;
    padding:10px 40px; 
    background:#dddddd;
    text-align:center;
    width:300px;
    border-radius:25px 0 25px 0;
}
</style>
</head>
<body>
<div>border-radius</div>
</body>
</html>
```

运行结果

![clipboard.png](https://segmentfault.com/img/bVTegF?w=486&h=82)

## 8.背景

这一块主要讲css3提供背景的三个属性

### background-clip

制定背景绘制（显示）区域

默认情况（从边框开始绘制）

![clipboard.png](https://segmentfault.com/img/bVTeqt?w=533&h=251)

从padding开始绘制（显示），不算border,，相当于把border那里的背景给裁剪掉！（background-clip: padding-box;）

![clipboard.png](https://segmentfault.com/img/bVTeqv?w=533&h=255)

只在内容区绘制（显示），不算padding和border，相当于把padding和border那里的背景给裁剪掉！（background-clip: content-box;）

![clipboard.png](https://segmentfault.com/img/bVTeqy?w=537&h=244)

### background-origin

引用菜鸟教程的说法：background-Origin属性指定background-position属性应该是相对位置

下面的div初始的html和css代码都是一样的。如下
html

```
<div>
Lorem ipsum dolor sit amet, consectetuer adipiscing elit, sed diam nonummy nibh euismod tincidunt ut laoreet dolore magna aliquam erat volutpat. Ut wisi enim ad minim veniam, quis nostrud exerci tation ullamcorper suscipit lobortis nisl ut aliquip ex ea commodo consequat.
</div>
```

css

```
div
{
    border:10px dashed black;
    padding:35px;
    background:url('logo.png') no-repeat,#ccc;
    background-position:0px 0px;
}
```

下面看下，background-origin不同的三种情况

![图片描述](https://segmentfault.com/img/bVZGAM?w=800&h=506)

### background-size

这个相信很好理解，就是制定背景的大小
下面的div初始的html和css代码都是一样的。如下
html

```
<div>
Lorem ipsum dolor sit amet, consectetuer adipiscing elit, sed diam nonummy nibh euismod tincidunt ut laoreet dolore magna aliquam erat volutpat. Ut wisi enim ad minim veniam, quis nostrud exerci tation ullamcorper suscipit lobortis nisl ut aliquip ex ea commodo consequat.
</div>
```

css

```
div
{
    border:1px dashed black;
    padding:35px;
    background:url('test.jpg') no-repeat;
}
```

![clipboard.png](https://segmentfault.com/img/bVTgk7?w=1016&h=768)

### 多张背景图

这个没什么，就是在一张图片，使用多张背景图片，代码如下！
html

```
<p>两张图片的背景</p>
<div>
Lorem ipsum dolor sit amet, consectetuer adipiscing elit, sed diam nonummy nibh euismod tincidunt ut laoreet dolore magna aliquam erat volutpat. Ut wisi enim ad minim veniam, quis nostrud exerci tation ullamcorper suscipit lobortis nisl ut aliquip ex ea commodo consequat.
</div>
```

css

```
div
{
    border:1px dashed black;
    padding:35px;
    background-size: contain;
    background:url('test.jpg') no-repeat left,url(logo.png) no-repeat right;
}
```

![clipboard.png](https://segmentfault.com/img/bVTglS?w=999&h=199)

## 9.反射

这个也可以说是倒影，用起来也挺有趣的。

### 9-1.语法

```
-webkit-box-reflect:方向[ above-上 | below-下 | right-右 | left-左 ]，偏移量，遮罩图片
```

### 9-2.下倒影

html

```
<p>下倒影</p>
<p class="reflect-bottom-p"><img src="test.jpg" class="reflect-bottom"></p>
```

css

```
.reflect-bottom-p {
    padding-bottom: 300px;
}
        
.reflect-bottom {
    -webkit-box-reflect: below;
}   
```

![clipboard.png](https://segmentfault.com/img/bVTeoE?w=518&h=669)

### 9-2.右倒影（有偏移）

![clipboard.png](https://segmentfault.com/img/bVTeoU?w=994&h=351)

html

```
<p>右倒影同时有偏移</p>
<p><img src="test.jpg" class="reflect-right-translate"></p>
```

css

```
.reflect-right-translate {
    -webkit-box-reflect: right 10px;
}
```

### 9-3.下倒影（渐变）

![clipboard.png](https://segmentfault.com/img/bVTepo?w=507&h=668)

html

```
<p>下倒影（渐变）</p>
<p class="reflect-bottom-p"><img src="test.jpg" class="reflect-bottom-mask"></p>
```

css

```
reflect-bottom-mask {
    -webkit-box-reflect: below 0 linear-gradient(transparent, white);
}
```

### 9-3.下倒影（图片遮罩）

使用的图片

![clipboard.png](https://segmentfault.com/img/bVTepE?w=200&h=200)

![clipboard.png](https://segmentfault.com/img/bVTepD?w=510&h=672)

html

```
<p>下倒影（png图片）</p>
<p class="reflect-bottom-p"><img src="test.jpg" class="reflect-bottom-img"></p>
```

css

```
.reflect-bottom-img {
    -webkit-box-reflect: below 0 url(shou.png);
}
```

## 10.文字

### 换行

语法：`word-break: normal|break-all|keep-all;`
例子和运行效果

![clipboard.png](https://segmentfault.com/img/bVTgo9?w=511&h=446)

语法：`word-wrap: normal|break-word;`
例子和运行效果

![clipboard.png](https://segmentfault.com/img/bVTgpp?w=602&h=423)

> 超出省略号这个，主要讲`text-overflow`这个属性，我直接讲实例的原因是`text-overflow`的三个写法，`clip|ellipsis|string`。`clip`这个方式处理不美观，不优雅。`string`只在火狐兼容。

![clipboard.png](https://segmentfault.com/img/bVTgpF?w=595&h=299)

### 超出省略号

这个其实有三行代码，禁止换行，超出隐藏，超出省略号
html

```
<div>This is some long text that will not fit in the box</div>
```

css

```
div
{
    width:200px; 
    border:1px solid #000000;
    overflow:hidden;
    white-space:nowrap; 
    text-overflow:ellipsis;
}
```

运行结果

![clipboard.png](https://segmentfault.com/img/bVTgqd?w=292&h=42)

### 多行超出省略号

超出省略号。这个对于大家来说，不难！但是以前如果是多行超出省略号，就只能用js模拟！现在css3提供了多行省略号的方法！遗憾就是这个暂时只支持webkit浏览器！

代码如下

```
<!DOCTYPE html>
<html>    
<head>
<meta charset="utf-8"> 
<title></title> 
<style> 
div
{
    width:400px;
    margin:0 auto;
    overflow : hidden;
    border:1px solid #ccc;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
}
</style>
</head>
<body>

<div>这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏这里将会超出隐藏</div>


</body>
</html>
```

效果图

![clipboard.png](https://segmentfault.com/img/bVTd6V?w=491&h=74)

这样发现边框贴着难看，要撑开一点，但是撑开上下边框不要使用padding!因为会出现下面这个效果。

![clipboard.png](https://segmentfault.com/img/bVTd66?w=527&h=102)

正确姿势是这样写

```
<style> 
div
{
    width:400px;
    margin:0 auto;
    overflow : hidden;
    border:1px solid #ccc;
    text-overflow: ellipsis;
    padding:0 10px;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    line-height:30px;
    height:60px;
}
</style>
```

运行结果

![clipboard.png](https://segmentfault.com/img/bVTd7k?w=478&h=104)

这样写，就算在不是webkit内核的浏览器，也可以优雅降级（高度=行高*行数（webkit-line-clamp））！

![clipboard.png](https://segmentfault.com/img/bVTd7u?w=481&h=108)

### 文字阴影

语法：text-shadow:水平阴影，垂直阴影，模糊的距离，以及阴影的颜色。
例子：`text-shadow: 0 0 10px #f00;`
效果

![clipboard.png](https://segmentfault.com/img/bVTgnR?w=378&h=78)

## 11.颜色

这个其实就是css3提供了新的颜色表示方法。

### rgba

一个是rgba（rgb为颜色值，a为透明度）

```
color: rgba(255,00,00,1);
background: rgba(00,00,00,.5);
```

![clipboard.png](https://segmentfault.com/img/bVTgri?w=924&h=81)

### hsla

h:色相”，“s：饱和度”，“l：亮度”，“a：透明度”
这个我姿势了解过，没用过，这里简单给一个例子

```
color: hsla( 112, 72%, 33%, 0.68);
background-color: hsla( 49, 65%, 60%, 0.68);
```

![clipboard.png](https://segmentfault.com/img/bVTgqS?w=934&h=72)

## 12.渐变

css3的渐变可以说是一大亮点，提供了线性渐变，径向渐变，圆锥渐变（w3c和菜鸟教程都没有提及，是我从一篇文章了解到，但是我自己在谷歌浏览器尝试，却是一个无效的写法！大家如果知道怎么用，请告知！感谢）
渐变这一部分，由于用法灵活，功能也强大，这个写起来很长，写一点又感觉没什么意思，我这里贴几个链接教程给大家，在文章我不多说了，毕竟我也是从那几个地方学的，他们写得也是比我好，比我详细！

[CSS3 Gradient](http://www.w3cplus.com/content/css3-gradient)
[再说CSS3渐变——线性渐变](http://www.w3cplus.com/css3/new-css3-linear-gradient.html)
[再说CSS3渐变——径向渐变](http://www.w3cplus.com/css3/new-css3-radial-gradient.html)
[神奇的 conic-gradient 圆锥渐变](http://www.cnblogs.com/coco1s/p/7079529.html)（这篇就是看我看到圆锥渐变的文章）

## 13.Filter（滤镜）

css3的滤镜也是一个亮点，功能强大，写法也灵活。

例子代码如下

```
<p>原图</p>
<img src="test.jpg" />
<p>黑白色filter: grayscale(100%)</p>
<img src="test.jpg" style="filter: grayscale(100%);"/>
<p>褐色filter:sepia(1)</p>
<img src="test.jpg" style="filter:sepia(1);"/>
<p>饱和度saturate(2)</p>
<img src="test.jpg" style="filter:saturate(2);"/>
<p>色相旋转hue-rotate(90deg)</p>
<img src="test.jpg" style="filter:hue-rotate(90deg);"/>
<p>反色filter:invert(1)</p>
<img src="test.jpg" style="filter:invert(1);"/>
<p>透明度opacity(.5)</p>
<img src="test.jpg" style="filter:opacity(.5);"/>
<p>亮度brightness(.5)</p>
<img src="test.jpg" style="filter:brightness(.5);"/>
<p>对比度contrast(2)</p>
<img src="test.jpg" style="filter:contrast(2);"/>
<p>模糊blur(3px)</p>
<img src="test.jpg" style="filter:blur(3px);"/>
<p>阴影drop-shadow(5px 5px 5px #000)</p>
<img src="test.jpg" style="filter:drop-shadow(5px 5px 5px #000);"/>
```

![clipboard.png](https://segmentfault.com/img/bVTgyB?w=504&h=357)

![clipboard.png](https://segmentfault.com/img/bVTgzW?w=528&h=716)

![clipboard.png](https://segmentfault.com/img/bVTgzY?w=511&h=728)

![clipboard.png](https://segmentfault.com/img/bVTgzZ?w=511&h=721)

![clipboard.png](https://segmentfault.com/img/bVTgz0?w=513&h=723)

![clipboard.png](https://segmentfault.com/img/bVTgPQ?w=519&h=715)

## 14.弹性布局

这里说的弹性布局，就是flex；这一块要讲的话，必须要全部讲完，不讲完没什么意思，反而会把大家搞蒙！讲完也是很长，所以，这里我也只贴教程网址。博客讲的很好，很详细！

[Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
[Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)

## 15.栅格布局

栅格化布局，就是grid；这一块和flex一样，要讲就必须讲完。这块的内容和flex差不多，也有点长，这里我也贴链接，这个链接讲得也很详细！

[Grid布局指南](http://www.jianshu.com/p/d183265a8dad)

## 16.多列布局

这一块，我也是了解过，我觉得多列应该还是挺有用的。虽然我没在项目中用过，下面我简单说下！举个例子！这个属性，建议加私有前缀，兼容性有待提高！
html

```
<div class="newspaper">
当我年轻的时候，我梦想改变这个世界；当我成熟以后，我发现我不能够改变这个世界，我将目光缩短了些，决定只改变我的国家；当我进入暮年以后，我发现我不能够改变我们的国家，我的最后愿望仅仅是改变一下我的家庭，但是，这也不可能。当我现在躺在床上，行将就木时，我突然意识到：如果一开始我仅仅去改变我自己，然后，我可能改变我的家庭；在家人的帮助和鼓励下，我可能为国家做一些事情；然后，谁知道呢?我甚至可能改变这个世界。
</div>
```

css

```
.newspaper
{
    column-count: 3;
    -webkit-column-count: 3;
    -moz-column-count: 3;
    column-rule:2px solid #000;
    -webkit-column-rule:2px solid #000;
    -mox-column-rule:2px solid #000;
}    
```

![clipboard.png](https://segmentfault.com/img/bVTgRx?w=587&h=163)

## 17.盒模型定义

box-sizing这个属性，网上说法是：属性允许您以特定的方式定义匹配某个区域的特定元素。

这个大家看着可能不知道在说什么，简单粗暴的理解就是：box-sizing:border-box的时候，边框和padding包含在元素的宽高之内！如下图

![图片描述](https://segmentfault.com/img/bVZDJZ?w=1040&h=797)

box-sizing:content-box的时候，边框和padding不包含在元素的宽高之内！如下图

![图片描述](https://segmentfault.com/img/bVZDJ5?w=950&h=876)

## 18.媒体查询

媒体查询，就在监听屏幕尺寸的变化，在不同尺寸的时候显示不同的样式！在做响应式的网站里面，是必不可少的一环！不过由于我最近的项目都是使用rem布局。所以媒体查询就没怎么用了！但是，媒体查询，还是很值得一看的！说不定哪一天就需要用上了！

例子代码如下

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8"> 
<title></title> 
<style>
body {
    background-color: pink;
}
@media screen and (max-width: 960px) {
    body {
        background-color: darkgoldenrod;
    }
}
@media screen and (max-width: 480px) {
    body {
        background-color: lightgreen;
    }
}
</style>
</head>
<body>

<h1>重置浏览器窗口查看效果！</h1>
<p>如果媒体类型屏幕的可视窗口宽度小于 960 px ，背景颜色将改变。</p>
<p>如果媒体类型屏幕的可视窗口宽度小于 480 px ，背景颜色将改变。</p>

</body>
</html>
```

运行效果

![clipboard.png](https://segmentfault.com/img/bVTgPW?w=1022&h=157)

## 19.混合模式

混合模式，就像photoshop里面的混合模式！这一块，我了解过，在项目上没用过，但是我觉得这个应该不会没有用武之地！
css3的混合模式，两个（background-blend-mode和mix-blend-mode）。这两个写法和显示效果都非常像！区别就在于background-blend-mode是用于同一个元素的背景图片和背景颜色的。mix-blend-mode用于一个元素的背景图片或者颜色和子元素的。看以下代码，区别就出来了！

> 这一块图片很多，大家看图片快速扫一眼，看下什么效果就好！

### background-blend-mode

代码

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>
    <style>
        div{
            width: 480px;
            height: 300px;
            background:url('test.jpg')no-repeat,#09f;
        }
    </style>
    <body>
        <!---->
        
        <p>原图</p>
        <div></div>
        <p>multiply正片叠底</p>
        <div style="background-blend-mode: multiply;"></div>
        <p>screen滤色</p>
        <div style="background-blend-mode: screen;"></div>
        <p>overlay叠加</p>
        <div style="background-blend-mode: overlay;"></div>
        <p>darken变暗</p>
        <div style="background-blend-mode: darken;"></div>
        <p>lighten变亮</p>
        <div style="background-blend-mode: lighten;"></div>
        <p>color-dodge颜色减淡模式</p>
        <div style="background-blend-mode: color-dodge;"></div>
        <p>color-burn颜色加深</p>
        <div style="background-blend-mode: color-burn;"></div>
        <p>hard-light强光</p>
        <div style="background-blend-mode: hard-light;"></div>
        <p>soft-light柔光</p>
        <div style="background-blend-mode: soft-light;"></div>
        <p>difference差值</p>
        <div style="background-blend-mode: difference;"></div>
        <p>exclusion排除</p>
        <div style="background-blend-mode: exclusion;"></div>
        <p>hue色相</p>
        <div style="background-blend-mode: hue;"></div>
        <p>saturation饱和度</p>
        <div style="background-blend-mode: saturation;"></div>
        <p>color颜色</p>
        <div style="background-blend-mode: color;"></div>
        <p>luminosity亮度</p>
        <div style="background-blend-mode: luminosity;"></div>
    </body>
</html>
```

运行效果

![clipboard.png](https://segmentfault.com/img/bVTgPX?w=502&h=362)

![clipboard.png](https://segmentfault.com/img/bVTgPZ?w=506&h=710)

![clipboard.png](https://segmentfault.com/img/bVTgP0?w=512&h=711)

![clipboard.png](https://segmentfault.com/img/bVTgP1?w=516&h=711)

![clipboard.png](https://segmentfault.com/img/bVTgP2?w=514&h=353)

![clipboard.png](https://segmentfault.com/img/bVTgP3?w=523&h=709)

![clipboard.png](https://segmentfault.com/img/bVTgP6?w=521&h=708)

![clipboard.png](https://segmentfault.com/img/bVTgP9?w=544&h=713)

![clipboard.png](https://segmentfault.com/img/bVTgQa?w=525&h=705)

### mix-blend-mode

代码

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>
    <style>
        div{
            padding: 20px;
            width: 480px;
            background: #09f;
        }
    </style>
    <body>
        <p>原图</p>
        <div><img src="test.jpg"/></div>
        <p>multiply正片叠底</p>
        <div><img src="test.jpg" style="mix-blend-mode: multiply;"/></div>
        <p>screen滤色</p>
        <div><img src="test.jpg" style="mix-blend-mode: screen;"/></div>
        <p>overlay叠加</p>
        <div><img src="test.jpg" style="mix-blend-mode: overlay;"/></div>
        <p>darken变暗</p>
        <div><img src="test.jpg" style="mix-blend-mode: darken;"/></div>
        <p>lighten变亮</p>
        <div><img src="test.jpg" style="mix-blend-mode: lighten;"/></div>
        <p>color-dodge颜色减淡模式</p>
        <div><img src="test.jpg" style="mix-blend-mode: color-dodge;"/></div>
        <p>color-burn颜色加深</p>
        <div><img src="test.jpg" style="mix-blend-mode: color-burn;"/></div>
        <p>hard-light强光</p>
        <div><img src="test.jpg" style="mix-blend-mode: hard-light;"/></div>
        <p>soft-light柔光</p>
        <div><img src="test.jpg" style="mix-blend-mode: soft-light;"/></div>
        <p>difference差值</p>
        <div><img src="test.jpg" style="mix-blend-mode: difference;"/></div>
        <p>exclusion排除</p>
        <div><img src="test.jpg" style="mix-blend-mode: exclusion;"/></div>
        <p>hue色相</p>
        <div><img src="test.jpg" style="mix-blend-mode: hue;"/></div>
        <p>saturation饱和度</p>
        <div><img src="test.jpg" style="mix-blend-mode: saturation;"/></div>
        <p>color颜色</p>
        <div><img src="test.jpg" style="mix-blend-mode: color;"/></div>
        <p>luminosity亮度</p>
        <div><img src="test.jpg" style="mix-blend-mode: luminosity;"/></div>
    </body>
</html>
```

运行效果

![clipboard.png](https://segmentfault.com/img/bVTgQd?w=556&h=406)

![clipboard.png](https://segmentfault.com/img/bVTgQf?w=550&h=400)

![clipboard.png](https://segmentfault.com/img/bVTgQk?w=554&h=400)

![clipboard.png](https://segmentfault.com/img/bVTgQm?w=554&h=398)

![clipboard.png](https://segmentfault.com/img/bVTgQp?w=551&h=401)

![clipboard.png](https://segmentfault.com/img/bVTgQs?w=544&h=400)

![clipboard.png](https://segmentfault.com/img/bVTgQv?w=568&h=402)

![clipboard.png](https://segmentfault.com/img/bVTgQx?w=555&h=391)

![clipboard.png](https://segmentfault.com/img/bVTgQz?w=563&h=404)

![clipboard.png](https://segmentfault.com/img/bVTgQB?w=553&h=400)

![clipboard.png](https://segmentfault.com/img/bVTjea?w=556&h=399)

![clipboard.png](https://segmentfault.com/img/bVTgSP?w=551&h=396)

![clipboard.png](https://segmentfault.com/img/bVTjeh?w=560&h=400)

![clipboard.png](https://segmentfault.com/img/bVTjem?w=558&h=400)

![clipboard.png](https://segmentfault.com/img/bVTjep?w=547&h=402)

![clipboard.png](https://segmentfault.com/img/bVTjeq?w=551&h=395)

# 9.CSS样式隔离

CSS in JS，意思就是使用 js 语言写 css，完全不需要些单独的 css 文件，所有的 css 代码全部放在组件内部，以实现 css 的模块化。

CSS in JS 其实是一种编写思想，目前已经有超过 40 多种方案的实现，最出名的是 styled-components。

使用方式如下：

```
import React from "react";
import styled from "styled-components";

// 创建一个带样式的 h1 标签
const Title = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: palevioletred;
`;

// 创建一个带样式的 section 标签
const Wrapper = styled.section`
  padding: 4em;
  background: papayawhip;
`;

// 通过属性动态定义样式
const Button = styled.button`
  background: ${props => (props.primary ? "palevioletred" : "white")};
  color: ${props => (props.primary ? "white" : "palevioletred")};

  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

// 样式复用
const TomatoButton = styled(Button)`
  color: tomato;
  border-color: tomato;
`;

<Wrapper>
  <Title>Hello World, this is my first styled component!</Title>
  <Button primary>Primary</Button>
</Wrapper>;
```

可以看到，我们直接在 js 中编写 css，案例中在定义源生 html 时就创建好了样式，在使用的时候就可以渲染出带样式的组件了。

除此之外，还有其他比较出名的库：

- emotion
- radium
- glamorous

日常的开发模式 存在以下痛点。

- 全局污染：CSS 选择器的作用域是全局的，所以很容易引起选择器冲突；而为了避免全局冲突，又会导致类命名的复杂度上升
- 复用性低：CSS 缺少抽象的机制，选择器很容易出现重复，不利于维护和复用

对于这个问题，也有一些方案。 vue 框架已经帮我们实现了 css 模块化, 通过 style 标签的 scoped 指令定义作用域，通过编译为该作用域所有标签生成唯一的属性。如图： ![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6b86cc72b94e4df49adb0fba1ffee3eb~tplv-k3u1fbpfcp-zoom-1.image) 但是 react 并未给我们实现，我们可能会能通过其它方案来实现，如：约定 css class 命名规范(命名空间) 加上业务前缀，或者封装组件, 或者[BEM 命名规范(block-element-modife)](https://bemcss.com/)/[BEM 简介](https://segmentfault.com/a/1190000012705634)、 但并不能解决根源问题，反而使代码更难维护。

## 目前主流的 css 模块化分为 css modules 和 css in js 两种方案

### css modules

> CSS Modules 指的是我们像 import js 一样去引入我们的 css 代码，代码中的每一个类名都是引入对象的一个属性, 编译时会将 css 类名 加上唯一 hash。

css module 需要 webpack 配置 css-loader 或者 scss-loader , module 为 true

```js
{
    loader: 'css-loader',
    options: {
        modules: true, // 开启模块化
        localIdentName: '[path][name]-[local]-[hash:base64:5]'
    }
}
```

效果如图所示： ![效果图](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e81eff3bce7b4d168b7cf72899d75325~tplv-k3u1fbpfcp-zoom-1.image)

讲解一下 localIdentName 自定义生成的类名格式，可选参数有：

- [path]表示样式表相对于项目根目录所在的路径(默认不拼接)
- [name] 表示样式表文件名称
- [local] 表示样式表的类名定义名称
- [hash:length] 表示 32 位的 hash 值 注意：只有类名选择器和 ID 选择器才会被模块化控制，类似 body h2 span 这些标签选择器是不会被模块化控制

css module 作用域

- 作用域默认为 local 即只在当前模块生效
- global 被 :global 包裹起来的类名，不会被模块化

```js
/* 加上 :global 会全局样式 */
:global(.global-color) {
  color: blue;
  :global(.common-width) {
    width: 200px;
  }
}
```

css module 高级使用

- 和外部样式混用

```js
import classNames from 'classnames';

// 使用classNames
const wrapperClassNames = classNames({
  'common-show': visible,
  'common-hide': !visible,
  [styles1['view-wrapper']]: true
});
<div className={wrapperClassNames}></div>;

// 使用模板字符串
<div className={`${styles1.content} ${styles1.color} common-show`}>
  我是文章内容我是文章内容我是文章内容我是文章内容我是文章内容我是文章内容
</div>;
复制代码
```

- 覆盖第三方 UI 库

```jsx
{/* 覆盖第三方UI库 样式*/}
<div className={styles1['am-button-custom-wrapper']}>
  <Button type={'primary'} onClick={() => toggle()}>
     {visible ? '隐藏' : '显示'}
  </Button>
</div>

//  覆盖第三方UI库的 样式
.am-button-custom-wrapper {
  :global {
    .am-button-primary {
      color: red;
    }
  }
}
复制代码
```

### css in js

- style 对象

```js
const style = {
  width: 200,
  color: '#fff',
  backgroundColor: 'red'
};
```

- [emotion](https://emotion.sh/docs/introduction)

- styled-components

  > [styled-components](https://styled-components.com/docs/basics#extending-styles) 是针对 React 写的一套 css in js 框架, 在你使用 styled-components 进行样式定义的同时，你也就创建了一个 React 组件。[css in js ](https://www.jianshu.com/p/27788be90605)

  ```css
  const DivWrapper = styled.div`
    width: '100%';
    height: 300;
    background-color: ${(props) => props.color};
  `;
  
  // 封装第三方组件库
  const AntdButtonWrapper = styled(Button)`
    color: 'red';
  `;
  
  // 通过属性动态定义样式
  const MyButton = styled.button`
    background: ${(props) => (props.primary ? 'palevioletred' : 'white')};
    color: ${(props) => (props.primary ? 'white' : 'palevioletred')};
  
    font-size: 1em;
    margin: 1em;
    padding: 0.25em 1em;
    border: 2px solid palevioletred;
    border-radius: 3px;
  `;
  
  // 样式复用
  const TomatoButton = styled(MyButton)`
    color: tomato;
    border-color: tomato;
  `;
  
  // 创建关键帧
  const rotate = keyframes`
    from {
      transform: rotate(0deg);
    }
  
    to {
      transform: rotate(360deg);
    }
    `;
  
  // 创建动画组件
  const Rotate = styled.div`
    display: inline-block;
    animation: ${rotate} 2s linear infinite;
    padding: 2rem 1rem;
    font-size: 1.2rem;
  `;
  ```

styled-components 优势: 支持将 props 以插值的方式传递给组件,以调整组件样式, 跨平台可在 RN 和 next 中使用。 缺点： 预处理器和后处理器不兼容。

### 对比总结

![总结](https://user-gold-cdn.xitu.io/2019/12/30/16f5477372d2bee3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## 参考链接

- [梳理 CSS 模块化 ](https://juejin.im/post/6844904034281734151#heading-9)
- [styled-component, emotion and jss 对比](https://www.cnblogs.com/yy17yy/p/11618775.html)

# 10.CSS性能优化

## 提高性能的方法有哪些?

```javascript
1. 合并css文件，如果页面加载10个css文件,每个文件1k，那么也要比只加载一个100k的css文件慢。
2. 减少css嵌套，最好不要嵌套三层以上。
3. 不要在ID选择器前面进行嵌套，ID本来就是唯一的而且权限值大，嵌套完全是浪费性能。
4. 建立公共样式类，把相同样式提取出来作为公共类使用。
5. 减少通配符*或者类似[hidden="true"]这类选择器的使用，挨个查找所有...这性能能好吗？
6. 巧妙运用css的继承机制，如果父节点定义了，子节点就无需定义。
7. 拆分出公共css文件，对于比较大的项目可以将大部分页面的公共结构样式提取出来放到单独css文件里，这样一次下载 后就放到缓存里，当然这种做法会增加请求，具体做法应以实际情况而定。
8. 不用css表达式，表达式只是让你的代码显得更加酷炫，但是对性能的浪费可能是超乎你想象的。
9. 少用css rest，可能会觉得重置样式是规范，但是其实其中有很多操作是不必要不友好的，有需求有兴趣，可以选择normolize.css。
10. cssSprite，合成所有icon图片，用宽高加上background-position的背景图方式显现icon图，这样很实用，减少了http请求。
11. 善后工作，css压缩(在线压缩工具 YUI Compressor)
12. GZIP压缩，是一种流行的文件压缩算法。
123456789101112
```

## 性能优化

> **> \**1. 避免使用@import，外部的css文件中使用@import会使得页面在加载时增加额外的延迟。\****

首先，使用@import引入css会影响浏览器的并行下载。使用@import引用的css文件只有在引用它的那个css文件被下载，解析之后，浏览器才会知道还有另外一个css需要下载，这时才去下载，然后下载后开始解析，构建render tree等一系列操作，这就导致浏览器无法并行下载
所需的样式文件。
其次，多个@import会导致下载顺序紊乱，在IE中，@import会引发资源文件的下载顺序被打乱，即排列在@import后面的js文件优先于@import下载，并且打乱甚至破坏@import自身的并行下载。
所以不要使用这一方法，使用link标签就行了。

> **> \**2.避免过分重排\****

- 浏览器为了重新渲染部分或整个页面，重新计算页面元素位置和几何结构的进程叫做reflow
- 浏览器根据定义好的样式来计算，并将元素放到该出现的位置上，这个过程叫做reflow
- 页面上任何一个节点触发来reflow，会导致他的子节点和祖先节点重新渲染
- 导致reflow发生的情况

```bash
 1. 改变窗口的大小  
 2. 改变文字的大小
 3. 添加 删除样式表
 4. 内容的改变 输入框输入内容也会
 5. 伪类的激活
 6. 操作class属性
 7. 脚本操作dom js改变css类
 8. 计算offsetWidth和offsetHeight
 9. 设置style属性
 10.改变元素的内外边距 
12345678910
```

- 常见重排元素

```bash
 1. 大小有关的 width,height,padding,margin,border-width,border,min-height
 2. 布局有关的 display,top,position,float,left,right,bottom
 3. 字体有关的 font-size,text-align,font-weight,font-family,line-height,white-space,vertical-align
 4. 隐藏有关的 overflow,overflow-x,overflow-y
1234
```

- 减少reflow对性能的影响的建议

```bash
 1. 不要一条条的修改dom的样式，预先定义好class，然后修改dom的classname
 2. 不要修改影响范围较大的dom
 3. 为动画元素使用绝对定位
 4. 不要table布局，因为一个很小的改动会造成整个table重新布局
 5. 避免设置大量的style属性，通过设置style属性改变节点样式的话，每一次设置都会触发一次reflow，所以最好使用class属性
 6. 如果css里面有计算表达式，每次都会重新计算一遍，触发一次reflow
123456
```

> **> \**repaint\****

1. 当一个元素的外观被改变，但是布局没有改变的情况
2. 当元素改变的时候，不影响元素在页面中的位置，浏览器仅仅会用新的样式重绘此元素
3. 常见的重绘元素

```bash
 - 颜色 color,background
 - 边框样式 border-style,outline-color,outline,outline-style,border-radius,box-shadow,outline-width
 - 背景有关 background,backgound-image,background-position,background-repeat,background-size
123
```

> **> CSS动画**

1. css动画启用GPU加速，应用GPU的图形性能对浏览器中的一些图形操作交给GPU完成。canvas2D，布局合成，css3转换，css3d变换，webGL，视频
2. 2d加速
3. 3d加速

> **> 文件压缩**

性能优化时最容易想到的，也是最常见的方法，就是文件压缩，这一方案往往效果显著
文件的大小会直接影响浏览器的加载速度，这一点在网络较差时表现尤为明显，构建工具webpack，gulp/grunt，rollup，压缩之后能够明显减少，可以大大降低浏览器的加载时间。

> **> 去除无用CSS**

虽然文件压缩能够降低文件大小，但css文件压缩通常只会去除无用的空格，这样就限制来css文件的压缩比例。如果压缩后的文件仍然超过来预期的大小，可以试着找到并删除代码中无用的css。
一般情况下，会存在这两种无用的CSS代码：

1. 不同元素或者其他情况下的重复代码，
2. 整个页面内没有生效的CSS代码

> **> 有选择地使用选择器**

css选择器的匹配是从右向左进行的，这一策略导致来不同种类的选择器之间的性能也存在差异。相比于 #markdown-content-h3,显然使用 #markdown.content h3时，浏览器生成渲染树所要花费的时间更多。因为后者需要先找到DOM中的所有h3元素，再过滤掉祖先元素不是.content的，最后过滤掉.content不是#markdown的。试想，页面中的元素更多，那么匹配所要花费的时间代价自然更高。
显得浏览器在这一方面做了很多优化，不同选择器的性能差别并不明显，甚至可以说差别甚微，此外不同选择器在不同浏览器中的性能表现也不统一，在编写css的时候无法兼顾每种浏览器，鉴于这两点，在使用选择器时，尽量记住以下几点：

```bash
 1. 保持简单，不要使用嵌套过多过于复杂的选择器
 2. 通配符和属性选择器效率最低，需要匹配的元素最多，尽量避免使用。
 3. 不要使用类选择器和ID选择器修饰元素标签，如：h3#markdown-content，这一多此一举，还会降低效率
 4. 不要为了追求速度而放弃可读性和可维护性
1234
```

TIPS：为什么css选择器是从右向左匹配的？
css中更多的选择器是不会匹配的，所以在考虑性能问题时，需要考虑的是如何在选择器不匹配时提升效率，从右向左匹配就是为了达成这一目的的，通过这一策略能够使得css选择器在不匹配的时候效率更高。

> **> 减少使用昂贵的属性**

在浏览器绘制屏幕时，所有需要浏览器进行操作或计算的属性相对而言都需要花费更大的代价，而页面发生重绘时，它们会降低浏览器的渲染性能。所以在编写css时，应该尽量减少使用昂贵属性，如:
box-shadow, border-radius, filter, 透明度, :nth-child等
当然并不是不要使用这些属性，这些都是经常使用的属性，只是这里可以作为一个了解。当有其他方案可以选择的时候，可以优先选择没有昂贵属性或昂贵属性更少的方案，这一网站的性能会在不知不觉中得到一定的提升。

> **> 硬件加速的好坏**

1. 仅仅依靠GPU还是不行的，许多动画还是需要CPU的介入，连接cpu和GPU的总带宽不是无限的，所以需要注意数据在cpu和GPU之间的传输，尽量避免造成通道的拥挤，要一直注意像素的传输。
2. 一个重点是了解创建的合成层的数量，每一个层都对应来一个GPU纹理，太多的层会消耗很多内存。
3. `**chrome://flags/#composited-layer-borders**`观察的地址。
4. 每一个dom元素的合成层都会被标记一个额外的边框，这一就可以验证是否有了很多层
5. 另一个重点是保持GPU和CPU之间传输量达到最小值，也就是说，层的更新数量最好是一个理想的常量，每次层更新的时候，一堆新的像素就可能需要传输给GPU。
6. 因为为了高性能，动画开始之后避免层的更新也是非常重要的，避免动画进行中其他层一直更新导致拥堵。
7. 也就是使用这些css属性来实现动画：transformation, opacity, filter
8. 使用性能工具检测优化的合理性，timeline检测优化是否合理，还需要实现自动操作来做性能回归测试。
9. 检测层数和层更新次数是非常有用的。

# 11.CSS中的层叠上下文和层叠顺序

### 零、世间的道理都是想通的

在这个世界上，凡事都有个先后顺序，凡物都有个论资排辈。比方说食堂排队打饭，对吧，讲求先到先得，总不可能一拥而上。再比如说话语权，老婆的话永远是对的，领导的话永远是对的。

在CSS届，也是如此。只是，一般情况下，大家歌舞升平，看不出什么差异，即所谓的众生平等。但是，当发生冲突发生纠葛的时候，显然，是不可能做到完全等同的，先后顺序，身份差异就显现出来了。例如，杰克和罗斯，只能一人浮在木板上，此时，出现了冲突，结果大家都知道的。那对于CSS世界中的元素而言，所谓的“冲突”指什么呢，其中，很重要的一个层面就是“层叠显示冲突”。

默认情况下，网页内容是没有偏移角的垂直视觉呈现，当内容发生层叠的时候，一定会有一个前后的层叠顺序产生，有点类似于真实世界中论资排辈的感觉。

而要理解网页中元素是如何“论资排辈”的，就需要深入理解CSS中的层叠上下文和层叠顺序。

我们大家可能都熟悉CSS中的`z-index`属性，需要跟大家讲的是，`z-index`实际上只是CSS层叠上下文和层叠顺序中的一叶小舟。

### 一、什么是层叠上下文

层叠上下文，英文称作”stacking context”. 是HTML中的一个三维的概念。如果一个元素含有层叠上下文，我们可以理解为这个元素在z轴上就“高人一等”。

这里出现了一个名词-**z轴**，指的是什么呢？

表示的是用户与屏幕的这条看不见的垂直线（参见下图示意-红线）：
![网页中z轴示意](https://image.zhangxinxu.com/image/blog/201601/z-aris.png)

层叠上下文是一个概念，跟「[块状格式化上下文(BFC)](http://www.zhangxinxu.com/wordpress/?p=4588)」类似。然而，概念这个东西是比较虚比较抽象的，要想轻松理解，我们需要将其具象化。

怎么个具象化法呢？

你可以**把「层叠上下文」理解为当官**：网页中有很多很多的元素，我们可以看成是真实世界的芸芸众生。真实世界里，我们大多数人是普通老百姓们，还有一部分人是做官的官员。OK，这里的“官员”就可以理解为网页中的层叠上下文元素。

换句话说，页面中的元素有了层叠上下文，就好比我们普通老百姓当了官，一旦当了官，相比普通老百姓而言，离皇帝更近了，对不对，就等同于网页中元素级别更高，离我们用户更近了。

![你懂的](https://image.zhangxinxu.com/image/emtion/point.gif)

### 二、什么是层叠水平

再来说说层叠水平。“层叠水平”英文称作”stacking level”，决定了同一个层叠上下文中元素在z轴上的显示顺序。level这个词很容易让我们联想到我们真正世界中的三六九等、论资排辈。真实世界中，每个人都是独立的个体，包括同卵双胞胎，有差异就有区分。例如，双胞胎虽然长得像Ctrl+C/Ctrl+V得到的，但实际上，出生时间还是有先后顺序的，先出生的那个就大，大哥或大姐。网页中的元素也是如此，页面中的每个元素都是独立的个体，他们一定是会有一个类似的排名排序的情况存在。而这个排名排序、论资排辈就是我们这里所说的“层叠水平”。层叠上下文元素的层叠水平可以理解为官员的职级，1品2品，县长省长之类；对于普通元素，这个嘛……你自己随意理解。

于是，显而易见，所有的元素都有层叠水平，包括层叠上下文元素，层叠上下文元素的层叠水平可以理解为官员的职级，1品2品，县长省长之类。然后，对于普通元素的层叠水平，我们的探讨仅仅局限在当前层叠上下文元素中。为什么呢？因为否则没有意义。

这么理解吧~ 上面提过元素具有层叠上下文好比当官，大家都知道的，这当官的家里都有丫鬟啊保镖啊管家啊什么的。所谓打狗看主人，A官员家里的管家和B官员家里的管家做PK实际上是没有意义的，因为他们牛不牛逼完全由他们的主子决定的。一人得道鸡犬升天，你说这和珅家里的管家和七侠镇娄知县县令家里的管家有可比性吗？李总理的秘书是不是分分钟灭了你村支部书记的秘书（如果有）。

翻译成术语就是：普通元素的层叠水平优先由层叠上下文决定，因此，层叠水平的比较只有在当前层叠上下文元素中才有意义。

![你懂的](https://image.zhangxinxu.com/image/emtion/point.gif)

需要注意的是，诸位千万不要把层叠水平和CSS的z-index属性混为一谈。没错，某些情况下z-index确实可以影响层叠水平，但是，只限于定位元素以及flex盒子的孩子元素；而层叠水平所有的元素都存在。

### 三、什么是层叠顺序

再来说说层叠顺序。“层叠顺序”英文称作”stacking order”. 表示元素发生层叠时候有着特定的垂直显示顺序，注意，这里跟上面两个不一样，上面的**层叠上下文和层叠水平是概念**，而这里的**层叠顺序是规则**。

在CSS2.1的年代，在CSS3还没有出现的时候（注意这里的前提），层叠顺序规则遵循下面这张图：
![层叠顺序](https://image.zhangxinxu.com/image/blog/201601/2016-01-07_223349.png)

有人可能有见过类似图，那个图是很多很多年前老外绘制的，英文内容。而是更关键的是国内估计没有同行进行过验证与实践，实际上很多关键信息缺失。上面是我自己手动重绘的中文版同时补充很多其他地方绝对没有的重要知识信息。

缺失的关键信息包括：

1. 位于最低水平的`border`/`background`指的是层叠上下文元素的边框和背景色。每一个层叠顺序规则适用于一个完整的层叠上下文元素。
2. 原图没有呈现inline-block的层叠顺序，实际上，inline-block和inline水平元素是同等level级别。
3. z-index:0实际上和z-index:auto单纯从层叠水平上看，是可以看成是一样的。注意这里的措辞——“单纯从层叠水平上看”，实际上，两者在层叠上下文领域有着根本性的差异。

下面我要向大家发问了，大家有没有想过，为什么内联元素的层叠顺序要比浮动元素和块状元素都高？
![疑问](https://image.zhangxinxu.com/image/emtion/ask.gif)
![层叠顺序元素的标注说明](https://image.zhangxinxu.com/image/blog/201601/2016-01-07_235108.png)

诸如`border`/`background`一般为装饰属性，而浮动和块状元素一般用作布局，而内联元素都是内容。网页中最重要的是什么？当然是内容了哈，对不对！

因此，一定要让内容的层叠顺序相当高，当发生层叠是很好，重要的文字啊图片内容可以优先暴露在屏幕上。例如，文字和浮动图片重叠的时候：

![浮动和文字重叠](https://image.zhangxinxu.com/image/blog/201601/2016-01-07_235830.jpg)

上面说的这些层叠顺序规则还是老时代的，如果把CSS3也牵扯进来。

### 四、务必牢记的层叠准则

下面这两个是层叠领域的黄金准则。当元素发生层叠的时候，其覆盖关系遵循下面2个准则：

1. **谁大谁上：**当具有明显的层叠水平标示的时候，如识别的z-indx值，在同一个层叠上下文领域，层叠水平值大的那一个覆盖小的那一个。通俗讲就是官大的压死官小的。
2. **后来居上：**当元素的层叠水平一致、层叠顺序相同的时候，在DOM流中处于后面的元素会覆盖前面的元素。

在CSS和HTML领域，只要元素发生了重叠，都离不开上面这两个黄金准则。因为后面会有多个实例说明，这里就到此为止。

### 五、层叠上下文的特性

层叠上下文元素有如下特性：

- 层叠上下文的层叠水平要比普通元素高（原因后面会说明）；
- 层叠上下文可以阻断元素的混合模式（见[此文第二部分说明](http://www.zhangxinxu.com/wordpress/?p=5155)）；
- 层叠上下文可以嵌套，内部层叠上下文及其所有子元素均受制于外部的层叠上下文。
- 每个层叠上下文和兄弟元素独立，也就是当进行层叠变化或渲染的时候，只需要考虑后代元素。
- 每个层叠上下文是自成体系的，当元素发生层叠的时候，整个元素被认为是在父层叠上下文的层叠顺序中。

翻译成真实世界语言就是：

- 当官的比老百姓更有机会面见圣上；
- 领导下去考察，会被当地官员阻隔只看到繁荣看不到真实民情；
- 一个家里，爸爸可以当官，孩子也是可以同时当官的。但是，孩子这个官要受爸爸控制。
- 自己当官，兄弟不占光。有什么福利或者变故只会影响自己的孩子们。
- 每个当官的都有属于自己的小团体，当家眷管家发生摩擦磕碰的时候（包括和其他官员的家眷管家），都是要优先看当官的也就是主子的脸色。

### 六、层叠上下文的创建

卖了这么多文字，到底层叠上下文是个什么鬼，倒是拿出来瞅瞅啊！

哈哈。如同块状格式化上下文，层叠上下文也基本上是有一些特定的CSS属性创建的。我将其总结为3个流派，也就是做官的3种途径：

1. **皇亲国戚**派：页面根元素天生具有层叠上下文，称之为“根层叠上下文”。
2. **科考入选**派：z-index值为数值的定位元素的传统层叠上下文。
3. **其他当官途径**：其他CSS3属性。

//zxx: 下面很多例子是实时CSS效果，建议您去[原地址浏览](http://www.zhangxinxu.com/wordpress/?p=5115)，以便预览更准确的效果。

**①. 根层叠上下文**
指的是页面根元素，也就是滚动条的默认的始作俑者`<html>`元素。这就是为什么，绝对定位元素在`left`/`top`等值定位的时候，如果没有其他定位元素限制，会相对浏览器窗口定位的原因。

**②. 定位元素与传统层叠上下文**
对于包含有`position:relative`/`position:absolute`的定位元素，以及FireFox/IE浏览器（不包括Chrome等webkit内核浏览器）（目前，也就是2016年初是这样）下含有`position:fixed`声明的定位元素，当其`z-index`值不是`auto`的时候，会创建层叠上下文。

知道了这一点，有些现象就好理解了。

如下HTML代码：

```
<div style="position:relative; z-index:auto;">
    <img src="mm1.jpg" style="position:absolute; z-index:2;">    <-- 横妹子 -->
</div>
<div style="position:relative; z-index:auto;">
    <img src="mm2.jpg" style="position:relative; z-index:1;">    <-- 竖妹子 -->
</div>
```

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

![img](https://image.zhangxinxu.com/image/study/s/s256/mm2.jpg)

大家会发现，竖着的妹子(mm2)被横着的妹子(mm1)给覆盖了。

下面，我们对父级简单调整下，把`z-index:auto`改成层叠水平一致的`z-index:0`, 代码如下：

```
<div style="position:relative; z-index:0;">
    <img src="mm1.jpg" style="position:absolute; z-index:2;">    <-- 横妹子 -->
</div>
<div style="position:relative; z-index:0;">
    <img src="mm2.jpg" style="position:relative; z-index:1;">    <-- 竖妹子 -->
</div>
```

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

![img](https://image.zhangxinxu.com/image/study/s/s256/mm2.jpg)

大家会发现，尼玛反过来了，竖着的妹子(mm2)这回趴在了横着的妹子(mm1)身上。

差别就在于，`z-index:0`所在的`<div>`元素是层叠上下文元素，而`z-index:auto`所在的`<div>`元素是一个普通的元素，于是，里面的两个`<img>`妹子的层叠比较就不受父级的影响，两者直接套用层叠黄金准则，这里，两者有着明显不一的`z-index`值，因此，遵循“**谁大谁上**”的准则，于是，`z-index`为`2`的那个横妹子，就趴在了`z-index`为`1`的竖妹子身上。

而`z-index`一旦变成数值，哪怕是`0`，都会创建一个层叠上下文。此时，层叠规则就发生了变化。层叠上下文的特性里面最后一条——自成体系。两个`<img>`妹子的层叠顺序比较变成了优先比较其父级层叠上下文元素的层叠顺序。这里，由于两者都是`z-index:0`，层叠顺序这一块两者一样大，此时，遵循层叠黄金准则的另外一个准则“**后来居上**”，根据在DOM流中的位置决定谁在上面，于是，位于后面的竖着的妹子就自然而然趴在了横着的妹子身上。对，没错，`<img>`元素上的`z-index`打酱油了！

有时候，我们在网页重构的时候，会发现，`z-index`嵌套错乱，看看是不是受父级的层叠上下文元素干扰了。然后，可能没多大意义了，但我还是提一下，算是祭奠下，IE6/IE7浏览器有个bug，就是`z-index:auto`的定位元素也会创建层叠上下文。这就是为什么在过去，IE6/IE7的`z-index`会搞死人的原因。

然后，我再提一下`position:fixed`, 在过去，`position:fixed`和`relative/absolute`在层叠上下文这一块是一路货色，都是需要`z-index`为数值才行。但是，不知道什么时候起，Chrome等webkit内核浏览器，`position:fixed`元素天然层叠上下文元素，无需`z-index`为数值。根据我的测试，目前，IE以及FireFox仍是老套路。

**③. CSS3与新时代的层叠上下文**
CSS3的出现除了带来了新属性，同时还对过去的很多规则发出了挑战。例如，CSS3 `transform`[对overflow隐藏对position:fixed定位的影响](http://www.zhangxinxu.com/wordpress/2015/05/css3-transform-affect/)等。而这里，层叠上下文这一块的影响要更加广泛与显著。

如下：

1. `z-index`值不为`auto`的`flex`项(父元素`display:flex|inline-flex`).
2. 元素的`opacity`值不是`1`.
3. 元素的`transform`值不是`none`.
4. 元素`mix-blend-mode`值不是`normal`.
5. 元素的`filter`值不是`none`.
6. 元素的`isolation`值是`isolate`.
7. `will-change`指定的属性值为上面任意一个。
8. 元素的`-webkit-overflow-scrolling`设为`touch`.

基本上每一项都有很多槽点。

**1. display:flex|inline-flex与层叠上下文**
注意，这里的规则有些~~负责~~复杂。要满足两个条件才能形成层叠上下文：条件1是父级需要是`display:flex`或者`display:inline-flex`水平，条件2是子元素的z-index不是`auto`，必须是数值。此时，这个子元素为层叠上下文元素，没错，注意了，是子元素，不是flex父级元素。

眼见为实，给大家上例子吧。

如下HTML和CSS代码：

```
<div class="box">
    <div>
    	<img src="mm1.jpg">
    </div>
</div>
.box {  }
.box > div { background-color: blue; z-index: 1; }    /* 此时该div是普通元素，z-index无效 */
.box > div > img { 
  position: relative; z-index: -1; right: -150px;     /* 注意这里是负值z-index */
}
```

结果如下：

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

会发现，妹子跑到蓝色背景的下面了。为什么呢？层叠顺序图可以找到答案，如下：
![负值z-index的层叠顺序](https://image.zhangxinxu.com/image/blog/201601/2016-01-08_235511.png)

从上图可以看出负值z-index的层叠顺序在block水平元素的下面，而蓝色背景`div`元素是个普通元素，因此，妹子直接穿越过去，在蓝色背景后面的显示了。

现在，我们CSS微调下，增加`display:flex`, 如下：

```
.box { display: flex; }
.box > div { background-color: blue; z-index: 1; }    /* 此时该div是层叠上下文元素，同时z-index生效 */
.box > div > img { 
  position: relative; z-index: -1; right: -150px;     /* 注意这里是负值z-index */
}
```

结果：

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

会发现，妹子在蓝色背景上面显示了，为什么呢？层叠顺序图可以找到答案，如下：
![img](https://image.zhangxinxu.com/image/blog/201601/2016-01-08_235217.png)

从上图可以看出负值`z-index`的层叠顺序在当前第一个父层叠上下文元素的上面，而此时，那个`z-index`值为`1`的蓝色背景`<div>`的父元素的`display`值是`flex`，一下子升官发财变成层叠上下文元素了，于是，图片在蓝色背景上面显示了。这个现象也证实了层叠上下文元素是`flex`子元素，而不是`flex`容器元素。

另外，另外，这个例子也颠覆了我们传统的对`z-index`的理解。在CSS2.1时代，`z-index`属性必须和定位元素一起使用才有作用，但是，在CSS3的世界里，非定位元素也能和`z-index`愉快地搞基。

**2. opacity与层叠上下文**
我们直接看代码，原理和上面例子一样，就不解释了。

如下HTML和CSS代码：

```
<div class="box">
    <img src="mm1.jpg">
</div>
.box { background-color: blue;  }
.box > img { 
  position: relative; z-index: -1; right: -150px;
}
```

结果如下：

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

然后价格透明度，例如50%透明：

```
.box { background-color: blue; opacity: 0.5;  }
.box > img { 
  position: relative; z-index: -1; right: -150px;
}
```

结果如下：

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

原因就是半透明元素具有层叠上下文，妹子图片的`z-index:-1`无法穿透，于是，在蓝色背景上面乖乖显示了。

**3. transform与层叠上下文**
应用了[transform变换](http://www.zhangxinxu.com/wordpress/2010/11/css3-transitions-transforms-animation-introduction/)的元素同样具有菜单上下文。

我们直接看应用后的结果，如下CSS代码：

```
.box { background-color: blue; transform: rotate(15deg);  }
.box > img { 
  position: relative; z-index: -1; right: -150px;
}
```

结果如下：

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

妹子同样在蓝色背景之上。

**4. mix-blend-mode与层叠上下文**
`mix-blend-mode`类似于PS中的混合模式，之前专门有文章介绍-“[CSS3混合模式mix-blend-mode简介](http://www.zhangxinxu.com/wordpress/2015/05/css3-mix-blend-mode-background-blend-mode/)”。

元素和白色背景混合。无论哪种模式，要么全白，要么没有任何变化。为了让大家有直观感受，因此，下面例子我特意加了个原创平铺背景：

```
.box { background-color: blue; mix-blend-mode: darken;  }
.box > img { 
  position: relative; z-index: -1; right: -150px;
}
```

结果如下：

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

需要注意的是，目前，IE浏览器(包括IE14)还不支持`mix-blend-mode`，因此，要想看到妹子在背景色之上，请使用Chrome或FireFox。

同样的，因为蓝色背景元素升级成了层叠上下文，因此，`z-index:-1`无法穿透，在蓝色背景上显示了。

**5. filter与层叠上下文**
此处说的`filter`是CSS3中规范的滤镜，不是旧IE时代私有的那些，虽然目的类似。同样的，我之前有提过，例如[图片的灰度](http://www.zhangxinxu.com/wordpress/2012/08/css-svg-filter-image-grayscale/)或者[图片的毛玻璃效果](http://www.zhangxinxu.com/wordpress/2013/11/css-svg-image-blur/)等。

我们使用常见的模糊效果示意下：

```
.box { background-color: blue; filter: blur(5px);  }
.box > img { 
  position: relative; z-index: -1; right: -150px;
}
```

结果如下：

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

好吧，果然被你猜对了，妹子蓝色床上躺着，只是你眼镜摘了，看得有些不够真切罢了。

**6. isolation:isolate与层叠上下文**
`isolation:isolate`这个声明是`mix-blend-mode`应运而生的。默认情况下，`mix-blend-mode`会混合z轴所有层叠在下面的元素，要是我们不希望某个层叠的元素参与混合怎么办呢？就是使用`isolation:isolate`。由于一言难尽，我特意为此写了篇文章：“[理解CSS3 isolation: isolate的表现和作用](http://www.zhangxinxu.com/wordpress/?p=5155)”，解释了其阻隔混合模式的原理，建议大家看下。

要演示这个效果，我需要重新设计下，如下HTML结构：

```
<img src="img/mm2.jpg" class="mode">
<div class="box">
    <img src="mm1.jpg">
</div>
```

CSS主要代码如下：

```
.mode {
  /* 竖妹子绝对定位，同时混合模式 */
  position: absolute; mix-blend-mode: darken;
}    
.box {
  background: blue;         
}
.box > img { 
  position: relative; z-index: -1;
}
```

结构如下：

![img](https://image.zhangxinxu.com/image/study/s/s256/mm2.jpg)

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

会发现，横妹子被混合模式了。此时，我们给妹子所在容器增加`isolation:isolate`，如下CSS所示：

```
.mode {
  /* 竖妹子绝对定位，同时混合模式 */
  position: absolute; mix-blend-mode: darken;
}    
.box {
  background: blue; isolation:isolate;         
}
.box > img { 
  position: relative; z-index: -1;
}
```

结果为：

![img](https://image.zhangxinxu.com/image/study/s/s256/mm2.jpg)

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

会发现横着的妹子跑到蓝色背景上面了。这表明确实创建了层叠上下文。

**7. will-change与层叠上下文**
关于`will-change`，如果有同学还不了解，可以参见我之前写的文章：“[使用CSS3 will-change提高页面滚动、动画等渲染性能](http://www.zhangxinxu.com/wordpress/2015/11/css3-will-change-improve-paint/)”。

都是类似的演示代码：

```
.box { background-color: blue; will-change: transform;  }
.box > img { 
  position: relative; z-index: -1; right: -150px;
}
```

结果如下：

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

果然不出所料，妹子上了蓝色的背景。

### 七、层叠上下文与层叠顺序

本文多次提到，一旦普通元素具有了层叠上下文，其层叠顺序就会变高。那它的层叠顺序究竟在哪个位置呢？

这里需要分两种情况讨论：

1. 如果层叠上下文元素不依赖`z-index`数值，则其层叠顺序是`z-index:auto`可看成`z:index:0`级别；
2. 如果层叠上下文元素依赖`z-index`数值，则其层叠顺序由`z-index`值决定。

于是乎，我们上面提供的层叠顺序表，实际上还是缺少其他重要信息。我又花功夫重新绘制了一个更完整的7阶层叠顺序图（同样的版权所有，商业请购买，可得无水印大图）：

![更完整的7阶层叠顺序图](https://image.zhangxinxu.com/image/blog/201601/2016-01-09_211116.png)

大家知道为什么定位元素会层叠在普通元素的上面吗？

其根本原因就在于，元素一旦成为定位元素，其`z-index`就会自动生效，此时其`z-index`就是默认的`auto`，也就是`0`级别，根据上面的层叠顺序表，就会覆盖`inline`或`block`或`float`元素。

而不支持z-index的层叠上下文元素天然`z-index:auto`级别，也就意味着，层叠上下文元素和定位元素是一个层叠顺序的，于是当他们发生层叠的时候，遵循的是“后来居上”准则。

我们可以速度测试下：

```
<img src="mm1" style="position:relative">
<img src="mm2" style="transform:scale(1);">
<img src="mm2" style="transform:scale(1);">
<img src="mm1" style="position:relative">
```

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)![img](https://image.zhangxinxu.com/image/study/s/s256/mm2.jpg)
![img](https://image.zhangxinxu.com/image/study/s/s256/mm2.jpg)![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

会发现，两者样式一模一样，仅仅是在DOM流中的位置不一样，导致他们的层叠表现不一样，后面的妹子趴在了前面妹子的身上。这也说明了，层叠上下文元素的层叠顺序就是`z-index:auto`级别。

**z-index值与层叠顺序**
如果元素支持z-index值，则层叠顺序就要好理解些了，比较数值大小嘛，小盆友都会，本质上是应用的“谁大谁上”的准则。在以前，我们只需要关心定位元素的z-index就好，但是，在CSS3时代，flex子项也支持`z-index`，使得我们面对的情况比以前要负复杂。然而，好的是，规则都是一样的，对于`z-index`的使用和表现也是如此，套用上面的7阶层叠顺序表就可以了。

同样，举个简单例子，看下`z-index:-1`和`z-index:1`变化对层叠表现的影响，如下两段HTML：

```
<div style="display:flex; background:blue;">
   <img src="mm1.jpg" style="z-index:-1;">
</div>
<div style="display:flex; background:blue;">
   <img src="mm1.jpg" style="z-index:1;">
</div>
```

最后，会发现，`z-index:-1`跑到了背景色小面，而`z-index:1`高高在上。

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

![img](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)

**一个与层叠上下文相关的有趣的显示现象**
在实际项目中，我们可能会渐进使用CSS3的fadeIn淡入animation效果增强体验，于是，我们可能就会遇到类似下面的现象：

您可以狠狠地点击这里：[CSS3 fadeIn淡入animation动画有趣现象](http://www.zhangxinxu.com/study/201601/css3-fadein-animation-stacking-context.html)

有一个绝对定位的黑色半透明层覆盖在图片上，默认显示是这样的：

![文字在妹子上](https://image.zhangxinxu.com/image/blog/201801/2018-01-09_004641.png)

但是，一旦图片开始走fadeIn淡出的CSS3动画，文字跑到图片后面去了![img](https://mat1.gtimg.com/www/mb/images/face/36.gif)：

![文字跑到图片后面](https://image.zhangxinxu.com/image/blog/201801/2018-01-09_004654.png)

为什么会这样？

实际上，学了本文的内容，就很简单了！fadeIn动画本质是`opacity`透明度的变化：

```
@keyframes fadeIn {
  0% { 
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
```

要知道，`opacity`的值不是`1`的时候，是具有层叠上下文的，层叠顺序是`z-index:auto`级别，跟没有`z-index`值的`absolute`绝对定位元素是平起平坐的。而本demo中的文字元素在图片元素的前面，于是，当CSS3动画只要不是最终一瞬间的`opacity: 1`，位于DOM流后面的图片就会遵循“后来居上”准则，覆盖文字。

这就是原因，于是，我们想要解决这个问题就很简单。

1. 调整DOM流的先后顺序；
2. 提高文字的层叠顺序，例如，设置`z-index:1`;

### 八、结束语

只要元素发生层叠，要解释其表现，基本上就本文的这些内容了。

我发现很多重构小伙伴都有z-index滥用，或者使用不规范的问题。我觉得最主要的原因还是对理解层叠上下文以及层叠顺序这些概念都不了解。例如，只要使用了定位元素，尤其`absolute`绝对定位，都离不开设置一个`z-index`值；或者只要元素被其他元素覆盖了，例如变成定位元素或者增加`z-index`值升级。页面一复杂，必然搞得乱七八糟。

实际上，在我看来，觉得多数常见，z-index根本就没有出现的必要。知道了内联元素的层叠水平比块状元素高，于是，某条线你想覆盖上去的时候，需要设置`position:relative`吗？不需要，`inline-block`化就可以。因为IE6/IE7 `position:relative`会创建层叠上下文，很烦的。

OK，本文已经够长了，就不多啰嗦了。

行为匆忙，出错在所难免，欢迎大力指正。也欢迎各种形式的交流，或者指出文中概念性的错误。

感谢阅读！

# 12.div居中

## 使div水平垂直居中

### 1. flex 布局实现 （元素已知宽度）

效果图：

![img](https://user-gold-cdn.xitu.io/2019/4/15/16a1eccc17f7ff91?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

内部 div 要有宽度

```
CSS 代码:
<style>        
    .box{            
        width: 300px;            
        height: 300px;           
        background-color: #ccc;            
        display: flex;            
        display: -webkit-flex;            
        justify-content: center;            
        align-items: center;        
    }        
    .box .a{            
        width: 100px;            
        height: 100px;            
        background-color: blue;        
    }    
</style>
HTML 代码：
<div class="box">        
    <div class="a"></div>    
</div>
```

### 2. position （元素已知宽度）

​            父元素设置为：position: relative;

​            子元素设置为：position: absolute;

​            距上50%，据左50%，然后减去元素自身宽度的一半距离就可以实现

效果图：

![img](https://user-gold-cdn.xitu.io/2019/4/15/16a1ecd08d952a7e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

```
CSS代码：
<style>        
    .box{            
        width: 300px;            
        height: 300px;            
        background-color: red;            
        position: relative;        
    }        
    .box .a{            
        width: 100px;            
        height: 100px;            
        background-color: blue;            
        position: absolute;            
        left: 50%;            
        top: 50%;            
        margin: -50px 0 0 -50px;        
    }    
    </style>

HTML 代码：
<div class="box">        
    <div class="a">love</div>    
</div>
```

### 3. position transform （元素未知宽度）

如果元素未知宽度，只需将上面例子中的` margin: -50px 0 0 -50px;`替换为：**`transform: translate(-50%,-50%);`**

效果图：

![img](https://user-gold-cdn.xitu.io/2019/4/15/16a1ecf67eb4c5a5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)







```
CSS 代码：
<style>        
    .box{            
        width: 300px;            
        height: 300px;            
        background-color: red;            
        position: relative;        
    }        
    .box .a{            
        background-color: blue;            
        position: absolute;            
        top: 50%;            
        left: 50%;            
        transform: translate(-50%, -50%);        
    }    
</style>
```

### 4. position（元素已知宽度）（left，right，top，bottom为0，maigin：auto ）

效果图：

![img](https://user-gold-cdn.xitu.io/2019/4/15/16a1ed33c6187453?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

```
CSS 代码：
<style>        
    .box{            
        width: 300px;            
        height: 300px;           
        background-color: red;            
        position: relative;        
    }        
    .box .a{            
        width: 100px;            
        height: 100px;            
        background-color: blue;            
        position: absolute;            
        top: 0;            
        bottom: 0;            
        left: 0;            
        right: 0;            
        margin: auto;        
    }    
</style>
HTML 代码：
 <div class="box">        
    <div class="a">love</div>    
</div>
```

### ★第四种情况方案中，如果子元素不设置宽度和高度，将会铺满整个父级（应用：模态框）

效果图：

![img](https://user-gold-cdn.xitu.io/2019/4/15/16a1ed5caebd6979?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

```
CSS 代码：
<style>        
    .box{            
        width: 300px;            
        height: 300px;            
        background-color: red;            
        position: relative;        
    }        
    .box .a{            
        /* 如果不设置宽高，将铺满整个父级*/            
        background-color: pink;            
        position: absolute;            
        left: 0;            
        right: 0;            
        top: 0;            
        bottom: 0;            
        margin: auto;            
        text-align: center;                    
    }    
</style>
HTML:
<div class="box">
    <div class="a">love</div>
</div>
```

### 5. table-cell 布局实现

table 实现垂直居中，子集元素可以是块元素，也可以不是块元素

```
CSS：
<style>        
    .box{            
        width: 300px;            
        height: 300px;            
        background-color: red;            
        display: table-cell;            
        vertical-align: middle;                    
    }        
    .box .a{            
        margin-left: 100px;            
        width: 100px;            
        height: 100px;            
        background-color: blue;        
    }    
</style>

<div class="box">         
    <div class="a">love</div>    
</div>
```

## 使内容（文字，图片）水平垂直居中（table-cell 布局）

行元素 text-align ：center；

块元素 ：margin ：0 auto；

```
text-align : center  给元素的父级加，可以使文本或者行级元素(例如：图片)水平居中
line-height : 值为元素的高度，可以使元素的文本内容垂直居中
margin: 0 auto 使用条件：父级元素宽度可有可无，子级元素必须使块元素，而且要有宽度（否则继承父级）
```

`display：table-cell `会使元素表现的类似一个表格中的单元格td，利用这个特性可以实现文字的垂直居中效果。同时它也会破坏一些 CSS 属性，使用 table-cell 时最好不要与 float 以及 position: absolute 一起使用，设置了 table-cell 的元素对高度和宽度高度敏感，对margin值无反应，可以响 padding 的设置，表现几乎类似一个 td 元素。

小结： 

1. 不要与 float：left， position : absolute， 一起使用 

2. 可以实现大小不固定元素的垂直居中 

3. margin 设置无效，响应 padding 设置 

4. 对高度和宽度高度敏感 

5. 不要对 display：table-cell 使用百分比设置宽度和高度

### 应用：

### 1. 使文字水平垂直居中

效果图：

![img](https://user-gold-cdn.xitu.io/2019/4/15/16a1ee843df854e4?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

```
CSS 代码：
<style>        
    .box{            
        width: 300px;            
        height: 300px;            
        background-color: red;            
        display: table-cell;            
        text-align: center;/*使元素水平居中 */            
        vertical-align: middle;/*使元素垂直居中 */        
    }    
</style>

HTML 代码：
<div class="box">love</div>
```

给父级设置 display : table，子集设置 display：tablecell ，子集会充满全屏

![img](https://user-gold-cdn.xitu.io/2019/4/15/16a1eed995e17eef?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

```
CSS 代码：
<style>        
    .box{            
        width: 300px;            
        height: 300px;            
        background-color: red;            
        display: table;        
    }        
    .box .a{            
        display: table-cell;            
        vertical-align: middle;            
        text-align: center;            
        background-color: blue;        
    }    
</style>

HTML ：
<div class="box">        
    <div class="a">love</div>    
</div>
```

### 2. 图片水平垂直居中

效果图：

![img](https://user-gold-cdn.xitu.io/2019/4/15/16a1f0a0dfeb3002?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

```
<style>        
    .box{            
        width: 300px;            
        height: 300px;            
        background-color: skyblue;            
        display: table-cell;            
        text-align: center;            
        vertical-align: middle;        
    }        
    img{            
        /* 设置成块元素后，text-align：center 就会失效 */            
        width: 100px;            
        height: 100px;        
    }    
</style>

HTML：
<div class="box">    
    <img src="1.jpg" alt="">
</div>
```

中间的图片会随着外层容器的大小而自动水平垂直居中，其实原理和文字水平垂直居中一模一样

### 3. 元素两端垂直对齐

```
CSS 代码：
<style>        
    *{            
        padding: 0;            
        margin: 0;        
    }        
    .box{            
        display: table;            
        width: 90%;            
        margin: 10px  auto;            
        padding: 10px;             
        border: 1px solid green;            
        height: 100px;        
    }        
    .left,.right{            
        display: table-cell;                        
        width: 20%;            
        border: 1px solid red;                 
    }        
    .center{            
        /* padding-top: 10px; */            
        height: 100px;            
        background-color: green;        
    }    
</style>

HTML：
<div class="box">        
    <div class="left">            
        <p>我是左边</p>        
    </div>        
    <div class="center">            
        <p>我是中间</p>       
    </div>        
    <div class="right">            
        <p>我是右边</p>        
    </div>    
</div>
```

效果：![img](https://user-gold-cdn.xitu.io/2019/4/15/16a1f1e596191dfa?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



其中 center 的顶部没有与左右两侧对齐，原因是 left 中的 <p> 有一个 margin-top ， 而表格布局中默认是文字顶部对齐的，所以 center 会向下移动到首行文字基线对齐，解决办法是为 center 添加与左右两侧中 margin-top 较大者等值的 padding-top 即可。 

# 13.[CSS浮动](https://segmentfault.com/a/1190000012739764)

页面布局是CSS的一个重点应用，例如：
![图片描述](https://segmentfault.com/img/bVuVFG)

而实现页面布局主要应用到两种方法，一种是**CSS浮动**，一种是**Flexbox**（IE9以上），本文主要讲的是CSS浮动，下一篇文章将阐述Flexbox。

## 浮动元素

**什么是浮动元素：**浮动元素同时处于常规流内和流外的元素。其中块级元素认为浮动元素不存在，而浮动元素会影响行内元素的布局，浮动元素会通过影响行内元素间接影响了包含块的布局。

> **常规流：**页面上从左往右，从上往下排列的元素流，就是常规流
> **脱离常规流：**绝对定位，fixed定位的元素有自己固定的位置，脱离了常规流
> **包含块：**一个元素离它最近的块级元素是它的包含块

下面详细描述以上的内容，举个例子：

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>JS Bin</title>
</head>
<style>
.border {
  border: 2px solid;
}

.common-div {
  width: 160px;
  height: 50px;
  background-color: red;
}

.float-red {
  width: 100px;
  height: 50px;
  background-color: #fcc;
  float: left;
}

.float-blue {
  width: 100px;
  height: 50px;
  background-color: #09c;
  float: left;
}
</style>
<body>
    <div class="border">
        <div class="float-red"></div>
        <div class="common-div"></div>
        <div class="float-blue"></div>
        Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
    </div>
</body>
</html>
```

上面这段代码显示的样子如下：
![图片描述](https://segmentfault.com/img/bV1Cjj?w=1482&h=394)

- 块级元素认为浮动元素不存在：红色的块级元素没有受到粉色浮动元素的影响，还展示在左上角的位置，但是被粉色元素盖住了左边的部分
- 浮动元素会影响行内元素：文字部分被蓝色浮动元素影响，空出了蓝色浮动元素的部分
- 浮动元素会间接影响了包含块的布局：浮动元素影响了文字部分吗，使之多出了一行，文字部分撑高了最外面的border框，所以间接影响了包含块的布局。

其中，**浮动元素的摆放**会遵循如下的规则：

- 尽量靠上
- 尽量靠左
- 尽量一个挨着一个
- 不能超出包含块，除非元素比包含块更宽
- 不能超过所在行的最高点
- 不能超过它前面浮动元素的最高点
- 行内元素绕着浮动元素摆放：左浮动元素的右边和右浮动元素的左边会出浮动元素

## 应用

> 浮动元素并不能撑起包含块，这和我们的预期并不相符。通过以下的办法可以将包含块撑开，称之为**闭合浮动**

![图片描述](https://segmentfault.com/img/bV1CkU?w=798&h=248)
![图片描述](https://segmentfault.com/img/bV1Ck1?w=802&h=248)

### 闭合浮动的方法：

- **BFC:** 1) 包含块设置overflow:hidden 或者 2)包含块设置display:table-cell/table/flex...

> **BFC：块级格式化上下文。它是一个独立的渲染区域，只有Block-level box参与， 它规定了内部的块级元素如何布局，并且与这个区域外部毫不相干。**
> 只要**符合以下的条件就是BFC:**
> 1) 根元素
> 2) float属性不为none
> 3) position为absolute或fixed
> 4) display为inline-block, table-cell, table-caption, flex, inline-flex
> 5) overflow不为visible

相应的背景文档可以参阅：[《BFC的神奇原理》](http://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html)

- **伪元素**：

```
.clearfix::after {
    content: '';
    display: block;
    clear: both;    
}
```

> clear:both;意味着块级元素的左边和右边都不能有浮动元素。在包含块的末尾建立了一个内容为空的伪元素，并设置clear:both，使这个元素位于所有的浮动元素之后，从而撑开了包含块的高。

- **包含块自己也浮动**

这个方法也是w3c使用的方法。不过，下一个元素会受到这个浮动元素的影响。为了解决这个问题，有些人选择对布局中的所有东西进行浮动，然后使用适当的有意义的元素（常常是站点的页脚）对这些浮动进行清理。这有助于减少或消除不必要的标记。

相应的背景文档：[《W3C CSS浮动》](http://www.w3school.com.cn/css/css_positioning_floating.asp)

##