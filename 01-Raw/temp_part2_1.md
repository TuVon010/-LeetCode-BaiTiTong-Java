

# 第二部分：Hot 100 题目详解

按照力扣官方 Hot 100 的分类组织题目。每道题包含题意简述、思路分析、Java代码、复杂度分析和要点提示。

---

## 分类一：哈希

### 1. [1] 两数之和 (Easy)

**题意简述**：给定一个数组和一个目标值，找出数组中和为目标值的两个数的下标。

**思路分析**：使用哈希表记录已遍历过的元素及其下标。对于每个元素 `num`，检查 `target - num` 是否在哈希表中。

**Java代码**：

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> seen = new HashMap<>();  // 值 -> 索引
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];       // 计算互补值
            if (seen.containsKey(complement)) {      // 如果互补值已存在
                return new int[]{seen.get(complement), i};
            }
            seen.put(nums[i], i);                    // 记录当前值
        }
        return new int[]{};
    }
}
```

**复杂度分析**：时间 O(n)，空间 O(n)

**要点/易错点**：不能先把所有元素放入哈希表再查找，因为可能出现同一个元素被使用两次的情况。

---

### 2. [49] 字母异位词分组 (Medium)

**题意简述**：将字母异位词（相同字母不同排列）组合在一起。

**思路分析**：异位词排序后得到相同的字符串，以排序结果作为哈希表的key进行分组。

**Java代码**：

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> groups = new HashMap<>();
        for (String s : strs) {
            char[] chars = s.toCharArray();
            Arrays.sort(chars);
            String key = new String(chars);    // 排序后作为key
            groups.computeIfAbsent(key, k -> new ArrayList<>()).add(s);
        }
        return new ArrayList<>(groups.values());
    }
}
```

**复杂度分析**：时间 O(n * k * log(k))，其中k是字符串最大长度；空间 O(n * k)

**要点/易错点**：另一种方法是用字符计数元组（26个字母的频次）作为key。

---

### 3. [128] 最长连续序列 (Medium)

**题意简述**：给定未排序的整数数组，找出最长连续元素序列的长度。要求 O(n)。

**思路分析**：将数组放入集合中。对于每个数，如果 `num-1` 不在集合中，说明它是一个连续序列的起点，从该起点开始计数。

**Java代码**：

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> numSet = new HashSet<>();  // 放入集合，O(1)查找
        for (int num : nums) numSet.add(num);
        int maxLen = 0;
        for (int num : numSet) {
            // 只有当num是序列起点时才计数
            if (!numSet.contains(num - 1)) {
                int current = num;
                int length = 1;
                while (numSet.contains(current + 1)) {  // 向后延伸
                    current++;
                    length++;
                }
                maxLen = Math.max(maxLen, length);
            }
        }
        return maxLen;
    }
}
```

**复杂度分析**：时间 O(n)，虽然有嵌套循环，但每个元素最多被访问两次；空间 O(n)

**要点/易错点**：必须判断 `num-1` 是否在集合中，否则时间复杂度会退化为 O(n^2)。

---

## 分类二：双指针

### 4. [283] 移动零 (Easy)

**题意简述**：将数组中的0移动到末尾，保持非零元素的相对顺序。要求原地操作。

**思路分析**：使用快慢指针。快指针遍历数组，遇到非零元素就与慢指针位置交换。

**Java代码**：

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int slow = 0;  // slow指向下一个应该放非零元素的位置
        for (int fast = 0; fast < nums.length; fast++) {
            if (nums[fast] != 0) {
                int temp = nums[slow];
                nums[slow] = nums[fast];
                nums[fast] = temp;
                slow++;
            }
        }
    }
}
```

**复杂度分析**：时间 O(n)，空间 O(1)

---

### 5. [11] 盛最多水的容器 (Medium)

**题意简述**：给定n个非负整数表示宽度为1的柱子高度，找两根柱子使其盛水最多。

**思路分析**：左右对撞指针。每次移动较短的那根柱子（因为移动较长的只会使面积更小或不变）。

**Java代码**：

```java
class Solution {
    public int maxArea(int[] height) {
        int left = 0, right = height.length - 1;
        int maxWater = 0;
        while (left < right) {
            // 当前容器的水量 = 宽度 * 较矮的柱子
            int water = (right - left) * Math.min(height[left], height[right]);
            maxWater = Math.max(maxWater, water);
            // 移动较矮的柱子
            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }
        return maxWater;
    }
}
```

**复杂度分析**：时间 O(n)，空间 O(1)

**要点/易错点**：为什么移动较短的是正确的？因为容器的水量由较短的柱子决定，移动较长的柱子不可能得到更大的水量（宽度在缩小，高度受限于较短的）。

---

### 6. [15] 三数之和 (Medium)

**题意简述**：找出数组中所有和为0的三元组，不重复。

