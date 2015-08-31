title: Python实现最长公共子序列-Longest Common Subsequences
mathjax: true
tags: [Python3]
categories: Algorithms
date: 2015-07-03 22:34:40

---

###定义
一个数列**S**，如果分别是两个或多个已知数列的子序列，且是所有符合此条件序列中最长的，则**S**称为已知序列的最长公共子序列。
###复杂度
对于一般性的LCS问题（即任意数量的序列）是属于`NP-hard`。但当序列的数量确定时，问题可以使用动态规划（Dynamic Programming）在多项式时间解决。


###解法
动态规划的一个计算最长公共子序列的方法如下，以两个序列`X`、`Y`为例子：
设有二维数组`f[i][j]`表示X的`i`位和`Y`的`j`位之前的最长公共子序列的长度，则有：

$$f[1][1] = same(1,1)$$

$$f[i][j] = max \lbrace f[i-1][j-1] + same(i,j), f[i-1][j], f[i][j-1] \rbrace$$

其中，`same(a,b)`当`X`的第`a`位与`Y`的第`b`位完全相同时为“1”，否则为“0”。
此时，`f[i][j]`中最大的数便是`X`和`Y`的最长公共子序列的长度，依据该数组回溯，便可找出最长公共子序列。
该算法的空间、时间复杂度均为$$O(n^{2})$$经过优化后，空间复杂度可为$$O(n)$$

###Python实现LCS
```python
# -*- coding: utf-8 -*-
"""
Longest Common Subsequences
Created on 2015/7/2  15:11
@author: Wang Xu
该算法的核心思想就是，构造一个矩阵，记录两个字串中所有位置的两个字符之间的匹配情况，若是匹配则为1，否则为0。然后求出该矩阵的对角线全为1的最长序列
"""
class LCS:
    def lcs_base(self, input_x, input_y):
        if len(input_x) == 0 or len(input_y) == 0:
            return ""
        else:
            a = input_x[0]
            b = input_y[0]
            if a == b:
                return self.lcs_base(input_x[1:], input_y[1:]) + a
            else:
                # get the max one
                return self.getMax(self.lcs_base(input_x[1:], input_y), self.lcs_base(input_x, input_y[1:]))

    # construct a list by the input string
    def getList(self, inputStr):
        listRes = list()
        if len(inputStr) != 0:
            for i in range(0, len(inputStr)):
                listRes.append(inputStr[i])
        return listRes

    # return the max one between a and b, equivalently return the longest one
    def getMax(self, a, b):
        if len(a) >= len(b):
            return a
        else:
            return b

if __name__ == '__main__':
    lcs = LCS()
    l1 = lcs.getList('我的大中国')
    l2 = lcs.getList('大中国我的')

    l3 = lcs.lcs_base(l1, l2)
    print(l3[::-1])

    l3 = lcs.lcs_base(l2, l1)
    print(l3[::-1])

    l1 = '1233433236676'
    l2 = '98723765655423'
    l3 = lcs.lcs_base(l1, l2)
    print(l3[::-1])

    l1 = '123s212346我的大中国啊33z'
    l2 = '33z的大中国'
    l3 = lcs.lcs_base(l1, l2)
    print(l3[::-1])
```
###LCS变体-最长公共子串
最长公共子串与最长公共子序列稍有区别，不过也算是`LCS`的一个变体，在`LCS`中，子序列是不必要求连续的，而子串则是“连续”的。即：
题：给定两个字符串`X`，`Y`，求二者最长的公共子串，例如`X=[aaaba]`，`Y=[ababaa]`。二者的最长公共子串为`[aba]`，长度为3。
####基本算法
将X的每个子串与Y的每个子串做对比，求出最长公共子串
```python
# -*- coding: utf-8 -*-
"""
Created on 2015/7/2  16:13
@author: Wang Xu
"""
# 使用基本算法获取最长公共子串（连续），最长公共子序列可以是非连续的
def getcomlen(firststr, secondstr):
    comlen = 0
    while firststr and secondstr:
        if firststr[0] == secondstr[0]:
            comlen += 1
            firststr = firststr[1:]
            secondstr = secondstr[1:]
        else:
            break
    return comlen

def lcs_base(input_x, input_y):
    max_common_len = 0
    common_index = 0
    for xtemp in range(0, len(input_x)):
        for ytemp in range(0, len(input_y)):
            com_temp = getcomlen(input_x[xtemp: len(input_x)], input_y[ytemp: len(input_y)])
            if com_temp > max_common_len:
                max_common_len = com_temp
                common_index = xtemp

    print('公共子串的长度是：%s' % max_common_len)
    print('最长公共子串是：%s' % input_x[common_index:common_index + max_common_len])


if __name__ == '__main__':
    lcs_base('d11zabcdeabdcdbbcd', 'bbcd11yabcdefaaa')


'''
OutPut:
公共子串的长度是：5
最长公共子串是：abcde
'''
```
####DP算法
使用动态规划来解最长公共子串问题，可以考虑如何将`arr[0,...,i]`的问题转化为求解`arr[0,...,i-1]`的问题，此处考虑使用`dp[i][j]`表示以`X[i]`和`Y[j]`结尾的最长公共子串的长度，因为要求子串连续，所以对于`X[i]`和`Y[j]`来讲，它们要么与之前的公共子串构成新的公共子串；要么就是不构成公共子串；状态转移方程为

$$X[i] == Y[j]，dp[i][j] = dp[i-1][j-1] + 1$$

$$X[i] != Y[j]，dp[i][j] = 0$$

对于初始化，`i==0`或者`j==0`，如果`X[i] == Y[j]`，`dp[i][j] = 1`；否则`dp[i][j] = 0`。

```python
class LCS3:
    def lcs_dp(self, input_x, input_y):
        # input_y as column, input_x as row
        dp = [([0] * len(input_y)) for i in range(len(input_x))]
        maxlen = maxindex = 0
        for i in range(0, len(input_x)):
            for j in range(0, len(input_y)):
                if input_x[i] == input_y[j]:
                    if i!=0 and j!=0:
                        dp[i][j] = dp[i - 1][j - 1] + 1
                    if i == 0 or j == 0:
                        dp[i][j] = 1
                    if dp[i][j] > maxlen:
                        maxlen = dp[i][j]
                        maxindex = i + 1 - maxlen
                        # print('最长公共子串的长度是:%s' % maxlen)
                        # print('最长公共子串是:%s' % input_x[maxindex:maxindex + maxlen])
        return input_x[maxindex:maxindex + maxlen]

if __name__ == '__main__':
    lcs3 = LCS3()
    print(lcs3.lcs_dp('我是美abc国中defg国中间人', 'abdde我是美中国中国中国人'))
    print(lcs3.lcs_dp('cabdec','cbdec'))
```


参考资料
【1】http://dsqiu.iteye.com/blog/1701541
【2】http://www.ahathinking.com/archives/122.html

