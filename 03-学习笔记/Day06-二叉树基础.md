# Day 6 · 二叉树基础

> **日期：** 2026-08-09
> **学习目标：** 二叉树基础——DFS 与 BFS 两大框架
> **相关知识页：** [[02-Wiki/专题总结/05-二叉树]] · [[02-Wiki/专题总结/08-回溯算法]] · [[02-Wiki/专题总结/00-Java容器方法速查表]]

---

## 一、今日模板回顾

### DFS（递归框架）
```java
public void dfs(TreeNode node) {
    if (node == null) {
        return;
    }
    // 前序：处理 node
    dfs(node.left);
    // 中序：处理 node
    dfs(node.right);
    // 后序：处理 node
}
```

### BFS（层序框架）
```java
import java.util.Deque;
import java.util.ArrayDeque;
Deque<TreeNode> q = new ArrayDeque<>();
q.add(root);
while (!q.isEmpty()) {
    int size = q.size();
    for (int i = 0; i < size; i++) {
        TreeNode node = q.pollFirst();
        // 处理 node
        if (node.left != null) q.add(node.left);
        if (node.right != null) q.add(node.right);
    }
}
```

---

## 二、做题记录

### 1. 二叉树的中序遍历（Easy）
- **核心思路：** 中序 = 左 → 根 → 右。递归版：先左再根后右。迭代版用栈模拟递归：**一路向左压栈 → 弹一个访问（根）→ 拐向右**。栈的作用是手动存"往左走时路过的节点"（REV-25 栈）
- **代码实现：**
  ```java
  // 迭代版（学员独立写出 ✅）
  List<Integer> res = new ArrayList<>();
  public List<Integer> inorderTraversal(TreeNode root) {
      Deque<TreeNode> stack = new ArrayDeque<>();   // 栈用 Deque 实现（push/pop）
      TreeNode p = root;
      while (p != null || !stack.isEmpty()) {        // 节点存在 或 栈非空
          if (p != null) {
              stack.push(p);      // ① 一路向左压栈
              p = p.left;
          } else {
              p = stack.pop();    // ② 弹出最左的
              res.add(p.val);     // ③ 访问它（此时它是"根"）
              p = p.right;        // ④ 拐向右子树
          }
      }
      return res;
  }
  ```
- **复杂度：** O(n) / O(h)
- **掌握程度：** ✅（学员独立写出，逻辑正确）
- **感悟/易错点：** ① **记忆锚点：一路向左压栈 → 弹一个访问 → 拐向右**；② `!stack.isEmpty()` 比 `stack.size() > 0` 更规范；③ 中序遍历迭代 = 手写栈模拟递归，这题也为 98 验证 BST / 230 第K小 打基础；④ 栈 = Deque 的 `push/pop/peek`（都是操作队头 First 端，见 REV-25 速查表）

### 2. 二叉树的最大深度（Easy）
- **核心思路：** 两种方法。①递归 DFS：`深度 = 1 + max(左子树深度, 右子树深度)`，空节点深度 0。②BFS 层序：一层层数，每层处理完 depth++。两种都是 O(n)，递归栈 O(h)，BFS 队列 O(w)
- **代码实现：**
  ```java
  // ========== 方法一：递归 DFS（学员独立 AC ✅）==========
  public int maxDepth(TreeNode root) {
      if (root == null) return 0;                        // 基准：空节点深度 0
      int leftDepth = maxDepth(root.left);               // 递归：左子树深度
      int rightDepth = maxDepth(root.right);             // 递归：右子树深度
      return leftDepth > rightDepth ? (leftDepth + 1) : (rightDepth + 1);
      // ↑ 等价于 1 + Math.max(leftDepth, rightDepth)，面试更推荐 Math.max 版
  }

  // ========== 方法二：BFS 层序（学员尝试版，修正 5 个错）==========
  // ⭐ 学员易错点全标注在这份代码里，复习时逐条对照
  public int maxDepth(TreeNode root) {
      if (root == null) return 0;
      // ❌ 学员错误1：Deque<TreeNode> deque = new ArrayList<>();
      //    ArrayList 实现 List 不是 Deque！编译错误。
      //    ✅ Deque 的常用实现是 ArrayDeque 或 LinkedList
      Deque<TreeNode> queue = new ArrayDeque<>();

      queue.offer(root);              // ✅ 入队用 offer（队尾进）
      //  ❌ 学员错误2：写了 deque.add(root)，add 是抛异常版，offer 是返回特殊值版，刷题统一 offer

      int depth = 0;

      while (!queue.isEmpty()) {      // ✅ 判空用 isEmpty()
          // ❌ 学员错误3：for (int i = 0; i < queue.size(); i++)
          //    循环里入队会让 queue.size() 一直变 → 当前层人数失控
          //    ✅ 必须先把"当前层节点数"固定下来
          int size = queue.size();
          for (int i = 0; i < size; i++) {   // 处理这一整层
              // ❌ 学员错误4：p = deque.peekFirst(); 只"看"不"弹出"！
              //    节点永远留在队里 → 死循环
              //    ✅ 要取出并移除队头，用 poll()
              TreeNode node = queue.poll();
              if (node.left != null) queue.offer(node.left);    // ✅ 左孩子入队
              // ❌ 学员错误5：depth.push(node.right) —— depth 是 int 没有 push 方法！
              //    ✅ 应该是 queue.offer(node.right)，右孩子入队
              //    （这是复制粘贴没改名的坑，ERR-016）
              if (node.right != null) queue.offer(node.right);
          }
          depth++;                     // 一层处理完，深度 +1
      }
      return depth;
  }
  ```
