# Day 9 · 单调栈与堆

> **日期：** 2026-08-12（8-12 开场复习专场；8-13 完成 84/215/347/295）
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
- **代码实现（单调栈两遍版 O(n)，已 AC）：**
  ```java
  class Solution {
      public int largestRectangleArea(int[] heights) {
          int n = heights.length;
          int[] left = new int[n], right = new int[n];
          Deque<Integer> stack = new ArrayDeque<>();

          for (int i = 0; i < n; i++) {
              while (!stack.isEmpty() && heights[stack.peek()] >= heights[i]) stack.pop();
              left[i] = stack.isEmpty() ? -1 : stack.peek();
              stack.push(i);
          }
          stack.clear();
          for (int i = n - 1; i >= 0; i--) {
              while (!stack.isEmpty() && heights[stack.peek()] >= heights[i]) stack.pop();
              right[i] = stack.isEmpty() ? n : stack.peek();
              stack.push(i);
          }
          int res = 0;
          for (int i = 0; i < n; i++) {
              res = Math.max(res, heights[i] * (right[i] - left[i] - 1));
          }
          return res;
      }
  }
  ```
- **复杂度：** 暴力 O(n²) / O(1)；单调栈 O(n) / O(n)
- **掌握程度：** 🟡（left[] 模板我给，right[] 对称套用 + 主动质疑 `>=`；还独立把 C++ 单遍版转成 Java）
- **感悟/易错点：**
  - "天花板"思想：矩形高 = 内部最矮柱；每根柱子往左右延伸到**第一个更矮**的为止
  - `>=` 弹掉相等的（两遍版）：相等柱子是"共同体"，不互相挡路才能合并成大矩形；单遍版用 `<` 靠"早期柱子往右兜底"同样算对
  - 面积公式：`heights[i] * (right[i] - left[i] - 1)`，左界没有 -1，右界没有 n
  - ⚠️ Java 数组默认 0，left 要 `Arrays.fill(left,-1)`、right 要 `Arrays.fill(right,n)`（C++ `vector(n,-1)` 转 Java 的坑，REV-38）

### 3. 数组中的第 K 个最大元素（Medium）
- **核心思路：** 三解法：①排序 `Arrays.sort` + `nums[n-k]`（O(n log n)）②小根堆守 k 个（O(n log k)，堆顶=第k大守门员）③快速选择 quickSelect（O(n) 平均，快排只排一边）
- **代码实现（堆版）：**
  ```java
  public int findKthLargest(int[] nums, int k) {
      PriorityQueue<Integer> heap = new PriorityQueue<>();   // 小根堆
      for (int num : nums) {
          heap.offer(num);
          if (heap.size() > k) heap.poll();    // 堆超过 k 个，踢掉最小的
      }
      return heap.peek();                      // 堆顶 = 第 k 大
  }
  ```
- **复杂度：** 排序 O(n log n) / O(1)；堆 O(n log k) / O(k)；quickSelect 平均 O(n) / O(log n)
- **掌握程度：** 🟡（排序版✅独立；堆版🟡骨架；quickSelect🟡理解）
- **感悟/易错点：** 小根堆守 k 个 = "前 k 名榜单"，堆顶是守门员（最弱的）；quickSelect 三步：随机 pivot → 分 big/equal/small → 第 k 大在哪个区就递归哪个区（快排只排一边）；情况2新 k = k - (big+equal)

### 4. 前 K 个高频元素（Medium）
- **核心思路：** ①HashMap 数频率 → 小根堆守 k 个（按频率比）②桶排序 O(n)：`buckets[频率] = 数字列表`，从高到低收集
- **代码实现（堆版）：**
  ```java
  public int[] topKFrequent(int[] nums, int k) {
      Map<Integer, Integer> freq = new HashMap<>();
      for (int num : nums) {
          freq.put(num, freq.getOrDefault(num, 0) + 1);
      }
      PriorityQueue<int[]> heap = new PriorityQueue<>((a, b) -> a[1] - b[1]);  // 按频率的小根堆
      for (Map.Entry<Integer, Integer> e : freq.entrySet()) {
          heap.offer(new int[]{e.getKey(), e.getValue()});
          if (heap.size() > k) heap.poll();
      }
      int[] res = new int[k];
      for (int i = 0; i < k; i++) res[i] = heap.poll()[0];
      return res;
  }
  ```
