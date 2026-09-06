# 📝 Day 23 · CodeTop Day5（1143✅/82✅默写 + 124🟡 + 93🟡 + 19🟡）

> **日期：** 2026-09-06（周日）
> **范围：** 开场默写 1143/82 + 快速复习 + 124二叉树最大路径和 + 93复原IP地址 + 19删除链表倒数第N个
> **节奏：** 复习日 + 新题推进，124 负贡献取 0 详细讲解，93 回溯+三重循环两版，19 off-by-one 复发

---

## 一、今日完成

| # | 题号 | 题目 | 难度 | 结果 | 备注 |
|---|------|------|------|------|------|
| 默写 | 1143 | 最长公共子序列 | Medium | ✅ | 从 🟡 升 ✅，双串 DP 全对 |
| 默写 | 82 | 删除排序链表重复 II | Medium | ✅ | 从 🟡 升 ✅，单指针 cur 写法理解 |
| 复习 | 42/160 | 快速口头复述 | - | ✅ | 42"承诺"概念、160 不相交同时到 null |
| #34 | 124 | 二叉树最大路径和 | Hard | 🟡 | 负贡献未取 0（REV-30 经典坑）|
| #35 | 93 | 复原 IP 地址 | Medium | 🟡 | 回溯 + 三重循环两版，字符串方法学习 |
| #36 | 19 | 删除链表倒数第 N 个 | Medium | 🟡 | off-by-one 复发（ERR-025），while 条件应为 fast.next != null |

**CodeTop Day 5 进度：** 82✅/124🟡/93🟡/19🟡，剩余 4/142/165/199

---

## 二、逐题笔记

### 0. 开场默写：1143 最长公共子序列 ✅

从 🟡 升 ✅。dp 定义、下标差 1、初始化（数组默认 0）、相等抄左上+1、不等取 max(上,左)、返回 dp[m][n]，全对。

---

### 1. 开场默写：82 删除排序链表重复 II ✅

从 🟡 升 ✅。第一次写犯了 4 个错误，第二次理解了单指针 cur 写法：
- cur 站在"已确定不重复的最后一个节点"
- 重复时 cur 不动，while 改 cur.next 跳过整段
- 不重复时 cur = cur.next

用户疑问"不写 pre.next = p1 也行"——在特定实现中确实能跑通（因为初始连接和重复段处理后的连接保证了 pre.next 总是 p1），但写上更安全清晰。

---

### 2. 快速复习要点

**42 接雨水"承诺"概念：** rightMax 是一个"承诺"——右边至少有 rightMax 这么高的墙。当 leftMax < rightMax 时，对于 left 位置，右边一定有 > leftMax 的墙，所以 min(左边最高, 右边最高) = leftMax。

**160 相交链表不相交时：** 两个指针同时走到 null，pA == pB（都是 null），循环结束，返回 null。

---

### 3. 124 二叉树最大路径和 🟡

**核心思路：** 后序遍历，递归返回"单臂最大路径和"，全局更新"双臂最大路径和"。

**今日错误：负贡献未取 0**
```java
int left = maxGain(node.left);    // ❌ 如果 left 是负数，会拉低路径和
int right = maxGain(node.right);  // ❌ 同理
```
应为：
```java
int left = Math.max(0, maxGain(node.left));   // ✅ 负贡献取 0（不选这个方向）
int right = Math.max(0, maxGain(node.right)); // ✅
```

**例子：**
```
    2
   / \
 -1   3
```
正确答案 5（路径 2→3）。不取 0 时双臂路径和 = -1+3+2 = 4 ❌；取 0 后 = 0+3+2 = 5 ✅。

**正确代码：**
```java
class Solution {
    int max = Integer.MIN_VALUE;
    
    public int maxPathSum(TreeNode root) {
        maxGain(root);
        return max;
    }
    
    int maxGain(TreeNode node) {
        if (node == null) return 0;
        int left = Math.max(0, maxGain(node.left));
        int right = Math.max(0, maxGain(node.right));
        max = Math.max(left + right + node.val, max);  // 双臂更新全局
        return Math.max(left, right) + node.val;        // 单臂返回
    }
}
```

**关键易错点（REV-30）：**
| 易错点 | 说明 |
|--------|------|
| 负贡献取 0 | `Math.max(0, maxGain(child))` |
| max 初始值 | `Integer.MIN_VALUE`，不是 0 |
| 返回单臂 vs 更新双臂 | 返回 `max(left,right)+val`，更新全局 `left+right+val` |
| 后序遍历 | 先算左右再算当前 |

---

### 4. 93 复原 IP 地址 🟡

**核心思路：** 回溯或三重循环，将字符串分成 4 段，每段 1-3 位，满足 IP 规则。

**两种写法：**

#### 写法 1：回溯（通用）
```java
class Solution {
    List<String> res = new ArrayList<>();
    List<String> path = new ArrayList<>();
    
    public List<String> restoreIpAddresses(String s) {
        if (s.length() < 4 || s.length() > 12) return res;
        backtrack(s, 0, 0);
        return res;
    }
    
    void backtrack(String s, int start, int pointNum) {
        if (pointNum == 4) {
            if (start == s.length()) res.add(String.join(".", path));
            return;
        }
        for (int i = start; i < s.length(); i++) {
            if (i - start + 1 > 3) break;
            String segment = s.substring(start, i + 1);
            if (!isValid(segment)) continue;
            path.add(segment);
            backtrack(s, i + 1, pointNum + 1);
            path.remove(path.size() - 1);
        }
    }
    
    boolean isValid(String s) {
        if (s.length() > 1 && s.charAt(0) == '0') return false;
        return Integer.parseInt(s) <= 255;
    }
}
```

