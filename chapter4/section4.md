# 剑指 Offer月计划 · 第四天——查找算法（简单）



## 第一题：数组中重复的数字

在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

**示例1：**

```
输入：
[2, 3, 1, 0, 2, 5, 3]
输出：2 或 3 
```

**限制：**

`2 <= n <= 100000`

**题解：**

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var findRepeatNumber = function(nums) {
  const map = new Map();
  const l = nums.length;
  for(let i = 0; i < l; i++) {
    if(!map.get(nums[i])) {
      map.set(nums[i], 1);
    }else {
      map.set(nums[i], map.get(nums[i]) + 1);
    }

    if(map.get(nums[i]) > 1) {
      return nums[i];
    }
  }
};
```



## 第二题：在排序数组中查找数字

统计一个数字在排序数组中出现的次数。

**示例1：**

```
输入: nums = [5,7,7,8,8,10], target = 8
输出: 2
```

**示例2：**

```
输入: nums = [5,7,7,8,8,10], target = 6
输出: 0
```

**提示：**

+ `0 <= nums.length <= 105`
+ `-109 <= nums[i] <= 109`
+ `nums 是一个非递减数组`
+ `-109 <= target <= 109`

**题解：**

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var search = function(nums, target) {
  let length = nums.length;
  if(length === 0) return 0;
  let firstPosition = findFirstPosition(nums, target);
  if(firstPosition === -1) {
    return 0;
  }

  let lastPosition = findLastPosition(nums, target);

  return lastPosition - firstPosition + 1;
};

function findFirstPosition(nums, target) {
  let length = nums.length;
  let left = 0;
  let right = length - 1;
  
  while(left < right) {
    let middle = Math.floor((left + right)/2);
    if(nums[middle] < target) {
      left = middle + 1;
    }else if(nums[middle] === target) {
      right = middle;
    }else {
      right = middle - 1;
    }
  }

  if(nums[left] === target) {
    return left;
  }
  return -1;
}

function findLastPosition(nums, target) {
  let length = nums.length;
  let left = 0;
  let right = length - 1;
  
  while(left < right) {
    let middle = Math.ceil((left + right)/2);
    if(nums[middle] < target) {
      left = middle + 1;
    }else if(nums[middle] === target) {
      left = middle;
    }else {
      right = middle - 1;
    }
  }

  return left;
}
```



## 第三题：0～n-1中缺失的数字

一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围0～n-1之内。在范围0～n-1内的n个数字中有且只有一个数字不在该数组中，请找出这个数字。

**示例1：**

```
输入: [0,1,3]
输出: 2
```

**示例2：**

```
输入: [0,1,2,3,4,5,6,7,9]
输出: 8
```

**限制：**

`1 <= 数组长度 <= 10000`

**题解：**

```javascript
var missingNumber = function(nums) {
  const length = nums.length;
  let left = 0;
  let right = length - 1;

  while(left <= right) {
    let mid = Math.floor((left + right)/2);
    if(nums[mid] === mid) {
      // 证明[left, mid]不缺少数字
      left = mid + 1;
    }else if(nums[mid] > mid) {
      right = mid - 1;
    }
  }

  return left;
};
```

