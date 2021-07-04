---
title:  Rollup——适合框架和类库使用的模块打包器
date: 2021-06-06 17:51:30
categories: 
- web前端
tags:
- Rollup
- 模块打包
---
webpack是最常见的前端打包工具，功能丰富，在ts-axios库的开发中，我采用了Rollup作为打包工具，相比之下更为轻便，特别是看到Vue也是用了Rollup作为打包工具，感觉非常有意思，想要系统学习Rollup这个工具，在上手操作以后又有了新的认识。
<!-- more -->

## 1.Rollup概述

- [rollup.js 中文网](https://www.rollupjs.com/) 、[rollup.js官网](http://rollupjs.org/guide/en/)

`Rollup`仅仅是一款`JavaScript` 模块打包器，也称为`ESM`打包器，并没有像`webpack`那样有很多其他额外的功能，它可以将项目中散落的细小模块打包成整块的代码，可以让他们更好的运行在**浏览器环境** or **Node.js环境** 。

官方文档已经写的很清楚了：

> Rollup 是一个 JavaScript 模块打包器，可以将小块代码编译成大块复杂的代码，例如 library 或应用程序。

与`Webpack`偏向于应用打包的定位不同，`rollup.js`更专注于`Javascript`类库打包。

我们熟知的`Vue`、`React`等诸多知名框架或类库都是通过`rollup.js`进行打包的。

rollup 对代码模块使用新的标准化格式，这些标准都包含在 JavaScript 的 ES6 版本中，而不是像CommonJS 和 AMD这种特殊解决方案。

rollup最大的亮点就是Tree-shaking，即可以静态分析代码中的 import，并排除任何未使用的代码。这允许我们架构于现有工具和模块之上，而不会增加额外的依赖或使项目的大小膨胀。如果用webpack做，虽然可以实现tree-shaking，但是需要自己配置并且打包出来的代码非常臃肿，所以对于库文件和UI组件，rollup更加适合。



## 2.Rollup vs Webpack

`webpack`做前端的同学大家都用过，那么为什么有些场景还要使用`rollup`呢？这里简单对`webpack`和`rollup`做一个比较：

总体来说`webpack`和`rollup`在不同场景下，都能发挥自身优势作用。`webpack`对于代码分割和静态资源导入有着“先天优势”，并且支持热模块替换(`HMR`)，而`rollup`并不支持。`Rollup`与`Webpack`作用类似，但是`Rollup`更为小巧，`webpack`可以在前端开发中完成前端工程化的绝大多数功能，而`Rollup`仅仅是一款`ESM`打包器，并没有其他额外的功能。

`Rollup`中并不支持类似`HMR`这种高级特性。但是`Rollup`诞生的目的并不是要与`webpack`全面竞争，**其初衷只是提供一个高效的`ES Modules`的打包器，充分利用`ESM`的各项特性构建出结构比较扁平，性能比较出众的类库。**

所以当开发应用时可以优先选择`webpack`，但是`rollup`对于代码的`Tree-shaking`和`ES6`模块有着算法优势上的支持，若你项目只需要打包出一个简单的`bundle`包，并是基于`ES6`模块开发的，可以考虑使用`rollup`。

其实`webpack`从`2.0`开始就已经支持`Tree-shaking`，并在使用`babel-loader`的情况下还可以支持`es6 module`的打包，从`4.0`开始开启生产环境就会自动启动这个优化功能。实际上，`rollup`已经在渐渐地失去了当初的优势了。但是它并没有被抛弃，反而因其简单的`API`、使用方式被许多库开发者青睐，如`Vue`、`React`等，都是使用`rollup`作为构建工具的。



## 3.快速上手

### 3.1 安装

首先全局安装`rollup`：

```bash
npm i rollup -g
```

### 3.2 目录准备（hello world）

在本地创建一个项目：

```bash
mkdir -p hello_world
cd hello_world
```

接着，我们初始化一个如下所示的项目目录

```
├── dist # 编译结果
├── example # HTML引用例子
│   └── index.html
├── package.json
└── src # 源码
    └── index.js
```

首先我们在`src/index.js`中写入如下代码：

```js
console.log("柯森");
```

然后在命令行执行以下命令：

```bash
rollup src/index.js -f umd -o dist/bundle.js
```

执行命令，我们即可在`dist`目录下生成`bundle.js`，就是我们想要的打包后的文件：

```js
(function (factory) {
	typeof define === 'function' && define.amd ? define(factory) :
	factory();
}((function () { 'use strict';

	console.log("柯森");

})));
```

这时，我们再在`example/index.html`中引入上面打包生成的`bundle.js`文件，打开浏览器：

 ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210606201145.png) 

如我们所预料的，控制台输出了`柯森`。

到这里，我们就用`rollup`打包了一个最最简单的`demo`。

我们也可以用package.json来设置打包配置信息，用npm run xxx来打包和测试代码。

可能很多同学看到这里对于上面命令行中的参数不是很明白，我依次说明下：

- `-f`。`-f`参数是`--format`的缩写，它表示生成代码的格式，`amd`表示采用`AMD`标准，`cjs`为`CommonJS`标准，`esm`（或 es）为`ES`模块标准。`-f`的值可以为`amd`、`cjs`、`system`、`esm`（'es’也可以）、`iife`或`umd`中的任何一个。
- `-o`。`-o`指定了输出的路径，这里我们将打包后的文件输出到`dist`目录下的`bundle.js`

