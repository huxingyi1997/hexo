---
title:  Tic Tac Toe Extended 强化版井字棋
date:  2022-04-15 11:30:00
categories: 
- 计算机基础知识
tags:
- React
- 前端
- 项目

---
React的[官方教程](https://react.docschina.org/tutorial/tutorial.html)中就使用了Tic Tac Toe（井字棋）这款游戏作为实战教程引导大家使用React，我自己复现并针对官网教程总结部分的[更多要求](https://react.docschina.org/tutorial/tutorial.html#wrapping-up)部分进行了复现，最终得到了完整的[Tic Tac Toe游戏](https://hxy1997.xyz/tic-tac-toe/)。玩过之后怅然若失，也很想要找一些自己感兴趣的项目来磨刀。想到小时候常玩的五子棋游戏，忍不住手痒，很想要自己复现一个，仔细一想了Tic Tac Toe游戏本质上就是五子棋的简化版，为了做出一些小改变来增加趣味性，因此决定来做一个伸缩自如的了Tic Tac Toe  Extended游戏。

<!-- more -->

# 基础版Tic Tac Toe游戏简介

Tic Tac Toe（井字棋）游戏规则：
两个玩家，一个打圈(◯)，一个打叉(✗)，轮流在3乘3的格上打自己的符号，最先以横、直、斜连成一线则为胜。如果双方都下得正确无误，将得和局。

参考：[井字棋介绍](https://zh.wikipedia.org/wiki/%E4%BA%95%E5%AD%97%E6%A3%8B)

[我的Tic Tac Toe](https://hxy1997.xyz/tic-tac-toe/)

# Tic Tac Toe  Extended游戏功能构想

我们这次要做的是伸缩自如的Tic Tac Toe  Extended游戏，所以我希望有下列功能：

1. 棋盘大小可以按照玩家自己设定，例如3x3, 4x4, 5x5….，爱有多大，就有多大。
2. 连成一条直线的胜利条件可以自己设定，意思就是说，可以按自己心意设定连续3子获胜，或是连续5子获胜，直接变成真正的五子棋！(虽然说五子棋好像都是下在棋盘的十字上，我们是下在空格里，但是好像不会影响游戏进行，所以这边先忽略这些细节)。
3. 我们需要一个判断胜负的演算法，告诉我们是谁获胜了，并且标示他获胜棋子的连线。这边的胜负演算法需要适应不同棋盘大小，以及不同胜利条件。
4. 要有一个信息显示版，显示目前轮到谁。当游戏结束，即时显示谁获胜，或和局。
5. 要有一个重新开始的按钮，可以随时重新开始游戏。
6. 可以切换成Single Play模式，也就是说身边没有朋友(哭...)的时候，可以自己跟电脑玩。不过因为我们这次不是以人工只能下棋算法为主题，所以暂时用random算法来让电脑下棋。但是这边会单独设计算法可以方便抽换，之后若有心要让电脑变聪明，直接改演算法就可以了，不需要动到整个程式的架构。

# 开发环境及模板介绍

我常用的开发环境是`create-react-app`和`react-boilerplate`。这边简单引用官方文件介绍一下，会分享一下我选用的理由，另外详细部分我附上连结。

### create-react-app

> Create React App is a comfortable environment for learning React, and is the best way to start building a new single-page application in React.

##### [Github facebook/create-react-app](https://github.com/facebook/create-react-app)

##### [Create React App](https://reactjs.org/docs/create-a-new-react-app.html#create-react-app)

### react-boilerplate

The Hitchhikers Guide to react-boilerplate:

> This is a production-ready boilerplate, and as such optimized for browsers, not for beginners. It includes tools to help you manage performance, asynchrony, styling, everything you need to build a real application.

##### [Github react-boilerplate](https://github.com/react-boilerplate/react-boilerplate)

##### [The Hitchhikers Guide to react-boilerplate](https://github.com/react-boilerplate/react-boilerplate/blob/master/docs/general/introduction.md)

# 选择开发环境

我想选用`create-react-app`来当作快速开发的环境。虽然说我也蛮喜欢react-boilerplate的，因为react-boilerplate不用再自己一个一个安装redux, reselect, immutableJS, Styled Components...等等常用的package，而且也定期有在更新，github上面关注的人也蛮多的。

但是我觉得create-react-app比起react-boilerplate更适合入门者，因为create-react-app很好心的帮忙把一些复杂的工具(例如webpack, babel)的配置封装起来，所以大大的降低了入门者的门槛。而且依照我上网找教学文章的经验，觉得比较容易找到create-react-app的相关资料，例如如何快速将ReactJS Web App部署到github page上，有很多的参考文章是以create-react-app为例，所以为了让过程顺利的进行，少踩一些雷，这次我先选择create-react-app，让焦点聚焦在代码开发上。

# 快速建立一个项目

快速建立一个名为`tic-tac-toe-extended`的项目

```bash
npx create-react-app tic-tac-toe-extended
```

ex:

![create-react-app](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220419111749.png)

进入资料夹之后，执行`npm start`，即可查看你的app

```bash
cd tic-tac-toe-extended
npm start
```

ex:

![create-react-app_start](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220419115506.png)

文件结构如下：

![create-react-app__file-structure](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220419120542.png)

### 部署到GitHub-Pages

通常在这一步的时候，我就会把建立好的项目部署到`GitHub-Pages`，GitHub-Pages方便我们在GitHub建立静态网站，在展示作品的时候非常方便，而且官网说

> You get one site per GitHub account and organization, and unlimited project sites.

我之前有一天发现有GitHub-Pages的时候我觉得很不可思议，`unlimited project sites`!!! 对前端开发者来说是一大福音啊！

部署的详细步骤请容我省略，但是我附上我常参考的链接跟大家分享：

> **Deploy React to GitHub-Pages**
>
> ##### [GitHub-Pages README.md](https://create-react-app.dev/docs/deployment/#github-pages)
>
> ##### [Deploying a React App to GitHub Pages](https://github.com/gitname/react-gh-pages)
>
> ##### [Deploy React to GitHub-Pages to create an amazing website!](https://codeburst.io/deploy-react-to-github-pages-to-create-an-amazing-website-42d8b09cd4d)

### Absolute Imports

然后还有一件事情是我会在刚建立好create-react-app项目的时候做，就是我会让我的项目可以在import的时候指定`绝对路径`，因为有时候指定`相对路径`常会造成我在开发上的困扰，例如当专案变复杂的时候，我们很容易写出下面这样的东西，很容易写错

```JavaScript
import { setInit } from ‘../../../../../TicTacToe/actions’;
import {  selectTicTacToe } from ‘../../../../../TicTacToe/selectors;
```

为了解决这个问题，我还去装了一些VScode的extension，例如`Path Autocomplete`，来帮助我不要写错。

但是像我们在refactor的时候，或是想要把这段import复制到别的文件里面用的时候，相对路径很容易会改变，所以常被重复使用的import我会干脆写成绝对路径，方便我重复使用。

这边我分享我在设定的时候常会参考的链接：

> **Absolute Imports with Create React App**
>
> ##### [Absolute Imports with Create React App](https://dev.to/mr_frontend/absolute-imports-in-create-react-app-3ge8)
>
> ##### [Setting Up Absolute Paths in create-react-app](https://fdp.io/blog/2018/01/18/setting-up-absolute-paths-in-create-react-app/)

#### 补充说明

如果项目不是透过create-react-app建立的，或是我们的create-react-app已经执行npm run eject了，此时若要让专案可以使用绝对路径，就需要去修改webpack的path.resolve。

> ##### [如何扩展Create React App 的Webpack 配置](https://juejin.im/post/5a5d5b815188257327399962)
>
> ##### [ES6 Import Statement Without Relative Paths Using Webpack](https://moduscreate.com/blog/es6-es2015-import-no-relative-path-webpack/)

总结一下，今天我们介绍了两个不错的开发环境及样板，然后开始了我们的专案，并且也做了一些前置作业，像是可以部署到Github Page方便我们展示作品，以及如何设定绝对路径，方便我们往后的开发。

接下来就要直接写Code啦！我会用到的packages，之后会再边写边介绍。
