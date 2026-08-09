# 00 · Java 容器方法速查表 ⭐

> **用途：** 所有刷题遇到的 Java 集合容器的**完整方法**一页速查。
> **来源：** Day1~Day6 实战踩坑整理 + Java API
> **记忆法：** 每种容器只记"核心方法族"，命名规律统一（add/offer、remove/poll、element/peek）。

---

## 一、方法命名规律（先记这个总纲）

同一个操作，Java 给**两套名字**，区别是**失败时抛异常还是返回特殊值**：

| 操作 | 抛异常版 | 返回特殊值版 | 特殊值 |
|------|---------|-------------|--------|
| 插入 | `add(e)` | `offer(e)` | false |
| 删除 | `remove()` | `poll()` | null |
| 查看 | `element()` | `peek()` | null |

> 刷题基本都用**返回特殊值版**（`offer/poll/peek`），不用 try-catch。

**Deque 双端队列**在此基础上再加 `First/Last` 后缀：

| 操作 | 队头（First）| 队尾（Last）|
|------|-------------|-------------|
| 插入 | `addFirst(e)` / `offerFirst(e)` | `addLast(e)` / `offerLast(e)` |
| 删除 | `removeFirst()` / `pollFirst()` | `removeLast()` / `pollLast()` |
| 查看 | `getFirst()` / `peekFirst()` | `getLast()` / `peekLast()` |

---

## 二、栈 Stack（LIFO 后进先出）

**推荐：`Deque<T> stack = new ArrayDeque<>();`**（不要用老 API `Stack`）

| 方法 | 作用 |
|------|------|
| `push(x)` | 压栈（= `addFirst`）|
| `pop()` | 弹栈（= `removeFirst`）|
| `peek()` | 看栈顶（= `peekFirst`，不弹）|
| `isEmpty()` | 判空 |

> 🧠 栈 = **Deque 只操作队头**。`push/pop/peek` 就是 `addFirst/removeFirst/peekFirst` 的别名。

**典型题：** 括号匹配、中序遍历迭代（94）、单调栈（739/84）

---

## 三、队列 Queue（FIFO 先进先出）

**推荐：`Deque<T> queue = new ArrayDeque<>();` 或 `Queue<T> q = new LinkedList<>();`**

| 方法 | 作用 |
|------|------|
| `offer(x)` | 入队（队尾）|
| `poll()` | 出队（队头）|
| `peek()` | 看队头 |
| `isEmpty()` | 判空 |

> 🧠 队列 = **Deque 队尾进、队头出**。`offer/poll/peek` = `offerLast/pollFirst/peekFirst`。

**典型题：** BFS 层序遍历（102）、二叉树的层序遍历

---

## 四、双端队列 Deque（两头都能进/出）

**`Deque<T> dq = new ArrayDeque<>();`**（刷题主力容器）

| 操作 | 队头 First | 队尾 Last |
|------|-----------|-----------|
| 入 | `addFirst(e)` / `offerFirst(e)` | `addLast(e)` / `offerLast(e)` |
| 出 | `removeFirst()` / `pollFirst()` | `removeLast()` / `pollLast()` |
| 看 | `getFirst()` / `peekFirst()` | `getLast()` / `peekLast()` |
| 判空 | `isEmpty()` | |

**最常用组合：**
- 当**栈**用：`push` / `pop` / `peek`（= First 三件套）
- 当**队列**用：`offerLast` / `pollFirst` / `peekFirst`
- **单调队列**（239 滑动窗口最大值）：`peekLast` / `pollLast`（队尾淘汰）+ `peekFirst` / `pollFirst`（队首过期）

> ⚠️ **踩坑记忆（ERR-008）：** 单调队列"淘汰所有比新元素小的"用 **while + pollLast**，不是 if！

---

## 五、优先队列 / 堆 PriorityQueue

**`PriorityQueue<T> heap = new PriorityQueue<>();`** 默认**小顶堆**

| 方法 | 作用 | 复杂度 |
|------|------|--------|
| `offer(x)` / `add(x)` | 入堆 | O(log n) |
| `poll()` | 弹堆顶（最小/最大）| O(log n) |
| `peek()` | 看堆顶（不弹）| O(1) |
| `isEmpty()` | 判空 | O(1) |
| `size()` | 大小 | O(1) |