其实除了这两个，还有很多其他常用的命令（这里我暂且列举剩下两个也比较常用的，完整的[rollup 命令行参数](https://rollupjs.org/guide/en/#command-line-flags)）：

- `-c`。指定`rollup`的配置文件。
- `-w`。监听源文件是否有改动，如果有改动，重新打包。

### 3.3 使用配置文件(`rollup.config.js`)

使用命令行的方式，如果选项少没什么问题，但是如果添加更多的选项，这种命令行的方式就显得麻烦了。

为此，我们可以创建配置文件来囊括所需的选项

在项目中创建一个名为`rollup.config.js`的文件，增加如下代码：

```js
export default {
  input: ["./src/index.js"],
  output: {
    file: "./dist/bundle.js",
    format: "umd",
    name: "experience",
  },
};
```

然后命令行执行：

```bash
rollup -c
```

打开`dist/bundle.js`文件，我们会发现和上面采用命令行的方式打包出来的结果是一样的。

这里，我对配置文件的选项做下简单的说明：

- `input`表示入口文件的路径（老版本为 entry，已经废弃）
- `output`表示输出文件的内容，它允许传入一个对象或一个数组，当为数组时，依次输出多个文件，它包含以下内容：
  - `output.file`：输出文件的路径（老版本为 dest，已经废弃）
  - `output.format`：输出文件的格式
  - `output.banner`：文件头部添加的内容
  - `output.footer`：文件末尾添加的内容

到这里，相信你已经差不多上手`rollup`了。



## 4.插件

如果要加载其他类型的资源文件，或者是导入`CommonJS`模块，或者编译`ES6`新特性，`Rollup`同样支持使用插件的方式扩展。

**插件是`Rollup`唯一扩展途径**，这个与`webpack`有所不同，`webpack`有`plugins`、`module`、`optimization`三种途径。

下面，我将介绍`rollup`中的几种常用的插件以及`external`属性、`tree-shaking`机制。

### 4.1 `resolve`插件

#### 为什么要使用`resolve`插件

在上面的入门案例中，我们打包的对象是本地的`js`代码和库，但实际开发中，不太可能所有的库都位于本地，我们大多会通过`npm`下载远程的库。`rollup`默认只能按照路径的方式加载本地模块，对于第三方模块并不能想`webpack`一样通过名称导入，所以需要通过插件处理。

与`webpack`和`browserify`这样的其他捆绑包不同，`rollup`不知道如何打破常规去处理这些依赖。因此我们需要添加一些配置查找外部模块，然后导入加载npm模块。

#### `resolve`插件使用

首先在我们的项目中添加一个依赖`the-answer`，然后修改`src/index.js`文件:

```js
import answer from "the-answer";

export default function () {
  console.log("the answer is " + answer);
}
```

执行`npm run build`。

> 这里为了方便，我将原本的`rollup -c -w`添加到了`package.json`的`scripts`中：`"build": "rollup -c -w"`

会得到以下报错：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210606202500.png) 打包后的`bundle.js`仍然会在`Node.js`中工作，但是`the-answer`不包含在包中。为了解决这个问题，将我们编写的源码与依赖的第三方库进行合并，`rollup.js`为我们提供了`resolve`插件。

首先，安装`resolve`插件：

```bash
npm i -D @rollup/plugin-node-resolve
```

修改配置文件`rollup.config.js`：

```js
// 默认导出是一个插件函数
import resolve from "@rollup/plugin-node-resolve";

export default {
  input: ["./src/index.js"],
  output: {
    file: "./dist/bundle.js",
    format: "umd",
    name: "experience",
  },
  plugins: [
    // 这样 Rollup 能找到 `ms`
    resolve(),
  ],
};
```

这时再次执行`npm run build`，可以发现报错已经没有了： 

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210606202605.png)

打开`dist/bundle.js`文件：

```js
(function (global, factory) {
  typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory() :
  typeof define === 'function' && define.amd ? define(factory) :
  (global = typeof globalThis !== 'undefined' ? globalThis : global || self, global.experience = factory());
}(this, (function () { 'use strict';

  var index = 42;

  function index$1 () {
    console.log("the answer is " + index);
  }

  return index$1;

})));
```

打包文件`bundle.js`中已经包含了引用的模块。

有些场景下，虽然我们使用了`resolve`插件，但可能我们仍然想要某些库保持外部引用状态，这时我们就需要使用`external`属性，来告诉`rollup.js`哪些是外部的类库。

### 4.2 external 属性

修改`rollup.js`的配置文件：

```js
// 默认导出是一个插件函数
import resolve from "@rollup/plugin-node-resolve";

export default {
  input: ["./src/index.js"],
  output: {
    file: "./dist/bundle.js",
    format: "umd",
    name: "experience",
  },
  plugins: [
    // 这样 Rollup 能找到 `ms`
    resolve(),
  ],
  external: ["the-answer"],
};
```

重新打包，打开`dist/bundle.js`文件：

