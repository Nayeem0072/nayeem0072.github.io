---
title: "Coding Solutions (Java): LeetCode 309 - Best Time to Buy and Sell Stock with Cooldown"
date: 2024-01-01T23:40:00+06:00
description: "In-depth discussion about approaches to different solutions of LeetCode 309"
tags: [code, problem solving, leetcode]
---
[LeetCode 309](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

Difficulty: **Medium**

**Steps:**

1. We are using three int values to denote three states that can happen in a day (0 = buy, 1 = sell, 2 = cooldown).
2. Initially, we set state = buy for the first day.
3. In a buy state, we can either buy the stock that day, or skip to buy the stock the next day.
4. In a sell state, we can either sell the stock that day, or skip to sell the stock the next day.
5. In a cooldown state, we can only buy the stock the next day.
6. We calculate max profit for each day on the basis of state and memoize the result to return.

**Recursive DP Solution**

```java
class Solution {
    public int maxProfit(int[] prices) {
		int[][] dp = new int[prices.length][3];
        return calculateProfit(prices, dp, 0, 0); // state: 0 = buy, 1 = sell, 2 = cooldown
    }
	
	private int calculateProfit(int[] prices, int[][] dp, int index, int state) {
		if(index >= prices.length) return 0;
		
		if(dp[index][state] != 0) 
			return dp[index][state];
			
		int profit = 0;
		
		if(state == 0) { // we have to buy in this state (or skip to buy later)
			int buyNow = calculateProfit(prices, dp, index + 1, 1) - prices[index];
			int buyLater = calculateProfit(prices, dp, index + 1, state); // skipping today, so sending the same state for the next day
			
			profit = Math.max(buyNow, buyLater);	
		}
		else if(state == 1) { // we have to sell in this state (or skip to sell later)
			int sellNow = prices[index] + calculateProfit(prices, dp, index + 1, 2);
			int sellLater = calculateProfit(prices, dp, index + 1, state); // skipping today, so sending the same state for the next day
			
			profit = Math.max(sellNow, sellLater);
		}
		else if(state == 2) { // cooldown, we can buy stock the next day
			profit = calculateProfit(prices, dp, index + 1, 0);
		}
				
		return dp[index][state] = profit;
	}
}

```
