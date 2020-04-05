

# LeetCode第183场竞赛

## [5376. 非递增顺序的最小子序列](https://leetcode-cn.com/problems/minimum-subsequence-in-non-increasing-order/)
### 题目描述 

  给你一个数组 `nums`，请你从中抽取一个子序列，满足该子序列的元素之和 **严格** 大于未包含在该子序列中的各元素之和。

  如果存在多个解决方案，只需返回 **长度最小** 的子序列。如果仍然有多个解决方案，则返回 **元素之和最大** 的子序列。

  与子数组不同的地方在于，「数组的子序列」不强调元素在原数组中的连续性，也就是说，它可以通过从数组中分离一些（也可能不分离）元素得到。

  **注意**，题目数据保证满足所有约束条件的解决方案是 **唯一** 的。同时，返回的答案应当按 **非递增顺序** 排列。

### 思路

从大小排序, 取前`k`个和大于总和的`1/2`即可.

### 代码

```python
class Solution:
    def minSubsequence(self, nums: List[int]) -> List[int]:
        nums.sort(reverse=True)
        total = sum(nums)
        res, s = [], 0
        for i in nums:
            res += [i]
            s += i
            if s > total / 2:
                return res
        return res
```

### 复杂度分析

时间复杂度:`O(nlog(n))`; 空间复杂化: `O(1)`

## [5377. 将二进制表示减到 1 的步骤数](https://leetcode-cn.com/contest/weekly-contest-183/problems/number-of-steps-to-reduce-a-number-in-binary-representation-to-one/)
### 题目描述
给你一个以二进制形式表示的数字 `s` 。请你返回按下述规则将其减少到 1 所需要的步骤数：

- 如果当前数字为偶数，则将其除以 2 。
- 如果当前数字为奇数，则将其加上 1 。

题目保证你总是可以按上述规则将测试用例变为 1 。

### 思路

按照题目要求操作即可

- 除以`2`, 即删除字符串最后一位
- 加1, 对末尾进行二进制加一操作

### 代码
```python
class Solution:
    def numSteps(self, s: str) -> int:
        step = 0
        s = list(s)
        while len(s) != 1 or s[0] != '1':
            if s[-1] == '0':
                s = s[:-1]
            else:
                i = -1
                while i >= -len(s) and s[i] != '0':
                    s[i] = '0'
                    i -= 1
                if i >= -len(s):
                    s[i] = '1'
                else:
                    s = ['1'] + s
            step += 1
        return step
```

### 复杂度分析
时间复杂度:`O(1)`; 空间复杂化: `O(1)`

## [5195. 最长快乐字符串](https://leetcode-cn.com/problems/longest-happy-string/)
### 题目描述

  如果字符串中不含有任何 `'aaa'`，`'bbb'` 或 `'ccc'` 这样的字符串作为子串，那么该字符串就是一个「快乐字符串」。

  给你三个整数 `a`，`b` ，`c`，请你返回 **任意一个** 满足下列全部条件的字符串 `s`：

  - `s` 是一个尽可能长的快乐字符串。
  - `s` 中 **最多** 有`a` 个字母 `'a'`、`b` 个字母 `'b'`、`c` 个字母 `'c'` 。
  - `s `中只含有 `'a'`、`'b'` 、`'c'` 三种字母。

  如果不存在这样的字符串 `s` ，请返回一个空字符串 `""`。

### 思路

考察贪心算法, 总时选择最大的字母加入结果, 当结果与定义冲突时, 则加入第二多的字母加入结果, 直到无法加入结果为止

### 代码
```python
class Solution(object):
    def longestDiverseString(self, a, b, c):
        res = ""
        while a + b + c > 0:
            if a == max(a,b,c):
                if res[-2:] != 'aa':
                    res += 'a'
                    a -= 1
                elif max(b,c) > 0:
                    if b >= c:
                        res += 'b'
                        b -= 1
                    else:
                        res += 'c'
                        c -= 1
                else:
                    break
            elif b == max(a,b,c):
                if res[-2:] != 'bb':
                    res += 'b'
                    b -= 1
                elif max(a,c) > 0:
                    if a >= c:
                        res += 'a'
                        a -= 1
                    else:
                        res += 'c'
                        c -= 1
                else:
                    break
            elif c == max(a,b,c):
                if res[-2:] != 'cc':
                    res += 'c'
                    c -= 1
                elif max(b,a) > 0:
                    if b >= a:
                        res += 'b'
                        b -= 1
                    else:
                        res += 'a'
                        a -= 1
                else:
                    break
        return res
```

### 复杂度分析

时间复杂度:`O(n)`, `n`为`a+b+c`; 空间复杂化: `O(1)`

## [5379. 石子游戏 III](https://leetcode-cn.com/problems/stone-game-iii/)
### 题目描述
Alice 和 Bob 用几堆石子在做游戏。几堆石子排成一行，每堆石子都对应一个得分，由数组 `stoneValue` 给出。

Alice 和 Bob 轮流取石子，**Alice** 总是先开始。在每个玩家的回合中，该玩家可以拿走剩下石子中的的前 **1、2 或 3 堆石子** 。比赛一直持续到所有石头都被拿走。

每个玩家的最终得分为他所拿到的每堆石子的对应得分之和。每个玩家的初始分数都是 **0** 。比赛的目标是决出最高分，得分最高的选手将会赢得比赛，比赛也可能会出现平局。

假设 Alice 和 Bob 都采取 **最优策略** 。如果 Alice 赢了就返回 *"Alice"* *，*Bob 赢了就返回 *"Bob"，*平局（分数相同）返回 *"Tie"* 。

### 思路
我们用一个数组 dp 来表示“在只剩下第 i 堆到最后一堆石子时，当前玩家最多能拿多少分”。假如算出了这个 dp 数组，那么最终答案就是判断 dp[0] 和（分数总和-dp[0]）之间的大小关系即可。

我们倒着计算这个 dp 数组。首先计算边界情况：dp[n-1] = s[n-1]，因为最后一个堆的时候只能拿走。

对于其他的 i，我们可以这样思考：当前你的选择有 “取走一、二、三堆”，结果就是给对方留下了 dp[i+1] dp[i+2] dp[i+3] 对应的情况。也就是对方能够得到的最高分就是 dp[i+1] dp[i+2] dp[i+3] 中的一个，而我们能得到的分数就是剩下的所有分数减去对方能拿到的分数。为了让我们拿到的更多，就得让对方拿到的最少。

因此有 dp[i]= sum{i,n} - min{dp[i+1],dp[i+2],dp[i+3]}，分别对应取走一堆、两堆、三堆石子的情况。

通过 dp[n-1] 和 递推式，利用动态规划我们就能算出 dp[0]，从而得到答案了。

博弈论题第一次见肯定懵逼。本题属于信息透明的平等博弈，是博弈论中最基础的一种，思路就是倒着从游戏的最后一步开始反着算，对每个状态计算“玩家从该状态开始能不能获胜/最多能拿多少分”，用类似动态规划的思想一直算到第一步。

### 代码

```python
class Solution:
    def stoneGameIII(self, s: List[int]) -> str:
        su = 0
        dp = [0]*(len(s)+1)+[10000000]*4
        for i in range(len(s)-1, -1, -1):
            su += s[i]
            cho = min(dp[i+1],dp[i+2],dp[i+3])
            dp[i] = su - cho
   #    print(dp)
        if dp[0]+dp[0]<su:
            return "Bob"
        elif dp[0]+dp[0]>su:
            return "Alice"
        return "Tie"
```
### 复杂度分析

时间复杂度:`O(n)`; 空间复杂化: `O(n)`
