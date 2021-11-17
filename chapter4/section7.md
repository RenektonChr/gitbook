# 每日一练——day01

### 函数柯里化：

```javascript
function curry(func) {
  //此处补全
}
function sum(a, b, c) {
  return a + b + c;
}

let curriedSum = curry(sum);

alert(curriedSum(1, 2, 3)); // 6, still callable normally
alert(curriedSum(1)(2, 3)); // 6, currying of 1st arg
alert(curriedSum(1)(2)(3)); // 6, full currying
```

这道题属于高级柯里化实现：

```javascript
function curry(func) {
  return function curried(...args) {
    if(args.length >= func.length) {
      return func.apply(this, args);
    }else {
      return function(...args2) {
        return curried.apply(this, args.concat(args2));
      }
    }
  }
}

function sum(a, b, c) {
  return a + b + c;
}

let curriedSum = curry(sum);

console.log( curriedSum(1, 2, 3) ); // 6，仍然可以被正常调用
console.log( curriedSum(1)(2,3) ); // 6，对第一个参数的柯里化
console.log( curriedSum(1)(2)(3) ); // 6，全柯里化
```

### 算法：

#### [989. 数组形式的整数加法](https://leetcode-cn.com/problems/add-to-array-form-of-integer/)

对于非负整数 X 而言，X 的数组形式是每位数字按从左到右的顺序形成的数组。例如，如果 X = 1231，那么其数组形式为 [1,2,3,1]。

给定非负整数 X 的数组形式 A，返回整数 X+K 的数组形式。

```javascript
const addToArrayForm = function(num, k) {
  const len = num.length;
  let result = 0;
  for(let i = 0; i < len; i++) {
    result += num[len-(i+1)] * Math.pow(10, i)  
  }
  result += k;

  return String(result).split('').map(item => Number(item));
}

console.log(addToArrayForm([2,7,4], 181));
```

