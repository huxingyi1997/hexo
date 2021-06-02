---
title:  从0开始编写webpack插件
date: 2021-06-02 21:07:33
categories: 
- web前端
tags:
- webpack
- 插件
- 实战
---
继续抱佛脚，从0开始编写一个自己的 `webpack` 插件。
<!-- more -->

## 1. 前言

插件(plugins)是 `webpack` 中的一等功臣。正是由于有了诸多插件的存在，才使得 `webpack` 无所不能。在 `webpack` 源码中也是使用了大量的内部插件，插件要是用的好，可以让你的工作效率事半功倍。用了别人写的插件很爽，那么我们何不考虑编写一个自己的插件呢？本篇文章就来教你如何编写一个自己的 `webpack` 插件。



## 2. webpack运行机制

在编写插件之前，我们有必要先了解一下 `webpack` 的整体运行机制。 `Webpack` 的运行机制本质上是一种事件流的机制，在 `webpack` 运行过程中，从初始化参数、开始编译文件、确定入口、编译模块、编译完成、生成资源、输出资源、全部完成等等一系列过程，在这整个过程中的每一个节点， `webpack` 都会向外广播一个事件，我们可以通过监听每一个事件，在合适的时机通过 `webpack` 提供的 `API` 做一些合适的事情。

**通俗易懂的解释：**

如果上面的文字你不理解的话，那么我们以一个通俗易懂的例子再加以解释，如下：

我们把 `webpack` 比作一个厨师，而把 `webpack` 的整个运行过程比作厨师要炒一道菜，菜名就叫酸辣土豆丝。那么从准备要做这道菜到这道菜做好盛盘大概一共要经历以下几道工序：洗菜——切菜——炒菜——盛盘。并且我们跟厨师约定好，当厨师每进行一道工序的时候就让他大喊出这道工序的名称，即让厨师广播出一个事件，例如当厨师开始洗菜的时候，就让厨师大喊：“洗菜”。如果没有其他要求，那么这道酸辣土豆丝在经过如上四道工序后就可以完成啦。但是，我们有一个额外要求，就是想让这道酸辣土豆丝更辣一些。而原本的厨师只会按照正常的工序做，并不会额外的加辣椒，那要怎么办呢？此时我们的插件就登场啦，我们就需要编写一个“加辣椒”的插件。编写之前我们先要考虑这个辣椒在什么时候加合适，也就是说我们该监听哪一道工序，很明显，在炒菜的时候加。那么我们就监听“炒菜”这道工序，一旦厨师广播了这个事件，即大喊“炒菜”的时候，我们就把辣椒加进去，这样最后做出来的酸辣土豆丝就会更辣一些。

**总结来说：**

`Webpack` 就像一条生产线，要经过一系列处理流程后才能将源文件转换成输出结果。 这条生产线上的每个处理流程的职责都是单一的，多个流程之间有存在依赖关系，只有完成当前处理后才能交给下一个流程去处理。 插件就像是一个插入到生产线中的一个功能，在特定的时机对生产线上的资源做处理。

