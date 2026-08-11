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
- **核心思路：** **多源 BFS**。所有烂橘子（2）第 0 分钟同时入队，一层层向外扩散（每层 = 1 分钟）。准备阶段数好新鲜橘子 fresh；扩散时邻居是 1 → 变 2、fresh--、入队。**关键是"多源"**：所有起点站在第 0 分钟同一条起跑线，只从一个起点开始会算错分钟（其他烂橘第 0 分钟的传染被算成更晚）。`while (!q.isEmpty() && fresh > 0)` 防空转；`fresh == 0 ? minutes : -1` 兜底有烂不到的新鲜橘子
- **代码实现：**
  ```java
  class Solution {
      public int orangesRotting(int[][] grid) {
          Deque<int[]> q = new ArrayDeque<>();
          int fresh = 0;
          for (int i = 0; i < grid.length; i++)
              for (int j = 0; j < grid[0].length; j++) {
                  if (grid[i][j] == 1) fresh++;
                  if (grid[i][j] == 2) q.offer(new int[]{i, j});   // 所有起点入队（多源）
              }
          int minutes = 0;
          while (!q.isEmpty() && fresh > 0) {   // ⭐ 还有新鲜橘子才继续（防空转）
              minutes++;
              int size = q.size();              // ⭐ 固定本层人数（层序模板）
              for (int k = 0; k < size; k++) {
                  int[] cur = q.poll();
                  int i = cur[0], j = cur[1];
                  // 四方向：邻居是 1 → 变 2、fresh--、入队（学员写的 4 个 if，可改方向数组）
                  if (i - 1 >= 0 && grid[i - 1][j] == 1) { grid[i - 1][j] = 2; fresh--; q.offer(new int[]{i - 1, j}); }
                  if (i + 1 < grid.length && grid[i + 1][j] == 1) { grid[i + 1][j] = 2; fresh--; q.offer(new int[]{i + 1, j}); }
                  if (j - 1 >= 0 && grid[i][j - 1] == 1) { grid[i][j - 1] = 2; fresh--; q.offer(new int[]{i, j - 1}); }
                  if (j + 1 < grid[0].length && grid[i][j + 1] == 1) { grid[i][j + 1] = 2; fresh--; q.offer(new int[]{i, j + 1}); }
              }
          }
          return fresh == 0 ? minutes : -1;
      }
  }
  ```
- **复杂度：** O(m·n) / O(m·n)（队列最大存所有格子）
- **掌握程度：** ✅（学员在思路框架下独立写出四方向扩散，能讲清"多源 = 所有起点同时传染"）
- **感悟/易错点：** ① ⭐ **多源 BFS = 所有起点先全部入队，再照常层序**（核心：同一条起跑线）；② `fresh > 0` 防空转（全烂后继续扩散会多算分钟）；③ `fresh == 0 ? minutes : -1`（有永远烂不到的橘子返回 -1）；④ 层序模板三件套：固定 size / poll / offer 邻居；⑤ **方向数组进阶**（`di/dj` + for 循环代替 4 个 if）：`int[] di={-1,1,0,0}; int[] dj={0,0,-1,1}`，200/994/79/130 通用，改数组即可换方向

### 3. 课程表（Medium）
- **核心思路：** **拓扑排序（Kahn 算法 / BFS 版）判断图有没有环**。能学完全部课 = 无环；有环（先有鸡还是先有蛋）学不完。核心：①统计每门课入度（前置数）②入度为 0 的课先学（入队/入栈）③弹出后把它解锁的课入度 -1，归零的课入队 ④总共学到的课数 == numCourses ⟺ 无环
- **代码实现：**
  ```java
  // ⭐ 学员初版（扫 prerequisites，O(n·m)）+ 标准写法（邻接表，O(n+m)）
  public boolean canFinish(int numCourses, int[][] prerequisites) {
      int[] indegree = new int[numCourses];
      Deque<Integer> s = new ArrayDeque<>();
      for (int[] p : prerequisites) indegree[p[0]]++;        // 统计入度（前置数）
      for (int i = 0; i < numCourses; i++) if (indegree[i] == 0) s.push(i);  // 无前置先学
      int cnt = 0;
      while (!s.isEmpty()) {
          int p = s.pop();                                    // ⭐ pop 不是 pull
          cnt++;
          for (int[] pre : prerequisites) {
              if (pre[1] == p) {                             // p 是这门课的前置
                  indegree[pre[0]]--;
                  if (indegree[pre[0]] == 0) s.push(pre[0]); // ⭐ 归零才入栈（否则重复入栈死循环）
              }
          }
      }
      return cnt == numCourses;
  }

  // ⭐ 标准写法（邻接表，面试推荐，210 姐妹题直接复用）
  List<List<Integer>> graph = new ArrayList<>();
  for (int i = 0; i < numCourses; i++) graph.add(new ArrayList<>());  // 先备好 n 张空纸条
  for (int[] p : prerequisites) {
      graph.get(p[1]).add(p[0]);   // p[1] 学完 → 解锁 p[0]（get 取抽屉，add 写名字）
      indegree[p[0]]++;
  }
  // BFS 里：for (int next : graph.get(cur)) { indegree[next]--; if (indegree[next]==0) q.offer(next); }
  ```
