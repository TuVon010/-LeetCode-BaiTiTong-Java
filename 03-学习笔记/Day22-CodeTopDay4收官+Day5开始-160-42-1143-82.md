# 📝 Day 22 · CodeTop Day4收官 + Day5开始（56✅/72✅默写 + 160✅/42✅/1143🟡/82🟡）

> **日期：** 2026-09-04（周五）
> **范围：** 开场默写 56/72 + 快速复习 + 160相交链表 + 42接雨水 + 1143最长公共子序列 + 82删除排序链表重复II
> **节奏：** 复习日 + 新题推进，42 对撞双指针原理详细讲解，1143 双串 DP 套路总结

---

## 一、今日完成

| # | 题号 | 题目 | 难度 | 结果 | 备注 |
|---|------|------|------|------|------|
| 默写 | 56 | 合并区间 | Medium | ✅ | 从 🟡 升 ✅，昨天 3 个错误全避 |
| 默写 | 72 | 编辑距离 | Hard | ✅ | 从 🟡 升 ✅，三种操作对应关系全对 |
| 复习 | 415/54/143 | 快速口头复述 | - | ✅ | 54 左方向行列索引、143 fast=head.next 慢半步已纠正 |
| #30 | 160 | 相交链表 | Easy | ✅ | 双指针交错走，总路程相等 |
| #31 | 42 | 接雨水 | Hard | ✅ | 从 🟡 升 ✅，对撞双指针"承诺"概念理解透彻 |
| #32 | 1143 | 最长公共子序列 | Medium | 🟡 | 双串 DP，相等+1/不等取max，新题第一次写 |
| #33 | 82 | 删除排序链表中的重复元素 II | Medium | 🟡 | 新题，删全部重复，cur 站在安全位置跳过整段 |

**CodeTop Day 4 全部收官：** 141✅/143✅/56✅/415✅/72✅/160✅/42✅/1143🟡
**CodeTop Day 5 开始：** 82🟡，剩余 124/93/19/4/142/165/199

---

## 二、逐题笔记

### 0. 开场默写：56 合并区间 ✅

昨天 3 个错误今天全避：
- ✅ 终点取 max：`Math.max(last[1], interval[1])`
- ✅ toArray 指定类型：`toArray(new int[res.size()][])`
- ✅ new 新数组：`add(new int[]{interval[0], interval[1]})`

小冗余：`int start = ...` 定义了没用到，不影响正确性。

---

### 1. 开场默写：72 编辑距离 ✅

Hard 题隔了两天能默写全对，三种操作对应关系全对：
- 删除 = `dp[i-1][j] + 1`（word1 少 i）
- 插入 = `dp[i][j-1] + 1`（word2 少 j）
- 替换 = `dp[i-1][j-1] + 1`（两个都少）

小笔误：变量名 `repalce` 拼错（应为 `replace`），不影响运行。

---

### 2. 快速复习要点

**54 左方向纠正：** `for (int i=bottom; i>=top; i--) res.add(matrix[i][left])`，列固定 left，行变 i。

**143 fast 初始化纠正：** `fast = head.next`（慢半步）是故意的，保证奇数个节点时前半多一个，交错合并时后半更短，`while(p2 != null)` 刚好合并完。不是都行。

---

### 3. 160 相交链表 ✅

**核心思路：** 双指针各走一遍"自己的链表 + 对方的链表"，总路程相等，相遇点就是相交点。

**数学公式：**
```
A 长度 = a，B 长度 = b，相交部分 = c
pA 总路程 = a + (b-c) = a + b - c
pB 总路程 = b + (a-c) = a + b - c
两者相等 → 同时到达相交点
```

**不相交时：** c=0，两者同时到 null，`pA == pB`（都是 null），循环结束，返回 null。

**代码：**
```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode pA = headA, pB = headB;
        while (pA != pB) {
            pA = (pA != null ? pA.next : headB);
            pB = (pB != null ? pB.next : headA);
        }
        return pA;
    }
}
```

**易错点：** 判断同一节点用引用 `==`，不是 `.val ==`。

---

### 4. 42 接雨水 ✅（从 🟡 升 ✅）

**核心思路：** 对撞双指针，维护 leftMax/rightMax，移动矮的一边。

**"承诺"概念（核心理解）：**
- `rightMax` 是一个"承诺"：右边至少有 rightMax 这么高的墙
- 当 `leftMax < rightMax` 时，对于 left 位置，右边一定有 > leftMax 的墙，所以 min(左边最高, 右边最高) = leftMax
- left 位置能接的水 = `leftMax - height[left]`，不需要知道右边具体多高
- 移动左指针是安全的，右侧已被 rightMax 这个"承诺"覆盖

**代码：**
```java
class Solution {
    public int trap(int[] height) {
        int left = 0, right = height.length - 1;
        int leftMax = 0, rightMax = 0, res = 0;
        while (left < right) {
            leftMax = Math.max(leftMax, height[left]);
            rightMax = Math.max(rightMax, height[right]);
            if (leftMax < rightMax) {
                res += leftMax - height[left];
                left++;
            } else {
                res += rightMax - height[right];
                right--;
            }
        }
        return res;
    }
}
```

