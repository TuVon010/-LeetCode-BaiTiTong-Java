# 📖 Java 基础复习卡 · String 字符串全掌握

> **用途：** 记录刷题必备的 Java 基础知识，纳入间隔复习体系。
> **与纠错文档的关系：** 纠错文档记录"犯过的错"，本文档记录"必须会的基础"。
> **创建日期：** 2026-08-02（Day 2 补基础时建立）

---

## 一、String 基础速查表

### 1.1 三个"长相相似"的类型

| 类型 | 是什么 | 例子 | 可变吗 |
|------|--------|------|--------|
| `char` | **单个**字符 | `'a'`、`'7'`、`' '` | 变量，可改 |
| `String` | **一串**字符 | `"hello"` | 不可变 |
| `char[]` | 字符**数组** | `{'h','e','l','l','o'}` | 可变 |

> 🧠 **记忆关键：**
> - `char` 用**单引号** `'a'`
> - `String` 用**双引号** `"abc"`
> - `char[]` 是数组，像 `int[]` 一样的数组

### 1.2 String  ↔ char[] 互转

```java
// 字符串 → 字符数组（最常用！）
String s = "hello";
char[] arr = s.toCharArray();   // {'h','e','l','l','o'}

// 字符数组 → 字符串
String back = new String(arr);  // "hello"
```

**为什么有用？** 数组可以按下标随机访问和修改，方便遍历/排序/修改。

```java
for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);   // 遍历
}
arr[0] = 'H';                      // 修改 → {'H','e','l','l','o'}
```

> 🔗 **应用回顾：** 字母异位词分组 = `toCharArray` + `Arrays.sort` + `new String(arr)`

---

## 二、String 常用方法大全（含记忆法）

### 2.1 核心语法结构

```
对象.方法名(参数)
 └─┘ └──┘  └─┘
 谁   干    什么
```

> **"点"就是"的"**：`"world".charAt(3)` 读作 "**world 的 取字符(3)**"
>
> 所有方法都是这个骨架：**谁.干什么(参数)**。

### 2.2 方法速查表 + 词根记忆

| 方法 | 作用 | 示例 | 词根记忆 |
|------|------|------|---------|
| `s.length()` | 长度 | `"abc".length()` → 3 | length=长度 |
| `s.charAt(i)` | 取第 i 个字符 | `"abc".charAt(1)` → `'b'` | char+**At**="字符在位置" |
| `s.indexOf(c)` | 找位置 | `"abc".indexOf('b')` → 1 | index=索引，of=的 |
| `s.substring(a,b)` | 截取 [a,b) | `"abcde".substring(1,3)` → `"bc"` | **sub**="子"，string=字符串 |
| `s.equals(t)` | 内容相等 | `"abc".equals("abc")` → true | equal=相等 |
| `s.contains(t)` | 包含子串 | `"hello".contains("ell")` → true | con=共同+tain=持有 |
| `s.startsWith(p)` | 以p开头 | `"hello".startsWith("he")` → true | start=开始，With=用 |
| `s.split(",")` | 切分 | `"a,b,c".split(",")` → `["a","b","c"]` | split=劈开 |
| `s.toUpperCase()` | 转大写 | `"abc".toUpperCase()` → `"ABC"` | **to**="转成" |
| `s.toLowerCase()` | 转小写 | `"ABC".toLowerCase()` → `"abc"` | **to**="转成" |
| `s.replace(a,b)` | 替换 | `"a-b".replace('-','_')` → `"a_b"` | re=重新+place=放置 |
| `s.trim()` | 去首尾空格 | `" hi ".trim()` → `"hi"` | trim=修剪 |
| `s.isEmpty()` | 是否为空 | `"".isEmpty()` → true | is=是否 |
| `String.valueOf(x)` | 任意转字符串 | `String.valueOf(123)` → `"123"` | value=值 |

### 2.3 高频词根总结 ⭐

| 词根/前缀 | 意思 | 例子 |
|----------|------|------|
| `to` | 转成 | `toCharArray`、`toString`、`toUpperCase` |
| `sub` | 子/下面 | `substring` |
| `At` | 在...位置 | `charAt` |
| `equal` | 相等 | `equals`、`equalsIgnoreCase` |
| `index` | 索引/位置 | `indexOf` |
| `is` | 是否 | `isEmpty`、`isBlank` |

### 2.4 三个最容易踩的坑 ⚠️