- **复杂度：** O(n) / O(h)（递归）或 O(w)（BFS，w=最大层宽）
- **掌握程度：** ✅（递归独立 AC；BFS 思路对，修正 5 个细节后理解）
- **感悟/易错点：** ① **BFS 精髓：每轮 while = 处理一整层**，先 `int size = queue.size()` 固定人数，for 循环弹完这一层 + 把下一层孩子入队，循环结束 depth++；② Deque 当队列用统一 `offer/poll/peekFirst`，别混用 add/push/peekFirst（REV-25）；③ `peekFirst()` 只看不弹，要弹出用 `poll()`；④ `ArrayList` 不是 Deque，用 `ArrayDeque`；⑤ for 循环条件里的 size 必须在循环前固定；⑥ int 类型没有 push 方法，复制粘贴必须改名（ERR-016）

### 3. 翻转二叉树（Easy）
- **核心思路：** 交换每个节点的左右孩子（temp 变量）。前序/后序都行，只是"交换"发生在递归前还是后。学员写后序版（先递归翻子树再交换），逻辑更稳。Homebrew 作者被挂的那道题
- **代码实现：**
  ```java
  // 后序版（学员独立写出 ✅）
  class Solution {
      public TreeNode invertTree(TreeNode root) {
          if (root == null) return null;
          TreeNode left = invertTree(root.left);     // ① 先翻左子树
          TreeNode right = invertTree(root.right);   // ② 再翻右子树
          root.left = right;                         // ③ 交换
          root.right = left;
          return root;
      }
      // 前序版（交换提前）：
      // if (root == null) return null;
      // TreeNode temp = root.left; root.left = root.right; root.right = temp;
      // invertTree(root.left); invertTree(root.right); return root;
  }
  ```
- **复杂度：** O(n) / O(h)
- **掌握程度：** ✅（学员独立写出后序版）
- **感悟/易错点：** ① **核心就一句：翻转 = 交换每个节点的左右孩子（用 temp）**；② 前序/后序只差"交换时机"，都能 AC；③ 空节点基准 `return null`；④ 这题证明了递归骨架（基准 → 递归子问题 → 处理自己）已彻底掌握

### 4. 对称二叉树（Easy）
- **核心思路：** 判断一棵树是否镜像对称 = 检查它的左子树和右子树是否互为镜像（**两棵树递归**）。三个条件：①根值相等 ②外侧比外侧（`p.left ↔ q.right`）③内侧比内侧（`p.right ↔ q.left`）。基准：都空→true、一个空→false、值不等→false。层序遍历正反比较不可靠（缺节点的树会误判），递归交叉最稳
- **代码实现：**
  ```java
  // ⭐ 学员看题解后理解写出，核心是"交叉比"（REV-26）
  class Solution {
      public boolean isSymmetric(TreeNode root) {
          if (root == null) return true;
          return check(root.left, root.right);   // 检查左右子树是否镜像
      }
      boolean check(TreeNode p, TreeNode q) {
          if (p == null && q == null) return true;    // ① 都空 → 对称
          if (p == null || q == null) return false;   // ② 一个空 → 不对称
          if (p.val != q.val) return false;           // ③ 值不等 → 不对称
          return check(p.left, q.right) && check(p.right, q.left);
          //     ↑ 外侧比外侧           ↑ 内侧比内侧（镜像 = 交叉比）
      }
  }
  ```
- **复杂度：** O(n) / O(h)
- **掌握程度：** ✅（看题解后理解写出，能解释为什么交叉；交叉比已登记 REV-26）
- **感悟/易错点：** ① **对称 = 交叉比**：`check(p.left, q.right)`（外侧 vs 外侧）+ `check(p.right, q.left)`（内侧 vs 内侧）；② 类比"照镜子"：你举右手，镜中人是左手，所以 `p.right` 要比 `q.left`；③ 千万别写成 `check(p.left, p.right)`（一棵树自己跟自己比，错）；④ 层序正反比较只对满二叉树碰巧对，缺节点会误判；⑤ 这是"两棵树递归"的入门经典（后面 100 相同的树、572 子树 同款思路）

