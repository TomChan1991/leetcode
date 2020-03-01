#### [5344. 有多少小于当前数字的数字](https://leetcode-cn.com/problems/how-many-numbers-are-smaller-than-the-current-number/)

### 题目描述

给你一个数组 `nums`，对于其中每个元素 `nums[i]`，请你统计数组中比它小的所有数字的数目。

换而言之，对于每个 `nums[i]` 你必须计算出有效的 `j` 的数量，其中 `j` 满足 `j != i` **且** `nums[j] < nums[i]` 。

以数组形式返回答案。

**提示：**

- `2 <= nums.length <= 500`
- `0 <= nums[i] <= 100`

### 思路

这个数据限制直接暴力就可以了, 时间复杂度为:`O(n^2)`

```python
def smallerNumbersThanCurrent(self, num: List[int]) -> List[int]:
        res = [0] * len(num)
        for i in range(len(num)):
            for j in range(i + 1, len(num)):
                if num[i] > num[j]: res[i] += 1
                elif num[i] < num[j]: res[j] += 1
        return res
```

#### [5345. 通过投票对团队排名](https://leetcode-cn.com/problems/rank-teams-by-votes/)

### 题目描述

现在有一个特殊的排名系统，依据参赛团队在投票人心中的次序进行排名，每个投票者都需要按从高到低的顺序对参与排名的所有团队进行排位。

排名规则如下：

- 参赛团队的排名次序依照其所获「排位第一」的票的多少决定。如果存在多个团队并列的情况，将继续考虑其「排位第二」的票的数量。以此类推，直到不再存在并列的情况。
- 如果在考虑完所有投票情况后仍然出现并列现象，则根据团队字母的字母顺序进行排名。

给你一个字符串数组 `votes` 代表全体投票者给出的排位情况，请你根据上述排名规则对所有参赛团队进行排名。

请你返回能表示按排名系统 **排序后** 的所有团队排名的字符串。

**提示：**

- `1 <= votes.length <= 1000`
- `1 <= votes[i].length <= 26`
- `votes[i].length == votes[j].length for 0 <= i, j < votes.length`
- `votes[i][j]` 是英文 大写 字母
- `votes[i] `中的所有字母都是唯一的
- `votes[0]` 中出现的所有字母 同样也 出现在 `votes[j] `中，其中 `1 <= j < votes.length`

### 思路

使用`map`统计各个字母在位置`i`出现的位置

将`map`转换为`list`,并且按照出现的次数排序, 如果出现次数相同, 那么就按照字母排序

### 代码

```python
class Solution:
    def rankTeams(self, votes: List[str]) -> str:
        res = {i:[0] * len(votes[0]) for i in votes[0]}
        for vote in votes:
            for i in range(len(vote)):
                res[vote[i]][i] -= 1
        res = list(res.items())
        res.sort(key = lambda x: x[1] + [x[0]])
        return ''.join([i[0] for i in res ])
```

#### [5346. 二叉树中的列表](https://leetcode-cn.com/problems/linked-list-in-binary-tree/)

给你一棵以 `root` 为根的二叉树和一个 `head` 为第一个节点的链表。

如果在二叉树中，存在一条一直向下的路径，且每个点的数值恰好一一对应以 `head` 为首的链表中每个节点的值，那么请你返回 `True` ，否则返回 `False` 。

一直向下的路径的意思是：从树中某个节点开始，一直连续向下的路径。

**提示：**

二叉树和链表中的每个节点的值都满足 `1 <= node.val <= 100` 。
链表包含的节点数目在 `1` 到 `100 `之间。
二叉树包含的节点数目在 `1` 到 `2500` 之间。

### 思路

寻找节点值与链表的头节点的值相同的节点

从链表的头节点开始匹配, 链表可以的下一个节点可以是当前节点的左节点或者右节点, 直到链表末尾

### 代码

```python
class Solution:
    def isSubPath(self, head: ListNode, root: TreeNode) -> bool:
        def run(head, root, start=False):    
            if not head: return True
            if not root: return False   
            if root.val == head.val:
                if run(head.next, root.left, True) or run(head.next, root.right, True):
                    return True
            if start: return False
            return run(head, root.left) or run(head, root.right)
        return run(head, root, False)
```



#### [5347. 使网格图至少有一条有效路径的最小代价](https://leetcode-cn.com/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/)

给你一个 m x n 的网格图 `grid` 。 `grid` 中每个格子都有一个数字，对应着从该格子出发下一步走的方向。 `grid[i][j]` 中的数字可能为以下几种情况：

- **1** ，下一步往右走，也就是你会从 `grid[i][j]` 走到 `grid[i][j + 1]`
- **2** ，下一步往左走，也就是你会从 `grid[i][j]` 走到 `grid[i][j - 1]`
- **3** ，下一步往下走，也就是你会从 `grid[i][j]` 走到 `grid[i + 1][j]`
- **4** ，下一步往上走，也就是你会从 `grid[i][j]` 走到 `grid[i - 1][j]`

注意网格图中可能会有 **无效数字** ，因为它们可能指向 `grid` 以外的区域。

一开始，你会从最左上角的格子 `(0,0)` 出发。我们定义一条 **有效路径** 为从格子 `(0,0)` 出发，每一步都顺着数字对应方向走，最终在最右下角的格子 `(m - 1, n - 1)` 结束的路径。有效路径 **不需要是最短路径** 。

你可以花费 `cost = 1` 的代价修改一个格子中的数字，但每个格子中的数字 **只能修改一次** 。

请你返回让网格图至少有一条有效路径的最小代价。

**提示：**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 100`

### 思路

按照修改次数进行遍历.

先遍历修改`0`此可以到达的节点, 然后遍历修改`1`次可以到达位置直到到打目标地址

这道题的难点是在遍历修改`i`次可以到达的节点, 可能会增加修改`i`次可以到达节点

在实际代码中, 使用最小堆来达到修改数目少的可达节点一定在修改数目大的可达节点前修改

### 代码

```python
class Solution:
    def minCost(self, grid: List[List[int]]) -> int:
        dir = [0, [0, 1], [0, -1], [1, 0], [-1, 0]]
        que = [(0, 0, 0)]
        while que:
            s, x, y = heapq.heappop(que)
            if grid[x][y] > 5: continue
            grid[x][y] += 5
            if (x, y) == (len(grid) - 1, len(grid[0]) - 1):
                return s
            for i in range(1, 5):
                nx, ny = x + dir[i][0], y + dir[i][1]
                if 0 <= nx < len(grid) and 0 <= ny < len(grid[0]) and grid[nx][ny] < 5:
                    if i + 5 == grid[x][y]: heapq.heappush(que, (s, nx, ny))
                    else: heapq.heappush(que, (s + 1, nx, ny))
                        
        return
```

