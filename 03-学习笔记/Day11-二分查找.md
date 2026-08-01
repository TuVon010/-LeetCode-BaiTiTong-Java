# Day 11 · 二分查找

> **日期：** 2026-__-__
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

### 1. 搜索插入位置（Easy）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 2. 搜索二维矩阵（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

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
