# Day 4 · 链表基础

> **日期：** 2026-08-06
> **学习目标：** 链表基础操作——指针操纵与虚拟头节点
> **相关知识页：** [[02-Wiki/专题总结/04-链表]] · [[02-Wiki/专题总结/02-双指针与滑动窗口]]

---

## 一、今日模板回顾

### 链表基础（REV-13）
```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; next = null; }
}
// 判断链表空：head == null
// 遍历：ListNode cur = head; while (cur != null) { ...; cur = cur.next; }
// 数组 vs 链表：数组按下标"算"位置 O(1)，链表只能走 next O(n)
```

### 虚拟头节点
```java
ListNode dummy = new ListNode(0, head);
// ...
return dummy.next;
```

### 链表翻转（三指针）
```java
ListNode pre = null, cur = head;
while (cur != null) {
    ListNode nxt = cur.next;
    cur.next = pre;
    pre = cur;
    cur = nxt;
}
return pre;
```

### 快慢指针（判环）
```java
ListNode slow = head, fast = head;
while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
    if (slow == fast) {
        return true;
    }
}
return false;
```

---

## 二、做题记录

### 1. 相交链表（Easy）
- **核心思路：** 两条链表可能共享后半段节点，找第一个相交节点。解法一：算长度对齐（长的先走差值，再同步走比引用）。解法二（更帅）：双指针交错——pA 走完 A 换去走 B，pB 走完 B 换去走 A，总路程相等，必然在交点相遇
- **代码实现：**
  ```java
  public class Solution {
      public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
          // 解法二：双指针交错法（不数长度）
          if (headA == null || headB == null) return null;
          ListNode pA = headA, pB = headB;
          while (pA != pB) {                        // 不相遇就一直走
              pA = (pA == null) ? headB : pA.next;  // 走完 A → 换去走 B
              pB = (pB == null) ? headA : pB.next;  // 走完 B → 换去走 A
          }
          return pA;                                 // 相遇点 = 交点（或 null）
      }
  }
  ```
- **复杂度：** O(m+n) / O(1)
- **掌握程度：** ✅（学员写出了"长度对齐法"并独立修复 6 个 bug）
- **感悟/易错点：** ⭐ **链表判"同一节点"用 `==`（引用），不是 `.val ==`（值）**（ERR-015）；`while(curA)` 对象不能当 boolean（ERR-016）；`Math.min` 不是 `min`；变量作用域（块内声明外面用不了）；复制粘贴忘改名导致死循环（ERR-016）

### 2. 反转链表（Easy）
- **核心思路：** 让每个节点指向它的前驱。三种写法本质相同：①头插法（新建 dummy，逐个摘节点插到最前）②三指针法（pre/cur/tmp 原地掉头）③递归（信任函数 + 接到尾巴）。核心铁律：**改 next 前先存 tmp = cur.next**
- **代码实现：**
  ```java
  // 写法一：三指针法（原地掉头）⭐推荐面试用
  class Solution {
      public ListNode reverseList(ListNode head) {
          ListNode pre = null, cur = head;
          while (cur != null) {
              ListNode tmp = cur.next;   // ①先存后继，防丢
              cur.next = pre;            // ②掉头指向前面
              pre = cur;                 // ③pre 前进
              cur = tmp;                 // ④cur 前进
          }
          return pre;                    // 新头
      }
  }

  // 写法二：头插法（学员独立想出）
  // dummy 虚拟头，每轮摘 cur 插到 dummy.next，最后返回 dummy.next
  // 写法三：递归（理解中，待复习）
  // ListNode newHead = reverseList(head.next);
  // head.next.next = head; head.next = null; return newHead;
  ```
- **复杂度：** O(n) / O(1)（迭代）/ O(n)（递归栈）
- **掌握程度：** ✅（学员独立想出"头插法"，三指针法已捋顺，递归待复习）
- **感悟/易错点：** 三指针口诀："tmp 是护身符，改 next 前先保存"；"没存 tmp 别改 next，改了后面就丢"；循环结束条件 cur==null，返回 pre；递归关键 = "接到尾巴（head.next）上，不是接到新头（newHead）上"

### 3. 回文链表（Easy）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 4. 环形链表（Easy）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 5. 环形链表 II（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 6. 合并两个有序链表（Easy）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 7. 两数相加（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 8. 删除链表的倒数第 N 个节点（Medium）
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
