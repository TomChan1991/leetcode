# LeetCode第21场双周赛

### [5336. 上升下降字符串](https://leetcode-cn.com/contest/biweekly-contest-21/problems/increasing-decreasing-string/)

###  题目描述

给你一个字符串 `s` ，请你根据下面的算法重新构造字符串：

1. 从 `s` 中选出 **最小** 的字符，将它 **接在** 结果字符串的后面。
2. 从 `s` 剩余字符中选出 **最小** 的字符，且该字符比上一个添加的字符大，将它 **接在** 结果字符串后面。
3. 重复步骤 2 ，直到你没法从 `s` 中选择字符。
4. 从 `s` 中选出 **最大** 的字符，将它 **接在** 结果字符串的后面。
5. 从 `s` 剩余字符中选出 **最大** 的字符，且该字符比上一个添加的字符小，将它 **接在** 结果字符串后面。
6. 重复步骤 5 ，直到你没法从 `s` 中选择字符。
7. 重复步骤 1 到 6 ，直到 `s` 中所有字符都已经被选过。

在任何一步中，如果最小或者最大字符不止一个 ，你可以选择其中任意一个，并将其添加到结果字符串。

请你返回将 `s` 中字符重新排序后的 **结果字符串** 。

### 解题思路

将**从小大**和**从大到小**看成一个过程

### 代码

```python
class Solution:
    def sortString(self, s: str) -> str:
        cnt = list(collections.Counter(s).items())
        cnt = [list(i) for i in cnt]
        cnt.sort()
        res = ''
        while len(res) < len(s):
            for i in range(len(cnt)):
                if cnt[i][1] > 0:
                    res += cnt[i][0]
                    cnt[i][1] -= 1
            for i in range(len(cnt) - 1, -1, -1):
                if cnt[i][1] > 0:
                    res += cnt[i][0]
                    cnt[i][1] -= 1 
        return res
```

### [5337. 每个元音包含偶数次的最长子字符串](https://leetcode-cn.com/contest/biweekly-contest-21/problems/find-the-longest-substring-containing-vowels-in-even-counts/)

### 题目描述

给你一个字符串 `s` ，请你返回满足以下条件的最长子字符串的长度：每个元音字母，即 `'a'，'e'，'i'，'o'，'u'` ，在子字符串中都恰好出现了偶数次。

### 解题思路

一道简单的`dp`问题.

从左向右计算每个位置出现`aeiou`出现的次数是奇数还是偶数(0表示偶数, 1表示奇数), 这样一来, 整个字符串最多自由`2^5=32`个状态. 记录每一个状态**第一次**出现的位置, 再次出现时, 他与第一次出现的位置的后一个之间就构成一个符合要求的字符串. `(aeiou, 00000)`是从第零个元素开计算,能否出现符合要求的字符串.

### 代码

```python
class Solution:
    def findTheLongestSubstring(self, s: str) -> int:
        cur = {'a':0, 'e':0, 'i':0, 'o':0, 'u':0}
        memo, res = {(0,0,0,0,0):0}, 0
        for l, i in enumerate(s) :
            if i in cur:
                cur[i] ^= 1          
            key = tuple(v for k, v in cur.items())
            if key in memo:
                res = max(res, l - memo[key] + 1)
            else:
                memo[key] = l + 1
        return res
```

### 5338. 二叉树中的最长交错路径

### 题目描述

给你一棵以 `root` 为根的二叉树，二叉树中的交错路径定义如下：

- 选择二叉树中 **任意** 节点和一个方向（左或者右）。
- 如果前进方向为右，那么移动到当前节点的的右子节点，否则移动到它的左子节点。
- 改变前进方向：左变右或者右变左。
- 重复第二步和第三步，直到你在树中无法继续移动。

交错路径的长度定义为：**访问过的节点数目 - 1**（单个节点的路径长度为 0 ）。

请你返回给定树中最长 **交错路径** 的长度。

### 解题思路

对任意一个节点来说,  如果他为父亲节点的左节点, 那么最长交错路径要么以他为根节点, 且下一个节点为他的右节点. 或者他是路径的一个节点, 且下一个节点为他的左节点, 上一个节点为他的父亲节点.

### 代码

```python
class Solution:
    def longestZigZag(self, root: TreeNode) -> int:
        res = 1
        def maxL(root, par):
            nonlocal res
            if not root: return 0
            r = 1 + maxL(root.right, 2)
            res = max(r, res)
            l = 1 + maxL(root.left, 1)
            res = max(l, res)
            if par == 1: ## 该节点是左节点
                return r ## 返回右节点个数
            else:
                return l
            return x
        maxL(root, 1)
        return res - 1 ## 因为计算的是元素个数
```

### [5339. 二叉搜索子树的最大键值和](https://leetcode-cn.com/contest/biweekly-contest-21/problems/maximum-sum-bst-in-binary-tree/)

 ### 题目描述

给你一棵以 `root` 为根的 **二叉树** ，请你返回 **任意** 二叉搜索子树的最大键值和。

二叉搜索树的定义如下：

- 任意节点的左子树中的键值都 **小于** 此节点的键值。
- 任意节点的右子树中的键值都 **大于** 此节点的键值。
- 任意节点的左子树和右子树都是二叉搜索树。

### 解题思路

如果一个节点是搜索二叉树的根节点, 他的左节点以及右节点都是搜索二叉树的根节点或者为空节点.同时它的值小于右节点的值(如果存在) 大于左节点的值(如果存在)

### 代码

```python
class Solution:
    def maxSumBST(self, root: TreeNode) -> int:
        res = 0
        def dfs(root):
            nonlocal res
            if not root:return [True, 0] ## res[0]:表示该节点是否为根节点, res[1]:为节点值的和
            l, r = dfs(root.left), dfs(root.right)
            if l[0] and r[0] and (not root.left or root.val > root.left.val) and (not root.right or root.val < root.right.val):
                x = l[1] + r[1] + root.val
                res = max(res, x)
                return [True, x]
            return [False, 0]
        dfs(root)
        return res
```



