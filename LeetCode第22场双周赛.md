# Leetcode第22场双周赛

#### [5348. 两个数组间的距离值](https://leetcode-cn.com/problems/find-the-distance-value-between-two-arrays/)

### 题目描述

给你两个整数数组 `arr1` ， `arr2` 和一个整数 `d` ，请你返回两个数组之间的 **距离值** 。

「**距离值**」 定义为符合此描述的元素数目：对于元素 `arr1[i]` ，不存在任何元素 `arr2[j]` 满足 `|arr1[i]-arr2[j]| <= d` 

### 解题思路

#### 方法一:

####  因为数据要求的问题, 直接暴力就可以解决了, 对`arr1`中的每一个元素都在`arr2`中进行遍历.时间复杂度`O(n^2)`

#### 方法二

将`arr2`排序, 然后遍历`arr1`, 使用**二分法**查找`arr1`中的元素再`arr2`中的插入位置, 比较其与前后元素的差值. 时间复杂度`O(nlog(n)`

### 代码

#### 方法一

```python
class Solution:
    def findTheDistanceValue(self, arr1: List[int], arr2: List[int], d: int) -> int:
        res = 0
        for i in arr1:
            if all (abs(i - j) > d for j in arr2):
                res += 1
        return res
```



#### 方法二

```python
class Solution:
    def findTheDistanceValue(self, arr1: List[int], arr2: List[int], d: int) -> int:
        res = 0
        arr2.sort()
        for i in arr1:
            x = bisect.bisect_right(arr2, i)
            if x == 0 and abs(i - arr2[x]) > d:
                res += 1
            elif abs(i - arr2[x-1]) > d and (x == len(arr2) or abs(i - arr2[x]) > d):
                res += 1       
        return res
```

#### [5349. 安排电影院座位](https://leetcode-cn.com/problems/cinema-seat-allocation/)

### 题目描述

![img](D:\cyh\Desktop\Blog\Leetcode\res\cinema_seats_1.png)

如上图所示，电影院的观影厅中有 `n` 行座位，行编号从 1 到 `n` ，且每一行内总共有 10 个座位，列编号从 1 到 10 。

给你数组 `reservedSeats` ，包含所有已经被预约了的座位。比如说，`researvedSeats[i]=[3,8]` ，它表示第 **3** 行第 **8** 个座位被预约了。

请你返回 **最多能安排多少个 4 人家庭** 。4 人家庭要占据 **同一行内连续** 的 4 个座位。隔着过道的座位（比方说 [3,3] 和 [3,4]）不是连续的座位，但是如果你可以将 4 人家庭拆成过道两边各坐 2 人，这样子是允许的。

**提示：**

- `1 <= n <= 10^9`
- `1 <= reservedSeats.length <= min(10*n, 10^4)`
- `reservedSeats[i].length == 2`
- `1 <= reservedSeats[i][0] <= n`
- `1 <= reservedSeats[i][1] <= 10`
- 所有 `reservedSeats[i]` 都是互不相同的。

### 解题思路

看到提示, 注意其中`n`的范围, 这个数据范围,说明按行遍历肯定是行不通的. 看到`reservedSeats`的数据范围, 就可以确定了, 肯定是要按照`reservedSeats`遍历的.

将`reservedSeats`按照行分组

得到有人预约的行数`k`, 从而得到没有人预约的行数`n-k`, 而没有人预约可以坐下的家庭为2

有人预约的按照可以坐下家庭的位置判断即可, 分别为[2-5], [6-9]以及[4,7]

### 代码

```python
class Solution:
    def maxNumberOfFamilies(self, n: int, reservedSeats: List[List[int]]) -> int:
        res = 0
        memo = {}
        for r, c in reservedSeats:
            if r in memo:
                memo[r].add(c)
            else:
                memo[r] = set([c])
        res = (n - len(memo)) * 2
        for cols in memo.values():
            if all(i not in cols for i in range(2, 6)):
                res += 1
                if all(i not in cols for i in range(6, 10)):
                    res += 1
            elif all(i not in cols for i in range(4, 8)) or all(i not in cols for i in range(6, 10)):
                res += 1
        return res
```

#### [5350. 将整数按权重排序](https://leetcode-cn.com/problems/sort-integers-by-the-power-value/)

### 题目描述