### 5. 二叉树的直径（Easy）
- **核心思路：** 最长路径一定"拐过"某个节点 = 左子树深度 + 右子树深度。直径 = 所有节点的 `左深+右深` 最大值。**关键分工："向上传单臂，全局记双臂"**——`depth()` 返回最长单臂（给父节点用），`res` 记双臂之和（直径候选）
- **代码实现：**
  ```java
  // ⭐ 学员理解了"拐点"思路，从"一个函数想干两件事"的误区纠正过来
  class Solution {
      int res = 0;   // 全局直径
      public int diameterOfBinaryTree(TreeNode root) {
          depth(root);
          return res;
      }
      int depth(TreeNode root) {
          if (root == null) return 0;
          int left = depth(root.left);      // 左臂长度
          int right = depth(root.right);    // 右臂长度
          res = Math.max(res, left + right);      // ⭐ 全局记双臂（直径候选）
          return Math.max(left, right) + 1;       // ⭐ 向上传单臂（父节点只要最长那只手）
      }
  }
  ```
- **复杂度：** O(n) / O(h)
- **掌握程度：** ✅（理解"拐点 + 传单臂记双臂"，自己走通例子）
- **感悟/易错点：** ① 核心：直径 = `左深+右深`，在递归求深度时顺手更新；② ⭐ **一个函数只干一件事**：`depth` 返回单臂（向上传），`res` 记双臂（全局存），别混在一个返回值里；③ 注意这里是**边数**不是节点数（104 深度是节点数，543 直径是边数，`left+right` 不加额外 1）；④ 和 104 的关系：**就是 maxDepth + 一行**；⑤ 遍历一遍所有节点，每个都算"左臂+右臂"，取最大

### 6. 二叉树的层序遍历（Medium）
- **核心思路：** BFS 层序模板标准形态。`while(!empty)` 每轮处理一整层；`len` 固定当前层节点数；`poll` 弹出节点收集到 tempList；`offer` 把左右孩子入队（下一层）；`res.add(本层)`。每轮 while = 一层
- **代码实现：**
  ```java
  // ⭐ 学员独立写出，ERR-018 的 5 个坑全对（模板级代码，值得背）
  class Solution {
      public List<List<Integer>> levelOrder(TreeNode root) {
          List<List<Integer>> res = new ArrayList<>();
          if (root == null) return res;
          Deque<TreeNode> queue = new ArrayDeque<>();   // ✅ 泛型写全
          queue.offer(root);                            // ✅ offer 入队
          while (!queue.isEmpty()) {
              int len = queue.size();                   // ✅ 固定当前层人数
              ArrayList<Integer> tempList = new ArrayList<>();
              for (int i = 0; i < len; i++) {
                  TreeNode p = queue.poll();            // ✅ poll 弹出
                  tempList.add(p.val);
                  if (p.left != null) queue.offer(p.left);   // ✅ offer 孩子入队
                  if (p.right != null) queue.offer(p.right);
              }
              res.add(tempList);                        // ✅ 本层入结果
          }
          return res;
      }
  }
  ```
- **复杂度：** O(n) / O(w)
- **掌握程度：** ✅（学员独立 AC，ERR-018 全对）
- **感悟/易错点：** ① **这就是 BFS 层序模板的标准形态**，和 104 的 BFS 版几乎一样，只多 `res.add(tempList)`；② ERR-018 五坑全避免：ArrayDeque/offer/len 固定/poll/不入队非节点；③ 记住骨架："while 判空 → len 固定 → for 弹本层 → offer 孩子 → res 加层"；④ 泛型写全 `new ArrayDeque<>()`

### 7. 将有序数组转换为二叉搜索树（Easy）
- **核心思路：** 平衡的关键 = **每次取中点当根**。中点保证左右子树节点数最多差 1，天然平衡，无需额外操作。左半段递归建左子树，右半段递归建右子树，基准 `l > r` 返回 null
- **代码实现：**
  ```java
  // 学员逻辑正确，修正递归调用带类型 int[] 的语法错误（ERR-004 复发）
  class Solution {
      public TreeNode sortedArrayToBST(int[] nums) {
          return build(nums, 0, nums.length - 1);   // 区间 [0, n-1]
      }
      TreeNode build(int[] nums, int l, int r) {
          if (l > r) return null;                   // 基准：空区间
          int mid = l + (r - l) / 2;                // 取中点（防溢出写法，等价 (l+r)/2）
          TreeNode root = new TreeNode(nums[mid]);  // 中点当根
          root.left = build(nums, l, mid - 1);      // 左半段 → 左子树
          root.right = build(nums, mid + 1, r);     // 右半段 → 右子树
          return root;
      }
  }
  ```
