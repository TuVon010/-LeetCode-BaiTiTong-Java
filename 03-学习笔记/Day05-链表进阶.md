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
- **核心思路：** 深拷贝 + 对照表。核心问题：random 可能指向后面的节点，遍历时副本还没建好 → 所以分两遍。**第一遍只建节点**（`map.put(旧, new Node(旧.val))`），**第二遍连指针**（`map.get(旧).next = map.get(旧.next)`、`.random = map.get(旧.random)`）。`map.get(null)` 自动处理 random 为 null，不用特判
- **代码实现：**
  ```java
  // HashMap 两遍法（标准）
  class Solution {
      public Node copyRandomList(Node head) {
          if (head == null) return null;
          Map<Node, Node> map = new HashMap<>();   // 对照表：旧 → 新
          Node cur = head;
          while (cur != null) {                    // ① 先办证：只建节点
              map.put(cur, new Node(cur.val));
              cur = cur.next;
          }
          cur = head;
          while (cur != null) {                    // ② 再填表：靠 map 翻译指针
              map.get(cur).next = map.get(cur.next);
              map.get(cur).random = map.get(cur.random);
              cur = cur.next;
          }
          return map.get(head);
      }
  }
  ```
- **复杂度：** O(n) / O(n)（map 存 n 个副本）
- **掌握程度：** 🔄（听懂了，但自觉"第二次不一定能想到"，需复习）
- **感悟/易错点：** ① 两行核心翻译：`map.get(cur).next = map.get(cur.next)`（左边新节点属性，右边查词典）；② 为什么第一遍只建、第二遍才连——random 往前指，副本没建好；口诀"**先办证，再填表**"；③ `map.get(null)` 免费处理 random=null；④ 学员的 List 法思路对但 O(n²)（indexOf 线性扫）——用空间换时间；⑤ `Node` 只有单参构造器，不能直接塞旧指针（否则浅拷贝）

### 4. 排序链表（Medium）
- **核心思路：**

### 5. 合并 K 个升序链表（Hard）
- **核心思路：** 21 合并两个有序链表的"K 条升级版"。三种解法：①顺序两两合并（复用 21，O(K·n)，最稳）②优先队列堆（所有头进小顶堆，poll 取最小 + 把 next 补进，O(n·logK)，最炫）③分治递归（对半拆到 1 条，再两两合，O(n·logK)，不用堆也最优）。堆核心方法：`offer`（加）、`poll`（取最小）、`peek`（看最小）；小顶堆比较器 `(a,b)->a.val-b.val`
- **代码实现：**
  ```java
  // 解法一：顺序两两合并（学员独立写出，AC ✅）
  class Solution {
      public ListNode mergeKLists(ListNode[] lists) {
          if (lists.length == 0) return null;
          ListNode res = lists[0];                  // 独立累积变量（比原地覆盖 lists[i] 更清晰）
          for (int i = 1; i < lists.length; i++) {
              res = twoCompare(res, lists[i]);      // 一条条吞进累积结果
          }
          return res;
      }
      public ListNode twoCompare(ListNode l1, ListNode l2) {  // 复用 21 题
          ListNode dummy = new ListNode(-1);
          ListNode p = dummy;
          while (l1 != null && l2 != null) {
              if (l1.val < l2.val) { p.next = l1; l1 = l1.next; }
              else { p.next = l2; l2 = l2.next; }
              p = p.next;
          }
          p.next = (l1 == null) ? l2 : l1;
          return dummy.next;
      }
  }

  // 解法二：优先队列（堆）
  // PriorityQueue<ListNode> heap = new PriorityQueue<>((a,b)->a.val-b.val);
  // for (ListNode h : lists) if (h != null) heap.offer(h);
  // ListNode dummy = new ListNode(-1), p = dummy;
  // while (!heap.isEmpty()) {
  //     ListNode cur = heap.poll();                    // 先取最小
  //     if (cur.next != null) heap.offer(cur.next);    // 再补它的下一个
  //     p.next = cur; p = p.next;
  // }
  // return dummy.next;

  // 解法三：分治递归（归并排序套路，不用堆也 O(n·logK)）
  // merge(lists, 0, lists.length-1)
  //   基准：lo == hi → 返回 lists[lo]
  //   mid = (lo+hi)/2; left=merge(lo,mid); right=merge(mid+1,hi);
  //   return twoCompare(left, right);
  ```
- **复杂度：** ① O(K·n) ② O(n·logK) ③ O(n·logK) / O(1)（堆法 O(K)）
- **掌握程度：** ✅（解法一独立 AC；解法二堆已理解；解法三递归分治待 Day 6 二叉树后回炉）
- **感悟/易错点：** ① 堆=自动排序的贩卖机，只记 `offer/poll/peek` 三词；"一堆动态元素反复取最小"→堆，不是单调队列（滑动窗口顺序进出才用队列）；② 堆法顺序"先 poll 取最小，再 offer 它的 next"；`p.next=cur` 与 `offer next` 两行无依赖可换序；③ 递归分治=REV-18 骨架（基准 lo==hi → 递归左右 → 合并自己）；信任递归，不用追踪每一步；④ 顺序两两合并用独立累积变量 res 比原地覆盖 lists[i] 更清晰

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
- 深拷贝 + 对照表（138）：先办证（只建节点）→ 再填表（map 翻译指针），`map.get(null)` 处理 null
- 堆（PriorityQueue，23）：offer/poll/peek，"自动排序的贩卖机"，反复取最小；小顶堆比较器
- 分治递归（23）：对半拆到 1 条再两两合并，REV-18 骨架复用

**遇到的困难：**
- 25 第一版写乱（p 未定义、flag 未复位、反转条件错误）→ 拆成"探测/反转/重连"三步后能对照重建
- 138 自觉"第二次不一定能想到"→ 深拷贝是对照表套路，靠 REV-20 复习，不强求一遍记住
- 23 堆方法没学过 → 用"自动排序贩卖机"类比讲透 offer/poll/peek；区分堆 vs 单调队列
- 23 递归分治没学过 → 讲 REV-18 骨架复用 + "信任递归"；明确可不强求，Day 6 二叉树再回炉
- 学员诚实评估：25/138 是"看懂/重建"非"独立写出"，标 🔄；23 解法一独立 AC 标 ✅

**遗留问题（需复习）：**
- REV-19（25）、REV-20（138 对照表）、REV-21（堆 offer/poll/peek）待 Day 6 复习
- REV-14/15/16/17 待复习
- 23 递归分治版待 Day 6 二叉树后回炉
- Day 5 剩余 2 题：**148 排序链表（下次先做，和 23 共用归并思路）** / 146 LRU

**整体感受：** 😊（今天 4 题都学到新套路，状态好，23 独立 AC）
