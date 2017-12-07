title: React项目（五）脚手架create-react-app依赖PostCSS系列分析
author: BIGPIG
tags:
  - PostCSS
  - React
categories:
  - React
date: 2017-07-19 13:53:00
---

### PostCSS平台
PostCSS提供了多样化的功能插件，通常需要和构建工具使用，我们项目使用的构建工具为[`webpack`][]所以只讲[`webpack`][]如何配置使用PostCSS提供的插件。
 
- [`postcss-loader`][]结合[`webpack`][]做插件处理，这里结合[`autoprefixer`][]（CSS浏览器兼容补全）、[`style-loader`][]（将CSS转换为注入style标签的JS模块）、[`css-loader`][]（自动解析CSS路径）一起使用，实现构建时自动import JS用到的CSS到html中，`webpack.config.dev.js`配置如下:


- [`autoprefixer`][]：css浏览器兼容补全

