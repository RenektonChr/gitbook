# 剑指 Offer月计划 · 第二天——链表



## 第一题：从尾到头打印链表

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

**示例：**

```
输入：head = [1,3,2]
输出：[2,3,1]
```

**题解：**

```javascript
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} head
 * @return {number[]}
 */
var reversePrint = function(head) {
    const nodeList = [];
    const resList = [];
    while(head) {
        nodeList.push(head.val);
        head = head.next;
    }

    for(let i = 0, l = nodeList.length; i < l; i++) {
        resList.push(nodeList.pop());
    }

    return resList;
};
```



## 第二题：翻转链表

定义一个函数，输入一个链表的头节点，反转该链表并输出反转后链表的头节点。

**示例：**

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

**题解：**

```javascript
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
var reverseList = function(head) {
    let curr = head;
    let prev = null;
    while(curr) {
        let next = curr.next;
        curr.next = prev
        prev = curr;
        curr = next;
    }

    return prev;
};
```

