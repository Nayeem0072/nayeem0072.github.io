---
title: "Coding Solutions (Java): LeetCode 518 - Coin Change 2"
date: 2024-01-02T22:45:00+06:00
description: "In-depth discussion about approaches to different solutions of LeetCode 518"
tags: [code, problem solving, leetcode]
---
[LeetCode 518](https://leetcode.com/problems/coin-change-ii/)

Difficulty: **Medium**

This is a variation of unbounded Knapsack problem, where `amount` is the capacity of the backpack and `coins[]` is the array of items to choose from.

**1. Top Down Recursion [Rejected - TLE]**

TC: `O(2^n)`, where `n` = number of coins.

SC: `O(n)`, to store the recursion stack.

```java
public int change(int amount, int[] coins) {
	return recurse(amount, 0, coins);		
}

private int recurse(int amount, int index, int[] coins) {
	if(amount == 0) return 1;
	if(amount < 0) return 0;
	if(index == coins.length) return 0;

	int count = 0;		
	count += recurse(amount - coins[index], index, coins);
	count += recurse(amount, index + 1, coins);		

	return count;
}
```

**2. Top Down Recursion with Memoization [Accepted]**

TC: `O(nC)` where `n` = number of coins, and `C` = amount.

SC: `O(nC + n)`

```java
public int change(int amount, int[] coins) {
	Integer[][] memo = new Integer[amount + 1][coins.length + 1];
	return recurse(amount, 0, coins, memo);		
}

private int recurse(int amount, int index, int[] coins, Integer[][] memo) {
	if(amount == 0) return 1;
	if(amount < 0) return 0;
	if(index == coins.length) return 0;
	if(memo[amount][index] != null) return memo[amount][index];

	int count = 0;		
	count += recurse(amount - coins[index], index, coins, memo);
	count += recurse(amount, index + 1, coins, memo);		

	return memo[amount][index] = count;
}
```

**3. Bottom Up Dynamic Programming [Accepted]**

TC: `O(nC)` where `n` = number of coins, and `C` = amount.

SC: `O(nC)`

```java
public int change(int amount, int[] coins) {
	int[][] dp = new int[coins.length + 1][amount + 1];
	for(int i = 1; i <= coins.length; i++) {
		for(int j = 0; j <= amount; j++) {
			if(j == 0)
				dp[i][j] = 1;
			else 
				if(j >= coins[i - 1])
					dp[i][j] = dp[i][j - coins[i - 1]] + dp[i - 1][j];
				else 
					dp[i][j] = dp[i - 1][j];
		}
	}

	return dp[coins.length][amount];
}

```

**4. Space Optimized Bottom Up Dynamic Programming [Accepted]**

TC: `O(nC)` where `n` = number of coins, and `C` = amount.

SC: `O(C)`

```java
public int change(int amount, int[] coins) {
	int[] dp = new int[amount + 1];
	dp[0] = 1;
	for(int coin: coins) {
		for(int j = 0; j <= amount; j++) {
			if(j >= coin)
				dp[j] += dp[j - coin];
		}			
	}

	return dp[amount];
}
```