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
React的[官方教程](https://react.docschina.org/tutorial/tutorial.html)中就使用了Tic Tac Toe（井字棋）这款游戏作为实战教程引导大家使用React，我自己复现并针对官网教程总结部分的[更多要求](https://react.docschina.org/tutorial/tutorial.html#wrapping-up)部分进行了复现，最终得到了完整的[Tic Tac Toe游戏](https://hxy1997.xyz/tic-tac-toe/)。玩过之后怅然若失，也很想要找一些自己感兴趣的项目来磨刀。想到小时候常玩的五子棋游戏，忍不住手痒，很想要自己复现一个，仔细一想了Tic Tac Toe游戏本质上就是五子棋的简化版，为了做出一些小改变来增加趣味性，因此决定来做一个伸缩自如的Tic Tac Toe  Extended游戏。

<!-- more -->

# 基础版Tic Tac Toe游戏简介

Tic Tac Toe（井字棋）游戏规则：
两个玩家，一个画圈圈(O)，一个画叉叉(X)，轮流在3乘3的格上打自己的符号，最先以横、直、斜连成一线则为胜。如果双方都下得正确无误，将得和局。

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

我想选用`create-react-app`来当作快速开发的环境。虽然说我也蛮喜欢react-boilerplate的，因为react-boilerplate不用再自己一个一个安装redux, reselect, immutableJS, Styled Components...等等常用的package，而且也定期有在更新，Github上面关注的人也蛮多的。

但是我觉得create-react-app比起react-boilerplate更适合入门者，因为create-react-app很好心的帮忙把一些复杂的工具(例如webpack, babel)的配置封装起来，所以大大的降低了入门者的门槛。而且依照我上网找教学文章的经验，觉得比较容易找到create-react-app的相关资料，例如如何快速将ReactJS Web App部署到Github Page上，有很多的参考文章是以create-react-app为例，所以为了让过程顺利的进行，少踩一些雷，这次我先选择create-react-app，让焦点聚焦在代码开发上。

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

然后还有一件事情是我会在刚建立好create-react-app项目的时候做，就是我会让我的项目可以在import的时候指定`绝对路径`，因为有时候指定`相对路径`会造成我开发上的问题，例如当项目变复杂的时候，我们很容易写出下面这样的东西，很容易写错

```js
import { setInit } from ‘../../../../../TicTacToe/actions’;
import { selectTicTacToe } from ‘../../../../../TicTacToe/selectors;
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

如果项目不是透过create-react-app建立的，或是我们的create-react-app已经执行npm run eject了，此时若要让项目可以使用绝对路径，就需要去修改webpack的path.resolve。

> ##### [如何扩展Create React App 的Webpack 配置](https://juejin.im/post/5a5d5b815188257327399962)
>
> ##### [ES6 Import Statement Without Relative Paths Using Webpack](https://moduscreate.com/blog/es6-es2015-import-no-relative-path-webpack/)

总结一下，今天我们介绍了两个不错的开发环境及样板，然后开始了我们的专案，并且也做了一些前置作业，像是可以部署到Github Page方便我们展示作品，以及如何设定绝对路径，方便我们往后的开发。

接下来就要直接写Code啦！我会用到的packages，之后会再边写边介绍。

# 文件结构规划

开始一个项目之前，还有一个重要的问题要面对，就是文件结构(File Structure)的规划。特别当项目文件及页面越来越多，功能越来越复杂，好的文件结构规划可以帮助我们在复杂及众多的文件中轻松且快速地找到我们想要的目标代码，也可以帮助我们减少犯错的机会。

但身为一个Reac的入门开发者，在这部分通常会相对没有经验，可以想到的部分没有办法很缜密，所以我上网做了一点功课，并且挑了几篇我自己觉得不错的来跟大家分享。

在React的官方文件里有针对这个问题来做回答：

> Is there a recommended way to structure React projects?
> React doesn't have opinions on how you put files into folders. That said there are a few common approaches popular in the ecosystem you may want to consider.

##### [React的官方文件: File Structure](https://reactjs.org/docs/faq-structure.html)

官方文件里面有提到几个重要的原则，但也建议我们`Don’t overthink it`:

1. Grouping by features or routes
2. Grouping by file type
3. Avoid too much nesting
4. Don't overthink it

根据这些原则，考量到我们的Tic Tac Toe Extended里面不会用到route（路由），然后因为是小游戏，component也不会有复杂结构，所以其实在这个项目文件我们一切从简。

由于我们会用到`Redux`，在redux 和react 共同构成的架构下，我们将组件分成`container`和`component`，container资料夹下面我会放Tic Tac Toe Extended的主要组件，这些组件跟这个项目依赖性较高，而component里面我会放跟这个项目低依赖性的组件，这些组件只被动的接收资料的传入，甚至可以单独抽出来放到别的项目里使用，例如我们需要用的圈圈，叉叉组件，或是我们自己做的按钮等等。

下面是我的文件规划方案，给大家参考:

![file-structure](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220420110853.png)

这次因为我们containers下面只会有TicTacToeExtended，所以不会有多个containers下的组件去共用Components里面的元件的状况，这些Components只会被TicTacToeExtended用到，所以我这次就把components放在TicTacToe下面。

> ##### [How to better organize your React applications?](https://medium.com/@alexmngn/how-to-better-organize-your-react-applications-2fd3ea1920f1)
>
> ##### [我们如何组织 React + Redux 项目结构](https://starandtina.github.io/articles/Tech/React/2017-06-04-how-we-structure-react-redux-project-stucture.html)

# Tic Tac Toe参数存储规划

#### 选用React + Redux架构

这个项目我会使用到Redux，Redux是为了解决复杂的React App中，有很多层和很复杂的巢状结构，如果要跨Component 之间一层一层的传递参数，会让代码变得很混乱而不容易维护，所以干脆有一个中央控制参数的store来储存所有的state，让需要用到state参数的component直接跟store拿资料即可。所以就可以避免一些component他明明自己不需要这个state，却因为它下面的component需要用到这个state，而硬生生把state传进去又传出来的状况。而且如果传递中的state，在传递过程中，不小心被哪个component无心改动到，就会让除错又变得更棘手。

![redux-flow](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220420134025.png)


[react-redux-framework](https://www.slideshare.net/binhqdgmail/006-react-redux-framework)

根据上面这些理由，我们的Tic Tac Toe好像也不需要用到Redux，因为我们不会有很多不同的component需要彼此传递资料的复杂状况，反而在简单的架构下使用Redux会让专案变得没有必要的复杂。但是因为是应用练习，为了在将来复杂的专案下我们也能很熟练Redux，所以这边我们还是来练习使用一下React + Redux的架构。

因为这次的主题是设定在入门之后的应用练习，所以Redux的详细介绍和安装方式会直接略过，直接进入到文件结构的规划，我的安装设定方式是参考react-boilerplate。我的设定方式或许不是最好的，但是代码也会提供给大家做参考。

> [tic-tac-toe/src/store/](https://github.com/TimingJL/tic-tac-toe/tree/master/src/store)

#### 资料储存规划

我们要来设计state要储存的内容以及储存方式，以下是我设计的参数说明：

1. gameScale:

   - 因为我们要做的是一个棋盘大小可以伸缩自如的圈圈叉叉游戏，所以我们需要有一个参数来存目前棋盘大小，当调整棋盘大小的时候，只要改变这个参数就可以了，这里我命名为gameScale。例如，当棋盘为3x3的时候，gameScale为3，当棋盘为5x5的时候，gameScale为5。

2. winnerCondition:

   - 除了伸缩自如的棋盘之外，我们的胜利条件也可以让玩家自行调整，可以3子连成一直线为获胜，或是调整成5子连成一直线为获胜，所以在winnerCondition储存这个数字，当条件改变的时候，就调整这个参数。

3. blocks:

   - 这边我用一个物件的阵列来储存棋盘上每一个格子当下的状态，每一个物件有一个id及owner，id用来帮助我在运算的时候找到我想要的那个指定的格子，owner用来储存目前这个格子标示的是圈圈，还是叉叉，还是空的。

    ```js
    blocks = [{id, owner}, {id, owner}, {id, owner}, {id, owner}, ...]
    ```

4. currentRole:

   - 顾名思义就是当前是轮到谁要下棋，是圈圈还是叉叉。下面是我在constants.js里面存的参数资料，我用1代表圈圈，用-1代表叉叉，因为我希望乘以-1就可以达到换人的效果。然后下面PLAYER_1意思是游戏开始的时候先攻的人，我这边让圈圈可以先攻。

   ```js
   export const CIRCLE = 1;
   export const CROSS = -1;
   export const TOGGLE = -1;
   export const PLAYER_1 = CIRCLE;
   
   export const GAME_WRAPPER_SIZE = 600;
   export const DEFAULT_GAME_SCALE = 3;
   export const DEFAULT_WINNER_CONDITION = 3;
   ```

   tic-tac-toe/src/containers/TicTacToe/constants.js

5. isSinglePlayer

   - 我们希望可以切换让自己跟电脑对战的模式，或是跟朋友一起玩的模式，所以isSinglePlayer为true的时候，会让电脑成为你的对手。
   
     ![data-structure.](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220420215319.png)

# 工具介绍：styled-components

我在React里面写CSS常用的工具是LESS，本项目想尝试一下styled-components。一般来说，CSS的限制在于没有变量、循环和函数等逻辑，所以我们很难在撰写纯CSS的时候做出复杂的变化。为了解决这个问题，开始出现了在JS上编写CSS的做法， styled-components 是其中一种解决方案。

使用styled-components的好处有很多，例如它可以帮助我们将样式写成更具语义化的组件形式，提高代码可读性。另外有一个我很喜欢的功能，就是可以将React的参数用props的方式传入来控制样式，所以页面的样式就能够透过改变这些参数来做更随心所欲的变化，这两个功能在项目都会使用到。

而且CSS的作用域是全局的，所以命名相同很容易产生冲突，特别是在项目更复杂的时候。但是使用styled-components会为我们生成的React组件产生随机的className，重复使用这些组件的时候随机的className也会不同，因此能够避免组件之间className的冲突，顺利的解决CSS全局作用域的问题。

[styled-components](https://www.styled-components.com/)
[CSS Evolution: From CSS, SASS, BEM, CSS Modules to Styled Components](https://medium.com/@perezpriego7/css-evolution-from-css-sass-bem-css-modules-to-styled-components-d4c1da3a659b)
[Styled Components：让样式也成为组件](https://www.itread01.com/articles/1494952701.html)

# 页面布局规划

在准备好资料之后，我们要开始规划一下我们的页面，下面这个图是我设计的页面雏形。

![layout-draft](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220422224221.png)

为了让规划不再是规划，而是能变成实体，我先开始做出整个项目的骨干。
我延用create-react-app他原本的App组件作为背景，并包裹`<TicTacToe />`组件

![TicTacToe-component](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220423214815.png)

![tictactoe-main-draft](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220423215633.png)

得到的初步布局如下：

![tictactoe-border-layout](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220423214111.png)

### Grid & Flex

为了要做出设计图的layout，我会喜欢用grid和flex这两个CSS的属性。

- flex可以帮助我们灵活做出自适应的单轴排版
- grid的强项则是在网格状的排版提供了很方便的操作。
  当页面比较复杂的时候，我会用grid先把网格状的布局定位好，再来用flex调整局部的单一轴向排版。下面分享一些我在学习grid和flex的时候，常会参考的文章及教学影片：

[深入解析CSS Flexbox](https://www.oxxostudio.tw/articles/201501/css-flexbox.html)
[图解：CSS Flex 属性一点也不难](https://wcc723.github.io/css/2017/07/21/css-flex/)
[CSS Grid 属性介绍](https://wcc723.github.io/css/2017/03/22/css-grid-layout/)
[CSS Grid For Everyone](https://www.youtube.com/watch?v=bLbkYuGkQMU&feature=youtu.be)
[Grid Garden - A game for learning CSS grid](http://cssgridgarden.com/)

上面CSS Grid For Everyone 这位讲者，Part1和Part2讲的内容我觉得还蛮清楚的，帮我厘清很多问题，让我觉得使用grid和flex这两个属性不用再那么害怕，然后讲解过程中也会提到一些实战经验，我觉得很受用，所以推荐给大家。

另外Grid Garden是透过闯关游戏的方式来熟悉grid的语法，还蛮有趣的，也推荐给大家。

不过在使用这两个属性的时候还是需要考虑一下浏览器支持，目前grid还不到90%，所以在使用的时候还是要特别留意。

![grid-browser-support](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220423220000.png)

![flex-browser-support](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20220511220900.png)
