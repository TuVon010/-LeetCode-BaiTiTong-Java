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

### 4. 组合总和（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 5. 括号生成（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 6. 单词搜索（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 7. 分割回文串（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 8. N 皇后（Hard）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

---

## 三、今日总结

**学到的新模板/技巧：**
-

**遇到的困难：**
-

**遗留问题（需复习）：**
-

**整体感受：** 😊 😐 😢
