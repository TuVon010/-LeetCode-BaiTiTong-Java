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
- **核心思路：** 前缀和 + 哈希表。子数组和=k ⟺ 存在历史前缀和 = 当前前缀和 - k。不能用滑动窗口（有负数）
- **代码实现：**
  ```java
  import java.util.*;
  class Solution {
      public int subarraySum(int[] nums, int k) {
          Map<Integer, Integer> map = new HashMap<>();
          map.put(0, 1);          // 空数组前缀和
          int s = 0, ans = 0;
          for (int x : nums) {
              s += x;                                   // ① 先更新前缀和
              ans += map.getOrDefault(s - k, 0);        // ② 先查历史匹配
              map.put(s, map.getOrDefault(s, 0) + 1);   // ③ 后记录当前
          }
          return ans;
      }
  }
  ```
- **复杂度：** O(n) / O(n)
- **掌握程度：** ✅
- **感悟/易错点：** 我的写法（每轮开头记上一轮 s）与标准写法（初始 put(0,1)+轮尾记录）**等价且都正确**；核心是"查的时刻 map 里不含当前 s"。`map.put(0,1)` 不能省；用 getOrDefault

### 4. 滑动窗口最大值（Hard）
- **核心思路：** 单调递减队列（Deque）。新元素进来时用 while 弹出队尾所有比它小的（它们当不了最大）；队首过期就移除；队首就是当前窗口最大值
- **代码实现：**
  ```java
  import java.util.*;
  class Solution {
      public int[] maxSlidingWindow(int[] nums, int k) {
          if (nums.length == 0 || k == 0) return new int[0];
          Deque<Integer> q = new ArrayDeque<>();   // 存值（也可存索引）
          int[] res = new int[nums.length - k + 1];
          for (int i = 0; i < k; i++) {
              while (!q.isEmpty() && q.peekLast() < nums[i]) q.removeLast();
              q.addLast(nums[i]);
          }
          res[0] = q.peekFirst();
          for (int i = k; i < nums.length; i++) {
              if (q.peekFirst() == nums[i - k]) q.removeFirst();   // 过期
              while (!q.isEmpty() && q.peekLast() < nums[i]) q.removeLast();
              q.addLast(nums[i]);
              res[i - k + 1] = q.peekFirst();
          }
          return res;
      }
  }
  ```
- **复杂度：** O(n) / O(k)
- **掌握程度：** ✅
- **感悟/易错点：** 我用了"值版"（队列存值，用 == 判过期），也可用"索引版"；**弹小必须用 while 不是 if**（ERR-008）；Deque 四个方向：队尾淘汰(pollLast)+入队(addLast)、队首过期(pollFirst)+取值(peekFirst)；单调队列=餐厅等位名单

### 5. 最小覆盖子串（Hard）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 6. 最大子数组和（Medium）
- **核心思路：** Kadane 算法。`sum`（以 i 结尾的局部最优）= max(接上前面, 从自己重开)；`max`（全局最优）记录历史最好成绩。两道 max 分工：局部 vs 全局
- **代码实现：**
  ```java
  class Solution {
      public int maxSubArray(int[] nums) {
          int max = nums[0];
          int sum = 0;
          for (int i = 0; i < nums.length; i++) {
              sum = Math.max(sum + nums[i], nums[i]);   // 局部：接上 or 重开
              max = Math.max(max, sum);                  // 全局：记录最好成绩
          }
          return max;
      }
  }
  ```
- **复杂度：** O(n) / O(1)
- **掌握程度：** 🔄（需复习）
- **感悟/易错点：** 第一次接触 Kadane，似懂非懂。核心困惑："为什么两道 max"——sum 管"现在这段怎么走"，max 管"历史最好成绩"。sum 只涨跌当前，max 只涨不跌。必须初始化为 nums[0]（全负数时不能是 0）

### 7. 合并区间（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 附加巩固：买卖股票的最佳时机（121 · 非 Day2 计划内）
- **核心思路：** ⭐ 我自己想到的转化思路——算盈利数组（相邻差价），再套 Kadane 求最大连续子数组和。把"买卖股票"转化为"最大子数组和"！
- **代码实现：**
  ```java
  class Solution {
      public int maxProfit(int[] prices) {
          if (prices.length == 1) return 0;
          int[] profit = new int[prices.length - 1];
          int max = 0, sum = 0;
          for (int i = 0; i < profit.length; i++) {
              profit[i] = prices[i + 1] - prices[i];
              sum = Math.max(sum + profit[i], profit[i]);
              max = Math.max(max, sum);
          }
          return max;
      }
  }
  ```
- **复杂度：** O(n) / O(n)（可优化为 O(1)，不建数组）
- **掌握程度：** ✅
- **感悟/易错点：** 转化思路（盈利数组+Kadane）是我自己想到的；另一种解法是维护 minPrice 直接求差。max 初始化为 0（不交易=0 利润）。"举一反三"的里程碑！

---

## 三、今日总结

**学到的新模板/技巧：**
- 滑动窗口三大流派：不定长（3）、定长+计数数组（438）、定长+单调队列（239）
- 前缀和 + 哈希表（560）：`子数组和 = prefix[i+1] - prefix[j]`，有负数时不能用滑动窗口
- 计数数组技巧：`ch - 'a'` 映射下标，异位词 ⟺ 计数数组相等
- 单调队列（Deque）：队尾淘汰小的 + 队首过期移除，O(1) 取窗口最值
- String 基础 + Deque 四方向操作（详见 [[05-纠错文档/01-Java基础复习卡-String字符串]]）

**遇到的困难：**
- 滑动窗口 4 坑（ERR-006）：循环方向、更新时机、while vs if、长度+1
- 语法 5 坑（ERR-007）：charAt、length()、false、[]、滑入方向
- if vs while（ERR-008）：删除所有满足条件的用 while
- 前缀和"先查后记"与"先记上一轮"两种写法等价，核心是查的时刻不含当前 s
- 单调队列（239）第一次见较晕，用"餐厅等位名单"类比理解

**遗留问题（需复习）：**
- Day 2 剩余 3 题：76 最小覆盖子串（Hard）、53 最大子数组和、56 合并区间
- 239 单调队列需要二刷巩固
- ERR-006/007/008 待 Day 4 检验
- String 复习卡自测题待做

**整体感受：** 😊
