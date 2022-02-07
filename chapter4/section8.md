# 每日一练——day02

## 算法：

#### [821. 字符的最短距离](https://leetcode-cn.com/problems/shortest-distance-to-a-character/)

给你一个字符串 s 和一个字符 c ，且 c 是 s 中出现过的字符。

返回一个整数数组 answer ，其中 answer.length == s.length 且 answer[i] 是 s 中从下标 i 到离它 最近 的字符 c 的 距离 。

两个下标 i 和 j 之间的 距离 为 abs(i - j) ，其中 abs 是绝对值函数。

```javascript
/**
 * @param {string} s
 * @param {character} c
 * @return {number[]}
 */

/**
 * 算法思路：
 * 1. 求出c在s中所有的位置下标，形成一个数组。
 * 2. 然后遍历s，求每个字母相对于c的距离，找出最小的。
 */
const shortestToChar = (str, chr) => {

  const getPosition = (str, chr) => {
    const positionList = [];
    let pos = str.indexOf(chr);
    while (pos > -1) {
      positionList.push(pos);
      pos = str.indexOf(chr, pos + 1);
    }
    return positionList;
  }

  const positions = getPosition(str, chr);
  let minDistance = 0;
  let result = [];
  for(let i = 0; i < str.length; i++) {
    for(let p = 0; p < positions.length; p++) {
      let currentDistance = Math.abs(positions[p] - i);
      if(p === 0) {
        minDistance = currentDistance;
      }else {
        minDistance = minDistance < currentDistance? minDistance: currentDistance;
      }
    }
    result.push(minDistance);
  }

  return result;
};
```



## JS手写题：

### 实现symbol polyfill

`题目解释：如果浏览器不支持symbol polyfill的情况下，写出代码让浏览器支持。`

### 解析：

+ Symbol() 是函数，有一个可选的字符串参数。
+ 返回的是原始数据类型 Symbol。
+ 具有唯一性
+ 不允许作为构造器使用，即不允许使用 new，使用之后会报错。
+ 不允许隐式转换成字符串
+ 不允许转换成数字
+ 有两个静态方法 for 和 keyFor
+ 有几个内置 Symbol 属性



如果使用 new ，就报错

如果 description 是 undefined，让 descString 为 undefined

否则 让 descString 为 ToString(description)

如果报错，就返回

返回一个新的唯一的 Symbol 值，它的内部属性 [[Description]] 值为 descString

```JavaScript
(function() {
  const _this = this;


  let NameFix = 0;
  function generateName(descStr) {
    NameFix++;
    return `descStr_${NameFix}`;
  }

  const SymbolPolyfill = function Symbol(description) {

    // symbol不能使用new操作符
    if(this instanceof SymbolPolyfill) {
      throw new Error('Symbol is not a constructor');
    }
  
    // descStr只能是一个字符串
    let descStr = description === undefined? undefined: String(description);
    // toString方法
    let symbol = Object.create({
      toString: function() {
        return this.__Name__;
      },
      // 隐式类型类型转换
      valueOf: function() {
        throw new Error('Cannot convert a Symbol value');
      }
    });
    // 给symbol对象添加属性
    Object.defineProperties(symbol, {
      // 根据文档上的描述symbol是不能枚举，不能更改的。
      '__Description__': {
        value: descStr,
        writable: false,
        enumerable: false,
        configurable: false
      },
      '__Name__': {
        value: generateName(descStr),
        writable: false,
        enumerable: false,
        configurable: false
      }
    });
  
    // 工厂模式，每次返回一个新的对象，其实我也不知道是不是工厂模式。
    return symbol;
  }

  _this.SymbolPolyfill = SymbolPolyfill;
})();

var a = SymbolPolyfill('foo');
var b = SymbolPolyfill('foo');

console.log(a ===  b); // false

var obj = {};
obj[a] = 'hello';
obj[b] = 'world';

console.log(obj);

```

