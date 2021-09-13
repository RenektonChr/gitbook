# 手写源码系列——Vue1.x



## 零、写在前面

可能会有很多读者在看到这个标题的时候会很迷茫，手写源码的文章很多，手写源码的课程也很多，但是绝大多数都是手写Vue2.x，甚至手写Vue3.x（作者在写这篇文章的时候已经是2021年9月10号了，Vue3已经出现了快两年了。）。到现在了怎么还会有人手写Vue1.x？这个问题其实很好解释，首先说明的一点是，Vue1.x和Vue2.x的根本区别在于Vue2.x引入了虚拟节点（VNode）和Patch算法（也就是我们经常说的Diff算法，Diff算法是React中的，在Vue中叫做Patch算法）。所以我们要想了解Vue的核心原理——数据响应式，以及VNode和Patch算法，以及编译器的发展和引入，手写Vue1.x是最好的出发点，由此我们可以深切的体会到Vue2为什么会引入VNode和Patch算法，这两者的引入解决了Vue1.x的什么问题？

**让我们带着这些问题读文章，我相信会收获很多。**



## 一、new Vue(options)——Vue的初始化

我们在面试的时候，谈到Vue的时候一个老生常谈的问题就是，`new Vue的时候发生了什么？`这个面试题我们在这里就可以找到答案。我们在不适用Vue-cli构建项目的时候通常会直接在页面中引入`vue.js`文件，然后我们就会new Vue(options)。在Vue源码中new Vue做了什么？我们先来看代码：

```javascript
// src/index.js
export default function Vue(options) {
  this._init(options);
}
```

我们会发现所谓的Vue构造函数竟然只有一行代码，就是执行了一个this._init(options)方法。从方法名我们可以判断出来，`this._init`方法主要的任务就是对于Vue进行初始化操作：

```javascript
// src/index.js
export default function Vue(options) {
  this._init(options);
}

Vue.prototype._init = function(options) {
  this.options = options;
  // 数据响应式
  initData(this);
  // 模板编译和挂载
  if(this.options.el) {
    this.$mount()
  }
}

Vue.prototype.$mount = function() {
  mount(this);
}
```

Vue的初始化其实就做了两件事：

+ 数据的响应式处理
+ 模板的编译和挂载



## 二、数据的响应式处理——initData函数

initData函数接收了Vue实例，并对Vue实例上的data数据进行了响应式处理：

```javascript
// src/initData.js
import proxy from './proxy.js';
import observe from './observe.js';

export default function initData(vm) {
  const { data } = vm.$options;
  
  if(!data) {
    vm._data = {};
  }else {
    vm._data = typeof data === 'function'? data(): data;
  }
  
  // 做一层代理
  for(key in data) {
    proxy(vm, '_data', key)
	}
  
  // 数据响应式处理的入口
  observe(vm._data);
}
```

initData函数的实现的是data对象的获取，要判断data是函数还是对象，如果是函数那么直接执行，如果是对象直接赋值。以及data对象中属性的代理，即把`this._data`对象中的属性代理到`this`实例上。这个代理的工作由`proxy`完成：

```javascript
// src/proxy.js

export default function proxy(vm, sourceKey, key) {
  // vm.xxx ----> vm._data.xxx
  Object.defineProperty(vm, key {
  	get() {
    	return vm[sourceKey][key];
  	},
    set(newVal) {
      vm[sourceKey][key] = newVal;
    }
  });
}
```

代理完成之后，我们就可以对于`vm._data`上的属性进行响应式处理了。



**observe函数：**

```javascript
// src/observe.js
import Observer from './Observer.js';

export default function observe(value) {
  if(typeof value !== 'object') return;
  if(value.__ob__) return value.__ob__;
  
  return new Observer(value)
}
```

observe函数干了下面这几件事：

+ 检查value是否为值类型，如果为值类型则直接返回，终止程序。
+ 检查value是否带有`__ob__`属性，如果有则直接返回该属性。
+ 两项检查都不符合，直接进行`new Observer`操作，对value进行响应式处理，并返回响应式对象。



**Observer类（构造函数）：**

```javascript
// src/Observer.js
import protoArgument from './protoArgument.js'

export default Observer(value) {
  // 这里给value绑定__ob__是为了数组的响应式，依赖的收集和更新
  Object.defineProperty(value, '__ob__', {
    value: this,
    configurable: true,
    // 如果为true造成无限递归
    enumerable: false,
    writable: true
  });
  if(Array.isArray(value)) {
    // 数组的响应式处理
    protoArgument(value)
    this.observeArray(value);
  }else {
    // 对象的响应式处理
    this.walk(value)
  }
}

Observer.prototype.walk = function(obj) {
  for(key in obj) {
    defineReactive(obj, key, obj[key]);
  }
}

Observer.prototype.observeArray = function(value) {
  for(item of value) {
    observe(item);
  }
}
```



