# Day 10 · 回溯算法

> **日期：** 2026-08-14
> **学习目标：** 回溯算法框架与剪枝优化
> **相关知识页：** [[02-Wiki/专题总结/08-回溯算法]] · [[02-Wiki/专题总结/07-图论]]

---

## 一、今日模板回顾

### 回溯标准模板
```java
void backtrack(List<Integer> path, List<Integer> choices, List<List<Integer>> res) {
    if (/* 满足条件 */) {
        res.add(new ArrayList<>(path));
        return;
    }
    for (int choice : choices) {
        path.add(choice);
        backtrack(path, newChoices, res);
        path.remove(path.size() - 1);
    }
}
```

### 排列 vs 组合 vs 子集
```java
// 排列：visited 标记
// 组合：start 参数控制
// 子集：start + 每个节点都加入结果
```

### 去重技巧
```java
// 排序 + 同层跳过
Arrays.sort(nums);
for (int i = 0; i < nums.length; i++) {
    if (used[i] || (i > 0 && nums[i] == nums[i - 1] && !used[i - 1])) {
        continue;
    }
}
```

---

## 二、做题记录

### 1. 全排列（Medium）→ LeetCode 46
- **核心思路：** 同集合排顺序 → `used[]` 防同一数用两次；叶子（path 排满）才收集
- **代码实现：**
  ```java
  class Solution {
      List<List<Integer>> res = new ArrayList<>();
      List<Integer> path = new ArrayList<>();
      boolean[] used;
      public List<List<Integer>> permute(int[] nums) {
          used = new boolean[nums.length];
          backtrack(nums);
          return res;
      }
      void backtrack(int[] nums) {
          if (path.size() == nums.length) {
              res.add(new ArrayList<>(path));
              return;
          }
          for (int i = 0; i < nums.length; i++) {
              if (used[i]) continue;
              path.add(nums[i]);
              used[i] = true;
              backtrack(nums);
              used[i] = false;
              path.remove(path.size() - 1);
          }
      }
  }
  ```
- **复杂度：** O(n·n!) / O(n)
- **掌握程度：** 🟡 看题解后敲，待独立默写
- **感悟/易错点：** `res.add(new ArrayList<>(path))` 拷贝快照不是存引用；`if(used[i]) continue` 比 if 包裹更清晰；撤销顺序无要求（先撤销数组再删 path 也行）

### 2. 子集（Medium）→ LeetCode 78
- **核心思路：** 同集合不排顺序 → `start` 锁方向不回头（**不用 used**）；**每个节点**都是答案，函数开头无条件收集
- **代码实现：**
  ```java
  class Solution {
      List<List<Integer>> res = new ArrayList<>();
      List<Integer> path = new ArrayList<>();
      public List<List<Integer>> subsets(int[] nums) {
          backtrack(nums, 0);
          return res;
      }
      void backtrack(int[] nums, int start) {
          res.add(new ArrayList<>(path));     // 每个节点收集（空集在根节点）
          for (int i = start; i < nums.length; i++) {
              path.add(nums[i]);
              backtrack(nums, i + 1);         // 只往后选
              path.remove(path.size() - 1);
          }
      }
  }
  ```
- **复杂度：** O(2ⁿ·n) / O(n)
- **掌握程度：** 🟡 提示下完成，待独立默写
- **感悟/易错点：** ⭐2 考点：①为什么没显式终止条件不崩 → start 每层严格变大，for 自然到头 ②函数开头"空集"为什么不重复 → 加的是 path 快照，空集只在根出现一次

### 3. 电话号码的字母组合（Medium）→ LeetCode 17
- **核心思路：** 不同集合各选一 → `index` 定位按键（不用 start 不用 used）；选完所有按键（path.length()==digits.length()）叶子收集
- **代码实现：**
  ```java
  class Solution {
      List<String> res = new ArrayList<>();
      StringBuilder path = new StringBuilder();
      public List<String> letterCombinations(String digits) {
          if (digits.isEmpty()) return res;   // ⭐ 空串保护
          String[] map = {"","","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};
          backtrack(map, digits, 0);
          return res;
      }
      void backtrack(String[] map, String digits, int index) {
          if (path.length() == digits.length()) {
              res.add(path.toString());
              return;
          }
          String letters = map[digits.charAt(index) - '0'];   // ⭐ 位置 index → 数字键
          for (int i = 0; i < letters.length(); i++) {
              path.append(letters.charAt(i));
              backtrack(map, digits, index + 1);
              path.deleteCharAt(path.length() - 1);
          }
      }
  }
  ```
