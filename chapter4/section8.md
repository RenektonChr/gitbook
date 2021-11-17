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

```
...
```