- **复杂度：** 扫 prerequisites 版 O(n·m) / 邻接表版 O(n+m)；空间 O(n+m)
- **掌握程度：** ✅（学员理解 Kahn 算法，自己修正 pull→pop 和"归零才入栈"两个 bug）
- **感悟/易错点：** ① ⭐ **Kahn 判环 = 学到的课数 == 总数**（有环 → 剩余课永远没法学）；② ⭐ **"归零才入栈"**：入度减 1 后只有恰好归零才入队，否则重复入队死循环；③ `pop` 不是 `pull`（Deque 栈操作 push/pop）；④ **邻接表 vs 邻接矩阵**：邻接表 = 每人一张"朋友名单"（List<List<Integer>>，n 个抽屉各装一张纸条），`graph.get(a).add(b)` = 打开 a 的抽屉写 b；邻接矩阵 = `boolean[n][n]` 表格；稀疏图用表，稠密图用矩阵；⑤ 邻接表建图时**必须先 add 空 List 初始化**，否则 get 到 null 空指针；⑥ 拓扑排序是有向图判环的通用工具（207/210/可能 310）

### 4. 实现 Trie（Medium）
- **核心思路：** **前缀树（Trie）**，专治字符串前缀匹配。每个节点 = `children[26]`（a~z 各一个孩子）+ `isEnd`（是不是某单词结尾）。核心操作全是"沿树走"：insert 没路就建、走到底打 isEnd；search 要走到 + isEnd；startsWith 只要走到。**节点不存字符**——是什么字母由"爸爸的 children 下标"决定（门牌号写在门框上，不写在门里）
- **代码实现：**
  ```java
  class Trie {
      class TrieNode {
          TrieNode[] children = new TrieNode[26];  // 26 格信箱架，格子默认 null（空）
          boolean isEnd;
      }
      TrieNode root;
      public Trie() { root = new TrieNode(); }

      public void insert(String word) {
          TrieNode node = root;
          for (char c : word.toCharArray()) {
              int idx = c - 'a';
              if (node.children[idx] == null) node.children[idx] = new TrieNode();  // 格子空就放信
              node = node.children[idx];   // 往下走
          }
          node.isEnd = true;   // 走到底打结尾标记
      }

      public boolean search(String word) {
          TrieNode node = root;
          for (char c : word.toCharArray()) {
              int idx = c - 'a';
              if (node.children[idx] == null) return false;  // 走不通 → 前缀不存在
              node = node.children[idx];
          }
          return node.isEnd;   // ⭐ search 要踩在单词终点上
      }

      public boolean startsWith(String prefix) {
          TrieNode node = root;
          for (char c : prefix.toCharArray()) {
              int idx = c - 'a';
              if (node.children[idx] == null) return false;
              node = node.children[idx];
          }
          return true;   // ⭐ startsWith 只要路走得通
      }
  }
  ```
- **复杂度：** insert/search/startsWith 均 O(单词长度)；空间 O(总字符数 × 26)
- **掌握程度：** ✅（学员独立写出全部 4 个方法，注释清晰，理解 search 与 startsWith 就差 isEnd 一行）
- **感悟/易错点：** ① ⭐ **search vs startsWith 就差一行**：search 要 `node.isEnd`（完整单词），startsWith 只要走到（前缀存在）；② **数组默认值**：`new TrieNode[26]` 给 26 个格子，引用默认 null（"26 格信箱架，格子空着"），所以访问前要 `if (== null)`；③ `idx = c - 'a'`：字符转 0~25 下标（'a'→0）；④ `toCharArray()`：String → char[]，for-each 逐字符；⑤ **节点不存字符**，靠 children 下标定位；⑥ Trie 优势：查前缀 O(长度) 与字典大小无关，自动补全/拼写检查/IP 路由的基础

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