```js
(function (global, factory) {
  typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory(require('the-answer')) :
  typeof define === 'function' && define.amd ? define(['the-answer'], factory) :
  (global = typeof globalThis !== 'undefined' ? globalThis : global || self, global.experience = factory(global.answer));
}(this, (function (answer) { 'use strict';

  function _interopDefaultLegacy (e) { return e && typeof e === 'object' && 'default' in e ? e : { 'default': e }; }

  var answer__default = /*#__PURE__*/_interopDefaultLegacy(answer);

  function index () {
    console.log("the answer is " + answer__default['default']);
  }

  return index;

})));
```

这时我们看到`the-answer`已经是做为外部库被引入了。

在自己的库中需要使用第三方库，例如lodash等，又不想在最终生成的打包文件中出现jquery。这个时候我们就需要使用external属性。比如我们使用了lodash，

```js
import _ from "lodash";

// rollup.config.js
export default {
  input: ["src/main.js"],
  external: ["lodash"],
  globals: {
    lodash: "_",
  },
  output: [
    { file: pkg.main, format: "cjs" },
    { file: pkg.module, format: "es" },
  ],
};
```

### 4.3 `commonjs`插件

#### 为什么需要`commonjs`插件

`rollup.js`编译源码中的模块引用默认只支持 `ES6+`的模块方式`import/export`。然而大量的`npm`模块是基于`CommonJS`模块方式，这就导致了大量 `npm`模块不能直接编译使用。

因此使得`rollup.js`编译支持`npm`模块和`CommonJS`模块方式的插件就应运而生：`@rollup/plugin-commonjs`。

#### `commonjs`插件使用

首先，安装该模块：

```bash
npm i -D @rollup/plugin-commonjs
```

然后修改`rollup.config.js`文件：

```js
// 默认导出是一个插件函数
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";

export default {
  input: ["./src/index.js"],
  output: {
    file: "./dist/bundle.js",
    format: "umd",
    name: "experience",
  },
  plugins: [
    // 这样 Rollup 能找到 `ms`
    resolve(),
    // 这样 Rollup 能转换 `ms` 为一个ES模块
    commonjs(),
  ],
  external: ["the-answer"],
};
```

### 4.4 `babel`插件

#### 为什么需要`babel`插件？

我们在`src`目录下添加`es6.js`文件(⚠️ 这里我们使用了 es6 中的箭头函数)：

```js
const a = 1;
const b = 2;
console.log(a, b);
export default () => {
  return a + b;
};
```

然后修改`rollup.config.js`配置文件：

```js
// 默认导出是一个插件函数
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";

export default {
  input: ["./src/es6.js"],
  output: {
    file: "./dist/bundle.js",
    format: "umd",
    name: "experience",
  },
  plugins: [
    // 这样 Rollup 能找到 `ms`
    resolve(),
    // 这样 Rollup 能转换 `ms` 为一个ES模块
    commonjs(),
  ],
  external: ["the-answer"],
};
```

执行打包，可以看到`dist/esBundle.js`文件内容如下：

```js
(function (global, factory) {
  typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory() :
  typeof define === 'function' && define.amd ? define(factory) :
  (global = typeof globalThis !== 'undefined' ? globalThis : global || self, global.experience = factory());
}(this, (function () { 'use strict';

  const a = 1;
  const b = 2;
  console.log(a, b);
  var es6 = () => {
    return a + b;
  };

  return es6;

})));
```

可以看到箭头函数被保留下来，这样的代码在不支持`ES6`的环境下将无法运行。我们期望在`rollup.js`打包的过程中就能使用`babel`完成代码转换，因此我们需要`babel`插件。

#### `babel`插件的使用

首先，安装：

```bash
npm i -D @rollup/plugin-babel
```

同样修改配置文件`rollup.config.js`：

```js
// 默认导出是一个插件函数
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";
import babel from "@rollup/plugin-babel";

export default {
  input: ["./src/es6.js"],
  output: {
    file: "./dist/bundle.js",
    format: "umd",
    name: "experience",
  },
  plugins: [
    // 这样 Rollup 能找到 `ms`
    resolve(),
    // 这样 Rollup 能转换 `ms` 为一个ES模块
    commonjs(),
    babel(),
  ],
  external: ["the-answer"],
};
```

然后打包，发现会出现报错： 

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210606204835.png) 提示我们缺少`@babel/core`，因为`@babel/core`是`babel`的核心。我们来进行安装：

```bash
npm i @babel/core
```

再次执行打包，发现这次没有报错了，但是我们尝试打开`dist/esBundle.js`：

```js
(function (global, factory) {
  typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory() :
  typeof define === 'function' && define.amd ? define(factory) :
  (global = typeof globalThis !== 'undefined' ? globalThis : global || self, global.experience = factory());
}(this, (function () { 'use strict';

  const a = 1;
  const b = 2;
  console.log(a, b);
  var es6 = (() => {
    return a + b;
  });

  return es6;

})));
```

可以发现箭头函数仍然存在，显然这是不正确的，说明我们的`babel`插件没有起到作用。这是为什么呢？

原因是由于我们缺少`.babelrc`文件，添加该文件：

```js
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "modules": false,
        "useBuiltIns": "usage",
        "corejs": "2.6.10",
      }
    ]
  ],
  "plugins": [
      // 解决多个地方使用相同代码导致打包重复的问题
      ["@babel/plugin-transform-runtime"]
  ],
  "ignore": [
      "node_modules/**"
    ]
}
```

