# 📝 Day 20 · CodeTop Day3收官 + Day4开始（54/23/141/143 + 开场4题复习）

> **日期：** 2026-09-01（周二）
> **范围：** 开场复习 92/103/236/300 + 54螺旋矩阵 + 23合并K个 + 141环形链表 + 143重排链表
> **节奏：** 复习日 + 新题推进，用户表达"新题没头绪"的挫败感，给予方法论指导

---

## 一、今日完成

| # | 题号 | 题目 | 难度 | 结果 | 备注 |
|---|------|------|------|------|------|
| 复习 | 92/103/236/300 | 口头复述 | - | ✅ | 236混淆递归返回值vs树结构、300 dp定义少了"以nums[i]结尾"，已纠正 |
| #23 | 54 | 螺旋矩阵 | Medium | 🟡 | 暂时掌握，犯了多个错误（函数名拼写/行列索引反/命名反/漏收缩边界）|
| #24 | 23 | 合并K个升序链表 | Hard | ✅ | 小顶堆解法全对，保持✅；补充分治两两合并解法 |
| #25 | 141 | 环形链表 | Easy | ✅ | 快慢指针全对，保持✅ |
| #26 | 143 | 重排链表 | Medium | 🟡 | 新题，三步走思路（找中点+反转后半+交错合并）|

**CodeTop Day 3 全部收官！**（92/300/103/236/23/54 六题齐）
**CodeTop Day 4 开始：** 141✅/143🟡，56 移到明天

---

## 二、逐题笔记

### 0. 开场复习：92/103/236/300 口头复述

**92 反转链表 II**：四步框架（虚拟头→定位前驱left-1步→头插法反转→三步重连），全对 ✅

**103 锯齿层序遍历**：LinkedList头插法（偶数层addLast/奇数层addFirst），出队后孩子入队不能漏，全对 ✅

**236 最近公共祖先**：
- 用户回答"返回的是树结构"→ 纠正：判断的是递归返回值 `left`/`right`（找没找到），不是树结构 `root.left`/`root.right`（有没有孩子）
- 后序递归：left/right都非空→return root；左空→return right；右空→return left

**300 最长递增子序列**：
- 用户定义"到当前位置的最长递增子序列长度"→ 纠正：必须是"**以 nums[i] 结尾**的最长递增子序列长度"
- 为什么只检查 nums[j]<nums[i]：dp[j] 已保证以 nums[j] 结尾的子序列递增，只需最后一步 nums[j]<nums[i]，接上去整个还是递增

---

### 1. 54 螺旋矩阵 🟡

**标准写法（普通while + 方向内判断）：**
```java
int top=0, bottom=matrix.length-1, left=0, right=matrix[0].length-1;
while (top <= bottom && left <= right) {
    for (int i=left; i<=right; i++) res.add(matrix[top][i]);  // 上
    top++;
    for (int i=top; i<=bottom; i++) res.add(matrix[i][right]); // 右
    right--;
    if (top <= bottom) {  // 下（需判断）
        for (int i=right; i>=left; i--) res.add(matrix[bottom][i]);
        bottom--;
    }
    if (left <= right) {  // 左（需判断）
        for (int i=bottom; i>=top; i--) res.add(matrix[i][left]);
        left++;
    }
}
```

**K神写法（while(true) + 每方向后判断）：**
```java
while (true) {
    for (int i=l; i<=r; i++) res.add(matrix[t][i]);
    if (++t > b) break;
    for (int i=t; i<=b; i++) res.add(matrix[i][r]);
    if (l > --r) break;
    for (int i=r; i>=l; i--) res.add(matrix[b][i]);
    if (t > --b) break;
    for (int i=b; i>=t; i--) res.add(matrix[i][l]);
    if (++l > r) break;
}
```

**今日犯的错误（明天重点复习）：**
| 错误 | 类型 | 纠正 |
|------|------|------|
| 函数名 `spiralOrde1r` 拼错（数字1代替字母r）| 低级错误 | 写完扫一眼函数名 |
| `int[]` + `new ArrayList(res)` 编译错误 | 类型不匹配 | 用 `Integer[]`+`Arrays.asList()` 或直接 `ArrayList<Integer>` |
| 左方向 `matrix[left][i]` 行列索引反 | 概念错误 | 纵向遍历列固定行变：`matrix[i][left]` |
| `top/bottom` 命名搞反 | 可读性 | 标准：`top=0, bottom=length-1` |
| 第一次写漏了收缩边界 | 思路不全 | 四方向后必须收缩边界 |

**行列索引速记：** 横向（上/下）行固定列变 `matrix[行][i]`；纵向（左/右）列固定行变 `matrix[i][列]`

---

### 2. 23 合并 K 个升序链表 ✅

