---
title: ssh的三俩事
tags:
- ssh
- npm
- NodeJS
categories: Coding
date: 2021-11-12 13:52:33
---


最近在项目中用到了git+ssh的形式去安装npm包

![img.png](img.png)

开发时很爽～  
部署时，CI有点问题，但运维也解决了～  
就是开发时，老是有人问，为啥`npm install`报错了？

诶～明明我已经把项目要配置ssh公钥的通知发下去了呀～

直到我在另一位同事电脑前，观看到第一次安装时，卡在了这：

![](error_code.png)

好家伙，突然想起，ssh在第一次和某个域名发生通信时，会有一次校验的！  
但因为需要进行IO交互，所以`npm install`的过程中等不到输入`yes`，于是就卡住了……

可恶

然后当时只想到通过这种形式给know_hosts增加校验

![img.png](ssh_verify.png)

因为需要交互，所以没办法写成脚本放进npm scripts里

……

直到

……

我在找[Hexo部署的GitHub Actions文章](https://sanonz.github.io/2020/deploy-a-hexo-blog-from-github-actions/)时，意外看到了这个～

![](script_from_an_article.png)

```bash
ssh-keyscan github.com >> ~/.ssh/known_hosts
```

⬆️这！不就是我梦寐以求的么？

让我们来看看～

![](ssh-keyscan.png)

很好～

来试试～

![](success.png)

搞定！

那么npm script可以这样写：

```json
{
  "scripts": {
    "secure-install": "ssh-keyscan github.com >> ~/.ssh/known_hosts && npm install"
  }
}
```

![](perfect.png)

完美运行！