**defineReactive**

```javascript
// src/defineReactive.js

export default function defineReactive(obj, key, value) {
  // 处理value可能为对象的情况
  observe(value);
  // obj中的每个key做响应式处理
  Object.defineProperty(obj, key, {
    get() {
      console.log(`getter key = ${key}`);
      return value;
    },
    set(newVal) {
      console.log(`setter ${key}: ${value}`);
      if(newVal === value) return;
      value = newVal;
      // 处理新赋的值可能为对象的情况
      observe(value);
    }
  });
}
```

这一段代码是对象响应式的核心，让`obj[key]`通过Object.defineProperty的处理，在getter和setter函数的帮助下，我们就能准确的得知`obj[key]`的读写操作，从而可以实现响应式。



**数组的响应式处理**

```javascript
// src/protoArgument.js

const arrayProto = Array.prototype;
const arrayMethods = Object.create(arrayProto);
const methodsPatch = ['push', 'pop', 'unshift', 'shift', 'split', 'reverse', 'sort'];

methodsPatch.forEach(method => {
  Object.defineProperty(arrayMethods, method, {
    value: function(...args) {
      const result = arrayProto[method]apply(this, args);
      console.log('数组的响应式处理...');
      let inserted;
      switch(method) {
        case 'push':
        case 'unshift':
          inserted = args;
          break;
        case 'split':
          inserted = args.slice(2);
          break;
      }
      
      if(inserted) {
        this.observeArray(inserted)
      }
      return result;
    }
  })
});

export default function protoArgument(value) {
  // 更改数组的原型
  value.__proto__ = arrayMethods;
}
```

数组的响应式做了以下几件事：

+ 监听了七种可以改变原数组的方法。
+ 更换了数组的原型。

这里的重点就在于数组的响应式是利用数组的原型为参数，创造了一个对象（arrayMethods）。然后拦截了七个改变数组的方法，把这七个方法设置到新创建的对象上，并且把新创建的对象更换为数组的原型。

这段代码的难点在于this的指向问题，这段代码中的this实际上是我们在Vue中常用到的`this.arr`，而并不是Vue实例。这样当我们在代码中编写`this.arr.push`这种代码的时候，就会触发arrayMethods对象上的push函数，我们就得知了数组的 变化。这么做的根本原因是数组的监听不太适合使用Object.defineProperty方法。

> **注意这里笔者说的意思是不太适合，并不是Object.defineProperty不能监听数组，而是如果使用Object.defineProperty来监听数组会带来很大的性能问题！**
>
> 关于这个问题，网上很多帖子说Object.defineProperty不能监听数组，这是大错特错的。



至此为止我们把对象和数组的响应式处理全部介绍完了。那么我们现在想一个问题，数据的响应式实现了，**怎么才能通过数据的改变完成视图的改变呢？**

要想解决这个问题，我们需要先思考三个问题：

+ 什么是视图？

+ 怎么改变视图？
+ 谁改变了视图？

第一个问题很好想，在web领域中，视图就是一些DOM元素所组成的显示在网页上的内容。

第二个问题，改变视图的方式无非是使用JS进行DOM操作。

第三个问题，至于是谁改变了视图，一定是有一个方法执行了改变了视图。这个方法在谁那儿，谁就改变了视图，这个**“谁”**就是watcher对象。

我们再思考一个问题：**什么时候更新视图？**

这个问题也很简单，数据发生改变之后我们立即更改视图。

最后一个问题：**我们怎么能得知应该更新那个视图？**

这就引出了一个重要的概念——**依赖**，我们在视图中改变的是那些变化的dom结构，只有用到了响应式数据的dom结构才有可能发生变化，所以我们就说这个视图依赖了响应式数据。即响应式数据是dom结构的依赖。dom结构的更新也就是依赖的更新。我们更改的是含有依赖的dom结构，所以我们要在一个时机把这些依赖收集起来。以便在数据发生改变的时候更改视图，更改视图的动作我们叫做通知依赖更新。那么我们在什么时候收集依赖呢？

**显然的，我们要在读取数据的时候收集依赖，在数据改变的时候通知依赖。**

