# Day 11 · 二分查找

> **日期：** 2026-08-17
> **学习目标：** 二分查找的多种变体与应用
> **相关知识页：** [[02-Wiki/专题总结/09-二分查找]] · [[02-Wiki/专题总结/03-数组与矩阵]]

---

## 一、今日模板回顾

### 标准二分
```java
int left = 0, right = nums.length - 1;
while (left <= right) {
    int mid = left + (right - left) / 2;
    if (nums[mid] == target) return mid;
    else if (nums[mid] < target) left = mid + 1;
    else right = mid - 1;
}
return -1;
```

### 左边界二分
```java
// 找第一个 >= target 的位置
int left = 0, right = nums.length - 1;
while (left <= right) {
    int mid = left + (right - left) / 2;
    if (nums[mid] < target) {
        left = mid + 1;
    } else {
        right = mid - 1;
    }
}
return left;
```

### 右边界二分
```java
// 找最后一个 <= target 的位置
int left = 0, right = nums.length - 1;
while (left <= right) {
    int mid = left + (right - left) / 2;
    if (nums[mid] <= target) {
        left = mid + 1;
    } else {
        right = mid - 1;
    }
}
return right;
```

---

## 二、做题记录

### 1. 搜索插入位置（Easy）→ LeetCode 35
- **核心思路：** 找第一个 `>= target` 的位置（插入位置）；闭区间模板 `while(left <= right)` + `mid±1`；mid 用 `left + (right-left)/2` 防溢出
- **代码实现：**
  ```java
  class Solution {
      public int searchInsert(int[] nums, int target) {
          int left = 0, right = nums.length - 1;
          while (left <= right) {   // 闭区间，left==right 也要查
              int mid = left + (right - left) / 2;   // 防溢出
              if (nums[mid] == target) return mid;
              if (target < nums[mid]) right = mid - 1;
              else left = mid + 1;
          }
          return left;   // 插入位置 = 第一个 >= target 的下标
      }
  }
  ```
- **复杂度：** O(log n) / O(1)
- **掌握程度：** ✅ 独立写出（边界自修）
- **感悟/易错点：** 闭区间模板 left<=right 配 mid±1；两派模板（闭区间 vs 左闭右开）选一个用熟

### 2. 搜索二维矩阵（Medium）→ LeetCode 74
- **核心思路：** 法一"虚拟拍平"——把 m×n 矩阵想象成一维数组，二分下标映射 `row=mid/n, col=mid%n`（O(log mn)）；法二 L 型走法——右上角起点，`value<target` 往下 i++，`>target` 往左 j--（O(m+n)）
- **代码实现：**
  ```java
  // 法一：虚拟拍平 + 二分
  class Solution {
      public boolean searchMatrix(int[][] matrix, int target) {
          int m = matrix.length, n = matrix[0].length;
          int left = 0, right = m * n - 1;
          while (left <= right) {
              int mid = left + (right - left) / 2;
              int row = mid / n, col = mid % n;   // ⭐ mid%n 不是 %m！
              int value = matrix[row][col];
              if (value == target) return true;
              if (value < target) left = mid + 1;
              else right = mid - 1;
          }
          return false;
      }
  }
  // 法二：右上角 L 型（240 也会用）
  // int i=0, j=n-1; while(i<m && j>=0) { if==target true; if< i++; else j--; }
  ```
- **复杂度：** O(log(m·n)) / O(m+n) / O(1)
- **掌握程度：** ✅ 两法都独立写出（各修一个边界 bug）
- **感悟/易错点：** `col=mid%n`（按列数取模，不是行数）；法二 `j>=0`（第 0 列也要查）；法二不是最优（O(m+n)），面试答二分最优

### 3. 在排序数组中查找元素首末位置（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 4. 搜索旋转排序数组（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 5. 寻找旋转排序数组中的最小值（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 6. 寻找两个正序数组的中位数（Hard）
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
