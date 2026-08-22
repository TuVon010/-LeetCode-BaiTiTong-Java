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
- **核心思路：** 骨架 E（矩阵路径）二维 DP 入门；递归"传话员"（每个格子问孩子路径数）→ 记忆化 → 迭代自底向上；`dp[i][j]=dp[i+1][j]+dp[i][j+1]`（数方案用 +）
- **代码实现：**（学员独立从记忆化递归改写为迭代 DP）
  ```java
  class Solution {
      public int uniquePaths(int m, int n) {
          int[][] dp = new int[m][n];
          dp[m-1][n-1] = 1;                    // 终点：1 条路（原地）
          for (int i = m-1; i >= 0; i--) {
              for (int j = n-1; j >= 0; j--) {
                  if (i == m-1 && j == n-1) continue;      // 终点
                  else if (i == m-1) dp[i][j] = dp[i][j+1];      // 最后一行：只能向右
                  else if (j == n-1) dp[i][j] = dp[i+1][j];      // 最后一列：只能向下
                  else dp[i][j] = dp[i+1][j] + dp[i][j+1];       // 中间：向下+向右
              }
          }
          return dp[0][0];
      }
  }
  ```
- **复杂度：** O(m×n) / O(m×n)
- **掌握程度：** 🟡 提示下完成（从递归→记忆化→迭代思维链自己走通）
- **感悟/易错点：** ⭐ "递归传话员"类比：每个格子把孩子报上来的数相加再上报；登记 REV-74；可状态压缩成一维 `dp[j]+=dp[j+1]`

### 6. 最小路径和（Medium）
- **核心思路：** 62 同骨架 E，只差转移：数方案用 +，求**最小和**用 `min`；`dp[i][j]=min(dp[i+1][j],dp[i][j+1])+grid[i][j]`
- **代码实现：**（学员写了 3 版：迭代 DP / 记忆化递归 / 纯递归，全对）
  ```java
  class Solution {
      public int minPathSum(int[][] grid) {
          int m = grid.length, n = grid[0].length;
          int[][] dp = new int[m][n];
          dp[m-1][n-1] = grid[m-1][n-1];
          for (int i = m-1; i >= 0; i--) {
              for (int j = n-1; j >= 0; j--) {
                  if (i == m-1 && j == n-1) continue;
                  else if (i == m-1) dp[i][j] = dp[i][j+1] + grid[i][j];
                  else if (j == n-1) dp[i][j] = dp[i+1][j] + grid[i][j];
                  else dp[i][j] = Math.min(dp[i][j+1], dp[i+1][j]) + grid[i][j];
              }
          }
          return dp[0][0];
      }
  }
  ```
- **复杂度：** O(m×n) / O(m×n)
- **掌握程度：** 🟡 从 62 迁移，几乎独立（换 min 即得）
- **感悟/易错点：** ⭐ 62↔64 = "+数方案 vs min 求最优"；记忆化版暴露**二维数组初始化坑**（REV-76）；登记 REV-75

### 7. 最长回文子串（Medium）
- **核心思路：** 区间 DP（骨架 G）——`dp[i][j]` = s[i..j] 是不是回文；`dp[i][j] = (s[i]==s[j]) && dp[i+1][j-1]`；**按区间长度从小到大填**（因为依赖更短区间）
- **代码实现：**（看过 4 种解法，最推荐中心扩展统一写法）
  ```java
  class Solution {
      public String longestPalindrome(String S) {
          char[] s = S.toCharArray();
          int n = s.length;
          int ansLeft = 0, ansRight = 0;
          // 中心扩展：遍历 2n-1 个中心（i 偶=单字符中心，i 奇=双字符中心）
          for (int i = 0; i < 2 * n - 1; i++) {
              int l = i / 2;
              int r = (i + 1) / 2;       // l = i/2(下取整), r=(i+1)/2(上取整)
              while (l >= 0 && r < n && s[l] == s[r]) {
                  l--;
                  r++;
              }
              if (r - l - 1 > ansRight - ansLeft) {   // while 后长度 = r-l-1（l、r 各多走一步）
                  ansLeft = l + 1;
                  ansRight = r;          // 左闭右开
              }
          }
          return S.substring(ansLeft, ansRight);
      }
  }
  ```
- **复杂度：** 区间DP O(n²)/O(n²)；中心扩展 O(n²)/O(1)
- **掌握程度：** 🔄 看题解（看过递归/记忆化/区间DP/中心扩展 4 种）
- **感悟/易错点：** ⭐ 三种解法 = 同一思维链"递归→记忆化→迭代"；`dp[i+1][j-1]` 依赖更短区间 → **按长度从小填**；登记 REV-77（学员点名要默写中心扩展 + 区间 DP）

### 8. 最长公共子序列（Medium）
- **核心思路：** 双串 DP（骨架 F）——`dp[i][j]` = text1 **前 i 个** vs text2 **前 j 个**的 LCS；**配对 vs 淘汰**：相等 `dp[i-1][j-1]+1`（两尾巴都用上），不等 `max(dp[i-1][j], dp[i][j-1])`（至少淘汰一边尾巴）
- **代码实现：**（学员递归版看题解后独立完成，迭代版前缀法迁移 1143）
  ```java
  class Solution {
      public int longestCommonSubsequence(String text1, String text2) {
          int n = text1.length(), m = text2.length();
          int[][] dp = new int[n + 1][m + 1];   // 前 i 个 / 前 j 个，0 留空串
          for (int i = 1; i <= n; i++) {
              for (int j = 1; j <= m; j++) {
                  if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                      dp[i][j] = dp[i - 1][j - 1] + 1;       // 配对
                  } else {
                      dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);  // 淘汰
                  }
              }
          }
          return dp[n][m];
      }
  }
  ```
