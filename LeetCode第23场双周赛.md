# LeetCode第23场双周赛

## [5360. 统计最大组的数目](https://leetcode-cn.com/contest/biweekly-contest-23/problems/count-largest-group/)
### 题目描述
给你一个整数 `n` 。请你先求出从 `1` 到 `n` 的每个整数 10 进制表示下的数位和（每一位上的数字相加），然后把数位和相等的数字放到同一个组中。

请你统计每个组中的数字数目，并返回数字数目并列最多的组有多少个。

### 解题思路

暴力解决

### 代码

```python
class Solution:
    def countLargestGroup(self, n: int) -> int:
        memo = [0] * 50
        for i in range(1, n + 1):
            cur,x  = i, 0
            while cur:
                x += cur % 10
                cur = cur // 10
            memo[x] += 1     
        index, res = 0, 0
        for i in range(50):
            if memo[index] == memo[i]:
                res += 1 
            elif memo[index] < memo[i]:
                res = 1
                index = i
        return res
```

## [5362. 构造 K 个回文字符串](https://leetcode-cn.com/contest/biweekly-contest-23/problems/construct-k-palindrome-strings/)
### 题目描述
给你一个字符串 `s` 和一个整数 `k` 。请你用 `s` 字符串中 **所有字符** 构造 `k` 个非空 **回文串** 。

如果你可以用 `s` 中所有字符构造 `k` 个回文字符串，那么请你返回 **True** ，否则返回 **False** 

### 解题思路

弄清楚构造`k`个回文串的条件就很简单了, 需要满足以下条件

- 至少需要`k`个字母, 也就是题目中的`s`的长度必须大于`k`
- 每个回文串中, 至多包含一个字母出现次数为奇数, 也就是说字符串`s`中,出现次数为奇数的字母的个数小于等于`k`个

### 代码

```python
class Solution:
    def canConstruct(self, s: str, k: int) -> bool:
        if len(s) < k: return False
        cnt = collections.Counter(s)
        c = 0
        for i in cnt.values():
            if i % 2: c += 1
        return c <= k
```

## [5361. 圆和矩形是否有重叠](https://leetcode-cn.com/contest/biweekly-contest-23/problems/circle-and-rectangle-overlapping/)
### 题目描述
给你一个以 (`radius`, `x_center`, `y_center`) 表示的圆和一个与坐标轴平行的矩形 (`x1`, `y1`, `x2`, `y2`)，其中 (`x1`, `y1`) 是矩形左下角的坐标，(`x2`, `y2`) 是右上角的坐标。

如果圆和矩形有重叠的部分，请你返回 True ，否则返回 False 。

换句话说，请你检测是否 **存在** 点 `(xi, yi)` ，它既在圆上也在矩形上（两者都包括点落在边界上的情况）。

### 解题思路

因为正方形没有旋转, 所以题目还是比较简单的.根据以下几个条件进行判断即可:

- 正方形的四个顶点是否在圆内
- 圆心是否在正方形内
- 圆心的`x`坐标在`[x1, x2]`内, 正方形离圆最近的点为`(x_center, y1)`或者, `(x_center, y2)`
- 圆心的`y`坐标在`[y1, y2]`内, 正方形离圆最近的点为`(x1, y_center)`或者, `(x2, y_center)`

### 代码

```python
class Solution:
    def checkOverlap(self, radius: int, x_center: int, y_center: int, x1: int, y1: int, x2: int, y2: int) -> bool:
        def inCircle(x, y):
            return (x_center - x) ** 2 + (y_center - y) ** 2 <= radius ** 2
        if inCircle(x1, y1) or inCircle(x1, y2) or inCircle(x2, y1) or inCircle(x2, y2):
            return True
        if x1 <= x_center <= x2 and y1 <= y_center <= y2:
            return True
        if x1 <= x_center <= x2:
            if inCircle(x_center, y1) or inCircle(x_center, y2):
                return True
        if y1 <= y_center <= y2:
            if inCircle(x1, y_center) or inCircle(x2, y_center):
                return True
        return False
```

## [5363. 做菜顺序](https://leetcode-cn.com/contest/biweekly-contest-23/problems/reducing-dishes/)
### 题目描述
  一个厨师收集了他 `n` 道菜的满意程度 `satisfaction` ，这个厨师做出每道菜的时间都是 1 单位时间。

  一道菜的 「喜爱时间」系数定义为烹饪这道菜以及之前每道菜所花费的时间乘以这道菜的满意程度，也就是 `time[i]`*`satisfaction[i]` 。

  请你返回做完所有菜 「喜爱时间」总和的最大值为多少。

  你可以按 **任意** 顺序安排做菜的顺序，你也可以选择放弃做某些菜来获得更大的总和。

### 解题思路

假设一共做`k`到菜, 那么很明显做`satisfaction`最大的`k`道菜, 而且是从小到大做. 做菜的数量可以从`0`到`n`, 只需要遍历即可. 

我们将`satisfaction`按照从大到小排序, 排序后记为`s`, 那么获取做`k`道菜的喜爱时间, 我们可以在线性时间内获得, 便利全部`k`的可能性, 时间复杂度为`O(n^2)`

但是有没有可能在线性时间内完成呢?假设完成`k`道菜的喜爱时间为`Res[k]`, 很明显 `Res[0] = 0`
$$
\begin{align}
Res[k] &= k * s[0] + (k - 1) * s[1] + \cdots + s[k-1] \\ 
	   &= Res[i-1] + \sum_{j=0}^{k-1}s[j] \\
	   &= Res[i-1] + S[k]
\end{align}
$$
$S[k] = \sum_{j=0}^{k-1}s[j]$, 很明显`S[k]`的计算时间为`O(n)`. 通过这样的变换, 这个算法时间复杂度就降为`O(n)`了

通过**python**内置函数, 甚至可以在一行内解决

`return max(0, max(itertools.accumulate(itertools.accumulate(sorted(sat, reverse=True)))))`

### 代码

```python
class Solution:
    def maxSatisfaction(self, sat: List[int]) -> int:
        # return max(0, max(itertools.accumulate(itertools.accumulate(sorted(sat, reverse=True)))))
        sat.sort(reverse=True)
        s = [0] * (len(sat) + 1)
        for i in range(1, len(s)):
            s[i] = s[i-1] + sat[i-1]
        res, cur = 0, 0
        for i in range(len(s)):
            cur += s[i]
            res = max(res, cur)
        return res
```



