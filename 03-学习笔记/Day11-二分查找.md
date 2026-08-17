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

### 3. 在排序数组中查找元素首末位置（Medium）→ LeetCode 34
- **核心思路：** 左边界 + 右边界两个模板组合；左边界找第一个 `>=target`（`<` 右移，否则左压，返回 left）；右边界找最后一个 `<=target`（`<=` 右移，否则左压，返回 right）；主函数判断 `left` 越界或 `nums[left]!=target` → 不存在
- **代码实现：**
  ```java
  class Solution {
      public int[] searchRange(int[] nums, int target) {
          int left = leftBound(nums, target);
          int right = rightBound(nums, target);
          if (left >= nums.length || nums[left] != target) return new int[]{-1, -1};
          return new int[]{left, right};
      }
      int leftBound(int[] nums, int target) {   // 找第一个 >= target
          int l = 0, r = nums.length - 1;
          while (l <= r) {
              int mid = l + (r - l) / 2;
              if (nums[mid] < target) l = mid + 1;
              else r = mid - 1;                 // 相等也压缩，找更左
          }
          return l;
      }
      int rightBound(int[] nums, int target) {  // 找最后一个 <= target
          int l = 0, r = nums.length - 1;
          while (l <= r) {
              int mid = l + (r - l) / 2;
              if (nums[mid] <= target) l = mid + 1;   // 相等也右移，找更右
              else r = mid - 1;
          }
          return r;
      }
  }
  ```
- **复杂度：** O(log n) / O(1)
- **掌握程度：** 🟡 看题解后默写（边界难理解）
- **感悟/易错点：** ⭐ 分界线本质：leftBound 维护 `<target | >=target` 分界线返回左；rightBound 维护 `<=target | >target` 返回右；循环结束 left=right+1 交叉；口诀"左边不认等号（<），右边认等号（<=）"

### 4. 搜索旋转排序数组（Medium）→ LeetCode 33
- **核心思路：** 旋转数组 = 两段升序；二分取 mid 后至少一半有序；判断哪半有序（`nums[left]<=nums[mid]`）→ target 在有序半范围内则去那半，否则去另一半
- **代码实现：**
  ```java
  class Solution {
      public int search(int[] nums, int target) {
          int l = 0, r = nums.length - 1;
          while (l <= r) {
              int mid = l + (r - l) / 2;
              if (nums[mid] == target) return mid;
              if (nums[l] <= nums[mid]) {        // 左半有序
                  if (nums[l] <= target && target < nums[mid]) r = mid - 1;
                  else l = mid + 1;
              } else {                            // 右半有序
                  if (nums[mid] < target && target <= nums[r]) l = mid + 1;
                  else r = mid - 1;
              }
          }
          return -1;
      }
  }
  ```
- **复杂度：** O(log n) / O(1)
- **掌握程度：** 🟡 骨架给定后填充（理解但需熟练）
- **感悟/易错点：** 口诀"哪半有序看左端点≤mid；target 在有序半范围 → 去那半，否则去另一半"

### 5. 寻找旋转排序数组中的最小值（Medium）→ LeetCode 153
- **核心思路：** 最小值藏在"无序的那一侧"（旋转点那侧）；每轮记录有序侧端点最小值，再去无序侧继续
- **代码实现：**
  ```java
  class Solution {
      public int findMin(int[] nums) {
          int min = nums[0];
          int l = 0, r = nums.length - 1;
          while (l <= r) {
              int mid = l + (r - l) / 2;
              if (nums[l] <= nums[mid]) {         // 左半有序
                  min = Math.min(min, nums[l]);
                  l = mid + 1;                    // 更小值在右半（旋转点那侧）
              } else {                             // 左半无序 → 旋转点在左半
                  min = Math.min(min, nums[mid]); // 右半必有序，最小值 = nums[mid]
                  r = mid - 1;
              }
          }
          return min;
      }
  }
  ```
