# Day 7 · 二叉树进阶 + 第一周复习

> **日期：** 2026-08-10
> **学习目标：** 二叉树进阶 + 第一周知识体系梳理
> **相关知识页：** [[02-Wiki/专题总结/05-二叉树]] · [[02-Wiki/专题总结/03-数组与矩阵]]

---

## 一、今日模板回顾

### 二叉树构造
```java
// 前序+中序构造
TreeNode root = new TreeNode(preorder[preL]);
int inIdx = indexMap.get(root.val);
int leftSize = inIdx - inL;
root.left = build(...);
root.right = build(...);
```

### 路径前缀和
```java
// 树上前缀和 = 数组前缀和的树形推广
prefixSumCount.put(prefix, prefixSumCount.getOrDefault(prefix, 0) + 1);
```

### 后序遍历 + 全局变量
```java
public int postorder(TreeNode node) {
    int left = postorder(node.left);
    int right = postorder(node.right);
    // 用 left 和 right 更新全局变量
    return node.val + Math.max(left, right);
}
```

---

## 二、做题记录

### 1. BST 第 K 小的元素（Medium）
- **核心思路：** BST 中序遍历 = 从小到大，**数到第 k 个访问的节点就是答案**。就是 98 的中序模板，把"检查递增"换成"数到 k 记答案"。计数器跨递归共享 → 用全局变量
- **代码实现：**
  ```java
  class Solution {
      int count = 0;    // ⭐ 全局计数器：所有递归共享
      int result = 0;   // ⭐ 全局答案

      public int kthSmallest(TreeNode root, int k) {
          inorder(root, k);
          return result;
      }
      void inorder(TreeNode root, int k) {
          if (root == null) return;
          inorder(root.left, k);        // 左
          count++;                      // 中：数一个
          if (count == k) {
              result = root.val;        // ⭐ 数到第 k 个，记答案
              return;
          }
          inorder(root.right, k);       // 右
      }
  }
  ```
- **复杂度：** O(n) / O(h)（h = 树高）
- **掌握程度：** ✅（学员先尝试传参版，修正后改用全局变量版 AC）
- **感悟/易错点：** ① ⭐ **跨递归共享的计数器必须用全局变量**（成员字段），不能传参——Java 基本类型按值传递，每层递归是"复印件"，子调用改了自己那张，父调用不知道（543 同款教训：黑板 vs 草稿纸）；② `count` 记"访问到第几个"，`result` 记"答案"，两个全局，分工清晰；③ 直接孩子/返回值丢失：左子树找到答案要向上传，用全局 result 绕开；④ **230 = 98 中序模板 + 换中间 3 行**：98 检查递增，230 数到 k 记答案；⑤ 函数末尾必须有 return 或声明 void，别滑出函数（编译错误）

### 2. 二叉树的右视图（Medium）
- **核心思路：** BFS 层序模板 + 一行：**每层最后一个节点就是最右节点**（`if(i == len-1) res.add(p.val)`）。右视图 = 每层取最右
- **代码实现：**
  ```java
  class Solution {
      public List<Integer> rightSideView(TreeNode root) {
          Deque<TreeNode> queue = new ArrayDeque<>();
          List<Integer> res = new ArrayList<>();
          if (root == null) return res;
          queue.offer(root);
          while (!queue.isEmpty()) {
              int len = queue.size();              // 固定本层人数
              for (int i = 0; i < len; i++) {
                  TreeNode p = queue.poll();
                  if (i == len - 1) res.add(p.val);  // ⭐ 本层最后一个 = 最右
                  if (p.left != null) queue.offer(p.left);
                  if (p.right != null) queue.offer(p.right);
              }
          }
          return res;
      }
  }
  ```
- **复杂度：** O(n) / O(w)（w = 最大层宽）
- **掌握程度：** ✅（学员独立 AC，BFS 模板完全掌握）
- **感悟/易错点：** ① **199 = 102 层序模板 + 一行**：`if(i == len-1) res.add(p.val)`；② `len-1` 是本层最后一个下标，`i==len-1` 时弹出的就是最右节点；③ ERR-018 五坑全避免（ArrayDeque/offer/固定size/poll/改名）→ BFS 模板已熟练；④ DFS 前序进阶版：先走右子树，每层第一个访问的就是最右（了解即可）

