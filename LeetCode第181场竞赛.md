# LeetCode第181场竞赛

## [5364. 按既定顺序创建目标数组](https://leetcode-cn.com/problems/create-target-array-in-the-given-order/)

### 题目描述

给你两个整数数组 `nums` 和 `index`。你需要按照以下规则创建目标数组：

- 目标数组 `target` 最初为空。
- 按从左到右的顺序依次读取 `nums[i]` 和 `index[i]`，在 `target` 数组中的下标 `index[i]` 处插入值 `nums[i]` 。
- 重复上一步，直到在 `nums` 和 `index` 中都没有要读取的元素。

请你返回目标数组。

题目保证数字插入位置总是存在。

**示例 1：**

```
输入：nums = [0,1,2,3,4], index = [0,1,2,2,1]
输出：[0,4,1,3,2]
解释：
nums       index     target
0            0        [0]
1            1        [0,1]
2            2        [0,1,2]
3            2        [0,1,3,2]
4            1        [0,4,1,3,2]
```

**示例 2：**

```
输入：nums = [1,2,3,4,0], index = [0,1,2,3,0]
输出：[0,1,2,3,4]
解释：
nums       index     target
1            0        [1]
2            1        [1,2]
3            2        [1,2,3]
4            3        [1,2,3,4]
0            0        [0,1,2,3,4]
```

**示例 3：**

```
输入：nums = [1], index = [0]
输出：[1]
```

**提示：**

- `1 <= nums.length, index.length <= 100`
- `nums.length == index.length`
- `0 <= nums[i] <= 100`
- `0 <= index[i] <= i`

### 解题思路

直接按照要求插入即可

### 代码

```python
class Solution:
    def createTargetArray(self, nums: List[int], index: List[int]) -> List[int]:
        target = []
        for n, i in zip(nums, index):
            target.insert(i, n)
        return target
```



## [5178. 四因数](https://leetcode-cn.com/problems/four-divisors/)

### 题目描述

给你一个整数数组 `nums`，请你返回该数组中恰有四个因数的这些整数的各因数之和。

如果数组中不存在满足题意的整数，则返回 `0` 。

**示例：**

```
输入：nums = [21,4,7]
输出：32
解释：
21 有 4 个因数：1, 3, 7, 21
4 有 3 个因数：1, 2, 4
7 有 2 个因数：1, 7
答案仅为 21 的所有因数的和。
```

**提示：**

- `1 <= nums.length <= 10^4`
- `1 <= nums[i] <= 10^5`

### 解题思路

计算每一个`n`的所有因数, 当因数大于5个时放回

```python
class Solution:
    def sumFourDivisors(self, nums: List[int]) -> int:
        def numE(x):
            res = []
            for i in range(1, int(math.sqrt(x)) + 1):
                if x % i == 0:
                    res += [i]
                    if x // i != i:
                        res += [x // i]
                    if len(res) > 4: return res    
            return res
        res = 0
        for n in nums:
            x = numE(n)
            if len(x) == 4:
                res += sum(x)
        return res
```

## [5366. 检查网格中是否存在有效路径](https://leetcode-cn.com/problems/check-if-there-is-a-valid-path-in-a-grid/)

### 题目描述

给你一个 *m* x *n* 的网格 `grid`。网格里的每个单元都代表一条街道。`grid[i][j]` 的街道可以是：

- **1** 表示连接左单元格和右单元格的街道。
- **2** 表示连接上单元格和下单元格的街道。
- **3** 表示连接左单元格和下单元格的街道。
- **4** 表示连接右单元格和下单元格的街道。
- **5** 表示连接左单元格和上单元格的街道。
- **6** 表示连接右单元格和上单元格的街道。

![img](res\main.png)

你最开始从左上角的单元格 `(0,0)` 开始出发，网格中的「有效路径」是指从左上方的单元格 `(0,0)` 开始、一直到右下方的 `(m-1,n-1)` 结束的路径。**该路径必须只沿着街道走**。

**注意：**你 **不能** 变更街道。

如果网格中存在有效的路径，则返回 `true`，否则返回 `false` 。

 

**示例 1：**

![img](res\e1.png)

```
输入：grid = [[2,4,3],[6,5,2]]
输出：true
解释：如图所示，你可以从 (0, 0) 开始，访问网格中的所有单元格并到达 (m - 1, n - 1) 。
```

