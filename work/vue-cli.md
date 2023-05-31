# 手动实现vue-cli

## 零、面向人群



## 一、前置知识的讲解（大约要说半小时到一小时）

1. cli是什么？脚手架（Webpack、rollup自己配置文件， 然后处理并且打包   vue、React ----》 JS CSS HTML  ）
1. CLI   命令行交互   人机交互   cli根据使用这的选项  最终生成项目模板
1. 明确`脚手架、CLI`的概念！！！！！
2. 为什么会有vue-cli？
3. vue-cli解决了什么问题？
4. vue-cli和vue的关系（其实关系不大）
5. 为什么要学习vue-cli？
6. vue-cli和前端工程化的关系



## 二、Vue-cli的大致工作过程的拆分（这个部分不涉及源码的分析，只是通过现象做分析）

1. Vue-cli最终生成的结果（当然是vue完整的SPA项目，这里只考虑SPA，不考虑VueSSR）
2. Vue-cli是怎么生成的？（这里需要引出vue-cli模板的概念）
3. 展示vue-cli官方使用的模板文件
4. 远端模板的配置（这里要引出怎么支持用户自定义模板）
5. 通过对于vue-cli的工作过程的分析总结出vue-cli工作的流程
6. 下面就是用代码来实现这个流程



## 三、vue-cli的工作流程分析

1. 初始化：PATH ---> vue
2. 获取用户配置：交互的过程
3. 解析配置信息：用户配置 + 默认配置 ----> 配置
   1. `这里有一个问题，用户的配置是如何覆盖默认配置的`
4. 生成项目文件：配置 + 模板（官方模板）-----> 项目文件   `怎么生成？`
   1. 官方模板：git拉取
   2. 自定义模板
      1. template----> 模板文件
      2. meta.js/json---->模板引擎的扩展  `不同的模板这个文件一样吗？`
         1. Prompts -------> 弹框信息  `怎么用的？`
         2. helpers----->模板引擎的扩展
         3. complete---->钩子函数  `什么时候执行？`
         4. metalsmith----->站点生成器  `怎么用metalsmith？`
5. 清理工作，完成



## 四、一些工具——解决上述问题的工具

1. Commander.js  命令行
2. Inquirer.js 弹框工具，做用户交互
3. chalk.js  粉笔，文字改颜色
4. ora  加载状态，进度条....
5. execa   执行shell命令   返回的是promise
6. metalSmath 静态模板生成器
7. handleBar  模板语法的扩展
8. consolidate 模板渲染器



## 五、vue-cli的源码目录

+ lib ---> 核心逻辑
+ test---->测试
+ bin---->命令
+ package.json
  + bin: 用户自定义命令
  + npm link（命令的软链接，链接到全局或者某一个空间下）
  + 全局命令可以直接执行
  + node_modules中的.bin: npm run xxx -----> xxx 添加到 PATH  然后在执行  -----> 删除
  + PATH（全局环境变量）



## 六、手写第一个命令

\#!usr/bin/env node

表示了这个文件是node的可执行文件



**利用一个命令弄清楚下面这些node工具的使用**

+ npm link
+ commander.js
+ chalk.js
+ ora.js
+ Inquirer.js



## 七、进入真正的手写

主要实现以下这两个命令

+ vue-list
+ vue-init



+ Vue（主命令）
  + init
    + 初始化Usage
    + Help初始化
    + 根据属性初始化配置
    + 本地、 非本地
    + generate
  + list  很简单接口调用





名字：自定义脚手架

同学已经掌握完webpack、脚手架全部内容

1. 课程意义——需要自定义脚手架，满足需求。以vue-cli为例，搭一个差不多的。
2. 直接上代码
   1. 如果工具复杂，单量出来一节课





