| 坑 | 正确做法 | 原因 |
|----|---------|------|
| 用 `==` 比较字符串 | 用 `a.equals(b)` | `==` 比引用（是否同一对象），`equals` 比内容 |
| `substring` 边界搞错 | `[a, b)` 含头不含尾 | 左闭右开区间 |
| 用 `length()` 时忘了括号 | `s.length()` | String 的方法是 `length()`（数组是 `.length` 没括号） |

### 2.5 `charAt` vs `toCharArray` 对比（高频混淆 ⭐）

| | `charAt(i)` | `toCharArray()` |
|---|---|---|
| 意思 | 取**第 i 个**字符 | 变成**整个**字符数组 |
| 返回 | `char`（单个）| `char[]`（数组）|
| 参数 | 有（下标 i）| 无 |
| 例子 | `"abc".charAt(1)` → `'b'` | `"abc".toCharArray()` → `{'a','b','c'}` |
| 记忆点 | char **At** = 字符**在位置** | to **CharArray** = 转成**字符数组** |

> 🧠 **一句话：** `At` = 单点定位（带下标），`Array` = 整体打包（无下标）。
> - 想"取"某个位置 → `charAt(i)`
> - 想"变"成数组 → `toCharArray()`
> - 等价关系：`"hello".charAt(1)` ≡ `"hello".toCharArray()[1]`

**使用场景：**
- 滑动窗口按位置取字符 → `charAt(i)`
- 排序/修改/统计全部字符（如异位词）→ `toCharArray()`

---

## 三、StringBuilder（频繁拼接用）

`String` 不可变，每次 `+` 拼接都产生新对象，浪费内存。频繁拼接用 `StringBuilder`：

```java
StringBuilder sb = new StringBuilder();
sb.append('a');      // 追加字符
sb.append("bc");     // 追加字符串
sb.reverse();        // 反转
sb.toString();       // 变回 String
```

> 🧠 场景：反转字符串、循环拼接大量字符串、DFS/回溯中维护路径。

---

## 四、字符 ↔ 数字 转换

```java
// 字符 → 数字（char 本质是 ASCII 码）
char c = '5';
int num = c - '0';        // 5（减 '0' 的 ASCII 码 48）
int code = (int) c;       // 53（'5' 的 ASCII 码）

// 数字 → 字符
int n = 7;
char c2 = (char)('0' + n);   // '7'
char c3 = (char)(n + 'a');   // 'h'（97+7=104）

// 大小写转换（ASCII 差 32）
char lower = 'A' + 32;   // 'a'
char upper = 'a' - 32;   // 'A'
```

> 🧠 **记住三个 ASCII 基准：** `'0'`=48，`'A'`=65，`'a'`=97
> 字符可以直接参与数学运算！

---

## 五、自测题（纳入间隔复习）

> 检验方式：✍️ 填空 / 判断，与纠错文档检验同步进行。

### 5.1 填空

```java
// 1. 字符串 → 字符数组
char[] arr = "hello".______;          // toCharArray

// 2. 比较内容相等
boolean same = a.______(b);           // equals

// 3. 取第 i 个字符
char ch = s.______(i);                // charAt

// 4. 截取 [1,3)
String sub = "abcde".______(1, 3);    // substring → "bc"

// 5. 转大写
String up = s.______();               // toUpperCase
```

### 5.2 判断对错

```java
// 1. String 可以直接用 == 比较内容吗？
// 2. "abc".length 和 "abc".length() 哪个对？
// 3. "abcde".substring(1, 4) 的结果是 "bcd" 还是 "bcd"？(验证边界)
// 4. char 用单引号，String 用双引号，对吗？
```

### 5.3 写代码

```java
// 写一个方法：统计字符串 s 中每个字符出现的次数（返回 Map<Character, Integer>）
public Map<Character, Integer> countChars(String s) {
    // 提示：for 循环 + charAt + getOrDefault
}
```

---

## 六、检验记录

| 检验日期 | 学习日 | 检验方式 | 结果 | 备注 |
|---------|--------|---------|------|------|
| （待填） | Day 2 | ✍️ 填空 | （待记录） | 结合 5.1 |
| （待填） | Day 4 | ✅ 判断 | （待记录） | 结合 5.2 |
| （待填） | Day 8 | 💻 写代码 | （待记录） | 结合 5.3 |

---

> **最后更新：** 2026-08-02