我们看`.babelrc`配置了的插件，需要进行安装：

```bash
npm i core-js @babel/core @babel/preset-env @babel/plugin-transform-runtime
```

`@babel/preset-env`可以根据配置的目标浏览器或者运行环境来自动将ES2015+的代码转换为es5。需要注意的是，我们设置`"modules": false`，否则 Babel 会在 Rollup 有机会做处理之前，将我们的模块转成 CommonJS，导致 Rollup 的一些处理失败。

为了解决多个地方使用相同代码导致打包重复的问题，我们需要在.babelrc的plugins里配置`@babel/plugin-transform-runtime`，同时我们需要修改rollup的配置文件：

```js
// 默认导出是一个插件函数
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";
import babel from "@rollup/plugin-babel";

export default {
  input: ["./src/es6.js"],
  output: {
    file: "./dist/bundle.js",
    format: "umd",
    name: "experience",
  },
  plugins: [
    // 这样 Rollup 能找到 `ms`
    resolve(),
    // 这样 Rollup 能转换 `ms` 为一个ES模块
    commonjs(),
    babel({
      exclude: "node_modules/**", // 防止打包node_modules下的文件
      runtimeHelpers: true, // 使plugin-transform-runtime生效
    }),
  ],
  external: ["the-answer"],
};

```

这次再次执行打包，我们打开`dist/esBundle.js`文件：

```js
(function (global, factory) {
  typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory() :
  typeof define === 'function' && define.amd ? define(factory) :
  (global = typeof globalThis !== 'undefined' ? globalThis : global || self, global.experience = factory());
}(this, (function () { 'use strict';

  var a = 1;
  var b = 2;
  console.log(a, b);
  var es6 = (function () {
    return a + b;
  });

  return es6;

})));
```

可以看到箭头函数被转换为了`function`，说明`babel`插件正常工作。

### 4.5 `json`插件

#### 为什么要使用`json`插件？

在`src`目录下创建`json.js`文件：

```js
import json from "../package.json";
console.log(json.author);
```

内容很简单，就是引入`package.json`，然后去打印`author`字段。

修改`rollup.config.js`配置文件：

```js
// 默认导出是一个插件函数
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";
import babel from "@rollup/plugin-babel";

export default {
  input: ["./src/json.js"],
  output: {
    file: "./dist/jsonBundle.js",
    format: "umd",
    name: "experience",
  },
  plugins: [
    // 这样 Rollup 能找到 `ms`
    resolve(),
    // 这样 Rollup 能转换 `ms` 为一个ES模块
    commonjs(),
    babel({
      exclude: "node_modules/**", // 防止打包node_modules下的文件
      runtimeHelpers: true, // 使plugin-transform-runtime生效
    }),
  ],
  external: ["the-answer"],
};
```

执行打包，发现会发生如下报错：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210606205812.png) 提示我们缺少`@rollup/plugin-json`插件来支持`json`文件。

#### `json`插件的使用

来安装该插件：

```bash
npm i -D @rollup/plugin-json
```

同样修改下配置文件，将插件加入`plugins`数组即可。

然后再次打包，发现打包成功了，我们打开生成的`dist/jsonBundle`目录：

```js
(function (factory) {
  typeof define === 'function' && define.amd ? define(factory) :
  factory();
}((function () { 'use strict';

  var name = "rollup-experience";
  var version = "1.0.0";
  var description = "";
  var main = "index.js";
  var directories = {
  	example: "example"
  };
  var scripts = {
  	build: "rollup -c -w",
  	test: "echo \"Error: no test specified\" && exit 1"
  };
  var author = "Cosen";
  var license = "ISC";
  var dependencies = {
  	"@babel/core": "^7.11.6",
  	"@babel/preset-env": "^7.11.5",
  	"the-answer": "^1.0.0"
  };
  var devDependencies = {
  	"@rollup/plugin-babel": "^5.2.0",
  	"@rollup/plugin-commonjs": "^15.0.0",
  	"@rollup/plugin-json": "^4.1.0",
  	"@rollup/plugin-node-resolve": "^9.0.0"
  };
  var json = {
  	name: name,
  	version: version,
  	description: description,
  	main: main,
  	directories: directories,
  	scripts: scripts,
  	author: author,
  	license: license,
  	dependencies: dependencies,
  	devDependencies: devDependencies
  };

  console.log(json.author);

})));
```

完美！！

### 4.6 `tree-shaking`机制

这里我们以最开始的`src/index.js`为例进行说明：

```js
import answer from "the-answer";

export default function () {
  console.log("the answer is " + answer);
}
```

修改上述文件：

```js
const a = 1;
const b = 2;
export default function () {
  console.log(a + b);
}
```

执行打包。打开`dist/bundle.js`文件：

```js
(function (global, factory) {
  typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory() :
  typeof define === 'function' && define.amd ? define(factory) :
  (global = typeof globalThis !== 'undefined' ? globalThis : global || self, global.experience = factory());
}(this, (function () { 'use strict';

  var a = 1;
  var b = 2;
  function index () {
    console.log(a + b);
  }

  return index;

})));
```

再次修改`src/index.js`文件：

```js
const a = 1;
const b = 2;
export default function () {
  console.log(a);
}
```

再次执行打包，打开打包文件：

