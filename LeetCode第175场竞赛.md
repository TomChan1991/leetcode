### 5332. [检查整数及其两倍数是否存在](https://leetcode-cn.com/problems/check-if-n-and-its-double-exist/)

#### 题目描述

给你一个整数数组 `arr`，请你检查是否存在两个整数 `N` 和 `M`，满足 `N` 是 `M` 的两倍（即，`N = 2 * M`）。

更正式地，检查是否存在两个下标 `i` 和 `j` 满足：

- `i != j`
- `0 <= i, j < arr.length`
- `arr[i] == 2 * arr[j]`

#### 题目解析

将`list`转换成`set`之后, 直接判断就可以了.

<font color='red'>注意: 应为`0 * 2 = 0`, 所以`0`应该单独处理. 否则会错误 </font>

#### 代码

```python
class Solution:
    def checkIfExist(self, arr: List[int]) -> bool:
        memo = set(arr)
        if arr.count(0) > 1: return True
        for i in arr:
            if 2 * i in memo and i != 0:
                return True
        return False
```

#### 复杂度分析

时间复杂度:`O(n)`, 构造`set`以及遍历`arr`都是`O(n)`; 空间复杂化: `O(1)`

### 5333. [制造字母异位词的最小步骤数](https://leetcode-cn.com/problems/minimum-number-of-steps-to-make-two-strings-anagram/)

#### 题目描述

给你两个长度相等的字符串 `s` 和 `t`。每一个步骤中，你可以选择将 `t` 中的 **任一字符** 替换为 **另一个字符**。

返回使 `t` 成为 `s` 的字母异位词的最小步骤数。

**字母异位词** 指字母相同，但排列不同的字符串

#### 题目解析

首先对单词进行计数, 其次将对应差值求和. 这样就可以发现两个字符串之间一共有多少不一样. 一次变换可以减少两个不同字符

#### 代码
```python
class Solution:
    def minSteps(self, s: str, t: str) -> int:
        def count(s):
            ans = [0] * 26
            for i in s:
                ans[ord(i) - ord('a')] += 1
            return ans
        return sum(abs(i - j) for i, j in zip(count(s), count(t))) // 2
```

#### 复杂度分析

时间复杂度:`O(n)`, 遍历`s , t`都是`O(n)`; 空间复杂化: `O(1)`

### 1348. [推文计数](https://leetcode-cn.com/problems/tweet-counts-per-frequency/)

#### 题目描述

请你实现一个能够支持以下两种方法的推文计数类 `TweetCounts`：

1.` recordTweet(string tweetName, int time)`

- 记录推文发布情况：用户 `tweetName` 在 `time`（以 **秒** 为单位）时刻发布了一条推文。

2.` getTweetCountsPerFrequency(string freq, string tweetName, int startTime, int endTime)`

- 返回从开始时间 `startTime`（以 **秒** 为单位）到结束时间 `endTime`（以 **秒** 为单位）内，每 **分** ***minute**，***时 \*hour\*** 或者 **日 \*day\*** （取决于 `freq`）内指定用户 `tweetName` 发布的推文总数。
- `freq` 的值始终为 **分** ***minute**，***时** ***hour*** 或者 **日** ***day*** 之一，表示获取指定用户 `tweetName` 发布推文次数的时间间隔。
- 第一个时间间隔始终从 `startTime` 开始，因此时间间隔为 `[startTime, startTime + delta*1>,  [startTime + delta*1, startTime + delta*2>, [startTime + delta*2, startTime + delta*3>, ... , [startTime + delta*i, **min**(startTime + delta*(i+1), endTime + 1)>`，其中 `i` 和 `delta`（取决于 `freq`）都是非负整数。

#### 题目解析

难点在于读懂题目, 以及判断有几个间隔以及`time`处于哪一个间隔.

首先,根据`freq`计算间隔

根据间隔, `startTime, endTime`计算间隔个数, 

遍历即可

#### 代码
```python
class TweetCounts:
    def __init__(self):
        self.memo = {}

    def recordTweet(self, tweetName: str, time: int) -> None:
        if tweetName in self.memo: self.memo[tweetName].append(time)       
        else: self.memo[tweetName] = [time]
          
    def getTweetCountsPerFrequency(self, freq: str, tweetName: str, startTime: int, endTime: int) -> List[int]:
        import math
        d = 60
        if freq == 'hour': d = 3600
        elif freq == 'day': d = 86400
        ans = [0] * math.ceil((endTime - startTime + 1) / d)
        for i in self.memo[tweetName]:
            if i < startTime or i > endTime: continue
            ans[(i-startTime) // d] += 1
        return ans
```

### 5335. [参加考试的最大学生数](https://leetcode-cn.com/problems/maximum-students-taking-exam/)

#### 题目描述

给你一个 `m * n` 的矩阵 `seats` 表示教室中的座位分布。如果座位是坏的（不可用），就用 `'#'` 表示；否则，用 `'.'` 表示。

学生可以看到左侧、右侧、左上、右上这四个方向上紧邻他的学生的答卷，但是看不到直接坐在他前面或者后面的学生的答卷。请你计算并返回该考场可以容纳的一起参加考试且无法作弊的最大学生人数。

学生必须坐在状况良好的座位上。

#### 题目解析
按照题目意思, 可以知道当前行的状态之和前一行的状态有关, 而每一行的状态最多有`2^8 = 64`, 每一行只有8个座位.
$$
dp[i][j] = max_{j \in [1, 64]}(dp[i-1][j] + count(j))
$$
`count(j)`表示: `j`的二进制表示, 的`1`的数量.
##### 位操作
将`seats`每一行转换为二进制表示, `'#'=0, '.'=1`, 每一行记为`row[i]`
状态同样是使用二进制表示, `1`:表示有人做, `0`:表示没有人做, 设当前状态码为`j`, 之前一行的状态码为`k`.
首先做人的座位, 左右两边都不能做人的充要条件为: `j & (j << 1) == 0 and j & (j >> 1) == 0`
人不能坐在坏的座位上的充要条件为: `j & row[i] == j`
人做的位置,前面一行左右位置不能做人的充要条件为: `j & (pre << 1) == 0 and j & (pre >> 1) == 0`

#### 代码

```python
class Solution:
    def maxStudents(self, seats: List[List[str]]) -> int:
        dp = {0:0}
        arr = [0] * len(seats)
        for i, row in enumerate(seats):
            for j in row:
                arr[i] = arr[i] * 2 + (1 if j == '.' else 0)
        for i in range(len(arr)):
            newdp = {}
            for j in range(1 << len(seats[0])):
                if j & arr[i] != j or j & (j << 1) != 0 or j & (j >> 1) != 0: continue
                num = bin(j).count('1')
                for k, v in dp.items():
                    if j & (k << 1) != 0 or j & (k >> 1) != 0: continue
                    newdp[j] = max(newdp.get(j, 0), v + num)
            dp = newdp
        return max(dp.values())
```

