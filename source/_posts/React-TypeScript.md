title: React+TypeScript
author: BIGPIG
tags:
  - React
  - TypeScript
categories:
  - React
date: 2017-07-07 15:48:00
---
### 新建项目
#### 新建工程文件夹
```
$ mkdir TypeScriptDemo && cd TypeScriptDemo
```
#### 初始化工程
除了package name 其他都默认敲回车即可

```
$ npm init
package name: (TypeScriptDemo) TypeScriptDemo
...
Is this ok? (yes) yes
```
#### 组织目录结构

`src`目录存放工程代码，`dist`最终由webpack生成
```
TypeScriptDemo/
├─ dist/
└─ src/
   └─ components/
```
### 安装依赖
* 安装全局webpack

```
npm install -g webpack
```
* 安装TypeScript

```
$ npm install typescript --save-dev
```

### 配置WebStorm自动编译ts文件
打开配置页面`WebStorm>Preferences`快捷键`⌘,`，按照如下配置，步骤2为`node`环境目录

 {% qnimg React/WebStorm-tpyeScript-Setting.png %}
---
### 参考文献
- [app-root-path](https://www.npmjs.com/package/app-root-path)
- [autoprefixer]()

> Autoprefixer是一个后处理程序，你可以同Sass，Stylus或LESS等预处理器共通使用。它适用于普通的CSS，而你无需关心要为哪些浏览器加前缀，只需全新关注于实现，并使用W3C最新的规范。

3. [case-sensitive-paths-webpack-plugin]()
4. [chalk]()
5. [cli-highlight]()
6. [css-loader]()
7. [dotenv]()
8. [extract-text-webpack-plugin]()
9. [file-loader]()
10. [fs-extra]()
11. [fsevents]()
12. [html-webpack-plugin]()
13. [jest]()
14. [object-assign]()
15. [postcss-flexbugs-fixes]()
16. [postcss-loader]()
17. [promise]()
18. [react-dev-utils]()
19. [react-error-overlay]()
20. [source-map-loader]()
21. [style-loader]()
22. [sw-precache-webpack-plugin]()
23. [ts-loader]()
24. [tslint]()
25. [tslint-loader]()
26. [tslint-react]()
27. [typescript]()
28. [url-loader]()
29. [webpack](https://doc.webpack-china.org)
30. [webpack-dev-server](https://segmentfault.com/a/1190000006670084)
31. [webpack-manifest-plugin]()
32. [whatwg-fetch]()





