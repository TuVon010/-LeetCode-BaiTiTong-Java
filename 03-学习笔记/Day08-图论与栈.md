# Day 8 · 图论与栈

> **日期：** 2026-__-__
> **学习目标：** 图的遍历（DFS/BFS）与栈的基础应用
> **相关知识页：** [[02-Wiki/专题总结/07-图论]] · [[02-Wiki/专题总结/06-栈与堆]]

---

## 一、今日模板回顾

### 网格 DFS（沉岛法）
```java
void dfs(int i, int j, char[][] grid) {
    if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] != '1') {
        return;
    }
    grid[i][j] = '0';
    dfs(i + 1, j, grid);
    dfs(i - 1, j, grid);
    dfs(i, j + 1, grid);
    dfs(i, j - 1, grid);
}
```

### 多源 BFS
```java
Deque<int[]> q = new ArrayDeque<>();
// 将所有起点入队
int minutes = 0;
while (!q.isEmpty() && fresh > 0) {
    minutes++;
    int size = q.size();
    for (int k = 0; k < size; k++) {
        // 层序遍历
    }
}
```

### 拓扑排序（Kahn 算法）
```java
Deque<Integer> q = new ArrayDeque<>();
for (int i = 0; i < n; i++) {
    if (indegree[i] == 0) q.addLast(i);
}
while (!q.isEmpty()) {
    int node = q.pollFirst();
    for (int neighbor : graph[node]) {
        indegree[neighbor]--;
        if (indegree[neighbor] == 0) {
            q.addLast(neighbor);
        }
    }
}
```

---

## 二、做题记录

### 1. 岛屿数量（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 2. 腐烂的橘子（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 3. 课程表（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 4. 实现 Trie（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 5. 有效的括号（Easy）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 6. 最小栈（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 7. 字符串解码（Medium）
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
