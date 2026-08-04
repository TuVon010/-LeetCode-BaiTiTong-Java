# 📖 Java 进阶复习卡 · 刷题实用语法

> **用途：** 记录刷题中高频遇到的"进阶语法"，区别于基础复习卡。
> **创建日期：** 2026-08-03（Day 2 合并区间时建立）

---

## 一、三种"长度/大小"的区别（高频考点 ⭐）

| 类型 | 写法 | 本质 | 例子 |
|------|------|------|------|
| 数组 | `arr.length` | **属性**（无括号）| `nums.length` |
| String | `s.length()` | **方法**（有括号）| `"abc".length()` |
| List | `list.size()` | **方法**（有括号）| `merge.size()` |

> 🧠 **口诀：** 数组不带括号，对象都带括号。

---

## 二、Lambda 表达式（`(a, b) -> ...`）

### 是什么？
一段"可以当参数传的代码"。`->` 左边是参数，右边是逻辑。

```java
(a, b) -> Integer.compare(a[0], b[0])
 ─┬─      ───────────┬───────────────
 参数               逻辑体（返回结果）
```

### 为什么排序要用它？
`Arrays.sort` 对二维数组不知道按什么排，需要你给规则：

```java
// 按起点升序（最常用）
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
// 按起点降序
Arrays.sort(intervals, (a, b) -> Integer.compare(b[0], a[0]));
// 按终点升序
Arrays.sort(intervals, (a, b) -> Integer.compare(a[1], b[1]));
// 字符串按长度排
Arrays.sort(strs, (a, b) -> a.length() - b.length());
```

### `Integer.compare(x, y)` 是什么？
比较两个数的工具，返回 **负数/0/正数**（x<y / 相等 / x>y）。

> 🧠 **记忆：** `(a, b)` 默认"a 在前 b 在后"，想升序就是 `Integer.compare(a[0], b[0])`。
> ⚠️ **易错：** 必须写 `Integer.compare(a[0], b[0])`，不能只写 `Integer(a[0], b[0])`（`Integer` 是类不是方法）。

### 完整版 vs Lambda（等价）
```java
// Lambda 简写
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));

// 完整版（等价）
Arrays.sort(intervals, new Comparator<int[]>() {
    public int compare(int[] a, int[] b) {
        return Integer.compare(a[0], b[0]);
    }
});
```

---

## 三、List 转数组 `toArray()`

### 为什么需要？
题目要返回 `int[][]`，但过程中用 `List<int[]>`，最后要转回数组。

### 固定套路（背下来 ⭐）
```java
List<int[]> list = new ArrayList<>();
// ... add 元素 ...
int[][] result = list.toArray(new int[list.size()][]);
//                          ↑ 传一个"空的目标数组"告诉 Java 返回类型
```

### 为什么必须传参？
Java 运行时"不知道" List 里存什么类型（类型擦除）：

```java
list.toArray();                          // 返回 Object[]，类型不对！
list.toArray(new int[list.size()][]);    // 返回 int[][]，正确！
```

### 为什么 `new int[size][]` 这个格式？
- `size` = 外层长度（有几个区间）
- 后面的 `[]` = 每个元素是 `int[]`
- 合起来 = 一个长度 size 的二维数组，等 `toArray` 填充

> 🧠 **记忆：** `list.toArray(new 类型[list.size()][])` —— 见一次背一次。

---

## 四、为什么用 `List<int[]>` 而不是 `List<Integer>`？

因为一个区间要存**两个数**（start 和 end）：

```
List<int[]> : [ [1,6], [8,10], [15,18] ]   ← 每个元素是一个区间
List<Integer>: [ 1, 6, 8, 10, 15, 18 ]     ← 装不下"区间"概念
```

> 🧠 **判断：** 元素是一个值 → `List<Integer>`；元素是多个值的组合 → `List<int[]>`。

---

## 五、引用类型：改了 `last[1]` 为什么 `merge` 也变？（核心理解 ⭐）

### Java 数组是"引用类型"

```java
int[] last = merge.get(merge.size() - 1);
//   ↑ 不是拷贝，是指向 merge 里那个数组对象

last[1] = 6;   // 修改的是"共享的对象"，merge 里那个数组也变了
```

### 打个比方
```
merge 里的 [1,3] 是一间房子
int[] last = merge.get(...) = 给你这把房子的钥匙
last[1] = 6 = 用钥匙进屋把 3 改成 6
→ 房子变了，merge 里看到的当然也变
```

### 所以：
- **修改 `last[1]` 影响 `merge`** → 合并区间正是要这个效果 ✅
- **`merge.add(interval)` 存原数组引用** → 会污染输入数据，不推荐 ❌
- **`merge.add(new int[]{...})` 新建副本** → 安全 ✅

> 🧠 **口诀：** `int[] a = b` 不是拷贝是"给钥匙"，改 a 就是改 b。

---

## 六、自测题（纳入间隔复习）

### 6.1 填空
```java
// 1. 数组长度
int n = nums.______;             // length（无括号）

// 2. String 长度
int len = s.______();            // length()

// 3. List 大小
int cnt = list.______();         // size()

// 4. 二维数组按起点升序排序
Arrays.sort(intervals, (a, b) -> ______);   // Integer.compare(a[0], b[0])

// 5. List<int[]> 转二维数组
int[][] res = list.______(new int[list.size()][]);   // toArray
```

### 6.2 判断对错
```java
// 1. "abc".length 能取到长度 3 吗？
// 2. Arrays.sort(intervals, (a,b) -> Integer(a[0],b[0])); 对吗？
// 3. int[] a = b; 然后 a[0]=99，b[0] 会变吗？
// 4. list.toArray() 不带参数能返回 int[][] 吗？
```

### 6.3 写代码
```java
// 把 List<int[]> 转成 int[][]（写出完整一行）
// 把 int[][] intervals 按终点（a[1]）升序排序（写一行）
```

---

## 七、检验记录

| 检验日期 | 学习日 | 检验方式 | 结果 | 备注 |
|---------|--------|---------|------|------|
| （待填） | Day 4 | ✍️ 填空 | （待记录） | 结合 6.1 |
| （待填） | Day 8 | ✅ 判断 | （待记录） | 结合 6.2 |
| （待填） | Day 15 | 💻 写代码 | （待记录） | 结合 6.3 |

---

> **最后更新：** 2026-08-03
