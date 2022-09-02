---
title: Vue Jsx 踩坑记录 （一）
date: 2022-09-02 15:14:47
tags: Vue
categories: Coding
---

最近在写一个新项目～用了Vue3 + NaiveUI的技术栈

想着尝试一下新东西，于是就尝试一下使用tsx

首先用WebStorm编辑了一个模板，感觉还挺好用的

```vue
<script lang="tsx">
import { defineComponent } from 'vue'

export default defineComponent({
  name: '', // 定义组件名字
  // components: {}, // 引入组件
  // inheritAttrs: true, // Attribute 继承
  // customOptions: {}, // 当前组件实例的初始化选项
  setup() {
    return () => (
      <>
        {/* 模板这里写 */}
      </>
    )
  },
})
</script>

<style scoped>
/* 样式这里写 */
</style>

```

这里就要说到使用tsx可以达到的最大不同了：

**在setup函数的返回值里面，可以直接把模板返回出去**

于是就节省了专门定义模板暴露值的繁琐步骤，减少bug的发生。  
毕竟有时候辛辛苦苦写的东西，没有暴露出去，模版就用不到了；

与`script setup语法糖`相比，也会减少不必要的参数暴露，做到一定程度上的参数隔离。
毕竟不是所有定义到的临时变量都要用去模版上的，对吧？

但是在摸索出这一套自认为不错的实现方式之后～后续的tsx编写就不那么尽如人意了……

首先最大的问题就是：**文档太少啦！！！！！**

我能找到的只有：
- [Babel Plugin JSX for Vue 3.0](https://github.com/vuejs/babel-plugin-jsx)

而且还有一些特殊用法是不全的，比如说接下来我要分享的

### `<component>动态组件`在JSX中的用法

开发中遇到了要使用KeepAlive的地方，文档是这么写的：
![](img_1.png)

一开始理所当然我也这么用了
```vue
<keep-alive>
  <component is={XXXComponent} />
</keep-alive>
```

发现不得行，后来才知道，原来需要引入的组件才能用
![](img.png)

但发现报错变了！
![img.png](img_2.png)

百思不得其解，我甚至还尝试从vue引入Component组件，但这东西并不存在
![img.png](img_4.png)

还用不少关键词找翻找了很多资料

最终，才在山卡拉位置找到正确的使用方法：[Vue3 gracefully uses dynamic component in JSX](https://qdmana.com/2022/03/202203261921295504.html)  
注意，这个网站很像哪种clickbait的流量工厂网站，不过实在找不到原文章了

网站上这么写：
![](img_3.png)

也就是说，正确的应该要这么来：

```vue
<keep-alive>
  { h(resolveComponent('XXXComponent')) }
</keep-alive>
```

额！文档完全没写！

唉，只能吃一堑长一智了
