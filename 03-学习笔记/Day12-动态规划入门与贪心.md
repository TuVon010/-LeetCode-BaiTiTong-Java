# Day 12 · 动态规划入门与贪心算法

> **日期：** 2026-08-18 ~ 08-19
> **学习目标：** 掌握 DP 基础模式与贪心算法
> **相关知识页：** [[02-Wiki/专题总结/10-动态规划]] · [[02-Wiki/专题总结/11-贪心算法]]

---

## 一、今日模板回顾
动态规划理解可以看灵神动态规划和卡尔背包的入门视频

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
- **核心思路：** `f(i)=max(f(i-1), f(i-2)+nums[i])`——要么不抢这家（继承上一家最优），要么抢这家（上上家最优 + 本家）；与 70 同骨架，只是"转移动作"加法 vs 取 max
- **代码实现：**
  ```java
  class Solution {
      public int rob(int[] nums) {
          if (nums.length == 1) return nums[0];
          int ll = nums[0];
          int last = Math.max(nums[0], nums[1]);
          for (int i = 2; i < nums.length; i++) {
              int cur = Math.max(ll + nums[i], last);
              ll = last;
              last = cur;
          }
          return last;
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** 🟡 迭代版补滚动更新（递归版独立 ✅）
- **感悟/易错点：** 滚动三件套 `ll/last/cur` 每轮 `ll=last; last=cur`（**漏滚动更新会错**）；不需要额外 max 变量——状态本身维护最大；登记 REV-65

### 8. 完全平方数（Medium）- 背包DP
- **核心思路：** 与 322 是同一道题（硬币 ↔ 完全平方数）：`dp[i]=min(dp[i], dp[i-j*j]+1)`；j 从 1 到 sqrt(i)，完全背包**正序**内层允许无限复用
- **代码实现：**
  ```java
  class Solution {
      public int numSquares(int n) {
          int[] dp = new int[n + 1];
          Arrays.fill(dp, n + 1);   // 默认"最大值"，表示凑不出
          dp[0] = 0;
          for (int i = 1; i <= n; i++) {
              for (int j = 1; j * j <= i; j++) {
                  dp[i] = Math.min(dp[i], dp[i - j * j] + 1);
              }
          }
          return dp[n];
      }
  }
  ```
- **复杂度：** O(n√n) / O(n)
- **掌握程度：** 🟡（内层循环条件自修）
- **感悟/易错点：** 内层循环条件是 `j*j<=i`（动态上限），不是 `i<=Math.sqrt(n)`（把外层 i 当成了内层变量）；`Arrays.fill` 配大数作"∞"哨兵；登记 REV-68

### 9. 零钱兑换（Medium）- 背包DP
- **核心思路：** 完全背包——`dp[cost]=min(dp[cost], dp[cost-coin]+1)`；外层 coins、内层 amount **正序**（允许同一硬币重复用）；初始化 `Arrays.fill(dp, amount+1)` 当"∞"，`dp[0]=0`
- **代码实现：**
  ```java
  class Solution {
      public int coinChange(int[] coins, int amount) {
          int[] dp = new int[amount + 1];
          Arrays.fill(dp, amount + 1);   // "∞"哨兵：凑不出
          dp[0] = 0;
          for (int i = 0; i < coins.length; i++) {        // 外层：硬币
              for (int cost = coins[i]; cost <= amount; cost++) {  // 内层：金额，正序
                  dp[cost] = Math.min(dp[cost], dp[cost - coins[i]] + 1);
              }
          }
          return dp[amount] == amount + 1 ? -1 : dp[amount];
      }
  }
  ```
- **复杂度：** O(coins × amount) / O(amount)
- **掌握程度：** 🟡（写了两版，逻辑已通）
- **感悟/易错点：** 为什么用数组不用滚动变量——状态转移要看**任意前面的金额**（不是只前 1-2 个），滚动变量只够看最近的；`coins[i]<=cost` 才更新 / 或 `cost` 从 `coins[i]` 起跳；登记 REV-67

### 10. 单词拆分（Medium）- DP
- **核心思路：** `dp[i]=true ⟺ 存在 j 使 dp[j]=true && s[j:i) 在字典里`；枚举所有切点 j，**不靠贪心"遇到就删"**（反例 "cars" + [car,ca,rs]：先删 car 剩 s 拆不开，其实 ca+rs 能拆）；hashset 装字典 O(1) 查词
- **代码实现：**
  ```java
  class Solution {
      public boolean wordBreak(String s, List<String> wordDict) {
          Set<String> dict = new HashSet<>(wordDict);
          int n = s.length();
          boolean[] dp = new boolean[n + 1];
          dp[0] = true;                       // 空串能拆
          for (int i = 1; i <= n; i++) {      // 枚举右端点
              for (int j = 0; j < i; j++) {   // 枚举切点
                  if (dp[j] && dict.contains(s.substring(j, i))) {
                      dp[i] = true;
                      break;
                  }
              }
          }
          return dp[n];
      }
  }
  ```
- **复杂度：** O(n²) / O(n)
- **掌握程度：** 🔄 看题解（迭代版 + 递归记忆化版都看过，未独立写）
- **感悟/易错点：** 递归记忆化版 memo 用 `int[]` 三态（-1 未算 / 0 拆不了 / 1 能拆）——boolean 无法区分"算过=false"和"没算过"；可加 maxLen 剪枝（只回看最长词长度）；登记 REV-69

---

## 三、今日总结

**学到的新模板/技巧：**
- ⭐ 贪心区间覆盖三题（55/45/763）：**"维护右边界，到边界触发一次动作"**（45 steps++ / 763 切一刀）；判断失败用"提前拦截 `i>maxPos`"
- ⭐ DP 思维链：**先递归 → 发现重叠子问题 → 记忆化 → 写成迭代（滚动变量）**（70/198）
- ⭐ DP 滚动变量三件套：`ll/last/cur`，每轮 `ll=last; last=cur`（70 加法 / 198 取 max）——**同一骨架，只差转移动作**
- ⭐ 完全背包 DP：`dp[i]=min(dp[i], dp[i-coin]+1)` 内层**正序**允许无限复用（322 硬币 ↔ 279 平方数是同一题）
- ⭐ 判断标准：状态转移只看前 1-2 个 → 滚动变量（70/198）；要看**任意前面的** → 数组（322/279/139）
- 139 单词拆分：`dp[i]=true ⟺ ∃j: dp[j] && 子串在字典`；贪心"遇到就删"会失败（"cars" 反例）
- DP 五步法：状态含义 → 状态转移 → 初始化 → 遍历顺序 → 手动验证
- List 取值语法家族：`list.get(i)` / `new ArrayList<>(n)` / `List.of(1)` / 二维 List `res.get(i-1).get(j-1)`

**遇到的困难：**
- 45 一开始没思路（"什么时候 steps++"）→ 接力赛类比化解（8-19 已独立默写 ✅）
- 198 迭代版漏滚动更新（`cur` 算完没 `ll=last; last=cur`）→ 和 70 对照修好
- 118 暴露 List 语法不熟（`.get()` vs `[]`）→ 登记 REV-64 专项
- 322 第一版想复用滚动变量 → 不行！要看任意前面的金额必须用数组
- 279 内层循环条件写错（`i<=sqrt(n)` 外层变量进了内层）→ 自修 `j*j<=i`
- 139 直觉"遇到字典词就删" → "cars"+[car,ca,rs] 反例，贪心不行必须 DP 枚举切点

**遗留问题（需复习）：**
- 🔴 REV-60~69 待默写（55/45/763/70/118/198/121 + 322/279/139）
- ✅ REV-61（45 跳跃游戏 II）8-19 已默写通过
- 🔴 REV-64 List 语法专项抽查
- ⏸️ REV-58 4 中位数挂起待回炉
- 🔴 139 单词拆分 🔄 待关题解独立默写（迭代版 + 递归记忆化版）

**整体感受：** 😊（Day 12 全部 10 题收官，背包 DP 三连从"晕"到"能写出来"）