- **复杂度：** O(4ᴺ·N) / O(N)
- **掌握程度：** 🟡 提示下完成，待独立默写
- **感悟/易错点：** ERR-022 语法连锁（map.[index] 多点 / 逗号应 && / String 无 List 构造器 / StringBuilder 用 length() 不是 size() / 变量要传参 / 空串先挡门）；ERR-023 `map[digits.charAt(index)-'0']` 取抽屉（位置 index ≠ 数字键）

### 4. 组合总和（Medium）→ LeetCode 39
- **核心思路：** 同一个数可重复选 → 递归传 `i`（不是 `i+1`）；剪枝 `sum>target` return；收集 `sum==target`；`start` 锁死左边防重复（选 3 后下标 0 的 2 进不了分支 → 无 [3,2,2]）
- **代码实现：**
  ```java
  class Solution {
      List<List<Integer>> res = new ArrayList<>();
      List<Integer> path = new ArrayList<>();
      int sum = 0;   // 字段版：跨层共享，撤销要 sum-=candidates[i]
      public List<List<Integer>> combinationSum(int[] candidates, int target) {
          backtracking(candidates, target, 0);
          return res;
      }
      void backtracking(int[] candidates, int target, int start) {
          if (sum == target) { res.add(new ArrayList<>(path)); return; }
          if (sum > target) return;
          for (int i = start; i < candidates.length; i++) {
              sum += candidates[i];
              path.add(candidates[i]);
              backtracking(candidates, target, i);   // ⭐ 传 i 可重复
              sum -= candidates[i];
              path.remove(path.size() - 1);
          }
      }
  }
  ```
- **复杂度：** O(2ⁿ·n) / O(n)
- **掌握程度：** 🟡 提示下完成，待独立默写
- **感悟/易错点：** "传 i 实现可重复"是关键（REV-47）；sum 字段版 vs 参数版（REV-49）；防重复靠 start 锁左边

### 5. 括号生成（Medium）→ LeetCode 22
- **核心思路：** 不用 for（只有 2 选项且条件不同：放 `(` 要 `left<n`、放 `)` 要 `right<left` → 用 if 分支）；不用 start/used（无集合可选，两个计数器保证合法）；终止 `left==n&&right==n` 收集；`n==0` 空串保护
- **代码实现：**
  ```java
  class Solution {
      int left = 0, right = 0;
      List<String> res = new ArrayList<>();
      StringBuilder path = new StringBuilder();
      public List<String> generateParenthesis(int n) {
          if (n == 0) return res;   // ⭐ 空串保护
          backtracking(n);
          return res;
      }
      void backtracking(int n) {
          if (left == n && right == n) { res.add(path.toString()); return; }
          if (left < n) {
              path.append('('); left++;
              backtracking(n);
              path.deleteCharAt(path.length() - 1);   // ⭐ 下标不是字符（ERR-024）
              left--;
          }
          if (right < left) {
              path.append(')'); right++;
              backtracking(n);
              right--;
              path.deleteCharAt(path.length() - 1);
          }
      }
  }
  ```
- **复杂度：** O(4ⁿ/√n) / O(n)
- **掌握程度：** 🟡 提示下完成，待独立默写
- **感悟/易错点：** 为什么不用 for / 不用 start / 参数版 vs 字段版（REV-49）；`deleteCharAt(length-1)` 不是字符（ERR-024）

### 6. 单词搜索（Medium）→ LeetCode 79
- **核心思路：** 两层结构：exist 满地图找入口（两层 for + 匹配 word[0]）→ dfs 从入口四方向扩展；沉岛法标记（`board[i][j]='0'` 表示用过，word 只有字母不会撞）；撤销改回原字符；两个基准：越界/不匹配 → false、k==s.length()-1 匹配完 → true
- **代码实现：**
  ```java
  class Solution {
      public boolean exist(char[][] board, String word) {
          for (int row = 0; row < board.length; row++)
              for (int col = 0; col < board[0].length; col++)
                  if (dfs(board, word, row, col, 0)) return true;
          return false;
      }
      boolean dfs(char[][] b, String s, int row, int col, int k) {
          if (row < 0 || row >= b.length || col < 0 || col >= b[0].length || b[row][col] != s.charAt(k))
              return false;   // ⭐ >= 不是 >（ERR-025）
          if (k == s.length() - 1) return true;   // ⭐ 匹配完最后一个直接成功
          char c = b[row][col];
          b[row][col] = '0';   // 沉岛法标记
          // 短路版：找到一个方向 true 就停
          if (dfs(b, s, row - 1, col, k + 1)) return true;
          if (dfs(b, s, row + 1, col, k + 1)) return true;
          if (dfs(b, s, row, col - 1, k + 1)) return true;
          if (dfs(b, s, row, col + 1, k + 1)) return true;
          b[row][col] = c;   // ⭐ 撤销：共享对象必须复原
          return false;
      }
  }
  ```
