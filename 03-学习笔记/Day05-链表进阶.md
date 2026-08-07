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
- **核心思路：** 分组 + 组内三指针反转 + 组间重连。dummy 虚拟头；每轮先"探测"够不够 K 个（cur 走 k 步，遇到 null 说明不够 → `return dummy.next`）；够就反转这一组（到 nextStart 停）；三步重连：`pre.next=新头` → `groupStart.next=nextStart` → `pre=groupStart`。`while(true)+内部 return` = **"中间出口循环"模式**（出口在循环体中间）
- **代码实现：**
  ```java
  // 学员版（对照思路重建，🔄 待复习）——结构正确，2 处冗余
  class Solution {
      public ListNode reverseKGroup(ListNode head, int k) {
          ListNode dummy = new ListNode(-1);
          ListNode pre = dummy;
          dummy.next = head;
          ListNode cur = head;
          while (true) {
              ListNode pre1 = pre;
              ListNode p = cur;
              for (int i = 0; i < k; i++) {
                  if (cur == null) return dummy.next;   // 不够 k 个 → 结束
                  cur = cur.next;
                  pre1 = pre1.next;
              }
              ListNode groupStart = pre.next;
              // ListNode groupEnd = pre1;   // ❌ 定义了没用，删
              ListNode nextStart = cur;
              ListNode preGroup = null;
              while (p != null && p != nextStart) {     // p != null 可省
                  ListNode temp = p.next;
                  p.next = preGroup;
                  preGroup = p;
                  p = temp;
              }
              pre.next = preGroup;
              groupStart.next = nextStart;
              pre = groupStart;
          }
      }
  }

  // 标准版（更精简）
  class Solution {
      public ListNode reverseKGroup(ListNode head, int k) {
          ListNode dummy = new ListNode(-1);
          dummy.next = head;
          ListNode pre = dummy, cur = head;
          while (true) {
              for (int i = 0; i < k; i++) {       // ① 探测够不够
                  if (cur == null) return dummy.next;
                  cur = cur.next;
              }
              ListNode groupStart = pre.next;     // 组头（反转后变组尾）
              ListNode nextStart = cur;           // 下一组起点
              ListNode newHead = null;            // 反转后的组头
              ListNode p = groupStart;
              while (p != nextStart) {            // ② 三指针反转，到 nextStart 停
                  ListNode temp = p.next;
                  p.next = newHead;
                  newHead = p;
                  p = temp;
              }
              pre.next = newHead;                 // ③ 接新头
              groupStart.next = nextStart;        //    组尾接下一组
              pre = groupStart;                   //    pre 移到组尾（下一组前驱）
          }
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** 🔄（对照思路重建，结构正确；**独立 AC = 真正的毕业考**，待 Day 6 复写）
- **感悟/易错点：** ① `while(true)`：出口在循环体中间（探测不够就 return），是"中间出口循环"模式，不是死循环；② `cur` 跨轮次当 nextStart 用（状态保持）；③ 反转条件 `p != nextStart` 已够用（探测保证够 k 个，p 恰好停在 nextStart）；④ 三步重连顺序：接新头 → 组尾接下一组 → pre 移到组尾；⑤ 写完检查"声明了但没用"的变量（groupEnd）

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
- 分组反转（25）：`while(true)` 中间出口 + 探测够 K 个 + 组内三指针反转 + 三步重连
- 递归两两交换（24）：基准 → 递归处理后面 → 处理自己 → 返回新头（REV-18）
- "中间出口循环"模式：出口在循环体中间（`return`），用 `while(true)` 最清晰

**遇到的困难：**
- 25 第一版写乱（p 未定义、flag 未复位、反转条件错误）→ 拆成"探测/反转/重连"三步后能对照重建
- 学员诚实评估：25 是"对照思路重建"非"独立写出"，标 🔄 待 Day 6 复写

**遗留问题（需复习）：**
- REV-19：25 独立复写（真正的毕业考）
- REV-14/15/16/17 待复习
- Day 5 剩余 4 题：148 排序链表 / 138 随机链表复制 / 146 LRU / 23 合并 K 个升序链表

**整体感受：** 😊
