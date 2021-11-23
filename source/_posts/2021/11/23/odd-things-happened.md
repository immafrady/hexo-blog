---
title: 奇怪的事情发生了  
tags:
- ssh
- npm
- NodeJS  
categories: Coding  
date: 2021-11-23 10:54:13
---
前些天提到的git+ssh形式，本以为CI已经解决了ssh的身份鉴权问题。

没想到一升级版本号，立刻又抛错！

简直无语😓

![](error.png)

结果，运维折腾了一下。

发现删了package-lock.json中和组件相关的描述，就没问题了……

这也挺奇葩的，因为一旦有lock，哪怕它之前成功，接下来也没办法成功了。

后来，运维又发现，额，用yarn就没事了。

震惊我一百年。

虽然明白yarn和npm的安装是有区别的，可这差异也太大了吧。
