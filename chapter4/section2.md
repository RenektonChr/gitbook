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



## 第三题：复杂链表的复制

请实现 copyRandomList 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。

**示例1：**

<img src="../assets/images/chapter4/e1.png" alt="node-app.png" style="zoom:80%;" />

```
输入：head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
输出：[[7,null],[13,0],[11,4],[10,2],[1,0]]
```

**示例2：**

<img src="../assets/images/chapter4/e2.png" alt="node-app.png" style="zoom:80%;" />

```
输入：head = [[1,1],[2,1]]
输出：[[1,1],[2,1]]
```

**示例3：**

<img src="../assets/images/chapter4/e3.png" alt="node-app.png" style="zoom:80%;" />

```
输入：head = [[3,null],[3,0],[3,null]]
输出：[[3,null],[3,0],[3,null]]
```

**示例4：**

```
输入：head = []
输出：[]
解释：给定的链表为空（空指针），因此返回 null。
```

**提示：**

- `-10000 <= Node.val <= 10000`
- `Node.random` 为空（null）或指向链表中的节点。
- 节点数目不超过 1000 。



**题解：**

```javascript
/**
 * // Definition for a Node.
 * function Node(val, next, random) {
 *    this.val = val;
 *    this.next = next;
 *    this.random = random;
 * };
 */

/**
 * @param {Node} head
 * @return {Node}
 */

const copyRandomList = function(head) {
  if(!head) {
    return null;
  }
  
  const map = new Map();
  let node = head;
  while(node) {
    map.set(node, new Node(node.val));
    node = node.next;
	}
  
  node = head;
  while(node) {
    map.get(node).next = node.next? map.get(node.next): null;
    map.get(node).random = node.random? map.get(node.random): null;
    node = node.next;
  }
  
  return map.get(head);
}
```

