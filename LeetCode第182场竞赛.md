# LeetCode第182场竞赛
## [1394. 找出数组中的幸运数](https://leetcode-cn.com/problems/find-lucky-integer-in-an-array/)

### 题目描述

在整数数组中，如果一个整数的出现频次和它的数值大小相等，我们就称这个整数为「幸运数」。

给你一个整数数组 `arr`，请你从中找出并返回一个幸运数。

- 如果数组中存在多个幸运数，只需返回 **最大** 的那个。
- 如果数组中不含幸运数，则返回 **-1** 。

### 解题思路

使用`hashmap`对`arr`进行计数, **python**可以直接使用`collections.Counter`进行计数, 然后进行比较, 返回出现次数与数值相同的值中的最大值.

### 代码

```python
class Solution:
    def findLucky(self, arr: List[int]) -> int:
        res  = -1
        cnt = collections.Counter(arr)
        for i, v in cnt.items():
            if i == v:
                res = max(res, i)
        return res
```

## [1395. 统计作战单位数](https://leetcode-cn.com/problems/count-number-of-teams/)

### 题目描述

`n` 名士兵站成一排。每个士兵都有一个 **独一无二** 的评分 `rating` 。

每 **3** 个士兵可以组成一个作战单位，分组规则如下：

- 从队伍中选出下标分别为 `i`、`j`、`k` 的 3 名士兵，他们的评分分别为 `rating[i]`、`rating[j]`、`rating[k]`
- 作战单位需满足： `rating[i] < rating[j] < rating[k]` 或者 `rating[i] > rating[j] > rating[k]` ，其中 `0 <= i < j < k < n`

请你返回按上述条件可以组建的作战单位数量。每个士兵都可以是多个作战单位的一部分。

### 解题思路

问题乍一看很复杂, 但其实弄清楚关系之后,非常的简单. 首先 `i, j, k`是有趣的, 我们对 `j`进行分析, 对于每一个`j`, 可以组成的复合要求的作战单位, 有两种情况`I > J and J > K` 或者 `I < J and J < K`, `I, J, K`分别代表对应位置的`rating`. 对于第一种情况, 我们只需要统计在 `j`之前有多少个大于 `J`的数以及在`j`之后有多少个小于`J`的数, 在把他们相乘即可, 第二种情况则相反. 

### 代码

```python
class Solution:
    def numTeams(self, rating: List[int]) -> int:
        res = 0
        lb, rb = [0] * len(rating), [0] * len(rating)
        
        for i in range(len(rating)):
            for j in range(i):
                if rating[i] < rating[j]:
                    lb[i] += 1

        for i in range(len(rating) - 1, -1, -1):
            for j in range(i + 1, len(rating)):
                if rating[i] <  rating[j]:
                    rb[i] += 1
        for i in range(len(lb)):
            res += lb[i] * (len(lb) - i - 1 - rb[i])
            res += (i - lb[i]) * rb[i]
        return res
```

## [1396. 设计地铁系统](https://leetcode-cn.com/problems/design-underground-system/)

### 题目描述

  请你实现一个类 `UndergroundSystem` ，它支持以下 3 种方法：

  1.` checkIn(int id, string stationName, int t)`

  - 编号为 `id` 的乘客在 `t` 时刻进入地铁站 `stationName` 。
  - 一个乘客在同一时间只能在一个地铁站进入或者离开。

  2.` checkOut(int id, string stationName, int t)`

  - 编号为 `id` 的乘客在 `t` 时刻离开地铁站 `stationName` 。

3. `getAverageTime(string startStation, string endStation)` 

  - 返回从地铁站 `startStation` 到地铁站 `endStation` 的平均花费时间。
  - 平均时间计算的行程包括当前为止所有从 `startStation` **直接到达** `endStation` 的行程。
  - 调用 `getAverageTime` 时，询问的路线至少包含一趟行程。

  你可以假设所有对 `checkIn` 和 `checkOut` 的调用都是符合逻辑的。也就是说，如果一个顾客在 **t1** 时刻到达某个地铁站，那么他离开的时间 **t2** 一定满足 **t2 > t1** 。所有的事件都按时间顺序给出。

