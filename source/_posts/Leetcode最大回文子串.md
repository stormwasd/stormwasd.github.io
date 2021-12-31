---
title: LeetCode最大回文子串
date: 2021-12-30 10:43:20
---

动态规划:

对于一个字串而言，如果它是回文串，并且长度大于2，那么将它首位的两个字母去掉之后，它仍然是个回文串，根据这个思路，我们就可以用动态规划的方法解决本题，我们用s[i, j]表示字符串s的第i个到第j个字母组成的串是否为回文串:

我们可以得到只有s[i+1, j-1]是回文串，并且s的第i个和第j个字母相同时，s[i, j]才会是回文串

上文所有讨论都是建立在字串长度大于2的前提上的，我们还需要考虑动态规划中的边界条件，就是字串的长度为1或2。对于长度为1的字串，他显然是个回文串，对于长度为2的字串，只要它的两个字母相同，他就是一个回文串，因此我们就可以得到动态规划的边界条件

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n = len(s)
        if n < 2:
            return s
        
        max_len = 1
        begin = 0
        # dp[i][j] 表示 s[i..j] 是否是回文串
        dp = [[False] * n for _ in range(n)]
        for i in range(n):
            dp[i][i] = True
        
        # 递推开始
        # 先枚举子串长度
        for L in range(2, n + 1):
            # 枚举左边界，左边界的上限设置可以宽松一些
            for i in range(n):
                # 由 L 和 i 可以确定右边界，即 j - i + 1 = L 得
                j = L + i - 1
                # 如果右边界越界，就可以退出当前循环
                if j >= n:
                    break
                    
                if s[i] != s[j]:
                    dp[i][j] = False 
                else:
                    if j - i < 3:
                        dp[i][j] = True
                    else:
                        dp[i][j] = dp[i + 1][j - 1]
                
                # 只要 dp[i][L] == true 成立，就表示子串 s[i..L] 是回文，此时记录回文长度和起始位置
                if dp[i][j] and j - i + 1 > max_len:
                    max_len = j - i + 1
                    begin = i
        return s[begin:begin + max_len]
```