```js
(function (global, factory) {
  typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory() :
  typeof define === 'function' && define.amd ? define(factory) :
  (global = typeof globalThis !== 'undefined' ? globalThis : global || self, global.experience = factory());
}(this, (function () { 'use strict';

  var a = 1;
  function index () {
    console.log(a);
  }

  return index;

})));
```

发现了什么？

我们发现关于变量`b`的定义没有了，因为源码中并没有用到这个变量。这就是`ES`模块著名的`tree-shaking`机制，它动态地清除没有被使用过的代码，使得代码更加精简，从而可以使得我们的类库获得更快的加载速度。

### 4.7 `eslint`插件

安装

```bash
npm i -D @rollup/plugin-eslint
```

我们可以使用上面的提到的rollup-plugin-eslint来配置：

```js
import { eslint } from "rollup-plugin-eslint";

export default {
  plugins: [
    eslint({
      throwOnError: true,
      throwOnWarning: true,
      include: ["src/**"],
      exclude: ["node_modules/**"],
    }),
  ],
};
```

然后建立.eslintrc.js来根据自己风格配置具体检测：

```js
module.exports = {
  env: {
    browser: true,
    es6: true,
    node: true,
  },
  extends: "eslint:recommended",
  globals: {
    Atomics: "readonly",
    SharedArrayBuffer: "readonly",
    ENV: true,
  },
  parserOptions: {
    ecmaVersion: 2018,
    sourceType: "module",
  },
  rules: {
    "linebreak-style": ["error", "unix"],
    quotes: ["error", "single"],
  },
};
```

详细的eslint配置可以去官网学习。





## 5.进阶版配置

### 5.1 代码拆分

**动态导入**，`rollup`内部会自动处理代码分包，
在`src/index.js`中引入

```js
// import函数返回一个promise对象
// then方法参数是module，由于模块导出的成员都会放在module对象中，所以可以通过解构的方式提取log
import('./logger').then(({ log }) => {
  log('code splitting~')
})  
```

修改`roll.config.js`中`output`里面的配置

```js
export default {
  // input 是打包入口文件路径
  input: 'src/index.js',
  // 输出配置
  output: {
    // 输出目录名称
    dir: 'dist',
    // 输出格式
    format: 'amd'
  }
}
```

不修改配置文件直接运行

```bash
rollup --config
```

会报错

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210606211810.png)

`UMD` 和 `iife` 是不支持代码拆分方式格式，因为自执行函数会把所有的模块都放到一个函数中，并没有像`webpack`一样有一些引导代码，所以没有办法做到代码拆分

如果要使用代码拆分，就需要使用`AMD` or `CommonJS`等方式。在浏览器中只能使用`AMD`的方式，所以这里改用输出格式为`AMD`

况且我们拆分代码输出不同的文件，`file`属性只是针对一个文件，所以我们需要改用`dir`去指定文件夹名称，不然还是会报错

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210606211816.png)

运行代码

```bash
rollup --config
```

可以看到`dist`文件夹里面有两个拆分打包的文件。

### 5.2 多入口打包

rollup支持多入口打包，对于不同文件的公共部分也会自动提取到单个文件中作为独立的`bundle.js`

模板中将多入口打包的代码开启，可以看到`album`和`index`都引用了`fetch.js`和`logger.js`的代码，我们对`rollup.config.js`进行修改

```js
export default {
  // 这里input要改成数组形式或者对象形式，对象形式可以修改打包的文件名，键对应的就是打包的文件名
  // input: ['src/index.js', 'src/album.js'],
  input: {
    indexjs: 'src/index.js',
    albumjs: 'src/album.js'
  },
  // 输出配置要改成拆分包的配置，以为多入口打包默认会执行打包拆分的特性。所以输出格式要改成amd
  output: {
    dir: 'dist',
    format: 'amd'
  }
}
```

命令行执行

```bash
rollup --config
```

可以看到`dist`里面生成了三个文件，其中两个文件打包和一个公共模块的打包，里面包含了`logger`和`fetch`模块

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210606212201.png)

**amd输出格式要注意什么？**

对于`amd`输出格式的打包文件是不能直接引用到页面上，必须通过实现`AMD`标准的库去加载。

尝试使用一下

在`dist`下面生成一个`HTML`文件，尝试引入`requirejs`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
 <!--requirejs的cdn地址，data-main是入口文件的名称-->
  <script src="https://unpkg.com/requirejs@2.3.6/bin/r.js" data-main="albumjs.js"></script>
