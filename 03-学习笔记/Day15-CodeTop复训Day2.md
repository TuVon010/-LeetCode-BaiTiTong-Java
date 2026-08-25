# Day 15 · CodeTop 高频复训第 1 轮 Day 2（上半场）

> **日期：** 2026-08-25
> **学习目标：** CodeTop Day 2 前半 + 开场召回
> **相关知识页：** [[02-Wiki/专题总结/09-动态规划]] · [[02-Wiki/题目详解]]

---

## 一、开场召回（6 题 · 4/6 通过）

| 题号 | 内容 | 结果 | 补充点 |
|------|------|------|--------|
| Q1 | Floyd 环入口=重复数 | ✅ | 重复数被两个下标指向 → 环入口 |
| Q2 | 滑动窗口口诀 + 先加后收 bug | 🟡 | 口诀补记："先收后加再计数，窗口合法才更新" |
| Q3 | sum=num vs sum+=num（Kadane） | ✅ | 重开 vs 续杯，理解到位 |
| Q4 | 136 异或原理 | ✅ | a^a=0，成对抵消 |
| Q5 | 75 遇 2 不前进 | 🟡 | 标准反例 [1,2,0]；换过来的未看过必须重查 |
| Q6 | 31 四步法 + >= 原因 | ✅ | >= 原因：找严格升序对，相等不能构成"可增大"位置 |

---

## 二、做题记录

### 1. 最长回文子串（Medium · 5）· REV-77

**掌握程度：🟡（中心扩展法独立写出，偶数初始化 bug 提示后修正；区间 DP 未完成）**

#### 中心扩展法（已完成 ✅）

**核心思路：** 回文 = 中心对称，从每个中心向两边扩展，取最长。

**两种中心：**
- 奇数回文：中心是单个字符 `left=i, right=i`
- 偶数回文：中心是两字符间隙 `left=i, right=i+1` ⭐（易错点）

**代码实现：**
```java
class Solution {
    public String longestPalindrome(String S) {
        if (S.length() == 0) return "";
        char[] s = S.toCharArray();
        int maxLen = 0, ansLeft = 0, ansRight = 0;
        
        // 奇数回文
        for (int i = 0; i < s.length; i++) {
            int left = i, right = i;
            while (left >= 0 && right < s.length) {
                if (s[left] != s[right]) break;
                if (right - left + 1 > maxLen) {
                    maxLen = right - left + 1;
                    ansLeft = left;
                    ansRight = right;
                }
                left--; right++;
            }
        }
        
        // 偶数回文
        for (int i = 0; i < s.length; i++) {
            int left = i, right = i + 1;  // ⭐ 相邻两字符，不是 i-1 和 i+1
            while (left >= 0 && right < s.length) {
                if (s[left] != s[right]) break;
                if (right - left + 1 > maxLen) {
                    maxLen = right - left + 1;
                    ansLeft = left;
                    ansRight = right;
                }
                left--; right++;
            }
        }
        return S.substring(ansLeft, ansRight + 1);
    }
}
```

**易错点（本次犯）：**
- ❌ 偶数回文初始写成 `left=i-1, right=i+1` → 跳过中心相邻字符，"aa"/"abba" 检测不到
- ✅ 正确：`left=i, right=i+1`，先比较相邻的两个字符

**复杂度：** 时间 O(n²)，空间 O(1)

#### 区间 DP 法（待完成 ⏳）

**核心思路：** `dp[i][j]` = s[i..j] 是否为回文
- 转移：`s[i]==s[j] && dp[i+1][j-1]`
- base：`dp[i][i]=true`（长度1），`dp[i][i+1] = s[i]==s[i+1]`（长度2）
- 填表顺序：按区间长度从小到大（len=1,2,3...），因为长区间依赖短区间

> 明日继续：区间 DP 版默写 + 剩余 7 题

---

## 三、今日总结

- 开场召回 4/6，Floyd/Kadane/异或/31 四步法扎实
- 5 题中心扩展法核心思路独立，偶数中心初始化是细节 bug，已修正
- 区间 DP 思路已引导（短区间推长区间），明日默写
- 今日用时较短（学员主动收工），明日火力全开

---

> **最后更新：** 2026-08-25
