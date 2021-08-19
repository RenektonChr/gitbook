# 剑指 Offer月计划 · 第三天——字符串



## 第一题：替换空格

请实现一个函数，把字符串 `s` 中的每个空格替换成"%20"。

**实例1：**

```
输入：s = "We are happy."
输出："We%20are%20happy."
```

**限制：**

`0 <= s 的长度 <= 10000`

**题解：**

```javascript
/**
 * @param {string} s
 * @return {string}
 */
var replaceSpace = function(s) {
  const resStr = [];
  const l = s.length;
  for(let i = 0; i < l; i++) {
    if(s.charCodeAt(i) === 32) {
      resStr.push('%20');
    }else {
      resStr.push(s[i]);
    }
  }

  return resStr.join('');
};
```



## 第二题：左旋转字符串

字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。

**示例1：**

```
输入: s = "abcdefg", k = 2
输出: "cdefgab"
```

**示例2：**

```
输入: s = "lrloseumgh", k = 6
输出: "umghlrlose"
```

**题解：**

```javascript
/**
 * @param {string} s
 * @param {number} n
 * @return {string}
 */
var reverseLeftWords = function(s, n) {
  const firstStr = s.slice(0, n);
  const secondStr = s.slice(n);

  return secondStr + firstStr;
};
```

