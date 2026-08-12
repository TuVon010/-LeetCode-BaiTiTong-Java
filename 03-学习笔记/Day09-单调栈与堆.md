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
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 2. 柱状图中最大的矩形（Hard）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

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
-

**遇到的困难：**
-

**遗留问题（需复习）：**
-

**整体感受：** 😊 😐 😢