### 3. 二叉树展开为链表（Medium）
- **核心思路：** 把二叉树"压扁"成一条只有右孩子的链，顺序 = 前序。两版解法：①**末尾接上版**（后序：先拉平左右子树，把左链当右链，走到底接上原右链）；②**头插法版**（倒序前序"右→左→根"+ 头插，先访问的被挤到后面，根最后头插在最前 = 前序结果，REV-28）
- **代码实现：**
  ```java
  // ⭐ 解法一：头插法（右左根 + 头插，代码最简，学员找到 C 版并理解原理）
  class Solution {
      TreeNode head = null;            // ⭐ 成员变量代替 C 的 TreeNode**（跨递归共享）
      public void flatten(TreeNode root) {
          dfs(root);
      }
      void dfs(TreeNode root) {
          if (root == null) return;
          dfs(root.right);             // 右（先访问的 → 头插后被挤到最后）
          dfs(root.left);              // 左
          root.left = null;            // 左置空
          root.right = head;           // 头插：当前节点接住已有链表头
          head = root;                 // 更新链表头 = 当前节点
      }
  }

  // ⭐ 解法二：末尾接上版（后序，理解更直观）
  TreeNode flattenTree(TreeNode root) {
      if (root == null) return null;
      TreeNode left = flattenTree(root.left);    // ① 左子树拉平，返回链头
      TreeNode right = flattenTree(root.right);  // ② 右子树拉平，返回链头
      root.left = null;                          // ③ 左置空
      root.right = left;                         // ④ 右 = 左链头
      TreeNode p = root;
      while (p.right != null) p = p.right;       // ⑤ 走到底找链尾
      p.right = right;                           // ⑥ 末尾接上右链头
      return root;
  }
  ```
- **复杂度：** O(n) / O(h)
- **掌握程度：** 🔄（学员找到头插法 C 版并讲清原理，Java 版需独立默写）
- **感悟/易错点：** ① 两版对比：**末尾接上版**要 while 走到底找链尾；**头插法版**不用找链尾（每次只插头部），用"访问顺序反过来"抵消"插在头部"；② 🧠 记忆锚点：**"右左根 + 头插 = 前序结果"**——先访问的（右子树）被挤到最后=前序末尾，根最后头插在最前=前序开头；③ 递归顺序（右左根）≠ 结果顺序（前序根左右），别绕晕；④ Java 没有指针，跨递归共享的 head 用**成员变量**；⑤ 头插法 = REV-28 链表高频套路（206 反转 / 25 组内反转 / 114 同款）

### 4. 从前序与中序遍历构造二叉树（Medium）
- **核心思路：** **前序定根 + 中序切分**。前序第一个 = 根；在中序里找根的位置，左边 = 左子树节点、右边 = 右子树节点；左子树大小 = 根在中序的下标；递归建左右子树。两版：切数组版（直观，O(n) 空间）/ 区间法（传下标不拷贝，O(1) 空间，面试推荐）
- **代码实现：**
  ```java
  // ⭐ 学员版：切数组版（理解透彻，能讲清每行）—— Arrays.copyOfRange [start,end) 左闭右开
  class Solution {
      public TreeNode buildTree(int[] preorder, int[] inorder) {
          int n = preorder.length;
          if (preorder.length == 0) return null;
          TreeNode root = new TreeNode(preorder[0]);
          int leftSize = indexOf(inorder, preorder[0]);       // 左子树大小 = 根在中序下标
          int[] pre1 = Arrays.copyOfRange(preorder, 1, 1 + leftSize);  // 左子树前序
          int[] pre2 = Arrays.copyOfRange(preorder, 1 + leftSize, n);  // 右子树前序
          int[] in1  = Arrays.copyOfRange(inorder, 0, leftSize);       // 左子树中序
          int[] in2  = Arrays.copyOfRange(inorder, leftSize + 1, n);   // 右子树中序
          return new TreeNode(preorder[0], buildTree(pre1, in1), buildTree(pre2, in2));
      }
      private int indexOf(int[] a, int x) {
          for (int i = 0; i < a.length; i++) if (a[i] == x) return i;
          return -1;    // 保证 x 一定在 a 中；编译器要求所有路径有 return
      }
  }

  // ⭐ 区间法（面试推荐）：HashMap 值→中序下标，传 4 个下标，不拷贝数组
  class Solution {
      Map<Integer, Integer> indexMap = new HashMap<>();
      public TreeNode buildTree(int[] preorder, int[] inorder) {
          for (int i = 0; i < inorder.length; i++) indexMap.put(inorder[i], i);
          return build(preorder, inorder, 0, preorder.length-1, 0, inorder.length-1);
      }
      TreeNode build(int[] preorder, int[] inorder, int preL, int preR, int inL, int inR) {
          if (preL > preR) return null;
          int rootVal = preorder[preL];
          TreeNode root = new TreeNode(rootVal);
          int inIdx = indexMap.get(rootVal);         // HashMap O(1) 找根
          int leftSize = inIdx - inL;                // 左子树大小
          root.left = build(preorder, inorder, preL+1, preL+leftSize, inL, inIdx-1);
          root.right = build(preorder, inorder, preL+leftSize+1, preR, inIdx+1, inR);
          return root;
      }
  }
  ```
