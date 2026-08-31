# 📝 Day 19 · CodeTop Day 3 复习巩固（92/103/236/300 默写全升 ✅）

> **日期：** 2026-08-31（周一）
> **范围：** 昨天 🟡 的 4 题全部关题解默写
> **节奏：** 纯复习日，4 题全从 🟡 升 ✅

---

## 一、今日完成

| # | 题号 | 题目 | 昨天状态 | 今天默写 | 结果 |
|---|------|------|---------|---------|------|
| 1 | 92 | 反转链表 II | 🟡 | 迭代法头插法，磕磕碰碰写出，3 个错全避 | ✅ 升掌握 |
| 2 | 103 | 锯齿形层序遍历 | 🟡 | LinkedList 头插法，第一次漏孩子入队，修正后全对 | ✅ 升掌握 |
| 3 | 236 | 最近公共祖先 | 🟡 | 后序递归，第一次混淆 left/right 和 root.left/root.right，修正后全对 | ✅ 升掌握 |
| 4 | 300 | 最长递增子序列 | 🟡 | DP 转移正确，详细讲解了 dp 设计原理和 nums[j]<nums[i] 的含义 | ✅ 升掌握 |

**4 题全从 🟡 升 ✅，复习日效果满分。**

---

## 二、逐题易错点回顾

### 92 反转链表 II · 迭代法

**核心框架：** 虚拟头 → 定位前驱（left-1 步）→ 头插法反转 → 三步重连

**易错点（昨天 3 错，今天全避）：**
1. 定位步数：`left-1` 步（不是 left 步），位置从 1 开始
2. 虚拟头：`dummy.next = head`，从 dummy 出发定位，防 left=1 空指针
3. 角色分离：`reverseHead` 独立做反转临时头，不跟 dummy 混

**今天默写亮点：** 用了 `new ListNode(-1, head)` 带参构造，一步完成虚拟头挂 head

---

### 103 锯齿形层序遍历

**正确写法（LinkedList 头插）：**
```java
boolean reverse = false;
while (!queue.isEmpty()) {
    int size = queue.size();
    LinkedList<Integer> path = new LinkedList<>();
    for (int i = 0; i < size; i++) {
        TreeNode cur = queue.poll();
        if (reverse) path.addFirst(cur.val);  // 奇数层头插
        else path.addLast(cur.val);
        if (cur.left != null) queue.offer(cur.left);   // ⭐ 别漏孩子入队！
        if (cur.right != null) queue.offer(cur.right);
    }
    res.add(path);
    reverse = !reverse;
}
```

**今天默写的错：** 第一次漏掉了孩子入队（`queue.offer(cur.left/right)`），导致只能遍历第一层。修正后全对。

**❌ 废弃写法：** "改变入队顺序"法（只在当前层左到右出队时有效，右到左出队时整体反了，非对称树有 bug）

---

### 236 最近公共祖先

**后序递归法（面试首选）：**
```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q) return root;
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    if (left != null && right != null) return root;  // ⭐ 判断递归返回值 left/right
    if (left == null) return right;                    //    不是树的结构 root.left/root.right
    return left;
}
```

**今天默写的错：** 第一次写成 `if (root.left != null && root.right != null)`，混淆了：
- `left` / `right`：递归返回值（左/右子树里有没有找到 p/q）
- `root.left` / `root.right`：树的结构（root 有没有左/右孩子）

**递归意义：** 每个节点返回"以该节点为根的子树中，p 和 q 的 LCA"。只找到 p 返回 p，只找到 q 返回 q，都没找到返回 null，都找到了返回 LCA。

---

### 300 最长递增子序列

**DP 设计原理（今天详细讲解）：**

| 设计要素 | 内容 | 为什么 |
|---------|------|--------|
| dp[i] 定义 | 以 nums[i] 结尾的 LIS 长度 | "结尾"是关键，保证转移时只看最后一步 |
| 初始值 | 1 | 每个元素自身就是长度 1 的子序列 |
| 转移 | 枚举所有 j<i，nums[j]<nums[i] 时取 max | 前一个元素可以是任何位置，不只是相邻 |
| 条件 nums[j]<nums[i] | 保证最后一步递增 | dp[j] 已保证前面递增，只需检查最后一步 |
| 答案 | max(dp) | LIS 不一定以最后一个元素结尾 |

**nums[j] < nums[i] 没有 bug：** dp[j] 的定义已经保证了以 nums[j] 结尾的子序列是递增的，所以只要 nums[j] < nums[i]，把 nums[i] 接在后面整个序列还是递增的。不需要再比较子序列里的其他元素。

**反例（为什么不能只看 j=i-1）：** `[2, 5, 3, 4]`，i=3（值 4）最长是 `[2,3,4]` 接棒 j=2（值 3），不是 j=1（值 5，5<4 不满足）。

---

## 三、明日计划

1. **开场快速复习：** 今天 4 题口头复述思路（92 迭代框架 / 103 头插法 / 236 后序递归 / 300 DP 转移）
2. **开新题：** CodeTop Day 3 剩余
   - 23 合并 K 个升序链表（Hard，堆/分治）
   - 54 螺旋矩阵（Medium，边界收缩）
3. 视状态开始 CodeTop Day 4

---

> **最后更新：** 2026-08-31