**思路分析**：排序 + 固定一个数 + 双指针。对于每个 `nums[i]`，在 `nums[i+1..n-1]` 中用对撞指针找两个数使三数之和为0。

**Java代码**：

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> result = new ArrayList<>();
        for (int i = 0; i < nums.length - 2; i++) {
            // 去重：跳过相同的nums[i]
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            // 剪枝：最小的三个数之和都大于0
            if (nums[i] + nums[i + 1] + nums[i + 2] > 0) break;
            // 剪枝：当前数加最大的两个数还小于0
            if (nums[i] + nums[nums.length - 1] + nums[nums.length - 2] < 0) continue;
            int left = i + 1, right = nums.length - 1;
            while (left < right) {
                int s = nums[i] + nums[left] + nums[right];
                if (s == 0) {
                    result.add(Arrays.asList(nums[i], nums[left], nums[right]));
                    left++;
                    right--;
                    // 去重
                    while (left < right && nums[left] == nums[left - 1]) left++;
                    while (left < right && nums[right] == nums[right + 1]) right--;
                } else if (s < 0) {
                    left++;
                } else {
                    right--;
                }
            }
        }
        return result;
    }
}
```

**复杂度分析**：时间 O(n^2)，空间 O(log n)（排序）

**要点/易错点**：去重是三数之和的关键难点。需要在三个层面去重：外层循环跳过重复的 `nums[i]`，内层循环跳过重复的 `left` 和 `right`。

---

### 7. [42] 接雨水 (Hard)

**题意简述**：给定n个非负整数表示柱子高度，计算能接多少雨水。

**思路分析**：
- **方法一（双指针）**：维护左右两侧的最大高度。对于当前位置，能接的水量 = min(左侧最大高度, 右侧最大高度) - 当前高度。
- **方法二（单调栈）**：维护一个单调递减栈，当遇到更高的柱子时，计算凹槽中的水量。

**Java代码**：

```java
// 方法一：左右双指针
class Solution {
    public int trap(int[] height) {
        if (height.length == 0) return 0;
        int left = 0, right = height.length - 1;
        int leftMax = height[left], rightMax = height[right];
        int water = 0;
        while (left < right) {
            if (leftMax < rightMax) {
                left++;
                leftMax = Math.max(leftMax, height[left]);
                water += leftMax - height[left];    // 左侧是短板，水量由左侧决定
            } else {
                right--;
                rightMax = Math.max(rightMax, height[right]);
                water += rightMax - height[right];  // 右侧是短板
            }
        }
        return water;
    }
}

// 方法二：单调栈
class Solution2 {
    public int trap(int[] height) {
        Deque<Integer> stack = new ArrayDeque<>();  // 存储索引，栈底到栈顶高度递减
        int water = 0;
        for (int i = 0; i < height.length; i++) {
            while (!stack.isEmpty() && height[i] > height[stack.peek()]) {
                int bottom = stack.pop();           // 凹槽底部
                if (stack.isEmpty()) break;
                // 宽度 = 当前右边界 - 左边界 - 1
                int width = i - stack.peek() - 1;
                // 高度 = min(左边界高度, 右边界高度) - 底部高度
                int h = Math.min(height[stack.peek()], height[i]) - height[bottom];
                water += width * h;
            }
            stack.push(i);
        }
        return water;
    }
}
```

**复杂度分析**：两种方法都是时间 O(n)；方法一空间 O(1)，方法二空间 O(n)

**要点/易错点**：接雨水是经典Hard题，关键理解是"每个位置能接多少水取决于它两侧最高柱子中较矮的那个"。单调栈方法中，注意计算的是横向的水量。

---

## 分类三：滑动窗口

### 8. [3] 无重复字符的最长子串 (Medium)

**题意简述**：找出不含重复字符的最长子串的长度。

**思路分析**：滑动窗口 + 哈希集合。扩大窗口加入新字符，当出现重复时缩小窗口直到无重复。

**Java代码**：

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        Set<Character> charSet = new HashSet<>();    // 记录窗口内的字符
        int left = 0;
        int maxLen = 0;
        for (int right = 0; right < s.length(); right++) {
            while (charSet.contains(s.charAt(right))) {  // 出现重复，缩小窗口
                charSet.remove(s.charAt(left));
                left++;
            }
            charSet.add(s.charAt(right));                // 加入新字符
            maxLen = Math.max(maxLen, right - left + 1);
        }
        return maxLen;
    }
}
```

**复杂度分析**：时间 O(n)，每个字符最多进出窗口各一次；空间 O(min(n, m))，m为字符集大小

---

### 9. [438] 找到字符串中所有字母异位词 (Medium)

**题意简述**：找到字符串s中所有是p的字母异位词的子串，返回起始索引列表。