- **复杂度：** 堆 O(n log k) / O(n)；桶 O(n) / O(n)
- **掌握程度：** 🟡（Step1 HashMap 自己写对，堆部分骨架填空；桶排序 🟡 理解）
- **感悟/易错点：** 堆里存 `int[]{数字,频率}`，比较器按频率 `a[1]-b[1]`；桶排序"频率当下标"= O(n) 关键，前提频率是有界整数；新语法 `merge`/`Collections.max`/`Arrays.setAll`（REV-41）

### 5. 数据流的中位数（Hard）
- **核心思路：** 双堆劈半。左边大根堆（存较小一半，堆顶=左边最大），右边小根堆（存较大一半，堆顶=右边最小）。**左边比右边最多多 1**：奇数时左堆顶=中位数，偶数时 (左顶+右顶)/2.0。
- **代码实现（已 AC）：**
  ```java
  class MedianFinder {
      PriorityQueue<Integer> left = new PriorityQueue<>((a, b) -> b - a);  // 大根堆：较小一半
      PriorityQueue<Integer> right = new PriorityQueue<>();                // 小根堆：较大一半

      public MedianFinder() {}

      public void addNum(int num) {
          if (left.size() == right.size()) {   // 相等 → 最终放左边
              right.offer(num);
              left.offer(right.poll());        // 借右边最小给左边
          } else {                             // 左边多1 → 最终放右边
              left.offer(num);
              right.offer(left.poll());        // 借左边最大给右边
          }
      }

      public double findMedian() {
          if (left.size() > right.size()) return left.peek();
          return (left.peek() + right.peek()) / 2.0;
      }
  }
  ```
- **复杂度：** addNum O(log n) / findMedian O(1)；空间 O(n)
- **掌握程度：** 🟡（核心分支"size相等判断"自己想出，逻辑全对，语法小卡壳）
- **感悟/易错点：**
  - 中位数 = 劈两半：奇数取左堆顶，偶数取 `(左顶+右顶)/2.0`
  - ⭐ `/ 2.0` 必须带 `.0`！`/2` 是整数整除，1.5 会截断成 1
  - 先过一边再借最大/最小 = 保证左堆全部 ≤ 右堆全部
  - 大根堆写法：`new PriorityQueue<>((a,b)->b-a)` 或 `Collections.reverseOrder()`

---

## 三、今日总结

**学到的新模板/技巧：**
- ⭐ 单调栈两大模式：①找右边第一个更大/更小（739 递减栈弹小算答案）②找左右边界（84 递增栈，`>=` 弹掉相等的防挡路）
- ⭐ 堆的"守 k 个"套路：小根堆留 k 个，堆顶=第k大/前k高频守门员（215/347）
- ⭐ 双堆求中位数（295）：左大根堆 + 右小根堆，左边最多多 1，奇数取左顶、偶数取平均
- ⭐ 桶排序（347）：`buckets[频率]` = 数字列表，频率当下标 → O(n)
- 三种排序视角：快排/堆排（比较排序 O(n log n)）/ 计数排序（非比较 O(n)）
- Java 容器方法：PriorityQueue offer/poll/peek；peek=只看不删；Deque 才有 peekFirst/peekLast

**遇到的困难：**
- 信息量偏大，中途"晕"了：单调栈+堆+桶排序+一堆新语法一天涌进来
- 语法短板集中暴露：PriorityQueue 拼写、entrySet 遍历、Arrays.fill/setAll、merge、泛型数组、/2.0 保小数
- 已登记 REV-38/39/40/41 等，后续逐项复习

**遗留问题（需复习）：**
- 🟡 Day 9 五题全部是"骨架/提示下完成"，需各关题解默写一遍升 ✅（739/84/215/347/295）
- 🟡 REV-34 394 独立默写（关题解）
- 🔴 REV-29 437 前缀和默写、REV-30 543/230/124 三件套默写、REV-28 头插法实战（86/92）顺延未做
- 🔴 REV-33 Q1 String 不可变、REV-38/39/40/41 语法待复习
- ⭐ 语法速查见 [[02-Wiki/专题总结/00-Java容器方法速查表]]

**整体感受：** 😐→😊（中途晕过，但 5 题全 AC，进度 60%，Day 9 收官）
