# Day25 - CodeTop Day5（124✅/19✅默写 + 4中位数挂起 + 165比较版本号）

> **日期：** 2026-09-08
> **学习时长：** 约 2 小时
> **CodeTop Day5 进度：** 82✅/124✅/93🟡/19✅/165✅，剩余 4（Hard挂起）/142/199

---

## 一、开场默写（3 题）

### ✅ 124 二叉树最大路径和（从 🟡 升 ✅）

**默写前口述：** 负贡献取 0 的原因——全负数例子（根-3/左-4/右-5），不取 0 时左右都是负数加起来更小，取 0 后只选根节点本身，最大路径和就是 -3。

**代码：**
```java
class Solution {
    int max = Integer.MIN_VALUE;
    public int maxPathSum(TreeNode root) {
        maxGain(root);
        return max;
    }
    int maxGain(TreeNode node) {
        if (node == null) return 0;
        int left = Math.max(maxGain(node.left), 0);   // ✅ 负贡献取 0
        int right = Math.max(maxGain(node.right), 0); // ✅ 负贡献取 0
        max = Math.max(left + right + node.val, max);
        return Math.max(left + node.val, right + node.val);
    }
}
```

**结果：** 连续犯两次后第三次写对，从 🟡 升 ✅。

---

### 🟡 93 复原 IP 地址（继续 🟡）

**默写前口述：** 三重循环枚举三个分割点 i/j/k，每段合法条件（长度 1-3、无前导 0、<=255）。j 从 i+1 开始的原因（每段至少 1 字符）。前导 0 判断（长度 >1 且首字符是 '0'）。

**代码问题：**
```java
return Integer.parseInt(substring(s, i, j)) <= 255;  // ❌ 写错了
return Integer.parseInt(s.substring(i, j)) <= 255;     // ✅ 正确
```

**错误原因：** `substring` 是 String 对象的方法，应该用 `s.substring(i, j)` 调用，不是 `substring(s, i, j)` 静态调用。

**记忆口诀：**
- `s.substring(i, j)` —— "谁的子串？s 的子串"，s 在前点调用
- `Integer.parseInt(str)` —— "谁来解析？Integer 类来解析"，类名调用静态方法

**结果：** 思路全对，只有方法调用方式错误，继续 🟡，明天再默写。

---

### ✅ 19 删除链表的倒数第 N 个节点（从 🟡 升 ✅）

**代码：**
```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(-1, head);
        ListNode fast = dummy, slow = dummy;
        for (int i = 0; i < n; i++) fast = fast.next;
        while (fast.next != null) {  // ✅ off-by-one 避开了
            fast = fast.next;
            slow = slow.next;
        }
        slow.next = slow.next.next;
        return dummy.next;
    }
}
```

**结果：** ERR-025 off-by-one 这次避开了（while 条件用 `fast.next != null`），从 🟡 升 ✅。

---

## 二、快速复习

### 42 接雨水（口述正确 ✅）

**问题：** 当 `leftMax < rightMax` 时，为什么可以直接算 left 位置的水量并移动左指针？

**回答：** 左边都遍历过，左边最大值能确定；右边只要看到有一个高度大于 leftMax 的，短板一定是 leftMax，所以可以确定当前 left 的接水量并移动左指针。右侧同理。

---

## 三、新题学习

### 🟡 4 寻找两个正序数组的中位数（Hard，挂起）

**题目类型：** 二分查找 / 数组分割
**核心要求：** O(log(m+n)) 时间复杂度
**历史记录：** Hot100 最后 1 题，REV-58 挂起题

**两种解法：**

#### 解法一：切分线解法（二分查找）
- 核心：在较短数组上二分分割点 i，nums2 的分割点 j = (m+n+1)/2 - i
- 四边界：aLeft/aRight/bLeft/bRight，越界用 ±∞ 代替
- 切割合法条件：aLeft <= bRight && bLeft <= aRight
- 不合法调整：aLeft > bRight → i 太大 → right = i-1；否则 i 太小 → left = i+1
- 中位数：奇数取左边最大值，偶数取 (左大+右小)/2

#### 解法二：递归找第 K 小
- 核心：用两个 K 统一奇偶（leftK=(m+n+1)/2, rightK=(m+n+2)/2）
- findKth 函数：在两个数组剩余部分找第 k 小
- 每次取 k/2 个比较，小的那组全部淘汰，递归找第 k-k/2 小
- 边界：一个数组耗尽直接从另一个取；k=1 取较小值

**学习状态：** 用户表示"还是不明白，后面再讲"，标记 🟡 挂起，分 3 次消化（第 1 次理解核心思想，第 2 次默写递归版，第 3 次理解切分线版）。