**思路分析**：定长滑动窗口（窗口长度 = len(p)）。维护窗口内字符计数与p的字符计数的匹配情况。

**Java代码**：

```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> result = new ArrayList<>();
        if (s.length() < p.length()) return result;

        int[] pCount = new int[26];
        int[] windowCount = new int[26];
        for (int i = 0; i < p.length(); i++) {
            pCount[p.charAt(i) - 'a']++;
            windowCount[s.charAt(i) - 'a']++;
        }
        if (Arrays.equals(pCount, windowCount)) result.add(0);

        for (int i = p.length(); i < s.length(); i++) {
            // 滑入新字符
            windowCount[s.charAt(i) - 'a']++;
            // 滑出旧字符
            windowCount[s.charAt(i - p.length()) - 'a']--;
            // 检查是否匹配
            if (Arrays.equals(pCount, windowCount)) {
                result.add(i - p.length() + 1);
            }
        }
        return result;
    }
}
```

**复杂度分析**：时间 O(n)，空间 O(1)

---

## 分类四：子串

### 10. [560] 和为K的子数组 (Medium)

**题意简述**：找出数组中和为k的连续子数组的个数。

**思路分析**：前缀和 + 哈希表。如果 `prefix[j] - prefix[i] = k`，则 `nums[i+1..j]` 的和为k。

**Java代码**：

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        Map<Integer, Integer> prefixCount = new HashMap<>();
        prefixCount.put(0, 1);          // 前缀和为0出现1次
        int currentSum = 0;
        int count = 0;
        for (int num : nums) {
            currentSum += num;
            count += prefixCount.getOrDefault(currentSum - k, 0);
            prefixCount.put(currentSum, prefixCount.getOrDefault(currentSum, 0) + 1);
        }
        return count;
    }
}
```

**复杂度分析**：时间 O(n)，空间 O(n)

**要点/易错点**：注意 `prefixCount.put(0, 1)` 的初始化，它表示空前缀的和为0。

---

### 11. [239] 滑动窗口最大值 (Hard)

**题意简述**：给定数组和窗口大小k，返回每个窗口的最大值。

**思路分析**：单调队列（单调递减的双端队列）。队列中存储索引，保证队首始终是当前窗口的最大值。

**Java代码**：

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        Deque<Integer> dq = new ArrayDeque<>();     // 存储索引，对应的值单调递减
        int[] result = new int[nums.length - k + 1];
        int idx = 0;
        for (int i = 0; i < nums.length; i++) {
            // 移除窗口外的元素
            while (!dq.isEmpty() && dq.peekFirst() < i - k + 1) {
                dq.pollFirst();
            }
            // 维护单调性：移除比当前元素小的
            while (!dq.isEmpty() && nums[dq.peekLast()] <= nums[i]) {
                dq.pollLast();
            }
            dq.addLast(i);
            // 窗口形成后记录结果
            if (i >= k - 1) {
                result[idx++] = nums[dq.peekFirst()];
            }
        }
        return result;
    }
}
```

**复杂度分析**：时间 O(n)，每个元素最多入队出队各一次；空间 O(k)

---

### 12. [76] 最小覆盖子串 (Hard)

**题意简述**：找到字符串s中包含字符串t所有字符的最小子串。

**思路分析**：不定长滑动窗口。扩大窗口直到包含t的所有字符，然后缩小窗口寻找最小覆盖子串。

**Java代码**：

```java
class Solution {
    public String minWindow(String s, String t) {
        Map<Character, Integer> need = new HashMap<>();
        for (char c : t.toCharArray()) {
            need.put(c, need.getOrDefault(c, 0) + 1);
        }
        Map<Character, Integer> window = new HashMap<>();
        int have = 0;
        int required = need.size();
        int left = 0;
        int minLen = Integer.MAX_VALUE;
        int minStart = 0;

        for (int right = 0; right < s.length(); right++) {
            char c = s.charAt(right);
            window.put(c, window.getOrDefault(c, 0) + 1);
            if (need.containsKey(c) && window.get(c).equals(need.get(c))) {
                have++;
            }

            while (have == required) {
                if (right - left + 1 < minLen) {
                    minLen = right - left + 1;
                    minStart = left;
                }
                char leftChar = s.charAt(left);
                window.put(leftChar, window.get(leftChar) - 1);
                if (need.containsKey(leftChar) && window.get(leftChar) < need.get(leftChar)) {
                    have--;
                }
                left++;
            }
        }

        return minLen == Integer.MAX_VALUE ? "" : s.substring(minStart, minStart + minLen);
    }
}
```

**复杂度分析**：时间 O(n)，空间 O(m)，m为字符集大小

**要点/易错点**：用 `have` 和 `required` 来跟踪满足情况，避免每次都遍历哈希表比较。注意只在窗口缩小时更新答案。

---
