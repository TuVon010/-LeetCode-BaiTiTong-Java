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
- **核心思路：** 栈的经典应用。左括号入栈，右括号和栈顶配对：栈顶匹配则弹出，不匹配或栈空则 false；遍历完栈必须为空。**后进先出 = 最近配对的括号在栈顶**（叠盘子，最后放的先拿）
- **代码实现：**
  ```java
  class Solution {
      public boolean isValid(String s) {
          Deque<Character> stack = new ArrayDeque<>();
          for (char c : s.toCharArray()) {
              switch (c) {
                  case '(': case '[': case '{':   // 左括号三兄弟共用一段（叠 case）
                      stack.push(c);
                      break;
                  case ')':
                      if (stack.isEmpty() || stack.pop() != '(') return false;
                      break;                      // ⭐ 每个 case 都要 break，否则穿透（ERR-020）
                  case ']':
                      if (stack.isEmpty() || stack.pop() != '[') return false;
                      break;
                  case '}':
                      if (stack.isEmpty() || stack.pop() != '{') return false;
                      break;
                  default:
                      break;
              }
          }
          return stack.isEmpty();   // 栈空 = 全配上
      }
  }
  ```
- **复杂度：** O(n) / O(n)
- **掌握程度：** ✅（学员从"switch 不会用"到独立修好 break + isEmpty 两个坑，还写出了更优的 Map 版）
- **感悟/易错点：** ① ⭐ **switch 缺 break 会穿透**（ERR-020）：case 执行完没 break 会滑到下一个 case，合法输入被误判（`"()"` 穿透到 `]` 又 pop 空栈 → false）；口诀"case 带把锁（break），不锁滑到下一家"；② ⭐ **pop 前先问 isEmpty**：空栈 pop 抛 NoSuchElementException 崩溃，且空栈遇右括号本就该 false（短路：`isEmpty() || pop() != x`）；③ **Map 版更推荐**（避免 switch 的坑）：`Map.of(')','(',']','[','}','{')` + `if (stack.isEmpty() || stack.pop() != map.get(c)) return false;`；④ 左括号三兄弟可"叠 case"共享代码；⑤ `return stack.isEmpty()` 代替 `if(!isEmpty) return false; return true;`；⑥ 🧠 **栈操作三连问（REV-33 补强）**：写 `pop()` 前问"空吗？"、写 `peek()` 前问"空吗？"、`push()` 永不崩不用问

### 6. 最小栈（Medium）
- **核心思路：** **辅助栈同步记录"每一步的最小值"**。数据栈 stack 存数据，辅助栈 minStack 存每一步的当前最小值（`Math.min(minStack.peek(), val)`）。pop 时两栈一起弹 → minStack 顶自动回到上一个最小值，O(1)。**难点就是"想到要创建两个栈"**——记位置的思路走不通（pop 后无法 O(1) 找回上一个最小值），辅助栈保存了历史最小值档案
- **代码实现：**
  ```java
  class MinStack {
      Deque<Integer> stack;
      Deque<Integer> minStack;
      public MinStack() {
          stack = new ArrayDeque<>();
          minStack = new ArrayDeque<>();
      }
      public void push(int val) {
          stack.push(val);
          int min = minStack.isEmpty() ? val : Math.min(minStack.peek(), val);
          minStack.push(min);   // ⭐ 每一步都记下当前最小值
      }
      public void pop() {
          stack.pop();
          minStack.pop();       // ⭐ 两栈一起弹，自动回到上一个最小值
      }
      public int top() {
          return stack.peek();
      }
      public int getMin() {
          return minStack.peek();   // 辅助栈顶 = 当前最小
      }
  }
  ```
- **复杂度：** 全部 O(1)；空间 O(n)（辅助栈）
- **掌握程度：** ✅（学员理解辅助栈思想，独立写出 AC；学员先试"记位置"思路，被 pop 失效点卡住后悟到辅助栈）
- **感悟/易错点：** ① ⭐ **难点 = 想到两个栈**：要 O(1) 取最值 + 可回退 → "再开一个栈记历史"（空间换时间）；② **记位置走不通**：min_location 指向的下标 pop 掉后无法 O(1) 找回下一个最小值，必须重扫 O(n)；③ 辅助栈存"每一步的最小值本身"，pop 一起弹自动还原；④ 类比：min_location 只记现任冠军，辅助栈是"每届冠军档案"；⑤ 通用套路：**主结构 + 辅助结构同步维护**（155 辅助栈 / 146 辅助双向链表 / 232 双栈队列）

### 7. 字符串解码（Medium）
- **核心思路：** **双栈解嵌套**。数字栈存每层重复次数，字符串栈存每层之前拼好的字符串。`[` = 存档（压栈 + 重置），`]` = 读档解压（弹次数、弹之前字符串、重复拼回）。嵌套天然"内层先解压外层后解压" = 后进先出 = 栈（和 20 括号配对同源）
- **代码实现：**
  ```java
  class Solution {
      public String decodeString(String s) {
          Deque<Integer> numStack = new ArrayDeque<>();
          Deque<String> strStack = new ArrayDeque<>();
          int num = 0;                              // 累积当前数字
          StringBuilder cur = new StringBuilder();  // 累积当前段字符串（白板）
          for (char c : s.toCharArray()) {
              if (Character.isDigit(c)) {
                  num = num * 10 + (c - '0');       // 字符数字 → 真数字 + 拼多位
              } else if (c == '[') {
                  numStack.push(num);                // 存档数字
                  strStack.push(cur.toString());     // 存档字符串
                  num = 0;                           // 重置，准备下一层
                  cur = new StringBuilder();
              } else if (c == ']') {
                  int repeat = numStack.pop();       // 读档：重复次数
                  String prev = strStack.pop();      // 读档：之前拼好的
                  StringBuilder temp = new StringBuilder();
                  for (int i = 0; i < repeat; i++) temp.append(cur);
                  cur = new StringBuilder(prev).append(temp);   // prev + 重复结果
              } else {
                  cur.append(c);                     // 字母直接写白板
              }
          }
          return cur.toString();
      }
  }
  ```
- **复杂度：** O(输出长度) / O(嵌套深度)
- **掌握程度：** 🔄（学员诚实承认"复制了题解/老师骨架，不是独立写的"；双栈思想 + StringBuilder/Character 语法短板已登记 REV-33，整题登记 REV-34 待独立重写）
- **感悟/易错点：** ① ⭐ **`[` 存档、`]` 读档**：`[` 把当前数字和字符串压栈再重置，`]` 弹出重复次数和之前字符串重复拼回——嵌套 = 层层的存档/读档，内层先解压；② **StringBuilder** = 可变字符串"白板"，拼接多次用 `append` 而非 `+`（String 不可变，每次 + 新建对象很慢），最后 `toString()` 交卷；③ **`c - '0'`** = 字符数字转真数字（`'5'-'0'=5`），`num*10+digit` 拼多位数字（读 1 再读 2 → 12）；④ `Character.isDigit(c)` 判断是不是数字字符；⑤ 和 20 括号配对同源：左括号入栈右括号配对，只是这里入的是"数字+字符串"两样东西

---

## 三、今日总结

**学到的新模板/技巧：**
-

**遇到的困难：**
-

**遗留问题（需复习）：**
-

**整体感受：** 😊 😐 😢
