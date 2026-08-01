# 208. 实现 Trie（前缀树）(Medium)

> **专题归类：** [[02-Wiki/专题总结/07-图论]] · [[02-Wiki/专题总结/12-技巧专题]]
> **LeetCode 链接：** https://leetcode.cn/problems/implement-trie-prefix-tree/

---

## 题目描述

Trie（发音类似 "try"）或者说 **前缀树** 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补全和拼写检查。

请你实现 Trie 类：

- `Trie()` 初始化前缀树对象。
- `void insert(String word)` 向前缀树中插入字符串 `word`。
- `boolean search(String word)` 如果字符串 `word` 在前缀树中，返回 `true`（即检索之前已插入）；否则返回 `false`。
- `boolean startsWith(String prefix)` 如果之前已插入的字符串 `word` 的前缀之一为 `prefix`，返回 `true`；否则返回 `false`。

**示例：**
```
输入：
["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
[[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]]
输出：
[null, null, true, false, true, null, true]

解释：
Trie trie = new Trie();
trie.insert("apple");
trie.search("apple");   // 返回 True
trie.search("app");     // 返回 False
trie.startsWith("app"); // 返回 True
trie.insert("app");
trie.search("app");     // 返回 True
```

---

## 🧩 题目详细分析

- **数据范围：** 1 ≤ word.length, prefix.length ≤ 2000，最多调用 3×10^4 次 insert/search/startsWith。
- **输入输出特征：** 只包含小写英文字母（题目规定），因此子节点可以用长度为 26 的数组实现，也可以用哈希表。字符串不含空格或特殊字符。
- **边界条件：** 空字符串是否有效？题目没说，但 word/prefix 长度至少为 1。插入空串通常视为无效操作。
- **核心约束：** search 要求精确匹配（完整单词），startsWith 只要求前缀匹配（不要求单词结尾标记）。同一个单词可能被插入多次？题目暗示可重复插入，但通常只需处理第一次插入（也可覆盖）。
- **隐藏条件：** 
  - Trie 本质上是一个 N 叉树，每个节点有若干子节点和一个 isEnd 标志。
  - 前缀搜索只要求路径存在，不需要 isEnd = true。
  - 空间换时间：Trie 的空间消耗大（每个字符一个节点），但查询时间只与字符串长度相关。

---

## 👶 小白版直白理解

Trie 就像一本按字母顺序组织的词典，但比普通词典更智能。

想象你有一个 **电话通讯录**，里面存了很多名字。每次你想查一个名字：
- `search("Alice")`：逐个字母 A → l → i → c → e，沿途检查每个字母的分支是否存在。
- `startsWith("Ali")`：只查前缀 A → l → i，不关心后面还有没有字母。
- `insert("Alice")`：看 A 有没有分支，没有就新建；再看 l，没有就新建……直到最后一个字母 e，做上标记"这是一个完整的名字"。

为什么不用哈希表？因为哈希表不能高效地查前缀！用哈希表查前缀需要遍历所有 key，而 Trie 只需沿树向下走即可。

---

## 💡 解题思路

### 思路一：数组实现（适合固定小字符集，性能更高）（推荐）

用长度为 26 的数组替代字典，访问更快（数组索引 O(1) vs 字典哈希 O(1) 但常数略大）。

```java
class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode node = root;
        for (char ch : word.toCharArray()) {
            int idx = ch - 'a';
            if (node.children[idx] == null) {
                node.children[idx] = new TrieNode();
            }
            node = node.children[idx];
        }
        node.isEnd = true;
    }
    
    public boolean search(String word) {
        TrieNode node = find(word);
        return node != null && node.isEnd;
    }
    
    public boolean startsWith(String prefix) {
        return find(prefix) != null;
    }
    
    private TrieNode find(String prefix) {
        TrieNode node = root;
        for (char ch : prefix.toCharArray()) {
            int idx = ch - 'a';
            if (node.children[idx] == null) {
                return null;
            }
            node = node.children[idx];
        }
        return node;
    }
    
    // 内部节点类
    private static class TrieNode {
        TrieNode[] children = new TrieNode[26];   // 固定 26 个小写字母
        boolean isEnd = false;
    }
}
```

### 思路二：哈希表实现（适合字符集较大/灵活场景）

每个 Trie 节点包含一个 HashMap `children` 和一个布尔标志 `isEnd`。`children` 的键是字符，值是下一个 Trie 节点。