- **复杂度：** O(log n) / O(1)
- **掌握程度：** ✅ 独立迁移（基于 33 思路自己改编，非照抄题解）
- **感悟/易错点：** 标准解更简洁：`nums[mid] > nums[right]` → 左移 else 右移；"最小值在无序侧"

### 6. 寻找两个正序数组的中位数（Hard）→ LeetCode 4
- **核心思路：** 切割线解法：不真合并，在较短数组上二分切分点 i，`j=(m+n+1)/2-i`；切割合法 ⟺ `A左≤B右 && B左≤A右`；奇数取左大、偶数取左大+右小平均；保证 nums1 较短防 j 越界
- **代码实现：**
  ```java
  class Solution {
      public double findMedianSortedArrays(int[] nums1, int[] nums2) {
          if (nums1.length > nums2.length) return findMedianSortedArrays(nums2, nums1);
          int m = nums1.length, n = nums2.length;
          int l = 0, r = m;
          while (l <= r) {
              int i = l + (r - l) / 2;
              int j = (m + n + 1) / 2 - i;
              int aL = (i == 0) ? Integer.MIN_VALUE : nums1[i-1];
              int aR = (i == m) ? Integer.MAX_VALUE : nums1[i];
              int bL = (j == 0) ? Integer.MIN_VALUE : nums2[j-1];
              int bR = (j == n) ? Integer.MAX_VALUE : nums2[j];
              if (aL <= bR && bL <= aR) {
                  if ((m + n) % 2 == 1) return Math.max(aL, bL);
                  return (Math.max(aL, bL) + Math.min(aR, bR)) / 2.0;
              } else if (aL > bR) r = i - 1;
              else l = i + 1;
          }
          return -1;
      }
  }
  ```
- **复杂度：** O(log(min(m,n))) / O(1)
- **掌握程度：** 🔄 看题解，理解了两种版本（二分切割 + 递归第K小），未独立写
- **感悟/易错点：** 切割线思想（左半等长 + 左≤右）；`j=(m+n+1)/2-i`；越界用 ±∞；保证短数组在前；递归版 `findKth`（淘汰 half，k-half）更好理解但也要多写

## 附：Day 11 二分模板总结

| 模板 | 循环 | 收缩 | 返回 |
|---|---|---|---|
| 标准二分 | `l<=r` | `l=mid+1 / r=mid-1` | `mid` 命中或 -1 |
| 左边界 | `l<=r` | `<target` 右移，否则左压 | `l`（第一个 ≥target）|
| 右边界 | `l<=r` | `<=target` 右移，否则左压 | `r`（最后一个 ≤target）|
| 旋转找数 | `l<=r` | 判断哪半有序再缩 | mid 命中或 -1 |
| 旋转找min | `l<=r` | 记录有序侧端点，去无序侧 | min |
| 两数组合并 | `l<=r` | 切分点 i 二分 | 中位数 |

---

## 三、今日总结

**学到的新模板/技巧：**
- 二分两大派：闭区间 `l<=r` + `mid±1`；左闭右开 `l<r` + `right=mid`
- 左右边界二分（34）：左边界不认等号返回 l、右边界认等号返回 r
- 旋转数组两件套（33/153）："哪半有序" + "最小值在无序侧"
- 虚拟拍平（74）：`row=mid/n, col=mid%n`
- 切割线求中位数（4）：短数组二分 + `j=(m+n+1)/2-i`

**遇到的困难：**
- 34 边界最难理解（分界线思想，需反复）
- 4 是 Hard 天花板：各种条件限制（±∞ 哨兵、奇偶、短数组优先）难消化，递归版更直观但都未动手

**遗留问题（需复习）：**
- 34 左右边界二分（REV-55 二分两派模板）
- 4 中位数切割线（🔄 看过题解未动手，待独立理解/默写）
- 33 旋转数组找数（🟡 需默写）
- 153 标准解 `nums[mid]>nums[right]`（✅ 独立解法已过，标准变体顺带默写）

**整体感受：** 😊
