# Day 3 · 数组与矩阵

> **日期：** 2026-08-03
> **学习目标：** 数组原地操作技巧与矩阵遍历/变换
> **相关知识页：** [[02-Wiki/专题总结/03-数组与矩阵]] · [[02-Wiki/专题总结/01-哈希表]]

---

## 一、今日模板回顾

### 三次翻转法
```java
// 整体反转 + 前半反转 + 后半反转
reverse(nums, 0, nums.length - 1);
reverse(nums, 0, k - 1);
reverse(nums, k, nums.length - 1);

// 辅助方法
private void reverse(int[] nums, int start, int end) {
    while (start < end) {
        int temp = nums[start];
        nums[start] = nums[end];
        nums[end] = temp;
        start++;
        end--;
    }
}
```

### 前缀积+后缀积
```java
res[i] = prefix;   // 先乘前缀
res[i] *= suffix;  // 再乘后缀
```

### 螺旋矩阵边界收缩
```java
int top = 0, bottom = m - 1;
int left = 0, right = n - 1;
```

### 搜索二维矩阵（右上角法）
```java
int row = 0, col = n - 1;
```

---

## 二、做题记录

### 1. 轮转数组（Medium）
- **核心思路：** 三次翻转法。整体翻转→前k个翻转→后n-k个翻转，实现向右轮转 k 位。先 `k %= n` 处理 k>n
- **代码实现：**
  ```java
  class Solution {
      public void rotate(int[] nums, int k) {
          k %= nums.length;              // 关键：取模防越界
          reverse(nums, 0, nums.length - 1);  // 1. 整体翻转
          reverse(nums, 0, k - 1);            // 2. 前 k 个
          reverse(nums, k, nums.length - 1);  // 3. 后 n-k 个
      }
      private void reverse(int[] nums, int start, int end) {
          while (start < end) {
              int temp = nums[start];
              nums[start] = nums[end];
              nums[end] = temp;
              start++;
              end--;
          }
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** ✅
- **感悟/易错点：** 漏了 `k %= n` 导致越界（ERR-011）；三次翻转顺序不能变；reverse 双指针用 start<end

### 2. 除自身以外数组的乘积（Medium）
- **核心思路：** 两遍扫描。第一遍从左到右存"左边积"到 res；第二遍从右到左把"右边积"乘上去。答案 = 左积 × 右积
- **代码实现：**
  ```java
  class Solution {
      public int[] productExceptSelf(int[] nums) {
          int[] res = new int[nums.length];
          int left = 1;
          for (int i = 0; i < nums.length; i++) {
              res[i] = left;       // 第一遍：存左边积
              left *= nums[i];
          }
          int right = 1;
          for (int i = nums.length - 1; i >= 0; i--) {
              res[i] = res[i] * right;   // 第二遍：乘上右边积（叠加！）
              right *= nums[i];
          }
          return res;
      }
  }
  ```
- **复杂度：** O(n) / O(1)（res 不算）
- **掌握程度：** ✅
- **感悟/易错点：** 第二遍必须用 `*=`（叠加）不是 `=`（覆盖），否则丢第一遍结果（ERR-012）；"先赋值再更新"顺序不能反；不能用除法（有0会崩）

### 3. 缺失的第一个正数（Hard）
- **核心思路：** 原地哈希。答案必在 [1, n+1]。把值 x 归位到索引 x-1（原地哈希），归位后扫描第一个不对的位置就是答案。只处理 [1,n] 的值
- **代码实现：**
  ```java
  class Solution {
      public int firstMissingPositive(int[] nums) {
          int n = nums.length;
          for (int i = 0; i < n; i++) {
              // 值在[1,n] 且 目标位置不是我 → 交换归位
              while (1 <= nums[i] && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
                  int correctPos = nums[i] - 1;   // 值 x 应在索引 x-1
                  int temp = nums[correctPos];
                  nums[correctPos] = nums[i];
                  nums[i] = temp;
              }
          }
          for (int i = 0; i < n; i++) {
              if (nums[i] != i + 1) return i + 1;  // 第一个位置不对 → 缺失它
          }
          return n + 1;   // 全归位 → 1~n 都在 → 缺失 n+1
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** ✅（看思路后写出 + 自行发现死循环 bug）
- **感悟/易错点：** 核心洞察"答案在[1,n+1]"是难点；死循环保护 `nums[nums[i]-1] != nums[i]`（ERR-013）；值 x → 索引 x-1 的 off-by-one；while 不是 if（交换后新值可能还要归位）
- **感悟/易错点：**

### 4. 矩阵置零（Medium）
- **核心思路：** 用第一行第一列当标记板。先备份首行首列是否有0，再扫描内部打标记，根据标记置零，最后处理首行首列本身
- **代码实现：**
  ```java
  class Solution {
      public void setZeroes(int[][] matrix) {
          int m = matrix.length, n = matrix[0].length;
          boolean firstRowHasZero = false, firstColHasZero = false;
          for (int j = 0; j < n; j++) if (matrix[0][j] == 0) firstRowHasZero = true;
          for (int i = 0; i < m; i++) if (matrix[i][0] == 0) firstColHasZero = true;
          for (int i = 1; i < m; i++) {
              for (int j = 1; j < n; j++) {
                  if (matrix[i][j] == 0) {
                      matrix[i][0] = 0;   // 标记第 i 行
                      matrix[0][j] = 0;   // 标记第 j 列
                  }
              }
          }
          for (int i = 1; i < m; i++) {
              for (int j = 1; j < n; j++) {
                  if (matrix[i][0] == 0 || matrix[0][j] == 0) matrix[i][j] = 0;
              }
          }
          if (firstRowHasZero) for (int j = 0; j < n; j++) matrix[0][j] = 0;
          if (firstColHasZero) for (int i = 0; i < m; i++) matrix[i][0] = 0;
      }
  }
  ```
- **复杂度：** O(m×n) / O(1)
- **掌握程度：** 🟡（看思路后写出，但二维数组访问不熟）
- **感悟/易错点：** 二维数组访问是短板（REV-11）——`matrix.length`=行、`matrix[0].length`=列、`matrix[i][j]`；处理整行动 j、整列动 i；标记与置零必须分两步（防雪崩）；先备份首行首列
- **感悟/易错点：**

### 5. 螺旋矩阵（Medium）
- **核心思路：** 边界收缩法（剥洋葱）。维护 top/bottom/left/right 四边界，按 右→下→左→上 遍历，每走完一边收缩对应边界。下边/左边前要检查边界防重复
- **代码实现：**
  ```java
  import java.util.*;
  class Solution {
      public List<Integer> spiralOrder(int[][] matrix) {
          List<Integer> res = new ArrayList<>();
          int top = 0, bottom = matrix.length - 1;
          int left = 0, right = matrix[0].length - 1;
          while (top <= bottom && left <= right) {
              for (int i = left; i <= right; i++) res.add(matrix[top][i]);  // 上边
              top++;
              for (int i = top; i <= bottom; i++) res.add(matrix[i][right]); // 右边
              right--;
              if (top <= bottom) {                                            // ⭐ 下边检查
                  for (int i = right; i >= left; i--) res.add(matrix[bottom][i]);
                  bottom--;
              }
              if (left <= right) {                                            // ⭐ 左边检查
                  for (int i = bottom; i >= top; i--) res.add(matrix[i][left]);
                  left++;
              }
          }
          return res;
      }
  }
  ```
- **复杂度：** O(m×n) / O(1)
- **掌握程度：** ✅（看思路后写出，补了边界检查）
- **感悟/易错点：** 漏了"下边看上下(top<=bottom)、左边看左右(left<=right)"检查会重复遍历（ERR-014）；正好练了二维数组访问（REV-11）
- **感悟/易错点：**

### 6. 旋转图像（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 7. 搜索二维矩阵 II（Medium）
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