- **复杂度：** O(m·n·4^L) / O(L)（L 单词长度）
- **掌握程度：** 🟡 提示下完成，待独立默写
- **感悟/易错点：** 两函数拆分（exist 找入口 / dfs 深搜）；边界 `>=`（ERR-025）；参数不用复原但共享对象必须复原（REV-49）；沉岛法标记安全（word 只含字母）

### 7. 分割回文串（Medium）→ LeetCode 131
- **核心思路：** 78 子集 start 套路套皮；`start` 锁定起点，从 `start` 往后枚举终点 `i`，`s[start..i]` 是回文就切下递归 `i+1`；终止 `start==s.length()` 收集
- **代码实现：**
  ```java
  class Solution {
      List<List<String>> res = new ArrayList<>();
      List<String> path = new ArrayList<>();
      public List<List<String>> partition(String s) {
          dfs(s, 0);
          return res;
      }
      void dfs(String s, int start) {
          if (start == s.length()) { res.add(new ArrayList<>(path)); return; }
          for (int i = start; i < s.length(); i++) {
              if (!isPalindrome(s, start, i)) continue;
              path.add(s.substring(start, i + 1));   // 左闭右开，endIndex 要 +1
              dfs(s, i + 1);
              path.remove(path.size() - 1);   // 撤销 = 腾位让同一 start 枚举更长前缀
          }
      }
      boolean isPalindrome(String s, int l, int r) {
          while (l < r) if (s.charAt(l++) != s.charAt(r--)) return false;
          return true;
      }
  }
  ```
- **复杂度：** O(n·2ⁿ) / O(n)
- **掌握程度：** 🟡 提示下完成，待独立默写
- **感悟/易错点：** `substring(start, i+1)` 左闭右开；撤销的真正作用（start 不动，i 递增，前缀变长）；回文双指针

### 8. N 皇后（Hard）→ LeetCode 51
- **核心思路：** 逐行放皇后（一行一个 → 天然免同行冲突）；防三样：列 + 主对角线 + 副对角线。两种实现：①isValid 版（向上扫描列/两对角线，直观 O(n)）②boolean 数组版（列 `col[j]`、主对角线 `diagMain[i-j+n-1]`、副对角线 `diagSub[i+j]`，O(1) 查重）
- **代码实现：**（boolean 数组版，理解优先）
  ```java
  class Solution {
      List<List<String>> res = new ArrayList<>();
      char[][] board;
      boolean[] col, diagMain, diagSub;

      public List<List<String>> solveNQueens(int n) {
          board = new char[n][n];
          for (char[] row : board) Arrays.fill(row, '.');
          // 主对角线（\）：i-j 恒定，但范围 -(n-1)~n-1 有负数 → 平移 +n-1
          // 副对角线（/）：i+j 恒定，范围 0~2n-2 非负 → 不用平移
          col = new boolean[n];
          diagMain = new boolean[2 * n - 1];
          diagSub = new boolean[2 * n - 1];
          backtrack(n, 0);
          return res;
      }
      void backtrack(int n, int row) {
          if (row == n) {
              List<String> list = new ArrayList<>();
              for (char[] r : board) list.add(new String(r));
              res.add(list);
              return;
          }
          for (int j = 0; j < n; j++) {
              if (col[j] || diagMain[row - j + n - 1] || diagSub[row + j]) continue;
              board[row][j] = 'Q'; col[j] = true;
              diagMain[row - j + n - 1] = true; diagSub[row + j] = true;
              backtrack(n, row + 1);
              board[row][j] = '.'; col[j] = false;
              diagMain[row - j + n - 1] = false; diagSub[row + j] = false;
          }
      }
  }
  ```
- **复杂度：** O(n!) / O(n)
- **掌握程度：** 🔄 看过答案，待默写
- **感悟/易错点：** ⭐ 对角线判定 = 直线方程：主对角 `i-j` 恒定（y=x+k，有负要平移 +n-1）、副对角 `i+j` 恒定（y=-x+k，天然非负）；回溯三件套：放Q → 下一行 → 拿走Q；isValid 版 vs boolean 数组版（直观 vs O(1)）

---

## 三、今日总结

**学到的新模板/技巧：**
-

**遇到的困难：**
-

**遗留问题（需复习）：**
-

**整体感受：** 😊 😐 😢