---

### ✅ 165 比较版本号（新学）

**题目类型：** 字符串 / 双指针
**核心要求：** 比较两个版本号，> 返回 1，< 返回 -1，相等返回 0
**关键细节：** 修订号可能有前导 0；版本号长度可能不同，短的后面补 0

**推荐写法（宫水三叶：split + parseInt）：**
```java
class Solution {
    public int compareVersion(String v1, String v2) {
        String[] ss1 = v1.split("\\."), ss2 = v2.split("\\.");
        int n = ss1.length, m = ss2.length;
        int i = 0, j = 0;
        while (i < n || j < m) {
            int a = 0, b = 0;
            if (i < n) a = Integer.parseInt(ss1[i++]);
            if (j < m) b = Integer.parseInt(ss2[j++]);
            if (a != b) return a > b ? 1 : -1;
        }
        return 0;
    }
}
```

**关键点：**
1. `split("\\.")` —— `.` 在正则里是特殊字符，必须转义 `\\.`
2. `Integer.parseInt` —— 自动忽略前导 0（`"001"` → 1）
3. 短版本补 0 —— 指针到末尾时 if 不执行，a/b 保持初始值 0
4. `i++` 后置自增 —— 先取值再自增

**另一种写法（双指针逐位累加）：**
- 边遍历边解析 `num = num*10 + (char-'0')`，自动忽略前导 0
- 代码较长，但空间 O(1)

---

## 四、Java 字符串方法整理（用户不熟悉，后面复习带）

| 方法 | 作用 | 例子 |
|------|------|------|
| `s.split("\\.")` | 按正则分割成数组 | `"1.0".split("\\.")` → `["1","0"]` |
| `s.substring(i, j)` | 取子串 `[i,j)` | `"abcde".substring(1,3)` → `"bc"` |
| `s.charAt(i)` | 取第 i 个字符 | `"abc".charAt(1)` → `'b'` |
| `Integer.parseInt(s)` | 字符串转 int（自动忽略前导 0） | `Integer.parseInt("001")` → `1` |
| `String.valueOf(n)` | int 转字符串 | `String.valueOf(123)` → `"123"` |
| `String.join(".", arr)` | 用分隔符拼接数组 | `String.join(".", ["1","0","0"])` → `"1.0.0"` |
| `String.format("%s.%s", a, b)` | 格式化字符串 | `String.format("%s.%s", "1", "0")` → `"1.0"` |
| `s.toCharArray()` | 字符串转 char 数组 | `"abc".toCharArray()` → `['a','b','c']` |
| `new StringBuilder().append(x).reverse().toString()` | 拼接+反转+转字符串 | 大数加法用 |

---

## 五、今日总结

| 项 | 内容 | 结果 |
|----|------|------|
| 124 默写 | 负贡献取 0 第三次写对 | ✅ 升掌握 |
| 93 默写 | 思路全对，substring 调用方式错误 | 🟡 继续 |
| 19 默写 | off-by-one 避开了 | ✅ 升掌握 |
| 42 口述 | "承诺"概念理解正确 | ✅ |
| 4 中位数 | Hard，理解思路，未掌握 | 🟡 挂起 |
| 165 比较版本号 | split + parseInt 写法 | ✅ 新学 |

**CodeTop Day5 进度：** 82✅/124✅/93🟡/19✅/165✅，剩余 4（Hard挂起）/142/199

---

## 六、明日计划

### 🔴 默写
1. **93 复原 IP 地址**（重点记 `s.substring` 和 `Integer.parseInt` 调用方式）
2. **165 比较版本号**（split + parseInt 写法）

### 🟡 快速口述
3. 124/19/42/82/1143 快速口述思路

### 🔵 易错点专项
4. 93 substring 调用方式 / 165 split 转义 / 字符串方法整理 / Scanner

### 📚 新题
5. **142 环形链表 II**（快慢指针找环入口）
6. **199 二叉树右视图**（层序遍历取每层最后一个）
7. 4 中位数视状态再讲一次

---

## 七、AI 建议

- 今天开场 3 题默写 2 题升 ✅，效果很好，说明"口述原理→默写"的流程有效
- 93 还有方法调用方式的错误，明天默写前先口述"s.substring 是对象调用，Integer.parseInt 是类调用"，再写代码
- 4 中位数是 Hard，不用急，分多次消化，今天理解了"每次排除一半"的核心思想就够了
- 165 比较版本号相对简单，split + parseInt 写法很简洁，明天应该能默写
- 字符串方法整理了 9 个高频方法，明天快速过一遍，多写几次就记住了
- CodeTop Day5 只剩 3 题（4挂起/142/199），明天推进 142 和 199，Day5 即将收官
