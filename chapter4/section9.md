# 每日一练——day03



## 算法题：

**[1381. 设计一个支持增量操作的栈](https://leetcode-cn.com/problems/design-a-stack-with-increment-operation/)**

请你设计一个支持下述操作的栈。

实现自定义栈类 CustomStack ：

+ CustomStack(int maxSize)：用 maxSize 初始化对象，maxSize 是栈中最多能容纳的元素数量，栈在增长到 maxSize 之后则不支持 push 操作。
+ void push(int x)：如果栈还未增长到 maxSize ，就将 x 添加到栈顶。
+ int pop()：弹出栈顶元素，并返回栈顶的值，或栈为空时返回 -1 。
+ void inc(int k, int val)：栈底的 k 个元素的值都增加 val 。如果栈中元素总数小于 k ，则栈中的所有元素都增加 val 。

**示例：**

```
输入：
["CustomStack","push","push","pop","push","push","push","increment","increment","pop","pop","pop","pop"]
[[3],[1],[2],[],[2],[3],[4],[5,100],[2,100],[],[],[],[]]
输出：
[null,null,null,2,null,null,null,null,null,103,202,201,-1]
解释：
CustomStack customStack = new CustomStack(3); // 栈是空的 []
customStack.push(1);                          // 栈变为 [1]
customStack.push(2);                          // 栈变为 [1, 2]
customStack.pop();                            // 返回 2 --> 返回栈顶值 2，栈变为 [1]
customStack.push(2);                          // 栈变为 [1, 2]
customStack.push(3);                          // 栈变为 [1, 2, 3]
customStack.push(4);                          // 栈仍然是 [1, 2, 3]，不能添加其他元素使栈大小变为 4
customStack.increment(5, 100);                // 栈变为 [101, 102, 103]
customStack.increment(2, 100);                // 栈变为 [201, 202, 103]
customStack.pop();                            // 返回 103 --> 返回栈顶值 103，栈变为 [201, 202]
customStack.pop();                            // 返回 202 --> 返回栈顶值 202，栈变为 [201]
customStack.pop();                            // 返回 201 --> 返回栈顶值 201，栈变为 []
customStack.pop();                            // 返回 -1 --> 栈为空，返回 -1
```

**提示：**

+ 1 <= maxSize <= 1000
+ 1 <= x <= 1000
+ 1 <= k <= 1000
+ 0 <= val <= 100
+ 每种方法 increment，push 以及 pop 分别最多调用 1000 次

**分析：**

我们使用JS来解决这个问题，题意很简单，是对栈这个数据结构定义的考察，先看一下这个题目的示例，我们可以得出下面这几个结论：

+ 我们在实例化CustomStack的时候传入一个数字作为栈的最大长度，但是这时候栈是空的，如果我们直接实例化一个长度为3的数组，显然是不行的，因为题意是在执行`new CustomStack(3)`之后，栈为空。
+ push和pop操作和栈的定义是相同的，只不过多了一个最大长度，所以，我们要在CustomStack类中维护一个长度最大值`maxLength`。
+ increment的操作则更加简单，只需要对比maxLength的值，然后分情况进行遍历并且增长。

**题解：**

```javascript
/**
 * @param {number} maxSize
 */
var CustomStack = function(maxSize) {
  this.maxLength = maxSize;
  this.stack = [];
};

/** 
 * @param {number} x
 * @return {void}
 */
CustomStack.prototype.push = function(x) {
	if(this.stack.length < this.maxLength) {
    this.stack.push(x);
  }
};

/**
 * @return {number}
 */
CustomStack.prototype.pop = function() {
	if(this.stack.length !== 0) {
    return this.stack.pop();
  }else {
    return -1;
  }
};

/** 
 * @param {number} k 
 * @param {number} val
 * @return {void}
 */
CustomStack.prototype.increment = function(k, val) {
  let loop = 0;
  if(k < this.stack.length) {
    loop = k;
  }else {
    loop = this.stack.length;
  }
  
  for(let i = 0; i < loop; i++) {
    this.stack[i] = this.stack[i] + val;
  }
};
```



## 手写题：

**手写Promise.allSettled()：**

该**`Promise.allSettled()`**方法返回一个在所有给定的promise都已经`fulfilled`或`rejected`后的promise，并带有一个对象数组，每个对象表示对应的promise结果。

```javascript
function allSettled(promises) {
  if(promises.length === 0) {
    return Promise.resolve([]);
  }
  
  const resultArr = [];
  let counter = 0;
  const len = promises.length;
  
  return new Promise((redolve, reject) => {
    promises.forEach((promise, index) => {
      Promise
      .resolve(promise)
      .then(result => {
        resultArr[index] = { status: 'fulfilled', value: result };
        counter++;
        if(counter === len) {
          redolve(resultArr)
        }
      }).catch(err => {
        resultArr[index] = { status: 'rejected', reason: err };
        counter++;
        if(counter === len) {
          redolve(resultArr)
        }
      })
    })
  })
}
```

