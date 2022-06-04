---
title: Electron小工具！我的选型之路
date: 2022-06-04 22:00:13
tags:
- NodeJS
- Go
- Python
categories:
- Coding
---
啊任，一个小任务需要前端

提出做个页面

我想着，正好可以练习别的
于是提出用electron做前端

后端python连数据库

我提出electron也能直连数据库

先用了py做后台
打包发现加上pandas和numpy的包，都去到了200m；
而electron都要占用200m

后面完整实现其实大小并不大

但因为安全问题，毕竟要连数据库

于是想到了用Go打包编译一个专门链接数据库的小程序，gRPC远程调用

继续折腾

接下来的目标，用Flutter实现一个完整版的