**示例 2：**

![img](res\e2.png)

```
输入：grid = [[1,2,1],[1,2,1]]
输出：false
解释：如图所示，单元格 (0, 0) 上的街道没有与任何其他单元格上的街道相连，你只会停在 (0, 0) 处。
```

**示例 3：**

```
输入：grid = [[1,1,2]]
输出：false
解释：你会停在 (0, 1)，而且无法到达 (0, 2) 。
```

**示例 4：**

```
输入：grid = [[1,1,1,1,1,1,3]]
输出：true
```

**示例 5：**

```
输入：grid = [[2],[2],[2],[2],[2],[2],[6]]
输出：true
```

**提示：**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 300`
- `1 <= grid[i][j] <= 6`

### 解题思路

线性定义街道的连通性, 假设上下左右分别为`[1, 2, 0, 3]`, 那么题目给出的六个街道分别对应`memo = [(), (0, 3), (1, 2), (0, 2), (2, 3), (0, 1), (1, 3)]`, 第一元素为空是为了下标一一对应

一个街道有入口和出口, 假设上一个街道的出口为`out`, 那么它可以进入该街道的条件为, 当前街道有一个与其向对应入口, 按照方向的定义, 该入口应为 `3-out`.否则无法进入该节点, 那么该街道的出口就为剩下的那个出入口.

如果一个街道的出口为`out`, 那么他的下一个街道的坐标因为该出口连接的下一个街道, 其坐标与当前坐标差是:` dir = [(0, -1), (-1, 0), (1, 0), (0, 1)]`

具体流程见代码

### 代码

```python
class Solution:
    def hasValidPath(self, grid: List[List[int]]) -> bool:
        memo = [(), (0, 3), (1, 2), (0, 2), (2, 3), (0, 1), (1, 3)]
        dir = [(0, -1), (-1, 0), (1, 0), (0, 1)]
        m, n = len(grid), len(grid[0])
        def path(x, y, come):
            while True:
                if x < 0 or x == m or y < 0 or y == n: return False # 该节点在范围外
                d = memo[grid[x][y]]
                if come + d[0] == 3 or come + d[1] == 3: # 该街道是否可以到达
                    if (x, y) == (m - 1, n - 1): return True
                    out = come + d[0] + d[1] - 3
                    # print(x, y, come, xi)
                    x, y = x + dir[out][0], y + dir[out][1] # 下一街道坐标
                    come = out # 当前出口, 为下一街道入口
                else: return False
            return False
        y = path(0, 0, 0) or path(0, 0, 1) or path(0, 0, 2) or path(0, 0, 3) # 可以从任意一个方向进入街道(0, 0)
    
        return y
```



## [5367. 最长快乐前缀](https://leetcode-cn.com/problems/longest-happy-prefix/)

### 题目描述

「快乐前缀」是在原字符串中既是 **非空** 前缀也是后缀（不包括原字符串自身）的字符串。

给你一个字符串 `s`，请你返回它的 **最长快乐前缀**。

如果不存在满足题意的前缀，则返回一个空字符串。

**示例 1：**

```
输入：s = "level"
输出："l"
解释：不包括 s 自己，一共有 4 个前缀（"l", "le", "lev", "leve"）和 4 个后缀（"l", "el", "vel", "evel"）。最长的既是前缀也是后缀的字符串是 "l" 。
```

**示例 2：**

```
输入：s = "ababab"
输出："abab"
解释："abab" 是最长的既是前缀也是后缀的字符串。题目允许前后缀在原字符串中重叠。
```

**示例 3：**

```
输入：s = "leetcodeleet"
输出："leet"
```

**示例 4：**

```
输入：s = "a"
输出：""
```

 

**提示：**

- `1 <= s.length <= 10^5`
- `s` 只含有小写英文字母

### 解题思路

KMP算法, 在`s`随便加入一个元素, 可以得到**最长快乐前缀**的长度

### 代码

```python
class Solution:
    def longestPrefix(self, s: str) -> str:
        s += 'a'
        memo = [-1] * (len(s))
        for i in range(1, len(s)):
            x = memo[i-1] 
            while x != -1 and s[x] != s[i-1]: x = memo[x]
            memo[i] = x + 1
        return s[:memo[-1]]
```