- **复杂度：** O(n)（每节点建一次，HashMap O(1) 找根）/ O(n)（递归栈 + 切数组 O(n) 空间）或 O(h)（区间法）
- **掌握程度：** ✅（学员切数组版理解透彻，能讲清"中序根左边=左子树"；区间法已讲解）
- **感悟/易错点：** ① ⭐ **两大铁律：前序定根（preorder[0] 是根）+ 中序切分（根左边=左子树、右边=右子树）**；② 左子树大小 = 根在中序的下标（下标从 0 起 = 左边元素个数）；③ 切数组用 `Arrays.copyOfRange(a, start, end)` 左闭右开 `[start,end)`；④ **为什么必须"前序+中序"或"后序+中序"**：前序/后序给"根"，中序给"左右分界"，缺一不可；单独前序不知道左右子树各几个节点；⑤ HashMap 存"值→中序下标"是加速套路（138/146/105 同款）；⑥ 和 108 的关系：108 是"有序数组转 BST"（中点当根），105 是"前中序构造"（前序当根+中序切分），都是"找根→划分→递归"构造类

### 5. 路径总和 III（Medium）
- **核心思路：** 暴力法 = 枚举每个起点往下数（O(n²)）；**前缀和 + 回溯法（O(n)）= 560 数组前缀和的树形版**——路径和 = 后代前缀 - 祖先前缀，找 `map[currSum - target]`。树 DFS 必须"用完要还"（回溯撤销）
- **代码实现：**
  ```java
  // ⭐ 暴力法（学员独立写出，O(n²)）：每个节点当起点，dfs 往下数
  class Solution {
      private int result;
      private int targetSum;
      public int pathSum(TreeNode root, int targetSum) {
          this.targetSum = targetSum;
          this.result = 0;
          traverseAllNodes(root);   // 枚举每个起点
          return result;
      }
      private void traverseAllNodes(TreeNode node) {
          if (node == null) return;
          dfs(node, (long)node.val);        // 以 node 为起点往下数
          traverseAllNodes(node.left);
          traverseAllNodes(node.right);
      }
      private void dfs(TreeNode node, long currentSum) {
          if (currentSum == targetSum) result++;
          if (node.left != null) dfs(node.left, currentSum + node.left.val);
          if (node.right != null) dfs(node.right, currentSum + node.right.val);
      }
  }

  // ⭐ 前缀和 + 回溯法（O(n)，REV-29 重点复习）
  class Solution {
      int res = 0;
      Map<Long, Integer> map = new HashMap<>();
      public int pathSum(TreeNode root, int targetSum) {
          map.put(0L, 1);                          // ⭐ 易错3：初始化前缀和0（代表"从根开始"）
          dfs(root, 0L, targetSum);
          return res;
      }
      void dfs(TreeNode root, long currSum, int targetSum) {
          if (root == null) return;
          currSum += root.val;
          res += map.getOrDefault(currSum - targetSum, 0);  // ⭐ 易错1：先查后记
          map.put(currSum, map.getOrDefault(currSum, 0) + 1);
          dfs(root.left, currSum, targetSum);
          dfs(root.right, currSum, targetSum);
          map.put(currSum, map.get(currSum) - 1);  // ⭐ 易错2：回溯撤销"用完要还"
      }
  }
  ```
- **复杂度：** 暴力 O(n²) / 前缀和 O(n)；空间 O(h) + map
- **掌握程度：** 🔄（暴力法独立 AC；前缀和+回溯 4 易错点需复习，登记 REV-29）
- **感悟/易错点：** ① ⭐ **前缀和法 = 560 的树形版**：`路径和 = 后代前缀 - 祖先前缀`，查 `map[currSum - target]`；② ⭐ **易错1 先查后记**：先 `res += map.get(currSum-target)` 再 `map.put(currSum,...)`，反了会在 target=0 时把自己当"更早前缀"多算空路径；③ ⭐ **易错2 回溯撤销**：从孩子返回后 `map.put(currSum, map.get(currSum)-1)`——map 记录的是"当前路径上的前缀和"，换分支必须撤销，否则结果偏大；"借了要还"；④ ⭐ **易错3 初始化** `map.put(0L,1)`：让"从根到当前节点整条路径"能被查到；⑤ ⭐ **易错4 用 long**：前缀和可能超 int 范围；⑥ 暴力法思路（枚举起点）也是有效心智模型，先暴力再优化是正道