#### 写法 2：三重循环（推荐，更直观）
```java
class Solution {
    public List<String> restoreIpAddresses(String s) {
        int n = s.length();
        List<String> ans = new ArrayList<>();
        for (int i = 1; i < n && isValid(s, 0, i); i++) {
            for (int j = i + 1; j < n && isValid(s, i, j); j++) {
                for (int k = j + 1; k < n && isValid(s, j, k); k++) {
                    if (isValid(s, k, n)) {
                        ans.add(String.format("%s.%s.%s.%s",
                            s.substring(0, i),
                            s.substring(i, j),
                            s.substring(j, k),
                            s.substring(k)));
                    }
                }
            }
        }
        return ans;
    }
    
    private boolean isValid(String s, int i, int j) {
        if (j - i > 3 || (j - i > 1 && s.charAt(i) == '0')) return false;
        return Integer.parseInt(s.substring(i, j)) <= 255;
    }
}
```

**关键易错点：**
| 易错点 | 说明 |
|--------|------|
| 终止条件 | pointNum == 4 且 start == s.length() 才收集 |
| 前导 0 | `length > 1 && charAt(0) == '0'` 不合法，单独 "0" 合法 |
| 数值范围 | <= 255 |
| 每段最多 3 位 | `i - start + 1 > 3` break |
| 整体长度剪枝 | length < 4 或 > 12 直接返回 |

---

### 5. 19 删除链表倒数第 N 个 🟡

**核心思路：** 虚拟头 + 快慢指针，fast 先走 n 步，同步走到尾，slow 指向待删节点前驱。

**今日错误：off-by-one（ERR-025 复发）**
```java
while (fast != null) {  // ❌ slow 最后指向待删节点本身，删错了
```
应为：
```java
while (fast.next != null) {  // ✅ fast 到尾节点停止，slow 指向待删节点前驱
```

**例子验证：**
```
head = 1 → 2 → 3 → 4 → 5, n = 2（应该删除 4）

错误写法（while fast != null）：
  fast 先走 2 步：fast = 2
  while: fast=3,slow=1 → fast=4,slow=2 → fast=5,slow=3 → fast=null,slow=4
  slow = 4（待删节点本身），slow.next = slow.next.next 删除了 5 ❌

正确写法（while fast.next != null）：
  fast 先走 2 步：fast = 2
  while: fast=3,slow=1 → fast=4,slow=2 → fast=5,slow=3（fast.next=null 停止）
  slow = 3（待删节点 4 的前驱），slow.next = slow.next.next 删除了 4 ✅
```

**正确代码：**
```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(-1, head);
        ListNode fast = dummy, slow = dummy;
        for (int i = 0; i < n; i++) fast = fast.next;
        while (fast.next != null) {  // ✅ fast 到尾节点停止
            fast = fast.next;
            slow = slow.next;
        }
        slow.next = slow.next.next;
        return dummy.next;
    }
}
```

**核心原理：** slow 要指向待删节点的前驱（倒数第 n+1 个），不是待删节点本身（倒数第 n 个）。fast 和 slow 之间要差 n+1 个位置。

---

## 三、今日学习的 Java 字符串/数字方法（⭐ 巩固）

| 方法 | 作用 | 高频场景 |
|------|------|---------|
| `String.join(sep, list)` | 用分隔符连接集合 | 回溯收集结果 |
| `String.format(fmt, args)` | 格式化拼接 | 固定格式（IP 地址）|
| `s.substring(start, end)` | 取子串（左闭右开）| 字符串分割 |
| `Integer.parseInt(s)` | 字符串转 int | 数字判断（<=255）|
| `String.valueOf(num)` | 数字转字符串 | 类型转换 |

**String.format 常用占位符：** `%s` 字符串、`%d` 整数、`%f` 浮点数、`%c` 字符、`%b` 布尔。

---

## 四、明日复习计划（遗忘曲线 + 易错点）

### 🔴 今天 🟡 题默写（隔 1 天，重点）
1. **124 二叉树最大路径和**（负贡献取 0、max 初始 MIN_VALUE、单臂vs双臂）
2. **93 复原 IP 地址**（三重循环推荐、前导 0、255、4段用完字符）
3. **19 删除链表倒数第 N 个**（while fast.next != null、ERR-025 off-by-one）

### 🟡 今天 ✅ 题快速口头复述
4. **1143 最长公共子序列**（双串 DP 套路）
5. **82 删除排序链表重复 II**（cur 站在安全位置、删全部）

### 🟢 隔 2 天复习（快速过）
6. **42 接雨水**（对撞双指针"承诺"概念）
7. **160 相交链表**（双指针交错走）

### 🔵 易错点专项
8. **124 负贡献取 0**：`Math.max(0, maxGain(child))`
9. **19 off-by-one**：ERR-025 复发，while 条件 fast.next != null
10. **93 前导 0 / 255 / 4段**
11. **字符串方法**：String.join / format / substring / parseInt
12. **程序骨架 Scanner**：每天 1 道 ACM 输入小测

### 📚 新题推进
13. **4 寻找两个正序数组的中位数**（Hard，REV-58 挂起题，Day 5 第 5 题）
14. 视状态继续：142 环形链表 II / 165 比较版本号 / 199 二叉树右视图

---

> **最后更新：** 2026-09-06
