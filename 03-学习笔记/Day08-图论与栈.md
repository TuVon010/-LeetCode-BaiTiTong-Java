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
- **核心思路：** 沉岛法（学员起名"插旗法"）。遍历每个格子，遇到 '1' 计数+1，然后 DFS 把整个岛沉掉（变 '0'）。**核心：沉岛 = 标记已访问，防止回头路把同一个岛重复计数**（不沉岛，外层循环再遇到同岛格子会重复 +1）。网格 DFS = 二叉树递归的"四方向版"（二叉树两个分支 → 网格四个方向）
- **代码实现：**
  ```java
  class Solution {
      public int numIslands(char[][] grid) {
          int count = 0;
          for (int row = 0; row < grid.length; row++) {
              for (int col = 0; col < grid[0].length; col++) {
                  if (grid[row][col] == '1') {
                      count++;
                      dfs(grid, row, col);      // 沉掉整个岛
                  }
              }
          }
          return count;
      }
      void dfs(char[][] grid, int i, int j) {
          if (i < 0 || j < 0 || i >= grid.length || j >= grid[0].length || grid[i][j] != '1') {
              return;                            // 刹车：越界 / 不是 '1'
          }
          grid[i][j] = '0';                      // 沉岛 = 标记已访问（防重复）
          dfs(grid, i - 1, j);                   // 上下左右四方向
          dfs(grid, i + 1, j);
          dfs(grid, i, j - 1);
          dfs(grid, i, j + 1);
      }
  }
  ```
- **复杂度：** O(m·n)（每个格子至多访问一次）/ O(m·n)（最坏全为 '1' 时递归栈深）
- **掌握程度：** ✅（学员在提示下写出完整四方向 DFS，能讲清"沉岛防重复"原理）
- **感悟/易错点：** ① ⭐ **沉岛 = visited**：遍历过的节点必须标记，否则回头路把答案数重（图论通用规律，二叉树天然分左右不用标记，网格要）；② **刹车条件一个都不能少**：越界（上下左右四个） + 不是 '1'，漏一个就栈溢出或重复；③ **网格 DFS = 二叉树递归四方向版**：同一个骨架，二叉树 `node.left/right` → 网格 `(i±1,j)/(i,j±1)`；④ 主函数双重循环逐个格子找起点

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
