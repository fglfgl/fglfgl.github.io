title: Hexo命令
author: BIGPIG
tags:
  - Hexo
categories:
  - Hexo
date: 2017-07-01 16:19:00
---
### Hexo基本命令
- 清除已生成网页
```
$ hexo clean
```
- 构建生成静态网页
```
$ hexo generate 
# 或
$ hexo g
```
- 启动服务
```
$ hexo server
# 或
$ hexo s
```
- 部署到服务器
```
$ hexo deploy
# 或
$ hexo d
```

### Hexo 插件命令
- 七牛：[官网](http://qiniu.com)、[插件地址](https://github.com/gyk001/hexo-qiniu-sync) 
 - 显示插件版本，作者及Github地址信息等 
 ```
 $ hexo qiniu info
 # 或
 $ hexo qiniu i
 ```
 - 同步静态资源到七牛空间 
 ```
 $ hexo qiniu sync
 # 或
 $ hexo qiniu s
 ```
 - 同步上传那些本地与七牛空间有差异的文件
 ```
 $ hexo qiniu sync
 # 或
 $ hexo qiniu s
 ```
 {% qnimg jacky.jpg %}