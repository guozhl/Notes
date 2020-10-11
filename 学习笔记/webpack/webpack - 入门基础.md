## 基础知识

### 概念
webpack 是一个现代 JavaScript 应用程序的静态模块打包工具。当 webpack 处理应用程序时，它会在内部构建一个 __依赖图__，此依赖图会映射项目所需的每个模块，并生成一个或多个 __bundle__。

#### 入口(entry) 
入口起点指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

默认值是 ./src/index.js，但你也可以通过配置 __entry__ 属性，来指定一个（或多个）不同的入口起点。例如：

__webpack.config.js__
```javascript
module.exports = {
  entry: './src/index.js'
};
```

#### 输出(output)
output 属性告诉 webpack 在哪里输出它所创建的 bundle，以及如何命名这些文件。主要输出文件的默认值是 ./dist/main.js，其他生成文件默认放置在 ./dist 文件夹中。

你可以通过在配置中指定一个 output 字段，来配置这些处理过程：

__webpack.config.js__
```javascript
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js'
  },
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  }
};
```

#### loader
webpack 只能理解 JavaScript 和 JSON 文件。loader 让 webpack 能够去处理其他类型的文件，并将它们转换为 __有效模块__，以供应用程序使用，以及被添加到依赖图中。

#### 插件(plugin)
loader 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。

想要使用一个插件，你只需要 require() 它，然后把它添加到 plugins 数组中。
你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 new 操作符来创建它的一个实例。
