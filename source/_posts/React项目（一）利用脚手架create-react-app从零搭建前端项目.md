title: React项目（一）利用脚手架create-react-app从零搭建前端项目
author: BIGPIG
tags:
  - React
  - TypeScript
  - create-react-app
categories:
  - React
date: 2017-07-18 10:59:00
---
### 脚手架
1. 脚手架的作用在于快速搭建项目框架，是前人从0整理的前端项目需要的依赖，减少必要的重复工作。
2. 如果最开始自己搭建，会晕头转向，因为用到的依赖实在太多，不利于新手学习。
3. 目前流行的脚手架有，[`react-boilerplate`](https://github.com/react-boilerplate/react-boilerplate)、[`react-redux-starter-kit`](https://github.com/davezuko/react-redux-starter-kit)、[`create-react-app`](https://github.com/facebookincubator/create-react-app) ，FaceBook的`create-react-app`可谓后来居上。我没有去比较各自的异同，Facebook更符合我学习的目的，毕竟大厂出的，而且start数已达30K。
4. 下面先基于`create-react-app`的脚手架搭建项目跑起来，之后再分析脚手架用到的依赖，以便今后开发使用。

### 安装脚手架`create-react-app`
> [create-react-app](https://github.com/facebookincubator/create-react-app) 

```
# 安装全局依赖
$ npm install create-react-app -g
```
### 依赖脚手架创建[`TypeScript`](https://zhongsp.gitbooks.io/typescript-handbook/content/)项目
> [create-react-app-typescript](https://github.com/wmonk/create-react-app-typescript)

```
# scaffold-demo 为项目名称
$ create-react-app scaffold-demo --scripts-version=react-scripts-ts
# 完成后进到项目根目录
$ cd scaffold-demo
# 启动项目 打开 http://localhost:3000/
$ npm start
```
进到`scaffold-demo`, 执行脚本`npm run build`会在根目录生成`build`文件夹，里面的文件为网页的静态文件，最终项目结构如下：
 {% qnimg React/create-react-app-scaffold-demo.png title:项目目录 'class:class1 class2' extend:?imageView2/2/w/600 %}

### 部署网站到`nginx`
- 本地安装`nginx`

> 安装完成后，nginx配置在目录`/usr/local/etc/nginx`下，文件`nginx.conf`代码最后会加载根目录`server/*`所有配置文件。

```
$ brew install nginx
```


- 把生成的静态文件发布到`nginx`，同时配置`nginx`。在`server`下新增配置文件`scaffold_demo.conf`，内容如下：

```
server {
    listen 9000;
    server_name  localhost;

    location / {
        # 静态夹文件路径
        root xxx/scaffold-demo/build;
        # 网页入口
        index  index.htm index.html;
    }
    # nginx 处理静态文件
    location /static/ {
        # 静态文件根目录
        root xxxx/scaffold-demo/build;
        # 开启gzip
        gzip on;
        gzip_min_length 1000;
        gzip_types text/plain application/javascript application/x-javascript text/javascript text/css application/xml;
        expires 30d;
    }
}
```
- 配置完成，检查、启动、停止

```
# 测试配置成不成功，不成功会报错
$ nginx -t  
# 启动nginx
$ nginx
# 若修改配置，重新加载配置
$ nginx -s reload 
# 停止nginx
$ nginx -s stop
```
- 到这里项目已经简单部署到本地[http://localhost:9000](http://localhost:9000)，实际项目部署也是更改服务器的`nginx`的配置，实现域名访问。

### 总结
以上步骤完成了项目结构搭建，项目本地部署，简单的跑完的整个流程。接下来会分析脚手架里依赖到的插件，了解其作用以及如何使用它们。等一切都弄清楚了就能顺利的开发，以及开发完的项目该如何配置，打包发布到线上。