```java
import java.util.HashMap;
import java.util.Map;

class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode node = root;
        for (char ch : word.toCharArray()) {
            node.children.putIfAbsent(ch, new TrieNode());
            node = node.children.get(ch);
        }
        node.isEnd = true;
    }
    
    public boolean search(String word) {
        TrieNode node = find(word);
        return node != null && node.isEnd;
    }
    
    public boolean startsWith(String prefix) {
        return find(prefix) != null;
    }
    
    private TrieNode find(String prefix) {
        TrieNode node = root;
        for (char ch : prefix.toCharArray()) {
            if (!node.children.containsKey(ch)) {
                return null;
            }
            node = node.children.get(ch);
        }
        return node;
    }
    
    private static class TrieNode {
        Map<Character, TrieNode> children = new HashMap<>();
        boolean isEnd = false;
    }
}
```

**性能对比：** 数组版更快（内存连续，无哈希冲突），哈希表版更灵活（支持 Unicode、数字等所有字符）。

### 思路三：递归插入 + 删除操作扩展

Trie 也支持删除操作，但本题不要求。如果要做删除，可以采用递归或计数方式：

```java
private TrieNode delete(TrieNode root, String word, int depth) {
    if (root == null) return null;
    if (depth == word.length()) {
        root.isEnd = false;  // 取消结尾标记
        // 如果没有子节点，可以删掉当前节点
        if (isEmpty(root.children)) {
            return null;
        }
        return root;
    }
    int idx = word.charAt(depth) - 'a';
    root.children[idx] = delete(root.children[idx], word, depth + 1);

    // 如果当前节点没有子节点且不是单词结尾，可以删除
    if (!root.isEnd && isEmpty(root.children)) {
        return null;
    }
    return root;
}

private boolean isEmpty(TrieNode[] children) {
    for (TrieNode child : children) {
        if (child != null) return false;
    }
    return true;
}
```

---

## ⚠️ 易错点

- **search vs startsWith 的区别：** `search` 要求精确匹配且 `isEnd = true`；`startsWith` 只需要路径存在，**不需要 `isEnd`**。这是最常见的 bug。
- **插入重复单词：** 重复插入同一单词时，第二次 insert 不会改变任何结构（路径已存在），但注意 `isEnd` 已经为 true，无需再设。
- **空字符串处理：** 虽然题目没给空串，但如果你在根节点设置 `isEnd = true`，相当于插入了空字符串，这会影响 search("") 的结果。
- **数组索引越界：** 使用数组实现时，必须确保字符在 'a'~'z' 范围内，`ch - 'a'` 计算索引。如果字符集不保证是小写字母，用 HashMap 更安全。
- **子节点复用：** 插入 "app" 和 "apple" 时，"app" 路径会被两个单词共享。search("app") 依赖 `isEnd` 判断，"apple" 的插入不会覆盖 "app" 的结尾标记。

---

## 🔧 框架提炼

**Trie 标准模板（数组版，字符集固定）：**

```java
class Trie {
    private TrieNode root;
    
    public Trie() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode node = root;
        for (char ch : word.toCharArray()) {
            int idx = ch - 'a';
            if (node.children[idx] == null) {
                node.children[idx] = new TrieNode();
            }
            node = node.children[idx];
        }
        node.isEnd = true;
    }
    
    private TrieNode searchPrefix(String prefix) {
        TrieNode node = root;
        for (char ch : prefix.toCharArray()) {
            int idx = ch - 'a';
            if (node.children[idx] == null) return null;
            node = node.children[idx];
        }
        return node;
    }
    
    public boolean search(String word) {
        TrieNode node = searchPrefix(word);
        return node != null && node.isEnd;
    }
    
    public boolean startsWith(String prefix) {
        return searchPrefix(prefix) != null;
    }
    
    private static class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEnd = false;
    }
}
```

**适用场景：** 前缀匹配、自动补全、拼写检查、词频统计、IP 路由前缀匹配。

---

## 🔗 关联题目

- [[211-添加与搜索单词]] — Trie + DFS 模糊搜索（支持通配符 `.` 匹配任意字符），考察 Trie 与回溯的结合。
- [[212-单词搜索 II]] — Trie + 网格回溯（在二维网格中找所有出现在字典中的单词），将 Trie 用作字典来加速前缀剪枝。
- [[14-最长公共前缀]] — 也可以用 Trie 解决（Trie 中从根向下唯一路径即最长公共前缀），但更简单的解法是纵向扫描。
