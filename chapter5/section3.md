# 从0手写Vue SSR

## 零、写在前面

一个服务端渲染的 Vue.js 应用也可以被认为是“同构的”或”通用的“。这意味着应用代码的主体可以同时运行在“服务端”*和*“客户端”。

**为什么选择Vue SSR？**

+ 更好的搜索引擎优化 (SEO)。因为搜索引擎爬虫会直接读取完整的渲染出来的页面。
+ 更快的内容呈现，尤其是网络连接缓慢或设备运行速度缓慢的时候。服务端标记不需要等待所有的 JavaScript 都被下载并执行之后才显示，所以用户可以更快看到完整的渲染好的内容。这通常会带来更好的用户体验，同时对于内容呈现时间和转化率呈正相关的应用来说尤为关键。
+ 极致的性能优化

**权衡之处：**

+ 开发条件受限
+ 配置复杂
+ 服务器端的负载会大一些

**本篇文章用到的技术栈：**

+ Vue3 + vue-router + vuex
+ @vue/server-renderer
+ Webpack5



## 一、服务端渲染 VS 客户端渲染

**服务端渲染的特点：**

+ 服务端渲染首屏加载快
+ 服务端渲染SEO友好
+ 服务器压力大
+ 可见不一定可操作性

**客户端渲染：**

+ 不利于SEO
+ 存在白屏时间
+ 可见即可操作
+ 页面切换流畅，页面切换是本地JS操作

> VueSSR注意事项：
>
> + 避免状态单例
> + 生命周期问题
> + window dom
> + 保证版本对应正确

**Vue SSR的示意图：**

<img src="../assets/images/chapter5/01.png" alt="node-app.png" style="zoom:80%;" />

## 二、实战上手

本篇文章是`从0到1`搭建VueSSR，所以我们不会使用任何的脚手架或者VueSSR的框架——比如Nuxt。而是使用webpack5搭建一个Vue SSR的脚手架。

### 安装必要的工具包

```bash
npm install vue@next
npm install @vue/server-renderer
```

### 创建项目

**源代码结构：**

```bash
src
├── client
│   ├── components
│   └── router
│   └── store
│   └── views
│   └── app.js
│   └── APP.vue
│   └── entry-client.js # 只在浏览器中运行
│   └── entry-server.js # 只在浏览器中运行
├── server
│   ├── router
│   └── app.js
```

按照上面的源码结构创建项目，项目命名为`Vue-SSR`。

创建完项目之后，执行下面的命令：

```bash
$ npm init -y
```

初始化完项目之后，接下来我们梳理一下要做的事情：

+ 客户端搭建
  + vuex
  + vue-router
+ 配置webpack
  + loader
    + vue-loader
    + css-loader
    + css提取
  + plugin
    + html-webpack-plugin
  + entry
  + output
  + devServer
    + webpack server
+ 搭建服务器
  + Koa
  + koa-router
  + koa-static
+ SSR的处理
  + 路由处理
  + 请求处理

### 客户端搭建

**APP.vue**

```vue
<template>
  <div id="app-box"></div>
</template>
<script>
export default defineComponent({
  name: "App",
})
</script>
```

然后在`src/client/views`中创建Home目录和About目录，两个目录中分别创建`index.vue`：

```vue
// src/client/views/Home/index.vue

<template>
  <h1 id="home">home</h1>
</template>
<script>
export default defineComponent({
  name: "Home",
})
</script>
<style scoped>
.home {
  color: blue;
}
</style>
```

```vue
// src/client/views/About/index.vue

<template>
  <h1 id="about">about</h1>
</template>
<script>
export default defineComponent({
  name: "About",
})
</script>
<style scoped>
.about {
  color: red;
}
</style>
```

然后再client目录下再创建一个模板——`index.template.html`，相当于vue脚手架中的`index.html`。

```html
<!-- index.template.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Vue SSR</title>
</head>
<body>
  <div id="app">
    
  </div>
</body>
</html>
```

我们写过Vue的客户端渲染的开发者都知道，最终所有的组件都是挂载到这个html模板中。

**下面我们来编写客户端代码的入口app.js**

客户端入口的作用就是创建Vue实例，将组价挂载到模板的容器中。

```javascript
// src/client/app.js

import { createApp, createSSRApp } from 'vue';
import App from './App.vue';

export default function() {
  // 执行环境判断
  const isSSR = typeof window === undefined;
  const app = (isSSR? createSSRApp: createApp)(App);
  return { app };
}
```

Vue3给我们提供了`createApp`和`createSSRApp`两个方法，分别对应客户端和服务端两中环境下Vue实例的创建。`app.js` 导出一个函数，这个函数通过window对象来判断环境来返回对应环境下的Vue实例。

### 配置webpack

接下来，为了使我们的项目能够运行起来，还需要配置webpack，配置之前先安装webpack和webpack-cli：

```shell
$ npm install webpack webpack-cli -D
```

我们使用webpack是为了解析`.vue`文件，所以还需要安装`vue-loader`：

```shell
$ npm install vue-loader@next
```

我们还需要安装babel来对ES6语法进行处理：

```shell
$ npm install babel-loader @babel/core @babel/preset-env -D
```

> babel的替代插件：
>
> 这里的babel可以使用swc进行替代，和babel的配置相同，但是性能会有很大的提升。

除了处理JS，我们还需要处理CSS，并且提取出CSS单独打包出来。所以`CSS-loader`和`mini-css-extract-plugin`必不可少：

```shell
$ npm install css-loader mini-css-extract-plugin -D
```

到现在为止，前期所需要的包已经全部安装完成了。在根目录创建一个build目录，用来放webpack的各个环境配置文件。首先在build中创建一个`webpack.base.js`：

```javascript
// /build/webpack.base.js

const { resolve } = require('path');

module.exports = {
  output: {
    path: resolve(__dirname, "../dist"),
  },
  module: {
    rules: [
      {
        test: /\.vue$/,
        use: ["vue-loader"]
      },
      {
        test: /\.js$/,
        use: ["babel-loader"],
        exclude: /node_modules/
      }
    ]
  }
}
```

> 这里要注意一个问题：我们在做不同环境的webpack配置的时候，最后是需要合并公共配置的，那么webpack配置的合并需要一个插件是`webpack-merge`
>
> 安装webpack-merge：
>
> `npm install webpack-merge -D`
>
> 并且我们需要把打好包的css和JS自动注入到HTML模板中，就需要html-webpack-plugin：
>
> ``npm install html-webpack-plugin -D``

```

```



