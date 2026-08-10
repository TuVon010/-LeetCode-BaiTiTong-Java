# Day 7 · 二叉树进阶 + 第一周复习

> **日期：** 2026-__-__
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
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 6. 二叉树的最近公共祖先（Medium）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

### 7. 二叉树中的最大路径和（Hard）
- **核心思路：**
- **代码实现：**
- **复杂度：** O(__) / O(__)
- **掌握程度：** ✅ 🔄 ❌
- **感悟/易错点：**

---

## 三、第一周复习总结

### 模板默写检查

| 模板 | 能否默写 | 备注 |
|------|----------|------|
| 哈希表查找 | ✅ ❌ | |
| 双指针对撞 | ✅ ❌ | |
| 快慢指针 | ✅ ❌ | |
| 滑动窗口 | ✅ ❌ | |
| 前缀和 | ✅ ❌ | |
| 链表翻转 | ✅ ❌ | |
| 二叉树 DFS | ✅ ❌ | |
| 二叉树 BFS | ✅ ❌ | |

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
| | | | ✅ ❌ |

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
-

**遇到的困难：**
-

**遗留问题（需复习）：**
-

**整体感受：** 😊 😐 😢