我们要收集依赖和通知依赖更新需要有两个类：

+ Dep
+ Watcher

Dep是依赖的收集和通知，Watcher是改变dom结构的实体。直接看代码：



**Dep**

```javascript
// src/dep.js

export default function Dep() {
  this.watchers = [];
}

// 依赖收集
Dep.prototype.depend = function() {
  // 防止重复收集依赖
  if(this.watchers.includes(Dep.target)) return; 
  this.watcher.push(Dep.target);
}

// 通知更新
Dep.prototype.notify = function() {
  for(watcher of this.watchers) {
    watcher.update();
  }
}
```

Dep做了两件事：

+ 收集依赖（Dep.target会在Watcher实例化的时候被赋值）
+ 通知依赖更新



**Watcher**

```javascript
// src/watcher.js
import Dep from './dep.js'

export default function Watcher(cb) {
  // watcher带一个回调函数，这个回调函数就是上文说到了改变视图的函数。
  this._cb = cb;
  Dep.target = this;
  // 执行回调函数的时候会触发响应式数据的getter操作，重新收集依赖。
  this._cb();
  // 依赖置空
  Dep.target = null;
}

Watcher.prototype.update = function() {
  this._cb();
}
```

Watcher带有一个回调函数，这个回调函数就是实现dom改变的，Watcher就是改变视图的实体。



到现在为止，我们有了依赖通知和更新所需的所有的实体（Dep、Watcher）。下面我们就开始改造数据响应式的代码，使数据响应式和依赖的通知和更新结合起来，产生关系：

**整个对象的依赖收集**

```javascript
// Observer.js
// src/Observer.js
import protoArgument from './protoArgument.js'
import observe from './observe.js'
import defineReactive from './defineReactive.js'
import Dep from './dep.js'

export default Observer(value) {
  // 这里给value绑定__ob__是为了数组的响应式，依赖的收集和更新
  Object.defineProperty(value, '__ob__', {
    value: this,
    configurable: true,
    // 如果为true造成无限递归
    enumerable: false,
    writable: true
  });
  if(Array.isArray(value)) {
    // 数组的响应式处理
    protoArgument(value)
    this.observeArray(value);
  }else {
    // 对象的响应式处理
    this.walk(value)
  }
}

// 整个对象、数组的依赖收集和更新
value.__ob__.dep = new Dep();

Observer.prototype.walk = function(obj) {
  for(key in obj) {
    defineReactive(obj, key, obj[key]);
  }
}

Observer.prototype.observeArray = function(value) {
  for(item of value) {
    observe(item);
  }
}
```

**对象中每个项的依赖收集和更新**

```javascript
// src/defineReactive.js
import Dep from './dep.js'
import observe from './observe.js'

export default function defineReactive(obj, key, value) {
  // 处理value可能为对象的情况
  const childOb = observe(value);
  const dep = new Dep();
  // obj中的每个key做响应式处理
  Object.defineProperty(obj, key, {
    get() {
      console.log(`getter key = ${key}`);
      if(Dep.target) {
        dep.depend();
        if(childOb) {
          childOb.dep.depend();
        }
      }
      return value;
    },
    set(newVal) {
      console.log(`setter ${key}: ${value}`);
      if(newVal === value) return;
      value = newVal;
      // 处理新赋的值可能为对象的情况
      observe(value);
      dep.notify();
    }
  });
}
```

**数组中依赖收集和更新**

```javascript
// src/protoArgument.js

const arrayProto = Array.prototype;
const arrayMethods = Object.create(arrayProto);
const methodsPatch = ['push', 'pop', 'unshift', 'shift', 'split', 'reverse', 'sort'];

methodsPatch.forEach(method => {
  Object.defineProperty(arrayMethods, method, {
    value: function(...args) {
      const result = arrayProto[method]apply(this, args);
      console.log('数组的响应式处理...');
      let inserted;
      switch(method) {
        case 'push':
        case 'unshift':
          inserted = args;
          break;
        case 'split':
          inserted = args.slice(2);
          break;
      }
      if(inserted) {
        this.__ob__.observeArray(inserted)
      }
      this.__ob__.notify();
      return result;
    }
  })
});

export default function protoArgument(value) {
  // 更改数组的原型
  value.__proto__ = arrayMethods;
}
```



**我们已经取得了阶段性的成果，数据响应式和依赖的通知和更新都已经完成了。接下来就是模板的编译，配合依赖的收集和更新实现视图的更新！**



## 三、模板编译