**三种解法对比：**
| 解法 | 时间 | 空间 |
|------|------|------|
| 暴力 | O(n²) | O(1) |
| DP 预处理 | O(n) | O(n) |
| 对撞双指针 | O(n) | O(1) ⭐ |

---

### 5. 1143 最长公共子序列 🟡

**核心思路：** 双串 DP，dp[i][j] = text1 前 i 个和 text2 前 j 个的最长公共子序列长度。

**状态转移：**
- 相等：`dp[i][j] = dp[i-1][j-1] + 1`（匹配上了）
- 不等：`dp[i][j] = max(dp[i-1][j], dp[i][j-1])`（不选 text1 当前 或 不选 text2 当前，取最长）

**初始化：** `dp[i][0] = 0, dp[0][j] = 0`（数组默认值，空串的 LCS 为 0）

**代码：**
```java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int m = text1.length(), n = text2.length();
        int[][] dp = new int[m+1][n+1];
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (text1.charAt(i-1) == text2.charAt(j-1)) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                } else {
                    dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]);
                }
            }
        }
        return dp[m][n];
    }
}
```

**双串 DP 通用套路（⭐ 记住这个，所有双串 DP 都能套）：**
1. 定义 dp[i][j]（两个字符串，前 i 个和前 j 个）
2. 初始化第一行和第一列（空串的情况）
3. 填表，分两种情况：当前字符相等怎么办？不等怎么办（取 min 还是 max）？
4. 返回 dp[m][n]

**72 vs 1143 对比：**
| | 72 编辑距离 | 1143 最长公共子序列 |
|---|---|---|
| 定义 | 最少操作数 | 最长 LCS 长度 |
| 相等 | dp[i-1][j-1]（不用操作）| dp[i-1][j-1]+1（匹配上了）|
| 不等 | 1+min(替换,删除,插入) | max(不选text1,不选text2) |
| 初始化 | dp[i][0]=i, dp[0][j]=j | dp[i][0]=0, dp[0][j]=0 |

**用户困惑：** "感觉像在背题，第二次做又忘"——解决方法：不要背转移方程，要背"状态定义 + 最后一步有几种选择"。状态定义清楚了，最后一步想清楚了，转移方程自然就出来了。多做几道同类型题（72/1143/583/115）形成肌肉记忆。

---

### 6. 82 删除排序链表中的重复元素 II 🟡

**核心思路：** 虚拟头 + cur 站在"已确定不重复的最后一个节点"，遇到重复值整段跳过（一个不留）。

**和 83 的区别：** 83 是"留一个"，82 是"全删掉"。

**代码（单指针 cur 写法）：**
```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode dummy = new ListNode(-1, head);
        ListNode cur = dummy;
        while (cur.next != null && cur.next.next != null) {
            if (cur.next.val == cur.next.next.val) {
                int val = cur.next.val;
                while (cur.next != null && cur.next.val == val) {
                    cur.next = cur.next.next;  // 跳过所有等于 val 的节点
                }
            } else {
                cur = cur.next;  // 不重复，cur 往前走
            }
        }
        return dummy.next;
    }
}
```

**用户第一次写的 4 个错误：**
1. dummy 没挂 head（`new ListNode(-1)` 应为 `new ListNode(-1, head)`）
2. 内层 while 死循环（p1 没移动）
3. 不重复时 pre 连接不对（应先 `pre.next = p1` 再 `pre = p1`）
4. 循环条件漏掉最后一个节点（`p1.next != null` 导致最后一个不重复节点没处理）

**关键易错点：**
| 易错点 | 说明 |
|--------|------|
| 删全部 vs 留一个 | 82 是删全部，83 是留一个 |
| cur 站在哪 | 站在"已确定不重复的最后一个节点" |
| 重复时 cur 动不动 | 重复时 cur 不动，只改 cur.next；不重复时 cur 才往前走 |
| 跳过整段 | 用 while + 记录 val，跳过所有等于 val 的节点 |
| 虚拟头 | 头节点可能被删，必须用 dummy |

---

## 三、明日复习计划（遗忘曲线 + 易错点）

### 🔴 今天 🟡 题默写（隔 1 天，重点）
1. **1143 最长公共子序列**（双串 DP 套路、相等+1/不等取max）
2. **82 删除排序链表中的重复元素 II**（删全部、cur 站在安全位置、跳过整段）

### 🟡 今天 ✅ 题快速口头复述
3. **160 相交链表**（双指针交错走、总路程相等）
4. **42 接雨水**（对撞双指针、"承诺"概念、移动矮边）

### 🟢 隔 2 天复习（快速过）
5. **56 合并区间**（终点取 max、toArray 类型、new 数组）
6. **72 编辑距离**（三种操作对应关系）

### 🔵 易错点专项
7. **82**：删全部 vs 留一个、cur 站在安全位置、重复时 cur 不动
8. **1143**：双串 DP 通用套路、不要背方程要背状态定义
9. **42**：对撞双指针"承诺"概念、移动矮的一边
10. **程序骨架 Scanner**：每天 1 道 ACM 输入小测

### 📚 新题推进
11. **124 二叉树中的最大路径和**（Hard，Day 5 第 2 题，后序+全局 max）
12. 视状态继续：93 复原 IP 地址 / 19 删除链表倒数第 N 个

---

> **最后更新：** 2026-09-04