**小顶堆解法（面试首选）：**
```java
PriorityQueue<ListNode> heap = new PriorityQueue<>((a,b)->(a.val-b.val));
ListNode dummy = new ListNode(-1);
for (ListNode h : lists) if (h != null) heap.offer(h);
ListNode cur = dummy;
while (!heap.isEmpty()) {
    ListNode take = heap.poll();
    if (take.next != null) heap.offer(take.next);
    cur.next = take;
    cur = cur.next;
}
return dummy.next;
```

**分治两两合并（备选）：**
- 类似归并排序，把 K 个链表两两配对合并
- 时间 O(kn log k)，空间 O(log k) 递归栈
- 复用 21 题的 mergeTwo 方法

**顺序两两合并（不推荐）：** 依次合并 merge(merge(l1,l2),l3)...，时间 O(k²n)

**用户易错点（后面复习带）：**
- `PriorityQueue<ListNode>` 定义 + 比较器 `(a,b)->(a.val-b.val)`（小顶堆）
- 大顶堆是 `(a,b)->(b.val-a.val)`
- offer 入堆 / poll 出堆（最小元素）/ peek 看堆顶

---

### 3. 141 环形链表 ✅

```java
ListNode slow = head, fast = head;
while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
    if (slow == fast) return true;
}
return false;
```

- while 条件 `fast!=null && fast.next!=null`（防止 fast.next.next 空指针）
- 有环：fast 在环里追上 slow，相遇返回 true
- 无环：fast 先到尾部，循环结束返回 false

---

### 4. 143 重排链表 🟡

**三步走：**
1. **找中点**：快慢指针（fast=head.next 慢半步），切断前后两半
2. **反转后半部分**：头插法或三指针
3. **交错合并**：前后两半各取一个交替连接

```java
// 第一步：找中点
ListNode slow = head, fast = head.next;
while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
}
ListNode second = slow.next;
slow.next = null;  // 切断

// 第二步：反转后半
ListNode reversedSecond = null;
while (second != null) {
    ListNode next = second.next;
    second.next = reversedSecond;
    reversedSecond = second;
    second = next;
}

// 第三步：交错合并
ListNode first = head;
while (reversedSecond != null) {
    ListNode firstNext = first.next;
    ListNode secondNext = reversedSecond.next;
    first.next = reversedSecond;
    reversedSecond.next = firstNext;
    first = firstNext;
    reversedSecond = secondNext;
}
```

**易错点：**
- 找中点 fast 初始 `head.next`（慢半步），不是 `head`
- 别忘了 `slow.next = null` 切断
- 交错合并时 `firstNext` 和 `secondNext` 都要先保存

---

## 三、用户困惑与方法论指导

**用户表达：** "为什么我每次遇到新题就做不来没有头绪啊"

**原因分析：** 正处在从"记忆题解"到"迁移能力"的瓶颈期。新题 = 旧套路的重新组合，缺的是"题型识别"的意识。

**3 个可操作方法：**
1. **做题前 2 分钟题型识别**：数据结构 → 经典操作 → 组合思路，口述后再写代码
2. **从暴力解法出发再优化**：先想最暴力的，问"哪里慢了？能不能优化？"
3. **画具体例子找规律**：手动模拟 1-2 个例子，比盯着想 10 分钟有用

**核心信念：** 这不是能力问题，是方法问题。题量基础已够，需要把零散题解整合成系统套路。

---

## 四、明日复习计划（遗忘曲线 + 易错点）

### 🔴 今天的 🟡 题默写（隔 1 天）
1. **54 螺旋矩阵**（标准写法，重点：行列索引、函数名、边界判断）
2. **143 重排链表**（三步走，重点：找中点fast初始值、切断、交错合并保存next）

### 🟡 今天的 ✅ 题快速口头复述（隔 1 天）
3. **23 合并 K 个**：小顶堆 + 分治两种思路口述，比较器写法
4. **141 环形链表**：快慢指针口述，while 条件

### 🟢 隔 2 天复习（8-31 默写的 4 题，快速过）
5. **92/103/236/300**：每题 1 句话口述核心思路，不写代码

### 🔵 易错点专项复习
6. **ERR-025 off-by-one**：边界 `>=` vs `>`（79→88 同族复发）
7. **54 易错点**：行列索引速记、函数名拼写、Integer[] vs int[]
8. **PriorityQueue 比较器**：小顶堆 `a.val-b.val`，大顶堆 `b.val-a.val`
9. **程序骨架**：main 签名、Scanner 输入（用户之前的短板）

### 📚 新题推进
10. **56 合并区间**（昨天移到今天的，Medium）
11. 视状态继续 CodeTop Day 4：415/72/160/42/1143

---

> **最后更新：** 2026-09-01
