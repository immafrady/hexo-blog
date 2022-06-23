---
title: Electron小工具！我的选型之路
tags:
  - NodeJS
  - Go
  - Python 
categories:
  - Coding
date: 2022-06-22 22:00:13
---

前些日子，开周会时，leader提到了要给公司内部人员做个小工具，他说"后端代码已经写好了，前端弄个界面就好了"  
有幸见识过后端为了完成任务所写的简单页面，那简直惨不忍睹……  
但是，这么好的机会，不拿来练手简直说不过去！

于是我当即提出，"如果是小工具的话，可以试试使用Electron"

理由如下

> - 鉴于现在服务器上同时在跑的服务太多了，做成客户端可以减轻服务器的压力

leader觉得问题不大，就让我去调研了

### Day1

客户端选型： Typescript + Electron + Vue3 + NaiveUI

这里推荐一下模版仓库[Deluze / electron-vue-template](https://github.com/Deluze/electron-vue-template)，在它的帮助之下项目很快就搭建起来了。  
而且它的项目文件放得非常规整，启动逻辑清晰易懂，很适合拿来学习Electron项目。  
也很适合拿来魔改成"Electron + Angular/React"。  
同时得益于它的`electron-builder`配置非常完善，可以有效缩短了客户端开发中最难的一步："打包/编译/发布"的踩坑时间

### Day2

在看了看了最初的demo之后，leader觉得不错，想叫我和他前后端联调一下。
我转念一想，既然客户端能做，那后端那一块应该也能做进客户端里～

于是使用`git submodule`，把后端python代码也拖进项目来，改了一下启动脚本。通过node `child_process`拉起了python脚本，发现运行情况十分良好。

于是折腾一下打包，不试不知道，一试吓一跳。  
使用pyinstaller打包python项目，在我的黑苹果上，竟然跑了40分钟才把可执行文件打包出来，而且打出来的包，竟然去到了200Mb！！  
要知道electron自身也要用掉将将200M的空间。

与此同时，leader也在自己电脑那里试了下另一种打包方式：python源码不压缩，但把整个python环境打包进去。  
这一通操作下来，打包时间的确减少了，5分钟解决战斗，可是大小还是200Mb左右……

这一条路： Dead End

### Day3

后端的工作实际上仅仅是数据库查找，然后数据归类而已。  
这种简单的工作，python能做，nodejs照样能做！

心动不如行动，立刻着手后端代码整改～  
很快搞定！ 甚至把与python后端的http请求交互，改造成了electron的ipc调用，降低了程序被抓取数据的风险～

leader表示很满意，但也提出："因为是从客户端去调用数据库，所以代码加密防止凭证泄漏是很重要的"

于是乎调研的重点变成了如何加密nodejs代码

- 一开始以为它打包出来的asar文件无懈可击，但是直接使用npm包"asar"就能解压缩，简直就像裸奔
- 代码混淆，可是最关键的数据库用户名密码那些，还是字符串，无法被混淆，按照特征还是能找出来的

后来翻到一篇不错的文章[《基于 Node.js Addon 和 v8 字节码的 Electron 代码保护解决方案》](https://juejin.cn/post/6968291704071782430)，基本上思路很清晰了。

感觉v8字节码是个很不错的思路，但文章也提到了它的局限性

> #### 在代码保护上的局限
>
> v8 字节码不保护字符串，如果我们在 JS 代码中写死了一些数据库的密钥等信息，只要将 v8 字节码作为字符串阅读，还是能直接看到这些字符串内容的。当然，简单一点的方法就是使用 Binary 形式的非字符串密钥。

如此以来折腾的成本大大提升，该如何是好呢？

### Day4

突然想到，既然能用python，那就也能用别的语言做后台～  
于是想到了Go，毕竟相比python，Go小而美哈哈。  
leader还问我"那为啥不用Rust"，我说rust不会……😅

一通折腾下，用Go复刻了一下代码逻辑，再用gRPC和Electron端进行交互，最后修改了一下启动脚本。  
完美，Go打包出来的源码大小只有14Mb，gRPC的通信也不太容易抓包分析，更重要的是Go逆向工程是需要点时间的，只要别人不知道我是用Go写的，估计也一时半会不太能下得去手。

于是乎主方案确定了：
- Go： 负责数据库交互
- Electron(主进程)： 使用gRPC远程调用，从Go获取数据
- Electron(渲染进程)：使用ipc与主进程进行交互

至此，方案确定，正式进入开发

### 一点小坑

一开始，我打算把Go二进制文件直接复制进resource里一起打包的，但是当它被塞进asar压缩包之后，诡异的事情发生了。

```ts
import { app } from 'electron'
import childProcess from 'child_process'


const LOCATION = path.join(app.getAppPath(), 'bin', 'main.exe')
goProcess = childProcess.spawn(GO)
```
此时无法从子进程拉起Go……

后来找到了问题的原因，跟这个问答高度相似 [Electron app shows child process error and it doesn't work](https://stackoverflow.com/questions/63264606/electron-app-shows-child-process-error-and-it-doesnt-work)

最后，把存放二进制的文件夹排除掉，才得以解决

### 展望

这个工具开发好了之后，打算用Flutter去重构一遍～  
毕竟最近Flutter支持了全平台，怎么也得试着玩一下的对吧～