### 6. 二叉树的最近公共祖先（Medium）
- **核心思路：** 后序递归 + 返回值"向上抛"。`dfs(node)` 在 node 子树里找 p 或 q，找到返回它，找不到返回 null。**关键：左右都非空 ⟺ p、q 分居两侧 → 当前节点是 LCA**（因为都挤一边时另一边必为 null，已被递归滤掉）。单边找到就往上抛
- **代码实现：**
  ```java
  // ⭐ 学员独立 AC，把 return left!=null?left:right 拆成两个 if（更好读）
  class Solution {
      public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
          if (root == null || p == root || q == root) return root;  // 基准：空/找到目标
          TreeNode left = lowestCommonAncestor(root.left, p, q);    // 左找
          TreeNode right = lowestCommonAncestor(root.right, p, q);  // 右找
          if (left != null && right != null) return root;  // ⭐ 分居两侧 → 自己是 LCA
          if (right == null) return left;                  // 右边空 → 答案在左边，往上抛
          return right;                                    // 左边空 → 答案在右边
      }
  }
  ```
- **复杂度：** O(n) / O(h)
- **掌握程度：** ✅（学员独立 AC，理解"左右都非空=分居两侧"）
- **感悟/易错点：** ① ⭐ **核心推理：左右都非空 ⟺ p、q 各在一边**——如果 p、q 都挤在一侧，另一侧返回必为 null；所以走到"左右都非空"只剩"分居两侧"一种情况，当前节点就是唯一分叉点 = LCA；② "别扭感"解药：递归已经把"都挤一边"滤掉了；③ 🧠 记忆锚点：**LCA = 从下往上第一个分叉点**（两个人各自往上走，第一次碰面的节点）；④ 单边找到就 `return left/right` 往上抛（答案在那边）；⑤ 基准 `root==p || root==q` 直接返回——目标是祖先时，它自己就是答案；⑥ 和 543/101 同款"后序 + 返回值向上传"

### 7. 二叉树中的最大路径和（Hard）
- **核心思路：** **543 直径"传单臂记双臂"的拓展版**。每个节点算"经过它的最大路径和"（双臂：`node.val + 左单臂 + 右单臂`）全局记 max；递归向上传"单臂最大贡献"（只能往一个方向走）。唯一升级：节点值可能为负，**负的单臂贡献取 0**（`Math.max(...,0)`）——负的臂对路径和是拖累，不如不要
- **代码实现：**
  ```java
  class Solution {
      int max = Integer.MIN_VALUE;   // ⭐ 全局最大（初始为最小，因为可能有负值）
      public int maxPathSum(TreeNode root) {
          maxGain(root);
          return max;
      }
      int maxGain(TreeNode node) {
          if (node == null) return 0;
          int left = Math.max(maxGain(node.left), 0);   // ① 左单臂（负贡献取 0）
          int right = Math.max(maxGain(node.right), 0); // ② 右单臂（负贡献取 0）
          max = Math.max(max, node.val + left + right); // ③ 全局记双臂（经过本节点）
          return node.val + Math.max(left, right);      // ④ 向上传单臂
      }
  }
  ```
- **复杂度：** O(n) / O(h)
- **掌握程度：** 🔄（学员照着题解写对、能讲清"负贡献取 0"，但未独立 AC；登记 REV-30，Day 9 配合 543/230 一起默写）
- **感悟/易错点：** ① ⭐ **和 543 逐行对比**：543 返单臂长度、124 返单臂贡献（带 val）；543 记 `left+right`、124 记 `node.val+left+right`；唯一新东西是 `Math.max(...,0)`；② **为什么取 0**：负的臂对路径和是拖累，不如不要（"断臂求荣"）；③ ⭐ **`max` 初始 `Integer.MIN_VALUE` 不是 0**：节点值可能全为负，用 0 会把最大负值漏掉；④ 🧠 **"后序 + 全局变量"三件套**：543 的 res、230 的 count/result、124 的 max——"一个函数只干一件事，结果放全局"（Day 6-7 最核心套路，可教级）；⑤ **124 = 543 拓展版**，"传单臂记双臂"是二叉树面试最高频套路之一

