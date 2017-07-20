title: React项目（二）脚手架create-react-app依赖分析
author: BIGPIG
tags:
  - create-react-app
  - react-scripts-ts
  - React
categories:
  - React
date: 2017-07-18 15:24:00
---
## 使用[`WebStome`][]
### 下载安装
[下载](https://www.jetbrains.com/webstorm/download/)安装[`WebStome`][]并激活，激活方式参考[JetBrains激活](http://www.imsxm.com/jetbrains-license-server.html)，完成之后`open`项目`scaffold-demo`，下一步配置。
### 配置[`WebStome`][]自动编译
打开配置页面`WebStorm->Preferences`快捷键`⌘,`，按照如下配置，步骤2为`node`环境目录

{% qnimg React/WebStorm-tpyeScript-Setting.png title=IDE配置 'class:class1 class2' extend:?imageMogr2/2/w/600}

## 项目react-scripts-ts
> `react-scripts-ts`是[`create-react-app`][]最重要的依赖项目，`react-scripts-ts`本身依赖了很多插件，正式由这些插件为基础支撑起了整个项目。

定位`react-scripts-ts`。为何这个项目是重点？还记得如何启动服务吗？`npm start`命令实际上真正的来源是`根目录/package.json`的`scripts`命令：

```
 "scripts": {
    "start": "react-scripts-ts start",
    "build": "react-scripts-ts build",
    "test": "react-scripts-ts test --env=jsdom",
    "eject": "react-scripts-ts eject"
  }
```
追根溯源，`react-scripts-ts`是dependencies依赖进来的可以在`node_modules`下找到，`react-scripts-ts`项目结构如下(红框部分是我们分析的重点)：

{% qnimg React/react-scripts-ts-catalogue.png title:项目目录 'class:class1 class2' extend:?imageMogr2/2/w/600 %}

## config

## 分析`package.json`
打开`package.json`你可以看到几十个依赖（见下图），这些依赖是开发的关键，这也是为什么出现脚手架的原因。毕竟从0开始搭框架不清楚要用什么东西，分分钟搞到你放弃。当然，如果你有一定前端基础，一步步摸索，更加深刻的去理解搭建过程中用到的东西，明白其中的原理也是不错的选择。
{% qnimg React/react-scripts-ts-package.png title:依赖的插件 'class:class1 class2' extend:?imageMogr2/2/w/600 %}

### 我把`package.json`中依赖的插件分为几个部分

- [`webpack`][]
- [`TypeScript`][]
- [`PostCSS`][]

在接下的的学习中分析每个大块以及块内依赖的作用以及关联




[`create-react-app`]:https://github.com/facebookincubator/create-react-app
[`webpack`]:https://doc.webpack-china.org
[`TypeScript`]:https://zhongsp.gitbooks.io/typescript-handbook/content
[`PostCSS`]:https://github.com/postcss/postcss
[`WebStome`]:https://www.jetbrains.com/webstorm


