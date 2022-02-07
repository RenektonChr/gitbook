# 每日一练——day03



#### [394. 字符串解码](https://leetcode-cn.com/problems/decode-string/)

给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: k[encoded_string]，表示其中方括号内部的 encoded_string 正好重复 k 次。注意 k 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 k ，例如不会出现像 3a 或 2[4] 的输入。

**示例1：**

```
输入：s = "3[a]2[bc]"
输出："aaabcbc"
```

**示例2：**

```
输入：s = "3[a2[c]]"
输出："accaccacc"
```

**示例 3：**

```
输入：s = "2[abc]3[cd]ef"
输出："abcabccdcdcdef"
```

**示例 4：**

```
输入：s = "abc3[cd]xyz"
输出："abccdcdcdxyz"
```

**分析：**

这个题是一个经典的栈操作的题目，我们需要两个栈，一个字符栈，一个数字栈，出栈入栈的操作根据当前遍历的字符是做括号还是右括号进行判断：

+ 如果是数字，直接把当前的数字转化成整数，作为计算倍数。
+ 如果是字符，延长当前的临时字符串。
+ 如果是左括号，我们需要把当前的临时字符串和计算倍数推入栈。
+ 如果是右括号，弹出状态，组合字符串。

**题解：**

```typescript
// '3[a2[bc]]'
function decodeString(s: string): string {
  let stack = [];
  let str = "";
  let num = 0;
  
  for(c of s) {
    if(!isNaN(Number(c))) {
      // 当前字符为数字
      num = num * 10 + Number(c);
    }else if(c === '[') {
      // 当前字符为左括号
      stack.push(str);
      stack.push(num);
      num = 0;
      str = '';
    }else if(c === ']') {
      // 当前字符为右括号
      let repeatNum = stack.pop();
      let popStr = stack.pop;
      str = popStr + str.repeat(repeatNum);
    }else {
      // 当前字符为字母
      str = str + c;
    }
  }
};
```