- **复杂度：** O(n) / O(log n)（每次取中点 → 树平衡，递归深度 = 树高 log n）
- **掌握程度：** ✅（逻辑正确，修正语法错误）
- **感悟/易错点：** ① **"取中点当根"= 自动平衡**，这是 108 唯一的招，不用别的操作；② 递归调用**不要带类型**：`build(nums, l, mid-1)` 不是 `build(int[] nums, ...)`（ERR-004 复发，盯防）；③ 左右区间是 `[l, mid-1]` 和 `[mid+1, r]`，**mid 不能重复包含**；④ 中点写 `l + (r-l)/2` 防溢出；⑤ 基准 `l > r` 和二分查找同款退出条件

### 8. 验证二叉搜索树（Medium）
- **核心思路：** 两种解法，都要掌握。**①中序版（学员版）：** BST 的中序遍历 = 严格递增序列，一边中序遍历一边检查 `当前值 > 上一个`。**②范围版：** BST = 每个节点都在 (lower, upper) 区间内，范围随递归传递。两版思想不同但都对
- **代码实现：**
  ```java
  // ⭐ 解法一：中序版（学员找到，作者灵茶山艾府）—— 靠"中序严格递增"
  class Solution {
      private long pre = Long.MIN_VALUE;   // 记录"上一个访问的值"，用 long 防 int 最小边界误判

      public boolean isValidBST(TreeNode root) {
          if (root == null) return true;
          if (!isValidBST(root.left)) return false;  // 左：先走完左子树
          if (root.val <= pre) return false;         // 中：必须严格递增（<= 不是 <，BST 不允许重复）
          pre = root.val;                            // 更新"上一个"
          return isValidBST(root.right);             // 右：再走右子树
      }
  }

  // ⭐ 解法二：范围版 —— 靠"节点在区间内"（学员先写，有 NPE bug，已修正）
  class Solution {
      public boolean isValidBST(TreeNode root) {
          return validate(root, Long.MIN_VALUE, Long.MAX_VALUE);
      }
      boolean validate(TreeNode root, long lower, long upper) {
          if (root == null) return true;                            // 基准：空节点 true
          if (root.val <= lower || root.val >= upper) return false; // ⭐ 检查当前节点在不在范围
          return validate(root.left, lower, root.val)               // 左子树收上界
              && validate(root.right, root.val, upper);             // 右子树收下界
      }
  }
  ```
- **复杂度：** O(n) / O(h)（递归栈深度 = 树高）
- **掌握程度：** ✅（中序版学员找到并理解；范围版修正 NPE 后理解）
- **感悟/易错点：** ① ⭐ **BST 两大铁律：中序严格递增 / 每个节点在 (lower,upper) 内**，各对应一种解法；② 中序版的核心是"**检查当前值 vs 上一个（pre）**"，`<=` 不是 `<`（重复值非法）；③ `pre` 用 `long` 且初始 `Long.MIN_VALUE`——否则树里恰好有 `Integer.MIN_VALUE` 时 `val <= pre` 误判；④ 范围版**只检查当前节点对范围，别去裸访问 `root.left.val`/`root.right.val`**（单孩子节点会 NPE，学员踩坑）；⑤ 范围版递归时左子树收紧上界 `root.val`、右子树收紧下界 `root.val`，爷爷约束自动带下去；⑥ 中序版天然安全（不查孩子，只查 pre）；⑦ 中序版为 230 第K小打基础（同一个"中序走一遍"思路）

---

## 三、今日总结

**学到的新模板/技巧：**
- DFS 递归框架（前/中/后序）：基准 → 递归子问题 → 处理自己（REV-18 骨架，Day 6 彻底吃透）
- BFS 层序模板：while 判空 → len 固定 → for 弹本层 → offer 孩子 → res 加层（ERR-018 五坑全对）
- "传单臂记双臂"（543 直径）：一个函数只干一件事，全局记结果
- "交叉比"（101 对称）：外侧比外侧 + 内侧比内侧
- "取中点当根"（108 平衡 BST）
- BST 验证两大铁律（98）：中序严格递增 / 范围传递

**遇到的困难：**
- 98 范围版：裸访问 `root.left.val`/`root.right.val` 单孩子节点 NPE（已修正为只查当前节点）
- 98 中序版：`pre` 为什么用 long、检查为什么用 `<=`（严格递增）
- 108：递归调用带类型 `int[]` 语法错误（ERR-004 复发）

**遗留问题（需复习）：**
- 98 两版解法 + 中序版原理（已登记 REV-27）
- 101 交叉比（REV-26）
- ERR-018 BFS 五坑（104/102 复习时对照）
- 108 递归带类型语法（ERR-004 复发，盯防）

**整体感受：** 😊（Day 6 二叉树基础 8/8 收官，递归骨架已从"看不懂"到"能独立写"）