</body>
</html>
```

浏览器打开可以看到内容正常引入，控制台也正常工作。

### 5.3 导出模式

我们可以将自己的代码导出成commonjs模块，es模块，以及浏览器能识别的模块，通过如下方式设置：

```js
export default {
  input: "src/main.js",
  external: ["ms"],
  output: [
    { file: pkg.main, format: "cjs" },
    { file: pkg.module, format: "es" },
    { file: pkg.module, format: "umd" },
  ],
};
```

### 5.4 发布到npm

如果你是之前没有注册npm账号，你可以通过如下方式配置：

```bash
npm adduser
```

然后输入用户名，邮箱，密码，最后使用npm publish发布。这里介绍包的配置文件，即package.json:

```json
{
  "name": "@alex_xu/time",
  "version": "1.0.0",
  "description": "common use js time lib",
  "main": "dist/tool.cjs.js",
  "module": "dist/time.esm.js",
  "browser": "dist/time.umd.js",
  "author": "alex_xu",
  "homepage": "https://github.com/MrXujiang/timeout_rollup",
  "keywords": [
    "tools",
    "javascript",
    "library",
    "time"
  ],
  "dependencies": {
    // ...
  },
  "devDependencies": {
    // ...
  },
  "scripts": {
    "build": "NODE_ENV=production rollup -c",
    "dev": "rollup -c -w",
    "test": "node test/test.js",
    "pretest": "npm run build"
  },
  "files": [
    "dist"
  ]
}
```

name是包的名字，可以直接写包名，比如loadash，或者添加域，类似于@koa/router这种，@后面是你npm注册的用户名。key为包的关键字。

发布后，我们可以用类似于下面这种方式安装：

```bash
npm install @alex_xu/time
```

使用

```js
import { sleep } from '@alex_xu/time'
// 或
const { sleep } = requrie('@alex_xu/time')
```

如下是安装截图：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210606214056.webp)

在npm上也可以搜索到自己的包：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210606214104.webp)

是不是很有成就感呢？快让大家一起使用你开发的包吧！

### 5.5 指令参数参考

- `--format` —— 指定输出文件打包格式，例如：`iife`是自调用函数
- `--file` —— 输出文件，后面跟打印路径，不写会打印到控制台
- `--config` —— 指定使用配置文件，后面可以加指定配置文件的名称，`rollup --config <filename>`，默认是`rollup.config.js`。



## 6.rollup 初版源码解读

rollup 初版源码，才 1000 行左右。麻雀虽小五脏俱全初版源码已经实现了怎么打包的，怎么做 tree-shaking 这两个功能（半成品），所以看初版源码已经足够让我们理解其工作形式。

rollup 使用了 `acorn` 和 `magic-string` 两个库。为了更好的阅读 rollup 源码，必须对它们有所了解。

下面我将简单的介绍一下这两个库的作用。

### 6.1 acorn

`acorn` 是一个 JavaScript 语法解析器，它将 JavaScript 字符串解析成语法抽象树 AST。

例如以下代码：

```js
export default function add(a, b) {
  return a + b;
}
```

将被解析为：

```js
{
  type: "Program",
  start: 0,
  end: 50,
  body: [
    {
      type: "ExportDefaultDeclaration",
      start: 0,
      end: 50,
      declaration: {
        type: "FunctionDeclaration",
        start: 15,
        end: 50,
        id: {
          type: "Identifier",
          start: 24,
          end: 27,
          name: "add",
        },
        expression: false,
        generator: false,
        params: [
          {
            type: "Identifier",
            start: 28,
            end: 29,
            name: "a",
          },
          {
            type: "Identifier",
            start: 31,
            end: 32,
            name: "b",
          },
        ],
        body: {
          type: "BlockStatement",
          start: 34,
          end: 50,
          body: [
            {
              type: "ReturnStatement",
              start: 36,
              end: 48,
              argument: {
                type: "BinaryExpression",
                start: 43,
                end: 48,
                left: {
                  type: "Identifier",
                  start: 43,
                  end: 44,
                  name: "a",
                },
                operator: "+",
                right: {
                  type: "Identifier",
                  start: 47,
                  end: 48,
                  name: "b",
                },
              },
            },
          ],
        },
      },
    },
  ],
  sourceType: "module",
};
```

可以看到这个 AST 的类型为 `program`，表明这是一个程序。`body` 则包含了这个程序下面所有语句对应的 AST 子节点。

每个节点都有一个 `type` 类型，例如 `Identifier`，说明这个节点是一个标识符；`BlockStatement` 则表明节点是块语句；`ReturnStatement` 则是 return 语句。

如果想了解更多详情 AST 节点的信息可以看一下这篇文章[《使用 Acorn 来解析 JavaScript》](https://juejin.im/post/6844903450287800327)。

### 6.2 magic-string

`magic-string` 也是 rollup 作者写的一个关于字符串操作的库。下面是 github 上的示例：

```js
var MagicString = require("magic-string");
var s = new MagicString("problems = 99");

s.overwrite(0, 8, "answer");
s.toString(); // 'answer = 99'

s.overwrite(11, 13, "42"); // character indices always refer to the original string
s.toString(); // 'answer = 42'

s.prepend("var ").append(";"); // most methods are chainable
s.toString(); // 'var answer = 42;'

var map = s.generateMap({
  source: "source.js",
  file: "converted.js.map",
  includeContent: true,
}); // generates a v3 sourcemap

