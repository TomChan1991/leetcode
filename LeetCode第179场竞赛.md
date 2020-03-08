# LeetCode第179场竞赛

## [5352. 生成每种字符都是奇数个的字符串](https://leetcode-cn.com/contest/weekly-contest-179/problems/generate-a-string-with-characters-that-have-odd-counts/)

###  题目描述

给你一个整数 `n`，请你返回一个含 *`n`* 个字符的字符串，其中每种字符在该字符串中都恰好出现 **奇数次** ***。***

返回的字符串必须只含小写英文字母。如果存在多个满足题目要求的字符串，则返回其中任意一个即可。

### 解题思路

如果`n`为偶数, 返回 `n-1`个`a`以及一个`b`组成的字符串

如果`n`为基数, 返回 `n`个`a`组成的字符串

### 代码

```python
class Solution:
    def generateTheString(self, n: int) -> str:
        if n % 2 == 0: return ''.join('a' * (n - 1)) + 'b'
        return ''.join('a' * n)
```

##  [5353. 灯泡开关 III](https://leetcode-cn.com/contest/weekly-contest-179/problems/bulb-switcher-iii/)

### 题目描述

房间中有 `n` 枚灯泡，编号从 `1` 到 `n`，自左向右排成一排。最初，所有的灯都是关着的。

在 *k* 时刻（ *k* 的取值范围是 `0` 到 `n - 1`），我们打开 `light[k]` 这个灯。

灯的颜色要想 **变成蓝色** 就必须同时满足下面两个条件：

- 灯处于打开状态。
- 排在它之前（左侧）的所有灯也都处于打开状态。

请返回能够让 **所有开着的** 灯都 **变成蓝色** 的时刻 **数目 。**

### 解题思路
从左到右遍历`light`, 并记录已经打开的的灯的最大位置, 遍历到的下标`i`就是已经打开灯光的数目-1, 如果打开灯的数目与最大位置相同, 就证明所有灯都是蓝色的. 

### 代码

```python
class Solution:
    def numTimesAllBlue(self, light: List[int]) -> int:
        n, m, res = len(light), -1, 0
        for i, v in enumerate(light):
            m = max(m, v)
            if m == i + 1:
                res += 1
        return res
```

## [5354. 通知所有员工所需的时间](https://leetcode-cn.com/contest/weekly-contest-179/problems/time-needed-to-inform-all-employees/)

### 题目描述

公司里有 `n` 名员工，每个员工的 ID 都是独一无二的，编号从 `0` 到 `n - 1`。公司的总负责人通过 `headID` 进行标识。

在 `manager` 数组中，每个员工都有一个直属负责人，其中 `manager[i]` 是第 `i` 名员工的直属负责人。对于总负责人，`manager[headID] = -1`。题目保证从属关系可以用树结构显示。

公司总负责人想要向公司所有员工通告一条紧急消息。他将会首先通知他的直属下属们，然后由这些下属通知他们的下属，直到所有的员工都得知这条紧急消息。

第 `i` 名员工需要 `informTime[i]` 分钟来通知它的所有直属下属（也就是说在 informTime[i] 分钟后，他的所有直属下属都可以开始传播这一消息）。

返回通知所有员工这一紧急消息所需要的 **分钟数** 。

### 解题思路

树的遍历, 遍历树的深度, 与普通树的深度不同的是, 每个深度是带权的, 只要把权重加入, 就可以了

### 代码

```python
class Solution:
    def numOfMinutes(self, n: int, headID: int, manager: List[int], informTime: List[int]) -> int:
        memo = {}
        for i, v in enumerate(manager):
            if v in memo:
                memo[v].append(i)
            else:
                memo[v] = [i]
        que, res = collections.deque([(headID, informTime[headID])]), 0
        while que:
            id, time = que.popleft()
            if id not in memo:
                continue
            for e in memo[id]:
                ntime = time + informTime[e]
                res = max(res, ntime)
                que.append((e, ntime))
        return res
```

## [5355. T 秒后青蛙的位置](https://leetcode-cn.com/contest/weekly-contest-179/problems/frog-position-after-t-seconds/)

 ### 题目描述

给你一棵由 n 个顶点组成的无向树，顶点编号从 1 到 `n`。青蛙从 **顶点 1** 开始起跳。规则如下：

- 在一秒内，青蛙从它所在的当前顶点跳到另一个 **未访问** 过的顶点（如果它们直接相连）。
- 青蛙无法跳回已经访问过的顶点。
- 如果青蛙可以跳到多个不同顶点，那么它跳到其中任意一个顶点上的机率都相同。
- 如果青蛙不能跳到任何未访问过的顶点上，那么它每次跳跃都会停留在原地。

无向树的边用数组 `edges` 描述，其中 `edges[i] = [fromi, toi]` 意味着存在一条直接连通 `fromi` 和 `toi` 两个顶点的边。

返回青蛙在 *`t`* 秒后位于目标顶点 *`target`* 上的概率。

### 解题思路

在步数结束之前无法遍历到目标节点: 到达目标节点的概率为`0`
遍历到目标节点之后:
- 如果目标节点为叶子节点, 返回到达该节点的概率
- 如果不为叶子节点, 就判断是不是最后一条, 是: 返回到达该节点的概率, 否则返回0

这道题如果改成, 可以重复到达一个节点, 会有趣的多


### 代码

```python
class Solution:
    def frogPosition(self, n: int, edges: List[List[int]], t: int, target: int) -> float:
        memo = {}
        for x, f in edges:
            if x in memo:
                memo[x].append(f)
            else:
                memo[x] = [f]
            if f in memo:
                memo[f].append(x)
            else:
                memo[f] = [x]
        if target == 1 and 1 not in memo: return 1
        if target == 1: return 0
        que, step, v = collections.deque([(1, 1)]), 0, [0] * (n + 1)
        v[1] = 1
        while que and step <= t:
            l = len(que)
            for _ in range(l):
                node, p = que.popleft()
                if node == target: 
                    if step == t or len(memo[node]) == 1:
                        return p
                    return 0
                for i in memo[node]:
                    if v[i]: continue
                    v[i] = 1
                    que.append((i, p / (len(memo[node]) - (0 if step == 0 else 1))))
            step += 1
        return 0
```



