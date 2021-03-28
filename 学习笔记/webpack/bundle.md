## 痛点
首屏加载缓慢，目前的体积包过于庞大

## 解决思路
1. 通过webpack-bundle-analyzer进行体积分析，将目前的bundle进行拆包
2. main包过大，需路由懒加载
3. 公共包过大，需module懒加载

## 方式
经过查看，目前市面上懒加载使用的工具有如下：
* require.ensure
* import()
* lazyload-loader
* react.lazy
* bundle-loader
* react-loadable

**传送门**
[webpack异步加载的原理](https://github.com/wugaoliang1116/webpa_ensure)
[Webpack实现路由懒加载的三种方式](https://www.cnblogs.com/aimeeblogs/p/9497414.html)
[基于webpack4+react 的js懒加载](https://cloud.tencent.com/developer/article/1406301)
[webpack v3 结合 react-router v4 做 dynamic import — 按需加载（懒加载）](https://segmentfault.com/a/1190000011128817)
[react-loadable路由懒加载](https://www.cnblogs.com/anxiaoyu/p/10879981.html)

## 方案：react-loadable+loader
#### 使用方式
1. [react-loadable example](https://www.npmjs.com/package/react-loadable#example)
2. [customizing-rendering](https://www.npmjs.com/package/react-loadable#customizing-rendering)
3. 编写自定义webpack loader，通过解析每个模块导出页面的index.js文件，转换为lazy导出方式

#### 补充知识点
1. [webpack的执行流程](https://segmentfault.com/a/1190000015088834?utm_source=tag-newest#articleHeader3)
2. [如何编写一个loader](https://segmentfault.com/a/1190000012718374)
3. [手把手教你撸一个 Webpack Loader](https://blog.csdn.net/lszy16/article/details/79162960)

#### 涉及修改内容
1. 需规范我们的页面导出方式
2. 配置webpack loader

## 其他方案
### lazy
#### 使用方式
修改页面类导出的方式
```
之前:
export {ProductDetail as ProductDetail} from './detail/product-detail';

之后:
const LazyProductDetail = lazy(() => import('./detail/product-detail').then(mod => mod.ProductDetail));
class ProductDetail extends Component {
  render() {
    return (
      <div>
        <Suspense fallback={<div>Loading...</div>}>
          <LazyProductDetail {...this.props}/>
        </Suspense>
      </div>
    );
  }
}
export {
  ProductDetail
};
```
> PS: 此外，react-loadable和react lazy使用方式基本一致

#### 涉及修改内容
1. web页面导出需修改导出方式
2. 该种导出方式和react native不兼容


### bundle-loader
#### 使用方式
1. webpack修改配置，loader增加bundle-loader
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.bundle\.js$/,
        use: 'bundle-loader'
      }
    ]
  }
}
```

本质上是将匹配的文件包裹require.ensure, 代码如下：
```
if(query.lazy) {
  result = [
    "module.exports = function(cb) {\n",
    "	require.ensure([], function(require) {\n",
    "		cb(require(", loaderUtils.stringifyRequest(this, "!!" + remainingRequest), "));\n",
    "	}" + chunkNameParam + ");\n",
    "}"];
}
```

此时，已不支持如下导出方式
```
export {ProductDetail as ProductDetail} from './detail/product-detail';
```

2. 提供支持懒加载包裹的组件，如下:
```
class Bundle extends React.Component {
  constructor(props){
    super(props);
    this.state = {
      // 默认为空
      mod: null
    }

    console.log("xxxxxx", props.load);
  }
 
 
  componentWillMount() {
    // 加载初始状态
    this.load(this.props);
  }
 
  componentWillReceiveProps(nextProps) {
    if (nextProps.load !== this.props.load) {
      this.load(nextProps);
    }
  }
 
  load(props) {
    // 重置状态
    this.setState({
      mod: null
    });

    // 传入组件的组件
    props.load((mod) => {
      this.setState({
        mod: mod.default ? mod.default : mod
      });
    });
  }
 
  render() {
    // 不为空，则渲染传入的子组件函数
    return this.state.mod ? this.props.children(this.state.mod) : null;
  }
}
```

3. 修改路由声明
```
<Route key={config.path} path={config.path} render={(props) => {
  return <Bundle load={config.component}>
    {
      Comp => {
        if (Comp) {
          let C = Inject(Comp, config.options);
          return <C {...props} {...parentState.query}  title={config.options.title}/>
        } else {
          return <div>Loading...</div>
        }
      }
    }
  </Bundle>
}}exact/>
```

> PS: 此外，require.ensure、import()和bundle-loader使用基本一致

#### 涉及修改内容
1. 页面导出在路由文件中按文件路径进行引用
2. webpack需配置懒加载的文件项
3. cp库支持懒加载路由


### 后续的思考
1. webpack可以生成打包分析的json文件，依据此可以后续进行web包体积监控
基本思路：
检查package.json是否改动来检查包体积是否有较大变动
2. 随着npm ci下载依赖的加速，可以考虑将同步的web打包，ios打包，android打包分离

### 后续优化的点
1. node_modules包还可以继续缩减体积，主要在于lodash，URI等体积比较大的包
2. main包可以继续进行图片拆离研究，进一步缩小体积



问题记录：
1. 使用loader测试拆分node_modules
2. loader的test重新写一下
3. 将所有node_modules一级目录进行拆分
