title: React项目（三）脚手架create-react-app依赖Webpack系列分析
author: BIGPIG
tags:
  - React
  - Webpack
categories:
  - React
date: 2017-07-19 12:01:00
---
## 说明
所有的分析都是加上个人理解，快速弄懂每个插件的作用，以及在项目中如何配置简单使用。更加深入具体的东西，需要每个插件慢慢去吃透，不说源码以及原理，至少理解到细节的功能，知道有这么回事，开发中遇到问题才有初步的解决思路。每个插件已经关联了链接，都有具体对应的文件可以看。
## 依赖插件简介
- [webpack][]：是JavaScript 应用程序的模块打包器(*module bundler*)。通过递归所有模块，打包成`bundle`，由浏览器加载。
- [webpack-dev-server][]：用于快速开发应用程序，通过配置，改变程序行为，如`host`、文件变动监控等。
- [webpack-manifest-plugin][]：是 [webpack][] 的优化，对编译生成的静态文件加`md5`还是`hash`重命名输出，并记录成清单，用作缓存。
- [case-sensitive-paths-webpack-plugin][]：强制文件路径大小写敏感，`mac OS`大小写不敏感会导致其他平台引用文件路径出错。
- [extract-text-webpack-plugin][]：将`CSS`从`JS`文件中分离开来，成为单独的`.CSS`文件。
- [html-webpack-plugin][]：自动生成`html`文件。
- [sw-precache-webpack-plugin][]：预缓存，配合[webpack-manifest-plugin][]使用可以缓存加载到文件、甚至网站用到的所有静态文件`CSS`、`JS`、`Image...`等。

