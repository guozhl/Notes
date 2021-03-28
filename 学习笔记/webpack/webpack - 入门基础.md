# 基础知识
## 概念
webpack 是一个现代 JavaScript 应用程序的静态模块打包工具。当 webpack 处理应用程序时，它会在内部构建一个 __依赖图__，此依赖图会映射项目所需的每个模块，并生成一个或多个 __bundle__。

## 入口(entry) 
入口起点指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

默认值是 ./src/index.js，但你也可以通过配置 __entry__ 属性，来指定一个（或多个）不同的入口起点。例如：

```javascript
module.exports = {
  entry: './src/index.js'
};
```

## 输出(output)
output 属性告诉 webpack 在哪里输出它所创建的 bundle，以及如何命名这些文件。主要输出文件的默认值是 ./dist/main.js，其他生成文件默认放置在 ./dist 文件夹中。

你可以通过在配置中指定一个 output 字段，来配置这些处理过程：

```javascript
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js'
  }
};
```

在上面的示例中，我们通过 output.filename 和 output.path 属性，来告诉 webpack bundle 的名称，以及我们想要 bundle 生成(emit)到哪里。

## loader
webpack 只能理解 JavaScript 和 JSON 文件。loader 让 webpack 能够去处理其他类型的文件，并将它们转换为 __有效模块__，以供应用程序使用，以及被添加到依赖图中。

在更高层面，在 webpack 的配置中，loader 有两个属性：
- test 属性，识别出哪些文件会被转换。
- use 属性，定义出在进行转换时，应该使用哪个 loader。

```javascript
const path = require('path');

module.exports = {
  output: {
    filename: 'my-first-webpack.bundle.js',
  },
  module: {
    rules: [{ test: /\.txt$/, use: 'raw-loader' }],
  },
};
```
以上配置中，对一个单独的 module 对象定义了 rules 属性，里面包含两个必须属性：test 和 use。

## 插件(plugin)
loader 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。

想要使用一个插件，你只需要 require() 它，然后把它添加到 plugins 数组中。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 new 操作符来创建它的一个实例。
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
const webpack = require('webpack'); // 用于访问内置插件

module.exports = {
  module: {
    rules: [{ test: /\.txt$/, use: 'raw-loader' }],
  },
  plugins: [new HtmlWebpackPlugin({ template: './src/index.html' })],
};
```
在上面的示例中，html-webpack-plugin 为应用程序生成一个 HTML 文件，并自动注入所有生成的 bundle。

### 常见插件
#### HtmlWebpackPlugin
该插件将为你生成一个 HTML5 文件， 在 body 中使用 script 标签引入你所有 webpack 生成的 bundle。

详细见[HtmlWebpackPlugin](https://github.com/jantimon/html-webpack-plugin)
#### SplitChunksPlugin
webpack 将根据以下条件自动拆分 chunks：
- 新的 chunk 可以被共享，或者模块来自于 node_modules 文件夹
- 新的 chunk 体积大于 20kb（在进行 min+gz 之前的体积）
- 当按需加载 chunks 时，并行请求的最大数量小于或等于 30
- 当加载初始化页面时，并发请求的最大数量小于或等于 30

当尝试满足最后两个条件时，最好使用较大的 chunks。

详细见[SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin)

#### webpack-bundle-analyzer
模块体积分析。

详细见[webpack-bundle-analyzer](https://www.npmjs.com/package/webpack-bundle-analyzer)

## 模式(mode) 
通过选择 development, production 或 none 之中的一个，来设置 mode 参数，你可以启用 webpack 内置在相应环境下的优化。其默认值为 production。
```javascript
module.exports = {
  mode: 'production',
};
```
