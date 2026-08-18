# Day 12 · 动态规划入门与贪心算法

> **日期：** 2026-08-18
> **学习目标：** 掌握 DP 基础模式与贪心算法
> **相关知识页：** [[02-Wiki/专题总结/10-动态规划]] · [[02-Wiki/专题总结/11-贪心算法]]

---

## 一、今日模板回顾

### DP 解题五步法
```
1. dp 数组含义
2. 状态转移方程
3. 初始化
4. 遍历顺序
5. 手动验证
```

### 背包问题框架
```java
// 0-1 背包：容量倒序遍历
for (int j = capacity; j >= w; j--) {
    dp[j] = Math.max(dp[j], dp[j - w] + v);
}

// 完全背包：容量正序遍历
for (int j = w; j <= capacity; j++) {
    dp[j] = Math.max(dp[j], dp[j - w] + v);
}
```

### 贪心 vs DP
```java
// 贪心：每一步局部最优
// DP：记录所有可能，取最优
```

---

## 二、做题记录

### 1. 买卖股票的最佳时机（Easy）- 贪心 → LeetCode 121
> **已在 Day 2 完成（Kadane）**。本次复习：看题解才写出来 → 降级 🟡，登记 REV-66
- **核心思路：** 扫一遍，维护历史最低价 minPrice 和最大利润 maxProfit；`maxProfit=max(maxProfit, price-minPrice)`
- **代码实现：**
  ```java
  class Solution {
      public int maxProfit(int[] prices) {
          int minPrice = prices[0];
          int maxProfit = 0;
          for (int price : prices) {
              minPrice = Math.min(minPrice, price);
              maxProfit = Math.max(maxProfit, price - minPrice);
          }
          return maxProfit;
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** 🟡 复习时看题解（Day 2 曾 ✅）
- **感悟/易错点：** ✅ 是"当天会"，REV 是"隔天还会"——间隔久了会遗忘，需间隔复习兜底；这是 DP 雏形（不记过程只记状态）

### 2. 跳跃游戏（Medium）- 贪心 → LeetCode 55
- **核心思路：** 区间覆盖——`maxPos` 记录走过的范围内最远可达；判断"到不了"用 `i > maxPos`（**提前拦截**，比"走到死胡同再回头"更简洁）
- **代码实现：**
  ```java
  class Solution {
      public boolean canJump(int[] nums) {
          int maxPos = 0;
          for (int i = 0; i < nums.length; i++) {
              if (i > maxPos) return false;   // 我到不了 i，直接输
              maxPos = Math.max(maxPos, i + nums[i]);
          }
          return true;
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** 🟡 提示下完成（学员自己调出 `nums[i]==0 && maxPos<=i && length-1>i` 边界）
- **感悟/易错点：** 学员版"走到死胡同才回头" vs 标准版"路早就断了"——**在问题发生前拦截**；登记 REV-60

### 3. 跳跃游戏 II（Medium）- 贪心 → LeetCode 45
- **核心思路：** 接力赛交接棒——`end`=当前这一跳右边界、`maxPos`=下一跳最远；`i==end` 时 steps++ 并 `end=maxPos`；循环到 n-1 停
- **代码实现：**
  ```java
  class Solution {
      public int jump(int[] nums) {
          int steps = 0, end = 0, maxPos = 0;
          for (int i = 0; i < nums.length - 1; i++) {
              maxPos = Math.max(maxPos, i + nums[i]);
              if (i == end) {
                  steps++;
                  end = maxPos;
              }
          }
          return steps;
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** 🟡 骨架填充（三处填空全对）
- **感悟/易错点：** 与 55 区别：能不能到 vs 最少几步；"到边界就 +1"；登记 REV-61

### 4. 划分字母区间（Medium）- 贪心 → LeetCode 763
- **核心思路：** 先扫记每个字母**最后出现位置** last[]；再扫维护区间右边界 `end=max(end, last[c-'a'])`；`i==end` 切一刀
- **代码实现：**
  ```java
  class Solution {
      public List<Integer> partitionLabels(String s) {
          int[] last = new int[26];
          for (int i = 0; i < s.length(); i++) last[s.charAt(i) - 'a'] = i;
          int end = 0, count = 0;
          List<Integer> res = new ArrayList<>();
          for (int i = 0; i < s.length(); i++) {
              end = Math.max(end, last[s.charAt(i) - 'a']);
              count++;
              if (i == end) { res.add(count); count = 0; }
          }
          return res;
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** 🟡 提示下完成（终止条件自修 `i==end` 不是 `pos==end`）
- **感悟/易错点：** 与 45 同骨架（**到边界触发动作**）；边界=区间最大值不是单个字母位置；登记 REV-62

### 5. 爬楼梯（Easy）- DP → LeetCode 70
- **核心思路：** 状态转移 `f(n)=f(n-1)+f(n-2)`；递归版重复计算超时 → 记忆化 → 迭代滚动变量 ll/last/cur
- **代码实现：**
  ```java
  class Solution {
      public int climbStairs(int n) {
          if (n <= 2) return n;
          int ll = 1, last = 2;
          for (int i = 3; i <= n; i++) {
              int cur = ll + last;
              ll = last;
              last = cur;
          }
          return last;
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** 🟡（递归版独立 ✅，迭代版看视频转化）
- **感悟/易错点：** ⭐ 思维链"**先递归→记忆化→写成迭代**"是 DP 总开关；重叠子问题→滚动变量省空间；登记 REV-63

### 6. 杨辉三角（Easy）- 模拟 → LeetCode 118
- **核心思路：** 每行首尾是 1，中间数 = 上一行左上 + 正上
- **代码实现：**
  ```java
  class Solution {
      public List<List<Integer>> generate(int numRows) {
          List<List<Integer>> res = new ArrayList<>();
          for (int i = 0; i < numRows; i++) {
              List<Integer> row = new ArrayList<>();
              for (int j = 0; j <= i; j++) {
                  if (j == 0 || j == i) row.add(1);
                  else row.add(res.get(i-1).get(j-1) + res.get(i-1).get(j));
              }
              res.add(row);
          }
          return res;
      }
  }
  ```
- **复杂度：** O(n²) / O(1)（不计输出）
- **掌握程度：** 🟡（看过题解后自己写）
- **感悟/易错点：** List 取下标用 `.get(i)`（方法() vs 数组[]）；`new ArrayList<>(n)` 预分配、`List.of(1)` 不可变 List（REV-64）

### 7. 打家劫舍（Medium）- DP
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 8. 完全平方数（Medium）- 背包DP
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 9. 零钱兑换（Medium）- 背包DP
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 10. 单词拆分（Medium）- DP
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

---

## 三、今日总结

**学到的新模板/技巧：**
- ⭐ 贪心区间覆盖三题（55/45/763）：**"维护右边界，到边界触发一次动作"**（45 steps++ / 763 切一刀）；判断失败用"提前拦截 `i>maxPos`"
- ⭐ DP 思维链：**先递归 → 发现重叠子问题 → 记忆化 → 写成迭代（滚动变量）**（70/198）
- ⭐ DP 滚动变量三件套：`ll/last/cur`，每轮 `ll=last; last=cur`（70 加法 / 198 取 max）
- DP 五步法：状态含义 → 状态转移 → 初始化 → 遍历顺序 → 手动验证
- List 取值语法家族：`list.get(i)` / `new ArrayList<>(n)` / `List.of(1)` / 二维 List `res.get(i-1).get(j-1)`

**遇到的困难：**
- 45 一开始没思路（"什么时候 steps++"）→ 接力赛类比化解
- 198 迭代版漏滚动更新（`cur` 算完没 `ll=last; last=cur`）→ 和 70 对照修好
- 118 暴露 List 语法不熟（`.get()` vs `[]`）→ 登记 REV-64 专项

**遗留问题（需复习）：**
- 🔴 REV-60~66 待默写（55/45/763/70/118/198/121）
- 🔴 REV-64 List 语法专项抽查
- ⏸️ REV-58 4 中位数挂起待回炉
- 🔴 Day 12 剩余：322 零钱兑换 / 139 单词拆分 / 279 完全平方数（下一站）

**整体感受：** 😊