require("fs").writeFile("converted.js", s.toString());
require("fs").writeFile("converted.js.map", map.toString());
```

从示例中可以看出来，这个库主要是对字符串一些常用方法进行了封装。这里就不多做介绍了。

### 6.3 rollup 源码结构

```
│  bundle.js // Bundle 打包器，在打包过程中会生成一个 bundle 实例，用于收集其他模块的代码，最后再将收集的代码打包到一起。
│  external-module.js // ExternalModule 外部模块，例如引入了 'path' 模块，就会生成一个 ExternalModule 实例。
│  module.js // Module 模块，开发者自己写的代码文件，都是 module 实例。例如有 'foo.js' 文件，它就对应了一个 module 实例。
│  rollup.js // rollup 函数，一切的开始，调用它进行打包。
│
├─ast // ast 目录，包含了和 AST 相关的类和函数
│      analyse.js // 主要用于分析 AST 节点的作用域和依赖项。
│      Scope.js // 在分析 AST 节点时为每一个节点生成对应的 Scope 实例，主要是记录每个 AST 节点对应的作用域。
│      walk.js // walk 就是递归调用 AST 节点进行分析。
│
├─finalisers
│      cjs.js // 打包模式，目前只支持将代码打包成 common.js 格式
│      index.js
│
└─utils // 一些帮助函数
        map-helpers.js
        object.js
        promise.js
        replaceIdentifiers.js
```

上面是初版源码的目录结构，在继续深入研究rollup 打包过程前，请仔细阅读上面的注释，了解一下每个文件的作用。



## 7.rollup 如何打包的？

在 rollup 中，一个文件就是一个模块。每一个模块都会根据文件的代码生成一个 AST 语法抽象树，rollup 需要对每一个 AST 节点进行分析。

分析 AST 节点，就是看看这个节点有没有调用函数或方法。如果有，就查看所调用的函数或方法是否在当前作用域，如果不在就往上找，直到找到模块顶级作用域为止。

如果本模块都没找到，说明这个函数、方法依赖于其他模块，需要从其他模块引入。

例如 `import foo from './foo.js'`，其中 `foo()` 就得从 `./foo.js` 文件找。

在引入 `foo()` 函数的过程中，如果发现 `foo()` 函数依赖其他模块，就会递归读取其他模块，如此循环直到没有依赖的模块为止。

最后将所有引入的代码打包在一起。

**上面例子的示例图：**

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210606215913.png)

**接下来我们从一个具体的示例开始，一步步分析 rollup 是如何打包的**。

以下两个文件是代码文件。

`main.js`

```js
import { foo1, foo2 } from "./foo";

foo1();

function test() {
  const a = 1;
}

console.log(test());
```

`foo.js`

```js
export function foo1() {}
export function foo2() {}
```

下面是测试代码：

```js
const rollup = require("../dist/rollup");

rollup(__dirname + "/main.js").then((res) => {
  res.wirte("bundle.js");
});
```

### 7.1 rollup 读取 `main.js` 入口文件。

`rollup()` 首先生成一个 `Bundle` 实例，也就是打包器。然后根据入口文件路径去读取文件，最后根据文件内容生成一个 `Module` 实例。

```js
fs.readFile(path, "utf-8", (err, code) => {
  if (err) reject(err);
  const module = new Module({
    code,
    path,
    bundle: this, // bundle 实例
  });
});
```

### 7.2 new Moudle() 过程

在 new 一个 `Module` 实例时，会调用 `acorn` 库的 `parse()` 方法将代码解析成 AST。

```js
this.ast = parse(code, {
  ecmaVersion: 6, // 要解析的 JavaScript 的 ECMA 版本，这里按 ES6 解析
  sourceType: "module", // sourceType值为 module 和 script。module 模式，可以使用 import/export 语法
});
```

接下来需要对生成的 AST 进行分析。

#### **第一步**，分析导入和导出的模块，将引入的模块和导出的模块填入对应的对象。

每个 `Module` 实例都有一个 `imports` 和 `exports` 对象，作用是将该模块引入和导出的对象填进去，代码生成时要用到。

上述例子对应的 `imports` 和 `exports` 为：

```js
// key 为要引入的具体对象，value 为对应的 AST 节点内容。
imports = {
  foo1: { source: "./foo", name: "foo1", localName: "foo1" },
  foo2: { source: "./foo", name: "foo2", localName: "foo2" },
};
// 由于没有导出的对象，所以为空
exports = {};
```

#### **第二步**，分析每个 AST 节点间的作用域，找出每个 AST 节点定义的变量。

每遍历到一个 AST 节点，都会为它生成一个 `Scope` 实例。

```js
// 作用域
class Scope {
  constructor(options = {}) {
    this.parent = options.parent; // 父作用域
    this.depth = this.parent ? this.parent.depth + 1 : 0; // 作用域层级
    this.names = options.params || []; // 作用域内的变量
    this.isBlockScope = !!options.block; // 是否块作用域
  }

  add(name, isBlockDeclaration) {
    if (!isBlockDeclaration && this.isBlockScope) {
      // it's a `var` or function declaration, and this
      // is a block scope, so we need to go up
      this.parent.add(name, isBlockDeclaration);
    } else {
      this.names.push(name);
    }
  }

  contains(name) {
    return !!this.findDefiningScope(name);
  }

