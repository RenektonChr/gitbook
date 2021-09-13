# 剑指 Offer月计划 · 第五天——查找算法（中等）



## 第一题：第一个只出现一次的字符

在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 s 只包含小写字母。

**示例：**

```
s = "abaccdeff"
返回 "b"

s = "" 
返回 " "
```

**限制：**

`0 <= s 的长度 <= 50000`

**题解：**

```javascript
/**
 * @param {string} s
 * @return {character}
 */
var firstUniqChar = function(s) {
  const length= s.length;
  const myMap = new Map();
  if(s.length === 0) {
    return " ";
  }

  for(let i = 0; i < length; i++) {
    if(!myMap.get(s[i])) {
      myMap.set(s[i], 1);
    }else {
      myMap.set(s[i] ,myMap.get(s[i]) + 1);
    }
  }

  for(let i = 0; i < length; i++) {
    if(myMap.get(s[i]) === 1) {
      return s[i];
    }
  }

  return " ";
};
```



## 第二题：旋转数组中的最小数字

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一个旋转，该数组的最小值为1。

**示例1：**

```
输入：[3,4,5,1,2]
输出：1
```

**示例2：**

```
输入：[2,2,2,0,1]
输出：0
```

**题解：**

```javascript
/**
 * @param {number[]} numbers
 * @return {number}
 */
var minArray = function(numbers) {
  const len = numbers.length;
  const stack = [];
  for(let i = 0; i < len; i++) {
    if(stack.length === 0) {
      stack.push(numbers[i])
    }else {
      if(numbers[i] < stack[stack.length - 1]) {
        return numbers[i];
      }else {
        stack.push(numbers[i])
      }
    }
  }

  return stack[0];
};
```



## 第三题：二维数组中的查找

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

**题解：**

```javascript
/**
 * @param {number[][]} matrix
 * @param {number} target
 * @return {boolean}
 */
var findNumberIn2DArray = function(matrix, target) {
  if(matrix.length === 0) {
    return false;
  }

  const arrayLength = matrix.length;
  let rowIndex = 0;
  let colIndex = matrix[0].length - 1;
  let current = undefined;

  while(rowIndex < arrayLength && colIndex >= 0) {
    current = matrix[rowIndex][colIndex];
    if(current === target) {
      return true;
    }else if(current > target){
      colIndex = colIndex - 1;
    }else {
      rowIndex = rowIndex + 1;
    }
  }

  return false;
};
```

