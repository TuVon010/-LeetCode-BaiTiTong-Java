# Day 13 · 进阶动态规划

> **日期：** 2026-08-20
> **学习目标：** 进阶 DP 与多维动态规划
> **相关知识页：** [[02-Wiki/专题总结/10-动态规划]] · [[02-Wiki/专题总结/14-动态规划解题方法论与背包变式大全]] · [[02-Wiki/专题总结/12-技巧专题]]

---

## 一、今日模板回顾

### 子序列 DP
```java
// dp[i] = max(dp[j] + 1)  for j < i if nums[j] < nums[i]
```

### 区间 DP
```java
// dp[i][j] = s[i]==s[j] and dp[i+1][j-1]
// 从短到长枚举区间长度
```

### 路径 DP
```java
// dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1])
```

### 编辑距离
```java
// 三种操作：插入、删除、替换
// dp[i][j] = min(dp[i-1][j] + 1, dp[i][j-1] + 1, dp[i-1][j-1] + cost)
```

---

## 二、做题记录

### 1. 最长递增子序列（Medium）
- **核心思路：** `dp[i]` = **以 nums[i] 结尾**的最长递增子序列长度；从前面所有 `nums[j] < nums[i]` 的 j 里挑 `dp[j]+1` 最大的
- **代码实现：**
  ```java
  class Solution {
      public int lengthOfLIS(int[] nums) {
          int[] dp = new int[nums.length];
          Arrays.fill(dp, 1);            // 光自己也算长度1
          int maxRes = 1;
          for (int i = 0; i < nums.length; i++) {
              for (int j = 0; j < i; j++) {
                  if (nums[j] < nums[i]) {
                      dp[i] = Math.max(dp[i], dp[j] + 1);
                  }
              }
              maxRes = Math.max(maxRes, dp[i]);
          }
          return maxRes;
      }
  }
  ```
- **复杂度：** O(n²) / O(n)
- **掌握程度：** 🟡 提示下完成（dp 含义 + 枚举 j 方向由 AI 给）
- **感悟/易错点：** ⭐ "以 i 结尾"是子序列 DP 总开关；接棒人可以是**任意**前面的 j（不是只看 i-1！）；登记 REV-70；有 O(n log n) 贪心+二分优化版