**构造：**
```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();                    // 小顶堆（默认）
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Comparator.reverseOrder());  // 大顶堆
PriorityQueue<ListNode> heap = new PriorityQueue<>((a, b) -> a.val - b.val);  // 自定义对象：小顶堆
PriorityQueue<int[]> heap = new PriorityQueue<>((a, b) -> a[1] - b[1]);      // 按 int[] 第二个元素排
```

> 🧠 比较器 `(a,b) -> a.val - b.val`：**结果为负 → a 排前面**。升序（a-b）= 小顶堆。

**典型题：** 23 合并K个升序链表、215 第K大、347 前K高频、295 数据流中位数

---

## 六、哈希表 HashMap / HashSet

**`Map<K, V> map = new HashMap<>();`**

| 方法 | 作用 |
|------|------|
| `put(k, v)` | 存入（key 存在则覆盖）|
| `get(k)` | 取 value，没有则 null |
| `getOrDefault(k, 默认值)` | 取 value，没有返回默认值 |
| `containsKey(k)` | 是否含 key |
| `containsValue(v)` | 是否含 value（少用，O(n)）|
| `remove(k)` | 删除 |
| `size()` | 大小 |
| `keySet()` / `values()` | 遍历 key / value |

**`Set<T> set = new HashSet<>();`**

| 方法 | 作用 |
|------|------|
| `add(x)` | 加（已有则 false）|
| `contains(x)` | 是否含 |
| `remove(x)` | 删 |

> ⚠️ **踩坑记忆（ERR-003）：** 判断 key 存在用 `containsKey`，不是 `get`！`get` 返回 value 可能是 null。

**典型题：** 1 两数之和、49 异位词分组、128 最长连续序列、138 随机链表复制、146 LRU

---

## 七、数组操作 Arrays（工具类）

```java
Arrays.sort(arr);              // 排序（带 s！ERR-002）
Arrays.sort(arr, (a,b) -> a-b); // 自定义排序
Arrays.fill(arr, 0);           // 填充
Arrays.copyOf(arr, n);         // 拷贝
Arrays.asList(...);            // 转 List（定长，不能增删！ERR-005）
Arrays.toString(arr);          // 打印
```

---

## 八、集合工具 Collections

```java
Collections.sort(list);              // 排序 List
Collections.reverse(list);           // 反转
Collections.max(list) / min(list);   // 最值
Collections.fill(list, x);           // 填充
```

---

## 九、链表 LinkedList

**刷题两种用：**
1. **手写 ListNode 节点**（算法题链表）：`class ListNode { int val; ListNode next; }`
2. **当队列/双端队列用**：`Deque<T> dq = new LinkedList<>();`（ArrayDeque 不能存 null）

**双向链表手写要点（REV-24）：**
```java
class DNode { int key, val; DNode prev, next; }

// remove（抽书，2 行）：改前一本 next + 改后一本 prev
void remove(DNode x) {
    x.prev.next = x.next;
    x.next.prev = x.prev;
}

// addToHead（放最上面，4 行）：先接自己两手，再改邻居
void addToHead(DNode x) {
    x.prev = dummy;
    x.next = dummy.next;
    dummy.next.prev = x;
    dummy.next = x;
}
```

---

## 十、字符串 String（补充）

```java
s.length()                 // 长度（对象方法，带括号！ERR-007）
s.charAt(i)                // 第 i 个字符
s.substring(a, b)          // 子串 [a, b)
s.equals(t)                // 比较内容
s.toCharArray()            // 转 char[]
s.indexOf(c) / lastIndexOf(c)
s.split(" ")               // 分割
```

---

## 十一、容器选择口诀

| 需求 | 选什么 |
|------|--------|
| 按下标 O(1) 访问 | 数组 |
| 频繁头尾插入删除 | 链表 |
| 后进先出 | 栈（Deque+push/pop）|
| 先进先出 | 队列（Deque+offer/poll）|
| 两头进出 + 单调 | 双端队列 Deque |
| O(1) 查找 key | HashMap |
| 反复取最值 | 堆 PriorityQueue |
| 分层遍历 | BFS + 队列 |

---

> **最后更新：** 2026-08-09（Day 6 建立，随刷题持续补充）
