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
- **核心思路：** 三步走：①快慢指针找中点（slow 停在后半段起点）②反转后半段（三指针法）③前半段 vs 反转后的后半段比较。O(1) 空间，不复制整条链
- **代码实现：**
  ```java
  class Solution {
      public boolean isPalindrome(ListNode head) {
          // 1. 快慢指针找中点（fast 走2步、slow 走1步）
          ListNode slow = head, fast = head;
          while (fast != null && fast.next != null) {   // 先判 fast 再判 fast.next（短路）
              slow = slow.next;
              fast = fast.next.next;
          }
          // slow 现在指向后半段起点（偶数=右半起点，奇数=正中间）

          // 2. 反转后半段（三指针法）
          ListNode pre = null;
          while (slow != null) {
              ListNode temp = slow.next;
              slow.next = pre;
              pre = slow;      // pre 是反转后的头（不是 slow！slow 最后是 null）
              slow = temp;
          }

          // 3. 前半段 vs 反转后的后半段
          ListNode left = head, right = pre;
          while (right != null) {
              if (left.val != right.val) return false;
              left = left.next;
              right = right.next;
          }
          return true;
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** ✅（学员独立写出标准解法 + 主动标注 3 个易错点）
- **感悟/易错点：** 快慢指针 while 条件 `fast != null && fast.next != null`（短路防空指针）；slow 停在"后半段起点"；反转结束后头是 pre 不是 slow；奇数链表中间节点和自己比一次不影响结果；学员第一版用"复制+反转+比较"O(n) 空间，边界多造一个空节点（隐藏雷），正解 O(1)

### 4. 环形链表（Easy）
- **核心思路：** 快慢指针判环。slow 走 1 步、fast 走 2 步，有环则 fast 套圈追上 slow（引用 == 相遇）。备选：哈希集合存节点，遇到重复即有环
- **代码实现：**
  ```java
  public class Solution {
      public boolean hasCycle(ListNode head) {
          ListNode slow = head, fast = head;
          while (fast != null && fast.next != null) {   // fast 走2步，先判自身再判 next（短路）
              slow = slow.next;
              fast = fast.next.next;
              if (slow == fast) return true;            // 相遇 = 有环（引用 ==）
          }
          return false;
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** ✅（学员看提醒后独立写出）
- **感悟/易错点：** `fast != null && fast.next != null` 顺序不能反（防空指针）；相遇判定用 `==` 不是 `.val ==`；面试策略"先写哈希 O(n) 再优化 O(1)"更好

### 5. 环形链表 II（Medium）
- **核心思路：** 141 + 找入口。快慢相遇后，一个指针回 head，两个都走 1 步，再相遇处 = 环入口。数学：设 a=头→入口，b=入口→相遇点，c=环长，fast=2×slow → `a = (c-b) + 整圈`，所以"从头走 a"与"从相遇点走 c-b"在入口汇合
- **代码实现：**
  ```java
  public class Solution {
      public ListNode detectCycle(ListNode head) {
          ListNode slow = head, fast = head;
          while (fast != null && fast.next != null) {
              slow = slow.next;
              fast = fast.next.next;
              if (slow == fast) {                 // 有环，找到相遇点
                  ListNode p = head;              // 一个回 head
                  while (p != slow) {             // 都走 1 步
                      p = p.next;
                      slow = slow.next;
                  }
                  return p;                       // 再相遇 = 环入口
              }
          }
          return null;                            // 无环
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** ✅（学员独立写出 + 自己讲解数学推导 a+nb）
- **感悟/易错点：** 学员自己讲清"slow 走了 nb、fast 走了 2nb，再走 a 回到起点"；结论"相遇后一回头就是入口"是套路要背住；哈希法更直白（第一次重复节点=入口）但 O(n) 空间；面试最佳=先哈希再优化双指针

### 6. 合并两个有序链表（Easy）
- **核心思路：** 虚拟头节点 + 双指针取小。dummy 避免特判头节点；每次接小的到 p 后面；一条走完后直接把另一条整条接上
- **代码实现：**
  ```java
  class Solution {
      public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
          ListNode dummy = new ListNode(-1);
          ListNode p = dummy;                 // p 在新链表上游走

          while (list1 != null && list2 != null) {
              if (list1.val < list2.val) {
                  p.next = list1;
                  list1 = list1.next;         // 直接走，不用 temp
              } else {
                  p.next = list2;
                  list2 = list2.next;
              }
              p = p.next;                     // p 前进到新接上的节点
          }

          p.next = (list1 == null) ? list2 : list1;   // 接上剩余整条

          return dummy.next;                  // 跳过虚拟头
      }
  }
  ```
- **复杂度：** O(m+n) / O(1)
- **掌握程度：** ✅（学员 AC，双指针取小 + 虚拟头正确，可再简洁）
- **感悟/易错点：** ⭐ 合并不需要"断链"——学员把反转的习惯（temp 存 + cur.next=null）带进来了。判断口诀：**改自己的 next（`cur.next=xxx`）才要 temp 先存；改别人的 next（`p.next=xxx`）不用管原链表，直接走**（REV-16）

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