- **复杂度：** O(n×m) / O(n×m)
- **掌握程度：** 🟡 看题解后独立完成（递归 + 迭代两版都写对）
- **感悟/易错点：** ⭐ "配对 vs 淘汰"对称操作是 LCS 钥匙；0 下标版边界不能 +1（一数多吃，`"a"` vs `"aa"` 反例）；**下标差 1 = "前 i 个"标志**，双串 DP 统一套路；登记 REV-78

### 9. 编辑距离（Hard）
- **核心思路：** 双串 DP（骨架 F）进阶——三种操作 `min(删, 插, 替)`；相等 `dp[i-1][j-1]`（不用操作），不等 `min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1`
- **代码实现：**（学员递归版看题解后独立完成，迭代版两版全对）
  ```java
  class Solution {
      public int minDistance(String word1, String word2) {
          int m = word1.length(), n = word2.length();
          int[][] dp = new int[m + 1][n + 1];   // 前缀法便于处理边界
          for (int i = 0; i <= m; i++) dp[i][0] = i;   // 删 i 次
          for (int j = 0; j <= n; j++) dp[0][j] = j;   // 插 j 次
          for (int i = 1; i <= m; i++) {
              for (int j = 1; j <= n; j++) {
                  if (word1.charAt(i-1) == word2.charAt(j-1)) {
                      dp[i][j] = dp[i-1][j-1];
                  } else {
                      int f1 = dp[i][j-1] + 1;      // 插入
                      int f2 = dp[i-1][j] + 1;      // 删除
                      int f3 = dp[i-1][j-1] + 1;    // 替换
                      dp[i][j] = Math.min(Math.min(f1, f2), f3);
                  }
              }
          }
          return dp[m][n];
      }
  }
  ```
- **复杂度：** O(n×m) / O(n×m)
- **掌握程度：** 🟡 看题解后独立完成（递归 + 迭代两版都写对）
- **感悟/易错点：** ⭐ 与 LCS 同骨架只差**转移动作**（max→min+三操作）；base case `dp[i][0]=i`/`dp[0][j]=j` 与递归边界一一对应；`Math.min` 只接受 2 参数要嵌套；登记 REV-79

---

## 三、今日总结

**学到的新模板/技巧：**
- ⭐ 二维 DP 骨架 E（矩阵路径）：62 数方案（`+`）/ 64 最小和（`min`）——同骨架只差转移动作
- ⭐ 思维链"递归传话员 → 记忆化 → 迭代 DP"连续在 62/64/5/1143/72 复现（已形成条件反射）
- ⭐ 区间 DP（骨架 G）：`dp[i][j]` 表示区间；**按区间长度从小到大填**（依赖更短区间）
- ⭐ 回文子串 4 种解法：纯递归 / 记忆化 / 区间 DP / 中心扩展（O(1) 空间最推荐，`l=i/2, r=(i+1)/2` 合并奇偶中心）
- ⭐ 0-1 背包倒序彻底吃透：正序=一数多吃（用 `nums=[1,3],target=2` 合法反例证明）；临时数组版 vs 倒序版等价
- ⭐ REV-76 二维数组初始化：`Arrays.fill` 只能填一维，二维要逐行 `for(int[] row : memo) Arrays.fill(row,-1)`
- ⭐ 双串 DP（骨架 F）**"配对 vs 淘汰"**：1143 相等配 `+1`/不等淘汰 `max`；72 不等则 `min(删,插,替)+1`——同骨架只差转移动作（max→min）
- ⭐ 双串 DP 降维三问：①依赖谁（上/左/左上）②哪个旧值被覆盖还急需 ③被覆盖又需要 → 变量 `prev` 暂存（**左上**被"左"覆盖是唯一坑）
- ⭐ 双串 DP 下标差 1 套路：`dp[i][j]` 表示"前 i 个"→ 取字符用 `i-1`/`j-1`，`[m+1][n+1]` 让空串天然为 0 免边界特判

**遇到的困难：**
- 416 快问：正序倒序说不清 → 补课用"同物品能不能当轮重复用"讲透，学员最终自己讲出"j_大-num=j_old 已拿过一数"
- 62：`void`+count 回溯超时 → 引导改成"int 传话员"递归，学员自己翻译成迭代 DP
- 64：学员写了 3 个版本全对（迭代/记忆化/纯递归），暴露二维数组 fill 坑
- 5：区间 DP 不会 → 给递归+记忆化+DP 三版讲透；学员又找灵茶中心扩展题解要求注释
- 1143：对"为什么淘汰是 max(dfs(i-1,j), dfs(i,j-1))"困惑 → 用"至少淘汰一方尾巴"讲透；边界 `+1` 一数多吃（`"a"` vs `"aa"` 反例）
- 72：递归/迭代两版都看题解思路后**独立完成**，三个操作方向 + base case 全对

**遗留问题（需复习）：**
- 🔴 REV-74/75/76/77/78/79 待默写（62/64/二维数组/5/1143/72，其中 5 点名要默写中心扩展+区间DP）
- 🔴 REV-70~73 等旧题待默写（按 v1.5 每天最多 3 道）
- 🟡 二维 DP 降维：只到"理解思路"层（知道为啥要 prev），暂不强制默写（学员决定）
- ⏸️ REV-58 4 中位数挂起待回炉

**整体感受：** 😊（二维/区间/双串 DP 全家桶，Day 13 九题全收官，进度 92%）