`Webpack` 通过 [Tapable](https://github.com/webpack/tapable)来组织这条复杂的生产线。 `Webpack` 在运行过程中会广播事件，插件只需要监听它所关心的事件，就能加入到这条生产线中，去改变生产线的运作。 `Webpack` 的事件流机制保证了插件的有序性，使得整个系统扩展性很好。

OK，相信现在你已经该对 `webpack` 的运行机制有个初步的理解了。接下来，我们就开始逐步编写插件。



## 3. 编写插件

我们知道，在我们通常使用别人的插件时往往是像如下方式使用的：

```javascript
module.exports = {
  entry: '',
  output: {},
  module: {},
  plugins: [
    new XXXPlugin(options)
  ]
}
```

从上面代码中我们可以看到，使用插件时往往都是 `new XXXPlugin()` ，那么换句话说一个插件就是一个类，使用该插件就是 `new` 一个该插件的实例，并且把插件所需要的配置参数传给该类的构造函数。那么我们就可以写出编写插件的第一步，如下：

```javascript
class XXXPlugin {
  // 在构造函数中获取用户给该插件传入的配置
  constructor(options) {
    this.options = options;
  }
}

// 导出 Plugin
module.exports = XXXPlugin;
```

接着，通过阅读 `webpack` 源码[webpack.js:46行](https://github.com/webpack/webpack/blob/master/lib/webpack.js##L46)我们知道：

```javascript
if (options.plugins && Array.isArray(options.plugins)) {
  for (const plugin of options.plugins) {
    if (typeof plugin === "function") {
      plugin.call(compiler, compiler);
    } else {
      plugin.apply(compiler);
    }
  }
}
```

当 `webpack` 内部在调用插件时会调用插件实例的 `apply` 方法，并为其传入 `compiler` 对象，OK，那么编写插件的第二步就来了，给插件类添加 `apply` 方法：

```javascript
class XXXPlugin {
  // 在构造函数中获取用户给该插件传入的配置
  constructor(options) {
    this.options = options;
  }
  // Webpack 会调用 XXXPlugin 实例的 apply 方法给插件实例传入 compiler 对象
  apply(compiler) {
      
  }
}

// 导出 Plugin
module.exports = XXXPlugin;
```

那么，这个 `complier` 又是什么？在这里我们有必要详细说明一下：

在开发 `Plugin` 时最常用的两个对象就是 `Compiler` 和 `Compilation` ，它们都继承自 `Tapable` ，是 `Plugin` 和 `Webpack` 之间的桥梁。 `Compiler` 和 `Compilation` 的含义如下：

- `Compiler` 对象代表了完整的 `webpack` 环境配置。这个对象在启动 `webpack` 时被一次性建立，并配置好所有可操作的设置，包括 `options`，`loader` 和 `plugin`。当在 `webpack` 环境中应用一个插件时，插件将收到此 `Compiler` 对象的引用。可以使用它来访问 `webpack` 的主环境。
- `Compilation`对象代表了一次资源版本构建。当运行`webpack` 开发环境中间件时，每当检测到一个文件变化，就会创建一个新的 `Compilation`，从而生成一组新的编译资源。一个 `Compilation`对象表现了当前的模块资源、编译生成资源、变化的文件、以及被跟踪依赖的状态信息。`Compilation`对象也提供了很多关键时机的回调，以供插件做自定义处理时选择使用。

`Compiler` 和 `Compilation` 的区别在于： `Compiler` 代表了整个 `Webpack` 从启动到关闭的生命周期，而 `Compilation` 只是代表了一次新的编译。

使用 `Compiler` 对象就可以访问 `webpack` 的主环境，就可以监听到 `webpack` 在运行过程中所广播出的一系列事件。OK，到这里我们才搞明白，**一个插件的核心部分原来是在 `apply` 方法里，在该方法里通过监听 `webpack` 在合适时机的广播出的事件然后做一些合适的事情。**此时，你可能还有个疑问：**我们该监听哪个事件？或者说webpack在整个流程中都广播了哪些事件？**接下来，我们就通过阅读源码来 `webpack` 看看在整个运行过程中都广播出了哪些事件。



## 4. webpack工作流程

`Webpack` 的工作流程是一个串行的过程，从启动到结束会依次执行以下流程：

- **初始化参数：**从配置文件和 `Shell` 语句中读取与合并参数，得出最终的参数；
- **开始编译：**用上一步得到的参数初始化 `Compiler` 对象，加载所有配置的插件，执行对象的`run`方法开始执行编译；
- **确定入口：**根据配置中的 `entry` 找出所有的入口文件
- **编译模块：**从入口文件出发，调用所有配置的 Loader 对模块进行编译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理；
- **完成模块编译：**在经过第4步使用 `Loader` 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系；
- **输出资源：**根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 `Chunk`，再把每个 `Chunk` 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会；
- **输出完成：**在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。

在以上过程中， `Webpack` 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 `Webpack` 提供的 `API` 改变 `Webpack` 的运行结果。

按照广播的事件又可大致分为三大阶段：初始化阶段、编译阶段、输出阶段。三个阶段所发生的部分常用事件如下。

### 4.1 初始化阶段

| 事件名          | 解释                                                         |
| :-------------- | :----------------------------------------------------------- |
| 初始化参数      | 从配置文件和Shell 语句中读取与合并参数，得出最终的参数。     |
| 实例化 Compiler | 1. 用上一步得到的参数初始化 Compiler 实例 2. Compiler 负责文件监听和启动编译 3. Compiler 实例中包含了完整的 Webpack 配置，全局只有一个 Compiler 实例。 |
| 加载插件        | 1. 依次调用插件的 apply 方法，让插件可以监听后续的所有事件节点。 同时给插件传入 compiler 实例的引用，以方便插件通过 compiler 调用 Webpack 提供的 API。 |
| environment     | 开始应用Node.js 风格的文件系统到compiler 对象，以方便后续的文件寻找和读取 |
| entry-option    | 读取配置的 Entrys，为每个 Entry 实例化一个对应的 EntryPlugin，为后面该 Entry 的递归解析工作做准备 |
| after-plugins   | 调用完所有内置的和配置的插件的apply 方法                     |
| after-resolvers | 根据配置初始化resolver, resolver 负责在文件系统中寻找指定路径的文件 |

### 4.2 编译阶段

| 事件名        | 解释                                                         |
| :------------ | :----------------------------------------------------------- |
| run           | 启动一次新的编译                                             |
| watch-run     | 和run 类似，区别在于它是在监听模式下启动编译，在这个事件中可以获取是哪些文件发生了变化从而导致重新启动一次新的编译 |
| compile       | 该事件是为了告诉插件一次新的编译将要启动，同时会给插件传入compiler 对象。 |
| compilation   | 当 Webpack 以开发模式运行时，每当检测到文件变化，一次新的 Compilation 将被创建。 一个 Compilation 对象包含了当前的模块资源、编译生成资源、变化的文件等。 Compilation 对象也提供了很多事件回调供插件做扩展。 |
| make          | 一个新的 Compilation 创建完毕主开始编译                      |
| after-compile | 一次Compilation 执行完成                                     |
| invalid       | 当遇到文件不存在、文件编译错误等异常时会触发该事件，该事件不会导致Webpack 退出 |

### 4.3 输出阶段

| 事件名            | 解释                                                         |
| :---------------- | :----------------------------------------------------------- |
| seal              | 所有模块及其依赖的模块都通过Loader 转换完成，根据依赖关系开始生成Chunk |
| addChunk          | 生成资源                                                     |
| createChunkAssets | 创建资源                                                     |
| getRenderManifest | 获得要渲染的描述文件                                         |
| render            | 渲染源码                                                     |
| afterCompile      | 编译结束                                                     |
| shouldEmit        | 所有需要输出的文件已经生成好，询问插件哪些文件需要输出，哪些不需要。 |
| emit              | 确定好要输出哪些文件后，执行文件输出，可以在这里获取和修改输出内容。 |
| emitRecords       | 写入记录                                                     |
| done              | 成功完成一次完整的编译和输出流程                             |
| failed            | 如果在编译和输出的流程中遇到异常，导致Webpack 退出， 就会直接跳转到本步骤，插件可以在本事件中获取具体的错误原因 |

更多的事件钩子请求查看这里：[Compiler对象事件](https://github.com/webpack/webpack/blob/master/lib/Compiler.js##L45)、[Compilation对象事件](https://github.com/webpack/webpack/blob/master/lib/Compilation.js##L250)。



## 5. 继续编写插件

知道了广播了哪些事件之后，我们就可以在 `apply` 中监听事件并编写相应的逻辑啦。

### 5.1 监听事件和相应逻辑

如下：

```javascript
class XXXPlugin {
  // 在构造函数中获取用户给该插件传入的配置
  constructor(options) {
    this.options = options;
  }
  // Webpack 会调用 XXXPlugin 实例的 apply 方法给插件实例传入 compiler 对象
  apply(compiler) {
    // 监听某个事件
    compiler.hooks.
    'compiler事件名称'.tap('XXXPlugin', (compilation) => {
      // 编写相应逻辑
        
    });
  }
}

// 导出 Plugin
module.exports = XXXPlugin;
```

当我们监听 `Compiler` 对象事件中的 `compilation` 事件时，此时可以在回调函数中再次监听 `compilation` 对象里的事件，如下：

```javascript
class XXXPlugin {
  // 在构造函数中获取用户给该插件传入的配置
  constructor(options) {
    this.options = options;
  }
  // Webpack 会调用 XXXPlugin 实例的 apply 方法给插件实例传入 compiler 对象
  apply(compiler) {
    // 监听某个事件
    compiler.hooks.compilation.tap("XXXPlugin", (compilation) => {
      compilation.hooks.
      'compiler事件名称'.tap('optimize', () => {
        // 编写相应逻辑
          
      });
    });
  }
}

// 导出 Plugin
module.exports = XXXPlugin;
```

### 5.2 异步插件

有一些事件使用了异步钩子 `AsyncHook` ，此时在监听这些事件的时候我们就需要使用 `tapAsync` 或 `tapPromise` ，并且还需要额外传入一个 `callback` 回调函数，在插件运行结束时，必须调用这个回调函数，如下：

```javascript
class XXXPlugin {
  // 在构造函数中获取用户给该插件传入的配置
  constructor(options) {
    this.options = options;
  }
  // Webpack 会调用 XXXPlugin 实例的 apply 方法给插件实例传入 compiler 对象
  apply(compiler) {
    // 监听某个事件
    compiler.hooks.emit.tapAsync("XXXPlugin", (compilation, callback) => {
      setTimeout(function () {
        console.log("异步任务完成");
        callback();
      }, 500);
    });
  }
}

// 导出 Plugin
module.exports = XXXPlugin;
```

OK，以上就是编写一个 `webpack` 插件所有需要用到的东西，接下里我们就来编写一个简单的插件做个示例。



## 6. 插件示例

在这里我们编写一个简单的插件，该插件的功能是：将 `webpack` 输出的所有文件打包压缩成一个 `zip` 压缩包，该插件接收一个配置参数 `filename` ，表示最终生成的压缩包文件名称。其代码如下：

定义 `ZipPlugin` 插件：

```javascript
const {
  RawSource
} = require("webpack-sources");
const JSZip = require("jszip");

class ZipPlugin {
  constructor(options) {
    this.options = options;
  }
  apply(compiler) {
    compiler.hooks.emit.tapAsync("ZipPlugin", (compilation, callback) => {
      var zip = new JSZip();
      for (let filename in compilation.assets) {
        const source = compilation.assets[filename].source();
        zip.file(filename, source);
      }
      zip.generateAsync({
        type: "nodebuffer"
      }).then(content => {
        compilation.assets[this.options.filename || 'fileList.zip'] = new RawSource(content);
        callback();
      });
    });
  }
}

module.exports = ZipPlugin;
```

代码说明：

1. 首先获取到用户传入的配置对象，将其存入`this.options`内。
2. 我们要想在`webpack`输出完成后将输出的所有文件打包成`zip`压缩包，那么就必须监听`webpack`输出完成后的那个事件，即`emit`事件。并且该事件为异步事件，所以使用`tabAsync`监听。
3. 通过`webpack`的输出资源对象`compilation.assets`获取到输出的所有文件，遍历这些文件，使用`JSZip`包将这些文件都放入一个`zip`压缩包内。
4. 然后将这个压缩包挂载到`webpack`的输出资源对象`compilation.assets`上，使其跟随输出资源一同被输出，该压缩包的文件名使用用户传入的`filename`，如果用户未传入则使用默认的名字`fileList.zip`。
5. 最后由于是异步事件，在运行结束时，必须调用`callback()`

使用 `ZipPlugin` 插件：

```javascript
const ZipPlugin = require('./src/plugins/ZipPlugin');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve('dist'),
    filename: 'bundle.js'
  },
  plugins: [
    // 使用ZipPlugin插件
    new ZipPlugin({
      filename: 'XXX.zip'
    })
  ]
}
```



## 7. 总结

那么我们可以简单总结一下，一个插件的构成部分：

- 一个插件就是一个类。
- 在插件类上定义一个 `apply` 方法。
- 在`apply`方法中监听一个`webpack` 自身的事件钩子。
- 处理 `webpack` 内部实例的特定数据。
- 功能完成后调用 `webpack` 提供的回调。

相信读完这篇文章，你应该就能写出一个自己的插件了。