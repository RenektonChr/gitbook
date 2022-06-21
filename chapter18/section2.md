# 深度剖析实现symbol polyfill

## 前言

在阅读这一篇文章之前我们要弄懂两个问题：

+ 什么是polyfill？
+ 什么是symbol？

**来解答第一个问题：什么是polyfill？**

这个问题比较简单，用一句话就可以解释清楚：**用于实现浏览器不支持的原生API。**举个例子，IE6作为一款非常古老的浏览器，它是不支持Promise的，我们就可以写一个Promise polyfill，使得IE6支持Promise。

**第二个问题：什么是symbol**

Symbol是ES6新增的一种基本数据类型，最大的特点就是Symbol的值是唯一的，不会重复。如果想要得到一个Symbol值，需要调用Symbol()。

> Symbol不是一个构造函数，不能被new。

Symbol上有大概15个方法。如果使用typeof操作符对symbol值进行运算会得到"symbol"。



## Symbol的一般性质

上文说到Symbol是ES6新引入的基本数据类型，表示了一个独一无二的值。Symbol有如下的性质：

+ Symbol类型的值一般称之为“符号”，符号需要通过`Symbol()`函数初始化。

+ Symbol符号的出现就是为了生成一个唯一的、不可变的值。

+ Symbol符号是原始数据类型，所以TypeOf操作符会返回"symbol"。

+ 调用`Symbol()`函数时，可以传一个字符串参数，作为符号值的描述。但是要注意的是传入的字符串只是符号的描述并不能决定符号的值，所以尽管传入的字符串相同，得到的符号的值也不同。

+ `Symbol()`函数不能作为构造函数，不能被new，这样设计的目的是为了避免创建符号包装对象。

+ 可以使用`全局符号注册表`来创建或者重用符号。为此需要使用`Symbol.for(desStr)`。只要是通过全局符号注册表创建的符号desStr相同的话，那么两个符号就是相等的。使用`Symbol()`和`Symmbol.for()`两种方式初始化的符号即使描述相同也并不相等。

  ```javascript
  let fooSymbol = Symbol.for('foo');
  let barSymbol = Symbol.for('bar');
  
  console.log(fooSymbol === barSymbol);			// false
  
  let otherFooSymbol = Symbol.for('foo');
  
  console.log(fooSymbol === otherFooSymbol); 		// true
  
  let globalSymbol = Symbol.for('renekton');
  let localSymbol = Symbol('renekton');
  
  console.log(globalSymbol === localSymbol);		// false
  ```

+ 使用符号作为对象的属性名——使用计算属性的方法

+ Symbol拥有静态15个属性。



## Symbol的具体实现

```javascript
// 立即执行函数

(function() {
    var root = this;

    var generateName = (function(){
        var postfix = 0;
        return function(descString){
            postfix++;
            return '@@' + descString + '_' + postfix
        }
    })()

    var SymbolPolyfill = function Symbol(description) {

        if (this instanceof SymbolPolyfill) throw new TypeError('Symbol is not a constructor');

        var descString = description === undefined ? undefined : String(description)

        var symbol = Object.create({
            toString: function() {
                return this.__Name__;
            },
            valueOf: function() {
                return this;
            }
        })

        Object.defineProperties(symbol, {
            '__Description__': {
                value: descString,
                writable: false,
                enumerable: false,
                configurable: false
            },
            '__Name__': {
                value: generateName(descString),
                writable: false,
                enumerable: false,
                configurable: false
            }
        });

        return symbol;
    }

    var forMap = {};

    Object.defineProperties(SymbolPolyfill, {
        'for': {
            value: function(description) {
                var descString = description === undefined ? undefined : String(description)
                return forMap[descString] ? forMap[descString] : forMap[descString] = SymbolPolyfill(descString);
            },
            writable: true,
            enumerable: false,
            configurable: true
        },
        'keyFor': {
            value: function(symbol) {
                for (var key in forMap) {
                    if (forMap[key] === symbol) return key;
                }
            },
            writable: true,
            enumerable: false,
            configurable: true
        }
    });

    root.SymbolPolyfill = SymbolPolyfill;

})();
```



























