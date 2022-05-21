---
title:  GRID GARDEN
date:  2022-04-23 22:30:00
categories: 
- 前端
tags:
- css
- grid布局
- 答案
---
grid布局可以创建任意数量的网格。fraction unit 使得控制布局比例非常方便，repeat可以重复布局。grid布局基于最简原则，只需要定义需要使用的行和列，网格支持命名。[Grid Garden](http://cssgridgarden.com/)是透过闯关游戏的方式来熟悉grid的语法，还蛮有趣的，也推荐给大家。

<!-- more -->

### 1

![image-20220423220955190](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220423220955.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-column-start: 3;
}
```

### 2

![image-20220423221039674](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220423221039.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-column-start: 3;
}
```

### 3

![image-20220423221215354](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220423221215.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-column-start: 1;
    grid-column-end: 4;
}
```

### 4

![image-20220423221409490](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220423221409.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-column-start: 5;
    grid-column-end: 2;
}
```

### 5

![image-20220425205636865](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220425205643.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-column-start: 1;
    grid-column-end: -2;
}
```

### 6

![image-20220425210333197](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220425210333.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#poison {
    grid-column-start: -3;
}
```

### 7

![image-20220425210552881](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220425210553.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-column-start: 2;
    grid-column-end: span 2;
}
```

### 8

![image-20220425211010061](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220425211010.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-column-start: 1;
    grid-column-end: span 5;
}
```

### 9

![image-20220425220240013](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220425220240.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-column-start: span 3;
    grid-column-end: 6;
}
```

### 10

![image-20220425222420299](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220425222420.png)

```css
#garden {
  display: grid;
  grid-template-columns: 20% 20% 20% 20% 20%;
  grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
	grid-column: 4 / 6;
}
```

### 11

![image-20220426102055082](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426102318.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-column: 2 / 5;
}
```

### 12

![image-20220426112632075](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426112632.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-row-start: 3;
}
```

### 13

![image-20220426120016547](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426120016.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-row: 3/ 6;
}
```

### 14

![image-20220426152812610](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426152812.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#poison {
    grid-row: 5/ 6;
    grid-column: 2 / 3;
}
```

### 15

![image-20220426155332494](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426155332.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-row: 1 / span 5;
    grid-column: 2 / span 4;
}
```

### 16

![image-20220426161418732](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426161418.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-area: 1 / 2 / 4 / 6;
}
```

### 17

![image-20220426162808141](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426162808.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-area: 1 / 2 / 4 / 6;
}
```

### 18

![image-20220426163910866](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426163911.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

.water {
    order: 0;
}

#poison {
    order: 1;
}
```

### 19

![image-20220426165220603](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426165220.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

.water {
    order: 0;
}

.poison {
    order: -1;
}
```

### 20

![image-20220426171229442](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426171229.png)

```css
#garden {
    display: grid;
    grid-template-columns: 50% 1fr 1fr 1fr 1fr;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-column: 1;
    grid-row: 1;
}
```

### 21

![image-20220426172316733](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426172316.png)

```css
#garden {
    display: grid;
    grid-template-columns: repeat(8, 12.5%);
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-column: 1;
    grid-row: 1;
}
```

### 22

![image-20220426183202538](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426183202.png)

```css
#garden {
    display: grid;
    grid-template-columns: 100px 3em 40%;
    grid-template-rows: 20% 20% 20% 20% 20%;
}
```

### 23

![image-20220426184818674](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426184818.png)

```css
#garden {
    display: grid;
    grid-template-columns: 1fr 5fr;
    grid-template-rows: 20% 20% 20% 20% 20%;
}
```

### 24

![image-20220426185349368](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426185458.png)

```css
#garden {
    display: grid;
    grid-template-columns: 50px repeat(3, 1fr) 50px;
    grid-template-rows: 20% 20% 20% 20% 20%;
}

#water {
    grid-area: 1 / 1 / 6 / 2;
}

#poison {
    grid-area: 1 / 5 / 6 / 6;
}
```

### 25

![image-20220426191424091](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426191424.png)

```css
#garden {
    display: grid;
    grid-template-columns: 75px 3fr 2fr
  	grid-template-rows: 100%;
}
```

### 26

![image-20220426192352700](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426192353.png)

```css
#garden {
    display: grid;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-columns: 20% 20% 20% 20% 20%;
    grid-template-rows: 50px repeat(3, 0) 1fr;
}

#water {
    grid-column: 1 / 6;
    grid-row: 5 / 6;
}
```

### 27

![image-20220426193036172](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426193036.png)

```css
#garden {
    display: grid;
    grid-template: 60% 40% / 200px 1fr
}

#water {
    grid-column: 1;
    grid-row: 1;
}
```

### 28

![image-20220426193203143](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220426193203.png)

```css
#garden {
    display: grid;
    grid-template: 1fr 50px / 1fr 4fr;
}
```

