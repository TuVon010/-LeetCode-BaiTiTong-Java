# Day 2 · 滑动窗口与子串

> **日期：** 2026-08-02
> **学习目标：** 滑动窗口三问法与子串处理技巧
> **相关知识页：** [[02-Wiki/专题总结/02-双指针与滑动窗口]] · [[02-Wiki/专题总结/03-数组与矩阵]]

---

## 一、今日模板回顾

### 滑动窗口通用模板
```java
int left = 0;
for (int right = 0; right < s.length(); right++) {
    // 右扩：将 s.charAt(right) 纳入窗口
    // while 窗口不合法：左缩
    // 更新答案
}
```

### 前缀和
```java
int[] prefix = new int[nums.length + 1];
for (int i = 0; i < nums.length; i++) {
    prefix[i + 1] = prefix[i] + nums[i];
}
// 区间 [i, j] 和 = prefix[j+1] - prefix[i]
```

### 单调队列
```java
import java.util.Deque;
import java.util.ArrayDeque;
Deque<Integer> q = new ArrayDeque<>();
// 维护单调递减：队首始终是最大值
```

---

## 二、做题记录

### 1. 无重复字符的最长子串（Medium）
- **核心思路：** 不定长滑动窗口 + HashSet。右指针扩展窗口，遇到重复字符时左指针收缩到无重复，每次扩展后更新最大长度
- **代码实现：**
  ```java
  import java.util.*;
  class Solution {
      public int lengthOfLongestSubstring(String s) {
          Set<Character> set = new HashSet<>();
          int maxlen = 0, left = 0;
          for (int right = 0; right < s.length(); right++) {
              char ch = s.charAt(right);
              while (set.contains(ch)) {
                  set.remove(s.charAt(left));
                  left++;
              }
              set.add(ch);
              maxlen = Math.max(maxlen, right - left + 1);
          }
          return maxlen;
      }
  }
  ```
- **复杂度：** O(n) / O(k)（k=字符集大小）
- **掌握程度：** ✅
- **感悟/易错点：** 踩了 4 个坑——循环条件写反、只在重复时更新答案（无重复会得 0）、用 if 而不是 while 收不干净、长度公式忘 +1 且 remove 顺序反了。滑动窗口核心：**每次扩展后更新答案 + while 收缩**

### 2. 找到字符串中所有字母异位词（Medium）
- **核心思路：** 定长滑动窗口 + 计数数组。窗口大小 = len(p)，每次滑出左边(-1)、滑入右边(+1)，比较窗口计数与 p 计数是否相等（Arrays.equals）
- **代码实现：**
  ```java
  import java.util.*;
  class Solution {
      public boolean areEqual(int[] cntP, int[] window) {
          for (int i = 0; i < cntP.length; i++)
              if (cntP[i] != window[i]) return false;
          return true;
      }
      public List<Integer> findAnagrams(String s, String p) {
          if (p.length() > s.length()) return new ArrayList<>();
          int[] cntP = new int[26], window = new int[26];
          List<Integer> res = new ArrayList<>();
          for (int i = 0; i < p.length(); i++) {
              cntP[p.charAt(i) - 'a']++;
              window[s.charAt(i) - 'a']++;
          }
          if (areEqual(cntP, window)) res.add(0);
          for (int left = 0; left < s.length() - p.length(); left++) {
              window[s.charAt(left) - 'a']--;                // 左边滑出
              window[s.charAt(left + p.length()) - 'a']++;   // 右边滑入
              if (areEqual(cntP, window)) res.add(left + 1);
          }
          return res;
      }
  }
  ```
- **复杂度：** O(n) / O(1)（固定 26 数组）
- **掌握程度：** ✅
- **感悟/易错点：** 异位词 ⟺ 计数数组相等（关键洞察）；滑出减、滑入加；`charAt` 拼写、`length()` 带括号、`false` 小写、数组参数带 `[]`（见 ERR-007）

### 3. 和为 K 的子数组（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 4. 滑动窗口最大值（Hard）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 5. 最小覆盖子串（Hard）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 6. 最大子数组和（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 7. 合并区间（Medium）
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
