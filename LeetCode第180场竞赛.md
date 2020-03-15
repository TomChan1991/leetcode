# LeetCode第180场竞赛

## [5356. 矩阵中的幸运数](https://leetcode-cn.com/problems/lucky-numbers-in-a-matrix/)

###  题目描述

给你一个 `m * n` 的矩阵，矩阵中的数字 **各不相同** 。请你按 **任意** 顺序返回矩阵中的所有幸运数。

幸运数是指矩阵中满足同时下列两个条件的元素：

- 在同一行的所有元素中最小
- 在同一列的所有元素中最大

### 解题思路

- 遍历矩阵, 得到每一列的最大值
- 按行遍历, 找出最小值所在的列, 与该列的最大比较, 如果与该列的最大值相等,即为所求

### 代码

```python
class Solution:
    def luckyNumbers (self, matrix: List[List[int]]) -> List[int]:
        maxV, res = [0] * len(matrix[0]), []       
        m, n = len(matrix), len(matrix[0])
        minV = [99999999] * m
        for i in range(m):
            for j in range(n):
                maxV[j] = max(maxV[j], matrix[i][j])          
        for i in range(m):
            index = 0
            for j in range(n):       
                if matrix[i][index] > matrix[i][j]:
                    index = j
            if matrix[i][index] == maxV[index]:
                res.append(matrix[i][index])
        return res
```

##  [5357. 设计一个支持增量操作的栈](https://leetcode-cn.com/problems/design-a-stack-with-increment-operation/)

### 题目描述

请你设计一个支持下述操作的栈。

实现自定义栈类 `CustomStack` ：

- `CustomStack(int maxSize)`：用 `maxSize` 初始化对象，`maxSize` 是栈中最多能容纳的元素数量，栈在增长到 `maxSize` 之后则不支持 `push` 操作。
- `void push(int x)`：如果栈还未增长到 `maxSize` ，就将 `x` 添加到栈顶。
- `int pop()`：返回栈顶的值，或栈为空时返回 **-1** 。
- `void inc(int k, int val)`：栈底的 `k` 个元素的值都增加 `val` 。如果栈中元素总数小于 `k` ，则栈中的所有元素都增加 `val` 

### 解题思路
安装题目要求操作,即可

### 代码

```python
class CustomStack:

    def __init__(self, maxSize: int):
        self.que = collections.deque()
        self.maxSize = maxSize


    def push(self, x: int) -> None:
        if len(self.que) >= self.maxSize: return
        self.que.appendleft(x)


    def pop(self) -> int:
        if not self.que: return -1
        return self.que.popleft()

    def increment(self, k: int, val: int) -> None:
        nq = collections.deque()
        while len(self.que) > k:
            nq.append(self.que.popleft())
        while self.que:
             nq.append(self.que.popleft() + val)
        self.que = nq
```

## [5179. 将二叉搜索树变平衡](https://leetcode-cn.com/problems/balance-a-binary-search-tree/)

### 题目描述

给你一棵二叉搜索树，请你返回一棵 **平衡后** 的二叉搜索树，新生成的树应该与原来的树有着相同的节点值。

如果一棵二叉搜索树中，每个节点的两棵子树高度差不超过 1 ，我们就称这棵二叉搜索树是 **平衡的** 。

如果有多种构造方法，请你返回任意一种。

### 解题思路

- 中序遍历, 得到所有数的有序列表
- 二分插入,构建平衡树

### 代码

```python
class Solution:
    def balanceBST(self, root: TreeNode) -> TreeNode:
        allNum = []
        def getall(root):
            nonlocal allNum
            if not root: return
            getall(root.left)
            allNum += [root.val]
            getall(root.right)
        def build(start, end):
            nonlocal allNum
            if start > end: return None
            if start == end:
                return TreeNode(allNum[start])
            mid = (start + end) // 2
            root = TreeNode(allNum[mid])
            root.left = build(start, mid - 1)
            root.right = build(mid + 1, end)
            return root
        getall(root)
        return build(0, len(allNum) - 1)
```

 ### [5359. 最大的团队表现值](https://leetcode-cn.com/problems/maximum-performance-of-a-team/)

公司有编号为 `1` 到 `n` 的 `n` 个工程师，给你两个数组 `speed` 和 `efficiency` ，其中 `speed[i]` 和 `efficiency[i]` 分别代表第 `i` 位工程师的速度和效率。请你返回由最多 `k` 个工程师组成的 **最大团队表现值** ，由于答案可能很大，请你返回结果对 `10^9 + 7` 取余后的结果。

**团队表现值** 的定义为：一个团队中「所有工程师速度的和」乘以他们「效率值中的最小值」。

### 解题思路

选择`k`个工程师, 如果工程师`i`的`efficiency`为最低, 那么其他`k-1`个工程为`efficiency`大于它的, `speed`排行前`k-1`的.

按照`efficiency`从小到大遍历, 将`speed`排序, 如果前`k-1`个`speed`包括已经遍历过的工程师的`speed(efficiency<=当前工程师` ,则将其删除并向后取一个.


### 代码

```python
class Solution:
    def maxPerformance(self, n: int, speed: List[int], efficiency: List[int], k: int) -> int:
        mod = int(10 ** 9 + 7)
        if k == 1:
            return max(i * j for i, j in zip(speed, efficiency)) % mod
        memo = [(j, i) for i, j in zip(speed, efficiency)]
        memo.sort()
        speed.sort(reverse=True)
        total = sum(speed[:k-1])
        index, cur = k - 2, []
        res = 0
        print(speed)
        for ef, s in memo:
            heapq.heappush(cur, -s) ## 已遍历过的工程师, 按照speed从大小构建大根堆
            while cur and (index + 1 >= n or -cur[0] >= speed[index]) : # 判断已经遍历过的工程师,是否存在选择的k-1个工程师中,存在将其删除,并按照speed大小,向后取一个
                index += 1
                total =  total + heapq.heappop(cur) + (speed[index] if index < n else 0)
            res = max(res, ef * (total + s))
            # if index + 1 == n : break
        return res % mod
```