---

## 三、第一周复习总结

### 模板默写检查

| 模板 | 能否默写 | 备注 |
|------|----------|------|
| 哈希表查找 | ✅ | Day 1 实战 7 题，可教级（🔵）|
| 双指针对撞 | ✅ | Day 1 三数之和/接雨水，可教级（🔵）|
| 快慢指针 | ✅ | Day 4/5 判环/找中点/归并切断，可教级（🔵）|
| 滑动窗口 | ✅ | Day 2 四题（3/438/239/76），可教级（🔵）|
| 前缀和 | 🔄 | 数组版 560 ✅；**树形版 437 待复习（REV-29）** |
| 链表翻转 | ✅ | Day 4/5 头插/三指针/递归，可教级（🔵）|
| 二叉树 DFS | ✅ | Day 6/7 反复实战，"后序+全局变量"三件套（543/230/124）|
| 二叉树 BFS | ✅ | Day 6/7 层序模板，ERR-018 检验通过 |

### 本周知识图谱

```
算法基础
├── 哈希表：空间换时间
├── 双指针：对撞 / 快慢 / 滑动窗口
├── 前缀和：区间和 → 两次相减
├── 数组/矩阵：原地操作 / 边界收缩
├── 链表：虚拟头 / 快慢 / 翻转
└── 二叉树：DFS(前/中/后) / BFS / BST
```

### 错题集

| 题号 | 题目 | 错误原因 | 现在是否能做对 |
|------|------|----------|---------------|
| 230 | BST 第K小 | 跨递归计数器**传参不累加**（按值传递）；左子树返回值丢失、少参数调用 | ✅（改全局变量版）|
| 199 | 右视图(DFS版) | `if(root!=null \|\| ...)` 永远为 true 全加；缺深度参数 | ✅（BFS 版）|
| 114 | 展开为链表 | 递归卡住：不知道返回什么、不会接链尾 | 🔄（REV-28 头插法 Day 8 复习）|
| 437 | 路径总和III | 前缀和+回溯 4 易错：先查后记/回溯撤销/初始化 0L/用 long | 🔄（REV-29 Day 9 复习）|
| 124 | 最大路径和 | 负贡献取 0 不熟；全局 max 初始值 | 🔄（REV-30 Day 9 复习）|

### 第二周展望
- Day 8：图论 + 栈
- Day 9：单调栈 + 堆
- Day 10：回溯算法
- Day 11：二分查找
- Day 12：动态规划入门 + 贪心
- Day 13：进阶动态规划
- Day 14：技巧 + 全局复习

---

## 四、今日总结

**学到的新模板/技巧：**
- ⭐ **"后序 + 全局变量"三件套**（Day 6-7 最核心套路）：543 传单臂记双臂 / 230 全局 count+result / 124 全局 max，一个骨架通吃
- 前中序构造：**前序定根 + 中序切分**，左子树大小 = 根在中序下标；区间法 + HashMap O(1) 找根
- 树上前缀和（437）= 560 数组前缀和的树形版：`路径和 = 后代前缀 - 祖先前缀`
- LCA（236）= 从下往上第一个分叉点：**左右都非空 ⟺ p、q 分居两侧**
- 头插法（114）：**"右左根 + 头插 = 前序结果"**（先访问的被挤到后面）

**遇到的困难：**
- 跨递归累加要用全局变量，不能传参（230，Java 按值传递）
- 递归返回值设计：返回什么、怎么接（114 展开）
- 437 前缀和+回溯 4 易错点：先查后记 / 回溯撤销 / 初始化 / 用 long
- 124 负贡献取 0 的"为什么"（负的臂是拖累）

**遗留问题（需复习）：**
- REV-28（头插法+虚拟节点）→ **Day 8** 配合 86 分隔链表 / 92 反转区间 II
- REV-29（437 前缀和+回溯）→ **Day 9** 配合 560 数组版对照
- REV-30（124 后序全局套路）→ **Day 9** 配合 543/230 三题一起默写
- **114 复写**（Day 8 开始前）
- REV-19/20/21/22/23/24 + ERR-017（Day 5 遗留，含 23 递归分治回炉）

**整体感受：** 😊 Day 7 收官 7/7，第一周（Day 1-7）全部完成，进度 50%！二叉树框架已打通，下周进图论+栈
