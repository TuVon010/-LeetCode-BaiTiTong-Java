# Day 1 · 哈希与双指针

> **日期：** 2026-__-__
> **学习目标：** 哈希表的使用技巧与双指针的经典模式
> **相关知识页：** [[02-Wiki/专题总结/01-哈希表]] · [[02-Wiki/专题总结/02-双指针与滑动窗口]]

---

## 一、今日模板回顾

### 哈希表
```java
// 模板速记：值 → 索引映射
Map<Integer, Integer> seen = new HashMap<>();
for (int i = 0; i < nums.length; i++) {
    if (seen.containsKey(target - nums[i])) {
        return new int[]{seen.get(target - nums[i]), i};
    }
    seen.put(nums[i], i);
}
```

### 双指针（对撞）
```java
int left = 0, right = nums.length - 1;
while (left < right) {
    if (nums[left] + nums[right] == target) {
        return new int[]{left, right};
    } else if (nums[left] + nums[right] < target) {
        left++;
    } else {
        right--;
    }
}
```

### 双指针（快慢）
```java
int slow = 0;
for (int fast = 0; fast < nums.length; fast++) {
    if (nums[fast] != 0) {
        int temp = nums[slow];
        nums[slow] = nums[fast];
        nums[fast] = temp;
        slow++;
    }
}
```

---

## 二、做题记录

### 1. 两数之和（Easy）
- **核心思路：** 哈希表"边遍历边查"——遍历时查 `target - nums[i]` 是否已出现，存"值→索引"映射
- **代码实现：**
  ```java
  Map<Integer, Integer> map = new HashMap<>();
  for (int i = 0; i < nums.length; i++) {
      int need = target - nums[i];
      if (map.containsKey(need)) {
          return new int[]{i, map.get(need)};
      }
      map.put(nums[i], i);
  }
  return new int[0];
  ```
- **复杂度：** O(n) / O(n)
- **掌握程度：** ✅
- **感悟/易错点：** 必须先查再存（否则重复元素可能返回同一下标）；`new int[]{}` 除声明外都要 new；返回下标顺序无关紧要

### 2. 字母异位词分组（Medium）
- **核心思路：** 排序后作为 key 分组——`Arrays.sort` 排序 char 数组，拼回字符串当 key
- **代码实现：**
  ```java
  Map<String, List<String>> map = new HashMap<>();
  for (String str : strs) {
      char[] arr = str.toCharArray();
      Arrays.sort(arr);
      String key = new String(arr);
      if (!map.containsKey(key)) {
          map.put(key, new ArrayList<>());
      }
      map.get(key).add(str);
  }
  return new ArrayList<>(map.values());
  ```
- **复杂度：** O(n·k·logk) / O(n·k)
- **掌握程度：** ✅
- **感悟/易错点：** `Arrays` 带 s（工具类）；判断存在用 `containsKey` 不是 `get`；for-each 要写类型 `String str`

### 3. 最长连续序列（Medium）
- **核心思路：** HashSet 去重 + 只从序列起点开始数——`!set.contains(num-1)` 才是起点
- **代码实现：**
  ```java
  Set<Integer> set = new HashSet<>();
  for (int num : nums) set.add(num);
  int maxLen = 0;
  for (int num : set) {
      if (!set.contains(num - 1)) {
          int current = num, len = 1;
          while (set.contains(current + 1)) {
              current++; len++;
          }
          maxLen = Math.max(maxLen, len);
      }
  }
  return maxLen;
  ```
- **复杂度：** O(n) / O(n)
- **掌握程度：** ✅
- **感悟/易错点：** 遍历集合不是数组；每个数最多被访问两次，保证 O(n)；Set 用 `add` 不是 `put`

### 4. 移动零（Easy）
- **核心思路：** 快慢指针——slow 指向非零元素落点，fast 扫描；非零交换到 slow 位置
- **代码实现（交换版）：**
  ```java
  int slow = 0;
  for (int fast = 0; fast < nums.length; fast++) {
      if (nums[fast] != 0) {
          int temp = nums[fast];
          nums[fast] = nums[slow];
          nums[slow] = temp;
          slow++;
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** ✅
- **感悟/易错点：** 我写了两版——覆盖+补零版、交换版，都是快慢指针；交换版更简洁；`index`/`slow` 就是慢指针，`i`/`fast` 就是快指针

### 5. 盛最多水的容器（Medium）
- **核心思路：** 对撞指针——两侧向中间靠拢，面积 = 宽 × 矮墙高度；**每次移动较矮的那侧**（木桶效应）
- **代码实现：**
  ```java
  int left = 0, right = height.length - 1, res = 0;
  while (left < right) {
      int h = Math.min(height[left], height[right]);
      res = Math.max(res, h * (right - left));
      if (height[left] < height[right]) {
          left++;
      } else {
          right--;
      }
  }
  return res;
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** ✅
- **感悟/易错点：** 移动矮墙是灵魂（移动高的只会让面积减小）；宽 = 下标差 `right-left`；相等时移哪边都行；数据大时可考虑 long 防溢出

### 6. 三数之和（Medium）
- **核心思路：** 排序 + 固定一个 + 双指针找两数之和 = `-nums[i]`；用 Set 自动去重
- **代码实现：**
  ```java
  Arrays.sort(nums);
  Set<List<Integer>> set = new HashSet<>();
  for (int i = 0; i < nums.length - 2; i++) {
      int left = i + 1, right = nums.length - 1;
      int need = -nums[i];
      while (left < right) {
          int twoSum = nums[left] + nums[right];
          if (need > twoSum) {
              left++;
          } else if (need < twoSum) {
              right--;
          } else {
              set.add(Arrays.asList(nums[i], nums[left], nums[right]));
              left++;     // ★ 加完必须移动指针，否则死循环
              right--;
          }
      }
  }
  return new ArrayList<>(set);
  ```
- **复杂度：** O(n²) / O(n²)（Set 去重版空间）
- **掌握程度：** ✅
- **感悟/易错点：** 两处 bug——`int[]` 不能放进 `List<Integer>` 要用 `Arrays.asList`；找到解后必须移动指针否则死循环。Set 去重法比手动 3 处去重更简单

### 7. 接雨水（Hard）
- **核心思路：** 每个位置水量 = `min(左最高, 右最高) - 当前高度`；对撞指针边走边维护左右最大值，谁矮先算谁
- **代码实现（对撞指针）：**
  ```java
  int left = 0, right = height.length - 1;
  int leftMax = 0, rightMax = 0, sum = 0;
  while (left < right) {
      leftMax = Math.max(leftMax, height[left]);
      rightMax = Math.max(rightMax, height[right]);
      if (leftMax < rightMax) {
          sum += leftMax - height[left];
          left++;
      } else {
          sum += rightMax - height[right];
          right--;
      }
  }
  return sum;
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** ✅（暴力版 ✅ + 对撞版 ✅）
- **感悟/易错点：** 先写了暴力版（每个位置左右各扫一遍 O(n²)）——思路对但可能超时；对撞版每轮锁定一格（拉链原理）。核心困惑见 [[02-Wiki/题目详解/42-接雨水]] 的"学员困惑解答"

---

## 三、今日总结

**学到的新模板/技巧：**
-

**遇到的困难：**
-

**遗留问题（需复习）：**
-

**整体感受：** 😊 😐 😢