  findDefiningScope(name) {
    if (this.names.includes(name)) {
      return this;
    }

    if (this.parent) {
      return this.parent.findDefiningScope(name);
    }

    return null;
  }
}
```

`Scope` 的作用很简单，它有一个 `names` 属性数组，用于保存这个 AST 节点内的变量。 例如下面这段代码：

```js
function test() {
  const a = 1;
}
```

打断点可以看出来，它生成的作用域对象，`names` 属性就会包含 `a`。并且因为它是模块下的一个函数，所以作用域层级为 1（模块顶级作用域为 0）。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210606220353.png)

#### **第三步**，分析标识符，并找出它们的依赖项。

什么是标识符？如变量名，函数名，属性名，都归为标识符。当解析到一个标识符时，rollup 会遍历它当前的作用域，看看有没这个标识符。如果没有找到，就往它的父级作用域找。如果一直找到模块顶级作用域都没找到，就说明这个函数、方法依赖于其它模块，需要从其他模块引入。如果一个函数、方法需要被引入，就将它添加到 `Module` 的 `_dependsOn` 对象里。

例如 `test()` 函数中的变量 `a`，能在当前作用域找到，它就不是一个依赖项。`foo1()` 在当前模块作用域找不到，它就是一个依赖项。

打断点也能发现 `Module` 的 `_dependsOn` 属性里就有 `foo1`。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210606220415.png)

**这就是 rollup 的 tree-shaking 原理。**

rollup 不看你引入了什么函数，而是看你调用了什么函数。如果调用的函数不在此模块中，就从其它模块引入。

换句话说，如果你手动在模块顶部引入函数，但又没调用。rollup 是不会引入的。从我们的示例中可以看出，一共引入了 `foo1()` `foo2()` 两个函数，`_dependsOn` 里却只有 `foo1()`，因为引入的 `foo2()` 没有调用。

`_dependsOn` 有什么用呢？后面生成代码时会根据 `_dependsOn` 里的值来引入文件。

### 7.3 根据依赖项，读取对应的文件。

从 `_dependsOn` 的值可以发现，我们需要引入 `foo1()` 函数。

这时第一步生成的 `imports` 就起作用了：

```js
imports = {
  foo1: { source: "./foo", name: "foo1", localName: "foo1" },
  foo2: { source: "./foo", name: "foo2", localName: "foo2" },
};
```

rollup 将 `foo1` 当成 key，找到它对应的文件。然后读取这个文件生成一个新的 `Module` 实例。由于 `foo.js` 文件导出了两个函数，所以这个新 `Module` 实例的 `exports` 属性是这样的：

```js
exports = {
  foo1: {
    node: Node {
      type: 'ExportNamedDeclaration',
      start: 0,
      end: 25,
      declaration: [Node],
      specifiers: [],
      source: null
    },
    localName: 'foo1',
    expression: Node {
      type: 'FunctionDeclaration',
      start: 7,
      end: 25,
      id: [Node],
      expression: false,
      generator: false,
      params: [],
      body: [Node]
    }
  },
  foo2: {
    node: Node {
      type: 'ExportNamedDeclaration',
      start: 27,
      end: 52,
      declaration: [Node],
      specifiers: [],
      source: null
    },
    localName: 'foo2',
    expression: Node {
      type: 'FunctionDeclaration',
      start: 34,
      end: 52,
      id: [Node],
      expression: false,
      generator: false,
      params: [],
      body: [Node]
    }
  }
}
```

这时，就会用 `main.js` 要导入的 `foo1` 当成 key 去匹配 `foo.js` 的 `exports` 对象。如果匹配成功，就把 `foo1()` 函数对应的 AST 节点提取出来，放到 `Bundle` 中。如果匹配失败，就会报错，提示 `foo.js` 没有导出这个函数。

### 7.4 生成代码

由于已经引入了所有的函数。这时需要调用 `Bundle` 的 `generate()` 方法生成代码。

同时，在打包过程中，还需要对引入的函数做一些额外的操作。

#### **移除额外代码**

例如从 `foo.js` 中引入的 `foo1()` 函数代码是这样的：`export function foo1() {}`。rollup 会移除掉 `export `，变成 `function foo1() {}`。因为它们就要打包在一起了，所以就不需要 `export` 了。

#### **重命名**

例如两个模块中都有一个同名函数 `foo()`，打包到一起时，会对其中一个函数重命名，变成 `_foo()`，以避免冲突。

好了，回到正文。

还记得文章一开始提到的 `magic-string` 库吗？在 `generate()` 中，会将每个 AST 节点对应的源代码添加到 `magic-string` 实例中：

```js
magicString.addSource({
  content: source,
  separator: newLines,
});
```

这个操作本质上相当于拼字符串：

```js
str += '这个操作相当于将每个 AST 的源代码当成字符串拼在一起，就像现在这样';
```

最后将拼在一起的代码返回。

```js
return { code: magicString.toString() };
```

到这就已经结束了，如果你想把代码生成文件，可以调用 `write()` 方法生成文件：

```js
rollup(__dirname + "/main.js").then((res) => {
  res.wirte("dist.js");
});
```

这个方法是写在 `rollup()` 函数里的。

```js
function rollup(entry, options = {}) {
  const bundle = new Bundle({ entry, ...options });
  return bundle.build().then(() => {
    return {
      generate: (options) => bundle.generate(options),
      wirte(dest, options = {}) {
        const { code } = bundle.generate({
          dest,
          format: options.format,
        });

        return fs.writeFile(dest, code, (err) => {
          if (err) throw err;
        });
      },
    };
  });
}
```



## 总结

本文大致向大家介绍了什么是`rollup`以及如何快速上手`rollup`，解读了`rollup`的初版代码。文中提到的这些其实只是冰山一角，`rollup`能玩的东西还有很多，关于更多可以去[rollup 官网](https://rollupjs.org/guide/en/)查询



