---
layout:     post
title:      Series Article of Leetcode Notes -- 07
subtitle:   动态规划——股票买卖系列问题专题博客      
date:       2021-11-05
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++      
    - dynamic programming      
---     

> 用动态规划求解股票买卖系列问题。From [代码随想录](https://programmercarl.com/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92-%E8%82%A1%E7%A5%A8%E9%97%AE%E9%A2%98%E6%80%BB%E7%BB%93%E7%AF%87.html)         


## 121. 买卖股票的最佳时机       
给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。      
你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。          
返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。     

示例 1：      
输入：[7,1,5,3,6,4]      
输出：5       
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。        
注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。      

示例 2：      
输入：prices = [7,6,4,3,1]       
输出：0      
解释：在这种情况下, 没有交易完成, 所以最大利润为 0。        

提示：         
* 1 <= prices.length <= 105         
* 0 <= prices[i] <= 104        

### 思路             
* dp[i][0] 表示第i天持有股票所得现金。     
* dp[i][1] 表示第i天不持有股票所得现金。       


如果第 i 天持有股票即dp[i][0]， 那么可以由两个状态推出来        
1. 第i-1天就持有股票，那么就保持现状，所得现金就是昨天持有股票的所得现金 即：dp[i - 1][0]        
2. 第i天买入股票，所得现金就是买入今天的股票后所得现金即：-prices[i] 所以dp[i][0] = max(dp[i - 1][0], -prices[i]);        

如果第i天不持有股票即dp[i][1]， 也可以由两个状态推出来       
1. 第i-1天就不持有股票，那么就保持现状，所得现金就是昨天不持有股票的所得现金 即：dp[i - 1][1]       
2. 第i天卖出股票，所得现金就是按照今天股票佳价格卖出后所得现金即：prices[i] + dp[i - 1][0] 所以dp[i][1] = max(dp[i - 1][1], prices[i] + dp[i - 1][0]);       

同时注意到，每次 dp[i] 的更新只和前一项有关，于是建立固定长度为 2 的二维动态规划数组 dp[2][2]。      

### 实现         
```c++       
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int len = prices.size();
        if( len == 1) return 0;
        int dp[2][2];
        dp[0][0] = -prices[0];
        dp[0][1] = 0;
        for (int i = 1; i < len; i++){
            dp[i%2][0] = max(dp[(i-1)%2][0], -prices[i]);
            dp[i%2][1] = max(dp[(i-1)%2][1], prices[i]+dp[(i-1)%2][0]);
        }
        return dp[(len-1)%2][1];
    }
};
```