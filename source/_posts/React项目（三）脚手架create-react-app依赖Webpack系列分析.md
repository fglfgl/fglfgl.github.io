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

#### 插件 [plugins](https://doc.webpack-china.org/configuration/output)



### PostCSS平台
PostCSS提供了多样化的功能插件，通常需要和构建工具使用，我们项目使用的构建工具为[`webpack`][]所以只讲[`webpack`][]如何配置使用PostCSS提供的插件。
 
- [`postcss-loader`][]结合[`webpack`][]做插件处理，这里结合[`autoprefixer`][]（CSS浏览器兼容补全）、[`style-loader`][]（将CSS转换为注入style标签的JS模块）、[`css-loader`][]（自动解析CSS路径）一起使用，实现构建时自动import JS用到的CSS到html中，`webpack.config.dev.js`配置如下:

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

- [`autoprefixer`][]：css浏览器兼容补全

### 参考
- [使用PostCSS进行CSS处理](https://www.ibm.com/developerworks/cn/web/1604-postcss-css/index.html)

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

