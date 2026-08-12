# Day 9 · 单调栈与堆

> **日期：** 2026-08-12
> **学习目标：** 单调栈与堆（优先队列）的应用
> **相关知识页：** [[02-Wiki/专题总结/06-栈与堆]] · [[02-Wiki/专题总结/02-双指针与滑动窗口]]

---

## 一、今日模板回顾

### 单调栈模板
```java
Deque<Integer> stack = new ArrayDeque<>();
for (int i = 0; i < nums.length; i++) {
    while (!stack.isEmpty() && nums[stack.peek()] < nums[i]) {
        int idx = stack.pop();
        res[idx] = nums[i];
    }
    stack.push(i);
}
```

### 堆操作
```java
import java.util.PriorityQueue;

PriorityQueue<Integer> heap = new PriorityQueue<>();
heap.offer(val);   // 入堆
val = heap.poll();  // 出堆
// 大根堆：使用 Collections.reverseOrder()
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
maxHeap.offer(val);
```

### 双堆求中位数
```java
// small: 大根堆（PriorityQueue + Collections.reverseOrder()），存较小一半
// large: 小根堆，存较大一半
// 保持 small.size() >= large.size() 且 small 最大 <= large 最小
```

---

## 二、做题记录

### 1. 每日温度（Medium）
- **核心思路：** 单调递减栈，栈内存**下标**。新来的温度更高，就把栈里比它矮的都弹出，顺便算好答案 `res[m] = i - m`。
- **代码实现：**
  ```java
  class Solution {
      public int[] dailyTemperatures(int[] temperatures) {
          ArrayDeque<Integer> stack = new ArrayDeque<>();
          int len = temperatures.length;
          int[] res = new int[len];
          stack.push(0);
          for (int i = 1; i < len; i++) {
              int cur = temperatures[i];
              while (!stack.isEmpty() && temperatures[stack.peek()] < cur) {
                  int m = stack.pop();
                  res[m] = i - m;
              }
              stack.push(i);
          }
          return res;
      }
  }
  ```
- **复杂度：** O(n) / O(n)
- **掌握程度：** 🟡（骨架自己写，思路给提示，两 bug 引导修复）
- **感悟/易错点：** ⭐ 栈里存**下标**，取温度必须 `temperatures[stack.peek()]`（ERR-021）；`push(0)` 只保证开场不空，while 里弹空后还要再查 → 必须先判 `!stack.isEmpty()`；答案 = 两个下标相减 = 等了几天

### 2. 柱状图中最大的矩形（Hard）
- **核心思路：** 每根柱子轮流当"天花板"（矩形的高 = 内部最矮那根），往左右延伸到第一个比自己矮的柱子为止。优化：单调**递增**栈，一次算出所有柱子的左右边界。
- **代码实现（暴力版 O(n²)，已 AC）：**
  ```java
  class Solution {
      public int largestRectangleArea(int[] heights) {
          int len = heights.length;
          if (len == 0) return 0;
          int res = 0;
          for (int i = 0; i < len; i++) {
              int left = i;
              int curHeight = heights[i];
              while (left > 0 && heights[left - 1] >= curHeight) left--;
              int right = i;
              while (right < len - 1 && heights[right + 1] >= curHeight) right++;
              int width = right - left + 1;
              res = Math.max(res, width * curHeight);
          }
          return res;
      }
  }
  ```
- **复杂度：** 暴力 O(n²) / O(1)；单调栈 O(n) / O(n)
- **掌握程度：** ⏳ 进行中（暴力已 AC，单调栈左边界模板已给，待完成 right[] + 面积）
- **感悟/易错点：** "延伸"= 天花板高度固定的前提下能罩多宽，到第一根更矮的柱子就得停（短板效应）；单调栈弹掉的是**比新来的矮或相等**的柱子（`>=` 弹掉相等的防挡路）

### 3. 数组中的第 K 个最大元素（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 4. 前 K 个高频元素（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 5. 数据流的中位数（Hard）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

---

## 三、今日总结

**学到的新模板/技巧：**
- 单调栈模板：存**下标**，弹小算答案（739）；递增栈弹高算边界（84）
- ⭐ ERR-021：单调栈存下标，取真值必须 `数组[stack.peek()]`
- 空栈保护：`!stack.isEmpty() &&` 短路（peek/pop 空栈都会崩）
- REV-33 小测：StringBuilder 不可变误区已补；char→int、拼多位数、数组默认值已过

**遇到的困难：**
- 84 单调栈（left[]/right[] 三遍法）未完成，明天继续
- 394（REV-34）逻辑通了但还需关题解独立默写
- 单调栈 peek/pop 方法记不牢，需背速查表

**遗留问题（需复习）：**
- ⏳ 84 单调栈版：补 right[]（从右往左扫）+ 面积循环
- ⏳ Day 9 剩 3 题：215 第K大 / 347 前K高频 / 295 中位数
- 🟡 REV-34 394 独立默写（关题解）
- 🟡 REV-29 437 前缀和默写、REV-30 543/230/124 三件套默写、REV-28 头插法实战（86/92）都还没做，顺延
- 🔴 REV-33 Q1 String 不可变误区需再抽

**整体感受：** 😐（状态还在，但今天到 84 暴力解已尽力，先休息）
