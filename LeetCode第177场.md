### [5169. 日期之间隔几天](https://leetcode-cn.com/contest/weekly-contest-177/problems/number-of-days-between-two-dates/)

#### 题目描述

请你编写一个程序来计算两个日期之间隔了多少天。

日期以字符串形式给出，格式为 `YYYY-MM-DD`，如示例所示。

#### 解题思路
使用库自带函数把, 不然要考虑的特殊点有点多, 容易出错.
这种题其实意义不大, 纯粹使用库函数

#### 代码


```python
class Solution:
    def daysBetweenDates(self, date1: str, date2: str) -> int:
        from datetime import date
        start = date.fromisoformat(date1)
        end = date.fromisoformat(date2)
        return abs((end-start).days)
```

### [5170. 验证二叉树](https://leetcode-cn.com/contest/weekly-contest-177/problems/validate-binary-tree-nodes/)
#### 题目描述
二叉树上有 `n` 个节点，按从 `0` 到 `n - 1` 编号，其中节点 `i` 的两个子节点分别是 `leftChild[i]` 和 `rightChild[i]`。

只有 **所有** 节点能够形成且 **只** 形成 **一颗** 有效的二叉树时，返回 `true`；否则返回 `false`。

如果节点 `i` 没有左子节点，那么 `leftChild[i]` 就等于 `-1`。右子节点也符合该规则。

注意：节点没有值，本问题中仅仅使用节点编号。
#### 解题思路
一个合法的二叉树, 具有以下特点:
1. 有且只有一个根元素, 没有父节点
2. 任何一个元素最多只有一个父节点

只要判断一个节点有小于两个父节点即可.  使用`set`管理该节点是否有父节点, 如果有, 其存在`set`中, 否则不存在`set`中

因为最大只有一棵树, 所以`set`的长度应该为`n-1`

#### 代码
```python
class Solution:
    def validateBinaryTreeNodes(self, n: int, lC: List[int], rC: List[int]) -> bool:
        memo = set()
        for i, j in zip(lC, rC):
            if i in memo: return False
            if i != -1: memo.add(i)
            if j in memo: return False
            if j != -1: memo.add(j)
        return len(memo) == n - 1 
```

### [5171. 最接近的因数](https://leetcode-cn.com/problems/closest-divisors/)
#### 题目描述
给你一个整数 `num`，请你找出同时满足下面全部要求的两个整数：

- 两数乘积等于  `num + 1` 或 `num + 2`
- 以绝对差进行度量，两数大小最接近

你可以按任意顺序返回这两个整数。
#### 解题思路
直接暴力, 没有什么特殊方法, 分解质因数本来就只有暴力破解一条路可以走

#### 代码
```python
def run(num):
            ans = [num, 1]
            for i in range(2, int(math.sqrt(num) + 1)):
                if num % i == 0:
                    if num // i - i < ans[0] - ans[1]:
                        ans = [num // i, i]
            return ans
        x, y = run(num + 1), run(num + 2)
        if x[0] - x[1] > y[0] - y[1]:
            return y
        return x
```

### [5172. 形成三的最大倍数](https://leetcode-cn.com/contest/weekly-contest-177/problems/largest-multiple-of-three/)
#### 题目描述

给你一个整数数组 `digits`，你可以通过按任意顺序连接其中某些数字来形成 **3** 的倍数，请你返回所能得到的最大的 3 的倍数。

由于答案可能不在整数数据类型范围内，请以字符串形式返回答案。

如果无法得到答案，请返回一个空字符串。
你可以按任意顺序返回这两个整数。
#### 解题思路
一个数可以被**3**整除, 那么他各个位数之和为**3**的倍数.
因为给出的数字都是`1-9`所以先统计各个数组出现的次数, 然后计算其和`total`
如果`total`是**3**的倍数, 直接把数组从大到小排列, 返回
如果`total`除**3**的余数为**1**,
- 首席看能否在数字是否存在`[1, 4, 7]`, 如果存在,将其出现的最小数字计数减去`1`即可.
- 如果不存在, 则将`[2, 5, 8]`的出现次数, 按从小到大排列, 减去`2`
如果`total`除**3**的余数为**2**,
- 首席看能否在数字是否存在`[2, 5, 8]`, 如果存在,将其出现的最小数字计数减去`1`即可.
- 如果不存在, 则将`[1, 4, 7]`的出现次数, 按从小到大排列, 减去`2`
#### 代码
```python
class Solution:
    def largestMultipleOfThree(self, digits: List[int]) -> str:
        def ans(cnt):
            res = []
            for i in range(9, -1, -1):
                if not res and i == 0 and cnt[i] > 0: return '0'
                if cnt[i] > 0:
                    res = res + ([chr(i + ord('0'))] * cnt[i])
            return ''.join(res)
                
        cnt = [0] * 10
        for i in digits:
            cnt[i] += 1
        total = 0
        for i in range(10):
            total += i * cnt[i]
        if total % 3 == 0:
            return ans(cnt)
        if total % 3 == 1:
            for i in range(1, 10, 3):
                if cnt[i] > 0:
                    cnt[i] -= 1
                    return ans(cnt)
            t = 2
            for i in range(2, 10, 3):
                while t > 0 and cnt[i] > 0:
                    cnt[i] -= 1
            return ans(cnt)
        if total % 3 == 2:
            for i in range(2, 10, 3):
                if cnt[i] > 0:
                    cnt[i] -= 1
                    return ans(cnt)
            t = 2
            for i in range(1, 10, 3):
                while t > 0 and cnt[i] > 0:
                    cnt[i] -= 1
            return ans(cnt)
                
```