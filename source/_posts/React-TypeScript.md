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