## 依赖分析
### [webpack][]
webpack 用于编译 JavaScript 模块，项目`config`下包含`webpack`以及`paths.js`的都是 webpack 的配置文件。我们先了解其提供的**四个核心概念**：入口([entry](https://doc.webpack-china.org/concepts/entry-points/))、输出([output](https://doc.webpack-china.org/concepts/output))、加载器([loader](https://doc.webpack-china.org/concepts/loaders))、插件([plugins](https://doc.webpack-china.org/configuration/output))的配置。`config/paths.json` 文件是`webpack`配置要用到的路径，下面不少地方会用到`paths.xxxx`。

#### 入口 [entry](https://doc.webpack-china.org/concepts/entry-points)
webpack 可以用多种方式定义入口的属性，应用程序的入口` paths.appIndexJs`放在最后，程序出错才不会对`WebpackDevServer`造成压力，同时仍然能触发文件变动刷新。

> webpack.config.dev.js

```
entry: [
    require.resolve('react-dev-utils/webpackHotDevClient'),
    require.resolve('react-error-overlay'),
    require.resolve('./polyfills'),
    paths.appIndexJs,
]
```
> webpack.config.prod.js

```
entry: [
    require.resolve('./polyfills'),
    paths.appIndexJs,
]
```
#### 输出 [output](https://doc.webpack-china.org/configuration/output)
上面我们提到程序的入口，以及其他插件的入口，入口可以存在多个，但是最终编译的文件只能有一个输出配置`output`。
> webpack.config.dev.js

```
output: {
    // 输出目录
    path: paths.appBuild,
    // 告诉 webpack 在 bundle 保留模块的注释，生产环境应该关闭。
    pathinfo: true,
    // 每个 bundle 输出的路径和名称
    filename: 'static/js/bundle.js',
    // 未被列在entry中，需要打包出来的文件会用占位符name先代替，之后自动分配ID名称
    chunkFilename: 'static/js/[name].chunk.js',
    // 网站根目录，这里是 "/"
    publicPath: publicPath,
    // 还不知道什么鬼，再说
    devtoolModuleFilenameTemplate: info =>
      path.resolve(info.absoluteResourcePath),
  },
```

#### 加载器 [loader](https://doc.webpack-china.org/concepts/loaders)
loader 用于对模块的源代码进行转换，webpack 实际只能处理`JS`文件，loader 是 webpack 提供将其他语言转成`JS`的的重要功能，不同语言会有不同的 loader，如之后会用到的 `TypeScript 的 ts-loader`, `CSS 的 css-loader`、`file-loader...`等。 
loader 的配置方式有3种，推荐在`webpack config`内配置，另外2种可以看文档。`module.rules` 允许你在 webpack 配置中指定多个 loader。
> webpack.config.dev.js

```
{
  test: /\.css$/,
  use: [
    require.resolve('style-loader'),
    {
      loader: require.resolve('css-loader'),
      options: {
        importLoaders: 1,
      },
    },
    {
      loader: require.resolve('postcss-loader'),
      options: {
        ident: 'postcss', // https://webpack.js.org/guides/migrating/#complex-options
        plugins: () => [
        require('postcss-flexbugs-fixes'),
        autoprefixer({
        browsers: [
        '>1%',
        'last 4 versions',
        'Firefox ESR',
        'not ie < 9', // React doesn't support IE8 anyway
        ],
        flexbox: 'no-2009',
        }),
        ],
      },
    },
  ],
}
```

loader 实际就是 node module，方法`callback`会返回当前 loader 处理的结果给下一个 loader，所以loader的处理是有顺序的，从左到右从上到下，上面的`CSS`文件内容依次经过`style-loader`、`css-loader`、`postcss-loader...`，前者的输出作为后者的输入，最终处理之后返回。

```
module.exports = function(source, other) {
  // 具体代码实现
  // return source; 返回单一处理结果
  this.callback(null, source, other); // 返回当前处理结果给下一个loader
};
```
如果你打算自定义一个 loader 可以参考这篇文章 [如何开发一个webpack-loader](http://www.alloyteam.com/2016/01/webpack-loader-1)

#### 插件 [plugins](https://doc.webpack-china.org/configuration/output)
到这里，你可能跟我一样有疑惑，有`loader`了为何还要`plungin`？两个东西有什么区别？

- `loader`：webpack本身只能打包 [CommonJS](http://zhaoda.net/webpack-handbook/commonjs.html) 规范的`JS`文件，对于其他资源例如 CSS、图片、或者其他的语法集，比如 JSX 是没有办法加载的。 这就需要对应的loader将资源转化，加载进来。从字面意思也能看出，loader是用于加载的，它作用于一个个文件上。
- `plugin`：直接作用于 webpack，扩展了它的功能。当然loader也时变相的扩展了 webpack ，但是它只专注于转化文件`transform`这一个领域。而plugin的功能更加的丰富，而不仅局限于资源的加载。

> webpack.config.dev.js

```
# module.plugins 下可以配置多个插件
const xxxPlugin = require('xxxplugin');
const yyyPlugin = require('yyyplugin');

module.exports = {
    plugins:[
        // plugins 实例
        new xxxPlugin(),
        new yyyPlugin()
    ]
}
```
我们可以这样自定义一个插件：
> MyPlugin.js

```
function MyPlugin(options) {
  // 配置
}

MyPlugin.prototype.apply = function(compiler) {
  // ...
  compiler.plugin('compilation', function(compilation) {
    compilation.plugin('my-plugin-xxxx', function(myPluginXxx, callback) {
      // 代码实现
      callback(null, htmlPluginData);
    });
  });
};

module.exports = MyPlugin;
```

### [webpack-dev-server][]
`webpack-dev-server` 就是一个小型的静态文件服务器。使用它，可以为`webpack`打包生成的资源文件提供`Web`服务。在开发过程中，变动文件会触发自动编译同时刷新网页，`WDS`提供了**两种**用于刷新页面的模式。
#### inline模式
启动`inline`模式的`cmd`命令

```
# 默认 --host localhost --port 8080
$ webpack-dev-server --inline
```
启动后打开<http://localhost:8080/index.html>，即可访问本地网站。

#### iframe模式
启动`iframe`模式的`cmd`命令

```
# 不需要加其他指令，默认就是iframe
$ webpack-dev-server
```
启动后打开<http://localhost:8080/webpack-dev-server/index.html>，即可访问本地网站。

### [webpack-manifest-plugin][]
是 [webpack][] 的优化，对编译生成的静态文件加`md5`还是`hash`重命名输出，并记录成清单，用作缓存。
> webpack.config.prod.js
 
```
const ManifestPlugin = require('webpack-manifest-plugin');

module.exports = {
    // ...
    plugins: [
      new ManifestPlugin({
      fileName: 'asset-manifest.json', // 生成清单文件文件名
      // 还有其他配置可以看下文档
      })
    ]
};
```

### [case-sensitive-paths-webpack-plugin][]
强制文件路径大小写敏感，`mac OS`大小写不敏感会导致其他平台引用文件路径出错。
> webpack.config.dev.js

```
const CaseSensitivePathsPlugin = require('case-sensitive-paths-webpack-plugin');

module.exports = {
    // ...
    plugins: [
        // debug模式下可以看到更多的信息
        new CaseSensitivePathsPlugin({debug: true})),
    ]
}
```
### [extract-text-webpack-plugin][]
将`CSS`从`JS`文件中分离开来，成为单独的`.CSS`文件，该插件通常会与[`PostCSS`][]系列的`loader`，如[`css-loader`][]、[`postcss-loader`][]一起使用。

### [html-webpack-plugin][]
就是`HTML`生成插件，将 webpack中`entry`配置的相关入口`thunk`和`extract-text-webpack-plugin`抽取的`CSS`文件插入到该插件提供的`template`或者`templateContent`配置项指定的内容基础上生成一个`html`文件，具体插入方式是将样式`link`插入到`head`元素中，`script`插入到`head`或者`body`中。
> webpack.config.prod.js

```
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    // ...
    plugins: [
        new HtmlWebpackPlugin(), // 不配置任何参数
        new HtmlWebpackPlugin
    ]
}
```


## 参考
- [使用PostCSS进行CSS处理](https://www.ibm.com/developerworks/cn/web/1604-postcss-css/index.html)
- [如何开发一个webpack-loader](http://www.alloyteam.com/2016/01/webpack-loader-1)
- [使用Loader](https://webpack.toobug.net/zh-cn/chapter4/using-loaders.html)

[`webpack`]:https://doc.webpack-china.org
[webpack]:https://doc.webpack-china.org
[webpack-dev-server]:https://doc.webpack-china.org/configuration/dev-server
[webpack-manifest-plugin]:https://github.com/danethurber/webpack-manifest-plugin
[case-sensitive-paths-webpack-plugin]:https://github.com/Urthen/case-sensitive-paths-webpack-plugin
[extract-text-webpack-plugin]:https://github.com/webpack-contrib/extract-text-webpack-plugin
[html-webpack-plugin]:https://github.com/jantimon/html-webpack-plugin
[sw-precache-webpack-plugin]:https://github.com/goldhand/sw-precache-webpack-plugin

[`TypeScript`]:https://zhongsp.gitbooks.io/typescript-handbook/content
[`PostCSS`]:https://github.com/postcss/postcss
[`style-loader`]:https://github.com/webpack-contrib/style-loader
[`css-loader`]:https://github.com/webpack-contrib/css-loader
[`postcss-loader`]:https://github.com/postcss/postcss-loader
[`autoprefixer`]:https://github.com/postcss/autoprefixer