我们将整数 `x` 的 **权重** 定义为按照下述规则将 `x` 变成 `1` 所需要的步数：

- 如果 `x` 是偶数，那么 `x = x / 2`
- 如果 `x` 是奇数，那么 `x = 3 * x + 1`

比方说，x=3 的权重为 7 。因为 3 需要 7 步变成 1 （3 --> 10 --> 5 --> 16 --> 8 --> 4 --> 2 --> 1）。

给你三个整数 `lo`， `hi` 和 `k` 。你的任务是将区间 `[lo, hi]` 之间的整数按照它们的权重 **升序排序** ，如果大于等于 2 个整数有 **相同** 的权重，那么按照数字自身的数值 **升序排序** 。

请你返回区间 `[lo, hi]` 之间的整数按权重排序后的第 `k` 个数。

注意，题目保证对于任意整数 `x` `（lo <= x <= hi）` ，它变成 `1` 所需要的步数是一个 32 位有符号整数。

**提示：**

- `1 <= lo <= hi <= 1000`
- `1 <= k <= hi - lo + 1`

### 解题思路

很明显可以通过dp来求解该问题.`dp[i] = dp[ i // 2] if i % 2 == 0 else dp[3 * i + 1]`

这道题的难点在于, 当`i`为奇数时 `3 * i+1>hi`是怎么处理, 是否保存, 如果保存, 我们需要用多大的数组进行保存. 最好选择不保存, 继续查找, 知道目标等于小于`hi`为止, 得到 ,`dp[i] = target, step`表示, 数字`i`可以由数字`target<=hi`经过`step`步变换得到. 然后根据该关系, 获得每个数的总`step`. 

当然整个算法也可以使用**带记忆的`dfs`**

### 代码

```python
class Solution:
    def getKth(self, lo: int, hi: int, k: int) -> int:
        memo = [(1, 0)] * (hi + 1)
        for i in range(2, hi + 1):
            n, step = i, 1
            while True:
                n = n * 3 + 1 if n % 2 else n // 2
                if n <= hi:
                    memo[i] = (n, step)
                    break
                step += 1
        res = []
        for i in range(lo, hi + 1):
            p, step = memo[i]
            while p != 1:
                p, step = memo[p][0], step + memo[p][1]
            res.append((step, i))
        res.sort()
        return res[k-1][1]
```

#### [5351. 3n 块披萨](https://leetcode-cn.com/problems/pizza-with-3n-slices/)

### 题目描述

给你一个披萨，它由 3n 块不同大小的部分组成，现在你和你的朋友们需要按照如下规则来分披萨：

- 你挑选 **任意** 一块披萨。
- Alice 将会挑选你所选择的披萨逆时针方向的下一块披萨。
- Bob 将会挑选你所选择的披萨顺时针方向的下一块披萨。
- 重复上述过程直到没有披萨剩下。

每一块披萨的大小按顺时针方向由循环数组 `slices` 表示。

请你返回你可以获得的披萨大小总和的最大值。

**提示：**

- `1 <= slices.length <= 500`
- `slices.length % 3 == 0`
- `1 <= slices[i] <= 1000`

### 解题思路

这道题的难点在于如果将问题转换成一个简单点的问题.

首先你可以从全部`3n`块披萨中,挑选`n`块披萨, 如果没有任何要求, 那么肯定是选择其中最大的`n`块, 但是现在你选择某一块披萨之后, 前后的都会别别人选走, 那么现在问题就简单了, 我只要不选相邻的两块披萨就可以了. 

还有一个问题就是, 披萨时圆形的,第一块披萨与最后一块披萨也是相邻的, 这样的问题就变得比较复杂, 但是在第一块披萨与最后一块披萨之间, 最多可以选择一块披萨, 那我们把原问题分成两种情况即可, 你不选最后一块披萨以及你不选第一块披萨, 这样头尾就不相连了

```python
class Solution:
    def maxSizeSlices(self, slices: List[int]) -> int:
        def rundp(slices):
            dp = [0] * len(slices)
            for i in range(len(slices) // 3 + 1):
                newdp = dp.copy()
                for j in range(len(slices)):
                    dp[j] = max(dp[j-1] if j > 0 else 0, (newdp[j - 2] if j > 1 else 0) + slices[j])
            return dp[-1]
        return max(rundp(slices[1:]), rundp(slices[:-1]))
```



