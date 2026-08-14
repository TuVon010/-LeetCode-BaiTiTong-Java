# 📇 Java 容器与遍历序复习卡（Day 9-10）

> **用途：** 一站汇总"遍历序判断 + Map 全部操作 + 容器方法区分"，供学员随时自查与 AI 抽查。
> **创建：** 2026-08-14

---

## 一、三种遍历序：就看"什么时候处理节点"

| 序 | 顺序 | 什么时候"用自己" | 典型用途 |
|---|---|---|---|
| **前序** | 处理自己 → 左 → 右 | **一进门就用**（还没看孩子）| 累加前缀和、做选择、复制树 |
| **中序** | 左 → 处理自己 → 右 | 看完左边、进自己 | BST 中序天然升序（230 第K小）|
| **后序** | 左 → 右 → 处理自己 | **出门才用**（孩子全看完了）| 需要孩子结果（104 深度/543 直径/124 路径和）|

**判断口诀：**
> **用自己的值 → 前序；用孩子的结果 → 后序；BST 要顺序 → 中序。**

**回溯 = 前序做选择 + 后序撤销选择：**
```java
void dfs(node) {
    if (node == null) return;
    // 前序【进门】：做选择（累加、记账、查答案）
    dfs(node.left);
    dfs(node.right);
    // 后序【出门】：撤销选择（擦掉状态）
}
```
> 类比：进房间开灯（前序），出房间关灯（后序）。不关灯 → 污染下一个分支。

---

## 二、Map 方法全解

### 2.1 增删改查

| 方法 | 作用 | 例 |
|---|---|---|
| `put(k, v)` | 存/覆盖 | `map.put("a", 3)` |
| `get(k)` | 取（没有返回 null）| `map.get("a")` → 3 |
| `getOrDefault(k, 默认值)` | 取，没有用默认 | `map.getOrDefault("a", 0)` |
| `containsKey(k)` | 有没有这个 key | `map.containsKey("a")` → true |
| `containsValue(v)` | 有没有这个 value | `map.containsValue(3)` |
| `remove(k)` | 删除 | `map.remove("a")` |
| `clear()` | 清空 | `map.clear()` |
| `size()` | 键值对数量 | `map.size()` |
| `isEmpty()` | 空不空 | `map.isEmpty()` |

### 2.2 遍历

| 遍历方式 | 代码 |
|---|---|
| 遍历 key-value | `for (Map.Entry<K,V> e : map.entrySet()) { e.getKey(); e.getValue(); }` |
| 遍历 key | `for (K k : map.keySet())` |
| 遍历 value | `for (V v : map.values())` |
| 遍历并删除 | `map.entrySet().removeIf(e -> e.getValue() == 0);` |

### 2.3 merge —— 数频率神器

```java
map.merge(key, 传入值, 合并器);
// 1. key 不存在 → 直接放"传入值"
// 2. key 存在 → 旧值 和 传入值 用"合并器"合成新值
```

| 需求 | merge 写法 | 等价 getOrDefault 写法 |
|---|---|---|
| 计数 +1 | `map.merge(x, 1, Integer::sum)` | `map.put(x, map.getOrDefault(x,0)+1)` |
| 计数 -1 | `map.merge(x, -1, Integer::sum)` | `map.put(x, map.get(x)-1)` |
| 合并列表 | `map.merge(k, newList, (a,b)->{a.addAll(b); return a;})` | -- |

> ⚠️ `Integer::sum` 小写 sum，是 `(a,b)->a+b` 的缩写（方法引用）。
> 💡 **建议：数频率主用 `getOrDefault`（直白不易错），merge 看懂即可（面试装点）。**

### 2.4 工具类 Collections

| 方法 | 作用 |
|---|---|
| `Collections.max(collection)` | 最大值 |
| `Collections.min(collection)` | 最小值 |
| `Collections.sort(list)` | 排序 |
| `Collections.reverse(list)` | 反转 |
| `Collections.reverseOrder()` | 比较器：降序（大根堆用）|

### 2.5 数组工具 Arrays

| 方法 | 作用 |
|---|---|
| `Arrays.sort(arr)` | 数组排序 |
| `Arrays.fill(arr, 值)` | 全部填某值（⚠️ int[] 默认 0）|
| `Arrays.fill(arr, 起, 止, 值)` | 填 [起,止) |
| `Arrays.copyOf(arr, 新长度)` | 拷贝 |
| `Arrays.equals(a, b)` | 数组相等 |
| `Arrays.toString(arr)` | 转字符串打印 |
| `Arrays.asList(...)` | 数组→List（定长，不能增删）|
| `Arrays.setAll(arr, i -> 表达式)` | 按下标批量初始化 |

---

## 三、容器方法速查（栈/队列/堆/Deque）

| 容器 | 放 | 取（删）| 看（不删）| 空判断 |
|---|---|---|---|---|
| 栈（Deque）| `push(x)` | `pop()` | `peek()` | `isEmpty()` |
| 队列（Deque）| `offer(x)` | `poll()` | `peek()` | `isEmpty()` |
| 堆（PriorityQueue）| `offer(x)` | `poll()` | `peek()` | `isEmpty()` |
| 双端队列（Deque）| `offerFirst/offerLast` | `pollFirst/pollLast` | `peekFirst/peekLast` | `isEmpty()` |

**铁律：**
- `peek`/`pop`/`poll` 前**必须先判空**：`!stack.isEmpty() && ...`
- `push(0)` 只保证开场不空，while 里弹空后还要再查 → 必须判空
- **大根堆**：`new PriorityQueue<>(Collections.reverseOrder())` 或 `new PriorityQueue<>((a,b)->b-a)`

---

## 四、单调栈 / 堆 记忆锚点

**单调栈（存下标 vs 存值）：**
> 需要**算距离/边界** → 存下标（`数组[stack.peek()]` 取值，`i-m` 算距）
> 只要**最值** → 存值（239 滑动窗口最大值）

**堆"守 k 个"套路（第K大/前K高频）：**
> 小根堆留 k 个，堆顶 = 第 k 大守门员；来了更强的就 poll 掉守门员。

**双堆求中位数（295）：**
> 左大根堆（较小半）+ 右小根堆（较大半）；左边最多多 1
> 奇数取左顶，偶数取 `(左顶+右顶)/2.0`（⭐ 2.0 保小数）

**排序家族：**
> 比较排序（快排/归并/堆排）O(n log n) vs 计数排序（有界整数当下标）O(n)

---

> **最后更新：** 2026-08-14
