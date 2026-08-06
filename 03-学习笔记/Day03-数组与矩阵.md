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
- **核心思路：** 顺时针旋转 90° = 转置 + 左右翻转。转置沿主对角线（`matrix[i][j] ↔ matrix[j][i]`），只遍历一半（上三角 `j=i+1` 或下三角 `j<i`，防交换两次）；再对每一行做左右翻转（即列对换 col i ↔ col n-1-i）。
- **代码实现：**
  ```java
  class Solution {
      public void rotate(int[][] matrix) {
          int n = matrix.length;
          if (n <= 1) return;              // 1x1 不用转

          // Step 1: 转置（沿主对角线翻转）
          for (int i = 0; i < n; i++) {
              for (int j = i + 1; j < n; j++) {   // j 从 i+1 开始，只遍历上三角
                  int temp = matrix[i][j];
                  matrix[i][j] = matrix[j][i];
                  matrix[j][i] = temp;
              }
          }

          // Step 2: 每行左右翻转（reverse）
          for (int i = 0; i < n; i++) {           // i 管行
              int left = 0, right = n - 1;
              while (left < right) {
                  int temp = matrix[i][left];
                  matrix[i][left] = matrix[i][right];
                  matrix[i][right] = temp;
                  left++; right--;
              }
          }
      }
  }
  ```
- **复杂度：** O(n²) / O(1)
- **掌握程度：** ✅（独立写出！思路 = 转置 + 列对换，与标准写法等价）
- **感悟/易错点：** ⭐ 学员自己推导出"列对换 col i ↔ col n-1-i"= 每行 reverse，理解到本质非背模板；注意 i 管行、j 管列，别写反（REV-11 实战）；转置只遍历一半防换两次变回去；`Collections.reverse()` 不能用于基本类型数组

### 7. 搜索二维矩阵 II（Medium）
- **核心思路：** 从右上角出发（或左下角），利用"行列都升序"剪枝。`target > 当前值` 往下（排除整行）、`target < 当前值` 往左（排除整列）。每次排除一整行/列，最多走 m+n 步。
- **代码实现：**
  ```java
  class Solution {
      public boolean searchMatrix(int[][] matrix, int target) {
          int m = matrix.length;         // m 行
          int n = matrix[0].length;      // n 列
          int i = 0, j = n - 1;          // 从右上角出发
          while (i < m && j >= 0) {
              if (target > matrix[i][j]) {
                  i++;                    // 排除第 i 行（左边全更小）
              } else if (target < matrix[i][j]) {
                  j--;                    // 排除第 j 列（下面全更大）
              } else {
                  return true;
              }
          }
          return false;
      }
  }
  ```
- **复杂度：** O(m+n) / O(1)
- **掌握程度：** ✅（独立写出！思路 = 右上角剪枝）
- **感悟/易错点：** ⭐ 学员自己想到"旋转 45° 看成二叉搜索树"——右上角就是根节点，天然一边全大一边全小；为什么右上/左下能行：那两个角落的列行方向一边大一边小，能先排除一边；O(m+n) 是这道题的最优/标准答案

---

## 三、今日总结

**学到的新模板/技巧：**
- 三次翻转法（189 轮转）：整体翻 → 前 k 翻 → 后 n-k 翻，先 `k %= n`
- 两遍扫描（238 除自身乘积）：左积 × 右积，第二遍用 `*=` 叠加
- 原地哈希（41 缺失正数）：值 x → 索引 x-1，带死循环保护
- 首行列标记（73 矩阵置零）：Excel 表头类比
- 边界收缩（54 螺旋矩阵）：剥洋葱，"下边看上下、左边看左右"
- 转置 + 翻转（48 旋转图像）：旋转 90° = 转置 + 每行 reverse（或列对换）
- 右上角剪枝（240 搜索矩阵）：旋转 45° = 二叉搜索树，每次排除整行/整列

**遇到的困难：**
- 二维数组访问不熟练（REV-11，已建复习卡，48/240 实战后基本过关）
- 忘取模（ERR-011）、覆盖vs叠加（ERR-012）、死循环保护（ERR-013）、螺旋边界（ERR-014）
- 心态：第一次看 Hard 想不到思路很正常，"被点一下能学会"就是进步

**遗留问题（需复习）：**
- REV-04/05/06/08 到期（Day 4 开始前检验）——今天已抽测 B1/B2/B3，B3 覆盖语义待巩固
- REV-11 二维数组：48/240 实战通过，下次复习时升为 🟢
- 41 原地哈希 / 73 矩阵置零 标记 🟡，Day 6 复习 REV-10 时重做

**整体感受：** 😊 圆满收官！Day 3 全 7 题完成，48/240 都是独立 AC