### 2. 乘积最大子数组（Medium）
- **核心思路：** 负数会翻盘 → 同时维护**当前最大 curMax 和当前最小 curMin**，两者互相喂数据；三种来源：max×cur / min×cur / 重新开始
- **代码实现：**
  ```java
  class Solution {
      public int maxProduct(int[] nums) {
          int maxRes = nums[0], curMax = nums[0], curMin = nums[0];
          for (int i = 1; i < nums.length; i++) {
              int tempMax = curMax, tempMin = curMin;   // ⭐ 存旧值，防覆盖
              curMax = Math.max(Math.max(tempMax * nums[i], tempMin * nums[i]), nums[i]);
              curMin = Math.min(Math.min(tempMax * nums[i], tempMin * nums[i]), nums[i]);
              maxRes = Math.max(curMax, maxRes);
          }
          return maxRes;
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** 🟡 看题解 + AI 思路后写
- **感悟/易错点：** ⭐ 双状态型（骨架 D）——只记一个量不够；必须 temp 存旧值否则 curMin 计算时 curMax 已被覆盖；登记 REV-71

### 3. 分割等和子集（Medium）
- **核心思路：** 转化！两子集和相等 → target = sum/2，问题变成"能不能从数组挑数凑出 target" → **0-1 背包**（每个数一次）
- **代码实现：**
  ```java
  class Solution {
      public boolean canPartition(int[] nums) {
          int sum = 0;
          for (int num : nums) sum += num;
          if (sum % 2 == 1) return false;        // 奇数不可能分两个相等整数
          int target = sum / 2;
          boolean[] dp = new boolean[target + 1];
          dp[0] = true;                          // ⭐ 地基！空集凑出0
          for (int num : nums) {
              for (int j = target; j >= num; j--) {   // ⭐ 倒序=0-1背包（防一数多吃）
                  dp[j] = dp[j] || dp[j - num];       // 不选 || 选
              }
          }
          return dp[target];
      }
  }
  ```
- **复杂度：** O(n × target) / O(target)
- **掌握程度：** 🟡 转移方程由 AI 带填空完成（学员填出核心 `dp[j]||dp[j-num]`）
- **感悟/易错点：** ⭐ 0-1 背包内层**倒序**（正序=完全背包=一数多吃）；`dp[0]=true` 漏了全盘皆输；登记 REV-72

### 4. 最长有效括号（Hard）
- **核心思路：** 用栈存**下标**；三种解法：①哨兵栈（栈底压 -1，算 i-栈顶）②标记法（匹配的括号标 T，找最长连续 T 段，学员实现）③DP
- **代码实现：**（学员写的标记法）
  ```java
  class Solution {
      public int longestValidParentheses(String s) {
          boolean[] marks = new boolean[s.length()];   // 匹配上的括号标 true
          Deque<Integer> stack = new ArrayDeque<>();
          for (int i = 0; i < s.length(); i++) {
              if (s.charAt(i) == '(') {
                  stack.push(i);                        // 存下标
              }
              if (!stack.isEmpty() && s.charAt(i) == ')') {
                  if (s.charAt(stack.peek()) == '(') {  // 栈顶配对
                      marks[stack.pop()] = true;
                      marks[i] = true;
                  }
              }
          }
          int maxLen = 0, curLen = 0;
          for (int i = 0; i < marks.length; i++) {
              if (marks[i]) { curLen++; maxLen = Math.max(maxLen, curLen); }
              else curLen = 0;                          // 没配对的位置切断连续段
          }
          return maxLen;
      }
  }
  ```
- **复杂度：** O(n) / O(n)
- **掌握程度：** 🔄 看题解（哨兵版）后，自己写出标记版
- **感悟/易错点：** ⭐ 栈存下标（算长度要位置）；哨兵 -1 让从 0 开始能算长度；标记法"未配对处天然切断"很妙；登记 REV-73

### 5. 不同路径（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 6. 最小路径和（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 7. 最长回文子串（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 8. 最长公共子序列（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 9. 编辑距离（Hard）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

---

## 三、今日总结

**学到的新模板/技巧：**
- ⭐ 新增专题文档 [[02-Wiki/专题总结/14-动态规划解题方法论与背包变式大全]]：DP 总纲 + 背包分类三问 + 一维/二维骨架 + 背包变式对照表
- ⭐ 判断是不是背包：**一堆东西 + 目标值 + 能不能/最少/方案数** 三件套
- ⭐ 0-1 背包（416）内层**倒序** vs 完全背包（322/279）内层**正序**——"能几次定倒正"
- ⭐ 一维 DP 骨架：以 i 结尾（300）、双状态 max/min（152）
- ⭐ "以 i 结尾"型：接棒人可以是任意前面 j（不是只看 i-1）

**遇到的困难：**
- 300：一开始想成"只看上一位置"→ 反例 [2,5,3,7] 打碎（3 可跟在 2 后）
- 416：漏 `dp[0]=true` → 全盘皆输（转移方程地基没打）；内层方向误写成正序 → "一数多吃"
- 32：Hard，直接看题解，用标记法写出来

**遗留问题（需复习）：**
- 🔴 REV-70~73 待默写（300/152/416/32）
- 🔴 Day 13 剩余 5 题：62 不同路径 / 64 最小路径和 / 5 最长回文子串 / 1143 最长公共子序列 / 72 编辑距离（下一站）
- 🔴 REV-60~69 等旧题待默写（按 v1.5 每天最多 3 道）
- ⏸️ REV-58 4 中位数挂起待回炉

**整体感受：** 😊（第一道二维 DP 还没做，二维骨架待明天实战）
