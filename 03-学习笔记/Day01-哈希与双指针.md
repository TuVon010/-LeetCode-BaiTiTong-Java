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
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 6. 三数之和（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 7. 接雨水（Hard）
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