### 解题思路

使用两个`hashmap`进行记录. 

- 当有乘客进站时, 使用`hashmap`记录乘客进站的时间以及进站名, 记为`start<id, (stationName, t)>`
- 当有乘客离开站时, 使用`start<id, (stationName, t)>`获得乘客的乘车时间, 使用`hashmap`记录 所有在该乘车区间花费的总时间以及人数, 记为`interval<(start, end), (total, num)>`
- 当查询, 使用`interval<(start, end), (total, num)>`直接计算即可

### 代码

```python
class UndergroundSystem:
    def __init__(self):
        self.start = {}
        self.interval = {}

    def checkIn(self, id: int, stationName: str, t: int) -> None:
        self.start[id] = stationName, t

    def checkOut(self, id: int, stationName: str, t: int) -> None:
        ss, st = self.start[id]
        t1, t2 = self.interval.get((ss, stationName), (0, 0))
        self.interval[(ss, stationName)] = t1 + t - st, t2 + 1
        
    def getAverageTime(self, startStation: str, endStation: str) -> float:
        t1, t2 = self.interval[(startStation, endStation)]
        return t1 / t2
```

## [1397. 找到所有好字符串](https://leetcode-cn.com/problems/find-all-good-strings/)

### 题目描述

给你两个长度为 `n` 的字符串 `s1` 和 `s2` ，以及一个字符串 `evil` 。请你返回 **好字符串** 的数目。

**好字符串** 的定义为：它的长度为 `n` ，字典序大于等于 `s1` ，字典序小于等于 `s2` ，且不包含 `evil` 为子字符串。

由于答案可能很大，请你返回答案对 10^9 + 7 取余的结果。

### 解题思路

前面的各位大佬已经写了很详细的题解，但第一次见这种字符串+组合计数类型的同学们可能很难一下子反应过来这题和KMP、DP到底有啥关系，以及这个思路是怎么想到的。所以这里给大家解释一下。

这道题的最大难点在于，我们要求计算有多少个字符串“不包含”某个子串。而这个子串本身是可以重复的，这就导致重复计数的问题。也就是说，假如我们的思路是计算所有 sa 到 sb 的字符串有多少个，再减去所有不符合条件的字符串，那么减的过程中就得考虑“包含好几个evil串”的这个情况。

比如说，不包含的子串为 "abaab"，那么 "abaabaab" 这个字符串中包含两个 "abaab"，被“减”了两次，根据容斥原理，就需要返还一次。那么怎么判断字符串中有没有/有几个 evil 串呢？这就是 KMP 算法的范畴了。

因此需要用到KMP。

KMP算法应该都不陌生。该算法根据匹配串构建了一个状态机，也就是实现中的“Fail数组”。不理解KMP和状态机有啥区别的同学可以看看这个博客：从有限状态机理解KMP。

但传统的字符串算法中，我们是对用一个匹配串去匹配一个原字符串，现在我们没有“一个”源字符串，而是有很多很多的字符串。假如一个一个枚举，这个复杂度肯定难以想象了。

因此要用到DP。

具体来说，这题就是想象把字典序从 sa 到 sb 之间的所有字符串全都写出来，然后一起放进KMP的状态机中跑。在其过程中，所有达到了最后一个节点（也就是KMP中匹配成功的那个节点）的字符串就是不符合条件的，而其他字符串都是符合条件的。

由于我们最后只需要求出数量，因此我们只需用一个数据结构来保存“每个状态的节点分别有多少个”，然后用动态规划的方法进行转移即可。

具体的状态转移设计和代码实现可以参见其他dalao的题解，就不再赘述了。

作者：littledva
链接：https://leetcode-cn.com/problems/find-all-good-strings/solution/kmpshang-de-dpc-by-zhu-mang-4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

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

