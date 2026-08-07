# Day 5 · 链表进阶

> **日期：** 2026-08-07
> **学习目标：** 链表进阶——递归、多指针与复杂操作
> **相关知识页：** [[02-Wiki/专题总结/04-链表]]

---

## 一、今日模板回顾

### 递归两两交换（24）⭐ 递归套路样板
```java
public ListNode swapPairs(ListNode head) {
    if (head == null || head.next == null) return head;  // ① 基准
    ListNode nextPair = swapPairs(head.next.next);        // ② 递归处理"剩下的"
    ListNode second = head.next;                          // ③ 交换前两个
    second.next = head;
    head.next = nextPair;
    return second;                                        // ④ 返回新头
}
```

### K 个一组翻转
```java
// 核心：分组翻转 + 组间重连
ListNode groupHead = pre.next;
ListNode nextGroup = cur.next;
pre.next = reverseSublist(groupHead, k);
groupHead.next = nextGroup;
pre = groupHead;
```

### LRU 缓存
```java
// 核心：哈希表 + 双向链表
// get: 查到后移到头部
// put: 满时淘汰尾部
```

---

## 二、做题记录

### 1. 两两交换链表中的节点（Medium）
- **核心思路：** 每两个一组交换，dummy 虚拟头简化。迭代版：pre/p 双指针，三步重连（先接第二、再第一指向第三、第二回头指第一）。递归版：换前两个 + 信任递归处理剩下的（head.next.next 是"剩下的开头"）
- **代码实现：**
  ```java
  // 迭代版（学员独立写出）
  class Solution {
      public ListNode swapPairs(ListNode head) {
          ListNode dummy = new ListNode(-1);
          ListNode p = head;
          dummy.next = head;
          ListNode pre = dummy;
          while (p != null && p.next != null) {
              pre.next = p.next;          // 1. 接到第二个
              p.next = p.next.next;       // 2. 第一个指向第三个
              pre.next.next = p;          // 3. 第二个回头指第一个
              pre = p;                    // 移动 pre
              p = p.next;                 // p 后移
          }
          return dummy.next;
      }
  }

  // 递归版（REV-18 复习）
  // if (head == null || head.next == null) return head;
  // ListNode nextPair = swapPairs(head.next.next);
  // ListNode second = head.next;
  // second.next = head;
  // head.next = nextPair;
  // return second;
  ```
- **复杂度：** O(n) / O(1)（迭代）/ O(n)（递归栈）
- **掌握程度：** ✅（学员独立写出迭代版 AC，递归版已理解）
- **感悟/易错点：** 三步重连顺序不能乱；递归版 `head.next.next` = "剩下的开头"（两人一组报数，报完从第3个开始）；递归套路骨架：基准 → 递归处理后面 → 处理自己这一环 → 返回新头（REV-18）

### 2. K 个一组翻转链表（Hard）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 3. 随机链表的复制（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 4. 排序链表（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 5. 合并 K 个升序链表（Hard）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 6. LRU 缓存（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

---

## 三、今日总结

**学到的新模板/技巧：**
-

**遇到的困难：**
-

**遗留问题（需复习）：**
-

**整体感受：** 😊 😐 😢
